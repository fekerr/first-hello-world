# Raspberry Pi (ARMv7) Emulation via Docker

## 1\. Overview

This document provides instructions to create and use a Docker-based emulated ARMv7 environment based on Debian Bullseye.

The environment is intended for developing and testing applications that target 32-bit ARM systems. Due to limitations of the host emulation platform (Docker Desktop with WSL2 and QEMU), standard privilege escalation tools (`sudo`, `su`) are non-functional within the container's SSH session. Administrative access is achieved via the `docker exec` command.

## 2\. Prerequisites

  * Docker Desktop for Windows, with the WSL2 backend enabled and running.

## 3\. Setup Procedure

### 3.1. Create the Dockerfile

In your project directory, create a subdirectory named `rpi_docker`. Inside `rpi_docker`, create a file named `Dockerfile` with the following content.

```dockerfile
# Base image for ARMv7 (32-bit)
FROM arm32v7/debian:bullseye-slim

# Install necessary software
RUN apt-get update && apt-get install -y \
    openssh-server \
    sudo \
    nano \
    curl \
    net-tools \
    apt-utils \
    --no-install-recommends && \
    rm -rf /var/lib/apt/lists/*

# Set permissions for sudo binary. Note: This does not make sudo functional
# in this environment, but is included for correctness.
RUN chmod u+s /usr/bin/sudo

# Create standard 'pi' user and add to sudo group.
RUN useradd -m -s /bin/bash pi && \
    adduser pi sudo

# Set passwords for 'pi' and 'root' users.
# Note: The root password is not usable via su due to environment restrictions.
RUN echo 'root:raspberry' | chpasswd
RUN echo 'pi:raspberry' | chpasswd

# Configure OpenSSH server
RUN sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config
RUN mkdir -p /run/sshd
EXPOSE 22

# Default command to start the SSH daemon
CMD ["/usr/sbin/sshd", "-D"]
```

### 3.2. Build the Docker Image

1.  Navigate your terminal into the `rpi_docker` directory.
    ```bash
    cd path/to/your/project/rpi_docker
    ```
2.  Execute the build command. The `.` specifies the current directory as the build context.
    ```bash
    docker buildx build --platform linux/arm/v7 -t rpi_emulated:latest --load .
    ```

## 4\. Usage

### 4.1. Run the Container

Execute the following command from your host terminal to start the container in detached mode.

```bash
docker run -d -p 2222:22 --name rpi_ssh rpi_emulated:latest
```

### 4.2. Accessing the Container

A two-terminal workflow is required for both user-level and administrative access.

#### 4.2.1. Standard User Access (pi)

For non-administrative tasks like editing files or running code as a standard user.

1.  Connect via SSH. The password is `raspberry`.
    ```bash
    ssh pi@localhost -p 2222
    ```
2.  If you receive a `REMOTE HOST IDENTIFICATION HAS CHANGED` error, remove the old host key first, then reconnect.
    ```bash
    ssh-keygen -R '[localhost]:2222'
    ```

#### 4.2.2. Administrator Access (root)

For administrative tasks like installing packages (`apt install`).

1.  Open a **new** host terminal window.
2.  Execute the following command to get an interactive root shell in the running container.
    ```bash
    docker exec -it --user root rpi_ssh /bin/bash
    ```
3.  You will be presented with a root prompt (`root@<container_id>:/#`) and can execute administrative commands directly without `sudo`.

## 5\. Known Limitations

  * **Privilege Escalation:** `sudo` and `su` are non-functional within the SSH session. The filesystem is mounted by the host with properties that prevent these tools from gaining root privileges, resulting in errors. The mandatory workflow is to use `docker exec` for root access as described in section 4.2.2.

## 6\. Container Management Commands

  * **List running containers:** `docker ps`
  * **Stop the container:** `docker stop rpi_ssh`
  * **Start a stopped container:** `docker start rpi_ssh`
  * **Remove the container (must be stopped first):** `docker rm rpi_ssh`


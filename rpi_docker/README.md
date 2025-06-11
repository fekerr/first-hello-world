# Raspberry Pi (ARMv7) Emulation via Docker

This document provides instructions to set up and use a Docker-based Raspberry Pi (ARMv7) emulated environment. This allows you to develop and test applications for ARMv7 architecture, specifically targeting a Debian Bullseye environment similar to Raspberry Pi OS.

## Quick Start

1.  **Ensure Prerequisites:**
    *   Docker installed and running.
    *   Docker Buildx enabled (usually default with modern Docker).
    *   QEMU user-mode static handlers registered:
        \`\`\`sh
        docker run --rm --privileged multiarch/qemu-user-static --reset -p yes
        \`\`\`
        (Run this once if you haven't before or if you encounter issues running ARM containers).

2.  **Build the Docker Image:**
    (From the repository root)
    \`\`\`sh
    docker buildx build --platform linux/arm/v7 -t rpi_emulated:latest --load rpi_docker/
    \`\`\`

3.  **Run the Container:**
    \`\`\`sh
    docker run -d -p 2222:22 --name rpi_ssh rpi_emulated:latest
    \`\`\`

4.  **SSH into the Emulated RPi:**
    (Allow a few seconds for SSH to start)
    \`\`\`sh
    ssh pi@localhost -p 2222
    \`\`\`
    (Default password: \`raspberry\`)

    As root:
    \`\`\`sh
    ssh root@localhost -p 2222
    \`\`\`
    (Default password: \`raspberry\`)

5.  **Stop the Container:**
    \`\`\`sh
    docker stop rpi_ssh
    \`\`\`

## Detailed Documentation

### Purpose

This Docker setup provides an emulated ARMv7 environment based on Debian Bullseye Slim. It's designed for tasks like:
- Developing and testing applications for Raspberry Pi (32-bit ARM).
- Cross-compilation validation.
- Running ARM-specific tools and utilities.

### Prerequisites

*   **Docker:** The Docker platform must be installed and the Docker daemon running.
*   **Docker Buildx:** Required for building multi-architecture images. Check with \`docker buildx version\`. It is usually available by default in recent Docker installations.
*   **QEMU User-Mode Static Handlers:** To run ARM architecture containers on a non-ARM host system (e.g., x86_64), QEMU's static handlers must be registered. You can do this by running:
    \`\`\`sh
    docker run --rm --privileged multiarch/qemu-user-static --reset -p yes
    \`\`\`
    This command typically only needs to be run once per host system setup.

### Dockerfile Overview

The configuration is defined in \`rpi_docker/Dockerfile\` within this repository. Key aspects:
- **Base Image:** \`arm32v7/debian:bullseye-slim\`.
- **Installed Software:** Includes \`openssh-server\`, \`sudo\`, \`nano\`, \`curl\`, \`net-tools\`, and \`apt-utils\`.
- **Users:**
    - \`root\` with password \`raspberry\`.
    - \`pi\` (standard user with \`sudo\` privileges) with password \`raspberry\`.
- **SSH:** OpenSSH server is installed and configured to start automatically. Port 22 is exposed.

### Building the Image

Navigate to the root directory of this repository. The build command utilizes \`docker buildx\` to target the \`linux/arm/v7\` platform:

\`\`\`sh
docker buildx build --platform linux/arm/v7 -t rpi_emulated:latest --load rpi_docker/
\`\`\`
- \`--platform linux/arm/v7\`: Specifies the target architecture.
- \`-t rpi_emulated:latest\`: Tags the image as \`rpi_emulated:latest\`.
- \`--load\`: This flag ensures the built image is made available in the local Docker image store, not just the buildx cache.
- \`rpi_docker/\`: The build context (directory containing the Dockerfile).

### Running the Container

To run the container in detached mode with SSH port mapping:

\`\`\`sh
docker run -d -p 2222:22 --name rpi_ssh rpi_emulated:latest
\`\`\`
- \`-d\`: Runs the container in the background.
- \`-p 2222:22\`: Maps port \`2222\` on your host computer to port \`22\` (SSH) inside the container. You can choose a different host port if \`2222\` is in use.
- \`--name rpi_ssh\`: Assigns a manageable name (\`rpi_ssh\`) to your container.

### Accessing via SSH

After starting the container, wait a few moments for the SSH service to initialize.

-   **As user \`pi\`:**
    \`\`\`sh
    ssh pi@localhost -p 2222
    \`\`\`
    Password: \`raspberry\`

-   **As user \`root\`:**
    \`\`\`sh
    ssh root@localhost -p 2222
    \`\`\`
    Password: \`raspberry\`

### Security Recommendations

*   **Change Default Passwords:** Upon first login, it is crucial to change the default passwords for both the \`pi\` and \`root\` users. Use the \`passwd\` command inside the container:
    \`\`\`sh
    passwd pi
    passwd root
    \`\`\`
*   **SSH Key Authentication:** For enhanced security, consider setting up SSH key-based authentication and disabling password authentication. This involves generating an SSH key pair, copying the public key to \`~/.ssh/authorized_keys\` within the container for the desired user, and then modifying \`/etc/ssh/sshd_config\` to set \`PasswordAuthentication no\`.

### Container Management

-   **View Logs:**
    \`\`\`sh
    docker logs rpi_ssh
    \`\`\`
-   **Stop the Container:**
    \`\`\`sh
    docker stop rpi_ssh
    \`\`\`
-   **Start a Stopped Container:**
    \`\`\`sh
    docker start rpi_ssh
    \`\`\`
-   **Remove the Container (when no longer needed, after stopping):**
    \`\`\`sh
    docker rm rpi_ssh
    \`\`\`

### Environment Notes

The emulated environment is \`linux/arm/v7\` running Debian Bullseye Slim. While it provides a good general ARMv7 environment, it may not perfectly replicate every aspect of a physical Raspberry Pi running Raspberry Pi OS (which is also Debian-based but has Raspberry Pi-specific configurations and pre-installed software).

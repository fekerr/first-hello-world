Acknowledged. This is a sound documentation strategy. The main `README.md` will serve as an entry point, and `doit.md` will be the detailed reference for the control script.

Here are the contents for the two files.

-----

### File: `README.md`

````markdown
# Raspberry Pi (ARMv7) Emulation via Docker

## 1. Overview

This project provides a Docker-based emulated ARMv7 environment based on Debian Bullseye, suitable for developing and testing applications that target 32-bit ARM systems.

The entire lifecycle of building, running, and managing this environment is handled by the `./doit` utility script.

## 2. Prerequisites

- Docker Desktop for Windows, with the WSL2 backend enabled and running.

## 3. Quick Start

All commands should be run from within the `rpi_docker` directory.

1.  **Make the script executable (one-time action):**
    ```bash
    chmod +x doit
    ```

2.  **Perform one-time QEMU setup:**
    ```bash
    ./doit install
    ```

3.  **Build, run, and connect to the environment:**
    ```bash
    ./doit cleanrestart
    ```

## 4. Detailed Usage

All operations are managed via the `doit` script. For a complete reference of all available commands and their functions, see the script's dedicated documentation.

**[-> See `doit.md` for detailed command reference.](./doit.md)**
````

-----

### File: `doit.md`

````markdown
# `doit` Script Reference

## Overview

The `doit` script is the primary tool for managing the Raspberry Pi emulation environment. It automates all setup, build, execution, and cleanup tasks.

**Location:** This script must be run from within the `rpi_docker` directory.

**Usage:**
```bash
./doit [command]
````

-----

## Workflow Commands

### `./doit cleanrestart`

Runs the full, automated cycle of operations to ensure a fresh environment. This is the recommended command for starting a work session.

It executes the following commands in sequence:

1.  `cleanup`: Stops and removes any existing project container.
2.  `build`: Builds the Docker image from the `Dockerfile`.
3.  `run`: Starts a new container from the built image.
4.  Waits 5 seconds for the SSH service to initialize.
5.  `ssh`: Connects to the new container.

-----

## Individual Commands

### `./doit install`

Performs the one-time registration of QEMU handlers required for Docker to run multi-architecture images. This command only needs to be run once per host machine setup.

### `./doit build`

Builds the Docker image, tagging it as `rpi_emulated:latest`. The build context is the current (`rpi_docker`) directory.

### `./doit run`

Starts the container in the background. This command performs the following actions:

1.  Ensures the `./shared` directory exists on the host, creating it if necessary.
2.  Stops and removes any older container with the same name (`rpi_ssh`) to prevent conflicts.
3.  Starts the new container with port 2222 on the host mapped to port 22 in the container.
4.  Creates a bind mount, mapping the host's `./shared` directory to `/home/pi/shared` inside the container for real-time file sharing.

### `./doit stop`

Stops the running project container. It does not produce an error if the container is already stopped.

### `./doit status`

Provides a diagnostic report on the environment, checking four key areas:

1.  **Docker Daemon:** Verifies that the Docker engine is running.
2.  **Docker Image:** Checks if the `rpi_emulated:latest` image has been built.
3.  **Project Container:** Shows the status of the `rpi_ssh` container (running, exited, or not created).
4.  **Bind Mount:** Verifies the existence of the local `./shared` directory and checks if the running container has the mount point configured.

### `./doit ssh`

Connects to the running container as the `pi` user.

  - It automatically handles SSH host key changes, preventing connection errors when the container is recreated.
  - It provides a reminder of the default password (`raspberry`) before the password prompt.

### `./doit root`

Provides administrator-level access to the running container.

  - It opens a new, interactive root shell using `docker exec`.
  - **This is the mandatory method for performing administrative tasks** (e.g., `apt install`), as `sudo` is non-functional within the SSH session.

### `./doit cleanup`

Stops and permanently removes the project container (`rpi_ssh`). It does not produce an error if the container does not exist.

### `./doit help`

Displays a list of all available commands and their descriptions.

```
```

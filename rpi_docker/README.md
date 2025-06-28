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

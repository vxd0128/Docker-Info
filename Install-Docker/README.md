# Install Docker (Prerequisite)

Before building or running Docker images, Docker must be installed on the machine.

---

## Directory

* [1. Download Docker Desktop](#1-download-docker-desktop)
* [2. Install Docker](#2-install-docker)
* [3. Start Docker](#3-start-docker)
* [4. Verify Installation](#4-verify-installation)
* [5. Run a Test Container](#5-run-a-test-container)

---

## 1. Download Docker Desktop

Download Docker Desktop from the official website:

[https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/)

Choose the version for your operating system:

* **Mac (Apple Silicon or Intel)**
* **Windows**
* **Linux**

---

## 2. Install Docker

Run the installer you downloaded and follow the setup instructions.

Typical installation steps:

1. Open the installer.
2. Follow the guided setup.
3. Allow Docker to install required components.
4. Restart the computer if prompted.

---

## 3. Start Docker

After installation:

1. Open **Docker Desktop**.
2. Wait until Docker finishes starting.

You should see Docker running in the system tray or menu bar.

---

## 4. Verify Installation

Open a terminal and run:

```bash
docker --version
```

Example output:

```text
Docker version 25.x.x
```

---

## 5. Run a Test Container

Run the Docker test container:

```bash
docker run hello-world
```

Expected output:

```text
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

If you see this message, Docker is installed correctly.

---

## Additional Useful Commands

List Docker images:

```bash
docker images
```

List running containers:

```bash
docker ps
```

---

## Next Step

Once Docker is installed and working, continue with the guide for:

* Creating a **Dockerfile**
* Building a **Docker image**
* Running a **Docker container**


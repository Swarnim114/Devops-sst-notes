Here are your **to-the-point class notes** in clean **Obsidian Markdown format** — perfect for copy-paste 👇

---

# 🐳 Docker & System Concepts

## ⚙️ Why Docker is Required

* Ensures **consistency** across environments (works the same on any system).
* Provides **isolation** — each app runs in its own container.
* Lightweight alternative to VMs — **no full OS overhead**.
* Enables **faster deployment** and **scaling**.
* Makes **CI/CD & microservices** easy.

---

## 🧠 How Docker Works on Windows / Mac

* Docker is built for the **Linux kernel** (uses namespaces + cgroups).
* Windows & macOS don’t have the Linux kernel → Docker uses a **lightweight Linux VM**:

  * **Windows:** via **WSL2** or older **Hyper-V**.
  * **Mac:** via **HyperKit** or **Apple Virtualization Framework**.
* The Linux VM provides the kernel → containers run normally inside it.

---

## 💻 What is a Hypervisor

* Software layer that **creates & manages virtual machines (VMs)**.
* Sits between **hardware** and **virtual OSes**.
* Types:

  * **Type 1 (bare-metal):** runs directly on hardware (e.g., VMware ESXi, Hyper-V Server).
  * **Type 2 (hosted):** runs on an existing OS (e.g., VirtualBox, VMware Workstation).

---

## 🧩 How a Hypervisor Creates a Virtual Machine

* Divides hardware resources (CPU, RAM, disk, etc.) into **virtual units**.
* Allocates these to guest OSes.
* Uses **hardware virtualization extensions** (Intel VT-x / AMD-V).
* Each VM runs its own OS, kernel, and apps as if on separate hardware.

---

## ⚙️ What Hypervisor Does to Hardware

* Controls **CPU scheduling** among VMs.
* Manages **memory mapping** (each VM thinks it has its own RAM).
* Emulates **network adapters, disks, and devices**.
* Handles **I/O isolation** so VMs don’t interfere with each other.

---

## 🧱 Difference Between VM and Container

| Feature     | Virtual Machine               | Container              |
| ----------- | ----------------------------- | ---------------------- |
| Kernel      | Each VM has its own OS/kernel | All share host kernel  |
| Size        | Heavy (GBs)                   | Lightweight (MBs)      |
| Boot time   | Minutes                       | Seconds                |
| Isolation   | Full hardware-level           | Process-level          |
| Performance | Slower (emulation)            | Faster (native)        |
| Use case    | Multiple OS types             | Same OS, isolated apps |

---

## 🔌 BIOS / EFI / UEFI / MBR / Bootloader

### 🧬 BIOS

* Basic Input/Output System.
* Old firmware interface between OS & hardware.
* Stored in motherboard ROM.
* Runs POST → finds bootable device.

### ⚙️ EFI (Extensible Firmware Interface)

* Early Intel replacement for BIOS.
* Defined boot process more flexibly.

### 🧠 UEFI (Unified EFI)

* Modern standard replacing BIOS.
* Supports **GPT partition**, large disks, secure boot, GUI setup, faster boot.

### 💽 MBR (Master Boot Record)

* First sector of storage (512 bytes).
* Contains partition table + bootloader code.
* Used in BIOS systems only.

### 🚀 Bootloader

* Program that loads the OS kernel into memory (e.g., GRUB, LILO).
* Located in MBR or EFI partition.

### 🧾 Difference Between Bootloader and MBR

| Term           | Function                                                       |
| -------------- | -------------------------------------------------------------- |
| **MBR**        | The first sector on disk containing partition info + boot code |
| **Bootloader** | The program *inside* or *after* MBR that loads the OS          |

---

## ⚙️ Init Calls During Bootup

* Kernel starts **`init`** (or **`systemd`**) as PID 1.
* It initializes:

  1. Mounts filesystems
  2. Starts essential daemons
  3. Brings up networking
  4. Spawns login shells / display manager

---

# 🐋 Docker Commands

### 🧩 `docker run`

* Creates and runs a new container.

```bash
docker run ubuntu
```

### 🔍 `docker inspect`

* Shows detailed info about container/image (IP, volumes, etc.)

```bash
docker inspect <container_id>
```

### 🕹️ `docker run -d`

* Runs container **detached (background)**.

```bash
docker run -d nginx
```

### 📋 `docker ps`

* Shows **running** containers.

```bash
docker ps
```

### 🧾 `docker ps -a`

* Shows **all** containers (running + stopped).

### 🌐 `curl` command to check container

* Check if container web service is running:

```bash
curl http://localhost:<port>
```

### 🧠 Getting inside container (`exec`)

```bash
docker exec -it <container_id> bash
```

→ Opens interactive shell inside container.

### 🎛️ `docker run -it`

* Runs container interactively with TTY.

```bash
docker run -it ubuntu
```

### 🔄 `docker start`

* Starts a **stopped** container.

```bash
docker start -ai <container_id>
```

---

# 🧱 Creating Image from Container

### 🧩 `docker commit`

* Create image from an existing container’s state.

```bash
docker commit <container_id> <image_name>:<tag>
```

Example:

```bash
docker commit 33 myubuntuimage:v1
```

---

## 🧠 Difference Between Docker Image and Container

| Term          | Description                                                     |
| ------------- | --------------------------------------------------------------- |
| **Image**     | Blueprint / snapshot (read-only layers) for creating containers |
| **Container** | Running instance of an image (read-write layer on top)          |
| **Analogy**   | Image = class, Container = object                               |


## ⚙️ Basic Commands

### 🛑 `docker stop`

* Gracefully stops a **running container**.
* Sends `SIGTERM`, then `SIGKILL` if it doesn’t stop in time.

```bash
docker stop <container_id_or_name>
```

---

### 📤 `docker push`

* Uploads (pushes) a **Docker image** to **Docker Hub** or another registry.
* Requires you to be **logged in** and use a **properly tagged image**.

```bash
docker push username/image_name:tag
```

---

### 🏷️ `docker tag`

* Creates a **new tag** (alias) for an existing image.
* Used for **versioning** or naming before pushing.

```bash
docker tag <source_image> <username/repository>:<tag>
```

Example:

```bash
docker tag ubuntu:latest swarnim114/ubuntu_image:v1
```

---

### 💀 `docker kill`

* **Forcefully stops** a running container immediately.
* Sends `SIGKILL` (no cleanup).

```bash
docker kill <container_id_or_name>
```

---

### 🧹 `docker container prune`

* Deletes **all stopped containers**.
* Frees up disk space.

```bash
docker container prune
# Use -f to skip confirmation
docker container prune -f
```

---

### 🗑️ `docker rmi`

* Removes one or more **images** from the local system.
* Cannot delete images used by running containers.

```bash
docker rmi <image_id_or_name>
```

Examples:

```bash
docker rmi ubuntu:latest
docker image prune -a   # remove all unused images
```

---

# 🧱 Docker Internal Structure

## 🧩 Layers

* Docker images are made of **stacked read-only layers**.
* Each Dockerfile instruction (e.g., `RUN`, `COPY`, `ADD`) creates a **new layer**.
* Containers add a **read-write layer** on top of image layers.
* Layers are reused across images → **saves space** and speeds up builds.

```
Base Layer (Ubuntu)
   ├─ RUN apt install python → Layer 1
   ├─ COPY app/ /app        → Layer 2
   └─ CMD ["python3","app.py"] → Layer 3
-------------------------------------------
   ↑ Writable layer (container runtime)
```

---

## 🧠 Overlay2 (Storage Driver)

* Default **storage driver** for Docker on Linux.
* Implements **Copy-on-Write (CoW)** — each container sees a single unified filesystem.
* Stores all layer data under:

```
/var/lib/docker/overlay2/
```

* Benefits:

  * Fast and space-efficient.
  * Shares unchanged layers between containers.

---

## 🏷️ Matching Image Tags

* Tags uniquely identify versions of an image.
* Format:

  ```
  <repository>:<tag>
  ```

  Examples:

  * `ubuntu:22.04`
  * `ubuntu:latest`
* If no tag specified → defaults to `latest`.
* Tags can **point to the same image ID** (aliases).

```bash
docker tag myimage:latest myimage:v2
```

Both tags refer to the same image until rebuilt.

---

# 🧾 Summary Table

| Command                  | Description               | Example                              |
| ------------------------ | ------------------------- | ------------------------------------ |
| `docker stop`            | Gracefully stop container | `docker stop webapp`                 |
| `docker kill`            | Force stop container      | `docker kill webapp`                 |
| `docker tag`             | Label/version image       | `docker tag ubuntu myuser/ubuntu:v1` |
| `docker push`            | Upload image to Hub       | `docker push myuser/ubuntu:v1`       |
| `docker container prune` | Delete stopped containers | `docker container prune`             |
| `docker rmi`             | Delete local images       | `docker rmi ubuntu:latest`           |

---

# 🧩 Docker Structure Overview

```
[ Docker CLI ] → sends commands to → [ Docker Daemon ]
       │                                     │
       ▼                                     ▼
   User Commands                     Builds / Runs containers
       │                                     │
       ▼                                     ▼
  [ Images (Layers) ] → Overlay2 (Union FS) → [ Containers (RW layer) ]
```

---



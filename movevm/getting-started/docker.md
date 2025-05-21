# Introduction to Docker

Before installing the Supra CLI, you will need to install Docker. It’s helpful to understand what Docker is, how it works, and why we use it for this setup.

***

## Prerequisites

* [ ] Install [Docker Desktop](https://www.docker.com/) ([Windows](https://docs.docker.com/desktop/setup/install/windows-install/), [Mac](https://docs.docker.com/desktop/setup/install/mac-install/), [Linux](https://docs.docker.com/desktop/setup/install/linux/))

***

## 🔧 What Is Docker?

**Docker** is a platform that packages applications into lightweight, portable **containers**. These containers include everything an app needs to run: code, runtime, libraries, and environment variables.

{% hint style="info" %}
Think of containers as lightweight, isolated virtual machines — but more efficient and faster to start.
{% endhint %}

***

## 💡 Why Use Docker with the Supra CLI?

Docker provides a consistent, repeatable environment that eliminates:

* 🧩 OS differences (Linux, Mac, Windows)
* ⚙️ Dependency/version mismatches
* ❌ “It works on my machine” issues

With Docker, developers can run the **Supra CLI** in a pre-configured environment without needing to install the CLI or dependencies locally.

***

## 📦 What Is Docker Compose?

**Docker Compose** is a tool used to define and run multi-container Docker applications using a YAML file (`compose.yaml` or `docker-compose.yml`).

In our case, the Compose file:

* Defines the container configuration for the Supra CLI
* Specifies the image to use
* Sets up **bind mounts** to share files between your machine and the container

***

## 🔗 Bind Mounts

A **bind mount** is a way to share directories between your host and the container.

In the Supra CLI setup:

* The `supra/configs` folder on your host is bind-mounted to the `configs/` folder inside the container.
* The `supra/move_workspace` folder on your host is bind-mounted to the `move_workspace/` folder inside the container.
* This means anything you create/do inside these folders within the container will appear on your host — and vice versa.

{% hint style="info" %}
📂 Example: `~/Documents/supra/configs ↔ /supra/configs`&#x20;
{% endhint %}

***

## 🔓 Entering the Shell of a Container

Once a container is running, you can open a terminal session _inside_ it using:

```bash
docker exec -it supra_cli /bin/bash
```

* `exec` tells Docker to run a command inside an existing container.
* `-it` allows interactive terminal input/output.
* `supra_cli` is the name of the container.
* `/bin/bash` starts a shell session.

This is useful when you want to:

* Run the Supra CLI directly (`supra move tool ...`)
* Explore the file system
* Debug issues inside the container

{% hint style="info" %}
💡 Make sure the container is running first:\
Use `docker start supra_cli` if needed.
{% endhint %}

***

## ⚙️ Common Docker Commands

| Command                                 | Description                                |
| --------------------------------------- | ------------------------------------------ |
| `docker ps --all`                       | Lists all containers (running and stopped) |
| `docker start <container>`              | Starts an existing container               |
| `docker stop <container>`               | Stops a running container                  |
| `docker exec -it <container> /bin/bash` | Enters the shell of a container            |
| `docker logs <container>`               | Prints logs from a container               |
| `docker rm <container>`                 | Removes a container                        |
| `docker images`                         | Lists downloaded Docker images             |
| `docker volume ls`                      | Lists Docker volumes (persistent storage)  |

***

## 🧰 Common Docker Compose Commands

| Command                  | Description                                           |
| ------------------------ | ----------------------------------------------------- |
| `docker compose up -d`   | Starts containers in detached (background) mode       |
| `docker compose down`    | Stops and removes containers, networks, and volumes   |
| `docker compose ps`      | Lists containers defined in your Compose file         |
| `docker compose logs`    | Shows logs from all Compose services                  |
| `docker compose restart` | Restarts all services defined in the Compose file     |
| `docker compose pull`    | Pulls the latest versions of images from the registry |
| `docker compose build`   | Builds or rebuilds services defined in the file       |

{% hint style="info" %}
✅ You can also use `docker compose -f <file>` to specify a compose file, including remote files via `curl ... | docker compose -f - up`.
{% endhint %}

***

## 📌 Summary

Docker simplifies the Supra CLI experience by:

* Eliminating setup headaches
* Ensuring all developers use the same runtime
* Providing isolated environments with shared config folders

If you're new to Docker, this may feel like a lot — but once set up, you’ll rarely need to modify it again.

***

## 📚 Want to Learn More?

* [Docker Overview (Official Docs)](https://docs.docker.com/get-started/overview/)
* [Docker Compose Overview](https://docs.docker.com/compose/)
* [Docker Bind Mounts](https://docs.docker.com/storage/bind-mounts/)

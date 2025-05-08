---
description: Installation of the Supra CLI using Docker.
---

# Install Supra CLI with Docker

{% hint style="success" %}
On May 2nd, 2025 a new version of the CLI was released. To update your CLI to the latest version, please repeat the steps below.
{% endhint %}

## Prerequisites

* [ ] Install [Docker](https://www.docker.com/) ([Windows](https://docs.docker.com/desktop/setup/install/windows-install/), [Mac](https://docs.docker.com/desktop/setup/install/mac-install/), [Linux](https://docs.docker.com/desktop/setup/install/linux/))
* [ ] Follow [Docker post-installation steps](https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user) (Linux only, If needed)
* [ ] Review the [Docker Cheat Sheet](https://docs.docker.com/get-started/docker_cheatsheet.pdf) for useful commands

{% hint style="info" %}
The installation process of Docker is straight forward and requires no additional configuration. The only requirement to proceed is to ensure the [Docker Daemon](https://docs.docker.com/engine/daemon/start/) is running. If you installed Docker Engine, opening the program will start the daemon.
{% endhint %}

## Install and Setup the CLI

{% stepper %}
{% step %}
### Start the container with Docker Compose

Open your terminal and `cd` to the directory on your machine where you wish to setup the CLI. For convenience, we recommend creating a directory named `supra` within your `documents` directory. However, this is entirely arbitrary and there will be no repercussions for choosing differently.

{% hint style="info" %}
`compose.yaml` is a YAML configuration file that contains all of the parameters needed to configure the container. The file contains the link to the Docker Image containing the Supra CLI binary, bind mount paths to enable file sharing between the host machine/container, and more. You can read more about [how compose works here](https://docs.docker.com/compose/intro/compose-application-model/).
{% endhint %}

The following command will use the latest compose file (which you can [view here](https://github.com/Entropy-Foundation/supra-dev-hub/blob/main/Scripts/cli/compose.yaml)) and pipe it into the Docker Compose command. This will pull the latest image, create, and start the container.

{% code title="execute me!" overflow="wrap" %}
```bash
curl -s https://raw.githubusercontent.com/Entropy-Foundation/supra-dev-hub/refs/heads/main/Scripts/cli/compose.yaml | docker compose -f - up -d
```
{% endcode %}

Once executed, the `supra_cli` container will be created. If a `supra_configs` directory does not already exist within your current working directory, one will be created for you.&#x20;

{% hint style="info" %}
The `supra_configs` directory will be [bind mounted](https://docs.docker.com/engine/storage/bind-mounts/) to the `configs` directory within the Docker container. Files within these directories will coexist between the host machine and the container. Generated key profiles, logs, and more can be found within these folders. This setup is defined within the `compose.yaml` file under the `volumes` section.
{% endhint %}

You can confirm that the container is running by executing `docker ps --all`. This command will output all docker containers that exist on your machine, whether they are running or stopped.
{% endstep %}

{% step %}
### Enter the shell of the container

{% hint style="info" %}
Docker containers are a form of virtualization that contain everything needed to run the desired application. This makes the distribution and setup of the Supra CLI extremely convenient. There are many useful actions that can be taken with this setup, but for now it is important to recognize that the CLI itself is executed within the container, not on your host machine.
{% endhint %}

To interact with the Supra CLI, we must enter the shell of the container. This will allow us to execute commands within the container from our host machine.&#x20;

{% code title="execute me!" %}
```bash
docker exec -it supra_cli /bin/bash 
```
{% endcode %}

Once inside, execute the `ls` command to gain a simple understanding of the file system. The displayed `configs` directory is bind mounted to the `supra_configs` directory on your host machine. These directories are shared/linked between your host machine and the container.

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption><p>Note: "supra" is the CLI binary within the container. <br>The compose file automatically setup the alias to interact with the binary using the "supra" command.</p></figcaption></figure>
{% endstep %}

{% step %}
### Execute the `supra` command

{% hint style="info" %}
Within the shell of the container, CLI commands operate in the same manner as you would expect on your host machine. For example, executing commands such as `supra move tool init` will initialize the new module within your current working directory. You may view all available arguments of any command by passing the `--help` argument.&#x20;

Example: `supra move tool init --help`
{% endhint %}
{% endstep %}
{% endstepper %}

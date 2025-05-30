---
description: Installation of the Supra CLI using Docker.
---

# Setup Supra CLI

{% hint style="success" %}
On May 2nd, 2025 a new version of the CLI was released. To update your CLI to the latest version, please repeat the steps below.
{% endhint %}

## Prerequisites

* [ ] Install [Docker Desktop](https://www.docker.com/) ([Windows](https://docs.docker.com/desktop/setup/install/windows-install/), [Mac](https://docs.docker.com/desktop/setup/install/mac-install/), [Linux](https://docs.docker.com/desktop/setup/install/linux/))
* [ ] If you are new to Docker, review the [Introduction to Docker](docker.md) page

{% hint style="info" %}
The installation process of Docker is straight forward and requires no additional configuration. The only requirement to proceed is to ensure the [Docker Daemon](https://docs.docker.com/engine/daemon/start/) is running. If you installed Docker Engine, opening the program will start the daemon.
{% endhint %}

## Install and Setup the CLI

{% stepper %}
{% step %}
### Start the container with Docker Compose

Open your terminal and `cd` to the directory on your machine where you wish to setup the CLI. For convenience, we recommend placing it within the `Documents` directory. However, this is entirely arbitrary and there will be no repercussions for choosing differently.

{% code title="execute me!" %}
```bash
cd Documents
```
{% endcode %}

The following command will use the latest compose file (which you can [view here](https://github.com/Supra-Labs/supra-dev-hub/blob/main/Scripts/cli/compose.yaml)) and pipe it into the [Docker Compose command](docker.md#what-is-docker-compose). This will pull the latest image, create, and start the container.\
\
Please note that the following command will create a `supra` directory within your current working directory if one does not already exist.

{% code title="execute me!" overflow="wrap" %}
```bash
curl https://raw.githubusercontent.com/supra-labs/supra-dev-hub/refs/heads/main/Scripts/cli/compose.yaml | docker compose -f - up -d
```
{% endcode %}

You can confirm that the container is running by executing `docker ps --all`. This command will output all docker containers that exist on your machine, whether they are running or stopped.
{% endstep %}

{% step %}
### Enter the shell of the container

To interact with the Supra CLI, we must enter the shell of the container. This will allow us to execute commands within the container from our host machine.&#x20;

{% code title="execute me!" %}
```bash
docker exec -it supra_cli /bin/bash 
```
{% endcode %}

Once inside, execute the `ls` command to gain a simple understanding of the file system. The displayed `configs` directory is bind mounted to the `/supra/configs` directory on your host machine. These directories are shared/linked between your host machine and the container.

<figure><img src="../.gitbook/assets/Screenshot 2025-05-20 at 11.15.05 PM.png" alt=""><figcaption><p>Note: "supra" is the CLI binary within the container. <br>The compose file automatically setup the alias to interact with the binary using the "supra" command.</p></figcaption></figure>
{% endstep %}

{% step %}
### Execute the `supra` command

<figure><img src="../.gitbook/assets/Screenshot 2025-05-21 at 1.55.02 AM.png" alt="" width="563"><figcaption><p>Example output of the "supra" command</p></figcaption></figure>

{% hint style="info" %}
Within the shell of the container, CLI commands operate in the same manner as you would expect on your host machine. For example, executing commands such as `supra move tool init` will initialize the new module within your current working directory. You may view all available arguments of any command by passing the `--help` argument.&#x20;

Example: `supra move tool init --help`
{% endhint %}
{% endstep %}
{% endstepper %}

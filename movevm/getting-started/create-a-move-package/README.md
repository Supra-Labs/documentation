---
description: Create and deploy your first move module.
---

# Create a Move Package

{% hint style="danger" %}
If you are not currently within the shell of your container, please execute the following command and enter the shell: `docker exec -it supra_cli /bin/bash`&#x20;

If no such container exists, you must [complete the installation guide](../supra-cli-with-docker.md) before proceeding. If your container is not running, you can start your container with the command: \
`docker start supra_cli`&#x20;
{% endhint %}

{% hint style="info" %}
Reminder, you can view all existing containers on your machine with: `docker ps --all`
{% endhint %}

## Setup your workspace

{% stepper %}
{% step %}
### Create your `move_workspace` directory

The first step to deploying your first Move contract is to setup a directory that will house all of your package files/code.&#x20;

If you recall from the CLI installation guide, the `configs` directory is bind mounted to the `supra_configs` directory on your host machine.&#x20;

These directories are shared/linked as if they are one, so files will coexist between both the host and the container. We recommend creating a dedicated `move_workspace` directory to house all of your move packages/projects within this linked directory. This will dramatically improve your development cycle as edits made on your host machine will be observed within the container.

Within the shell of your container, `cd` into the configs directory and execute `mkdir move_workspace`.&#x20;

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Observe the bind mount in action

To visualize the bind mounted directories, open the file explorer on your host machine and navigate to the `supra_configs` directory. You should observe the presence of the `move_workspace` directory following the execution of the `mkdir` command.&#x20;

{% hint style="warning" %}
If this is not the case, your bind mount is not setup properly. Repeat the [install guide](../supra-cli-with-docker.md) and try again. If the issue persists, our team will be more than happy to help debug your issue within our [Discord server](https://discord.gg/supralabs).
{% endhint %}
{% endstep %}
{% endstepper %}

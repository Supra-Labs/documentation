# Step 1

## Generate and Store the SSH Key

Generate an SSH Key Pair on the Old Instance:

* Run the following command on the old instance to generate an SSH key pair (if it doesn’t already exist) on the new instance.

```
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -C "your_email@example.com"
```

* Copy the SSH Public Key to the New Instance:

Use the following command to transfer the public key to the new instance.

```
ssh-copy-id -i ~/.ssh/id_rsa.pub username@new_instance_ip
```

* Remove the Public Key from the New Instance (Post-Termination of Old Node):

1. Once the old instance is terminated, remove its public key from the `authorized_keys` file on the new instance to revoke access.
2. Delete the corresponding key entry and save the file.

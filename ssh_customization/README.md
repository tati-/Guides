# SSH customization

## Establish passwordless SSH connection

### Generate ssh key in local machine
>**_NOTE:_**  In general a single ssh key per machine is enough / advised.

in terminal run:
```sh
ssh-keygen
```
(specify the file to save the key, for example `/path/to/home/.ssh/id_rsa_mykey`)

### Copy the key to the target server
```sh
ssh-copy-id -i ~/.ssh/id_rsa_mykey user@TARGET
```

### Test the new key
```sh
ssh user@TARGET
```
The login should now complete **without** asking for a password. Note, however, that the command might ask for the passphrase you specified for the key.

### Add key to SSH-agent
The ssh-agent is a key manager for SSH. It holds the ssh keys and certificates in memory, unencrypted, and ready for use by `ssh`.  It runs in the background, separately from `ssh`, and it usually starts up the first time you run `ssh` after a reboot.

The behavior of ssh-agent depends on the flavor and version of your operating system. On OS X Leopard or later your keys can be saved in the system’s keychain. Most Linux installations will automatically start ssh-agent when you log in.

To add the key to ssh-agent:
```sh
ssh-add ~/.ssh/id_rsa_mykey
```
When running `ssh-add` without any parameters, your home directory will be scanned for some standard keys and those will be added to your agent. By default it looks for:
- `~/.ssh/id_rsa`
- `~/.ssh/id_ed25519`
- `~/.ssh/id_dsa`
- `~/.ssh/id_ecdsa`

More information on SSH-agent can be found in this [post](https://smallstep.com/blog/ssh-agent-explained/).

## Customize SSH environment

### Create SSH alias
To simplify the login to a remote server, you can define a name (alias) for the user/hostname combination, where you can also add some customization.

#### Mac/Linux/Unix

Add a **host declaration** to `~/.ssh/config` file on your local machine. You can see an example below.

```
Host <alias>
        Hostname  deepmachine.univ-lyon2.fr
        User <username>
        IdentityFIle ~/.ssh/id_rsa_mykey
        ProxyJump <alias of proxy server>
        AddKeysToAgent yes
        ServerAliveInterval 60
        RemoteCommand bash
```

From now on you can establish an ssh connection by running
```sh
ssh <alias>
```

#### Some of the options explained
We will now explain some of the options used in the host declaration. More info on the host declaration options can be found in the [manual page](https://linux.die.net/man/5/ssh_config).

##### SSH session timeout
If a SSH connection goes idle for a specific amount of time (default 10 minutes), you may be confronted with a `Write failed: Broken pipe` error message. The connection might also simply be frozen, which forces you to log in again. To prevent this from happening, the option
```sh
 ServerAliveInterval 60
```
allows the client to periodically (e.g. every 60 seconds) send a message to trigger a response from the remote server.

##### SSH key
To avoid having to give your password every time you login, you can create an [ssh key pair](#establish-passwordless-ssh-connection), and add the key to your host declaration with the following line:
```sh
IdentityFIle ~/.ssh/id_rsa_mykey
```

# SSH

## Overview

The server uses OpenSSH for remote administration over the local network.

SSH is configured to allow **key-based authentication only**. Password-based SSH authentication and root login are disabled.

## Installation

The OpenSSH server was installed during the Debian installation.

Package:

```text
openssh-server
```

If SSH needs to be installed manually:

```bash
sudo apt update
sudo apt install openssh-server
```

## Service

Check the SSH service:

```bash
sudo systemctl status ssh
```

Enable SSH to start automatically at boot:

```bash
sudo systemctl enable ssh
```

Restart SSH after configuration changes:

```bash
sudo systemctl restart ssh
```

Verify that SSH is enabled:

```bash
systemctl is-enabled ssh
```

## Server Configuration

The main SSH server configuration file is:

```text
/etc/ssh/sshd_config
```

Additional configuration files may be located in:

```text
/etc/ssh/sshd_config.d/
```

The following authentication settings have been configured:

```text
PermitRootLogin no
PubkeyAuthentication yes
PasswordAuthentication no
KbdInteractiveAuthentication no
UsePAM no
```

### Authentication Policy

| Setting                             | Value    |
| ----------------------------------- | -------- |
| Root login                          | Disabled |
| SSH public-key authentication       | Enabled  |
| Password authentication             | Disabled |
| Keyboard-interactive authentication | Disabled |
| PAM                                 | Disabled |

As a result, SSH connections require a valid authorized SSH key.

## SSH Keys

SSH public-key authentication is used for server access.

The client's public key is installed on the server in:

```text
~/.ssh/authorized_keys
```

Generate an Ed25519 key on the client:

```bash
ssh-keygen -t ed25519
```

Copy the public key to the server:

```bash
ssh-copy-id <username>@<server-ip>
```

## Client Configuration

A client-side SSH configuration is used to simplify connecting to the server.

The configuration file is:

```text
~/.ssh/config
```

```sshconfig
Host home-server
    HostName <server-ip>
    User <username>
    IdentityFile ~/.ssh/home_server
    IdentitiesOnly yes
    Port 22
    ServerAliveInterval 60
    ServerAliveCountMax 3
```

With this configuration, the server can be accessed using:

```bash
ssh home-server
```

### Client Configuration Options

| Option                   | Purpose                                                |
| ------------------------ | ------------------------------------------------------ |
| `Host`                   | Alias used when connecting                             |
| `HostName`               | Server address                                         |
| `User`                   | Remote user                                            |
| `IdentityFile`           | SSH private key used for authentication                |
| `IdentitiesOnly yes`     | Only use the specified identity                        |
| `Port`                   | SSH server port                                        |
| `ServerAliveInterval 60` | Send keepalive every 60 seconds                        |
| `ServerAliveCountMax 3`  | Allow three unanswered keepalives before disconnecting |

The private key referenced by `IdentityFile` is stored only on the client machine.

## Connection

Using the client configuration:

```bash
ssh home-server
```

## Configuration Changes

Before applying changes to `sshd_config`, validate the configuration:

```bash
sudo sshd -t
```

If the command produces no output, the configuration syntax is valid.

Restart SSH after making configuration changes:

```bash
sudo systemctl restart ssh
```

Keep an existing SSH session open while testing configuration changes to avoid being locked out by a configuration mistake.

## Verification

### Check SSH Service

```bash
sudo systemctl status ssh
```
Verify the authentication settings:

```bash
sudo sshd -T | grep -E 'permitrootlogin|pubkeyauthentication|passwordauthentication|kbdinteractiveauthentication|usepam'
```

Expected:

```text
permitrootlogin no
pubkeyauthentication yes
passwordauthentication no
kbdinteractiveauthentication no
usepam no
```

### Test SSH

From the configured client:

```bash
ssh home-server
```

A valid SSH private key is required for authentication.


## Security Configuration

Current SSH security configuration:

* [x] Root login disabled
* [x] Public-key authentication enabled
* [x] Password authentication disabled
* [x] Keyboard-interactive authentication disabled
* [x] SSH enabled at boot
* [x] SSH configuration validated
* [ ] Firewall configured
* [ ] Fail2ban configured


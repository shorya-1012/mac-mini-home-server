
# Installation

## Overview

* Model Identifier: `Macmini6,1`
* Install date: `25-09-2026`
* Installed via: USB flash drive

## Operating System

* OS: Debian 13
* Architecture: `x86_64`
* Desktop Environment: None
* Hostname: `mac-home`

## Pre-Installation

* Boot mode: EFI
* Installation media: Debian 13 ISO
* USB creation method: `dd`
* USB created from: Laptop running Linux

The Debian ISO was written directly to the USB using the `dd` command.

Example:

```bash
sudo dd if=<debian.iso> of=/dev/sdX bs=4M status=progress oflag=sync
```

## Partitioning

The internal 128 GB SSD (`/dev/sda`) was partitioned as follows:

```text
/dev/sda             119.2G  disk
├─/dev/sda1            976M  part  /boot/efi
├─/dev/sda2          112.1G  part  /
└─/dev/sda3            6.2G  part  [SWAP]
```

### Filesystems

* `/dev/sda1` — EFI System Partition — `976 MB`
* `/dev/sda2` — Btrfs — `112.1 GB` — `/`
* `/dev/sda3` — Swap — `6.2 GB`

### Btrfs

The root filesystem is formatted as Btrfs.

* Root: `/dev/sda2`
* Mount point: `/`

No Btrfs subvolumes were created during installation.

### Encryption

* Encryption: No
* LUKS: Not configured

## Installer Configuration

* Install method: Graphical installer
* Base packages: Standard system utilities
* SSH server: Installed
* Desktop environment: None
* System configured as a headless server

## Post-Installation

### Wi-Fi Issue

After installation, the Wi-Fi interface was not working on the Mac mini.

**Cause:** Missing proprietary firmware for the Broadcom `b43` driver, not included by default in Debian.

**Symptoms** (`dmesg | grep b43`):

```text
b43-phy0 ERROR: Firmware file "b43/ucode29_mimo.fw" not found
b43-phy0 ERROR: Firmware file "b43-open/ucode29_mimo.fw" not found
```

**Fix:**

1. Enable the `non-free-firmware` component in `/etc/apt/sources.list` (add `contrib non-free` before `non-free-firmware` on each `deb`/`deb-src` line):

   ```text
   deb http://deb.debian.org/debian/ trixie main contrib non-free non-free-firmware
   ```

2. Install the firmware:

   ```bash
   apt update
   apt install firmware-b43-installer
   reboot
   ```

### GRUB Configuration

After installation, the GRUB configuration was modified to remove the boot menu delay.

The GRUB timeout was set to `0`:

```text
GRUB_TIMEOUT=0
```

After modifying `/etc/default/grub`, the GRUB configuration was regenerated:

```bash
sudo update-grub
```

This allows the system to boot without waiting for the GRUB menu timeout.

### Other Post-Installation Setup

* [x] Configured hostname
* [x] Configured Wi-Fi
* [x] Installed/configured SSH
* [x] Configured stable LAN IP
* [x] Set GRUB timeout to `0`
* [ ] Firewall
* [ ] Automatic security updates
* [ ] Backup system

## Verification

### System

```bash
uname -a
```

```bash
cat /etc/os-release
```

### Architecture

```bash
uname -m
```

```bash
dpkg --print-architecture
```

### Network

Verify the network interfaces:

```bash
ip addr
```

Verify the routing configuration:

```bash
ip route
```

Verify connectivity:

```bash
ping -c 4 192.168.1.1
ping -c 4 google.com
```

### SSH

Verify the SSH service:

```bash
sudo systemctl status ssh
```

### Boot

* Boot completes without manual intervention: `yes`
* Wi-Fi connects automatically: `yes`
* SSH is available after reboot: `yes`
* Stable IP remains assigned after reboot: `yes`
* GRUB boots without a timeout delay: `yes`

## Notes

* The server is currently running headless with no desktop environment.
* The primary network connection is Wi-Fi.
* The system uses Btrfs for the root filesystem.
* No disk encryption is currently configured.
* GRUB is configured with a `0` second timeout for faster boot.



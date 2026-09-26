# Hardware

## Machine

* Model: Mac mini (Late 2012)
* Model Identifier: `Macmini6,1`

## CPU

* Model: Intel Core i5-3210M
* Cores/Threads: 2 cores / 4 threads
* Base clock: 2.5 GHz

## Memory

* Total: 8 GB
* Type: DDR3
* Speed: 1600 MT/s
* Slots used/available: 2/2
* Maximum supported: 16 GB

## Storage

* Device: `/dev/sda`
* Model: XPD0250902TNSR128G
* Type: 2.5" SATA SSD
* Capacity: 128 GB (128,035,676,160 bytes)
* Interface: SATA 3.2 / 6.0 Gb/s
* TRIM: Supported
* Sector size: 512 bytes
* SMART: Supported and enabled

## Network

* Wi-Fi interface: `wlp2s0b1`
* Wired interface: `enp1s0f0`
* Wired status: Unused — no cable connected (`NO-CARRIER`)

## Verification

### System / Model

Get general system and model information:

```bash
sudo dmidecode -t system
```

Get the Mac model identifier:

```bash
sudo dmidecode -s system-product-name
```

### CPU

Get detailed CPU information:

```bash
lscpu
```

### Memory

Quick memory summary:

```bash
free -h
```

Detailed memory information, including type, speed, and slot information:

```bash
sudo dmidecode -t memory
```

### Storage

List storage devices:

```bash
lsblk -o NAME,SIZE,MODEL,TYPE
```

Install `smartmontools` if required:

```bash
sudo apt install smartmontools
```

Get SSD/device information:

```bash
sudo smartctl -i /dev/sda
```

Check SMART health status:

```bash
sudo smartctl -H /dev/sda
```

Get complete SMART information:

```bash
sudo smartctl -a /dev/sda
```

### Network

List network interfaces, IP addresses, and MAC addresses:

```bash
ip a
```

If NetworkManager is installed:

```bash
nmcli device show
```

Show routing information:

```bash
ip route
```

## Known Quirks

* Wi-Fi is the primary network connection.
* Wired Ethernet is currently unused.


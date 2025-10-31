# WireShark-Portfolio

# Objectives
The goal is to follow the [Wireshark labs](https://gaia.cs.umass.edu/kurose_ross/wireshark.php) lead by Kuross and Ross to:
- install WireShark on Debian Linux

## Installation

### 1. Update your system
```bash
sudo apt update && sudo apt upgrade -y
```
---

### Install Wireshark

Use this command to
- install the latest stable Wireshark package from the official Debian repositories
- and install all recommended dependencies.
```bash
sudo apt install wireshark -y
```



---

### Allow non-root users to capture packets

During installation you’ll be asked:

```
Should non-superusers be able to capture packets? [Yes/No]
```

**Select `Yes`** (or run the command below later).

If you missed it or want to change it later:

```bash
sudo dpkg-reconfigure wireshark-common
```

Choose **Yes** → this adds your user to the `wireshark` group.

Then add yourself (replace `youruser`):

```bash
sudo usermod -aG wireshark youruser
```

Log out and back in (or reboot) for the group change to take effect.
---

### Verify the installation

Run this command in the Terminal to open wireshark:

```bash
wireshark --version
Wireshark 4.4.7.
```
---

### Open Wireshark
```bash
sudo wireshark &
```

> - wireshark: Starts the Wireshark GUI program.
> - &: This tells the shell to run the command in the background so you can use the same terminal session right after opening Wireshark.
---

# What I Learned

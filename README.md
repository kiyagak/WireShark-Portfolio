# WireShark-Portfolio

This portfolio shows my use of Wireshark to complete [Kuross and Ross' Wireshark labs](https://gaia.cs.umass.edu/kurose_ross/wireshark.php).  I will use Wireshark to capture traffic from protocols such as
- [HTTP](https://github.com/kiyagak/WireShark-Portfolio/blob/main/HTTP.md)
- [DNS](https://github.com/kiyagak/WireShark-Portfolio/blob/main/DNS.md)
- [TCP](https://github.com/kiyagak/WireShark-Portfolio/blob/main/TCP.md)
- [UDP](https://github.com/kiyagak/WireShark-Portfolio/blob/main/UDP.md)
- [IP](https://github.com/kiyagak/WireShark-Portfolio/blob/main/IP.md)
- [NAT](https://github.com/kiyagak/WireShark-Portfolio/blob/main/NAT.md)
- DHCP
- ICMP
- Ethernet and ARP
- 802.11 WiFi
- 5G
- and TLS

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
```

    Wireshark 4.4.7.

---

### Open Wireshark
```bash
sudo wireshark &
```

> - **wireshark**: Starts the Wireshark GUI program.
> - **&**: This tells the shell to run the command in the background so you can use the same terminal session right after opening Wireshark.

<img width="894" height="716" alt="image" src="https://github.com/user-attachments/assets/1f2deb53-2827-46de-be3c-433a4718f838" />

---

# What I Learned

I learned how to 
- install Wireshark on Debian Linux
- open Wireshark as a root or admin user so that I can capture the specific network interface I want

+++
title = "Connect to headless Raspberry with SSH and Wireshark"
date = "2024-07-25"
description = "Connect to headless Raspberry with SSH and Wireshark"
tags = [
    "software",
    "en",
]
+++

---

In many situations one would like to connect to their Raspberry Pi without the needing a keyboard, mouse and a screen. Most people don't have them just lying around. Then the only option is to connect your laptop and Raspberry with an Ethernet cable, either directly or via a router, and then use [SSH](https://en.wikipedia.org/wiki/Secure_Shell) to remotely login into the Raspberry. This latter option is called "headless" connection, because of the lack of a GUI.

Unfortunately, many tutorials just didn't work for me. They might suggest you connect with `ssh pi@raspberrypi.local` like [here](https://www.tomshardware.com/reviews/raspberry-pi-headless-setup-how-to,6028.html), which just doesn't resolve to the Raspberry's IP address and leaves the terminal hanging. Other tutorials might tell you a very convoluted way to find the IP address of the Raspberry by downloading a special program. Moreover, even if you find the IP of the Raspberry, it might be IPv6 and then no one tells you how to use it. The vast majority juts care about IPv4.

This is my method of connecting with SSH to a Raspberry, that works both on Windows and on Linux (I don't have a Mac but I presume it should work since it works for Linux). Firstly, we have to find the IP address that is given to the Raspberry by your computer's DHCP server when you plug the Ethernet cable in your computer. We use a [packet sniffer](https://en.wikipedia.org/wiki/Packet_analyzer) for that, namely Wireshark. Then, we run Wireshark and find the Raspberry's IP address which we then use for the `ssh` command.

### Linux
1. Open a new terminal. Download and install Wireshark with the following command:
```
sudo apt install wireshark
```
2. Run Wireshark as root:
```
sudo wireshark
```
3. Wireshark's GUI opens up. Select your Ethernet interface by double-clicking on it. In general it is called `eth0`:
![img](/static/images/sshrpi/selectinterface.png)
4. If the Ethernet from the Raspberry is already plugged in then a stream of packets should appear on the screen. Otherwise, plug in the Ethernet cable in your computer.
5. Look at the stream of packets. One packet of the MDNS protocol should contain a reference to `raspberrypi.local`, like the highlighted one in the screenshot below. The source of that packet is the Raspberry, and we can see its IPv6 or IPv4 address.
![img](/static/images/sshrpi/sniff.png)
6. In my case, I can see only IPv6 addresses (I don't know why. If somebody knows then please let me know). That is no problem. Copy the IPv6 address.
7. Open a new terminal and use SSH to connect to the Raspberry. The format of the command for IPv4 is:
```
ssh [user]@[IPv4]
```
- For IPv6 the format is:
```
ssh -6 [user]@[IPv6]%[interface]
```
- In my case the username for the Raspberry is the default one, `pi`. The interface is `eth0`, the one that we are sniffing with Wireshark. Thus, my command is:
```
ssh -6 pi@fe80::3db:cf7f:5ee8:4d5b%eth0
```
8. You should be prompted to enter the password, which by default is `raspberry`. Then, you should be in!
![img](/static/images/sshrpi/connect.png)
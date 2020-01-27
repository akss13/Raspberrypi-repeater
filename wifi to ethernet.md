Raspberry pi-ethernet to wifi repeater
======
For configuring the system I assume you have installed raspian os with desktop and enabled ssh and/or vnc.

First update date and time:
```bash
date
```
**Ouptput:**
```bash
Sun 26 Jan 18:43:19 IST 2020
```
Set date and time accordingly:
```bash
sudo date -s "Sun 26 Jan 18:43:19 IST 2020"
```
Update and upgrade system:
```bash
sudo apt update && sudo apt full-upgrade -y 
```
Install dnsmasq.
```bash
sudo apt-get install dnsmasq -y
```
**Assign eth0 an IP address.** Open:
```bash
sudo nano /etc/dhcpcd.conf 
```
Add to the bottom of the file:
```bash
interface eth0
static ip_address=192.168.4.1/24
```
**Save and exit.**

Save original **dnsmasq.conf**
```bash
sudo mv /etc/dnsmasq.conf /etc/dnsmasq.conf.orig
```
Open **/etc/dnsmasq.conf**
```bash
sudo nano /etc/dnsmasq.conf
```
Add to the bottom of the file:
```bash
interface=eth0
dhcp-range=192.168.4.8,192.168.4.250,255.255.255.0,12h
```
Save and exit.

Edit **/etc/sysctl.conf**
```bash
sudo nano /etc/sysctl.conf
```
Uncomment
```bash
net.ipv4.ip_forward=1
```
Save and exit.

Open /etc/rc.local
```bash
sudo nano /etc/rc.local
```
Add just above **exit 0**
```bash
iptables -t nat -A  POSTROUTING -o wlan0 -j MASQUERADE
```
Save and exit.

Reboot system
```bash
sudo reboot now
```
### Connect router via ethernet port of Raspberry pi.

Raspberry pi-inbuilt wifi ap with internet connected on ehternet port.
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
Install dnsmasq and hostapd.
```bash
sudo apt install dnsmasq hostapd bridge-utils -y
```
Since the configuration files are not ready yet, turn the new software off as follows:
```bash
sudo systemctl stop dnsmasq
sudo systemctl stop hostapd
```
To configure the static IP address, edit the dhcpcd configuration file with:
```bash
sudo nano /etc/dhcpcd.conf
```
Go to the end of the file and add:
```bash
interface wlan0
static ip_address=192.168.0.10/24
nohook wpa_supplicant
denyinterfaces eth0
denyinterfaces wlan0
```
Save and exit.

Configuring the DHCP server (dnsmasq):
```bash
sudo mv /etc/dnsmasq.conf /etc/dnsmasq.conf.orig
sudo nano /etc/dnsmasq.conf
```
Add to the bottom of the file:
```bash
interface=wlan0
  dhcp-range=192.168.0.11,192.168.0.30,255.255.255.0,24h
```
Save and exit.

Configuring the access point host software (hostapd):
```bash
sudo nano /etc/hostapd/hostapd.conf
```
Add to the file. Change the **ssid** and **wpa_passphrase** of your choice
```bash
interface=wlan0
bridge=br0
hw_mode=g
channel=7
wmm_enabled=0
macaddr_acl=0
auth_algs=1
ignore_broadcast_ssid=0
wpa=2
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP
rsn_pairwise=CCMP
ssid=NETWORK
wpa_passphrase=PASSWORD
```
Save and exit.
We now need to tell the system where to find this configuration file.
```bash
sudo nano /etc/default/hostapd
```
Uncomment **#DAEMON_CONF** and add the following:
```bash
DAEMON_CONF="/etc/hostapd/hostapd.conf"
```
Save and exit.


Edit **/etc/sysctl.conf**
```bash
sudo nano /etc/sysctl.conf
```
Uncomment this line **#net.ipv4.ip_forward=1**
```bash
net.ipv4.ip_forward=1
```
Save and exit.

Add a new iptables rule:
```bash
sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
```
Save the new iptables rule:
```bash
sudo sh -c "iptables-save > /etc/iptables.ipv4.nat"
```
Edit the file **/etc/rc.local**:
```bash
sudo nano /etc/rc.local 
```
Add just above **exit 0**:
```bash
iptables-restore < /etc/iptables.ipv4.nat
```
Save and exit.

Setup the bridge for internet connection:
```bash
sudo brctl addbr br0
sudo brctl addif br0 eth0
```
Edit the interfaces file:
```bash
sudo nano /etc/network/interfaces
```
Add the following lines at the end of the file:
```bash
auto br0
iface br0 inet manual
bridge_ports eth0 wlan0
```
Save and exit.

Now enable and start hostapd:
```bash
sudo systemctl unmask hostapd
sudo systemctl enable hostapd
sudo systemctl start hostapd
```
Reboot:
```bash
sudo reboot now
```


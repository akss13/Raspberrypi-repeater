Raspberry pi-inbuilt wifi AP with external wifi dongle.
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
Install usb wifi dongle driver:
```bash
curl -O https://raw.githubusercontent.com/akss13/Raspberrypi-repeater/master/wifi_dongle_driver.sh
sudo mv wifi_dongle_driver.sh /usr/bin/install-wifi
sudo chmod +x /usr/bin/install-wifi
sudo install-wifi
```
Check **wlan0** and **wlan1** both show up using **ifconfig**

Install dnsmasq and hostapd.
```bash
sudo apt install dnsmasq hostapd -y
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
    static ip_address=192.168.4.1/24
    nohook wpa_supplicant
```
Save and exit.

Restart the dhcpcd daemon:
```bash
sudo service dhcpcd restart
```
Configuring the DHCP server (dnsmasq):
```bash
sudo mv /etc/dnsmasq.conf /etc/dnsmasq.conf.orig
sudo nano /etc/dnsmasq.conf
```
Add to the bottom of the file:
```bash
interface=wlan0
dhcp-range=192.168.4.2,192.168.4.20,255.255.255.0,24h
```
Save and exit.

Start dnsmasq
```bash
sudo systemctl start dnsmasq
```
Configuring the access point host software (hostapd):
```bash
sudo nano /etc/hostapd/hostapd.conf
```
Add to the file. Change the **ssid** and **wpa_passphrase** of your choice
```bash
interface=wlan0
driver=nl80211
ssid=NameOfNetwork
hw_mode=g
channel=7
wmm_enabled=0
macaddr_acl=0
auth_algs=1
ignore_broadcast_ssid=0
wpa=2
wpa_passphrase=AardvarkBadgerHedgehog
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP
rsn_pairwise=CCMP
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

Now enable and start hostapd:
```bash
sudo systemctl unmask hostapd
sudo systemctl enable hostapd
sudo systemctl start hostapd
```
Edit **/etc/sysctl.conf**
```bash
sudo nano /etc/sysctl.conf
```
Uncomment this line **#net.ipv4.ip_forward=1**
```bash
net.ipv4.ip_forward=1
```
Save and exit.

Edit **/etc/rc.local**
```bash
sudo nano /etc/rc.local 
```
Add just above **exit 0**
```bash
iptables -t nat -A  POSTROUTING -o wlan1 -j MASQUERADE
```
Save and exit.

Reboot:
```bash
sudo reboot now
```


Raspberrypi-repeater
======
## Instructions to convert Raspberrypi to repeater
**To connect Raspberrypi to internet using internal wifi and attach a router for repeating the signal or use ehternet directly with your pc:**

[Instructions for pi wifi to ethernet](https://github.com/akss13/Raspberrypi-repeater/blob/master/pi%20to%20ehternet%20repeater.md)

**To create Raspberrypi AP and use external wifi dongle for connecting to wifi:**

[Instructions for pi AP to external dongle to wifi](https://github.com/akss13/Raspberrypi-repeater/blob/master/pi%20inbuilt%20wifi%20with%20external%20wifi%20repeater.md)

[Use the script for installing driver for your wifi dongle](https://github.com/akss13/Raspberrypi-repeater/blob/master/wifi_dongle_driver.sh)
```bash
curl -O https://raw.githubusercontent.com/akss13/Raspberrypi-repeater/master/wifi_dongle_driver.sh
sudo mv wifi_dongle_driver.sh /usr/bin/install-wifi
sudo chmod +x /usr/bin/install-wifi
sudo install-wifi
```

The above provided instructions have been tested with the following:

1. raspberry pi 3b+
2. 2019-09-26-raspbian-buster.img
3. TP-LINK TL-WN823N usb dongle

To manually add a wifi, go to **/etc/wpa_supplicant/wpa_supplicant.conf** file and add the following:
```bash
network={
	ssid="ssid"
	psk="password"
	key_mgmt=WPA-PSK
}
```
Change the **ssid** and **psk** as per your wifi and save and exit.

To repeat a **eap/phase 2 authentication wifi**, add the following in the **/etc/wpa_supplicant/wpa_supplicant.conf**
```bash
network={
	ssid="SRM HOSTELS"
	scan_ssid=1
	key_mgmt=WPA-EAP
	eap=PEAP
	identity="an5179"
	password="Welcome@123"
	phase1="peaplabel=auto peapver=auto"
	phase2="auth=MSCHAPV2"
}
```
Change the **ssid**, **psk** and **identity** as per your wifi and save and exit.

**Note:** Repeating a **eap/phase 2 authentication wifi** wifi is illegal. Please do so at your own risk.
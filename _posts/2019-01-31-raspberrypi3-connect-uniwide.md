---
layout: post
title: "Raspberry Pi 3+ Connect to Uniwide (wifi)"
date: 2019-01-31
---

## Raspbery Pi 3+ connect to wifi Uniwide UNSW
---

Firstly, was trying to connect to eduroam and failed, ocallsionally saw our uni's [document](https://www.it.unsw.edu.au/students/uniwide/UniWide_Ubuntu.pdf), and adjust the configure as bellowing, it works and connected to uniwide.

```
network={
ssid="uniwide"
scan_ssid=1
key_mgmt=WPA-EAP
eap=PEAP
#phase1="peaplabel=0"
phase2="auth=MSCHAPV2"
identity="zxx@uxx.edu.au"
password="xxx12345"
}

```

---

Install the wpa support tool for the raspberry pi, most likely rasperberry pi 3+ comes with that (Rasbian):

    sudo apt-get install wpasupplicant

This above configure should be edited in:

    sudo gedit /etc/wpa_supplicant/wpa_supplicant.conf
    
Now edit /etc/network/interfaces with

    sudo gedit /etc/network/interfaces

adding:
    
    wpa-driver wext
    wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
    
The original documents use[1]

    sudo /etc/init.d/networking restart
    
to reboot the network service, howerver, I failed, so I just reboot.


[1] https://ubuntuforums.org/showthread.php?t=249654

[2] https://www.raspberrypi.org/forums/viewtopic.php?t=86253

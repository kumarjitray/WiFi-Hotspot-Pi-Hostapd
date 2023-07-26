# Wifi-Hotspot-Pi-Hostapd
This is for Raspberry Pi 3B+ model with Raspios 32bit image. The procedure will be almost same for all models. 
## Use Raspberry Pi as an Access Point (AP) by enabling WiFi Hotspot using Hostapd.

## Follow the following instruction--->>>

1.     $sudo apt update

2.     $sudo apt -y upgrade

3.     $sudo apt install -y hostapd dnsmasq

4.     $sudo systemctl unmask hostapd

5.     $sudo systemctl enable hostapd

6.     $sudo DEBIAN_FRONTEND=noninteractive apt install -y netfilter-persistent iptables-persistent

7.     $sudo reboot

### This will be the IP of your AP's Wlan0 port.
1.     $sudo nano /etc/dhcpcd.conf
```
    interface wlan0
    static ip_address=192.168.4.1/24
    nohook wpa_supplicant
    
```
2.     $sudo nano /etc/sysctl.d/routed-ap.conf
```
    # Enable IPv4 routing
    net.ipv4.ip_forward=1
```

### Everything in this file is commented out by default, so you can insert the following lines anywhere…beginning, end, or delete everything there and replace with just these lines.
1.     $sudo nano /etc/dnsmasq.conf
```
    interface=wlan0 # Listening interface
    dhcp-range=192.168.4.2,192.168.4.20,255.255.255.0,24h
    # Pool of IP addresses served via DHCP
    domain=wlan
    # Local wireless DNS domain
    address=/gw.wlan/192.168.4.1
    # Alias for this router
```

2.     $sudo nano /etc/hostapd/hostapd.conf
```
    country_code=IN
    interface=wlan0
    ssid=WiFiHotspotName
    hw_mode=a
    channel=48
    macaddr_acl=0
    auth_algs=1
    ignore_broadcast_ssid=0
    wpa=2
    wpa_passphrase=WifiPassword
    wpa_key_mgmt=WPA-PSK
    wpa_pairwise=TKIP
    rsn_pairwise=CCMP
```
3.     $sudo reboot

####
WiFiHotspotName is the name (or “SSID”) one sees in the WiFi network selection menu on your computer or phone; replace with something descriptive.

WifiPassword is the corresponding password for the above.

country_code=US must be edited with the 2-letter country code for your location; the default here is for the United States. 5 GHz WiFi will not work correctly without it! 

The lines hw_mode=a and channel=48 (36, 40, 44, 48) probably will need to be edited…

hw_mode can be a, b or g, corresponding to different 802.11 WiFi generations. 802.11a is 5 GHz (Pi 3 B+ onwards), the others are 2.4 GHz; for USB WiFi dongles, try g.

channel will depend on hw_mode and your country. If it’s a 2.4 GHz mode (b or g), this is 1–11 (and up to 14 in some places) (List of 2.4 GHz channels). If 5 GHz (hw_mode=a only), the range is much larger and a bit confusing (List of 5 GHz channels — look for ones with a “Yes” in your country/region’s column). 48 worked for me in the US, but you might need to try a few different channels before one sticks, rebooting each time.

If using a non-Adafruit USB WiFi adapter, you might need to add a line: driver=nl80211 or similar, depends on the WiFi chip used within. Check documentation for the item, also there’s no guarantee all such dongles can work with the Pi.

``` sudo service hostapd status -l ```

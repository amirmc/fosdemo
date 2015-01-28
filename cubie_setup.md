Throughout this document I'll refer to the wireless interface as `wlan1`; on a CubieBoard, it's likely that the wireless dongle will be `wlan0` instead.

* install hostapd (for wireless access point) and dnsmasq (for dhcp server): 

```
sudo apt-get install hostapd dnsmasq
```

* apt will start dnsmasq right after it's installed.  You don't want it to be running yet as it's not properly configured, so kill it:

```
sudo kill `pidof dnsmasq`
```

* create the file `/etc/hostapd.conf` and add these lines to it:

```
interface=wlan1
ssid=cubietruck-test
channel=11
```

* initialise the wireless usb dongle:

```
sudo ip link set wlan1 up
```

* start hostapd:

```
sudo hostapd /etc/hostapd.conf &
```

Output should look something like this (the important part being "AP-ENABLED"):
```
Configuration file: /etc/hostapd.conf
Using interface wlan1 with hwaddr c4:04:15:7a:98:a4 and ssid "cubietruck-test"
VLAN: vlan_set_name_type: SET_VLAN_NAME_TYPE_CMD name_type=2 failed: Package not installed
wlan1: interface state UNINITIALIZED->ENABLED
wlan1: AP-ENABLED 
```


* create a new network bridge:

```
sudo brctl addbr br1
```

* add the wireless interface to the new network bridge:

```
sudo brctl addif br1 wlan1
```

* set up a static network on the new bridge.  I chose a 192.168 network that's unlikely to collide with other networks in use, but you may wish to use something else (particularly if you happen to be using a network with the same addressing to configure the CubieBoard!); if so, you'll need to change it here and in the step below where you configure `dnsmasq.conf`.

```
sudo ifconfig br1 192.168.252.1 netmask 255.255.255.0
```

* pick a MAC address for your unikernel.  In this example, I'll use the default mirage autoconfigured one, `c0:ff:ee:c0:ff:ee`.

* configure `dnsmasq`, the DHCP server, by adding the following lines to `/etc/dnsmasq.conf`:

```
interface=br1
dhcp-range=192.168.252.2,192.168.252.250,30m
dhcp-host=c0:ff:ee:c0:ff:ee,192.168.252.2
```

This will give each client a 30 minute lease.  If you need more IPs, you can reconfigure the bridge to have a larger network and expand the dhcp range accordingly.  A unikernel with a vif having mac address c0:ff:ee:c0:ff:ee will always get the address 192.168.252.2 .  (Note that if you instead wish to have your unikernel be statically configured, you don't need that last line, although leaving it in won't hurt anything.)

* start `dnsmasq`

```
sudo dnsmasq
```

* at this point you should be able to connect to the wifi with your phone, although there won't be anything to browse to.

* compile your unikernel:

```
NET=direct DHCP=please mirage configure --xen
make
```

* add this line to the generated config file (probably `www.xl`):

```
vif = [ 'mac=c0:ff:ee:c0:ff:ee,bridge=br1' ]
```

* try it out!  

```
sudo xl create www.xl -c
```

Now you'll (hopefully) see your unikernel come up and get a DHCP address.  You should then be able to load up a fun game!  Yay!

Optionally:

* allow users to navigate to http://2048 by adding this line to `/etc/hosts`:

```
192.168.252.2	2048
```


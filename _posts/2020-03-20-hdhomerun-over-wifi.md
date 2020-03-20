---
layout: post
title: How to Use an HDHomeRun over Wi-Fi
mathjax: no
categories: [tech]
tags: [raspberrypi, hdhomerun, networking, hobby, project]
---

I have an [HDHomeRun](https://www.silicondust.com/), which is a TV tuner that lets you connect an over-the-air antenna to your home network and stream TV from it. I use it with [Plex](https://www.plex.tv/) as a DVR for free broadcast TV.

But there's one problem: the HDHomeRun doesn't have Wi-Fi, so it needs to be connected to your router via ethernet. That means that with standard 6ft long ethernet and coax cables, the TV antenna can be at most 12 feet away from the router.

![Typical HDHomeRun setup](https://f001.backblazeb2.com/file/www-iantaylor-xyz/posts/hdhomerun-over-wifi/typical-hdhr-setup.png)

But for me, the ideal place for my router (a central closet in my apartment) isn't close to the ideal place for my TV antenna (near a south-facing window). It's also not really feasible for me to run a really long coax cable or a really long ethernet cable across my apartment. Instead, I want to use a [Raspberry Pi](https://www.raspberrypi.org/) as a wireless bridge between the HDHomeRun and the router. After all, I have a couple of those laying around.

![Ideal HDHomeRun setup](https://f001.backblazeb2.com/file/www-iantaylor-xyz/posts/hdhomerun-over-wifi/ideal-hdhr-setup.png) 

Since at the moment I'm hunkered down in the house, I have the time to tackle these kinds of projects. This is a guide to how I managed to create this setup. This is aimed at people with some Linux or Raspberry Pi familiarity already. I may write a more detailed version later, which will be a separate post. (If *you* would like a more detailed version, let me know! My contact information is in the page footer.)

## What you'll need

* HDHomeRun (I have the Extend, but I think any model would work)
* Raspberry Pi with ethernet and Wi-Fi (I used the Raspberry Pi 3B which has both built in, but a different Pi with USB ethernet or USB Wi-Fi adapters should also work)
  - Relevant accessories: power supply, SD card with Raspbian, ethernet cable, HDMI cable and USB keyboard for initial setup, USB Wi-Fi/ethernet adapters if necessary
* Existing Wi-Fi network you want to connect to (SSID and passphrase)
* The ability to change settings on your router admin page
* Wi-Fi network details: subnet and mask, DCHP range, gateway IP address, nameserver IP address
* HDHomeRun MAC address (usually located on label underneath)

## Instructions

The end result, network-wise, is two subnets joined by the Raspberry Pi as shown below.

![network diagram](https://f001.backblazeb2.com/file/www-iantaylor-xyz/posts/hdhomerun-over-wifi/network-diagram.png)

We'll create this in three steps. First, set up the Raspberry Pi and connect it to your desired Wi-Fi network.

### Step 1: static IP addresses

Give the Wi-Fi and ethernet interfaces static IP addresses. Edit `/etc/dhcpcd.conf` to add the following:
```
# Static config for wifi - to main LAN
interface wlan0
static ip_address=192.168.0.222/24
static routers=192.168.0.1
static domain_name_servers=192.168.0.1

# Static config for ethernet - to secondary LAN
interface eth0
static ip_address=192.168.2.1/24
static routers=192.168.2.0
```
Here my main network addresses are in the `192.168.0.x` block. I chose the address `192.168.0.222` for my Raspberry Pi, which is outside of my main router’s DHCP range. My router and DNS server are both at `192.168.0.1`. I chose `192.168.2.x` for the secondary network I’m creating behind the Pi’s ethernet port.

### Step 2: DHCP server

Next, install `dnsmasq` to be a DHCP server for the HDHomeRun.
```
sudo apt-get install dnsmasq
```
Move the default `/etc/dnsmasq.conf` to `/etc/dnsmasq.conf.orig` and create a new `/etc/dnsmasq.conf` with the following:
```
# Where to listen
interface=eth0
listen-address=192.168.2.1
# Note: some tutorials include this line, but I find it breaks things
#bind-interfaces

# DNS options for clients
server=192.168.0.1
domain-needed
bogus-priv

# DHCP options for clients
dhcp-range=192.168.2.10,192.168.2.250,12h

# HDHomeRun static IP address
dhcp-host=<your HDHomeRun MAC address>,192.168.2.90
```
The `listen-address` should be the same as the static address you created previously. `server=` should be a DNS server, but doesn’t need to be the same as the DNS server from before. The HDHomeRun's static IP address can be anything in the secondary network space (`192.168.2.x`). Make sure to replace your HDHomeRun's MAC address, removing the angle brackets also. That can usually be found on the label underneath the device.

### Step 3: routing

Now, enable routing on the Raspberry Pi. Edit the file `/etc/sysctl.conf` and uncomment the following line:
```
net.ipv4.ip_forward=1
```
Then reload the file by running `sudo sysctl -p`.

The Raspberry Pi’s routing table should be sufficient already, but we need to add a route on the main LAN’s router telling it how to reach devices on the secondary LAN. **This will be done differently depending on your specific router’s admin interface.** If your router is running Linux, an equivalent Linux command would be `sudo ip route add 192.168.2.0/24 via 192.168.0.222`. In other words, we tell the router that to reach any `192.168.2.x` address it needs to go through the Raspberry Pi. Here is what this looks like on my TP-Link router:

[![TP-Link Archer C1200 static route settings](https://f001.backblazeb2.com/file/www-iantaylor-xyz/posts/hdhomerun-over-wifi/tplink-route-add.png)](https://f001.backblazeb2.com/file/www-iantaylor-xyz/posts/hdhomerun-over-wifi/tplink-route-add.png)

Once this is in place, reboot the Raspberry Pi. The HDHomeRun should be accessible at the static IP address you chose earlier (in my case, `192.168.2.90`).

## Other Notes

1. The HDHomeRun is accessible directly through that IP address, but won't be "discovered" automatically by some apps. That's because broadcast packets don't traverse the network boundary. 
2. Plex does this weird thing where it will add your device as a DVR without any problem and stream TV just fine, but in the settings page it will tell you the device could not be found. I think this is also related to broadcast packets.
3. Other tutorials for how to bridge the Wi-Fi and ethernet on a Raspberry Pi, such as [this one](https://pimylifeup.com/raspberry-pi-wifi-bridge/) or [this one](https://www.raspberrypi.org/forums/viewtopic.php?t=132674) both of which I adapted from, set up NAT on the Raspberry Pi. This works as long as the device you plug into the ethernet port is a client-only machine, like a laptop. But an HDHomeRun runs a server and listens for requests from other computers. So you would need to additionally forward ports from the Pi to the HDHomeRun using `iptables`. I could never find a definitive list of the ports an HDHomeRun uses, and firewalls give me a headache, so I decided not to pursue this method.
4. My router is a TP-Link Archer C1200. There's a bug in the Advanced Routing menu where [static routes you add don't actually work](https://serverfault.com/questions/998272/tp-link-archer-c1200-not-adding-static-route-to-routing-table). I ended up using *another* Raspberry Pi as my default router on which I could actually add the correct routes. This shouldn't be necessary with most routers.

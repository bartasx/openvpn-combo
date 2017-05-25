# *OpenVPN*, *Transmission*, Socks in FreeNAS Jail 

Here's how to configure a FreeNAS 9.10 jail to include openvpn, transmission and *3proxy* (a socks server).

Why this solution? I want to use my VPN for some, but not all internet traffic from my FreeNAS server. In particular, I want to use *Transmission* for bittorrent traffic and to have a SOCKS5 server that I can use for browsing.

Putting *OpenVPN*, *Transmission*, and *3proxy* in a single jail works well, but there are some complications caused by how *Transmission* and *3proxy* configuration files specify the VPN endpoint: they both use the IP address of the VPN interface. 

So when *OpenVPN* creates a tunnel, a new IP address will be assigned to the interface (*tun0*). This IP address needs to be edited into the configuration files for both *Transmission* and *3proxy*. And the services need to be restarted.

Sometimes a VPN tunnel may fail, causing the device (*tun0*) to disappear, and *OpenVPN* needs to be restarted. And when that happens, a new IP address will typically be assigned to the tunnel device, thus requiring *Transmissino* and *3proxy* to be updated and restarted.

The *transvpnmon* service in this package solves these problems by:

* Monitoring the health of the VPN tunnel
* Start or restart the openvpn service as needed
* Check the tunnel IP address against the *Transmission* and *3proxy* configurations.
* Update, start, or restart the *Transmission* service as necessary
* Update, start, or restart the *3proxy* service as necessary

## Overview
[TBD]

## Install and Configure *OpenVPN*

From a shell in the jail, install the package:
```sh
/#pkg install -y openvpn
```
Create a directory where the ovpn file will be mapped in the host. This is where you will put the openvpn settings for your VPN:
```
/#mkdir /openvpn
```
Edit the */etc/rc.conf* file to include: 
```
openvpn_enable="YES"
openvpn_configfile="/openvpn/default.ovpn"
openvpn_flags="--script-security 2"
```
The `openvpn_enable="YES"` setting enables the openvpn service for starting and stopping.

The `openvpn_configfile="/openvpn/default.ovpn"` setting
identifies a file, *default.ovpn* in the directory you created above. Later you will create a storage mapping for the jail for this directory so you can drop your own version of *default.ovpn* in the host's filesystem.

The `openvpn_flags="--script-security 2"` setting allows the client code to execute scripts.

Create a jail storage mapping  for the `/openvpn` target directory to some directory in your host. This is where you will put your own version of `default.ovpn`

When the OpenVPN service is started, it will create a device, *tun0*, and give it an IP address. This IP address is what needs to be configured for both the *Transmission* and *3proxy* services, but more about that later.

## Install and Configure *Transmission*

From a shell in the jail, install the packages:
```sh
/#pkg install -y transmission-daemon transmission-cli transmission-web
```

The *Transmission* program uses different directories for downloading, etc.; you will want to define and map these appropriately on your system. You can add these to the `/etc/rc.conf` file:

* `transmission_conf_dir="/transmission/config"`
* `transmission_download_dir="/transmission/downloads"`
* `transmission_watch_dir="/transmission/watched"`

You might also want to add these to `/etc/rc.conf`:
* `transmission_user="media"`
* `transmission_flags="--logfile /transmission/config/transmission.log"`



**Important Note:** Transmission rewrites its configuration file, *settings.json* when it exists. You must stop the service before you edit any of the settings outside its web interface. 
## Install and Configure *3proxy*
[TBD]
## Install and Configure *transvpnmon*
[TBD]


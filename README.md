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
## Install and Configure *OpenVPN*
## Install and Configure *Transmission*
## Install and Configure *3proxy*
## Install and Configure *transvpnmon*


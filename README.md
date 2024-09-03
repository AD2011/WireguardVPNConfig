# Wireguard VPN Client Server Setup
Config for setup of personal VPN based on Wireguard Protocol with Dual Stack Support (IPv4 and IPv6)

Refer [Sample Server Config.conf](https://github.com/AD2011/WireguardVPNConfig/blob/main/Sample%20Server%20Config.conf) for instructions on server.

Refer [Sample Client Config.conf](https://github.com/AD2011/WireguardVPNConfig/blob/main/Sample%20Client%20Config.conf) for instructions on client.

Refer [CloudflareWarp-Wireguard.conf](https://github.com/AD2011/WireguardVPNConfig/blob/main/CloudflareWarp-Wireguard.conf) for creating Warp Wireguard Configuration. 

## Cloudflare Warp Endpoints:
Deafult [Anycast]: `engage.cloudflareclient.com`
> May route to Europe due to peering issues between Cloudflare and Airtel/Jio

India: `162.159.195.8` or `162.159.193.4`
> May route to nearest default Server [Changes with each city]

Endpoint Ports: `All UDP`

Default: `2408`

Alternatives: `500, 1701, 4500`

### Additional Commands
```
nano /etc/sysctl.conf			                            # Enable IPV4 and IPV6 forwarding
systemctl enable wg-quick@wg0	                        # Set wireguard to start on boot
ip a							                                    # Find Interface Name
wg genkey | tee privatekey | wg pubkey > publickey		# Generate Private and Public Keys
cd /etc/wireguard				                              # Default Wireguard Directory
nano wg0.conf					                                # Default WG Interface Name
wg-quick up wg0
wg-quick down wg0
wg show
wg set wg0 peer <Client Public Key>= allowed-ips <Client Unique IP Address(eg: 192.168.69.3/32, fd42:42:42::3/128)>
sample:
wg set wg0 peer cVU13uIpVWxCPE40uRB9ItoUQSq1rRuSuYncYWawViI= allowed-ips 192.168.69.3/32, fd42:42:42::3/128
```

#### To open all ports at startup (only for Oracle Cloud)

Auto open port https://www.youtube.com/watch?v=fYQBvjYQ63U
```
systemctl enable iptables-service.conf
nano iptables-startup.sh

#!/bin/sh

iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -F

ip6tables -P INPUT ACCEPT
ip6tables -P OUTPUT ACCEPT
ip6tables -P FORWARD ACCEPT
ip6tables -F
```

## Make iptables-startup.sh executable and run on startup
```
chmod +x iptables-startup.sh
cd /etc/systemd/system
nano iptables-startup.service

[Unit]
Description=iptables startup

[Service]
ExecStart=/home/ubuntu/iptables-startup.sh

[Install]
WantedBy=multi-user.target

sudo systemctl start iptables-startup
sudo systemctl enable iptables-startup
sudo systemctl status iptables-startup
```

## Alternate way to enable persistant iptables all acecept on Oracle Cloud  [Only recommend if you have good firewall rules on the Oracle Cloud Console]
```
sudo su
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -F

ip6tables -P INPUT ACCEPT
ip6tables -P OUTPUT ACCEPT
ip6tables -P FORWARD ACCEPT
ip6tables -F
```
There are versions of the saved iptables stored at /etc/iptables/rules.v4 and /etc/iptables/rules.v6 (Debian/Ubuntu). 

For Saving to those files for persistance across reboots:

`sudo sh -c '/sbin/iptables-save > /etc/iptables/rules.v4'`

`sudo sh -c '/sbin/iptables6-save > /etc/iptables/rules.v6'`

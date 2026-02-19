
# 1. DHCP

## Installation de dnsmasq sur proxmox

```
apt install dnsmasq
```

Modification du fichier /etc/dnsmasq.conf

```
interface=vmbr0
dhcp-range=x.x.x.x,x.x.x.x,12h
```

Redémarrage de dnsmasq

```
systemctl restart dnsmasq
```


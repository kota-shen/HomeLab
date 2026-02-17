# 29-01-2026

## Installation de proxmox sur une Debian 12

### modification du fichier hosts


```
127.0.0.1       localhost
x.x.x.x    srv00.homelab.local     srv00

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

La commande suivante nous permet de vérifier que le changement a bien été pris en compte

```
hostname --ip-address
X.X.X.X # should return at least one non-loopback IP address here
```

### Installation de Proxmox

#### Adaptation du fichier sources.list

```
echo "deb [arch=amd64] [http://download.proxmox.com/debian/pve](http://download.proxmox.com/debian/pve) bookworm pve-no-subscription" > /etc/apt/sources.list.d/pve-install-repo.list
```

Ajout de la clé gpg du dépôt de Proxmox VE

```
wget https://enterprise.proxmox.com/debian/proxmox-release-bookworm.gpg -O /etc/apt/trusted.gpg.d/proxmox-release-bookworm.gpg

```

mise à jour du dépôt et du système

```
apt update && apt full-upgrade
```

#### Installation du noyau Proxmox VE

```
apt install proxmox-default-kernel
```

Installation des packet de Proxmox

```
apt install proxmox-ve postfix open-iscsi chrony
```

Suppression du noyau Debian (pour éviter les conflits lors de mise à jour de Proxmox)

```
apt remove linux-image-amd64 'linux-image-6.1*'
```

Mise à jour du bootloader Grub

```
update-grub
```

Suppression du packet os-prober

```
apt remove os-prober
```

Redémarrage du system

```
systemctl reboot
```

## Gestion réseau virtuel

Création d'un vmbr0 qui agira comme un NAT, wlpXs0 étant la connection internet de l'hôte et enpXs0 étant l'interface éthernet

Dans le shell proxmox:

nano /etc/network/interfaces

```
iface enp4s0 inet static
        address x.x.x.x/24

auto vmbr0
iface vmbr0 inet static
    address x.x.x.x/24
    bridge-ports enpxs0
    bridge-stp off
    bridge-fd 0
```

## Activation du forwarding IP

```
echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf
sysctl -p
```

## Activation du NAT (iptables)

```
iptables -t nat -A POSTROUTING -s x.x.x.x/24 -o wlpxs0 -j MASQUERADE
iptables -A FORWARD -i enp3s0 -o wlpxs0 -j ACCEPT
iptables -A FORWARD -i wlp5s0 -o enpxs0 -m state --state RELATED,ESTABLISHED -j ACCEPT

```

```
nano /etc/dnsmasq.d/lan.conf

# Interface LAN (bridge ou ethernet)
interface=vmbr0
bind-interfaces

# Plage DHCP + bail de 12h
dhcp-range=192.168.1.50,192.168.1.150,12h

# Gateway (host Proxmox)
dhcp-option=3,192.168.1.1

# DNS (host + fallback)
dhcp-option=6,192.168.1.1,1.1.1.1

```

```
systemctl restart dnsmasq
systemctl status dnsmasq
```

## NAT vers Wi-FI (rappel)

```
iptables -t nat -A POSTROUTING -s x.x.x.x/24 -o wlpXs0 -j MASQUERADE
```

# Interface Wi-Fi (WAN)

```
allow-hotplug wlpXs0
iface wlpXs0 inet dhcp
	wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
	
# Bridge LAN pour VM
auto vmbr0
iface vmbr0 inet static
	address 192.168.1.1/24
	bridge-ports enp4s0
	bridge-stp off
	bridge-fd 0
# Interface Wi-Fi (WAN)
allow-hotplug wlpXs0
iface wlpXs0 inet dhcp
	wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
	
# Bridge LAN pour VM
auto vmbr0
iface vmbr0 inet static
	address 192.168.1.1/24
	bridge-ports enp4s0
	bridge-stp off
	bridge-fd 0
```

### Activer le forwarding IP 

```
nano /etc/sysctl.d/99-forward.conf
```

```
net.ipv4.ip_forward=1
```

et Appliquer

```
sysctl --system
```

NAT persistant avec nftables

```
apt install nftables
systemctl enable nftables
```

```
nano /etc/nftables.conf
```

```
#!/usr/sbin/nft -f

flush ruleset

table inet filter {
    chain forward {
        type filter hook forward priority 0;
        policy drop;

        iifname "vmbr0" oifname "wlpXs0" accept
        iifname "wlp5s0" oifname "vmbr0" ct state related,established accept
    }
}

table ip nat {
    chain postrouting {
        type nat hook postrouting priority 100;
        oifname "wlp5s0" ip saddr 10.0.0.1/24 masquerade
    }
}

```

Activer :

```
systemctl restart nftables
nft list ruleset
```

Installation de dnsmasq qui agira comme serveur DHCP et DNS local (à installer sur l'hôte proxmox)

```
apt update
apt install dnsmasq
```

```
nano /etc/dnsmasq.d/vmbr0.conf
```

```
interface=vmbr0
bind-interfaces

dhcp-range=X.X.X.2,X.X.X.100,24h

dhcp-option=3,X.X.X.1
dhcp-option=6,X.X.X.1,1.1.1.1

```

```
systemctl restart dnsmasq
systemctl enable dnsmasq
```


<img src="Images/Proxmox_network.png">

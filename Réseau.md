
# Bridge NAT

Création d'un bridge linux servant de NAT pour permettre aux VM de se connecter à internet

```
auto vmbr0
iface vmbr0 inet static
    address x.x.x.x
    netmask 255.255.255.0
    bridge-ports none
    bridge-stp off
    bridge-fd 0
```

Redémarrage du réseau

```
systemctl restart networking
```

Activer le NAT avec iptables

```
# Autoriser le NAT depuis le bridge vers le Wi-Fi
iptables -t nat -A POSTROUTING -s x.x.x.x/24 -o wlpXs0 -j MASQUERADE

# Autoriser le forwarding
iptables -A FORWARD -i vmbrX -o wlpXs0 -j ACCEPT
iptables -A FORWARD -i wlpXs0 -o vmbrX -m state --state RELATED,ESTABLISHED -j ACCEPT

```

Pour la persistence 

```
apt install iptables-persistent
netfilter-persistent save
```

Activer le forwarding d'ip

```
echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf
sysctl -p
```
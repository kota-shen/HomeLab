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


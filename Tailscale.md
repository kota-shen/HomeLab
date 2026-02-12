
Mise en place de tailscale pour accès à distance de manière sécurisée au noeud proxmox


## Ajout du dépot Tailscale et installation

```
curl -fsSL https://tailscale.com/install.sh | sh
```

## Activation sur le noeud

```
tailscale up
```

-> Cette commande lance tailscale et affiche une url afin d'ajouter le noeud au réseau Tailscale

## Vérifications du fonctionnement

```
tailscale ip
tailscale status
```



# 🏠 Homelab – Documentation d’Infrastructure

## 1. Introduction


Création et mise en oeuvre d'un homelab orienté support IT/Helpdesk/Cybersécurité

### 1.1 Objectif du Homelab

Permettre la mise en pratique et l'apprentissage dans divers domaine tel que les systèmes, le développement (python principalement), le réseau, la cybersécurité (offensive, défensive et GRC/Compliance)

### 1.2 Contexte et motivations

Etant en cours d'étude afin de devenir formateur accrédité CISCO ainsi que pour pouvoir passer diverses certifications en cybersécurité, je souhaite en apprendre d'avantage.

### 1.3 Périmètre actuel

Ce homelab se fera sur une machine basée sur 1 I9 12900k avec 64 Go RAM embarquant proxmox par dessus une debian.

A l'heure actuelle ce qui est déployé

- Debian 12
- Proxmox
- VM clientes (Windows/Linux/Mac/android)
- Services (DHCP, DNS, NAS)

A venir :

- infrastructure réseau Cisco (eve-ng)
- Domain Controller
- glpi

## 2. Mise en oeuvre

### 2.1 Diagramme infrastructure


![[Diagramme infra.18-02-2026.png]]
Diagramme au 17/02/2026

![[Diagramme infra.19-02-2026.png]]
Diagramme au 18/02/2026

![[Diagramme infra.20-02-2026.png]]
Diagramme au 19/02/2026

### 2.2 Proxmox

Installation de [[Proxmox]]

### 2.3 Réseau

Configuration [[Réseau]]

### 2.4 Services

Configurations des  [[Services]]

### 2.5 Machines virtuelles

Installation d'une machine [[Windows]]
Installation d'une machine [[Ubuntu]]
Installation d'une machine [[Fedora]]
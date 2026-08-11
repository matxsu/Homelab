# Homelab — Galaxy

Repo chapeau, public : documente l'architecture globale. Le code (Terraform, manifests K8s, futur Ansible) vit dans [`galaxy`](https://github.com/matxsu/galaxy), privé.

> **Statut : premier jet.** Ce README est reconstruit en observant l'état réel des repos (commits, manifests, Terraform), pas à partir d'un plan figé — pas mal de choses ont changé depuis les anciennes notes. Tout ce qui est marqué ❓ vient de notes antérieures potentiellement obsolètes et doit être confirmé ou corrigé.

## Ce qui tourne aujourd'hui (vérifié dans les repos)

### Kuat — Raspberry Pi 5, 16GB — `192.168.3.30`
Control-plane + worker K3s, nœud unique pour l'instant.

| Namespace | Service | Codename | Image |
|---|---|---|---|
| gateway | Traefik, AdGuard, Cloudflared | — | — |
| iam | Authentik + PostgreSQL + Redis | Obroa-skai | — |
| vault | HashiCorp Vault | — | — |
| media | Jellyfin | Scarif | — |
| media | Jellyseerr | Jedha | — |
| download | Gluetun + NordVPN | Kessel | — |
| download | qBittorrent | Ord Mantell | — |
| download | Prowlarr | Umbara | — |
| download | Sonarr | Lothal | — |
| download | Radarr | Corellia | — |
| download | Bazarr | Atollon | — |
| download | (rotation VPN via RBAC) | Ziost | — |
| ops | Homarr (dashboard) | Alderaan | `homarr-labs/homarr` |
| ops | Gitea | Kashyyyk | `gitea/gitea` |
| ops | Kavita | Yavin | `jvmilazz0/kavita` |
| ops | AudiobookShelf | Christophsis | `advplyr/audiobookshelf` |
| ops | Vaultwarden (password manager) | Serenno | `vaultwarden/server` |

### Proxmox — nœud `corulag`
IaC encore minimal : une seule VM définie dans `galaxy/terraform/proxmox/`.

| VM | Rôle | IP | Specs |
|---|---|---|---|
| endor | Pelican Panel + Wings (game hosting) | `192.168.3.45/24` | 4 vCPU, 10 Go RAM, 80 Go disque |

Réseau observé : gateway `192.168.3.1`, DNS interne `192.168.3.55` (+ 1.1.1.1 en secours), domaine interne `.galaxy`.

## Outils annexes
- **rackula** — planification visuelle de rack (self-hosted, `ghcr.io/rackulalives/rackula`). Tourne actuellement en Docker Compose à la racine (`~/rackula`), port 8080, **hors de `Projects/`**. Pas encore de layout réel enregistré (juste le rack de démo "Racky McRackface"). Intégration dans `Projects/` prévue plus tard, à froid (arrêt propre du conteneur avant de bouger `data/`).
- **Fichiers matériel/dashboard reçus** (rangés dans `hardware/` et `assets/` de ce repo) : 3 designs d'impression 3D pour montage rack 10" (2 racks HDD hot-swap + 1 support Raspberry Pi/JetKVM), icônes Jellyfin/Pelican probablement destinées au dashboard Homarr (Alderaan).

## Matériel réel (confirmé le 2026-08-12 — remplace les anciennes notes)

Les notes Obsidian d'avril 2026 parlaient de "3× Lenovo M920q" — c'est obsolète. Voici le matériel réel :

### Compute
| Machine | CPU | RAM | Stockage | Codename Proxmox |
|---|---|---|---|---|
| Raspberry Pi 5 | — | 16 Go | slot NVMe + carte SD | Kuat (confirmé) |
| Dell OptiPlex 3000 Micro | i5-12400T | 2×8 Go (16 Go) | 1 slot SSD SATA + 1 slot SSD NVMe | ❓ |
| HP EliteDesk 800 G5 Micro | i5-9500T | 2×8 Go (16 Go) | 1 slot SSD SATA + 1 slot SSD NVMe | ❓ |

→ 2 mini PC, pas 3 — un futur cluster Proxmox HA sur ces deux machines n'aurait pas de quorum majoritaire automatique à 2 nœuds (il en faut 3, ou un qdevice tiers). À garder en tête si HA est l'objectif.

### Réseau
- Switch Unifi 8 ports PoE
- Unifi Cloud Gateway Fiber
- AP Unifi WiFi 7

### Rack
Rack imprimé en 3D — 3 slots HDD SATA + 5 slots SSD SATA. Adaptateurs/câbles/connecteurs pas encore achetés (en projet) — cohérent avec les designs d'impression dans `hardware/rack-mounts/`.

## ❓ Encore à confirmer avec toi

- [ ] `corulag` (nom de nœud dans le Terraform `endor`) correspond à quelle des deux machines — l'OptiPlex ou l'EliteDesk ?
- [ ] Talos Linux toujours dans les plans, ou vous restez sur K3s classique vu qu'il n'y a que 2 nœuds compute ?
- [ ] Découpage VLAN actuel — les notes mentionnaient 7 VLANs mais je n'ai trouvé aucune trace exploitable dans les repos.
- [ ] Kuat (Pi 5) reste le nœud K3s principal à terme, ou bascule vers un rôle bastion/DNS/backup une fois les mini PC en service ?

## Structure de ce repo
```
hardware/      — fiches matériel, designs d'impression 3D (racks, supports)
assets/        — icônes et ressources pour dashboards (Homarr, etc.)
```

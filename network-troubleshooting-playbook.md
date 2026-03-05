# Network Troubleshooting Playbook (Lab)

## Objectif
Avoir une méthode simple et reproductible pour diagnostiquer les pannes réseau type support N1/N2 (DNS/DHCP/gateway/ports/partages/VPN).

## Contexte Lab
- VMs : Windows Server, Windows Client, Ubuntu Server
- Réseau : NAT (internet) + Host-only (LAN)
- Convention : documenter chaque panne avec **tests**, **résultats**, **fix**, **preuve** (captures/commandes)

---

## Réflexe N1/N2 : l’ordre de diagnostic (80/20)
1. **IP OK ?** (adresse, masque, DHCP)
2. **Gateway OK ?** (route par défaut, ping gateway)
3. **DNS OK ?** (résolution noms)
4. **Chemin OK ?** (traceroute)
5. **Ports OK ?** (SMB/RDP/SSH/HTTP…)
6. **Blocage ?** (pare-feu, proxy, VPN)

---

## Commandes essentielles

### Windows
- `ipconfig /all` : IP/masque/gateway/DNS/DHCP
- `ping <cible>` : connectivité
- `tracert <cible>` : chemin
- `nslookup <nom>` : DNS
- `route print` : routes
- PowerShell :
  - `Test-NetConnection <host> -Port <port>` : test de port
  - `Get-NetIPConfiguration` : vue rapide réseau

### Linux
- `ip a` : interfaces/IP
- `ip r` : routes
- `ping <cible>`
- `traceroute <cible>`
- `dig <nom>` / `nslookup <nom>`
- `ss -tulpn` : ports/services
- `journalctl -u <service>` : logs service

---

## Checklist “Connectivité” (à copier dans un ticket)
### Windows (ordre conseillé)
1. `ipconfig /all`
2. `ping 127.0.0.1`
3. `ping <IP_locale>`
4. `ping <gateway>`
5. `ping 8.8.8.8` (internet sans DNS)
6. `nslookup google.com` (DNS)
7. `tracert 8.8.8.8`

### Interprétation rapide
- OK jusqu’à gateway, KO après → route/firewall/ISP/VPN
- Ping IP OK mais noms KO → DNS
- IP = 169.254.x.x → DHCP KO
- Intermittent → Wi-Fi / saturation / DHCP / câble

---

## Ports indispensables (support)
- 53 : DNS
- 67/68 : DHCP
- 80/443 : Web
- 22 : SSH
- 3389 : RDP
- 445 : SMB (partages)
- 389 : LDAP (AD) (si AD)
- 88 : Kerberos (si AD)

---

# 10 Pannes Simulées (avec preuves)

> Pour chaque panne : colle au moins 1 capture + 1 sortie de commande dans la section “Preuves”.

---

## Panne 1 — DNS client mal configuré
### Symptômes
- Ping IP internet OK (8.8.8.8), mais web / noms KO

### Hypothèses
- DNS invalide configuré sur le client

### Reproduction (lab)
- Sur le client, mets un DNS faux (ex: 1.2.3.4) au lieu du DNS attendu

### Tests
- `ping 8.8.8.8` → OK
- `nslookup google.com` → échec / timeout

### Fix
- Remettre DNS correct (serveur DNS du lab / DHCP)

### Preuves
- (colle capture nslookup avant/après)
- (colle ipconfig /all avant/après)

---

## Panne 2 — Service DNS arrêté côté serveur (si DNS interne)
### Symptômes
- Noms internes (ex: server/lab.local) ne se résolvent plus

### Reproduction
- Stopper le service DNS sur Windows Server

### Tests
- `nslookup server` → échec
- `ping <IP_server>` → OK (si IP connue)

### Fix
- Redémarrer le service DNS

### Preuves
- (capture service arrêté/redémarré)
- (sortie nslookup)

---

## Panne 3 — DHCP arrêté (client en APIPA)
### Symptômes
- IP client = 169.254.x.x
- Pas d’accès au LAN / internet

### Reproduction
- Stopper le service DHCP ou désactiver DHCP sur le serveur (si utilisé)

### Tests
- `ipconfig /all` → APIPA
- `ipconfig /renew` → échec

### Fix
- Relancer service DHCP + renouveler bail

### Preuves
- (capture ipconfig APIPA)
- (capture renew OK après fix)

---

## Panne 4 — Scope DHCP épuisé
### Symptômes
- Certains clients n’obtiennent plus d’IP
- IP auto / pas de bail

### Reproduction
- Réduire le pool DHCP à 2 IP disponibles, puis connecter plusieurs clients

### Tests
- Côté client : `ipconfig /all` (pas de bail)
- Côté serveur : scope “full” / plus d’adresses

### Fix
- Agrandir la plage DHCP / ajuster exclusions

### Preuves
- (capture console DHCP scope)
- (capture ipconfig)

---

## Panne 5 — Gateway incorrecte
### Symptômes
- LAN OK mais internet KO

### Reproduction
- Mettre une mauvaise passerelle sur le client

### Tests
- `ping <gateway>` → KO
- `tracert 8.8.8.8` → bloque au 1er saut

### Fix
- Remettre la gateway correcte (ou DHCP)

### Preuves
- (tracert avant/après)

---

## Panne 6 — Conflit IP
### Symptômes
- Coupures / comportement instable
- Message conflit IP (parfois)

### Reproduction
- Mettre la même IP statique sur 2 machines du LAN

### Tests
- `ping` instable
- Vérif des configs IP des deux machines

### Fix
- Rendre une IP unique / repasser en DHCP

### Preuves
- (captures des 2 configs IP)

---

## Panne 7 — DNS “domaine” mal réglé (cas AD-like)
### Symptômes
- Accès ressources internes (nom machine / domaine) KO
- GPO / login domaine peuvent échouer (si AD en phase 2)

### Reproduction
- Mettre DNS = 8.8.8.8 au lieu du DNS interne

### Tests
- `nslookup lab.local` → KO
- `ping server` → KO mais `ping <IP_server>` → OK

### Fix
- Pointer vers le DNS interne

### Preuves
- (nslookup avant/après)

---

## Panne 8 — Ping OK mais web KO (DNS vs Proxy)
### Symptômes
- `ping 8.8.8.8` OK
- navigateur ne charge rien

### Reproduction
- Variante A : casser DNS (comme panne 1)
- Variante B : configurer un proxy invalide (si tu sais)

### Tests
- DNS : `nslookup` KO
- Proxy : DNS OK, mais navigateur KO → vérifier proxy

### Fix
- DNS correct / proxy désactivé ou corrigé

### Preuves
- (test nslookup + capture réglage proxy si utilisé)

---

## Panne 9 — Partage SMB inaccessible (port 445 / résolution)
### Symptômes
- `\\server\share` ne s’ouvre pas

### Reproduction
- Variante A : casser DNS (nom server ne résout plus)
- Variante B : bloquer le port 445 (pare-feu)

### Tests
- `Test-NetConnection server -Port 445`
- Accès via IP : `\\<IP_server>\share` (différencier DNS vs port)

### Fix
- Corriger DNS ou pare-feu (autoriser 445) selon cause

### Preuves
- (Test-NetConnection avant/après)

---

## Panne 10 — Latence / lenteur réseau (approche support)
### Symptômes
- “C’est lent”, timeouts intermittents

### Reproduction (lab - options)
- Saturer la VM (CPU/RAM) ou faire un gros download
- Limiter ressources VM

### Tests
- `ping <gateway>` avec temps élevé / pertes
- `tracert <cible>`
- Observer CPU/RAM/Disk pendant incident

### Hypothèses (exemples)
- saturation CPU/RAM
- perte Wi-Fi / lien instable
- résolution DNS lente
- congestion/route

### Fix (selon cause)
- Stabiliser ressources, redémarrer service, changer lien, etc.

### Preuves
- (ping avec latence)
- (capture perf/ressources)

---

## Notes & amélioration continue
- Ajoute un paragraphe “Leçon apprise” après chaque panne
- Ajoute une section “Prévention” (ex: standard DNS, monitoring DHCP, etc.)

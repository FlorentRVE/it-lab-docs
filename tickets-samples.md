# Tickets Samples (Support N1/N2) — Lab

> Format entreprise : Symptômes → Impact → Hypothèses → Tests → Résolution → Prévention → Preuves

---

## Ticket 01 — Web inaccessible sur le poste (DNS suspect)
**Utilisateur / équipe :** User-Lab (poste WIN-CLIENT)  
**Impact :** Dégradé (navigation web impossible)  
**Symptômes :** Le navigateur n’ouvre aucun site, message “DNS_PROBE…”  
**Contexte :** Problème apparu après modification réseau (suspect)

**Hypothèses :**
- H1 : DNS client mal configuré
- H2 : DNS serveur indisponible

**Tests & résultats :**
1. `ping 8.8.8.8` → OK  
2. `nslookup google.com` → timeout/échec  
3. `ipconfig /all` → DNS = 1.2.3.4 (invalide)

**Résolution :**
- Remise DNS correct via DHCP / configuration manuelle vers DNS lab
- Vidage cache DNS si besoin : `ipconfig /flushdns`

**Prévention / doc :**
- Standardiser DNS via DHCP (éviter config manuelle)
- Ajouter check DNS à la checklist

**Preuves :**
- (colle sortie `ipconfig /all` + `nslookup` avant/après)

---

## Ticket 02 — Client obtient une IP 169.254.x.x (DHCP KO)
**Utilisateur / équipe :** User-Lab  
**Impact :** Bloquant (pas de réseau)  
**Symptômes :** “Connecté” mais pas d’accès LAN/internet, IP = 169.254.x.x  
**Contexte :** Après redémarrage VM

**Hypothèses :**
- H1 : Service DHCP arrêté
- H2 : Scope DHCP épuisé

**Tests & résultats :**
1. `ipconfig /all` → IP 169.254.x.x, pas de bail  
2. `ipconfig /renew` → échec  
3. Côté serveur : service DHCP arrêté / scope full (selon scénario)

**Résolution :**
- Redémarrage service DHCP OU extension du scope
- Renouvellement du bail sur le client

**Prévention / doc :**
- Mettre une alerte “pool DHCP faible” (plus tard)
- Documenter taille de scope

**Preuves :**
- (capture ipconfig + console DHCP)

---

## Ticket 03 — LAN OK, internet KO (gateway incorrecte)
**Utilisateur / équipe :** User-Lab  
**Impact :** Dégradé (accès LAN ok, internet KO)  
**Symptômes :** Ping serveur LAN OK, ping internet KO  
**Contexte :** Changement IP statique récent

**Hypothèses :**
- H1 : passerelle mal configurée
- H2 : route par défaut absente

**Tests & résultats :**
1. `ping <IP_server>` → OK  
2. `ping 8.8.8.8` → KO  
3. `ipconfig /all` → gateway = 192.168.56.254 (incorrect)

**Résolution :**
- Correction gateway (valeur attendue du LAN)
- Validation : ping 8.8.8.8 OK

**Prévention / doc :**
- Favoriser DHCP pour éviter erreurs manuelles
- Ajouter “gateway check” dans checklist

**Preuves :**
- (ipconfig avant/après + ping)

---

## Ticket 04 — Accès \\server\share KO (résolution de nom)
**Utilisateur / équipe :** User-Lab  
**Impact :** Dégradé (accès partage KO)  
**Symptômes :** `\\server\commun` introuvable, mais IP du serveur connue  
**Contexte :** Aucun changement côté serveur

**Hypothèses :**
- H1 : DNS ne résout pas “server”
- H2 : NetBIOS/WINS absent (si utilisé)

**Tests & résultats :**
1. `ping server` → KO  
2. `ping <IP_server>` → OK  
3. `nslookup server` → échec

**Résolution :**
- Correction DNS client vers DNS interne
- Retest accès `\\server\commun` OK

**Prévention / doc :**
- Éviter d’utiliser des noms non enregistrés DNS
- Documenter DNS interne requis

**Preuves :**
- (nslookup + ping avant/après)

---

## Ticket 05 — Accès SMB KO (port 445 bloqué)
**Utilisateur / équipe :** User-Lab  
**Impact :** Dégradé (partages KO)  
**Symptômes :** `\\server\RH` timeout  
**Contexte :** Pare-feu modifié récemment

**Hypothèses :**
- H1 : port 445 bloqué sur serveur
- H2 : service SMB indisponible

**Tests & résultats :**
1. `Test-NetConnection server -Port 445` → Failed  
2. `ping server` → OK (connectivité IP OK)

**Résolution :**
- Réactiver règle pare-feu “File and Printer Sharing (SMB-In)”
- Retest port 445 OK, partage accessible

**Prévention / doc :**
- Standard ruleset pare-feu pour serveur de fichiers
- Ajout test port dans playbook

**Preuves :**
- (Test-NetConnection avant/après + capture firewall)

---

## Ticket 06 — RDP vers serveur impossible (3389)
**Utilisateur / équipe :** User-Lab  
**Impact :** Dégradé (administration distante KO)  
**Symptômes :** “Remote Desktop can’t connect”  
**Contexte :** Après modification réseau

**Hypothèses :**
- H1 : port 3389 bloqué
- H2 : serveur non joignable
- H3 : service RDP désactivé

**Tests & résultats :**
1. `ping server` → OK  
2. `Test-NetConnection server -Port 3389` → Failed  
3. Vérif serveur : règle pare-feu RDP désactivée

**Résolution :**
- Activer règle RDP + vérifier Remote Desktop enabled
- Retest : port OK, RDP OK

**Prévention / doc :**
- GPO/standard config pour management
- Checklist “ports admin”

**Preuves :**
- (Test-NetConnection + capture settings)

---

## Ticket 07 — SSH vers Ubuntu impossible (22)
**Utilisateur / équipe :** User-Lab  
**Impact :** Dégradé  
**Symptômes :** `ssh ubuntu@<ip>` timeout/refused  
**Contexte :** VM Ubuntu redémarrée

**Hypothèses :**
- H1 : service sshd arrêté
- H2 : port 22 bloqué
- H3 : IP Ubuntu a changé

**Tests & résultats :**
1. `ping <IP_ubuntu>` → OK  
2. `Test-NetConnection <IP_ubuntu> -Port 22` → Failed  
3. Côté Ubuntu (console) : `systemctl status ssh` → inactive

**Résolution :**
- `sudo systemctl start ssh` (+ enable si besoin)
- Retest : SSH OK

**Prévention / doc :**
- Activer `systemctl enable ssh`
- Ajouter check service dans monitoring (phase 3)

**Preuves :**
- (status ssh + test port avant/après)

---

## Ticket 08 — Conflit IP sur le LAN
**Utilisateur / équipe :** User-Lab  
**Impact :** Dégradé (instabilité)  
**Symptômes :** coupures, ping instable  
**Contexte :** IP statique configurée sur 2 VMs

**Hypothèses :**
- H1 : conflit IP
- H2 : saturation ressources

**Tests & résultats :**
1. Vérif IP sur VM A et VM B → même IP  
2. Ping vers gateway → pertes / variations

**Résolution :**
- Remettre une VM en DHCP / changer IP unique
- Retest : ping stable

**Prévention / doc :**
- Réserver IPs statiques (liste) / utiliser DHCP reservations

**Preuves :**
- (captures ipconfig des deux VMs)

---

## Ticket 09 — DNS interne KO mais internet OK
**Utilisateur / équipe :** User-Lab  
**Impact :** Dégradé (ressources internes KO)  
**Symptômes :** `\\server\commun` KO, mais web OK  
**Contexte :** DNS client changé

**Hypothèses :**
- H1 : DNS du client pointe vers public (8.8.8.8)
- H2 : enregistrement DNS interne manquant

**Tests & résultats :**
1. `ipconfig /all` → DNS = 8.8.8.8  
2. `nslookup server` → NXDOMAIN  
3. `nslookup google.com` → OK

**Résolution :**
- Remettre DNS vers DNS interne
- Retest `nslookup server` OK

**Prévention / doc :**
- Standardiser DNS via DHCP
- Documenter “DNS interne obligatoire pour ressources internes”

**Preuves :**
- (ipconfig + nslookup avant/après)

---

## Ticket 10 — “C’est lent” (latence) — analyse structurée
**Utilisateur / équipe :** User-Lab  
**Impact :** Dégradé  
**Symptômes :** chargements lents, timeouts intermittents  
**Contexte :** pendant forte charge VM

**Hypothèses :**
- H1 : saturation CPU/RAM sur client/serveur
- H2 : congestion réseau / lien instable
- H3 : résolution DNS lente

**Tests & résultats :**
1. `ping <gateway>` → latence élevée / pertes (si reproduit)  
2. `tracert 8.8.8.8` → ralentissement dès 1er saut (ou plus loin)  
3. Observations ressources : CPU/RAM proches max (gestionnaire tâches)

**Résolution :**
- Réduire charge (arrêt process), ajuster ressources VM
- Retest : latence redevenue normale

**Prévention / doc :**
- Baseline ressources et seuils
- Documenter procédure “incident lenteur”

**Preuves :**
- (ping avec temps + capture ressources)

---

## Ticket 11 — VPN “connecté” mais intranet KO (DNS/route)
**Utilisateur / équipe :** User-Lab  
**Impact :** Dégradé (accès ressources internes KO)  
**Symptômes :** VPN se connecte, mais pas d’accès aux noms internes  
**Contexte :** scénario support

**Hypothèses :**
- H1 : DNS VPN non appliqué
- H2 : routes split-tunnel manquantes

**Tests & résultats :**
1. `ipconfig /all` → DNS n’a pas changé  
2. `nslookup intranet.local` → échec  
3. `route print` → pas de route vers réseau interne (scénario)

**Résolution :**
- Appliquer DNS attendu via profil VPN (ou config)
- Ajouter route (si scénario) / corriger split tunnel

**Prévention / doc :**
- Checklist VPN : DNS + routes + creds
- Template de diagnostic VPN

**Preuves :**
- (ipconfig + route print)

---

## Ticket 12 — Impossible d’accéder à un site HTTPS spécifique
**Utilisateur / équipe :** User-Lab  
**Impact :** Dégradé  
**Symptômes :** un site en https ne charge pas, d’autres OK  
**Contexte :** scénario

**Hypothèses :**
- H1 : blocage firewall/inspection TLS
- H2 : DNS renvoie une mauvaise IP
- H3 : problème certificat (rare en lab)

**Tests & résultats :**
1. `nslookup site.com` → IP A  
2. Test autre DNS : `nslookup site.com 8.8.8.8` → IP B (diff)  
3. `Test-NetConnection site.com -Port 443` → OK/KO selon scénario

**Résolution :**
- Corriger DNS / vider cache / ajuster filtre réseau

**Prévention / doc :**
- Procédure “site unique KO”

**Preuves :**
- (nslookup comparatif + test port)

---

## Ticket 13 — Service web interne indisponible (port 80/443)
**Utilisateur / équipe :** User-Lab  
**Impact :** Dégradé  
**Symptômes :** page interne ne charge plus  
**Contexte :** service redémarré récemment

**Hypothèses :**
- H1 : service arrêté sur serveur
- H2 : port bloqué pare-feu

**Tests & résultats :**
1. `ping serveur` → OK  
2. `Test-NetConnection serveur -Port 80` → Failed  
3. Sur serveur : service web arrêté (scénario)

**Résolution :**
- Redémarrer service + vérifier écoute port
- Retest OK

**Prévention / doc :**
- Monitoring service + alerte
- Runbook “restart service web”

**Preuves :**
- (test port + status service)

---

## Ticket 14 — Impossible d’imprimer (scénario support)
**Utilisateur / équipe :** User-Lab  
**Impact :** Dégradé  
**Symptômes :** impression en file d’attente puis erreur  
**Contexte :** “après redémarrage”

**Hypothèses :**
- H1 : spooler arrêté
- H2 : imprimante hors ligne
- H3 : pilote/port

**Tests & résultats :**
1. Vérifier service Spooler → arrêté (scénario)  
2. Redémarrer spooler → amélioration

**Résolution :**
- Restart spooler + purge file d’attente si besoin

**Prévention / doc :**
- Ajouter spooler au monitoring basique (phase 3)

**Preuves :**
- (capture service spooler + résultat)

---

## Ticket 15 — Wi-Fi instable (méthode + checklist)
**Utilisateur / équipe :** User-Lab  
**Impact :** Dégradé (intermittent)  
**Symptômes :** pertes réseau aléatoires, visioconf coupe  
**Contexte :** surtout à certaines heures

**Hypothèses :**
- H1 : signal faible/interférences
- H2 : saturation AP
- H3 : DHCP renew / roaming

**Tests & résultats :**
1. Ping gateway en continu → pertes observées  
2. Test câble/4G → stable  
3. Vérifier puissance signal / canal (si possible)

**Résolution :**
- Recommandation : se rapprocher AP, changer bande 5GHz, câble si critique
- Escalade réseau si récurrent

**Prévention / doc :**
- Checklist Wi-Fi (signal, canal, charge, drivers)
- Standard “poste critique = ethernet”

**Preuves :**
- (logs ping + notes tests)

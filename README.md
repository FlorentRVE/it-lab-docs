
# Portfolio — IT Lab (Support / Sys / Réseau)

## Profil (résumé rapide)
Objectif : Support / Sys / Réseau junior → preuves concrètes (labs, docs, scripts).  
Lab : Windows Server + Windows Client + Ubuntu Server, réseau NAT + Host-only.

---

## Sommaire
- [0. Lab setup](#0-lab-setup)
- [1. Phase 1 — Réseau + Support](#1-phase-1--réseau--support)
- [2. Phase 2 — Windows + AD](#2-phase-2--windows--ad)
- [3. Phase 3 — Employabilité](#3-phase-3--employabilité)
- [4. Phase 4 — Automatisation + Linux](#4-phase-4--automatisation--linux)
- [5. Phase 5 — Cloud](#5-phase-5--cloud)
- [6. Phase 6 — DevOps + Capstone](#6-phase-6--devops--capstone)

---

# 0. Lab setup

## Status
- [x] ✅ Hyperviseur installé (VirtualBox/VMware)
- [x] ✅ VS Code + Git installés
- [x] ✅ GitHub configuré (SSH key / login)
- [x] ✅ 3 VMs bootables (Server / Client / Ubuntu)
- [x] ✅ Réseau NAT + Host-only OK
- [x] ✅ Ping client → server sur LAN interne
- [x] ✅ SSH vers Ubuntu
- [x] ✅ Premier commit/push vers GitHub

---

# 1. Phase 1 — Réseau + Support

## Objectif
Bases réseau + diagnostic N1/N2 + tickets “entreprise”.

## Livrables
- [ ] ⏳ Playbook réseau : [`network-troubleshooting-playbook.md`](./network-troubleshooting-playbook.md)
- [ ] ⏳ Tickets (15) : [`tickets-samples.md`](./tickets-samples.md)

## Validation
- [ ] Je peux expliquer DNS en 2 minutes (symptômes + tests + fix)
- [ ] Je peux expliquer DHCP en 2 minutes (APIPA + scope + renew)
- [ ] J’applique une méthode (symptômes → hypothèses → tests → fix)
- [ ] J’ai 10 pannes simulées avec preuves

## Preuves
- Panne 1 (DNS client) : `./proofs/phase1/panne1/`
- Panne 2 (DNS service) : `./proofs/phase1/panne2/`
- Panne 3 (DHCP KO) : `./proofs/phase1/panne3/`
- Panne 4 (Scope full) : `./proofs/phase1/panne4/`
- Panne 5 (Gateway) : `./proofs/phase1/panne5/`
- Panne 6 (Conflit IP) : `./proofs/phase1/panne6/`
- Panne 7 (DNS domaine) : `./proofs/phase1/panne7/`
- Panne 8 (Ping OK web KO) : `./proofs/phase1/panne8/`
- Panne 9 (SMB/445) : `./proofs/phase1/panne9/`
- Panne 10 (Latence) : `./proofs/phase1/panne10/`

---

# 2. Phase 2 — Windows + AD

## Livrables (à venir)
- [ ] ⏳ AD lab setup : `ad-lab-setup.md`
- [ ] ⏳ File shares & permissions : `fileshares-permissions.md`

## Preuves
- `./proofs/phase2/ad/`
- `./proofs/phase2/gpo/`
- `./proofs/phase2/fileshares/`

---

# 3. Phase 3 — Employabilité

## Livrables (à venir)
- [ ] ⏳ Support runbook : `support-runbook.md`
- [ ] ⏳ Monitoring basics : `monitoring-basics.md`

---

# 4. Phase 4 — Automatisation + Linux

## Livrables (à venir)
- [ ] ⏳ Repo PowerShell : `powershell-automation` (README + scripts)
- [ ] ⏳ Linux runbook : (dans `cloud-devops-labs`)

---

# 5. Phase 5 — Cloud

## Livrables (à venir)
- [ ] ⏳ Azure VNet + VM lab
- [ ] ⏳ Identity/Security
- [ ] ⏳ AWS bridge (équivalences)

---

# 6. Phase 6 — DevOps + Capstone

## Livrables (à venir)
- [ ] ⏳ CI/CD GitHub Actions
- [ ] ⏳ K8s basics
- [ ] ⏳ Capstone complet (IaC + app + pipeline + monitoring + runbooks)



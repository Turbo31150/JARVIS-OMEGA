# DEVIS — Mise en conformité facturation électronique

**Prestataire** : Franc Delmas (Turbo) — Développeur IA & Systèmes
**Client** : ATSD APP
**Date** : 3 avril 2026
**Référence** : DEV-2026-04-ATSD-FACTURX
**Validité** : 30 jours

---

## 1. Contexte

La réforme de la facturation électronique impose à compter de septembre 2026 (réception) puis 2027 (émission) la conformité de tous les logiciels de facturation aux normes Factur-X / UBL avec transmission via une Plateforme de Dématérialisation Partenaire (PDP) ou le Portail Public de Facturation (PPF).

Le client dispose d'un SaaS de devis/facturation qui doit être mis en conformité sans impacter l'expérience utilisateur existante.

**Document de référence** : Cahier des charges ATSD APP — "Mise en conformité facturation électronique" (5 pages, 9 sections).

---

## 2. Périmètre de la prestation

### Etape 1 — Génération des factures conformes Factur-X
- Génération automatique du format **Factur-X** (PDF/A-3 + XML CII embarqué)
- Profil **MINIMUM** ou **BASIC** selon les données disponibles dans le SaaS
- Ajout des champs obligatoires : SIREN émetteur/récepteur, numéro TVA intracommunautaire, code devise, mentions légales
- **Aucun changement visuel** pour l'utilisateur final — le PDF reste identique visuellement
- Validation du XML via le validateur officiel Factur-X (FNFE-MPE)

### Etape 2 — Module "Facturation électronique" activable
- Bouton d'activation dans l'interface SaaS ("Activer la facturation électronique")
- Popup explicatif simple (conformité, automatisation)
- Connexion à la PDP choisie via **API OAuth2**
- Stockage sécurisé des tokens d'authentification (chiffrement AES-256)
- Configuration par utilisateur : identifiants PDP, SIREN, préférences

### Etape 3 — Transmission automatique des factures
- Envoi automatique à la PDP lors de la création d'une facture (si module activé)
- Récupération du statut de transmission (accusé de réception PDP)
- Mode dégradé : si PDP indisponible, mise en file d'attente + retry automatique
- Fonctionnement classique (PDF seul) si module désactivé

### Etape 4 — Gestion des statuts
- Affichage du statut de chaque facture dans le SaaS :
  - **Envoyée** (transmise à la PDP)
  - **Reçue** (PDP a confirmé réception)
  - **Acceptée** (destinataire a accepté)
  - **Rejetée** (avec motif)
- Synchronisation via **webhooks PDP** entrants
- Endpoint webhook sécurisé côté SaaS pour recevoir les mises à jour

### Etape 5 — Réception factures fournisseurs (optionnel, inclus)
- Récupération des factures fournisseurs via l'API PDP
- Affichage dans un onglet dédié "Factures fournisseurs"
- Suivi des statuts et échéances de paiement

---

## 3. Architecture technique

### Backend
| Composant | Technologie | Description |
|-----------|-------------|-------------|
| Génération Factur-X | Librairie open-source (factur-x Python/Node) ou API | PDF/A-3 + XML CII conforme |
| Intégration PDP | API REST + OAuth2 | Envoi, statuts, webhooks |
| Webhooks entrants | Endpoint HTTPS sécurisé | Réception statuts PDP |
| Mapping utilisateurs | Table BDD | Liaison compte SaaS ↔ compte PDP |
| File d'attente | Queue (Redis/BDD) | Retry en cas d'indisponibilité PDP |

### Frontend
| Composant | Description |
|-----------|-------------|
| Toggle activation | Bouton on/off par utilisateur |
| Affichage statuts | Badge coloré sur chaque facture |
| Onglet fournisseurs | Liste des factures reçues (optionnel) |
| Indicateur conformité | "Conforme / Connecté" visible |

### APIs exposées
| Endpoint | Méthode | Description |
|----------|---------|-------------|
| `/api/invoices/{id}/send-pdp` | POST | Envoi manuel à la PDP |
| `/api/invoices/{id}/status` | GET | Statut PDP de la facture |
| `/api/webhooks/pdp` | POST | Webhook entrant PDP |
| `/api/e-invoicing/activate` | POST | Activation du module |
| `/api/e-invoicing/config` | PUT | Configuration PDP utilisateur |
| `/api/supplier-invoices` | GET | Liste factures fournisseurs |

---

## 4. Normes et conformité

| Norme | Application |
|-------|-------------|
| **Factur-X v1.0** | Format hybride PDF/A-3 + XML CII (EN 16931) |
| **EN 16931** | Norme européenne sémantique de facturation |
| **PDF/A-3** | Archivage longue durée avec pièces jointes |
| **CII (Cross Industry Invoice)** | Syntaxe XML pour le transport des données |
| **OAuth2** | Authentification sécurisée vers la PDP |
| **HTTPS / TLS 1.3** | Chiffrement des échanges |
| **RGPD** | Pas de stockage de données hors nécessité légale |

---

## 5. Livrables

| # | Livrable | Format |
|---|----------|--------|
| 1 | Module backend Factur-X (génération PDF/A-3 + XML) | Code source intégré au SaaS |
| 2 | Module connexion PDP (OAuth2 + API) | Code source + documentation API |
| 3 | Module transmission automatique + file d'attente | Code source |
| 4 | Module webhooks (réception statuts PDP) | Code source + endpoint |
| 5 | Module frontend (activation, statuts, fournisseurs) | Code source UI |
| 6 | Tests de validation conformité Factur-X | Rapport de tests + factures exemples |
| 7 | Documentation technique d'intégration | PDF + Markdown |
| 8 | Guide utilisateur (activation + utilisation) | PDF illustré |

---

## 6. Planning

| Jour | Tâche | Livrable |
|------|-------|----------|
| J1-J2 | Analyse du code SaaS existant + setup environnement | Audit technique |
| J3-J4 | Module génération Factur-X (PDF/A-3 + XML CII) | Livrable 1 |
| J5-J6 | Module connexion PDP (OAuth2) + transmission auto | Livrables 2, 3 |
| J7-J8 | Webhooks + gestion statuts + frontend | Livrables 4, 5 |
| J9 | Module fournisseurs + tests conformité | Livrables 5, 6 |
| J10 | Documentation + livraison + validation client | Livrables 7, 8 |

**Durée totale : 10 jours ouvrés**

---

## 7. Tarification

| Poste | Montant HT |
|-------|------------|
| Analyse et audit technique du SaaS existant | 75 € |
| Développement module Factur-X (génération) | 150 € |
| Intégration PDP (OAuth2 + API + webhooks) | 200 € |
| Frontend (activation, statuts, fournisseurs) | 125 € |
| Tests de conformité + validation | 100 € |
| Documentation technique + utilisateur | 50 € |
| Support post-livraison (15 jours) | 50 € |
| **TOTAL HT** | **750 €** |
| TVA (20%) | 150 € |
| **TOTAL TTC** | **900 €** |

---

## 8. Conditions

- **Paiement** : 50% à la commande, 50% à la livraison
- **Support** : 15 jours de support correctif inclus post-livraison
- **Garantie** : Conformité aux normes Factur-X v1.0 et EN 16931
- **Propriété** : Le code livré est la propriété exclusive du client
- **Confidentialité** : NDA implicite — aucune donnée client ne sera partagée

---

## 9. Prérequis client

Pour démarrer la prestation, le client doit fournir :

1. **Accès au code source** du SaaS de devis/facturation (repo Git ou archive)
2. **Accès à un environnement de développement** (staging/dev)
3. **Choix de la PDP** ou souhait d'accompagnement sur le choix
4. **Identifiants PDP** de test (sandbox) si disponibles
5. **Exemples de factures** actuelles générées par le SaaS (3-5 exemples PDF)

---

## 10. Engagement qualité

- Validation Factur-X via l'outil officiel FNFE-MPE avant livraison
- Tests d'envoi/réception sur la sandbox PDP
- Factures exemples conformes jointes au livrable
- Code documenté, testé, prêt pour la production
- Architecture légère et scalable conforme au CDC

---

**Pour acceptation**, merci de retourner ce devis signé avec la mention "Bon pour accord" et la date.

Cordialement,

**Franc Delmas**
Développeur IA & Systèmes Distribués
contact : turbo31150@gmail.com
GitHub : github.com/Turbo31150

# Spécifications techniques — Site web Coralline Natation Artistique

**Auteure :** Julie Cardin (bénévolat technique pour le club)
**Statut :** En production
**Dernière mise à jour :** 23 juillet 2026

---

## Vue d'ensemble

Site vitrine + système de gestion de contenu (CMS) pour un organisme à but non lucratif. Architecture **JAMstack** (JavaScript, API, Markup) : site statique généré à la compilation, sans serveur applicatif ni base de données à maintenir.

**Pile technologique (stack) :**

| Rôle | Outil |
|---|---|
| Générateur de site statique | Hugo (thème Ananke) |
| Hébergement + CDN | Netlify |
| Contrôle de version / code source | GitHub |
| Authentification des éditeurs | Netlify Identity (GoTrue) |
| Interface d'édition de contenu (CMS) | Decap CMS |
| Pont d'écriture Git | Netlify Git Gateway |

---

## A. Authentification et gestion des accès

**Objectif :** permettre à une personne non technique (éditrice de contenu) de s'authentifier de façon sécurisée, sans lui donner d'accès aux systèmes sous-jacents (code source, hébergement).

**Outil :** Netlify Identity (service basé sur GoTrue, un microservice open-source de gestion d'identité).

**Spécifications :**
- Mode d'inscription : **Invite only** — aucune auto-inscription possible ; seules les adresses explicitement invitées par l'administratrice peuvent créer un compte
- Chaque utilisatrice possède des identifiants individuels (email + mot de passe), distincts des comptes administrateurs (Netlify, GitHub)
- Portée d'accès : limitée à l'interface `/admin` (le CMS) — aucun accès au tableau de bord d'hébergement ni au dépôt de code
- Testé et validé (23 juillet 2026) : tentative de connexion avec une adresse email valide mais non invitée → refusée ("No user found")

---

## B. Sécurité et contrôle d'accès en écriture

**Objectif :** garantir qu'aucune personne non autorisée ne puisse modifier le contenu public du site, et que toute modification autorisée soit traçable.

**Outil :** Netlify Git Gateway (proxy d'API Git).

**Spécifications :**
- Le jeton d'accès GitHub réel n'est **jamais exposé côté client** (navigateur) — toutes les opérations d'écriture transitent par un proxy serveur (Git Gateway)
- Jeton d'accès **à portée limitée (scoped token)** : accès restreint à un seul dépôt (`coralline`), avec permission **Contents (lecture/écriture)** uniquement — aucun droit d'administration
- Chaque modification génère un **commit Git horodaté et attribué** à l'utilisatrice authentifiée (traçabilité complète)
- Réversibilité : toute modification peut être annulée instantanément via l'historique Git (`git revert`)
- Authentification à deux facteurs (2FA) activée sur les comptes racines (propriétaires) : GitHub et Netlify — méthode application d'authentification (TOTP), pas SMS, pour éviter d'associer un numéro de téléphone personnel aux comptes organisationnels

---

## C. Hébergement et déploiement

**Objectif :** publier le site de façon fiable, rapide, et permettre un déploiement automatique à chaque modification de contenu.

**Outil :** Netlify (plateforme d'hébergement avec CDN — réseau de distribution de contenu).

**Spécifications :**
- Déploiement continu (**CI/CD** — intégration et déploiement continus) : chaque `push` sur la branche `main` du dépôt GitHub déclenche automatiquement une reconstruction (`build`) et une republication du site
- Commande de build : `hugo --minify`
- Répertoire de publication : `public/`
- Environnement de prévisualisation disponible (déploiement de type *draft*, URL non indexée) pour valider des changements avant mise en production

---

## D. Gestion de contenu (CMS)

**Objectif :** offrir une interface d'édition simple (sans connaissance de code) pour des champs de contenu précis, définis à l'avance.

**Outil :** Decap CMS (anciennement Netlify CMS), version figée à **3.11.0** (voir note ci-dessous).

**Spécifications :**
- Configuration déclarative (`static/admin/config.yml`) définissant précisément les champs éditables — actuellement limité à la page **Inscription** (dates, horaires, liens d'inscription), scope volontairement restreint (V01)
- Chaque sauvegarde ("Publish") crée un commit Git, qui déclenche le pipeline de déploiement décrit en section C

**Note de gestion des dépendances :** la version de Decap CMS est **figée** (`decap-cms@3.11.0`) plutôt que de suivre automatiquement la dernière version disponible. Cette pratique évite qu'une mise à jour non testée du fournisseur ne casse l'outil en production — un incident réel de ce type (erreur d'affichage suite à une nouvelle version publiée quelques heures avant utilisation) a été rencontré et corrigé le 23 juillet 2026.

# Résumé technique — Projet site web Coralline (pour discussion professionnelle / entrevue)

**But de ce document :** pouvoir expliquer clairement, avec le bon vocabulaire, ce qui a été fait et pourquoi — à un pair technique, un superviseur, ou en entrevue.

---

## Résumé en une phrase (elevator pitch)

> "J'ai conçu et déployé un site web statique avec système de gestion de contenu pour un organisme à but non lucratif, en priorisant une architecture simple à maintenir, une sécurité par couches, et une autonomie d'édition pour une personne non technique."

---

## Si on te demande : "Tu as fait un site web, qu'est-ce que tu as utilisé et pourquoi ?"

**Réponse structurée (outil → pourquoi) :**

1. **Hugo (générateur de site statique)** — plutôt qu'un CMS traditionnel (WordPress) : pas de base de données à sécuriser/maintenir, performance élevée (fichiers déjà générés), surface d'attaque réduite.
2. **Netlify (hébergement + CI/CD)** — déploiement automatique à chaque changement de code, gratuit pour ce volume de trafic, gère nativement les formulaires et l'authentification légère.
3. **GitHub (contrôle de version)** — historique complet et traçable de tous les changements, standard de l'industrie, permet le travail collaboratif futur.
4. **Decap CMS + Netlify Identity + Git Gateway** — pour donner un accès d'édition à une personne non technique **sans** lui donner accès au code ou à l'hébergement ; chaque édition devient un commit Git traçable.

**Terme clé à utiliser :** architecture **JAMstack** (JavaScript, API, Markup) — site pré-généré, sans serveur applicatif à maintenir en continu.

---

## Si on te demande : "Comment as-tu géré la sécurité ?"

**Structure de réponse recommandée : principe de moindre privilège + défense en profondeur (plusieurs couches indépendantes).**

- **Authentification** : accès par invitation seulement (*invite-only registration*), pas d'inscription libre
- **Autorisation limitée (scoped access)** : le jeton d'accès au dépôt de code est restreint à un seul repository et à une seule permission (lecture/écriture du contenu, rien d'administratif)
- **Séparation des rôles** : les comptes "propriétaires" (admin technique) et les comptes "éditeurs" (contenu) sont complètement distincts, sans chevauchement de privilèges
- **Traçabilité** : chaque modification = un commit Git horodaté et attribué à une identité précise
- **Réversibilité** : possibilité de revenir à un état antérieur en quelques secondes (`revert`)
- **Authentification à deux facteurs (2FA)** sur les comptes à privilèges élevés
- **Gestion des secrets** : aucun mot de passe/jeton stocké en clair dans le code ou les notes — gestionnaire de mots de passe dédié, notes de projet contenant seulement des références

---

## Si on te demande : "As-tu testé que c'était sécuritaire, ou tu assumes juste que ça fonctionne ?"

Bonne question à anticiper — réponse : **oui, tests manuels ciblés**, pas juste une lecture de documentation :
- Test de non-régression fonctionnelle avec un compte de test dédié (persona distinct de l'admin) pour valider l'expérience réelle d'un utilisateur final
- Test négatif volontaire : tentative de connexion avec une adresse jamais autorisée, pour confirmer que l'accès est bien refusé
- Test de bout en bout (*end-to-end*) : modification → publication → vérification que le changement apparaît correctement sur le site en production

**Terme clé à utiliser :** *test négatif* (tester ce qui doit échouer, pas seulement ce qui doit réussir) — souvent négligé par les débutants, apprécié en entrevue.

---

## Incident réel géré (bon exemple à raconter en entrevue)

Une dépendance externe (Decap CMS) était référencée avec une version non figée (*floating version tag*), ce qui a causé une régression en production suite à une mise à jour du fournisseur non testée par moi. **Diagnostic** : recoupement entre le changelog du fournisseur et l'heure de l'incident. **Correction** : figer la version (*version pinning*) à une release stable connue. **Leçon appliquée** : ne jamais dépendre automatiquement de "la dernière version" d'un outil tiers en production.

C'est un bon exemple à donner si on te demande de parler d'un problème que tu as résolu.

---

## Vocabulaire à intégrer naturellement

| Terme professionnel | Explication simple |
|---|---|
| JAMstack | Architecture de site pré-généré, sans serveur applicatif continu |
| CI/CD (intégration/déploiement continus) | Publication automatique à chaque changement de code |
| Scoped token / least privilege | Accès limité au strict nécessaire |
| Invite-only registration | Inscription fermée, seulement sur invitation |
| Version pinning | Figer une version de dépendance plutôt que suivre la dernière automatiquement |
| Traceability (traçabilité) | Pouvoir identifier qui a fait quel changement, quand |
| End-to-end testing | Tester le parcours complet, pas juste une pièce isolée |
| Negative testing (test négatif) | Tester volontairement un scénario qui doit échouer |

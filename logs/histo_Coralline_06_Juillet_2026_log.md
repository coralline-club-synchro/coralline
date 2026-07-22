# histo_Coralline_06_Juillet_2026

## Contexte projet
- Client : club Coralline Natation Artistique (organisme but non lucratif)
- Bénévolat pour refaire site web désuet
- Site actuel : https://coralline.club/index.html (Weebly + JustHost)
- Contact client : 1 personne non-technique mais apprenante rapide

## Décisions d'architecture
- Stack : Hugo (site statique) + Decap CMS (interface admin) + Netlify (hébergement gratuit)
- Bitbucket : repo `coralline` sous compte JulieCardin75
- Pas de backend Python : sur-ingénierie pour ce besoin
- Hébergement : Netlify (gratuit, déploiement via CLI)
- Pas de connexion Bitbucket-Netlify OAuth (sécurité : accès trop large)
- Déploiement manuel : `netlify deploy --prod --dir=public`

## URLs importantes
- Site en production : https://coralline.netlify.app
- Admin Netlify : https://app.netlify.com/projects/coralline
- Netlify Project ID : 69d56696-e946-415a-aaa7-6e1dd48557d2
- Repo Bitbucket : https://bitbucket.org/JulieCardin75/coralline

## Commandes utiles
```bash
# Serveur local dev
hugo server --buildDrafts --disableFastRender

# Déployer en production
cd /Users/julie/VS_Code_Projects/Coralline
hugo --minify
netlify deploy --prod --dir=public
```

## Fonctionnalités requises
### Visiteurs (public)
- Pages : mission, équipe, valeurs, cours offerts, sport sain, contact ✅
- Nouvelles / actualités ✅
- Liens réseaux sociaux (Instagram, TikTok, Facebook) ✅
- Lien mailto: inscription (ouvre email pré-rempli) ✅
- Bannière photo accueil (placeholder, haute res à obtenir du club) 🔄

### Admin (1-2 personnes, interface /admin)
- Ajouter / supprimer photos ❌
- Poster une nouvelle ❌
- Modifier dates pratiques / compétitions ❌
- Modifier infos de contact ❌
→ Nécessite Decap CMS (prochaine étape)

## Approche de développement
- Étape par étape : tester et valider chaque morceau avant d'ajouter la suite

## État avancement (mis à jour 08 Juillet 2026)

### Fait ✅
- Hugo v0.164.0 installé (brew)
- Structure Hugo + thème Ananke, 7 pages contenu
- Menu navigation complet dans bon ordre
- Réseaux sociaux (Instagram, TikTok, Facebook) — déplacés sous le logo (pas dans la nav)
- Netlify CLI installé + site déployé en production (coralline.netlify.app)
- **Contenu répliqué depuis l'ancien site coralline.club** (recherche complète page par page, texte réel du client, pas du placeholder) : Accueil, Inscription, Cours offerts, Valeurs et équipe, Sport sain, Contact
- Header accueil corrigé : nav séparée de la bannière photo (plus de conflit de couleurs), titre du club affiché une seule fois
- Galerie photos repositionnée en fin de page d'accueil (ordre fidèle à l'original)
- Sections ajoutées : organismes partenaires, lieux d'entraînement, liens pratiques (Sport'Aide, DLTA, calendrier Outlook, etc.) — toutes en images cliquables téléchargées et hébergées localement (`static/images/liens/`)
- Conseil d'administration en grille visuelle (noms en vert + rôles), section "Nos valeurs" en 2 colonnes avec photo
- Cartes Google Maps intégrées sur la page Contact (Repentigny + Terrebonne)
- Vrai lien d'inscription en ligne trouvé et intégré (publicationsports.com)
- Formulaire infolettre fonctionnel (Netlify Forms natif, sans backend)
- **Recherche factuelle Weebly** : annonce du 20 juin 2026, fermeture du service dans 67 pays d'ici fin 2026 — mais **le Canada n'est PAS dans la liste**, donc pas de date de fermeture confirmée pour coralline.club à ce jour (source : page d'aide officielle Weebly)
- **Déploiement preview Netlify** fait (`netlify deploy --dir=public`, sans --prod) pour montrer le résultat à la cliente sans toucher au site en production
- Contact confirmé : **Genny** (mais pas la personne qui a toutes les infos — l'autre contact indisponible cette semaine). Premier retour de Genny : semble très bien.

### Fait ✅ (suite, 08 Juillet 2026) — Decap CMS branché de bout en bout
**Décision d'architecture importante** : Git Gateway (le mécanisme d'auth simple de Netlify pour Decap CMS) ne fonctionne PAS avec Bitbucket (seulement GitHub/GitLab). Le repo a migré vers GitHub.

**Compte GitHub du club (pas au nom de Julie)** :
- Email dédié déjà existant : `coralline.club.synchro@gmail.com`
- Compte GitHub existant : **GennyCoralline** (accès confirmé par Julie)
- Organisation GitHub créée : **coralline-club-synchro** → https://github.com/coralline-club-synchro
- Repo `coralline` créé dans cette organisation, code poussé depuis Bitbucket avec succès
- Repo passé de **Private à Public** (nécessaire : le plan Netlify gratuit ne permet pas de lier un repo privé à un nouveau site) — pas de risque, aucune donnée sensible dans le code
- Remote git local ajouté : `github` (SSH) en plus de `origin` (Bitbucket, conservé pour l'instant)

**Connexion GitHub ↔ Netlify (site `coralline`, compte Netlify jcardinbif@gmail.com)** :
- App GitHub "Netlify" installée sur l'organisation coralline-club-synchro, scope limité au repo `coralline`
- Repo lié dans Netlify (Project configuration → repo GitHub connecté, branche `main`, build `hugo --minify`, publish `public`)
- Netlify Identity activé + autorisé sur GitHub (OAuth "Netlify Auth", organisation coralline-club-synchro Grant)
- **Git Gateway activé et connecté** (Project configuration → Identity → Services → Git Gateway) — statut confirmé actif avec token GitHub API en place
- Note technique : Git Gateway est officiellement **déprécié** par Netlify (plus recommandé pour nouveaux sites, mais fonctionne toujours, failles de sécurité majeures encore corrigées). Alternative existante si besoin futur : **DecapBridge**. Pas d'action requise maintenant.

**Scope V01 du CMS (validé avec Julie)** :
- Éditable : page Inscription (saison/mois/année/lien, horaires Repentigny+Terrebonne avec ajout/retrait de lignes, horaire cours techniques) — déjà restructuré en front matter + shortcodes Hugo (`layouts/_shortcodes/inscription-intro.html`, `horaire-recreatif.html`, `horaire-technique.html`)
- Éditable priorité 2 (pas urgent) : noms du Conseil d'administration (Valeurs et équipe), galerie photos accueil, logos partenaires accueil
- PAS éditable pour l'instant : Cours offerts, Sport sain, Contact (restent en dur)
- Config de départ créée : `static/admin/config.yml`, `static/admin/index.html`, script Netlify Identity dans `layouts/_partials/head-additions.html`

**Invitation Genny** : invite Netlify Identity envoyée avec son **email personnel** (pas le gmail partagé du club — décision : chaque utilisatrice du CMS doit avoir son propre compte/mot de passe individuel). En attente qu'elle accepte l'invite et teste une modification réelle sur la page Inscription (créer commit → vérifier republication du site).

**Explications sécurité données à Julie** (question posée : comment être sûr qu'un individu malveillant ne peut pas modifier le site) — 6 couches de protection :
1. Site public = HTML statique généré, pas de point d'entrée modifiable en navigant
2. `/admin` exige un token valide délivré par Netlify Identity (GoTrue) — pas d'accès sans authentification serveur
3. **Registration = Invite only** (à vérifier/confirmer que ce réglage est bien actif — pas encore fait)
4. Le vrai token GitHub n'est jamais exposé au navigateur — passe par Git Gateway (proxy serveur)
5. Token GitHub scoped : accès seulement au repo `coralline`, permission "Contents" seulement
6. Historique Git = traçabilité (qui/quand) + réversibilité instantanée (revert commit)
- Maillon faible identifié : sécurité des mots de passe individuels + absence de 2FA sur les comptes Netlify (jcardinbif@gmail.com) et GitHub (GennyCoralline)

### À faire ❌
- **PROCHAINE ÉTAPE (prévue 09 Juillet 2026)** : activer la double authentification (2FA) sur le compte Netlify (jcardinbif@gmail.com) et le compte GitHub (GennyCoralline)
- Vérifier que Netlify Identity → Registration est bien sur "Invite only" (pas "Open") — pas encore explicitement confirmé
- Confirmer que Genny a accepté l'invite et testé une modification (Inscription → publier → vérifier sur le site en ligne)
- Étendre le CMS aux champs priorité 2 (CA, galerie photos, logos partenaires) — nécessite de restructurer ces sections en front matter comme fait pour Inscription
- Bannière haute résolution (demander au club)
- PDFs officiels (code d'éthique, philosophie de gestion, calendrier) — pointent encore vers l'ancien site coralline.club, à migrer quand le club fournit les fichiers
- Décider du sort du repo Bitbucket original (garder en backup ou fermer)
- Transfert domaine coralline.club → Netlify (quand prêt) — Julie a confirmé comprendre la distinction domaine (déjà payé, réutilisable) vs hébergement (change de Weebly/JustHost vers Netlify sans coût additionnel)

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

### Fait ✅ (suite, 22 Juillet 2026) — Bannière + test BetaTester + INCIDENT synchro GitHub résolu

**Bannière accueil remplacée** : photo haute résolution reçue du club (`images/Photo_cercle_filles_Grande_Ori.jpg`, 5198×3465), redimensionnée (2000px large, JPEG qualité 80) → `static/images/banniere_accueil.jpg`. Cadrage ajusté 2 fois suite au retour de Julie : `background-size: cover`, `background-position: center 68%` (au lieu de `center` pur) pour montrer le bas de la photo (les nageuses) plutôt que le haut, sans couper les visages. Déployé en production.

**Test persona BetaTester** : Julie a créé un compte email dédié (`beta42testeux@proton.me`) et l'a invité via Netlify Identity, pour tester l'expérience d'une éditrice non-admin (elle-même, mais sans utiliser ses droits super-admin/GitHub). Bonne pratique de test avant de faire confiance à l'accès réel de Genny.

**🔴 INCIDENT DÉCOUVERT ET RÉSOLU** : en testant `/admin` avec BetaTester, tous les champs de la page Inscription apparaissaient VIDES (saison, mois, année, 0 horaire). Investigation a révélé que **le repo GitHub (coralline-club-synchro/coralline) ne contenait qu'une version incomplète/ancienne du site** — le vrai travail de réplication de contenu (Inscription détaillée, Cours offerts, Valeurs, Contact, Sport sain, config Decap CMS, layouts) n'avait jamais été poussé vers GitHub après la migration du 8 juillet ; il existait seulement en local et sur l'ancien repo Bitbucket. Le site public semblait correct uniquement parce qu'il avait été déployé directement depuis l'ordinateur de Julie (`netlify deploy --prod`, contournant Git). Avec Git Gateway maintenant actif, toute publication future via `/admin` aurait utilisé cette base GitHub incomplète et risqué d'écraser le site.
- **Résolu** : commit de synchronisation (`eed7089`) avec les 55 fichiers manquants, poussé vers GitHub via HTTPS + nouveau fine-grained token (nom "CorallineSync", généré par Julie, utilisé une seule fois en ligne de commande, jamais sauvegardé)
- Remote SSH `github` ne fonctionnait pas (clé SSH liée au compte personnel de Julie, pas autorisée sur l'organisation) — le push HTTPS+token reste la méthode qui marche pour l'instant
- Netlify a redéployé automatiquement depuis GitHub après le push, contenu vérifié correct sur le site public
- `.gitignore` mis à jour : exclusion de `logs/notes_Ju.sh` (mots de passe en clair repérés et retirés par Julie — bon réflexe), `.claude/` (config agent), `/images/` (photos brutes de travail, non lues par Hugo, seul `static/images/` est servi)

**Leçon apprise / vigilance future** : toujours vérifier que `git push github main` a été fait après un lot de changements de contenu, surtout maintenant que Decap CMS dépend du contenu réellement présent sur GitHub — ne plus se fier uniquement à `netlify deploy` manuel qui masque ce genre de désynchronisation.

**Pédagogie** : questions de Julie sur "qu'est-ce que Netlify exactement" et "comment nommer/décrire correctement le déploiement" — expliqué : Netlify = hébergeur web (hosting provider) avec CDN, `public/` = dossier généré par Hugo, "déployer" = envoyer ce dossier vers l'hébergeur. Distinction clarifiée entre les 3 mots de passe différents (Proton = boîte mail, Netlify perso = admin site, Netlify Identity BetaTester = accès limité à `/admin` seulement).

**✅ TEST BETATESTER COMPLET RÉUSSI (22 Juillet 2026)** : après la resynchronisation, Julie a repris le test avec le persona BetaTester — champs Inscription bien pré-remplis (saison "estivale", horaires Grenouilles/Méduses/Dauphines/Maîtres). Modification réelle testée : titre "Inscription" → "Inscriptions" → Publish → commit GitHub `763bf1c` créé automatiquement, **attribué correctement à `beta42testeux@proton.me`** (traçabilité confirmée) → Netlify a redéployé automatiquement en moins d'une minute → site public mis à jour, vérifié par Julie ET techniquement (curl). Julie a ensuite remis "Inscription" sans le "s" pour revenir à l'état initial — toujours fonctionnel. **Le pipeline complet CMS est validé de bout en bout, prêt pour Genny.**

**🛡️ TESTS DE SÉCURITÉ — accès non autorisé (22 Juillet 2026)**
Objectif : vérifier que "Invite only" (couche de sécurité #3) fonctionne réellement, en simulant un attaquant qui tombe sur `/admin` sans invitation.

- **Test 1 (faux positif corrigé)** : Julie ouvre `/admin` dans un nouvel **onglet** d'une fenêtre privée déjà utilisée pour BetaTester → entre directement sans mot de passe. Panique initiale, mais diagnostic correct : dans une fenêtre de navigation privée, tous les onglets partagent la session tant que la fenêtre entière n'est pas fermée — ce n'était que la session BetaTester (déjà légitimement invitée) encore active, pas une faille. Julie a elle-même relevé l'erreur de méthodologie de ce test avant de conclure trop vite — bon réflexe.
- **Test 2 (le vrai test)** : fenêtre privée 100% fraîche → tentative de connexion sur `/admin` avec une **vraie adresse email existante mais jamais invitée** sur ce site (un contact réel de Julie) → message obtenu : **"No user found with that email, or password invalid."**
- **✅ Résultat confirmé : "Invite only" fonctionne correctement.** Aucun compte n'existe pour une adresse non invitée, aucune inscription libre n'est possible, aucun accès accordé. Le pipeline CMS résiste à un scénario réaliste d'accès non autorisé.
- **Leçon méthodologique retenue** : pour tester un scénario "jamais invité", il faut (a) fermer complètement toutes les fenêtres privées entre deux tests différents (pas juste les onglets), et (b) utiliser une adresse valide mais réellement jamais invitée — pas un texte au hasard, pour éviter une erreur de format qui fausserait la conclusion.

Tests de sécurité restants à faire éventuellement (moins urgents, système déjà jugé fiable) : tenter connexion BetaTester sur github.com et app.netlify.com directement (devrait échouer, comptes non liés), vérifier qu'aucune option d'inviter d'autres utilisateurs n'apparaît dans `/admin`.

**✅ 2FA ACTIVÉE sur les deux comptes racines (22 Juillet 2026)** : GitHub (GennyCoralline) et Netlify (jcardinbif@gmail.com), méthode app d'authentification (Authenticator, pas SMS — pour éviter d'exposer un numéro de téléphone, cohérent avec la préoccupation initiale de Julie de garder les comptes du club anonymisés). Codes de récupération sauvegardés dans un gestionnaire de mots de passe (Bitwarden), pas dans les fichiers du projet. Principe de gestion des secrets établi et compris par Julie : les vraies valeurs sensibles (mots de passe, tokens, codes de récup) vivent uniquement dans le gestionnaire de mots de passe ; les notes de projet ne contiennent que des références ("voir Bitwarden → nom de l'entrée"), jamais la valeur elle-même. Le maillon faible identifié plus tôt dans la session est maintenant réglé.

### À faire ❌
- Confirmer que Genny a accepté l'invite et testé une modification (Inscription → publier → vérifier sur le site en ligne)
- Étendre le CMS aux champs priorité 2 (CA, galerie photos, logos partenaires) — nécessite de restructurer ces sections en front matter comme fait pour Inscription
- PDFs officiels (code d'éthique, philosophie de gestion, calendrier) — pointent encore vers l'ancien site coralline.club, à migrer quand le club fournit les fichiers
- Décider du sort du repo Bitbucket original (garder en backup ou fermer)
- Transfert domaine coralline.club → Netlify (quand prêt) — Julie a confirmé comprendre la distinction domaine (déjà payé, réutilisable) vs hébergement (change de Weebly/JustHost vers Netlify sans coût additionnel)
- Envisager d'ajouter une clé SSH pour GennyCoralline sur l'organisation, pour éviter de régénérer un token à chaque fois qu'un push manuel est nécessaire

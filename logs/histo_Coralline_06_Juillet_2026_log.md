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

**🐛 INCIDENT ET RÉSOLUTION — Genny a testé pour vrai, erreur au Publish (23 Juillet 2026)**
Genny a accepté l'invitation, créé son mot de passe, ouvert `/admin`, cliqué sur Inscription et tenté de modifier un horaire. Au clic sur **Publish**, une page d'erreur React est apparue ("Minified React error #130"). Julie a envoyé une capture d'écran (photo du portable).

Diagnostic : `static/admin/index.html` chargeait Decap CMS via un **tag de version flottant** (`decap-cms@^3.0.0` sur unpkg), qui prend toujours la toute dernière version 3.x publiée. Une nouvelle version (**3.15.0**) venait d'être mise en ligne quelques heures avant l'incident — coïncidence très probable, l'erreur #130 étant un bug connu et récurrent de Decap CMS (plusieurs issues GitHub similaires trouvées).

**Correctif appliqué** : version figée à **3.11.0** (version stable documentée officiellement) au lieu du tag flottant `^3.0.0` — évite ce genre de régression surprise à chaque nouvelle sortie de Decap.

**Découverte cruciale en vérifiant GitHub** : malgré l'écran d'erreur affiché à Genny, sa publication **avait réellement réussi** — 3 commits créés sur GitHub (`763bf1c`, `35f96da`, `7a2ed8e`), incluant un vrai changement de sa part : horaire Terrebonne Maîtres modifié de 18h-19h30 à **18h30-20h**. L'erreur React était un bug d'affichage de la confirmation, pas un échec réel de sauvegarde (Git Gateway avait déjà fait son travail avant le crash du composant React).

**Résolution** : `git fetch` + `git merge` pour combiner le correctif de version ET la vraie modification de Genny sans rien perdre, poussé vers GitHub (`b5e2bf9`), site public vérifié à jour avec le bon horaire.

**Leçon pour Julie/Genny à venir** : si une erreur d'affichage apparaît après un Publish, ne pas assumer que rien n'a été sauvegardé — vérifier d'abord sur le site en ligne (après quelques minutes) avant de recommencer la modification, pour éviter les doublons.

**✅ 2e test de Genny réussi (23 Juillet 2026)** : après le correctif de version, Genny a modifié un horaire (Dauphines Repentigny : "18h-20h" → "18h-20h00") sans aucune erreur cette fois — commit `2142b48` propre. Confirme que le correctif de version Decap CMS (3.11.0 figée) tient en usage réel.

**✅ CMS étendu aux champs priorité 2 (23 Juillet 2026)** — Julie a donné le feu vert après la preuve de concept réussie sur Inscription :
- **Conseil d'administration** (page Valeurs et équipe) : liste `ca` (nom/rôle) en front matter, shortcode `layouts/_shortcodes/ca-liste.html`, éditable via collection "equipe" dans Decap
- **Galerie photos accueil** : liste `galerie` (image) en front matter, shortcode `galerie-photos.html`, éditable via collection "accueil" — ajout/retrait de photos possible via le widget image de Decap
- **Logos partenaires accueil** : liste `partenaires` (nom/lien/image) en front matter, shortcode `partenaires-liste.html`, éditable via collection "accueil"
- `static/admin/config.yml` mis à jour avec les 2 nouvelles collections ("equipe", "accueil")
- Build + déploiement Netlify + push GitHub tous vérifiés, contenu identique avant/après restructuration (confirmé par `grep` sur le HTML généré)

**📄 Documentation professionnelle créée (23 Juillet 2026)** — à la demande de Julie, pour appuyer une présentation professionnelle du travail (supervision, entrevue) :
- `docs/Specifications_Techniques_Coralline.md` : document de référence structuré Objectif → Outil → Spécifications par volet (authentification, sécurité, hébergement, CMS)
- `docs/Resume_Entrevue_Technique_Coralline.md` : aide-mémoire personnel avec vocabulaire professionnel (JAMstack, CI/CD, scoped token, version pinning, test négatif...) et réponses structurées aux questions probables en entrevue
- Dossier `docs/` volontairement distinct de `logs/` (référence structurée par sujet vs journal chronologique — convention établie précédemment)

**🔍 Audit des dépendances à l'ancien site (23 Juillet 2026)** : recherche exhaustive de toutes les références `coralline.club` dans le contenu. Conclusion : la grande majorité (adresse courriel `info@coralline.club`, `baseURL` dans hugo.toml, lien Instagram) ne sont **pas** des dépendances réelles à l'ancien hébergement — seuls **5 PDF** (philosophie de gestion, code d'éthique, politique respect et collaboration, comprendre le sport, DLTA) sont physiquement hébergés sur l'ancien serveur Weebly/JustHost. Migration jugée simple et gratuite une fois les fichiers originaux obtenus (pas de complexité technique, juste besoin des fichiers sources).

**Contexte important appris** : l'hébergement actuel (Weebly/JustHost) est **payé jusqu'à une échéance prochaine** ("le temps payé couvert") — donc échéance réelle à venir, pas juste une question théorique. Julie a déjà écrit à Genny pour demander les 5 PDF, en expliquant le pourquoi (indépendance vis-à-vis de l'ancien hébergement avant son expiration).

**Autres points soulevés par Julie (23 Juillet 2026), à traiter dans une prochaine session :**
- Le compte Netlify (hébergement) appartient personnellement à Julie (jcardinbif@gmail.com), contrairement à GitHub qui est déjà au nom du club — **risque de continuité** si Julie cesse le bénévolat un jour. Question ouverte : transférer la propriété Netlify vers un compte du club, comme fait pour GitHub ?
- Qui gère/paie le renouvellement du nom de domaine `coralline.club` — à clarifier avec le club
- Netlify Identity/Git Gateway étant déprécié (voir plus haut) reste un risque de dépendance à surveiller à moyen terme
- Formulaire d'infolettre collecte des courriels — vérifier si des obligations de la **Loi 25** (protection des renseignements personnels, Québec) s'appliquent — pas encore investigué, à ne pas assumer sans vérifier

**📚 Organisation de la documentation (23 Juillet 2026)** — Julie a demandé un endroit pour classer/répertorier tout ce qui s'accumulait. Structure finale en 3 dossiers distincts (jamais mélanger) :
- `logs/` — journal chronologique uniquement (ce fichier)
- `docs/` — documentation de référence : `Specifications_Techniques_Coralline.md`, `Resume_Entrevue_Technique_Coralline.md`, `Comprendre_Domaine_Hebergement_DNS.md` (explication domaine/hébergement/DNS avec analogie téléphone, tableau de questions pour la personne de l'ancien site, plan de transfert Netlify)
- `communications/` (nouveau) — messages/courriels rédigés pour l'externe : `message_client_preview.md`, `message_genny_invitation.md`, `message_genny_verif_fix.md`, `courriel_questions_domaine_ancien_site.md` (prêt à envoyer à Genny → sa collègue de l'ancien site)

**⏸️ PAUSE — Julie s'absente du projet (23 Juillet 2026)**
Julie doit consacrer du temps à d'autres tâches et ne sera pas disponible pour valider des questions pendant un moment. Consignes données pour la suite :
- **Permission confirmée** : autorisation permanente déjà établie pour ce projet (voir [[feedback_autonomy]]) couvre tout travail fichiers/code/terminal (édition contenu, build Hugo, commit/push GitHub avec token déjà fourni, déploiement Netlify CLI) — peut continuer sans redemander.
- **Limite claire identifiée** : impossible de naviguer dans un navigateur web (pas d'outil browser) — donc tout ce qui nécessite de cliquer dans le tableau de bord Netlify (créer compte club, inviter comme "Owner", activer des réglages via l'interface) doit attendre le retour de Julie.
- **Rien n'est bloqué actuellement** : le scope CMS demandé (Inscription + CA + galerie + partenaires) est 100% complété, poussé sur GitHub, déployé en production. Aucune action requise de Julie pendant son absence.
- **Si du contenu arrive entre-temps** (ex. les 5 PDF de Genny), possible de l'intégrer sans la déranger.
- **À son retour** : reprendre par le transfert de propriété Netlify vers un compte du club (nécessite sa présence pour les clics dans le navigateur) — voir plan détaillé dans `docs/Comprendre_Domaine_Hebergement_DNS.md`.

### À faire ❌
- **PROCHAINE ÉTAPE AU RETOUR DE JULIE** : transfert de propriété Netlify vers le compte du club (créer compte `coralline.club.synchro@gmail.com` sur Netlify, l'inviter comme "Owner" de l'équipe) — nécessite la présence de Julie, ne peut pas être fait de façon autonome (pas d'accès navigateur)
- Julie/Genny testent l'édition des nouveaux champs (CA, galerie, partenaires) dans `/admin`
- Recevoir les 5 PDF de Genny (ou sa collègue, suite au courriel) et les migrer vers notre hébergement (retirer la dépendance à l'ancien site)
- Décider du sort du repo Bitbucket original (garder en backup ou fermer)
- Transfert domaine coralline.club → Netlify (quand prêt) — Julie a confirmé comprendre la distinction domaine (déjà payé, réutilisable) vs hébergement (change de Weebly/JustHost vers Netlify sans coût additionnel)
- Discuter du renouvellement du domaine une fois les réponses de la collègue reçues
- Envisager d'ajouter une clé SSH pour GennyCoralline sur l'organisation, pour éviter de régénérer un token à chaque fois qu'un push manuel est nécessaire

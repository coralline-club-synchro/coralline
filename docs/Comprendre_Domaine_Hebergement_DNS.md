# Comprendre : nom de domaine, hébergement, DNS

**But de ce document :** notes personnelles pour comprendre et expliquer clairement la différence entre ces concepts, dans le contexte de la migration du site Coralline.

---

## Les trois éléments, et ce qu'ils font

- **Nom de domaine** (`coralline.club`)
  - Un nom **loué** (abonnement, pas un achat permanent), enregistré chez un **registraire**
  - Ne contient aucun fichier, aucun contenu — c'est juste un nom réservé
  - Doit être **renouvelé périodiquement** (souvent chaque année) pour rester à nous

- **Hébergement** (Weebly actuellement, Netlify pour le nouveau site)
  - L'endroit où les **vrais fichiers du site** sont stockés et servis aux visiteurs
  - Complètement séparé du nom de domaine — on peut changer d'hébergeur sans changer de nom

- **DNS** (*Domain Name System*)
  - L'**annuaire** qui relie le nom de domaine à l'hébergeur actuel
  - Répond à la question : "quand quelqu'un tape `coralline.club`, où est-ce que je vais chercher le contenu ?"
  - C'est ce qu'on doit modifier pour faire pointer `coralline.club` vers Netlify au lieu de Weebly

## Analogie

- **Nom de domaine** = un numéro de téléphone qu'on possède
- **Hébergement** = l'endroit où on habite réellement en ce moment
- **DNS** = le service d'annuaire qui redirige les appels vers notre adresse actuelle

On peut déménager (changer d'hébergeur) sans changer de numéro de téléphone (nom de domaine) — il suffit de mettre à jour l'annuaire (DNS).

## Pourquoi Netlify donne une adresse différente pour l'instant

- `coralline.netlify.app` = adresse **temporaire et gratuite**, attribuée automatiquement par Netlify
- Fonctionne immédiatement, sans configuration
- C'est le nom de domaine de **Netlify**, pas celui du club
- Pour utiliser `coralline.club` avec Netlify, il faut changer le DNS chez le registraire actuel (probablement lié à JustHost)

## Point clé sur le renouvellement du domaine

- **Payer pour garder `coralline.club` est obligatoire, peu importe l'hébergeur choisi** (Weebly ou Netlify) — ce n'est pas lié à notre projet technique
- C'est une responsabilité/dépense du club qui **existe indépendamment** de la migration
- Mais on a quand même besoin de savoir **qui gère ce paiement**, pour pouvoir un jour modifier le DNS
- Si personne ne renouvelle, le club **perd complètement le nom** `coralline.club` — peu importe où le site est hébergé

---

## Questions à poser à la personne qui gérait l'ancien site

| Question | Pourquoi |
|---|---|
| Qui a accès au compte du registraire (où `coralline.club` est enregistré) ? | Nécessaire pour renouveler le domaine et rediriger le DNS vers Netlify plus tard |
| Quelle est la date exacte de fin de la période payée chez Weebly/JustHost ? | Avoir un vrai échéancier, pas juste "bientôt" |
| Le domaine se renouvelle-t-il automatiquement (carte de crédit enregistrée) ou manuellement ? | Éviter de perdre le domaine si le renouvellement dépend d'une carte/personne qui n'est plus impliquée |
| Le courriel `info@coralline.club` est-il hébergé au même endroit que le site web, ou séparément ? | Éviter de couper les courriels du club par accident en migrant le site |
| Peut-elle fournir les 5 PDF originaux (philosophie de gestion, code d'éthique, etc.) ? | Nécessaires pour héberger ces documents nous-mêmes et arrêter de dépendre de l'ancien site |
| Y a-t-il d'autres services reliés à ce compte (sous-domaines, formulaires, statistiques) ? | Éviter de perdre quelque chose d'important sans le savoir |

### Si on demande "qu'est-ce que vous voulez faire au juste ?"

> On a construit un nouveau site (déjà fonctionnel, testé) hébergé ailleurs (Netlify), et on veut éventuellement faire pointer notre nom de domaine `coralline.club` vers ce nouveau site plutôt que vers l'ancien. On ne veut rien couper brusquement — on planifie la transition avant la fin de la période déjà payée.

### Si on demande "pourquoi changer d'hébergeur ?"

- L'ancien site est désuet (ancien design, plus difficile à maintenir)
- Le nouveau site permet au club de modifier le contenu soi-même (dates, horaires) sans connaissances techniques
- Hébergement gratuit sur Netlify

### Vocabulaire utile si la conversation devient technique

- **Registraire** : l'entreprise où le nom de domaine est enregistré
- **DNS** : l'aiguillage entre le nom de domaine et l'hébergeur
- **Renouvellement du domaine** : distinct de l'hébergement, paiement séparé et obligatoire

---

## Plan pour la propriété du compte Netlify (sujet connexe)

- Actuellement, le site Netlify appartient personnellement à Julie (jcardinbif@gmail.com)
- Objectif : donner aussi la propriété au club, comme déjà fait pour GitHub
- Plan simple (pas de transfert brutal) :
  1. Créer un compte Netlify avec l'adresse du club (`coralline.club.synchro@gmail.com`)
  2. Inviter cette adresse comme membre de l'équipe Netlify avec le rôle **"Owner"**
  3. Les deux comptes coexistent, chacun avec un accès complet et indépendant

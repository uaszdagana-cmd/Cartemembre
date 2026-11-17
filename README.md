# 🎓 AERDZ — Carte Étudiant Dagana

Site web (HTML / CSS / JavaScript, sans serveur) pour demander, payer et obtenir la carte étudiante de l'**AERDZ**.

## Nouveau parcours : le paiement vient APRÈS la validation

| Étape | Qui | Ce qui se passe |
|---|---|---|
| 1 | Étudiant | Remplit le formulaire (gratuit, aucun paiement). Reçoit une **référence de suivi** `DGN-XXXXXX`. |
| 2 | Admin | **Valide** (ou rejette) le dossier. Un numéro de carte est attribué. |
| 3 | Étudiant | Sur **Suivi & paiement**, le paiement Wave s'ouvre : il envoie 1 000 FCFA et saisit son n° de transaction (+ capture facultative). |
| 4 | Admin | Vérifie dans Wave, puis **confirme** le paiement (ou le refuse avec un motif : l'étudiant peut re-soumettre). |
| 5 | Étudiant | La **carte** devient visible et imprimable (PDF). |

La règle « pas de paiement avant validation » est appliquée dans le code métier (`Workflow` dans `js/data.js`),
pas seulement dans l'interface : un appel direct depuis la console du navigateur est refusé aussi.
La carte n'est affichée qu'après confirmation du paiement.

## Fichiers (à envoyer tels quels, en gardant les dossiers)

```
index.html          Accueil
inscription.html    Formulaire d'inscription (sans paiement)
suivi.html          Suivi de la demande + paiement Wave (après validation)
carte.html          Carte étudiant (après confirmation du paiement)
admin.html          Espace administrateur
css/style.css       Design
js/config.js        ⚙️ RÉGLAGES : prix, numéro Wave, contacts, filières
js/data.js          Noyau : validation, stockage, workflow, authentification admin
js/ui.js            Composants : en-tête/pied, notifications, boîtes de dialogue, carte
js/inscription.js   Page d'inscription
js/suivi.js         Page de suivi et de paiement
js/carte.js         Page carte
js/admin.js         Tableau de bord
img/logo.jpg        Logo AERD/Z
```

## Utilisation

- **En local** : double-clic sur `index.html`, ou servez le dossier (`python -m http.server`).
- **Mise en ligne gratuite** : Netlify, GitHub Pages, Cloudflare Pages (glisser-déposer le dossier).
- **Changer le prix / numéro Wave / contacts** : uniquement `js/config.js`.
- **Admin** : ouvrez `admin.html`. À la **première visite**, vous créez votre mot de passe (8 caractères minimum, lettre + chiffre).
  Il n'y a plus de mot de passe par défaut dans le code.

## Ce qui a changé par rapport à votre version

- Paiement déplacé après la validation (nouvelle page **Suivi & paiement**, `suivi.html` qui était vide).
- Admin : filtres par étape (À valider / À payer / Paiements à vérifier / Cartes prêtes / Rejetées), recherche,
  tri, pagination, fiche détaillée avec historique, confirmation ou refus motivé du paiement, export CSV, sauvegarde/restauration.
- Détection d'un numéro de transaction déjà utilisé pour un autre dossier.
- Mot de passe admin salé (PBKDF2), blocage temporaire après 5 échecs, déconnexion après 30 min d'inactivité.
- Carte : vrai logo AERD/Z, date de délivrance, année universitaire de la délivrance, impression propre en PDF.
- Interface : notifications, boîtes de dialogue accessibles, animations douces, menu mobile, validation en direct.
- Anciennes données (version v1) migrées automatiquement dans le même navigateur.

## ⚠️ Limite importante : les données sont dans le navigateur

Cette version n'a **pas de serveur**. Les inscriptions sont stockées dans le `localStorage` **du navigateur où elles ont été saisies**.
Conséquence : une inscription faite sur le téléphone d'un étudiant **n'apparaît pas** dans l'admin ouvert sur un autre appareil.
Le site fonctionne donc pour une démonstration, ou si tout se fait sur un seul appareil (ex. un poste de l'amicale où les étudiants s'inscrivent).

Pour un vrai usage multi-appareils (étudiants sur leurs téléphones, admin sur son ordinateur), il faut une base de données en ligne
(PHP/MySQL, Supabase, Firebase…). L'architecture est prête pour cela : toute la lecture/écriture passe par `Store` et `Workflow` dans `js/data.js`.

Autres limites liées à l'absence de serveur :
- Le mot de passe admin protège l'interface, pas les données : quelqu'un qui ouvre les outils de développement du même navigateur peut les lire.
- Stockage limité (~5 Mo) : environ 50 à 100 dossiers avec photos. Une jauge est affichée dans l'admin.
- Faites des **sauvegardes JSON** régulières (admin → « Télécharger la sauvegarde »).
- La vérification du paiement Wave est **manuelle** : l'admin compare le numéro de transaction avec son historique Wave.

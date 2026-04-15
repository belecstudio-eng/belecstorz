# Déploiement GitHub + Render

Ce projet peut être publié en ligne avec GitHub pour le code et Render pour exécuter le serveur Node.js.

## 1. Préparer le dépôt GitHub

Dans le dossier du projet, lancez:

```powershell
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/VOTRE-USER/VOTRE-REPO.git
git push -u origin main
```

Si le dépôt GitHub existe déjà, utilisez seulement:

```powershell
git add .
git commit -m "Prepare Render deployment"
git push
```

## 2. Vérifier les fichiers utiles au déploiement

Le projet contient déjà:

- `package.json` avec `npm start`
- `render.yaml` pour la création du service Render
- `.env.example` pour voir les variables à configurer

## 3. Créer le service sur Render

1. Ouvrez Render.
2. Cliquez sur New +.
3. Choisissez Blueprint si Render détecte `render.yaml`.
4. Connectez votre compte GitHub.
5. Sélectionnez votre dépôt.
6. Lancez la création du service.

Render va utiliser automatiquement:

- `buildCommand`: `npm install`
- `startCommand`: `npm start`
- le disque persistant monté sur `/var/data/studio-belec`

## 4. Configurer les variables d'environnement dans Render

Dans Render, ouvrez votre service puis Environment et renseignez au minimum:

```text
ADMIN_USERNAME=admin
ADMIN_PASSWORD=mot-de-passe-fort
CONTACT_OWNER_EMAIL=belecstudio@gmail.com
CONTACT_SMTP_USER=belecstudio@gmail.com
CONTACT_SMTP_PASS=VOTRE_MOT_DE_PASSE_APPLICATION_GMAIL
PAYMENT_DEPOSIT_NUMBER=0575335641
ORDER_DEPOSIT_EMAIL_DELAY_MS=60000
ORDER_PAYMENT_INSTRUCTIONS_EMAIL_DELAY_MS=60000
STORAGE_DIR=/var/data/studio-belec
```

Notes:

- `CONTACT_SMTP_PASS` doit être un mot de passe d'application Gmail.
- `PORT` n'a pas besoin d'être défini manuellement sur Render, Render le fournit automatiquement.
- `STORAGE_DIR` doit rester `/var/data/studio-belec` pour conserver vos commandes, logos, covers et sons.

## 5. Déployer

Après avoir rempli les variables:

1. Cliquez sur Manual Deploy puis Deploy latest commit.
2. Au premier démarrage, le serveur copie automatiquement `data.json`, `carts.json`, `orders.json`, `branding.json`, `covers/`, `sons/` et `branding/` du dépôt vers le disque persistant si ce disque est encore vide.
3. Attendez que le statut passe à Live.
4. Ouvrez l'URL Render générée.

## 6. Vérifier après mise en ligne

Testez au minimum:

1. la page publique
2. `admin.html`
3. `admin-orders.html`
4. l'envoi du formulaire contact
5. la création d'une commande
6. la réception des emails Gmail

## 7. Publier les prochaines modifications

Pour chaque changement local:

```powershell
git add .
git commit -m "Update site"
git push
```

Si l'auto deploy est activé sur Render, le site se mettra à jour automatiquement après le `git push`.

## 8. Important sur les fichiers persistants

Les fichiers suivants doivent vivre sur le disque Render et non seulement dans GitHub:

- `data.json`
- `carts.json`
- `orders.json`
- `branding.json`
- `covers/`
- `sons/`
- `branding/`

Le projet est déjà prévu pour cela grâce à `STORAGE_DIR`.

Au premier déploiement, le contenu actuel du dépôt sert d'initialisation. Ensuite, les modifications faites en production restent sur le disque Render et ne sont pas écrasées par les redéploiements.

## 9. Si GitHub Pages est proposé

N'utilisez pas GitHub Pages pour ce projet complet.

GitHub Pages ne peut pas:

- exécuter `server.js`
- recevoir les formulaires côté serveur
- envoyer les emails Gmail via Nodemailer
- sauvegarder les commandes et fichiers uploadés

Pour ce site, GitHub sert au code source et Render sert à l'hébergement.

# MyNetWork

Créer un nouveau projet VueJs intitulé MyNetWork, un mini site de type réseau social, qui va être composé des pages suivantes :

## Accueil (sans connexion):

dans laquelle vous allez afficher un fils d'actualités

1. il n'est pas possible de liker les posts ou d'ajouter des commentaires lorsque l'utilisateur n'est pas connecté

## Créer un profil:

dans lequel vous pouvez créer des profils utilisateurs

1. lors de l'inscription, il faut vérifier que le pseudo et le email n'est pas déjà utilisé par un
   autre compte utilisateur, afficher un message pour expliquer à l'utilisateur la situation
2. le pseudo doit contenir au moins 3 caractères, l'email doit être valide et le password doit
   contenir au mois 6 caractères
3. si les pseudo et email sont valides, un message vient confirmer que le compte est créé et
   l'utilisateur est dirigé vers la page de connexion

## Se connecter:

dans laquelle vous avez avec formulaire de connexion par email / mot de
passe

## Accueil (après connexion):

une fois que l'utilisateur s'est connecté avec un compte valide il est redirigé vers la page d'Accueil

1. il peut désormais créer de nouveaux posts via un formulaire qui apparaît au début du fils de discussion
2. d'ajouter des commentaires pour chaque post vie un formulaire sous le post créé 8. de liker les posts
3. l'utilisateur peut finaliser sa session en cliquant sur le lien Déconnexion dans le menu

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Compile and Minify for Production

```sh
npm run build
```

### Run database

```sh
npm run db
```

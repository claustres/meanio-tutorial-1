# Introduction à MEAN.IO

Ce premier article d'une série consacrée à MEAN.IO va nous permettre d'introduire les principaux concepts du framework et d'obtenir un environnement de développement opérationnel pour les exercices pratiques des prochains articles. Ce framework commençant à connaitre un certain "essor" (plus de 6 000 recommandations et 2 000 forks sur GitHub) je remercie Programmez! de me laisser l'occasion d'initier une des premières ressources francophone sur le sujet. 

## Présentation

[MEAN.IO](http://mean.io/), est un framework Javascript "full-stack" permettant de créer rapidement une application web avec [MongoDB](http://www.mongodb.org/), [Node.js](http://www.nodejs.org/), [Express](http://expressjs.com/), et [AngularJS](http://angularjs.org/). Le terme "full-stack" indique qu'il permet de gérer l'intégralité des couches de l'application en Javascript, depuis la base de données, en passant par l'API back-end jusqu'au front-end.

## Installation

Afin de me simplifier la vie j'ai l'habitude d'utiliser des solutions pré-packagées pour l'infrastructure de développement ou de production. Pour MEAN.IO j'utilise celles de [Bitnami](https://bitnami.com/stack/mean) disponibles en environnement Windows, Linux Ubuntu ou de type Cloud comme Amazon. Elles ont l'avantage d'intégrer également Apache et un outil d'administration pour MongoDB nommé [RockMongo](https://github.com/iwind/rockmongo). Actuellement je travaille avec Node.js v0.12.4, MongoDB v3.0.3, MEAN.IO v0.5.

L'outil utilisé par MEAN.IO pour exécuter les différentes tâches nécessaires au développement, aux tests et à la mise en production est [gulp](http://gulpjs.com/). Il faut également installer [bower](http://bower.io/) pour la gestion des dépendances côté front-end, npm de Node.js étant utilisé côté back-end. Vous procéderez donc comme suit : 
```
npm install -g gulp
npm install -g bower 
```

Bien qu'il soit possible de cloner directement le dépôt GitHub de MEAN.IO, il est conseillé de passer par l'outil en ligne de commande dédié nommé [mean-cli](https://www.npmjs.com/package/mean-cli) en l'installant via :
```
npm install -g mean-cli
```
Il est ensuite possible d'initialiser une application dans un dossier via les commandes suivantes :
```
mean init folder_name
cd folder_name
npm install
```

> MEAN.IO inclut un script (voir dossier *tools/scripts*) exécuté lors de la commande d'installation qui installe les dépendances back-end et front-end de tous les modules de l'application, il est donc inutile d'exécuter le classique `bower install`

De la même façon il sera possible (nous y reviendrons plus tard) d'initialiser un package (i.e. un module) de l'application dans un dossier via :
```
mean package folder_name
```

Le première chose à faire est de créer une base de données avec un utilisateur ayant les droits d'accès, pour cela utiliser le shell de MongoDB en le lançant via votre compte administrateur de la base de données (configuré à l'installation) :
```
mongo admin --username root --password root
db = db.getSiblingDB('mean-dev')
db.createUser( { user: "mean-dev", pwd: "mean-dev", roles: [ "readWrite", "dbAdmin" ]} )
```
Il faut ensuite modifier la configuration par défaut de MEAN.IO pour utiliser cette base et cet utilisateur, pour cela ouvrir le fichier *development.js* dans le dossier *config/env* et changer la valeur de la clef **db** :
```javascript
module.exports = {
  db: 'mongodb://mean-dev:mean-dev@localhost:27017/mean-dev',
  debug: true,
  ...
```
Pour lancer le serveur exécutez ensuite simplement la commande `gulp` puis connectez-vous avec votre browser à l'adresse [http://localhost:3000/](http://localhost:3000/). Vous pouvez créer votre premier utilisateur pour vous connecter à l'application via l'entrée *Join* dans la barre de menu. A ce stade vous ne devriez avoir que deux entrées accessibles dans la barre de menu en plus du menu associé à votre profil (voir Figure 1). Pour rendre l'utilisateur créé administrateur de l'application utilisez la commande suivante :
```
mean user email@google.com --addRole admin
```
Une fois connecté une nouvelle barre de menu verticale apparait sur la gauche et contient les différentes configurations accessibles en mode administrateur.

![Figure 1](Figure1.png "Figure 1 : page d'accueil de l'application MEAN.IO par défaut une fois loggué (en mode non administrateur le menu vertical sur la gauche n'est pas apparent)")

> **Trucs & Astuces** : exécutez la commande suivante dans le dossier racine de l'application pour obtenir toutes les informations de version sur MEAN.IO (utile par exemple lors de la soumission de bugs sur le tracker) :
> ```
> mean status
> ```

> **Trucs & Astuces** : sous Windows l'imbrication des modules node pose parfois des problèmes de chemins trop longs et il devient impossible d'effacer le dossier *node_modules*, pour cela j'utilise l'outil [rimraf](https://github.com/isaacs/rimraf) :
> ```
> npm install -g rimraf
> rimraf node_modules
> ```

**Parler de Python pour node-gyp**

## Anatomie de l'application

## Anatomie d'un module

## Coeur fonctionnel

### Aggrégation

### Authentification

### Barre de menu

## Conclusion

Nous avons passé en revue dans cet article les grands principes de MEAN.IO. Pour ma part, même s'il est évident que le framework n'est pas encore mature (la version 0.5 courante au moment de rédaction de l'article est annoncée comme la release candidate à la version 1.0), il me semble déjà procurer une bonne base pour structurer des applications web full JavaScript :

 - une architecture modulaire orientée composant (un module est un plugin qui peut être rajouté à l'application sans nécessité de modifier l'existant)
 - une organisation de fichiers à l'intérieur de chaque module qui permet de simplifier le développement en automatisant la déclaration des modèles, des routes et des dépendances
 - une usine logicielle (analyse de code, minification, documentation, etc.)
 - des fonctionnalités de base requises par la plupart des applications (authentification, gestion des rôles, gestion des menus, options de configuration, etc.)
 - un réseau naissant de modules d'extension portés par la communauté
 - tout en étant Open Source il reste porté par une entreprise qui l'utilise pour ses projets internes, ce qui peut éviter un essouflement rapide de la communauté

A noter que le lead développeur historique de MEAN.IO a quitté l'entreprise et créé un fork nommé [MEAN.JS](http://meanjs.org/). A ce stade il reste moins populaire malgré un départ assez fulgurant, le plus inquiétant étant le fait que les commits semblent se faire rare depuis quelques mois. De plus, MEAN.JS sépare les parties back-end et front-end contrairement à l'approche par composant de MEAN.IO, ce qui me parait moins élégant d'un point de vue structure.

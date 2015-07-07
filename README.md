# MEAN.IO (1/3)

Ce premier article d'une série consacrée à MEAN.IO va nous permettre d'introduire les principaux concepts du framework et d'obtenir un environnement de développement opérationnel pour les exercices pratiques des prochains articles. Ce framework commençant à connaitre un certain "essor" (plus de 6 000 recommandations et 2 000 forks sur GitHub) je remercie Programmez! de me laisser l'occasion d'initier une des premières ressources francophones sur le sujet. 

## Introduction

[MEAN.IO](http://mean.io/) est un framework Javascript "full-stack" permettant de créer rapidement une application web de type single-page application (SPA) avec [MongoDB](http://www.mongodb.org/), [Node.js](http://www.nodejs.org/), [Express](http://expressjs.com/), et [AngularJS](http://angularjs.org/) (MEAN). Le terme "full-stack" indique qu'il permet de gérer l'intégralité des couches de l'application en Javascript, depuis la base de données, en passant par l'API back-end jusqu'au front-end. Nous verrons que MEAN.IO est un framework au sens "cadre de travail" pour développer une application plutôt qu'au sens ensemble de librairies/composants.

Bien que cet article aborde l'utilisation des technologies MEAN (et d'autres comme [Mongoose](http://mongoosejs.com/) ou encore [Bootstrap](http://getbootstrap.com/)) au travers de leur utilisation dans MEAN.IO, je vous conseille en prérequis d'acquérir les bases de programmation dans ces différents frameworks/librairies.

## Installation et mise à jour

### Prérequis

Afin de me simplifier la vie j'ai l'habitude d'utiliser des solutions pré-packagées pour l'infrastructure de développement ou de production. Pour MEAN.IO j'utilise celles de [Bitnami](https://bitnami.com/stack/mean) disponibles en environnement Windows, Linux Ubuntu ou de type Cloud comme Amazon. Elles ont l'avantage d'intégrer également Apache, un outil d'administration pour MongoDB nommé [RockMongo](https://github.com/iwind/rockmongo) et [Git](http://git-scm.com/download/) dont vous aurez besoin pour MEAN.IO. Actuellement je travaille avec Node.js v0.12.4, MongoDB v3.0.3, MEAN.IO v0.5.

Certaines dépendances de MEAN.IO utilisent [node-gyp](https://github.com/TooTallNate/node-gyp) (comme les drivers MongoDB) il faut donc disposer en pré-requis de Python version 2.7.x et d'un compilateur C++ comme GCC sous Linux ou Microsoft Visual Studio C++ sous Windows.

L'outil utilisé par MEAN.IO pour exécuter les différentes tâches nécessaires au développement, aux tests et à la mise en production est [gulp](http://gulpjs.com/). Il faut également installer [bower](http://bower.io/) pour la gestion des dépendances côté front-end, npm de Node.js étant utilisé côté back-end. Vous procéderez donc comme suit : 
```
npm install -g gulp
npm install -g bower 
```

Bien qu'il soit possible de cloner directement le dépôt GitHub de MEAN.IO, il est conseillé de passer par l'outil en ligne de commande dédié nommé [mean-cli](https://www.npmjs.com/package/mean-cli) en l'installant via :
```
npm install -g mean-cli
```

### Installation et configuration de MEAN.IO

Il est ensuite possible d'initialiser une application dans un dossier grâce aux commandes suivantes :
```
mean init folder_name
cd folder_name
npm install
```

> MEAN.IO inclut un script (voir dossier *tools/scripts*) exécuté lors de la commande d'installation qui installe les dépendances back-end et front-end de tous les modules de l'application, il est donc inutile d'exécuter le classique `bower install`

De la même façon il sera possible (nous y reviendrons plus tard) d'initialiser un package (i.e. un module) de l'application via :
```
mean package package_name
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
Pour lancer le serveur exécutez simplement la commande `gulp` puis connectez-vous avec votre browser à l'adresse [http://localhost:3000/](http://localhost:3000/). Vous pouvez créer votre premier utilisateur pour vous connecter à l'application via l'entrée *Join* dans la barre de menu. A ce stade vous ne devriez avoir que deux entrées accessibles dans la barre de menu en plus du menu associé à votre profil (voir Figure 1). Pour rendre l'utilisateur créé administrateur de l'application utilisez la commande suivante :
```
mean user email@google.com --addRole admin
```
Une fois connecté une nouvelle barre de menu verticale apparait sur la gauche et contient les différentes options de configuration accessibles en mode administrateur.

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

### Mise à jour de MEAN.IO

Au final le dossier d'une application MEAN.IO est en fait un dépôt Git, puisque la commande `mean init` utilise Git pour installer le code du canevas. Elle créé par défaut un dépôt remote (distant) nommé **upstream**. Pour se maintenir à jour avec la dernière version il suffit donc de faire :
```
git pull upstream master
npm install
```

Maintenir à jour les prérequis peut parfois aider à corriger quelques bugs lors de l'installation :
```
npm update -g gulp
npm update -g bower
```

## Fonctionnement

Le principal objectif de MEAN.IO est de fournir un canevas en terme d’usine logicielle et de structure de base, ainsi qu'un mécanisme d’extension, pour développer une application. Celui-ci passe par le développement d'un package ou d'un module qui est un ensemble de fonctionnalités (services back-end + composants front-end) permettant d’étendre le canevas. Comme il n'est pas forcément nécessaire pour une application donnée, il s’intègre au sein du canevas selon une règle bien définie si besoin tel un "plugin" (i.e. "greffon").

### Environnements d'exécution

De façon classique une application MEAN.IO peut être lancée dans différents environnements :

- **development** : environnement utilisé pendant le développement
- **test** : environnement utilisé pour lancer les tests
- **production** : environnement utilisé pour la production

Pour définir l'environnement il est possible de passer par la variable d'environnement **NODE_ENV** avant de lancer le serveur :
```
set NODE_ENV=production
gulp
```
Il est aussi possible d'invoquer directement le serveur en passant l'environnement en paramètre :
```
gulp production
```

Les tâches par défaut qui sont exécutées par gulp suivant l'environnement sont les suivantes :

- **development** : exécution de [JSHint](http://jshint.com/), [CSSLint](https://github.com/CSSLint/csslint), [Less](http://lesscss.org/), [CoffeeScript](http://coffeescript.org/) sur le code et lancement du serveur en mode debug
- **test** : exécution de [Karma](http://jshint.com/) et de [Mocha](http://mochajs.org/)
- **production** : minification du code CSS/JS des dépendances et lancement du serveur en mode production (multithread)

Le format de sortie des logs côté serveur dépend également de l'environnement, [tiny](https://github.com/expressjs/morgan#tiny) en développement et [combined](https://github.com/expressjs/morgan#combined) en production :
```
// Environnement de développement
GET /system/views/index.html 304 2.379 ms - -
GET /admin/menu/main 304 8.687 ms - -
// Environnement de production
::1 - - [22/Mar/2015:13:13:42 +0000] "GET /modules/aggregated.js?group=header HTTP/1.1" 200 0 "http://localhost:3000/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2272.101 Safari/537.36"
::1 - - [22/Mar/2015:13:13:42 +0000] "GET / HTTP/1.1" 200 - "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2272.101 Safari/537.36"
```

### Anatomie d'une application

Une fois initialisé le dossier d'une application MEAN.IO présente la structure suivante :
```
Application folder
--- bower_components : contient les dépendances Bower (front-end)
--- config : fichiers de configuration (Express, base de données, etc.)
    --- env
    --- middlewares
--- gulp : fichiers décrivant les différentes tâches gulp en fonction de l'environnement
--- logs : contient les logs en sortie de Node.js
--- node_modules : contient les dépendances Node.js (back-end)
--- packages : contient l'ensemble des modules de l'application
    --- core : modules de base inclus avec MEAN.IO
    --- custom : modules spécifiques à la logique applicative
--- tools : 
    --- scripts
    --- test
```

A la racine on trouve tous les fichiers de configuration pour les outils de l'usine logicielle que sont npm, bower, gulp, jshint, karma, protractor, etc. Le répertoire **config** contient l'ensemble des fichiers de configuration, notamment *express.js* et le dossier **middlewares** (logging, etc.) pour Express et le dossier **env** pour les options propres à MEAN.IO. Les options communes à tous les environnements sont stockées dans le fichier **env/all.js**, les options propres à chaque  environnement sont stockées dans un fichier portant le nom de l'environnement dans le dossier **env**. Une liste non exhaustive des options de configuration est la suivante :

- **root** : le chemin vers la racine de l'application
- **db** : l'URL d'accès à la base de données, peut inclure un login/password de la forme *mongodb://login:password@host:port/base*
- **hostname** : le nom de l'hôte
- **http/https.port** : le numéro de port à utiliser via HTTP ou HTTPS
- **logging.format** : format [morgan](https://github.com/expressjs/morgan#predefined-formats) des logs serveur
- **app.name** : le nom de l'application
- **aggregate** : true/false pour activer/désactiver l'aggrégation (utile en mode debug)
- **secret** : clef privée pour la sécurisation via JWT (voir ci-après)
- informations pour se connecter via des réseaux sociaux

### Anatomie d'un module

Le dossier d'un package ou module MEAN.IO présente la structure suivante :
```
Module folder
--- docs : contient la documentation de l'API (back-end)
--- public : partie publique du module sur le site
    --- assets : données statiques (images, css, dépendances front-end)
    --- controllers : controllers front-end (AngularJS)
    --- routes : routing front-end (AngularUI Router)
    --- services : services front-end (AngularJS)
    --- tests : tests front-end (Jasmine)
    --- views : vues (AngularJS)
--- server : contient le code back-end
    --- controllers : controllers back-end (Express)
    --- routes : routing back-end (Express)
    --- models : modèle de données back-end (Mongoose)
    --- tests : tests back-end (Jasmine)
    --- views : vues HMTL (templating Swig)
--- node_modules : contient les dépendances Node.js du module (back-end)
```

A la racine on trouve comme dans le cas de l'application les fichiers de configuration pour npm, bower et MEAN.IO (**mean.json**). Le plus important est le fichier **app.js** qui est le point d'entrée du module. Tous les fichiers à l'intérieur du dossier **public** seront accessibles publiquement à l'URL */nom-module/chemin-relatif-fichier*. Par exemple pour accéder à un contrôleur AngularJS nommé 'controller' dans le module nommé 'module' l'URL sera *module/controllers/controller.js*.

Pour rajouter un module au canevas il suffit de lui donner un nom unique dans l'application et de le copier dans le répertoire **packages/custom** de l'application, MEAN.IO se charge du reste !

### Authentification

Le canevas inclus la gestion des utilisateurs et de leurs rôles. Vous disposez donc d'une page pour enregistrer un nouvel utilisateur, d'une page pour se connecter à l'application, d'une page de réinitialisation du mot de passe (nécessite toutefois de configurer un serveur SMTP) et d'une page d'index accessible de façon publique. Si vous avez le rôle administrateur ('admin') vous héritez aussi d'une IHM de gestion des utilisateurs (ajout, suppression, affectation de rôle). 

Si l'authentification permet d'adpater le contenu de l'application (menus et pages) en fonction du rôle de l'utilisateur, elle sert également à protéger l'accès à l'API côté back-end. Pour ce faire elle se base sur JSON Web Token ([JWT](http://jwt.io/)), qui est une spécification pour l'authentification. Un JWT est un objet JSON que le serveur encode en utilisant une clé privée. L'objet JSON encodé (qui est en fait l'utilisateur dans MEAN.IO) se présente sous la forme d'un token renvoyé au client qui s'est authentifié avec succès. Dans MEAN.IO cela se passe lors de la connexion qui déclenche un appel vers l'URL */api/login* de l'API. Ensuite, grâce à un intercepteur AngularJS, la partie cliente MEAN.IO associera ce token à chaque requête faite au serveur. Si en utilisant sa clé privée le serveur parvient à décoder le token, il s'assure de l'authenticité du client et autorise la requête.

### Aggrégation

Afin de pouvoir rajouter un module au canevas sans devoir modifier un quelconque fichier, MEAN.IO intègre un mécanisme automatique d'aggrégation, tant pour les fichiers applicatifs que pour les dépendances. Celui-ci repose principalement sur du templating côté serveur, par défaut tous les fichiers JS du dossier **public** de chaque module sont aggrégés à l'exception du sous dossier **assets** réservé pour le stockage des librairies externes, des images et des fichiers CSS. En environnement de production les fichiers sont également minifiés.

Concernant les dépendances MEAN.IO offre deux possibilités :

 - l'utilisation du fichier global **config/assets.json** qui contient une liste de fichiers JS/CSS externes
 - l'ajout des fichiers externes directement via le code source de chaque module
 
La première approche est utilisé de façon interne par MEAN.IO pour les dépendances globales du canevas comme AngularJS ou encore jQuery. Rien ne vous empêche de faire de même pour votre application, néanmoins ceci la rendra plus monolithique dans le sens où les dépendances de vos différents modules seront stockées de façon globale. Il deviendra donc impossible d'ajouter ou de supprimer un module simplement en déplaçant son dossier. L'avantage est par contre d'avoir toutes les dépendances localisées à un seul endroit : **bower_components** pour Bower (front-end) et **node_modules** pour Node.js (back-end).

La seconde approche permet à chaque module de déclarer ses dépendances et donc de rester complètement indépendant du canevas. Le chemin des fichiers JS et CSS doit être donné relativement aux dossiers **public/assets/js** et **public/assets/css**. Ceci se fait dans le fichier **app.js** du module :
```javascript
// Ajout d'une librairie externe
Module.aggregateAsset('js','lib.js');

// Ajout du fichier CSS du module
Module.aggregateAsset('css','module.css');
```
Il est possible de contrôler l'aggrégation plus finement si nécessaire
```javascript
// Ajout d'une librairie externe dans le header et avant les suivantes (par défaut le poids vaut 0)
Module.aggregateAsset('js','first.js',{weight: -1, group: 'header'});
// Ajout d'une librairie externe dans le footer
Module.aggregateAsset('js','last.js',{group: 'footer'});
```
Avec cette approche les dépendances d'un module sont généralement installées localement au module. Le script d'installation de MEAN.IO parcours en effet automatiquement tous les modules et exécute à l'intérieur un `npm/bower install`. NPM installera donc les dépendances indiquées du fichier **package.json** dans le dossier **node_modules** et il semble impossible de changer ce comportement par défaut. Néanmoins, comme NPM utilise une stratégie récursive de recherche des modules celà ne pose aucun problème particulier. La stratégie concernant Bower consiste, via un fichier **.bowerrc**, à lui faire installer les dépendances du fichier **bower.json** dans le dossier **public/assets/js**.
```
{
  "directory": "public/assets/lib"
}
```
> **Trucs & Astuces** : Personnellement je préfère installer les dépendances dans le dossier **bower_components** à la racine car comme Bower n'utilise pas une stratégie récursive de recherche contrairement à NPM il est possible qu'une même dépendance utilisée par deux modules différents soit en doublon, causant par la même des problèmes à l'exécution. 

## Contribuer

La société qui a lancé MEAN.IO (Linnovate) a également déployé une infrastructure afin de permettre à la communauté de partager et de mettre à disposition des modules, il s'agit du [MEANetwork](https://network.mean.io). La première chose pour utiliser cette infrastructure est de s'enregistrer via l'outil en ligne de commande :
```
mean register
```
Une fois enregistré vous pouvez publier un module en vous positionnant dans le dossier du module et en exécutant la commande `publish` :
```
cd packages/custom/module
mean publish
```
Lors de la publication d'un module, son code source sera en fait publié sur le service [GitLab](http://git.mean.io) propre au MEANetwork. Pour l'utilisateur nommé 'user' et le module nommé 'package' le dépôt associé sera accessible à l'URL http://git.mean.io/user/package. [GitLab](https://gitlab.com/) est un équivalent Open Source du service [GitHub]
(https://github.com/) qui peut être déployé de façon interne.

## Conclusion

Nous avons passé en revue dans cet article les grands principes de MEAN.IO. Pour ma part, même s'il est évident que le framework n'est pas encore mature (la version 0.5 courante au moment de rédaction de l'article est annoncée comme la release candidate à la version 1.0), il me semble déjà procurer une bonne base pour structurer des applications web SPA full JavaScript :

 - une architecture modulaire orientée composant (un module est un plugin qui peut être rajouté à l'application sans nécessité de modifier l'existant)
 - une organisation de fichiers à l'intérieur de chaque module qui permet de simplifier le développement en automatisant la déclaration des modèles, des routes et des dépendances
 - une usine logicielle (analyse de code, minification, documentation, etc.)
 - des fonctionnalités de base requises par la plupart des applications (authentification, gestion des rôles, gestion des menus, options de configuration, etc.)
 - un réseau naissant de modules d'extension portés par la communauté
 - tout en étant Open Source il reste porté par une entreprise qui l'utilise pour ses projets internes, ce qui peut éviter un essouflement rapide de la communauté

> A noter que le lead développeur historique de MEAN.IO a quitté l'entreprise et créé un fork nommé [MEAN.JS](http://meanjs.org/). A ce stade il reste moins populaire malgré un départ assez fulgurant, le plus inquiétant étant le fait que les commits semblent se faire rare depuis quelques mois. De plus, MEAN.JS sépare les parties back-end et front-end contrairement à l'approche par composant de MEAN.IO, ce qui me parait moins élégant d'un point de vue structure.

Dans les prochains articles nous aborderons la création et le fonctionnement détaillé d'un module MEAN.IO avec un cas d'utilisation concret.



# Introduction à MEAN.IO

Ce premier article d'une série consacrée à MEAN.IO va nous permettre d'introduire les principaux concepts du framework et d'obtenir un environnement de développement opérationnel pour les exercices pratiques des prochains articles.

## Présentation

[MEAN.IO](http://mean.io/), est un framework Javascript "full-stack" permettant de créer rapidement une application web avec [MongoDB](http://www.mongodb.org/), [Node.js](http://www.nodejs.org/), [Express](http://expressjs.com/), et [AngularJS](http://angularjs.org/). Le terme "full-stack" indique qu'il permet de gérer l'intégralité des couches de l'application en Javascript, depuis la base de données, en passant par l'API back-end jusqu'au front-end.

## Installation

Afin de me simplifier la vie j'ai l'habitude d'utiliser des solutions pré-packagées pour l'infrastructure de développement ou de production. Pour MEAN.IO j'utilise celles de [Bitnami](https://bitnami.com/stack/mean) disponibles en environnement Windows, Linux Ubuntu ou de type Cloud comme Amazon. Elles ont l'avantage d'intégrer également un outil d'administration pour MongoDB nommé RockMongo.

L'outil utilisé par MEAN.IO pour exécuter les différentes tâches nécessaires au développement, aux tests et à la mise en production est gulp. Il faut également installer bower pour la gestion des dépendances côté front-end, npm de node.js étant utilisé côté back-end. Vous procéderez donc comme suit : 
```
$ npm install -g gulp
$ npm install -g bower 
```

## Conclusion

Nous avons passé en revue dans cet article les grands principes de MEAN.IO. Pour ma part, même s'il est évident que le framework n'est pas encore mature (la version 0.5 courante au moment de rédaction de l'article est annoncée comme la release candidate à la version 1.0) il me semble déjà procurer une bonne base pour structurer des applications web full JavaScript :

 - une architecture modulaire orientée composant (un module est un plugin qui peut être rajouté à l'application sans nécessité de modifier l'existant)
 - une organisation de fichiers à l'intérieur de chaque module qui permet de simplifier le développement en automatisant la déclaration des modèles, des routes et des dépendances
 - une usine logicielle (analyse de code, minification, documentation, etc.)
 - tout en étant Open Source il reste portée par une entreprise qui l'utilise pour ses projets internes

A noter que le lead développeur historique de MEAN.IO a quitté l'entreprise et créé un fork nommé MEAN.JS. A ce stade il reste moins populaire malgré un départ assez rapide, le plus inquiétant étant le fait que les commits semblent se faire rare depuis quelques temps. De plus, MEAN.JS sépare les parties back-end et front-end contrairement à l'approche par composant de MEAN.IO, ce qui me parait moins élégant.

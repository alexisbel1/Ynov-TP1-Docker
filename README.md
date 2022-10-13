# TP 1 Conteneurisation

Ce TP a pour objectif de vous faire découvrir le fonctionnement des conteneurs Linux à travers Docker.

## Rendus du TP:

- Rendre un fichier contenant les réponses aux questions (format markdown)
- Pour chacune des questions, il faudra veiller à écrire les commandes utilisées permettant de répondre à la question. Ne pas hésiter à ajouter des détails supplémentaires afin de justifier votre réflexion.

## Contexte

Votre objectif sera d'automatiser l'environnement d'exécution d'une application en utilisant la conteneurisation.

## A) Installer Docker  

- Installer Docker en suivant la documentation officielle et s'assurer qu'il est fonctionnel :

```bash
docker run hello-world
```

## B) Déployer la base de données

1) Déployer une base données MongoDB (mode standalone) avec Docker. La base de données devra respecter les conditions suivantes:

- Une connection avec un user et un mot de passe sécurisé. 
- La base de données devra être accessible depuis l'host afin d'y accéder depuis un client MongoDB.

La base de données sera utilisé dans la prochaine section.

2) Télécharger MongoDB Compass (client graphique pour visualiser la base de données) et se connecter à MongoDB.
 
## C) Déploiement de l'API

Le dossier `api` contient le code d'une API REST Nodejs. Celle-ci nécessite une base de données MongoDB afin de fonctionner.

### Setup de l'API

#### Installation de Node et Yarn:

Afin de démarrer l'API il est nécessaire d'installer `node` dans votre environnement. Le plus simple est de passer par `nvm`:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
nvm use node
```

Ces 2 commandes vont respectivement installer `nvm` ainsi que la dernier version de `node`. Vérifier que `node` est fonctionnel avec cette commande:

```bash
node -v
```

Yarn est un package manager permettant de gérer les packages nodejs. Il est nécessaire pour télécharger les dépendances de l'API:
```bash
npm install --global yarn
```

#### Démarrage de l'API

Une fois node et Yarn nstallés, il est nécessaire de télécharger les dépendances de l'API (il faut se situer dans le dossier de l'API):
```bash
yarn install
```

Cela va créer un dossier `node_modules` contenant les dépendances de l'API.

Le code de l'application est du Typescript, hors node n'est capable d'interpréter que du Javascript. Il faut pour cela transpiler le code TS en JS grâce à cette commande:

```bash
yarn build
```
Cela va générer un dossier `dist`contenant le code Javascript.

L'api peut ensuite être démarré grâce à la commande suivante:
```bash
node dist/main.js
```

Il est nécessaire de configurer la connection à la base de données afin que l'API puisse démarrer.

Tester de démarrer l'API sans oublier de configurer la connection à MongoDB. **Il sera nécessaire de chercher dans le code source afin de trouver la manière de s'y connecter. Il ne faudra pas modifier le code source !** 

#### Tester l'API

Une fois démarrée, l'API est accessible à l'adresse suivante : http://localhost:3000.

L'API expose 2 routes:
- GET /users/ --> Liste tous les objets "Users" dans MongoDB
- POST /users/ --> Créer un objet user et le persiste dans MongoDB

Tester les commandes suivantes :

```bash
# Créer un user
curl --location --request POST 'http://localhost:3000/users' \
--header 'Content-Type: application/json' \
--data-raw '{"email":"alexis.bel@ynov.com", "firstName": "Alexis", "lastName": "Bel"}'

# Liste tous les users
curl 'http://localhost:3000/users' 
```

La 2ème commande doit renvoyer le user précédemment créé.

### 1) Conteneurisation de l'API

#### a) Création de l'image

Créer un Dockerfile afin de conteneurisé l'API en prenant en compte ces critères:

- La taille de l'image doit être la plus petit possible. Tips : Penser aux image de base `alpine` qui sont plus légère.
- Ne mettre dans l'image que le strict nécessaire pour faire fonctionner l'application.
- Réduire au maximum les privilèges de l'utilisateur configurer dans l'image (pas de root)
- Tout autres élément permettant d'optimiser ou de rendre plus sécurisé l'image.

Une fois le Dockerfile finalisé, créer l'image.

#### b) Créer le conteneur à partir de l'image.

Créer le conteneur à partir de l'image: 
- S'assurer que l'API est fonctionnelle (n'oubliez pas la connection à la base de données).
- Utiliser un réseau interne partagé entre le conteneur de l'api et celui la base données pour les faire communiquer (ne pas utiliser localhost:27017)

#### c) Partager l'image sur le Dockerhub

Créer vous un compte sur le DockerHub et partagez l'image que vous avez créé.

## D) Persistance des données

### 1) Supprimer le conteneur MongoDB et le récréer. Qu'est il arrivé aux données et pourquoi ?

### 2) Mettre en place un mécanisme permettant de persister les données même quand le conteneur est supprimé (pensez aux volumes).

### E) (Bonus) Réplication de l'API et load balancing avec un proxy NGINX

- Créer plusieurs replica de l'API 
- Créer un conteneur NGINX en front gérant les requêtes vers l'API et permettant de load balancer les requêtes vers les différents replica de l'API, le tout à partir d'un même hostname.
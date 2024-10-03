![Docker memo](https://upload.wikimedia.org/wikipedia/commons/7/70/Docker_logo.png "Docker Guide")
# Docker memo 
## Intro
Ce guide est un guide et un rappel d'utilisation de Docker sous Linux (evidemment)

Les points abordés sont les suivants:
* Commandes de base
* Créer ses images avec dockerfile
* docker compose
-----------

# Commandes de base
## Images
* Lister les images:
```bash
➜ docker images
➜ docker images --tree    #better view
```
* Rechercher des images:
```bash
➜ docker search TERMS
➜ docker search --filter is-official=true ubuntu  #Official only
```
* Télécharger des images:
```bash
➜ docker pull demolearn/hello_world
```
* Uploader des images:
```bash
➜ docker push hello_world
```
* Supprimer des images:
```bash
➜ docker rmi hello_world
➜ docker rmi 4b73e0bb9c5d     #image id
```

## Containers
* Lancer une image dans un conteneur:
```bash
➜ docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
➜ docker run hello-world
➜ docker run -d hello-world   #detach:lancé en arriere-plan
➜ docker run -d -p 8080:80 nginx  #pont 127.0.0.1:8080
```
* Liste des conteneurs:
```bash
➜ docker ps       #conteneurs en cours d'execution
➜ docker ps -a    #tous les conteneurs  
```
* Arreter des conteneurs:
```bash
➜ docker stop tutorial3_redis_1   #name
➜ docker stop dea6d44e53ec        #conteneur id
➜ docker kill dea6d44e53ec        #force la destruction d'un contener
```
* Démarrer des conteneurs arretés:
```bash
➜ docker start tutorial3_redis_1   #name
➜ docker start dea6d44e53ec        #conteneur id
```
* Supprimer des conteneurs (arretés):
```bash
➜ docker rm tutorial3_redis_1   #name
➜ docker rm dea6d44e53ec        #conteneur id
```
* Executer des commandes dans un conteneurs:
```bash
➜ docker exec dea6d44e53ec whoami
➜ docker exec -ti dea6d44e53ec bash #-t:tty -i:interactif
```
## Divers
* Purge des ressources:
```bash
➜ docker system prune

WARNING! This will remove:
- all stopped containers
- all networks not used by at least one container
- all dangling images
- all dangling build cache
Are you sure you want to continue? [y/N]
```

-----------

# Créer ses images avec Dockerfile

## Prérequis:
* créer un dossier pour votre image
* créer dans ce dossier un ficheir nommé "_Dockerfile_"

## Contenu du fichier _Dockerfile_:

```Dockerfile
FROM debian:9

RUN apt-get update -yq \
&& apt-get install curl gnupg -yq \
&& curl -sL https://deb.nodesource.com/setup_10.x | bash \
&& apt-get install nodejs -yq \
&& apt-get clean -y

ADD . /app/
WORKDIR /app
RUN npm install

EXPOSE 2368
VOLUME /app/logs

CMD npm run start
```

**Détails:**

| Commande  | <div style="text-align:left;">Description</div>  |
| ------------- |:-------------:|
| **FROM**      | <div style="text-align:left;">Image que vous allez utiliser comme base. utilisable qu'une seule fois dans un Dockerfile</div>    |
| **RUN**      | <div style="text-align:left;">exécuter une commande dans votre conteneur</div>     |
| **ADD**      | <div style="text-align:left;">copier ou de télécharger des fichiers dans l'image</div>     |
| **WORKDIR**      | <div style="text-align:left;">modifier le répertoire courant</div>     |
| **EXPOSE**      | <div style="text-align:left;">indique le port d'écoute de votre application</div>     |
| **VOLUME**      | <div style="text-align:left;">Indique quel répertoire à partager avec votre host</div>     |
| **CMD**      | <div style="text-align:left;">commande à exécuter lors du démarrage du conteneur</div>     |

## Fichier  "_.dockerignore_"
Permer d'ignorer certains fichiers du build
```.dockerignore
node_modules
.git
template.yaml
```

## Build et lancement d'une image
Se placer dans le bon dossier (voir prérequis precedemment)
```bash
docker build -t nomImage .
docker run -d -p 2368:2368 nomImage
```

**Détails:**

| Commande  | <div style="text-align:left;">Description</div> |
| ------------- |:-------------:|
|**build**|<div style="text-align:left;">Construit l'image</span>|
|**-t**|<div style="text-align:left;">Donner un nom à votre image</span>|
|**.**|<div style="text-align:left;">Répertoire où se trouve le Dockerfile</span>|
|**run**|<div style="text-align:left;">Lance l'image dans un conteneur</span>|
|**-d**|<div style="text-align:left;">détacher le conteneur</span>|
|**-p 2368:2368**|<div style="text-align:left;">Pont 127.0.0.1:2368</span>|

## Envoyez votre image sur le Docker Hub

Se placer dans le bon dossier (voir prérequis precedemment)
```bash
docker push
```

-----------

# Docker Compose

## Installation:
```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.23.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose && sudo chmod +x /usr/bin/docker-compose
```
**Check installation:**
```bash
docker-compose --version
```

## Commandes docker-compose
```bash
docker-compose COMMANDE
```

| COMMANDE  | <div style="text-align:left;">Description</div> |
| ------------- |:-------------:|
|**pull**|<div style="text-align:left;">Telecharge une image</span>|
|**up**|<div style="text-align:left;">Démarrer une stack (-d: détach)</span>|
|**down**|<div style="text-align:left;">Supprimer l'ensemble de la stack</span>|
|**ps**|<div style="text-align:left;">Liste des stacks</span>|
|**logs**|<div style="text-align:left;">Logs d'une stack (docker-compose logs -f --tail 5)</span>|
|**stop**|<div style="text-align:left;">Arreter les conteneurs d'une stack</span>|
|**config**|<div style="text-align:left;">valider la syntaxe de votre fichier docker-compose.yml</span>|

## Parametres du fichier "_docker-compose.yml_":

| COMMANDE  | <div style="text-align:left;">Description</div> |
| ------------- |:-------------:|
|**image**| <div style="text-align:left;">Spécifie l'image source pour le conteneur</div>|
|**build**| <div style="text-align:left;">Spécifie le Dockerfile source pour créer l'image du conteneur</div>|
|**volume**| <div style="text-align:left;">Spécifie les points de montage entre le système hôte et les conteneurs</div>|
|**restart**| <div style="text-align:left;">Définit  le comportement du conteneur en cas d'arrêt du processus</div>|
|**environment**| <div style="text-align:left;">Définit  les variables d’environnement</div>|
|**depends_on**| <div style="text-align:left;">Définit  le conteneur dépend d'un autre conteneur</div>|
|**ports**| <div style="text-align:left;">Définit ports disponibles entre la machine host et le conteneur</div>|

## EXEMPLES de fichiers "_docker-compose.yml_"

### NGINX + WORDPRESS + PVC :
Ce fichier "_docker-compose.yml_" est un exemple de serveur web avec wordpres et base de donnée (volume persistant)

**_docker-compose.yml_:**
```yaml
version: '3'
services:
  db:
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: somewordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
    
  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    ports:
      - "8000:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress

volumes:
  db_data: {}
```


### docker-compose + Dockerfile  :
Combninaison d'images persos et stacking

**_docker-compose.yml_:**
```yaml
version: "3"
services:
  redis:
    image: redis
    ports:
      - 6379:6379
  api:
    image: my_tuto3_api
    ports:
      - 5000:5000
    depends_on:
      - redis
```

**_Dockerfile_:**
```yaml
# base image
FROM python:3.8-alpine3.14

# copy just the requirements.txt first to leverage Docker cache
# install all dependencies for Python app
COPY ./requirements.txt /app/requirements.txt

WORKDIR /app

# install dependencies in requirements.txt
RUN pip install -r requirements.txt

# copy all content to work directory /app
COPY . /app

# specify the port number the container should expose
EXPOSE 5000

# run the application
CMD ["python", "/app/main.py"]
```

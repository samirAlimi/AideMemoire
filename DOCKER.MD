# DOCKER - AIDE MEMOIRE


## Les commandes basiques

Ce tableau résume les commandes de docker avec leur descriptif.
Pour plus d'informations, veuillez consulter la docummentation sur le site [Documentation Docker](https://docs.docker.com/engine/reference/commandline/docker/)
Commande | Description
------------ | -------------
docker ps -a | Afficher tous les conteneurs
docker ps | Afficher tous les conteneurs en cours d'exécution
docker rmi <IMAGE> | Supprimer un conteneur
docker run <IMAGE> | Pour lancer un conteneur
docker run -ti <IMAGE> | les drapeaux -ti permettent de modifier le conteneur
docker diff <ID CONTAINER> | 1. on récupère l'id du conteneur que l'on a modifié avec **docker ps -a**<br>2. Permet d'obtenir une liste des différentes modifications effectuées
docker commit <ID CONTAINER> <IMAGE> | Valider les modifications effectuées
docker save <IMAGE> **>** <CHEMIN_DESTINATION/IMAGE.tar> | Sauvegarder l'image dans la destination
docker tag <IMAGE **OR** ID> <HOSTNAME **OR** IP **:** PORT **/** CHEMIN_DESTINATION> **:** <FLAG> | Mettre un tag sur l'adresse de destination faisant référence à l'image locale
docker push <HOSTNAME **OR** IP **:** PORT **/** CHEMIN_DESTINATION> **:** <FLAG> | Publier l'image sur l'adresse de destination

## Exemples

Les exemples qui suivent mettre en pratiques les commandes ci dessus et quelques utilisations de Docker

### Un cas simple

Imaginons que nous voulons installer et utiliser un conteneur pour faire fonctionner Ghost (une plateforme de blogging open source basée sur nodejs).

On vérifie sur le hub si quelqu'un a déjà cherché à construire une image pour cette technologie<br>
  **docker run --rm -p 8080:2368 --name app-blog ghost**
  * **--rm** permet de supprimer le conteneur dès qu'il est fermé
  * **--name** permet de donner un nom à notre conteneur pour le retrouver plus facilement par la suite
  * **-p** permet de mapper un port local sur un port du conteneur

Cette commande lancée nous permet de contruire notre conteneur et d'y travailler. En revance, dès que nous allons fermer notre conteneur nos données vont disparaître, car un nouveau run aura pour effet de repartir de l'image sans conserver nos éventuelles modifications. Pour remédier à ce problème il est possible de relier un dossier de la machine hôte à un dossier à l'intérieur du conteneur à l'aide de l'**option -v**. Ceci va donner :<br>
  **docker run --rm -p 8080:2368 -v /chemin/vers/app-blog:/var/lib/ghost --name app-blog ghost**

De cette manière les données seront stockées sur la machine qui fait fonctionner docker et elles seront ainsi conservées à chaque fois que l'on exécutera un nouveau run de notre image.

### Faire un push sur un serveur

Récuperer l'ID du conteneur<br>
  **docker ps -a**<br>
Valider toutes les modifications effectuées sur le conteneur dans une nouvelle image<br>
  **docker commit c16378f943fe app-blog**<br>
Mettre le tag (version) sur le conteneur pour qu'il pointe sur le serveur distant<br>
  *A noter que le nom de l'image et le tag sont en minuscules*<br>
  **docker tag app-blog local.dev:8286/app-blog:v1**<br>
Publier le container<br>
  **docker push local.dev:8286/app-blog:v1**

### Faire un push ou un pull sur Docker Hub

Docker Hub est l'endroit où sont stockées les versions libres des images Docker. Lorsque nous avons exécuté notre première image en tapant

#### Importer (pull) une image de Docker Hub

Vérifier si l'image existe et le supprimer, le cas échéant<br>
  **docker run --rm -p 8080:2368 --name app-blog ghost**<br>
Importer l'image<br>
  **docker pull ghost**

#### Exporter (push) une image sur Docker Hub

Se connecter sur [Docker Hub](https://hub.docker.com/)<br>
  **docker login --username=yourhubusername --email=votre_email@company.com**<br>
  **Saisir votre mot de passe**<br>
Trouver l'ID ou le nom du conteneur<br>
  **docker ps -a**<br>
Valider toutes les modifications effectuées sur le conteneur dans une nouvelle image<br>
  **docker commit c16378f943fe app-blog**<br>
Mettre un tag sur votre conteneur<br>
  **docker tag app-blog votrehubusername/app-blog:firsttry**<br>
Publier le container sur docker hub<br>
  **docker push votrehubusername/app-blog**

#### Faire une sauvegarde en local du conteneur

Trouver l'ID ou le nom du conteneur<br>
  **docker ps -a**<br>
Valider toutes les modifications effectuées sur le conteneur dans une nouvelle image<br>
  **docker commit c16378f943fe app-blog**<br>
Enregistrer en local<br>
  **docker save app-blog > app-blog.tar**<br>
Charger un conteneur local<br>
  **docker load --input app-blog.tar**

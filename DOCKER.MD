# DOCKER - AIDE MEMOIRE

Ceci est un aide mémoire sur l'utilisation de DOCKER

## Les commandes basiques

Ce tableau résume les commandes de docker avec leur descriptif.
Pour plus d'informations, veuillez consulter la docummentation sur le site [Documentation Docker](https://docs.docker.com/engine/reference/commandline/docker/)
Commande | Description
------------ | -------------
docker ps -a | Afficher tous les conteneurs
docker ps | Afficher tous les conteneurs en cours d'exécution
docker rmi *nom_image* | Supprimer un conteneur
docker run *nom_image* | Pour lancer un conteneur
docker run -ti *nom_image* | les drapeaux -ti permettent de modifier le conteneur
docker diff *n°_image* | 1. on récupère l'id du conteneur que l'on a modifié avec **docker ps -a**<br>2. Permet d'obtenir une liste des différentes modifications effectuées
docker commit *n°_image* *nom_image* | Valider les modifications effectuées
docker save *nom_image* **>** *chemin_destination/nom_image.tar* | Sauvegarder l'image dans la destination
docker tag *nom_image* **ou** *n°_image* *nom_serveur **ou** ip_serveur **:** n°_port **/** chemin_destination> **:** flag* | Mettre un tag sur l'adresse de destination faisant référence à l'image locale
docker push *nom_serveur **ou** ip_serveur **:** n°_port **/** chemin_destination> **:** flag* | Publier l'image sur l'adresse de destination

## Exemples

Les exemples qui suivent mettre en pratiques les commandes ci dessus et quelques utilisations de Docker

### Un cas simple

Imaginons que nous voulons installer et utiliser un conteneur pour faire fonctionner Ghost (une plateforme de blogging open source basée sur nodejs).

On vérifie sur le hub si quelqu'un a déjà cherché à construire une image pour cette technologie :
  * **docker run --rm -p 8080:2368 --name app-blog ghost**
    * **--rm** permet de supprimer le conteneur dès qu'il est fermé
    * **--name** permet de donner un nom à notre conteneur pour le retrouver plus facilement par la suite
    * **-p** permet de mapper un port local sur un port du conteneur

Cette commande lancée nous permet de contruire notre conteneur et d'y travailler. En revance, dès que nous allons fermer notre conteneur nos données vont disparaître, car un nouveau run aura pour effet de repartir de l'image sans conserver nos éventuelles modifications. Pour remédier à ce problème il est possible de relier un dossier de la machine hôte à un dossier à l'intérieur du conteneur à l'aide de l'**option -v**.<br>Ceci va donner :
  **docker run --rm -p 8080:2368 -v /chemin/vers/app-blog:/var/lib/ghost --name app-blog ghost**

De cette manière les données seront stockées sur la machine qui fait fonctionner docker et elles seront ainsi conservées à chaque fois que l'on exécutera un nouveau run de notre image.

### Faire un push sur un serveur

  * Récuperer l'ID du conteneur :
    * **docker ps -a**
  * Valider toutes les modifications effectuées sur le conteneur dans une nouvelle image :
    * **docker commit c16378f943fe app-blog**
  * Mettre le tag (version) sur le conteneur pour qu'il pointe sur le serveur distant :
    * **docker tag app-blog local.dev:8286/app-blog:v1**
    * *A noter que le nom de l'image et le tag sont en minuscules*
  * Publier le container :
    * **docker push local.dev:8286/app-blog:v1**

### Faire un push ou un pull sur Docker Hub

Docker Hub est l'endroit où sont stockées les versions libres des images Docker. Lorsque nous avons exécuté notre première image en tapant

#### Importer (pull) une image de Docker Hub

  * Vérifier si l'image existe et le supprimer, le cas échéant :
    * **docker run --rm -p 8080:2368 --name app-blog ghost**
  * Importer l'image :
    * **docker pull ghost**

#### Exporter (push) une image sur Docker Hub

  * Se connecter sur [Docker Hub](https://hub.docker.com/) :
    * **docker login --username=yourhubusername --email=votre_email@company.com**
    * **Saisir votre mot de passe**
  * Trouver l'ID ou le nom du conteneur :
    * **docker ps -a**
  * Valider toutes les modifications effectuées sur le conteneur dans une nouvelle image :
    * **docker commit c16378f943fe app-blog**
  * Mettre un tag sur votre conteneur :
    * **docker tag app-blog votrehubusername/app-blog:firsttry**
  * Publier le container sur docker hub :
    * **docker push votrehubusername/app-blog**

#### Faire une sauvegarde en local du conteneur

  * Trouver l'ID ou le nom du conteneur :
    * **docker ps -a**
  * Valider toutes les modifications effectuées sur le conteneur dans une nouvelle image :
    * **docker commit c16378f943fe app-blog**
  * Enregistrer en local :
    * **docker save app-blog > app-blog.tar**
  * Charger un conteneur local :
    * **docker load --input app-blog.tar**

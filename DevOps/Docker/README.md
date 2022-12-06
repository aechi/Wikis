# Table of contents
1. [Installing Docker](#install)
2. [Use the Docker command line](#cmdline)


## Installing Docker <a name="install"></a>


1. Step 1

Installing Docker  À partir de l’adresse <https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-20-04> 
	1- sudo apt update
	2- sudo apt install apt-transport-https ca-certificates curl software-properties-common
	3- curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
	4- sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
	5- apt-cache policy docker-ce
	6- sudo apt install docker-ce
	7- sudo systemctl status docker

3. Step 2

Executing the Docker Command Without Sudo (Optional)

sudo usermod -aG docker ${USER}
su - ${USER}

## Use the Docker command line <a name="cmdline"></a>
The first paragraph text


Démarrer Docker 

You can search for images available on Docker Hub by using the docker command with the search subcommand. For example, to search for the Ubuntu image, type:

$ docker search ubuntu

Execute the following command to download the official ubuntu image to your computer:

$ docker pull ubuntu
To see the images that have been downloaded to your computer, type:

$ docker images
The output will look similar to the following:

Output
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              1d622ef86b13        3 weeks ago         73.9MB
hello-world         latest              bf756fb1ae65        4 months ago        13.3kB

Run a container using the latest image of Ubuntu. The combination of the -i and -t switches gives you interactive shell access into the container:

$ docker run -it ubuntu

Your command prompt should change to reflect the fact that you’re now working inside the container and should take this form:

Output
root@d9b100f2f636:/#

Note the container id in the command prompt. In this example, it is d9b100f2f636. You’ll need that container ID later to identify the container when you want to remove it.
Now you can run any command inside the container. For example, let’s update the package database inside the container. You don’t need to prefix any command with sudo, because you’re operating inside the container as the root user:

root@d9b100f2f636:/# apt update
 
Then install any application in it. Let’s install Node.js:

root@d9b100f2f636:/#  apt install nodejs
 
This installs Node.js in the container from the official Ubuntu repository. When the installation finishes, verify that Node.js is installed:

root@d9b100f2f636:/#  node -v
 
You’ll see the version number displayed in your terminal:

Output
v10.19.0
Any changes you make inside the container only apply to that container. To exit the container, type exit at the prompt.
Let’s look at managing the containers on our system next.


After using Docker for a while, you’ll have many active (running) and inactive containers on your computer. To view the active ones, use:

$ docker ps


To view all containers — active and inactive, run docker ps with the -a switch:

$ docker ps -a
To view the latest container you created, pass it the -l switch:
	
$ docker ps -l
	
To start a stopped container, use docker start, followed by the container ID or the container’s name. Let’s start the Ubuntu-based container with the ID of 1c08a7a0d0e4:

$ docker start 1c08a7a0d0e4

To stop a running container, use docker stop, followed by the container ID or name. This time, we’ll use the name that Docker assigned the container, which is quizzical_mcnulty:

$ docker stop quizzical_mcnulty
$ docker rm youthful_curie

Committing Changes in a Container to a Docker Image
When you start up a Docker image, you can create, modify, and delete files just like you can with a virtual machine. The changes that you make will only apply to that container. You can start and stop it, but once you destroy it with the docker rm command, the changes will be lost for good.

After installing Node.js inside the Ubuntu container, you now have a container running off an image, but the container is different from the image you used to create it. But you might want to reuse this Node.js container as the basis for new images later. Then commit the changes to a new Docker image instance using the following command.

$ docker commit -m "What you did to the image" -a "Author Name" container_id repository/new_image_name

For example, for the user sammy, with the container ID of d9b100f2f636, the command would be:

$ docker commit -m "added Node.js" -a "sammy" d9b100f2f636 sammy/ubuntu-nodejs

Listing the Docker images again will show the new image, as well as the old one that it was derived from:

	• docker images
You’ll see output like this:

Output
REPOSITORY               TAG                 IMAGE ID            CREATED             SIZE
sammy/ubuntu-nodejs   latest              7c1f35226ca6        7 seconds ago       179MB
...
To push your image, first log into Docker Hub.

$ docker login -u docker-registry-username

Then you may push your own image using:

$ docker push docker-registry-username/docker-image-name

Créer une image avec un Dockerfile

Nous avons toutes les lignes pour faire notre Dockerfile. Le voilà en version complete :

FROM debian:wheezy # indique la distribution de départ

# Nomme la personne qui à écrit ce Dockerfile 
MAINTAINER Baptiste Donaux <bdonaux@wanadev.fr>

#  Recherche les paquets disponibles et j'installe Nginx.
RUN apt-get update \
    && apt-get install -y \
        nginx
#  Copie successivement les configurations et scripts de mon système hôte vers mon image 
COPY nginx.conf /etc/nginx/nginx.conf
COPY service_start.sh /home/docker/script/service_start.sh

# J'applique les droits pour exécuter mon script
RUN chmod 744 /home/docker/script/service_start.sh

# Je définie un point d'entrée : le premier script qui va se lancer au démarrage du container

ENTRYPOINT /home/docker/script/service_start.sh

# Le dossier dans lequel je serai quand j'exécuterai un nouveau container sera WORKDIR
WORKDIR /home/docker


Construire une image depuis un Dockerfile

$ docker build /path/to/Dockerfile 

$ docker-commit  # permettre de gérer des états de vos images en sauvegardant les changements d'un container dans une nouvelle image

$ docker-history # voir toutes les étapes de la construction d'une image

$ docker build --tag="myImage[:myTag]"
Comment nommer vos images :
	1. Donner un nom à la construction de l'image avec l'option --tag
	2. Utiliser la commande docker-tag
	3. Nommer votre image au moment de l'utilisation de docker-commit


Déclarer un container

$ docker run debian:wheezy # Exécuter un container depuis une image Docker
# Si vous avez l'impression que cette commande n'a rien fait, vous pouvez voir avec la commande

$ docker ps -l

CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
9b4841d73b6e        debian:7            "/bin/bash"         3 seconds ago       Exited (0) 3 seconds ago                       furious_hopper
Ce retour indique qu'un container nommé "furious_hopper" identifié par l'ID 9b4841d73b6e, a lancé la commande /bin/bash depuis une image debian:7 et s'est terminé avec le code retour 0.

# Vous pouvez d'ailleurs afficher tous les containers (ceux en cours et ceux stoppés) en utilisant la commande 
$ docker ps -a

# Pour interagir avec notre container utiliser:
	• L'option --tty permet d'attacher la console à notre console actuelle et de ne pas perdre le focus. C'est grâce à cette option que votre container ne va pas se terminer.
	• L'option --interactive vous permet de dialoguer avec votre container. Sans cette option, tout ce que vous taperez dans votre console ne sera pas transmis au bash du container.

$ docker run --tty --interactive debian:7


Détaillons le fonctionnement de "docker run"

Que fait vraiment le "docker run" ? Cette commande exécute successivement deux autres commandes auxquelles vous avez accès : "docker create" et "docker start". 

$ docker-create # Avec cette commande, vous allez créer un container à partir d'une image en lui donnant des paramètres d'exécution ( mappage des ports , partage des dossiers, … )
$ docker create --tty --interactive --name="wonderful_mobidock" debian:7 # Le retour de cette commande devrait être une suite indigeste de caractères (indiquant l'ID donné à votre container).
Notre container est désormais créé grâce à la commande "docker create". D'ailleurs en faisant un "docker ps -a" vous devriez le trouver.
 Maintenant que vous avez créé votre container et que vous l'avez configuré, vous pouvez le lancer avec la commande "docker start".

$ docker start wonderful_mobidock
Par défaut, "docker start" ne vous attache pas la console, mais vous pouvez le spécifier avec l'option --attach.
$ docker start --attach wonderful_mobido

Ré-utiliser un container

La commande "docker stop" permet d'éteindre proprement un container (l'utilisation de "docker kill" doit être limitée).



Volumes ou bind mounts ?
Afin de faire un choix entre les deux possibilités, gardez les éléments suivants en tête :
	• Si vous montez un  vide dans le répertoire d'un conteneur existant, ces fichiers seront copiés dans votre volume. De même, si vous démarrez votre conteneur avec un volume vers un dossier qui n'existe pas. Celui-ci sera créé automatiquement. 
	• Un point de montage de type bind dans un dossier existant sur votre conteneur, va surcharger ce dossier avec le contenu de votre dossier hôte ( c'est finalement très similaire au principe du /mnt sous linux ). 

## Créer une volume
docker volume create <VOLUME NAME>
# Lister les volumes
docker volume ls
## Supprimer un ou plusieurs volume(s)
docker volume rm <VOLUME NAME>
    -f ou --force : forcer la suppression
## Récolter des informations sur une volume
docker volume inspect <VOLUME NAME>
## Supprimer tous les volumes locaux non inutilisés
docker volume prune
    -f ou --force : forcer la suppression
## Supprimer un conteneur Docker avec le/les volumes associés
docker rm -v <CONTAINER_ID ou CONTAINER_NAME>
    -f ou --force : forcer la suppression
    -v ou --volume : supprime les volumes associés au conteneur



## La commande suivante va créer et monter le volume data-test dans le dossier /data du conteneur. 
$ docker run -ti --name vtest_c -v data-test:/data vtest


##   La commande suivante va créer et monter $PWD/HelloWorld dans le dossier /data du conteneur. 
docker run -ti -v <Host_dir>:<container_dir> image_name

$ docker run -ti --name vtest_c -v  $PWD/HelloWorld:/data vtest


Utilisation d'un registre privé
Coté client mettre a jours le certificat d'AC:
# openssl s_client -connect jenkins-energie.actiatelecom.fr:5001 2>/dev/null </dev/null |  sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > /usr/local/share/ca-certificates/jenkins-energie.actiatelecom.fr.crt
# update-ca-certificates
# systemctl restart docker

À partir de l’adresse <https://wiki-energie.actiatelecom.fr/doku.php?id=r_d:outils:virtualization:tools:tutorialdocker> 

Jenkins Docker permission denied error
There’s an easy fix to the “Permission denied while trying to connect to the Docker daemon socket” error you encounter when you run a Jenkins build or a pipeline’s Jenkinsfile that accesses a Docker image. It’s just a single terminal command and then a reboot:
sudo usermod -a -G docker jenkins

À partir de l’adresse <https://www.theserverside.com/blog/Coffee-Talk-Java-News-Stories-and-Opinions/Fix-Jenkins-Docker-Permission-denied-daemon-error> 



Configure VLAN 10 : 10.0.10.1/24 and 2001:db8:babe:10::1/64 
Configure VLAN 20 : 10.0.20.1/24 and 2001:db8:babe:20::1/64


Create two macvlan networks, one for each VLAN:

docker network create -d macvlan 
    --subnet=10.18.12.0/24 --gateway=10.18.12.31 
    --subnet=2001:db8:babe:10::/64 --gateway=2001:db8:babe:10::1 
    -o parent=eth0.10
    --ipv6 
    macvlan10
	
docker network create -d macvlan 
    --subnet=10.0.20.0/24 --gateway=10.0.20.1 
    --subnet=2001:db8:babe:20::/64 --gateway=2001:db8:babe:20::1 
    -o parent=eth0.20
    --ipv6 
    macvlan20

macvlan10 network uses sub interface eth0.10 as a parent. macvlan20 network uses sub interface eth0.20. 



docker run \
  --name='container0' \
  --hostname='container0' \
  --net=macvlan10 \
  --ip=10.0.10.2 \
  --ip6=2001:db8:babe:10::2 \
  --detach=true \
  phusion/baseimage:latest
  
docker run \
  --name='container2' \
  --hostname='container2' \
  --net=macvlan20 \
  --ip=10.0.20.4 \
  --ip6=2001:db8:babe:20::4 \
  --detach=true \
  phusion/baseimage:latest

![image](https://user-images.githubusercontent.com/86666168/205909278-31ea17c0-c2e9-424d-aafb-14a422261dc5.png)

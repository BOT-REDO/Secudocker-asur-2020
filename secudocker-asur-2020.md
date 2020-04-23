# secudocker-asur-2020
arbaud-avellan-balbasrix-bernard


## 6 Les risques identifiés dans docker.

- La prise de contrôle du container.
- L'épuisement des ressources du container et de l'hôte par déni de service.
- L'attaque du Daemon Docker.
- Image docker du Docker Hub corrompu

Pour nous permettre de sécuriser tout ça nous allons voir la sécurisation des containers docker.

## 7 Utilisation des namespaces par Docker.

### Qu'est ce que c'est ?
Les namespaces sont les élément essentiels qui permet au conteneur d'être un espace de travail isolé.
à chaque fois que l'on crée un container, docker cree un namespace

Ils se représentent de cette façon.


- pid namespace: isolation du processus (PID: ID de processus).

- net namespace: gestion des interfaces réseau (NET: Networking).

- ipc namespace: gestion de l'accès aux ressources IPC (IPC: InterProcess Communication).

- mnt namespace: gestion des points de montage du système de fichiers (MNT: montage).

- uts namespace: isoler les identifiants du noyau et de la version. (UTS: Unix Timesharing System).




### A quoi ça sert ? 
Concrètement les namespaces permettent de partitionner les ressources du noyau de la maniere à ce qu'un ensemble de processus voit un ensemble de ressources isolées en groupes, les ressources peuvent exister sur plusieurs namespaces.


## 8 Sécurisation des containers docker.

### Qu'est ce que c'est ?

Lorsque l’on utilise des containers, la sécurité traditionnelle ne suffit pas. On ne sécurise pas une infrastructure containérisée comme on sécurise son infrastructure virtualisée. 

Il est donc important de bien comprendre les enjeux de la sécurité dans une infrastructure containérisée. 


### 8.1 Prise de contrôle du container avec des capabilities permettant une escalade de privilèges.

Par défaut un container na pas de privilège. Par exemple, nous ne pouvons pas executer un démon docker dans un conteneur docker. Il faut donc ajouter des capabilities pour permettre de contrôler ou avoir des droits sur un container. 


### 8.2 Lutte contre l’épuisement des ressources du container et de l’hôte par déni de service local ( "fork bomb" par exemple ).

Risque de sécurité : L’épuisement des ressources

En ce qui concerne les resources du containers et de l'hote, elles sont soumises a un controle CGROUPS, Ces CGROUPS ont accès au CPU, Memoire, Disque.Pour eviter des soucis il faut limiter les ressources utilisés par le container sur l'hote grace au CGROUPS.

L'épuisement des ressources du container et de l'hôte par déni de service d'un utilisateur local ("fork bomb" par exemple ) ou à distance ( Surcharges dues à des requêtes sur un serveur web ou l'ouverture abusive de connexions TCP.)


### 8.3 Attaque sur le daemon Docker par un utilisateur local à l’hôte. (Un utilisateur appartenant au groupe docker doit être considéré comme ayant les droits root sur l’hôte).

Risque de sécurité : L'attaque du "daemon Docker"

L'attaque du daemon Docker par un utilisateur local à l'hôte. Un utilisateur appartenant au groupe docker doit être considéré comme ayant les droits root sur l'hôte. Un utilisateur du groupe docker peut accéder ainsi accéder à n'importe quel fichier sur l'hôte et les exécuter en tant que root

Solutions pour contrer ce risque : 
Limiter le mode privilégié à certains conteneurs. 
Seuls les users de confiance doivent être autorisés à contrôler les demons docker. 

### 8.4 Utilisation de AppArmor afin de contrôler un container vulnérable à ShellShock.

AppArmor est un logiciel libre de sécurité sous Linux.

Il permet à l'administrateur système d'associer à chaque logiciel un profil qui va lui permettre de restreindre les actions du logiciel en question notament la faille dans le shell Unix Bash Shellshock.

Cette faille peut être utilisée dans plusieurs cas particuliers :

- Serveur Web
- Serveur DHCP
- Serveur de messagerie
- Serveur SSH


### 8.5 Utilisation de SecComp pour limiter l’utilisation de certains appels systèmes.

SecComp est une fonctionnalité de sécurité informatique du noyau Linux.

Il permet à un processus d'effectuer une transition vers un état de sécurité dans lequel il ne peut plus effectuer d'appel système excepté exit(), sigreturn(), read() and write() sur des descripteurs déjà ouvert.

### 8.6 Container en lecture seule.

Docker permet aussi de mettre en lecture seule le container tout en laissant certaines directories modifiables ce qui peut peut être utile par exemple pour des containers frontaux web qui n'ont pas de besoins en écriture.

### 8.7 Signature des images.

Les images sur le docker hub peuvent être signées ce qui va permettre de vérifier qu'elles n'ont pas été altérées lors de la commande docker pull.
Docker permet aussi de faire un scan des vulnérabilitées mais nécessite un compte payant.

## Rootless

Le rootless est un mode qui permet d'executer un deamon docker et les containers en tant qu'utilisateur non root ( dont le root-less ) pour eviter les problemes de failles potentielles du démon et du conteneur.

Le rootless sans racine ne requiert donc pas les  privilèges de l'utilisateur root, on pourrait croire que ceci poserait probleme lors de l'installation mais si les conditions préalables sont remplies ceci sera bon .

Il execute le deamon docker et les conteneurs a l'interieur d'un namespace utilisateur sans privileges root.Le but est de proteger le root de l'host d'attaque a travers les failles 

## Podman
Podman est une solution open-source provenant de chez Red Hat Entreprise sous different OS, c'est un logiciel de gestion de containers et d'images qui est comparée forcement a docker comme son futur "remplacant".

Podman permet de nombreuses fonctionnalité en plus de docker nativement :
Rootless, Deamonless, API en CLI.

Pour podman la commande est à peu près similaire à docker avec : 

podman run -it registry.iutbeziers.fr/debian:latest bash

Il y a une description qui dit que podman serait dans la dynamique DEV-OPS avec DEV en Podman et CRI-O en OPS 

L'interet de podman c'est qu'il n'y a pas de deamon en arriere-plan, il remplace la  ligne de commande docker standard et il peut fonctionner avec K8s.

## 9 Cluster de containers Docker


















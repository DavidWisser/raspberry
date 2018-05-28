#  Cross-compilation ##

## Introduction ##

Sans rentrer dans les détails, et de manière brêve, je vais décrire le principe de la cross-compilaton.

La cross compilation à comme but de pouvoir utiliser les ressources d'une machine avec un système d'exploitation, et de compiler sur une autre architecture. En l'occurence notre Raspberry Pi n'est pas très puissant pour compiler d'imposant Framework comme [Qt](https://www.qt.io/). Il faudrait quelques longues heures malgré le vaillant ARM Cortex-A53 d'un modèle 3 B+.

Alors pourquoi ne pas prendre un PC bien plus puissant et compiler le tout en moins de l'heure sur des machines d'aujourd'hui.

C'est ce que je vous propose de découvrir ici !!!

__Note:__ La suite du document est basé sur les écrits présentent sur la page [http://wiki.qt.io/RaspberryPi2EGLFS](http://wiki.qt.io/RaspberryPi2EGLFS) 

## Configuration ##

Machine principale Ubuntu 18.04 LTS ( [Site officiel](https://www.ubuntu.com/) )

Raspberry Pi modèle 3 B+ avec Rasbian Stretch édition Desktop du 2018-04-18 ( [Site officiel](https://www.raspberrypi.org/) )

Framework Qt 5.11 et Qt Creator 4.6.1

## Installation ##

Conseil: Avant de mettre en place l'image de Raspbian sur la carte SD, il est préfèrable de l'effacer par remplissage de zéro.

Sur *Linux* `dd if=/dev/zero of=/dev/sdX bs=4m` (status=progress pour l'état d'avancement)

Sur *Mac OS* `diskutil zeroDisk /dev/rdiskX`

### Raspbian sur Raspberry ###

1. Télécharger l'image de Raspbian sur le site officiel de la fondation, [ici](https://www.raspberrypi.org/downloads/raspbian/)

2. Dezipper le fichier: `unzip 2018-04-18-raspbian-stretch.zip`

3. Ecrire l'image sur votre carte SD: `dd if=2018-04-18-raspbian-stretch.img of=/dev/disk2 bs=4m && sudo sync`

4. Mettre votre carte SD et brancher l'alimentation sur votre Raspberry, votre Raspbian est prêt à l'utilisation

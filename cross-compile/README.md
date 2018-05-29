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

### Ubuntu sur PC ###

__Note:__ De nombreux articles décrivent la manière de procéder pour la mise en place de Ubuntu sur PC. Cette étape ne sera donc pas traitée.

### Configuration du Raspberry ###

1. Configurer le réseau Wifi ou Ethernet (au choix), afin qu'il soit sur votre réseau local

__Note:__ Afin de faciliter les prochaines étapes, nous allons configurer la connexion SSH entre le Raspberry et le PC

2. Ouvrir un terminal et lancer la commande `sudo raspi-config`

3. Entrer dans le menu "Change User Password" afin de définir un nouveau mot de passe

4. Entrer dans le menu "Network Options" puis "Hostname" et redéfinir le nom d'hôte. Pratique si plusieurs Raspberry en réseau ^^

5. Entrer dans le menu "Boot Options" puis "Desktop / CLI" et définir "Console" (choix personnel de ne pas utiliser l'environnement graphique)

6. Entrer dans le menu "Interfacing Options" puis "SSH" puis "Yes"

7. Entrer dans le meun "Advanced Options" puis "Memory Split" et taper 256

8. Quitter la configuration par "Finish" et rebooter le Raspberry en répondant "Yes" à la question

9. Attendre le redémarrage du Raspberry

10. Valider la connexion SSH vers le Raspberry avec la commande `ssh pi@hostname 'uname -srm'` et en retour la réponse `Linux 4.14.34-v7+ armv7l`

### Préparation du Raspberry ###

1. Mettre à jour Raspbian avec la commande `sudo rpi-update` et un reboot `sudo reboot`

2. Installation des librairies nécessaire à l'utilisation de Qt et pour la compilation du framework
    >> `sudo vim /etc/apt/sources.list` et décommenter la ligne **deb-src**
    >> `sudo apt update`
    >> `sudo apt build-dep qt4-x11`
    >> `sudo apt build-dep libqt5gui5`
    >> `sudo apt libudev-dev libinput-dev libts-dev libxcb-xinerama0-dev libxcb-xinerama0`

3. Préparation du répertoire cible
    >> `sudo mkdir /usr/local/qt5pi`
    >> `sudo chown pi:pi /usr/local/qt5pi`

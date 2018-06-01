#  Cross-compilation #

![Rasperry Pi logo](https://raw.githubusercontent.com/DavidWisser/raspberry/doc/cross-compile/img/logo_raspberrypi.png) ![Qt logo](https://raw.githubusercontent.com/DavidWisser/raspberry/doc/cross-compile/img/logo_qt.png)

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
	- `sudo vim /etc/apt/sources.list` et décommenter la ligne **deb-src**
	- `sudo apt update`
	- `sudo apt build-dep qt4-x11`
	- `sudo apt build-dep libqt5gui5`
	- `sudo apt install libudev-dev libinput-dev libts-dev libxcb-xinerama0-dev libxcb-xinerama0`

3. Préparation du répertoire cible
	- `sudo mkdir /usr/local/qt5pi`
	- `sudo chown pi:pi /usr/local/qt5pi`

### Configuration de Ubuntu ###

1. Installation du compilateur gcc avec la commande `sudo apt install gcc`

2. Installation du package OpenGL API -- GLX avec la commande `sudo apt install libgl1-mesa-dev`

3. Télécharger l'installateur de Qt, puis donner les droits d'execution et lancer le
	- `chmod 777 qt-unified-linux-x64-3.0.4-online.run`
	- `./qt-unified-linux-x64-3.0.4-online.run`

4. Sélectionner les options suivantes lors de l'installation de Qt
![Qt installation](https://raw.githubusercontent.com/DavidWisser/raspberry/doc/cross-compile/img/qt_install.png)

5. Lancer Qt Creator afin de valider l'installation et la compilation d'une première fenêtre
![Qt Window](https://raw.githubusercontent.com/DavidWisser/raspberry/doc/cross-compile/img/qt_window.png)

6. Création du répertoire de travail et mise en place de la tool-chain
    - `mkdir ~/raspi`
    - `cd ~/raspi`
    - `git clone https://github.com/raspberrypi/tools`

7. Création du répertoire sysroot et utilisation de rsync afin de parfaitement synchroniser l'ensembles des fichiers nécessaires à la cross-compilation
    - `mkdir sysroot sysroot/usr sysroot/opt`
    - `rsync -avz pi@hostname:/lib sysroot`
    - `rsync -avz pi@hostname:/usr/include sysroot/usr`
    - `rsync -avz pi@hostname:/usr/lib sysroot/usr`
    - `rsync -avz pi@hostname:/opt/vc sysroot/opt`

8. Création des liens symboliques avec le répertoire sysroot
    - `wget https://raw.githubusercontent.com/DavidWisser/raspberry/doc/cross-compile/script/sysroot-relativelinks.py`
    - `chmod +x sysroot-relativelinks.py`
    - `./sysroot-relativelinks.py sysroot`

### Compilation de Qt ###

1. Récupérer les sources depuis le dépôt Git officiel de Qt
	- `git clone git://code.qt.io/qt/qtbase.git -b 5.11`

2. Configurer pour la préparation de la compilation
	- `cd qtbase`
	- `./configure -no-use-gold-linker -release -opengl es2 -device linux-rasp-pi3-g++ -device-option CROSS_COMPILE=~/raspi/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian-x64/bin/arm-linux-gnueabihf- -sysroot ~/raspi/sysroot -opensource -confirm-license -make libs -prefix /usr/local/qt5pi -extprefix ~/raspi/qt5pi -hostprefix ~/raspi/qt5 -v`

3. Compiler en ajustant le nombre de processeur de votre machine avec l'option -j n (nombre de processus en parallèle)
	- `make -j 8`
	- `make install`

4. Deploiment Qt sur le Raspberry
	- `rsync -avz qt5pi pi@hostname:/usr/local`

5. Edition des liens symboliques pour trouver les librairies Qt
	- `echo /usr/local/qt5pi/lib | sudo tee /etc/ld.so.conf.d/qt5pi.conf`
	- `sudo ldconfig`
	- `sudo mv /usr/lib/arm-linux-gnueabihf/libEGL.so.1.0.0 /usr/lib/arm-linux-gnueabihf/libEGL.so.1.0.0_backup`
	- `sudo mv /usr/lib/arm-linux-gnueabihf/libGLESv2.so.2.0.0 /usr/lib/arm-linux-gnueabihf/libGLESv2.so.2.0.0_backup`
	- `sudo ln -s /opt/vc/lib/libEGL.so /usr/lib/arm-linux-gnueabihf/libEGL.so.1.0.0`
	- `sudo ln -s /opt/vc/lib/libGLESv2.so /usr/lib/arm-linux-gnueabihf/libGLESv2.so.2.0.0`
	- `sudo ln -s /opt/vc/lib/libEGL.so /opt/vc/lib/libEGL.so.1`
	- `sudo ln -s /opt/vc/lib/libGLESv2.so /opt/vc/lib/libGLESv2.so.2`

6. Exporter les variables pour la configuration de l'écran et la police d'écriture, en rajoutant dans votre fichier *.bashrc*. __Note:__ Adapter les valeurs en fonction de votre écran. Valeur en mm
	- `export QT_QPA_EGLFS_PHYSICAL_WIDTH=528`
	- `export QT_QPA_EGLFS_PHYSICAL_HEIGHT=295`
	- `export QT_QPA_FONTDIR=/usr/share/fonts/truetype/dejavu`

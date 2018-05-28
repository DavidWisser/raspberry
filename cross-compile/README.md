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

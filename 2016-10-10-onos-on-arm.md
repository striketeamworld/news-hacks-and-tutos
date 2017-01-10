## Onos on ARMv7
J'ai réussi ce weekend à faire compiler (build) et créer une image container pour Onos (Open Network Operating System) sur Raspberry Pi 2 (Arm v7l) sur le système Raspbian 8.0 (Debian jessie pour Raspberry), et le kernel Linux 4.1.19. J'écris ce post tant que c'est frais dans ma tête.

Les détails de tout ça sont sur [mon repo](https://github.com/sofianinho/onos-rpi) et l'image du container sur mon [profil dockerHub](https://hub.docker.com/r/sofianinho/onos-rpi/).

## Challenges et leçons
 - En gros le temps de compilation sur Raspberry pi, dont on se rend compte quand on règle n'importe quel problème et qu'on doit refaire
 - Trouver les images de base docker pour Raspberry (et ARM en général). Docker est officiellement supporté par ARM mais il n'y pas encore de moyen convaincant et simlple pour:
    - Construire son image pour raspberry. Les sorciers qui détiennent le secret se nomment "Hypriot", qui ont vraiment fait beaucoup pour docker sur Raspberry, y compris une distribution prête à l'installation.
    - Déployer sur DockerHub pour partager. Ce que Hypriot et les autres font pour être sur dockerHub c'est faire une archive (.tar.xz) de la racine (/), et lier le déploiement à cirleCI ou travis sur Github, pour décompresser l'archive lorsque dockerHub tente de faire le build automatique à partir du Dockerfile. Le problème d'origine est que dockerHub ne connait pas l'architecture ARM, donc il tentera de contruire le Dockerfile sur AMD64 (x86_64 en gros), ce qui résultera en "exec error format". D'où l'archive pour éviter cette phase. En gros un hack qui est inspiré de Jerome Pettazoni et son script mkimage.sh (vraiment pas mal pour contruire n'importe quelle image docker depuis la distrib kernel en partant d'une image docker SCRATCH).
 - Entre temps et lié à Onos, il faut aussi trouver les bons compilateurs ARM 32bits (c'est l'archi de raspberry). Si vous êtes sor Odroid c'est ARM 64bits (je crois)...etc. Les exe responsables de pas mal de migraines sont: thrift (facile à trouver), protobuf et gRPC. J'ai donc vu par la compilation que Onos utilise tout ces jolis protocoles/approches pour son développement ce qui était très instructif (je ne connaissais pas thrift par exemple et je croyais que gRPC était encore en béta ou en tout cas pas mature).

## Dockerfile
Tout ça est documenté sur le repo github, et je donne aussi les commandes pour le faire. Si j'ai le temps un jour, il faudra passer à un Dockerfile complet avec les commandes dedans. Comme je le disais avant, le soucis c'est le temps de compilations sur raspberry. 

Du coup, j'ai fait un racourcis dans mon Dockerfile qui permet de créer sa propre image raspberry pi, mais en partant d'un package Onos 1.8 déjà compilé et hébergé sur mon google drive. 5minutes au final suffisent pour créer son image avec une bonne connexion Internet.

     

Sofiane

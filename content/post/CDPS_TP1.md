+++
title = "TP - Créer et gérer des machines virtuelles, Linux (VM)"
description = "Centros de Datos y Procesos de Sistema - CDPS"
date = 2018-09-03
tags = ["CDPS,VM,Linux"]
author = "Damien Narbais"
+++

## Objectif du TP

Prise en main, compréhension et comparaison de différents types de machines virtuelles.
- Connaitre les principes de base de la virtualisation Linux, avec KVM et LXC
- Gerer des machines virtuelles en utilisant une interface graphique et la ligne de commande


## 1 - Gestion de VM KVM avec une interface graphique

On a commencé par créer des VM au format *raw*.  

On a ensuite utilise le format *QCOW2*.
Ce format est tres pratique car a partir d'une image "mere" (>2Go), on peut creer des VM images beaucoup plus légères (-[400Mo ; 800M0]). Ce qui est tres pratique quand on a beaucoup de VM.

## 2 - Gestion de VM KVM avec l'interface libvirt + Fichier *xml*

Tout d'abord, pour la virtualisation KVM, on a utilisé l'interface libvirt. Nous avons créé une VM à partir d'une image au format *qcow2*.   
Ensuite, pour créer la deuxième machine virtuelle avec virsh, nous avons eu besoin de 2 éléments :
- une nouvelle image,
- un fichier de configuration *xml*

### Le fichier *xml*

Pour avoir la structure du format *xml*, nous avons extrait le fichier *xml* de la première machine virtuelle. Nous avons modifié certains paramètres (nom de la machine, nom de l'image utilisée, l'identifiant unique uuid et l'identifiant MAC).

&nbsp;&nbsp;&nbsp;&nbsp; On va maintenant présenter et analyser la structure du fichier *xml*.  
Ce fichier a une hiérarchie. Dans la balise de *domaine*, il y en a d'autres dont le *name*, *uuid*, *memory*, *vcpu*, *os*...    
Certaines balises en contiennent d'autres. Chacune d'elle contient des éléments spcécifique (voir tableau).


| ![Fichier xml](/static/images/CaptureFichierXml.png) |
|:--:|
| *Capture d'écran du fichier xml utilisé en TP * |

| Nom de la balise  | Signification  | Exemple : Contenu pour notre TP |
|:-:|:-:|:-:|
| name  | C'est le nom de la VM. | ubuntu16.04-qcow1  |
| uuid  | C'est l'identifiant de la VM qui est unique. Pour le générer on utilisé la commande : `uuidgen --random`|   |
| memory  | Quantité de mémoire disponible au lancement de la VM. On peut l'augmenter avec des appareils externes.  KiB (kibibytes): c'est l'unité qui correspond à 1024 bytes  | 2097159  |
| currentMemory  | C'est la mémoire acutelle de la VM. Elle peut être inférieur à la taille de la *memory*. S'il n'y a pas d'indication, c'est qu'elle fait la même taille que la *memory*. C'est la même unité que pour la *memory*  | 2097159  |
| vcpu  | C'est le nombre de CPU maximum que peut avoir la VM | 2 |
| ressource  | Le superviseur peut accepter de partitionner la VM. Il y a en toujours une par défault. | Dans notre exemple, on n'a pas spécifié de partition, donc c'est celle par défaut qui est utilisée. |
| os  | Contient les paramètres du système opérationnel pour initier la VM. Il contient le type de l'OS, le type de la machine..  | L'architecture du CPU est 'x86_64’. LA machine est 'pc-i440fx-xenial'. Et 'hvm' est notre type de virtualisation  |
| features  | Une longue liste de caractéristiques que nous pouvons activées ou désactivées  | ACPI est utile pour la gestion de l'énergie. Par exemple, avec les invités KVM, nous avons besoin d'une fermeture progressive pour travailler.  |
| cpu  | C'est le conteneur principale pour décrire les pré-requis du CPU invité  | Dans notre exemple, le CPU virtuel fourni à la machine invitée doit correspondre exactement aux besoins du CPU. (<match> ='exact'). Un invité persistant verra le même matériel quel que soit l'hôte sur lequel il démarre. (<mode> ='personnalisé')  |
| devices  | Cette balise mentionne les dispositifs du domaine invité. Tous sont hérités du dispositif principal (émulateur, disque, controleur, son, vidéo ...)  | X  |
*Toutes les balises non pas été expliquées dans ce tableau*

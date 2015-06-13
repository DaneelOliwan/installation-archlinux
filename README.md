# How install an archlinux distribution step by step with grub, dual boot EFI.

Créer une clé bootable pour archLinux. 

Sous linux :
    
    dd if=<fichier.iso> of=/dev/<sdb> bs=4M 

où <fichier.iso> est le path vers l'iso contenant l'image de archLinux, et <sdb> le path vers la clé usb (ou DVD).

Sous Windows, certains outils font très bien le travail.

Si vous faites un dualboot sous Windows, il faut booter sous Windows avant et désactiver le fast boot. Sous Windows, Taper Win+R puis pour désactiver le fast boot :

    shutdown -r -t 0

Puis rebooter en sélectionnant la clé usb


Première chose à faire : changer la configuration du clavier pour passer à un clavier azerty durant l'installation (et uniquement durant l'installation)

    loadkeys fr


## Partitionnement

Pour connaître les disques disponibles et surtout leur emplacement :

    fdisk -l

(Il est aussi possible d'utiliser gdisk pour la suite. gdisk est pour GPT et fdisk pour MBR).

Chez moi par exemple, je sais qu'il ne faut pas installer archLinux sur /dev/sda qui est ma clé USB mais uniquement sur /dev/sdg. Ensuite pour avoir plus d'information sur votre partiton :

    fdisk -l /dev/sdg
    
On peut ainsi connaître le type de table des partitions, les partitions présentes sur ce disques ainsi que leur taille. L'option -l permet de juste lister les informations. En enlevant le -l on entre dans un mode interactif pour modifier les partitions.

    fdisk /dev/sdg
    
Créer les différentes partitions dont on a besoin. Je conseille a minima :

- partition EFI (si windows et 8 ou +, il y en a déjà une) de 200 Mo 
- partition / de 30-60 Go (en fonction de l'espace que vous avez)
- partition swap de la même taille que votre RAM
- partition /home

Créer les partitions et penser à bien définir leur code qui permet d'indiquer à quoi va servir ces partition (mais c'est en fait plus tard que l'on va vraiment définir les points de montage relatifs à chaque partition). Penser aussi à bien utiliser w pour la dernière commande interactive de gdisk afin d'écrire toutes les modifications.

Une fois le partitionnement réalisé, relister les partitions afin de connaître les numéros de chaque partition pour les identifier.

    gdisk -l /dev/sdg

### Swap

Il faut maintenant utiliser la partition prévue pour le swap. On commence par la formater. (En utilisant -L on peut renommer la partition).

    mkswap -L "ArchLinux Swap" /dev/sdg3
    

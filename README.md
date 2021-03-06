# 🚀 Flashing the beaglebone by Romain 🤖

### Written by the master of markdown also known as David Guerin

![yocto.png](https://pbs.twimg.com/profile_images/1158899683/yocto-project-whitebg_400x400.png)

#### Introduction 🎁

Modifier le `local.conf` qui se trouve dans `/build/conf` depuis le dossier `pocky-rocko`, et y décommenter :

```
MACHINE ?= "beaglebone"
```

Cela permet du coup de choisir la machine cible de notre distribution.

---

Afin d'activer un layer, dans le fichier `bblayers.conf`, ajouter :

```
... \

/data_container/poky-rocko-18.0.0/meta-hello \
```
---

Afin d'ajouter certaines recettes de ces layers modifier `local.conf` :

```
IMAGE_INSTALL += " example sl"
```

Cela permet de dire qu'au moment de la compilation de la distribution ces paquets seront disponible dans la distribution, cela active les packages des layers, ici : `example` et `sl`.

On peut aussi paramétrer certaines recettes (paquets) pour qu'elles s'ajoutent par défaut dès lors que le layer dans lequel elles appartient à été ajouté. Cela se fait avec la même ligne de code, mais dans le fichier `local.conf`qui se situe dans le dossier du layer, c'est à dire `LAYERNAME/conf/local.conf`

---

Pour activer un patch, il faut modifier le `NOMRECETTE_VERSION.bb` qui se trouve dans le dossier de la recette :

```

...


SRC_URI =  " ... \
    file://bonjour.patch"

...

```

Pour compiler une recette, il faut remplir la fonction `do_compile()`, ici :

```
do_compile() {
    ${CC} ${LD_FLAGS} helloworld.c -o helloworld
}
```
---

Modifier `recipes-kernel/linux/linux-yocto_4.9.bbappend` afin de patcher un noyau linux :

```
COMPATIBLE_MACHINE = "beaglebone"
RDEPENDS_kernel-base += "kernel-devicetree"
DEPENDS += "xz-native bc-native"
KERNEL_DEVICETREE_beaglebone += " \
    bbb-4dcape431.dtb \
"

FILESEXTRAPATH_prepend := "${THISDIR}/linux-yocto_${LINUX_VERSION}:"

PV = "4.9.49"

SRC_URI += " \

```


Yocto :
chemin de travail:  /data_container/poky-rocko-18.0.0/
source variables d'environnement : . oe-init-build-env

commande pour lister les layers de l'image
bitbake-layers show-layers

Pour avoir une image beaglebone:
editer conf/local.conf pour décommenter beaglebone
bitbake cire-image-minimal pour construire l'image

ajouter un layer
modifier build/conf/bblayers.conf
ajouter le chemin du layer dans "BBLAYERS=?"

ajouter une recette
dans build/conf/local.conf
ajouter cette ligne :
IMAGE_INSTALL_append = "nom de la recette dans un layer"

en gros, un layer = un ensemble de recettes pour construire des programmes

se connecter au port série du beaglebone
picocom -b 115200 /dev/ttyUSB0

emplacement des images :
build/tmp/deploy/images/

dumper l'image core
dd if=core-image-minimal-beaglebone.wic of=/dev/sdc status=progress

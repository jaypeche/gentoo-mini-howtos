# TIPS : Working Cirrus CS35L41 sound chipset on ASUS_RoG_Strix 2023

Le chipset son Cirrus CS35L41, présent sur plusieurs modèles d'ordinateurs ASUS ROG Strix 2023, est connu pour poser des problèmes de compatibilité sous Gentoo Linux. Bien que ce chipset soit réputé pour sa qualité audio, il peut entraîner des difficultés comme l'absence de son, des craquements ou des déchirures sonores.

Dans ce mémo, je partage une solution complète pour résoudre ces problèmes, en mettant en avant les configurations kernel, les paramètres de Pipewire, les firmwares nécessaires, et même des conseils matériels pour optimiser l'audio interne. Cette configuration a été testée sur une machine G814JV, mais elle devrait être applicable à d'autres configurations similaires.

**J'ai pour ma part été confronté à 6 problèmes différents :**

## 1. Support Cirrus chipset ( kernel-6.18+ )

Comme en informatique cela ne sert à rien de réinventer la roue.
Je mets à votre disposition, ma config kernel Gentoo incluant les différents chipsets de mon G814JV.

Elle est basée sur la configuration Gentoo standard améliorée ( Cirrus/Intel/Nvidia/Netfilter/ACPI... ) et devrait pouvoir s'appliquer à d'autres configurations car elle se veut généraliste.

Elle est disponible içi :

* [Kernel 6.18.18 configuration](https://gist.github.com/jaypeche/a70212ed1598f2a83b529979a30bf738)

Avec cette config kernel, les modules suivants devraient être chargés :

* [lsmod | grep snd](https://gist.github.com/jaypeche/e265ffc41091d874ee830fc0831ac49c)

## 2. Absence de son sur la sortie interne ( pipewire || pulseaudio )

Il s'agit d'un bug connu sous Gentoo, d'après la documentation officielle il suffit simplement d'activer "Serial Bus Multi Instantiate" dans le kernel, pour que les hauts-parleurs internes soient bien mappés, reconnus et fonctionnels.

* https://wiki.gentoo.org/wiki/User:Pietinger/Experimental/Manual_Configuring_Current_Kernel#Serial_Bus_Multi_Instantiate

`Device Drivers  --->
    -*- X86 Platform Specific Device Drivers  --->
        [*]   Serial bus multi instantiate pseudo device driver
`

On précisera également le chipset au noyau en renseignant /etc/modprobe.d/10-alsa.conf :

`options snd-hda-intel model=alc294`

On vérifie ensuite qu'on utilise le bon chipset :

`dmesg | grep -in20 alc294`

## 3. Problèmes de stuttering/cracking ( Phénomène de craquements )

Il arrive souvent avec ce chipset récent de renconter des problèmes de craquements ou de déchirures du son.
Il m'a fallu beaucoup de recherches infructueuses, avant de trouver ce post fort utile qui m'a clairement permis d'apprécier une sonorité de qualité avec l'audio interne.

* https://forums.gentoo.org/viewtopic.php?p=8863683

Il s'agit d'ajouter ces deux variables d'environnement à la config make.conf de portage, et ensuite recompiler votre serveur audio ( pipewire dans mon cas ).

* **Ajouter ceçi à votre /etc/portage/make.conf :**

`
PORTAGE_NICENESS=19
PORTAGE_SCHEDULING_POLICY=batch
`

* **Recompiler ensuite votre serveur son; pour pipewire par exemple :**

`# emerge -av media-video/pipewire media-video/wireplumber`

* **Il peut être utile d'élargir la valeur minimale du quantum dans pipewire conf :**

`nano /etc/pipewire/pipewire.conf.d/01-increase-quantum.conf`

* **Modifier 01-increase-quantum.conf comme ceçi :**

`context.properties = {
        default.clock.quantum     = 2048
        default.clock.min-quantum = 2048
        default.clock.max-quantum = 4096
}
`

* **Cela devrait avoir un impact positif sur la stabilité du flux audio.**

## 4. Présence des firmwares à jour ( sys-kernel/linux-firmware sys-firmware/sof-firmware )

`# echo "sys-kernel/linux-firmware ~amd64" >> /etc/portage/package.accept_keywords/linux-firmware`

`# mount /boot && emerge -av sys-kernel/linux-firmware sys-firmware/sof-firmware`

## 5. Migration vers pipewire sound server ( media-video/pipewire media-video/wireplumber )

Gentoo Linux tend depuis quelques mois à remplacer pulseaudio par pipewire.

Je recommande de partir de la configuration par défaut, pour ensuite ajouter une à une d'autres fonctionnalités.

`# echo "media-video/pipewire bluetooth pipewire-alsa sound-server" >> /etc/portage/package.use/pipewire`

`# emerge -av media-video/pipewire media-video/wireplumber`

Il faut également savoir que ce chipset son récent, est encore en développement; et n'as pu être complétement  implémenté. Pulseaudio et Pipewire ne permettent pas encore de tirer profit de l'ensemble du chipset Cirrus compatible Dolby.

Les mises à jour à venir, permettront certainement à terme de le supporter complétement.

## 6. Hardware - HP interne mal positionné

Lors du nettoyage de la ventilation, je me suis aperçu qu'un des deux HPs rectangulaires était mal positionné. Ils sont logiquement, montés sur silent-blocs, afin justement d'éviter les vibrations mécaniques. Le repositionement du HP sur son support a résolu ce problème audio parasite.

## Pour finir :

La configuration proposée dans ce mémo vise à résoudre les problèmes audio liés au chipset Cirrus CS35L41 en optimisant à la fois le noyau Linux, le serveur audio (Pipewire), et les firmwares nécessaires.

Au niveau du noyau, l'activation de l'option "Serial Bus Multi Instantiate" permet de corriger un bug connu lié à la reconnaissance des haut-parleurs internes.
Au niveau de Pipewire, l'ajout des variables d'environnement :

PORTAGE_NICENESS et PORTAGE_SCHEDULING_POLICY

Ainsi que la modification du quantum permettent d'améliorer la stabilité et la performance du flux audio.
Au niveau des firmwares, l'installation de linux-firmware et sof-firmware assure la compatibilité avec le chipset CS35L41.

Cette configuration a été testée sur une machine ASUS ROG Strix G814JV sous Gentoo Linux et devrait être applicable à d'autres configurations équipées de ce chipset.


Mis à jour le 3/6/2026

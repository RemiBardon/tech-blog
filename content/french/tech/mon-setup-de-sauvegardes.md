---
title: Sécuriser sa vie numérique en tant que *digital nomad*
summary: &summary >
  En voyage, et surtout en *backpacking*, il est crucial de bien maîtriser la sauvegarde de ses données, pour éviter de les perdre.
  Je vous partage ici mes conseils pour passer un voyage serein.
description: *summary
date: 2023-11-04
lastmod: 2024-07-23
tags:
  - Backup
  - Conseils
  - Digital nomad
  - Formattage
  - macOS
  - Sauvegarde
  - Sécurité
  - SSD
  - Stockage
  - Voyage
draft: false
---

{{< callout "note" >}}
Cet article n’est pas entièrement rédigé. Comme souvent, je l'ai publié pour qu’au moins une partie soit lisible. Je ne terminerai un jour™.
{{< /callout >}}

> ***Disclaimer* :**
> J’utilise un Mac et un iPhone, alors les outils que je mentionne ici sont pour certains spécifiques à ces environnements.
> Cela dit, les solutions que je propose sont universelles, et ont certainement leur équivalent partout.

## *TL;DR* {#tldr}

- [Tout sauvegarder avant de partir](#tout-sauvegarder-avant-de-partir)
  - [Time Machine], mots de passe, photos…
- [Avoir plusieurs sauvegardes](#avoir-plusieurs-sauvegardes)
  - Sauvegarde froide chez un proche
  - Sauvegarde « de tous les jours » dans le logement (principalement [Time Machine])
  - Stockage portable qui nous suit partout (avec [Time Machine] si possible)
- [Tout chiffrer](#tout-chiffrer)
  - [FileVault] sur macOS
  - APFS chiffré sur les disques
  - TODO
- Partager des accès de récupération voire d'héritage
  - TODO

## Contexte

Quand on est *digital nomad*, on s’expose à plus de risques que nos analogues sédentaires.
On est souvent dans des endroits à la vue de tous, on peut se faire voler un seul sac contenant une grande partie de notre vie et, suivant le pays, on peut avoir du matériel qui n’est même pas abordable pour la plupart des gens.

Il faut donc se préparer au fait que l’on nous vole nos affaires.

### Ce que l’on veut éviter

Quand on est *digital nomad*, il y a plusieurs choses que l’on veut éviter (sans ordre particulier) :

- Perdre l’accès à des comptes
- Perdre des données
- Recevoir une facture téléphonique de plusieurs centaines d’euros

Les dangers :

- Vol : dans une cafétéria, en terrasse, à la plage, dans un *hostel*, dans un bus…
- Perte : n’importe où, n’importe quand
- Destruction : incendie, innondation…

### Les besoins

- Accès instantané
- Accès depuis un nouvel appareil
- Accès gratuit
- Accès sans Internet

## Règle n°1 : Tout sauvegarder avant de partir {#tout-sauvegarder-avant-de-partir}

Avant de partir en voyage, je recommande fortement de faire une sauvegarde intégrale de **toutes** nos données sur un disque que l’on peut laisser à un(e) proche.
Dans le cas d’un nomadisme à long terme il y a certainement d’autres variantes, mais, ne l’ayant pas encore vécu, je suis mal placé pour donner des conseils.

## Règle n°2 : Sauvegarder à plusieurs endroits physiques {#avoir-plusieurs-sauvegardes}

Tout comme il ne faut pas mettre tous ses papiers et tout son argent au même endroit quand on voyage, il ne faut pas mettre toutes ses sauvegardes dans le même lieu.
À l’inverse des papiers d’identité qui peuvent être refabriqués[^refabriquer-id] ou de l’argent que l’on gagnera à nouveau, nos données ne sont pas récupérables si elles sont définitivement inaccessibles (perte[^perte], vol, destruction…).
C’est pourquoi il faut faire plusieurs copies que l’on stocke à différents endroits.

[^refabriquer-id]: C’est certainement très compliqué mais c’est **possible**.

Mon fonctionnement (que je recommande) est le suivant :

- Un SSD de 2 To[^sandisk-extreme] resté en France, contenant :
  - Une sauvegarde [Time Machine] de mon Mac, faite le jour avant mon départ
  - Une sauvegarde de ma photothèque iCloud entière
- TODO: Terminer la liste

## Règle n°3 : Chiffrer tous ses disques {#tout-chiffrer}

Nos données ne sont pas toutes publiques, alors ça devrait être un réflexe de chiffrer tous nos stockages.

- Sur macOS : Activer [FileVault]
- Sur les stockages externes : Utiliser des formats permettant le chiffrement comme l’APFS
  - Rassurez-vous, un stockage chiffré est toujours lisible par un iPhone. En branchant le stockage chiffré, l’iPhone demandera son mot de passe pour pouvoir lire les données.
- TODO: Terminer la liste

## Règle n°4 : Ne pas dépendre d’une carte SIM / utiliser une clé d’authentification {#security-key}

J’ai une [YubiKey 5C Nano], et elle m’a déjà sorti d’une très mauvaise situation.

J’ai récemment [noyé mon téléphone](https://blog.remibardon.name/aventures/2023-10-mexique/2023-10-17-j-ai-noye-mon-telephone/) pendant un voyage au Mexique, et j’ai du récupérer les accès à tous mes comptes. TODO: Finir paragraphe

Bref, ça peut paraître cher, mais c’est très pratique.

Idéalement, il faudrait deux clés au cas où on en perdrait une, mais dans certains cas la carte SIM remplit parfois ce rôle (ne pas la garder avec la clé d’authentification).

TODO: [YubiKey 5C Nano] utilisable directement sur l’iPhone 15 grâce à l'USB-C, les autres utilisant le NFC.

## Règle n°5 : Partager des accès *legacy* {#legacy-access}

Quand on sécurise toute sa vie numérique, on doit faire attention à ne jamais se retrouver bloqué « en dehors de chez soi ».
C’est évidemment une chose à laquelle on pense, mais on oublie parfois de regarder plus loin.
S’il nous arrive quelque chose de grave, on peut ne plus être en capacité d’accéder à nos données, mais quelqu’un d’autre peut en avoir besoin.

C’est là qu’entrent en jeu les héritiers numériques, des personnes désignées par l’utilisateur d’un service pouvant accéder à une partie de ses données après son décès.
Google et Apple[^apple-legacy-contact], permettent de déclarer des héritiers numériques, et il ne sont pas les seuls.

[^apple-legacy-contact]: [How to add a Legacy Contact for your Apple ID - Apple Support](https://support.apple.com/en-us/HT212360)

En tant que *digital nomads*, souvent entrepreneurs, on a beaucoup de responsabilités et des personnes peuvent dépendre de nous.
C’est pourquoi je recommande d’avoir recourt à cette pratique afin de faciliter la succession dans une situation tragique.

Pour information, avec Apple le code d'accès se présente sous la forme d'une page au format PDF contenant un code et son QR-code équivalent.
Il faut ensuite la partager à une personne de confiance (avec AirDrop par exemple pour éviter tout transfert par Internet).

TODO: Il faut deux clés pour activer la 2FA physique sur iCloud

TODO: Approuver le numéro de téléphone temporaire tout de suite sur iCloud (pour Wallet… ne fonctionne pas)

## Quelques conseils {#conseils}

### Créer une partition avec nos coordonnées {#partition-drive_owner}

Tous nos stockages devraient être chiffrés, mais dans ce cas comment quelqu’un peut nous retourner un objet qu’il aurait trouvé ?

Ma solution est de découper mes disques en deux partitions :
une de quelques Mo encodée en MS-DOS (FAT16) et une encodée en APFS chiffré prenant le reste de la place du disque.

Dans la première, appelée "DRIVE_OWNER", je stocke un seul fichier contenant mes coordonnées. Il s’appelle "IDENTITY.txt" et qui se présente comme ceci :

```txt
This storage belongs to:
Rémi BARDON

If you find it, please contact me via:
Email: remi.bardon+emergency@proton.me
Phone: +33 7 XX XX XX XX

You can also send it to:
<ADDRESS>

Thank you.
```

Quelques remarques :

- Encodage MS-DOS (FAT16) car c’est le plus compatible
- Extension `.txt` pour permettre l’ouverture sur tout appareil
- `+33` devant le numéro de téléphone pour permettre l’utilisation depuis n’importe quel pays
- `+emergency` dans l’adresse e-mail pour pouvoir marquer l’e-mail comme important dès sa réception

#### Créer la partition "DRIVE_OWNER" sur macOS

1. Vider le disque
2. Ouvrir Disk Utility (application intégrée à macOS)
3. Sélectionner le disque puis "Partition"
4. Bouton "+" puis "Add Partition" (et non pas "Add Volume") si Disk Utility le demande
   - Si Disk Utility le demande, on veut une "GUID Partition Map"
5. Changer la taille à 10 Mo (il est important de choisir la taille avec de choisir le format, puisque les partitions MS-DOS ne peuvent pas être redimentionnées)
   - Au moment de formatter le disque, Disk Utility refuse parfois certaines tailles de partitions MS-DOS trop petites. Je n’ai jamais compris quelle était la vraie limite, il faut parfois plusieurs tentatives pour qu’il accepte.
6. Changer le format à "MS-DOS (FAT)"
7. Changer le format de l’autre partition à "APFS (Encrypted)" (pour pouvoir [augmenter l’espace utilisable du disque] s’il est utilisé avec [Time Machine])
8. Renommer les deux partitions
9. "Apply"

#### Désactiver l’*auto-mount* sur macOS

La [partition "DRIVE_OWNER"](#partition-drive_owner) ne nous sert à rien à nous.
Elle encombre notre affichage et cause l’affichage d’une alerte au moment déjecter le disque nous demandant si on veut éjecter tous les volumes ou seulement un seul.
Ce serait bien si on pouvait ne pas la voir, hein ? Eh bien c’est possible !

On peut désactiver l’*auto-mount* d’une partition sur notre appareil, sans empêcher les autres personnes de la voir apparaître.
Pour cela, il suffit de:

```bash
sudo vifs
```

puis insérer:

```txt
LABEL=DRIVE_OWNER none auto rw,noauto
```

{{< callout "tip" >}}
`cat /etc/fstab` permet de vérifier que l'écriture a bien marché.
{{< /callout >}}

Pour désactiver l’*auto-mount* d’une autre partition sur notre appareil, voici les étapes à suivre:

1. Récupérer le "Volume UUID" de la partition (en remplaçant `<VOLUME>` par le nom du volume) :

   ```bash
   diskutil info /Volumes/<VOLUME>
   ```

   {{< collapse summary="Exemple de résultat attendu" >}}

   ```txt
      Device Identifier:         disk4s3
      Device Node:               /dev/disk4s3
      Whole:                     No
      Part of Whole:             disk4

      Volume Name:               UNIVERSAL
      Mounted:                   Yes
      Mount Point:               /Volumes/UNIVERSAL

      Partition Type:            Microsoft Basic Data
      File System Personality:   ExFAT
      Type (Bundle):             exfat
      Name (User Visible):       ExFAT

      OS Can Be Installed:       No
      Media Type:                Generic
      Protocol:                  USB
      SMART Status:              Not Supported
      Volume UUID:               884434A4-1EAF-3017-8527-57B6814B3524
      Disk / Partition UUID:     612DFF07-D670-4638-843D-E9B0B565DC62
      Partition Offset:          1936365453312 Bytes (3781963776 512-Byte-Device-Blocks)

      Disk Size:                 64.0 GB (63999836160 Bytes) (exactly 124999680 512-Byte-Units)
      Device Block Size:         512 Bytes

      Volume Total Space:        64.0 GB (63996821504 Bytes) (exactly 124993792 512-Byte-Units)
      Volume Used Space:         16.6 GB (16630677504 Bytes) (exactly 32481792 512-Byte-Units) (26.0%)
      Volume Free Space:         47.4 GB (47366144000 Bytes) (exactly 92512000 512-Byte-Units) (74.0%)
      Allocation Block Size:     512 Bytes

      Media OS Use Only:         No
      Media Read-Only:           No
      Volume Read-Only:          No

      Device Location:           External
      Removable Media:           Fixed

      Solid State:               Yes
   ```

   {{< /collapse >}}

2. ```bash
   sudo vifs
   ```

   puis insérer (en remplaçant `<UUID>` par le « _Volume UUID_ » trouvé plus tôt et `<TYPE>` par le « _Type (Bundle)_ » ou `auto` pour faire simple) :

   ```txt
   UUID=<UUID> none <TYPE> rw,noauto
   ```

   {{< callout "important" >}}
   Sur macOS Sequoia, le « _Type (Bundle)_ » `msdos` n’est pas  interprété correctement.
   Dans ce cas, il vaut donc mieux utiliser `auto` et non `msdos`.
   Pour plus d’informations, consultez _[Automount settings from fstab not respected after Sequoia update - Apple Community](https://discussions.apple.com/thread/255831146)_.
   {{< /callout >}}

4. Vérifier le contenu de `/etc/fstab`, qui devrait contenir la nouvelle ligne :

   ```bash
   cat /etc/fstab
   ```

   {{< collapse summary="Mon fichier `/etc/fstab`" >}}

   ```txt
   #
   # Warning - this file should only be modified with vifs(8)
   #
   # Failure to do so is unsupported and may be destructive.
   #

   # NOTE: `auto` and not `msdos` because [Automount settings from fstab not respected after Sequoia update - Apple Community](https://discussions.apple.com/thread/255831146).
   LABEL=DRIVE_OWNER none auto rw,noauto

   # RB_T7 / UNIVERSAL
   UUID=4318F670-8188-3ECD-9356-25AAB84B9C28 none exfat rw,noauto
   # RB_SX / UNIVERSAL
   UUID=884434A4-1EAF-3017-8527-57B6814B3524 none exfat rw,noauto
   ```

   > Les partitions `exfat` sont des partitions pour Windows, utilisées comme stockage lecture/écriture pour mon *dual boot* Windows (puisque la partition Bootcamp permet seulement la lecture) et pour accéder à des fichiers sur Linux.

   {{< /collapse >}}

### Augmenter l’espace utilisable sur une partition APFS

TODO

<!-- Les partitions APFS ont une  sur un disque utilisé par [Time Machine] -->

### Fournir des codes d’accès à une connaissance

TODO

## D’autres conseils

Je publierai bientôt™ un autre article pour revenir sur une mauvaise expérience de voyage, en analysant ce qui s’est mal enchaîné et en détaillant ce que je ferai mieux la prochaine fois. Je partagerai le lien ici quand l’article sera publié.

[^perte]: Voir [« Créer une partition avec nos coordonnées »](#partition-drive_owner) pour des conseils permettant de récupérer un stockage perdu
[^sandisk-extreme]: Le [SanDisk Extreme 2 To](https://www.amazon.fr/dp/B08HN37XC1/ref=pe_27091421_487052621_TE_item) (NVMe, USB-C, USB 3.2, 1050 Mo/s, IP65)

[FileVault]: https://support.apple.com/guide/security/volume-encryption-with-filevault-sec4c6dc1b6e/web "Volume encryption with FileVault in macOS - Apple Support"
[Time Machine]: https://support.apple.com/en-us/HT201250 "Back up your Mac with Time Machine - Apple Support"
[YubiKey 5C Nano]: https://www.yubico.com/mx/product/yubikey-5c-nano/ "USB-C YubiKey 5C Nano | Two Factor Security Key | Yubico"

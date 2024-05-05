---
title: Choix de la batterie
summary: Détail du choix de la batterie
date: 2023-05-05
publishDate: 2023-12-29
---

## Données

### Module Heltec CubeCell v2

Si on part sur un module Heltec CubeCell v2 (`HTCC-AM01_V2`), la _data sheet_ donne [les caractéristiques suivantes](https://resource.heltec.cn/download/CubeCell/HTCC-AM01_V2/HTCC-AM01_V2(Rev1.1).pdf#page8) :

{{< cloudinary_image
  assetId="gyygdvpzbzra4uoskaid"
  date="2023-05-06T15:50:04+02:00"
>}}

{{< callout "help" "-" "Que signifient TX, RX et _Sleep_ ?" >}}

  Ces termes sont expliqués dans la [FAQ]({{< relref "faq" >}}#tx-rx-sleep).
{{< /callout >}}

Pour le _dev kit_ (`HTCC-AB01_V2`), la _data sheet_ donne [les caractéristiques suivantes](https://resource.heltec.cn/download/CubeCell/HTCC-AB01_V2/HTCC-AB01_V2(Rev1.1).pdf#page11) :

{{< cloudinary_image
  assetId="x6zwbizzmyr5iizkru7c"
  date="2023-05-06T15:47:35+02:00"
>}}

{{< callout "help" "-" "Que signifient TX, RX et _Sleep_ ?" >}}

  Ces termes sont expliqués dans la [FAQ]({{< relref "faq" >}}#tx-rx-sleep).
{{< /callout >}}

### Pile CR2032

Une pile bouton CR2032 de la marque Duracell a une capacité de 225mAh[^cr2032-duracell].

## Consommation max pour une pile bouton

Pour tenir 72 heures[^autonomie] avec une telle pile, la consommation du traceur
ne doit pas dépasser $\frac{225}{72} = 3.125$mA.

Si on veut que la balise reste allumée durant tout le stage,
il faudra passer en _deep sleep_ autant que possible
car le module Heltec CubeCell v2 en mode réception (`RX`) consomme 20mA à lui seul.

## Autonomie sur une pile bouton

### Avec réception en continu

Le module Heltec CubeCell v2 consomme 20mA en mode `RX`. La balise fera aussi de la transmission de temps en temps, mais ce sera sur des durées tellement courtes que c'est négligeable.

Sur une pile CR2032 Duracell, on aurait donc une autonomie de $\frac{225}{20}=11.25$ heures.

### Sans réception

On aimerait bien ne pas avoir à changer la pile régulièrement, alors on peut calculer l'autonomie des traceurs en mode veille pour savoir s'ils faudrait changer/déconnecter les piles entre les stages.

Le module Heltec CubeCell v2 consomme 3.5μA en mode _Sleep_,
il pourrait donc tenir $\frac{225}{0.0035} \approx 64285$ heures
(soit $\frac{225}{0.0035 \times 24 \times 365} \approx 7.33$ années) dans ce mode.

Évidemment, le traceur GPS n'est pas seulement constitué de ce module et il ne servirait à rien s’il n’écoutait et n’émettait pas,
mais ce calcul nous rassure concernant le fait que les traceurs pourraient être utilisés durant plusieurs stages sans changer leurs batteries.

[^autonomie]: Cette valeur vient de « [Besoins]({{< relref "besoins" >}}#autonomie) ».
[^cr2032-duracell]: Source : [CR2032 Batterie | Marque de batterie la plus fiable | Duracell](https://www.duracell.com/fr/en-us/product/cr-2032-lithium-coin-button-battery/).

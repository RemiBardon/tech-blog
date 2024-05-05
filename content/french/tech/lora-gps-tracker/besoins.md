---
title: Besoins
summary: Explication des besoins
date: 2023-05-05
publishDate: 2023-12-29
---

## *User Stories*

{{< callout "help" "-" >}}

  Le terme *User Story* désigne un scénario d’utilisation, souvent un besoin d’un utilisateur.
{{< /callout >}}

### On tourne en voiture à la recherche de groupes

- Porté de quelques kilomètres

### Quelqu'un est perdu

- Besoin d’augmenter la fréquence d’émission et/ou la puissance du signal, même si ça consomme plus de ressources
- Possible de créer un [réseau maillé (*mesh*)](https://en.m.wikipedia.org/wiki/Wireless_mesh_network) pour augmenter la porté des messages

### On veut regarder à posteriori les trajets des groupes

- Une carte SD dans le traceur enregistre les coordonnées (et d’autres infos comme une *black box* d’avion)

### On oublie de mettre en veille un traceur

- Veille automatique (quelques jours pour être large et ne pas mettre en veille le traceur d’une personne que l’on recherche depuis plusieurs heures)
- Une LED pour indiquer que le traceur est allumé ?
  - Pas vraiment nécessaire s’il y a un *switch*
  - Pas discret
  - N’évite pas les oublis

## Spécifications techniques

### Autonomie {#autonomie}

On veut une autonomie **minimale** de la durée d'un stage (en supposant une utilisation constante).
Cela fait $3 \times 24 = 72$ heures pour 3 jours de stage.

---
title: Une application pour ajouter des photos depuis mon téléphone
summary: "J’écris ce blog depuis mon téléphone, mais c’était compliqué d’y insérer des images alors je me suis créé une application pour pouvoir le faire facilement 😇"
date: 2023-11-05
draft: false
---

## Contexte

J’écris une grande partie de ce blog depuis mon téléphone, depuis un peu n’importe où, en utilisant l’application [Obsidian](https://obsidian.md) comme éditeur. Je trouve l’application super pratique pour de très nombreuses raisons, et je ne compte pas en changer.

Dans mes articles, notamment [en voyage](https://blog.remibardon.name/aventures/), j’aime insérer des images et des vidéos, mais il est important pour moi qu’elles soient servies aux lecteurs rapidement et en consommant le moins de données possible. C’est pour cette raison que j’ai choisi d’utiliser Cloudinary comme hébergeur de contenu média.

Aussi, j’aime ajouter des titres à mes illustration, créditer leur auteur (le cas échéant) et ajouter des descriptions alternatives comme il est recommandé de faire.

## Problèmes

### Syntaxe complexe sur téléphone

Je ne peux pas faire tout cela avec la syntaxe Markdown basique (`![placeholder](link "alt")`), alors j’ai créé un [« *shortcode* » Hugo](https://gohugo.io/content-management/shortcodes/), et il fonctionne très bien ! Le truc, c’est que je ne peux pas facilement écrire ce *shortcode* sur mon téléphone, car il est long et contient des caractères un peu spéciaux. Les `{`, `}`, `<` et `>` ça va, mais les `""` sont transformés par mon clavier en `«  »` alors je ne peux pas faire un simple raccourci clavier. En plus il faut que je copie-colle un lien mais que j’en prenne seulement un bout à mettre dans le *shortcode*, bref c’est trop fastidieux.

### La console de Cloudinary tourne des images

Quand j’utilise la console de Cloudinary depuis mon téléphone, je peux sélectionner des médias depuis ma galerie ou mes fichiers, c’est parfait. Malheureusement, quand  je publie une image prise au format portrait, elle est tournée d’un quart de tour et elle se retrouve à l’horizontale.

Le problème ne doit pas venir de leur côté, il vient de la manière dont iOS encode les image **je crois**, mais ça reste bloquant pour moi.

## Solution

### Un *plugin* Obsidian ?

Obsidian gère super bien l’ajout de contenus média, mais malheureusement l’application importe les fichiers dans le *repository* git et utilise la syntaxe Markdown basique. J’ai pensé me faire un plugin Obsidian pendant un temps, mais accéder à la galerie, faire de la conversion vidéo et présenter une interface complexe, le tout en JavaScript, me paraissait trop ambitieux.

### Non, une app iOS

Je suis donc parti sur ce que je sais faire : une application iOS 👨🏻‍💻 En quelques heures, j’ai créé une application qui :

1. Permet à l’utilisateur de choisir une image depuis sa galerie
2. Redimensionne l’image et la compresse en JPEG qualité 0,75 (sur une image de test, j’ai eu un taux de compression de 1600%)
3. Publie l’image sur Cloudinary grâce au [SDK Cloudinary pour iOS](https://github.com/cloudinary/cloudinary_ios), en affichant une barre de progression
4. Affiche un formulaire permettant à l’utilisateur d’entrer un titre, un sous-titre, un texte alternatif et le *copyright*
5. Génère le code correspondant et permet de le copier en un clic, pour pouvoir l’insérer dans le blog ensuite

Ma solution n’est pas idéale en terme d’expérience utilisateur, il y a encore plein d’opportunités d’amélioration, mais au moins je peux maintenant tout faire depuis mon téléphone. C’est une super avancée.

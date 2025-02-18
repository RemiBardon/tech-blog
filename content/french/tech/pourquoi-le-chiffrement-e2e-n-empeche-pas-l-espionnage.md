---
title: Pourquoi le chffrement de bout en bout n'empêche pas l'espionnage
summary:
date: 2024-07-23
draft: true
tags:
  - Chiffrement
  - Securité
  - Espionnage
---

Depuis que j'ai commencé à travailler pour [Prose] il y a quelques mois, il m'arrive très fréquemment d'informer sur les problèmes des outils de communication de notre quotidien, en expliquant ceux que Prose résout. Je mentionne souvent que le [chiffrement de bout en bout] permet de s'assurer que personne d'autre que les destinataires d'un échange peuvent en lire le contenu, mais je ne prends que rarement le temps de souligner que cela n'empêche pas l'espionnage. Dans cet article, j'explique pourquoi en donnant des exemples concrets.

## C'est quoi le chiffrement de bout en bout ? {#e2e-encryption}

Pour faire très simple, le [chiffrement de bout en bout] c'est une méthode mathématique permettant de sécuriser un échange d'information de l'application[^osi] émettrice jusqu'à l'application[^osi] réceptrice. Personne entre les deux ne peut lire le contenu de l'échange, c'est mathématiquement impossible[^algo].

[^osi]: Selon la terminologie du [modèle OSI](https://fr.wikipedia.org/wiki/Mod%C3%A8le_OSI "Modèle OSI — Wikipédia").
[^algo]: Cela dépend évidemment de l'algorithme de chiffrement utilisé. Avec un algorithme suivant les recommandations de sécurité actuelles, c'est tellement improbable que l'on considère que c'est impossible.

## Savoir qu'il y a un échange est déjà une information valorisable

Comme sur une lettre ou un colis, une information est toujours publique[^vpn-tor] pour permettre le bon acheminement du paquet : l'adresse du destinataire. N'importe qui interceptant le paquet peut savoir d'où il provient (au moins qui est la dernière entité à l'avoir transporté), où il va, quand est-ce qu'il a été envoyé et quelle taille il fait.

[^vpn-tor]: Ce problème peut être évité en utilisant par exemple un [VPN](https://fr.wikipedia.org/wiki/R%C3%A9seau_priv%C3%A9_virtuel "Réseau privé virtuel — Wikipédia") ou le [réseau Tor](https://fr.wikipedia.org/wiki/Tor_(r%C3%A9seau) "Tor (réseau) — Wikipédia"), mais ce n'est pas une pratique courante.

Cela peut paraître anodin, mais le fait de savoir que cet échange existe a déjà beaucoup de valeur. Voici quelques exemples concrets pour vous le montrer :

- Une personne mariée envoie souvent des messages à quelqu'un tard le soir et lorsque son/sa conjoint(e) est absent(e) -> tromperie ?
- Dans une entreprise, deux équipes se mettent soudainement à échanger très régulièrement -> un nouveau projet ?
- Deux régiments se mettent soudainement à échanger très régulièrement -> une attaque en préparation ?
- Deux personnes en couple arrêtent soudainement de se parler -> rupture ?
- Une personne contacte un *dealer* -> cliente ?

- Géants du numérique et recroisements
- Intéractions entre plusieurs groupes

## Suivre un paquet par sa taille

- Retrouver le nom du système qui change la taille du paquet pendant le trajet pour éviter la traçabilité

## Déchiffrer des années plus tard

- [Loi de Moore](https://fr.wikipedia.org/wiki/Loi_de_Moore)
- [Cryptographie post-quantique](https://fr.wikipedia.org/wiki/Cryptographie_post-quantique)

[Prose]: https://prose.org/ "Page d’acceuil de Prose"
[chiffrement de bout en bout]: https://fr.wikipedia.org/wiki/Chiffrement_de_bout_en_bout "Chiffrement de bout en bout — Wikipédia"

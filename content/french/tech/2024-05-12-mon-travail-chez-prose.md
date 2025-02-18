---
title: Ce que je fais chez Prose
summary: TODO
date: 2024-05-12
tags:
  - Prose
draft: true
---

Depuis que [je me suis lancé en auto-entrepreneur](https://blog.remibardon.name/2023-12-11-j-ai-cree-mon-entreprise/), je travaille exclusivement pour [Prose] et ce pour une durée indéterminée[^salariat-deguise]. C’est un projet que j’avais découvert début 2022, et pour lequel j’avais déjà effectué un stage de 3 mois durant l’été suivant (depuis l’Afrique du Sud).

[^salariat-deguise]: Une telle situation pourrait s’apparenter à du salariat déguisé, ce qui est interdit par la loi. Dans mon cas ce n’en est pas puisque, comme je le détaille dans cet article, je n’ai pas de contraintes horaires ou de résultats.

{{< callout "important" >}}
Cet article entre dans le détail de le fonctionnement de Prose, et suppose une compréhension globale du projet. Je vous invite à lire d’abord mon article [« Prose, une messagerie instantanée pour les entreprise en quête de souveraineté numérique »]({{< ref "prose" >}}) si vous ne connaissez pas le projet.
{{< /callout >}}

## Ce que je fais

Le projet Prose est en fait un ensemble de sous-projets, que je pourrais simplifier comme cela :

#TODO: Illustration projets

Grâce à l’utilisation d’un protocole de communication libre (le [XMPP]), les applications Prose peuvent être utilisées seules telles qu’une application comme Slack s’utiliserait. Cependant, pour être utilisable par n’importe quelle entreprise sans compétence technique, le projet Prose sera fourni avec une interface visuelle d’administration du server de messagerie.

Aujourd’hui, l’application macOS ([hybride] pour l’instant) fonctionne et peut déjà être utilisée pour s’envoyer des messages textuels ou vocaux et partager des fichiers. Nous l’utilisons d’ailleurs déjà pour toutes les communications liées au projet depuis plusieurs mois 🥳. L’interface d’administration, quant à elle, n’existe que sous forme de [*mock-ups*](https://fr.m.wikipedia.org/wiki/Mock-up) (en particulier [prose-pod-dashboard.sketch](https://github.com/prose-im/prose-medley/blob/master/designs/pod/prose-pod-dashboard.sketch)).

À partir de ces *mock-ups*, mon activité est de définir et implémenter le logiciel (une [API REST]) qui sera utilisé par cette interface pour configurer le serveur de messagerie. J’avance bien mais comme tout projet informatique suffisamment avancé il est parsemé d’embûches qui me font perdre beaucoup de temps. De plus, notre choix d’utiliser des technologies performantes et sûres est à double tranchant : on obtient un résultat de grande qualité et durable en échange d’une perte de vélocité.

## Comment je vois le futur

Une fois que ma partie sera suffisamment stable, Valerian pourra prendre le relais en développant l’interface web d’administration. Il y a de nombreuses fonctionnalités à développer et je devrai assurer la « maintenabilité » de mon code (le projet étant [libre]) alors j’y serai certainement toujours dans plusieurs mois.

Vers fin 2024, on devrait avoir un produit très complet alors je passerai certainement sur le développement de l’application iOS, qui sera en cours de développement. Pour l’instant on va garder une application [hybride] sur macOS car c’est suffisant et il vaut mieux prioriser les applications mobiles.

De nombreuses entreprises sont déjà inscrites à notre liste d’attente et aimeraient tester Prose alors on annoncera certainement[^certainement] une *alpha* une fois les applications web, macOS et iOS disponibles (en plus de l’interface d’administration).

[^certainement]: Ceci est seulement **mon avis**, pas une information officielle (même semi-officielle).

Cela va sans dire, je

[API REST]: https://fr.m.wikipedia.org/wiki/Representational_state_transfer "*Representational state transfer* | Wikipédia"
[hybride]: https://fr.m.wikipedia.org/wiki/Application_hybride "Application hybride | Wikipédia"
[libre]: https://fr.m.wikipedia.org/wiki/Logiciel_libre "Logiciel libre | Wikipédia"
[Prose]: https://prose.org "Site de Prose"
[XMPP]: https://en.wikipedia.org/wiki/XMPP "XMPP | Wikipédia"

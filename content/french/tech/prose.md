---
title: Prose, une messagerie instantanée pour les entreprises en quête de souveraineté numérique
summary: &summary >
  Dans cet article, je vous présente Prose, un projet en lequel je crois et pour lequel je travaille aujourd’hui.
description: *summary
date: 2024-05-12
lastmod: 2024-07-22
tags:
  - Prose
  - "Souveraineté numérique"
  - XMPP
draft: false
---

## Qu’est-ce que Prose ? {#qu-est-ce-que-prose}

Tout est expliqué (en anglais) dans [« *Introducing Prose* » sur le blog du projet][annonce-prose], mais je vais essayer de résumer l’article avec mes mots histoire de vous faciliter la compréhension. De plus, depuis la publication originale en septembre 2021, certains points se sont précisés (à mesure que le projet a avancé).

### Pourquoi Prose ? {#pourquoi-prose}

{{< callout "note" >}}
Ce que je vais dire est **mon** avis, il n’a ni été vérifié ni accepté par le porteur du projet.
{{< /callout >}}

{{< callout type="cite" title="Extrait de [*Introducing Prose*](https://prose.org/blog/introducing-prose/), traduit avec [DeepL.com](https://www.deepl.com) (version gratuite). J’ai rajouté un peu de formatage et quelques liens." >}}
Aujourd’hui, **presque toutes les entreprises utilisent des outils de messagerie instantanée** au sein de leurs équipes. Ces outils aident les entreprises à accroître leur productivité. Ils ont (presque) entièrement remplacé les *e-mails*. Mais il y a un hic : **ces nouvelles plateformes sont toutes centralisées**. Qu’il s’agisse de Slack, de Skype for Business, de Microsoft Teams ou du défunt HipChat, tous les **secrets industriels** et les **conversations internes** de l’entreprise sont relayés et stockés sur des [*clouds*](https://www.cloudflare.com/fr-fr/learning/cloud/what-is-the-cloud/) centralisés (situés pour la plupart aux États-Unis)**. Il en va de même pour les communications personnelles**, avec Facebook Messenger, Telegram, WhatsApp et iMessage.

Si l’internet rend la **décentralisation techniquement possible et souhaitable**, elle n’est pas une exigence de facto pour les opérateurs de services. Au contraire, **toutes les entreprises financées par le capital-risque ont une forte incitation économique à la centralisation**, afin de concentrer davantage de valeur dans leurs **jardins clos**. Si ce modèle centralisé est efficace pour créer des entreprises publiques de plus d’un milliard de dollars, il va **à l’encontre de la raison d’être de l’internet**.
{{< /callout >}}

À cette introduction déjà très claire, je rajouterais que stocker ses données sur le sol d’un autre pays les expose aux lois du pays en question. Dans le cas des États-Unis, il y a notamment le [CLOUD Act](https://fr.m.wikipedia.org/wiki/CLOUD_Act):

{{< callout type="cite" title="Extrait de [*CLOUD Act* sur Wikipédia](https://fr.m.wikipedia.org/wiki/CLOUD_Act)" >}}
Critiquée par certaines associations de défense de la [vie privée](https://fr.m.wikipedia.org/wiki/Vie_priv%C3%A9e), cette loi permet notamment aux instances de justice américaines de solliciter auprès des fournisseurs de services opérant aux États-Unis, les communications personnelles d’un individu, citoyen ou résident US, sans que celui-ci en soit informé, ni que son pays de résidence ne le soit, ni que le pays où sont stockées ces données ne le soit[^1].

[^1]: C. Fischer, [The CLOUD Act: A Dangerous Expansion of Police Snooping on Cross-Border Data](https://www.eff.org/deeplinks/2018/02/cloud-act-dangerous-expansion-police-snooping-cross-border-data) [archive](https://archive.wikiwix.com/cache/?url=https%3A%2F%2Fwww.eff.org%2Fdeeplinks%2F2018%2F02%2Fcloud-act-dangerous-expansion-police-snooping-cross-border-data), [Electronic Frontier Foundation](https://fr.m.wikipedia.org/wiki/Electronic_Frontier_Foundation), 8 février 2018
{{< /callout >}}

Comme détaillé dans [« Critiques »](https://fr.m.wikipedia.org/wiki/CLOUD_Act#Critiques), le *CLOUD Act* entre en conflit avec le renommé [Règlement Général sur la Protection des Données (RGPD)](https://fr.m.wikipedia.org/wiki/R%C3%A8glement_g%C3%A9n%C3%A9ral_sur_la_protection_des_donn%C3%A9es), et selon Nathalie Devillier, professeure en droit du numérique à l’école du management de Grenoble, « \[Il] offre un cadre légal à la saisie \[…] de toutes les communications captées à l’étranger par les serveurs des sociétés américaines »[^15].

[^15]: Alexandra Saviana, « ["Cloud Act" : malgré la RGPD, les Etats-Unis à l’assaut de vos données personnelles](https://www.marianne.net/monde/cloud-act-malgre-la-rgpd-les-etats-unis-l-assaut-de-vos-donnees-personnelles) [archive](https://archive.wikiwix.com/cache/?url=https%3A%2F%2Fwww.marianne.net%2Fmonde%2Fcloud-act-malgre-la-rgpd-les-etats-unis-l-assaut-de-vos-donnees-personnelles) », [Marianne](https://fr.m.wikipedia.org/wiki/Marianne_(magazine)), 19 juin 2018

### Concrètement, c’est quoi Prose ? {#prose-concretement}

Si je devais le résumer simplement, je dirais que **[Prose] est une application de [messagerie instantanée] pour les entreprises soucieuses de la discrétion de leurs échanges internes**. Contrairement à la quasi totalité des services sur le marché, **Prose [chiffre de bout en bout]** les communications et **permet d’héberger soi-même son serveur de [messagerie instantanée]** (les entreprises possèdent donc leurs données). En utilisant le très renommé protocole de messagerie **[XMPP]**, **Prose s’intègre dans un écosystème d’outils déjà existants** sur toutes les plateformes du monde. Tout comme un serveur de messagerie électronique (e-mails), un serveur Prose permet à ses utilisateurs de discuter avec n’importe quel compte XMPP (même externe à l’entreprise). Pour couronner le tout, **la totalité du code de Prose est [libre][free-software]**, donc il peut être consulté, modifié et déployé gratuitement et librement.

{{< callout "info" "+" "À la base, c’était ça WhatsApp" >}}
En 2009, quand [Jan Koum] et [Brian Acton] ont lancé [WhatsApp], ils avaient la même vision que Prose. Se rappelant de la surveillance des communications ukrainiennes par les autorités soviétiques quand Koum était jeune, les deux ont créé une application qui ne permettait pas la revente de données personnelles ou l’espionnage des messages envoyés. Depuis sa création, WhatsApp utilise une version personnalisée du protocole de communication [XMPP] (qui utilise le numéro de téléphone comme identifiant)[^1]. Cependant, après son rachat en 2014 par Facebook (aujourd’hui Meta), cette vision a changé, le code source de l’application a été rendu privé, toutes les interfaces avec le reste de l’écosystème [XMPP] ont été fermées et les deux créateurs originaux ont quitté l’entreprise pour des raisons morales.

Si l’histoire fascinante de WhatsApp vous intéresse, je vous conseille [la vidéo « L’histoire impossible des créateurs de WhatsApp » de la chaîne Underscore_ sur YouTube][video-underscore], qui vulgarise très bien l’ascension du géant que l’on connait aujourd’hui.

[^1]: Source : [WhatsApp — Wikipédia](https://fr.wikipedia.org/wiki/WhatsApp#Principe "WhatsApp > Principe — Wikipédia")

[Jan Koum]: https://fr.wikipedia.org/wiki/Jan_Koum "Jan Koum — Wikipédia"
[Brian Acton]: https://fr.wikipedia.org/wiki/Brian_Acton "Brian Acton — Wikipédia"
[WhatsApp]: https://fr.wikipedia.org/wiki/WhatsApp "WhatsApp — Wikipédia"
[XMPP]: https://fr.wikipedia.org/wiki/Extensible_Messaging_and_Presence_Protocol#Le_d%C3%A9but_:_Jabber "Extensible Messaging and Presence Protocol — Wikipédia"
[video-underscore]: https://youtu.be/x3KPHuML1og?feature=shared "L’histoire impossible des créateurs de WhatsApp | YouTube"
{{< /callout >}}

## Pourquoi je crois en Prose {#pourquoi-je-crois-en-prose}

{{< callout "note" >}}
La suite de cet article n’est pas encore rédigée, mais comme souvent je publie l’article pour qu’au moins une partie soit lisible.
{{< /callout >}}

- Adéquation projet-porteur de projet
- Prose est une *non profit*
- Modèle économique connu et qui marche
- Protocole de messagerie déjà très utilsé et robuste
  - \+ clients déjà existants sur toutes les plateformes

## Pour en savoir plus {#en-savoir-plus}

1. [prose.org](https://prose.org) / [prose.org/blog](https://prose.org/blog)
2. [Me contacter](mailto:remi@remibardon.name)
3. [Prose#general](xmpp:org.prose.channel.5n6mqquv@groups.prose.org) (XMPP)
4. [github.com/prose-im](https://github.com/prose-im)
5. Le *tag* [#Prose]({{< relref "tags/prose" >}}) sur mon blog

[Prose]: https://prose.org/ "Page d’acceuil de Prose"
[annonce-prose]: https://prose.org/blog/introducing-prose/
[messagerie instantanée]: https://fr.wikipedia.org/wiki/Messagerie_instantan%C3%A9e "Messagerie instantanée — Wikipédia"
[chiffre de bout en bout]: https://fr.wikipedia.org/wiki/Chiffrement_de_bout_en_bout "Chiffrement de bout en bout — Wikipédia"
[XMPP]: https://fr.wikipedia.org/wiki/Extensible_Messaging_and_Presence_Protocol#Le_d%C3%A9but_:_Jabber "Extensible Messaging and Presence Protocol — Wikipédia"
[free-software]: https://fr.wikipedia.org/wiki/Logiciel_libre "Logiciel libre — Wikipédia"

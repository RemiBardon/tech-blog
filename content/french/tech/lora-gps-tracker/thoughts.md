---
title: Thoughts
date: 2023-05-06
publishDate: 2023-12-29
draft: true
---

- v1 : Que du TX
- v2 : TX + RX direct
- v3 : TX + RX meshed
- v4 : TX + RX meshed avec élection de leader

- Si on reste en RX, ça consomme beaucoup
- Si on coupe la RX, on loupé de l'info
- On pourrait se dire "tout le monde écoute et discute toutes les 5 minutes", mais on aurait vite des problèmes de synchronisation
	- En plus ça empêche des usages plus avancés
- Il faudrait qu'une node dise "Je vais écouter pendant 15 secondes", et que la gateway communique les messages à cette node dans cet intervalle
	- La gateway doit garder une pile de message pour chaque node
- Si la node est hors de portée lors de son broadcast ou lors de l'envoi du message, l'info est perdue, il faudrait mesher
- Mesh avec broadcast
	- Chaque node doit garder une pile des messages à communiquer
	- Chaque message doit avoir un timestamp d'envoi
	- Chaque message doit avoir un destinataire
	- Chaque node doit garder une trace des messages reçus, pour éviter de traiter deux fois le même message
		- Chaque message doit avoir un ID
	- Pour éviter les cycles, chaque message doit avoir un envoyeur (source du message)
	- Ça commence à ressembler à du MQTT

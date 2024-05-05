---
title: Research
date: 2023-04-22
publishDate: 2023-12-29
draft: true
---

https://randomnerdtutorials.com/esp32-lora-rfm95-transceiver-arduino-ide/
https://www.adafruit.com/product/3269
https://blog.adafruit.com/2023/04/19/making-a-trail-counter-using-a-esp32-reverse-tft-and-adalogger-feather-feather-esp32-arduino/


- LoRa antenna
	- SMA connector
		- Attention : SMA-RP means Reverse Polarity and they are not interchangeable

LoRa uses ISM (Industrial, Scientific and Medical) radio bands

868 MHz en Europe

## Choix du module LoRa

Heltec CubeCell

- [CubeCell – Module (V2) · 9,90 $ · heltec.org](https://heltec.org/project/htcc-am01-v2/)
	- ![[Pasted image 20230502130407.png]]
	- Le module en lui-même
	- Specs
		- Ultra low power design: 3.5µA in deep sleep
- [CubeCell – Dev-Board (V2) · 13,90 $ · heltec.org](https://heltec.org/project/htcc-ab01-v2/)
	- ![[Pasted image 20230502130331.png]]
	- Dev board
	- Tout petit, pas d'écran

### LoRa + Wi-Fi + BLE

- [Wireless Shell (V3) · 14,90 $ · heltec.org](https://heltec.org/project/wireless-shell-v3/)
	- ![[Pasted image 20230502130657.png]]
	- Module
	- LoRa, Wi-Fi, BLE, pas d'écran
	- Specs
		- Sleep: 9µA theoretically
- [Wireless Stick（V3) · 17,90 $ · heltec.org](https://heltec.org/project/wireless-stick-v3/)
	- ![[Pasted image 20230502130316.png]]
	- Dev board plus complet
	- LoRa, Wi-Fi, BLE, écran

## Choix de l'antenne

### Choose / build a good antenna

-  Rule #1: Use short, high quality, and thick antenna cables
-  ﻿﻿Rule #2: An SWR below 2 is acceptable
-  ﻿﻿Rule #3: Always connect an antenna to the sender.
-  ﻿﻿Rule #4: Keep the polarization of your antennas the same way
-  Rule #5: The more dBi, the more directionality and the more power
-  Rule #6: With a proper antenna setup and a line of sight we can go long distance
-  ﻿﻿Rule #7: Longer is not always better for antennas. Smarter is better

### Liens

- [RST-MA11-10808-22M-FY-001 · 4,11 € / u · digikey.fr](https://www.digikey.fr/en/products/detail/raltron-electronics/RST-MA11-10808-22M-FY-001/10272999)
	- Antenne comme en cours
- [ANT-19036EB56 · 4,21 € / u · digikey.fr](https://www.digikey.fr/en/products/detail/adam-tech/ANT-19036EB56/16123705)
	- Antenne comme en cours
- [JCG402LR-1 · 4,33 € / u · digikey.fr](https://www.digikey.fr/en/products/detail/jc-antenna/JCG402LR-1/15814463)
	- Antenne comme en cours
- [APAE868R2540JBDB2-T · 4,35 € / u · digikey.fr](https://www.digikey.fr/en/products/detail/abracon-llc/APAE868R2540JBDB2-T/3727165)
	- Ceramic patch antenna

## ESP32

### Products

- [ESP32-C3 · 4,90 $ · heltec.org](https://heltec.org/project/esp32-c3/)
	- ![[Pasted image 20230502131004.png]]
	- Dev board
	- Wi-Fi + BLE

## Piles

- [CR2032 (pile bouton)](https://data.energizer.com/pdfs/cr2032.pdf)
	- 3.0 V
	- 235 mAh

## Recherches

### Board

#### Arduino ?

- [Arduino Cloud](https://cloud.arduino.cc/)
- [9 Types of Arduino: Uses and Costs Compared](https://chipwired.com/arduino-types-compared/)

Overkill.

#### ESP32 ?

- [I've built a GPS tracker with ESP32 | soldering, assembly & first test | makermoekoe · YouTube](https://youtu.be/jASQQFm4NNM)
- [I built a LORA GPS-Tracker with ESP32 | makermoekoe · YouTube](https://youtu.be/C7jFupvuy9M)
- [I built a LoRa-GPS Tracker // Range test, battery life & firmware · YouTube](https://youtu.be/90ziiwkOJFw)
- [Early days development of a LoRa dog tracker · YouTube](https://youtu.be/lwYfUkqIqCo)

#### ESP32 ou ESP8266 ?

- [ESP32 vs ESP8266 · Random blog](http://www.embedic.com/technology/details/esp32-vs-esp8266--which-is-better-and-how-to-choose)

ESP32 et ESP8266 peuvent se connecter en Wi-Fi.

ESP32 est mieux sur tous les aspects + permet le BLE.

#### Ça coûte combien un ESP32 ?

- [ESP Product Selector](https://products.espressif.com/#/product-selector?language=en&names=)
- [ESP32 official product page](https://www.espressif.com/en/products/socs/esp32)
- [Acheter un ESP32 · mouser.fr](https://www.mouser.fr/c/?q=ESP32)

#### ESP32 + Rust ?

- https://github.com/esp-rs/awesome-esp-rust
- https://github.com/esp-rs
- https://iot.stackexchange.com/questions/2102/working-with-rust-on-the-esp32

Ç'aurait été intéressant mais on va éviter de se compliquer la tâche sur un premier projet.

#### Peut-être que les boards LoRa de Heltec sont plus adaptées ?

- [\#182 ESP32 Lora Boards: What you need to know before you buy (incl. Antenna knowledge) · Andreas Spiess · YouTube](https://youtu.be/CJNq2I_PDHQ)
	- Vidéo de février 2018, *boards* un peu anciennes
- [\#304 New LoRa chips and Heltec LoRa boards. How good are they? (Arduino, SX1262, ASR6501)](https://youtu.be/lobNwqHLrag)
	- Vidéo de décembre 2019, *boards* encore un peu anciennes






https://www.cyberark.com/resources/threat-research-blog/lorawan-mqtt-what-to-know-when-securing-your-iot-network
https://www.hivemq.com/blog/lorawan-and-mqtt-integrations-for-iot-applications-design/

https://hackaday.com/2020/02/26/lora-mesh-network-with-off-the-shelf-hardware/
[https://disaster.radio](https://disaster.radio/)

https://www.mdpi.com/1424-8220/21/13/4314
https://youtu.be/TY6m6fS8bxU


"is line of sight radio working in mountains"
https://radiofinds.com/do-walkie-talkies-work-in-the-mountains/

[Adafruit RFM95W · 19,95$ · Adafruit](https://www.adafruit.com/product/3072)
![](https://cdn-shop.adafruit.com/970x728/3072-07.jpg)
![](https://cdn-shop.adafruit.com/970x728/3072-13.jpg)
![](https://cdn-shop.adafruit.com/970x728/3072-03.jpg)
![](https://cdn-shop.adafruit.com/970x728/3072-04.jpg)
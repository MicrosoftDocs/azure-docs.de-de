---
title: Internet der Dinge – bewährte Methoden für die Sicherheit | Microsoft Docs
description: Der Artikel enthält eine Aufstellung von bewährten Methoden zur Sicherheit des Internets der Dinge von Microsoft und allgemeine Empfehlungen.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 2d5598c5-4c30-481d-b8f4-51ee024ea9a7
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 9413c0503c1b78550776d1c2f6ab8239205a788b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62121623"
---
# <a name="internet-of-things-security-best-practices"></a>Internet der Dinge – bewährte Methoden für die Sicherheit

Sicherung der Infrastruktur des Internets der Dinge (Internet of Things, IoT) ist ein kritisches Unterfangen für Benutzer, die sich mit IoT-Lösungen befassen. Aufgrund der Anzahl der betroffenen Geräte, und weil die Geräte verteilt sind, ist ein Sicherheitsereignis, das Millionen von IoT-Geräten gefährdet, keine Bagatelle und kann umfangreiche Auswirkungen haben.

Aus diesem Grund ist für die IoT-Sicherheit ein fundierter Ansatz erforderlich. In der Cloud und beim Verschieben über private und öffentliche Netzwerke müssen Daten sicher sein. Methoden zur sicheren Bereitstellung von IoT-Geräten müssen eingerichtet werden. Jede Ebene vom Gerät bis zum Netzwerk und Cloud-Back-End erfordert hohe Sicherheitsgarantien.

Die bewährten IoT-Methoden können auf folgende Weise kategorisiert werden:

* IoT-Hardwarehersteller bzw. -integrator
* IoT-Lösungsentwickler
* IoT-Lösungsbereitsteller
* IoT-Lösungsoperator

Dieser Artikel ist eine Zusammenfassung von [Internet der Dinge (IoT) – Bewährte Methoden für die Sicherheit](../iot-suite/iot-security-best-practices.md). In diesem Artikel finden Sie weitere Informationen.

## <a name="iot-hardware-manufacturer-or-integrator"></a>IoT-Hardwarehersteller bzw. -integrator

Befolgen Sie diese bewährten Methoden, wenn Sie IoT-Hardwarehersteller oder Hardwareintegrator sind:

* **Einhalten der Mindestanforderungen für Hardware:** Die Hardware sollte lediglich die für den Betrieb der Hardware erforderlichen Features enthalten. 
* **Manipulationssicheres Gestalten von Hardware**: Integrieren Sie einen Mechanismus zum Erkennen von physischer Manipulation an der Hardware, z.B. Öffnen der Geräteabdeckung, Entfernen eines Teils des Geräts usw. 
* **Entwickeln sicherer Hardware:** Integrieren Sie, falls gemäß [COGS](https://en.wikipedia.org/wiki/Cost_of_goods_sold) zulässig, Sicherheitsfeatures wie sichere und verschlüsselte Speicher und eine auf Trusted Platform Module (TPM) basierende Startfunktion.
* **Upgrades schützen**: Das Aktualisieren der Firmware während der Lebensdauer des Geräts ist unvermeidlich.

## <a name="iot-solution-developer"></a>IoT-Lösungsentwickler

Befolgen Sie diese bewährten Methoden, wenn Sie IoT-Lösungsentwickler sind:

* **Befolgen einer sicheren Softwareentwicklungsmethodik:** Zur Entwicklung sicherer Software muss von Grund auf die Sicherheit bedacht werden – vom Beginn des Projekts über Implementierung und Tests bis hin zur Bereitstellung.
* **Open Source-Software mit Bedacht wählen**: Open Source-Software ermöglicht eine schnelle Lösungsentwicklung.
* **Mit Bedacht integrieren**: Viele Software-Sicherheitsschwachstellen liegen zwischen Bibliotheken und APIs. 

## <a name="iot-solution-deployer"></a>IoT-Lösungsbereitsteller

Befolgen Sie diese bewährten Methoden, wenn Sie IoT-Lösungsbereitsteller sind:

* **Hardware sicher bereitstellen**: Für IoT-Bereitstellungen muss Hardware möglicherweise an unsicheren Orten wie öffentlichen Bereichen oder unbeaufsichtigten Gebieten bereitgestellt werden.
* **Authentifizierungsschlüssel schützen**: Jedes Gerät benötigt während der Bereitstellung Geräte-IDs und die zugehörigen, vom Clouddienst generierten, Authentifizierungsschlüssel. Bewahren Sie diese Schlüssel auch nach der Bereitstellung sicher auf. Jeder kompromittierte Schlüssel kann von einem böswilligen Gerät verwendet werden, um sich als ein vorhandenes Gerät auszugeben.

## <a name="iot-solution-operator"></a>IoT-Lösungsoperator

Befolgen Sie diese bewährten Methoden, wenn Sie IoT-Lösungsoperator sind:

* **Stetige Aktualisierung des Systems**: Stellen Sie sicher, dass Gerätebetriebssysteme und alle Gerätetreiber auf die neueste Version aktualisiert werden. 
* **Vor schädlichen Aktivitäten schützen**: Wenn es das Betriebssystem zulässt, installieren Sie die neueste Antivirus- und Antimalwaresoftware auf jedem Gerätebetriebssystem. 
* **Häufig überwachen**: Die Überwachung der IoT-Infrastruktur auf sicherheitsbezogene Probleme ist entscheidend für die Reaktion auf Sicherheitsvorfälle.
* **IoT-Infrastruktur physisch schützen**: Die schlimmsten Angriffe gegen die IoT-Infrastruktur beginnen mit dem physischen Zugriff auf Geräte.
* **Schützen der Cloud-Anmeldeinformationen**: Cloud-Authentifizierungsanmeldeinformationen für die Konfiguration und den Betrieb einer IoT-Bereitstellung sind wahrscheinlich die einfachste Möglichkeit zum Zugreifen und Manipulieren eines IoT-Systems. 


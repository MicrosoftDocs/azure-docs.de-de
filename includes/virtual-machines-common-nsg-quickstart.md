---
title: include file
description: include file
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 09/12/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ec6cbcbc93fe87634c87caeb0041b75ec916a22f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178061"
---
In Azure öffnen Sie einen Port oder erstellen einen Endpunkt für einen virtuellen Computer, indem Sie einen Netzwerkfilter für ein Subnetz oder eine VM-Netzwerkschnittstelle erstellen. Sie platzieren diese Filter, mit denen sowohl eingehender als auch ausgehender Datenverkehr gesteuert wird, in einer Netzwerksicherheitsgruppe, die an die Ressource angefügt ist, die den Datenverkehr empfängt.

Anhand des Beispiels in diesem Artikel wird veranschaulicht, wie Sie einen Netzwerkfilter erstellen, der den standardmäßigen TCP-Port 80 nutzt. (Es wird davon ausgegangen, dass Sie die entsprechenden Dienste bereits gestartet und alle Betriebssystem-Firewallregeln auf dem virtuellen Computer geöffnet haben.)

Nach der Erstellung eines virtuellen Computers, der für die Bereitstellung von Webanforderungen über den standardmäßigen TCP-Port 80 konfiguriert ist, haben Sie folgende Möglichkeiten:

1. Erstellen Sie eine Netzwerksicherheitsgruppe.

2. Erstellen Sie eine Eingangssicherheitsregel, die Datenverkehr zulässt, und weisen Sie den folgenden Einstellungen Werte zu:

   - **Zielportbereiche**: 80

   - **Quellportbereiche**: * (beliebiger Quellport zulässig)

   - **Prioritätswert**: Geben Sie einen Wert von maximal 65.500 und mit einer höheren Priorität als die standardmäßige Catch-All-Verweigerungsregel für eingehenden Datenverkehr ein.

3. Ordnen Sie die Netzwerksicherheitsgruppe der VM-Netzwerkschnittstelle oder dem Subnetz zu.

In diesem Beispiel wird zwar eine einfache Regel zum Zulassen von HTTP-Datenverkehr verwendet, Sie können aber auch Netzwerksicherheitsgruppen und -regeln nutzen, um komplexere Netzwerkkonfigurationen zu erstellen. 





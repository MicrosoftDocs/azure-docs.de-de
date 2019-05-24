---
title: Includedatei
description: Includedatei
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ae36adc78d8c87d85c64fd61cb3a50dfcae60b85
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170575"
---
Sie bezahlen für zwei Dinge: die stündlichen Computekosten für das Gateway des virtuellen Netzwerks und die Übertragung der Ausgangsdaten vom Gateway des virtuellen Netzwerks. Informationen zu den Preisen finden Sie auf der Seite [Preise](https://azure.microsoft.com/pricing/details/vpn-gateway) .

**Computekosten für virtuelles Netzwerkgateway**<br>Für jedes Gateway des virtuellen Netzwerks fallen stündliche Computekosten an. Der Preis basiert auf der Gateway-SKU, die Sie beim Erstellen des Gateways des virtuellen Netzwerks angeben. Die Kosten für das eigentliche Gateway fallen zusätzlich zur Datenübertragung an, die über das Gateway durchgeführt wird. Die Kosten eines aktiv-aktiven Setups sind gleich wie für aktiv-passiv.

**Datenübertragungskosten**<br>Datenübertragungskosten werden basierend auf ausgehendem Datenverkehr vom Gateway des virtuellen Quellnetzwerks berechnet.

* Wenn Sie Datenverkehr an Ihr lokales VPN-Gerät senden, wird dafür die Internetrate für die Übertragung von Ausgangsdaten berechnet.
* Wenn Sie Datenverkehr zwischen virtuellen Netzwerken in unterschiedlichen Regionen senden, richtet sich der Preis nach der Region.
* Wenn Sie Datenverkehr nur zwischen virtuellen Netzwerken senden, die sich in derselben Region befinden, fallen keine Kosten für die Datenübertragung an. Der Datenverkehr zwischen VNets in derselben Region ist kostenlos.
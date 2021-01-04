---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/24/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 92a6fef3305891b47c4dc2e0f0432e1079df5a69
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "71266518"
---
Das Gateway für virtuelle Netzwerke verwendet ein bestimmtes Subnetz, das als „Gatewaysubnetz“ bezeichnet wird. Das Gatewaysubnetz ist Teil des IP-Adressbereichs für das virtuelle Netzwerk, den Sie beim Konfigurieren Ihres virtuellen Netzwerks angeben. Es enthält die IP-Adressen, die von den Ressourcen und Diensten des Gateways für virtuelle Netzwerke verwendet werden. 


Bei der Gatewayerstellung geben Sie die Anzahl der im Subnetz enthaltenen IP-Adressen an. Wie viele IP-Adressen erforderlich sind, hängt von der VPN-Gatewaykonfiguration ab, die Sie erstellen möchten. Einige Konfigurationen erfordern mehr IP-Adressen als andere. Es empfiehlt sich, ein Gatewaysubnetz mit /27 oder /28 zu erstellen.


Sollte ein Fehler mit dem Hinweis angezeigt werden, dass sich der Adressraum mit einem Subnetz überschneidet oder dass das Subnetz nicht im Adressraum für Ihr virtuelles Netzwerk enthalten ist, überprüfen Sie Ihren VNet-Adressbereich. Möglicherweise sind in dem Adressbereich, den Sie für Ihr virtuelles Netzwerk erstellt haben, nicht genügend IP-Adressen verfügbar. Wenn Ihr Standardsubnetz beispielsweise den gesamten Adressbereich umfasst, stehen für die Erstellung zusätzlicher Subnetze keine IP-Adressen mehr zur Verfügung. Sie können entweder Ihre Subnetze innerhalb des vorhandenen Adressraums anpassen, um IP-Adressen freizugeben, oder einen zusätzlichen Adressbereich angeben und das Gatewaysubnetz dort erstellen.
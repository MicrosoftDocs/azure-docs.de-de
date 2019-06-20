---
title: Includedatei
description: Includedatei
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 66e21aefa5648262ca2fcc61501905f725ac0e4b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66150822"
---
Vergewissern Sie sich vor Beginn der Konfiguration, dass die folgenden Voraussetzungen erfüllt sind bzw. Folgendes vorhanden ist:

* Falls Sie bereits über ein virtuelles Netzwerk verfügen, mit dem Sie eine Verbindung herstellen möchten, sollten Sie Folgendes sicherstellen: Kein Subnetz Ihres lokalen Netzwerks darf sich mit den virtuellen Netzwerken überlappen, mit denen eine Verbindung hergestellt werden soll. Für Ihr virtuelles Netzwerk ist kein Gatewaysubnetz erforderlich, und es können keine Gateways für virtuelle Netzwerke verwendet werden. Falls Sie nicht über ein virtuelles Netzwerk verfügen, können Sie mit den Schritten in diesem Artikel ein Netzwerk dieser Art erstellen.
* Beschaffen Sie sich einen IP-Adressbereich für Ihre Hubregion. Der Hub ist ein virtuelles Netzwerk, und der von Ihnen für die Hubregion angegebene Adressbereich darf sich nicht mit einem Ihrer vorhandenen virtuellen Netzwerke überlappen, mit denen Sie eine Verbindung herstellen. Außerdem ist keine Überlappung mit Ihren Adressbereichen möglich, mit denen Sie lokal eine Verbindung herstellen. Falls Sie nicht mit den IP-Adressbereichen in Ihrer lokalen Netzwerkkonfiguration vertraut sind, wenden Sie sich an eine Person, die Ihnen diese Informationen zur Verfügung stellen kann.
* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.
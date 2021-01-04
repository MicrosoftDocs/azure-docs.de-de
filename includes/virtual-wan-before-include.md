---
title: include file
description: include file
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4940bce453729d13ba76071fa59bcf95c7672a24
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359498"
---
* Sie verfügen über ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen.

* Sie verfügen über ein virtuelles Netzwerk, mit dem Sie eine Verbindung herstellen möchten. Stellen Sie sicher, dass sich kein Subnetz Ihres lokalen Netzwerks mit den virtuellen Netzwerken für die Verbindungsherstellung überschneidet. Informationen zum Erstellen eines virtuellen Netzwerks im Azure-Portal finden Sie im Artikel zur [Schnellstartanleitung](../articles/virtual-network/quick-create-portal.md).

* Ihr virtuelles Netzwerk darf nicht über vorhandene Gateways für virtuelle Netzwerke verfügen. Wenn Ihr virtuelles Netzwerk bereits über Gateways verfügt (VPN oder ExpressRoute), müssen Sie alle Gateways entfernen, bevor Sie fortfahren können. Für diese Konfiguration ist es erforderlich, dass virtuelle Netzwerke nur mit dem Gateway des Virtual WAN-Hubs verbunden werden.

* Ein virtueller Hub ist ein virtuelles Netzwerk, das von Virtual WAN erstellt und genutzt wird. Es ist der Kern Ihres Virtual WAN-Netzwerks in einer Region. Beschaffen Sie sich einen IP-Adressbereich für Ihre Region des virtuellen Hubs. Der von Ihnen für den Hub angegebene Adressbereich darf sich nicht mit einem der vorhandenen virtuellen Netzwerke überlappen, mit denen Sie eine Verbindung herstellen. Außerdem ist keine Überlappung mit den lokalen Adressbereichen möglich, mit denen Sie eine Verbindung herstellen. Falls Sie nicht mit den IP-Adressbereichen in Ihrer lokalen Netzwerkkonfiguration vertraut sind, sollten Sie sich an eine Person wenden, die Ihnen diese Informationen zur Verfügung stellen kann.

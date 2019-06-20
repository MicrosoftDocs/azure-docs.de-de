---
title: VMware-Lösung von CloudSimple – Azure-Netzwerkverbindungen
description: Erfahren Sie mehr über das Verbinden Ihres virtuellen Azure-Netzwerks mit Ihrem CloudSimple-Regionsnetzwerk.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2ab82b6c1b4b373c186019eaf96f9864861b9d9
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497622"
---
# <a name="azure-network-connections-overview"></a>Übersicht über Azure-Netzwerkverbindungen

Wenn Sie einen CloudSimple-Dienst in einer Region erstellen, passiert Folgendes:

* Es wird eine Azure ExpressRoute-Verbindung erstellt, die dem Dienst in dieser Region zugeordnet wird.
* Es wird das Herstellen einer Verbindung aus Ihrem CloudSimple-Regionsnetzwerk mit Ihrem virtuellen Azure-Netzwerk oder Ihrem lokalen Netzwerk mithilfe von Azure ExpressRoute ermöglicht.
* Es wird ermöglicht, dass Sie aus der Umgebung Ihrer privaten Cloud Zugriff auf Dienste haben, die in Ihrem Azure-Abonnement oder lokalen Netzwerk ausgeführt werden.

Die Verbindung bietet Folgendes:

* Schützen
* Private
* Hohe Bandbreite
* Geringe Wartezeit

## <a name="benefits"></a>Vorteile

Eine Azure-Netzwerkverbindung ermöglicht Ihnen Folgendes:

* Verwenden von Azure als Sicherungsziel für virtuelle Computer in Ihrer privaten Cloud
* Bereitstellen von KMS-Servern in Ihrem Azure-Abonnement, um den vSAN-Datenspeicher Ihrer privaten Cloud zu verschlüsseln
* Verwenden von Hybridanwendungen, bei denen die Webebene der jeweiligen Anwendung in der öffentlichen Cloud ausgeführt wird, während die Anwendungs- und die Datenbankebene in Ihrer privaten Cloud ausgeführt werden

## <a name="azure-virtual-network-connection"></a>Verbindung mit virtuellem Azure-Netzwerk

Private Clouds können mit Ihren Azure-Ressourcen mithilfe von ExpressRoute verbunden werden.  Sie können diese Verbindung verwenden, um auf verschiedene Ressourcen zuzugreifen, die in Ihrem Azure-Abonnement über Ihre private Cloud ausgeführt werden.  Diese Verbindung ermöglicht es Ihnen, Ihr privates Cloudnetzwerk auf Ihr virtuelles Azure-Netzwerk zu erweitern.

![Azure ExpressRoute-Verbindung mit virtuellem Netzwerk](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute-Verbindung mit lokalem Netzwerk

Sie können Ihre vorhandene Azure ExpressRoute-Verbindung (Netzwerk) mit Ihrer CloudSimple-Region verbinden. Über den ExpressRoute-Dienst Global Reach werden die beiden Netzwerke miteinander verbunden.  Es wird eine Verbindung zwischen dem lokalen und dem CloudSimple-ExpressRoute-Netzwerk hergestellt.  Diese Verbindung ermöglicht es Ihnen, Ihre lokalen Netzwerke auf das private Cloudnetzwerk zu erweitern.

![Lokale ExpressRoute-Verbindung – Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>Nächste Schritte

* [Abrufen von Peering-Informationen für eine Verbindung zwischen Azure Virtual Network und CloudSimple](https://docs.azure.cloudsimple.com/virtual-network-connection)
* [Herstellen einer Verbindung von lokalen Standorten mit CloudSimple mithilfe von ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)

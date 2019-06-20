---
title: Architektur mit einem globalen Transitnetzwerk für Azure Virtual WAN | Microsoft-Dokumentation
description: Erfahren Sie mehr zur Architektur mit einem globalen Transitnetzwerk für Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 05/20/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand global transit network architecture as it relates to Virtual WAN.
ms.openlocfilehash: 114d11f98c6181a03f5ce52527b5e2efea468c42
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65965981"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Architektur mit einem globalen Transitnetzwerk und Azure Virtual WAN

Die Architektur mit einem globalen Transitnetzwerk wird von Unternehmen eingesetzt, um den cloudzentrischen IT-Fußabdruck moderner Unternehmen zu konsolidieren, zu vernetzen und zu steuern. In einem modernen cloudzentrischen Unternehmen muss der Netzwerkdatenverkehr nicht an den Hauptsitz den Unternehmens zurückgeleitet werden. Die Architektur mit einem globalen Transitnetzwerk basiert sowohl auf vertrauten Netzwerkkonzepten als auch auf neuen Konzepten, die so nur in Cloud- und cloudbasierten Architekturen eingesetzt werden.

![Architektur](./media/virtual-wan-global-transit-network-architecture/architecture2.png)

**Abbildung 1: Globales Transitnetzwerk mit Virtual WAN**

Moderne Unternehmen erfordern überall Verbindungen zwischen umfassend verteilten Anwendungen, Daten und Benutzern in der Cloud und lokal. Durch Azure Virtual WAN wird eine Architektur mit einem globalen Transitnetzwerk ermöglicht, da überall n:n-Verbindungen zwischen global verteilten VNETs, Sites, Anwendungen und Benutzern möglich sind. Azure Virtual WAN ist ein von Microsoft verwalteter Dienst. Alle Netzwerkkomponenten, aus denen dieser Dienst besteht, werden von Microsoft gehostet und verwaltet. Weitere Informationen zu Virtual WAN finden Sie im Artikel mit der [Übersicht über Virtual WAN](virtual-wan-about.md).

In der Azure Virtual WAN-Architektur fungieren Azure-Regionen als Hubs, die Sie mit Ihren Zweigstellen verbinden können. Sobald die Zweigstellen verbunden sind, können Sie den Azure-Backbone nutzen, um optional Zweigstelle-zu-VNET- und Zweigstelle-zu-Zweigstelle-Verbindungen herzustellen.

Sie können ein virtuelles WAN erstellen, indem Sie einen Virtual WAN-Hub in der Region erstellen, in der sich die meisten Spokes (Zweigstellen, VNETs, Benutzer) befinden. Stellen Sie anschließend eine Verbindung mit den Spokes her, die sich in einer anderen Region als der Hub befinden. Wenn die Spokes geografisch verteilt sind, können Sie alternativ regionale Hubs erstellen und diese miteinander verbinden. Die Hubs sind zwar alle Teil desselben virtuellen WAN, aber sie können unterschiedlichen regionalen Richtlinien unterliegen.

## <a name="hub"></a>Hub-and-Spoke-Transit

Die Architektur mit einem globalen Transitnetzwerk basiert auf einem klassischen Hub-and-Spoke-Konnektivitätsmodell, bei dem der in der Cloud gehostete Netzwerkhub Übertragungsverbindungen zwischen Endpunkten ermöglicht, die sich in unterschiedlichen Spokes befinden.
  
In diesem Modell kann eine Spoke Folgendes sein:

* ein virtuelles Netzwerk (VNET)
* eine physische Zweigstelle
* ein Remotebenutzer
* Internet

![Diagramm: Hub-and-Spoke-Modell für globalen Transitdatenverkehr](./media/virtual-wan-global-transit-network-architecture/architecture.png)

**Abbildung 2: Hub-and-Spoke-Modell**

In Abbildung 2 sehen Sie den logischen Aufbau des globalen Netzwerks, bei dem geografisch verteilte Benutzer, physische Standorte und virtuelle Netzwerke über einen in der Cloud gehosteten Netzwerkhub miteinander verbunden sind. Durch diese Architektur sind logische Transitverbindungen mit einem Hop zwischen den Netzwerkendpunkten möglich. Die Spokes sind über unterschiedliche Azure-Netzwerkdienste miteinander verbunden (z.B. ExpressRoute oder Site-to-Site-VPNs für physische Zweigstellen, VNET-Peering für virtuelle Netzwerke und Point-to-Site-VPNs für Remotebenutzer).

## <a name="crossregion"></a>Regionsübergreifende Verbindungen

Für ein Unternehmen entspricht der Cloudfußabdruck oft dem physischen Fußabdruck. Die meisten Unternehmen greifen über eine ihren physischen Standorten und Benutzern nahe gelegene Region auf die Cloud zu. Einer der wichtigsten Faktoren für eine Architektur mit einem globalen Netzwerk ist das Ermöglichen regionsübergreifender Verbindungen zwischen Netzwerkentitäten und -endpunkten. Ein Cloudfußabdruck kann sich auf mehrere Regionen erstrecken. Das bedeutet, das der Datenverkehr einer Zweigstelle, die in einer bestimmten Region mit der Cloud verbunden ist, eine Zweigstelle oder ein virtuelles Netzwerk in einer anderen Region mithilfe von Hub-zu-Hub-Konnektivität, die sich aktuell in der Vorschau befindet, erreichen kann.

## <a name="any"></a>N:n-Verbindungen

Durch die Architektur mit einem globalen Transitnetzwerk sind *n:n-Verbindungen* über einen zentralen Netzwerkhub möglich. Diese Architektur macht Verbindungsmodelle mit vermaschten Netzen und teilweise vermaschten Netzen obsolet bzw. minimiert deren Notwendigkeit. Deren Erstellung und Wartung ist komplexer. Zusätzlich kann die Routingsteuerung in Hub-and-Spoke-Modellen im Vergleich zu vermaschten Netzwerken einfacher konfiguriert und gewartet werden.

Durch n:n-Verbindungen können Unternehmen mit einer globalen Architektur global verteilte Benutzer, Zweigstellen, Rechenzentren, VNETs und Anwendungen über einen Transithub miteinander vernetzen. Der Transithub fungiert als globales Transitsystem.

![Datenverkehrspfade](./media/virtual-wan-global-transit-network-architecture/trafficpath.png)

**Abbildung 3: Datenverkehrspfade in Azure Virtual WAN**

Azure Virtual WAN unterstützt die folgenden globalen Transitverbindungsarten. Die Buchstaben in Klammern entsprechen den Buchstaben in Abbildung 3.

* Zweigstelle-zu-VNET (a)  
* Zweigstelle-zu-Zweigstelle (b)
* Remotebenutzer-zu-VNET (c)
* Remotebenutzer-zu-Zweigstelle (d)
* VNET-zu-VNET mit VNET-Peering (e)
* ExpressRoute Global Reach 

### <a name="branchvnet"></a>Zweigstelle-zu-VNET

Pfade zwischen Zweigstellen und VNETs werden primär von Azure Virtual WAN unterstützt. Mit diesem Pfad können Sie Zweigstellen mit Azure-IaaS-Unternehmensworkloads vernetzen, die in Azure-VNETs bereitgestellt wurden. Zweigstellen können über ExpressRoute oder ein Site-to-Site-VPN mit einem virtuellen WAN vernetzt werden. Der Datenverkehr wird an VNETs übertragen, die mit den Hubs des virtuellen WAN über VNET-Verbindungen verbunden sind.

### <a name="branchbranch"></a>Zweigstelle-zu-Zweigstelle

Zweigstellen können mit einem Azure Virtual WAN-Hub über ExpressRoute-Verbindungen und/oder ein Site-to-Site-VPN verbunden sein. Sie können die Zweigstelle mit einem Virtual WAN-Hub verbinden, der sich in einer Region befindet, die der Zweigstelle am nächsten ist.

Durch diese Option können Unternehmen den Azure-Backbone zum Verbinden von Zweigstellen verwenden. Diese Funktion ist zwar verfügbar, Sie sollten aber dennoch die Vorteile der Verbindung von Zweigstellen über Azure Virtual WAN mit den Vorteilen eines privaten WAN vergleichen.

### <a name="usertovnet"></a>Remotebenutzer-zu-VNET

Sie können direkten, sicheren Remotezugriff auf Azure über Point-to-Site-Verbindungen zwischen einem Remotebenutzerclient und einem virtuellen WAN ermöglichen. Remotebenutzer eines Unternehmens müssen so nicht mehr mühsam eine Verbindung über ein Unternehmens-VPN herstellen.

### <a name="usertobranch"></a>Remotebenutzer-zu-Zweigstelle

Mit dem Remotebenutzer-zu-Zweigstelle-Pfad können Remotebenutzer, die über eine Point-to-Site-Verbindung mit Azure verbunden sind, auf lokale Workloads und Anwendungen über die Cloud zugreifen. Durch diesen Pfad können Remotebenutzer flexibel auf Workloads zugreifen, die in Azure oder lokal bereitgestellt wurden. Unternehmen können in Azure Virtual WAN einen zentralen, cloudbasierten, sicheren Remotezugriffsdienst verfügbar machen.

### <a name="vnetvnet"></a>VNET-zu-VNET mit VNET-Peering

Verwenden Sie VNET-Peering, um VNETs miteinander zu verbinden, um mehrschichtige Anwendungen zu unterstützen, die in mehreren VNETs implementiert wurden. Ein VNET-zu-VNET-Transitszenario über Azure Virtual WAN wird aktuell nicht unterstützt, ist aber Teil der Azure-Roadmap. Verbinden von VNETs per VNET-Peering ist die empfohlene Lösung für VNETs, die miteinander verbunden werden müssen. [Gatewaytransit](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) (im Kontext von VNET-Peering) ist für Virtual WAN nicht erforderlich, da Virtual WAN automatisch Gatewaytransit aktiviert.

### <a name="globalreach"></a>ExpressRoute Global Reach

ExpressRoute ist eine private und zuverlässige Möglichkeit, Ihre lokalen Netzwerke mit der Microsoft Cloud zu verbinden. ExpressRoute Global Reach ist ein Add-On-Feature für ExpressRoute. Mithilfe von Global Reach können Sie ExpressRoute-Verbindungen miteinander verbinden, um ein privates Netzwerk zwischen Ihren lokalen Netzwerken aufzubauen. Zweigstellen, die mit Azure Virtual WAN über ExpressRoute verbunden sind, erfordern ExpressRoute Global Reach, um miteinander kommunizieren zu können.

In diesem Modell kann jede Zweigstelle, die mit ExpressRoute mit dem Virtual WAN-Hub verbunden ist, über den Zweigstelle-zu-VNET-Pfad eine Verbindung mit VNETs herstellen. Datenverkehr zwischen Zweigstellen wird nicht an den Hub übertragen, weil ExpressRoute Global Reach den optimaleren Pfad über Azure Virtual WAN ermöglicht.

## <a name="security"></a>Sicherheits- und Richtliniensteuerung

Der VNET-Hub verbindet den gesamten Transitdatenverkehr und kann ihn potenziell einsehen. Er eignet sich für das Hosten zentraler Netzwerkfunktionen und -dienste, z. B. Cloudrouting, Netzwerkrichtlinien und -sicherheit und Internetzugriffssteuerung.

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie eine Verbindung mithilfe von Azure Virtual WAN.

* [Erstellen einer Site-to-Site-Verbindung per Azure Virtual WAN](virtual-wan-site-to-site-portal.md)
* [Erstellen einer Point-to-Site-Verbindung per Azure Virtual WAN](virtual-wan-point-to-site-portal.md)
* [Erstellen einer ExpressRoute-Zuordnung per Azure Virtual WAN](virtual-wan-expressroute-portal.md)

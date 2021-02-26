---
title: Konzepte – Netzwerkinterkonnektivität
description: Erfahren Sie mehr über wichtige Aspekte und Anwendungsfälle für Netzwerke und Interkonnektivität in Azure VMware Solution.
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: bc8a2f8c07a2a4fe37c4899dc33d5173a99dc423
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538974"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Azure VMware Solution – Netzwerk- und Interkonnektivitätskonzepte

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Eine Möglichkeit, Interkonnektivität zu veranschaulichen, besteht darin, die beiden Implementierungsarten von privaten Azure VMware Solution-Clouds zu betrachten:

1. [**Die allgemeine reine Azure-Interkonnektivität**](#azure-virtual-network-interconnectivity) ermöglicht Ihnen die Verwaltung und Nutzung Ihrer privaten Cloud mit nur einem einzelnen virtuellen Netzwerk in Azure. Diese Implementierung eignet sich am besten für Azure VMware Solution-Evaluierungen oder -Implementierungen, die keinen Zugriff von lokalen Umgebungen erfordern.

1. [**Vollständige Interkonnektivität zwischen lokalen und privaten Clouds**](#on-premises-interconnectivity) erweitert die allgemeine reine Azure-Implementierung um die Interkonnektivität zwischen lokalen und privaten Azure VMware Solution-Clouds.
 
In diesem Artikel werden einige wichtige Konzepte, die Netzwerke und Interkonnektivität bilden, sowie Anforderungen und Beschränkungen behandelt. Es werden auch weitere Informationen zu den beiden Arten der Implementierungen der privaten Azure VMware Solution-Cloudinterkonnektivität behandeln. In diesem Artikel erfahren Sie, wie Sie Ihr Netzwerk ordnungsgemäß für die Verwendung von Azure VMware Solution konfigurieren.

## <a name="azure-vmware-solution-private-cloud-use-cases"></a>Anwendungsfälle für private Azure VMware Solution-Clouds

Die Anwendungsfälle für private Azure VMware Solution-Clouds umfassen:
- Neue VMware-VM-Workloads in der Cloud
- Bursting von VM-Workloads in die Cloud (nur lokal zu Azure VMware Solution)
- Migration von VM-Workloads in die Cloud (nur lokal zu Azure VMware Solution)
- Notfallwiederherstellung (Azure VMware Solution zu Azure VMware Solution oder lokal zu Azure VMware Solution)
- Nutzung von Azure-Diensten

> [!TIP]
> Bei allen Anwendungsfällen für den Azure VMware Solution-Dienst bestehen Verbindungen zwischen lokalen Standorten und privaten Clouds.

## <a name="azure-virtual-network-interconnectivity"></a>Azure Virtual Network-Interkonnektivität

In der Implementierung vom virtuellen Netzwerk zur privaten Cloud können Sie Ihre private Azure VMware Solution-Cloud verwalten, Workloads in Ihrer privaten Cloud verarbeiten und auf Azure-Dienste über die ExpressRoute-Leitung zugreifen. 

Im folgenden Diagramm ist die grundlegende Netzwerkinterkonnektivität dargestellt, die während der Bereitstellung einer privaten Cloud hergestellt wird. Das Diagramm zeigt logische ExpressRoute-basierte Netzwerkverbindungen zwischen einem virtuellen Netzwerk in Azure und einer privaten Cloud. Die Interkonnektivität wird drei der primären Anwendungsfälle gerecht:
* Eingehender Zugriff auf vCenter Server und NSX-T Manager, der von den virtuellen Computern in Ihrem Azure-Abonnement und nicht von Ihren lokalen Systemen aus zugänglich ist. 
* Ausgehender Zugriff von VMs auf Azure-Dienste. 
* Eingehender Zugriff und Nutzung von Workloads, die in einer privaten Cloud ausgeführt werden.

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Basiskonnektivität zwischen einem virtuellen Netzwerk und einer privaten Cloud" border="false":::

## <a name="on-premises-interconnectivity"></a>Lokale Interkonnektivität

Bei der Implementierung vom lokalen oder virtuellen Netzwerk bis hin zur vollständigen privaten Cloud können Sie von lokalen Umgebungen aus auf Ihre privaten Azure VMware Solution-Clouds zugreifen. Diese Implementierung ist eine Erweiterung der grundlegenden Implementierung, die im vorherigen Abschnitt beschrieben wurde. Wie bei der grundlegenden Implementierung ist eine ExpressRoute-Leitung erforderlich, aber bei dieser Implementierung wird sie verwendet, um eine Verbindung von lokalen Umgebungen mit Ihrer privaten Cloud in Azure herzustellen. 

Das nachstehende Diagramm zeigt die Interkonnektivität von lokalen zu privaten Clouds, die die folgenden Anwendungsfälle ermöglicht:
* Hot/Cold Cross-vCenter vMotion
* Verwaltungszugriff von der lokalen Umgebung auf die private Azure VMware Solution-Cloud

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Vollständige Konnektivität zwischen einem virtuellen Netzwerk und einer lokalen privaten Cloud" border="false":::

Für eine vollständige Interkonnektivität mit Ihrer privaten Cloud aktivieren Sie ExpressRoute Global Reach und fordern dann einen Autorisierungsschlüssel und eine private Peering-ID für Global Reach im Azure-Portal an. Der Autorisierungsschlüssel und die Peering-ID werden verwendet, um Global Reach zwischen einer ExpressRoute-Leitung in Ihrem Abonnement und der ExpressRoute-Leitung für Ihre neue private Cloud zu aktivieren. Sobald die ExpressRoute-Leitungen verknüpft wurden, wird der Netzwerkdatenverkehr zwischen lokalen Umgebungen und der privaten Cloud über die beiden Leitungen geroutet.  Weitere Informationen zum Anfordern und Verwenden von Autorisierungsschlüssel und Peering-ID finden Sie im [Tutorial zum Erstellen eines ExpressRoute Global Reach-Peerings mit einer privaten Cloud](tutorial-expressroute-global-reach-private-cloud.md).

## <a name="next-steps"></a>Nächste Schritte 

Nachdem Sie sich mit den Netzwerk- und Konnektivitätskonzepten bei Azure VMware Solution vertraut gemacht haben, informieren Sie sich über die folgenden Themen:

- [Speicherkonzepte von Azure VMware Solution](concepts-storage.md)
- [Identitätskonzepte von Azure VMware Solution](concepts-identity.md)
- [Aktivieren einer Azure VMware Solution-Ressource](enable-azure-vmware-solution.md)

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->


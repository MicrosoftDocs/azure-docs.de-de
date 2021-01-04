---
title: 'Szenario: Benutzerdefiniertes Routing über Azure Firewall für Virtual WAN'
titleSuffix: Azure Virtual WAN
description: Routingszenarios – direktes Routing von Datenverkehr zwischen VNets, jedoch Verwendung von Azure Firewall für die Datenverkehrsflüsse VNet > Internet/Branch und Branch > VNet
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: d083607782f96744ecbd7d23976f77ee53fec49d
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94515568"
---
# <a name="scenario-azure-firewall---custom"></a>Szenario: Azure Firewall: Benutzerdefiniert

Wenn Sie mit Virtual WAN-Routing für virtuelle Hubs arbeiten, steht Ihnen eine ganze Reihe von Szenarios zur Verfügung. In diesem Szenario besteht das Ziel darin, Datenverkehr zwischen VNETs direkt weiterzuleiten, für die Datenverkehrsflüsse vom VNET zum Internet/Branch und vom Branch zum VNET hingegen Azure Firewall zu verwenden.

## <a name="design"></a><a name="design"></a>Entwurf

Sie können eine Verbindungsmatrix erstellen, in der jede Zelle angibt, ob eine Quelle (Zeile) mit einem Ziel (Spalte) kommunizieren kann, um herauszufinden, wie viele Routingtabellen erforderlich sind. Die Verbindungsmatrix in diesem Szenario ist trivial, aber konsistent mit anderen Szenarios, sodass sie dennoch angesehen werden kann.

**Konnektivitätsmatrix**

| Von           | Nach:      | *VNETs*      | *Branches*    | *Internet*   |
|---             |---       |---           |---            |---           |
| **VNETs**      |   &#8594;|    Direkt    |     AzFW      |     AzFW     |
| **Branches**   |   &#8594;|    AzFW      |    Direkt     |    Direkt    |

In der obigen Tabelle stellt ein „Direkt“ die direkte Konnektivität zwischen zwei Verbindungen dar, ohne dass der Datenverkehr Azure Firewall in Virtual WAN durchläuft, und „AzFW“ zeigt an, dass der Flow Azure Firewall durchläuft. Da in der Matrix zwei verschiedene Konnektivitätsmuster vorhanden sind, benötigen Sie zwei Routingtabellen, die wie folgt konfiguriert werden:

* Virtuelle Netzwerke:
  * Zugeordnete Routingtabelle: **RT_VNet**
  * Weitergabe an Routingtabellen: **RT_VNet**
* Branches:
  * Zugeordnete Routingtabelle: **Standard**
  * Weitergabe an Routingtabellen: **Standard**

> [!NOTE]
> Sie können eine separate Virtual WAN-Instanz mit einem einzelnen geschützten virtuellen Hub in jeder Region erstellen. Anschließend können Sie die einzelnen Virtual WAN über ein Site-to-Site-VPN miteinander verbinden.

Weitere Informationen zum Routing für virtuelle Hubs finden Sie unter [Informationen zum Routing virtueller Hubs](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="workflow"></a>Workflow

In diesem Szenario möchten Sie Datenverkehr von einem VNet zum Internet oder Branch und von einem Branch zu einem VNet über Azure Firewall weiterleiten, während der Datenverkehr zwischen VNets direkt weitergeleitet werden soll. Wenn Sie Azure Firewall Manager verwendet haben, werden die Routeneinstellungen automatisch in die **Standardroutingtabelle** eingetragen. Privater Datenverkehr gilt für VNet und Branches, Internetdatenverkehr gilt für 0.0.0.0/0.

VPN-, ExpressRoute- und Benutzer-VPN-Verbindungen werden zusammen als Branches bezeichnet und derselben (Standard-)Routingtabelle zugeordnet. Alle VPN-, ExpressRoute- und Benutzer-VPN-Verbindungen geben Routen an dieselben Routingtabellen weiter. Führen Sie zur Konfiguration dieses Szenarios die folgenden Schritte aus:

1. Erstellen Sie eine benutzerdefinierte Routingtabelle **RT_VNet**.
1. Erstellen Sie eine Route, um VNet zu Internet und VNet zu Branch: 0.0.0.0/0 mit dem nächsten Hop zu aktivieren, der auf Azure Firewall zeigt. Stellen Sie im Abschnitt „Verteilung“ sicher, dass VNets ausgewählt werden, sodass spezifischere Routen gewährleistet werden und dadurch ein direkter Datenverkehrsfluss von VNet zu VNet möglich wird.

   * Unter **Zuordnung**: Wählen Sie VNETs aus, sodass VNETs das Ziel gemäß den Routen dieser Routingtabelle erreichen.
   * Unter **Weitergabe**: Wählen Sie VNETs aus, sodass die Weitergabe der VNETs an diese Routingtabelle erfolgt. Die Weitergabe an diese Routingtabelle erfolgt also mit spezifischeren Routen, was einen direkten Datenverkehrsfluss zwischen den VNETs sicherstellt.

1. Fügen Sie eine aggregierte statische Route für VNets zur **Standardroutingtabelle** hinzu, um den Datenverkehrsfluss vom Branch zum VNet über Azure Firewall zu aktivieren.

   * Beachten Sie, dass Branches verknüpft sind und bei ihnen die Weitergabe an die Standardroutingtabelle erfolgt.
   * Branches führen keine Weitergabe an die Routingtabelle „RT_VNet“ durch. Hierdurch wird sichergestellt, dass der Datenverkehrsfluss vom VNet zum Branch über Azure Firewall erfolgt.

Dies führt, wie in **Abbildung 1** gezeigt, zu Änderungen an der Routingkonfiguration.

**Abbildung 1**

:::image type="content" source="./media/routing-scenarios/between-vnets-firewall/routing.png" alt-text="Abbildung 1":::

> [!NOTE]
> Die Virtual WAN-Hubs und die verbundenen virtuellen Netzwerke sollten sich in derselben Azure-Region befinden.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Virtual WAN finden Sie unter [Häufig gestellte Fragen](virtual-wan-faq.md).
* Weitere Informationen zum Routing für virtuelle Hubs finden Sie unter [Informationen zum Routing virtueller Hubs](about-virtual-hub-routing.md).
* Weitere Informationen zum Konfigurieren des Routings für virtuelle Hubs finden Sie unter [Konfigurieren des Routings für virtuelle Hubs](how-to-virtual-hub-routing.md).

---
title: 'Szenario: Weiterleiten von Datenverkehr über ein virtuelles Netzwerkgerät (NVA)'
titleSuffix: Azure Virtual WAN
description: Weiterleiten von Datenverkehr über ein virtuelles Netzwerkgerät
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 78ff0440fa83b6bd002cdf4256dc066342b1b390
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424751"
---
# <a name="scenario-route-traffic-through-an-nva"></a>Szenario: Weiterleiten von Datenverkehr über ein virtuelles Netzwerkgerät

Wenn Sie mit Virtual WAN-Routing für virtuelle Hubs arbeiten, stehen Ihnen eine ganze Reihe von Szenarien zur Verfügung. In diesem NVA-Szenario besteht das Ziel darin, den Datenverkehr durch ein virtuelles Netzwerkgerät (Network Virtual Appliance, NVA) für Branch-zu-VNet und VNet-zu-Branch zu leiten. Weitere Informationen zum Routing für virtuelle Hubs finden Sie unter [Informationen zum Routing virtueller Hubs](about-virtual-hub-routing.md).

> [!NOTE]
> Wenn Sie bereits über eine Einrichtung mit Routen verfügen, die vor der Verfügbarkeit der in [Konfigurieren des Routings für virtuelle Hubs](how-to-virtual-hub-routing.md) beschriebenen neuen Möglichkeiten liegen, befolgen Sie die Schritte in diesen Versionen der Artikel:
>* [Artikel zum Azure-Portal](virtual-wan-route-table-nva-portal.md)
>* [Artikel zu PowerShell](virtual-wan-route-table-nva.md)
>

## <a name="design"></a><a name="design"></a>Entwurf

In diesem Szenario wird die folgende Benennungskonvention verwendet:

* „NVA-VNETs“ für virtuelle Netzwerke, in denen Benutzer eine NVA bereitgestellt und andere virtuelle Netzwerke als Spokes verbunden haben (VNET 2 und VNET 4 unten in der **Konnektivitätsmatrix** ).
* „NVA-Spokes“ für virtuelle Netzwerke, die mit einem NVA-VNET (VNET 5, VNET 6, VNET 7 und VNET 8 unten in der **Konnektivitätsmatrix** ) verbunden sind.
* „Nicht-NVA-VNETs“ für mit Virtual WAN verbundene virtuelle Netzwerke, für die kein NVA vorhanden ist bzw. keine anderen VNETs per Peering verbunden sind (VNET 1 und VNET 3 unten in der **Konnektivitätsmatrix** ).
* „Hubs“ für von Microsoft verwaltete Virtual WAN-Hubs, mit denen NVA-VNETs verbunden sind. NVA-Spoke-VNETs müssen nicht mit Virtual WAN-Hubs verbunden sein, sondern nur mit NVA-VNETs.

In der folgenden Konnektivitätsmatrix sind die für dieses Szenario unterstützten Datenflüsse zusammengefasst:

**Konnektivitätsmatrix**

| From             | Nach:|   *NVA-Spokes*|*NVA-VNETs*|*Nicht-NVA-VNETs*|*Branches*|
|---|---|---|---|---|---|
| **NVA-Spokes**   | &#8594; | Über NVA-VNET | Peering | Über NVA-VNET | Über NVA-VNET |
| **NVA-VNETs**    | &#8594; | Peering | Direkt | Direkt | Direkt |
| **Nicht-NVA-VNETs**| &#8594; | Über NVA-VNET | Direkt | Direkt | Direkt |
| **Branches**     | &#8594; | Über NVA-VNET | Direkt | Direkt | Direkt |

In den einzelnen Zellen der Konnektivitätsmatrix ist jeweils beschrieben, wie ein VNET oder Branch (Von-Seite des Flows bzw. Zeilenüberschrift in der Tabelle) mit einem Ziel-VNET oder Zielbranch (An-Seite des Flows bzw. kursive Spaltenüberschrift in der Tabelle) kommuniziert. „Direkt“ bedeutet, dass die Konnektivität nativ von Virtual WAN bereitgestellt wird. „Peering“ bedeutet, dass die Konnektivität durch eine benutzerdefinierte Route im VNET bereitgestellt wird. „Über NVA-VNET“ bedeutet, dass die Konnektivität das im NVA-VNET bereitgestellte NVA durchläuft. Beachten Sie Folgendes:

* NVA-Spokes werden nicht mit Virtual WAN verwaltet. Die Mechanismen, über die die Kommunikation mit anderen VNETs oder Branches erfolgt, werden also vom Benutzer verwaltet. Die Konnektivität mit dem NVA-VNET wird per VNET-Peering bereitgestellt, und eine Standardroute zu „0.0.0.0/0“, die auf das NVA als nächsten Hop verweist, sollte die Konnektivität für das Internet, die anderen Spokes und die Branches abdecken.
* NVA-VNETs kennen ihre eigenen NVA-Spokes, aber keine NVA-Spokes, die mit anderen NVA-VNETs verbunden sind. In Tabelle 1 ist VNET 2 über VNET 5 und VNET 6 informiert, aber nicht über andere Spokes, z. B. VNET 7 und VNET 8. Eine statische Route ist erforderlich, um die Präfixe anderer Spokes in NVA-VNETs einzufügen.
* Auch Branches und NVA-fremde VNETs sind nicht über NVA-Spokes informiert, da NVA-Spokes nicht mit Virtual WAN-Hubs verbunden sind. Aus diesem Grund werden hier ebenfalls statische Routen benötigt.

Die NVA-Spokes werden wie oben erwähnt nicht per Virtual WAN verwaltet, aber in allen restlichen Zeilen ist das gleiche Konnektivitätsmuster angegeben. Daher deckt eine einzelne Routingtabelle (Standardtabelle) Folgendes ab:

* Virtuelle Netzwerke (Nicht-Hub-VNETs und Benutzerhub-VNETs):
  * Zugeordnete Routingtabelle: **Standard**
  * Weitergabe an Routingtabellen: **Standard**
* Branches:
  * Zugeordnete Routingtabelle: **Standard**
  * Weitergabe an Routingtabellen: **Standard**

In diesem Szenario müssen wir aber überlegen, welche statischen Routen konfiguriert werden sollen. Jede statische Route verfügt über zwei Komponenten: einen Teil im Virtual WAN-Hub, der den Virtual WAN-Komponenten mitteilt, welche Verbindung für die einzelnen Spokes verwendet werden soll, und einen anderen Teil innerhalb der spezifischen Verbindung, der auf die konkrete IP-Adresse verweist, die dem NVA zugewiesen ist (oder einem Lastenausgleichsmodul vor mehreren NVAs). Dies ist in **Abbildung 1** dargestellt:

**Abbildung 1**

:::image type="content" source="media/routing-scenarios/nva/nva-static-concept.png" alt-text="Abbildung 1":::

Hierbei lauten die statischen Routen, die wir in der Standardtabelle zum Senden von Datenverkehr an die NVA-Spokes hinter dem NVA-VNET benötigen, wie folgt:

| BESCHREIBUNG | Routingtabelle | Statische Route              |
| ----------- | ----------- | ------------------------- |
| VNet 2       | Standard     | 10.2.0.0/16 -> eastusconn |
| VNET 4       | Standard     | 10.4.0.0/16 -> weconn     |

Das Virtual WAN verfügt jetzt über die Information, an welche Verbindung die Pakete gesendet werden sollen. Die Verbindung muss aber auch wissen, was zu tun ist, wenn diese Pakete empfangen werden: Hierfür werden die Routingtabellen für Verbindungen verwendet. Hier verwenden wir die kürzeren Präfixe („/24“ anstelle des längeren „/16“), um sicherzustellen, dass diese Routen Vorrang vor Routen erhalten, die aus den NVA-VNETs (VNET 2 und VNET 4) importiert werden:

| BESCHREIBUNG | Verbindung | Statische Route            |
| ----------- | ---------- | ----------------------- |
| VNET 5       | eastusconn | 10.2.1.0/24 -> 10.2.0.5 |
| VNET 6       | eastusconn | 10.2.2.0/24 -> 10.2.0.5 |
| VNET 7       | weconn     | 10.4.1.0/24 -> 10.4.0.5 |
| VNET 8       | weconn     | 10.4.2.0/24 -> 10.4.0.5 |

Jetzt verfügen NVA-VNETs, Nicht-NVA-VNETs und Branches über die Information, wie alle NVA-Spokes erreicht werden können. Weitere Informationen zum Routing für virtuelle Hubs finden Sie unter [Informationen zum Routing virtueller Hubs](about-virtual-hub-routing.md).

## <a name="architecture"></a><a name="architecture"></a>Architektur

In **Abbildung 2** gibt es zwei Hubs: **Hub1** und **Hub2**.

* **Hub1** und **Hub2** sind direkt mit den NVA-VNETs **VNET 2** und **VNET 4** verbunden.

* **VNET 5** und **VNET 6** sind mit **VNET 2** per Peering verbunden.

* **VNET 7** und **VNET 8** sind mit **VNET 4** per Peering verbunden.

* **VNETs 5, 6, 7 und 8** sind indirekte Spokes, die nicht direkt mit einem virtuellen Hub verbunden sind.

**Abbildung 2**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="Abbildung 2" lightbox="./media/routing-scenarios/nva/nva.png":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Szenarioworkflow

Zur Einrichtung des Routings über das virtuelle Netzwerkgerät sind die folgenden Schritte zu beachten:

1. Identifizieren Sie die NVA-Spoke-VNet-Verbindung. In **Abbildung 2** sind dies **VNET 2-Verbindung (eastusconn)** und **VNET 4-Verbindung (weconn)** .

   Stellen Sie sicher, dass UDRs eingerichtet sind:
   * Von VNET 5 und VNET 6 zu VNET 2-NVA-IP-Adresse
   * Von VNET 7 und VNET 8 zu VNET 4-NVA-IP-Adresse 
   
   Sie müssen die VNETs 5, 6, 7 und 8 nicht direkt mit den virtuellen Hubs verbinden. Stellen Sie sicher, dass die Netzwerksicherheitsgruppen in den VNETs 5, 6, 7 und 8 den Datenverkehr für den Branch (VPN/ER/P2S) oder für VNETs zulassen, die mit ihren Remote-VNETs verbunden sind. Beispielsweise muss für die VNETs 5 und 6 sichergestellt sein, dass die Netzwerksicherheitsgruppen den Datenverkehr für lokale Adresspräfixe und die VNETs 7 und 8 zulassen, die mit dem Remotehub 2 verbunden sind.

Virtual WAN unterstützt kein Szenario, in dem VNet 5 und VNet 6 eine Verbindung mit einem virtuellen Hub herstellen und über die NVA-IP-Adresse von VNet 2 kommunizieren. Daher ist es erforderlich, VNet 5 und VNet 6 mit VNet 2 bzw. VNet 7 und VNet 8 mit VNet 4 zu verbinden.

2. Fügen Sie der Standardroutingtabelle von Hub 1 einen aggregierten statischen Routeneintrag für die VNETs 2, 5 und 6 hinzu.

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="Beispiel":::

3. Konfigurieren Sie eine statische Route für die VNETs 5 und 6 in der virtuellen Netzwerkverbindung von VNET 2. Informationen zum Einrichten der Routingkonfiguration für eine virtuelle Netzwerkverbindung finden Sie unter [Routing virtueller Hubs](how-to-virtual-hub-routing.md#routing-configuration).

4. Fügen Sie der Standardroutingtabelle von Hub 1 einen aggregierten statischen Routeneintrag für die VNETs 4, 7 und 8 hinzu.

5. Wiederholen Sie die Schritte 2, 3 und 4 für die Standardroutingtabelle von Hub 2.

Dies führt, wie unten in **Abbildung 3** dargestellt, zu Änderungen an der Routingkonfiguration.

**Abbildung 3**

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="Abbildung 3" lightbox="./media/routing-scenarios/nva/nva-result.png":::

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Virtual WAN finden Sie unter [Häufig gestellte Fragen](virtual-wan-faq.md).
* Weitere Informationen zum Routing für virtuelle Hubs finden Sie unter [Informationen zum Routing virtueller Hubs](about-virtual-hub-routing.md).

---
title: 'Szenario: Benutzerdefinierte Isolation für virtuelle Netzwerke und Branches'
titleSuffix: Azure Virtual WAN
description: 'Routingszenarien: Verhindern, dass ausgewählte VNets und Branches einander erreichen können'
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: wellee
ms.openlocfilehash: e8e5a5a1b9325f40fdd51133155a0daffaa55a7b
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99396240"
---
# <a name="scenario-custom-isolation-for-virtual-networks-and-branches"></a>Szenario: Benutzerdefinierte Isolation für virtuelle Netzwerke und Branches

Wenn Sie mit Virtual WAN-Routing für virtuelle Hubs arbeiten, steht Ihnen eine ganze Reihe von Szenarios zur Verfügung. In einem benutzerdefinierten Isolationsszenario sowohl für virtuelle Netzwerke (VNets) als auch für Branches besteht das Ziel darin, zu verhindern, dass ein bestimmter Satz von VNets einen anderen Satz von VNets erreicht. Ebenso dürfen Branches (VPN/ER/Benutzer-VPN) nur bestimmte Sätze von VNets erreichen.

Wir führen auch die zusätzliche Anforderung ein, dass Azure Firewall den Datenverkehr von Branch zu VNet und VNet zu Branch überprüfen soll, **nicht** aber den Datenverkehr von VNet zu VNet.  

Weitere Informationen zum Routing für virtuelle Hubs finden Sie unter [Informationen zum Routing virtueller Hubs](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Entwurf

Um herauszufinden, wie viele Routingtabellen erforderlich sind, können Sie eine Verbindungsmatrix erstellen. Für dieses Szenario sieht diese wie die folgende Tabelle aus, in der jede Zelle angibt, ob eine Quelle (Zeile) mit einem Ziel (Spalte) kommunizieren kann:

| From | Nach:| *Blaue VNets* | *Rote VNets* | *Rote Branches*| *Blaue Branches*| 
|---|---|---|---|---|---|
| **Blaue VNets** |   &#8594;|   Direkt     |           |   |  AzFW|
| **Rote VNets**  |   &#8594;|              |   Direkt  |  AzFW  | 
| **Rote Branches**   |   &#8594;|   |   AzFW  |  Direkt | Direkt
| **Blaue Branches**| &#8594;| AzFW  |   |Direkt   | Direkt

Jede Zelle in der vorstehenden Tabelle beschreibt, ob eine Virtual WAN-Verbindung (die Seite „Von“ des Flows, die Zeilenüberschriften) mit einem Ziel (die Seite „Zu“ des Flows, die kursiv gesetzten Spaltenüberschriften) kommuniziert. **Direkt** bedeutet, dass der Datenverkehr direkt durch Virtual WAN fließt, während **AzFW** bedeutet, dass der Datenverkehr von Azure Firewall geprüft wird, bevor er an das Ziel weitergeleitet wird. Ein leerer Eintrag bedeutet, dass der Flow im Setup gesperrt ist.

In diesem Fall sind die beiden Routingtabellen für die VNets erforderlich, um das Ziel der VNet-Isolation ohne Azure Firewall im Pfad zu erreichen. Wir nennen diese Routingtabellen **RT_BLUE** und **RT_RED**.

Außerdem müssen Branches immer mit der **Standardroutingtabelle** verbunden sein. Um sicherzustellen, dass der Datenverkehr zu und von den Branches von Azure Firewall geprüft wird, fügen wir statische Routen in den Routingtabellen **Standard**, **RT_RED** und **RT_BLUE** hinzu, die den Datenverkehr zu Azure Firewall leiten, und richten Netzwerkregeln ein, um den gewünschten Datenverkehr zuzulassen. Außerdem stellen wir sicher, dass die Branches **nicht** an **RT_BLUE** und **RT_RED** weitergegeben werden.

Dies ist somit der endgültige Entwurf:

* Blaue virtuelle Netzwerke:
  * Zugeordnete Routingtabelle: **RT_BLUE**
  * Weitergabe an Routingtabellen: **RT_BLUE**
* Rote virtuelle Netzwerke:
  * Zugeordnete Routingtabelle: **RT_RED**
  * Weitergabe an Routingtabellen: **RT_RED** 
* Branches:
  * Zugeordnete Routingtabelle: **Standard**
  * Weitergabe an Routingtabellen: **Standard**
* Statische Routen:
    * **Standardroutingtabelle**: Virtual Network-Adressräume mit Azure Firewall im nächsten Hop
    * **RT_RED**: 0.0.0.0/0 mit Azure Firewall im nächsten Hop
    * **RT_BLUE**: 0.0.0.0/0 mit Azure Firewall im nächsten Hop
* Firewallnetzwerkregeln:
    * **ZULASSUNGSREGEL** – **Quellpräfix**: Adresspräfix des blauen Branch – **Zielpräfix**: Blaue VNet-Präfixe 
    * **ZULASSUNGSREGEL** – **Quellpräfix**: Adresspräfix des roten Branch – **Zielpräfix**: Rote VNet-Präfixe

> [!NOTE]
> Da alle Zweigstellen der Routingtabelle „Standard“ zugeordnet sein und Daten an dieselben Routingtabellen weitergeben müssen, verfügen sie alle über das gleiche Konnektivitätsprofil. Anders ausgedrückt: Das Konzept mit Rot und Blau für VNets kann nicht auf Zweigstellen angewendet werden. Um jedoch ein benutzerdefiniertes Routing für Branches zu erreichen, können wir den Datenverkehr von den Branches zu Azure Firewall weiterleiten.

> [!NOTE]
> Azure Firewall verweigert standardmäßig den Datenverkehr in einem Zero Trust-Modell. Wenn es keine explizite **Zulassungsregel** gibt, die mit dem untersuchten Paket übereinstimmt, verwirft Azure Firewall das Paket.

Weitere Informationen zum Routing für virtuelle Hubs finden Sie unter [Informationen zum Routing virtueller Hubs](about-virtual-hub-routing.md).



## <a name="workflow"></a><a name="architecture"></a>Workflow

In **Abbildung 1** gibt es blaue und rote VNets sowie Branches, die entweder auf blaue oder rote VNets zugreifen können.

* Blaue VNets können sich gegenseitig und alle Verbindungen mit blauen Branches (VPN/ER/P2S) erreichen. Im Diagramm ist der blaue Branch der Site-to-Site-VPN-Standort.
* Rote VNets können sich gegenseitig und alle Verbindungen mit roten Branches (VPN/ER/P2S) erreichen. Im Diagramm ist der rote Branch der Point-to-Site-VPN-Benutzer.

Führen Sie zum Einrichten des Routings die folgenden Schritte aus.

1. Erstellen Sie im Azure-Portal zwei benutzerdefinierte Routingtabellen, **RT_BLUE** und **RT_RED**, um den Datenverkehr zu diesen VNets anzupassen.
2. Wenden Sie für die Routingtabelle **RT_BLUE** die folgenden Einstellungen an, um sicherzustellen, dass blaue VNets die Adresspräfixe aller anderen blauen VNets lernen:
   * **Zuordnung:** Wählen Sie alle blauen VNets aus.
   * **Weitergabe**: Wählen Sie alle blauen VNets aus.
3. Wiederholen Sie diese Schritte für die Routingtabelle **RT_RED** für rote VNets.
4. Stellen Sie eine Azure Firewall in Virtual WAN bereit. Weitere Informationen zu Azure Firewall im Virtual WAN-Hub finden Sie unter [Konfigurieren von Azure Firewall im Virtual WAN-Hub](howto-firewall.md).
5. Fügen Sie der **Standard**-Routingtabelle des virtuellen Hubs eine statische Route hinzu, die den gesamten Datenverkehr, der für die VNet-Adressräume (sowohl blau als auch rot) bestimmt ist, zu Azure Firewall leitet. Mit diesem Schritt wird sichergestellt, dass alle Pakete aus Ihren Branches zur Überprüfung an Azure Firewall gesendet werden.
    * Beispiel: **Zielpräfix**:  10.0.0.0/24 **Nächster Hop**: Azure Firewall
    >[!NOTE]
    > Dieser Schritt kann auch über Firewall Manager erfolgen, indem Sie die Option „Secure Private Traffic“ (Privaten Datenverkehr sichern) auswählen. Dadurch wird eine Route für alle privaten RFC1918-IP-Adressen hinzugefügt, die für VNets und Branches gelten. Sie müssen alle Branches oder virtuellen Netzwerke, die nicht mit RFC1918 konform sind, manuell hinzufügen. 

6. Fügen Sie eine statische Route zu **RT_RED** und **RT_BLUE** hinzu, die den gesamten Datenverkehr zu Azure Firewall leitet. Dieser Schritt stellt sicher, dass VNets nicht direkt auf Branches zugreifen können. Dieser Schritt kann nicht über Firewall Manager durchgeführt werden, da diese virtuellen Netzwerke nicht mit der Standardroutingtabelle verknüpft sind.
    * Beispiel: **Zielpräfix**: 0.0.0.0/0 **Nächster Hop**: Azure Firewall

    > [!NOTE]
    > Das Routing wird mithilfe der längsten Präfixübereinstimmung (Longest Prefix Match, LPM) durchgeführt. Infolgedessen werden die statischen Routen 0.0.0.0/0 **NICHT** gegenüber den exakten Präfixen bevorzugt, die in **BLUE_RT** und **RED_RT** vorhanden sind. Daher wird der VNet-interne Datenverkehr nicht von Azure Firewall überprüft.

Dies führt zu Änderungen der Routingkonfiguration, wie in der folgenden Abbildung dargestellt.

**Abbildung 1**
[ ![Abbildung 1](./media/routing-scenarios/custom-branch-vnet/custom-branch.png) ](./media/routing-scenarios/custom-branch-vnet/custom-branch.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Virtual WAN finden Sie unter [Häufig gestellte Fragen](virtual-wan-faq.md).
* Weitere Informationen zum Routing für virtuelle Hubs finden Sie unter [Informationen zum Routing virtueller Hubs](about-virtual-hub-routing.md).

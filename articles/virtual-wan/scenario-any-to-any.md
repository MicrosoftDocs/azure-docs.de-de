---
title: 'Szenario: Any-to-Any'
titleSuffix: Azure Virtual WAN
description: 'Szenarien für das Routing: Any-to-Any'
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 3b5002873160490dfb7b8d3ad9790f9c6f1e8ae6
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525787"
---
# <a name="scenario-any-to-any"></a>Szenario: Any-to-Any

Wenn Sie mit Virtual WAN-Routing für virtuelle Hubs arbeiten, stehen Ihnen eine ganze Reihe von Szenarien zur Verfügung. In einem Any-to-Any-Szenario kann jeder Spoke einen anderen Spoke erreichen. Wenn mehrere Hubs vorhanden sind, ist das Hub-zu-Hub-Routing (auch als Routing zwischen Hubs bezeichnet) im virtuellen Standard-WAN standardmäßig aktiviert. Sie können diese Konfiguration mit einer Vielzahl verschiedener Methoden erstellen, z. B. über das Azure-Portal oder mit einer [Azure-Schnellstartvorlage](quickstart-any-to-any-template.md). Weitere Informationen zum Routing für virtuelle Hubs finden Sie unter [Informationen zum Routing virtueller Hubs](about-virtual-hub-routing.md). 

## <a name="design"></a><a name="design"></a>Entwurf

Um herauszufinden, wie viele Routingtabellen in einem Virtual WAN-Szenario erforderlich sind, können Sie eine Verbindungsmatrix erstellen, in der jede Zelle angibt, ob eine Quelle (Zeile) mit einem Ziel (Spalte) kommunizieren kann.

| From |   To |  *VNETs* | *Branches* |
| -------------- | -------- | ---------- | ---|
| VNETs     | &#8594;| Direkt | Direkt |
| Branches   | &#8594;| Direkt  | Direkt |

Die Zellen in der vorherigen Tabelle beschreiben, ob eine Virtual WAN-Verbindung (die Seite „Von“ des Flows, die Zeilenüberschriften) mit einem Zielpräfix (die Seite „Zu“ des Flows, die kursiven Spaltenüberschriften) kommuniziert. In diesem Szenario gibt es keine Firewalls oder virtuellen Netzwerkgeräte, sodass die Kommunikation direkt über Virtual WAN erfolgt (daher die Angabe „direkt“ in der Tabelle).

Da für alle Verbindungen von VNETs und Branches (VPN, ExpressRoute und Benutzer-VPN) die gleichen Konnektivitätsanforderungen gelten, ist eine einzelne Routingtabelle erforderlich. Dadurch werden alle Verbindungen verknüpft und an dieselbe Routingtabelle weitergegeben, nämlich die Standardroutingtabelle:

* Virtuelle Netzwerke:
  * Zugeordnete Routingtabelle: **Standard**
  * Weitergabe an Routingtabellen: **Standard**
* Branches:
  * Zugeordnete Routingtabelle: **Standard**
  * Weitergabe an Routingtabellen: **Standard**

Weitere Informationen zum Routing für virtuelle Hubs finden Sie unter [Informationen zum Routing virtueller Hubs](about-virtual-hub-routing.md).

## <a name="architecture"></a><a name="architecture"></a>Architektur

In **Abbildung 1** können alle VNETs und Branches (VPN, ExpressRoute, P2S) einander erreichen. In einem virtuellen Hub funktionieren die Verbindungen wie folgt:

* Eine VPN-Verbindung verbindet einen VPN-Standort mit einem VPN-Gateway.
* Eine VNET-Verbindung verbindet ein virtuelles Netzwerk mit einem virtuellen Hub. Der Router des virtuellen Hubs bietet die Transitfunktionalität zwischen VNETs.
* Eine ExpressRoute-Verbindung verbindet eine ExpressRoute-Leitung mit einem ExpressRoute-Gateway.

Diese Verbindungen werden (standardmäßig bei Erstellung) der Standardroutingtabelle zugeordnet, sofern Sie die Routingkonfiguration der Verbindung nicht als **Keine** oder eine benutzerdefinierte Routingtabelle einrichten. Diese Verbindungen übergeben Routen standardmäßig an die Standardroutingtabelle. Dadurch wird ein Any-to-Any-Szenario ermöglicht, in dem alle Spokes (VNET, VPN, ER, P2S) einander erreichen können.

**Abbildung 1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="Abbildung 1":::

## <a name="workflow"></a><a name="workflow"></a>Workflow

Dieses Szenario ist für das virtuelle Standard-WAN standardmäßig aktiviert. Wenn die Einstellungen für Zweigstelle-zu-Zweigstelle in der WAN-Konfiguration deaktiviert sind, sind keine Verbindungen zwischen Zweigstellenspokes möglich. VPN/ExpressRoute/Benutzer-VPN werden in Virtual WAN als Branchspokes angesehen.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Virtual WAN finden Sie unter [Häufig gestellte Fragen](virtual-wan-faq.md).
* Weitere Informationen zum Routing für virtuelle Hubs finden Sie unter [Informationen zum Routing virtueller Hubs](about-virtual-hub-routing.md).

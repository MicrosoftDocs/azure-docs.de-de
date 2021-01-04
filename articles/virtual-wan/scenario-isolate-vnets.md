---
title: 'Szenario: Isolieren von virtuellen Netzwerken (VNETs)'
titleSuffix: Azure Virtual WAN
description: 'Szenarien für Routing: Isolieren von virtuellen Netzwerken (VNETs)'
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: e5e2ce17be6d8a1fa82d8a92b9b788f0bd2a37b8
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424746"
---
# <a name="scenario-isolating-vnets"></a>Szenario: Isolieren von virtuellen Netzwerken (VNETs)

Wenn Sie mit Virtual WAN-Routing für virtuelle Hubs arbeiten, stehen Ihnen eine ganze Reihe von Szenarien zur Verfügung. In diesem Szenario besteht das Ziel darin zu verhindern, dass VNETs andere VNETs erreichen können. Dies wird als Isolieren von VNETs bezeichnet. Weitere Informationen zum Routing für virtuelle Hubs finden Sie unter [Informationen zum Routing virtueller Hubs](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Entwurf

In diesem Szenario bleibt die Workload in einem bestimmten VNET isoliert und kann nicht mit anderen VNETs kommunizieren. Allerdings müssen die VNETs alle Zweige (VPN, ER und Benutzer-VPN) erreichen können. Um herauszufinden, wie viele Routingtabellen erforderlich sind, können Sie eine Verbindungsmatrix erstellen. In diesem Szenario sieht diese wie die folgende Tabelle aus, in der jede Zelle angibt, ob eine Quelle (Zeile) mit einem Ziel (Spalte) kommunizieren kann:

| From |   To |  *VNETs* | *Branches* |
| -------------- | -------- | ---------- | ---|
| VNETs     | &#8594;| Direkt |   Direkt    |
| Branches   | &#8594;|  Direkt  |   Direkt    |

Die Zellen in der vorherigen Tabelle beschreiben, ob eine Virtual WAN-Verbindung (die Seite „Von“ des Flows, die Zeilenüberschriften) mit einem Zielpräfix (die Seite „Zu“ des Flows, die kursiven Spaltenüberschriften) kommuniziert. In diesem Szenario gibt es keine Firewalls oder virtuellen Netzwerkgeräte, sodass die Kommunikation direkt über Virtual WAN erfolgt (daher das Wort „Direkt“ in der Tabelle).

Diese Verbindungsmatrix gibt zwei verschiedene Zeilenmuster an, die in zwei Routingtabellen übersetzt werden. Virtual WAN verfügt bereits über eine Standardroutingtabelle, daher ist eine weitere Routingtabelle erforderlich. In diesem Beispiel nennen wir die Routingtabelle **RT_VNET**.

VNETs werden dieser Routingtabelle **RT_VNET** zugeordnet. Da die Branches Konnektivität zu Branches benötigen, müssen sie an **RT_VNET** weitergegeben werden (andernfalls werden die Branchpräfixe nicht von den VNETs erlernt). Da die Branches immer der Standardroutingtabelle zugeordnet sind, müssen die VNETs an die Standardroutingtabelle weitergegeben werden. Dies ist somit der endgültige Entwurf:

* Virtuelle Netzwerke:
  * Zugeordnete Routingtabelle: **RT_VNET**
  * Weitergabe an Routingtabellen: **Standard**
* Branches:
  * Zugeordnete Routingtabelle: **Standard**
  * Weitergabe an Routingtabellen: **RT_VNET** und **Standard**

Da nur Branches an die Routingtabelle **RT_VNET** weitergegeben werden, sind dies die einzigen Präfixe, die VNETs erlernen, nicht die anderer VNETs.

Weitere Informationen zum Routing für virtuelle Hubs finden Sie unter [Informationen zum Routing virtueller Hubs](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="workflow"></a>Workflow

Führen Sie zur Konfiguration dieses Szenarios die folgenden Schritte aus:

1. Erstellen Sie eine benutzerdefinierte Routingtabelle in jedem Hub. Im Beispiel ist **RT_VNet** die Routingtabelle. Informationen, wie eine Routingtabelle erstellt wird, finden Sie unter [Konfigurieren des Routings für virtuelle Hubs](how-to-virtual-hub-routing.md). Weitere Informationen über Routingtabellen finden Sie unter [Informationen zum Routing virtueller Hubs](about-virtual-hub-routing.md).
2. Wenn Sie die **RT_VNet** -Routingtabelle erstellen, konfigurieren Sie die folgenden Einstellungen:

   * **Zuordnung:** Wählen Sie die VNETs aus, die Sie isolieren möchten.
   * **Weitergabe** : Wählen Sie die Option für Zweige aus, was impliziert, dass Zweigverbindungen (VPN/ER/P2S-Verbindungen) Routen an diese Routingtabelle weitergeben.

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="Isolierte VNETs":::

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Virtual WAN finden Sie unter [Häufig gestellte Fragen](virtual-wan-faq.md).
* Weitere Informationen zum Routing für virtuelle Hubs finden Sie unter [Informationen zum Routing virtueller Hubs](about-virtual-hub-routing.md).

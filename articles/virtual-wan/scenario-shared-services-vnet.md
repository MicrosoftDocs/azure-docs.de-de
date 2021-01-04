---
title: 'Szenario: Routen zu VNETs für gemeinsame Dienste'
titleSuffix: Azure Virtual WAN
description: 'Routingszenarios: Einrichten von Routen für den Zugriff auf ein VNET für gemeinsame Dienste mit einer Workload, auf die alle VNETs und Branches zugreifen können.'
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 03c71664769f1518ba80d36867c71ef35b2ca026
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461463"
---
# <a name="scenario-route-to-shared-services-vnets"></a>Szenario: Routen zu VNETs für gemeinsame Dienste

Wenn Sie mit Virtual WAN-Routing für virtuelle Hubs arbeiten, steht Ihnen eine ganze Reihe von Szenarios zur Verfügung. In diesem Szenario richten Sie Routen für den Zugriff auf ein VNET für **gemeinsame Dienste** mit Workloads ein, auf die alle VNETs und Branches (VPN/ER/P2S) zugreifen können sollen. Solche gemeinsam genutzten Workloads können beispielsweise Virtual Machines mit Diensten wie Domänencontrollern oder Dateifreigaben sein oder Azure-Dienste, die intern über [private Azure-Endpunkte](../private-link/private-endpoint-overview.md) verfügbar gemacht werden.

Weitere Informationen zum Routing für virtuelle Hubs finden Sie unter [Informationen zum Routing virtueller Hubs](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Entwurf

Wir können eine Konnektivitätsmatrix verwenden, um die Anforderungen dieses Szenarios zusammenzufassen:

**Konnektivitätsmatrix**

| Von             | Zu:   |*Isolierte VNETs*|*Gemeinsam genutzte VNETs*|*Branches*|
|---|---|---|---|---|
|**Isolierte VNETs**|&#8594;|        | Direkt | Direkt |
|**Gemeinsam genutzte VNETs**  |&#8594;| Direkt | Direkt | Direkt |
|**Branches**      |&#8594;| Direkt | Direkt | Direkt |

Jede Zelle in der vorstehenden Tabelle beschreibt, ob eine Virtual WAN-Verbindung (die Seite „Von“ des Flows, die Zeilenüberschriften) mit einem Ziel (die Seite „Zu“ des Flows, die kursiv gesetzten Spaltenüberschriften) kommuniziert. In diesem Szenario gibt es keine Firewalls oder virtuellen Netzwerkgeräte, sodass die Kommunikation direkt über Virtual WAN erfolgt (daher das Wort „direkt“ in der Tabelle).

Ähnlich wie das [Szenario mit isolierten VNETs](scenario-isolate-vnets.md) bietet diese Konnektivitätsmatrix zwei Zeilenmuster, die sich in zwei Routingtabellen übersetzen lassen (die VNETs für gemeinsame Dienste und Filialen weisen dieselben Konnektivitätsanforderungen auf). Ein Virtual WAN verfügt bereits über eine Standardroutingtabelle, wir benötigen also eine weitere benutzerdefinierte Routingtabelle. Diese wird in diesem Beispiel als **RT_SHARED** bezeichnet.

VNETs werden der Routingtabelle **RT_SHARED** zugeordnet. Da Konnektivität mit Branches und den VNETs für gemeinsame Dienste gegeben sein muss, ist deren Weitergabe an **RT_SHARED** erforderlich (andernfalls haben die VNETs keine Kenntnis über Präfixe für Branches und gemeinsam genutzte VNETs). Die Branches sind immer der Standardroutingtabelle zugeordnet, und die Konnektivitätsanforderungen entsprechen denen für VNETs für gemeinsame Dienste. Daher ordnen wir auch die VNETs für gemeinsame Dienste der Standardroutingtabelle zu.

Dies ist somit der endgültige Entwurf:

* Isolierte virtuelle Netzwerke:
  * Zugeordnete Routingtabelle: **RT_SHARED**
  * Weitergabe an Routingtabellen: **Standard**
* Virtuelle Netzwerke für gemeinsame Dienste:
  * Zugeordnete Routingtabelle: **Standard**
  * Weitergabe an Routingtabellen: **RT_SHARED** und **Standard**
* Branches:
  * Zugeordnete Routingtabelle: **Standard**
  * Weitergabe an Routingtabellen: **RT_SHARED** und **Standard**

> [!NOTE]
> Wenn Ihr Virtual WAN regionsübergreifend bereitgestellt ist, müssen Sie die Routingtabelle **RT_SHARED** in jedem Hub erstellen. Routen von jeder Verbindung aus einem VNET für gemeinsame Dienste oder einem Branch müssen über Weitergabebezeichnungen an die Routingtabellen in jedem virtuellen Hub weitergegeben werden.

Weitere Informationen zum Routing für virtuelle Hubs finden Sie unter [Informationen zum Routing virtueller Hubs](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="workflow"></a>Workflow

Führen Sie die folgenden Schritte aus, um das Szenario zu konfigurieren:

1. Identifizieren Sie das VNET für **gemeinsame Dienste**.
2. Erstellen Sie eine benutzerdefinierte Routingtabelle. Im Beispiel wird die Routingtabelle als **RT_SHARED** bezeichnet. Eine Anleitung zum Erstellen einer Routingtabelle finden Sie unter [Konfigurieren des Routings für virtuelle Hubs](how-to-virtual-hub-routing.md). Verwenden Sie als Richtlinie die folgenden Werte:

   * **Zuordnung**
     * Wählen Sie für **VNETs *mit Ausnahme* des VNETs für gemeinsame Dienste** die VNETs aus, die isoliert werden sollen. Dadurch können alle ausgewählten VNETs (mit Ausnahme des VNETs für gemeinsame Dienste) basierend auf den Routen der Tabelle „RT_SHARED“ das Ziel erreichen.

   * **Weitergabe**
      * Geben Sie für **Branches** zusätzlich zu weiteren Routingtabellen, die Sie möglicherweise bereits ausgewählt haben, Routen an diese Routingtabelle weiter. Durch diesen Schritt wird die Routingtabelle „RT_SHARED“ über die Routen aller Branchverbindungen (VPN/ER/Benutzer-VPN) informiert.
      * Wählen Sie für **VNETs** das **VNET für gemeinsame Dienste** aus. Durch diesen Schritt wird die Routingtabelle „RT_SHARED“ über die Routen der Verbindung des VNETs für gemeinsame Dienste informiert.

Dies führt, wie in der folgenden Abbildung gezeigt, zu Änderungen an der Routingkonfiguration:

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="VNET für gemeinsame Dienste" lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Virtual WAN finden Sie unter [Häufig gestellte Fragen](virtual-wan-faq.md).
* Weitere Informationen zum Routing für virtuelle Hubs finden Sie unter [Informationen zum Routing virtueller Hubs](about-virtual-hub-routing.md).

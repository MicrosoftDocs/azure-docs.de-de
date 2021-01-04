---
title: Weiterleiten von Datenverkehr über virtuelle Netzwerkgeräte (Network Virtual Appliances, NVAs) mithilfe benutzerdefinierter Einstellungen
titleSuffix: Azure Virtual WAN
description: Dieses Szenario hilft Ihnen beim Weiterleiten von Datenverkehr über NVAs mithilfe eines anderen NVA für Internetdatenverkehr.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 122e76e4bde96823ff18207bc24df4a8e91afb1c
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517967"
---
# <a name="scenario-route-traffic-through-nvas-by-using-custom-settings"></a>Szenario: Weiterleiten von Datenverkehr über virtuelle Netzwerkgeräte (Network Virtual Appliances, NVAs) mithilfe benutzerdefinierter Einstellungen

Wenn Sie mit dem Routing virtueller Hubs von Azure Virtual WAN arbeiten, stehen Ihnen eine Reihe von Optionen zur Verfügung. Der Schwerpunkt dieses Artikels liegt darauf, wenn Sie Datenverkehr für Kommunikation zwischen virtuellen Netzwerken und Filialen durch ein virtuelles Netzwerkgerät (Network Virtual Appliance, NVA) leiten möchten und für Internetdatenverkehr ein anderes NVA verwenden möchten. Weitere Informationen finden Sie unter [Informationen zum Routing virtueller Hubs](about-virtual-hub-routing.md).

## <a name="design"></a>Entwurf

* **Spokes** für virtuelle Netzwerke, die mit dem virtuellen Hub verbunden sind. (Beispiel: VNET 1, VNET 2 und VNET 3 im Diagramm weiter unten in diesem Artikel.)
* **Dienst-VNET** für virtuelle Netzwerke, in denen Benutzer ein NVA bereitgestellt haben, um den Nicht-Internetdatenverkehr zu überprüfen, und möglicherweise mit gemeinsamen Diensten, auf die über Spokes zugegriffen wird. (Beispiel: VNET 4 im Diagramm weiter unten in diesem Artikel.) 
* **Umkreis-VNET** für virtuelle Netzwerke, in denen Benutzer ein NVA für die Untersuchung von Internetdatenverkehr bereitgestellt haben. (Beispiel: VNET 5 im Diagramm weiter unten in diesem Artikel.)
* **Hubs** für von Microsoft verwaltete Virtual WAN-Hubs.

In der folgenden Tabelle werden die in diesem Szenario unterstützten Verbindungen zusammengefasst:

| From          | Beschreibung|Spokes|Dienst-VNET|Branches|Internet|
|---|---|:---:|:---:|:---:|:---:|:---:|
| **Spokes**| ->| direkt |direkt | über Dienst-VNET |über Umkreis-VNET |
| **Dienst-VNET**| ->| direkt |–| direkt | |
| **Branches** | ->| über Dienst-VNET |direkt| direkt |  |

Jede der Zellen in der Konnektivitätsmatrix beschreibt, ob Konnektivität direkt über Virtual WAN oder über eines der virtuellen Netzwerke mit einem NVA bereitgestellt wird. 

Beachten Sie Folgendes:
* Spokes:
  * Spokes können andere Spokes direkt über Virtual WAN-Hubs erreichen.
  * Spokes erhalten die Konnektivität mit Branches über eine statische Route, die auf das Dienst-VNET verweist. Sie erlernen keine spezifischen Präfixe von den Filialen, weil diese spezifischer sind und die Zusammenfassung außer Kraft setzen.
  * Spokes senden Internetdatenverkehr über ein direktes VNET-Peering an das Umkreis-VNET.
* Branches erreichen Spokes per statischem Routing, wobei auf das Dienst-VNET verwiesen wird. Sie erlernen keine spezifischen Präfixe von den VNETs, die die zusammengefasste statische Route außer Kraft setzen.
* Das Dienst-VNET ähnelt einem VNET für gemeinsame Dienste, das für jedes VNET und jede Filiale erreichbar sein muss.
* Das Umkreis-VNET benötigt keine Konnektivität über Virtual WAN, da der einzige Datenverkehr, den es unterstützt, über direkte VNET-Peerings erfolgt. Um die Konfiguration zu vereinfachen, verwenden Sie jedoch dasselbe Konnektivitätsmodell wie für das Umkreis-VNET.

Es gibt drei verschiedene Konnektivitätsmuster, die in drei Routingtabellen übersetzt werden. Die Zuordnungen zu den verschiedenen virtuellen Netzwerken sind wie folgt:

* Spokes:
  * Zugeordnete Routingtabelle: **RT_V2B**
  * Weitergabe an Routingtabellen: **RT_V2B** und **RT_SHARED**
* NVA-VNETs (Dienst-VNET und Umkreis-VNET):
  * Zugeordnete Routingtabelle: **RT_SHARED**
  * Weitergabe an Routingtabellen: **RT_SHARED**
* Branches:
  * Zugeordnete Routingtabelle: **Standard**
  * Weitergabe an Routingtabellen: **RT_SHARED** und **Standard**

Diese statischen Routen stellen sicher, dass der Datenverkehr zum und vom virtuellen Netzwerk und der Filiale über das NVA im Dienst-VNET (VNET 4) läuft:

| BESCHREIBUNG | Routingtabelle | Statische Route              |
| ----------- | ----------- | ------------------------- |
| Branches    | RT_V2B      | 10.2.0.0/16 -> vnet4conn  |
| NVA-Spokes  | Standard     | 10.1.0.0/16 -> vnet4conn  |

Jetzt können Sie mit Virtual WAN die richtige Verbindung auswählen, an die die Pakete gesendet werden sollen. Sie müssen Virtual WAN auch verwenden, um die richtige Aktion auszuwählen, die beim Empfang dieser Pakete erfolgen soll. Dazu nutzen Sie die Verbindungsroutingtabellen wie folgt:

| BESCHREIBUNG | Verbindung | Statische Route            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16 -> 10.4.0.5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16 -> 10.4.0.5 |

Weitere Informationen finden Sie unter [Informationen zum Routing virtueller Hubs](about-virtual-hub-routing.md).

## <a name="architecture"></a>Aufbau

Hier sehen Sie ein Diagramm der weiter oben in diesem Artikel beschriebenen Architektur.

Es gibt einen Hub mit dem Namen **Hub 1**.

* **Hub 1** ist direkt mit den NVA-VNETs **VNET 4** und **VNET 5** verbunden.

* Datenverkehr zwischen den VNETs 1, 2 und 3 und den Filialen wird über **NVA-VNET 4** (10.4.0.5) erwartet.

* Der gesamte Internetdatenverkehr aus den VNETs 1, 2 und 3 wird über **NVA-VNET 5** (10.5.0.5) erwartet.

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="Diagramm der Netzwerkarchitektur":::

## <a name="workflow"></a>Workflow

Zur Einrichtung des Routings über das virtuelle Netzwerkgerät sind die folgenden Schritte zu beachten:

1. Damit Internetdatenverkehr über VNET 5 geleitet wird, müssen die VNETs 1, 2 und 3 per VNET-Peering eine direkte Verbindung mit VNET 5 herstellen. Außerdem benötigen Sie eine benutzerdefinierte Route, die in den virtuellen Netzwerken für 0.0.0.0/0 und den nächsten Hop 10.5.0.5 eingerichtet ist. Zurzeit lässt Virtual WAN im virtuellen Hub für 0.0.0.0/0 kein NVA für den nächsten Hop zu.

1. Wechseln Sie im Azure-Portal zu Ihrem virtuellen Hub, und erstellen Sie eine benutzerdefinierte Routingtabelle mit dem Namen **RT_Shared**. Diese Tabelle erlernt Routen über die Weitergabe aus allen virtuellen Netzwerken und Filialverbindungen. Diese leere Tabelle wird im folgenden Diagramm gezeigt.

   * **Routen:** Sie müssen keine statischen Routen hinzufügen.

   * **Zuordnung:** Wählen Sie die VNETs 4 und 5 aus, was bedeutet, dass die Verbindungen dieser virtuellen Netzwerke der Routingtabelle **RT_Shared** zugeordnet werden.

   * **Weitergabe:** Da Sie möchten, dass alle Filialen und VNET-Verbindungen ihre Routen dynamisch an diese Routingtabelle weitergeben, wählen Sie Filialen und alle VNETs aus.

1. Erstellen Sie die benutzerdefinierte Routingtabelle **RT_V2B** , um den Datenverkehr von den VNETs 1, 2 und 3 an Filialen weiterzuleiten.

   * **Routen:** Fügen Sie einen aggregierten statischen Routeneintrag für Filialen mit dem nächsten Hop als VNET 4-Verbindung hinzu. Konfigurieren Sie eine statische Route in der VNET 4-Verbindung für Filialpräfixe, und geben Sie an, dass der nächste Hop die spezifische IP-Adresse des NVA in VNET 4 sein soll.

   * **Zuordnung:** Wählen Sie alle VNETs aus (1, 2 und 3). Dies bedeutet, dass die VNet-Verbindungen 1, 2 und 3 dieser Routingtabelle zugeordnet werden und in dieser Routingtabelle Routen (statisch und dynamisch über die Weitergabe) erlernen können.

   * **Weitergabe:** Verbindungen geben Routen an Routingtabellen weiter. Durch Auswahl der VNETs 1, 2 und 3 wird die Weitergabe von Routen von den VNETs 1, 2 und 3 an diese Routingtabelle ermöglicht. Es ist nicht erforderlich, Routen von Filialverbindungen an **RT_V2B** weiterzugeben, da der Datenverkehr des Filial-VNET über das NVA in VNET 4 geleitet wird.
  
1. Bearbeiten Sie die Standardroutingtabelle **DefaultRouteTable**.

   Alle VPN-, Azure ExpressRoute- und Benutzer-VPN-Verbindungen sind der Standardroutingtabelle zugeordnet. Alle VPN-, ExpressRoute- und Benutzer-VPN-Verbindungen geben Routen an dieselbe Gruppe von Routingtabellen weiter.

   * **Routen:** Fügen Sie einen aggregierten statischen Routeneintrag für die VNETs 1, 2 und 3 mit dem nächsten Hop als VNET 4-Verbindung hinzu. Konfigurieren Sie eine statische Route sowie drei aggregierte Präfixe in der VNET 4-Verbindung für die VNETs 1, 2 und 3, und geben Sie an, dass der nächste Hop die spezifische IP-Adresse des NVA in VNET 4 sein soll.

   * **Zuordnung:** Vergewissern Sie sich, dass die Option für Filialen (VPN/ER/P2S) ausgewählt ist, und dass lokale Filialverbindungen der Standardroutingtabelle zugeordnet sind.

   * **Weitergabe von:** Vergewissern Sie sich, dass die Option für Filialen (VPN/ER/P2S) ausgewählt ist, und dass lokale Verbindungen Routen an die Standardroutingtabelle weitergeben.

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="Diagramm des Workflows." lightbox="./media/routing-scenarios/nva-custom/figure-2.png":::

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Virtual WAN finden Sie unter [Häufig gestellte Fragen](virtual-wan-faq.md).
* Weitere Informationen zum Routing für virtuelle Hubs finden Sie unter [Informationen zum Routing virtueller Hubs](about-virtual-hub-routing.md).

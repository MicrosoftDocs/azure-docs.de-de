---
title: Protokolle und Metriken
titleSuffix: Azure Virtual WAN
description: Informationen zu Azure Virtual WAN-Protokollen und -Metriken
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/05/2020
ms.author: cherylmc
ms.openlocfilehash: 53b8d74d6eb35347d6ac5b27d12fa5b5eaed2582
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566366"
---
# <a name="azure-virtual-wan-logs-and-metrics"></a>Azure Virtual WAN-Protokolle und -Metriken

Sie können Azure Virtual WAN mit Azure Monitor überwachen. Virtual WAN ist ein Netzwerkdienst, der viele Netzwerk-, Sicherheits- und Routingfunktionen auf einer einzigen Bedienoberfläche vereint. Für Virtual WAN-VPN-Gateways, ExpressRoute-Gateways und Azure Firewall sind über Azure Monitor Protokollierungsfunktionen und Metriken verfügbar. Informationen zu Azure Firewall finden Sie unter [Azure Firewall-Protokolle und -Metriken](../firewall/logs-and-metrics.md).

In diesem Artikel werden die Metriken und Diagnosen erläutert, die über das Portal verfügbar sind. Metriken sind einfach und können Szenarien nahezu in Echtzeit unterstützen. Dadurch lassen sie sich für Warnungen und zur schnellen Problemerkennung einsetzen.

## <a name="metrics"></a>Metriken

Metriken in Azure Monitor sind numerische Werte, die einen Aspekt eines Systems zu einem bestimmten Zeitpunkt beschreiben. Metriken werden jede Minute erfasst und eignen sich gut für Warnmeldungen, da sie häufig abgefragt werden können. Eine Warnung kann mit relativ einfacher Logik schnell ausgelöst werden.

### <a name="site-to-site-vpn-gateways"></a>Site-to-Site-VPN-Gateways

Die folgenden Metriken sind für Site-to-Site-VPN-Gateways in Azure verfügbar:

* **Gatewaybandbreite** – Durchschnittliche aggregierte Site-to-Site-Bandbreite eines Gateways in Bytes pro Sekunde.
* **Bandbreite des Tunnels** – Durchschnittliche Bandbreite eines Tunnels in Bytes pro Sekunde.
* **Vom Tunnel ausgehende Bytes** – Ausgehende Bytes eines Tunnels. 
* **Vom Tunnel ausgehende Pakete** – Ausgehende Paketanzahl für einen Tunnel. 
* **Ausgehende gelöschte Pakete eines Tunnels durch einen TS-Konflikt** – Anzahl der durch einen Konflikt mit dem Datenverkehrsselektor eines Tunnels gelöschten ausgehenden Pakete. 
* **In Tunnel eingehende Bytes** – Eingehende Bytes eines Tunnels. 
* **In Tunnel eingehende Pakete** – Eingehende Paketanzahl für einen Tunnel. 
* **Eingehende gelöschte Pakete eines Tunnels durch einen TS-Konflikt** – Anzahl der durch einen Konflikt mit dem Datenverkehrsselektor eines Tunnels gelöschten eingehenden Pakete. 

### <a name="point-to-site-vpn-gateways"></a>Point-to-Site-VPN-Gateways

Die folgenden Metriken sind für Point-to-Site-VPN-Gateways in Azure verfügbar:

* **P2S-Gatewaybandbreite** – Durchschnittliche aggregierte Point-to-Site-Bandbreite eines Gateways in Bytes pro Sekunde.
* **Anzahl der P2S-Verbindungen** – Anzahl der Point-to-Site-Verbindungen eines Gateways.

### <a name="azure-expressroute-gateways"></a>Azure ExpressRoute-Gateways

Die folgenden Metriken sind für Azure ExpressRoute-Gateways verfügbar:

* **BitsInPerSecond** – In Azure eingehende Bits pro Sekunde.
* **BitsOutPerSecond** – Aus Azure ausgehende Bits pro Sekunde.

### <a name="view-gateway-metrics"></a><a name="metrics-steps"></a>Anzeigen von Gatewaymetriken

Führen Sie die folgenden Schritte aus, um Metriken zu suchen und anzuzeigen:

1. Navigieren Sie im Portal zu dem virtuellen Hub, der das Gateway enthält.

2. Wählen Sie **VPN (Site-to-Site)** aus, um ein Site-to-Site-Gateway zu suchen, **ExpressRoute**, um ein ExpressRoute-Gateway zu suchen, oder **Benutzer-VPN (Point-to-Site)** , um ein Point-to-Site-Gateway zu suchen. Auf der Seite werden die Gatewayinformationen angezeigt. Kopieren Sie diese Informationen. Sie werden diese später verwenden, um die Diagnose mithilfe von Azure Monitor anzuzeigen.

3. Klicken Sie auf **Metriken**.

   :::image type="content" source="./media/logs-metrics/metrics.png" alt-text="Screenshot zeigt den Bereich „VPN (Site-to-Site)“ mit ausgewähltem „In Azure Monitor anzeigen“.":::

4. Auf der Seite **Metriken** können Sie die Metriken anzeigen, an denen Sie interessiert sind.

   :::image type="content" source="./media/logs-metrics/metrics-page.png" alt-text="Screenshot der Seite „Metriken“ mit hervorgehobenen Kategorien.":::

## <a name="diagnostic-logs"></a><a name="diagnostic"></a>Diagnoseprotokolle

### <a name="site-to-site-vpn-gateways"></a>Site-to-Site-VPN-Gateways

Die folgenden Diagnosen sind für Site-to-Site-VPN-Gateways in Azure verfügbar:

* **Gatewaydiagnoseprotokolle** – Gatewayspezifische Diagnosen, z. B. Integrität, Konfiguration, Dienst Updates, sowie zusätzliche Diagnosen.
* **Tunneldiagnoseprotokolle** – Hierbei handelt es sich um Protokolle für IPSec-Tunnel, die z. B. Verbindungs-und Trennungsereignisse für einen Site-to-Site-IPSec-Tunnel, aushandelte SAs und Trennungsgründe, sowie zusätzliche Diagnoseinformationen enthalten.
* **Routendiagnoseprotokolle** – Hierbei handelt es sich um Protokolle in Bezug auf Ereignisse für statische Routen, BGP, Routenaktualisierungen und zusätzliche Diagnosen.
* **IKE-Diagnoseprotokolle** – IKE-spezifische Diagnosen für IPSec-Verbindungen.

### <a name="point-to-site-vpn-gateways"></a>Point-to-Site-VPN-Gateways

Die folgenden Diagnosen sind für Point-to-Site-VPN-Gateways in Azure verfügbar:

* **Gatewaydiagnoseprotokolle** – Gatewayspezifische Diagnosen, z. B. Integrität, Konfiguration, Dienst Updates, sowie weitere Diagnosen.
* **IKE-Diagnoseprotokolle** – IKE-spezifische Diagnosen für IPSec-Verbindungen.
* **P2S-Diagnoseprotokolle** – Diese umfassen P2S-Konfigurations- und Client-Ereignisse für Benutzer-VPNs (Point-to-Site). Dazu zählen die Verbindung/Trennung von Clients, VPN-Clientadresszuweisung sowie andere Diagnosen.

### <a name="view-diagnostic-logs"></a><a name="diagnostic-steps"></a>Anzeigen von Diagnoseprotokollen

Führen Sie die folgenden Schritte aus, um Diagnosen zu suchen und anzuzeigen:

1. Navigieren Sie im Portal zu Ihrer Virtual WAN-Ressource. Wählen Sie im Portal im Abschnitt **Übersicht** der Virtual WAN-Seite **Zusammenfassung** aus, um die Ansicht zu erweitern und Informationen zu Ressourcengruppen abzurufen. Kopieren Sie die Ressourcengruppeninformationen.

   :::image type="content" source="./media/logs-metrics/3.png" alt-text="Screenshot des Abschnitts „Übersicht“ mit einem Pfeil, der auf die Schaltfläche „Kopieren“ zeigt.":::

2. Navigieren Sie im Abschnitt „Überwachung“ zur Ressourcengruppe. Wählen Sie **Diagnoseeinstellungen** aus, und geben Sie dann die Ressourceninformationen ein. Dabei handelt es sich um die Ressourceninformationen, die Sie in Schritt 2 im Abschnitt [Anzeigen von Gatewaymetriken](#metrics-steps) weiter oben in diesem Artikel kopiert haben.

   :::image type="content" source="./media/logs-metrics/4.png" alt-text="Screenshot des Abschnitts „Überwachung“ mit einem Pfeil, der auf die Dropdownliste „Ressource“ zeigt.":::

3. Wählen Sie auf der Seite mit den Ergebnissen **+ Diagnoseeinstellung hinzufügen** und dann eine Option aus. Sie können die Daten an Log Analytics senden, an einen Event Hub streamen oder einfach in einem Speicherkonto archivieren.

   :::image type="content" source="./media/logs-metrics/5.png" alt-text="Seite „Metriken“":::

### <a name="log-analytics-sample-query"></a><a name="sample-query"></a>Log Analytics-Beispielabfrage

Die Protokolle befinden sich im **Azure Log Analytics-Arbeitsbereich**. Sie können in Log Analytics eine Abfrage einrichten. Das folgende Beispiel enthält eine Abfrage zum Abrufen von Site-to-Site-Routendiagnosen.

```AzureDiagnostics | where Category == "RouteDiagnosticLog"```

Ersetzen Sie die folgenden Werte hinter **= =** je nach Bedarf.

* "GatewayDiagnosticLog"
* "IKEDiagnosticLog"
* "P2SDiagnosticLog”
* "TunnelDiagnosticLog"
* "RouteDiagnosticLog"

## <a name="activity-logs"></a><a name="activity-logs"></a>Aktivitätsprotokolle

Einträge im **Aktivitätsprotokoll** werden standardmäßig erfasst und können im Azure-Portal angezeigt werden. Mit Azure-Aktivitätsprotokollen (ehemals *Betriebsprotokolle* und *Überwachungsprotokolle*) können Sie alle an Ihr Azure-Abonnement übermittelten Vorgänge anzeigen.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Überwachen von Azure Firewall-Protokollen und Metriken finden Sie unter [Tutorial: Überwachen von Azure Firewall-Protokollen](../firewall/firewall-diagnostics.md).
* Weitere Informationen zu Metriken in Azure Monitor finden Sie unter [Metriken in Azure Monitor](../azure-monitor/platform/data-platform-metrics.md).
---
title: Überwachung von Metriken und Protokollen in Azure Front Door Service | Microsoft-Dokumentation
description: Dieser Artikel beschreibt die verschiedenen Metriken und Zugriffsprotokolle, die Azure Front Door Service unterstützt.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: 16770ea0a320b3d9f081cc21a102ab050a6467f6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60736781"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door-service"></a>Überwachung von Metriken und Protokollen in Azure Front Door Service

Mit dem Azure Front Door Service können Sie Ressourcen auf die folgenden Arten überwachen:

- **Metriken:** Application Gateway verfügt derzeit über sieben Metriken, um Leistungsindikatoren anzuzeigen.
- **Protokolle**. Protokolle ermöglichen das Speichern und Nutzen von Leistungs-, Zugriffs- und anderen Daten einer Ressource zu Überwachungszwecken.

### <a name="metrics"></a>metrics

Metriken sind ein Feature für bestimmte Azure-Ressourcen, mit dem Sie die Leistungsindikatoren im Portal anzeigen können. Die folgenden Front Door-Metriken stehen zur Verfügung:

| Metrik | Metrikanzeigename | Unit | Dimensionen | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| RequestCount | Anzahl der Anforderungen | Count | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Die Anzahl der von Front Door Service bereitgestellten Clientanforderungen.  |
| RequestSize | Anforderungsgröße | Byte | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Die Anzahl der von Clients als Anforderungen an Front Door Service gesendeten Bytes. |
| ResponseSize | Antwortgröße | Byte | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Die Anzahl der von Front Door Service als Antworten an Clients gesendeten Bytes. |
| TotalLatency | Gesamtlatenz | Millisekunden | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Die Zeitspanne zwischen dem Empfang der Clientanforderung durch Front Door und der Bestätigung des letzten Antwortbytes von Front Door durch den Client. |
| BackendRequestCount | Back-End-Anforderungsanzahl | Count | HttpStatus</br>HttpStatusGroup</br>Back-End | Die Anzahl der von Front Door Service an Back-Ends gesendeten Anforderungen. |
| BackendRequestLatency | Latenz der Back-End-Anforderung | Millisekunden | Back-End | Die Zeitspanne zwischen dem Senden der Anforderung durch Front Door Service an das Back-End und dem Empfang des letzten Antwortbytes durch Front Door Service vom Back-End. |
| BackendHealthPercentage | Prozentsatz der Back-End-Integrität | Percent | Back-End</br>BackendPool | Der Prozentsatz der erfolgreichen Integritätstests von Front Door Service zu Back-Ends. |
| WebApplicationFirewallRequestCount | Anforderungsanzahl für die Web Application Firewall | Count | PolicyName</br>RuleName</br>Aktion | Die Anzahl der Clientanforderungen, die von der Anwendungssicherheitsschicht von Front Door Service verarbeitet werden. |

## <a name="activity-log"></a>Aktivitätsprotokolle

Aktivitätsprotokolle geben Informationen zu den Front Door Service-Vorgängen. Damit können Sie auch die Antworten auf die Fragen „Was“, „Wer“ und „Wann“ für alle Schreibvorgänge (put, post oder delete) in Ihrer Front Door Service-Instanz ermitteln.

>[!NOTE]
>Aktivitätsprotokolle umfassen keine Lesevorgänge (get). Sie enthalten auch keine Vorgänge, die Sie entweder mithilfe des Azure-Portals oder der ursprünglichen Verwaltungs-API ausführen.

Greifen Sie auf Aktivitätsprotokolle in Ihrer Front Door Service-Instanz oder alle Protokolle Ihrer Azure-Ressourcen in Azure Monitor zu. So zeigen Sie Aktivitätsprotokolle an:

1. Wählen Sie Ihre Front Door-Instanz aus.
2. Wählen Sie **Aktivitätsprotokoll** aus.

    ![Aktivitätsprotokoll](./media/front-door-diagnostics/activity-log.png)

3. Wählen Sie einen Filterbereich und dann **Übernehmen** aus.

## <a name="diagnostic-logging"></a>Diagnoseprotokolle
Diagnoseprotokolle bieten umfassende Informationen zu Vorgängen und Fehlern, die zur Überwachung und Problembehandlung relevant sind. Diagnoseprotokolle unterscheiden sich von Aktivitätsprotokollen.

Aktivitätsprotokolle geben Einblick in die Vorgänge, die für Azure-Ressourcen ausgeführt wurden. Diagnoseprotokolle bieten Einblicke in Vorgänge, die Ihre Ressource ausgeführt hat. Weitere Informationen finden Sie unter [Erfassen und Nutzen von Protokolldaten aus Ihren Azure-Ressourcen](../azure-monitor/platform/diagnostic-logs-overview.md).

![Diagnoseprotokolle](./media/front-door-diagnostics/diagnostic-log.png)

So konfigurieren Sie Diagnoseprotokolle für Ihre Front Door Service-Instanz:

1. Wählen Sie Ihre Azure Front Door Service-Instanz aus.

2. Wählen Sie **Azure-Diagnoseeinstellungen** aus.

3. Wählen Sie **Diagnose aktivieren**. Archivieren Sie Diagnoseprotokolle zusammen mit Metriken in einem Speicherkonto, streamen Sie sie an einen Event Hub, oder senden Sie sie an Azure Monitor-Protokolle.

Front Door Service stellt derzeit Diagnoseprotokolle bereit (stündlicher Batch). Diagnoseprotokolle enthalten einzelne API-Anforderungen, wobei jeder Eintrag folgendem Schema entspricht:

| Eigenschaft  | BESCHREIBUNG |
| ------------- | ------------- |
| ClientIp | Die IP-Adresse des Clients, der die Anforderung gestellt hat. |
| ClientPort | Der IP-Port des Clients, der die Anforderung gestellt hat. |
| HttpMethod | Von der Anforderung verwendete HTTP-Methode |
| HttpStatusCode | Der HTTP-Statuscode, der vom Proxy zurückgegeben wurde. |
| HttpStatusDetails | Resultierender Status in der Anforderung. Informationen zur Bedeutung dieses Zeichenfolgenwerts finden Sie in der Verweistabelle zum Status. |
| HttpVersion | Typ der Anforderung oder Verbindung. |
| RequestBytes | Die Größe der HTTP-Anforderungsnachricht in Byte, einschließlich Anforderungsheader und -text. |
| RequestUri | URI der empfangenen Anforderung |
| ResponseBytes | Die vom Back-End-Server als Antwort gesendeten Bytes.  |
| RoutingRuleName | Der Name der Routingregel, der die Anforderung entspricht. |
| SecurityProtocol | Die TLS-/SSL-Protokollversion, die von der Anforderung verwendet wird, oder NULL, wenn keine Verschlüsselung verwendet wird. |
| TimeTaken | Die Dauer der Aktion in Millisekunden. |
| UserAgent | Der vom Client verwendete Browsertyp |
| TrackingReference | Die eindeutige Verweiszeichenfolge zur Identifizierung einer Anforderung, die von der Front Door-Instanz verarbeitet wird. Diese wird auch als X-Azure-Ref-Header an den Client gesendet. Sie ist für die Suche nach Informationen in den Zugriffsprotokollen für eine bestimmte Anforderung erforderlich. |

## <a name="next-steps"></a>Nächste Schritte

- [Schnellstart: Erstellen einer Front Door-Instanz](quickstart-create-front-door.md)
- [Übersicht über die Routingarchitektur](front-door-routing-architecture.md)

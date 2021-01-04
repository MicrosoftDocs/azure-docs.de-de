---
title: Überwachung von Metriken und Protokollen in Azure Front Door | Microsoft-Dokumentation
description: Dieser Artikel beschreibt die verschiedenen Metriken und Zugriffsprotokolle, die Azure Front Door unterstützt.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: yuajia
ms.openlocfilehash: cd99be40700ab1c34176f2bf7497e4debf5cd424
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483796"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Überwachung von Metriken und Protokollen in Azure Front Door

Mit Azure Front Door können Sie Ressourcen auf die folgenden Arten überwachen:

- **Metriken:** Azure Front Door verfügt derzeit über acht Metriken, um Leistungsindikatoren anzuzeigen.
- **Protokolle**. Protokolle ermöglichen das Speichern und Nutzen von Leistungs-, Zugriffs- und anderen Daten einer Ressource zu Überwachungszwecken.

### <a name="metrics"></a>Metriken

Metriken sind ein Feature für bestimmte Azure-Ressourcen, mit dem Sie die Leistungsindikatoren im Portal anzeigen können. Die folgenden Front Door-Metriken stehen zur Verfügung:

| Metrik | Metrikanzeigename | Einheit | Dimensionen | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| RequestCount | Anzahl der Anforderungen | Anzahl | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Die Anzahl der von Front Door Service bereitgestellten Clientanforderungen.  |
| RequestSize | Anforderungsgröße | Byte | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Die Anzahl der von Clients als Anforderungen an Front Door Service gesendeten Bytes. |
| ResponseSize | Antwortgröße | Byte | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Die Anzahl der von Front Door Service als Antworten an Clients gesendeten Bytes. |
| TotalLatency | Gesamtlatenz | Millisekunden | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Die Gesamtzeit zwischen dem Empfang der Clientanforderung in Front Door und dem letzten von AFD an den Client gesendeten Antwortbyte. |
| BackendRequestCount | Back-End-Anforderungsanzahl | Anzahl | HttpStatus</br>HttpStatusGroup</br>Back-End | Die Anzahl der von Front Door Service an Back-Ends gesendeten Anforderungen. |
| BackendRequestLatency | Latenz der Back-End-Anforderung | Millisekunden | Back-End | Die Zeitspanne zwischen dem Senden der Anforderung durch Front Door Service an das Back-End und dem Empfang des letzten Antwortbytes durch Front Door Service vom Back-End. |
| BackendHealthPercentage | Prozentsatz der Back-End-Integrität | Percent | Back-End</br>BackendPool | Der Prozentsatz der erfolgreichen Integritätstests von Front Door Service zu Back-Ends. |
| WebApplicationFirewallRequestCount | Anforderungsanzahl für die Web Application Firewall | Anzahl | PolicyName</br>RuleName</br>Aktion | Die Anzahl der Clientanforderungen, die von der Anwendungssicherheitsschicht von Front Door Service verarbeitet werden. |

## <a name="activity-logs"></a><a name="activity-log"></a>Aktivitätsprotokolle

Aktivitätsprotokolle geben Informationen zu den Front Door-Vorgängen. Damit können Sie auch die Antworten auf die Fragen „Was“, „Wer“ und „Wann“ für alle Schreibvorgänge (put, post oder delete) in Ihrer Front Door-Instanz ermitteln.

>[!NOTE]
>Aktivitätsprotokolle umfassen keine Lesevorgänge (get). Sie enthalten auch keine Vorgänge, die Sie entweder mithilfe des Azure-Portals oder der ursprünglichen Verwaltungs-API ausführen.

Greifen Sie auf Aktivitätsprotokolle in Ihrer Front Door-Instanz oder alle Protokolle Ihrer Azure-Ressourcen in Azure Monitor zu. So zeigen Sie Aktivitätsprotokolle an:

1. Wählen Sie Ihre Front Door-Instanz aus.
2. Wählen Sie **Aktivitätsprotokoll** aus.

    :::image type="content" source="./media/front-door-diagnostics/activity-log.png" alt-text="Aktivitätsprotokoll":::

3. Wählen Sie einen Filterbereich und dann **Übernehmen** aus.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Diagnoseprotokolle
Diagnoseprotokolle bieten umfassende Informationen zu Vorgängen und Fehlern, die zur Überwachung und Problembehandlung relevant sind. Diagnoseprotokolle unterscheiden sich von Aktivitätsprotokollen.

Aktivitätsprotokolle geben Einblick in die Vorgänge, die für Azure-Ressourcen ausgeführt wurden. Diagnoseprotokolle bieten Einblicke in Vorgänge, die Ihre Ressource ausgeführt hat. Weitere Informationen finden Sie unter [Erfassen und Nutzen von Protokolldaten aus Ihren Azure-Ressourcen](../azure-monitor/platform/platform-logs-overview.md).

:::image type="content" source="./media/front-door-diagnostics/diagnostic-log.png" alt-text="Diagnoseprotokolle":::

So konfigurieren Sie Diagnoseprotokolle für Ihre Front Door-Instanz

1. Wählen Sie Ihre Azure Front Door-Instanz aus.

2. Wählen Sie **Azure-Diagnoseeinstellungen** aus.

3. Wählen Sie **Diagnose aktivieren**. Archivieren Sie Diagnoseprotokolle zusammen mit Metriken in einem Speicherkonto, streamen Sie sie an einen Event Hub, oder senden Sie sie an Azure Monitor-Protokolle.

Front Door stellt derzeit Diagnoseprotokolle bereit (stündlicher Batch). Diagnoseprotokolle enthalten einzelne API-Anforderungen, wobei jeder Eintrag folgendem Schema entspricht:

| Eigenschaft  | BESCHREIBUNG |
| ------------- | ------------- |
| BackendHostname | Wenn die Anforderung an ein Back-End weitergeleitet wurde, stellt dieses Feld den Hostnamen des Back-Ends dar. Dieses Feld ist leer, wenn die Anforderung umgeleitet oder an einen regionalen Cache weitergeleitet wurde (sofern Zwischenspeicherung für die Routingregel aktiviert ist). |
| CacheStatus | Bei Zwischenspeicherungsszenarien definiert dieses Feld den Cachetreffer/Cachefehler im POP. |
| ClientIp | Die IP-Adresse des Clients, der die Anforderung gestellt hat. Wenn in der Anforderung ein X-Forwarded-For-Header vorhanden ist, wird die Client-IP daraus ausgewählt. |
| ClientPort | Der IP-Port des Clients, der die Anforderung gestellt hat. |
| HttpMethod | Von der Anforderung verwendete HTTP-Methode |
| HttpStatusCode | Der HTTP-Statuscode, der vom Proxy zurückgegeben wurde. |
| HttpStatusDetails | Resultierender Status in der Anforderung. Informationen zur Bedeutung dieses Zeichenfolgenwerts finden Sie in der Verweistabelle zum Status. |
| HttpVersion | Typ der Anforderung oder Verbindung. |
| POP | Kurzname des Edges, in dem die Anforderung gelandet ist. |
| RequestBytes | Die Größe der HTTP-Anforderungsnachricht in Byte, einschließlich Anforderungsheader und -text. |
| RequestUri | URI der empfangenen Anforderung |
| ResponseBytes | Die vom Back-End-Server als Antwort gesendeten Bytes.  |
| RoutingRuleName | Der Name der Routingregel, der die Anforderung entspricht. |
| RulesEngineMatchNames | Die Namen der Regeln, mit denen die Anforderung übereinstimmte. |
| SecurityProtocol | Die TLS-/SSL-Protokollversion, die von der Anforderung verwendet wird, oder NULL, wenn keine Verschlüsselung verwendet wird. |
| SentToOriginShield </br> (veraltet) * **Hinweise dazu finden Sie im folgenden Abschnitt.**| Wenn der Wert TRUE ist, wurde die Anforderung vom Origin Shield-Cache anstelle des Edge-POP beantwortet. Origin Shield ist ein übergeordneter Cache zum Verbessern der Cachetrefferquote. |
| isReceivedFromClient | Wenn dies „true“ ergibt, stammte die Anforderung vom Client. Wenn dies „false“ ergibt, ist die Anforderung ein Fehler in der Edge (untergeordneter POP) und wird von Origin Shield (übergeordneter POP) beantwortet. |
| TimeTaken | Die Dauer vom ersten Byte der Anforderung an Front Door bis zum letzten Byte der Antwort in Sekunden. |
| TrackingReference | Die eindeutige Verweiszeichenfolge zur Identifizierung einer Anforderung, die von der Front Door-Instanz verarbeitet wird. Diese wird auch als X-Azure-Ref-Header an den Client gesendet. Sie ist für die Suche nach Informationen in den Zugriffsprotokollen für eine bestimmte Anforderung erforderlich. |
| UserAgent | Der vom Client verwendete Browsertyp |
| ErrorInfo | Dieses Feld enthält den spezifischen Fehlertyp zur weiteren Problembehandlung. </br> Mögliche Werte sind: </br> **NoError**: Gibt an, dass kein Fehler gefunden wurde. </br> **CertificateError**: Generischer SSL-Zertifikatsfehler.</br> **CertificateNameCheckFailed**: Der Hostname im SSL-Zertifikat ist ungültig oder stimmt nicht überein. </br> **ClientDisconnected**: Anforderungsfehler aufgrund der Clientnetzwerkverbindung. </br> **UnspecifiedClientError**: Generischer Clientfehler. </br> **InvalidRequest**: Ungültige Anforderung. Dies kann aufgrund einer falschen Formatierung von Header, Text und URL auftreten. </br> **DNSFailure**: DNS-Fehler. </br> **DNSNameNotResolved**: Der Servername oder die Adresse konnte nicht aufgelöst werden. </br> **OriginConnectionAborted**: Die Verbindung mit dem Ursprung wurde plötzlich beendet. </br> **OriginConnectionError**: Generischer Fehler der Ursprungsverbindung. </br> **OriginConnectionRefused**: Die Verbindung mit dem Ursprung konnte nicht hergestellt werden. </br> **OriginError**: Generischer Ursprungsfehler. </br> **OriginInvalidResponse**: Der Ursprung hat eine ungültige oder unbekannte Antwort zurückgegeben. </br> **OriginTimeout**: Das Zeitlimit für die Ursprungsanforderung ist abgelaufen. </br> **ResponseHeaderTooBig**: Der Ursprung hat einen zu großen Antwortheader zurückgegeben. </br> **RestrictedIP**: Die Anforderung wurde aufgrund einer eingeschränkten IP-Adresse blockiert. </br> **SSLHandshakeError**: Aufgrund eines Fehlers beim SSL-Handshake kann keine Verbindung mit dem Ursprung hergestellt werden. </br> **UnspecifiedError**: Es ist ein Fehler aufgetreten, der keinem Fehler in der Tabelle entspricht. |

### <a name="sent-to-origin-shield-deprecation"></a>An eingestellte Unterstützung von Origin Shield gesendet
Die unformatierte Protokolleigenschaft **isSentToOriginShield** wurde eingestellt und durch ein neues Feld, **isReceivedFromClient**, ersetzt. Verwenden Sie das neue Feld, wenn Sie bereits das veraltete Feld verwenden. 

Unformatierte Protokolle umfassen Protokolle, die sowohl über CDN Edge (untergeordneter POP) als auch Origin Shield generiert wurden. Origin Shield bezieht sich auf übergeordnete Knoten, die strategisch über den Globus verteilt sind. Diese Knoten kommunizieren mit den Ursprungsservern und reduzieren den Datenverkehr am Ursprung. 

Für jede an Origin Shield gerichtete Anforderung gibt es zwei Protokolleinträge:

* Einen für Edgeknoten.
* Einen für Origin Shield. 

Um die ausgehenden Daten oder Antworten von den Edgeknoten von denen von Origin Shield zu unterscheiden, können Sie das Feld **isReceivedFromClient** verwenden, um die richtigen Daten zu erhalten. 

Wenn der Wert „false“ ergibt, bedeutet dies, dass die Anforderung von Origin Shield bis zu den Edgeknoten beantwortet wird. Dieser Ansatz ist effektiv, um unformatierte Protokolle mit Abrechnungsdaten zu vergleichen. Für die ausgehenden Daten von Origin Shield zu den Edgeknoten fallen keine Gebühren an. Es fallen Gebühren für ausgehende Daten von den Edgeknoten zu Clients an. 

**Beispiel für eine Kusto-Abfrage zum Ausschließen von Protokollen, die von Origin Shield in Log Analytics generiert werden.**

`AzureDiagnostics 
| where Category == "FrontdoorAccessLog" and isReceivedFromClient_b == true`

> [!NOTE]
> Für verschiedene Routingkonfigurationen und Datenverkehrsverhalten können einige der Felder wie „backendHostname“, „cacheStatus“, „isReceivedFromClient“ und „POP“ mit unterschiedlichen Werten antworten. In der folgenden Tabelle werden die unterschiedlichen Werte erläutert, die diese Felder in verschiedenen Szenarien enthalten können:

| Szenarien | Anzahl der Protokolleinträge | POP | BackendHostname | isReceivedFromClient | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| Routingregel ohne aktiviertes Zwischenspeichern | 1 | Edge-POP-Code | Das Back-End, an das die Anforderung weitergeleitet wurde | Richtig | CONFIG_NOCACHE |
| Routingregel mit aktiviertem Zwischenspeichern. Cachetreffer beim Edge-POP | 1 | Edge-POP-Code | Leer | Richtig | HIT |
| Routingregel mit aktiviertem Zwischenspeichern. Cachefehler bei Edge-POP, aber Cachetreffer beim übergeordneten Cache-POP | 2 | 1. Edge-POP-Code</br>2. POP-Code des übergeordneten Caches | 1. POP-Hostname des übergeordneten Caches</br>2. Leer | 1. True</br>2. False | 1. MISS</br>2. HIT |
| Routingregel mit aktiviertem Zwischenspeichern. Cachefehler bei Edge-POP, aber teilweiser Cachetreffer beim übergeordneten Cache-POP | 2 | 1. Edge-POP-Code</br>2. POP-Code des übergeordneten Caches | 1. POP-Hostname des übergeordneten Caches</br>2. Back-End zum Auffüllen des Caches | 1. True</br>2. False | 1. MISS</br>2. PARTIAL_HIT |
| Routingregel mit aktiviertem Zwischenspeichern. PARTIAL_HIT des Caches bei POP, aber Cachetreffer beim übergeordneten Cache-POP | 2 | 1. Edge-POP-Code</br>2. POP-Code des übergeordneten Caches | 1. Edge-POP-Code</br>2. POP-Code des übergeordneten Caches | 1. True</br>2. False | 1. PARTIAL_HIT</br>2. HIT |
| Routingregel mit aktiviertem Zwischenspeichern. Cachefehler bei Edge und übergeordnetem Cache-POP | 2 | 1. Edge-POP-Code</br>2. POP-Code des übergeordneten Caches | 1. Edge-POP-Code</br>2. POP-Code des übergeordneten Caches | 1. True</br>2. False | 1. MISS</br>2. MISS |

> [!NOTE]
> Für Cacheszenarien ist der Wert für „Cache Status“ partial_hit, wenn einige Bytes für eine Anforderung aus dem Front Door-Edge- oder Origin Shield-Cache stammen, während einige Bytes aus dem Ursprung für große Objekte stammen.

Bei Azure Front Door Service kommt eine Technik namens Objektblockerstellung zum Einsatz. Wenn eine große Datei angefordert wird, ruft Front Door kleinere Teile der Datei vom Ursprung ab. Nachdem der Front Door-POP-Server die angeforderte Datei vollständig oder auf einen Bytebereich beschränkt empfangen hat, fordert der Front Door-Edgeserver die Datei beim Ursprung in Blöcken zu jeweils 8 MB an.

Nachdem der Block im Front Door -Edgebereich eingegangen ist, wird er zwischengespeichert und sofort für den Benutzer bereitgestellt. Front Door ruft den nächsten Block dann parallel dazu ab. Durch diesen Vorababruf wird sichergestellt, dass der Inhalt dem Benutzer immer einen Block voraus ist, sodass sich die Wartezeit reduziert. Dieser Prozess wird fortgesetzt, bis die gesamte Datei heruntergeladen wurde (falls angefordert), alle Bytebereiche verfügbar sind (falls angefordert) oder der Client die Verbindung schließt. Weitere Informationen zur Bytebereichsanforderung finden Sie unter RFC 7233. Front Door speichert alle Blöcke zwischen, sobald sie eingetroffen sind. Es ist nicht erforderlich, die gesamte Datei im Front Door-Cache zwischenzuspeichern. Nachfolgende Anforderungen für die Datei oder Bytebereiche werden über den Front Door-Cache verarbeitet. Wenn nicht alle Blöcke in Front Door zwischengespeichert werden, wird ein Vorababruf verwendet, um Blöcke vom Ursprung anzufordern. Diese Optimierung beruht auf der Fähigkeit des Ursprungsservers, Bytebereichsanforderungen zu unterstützen. Wenn der Ursprungsserver keine Anforderungen für Bytebereiche unterstützt, ist diese Optimierung nicht effektiv.

## <a name="next-steps"></a>Nächste Schritte

- [Schnellstart: Erstellen einer Front Door-Instanz](quickstart-create-front-door.md)
- [Übersicht über die Routingarchitektur](front-door-routing-architecture.md)

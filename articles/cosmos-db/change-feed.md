---
title: Verwenden der Unterstützung von Änderungsfeeds in Azure Cosmos DB
description: Verwenden Sie die Azure Cosmos DB-Unterstützung von Änderungsfeeds, um Änderungen in Dokumenten sowie ereignisbasierten Verarbeitungen (wie mit Triggern) nachzuverfolgen und Caches und Analysesysteme auf dem neuesten Stand zu halten.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.reviewer: sngun
ms.custom: seodec18, "seo-nov-2020"
ms.openlocfilehash: c8c479050b434904de57397e5e7d73594090e031
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94336130"
---
# <a name="change-feed-in-azure-cosmos-db"></a>Änderungsfeed in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Der Änderungsfeed in Azure Cosmos DB ist eine persistente Aufzeichnung der Änderungen an einem Container in der Reihenfolge ihres Auftretens. Zur Unterstützung des Änderungsfeeds in Azure Cosmos DB wird gelauscht, ob in einem Azure Cosmos-Container Änderungen auftreten. Anschließend wird die sortierte Liste von geänderten Dokumenten in der Reihenfolge ausgegeben, in der sie geändert wurden. Die persistenten Änderungen können asynchron und inkrementell verarbeitet werden, und die Ausgabe kann über einen oder mehrere Consumer für die Parallelverarbeitung verteilt werden.

Erfahren Sie mehr über [Entwurfsmuster für Änderungsfeeds](change-feed-design-patterns.md).

## <a name="supported-apis-and-client-sdks"></a>Unterstützte APIs und Client-SDKs

Dieses Feature wird derzeit von den folgenden Azure Cosmos DB-APIs und Client-SDKs unterstützt.

| **Clienttreiber** | **SQL-API** | **Azure Cosmos DB-API für Cassandra** | **MongoDB-API von Azure Cosmos DB** | **Gremlin-API**|**Tabellen-API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Ja | Ja | Ja | Ja | Nein |
|Java|Ja|Ja|Ja|Ja|Nein|
|Python|Ja|Ja|Ja|Ja|Nein|
|Node/JS|Ja|Ja|Ja|Ja|Nein|

## <a name="change-feed-and-different-operations"></a>Änderungsfeed und verschiedene Vorgänge

Heute werden alle Einfügungen und Updates im Änderungsfeed angezeigt. Sie können den Änderungsfeed nicht nach einen bestimmten Vorgangstyp filtern. Als Alternative können Sie für das Element eine „schwache Markierung“ für Aktualisierungen hinzufügen und dies bei der Verarbeitung von Elementen im Änderungsfeed entsprechend filtern.

Aktuell protokollieren Änderungsfeeds keine Löschvorgänge. Wie im vorherigen Beispiel können Sie den zu löschenden Elementen eine schwache Markierung hinzufügen. Sie können dem Element z. B. das Attribut „deleted“ (gelöscht) hinzufügen und es auf TRUE festlegen sowie eine Gültigkeitsdauer (TTL) für das Element festlegen, damit es automatisch gelöscht werden kann. Sie können den Änderungsfeed nach früheren Elementen durchsuchen (der letzten Änderung, die dem Element entspricht; zwischenzeitliche Änderungen sind nicht enthalten), z.B. nach Elementen, die vor fünf Jahren hinzugefügt wurden. Sie können den Änderungsfeed bis zum Ursprung Ihres Containers lesen, wenn jedoch ein Element gelöscht wird, wird es auch aus dem Änderungsfeed entfernt.

### <a name="sort-order-of-items-in-change-feed"></a>Sortierreihenfolge der Elemente im Änderungsfeed

Elemente im Änderungsfeed sind in der Reihenfolge des Zeitpunkts ihrer letzten Änderung aufgeführt. Diese Sortierreihenfolge ist pro logischem Partitionsschlüssel festgelegt.

### <a name="consistency-level"></a>Konsistenzebene

Bei Verarbeitung des Änderungsfeeds in einer späteren Konsistenzebene können in nachfolgenden Änderungsfeed-Lesevorgängen doppelte Ereignisse auftreten (das letzte Ereignis eines Lesevorgangs wird als erstes des nächsten angezeigt).

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Änderungsfeed in Azure Cosmos-Konten mit mehreren Regionen

Wenn in einem Azure Cosmos-Konto mit mehreren Regionen ein Failover einer Schreibregion eintritt, funktioniert der Änderungsfeed übergreifend über den manuellen Failovervorgang und bleibt zusammenhängend.

### <a name="change-feed-and-time-to-live-ttl"></a>Änderungsfeed und Gültigkeitsdauer (TTL)

Wenn eine TTL-Eigenschaft (Time to Live, Gültigkeitsdauer) für ein Element auf „-1“ festgelegt ist, bleibt der Änderungsfeed dauerhaft erhalten. Wenn die Daten nicht gelöscht werden, verbleiben sie im Änderungsfeed.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Änderungsfeed und „_etag“, „_lsn“ oder „_ts“

Das _etag-Format ist intern, und Sie sollten nicht darauf aufbauen, da es jederzeit geändert werden kann. „_ts“ ist ein Änderungs- oder Erstellungszeitstempel. Sie können „_ts“ für chronologische Vergleiche verwenden. „_lsn“ ist eine Batch-ID, die nur für den Änderungsfeed hinzugefügt wird. Sie stellt die Transaktions-ID dar. Viele Elemente können die gleiche „_lsn“ aufweisen. „ETag“ in der FeedResponse unterscheidet sich von dem „_etag“, das für das Element angezeigt wird. „_etag“ ist ein interner Bezeichner, der für die Gleichzeitigkeitssteuerung verwendet wird. Die _etag-Eigenschaft informiert über die Version des Elements, während die ETag-Eigenschaft zum Sequenzieren des Feeds verwendet wird.

## <a name="working-with-change-feed"></a>Verwenden des Änderungsfeeds

Sie können den Änderungsfeed mit den folgenden Optionen verwenden:

* [Verwenden des Änderungsfeeds in Azure Functions](change-feed-functions.md)
* [Verwenden des Änderungsfeeds mit dem Änderungsfeedprozessor](change-feed-processor.md) 

Der Änderungsfeed ist für jeden logischen Partitionsschlüssel innerhalb des Containers verfügbar. Er kann für eine parallele Verarbeitung über mehrere Consumer verteilt werden, wie in der folgenden Abbildung gezeigt.

:::image type="content" source="./media/change-feed/changefeedvisual.png" alt-text="Verteilte Verarbeitung des Azure Cosmos DB-Änderungsfeeds" border="false":::

## <a name="features-of-change-feed"></a>Features des Änderungsfeeds

* Der Änderungsfeed ist standardmäßig für alle Azure Cosmos-Konten aktiviert.

* Sie können Ihren [bereitgestellten Durchsatz](request-units.md) in allen der Azure Cosmos-Datenbank zugeordneten Regionen zum Lesen aus dem Änderungsfeed verwenden, so wie es auch bei allen anderen Vorgängen von Azure Cosmos DB möglich ist.

* Der Änderungsfeed enthält Einfüge- und Aktualisierungsvorgänge, die für Elemente im Container durchgeführt wurden. Sie können Löschvorgänge durch ein Kennzeichen für „vorläufiges Löschen“ erfassen, das den gelöschten Text in den Elementen (z.B. in Dokumenten) ersetzt. Alternativ können Sie über die [TTL-Funktion](time-to-live.md) einen begrenzten Ablaufzeitraum für die Elemente festlegen, z.B. 24 Stunden, und den Wert dieser Eigenschaft verwenden, um Löschvorgänge zu erfassen. Mit dieser Lösung müssen Sie die Änderungen in einem kürzeren Zeitraum als dem TTL-Ablaufzeitraum verarbeiten.

* Jede Änderung an einem Element wird im Änderungsfeed genau einmal angezeigt, und die Clients müssen ihre Prüfpunktausführungs-Logik verwalten. Wenn Sie die Komplexität der Verwaltung von Prüfpunkten umgehen möchten, bietet der Änderungsfeedprozessor automatische Prüfpunktausführung und „Mindestens einmal“-Semantik. Weitere Informationen finden Sie unter [Verwenden des Änderungsfeeds mit dem Änderungsfeedprozessor](change-feed-processor.md).

* Nur die letzte Änderung für ein bestimmtes Element ist im Änderungsprotokoll enthalten. Zwischenzeitliche Änderungen sind möglicherweise nicht verfügbar.

* Der Änderungsfeed ist anhand der Reihenfolge der Änderungen innerhalb der einzelnen logischen Partitionsschlüsselwerte sortiert. Es gibt keine festgelegte Reihenfolge über Partitionsschlüsselwerte hinweg.

* Änderungen können für jeden Zeitpunkt synchronisiert werden, d.h., es gibt keine feste Datenaufbewahrungsdauer, in der die Änderungen verfügbar sind.

* Änderungen sind für alle logischen Partitionsschlüssel eines Azure Cosmos-Containers parallel möglich. Auf diese Weise können Änderungen aus umfangreichen Containern parallel von mehreren Consumern verarbeitet werden.

* Anwendungen können mehrere Änderungsfeeds gleichzeitig für den gleichen Container anfordern. Mithilfe von „ChangeFeedOptions.StartTime“ kann ein anfänglicher Startpunkt angegeben werden, z.B. um das Fortsetzungstoken zu suchen, das der angegebenen Uhrzeit entspricht. Der ContinuationToken-Wert (sofern angegeben) hat Vorrang vor den Werten StartTime und StartFromBeginning. Die Genauigkeit von „ChangeFeedOptions.StartTime“ beträgt ca. 5 Sekunden.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Änderungsfeed in APIs für Cassandra und MongoDB

Die Funktionen des Änderungsfeeds werden in der MongoDB-API als Änderungsdatenstrom und in der Cassandra-API über Prädikate verfügbar gemacht. Weitere Informationen zu den Implementierungsdetails für die MongoDB-API finden Sie unter [Änderungsdatenströme in der Azure Cosmos DB-API für MongoDB](mongodb-change-streams.md).

Natives Apache Cassandra bietet Change Data Capture (CDC), einen Mechanismus zum Markieren bestimmter Tabellen für die Archivierung sowie zum Ablehnen von Schreibvorgängen in diesen Tabellen, nachdem eine konfigurierbare Größe auf dem Datenträger für das CDC-Protokoll erreicht wurde. Der Änderungsfeed in der Azure Cosmos DB-API für Cassandra verbessert die Möglichkeit, Änderungen mit Prädikaten über CQL abzufragen. Weitere Informationen zu den Implementierungsdetails finden Sie unter [Änderungsfeed in der Azure Cosmos DB-API für Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln erfahren Sie mehr über Änderungsfeeds:

* [Möglichkeiten zum Lesen von Änderungsfeeds](read-change-feed.md)
* [Verwenden des Änderungsfeeds in Azure Functions](change-feed-functions.md)
* [Verwenden des Änderungsfeedprozessors](change-feed-processor.md)

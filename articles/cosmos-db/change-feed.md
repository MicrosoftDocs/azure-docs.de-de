---
title: Verwenden der Unterstützung von Änderungsfeeds in Azure Cosmos DB
description: Verwenden Sie die Azure Cosmos DB-Unterstützung von Änderungsfeeds, um Änderungen in Dokumenten nachzuverfolgen, eine ereignisbasierte Verarbeitung (wie mit Triggern) durchzuführen und Caches und Analysesysteme auf dem neuesten Stand zu halten.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: 51a554586c67842ead40cd4a1bfaaa51bbdd8a18
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65954400"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Änderungsfeed in Azure Cosmos DB – Übersicht

Zur Unterstützung des Änderungsfeeds in Azure Cosmos DB wird gelauscht, ob in einem Azure Cosmos DB-Container Änderungen auftreten. Anschließend wird die sortierte Liste von geänderten Dokumenten in der Reihenfolge ausgegeben, in der sie geändert wurden. Die Änderungen bleiben erhalten und können asynchron und inkrementell verarbeitet werden, und die Ausgabe kann über einen oder mehrere Consumer für eine parallele Verarbeitung verteilt werden. 

Azure Cosmos DB eignet sich besonders gut für IoT-, Spiele-, Verkaufs- und Vorgangsprotokollierungsanwendungen. Ein allgemeines Entwurfsmuster in diesen Anwendungen ist die Verwendung von Datenänderungen, um weitere Aktionen auszulösen. Beispiele für weitere Aktionen sind:

* Auslösen einer Benachrichtigung oder eines Aufrufs einer API, wenn ein Element eingefügt oder aktualisiert wird
* Streamverarbeitung in Echtzeit für IoT oder Analyseverarbeitung in Echtzeit für operative Daten
* Zusätzliche Datenverschiebung durch Synchronisieren mit einem Cache, einer Suchmaschine oder einem Data Warehouse oder durch Archivieren von Daten in Cold Storage

Mit dem Änderungsfeed in Azure Cosmos DB können Sie effiziente und skalierbare Lösungen für jedes dieser Muster erstellen, wie in der folgenden Abbildung gezeigt:

![Verwenden des Azure Cosmos DB-Änderungsfeeds für Echtzeitanalysen und ereignisgesteuerte Computingszenarien](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>Unterstützte APIs und Client-SDKs

Dieses Feature wird derzeit von den folgenden Azure Cosmos DB-APIs und Client-SDKs unterstützt.

| **Clienttreiber** | **Azure-Befehlszeilenschnittstelle** | **SQL-API** | **Cassandra-API** | **MongoDB-API von Azure Cosmos DB** | **Gremlin-API**|**Tabellen-API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Nicht verfügbar | Ja | Nein  | Nein  | Ja | Nein  |
|Java|Nicht verfügbar|Ja|Nein |Nein |Ja|Nein |
|Python|Nicht verfügbar|Ja|Nein |Nein |Ja|Nein |
|Node/JS|Nicht verfügbar|Ja|Nein |Nein |Ja|Nein |

## <a name="change-feed-and-different-operations"></a>Änderungsfeed und verschiedene Vorgänge

Derzeit werden im Änderungsfeed alle Vorgänge angezeigt. Die Funktionalität, mit der Sie den Änderungsfeed für bestimmte Vorgänge steuern können, z.B. nur in Aktualisierungen und nicht in Einfügungen, steht noch nicht zur Verfügung. Sie können für das Element eine schwache Markierung für Aktualisierungen hinzufügen und dies bei der Verarbeitung von Elementen im Änderungsfeed entsprechend filtern. Aktuell protokollieren Änderungsfeeds keine Löschvorgänge. Ähnlich wie beim vorherigen Beispiel können Sie eine schwache Markierung für die Elemente hinzufügen, die gelöscht werden. Beispielweise können Sie im Element das Attribut „deleted“ hinzufügen und auf „true“ festlegen und eine Gültigkeitsdauer für das Element angeben, sodass es automatisch gelöscht werden kann. Sie können den Änderungsfeed nach früheren Elementen durchsuchen, z.B. nach Elementen, die vor fünf Jahren hinzugefügt wurden. Sofern Elemente nicht gelöscht wurden, können Sie den Änderungsfeed bis zurück zum Ursprung des Containers lesen.

### <a name="sort-order-of-items-in-change-feed"></a>Sortierreihenfolge der Elemente im Änderungsfeed

Elemente im Änderungsfeed sind in der Reihenfolge des Zeitpunkts ihrer letzten Änderung aufgeführt. Diese Sortierreihenfolge ist pro logischem Partitionsschlüssel festgelegt.

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Änderungsfeed in Azure Cosmos-Konten mit mehreren Regionen

Wenn in einem Azure Cosmos-Konto mit mehreren Regionen ein Failover einer Schreibregion eintritt, funktioniert der Änderungsfeed übergreifend über den manuellen Failovervorgang und bleibt zusammenhängend.

### <a name="change-feed-and-time-to-live-ttl"></a>Änderungsfeed und Gültigkeitsdauer (TTL)

Wenn eine TTL-Eigenschaft (Time to Live, Gültigkeitsdauer) für ein Element auf „-1“ festgelegt ist, bleibt der Änderungsfeed dauerhaft erhalten. Wenn die Daten nicht gelöscht werden, verbleiben sie im Änderungsfeed.  

### <a name="change-feed-and-etag-lsn-or-ts"></a>Änderungsfeed und „_etag“, „_lsn“ oder „_ts“

Das _etag-Format ist intern, und Sie sollten nicht darauf aufbauen, da es jederzeit geändert werden kann. „_ts“ ist ein Änderungs- oder Erstellungszeitstempel. Sie können „_ts“ für chronologische Vergleiche verwenden. „_lsn“ ist eine Batch-ID, die nur für den Änderungsfeed hinzugefügt wird. Sie stellt die Transaktions-ID dar. Viele Elemente können die gleiche „_lsn“ aufweisen. „ETag“ in der FeedResponse unterscheidet sich von dem „_etag“, das für das Element angezeigt wird. „_etag“ ist ein interner Bezeichner und wird zur Gleichzeitigkeitssteuerung verwendet, er gibt die Version des Elements an. „ETag“ wird dagegen für die Sequenzierung des Feeds verwendet.

## <a name="change-feed-use-cases-and-scenarios"></a>Anwendungsfälle und Szenarien für den Änderungsfeed

Der Änderungsfeed ermöglicht eine effiziente Verarbeitung großer Datasets mit vielen Schreibvorgängen. Der Änderungsfeed bietet zudem eine Alternative zum Abfragen eines gesamten Datasets, um Änderungen zu ermitteln.

### <a name="use-cases"></a>Anwendungsfälle

Mit einem Änderungsfeed können Sie beispielsweise die folgenden Aufgaben effizient ausführen:

* Aktualisieren eines Caches, eines Suchindex oder eines Data Warehouse mit in Azure Cosmos DB gespeicherten Daten.

* Implementieren von Datentiering und -archivierung auf Anwendungsebene, z.B. Speichern von „aktiven Daten“ in Azure Cosmos DB und Auslagern von „inaktiven Daten“ in andere Speichersysteme, z.B. [Azure Blob Storage](../storage/common/storage-introduction.md).

* Ausführen von Migrationsvorgängen zu einem anderen Azure Cosmos-Konto oder einem anderen Azure Cosmos-Container mit einem anderen logischen Partitionsschlüssel ohne Ausfallzeit.

* Implementieren der [Lambda-Architektur](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) mithilfe von Azure Cosmos DB, in der Azure Cosmos DB Bereitstellungsebenen für Batches sowie für Abfragen in Echtzeit unterstützt und so eine Lambda-Architektur mit geringen Gesamtkosten ermöglicht.

* Empfangen und Speichern von Ereignisdaten von Geräten, Sensoren, Infrastrukturen und Anwendungen und Verarbeiten dieser Ereignisse in Echtzeit, z.B. mit [Spark](../hdinsight/spark/apache-spark-overview.md).  In der folgenden Abbildung ist dargestellt, wie Sie die Lambda-Architektur mithilfe von Azure Cosmos DB über den Änderungsfeed implementieren können:

![Azure Cosmos DB-basierte Lambda-Pipeline für die Erfassung und Abfrage](./media/change-feed/lambda.png)

### <a name="scenarios"></a>Szenarien

Im Folgenden sind einige Szenarien aufgeführt, die Sie mit einem Änderungsfeed ganz einfach implementieren können:

* In Ihren [serverlosen](https://azure.microsoft.com/solutions/serverless/) Web- oder mobilen Apps können Sie Ereignisse wie z.B. alle Änderungen am Profil, an den Voreinstellungen oder am Standort des Kunden nachverfolgen, um bestimmte Aktionen auszulösen, z.B. das Senden von Pushbenachrichtigungen an die Geräte des Kunden mit [Azure Functions](change-feed-functions.md).

* Wenn Sie Azure Cosmos DB zum Erstellen eines Spiels verwenden, können Sie den Änderungsfeed beispielsweise verwenden, um in Echtzeit Bestenlisten anhand der Ergebnisse von abgeschlossenen Spiele zu implementieren.


## <a name="working-with-change-feed"></a>Verwenden des Änderungsfeeds

Sie können den Änderungsfeed mit den folgenden Optionen verwenden:

* [Verwenden des Änderungsfeeds in Azure Functions](change-feed-functions.md)
* [Verwenden des Änderungsfeeds mit der Change Feed Processor-Bibliothek](change-feed-processor.md) 

Der Änderungsfeed ist für jeden logischen Partitionsschlüssel innerhalb des Containers verfügbar. Er kann für eine parallele Verarbeitung über mehrere Consumer verteilt werden, wie in der folgenden Abbildung gezeigt.

![Verteilte Verarbeitung des Azure Cosmos DB-Änderungsfeeds](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Features des Änderungsfeeds

* Der Änderungsfeed ist standardmäßig für alle Azure Cosmos-Konten aktiviert.

* Sie können Ihren [bereitgestellten Durchsatz](request-units.md) in allen der Azure Cosmos-Datenbank zugeordneten Regionen zum Lesen aus dem Änderungsfeed verwenden, so wie es auch bei allen anderen Vorgängen von Azure Cosmos DB möglich ist.

* Der Änderungsfeed enthält Einfüge- und Aktualisierungsvorgänge, die für Elemente im Container durchgeführt wurden. Sie können Löschvorgänge durch ein Kennzeichen für „vorläufiges Löschen“ erfassen, das den gelöschten Text in den Elementen (z.B. in Dokumenten) ersetzt. Alternativ können Sie über die [TTL-Funktion](time-to-live.md) einen begrenzten Ablaufzeitraum für die Elemente festlegen, z.B. 24 Stunden, und den Wert dieser Eigenschaft verwenden, um Löschvorgänge zu erfassen. Mit dieser Lösung müssen Sie die Änderungen in einem kürzeren Zeitraum als dem TTL-Ablaufzeitraum verarbeiten. 

* Jede Änderung an einem Element wird im Änderungsfeed genau einmal angezeigt, und die Clients müssen ihre Prüfpunktausführungs-Logik verwalten. Wenn Sie die Komplexität der Verwaltung von Prüfpunkten umgehen möchten, bietet die Änderungsfeed-Prozessorbibliothek automatische Prüfpunktausführung und „Mindestens einmal“-Semantik. Siehe [Verwenden des Änderungsfeeds mit der Change Feed Processor-Bibliothek](change-feed-processor.md).

* Nur die letzte Änderung für ein bestimmtes Element ist im Änderungsprotokoll enthalten. Zwischenzeitliche Änderungen sind möglicherweise nicht verfügbar.

* Der Änderungsfeed ist anhand der Reihenfolge der Änderungen innerhalb der einzelnen logischen Partitionsschlüsselwerte sortiert. Es gibt keine festgelegte Reihenfolge über Partitionsschlüsselwerte hinweg.

* Änderungen können für jeden Zeitpunkt synchronisiert werden, d.h., es gibt keine feste Datenaufbewahrungsdauer, in der die Änderungen verfügbar sind.

* Änderungen sind für alle logischen Partitionsschlüssel eines Azure Cosmos-Containers parallel möglich. Auf diese Weise können Änderungen aus umfangreichen Containern parallel von mehreren Consumern verarbeitet werden.

* Anwendungen können mehrere Änderungsfeeds gleichzeitig für den gleichen Container anfordern. Mithilfe von „ChangeFeedOptions.StartTime“ kann ein anfänglicher Startpunkt angegeben werden, z.B. um das Fortsetzungstoken zu suchen, das der angegebenen Uhrzeit entspricht. Der „ContinuationToken“-Wert, sofern angegeben, hat Vorrang vor den Werten „StartTime“ und „StartFromBeginning“. Die Genauigkeit von „ChangeFeedOptions.StartTime“ beträgt ca. 5 Sekunden. 

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln erfahren Sie mehr über Änderungsfeeds:

* [Möglichkeiten zum Lesen von Änderungsfeeds](read-change-feed.md)
* [Verwenden des Änderungsfeeds in Azure Functions](change-feed-functions.md)
* [Verwenden der Änderungsfeed-Verarbeitungsbibliothek](change-feed-processor.md)

---
title: Änderungsfeed-Entwurfsmuster in Azure Cosmos DB
description: Übersicht über allgemeine Änderungsfeed-Entwurfsmuster
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 443d00e61e593daacca04a4451b90bb78cc7d854
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93334583"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>Änderungsfeed-Entwurfsmuster in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Der Änderungsfeed von Azure Cosmos DB ermöglicht eine effiziente Verarbeitung großer Datasets mit vielen Schreibvorgängen. Der Änderungsfeed bietet zudem eine Alternative zum Abfragen eines gesamten Datasets, um Änderungen zu ermitteln. Dieses Dokument konzentriert sich auf allgemeine Entwurfsmuster für Änderungsfeeds, Entwurfskompromisse und Einschränkungen von Änderungsfeeds.

Azure Cosmos DB eignet sich besonders gut für IoT-, Spiele-, Verkaufs- und Vorgangsprotokollierungsanwendungen. Ein allgemeines Entwurfsmuster in diesen Anwendungen ist die Verwendung von Datenänderungen, um weitere Aktionen auszulösen. Beispiele für weitere Aktionen sind:

* Auslösen einer Benachrichtigung oder eines Aufrufs einer API, wenn ein Element eingefügt oder aktualisiert wird
* Streamverarbeitung in Echtzeit für IoT oder Analyseverarbeitung in Echtzeit für operative Daten
* Datenbewegungen wie die Synchronisierung mit einem Cache, einer Suchmaschine, einem Data Warehouse oder mit Cold Storage.

Mit dem Änderungsfeed in Azure Cosmos DB können Sie effiziente und skalierbare Lösungen für jedes dieser Muster erstellen, wie in der folgenden Abbildung gezeigt:

:::image type="content" source="./media/change-feed/changefeedoverview.png" alt-text="Verwenden des Azure Cosmos DB-Änderungsfeeds für Echtzeitanalysen und ereignisgesteuerte Computingszenarien" border="false":::

## <a name="event-computing-and-notifications"></a>Ereignisse und Benachrichtigungen

Der Änderungsfeed von Azure Cosmos DB kann Szenarien vereinfachen, in denen eine Benachrichtigung oder ein API-Aufruf basierend auf einem bestimmten Ereignis ausgelöst oder gesendet werden muss. Sie können die [Änderungsfeed-Prozessbibliothek](change-feed-processor.md) verwenden, um den Container automatisch nach Änderungen abzufragen und eine externe API aufzurufen, wenn ein Schreib- oder Aktualisierungsvorgang vorliegt.

Sie können auch selektiv eine Benachrichtigung auslösen oder einen Aufruf an eine API basierend auf bestimmten Kriterien senden. Wenn Sie z. B. mit [Azure Functions](change-feed-functions.md) aus dem Änderungsfeed lesen, können Sie Logik in die Funktion einfügen, damit nur dann eine Benachrichtigung gesendet wird, wenn bestimmte Kriterien erfüllt sind. Auch wenn der Azure Functions-Code während jedes Schreib- und Aktualisierungsvorgangs ausgeführt wird, wird die Benachrichtigung nur gesendet, wenn bestimmte Kriterien erfüllt wurden.

## <a name="real-time-stream-processing"></a>Streamverarbeitung in Echtzeit-

Der Änderungsfeed von Azure Cosmos DB kann für Streamverarbeitung in Echtzeit für IoT oder Analysen in Echtzeit für operative Daten verwendet werden.
Sie empfangen und speichern Ereignisdaten von Geräten, Sensoren, Infrastrukturen und Anwendungen und verarbeiten diese Ereignisse z. B. in Echtzeit mit [Spark](../hdinsight/spark/apache-spark-overview.md). In der folgenden Abbildung wird dargestellt, wie Sie eine Lambda-Architektur mithilfe von Azure Cosmos DB über den Änderungsfeed implementieren können:

:::image type="content" source="./media/change-feed/lambda.png" alt-text="Azure Cosmos DB-basierte Lambda-Pipeline für die Erfassung und Abfrage" border="false":::

In vielen Fällen empfangen Streamverarbeitungsimplementierungen zunächst eine große Menge eingehender Daten in einer temporären Nachrichtenwarteschlange, z. B. mit Azure Event Hub oder Apache Kafka. Der Änderungsfeed ist eine gute Alternative, weil Azure Cosmos DB eine anhaltend hohe Rate der Datenerfassung mit garantierter geringer Lese- und Schreiblatenz unterstützen kann. Die folgenden Vorteilen ergeben sich, wenn der Änderungsfeed von Azure Cosmos DB über eine Nachrichtenwarteschlange erfolgt:

### <a name="data-persistence"></a>Datenpersistenz

In Azure Cosmos DB geschriebene Daten erscheinen im Änderungsfeed und bleiben bis zum Löschen erhalten. Nachrichtenwarteschlangen wiesen in der Regel eine maximale Beibehaltungsdauer auf. Beispielsweise bietet [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) eine maximale Datenaufbewahrung von 90 Tagen.

### <a name="querying-ability"></a>Abfragefähigkeit

Zusätzlich zum Lesen aus dem Änderungsfeed eines Cosmos-Containers können Sie auch SQL-Abfragen für die in Azure Cosmos DB gespeicherten Daten ausführen. Der Änderungsfeed ist keine Duplizierung von Daten, die sich bereits im Container befinden, sondern lediglich ein anderer Mechanismus zum Lesen der Daten. Daher sind beim Lesen von Daten aus dem Änderungsfeed diese immer konsistent mit Abfragen desselben Azure Cosmos DB-Containers.

### <a name="high-availability"></a>Hochverfügbarkeit

Azure Cosmos DB bietet bis zu 99,999 % Lese- und Schreibverfügbarkeit. Im Gegensatz zu vielen Nachrichtenwarteschlangen können Azure Cosmos DB-Daten problemlos global verteilt und mit einer [RTO (Recovery Time Objective)](./consistency-levels.md#rto) von null konfiguriert werden.

Nachdem Sie Elemente im Änderungsfeed verarbeitet haben, können Sie eine materialisierte Sicht erstellen und aggregierte Werte in Azure Cosmos DB persistent speichern. Wenn Sie Azure Cosmos DB zum Erstellen eines Spiels verwenden, können Sie den Änderungsfeed beispielsweise verwenden, um in Echtzeit Bestenlisten anhand der Ergebnisse von abgeschlossenen Spiele zu implementieren.

## <a name="data-movement"></a>Datenverschiebung

Sie können auch aus dem Änderungsfeed lesen, um Datenverschiebungen in Echtzeit abzurufen.

Mit einem Änderungsfeed können Sie beispielsweise die folgenden Aufgaben effizient ausführen:

* Aktualisieren eines Caches, eines Suchindex oder eines Data Warehouse mit in Azure Cosmos DB gespeicherten Daten.

* Ausführen von Migrationsvorgängen zu einem anderen Azure Cosmos-Konto oder einem anderen Azure Cosmos-Container mit einem anderen logischen Partitionsschlüssel ohne Ausfallzeit.

* Implementieren von Datentiering und Archivierung auf Anwendungsebene. Beispielsweise können Sie „heiße Daten“ in Azure Cosmos DB speichern und „alte Daten“ in andere Speichersysteme (z. B. [Azure Blob Storage](../storage/common/storage-introduction.md)) auslagern.

Wenn Sie Daten [über Partitionen und Container hinweg denormalisieren](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
) müssen, können Sie für diese Datenreplikation aus dem Änderungsfeed Ihres Containers als Quelle lesen. Datenreplikation in Echtzeit- mit dem Änderungsfeed kann nur letztliche Konsistenz gewährleisten. Sie können [überwachen, wie weit der Änderungsfeedprozessor bei der Verarbeitung von Änderungen in Ihrem Cosmos-Container hinterherhinkt](how-to-use-change-feed-estimator.md).

## <a name="event-sourcing"></a>Ereignissourcing

Das [Ereignissourcingmuster](/azure/architecture/patterns/event-sourcing) beinhaltet die Verwendung eines reinen Anfügespeichers zum Aufzeichnen aller Aktionen für diese Daten. Der Änderungsfeed von Azure Cosmos DB ist eine gute Wahl als zentraler Datenspeicher in ereignisbasierten Architekturen, in denen die gesamte Datenerfassung als Schreibvorgänge (keine Aktualisierungen oder Löschungen) modelliert wird. In diesem Fall ist jeder Schreibvorgang in Azure Cosmos DB ein „Ereignis“, und Sie verfügen über einen vollständigen Datensatz früherer Ereignisse im Änderungsfeed. Typische Verwendungsmöglichkeiten von Ereignissen, die vom zentralen Ereignisspeicher veröffentlicht werden, sind die Verwaltung materialisierter Sichten oder die Integration in externe Systeme. Da es keine zeitliche Beschränkung für die Datenaufbewahrung im Änderungsfeed gibt, können Sie alle vergangenen Ereignisse wiedergeben, indem vom Anfang des Änderungsfeeds Ihres Cosmos-Containers lesen.

Sie können [mehrere Consumer von Änderungsfeeds den Änderungsfeed des gleichen Containers abonnieren](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers) lassen. Abgesehen vom bereitgestelltem Durchsatz des [Leasecontainers](change-feed-processor.md#components-of-the-change-feed-processor) entstehen keine Kosten für die Nutzung des Änderungsfeeds. Der Änderungsfeed ist in jedem Container verfügbar, und zwar unabhängig davon, ob er verwendet wird.

Azure Cosmos DB ist aufgrund der Stärken hinsichtlich horizontaler Skalierbarkeit und Hochverfügbarkeit ein großartiger zentraler und persistenter reiner Anfügespeicher im Ereignissourcingmuster. Außerdem bietet die Änderungsfeed-Prozessorbibliothek eine Garantie des Typs [„mindestens ein Mal“](change-feed-processor.md#error-handling), die sicherstellt, dass die Verarbeitung keines Ereignisses verpasst wird.

## <a name="current-limitations"></a>Aktuelle Einschränkungen

Der Änderungsfeed weist wichtige Einschränkungen auf, die Sie kennen sollten. Zwar verbleiben Elemente in einem Cosmos-Container immer im Änderungsfeed, der Änderungsfeed ist aber kein vollständiges Vorgangsprotokoll. Beim Entwerfen einer Anwendung, die den Änderungsfeed verwendet, sind wichtige Punkte zu beachten.

### <a name="intermediate-updates"></a>Zwischenaktualisierungen

Nur die letzte Änderung für ein bestimmtes Element ist im Änderungsfeed enthalten. Wenn Änderungen verarbeitet werden, lesen Sie die neueste verfügbare Elementversion. Wenn mehrere Aktualisierungen für das gleiche Element innerhalb kurzer Zeit vorhanden sind, ist es möglich, dass die Verarbeitung von Zwischenaktualisierungen verpasst wird. Wenn Sie Aktualisierungen nachverfolgen und in der Lage sein möchten, frühere Aktualisierungen für ein Element wiederzugeben, empfiehlt es sich, diese Aktualisierungen stattdessen als eine Reihe von Schreibvorgängen zu modellieren.

### <a name="deletes"></a>Löschvorgang

Der Änderungsfeed erfasst keine Löschvorgänge. Wenn Sie ein Element aus dem Container löschen, wird es auch aus dem Änderungsfeed entfernt. Die gebräuchlichste Methode, dies zu handhaben, ist das Hinzufügen einer vorläufigen Markierung für die zu löschenden Elemente. Sie können eine Eigenschaft mit dem Namen „deleted“ (gelöscht) hinzufügen und sie zum Zeitpunkt der Löschung auf TRUE festlegen. Diese Dokumentaktualisierung wird im Änderungsfeed angezeigt. Sie können eine Gültigkeitsdauer für dieses Element festlegen, damit es später automatisch gelöscht werden kann.

### <a name="guaranteed-order"></a>Garantierte Reihenfolge

Es gibt eine garantierte Reihenfolge im Änderungsfeed innerhalb eines Partitionsschlüsselwerts, jedoch nicht über Partitionsschlüsselwerte hinweg. Sie sollten einen Partitionsschlüssel auswählen, der Ihnen eine sinnvolle Garantie der Reihenfolge bietet.

Stellen Sie sich z. B. eine Einzelhandelsanwendung vor, die das Ereignissourcing-Entwurfsmuster verwendet. In dieser Anwendung sind unterschiedliche Benutzeraktionen jeweils „Ereignisse“, die als Schreibvorgänge in Azure Cosmos DB modelliert werden. Angenommen, einige Beispielereignisse sind in der folgenden Reihenfolge aufgetreten:

1. Kunde fügt Artikel A dem Einkaufswagen hinzu.
2. Kunde fügt Artikel B dem Einkaufswagen hinzu.
3. Kunde entfernt Artikel A aus dem Einkaufswagen.
4. Kunde geht zur Kasse, und der Inhalt des Einkaufswagens wird geliefert.

Eine materialisierte Sicht der Inhalte des aktuellen Einkaufswagens wird für jeden Kunden verwaltet. Diese Anwendung muss sicherstellen, dass diese Ereignisse in der Reihenfolge verarbeitet werden, in der sie auftreten. Wenn die Bezahlung des Inhalts des Einkaufswagens z. B. vor dem Entfernen von Artikel A verarbeitet würde, wäre es wahrscheinlich, dass Artikel A an den Kunde geliefert würde, nicht der gewünschte Artikel B. Um sicherzustellen, dass diese vier Ereignisse in der Reihenfolge ihres Auftretens verarbeitet werden, sollten sie in den gleichen Partitionsschlüsselwert fallen. Wenn Sie **username** (jeder Kunde beitzt einen eindeutigen Benutzernamen) als Partitionsschlüssel ausgewählt haben, können Sie garantieren, dass diese Ereignisse im Änderungsfeed in derselben Reihenfolge erscheinen, in der sie in Azure Cosmos DB geschrieben werden.

## <a name="examples"></a>Beispiele

Im Folgenden finden Sie einige praktische Änderungsfeed-Codebeispiele, die über die in der Microsoft-Dokumentation bereitgestellten Beispiele hinausgehen:

- [Einführung in den Änderungsfeed](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [IoT-Anwendungsfall für einen Änderungsfeed](https://github.com/AzureCosmosDB/scenario-based-labs)
- [Einzelhandelsanwendungsfall für einen Änderungsfeed](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über den Änderungsfeed](change-feed.md)
* [Möglichkeiten zum Lesen von Änderungsfeeds](read-change-feed.md)
* [Verwenden des Änderungsfeeds in Azure Functions](change-feed-functions.md)
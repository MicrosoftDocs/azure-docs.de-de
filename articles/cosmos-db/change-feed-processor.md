---
title: Arbeiten mit der Änderungsfeed-Prozessorbibliothek in Azure Cosmos DB
description: Verwenden der Änderungsfeed-Prozessorbibliothek von Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 9cf9e1aabc0898ef025c7c2f517e631a812e67d7
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969024"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Änderungsfeedprozessor in Azure Cosmos DB 

Mit der [Änderungsfeed-Prozessorbibliothek von Azure Cosmos DB](sql-api-sdk-dotnet-changefeed.md) können Sie die Ereignisverarbeitung auf mehrere Consumer verteilen. Diese Bibliothek vereinfacht das Partitionen übergreifende Lesen von Änderungen und die Parallelverarbeitung mehrerer Threads.

Der Hauptnutzen der Änderungsfeed-Prozessorbibliothek besteht darin, dass Sie nicht jede Partition und jedes Fortsetzungstoken verwalten und nicht jeden Container manuell abrufen müssen.

Die Change Feed Processor-Bibliothek vereinfacht das Partitionen übergreifende Lesen von Änderungen und die Parallelverarbeitung mehrerer Threads. Sie verwaltet automatisch das Partitionen übergreifende Lesen von Änderungen mit einem Leasemechanismus. Wenn Sie zwei Clients starten, die die Change Feed Processor-Bibliothek verwenden, teilen sie die Arbeit untereinander auf, wie die folgende Abbildung zeigt. Wenn Sie die Anzahl von Clients weiter erhöhen, teilen diese weiterhin die Arbeit untereinander auf.

![Verwenden der Änderungsfeed-Prozessorbibliothek von Azure Cosmos DB](./media/change-feed-processor/change-feed-output.png)

Der linke Client wurde zuerst gestartet und begann mit der Überwachung aller Partitionen, dann wurde der zweite Client gestartet, und der erste Client hat dem zweiten einige der Leases überlassen. Dies ist eine effiziente Methode zur Verteilung der Arbeit auf verschiedene Computer und Clients.

Wenn zwei serverlose Azure-Funktionen denselben Container überwachen und die gleiche Lease verwenden, erhalten diese beiden Funktionen möglicherweise unterschiedliche Dokumente. Dies hängt davon ab, wie die Prozessorbibliothek über die Verarbeitung der Partitionen entscheidet.

## <a name="implementing-the-change-feed-processor-library"></a>Implementieren der Änderungsfeed-Prozessorbibliothek

Die Implementierung der Änderungsfeed-Prozessorbibliothek umfasst vier Hauptkomponenten: 

1. **Überwachter Container:** Der überwachte Container enthält die Daten, aus denen der Änderungsfeed generiert wird. Alle Einfügungen im und Änderungen am überwachten Container werden im Änderungsfeed des Containers wiedergegeben.

1. **Leasecontainer:** Der Leasecontainer koordiniert die Verarbeitung des Änderungsfeeds über mehrere Worker hinweg. Ein separater Container wird verwendet, um die Leases mit einer Lease pro Partition zu speichern. Es empfiehlt sich, diesen Leasecontainer in einem anderen Konto zu speichern, bei dem sich die Schreibregion näher am Ausführungsort des Änderungsfeedprozessors befindet. Ein Lease-Objekt beinhaltet die folgenden Attribute:

   * Besitzer: Gibt den Host an, der die Lease besitzt.

   * Fortsetzung: Gibt die Position (Fortsetzungstoken) im Änderungsfeed für eine bestimmte Partition an.

   * Zeitstempel: Zeitpunkt der letzten Aktualisierung der Lease. Mithilfe des Zeitstempels kann überprüft werden, ob die Lease als abgelaufen betrachtet wird.

1. **Prozessorhost:** Jeder Host bestimmt, wie viele Partitionen verarbeitet werden, je nachdem, wie viele andere Instanzen des Hosts aktive Leases aufweisen.

   * Wenn ein Host gestartet wird, erhält er Leases, um die Workloads gleichmäßig auf alle Hosts zu verteilen. Ein Host erneuert in regelmäßigen Abständen Leases, damit die Leases aktiv bleiben.

   * Ein Host überprüft das letzte Fortsetzungstoken auf seiner Lease für jeden Lesezugriff. Um Parallelitätssicherheit zu gewährleisten, überprüft ein Host den ETag für jedes Lease-Update. Andere Prüfpunkt-Strategien werden ebenfalls unterstützt.

   * Nach dem Herunterfahren gibt ein Host alle Leases frei, speichert jedoch die Fortsetzungsinformationen, sodass das Lesen aus dem gespeicherten Prüfpunkt später fortgesetzt werden kann.

   Gegenwärtig darf die Anzahl von Hosts nicht die Anzahl von Partitionen (Leases) überschreiten.

1. **Consumer:** Consumer oder Worker sind Threads, welche die von jedem Host initiierte Änderungsfeedverarbeitung ausführen. Jeder Prozessor-Host kann mehrere Consumer besitzen. Jeder Consumer liest den Änderungsfeed aus der Partition, der er zugewiesen wurde, und benachrichtigt seinen Host bei Änderungen und abgelaufenen Leases.

Um besser zu verstehen, wie diese vier Elemente von Change Feed Processor zusammenarbeiten, sehen wir uns ein Beispiel in der folgenden Abbildung an. Die überwachte Sammlung speichert Dokumente und verwendet „City“ als Partitionsschlüssel. Sie sehen, dass die blaue Partition Dokumente mit dem Feld „City“ aus „A-E“ usw. enthält. Es gibt zwei Hosts mit jeweils zwei Consumern, die aus den vier Partitionen gleichzeitig lesen. Die Pfeile zeigen die Consumer beim Lesen aus einer bestimmten Stelle im Änderungsfeed. In der ersten Partition stellt das dunklere Blau ungelesene Änderungen dar, während das Hellblau die bereits gelesenen Änderungen im Änderungsfeed darstellt. Die Hosts verwenden die Lease-Sammlung zum Speichern eines „Fortsetzungs“-Werts der aktuellen Leseposition für jeden Consumer.

![Beispiel für den Änderungsfeedprozessor](./media/change-feed-processor/changefeedprocessor.png)

### <a name="change-feed-and-provisioned-throughput"></a>Änderungsfeed und bereitgestellter Durchsatz

Ihnen werden die genutzten Anforderungseinheiten (Request Units, RUs) in Rechnung gestellt, da bei der Datenverschiebung in und aus Cosmos-Containern immer RUs verbraucht werden. Ihnen werden die vom Leasecontainer genutzten Anforderungseinheiten in Rechnung gestellt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Änderungsfeed-Prozessorbibliothek von Azure Cosmos DB](sql-api-sdk-dotnet-changefeed.md)
* [NuGet-Paket](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [Zusätzliche Beispiele auf GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln erfahren Sie mehr über Änderungsfeeds:

* [Übersicht über Änderungsfeeds](change-feed.md)
* [Ways to read change feed](read-change-feed.md) (Möglichkeiten zum Lesen von Änderungsfeeds)
* [Using change feed with Azure Functions](change-feed-functions.md) (Verwenden von Änderungsfeeds mit Azure Functions)

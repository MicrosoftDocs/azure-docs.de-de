---
title: Lesen des Azure Cosmos DB-Änderungsfeeds
description: In diesem Artikel werden die verschiedenen Optionen beschrieben, die für das Lesen des Änderungsfeeds und den Zugriff darauf in Azure Cosmos DB zur Verfügung stehen.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/27/2020
ms.reviewer: sngun
ms.openlocfilehash: 7021367e1230573343ddf57ccd399d998ad5280e
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339273"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Lesen des Azure Cosmos DB-Änderungsfeeds
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Für die Arbeit mit dem Azure Cosmos DB-Änderungsfeed können Sie ein Pushmodell oder ein Pullmodell verwenden. Bei einem Pushmodell pusht der Änderungsfeedprozessor die Aufgaben an einen Client, der über Geschäftslogik zur Verarbeitung dieser Aufgaben verfügt. Die Komplexität der Überprüfung auf Aufgaben und das Speichern des Zustands der zuletzt verarbeiteten Aufgaben wird jedoch im Änderungsfeedprozessor behandelt.

Bei einem Pullmodell muss der Client die Aufgaben vom Server abrufen. Der Client verfügt in diesem Fall nicht nur über Geschäftslogik für die Verarbeitung von Aufgaben, sondern kümmert sich auch um die Speicherung des Zustands für die zuletzt verarbeitete Aufgabe, den Lastenausgleich für mehrere Clients, die die Aufgaben parallel verarbeiten, und die Fehlerbehandlung.

Beim Lesen aus dem Azure Cosmos DB-Änderungsfeed empfiehlt es sich in der Regel, ein Pushmodell zu verwenden, da Sie sich über Folgendes keine Gedanken machen müssen:

- Abrufen des Änderungsfeeds bei zukünftigen Änderungen.
- Speichern des Zustands für die zuletzt verarbeitete Änderung. Diese wird beim Lesen aus dem Änderungsfeed automatisch in einem [Leasecontainer](change-feed-processor.md#components-of-the-change-feed-processor) gespeichert.
- Lastenausgleich über mehrere Clients, die Änderungen verbrauchen. Wenn beispielsweise ein Client nicht mit der Verarbeitung von Änderungen Schritt halten kann, während ein anderer noch Kapazitäten hat.
- [Behandeln von Fehlern](change-feed-processor.md#error-handling). Dies umfasst z. B. automatische Wiederholungen bei fehlerhaften Änderungen, die nach einer nicht behandelten Ausnahme im Code oder einem zeitweiligen Netzwerkproblem nicht richtig verarbeitet wurden.

In den meisten Szenarien, in denen der Azure Cosmos DB-Änderungsfeed verwendet wird, wird eine der Pushmodelloptionen verwendet. Es gibt jedoch einige Szenarien, in denen Sie möglicherweise die zusätzliche Kontrolle auf niedriger Ebene des Pullmodells benötigen. Dazu gehören:

- Lesen von Änderungen von einem bestimmten Partitionsschlüssel
- Steuern der Geschwindigkeit, mit der der Client Änderungen für die Verarbeitung empfängt
- Ausführen eines einmaligen Lesevorgangs über die vorhandenen Daten im Änderungsfeed (z. B. für eine Datenmigration)

## <a name="reading-change-feed-with-a-push-model"></a>Lesen des Änderungsfeeds mit einem Pushmodell

Die Verwendung eines Pushmodells stellt die einfachste Möglichkeit dar, aus dem Änderungsfeed zu lesen. Es gibt zwei Möglichkeiten, mit einem Pushmodell aus dem Änderungsfeed zu lesen: [Cosmos DB-Trigger für Azure Functions](change-feed-functions.md) und die [Bibliothek für den Änderungsfeedprozessor](change-feed-processor.md). Azure Functions verwendet im Hintergrund den Änderungsfeedprozessor, sodass es sich bei beiden um sehr ähnliche Methoden zum Lesen des Änderungsfeeds handelt. Stellen Sie sich Azure Functions einfach als Hostingplattform für den Änderungsfeedprozessor vor und nicht als völlig andere Form, den Änderungsfeed zu lesen.

### <a name="azure-functions"></a>Azure-Funktionen

Azure Functions ist die einfachste Option, wenn Sie nur die ersten Schritte mit dem Änderungsfeed durchführen möchten. Aufgrund der Einfachheit ist es auch die empfohlene Option für die meisten Anwendungsfälle mit dem Änderungsfeed. Wenn Sie einen Azure Functions-Trigger für Azure Cosmos DB erstellen, wählen Sie den Container aus, mit dem eine Verbindung hergestellt werden soll. Die Azure-Funktion wird immer dann ausgelöst, wenn eine Änderung am diesem Container erfolgt. Da Azure Functions im Hintergrund den Änderungsfeedprozessor verwendet, wird die Änderungsverarbeitung automatisch über die [Partitionen](partitioning-overview.md) des Containers parallelisiert.

Die Entwicklung mit Azure Functions ist sehr einfach und kann schneller sein als eine eigene Bereitstellung des Änderungsfeedprozessors. Trigger können im Azure Functions-Portal oder programmgesteuert über SDKs erstellt werden. Visual Studio und VS Code bieten Unterstützung für das Schreiben von Azure-Funktionen, und Sie können sogar die Azure Functions-CLI zur plattformübergreifenden Entwicklung verwenden. Sie können den Code auf Ihrem Desktop schreiben und debuggen und dann die Funktion mit einem Klick bereitstellen. Weitere Informationen finden Sie in den Artikeln [Serverloses Datenbankcomputing mithilfe von Azure Functions](serverless-computing-database.md) und [Verwenden des Änderungsfeeds mit Azure Functions](change-feed-functions.md).

### <a name="change-feed-processor-library"></a>Änderungsfeedprozessor-Bibliothek

Der Änderungsfeedprozessor bietet Ihnen mehr Kontrolle über den Änderungsfeed, verringert die Komplexität allerdings erheblich. Die Bibliothek für den Änderungsfeedprozessor folgt dem Beobachtermuster, bei dem Ihre Verarbeitungsfunktion von der Bibliothek aufgerufen wird. Die Änderungsfeedprozessor-Bibliothek überprüft automatisch, ob Änderungen vorhanden sind. Beim Erkennen von Änderungen werden diese an den Client gepusht. Wenn Ihr Änderungsfeed einen hohen Durchsatz aufweist, können Sie zum Lesen des Änderungsfeeds mehrere Clients instanziieren. Die Bibliothek für den Änderungsfeedprozessor teilt die Last automatisch zwischen den verschiedenen Clients auf. Es ist nicht erforderlich, Logik für den Lastenausgleich über mehrere Clients oder Logik zum Verwalten des Leasezustands zu implementieren.

Die Änderungsfeedprozessor-Bibliothek garantiert eine mindestens einmalige („at-least-once“) Übermittlung aller Änderungen. Anders ausgedrückt: Wenn Sie die Änderungsfeedprozessor-Bibliothek verwenden, wird die Verarbeitungsfunktion für jedes Element im Änderungsfeed erfolgreich aufgerufen. Wenn in der Geschäftslogik Ihrer Verarbeitungsfunktion ein Ausnahmefehler auftritt, werden die fehlerhaften Änderungen wiederholt, bis sie erfolgreich verarbeitet wurden. Um zu verhindern, dass der Änderungsfeedprozessor fortlaufend dieselben Änderungen wiederholt, sollten Sie in Ihrer Verarbeitungsfunktion Logik hinzufügen, um bei einer Ausnahme Dokumente in eine Warteschlange für unzustellbare Nachrichten zu schreiben. Erfahren Sie mehr über die [Fehlerbehandlung](change-feed-processor.md#error-handling).

In Azure Functions ist die Empfehlung zur Fehlerbehandlung identisch. Sie sollten weiterhin Logik in Ihrem Delegatcode hinzufügen, um Dokumente bei einer Ausnahme in eine Warteschlange für unzustellbare Nachrichten zu schreiben. Wenn jedoch ein Ausnahmefehler in Ihrer Azure-Funktion vorliegt, wird die Änderung, die die Ausnahme generiert hat, nicht automatisch wiederholt. Wenn in der Geschäftslogik ein Ausnahmefehler auftritt, fährt die Azure-Funktion mit der Verarbeitung der nächsten Änderung fort. Die Azure-Funktion wiederholt nicht dieselbe fehlerhafte Änderung.

Wie bei Azure Functions ist auch das Entwickeln mit der Änderungsfeedprozessor-Bibliothek einfach. Sie müssen jedoch mindestens einen Host für den Änderungsfeedprozessor bereitstellen. Ein Host ist eine Anwendungsinstanz, die den Änderungsfeedprozessor verwendet, um auf Änderungen zu lauschen. Auch wenn Azure Functions über Funktionen für die automatische Skalierung verfügt, sind Sie für das Skalieren Ihrer Hosts selbst verantwortlich. Weitere Informationen finden Sie unter [Verwenden des Änderungsfeedprozessors](change-feed-processor.md#dynamic-scaling). Die Bibliothek für den Änderungsfeedprozessor ist im [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3) enthalten.

## <a name="reading-change-feed-with-a-pull-model"></a>Lesen des Änderungsfeeds mit einem Pullmodell

Mit dem [Pullmodell für den Änderungsfeed](change-feed-pull-model.md) können Sie den Änderungsfeed in Ihrem eigenen Tempo verwenden. Änderungen müssen vom Client angefordert werden, und es erfolgt kein automatisches Abrufen von Änderungen. Wenn Sie die letzte verarbeitete Änderung (ähnlich wie beim Leasecontainer des Pushmodells) dauerhaft als „Lesezeichen“ speichern möchten, müssen Sie ein [Fortsetzungstoken speichern](change-feed-pull-model.md#saving-continuation-tokens).

Mit dem Pullmodell für den Änderungsfeed erhalten Sie eine bessere Kontrolle auf niedriger Ebene über den Änderungsfeed. Sie haben beim Lesen des Änderungsfeeds mit dem Pullmodell drei Möglichkeiten:

- Lesen von Änderungen für einen vollständigen Container
- Lesen von Änderungen für einen bestimmten [FeedRange](change-feed-pull-model.md#using-feedrange-for-parallelization)
- Lesen von Änderungen für einen bestimmten Partitionsschlüsselwert

Ebenso wie beim Änderungsfeedprozessor können Sie die Verarbeitung von Änderungen über mehrere Clients hinweg parallelisieren. Allerdings erfolgt der Lastenausgleich auf mehrere Clients beim Pullmodell nicht automatisch. Wenn Sie das Pullmodell verwenden, um die Verarbeitung des Änderungsfeeds zu parallelisieren, erhalten Sie zunächst eine Liste von FeedRanges. Ein FeedRange stellt einen Bereich von Partitionsschlüsselwerten dar. Sie benötigen einen Orchestratorprozess, der FeedRanges abruft und auf Ihre Computer verteilt. Anschließend können Sie diese FeedRanges verwenden, damit mehrere Computer den Änderungsfeed parallel lesen können.

Es gibt beim Pullmodell keine Garantie für eine mindestens einmalige („at-least-once“) Übermittlung. Das Pullmodell bietet Ihnen mehr Kontrolle auf niedriger Ebene, sodass Sie entscheiden können, wie Fehler behandelt werden sollen.

> [!NOTE]
> Das Pullmodell für den Änderungsfeed ist derzeit ausschließlich als [Vorschauversion im Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.15.0-preview) verfügbar. Für andere SDK-Versionen ist noch keine Vorschauversion verfügbar.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Änderungsfeed in APIs für Cassandra und MongoDB

Die Funktionen des Änderungsfeeds werden in der MongoDB-API als Änderungsdatenstrom und in der Cassandra-API über Prädikate verfügbar gemacht. Weitere Informationen zu den Implementierungsdetails für die MongoDB-API finden Sie unter [Änderungsdatenströme in der Azure Cosmos DB-API für MongoDB](mongodb-change-streams.md).

Natives Apache Cassandra bietet Change Data Capture (CDC), einen Mechanismus zum Markieren bestimmter Tabellen für die Archivierung sowie zum Ablehnen von Schreibvorgängen in diesen Tabellen, nachdem eine konfigurierbare Größe auf dem Datenträger für das CDC-Protokoll erreicht wurde. Der Änderungsfeed in der Azure Cosmos DB-API für Cassandra verbessert die Möglichkeit, Änderungen mit Prädikaten über CQL abzufragen. Weitere Informationen zu den Implementierungsdetails finden Sie unter [Änderungsfeed in der Azure Cosmos DB-API für Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln erfahren Sie mehr über den Änderungsfeed:

* [Übersicht über Änderungsfeeds](change-feed.md)
* [Verwenden des Änderungsfeeds in Azure Functions](change-feed-functions.md)
* [Verwenden der Änderungsfeed-Verarbeitungsbibliothek](change-feed-processor.md)

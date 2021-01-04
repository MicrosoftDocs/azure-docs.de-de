---
title: Azure Cosmos DB .NET-Änderungsfeedprozessor-API, -SDK und -Versionshinweise
description: Wichtige Informationen zur Change Feed Processor-API und zum Change Feed Processor-SDK, einschließlich Terminen für Veröffentlichung und Außerbetriebnahme sowie Änderungen an den einzelnen Versionen des .NET Change Feed Processor-SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.openlocfilehash: ff861b5a58de4f108e49e52e6f09c71d7dd678ac
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097191"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET Change Feed Processor-SDK: Download und Versionshinweise
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
>
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark-Connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api
> * [REST-Ressourcenanbieter](/rest/api
> * [SQL](sql-api-query-reference.md)
> * [Bulk Executor – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor – Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**SDK-Download**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API-Dokumentation**|[Referenzdokumentation zum Ändern der Feedprozessorbibliotheks-API](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet&preserve-view=true)|
|**Erste Schritte**|[Erste Schritte mit dem .NET Change Feed Processor-SDK](change-feed.md)|
|**Aktuelles unterstütztes Framework**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

> [!NOTE]
> Wenn Sie den Änderungsfeedprozessor verwenden, sehen Sie sich die neueste 3.x-Version des [.NET SDKs](change-feed-processor.md) an, da der Änderungsfeedprozessor bei dieser Version in die SDK integriert ist. 

## <a name="release-notes"></a>Versionshinweise

### <a name="v2-builds"></a>V2-Builds

### <a name="232"></a><a id="2.3.2"></a>2.3.2
* Leasespeicherkompatibilität mit dem [V3 SDK] wurde hinzugefügt, um Pfade für die Livemigration zu ermöglichen. Eine Anwendung kann zu V3 SDK migriert und zur Änderungsfeed-Prozessorbibliothek zurück migriert werden, ohne dass ein Zustand verloren geht.

### <a name="231"></a><a id="2.3.1"></a>2.3.1
* Ein Szenario wurde korrigiert, bei dem der Schließungsgrund `FeedProcessing.ChangeFeedObserverCloseReason.Unknown` an `FeedProcessing.IChangeFeedObserver.CloseAsync` gesendet wurde, wenn die Partition nicht gefunden wurde oder wenn das Zielreplikat nicht auf dem neuesten Stand der Lesesitzung ist. In diesen Fällen werden nun die Schließungsgründe `FeedProcessing.ChangeFeedObserverCloseReason.ResourceGone` und `FeedProcessing.ChangeFeedObserverCloseReason.ReadSessionNotAvailable` verwendet.
* Der neue Schließungsgrund `FeedProcessing.ChangeFeedObserverCloseReason.ReadSessionNotAvailable` wurde hinzugefügt, der zum Schließen des Änderungsfeeds „Beobachter“ gesendet wird, wenn das Zielreplikat nicht auf dem neuesten Stand der Lesesitzung ist.

### <a name="230"></a><a id="2.3.0"></a>2.3.0
* Die neue `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory`-Methode und die zugehörige öffentliche Schnittstelle `ICheckpointPartitionProcessorFactory` wurden hinzugefügt. Dies ermöglicht einer Implementierung der Schnittstelle `IPartitionProcessor`, den integrierten Prüfpunktmechanismus zu verwenden. Die neue Factory ähnelt der vorhandenen `IPartitionProcessorFactory`, mit dem Unterschied, dass die `Create`-Methode auch den Parameter `ILeaseCheckpointer` akzeptiert.
* Nur eine der beiden Methoden (entweder `ChangeFeedProcessorBuilder.WithPartitionProcessorFactory` oder `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory`) kann für dieselbe `ChangeFeedProcessorBuilder`-Instanz verwendet werden.

### <a name="228"></a><a id="2.2.8"></a>2.2.8
* Verbesserungen hinsichtlich Stabilität und Diagnostizierbarkeit:
  * Die Erkennung lang andauernder Lesevorgänge des Änderungsfeeds wird nun unterstützt. Wenn es länger dauert, als vom Wert der `ChangeFeedProcessorOptions.ChangeFeedTimeout`-Eigenschaft angegeben, werden die folgenden Schritte ausgeführt:
    * Der Vorgang zum Lesen des Änderungsfeeds auf der problematischen Partition wird abgebrochen.
    * Die Änderungsfeedprozessor-Instanz verwirft den Besitz der problematischen Lease. Die verworfene Lease wird während des nächsten Schritts zum Leaseabruf durch denselben oder einen anderen Änderungsfeedprozessor-Instanz ausgewählt. Auf diese Weise wird der Lesevorgang des Änderungsfeeds neu gestartet.
    * Ein Problem wird an den Integritätsmonitor gemeldet. Der standardmäßige Integritätsmonitor sendet alle gemeldeten Probleme an das Ablaufverfolgungsprotokoll.
  * Es wurde eine neue öffentliche Eigenschaft hinzugefügt: `ChangeFeedProcessorOptions.ChangeFeedTimeout`. Der Standardwert dieser Eigenschaft ist 10 Minuten.
  * Es wurde ein neuer öffentlicher Enumerationswert hinzugefügt: `Monitoring.MonitoredOperation.ReadChangeFeed`. Wenn der Wert von `HealthMonitoringRecord.Operation` auf `Monitoring.MonitoredOperation.ReadChangeFeed` festgelegt ist, wird angegeben, dass sich das Integritätsproblem auf das Lesen des Änderungsfeeds bezieht.

### <a name="227"></a><a id="2.2.7"></a>2.2.7
* Verbesserte Lastenausgleichsstrategie für ein Szenario, bei dem das Abrufen aller Leases länger dauert als das Leaseablaufintervall, z. B. aufgrund von Netzwerkproblemen:
  * In diesem Szenario wird ein Lastenausgleichsalgorithmus verwendet, um Leases fälschlicherweise als abgelaufen zu betrachten. Das führt dazu, dass aktiven Besitzern Leases gestohlen werden. Dies könnte einen unnötigen erneuten Ausgleich zahlreicher Leases auslösen.
  * Dieses Problem wurde in der vorliegenden Version folgendermaßen behoben: Bei einem Konflikt wird eine Wiederholung vermieden, wobei die abgelaufene Lease, die der Besitzer nicht geändert hat, abgerufen und dieser Abruf auf die nächste Iteration des Lastenausgleichs verschoben wird.

### <a name="226"></a><a id="2.2.6"></a>2.2.6
* Verbesserte Behandlung von Beobachterausnahmen.
* Umfangreichere Informationen Beobachterfehlern:
  * Wenn ein Beobachter aufgrund einer Ausnahme geschlossen wird, die von „ProcessChangesAsync“ des Beobachters ausgelöst wird, erhält „CloseAsync“ nun den reason-Parameter „ChangeFeedObserverCloseReason.ObserverError“.
  * Hinzugefügte Ablaufverfolgungen zum Bestimmen von Fehlern im Benutzercode bei einem Beobachter.

### <a name="225"></a><a id="2.2.5"></a>2.2.5
* Unterstützung für die Verarbeitung von Aufteilungen von Sammlungen, die gemeinsam genutzten Datenbankdurchsatz verwenden, wurde hinzugefügt.
  * Diese Version behebt ein Problem, das u. U. bei einer Aufteilung von Sammlungen mit gemeinsam genutztem Datenbankdurchsatz entsteht, wenn die Aufteilung zu einem Partitionsausgleich führt, bei dem nur ein untergeordneter Partitionsschlüsselbereich (statt zwei) erstellt wird. In diesem Fall kann der Change Feed Processor beim Löschen der Lease für den alten Partitionsschlüsselbereich hängen bleiben und erstellt keine neuen Leases mehr. Dieses Problem wurde in diesem Release behoben.

### <a name="224"></a><a id="2.2.4"></a>2.2.4
* Die Eigenschaft ChangeFeedProcessorOptions.StartContinuation wurde hinzugefügt, um das Starten des Änderungsfeeds über das Fortsetzungstoken für die Anforderung zu unterstützen. Dies wird nur verwendet, wenn die Leasesammlung leer ist oder für eine Lease kein ContinuationToken festgelegt wurde. Bei Leases in einer Leasesammlung, für die ein ContinuationToken festgelegt wurde, wird dieses verwendet und ChangeFeedProcessorOptions.StartContinuation ignoriert.

### <a name="223"></a><a id="2.2.3"></a>2.2.3
* Unterstützung für die Verwendung von benutzerdefinierten Speichern für das Beibehalten von Fortsetzungstoken pro Partition wurde hinzugefügt.
  * Ein benutzerdefinierter Leasespeicher kann z.B. eine Azure Cosmos DB-Leasesammlung sein, die auf eine beliebige benutzerdefinierte Weise partitioniert wurde.
  * Benutzerdefinierte Leasespeicher können den neuen Erweiterbarkeitspunkt ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) und die öffentliche ILeaseStoreManager-Schnittstelle verwenden.
  * Die ILeaseManager-Schnittstelle wurde in mehrere Rollenschnittstellen umgestaltet.
* Kleiner Breaking Change: Der Erweiterbarkeitspunkt ChangeFeedProcessorBuilder.WithLeaseManager(ILeaseManager) wurde entfernt. Verwenden Sie stattdessen ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager).

### <a name="222"></a><a id="2.2.2"></a>2.2.2
* Dieses Release behebt ein Problem, das während der Verarbeitung einer Teilung in einer überwachten Sammlung und bei Verwendung einer partitionierten Leasesammlung auftritt. Wenn Sie eine Lease für eine Teilungspartition verarbeiten, wird die Lease für diese Partition möglicherweise nicht gelöscht. Dieses Problem wurde in diesem Release behoben.

### <a name="221"></a><a id="2.2.1"></a>2.2.1
* Die Berechnung der Schätzung für Konten mit mehreren Schreibregionen wurde korrigiert und ein neues Sitzungstokenformat eingeführt.

### <a name="220"></a><a id="2.2.0"></a>2.2.0
* Unterstützung für partitionierte Leasesammlungen wurde hinzugefügt. Der Partitionsschlüssel muss als „/id“ definiert werden.
* Kleinerer Breaking Change: Die Methoden der IChangeFeedDocumentClient-Schnittstelle und der ChangeFeedDocumentClient-Klasse wurden geändert, um die Parameter RequestOptions und CancellationToken einzuschließen. IChangeFeedDocumentClient ist ein verbesserter Erweiterungspunkt, mit dem Sie eine benutzerdefinierte Implementierung des Dokumentclients für die Verwendung mit dem Änderungsfeedprozessor bereitstellen können. Sie können beispielsweise DocumentClient ergänzen und alle Aufrufe an dieses Element abfangen, um eine zusätzliche Ablaufverfolgung, Fehlerbehandlung o. Ä. auszuführen. Mit diesem Update muss der Code, der IChangeFeedDocumentClient implementiert, geändert werden, um neue Parameter in die Implementierung einzuschließen.
* Kleinere Verbesserungen bei der Diagnose.

### <a name="210"></a><a id="2.1.0"></a>2.1.0
* Neue API hinzugefügt, Task&lt;IReadOnlyList&lt;RemainingPartitionWork&gt;&gt; IRemainingWorkEstimator.GetEstimatedRemainingWorkPerPartitionAsync(). Kann verwendet werden, um den geschätzten Arbeitsaufwand für jede Partition abzurufen.
* Unterstützt Microsoft.Azure.DocumentDB-SDK 2.0. Erfordert Microsoft.Azure.DocumentDB 2.0 oder höher.

### <a name="206"></a><a id="2.0.6"></a>2.0.6
* Öffentliche Eigenschaft „ChangeFeedEventHost.HostName“ zur Kompatibilität mit v1 hinzugefügt.

### <a name="205"></a><a id="2.0.5"></a>2.0.5
* Ein Problem mit einer Racebedingung, das während der Partitionsaufteilung auftritt, wurde behoben. Die Racebedingung kann dazu führen, dass das Lease angefordert, bei der Partitionsaufteilung sofort verloren geht und einen Konflikt verursacht. In diesem Release wurde das Problem mit der Racebedingung behoben.

### <a name="204"></a><a id="2.0.4"></a>2.0.4
* Allgemeine Verfügbarkeit (GA) des SDK

### <a name="203-prerelease"></a><a id="2.0.3-prerelease"></a>2.0.3 – Vorabversion
* Die folgenden Probleme wurden behoben:
  * Wenn eine Partition geteilt wird, werden Dokumente, die vor der Teilung modifiziert wurden, möglicherweise doppelt verarbeitet.
  * Die GetEstimatedRemainingWork-API gab 0 zurück, wenn in der Leasesammlung keine Leases vorhanden waren.

* Die folgenden Ausnahmen wurden öffentlich gemacht. Erweiterungen, die IPartitionProcessor implementieren, können diese Ausnahmen auslösen.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="202-prerelease"></a><a id="2.0.2-prerelease"></a>2.0.2 – Vorabversion
* Geringfügige API-Änderungen:
  * Die als veraltet gekennzeichnete ChangeFeedProcessorOptions.IsAutoCheckpointEnabled wurde entfernt.

### <a name="201-prerelease"></a><a id="2.0.1-prerelease"></a>2.0.1 – Vorabversion
* Verbesserungen der Stabilität:
  * Bessere Handhabung der Leasespeicherinitialisierung. Wenn der Leasespeicher leer ist, kann er nur von einer Instanz des Prozessors initialisiert werden, die anderen warten.
  * Stabilere/effizientere Leaseerneuerung/-freigabe. Das Erneuern und Freigeben einer Lease für eine Partition erfolgt unabhängig von der Erneuerung anderer Leases. In v1 erfolgte dies sequenziell für alle Partitionen.
* Neue v2-API:
  * Generatormuster für die flexible Erstellung des Prozessors: ChangeFeedProcessorBuilder-Klasse
    * Akzeptiert beliebige Kombinationen aus Parametern
    * Akzeptiert die DocumentClient-Instanz für die Überwachung und/oder Leasesammlung (nicht in v1 verfügbar)
  * „IChangeFeedObserver.ProcessChangesAsync“ akzeptiert nun „CancellationToken“.
  * IRemainingWorkEstimator: Die Schätzung der verbleibenden Arbeit kann separat vom Prozessor verwendet werden.
  * Neue Erweiterbarkeitspunkte:
    * IPartitionLoadBalancingStrategy: für den benutzerdefinierten Lastenausgleich von Partitionen zwischen Prozessorinstanzen
    * ILease, ILeaseManager: für die benutzerdefinierte Leaseverwaltung
    * IPartitionProcessor: für benutzerdefinierte Verarbeitungsänderungen an einer Partition
* Protokollierung: Verwendet die [LibLog](https://github.com/damianh/LibLog)-Bibliothek
* 100-prozentige Abwärtskompatibilität mit der v1-API
* Neue Codebasis.
* Kompatibel mit [SQL .NET SDK](sql-api-sdk-dotnet.md), Version 1.21.1 und höher

### <a name="v1-builds"></a>V1-Builds

### <a name="133"></a><a id="1.3.3"></a>1.3.3
* Weitere Protokollierung hinzugefügt.
* Ein DocumentClient-Fehler beim mehrfachen Aufrufen der Schätzung für ausstehende Arbeit wurde behoben.

### <a name="132"></a><a id="1.3.2"></a>1.3.2
* Korrekturen der Schätzung ausstehender Arbeit.

### <a name="131"></a><a id="1.3.1"></a>1.3.1
* Verbesserungen der Stabilität.
  * Korrektur für das Behandeln von Problemen mit abgebrochenen Aufgaben, die dazu führen können, dass Beobachter auf einigen Partitionen beendet werden.
* Unterstützung für das manuelle Setzen von Prüfpunkten.
* Kompatibel mit [SQL .NET SDK](sql-api-sdk-dotnet.md), Version 1.21 und höher.

### <a name="120"></a><a id="1.2.0"></a>1.2.0
* Unterstützung für .NET Standard 2.0 wurde hinzugefügt. Das Paket unterstützt nun die Frameworkmoniker `netstandard2.0` und `net451`.
* Kompatibel mit [SQL .NET SDK](sql-api-sdk-dotnet.md), Version 1.17.0 und höher.
* Kompatibel mit [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md), Version 1.5.1 und höher.

### <a name="111"></a><a id="1.1.1"></a>1.1.1
* Ein Problem bei der Kalkulierung der geschätzten verbleibenden Arbeit wurde behoben, wenn der Änderungsfeed leer war oder keine Arbeit ausstand.
* Kompatibel mit [SQL .NET SDK](sql-api-sdk-dotnet.md), Version 1.13.2 und höher.

### <a name="110"></a><a id="1.1.0"></a>1.1.0
* Eine Methode wurde hinzugefügt, mit der eine Schätzung der verbleibenden im Änderungsfeed zu verarbeitenden Arbeit abgerufen werden kann.
* Kompatibel mit [SQL .NET SDK](sql-api-sdk-dotnet.md), Version 1.13.2 und höher.

### <a name="100"></a><a id="1.0.0"></a>1.0.0
* Allgemeine Verfügbarkeit (GA) des SDK
* Kompatibel mit [SQL .NET SDK](sql-api-sdk-dotnet.md), Version 1.14.1 und niedriger.

## <a name="release--retirement-dates"></a>Veröffentlichungs- und Deaktivierungstermine

Wenn Microsoft ein SDK deaktiviert, werden Sie mindestens **12 Monate** vorher benachrichtigt, um einen reibungslosen Übergang zu einer neueren/unterstützten Version zu gewährleisten. Neue Features, Funktionen und Optimierungen werden nur dem aktuellen SDK hinzugefügt. Daher wird empfohlen, immer so früh wie möglich auf die neueste SDK-Version zu aktualisieren.

> [!WARNING]
> Nach dem 31. August 2022 werden von Azure Cosmos DB für die Version 1.x des Azure Cosmos DB .NET oder .NET Core SDK für die SQL-API keine Fehlerbehebungen durchgeführt, keine neue Funktionen hinzugefügt, und es wird keine Unterstützung bereitgestellt. Wenn Sie kein Upgrade durchführen möchten, werden Anforderungen, die von Version 1.x des SDK gesendet werden, weiterhin vom Azure Cosmos DB-Dienst bedient.

<br/>

| Version | Veröffentlichungsdatum | Deaktivierungstermine |
| --- | --- | --- |
| [2.3.2](#2.3.2) |11. August 2020 |--- |
| [2.3.1](#2.3.1) |30. Juli 2020 |--- |
| [2.3.0](#2.3.0) |2\. April 2020 |--- |
| [2.2.8](#2.2.8) |28. Oktober 2019 |--- |
| [2.2.7](#2.2.7) |14. Mai 2019 |--- |
| [2.2.6](#2.2.6) |29. Januar 2019 |--- |
| [2.2.5](#2.2.5) |13. Dezember 2018 |--- |
| [2.2.4](#2.2.4) |29. November 2018 |--- |
| [2.2.3](#2.2.3) |19. November 2018 |--- |
| [2.2.2](#2.2.2) |31. Oktober 2018 |--- |
| [2.2.1](#2.2.1) |24. Oktober 2018 |--- |
| [1.3.3](#1.3.3) |8\. Mai 2018 |--- |
| [1.3.2](#1.3.2) |18. April 2018 |--- |
| [1.3.1](#1.3.1) |13. März 2018 |--- |
| [1.2.0](#1.2.0) |31. Oktober 2017 |--- |
| [1.1.1](#1.1.1) |29. August 2017 |--- |
| [1.1.0](#1.1.0) |13. August 2017 |--- |
| [1.0.0](#1.0.0) |7\. Juli 2017 |--- |

## <a name="faq"></a>Häufig gestellte Fragen

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Weitere Informationen

Weitere Informationen zu Cosmos DB finden Sie auf der Seite zum Dienst [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

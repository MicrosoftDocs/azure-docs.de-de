---
title: 'Azure Cosmos DB: .NET Change Feed Processor-API, -SDK und -Ressourcen'
description: Wichtige Informationen zur Change Feed Processor-API und zum Change Feed Processor-SDK, einschließlich Terminen für Veröffentlichung und Außerbetriebnahme sowie Änderungen an den einzelnen Versionen des .NET Change Feed Processor-SDK.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/30/2019
ms.author: maquaran
ms.openlocfilehash: 2a4d636ccb03e36f7c495f3c10c90033d7c3c93c
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417917"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET Change Feed Processor-SDK: Download und Anmerkungen zum Release

> [!div class="op_single_selector"]
>
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-Änderungsfeed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-Ressourcenanbieter](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [BulkExecutor: .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor: Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**SDK-Download**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API-Dokumentation**|[Referenzdokumentation zum Ändern der Feedprozessorbibliotheks-API](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Erste Schritte**|[Erste Schritte mit dem .NET Change Feed Processor-SDK](change-feed.md)|
|**Aktuelles unterstütztes Framework**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>Versionshinweise

### <a name="v2-builds"></a>V2-Builds

### <a name="a-name227227"></a><a name="2.2.7"/>2.2.7
* Verbesserte Lastenausgleichsstrategie für ein Szenario, bei dem das Abrufen aller Leases länger dauert als das Leaseablaufintervall, z.B. aufgrund von Netzwerkproblemen:
  * In diesem Szenario wird ein Lastenausgleichsalgorithmus verwendet, um Leases fälschlicherweise als abgelaufen zu betrachten, was dazu führt, dass aktiven Besitzern Leases gestohlen werden. Dies könnte einen unnötigen erneuten Ausgleich vieler Leases auslösen.
  * Dieses Problem wurde in der vorliegenden Version folgendermaßen behoben: Bei einem Konflikt wird eine Wiederholung vermieden, wobei die abgelaufene Lease, die der Besitzer nicht geändert hat, abgerufen und dieser Abruf auf die nächste Iteration des Lastenausgleichs verschoben wird.

### <a name="a-name226226"></a><a name="2.2.6"/>2.2.6
* Verbesserte Behandlung von Beobachterausnahmen.
* Umfangreichere Informationen Beobachterfehlern:
  * Wenn ein Beobachter aufgrund einer Ausnahme geschlossen wird, die von „ProcessChangesAsync“ des Beobachters ausgelöst wird, erhält „CloseAsync“ nun den reason-Parameter „ChangeFeedObserverCloseReason.ObserverError“.
  * Hinzugefügte Ablaufverfolgungen zum Bestimmen von Fehlern im Benutzercode bei einem Beobachter.

### <a name="a-name225225"></a><a name="2.2.5"/>2.2.5
* Unterstützung für die Verarbeitung von Aufteilungen von Sammlungen, die gemeinsam genutzten Datenbankdurchsatz verwenden, wurde hinzugefügt.
  * Diese Version behebt ein Problem, das u. U. bei einer Aufteilung von Sammlungen bei gemeinsam genutztem Datenbankdurchsatz entsteht, wenn die Aufteilung zu einem Partitionsausgleich (Partition Rebalancing) führt, bei dem nur ein untergeordneter Partitionsschlüsselbereich (statt zwei) erstellt wird. In diesem Fall kann der Change Feed Processor beim Löschen der Lease für den alten Partitionsschlüsselbereich hängen bleiben und erstellt keine neuen Leases mehr. Dieses Problem wurde in diesem Release behoben.

### <a name="a-name224224"></a><a name="2.2.4"/>2.2.4
* Die Eigenschaft ChangeFeedProcessorOptions.StartContinuation wurde hinzugefügt, um das Starten des Änderungsfeeds über das Fortsetzungstoken für die Anforderung zu unterstützen. Dies wird nur verwendet, wenn die Leasesammlung leer ist oder für eine Lease kein ContinuationToken festgelegt wurde. Bei Leases in einer Leasesammlung, für die ein ContinuationToken festgelegt wurde, wird dieses verwendet und ChangeFeedProcessorOptions.StartContinuation ignoriert.

### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3
* Unterstützung für die Verwendung von benutzerdefinierten Speichern für das Beibehalten von Fortsetzungstoken pro Partition wurde hinzugefügt.
  * Ein benutzerdefinierter Leasespeicher kann z.B. eine Azure Cosmos DB-Leasesammlung sein, die auf eine beliebige benutzerdefinierte Weise partitioniert wurde.
  * Benutzerdefinierte Leasespeicher können den neuen Erweiterbarkeitspunkt ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) und die öffentliche ILeaseStoreManager-Schnittstelle verwenden.
  * Die ILeaseManager-Schnittstelle wurde in mehrere Rollenschnittstellen umgestaltet.
* Kleiner Breaking Change: Der Erweiterbarkeitspunkt ChangeFeedProcessorBuilder.WithLeaseManager(ILeaseManager) wurde entfernt. Verwenden Sie stattdessen ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager).

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* Dieses Release behebt ein Problem, das während der Verarbeitung einer Teilung in einer überwachten Sammlung und bei Verwendung einer partitionierten Leasesammlung auftritt. Wenn Sie eine Lease für eine Teilungspartition verarbeiten, wird die Lease für diese Partition möglicherweise nicht gelöscht. Dieses Problem wurde in diesem Release behoben.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Die Berechnung der Schätzung für Multimasterkonten wurde korrigiert und ein neues Sitzungstokenformat eingeführt.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Unterstützung für partitionierte Leasesammlungen wurde hinzugefügt. Der Partitionsschlüssel muss als „/id“ definiert werden.
* Kleinerer Breaking Change: Die Methoden der IChangeFeedDocumentClient-Schnittstelle und der ChangeFeedDocumentClient-Klasse wurden geändert, um die Parameter RequestOptions und CancellationToken einzuschließen. IChangeFeedDocumentClient ist ein verbesserter Erweiterungspunkt, mit dem Sie eine benutzerdefinierte Implementierung des Document-Clients für die Verwendung bei der Verarbeitung des Änderungsfeeds bereitstellen können. Sie können z.B. DocumentClient ergänzen und alle Aufrufe an diesen abfangen, um eine zusätzliche Ablaufverfolgung, Fehlerbehandlung o.Ä. auszuführen. Mit diesem Update muss der Code, der IChangeFeedDocumentClient implementiert, geändert werden, um neue Parameter in die Implementierung einzuschließen.
* Kleinere Verbesserungen bei der Diagnose.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Neue API hinzugefügt, Task&lt;IReadOnlyList&lt;RemainingPartitionWork&gt;&gt; IRemainingWorkEstimator.GetEstimatedRemainingWorkPerPartitionAsync(). Kann verwendet werden, um den geschätzten Arbeitsaufwand für jede Partition abzurufen.
* Unterstützt Microsoft.Azure.DocumentDB-SDK 2.0. Erfordert Microsoft.Azure.DocumentDB 2.0 oder höher.

### <a name="a-name206206"></a><a name="2.0.6"/>2.0.6
* Öffentliche Eigenschaft „ChangeFeedEventHost.HostName“ zur Kompatibilität mit v1 hinzugefügt.

### <a name="a-name205205"></a><a name="2.0.5"/>2.0.5
* Ein Problem mit einer Racebedingung, das während der Partitionsaufteilung auftritt, wurde behoben. Die Racebedingung kann dazu führen, dass das Lease angefordert, bei der Partitionsaufteilung sofort verloren geht und einen Konflikt verursacht. In diesem Release wurde das Problem mit der Racebedingung behoben.

### <a name="a-name204204"></a><a name="2.0.4"/>2.0.4
* Allgemeine Verfügbarkeit (GA) des SDK

### <a name="a-name203-prerelease203-prerelease"></a><a name="2.0.3-prerelease"/>2.0.3 – Vorabversion
* Die folgenden Probleme wurden behoben:
  * Wenn eine Partition geteilt wird, werden Dokumente, die vor der Teilung modifiziert wurden, möglicherweise doppelt verarbeitet.
  * Die GetEstimatedRemainingWork-API gab 0 zurück, wenn in der Leasesammlung keine Leases vorhanden waren.

* Die folgenden Ausnahmen wurden öffentlich gemacht. Erweiterungen, die IPartitionProcessor implementieren, können diese Ausnahmen auslösen.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="a-name202-prerelease202-prerelease"></a><a name="2.0.2-prerelease"/>2.0.2-Vorabversion
* Geringfügige API-Änderungen:
  * Die als veraltet gekennzeichnete ChangeFeedProcessorOptions.IsAutoCheckpointEnabled wurde entfernt.

### <a name="a-name201-prerelease201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1-prerelease
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

### <a name="a-name133133"></a><a name="1.3.3"/>1.3.3
* Weitere Protokollierung hinzugefügt.
* Ein DocumentClient-Fehler beim mehrfachen Aufrufen der Schätzung für ausstehende Arbeit wurde behoben.

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2
* Korrekturen der Schätzung ausstehender Arbeit.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1
* Verbesserungen der Stabilität.
  * Korrektur für das Behandeln von Problemen mit abgebrochenen Aufgaben, die dazu führen können, dass Beobachter auf einigen Partitionen beendet werden.
* Unterstützung für das manuelle Setzen von Prüfpunkten.
* Kompatibel mit [SQL .NET SDK](sql-api-sdk-dotnet.md), Version 1.21 und höher.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Unterstützung für .NET Standard 2.0 wurde hinzugefügt. Das Paket unterstützt nun die Frameworkmoniker `netstandard2.0` und `net451`.
* Kompatibel mit [SQL .NET SDK](sql-api-sdk-dotnet.md), Version 1.17.0 und höher.
* Kompatibel mit [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md), Version 1.5.1 und höher.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Ein Problem bei der Kalkulierung der geschätzten verbleibenden Arbeit wurde behoben, wenn der Änderungsfeed leer war oder keine Arbeit ausstand.
* Kompatibel mit [SQL .NET SDK](sql-api-sdk-dotnet.md), Version 1.13.2 und höher.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Eine Methode wurde hinzugefügt, mit der eine Schätzung der verbleibenden im Änderungsfeed zu verarbeitenden Arbeit abgerufen werden kann.
* Kompatibel mit [SQL .NET SDK](sql-api-sdk-dotnet.md), Version 1.13.2 und höher.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Allgemeine Verfügbarkeit (GA) des SDK
* Kompatibel mit [SQL .NET SDK](sql-api-sdk-dotnet.md), Version 1.14.1 und niedriger.

## <a name="release--retirement-dates"></a>Veröffentlichungs- und Deaktivierungstermine

Wenn Microsoft ein SDK deaktiviert, werden Sie mindestens **12 Monate** vorher benachrichtigt, um einen reibungslosen Übergang zu einer neueren/unterstützten Version zu gewährleisten.

Neue Features, Funktionen und Optimierungen werden nur dem aktuellen SDK hinzugefügt. Daher wird empfohlen, immer so früh wie möglich auf die neueste SDK-Version zu aktualisieren. 

Anforderungen an Cosmos DB mithilfe eines deaktivierten SDK werden vom Dienst abgelehnt.

<br/>

| Version | Herausgabedatum | Deaktivierungstermine |
| --- | --- | --- |
| [2.2.7](#2.2.7) |14. Mai 2019 |--- |
| [2.2.6](#2.2.6) |29. Januar 2019 |--- |
| [2.2.5](#2.2.5) |13. Dezember 2018 |--- |
| [2.2.4](#2.2.4) |29. November 2018 |--- |
| [2.2.3](#2.2.3) |19. November 2018 |--- |
| [2.2.2](#2.2.2) |31. Oktober 2018 |--- |
| [2.2.1](#2.2.1) |24. Oktober 2018 |--- |
| [1.3.3](#1.3.3) |8. Mai 2018 |--- |
| [1.3.2](#1.3.2) |18. April 2018 |--- |
| [1.3.1](#1.3.1) |13. März 2018 |--- |
| [1.2.0](#1.2.0) |31. Oktober 2017 |--- |
| [1.1.1](#1.1.1) |29. August 2017 |--- |
| [1.1.0](#1.1.0) |13. August 2017 |--- |
| [1.0.0](#1.0.0) |7. Juli 2017 |--- |

## <a name="faq"></a>Häufig gestellte Fragen

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Weitere Informationen

Weitere Informationen zu Cosmos DB finden Sie auf der Seite zum Dienst [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

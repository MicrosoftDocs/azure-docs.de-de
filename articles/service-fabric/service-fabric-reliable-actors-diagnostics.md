---
title: Diagnose und -Überwachung von Reliable Actors | Microsoft Docs
description: Dieser Artikel beschreibt die Features für die Diagnose und Leistungsüberwachung der Reliable Actors-Laufzeit in Service Fabric, einschließlich der davon ausgegebenen Ereignisse und Leistungsindikatoren.
services: service-fabric
documentationcenter: .net
author: abhishekram
manager: chackdan
editor: vturecek
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/26/2017
ms.author: abhisram
ms.openlocfilehash: 5f573db887b3acc2c4a668a8c19c7f8e3cb25019
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60726569"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Diagnose und Leistungsüberwachung für Reliable Actors
Die Reliable Actors-Laufzeit gibt [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx)-Ereignisse und [Leistungsindikatoren](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx) aus. Diese bieten Einblicke in den Laufzeitbetrieb und Hilfe bei der Problembehandlung und Leistungsüberwachung.

## <a name="eventsource-events"></a>EventSource-Ereignisse
Der EventSource-Anbietername für die Reliable Actors-Laufzeit lautet „Microsoft-ServiceFabric-Actors“. Ereignisse aus dieser Ereignisquelle werden beim [Debuggen der Actor-Anwendung in Visual Studio](service-fabric-debugging-your-application.md) im Fenster für [Diagnoseereignisse](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) angezeigt.

Beispiele für Tools und Technologien, mit deren Hilfe EventSource-Ereignisse erfasst und/oder angezeigt werden können, sind [PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md), [Semantic Logging](https://msdn.microsoft.com/library/dn774980.aspx) und die [Microsoft TraceEvent Library](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Schlüsselwörter
Allen Ereignissen, die zu Reliable Actors EventSource gehören, werden ein oder mehrere Schlüsselwörter zugeordnet. Dies ermöglicht das Filtern von erfassten Ereignissen. Die folgenden Schlüsselwort-Bits sind definiert.

| bit | BESCHREIBUNG |
| --- | --- |
| 0x1 |Gruppe von wichtigen Ereignissen, die den Vorgang der Fabric Actors-Laufzeit zusammenfassen. |
| 0x2 |Gruppe von Ereignissen, die Actor-Methodenaufrufe beschreiben. Weitere Informationen finden Sie im [Einführungsthema zu Actors](service-fabric-reliable-actors-introduction.md). |
| 0x4 |Gruppe von Ereignissen im Zusammenhang mit dem Actor-Status. Weitere Informationen finden Sie im Thema unter [Actor-Zustandsverwaltung](service-fabric-reliable-actors-state-management.md). |
| 0x8 |Gruppe von Ereignissen im Zusammenhang mit Turn-basierter Parallelität im Actor. Weitere Informationen finden Sie im Thema zu [Parallelität](service-fabric-reliable-actors-introduction.md#concurrency). |

## <a name="performance-counters"></a>Leistungsindikatoren
Die Reliable Actors-Laufzeit definiert die folgenden Leistungsindikatorkategorien.

| Category (Kategorie) | BESCHREIBUNG |
| --- | --- |
| Service Fabric Actor |Spezielle Indikatoren für Azure Service Fabric Actors, wie z. B. die benötigte Zeit zum Speichern des Actor-Status. |
| Service Fabric Actor-Methode |Spezielle Indikatoren für Methoden, die von Service Fabric Actors implementiert wurden, z. B. wie oft eine Actor-Methode aufgerufen wird. |

Jede der oben genannten Kategorien verfügt über einen oder mehrere Leistungsindikatoren.

Die Anwendung [Windows-Systemmonitor](https://technet.microsoft.com/library/cc749249.aspx) , die standardmäßig im Windows-Betriebssystem verfügbar ist, kann zum Erfassen und Anzeigen von Leistungsindikatordaten verwendet werden. [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) ist eine weitere Option für das Erfassen von Leistungsindikatordaten und Hochladen in Azure-Tabellen.

### <a name="performance-counter-instance-names"></a>Namen von Leistungsindikatorinstanzen
Ein Cluster mit einer großen Anzahl von Actor-Diensten oder Actor-Dienst-Partitionen weist eine große Anzahl von Actor-Leistungsindikatorinstanzen auf. Die Namen der Leistungsindikatorinstanzen können die Identifizierung der speziellen [Partition](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) und Actor-Methode (falls zutreffend) erleichtern, mit denen die Leistungsindikatorinstanz verknüpft ist.

#### <a name="service-fabric-actor-category"></a>Service Fabric Actor-Kategorie
Für die Kategorie `Service Fabric Actor`haben die Namen von Leistungsindikatorinstanzen das folgende Format:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* ist die Zeichenfolgendarstellung der Service Fabric-Partitions-ID, mit der die Leistungsindikatorinstanz verknüpft ist. Die Partitions-ID ist eine GUID. Ihre Zeichenfolgendarstellung wird mithilfe der [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx)-Methode mit dem Formatbezeichner „D“ generiert.

*ActorRuntimeInternalID* ist die Zeichenfolgendarstellung einer 64-Bit-Ganzzahl, die von der Fabric Actors-Laufzeit zur internen Verwendung generiert wird. Sie wird in den Namen der Leistungsindikatorinstanz eingefügt, um deren Eindeutigkeit sicherzustellen und Konflikte mit anderen Namen von Leistungsindikatorinstanzen zu vermeiden. Benutzer sollten nicht versuchen, diesen Teil des Namens der Leistungsindikatorinstanz zu interpretieren.

Nachfolgend finden Sie ein Beispiel für den Namen einer Leistungsindikatorinstanz für einen Indikator, der zur Kategorie `Service Fabric Actor` gehört:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

Im obigen Beispiel ist `2740af29-78aa-44bc-a20b-7e60fb783264` die Zeichenfolgendarstellung der Service Fabric -Partitions-ID, und `635650083799324046` ist die 64-Bit-ID, die für die Laufzeit zur internen Verwendung generiert wird.

#### <a name="service-fabric-actor-method-category"></a>Kategorie „Service Fabric Actor-Methode“
Für die Kategorie `Service Fabric Actor Method`haben die Namen von Leistungsindikatorinstanzen das folgende Format:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* ist der Name der Actor-Methode, mit der die Leistungsindikatorinstanz verknüpft ist. Das Format des Methodennamens wird anhand der Logik in der Fabric Actors-Laufzeit bestimmt, die die Lesbarkeit des Namens durch Einschränkungen für die maximale Länge der Namen von Leistungsindikatorinstanzen unter Windows ausgleicht.

*ActorsRuntimeMethodId* ist die Zeichenfolgendarstellung einer 32-Bit-Ganzzahl, die von der Fabric Actors-Laufzeit zur internen Verwendung generiert wird. Sie wird in den Namen der Leistungsindikatorinstanz eingefügt, um deren Eindeutigkeit sicherzustellen und Konflikte mit anderen Namen von Leistungsindikatorinstanzen zu vermeiden. Benutzer sollten nicht versuchen, diesen Teil des Namens der Leistungsindikatorinstanz zu interpretieren.

*ServiceFabricPartitionID* ist die Zeichenfolgendarstellung der Service Fabric-Partitions-ID, mit der die Leistungsindikatorinstanz verknüpft ist. Die Partitions-ID ist eine GUID. Ihre Zeichenfolgendarstellung wird mithilfe der [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx)-Methode mit dem Formatbezeichner „D“ generiert.

*ActorRuntimeInternalID* ist die Zeichenfolgendarstellung einer 64-Bit-Ganzzahl, die von der Fabric Actors-Laufzeit zur internen Verwendung generiert wird. Sie wird in den Namen der Leistungsindikatorinstanz eingefügt, um deren Eindeutigkeit sicherzustellen und Konflikte mit anderen Namen von Leistungsindikatorinstanzen zu vermeiden. Benutzer sollten nicht versuchen, diesen Teil des Namens der Leistungsindikatorinstanz zu interpretieren.

Nachfolgend finden Sie ein Beispiel für den Namen einer Leistungsindikatorinstanz für einen Indikator, der zur Kategorie `Service Fabric Actor Method` gehört:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

Im obigen Beispiel ist `ivoicemailboxactor.leavemessageasync` der Methodenname, `2` ist die von der Laufzeit zur internen Verwendung generierte 32-Bit-ID, `89383d32-e57e-4a9b-a6ad-57c6792aa521` ist die Zeichenfolgendarstellung der Service Fabric-Partitions-ID, und `635650083804480486` ist die von der Laufzeit zur internen Verwendung generierte 64-Bit-ID.

## <a name="list-of-events-and-performance-counters"></a>Liste von Ereignissen und Leistungsindikatoren
### <a name="actor-method-events-and-performance-counters"></a>Actor-Methodenereignisse und Leistungsindikatoren
Die Reliable Actors-Laufzeit gibt die folgenden Ereignisse im Zusammenhang mit [Actor-Methoden](service-fabric-reliable-actors-introduction.md) aus.

| Ereignisname | Ereignis-ID | Level | Schlüsselwort | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| ActorMethodStart |7 |Ausführlich |0x2 |Die Actors-Laufzeit ruft in Kürze eine Actor-Methode auf. |
| ActorMethodStop |8 |Ausführlich |0x2 |Die Ausführung einer Actor-Methode wurde abgeschlossen. Das heißt, der asynchrone Aufruf der Laufzeit an die Actor-Methode wurde zurückgegeben, und die von der Actor-Methode zurückgegebene Aufgabe ist abgeschlossen. |
| ActorMethodThrewException |9 |Warnung |0x3 |Während der Ausführung einer Actor-Methode wurde eine Ausnahme ausgelöst, und zwar entweder durch den asynchronen Aufruf der Laufzeit an die Actor-Methode oder während der Ausführung der von der Actor-Methode zurückgegebenen Aufgabe. Dieses Ereignis gibt einen Fehler im Actor-Code an, der untersucht werden muss. |

Die Reliable Actors-Laufzeit veröffentlicht die folgenden Leistungsindikatoren im Zusammenhang mit der Ausführung von Actor-Methoden.

| Name der Kategorie | Name des Leistungsindikators | BESCHREIBUNG |
| --- | --- | --- |
| Service Fabric Actor-Methode |Aufrufe pro Sekunde |Anzahl der Aufrufe der Actor-Dienstmethode pro Sekunde |
| Service Fabric Actor-Methode |Durchschnittliche Anzahl von Millisekunden pro Aufruf |Ausführungszeit der Actor-Dienstmethode in Millisekunden |
| Service Fabric Actor-Methode |Ausgelöste Ausnahmen pro Sekunde |Anzahl der von der Actor-Dienstmethode ausgelösten Ausnahmen pro Sekunde |

### <a name="concurrency-events-and-performance-counters"></a>Parallelitätsereignisse und Leistungsindikatoren
Die Reliable Actors-Laufzeit gibt die folgenden Ereignisse im Zusammenhang mit [Parallelität](service-fabric-reliable-actors-introduction.md#concurrency)aus.

| Ereignisname | Ereignis-ID | Level | Schlüsselwort | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| ActorMethodCallsWaitingForLock |12 |Ausführlich |0x8 |Dieses Ereignis wird zu Beginn jedes neuen Turns in einen Actor geschrieben. Es enthält die Anzahl der ausstehenden Actor-Aufrufe, die darauf warten, die Pro-Actor-Sperre zu übernehmen, die Turn-basierte Parallelität erzwingt. |

Die Reliable Actors-Laufzeit veröffentlicht die folgenden Leistungsindikatoren im Zusammenhang mit Parallelität.

| Name der Kategorie | Name des Leistungsindikators | BESCHREIBUNG |
| --- | --- | --- |
| Service Fabric Actor |Anzahl der Actor-Aufrufe, die auf die Actor-Sperre warten |Anzahl der ausstehenden Actor-Aufrufe, die darauf warten, die Pro-Actor-Sperre zu übernehmen, welche Turn-basierte Parallelität erzwingt. |
| Service Fabric Actor |Durchschnittliche Anzahl von Millisekunden bis zur Sperrung des Actors |Zeit (in Millisekunden), die erforderlich war, um die Sperre pro Actor zu erlangen, welche Turn-basierte Parallelität erzwingt |
| Service Fabric Actor |Durchschnittliche Anzahl von Millisekunden, die eine Actor-Sperre hielt |Zeit (in Millisekunden), für die die Sperre pro Actor gehalten wird |

### <a name="actor-state-management-events-and-performance-counters"></a>Actor-Statusmanagement-Ereignisse und Leistungsindikatoren
Die Reliable Actors-Laufzeit gibt die folgenden Ereignisse im Zusammenhang mit der [Actor-Statusverwaltung](service-fabric-reliable-actors-state-management.md)aus.

| Ereignisname | Ereignis-ID | Level | Schlüsselwort | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |Ausführlich |0x4 |Die Actors-Laufzeit speichert in Kürze den Actor-Status. |
| ActorSaveStateStop |11 |Ausführlich |0x4 |Die Actors-Laufzeit hat das Speichern des Actor-Status abgeschlossen. |

Die Reliable Actors-Laufzeit veröffentlicht die folgenden Leistungsindikatoren im Zusammenhang mit der Actor-Statusverwaltung.

| Name der Kategorie | Name des Leistungsindikators | BESCHREIBUNG |
| --- | --- | --- |
| Service Fabric Actor |Durchschnittliche Anzahl von Millisekunden pro Speicherstatusvorgang |Benötigte Zeit in Millisekunden zum Speichern des Actor-Status |
| Service Fabric Actor |Durchschnittliche Anzahl von Millisekunden pro Statusladevorgang |Zeit in Millisekunden, die zum Laden des Actor-Status erforderlich war |

### <a name="events-related-to-actor-replicas"></a>Mit Actor-Replikaten verbundene Ereignisse
Die Reliable Actors-Laufzeit gibt die folgenden Ereignisse im Zusammenhang mit [Actor-Replikaten](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) aus.

| Ereignisname | Ereignis-ID | Level | Schlüsselwort | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| ReplicaChangeRoleToPrimary |1 |Information |0x1 |Rolle von Actor-Replikat auf „Primär“ geändert. Dies bedeutet, dass die Actors für diese Partition in diesem Replikat erstellt werden. |
| ReplicaChangeRoleFromPrimary |2 |Information |0x1 |Rolle von Actor-Replikat auf „Nicht primär“ geändert. Dies bedeutet, dass die Actors für diese Partition nicht mehr in diesem Replikat erstellt werden. An Actors, die bereits in diesem Replikat erstellt wurden, werden keine neuen Anforderungen übermittelt. Die Actors werden zerstört, nachdem alle gerade ausgeführten Anforderungen abgeschlossen sind. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Actor-Aktivierungs- und -Deaktivierungsereignisse und Leistungsindikatoren
Die Reliable Actors-Laufzeit gibt die folgenden Ereignisse im Zusammenhang mit der [Actor-Aktivierung und -Deaktivierung](service-fabric-reliable-actors-lifecycle.md)aus.

| Ereignisname | Ereignis-ID | Level | Schlüsselwort | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| ActorActivated |5 |Information |0x1 |Ein Actor wurde aktiviert. |
| ActorDeactivated |6 |Information |0x1 |Ein Actor wurde deaktiviert. |

Die Reliable Actors-Laufzeit veröffentlicht die folgenden Leistungsindikatoren im Zusammenhang mit der Actor-Aktivierung und -Deaktivierung.

| Name der Kategorie | Name des Leistungsindikators | BESCHREIBUNG |
| --- | --- | --- |
| Service Fabric Actor |Durchschnittliche OnActivateAsync Millisekunden |Zeit (in Millisekunden), die erforderlich war, um die OnActivateAsync-Methode auszuführen |

### <a name="actor-request-processing-performance-counters"></a>Leistungsindikatoren für die Anforderungsverarbeitung durch Actors
Wenn ein Client eine Methode über ein Actor-Proxy-Objekt aufruft, wird eine Anforderungsnachricht über das Netzwerk an den Actor-Dienst gesendet. Der Dienst verarbeitet die Anforderungsnachricht und sendet eine Antwort an den Client zurück. Die Reliable Actors-Laufzeit veröffentlicht die folgenden Leistungsindikatoren im Zusammenhang mit der Anforderungsverarbeitung durch Actors.

| Name der Kategorie | Name des Leistungsindikators | BESCHREIBUNG |
| --- | --- | --- |
| Service Fabric Actor |Anzahl von ausstehenden Anfragen |Anzahl von Anforderungen, die im Dienst verarbeitet werden |
| Service Fabric Actor |Durchschnittliche Anzahl von Millisekunden pro Anforderung |Zeit (in Millisekunden), die der Dienst zum Verarbeiten einer Anforderung erforderte |
| Service Fabric Actor |Durchschnittliche Anzahl von Millisekunden für die Anforderungsdeserialisierung |Zeit (in Millisekunden), die erforderlich war, um die Anforderungsnachricht an den Actor zum Empfangszeitpunkt am Dienst zu deserialisieren |
| Service Fabric Actor |Durchschnittliche Anzahl von Millisekunden für die Anwortserialisierung |Zeit (in Millisekunden), die erforderlich war, um die Antwortnachricht des Actors vor der Versendung an den Client am Dienst zu serialisieren |

## <a name="next-steps"></a>Nächste Schritte
* [Verwendung der Service Fabric-Plattform durch Reliable Actors](service-fabric-reliable-actors-platform.md)
* [Actor-API-Referenzdokumentation](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Beispielcode](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [EventSource-Anbieter in PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)

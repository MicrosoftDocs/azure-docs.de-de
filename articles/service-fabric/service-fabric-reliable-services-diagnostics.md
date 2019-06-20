---
title: Diagnose für zustandsbehaftete zuverlässige Dienste in Azure Service Fabric | Microsoft-Dokumentation
description: Diagnosefunktionen für zustandsbehaftete zuverlässige Dienste in Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ae0e8f99-69ab-4d45-896d-1fa80ed45659
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/24/2018
ms.author: dekapur
ms.openlocfilehash: f49176f944aa2abfa1d355ce0bd207d1b544c275
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60772957"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Diagnosefunktionen für zustandsbehaftete Reliable Services
Die StatefulServiceBase-Klasse der zustandsbehafteten zuverlässigen Dienste in Azure Service Fabric gibt [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx)-Ereignisse aus. Diese können verwendet werden, um den Dienst zu debuggen, Einblicke in den Laufzeitbetrieb zu erhalten und Fehler zu beheben.

## <a name="eventsource-events"></a>EventSource-Ereignisse
Der Name des EventSource-Ereignisses für die Klasse „StatefulServiceBase“ der zustandsbehafteten zuverlässigen Dienste lautet „Microsoft-ServiceFabric-Services“. Ereignisse aus dieser Ereignisquelle werden beim [Debuggen des Diensts in Visual Studio](service-fabric-debugging-your-application.md) im Fenster für [Diagnoseereignisse](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) angezeigt.

Beispiele für Tools und Technologien, mit deren Hilfe EventSource-Ereignisse erfasst bzw. angezeigt werden können, sind [PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) und [Microsoft TraceEvent Library](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Events
| Ereignisname | Ereignis-ID | Level | Ereignisbeschreibung |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Information |Wird beim Start der RunAsync-Dienstaufgabe ausgegeben |
| StatefulRunAsyncCancellation |2 |Information |Wird beim Abbruch der RunAsync-Dienstaufgabe ausgegeben |
| StatefulRunAsyncCompletion |3 |Information |Wird nach Abschluss der RunAsync-Dienstaufgabe ausgegeben |
| StatefulRunAsyncSlowCancellation |4 |Warnung |Wird ausgegeben, wenn das Abbrechen der RunAsync-Dienstaufgabe zu lange dauert |
| StatefulRunAsyncFailure |5 |Error |Wird bei einer Ausnahme der RunAsync-Dienstaufgabe ausgegeben |

## <a name="interpret-events"></a>Interpretieren von Ereignissen
Die Ereignisse „StatefulRunAsyncInvocation“, „StatefulRunAsyncCompletion“ und „StatefulRunAsyncCancellation“ sind für den Dienstautor nützlich, um den Lebenszyklus eines Diensts sowie die zeitliche Steuerung des Starts, Abbruchs oder Abschlusses eines Diensts zu verstehen. Diese Informationen können hilfreich sein, um Probleme bei Diensten zu debuggen oder den Dienstlebenszyklus zu verstehen.

Dienstautoren sollten unbedingt auf die Ereignisse StatefulRunAsyncSlowCancellation und StatefulRunAsyncFailure achten, da sie auf Probleme mit dem Dienst hinweisen.

StatefulRunAsyncFailure wird ausgegeben, wenn RunAsync() eine Ausnahme ausgibt. In der Regel weist eine Ausnahme auf einen Fehler oder Bug im Dienst hin. Darüber hinaus bewirkt die Ausnahme, dass der Dienst nicht ausgeführt wird, sodass er auf einen anderen Knoten verschoben wird. Dieser Vorgang kann aufwendig sein und eingehende Anforderungen verzögern, während der Dienst verschoben wird. Dienstautoren sollten nach Möglichkeit die Ursache der Ausnahme ermitteln und beheben.

StatefulRunAsyncSlowCancellation wird ausgegeben, wenn eine Abbruchanforderung für RunAsync länger als vier Sekunden dauert. Wenn das Abbrechen eines Diensts zu lange dauert, beeinträchtigt dies seine Fähigkeit, umgehend auf einem anderen Knoten neu gestartet werden zu können. Dieses Szenario kann sich negativ auf die allgemeine Verfügbarkeit des Diensts auswirken.

## <a name="performance-counters"></a>Leistungsindikatoren
Die Reliable Services-Runtime definiert die folgenden Leistungsindikatorkategorien:

| Category (Kategorie) | BESCHREIBUNG |
| --- | --- |
| Service Fabric-Transaktionsreplikator |Spezifische Leistungsindikatoren für den Azure Service Fabric-Transaktionsreplikator. |
| Service Fabric TStore |Spezifische Leistungsindikatoren für den Azure Service Fabric TStore |

Der [Reliable State Manager](service-fabric-reliable-services-reliable-collections-internals.md) verwendet den Service Fabric-Transaktionsreplikator zum Replizieren von Transaktionen in einer bestimmten Gruppe von [Replikaten](service-fabric-concepts-replica-lifecycle.md).

Der Service Fabric TStore ist eine Komponente, die in [zuverlässigen Sammlungen](service-fabric-reliable-services-reliable-collections-internals.md) zum Speichern und Abrufen von Schlüssel-Wert-Paaren verwendet wird.

Die Anwendung [Windows-Systemmonitor](https://technet.microsoft.com/library/cc749249.aspx) , die standardmäßig im Windows-Betriebssystem verfügbar ist, kann zum Erfassen und Anzeigen von Leistungsindikatordaten verwendet werden. [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) ist eine weitere Option für das Erfassen von Leistungsindikatordaten und Hochladen in Azure-Tabellen.

### <a name="performance-counter-instance-names"></a>Namen von Leistungsindikatorinstanzen
Ein Cluster mit einer großen Anzahl von Reliable Services oder Reliable Service-Partitionen weist eine große Anzahl von Transaktionsreplikator-Leistungsindikatorinstanzen auf. Dies gilt auch für TStore-Leistungsindikatoren, wird jedoch auch durch die Anzahl der verwendeten zuverlässigen Wörterbüchern und zuverlässigen Warteschlangen multipliziert. Die Namen der Leistungsindikatorinstanzen können die Identifizierung spezifischer [Partitionen](service-fabric-concepts-partitioning.md), Dienstreplikate und Zustandsanbieter erleichtern, mit denen die Leistungsindikatorinstanz verknüpft ist.

#### <a name="service-fabric-transactional-replicator-category"></a>Service Fabric-Transaktionsreplikator-Kategorie
Für die Kategorie `Service Fabric Transactional Replicator`haben die Namen von Leistungsindikatorinstanzen das folgende Format:

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId* ist die Zeichenfolgendarstellung der Service Fabric-Partitions-ID, mit der die Leistungsindikatorinstanz verknüpft ist. Die Partitions-ID ist eine GUID. Ihre Zeichenfolgendarstellung wird mithilfe von [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) mit dem Formatbezeichner „D“ generiert.

*ServiceFabricReplicaId* die ID, die einem bestimmten Replikat eines Reliable Service zugeordnet ist. Die Replikat-ID wird in den Namen der Leistungsindikatorinstanz eingefügt, um deren Eindeutigkeit sicherzustellen und Konflikte mit anderen, von derselben Partition generierten Leistungsindikatorinstanzen zu vermeiden. Weitere Details zu Replikaten und ihrer Rolle in Reliable Services finden Sie [hier](service-fabric-concepts-replica-lifecycle.md).

Der folgende Indikatorinstanzname ist typisch für einen Indikator unter der `Service Fabric Transactional Replicator`-Kategorie:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

Im vorherigen Beispiel ist `00d0126d-3e36-4d68-98da-cc4f7195d85e` die Zeichenfolgendarstellung der Service Fabric-Partitions-ID und `131652217797162571` die Replikat-ID.

#### <a name="service-fabric-tstore-category"></a>Service Fabric TStore-Kategorie
Für die Kategorie `Service Fabric TStore`haben die Namen von Leistungsindikatorinstanzen das folgende Format:

`ServiceFabricPartitionId:ServiceFabricReplicaId:ServiceFabricStateProviderId_PerformanceCounterInstanceDifferentiator`

*ServiceFabricPartitionId* ist die Zeichenfolgendarstellung der Service Fabric-Partitions-ID, mit der die Leistungsindikatorinstanz verknüpft ist. Die Partitions-ID ist eine GUID. Ihre Zeichenfolgendarstellung wird mithilfe von [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) mit dem Formatbezeichner „D“ generiert.

*ServiceFabricReplicaId* die ID, die einem bestimmten Replikat eines Reliable Service zugeordnet ist. Die Replikat-ID wird in den Namen der Leistungsindikatorinstanz eingefügt, um deren Eindeutigkeit sicherzustellen und Konflikte mit anderen, von derselben Partition generierten Leistungsindikatorinstanzen zu vermeiden. Weitere Details zu Replikaten und ihrer Rolle in Reliable Services finden Sie [hier](service-fabric-concepts-replica-lifecycle.md).

*ServiceFabricStateProviderId* ist die einem Anbieter zugeordnete ID innerhalb eines zuverlässigen Diensts. Die Zustandsanbieter-ID ist im Namen der Leistungsindikatorinstanz enthalten, damit TStores voneinander unterschieden werden können.

*PerformanceCounterInstanceDifferentiator* ist eine als Unterscheidungsmerkmal dienende ID, die einer Leistungsindikatorinstanz in einem Zustandsanbieter zugeordnet ist. Das Unterscheidungsmerkmal wird in den Namen der Leistungsindikatorinstanz eingefügt, um deren Eindeutigkeit sicherzustellen und Konflikte mit anderen, von demselben Zustandsanbieter generierten Leistungsindikatorinstanzen zu vermeiden.

Der folgende Indikatorinstanzname ist typisch für einen Indikator unter der `Service Fabric TStore`-Kategorie:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571:142652217797162571_1337`

Im vorherigen Beispiel ist `00d0126d-3e36-4d68-98da-cc4f7195d85e` die Zeichenfolgendarstellung der Service Fabric-Partitions-ID, `131652217797162571` die Replikat-ID, `142652217797162571` die Zustandsanbieter-ID und `1337` das Unterscheidungsmerkmal der Leistungsindikatorinstanz.

### <a name="transactional-replicator-performance-counters"></a>Transaktionsreplikator-Leistungsindikatoren

Die Reliable Services-Runtime gibt die folgenden Ereignisse unter der `Service Fabric Transactional Replicator`-Kategorie aus.

 Name des Leistungsindikators | BESCHREIBUNG |
| --- | --- |
| Gestartete Transaktionsvorgänge/Sek | Die Anzahl der neuen Schreibtransaktionen, die pro Sekunde erstellt werden.|
| Transaktionsvorgänge/Sek | Die Anzahl der pro Sekunde für zuverlässige Sammlungen durchgeführten Hinzufügungs-/Aktualisierungs-/Löschvorgänge.|
| Protokollleerung in Bytes/Sek | Die Anzahl von Bytes, die pro Sekunde vom Transaktionsreplikator auf den Datenträger geleert werden |
| Gedrosselte Vorgänge/Sek | Die Anzahl der Vorgänge, die pro Sekunde vom Transaktionsreplikator aufgrund von Drosselung abgelehnt werden. |
| Durchschn. Transaktionsdauer in ms/Commit | Durchschnittliche Commitlatenz pro Transaktion in Millisekunden |
| Durchschn. Wartezeit beim Leeren (ms) | Durchschnittliche Dauer der Vorgänge des Leerens auf die Festplatte, die vom Transaktionsreplikator gestartet werden, in Millisekunden |

### <a name="tstore-performance-counters"></a>TStore-Leistungsindikatoren

Die Reliable Services-Runtime gibt die folgenden Ereignisse unter der `Service Fabric TStore`-Kategorie aus.

 Name des Leistungsindikators | BESCHREIBUNG |
| --- | --- |
| Anzahl der Elemente | Die Anzahl von Elementen im Speicher|
| Datenträgergröße | Gesamtgröße von Prüfpunktdateien für den Speicher auf dem Datenträger in Byte|
| Für Prüfpunktdatei geschriebene Bytes/s | Die Anzahl von Bytes, die pro Sekunde für die letzte Prüfpunktdatei geschrieben wurden|
| Beim Kopieren des Datenträgers übertragene Bytes/s | Die Anzahl gelesener (auf primärem Replikat) oder geschriebener (auf sekundärem Replikat) Datenträgerbytes pro Sekunde beim Speichern einer Kopie|

## <a name="next-steps"></a>Nächste Schritte
[EventSource-Anbieter in PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)

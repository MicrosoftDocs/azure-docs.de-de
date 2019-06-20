---
title: Azure Service Fabric-Diagnose und -Überwachung | Microsoft-Dokumentation
description: In diesem Artikel werden die Funktionen zur Leistungsüberwachung in der Service Fabric Reliable ServiceRemoting-Runtime beschrieben, z.B. die ausgegebenen Leistungsindikatoren.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: chackdan
editor: suchiagicha
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: suchiagicha
ms.openlocfilehash: 01430c40ec9fcf1af3a463f8f86d646d15b6dd49
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64925940"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Diagnose und Leistungsüberwachung für Reliable ServiceRemoting
Die Reliable ServiceRemoting-Runtime gibt [Leistungsindikatoren](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx) aus. Diese bieten einen Einblick in die Funktion von ServiceRemoting und unterstützen bei der Problembehandlung und Leistungsüberwachung.


## <a name="performance-counters"></a>Leistungsindikatoren
Die Reliable ServiceRemoting-Runtime definiert die folgenden Leistungsindikatorkategorien:

| Category (Kategorie) | BESCHREIBUNG |
| --- | --- |
| Service Fabric-Dienst |Leistungsindikatoren für Azure Service Fabric ServiceRemoting, beispielsweise die durchschnittliche Zeit zum Verarbeiten der Anforderung |
| Service Fabric-Dienstmethode |Leistungsindikatoren für Methoden, die vom Service Fabric Remoting-Dienst implementiert werden, z.B. wie oft eine Dienstmethode aufgerufen wird |

Jede der genannten Kategorien verfügt über einen oder mehrere Leistungsindikatoren.

Die Anwendung [Windows-Systemmonitor](https://technet.microsoft.com/library/cc749249.aspx) , die standardmäßig im Windows-Betriebssystem verfügbar ist, kann zum Erfassen und Anzeigen von Leistungsindikatordaten verwendet werden. [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) ist eine weitere Option für das Erfassen von Leistungsindikatordaten und Hochladen in Azure-Tabellen.

### <a name="performance-counter-instance-names"></a>Namen von Leistungsindikatorinstanzen
Ein Cluster mit einer großen Anzahl von ServiceRemoting-Diensten oder -Partitionen weist eine große Anzahl von Leistungsindikatorinstanzen auf. Die Namen der Leistungsindikatorinstanzen können die Identifizierung der speziellen Partition und Dienstmethode (sofern zutreffend) erleichtern, mit denen die Leistungsindikatorinstanz verknüpft ist.

#### <a name="service-fabric-service-category"></a>Service Fabric Service-Kategorie
Für die Kategorie `Service Fabric Service`haben die Namen von Leistungsindikatorinstanzen das folgende Format:

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* ist die Zeichenfolgendarstellung der Service Fabric-Partitions-ID, mit der die Leistungsindikatorinstanz verknüpft ist. Die Partitions-ID ist eine GUID. Ihre Zeichenfolgendarstellung wird mithilfe der [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx)-Methode mit dem Formatbezeichner „D“ generiert.

*ServiceReplicaOrInstanceId* ist die Zeichenfolgendarstellung der Service Fabric-Replikat-/-Instanz-ID, der die Leistungsindikatorinstanz zugeordnet ist.

*ServiceRuntimeInternalID* ist die Zeichenfolgendarstellung einer 64-Bit-Ganzzahl, die von der Fabric-Dienst-Runtime zur internen Verwendung generiert wird. Sie wird in den Namen der Leistungsindikatorinstanz eingefügt, um deren Eindeutigkeit sicherzustellen und Konflikte mit anderen Namen von Leistungsindikatorinstanzen zu vermeiden. Benutzer sollten nicht versuchen, diesen Teil des Namens der Leistungsindikatorinstanz zu interpretieren.

Nachfolgend finden Sie ein Beispiel für den Namen einer Leistungsindikatorinstanz für einen Indikator, der zur Kategorie `Service Fabric Service` gehört:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

Im Beispiel oben ist `2740af29-78aa-44bc-a20b-7e60fb783264` die Zeichenfolgendarstellung der Service Fabric-Partitions-ID, `635650083799324046` ist die Zeichenfolgendarstellung der Replikat-ID/InstanceId, und `5008379932` ist die 64-Bit-ID, die für die Runtime zur internen Verwendung generiert wird.

#### <a name="service-fabric-service-method-category"></a>Kategorien der Service Fabric-Dienstmethoden
Für die Kategorie `Service Fabric Service Method`haben die Namen von Leistungsindikatorinstanzen das folgende Format:

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*MethodName* ist der Name der Dienstmethode, der die Leistungsindikatorinstanz zugeordnet ist. Das Format des Methodennamens wird anhand der Logik in der Fabric-Dienst-Runtime bestimmt, die die Lesbarkeit des Namens durch Einschränkungen für die maximale Länge der Namen von Leistungsindikatorinstanzen unter Windows ausgleicht.

*ServiceRuntimeMethodId* ist die Zeichenfolgendarstellung einer 32-Bit-Ganzzahl, die von der Fabric-Dienst-Runtime zur internen Verwendung generiert wird. Sie wird in den Namen der Leistungsindikatorinstanz eingefügt, um deren Eindeutigkeit sicherzustellen und Konflikte mit anderen Namen von Leistungsindikatorinstanzen zu vermeiden. Benutzer sollten nicht versuchen, diesen Teil des Namens der Leistungsindikatorinstanz zu interpretieren.

*ServiceFabricPartitionID* ist die Zeichenfolgendarstellung der Service Fabric-Partitions-ID, mit der die Leistungsindikatorinstanz verknüpft ist. Die Partitions-ID ist eine GUID. Ihre Zeichenfolgendarstellung wird mithilfe der [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx)-Methode mit dem Formatbezeichner „D“ generiert.

*ServiceReplicaOrInstanceId* ist die Zeichenfolgendarstellung der Service Fabric-Replikat-/-Instanz-ID, der die Leistungsindikatorinstanz zugeordnet ist.

*ServiceRuntimeInternalID* ist die Zeichenfolgendarstellung einer 64-Bit-Ganzzahl, die von der Fabric-Dienst-Runtime zur internen Verwendung generiert wird. Sie wird in den Namen der Leistungsindikatorinstanz eingefügt, um deren Eindeutigkeit sicherzustellen und Konflikte mit anderen Namen von Leistungsindikatorinstanzen zu vermeiden. Benutzer sollten nicht versuchen, diesen Teil des Namens der Leistungsindikatorinstanz zu interpretieren.

Nachfolgend finden Sie ein Beispiel für den Namen einer Leistungsindikatorinstanz für einen Indikator, der zur Kategorie `Service Fabric Service Method` gehört:

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

Im Beispiel oben ist `ivoicemailboxservice.leavemessageasync` der Methodenname, `2` ist die von der Runtime zur internen Verwendung generierte 32-Bit-ID, `89383d32-e57e-4a9b-a6ad-57c6792aa521` ist die Zeichenfolgendarstellung der Service Fabric-Partitions-ID, `635650083804480486` ist die Zeichenfolgendarstellung der Service Fabric-Replikat-/-Instanz-ID, und `5008380` ist die von der Runtime zur internen Verwendung generierte 64-Bit-ID.

## <a name="list-of-performance-counters"></a>Liste der Leistungsindikatoren
### <a name="service-method-performance-counters"></a>Leistungsindikatoren der Dienstmethode

Die Reliable Service-Runtime veröffentlicht die folgenden Leistungsindikatoren im Zusammenhang mit der Ausführung von Dienstmethoden.

| Name der Kategorie | Name des Leistungsindikators | BESCHREIBUNG |
| --- | --- | --- |
| Service Fabric-Dienstmethode |Aufrufe pro Sekunde |Anzahl der Aufrufe der Dienstmethode pro Sekunde |
| Service Fabric-Dienstmethode |Durchschnittliche Anzahl von Millisekunden pro Aufruf |Ausführungsdauer der Dienstmethode in Millisekunden |
| Service Fabric-Dienstmethode |Ausgelöste Ausnahmen pro Sekunde |Anzahl der von der Dienstmethode ausgelösten Ausnahmen pro Sekunde |

### <a name="service-request-processing-performance-counters"></a>Leistungsindikatoren für die Dienstanforderungsverarbeitung
Wenn ein Client eine Methode über ein Dienst-Proxy-Objekt aufruft, wird eine Anforderungsnachricht über das Netzwerk an den Remoting-Dienst gesendet. Der Dienst verarbeitet die Anforderungsnachricht und sendet eine Antwort an den Client zurück. Die Reliable ServiceRemoting-Runtime veröffentlicht die folgenden Leistungsindikatoren im Zusammenhang mit der Verarbeitung von Dienstanforderungen.

| Name der Kategorie | Name des Leistungsindikators | BESCHREIBUNG |
| --- | --- | --- |
| Service Fabric-Dienst |Anzahl von ausstehenden Anfragen |Anzahl von Anforderungen, die im Dienst verarbeitet werden |
| Service Fabric-Dienst |Durchschnittliche Anzahl von Millisekunden pro Anforderung |Zeit (in Millisekunden), die der Dienst zum Verarbeiten einer Anforderung erforderte |
| Service Fabric-Dienst |Durchschnittliche Anzahl von Millisekunden für die Anforderungsdeserialisierung |Zeit (in Millisekunden), die erforderlich war, um die Dienstanforderungsnachricht zum Empfangszeitpunkt beim Dienst zu deserialisieren |
| Service Fabric-Dienst |Durchschnittliche Anzahl von Millisekunden für die Anwortserialisierung |Zeit (in Millisekunden), die erforderlich war, um die Dienstantwortnachricht vor dem Senden an den Client beim Dienst zu serialisieren |

## <a name="next-steps"></a>Nächste Schritte
* [Beispielcode](https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0)
* [EventSource-Anbieter in PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)

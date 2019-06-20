---
title: Leistungsüberwachung in Azure Service Fabric | Microsoft-Dokumentation
description: Erfahren Sie mehr zu Leistungsindikatoren zum Überwachen und für Diagnosen von Azure Service Fabric-Clustern.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: ee1608c40801f568b38ace4670b0d5ea7f73003c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60392890"
---
# <a name="performance-metrics"></a>Leistungsmetriken

Sie sollten Metriken erfassen, um einen besseren Einblick in die Leistung Ihrer Cluster sowie der darin ausgeführten Anwendungen zu erhalten. Es wird empfohlen, für Service Fabric-Cluster die folgenden Leistungsindikatoren zu erfassen.

## <a name="nodes"></a>Nodes

Für die Computer in Ihrem Cluster sollten Sie das Erfassen der folgenden Leistungsindikatoren in Erwägung ziehen, um Einblick in die Last jedes Computers zu erhalten und entsprechende Entscheidungen zur Skalierung des Clusters treffen zu können.

| Indikatorkategorie | Name des Leistungsindikators |
| --- | --- |
| Logischer Datenträger | Logischer Datenträger – verfügbarer Speicherplatz |
| PhysicalDisk (pro Datenträger) | Durchschn. Warteschlangenlänge der Datenträger-Lesevorgänge |
| PhysicalDisk (pro Datenträger) | Durchschn. Warteschlangenlänge der Datenträger-Schreibvorgänge |
| PhysicalDisk (pro Datenträger) | Durchschn. Datenträger s/gelesen |
| PhysicalDisk (pro Datenträger) | Durchschn. Datenträger s/geschrieben |
| PhysicalDisk (pro Datenträger) | Lesevorgänge/s |
| PhysicalDisk (pro Datenträger) | Byte gelesen/s |
| PhysicalDisk (pro Datenträger) | Schreibvorgänge/s |
| PhysicalDisk (pro Datenträger) | Byte geschrieben/s |
| Arbeitsspeicher | Verfügbare MB |
| PagingFile | Prozent genutzt |
| Prozessor (gesamt) | % Prozessorzeit |
| Prozess (pro Dienst) | % Prozessorzeit |
| Prozess (pro Dienst) | Prozess-ID |
| Prozess (pro Dienst) | Private Bytes |
| Prozess (pro Dienst) | Threadanzahl |
| Prozess (pro Dienst) | Virtuelle Bytes |
| Prozess (pro Dienst) | Arbeitssatz |
| Prozess (pro Dienst) | Arbeitsseiten (privat) |
| Netzwerkschnittstelle (alle Instanzen) | Erfasste Bytes |
| Netzwerkschnittstelle (alle Instanzen) | Gesendete Bytes |
| Netzwerkschnittstelle (alle Instanzen) | Bytes gesamt |
| Netzwerkschnittstelle (alle Instanzen) | Länge der Ausgabewarteschlange |
| Netzwerkschnittstelle (alle Instanzen) | Verworfene ausgehende Pakete |
| Netzwerkschnittstelle (alle Instanzen) | Verworfene empfangene Pakete |
| Netzwerkschnittstelle (alle Instanzen) | Ausgehende Pakete mit Fehlern |
| Netzwerkschnittstelle (alle Instanzen) | Empfangene Pakete mit Fehlern |

## <a name="net-applications-and-services"></a>.NET-Anwendungen und -Dienste

Erfassen Sie die folgenden Leistungsindikatoren, wenn Sie .NET-Dienste an Ihren Cluster bereitstellen. 

| Indikatorkategorie | Name des Leistungsindikators |
| --- | --- |
| .NET CLR Memory (pro Dienst) | Prozess-ID |
| .NET CLR Memory (pro Dienst) | Anzahl von zugesicherten Bytes |
| .NET CLR Memory (pro Dienst) | Anzahl von reservierten Bytes |
| .NET CLR Memory (pro Dienst) | Anzahl von Bytes in allen Heaps |
| .NET CLR Memory (pro Dienst) | Objektheapgröße |
| .NET CLR Memory (pro Dienst) | Anzahl der GC-Handle |
| .NET CLR Memory (pro Dienst) | Anzahl von Gen 0-Sammlungen |
| .NET CLR Memory (pro Dienst) | Anzahl von Gen 1-Sammlungen |
| .NET CLR Memory (pro Dienst) | Anzahl von Gen 2-Sammlungen |
| .NET CLR Memory (pro Dienst) | GC-Zeitdauer in Prozent |

### <a name="service-fabrics-custom-performance-counters"></a>Benutzerdefinierte Leistungsindikatoren von Service Fabric

Service Fabric generiert eine beträchtliche Menge an benutzerdefinierten Leistungsindikatoren. Wenn Sie das SDK installiert haben, sehen Sie eine umfassende Liste in Ihrer Leistungsmonitoranwendung auf Ihrem Windows-Computer (Start > Leistungsmonitor). 

In der von Ihnen für Ihren Cluster bereitgestellten Anwendung können Sie Indikatoren über die Kategorien `Service Fabric Actor` und `Service Fabric Actor Method` hinzufügen, wenn Sie Reliable Actors verwenden. (Weitere Informationen finden Sie unter [Diagnose und Leistungsüberwachung für Reliable Actors](service-fabric-reliable-actors-diagnostics.md).)

Bei Verwendung von Reliable Services oder Dienstremoting stehen analog dazu die Indikatorkategorien `Service Fabric Service` und `Service Fabric Service Method` zur Verfügung, aus denen Sie Leistungsindikatoren erfassen sollten (siehe [Diagnose und Leistungsüberwachung für Reliable ServiceRemoting](service-fabric-reliable-serviceremoting-diagnostics.md) bzw. [Leistungsindikatoren](service-fabric-reliable-services-diagnostics.md#performance-counters)). 

Wenn Sie Reliable Collections verwenden, wird empfohlen, den `Avg. Transaction ms/Commit` aus dem `Service Fabric Transactional Replicator` hinzuzufügen, um die durchschnittliche Wartezeit pro Transaktion zu erfassen.


## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [die Ereignisgenerierung auf Plattformebene](service-fabric-diagnostics-event-generation-infra.md) in Service Fabric.
* Erfassen von Leistungsmetriken über den [Log Analytics-Agent](service-fabric-diagnostics-oms-agent.md)

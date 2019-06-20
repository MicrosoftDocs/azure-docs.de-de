---
title: Defragmentierung von Metriken in Azure Service Fabric | Microsoft Docs
description: Eine Übersicht über den Einsatz von Defragmentierung oder Überlastung als Strategie für Metriken in Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: e5ebfae5-c8f7-4d6c-9173-3e22a9730552
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 6e041e41372c72c6792c1fb4a1fbdc3bbe475b21
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60844395"
---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Defragmentierung von Metriken und Lasten in Service Fabric
Die Standardstrategie des Clusterressourcen-Managers von Service Fabric beim Verwalten von Auslastungsmetriken besteht darin, die Auslastung zu verteilen. Durch die Sicherstellung der gleichmäßigen Nutzung der Knoten werden Hotspots und Coldspots vermieden, die gleichermaßen zu Konflikten und vergeudeten Ressourcen führen. Die Verteilung von Workloads im Cluster ist zudem das sicherste Konzept im Hinblick auf das Überstehen von Ausfällen, da dadurch sichergestellt wird, dass bei einem Ausfall kein hoher Prozentsatz einer Workload verloren geht. 

Der Clusterressourcen-Manager von Service Fabric unterstützt eine andere Strategie, die als Defragmentierung bezeichnet wird. Defragmentierung bedeutet, dass die Nutzung einer Metrik nicht auf den Cluster verteilt wird, sondern dass sie konsolidiert wird. Die Konsolidierung ist lediglich eine Umkehrung der standardmäßigen Ausgleichsstrategie – anstatt die durchschnittliche Standardabweichung der Metriklast zu minimieren, zielt der Clusterressourcen-Manager darauf ab, sie zu vergrößern.

## <a name="when-to-use-defragmentation"></a>Situationen, in denen Defragmentierung verwendet wird
Beim Verteilen der Last auf den Cluster werden einige Ressourcen auf jedem Knoten verbraucht. Einige Workloads erstellen Dienste, die außergewöhnlich groß sind und den größten Teil eines Knotens oder den Knoten komplett nutzen. In solchen Fällen ist es möglich, dass bei der Erstellung großer Workloads kein ausreichender Platz auf einem Knoten vorhanden ist, um sie auszuführen. Große Workloads stellen in Service Fabric kein Problem dar; in derartigen Fällen stellt der Clusterressourcen-Manager fest, dass der Cluster neu organisiert werden muss, um Platz für die große Workload zu schaffen. In der Zwischenzeit muss diese Workload jedoch warten, bis sie im Cluster geplant werden kann.

Wenn viele Dienste und Status zu verschieben sind, kann es lange dauern, bis die große Workload im Cluster platziert wird. Dies ist wahrscheinlicher, wenn andere Workloads im Cluster ebenfalls groß sind und die Neuorganisation daher länger dauert. Das Service Fabric-Team hat die Erstellungszeiten in Simulationen dieses Szenarios gemessen. Wir haben festgestellt, dass das Erstellen großer Dienste viel länger dauerte, sobald die Clusterauslastung über 30% bis 50% anstieg. Um dieses Szenario zu verarbeiten, haben wir die Defragmentierung als Strategie für den Lastenausgleich eingeführt. Wir haben festgestellt, dass bei großen Workloads – insbesondere solchen, bei denen die Erstellungsdauer eine große Rolle spielt – die Defragmentierung in hohem Maß dazu beiträgt, dass diese neuen Workloads im Cluster geplant werden können.

Sie können Defragmentierungsmetriken konfigurieren, damit der Clusterressourcen-Manager proaktiv versucht, die Last der Dienste auf eine geringere Anzahl von Knoten zu verteilen. So lässt sich sicherstellen, dass fast immer ausreichend Platz auch für sehr große Dienste vorhanden ist, ohne dass der Cluster neu organisiert werden muss. Wenn keine Neuorganisation des Clusters erforderlich ist, können große Workloads schnell erstellt werden.

Die meisten Benutzer benötigen keine Defragmentierung. Dienste sind normalerweise klein, daher ist es nicht schwierig, genügend Platz für sie im Cluster zu finden. Wenn eine Neuorganisation möglich ist, erfolgt diese schnell, da die meisten Dienste klein sind und schnell und gleichzeitig verschoben werden können. Wenn Sie jedoch mit großen Diensten arbeiten und diese schnell erstellen müssen, ist die Defragmentierung die richtige Strategie für Sie. Im Folgenden werden die Vor- und Nachteile der Defragmentierung erörtert. 

## <a name="defragmentation-tradeoffs"></a>Vor- und Nachteile der Defragmentierung
Die Defragmentierung kann die Auswirkungen von Ausfällen verstärken, da mehr Dienste auf ausgefallenen Knoten ausgeführt werden. Die Defragmentierung kann zudem die Kosten steigern, da Ressourcen im Cluster in Reserve gehalten werden müssen, sodass sie beim Erstellen großer Workloads zur Verfügung stehen.

Die folgende Abbildung veranschaulicht zwei Cluster, von denen der eine defragmentiert und der andere nicht defragmentiert ist. 

<center>

![Vergleich zwischen Clustern mit Lastenausgleich und defragmentierten Clustern][Image1]
</center>

Beachten Sie im Cluster mit Lastenausgleich die Anzahl von Verschiebungen, die notwendig wären, um eines der größten Dienstobjekte zu platzieren. Im defragmentierten Cluster konnte die große Arbeitslast auf dem vierten bzw. fünften Knoten platziert werden, ohne dass abgewartet werden musste, bis alle anderen Dienste verschoben wurden.

## <a name="defragmentation-pros-and-cons"></a>Vor- und Nachteile der Defragmentierung
Was sind die anderen grundlegenden Kompromisse? Es folgt eine Kurzübersicht der zu beachtenden Aspekte:

| Vorteile der Defragmentierung | Nachteile der Defragmentierung |
| --- | --- |
| Ermöglicht eine schnellere Erstellung großer Dienste |Konzentriert die Last auf weniger Knoten, wodurch sich Konflikte vermehren |
| Ermöglicht eine langsamere Datenverschiebung während der Erstellung |Ausfälle können sich auf mehr Dienste auswirken und mehr Wechsel verursachen |
| Ermöglicht eine umfassende Beschreibung der Anforderungen und Freigabe von Speicherplatz |Komplexere allgemeine Konfiguration der Ressourcenverwaltung |

Sie können defragmentierte und normale Metriken im gleichen Cluster kombinieren. Der Clusterressourcen-Manager versucht, die Defragmentierungsmetriken so weit wie möglich zu konsolidieren und die anderen Metriken zu verteilen. Die Ergebnisse des Kombinierens der Defragmentierungsstrategie und der Ausgleichsstrategie hängen von verschiedenen Faktoren ab. Hierzu zählen Folgende:
  - die Anzahl der Ausgleichsmetriken und die Anzahl der Defragmentierungsmetriken
  - der Umstand, ob Dienste beide Typen von Metriken nutzen 
  - die Metrikgewichtungen
  - die aktuelle Metriklast
  
Sie werden ein wenig experimentieren müssen, um die exakte Konfiguration zu ermitteln, die Sie benötigen. Es wird empfohlen, vor dem Aktivieren von Defragmentierungsmetriken in der Produktion Ihre Workloads gründlich zu messen. Dies gilt insbesondere, wenn in einem Dienst sowohl Defragmentierungsmetriken als auch Ausgleichsmetriken genutzt werden. 

## <a name="configuring-defragmentation-metrics"></a>Konfigurieren von Defragmentierungsmetriken
Das Konfigurieren von Defragmentierungsmetriken ist eine globale Entscheidung im Cluster, und einzelne Metriken können für die Defragmentierung ausgewählt werden. In den folgenden Konfigurationscodeausschnitten wird das Konfigurieren von Metriken für die Defragmentierung veranschaulicht. In diesem Fall wird „Metric1“ als Defragmentierungsmetrik konfiguriert, während „Metric2“ weiterhin normal ausgeglichen wird. 

ClusterManifest.xml

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

Über „ClusterConfig.json“ für eigenständige Bereitstellungen bzw. über „Template.json“ für in Azure gehostete Cluster:

```json
"fabricSettings": [
  {
    "name": "DefragmentationMetrics",
    "parameters": [
      {
          "name": "Metric1",
          "value": "true"
      },
      {
          "name": "Metric2",
          "value": "false"
      }
    ]
  }
]
```


## <a name="next-steps"></a>Nächste Schritte
- Der Clusterressourcen-Manager bietet zahlreiche Optionen zum Beschreiben des Clusters. Weitere Informationen hierzu finden Sie in diesem Artikel zum [Beschreiben eines Service Fabric-Clusters](service-fabric-cluster-resource-manager-cluster-description.md).
- Metriken bestimmen, wie der Clusterressourcen-Manager von Service Fabric den Ressourcenverbrauch und die Kapazität im Cluster verwaltet. Weitere Informationen zu Metriken und deren Konfiguration finden Sie in [diesem Artikel](service-fabric-cluster-resource-manager-metrics.md).

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png

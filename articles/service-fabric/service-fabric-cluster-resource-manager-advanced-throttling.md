---
title: Drosselung im Clusterressourcen-Manager von Service Fabric | Microsoft Docs
description: Sie lernen, die Drosselungen zu konfigurieren, die der Clusterressourcen-Manager von Service Fabric ermöglicht.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 4a44678b-a5aa-4d30-958f-dc4332ebfb63
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 4abc3e4a28b8b98070affe19b7b7ca38f904c45b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60384968"
---
# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>Drosselung im Clusterressourcen-Manager von Service Fabric
Auch wenn Sie den Clusterressourcen-Manager ordnungsgemäß konfiguriert haben, kann es für den Cluster zu Unterbrechungen kommen. Beispielsweise können Knoten und Fehlerdomänen gleichzeitig ausfallen. Was wäre, wenn dies während eines Upgrades passieren würde? Der Clusterressourcen-Manager versucht immer, alle Fehler zu beheben, und nutzt dabei die Ressourcen des Clusters, um ihn neu zu organisieren und zu reparieren. Drosselungen bieten eine Abfangfunktion, damit der Cluster Ressourcen zur Stabilisierung verwenden kann. Die Knoten kehren zurück, die Netzwerkpartitionen werden repariert und die korrigierten Komponenten werden bereitgestellt.

Um Sie in Situationen wie diesen zu unterstützen, bietet der Clusterressourcen-Manager von Service Fabric mehrere Drosselungen an. Alle diese Drosselungen haben relativ umfangreiche Auswirkungen. In der Regel sollten sie nicht ohne sorgfältige Planung und Tests geändert werden.

Wenn Sie die Drosselungen des Clusterressourcen-Managers ändern, sollten Sie sie anhand Ihrer erwarteten tatsächlichen Auslastung optimieren. Sie können zu dem Schluss kommen, dass Sie einige Drosselungen einsetzen müssen, auch wenn der Cluster dadurch in einigen Situationen länger zur Stabilisierung braucht. Zum Bestimmen der richtigen Werte für Drosselungen sind Tests erforderlich. Drosselungen müssen hoch genug, um den Cluster eine Reaktion auf Änderungen in einem angemessenen Zeitraum zu ermöglichen, sowie niedrig genug, um tatsächlich zu viel Ressourcenverbrauch zu vermeiden. 

In den meisten Fällen stellen wir fest, dass Kunden Drosselungen verwendet haben, weil sie sich ohnehin in einer Umgebung mit eingeschränkten Ressourcen befanden. Einige Beispiele sind eine begrenzte Netzwerkbandbreite für einzelne Knoten oder Datenträger, die aufgrund von Durchsatzeinschränkungen nicht viele zustandsbehaftete Replikate parallel erstellen können. Ohne Drosselungen werden diese Ressourcen u.U. von Vorgängen überlastet, sodass diese fehlschlagen oder langsam sind. In diesen Situationen haben Kunden Drosselungen verwendet und waren sich bewusst, dass sie die Zeitspanne erweitern, die der Cluster zum Erreichen eines stabilen Zustands benötigt. Kunden waren sich auch darüber im Klaren, dass sie während der Drosselung möglicherweise eine geringere allgemeine Zuverlässigkeit in Kauf nahmen.


## <a name="configuring-the-throttles"></a>Konfigurieren der Drosselungen

Service Fabric bietet zwei Mechanismen für das Drosseln der Anzahl von Replikatverschiebungen. Beim Standardmechanismus, der vor Service Fabric 5.7 vorhanden war, gilt die Drosselung als eine absolute Anzahl von zulässigen Verschiebungen. Dies funktioniert nicht für Cluster jeder Größe. Insbesondere kann für große Cluster der Standardwert zu klein sein, sodass der Lastenausgleich erheblich verlangsamt wird, selbst wenn er erforderlich ist; in kleineren Clustern gibt es keine Auswirkung. Dieser frühere Mechanismus wurde durch auf Prozentsätzen basierende Drosselung ersetzt, die sich für dynamische Cluster, in denen sich die Anzahl der Dienste und Knoten regelmäßig ändert, besser skalieren lässt.

Die Drosselungen basieren auf einem Prozentsatz der Anzahl von Replikaten in den Clustern. Auf Prozentsätzen basierende Drosselungen ermöglichen beispielsweise das Ausdrücken der Regel: „Nicht mehr als 10% der Replikate in einem Intervall von 10 Minuten verlagern“.

Die Konfigurationseinstellungen für die auf Prozentsätzen basierende Drosselung sind:

  - GlobalMovementThrottleThresholdPercentage: Maximale Anzahl von jederzeit im Cluster zulässigen Verschiebungen, ausgedrückt als Prozentwert der Gesamtanzahl von Replikaten im Cluster. Bei 0 gilt keine Begrenzung. Der Standardwert ist 0. Wenn sowohl diese Einstellung als auch „GlobalMovementThrottleThreshold“ festgelegt sind, wird der konservativere Grenzwert verwendet.
  - GlobalMovementThrottleThresholdPercentageForPlacement: Maximale Anzahl von zulässigen Verschiebungen in der Platzierungsphase, ausgedrückt als Prozentwert der Gesamtanzahl von Replikaten im Cluster. Bei 0 gilt keine Begrenzung. Der Standardwert ist 0. Wenn sowohl diese Einstellung als auch „GlobalMovementThrottleThresholdForPlacement“ festgelegt sind, wird der konservativere Grenzwert verwendet.
  - GlobalMovementThrottleThresholdPercentageForBalancing: Maximale Anzahl von zulässigen Verschiebungen in der Lastenausgleichsphase, ausgedrückt als Prozentwert der Gesamtanzahl von Replikaten im Cluster. Bei 0 gilt keine Begrenzung. Der Standardwert ist 0. Wenn sowohl diese Einstellung als auch „GlobalMovementThrottleThresholdForBalancing“ festgelegt sind, wird der konservativere Grenzwert verwendet.

Beim Festlegen des Prozentsatzes der Drosselungsrate würden Sie 5% als 0,05 angeben. Das Intervall, mit dem diese Drosselungen gesteuert werden, ist der Wert von „GlobalMovementThrottleCountingInterval“, der in Sekunden angegeben wird.


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

Über „ClusterConfig.json“ für eigenständige Bereitstellungen bzw. über „Template.json“ für in Azure gehostete Cluster:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "GlobalMovementThrottleThresholdPercentage",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForPlacement",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForBalancing",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleCountingInterval",
          "value": "600"
      }
    ]
  }
]
```

### <a name="default-count-based-throttles"></a>Standardmäßige anzahlbasierte Drosselungen
Diese Informationen werden bereitgestellt, falls Sie über ältere Cluster verfügen oder diese Konfigurationen in Clustern beibehalten, die zwischenzeitlich aktualisiert wurden. Im Allgemeinen wird empfohlen, dass diese durch die oben beschriebenen, auf Prozentsätzen basierenden Drosselungen ersetzt werden. Da die auf Prozentsätzen basierende Drosselung in der Standardeinstellung deaktiviert ist, bleiben diese Drosselungen die Standarddrosselungen für einen Cluster, bis sie deaktiviert und durch die auf Prozentsätzen basierenden Drosselungen ersetzt werden. 

  - GlobalMovementThrottleThreshold: Diese Einstellung steuert die Gesamtzahl von Bewegungen im Cluster über eine bestimmte Zeit. Der Zeitraum wird als „GlobalMovementThrottleCountingInterval“ in Sekunden angegeben. Der Standardwert für „GlobalMovementThrottleThreshold“ ist 1000, und der Standardwert für „GlobalMovementThrottleCountingInterval“ ist 600.
  - MovementPerPartitionThrottleThreshold: Diese Einstellung steuert die Gesamtzahl von Bewegungen für jede Dienstpartition über eine bestimmte Zeit. Der Zeitraum wird als „MovementPerPartitionThrottleCountingInterval“ in Sekunden angegeben. Der Standardwert für „MovementPerPartitionThrottleThreshold“ ist 50, und der Standardwert für „MovementPerPartitionThrottleCountingInterval“ ist 600.

Die Konfiguration für diese Drosselungen folgt demselben Muster wie die auf Prozentsätzen basierende Drosselung.

## <a name="next-steps"></a>Nächste Schritte
- Informationen darüber, wie der Clusterressourcen-Manager die Auslastung im Cluster verwaltet und verteilt, finden Sie im Artikel zum [Lastenausgleich](service-fabric-cluster-resource-manager-balancing.md)
- Der Clusterressourcen-Manager bietet zahlreiche Optionen zum Beschreiben des Clusters. Weitere Informationen hierzu finden Sie in diesem Artikel zum [Beschreiben eines Service Fabric-Clusters](service-fabric-cluster-resource-manager-cluster-description.md).

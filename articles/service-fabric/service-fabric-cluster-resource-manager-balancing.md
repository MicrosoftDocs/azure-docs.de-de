---
title: Ausgleichen des Azure Service Fabric-Clusters
description: Eine Einführung in den Lastenausgleich für einen Cluster mit dem Clusterressourcen-Manager von Service Fabric.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 192aca589c3b1e660667dbe8377afe7802b56f17
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146193"
---
# <a name="balancing-your-service-fabric-cluster"></a>Lastenausgleich für Service Fabric-Cluster
Der Clusterressourcen-Manager von Service Fabric unterstützt dynamische Laständerungen und reagiert auf hinzugefügte oder entfernte Knoten oder Dienste. Er korrigiert Einschränkungsverletzungen automatisch und gleicht die Last des Clusters proaktiv aus. Doch wie oft werden diese Aktionen ausgeführt, und was löst sie aus?

Es gibt drei verschiedene Kategorien von Aufgaben, die der Clusterressourcen-Manager ausführt. Sie lauten wie folgt:

1. Platzierung – In dieser Phase erfolgt die Platzierung zustandsbehafteter Replikate und zustandsloser Instanzen, die ggf. fehlen. Dies umfasst sowohl neue Dienste als auch die Behandlung von zustandsbehafteten Replikaten oder zustandslosen Instanzen, die ausgefallen sind. Hier werden das Löschen und Verwerfen von Replikaten oder Instanzen behandelt.
2. Einschränkungsüberprüfungen – In dieser Phase erfolgt eine Überprüfung auf und Korrektur bei Verstößen gegen verschiedene Platzierungseinschränkungen (Regeln) im System. Dadurch kann beispielsweise sichergestellt werden, dass Knoten ihre Kapazität nicht überschreiten und die Platzierungseinschränkungen eines Diensts eingehalten werden.
3. Ausgleich – In dieser Phase wird auf Grundlage des gewünschten Ausgleichsgrads für verschiedene Metriken geprüft, ob ein erneuter Ausgleich erforderlich ist. Falls ja, wird versucht, eine ausgeglichenere Anordnung im Cluster zu finden.

## <a name="configuring-cluster-resource-manager-timers"></a>Konfigurieren von Timern für den Clusterressourcen-Manager
Die ersten Steuerelemente im Zusammenhang mit dem Lastenausgleich sind eine Reihe von Timern. Diese Timer legen fest, wie oft der Clusterressourcen-Manager den Cluster überprüft und Korrekturmaßnahmen ergreift.

Jede dieser unterschiedlichen Korrekturarten, die der Clusterressourcen-Manager durchführen kann, wird durch einen anderen Timer gesteuert, um festzulegen, wie häufig die Vorgänge ausgeführt werden. Beim Auslösen der einzelnen Timer wird der Task geplant. Der Ressourcen-Manager führt standardmäßig folgende Schritte aus:

* Er überprüft seinen Zustand und wendet jede Zehntelsekunde Updates an (beispielsweise, um zu erfassen, dass ein Knoten inaktiv ist).
* Er legt jede Sekunde das Kennzeichen für die Platzierungsüberprüfung fest.
* Legt das Kennzeichen für die Einschränkungsüberprüfung sekündlich fest
* Er legt alle fünf Sekunden das Kennzeichen für den Ausgleich fest.

Beispiele der Konfiguration dieser Timer sind unten aufgeführt:

ClusterManifest.xml

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

Über „ClusterConfig.json“ für eigenständige Bereitstellungen bzw. über „Template.json“ für in Azure gehostete Cluster:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PLBRefreshGap",
          "value": "0.10"
      },
      {
          "name": "MinPlacementInterval",
          "value": "1.0"
      },
      {
          "name": "MinConstraintCheckInterval",
          "value": "1.0"
      },
      {
          "name": "MinLoadBalancingInterval",
          "value": "5.0"
      }
    ]
  }
]
```

Derzeit werden die Aktionen des Clusterressourcen-Managers einzeln nacheinander ausgeführt. Die Timer werden daher als „Mindestintervalle“ und die Aktionen, die erfolgen, wenn die Timer ausgelöst werden, als „Festlegen von Kennzeichen“ bezeichnet. So verarbeitet der Clusterressourcen-Manager beispielsweise zunächst ausstehende Anforderungen für die Diensterstellung und gleicht erst dann den Cluster aus. Wie anhand der angegebenen Standardintervalle zu sehen ist, ermittelt der Clusterressourcen-Manager in kurzen Abständen, ob etwas zu tun ist. Normalerweise bedeutet dies, dass die Menge von Änderungen, die während der einzelnen Schritte erfolgt, klein ist. Durch Vornehmen geringfügiger Änderungen in kurzen Abständen kann der Clusterressourcen-Manager auf Vorgänge im Cluster reagieren. Die Standardtimer ermöglichen eine gewisse Batchverarbeitung, da viele der gleichen Ereignisarten häufig gleichzeitig auftreten. 

Wenn beispielsweise Knoten ausfallen, kann dies nacheinander für gesamte Fehlerdomänen erfolgen. Alle diese Fehler werden während der nächsten Zustandsaktualisierung nach *PLBRefreshGap* erfasst. Die Korrekturen werden während der folgenden Platzierung, Einschränkungsüberprüfung und Ausführung von Ausgleichsvorgängen bestimmt. Standardmäßig geht der Clusterressourcen-Manager nicht die Clusteränderungen mehrerer Stunden durch und versucht, alle gleichzeitig zu behandeln. Dies hätte immer wieder kurze Phasen mit hohem Änderungsaufkommen zur Folge.

Der Clusterressourcen-Manager benötigt außerdem einige zusätzliche Informationen, um zu ermitteln, ob der Cluster unausgeglichen ist. Hierfür gibt es zwei weitere Konfigurationseinstellungen: *Ausgleichsschwellenwerte* (BalancingThresholds) und *Aktivitätsschwellenwerte* (ActivityThresholds).

## <a name="balancing-thresholds"></a>Ausgleichsschwellenwerte
Ein Ausgleichsschwellenwert ist das Hauptsteuerinstrument für das Auslösen eines erneuten Ausgleichs. Der Ausgleichsschwellenwert für eine Metrik ist ein _Verhältnis_. Wenn die Last für eine Metrik auf dem am stärksten ausgelasteten Knoten dividiert durch die Last auf dem am wenigsten ausgelasteten Knoten den *Ausgleichsschwellenwert* dieser Metrik überschreitet, ist der Cluster unausgeglichen. Bei der nächsten Ausführung des Clusterressourcen-Managers wird deshalb ein Ausgleich ausgelöst. Der *MinLoadBalancingInterval* -Timer bestimmt, wie oft der Clusterressourcen-Manager prüfen soll, ob ein erneuter Ausgleich erforderlich ist. Die Überprüfung bedeutet nicht, dass etwas passiert. 

Ausgleichsschwellenwerte werden als Teil der Clusterdefinition metrikbezogen definiert. Weitere Informationen zu Metriken finden Sie in [diesem Artikel](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

Über „ClusterConfig.json“ für eigenständige Bereitstellungen bzw. über „Template.json“ für in Azure gehostete Cluster:

```json
"fabricSettings": [
  {
    "name": "MetricBalancingThresholds",
    "parameters": [
      {
          "name": "MetricName1",
          "value": "2"
      },
      {
          "name": "MetricName2",
          "value": "3.5"
      }
    ]
  }
]
```

<center>

![Beispiel mit einem Ausgleichsschwellenwert][Image1]
</center>

In diesem Beispiel verbraucht jeder Dienst eine einzelne Einheit einer bestimmten Metrik. Im oberen Beispiel hat die maximale Auslastung auf einem Knoten den Wert „5“ und die minimale Auslastung den Wert „2“. Angenommen, der Ausgleichsschwellenwert für diese Metrik ist „3“. Da das Verhältnis des Clusters 5/2 (also 2,5) beträgt und damit kleiner als der angegebene Schwellenwert (3) ist, ist der Cluster ausgeglichen. Bei der Überprüfung durch den Clusterressourcen-Manager wird daher kein Ausgleich ausgelöst.

Im unteren Beispiel hat die maximale Auslastung auf einem Knoten den Wert „10“ und die minimale Auslastung den Wert „2“. Für das Verhältnis ergibt sich also der Wert „5“. Der Wert „5“ übersteigt den für die Metrik festgelegten Ausgleichsschwellenwert (3). Folglich wird beim nächsten Auslösen des Ausgleichstimers ein Ausgleich geplant. In einer derartigen Situation wird ein Teil der Auslastung meist an „Node3“ verteilt. Da der Clusterressourcen-Manager von Service Fabric keinen „gierigen“ Ansatz verfolgt, wird ein Teil der Auslastung unter Umständen auch an „Node2“ verteilt. 

<center>

![Ausgleichsschwellenwert – Beispielaktionen][Image2]
</center>

> [!NOTE]
> Für den Ausgleich werden zwei verschiedene Strategien zum Bewältigen der Last in Ihrem Cluster befolgt. Die Standardstrategie, die der Clusterressourcen-Manager befolgt, ist das Verteilen der Last auf die Knoten im Cluster. Die anderen Strategie ist die [Defragmentierung](service-fabric-cluster-resource-manager-defragmentation-metrics.md). Die Defragmentierung erfolgt während desselben Ausgleichsvorgangs. Die Strategien für Lastenausgleich und Defragmentierung können für verschiedene Metriken im Cluster verwendet werden. Ein Dienst kann sowohl Lastenausgleichs- als auch Defragmentierungsmetriken aufweisen. Bei Defragmentierungsmetriken löst der Verhältnis der Lasten im Cluster einen erneuten Ausgleich aus, wenn es _unter_ dem Ausgleichsschwellenwert liegt. 
>

Die Unterschreitung des Ausgleichsschwellenwerts ist kein explizites Ziel. Ausgleichsschwellenwerte sind lediglich ein *Auslöser*. Während des Ausgleichsvorgangs bestimmt der Clusterressourcen-Manager, ob und welche Verbesserungen erfolgen sollen. Nur weil eine Ausgleichssuche ausgelöst wird, heißt das nicht, dass etwas verschoben wird. Mitunter ist der Cluster unausgeglichenen, jedoch zu eingeschränkt, um Korrekturen vorzunehmen. Alternativ können Sie die Verbesserungen Verschiebevorgänge erfordern, die zu [kostspielig](service-fabric-cluster-resource-manager-movement-cost.md) sind.

## <a name="activity-thresholds"></a>Aktivitätsschwellenwerte
Mitunter ist die *Gesamtlast* des Clusters niedrig, obwohl Knoten relativ unausgeglichen sind. Dies kann auf einen vorübergehenden Rückgang zurückzuführen sein oder daran liegen, dass der Cluster neu ist und einem Bootstrapping-Vorgang unterzogen wird. In beiden Fällen empfiehlt es sich unter Umständen, von einem Ausgleich des Clusters abzusehen, da sich damit nur wenig erreichen lässt. Wenn der Cluster ausgeglichen wurde, würden Sie lediglich Netzwerk- und Computeressourcen beanspruchen, um Elemente zu verschieben, ohne jedoch einen großen *absoluten* Unterschied auszumachen. Diese unnötigen Verschiebevorgänge lassen sich durch so genannte Aktivitätsschwellenwerte vermeiden. Mithilfe von Aktivitätsschwellenwerten können Sie eine absolute Untergrenze für Aktivitäten angeben. Wird der Schwellenwert von keinem Knoten überschritten, wird auch bei Erreichen des Ausgleichsschwellenwerts kein Ausgleich ausgelöst.

Angenommen, wir behalten „3“ als Ausgleichsschwellenwert für diese Metrik bei. Außerdem nehmen wir an, dass es den Aktivitätsschwellenwert „1536“ gibt. Im ersten Fall ist der Cluster zwar laut Ausgleichsschwellenwert unausgeglichen, der Aktivitätsschwellenwert wird jedoch von keinem Knoten erreicht, sodass keine Maßnahmen ergriffen werden. Im unteren Beispiel überschreitet „Node1“ den Aktivitätsschwellenwert. Da sowohl der Ausgleichsschwellenwert als auch der Aktivitätsschwellenwert für die Metrik überschritten werden, wird ein Ausgleich geplant. Sehen Sie sich zur Veranschaulichung das folgende Beispieldiagramm an: 

<center>

![Beispiel mit einem Aktivitätsschwellenwert][Image3]
</center>

Aktivitätsschwellenwerte werden wie Ausgleichsschwellenwerte metrikbezogen in der Clusterdefinition definiert:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

Über „ClusterConfig.json“ für eigenständige Bereitstellungen bzw. über „Template.json“ für in Azure gehostete Cluster:

```json
"fabricSettings": [
  {
    "name": "MetricActivityThresholds",
    "parameters": [
      {
          "name": "Memory",
          "value": "1536"
      }
    ]
  }
]
```

Sowohl der Ausgleichs- als auch der Aktivitätsschwellenwert sind mit einer bestimmten Metrik verknüpft. Der Ausgleich wird nur ausgelöst, wenn sowohl der Ausgleichs- als auch der Aktivitätsschwellenwert für die gleiche Metrik überschritten werden.

> [!NOTE]
> Wenn nicht angegeben, ist der Ausgleichsschwellenwert für eine Metrik 1 und der Aktivitätsschwellenwert 0. Dies bedeutet, dass der Cluster Resource Manager versucht, diese Metrik für jede gegebene Auslastung perfekt im Ausgleich zu halten. Bei Verwendung benutzerdefinierter Metriken sollten Sie explizit Ihre eigenen Ausgleichs- und Aktivitätsschwellenwerte für Ihre Metriken definieren. 
>

## <a name="balancing-services-together"></a>Gemeinsamer Lastenausgleich von Diensten
Ob der Cluster unausgeglichen ist oder nicht, ist eine Entscheidung für den gesamten Cluster. Die Korrektur besteht jedoch darin, einzelne Dienstreplikate und -instanzen zu verschieben. Das ist einleuchtend, nicht wahr? Wenn auf einem Knoten ein hoher Arbeitsspeicherwert erreicht wird, kann dies auf mehrere Replikate oder Instanzen zurückzuführen sein. Daher kann es erforderlich sein, zustandsbehaftete Replikate oder zustandslose Instanzen zu verschieben, die die betroffene, unausgeglichene Metrik nutzen.

Gelegentlich wird jedoch ein Dienst, der selbst nicht unausgeglichen war, verschoben (denken Sie an die vorherige Diskussion lokaler und globaler Gewichtungen). Warum sollte ein Dienst verschoben werden, wenn alle seine Metrik ausgeglichen sind? Sehen wir uns ein Beispiel an:

- Angenommen, es gibt die vier Dienste Service1, Service2, Service3 und Service4. 
- Service1 meldet die Metriken Metric1 und Metric2. 
- Service2 meldet die Metriken Metric2 und Metric3. 
- Service3 meldet die Metriken Metric3 und Metric4.
- Service4 meldet die Metrik Metric99. 

Sicherlich können Sie erkennen, was hier vorliegt: eine Kette! Wir verfügen nicht wirklich über vier unabhängige Dienste, sondern vielmehr über drei Dienste, die in Bezug zueinander stehen, sowie über einen weiteren unabhängigen Dienst.

<center>

![Diagramm der gleichmäßigen Verteilung von Diensten][Image4]
</center>

Aufgrund dieser Kette ist es möglich, dass ein Ungleichgewicht bei den Metriken 1-4 bewirken kann, dass Replikate oder Instanzen, die zu den Diensten 1-3 gehören, verschoben werden. Wir wissen auch, dass ein Ungleichgewicht bei der Metrik 1, 2 oder 3 keine Verschiebungen in „Service4“ bewirkt. Das wäre sinnlos, da das Verschieben von Replikaten oder Instanzen, die zu „Service4“ gehören, keinerlei Auswirkung auf die Ausgeglichenheit der Metriken 1-3 hat.

Der Clusterressourcen-Manager ermittelt automatisch, welche Dienste verknüpft sind. Das Hinzufügen, Entfernen oder Ändern der Metriken dieser Dienste kann sich auf ihre Beziehungen auswirken. Zwischen zwei Ausgleichsvorgängen kann beispielsweise „Service2“ so aktualisiert worden sein, dass „Metric2“ entfernt wird. Dadurch wird die Kette zwischen Dienst 1 und Dienst 2 unterbrochen. Anstelle von zwei zusammenhängenden Diensten gibt es nun drei:

<center>

![Diagramm, das zeigt, wie der Clusterressourcen-Manager festlegt, welche Dienste verwandt sind][Image5]
</center>

## <a name="next-steps"></a>Nächste Schritte
* Metriken bestimmen, wie der Clusterressourcen-Manager von Service Fabric den Ressourcenverbrauch und die Kapazität im Cluster verwaltet. Weitere Informationen zu Metriken und deren Konfiguration finden Sie in [diesem Artikel](service-fabric-cluster-resource-manager-metrics.md).
* Bewegungskosten sind eine Möglichkeit, dem Clusterressourcen-Manager mitzuteilen, dass bestimmte Dienste teurer zu bewegen sind als andere. Weitere Informationen zu Bewegungskosten finden Sie in [diesem Artikel](service-fabric-cluster-resource-manager-movement-cost.md).
* Der Clusterressourcen-Manager bietet mehrere Drosselungen, die Sie konfigurieren können, um Änderungen im Cluster zu verlangsamen. Sie sind normalerweise nicht erforderlich, aber bei Bedarf finden Sie [hier](service-fabric-cluster-resource-manager-advanced-throttling.md)
* Der Clusterressourcen-Manager kann Subclustering erkennen und verarbeiten (eine Situation, die manchmal auftritt, wenn Sie Platzierungseinschränkungen und Lastenausgleich verwenden). Informationen dazu, wie sich Subclustering auf den Lastenausgleich auswirken kann und wie Sie damit umgehen können, finden Sie [hier](cluster-resource-manager-subclustering.md).

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png

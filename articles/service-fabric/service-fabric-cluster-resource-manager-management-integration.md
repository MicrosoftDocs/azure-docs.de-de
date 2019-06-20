---
title: Clusterressourcen-Manager von Service Fabric – Integration der Verwaltung | Microsoft Docs
description: Übersicht über die Integrationspunkte zwischen dem Clusterressourcen-Manager und der Service Fabric-Verwaltung.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 956cd0b8-b6e3-4436-a224-8766320e8cd7
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: c201945e94474d54b8a19918f3b55a0b40995a97
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60743512"
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Integration des Clusterressourcen-Managers in die Service Fabric-Clusterverwaltung
Der Clusterressourcen-Manager von Service Fabric führt zwar keine Upgrades in Service Fabric aus, ist jedoch daran beteiligt. Bei der Verwaltung unterstützt Sie der Clusterressourcen-Manager zunächst einmal durch das Verfolgen des gewünschten Clusterstatus und der im Cluster enthaltenen Dienste. Der Clusterressourcen-Manager sendet Integritätsberichte, wenn der Cluster nicht in die gewünschte Konfiguration versetzt werden kann. Dies ist beispielsweise der Fall, wenn nicht genügend Kapazität verfügbar ist. Der Clusterressourcen-Manager sendet dann Integritätswarnungen und Fehler, die das Problem angeben. Ein weiterer Teil der Integration hängt mit der Funktionsweise von Upgrades zusammen. Während Upgrades wird das Verhalten des Clusterressourcen-Managers geringfügig verändert.  

## <a name="health-integration"></a>Integration von Integrität
Der Clusterressourcen-Manager überwacht ständig die Regeln, die Sie für die Platzierung Ihrer Dienste definiert haben. Er überwacht außerdem die verbleibende Kapazität für alle Metriken auf den Knoten und im Cluster sowie für den gesamten Cluster. Wenn diese Regeln nicht erfüllt werden können oder nicht genügend Kapazität verfügbar ist, werden Integritätswarnungen und -fehler ausgegeben. Wenn beispielsweise ein Knoten über der Kapazitätsgrenze liegt, versucht der Clusterressourcen-Manager die Situation zu beheben, indem er Dienste verschiebt. Wenn das Problem nicht behoben werden kann, gibt er eine Integritätswarnung aus. Diese gibt an, welcher Knoten für welche Metriken über der Kapazitätsgrenze liegt.

Ein weiteres Beispiel für die Integritätswarnungen des Ressourcen-Managers sind Verstöße gegen Platzierungsbeschränkungen. Wenn Sie beispielsweise eine Platzierungseinschränkung definiert haben (z.B. `“NodeColor == Blue”`) und der Ressourcen-Manager einen Verstoß gegen diese Einschränkung erkennt, gibt er eine Integritätswarnung aus. Dies gilt für benutzerdefinierte Einschränkungen und die Standardeinschränkungen (z.B. Einschränkungen für Fehler- und Upgradedomänen).

Hier ein Beispiel eines solchen Integritätsberichts. In diesem Fall gilt der Integritätsbericht für eine der Systemdienstpartitionen. Die Integritätsmeldung zeigt an, dass die Replikate dieser Partition vorübergehend in zu wenigen Upgradedomänen abgelegt werden.

```posh
PS C:\Users\User > Get-ServiceFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_UpgradeDomain', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130766528804733380
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577821
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528854889931
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577822
                        AggregatedHealthState : Ok

                        ReplicaId             : 130837073190680024
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.PLB
                        Property              : ReplicaConstraintViolation_UpgradeDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: UpgradeDomain Details: UpgradeDomain ID -- 4, Replica on NodeName -- Node.8 Currently Upgrading -- false Distribution Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

Diese Integritätsmeldung gibt Folgendes an:

1. Alle Replikate selbst sind fehlerfrei: Jedes weist einen AggregatedHealthState auf: OK,
2. Derzeit wird gegen die Einschränkung für die Verteilung von Upgradedomänen verstoßen. Das bedeutet, dass eine bestimmte Upgradedomäne mehr Replikate für diese Partition aufweist, als sie sollte.
3. Den Knoten mit dem Replikat, der den Verstoß verursacht In diesem Fall ist es der Knoten mit dem Namen „Node.8“.
4. Ob derzeit ein Upgrade für diese Partition erfolgt („Currently Upgrading -- false“)
5. Die Verteilungsrichtlinie für diesen Dienst: „Distribution Policy -- Packing“. Dies wird von der [Platzierungsrichtlinie](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing) `RequireDomainDistribution` gesteuert. „Packing“ gibt an, dass in diesem Fall „DomainDistribution“ _nicht_ erforderlich war, damit wir wissen, dass die Platzierungsrichtlinie für diesen Dienst nicht angegeben wurde. 
6. Wann der Bericht erstellt wurde: 10.08.2015 19:13:02 Uhr

Informationen wie diese ermöglichen Warnungen, die in der Produktion ausgelöst werden, um Sie über ein Problem zu informieren, und dienen außerdem zum Anhalten fehlerhafter Upgrades. In diesem Fall möchten wir herausfinden, warum der Ressourcen-Manager die Replikate in der Upgradedomäne ablegen musste. Das Packen ist in der Regel vorübergehend, z.B. da die Knoten in den anderen Upgradedomänen ausgefallen sind.

Angenommen, der Clusterressourcen-Manager versucht, verschiedene Dienste zu platzieren, es gibt jedoch keine funktionierenden Lösungen. Wenn Dienste nicht platziert werden können, hat dies in der Regel einen der folgenden Gründe:

1. Eine vorübergehende Bedingung hat es unmöglich gemacht, diese Dienstinstanz bzw. dieses Replikat ordnungsgemäß zu platzieren.
2. Die Platzierungsanforderungen des Diensts sind unerfüllbar.

In diesen Fällen können Sie anhand von Integritätsberichten des Clusterressourcen-Managers ermitteln, warum der Dienst nicht platziert werden kann. Wir nennen diesen Prozess die „Sequenz zum Entfernen von Einschränkungen“. Im Verlauf dieses Prozesses durchläuft das System die konfigurierten Einschränkungen, die sich auf den Dienst auswirken, und zeichnet auf, was die Einschränkungen entfernen. Wenn Dienste nicht platziert werden können, lässt sich so prüfen, welche Knoten entfernt wurden und warum.

## <a name="constraint-types"></a>Einschränkungstypen
Nun sehen wir uns die verschiedenen Einschränkungen in diesen Integritätsberichten genauer an. Wenn Replikate nicht platziert werden können, werden Integritätsmeldungen zu diesen Einschränkungen angezeigt.

* **ReplicaExclusionStatic** und **ReplicaExclusionDynamic**: Diese Einschränkungen weisen darauf hin, dass eine Lösung abgelehnt wurde, da zwei Dienstobjekte aus derselben Partition auf demselben Knoten platziert werden müssten. Dies ist nicht zulässig, da sich ein Ausfall dieses Knotens dann zu stark auf diese Partition auswirken würde. Die Regel für „ReplicaExclusionStatic“ und die für „ReplicaExclusionDynamic“ sind fast identisch und die Unterschiede spielen keine Rolle. Wenn Ihnen eine Sequenz zum Entfernen von Einschränkungen angezeigt wird, die entweder die Einschränkung „ReplicaExclusionStatic“ oder „ReplicaExclusionDynamic“ enthält, geht der Clusterressourcen-Manager davon aus, dass nicht genügend Knoten vorhanden sind. Dies erfordert, dass die verbleibenden Lösungen diese ungültigen Platzierungen verwenden, die nicht zulässig sind. Die anderen Einschränkungen in der Sequenz informieren uns in der Regel darüber, warum Knoten überhaupt entfernt werden.
* **PlacementConstraint**: Wenn diese Meldung angezeigt wird, bedeutet dies, dass wir einige Knoten entfernt haben, da sie nicht den Platzierungseinschränkungen des Diensts entsprachen. Als Teil dieser Nachricht finden wir die derzeit konfigurierten Platzierungseinschränkungen. Dies ist normal, wenn Sie Platzierungsbeschränkungen definiert haben. Wenn eine Platzierungseinschränkung jedoch fälschlicherweise zu viele Knoten entfernt, würde es Ihnen an dieser Stelle auffallen.
* **NodeCapacity**: Diese Einschränkung bedeutet, dass der Clusterressourcen-Manager die Replikate nicht auf den angegebenen Knoten platzieren konnte, da dadurch die Kapazität des Knotens überschritten würde.
* **Affinity**: Diese Einschränkung gibt an, dass wir das Replikat nicht auf den betroffenen Knoten platzieren konnten, da dies zu einem Verstoß gegen die Affinitätseinschränkung führen würde. Weitere Informationen zur Affinität finden Sie in [diesem Artikel](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md).
* **FaultDomain** und **UpgradeDomain**: Diese Einschränkung entfernt Knoten, wenn das Platzieren des Replikats auf den angegebenen Knoten eine Überlastung in einer bestimmten Fehler- oder Upgradedomäne bewirken würde. Sie finden verschiedene Beispiele zu dieser Einschränkung im Thema zu [Einschränkungen und daraus resultierendes Verhalten von Fehler- und Upgradedomänen](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**: Diese Einschränkung wird normalerweise nicht angezeigt. Sie bewirkt, dass Knoten aus der Lösung entfernt werden, da sie standardmäßig auf Optimierung ausgelegt ist. Während Upgrades ist auch die Einschränkung für den bevorzugten Standort vorhanden. Während eines Upgrades dient sie dazu, Dienste an die Stelle zurück zu verschieben, an der sie sich bei Beginn des Upgrades befanden.

## <a name="blocklisting-nodes"></a>Blockieren von Knoten
Eine weitere Integritätsmeldung des Clusterressourcen-Managers erfolgt, wenn Knoten auf eine Blockliste gesetzt werden. Sie können sich das Blockieren als vorübergehende Einschränkung vorstellen, die automatisch für Sie angewendet wird. Knoten werden blockiert, wenn beim Starten von Instanzen dieses Diensttyps wiederholt Fehler auftreten. Knoten werden basierend auf dem Diensttyp blockiert. Ein Knoten kann für einen Diensttyp blockiert sein, für einen anderen jedoch nicht. 

Blockierungen erfolgen häufig während der Entwicklung: Ein Fehler bewirkt, dass der Diensthost beim Starten abstürzt. Service Fabric versucht einige Male, den Diensthost zu erstellen, und der Fehler tritt weiterhin auf. Nach einigen Versuchen wird der Knoten blockiert, und der Clusterressourcen-Manager versucht, den Dienst an anderer Stelle zu erstellen. Wenn dieser Fehler auf mehreren Knoten häufiger auftritt, ist es möglich, dass alle gültigen Knoten im Cluster blockiert werden. Durch Blockieren können auch so viele Knoten entfernt werden, dass nicht genügend den Dienst erfolgreich starten können, um die gewünschte Skalierung zu erzielen. In der Regel werden weitere Fehler oder Warnungen aus dem Clusterressourcen-Manager angezeigt, die angeben, dass der Dienst unter der gewünschten Replikat- oder Instanzenanzahl liegt, sowie Integritätsmeldungen, die den Fehler angeben, der überhaupt zu der Blockierung führt.

Das Blockieren ist keine permanente Bedingung. Nach einigen Minuten wird der Knoten von der Blockliste entfernt, und Service Fabric kann die Dienste auf diesem Knoten erneut aktivieren. Wenn Dienste weiterhin fehlschlagen, wird der Knoten für diesen Dienst erneut blockiert. 

### <a name="constraint-priorities"></a>Einschränkungsprioritäten

> [!WARNING]
> Das Ändern von Einschränkungsprioritäten wird nicht empfohlen und kann erhebliche negative Auswirkungen auf den Cluster haben. Die folgenden Informationen dienen als Referenz zu den standardmäßigen Einschränkungsprioritäten und deren Verhalten. 
>

Bei all diesen Einschränkungen sind Sie vielleicht zu folgendem Schluss gekommen: „Fehlerdomäneneinschränkungen sind in meinem System das Wichtigste. Ich bin bereit, andere Einschränkungen zu verletzen, wenn dies sicherstellt, dass die Fehlerdomäneneinschränkung nicht verletzt wird.“

Einschränkungen können mit verschiedenen Prioritätsstufen konfiguriert werden. Dies sind:

   - „hart“ (0)
   - „weich“ (1)
   - „Optimierung“ (2)
   - „deaktiviert“ (-1). 
   
Die meisten Einschränkungen werden standardmäßig als harte Einschränkungen konfiguriert.

Das Ändern der Priorität von Einschränkungen ist ungewöhnlich. Es gab Zeiten, in denen Einschränkungsprioritäten geändert werden mussten, meist, um andere Fehler oder Verhalten zu umgeben, die die Umgebung beeinträchtigt haben. Die Flexibilität der Infrastruktur mit Einschränkungsprioritäten funktioniert in der Regel sehr gut, ist jedoch nicht häufig erforderlich. In den meisten Fällen gelten die Standardprioritäten. 

Die Prioritätsstufen bedeuten weder, dass eine bestimmte Einschränkung verletzt _wird_, noch dass sie immer erfüllt wird. Einschränkungsprioritäten legen eine Reihenfolge fest, in der Einschränkungen erzwungen werden. Prioritäten definieren die Vor- und Nachteile, wenn es nicht möglich ist, alle Einschränkungen zu erfüllen. In der Regel können alle Einschränkungen erfüllt werden, es sei denn, in der Umgebung finden weitere Vorgänge statt. Einige Beispiele für Szenarien, die zu Einschränkungsverletzungen führen, sind in Konflikt stehende Einschränkungen oder zahlreiche gleichzeitige Ausfälle.

In komplexeren Situationen können die Einschränkungsprioritäten geändert werden. Angenommen, Sie möchten sicherstellen, dass die Affinität immer verletzt wird, wenn dies zum Beheben von Problemen mit der Knotenkapazität erforderlich ist. Um dies zu erreichen, könnten Sie die Priorität für die Affinitätseinschränkung auf „weich“ (1) einstellen und die Kapazitätseinschränkung auf „hart“ (0) festgelegt lassen.

Die Standardprioritätswerte für die verschiedenen Einschränkungen sind in der folgenden Konfiguration aufgeführt:

ClusterManifest.xml

```xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PlacementConstraintPriority" Value="0" />
            <Parameter Name="CapacityConstraintPriority" Value="0" />
            <Parameter Name="AffinityConstraintPriority" Value="0" />
            <Parameter Name="FaultDomainConstraintPriority" Value="0" />
            <Parameter Name="UpgradeDomainConstraintPriority" Value="1" />
            <Parameter Name="PreferredLocationConstraintPriority" Value="2" />
        </Section>
```

Über „ClusterConfig.json“ für eigenständige Bereitstellungen bzw. über „Template.json“ für in Azure gehostete Cluster:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PlacementConstraintPriority",
          "value": "0"
      },
      {
          "name": "CapacityConstraintPriority",
          "value": "0"
      },
      {
          "name": "AffinityConstraintPriority",
          "value": "0"
      },
      {
          "name": "FaultDomainConstraintPriority",
          "value": "0"
      },
      {
          "name": "UpgradeDomainConstraintPriority",
          "value": "1"
      },
      {
          "name": "PreferredLocationConstraintPriority",
          "value": "2"
      }
    ]
  }
]
```

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Einschränkungen für Fehlerdomänen und Upgradedomänen
Der Clusterressourcen-Manager möchte, dass Dienste auf Fehler- und Upgradedomänen verteilt bleiben. Er modelliert diese Anforderung als Einschränkung innerhalb der Engine des Clusterressourcen-Managers. Weitere Informationen zu ihrer Verwendung und ihrem bestimmten Verhalten finden Sie im Artikel zur [Clusterkonfiguration](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior).

Der Clusterressourcen-Manager muss möglicherweise einige Replikate in einer Upgradedomäne platzieren, um Upgrades, Fehler oder andere Verstöße gegen Einschränkungen zu verarbeiten. Das Packen in Fehler- oder Upgradedomänen erfolgt normalerweise nur, wenn mehrere Fehler oder andere Änderungen im System eine richtige Platzierung verhindern. Wenn Sie das Packen auch in diesen Situationen verhindern möchten, können Sie die `RequireDomainDistribution`-[Platzierungsrichtlinie](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing) verwenden. Beachten Sie, dass dies möglicherweise Nebenwirkungen auf die Dienstverfügbarkeit und -zuverlässigkeit hat. Prüfen Sie es also sorgfältig.

Wenn die Umgebung ordnungsgemäß konfiguriert ist, werden alle Einschränkungen selbst während der Upgrades vollständig eingehalten. Der wichtigste Punkt ist, dass der Clusterressourcen-Manager die Einschränkungen überwacht. Wenn er einen Verstoß erkennt, wird dieser sofort gemeldet und versucht, das Problem zu beheben.

## <a name="the-preferred-location-constraint"></a>Die Einschränkung für den bevorzugten Standort
Die Einschränkung für „PreferredLocation“ ist ein wenig anders, da sie zwei Verwendungszwecke hat. Zum einen wird diese Einschränkung während Anwendungsupgrades verwendet. Der Clusterressourcen-Manager verwaltet diese Einschränkung während Upgrades automatisch. Damit wird sichergestellt, dass Replikate nach abgeschlossenen Upgrades an ihre ursprünglichen Position zurückverschoben werden. Des Weiteren wird die Einschränkung „PreferredLocation“ für die [`PreferredPrimaryDomain`Platzierungsrichtlinie](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) verwendet. Beide Verwendungen sind Optimierungen, und daher ist die Einschränkung „PreferredLocation“ als einzige Einschränkung standardmäßig auf „Optimierung“ festgelegt.

## <a name="upgrades"></a>Upgrades
Der Clusterressourcen-Manager ist auch bei Anwendungs- und Clusterupgrades hilfreich, bei denen er zwei Aufgaben erfüllt:

* Sicherstellen, dass die Regeln des Clusters nicht gefährdet sind
* Unterstützen einer reibungslosen Durchführung des Upgrades

### <a name="keep-enforcing-the-rules"></a>Fortsetzen des Erzwingens der Regeln
Ein überaus wichtiger Aspekt ist, dass die Regeln – d.h. die strikten Einschränkungen z.B. hinsichtlich der Platzierung und Kapazitäten– auch im Verlauf von Upgrades weiter erzwungen werden. Platzierungsbeschränkungen stellen sicher, dass Ihre Arbeitsauslastungen auch während der Upgrades nur an erlaubter Stelle ausgeführt werden. Wenn Dienste stark eingeschränkt sind, können Upgrades länger dauern. Wenn der Dienst oder der Knoten, auf dem er ausgeführt wird, nach einem Update heruntergefahren wird, gibt es möglicherweise einige Optionen, wohin er umgeleitet werden kann.

### <a name="smart-replacements"></a>Intelligenter Ersatz
Zu Beginn eines Upgrades erfasst der Ressourcen-Manager eine Momentaufnahme der aktuellen Clusteranordnung. Nach Abschluss einer Upgradedomäne wird versucht, die ursprüngliche Anordnung der die Dienste in dieser Upgradedomäne wiederherzustellen. So erfolgen für einen Dienst höchstens zwei Übergänge während des Upgrades. Einer zum Verschieben aus dem betreffenden Knoten und ein weiterer zum Zurückverschieben. Durch die Wiederherstellung des Clusters oder Dienstes in den Zustand vor dem Upgrade wird außerdem sichergestellt, dass das Layout des Clusters nicht durch das Upgrade beeinträchtigt wird. 

### <a name="reduced-churn"></a>Weniger Änderungen
Wichtig ist der Hinweis, dass der Clusterressourcen-Manager während des Upgrades den Lastenausgleich deaktiviert. Durch das Verhindern des Lastenausgleichs werden unnötige Reaktionen auf das Upgrade selbst verhindert, beispielsweise das Verschieben von Diensten auf Knoten, die für das Upgrade geleert wurden. Wenn es sich beim betreffenden Upgrade um ein Clusterupgrade handelt, findet für den gesamten Cluster während des Upgrades kein Lastenausgleich statt. Einschränkungsüberprüfungen bleiben aktiv, nur die Verschiebung anhand des proaktiven Ausgleichs von Metriken wird deaktiviert.

### <a name="buffered-capacity--upgrade"></a>Gepufferte Kapazität und Upgrade
Im Allgemeinen möchten Sie, dass das Upgrade auch dann abgeschlossen wird, wenn der Cluster eingeschränkt oder beinahe voll belegt ist. Das Verwalten der Clusterkapazität ist während Upgrades noch wichtiger als sonst. Je nach Anzahl der Upgradedomänen müssen zwischen 5 und 20 Prozent der Kapazität migriert werden, während das Upgrade den Cluster durchläuft. Diese Arbeit muss irgendwo aufgefangen werden. Hier ist das Konzept der [gepufferten Kapazitäten](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity) hilfreich. Gepufferte Kapazität wird während des normalen Betriebs berücksichtigt. Der Clusterressourcen-Manager kann Knoten während des Upgrades ggf. bis zu deren Gesamtkapazität (Nutzung des Puffers) füllen.

## <a name="next-steps"></a>Nächste Schritte
* Starten Sie mit einer [Einführung in den Clusterressourcen-Manager von Service Fabric](service-fabric-cluster-resource-manager-introduction.md)

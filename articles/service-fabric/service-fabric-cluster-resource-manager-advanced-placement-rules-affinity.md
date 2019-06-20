---
title: Clusterressourcen-Manager von Service Fabric – Affinität | Microsoft Docs
description: Übersicht über die Konfiguration der Affinität für Service Fabric-Dienste
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 678073e1-d08d-46c4-a811-826e70aba6c4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 29377492b90f366227ca7bedf85890b7734ea25f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62118414"
---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Konfigurieren und Verwenden der Dienstaffinität in Service Fabric
Affinität ist ein Steuerelement, das hauptsächlich bereitgestellt wird, um den Übergang von größeren monolithischen Anwendungen in die Welt der Cloud und Microservices zu vereinfachen. Sie wird auch zur Verbesserung der Leistung von Diensten verwendet, dies ist jedoch unter Umständen mit Nebenwirkungen verbunden.

Angenommen, Sie überführen eine größere App (oder eine App, die nicht für Microservices entwickelt wurde) in Service Fabric oder in eine andere verteilte Umgebung. Dieser Übergang wird häufig durchgeführt. Sie verschieben zuerst die gesamte App in die Umgebung, paketieren sie und stellen sicher, dass sie reibungslos ausgeführt wird. Anschließend unterteilen Sie sie in verschiedene kleinere Dienste, die alle miteinander kommunizieren.

Irgendwann stellen Sie möglicherweise fest, dass bei der Anwendung einige Probleme auftreten. Die Probleme lassen sich in der Regel einer der folgenden Kategorien zuordnen:

1. Eine Komponente X in der monolithischen App verfügt über eine nicht dokumentierte Abhängigkeit von Komponente Y, und diese Komponenten wurden soeben in separate Dienste umgewandelt. Da diese Dienste jetzt auf unterschiedlichen Knoten im Cluster ausgeführt werden, funktionieren sie nicht mehr.
2. Diese Komponenten kommunizieren über (lokale benannte Pipes | freigegebenen Arbeitsspeicher | Dateien auf dem Datenträger) und müssen aus Leistungsgründen umgehend in eine freigegebene lokale Ressource schreiben können. Diese harte Abhängigkeit wird (möglicherweise) später entfernt.
3. Alles ist in Ordnung, doch es stellt sich heraus, dass diese beiden Komponenten sehr kommunikativ bzw. leistungsabhängig sind. Beim Verschieben der Komponenten in unterschiedliche Dienste hat die Leistung der Anwendung rapide abgenommen, oder die Latenz ist gestiegen. Folglich wird die erwartete Leistung der Anwendung nicht mehr erreicht.

Nun muss eine Lösung gefunden werden, bei der der Umgestaltungsaufwand nicht umsonst war und nicht erneut auf die monolithische Anwendung zurückgegriffen wird. Die letzte Bedingung ist möglicherweise sogar als einfache Optimierung wünschenswert. Bis die Komponenten so neu entworfen werden können, dass sie selbst als Dienste ausgeführt werden (oder bis die erwartete Leistung auf andere Weise erreicht wird), brauchen wir einen gewissen Ortssinn.

Vorgehensweise Wir könnten es mit dem Aktivieren von Affinität versuchen.

## <a name="how-to-configure-affinity"></a>Konfigurieren von Affinität
Um für Affinität zu sorgen, definieren Sie eine Affinitätsbeziehung zwischen zwei Diensten. Sie können sich das so vorstellen, dass ein Dienst auf einen anderen „zeigt“ und angibt, dass dieser Dienst nur dort ausgeführt werden kann, wo auch der andere ausgeführt wird. Mitunter wird Affinität als Beziehung über- und untergeordneter Dienste beschrieben (wobei der untergeordnete auf den übergeordneten Dienst zeigt). Mithilfe von Affinität wird sichergestellt, dass die Replikate oder Instanzen eines Diensts auf denselben Knoten platziert werden wie die Replikate oder Instanzen eines anderen Diensts.

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> Ein untergeordneter Dienst kann nur an einer einzelnen Affinitätsbeziehung beteiligt sein. Wenn der untergeordnete Dienst eine Affinitätsbeziehung mit zwei übergeordneten Diensten haben soll, haben Sie folgende Möglichkeiten:
> - Kehren Sie die Beziehungen um, sodass die beiden übergeordneten Dienste auf den untergeordneten Dienst verweisen. Oder:
> - Konfigurieren Sie einen der übergeordneten Dienste per Konvention als Hub, und lassen Sie alle Dienste auf diesen Dienst verweisen. 
>
> Das dadurch erzielte Platzierungsverhalten im Cluster sollte identisch sein.
>

## <a name="different-affinity-options"></a>Verschiedene Affinitätsoptionen
Affinität wird mithilfe eines von mehreren Korrelationschemen dargestellt und weist zwei verschiedene Modi auf. Der gängigste Affinitätsmodus ist, was wir als „NonAlignedAffinity“ bezeichnen. Bei NonAlignedAffinity werden die Replikate oder Instanzen der anderen Dienste auf denselben Knoten platziert. Der andere Modus heißt „AlignedAffinity“. AlignedAffinity wird ausschließlich für zustandsbehaftete Dienste verwendet. Durch die Konfiguration von zwei zustandsbehafteten Diensten mit AlignedAffinity wird sichergestellt, dass die primären Replikate dieser Dienste auf denselben Knoten platziert werden wie die des anderen Diensts. Außerdem wird jedes Paar aus sekundären Replikaten für diese Dienste auf denselben Knoten platziert. Es ist auch möglich, (jedoch nicht so üblich) „NonAlignedAffinity“ für zustandsbehaftete Dienste zu konfigurieren. Bei „NonAlignedAffinity“ werden die verschiedenen Replikate der beiden zustandsbehafteten Dienste zwar auf den gleichen Knoten ausgeführt, die primären Replikate können sich jedoch auf unterschiedlichen Knoten befinden.

<center>

![Affinitätsmodi und ihre Auswirkungen][Image1]
</center>

### <a name="best-effort-desired-state"></a>Anstreben des gewünschten Zustands
Mit einer Affinitätsbeziehung wird ein bestimmter Zustand angestrebt. Sie bietet nicht die gleichen Kollokations- oder Zuverlässigkeitsgarantien wie die Ausführung im gleichen ausführbaren Prozess. Die Dienste in einer Affinitätsbeziehung sind unterschiedliche Entitäten, die unabhängig voneinander ausfallen und verschoben werden können. Eine Affinitätsbeziehung kann auch unterbrochen werden, solche Unterbrechungen sind jedoch nur vorübergehend. So können beispielsweise Kapazitätseinschränkungen dazu führen, dass auf einem bestimmten Knoten nur einige Dienstobjekte der Affinitätsbeziehung Platz finden. In diesen Fällen besteht zwar eine Affinitätsbeziehung, aufgrund der anderen Einschränkungen kann sie jedoch nicht erzwungen werden. Nach Möglichkeit wird der Verstoß später automatisch korrigiert.

### <a name="chains-vs-stars"></a>Ketten im Vergleich zu Sternen
Heute ist der Clusterressourcen-Manager nicht dazu in der Lage, Affinitätsbeziehungsketten zu modellieren. Dies bedeutet, dass ein Dienst, der ein untergeordneter Dienst einer Affinitätsbeziehung ist, kein übergeordneter Dienst in einer anderen Affinitätsbeziehung sein kann. Wenn Sie diesen Typ von Beziehung modellieren möchten, müssen Sie sie als Stern und nicht als Kette modellieren. Um von einer Kette zu einem Stern zu wechseln, würde das übergeordnete Element des ersten untergeordneten Elements als übergeordnetes Element für das unterste untergeordnete Element verwendet. Abhängig von der Anordnung Ihrer Dienste müssen Sie dies möglicherweise mehrmals durchführen. Wenn es keinen natürlichen übergeordneten Dienst gibt, müssen Sie möglicherweise einen erstellen, der als Platzhalter dient. Je nach Ihren Anforderungen sollten Sie sich auch mit [Anwendungsgruppen](service-fabric-cluster-resource-manager-application-groups.md) befassen.

<center>

![Ketten im Vergleich zu Sternen im Kontext von Affinitätsbeziehungen][Image2]
</center>

Eine weitere Eigenschaft derzeitiger Affinitätsbeziehungen ist, dass sie standardmäßig gerichtet sind. Mit der Affinitätsregel wird also lediglich erzwungen, dass das untergeordnete Element dort platziert wird, wo sich auch das übergeordnete Element befindet. Sie sorgt nicht dafür, dass das übergeordnete Element dort platziert wird, wo sich das untergeordnete Element befindet. Wenn es also eine Affinitätsverletzung gibt und es zum Korrigieren der Verletzung aus irgendeinem Grund nicht möglich ist, den untergeordneten Knoten in den übergeordneten Knoten zu verschieben, dann wird der übergeordnete Knoten nicht in den untergeordneten Knoten verschoben (selbst dann nicht, wenn die Verschiebung des übergeordneten Knotens in den untergeordneten Knoten die Verletzung korrigiert hätte). Das Festlegen der Konfigurationseinstellung [MoveParentToFixAffinityViolation](service-fabric-cluster-fabric-settings.md) auf TRUE würde die Direktionalität entfernen. Ein weiterer wichtiger Punkt: Die Affinitätsbeziehung kann nicht perfekt oder umgehend erzwungen werden, da verschiedene Dienste über unterschiedliche Lebenszyklen verfügen und unabhängig voneinander ausfallen und verschoben werden können. Nehmen wir beispielsweise an, dass für das übergeordnete Element aufgrund eines Absturzes überraschend ein Failover auf einen anderen Knoten ausgeführt wird. Der Cluster Resource Manager und der Failover-Manager behandeln zunächst das Failover, da Bereitschaft, Konsistenz und Verfügbarkeit der Dienste oberste Priorität haben. Nach Abschluss des Failovers ist die Affinitätsbeziehung unterbrochen. Der Cluster Resource Manager geht jedoch davon aus, dass alles in Ordnung ist, bis er bemerkt, dass sich das untergeordnete Element nicht beim übergeordneten Element befindet. Die entsprechenden Überprüfungen werden in regelmäßigen Abständen durchgeführt. Weitere Informationen zur Auswertung von Einschränkungen durch den Cluster Resource Manager finden Sie in [diesem Artikel](service-fabric-cluster-resource-manager-management-integration.md#constraint-types). Informationen zum Konfigurieren des Auswertungsintervalls für diese Einschränkungen finden Sie [hier](service-fabric-cluster-resource-manager-balancing.md).   


### <a name="partitioning-support"></a>Unterstützung der Partitionierung
Der letzte wichtige Affinitätsaspekt ist, dass Affinitätsbeziehungen nicht unterstützt werden, wenn das übergeordnete Element partitioniert ist. Partitionierte übergeordnete Dienste werden möglicherweise später einmal unterstützt, momentan ist dies jedoch nicht zulässig.

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zum Konfigurieren von Diensten finden Sie unter [Konfigurieren von Einstellungen des Clusterressourcen-Managers für Service Fabric-Dienste](service-fabric-cluster-resource-manager-configure-services.md).
- Wenn Sie Dienste auf eine kleine Gruppe von Computern beschränken oder die Last von Diensten aggregieren möchten, verwenden Sie [Anwendungsgruppen](service-fabric-cluster-resource-manager-application-groups.md).

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png
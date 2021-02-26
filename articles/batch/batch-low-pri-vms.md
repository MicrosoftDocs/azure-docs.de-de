---
title: Ausführen von Workloads auf kostengünstigen VMs mit niedriger Priorität
description: Erfahren Sie, wie Sie VMs mit niedriger Priorität bereitstellen, um die Kosten von Azure Batch-Workloads zu verringern.
author: mscurrell
ms.topic: how-to
ms.date: 02/02/2021
ms.custom: seodec18
ms.openlocfilehash: 9214ef83ec9b8bef4fb7bc7489aa0ab388f67c0d
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99507271"
---
# <a name="use-low-priority-vms-with-batch"></a>Verwenden von VMs mit niedriger Priorität mit Batch

Azure Batch bietet virtuelle Computer (VMs) mit niedriger Priorität, um die Kosten für Batch-Workloads zu verringern. Durch VMs mit niedriger Priorität werden neue Typen von Batchworkloads möglich, indem eine große Menge an Computeleistung zu sehr niedrigen Kosten bereitgestellt wird.

VMs mit niedriger Priorität nutzen überschüssige Kapazitäten in Azure. Wenn Sie VMs mit niedriger Priorität in Pools angeben, kann Azure Batch diesen Überschuss nutzen, sobald er zur Verfügung steht.

Die Verwendung von VMs mit niedriger Priorität hat jedoch auch Nachteile: Möglicherweise stehen diese VMs für eine Zuweisung nicht immer zur Verfügung, und sie können jederzeit vorzeitig entfernt werden, je nach verfügbarer Kapazität. Aus diesem Grund eignen sich VMs mit niedriger Priorität bestens für Batch und asynchrone Verarbeitungsworkloads, bei denen der Zeitpunkt des Auftragsabschlusses flexibel ist und die Arbeit über viele VMs verteilt wird.

VMs mit niedriger Priorität werden im Vergleich zu dedizierten VMs zu einem deutlich günstigeren Preis angeboten. Ausführliche Preisinformationen finden Sie unter [Preise für Batch](https://azure.microsoft.com/pricing/details/batch/).

> [!NOTE]
> [Spot-VMs](https://azure.microsoft.com/pricing/spot/) sind jetzt für [VMs mit einer einfachen Instanz](../virtual-machines/spot-vms.md) und [VM-Skalierungsgruppen](../virtual-machine-scale-sets/use-spot.md) verfügbar. Spot-VMs sind eine Weiterentwicklung von VMs mit niedriger Priorität, unterscheiden sich aber dadurch, dass die Preise variieren können und bei der Zuweisung von Spot-VMs ein optionaler Maximalpreis festgelegt werden kann.
>
> Azure Batch-Pools unterstützen in wenigen Monaten Spot-VMs und sind dann mit neuen Versionen der [Batch-APIs und -Tools](./batch-apis-tools.md) allgemein verfügbar. Sobald die Unterstützt für Spot-VMs verfügbar ist, werden VMs mit niedriger Priorität für mindestens 12 Monate weiterhin unterstützt, indem die aktuellen API- und Toolversionen verwendet werden, damit ausreichend Zeit für die Migration zu Spot-VMs bleibt.
>
> Spot-VMs werden für [Clouddienstkonfigurations](/rest/api/batchservice/pool/add#cloudserviceconfiguration)-Pools nicht unterstützt. Um Spot-VMs verwenden zu können, müssen Clouddienstpools zu [VM-Konfigurations](/rest/api/batchservice/pool/add#virtualmachineconfiguration)pools migriert werden.

## <a name="batch-support-for-low-priority-vms"></a>Unterstützung von VMs mit niedriger Priorität in Batch

Azure Batch stellt verschiedene Funktionen bereit, die die vorteilhafte Nutzung von VMs mit niedriger Priorität erleichtern:

- Batchpools können sowohl dedizierte VMs als auch VMs mit niedriger Priorität enthalten. Die Anzahl der einzelnen VM-Typen kann beim Erstellen eines Pools angegeben und für einen bestehenden Pool mithilfe der ausdrücklichen Größenänderung oder der automatischen Skalierung jederzeit geändert werden. Die Auftrags- und Aufgabenübermittlung kann unabhängig von den VM-Typen im Pool unverändert bleiben. Sie können auch einen Pool zur ausschließlichen Verwendung von VMs mit niedriger Priorität konfigurieren, um Aufträge möglichst kostengünstig ausführen, jedoch die Anzahl der dedizierten VMs erhöhen zu können, wenn die Kapazität unter einen Mindestschwellenwert fällt. Dadurch werden Aufträge weiterhin ausgeführt.
- In Batch-Pools wird automatisch die Zielanzahl von VMs mit niedriger Priorität angestrebt. Wenn VMs vorzeitig entfernt werden oder nicht verfügbar sind, versucht Batch, die verlorene Kapazität auszugleichen und das Ziel wieder zu erreichen.
- Wenn Aufgaben unterbrochen werden, erkennt Batch dies und reiht die Aufgaben zur erneuten Ausführung automatisch wieder in eine Warteschlange.
- VMs mit niedriger Priorität verfügen über ein separates vCPU-Kontingent, das von dem dedizierter VMs abweicht. Das Kontingent für VMs mit niedriger Priorität ist höher als bei dedizierten VMs, da VMs mit niedriger Priorität weniger kosten. Weitere Informationen finden Sie im Artikel [Batch-Dienst – Kontingente und Limits](batch-quota-limit.md#resource-quotas).

> [!NOTE]
> VMs mit niedriger Priorität werden derzeit für Batch-Konten, die im Modus [Benutzerabonnement](accounts.md) erstellt wurden, nicht unterstützt.

## <a name="considerations-and-use-cases"></a>Überlegungen und Anwendungsfälle

Viele Batchworkloads eignen sich gut für VMs mit niedriger Priorität. Ziehen Sie Batchworkloads in Betracht, wenn Aufträge in viele parallele Aufgaben aufgeteilt sind, oder wenn Sie über viele Aufträge verfügen, die aufskaliert und auf viele VMs verteilt sind.

Einige Beispiele für Anwendungsfälle mit Batchverarbeitung, die gut für VMs mit niedriger Priorität geeignet sind:

- **Entwicklung und Tests**: Insbesondere bei der Entwicklung von umfangreichen Lösungen lassen sich beträchtliche Einsparungen realisieren. Alle Arten von Tests können profitieren, aber umfangreiche Auslastungstests und Regressionstests sind besonders gut geeignete Fälle.
- **Ergänzen der bedarfsgesteuerten Kapazität**: VMs mit niedriger Priorität können dazu verwendet werden, reguläre dedizierte VMs zu ergänzen. Wenn sie verfügbar sind, können Aufträge skaliert werden, sodass diese schneller und günstiger abgeschlossen werden. Wenn sie nicht verfügbar sind, ist weiterhin die Baseline der dedizierten VMs verfügbar.
- **Flexible Auftragsausführungszeit**: Wenn die Zeit, in der Aufträge abgeschlossen werden müssen, flexibel ist, können potenzielle Kapazitätseinbußen toleriert werden. Durch Hinzufügen von VMs mit niedriger Priorität lassen sich Aufträge jedoch häufig schneller und kostengünstiger ausführen.

Batchpools können auf verschiedene Weisen zur Verwendung von VMs mit niedriger Priorität konfiguriert werden:

- Ein Pool kann nur VMs mit niedriger Priorität verwenden. In diesem Fall stellt Batch vorzeitig entfernte Kapazitäten bei Verfügbarkeit wieder her. Diese Konfiguration stellt die günstigste Möglichkeit zum Ausführen von Aufträgen dar.
- VMs mit niedriger Priorität können in Verbindung mit einer festen Grundausstattung von dedizierten VMs verwendet werden. Die feste Anzahl von dedizierten VMs stellt sicher, dass immer eine gewisse Kapazität zum Fortsetzen der Verarbeitung eines Auftrags verbleibt.
- Ein Pool kann eine dynamische Kombination aus dedizierten VMs und VMs mit niedriger Priorität verwenden, sodass ausschließlich die kostengünstigeren VMs mit niedriger Priorität verwendet werden, wenn sie verfügbar sind, bei Bedarf jedoch die dedizierten VMs zum vollen Preis zentral hochskaliert werden. Durch eine solche Konfiguration bleibt eine Mindestmenge an Kapazität für die fortlaufende Verarbeitung der Aufträge verfügbar.

Beachten Sie Folgendes, wenn Sie die Verwendung von VMs mit niedriger Priorität planen:

- Zur Maximierung der Verwendung von überschüssigen Kapazitäten in Azure können geeignete Aufträge aufskaliert werden.
- Gelegentlich sind die VMs möglicherweise nicht verfügbar oder werden vorzeitig entfernt. Dies führt zu einer verringerten Kapazität für Aufträge und damit möglicherweise zu Unterbrechungen und einem erneutem Ausführen von Aufgaben.
- Aufgaben mit kürzeren Ausführungszeiten funktionieren im Allgemeinen am besten mit VMs mit niedriger Priorität. Aufträge mit längeren Aufgaben werden möglicherweise durch Unterbrechungen stärker beeinträchtigt. Wenn Aufgaben mit langer Ausführungsdauer Prüfpunkte implementieren, um den Fortschritt im Laufe der Ausführung zu speichern, werden diese Auswirkungen möglicherweise reduziert. 
- MPI-Aufträge mit langer Ausführungsdauer, die mehrere VMs nutzen, sind für die Verwendung von VMs mit niedriger Priorität nicht gut geeignet, da eine vorzeitig entfernte VM dazu führen kann, dass der gesamte Auftrag erneut ausgeführt werden muss.
- Knoten mit niedriger Priorität können als nicht verwendbar gekennzeichnet werden, wenn [NSG-Regeln (Netzwerksicherheitsgruppen)](batch-virtual-network.md#network-security-groups-specifying-subnet-level-rules) nicht ordnungsgemäß konfiguriert sind.

## <a name="create-and-manage-pools-with-low-priority-vms"></a>Erstellen und Verwalten von Pools mit VMs mit niedriger Priorität

Ein Batchpool kann sowohl dedizierte VMs als auch VMs mit niedriger Priorität (auch als Serverknoten bezeichnet) enthalten. Sie können die Zielanzahl der Serverknoten für dedizierte VMs und VMs mit niedriger Priorität festlegen. Die Zielanzahl der Knoten gibt die Anzahl von VMs an, die im Pool vorhanden sein sollen.

So erstellen Sie beispielsweise einen Pool mit Azure-VMs (in diesem Fall Linux-VMs) mit einem Ziel von 5 dedizierten VMs und 20 VMs mit niedriger Priorität:

```csharp
ImageReference imageRef = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "16.04-LTS",
    version: "latest");

// Create the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration("batch.node.ubuntu 16.04", imageRef);

pool = batchClient.PoolOperations.CreatePool(
    poolId: "vmpool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

Sie können die aktuelle Anzahl der Knoten für dedizierte VMs und VMs mit niedriger Priorität abrufen:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

Poolknoten weisen eine Eigenschaft auf, die angibt, ob es sich bei einem Knoten um eine dedizierte VM oder eine VM mit niedriger Priorität handelt:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

VMs können gelegentlich vorzeitig entfernt werden. Wenn dies geschieht, werden Tasks, die auf den VMs mit einem vorzeitig entfernten Knoten ausgeführt wurden, noch mal in die Warteschlange gestellt und nochmal ausgeführt.

Für Konfigurationspools für virtuelle Computer führt Azure Batch auch folgende Aktionen aus:

- Der Status der vorzeitig entfernten VMs wird in **Vorzeitig entfernt** geändert. 
- Die VM wird effektiv gelöscht, sodass alle auf der VM lokal gespeicherten Daten verloren gehen.
- Ein Vorgang zum Auflisten von Knoten gibt weiterhin die vorzeitig entfernten Knoten zurück.
- Der Pool versucht fortlaufend, die Zielanzahl der verfügbaren Knoten mit niedriger Priorität zu erreichen. Wenn Ersatzkapazitäten gefunden werden, behalten die Knoten ihre IDs bei, werden jedoch erneut initialisiert und durchlaufen wieder die Zustände **Wird erstellt** und **Wird gestartet**, bevor sie zum Planen von Aufgaben verfügbar sind.
- Die Anzahl der vorzeitigen Entfernungen ist als Metrik im Azure-Portal verfügbar.

## <a name="scale-pools-containing-low-priority-vms"></a>Skalieren von Pools mit VMs mit niedriger Priorität

Wie bei Pools, die ausschließlich aus dedizierten VMs bestehen, kann auch ein Pool mit VMs mit niedriger Priorität durch Aufrufen der Methode zum Ändern der Größe oder mithilfe der automatischen Skalierung skaliert werden.

Der Vorgang zum Ändern der Poolgröße nimmt einen zweiten optionalen Parameter entgegen, der den Wert von **targetLowPriorityNodes** aktualisiert:

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

Die Formel für die automatische Skalierung des Pools unterstützt VMs mit niedriger Priorität wie folgt:

- Den Wert der vom Dienst definierten Variable **$TargetLowPriorityNodes** können Sie abrufen oder festlegen.
- Den Wert der vom Dienst definierten Variable **$CurrentLowPriorityNodes** können Sie abrufen.
- Den Wert der vom Dienst definierten Variable **$PreemptedNodeCount** können Sie abrufen. Diese Variable gibt die Anzahl der Knoten mit dem Status „Vorzeitig entfernt“ zurück und ermöglicht Ihnen, die Anzahl der dedizierten Knoten abhängig von der Anzahl der nicht verfügbaren vorzeitig entfernten Knoten hoch- oder herunterzuskalieren.

## <a name="configure-jobs-and-tasks"></a>Konfigurieren von Aufträgen und Aufgaben

Für Aufträge und Aufgaben ist nur wenig zusätzliche Konfiguration für Knoten mit niedriger Priorität erforderlich. Berücksichtigen Sie dabei Folgendes:

- Die JobManagerTask-Eigenschaft eines Auftrags umfasst die Eigenschaft **AllowLowPriorityNode**. Wenn diese Eigenschaft auf TRUE festgelegt ist, kann die Auftrags-Manager-Aufgabe auf einem dedizierten oder einem Knoten mit niedriger Priorität geplant werden. Wenn diese Eigenschaft auf FALSE festgelegt ist, wird die Auftrags-Manager-Aufgabe nur auf einem dedizierten Knoten geplant.
- Die [Umgebungsvariable](batch-compute-node-environment-variables.md) AZ_BATCH_NODE_IS_DEDICATED ist für eine Aufgabenanwendung verfügbar, damit ermittelt werden kann, ob sie auf einem Knoten mit niedriger Priorität oder einem dedizierten Knoten ausgeführt wird.

## <a name="view-metrics-for-low-priority-vms"></a>Anzeigen von Metriken für VMs mit niedriger Priorität

Im [Azure-Portal](https://portal.azure.com) sind neue Metriken für Knoten mit niedriger Priorität verfügbar. Diese Metriken sind:

- Anzahl der Knoten mit niedriger Priorität
- Anzahl von Kernen mit niedriger Priorität
- Anzahl der vorzeitig entfernten Knoten

So zeigen Sie diese Metriken im Azure-Portal an:

1. Navigieren Sie im Azure-Portal zu Ihrem Batch-Konto.
2. Wählen Sie **Metriken** im Abschnitt **Überwachung** aus.
3. Wählen Sie die gewünschten Metriken aus der Liste **Metriken** aus.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über den [Workflow des Batch-Diensts und primäre Ressourcen](batch-service-workflow-features.md) wie Pools, Knoten, Aufträge und Aufgaben.
- Informieren Sie sich über die [Batch-APIs und Tools](batch-apis-tools.md), die für die Erstellung von Batch-Lösungen verfügbar sind.
- Beginnen Sie mit der Planung der Umstellung von VMs mit niedriger Priorität auf Spot-VMs. Wenn Sie VMs mit niedriger Priorität mit Pools für die **Clouddienstkonfiguration** verwenden, planen Sie stattdessen eine Migration zu [**VM-Konfigurationspools**](nodes-and-pools.md#configurations).

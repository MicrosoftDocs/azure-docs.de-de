---
title: Ausführen von Workloads auf kostengünstigen VMs mit niedriger Priorität – Azure Batch | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie VMs mit niedriger Priorität bereitstellen, um die Kosten von Azure Batch-Workloads zu verringern.
services: batch
author: mscurrell
manager: jeconnoc
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 03/19/2018
ms.author: markscu
ms.custom: seodec18
ms.openlocfilehash: 17668470be3e997c215aacc4cc2c32c80de2dd81
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60776125"
---
# <a name="use-low-priority-vms-with-batch"></a>Verwenden von VMs mit niedriger Priorität mit Batch

Azure Batch bietet virtuelle Computer (VMs) mit niedriger Priorität, um die Kosten für Batch-Workloads zu verringern. Durch VMs mit niedriger Priorität werden neue Typen von Batchworkloads möglich, indem eine große Menge an Computeleistung zu sehr niedrigen Kosten bereitgestellt wird.
 
VMs mit niedriger Priorität nutzen überschüssige Kapazitäten in Azure. Wenn Sie VMs mit niedriger Priorität in Pools angeben, kann Azure Batch diesen Überschuss nutzen, sobald er zur Verfügung steht.
 
Die Verwendung von VMs mit niedriger Priorität hat jedoch auch Nachteile: Möglicherweise stehen diese VMs für eine Zuweisung gerade nicht zur Verfügung, und sie können jederzeit vorzeitig entfernt werden, je nach der verfügbaren Kapazität. Aus diesem Grund sind VMs mit niedriger Priorität am besten für bestimmte Arten von Workloads geeignet. Verwenden Sie VMs mit niedriger Priorität für Batch und asynchrone Verarbeitungsworkloads, bei denen der Zeitpunkt des Auftragsabschlusses flexibel ist und die Arbeit über viele VMs verteilt wird.
 
VMs mit niedriger Priorität werden im Vergleich zu dedizierten VMs zu einem deutlich günstigeren Preis angeboten. Ausführliche Preisinformationen finden Sie unter [Preise für Batch](https://azure.microsoft.com/pricing/details/batch/).

## <a name="use-cases-for-low-priority-vms"></a>Anwendungsfälle für VMs mit niedriger Priorität

Welche Workloads sind aufgrund der besonderen Merkmale von VMs mit niedriger Priorität für diese geeignet? Im Allgemeinen sind Batch-Workloads gut geeignet, da die Aufträge in viele parallele Aufgaben aufgeteilt werden oder viele Aufträge vorhanden sind, die horizontal hochskaliert und auf viele VMs verteilt werden.

-   Zur Maximierung der Verwendung von überschüssigen Kapazitäten in Azure können geeignete Aufträge horizontal hochskaliert werden.

-   Gelegentlich sind die VMs möglicherweise nicht verfügbar oder werden vorzeitig entfernt. Dies führt zu einer verringerten Kapazität für Aufträge und damit möglicherweise zu Unterbrechungen und einem erneutem Ausführen von Aufgaben. Aufträge müssen daher flexibel in der zeitlichen Dauer ihrer Ausführung sein.

-   Aufträge mit längeren Aufgaben werden möglicherweise durch Unterbrechungen stärker beeinträchtigt. Wenn für Aufgaben mit langer Ausführungsdauer eine Prüfpunktausführung zum Speichern von Fortschritten bei der Ausführung implementiert wird, wirken sich Unterbrechungen weniger stark aus. Aufgaben mit kürzeren Ausführungszeiten funktionieren im Allgemeinen am besten mit VMs mit niedriger Priorität, da Unterbrechungen deutlich geringere Auswirkungen haben.

-   MPI-Aufträge mit langer Ausführungsdauer, die mehrere VMs nutzen, sind für die Verwendung von VMs mit niedriger Priorität nicht gut geeignet, da eine vorzeitig entfernte VM dazu führen kann, dass der gesamte Auftrag erneut ausgeführt werden muss.

Einige Beispiele für Anwendungsfälle mit Batchverarbeitung, die gut für VMs mit niedriger Priorität geeignet sind:

-   **Entwicklung und Tests**: Insbesondere bei der Entwicklung von umfangreichen Lösungen lassen sich beträchtliche Einsparungen realisieren. Alle Arten von Tests können profitieren, aber umfangreiche Auslastungstests und Regressionstests sind besonders gut geeignete Fälle.

-   **Ergänzen der bedarfsgesteuerten Kapazität**: Normale dedizierte VMs können durch VMs mit niedriger Priorität ergänzt werden: bei Verfügbarkeit dieser VMs können Aufträge skaliert und daher schneller und kostengünstiger abgeschlossen werden; bei Nichtverfügbarkeit stehen weiterhin die regulär bereitgestellten dedizierten VMs zur Verfügung.

-   **Flexible Auftragsausführungszeit**: Wenn die Zeit, in der Aufträge abgeschlossen werden müssen, flexibel ist, können potenzielle Kapazitätseinbußen toleriert werden. Durch Hinzufügen von VMs mit niedriger Priorität lassen sich Aufträge jedoch häufig schneller und kostengünstiger ausführen.

Batchpools können auf verschiedene Weise für VMs mit niedriger Priorität konfiguriert werden, je nachdem, wie flexibel die Ausführungszeit des Auftrags ist:

-   VMs mit niedriger Priorität können nur in einem Pool verwendet werden. In diesem Fall stellt Batch vorzeitig entfernte Kapazitäten bei Verfügbarkeit wieder her. Diese Konfiguration ist die kostengünstigste Möglichkeit, Aufträge auszuführen, da ausschließlich VMs mit niedriger Priorität verwendet werden.

-   VMs mit niedriger Priorität können in Verbindung mit einer festen Grundausstattung von dedizierten VMs verwendet werden. Die feste Anzahl von dedizierten VMs stellt sicher, dass immer eine gewisse Kapazität zum Fortsetzen der Verarbeitung eines Auftrags verbleibt.

-   Dedizierte VMs und VMs mit niedriger Priorität können dynamisch kombiniert werden, sodass ausschließlich die kostengünstigeren VMs mit niedriger Priorität verwendet werden, wenn sie verfügbar sind, bei Bedarf jedoch die dedizierten VMs zum vollen Preis zentral hochskaliert werden. Durch eine solche Konfiguration bleibt eine Mindestmenge an Kapazität für die fortlaufende Verarbeitung der Aufträge verfügbar.

## <a name="batch-support-for-low-priority-vms"></a>Unterstützung von VMs mit niedriger Priorität in Batch

Azure Batch stellt verschiedene Funktionen bereit, die die vorteilhafte Nutzung von VMs mit niedriger Priorität erleichtern:

-   Batchpools können sowohl dedizierte VMs als auch VMs mit niedriger Priorität enthalten. Die Anzahl der einzelnen VM-Typen kann beim Erstellen eines Pools angegeben und für einen bestehenden Pool mithilfe der ausdrücklichen Größenänderung oder der automatischen Skalierung jederzeit geändert werden. Die Auftrags- und Aufgabenübermittlung kann unabhängig von den VM-Typen im Pool unverändert bleiben. Sie können auch einen Pool zur ausschließlichen Verwendung von VMs mit niedriger Priorität konfigurieren, um Aufträge möglichst kostengünstig ausführen, jedoch die Anzahl der dedizierten VMs erhöhen zu können, wenn die Kapazität unter einen Mindestschwellenwert fällt. Dadurch werden Aufträge weiterhin ausgeführt.

-   In Batch-Pools wird automatisch die Zielanzahl von VMs mit niedriger Priorität angestrebt. Wenn VMs vorzeitig entfernt werden, versucht Batch, die verlorene Kapazität auszugleichen und das Ziel wieder zu erreichen.

-   Wenn Aufgaben unterbrochen werden, erkennt Batch dies und reiht die Aufgaben zur erneuten Ausführung automatisch wieder in eine Warteschlange.

-   VMs mit niedriger Priorität verfügen über ein separates vCPU-Kontingent, das von dem dedizierter VMs abweicht. 
    Das Kontingent für VMs mit niedriger Priorität ist höher als bei dedizierten VMs, da VMs mit niedriger Priorität weniger kosten. Weitere Informationen finden Sie im Artikel [Batch-Dienst – Kontingente und Limits](batch-quota-limit.md#resource-quotas).    

> [!NOTE]
> VMs mit niedriger Priorität werden derzeit für Batch-Konten, die im Modus [Benutzerabonnement](batch-api-basics.md#account) erstellt wurden, nicht unterstützt.
>

## <a name="create-and-update-pools"></a>Erstellen und Aktualisieren von Pools

Ein Batchpool kann sowohl dedizierte VMs als auch VMs mit niedriger Priorität (auch als Serverknoten bezeichnet) enthalten. Sie können die Zielanzahl der Serverknoten für dedizierte VMs und VMs mit niedriger Priorität festlegen. Die Zielanzahl der Knoten gibt die Anzahl von VMs an, die im Pool vorhanden sein sollen.

Um beispielsweise einen Pool von Azure-Clouddienst-VMs mit einem Ziel von 5 dedizierten VMs und 20 VMs mit niedriger Priorität zu erstellen, gehen Sie folgendermaßen vor:

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5") // WS 2016
);
```

Um einen Pool von Azure-VMs (in diesem Fall virtuelle Linux-Computer) mit einem Ziel von 5 dedizierten VMs und 20 VMs mit niedriger Priorität zu erstellen, gehen Sie folgendermaßen vor:

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

Wenn ein oder mehrere Knoten in einem Pool vorzeitig entfernt werden, gibt ein Listenknotenvorgang im Pool diese Knoten weiterhin zurück. Die aktuelle Anzahl der Knoten mit niedriger Priorität bleibt unverändert, doch der Status dieser Knoten wird auf den Zustand **Vorzeitig entfernt** festgelegt. Batch versucht, Ersatz-VMs zu finden, und bei erfolgreicher Suche durchlaufen die Knoten die Zustände **Wird erstellt** und dann **Wird gestartet**, bevor sie für die Aufgabenausführung verfügbar sind – genau wie neue Knoten.

## <a name="scale-a-pool-containing-low-priority-vms"></a>Skalieren eines Pools mit VMs mit niedriger Priorität

Wie bei Pools, die ausschließlich aus dedizierten VMs bestehen, kann auch ein Pool mit VMs mit niedriger Priorität durch Aufrufen der Methode zum Ändern der Größe oder mithilfe der automatischen Skalierung skaliert werden.

Der Vorgang zum Ändern der Poolgröße nimmt einen zweiten optionalen Parameter entgegen, der den Wert von **targetLowPriorityNodes** aktualisiert:

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

Die Formel für die automatische Skalierung des Pools unterstützt VMs mit niedriger Priorität wie folgt:

-   Den Wert der vom Dienst definierten Variable **$TargetLowPriorityNodes** können Sie abrufen oder festlegen.

-   Den Wert der vom Dienst definierten Variable **$CurrentLowPriorityNodes** können Sie abrufen.

-   Den Wert der vom Dienst definierten Variable **$PreemptedNodeCount** können Sie abrufen. 
    Diese Variable gibt die Anzahl der Knoten mit dem Status „Vorzeitig entfernt“ zurück und ermöglicht Ihnen, die Anzahl der dedizierten Knoten abhängig von der Anzahl der nicht verfügbaren vorzeitig entfernten Knoten zentral hoch oder herunter zu skalieren.

## <a name="jobs-and-tasks"></a>Aufträge und Aufgaben

Aufträge und Aufgaben erfordern nur wenige zusätzliche Konfigurationen für Knoten mit niedriger Priorität – die einzige Unterstützung ist die folgende:

-   Die JobManagerTask-Eigenschaft eines Auftrags hat eine neue Eigenschaft **AllowLowPriorityNode**. 
    Wenn diese Eigenschaft auf TRUE festgelegt ist, kann die Auftrags-Manager-Aufgabe auf einem dedizierten oder einem Knoten mit niedriger Priorität geplant werden. Wenn diese Eigenschaft auf „false“ festgelegt ist, wird die Auftrags-Manager-Aufgabe ausschließlich auf einen dedizierten Knoten geplant.

-   Für eine Aufgabenanwendung steht eine [Umgebungsvariable](batch-compute-node-environment-variables.md) zur Verfügung, sodass ermittelt werden kann, ob die Anwendung auf einem Knoten mit niedriger Priorität oder einem dedizierten Knoten ausgeführt wird. Die Umgebungsvariable lautet AZ_BATCH_NODE_IS_DEDICATED.

## <a name="handling-preemption"></a>Behandeln der vorzeitigen Entfernung

VMs werden gelegentlich vorzeitig entfernt. In diesem Fall geschieht in Batch Folgendes:

-   Der Status der vorzeitig entfernten VMs wird in **Vorzeitig entfernt** geändert.
-   Wenn auf den VMs der vorzeitig entfernten Knoten Aufgaben ausgeführt wurden, werden diese Aufgaben wieder in die Warteschlange gestellt und erneut ausgeführt.
-   Die VM wird effektiv gelöscht, sodass alle auf der VM lokal gespeicherten Daten verloren gehen.
-   Der Pool versucht fortlaufend, die Zielanzahl der verfügbaren Knoten mit niedriger Priorität zu erreichen. Wenn Ersatzkapazitäten gefunden werden, behalten die Knoten ihre IDs bei, werden jedoch erneut initialisiert und durchlaufen wieder die Zustände **Wird erstellt** und **Wird gestartet**, bevor sie zum Planen von Aufgaben verfügbar sind.
-   Die Anzahl der vorzeitigen Entfernungen ist als Metrik im Azure-Portal verfügbar.

## <a name="metrics"></a>Metriken

Im [Azure-Portal](https://portal.azure.com) sind neue Metriken für Knoten mit niedriger Priorität verfügbar. Diese Metriken sind:

- Anzahl der Knoten mit niedriger Priorität
- Anzahl von Kernen mit niedriger Priorität 
- Anzahl der vorzeitig entfernten Knoten

Metriken im Azure-Portal anzeigen:

1. Navigieren Sie zu Ihrem Batch-Konto im Verwaltungsportal, und zeigen Sie die Einstellungen für Ihr Batch-Konto an.
2. Wählen Sie **Metriken** im Abschnitt **Überwachung** aus.
3. Wählen Sie die Metriken, die Sie erhalten möchten, aus der Liste **Verfügbare Metriken** aus.

![Metriken für Knoten mit niedriger Priorität](media/batch-low-pri-vms/low-pri-metrics.png)

## <a name="next-steps"></a>Nächste Schritte

* Sehen Sie sich die [Übersicht über Batch-Features für Entwickler](batch-api-basics.md) an. Diese enthält wichtige Informationen für Entwickler, die sich auf die Verwendung von Batch vorbereiten möchten. Der Artikel enthält ausführliche Informationen zu Batch-Dienstressourcen wie Pools, Knoten, Aufträgen und Aufgaben sowie zu den zahlreichen API-Funktionen, die beim Erstellen der Batch-Anwendung verwendet werden können.
* Informieren Sie sich über die [Batch-APIs und Tools](batch-apis-tools.md), die für die Erstellung von Batch-Lösungen verfügbar sind.

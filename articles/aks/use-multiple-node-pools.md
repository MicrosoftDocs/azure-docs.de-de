---
title: Verwenden mehrerer Knotenpools in Azure Kubernetes Service (AKS)
description: Informationen zum Erstellen und Verwalten mehrerer Knotenpools für einen Cluster in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 04/08/2020
ms.openlocfilehash: 39c2fe177d0a6d913d7bf2b2baf44af3c69c0868
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006932"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Erstellen und Verwalten mehrerer Knotenpools für einen Cluster in Azure Kubernetes Service (AKS)

Im Azure Kubernetes Service (AKS) werden Knoten derselben Konfiguration zu *Knotenpools* zusammengefasst. Diese Knotenpools enthalten die zugrunde liegenden virtuellen Computer, die Ihre Anwendungen ausführen. Die anfängliche Anzahl der Knoten und ihre Größe (SKU) werden beim Erstellen eines AKS-Clusters festgelegt, der einen [Systemknotenpool][use-system-pool] erstellt. Sie können zusätzliche *Benutzerknotenpools* erstellen, um Anwendungen mit unterschiedlichen Compute- oder Speicheranforderungen zu unterstützen. Systemknotenpools dienen dem primären Zweck, kritische Systempods wie CoreDNS und tunnelfront zu hosten. Benutzerknotenpools dienen dem primären Zweck, Ihre Anwendungspods zu hosten. Anwendungspods lassen sich jedoch auf Systemknotenpools planen, wenn Sie nur einen Pool in Ihrem AKS-Cluster haben möchten. In Benutzerknotenpools können Sie dagegen anwendungsspezifische Pods speichern. Verwenden Sie diese zusätzlichen Benutzerknotenpools z. B. zum Bereitstellen von GPUs für rechenintensive Anwendungen oder für den Zugriff auf leistungsstarken SSD-Speicher.

> [!NOTE]
> Diese Funktion ermöglicht eine höhere Kontrolle über das Erstellen und Verwalten mehrerer Knotenpools. Daher sind separate Befehle zum Erstellen, Aktualisieren und Löschen erforderlich. Für über `az aks create` oder `az aks update` ausgeführte Clustervorgänge wurde bisher die managedCluster-API verwendet, und diese Vorgänge stellten die einzige Möglichkeit zum Ändern der Steuerungsebene und eines einzelnen Knotenpools dar. Diese Funktion stellt einen separaten Vorgang für Agent-Pools über die agentPool-API zur Verfügung und erfordert die Verwendung des `az aks nodepool`-Befehlssatzes zum Ausführen von Vorgängen für einen einzelnen Knotenpool.

In diesem Artikel erfahren Sie, wie Sie mehrere Knotenpools in einem AKS-Cluster erstellen und verwalten.

## <a name="before-you-begin"></a>Voraussetzungen

Azure CLI-Version 2.2.0 oder höher muss installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][install-azure-cli].

## <a name="limitations"></a>Einschränkungen

Die folgenden Einschränkungen gelten für die Erstellung und Verwaltung von AKS-Clustern, die mehrere Knotenpools unterstützen:

* Siehe [Kontingente, Größeneinschränkungen für virtuelle Computer und regionale Verfügbarkeit in Azure Kubernetes Service (AKS)][quotas-skus-regions].
* Systemknotenpools können Sie löschen, wenn im AKS-Cluster ein anderer Systemknotenpool als Ersatz vorhanden ist.
* Systempools müssen mindestens einen Knoten enthalten, während Benutzerknotenpools keine oder mehrere Knoten enthalten können.
* Der AKS-Cluster muss den Lastenausgleich mit der SKU „Standard“ nutzen, um mehrere Knotenpools verwenden zu können. Das Feature wird für Lastenausgleichsmodule der SKU „Basic“ nicht unterstützt.
* Der AKS-Cluster muss VM-Skalierungsgruppen für die Knoten verwenden.
* Der Name eines Knotenpools darf nur Kleinbuchstaben und Ziffern enthalten und muss mit einem Kleinbuchstaben beginnen. Bei Linux-Knotenpools muss die Länge zwischen einem und zwölf Zeichen liegen. Bei Windows-Knotenpools muss die Länge zwischen einem und sechs Zeichen betragen.
* Alle Knotenpools müssen sich im selben virtuellen Netzwerk befinden.
* Beim Erstellen mehrerer Knotenpools während der Clustererstellung muss die Kubernetes-Version für alle Knotenpools der für die Steuerungsebene festgelegten Version entsprechen. Dies kann nach dem Bereitstellen des Clusters mithilfe von Poolvorgängen pro Knoten aktualisiert werden.

## <a name="create-an-aks-cluster"></a>Erstellen eines AKS-Clusters

> [!Important]
> Wenn Sie für Ihren AKS-Cluster nur einen Systemknotenpool in einer Produktionsumgebung ausführen, sollten Sie für den Knotenpool mindestens drei Knoten verwenden.

Erstellen Sie zu Beginn einen AKS-Cluster mit einem einzelnen Knotenpool. Im folgenden Beispiel wird der Befehl [az group create][az-group-create] verwendet, um eine Ressourcengruppe namens *myResourceGroup* in der Region *eastus* zu erstellen. Anschließend wird mit dem Befehl [az aks create][az-aks-create] ein AKS-Cluster mit dem Namen *myAKSCluster* erstellt.

> [!NOTE]
> Die Load Balancer-SKU *Basic* wird bei Verwendung mehrerer Knotenpools **nicht unterstützt**. Standardmäßig werden AKS-Cluster mit der Lastenausgleichs-SKU *Standard* über die Azure-Befehlszeilenschnittstelle und das Azure-Portal erstellt.

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 2 \
    --generate-ssh-keys \
    --load-balancer-sku standard
```

Die Erstellung des Clusters dauert einige Minuten.

> [!NOTE]
> Um sicherzustellen, dass Ihr Cluster zuverlässig funktioniert, sollten Sie mindestens zwei (2) Knoten im Standardknotenpool ausführen, da wichtige Systemdienste in diesem Knotenpool ausgeführt werden.

Wenn der Cluster bereit ist, verwenden Sie den Befehl [az aks get-credentials][az-aks-get-credentials], um die Clusteranmeldeinformationen für die Verwendung mit `kubectl` zu erhalten:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Hinzufügen eines Knotenpools

Der im vorherigen Schritt erstellte Cluster verfügt über einen einzelnen Knotenpool. Fügen Sie als Nächstes einen zweiten Knotenpool mit dem Befehl [az aks nodepool add][az-aks-nodepool-add] hinzu. Das folgende Beispiel erstellt einen Knotenpool namens *mynodepool*, der *3* Knoten ausführt:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

> [!NOTE]
> Der Name eines Knotenpools muss mit einem Kleinbuchstaben beginnen und darf nur alphanumerische Zeichen enthalten. Bei Linux-Knotenpools muss die Länge zwischen einem und zwölf Zeichen liegen. Bei Windows-Knotenpools muss die Länge zwischen einem und sechs Zeichen betragen.

Um den Status Ihrer Knotenpools anzuzeigen, verwenden Sie den Befehl [az aks node pool list][az-aks-nodepool-list], und geben Sie Ihre Ressourcengruppe und den Clusternamen an:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

Die folgende Beispielausgabe zeigt, dass *mynodepool* erfolgreich mit drei Knoten im Knotenpool erstellt wurde. Wenn der AKS-Cluster im vorherigen Schritt erstellt wurde, dann wurde ein Standardknotenpool (*nodepool1*) mit *2* Knoten erstellt.

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> Wenn beim Hinzufügen eines Knotenpools keine *VmSize* angegeben ist, lautet die Standardgröße *Standard_D2s_v3* für Windows-Knotenpools und *Standard_DS2_v2* für Linux-Knotenpools. Wenn keine *OrchestratorVersion* angegeben ist, wird standardmäßig die gleiche Version wie für die Steuerungsebene verwendet.

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>Hinzufügen eines Knotenpools mit einem eindeutigen Subnetz (Vorschau)

Eine Workload erfordert für die logische Isolation möglicherweise das Aufteilen der Knoten eines Clusters in getrennte Pools. Diese Isolation kann mit separaten Subnetzen unterstützt werden, die für die einzelnen Knotenpools im Cluster vorgesehen sind. Damit können spezifische Anforderungen erfüllt werden, wie z. B. ein nicht zusammenhängender IP-Adressraum für das virtuelle Netzwerk, der auf Knotenpools aufgeteilt ist.

#### <a name="limitations"></a>Einschränkungen

* Alle Subnetze, die Knotenpools zugewiesen sind, müssen demselben virtuellen Netzwerk angehören.
* Systempods müssen Zugriff auf alle Knoten im Cluster haben, um wichtige Funktionen wie die DNS-Auflösung über coreDNS bereitstellen zu können.
* Die Zuweisung eines eindeutigen Subnetzes pro Knotenpool ist nur während der Vorschauphase von Azure CNI möglich.
* Die Verwendung von Netzwerkrichtlinien mit einem eindeutigen Subnetz pro Knotenpool wird während der Vorschau nicht unterstützt.

Um einen Knotenpool mit einem dedizierten Subnetz zu erstellen, übergeben Sie beim Erstellen des Knotenpools die Subnetzressourcen-ID als zusätzlichen Parameter.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>Durchführen eines Upgrades für einen Knotenpool

> [!NOTE]
> Upgrade- und Skalierungsvorgänge für einen Cluster- oder Knotenpool können nicht gleichzeitig ausgeführt werden. Bei dem Versuch wird ein Fehler zurückgegeben. Stattdessen muss jeder Vorgangstyp für die Zielressource abgeschlossen sein, bevor eine neue Anforderung an dieselbe Ressource gerichtet werden kann. Weitere Informationen hierzu finden Sie in unserem [Leitfaden zur Problembehandlung](./troubleshooting.md#im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade).

Mit den Befehlen in diesem Abschnitt wird gezeigt, wie ein Upgrade für einen einzelnen spezifischen Knotenpool durchgeführt wird. Die Beziehung zwischen dem Upgrade der Kubernetes-Version der Steuerungsebene und des Knotenpools wird im [Abschnitt weiter unten](#upgrade-a-cluster-control-plane-with-multiple-node-pools) erläutert.

> [!NOTE]
> Die Betriebssystem-Imageversion des Knotenpools ist an die Kubernetes-Version des Clusters gebunden. Upgrades für Betriebssystemimages erhalten Sie nur nach einem Clusterupgrade.

Da in diesem Beispiel zwei Knotenpools vorhanden sind, muss [az aks nodepool upgrade][az-aks-nodepool-upgrade] für das Upgrade eines Knotenpools verwendet werden. Verwenden Sie [az aks get-upgrades][az-aks-get-upgrades], um die verfügbaren Upgrades anzuzeigen.

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Führen Sie für *mynodepool* ein Upgrade durch. Verwenden Sie den Befehl [az aks nodepool upgrade][az-aks-nodepool-upgrade], um ein Upgrade für den Knotenpool durchzuführen, wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version KUBERNETES_VERSION \
    --no-wait
```

Listen Sie den Status Ihrer Knotenpools mit dem Befehl [az aks node pool list][az-aks-nodepool-list] erneut auf. Das folgende Beispiel zeigt, dass *mynodepool* den Zustand *Upgrading* (Wird aktualisiert) auf *KUBERNETES_VERSION* aufweist:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "KUBERNETES_VERSION",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Es dauert einige Minuten, bis ein Upgrade der Knoten auf die angegebene Version erfolgt ist.

Als bewährte Methode sollten Sie alle Knotenpools in einem AKS-Cluster auf dieselbe Kubernetes-Version aktualisieren. Beim Standardverhalten von `az aks upgrade` werden alle Knotenpools gemeinsam mit der Steuerungsebene aktualisiert, um diese Anpassung zu erzielen. Durch die Möglichkeit, ein Upgrade für einzelne Knotenpools durchzuführen, können Sie ein paralleles Upgrade durchführen und Pods zwischen Knotenpools planen, um die Anwendungsbetriebszeit innerhalb der oben genannten Einschränkungen aufrechtzuerhalten.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Aktualisieren einer Clustersteuerungsebene mit mehreren Knotenpools

> [!NOTE]
> Kubernetes verwendet als Standardversionierungsschema die [semantische Versionierung](https://semver.org/). Die Versionsnummer wird im Format *x.y.z* angegeben. *x* steht dabei für die Hauptversion, *y* für die Nebenversion und *z* für die Patchversion. Ein Beispiel: Bei der Version *1.12.6* ist „1“ die Hauptversion, „12“ die Nebenversion und „6“ die Patchversion. Die Kubernetes-Versionen der Steuerungsebene und des ursprünglichen Knotenpools werden bei der Clustererstellung festgelegt. Bei allen zusätzlichen Knotenpools wird die Kubernetes-Version festgelegt, wenn sie dem Cluster hinzugefügt werden. Die Kubernetes-Versionen können sich zwischen Knotenpools sowie zwischen einem Knotenpool und der Steuerungsebene unterscheiden.

Ein AKS-Cluster verfügt über zwei Clusterressourcenobjekte mit zugeordneten Kubernetes-Versionen.

1. Eine Kubernetes-Version der Clustersteuerungsebene
2. Ein Knotenpool mit einer Kubernetes-Version

Eine Steuerungsebene ist einem oder mehreren Knotenpools zugeordnet. Das Verhalten eines Upgradevorgangs hängt davon ab, welcher Azure CLI-Befehl verwendet wird.

Zum Aktualisieren einer AKS-Steuerungsebene muss `az aks upgrade` verwendet werden. Durch diesen Befehl werden die Version der Steuerungsebene und alle Knotenpools im Cluster aktualisiert.

Beim Ausgeben des Befehls `az aks upgrade` mit dem Flag `--control-plane-only` wird nur die Clustersteuerungsebene aktualisiert. Keiner der zugeordneten Knotenpools im Cluster wird geändert.

Zum Aktualisieren einzelner Knotenpools muss `az aks nodepool upgrade` verwendet werden. Durch diesen Befehl wird nur der Zielknotenpool mit der angegebenen Kubernetes-Version aktualisiert.

### <a name="validation-rules-for-upgrades"></a>Validierungsregeln für Upgrades

Die gültigen Kubernetes-Upgrades für die Steuerungsebene und die Knotenpools eines Clusters werden anhand der folgenden Regeln überprüft.

* Regeln für gültige Versionen zum Aktualisieren von Knotenpools:
   * Die Knotenpoolversion muss dieselbe *Hauptversion* aufweisen wie die Steuerungsebene.
   * Die *Nebenversion* des Knotenpools muss innerhalb von zwei *Nebenversionen* der Version der Steuerungsebene liegen.
   * Die Version des Knotenpools darf nicht höher sein als die Version `major.minor.patch` der Steuerungsebene.

* Regeln für die Übermittlung eines Upgradevorgangs:
   * Sie können die Kubernetes-Version der Steuerungsebene und die eines Knotenpools nicht herabstufen.
   * Wenn die Kubernetes-Version eines Knotenpools nicht angegeben ist, hängt das Verhalten vom verwendeten Client ab. Bei der Deklaration in Resource Manager-Vorlagen wird auf die für den Knotenpool definierte vorhandene Version zurückgegriffen, sofern sie verwendet wird. Wenn kein Wert festgelegt ist, wird die Version der Steuerungsebene verwendet.
   * Sie können eine Steuerungsebene oder einen Knotenpool zu einem bestimmten Zeitpunkt aktualisieren oder skalieren. Sie können nicht mehrere Vorgänge für eine Steuerungsebene oder einen Knotenpool gleichzeitig übermitteln.

## <a name="scale-a-node-pool-manually"></a>Manuelles Skalieren eines Knotenpools

Wenn sich die Anforderungen der Workloads Ihrer Anwendungen ändern, müssen Sie möglicherweise die Anzahl der Knoten in einem Knotenpool skalieren. Die Anzahl der Knoten kann erhöht oder verringert werden.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Um die Anzahl der Knoten in einem Knotenpool zu skalieren, verwenden Sie den Befehl [az aks node pool scale][az-aks-nodepool-scale]. Das folgende Beispiel skaliert die Anzahl der Knoten in *mynodepool* auf *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Listen Sie den Status Ihrer Knotenpools mit dem Befehl [az aks node pool list][az-aks-nodepool-list] erneut auf. Das folgende Beispiel zeigt, dass *mynodepool* den Zustand *Scaling* (Wird skaliert) mit einer neuen Anzahl von *5* Knoten aufweist:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Es dauert einige Minuten, bis die Skalierung abgeschlossen ist.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Automatisches Skalieren eines bestimmten Knotenpools durch Aktivieren der automatischen Clusterskalierung

AKS bietet eine separate Funktion zum automatischen Skalieren von Knotenpools mit einem als [automatische Clusterskalierung](cluster-autoscaler.md) bezeichneten Feature. Das Feature kann mit einer eindeutigen minimalen und maximalen Anzahl von Skalierungen pro Knotenpool aktiviert werden. Erfahren Sie, wie Sie [die automatische Clusterskalierung pro Knotenpool verwenden](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled).

## <a name="delete-a-node-pool"></a>Löschen eines Knotenpools

Wenn Sie einen Pool nicht mehr benötigen, können Sie ihn löschen und die zugrunde liegenden VM-Knoten entfernen. Um einen Knotenpool zu löschen, verwenden Sie den Befehl [az aks node pool delete][az-aks-nodepool-delete], und geben Sie den Namen des Knotenpools an. Das folgende Beispiel löscht den in den vorherigen Schritten erstellten Knotenpool *mynodepool*:

> [!CAUTION]
> Es gibt keine Wiederherstellungsoptionen für Datenverluste, die beim Löschen eines Knotenpools auftreten können. Wenn Pods nicht in anderen Knotenpools geplant werden können, sind diese Anwendungen nicht verfügbar. Stellen Sie sicher, dass Sie einen Knotenpool nicht löschen, wenn aktive Anwendungen keine Datensicherungen aufweisen oder nicht in anderen Knotenpools in Ihrem Cluster ausgeführt werden können.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

Die folgende Beispielausgabe des Befehls [az aks node pool list][az-aks-nodepool-list] zeigt, dass sich *mynodepool* im Zustand *Deleting* (Wird gelöscht) befindet:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Das Löschen der Knoten und des Knotenpools dauert einige Minuten.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Angeben einer VM-Größe für einen Knotenpool

In den vorherigen Beispielen zur Erstellung eines Knotenpools wurde für die im Cluster erstellten Knoten eine VM-Standardgröße verwendet. Ein üblicheres Szenario ist es, Knotenpools mit unterschiedlichen VM-Größen und -Funktionen zu erstellen. Sie können z. B. einen Knotenpool erstellen, der Knoten mit einer großen Anzahl an CPUs oder mit viel Arbeitsspeicher enthält, oder einen Knotenpool, der GPU-Unterstützung bietet. Im nächsten Schritt teilen Sie dem Kubernetes-Planer durch [Verwenden von Taints und Toleranzen](#setting-nodepool-taints) mit, wie Sie den Zugriff auf Pods, die auf diesen Knoten ausgeführt werden können, einschränken können.

Erstellen Sie im folgenden Beispiel einen GPU-basierten Knotenpool, der die VM-Größe *Standard_NC6* verwendet. Diese virtuellen Computer werden von der NVIDIA Tesla K80-Karte unterstützt. Informationen zu den verfügbaren VM-Größen finden Sie unter [Größen für virtuelle Linux-Computer in Azure][vm-sizes].

Erstellen Sie erneut einen Knotenpool mit dem Befehl [az aks node pool add][az-aks-nodepool-add]. Geben Sie diesmal den Namen *gpunodepool* und mit dem Parameter `--node-vm-size` die Größe *Standard_NC6* an:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

Die folgende Beispielausgabe des Befehls [az aks node pool list][az-aks-nodepool-list] zeigt, dass *gpunodepool* Knoten mit der angegebenen *VmSize* (VM-Größe) *erstellt*:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Es dauert einige Minuten, bis *gpunodepool* erfolgreich erstellt wurde.

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>Angeben von Taint, Bezeichnung oder Tag für einen Knotenpool

### <a name="setting-nodepool-taints"></a>Festlegen von Knotenpooltaints

Beim Erstellen eines Knotenpools können Sie diesem Knotenpool Taints, Bezeichnungen oder Tags hinzufügen. Wenn Sie einen Taint, eine Bezeichnung oder ein Tag hinzufügen, erhalten alle Knoten innerhalb dieses Knotenpools ebenfalls diesen Taint, diese Bezeichnung oder dieses Tag.

Verwenden Sie [az aks nodepool add][az-aks-nodepool-add], um einen Knotenpool mit einem Taint zu erstellen. Geben Sie den Namen *taintnp* an, und verwenden Sie den Parameter `--node-taints`, um *sku=gpu:NoSchedule* für den Taint anzugeben.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-count 1 \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

> [!NOTE]
> Ein Taint kann nur bei der Erstellung des Knotenpools festgelegt werden.

Die folgende Beispielausgabe des Befehls [az aks nodepool list][az-aks-nodepool-list] zeigt, dass *taintnp* Knoten mit dem angegebenen *nodeTaints* *erstellt*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "taintnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeTaints":  [
      "sku=gpu:NoSchedule"
    ],
    ...
  },
 ...
]
```

Die Taint-Informationen sind in Kubernetes für die Behandlung von Planungsregeln für Knoten sichtbar. Der Kubernetes-Planer kann Taints und Toleranzen verwenden, um einzuschränken, welche Workloads auf Knoten ausgeführt werden können.

* Ein **Taint** wird auf einen Knoten angewendet, der anzeigt, dass nur bestimmte Pods darauf geplant werden können.
* Für den Pod wird anschließend eine **Toleranz** angewendet, damit dieser den Taint des Knotens *tolerieren* kann.

Weitere Informationen zur Verwendung der erweiterten geplanten Kubernetes-Features finden Sie unter [Best Practices für erweiterte Scheduler-Funktionen in Azure Kubernetes Service (AKS)][taints-tolerations].

Im vorherigen Schritt haben Sie beim Erstellen Ihres Knotenpools den *sku=gpu:NoSchedule*-Taint angewendet. Das folgende einfache YAML-Beispielmanifest verwendet eine Toleranz, damit der Kubernetes-Planer einen NGINX-Pod auf einem Knoten in diesem Knotenpool ausführen kann.

Erstellen Sie eine Datei mit dem Namen `nginx-toleration.yaml`, und fügen Sie den folgenden YAML-Beispielcode ein:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Planen Sie den Pod mit dem Befehl `kubectl apply -f nginx-toleration.yaml`:

```console
kubectl apply -f nginx-toleration.yaml
```

Es dauert einige Sekunden, um den Pod zu planen und das NGINX-Image per Pull abzurufen. Verwenden Sie den Befehl [kubectl describe pod][kubectl-describe], um den Podstatus anzuzeigen. Die folgende kompakte Beispielausgabe zeigt, dass die Toleranz *sku=gpu:NoSchedule* angewendet wird. Im Ereignisabschnitt hat der Planer den Pod dem Knoten *aks-taintnp-28993262-vmss000000* zugewiesen:

```console
kubectl describe pod mypod
```

```output
[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                Message
  ----    ------     ----   ----                -------
  Normal  Scheduled  4m48s  default-scheduler   Successfully assigned default/mypod to aks-taintnp-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet             pulling image "mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine"
  Normal  Pulled     4m43s  kubelet             Successfully pulled image "mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine"
  Normal  Created    4m40s  kubelet             Created container
  Normal  Started    4m40s  kubelet             Started container
```

Nur Pods mit dieser Toleranz können auf Knoten in *taintnp* geplant werden. Alle anderen Pods werden im Knotenpool *nodepool1* geplant. Wenn Sie weitere Knotenpools erstellen, können Sie mit zusätzlichen Taints und Toleranzen einschränken, welche Pods für diese Knotenressourcen geplant werden können.

### <a name="setting-nodepool-labels"></a>Festlegen von Knotenpoolbezeichnungen

Sie können einem Knotenpool auch während der Erstellung des Knotenpools Bezeichnungen hinzufügen. Für den Knotenpool festgelegte Bezeichnungen werden jedem Knoten im Knotenpool hinzugefügt. Diese [Bezeichnungen sind in Kubernetes][kubernetes-labels] zur Behandlung von Planungsregeln für Knoten sichtbar.

Verwenden Sie [az aks nodepool add][az-aks-nodepool-add], um einen Knotenpool mit einer Bezeichnung zu erstellen. Geben Sie den Namen *labelnp* an, und verwenden Sie den `--labels`-Parameter, um *dept=IT* und *costcenter=9999* als Bezeichnungen anzugeben.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> Die Bezeichnung kann für Knotenpools nur während der Erstellung des Knotenpools festgelegt werden. Bezeichnungen müssen auch ein Schlüssel-Wert-Paar sein und eine [gültige Syntax][kubernetes-label-syntax] aufweisen.

Die folgende Beispielausgabe des Befehls [az aks nodepool list][az-aks-nodepool-list] zeigt, dass *labelnp* Knoten mit dem angegebenen *nodeLabels* *erstellt*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "labelnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeLabels":  {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

### <a name="setting-nodepool-azure-tags"></a>Festlegen von Azure-Tags für Knotenpools

Sie können ein Azure-Tag auf Knotenpools in Ihrem AKS-Cluster anwenden. Auf einen Knotenpool angewendete Tags werden auf jeden Knoten innerhalb des Knotenpools angewendet und bleiben bei Upgrades erhalten. Tags werden auch auf neue Knoten angewendet, die einem Knotenpool im Rahmen von Vorgängen zum horizontalen Skalieren hinzugefügt werden. Das Hinzufügen eines Tags kann Aufgaben wie das Nachverfolgen von Richtlinien oder die Kostenschätzung erleichtern.

Bei den Schlüsseln von Azure-Tags wird die Groß-/Kleinschreibung für Vorgänge nicht beachtet, z. B. beim Abrufen eines Tags durch Suchen nach dem Schlüssel. In diesem Fall wird ein Tag mit dem angegebenen Schlüssel unabhängig von der Groß-/Kleinschreibung aktualisiert oder abgerufen. Bei den Tagwerten wird Groß- und Kleinschreibung unterschieden.

Wenn in AKS mehrere Tags gleiche Schlüssel mit unterschiedlicher Groß-/Kleinschreibung aufweisen, wird das erste Tag in alphabetischer Reihenfolge verwendet. Beispielsweise führt `{"Key1": "val1", "kEy1": "val2", "key1": "val3"}` zur Festlegung von `Key1` und `val1`.

Erstellen Sie einen Knotenpool mit dem Befehl [az aks nodepool add][az-aks-nodepool-add]. Geben Sie den Namen *tagnodepool* an, und verwenden Sie den `--tag`-Parameter, um *dept=IT* und *costcenter=9999* als Tags anzugeben.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name tagnodepool \
    --node-count 1 \
    --tags dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> Sie können den `--tags`-Parameter darüber hinaus mit dem [az aks nodepool update][az-aks-nodepool-update]-Befehl sowie während der Clustererstellung verwenden. Während der Clustererstellung wendet der `--tags`-Parameter das Tag auf den zusammen mit dem Cluster erstellten ursprünglichen Knotenpool an. Alle Tagnamen müssen den in [Verwenden von Tags zum Organisieren von Azure-Ressourcen][tag-limitation] genannten Einschränkungen entsprechen. Das Aktualisieren eines Knotenpools mit dem `--tags`-Parameter bewirkt die Aktualisierung aller Tagwerte und fügt ggf. vorhandene neue Tags an. Wenn Ihr Knotenpool beispielsweise die Tags *dept=IT* und *costcenter=9999* aufweist und sie ihn mit den Tags *team=dev* und *costcenter=111* aktualisieren, hat Ihr Knotenpool die Tags *dept=IT*, *costcenter=111* und *team=dev*.

Die folgende Beispielausgabe des Befehls [az aks nodepool list][az-aks-nodepool-list] zeigt, dass *tagnodepool* Knoten mit dem angegebenen *Tag* *erstellt*:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "tagnodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "tags": {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Verwalteten von Knotenpools mithilfe einer Resource Manager-Vorlage

Wenn Sie eine Azure Resource Manager-Vorlage zum Erstellen und Verwalten von Ressourcen verwenden, können Sie die Einstellungen in Ihrer Vorlage normalerweise aktualisieren und neu bereitstellen, um die Ressource zu aktualisieren. Bei Knotenpools in AKS kann das Profil des Anfangsknotenpools nicht mehr aktualisiert werden, nachdem der AKS-Cluster erstellt wurde. Dieses Verhalten bedeutet, dass Sie eine vorhandene Resource Manager-Vorlage nicht aktualisieren können, um eine Änderung an den Knotenpools vorzunehmen und dann neu bereitzustellen. Stattdessen müssen Sie eine separate Resource Manager-Vorlage erstellen, über die nur die Knotenpools für einen vorhandenen AKS-Cluster aktualisiert werden.

Erstellen Sie eine Vorlage wie `aks-agentpools.json`, und fügen Sie das folgende Beispielmanifest ein. Diese Beispielvorlage konfiguriert die folgenden Einstellungen:

* Aktualisiert den *Linux*-Knotenpool mit dem Namen *myagentpool*, sodass dieser drei Knoten ausführt.
* Legt die Knoten im Knotenpool auf die Ausführung von Kubernetes Version *1.15.7* fest.
* Definiert die Knotengröße als *Standard_DS2_v2*.

Bearbeiten Sie diese Werte nach Bedarf, um Knotenpools nach Bedarf zu aktualisieren, hinzuzufügen oder zu löschen:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
            "type": "string",
            "metadata": {
                "description": "The name of your existing AKS cluster."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "The location of your existing AKS cluster."
            }
        },
        "agentPoolName": {
            "type": "string",
            "defaultValue": "myagentpool",
            "metadata": {
                "description": "The name of the agent pool to create or update."
            }
        },
        "vnetSubnetId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The Vnet subnet resource ID for your existing AKS cluster."
            }
        }
    },
    "variables": {
        "apiVersion": {
            "aks": "2020-01-01"
        },
        "agentPoolProfiles": {
            "maxPods": 30,
            "osDiskSizeGB": 0,
            "agentCount": 3,
            "agentVmSize": "Standard_DS2_v2",
            "osType": "Linux",
            "vnetSubnetId": "[parameters('vnetSubnetId')]"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-01-01",
            "type": "Microsoft.ContainerService/managedClusters/agentPools",
            "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
            "location": "[parameters('location')]",
            "properties": {
                "maxPods": "[variables('agentPoolProfiles').maxPods]",
                "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
                "count": "[variables('agentPoolProfiles').agentCount]",
                "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
                "osType": "[variables('agentPoolProfiles').osType]",
                "storageProfile": "ManagedDisks",
                "type": "VirtualMachineScaleSets",
                "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
                "orchestratorVersion": "1.15.7"
            }
        }
    ]
}
```

Stellen Sie diese Vorlage mithilfe des Befehls [az group deployment create][az-group-deployment-create] bereit, wie im folgenden Beispiel gezeigt. Sie werden zur Eingabe des Namens und Speicherorts des vorhandenen AKS-Clusters aufgefordert:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> Sie können Ihrem Knotenpool ein Tag hinzufügen, indem Sie die *tag*-Eigenschaft in der Vorlage hinzufügen, wie im folgenden Beispiel gezeigt.
> 
> ```json
> ...
> "resources": [
> {
>   ...
>   "properties": {
>     ...
>     "tags": {
>       "name1": "val1"
>     },
>     ...
>   }
> }
> ...
> ```

Es kann ein paar Minuten dauern, bis Ihr AKS-Cluster aktualisiert wird, abhängig von den Knoteneinstellungen und Vorgängen, die Sie in Ihrer Resource Manager-Vorlage definieren.

## <a name="assign-a-public-ip-per-node-for-your-node-pools-preview"></a>Zuweisen einer öffentlichen IP-Adresse pro Knoten in Ihren Knotenpools (Vorschau)

> [!WARNING]
> Sie müssen die Erweiterung 0.4.43 oder höher der CLI-Vorschauversion installieren, um das Feature „Öffentliche IP-Adresse pro Knoten“ verwenden zu können.

AKS-Knoten benötigen keine eigene öffentliche IP-Adresse für die Kommunikation. In einigen Szenarien müssen Knoten in einem Knotenpool jedoch möglicherweise jeweils eine eigene dedizierte öffentliche IP-Adresse erhalten. Ein häufiges Szenario hierfür sind Gamingworkloads, bei denen eine Konsole eine direkte Verbindung mit einem virtuellen Cloudcomputer herstellen muss, um Hops zu minimieren. Dieses Szenario kann in AKS erreicht werden, indem Sie sich für eine Previewfunktion für öffentliche IP-Adressen für Knoten (Vorschau) registrieren.

Um die neueste Erweiterung von aks-preview zu installieren und zu aktualisieren, verwenden Sie die folgenden Azure CLI-Befehle:

```azurecli
az extension add --name aks-preview
az extension update --name aks-preview
az extension list
```

Registrieren Sie sich für das Feature für öffentliche IP-Adressen von Knoten über den folgenden Azure CLI-Befehl:

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```
Die Registrierung des Features kann einige Minuten dauern.  Sie können den Status mit dem folgenden Befehl überprüfen:

```azurecli-interactive
 az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/NodePublicIPPreview')].{Name:name,State:properties.state}"
```

Erstellen Sie nach erfolgreicher Registrierung eine neue Ressourcengruppe.

```azurecli-interactive
az group create --name myResourceGroup2 --location eastus
```

Erstellen Sie einen neuen AKS-Cluster, und fügen Sie an Ihre Knoten eine öffentliche IP-Adresse an. Jeder Knoten im Knotenpool erhält eine eindeutige öffentliche IP-Adresse. Sie können dies überprüfen, indem Sie sich die VM-Skalierungsgruppeninstanzen ansehen.

```azurecli-interactive
az aks create -g MyResourceGroup2 -n MyManagedCluster -l eastus  --enable-node-public-ip
```

Bei vorhandenen AKS-Clustern können Sie auch einen neuen Knotenpool hinzufügen und eine öffentliche IP-Adresse an Ihre Knoten anfügen.

```azurecli-interactive
az aks nodepool add -g MyResourceGroup2 --cluster-name MyManagedCluster -n nodepool2 --enable-node-public-ip
```

> [!Important]
> In der Vorschauphase unterstützt der Azure Instance Metadata Service derzeit nicht das Abrufen öffentlicher IP-Adressen für die VM-SKU im Standardtarif. Aufgrund dieser Einschränkung können Sie nicht über kubectl-Befehle die den Knoten zugewiesenen öffentlichen IP-Adressen anzeigen. Die IP-Adressen sind jedoch zugewiesen und funktionieren wie vorgesehen. Die öffentlichen IP-Adressen Ihrer Knoten werden an die Instanzen in Ihrer VM-Skalierungsgruppe angefügt.

Sie können die öffentlichen IP-Adressen Ihrer Knoten auf verschiedene Weise ermitteln:

* Mit dem Azure CLI-Befehl [az vmss list-instance-public-ips][az-list-ips]
* Mit [PowerShell- oder Bash-Befehlen][vmss-commands] 
* Sie können die öffentlichen IP-Adressen auch im Azure-Portal anzeigen, indem Sie die Instanzen in der VM-Skalierungsgruppe einsehen.

> [!Important]
> Die [Knotenressourcengruppe][node-resource-group] enthält die Knoten und deren öffentlichen IP-Adressen. Verwenden Sie die Knotenressourcengruppe, wenn Sie Befehle ausführen, um die öffentlichen IP-Adressen Ihrer Knoten zu ermitteln.

```azurecli
az vmss list-instance-public-ips -g MC_MyResourceGroup2_MyManagedCluster_eastus -n YourVirtualMachineScaleSetName
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In diesem Artikel haben Sie einen AKS-Cluster erstellt, der GPU-basierte Knoten enthält. Um unnötige Kosten zu reduzieren, können Sie *gpunodepool* oder den gesamten AKS-Cluster löschen.

Verwenden Sie zum Löschen des GPU-basierten Knotenpools den Befehl [az aks nodepool delete][az-aks-nodepool-delete], wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Wenn Sie den Cluster selbst löschen möchten, verwenden Sie den Befehl [az group delete][az-group-delete], um die AKS-Ressourcengruppe zu löschen:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

Sie können auch den zusätzlichen Cluster löschen, den Sie für das Szenario für öffentliche IP-Adressen für Knotenpools erstellt haben.

```azurecli-interactive
az group delete --name myResourceGroup2 --yes --no-wait
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu [Systemknotenpools][use-system-pool].

In diesem Artikel haben Sie erfahren, wie Sie mehrere Knotenpools in einem AKS-Cluster erstellen und verwalten. Weitere Informationen zum knotenpoolübergreifenden Steuern von Pods finden Sie unter [Best Practices für erweiterte Scheduler-Funktionen in Azure Kubernetes Service (AKS)][operator-best-practices-advanced-scheduler].

Informationen zum Erstellen und Verwenden von Windows Server-Containerknotenpools finden Sie unter [Erstellen eines Windows Server-Containers in AKS][aks-windows].

Verwenden Sie [Näherungsplatzierungsgruppe][reduce-latency-ppg], um die Latenz für Ihre AKS-Anwendungen zu verringern.

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-upgrades]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-upgrades
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-system-pool]: use-system-pools.md
[ip-limitations]: ../virtual-network/virtual-network-ip-addresses-overview-arm#standard
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[vmss-commands]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine
[az-list-ips]: /cli/azure/vmss?view=azure-cli-latest.md#az-vmss-list-instance-public-ips
[reduce-latency-ppg]: reduce-latency-ppg.md
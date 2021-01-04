---
title: Clusterkonfiguration in Azure Kubernetes Services (AKS)
description: Erfahren Sie, wie Sie in Azure Kubernetes Service (AKS) einen Cluster konfigurieren.
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 439714f8fe4a6373f2ffce80e744802dd19b67f0
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928782"
---
# <a name="configure-an-aks-cluster"></a>Konfigurieren eines AKS-Clusters

Im Rahmen der Erstellung eines AKS-Clusters müssen Sie möglicherweise Ihre Clusterkonfiguration an Ihre Anforderungen anpassen. In diesem Artikel werden einige Optionen zur Anpassung Ihres AKS-Clusters vorgestellt.

## <a name="os-configuration"></a>Betriebssystemkonfiguration

AKS unterstützt jetzt Ubuntu 18.04 als Knotenbetriebssystem (OS) in allgemeiner Verfügbarkeit für Cluster in Kubernetes-Versionen, die höher als 1.18.8 sind. Für Versionen unter 1.18.x ist AKS Ubuntu 16.04 weiterhin das Standardbasisimage. Ab Kubernetes v1.18.x ist die Standardbasis AKS Ubuntu 18.04.

> [!IMPORTANT]
> Knotenpools, die unter Kubernetes 1.18 oder höher erstellt wurden, verwenden standardmäßig ein `AKS Ubuntu 18.04`-Knotenimage. Knotenpools auf einer unterstützten Kubernetes-Version vor 1.18 erhalten `AKS Ubuntu 16.04` als Knotenimage, werden jedoch auf `AKS Ubuntu 18.04` aktualisiert, sobald die Kubernetes-Version des Knotenpools auf 1.18 oder höher aktualisiert wird.
> 
> Es wird dringend empfohlen, Ihre Workloads auf AKS Ubuntu 18.04-Knotenpools zu testen, bevor Sie Cluster auf 1.18 oder höher verwenden. Informieren Sie sich über das [Testen von Ubuntu 18.04-Knotenpools](#use-aks-ubuntu-1804-existing-clusters-preview).

Im folgenden Abschnitt wird erläutert, wie Sie AKS Ubuntu 18.04 auf Clustern, die noch keine Kubernetes-Version 1.18.x oder höher verwenden oder die erstellt wurden, bevor diese Funktion allgemein verfügbar wurde, mithilfe der Vorschau der Betriebssystemkonfiguration verwenden und testen.

Die folgenden Ressourcen müssen installiert sein:

- [Azure CLI][azure-cli-install] ab Version 2.2.0
- Die Erweiterung aks-preview 0.4.35

Verwenden Sie zum Installieren der Erweiterung aks-preview 0.4.35 oder höher die folgenden Azure CLI-Befehle:

```azurecli
az extension add --name aks-preview
az extension list
```

Registrieren Sie das Feature `UseCustomizedUbuntuPreview`:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Es kann einige Minuten dauern, bis der Status als **Registriert** angezeigt wird. Sie können den Registrierungsstatus mithilfe des Befehls [az feature list](/cli/azure/feature#az-feature-list) überprüfen:

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Wenn der Status als registriert angezeigt wird, können Sie die Registrierung des `Microsoft.ContainerService`-Ressourcenanbieters mit dem Befehl [az provider register](/cli/azure/provider#az-provider-register) aktualisieren:

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### <a name="use-aks-ubuntu-1804-on-new-clusters-preview"></a>Verwenden von AKS Ubuntu 18.04 auf neuen Clustern (Vorschau)

Konfigurieren Sie den Cluster für die Verwendung von Ubuntu 18,04, wenn der Cluster erstellt wird. Verwenden Sie das Flag `--aks-custom-headers`, um Ubuntu 18.04 als Standardbetriebssystem festzulegen.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Wenn Sie Cluster mit dem AKS Ubuntu 16.04-Image erstellen möchten, lassen Sie dazu das benutzerdefinierte Tag `--aks-custom-headers` weg.

### <a name="use-aks-ubuntu-1804-existing-clusters-preview"></a>Verwenden von AKS Ubuntu 18.04 auf vorhandenen Clustern (Vorschau)

Konfigurieren Sie einen neuen Knotenpool zur Verwendung von Ubuntu 18.04. Verwenden Sie das Flag `--aks-custom-headers`, um Ubuntu 18.04 als Standardbetriebssystem für diesen Knotenpool festzulegen.

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Wenn Sie Knotenpools mit dem AKS Ubuntu 16.04-Image erstellen möchten, lassen Sie dazu das benutzerdefinierte Tag `--aks-custom-headers` weg.

## <a name="container-runtime-configuration"></a>Konfiguration der Containerruntime

Eine Containerruntime ist eine Software, die Container ausführt und Containerimages auf einem Knoten verwaltet. Die Runtime erleichtert die Abstraktion von sys-Aufrufen oder betriebssystemspezifischen Funktionen zum Ausführen von Containern unter Linux oder Windows. AKS-Cluster, die Knotenpools mit Version 1.19 und höheren Versionen von Kubernetes verwenden, nutzen `containerd` als Containerruntime. AKS-Cluster, die Knotenpools mit älteren Kubernetes-Versionen als 1.19 verwenden, nutzen [Moby](https://mobyproject.org/) (Upstream-Docker) als Containerruntime.

![Docker CRI 1](media/cluster-configuration/docker-cri.png)

[`Containerd`](https://containerd.io/) ist eine mit [OCI](https://opencontainers.org/) (Open Container Initiative) kompatible Kerncontainerruntime, die den Mindestsatz erforderlicher Funktionen zum Ausführen von Containern und zum Verwalten von Images auf einem Knoten bereitstellt. Sie wurde im März 2017 an die Cloud Native Compute Foundation (CNCF) [übergeben](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/). Die aktuelle Moby-Version, die AKS verwendet, baut bereits auf `containerd` auf, wie oben dargestellt.

Bei Knoten und Knotenpools, die auf `containerd` basieren, kommuniziert das Kubelet statt mit `dockershim` direkt über das CRI-Plug-In (Container Runtime Interface) mit `containerd`. Gegenüber der Docker CRI-Implementierung benötigt der Flow so weniger Hops. Daraus ergibt sich eine verringerte Latenz beim Podstart und eine geringere Ressourcenauslastung (CPU und Arbeitsspeicher).

Durch die Verwendung von `containerd` für AKS-Knoten werden die Podstartlatenz und der Ressourcenverbrauch durch die Containerlaufzeit auf dem Knoten verringert. Diese Verbesserungen sind eine Folge der neuen Architektur, bei der das Kubelet direkt über das CRI-Plug-In mit `containerd` kommuniziert. Bei der Moby/Docker-Architektur kommuniziert das Kubelet hingegen mit `dockershim` und der Docker-Engine, bevor `containerd` erreicht wird, was zu zusätzlichen Hops im Flow führt.

![Docker CRI 2](media/cluster-configuration/containerd-cri.png)

`Containerd` funktioniert mit jeder allgemein verfügbaren Version von Kubernetes in AKS und mit jeder Upstream-Kubernetes-Version ab 1.19 und unterstützt alle Features von Kubernetes und AKS.

> [!IMPORTANT]
> Cluster mit Knotenpools, die unter Kubernetes 1.19 oder höher erstellt wurden, verwenden standardmäßig `containerd` für die Containerruntime. Cluster mit Knotenpools unter einer unterstützten Kubernetes-Version vor 1.19 erhalten `Moby` als Containerruntime, werden jedoch auf `ContainerD` aktualisiert, sobald die Kubernetes-Version des Knotenpools auf 1.19 oder höher aktualisiert wird. Sie können `Moby`-Knotenpools und -Cluster unter älteren unterstützten Versionen verwenden, solange diese noch unterstützt werden.
> 
> Es wird dringend empfohlen, Ihre Workloads in AKS-Knotenpools mit `containerD` zu testen, bevor Sie Cluster unter 1.19 oder höher verwenden.

Im folgenden Abschnitt wird erläutert, wie Sie AKS mit `containerD` in Clustern, die noch nicht Kubernetes-Version 1.19 oder höher verwenden oder die erstellt wurden, bevor diese Funktion allgemein verfügbar wurde, mithilfe der Vorschau der Containerruntimekonfiguration verwenden und testen.

### <a name="use-containerd-as-your-container-runtime-preview"></a>Verwenden von `containerd` als Containerruntime (Vorschau)

Die folgenden Voraussetzungen müssen erfüllt sein:

- Installation der [Azure CLI][azure-cli-install] ab Version 2.8.0
- aks-preview-Erweiterung ab Version 0.4.53
- Registrierung des `UseCustomizedContainerRuntime`-Featureflags
- Registrierung des `UseCustomizedUbuntuPreview`-Featureflags

Verwenden Sie zum Installieren von Version 0.4.53 oder höher der aks-preview-Erweiterung die folgenden Azure CLI-Befehle:

```azurecli
az extension add --name aks-preview
az extension list
```

Registrieren Sie die Features `UseCustomizedContainerRuntime` und `UseCustomizedUbuntuPreview`:

```azurecli
az feature register --name UseCustomizedContainerRuntime --namespace Microsoft.ContainerService
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService

```

Es kann einige Minuten dauern, bis der Status als **Registriert** angezeigt wird. Sie können den Registrierungsstatus mithilfe des Befehls [az feature list](/cli/azure/feature#az-feature-list) überprüfen:

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedContainerRuntime')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Wenn der Status als registriert angezeigt wird, können Sie die Registrierung des `Microsoft.ContainerService`-Ressourcenanbieters mit dem Befehl [az provider register](/cli/azure/provider#az-provider-register) aktualisieren:

```azurecli
az provider register --namespace Microsoft.ContainerService
```  

### <a name="use-containerd-on-new-clusters-preview"></a>Verwenden von `containerd` in neuen Clustern (Vorschau)

Konfigurieren Sie den Cluster bei der Erstellen so, dass `containerd` verwendet wird. Legen Sie die Containerruntime mit dem Flag `--aks-custom-headers` auf `containerd` fest.

> [!NOTE]
> Die `containerd`-Runtime wird nur für Knoten und Knotenpools unterstützt, die das AKS Ubuntu 18.04-Image verwenden.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

Wenn Sie Cluster mit der Moby-(Docker-)Runtime erstellen möchten, lassen Sie das benutzerdefinierte Tag `--aks-custom-headers` weg.

### <a name="use-containerd-on-existing-clusters-preview"></a>Verwenden von `containerd` in vorhandenen Clustern (Vorschau)

Konfigurieren Sie einen neuen Knotenpool so, dass er `containerd` verwendet. Legen Sie die Runtime für den Knotenpool mit dem Flag `--aks-custom-headers` auf `containerd` fest.

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

Wenn Sie Knotenpools mit der Moby-(Docker-)Runtime erstellen möchten, lassen Sie das benutzerdefinierte Tag `--aks-custom-headers` weg.


### <a name="containerd-limitationsdifferences"></a>Einschränkungen und Unterschiede von `Containerd`

* Wenn Sie `containerd` als Containerruntime verwenden möchten, müssen Sie AKS Ubuntu 18.04 als Betriebssystem-Basisimage verwenden.
* Obwohl das Docker-Toolset weiterhin auf den Knoten vorhanden ist, verwendet Kubernetes `containerd` als Containerruntime. Da Moby/Docker mit Kubernetes erstellte Container auf den Knoten nicht verwaltet, ist es nicht möglich, die Container mithilfe von Docker-Befehlen (wie `docker ps`) oder der Docker-API anzuzeigen oder mit ihnen zu interagieren.
* Bei `containerd` wird empfohlen, [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) anstelle der Docker-CLI als Ersatz-CLI für die **Problembehandlung** bei Pods, Containern und Containerimages auf Kubernetes-Knoten zu verwenden (z. B. `crictl ps`). 
   * Diese unterstützt nicht alle Funktionen der Docker-CLI. Sie ist nur für die Problembehandlung gedacht.
   * `crictl` bietet eine für Kubernetes besser geeignete Ansicht von Containern mit Unterstützung von Konzepten wie Pods.
* Bei `Containerd` wird die Protokollierung im standardisierten `cri`-Protokollierungsformat eingerichtet. (Dieses unterscheidet sich vom Format des aktuellen Docker-JSON-Treibers.) Ihre Protokollierungslösung muss das `cri`-Protokollierungsformat unterstützen (wie z. B. [Azure Monitor für Container](../azure-monitor/insights/container-insights-enable-new-cluster.md)).
* Sie können nicht mehr auf die Docker-Engine `/var/run/docker.sock` zugreifen oder Docker-in-Docker (DinD) verwenden.
  * Wenn Sie zurzeit Anwendungsprotokolle oder Überwachungsdaten aus der Docker-Engine extrahieren, verwenden Sie stattdessen z. B. [Azure Monitor für Container](../azure-monitor/insights/container-insights-enable-new-cluster.md). Darüber hinaus unterstützt AKS keine Ausführung von Out-of-Band-Befehlen auf den Agent-Knoten, die zu einer Instabilität führen könnten.
  * Selbst bei Verwendung von Moby/Docker wird dringend davon abgeraten, direkt über die oben genannten Methoden Images zu erstellen und die Docker-Engine zu nutzen. Kubernetes erkennt diese genutzten Ressourcen nicht vollständig, und diese Ansätze bringen zahlreiche Probleme mit sich, die z. B. [hier](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) und [hier](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/) erläutert werden.
* Erstellen von Images: Die empfohlene Vorgehensweise zum Erstellen von Images besteht in der Verwendung von [ACR-Aufgaben](../container-registry/container-registry-quickstart-task-cli.md). Ein alternativer Ansatz besteht darin, sicherere clusterinterne Optionen wie [docker buildx](https://github.com/docker/buildx) zu verwenden.

## <a name="generation-2-virtual-machines-preview"></a>Virtuelle Computer der Generation 2 (Vorschau)

Azure unterstützt [virtuelle Computer (VMs) der Generation 2 (Gen2)](../virtual-machines/generation-2.md). VMs der Generation 2 unterstützen wichtige Features, die bei VMs der Generation 1 (Gen1) nicht unterstützt werden. Zu diesen Features gehören mehr Speicher, Intel Software Guard Extensions (Intel SGX) und virtualisierter persistenter Speicher (vPMEM).

VMs der Generation 2 verwenden die neue UEFI-basierte Startarchitektur und nicht mehr die BIOS-basierte Architektur von VMs der Generation 1.
Nur bestimmte SKUs und Größen unterstützen Gen2-VMs. Überprüfen Sie die [Liste der unterstützten Größen](../virtual-machines/generation-2.md#generation-2-vm-sizes), um festzustellen, ob Ihre SKU Gen2 unterstützt oder erfordert.

Zusätzlich unterstützen nicht alle VM-Images Gen2. Auf AKS-Gen2 wird für VMs das neue [AKS Ubuntu 18.04-Image](#os-configuration) verwendet. Dieses Image unterstützt alle Gen2-SKUs und -Größen.

Um Gen2-VMs während der Vorschauphase verwenden zu können, benötigen Sie Folgendes:
- Installation der `aks-preview`-CLI-Erweiterung
- Registrierung des `Gen2VMPreview`-Featureflags

Registrieren Sie das Feature `Gen2VMPreview`:

```azurecli
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```

Es kann einige Minuten dauern, bis der Status als **Registriert** angezeigt wird. Sie können den Registrierungsstatus mithilfe des Befehls [az feature list](/cli/azure/feature#az-feature-list) überprüfen:

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```

Wenn der Status als registriert angezeigt wird, können Sie die Registrierung des `Microsoft.ContainerService`-Ressourcenanbieters mit dem Befehl [az provider register](/cli/azure/provider#az-provider-register) aktualisieren:

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Verwenden Sie zum Installieren der aks-preview-CLI-Erweiterung die folgenden Azure CLI-Befehle:

```azurecli
az extension add --name aks-preview
```

Verwenden Sie zum Aktualisieren der aks-preview-CLI-Erweiterung die folgenden Azure CLI-Befehle:

```azurecli
az extension update --name aks-preview
```

### <a name="use-gen2-vms-on-new-clusters-preview"></a>Verwenden von Gen2-VMs auf neuen Clustern (Vorschau)
Konfigurieren Sie Gen2-VMs auf dem Cluster für die ausgewählte SKU, wenn der Cluster erstellt wird. Legen Sie Gen2 mit dem Flag `--aks-custom-headers` als VM-Generation auf einem neuen Cluster fest.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Wenn Sie einen herkömmlichen Cluster mit Generation 1-VMs (Gen1) erstellen möchten, lassen Sie das benutzerdefinierte Tag `--aks-custom-headers` weg. Sie können auch weitere Gen1- oder Gen2-VMs wie weiter unten beschrieben hinzufügen.

### <a name="use-gen2-vms-on-existing-clusters-preview"></a>Verwenden von Gen2-VMs auf vorhandenen Clustern (Vorschau)
Konfigurieren Sie Gen2-VMs auf einem neuen Knotenpool. Legen Sie Gen2 mit dem Flag `--aks-custom-headers` als VM-Generation für diesen Knotenpool fest.

```azurecli
az aks nodepool add --name gen2 --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Wenn Sie einen herkömmlichen Gen1-Knotenpool erstellen möchten, lassen Sie das benutzerdefinierte Tag `--aks-custom-headers` weg.


## <a name="ephemeral-os"></a>Kurzlebiges Betriebssystem

Standardmäßig repliziert Azure den Betriebssystemdatenträger automatisch für einen virtuellen Computer in Azure Storage, um Datenverluste zu vermeiden, wenn der virtuelle Computer auf einen anderen Host verschoben werden muss. Da Container jedoch nicht für die Speicherung des lokalen Zustands vorgesehen sind, hat dieses Verhalten einen begrenzten Nutzen und einige Nachteile wie etwa eine langsamere Knotenbereitstellung und eine höhere Wartezeit bei Lese-/Schreibvorgängen.

Im Gegensatz dazu werden kurzlebige Betriebssystemdatenträger genau wie ein temporärer Datenträger nur auf dem Hostcomputer gespeichert. Dies führt zu einer geringeren Wartezeit bei Lese-/Schreibvorgängen und ermöglicht eine schnellere Knotenskalierung sowie schnellere Clusterupgrades.

Ein kurzlebiger Betriebssystemdatenträger ist genau wie der temporäre Datenträger im Preis des virtuellen Computers enthalten. Es entstehen also keine zusätzlichen Speicherkosten.

> [!IMPORTANT]
>Wenn ein Benutzer nicht explizit verwaltete Datenträger für das Betriebssystem anfordert, verwendet AKS standardmäßig nach Möglichkeit das kurzlebige Betriebssystem für eine bestimmte Knotenpoolkonfiguration.

Wenn das kurzlebige Betriebssystem verwendet wird, muss der Betriebssystemdatenträger in den VM-Cache passen. Die Größen für den VM-Cache sind in der [Azure-Dokumentation](../virtual-machines/dv3-dsv3-series.md) in Klammern neben dem E/A-Durchsatz („Cachegröße in GiB“) aufgeführt.

Beispiel: Standard_DS2_v2, die Standard-VM-Größe für AKS mit der standardmäßigen Betriebssystemdatenträger-Größe von 100 GB unterstützt das kurzlebige Betriebssystem, hat aber nur eine Cachegröße von 86 GB. Diese Konfiguration ist standardmäßig auf verwaltete Datenträger festgelegt, wenn der Benutzer nicht explizit etwas anderes angibt. Wenn ein Benutzer das kurzlebige Betriebssystem explizit angefordert hat, erhält er eine Validierungsfehlermeldung.

Wenn ein Benutzer dieselbe Größe Standard_DS2_v2 mit einem 60-GB-Betriebssystemdatenträger anfordert, würde diese Konfiguration standardmäßig das kurzlebige Betriebssystem unterstützen: die angeforderte Größe von 60 GB liegt unter der maximalen Cachegröße von 86 GB.

Wenn Sie Standard_D8s_v3 mit 100-GB-Betriebssystemdatenträger verwenden, unterstützt diese VM-Größe das kurzlebige Betriebssystem und hat 200 GB Cachespeicher. Wenn ein Benutzer den Betriebssystemdatenträger-Typ nicht angibt, erhält der Knotenpool standardmäßig das kurzlebige Betriebssystem. 

Ein kurzlebiges Betriebssystem erfordert mindestens Version 2.15.0 der Azure CLI.

### <a name="use-ephemeral-os-on-new-clusters"></a>Verwenden eines kurzlebigen Betriebssystems in neuen Clustern

Konfigurieren Sie den Cluster für die Verwendung kurzlebiger Betriebssystemdatenträger bei der Clustererstellung. Verwenden Sie das Flag `--node-osdisk-type`, um die Art des Betriebssystemdatenträgers für den neuen Cluster auf ein kurzlebiges Betriebssystem festzulegen.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

Wenn Sie einen herkömmlichen Cluster mit netzwerkbasierten Betriebssystemdatenträgern erstellen möchten, geben Sie hierzu `--node-osdisk-type=Managed` an. Sie können auch weitere kurzlebige Betriebssystemknotenpools hinzufügen, wie im Anschluss beschrieben.

### <a name="use-ephemeral-os-on-existing-clusters"></a>Verwenden eines kurzlebigen Betriebssystems in vorhandenen Clustern
Konfigurieren Sie einen neuen Knotenpool für die Verwendung kurzlebiger Betriebssystemdatenträger. Verwenden Sie das Flag `--node-osdisk-type`, um die Art des Betriebssystemdatenträgers für diesen Knotenpool festzulegen.

```azurecli
az aks nodepool add --name ephemeral --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

> [!IMPORTANT]
> Mit einem kurzlebigen Betriebssystem können Sie VM- und Instanzimages bis zur Größe des VM-Caches bereitstellen. Im Fall von AKS werden in der Betriebssystemdatenträger-Konfiguration des Standardknotens 128 GB verwendet. Sie benötigen also eine VM-Größe, deren Cache größer als 128 GB ist. Die Cachegröße von Standard_DS2_v2 beträgt standardmäßig 86 GB und ist somit nicht ausreichend. Standard_DS3_v2 weist eine Cachegröße von 172 GB auf und ist damit groß genug. Sie können auch `--node-osdisk-size` verwenden, um die Standardgröße des Betriebssystemdatenträgers zu verringern. Die Mindestgröße für AKS-Images beträgt 30 GB. 

Wenn Sie Knotenpools mit netzwerkbasierten Betriebssystemdatenträgern erstellen möchten, geben Sie `--node-osdisk-type Managed` an.

## <a name="custom-resource-group-name"></a>Name der benutzerdefinierten Ressourcengruppe

Wenn Sie in Azure einen Azure Kubernetes Service-Cluster bereitstellen, wird für die Workerknoten eine zweite Ressourcengruppe erstellt. In AKS wird der Knotenressourcengruppe standardmäßig der Name `MC_resourcegroupname_clustername_location` zugewiesen. Sie können jedoch auch einen eigenen Namen angeben.

Installieren Sie die aks-preview-Erweiterungsversion 0.3.2 oder höher der Azure CLI, wenn Sie einen eigenen Ressourcengruppennamen angeben möchten. Verwenden Sie in der Azure CLI den Parameter `--node-resource-group` des Befehls `az aks create`, um einen benutzerdefinierten Namen für die Ressourcengruppe anzugeben. Wenn Sie eine Azure Resource Manager-Vorlage verwenden, um einen AKS-Cluster bereitzustellen, können Sie die `nodeResourceGroup`-Eigenschaft verwenden, um den Namen der Ressourcengruppe zu definieren.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Die sekundäre Ressourcengruppe wird automatisch vom Azure-Ressourcenanbieter in Ihrem eigenen Abonnement erstellt. Sie können einen benutzerdefinierten Namen für die Ressourcengruppe nur dann angeben, wenn der Cluster erstellt wird. 

Denken Sie bei der Arbeit mit der Knotenressourcengruppe daran, dass Folgendes nicht möglich ist:

- Angeben einer vorhandenen Ressourcengruppe als Knotenressourcengruppe
- Angeben eines anderen Abonnements für die Knotenressourcengruppe
- Ändern des Namens der Knotenressourcengruppe, nachdem der Cluster erstellt wurde
- Angeben von Namen für die verwalteten Ressourcen innerhalb der Knotenressourcengruppe
- Ändern oder Löschen von Azure erstellter Tags für verwaltete Ressourcen innerhalb der Knotenressourcengruppe

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie ein [Upgrade der Knotenimages](node-image-upgrade.md) in Ihrem Cluster durchführen.
- Unter [Upgrade eines Azure Kubernetes Service-Clusters (AKS)](upgrade-cluster.md) erfahren Sie, wie Sie Ihren Cluster auf die neueste Version von Kubernetes aktualisieren können.
- Weitere Informationen zu [`containerd` und Kubernetes](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/)
- In der Liste der [Häufig gestellten Fragen zu AKS](faq.md) finden Sie Antworten auf einige häufig gestellte Fragen zu AKS.
- Weitere Informationen zu kurzlebigen Betriebssystemdatenträgern finden Sie [hier](../virtual-machines/ephemeral-os-disks.md).


<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

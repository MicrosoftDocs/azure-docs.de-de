---
title: 'Verwenden eines dedizierten Pools zum Ausführen von Tasks: Tasks'
description: Richten Sie einen dedizierten Computepool (Agentpool) in Ihrer Registrierung ein, um einen Azure Container Registry-Task auszuführen.
ms.topic: article
ms.date: 10/12/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 94956af14aad2b62e6455f443329bcd3232095c0
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844913"
---
# <a name="run-an-acr-task-on-a-dedicated-agent-pool"></a>Ausführen eines ACR Tasks mit einem dedizierten Agentpool

Richten Sie einen von Azure verwalteten VM-Pool (*Agentpool*) ein, um die Ausführung Ihrer [Azure Container Registry-Tasks][acr-tasks] in einer dedizierten Computeumgebung zu ermöglichen. Nachdem Sie mindestens einen Pool in der Registrierung konfiguriert haben, können Sie einen Pool auswählen, um einen Task anstelle der standardmäßigen Computeumgebung des Diensts auszuführen.

Ein Agentpool bietet Folgendes:

- **Unterstützung für virtuelle Netzwerke**: Weisen Sie einen Agentpool einem Azure-VNET zu, und stellen Sie so Zugriff auf Ressourcen im VNET bereit, z. B. auf eine Containerregistrierung, einen Schlüsseltresor oder auf Speicher.
- **Skalierung nach Bedarf**: Erhöhen Sie die Anzahl der Instanzen in einem Agentpool für rechenintensive Aufgaben, oder skalieren Sie auf null. Die Abrechnung basiert auf der Poolzuordnung. Weitere Informationen finden Sie unter [Preise](https://azure.microsoft.com/pricing/details/container-registry/).
- **Flexible Optionen**: Wählen Sie unter verschiedenen [Pooltarifen](#pool-tiers) und Skalierungsoptionen, um die Anforderungen der Workload für den Task zu erfüllen.
- **Azure-Verwaltungs**: Task-Pools werden von Azure gepatcht und verwaltet und bieten eine reservierte Zuordnung, ohne dass die einzelnen VMs verwaltet werden müssen.

Diese Funktion ist auf der Dienstebene **Premium** der Containerregistrierung verfügbar. Weitere Informationen zu den Tarifen des Registrierungsdiensts und zu den Einschränkungen finden Sie unter [Azure Container Registry-SKUs][acr-tiers].

> [!IMPORTANT]
> Dieses Feature befindet sich derzeit in der Vorschauphase. Es gelten einige [Einschränkungen](#preview-limitations). Vorschauversionen werden Ihnen zur Verfügung gestellt, wenn Sie die [zusätzlichen Nutzungsbedingungen][terms-of-use] akzeptieren. Einige Aspekte dieses Features werden bis zur allgemeinen Verfügbarkeit unter Umständen noch geändert.
>

## <a name="preview-limitations"></a>Einschränkungen der Vorschau

- Taskagentpools unterstützen derzeit Linux-Knoten. Windows-Knoten werden zurzeit nicht unterstützt.
- Taskagentpools sind als Vorschau in den folgenden Regionen verfügbar: „USA, Westen 2“, „USA, Süden-Mitte“, „USA, Osten 2“, „USA, Osten“, „USA, Mitte“, „USGov, Arizona“, „USGov, Texas“ und „USGov, Virginia“.
- Für jede Registrierung hat das standardmäßige vCPU-Kontingent (Core) für alle Standard-Agentpools den Wert 16 und den Wert 0 für isolierte Agentpools. Öffnen Sie eine [Supportanfrage][open-support-ticket], um zusätzliche Zuordnungen zu erhalten.
- Sie können zurzeit keine Aufgaben abbrechen, die in einem Agentpool ausgeführt werden.

## <a name="prerequisites"></a>Voraussetzungen

* Um die in diesem Artikel beschriebenen Schritte mit der Azure CLI ausführen zu können, ist die Azure CLI-Version 2.3.1 oder höher erforderlich. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli]. Alternativ können Sie [Azure Cloud Shell](../cloud-shell/quickstart.md) verwenden.
* Sollten Sie noch nicht über eine Containerregistrierung verfügen, können Sie eine Registrierung in einer Vorschauregion [erstellen][create-reg-cli] (Premium-Tarif erforderlich).

## <a name="pool-tiers"></a>Pooltarife

Agentpooltarife stellen die folgenden Ressourcen pro Instanz im Pool bereit.

|Tarif    | type  |  CPU  |Arbeitsspeicher (GB)  |
|---------|---------|---------|---------|
|S1     |  Standard    | 2       |    3     |
|S2     |  Standard    | 4       |    8     |
|S3     |  Standard    | 8       |   16     |
|I6     |  Isoliert    | 64     |   216     |


## <a name="create-and-manage-a-task-agent-pool"></a>Erstellen und Verwalten eines Taskagentpools

### <a name="set-default-registry-optional"></a>Festlegen der Standardregistrierung (optional)

Wenn Sie die folgenden Befehle der Azure CLI vereinfachen möchten, legen Sie die Standardregistrierung fest, indem Sie den Befehl [az configure][az-configure] ausführen:

```azurecli
az configure --defaults acr=<registryName>
```

In den folgenden Beispielen wird davon ausgegangen, dass Sie die Standardregistrierung festgelegt haben. Wenn dies nicht der Typ ist, übergeben Sie einen `--registry <registryName>`-Parameter in jedem `az acr`-Befehl.

### <a name="create-agent-pool"></a>Erstellen des Agentpools

Erstellen Sie einen Agentpool mithilfe des Befehls [az acr agentpool create][az-acr-agentpool-create]. Im folgenden Beispiel wird ein Pool mit dem Tarif S2 (4 CPUs/Instanz) erstellt. Standardmäßig enthält der Pool eine Instanz.

```azurecli
az acr agentpool create \
    --name myagentpool \
    --tier S2
```

> [!NOTE]
> Das Erstellen eines Agentpools und andere Poolverwaltungsvorgänge nehmen einige Minuten in Anspruch.

### <a name="scale-pool"></a>Skalieren des Pools

Skalieren Sie die Poolgröße mit dem Befehl [az acr agentpool update][az-acr-agentpool-update] zentral hoch oder herunter. Im folgenden Beispiel wird der Pool auf zwei Instanzen skaliert. Sie können auf null Instanzen skalieren.

```azurecli
az acr agentpool update \
    --name myagentpool \
    --count 2
```

## <a name="create-pool-in-a-virtual-network"></a>Erstellen des Pools in einem virtuellen Netzwerk

### <a name="add-firewall-rules"></a>Hinzufügen von Firewallregeln

Taskagentpools benötigen Zugriff auf die folgenden Azure-Dienste. Die folgenden Firewallregeln müssen allen vorhandenen Netzwerksicherheitsgruppen oder benutzerdefinierten Routen hinzugefügt werden.

| Direction | Protocol | `Source`         | Quellport | Destination          | Dest Port | Verwendet    |
|-----------|----------|----------------|-------------|----------------------|-----------|---------|
| Ausgehend  | TCP      | VirtualNetwork | Any         | AzureKeyVault        | 443       | Standard |
| Ausgehend  | TCP      | VirtualNetwork | Any         | Speicher              | 443       | Standard |
| Ausgehend  | TCP      | VirtualNetwork | Any         | EventHub             | 443       | Standard |
| Ausgehend  | TCP      | VirtualNetwork | Any         | AzureActiveDirectory | 443       | Standard |
| Ausgehend  | TCP      | VirtualNetwork | Any         | AzureMonitor         | 443       | Standard |

> [!NOTE]
> Wenn Ihre Tasks zusätzliche Ressourcen aus dem öffentlichen Internet benötigen, fügen Sie die entsprechenden Regeln hinzu. Zusätzliche Regeln sind z. B. erforderlich, wenn Sie einen Docker-Buildtask ausführen, der die Basisimages von Docker Hub pullt oder ein NuGet-Paket wiederherstellt.

### <a name="create-pool-in-vnet"></a>Erstellen des Pools in einem VNET

Im folgenden Beispiel wird ein Agentpool im Subnetz *mysubnet* des Netzwerks *myvnet* erstellt:

```azurecli
# Get the subnet ID
subnetId=$(az network vnet subnet show \
        --resource-grop myresourcegroup \
        --vnet-name myvnet \
        --name mysubnetname \
        --query id --output tsv)

az acr agentpool create \
    --name myagentpool \
    --tier S2 \
    --subnet-id $subnetId
```

## <a name="run-task-on-agent-pool"></a>Ausführen einer Task für einen Agentpool

In den folgenden Beispielen wird veranschaulicht, wie Sie einen Agentpool angeben, wenn Sie einen Task in eine Warteschlange einreihen.

> [!NOTE]
> Um einen Agentpool in einem ACR Task zu verwenden, stellen Sie sicher, dass der Pool mindestens eine Instanz enthält.
>

### <a name="quick-task"></a>Schnelltask

Reihen Sie einen Schnelltask für den Agentpool mit dem Befehl [az acr build][az-acr-build] in die Warteschlange ein, und übergeben Sie den `--agent-pool`-Parameter:

```azurecli
az acr build \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --file Dockerfile \
    https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

### <a name="automatically-triggered-task"></a>Automatisch ausgelöster Task

Erstellen Sie z. B. einen geplanten Task für den Agentpool mit [az acr task create][az-acr-task-create], und übergeben Sie dabei den `--agent-pool`-Parameter.

```azurecli
az acr task create \
    --name mytask \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --schedule "0 21 * * *" \
    --file Dockerfile \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --commit-trigger-enabled false
```

Führen Sie [az acr task run][az-acr-task-run] aus, um das Setup des Tasks zu überprüfen:

```azurecli
az acr task run \
    --name mytask
```

### <a name="query-pool-status"></a>Abfragen des Poolstatus

Führen Sie [az acr agentpool show][az-acr-agentpool-show] aus, um die Anzahl der zurzeit für den Agentpool geplanten Ausführungen zu ermitteln.

```azurecli
az acr agentpool show \
    --name myagentpool \
    --queue-count
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Beispiele für die Erstellung und Wartung von Containerimages in der Cloud finden Sie in der [Tutorialreihe zu ACR Tasks](container-registry-tutorial-quick-task.md).



[acr-tasks]:           container-registry-tasks-overview.md
[acr-tiers]:           container-registry-skus.md
[azure-cli]:           /cli/azure/install-azure-cli
[open-support-ticket]: https://aka.ms/acr/support/create-ticket
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[az-configure]: /cli/azure#az-configure
[az-acr-agentpool-create]: /cli/azure/acr/agentpool#az-acr-agentpool-create
[az-acr-agentpool-update]: /cli/azure/acr/agentpool#az-acr-agentpool-update
[az-acr-agentpool-show]: /cli/azure/acr/agentpool#az-acr-agentpool-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[create-reg-cli]: container-registry-get-started-azure-cli.md

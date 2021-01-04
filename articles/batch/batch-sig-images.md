---
title: Verwenden von Shared Image Gallery zum Erstellen eines benutzerdefinierten Imagepools
description: Benutzerdefinierte Imagepools sind eine effiziente Möglichkeit zum Konfigurieren von Computeknoten, um Ihre Batch-Workloads auszuführen.
ms.topic: conceptual
ms.date: 11/18/2020
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: eb21a9e0d355274142e34fbb5c90a4d293c88ef1
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327303"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-image-pool"></a>Verwenden von Shared Image Gallery zum Erstellen eines benutzerdefinierten Imagepools

Wenn Sie einen Azure Batch-Pool mithilfe der Konfiguration des virtuellen Computers erstellen, geben Sie das Image eines virtuellen Computers (VM) an, das das Betriebssystem für jeden Computeknoten im Pool bereitstellt. Sie können einen Pool von virtuellen Computern mit einem unterstützten Azure Marketplace-Image erstellen, oder Sie erstellen ein benutzerdefiniertes Image aus einem [Shared Image Gallery-Image](../virtual-machines/windows/shared-image-galleries.md).

## <a name="benefits-of-the-shared-image-gallery"></a>Vorteile von Shared Image Gallery

Wenn Sie Shared Image Gallery für Ihr benutzerdefiniertes Image verwenden, haben Sie die Kontrolle über den Typ und die Konfiguration des Betriebssystems sowie über den Typ der Datenträger. Ihr freigegebenes Image kann Anwendungen und Verweisdaten enthalten, die auf allen Knoten des Batch-Pools verfügbar werden, sobald sie bereitgestellt werden.

Sie können auch nach Bedarf mehrere Versionen eines Image für Ihre Umgebung verwenden. Wenn Sie eine Imageversion zum Erstellen eines virtuellen Computers verwenden, wird die Imageversion verwendet, um neue Datenträger für den virtuellen Computer zu erstellen.

Durch die Verwendung eines freigegebenen Image sparen Sie Zeit beim Vorbereiten der Computekonten des Pools auf die Ausführung Ihrer Batch-Workload. Es ist zwar möglich, nach der Bereitstellung ein Azure Marketplace-Image zu verwenden und Software auf jedem Computeknoten zu installieren, aber die Verwendung eines freigegebenen Image ist in der Regel effizienter. Darüber hinaus können Sie mehrere Replikate für das freigegebene Image angeben, sodass Sie beim Erstellen von Pools mit vielen VMs (mehr als 600 VMs) Zeit bei der Poolerstellung sparen.

Die Verwendung eines für Ihr Szenario konfigurierten freigegebenen Image kann mehrere Vorteile bieten:

- **Dieselben Images in den Regionen verwenden**: Sie können freigegebene Imagereplikate in verschiedenen Regionen erstellen, sodass alle Pools dasselbe Image verwenden.
- **Das Betriebssystem (Operating System, OS) konfigurieren**: Sie können die Konfiguration des Betriebssystemdatenträgers des Images anpassen.
- **Anwendungen vorab installieren**: Das Vorinstallieren von Anwendungen auf dem Betriebssystemdatenträger ist effizienter und weniger fehleranfällig als die Installation von Anwendungen nach der Bereitstellung der Computeknoten mit einem Starttask.
- **Große Datenmengen einmalig kopieren**: Sie können statische Daten zu einem Teil des verwalteten freigegebenen Image machen, indem Sie die Daten auf die Datenträger eines verwalteten Image kopieren. Dieser Vorgang muss nur einmal ausgeführt werden und stellt Daten für jeden Knoten des Pools zur Verfügung.
- **Pools auf größere Größen skalieren**: Mit Shared Image Gallery können Sie größere Pools mit Ihren benutzerdefinierten Images zusammen mit weiteren freigegebenen Imagereplikaten erstellen.
- **Bessere Leistung als bei einfacher Verwendung eines verwalteten Images als benutzerdefiniertes Image.** Bei Verwendung eines Shared Image-Pools benutzerdefinierter Images verkürzt sich die Zeit, die der Pool benötigt, um den stabilen Status zu erreichen, um bis zu 25 Prozent, und die Leerlaufzeit des virtuellen Computers verkürzt sich um bis zu 30 Prozent.
- **Versionsverwaltung und Gruppierung von Images zur einfacheren Verwaltung**: Die Imagegruppierungsdefinition enthält Informationen darüber, warum das Image erstellt wurde, für welches Betriebssystem es vorgesehen ist und wie es verwendet wird. Das Gruppieren von Images ermöglicht eine einfachere Imageverwaltung. Weitere Informationen finden Sie unter [Imagedefinitionen](../virtual-machines/windows/shared-image-galleries.md#image-definitions).

## <a name="prerequisites"></a>Voraussetzungen

> [!NOTE]
> Sie müssen sich mit Azure AD authentifizieren. Wenn Sie shared-key-auth verwenden, erhalten Sie einen Authentifizierungsfehler.  

- Ein **Azure Batch-Konto.** Informationen zum Erstellen eines Batch-Kontos im Azure-Portal oder mit der Azure-Befehlszeilenschnittstelle finden Sie in den entsprechenden Batch-Schnellstartanleitungen unter [Schnellstart: Ausführen Ihres ersten Batch-Auftrags im Azure-Portal](quick-create-portal.md) und [Schnellstart: Ausführen Ihres ersten Batch-Auftrags mit der Azure CLI](quick-create-cli.md).

- **Ein Shared Image Gallery-Image**. Zum Erstellen eines freigegebenen Images müssen Sie über eine verwaltete Imageressource verfügen oder eine verwaltete Imageressource erstellen. Das Image sollte auf der Grundlage von Momentaufnahmen des Betriebssystemdatenträgers des virtuellen Computers und optional seiner angefügten Datenträger erstellt werden.

> [!NOTE]
> Wenn das freigegebene Image sich nicht im gleichen Abonnement wie das Batch-Konto befindet, müssen Sie für das Abonnement [den Ressourcenanbieter „Microsoft.Batch“ registrieren](../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider). Beide Abonnements müssen demselben Azure AD-Mandanten zugeordnet sein.
>
> Das Image kann sich in unterschiedlichen Regionen befinden, sofern es über Replikate in derselben Region wie Ihr Batch-Konto verfügt.

Wenn Sie eine Azure AD-Anwendung verwenden, um einen Pool mit benutzerdefinierten Images aus einem Shared Image Gallery-Image zu erstellen, muss dieser Anwendung eine [in Azure integrierte Rolle](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) erteilt worden sein, die ihr Zugriff auf das Shared Image gibt. Sie können diesen Zugriff im Azure-Portal erteilen, indem Sie zum Shared Image navigieren, **Zugriffssteuerung (IAM)** auswählen und eine Rollenzuweisung für die Anwendung hinzufügen.

## <a name="prepare-a-shared-image"></a>Vorbereiten eines Shared Image

In Azure können Sie ein freigegebenes Image aus einem verwalteten Image vorbereiten, das seinerseits aus Folgendem erstellt werden kann:

- Momentaufnahmen des Betriebssystems und der Datenträger eines virtuellen Azure-Computers
- Einen generalisierten virtuellen Azure-Computer mit verwalteten Datenträgern
- Eine generalisierte lokale VHD, die in die Cloud hochgeladen wurde

> [!NOTE]
> Batch unterstützt nur universelle freigegebene Images. Ein freigegebenes Image für einen spezifischen Anwendungsfall kann nicht zum Erstellen eines Pools verwendet werden.

In den folgenden Schritten wird gezeigt, wie Sie einen virtuellen Computer vorbereiten, eine Momentaufnahme erstellen und ein Image auf der Grundlage der Momentaufnahme erstellen.

### <a name="prepare-a-vm"></a>Vorbereiten eines virtuellen Computers

Wenn Sie einen neuen virtuellen Computer für das Image erstellen, können Sie ein von Batch unterstütztes Azure Marketplace-Erstanbieterimage als Basisimage für Ihr verwaltetes Image verwenden. Nur Images von Erstanbietern können als Basisimage verwendet werden. Eine vollständige Liste der von Azure Batch unterstützten Azure Marketplace-Imageverweisen finden Sie im Artikel zum Vorgang zum [Auflisten der Knoten-Agent-SKUs](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus).

> [!NOTE]
> Sie können kein Image von Drittanbietern verwenden, das über zusätzliche Lizenz- und Kaufbedingungen zu Ihrem Basisimage verfügt. Informationen zu diesen Marketplace-Images finden Sie in der Anleitung für [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms)- oder [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms)-VMs.

- Stellen Sie sicher, dass der virtuelle Computer mit einem verwalteten Datenträger erstellt wird. Dies ist die Standardeinstellung für den Speicher, wenn Sie einen virtuellen Computer erstellen.
- Installieren Sie auf dem virtuellen Computer keine Azure-Erweiterungen wie die benutzerdefinierte Skripterweiterung. Wenn das Image eine vorinstallierte Erweiterung enthält, treten in Azure beim Bereitstellen des Batch-Pools unter Umständen Probleme auf.
- Wenn Sie die angefügten Datenträger verwenden, müssen Sie sie innerhalb eines virtuellen Computers einbinden und formatieren, um sie zu verwenden.
- Stellen Sie sicher, dass das von Ihnen bereitgestellte Basisbetriebssystem-Image den standardmäßigen temporären Datenträger verwendet. Der Batch-Knoten-Agent erwartet derzeit den standardmäßigen temporären Datenträger.
- Stellen Sie sicher, dass der Betriebssystemdatenträger nicht verschlüsselt ist.
- Sobald der virtuelle Computer ausgeführt wird, stellen Sie über RDP (für Windows) oder SSH (für Linux) eine Verbindung mit ihm her. Installieren Sie erforderliche Software, oder kopieren Sie die gewünschten Daten.  

### <a name="create-a-vm-snapshot"></a>Erstellen einer Momentaufnahme eines virtuellen Computers

Eine Momentaufnahme ist eine vollständige, schreibgeschützte Kopie einer VHD. Sie können zum Erstellen einer Momentaufnahme des Betriebssystemdatenträgers oder der Datenträger eines virtuellen Computers das Azure-Portal oder Befehlszeilentools verwenden. Schritte und Optionen zum Erstellen einer Momentaufnahme finden Sie in der Anleitung für virtuelle [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)- oder [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)-Computer.

### <a name="create-an-image-from-one-or-more-snapshots"></a>Erstellen eines Images auf der Grundlage von Momentaufnahmen

Verwenden Sie zum Erstellen eines verwalteten Images auf der Grundlage einer Momentaufnahme Azure-Befehlszeilentools, etwa den Befehl [az image create](/cli/azure/image). Erstellen Sie ein Image, indem Sie eine Momentaufnahme des Betriebssystemdatenträgers und optional Momentaufnahmen der Datenträger angeben.

### <a name="create-a-shared-image-gallery"></a>Erstellen einer Shared Image Gallery-Instanz

Nachdem Sie Ihr verwaltetes Image erfolgreich erstellt haben, müssen Sie eine Shared Image Gallery-Instanz erstellen, um Ihr benutzerdefiniertes Image verfügbar zu machen. Weitere Informationen zum Vorbereiten einer Shared Image Gallery-Instanz für Ihre Images finden Sie unter [Erstellen einer Shared Image Gallery-Instanz mit der Azure-Befehlszeilenschnittstelle](../virtual-machines/shared-images-cli.md) und [Erstellen einer Shared Image Gallery-Instanz über das Azure-Portal](../virtual-machines/linux/shared-images-portal.md).

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Erstellen eines Pools aus einem freigegebenen Image mithilfe der Azure-Befehlszeilenschnittstelle

Um mithilfe der Azure-Befehlszeilenschnittstelle einen Pool aus Ihrem freigegebenen Image zu erstellen, verwenden Sie den Befehl `az batch pool create`. Geben Sie im Feld `--image` die ID des freigegebenen Image an. Stellen Sie sicher, dass der Betriebssystemtyp und die SKU mit den von `--node-agent-sku-id` angegebenen Versionen übereinstimmen.

> [!NOTE]
> Sie müssen sich mit Azure AD authentifizieren. Wenn Sie shared-key-auth verwenden, erhalten Sie einen Authentifizierungsfehler.  

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>Erstellen eines Pools aus einem freigegebenen Image mit C#

Alternativ können Sie einen Pool aus einem freigegebenen Image mit dem C# SDK erstellen.

```csharp
private static VirtualMachineConfiguration CreateVirtualMachineConfiguration(ImageReference imageReference)
{
    return new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: "batch.node.windows amd64");
}

private static ImageReference CreateImageReference()
{
    return new ImageReference(
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}");
}

private static void CreateBatchPool(BatchClient batchClient, VirtualMachineConfiguration vmConfiguration)
{
    try
    {
        CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: PoolId,
            targetDedicatedComputeNodes: PoolNodeCount,
            virtualMachineSize: PoolVMSize,
            virtualMachineConfiguration: vmConfiguration);

        pool.Commit();
    }
    ...
}
```

## <a name="create-a-pool-from-a-shared-image-using-python"></a>Erstellen eines Pools aus einem freigegebenen Image mithilfe von Python

Sie können einen Pool aus einem freigegebenen Image auch mit dem Python SDK erstellen: 

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch as batch
import azure.batch.models as batchmodels
from azure.common.credentials import ServicePrincipalCredentials

# Specify Batch account and service principal account credentials
account = "{batch-account-name}"
batch_url = "{batch-account-url}"
ad_client_id = "{sp-client-id}"
ad_tenant = "{tenant-id}"
ad_secret = "{sp-secret}"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_D2_V3"
node_count = 1

# Initialize the Batch client with Azure AD authentication
creds = ServicePrincipalCredentials(
    client_id=ad_client_id,
    secret=ad_secret,
    tenant=ad_tenant,
    resource="https://batch.core.windows.net/"
)
client = batch.BatchServiceClient(creds, batch_url)

# Configure the start task for the pool
start_task = batchmodels.StartTask(
    command_line="printenv AZ_BATCH_NODE_STARTUP_DIR"
)
start_task.run_elevated = True

# Create an ImageReference which specifies the image from
# Shared Image Gallery to install on the nodes.
ir = batchmodels.ImageReference(
    virtual_machine_image_id="/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}"
)

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04"
)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(
    id=pool_id,
    vm_size=vm_size,
    target_dedicated_nodes=node_count,
    virtual_machine_configuration=vmc,
    start_task=start_task
)

# Create pool in the Batch service
client.pool.add(new_pool)
```

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>Erstellen eines Pools auf der Grundlage eines freigegebenen Images über das Azure-Portal

Gehen Sie wie folgt vor, um über das Azure-Portal einen Pool auf der Grundlage eines freigegebenen Images zu erstellen:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Navigieren Sie zu **Batch-Konten**, und wählen Sie Ihr Konto aus.
1. Wählen Sie **Pools** und anschließend **Hinzufügen** aus, um einen neuen Pool zu erstellen.
1. Wählen Sie im Abschnitt **Imagetyp** die Option **Shared Image Gallery** aus.
1. Geben Sie in den restlichen Abschnitten Informationen zu Ihrem verwalteten Image an.
1. Klicken Sie auf **OK**.

![Erstellen Sie einen Pool auf der Grundlage eines freigegebenen Images mithilfe des Portals.](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>Überlegungen zu großen Pools

Wenn Sie beabsichtigen, einen Pool mit Hunderten oder Tausenden von VMs oder mehr mit einem freigegebenen Image zu erstellen, beachten Sie die folgenden Hinweise.

- **Anzahl von Shared Image Gallery-Replikaten**:  Es wird empfohlen, für jeden Pool mit bis zu 300 Instanzen mindestens ein Replikat beizubehalten. Wenn Sie z. B. einen Pool mit 3.000 VMs erstellen, sollten Sie mindestens 10 Replikate Ihres Image beibehalten. Sie sollten immer mehr Replikate beibehalten als in den Mindestanforderungen vorgesehen sind, um eine bessere Leistung zu erzielen.

- **Timeout bei Größenänderung.** Wenn Ihr Pool eine feste Anzahl von Knoten enthält (also nicht automatisch skaliert wird), erhöhen Sie die Eigenschaft `resizeTimeout` des Pools abhängig von der Größe des Pools. Bei jeweils 1000 VMs beträgt das empfohlene Timeout bei Größenänderung mindestens 15 Minuten. Beispielsweise beträgt das empfohlene Timeout bei Größenänderung für einen Pool mit 2000 VMs mindestens 30 Minuten.

## <a name="next-steps"></a>Nächste Schritte

- Eine ausführliche Übersicht über Batch finden Sie unter [Workflow und Ressourcen des Batch-Diensts](batch-service-workflow-features.md).
- Erfahren Sie mehr über das [Erstellen der Shared Image Gallery-Instanz](../virtual-machines/windows/shared-image-galleries.md).

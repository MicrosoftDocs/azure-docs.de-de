---
title: Ausführen von Linux auf virtuellen Computeknoten
description: Erfahren Sie, wie parallele Computeworkloads auf Pools auf virtuellen Linux-Computern in Azure Batch verarbeitet werden.
ms.topic: how-to
ms.date: 11/10/2020
ms.custom: H1Hack27Feb2017, devx-track-python, devx-track-csharp
ms.openlocfilehash: 0a9c801a13af05f077b87f296992da7f50742e4b
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533496"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Bereitstellen von Linux-Computeknoten in Batch-Pools

Sie können Azure Batch verwenden, um Computeworkloads auf virtuellen Linux- und Windows-Computern parallel auszuführen. In diesem Artikel erfahren Sie, wie Sie im Batch-Dienst mithilfe der Clientbibliotheken [Batch Python](https://pypi.python.org/pypi/azure-batch) und [Batch .NET](/dotnet/api/microsoft.azure.batch) Pools mit Linux-Computeknoten erstellen. 

## <a name="virtual-machine-configuration"></a>VM-Konfiguration

Wenn Sie einen Pool von Computeknoten in Batch erstellen, stehen Ihnen zwei Optionen zur Auswahl der Knotengröße und des Betriebssystems zur Verfügung: die Clouddienstkonfiguration und die Konfiguration des virtuellen Computers. Für die meisten Pools mit Windows-Computeknoten wird die [Konfiguration „Cloud Services“](nodes-and-pools.md#cloud-services-configuration) verwendet, die angibt, dass der Pool aus Azure Cloud Services-Knoten besteht. Diese Pools stellen nur Windows-Computeknoten bereit.

Die [VM-Konfiguration](nodes-and-pools.md#virtual-machine-configuration) gibt hingegen an, dass der Pool aus Azure-VMs besteht, die aus Linux- oder Windows-Images erstellt werden können. Wenn Sie einen Pool mit der VM-Konfiguration erstellen, müssen Sie eine [verfügbare Computeknotengröße](../virtual-machines/sizes.md), die VM-Imagereferenz, die SKU des Batch-Knoten-Agents (ein auf jedem Knoten ausgeführtes Programm, das eine Schnittstelle zwischen dem Knoten und dem Batch-Dienst bereitstellt) und die Imagereferenz der VM angeben, die auf den Knoten installiert wird.

### <a name="virtual-machine-image-reference"></a>VM-Imagereferenz

Der Batch-Dienst verwendet [VM-Skalierungsgruppen](../virtual-machine-scale-sets/overview.md), um Computeknoten in der VM-Konfiguration bereitzustellen. Sie können ein Image aus dem [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1) angeben oder [mithilfe der Shared Image Gallery ein benutzerdefiniertes Image vorbereiten](batch-sig-images.md).

Beim Erstellen einer VM-Imagereferenz müssen Sie die folgenden Eigenschaften angeben:

| **Imagereferenzeigenschaft** | **Beispiel** |
| --- | --- |
| Herausgeber |Canonical |
| Angebot |UbuntuServer |
| SKU |18.04-LTS |
| Version |latest |

> [!TIP]
> Weitere Informationen zu diesen Eigenschaften und zum Angeben von Marketplace-Images finden Sie unter [Suchen nach Linux-VM-Images im Azure Marketplace mit der Azure CLI](../virtual-machines/linux/cli-ps-findimage.md). Beachten Sie, dass nicht alle Marketplace-Images derzeit mit Batch kompatibel sind.

### <a name="node-agent-sku"></a>Knoten-Agent-SKU

Der [Batch-Knoten-Agent](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md) ist ein Programm, das auf jedem Knoten im Pool ausgeführt wird. Er stellt die Befehls- und Steuerungsschnittstelle zwischen dem Knoten und dem Batch-Dienst dar. Es gibt verschiedene Implementierungen des Knoten-Agents (SKUs) für verschiedene Betriebssysteme. Insbesondere beim Erstellen einer Konfiguration für einen virtuellen Computer legen Sie zuerst die VM-Imagereferenz und anschließend den Knoten-Agent fest, der auf dem Image installiert werden soll. In der Regel ist jede Knoten-Agent-SKU mit mehreren VM-Images kompatibel. Hier sind einige Beispiele für Knoten-Agent-SKUs:

- batch.node.ubuntu 18.04
- batch.node.centos 7
- batch.node.windows amd64

### <a name="list-of-virtual-machine-images"></a>Liste mit VM-Images

Nicht alle Marketplace-Images sind mit den derzeit verfügbaren Batch-Knoten-Agents kompatibel. Um eine Liste aller unterstützten Marketplace-VM-Images für den Batch-Dienst und die entsprechenden Knoten-Agent-SKUs abzurufen, verwenden Sie [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) (Python), [ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) (Batch.NET) oder die entsprechende API im SDK einer anderen Sprache.

## <a name="create-a-linux-pool-batch-python"></a>Erstellen eines Linux-Pools: Batch Python

Der folgende Codeausschnitt zeigt ein Beispiel für die Erstellung eines Pools mit Ubuntu Server-Computeknoten mithilfe der [Microsoft Azure Batch-Clientbibliothek für Python](https://pypi.python.org/pypi/azure-batch). Weitere Informationen zum Batch-Python-Modul finden Sie in der [Referenzdokumentation](/python/api/overview/azure/batch).

In diesem Ausschnitt erstellen wir explizit einen Imageverweis ([ImageReference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference)) und legen die einzelnen Eigenschaften (Herausgeber, Angebot, SKU, Version) fest. Im Produktionscode sollten Sie jedoch die Methode [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) verwenden, um die verfügbaren Kombinationen aus Image und Knoten-Agent-SKU zur Laufzeit auszuwählen.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_D2_V3"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, batch_url)
client = batch.BatchServiceClient(creds, batch_url)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id=pool_id, vm_size=vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="18.04-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent
# to install on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Wie bereits erwähnt, wird empfohlen, die Methode [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) zu verwenden, um aus den derzeit unterstützten Kombinationen aus Knoten-Agent und Marketplace-Image dynamisch eine Auswahl zu treffen, statt explizit eine [ImageReference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference) zu erstellen. Der folgende Python-Ausschnitt veranschaulicht die Verwendung dieser Methode.

```python
# Get the list of supported images from the Batch service
images = client.account.list_supported_images()

# Obtain the desired image reference
image = None
for img in images:
  if (img.image_reference.publisher.lower() == "canonical" and
        img.image_reference.offer.lower() == "ubuntuserver" and
        img.image_reference.sku.lower() == "18.04-lts"):
    image = img
    break

if image is None:
  raise RuntimeError('invalid image reference for desired configuration')

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=image.image_reference,
    node_agent_sku_id=image.node_agent_sku_id)
```

## <a name="create-a-linux-pool-batch-net"></a>Erstellen eines Linux-Pools: Batch .NET

Der folgende Codeausschnitt zeigt ein Beispiel für die Erstellung eines Pools mit Ubuntu Server-Computeknoten mithilfe der [Batch .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch/)-Clientbibliothek. Weitere Informationen zu Batch .NET finden Sie in der [Referenzdokumentation](/dotnet/api/microsoft.azure.batch).

Im folgenden Codeausschnitt wird die Methode [PoolOperations.ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) verwendet, um aus der Liste mit den derzeit unterstützten Kombinationen von Marketplace-Image und Knoten-Agent-SKU eine Auswahl zu treffen. Diese Vorgehensweise wird empfohlen, da sich die Liste mit den unterstützten Kombinationen von Zeit zu Zeit ändern kann. In den meisten Fällen werden unterstützte Kombinationen hinzugefügt.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_D2_V3";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<ImageInformation> images =
    batchClient.PoolOperations.ListSupportedImages().ToList();

// Find the appropriate image information
ImageInformation image = null;
foreach (var img in images)
{
    if (img.ImageReference.Publisher == "Canonical" &&
        img.ImageReference.Offer == "UbuntuServer" &&
        img.ImageReference.Sku == "18.04-LTS")
    {
        image = img;
        break;
    }
}

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(image.ImageReference, image.NodeAgentSkuId);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicatedComputeNodes: nodeCount);

// Commit the pool to the Batch service
await pool.CommitAsync();
```

Im vorherigen Codeausschnitt wird zwar die Methode [PoolOperations.ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) verwendet, um unterstützte Kombinationen aus Image und Knoten-Agent-SKU dynamisch aufzulisten und eine Auswahl zu treffen (empfohlene Vorgehensweise), Sie können aber auch explizit eine [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) konfigurieren:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

## <a name="connect-to-linux-nodes-using-ssh"></a>Herstellen einer Verbindung mit Linux-Knoten über SSH

Während der Entwicklung oder bei der Fehlerbehebung ist es unter Umständen erforderlich, sich bei den Knoten im Pool anzumelden. Im Gegensatz zu Windows-Computeknoten können Sie das Remotedesktopprotokoll (RDP) nicht für die Herstellung einer Verbindung mit Linux-Knoten verwenden. Stattdessen ermöglicht der Batch-Dienst den SSH-Zugriff auf jeden Knoten zum Herstellen einer Remoteverbindung.

Mit dem folgenden Python-Codeausschnitt wird ein Benutzer für jeden Knoten eines Pools erstellt, der für eine Remoteverbindung erforderlich ist. Anschließend werden die SSH-Verbindungsinformationen (Secure Shell) für die einzelnen Knoten ausgegeben.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
    credentials,
    base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Die Ausgabe dieses Codes sieht in etwa wie im folgenden Beispiel aus. In diesem Fall enthält der Pool vier Linux-Knoten.

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Anstelle eines Kennworts können Sie einen öffentlichen SSH-Schlüssel festlegen, wenn Sie einen Benutzer auf einem Knoten erstellen. Verwenden Sie im Python-SDK den Parameter **ssh_public_key** für [ComputeNodeUser](/python/api/azure-batch/azure.batch.models.computenodeuser). Verwenden Sie in .NET die Eigenschaft [ComputeNodeUser.SshPublicKey](/dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey).

## <a name="pricing"></a>Preise

Azure Batch basiert auf der Technologie von Azure Cloud Services und von Azure Virtual Machines. Der Batch-Dienst selbst wird kostenlos angeboten, d. h. dass Ihnen nur die Computeressourcen (und die damit verbundenen Kosten) in Rechnung gestellt werden, die Ihre Batch-Lösungen beanspruchen. Wenn Sie sich für die **VM-Konfiguration** entscheiden, erfolgt die Abrechnung auf der Grundlage der Preisstruktur unter [Virtuelle Computer – Preise](https://azure.microsoft.com/pricing/details/virtual-machines/).

Wenn Sie Anwendungen mit [Anwendungspaketen](batch-application-packages.md) auf Ihren Batch-Knoten bereitstellen, werden auch die von Ihren Anwendungspaketen genutzten Azure Storage-Ressourcen in Rechnung gestellt.

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die [Python-Codebeispiele](https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch) im GitHub-Repository [azure-batch-samples](https://github.com/Azure/azure-batch-samples) an, um zu erfahren, wie Sie allgemeine Batch-Vorgänge wie die Erstellung von Pools, Aufträgen und Aufgaben ausführen. Die jeweils zu den Python-Beispielen gehörende [INFODATEI](https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md) enthält Details zur Installation der erforderlichen Pakete.
- Informieren Sie sich über die Verwendung von [VMs mit niedriger Priorität](batch-low-pri-vms.md) mit Batch.

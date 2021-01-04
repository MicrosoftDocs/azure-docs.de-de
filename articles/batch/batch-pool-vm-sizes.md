---
title: Auswählen von VM-Größen und Images für Pools
description: 'Vorgehensweise: Auswahl aus den verfügbaren VM-Größen und Betriebssystemversionen für Computeknoten in Azure Batch-Pools'
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: seodec18
ms.openlocfilehash: 8bb54a4db62f56f442f7cec81e6768241a05ffee
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95895229"
---
# <a name="choose-a-vm-size-and-image-for-compute-nodes-in-an-azure-batch-pool"></a>Auswählen einer VM-Größe und eines Images für Computeknoten in einem Azure Batch-Pool

Wenn Sie eine Knotengröße für einen Azure Batch-Pool wählen, können Sie aus fast allen in Azure verfügbaren VM-Größen wählen. Azure bietet eine Reihe von Größen für virtuelle Linux- und Windows-Computer für verschiedene Workloads.

## <a name="supported-vm-series-and-sizes"></a>Unterstützte VM-Serien und -Größen

Bei der Auswahl einer VM-Größe gelten einige Ausnahmen und Einschränkungen für Ihren Batch-Pool:

- Einige VM-Serien oder VM-Größen werden in Batch nicht unterstützt.
- Einige VM-Größen sind eingeschränkt und müssen explizit aktiviert werden, damit sie zugeordnet werden können.

### <a name="pools-in-virtual-machine-configuration"></a>Pools in der Konfiguration des virtuellen Computers

Batch-Pools in der Konfiguration des virtuellen Computers unterstützen nahezu alle [VM-Größen](../virtual-machines/sizes.md). In der folgenden Tabelle finden Sie weitere Informationen zu unterstützten Größen und Einschränkungen.

| VM-Serie  | Unterstützte Größen |
|------------|---------|
| Basic A | Alle Größen *außer* Basic_A0 (A0) |
| Ein | Alle Größen *außer* Standard_A0 |
| Av2 | Alle Größen |
| B | Nicht unterstützt |
| SL | Nicht unterstützt |
| Dv2, DSv2 | Alle Größen |
| Dv3, Dsv3 | Alle Größen |
| Dav4<sup>1</sup> | Alle Größen |
| Dasv4<sup>1</sup> | Alle Größen |
| Ddv4, Ddsv4 |  Alle Größen |
| Dv4, Dsv4 | Nicht unterstützt |
| Ev3, Esv3 | Alle Größen außer E64is_v3 |
| Eav4<sup>1</sup> | Alle Größen |
| Easv4<sup>1</sup> | Alle Größen |
| Edv4, Edsv4 |  Alle Größen |
| Ev4, Esv4 | Nicht unterstützt |
| F, Fs | Alle Größen |
| Fsv2 | Alle Größen |
| G, Gs | Alle Größen |
| H | Alle Größen |
| HB<sup>1</sup> | Alle Größen |
| HBv2<sup>1</sup> | Alle Größen |
| HC<sup>1</sup> | Alle Größen |
| Ls | Alle Größen |
| Lsv2<sup>1</sup> | Alle Größen |
| M<sup>1</sup> | Alle Größen |
| Mv2<sup>1, 2</sup> | Alle Größen |
| NC | Alle Größen |
| NCv2<sup>1</sup> | Alle Größen |
| NCv3<sup>1</sup> | Alle Größen |
| NCasT4_v3 | Keine: Noch nicht verfügbar |
| ND<sup>1</sup> | Alle Größen |
| NDv2<sup>1</sup> | Keine: Noch nicht verfügbar |
| SH | Alle Größen |
| NVv3<sup>1</sup> | Alle Größen |
| NVv4<sup>1</sup> | Alle Größen |
| SAP HANA | Nicht unterstützt |

<sup>1</sup> Diese VM-Serien können bei der Konfiguration des virtuellen Computers in Batch-Pools zugewiesen werden. Sie müssen aber ein neues Batch-Konto erstellen und eine bestimmte [Kontingenterhöhung](batch-quota-limit.md#increase-a-quota) anfordern. Diese Einschränkung wird aufgehoben, sobald das vCPU-Kontingent pro VM-Serie für Batch-Konten vollständig unterstützt wird.

<sup>2</sup> Diese VM-Serien können nur mit VM-Images der zweiten Generation verwendet werden.

### <a name="using-generation-2-vm-images"></a>Verwenden von VM-Images der zweiten Generation

Einige VM-Serien (etwa [Mv2](../virtual-machines/mv2-series.md)) können nur mit [VM-Images der zweiten Generation](../virtual-machines/generation-2.md) verwendet werden. VM-Images der zweiten Generation werden genau wie andere VM-Images unter Verwendung der Eigenschaft „sku“ der Konfiguration [imageReference](/rest/api/batchservice/pool/add#imagereference) angegeben. Die SKU-Zeichenfolgen besitzen ein Suffix wie „-g2“ oder „-gen2“. Eine Liste mit von Batch unterstützten VM-Images (einschließlich Images der zweiten Generation) können Sie über die [API zum Auflisten der unterstützten Images](/rest/api/batchservice/account/listsupportedimages), über [PowerShell](/powershell/module/az.batch/get-azbatchsupportedimage) oder über die [Azure-Befehlszeilenschnittstelle](/cli/azure/batch/pool/supported-images) abrufen.

### <a name="pools-in-cloud-service-configuration"></a>Pools in der Clouddienstkonfiguration

Batch-Pools in der Clouddienstkonfiguration unterstützen alle [VM-Größen für Cloud Services](../cloud-services/cloud-services-sizes-specs.md) **mit Ausnahme** der folgenden:

| VM-Serie  | Nicht unterstützte Größen |
|------------|-------------------|
| A-Serie   | Sehr klein       |
| Av2-Serie | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Überlegungen zu Größen

- **Anwendungsanforderungen**: Berücksichtigen Sie die Merkmale und Anforderungen der Anwendungen, die auf den Knoten ausgeführt werden sollen. Die Beantwortung der Fragen, ob es sich beispielsweise um eine Multithreadanwendung handelt und wie viel Arbeitsspeicher sie beansprucht, kann Ihnen dabei behilflich sein, die am besten geeignete und kostengünstigste Knotengröße zu bestimmen. Ziehen Sie für [MPI-Workloads](batch-mpi.md) mit mehreren Instanzen oder CUDA-Anwendungen spezielle [HPC](../virtual-machines/sizes-hpc.md)- bzw. [GPU-fähige ](../virtual-machines/sizes-gpu.md) VM-Größen in Betracht. Weitere Informationen finden Sie unter [Verwenden RDMA-fähiger oder GPU-fähiger Instanzen in Batch-Pools](batch-pool-compute-intensive-sizes.md).

- **Aufgaben pro Knoten**: Die Knotengröße wird normalerweise unter der Annahme ausgewählt, dass jeweils nur eine Aufgabe auf einem Knoten ausgeführt wird. Es kann jedoch von Vorteil sein, mehrere Aufgaben (und somit mehrere Anwendungsinstanzen) während der Auftragsausführung auf Computeknoten [parallel zu nutzen](batch-parallel-node-tasks.md). In diesem Fall wird häufig eine Knotengröße mit mehreren Kernen gewählt, um den höheren Bedarf an parallelen Aufgabenausführungen decken zu können.

- **Auslastungsgrade für verschiedene Aufgaben**: Alle Knoten in einem Pool haben die gleiche Größe. Wenn Sie Anwendungen mit unterschiedlichen Systemanforderungen bzw. Auslastungsgraden ausführen möchten, empfehlen wir Ihnen die Verwendung von separaten Pools.

- **Regionale Verfügbarkeit**: Eine VM-Serie oder -Größe ist in den Regionen, in denen Sie die Batch-Konten erstellen, möglicherweise nicht verfügbar. Informationen dazu, welche Größen verfügbar sind, finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/regions/services/).

- **Kontingente**: Die [Kernkontingente](batch-quota-limit.md#resource-quotas) in Ihrem Batch-Konto können die Anzahl der Knoten einer bestimmten Größe beschränken, die Sie einem Batch-Pool hinzufügen können. Bei Bedarf können Sie eine [Kontingenterhöhung](batch-quota-limit.md#increase-a-quota) anfordern.

- **Poolkonfiguration**: Im Allgemeinen stehen Ihnen mehr VM-Größenoptionen zur Verfügung, wenn Sie einen Pool in der Konfiguration des virtuellen Computers anstatt in der Clouddienstkonfiguration erstellen.

## <a name="supported-vm-images"></a>Unterstützte VM-Images

Verwenden Sie eine der folgenden APIs, um eine Liste mit Windows- und Linux-VM-Images zurückzugeben, die derzeit von Batch unterstützt werden, einschließlich der Knoten-Agent-SKU-IDs für jedes Image:

- Batch-Dienste-REST-API: [Liste unterstützter Images](/rest/api/batchservice/account/listsupportedimages)
- Mit PowerShell: [Get-AzBatchSupportedImage](/powershell/module/az.batch/get-azbatchsupportedimage)
- Azure CLI: [az batch pool supported-images](/cli/azure/batch/pool/supported-images)

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über den [Workflow des Batch-Diensts und primäre Ressourcen](batch-service-workflow-features.md) wie Pools, Knoten, Aufträge und Aufgaben.
- Informationen zur Verwendung von rechenintensiven VM-Größen finden Sie unter [Verwenden RDMA-fähiger oder GPU-fähiger Instanzen in Batch-Pools](batch-pool-compute-intensive-sizes.md).

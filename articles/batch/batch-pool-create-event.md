---
title: 'Azure Batch: Poolerstellungsereignis'
description: Referenz zum Batch-Poolerstellungsereignis, das nach dem Erstellen eines Pools ausgegeben wird. Der Inhalt des Protokolls macht allgemeine Informationen zum Pool verfügbar.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: 03c656ea2698ee0e61c1ddb903fcc10df9d3044b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352387"
---
# <a name="pool-create-event"></a>Poolerstellungsereignis

 Dieses Ereignis wird ausgegeben, nachdem ein Pool erstellt wurde. Der Inhalt des Protokolls macht allgemeine Informationen zum Pool verfügbar. Wenn die Zielgröße des Pools größer als 0 Computeknoten ist, folgt unmittelbar auf dieses Ereignis ein Ereignis zum Start der Größenänderung des Pools.

 Das folgende Beispiel zeigt den Text eines Poolerstellungsereignisses für einen Pool, der mit der Eigenschaft `CloudServiceConfiguration` erstellt wurde.

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Standard_F1s",
    "imageType": "VirtualMachineConfiguration",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "virtualMachineConfiguration": {
          "imageReference": {
            "publisher": " ",
            "offer": " ",
            "sku": " ",
            "version": " "
          },
          "nodeAgentId": " "
        },
    "resizeTimeout": "300000",
    "targetDedicatedNodes": 2,
    "targetLowPriorityNodes": 2,
    "taskSlotsPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|Element|type|Notizen|
|-------------|----------|-----------|
|`id`|String|Die ID des Pools.|
|`displayName`|String|Der Anzeigename des Pools.|
|`vmSize`|String|Die Größe der virtuellen Computer im Pool. Alle virtuellen Computer in einem Pool haben die gleiche Größe. <br/><br/> Informationen zu den verfügbaren Größen von virtuellen Computern für Cloud Services-Pools (Pools, die mit der „CloudServiceConfiguration“-Eigenschaft erstellt wurden) finden Sie unter [Größen für Cloud Services](../cloud-services/cloud-services-sizes-specs.md). Der Azure Batch-Dienst unterstützt alle Cloud Services-VM-Größen mit Ausnahme von `ExtraSmall`.<br/><br/> Informationen zu verfügbaren VM-Größen für Pools, die Images aus Virtual Machines Marketplace verwenden (Pools, die mit der „VirtualMachineConfiguration“-Eigenschaft erstellt wurden), finden Sie unter [Größen für virtuelle Computer](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) oder [Größen für virtuelle Computer](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows). Batch unterstützt alle Größen von Azure-VMs mit Ausnahme von `STANDARD_A0` und Größen mit Storage Premium (Serien `STANDARD_GS`, `STANDARD_DS` und `STANDARD_DSV2`).|
|`imageType`|String|Die Bereitstellungsmethode für das Image. Die Werte `virtualMachineConfiguration` oder `cloudServiceConfiguration` werden unterstützt.|
|[`cloudServiceConfiguration`](#bk_csconf)|Komplexer Typ|Die Clouddienstkonfiguration des Pools.|
|[`virtualMachineConfiguration`](#bk_vmconf)|Komplexer Typ|Die VM-Konfiguration des Pools.|
|[`networkConfiguration`](#bk_netconf)|Komplexer Typ|Die Netzwerkkonfiguration des Pools.|
|`resizeTimeout`|Time|Das Timeout für die Zuweisung von Computeknoten zum Pool, das für den letzten Größenänderungsvorgang für den Pool angegeben wird.  (Die anfängliche Größe bei der Poolerstellung zählt als eine Größenänderung.)|
|`targetDedicatedNodes`|Int32|Die Anzahl der dedizierten Computeknoten, die für den Pool angefordert werden.|
|`targetLowPriorityNodes`|Int32|Die Anzahl der Computeknoten mit niedriger Priorität, die für den Pool angefordert werden.|
|`enableAutoScale`|Bool|Gibt an, ob die Poolgröße mit der Zeit automatisch angepasst wird.|
|`enableInterNodeCommunication`|Bool|Gibt an, ob der Pool für eine direkte Kommunikation zwischen Knoten eingerichtet ist.|
|`isAutoPool`|Bool|Gibt an, ob der Pool über den AutoPool-Mechanismus eines Auftrags erstellt wurde.|
|`taskSlotsPerNode`|Int32|Die maximale Anzahl von Tasks, die gleichzeitig auf einem einzelnen Computeknoten im Pool ausgeführt werden können.|
|`vmFillType`|String|Definiert, wie vom Batch-Dienst Tasks zwischen den Computeknoten im Pool verteilt werden. Gültige Werte sind „Spread“ oder „Pack“.|

###  <a name="cloudserviceconfiguration"></a><a name="bk_csconf"></a> cloudServiceConfiguration

|Elementname|type|Notizen|
|------------------|----------|-----------|
|`osFamily`|String|Die Azure-Gastbetriebssystemfamilie, die auf den virtuellen Computern im Pool installiert werden soll.<br /><br /> Mögliche Werte:<br /><br /> **2**: Betriebssystemfamilie 2, gleichbedeutend mit Windows Server 2008 R2 SP1.<br /><br /> **3**: Betriebssystemfamilie 3, gleichbedeutend mit Windows Server 2012.<br /><br /> **4**: Betriebssystemfamilie 4, gleichbedeutend mit Windows Server 2012 R2.<br /><br /> Weitere Informationen finden Sie unter [Azure-Gastbetriebssystemversionen](../cloud-services/cloud-services-guestos-update-matrix.md#releases).|
|`targetOSVersion`|String|Die Azure-Gastbetriebssystemversion, die auf den virtuellen Computern im Pool installiert werden soll.<br /><br /> Der Standardwert ist **\*** , der die aktuelle Betriebssystemversion für die angegebene Familie angibt.<br /><br /> Andere zulässige Werte finden Sie unter [Azure-Gastbetriebssystemversionen](../cloud-services/cloud-services-guestos-update-matrix.md#releases).|

###  <a name="virtualmachineconfiguration"></a><a name="bk_vmconf"></a> virtualMachineConfiguration

|Elementname|type|Notizen|
|------------------|----------|-----------|
|[`imageReference`](#bk_imgref)|Komplexer Typ|Gibt Informationen zur Plattform oder das zu verwendende Marketplace-Image an.|
|`nodeAgentId`|String|Die SKU des Batch-Knoten-Agents, die auf dem Computeknoten bereitgestellt ist.|
|[`windowsConfiguration`](#bk_winconf)|Komplexer Typ|Gibt die Einstellungen des Windows-Betriebssystems auf dem virtuellen Computer an. Diese Eigenschaft darf nicht angegeben werden, wenn „ImageReference“ auf ein Linux-Betriebssystemimage verweist.|

###  <a name="imagereference"></a><a name="bk_imgref"></a> imageReference

|Elementname|type|Notizen|
|------------------|----------|-----------|
|`publisher`|String|Gibt den Herausgeber des Images an.|
|`offer`|String|Das Angebot des Images.|
|`sku`|String|Die SKU des Images.|
|`version`|String|Die Version des Images.|

###  <a name="windowsconfiguration"></a><a name="bk_winconf"></a> windowsConfiguration

|Elementname|type|Notizen|
|------------------|----------|-----------|
|`enableAutomaticUpdates`|Boolean|Gibt an, ob der virtuelle Computer für automatische Updates aktiviert ist. Wenn diese Eigenschaft nicht angegeben wird, ist der Standardwert TRUE.|

###  <a name="networkconfiguration"></a><a name="bk_netconf"></a> networkConfiguration

|Elementname|type|Notizen|
|------------------|--------------|----------|
|`subnetId`|String|Gibt den Ressourcenbezeichner des Subnetzes an, in dem die Computeknoten des Pools erstellt werden.|
---
title: 'Azure Batch: Poolerstellungsereignis | Microsoft-Dokumentation'
description: Referenz zum Batch-Poolerstellungsereignis.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 176f00de77c2d353d6efeb8b5a535a607b8f3204
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60776506"
---
# <a name="pool-create-event"></a>Poolerstellungsereignis

 Dieses Ereignis wird ausgegeben, nachdem ein Pool erstellt wurde. Der Inhalt des Protokolls macht allgemeine Informationen zum Pool verfügbar. Wenn die Zielgröße des Pools größer als 0 Computeknoten ist, folgt unmittelbar auf dieses Ereignis ein Ereignis zum Start der Größenänderung des Pools.

 Das folgende Beispiel zeigt den Text eines Poolerstellungsereignisses für einen Pool, der mit der „CloudServiceConfiguration“-Eigenschaft erstellt wurde.

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicated": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|Element|Typ|Notizen|
|-------------|----------|-----------|
|id|string|Die ID des Pools.|
|displayName|string|Der Anzeigename des Pools.|
|vmSize|string|Die Größe der virtuellen Computer im Pool. Alle virtuellen Computer in einem Pool haben die gleiche Größe. <br/><br/> Informationen zu den verfügbaren Größen von virtuellen Computern für Cloud Services-Pools (Pools, die mit der „CloudServiceConfiguration“-Eigenschaft erstellt wurden) finden Sie unter [Größen für Cloud Services](https://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/). Der Azure Batch-Dienst unterstützt alle Cloud Services-VM-Größen mit Ausnahme von `ExtraSmall`.<br/><br/> Informationen zu verfügbaren VM-Größen für Pools, die Images aus Virtual Machines Marketplace verwenden (Pools, die mit der „VirtualMachineConfiguration“-Eigenschaft erstellt wurden), finden Sie unter [Größen für virtuelle Computer](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/) (Linux) oder [Größen für virtuelle Computer](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) (Windows). Batch unterstützt alle Größen von Azure-VMs mit Ausnahme von `STANDARD_A0` und Größen mit Storage Premium (Serien `STANDARD_GS`, `STANDARD_DS` und `STANDARD_DSV2`).|
|[cloudServiceConfiguration](#bk_csconf)|Komplexer Typ|Die Clouddienstkonfiguration des Pools.|
|[virtualMachineConfiguration](#bk_vmconf)|Komplexer Typ|Die VM-Konfiguration des Pools.|
|[networkConfiguration](#bk_netconf)|Komplexer Typ|Die Netzwerkkonfiguration des Pools.|
|resizeTimeout|Time|Das Timeout für die Zuweisung von Computeknoten zum Pool, das für den letzten Größenänderungsvorgang für den Pool angegeben wird.  (Die anfängliche Größe bei der Poolerstellung zählt als eine Größenänderung.)|
|targetDedicated|Int32|Die Anzahl der Computeknoten, die für den Pool angefordert werden.|
|enableAutoScale|Bool|Gibt an, ob die Poolgröße mit der Zeit automatisch angepasst wird.|
|enableInterNodeCommunication|Bool|Gibt an, ob der Pool für eine direkte Kommunikation zwischen Knoten eingerichtet ist.|
|isAutoPool|Bool|Gibt an, ob der Pool über den AutoPool-Mechanismus eines Auftrags erstellt wurde.|
|maxTasksPerNode|Int32|Die maximale Anzahl von Tasks, die gleichzeitig auf einem einzelnen Computeknoten im Pool ausgeführt werden können.|
|vmFillType|string|Definiert, wie vom Batch-Dienst Tasks zwischen den Computeknoten im Pool verteilt werden. Gültige Werte sind „Spread“ oder „Pack“.|

###  <a name="bk_csconf"></a> cloudServiceConfiguration

|Elementname|Type|Notizen|
|------------------|----------|-----------|
|osFamily|string|Die Azure-Gastbetriebssystemfamilie, die auf den virtuellen Computern im Pool installiert werden soll.<br /><br /> Mögliche Werte:<br /><br /> **2**: Betriebssystemfamilie 2, gleichbedeutend mit Windows Server 2008 R2 SP1.<br /><br /> **3**: Betriebssystemfamilie 3, gleichbedeutend mit Windows Server 2012.<br /><br /> **4**: Betriebssystemfamilie 4, gleichbedeutend mit Windows Server 2012 R2.<br /><br /> Weitere Informationen finden Sie unter [Azure-Gastbetriebssystemversionen](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|
|targetOSVersion|string|Die Azure-Gastbetriebssystemversion, die auf den virtuellen Computern im Pool installiert werden soll.<br /><br /> Der Standardwert ist **\*** , der die aktuelle Betriebssystemversion für die angegebene Familie angibt.<br /><br /> Andere zulässige Werte finden Sie unter [Azure-Gastbetriebssystemversionen](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|

###  <a name="bk_vmconf"></a> virtualMachineConfiguration

|Elementname|Type|Notizen|
|------------------|----------|-----------|
|[imageReference](#bk_imgref)|Komplexer Typ|Gibt Informationen zur Plattform oder das zu verwendende Marketplace-Image an.|
|nodeAgentSKUId|string|Die SKU des Batch-Knoten-Agents, die auf dem Computeknoten bereitgestellt ist.|
|[windowsConfiguration](#bk_winconf)|Komplexer Typ|Gibt die Einstellungen des Windows-Betriebssystems auf dem virtuellen Computer an. Diese Eigenschaft darf nicht angegeben werden, wenn „ImageReference“ auf ein Linux-Betriebssystemimage verweist.|

###  <a name="bk_imgref"></a> imageReference

|Elementname|Type|Notizen|
|------------------|----------|-----------|
|publisher|string|Gibt den Herausgeber des Images an.|
|offer|string|Das Angebot des Images.|
|sku|string|Die SKU des Images.|
|version|string|Die Version des Images.|

###  <a name="bk_winconf"></a> windowsConfiguration

|Elementname|Type|Notizen|
|------------------|----------|-----------|
|enableAutomaticUpdates|Boolean|Gibt an, ob der virtuelle Computer für automatische Updates aktiviert ist. Wenn diese Eigenschaft nicht angegeben wird, ist der Standardwert TRUE.|

###  <a name="bk_netconf"></a> networkConfiguration

|Elementname|Type|Notizen|
|------------------|--------------|----------|
|subnetId|string|Gibt den Ressourcenbezeichner des Subnetzes an, in dem die Computeknoten des Pools erstellt werden.|

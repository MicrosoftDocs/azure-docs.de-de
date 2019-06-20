---
title: Importieren von VMs aus einem anderen Lab in Azure DevTest Labs | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie in Azure DevTest Labs virtuelle Computer aus einem anderen Lab in das aktuelle Lab importieren.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: cb4a3ec9be82957b4c0366ec232f1147c52d0251
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62125898"
---
# <a name="import-vms-from-another-lab-in-azure-devtest-labs"></a>Importieren von VMs aus einem anderen Lab in Azure DevTest Labs
Der Azure DevTest Labs-Dienst verbessert die Verwaltung virtueller Computer (VMs) für Entwicklungs- und Testaktivitäten deutlich. Der Dienst ermöglicht es Ihnen, eine VM von einem Lab in ein anderes zu verschieben, wenn sich die Team- oder Infrastrukturanforderungen ändern. Im Folgenden finden Sie einige gängige Szenarios, bei denen dies möglicherweise nötig ist:

- Eine Person im Team wechselt in eine andere Gruppe innerhalb des Unternehmens und möchte Entwicklungs-VMs in das Lab des neuen Teams mitnehmen.
- Die Gruppe hat das Kontingent auf Abonnementebene erreicht und möchte die Teams in mehrere Abonnements unterteilen.
- Das Unternehmen plant einen Wechsel zu ExpressRoute (oder einer anderen neuen Netzwerktopologie), und das Team möchte VMs verschieben, um diese neue Infrastruktur zu verwenden.

## <a name="solution-and-constraints"></a>Lösung und Einschränkungen
Azure DevTest Labs ermöglicht es Labbesitzern, VMs aus einem Quelllab in ein Ziellab zu importieren. Die Labbesitzer können dabei während des Vorgangs optional einen neuen Namen für die Ziel-VM festlegen. Der Importvorgang umfasst alle Abhängigkeiten wie Datenträger, Zeitpläne, Netzwerkeinstellungen usw. Der Vorgang dauert eine Weile und wird durch die Anzahl/Größe der Datenträger beeinflusst, die an den Quellcomputer angefügt sind, sowie durch die Entfernung zum Ziel (z.B. von der Region USA, Osten bis zur Region Asien, Südosten). Sobald der Importvorgang abgeschlossen ist, bleibt der virtuelle Quellcomputer im heruntergefahrenen Zustand, und der neue virtuelle Computer läuft im Ziellab.

Zwei Haupteinschränkungen müssen bei der Planung des Imports einer VM in ein anderes Lab beachtet werden:

- Der Import von VMs über Abonnements und Regionen hinweg wird unterstützt, die Abonnements müssen jedoch dem gleichen Azure Active Directory-Mandanten zugeordnet sein.
- VMs dürfen sich im Quelllab nicht in einem abrufbaren Zustand befinden.

Darüber hinaus müssen Sie für den Import einer VM aus einem Lab in ein anderes der Besitzer der VM im Quelllab und des Labs im Ziellab sein.

## <a name="steps-to-import-a-vm-from-another-lab"></a>Schritte zum Importieren einer VM aus einem anderen Lab
Derzeit können Sie eine VM aus einem Lab nur mithilfe von Azure PowerShell und der REST-API in ein anderes Lab importieren.

### <a name="use-powershell"></a>Verwenden von PowerShell
Laden Sie die PowerShell-Skriptdatei „ImportVirtualMachines.ps1“ aus dem [Git-Repository zu Azure DevTest Lab](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines) auf Ihr lokales Laufwerk herunter.

#### <a name="import-a-single-vm"></a>Importieren einer einzelnen VM
Führen Sie das Skript „ImportVirtualMachines.ps1“ aus, um eine einzelne VM aus einem Quelllab in ein Ziellab zu importieren. Sie können einen neuen Namen für die VM angeben, die kopiert wird, indem Sie den Parameter „DestinationVirtualMachineName“ verwenden.

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source VM>" `
                            -SourceDevTestLabName "<Name of the lab that contains the source VM>" `
                            -SourceVirtualMachineName "<Name of the machine. Optional. If not specified, all VMs are copied>" `
                            -DestinationSubscriptionId "<ID of the target/destination subscription>" `
                            -DestinationDevTestLabName "<Name of the lab to which the VM is copied>" `
                            -DestinationVirtualMachineName "<New name for the VM. Optional>"
```


#### <a name="importing-all-vms"></a>Importieren aller VMs
Sofern Sie keine VM im Quelllab angeben, importiert das Skript „ImportVirtualMachines.ps1“ bei der Ausführung alle VMs aus dem Quelllab in das Ziellab.

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source VM>" `
                            -SourceDevTestLabName "<Name of the lab that contains the source VM>" `
                            -DestinationSubscriptionId "<ID of the target/destination subscription>" `
                            -DestinationDevTestLabName "<Name of the lab to which the VMs are copied>"
```

### <a name="use-rest-api"></a>REST-API
Rufen Sie die REST-API im Ziellab auf, und übergeben Sie wie im folgenden Beispiel gezeigt die Informationen des Quelllabs, Abonnements und der VM als Parameter:

```json
POST https://management.azure.com/subscriptions/<ID of the target/destination subscription>/resourceGroups/<Name of the resource group that contains the destination lab>/providers/Microsoft.DevTestLab/labs/<Name of the lab to which the VMs are copied>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<ID of the subscription that contains the source VM>/resourcegroups/<Name of the resource group that contains the source lab>/providers/microsoft.devtestlab/labs/<Name of the lab that contains the source VM>/virtualmachines/MyVm",
   destinationVirtualMachineName: "MyVmNew"
}
```

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Größenänderung einer VM finden Sie unter [Resize a VM (Ändern der Größe einer VM)](devtest-lab-resize-vm.md).
- Informationen zur erneuten Bereitstellung einer VM finden Sie unter [Redeploy a VM (Erneutes Bereitstellen einer VM)](devtest-lab-redeploy-vm.md).

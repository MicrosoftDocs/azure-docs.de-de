---
title: Importieren virtueller Computer aus einem anderen Lab in Azure DevTest Labs | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie virtuelle Computer aus einem anderen Lab in das aktuelle Lab importieren.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: ca6ed58cfabb5027830828812c4820c1b586875c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61322853"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Importieren virtueller Computer aus einem anderen Lab in Azure DevTest Labs
In diesem Artikel erfahren Sie, wie Sie virtuelle Computer aus einem anderen Lab in Ihr Lab importieren.

## <a name="scenarios"></a>Szenarien
Folgende Beispiele beschreiben Szenarios, in denen Sie VMs aus einem Lab in ein anderes Lab importieren müssen:

- Ein Mitglied eines Teams wechselt zu einer anderen Gruppe im Unternehmen und möchte den Entwicklerdesktop mit in die DevTest Labs-Umgebung des neuen Teams nehmen.
- Die Gruppe hat das [Kontingent auf Abonnementebene](../azure-subscription-service-limits.md) erreicht und möchte die Teams in mehrere Abonnements unterteilen.
- Das Unternehmen wechselt zu ExpressRoute (oder einer anderen neuen Netzwerktopologie), und das Team möchte virtuelle Computer verschieben, um diese neue Infrastruktur zu verwenden.

## <a name="solution-and-constraints"></a>Lösung und Einschränkungen
Mit diesem Feature können Sie VMs aus einem Lab (Quelle) in ein anderes Lab (Ziel) importieren. Während des Vorgangs können Sie optional einen neuen Namen für die Ziel-VM festlegen. Der Importvorgang umfasst alle Abhängigkeiten wie Datenträger, Zeitpläne, Netzwerkeinstellungen usw.

Der Vorgang dauert einige Zeit und wird von den folgenden Faktoren beeinflusst:

- Anzahl bzw. Größe der Datenträger, die mit dem Quellcomputer verbunden sind (da es sich um einen Kopier- und nicht um einen Verschiebevorgang handelt).
- Distanz zum Ziel (z.B. von der Region „USA, Osten“ nach „Asien, Südosten“).

Sobald der Vorgang abgeschlossen ist, bleibt der virtuelle Quellcomputer im heruntergefahrenen Zustand, und der neue virtuelle Computer läuft im Ziellab.

Zwei Haupteinschränkungen müssen bei der Planung des Imports von VMs aus einem in ein anderes Lab beachtet werden:

- Der Import von VMs über Abonnements und Regionen hinweg wird unterstützt, die Abonnements müssen jedoch dem gleichen Azure Active Directory-Mandanten zugeordnet sein.
- VMs dürfen sich im Quelllab nicht in einem abrufbaren Zustand befinden.
- Sie sind der Besitzer der VM im Quelllab und der Besitzer des Labs im Ziellab.
- Zurzeit wird dieses Feature ausschließlich von PowerShell und REST-API unterstützt.

## <a name="use-powershell"></a>Verwenden von PowerShell
Laden Sie die ImportVirtualMachines.ps1-Datei von [GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines) herunter. Mithilfe des Skripts können Sie eine einzelne VM oder alle VMs aus dem Quelllab in das Ziellab importieren.

### <a name="use-powershell-to-import-a-single-vm"></a>Importieren einer einzelnen VM mit PowerShell
Zur Ausführung dieses PowerShell-Skripts müssen Sie die Quell-VM und das Ziellab sowie optional einen neuen Namen für den Zielcomputer angeben:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>Importieren aller VMs aus dem Quelllab mit PowerShell
Wenn kein virtueller Quellcomputer angegeben ist, importiert das Skript automatisch alle VMs in die DevTest Labs-Umgebung.  Beispiel:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>Verwenden von HTTP REST zum Importieren einer VM
Der REST-Aufruf ist einfach. Sie geben genügend Informationen an, um die Quell- und Zielressourcen zu identifizieren. Denken Sie daran, dass der Vorgang für die Ressource des Ziellabs ausgeführt wird.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>Nächste Schritte
Entsprechende Informationen finden Sie in den folgenden Artikeln:

- [Festlegen von Richtlinien für ein Lab](devtest-lab-get-started-with-lab-policies.md)
- [Häufig gestellte Fragen](devtest-lab-faq.md)

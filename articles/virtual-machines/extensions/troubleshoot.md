---
title: Problembehandlung bei Fehlern im Zusammenhang mit Windows-VM-Erweiterungen
description: Erfahren Sie mehr über die Problembehandlung für Fehler bei Azure Windows-VM-Erweiterungen.
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: gwallace
editor: ''
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: bca826cda8dfe47c341886faaf4a0d66f09d37d2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966342"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Problembehandlung bei Fehlern im Zusammenhang mit Azure Windows-VM-Erweiterungen
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Anzeigen des Erweiterungsstatus
Azure Resource Manager-Vorlagen können aus Azure PowerShell ausgeführt werden. Sobald die Vorlage ausgeführt wird, kann der Erweiterungsstatus im Azure-Ressourcen-Explorer oder in den Befehlszeilentools angezeigt werden.

Beispiel:

Azure PowerShell:

```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

Hier ist die Beispielausgabe:

```output
Extensions:  {
  "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
  "Name": "myCustomScriptExtension",
  "SubStatuses": [
    {
      "Code": "ComponentStatus/StdOut/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
          \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
          test.txt                          \\n\\n",
                  "Time": null
      },
    {
      "Code": "ComponentStatus/StdErr/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "",
      "Time": null
    }
  ]
}
```

## <a name="troubleshooting-extension-failures"></a>Problembehandlung bei Erweiterungsfehlern

### <a name="rerun-the-extension-on-the-vm"></a>Erneutes Ausführen der Erweiterung auf dem virtuellen Computer
Wenn Sie mithilfe der benutzerdefinierten Skripterweiterung Skripts auf dem virtuellen Computer ausführen, könnten ab und an Fehler auftreten, bei denen der virtuelle Computer erfolgreich erstellt wurde, das Skript jedoch fehlgeschlagen ist. Unter diesen Bedingungen empfiehlt es sich, die Erweiterung zu entfernen und die Vorlage erneut auszuführen, um den Fehler zu beheben.
Hinweis: In Zukunft wird diese Funktionalität verbessert, damit es nicht mehr notwendig ist, die Erweiterung zu deinstallieren.

#### <a name="remove-the-extension-from-azure-powershell"></a>Entfernen der Erweiterung aus Azure PowerShell
```azurepowershell
Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"
```

Nachdem die Erweiterung entfernt wurde, kann die Vorlage erneut ausgeführt werden, um die Skripts auf dem virtuellen Computer auszuführen.

### <a name="trigger-a-new-goalstate-to-the-vm"></a>Auslösen eines neuen GoalState für die VM
Möglicherweise stellen Sie fest, dass eine Erweiterung nicht ausgeführt wurde oder aufgrund eines fehlenden „Microsoft Azure CRP Certificate Generator“ nicht ausgeführt werden kann. (Dieses Zertifikat wird verwendet, um den Transport der geschützten Einstellungen der Erweiterung abzusichern.)
Dieses Zertifikat wird automatisch erneut generiert, indem der Windows-Gast-Agent im virtuellen Computer neu gestartet wird:
- Öffnen Sie den Task-Manager.
- Navigieren Sie zur Registerkarte „Details“.
- Suchen Sie den Prozess „WindowsAzureGuestAgent.exe“.
- Klicken Sie mit der rechten Maustaste darauf, und wählen Sie „Task beenden“ aus. Der Prozess wird automatisch neu gestartet.


Sie können auch einen neuen GoalState für die VM auslösen, indem Sie ein „leeres Update“ ausführen:

Azure PowerShell:

```azurepowershell
$vm = Get-AzureRMVM -ResourceGroupName <RGName> -Name <VMName>  
Update-AzureRmVM -ResourceGroupName <RGName> -VM $vm  
```

Azure CLI:

```azurecli
az vm update -g <rgname> -n <vmname>
```

Wenn ein „leeres Update“ nicht funktioniert, können Sie der VM einen neuen leeren Datenträger für Daten aus dem Azure-Verwaltungsportal hinzufügen und diesen später entfernen, nachdem das Zertifikat wieder hinzugefügt wurde.

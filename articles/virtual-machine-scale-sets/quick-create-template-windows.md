---
title: 'Schnellstart: Erstellen einer Windows-VM-Skalierungsgruppe mit einer Azure-Vorlage | Microsoft-Dokumentation'
description: Es wird beschrieben, wie Sie mit einer Azure Resource Manager-Vorlage schnell eine Windows-VM-Skalierungsgruppe erstellen, mit der eine Beispiel-App bereitgestellt wird und Regeln für die automatische Skalierung konfiguriert werden.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/27/2018
ms.author: cynthn
ms.openlocfilehash: a91bbceb3cb55cf3e7f8e0f84581af07f1ffb80d
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66728905"
---
# <a name="quickstart-create-a-windows-virtual-machine-scale-set-with-an-azure-template"></a>Schnellstart: Erstellen einer Windows-VM-Skalierungsgruppe mit einer Azure-Vorlage

Mit einer VM-Skalierungsgruppe können Sie eine Gruppe identischer, automatisch skalierender virtueller Computer bereitstellen und verwalten. Sie können die Anzahl virtueller Computer in der Skalierungsgruppe manuell skalieren oder basierend auf der Ressourcennutzung, z.B. CPU-Auslastung, Speicherbedarf oder Netzwerkdatenverkehr, Regeln für die automatische Skalierung definieren. Daraufhin wird der Datenverkehr durch einen Azure-Lastenausgleich auf die VM-Instanzen in der Skalierungsgruppe verteilt. In dieser Schnellstartanleitung erstellen Sie eine VM-Skalierungsgruppe und stellen eine Beispielanwendung mit einer Azure Resource Manager-Vorlage bereit.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="define-a-scale-set-in-a-template"></a>Definieren einer Skalierungsgruppe in einer Vorlage
Mit Azure Resource Manager-Vorlagen können Sie Gruppen mit verwandten Ressourcen bereitstellen. Vorlagen werden in JSON (JavaScript Object Notation) geschrieben und definieren die gesamte Azure-Infrastrukturumgebung für Ihre Anwendung. In einer einzelnen Vorlage können Sie die VM-Skalierungsgruppe erstellen, Anwendungen installieren und Regeln für die automatische Skalierung konfigurieren. Bei Nutzung von Variablen und Parametern kann diese Vorlage wiederverwendet werden, um vorhandene Skalierungsgruppen zu aktualisieren oder zusätzliche zu erstellen. Sie können Vorlagen über das Azure-Portal, die Azure CLI oder Azure PowerShell oder über CI/CD-Pipelines (Continuous Integration/Continuous Delivery) bereitstellen.

Weitere Informationen zu Vorlagen finden Sie unter [Übersicht über Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment). Informationen zur JSON-Syntax und zu Eigenschaften finden Sie in der Vorlagenreferenz [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets).

Mit einer Vorlage wird die Konfiguration für die einzelnen Ressourcentypen definiert. Der Ressourcentyp einer VM-Skalierungsgruppe ist mit einer individuellen VM vergleichbar. Dies sind die wichtigsten Bestandteile des Ressourcentyps einer VM-Skalierungsgruppe:

| Eigenschaft                     | Beschreibung der Eigenschaft                                  | Beispiel für Vorlagenwert                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| type                         | Zu erstellender Azure-Ressourcentyp                            | Microsoft.Compute/virtualMachineScaleSets |
| name                         | Name der Skalierungsgruppe                                       | myScaleSet                                |
| location                     | Standort für die Erstellung der Skalierungsgruppe                     | USA (Ost)                                   |
| sku.name                     | VM-Größe für die einzelnen Skalierungsgruppeninstanzen                  | Standard_A1                               |
| sku.capacity                 | Anzahl von VM-Instanzen für die anfängliche Erstellung           | 2                                         |
| upgradePolicy.mode           | Upgrademodus für VM-Instanz bei Auftreten von Änderungen              | Automatisch                                 |
| imageReference               | Plattform- oder benutzerdefiniertes Image für die VM-Instanzen | Microsoft Windows Server 2016 Datacenter  |
| osProfile.computerNamePrefix | Namenspräfix für die einzelnen VM-Instanzen                     | myvmss                                    |
| osProfile.adminUsername      | Benutzername für die einzelnen VM-Instanzen                        | azureuser                                 |
| osProfile.adminPassword      | Kennwort für die einzelnen VM-Instanzen                        | P@ssw0rd!                                 |

 Im folgenden Beispiel wird die grundlegende Ressourcendefinition der Skalierungsgruppe veranschaulicht. Zu Anpassen einer Skalierungsgruppenvorlage können Sie die VM-Größe oder anfängliche Kapazität ändern oder ein anderes Plattformimage bzw. ein benutzerdefiniertes Image verwenden.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US",
  "apiVersion": "2017-12-01",
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

 Die Konfiguration der virtuellen Netzwerkschnittstellenkarte (NIC) ist nicht angegeben, um das Beispiel möglichst kurz zu halten. Auch zusätzliche Komponenten, z.B. ein Lastenausgleichsmodul, werden nicht angezeigt. Eine vollständige Skalierungsgruppenvorlage ist [am Ende dieses Artikels](#deploy-the-template) angegeben.


## <a name="add-a-sample-application"></a>Hinzufügen einer Beispielanwendung
Installieren Sie eine einfache Webanwendung, um Ihre Skalierungsgruppe zu testen. Beim Bereitstellen einer Skalierungsgruppe können über VM-Erweiterungen Aufgaben für die Konfiguration und Automatisierung nach der Bereitstellung, z.B. das Installieren einer App, bereitgestellt werden. Skripts können aus Azure Storage oder GitHub heruntergeladen oder dem Azure-Portal zur Laufzeit für die Erweiterung bereitgestellt werden. Fügen Sie dem obigen Ressourcenbeispiel den Abschnitt *extensionProfile* hinzu, um eine Erweiterung auf Ihre Skalierungsgruppe anzuwenden. Im Erweiterungsprofil werden normalerweise die folgenden Eigenschaften definiert:

- Erweiterungstyp
- Erweiterungsherausgeber
- Erweiterungsversion
- Speicherort der Konfiguration bzw. der Installationsskripts
- Auf den VM-Instanzen auszuführende Befehle

In der Beispielvorlage für eine [ASP.NET-Anwendung unter Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) wird die PowerShell DSC-Erweiterung verwendet, um eine ASP.NET MVC-App zu installieren, die in IIS ausgeführt wird. 

Ein Installationsskript wird wie unter *url* definiert von GitHub heruntergeladen. Anschließend führt die Erweiterung *InstallIIS* über das Skript *IISInstall.ps1* aus, wie in *function* und *Script* definiert. Die eigentliche ASP.NET-App wird als Web Deploy-Paket bereitgestellt, das auch von GitHub heruntergeladen wird, wie in *WebDeployPackagePath* definiert:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "Microsoft.Powershell.DSC",
      "properties": {
        "publisher": "Microsoft.Powershell",
        "type": "DSC",
        "typeHandlerVersion": "2.9",
        "autoUpgradeMinorVersion": true,
        "forceUpdateTag": "1.0",
        "settings": {
          "configuration": {
            "url": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/DSC/IISInstall.ps1.zip",
            "script": "IISInstall.ps1",
            "function": "InstallIIS"
          },
          "configurationArguments": {
            "nodeName": "localhost",
            "WebDeployPackagePath": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/WebDeploy/DefaultASPWebApp.v1.0.zip"
          }
        }
      }
    }
  ]
}
```

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage
Sie können die Vorlage für die [ASP.NET MVC-Anwendung unter Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) mit der folgenden Schaltfläche **In Azure bereitstellen** bereitstellen. Diese Schaltfläche bewirkt Folgendes: Das Azure-Portal wird geöffnet, die vollständige Vorlage wird geladen, und es werden einige Parameter abgefragt, z.B. der Name der Skalierungsgruppe, die Instanzanzahl und die Administratoranmeldeinformationen.

[![In Azure bereitstellen](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-windows-webapp-dsc-autoscale%2Fazuredeploy.json)

Sie können auch Azure PowerShell verwenden, um die ASP.NET-Anwendung wie folgt mit [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) unter Windows zu installieren:

```azurepowershell-interactive
# Create a resource group
New-AzResourceGroup -Name myResourceGroup -Location EastUS

# Deploy template into resource group
New-AzResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateFile https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json

# Update the scale set and apply the extension
Update-AzVmss `
    -ResourceGroupName myResourceGroup `
    -VmScaleSetName myVMSS `
    -VirtualMachineScaleSet $vmssConfig
```

Folgen Sie den Aufforderungen zum Angeben des Namens einer Skalierungsgruppe und der Administratoranmeldeinformationen für die VM-Instanzen. Es kann 10 bis 15 Minuten dauern, bis die Skalierungsgruppe erstellt und die Erweiterung zum Konfigurieren der App angewendet wurde.


## <a name="test-your-scale-set"></a>Testen Ihrer Skalierungsgruppe
Greifen Sie über einen Webbrowser auf die Beispielwebanwendung zu, um Ihre Skalierungsgruppe in Aktion zu sehen. Rufen Sie mit [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) die öffentliche IP-Adresse Ihres Lastenausgleichs ab:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Geben Sie die öffentliche IP-Adresse des Lastenausgleichsmoduls in einem Webbrowser im Format *http:\//publicIpAddress/MyApp* ein. Das Lastenausgleichsmodul verteilt den Datenverkehr auf eine Ihrer VM-Instanzen. Dies ist im folgenden Beispiel dargestellt:

![Ausführen der IIS-Website](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Mit [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) können Sie die Ressourcengruppe und die Skalierungsgruppe entfernen, sofern diese nicht mehr benötigt werden. Der Parameter `-Force` bestätigt ohne eine zusätzliche Aufforderung, dass Sie die Ressourcen löschen möchten. Der Parameter `-AsJob` gibt die Steuerung an die Eingabeaufforderung zurück, ohne zu warten, bis der Vorgang abgeschlossen ist.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Nächste Schritte
In dieser Schnellstartanleitung haben Sie mit einer Azure-Vorlage eine Windows-Skalierungsgruppe erstellt und die PowerShell DSC-Erweiterung verwendet, um eine einfache ASP.NET-App auf den VM-Instanzen zu installieren. Im nächsten Tutorial erfahren Sie mehr über die Erstellung und Verwaltung von Azure-VM-Skalierungsgruppen.

> [!div class="nextstepaction"]
> [Erstellen und Verwalten von Azure-VM-Skalierungsgruppen](tutorial-create-and-manage-powershell.md)

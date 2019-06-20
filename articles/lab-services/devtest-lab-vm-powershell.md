---
title: Erstellen einer VM in DevTest Labs mit Azure PowerShell | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie in Azure DevTest Labs mithilfe von Azure PowerShell VMs erstellen und verwalten.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: a9629cd14c71a163612c2c4ba3c7b109a52b91ad
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60622438"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Erstellen einer VM in DevTest Labs mithilfe von Azure PowerShell
In diesem Artikel wird das Erstellen einer VM in Azure DevTest Labs mithilfe von Azure PowerShell erläutert. Sie können PowerShell-Skripts verwenden, um die Erstellung von VMs in einem Lab in Azure DevTest Labs zu automatisieren. 

## <a name="prerequisites"></a>Voraussetzungen
Vorbereitungen

- [Erstellen Sie ein Lab](devtest-lab-create-lab.md), falls Sie kein vorhandenes Lab zum Testen des Skripts oder der Befehle in diesem Artikel verwenden möchten. 
- [Installieren Sie Azure PowerShell](/powershell/azure/install-az-ps?view=azps-1.7.0), oder verwenden Sie den integrierten Azure Cloud Shell-Dienst im Azure-Portal. 

## <a name="powershell-script"></a>PowerShell-Skript
Das Beispielskript in diesem Abschnitt verwendet das Cmdlet [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0).  Das Cmdlet erfordert die Ressourcen-ID des Labs, den Namen der auszuführenden Aktion (`createEnvironment`) und die zum Ausführen dieser Aktion erforderlichen Parameter. Die Parameter sind in einer Hashtabelle mit allen VM-Beschreibungseigenschaften enthalten. 

```powershell
[CmdletBinding()]

Param(
[Parameter(Mandatory = $false)]  $SubscriptionId,
[Parameter(Mandatory = $true)]   $LabResourceGroup,
[Parameter(Mandatory = $true)]   $LabName,
[Parameter(Mandatory = $true)]   $NewVmName,
[Parameter(Mandatory = $true)]   $UserName,
[Parameter(Mandatory = $true)]   $Password
)
 
pushd $PSScriptRoot

try {
    if ($SubscriptionId -eq $null) {
        $SubscriptionId = (Get-AzContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

    $labSubnetName = $virtualNetwork.properties.allowedSubnets[0].labSubnetName

    #Prepare all the properties needed for the createEnvironment
    # call used to create the new VM.
    # The properties will be slightly different depending on the base of the vm
    # (a marketplace image, custom image or formula).
    # The setup of the virtual network to be used may also affect the properties.
    # This sample includes the properties to add an additional disk under dataDiskParameters
    
    $parameters = @{
       "name"      = $NewVmName;
       "location"  = $lab.Location;
       "properties" = @{
          "labVirtualNetworkId"     = $virtualNetwork.ResourceId;
          "labSubnetName"           = $labSubnetName;
          "notes"                   = "Windows Server 2016 Datacenter";
          "osType"                  = "windows"
          "galleryImageReference"   = @{
             "offer"     = "WindowsServer";
             "publisher" = "MicrosoftWindowsServer";
             "sku"       = "2016-Datacenter";
             "osType"    = "Windows";
             "version"   = "latest"
          };
          "size"                    = "Standard_DS2_v2";
          "userName"                = $UserName;
          "password"                = $Password;
          "disallowPublicIpAddress" = $true;
          "dataDiskParameters" = @(@{
            "attachNewDataDiskOptions" = @{
                "diskName" = "adddatadisk"
                "diskSizeGiB" = "1023"
                "diskType" = "Standard"
                }
          "hostCaching" = "ReadWrite"
          })
       }
    }
    
    #The following line is the same as invoking
    # https://azure.github.io/projects/apis/#!/Labs/Labs_CreateEnvironment rest api

    Invoke-AzResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

Die Eigenschaften für die VM im obigen Skript ermöglichen das Erstellen einer VM mit dem Betriebssystem Windows Server 2016 DataCenter. Diese Eigenschaften unterscheiden sich für jeden VM-Typ geringfügig. Im Abschnitt [Definieren einer VM](#define-virtual-machine) wird erläutert, wie Sie die in diesem Skript zu verwendenden Eigenschaften ermitteln.

Der folgende Befehl ist ein Beispiel für die Ausführung des unter einem Dateinamen gespeicherten Skripts: Create-LabVirtualMachine.ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>Definieren einer VM
In diesem Abschnitt wird erläutert, wie Sie die spezifischen Eigenschaften eines VM-Typs abrufen, den Sie erstellen möchten. 

### <a name="use-azure-portal"></a>Verwenden des Azure-Portals
Beim Erstellen einer VM im Azure-Portal können Sie eine Azure Resource Manager-Vorlage generieren. Sie müssen den Prozess zum Erstellen der VM nicht vollständig ausführen. Befolgen Sie die Schritte lediglich, bis die Vorlage angezeigt wird. Dies ist die beste Methode zum Abrufen der erforderlichen JSON-Beschreibung, wenn Sie noch keine Lab-VM erstellt haben. 

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
2. Wählen Sie im linken Navigationsmenü die Option **Alle Dienste** aus.
3. Suchen Sie in der Liste der Dienste nach **DevTest Labs**, und wählen Sie den Eintrag aus. 
4. Wählen Sie auf der Seite **DevTest Labs** Ihr Lab in der Liste der Labs aus.
5. Klicken Sie auf der Startseite für Ihr Lab auf der Symbolleiste auf **+ Hinzufügen**. 
6. Wählen Sie ein **Basisimage** für die VM aus. 
7. Wählen Sie am unteren Rand der Seite über der Schaltfläche **Senden** die Option **Automation-Optionen** aus. 
8. Die **Azure Resource Manager-Vorlage** zum Erstellen der VM wird angezeigt. 
9. Das JSON-Segment im Abschnitt **resources** enthält die Definition für den Imagetyp, den Sie zuvor ausgewählt haben. 

    ```json
    {
      "apiVersion": "2018-10-15-preview",
      "type": "Microsoft.DevTestLab/labs/virtualmachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "notes": "Windows Server 2019 Datacenter",
        "galleryImageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2019-Datacenter",
          "osType": "Windows",
          "version": "latest"
        },
        "size": "[parameters('size')]",
        "userName": "[parameters('userName')]",
        "password": "[parameters('password')]",
        "isAuthenticationWithSshKey": false,
        "labSubnetName": "[variables('labSubnetName')]",
        "disallowPublicIpAddress": true,
        "storageType": "Standard",
        "allowClaim": false,
        "networkInterface": {
          "sharedPublicIpAddressConfiguration": {
            "inboundNatRules": [
              {
                "transportProtocol": "tcp",
                "backendPort": 3389
              }
            ]
          }
        }
      }
    }
    ```

Dieses Beispiel zeigt, wie eine Definition für ein Azure Marketplace-Image abgerufen wird. Sie können die Definition eines benutzerdefinierten Images, einer Formel oder Umgebung auf die gleiche Weise abrufen. Fügen Sie die erforderlichen Artefakte für die VM hinzu, und legen Sie alle notwendigen erweiterten Einstellungen fest. Geben Sie Werte für die Pflichtfelder und optionalen Felder an, bevor Sie auf die Schaltfläche **Automation-Optionen** klicken.

### <a name="use-azure-rest-api"></a>Verwenden der Azure-REST-API
Im Folgenden wird erläutert, wie Sie die Eigenschaften eines Images mit der REST-API abrufen: Diese Schritte gelten nur für eine vorhandene VM in einem Lab. 

1. Navigieren Sie zur Seite [Virtuelle Computer – Liste](/rest/api/dtl/virtualmachines/list), und klicken Sie auf die Schaltfläche **Ausprobieren**. 
2. Wählen Sie Ihr Azure-**Abonnement** aus.
3. Geben Sie die **Ressourcengruppe für das Lab** ein.
4. Geben Sie den **Namen des Labs** ein. 
5. Klicken Sie auf **Run** (Ausführen).
6. Die **Eigenschaften für das Image**, auf deren Grundlage die VM erstellt wurde, werden angezeigt. 



## <a name="next-steps"></a>Nächste Schritte
Lesen Sie den folgenden Artikel: [Azure PowerShell documentation for Azure DevTest Labs](/powershell/module/az.devtestlabs/) (Azure PowerShell-Dokumentation für Azure DevTest Labs)

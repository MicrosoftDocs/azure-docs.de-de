---
title: Erstellen von Windows-VM-Images mit Packer in Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit Packer Images von virtuellen Windows-Computern in Azure erstellen
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/22/2019
ms.author: cynthn
ms.openlocfilehash: 81dbd8082d5a7ab473cc0cbe5fcb6e564fbd750c
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65951129"
---
# <a name="how-to-use-packer-to-create-windows-virtual-machine-images-in-azure"></a>Erstellen von Images von virtuellen Windows-Computern in Azure mit Packer
Jeder virtuelle Computer (VM) in Azure wird anhand eines Images erstellt, das die Windows-Distribution und -Betriebssystemversion bestimmt. Images können vorinstallierte Anwendungen und Konfigurationen enthalten. Azure Marketplace enthält viele Images von Erst- und Drittanbietern für die gängigsten Betriebssysteme und Anwendungsumgebungen. Sie können jedoch auch entsprechend Ihren Anforderungen eigene benutzerdefinierte Images erstellen. In diesem Artikel wird erläutert, wie Sie mit dem Open-Source-Tool [Packer](https://www.packer.io/) benutzerdefinierte Images in Azure definieren und erstellen.

Dieser Artikel wurde zuletzt am 21.02.2019 unter Verwendung von Version 1.3.0 des [Azure PowerShell-Moduls](https://docs.microsoft.com/powershell/azure/install-az-ps) und Version 1.3.4 von [Packer](https://www.packer.io/docs/install/index.html) geprüft.

> [!NOTE]
> Azure bietet jetzt einen Dienst zum Definieren und Erstellen eigener benutzerdefinierter Images: Azure Image Builder (Vorschauversion). Azure Image Builder baut auf Packer auf, daher können Sie sogar Ihre vorhandenen Packer Shell Provisioner-Skripts importieren. Informationen zu den ersten Schritten mit Azure Image Builder finden Sie unter [Erstellen eines virtuellen Windows-Computers mit Azure Image Builder](image-builder.md).

## <a name="create-azure-resource-group"></a>Erstellen einer Azure-Ressourcengruppe
Während des Buildprozesses zum Erstellen der Quell-VM erstellt Packer temporäre Azure-Ressourcen. Zum Erfassen dieser Quell-VM zur Verwendung als Image müssen Sie eine Ressourcengruppe definieren. Die Ausgabe des Packer-Buildprozesses wird in dieser Ressourcengruppe gespeichert.

Erstellen Sie mit [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* erstellt:

```azurepowershell
$rgName = "mypackerGroup"
$location = "East US"
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Erstellen von Azure-Anmeldeinformationen
Packer authentifiziert sich bei Azure mithilfe eines Dienstprinzipals. Ein Azure-Dienstprinzipal ist eine Sicherheitsidentität, die Sie mit Apps, Diensten und Automatisierungstools wie Packer verwenden können. Sie steuern und definieren die Berechtigungen hinsichtlich der Vorgänge, die der Dienstprinzipal in Azure ausführen können soll.

Erstellen Sie mit [New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal) einen Dienstprinzipal, und weisen Sie mit [New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment) dem Dienstprinzipal Berechtigungen zum Erstellen und Verwalten von Ressourcen zu. Der Wert für `-DisplayName` muss eindeutig sein. Ersetzen Sie ihn wie erforderlich durch Ihren eigenen Wert.  

```azurepowershell
$sp = New-AzADServicePrincipal -DisplayName "PackerServicePrincipal"
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret)
$plainPassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Geben Sie dann das Kennwort und die Anwendungs-ID aus.

```powershell
$plainPassword
$sp.ApplicationId
```


Um sich bei Azure zu authentifizieren, müssen Sie mit [Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription) auch Ihren Azure-Mandanten und Ihre Azure-Abonnement-IDs abrufen:

```powershell
Get-AzSubscription
```


## <a name="define-packer-template"></a>Definieren der Packer-Vorlage
Um Images zu generieren, erstellen Sie eine Vorlage als JSON-Datei. In der Vorlage definieren Sie Generatoren und Provisioners (Bereitstellungsmethoden), die den tatsächlichen Buildprozess ausführen. Packer bietet einen [Generator für Azure](https://www.packer.io/docs/builders/azure.html), mit dem Sie Azure-Ressourcen definieren können, wie z.B. die Anmeldeinformationen des Dienstprinzipals, die Sie im vorherigen Schritt erstellt haben.

Erstellen Sie eine Datei namens *windows.json*, und fügen Sie den folgenden Inhalt ein. Geben Sie eigene Werte für Folgendes ein:

| Parameter                           | Bezugsquelle |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Anzeigen der Dienstprinzipal-ID mit `$sp.applicationId` |
| *client_secret*                     | Anzeigen des automatisch generierten Kennworts mit `$plainPassword` |
| *tenant_id*                         | Ausgabe des Befehls `$sub.TenantId` |
| *subscription_id*                   | Ausgabe des Befehls `$sub.SubscriptionId` |
| *managed_image_resource_group_name* | Name der Ressourcengruppe, die Sie im ersten Schritt erstellt haben |
| *managed_image_name*                | Name für das Image des verwalteten Datenträgers, das erstellt wird |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "client_secret": "ppppppp-pppp-pppp-pppp-ppppppppppp",
    "tenant_id": "zzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz",
    "subscription_id": "yyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyy",

    "managed_image_resource_group_name": "myPackerGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": true,
    "winrm_insecure": true,
    "winrm_timeout": "5m",
    "winrm_username": "packer",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "& $env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit",
      "while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 10  } else { break } }"
    ]
  }]
}
```

Diese Vorlage erstellt eine VM mit Windows Server 2016, installiert IIS und generalisiert dann den virtuellen Computer mit Sysprep. Die IIS-Installation zeigt, wie die PowerShell-Bereitstellung zusätzliche Befehle ausführen kann. Das endgültige Packer-Image enthält dann die erforderliche Softwareinstallation und -konfiguration.


## <a name="build-packer-image"></a>Erstellen des Packer-Images
Wenn Packer noch nicht auf dem lokalen Computer installiert sein sollte, [befolgen Sie die Installationsanweisungen für Packer](https://www.packer.io/docs/install/index.html).

Erstellen Sie das Image, indem Sie eine Befehlseingabeaufforderung öffnen und Ihre Packer-Vorlagendatei wie folgt angeben:

```
./packer build windows.json
```

Ein Beispiel der Ausgabe der vorherigen Befehle lautet wie folgt:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> task : Image deployment
==> azure-arm:  ->> dept : Engineering
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the certificate’s URL ...
==> azure-arm:  -> Key Vault Name        : ‘pkrkvpq0mthtbtt’
==> azure-arm:  -> Key Vault Secret Name : ‘packerKeyVaultSecret’
==> azure-arm:  -> Certificate URL       : ‘https://pkrkvpq0mthtbtt.vault.azure.net/secrets/packerKeyVaultSecret/8c7bd823e4fa44e1abb747636128adbb'
==> azure-arm: Setting the certificate’s URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.55.35’
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-powershell-provisioner902510110
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version=“1.1.0.1” xmlns=“http://schemas.microsoft.com/powershell/2004/04"><Obj S=“progress” RefId=“0"><TN RefId=“0”><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N=“SourceId”>1</I64><PR N=“Record”><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-pq0mthtbtt/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Compute Name              : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Es dauert einige Minuten, bis Packer die VM erstellt, die Provisioner ausgeführt und die Bereitstellung bereinigt hat.


## <a name="create-a-vm-from-the-packer-image"></a>Erstellen virtueller Computer aus dem Packer-Image
Sie können nun mit [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) einen virtuellen Computer anhand Ihres Images erstellen. Die unterstützenden Netzwerkressourcen werden erstellt, falls sie nicht bereits vorhanden sind. Geben Sie, wenn Sie dazu aufgefordert werden, einen Administratorbenutzernamen mit entsprechendem Kennwort ein, um die Erstellung auf der VM zu ermöglichen. Im folgenden Beispiel wird ein virtueller Computer namens *myVM* anhand von *myPackerImage* erstellt:

```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Image "myPackerImage"
```

Wenn Sie VMs in einer anderen Ressourcengruppe oder Region als Ihr Packerimage erstellen möchten, geben Sie die Image-ID und nicht den Imagenamen an. Sie können die Image-ID mit [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/Get-AzImage) abrufen.

Das Erstellen der VM anhand Ihres Packer-Images dauert einige Minuten.


## <a name="test-vm-and-webserver"></a>Testen von VM und Webserver
Rufen Sie mit [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) die öffentliche IP-Adresse Ihres virtuellen Computers ab. Im folgenden Beispiel wird die IP-Adresse für *myPublicIP* abgerufen, die wir zuvor erstellt haben:

```powershell
Get-AzPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

Um Ihre VM in Aktion zu sehen, die die IIS-Installation von der Packer-Bereitstellung enthält, geben Sie die öffentliche IP-Adresse in einen Webbrowser ein.

![IIS-Standardwebsite](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>Nächste Schritte
Sie können mit [Azure Image Builder](image-builder.md) auch vorhandene Packer Provisioner-Skripts verwenden.

---
title: Schnellstart für PowerShell in Azure Cloud Shell | Microsoft-Dokumentation
description: Schnellstart für PowerShell in Cloud Shell
services: Azure
documentationcenter: ''
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: damaerte
ms.openlocfilehash: 1fc9883e0ea35c384c3bfc83e76b8eded48cbcba
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60199526"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell"></a>Schnellstart für PowerShell in Azure Cloud Shell

Dieses Dokument erläutert die Verwendung von PowerShell in Cloud Shell im [Azure-Portal](https://portal.azure.com/).

> [!NOTE]
> Ein Schnellstart zu [Bash in Azure Cloud Shell](quickstart.md) ist ebenfalls verfügbar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="start-cloud-shell"></a>Starten von Cloud Shell

1. Klicken Sie im oberen Navigationsbereich des Azure-Portals auf die Schaltfläche **Cloud Shell**.

   ![](media/quickstart-powershell/shell-icon.png)

2. Wählen Sie die PowerShell-Umgebung aus der Dropdownliste aus, und Sie befindet sich auf dem Azure-Laufwerk `(Azure:)`.

   ![](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>Ausführen von PowerShell-Befehlen

Führen Sie reguläre PowerShell-Befehle wie in der Cloud Shell aus. Beispiel:

```azurepowershell-interactive
PS Azure:\> Get-Date

# Expected Output
Friday, July 27, 2018 7:08:48 AM

PS Azure:\> Get-AzVM -Status

# Expected Output
ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

## <a name="navigate-azure-resources"></a>Navigieren in Azure-Ressourcen

 1. Auflisten Ihrer gesamten Abonnements auf dem `Azure`-Laufwerk

    ```azurepowershell-interactive
    PS Azure:\> dir
    ```

 2. Führen Sie `cd` aus, um zu Ihrem bevorzugten Abonnement zu gelangen.

    ```azurepowershell-interactive
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. Zeigen Sie alle Azure-Ressourcen unter dem aktuellen Abonnement an.

    Geben Sie ein `dir`, um mehrere Ansichten der Azure-Ressourcen aufzulisten.

    ```azurepowershell-interactive
    PS Azure:\MySubscriptionName> dir

        Directory: azure:\MySubscriptionName

    Mode Name
    ---- ----
    +    AllResources
    +    ResourceGroups
    +    StorageAccounts
    +    VirtualMachines
    +    WebApps
    ```

### <a name="allresources-view"></a>AllResources-Ansicht

Geben Sie `dir` unter dem Verzeichnis `AllResources` ein, um Ihre Azure-Ressourcen anzuzeigen.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir AllResources
```

### <a name="explore-resource-groups"></a>Untersuchen von Ressourcengruppen

 Sie können zum Verzeichnis `ResourceGroups` wechseln und innerhalb einer bestimmten Ressourcengruppe virtuelle Computer suchen.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> cd ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines

PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines> dir


    Directory: Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines


VMName    Location   ProvisioningState VMSize          OS            SKU             OSVersion AdminUserName  NetworkInterfaceName
------    --------   ----------------- ------          --            ---             --------- -------------  --------------------
TestVm1   westus     Succeeded         Standard_DS2_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo371
TestVm2   westus     Succeeded         Standard_DS1_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo271
```

> [!NOTE]
> Sie werden möglicherweise feststellen, dass Cloud Shell bei der zweiten Eingabe von `dir` in der Lage ist, die Elemente wesentlich schneller anzuzeigen.
> Das liegt daran, dass die untergeordneten Elemente im Arbeitsspeicher erstellt werden, um eine höhere Benutzerfreundlichkeit zu erreichen.
Sie können jedoch immer `dir -Force` verwenden, um aktuelle Daten abzurufen.

### <a name="navigate-storage-resources"></a>Navigieren in Speicherressourcen

Im Verzeichnis `StorageAccounts` können Sie ganz einfach durch Ihre gesamten Speicherressourcen navigieren.

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files

Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>
```

Mit der Verbindungszeichenfolge können Sie den folgenden Befehl verwenden, um die Azure Files-Freigabe einzubinden.

```azurepowershell-interactive
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>
```

Weitere Informationen finden Sie unter [Einbinden einer Azure Files-Freigabe und Zugreifen auf die Freigabe unter Windows][azmount].

Sie können auch wie folgt durch die Verzeichnisse unter der Azure Files-Freigabe navigieren:

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1
```

### <a name="interact-with-virtual-machines"></a>Interagieren mit virtuellen Computern

Sie finden all Ihre virtuellen Computer unter dem aktuellen Abonnement über das `VirtualMachines`-Verzeichnis.

```azurepowershell-interactive
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running
```

#### <a name="invoke-powershell-script-across-remote-vms"></a>Aufrufen von PowerShell-Skripts zwischen virtuellen Remotecomputern

 > [!WARNING]
 > Weitere Informationen finden Sie unter [Problembehandlung bei der Remoteverwaltung von virtuellen Azure-Computern](troubleshooting.md#troubleshooting-remote-management-of-azure-vms).

  Angenommen, Sie verfügen über einen virtuellen Computer, MyVM1, dann verwenden wir `Invoke-AzVMCommand`, um einen PowerShell-Skriptblock auf dem Remotecomputer aufzurufen.

  ```azurepowershell-interactive
  Invoke-AzVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -EnableRemoting
  ```

  Sie können auch zuerst zum Verzeichnis „VirtualMachines“ navigieren und `Invoke-AzVMCommand` wie folgt ausführen.

  ```azurepowershell-interactive
  PS Azure:\> cd MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzVMCommand -Scriptblock {Get-ComputerInfo}

  # You will see output similar to the following:

  PSComputerName                                          : 65.52.28.207
  RunspaceId                                              : 2c2b60da-f9b9-4f42-a282-93316cb06fe1
  WindowsBuildLabEx                                       : 14393.1066.amd64fre.rs1_release_sec.170327-1835
  WindowsCurrentVersion                                   : 6.3
  WindowsEditionId                                        : ServerDatacenter
  WindowsInstallationType                                 : Server
  WindowsInstallDateFromRegistry                          : 5/18/2017 11:26:08 PM
  WindowsProductId                                        : 00376-40000-00000-AA947
  WindowsProductName                                      : Windows Server 2016 Datacenter
  WindowsRegisteredOrganization                           :
   ...
  ```

#### <a name="interactively-log-on-to-a-remote-vm"></a>Interaktives Anmelden auf einem virtuellen Remotecomputer

Mithilfe von `Enter-AzVM` können Sie sich interaktiv auf einem virtuellen Computer anmelden, der in Azure ausgeführt wird.

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzVM -Name MyVM1 -ResourceGroupName MyResourceGroup -EnableRemoting
  ```

Sie können auch zuerst zum Verzeichnis `VirtualMachines` navigieren und `Enter-AzVM` wie folgt ausführen.

  ```azurepowershell-interactive
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzVM
 ```

### <a name="discover-webapps"></a>Erkunden von WebApps

Im Verzeichnis `WebApps` können Sie ganz einfach durch Ihre Web-App-Ressourcen navigieren.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir .\WebApps\

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US

# You can use Azure cmdlets to Start/Stop your web apps
PS Azure:\MySubscriptionName\WebApps> Start-AzWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1

Name           State    ResourceGroup        EnabledHostNames                   Location
----           -----    -------------        ----------------                   --------
mywebapp1      Running  MyResourceGroup1     {mywebapp1.azurewebsites.net ...   West US

# Refresh the current state with -Force
PS Azure:\MySubscriptionName\WebApps> dir -Force

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Running  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US
```

## <a name="ssh"></a>SSH

Generieren Sie zum Authentifizieren für Server oder VMs per SSH das Paar aus einem öffentlichen und einem privaten Schlüssel in Cloud Shell, und veröffentlichen Sie den öffentlichen Schlüssel auf dem Remotecomputer unter `authorized_keys`, z.B. als `/home/user/.ssh/authorized_keys`.

> [!NOTE]
> Sie können private/öffentliche SSH-Schlüssel mit `ssh-keygen` erstellen und für `$env:USERPROFILE\.ssh` in Cloud Shell veröffentlichen.

### <a name="using-ssh"></a>Verwenden von SSH

Befolgen Sie die Anleitung [hier](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-powershell), um eine neue VM-Konfiguration mit Azure PowerShell-Cmdlets zu erstellen.
Fügen Sie der VM-Konfiguration vor dem Aufrufen von `New-AzVM` zum Starten der Bereitstellung den öffentlichen SSH-Schlüssel hinzu.
Die neu erstellte VM enthält den öffentlichen Schlüssel unter `~\.ssh\authorized_keys`, um für die VM eine SSH-Sitzung ohne Anmeldeinformationen zu ermöglichen.

```azurepowershell-interactive
# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH keys in Cloud Shell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH keys
$sshPublicKey = Get-Content "$HOME\.ssh\id_rsa.pub"
Add-AzVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# SSH to the VM
ssh azureuser@MyVM.Domain.Com
```

## <a name="list-available-commands"></a>Auflisten verfügbarer Befehle

Geben Sie unter dem Laufwerk `Azure` die Zeichenfolge `Get-AzCommand` ein, um kontextabhängige Azure-Befehle zu erhalten.

Alternativ können Sie immer `Get-Command *az* -Module Az.*` verwenden, um die verfügbaren Azure-Befehle zu ermitteln.

## <a name="install-custom-modules"></a>Installieren von benutzerdefinierten Modulen

Sie können `Install-Module` ausführen, um Module aus dem [PowerShell-Katalog][gallery] zu installieren.

## <a name="get-help"></a>Get-Help

Geben Sie `Get-Help` ein, um Informationen zu PowerShell in Azure Cloud Shell abzurufen.

```azurepowershell-interactive
Get-Help
```

Für einen bestimmten Befehl können Sie weiterhin `Get-Help` gefolgt von einem Cmdlet ausführen.

```azurepowershell-interactive
Get-Help Get-AzVM
```

## <a name="use-azure-files-to-store-your-data"></a>Verwenden von Azure Files zum Speichern Ihrer Daten

Sie können ein Skript erstellen (beispielsweise `helloworld.ps1`) und es in Ihrem `clouddrive` speichern, um es für verschiedene Shellsitzungen zu verwenden.

```azurepowershell-interactive
cd $HOME\clouddrive
# Create a new file in clouddrive directory
New-Item helloworld.ps1
# Open the new file for editing
code .\helloworld.ps1
# Add the content, such as 'Hello World!'
.\helloworld.ps1
Hello World!
```

Wenn Sie PowerShell das nächste Mal in Cloud Shell verwenden, ist die Datei `helloworld.ps1` im Verzeichnis `$HOME\clouddrive` enthalten, der Ihre Azure Files-Freigabe einbindet.

## <a name="use-custom-profile"></a>Verwenden benutzerdefinierter Profile

Sie können die PowerShell-Umgebung anpassen, indem Sie PowerShell-Profile – `profile.ps1` (oder `Microsoft.PowerShell_profile.ps1`) – erstellen.
Speichern Sie es unter `$profile.CurrentUserAllHosts` (oder `$profile.CurrentUserAllHosts`), damit es in jeder PowerShell in Cloud Shell-Sitzungen geladen werden kann.

Informationen zum Erstellen von Profilen finden Sie unter [Informationen zu Profilen][profile].

## <a name="use-git"></a>Verwenden von Git

Wenn Sie in Cloud Shell ein Git-Repository klonen möchten, müssen Sie ein [persönliches Zugriffstoken][githubtoken] erstellen und es als Benutzername verwenden. Wenn Sie über das Token verfügen, gehen Sie wie folgt vor, um das Repository zu klonen:

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## <a name="exit-the-shell"></a>Beenden Sie die Shell:

Geben Sie `exit` ein, um die Sitzung zu beenden.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/about/about_profiles
[azmount]: https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/

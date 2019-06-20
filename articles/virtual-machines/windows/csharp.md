---
title: Erstellen und Verwalten eines virtuellen Azure-Computers mit C# | Microsoft-Dokumentation
description: Verwenden Sie C# und Azure Resource Manager, um einen virtuellen Computer und alle unterstützenden Ressourcen bereitzustellen.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 87524373-5f52-4f4b-94af-50bf7b65c277
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 55b73f5f4e6998eb1eb8c5ebc873fa20f8722a3e
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304590"
---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Erstellen und Verwalten von virtuellen Windows-Computern in Azure mithilfe von C# #

Für einen [virtuellen Azure-Computer](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) sind mehrere unterstützende Azure-Ressourcen erforderlich. In diesem Artikel wird das Erstellen, Verwalten und Löschen virtueller Computerressourcen mithilfe von C# behandelt. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Erstellen eines Visual Studio-Projekts
> * Installieren des Pakets
> * Erstellen von Anmeldeinformationen
> * Erstellen von Ressourcen
> * Ausführen von Verwaltungsaufgaben
> * Löschen von Ressourcen
> * Ausführen der Anwendung

Die Ausführung dieser Schritte dauert etwa 20 Minuten.

## <a name="create-a-visual-studio-project"></a>Erstellen eines Visual Studio-Projekts

1. Wenn Sie dies noch nicht getan haben, installieren Sie [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Wählen Sie auf der Seite „Workloads“ **.NET-Desktopentwicklung** aus, und klicken Sie dann auf **Installieren**. In der Zusammenfassung sehen Sie, dass **Entwicklungstools für .NET Framework 4 – 4.6** automatisch für Sie ausgewählt ist. Wenn Sie Visual Studio bereits installiert haben, können Sie die .NET-Workload mit dem Visual Studio-Startprogramm hinzufügen.
2. Klicken Sie in Visual Studio auf **Datei** > **Neu** > **Projekt**.
3. Wählen Sie unter **Vorlagen** > **Visual C#** die Option **Konsolen-App (.NET Framework)** aus, geben Sie *myDotnetProject* als Name des Projekts ein, wählen Sie den Projektspeicherort, und klicken Sie dann auf **OK**.

## <a name="install-the-package"></a>Installieren des Pakets

NuGet-Pakete sind die einfachste Möglichkeit, um die Bibliotheken zu installieren, die Sie zum Ausführen dieser Schritte benötigen. Gehen Sie wie folgt vor, um die in Visual Studio benötigten Bibliotheken zu erhalten:

1. Klicken Sie auf **Extras** > **NuGet-Paket-Manager** und dann auf **Paket-Manager-Konsole**.
2. Geben Sie folgenden Befehl in die Konsole ein:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>Erstellen von Anmeldeinformationen

Bevor Sie mit diesem Schritt beginnen, stellen Sie sicher, dass Sie Zugriff auf einen [Active Directory-Dienstprinzipal](../../active-directory/develop/howto-create-service-principal-portal.md) haben. Sie sollten auch die Anwendungs-ID, den Authentifizierungsschlüssel und die Mandanten-ID aufzeichnen. Sie benötigen diese Angaben in einem späteren Schritt.

### <a name="create-the-authorization-file"></a>Erstellen der Autorisierungsdatei

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf *myDotnetProject* > **Hinzufügen** > **Neues Element**, und wählen Sie dann **Textdatei** in *Visual C#-Elemente* aus. Nennen Sie die Datei *azureauth.properties*, und klicken Sie dann auf **Hinzufügen**.
2. Fügen Sie diese Autorisierungseigenschaften hinzu:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.windows.net/
    ```

    Ersetzen Sie **&lt;subscription-id&gt;** durch Ihre Abonnement-ID, **&lt;application-id&gt;** durch die Active Directory-Anwendungs-ID, **&lt;authentication-key&gt;** durch den Anwendungsschlüssel und **&lt;tenant-id&gt;** durch die Mandanten-ID.

3. Speichern Sie die Datei „azureauth.properties“. 
4. Legen Sie eine Umgebungsvariable in Windows mit dem Namen AZURE_AUTH_LOCATION mit vollständigem Pfad zur erstellten Autorisierungsdatei fest. Sie können z.B. den folgende PowerShell-Befehl verwenden:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>Erstellen des Verwaltungsclients

1. Öffnen Sie die Datei „Program.cs“ für das von Ihnen erstellte Projekt. Fügen Sie dann am Anfang der Datei den vorhandenen Anweisungen die folgenden using-Anweisungen hinzu:

    ```
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    ```

2. Fügen Sie der „Main“-Methode den folgenden Code hinzu, um den Verwaltungsclient zu erstellen:

    ```
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-resources"></a>Erstellen von Ressourcen

### <a name="create-the-resource-group"></a>Ressourcengruppe erstellen

Alle Ressourcen müssen in einer [Ressourcengruppe](../../azure-resource-manager/resource-group-overview.md) enthalten sein.

Zum Angeben von Werten für die Anwendung und Erstellen der Ressourcengruppe fügen Sie der „Main“-Methode diesen Code hinzu:

```
var groupName = "myResourceGroup";
var vmName = "myVM";
var location = Region.USWest;
    
Console.WriteLine("Creating resource group...");
var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

### <a name="create-the-availability-set"></a>Erstellen der Verfügbarkeitsgruppe

[Verfügbarkeitsgruppen](tutorial-availability-sets.md) erleichtern die Verwaltung der virtuellen Computer, die von Ihrer Anwendung verwendet werden.

Fügen Sie der „Main“-Methode den folgenden Code hinzu, um die Verfügbarkeitsgruppe zu erstellen:

```
Console.WriteLine("Creating availability set...");
var availabilitySet = azure.AvailabilitySets.Define("myAVSet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithSku(AvailabilitySetSkuTypes.Managed)
    .Create();
```

### <a name="create-the-public-ip-address"></a>Erstellen der öffentlichen IP-Adresse

Eine öffentliche [IP-Adresse](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) ist für die Kommunikation mit dem virtuellen Computer erforderlich.

Fügen Sie der „Main“-Methode diesen Code hinzu, um die öffentliche IP-Adresse des virtuellen Computers zu erstellen:
   
```
Console.WriteLine("Creating public IP address...");
var publicIPAddress = azure.PublicIPAddresses.Define("myPublicIP")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithDynamicIP()
    .Create();
```

### <a name="create-the-virtual-network"></a>Erstellen des virtuellen Netzwerks

Ein virtueller Computer muss in einem Subnetz eines [virtuellen Netzwerks](../../virtual-network/virtual-networks-overview.md) enthalten sein.

Fügen Sie der „Main“-Methode diesen Code hinzu, um ein Subnetz und ein virtuelles Netzwerk zu erstellen:

```
Console.WriteLine("Creating virtual network...");
var network = azure.Networks.Define("myVNet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithAddressSpace("10.0.0.0/16")
    .WithSubnet("mySubnet", "10.0.0.0/24")
    .Create();
```

### <a name="create-the-network-interface"></a>Erstellen der Netzwerkschnittstelle

Ein virtueller Computer benötigt eine Netzwerkschnittstelle, um in dem virtuellen Netzwerk kommunizieren zu können.

Fügen Sie der „Main“-Methode diesen Code hinzu, um eine Netzwerkschnittstelle zu erstellen:

```
Console.WriteLine("Creating network interface...");
var networkInterface = azure.NetworkInterfaces.Define("myNIC")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetwork(network)
    .WithSubnet("mySubnet")
    .WithPrimaryPrivateIPAddressDynamic()
    .WithExistingPrimaryPublicIPAddress(publicIPAddress)
    .Create();
 ```

### <a name="create-the-virtual-machine"></a>Erstellen des virtuellen Computers

Nachdem Sie nun alle benötigten Ressourcen erstellt haben, können Sie einen virtuellen Computer erstellen.

Fügen Sie der „Main“-Methode diesen Code hinzu, um den virtuellen Computer zu erstellen:

```
Console.WriteLine("Creating virtual machine...");
azure.VirtualMachines.Define(vmName)
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .WithAdminUsername("azureuser")
    .WithAdminPassword("Azure12345678")
    .WithComputerName(vmName)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

> [!NOTE]
> In diesem Tutorial wird eine virtuelle Maschine erstellt, auf dem eine Version des Windows Server-Betriebssystems ausgeführt wird. Weitere Informationen zur Auswahl von anderen Images finden Sie unter [Navigieren zu und Auswählen von Images virtueller Linux-Computer in Azure mithilfe der Befehlszeilenschnittstelle oder PowerShell](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Wenn Sie einen vorhandenen Datenträger anstelle eines Marketplace-Images verwenden möchten, verwenden Sie diesen Code:

```
var managedDisk = azure.Disks.Define("myosdisk")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .WithSizeInGB(128)
    .WithSku(DiskSkuTypes.PremiumLRS)
    .Create();

azure.VirtualMachines.Define("myVM")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

## <a name="perform-management-tasks"></a>Ausführen von Verwaltungsaufgaben

Während der Lebensdauer eines virtuellen Computers können Sie Verwaltungsaufgaben wie das Starten, Beenden oder Löschen eines virtuellen Computers ausführen. Darüber hinaus empfiehlt es sich, Code zum Automatisieren von wiederkehrenden oder komplexen Aufgaben zu erstellen.

Wenn Sie mit dem virtuellen Computer Aufgaben ausführen müssen, benötigen Sie eine Instanz davon:

```
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>Abrufen von Informationen zum virtuellen Computer

Fügen Sie der „Main“-Methode diesen Code hinzu, um Informationen zum virtuellen Computer abzurufen:

```
Console.WriteLine("Getting information about the virtual machine...");
Console.WriteLine("hardwareProfile");
Console.WriteLine("   vmSize: " + vm.Size);
Console.WriteLine("storageProfile");
Console.WriteLine("  imageReference");
Console.WriteLine("    publisher: " + vm.StorageProfile.ImageReference.Publisher);
Console.WriteLine("    offer: " + vm.StorageProfile.ImageReference.Offer);
Console.WriteLine("    sku: " + vm.StorageProfile.ImageReference.Sku);
Console.WriteLine("    version: " + vm.StorageProfile.ImageReference.Version);
Console.WriteLine("  osDisk");
Console.WriteLine("    osType: " + vm.StorageProfile.OsDisk.OsType);
Console.WriteLine("    name: " + vm.StorageProfile.OsDisk.Name);
Console.WriteLine("    createOption: " + vm.StorageProfile.OsDisk.CreateOption);
Console.WriteLine("    caching: " + vm.StorageProfile.OsDisk.Caching);
Console.WriteLine("osProfile");
Console.WriteLine("  computerName: " + vm.OSProfile.ComputerName);
Console.WriteLine("  adminUsername: " + vm.OSProfile.AdminUsername);
Console.WriteLine("  provisionVMAgent: " + vm.OSProfile.WindowsConfiguration.ProvisionVMAgent.Value);
Console.WriteLine("  enableAutomaticUpdates: " + vm.OSProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);
Console.WriteLine("networkProfile");
foreach (string nicId in vm.NetworkInterfaceIds)
{
    Console.WriteLine("  networkInterface id: " + nicId);
}
Console.WriteLine("vmAgent");
Console.WriteLine("  vmAgentVersion" + vm.InstanceView.VmAgent.VmAgentVersion);
Console.WriteLine("    statuses");
foreach (InstanceViewStatus stat in vm.InstanceView.VmAgent.Statuses)
{
    Console.WriteLine("    code: " + stat.Code);
    Console.WriteLine("    level: " + stat.Level);
    Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
    Console.WriteLine("    message: " + stat.Message);
    Console.WriteLine("    time: " + stat.Time);
}
Console.WriteLine("disks");
foreach (DiskInstanceView disk in vm.InstanceView.Disks)
{
    Console.WriteLine("  name: " + disk.Name);
    Console.WriteLine("  statuses");
    foreach (InstanceViewStatus stat in disk.Statuses)
    {
        Console.WriteLine("    code: " + stat.Code);
        Console.WriteLine("    level: " + stat.Level);
        Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
        Console.WriteLine("    time: " + stat.Time);
    }
}
Console.WriteLine("VM general status");
Console.WriteLine("  provisioningStatus: " + vm.ProvisioningState);
Console.WriteLine("  id: " + vm.Id);
Console.WriteLine("  name: " + vm.Name);
Console.WriteLine("  type: " + vm.Type);
Console.WriteLine("  location: " + vm.Region);
Console.WriteLine("VM instance status");
foreach (InstanceViewStatus stat in vm.InstanceView.Statuses)
{
    Console.WriteLine("  code: " + stat.Code);
    Console.WriteLine("  level: " + stat.Level);
    Console.WriteLine("  displayStatus: " + stat.DisplayStatus);
}
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="stop-the-vm"></a>Beenden des virtuellen Computers

Sie können einen virtuellen Computer beenden und sämtliche Einstellungen beibehalten – dafür werden allerdings weiterhin Gebühren berechnet –, oder Sie können einen virtuellen Computer beenden und die Zuordnung aufheben. Wenn die Zuordnung eines virtuellen Computers aufgehoben wird, wird auch die Zuordnung aller mit diesem Computer verknüpften Ressourcen aufgehoben, und die Abrechnung wird beendet.

Zum Beenden des virtuellen Computers ohne Aufheben seiner Zuordnung fügen Sie der „Main“-Methode diesen Code hinzu:

```
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

Wenn Sie die Zuordnung des virtuellen Computers aufheben möchten, ändern Sie den Aufruf zum Herunterfahren in folgenden Code:

```
vm.Deallocate();
```

### <a name="start-the-vm"></a>Starten der VM

Fügen Sie der „Main“-Methode diesen Code hinzu, um den virtuellen Computer zu starten:

```
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>Ändern der Größe der VM

Viele Aspekte der Bereitstellung müssen berücksichtigt werden, wenn Sie die Größe für den virtuellen Computer festlegen. Weitere Informationen finden Sie unter [VM-Größen](sizes.md).  

Fügen Sie der „Main“-Methode diesen Code hinzu, um die Größe des virtuellen Computers zu ändern:

```
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Hinzufügen eines Datenträgers zur VM

Um dem virtuellen Computer einen Datenträger hinzuzufügen, fügen Sie der „Main“-Methode diesen Code hinzu. In diesem Beispiel wird ein Datenträger der Größe 2 GB hinzugefügt, mit der LUN „0“ und dem Cachetyp „ReadWrite“:

```
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>Löschen von Ressourcen

Da in Azure die genutzten Ressourcen in Rechnung gestellt werden, empfiehlt es sich grundsätzlich, nicht mehr benötigte Ressourcen zu löschen. Um den virtuellen Computer und alle unterstützenden Ressourcen zu löschen, müssen Sie lediglich die Ressourcengruppe löschen.

Fügen Sie der „Main“-Methode diesen Code hinzu, um die Ressourcengruppe zu löschen:

```
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Ausführen der Anwendung

Die vollständige Ausführung dieser Konsolenanwendung sollte etwa fünf Minuten dauern. 

1. Um die Konsolenanwendung auszuführen, klicken Sie auf **Starten**.

2. Bevor Sie die **EINGABETASTE** drücken, um das Löschen der Ressourcen zu starten, können Sie sich ein paar Minuten Zeit nehmen, um die Erstellung der Ressourcen im Azure-Portal zu überprüfen. Klicken Sie auf den Bereitstellungsstatus, um Informationen zur Bereitstellung anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte
* Nutzen Sie die Vorteile der Erstellung eines virtuellen Computers per Vorlage, indem Sie sich die Informationen unter [Bereitstellen eines virtuellen Azure-Computers mit C# und einer Resource Manager-Vorlage](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)durchlesen.
* Erfahren Sie mehr zur Verwendung der [Azure-Bibliotheken für .NET](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet).


---
title: Erstellen eines virtuellen Computers (klassisch) mit mehreren Netzwerkkarten – Azure PowerShell | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie über PowerShell einen virtuellen Computer (klassisch) mit mehreren Netzwerkkarten erstellen.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 6e50f39a-2497-4845-a5d4-7332dbc203c5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 087b52bd603e8aed6078ab340e84c1f6bd0e8082
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60748489"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-powershell"></a>Erstellen eines virtuellen Computers (VM) (klassisch) mit mehreren Netzwerkkarten über PowerShell

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Sie können virtuelle Computer (VMs) in Azure erstellen und jedem virtuellen Computer mehrere Netzwerkkarten (NICs) zuordnen. Mehrere NICs ermöglichen die Trennung von Datenverkehrstypen für alle NICs. Beispiel: Eine NIC kommuniziert mit dem Internet, während eine andere nur mit internen Ressourcen kommuniziert, für die keine Internetverbindung besteht. Die Möglichkeit, Netzwerkdatenverkehr auf mehreren NICs zu trennen, ist für zahlreiche virtuelle Netzwerkgeräte erforderlich, beispielsweise für Lösungen zur Anwendungsbereitstellung und WAN-Optimierung.

> [!IMPORTANT]
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen:  [das Resource Manager-Modell und das klassische Bereitstellungsmodell](../resource-manager-deployment-model.md). Dieser Artikel befasst sich mit der Verwendung des klassischen Bereitstellungsmodells. Microsoft empfiehlt für die meisten neuen Bereitstellungen die Verwendung des Ressourcen-Manager-Modells. Erfahren Sie, wie Sie diese Schritte mit dem [Resource Manager-Bereitstellungsmodell ausführen](../virtual-machines/windows/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

In den folgenden Schritten verwenden Sie eine Ressourcengruppe mit dem Namen *IaaSStory* für die Webserver und eine Ressourcengruppe mit dem Namen *IaaSStory-BackEnd* für die DB-Server.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie die DB-Server erstellen können, müssen Sie die Ressourcengruppe *IaaSStory* mit den erforderlichen Ressourcen für dieses Szenario erstellen. Führen Sie die folgenden Schritte aus, um diese Ressourcen zu erstellen: Erstellen Sie ein virtuelles Netzwerk, indem Sie die Schritte im Artikel [Erstellen eines virtuellen Netzwerks](virtual-networks-create-vnet-classic-netcfg-ps.md) befolgen.

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>Erstellen der Back-End-VMs
Die Back-End-VMs sind auf die Erstellung der folgenden Ressourcen angewiesen:

* **Back-End-Subnetz**. Die Datenbankserver gehören zum Aufteilen des Datenverkehrs einem separaten Subnetz an. Das folgende Skript erwartet dieses Subnetz in einem Vnet namens *WTestVnet*.
* **Speicherkonto für Datenträger.** Für eine bessere Leistung verwenden die Datenträger auf den Datenbankservern Solid State Drive (SSD)-Technik. Dafür ist ein Storage Premium-Konto erforderlich. Achten Sie darauf, dass der Azure-Speicherort für die Bereitstellung Storage Premium unterstützt.
* **Verfügbarkeitsgruppe**. Alle Datenbankserver werden einer einzigen Verfügbarkeitsgruppe hinzugefügt, damit sichergestellt ist, dass mindestens ein virtueller Computer während der Wartung ausgeführt wird.

### <a name="step-1---start-your-script"></a>Schritt 1: Starten des Skripts
Sie können das verwendete PowerShell-Skript ungekürzt [hier](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1)herunterladen. Gehen Sie folgendermaßen vor, um das Skript an Ihre Arbeitsumgebung anzupassen.

1. Ändern Sie die Werte der nachstehenden Variablen basierend auf der im obigen Abschnitt [Voraussetzungen](#prerequisites)bereitgestellten Ressourcengruppe.

    ```powershell
    $location              = "West US"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    ```

2. Ändern Sie die Werte der nachstehenden Variablen basierend auf den Werten, die Sie für die Back-End-Bereitstellung verwenden möchten.

    ```powershell
    $backendCSName         = "IaaSStory-Backend"
    $prmStorageAccountName = "iaasstoryprmstorage"
    $avSetName             = "ASDB"
    $vmSize                = "Standard_DS3"
    $diskSize              = 127
    $vmNamePrefix          = "DB"
    $dataDiskSuffix        = "datadisk"
    $ipAddressPrefix       = "192.168.2."
    $numberOfVMs           = 2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Schritt 2: Erstellen der erforderlichen Ressourcen für die virtuellen Computer
Sie müssen einen neuen Clouddienst und ein Speicherkonto für die Datenträger für alle virtuellen Computer erstellen. Sie müssen zudem ein Abbild und ein lokales Administratorkonto für die virtuellen Computer angeben. Führen Sie die folgenden Schritte aus, um diese Ressourcen zu erstellen:

1. Erstellen Sie einen neuen Clouddienst.

    ```powershell
    New-AzureService -ServiceName $backendCSName -Location $location
    ```

2. Erstellen Sie ein Premium-Speicherkonto.

    ```powershell
    New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
    -Location $location -Type Premium_LRS
    ```
3. Legen Sie das zuvor erstellte Speicherkonto als aktuelles Speicherkonto für Ihr Abonnement fest.

    ```powershell
    $subscription = Get-AzureSubscription | where {$_.IsCurrent -eq $true}  
    Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
    -CurrentStorageAccountName $prmStorageAccountName
    ```

4. Wählen Sie ein Abbild für den virtuellen Computer aus.

    ```powershell
    $image = Get-AzureVMImage `
    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
    | sort PublishedDate -Descending `
    | select -ExpandProperty ImageName -First 1
    ```

5. Legen Sie die Anmeldeinformationen des lokalen Administratorkontos fest.

    ```powershell
    $cred = Get-Credential -Message "Enter username and password for local admin account"
    ```

### <a name="step-3---create-vms"></a>Schritt 3: Erstellen von virtuellen Computern
Sie müssen die gewünschte Anzahl an virtuellen Computern mithilfe einer Schleife erstellen. Erstellen Sie die erforderlichen Netzwerkkarten und virtuellen Computer innerhalb der Schleife. Führen Sie zum Erstellen der Netzwerkkarten und virtuellen Computer die folgenden Schritte aus.

1. Starten Sie eine `for`-Schleife, um die Befehle zum Erstellen eines virtuellen Computers und zweier Netzwerkkarten auf Grundlage der `$numberOfVMs`-Variablen so oft wie erforderlich zu wiederholen.

    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. Erstellen Sie ein `VMConfig`-Objekt, mit dem Abbild, Größe und Verfügbarkeitsgruppe für den virtuellen Computer festgelegt werden.

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureVMConfig -Name $vmName `
        -ImageName $image `
        -InstanceSize $vmSize `
        -AvailabilitySetName $avSetName
    ```

3. Bereitstellen des virtuellen Computers als Windows-VM.

    ```powershell
    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
        -AdminUsername $cred.UserName `
        -Password $cred.GetNetworkCredential().Password
    ```

4. Legen Sie die Standard-Netzwerkkarte fest, und weisen Sie ihr eine statische IP-Adresse zu.

    ```powershell
    Set-AzureSubnet         -SubnetNames $backendSubnetName -VM $vmConfig
    Set-AzureStaticVNetIP   -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig
    ```

5. Fügen Sie für jeden virtuellen Computer eine zweite Netzwerkkarte hinzu.

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
    -SubnetName $backendSubnetName `
    -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
    -VM $vmConfig
    ```
    
6. Erstellen Sie für jeden virtuellen Computer zwei Datenträger.

    ```powershell
    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk1Name `
    -LUN 0

    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk2Name `
    -LUN 1
    ```

7. Erstellen Sie die virtuellen Computer, und beenden Sie die Schleife.

    ```powershell
    New-AzureVM -VM $vmConfig `
    -ServiceName $backendCSName `
    -Location $location `
    -VNetName $vnetName
    }
    ```

### <a name="step-4---run-the-script"></a>Schritt 4: Ausführen des Skripts
Führen Sie das Skript aus, nachdem sie es heruntergeladen und angepasst haben, um die Back-End-VMs mit mehreren Netzwerkkarten zu erstellen.

1. Speichern Sie Ihr Skript, und führen Sie es an der **PowerShell**-Eingabeaufforderung oder in **PowerShell ISE** aus. Anfänglich wird die folgende Ausgabe angezeigt.

        OperationDescription    OperationId                          OperationStatus

        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        
        WARNING: No deployment found in service: 'IaaSStory-Backend'.
2. Tragen Sie bei der Aufforderung die Anmeldeinformationen ein, und klicken Sie auf **OK**. Die folgende Ausgabe wird zurückgegeben.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Schritt 5: Konfigurieren des Routings im Betriebssystem des virtuellen Computers

Azure DHCP weist der ersten (primären) Netzwerkschnittstelle, die an den virtuellen Computer angefügt ist, ein Standardgateway zu. Azure weist zusätzlichen (sekundären) Netzwerkschnittstellen, die an einen virtuellen Computer angefügt sind, kein Standardgateway zu. Daher können Sie standardmäßig nicht mit Ressourcen außerhalb des Subnetzes kommunizieren, in dem sich eine sekundäre Netzwerkschnittstelle befindet. Sekundäre Netzwerkschnittstellen können jedoch mit Ressourcen außerhalb ihres Subnetzes kommunizieren. Informationen zum Konfigurieren für sekundäre Netzwerkschnittstellen finden Sie in den folgenden Artikeln:

- [Konfigurieren von Gastbetriebssystem für mehrere Netzwerkadapter (Windows)](../virtual-machines/windows/multiple-nics.md#configure-guest-os-for-multiple-nics
)

- [Konfigurieren von Gastbetriebssystem für mehrere Netzwerkadapter (Linux)](../virtual-machines/linux/multiple-nics.md#configure-guest-os-for-multiple-nics
)

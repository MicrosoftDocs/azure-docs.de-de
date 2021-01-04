---
title: Einrichten von Ressourcen für die Bereitstellung eines ML-Modells in Azure SQL Edge
description: Im ersten Teil dieses dreiteiligen Azure SQL Edge-Tutorials zum Vorhersagen von Eisenerzverunreinigungen installieren Sie die erforderliche Software und richten erforderliche Azure-Ressourcen für die Bereitstellung eines Machine Learning-Modells in Azure SQL Edge ein.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: d83745db6c720a2fdc2260a07a4e3e66b1a0771d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422211"
---
# <a name="install-software-and-set-up-resources-for-the-tutorial"></a>Installieren von Software und Einrichten von Ressourcen für das Tutorial

In diesem dreiteiligen Tutorial erstellen Sie ein Machine Learning-Modell zum Vorhersagen von Eisenerzverunreinigungen als Prozentsatz von Kieselsäure und zum anschließenden Bereitstellen des Modells in Azure SQL Edge. In Teil 1 installieren Sie die erforderliche Software und stellen Azure-Ressourcen bereit.

## <a name="prerequisites"></a>Voraussetzungen

1. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen.
2. Installieren von Visual Studio 2019 mit 
      * Azure IoT Edge-Tools
      * plattformübergreifender .NET Core-Entwicklung
      * Containerentwicklungstools
3. Installieren Sie [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio/).
4. Öffnen Sie Azure Data Studio, und konfigurieren Sie Python für Notebooks. Ausführliche Informationen finden Sie unter [Konfigurieren von Python für Notebooks](/sql/azure-data-studio/sql-notebooks#configure-python-for-notebooks). Dieser Schritt kann einige Minuten dauern.
5. Installieren Sie die neueste Version von [Azure CLI](https://github.com/Azure/azure-powershell/releases/tag/v3.5.0-February2020). Für die folgenden Skripts muss AZ PowerShell die neueste Version (3.5.0, Februar 2020) sein.
6. Richten Sie die Umgebung zum Debuggen, Ausführen und Testen der IoT Edge-Lösung ein, indem Sie das [Entwicklertool für Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/) installieren.
7. Installieren Sie Docker.
8. Laden Sie die [DACPAC](https://github.com/microsoft/sql-server-samples/tree/master/samples/demos/azure-sql-edge-demos/iron-ore-silica-impurities/DACPAC)-Datei herunter, die im Tutorial verwendet wird. 

## <a name="deploy-azure-resources-using-powershell-script"></a>Bereitstellen von Azure-Ressourcen mithilfe eines PowerShell-Skripts

Stellen Sie die für dieses Azure SQL Edge-Tutorial erforderlichen Azure-Ressourcen bereit. Sie können entweder mithilfe eines PowerShell-Skripts oder über das Azure-Portal bereitgestellt werden. In diesem Tutorial wird ein PowerShell-Skript verwendet.

1. Importieren Sie die Module, die zum Ausführen des PowerShell-Skripts in diesem Tutorial erforderlich sind.

   ```powershell
   Import-Module Az.Accounts -RequiredVersion 1.7.3
   Import-Module -Name Az -RequiredVersion 3.5.0
   Import-Module Az.IotHub -RequiredVersion 2.1.0
   Import-Module Az.Compute -RequiredVersion 3.5.0
   az extension add --name azure-iot
   az extension add --name azure-cli-ml
   ```

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]


2. Deklarieren Sie die für das PowerShell-Skript erforderlichen Variablen.

   ```powershell
   $ResourceGroup = "<name_of_the_resource_group>"
   $IoTHubName = "<name_of_the_IoT_hub>"
   $location = "<location_of_your_Azure_Subscription>"
   $SubscriptionName = "<your_azure_subscription>"
   $NetworkSecGroup = "<name_of_your_network_security_group>"
   $StorageAccountName = "<name_of_your_storage_account>"
   ```

3. Deklarieren Sie auch die restlichen Variablen.

   ```powershell
   $IoTHubSkuName = "S1"
   $IoTHubUnits = 4
   $EdgeDeviceId = "IronOrePredictionDevice"
   $publicIpName = "VMPublicIP"
   $imageOffer = "iot_edge_vm_ubuntu"
   $imagePublisher = "microsoft_iot_edge"
   $imageSku = "ubuntu_1604_edgeruntimeonly"
   $AdminAcc = "iotadmin"
   $AdminPassword = ConvertTo-SecureString "IoTAdmin@1234" -AsPlainText -Force
   $VMSize = "Standard_DS3"
   $NetworkName = "MyNet"
   $NICName = "MyNIC"
   $SubnetName = "MySubnet"
   $SubnetAddressPrefix = "10.0.0.0/24"
   $VnetAddressPrefix = "10.0.0.0/16"
   $MyWorkSpace = "SQLDatabaseEdgeDemo"
   $containerRegistryName = $ResourceGroup + "ContRegistry"
   ```

4. Wenn Sie mit dem Erstellen von Ressourcen beginnen möchten, melden Sie sich bei Azure an.

   ```powershell
   Login-AzAccount
   
   az login
   ```

5. Legen Sie die ID des Azure-Abonnements fest.

   ```powershell
   Select-AzSubscription -Subscription $SubscriptionName
   az account set --subscription $SubscriptionName
   ```

6. Erstellen Sie die Ressourcengruppe, wenn sie noch nicht vorhanden ist.

   ```powershell
   $rg = Get-AzResourceGroup -Name $ResourceGroup
   if($rg -eq $null)
   {
       Write-Output("Resource Group $ResourceGroup does not exist, creating Resource Gorup")
       New-AzResourceGroup -Name $ResourceGroup -Location $location
   }
   else
   {
       Write-Output ("Resource Group $ResourceGroup exists")
   }
   ```

7. Erstellen Sie das Speicherkonto und den Speicherkontocontainer in der Ressourcengruppe.

   ```powershell
   $sa = Get-AzStorageAccount -ResourceGroupName $ResourceGroup -Name $StorageAccountName 
   if ($sa -eq $null)
   {
       New-AzStorageAccount -ResourceGroupName $ResourceGroup -Name $StorageAccountName -SkuName Standard_LRS -Location $location -Kind Storage
       $sa = Get-AzStorageAccount -ResourceGroupName $ResourceGroup -Name $StorageAccountName 
       $storagekey = Get-AzStorageAccountKey -ResourceGroupName $ResourceGroup -Name $StorageAccountName 
       $storageContext = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $storagekey[0].Value
       New-AzStorageContainer -Name "sqldatabasedacpac" -Context $storageContext 
   }
   else
   {
      Write-Output ("Storage Account $StorageAccountName exists in Resource Group $ResourceGroup")     
   }
   ```

8. Laden Sie die Datenbankdatei `dacpac` in das Speicherkonto hoch, und generieren Sie eine SAS-URL für das Blob. **Notieren Sie sich die SAS-URL für das Datenbankblob `dacpac`.**

   ```powershell
   $file = Read-Host "Please Enter the location to the zipped Database DacPac file:"
   Set-AzStorageBlobContent -File $file -Container "sqldatabasedacpac" -Blob "SQLDatabasedacpac.zip" -Context $sa.Context
   $DacpacFileSASURL = New-AzStorageBlobSASToken -Container "sqldatabasedacpac" -Blob "SQLDatabasedacpac.zip" -Context $sa.Context -Permission r -StartTime (Get-Date).DateTime -ExpiryTime (Get-Date).AddMonths(12) -FullUri
   ```

9. Erstellen Sie eine Azure-Containerregistrierung innerhalb dieser Ressourcengruppe.

   ```powershell
   $containerRegistry = Get-AzContainerRegistry -ResourceGroupName $ResourceGroup -Name $containerRegistryName 
   if ($containerRegistry -eq $null)
   {
       New-AzContainerRegistry -ResourceGroupName $ResourceGroup -Name $containerRegistryName -Sku Standard -Location $location -EnableAdminUser 
       $containerRegistry = Get-AzContainerRegistry -ResourceGroupName $ResourceGroup -Name $containerRegistryName 
   }
   else
   {
       Write-Output ("Container Registry $containerRegistryName exists in Resource Group $ResourceGroup")
   }
   ```

10. Erstellen Sie die Netzwerksicherheitsgruppe innerhalb der Ressourcengruppe.

    ```powershell
    $nsg = Get-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroup -Name $NetworkSecGroup 
    if($nsg -eq $null)
    {
        Write-Output("Network Security Group $NetworkSecGroup does not exist in the resource group $ResourceGroup")
    
        $rule1 = New-AzNetworkSecurityRuleConfig -Name "SSH" -Description "Allow SSH" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22
        $rule2 = New-AzNetworkSecurityRuleConfig -Name "SQL" -Description "Allow SQL" -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1600
        New-AzNetworkSecurityGroup -Name $NetworkSecGroup -ResourceGroupName $ResourceGroup -Location $location -SecurityRules $rule1, $rule2
    
        $nsg = Get-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroup -Name $NetworkSecGroup
    }
    else
    {
        Write-Output ("Network Security Group $NetworkSecGroup exists in the resource group $ResourceGroup")
    }
    ```

11. Erstellen Sie einen virtuellen Azure-Computer (VM), der mit SQL Edge aktiviert wird. Diese VM fungiert als Edgegerät.

    ```powershell
    $AzVM = Get-AzVM -ResourceGroupName $ResourceGroup -Name $EdgeDeviceId
    If($AzVM -eq $null)
    {
        Write-Output("The Azure VM with Name- $EdgeVMName is not present in the Resource Group- $ResourceGroup ")
    
        $SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
        $Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroup -Location $location -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
        $publicIp = New-AzPublicIpAddress -Name $publicIpName -ResourceGroupName $ResourceGroup -AllocationMethod Static -Location $location  
        $NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroup -Location $location -SubnetId $Vnet.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $publicIp.Id
         
        ##Set-AzNetworkInterfaceIpConfig -Name "ipconfig1"  -NetworkInterface $NIC -PublicIpAddress $publicIp
    
        $Credential = New-Object System.Management.Automation.PSCredential ($AdminAcc, $AdminPassword);
    
        $VirtualMachine = New-AzVMConfig -VMName $EdgeDeviceId -VMSize $VMSize
        $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Linux -ComputerName $EdgeDeviceId -Credential $Credential
        $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id  
        $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName $imagePublisher -Offer $imageOffer -Skus $imageSku -Version latest 
        $VirtualMachine = Set-AzVMPlan -VM $VirtualMachine -Name $imageSku -Publisher $imagePublisher -Product $imageOffer
    
        $AzVM = New-AzVM -ResourceGroupName $ResourceGroup -Location $location -VM $VirtualMachine -Verbose
        $AzVM = Get-AzVM -ResourceGroupName $ResourceGroup -Name $EdgeDeviceId
          
    }
    else
    {
        Write-Output ("The Azure VM with Name- $EdgeDeviceId is present in the Resource Group- $ResourceGroup ")
    }
    ```

12. Erstellen Sie einen IoT-Hub innerhalb der Ressourcengruppe.

    ```powershell
    $iotHub = Get-AzIotHub -ResourceGroupName $ResourceGroup -Name $IoTHubName
    If($iotHub -eq $null)
    {
        Write-Output("IoTHub $IoTHubName does not exists, creating The IoTHub in the resource group $ResourceGroup")
        New-AzIotHub -ResourceGroupName $ResourceGroup -Name $IoTHubName -SkuName $IoTHubSkuName -Units $IoTHubUnits -Location $location -Verbose
    }
    else
    {
        Write-Output ("IoTHub $IoTHubName present in the resource group $ResourceGroup") 
    }
    ```

13. Fügen Sie ein Edgegerät zum IoT-Hub hinzu. In diesem Schritt wird nur die digitale Identität für das Gerät erstellt.

    ```powershell
    $deviceIdentity = Get-AzIotHubDevice -ResourceGroupName $ResourceGroup -IotHubName $IoTHubName -DeviceId $EdgeDeviceId
    If($deviceIdentity -eq $null)
    {
        Write-Output("The Edge Device with DeviceId- $EdgeDeviceId is not registered to the IoTHub- $IoTHubName ")
        Add-AzIotHubDevice -ResourceGroupName $ResourceGroup -IotHubName $IoTHubName -DeviceId $EdgeDeviceId -EdgeEnabled  
    }
    else
    {
        Write-Output ("The Edge Device with DeviceId- $EdgeDeviceId is registered to the IoTHub- $IoTHubName")
    }
    $deviceIdentity = Get-AzIotHubDevice -ResourceGroupName $ResourceGroup -IotHubName $IoTHubName -DeviceId $EdgeDeviceId
    ```

14. Rufen Sie die primäre Verbindungszeichenfolge für das Gerät ab. Sie wird später für die VM benötigt. Der folgende Befehl verwendet die Azure-Befehlszeilenschnittstelle (Azure CLI) für Bereitstellungen.

    ```powershell
    $deviceConnectionString = az iot hub device-identity show-connection-string --device-id $EdgeDeviceId --hub-name $IoTHubName --resource-group $ResourceGroup --subscription $SubscriptionName
    $connString = $deviceConnectionString[1].Substring(23,$deviceConnectionString[1].Length-24)
    $connString
    ```

15. Aktualisieren Sie die Verbindungszeichenfolge in der IoT Edge-Konfigurationsdatei auf dem Edgegerät. Die folgenden Befehle verwenden Azure CLI für Bereitstellungen.

    ```powershell
    $script = "/etc/iotedge/configedge.sh '" + $connString + "'"
    az vm run-command invoke -g $ResourceGroup -n $EdgeDeviceId  --command-id RunShellScript --script $script
    ```

16. Erstellen Sie einen Azure Machine Learning-Arbeitsbereich innerhalb der Ressourcengruppe.

    ```powershell
    az ml workspace create -w $MyWorkSpace -g $ResourceGroup
    ```


## <a name="next-steps"></a>Nächste Schritte

* [Einrichten von IoT Edge-Modulen und -Verbindungen](tutorial-set-up-iot-edge-modules.md)

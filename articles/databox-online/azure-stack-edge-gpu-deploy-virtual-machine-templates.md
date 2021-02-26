---
title: Bereitstellen von VMs auf Ihrem Azure Stack Edge Pro-Gerät über Vorlagen
description: Hier erfahren Sie, wie virtuelle Computer auf einem Azure Stack Edge Pro-Gerät anhand von Vorlagen erstellt und verwaltet werden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/25/2021
ms.author: alkohli
ms.openlocfilehash: 9a347d57de540ed31c862f618be7c8a98b685348
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546923"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-templates"></a>Bereitstellen von VMs auf Ihrem Azure Stack Edge Pro-GPU-Gerät über Vorlagen

In diesem Tutorial wird beschrieben, wie Sie virtuelle Computer (Virtual Machines, VMs) auf Ihrem Azure Stack Edge Pro-Gerät anhand von Vorlagen erstellen und verwalten. Bei diesen Vorlagen handelt es sich um JSON-Dateien (JavaScript Object Notation), in denen die Infrastruktur und die Konfiguration für Ihre VM definiert sind. Sie geben in diesen Vorlagen die bereitzustellenden Ressourcen und die Eigenschaften für diese Ressourcen an.

Vorlagen bieten für unterschiedliche Umgebungen eine hohe Flexibilität, da sie während der Laufzeit Parameter aus einer Datei als Eingabe verwenden können. Die standardmäßige Benennungsstruktur ist `TemplateName.json` für die Vorlage und `TemplateName.parameters.json` für die Parameterdatei. Weitere Informationen zu ARM-Vorlagen finden Sie unter [Was sind Azure Resource Manager-Vorlagen?](../azure-resource-manager/templates/overview.md).

In diesem Tutorial werden vorab erstellte Beispielvorlagen zum Erstellen von Ressourcen verwendet. Sie müssen die Vorlagendatei nicht bearbeiten, sondern können lediglich die `.parameters.json`-Dateien ändern, um die Bereitstellung für Ihren Computer anzupassen. 

## <a name="vm-deployment-workflow"></a>VM-Bereitstellungsworkflow

Für die Bereitstellung von Azure Stack Edge Pro-VMs auf einer Vielzahl von Geräten können Sie eine einzelne mit Sysprep vorbereitete VHD für sämtliche Ressourcen nutzen. Außerdem können Sie dieselbe Vorlage für die Bereitstellung verwenden und nur geringfügige Änderungen an den Parametern vornehmen, um die Vorlage für die einzelnen Bereitstellungsorte anzupassen (diese Änderungen können wie in diesem Beispiel manuell oder programmgesteuert vorgenommen werden). 

Nachfolgend finden Sie eine allgemeine Übersicht über den Bereitstellungsworkflow bei Verwendung von Vorlagen:

1. **Konfigurieren der Voraussetzungen**: Voraussetzungen müssen in drei Bereichen erfüllt sein: Gerät, Client und VM.

    1. **Voraussetzungen auf dem Gerät**

        1. Stellen Sie auf dem Gerät eine Verbindung mit Azure Resource Manager her.
        2. Aktivieren Sie die Computefunktion über die lokale Benutzeroberfläche.

    2. **Client-Voraussetzungen**

        1. Laden Sie die VM-Vorlagen und die zugehörigen Dateien auf dem Client herunter.
        1. Konfigurieren Sie optional TLS 1.2 auf dem Client.
        1. [Laden Sie Microsoft Azure Storage-Explorer auf Ihrem Client herunter](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409), und installieren Sie die App.

    3. **VM-Voraussetzungen**

        1. Erstellen Sie eine Ressourcengruppe am Gerätespeicherort, in der sämtliche VM-Ressourcen gespeichert werden.
        1. Erstellen Sie ein Speicherkonto, in das die zum Erstellen des VM-Images verwendete virtuelle Festplatte hochgeladen wird.
        1. Fügen Sie einen lokalen Speicherkonto-URI zum DNS oder zur hosts-Datei auf dem Client hinzu, der auf Ihr Gerät zugreift.
        1. Installieren Sie das Blob Storage-Zertifikat auf dem Gerät und auf dem lokalen Client, der auf Ihr Gerät zugreift. Installieren Sie optional das Blob Storage-Zertifikat im Storage-Explorer.
        1. Erstellen Sie eine virtuelle Festplatte, und laden Sie sie in das zuvor erstellte Speicherkonto hoch.

2. **Erstellen von VMs anhand von Vorlagen**

    1. Erstellen Sie ein VM-Image unter Verwendung der Parameterdatei `CreateImage.parameters.json` und der Bereitstellungsvorlage `CreateImage.json`.
    1. Erstellen Sie unter Verwendung der `CreateVM.parameters.json`-Parameterdatei und der `CreateVM.json`-Bereitstellungsvorlage eine VM mit den zuvor erstellten Ressourcen.

## <a name="device-prerequisites"></a>Voraussetzungen auf dem Gerät

Konfigurieren Sie diese Voraussetzungen auf Ihrem Azure Stack Edge Pro-Gerät.

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]

## <a name="client-prerequisites"></a>Client-Voraussetzungen

Konfigurieren Sie diese Voraussetzungen auf dem Client, über den auf das Azure Stack Edge Pro-Gerät zugegriffen wird.

1. [Laden Sie den Storage-Explorer herunter](https://azure.microsoft.com/features/storage-explorer/), wenn Sie ihn zum Hochladen einer virtuellen Festplatte verwenden. Alternativ können Sie AzCopy herunterladen, um eine virtuelle Festplatte hochzuladen. Wenn Sie ältere Versionen von AzCopy ausführen, müssen Sie möglicherweise TLS 1.2 auf Ihrem Clientcomputer konfigurieren. 
1. [Laden Sie die VM-Vorlagen und Parameterdateien](https://aka.ms/ase-vm-templates) auf Ihrem Clientcomputer herunter. Entpacken Sie die Vorlagen und Dateien in einem Verzeichnis, das Sie als Arbeitsverzeichnis verwenden.


## <a name="vm-prerequisites"></a>VM-Voraussetzungen

Konfigurieren Sie diese Voraussetzungen, um die für die VM-Erstellung benötigten Ressourcen zu erstellen. 

    
### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit [New-AzureRmResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Azure-Ressourcengruppe. Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen wie das Speicherkonto, der Datenträger und der verwaltete Datenträger bereitgestellt und verwaltet werden.

> [!IMPORTANT]
> Alle Ressourcen werden am Speicherort des Geräts erstellt. Dieser Speicherort ist auf **DBELocal** festgelegt.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

Nachfolgend sehen Sie eine Beispielausgabe.

```powershell
PS C:\windows\system32> New-AzureRmResourceGroup -Name myasegpurgvm -Location DBELocal

ResourceGroupName : myasegpurgvm
Location          : dbelocal
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/DDF9FC44-E990-42F8-9A91-5A6A5CC472DB/resourceGroups/myasegpurgvm

PS C:\windows\system32>
```

### <a name="create-a-storage-account"></a>Speicherkonto erstellen

Erstellen Sie unter Verwendung der im vorherigen Schritt erstellten Ressourcengruppe ein neues Speicherkonto. Bei diesem Konto handelt es sich um ein **lokales Speicherkonto**, das zum Hochladen des virtuellen Datenträgerimages für die VM verwendet wird.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Über Azure Resource Manager können ausschließlich lokale Speicherkonten wie lokal redundanter Speicher (Standard_LRS oder Premium_LRS) erstellt werden. Die erforderlichen Schritte zum Erstellen mehrstufiger Speicherkonten finden Sie unter [Hinzufügen von Speicherkonten auf Ihrem Azure Stack Edge Pro-Gerät](azure-stack-edge-j-series-deploy-add-storage-accounts.md).

Nachfolgend sehen Sie eine Beispielausgabe.

```powershell
PS C:\windows\system32> New-AzureRmStorageAccount -Name myasegpusavm -ResourceGroupName myasegpurgvm -Location DBELocal -SkuName Standard_LRS

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime
------------------ ----------------- -------- -------     ----    ---------- ------------
myasegpusavm       myasegpurgvm      DBELocal StandardLRS Storage            7/29/2020 10:11:16 PM

PS C:\windows\system32>
```

Führen Sie den Befehl `Get-AzureRmStorageAccountKey` aus, um den Speicherkontoschlüssel abzurufen. Nachfolgend ist eine Beispielausgabe dieses Befehls gezeigt.

```powershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasegpurgvm
Name: myasegpusavm

KeyName    Value                                                  Permissions   
-------     -----                                                   --
key1 GsCm7QriXurqfqx211oKdfQ1C9Hyu5ZutP6Xl0dqlNNhxLxDesDej591M8y7ykSPN4fY9vmVpgc4ftkwAO7KQ== 11 
key2 7vnVMJUwJXlxkXXOyVO4NfqbW5e/5hZ+VOs+C/h/ReeoszeV+qoyuBitgnWjiDPNdH4+lSm1/ZjvoBWsQ1klqQ== ll
```

### <a name="add-blob-uri-to-hosts-file"></a>Hinzufügen eines Blob-URI zur Datei „hosts“

Stellen Sie sicher, dass der Blob-URI bereits in der hosts-Datei für den Client hinzugefügt wurde, der für die Verbindung mit Blob Storage verwendet wird. **Führen Sie den Editor als Administrator** aus, und fügen Sie den folgenden Eintrag für den Blob-URI in `C:\windows\system32\drivers\etc\hosts` hinzu:

`<Device IP> <storage account name>.blob.<Device name>.<DNS domain>`

In einer typischen Umgebung würden Sie Ihr DNS so konfigurieren, dass alle Speicherkonten mit einem `*.blob.devicename.domainname.com`-Eintrag auf das Azure Stack Edge Pro-Gerät verweisen.

### <a name="optional-install-certificates"></a>(Optional) Installieren von Zertifikaten

Überspringen Sie diesen Schritt, wenn Sie die Verbindung über den Storage-Explorer (unter Verwendung von *http*) herstellen. Bei Verwendung von *https* müssen die erforderlichen Zertifikate im Storage-Explorer installiert werden. In diesem Fall installieren Sie das Blobendpunktzertifikat. Weitere Informationen finden Sie im Abschnitt zum Erstellen und Hochladen von Zertifikaten unter [Verwalten von Zertifikaten](azure-stack-edge-gpu-manage-certificates.md). 

### <a name="create-and-upload-a-vhd"></a>Erstellen und Hochladen einer VHD

Stellen Sie sicher, dass Sie über ein virtuelles Datenträgerimage verfügen, das Sie in einem späteren Schritt zum Hochladen verwenden können. Führen Sie die Schritte unter [Erstellen eines VM-Images](azure-stack-edge-gpu-create-virtual-machine-image.md) aus. 

Kopieren Sie die zu verwendenden Datenträgerimages in Seitenblobs im lokalen Speicherkonto, das Sie in den vorherigen Schritten erstellt haben. Sie können Tools wie den [Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) oder [AzCopy verwenden, um die VHD in das Speicherkonto hochzuladen](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd), das Sie in den vorherigen Schritten erstellt haben. 

### <a name="use-storage-explorer-for-upload"></a>Verwenden des Storage-Explorer für den Upload

1. Öffnen Sie den Storage-Explorer. Wechseln Sie zu **Bearbeiten**, und stellen Sie sicher, dass für die Anwendung **Azure Stack-APIs als Ziel verwenden** festgelegt ist.

    ![Festlegen der Azure Stack-APIs als Ziel](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/set-target-apis-1.png)

2. Installieren Sie das Clientzertifikat im PEM-Format. Wechseln Sie zu **Bearbeiten > SSL-Zertifikate > Zertifikate importieren**. Wählen Sie das Clientzertifikat aus.

    ![Importieren des Zertifikats für den Blob Storage-Endpunkt](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/import-blob-storage-endpoint-certificate-1.png)

    - Wenn Sie vom Gerät generierte Zertifikate verwenden, laden Sie das `.cer`-Zertifikat des Blob Storage-Endpunkts herunter, und konvertieren Sie es in das `.pem`-Format. Führen Sie den folgenden Befehl aus. 
    
        ```powershell
        PS C:\windows\system32> Certutil -encode 'C:\myasegpu1_Blob storage (1).cer' .\blobstoragecert.pem
        Input Length = 1380
        Output Length = 1954
        CertUtil: -encode command completed successfully.
        ```
    - Wenn Sie ein eigenes Zertifikat nutzen, verwenden Sie das Stammzertifikat für die Signaturkette im `.pem`-Format.

3. Starten Sie den Storage-Explorer nach dem Import des Zertifikats neu, damit die Änderungen übernommen werden.

    ![Neustart von Storage-Explorer](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/restart-storage-explorer-1.png)

4. Klicken Sie im linken Bereich mit der rechten Maustaste auf **Speicherkonten**, und wählen Sie **Verbindung mit Azure Storage herstellen** aus. 

    ![Verbindung mit Azure Storage herstellen 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-1.png)

5. Wählen Sie **Speicherkontonamen und -schlüssel verwenden** aus. Wählen Sie **Weiter** aus.

    ![Verbindung mit Azure Storage herstellen 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-2.png)

6. Geben Sie unter **Verbindung mit Name und Schlüssel herstellen** den **Anzeigenamen**, den **Speicherkontonamen** und den **Kontoschlüssel** von Azure Storage an. Wählen Sie für die Speicherdomäne **Andere** aus, und geben Sie die Verbindungszeichenfolge `<device name>.<DNS domain>` ein. Wenn Sie das Zertifikat nicht im Storage-Explorer installiert haben, aktivieren Sie die Option **HTTP verwenden**. Wählen Sie **Weiter** aus.

    ![Verbindung mit Name und Schlüssel herstellen](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-name-key-1.png)

7. Überprüfen Sie die **Verbindungszusammenfassung**, und wählen Sie **Verbinden** aus.

8. Das Speicherkonto wird auf der linken Seite angezeigt. Erweitern Sie das Speicherkonto. Wählen Sie die Option **Blobcontainer** aus, klicken Sie mit der rechten Maustaste darauf, und wählen Sie **Blobcontainer erstellen** aus. Geben Sie einen Namen für Ihren Blobcontainer ein.

9. Wählen Sie den soeben erstellten Container aus, und wählen Sie dann im rechten Bereich **Hochladen > Dateien hochladen** aus. 

    ![Hochladen der VHD-Datei 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-1.png)

10. Wählen Sie unter **Ausgewählte Dateien** die VHD aus, die Sie hochladen möchten. Wählen Sie für **Blobtyp** die Option **Seitenblob** und dann **Hochladen** aus.

    ![Hochladen der VHD-Datei 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-2.png)

11. Nachdem die VHD in den Blobcontainer hochgeladen wurde, wählen Sie die VHD aus, klicken mit der rechten Maustaste und wählen dann **Eigenschaften** aus. 

    ![Hochladen der VHD-Datei 3](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-3.png)

12. Kopieren und speichern Sie den **URI** für die Verwendung in den weiteren Schritten.

    ![Kopieren des URI](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/copy-uri-1.png)


## <a name="create-image-for-your-vm"></a>Erstellen eines Images für Ihren virtuellen Computer

Um das Image für Ihren virtuellen Computer zu erstellen, müssen Sie die Parameterdatei `CreateImage.parameters.json` bearbeiten und anschließend die Vorlage `CreateImage.json` bereitstellen, von der diese Parameterdatei verwendet wird.


### <a name="edit-parameters-file"></a>Bearbeiten der Parameterdatei

Die Datei `CreateImage.parameters.json` kann die folgenden Parameter enthalten: 

```json
"parameters": {
        "osType": {
              "value": "<Operating system corresponding to the VHD you upload can be Windows or Linux>"
        },
        "imageName": {
            "value": "<Name for the VM image>"
        },
        "imageUri": {
              "value": "<Path to the VHD that you uploaded in the Storage account>"
        },
    }
```

Bearbeiten Sie die Datei `CreateImage.parameters.json`, und fügen Sie die folgenden Werte für Ihr Azure Stack Edge Pro-Gerät hinzu:

1. Geben Sie den Betriebssystemtyp der VHD an, die Sie hochladen. Dies kann Windows oder Linux sein.

    ```json
    "parameters": {
            "osType": {
              "value": "Windows"
            },
    ```

2. Ändern Sie den Image-URI in den URI des Images, das Sie im vorherigen Schritt hochgeladen haben:

   ```json
   "imageUri": {
       "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
       },
   ```

   Wenn Sie *http* mit dem Storage-Explorer verwenden, müssen Sie den URI in einen *http*-URI ändern.

3. Geben Sie einen eindeutigen Imagenamen an. Dieses Image wird später zum Erstellen des virtuellen Computers verwendet. 

   Nachfolgend ist die JSON-Beispieldatei gezeigt, die in diesem Artikel verwendet wird.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
      "parameters": {
        "osType": {
          "value": "Linux"
        },
        "imageName": {
          "value": "myaselinuximg"
        },
        "imageUri": {
          "value": "https://sa2.blob.myasegpuvm.wdshcsso.com/con1/ubuntu18.04waagent.vhd"
        }
      }
    }
    ```

5. Speichern Sie die Parameterdatei.


### <a name="deploy-template"></a>Bereitstellen der Vorlage 

Stellen Sie die Vorlage `CreateImage.json` bereit. Mit dieser Vorlage werden die Imageressourcen bereitgestellt, die in einem späteren Schritt zum Erstellen virtueller Computer verwendet werden.

> [!NOTE]
> Wenn beim Bereitstellen der Vorlage ein Authentifizierungsfehler auftritt, sind Ihre Azure-Anmeldeinformationen für diese Sitzung möglicherweise abgelaufen. Führen Sie den Befehl `login-AzureRM` noch einmal aus, um eine neue Verbindung mit dem Azure Resource Manager auf Ihrem Azure Stack Edge Pro-Gerät herzustellen.

1. Führen Sie den folgenden Befehl aus: 
    
    ```powershell
    $templateFile = "Path to CreateImage.json"
    $templateParameterFile = "Path to CreateImage.parameters.json"
    $RGName = "<Name of your resource group>"
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $RGName `
        -TemplateFile $templateFile `
        -TemplateParameterFile $templateParameterFile `
        -Name "<Name for your deployment>"
    ```
    Durch diesen Befehl wird eine Imageressource bereitgestellt. Führen Sie zum Abfragen der Ressource den folgenden Befehl aus:

    ```powershell
    Get-AzureRmImage -ResourceGroupName <Resource Group Name> -name <Image Name>
    ``` 
    Hier sehen Sie eine Beispielausgabe eines erfolgreich erstellten Images.
    
    ```powershell
    PS C:\WINDOWS\system32> login-AzureRMAccount -EnvironmentName aztest -TenantId c0257de7-538f-415c-993a-1b87a031879d
    
    Account               SubscriptionName              TenantId                             Environment
    -------               ----------------              --------                             -----------
    EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d aztest
    
   PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateImage\CreateImage.json"
    PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateImage\CreateImage.parameters.json"
    PS C:\WINDOWS\system32> $RGName = "rg2"
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment4"
        
    DeploymentName          : deployment4
    ResourceGroupName       : rg2
    ProvisioningState       : Succeeded
    Timestamp               : 12/10/2020 7:06:57 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              osType           String                     Linux
                              imageName        String                     myaselinuximg
                              imageUri         String
                              https://sa2.blob.myasegpuvm.wdshcsso.com/con1/ubuntu18.04waagent.vhd
    
    Outputs                 :
    DeploymentDebugLogLevel :    
    PS C:\WINDOWS\system32>
    ```
    
## <a name="create-vm"></a>Erstellen eines virtuellen Computers

### <a name="edit-parameters-file-to-create-vm"></a>Bearbeiten der Parameterdatei für die VM-Erstellung
 
Verwenden Sie die Parameterdatei `CreateVM.parameters.json`, um eine VM zu erstellen. In dieser Datei können die folgenden Parameter angegeben werden.
    
```json
"vmName": {
            "value": "<Name for your VM>"
        },
        "adminUsername": {
            "value": "<Username to log into the VM>"
        },
        "Password": {
            "value": "<Password to log into the VM>"
        },
        "imageName": {
            "value": "<Name for your image>"
        },
        "vmSize": {
            "value": "<A supported size for your VM>"
        },
        "vnetName": {
            "value": "<Name for the virtual network, use ASEVNET>"
        },
        "subnetName": {
            "value": "<Name for the subnet, use ASEVNETsubNet>"
        },
        "vnetRG": {
            "value": "<Resource group for Vnet, use ASERG>"
        },
        "nicName": {
            "value": "<Name for the network interface>"
        },
        "privateIPAddress": {
            "value": "<Private IP address, enter a static IP in the subnet created earlier or leave empty to assign DHCP>"
        },
        "IPConfigName": {
            "value": "<Name for the ipconfig associated with the network interface>"
        }
```    

Weisen Sie in `CreateVM.parameters.json` die erforderlichen Parameter für Ihr Azure Stack Edge Pro-Gerät zu.

1. Geben Sie einen eindeutigen Namen, einen Namen der Netzwerkschnittstelle und einen ipconfig-Namen an. 
1. Geben Sie einen Benutzernamen, ein Kennwort und eine unterstützte VM-Größe ein.
1. Wenn Sie die Netzwerkschnittstelle für Compute aktiviert haben, wurden für diese Netzwerkschnittstelle automatisch ein virtueller Switch und ein virtuelles Netzwerk erstellt. Sie können das vorhandene virtuelle Netzwerk abfragen, um den VNET-Namen, den Subnetznamen und den Namen der VNET-Ressourcengruppe zu erhalten.

    Führen Sie den folgenden Befehl aus:

    ```powershell
    Get-AzureRmVirtualNetwork
    ```
    Hier ist die Beispielausgabe:
    
    ```powershell
    
    PS C:\WINDOWS\system32> Get-AzureRmVirtualNetwork
    
    Name                   : ASEVNET
    ResourceGroupName      : ASERG
    Location               : dbelocal
    Id                     : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/ASERG/providers/Microsoft
                             .Network/virtualNetworks/ASEVNET
    Etag                   : W/"990b306d-18b6-41ea-a456-b275efe21105"
    ResourceGuid           : f8309d81-19e9-42fc-b4ed-d573f00e61ed
    ProvisioningState      : Succeeded
    Tags                   :
    AddressSpace           : {
                               "AddressPrefixes": [
                                 "10.57.48.0/21"
                               ]
                             }
    DhcpOptions            : null
    Subnets                : [
                               {
                                 "Name": "ASEVNETsubNet",
                                 "Etag": "W/\"990b306d-18b6-41ea-a456-b275efe21105\"",
                                 "Id": "/subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/ASERG/provider
                             s/Microsoft.Network/virtualNetworks/ASEVNET/subnets/ASEVNETsubNet",
                                 "AddressPrefix": "10.57.48.0/21",
                                 "IpConfigurations": [],
                                 "ResourceNavigationLinks": [],
                                 "ServiceEndpoints": [],
                                 "ProvisioningState": "Succeeded"
                               }
                             ]
    VirtualNetworkPeerings : []
    EnableDDoSProtection   : false
    EnableVmProtection     : false
    
    PS C:\WINDOWS\system32>
    ```

    Verwenden Sie „ASEVNET“ für den VNET-Namen, „ASEVNETsubNet“ für den Subnetznamen und „ASERG“ für den Namen der VNET-Ressourcengruppe.
    
1. Nun müssen Sie der VM eine statische IP-Adresse zuweisen, die sich im oben definierten Subnetz befindet. Ersetzen Sie **PrivateIPAddress** in der Parameterdatei durch diese Adresse. Wenn die VM eine IP-Adresse vom lokalen DHCP-Server abrufen soll, lassen Sie den `privateIPAddress`-Wert leer.  
    
    ```json
    "privateIPAddress": {
                "value": "5.5.153.200"
            },
    ```
    
4. Speichern Sie die Parameterdatei.

    Nachfolgend ist die JSON-Beispieldatei gezeigt, die in diesem Artikel verwendet wird.
    
    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "vmName": {
              "value": "VM1"
          },
          "adminUsername": {
              "value": "Administrator"
          },
          "Password": {
              "value": "Password1"
          },
        "imageName": {
          "value": "myaselinuximg"
        },
        "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        "vnetName": {
          "value": "ASEVNET"
        },
        "subnetName": {
          "value": "ASEVNETsubNet"
        },
        "vnetRG": {
          "value": "aserg"
        },
        "nicName": {
          "value": "nic5"
        },
        "privateIPAddress": {
          "value": ""
        },
        "IPConfigName": {
          "value": "ipconfig5"
        }
      }
    }
    ```      

### <a name="deploy-template-to-create-vm"></a>Bereitstellen der Vorlage für die VM-Erstellung

Stellen Sie die Vorlage zur VM-Erstellung `CreateVM.json` bereit. Mit dieser Vorlage wird eine Netzwerkschnittstelle aus dem vorhandenen VNET und eine VM anhand des bereitgestellten Images erstellt.

1. Führen Sie den folgenden Befehl aus: 
    
    ```powershell
    Command:
        
        $templateFile = "<Path to CreateVM.json>"
        $templateParameterFile = "<Path to CreateVM.parameters.json>"
        $RGName = "<Resource group name>"
             
        New-AzureRmResourceGroupDeployment `
            -ResourceGroupName $RGName `
            -TemplateFile $templateFile `
            -TemplateParameterFile $templateParameterFile `
            -Name "<DeploymentName>"
    ```   

    Der Vorgang zur VM-Erstellung dauert 15-20 Minuten. Nachfolgend sehen Sie eine Beispielausgabe einer erfolgreich erstellten VM.
    
    ```powershell
    PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateVM\CreateVM.json"
    PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateVM\CreateVM.parameters.json"
    PS C:\WINDOWS\system32> $RGName = "rg2"
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "Deployment6"
       
    DeploymentName          : Deployment6
    ResourceGroupName       : rg2
    ProvisioningState       : Succeeded
    Timestamp               : 12/10/2020 7:51:28 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              vmName           String                     VM1
                              adminUsername    String                     Administrator
                              password         String                     Password1
                              imageName        String                     myaselinuximg
                              vmSize           String                     Standard_NC4as_T4_v3
                              vnetName         String                     ASEVNET
                              vnetRG           String                     aserg
                              subnetName       String                     ASEVNETsubNet
                              nicName          String                     nic5
                              ipConfigName     String                     ipconfig5
                              privateIPAddress  String
    
    Outputs                 :
    DeploymentDebugLogLevel :
    
    PS C:\WINDOWS\system32
    ```   

    Sie können den Befehl `New-AzureRmResourceGroupDeployment` auch asynchron mit dem Parameter `–AsJob` ausführen. Nachstehend finden Sie eine Beispielausgabe, die erzeugt wird, wenn das Cmdlet im Hintergrund ausgeführt wird. Anschließend können Sie den Status des Auftrags abfragen, der mithilfe des Cmdlets `Get-Job` erstellt wurde.

    ```powershell   
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment `
    >>     -ResourceGroupName $RGName `
    >>     -TemplateFile $templateFile `
    >>     -TemplateParameterFile $templateParameterFile `
    >>     -Name "Deployment2" `
    >>     -AsJob
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    2      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmResourceGro...
     
    PS C:\WINDOWS\system32> Get-Job -Id 2
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    ```

7. Überprüfen Sie, ob die VM erfolgreich bereitgestellt wurde. Führen Sie den folgenden Befehl aus:

    `Get-AzureRmVm`


## <a name="connect-to-a-vm"></a>Herstellen einer Verbindung mit einem virtuellen Computer

Je nachdem, ob Sie eine Windows- oder Linux-VM erstellt haben, können die Schritte abweichen.

### <a name="connect-to-windows-vm"></a>Herstellen einer Verbindung mit einer Windows-VM

Führen Sie diese Schritte aus, um eine Verbindung mit einer Windows-VM herzustellen.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

### <a name="connect-to-linux-vm"></a>Herstellen einer Verbindung mit einer Linux-VM

Führen Sie diese Schritte aus, um eine Verbindung mit einer Linux-VM herzustellen.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]



## <a name="next-steps"></a>Nächste Schritte

[Azure Resource Manager-Cmdlets](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)
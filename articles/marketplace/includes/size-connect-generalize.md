---
title: include file
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: 6c7536e38a0d2cf7d4e906947aff645c74e459c0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028438"
---
## <a name="generalize-the-image"></a>Generalisieren des Images

Alle Images im Azure Marketplace müssen allgemein wiederverwendbar sein. Um dies zu erreichen, muss die Betriebssystem-VHD generalisiert werden. Dies ist ein Vorgang, bei dem alle instanzspezifischen Bezeichner und Softwaretreiber von einer VM entfernt werden.

### <a name="for-windows"></a>Für Windows

Windows-Betriebssystemdatenträger werden mit dem [Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)-Tool generalisiert. Wenn Sie das Betriebssystem später aktualisieren oder neu konfigurieren, müssen Sie Sysprep erneut ausführen.

> [!WARNING]
> Fahren Sie die VM nach der Ausführung von Sysprep bis zur Bereitstellung herunter, da u. U. automatische Updates ausgeführt werden. Dadurch verhindern Sie, dass durch nachfolgende Updates instanzspezifische Änderungen am Betriebssystem oder den installierten Diensten vorgenommen werden. Weitere Informationen zum Ausführen von Sysprep finden Sie unter [Schritte zum Generalisieren einer VHD](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep).

### <a name="for-linux"></a>Für Linux

Mit dem folgenden Prozess wird eine Linux-VM generalisiert und als separate VM erneut bereitgestellt. Ausführliche Informationen finden Sie unter [Vorgehensweise zum Erstellen eines Images von einem virtuellen Computer oder einer VHD](../../virtual-machines/linux/capture-image.md). Für Sie sind in diesem Fall nur die Informationen bis zum Abschnitt „Bereitstellen eines virtuellen Computers anhand des erfassten Images“ relevant.

1. Entfernen Sie den Azure Linux-Agent.
    1. Stellen Sie mit einem SSH-Client eine Verbindung mit Ihrer Linux-VM her.
    2. Geben Sie im SSH-Fenster den Befehl `sudo waagent –deprovision+user` ein.
    3. Geben Sie Y ein, um fortzufahren (Sie können dem vorherigen Befehl den Parameter -force hinzufügen, um diesen Bestätigungsschritt zu umgehen).
    4. Geben Sie nach der Ausführung des Befehls **Exit** ein, um den SSH-Client zu schließen.
2. Beenden Sie die VM.
    1. Wählen Sie im Azure-Portal Ihre Ressourcengruppe (RG) aus, und heben Sie die Zuordnung der VM auf.
    2. Ihre VM ist jetzt generalisiert, und Sie können mit diesem VM-Datenträger eine neue VM erstellen.

### <a name="take-a-snapshot-of-the-vm-disk"></a>Erstellen einer Momentaufnahme des VM-Datenträgers

1. Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com/) an.
2. Wählen Sie zunächst oben links **Ressource erstellen** aus, suchen Sie **Momentaufnahme**, und wählen Sie die Option aus.
3. Wählen Sie im Blatt „Momentaufnahme“ **Erstellen** aus.
4. Geben Sie einen **Namen** für die Momentaufnahme ein.
5. Wählen Sie eine vorhandene Ressourcengruppe aus, oder geben Sie den Namen einer neuen Ressourcengruppe ein.
6. Wählen Sie für **Quelldatenträger** den verwalteten Datenträger aus, für den eine Momentaufnahme erstellt werden soll.
7. Wählen Sie den **Kontotyp** aus, der zum Speichern der Momentaufnahme verwendet werden soll. Wir empfehlen **Standard-Festplattenlaufwerke**, es sei denn, Sie benötigen eine leistungsstarke SSD.
8. Wählen Sie **Erstellen** aus.

#### <a name="extract-the-vhd"></a>Extrahieren der VHD

Verwenden Sie das folgende Skript, um die Momentaufnahme in eine VHD in Ihrem Speicherkonto zu exportieren.

```JSON
#Provide the subscription Id where the snapshot is created
subscriptionId=yourSubscriptionId

#Provide the name of your resource group where the snapshot is created
resourceGroupName=myResourceGroupName

#Provide the snapshot name
snapshotName=mySnapshot

#Provide Shared Access Signature (SAS) expiry duration in seconds (such as 3600)
#Know more about SAS here: https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1
sasExpiryDuration=3600

#Provide storage account name where you want to copy the underlying VHD file. 
storageAccountName=mystorageaccountname

#Name of the storage container where the downloaded VHD will be stored.
storageContainerName=mystoragecontainername

#Provide the key of the storage account where you want to copy the VHD 
storageAccountKey=mystorageaccountkey

#Give a name to the destination VHD file to which the VHD will be copied.
destinationVHDFileName=myvhdfilename.vhd

az account set --subscription $subscriptionId

sas=$(az snapshot grant-access --resource-group $resourceGroupName --name $snapshotName --duration-in-seconds $sasExpiryDuration --query [accessSas] -o tsv)

az storage blob copy start --destination-blob $destinationVHDFileName --destination-container $storageContainerName --account-name $storageAccountName --account-key $storageAccountKey --source-uri $sas
```

#### <a name="script-explanation"></a>Erläuterung des Skripts

Dieses Skript verwendet die folgenden Befehle zum Generieren des SAS-URIs für eine Momentaufnahme und zum Kopieren der zugrunde liegenden VHD in ein Speicherkonto mithilfe des SAS-URIs. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
| --- | --- |
| az disk grant-access | Generiert eine schreibgeschützte Shared Access Signature (SAS), die verwendet wird, um die zugrunde liegende VHD-Datei in ein Speicherkonto zu kopieren oder lokal herunterzuladen
| az storage blob copy start | Kopiert ein Blob asynchron aus einem Speicherkonto in ein anderes. Verwenden Sie `az storage blob show`, um den Status des neuen Blobs zu überprüfen. |
|

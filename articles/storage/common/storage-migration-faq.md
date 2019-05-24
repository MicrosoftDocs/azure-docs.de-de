---
title: Häufig gestellte Fragen zur Azure Storage-Migration | Microsoft-Dokumentation
description: Antworten auf allgemeine Fragen zum Migrieren von Azure Storage
services: storage
author: genlin
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.subservice: common
ms.openlocfilehash: cf1cba6f6d26d66fc560c86ea42459fa276cc880
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66114905"
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Häufig gestellte Fragen zur Azure Storage-Migration

In diesem Artikel werden Fragen zur Azure Storage-Migration beantwortet. 

## <a name="faq"></a>Häufig gestellte Fragen

**Wie erstelle ich ein Skript zum Kopieren von Dateien von einem Container in einen anderen?**

Sie können AzCopy zum Kopieren von Dateien zwischen Containern verwenden. Siehe folgendes Beispiel:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

AzCopy nutzt die [Copy Blob-API](https://docs.microsoft.com/rest/api/storageservices/copy-blob), um jede Datei im Container zu kopieren.  
  
Für die Ausführung von AzCopy können Sie einen beliebigen virtuellen oder lokalen Computer mit Internetzugriff verwenden. Sie können auch einen Azure Batch-Zeitplan für die automatische Ausführung verwenden, dieser Vorgang ist jedoch komplizierter.  
  
Das Automatisierungsskript ist auf die Azure Resource Manager-Bereitstellung und nicht auf die Verarbeitung von Speicherinhalten ausgelegt. Weitere Informationen hierzu finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Fallen für das Kopieren von Daten zwischen zwei Dateifreigaben im selben Speicherkonto in der gleichen Region Gebühren an?**

 Nein. Für diesen Vorgang fallen keine Gebühren an.

**Wie kann ich mein gesamtes Speicherkonto in einem anderen Speicherkonto sichern?**

Es gibt keine Option für die direkte Sicherung eines kompletten Speicherkontos. Sie können jedoch den Container in diesem Speicherkonto mit AzCopy oder mit dem Storage-Explorer in ein anderes Konto verschieben. Die folgenden Schritte veranschaulichen, wie Sie den Container mithilfe von AzCopy verschieben:  
 

1.  Installieren Sie das Befehlszeilentool [AzCopy](storage-use-azcopy.md). Mit diesem Tool können Sie die VHD-Datei zwischen Speicherkonten verschieben.

2.  Nachdem Sie AzCopy unter Windows mithilfe des Installationsprogramms installiert haben, öffnen Sie ein Eingabeaufforderungsfenster, und navigieren Sie zum Installationsordner von AzCopy auf Ihrem Computer. AzCopy wird standardmäßig in **%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy** oder **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy** installiert.

3.  Führen Sie den folgenden Befehl aus, um den Container zu verschieben. Sie müssen den Text durch die tatsächlichen Werte ersetzen.   
     
            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

    - `/Source`: Geben Sie den URI für das Quellspeicherkonto (bis zum Container) an.  
    - `/Dest`: Geben Sie den URI für das Zielspeicherkonto (bis zum Container) an.  
    - `/SourceKey`: Geben Sie den Primärschlüssel für das Quellspeicherkonto an. Sie können diesen Schlüssel aus dem Azure-Portal kopieren, indem Sie das Speicherkonto auswählen.  
    - `/DestKey`: Geben Sie den Primärschlüssel für das Zielspeicherkonto an. Sie können diesen Schlüssel aus dem Portal kopieren, indem Sie das Speicherkonto auswählen.

Nach dem Ausführen dieses Befehls werden die Containerdateien in das Zielspeicherkonto verschoben.

> [!NOTE]
> Die AzCopy-Befehlszeilenschnittstelle kann beim Kopieren zwischen Azure-Blobs nicht zusammen mit dem **Pattern**-Switch verwendet werden.
>
> Sie können den AzCopy-Befehl direkt kopieren und bearbeiten und anhand einer Gegenprobe sicherstellen, dass der Wert von **Pattern** mit der Quelle übereinstimmt. Vergewissern Sie sich darüber hinaus, dass **/S**-Platzhalter in Kraft sind. Weitere Informationen finden Sie unter [Übertragen von Daten mit AzCopy unter Windows](storage-use-azcopy.md).

**Wie verschiebe ich Daten aus einem Speichercontainer in einen anderen?**

Folgen Sie diesen Schritten:

1.  Erstellen Sie den Container (Ordner) im Zielblob.

2.  Kopieren Sie die Inhalte aus dem ursprünglichen Blobcontainer mit [AzCopy](https://azure.microsoft.com/blog/azcopy-5-1-release/) in einen anderen Blobcontainer.

**Wie erstelle ich ein PowerShell-Skript zum Verschieben von Daten von einer Azure-Dateifreigabe in eine andere in Azure Storage?**

Verwenden Sie AzCopy, um die Daten von einer Azure-Dateifreigabe in eine andere in Azure Storage zu verschieben. Weitere Informationen finden Sie unter [Übertragen von Daten mit AzCopy unter Windows](storage-use-azcopy.md) und [Übertragen von Daten mit AzCopy unter Linux](storage-use-azcopy-linux.md).

**Wie lade ich große CSV-Dateien in Azure Storage hoch?**

Laden Sie große CSV-Dateien mithilfe von AzCopy in den Azure-Speicher hoch. Weitere Informationen finden Sie unter [Übertragen von Daten mit AzCopy unter Windows](storage-use-azcopy.md) und [Übertragen von Daten mit AzCopy unter Linux](storage-use-azcopy-linux.md).

**Ich muss die Protokolle täglich von Laufwerk „D“ in mein Azure-Speicherkonto verschieben. Wie automatisiere ich diesen Vorgang?**

Sie können AzCopy verwenden und eine Aufgabe im Taskplaner erstellen. Laden Sie Dateien mithilfe eines AzCopy-Batchskripts in ein Azure-Speicherkonto hoch. Weitere Informationen finden Sie unter [Konfigurieren und Ausführen von Startaufgaben für einen Clouddienst](../../cloud-services/cloud-services-startup-tasks.md).

**Wie verschiebe ich meine Speicherkonten zwischen Abonnements?**

Verschieben Sie Ihre Speicherkonten mithilfe von AzCopy zwischen Abonnements. Weitere Informationen finden Sie unter [Übertragen von Daten mit AzCopy unter Windows](storage-use-azcopy.md) und [Übertragen von Daten mit AzCopy unter Linux](storage-use-azcopy-linux.md).

**Wie kann ich etwa 10 TB an Daten in einen Speicher in einer anderen Region verschieben?**

Verwenden Sie AzCopy zum Verschieben der Daten. Weitere Informationen finden Sie unter [Übertragen von Daten mit AzCopy unter Windows](storage-use-azcopy.md) und [Übertragen von Daten mit AzCopy unter Linux](storage-use-azcopy-linux.md).

**Wie kann ich lokal gespeicherte Daten in Azure Storage kopieren?**

Verwenden Sie AzCopy zum Kopieren der Daten. Weitere Informationen finden Sie unter [Übertragen von Daten mit AzCopy unter Windows](storage-use-azcopy.md) und [Übertragen von Daten mit AzCopy unter Linux](storage-use-azcopy-linux.md).

**Wie kann ich lokal gespeicherte Daten nach Azure Files verschieben?**

Verwenden Sie AzCopy zum Verschieben der Daten. Weitere Informationen finden Sie unter [Übertragen von Daten mit AzCopy unter Windows](storage-use-azcopy.md) und [Übertragen von Daten mit AzCopy unter Linux](storage-use-azcopy-linux.md).

**Wie ordne ich einen Containerordner auf einem virtuellen Computer zu?**

Verwenden Sie eine Azure-Dateifreigabe.

**Wie sichere ich Azure-Dateispeicher?**

Es gibt keine Sicherungslösung. Azure Files unterstützt jedoch auch das asynchrone Kopieren. Daher können Sie Dateien wie folgt kopieren:

- Aus einer Freigabe in eine andere Freigabe innerhalb eines Speicherkontos oder in ein anderes Speicherkonto.

- Aus einer Freigabe in einen Blobcontainer innerhalb eines Speicherkontos oder in ein anderes Speicherkonto.

Weitere Informationen finden Sie unter [Übertragen von Daten mit AzCopy unter Windows](storage-use-azcopy.md).

**Wie verschiebe ich verwaltete Datenträger in ein anderes Speicherkonto?**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Folgen Sie diesen Schritten:

1.  Beenden Sie den virtuellen Computer, an den der verwaltete Datenträger angefügt ist.

2.  Kopieren Sie die VHD-Datei des verwalteten Datenträgers durch Ausführen des folgenden Azure PowerShell-Skripts von einem Bereich in einen anderen:

    ```
    Connect-AzAccount

    Select-AzSubscription -SubscriptionId <ID>

    $sas = Grant-AzDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  Erstellen Sie einen verwalteten Datenträger, indem Sie die VHD-Datei in einer anderen Region verwenden, in die Sie die VHD kopiert haben. Führen Sie hierzu das folgende Azure PowerShell-Skript aus:  

    ```
    $resourceGroupName = 'MDDemo'

    $diskName = 'contoso\_os\_disk1'

    $vhdUri = 'https://contoso.storageaccou.com.vhd

    $storageId = '/subscriptions/<ID>/resourceGroups/<RG name>/providers/Microsoft.Storage/storageAccounts/contosostorageaccount1'

    $location = 'westus'

    $storageType = 'StandardLRS'

    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Import -SourceUri $vhdUri -StorageAccountId $storageId -DiskSizeGB 128

    $osDisk = New-AzDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
    ``` 

Weitere Informationen zum Bereitstellen eines virtuellen Computers über einen verwalteten Datenträger finden Sie unter [CreateVmFromManagedOsDisk.ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1).

**Wie kann ich 1-2 TB an Daten aus dem Azure-Portal herunterladen?**

Verwenden Sie AzCopy zum Herunterladen der Daten. Weitere Informationen finden Sie unter [Übertragen von Daten mit AzCopy unter Windows](storage-use-azcopy.md) und [Übertragen von Daten mit AzCopy unter Linux](storage-use-azcopy-linux.md).

**Wie ändere ich für ein Speicherkonto den sekundären Standort in die Region „Europa“?**

Wenn Sie ein Speicherkonto erstellen, wählen Sie die primäre Region für das Konto aus. Die sekundäre Region wird basierend auf der primären Region ausgewählt und kann nicht geändert werden. Weitere Informationen finden Sie unter [Georedundanter Speicher (GRS): Regionsübergreifende Replikation für Azure Storage](storage-redundancy.md).

**Wo finde ich weitere Informationen zu Azure Storage Service Encryption (SSE)?**  
  
Entsprechende Informationen finden Sie in den folgenden Artikeln:

-  [Azure Storage-Sicherheitsleitfaden](storage-security-guide.md)

-  [Azure Storage Service Encryption für ruhende Daten](storage-service-encryption.md)

**Wie kann ich Daten aus einem Speicherkonto verschieben oder herunterladen?**

Verwenden Sie AzCopy zum Herunterladen der Daten. Weitere Informationen finden Sie unter [Übertragen von Daten mit AzCopy unter Windows](storage-use-azcopy.md) und [Übertragen von Daten mit AzCopy unter Linux](storage-use-azcopy-linux.md).


**Wie verschlüssle ich Daten in einem Speicherkonto?**

Nach dem Aktivieren der Verschlüsselung in einem Speicherkonto werden die vorhandenen Daten nicht verschlüsselt. Um die vorhandenen Daten zu verschlüsseln, müssen Sie sie erneut in das Speicherkonto hochladen.

Kopieren Sie die Daten mithilfe von AzCopy in ein anderes Speicherkonto, und verschieben Sie sie anschließend zurück. Sie können auch die [Verschlüsselung ruhender Daten](storage-service-encryption.md) verwenden.

**Gibt es zum Herunterladen einer VHD auf einen lokalen Computer andere Möglichkeiten als die Downloadoption im Portal?**

Sie können eine VHD mit dem [Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) herunterladen.

**Gelten für das Ändern der Replikation eines Speicherkontos von georedundantem Speicher in lokal redundanten Speicher bestimmte Voraussetzungen?**

 Nein. 

**Wie greife ich auf redundanten Azure Files-Speicher zu?**

Für den Zugriff auf redundanten Speicher ist georedundanter Speicher mit Lesezugriff erforderlich. Azure Files unterstützt jedoch nur lokal redundanten Speicher und standardmäßigen georedundanten Speicher, der keinen schreibgeschützten Zugriff erlaubt. 

**Wie wechsel ich von einem Storage Premium-Konto zu einem Standardspeicherkonto?**

Folgen Sie diesen Schritten:

1.  Erstellen Sie ein Standardspeicherkonto. (Oder verwenden Sie ein bestehendes Standardspeicherkonto in Ihrem Abonnement.)

2.  Laden Sie AzCopy herunter. Führen Sie einen der folgenden AzCopy-Befehle aus.
      
    So kopieren Sie ganze Datenträger im Speicherkonto:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S 

    Wenn Sie nur einen Datenträger kopieren möchten, geben Sie für **Pattern** den Namen des Datenträgers an:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

   
Der Vorgang kann mehrere Stunden dauern.

Um sicherzustellen, dass die Übertragung erfolgreich abgeschlossen wurde, überprüfen Sie den Zielspeicherkonto-Container im Azure-Portal. Nachdem die Datenträger in das Standard-Speicherkonto kopiert wurden, können Sie sie als einen vorhandenen Datenträger an den virtuellen Computer anfügen. Weitere Informationen finden Sie unter [Anfügen eines verwalteten Datenträgers an eine Windows-VM im Azure-Portal](../../virtual-machines/windows/attach-managed-disk-portal.md).  
  
**Wie stelle ich für eine Dateifreigabe auf Azure Storage Premium um?**

Storage Premium ist für Azure-Dateifreigaben nicht zulässig.

**Wie führe ich ein Upgrade von einem Standardspeicherkonto auf ein Storage Premium-Konto durch? Wie führe ich ein Downgrade von einem Storage Premium-Konto auf ein Standardspeicherkonto durch?**

Sie müssen das Zielspeicherkonto erstellen, Daten aus dem Quellkonto in das Zielkonto kopieren und dann das Quellkonto löschen. Sie können ein Tool wie AzCopy verwenden, um die Daten zu kopieren.

Wenn Sie über virtuelle Computer verfügen, müssen Sie vor der Migration der Speicherkontodaten weitere Schritte ausführen. Weitere Informationen finden Sie unter [Migrieren zu Azure Storage Premium (Nicht verwaltete Datenträger)](storage-migration-to-premium-storage.md).

**Wie verschiebe ich Ressourcen von einem klassischen Speicherkonto in ein Azure Resource Manager-Speicherkonto?**

Sie können das Cmdlet **Move-AzStorageAccount** verwenden. Dieses Cmdlet verfügt über mehrere Schritte (überprüfen, vorbereiten, committen). Sie können die Verschiebung überprüfen, bevor Sie sie durchführen.

Wenn Sie über virtuelle Computer verfügen, müssen Sie vor der Migration der Speicherkontodaten weitere Schritte ausführen. Weitere Informationen finden Sie unter [Migrieren von IaaS-Ressourcen aus dem klassischen Bereitstellungsmodell zu Azure Resource Manager mithilfe von Azure PowerShell](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md).

**Wie lade ich Daten aus einem Azure-Speicherkonto auf einen Linux-basierten Computer herunter oder lade Daten von einem Linux-Computer hoch?**

Sie können die Azure-Befehlszeilenschnittstelle verwenden.

- Herunterladen eines einzelnen Blobs:

      azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

- Hochladen eines einzelnen Blobs: 

      azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Wie kann ich anderen Personen Zugriff auf meine Speicherressourcen gewähren?**

So gewähren Sie anderen Personen Zugriff auf die Speicherressourcen:

-   Verwenden Sie ein SAS-Token (Shared Access Signature), um Zugriff auf eine Ressource zu ermöglichen. 

-   Geben Sie einem Benutzer den primären oder sekundären Schlüssel für das Speicherkonto. Weitere Informationen finden Sie unter [Informationen zu Azure-Speicherkonten](storage-account-manage.md#access-keys).

-   Ändern Sie die Zugriffsrichtlinie, um anonymen Zugriff zuzulassen. Weitere Informationen finden Sie unter [Verwalten des anonymen Lesezugriffs auf Container und Blobs](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

**Wo wird AzCopy installiert?**

-   Wenn Sie über die Microsoft Azure Storage-Befehlszeile auf AzCopy zugreifen, geben Sie **AzCopy** ein. Die Befehlszeile wird zusammen mit AzCopy installiert.

-   Haben Sie die 32-Bit-Version installiert, befindet sich das Programm hier: **%ProgramFiles(x86)%\\Microsoft SDKs\\Azure\\AzCopy**.

-   Haben Sie die 64-Bit-Version installiert, befindet sich das Programm hier: **%ProgramFiles%\\Microsoft SDKs\\Azure\\AzCopy**.

**Wie greife ich bei einem replizierten Speicherkonto (z. B. zonenredundanter Speicher, georedundanter Speicher oder georedundanter Speicher mit Lesezugriff) auf Daten zu, die in der sekundären Region gespeichert sind?**

-   Wenn Sie zonenredundanten Speicher oder georedundanten Speicher verwenden, können Sie auf Daten in der sekundären Region nur zugreifen, indem Sie ein Failover zu dieser Region einleiten. Weitere Informationen zum Failoverprozess finden Sie unter [Notfallwiederherstellung und Failover von Speicherkonten (Vorschau) in Azure Storage](storage-disaster-recovery-guidance.md).

-   Bei Verwendung von georedundantem Speicher mit Lesezugriff können Sie jederzeit auf Daten in der sekundären Region zugreifen. Verwenden Sie eine der folgenden Methoden an:  
      
    - **AzCopy:** Fügen Sie in der URL an den Speicherkontonamen **-secondary** an, um auf den sekundären Endpunkt zuzugreifen. Beispiel:   
     
      https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd

    - **SAS-Token:** Greifen Sie von einem Endpunkt mithilfe eines SAS-Tokens auf Daten zu. Weitere Informationen finden Sie unter [Verwenden von Shared Access Signatures](storage-dotnet-shared-access-signature-part-1.md).

**Wie verwende ich eine benutzerdefinierte HTTPS-Domäne mit meinem Speicherkonto? Wie kann ich beispielsweise „https:\//mystorageaccountname.blob.core.windows.net/images/image.gif“ als „https:\//www.contoso.com/images/image.gif“ anzeigen?**

SSL wird für Speicherkonten mit benutzerdefinierten Domänen derzeit nicht unterstützt.
Sie können jedoch benutzerdefinierte Domänen ohne HTTPS verwenden. Weitere Informationen finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für Ihren Blob Storage-Endpunkt](../blobs/storage-custom-domain-name.md).

**Wie verwende ich FTP für den Zugriff auf Daten, die sich in einem Speicherkonto befinden?**

Es gibt keine Möglichkeit, mit FTP direkt auf ein Speicherkonto zuzugreifen. Sie können jedoch einen virtuellen Azure-Computer einrichten und anschließend einen FTP-Server auf dem virtuellen Computer installieren. Sie können festlegen, dass der FTP-Server Dateien auf einer Azure Files-Freigabe oder auf einem Datenträger speichert, der für den virtuellen Computer verfügbar ist.

Wenn Sie Daten nur herunterladen möchten, ohne den Storage-Explorer oder eine ähnliche Anwendung verwenden zu müssen, können Sie unter Umständen ein SAS-Token nutzen. Weitere Informationen finden Sie unter [Verwenden von Shared Access Signatures](storage-dotnet-shared-access-signature-part-1.md).

**How do I migrate Blobs from one storage account to another?** (Gewusst wie: Migrieren von Blobs aus einem Speicherkonto in ein anderes)

 Hierzu können Sie unser [Blob-Migrationsskript](../scripts/storage-common-transfer-between-storage-accounts.md) nutzen.

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Wenden Sie sich an den Support.

[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.

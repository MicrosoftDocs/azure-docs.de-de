---
title: Wiederherstellen von Azure Files mit PowerShell
description: In diesem Artikel erfahren Sie, wie Sie Azure Files mit dem Azure Backup-Dienst und PowerShell wiederherstellen.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 60c9848e12de80bcafe4553a9e8f3e27e8876d41
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021383"
---
# <a name="restore-azure-files-with-powershell"></a>Wiederherstellen von Azure Files mit PowerShell

In diesem Artikel wird erläutert, wie Sie eine vollständige Dateifreigabe oder bestimmte Dateien von einem vom [Azure Backup](backup-overview.md)-Dienst erstellten Wiederherstellungspunkt mithilfe von Azure PowerShell wiederherstellten.

Sie können eine gesamte Dateifreigabe oder aber bestimmte Dateien aus dieser Freigabe wiederherstellen. Dabei haben Sie die Möglichkeit, eine Wiederherstellung am ursprünglichen oder an einem alternativen Speicherort durchzuführen.

> [!WARNING]
> Stellen Sie für AFS-Sicherungen sicher, dass die PowerShell-Version auf die Mindestversion für „Az.RecoveryServices 2.6.0“ aktualisiert wird. Weitere Informationen finden Sie im [Abschnitt](backup-azure-afs-automation.md#important-notice-backup-item-identification) zur Anforderung für diese Änderung.

>[!NOTE]
>Azure Backup unterstützt jetzt das Wiederherstellen mehrerer Dateien oder Ordner am ursprünglichen oder an einem alternativen Speicherort mithilfe von PowerShell. Weitere Informationen dazu finden Sie in [diesem Abschnitt](#restore-multiple-files-or-folders-to-original-or-alternate-location) des Dokuments.

## <a name="fetch-recovery-points"></a>Abrufen von Wiederherstellungspunkten

Verwenden Sie [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint), um alle Wiederherstellungspunkte für das gesicherte Element aufzulisten.

Für das unten aufgeführte Skript gilt Folgendes:

* Die Variable **$rp** ist ein Array von Wiederherstellungspunkten für das ausgewählte Sicherungselement der letzten sieben Tage.
* Das Array wird in umgekehrter Reihenfolge sortiert. Der letzte Wiederherstellungspunkt liegt bei Index **0**.
* Verwenden Sie die standardmäßige PowerShell-Arrayindizierung zum Auswählen des Wiederherstellungspunkts.
* Im Beispiel wählt **$rp[0]** den letzten Wiederherstellungspunkt aus.

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"
$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID
$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0] | fl
```

Die Ausgabe sieht in etwa wie folgt aus:

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```

Nachdem der entsprechende Wiederherstellungspunkt ausgewählt wurde, stellen Sie die Dateifreigabe oder Datei am ursprünglichen oder an einem alternativen Speicherort wieder her.

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Wiederherstellen einer Azure-Dateifreigabe an einem alternativen Speicherort

Verwenden Sie [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem), um den ausgewählten Wiederherstellungspunkt wiederherzustellen. Geben Sie die folgenden Parameter an, um den alternativen Speicherort festzulegen:

* **TargetStorageAccountName**: Das Speicherkonto, in dem der gesicherte Inhalt wiederhergestellt wird. Das Zielspeicherkonto muss sich am gleichen Speicherort wie der Tresor befinden.
* **TargetFileShareName**: Die Dateifreigaben in dem Zielspeicherkonto, in dem der gesicherte Inhalt wiederhergestellt wird.
* **TargetFolder**: Der Ordner unter der Dateifreigabe, in dem die Daten wiederhergestellt werden. Wenn der gesicherte Inhalt in einem Stammordner wiederhergestellt werden soll, geben Sie die Werte für den Zielordner als eine leere Zeichenfolge ein.
* **ResolveConflict**: Die Anweisung bei einem Konflikt mit den wiederhergestellten Daten. Für diesen Parameter kann **Overwrite** oder **Skip** angegeben werden.

Führen Sie das Cmdlet mit den Parametern wie folgt aus:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

Der Befehl gibt einen Auftrag mit einer ID zur Nachverfolgung zurück, wie im folgenden Beispiel gezeigt wird.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>Wiederherstellen einer Azure-Datei an einem alternativen Speicherort

Verwenden Sie [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem), um den ausgewählten Wiederherstellungspunkt wiederherzustellen. Geben Sie die folgenden Parameter an, um den alternativen Speicherort und die wiederherzustellende Datei eindeutig festzulegen:

* **TargetStorageAccountName**: Das Speicherkonto, in dem der gesicherte Inhalt wiederhergestellt wird. Das Zielspeicherkonto muss sich am gleichen Speicherort wie der Tresor befinden.
* **TargetFileShareName**: Die Dateifreigaben in dem Zielspeicherkonto, in dem der gesicherte Inhalt wiederhergestellt wird.
* **TargetFolder**: Der Ordner unter der Dateifreigabe, in dem die Daten wiederhergestellt werden. Wenn der gesicherte Inhalt in einem Stammordner wiederhergestellt werden soll, geben Sie die Werte für den Zielordner als eine leere Zeichenfolge ein.
* **SourceFilePath**: Der absolute Pfad der (in der Dateifreigabe wiederherzustellenden) Datei als Zeichenfolge. Dieser Pfad ist derselbe Pfad, der im PowerShell-Cmdlet **Get-AzStorageFile** verwendet wird.
* **SourceFileType**: Gibt an, ob ein Verzeichnis oder eine Datei ausgewählt wurde. Für diesen Parameter kann **Directory** oder **File** angegeben werden.
* **ResolveConflict**: Die Anweisung bei einem Konflikt mit den wiederhergestellten Daten. Für diesen Parameter kann **Overwrite** oder **Skip** angegeben werden.

Die zusätzlichen Parameter („SourceFilePath“ und „SourceFileType“) beziehen sich nur auf die Datei, die Sie wiederherstellen möchten.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Der Befehl gibt einen Auftrag mit einer ID zur Nachverfolgung zurück, wie im vorherigen Abschnitt gezeigt wurde.

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Wiederherstellen von Azure-Dateifreigaben und -Dateien am ursprünglichen Speicherort

Wenn Sie an einem ursprünglichen Speicherort eine Wiederherstellung vornehmen, müssen Sie nicht alle Zielparameter und zielbezogenen Parameter angeben. Nur **ResolveConflict** muss angegeben werden.

### <a name="overwrite-an-azure-file-share"></a>Überschreiben einer Azure-Dateifreigabe

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

### <a name="overwrite-an-azure-file"></a>Überschreiben einer Azure-Datei

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>Wiederherstellen mehrerer Dateien oder Ordner am ursprünglichen oder an einem alternativen Speicherort

Verwenden Sie den Befehl [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem), indem Sie den Pfad aller Dateien oder Ordner, die Sie wiederherstellen möchten, als Wert für den Parameter **MultipleSourceFilePath** übergeben.

### <a name="restore-multiple-files"></a>Wiederherstellen mehrerer Dateien

Im folgenden Skript versuchen wir, die Dateien *FileSharePage.png* und *MyTestFile.txt* wiederherzustellen.

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("FileSharePage.png", "MyTestFile.txt")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType File -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

### <a name="restore-multiple-directories"></a>Wiederherstellen mehrerer Verzeichnisse

Im folgenden Skript versuchen wir, die Verzeichnisse *zrs1_restore* und *Restore* wiederherzustellen.

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("Restore","zrs1_restore")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType Directory -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

Die Ausgabe sieht etwa wie folgt aus:

```output
WorkloadName         Operation         Status          StartTime                EndTime       JobID
------------         ---------         ------          ---------                -------       -----
azurefiles           Restore           InProgress      4/5/2020 8:01:24 AM                    cd36abc3-0242-44b1-9964-0a9102b74d57
```

Wenn Sie mehrere Dateien oder Ordner an einem alternativen Speicherort wiederherstellen möchten, verwenden Sie die oben aufgeführten Skripts, indem Sie die Parameterwerte für den Zielspeicherort angeben, wie oben unter [Wiederherstellen einer Azure-Datei an einem alternativen Speicherort](#restore-an-azure-file-to-an-alternate-location) erläutert.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über die [Wiederherstellung von Azure Files](restore-afs.md) im Azure-Portal.

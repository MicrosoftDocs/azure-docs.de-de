---
title: Sichern und Wiederherstellen von Azure Files-Dateifreigaben mithilfe von Azure Backup und PowerShell
description: Erfahren Sie, wie Sie Azure Files-Dateifreigaben mithilfe von Azure Backup und PowerShell sichern und wiederherstellen.
author: pvrk
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: pullabhk
ms.openlocfilehash: 986414d0bac24d0c7e37b34df473346742fa97fd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65204177"
---
# <a name="back-up-and-restore-azure-files-with-powershell"></a>Sichern und Wiederherstellen von Azure Files-Dateifreigaben mithilfe von PowerShell

In diesem Artikel wird beschrieben, wie Sie mit Azure PowerShell und einem Recovery Services-Tresor von [Azure Backup](backup-overview.md) eine Azure Files-Dateifreigabe sichern und wiederherstellen. 

In diesem Tutorial werden folgende Punkte erläutert:

> [!div class="checklist"]
> * Einrichten von PowerShell und Registrieren des Azure Recovery Services-Anbieters
> * Erstellen Sie einen Recovery Services-Tresor.
> * Konfigurieren der Sicherung für eine Azure-Dateifreigabe
> * Ausführen eines Sicherungsauftrags
> * Wiederherstellen einer gesicherten Azure-Dateifreigabe oder einer einzelnen Freigabedatei
> * Überwachen von Sicherungs- und Wiederherstellungsaufträgen


## <a name="before-you-start"></a>Vorbereitung

- Verschaffen Sie sich einen [Überblick](backup-azure-recovery-services-vault-overview.md) über Recovery Services-Tresore.
- Informieren Sie sich über die Previewfeatures zum [Sichern von Azure-Dateifreigaben](backup-azure-files.md).
- Sehen Sie sich die PowerShell-Objekthierarchie für Recovery Services an.


## <a name="recovery-services-object-hierarchy"></a>Recovery Services-Objekthierarchie

Die Objekthierarchie ist im folgenden Diagramm zusammengefasst.

![Recovery Services-Objekthierarchie](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Sehen Sie sich die [Cmdlet-Referenz](/powershell/module/az.recoveryservices) zu **Az.RecoveryServices** in der Azure-Bibliothek an.


## <a name="set-up-and-install"></a>Einrichten und Installieren

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Richten Sie PowerShell wie folgt ein:

1. [Laden Sie die neueste Version von Azure PowerShell](/powershell/azure/install-az-ps) herunter. Version 1.0.0 ist die erforderliche Mindestversion.

2. Ermitteln Sie die Azure Backup-PowerShell-Cmdlets mit dem folgenden Befehl:

    ```powershell
    Get-Command *azrecoveryservices*
    ```
3. Sehen Sie sich die Aliase und Cmdlets für Azure Backup, Azure Site Recovery und den Recovery Services-Tresor an. Unten sehen Sie eine Beispielanzeige. Dies ist keine vollständige Liste der Cmdlets.

    ![Liste der Recovery Services-Cmdlets](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. Melden Sie sich mit **Connect-AzAccount** bei Ihrem Azure-Konto an.
4. Auf der daraufhin angezeigten Webseite werden Sie aufgefordert, Ihre Kontoanmeldeinformationen einzugeben.

    - Alternativ können Sie Ihre Kontoanmeldeinformationen als Parameter im Cmdlet **Connect-AzAccount** mit **-Credential** angeben.
    - Wenn Sie als CSP-Partner für einen Mandanten tätig sind, geben Sie den Kunden mit dessen Mandanten-ID oder primären Mandantendomänennamen als Mandanten an. Beispiel: **Connect-AzAccount -Tenant** fabrikam.com.

4. Da ein Konto mehrere Abonnements enthalten kann, müssen Sie das Abonnement, das Sie verwenden möchten, dem Konto zuordnen.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Falls Sie Azure Backup zum ersten Mal verwenden, registrieren Sie den Azure Recovery Services-Anbieter für Ihr Abonnement mithilfe des Cmdlets **Register-AzResourceProvider**.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Überprüfen Sie, ob die Anbieter erfolgreich registriert wurden:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
7. Vergewissern Sie sich in der Befehlsausgabe, dass sich der Wert für **RegistrationState** in **Registered** ändert. Ist dies nicht der Fall, führen Sie das Cmdlet **Register-AzResourceProvider** erneut aus.



## <a name="create-a-recovery-services-vault"></a>Erstellen eines Recovery Services-Tresors

Führen Sie die nachstehenden Schritte aus, um einen Recovery Services-Tresors zu erstellen.

- Der Recovery Services-Tresor ist eine Resource Manager-Ressource. Deshalb müssen Sie ihn in eine Ressourcengruppe einfügen. Sie können eine vorhandene Ressourcengruppe verwenden oder eine Ressourcengruppe mit dem Cmdlet **New-AzResourceGroup** erstellen. Wenn Sie eine Ressourcengruppe erstellen, geben Sie den Namen und den Speicherort dafür an. 

1. Ein Tresor wird in einer Ressourcengruppe platziert. Erstellen Sie mit [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0) eine neue Ressourcengruppe, wenn noch keine vorhanden ist. In diesem Beispiel wird eine neue Ressourcengruppe in der Region „USA, Westen“ erstellt.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```
2. Verwenden Sie das Cmdlet [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) zum Erstellen des Tresors. Geben Sie denselben Speicherort für den Tresor an, der für die Ressourcengruppe verwendet wurde.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Geben Sie den für den Tresorspeicher zu verwendenden Redundanztyp an.

   - Sie können [lokal redundanten Speicher](../storage/common/storage-redundancy-lrs.md) oder [georedundanten Speicher](../storage/common/storage-redundancy-grs.md) verwenden.
   - Im folgenden Beispiel wird die Option **-BackupStorageRedundancy** für den Befehl [Set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) für **testvault** auf den Wert **GeoRedundant** festgelegt.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>Anzeigen von Tresoren in einem Abonnement

Wenn Sie alle Tresore des Abonnements anzeigen möchten, verwenden Sie [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

Die Ausgabe sieht in etwa wie folgt aus: Beachten Sie, dass die zugeordnete Ressourcengruppe und der Speicherort angegeben werden.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Festlegen des Tresorkontexts

Speichern Sie das Tresorobjekt in einer Variablen, und legen Sie den Tresorkontext fest.

- Viele Azure Backup-Cmdlets erfordern das Recovery Services-Tresorobjekt als Eingabe. Daher ist es sinnvoll, das Tresorobjekt in einer Variablen zu speichern.
- Der Tresorkontext ist der Datentyp, der im Tresor geschützt wird. Legen Sie ihn mit [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0) fest. Der festgelegte Kontext gilt für alle nachfolgenden Cmdlets.


Im folgenden Beispiel wird der Tresorkontext für **testvault** festgelegt.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Abrufen der Tresor-ID

Es ist geplant, die Tresorkontexteinstellung gemäß den Azure PowerShell-Richtlinien als veraltet zu kennzeichnen. Stattdessen können Sie die Tresor-ID speichern oder abrufen und wie folgt an relevante Befehle übergeben:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Konfigurieren einer Sicherungsrichtlinie

Eine Sicherungsrichtlinie gibt den Zeitplan für Sicherungen an und legt fest, wie lange Wiederherstellungspunkte der Sicherung beibehalten werden sollen:

- Eine Sicherungsrichtlinie ist mindestens einer Aufbewahrungsrichtlinie zugeordnet. Eine Aufbewahrungsrichtlinie definiert, wie lange ein Wiederherstellungspunkt gespeichert werden soll, bevor er gelöscht wird.
- Zeigen Sie den Aufbewahrungszeitraum der Standardsicherungsrichtlinie mit [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0) an.
- Zeigen Sie den Zeitplan der Standardsicherungsrichtlinie mit [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0) an.
-  Mit dem Cmdlet [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) erstellen Sie eine neue Sicherungsrichtlinie. Dazu geben Sie die Zeitplan- und Aufbewahrungsrichtlinienobjekte ein.

Im folgenden Beispiel werden die Zeitplanrichtlinie und die Aufbewahrungsrichtlinie in Variablen gespeichert. Anschließend werden diese Variablen als Parameter für eine neue Richtlinie (**NewAFSPolicy**) verwendet. Die Richtlinie **NewAFSPolicy**führt eine tägliche Sicherung aus und bewahrt sie 30 Tage auf.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Die Ausgabe sieht in etwa wie folgt aus:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
```



## <a name="enable-backup"></a>Aktivieren der Sicherung

Nachdem Sie die Sicherungsrichtlinie definiert haben, können Sie den Schutz für die Azure-Dateifreigabe mit der Richtlinie aktivieren.

### <a name="retrieve-a-backup-policy"></a>Abrufen einer Sicherungsrichtlinie

Rufen Sie zuerst das entsprechende Richtlinienobjekt mit [Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) ab. Verwenden Sie dieses Cmdlet zum Abrufen einer bestimmten Richtlinie oder zum Anzeigen der Richtlinien, die einem bestimmten Workloadtyp zugeordnet sind.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Abrufen einer Richtlinie für einen Workloadtyp

Im folgenden Beispiel werden Richtlinien für den Workloadtyp **AzureFiles** abgerufen.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

Die Ausgabe sieht in etwa wie folgt aus:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```
> [!NOTE]
> Die Zeitzone für das Feld **BackupTime** in PowerShell ist die koordinierte Weltzeit (UTC). Wenn der Zeitpunkt der Sicherung im Azure-Portal angezeigt wird, wird die Uhrzeit auf Ihre lokale Zeitzone eingestellt.

### <a name="retrieve-a-specific-policy"></a>Abrufen einer bestimmten Richtlinie

Die folgende Richtlinie ruft die Sicherungsrichtlinie mit dem Namen **dailyafs** ab.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Aktivieren der Sicherung und Anwenden der Richtlinie

Aktivieren Sie den Schutz mit [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Nachdem die Richtlinie dem Tresor zugeordnet wurde, werden Sicherungen entsprechend dem Richtlinienzeitplan ausgelöst.

Im folgenden Beispiel wird der Schutz für die Azure-Dateifreigabe **testAzureFileShare** im Speicherkonto **testStorageAcct** mit der Richtlinie **dailyafs** aktiviert.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

Der Befehl wartet, bis der Auftrag zum Konfigurieren des Schutzes beendet ist, und gibt eine ähnliche Ausgabe wie gezeigt zurück.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="trigger-an-on-demand-backup"></a>Auslösen einer bedarfsgesteuerten Sicherung

Verwenden Sie [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0), um eine bedarfsgesteuerte Sicherung für eine geschützte Azure-Dateifreigabe auszuführen.

1. Rufen Sie mit [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer) das Speicherkonto und die Dateifreigabe aus dem Container im Tresor ab, in dem sich die Sicherungsdaten befinden.
2. Zum Starten eines Sicherungsauftrags rufen Sie mit [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem) Informationen zum virtuellen Computer ab.
3. Führen Sie mit [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem) eine bedarfsgesteuerte Sicherung aus.

Führen Sie die bedarfsgesteuerte Sicherung wie folgt aus:
    
```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

Der Befehl gibt einen Auftrag mit einer ID zur Nachverfolgung zurück, wie im folgenden Beispiel gezeigt wird.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Momentaufnahmen von Azure-Dateifreigaben werden beim Erstellen von Sicherungen verwendet. Daher ist der Auftrag in der Regel abgeschlossen, bis der Befehl diese Ausgabe zurückgibt.

### <a name="modify-the-protection-policy"></a>Ändern der Schutzrichtlinie

Verwenden Sie [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0), um die Sicherungsrichtlinie für die Azure-Dateifreigabe anzupassen. Geben Sie das relevante Sicherungselement und die neue Sicherungsrichtlinie an.

Im folgenden Beispiel wird die Schutzrichtlinie von **testAzureFS** von **dailyafs** in **monthlyafs** geändert.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-shares-and-files"></a>Wiederherstellen von Azure-Dateifreigaben und -Dateien

Sie können eine gesamte Dateifreigabe oder nur bestimmte Daten auf dieser wiederherstellen. Dabei haben Sie die Möglichkeit, eine Wiederherstellung am ursprünglichen oder an einem alternativen Speicherort durchzuführen. 

### <a name="fetch-recovery-points"></a>Abrufen von Wiederherstellungspunkten

Verwenden Sie [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0), um alle Wiederherstellungspunkte für das gesicherte Element aufzulisten.

Für das unten aufgeführte Skript gilt Folgendes:

- Die Variable **$rp** ist ein Array von Wiederherstellungspunkten für das ausgewählte Sicherungselement der letzten sieben Tage.
- Das Array wird in umgekehrter Reihenfolge sortiert. Der letzte Wiederherstellungspunkt liegt bei Index **0**.
- Verwenden Sie die standardmäßige PowerShell-Arrayindizierung zum Auswählen des Wiederherstellungspunkts.
- Im Beispiel wählt **$rp[0]** den letzten Wiederherstellungspunkt aus.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

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

### <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Wiederherstellen einer Azure-Dateifreigabe an einem alternativen Speicherort

Verwenden Sie [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0), um den ausgewählten Wiederherstellungspunkt wiederherzustellen. Geben Sie die folgenden Parameter an, um den alternativen Speicherort festzulegen: 

- **TargetStorageAccountName**: Das Speicherkonto, in dem der gesicherte Inhalt wiederhergestellt wird. Das Zielspeicherkonto muss sich am gleichen Speicherort wie der Tresor befinden.
- **TargetFileShareName**: Die Dateifreigaben in dem Zielspeicherkonto, in dem der gesicherte Inhalt wiederhergestellt wird.
- **TargetFolder**: Der Ordner unter der Dateifreigabe, in dem die Daten wiederhergestellt werden. Wenn der gesicherte Inhalt in einem Stammordner wiederhergestellt werden soll, geben Sie die Werte für den Zielordner als eine leere Zeichenfolge ein.
- **ResolveConflict**: Die Anweisung bei einem Konflikt mit den wiederhergestellten Daten. Für diesen Parameter kann **Overwrite** oder **Skip** angegeben werden.

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

### <a name="restore-an-azure-file-to-an-alternate-location"></a>Wiederherstellen einer Azure-Datei an einem alternativen Speicherort

Verwenden Sie [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0), um den ausgewählten Wiederherstellungspunkt wiederherzustellen. Geben Sie die folgenden Parameter an, um den alternativen Speicherort und die wiederherzustellende Datei eindeutig festzulegen:

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

### <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Wiederherstellen von Azure-Dateifreigaben und -Dateien am ursprünglichen Speicherort

Wenn Sie an einem ursprünglichen Speicherort eine Wiederherstellung vornehmen, müssen Sie nicht alle Zielparameter und zielbezogenen Parameter angeben. Nur **ResolveConflict** muss angegeben werden.

#### <a name="overwrite-an-azure-file-share"></a>Überschreiben einer Azure-Dateifreigabe

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Überschreiben einer Azure-Datei

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>Nachverfolgen von Sicherungs- und Wiederherstellungsaufträgen

Bei bedarfsgesteuerten Sicherungs- und Wiederherstellungsvorgängen wird ein Auftrag zusammen mit einer ID wie [oben](#trigger-an-on-demand-backup) gezeigt zurückgegeben. Verwenden Sie das Cmdlet [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0), um den Fortschritt des Auftrags nachzuverfolgen und Details abzurufen.

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```
## <a name="next-steps"></a>Nächste Schritte
Informieren Sie sich im Azure-Portal über die [Sicherung von Azure Files-Dateifreigaben](backup-azure-files.md).

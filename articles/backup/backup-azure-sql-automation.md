---
title: Sichern und Wiederherstellen von SQL-Datenbanken auf virtuellen Azure-Computern mit PowerShell
description: Sichern und Wiederherstellen von SQL-Datenbanken auf virtuellen Azure-Computern mit Azure Backup und PowerShell.
ms.topic: conceptual
ms.date: 03/15/2019
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 0b3b943a53c1da0f6f1e938b5b234dc82541b46d
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92901664"
---
# <a name="back-up-and-restore-sql-databases-in-azure-vms-with-powershell"></a>Sichern und Wiederherstellen von SQL-Datenbanken auf virtuellen Azure-Computern mit PowerShell

In diesem Artikel wird beschrieben, wie Sie mit Azure PowerShell eine SQL-Datenbank auf einem virtuellen Azure-Computer mithilfe des [Azure Backup](backup-overview.md) Recovery Services-Tresors sichern und wiederherstellen.

In diesem Artikel wird Folgendes erläutert:

> [!div class="checklist"]
>
> * Einrichten von PowerShell und Registrieren des Azure Recovery Services-Anbieters
> * Erstellen Sie einen Recovery Services-Tresor.
> * Konfigurieren der Sicherung für eine SQL-Datenbank auf einem virtuellen Azure-Computer
> * Ausführen eines Sicherungsauftrags
> * Wiederherstellen einer gesicherten SQL-Datenbank
> * Überwachen von Sicherungs- und Wiederherstellungsaufträgen

## <a name="before-you-start"></a>Vorbereitung

* [Lesen Sie weitere Informationen](backup-azure-recovery-services-vault-overview.md) zu Recovery Services-Tresoren.
* Erfahren Sie mehr über die Funktionen zum [Sichern von SQL-Datenbanken auf virtuellen Azure-Computern](backup-azure-sql-database.md#before-you-start).
* Sehen Sie sich die PowerShell-Objekthierarchie für Recovery Services an.

### <a name="recovery-services-object-hierarchy"></a>Recovery Services-Objekthierarchie

Die Objekthierarchie ist im folgenden Diagramm zusammengefasst.

![Recovery Services-Objekthierarchie](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

[Sehen Sie sich die](/powershell/module/az.recoveryservices)Cmdlet-Referenz zu **Az.RecoveryServices** in der Azure-Bibliothek an.

### <a name="set-up-and-install"></a>Einrichten und Installieren

Richten Sie PowerShell wie folgt ein:

1. [Laden Sie die neueste Version von Azure PowerShell](/powershell/azure/install-az-ps) herunter. Version 1.5.0 ist die erforderliche Mindestversion.

2. Ermitteln Sie die PowerShell-Cmdlets für Azure Backup mit dem folgenden Befehl:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Sehen Sie sich die Aliase und Cmdlets für Azure Backup und den Recovery Services-Tresor an. Unten sehen Sie eine Beispielanzeige. Dies ist keine vollständige Liste der Cmdlets.

    ![Liste der Recovery Services-Cmdlets](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Melden Sie sich mit **Connect-AzAccount** bei Ihrem Azure-Konto an.
5. Auf der daraufhin angezeigten Webseite werden Sie aufgefordert, Ihre Kontoanmeldeinformationen einzugeben.

    * Alternativ können Sie Ihre Kontoanmeldeinformationen als Parameter im Cmdlet **Connect-AzAccount** mit **-Credential** angeben.
    * Wenn Sie als CSP-Partner für einen Mandanten tätig sind, geben Sie den Kunden mit dessen Mandanten-ID oder primärem Mandantendomänennamen als Mandanten an. Beispiel: **Connect-AzAccount -Tenant** fabrikam.com.

6. Da ein Konto mehrere Abonnements enthalten kann, müssen Sie das Abonnement, das Sie verwenden möchten, dem Konto zuordnen.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Falls Sie Azure Backup zum ersten Mal verwenden, registrieren Sie den Azure Recovery Services-Anbieter für Ihr Abonnement mithilfe des Cmdlets **Register-AzResourceProvider** .

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Überprüfen Sie, ob die Anbieter erfolgreich registriert wurden:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Vergewissern Sie sich in der Befehlsausgabe, dass sich der Wert für **RegistrationState** in **Registered** ändert. Ist dies nicht der Fall, führen Sie das Cmdlet **Register-AzResourceProvider** erneut aus.

## <a name="create-a-recovery-services-vault"></a>Erstellen eines Recovery Services-Tresors

Führen Sie die nachstehenden Schritte aus, um einen Recovery Services-Tresors zu erstellen.

Der Recovery Services-Tresor ist eine Resource Manager-Ressource. Deshalb müssen Sie ihn in eine Ressourcengruppe einfügen. Sie können eine vorhandene Ressourcengruppe verwenden oder eine Ressourcengruppe mit dem Cmdlet **New-AzResourceGroup** erstellen. Wenn Sie eine Ressourcengruppe erstellen, geben Sie den Namen und den Speicherort dafür an.

1. Ein Tresor wird in einer Ressourcengruppe platziert. Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine neue Ressourcengruppe, wenn noch keine vorhanden ist. In diesem Beispiel wird eine neue Ressourcengruppe in der Region „USA, Westen“ erstellt.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Verwenden Sie das Cmdlet [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) zum Erstellen des Tresors. Geben Sie denselben Speicherort für den Tresor an, der für die Ressourcengruppe verwendet wurde.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Geben Sie den für den Tresorspeicher zu verwendenden Redundanztyp an.

    * Sie können [lokal redundanten Speicher](../storage/common/storage-redundancy.md#locally-redundant-storage), [georedundanten Speicher](../storage/common/storage-redundancy.md#geo-redundant-storage) oder [zonenredundanten Speicher](../storage/common/storage-redundancy.md#zone-redundant-storage) verwenden.
    * Im folgenden Beispiel wird die Option **-BackupStorageRedundancy** für den Befehl [Set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) für **testvault** auf den Wert **GeoRedundant** festgelegt.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Anzeigen von Tresoren in einem Abonnement

Wenn Sie alle Tresore des Abonnements anzeigen möchten, verwenden Sie [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault).

```powershell
Get-AzRecoveryServicesVault
```

Die Ausgabe sieht in etwa wie folgt aus: Die zugeordnete Ressourcengruppe und der Speicherort werden angegeben.

```output
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

* Viele Azure Backup-Cmdlets erfordern das Recovery Services-Tresorobjekt als Eingabe. Daher ist es sinnvoll, das Tresorobjekt in einer Variablen zu speichern.
* Der Tresorkontext ist der Datentyp, der im Tresor geschützt wird. Legen Sie ihn mit [Set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext) fest. Der festgelegte Kontext gilt für alle nachfolgenden Cmdlets.

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

* Eine Sicherungsrichtlinie ist mindestens einer Aufbewahrungsrichtlinie zugeordnet. Eine Aufbewahrungsrichtlinie definiert, wie lange ein Wiederherstellungspunkt gespeichert werden soll, bevor er gelöscht wird.
* Zeigen Sie den Aufbewahrungszeitraum der Standardsicherungsrichtlinie mit [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject) an.
* Zeigen Sie den Zeitplan der Standardsicherungsrichtlinie mit [Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) an.
* Mit dem Cmdlet [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) erstellen Sie eine neue Sicherungsrichtlinie. Dazu geben Sie die Zeitplan- und Aufbewahrungsrichtlinienobjekte ein.

Eine Startzeit wird standardmäßig im Schedule Policy Object (Zeitplanrichtlinienobjekt) definiert. Verwenden Sie das folgende Beispiel, um die Startzeit in die gewünschte Startzeit zu ändern. Die gewünschte Startzeit sollte ebenfalls in UTC angegeben werden. Beim folgenden Beispiel wird vorausgesetzt, dass die gewünschte Startzeit für tägliche Sicherungen 01:00 Uhr UTC ist.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Sie können die Startzeit nur in 30-Minuten-Einheiten angeben. Im obigen Beispiel kann sie nur „01:00:00“ oder „02:30:00“ lauten. 01:15:00 kann nicht als Startzeit angegeben werden.

Im folgenden Beispiel werden die Zeitplanrichtlinie und die Aufbewahrungsrichtlinie in Variablen gespeichert. Anschließend werden diese Variablen als Parameter für eine neue Richtlinie ( **NewSQLPolicy** ) verwendet. Mit **NewSQLPolicy** wird täglich eine „vollständige“ Sicherung ausgeführt, diese 180 Tage lang aufbewahrt und alle 2 Stunden eine Protokollsicherung durchgeführt.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Die Ausgabe sieht in etwa wie folgt aus:

```output
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 01:30:00 AM      Daily                                    False                True
```

## <a name="enable-backup"></a>Aktivieren der Sicherung

### <a name="registering-the-sql-vm"></a>Registrieren des virtuellen SQL-Computers

Im Falle von Sicherungen virtueller Azure-Computer und Azure-Dateifreigaben kann der Backup-Dienst eine Verbindung mit diesen Azure Resource Manager-Ressourcen herstellen und die relevanten Details abrufen. Da es sich bei SQL um eine Anwendung auf einem virtuellen Azure-Computer handelt, muss der Backup-Dienst über die Berechtigung für den Zugriff auf die Anwendung und das Abrufen der erforderlichen Informationen verfügen. Dazu müssen Sie den virtuellen Azure-Computer, der die SQL-Anwendung enthält, bei einem Recovery Services-Tresor *„registrieren“* . Nachdem Sie einen virtuellen SQL-Computer bei einem Tresor registriert haben, können Sie die SQL-Datenbanken nur in diesem Tresor schützen. Verwenden Sie zum Registrieren des virtuellen Computers das PowerShell-Cmdlet [Register-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/register-azrecoveryservicesbackupcontainer).

```powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

Der Befehl gibt einen „Sicherungscontainer“ dieser Ressource zurück, und der Status lautet „registriert“.

> [!NOTE]
> Wenn der Parameter „Force“ nicht angegeben wird, werden Sie mit einem Text vom Typ „Möchten Sie den Schutz für diesen Container deaktivieren?“ zur Bestätigung aufgefordert. Ignorieren Sie diesen Text, und bestätigen Sie mit „Ja“. Dies ist ein bekanntes Problem, und wir arbeiten gerade daran, den Text und die Anforderung für den Parameter „Force“ zu entfernen.

### <a name="fetching-sql-dbs"></a>Abrufen von SQL-Datenbanken

Nach der Registrierung kann der Backup-Dienst alle verfügbaren SQL-Komponenten auf dem virtuellen Computer auflisten. Verwenden Sie zum Anzeigen aller SQL-Komponenten, die in diesem Tresor noch zu sichern sind, das PowerShell-Cmdlet [Get-AzRecoveryServicesBackupProtectableItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectableitem).

```powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
```

Die Ausgabe zeigt alle nicht geschützten SQL-Komponenten auf allen virtuellen SQL-Computern, die bei diesem Tresor registriert sind, mit dem Elementtyp und dem Servernamen an. Sie können weiter nach einem bestimmten virtuellen SQL-Computer filtern, indem Sie den Parameter „-Container“ übergeben oder die Kombination aus „Name“ und „ServerName“ zusammen mit dem Flag „ItemType“ verwenden, um zu einem eindeutigen SQL-Element zu gelangen.

```powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
```

### <a name="configuring-backup"></a>Konfigurieren der Sicherung

Da nun die erforderliche SQL-Datenbank und die benötigte Richtlinie für deren Sicherung vorhanden sind, kann mit dem Cmdlet [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) die Sicherung für diese SQL-Datenbank konfiguriert werden.

```output
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
```

Der Befehl wartet, bis die Sicherungskonfiguration abgeschlossen ist, und gibt die folgende Ausgabe zurück:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Abrufen neuer SQL-Datenbanken

Sobald der Computer registriert ist, ruft der Backup-Dienst die Details der dann verfügbaren Datenbanken ab. Wenn SQL-Datenbanken oder SQL-Instanzen dem registrierten Computer später hinzugefügt werden, müssen Sie den Sicherungsdienst manuell auslösen, damit eine neue „Abfrage“ zum erneuten Abrufen **aller** nicht geschützten Datenbanken (einschließlich der neu hinzugefügten) ausgeführt wird. Verwenden Sie zum Ausführen einer neuen Abfrage das PowerShell-Cmdlet [Initialize-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/initialize-azrecoveryservicesbackupprotectableitem) auf dem virtuellen SQL-Computer. Der Befehl wartet, bis der Vorgang abgeschlossen ist. Verwenden Sie später das PowerShell-Cmdlet [Get-AzRecoveryServicesBackupProtectableItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectableitem), um die Liste der neuesten nicht geschützten SQL-Komponenten abzurufen.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
```

Nachdem die entsprechenden schützbaren Elemente abgerufen wurden, aktivieren Sie die Sicherungen wie im [Abschnitt oben](#configuring-backup) angegeben.
Wenn neue Datenbanken nicht manuell erkannt werden sollen, können Sie sich auch für automatischen Schutz entscheiden, wie er [weiter unten](#enable-autoprotection) erläutert ist.

## <a name="enable-autoprotection"></a>Aktivieren des automatischen Schutzes

Sie können die Sicherung so konfigurieren, dass alle zukünftig hinzugefügten Datenbanken mit einer bestimmten Richtlinie automatisch geschützt werden. Verwenden Sie zum Aktivieren des automatischen Schutzes das PowerShell-Cmdlet [Enable-AzRecoveryServicesBackupAutoProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupautoprotection).

Da die Anweisung das Sichern aller zukünftigen Datenbanken vorsieht, erfolgt der Vorgang auf der Ebene einer SQL-Instanz.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $targetvault.ID
```

Sobald der beabsichtigte automatische Schutz festgelegt ist, wird die Anfrage an den Computer zum Abrufen neu hinzugefügter Datenbanken alle acht Stunden als geplante Hintergrundaufgabe ausgeführt.

## <a name="restore-sql-dbs"></a>Wiederherstellen von SQL-Datenbanken

Azure Backup kann auf virtuellen Azure-Computern ausgeführte SQL Server-Datenbanken wie folgt wiederherstellen:

* Wiederherstellung eines bestimmten Datums oder einer bestimmten Uhrzeit (sekundengenau) mithilfe von Transaktionsprotokollsicherungen. Azure Backup ermittelt automatisch die geeignete vollständige differenzielle Sicherung und die Kette von Protokollsicherungen, die für die Wiederherstellung Ihrer Daten basierend auf dem ausgewählten Zeitpunkt benötigt werden.
* Wiederherstellung einer bestimmten vollständigen oder differenziellen Sicherung, um die Daten eines bestimmten Wiederherstellungspunkts wiederherzustellen.

Überprüfen Sie vor dem Wiederherstellen von SQL-Datenbanken die [hier](restore-sql-database-azure-vm.md#prerequisites) genannten Voraussetzungen.

Rufen Sie zunächst die jeweilige gesicherte SQL-Datenbank mit dem PowerShell-Cmdlet [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) ab.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
```

### <a name="fetch-the-relevant-restore-time"></a>Abrufen der relevanten Wiederherstellungszeit

Wie oben beschrieben, können Sie die gesicherte SQL-Datenbank auf eine vollständige/differenzielle Kopie **ODER** auf einen Protokollzeitpunkt wiederherstellen.

#### <a name="fetch-distinct-recovery-points"></a>Abrufen eindeutiger Wiederherstellungspunkte

Mit [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) können Sie eindeutige (vollständige/differenzielle) Wiederherstellungspunkte für eine gesicherte SQL-Datenbank abrufen.

```powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```output
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
```

Verwenden Sie zum Abrufen des relevanten Wiederherstellungspunkts den Filter „RecoveryPointId“ oder einen Arrayfilter.

```powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
```

#### <a name="fetch-point-in-time-recovery-point"></a>Abrufen eines Zeitpunkts als Wiederherstellungspunkt

Wenn Sie die Datenbank auf einen bestimmten Zeitpunkt wiederherstellen möchten, verwenden Sie dazu das PowerShell-Cmdlet [Get-AzRecoveryServicesBackupRecoveryLogChain](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverylogchain). Das Cmdlet gibt eine Liste mit Datumsangaben zurück, die Start- und Endzeiten einer ununterbrochenen, kontinuierlichen Protokollkette für das jeweilige SQL-Sicherungselement darstellen. Der gewünschte Zeitpunkt sollte innerhalb dieses Bereichs liegen.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -VaultId $targetVault.ID
```

Die Ausgabe entspricht etwa folgendem Beispiel:

```output
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
```

Die vorstehende Ausgabe bedeutet, dass Sie auf jeden beliebigen Zeitpunkt zwischen der angezeigten Start- und Endzeit wiederherstellen können. Die Zeitangaben sind in UTC. Erstellen Sie in PowerShell einen beliebigen Zeitpunkt innerhalb des oben gezeigten Bereichs.

> [!NOTE]
> Wenn ein Protokollzeitpunkt für die Wiederherstellung ausgewählt wird, müssen Sie keinen Startzeitpunkt angeben, d. h., die Datenbank wird aus der vollständigen Sicherung wiederhergestellt. Der Azure Backup-Dienst übernimmt die gesamte Wiederherstellungsplanung, d. h., welche vollständige Sicherung auszuwählen ist, welche Protokollsicherungen anzuwenden sind usw.

### <a name="determine-recovery-configuration"></a>Bestimmen der Wiederherstellungskonfiguration

Bei einer SQL-Datenbankwiederherstellung werden die folgenden Wiederherstellungsszenarien unterstützt.

* Überschreiben der gesicherten SQL-Datenbank mit Daten von einem anderen Wiederherstellungspunkt – OriginalWorkloadRestore
* Wiederherstellen der SQL-Datenbank als eine neue Datenbank in derselben SQL-Instanz – AlternateWorkloadRestore
* Wiederherstellen der SQL-Datenbank als eine neue Datenbank in einer anderen SQL-Instanz auf einem anderen virtuellen SQL-Computer – AlternateWorkloadRestore
* Wiederherstellen der SQL-Datenbank als BAK-Dateien -RestoreAsFiles

Nach dem Abrufen des relevanten Wiederherstellungspunkts (eindeutig oder Protokollzeitpunkt) rufen Sie das Wiederherstellungskonfigurationsobjekt mit dem PowerShell-Cmdlet [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupworkloadrecoveryconfig) entsprechend dem gewünschten Wiederherstellungsplan ab.

#### <a name="original-workload-restore"></a>Wiederherstellen der ursprünglichen Workload

Um die gesicherte Datenbank mit Daten vom Wiederherstellungspunkt zu überschreiben, geben Sie einfach das richtige Flag und den relevanten Wiederherstellungspunkt an, wie es in den folgenden Beispielen gezeigt ist.

##### <a name="original-restore-with-distinct-recovery-point"></a>Wiederherstellen des ursprünglichen Zustands mit eindeutigem Wiederherstellungspunkt

```powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="original-restore-with-log-point-in-time"></a>Wiederherstellen des ursprünglichen Zustands mit Protokollzeitpunkt

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Wiederherstellen einer anderen Workload

> [!IMPORTANT]
> Eine gesicherte SQL-Datenbank kann nur auf einem virtuellen Azure-Computer, der beim entsprechenden Tresor „registriert“ ist, als eine neue Datenbank in einer anderen SQL-Instanz wiederhergestellt werden.

Wie oben beschrieben, stellen Sie im Fall einer SQL-Zielinstanz auf einem anderen virtuellen Azure-Computer sicher, dass dieser [beim entsprechenden Tresor registriert ist](#registering-the-sql-vm) und die relevante SQL-Instanz als schützbares Element angezeigt wird.

```powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
```

Übergeben Sie dann einfach den relevanten Wiederherstellungspunkt und die SQL-Zielinstanz mit dem richtigen Flag, wie es nachfolgend gezeigt ist.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Wiederherstellen eines anderen Zustands mit eindeutigem Wiederherstellungspunkt

```powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="alternate-restore-with-log-point-in-time"></a>Wiederherstellen eines anderen Zustands mit Protokollzeitpunkt

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="restore-as-files"></a>Wiederherstellen als Dateien

Um die Sicherungsdaten als BAK-Dateien und nicht als Datenbank wiederherzustellen, wählen Sie die Option **Als Dateien wiederherstellen** aus. Die gesicherte SQL-Datenbank kann auf allen virtuellen Zielcomputern wiederhergestellt werden, die für diesen Tresor registriert sind.

```powershell
$TargetContainer= Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName "VM name" -VaultId $vaultID
```

##### <a name="restore-as-files-with-distinct-recovery-point"></a>Wiederherstellen als Dateien mit eindeutigem Wiederherstellungspunkt

```powershell
$FileRestoreWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-latest-full"></a>Wiederherstellen als Dateien mit Protokollzeitpunkt von letzter vollständiger Sicherung

```powershell
$FileRestoreWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-a-specified-full"></a>Wiederherstellen als Dateien mit Protokollzeitpunkt von einer angegebenen vollständigen Sicherung

Wenn Sie eine bestimmte vollständige Sicherung angeben möchten, die für die Wiederherstellung verwendet werden soll, verwenden Sie den folgenden Befehl:

```powershell
$FileRestoreWithLogAndSpecificFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -FromFull $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

Das endgültige Konfigurationsobjekt für den Wiederherstellungspunkt, das mit dem PowerShell-Cmdlet [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupworkloadrecoveryconfig) abgerufen wird, enthält alle relevanten Informationen für die Wiederherstellung und ähnelt der folgenden Abbildung.

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : No
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

Sie können den wiederhergestellten Datenbanknamen sowie der Felder „OverwriteWLIfpresent“, „NoRecoveryMode“ und „TargetPhysicalPath“ bearbeiten. Rufen Sie weitere Details für die Zieldateipfade ab, wie es nachfolgend gezeigt ist.

```powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath
```

```output
MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
```

Legen Sie die relevanten PowerShell-Eigenschaften als Zeichenfolgenwerte wie unten gezeigt fest.

```powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl
```

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : Yes
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

> [!IMPORTANT]
> Stellen Sie sicher, dass das endgültige Wiederherstellungskonfigurationsobjekt alle erforderlichen und richtigen Werte aufweist, da der Wiederherstellungsvorgang auf dem Konfigurationsobjekt basiert.

### <a name="restore-with-relevant-configuration"></a>Wiederherstellen mit relevanter Konfiguration

Nachdem das relevante Wiederherstellungskonfigurationsobjekt abgerufen und überprüft wurde, starten Sie den Wiederherstellungsvorgang mit dem PowerShell-Cmdlet [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem).

```powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
```

Der Wiederherstellungsvorgang gibt einen nachzuverfolgenden Auftrag zurück.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
```

## <a name="manage-sql-backups"></a>Verwalten von SQL-Sicherungen

### <a name="on-demand-backup"></a>Bedarfsgesteuerte Sicherung

Nachdem die Sicherung für eine Datenbank aktiviert wurde, können Sie mit dem PowerShell-Cmdlet [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) auch eine bedarfsgesteuerte Sicherung für die Datenbank auslösen. Im folgenden Beispiel wird eine vollständige Sicherung für eine SQL-Datenbank mit aktivierter Komprimierung ausgelöst, und die vollständige Sicherung soll 60 Tage lang aufbewahrt werden.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

Der Befehl zur bedarfsgesteuerten Sicherung gibt einen nachzuverfolgenden Auftrag zurück.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
```

Wenn die Ausgabe verloren geht oder Sie die relevante Auftrags-ID abrufen möchten, [rufen Sie die Liste der Aufträge](#track-azure-backup-jobs) aus dem Azure Backup-Dienst ab, und verfolgen Sie dann die Liste und ihre Details.

### <a name="change-policy-for-backup-items"></a>Ändern der Richtlinie für Sicherungselemente

Sie können die Richtlinie des gesicherten Elements von *Richtlinie1* in *Richtlinie2* ändern. Zum Wechseln der Richtlinien für ein gesichertes Element rufen Sie die entsprechende Richtlinie und das Sicherungselement ab, und verwenden Sie den Befehl [Enable-AzRecoveryServices](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) mit dem Sicherungselement als Parameter.

```powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
```

Der Befehl wartet, bis die Sicherungskonfiguration abgeschlossen ist, und gibt die folgende Ausgabe zurück:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="edit-an-existing-backup-policy"></a>Bearbeiten einer vorhandenen Sicherungsrichtlinie

Zum Bearbeiten einer vorhandenen Richtlinie verwenden Sie den Befehl [Set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy).

```powershell
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $Pol -SchedulePolicy $SchPol -RetentionPolicy $RetPol
```

Überprüfen Sie die Sicherungsaufträge nach einiger Zeit, um eventuelle Fehler zu zu entdecken. Wenn es Probleme gibt, müssen Sie sie beheben. Führen Sie dann den Befehl „Richtlinie bearbeiten“ mit dem Parameter **FixForInconsistentItems** erneut aus, um die Bearbeitung der Richtlinie für alle Sicherungsobjekte, für die der Vorgang zuvor fehlgeschlagen ist, erneut zu versuchen.

```powershell
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $Pol -FixForInconsistentItems
```

### <a name="re-register-sql-vms"></a>Erneutes Registrieren von virtuellen SQL-Computern

> [!WARNING]
> Lesen Sie vor dem Versuch einer erneuten Registrierung unbedingt dieses [Dokument](backup-sql-server-azure-troubleshoot.md#re-registration-failures), um die Symptome und Ursachen von Fehlern zu verstehen.

Um die erneute Registrierung des virtuellen SQL-Computers auszulösen, rufen Sie den relevanten Sicherungscontainer ab, und übergeben Sie ihn an das Cmdlet „Register“.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
```

### <a name="stop-protection"></a>Schutz beenden

#### <a name="retain-data"></a>Beibehalten von Daten

Wenn Sie den Schutz verhindern möchten, können Sie dazu das PowerShell-Cmdlet [Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) verwenden. Dadurch werden die geplanten Sicherungen beendet, doch werden die bis zu diesem Zeitpunkt gesicherten Daten dauerhaft beibehalten.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
```

#### <a name="delete-backup-data"></a>Löschen von Sicherungsdaten

Um die gespeicherten Sicherungsdaten im Tresor vollständig zu entfernen, fügen Sie einfach das Flag/den Switch „-RemoveRecoveryPoints“ zum [Befehl „Disable“](#retain-data) hinzu.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
```

#### <a name="disable-auto-protection"></a>Deaktivieren des automatischen Schutzes

Wenn der automatische Schutz für eine SQL-Instanz konfiguriert wurde, können Sie ihn mit dem PowerShell-Cmdlet [Disable-AzRecoveryServicesBackupAutoProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection) deaktivieren.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
```

#### <a name="unregister-sql-vm"></a>Aufheben der Registrierung des virtuellen SQL-Computers

Wenn alle Datenbanken eines SQL-Servers [nicht mehr geschützt sind und es keine Sicherungsdaten gibt](#delete-backup-data), können Sie die Registrierung des virtuellen SQL-Computers bei diesem Tresor aufheben. Erst dann können Sie Datenbanken in einem anderen Tresor schützen. Mit dem PowerShell-Cmdlet [Unregister-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) können Sie die Registrierung des virtuellen SQL-Computers aufheben.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
```

### <a name="track-azure-backup-jobs"></a>Nachverfolgen von Azure Backup-Aufträgen

Sie müssen beachten, dass Azure Backup nur vom Benutzer ausgelöste Aufträge in der SQL-Sicherung nachverfolgt. Geplante Sicherungen (einschließlich Protokollsicherungen) werden im Portal oder in PowerShell nicht angezeigt. Wenn jedoch Fehler bei geplanten Aufträgen auftreten, wird eine [Sicherungswarnung](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) generiert und im Portal angezeigt. [Verwenden Sie Azure Monitor](backup-azure-monitoring-use-azuremonitor.md) zum Nachverfolgen aller geplanten Aufträge und anderer relevanter Informationen.

Benutzer können bedarfsgesteuerte bzw. vom Benutzer ausgelöste Vorgänge anhand der Auftrags-ID nachverfolgen, die in der [Ausgabe](#on-demand-backup) asynchroner Aufträge (z. B. einer Sicherung) zurückgegeben ist. Mit dem PowerShell-Cmdlet [Get-AzRecoveryServicesBackupJobDetail](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjobdetail) können Sie einen Auftrag und seine Details nachverfolgen.

```powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
```

Zum Abrufen der Liste von bedarfsgesteuerten Aufträgen und deren Status aus dem Azure Backup-Dienst verwenden Sie das PowerShell-Cmdlet [Get-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob). Im folgenden Beispiel werden alle in Bearbeitung befindlichen SQL-Aufträge zurückgegeben.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Zum Abbrechen eines Auftrags in Bearbeitung verwenden Sie das PowerShell-Cmdlet [Stop-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob).

## <a name="managing-sql-always-on-availability-groups"></a>Verwalten von SQL-AlwaysOn-Verfügbarkeitsgruppen

Stellen Sie im Fall von SQL-AlwaysOn-Verfügbarkeitsgruppen sicher, dass [alle Knoten der Verfügbarkeitsgruppe registriert werden](#registering-the-sql-vm). Nach der Registrierung aller Knoten wird unter den schützbaren Elementen ein SQL-Verfügbarkeitsgruppenobjekt logisch erstellt. Die Datenbanken unter der SQL-Verfügbarkeitsgruppe werden als „SQLDatabase“ aufgeführt. Die Knoten werden als eigenständige Instanzen angezeigt, und die darunter befindlichen SQL-Standarddatenbanken werden ebenfalls als SQL-Datenbanken aufgeführt.

Angenommen beispielsweise, eine SQL-Verfügbarkeitsgruppe hat zwei Knoten ( *sql-server-0* und *sql-server-1* ) und eine SQL-Verfügbarkeitsgruppendatenbank. Wenn Sie nach der Registrierung dieser beiden Knoten [die schützbaren Elemente auflisten](#fetching-sql-dbs), sind die folgenden Komponenten enthalten:

* Ein SQL-Verfügbarkeitsgruppenobjekt – Typ des schützbaren Elements ist „SQLAvailabilityGroup“
* Eine SQL-Verfügbarkeitsgruppendatenbank – Typ des schützbaren Elements ist „SQLDatabase“
* sql-server-0 – Typ des schützbaren Elements ist „SQLInstance“
* sql-server-1 – Typ des schützbaren Elements ist „SQLInstance“
* Alle SQL-Standarddatenbanken (Master, Modell, MSDB) unter „sql-server-0“ – Typ des schützbaren Elements ist „SQLDatabase“
* Alle SQL-Standarddatenbanken (Master, Modell, MSDB) unter „sql-server-1“ – Typ des schützbaren Elements ist „SQLDatabase“

Beim [Auflisten von Sicherungscontainern](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) werden auch „sql-server-0“ und „sql-server-1“ als „AzureVMAppContainer“ aufgeführt.

Rufen Sie einfach die entsprechende Datenbank ab, um die [Sicherung zu aktivieren](#configuring-backup). Die PowerShell-Cmdlets für die [bedarfsgesteuerte Sicherung](#on-demand-backup) und [Wiederherstellung](#restore-sql-dbs) sind identisch.

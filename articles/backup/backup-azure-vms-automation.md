---
title: Sichern und Wiederherstellen von virtuellen Azure-Computern mithilfe von Azure Backup mit PowerShell
description: Beschreibt das Sichern und Wiederherstellen von virtuellen Azure-Computern mithilfe von Azure Backup und PowerShell
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: raynew
ms.openlocfilehash: 4df65819256e6a81a07927d463d130fbfdf9317a
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255011"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>Sichern und Wiederherstellen von virtuellen Azure-Computern mit PowerShell

Dieser Artikel erläutert das Sichern und Wiederherstellen eines virtuellen Azure-Computers in einem [Azure Backup](backup-overview.md)-Recovery Services-Tresor mit PowerShell-Cmdlets.

In diesem Artikel wird Folgendes behandelt:

> [!div class="checklist"]
> * Erstellen eines Recovery Services-Tresors und Einrichten des Tresorkontexts.
> * Definieren einer Sicherungsrichtlinie
> * Anwenden der Sicherungsrichtlinie zum Schützen mehrerer virtueller Computer
> * Auslösen eines bedarfsgesteuerten Sicherungsauftrags für die geschützten virtuellen Computer. Bevor Sie einen virtuellen Computer (Virtual Machine, VM) sichern (oder schützen) können, müssen Sie die [Voraussetzungen](backup-azure-arm-vms-prepare.md) schaffen, um Ihre Umgebung auf den Schutz Ihrer VMs vorzubereiten.

## <a name="before-you-start"></a>Vorbereitung

- [Lesen Sie weitere Informationen](backup-azure-recovery-services-vault-overview.md) zu Recovery Services-Tresoren.
- [Überprüfen Sie](backup-architecture.md#architecture-direct-backup-of-azure-vms) die Architektur für die Azure-VM-Sicherung, [erfahren Sie mehr über](backup-azure-vms-introduction.md) den Sicherungsvorgang, und [überprüfen Sie](backup-support-matrix-iaas.md) die Unterstützung, Einschränkungen und Voraussetzungen.
- Sehen Sie sich die PowerShell-Objekthierarchie für Recovery Services an.

## <a name="recovery-services-object-hierarchy"></a>Recovery Services-Objekthierarchie

Die Objekthierarchie ist im folgenden Diagramm zusammengefasst.

![Recovery Services-Objekthierarchie](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Sehen Sie sich die [Cmdlet-Referenz](https://docs.microsoft.com/powershell/module/Az.RecoveryServices/?view=azps-1.4.0) zu **Az.RecoveryServices** in der Azure-Bibliothek an.

## <a name="set-up-and-register"></a>Einrichten und Registrieren

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vorbereitung:

1. [Laden Sie die neueste Version von PowerShell herunter.](https://docs.microsoft.com/powershell/azure/install-az-ps)

2. Ermitteln Sie die Azure-PowerShell-Cmdlets zur Datensicherung mithilfe des folgenden Befehls:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

    Die Aliase und Cmdlets für Azure Backup, Azure Site Recovery und den Recovery Services-Tresor werden angezeigt. Die folgende Abbildung ist ein Beispiel für die angezeigten Elemente. Dies ist nicht die vollständige Liste mit den Cmdlets.

    ![Recovery Services-Liste](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Melden Sie sich mithilfe von **Connect-AzAccount** bei Ihrem Azure-Konto an. Dieses Cmdlet ruft eine Webseite auf, die Sie zur Eingabe Ihrer Kontoanmeldeinformationen auffordert:

    * Alternativ können Sie Ihre Kontoanmeldeinformationen als Parameter im Cmdlet **Connect-AzAccount** mit dem Parameter **-Credential** angeben.
    * Wenn Sie als CSP-Partner für einen Mandanten tätig sind, geben Sie den Kunden mit dessen Mandanten-ID oder primärem Mandantendomänennamen als Mandanten an. Beispiel:  **Connect-AzAccount -Tenant „fabrikam.com“**

4. Da ein Konto mehrere Abonnements enthalten kann, müssen Sie das Abonnement, das Sie verwenden möchten, dem Konto zuordnen:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Falls Sie Azure Backup zum ersten Mal verwenden, müssen Sie das Cmdlet **[Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** verwenden, um den Azure Recovery Service-Anbieter bei Ihrem Abonnement zu registrieren.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Mithilfe der folgenden Befehle können Sie überprüfen, ob die Anbieter erfolgreich registriert wurden:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

    In der Befehlsausgabe sollte sich der **RegistrationState**-Wert in **Registriert** ändern. Ist dies nicht der Fall, führen Sie einfach das Cmdlet **[Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** erneut aus.


## <a name="create-a-recovery-services-vault"></a>Erstellen eines Recovery Services-Tresors

Mit den folgenden Schritten können Sie einen Recovery Services-Tresor erstellen. Ein Recovery Services-Tresor unterscheidet sich von einem Sicherungstresor.

1. Der Recovery Services-Tresor ist eine Resource Manager-Ressource. Deshalb müssen Sie ihn in eine Ressourcengruppe einfügen. Sie können eine vorhandene Ressourcengruppe verwenden oder mit dem Cmdlet **[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)** eine Ressourcengruppe erstellen. Wenn Sie eine Ressourcengruppe erstellen, geben Sie den Namen und den Speicherort für die Ressourcengruppe an.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Erstellen Sie mithilfe des Cmdlets [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvault?view=azps-1.4.0) den Recovery Services-Tresor. Stellen Sie sicher, dass Sie den gleichen Speicherort für den Tresor angeben, der für die Ressourcengruppe verwendet wurde.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Geben Sie den Typ der zu verwendenden Speicherredundanz an – entweder [lokal redundanter Speicher (LRS)](../storage/common/storage-redundancy-lrs.md) oder [geografisch redundanter Speicher (GRS)](../storage/common/storage-redundancy-grs.md). Das folgende Beispiel zeigt, dass für die Option „BackupStorageRedundancy“ für „testVault“ der Wert auf „GeoRedundant“ festgelegt ist.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperty  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Viele Azure Backup-Cmdlets benötigen das Recovery Services-Tresorobjekt als Eingabe. Aus diesem Grund sollte das zur Sicherung verwendete Recovery Services-Tresorobjekt in einer Variablen gespeichert werden.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Anzeigen von Tresoren in einem Abonnement

Verwenden Sie [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0), um alle Tresore des Abonnements anzuzeigen:

```powershell
Get-AzRecoveryServicesVault
```

Die Ausgabe ähnelt dem folgenden Beispiel. Beachten Sie, dass die zugeordneten Elemente „ResourceGroupName“ und „Location“ angegeben sind.

```
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="back-up-azure-vms"></a>Sichern von Azure-VMs

Verwenden Sie einen Recovery Services-Tresor zum Schutz Ihrer virtuellen Computer. Bevor Sie den Schutz anwenden, legen Sie den Tresorkontext (den Typ der Daten, die im Tresor geschützt werden) fest, und überprüfen Sie die Schutzrichtlinie. Die Schutzrichtlinie stellt den Zeitplan für die Ausführung des Sicherungsauftrags dar, und für die Aufbewahrungsdauer jeder Sicherungsmomentaufnahme.

### <a name="set-vault-context"></a>Festlegen des Tresorskontexts

Verwenden Sie vor dem Aktivieren des Schutzes auf einem virtuellen Computer [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0), um den Tresorkontext festzulegen. Sobald der Tresorkontext festgelegt ist, gilt er für alle nachfolgenden-Cmdlets. Im folgenden Beispiel wird der Tresorkontext für den Tresor *testvault* festgelegt.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="modifying-storage-replication-settings"></a>Ändern der Einstellungen für die Speicherreplikation

Verwenden Sie den Befehl [Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty), um die Konfiguration für Speicherreplikation des Tresors auf „LRS/GRS“ festzulegen.

```powershell
$vault= Get-AzRecoveryServicesVault -name "testvault"
Set-AzRecoveryServicesBackupProperty -Vault $vault -BackupStorageRedundancy GeoRedundant/LocallyRedundant
```

> [!NOTE]
> Die Speicherredundanz kann nur geändert werden, wenn im Tresor keine Sicherungselemente vorhanden sind.

### <a name="create-a-protection-policy"></a>Erstellen einer Schutzrichtlinie

Wenn Sie einen Recovery Services-Tresor erstellen, enthält er automatisch standardmäßige Schutz- und Aufbewahrungsrichtlinien. Die standardmäßige Schutzrichtlinie löst täglich zu einem angegebenen Zeitpunkt einen Sicherungsauftrag aus. Die standardmäßige Aufbewahrungsrichtlinie bewahrt den täglichen Wiederherstellungspunkt für 30 Tage auf. Mit der Standardrichtlinie können Sie Ihren virtuellen Computer schnell schützen und die Richtlinie später mit anderen Details bearbeiten.

Verwenden Sie **[Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)** , um die im Tresor verfügbaren Schutzrichtlinien anzuzeigen. Sie können dieses Cmdlet zum Abrufen einer bestimmten Richtlinie verwenden oder um Richtlinien anzuzeigen, die einem bestimmten Workloadtyp zugeordnet sind. Im folgenden Beispiel werden Richtlinien für den Workloadtyp „AzureVM“ abgerufen.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> Die Zeitzone des Feldes „BackupTime“ in PowerShell ist UTC. Wenn jedoch der Zeitpunkt der Sicherung im Azure-Portal angezeigt wird, wird die Zeit auf Ihre lokale Zeitzone eingestellt.
>
>

Eine Sicherungsschutzrichtlinie ist mindestens einer Aufbewahrungsrichtlinie zugeordnet. In einer Aufbewahrungsrichtlinie wird definiert, wie lange ein Wiederherstellungspunkt gespeichert wird, bevor er gelöscht wird.

- Verwenden Sie [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject), um die standardmäßige Aufbewahrungsrichtlinie anzuzeigen.
- Auf ähnliche Weise können Sie [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) verwenden, um die standardmäßige Zeitplanrichtlinie abzurufen.
- Das Cmdlet [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) erstellt ein PowerShell-Objekt, das Informationen zu Sicherungsrichtlinien enthält.
- Die Zeitplan- und Aufbewahrungsrichtlinienobjekte werden als Eingaben für das Cmdlet New-AzRecoveryServicesBackupProtectionPolicy verwendet.

Eine Startzeit wird standardmäßig im Schedule Policy Object (Zeitplanrichtlinienobjekt) definiert. Verwenden Sie das folgende Beispiel, um die Startzeit in die gewünschte Startzeit zu ändern. Die gewünschte Startzeit sollte ebenfalls in UTC angegeben werden. Beim nachstehenden Beispiel wird vorausgesetzt, dass die gewünschte Startzeit für tägliche Sicherungen 01:00 Uhr UTC ist.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

Im folgenden Beispiel werden die Zeitplanrichtlinie und die Aufbewahrungsrichtlinie in Variablen gespeichert. Im Beispiel werden diese Variablen verwendet, um die Parameter beim Erstellen einer Schutzrichtlinie,*NewPolicy*, zu definieren.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Schutz aktivieren

Nachdem Sie die Schutzrichtlinie definiert haben, müssen Sie noch die Richtlinie für ein Element aktivieren. Verwenden Sie [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection), um den Schutz zu aktivieren. Zum Aktivieren des Schutzes sind zwei Objekte erforderlich – das Element und die Richtlinie. Sobald die Richtlinie mit dem Tresor verknüpft ist, wird der Sicherungsworkflow zu dem im Richtlinienzeitplan definierten Zeitpunkt gestartet.

Im folgenden Beispiel wird der Schutz für das Element „V2VM“ mithilfe der Richtlinie „NewPolicy“ aktiviert. Die Beispiele unterscheiden sich in Abhängigkeit davon, ob der virtuelle Computer verschlüsselt ist und welche Art von Verschlüsselung verwendet wird.

Gehen Sie wie folgt vor, um den Schutz für **nicht verschlüsselte Resource Manager-VMs** zu aktivieren:

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Um den Schutz auf verschlüsselten VMs (verschlüsselt mit BEK und KEK) zu aktivieren, müssen Sie dem Azure Backup-Dienst die Berechtigung zum Lesen von Schlüsseln und Geheimnissen aus dem Schlüsseltresor zuweisen.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Um den Schutz auf **verschlüsselten VMs (nur mit BEK verschlüsselt)** zu aktivieren, müssen Sie dem Azure Backup-Dienst die Berechtigung zum Lesen von Geheimnissen aus dem Schlüsseltresor zuweisen.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Wenn Sie die Azure Government-Cloud verwenden, verwenden Sie im Cmdlet [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) den Wert ff281ffe-705c-4f53-9f37-a40e6f2c68f3 für den Parameter ServicePrincipalName.
>

## <a name="monitoring-a-backup-job"></a>Überwachen eines Sicherungsauftrags

Sie können Vorgänge mit langer Ausführungsdauer, z.B. Sicherungsaufträge, ohne das Azure-Portal überwachen. Mit dem Cmdlet [Get-AzRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) können Sie den Status eines Auftrags abrufen, der sich in Bearbeitung befindet. Dieses Cmdlet ruft die Sicherungsaufträge für einen bestimmten Tresor ab. Dieser Tresor ist im Tresorkontext angegeben. Im folgenden Beispiel wird der Status eines laufenden Auftrags als Array abgerufen und in der Variable „$joblist“ gespeichert.

```powershell
$joblist = Get-AzRecoveryservicesBackupJob –Status "InProgress"
$joblist[0]
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Anstatt die Fertigstellung dieser Aufträge abzufragen (und dafür unnötigen zusätzlichen Code zu schreiben), verwenden Sie das Cmdlet [Wait-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob). Dieses Cmdlet hält die Ausführung an, bis entweder der Auftrag abgeschlossen oder der angegebene Timeoutwert erreicht ist.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="manage-azure-vm-backups"></a>Verwalten von Azure-VM-Sicherungen

### <a name="modify-a-protection-policy"></a>Ändern einer Schutzrichtlinie

Verwenden Sie [Set-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) zum Bearbeiten der Schutzrichtlinie, um die Objekte SchedulePolicy oder RetentionPolicy zu ändern.

#### <a name="modifying-scheduled-time"></a>Ändern der geplanten Zeit

Beim Erstellen einer Schutzrichtlinie wird ihr standardmäßig eine Startzeit zugewiesen. In den folgenden Beispielen wird gezeigt, wie Sie die Startzeit einer Schutzrichtlinie ändern.

````powershell
$SchPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z" (This is the time that the customer wants to start the backup)
$UtcTime = $UtcTime.ToUniversalTime()
$SchPol.ScheduleRunTimes[0] = $UtcTime
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -SchedulePolicy $SchPol
````

#### <a name="modifying-retention"></a>Ändern der Aufbewahrungsdauer

Im folgenden Beispiel wird der Aufbewahrungswert von Wiederherstellungspunkten in 365 Tage geändert.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

#### <a name="configuring-instant-restore-snapshot-retention"></a>Konfigurieren der Aufbewahrungsdauer von Momentaufnahmen für sofortige Wiederherstellung

> [!NOTE]
> Ab Az PS, Version 1.6.0, kann die Aufbewahrungsdauer von Momentaufnahmen für sofortige Wiederherstellung in Richtlinien mithilfe von PowerShell aktualisiert werden.

````powershell
PS C:\> $bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=7
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
````

Der Standardwert ist „2“. Der Benutzer kann den Wert auf mindestens „1“ und maximal „5“ festlegen. Bei Richtlinien zu wöchentlichen Sicherungen wird der Zeitraum auf „5“ festgelegt und kann nicht geändert werden.

### <a name="trigger-a-backup"></a>Auslösen einer Sicherung

Verwenden Sie [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem), um einen Sicherungsauftrag auszulösen. Wenn es die erste Sicherung ist, handelt es sich um eine vollständige Sicherung. Nachfolgende Sicherungen erstellen inkrementelle Kopien. Im folgenden Beispiel wird eine VM-Sicherung 60 Tage lang aufbewahrt:

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
$job = Backup-AzRecoveryServicesBackupItem -Item $item -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> Die Zeitzone der Felder „StartTime“ und „EndTime“ in PowerShell ist UTC. Wenn jedoch der Zeitpunkt im Azure-Portal angezeigt wird, wird die Zeit auf Ihre lokale Zeitzone eingestellt.
>
>

### <a name="change-policy-for-backup-items"></a>Ändern der Richtlinie für Sicherungselemente

Der Benutzer kann entweder die vorhandene Richtlinie bearbeiten oder die Richtlinie des gesicherten Elements von Richtlinie 1 in Richtlinie 2 ändern. Zum Wechseln der Richtlinien für ein gesichertes Element rufen Sie einfach die entsprechende Richtlinie und das Sicherungselement ab, und verwenden Sie den Befehl [Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) mit dem Sicherungselement als Parameter.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType AzureVM -BackupManagementType AzureVM -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
````

Der Befehl wartet, bis die Sicherungskonfiguration abgeschlossen ist, und gibt die folgende Ausgabe zurück:

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
TestVM           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>Schutz beenden

#### <a name="retain-data"></a>Beibehalten von Daten

Wenn der Benutzer den Schutz beenden möchte, kann er dazu das PS-Cmdlet [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) verwenden. Dadurch werden die geplanten Sicherungen beendet, doch werden die bis zu diesem Zeitpunkt gesicherten Daten dauerhaft beibehalten.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Löschen von Sicherungsdaten

Um die gespeicherten Sicherungsdaten im Tresor vollständig zu entfernen, fügen Sie einfach das Flag/den Switch „-RemoveRecoveryPoints“ zum [Befehl „Disable“](#retain-data) hinzu.

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

## <a name="restore-an-azure-vm"></a>Wiederherstellen eines virtuellen Azure-Computers

Es gibt einen wichtigen Unterschied zwischen dem Wiederherstellen eines virtuellen Computers mithilfe des Azure-Portals und dem Wiederherstellen eines virtuellen Computers mithilfe von PowerShell. Mit PowerShell ist der Wiederherstellungsvorgang mit der Erstellung der Datenträger und den Konfigurationsinformationen aus dem Wiederherstellungspunkt abgeschlossen. Beim Wiederherstellungsvorgang wird kein virtueller Computer erstellt. Informationen zum Erstellen eines virtuellen Computers aus einem Datenträger finden Sie im Abschnitt [Erstellen eines virtuellen Computers aus wiederhergestellten Datenträgern](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Wenn Sie nicht den gesamten virtuellen Computer wiederherstellen möchten, sondern nur einige Dateien aus einer Azure-VM-Sicherung, helfen Ihnen die Informationen im [Abschnitt zur Dateiwiederherstellung](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup) weiter.

> [!Tip]
> Beim Wiederherstellungsvorgang wird kein virtueller Computer erstellt.
>
>

Die folgende Grafik zeigt die Objekthierarchie von „RecoveryServicesVault“ bis „BackupRecoveryPoint“.

![Die Recovery Services-Objekthierarchie zeigt die BackupContainer an](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Identifizieren Sie das gesicherte Element und den Wiederherstellungspunkt, der die Zeitpunktdaten enthält, um die gesicherten Daten wiederherstellen zu können. Verwenden Sie [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem), um Daten aus dem Tresor in Ihrem Konto wiederherzustellen.

Zum Wiederherstellen eines virtuellen Azure-Computers müssen Sie folgende grundlegende Schritte ausführen:

* Wählen Sie den virtuellen Computer aus.
* Wählen Sie einen Wiederherstellungspunkt aus.
* Stellen Sie die Datenträger wieder her.
* Erstellen Sie den virtuellen Computer über die gespeicherten Datenträger.

### <a name="select-the-vm"></a>Auswählen des virtuellen Computers

Zum Abrufen des PowerShell-Objekts, das das richtige Sicherungselement identifiziert, starten Sie vom Container im Tresor aus, und durchlaufen Sie die Objekthierarchie nach unten. Verwenden Sie das Cmdlet [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer), und leiten Sie dessen Ausgabe an das Cmdlet [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) weiter, um den Container auszuwählen, der den virtuellen Computer darstellt.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Auswählen eines Wiederherstellungspunkts

Verwenden Sie das Cmdlet [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint), um alle Wiederherstellungspunkte für das Sicherungselement aufzulisten. Wählen Sie anschließend den zu wiederherstellenden Wiederherstellungspunkt. Wenn Sie nicht sicher sind, welchen Wiederherstellungspunkt Sie verwenden sollen, empfiehlt es sich, den letzten Punkt in der Liste auszuwählen, an dem sich RecoveryPointType = AppConsistent befand.

Im folgenden Skript ist die Variable **$rp** ein Array von Wiederherstellungspunkten für das ausgewählte Sicherungselement der letzten sieben Tage. Das Array wird in umgekehrter Reihenfolge sortiert. Der letzte Wiederherstellungspunkt liegt bei Index 0. Verwenden Sie die standardmäßige PowerShell-Arrayindizierung zum Auswählen des Wiederherstellungspunkts. Im Beispiel wählt „$rp[0]“ den letzten Wiederherstellungspunkt aus.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```powershell
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="restore-the-disks"></a>Wiederherstellung der Datenträger

Verwenden Sie das Cmdlet [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem), um Daten und die Konfiguration eines Sicherungselements auf einem Wiederherstellungspunkt wiederherzustellen. Nachdem Sie einen Wiederherstellungspunkt ermittelt haben, können Sie ihn als Wert für den **-RecoveryPoint**-Parameter verwenden. Im obigen Beispiel wurde **$rp[0]** als Wiederherstellungspunkt verwendet. Im folgenden Beispielcode ist **$rp[0]** der Wiederherstellungspunkt, der zum Wiederherstellen des Datenträgers verwendet wird.

So stellen Sie den Datenträger und die Konfigurationsinformationen wieder her:

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
$restorejob
```

#### <a name="restore-managed-disks"></a>Wiederherstellen von verwalteten Datenträgern

> [!NOTE]
> Wenn die gesicherte VM über verwaltete Datenträger verfügt und Sie diese als verwaltete Datenträger wiederherstellen möchten, können Sie die Funktion aus Azure PowerShell RM-Modul v6.7.0 oder höher nutzen.
>
>

Geben Sie den zusätzlichen Parameter **TargetResourceGroupName** an, um die RG festzulegen, in der die verwalteten Datenträger gespeichert werden. 

> [!NOTE]
> Es wird dringend empfohlen, den Parameter **TargetResourceGroupName** für die Wiederherstellung verwalteter Datenträger zu verwenden, da dies zu erheblichen Leistungssteigerungen führt. Außerdem ist dieser Parameter ab Azure Powershell Az-Module 1.0 im Falle einer Wiederherstellung mit verwalteten Datenträgern obligatorisch.
>
>


```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks"
```

Die Datei **VMConfig.JSON** wird für das Speicherkonto wiederhergestellt, und die verwalteten Datenträger werden in der angegebenen Zielressourcengruppe wiederhergestellt.

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```powershell
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Verwenden Sie das Cmdlet [Wait-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob), um auf den Abschluss des Wiederherstellungsauftrags zu warten.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Nachdem der Wiederherstellungsauftrag abgeschlossen ist, verwenden Sie das Cmdlet [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob), um die Details des Wiederherstellungsvorgangs abzurufen. Die JobDetails-Eigenschaft enthält die Informationen, die zum erneuten Erstellen des virtuellen Computers benötigt werden.

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob
```

Nachdem Sie die Datenträger wiederhergestellt haben, fahren Sie mit dem nächsten Abschnitt fort, um den virtuellen Computer zu erstellen.

## <a name="create-a-vm-from-restored-disks"></a>Erstellen eines virtuellen Computers aus wiederhergestellten Datenträgern

Nachdem Sie die Datenträger wiederhergestellt haben, können Sie die folgenden Schritte zum Erstellen und Konfigurieren des virtuellen Computers vom Datenträger verwenden.

> [!NOTE]
> Zum Erstellen von verschlüsselten virtuellen Computern aus wiederhergestellten Datenträgern muss Ihre Azure-Rolle über die Berechtigung zum Ausführen der Aktion **Microsoft.KeyVault/vaults/deploy/action** verfügen. Wenn Ihre Rolle nicht über diese Berechtigung verfügt, erstellen Sie mit dieser Aktion eine benutzerdefinierte Rolle. Weitere Informationen finden Sie unter [Benutzerdefinierte Rollen in Azure RBAC](../role-based-access-control/custom-roles.md).
>
>

> [!NOTE]
> Nach der Wiederherstellung von Datenträgern können Sie jetzt eine Bereitstellungsvorlage abrufen, die Sie direkt verwenden können, um einen neuen virtuellen Computer zu erstellen. Zum Erstellen verwalteter/nicht verwalteter VMs, die verschlüsselt/nicht verschlüsselt sind, sind nicht mehr verschiedene PS-Cmdlets erforderlich.

Aus den resultierenden Auftragsdetails ergibt sich der Vorlagen-URI, der abgefragt und bereitgestellt werden kann.

```powershell
   $properties = $details.properties
   $templateBlobURI = $properties["Template Blob Uri"]
```

Stellen Sie einfach die Vorlage bereit, um einen neuen virtuellen Computer wie [hier](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) beschrieben zu erstellen.

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobURI -storageAccountType Standard_GRS
```

Im folgenden Abschnitt werden die erforderlichen Schritte zum Erstellen eines virtuellen Computers mithilfe der „VMConfig“-Datei aufgelistet.

> [!NOTE]
> Es wird dringend empfohlen, die aufgeführte ausführliche Bereitstellungsvorlage zum Erstellen einer VM zu verwenden. Dieser Abschnitt (Punkte 1 bis 6) wird in Kürze veraltet sein.

1. Fragen Sie die Eigenschaften des wiederhergestellten Datenträgers für die Auftragsdetails ab.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Legen Sie den Azure-Speicherkontext fest, und stellen Sie die JSON-Konfigurationsdatei wieder her.

   ```powershell
   Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. Verwenden Sie die JSON-Konfigurationsdatei, um die Konfiguration des virtuellen Computers zu erstellen.

   ```powershell
   $vm = New-AzVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Fügen Sie die Betriebssystemdatenträger und Datenträger hinzu. Dieser Schritt enthält Beispiele für verschiedene verwaltete und verschlüsselte VM-Konfigurationen. Verwenden Sie das Beispiel, das für Ihre VM-Konfiguration geeignet ist.

   * **Nicht verwaltete unverschlüsselte VMs**: Verwenden Sie das folgende Beispiel für nicht verwaltete unverschlüsselte VMs.

       ```powershell
       Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
       $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
       foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
       {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
       ```

   * **Nicht verwaltete, verschlüsselte VMs mit Azure AD (nur BEK):** Für nicht verwaltete, verschlüsselte VMs mit Azure AD (nur mit BEK verschlüsselt) müssen Sie das Geheimnis für den Schlüsseltresor wiederherstellen, bevor Sie Datenträger anfügen können. Weitere Informationen finden Sie unter [Wiederherstellen eines virtuellen Azure-Computers mithilfe eines Azure Backup-Wiederherstellungspunkts](backup-azure-restore-key-secret.md). Im folgenden Beispiel wird gezeigt, wie man das Betriebssystem und die Datenträger für verschlüsselte VMs anfügt. Achten Sie beim Festlegen des Betriebssystemdatenträgers darauf, dass der relevante Betriebssystemtyp angegeben wird.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
      {
       $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **Nicht verwaltete, verschlüsselte VMs mit Azure AD (BEK und KEK):** Stellen Sie für nicht verwaltete, verschlüsselte VMs mit Azure AD (per BEK und KEK verschlüsselt) den Schlüssel und das Geheimnis für den Schlüsseltresor wieder her, bevor Sie die Datenträger anfügen. Weitere Informationen finden Sie unter [Wiederherstellen eines virtuellen Azure-Computers mithilfe eines Azure Backup-Wiederherstellungspunkts](backup-azure-restore-key-secret.md). Im folgenden Beispiel wird gezeigt, wie man das Betriebssystem und die Datenträger für verschlüsselte VMs anfügt.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
      ```

   * **Nicht verwaltete, verschlüsselte VMs ohne Azure AD (nur BEK):** Wenn für nicht verwaltete, verschlüsselte VMs ohne Azure AD (nur mit BEK verschlüsselt) das **keyVault/secret der Quelle nicht verfügbar ist**, stellen Sie die Geheimnisse in Key Vault wieder her. Gehen Sie dazu wie unter [Wiederherstellen eines nicht verschlüsselten virtuellen Computers aus einem Azure Backup-Wiederherstellungspunkt](backup-azure-restore-key-secret.md) beschrieben vor. Führen Sie dann die folgenden Skripts aus, um Verschlüsselungsdetails für das wiederhergestellte Betriebssystemblob festzulegen (dieser Schritt ist für Datenblobs nicht erforderlich). Die $dekurl können Sie aus dem wiederhergestellten keyVault abrufen.<br>

   Das folgende Skript muss nur ausgeführt werden, wenn das keyVault/secret der Quelle nicht verfügbar ist.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```

    Wenn die **Geheimnisse verfügbar sind** und die Verschlüsselungsdetails für das Betriebssystemblob ebenfalls festgelegt wurden, fügen Sie die Datenträger mit dem unten angegebenen Skript an.<br>

    Wenn die keyVault/secrets der Quelle bereits verfügbar sind, müssen Sie das obige Skript nicht ausführen.

      ```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **Nicht verwaltete, verschlüsselte VMs ohne Azure AD (BEK und KEK):** Wenn für nicht verwaltete, verschlüsselte VMs ohne Azure AD (mit BEK und KEK verschlüsselt) **keyVault/key/secret der Quelle nicht verfügbar ist**, stellen Sie den Schlüssel und die Geheimnisse in Key Vault wieder her. Gehen Sie dazu wie unter [Wiederherstellen eines nicht verschlüsselten virtuellen Computers aus einem Azure Backup-Wiederherstellungspunkt](backup-azure-restore-key-secret.md) beschrieben vor. Führen Sie dann die folgenden Skripts aus, um Verschlüsselungsdetails für das wiederhergestellte Betriebssystemblob festzulegen (dieser Schritt ist für Datenblobs nicht erforderlich). Die $dekurl und die $kekurl können Sie aus dem wiederhergestellten keyVault abrufen.

   Das folgende Skript muss nur ausgeführt werden, wenn das keyVault/key/secret der Quelle nicht verfügbar ist.

    ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""},""keyEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""keyUrl"":""$kekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```
   Wenn die **Schlüssel/Geheimnisse verfügbar sind** und die Verschlüsselungsdetails für das Betriebssystemblob festgelegt wurden, fügen Sie die Datenträger mit dem unten angegebenen Skript an.

    Wenn die keyVault/key/secrets der Quelle verfügbar sind, müssen Sie das obige Skript nicht ausführen.

    ```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **Verwaltete, unverschlüsselte VMs**: Fügen Sie für verwaltete und unverschlüsselte VMs die wiederhergestellten verwalteten Datenträger an. Ausführliche Informationen finden Sie unter [Anfügen eines Datenträgers an einen virtuellen Windows-Computer mithilfe von PowerShell](../virtual-machines/windows/attach-disk-ps.md).

   * **Verwaltete, verschlüsselte VMs mit Azure AD (nur BEK):** Fügen Sie für verwaltete, verschlüsselte VMs mit Azure AD (nur mit BEK verschlüsselt) die wiederhergestellten verwalteten Datenträger an. Ausführliche Informationen finden Sie unter [Anfügen eines Datenträgers an einen virtuellen Windows-Computer mithilfe von PowerShell](../virtual-machines/windows/attach-disk-ps.md).

   * **Verwaltete, verschlüsselte VMs mit Azure AD (BEK und KEK):** Fügen Sie für verwaltete, verschlüsselte VMs mit Azure AD (mit BEK und KEK verschlüsselt) die wiederhergestellten verwalteten Datenträger an. Ausführliche Informationen finden Sie unter [Anfügen eines Datenträgers an einen virtuellen Windows-Computer mithilfe von PowerShell](../virtual-machines/windows/attach-disk-ps.md).

   * **Verwaltete, verschlüsselte VMs ohne Azure AD (nur BEK):** Wenn für verwaltete, verschlüsselte VMs ohne Azure AD (nur mit BEK verschlüsselt) das **keyVault/secret der Quelle nicht verfügbar ist**, stellen Sie die Geheimnisse in Key Vault wieder her. Gehen Sie dazu wie unter [Wiederherstellen eines nicht verschlüsselten virtuellen Computers aus einem Azure Backup-Wiederherstellungspunkt](backup-azure-restore-key-secret.md) beschrieben vor. Führen Sie dann die folgenden Skripts aus, um Verschlüsselungsdetails auf dem wiederhergestellten Betriebssystemdatenträger festzulegen (dieser Schritt ist für Datenträger nicht erforderlich). Die $dekurl können Sie aus dem wiederhergestellten keyVault abrufen.

     Das folgende Skript muss nur ausgeführt werden, wenn das keyVault/secret der Quelle nicht verfügbar ist.  

     ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
      $diskupdateconfig = Set-AzDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
      Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
      ```

     Informationen darüber, wie Sie die wiederhergestellten verwalteten Datenträger anfügen, nachdem die Geheimnisse verfügbar sind und die Verschlüsselungsdetails auf dem Betriebssystemdatenträger festgelegt wurden, finden Sie unter [Anfügen eines Datenträgers an einen virtuellen Windows-Computer mithilfe von PowerShell](../virtual-machines/windows/attach-disk-ps.md).

   * **Verwaltete, verschlüsselte VMs ohne Azure AD (BEK und KEK):** Wenn für verwaltete, verschlüsselte VMs ohne Azure AD (mit BEK und KEK verschlüsselt) **keyVault/key/secret der Quelle nicht verfügbar ist**, stellen Sie den Schlüssel und die Geheimnisse in Key Vault wieder her. Gehen Sie dazu wie unter [Wiederherstellen eines nicht verschlüsselten virtuellen Computers aus einem Azure Backup-Wiederherstellungspunkt](backup-azure-restore-key-secret.md) beschrieben vor. Führen Sie dann die folgenden Skripts aus, um Verschlüsselungsdetails für den wiederhergestellte Betriebssystemdatenträger festzulegen (dieser Schritt ist für Datenträger nicht erforderlich). Die $dekurl und die $kekurl können Sie aus dem wiederhergestellten keyVault abrufen.

   Das folgende Skript muss nur ausgeführt werden, wenn das keyVault/key/secret der Quelle nicht verfügbar ist.

   ```powershell
     $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
     $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
     $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
     $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
     $diskupdateconfig = Set-AzDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
     $diskupdateconfig = Set-AzDiskUpdateKeyEncryptionKey -DiskUpdate $diskupdateconfig -KeyUrl $kekUrl -SourceVaultId $keyVaultId  
     Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    Informationen darüber, wie Sie die wiederhergestellten verwalteten Datenträger anfügen, nachdem die Schlüssel/Geheimnisse verfügbar sind und die Verschlüsselungsdetails auf dem Betriebssystemdatenträger festgelegt wurden, finden Sie unter [Anfügen eines Datenträgers an einen virtuellen Windows-Computer mithilfe von PowerShell](../virtual-machines/windows/attach-disk-ps.md).

5. Legen Sie die Netzwerkeinstellungen fest.

    ```powershell
    $nicName="p1234"
    $pip = New-AzPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzVirtualNetwork
    $vnet = Get-AzVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. Erstellen Sie den virtuellen Computer.

    ```powershell  
    New-AzVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

7. Pushen Sie die ADE-Erweiterung.

   * **Für virtuelle Computer mit Azure AD:** Verwenden Sie den folgenden Befehl, um die Verschlüsselung für die Datenträger manuell zu aktivieren.  

     **Nur BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **BEK und KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **Für virtuelle Computer ohne Azure AD:** Verwenden Sie den folgenden Befehl, um die Verschlüsselung für die Datenträger manuell zu aktivieren.

     Wenn Sie während der Ausführung des Befehls aufgefordert werden, die AADClientID anzugeben, müssen Sie Azure PowerShell aktualisieren.

     **Nur BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **BEK und KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

## <a name="restore-files-from-an-azure-vm-backup"></a>Wiederherstellen von Dateien aus einer Sicherung von virtuellen Azure-Computern

Sie können nicht nur Datenträger, sondern auch einzelne Dateien aus einer Sicherung von virtuellen Azure-Computern wiederherstellen. Für die Funktionen zum Wiederherstellen von Dateien ist der Zugriff auf alle Dateien eines Wiederherstellungspunkts möglich. Verwalten Sie die Dateien über den Datei-Explorer, wie Sie dies für andere Dateien gewohnt sind.

Grundlegende Schritte zum Wiederherstellen einer Datei aus einer Sicherung von virtuellen Azure-Computern:

* Auswählen des virtuellen Computers
* Auswählen eines Wiederherstellungspunkts
* Bereitstellen der Datenträger des Wiederherstellungspunkts
* Kopieren der erforderlichen Dateien
* Aufheben der Bereitstellung des Datenträgers

### <a name="select-the-vm"></a>Auswählen des virtuellen Computers

Zum Abrufen des PowerShell-Objekts, das das richtige Sicherungselement identifiziert, starten Sie vom Container im Tresor aus, und durchlaufen Sie die Objekthierarchie nach unten. Verwenden Sie das Cmdlet [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer), und leiten Sie dessen Ausgabe an das Cmdlet [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) weiter, um den Container auszuwählen, der den virtuellen Computer darstellt.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Auswählen eines Wiederherstellungspunkts

Verwenden Sie das Cmdlet [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint), um alle Wiederherstellungspunkte für das Sicherungselement aufzulisten. Wählen Sie anschließend den zu wiederherstellenden Wiederherstellungspunkt. Wenn Sie nicht sicher sind, welchen Wiederherstellungspunkt Sie verwenden sollen, empfiehlt es sich, den letzten Punkt in der Liste auszuwählen, an dem sich RecoveryPointType = AppConsistent befand.

Im folgenden Skript ist die Variable **$rp** ein Array von Wiederherstellungspunkten für das ausgewählte Sicherungselement der letzten sieben Tage. Das Array wird in umgekehrter Reihenfolge sortiert. Der letzte Wiederherstellungspunkt liegt bei Index 0. Verwenden Sie die standardmäßige PowerShell-Arrayindizierung zum Auswählen des Wiederherstellungspunkts. Im Beispiel wählt „$rp[0]“ den letzten Wiederherstellungspunkt aus.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```powershell
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>Bereitstellen der Datenträger des Wiederherstellungspunkts

Verwenden Sie das Cmdlet [Get-AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript), um das Skript zum Einbinden aller Datenträger des Wiederherstellungspunkts abzurufen.

> [!NOTE]
> Die Datenträger werden als angefügte iSCSI-Datenträger auf dem Computer bereitgestellt, auf dem das Skript ausgeführt wird. Die Bereitstellung wird sofort durchgeführt, und es fallen keine Gebühren an.
>
>

```powershell
Get-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```powershell
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Führen Sie das Skript auf dem Computer aus, auf dem die Dateien wiederhergestellt werden sollen. Um das Skript auszuführen, müssen Sie das angegebene Kennwort eingeben. Nachdem die Datenträger angefügt wurden, können Sie mit dem Windows-Datei-Explorer die neuen Volumes und Dateien durchsuchen. Weitere Informationen finden Sie im Backup-Artikel [Wiederherstellen von Dateien aus einer Sicherung von virtuellen Azure-Computern](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Aufheben der Bereitstellung von Datenträgern

Nachdem die erforderlichen Dateien kopiert wurden, können Sie [Disable-AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) verwenden, um die Einbindung der Datenträger aufzuheben. Achten Sie darauf, dass die Aufhebung der Bereitstellung für die Datenträger durchgeführt wird, damit der Zugriff auf die Dateien des Wiederherstellungspunkts nicht mehr möglich ist.

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

## <a name="next-steps"></a>Nächste Schritte

Falls Sie PowerShell für die Arbeit mit Azure-Ressourcen vorziehen, lesen Sie den PowerShell-Artikel [Sicherungen für Windows Server bereitstellen und verwalten](backup-client-automation.md). Wenn Sie DPM-Sicherungen verwalten, finden Sie im Artikel [Bereitstellen und Verwalten der Sicherung für DPM](backup-dpm-automation.md) weitere Informationen.

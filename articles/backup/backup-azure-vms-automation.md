---
title: Sichern und Wiederherstellen von virtuellen Azure-Computern mit PowerShell
description: Beschreibt das Sichern und Wiederherstellen von virtuellen Azure-Computern mithilfe von Azure Backup und PowerShell
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: ded2bc8a71bf564e31f40ca9f0d6c8049188768b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95978368"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>Sichern und Wiederherstellen von virtuellen Azure-Computern mit PowerShell

Dieser Artikel erläutert das Sichern und Wiederherstellen eines virtuellen Azure-Computers in einem [Azure Backup](backup-overview.md)-Recovery Services-Tresor mit PowerShell-Cmdlets.

In diesem Artikel wird Folgendes behandelt:

> [!div class="checklist"]
>
> * Erstellen eines Recovery Services-Tresors und Einrichten des Tresorkontexts.
> * Definieren einer Sicherungsrichtlinie
> * Anwenden der Sicherungsrichtlinie zum Schützen mehrerer virtueller Computer
> * Auslösen eines bedarfsgesteuerten Sicherungsauftrags für die geschützten virtuellen Computer. Bevor Sie einen virtuellen Computer (Virtual Machine, VM) sichern (oder schützen) können, müssen Sie die [Voraussetzungen](backup-azure-arm-vms-prepare.md) schaffen, um Ihre Umgebung auf den Schutz Ihrer VMs vorzubereiten.

## <a name="before-you-start"></a>Vorbereitung

* [Lesen Sie weitere Informationen](backup-azure-recovery-services-vault-overview.md) zu Recovery Services-Tresoren.
* [Überprüfen Sie](backup-architecture.md#architecture-built-in-azure-vm-backup) die Architektur für die Azure-VM-Sicherung, [erfahren Sie mehr über](backup-azure-vms-introduction.md) den Sicherungsvorgang, und [überprüfen Sie](backup-support-matrix-iaas.md) die Unterstützung, Einschränkungen und Voraussetzungen.
* Sehen Sie sich die PowerShell-Objekthierarchie für Recovery Services an.

## <a name="recovery-services-object-hierarchy"></a>Recovery Services-Objekthierarchie

Die Objekthierarchie ist im folgenden Diagramm zusammengefasst.

![Recovery Services-Objekthierarchie](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

[Sehen Sie sich die](/powershell/module/az.recoveryservices/)Cmdlet-Referenz zu **Az.RecoveryServices** in der Azure-Bibliothek an.

## <a name="set-up-and-register"></a>Einrichten und Registrieren

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vorbereitung:

1. [Laden Sie die neueste Version von PowerShell herunter.](/powershell/azure/install-az-ps)

2. Ermitteln Sie die Azure-PowerShell-Cmdlets zur Datensicherung mithilfe des folgenden Befehls:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

    Die Aliase und Cmdlets für Azure Backup, Azure Site Recovery und den Recovery Services-Tresor werden angezeigt. Die folgende Abbildung ist ein Beispiel für die angezeigten Elemente. Dies ist nicht die vollständige Liste von Cmdlets.

    ![Recovery Services-Liste](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Melden Sie sich mithilfe von **Connect-AzAccount** bei Ihrem Azure-Konto an. Dieses Cmdlet ruft eine Webseite auf, die Sie zur Eingabe Ihrer Kontoanmeldeinformationen auffordert:

    * Alternativ können Sie Ihre Kontoanmeldeinformationen als Parameter im Cmdlet **Connect-AzAccount** mit dem Parameter **-Credential** angeben.
    * Wenn Sie als CSP-Partner für einen Mandanten tätig sind, geben Sie den Kunden mit dessen Mandanten-ID oder primären Mandantendomänennamen als Mandanten an. Beispiel: **Connect-AzAccount -Tenant „fabrikam.com“**

4. Da ein Konto mehrere Abonnements enthalten kann, müssen Sie das Abonnement, das Sie verwenden möchten, dem Konto zuordnen:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Falls Sie Azure Backup zum ersten Mal verwenden, müssen Sie den Azure Recovery Service-Anbieter mithilfe des Cmdlets **[Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider)** bei Ihrem Abonnement registrieren.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Mithilfe der folgenden Befehle können Sie überprüfen, ob die Anbieter erfolgreich registriert wurden:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

    In der Befehlsausgabe sollte sich der **RegistrationState**-Wert in **Registriert** ändern. Ist dies nicht der Fall, führen Sie einfach das Cmdlet **[Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider)** erneut aus.

## <a name="create-a-recovery-services-vault"></a>Erstellen eines Recovery Services-Tresors

Mit den folgenden Schritten können Sie einen Recovery Services-Tresor erstellen. Ein Recovery Services-Tresor unterscheidet sich von einem Sicherungstresor.

1. Der Recovery Services-Tresor ist eine Resource Manager-Ressource. Deshalb müssen Sie ihn in eine Ressourcengruppe einfügen. Sie können eine vorhandene Ressourcengruppe verwenden oder mit dem Cmdlet **[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)** eine Ressourcengruppe erstellen. Wenn Sie eine Ressourcengruppe erstellen, geben Sie den Namen und den Speicherort für die Ressourcengruppe an.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Erstellen Sie mithilfe des Cmdlets [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) den Recovery Services-Tresor. Stellen Sie sicher, dass Sie den gleichen Speicherort für den Tresor angeben, der für die Ressourcengruppe verwendet wurde.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Geben Sie den Typ der zu verwendenden Speicherredundanz an. Sie können [lokal redundanten Speicher (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage), [georedundanten Speicher (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage) oder [zonenredundanten Speicher (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) verwenden. Das folgende Beispiel zeigt, dass für die Option **-BackupStorageRedundancy** für *testVault* der Wert auf **GeoRedundant** festgelegt wurde.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperty  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Viele Azure Backup-Cmdlets benötigen das Recovery Services-Tresorobjekt als Eingabe. Aus diesem Grund sollte das Backup Recovery Services-Tresorobjekt in einer Variablen gespeichert werden.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Anzeigen von Tresoren in einem Abonnement

Verwenden Sie [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault), um alle Tresore des Abonnements anzuzeigen:

```powershell
Get-AzRecoveryServicesVault
```

Die Ausgabe ähnelt dem folgenden Beispiel. Beachten Sie, dass die zugeordneten Elemente „ResourceGroupName“ und „Location“ angegeben sind.

```output
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

Verwenden Sie vor dem Aktivieren des Schutzes auf einem virtuellen Computer [Set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext), um den Tresorkontext festzulegen. Sobald der Tresorkontext festgelegt ist, gilt er für alle nachfolgenden-Cmdlets. Im folgenden Beispiel wird der Tresorkontext für den Tresor *testvault* festgelegt.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "Contoso-docs-rg" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Abrufen der Tresor-ID

Es ist geplant, die Tresorkontexteinstellung gemäß den Azure PowerShell-Richtlinien als veraltet zu kennzeichnen. Stattdessen können Sie die Tresor-ID speichern oder abrufen und an relevante Befehle übergeben. Wenn Sie also den Tresorkontext nicht festgelegt haben oder den Befehl so angeben möchten, dass er für einen bestimmten Tresor ausgeführt wird, übergeben Sie die Tresor-ID wie folgt als „-vaultID“ an alle relevanten Befehle:

```powershell
$targetVault = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault"
$targetVault.ID
```

oder

```powershell
$targetVaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

### <a name="modifying-storage-replication-settings"></a>Ändern der Einstellungen für die Speicherreplikation

Verwenden Sie den Befehl [Set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty), um die Konfiguration für Speicherreplikation des Tresors auf „LRS/GRS“ festzulegen.

```powershell
Set-AzRecoveryServicesBackupProperty -Vault $targetVault -BackupStorageRedundancy GeoRedundant/LocallyRedundant
```

> [!NOTE]
> Die Speicherredundanz kann nur geändert werden, wenn im Tresor keine Sicherungselemente vorhanden sind.

### <a name="create-a-protection-policy"></a>Erstellen einer Schutzrichtlinie

Wenn Sie einen Recovery Services-Tresor erstellen, enthält er automatisch standardmäßige Schutz- und Aufbewahrungsrichtlinien. Die standardmäßige Schutzrichtlinie löst täglich zu einem angegebenen Zeitpunkt einen Sicherungsauftrag aus. Die standardmäßige Aufbewahrungsrichtlinie bewahrt den täglichen Wiederherstellungspunkt für 30 Tage auf. Mit der Standardrichtlinie können Sie Ihren virtuellen Computer schnell schützen und die Richtlinie später mit anderen Details bearbeiten.

Verwenden Sie **[Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)** , um die im Tresor verfügbaren Schutzrichtlinien anzuzeigen. Sie können dieses Cmdlet zum Abrufen einer bestimmten Richtlinie verwenden oder um Richtlinien anzuzeigen, die einem bestimmten Workloadtyp zugeordnet sind. Im folgenden Beispiel werden Richtlinien für den Workloadtyp „AzureVM“ abgerufen.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> Die Zeitzone des Feldes „BackupTime“ in PowerShell ist UTC. Wenn jedoch der Zeitpunkt der Sicherung im Azure-Portal angezeigt wird, wird die Zeit auf Ihre lokale Zeitzone eingestellt.
>
>

Eine Sicherungsschutzrichtlinie ist mindestens einer Aufbewahrungsrichtlinie zugeordnet. Eine Aufbewahrungsrichtlinie definiert, wie lange ein Wiederherstellungspunkt gespeichert werden soll, bevor er gelöscht wird.

* Verwenden Sie [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject), um die standardmäßige Aufbewahrungsrichtlinie anzuzeigen.
* Auf ähnliche Weise können Sie [Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) verwenden, um die standardmäßige Zeitplanrichtlinie abzurufen.
* Das Cmdlet [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) erstellt ein PowerShell-Objekt, das Informationen zu Sicherungsrichtlinien enthält.
* Die Zeitplan- und Aufbewahrungsrichtlinienobjekte werden als Eingaben für das Cmdlet New-AzRecoveryServicesBackupProtectionPolicy verwendet.

Eine Startzeit wird standardmäßig im Schedule Policy Object (Zeitplanrichtlinienobjekt) definiert. Verwenden Sie das folgende Beispiel, um die Startzeit in die gewünschte Startzeit zu ändern. Die gewünschte Startzeit sollte ebenfalls in UTC angegeben werden. Beim folgenden Beispiel wird vorausgesetzt, dass die gewünschte Startzeit für tägliche Sicherungen 01:00 Uhr UTC ist.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Sie können die Startzeit nur in 30-Minuten-Einheiten angeben. Im vorstehenden Beispiel kann sie nur „01:00:00“ oder „02:30:00“ lauten. „01:15:00“ kann nicht als Startzeit angegeben werden.

Im folgenden Beispiel werden die Zeitplanrichtlinie und die Aufbewahrungsrichtlinie in Variablen gespeichert. Im Beispiel werden diese Variablen verwendet, um die Parameter beim Erstellen einer Schutzrichtlinie,*NewPolicy*, zu definieren.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultId $targetVault.ID
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Schutz aktivieren

Nachdem Sie die Schutzrichtlinie definiert haben, müssen Sie noch die Richtlinie für ein Element aktivieren. Verwenden Sie [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection), um den Schutz zu aktivieren. Zum Aktivieren des Schutzes sind zwei Objekte erforderlich – das Element und die Richtlinie. Sobald die Richtlinie mit dem Tresor verknüpft ist, wird der Sicherungsworkflow zu dem im Richtlinienzeitplan definierten Zeitpunkt gestartet.

> [!IMPORTANT]
> Wenn Sie die Sicherung mithilfe von PowerShell für mehrere virtuelle Computer gleichzeitig aktivieren, dürfen pro Richtlinie maximal 100 virtuelle Computer zugeordnet sein. Dies wird als [bewährte Methode](./backup-azure-vm-backup-faq.md#is-there-a-limit-on-number-of-vms-that-can-beassociated-with-the-same-backup-policy) empfohlen. Zurzeit blockiert der PowerShell-Client die Aktivierung bei mehr als 100 virtuellen Computern nicht explizit. Diese Überprüfung soll aber in Zukunft hinzugefügt werden.

Im folgenden Beispiel wird der Schutz für das Element „V2VM“ mithilfe der Richtlinie „NewPolicy“ aktiviert. Die Beispiele unterscheiden sich in Abhängigkeit davon, ob der virtuelle Computer verschlüsselt ist und welche Art von Verschlüsselung verwendet wird.

Gehen Sie wie folgt vor, um den Schutz für **nicht verschlüsselte Resource Manager-VMs** zu aktivieren:

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Um den Schutz auf verschlüsselten VMs (verschlüsselt mit BEK und KEK) zu aktivieren, müssen Sie dem Azure Backup-Dienst die Berechtigung zum Lesen von Schlüsseln und Geheimnissen aus dem Schlüsseltresor zuweisen.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Um den Schutz auf **verschlüsselten VMs (nur mit BEK verschlüsselt)** zu aktivieren, müssen Sie dem Azure Backup-Dienst die Berechtigung zum Lesen von Geheimnissen aus dem Schlüsseltresor zuweisen.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

> [!NOTE]
> Wenn Sie die Azure Government-Cloud verwenden, geben Sie im Cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) für den Parameter **ServicePrincipalName** den Wert `ff281ffe-705c-4f53-9f37-a40e6f2c68f3` an.
>

## <a name="monitoring-a-backup-job"></a>Überwachen eines Sicherungsauftrags

Sie können Vorgänge mit langer Ausführungsdauer, z.B. Sicherungsaufträge, ohne das Azure-Portal überwachen. Mit dem Cmdlet [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) können Sie den Status eines Auftrags abrufen, der sich in Bearbeitung befindet. Dieses Cmdlet ruft die Sicherungsaufträge für einen bestimmten Tresor ab. Dieser Tresor ist im Tresorkontext angegeben. Im folgenden Beispiel wird der Status eines laufenden Auftrags als Array abgerufen und in der Variable „$joblist“ gespeichert.

```powershell
$joblist = Get-AzRecoveryservicesBackupJob –Status "InProgress" -VaultId $targetVault.ID
$joblist[0]
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Anstatt die Fertigstellung dieser Aufträge abzufragen (und dafür unnötigen zusätzlichen Code zu schreiben), verwenden Sie das Cmdlet [Wait-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob). Dieses Cmdlet hält die Ausführung an, bis entweder der Auftrag abgeschlossen oder der angegebene Timeoutwert erreicht ist.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200 -VaultId $targetVault.ID
```

## <a name="manage-azure-vm-backups"></a>Verwalten von Azure-VM-Sicherungen

### <a name="modify-a-protection-policy"></a>Ändern einer Schutzrichtlinie

Verwenden Sie [Set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) zum Bearbeiten der Schutzrichtlinie, um die Objekte SchedulePolicy oder RetentionPolicy zu ändern.

#### <a name="modifying-scheduled-time"></a>Ändern der geplanten Zeit

Wenn Sie eine Schutzrichtlinie erstellen, wird ihr standardmäßig eine Startzeit zugewiesen. In den folgenden Beispielen wird gezeigt, wie Sie die Startzeit einer Schutzrichtlinie ändern.

````powershell
$SchPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z" (This is the time that you want to start the backup)
$UtcTime = $UtcTime.ToUniversalTime()
$SchPol.ScheduleRunTimes[0] = $UtcTime
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -SchedulePolicy $SchPol -VaultId $targetVault.ID
````

#### <a name="modifying-retention"></a>Ändern der Aufbewahrungsdauer

Im folgenden Beispiel wird der Aufbewahrungswert von Wiederherstellungspunkten in 365 Tage geändert.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol -VaultId $targetVault.ID
```

#### <a name="configuring-instant-restore-snapshot-retention"></a>Konfigurieren der Aufbewahrungsdauer von Momentaufnahmen für sofortige Wiederherstellung

> [!NOTE]
> Ab Azure PowerShell, Version 1.6.0, können Sie die Aufbewahrungsdauer von Momentaufnahmen für sofortige Wiederherstellung in Richtlinien mithilfe von PowerShell aktualisieren.

````powershell
$bkpPol = Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
$bkpPol.SnapshotRetentionInDays=7
Set-AzRecoveryServicesBackupProtectionPolicy -policy $bkpPol -VaultId $targetVault.ID
````

Dazu wird der Standardwert „2“ verwendet. Sie können für den Wert als Minimum „1“ und als Maximum „5“ festlegen. Bei Richtlinien zu wöchentlichen Sicherungen wird der Zeitraum auf „5“ festgelegt und kann nicht geändert werden.

#### <a name="creating-azure-backup-resource-group-during-snapshot-retention"></a>Erstellen einer Azure Backup-Ressourcengruppe während der Aufbewahrung von Momentaufnahmen

> [!NOTE]
> Ab Azure PowerShell, Version 3.7.0, können Sie die Ressourcengruppe erstellen und bearbeiten, die für das Speichern von Sofortmomentaufnahmen angelegt wird.

Ausführlichere Informationen zu den Regeln der Erstellung von Ressourcengruppen und andere relevante Details finden Sie in der Dokumentation unter [Azure Backup-Ressourcengruppe für virtuelle Computer](./backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines).

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -name "DefaultPolicyForVMs"
$bkpPol.AzureBackupRGName="Contosto_"
$bkpPol.AzureBackupRGNameSuffix="ForVMs"
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

### <a name="trigger-a-backup"></a>Auslösen einer Sicherung

Verwenden Sie [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem), um einen Sicherungsauftrag auszulösen. Wenn es die erste Sicherung ist, handelt es sich um eine vollständige Sicherung. Nachfolgende Sicherungen erstellen inkrementelle Kopien. Im folgenden Beispiel wird eine VM-Sicherung 60 Tage lang aufbewahrt:

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
$job = Backup-AzRecoveryServicesBackupItem -Item $item -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> Die Zeitzone der Felder „StartTime“ und „EndTime“ in PowerShell ist UTC. Wenn jedoch der Zeitpunkt im Azure-Portal angezeigt wird, wird die Zeit auf Ihre lokale Zeitzone eingestellt.
>
>

### <a name="change-policy-for-backup-items"></a>Ändern der Richtlinie für Sicherungselemente

Sie können entweder die vorhandene Richtlinie bearbeiten oder die Richtlinie des gesicherten Elements von „Richtlinie1“ in „Richtlinie2“ ändern. Zum Wechseln der Richtlinien für ein gesichertes Element rufen Sie die entsprechende Richtlinie und das Sicherungselement ab, und verwenden Sie den Befehl [Enable-AzRecoveryServices](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) mit dem Sicherungselement als Parameter.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName> -VaultId $targetVault.ID
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType AzureVM -BackupManagementType AzureVM -Name "<BackupItemName>" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1 -VaultId $targetVault.ID
````

Der Befehl wartet, bis die Sicherungskonfiguration abgeschlossen ist, und gibt die folgende Ausgabe zurück:

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
TestVM           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>Schutz beenden

#### <a name="retain-data"></a>Beibehalten von Daten

Wenn Sie den Schutz verhindern möchten, können Sie dafür das PowerShell-Cmdlet [Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) verwenden. Dadurch werden die geplanten Sicherungen beendet, doch werden die bis zu diesem Zeitpunkt gesicherten Daten dauerhaft beibehalten.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Löschen von Sicherungsdaten

Wenn Sie die gespeicherten Sicherungsdaten im Tresor vollständig entfernen möchten, fügen Sie dem [Befehl „disable protection“](#retain-data) das Flag/den Switch „-RemoveRecoveryPoints“ hinzu.

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

## <a name="restore-an-azure-vm"></a>Wiederherstellen eines virtuellen Azure-Computers

Es gibt einen wichtigen Unterschied zwischen dem Wiederherstellen eines virtuellen Computers über das Azure-Portal und dem Wiederherstellen eines virtuellen Computers mithilfe von PowerShell. Mit PowerShell ist der Wiederherstellungsvorgang mit der Erstellung der Datenträger und den Konfigurationsinformationen aus dem Wiederherstellungspunkt abgeschlossen. Beim Wiederherstellungsvorgang wird kein virtueller Computer erstellt. Informationen zum Erstellen eines virtuellen Computers aus einem Datenträger finden Sie im Abschnitt [Erstellen eines virtuellen Computers aus wiederhergestellten Datenträgern](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Wenn Sie nicht den gesamten virtuellen Computer wiederherstellen möchten, sondern nur einige Dateien aus einer Azure-VM-Sicherung, helfen Ihnen die Informationen im [Abschnitt zur Dateiwiederherstellung](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup) weiter.

> [!Tip]
> Beim Wiederherstellungsvorgang wird kein virtueller Computer erstellt.
>
>

Die folgende Grafik zeigt die Objekthierarchie von „RecoveryServicesVault“ bis „BackupRecoveryPoint“.

![Die Recovery Services-Objekthierarchie zeigt die BackupContainer an](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Identifizieren Sie das gesicherte Element und den Wiederherstellungspunkt, der die Zeitpunktdaten enthält, um die gesicherten Daten wiederherstellen zu können. Verwenden Sie [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem), um Daten aus dem Tresor in Ihrem Konto wiederherzustellen.

Zum Wiederherstellen eines virtuellen Azure-Computers müssen Sie folgende grundlegende Schritte ausführen:

* Wählen Sie den virtuellen Computer aus.
* Wählen Sie einen Wiederherstellungspunkt aus.
* Stellen Sie die Datenträger wieder her.
* Erstellen Sie den virtuellen Computer über die gespeicherten Datenträger.

### <a name="select-the-vm-when-restoring-files"></a>Auswählen des virtuellen Computers (beim Wiederherstellen von Dateien)

Zum Abrufen des PowerShell-Objekts, das das richtige Sicherungselement identifiziert, starten Sie vom Container im Tresor aus, und durchlaufen Sie die Objekthierarchie nach unten. Verwenden Sie das Cmdlet [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer), und leiten Sie dessen Ausgabe an das Cmdlet [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) weiter, um den Container auszuwählen, der den virtuellen Computer darstellt.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point-when-restoring-files"></a>Auswählen eines Wiederherstellungspunkts (beim Wiederherstellen von Dateien)

Verwenden Sie das Cmdlet [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint), um alle Wiederherstellungspunkte für das Sicherungselement aufzulisten. Wählen Sie anschließend den zu wiederherstellenden Wiederherstellungspunkt. Wenn Sie nicht genau wissen, welchen Wiederherstellungspunkt Sie verwenden sollen, empfiehlt es sich, den letzten „RecoveryPointType = AppConsistent“-Punkt in der Liste auszuwählen.

Im folgenden Skript ist die Variable **$rp** ein Array von Wiederherstellungspunkten für das ausgewählte Sicherungselement der letzten sieben Tage. Das Array wird in umgekehrter Reihenfolge sortiert. Der letzte Wiederherstellungspunkt liegt bei Index 0. Verwenden Sie die standardmäßige PowerShell-Arrayindizierung zum Auswählen des Wiederherstellungspunkts. Im Beispiel wählt „$rp[0]“ den letzten Wiederherstellungspunkt aus.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```output
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

Verwenden Sie das Cmdlet [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem), um Daten und die Konfiguration eines Sicherungselements auf einem Wiederherstellungspunkt wiederherzustellen. Nachdem Sie einen Wiederherstellungspunkt ermittelt haben, können Sie ihn als Wert für den **-RecoveryPoint**-Parameter verwenden. Im vorstehenden Beispiel wurde **$rp[0]** als Wiederherstellungspunkt verwendet. Im folgenden Beispielcode ist **$rp[0]** der Wiederherstellungspunkt, der zum Wiederherstellen des Datenträgers verwendet wird.

So stellen Sie den Datenträger und die Konfigurationsinformationen wieder her:

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -VaultId $targetVault.ID
$restorejob
```

#### <a name="restore-managed-disks"></a>Wiederherstellen von verwalteten Datenträgern

> [!NOTE]
> Wenn die gesicherte VM über verwaltete Datenträger verfügt und Sie diese als verwaltete Datenträger wiederherstellen möchten, können Sie die Funktion aus dem Azure PowerShell-RM-Modul Version 6.7.0 oder höher nutzen.
>
>

Geben Sie den zusätzlichen Parameter **TargetResourceGroupName** an, um die RG festzulegen, in der die verwalteten Datenträger gespeichert werden.

> [!IMPORTANT]
> Es wird dringend empfohlen, den Parameter **TargetResourceGroupName** für die Wiederherstellung von verwalteten Datenträgern zu verwenden, da dies zu erheblichen Leistungssteigerungen führt. Wenn dieser Parameter nicht angegeben wird, können Sie nicht von der Funktionalität zur sofortigen Wiederherstellung profitieren, und der Wiederherstellungsvorgang verläuft im Vergleich langsamer. Wenn Sie verwaltete Datenträger als nicht verwaltete Datenträger wiederherstellen möchten, geben Sie diesen Parameter nicht an, sondern verdeutlichen Sie Ihre Absicht durch Angabe des Parameters `-RestoreAsUnmanagedDisks`. Der Parameter `-RestoreAsUnmanagedDisks` steht ab Azure PowerShell 3.7.0 zur Verfügung. In zukünftigen Versionen ist die Angabe eines dieser Parameter für die richtige Wiederherstellung obligatorisch.
>
>

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID
```

Die Datei **VMConfig.JSON** wird für das Speicherkonto wiederhergestellt, und die verwalteten Datenträger werden in der angegebenen Zielressourcengruppe wiederhergestellt.

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```output
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Verwenden Sie das Cmdlet [Wait-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob), um auf den Abschluss des Wiederherstellungsauftrags zu warten.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Nachdem der Wiederherstellungsauftrag abgeschlossen ist, verwenden Sie das Cmdlet [Get-AzRecoveryServicesBackupJobDetails](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob), um die Details des Wiederherstellungsvorgangs abzurufen. Die JobDetails-Eigenschaft enthält die Informationen, die zum erneuten Erstellen des virtuellen Computers benötigt werden.

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob -VaultId $targetVault.ID
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob -VaultId $targetVault.ID
```

Nachdem Sie die Datenträger wiederhergestellt haben, fahren Sie mit dem nächsten Abschnitt fort, um den virtuellen Computer zu erstellen.

## <a name="replace-disks-in-azure-vm"></a>Ersetzen der Datenträger in Azure-VM

Wenn Sie die Datenträger und Konfigurationsinformationen ersetzen möchten, führen Sie die folgenden Schritte aus:

* Schritt 1: [Wiederherstellen der Datenträger](backup-azure-vms-automation.md#restore-the-disks)
* Schritt 2: [Trennen des Datenträgers mit PowerShell](../virtual-machines/windows/detach-disk.md#detach-a-data-disk-using-powershell)
* Schritt 3: [Anfügen eines Datenträgers an eine Windows-VM mit PowerShell](../virtual-machines/windows/attach-disk-ps.md)

## <a name="create-a-vm-from-restored-disks"></a>Erstellen eines virtuellen Computers aus wiederhergestellten Datenträgern

Nachdem Sie die Datenträger wiederhergestellt haben, können Sie die folgenden Schritte zum Erstellen und Konfigurieren des virtuellen Computers vom Datenträger verwenden.

> [!NOTE]
>
> 1. Es ist AzureAz-Modul 3.0.0 erforderlich. <br>
> 2. Zum Erstellen von verschlüsselten virtuellen Computern aus wiederhergestellten Datenträgern muss Ihre Azure-Rolle über die Berechtigung zum Ausführen der Aktion **Microsoft.KeyVault/vaults/deploy/action** verfügen. Wenn Ihre Rolle über diese Berechtigung nicht verfügt, erstellen Sie mit dieser Aktion eine benutzerdefinierte Rolle. Weitere Informationen finden Sie unter [Benutzerdefinierte Azure-Rollen](../role-based-access-control/custom-roles.md). <br>
> 3. Nach der Wiederherstellung von Datenträgern können Sie jetzt eine Bereitstellungsvorlage abrufen, die Sie direkt verwenden können, um einen neuen virtuellen Computer zu erstellen. Sie benötigen zum Erstellen von verwalteten/nicht verwalteten virtuellen Computern, die verschlüsselt/nicht verschlüsselt sind, keine unterschiedlichen PowerShell-Cmdlets.<br>
> <br>

### <a name="create-a-vm-using-the-deployment-template"></a>Erstellen eines virtuellen Computers mithilfe der Bereitstellungsvorlage

Aus den resultierenden Auftragsdetails ergibt sich der Vorlagen-URI, der abgefragt und bereitgestellt werden kann.

```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $templateBlobURI = $properties["Template Blob Uri"]
```

Auf die Vorlage kann nicht direkt zugegriffen werden, da sie sich unter dem Speicherkonto eines Kunden und dem angegebenen Container befindet. Wir benötigen die gesamte URL sowie ein temporäres SAS-Token, um auf diese Vorlage zugreifen zu können.

1. Extrahieren Sie zuerst den Vorlagennamen aus templateBlobURI. Das Format wird unten angegeben. Sie können mithilfe des Split-Vorgangs in PowerShell den endgültigen Vorlagennamen aus dieser URL extrahieren.

    ```http
    https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
    ```

2. Anschließend kann die vollständige URL wie [hier](../azure-resource-manager/templates/secure-template-with-sas-token.md?tabs=azure-powershell#provide-sas-token-during-deployment) erläutert generiert werden.

    ```powershell
    Set-AzCurrentStorageAccount -Name $storageAccountName -ResourceGroupName <StorageAccount RG name>
    $templateBlobFullURI = New-AzStorageBlobSASToken -Container $containerName -Blob <templateName> -Permission r -FullUri
    ```

3. Stellen Sie die Vorlage bereit, um einen neuen virtuellen Computer wie [hier](../azure-resource-manager/templates/deploy-powershell.md) beschrieben zu erstellen.

    ```powershell
    New-AzResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobFullURI -storageAccountType Standard_GRS
    ```

### <a name="create-a-vm-using-the-config-file"></a>Erstellen eines virtuellen Computers mit der Konfigurationsdatei

Im folgenden Abschnitt werden die erforderlichen Schritte zum Erstellen eines virtuellen Computers mithilfe der „VMConfig“-Datei aufgelistet.

> [!NOTE]
> Es wird dringend empfohlen, die oben beschriebene Bereitstellungsvorlage zum Erstellen eines virtuellen Computers zu verwenden. Dieser Abschnitt (Punkte 1 bis 6) wird in Kürze veraltet sein.

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

    * **Nicht verwaltete, verschlüsselte VMs ohne Azure AD (nur BEK):** Wenn für nicht verwaltete, verschlüsselte VMs ohne Azure AD (nur mit BEK verschlüsselt) das **keyVault/secret der Quelle nicht verfügbar ist**, stellen Sie die Geheimnisse in Key Vault wieder her. Gehen Sie dazu wie unter [Wiederherstellen eines nicht verschlüsselten virtuellen Computers aus einem Azure Backup-Wiederherstellungspunkt](backup-azure-restore-key-secret.md) beschrieben vor. Führen Sie dann die folgenden Skripts zum Festlegen von Verschlüsselungsdetails für das wiederhergestellte Betriebssystemblob aus (dieser Schritt ist bei einem Datenblob nicht erforderlich). Die $dekurl können Sie aus dem wiederhergestellten keyVault abrufen.

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

    Wenn die **Geheimnisse verfügbar sind** und die Verschlüsselungsdetails für das Betriebssystemblob ebenfalls festgelegt wurden, fügen Sie die Datenträger mit dem unten angegebenen Skript an.

    Wenn die keyVault/secrets der Quelle bereits verfügbar sind, muss das vorstehende Skript nicht ausgeführt werden.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Nicht verwaltete, verschlüsselte VMs ohne Azure AD (BEK und KEK):** Wenn für nicht verwaltete, verschlüsselte VMs ohne Azure AD (mit BEK und KEK verschlüsselt) **keyVault/key/secret der Quelle nicht verfügbar ist**, stellen Sie den Schlüssel und die Geheimnisse in Key Vault wieder her. Gehen Sie dazu wie unter [Wiederherstellen eines nicht verschlüsselten virtuellen Computers aus einem Azure Backup-Wiederherstellungspunkt](backup-azure-restore-key-secret.md) beschrieben vor. Führen Sie dann die folgenden Skripts zum Festlegen von Verschlüsselungsdetails für das wiederhergestellte Betriebssystemblob aus (dieser Schritt ist bei einem Datenblob nicht erforderlich). Die $dekurl und die $kekurl können Sie aus dem wiederhergestellten keyVault abrufen.

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

    Wenn die keyVault/key/secrets der Quelle verfügbar sind, muss das vorstehende Skript nicht ausgeführt werden.

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

    * **Verwaltete, verschlüsselte VMs ohne Azure AD (nur BEK):** Wenn für verwaltete, verschlüsselte VMs ohne Azure AD (nur mit BEK verschlüsselt) das **keyVault/secret der Quelle nicht verfügbar ist**, stellen Sie die Geheimnisse in Key Vault wieder her. Gehen Sie dazu wie unter [Wiederherstellen eines nicht verschlüsselten virtuellen Computers aus einem Azure Backup-Wiederherstellungspunkt](backup-azure-restore-key-secret.md) beschrieben vor. Führen Sie dann die folgenden Skripts zum Festlegen von Verschlüsselungsdetails für den wiederhergestellten Betriebssystemdatenträger aus (dieser Schritt ist bei einem Datenträger nicht erforderlich). Die $dekurl können Sie aus dem wiederhergestellten keyVault abrufen.

    Das folgende Skript muss nur ausgeführt werden, wenn das keyVault/secret der Quelle nicht verfügbar ist.  

    ```powershell
    $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
    $encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
    $encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
    Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    Informationen darüber, wie Sie die wiederhergestellten verwalteten Datenträger anfügen, nachdem die Geheimnisse verfügbar sind und die Verschlüsselungsdetails auf dem Betriebssystemdatenträger festgelegt wurden, finden Sie unter [Anfügen eines Datenträgers an einen virtuellen Windows-Computer mithilfe von PowerShell](../virtual-machines/windows/attach-disk-ps.md).

    * **Verwaltete, verschlüsselte VMs ohne Azure AD (BEK und KEK):** Wenn für verwaltete, verschlüsselte VMs ohne Azure AD (mit BEK und KEK verschlüsselt) **keyVault/key/secret der Quelle nicht verfügbar ist**, stellen Sie den Schlüssel und die Geheimnisse in Key Vault wieder her. Gehen Sie dazu wie unter [Wiederherstellen eines nicht verschlüsselten virtuellen Computers aus einem Azure Backup-Wiederherstellungspunkt](backup-azure-restore-key-secret.md) beschrieben vor. Führen Sie dann die folgenden Skripts zum Festlegen von Verschlüsselungsdetails für den wiederhergestellten Betriebssystemdatenträger aus (dieser Schritt ist bei Datenträgern nicht erforderlich). Die $dekurl und die $kekurl können Sie aus dem wiederhergestellten keyVault abrufen.

    Das folgende Skript muss nur ausgeführt werden, wenn das keyVault/key/secret der Quelle nicht verfügbar ist.

    ```powershell
    $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
    $encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
    $encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
    $encryptionSettingsElement.KeyEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndKeyReference
    $encryptionSettingsElement.KeyEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.KeyEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.KeyEncryptionKey.KeyUrl = $kekUrl
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
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
   Wenn die ADE-Erweiterungen nicht gepusht werden, werden die Datenträger als nicht verschlüsselt gekennzeichnet. Deshalb müssen die folgenden Schritte unbedingt ausgeführt werden:

   * **Für virtuelle Computer mit Azure AD:** Verwenden Sie den folgenden Befehl, um die Verschlüsselung für die Datenträger manuell zu aktivieren.  

     **Nur BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **BEK und KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **Für virtuelle Computer ohne Azure AD:** Verwenden Sie den folgenden Befehl, um die Verschlüsselung für die Datenträger manuell zu aktivieren.

     Wenn Sie während der Ausführung des Befehls aufgefordert werden, die AADClientID anzugeben, müssen Sie Azure PowerShell aktualisieren.

     **Nur BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **BEK und KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

> [!NOTE]
> Vergessen Sie nicht, die im Rahmen des Vorgangs „verschlüsselte VM aus wiederhergestellten Datenträgern“ erstellten JSON-Dateien manuell zu löschen.

## <a name="restore-files-from-an-azure-vm-backup"></a>Wiederherstellen von Dateien aus einer Sicherung von virtuellen Azure-Computern

Sie können nicht nur Datenträger, sondern auch einzelne Dateien aus einer Sicherung von virtuellen Azure-Computern wiederherstellen. Für die Funktionen zum Wiederherstellen von Dateien ist der Zugriff auf alle Dateien eines Wiederherstellungspunkts möglich. Verwalten Sie die Dateien über den Datei-Explorer, wie Sie dies für andere Dateien gewohnt sind.

Grundlegende Schritte zum Wiederherstellen einer Datei aus einer Sicherung von virtuellen Azure-Computern:

* Auswählen des virtuellen Computers
* Auswählen eines Wiederherstellungspunkts
* Bereitstellen der Datenträger des Wiederherstellungspunkts
* Kopieren der erforderlichen Dateien
* Aufheben der Bereitstellung des Datenträgers

### <a name="select-the-vm-when-restoring-the-vm"></a>Auswählen des virtuellen Computers (bei seiner Wiederherstellung)

Zum Abrufen des PowerShell-Objekts, das das richtige Sicherungselement identifiziert, starten Sie vom Container im Tresor aus, und durchlaufen Sie die Objekthierarchie nach unten. Verwenden Sie das Cmdlet [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer), und leiten Sie dessen Ausgabe an das Cmdlet [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) weiter, um den Container auszuwählen, der den virtuellen Computer darstellt.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point-when-restoring-the-vm"></a>Auswählen eines Wiederherstellungspunkts (beim Wiederherstellen des virtuellen Computers)

Verwenden Sie das Cmdlet [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint), um alle Wiederherstellungspunkte für das Sicherungselement aufzulisten. Wählen Sie anschließend den zu wiederherstellenden Wiederherstellungspunkt. Wenn Sie nicht genau wissen, welchen Wiederherstellungspunkt Sie verwenden sollen, empfiehlt es sich, den letzten „RecoveryPointType = AppConsistent“-Punkt in der Liste auszuwählen.

Im folgenden Skript ist die Variable **$rp** ein Array von Wiederherstellungspunkten für das ausgewählte Sicherungselement der letzten sieben Tage. Das Array wird in umgekehrter Reihenfolge sortiert. Der letzte Wiederherstellungspunkt liegt bei Index 0. Verwenden Sie die standardmäßige PowerShell-Arrayindizierung zum Auswählen des Wiederherstellungspunkts. Im Beispiel wählt „$rp[0]“ den letzten Wiederherstellungspunkt aus.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```output
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

Verwenden Sie das Cmdlet [Get-AzRecoveryServicesBackupRPMountScript](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript), um das Skript zum Einbinden aller Datenträger des Wiederherstellungspunkts abzurufen.

> [!NOTE]
> Die Datenträger werden als angefügte iSCSI-Datenträger auf dem Computer bereitgestellt, auf dem das Skript ausgeführt wird. Die Bereitstellung wird sofort durchgeführt, und es fallen keine Gebühren an.
>
>

```powershell
Get-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```output
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Führen Sie das Skript auf dem Computer aus, auf dem die Dateien wiederhergestellt werden sollen. Um das Skript auszuführen, müssen Sie das angegebene Kennwort eingeben. Nachdem die Datenträger angefügt wurden, können Sie mit dem Windows-Datei-Explorer die neuen Volumes und Dateien durchsuchen. Weitere Informationen finden Sie im Backup-Artikel [Wiederherstellen von Dateien aus einer Sicherung von virtuellen Azure-Computern](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Aufheben der Bereitstellung von Datenträgern

Nachdem die erforderlichen Dateien kopiert wurden, können Sie [Disable-AzRecoveryServicesBackupRPMountScript](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) verwenden, um die Einbindung der Datenträger aufzuheben. Achten Sie darauf, dass die Aufhebung der Bereitstellung für die Datenträger durchgeführt wird, damit der Zugriff auf die Dateien des Wiederherstellungspunkts nicht mehr möglich ist.

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

## <a name="next-steps"></a>Nächste Schritte

Falls Sie PowerShell für die Arbeit mit Azure-Ressourcen vorziehen, lesen Sie den PowerShell-Artikel [Sicherungen für Windows Server bereitstellen und verwalten](backup-client-automation.md). Wenn Sie DPM-Sicherungen verwalten, finden Sie im Artikel [Bereitstellen und Verwalten der Sicherung für DPM](backup-dpm-automation.md) weitere Informationen.

---
title: Vorläufiges Löschen für Azure Backup
description: In diesem Artikel wird erläutert, wie Sie mit den Azure Backup-Sicherheitsfunktionen für mehr Sicherheit für Ihre Sicherungen sorgen können.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: da473b1d886ec2fe95a7baae76b09aff38fb3cd7
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2020
ms.locfileid: "95254048"
---
# <a name="soft-delete-for-azure-backup"></a>Vorläufiges Löschen für Azure Backup

Die Sorgen bezüglich Sicherheitsproblemen wie Schadsoftware, Ransomware und Eindringlingen werden immer größer. Diese Sicherheitsprobleme können erhebliche Daten- und finanzielle Verluste mit sich bringen. Zum Schutz gegen solche Angriffe verfügt Azure Backup jetzt über Sicherheitsfeatures für den Schutz von Sicherungsdaten auch nach dem Löschen.

Eines dieser Features ist das vorläufige Löschen. Beim vorläufigen Löschen werden die Sicherungsdaten 14 Tage länger aufbewahrt, damit das jeweilige Sicherungselement auch dann ohne Datenverluste wiederhergestellt werden kann, wenn ein böswilliger Akteur eine Sicherung löscht oder die Sicherungsdaten versehentlich gelöscht werden. Für die zusätzlichen 14 Tage der Aufbewahrung von Sicherungsdaten mit dem Status „Vorläufiges Löschen“ fallen für Sie keine Kosten an.

Der Schutz durch das vorläufige Löschen ist für folgende Dienste verfügbar:

- [Vorläufiges Löschen für virtuelle Azure-Computer](soft-delete-virtual-machines.md)
- [Vorläufiges Löschen für SQL-Server auf virtuellen Azure-Computern und SAP HANA in Azure-VM-Workloads](soft-delete-sql-saphana-in-azure-vm.md)

Im folgenden Flussdiagramm sind die unterschiedlichen Schritte und Zustände eines Sicherungselements bei aktiviertem vorläufigem Löschen dargestellt:

![Lebenszyklus eines vorläufig gelöschten Sicherungselements](./media/backup-azure-security-feature-cloud/lifecycle.png)

## <a name="enabling-and-disabling-soft-delete"></a>Aktivieren und Deaktivieren des vorläufigen Löschens

Vorläufiges Löschen ist für neu erstellte Tresore standardmäßig aktiviert, um Sicherungsdaten vor versehentlichen oder vorsätzlichen Löschvorgängen zu schützen.  Es wird davon abgeraten, dieses Feature zu deaktivieren. Sie sollten das vorläufige Löschen nur dann deaktivieren, wenn Sie planen, Ihre geschützten Elemente in einen neuen Tresor zu verschieben, und nicht die 14 Tage warten können, die für das Löschen und erneute Schützen erforderlich sind (z. B. in einer Testumgebung). Nur der Tresorbesitzer kann dieses Feature deaktivieren. Wenn Sie dieses Feature deaktivieren, führen alle künftigen Löschvorgänge geschützter Elemente zu einer sofortigen Entfernung, ohne dass eine Wiederherstellung möglich ist. Sicherungsdaten, die vor der Deaktivierung dieses Features mit dem Status „Vorläufig gelöscht“ vorhanden waren, behalten 14 Tage diesen Status. Wenn Sie diese Elemente umgehend endgültig löschen möchten, müssen Sie sie wiederherstellen und anschließend erneut löschen, damit sie endgültig gelöscht werden.

Beachten Sie, dass die Deaktivierung des Features vorläufiges Löschen für alle Arten von Workloads gilt. So ist es beispielsweise nicht mehr möglich, vorläufiges Löschen nur für SQL Server- oder SAP HANA-Datenbanken zu deaktivieren, es gleichzeitig aber für virtuelle Computer in demselben Tresor aktiviert zu lassen. Für eine präzisere Steuerung können Sie separate Tresore erstellen.

### <a name="disabling-soft-delete-using-azure-portal"></a>Deaktivieren des vorläufigen Löschens für VMs über das Azure-Portal

Gehen Sie zum Deaktivieren des vorläufigen Löschens wie folgt vor:

1. Wechseln Sie im Azure-Portal zu Ihrem Tresor und dann zu **Einstellungen** -> **Eigenschaften**.
2. Wählen Sie im Eigenschaftenbereich **Sicherheitseinstellungen** -> **Aktualisieren** aus.  
3. Wählen Sie im Bereich mit den Sicherheitseinstellungen unter **Vorläufiges Löschen** die Option **Deaktivieren** aus.

![Deaktivieren des vorläufigen Löschens](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Deaktivieren des vorläufigen Löschens für VMs über Azure PowerShell

> [!IMPORTANT]
> Die erforderliche Mindestversion von Az.RecoveryServices für die Verwendung des vorläufigen Löschens mit Azure PowerShell ist 2.2.0. Verwenden Sie ```Install-Module -Name Az.RecoveryServices -Force```, um die aktuelle Version herunterzuladen.

Verwenden Sie zum Deaktivieren das PowerShell-Cmdlet [Set-AzRecoveryServicesVaultBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty).

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>Deaktivieren des vorläufigen Löschens über die REST-API

Um die Funktion des vorläufigen Löschens mithilfe der REST-API zu deaktivieren, führen Sie die [hier](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api) erläuterten Schritte aus.

## <a name="permanently-deleting-soft-deleted-backup-items"></a>Endgültiges Löschen vorläufig gelöschter Sicherungselemente

Sicherungsdaten, die vor der Deaktivierung dieses Features vorläufig gelöscht wurden, verbleiben im vorläufigen Löschzustand. Wenn Sie diese Elemente umgehend endgültig löschen möchten, müssen Sie sie wiederherstellen und anschließend erneut löschen, damit sie endgültig gelöscht werden.

### <a name="using-azure-portal"></a>Verwenden des Azure-Portals

Folgen Sie diesen Schritten:

1. Führen Sie die Schritte zum [Deaktivieren des vorläufigen Löschens](#enabling-and-disabling-soft-delete) aus.

2. Navigieren Sie im Azure-Portal unter Ihrem Tresor zu **Sicherungselemente**, und wählen Sie das vorläufig gelöschte Element aus.

   ![Auswählen des vorläufig gelöschten Elements](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Wählen Sie die Option **Wiederherstellen** aus.

   ![Auswählen von „Wiederherstellen“](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. Daraufhin wird ein Fenster angezeigt. Wählen Sie **Wiederherstellen** aus.

   ![Auswählen von „Wiederherstellen“](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Wählen Sie **Sicherungsdaten löschen** aus, um die Sicherungsdaten endgültig zu löschen.

   ![Auswählen von „Sicherungsdaten löschen“](./media/backup-azure-manage-vms/delete-backup-button.png)

6. Geben Sie den Namen des Sicherungselements ein, um zu bestätigen, dass Sie die Wiederherstellungspunkte löschen möchten.

   ![Eingeben des Namens des Sicherungselements](./media/backup-azure-manage-vms/delete-backup-data.png)

7. Wählen Sie **Löschen** aus, um die Sicherungsdaten des Elements zu löschen. Sie erhalten einer Benachrichtigung, dass die Sicherungsdaten gelöscht wurden.

### <a name="using-azure-powershell"></a>Verwenden von Azure PowerShell

Wenn Elemente vor der Deaktivierung des vorläufigen Löschens gelöscht wurden, befinden sie sich im vorläufig gelöschten Zustand. Um diese sofort zu löschen, muss der Löschvorgang umgekehrt und dann erneut ausgeführt werden.

Identifizieren Sie die Elemente, die sich im vorläufig gelöschten Zustand befinden.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Kehren Sie dann den Löschvorgang um, der bei aktiviertem vorläufigem Löschen ausgeführt wurde.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

Da das vorläufige Löschen jetzt deaktiviert ist, führt der Löschvorgang zu einem sofortigen Entfernen der Sicherungsdaten.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>Verwenden der REST-API

Wenn Elemente vor der Deaktivierung des vorläufigen Löschens gelöscht wurden, befinden sie sich im vorläufig gelöschten Zustand. Um diese sofort zu löschen, muss der Löschvorgang umgekehrt und dann erneut ausgeführt werden.

1. Machen Sie zunächst die Löschvorgänge mit den [hier](backup-azure-arm-userestapi-backupazurevms.md#undo-the-deletion) erläuterten Schritten rückgängig.
2. Deaktivieren Sie dann die Funktion des vorläufigen Löschens mithilfe der REST-API, indem Sie die [hier](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api) erläuterten Schritte ausführen.
3. Danach löschen Sie die Sicherungen mithilfe der REST-API, wie [hier](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data) erläutert.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Muss ich die Funktion für vorläufiges Löschen in jedem Tresor aktivieren?

Nein. Sie ist integriert und wird standardmäßig für alle Recovery Services-Tresore aktiviert.

### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-the-delete-operation-is-complete"></a>Kann ich konfigurieren, wie lange meine Daten nach Abschluss des Löschvorgangs im Status „Vorläufig gelöscht“ aufbewahrt werden?

Nein. Der Zeitraum ist auf eine um 14 Tage verlängerte Aufbewahrung nach dem Löschvorgang festgelegt.

### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Muss ich die Kosten für diesen zusätzlichen Aufbewahrungszeitraum von 14 Tagen bezahlen?

Nein. Dieser zusätzliche Aufbewahrungszeitraum von 14 Tagen ist im Rahmen der Funktion „Vorläufiges Löschen“ kostenlos.

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Kann ich einen Wiederherstellungsvorgang durchführen, wenn sich meine Daten im Status „Vorläufig gelöscht“ befinden?

Nein. Sie müssen das Löschen der vorläufig gelöschten Ressource rückgängig machen, um sie wiederherzustellen. Mit dem Vorgang „Wiederherstellen“ wird die Ressource zurück in den Status **Beendigung des Schutzes mit Beibehaltung der Daten** versetzt, und Sie können beliebige Wiederherstellungspunkte wiederherstellen. Der Garbage Collector bleibt in diesem Status angehalten.

### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Gilt für meine Momentaufnahmen der gleiche Lebenszyklus wie für meine Wiederherstellungspunkte im Tresor?

Ja.

### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Wie kann ich die geplanten Sicherungen für eine vorläufig gelöschte Ressource erneut auslösen?

Wenn Sie nach dem Wiederherstellen den Vorgang „Fortsetzen“ ausführen, ist die Ressource wieder geschützt. Beim Vorgang „Fortsetzen“ wird eine Sicherungsrichtlinie zugeordnet, um die geplanten Sicherungen mit dem ausgewählten Aufbewahrungszeitraum auszulösen. Darüber hinaus wird der Garbage Collector ausgeführt, sobald der Vorgang „Fortsetzen“ abgeschlossen ist. Falls Sie eine Wiederherstellung für einen Wiederherstellungspunkt durchführen möchten, für den das Ablaufdatum überschritten wurde, sollten Sie dies erledigen, bevor Sie den Wiederherstellungsvorgang auslösen.

### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Kann ich meinen Tresor löschen, wenn er vorläufig gelöschte Elemente enthält?

Der Recovery Services-Tresor kann nicht gelöscht werden, wenn sich darin Sicherungselemente im Zustand „Vorläufig gelöscht“ befinden. Die vorläufig gelöschten Elemente werden 14 Tage nach dem Löschvorgang endgültig gelöscht. Wenn Sie nicht so lange warten möchten, können Sie [vorläufiges Löschen deaktivieren](#enabling-and-disabling-soft-delete), die vorläufig gelöschten Elemente wiederherstellen und sie erneut löschen, um sie endgültig zu löschen. Nachdem Sie sich vergewissert haben, dass keine geschützten oder vorläufig gelöschten Elemente mehr vorhanden sind, kann der Tresor gelöscht werden.  

### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Kann ich die Daten löschen, bevor die 14 Tage im Status „Vorläufig gelöscht“ abgelaufen sind?

Nein. Das Löschen von vorläufig gelöschten Elementen kann nicht erzwungen werden. Sie werden nach 14 Tagen automatisch gelöscht. Diese Sicherheitsfunktion ist aktiviert, um die gesicherten Daten vor versehentlichen oder böswilligen Löschvorgängen zu schützen.  Sie sollten 14 Tage warten, bevor Sie für das Element eine andere Aktion ausführen.  Vorläufig gelöschte Elemente werden nicht in Rechnung gestellt.  Wenn Sie die zum vorläufigen Löschen gekennzeichneten Elemente innerhalb von 14 Tagen in einem neuen Tresor erneut schützen müssen, wenden Sie sich an den Microsoft-Support.

### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>Kann das vorläufige Löschen in PowerShell oder per CLI durchgeführt werden?

Vorläufige Löschvorgänge können mit PowerShell ausgeführt werden. Die Befehlszeilenschnittstelle wird derzeit nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Sicherheitsfeatures in Azure Backup](security-overview.md)

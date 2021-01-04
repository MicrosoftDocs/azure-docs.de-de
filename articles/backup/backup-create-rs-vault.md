---
title: Erstellen und Konfigurieren von Recovery Services-Tresoren
description: In diesem Artikel erfahren Sie, wie Sie Recovery Services-Tresore zum Speichern von Sicherungen und Wiederherstellungspunkten erstellen und konfigurieren. Erfahren Sie, wie Sie die regionsübergreifende Wiederherstellung zur Wiederherstellung in einer sekundären Region verwenden.
ms.topic: conceptual
ms.date: 05/30/2019
ms.custom: references_regions
ms.openlocfilehash: ef3d464c051f15d656a4a60937bb05ac496ee52d
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548425"
---
# <a name="create-and-configure-a-recovery-services-vault"></a>Erstellen und Konfigurieren von Recovery Services-Tresoren

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy"></a>Festlegen der Speicherredundanz

Azure Backup übernimmt automatisch die Speicherung für den Tresor. Sie müssen angeben, wie dieser Speicher repliziert wird.

> [!NOTE]
> Bevor Sicherungen im Tresor konfiguriert werden, muss der **Speicherreplikationstyp** (lokal redundant/georedundant) für einen Recovery Services-Tresor geändert werden. Sobald Sie eine Sicherung konfigurieren, wird die Änderungsoption deaktiviert.
>
>- Wenn Sie noch keine Sicherung konfiguriert haben, [führen Sie die folgenden Schritte aus](#set-storage-redundancy), um die Einstellungen zu überprüfen und zu ändern.
>- Wenn Sie die Sicherung bereits konfiguriert haben und von georedundantem Speicher (GRS) zu lokal redundantem Speicher (LRS) wechseln müssen, [überprüfen Sie diese Problemumgehungen](#how-to-change-from-grs-to-lrs-after-configuring-backup).

1. Wählen Sie im Bereich **Recovery Services-Tresore** den neuen Tresor aus. Wählen Sie im Abschnitt **Einstellungen** die Option **Eigenschaften** aus.
1. Wählen Sie in **Eigenschaften** unter **Sicherungskonfiguration** die Option **Aktualisieren** aus.

1. Wählen Sie den Speicherreplikationstyp und dann **Speichern** aus.

     ![Speicherkonfiguration für neuen Tresor festlegen](./media/backup-create-rs-vault/recovery-services-vault-backup-configuration.png)

   - Wir empfehlen, dass Sie, wenn Sie Azure als primären Endpunkt für den Sicherungsspeicher verwenden, weiterhin die Standardeinstellung **Georedundant** verwenden.
   - Wenn Sie Azure nicht als primären Speicherendpunkt für die Sicherung verwenden, wählen Sie **Lokal redundant** aus. Dadurch verringern sich die Kosten für Azure-Speicher.
   - Erfahren Sie mehr über [Georedundanz](../storage/common/storage-redundancy.md#geo-redundant-storage) and [lokale Redundanz](../storage/common/storage-redundancy.md#locally-redundant-storage).
   - Wenn Sie in einer Region Datenverfügbarkeit ohne Ausfallzeiten benötigen und Data Residency gewährleistet sein muss, wählen Sie [zonenredundanten Speicher](../storage/common/storage-redundancy.md#zone-redundant-storage) aus.

>[!NOTE]
>Die Speicherreplikationseinstellungen für den Tresor sind für das Sichern von Azure-Dateifreigaben nicht relevant, da die aktuelle Lösung auf Momentaufnahmen basiert und keine Daten in den Tresor übertragen werden. Momentaufnahmen werden im gleichen Speicherkonto gespeichert wie die gesicherte Dateifreigabe.

## <a name="set-cross-region-restore"></a>Festlegen der bereichsübergreifenden Wiederherstellung

Die Wiederherstellungsoption **Regionsübergreifende Wiederherstellung** ermöglicht Ihnen, Daten in einer sekundären [gekoppelten Azure-Region](../best-practices-availability-paired-regions.md) wiederherzustellen.

Sie unterstützt die folgenden Datenquellen:

- Virtuelle Azure-Computer
- Auf Azure-VMs gehostete SQL-Datenbanken
- Auf Azure-VMs gehostete SAP HANA-Datenbanken

Die regionsübergreifende Wiederherstellung bietet folgende Möglichkeiten:

- Durchführen von Übungen für Audit- oder Complianceanforderungen
- Wiederherstellen der Daten bei einer Katastrophe in der primären Region

Beim Wiederherstellen einer VM können Sie die VM oder den zugehörigen Datenträger wiederherstellen. Wenn Sie die Wiederherstellung von SQL-/SAP HANA-Datenbanken auf Azure-VMs durchführen, können Sie die Datenbanken oder deren Dateien wiederherstellen.

Wählen Sie zur Auswahl dieses Features im Bereich **Sicherungskonfiguration** die Option **Bereichsübergreifende Wiederherstellung aktivieren** aus.

Da dieser Prozess auf Speicherebene erfolgt, hat er [Auswirkungen auf den Preis](https://azure.microsoft.com/pricing/details/backup/).

>[!NOTE]
>Vorbereitungen
>
>- Machen Sie sich anhand der [Unterstützungsmatrix](backup-support-matrix.md#cross-region-restore) mit der Liste unterstützter verwalteter Typen und Regionen vertraut.
>- Das Feature der bereichsübergreifenden Wiederherstellung (CRR) ist nun in allen öffentlichen Azure-Regionen in der Vorschau verfügbar.
>- CRR ist ein optionales Feature auf Tresorebene für beliebige GRS-Tresore und standardmäßig deaktiviert.
>- Nach der Aktivierung kann es bis zu 48 Stunden dauern, bis die Sicherungselemente in sekundären Regionen verfügbar sind.
>- CRR für Azure-VMs wird zurzeit nur für Azure Resource Manager-VMs in Azure unterstützt. Klassische Azure-VMs werden nicht unterstützt.  Wenn CRR von weiteren Verwaltungstypen unterstützt wird, werden diese **automatisch** registriert.
>- Die bereichsübergreifende Wiederherstellung kann zurzeit nicht auf GRS oder LRS zurückgesetzt werden, sobald der Schutz zum ersten Mal initiiert wurde.

### <a name="configure-cross-region-restore"></a>Konfigurieren der bereichsübergreifenden Wiederherstellung

Bei einem mit GRS-Redundanz erstellten Tresor kann die bereichsübergreifende Wiederherstellung konfiguriert werden. Jeder GRS-Tresor verfügt über ein Banner, das mit der Dokumentation verknüpft ist. Wenn Sie CRR für den Tresor konfigurieren möchten, navigieren Sie zum Bereich mit der Sicherungskonfiguration. Dort befindet sich die Option zum Aktivieren dieses Features.

 ![Banner für die Sicherungskonfiguration](./media/backup-azure-arm-restore-vms/banner.png)

1. Navigieren Sie im Portal zu „Recovery Services-Tresor“ > „Einstellungen“ > „Eigenschaften“.
2. Wählen Sie unter **Bereichsübergreifende Wiederherstellung in diesem Tresor aktivieren** aus, um die Funktionalität zu aktivieren.

   ![Aktivieren der regionsübergreifenden Wiederherstellung](./media/backup-azure-arm-restore-vms/backup-configuration.png)

Weitere Informationen zum Sichern und Wiederherstellen mit CRR finden Sie in diesen Artikeln:

- [Regionsübergreifende Wiederherstellung für Azure-VMs](backup-azure-arm-restore-vms.md#cross-region-restore)
- [Regionsübergreifende Wiederherstellung für SQL-Datenbanken](restore-sql-database-azure-vm.md#cross-region-restore)
- [Regionsübergreifende Wiederherstellung für SAP HANA-Datenbanken](sap-hana-db-restore.md#cross-region-restore)

## <a name="set-encryption-settings"></a>Festlegen von Verschlüsselungseinstellungen

Standardmäßig werden die Daten im Recovery Services-Tresor mit plattformseitig verwalteten Schlüsseln verschlüsselt. Sie müssen von Ihrer Seite aus keine expliziten Maßnahmen ergreifen, um diese Verschlüsselung zu aktivieren, und sie gilt für alle Workloads, die in Ihrem Recovery Services-Tresor gesichert werden.  Sie können die Sicherungsdaten in diesem Tresor mit einem eigenen Schlüssel verschlüsseln. Dieser Schlüssel wird als kundenseitig verwalteter Schlüssel bezeichnet. Wenn Sie Sicherungsdaten mithilfe Ihres eigenen Schlüssels verschlüsseln möchten, muss der Verschlüsselungsschlüssel angegeben werden, bevor ein Element in diesem Tresor geschützt wird. Wenn Sie die Verschlüsselung mit Ihrem Schlüssel aktiviert haben, kann sie nicht mehr rückgängig gemacht werden.

### <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Konfigurieren eines Tresors mithilfe von kundenseitig verwalteten Schlüsseln

Zum Konfigurieren des Tresors für die Verschlüsselung mit kundenseitig verwalteten Schlüsseln müssen Sie diese Schritte in dieser Reihenfolge befolgen:

1. Aktivieren der verwalteten Identität für den Recovery Services-Tresor

1. Zuweisen von Berechtigungen zum Tresor für den Zugriff auf den Verschlüsselungsschlüssel im Azure Key Vault

1. Aktivieren des vorläufigen Löschens und des Löschschutzes im Azure Key Vault

1. Zuweisen des Verschlüsselungsschlüssels zum Recovery Services-Tresor

Die Anweisungen für die einzelnen Schritte finden Sie [in diesem Artikel](encryption-at-rest-with-cmk.md#configuring-a-vault-to-encrypt-using-customer-managed-keys).

## <a name="modifying-default-settings"></a>Ändern der Standardeinstellungen

Es wird dringend empfohlen, vor dem Konfigurieren von Sicherungen im Tresor die Standardeinstellungen für **Speicherreplikationstyp** und **Sicherheitseinstellungen** zu überprüfen.

- Der **Speicherreplikationstyp** ist standardmäßig auf **Georedundant** (GRS) festgelegt. Nachdem Sie die Sicherung konfiguriert haben, ist die Option zum Ändern deaktiviert.
  - Wenn Sie noch keine Sicherung konfiguriert haben, [führen Sie die folgenden Schritte aus](#set-storage-redundancy), um die Einstellungen zu überprüfen und zu ändern.
  - Wenn Sie die Sicherung bereits konfiguriert haben und von georedundantem Speicher (GRS) zu lokal redundantem Speicher (LRS) wechseln müssen, [überprüfen Sie diese Problemumgehungen](#how-to-change-from-grs-to-lrs-after-configuring-backup).

- **Vorläufiges Löschen** ist für neu erstellte Tresore standardmäßig auf **Aktiviert** festgelegt, um das versehentliche oder bösartige Löschen von Sicherungsdaten zu verhindern. [Führen Sie diese Schritte aus](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete), um die Einstellungen zu überprüfen und zu ändern.

### <a name="how-to-change-from-grs-to-lrs-after-configuring-backup"></a>Wechseln von GRS zu LRS nach dem Konfigurieren einer Sicherung

Bevor Sie sich für den Wechsel von GRS zu LRS (lokal redundanter Speicher) entscheiden, wägen Sie Kosten und höhere Dauerhaftigkeit der Daten gegeneinander ab und entscheiden Sie, was für Ihr Szenario am besten geeignet ist. Wenn Sie von GRS zu LRS wechseln müssen, haben Sie zwei Möglichkeiten. Die Möglichkeiten hängen von Ihren geschäftlichen Anforderungen an die Aufbewahrung der Sicherungsdaten ab:

- [Zuvor gesicherte Daten müssen nicht aufbewahrt werden](#dont-need-to-preserve-previous-backed-up-data)
- [Zuvor gesicherte Daten müssen aufbewahrt werden](#must-preserve-previous-backed-up-data)

#### <a name="dont-need-to-preserve-previous-backed-up-data"></a>Zuvor gesicherte Daten müssen nicht aufbewahrt werden

Zum Schutz von Workloads in einem neuen LRS-Tresor müssen der aktuelle Schutz und die Daten im GRS-Tresor gelöscht und die Sicherungen erneut konfiguriert werden.

>[!WARNING]
>Der folgende Vorgang ist destruktiv und kann nicht rückgängig gemacht werden. Alle Sicherungsdaten und Sicherungselemente, die dem geschützten Server zugeordnet sind, werden dauerhaft gelöscht. Gehen Sie daher mit Bedacht vor.

Beenden und Löschen des aktuellen Schutzes für den GRS-Tresor:

1. Deaktivieren Sie in den Eigenschaften des GRS-Tresors das vorläufige Löschen. Führen Sie [die folgenden Schritte](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) aus, um das vorläufige Löschen zu deaktivieren.

1. Beenden Sie den Schutz, und löschen Sie die Sicherungen aus dem vorhandenen GRS-Tresor. Wählen Sie im Dashbordmenü des Tresors die Option **Sicherungselemente** aus. Die hier aufgelisteten Elemente, die in den LRS-Tresor verschoben werden müssen, müssen zusammen mit den zugehörigen Sicherungsdaten entfernt werden. Weitere Informationen finden Sie unter [Löschen von geschützten Elementen in der Cloud](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) und [Löschen von geschützten lokalen Elementen](backup-azure-delete-vault.md#delete-protected-items-on-premises).

1. Wenn Sie planen, Azure-Dateifreigaben (AFS), SQL-Server oder SAP HANA-Server zu verschieben, müssen Sie auch deren Registrierung aufheben. Wählen Sie im Dashbordmenü des Tresors die Option **Sicherungsinfrastruktur** aus. Weitere Informationen finden Sie unter [Aufheben der Registrierung von SQL-Servern](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance), [Aufheben der Registrierung von Speicherkonten, die mit Azure-Dateifreigaben verknüpft sind](manage-afs-backup.md#unregister-a-storage-account) und [Aufheben der Registrierung einer SAP HANA-Instanz](sap-hana-db-manage.md#unregister-an-sap-hana-instance).

1. Nachdem Sie diese aus dem GRS-Tresor entfernt haben, fahren Sie mit dem Konfigurieren der Sicherungen für Ihre Workload im neuen LRS-Tresor fort.

#### <a name="must-preserve-previous-backed-up-data"></a>Zuvor gesicherte Daten müssen aufbewahrt werden

Wenn Sie die aktuell geschützten Daten im GRS-Tresor aufbewahren und den Schutz in einem neuen LRS-Tresor fortsetzen müssen, gibt es für einige der Workloads nur eingeschränkte Optionen:

- Bei MARS können Sie [Beendigung des Schutzes mit Beibehaltung der Daten](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) auswählen und den Agent im neuen LRS-Tresor registrieren.

  - Der Azure Backup-Dienst behält weiterhin alle vorhandenen Wiederherstellungspunkte des GRS-Tresors bei.
  - Die Beihaltung der Wiederherstellungspunkte im GRS-Tresor ist kostenpflichtig.
  - Für die Wiederherstellung gesicherter Daten können Sie nur nicht abgelaufene Wiederherstellungspunkte im GRS-Tresor auswählen.
  - Im LRS-Tresor muss ein neues Erstreplikat der Daten erstellt werden.

- Bei einem virtuellen Azure-Computer können Sie [Beendigung des Schutzes mit Beibehaltung der Daten](backup-azure-manage-vms.md#stop-protecting-a-vm) für den virtuellen Computer im GRS-Tresor auswählen, den virtuellen Computer in eine andere Ressourcengruppe verschieben und dann den virtuellen Computer im LRS-Tresor schützen. Weitere Informationen zum Verschieben eines virtuellen Computers in eine andere Ressourcengruppe finden Sie unter [Anleitungen und Einschränkungen](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md).

  Ein virtueller Computer kann jeweils nur in einem Tresor geschützt werden. Der virtuelle Computer in der neuen Ressourcengruppe kann aber im LRS-Tresor geschützt werden, da er als anderer virtueller Computer betrachtet wird.

  - Der Azure Backup-Dienst behält die Wiederherstellungspunkte bei, die im GRS-Tresor gesichert wurden.
  - Die Beibehaltung der Wiederherstellungspunkte im GRS-Tresor ist kostenpflichtig (Einzelheiten finden Sie unter [Azure Backup – Preise](azure-backup-pricing.md)).
  - Sie können den virtuellen Computer bei Bedarf aus dem GRS-Tresor wiederherstellen.
  - Die erste Sicherung des virtuellen Computers in der neuen Ressourcengruppe im LRS-Tresor erfolgt als Erstreplikat.

## <a name="next-steps"></a>Nächste Schritte

[Übersicht über Recovery Services-Tresore](backup-azure-recovery-services-vault-overview.md)
Informieren Sie sich über das [Löschen von Recovery Services-Tresoren](backup-azure-delete-vault.md).
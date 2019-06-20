---
title: Automatische, georedundante Azure SQL-Datenbank-Sicherungen | Microsoft-Dokumentation
description: SQL-Datenbank erstellt alle paar Minuten automatisch eine lokale Datenbanksicherung und verwendet georedundanten Azure-Speicher mit Lesezugriff (Read-Access Geographically Redundant Storage, RA-GRS), um für Georedundanz zu sorgen.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 05/20/2019
ms.openlocfilehash: 1c81f5748d1e3edff4902eb462b9beea78acd8bc
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65951656"
---
# <a name="automated-backups"></a>Automatisierte Sicherungen

SQL-Datenbank erstellt automatisch die Datenbanksicherungen, für die eine Speicherdauer von mindestens 7 und maximal 35 Tagen gilt. Es wird [georedundanter Azure-Speicher mit Lesezugriff (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) verwendet, um sicherzustellen, dass diese auch dann beibehalten werden, wenn das Rechenzentrum nicht verfügbar ist. Diese Sicherungen werden automatisch und ohne zusätzliche Kosten erstellt. Sie müssen keinerlei Aktionen durchführen. Datenbanksicherungen sind ein wesentlicher Bestandteil jeder Strategie für Geschäftskontinuität und Notfallwiederherstellung, da Ihre Daten vor versehentlichen Beschädigungen und Löschungen geschützt werden. Falls es gemäß Ihren Sicherheitsregeln erforderlich ist, dass Ihre Sicherungen über einen längeren Zeitraum verfügbar sind (bis zu 10 Jahre), können Sie eine [Langzeitaufbewahrung](sql-database-long-term-retention.md) in Singleton-Datenbanken und Pools für elastische Datenbanken konfigurieren.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Was ist die Sicherung einer SQL-Datenbank-Instanz?

SQL-Datenbank nutzt SQL Server-Technologie, um wöchentlich [vollständige Sicherungen](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server), alle zwölf Stunden [differenzielle Sicherungen](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) und alle fünf bis zehn Minuten [Transaktionsprotokollsicherungen](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) zu erstellen. Die Sicherungen werden in [RA-GRS-Speicherblobs](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) gespeichert, die in einem [gekoppelten Rechenzentrum](../best-practices-availability-paired-regions.md) repliziert werden, um Schutz vor Rechenzentrumsausfällen zu bieten. Wenn Sie eine Datenbank wiederherstellen, ermittelt der Dienst, welche vollständigen und differenziellen Sicherungen bzw. Transaktionsprotokollsicherungen wiederhergestellt werden müssen.

Sie können diese Sicherungen für Folgendes verwenden:

- **Stellen Sie für eine vorhandene Datenbank den Stand zu einem vergangenen Zeitpunkt wieder her**, der innerhalb des Aufbewahrungszeitraums liegt, indem Sie das Azure-Portal, Azure PowerShell, die Azure CLI oder die REST-API verwenden. In Einzeldatenbanken und Pools für elastische Datenbanken wird mit diesem Vorgang eine neue Datenbank auf demselben Server erstellt, auf dem sich auch die ursprüngliche Datenbank befindet. Bei einer verwalteten Instanz kann mit diesem Vorgang eine Kopie der Datenbank in derselben oder einer anderen verwalteten Instanz unter demselben Abonnement erstellt werden.
  - **[Ändern Sie den Aufbewahrungszeitraum der Sicherung](#how-to-change-the-pitr-backup-retention-period)** (bis zu 35 Tage), um Ihre Sicherungsrichtlinie zu konfigurieren.
  - **Ändern Sie die Langzeitaufbewahrung in bis zu zehn Jahre** für Einzeldatenbanken und Pools für elastische Datenbanken, indem Sie [das Azure-Portal](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies) oder [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-configure-long-term-retention-policies-and-restore-backups) verwenden.
- **Stellen Sie für eine gelöschte Datenbank den Stand zum Zeitpunkt des Löschvorgangs wieder her**, oder den Stand zu einem beliebigen anderen Zeitpunkt innerhalb des Aufbewahrungszeitraums. Die gelöschte Datenbank kann nur auf demselben logischen Server oder der verwalteten Instanz wiederhergestellt werden, auf dem bzw. der die ursprüngliche Datenbank erstellt wurde.
- **Stellen Sie eine Datenbank in einer anderen geografischen Region wieder her**. Die Geowiederherstellung ermöglicht die Wiederherstellung nach dem Ausfall einer geografischen Region, wenn Sie keinen Zugriff auf Ihren Server und Ihre Datenbank haben. Dabei wird eine neue Datenbank auf einem beliebigen Server an einem beliebigen Ort der Welt erstellt.
- **Führen Sie die Wiederherstellung einer Datenbank aus einer spezifischen langfristigen Sicherung durch** (in einer Einzeldatenbank oder einem Pool für elastische Datenbanken), wenn die Datenbank mit einer Richtlinie zur Langzeitaufbewahrung (Long-Term Retention, LTR) konfiguriert wurde. Mit LTR können Sie eine alte Version der Datenbank wiederherstellen, indem Sie [das Azure-Portal](sql-database-long-term-backup-retention-configure.md#view-backups-and-restore-from-a-backup-using-azure-portal) oder [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-configure-long-term-retention-policies-and-restore-backups) verwenden, um eine Konformitätsanforderung zu erfüllen oder eine alte Version der Anwendung auszuführen. Weitere Informationen finden Sie unter [Langfristige Aufbewahrung](sql-database-long-term-retention.md).
- Informationen zum Durchführen einer Wiederherstellung finden Sie unter [Wiederherstellen einer Azure SQL-Datenbank mit automatisierten Datenbanksicherungen](sql-database-recovery-using-backups.md).

> [!NOTE]
> In Azure Storage bezieht sich der Begriff *Replikation* auf das Kopieren von Dateien von einem Speicherort zum anderen. Die *Datenbankreplikation* der SQL bezieht sich auf das Beibehalten von mehreren sekundären Datenbanken, die mit einer primären Datenbank synchronisiert werden.

Sie können einige dieser Vorgänge ausprobieren, indem Sie die folgenden Beispiele verwenden:

| | Das Azure-Portal | Azure PowerShell |
|---|---|---|
| Ändern der Sicherungsaufbewahrung | [Einzeldatenbank](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-the-azure-portal) <br/> [Verwaltete Instanz](sql-database-automated-backups.md#change-pitr-for-a-managed-instance) | [Einzeldatenbank](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[Verwaltete Instanz](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Ändern der Langzeitaufbewahrung von Sicherungen | [Einzeldatenbank](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Verwaltete Instanz: Nicht verfügbar  | [Einzeldatenbank](sql-database-long-term-backup-retention-configure.md#use-powershell-to-configure-long-term-retention-policies-and-restore-backups)<br/>Verwaltete Instanz: Nicht verfügbar  |
| Wiederherstellen des Stands einer Datenbank zu einem bestimmten Zeitpunkt | [Einzeldatenbank](sql-database-recovery-using-backups.md#point-in-time-restore) | [Einzeldatenbank](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Verwaltete Instanz](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Wiederherstellen einer gelöschten Datenbank | [Einzeldatenbank](sql-database-recovery-using-backups.md#deleted-database-restore-using-the-azure-portal) | [Einzeldatenbank](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Verwaltete Instanz](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Wiederherstellen der Datenbank aus Azure Blob Storage | Einzeldatenbank: Nicht verfügbar <br/>Verwaltete Instanz: Nicht verfügbar  | Einzeldatenbank: Nicht verfügbar <br/>[Verwaltete Instanz](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="how-long-are-backups-kept"></a>Wie lange werden Sicherungen aufbewahrt?

Der Aufbewahrungszeitraum der Sicherung beträgt für jede SQL-Datenbank-Instanz standardmäßig zwischen 7 und 35 Tagen, was von Kaufmodell und Dienstebene abhängt. Sie können den Aufbewahrungszeitraum der Sicherung für eine Datenbank auf dem SQL-Datenbankserver aktualisieren. Weitere Informationen finden Sie unter [Ändern des Zeitraums für die Aufbewahrung von Sicherungen](#how-to-change-the-pitr-backup-retention-period).

Wenn Sie eine Datenbank löschen, bewahrt SQL-Datenbank die Sicherungen auf die gleiche Weise wie für eine Onlinedatenbank auf. Beim Löschen einer Datenbank vom Typ „Basic“, für die eine Aufbewahrungsdauer von sieben Tagen gilt, wird eine vier Tage alte Sicherung weitere drei Tage lang aufbewahrt.

Falls Sie die Sicherungen länger als die maximale Aufbewahrungsdauer beibehalten müssen, können Sie die Sicherungseigenschaften so ändern, dass Ihrer Datenbank eine oder mehrere Zeiträume für die langfristige Aufbewahrung hinzugefügt werden. Weitere Informationen finden Sie unter [Langfristige Aufbewahrung](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Wenn Sie die Azure SQL Server-Instanz löschen, auf der die SQL-Datenbanken gehostet werden, werden alle Pools für elastische Datenbanken und Datenbanken, die zum Server gehören, ebenfalls gelöscht und können nicht wiederhergestellt werden. Es ist nicht möglich, einen gelöschten Server wiederherzustellen. Wenn Sie aber die langfristige Aufbewahrung konfiguriert haben, werden die Sicherungen für die Datenbanken mit LTR nicht gelöscht, und diese Datenbanken können wiederhergestellt werden.

### <a name="default-backup-retention-period"></a>Standardaufbewahrungsdauer für Sicherungen

#### <a name="dtu-based-purchasing-model"></a>DTU-basiertes Kaufmodell

Die Standardaufbewahrungsdauer für eine Datenbank, die mit dem DTU-basierten Kaufmodell erstellt wurde, hängt von der Dienstebene ab:

- Für den Basic-Tarif beträgt die Dauer **eine** Woche.
- Für den Standard-Tarif beträgt die Dauer **fünf** Wochen.
- Für den Premium-Tarif beträgt die Dauer **fünf** Wochen.

#### <a name="vcore-based-purchasing-model"></a>vCore-basiertes Kaufmodell

Bei Verwendung des [V-Kern-basierten Kaufmodells](sql-database-service-tiers-vcore.md) beträgt der Standardaufbewahrungszeitraum **sieben** Tage (für Einzeldatenbanken, Pooldatenbanken und Instanzdatenbanken). Sie können für alle Azure SQL-Datenbanken (Einzeldatenbanken, Pooldatenbanken und Instanzdatenbanken) den [Aufbewahrungszeitraum für Sicherungen in bis zu 35 Tage ändern](#how-to-change-the-pitr-backup-retention-period).

> [!WARNING]
> Wenn Sie die aktuelle Aufbewahrungsdauer reduzieren, sind alle vorhandenen Sicherungen, die außerhalb der neuen Aufbewahrungsdauer liegen, nicht mehr verfügbar. Wenn Sie die aktuelle Aufbewahrungsdauer erhöhen, behält SQL-Datenbank die vorhandenen Sicherungen bei, bis die längere Aufbewahrungsdauer erreicht ist.

## <a name="how-often-do-backups-happen"></a>Wie oft erfolgen Sicherungen?

### <a name="backups-for-point-in-time-restore"></a>Sicherungen für die Point-in-Time-Wiederherstellung

SQL-Datenbank unterstützt den Self-Service für die Point-in-Time-Wiederherstellung (Point-in-Time Restore, PITR), indem automatisch vollständige Sicherungen, differenzielle Sicherungen und Transaktionsprotokollsicherungen erstellt werden. Vollständige Datensicherungen werden wöchentlich erstellt. Differenzielle Sicherungen erfolgen im Allgemeinen alle 12 Stunden und Transaktionsprotokollsicherungen alle 5-10 Minuten, wobei die Häufigkeit auf der Computegröße und dem Umfang der Datenbankaktivität basiert. Die erste vollständige Sicherung wird unmittelbar nach der Datenbankerstellung geplant. Sie wird normalerweise innerhalb von 30 Minuten abgeschlossen, aber der Vorgang kann auch länger dauern, wenn es sich um eine sehr große Datenbank handelt. Die erste Sicherung kann bei einer wiederhergestellten Datenbank oder einer Datenbankkopie beispielsweise länger dauern. Nach der ersten vollständigen Sicherung werden alle weiteren Sicherungen automatisch geplant und im Hintergrund verwaltet. Die genaue Zeitplanung für alle Datenbanksicherungen wird vom SQL-Datenbank-Dienst je nach der gesamten Systemworkload bestimmt. Sie können die Sicherungsaufträge nicht ändern oder deaktivieren. 

Die PITR-Sicherungen sind georedundant und per [regionsübergreifender Replikation für Azure Storage](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) geschützt.

Weitere Informationen finden Sie unter [Point-in-Time-Wiederherstellung](sql-database-recovery-using-backups.md#point-in-time-restore).

### <a name="backups-for-long-term-retention"></a>Sicherungen für die langfristige Aufbewahrung

Einzel- und Pooldatenbanken verfügen über eine Option zum Konfigurieren der Langzeitaufbewahrung (LTR) von vollständigen Sicherungen für eine Dauer von bis zu zehn Jahren in Azure Blob Storage. Wenn die LTR-Richtlinie aktiviert ist, werden die wöchentlichen vollständigen Sicherungen automatisch in einen anderen RA-GRS-Speichercontainer kopiert. Zur Erfüllung unterschiedlicher Konformitätsanforderungen können Sie verschiedene Aufbewahrungsdauern für wöchentliche, monatliche oder jährliche Sicherungen auswählen. Der Speicherverbrauch hängt von der ausgewählten Häufigkeit der Sicherungen und von den Aufbewahrungsdauern ab. Sie können den [LTR-Preisrechner](https://azure.microsoft.com/pricing/calculator/?service=sql-database) verwenden, um die Kosten für den LTR-Speicher zu schätzen.

Wie bei PITR auch, sind die LTR-Sicherungen georedundant und per [regionsübergreifender Replikation für Azure Storage](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) geschützt.

Weitere Informationen finden Sie unter [Langfristiges Aufbewahren von Sicherungen](sql-database-long-term-retention.md).

## <a name="storage-costs"></a>Speicherkosten
Automatisierte Sicherungen Ihrer Datenbanken für einen Zeitraum von 7 Tagen werden standardmäßig in ein Blob Storage-RA-GRS-Konto mit der SKU „Standard“ kopiert. Der Speicher wird für wöchentliche vollständige Sicherungen, tägliche differenzielle Sicherungen und im 5-Minuten-Takt kopierte Sicherungen von Transaktionsprotokollen verwendet. Die Größe des Transaktionsprotokolls hängt von der Änderungsrate der Datenbank ab. Eine Mindestspeichermenge, die der Gesamtgröße der Datenbank entspricht, wird kostenlos zur Verfügung gestellt. Zusätzlich verbrauchter Sicherungsspeicher wird pro GB und Monat abgerechnet.

Weitere Informationen zu den Preisen für Storage finden Sie auf der Seite [Azure SQL-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-database/single/). 

## <a name="are-backups-encrypted"></a>Werden Sicherungen verschlüsselt?

Wenn Ihre Datenbank mit TDE verschlüsselt ist, werden die Sicherungen im Ruhezustand, einschließlich LTR-Sicherungen, automatisch verschlüsselt. Wenn Transparent Data Encryption (TDE) für eine Azure SQL-Datenbank aktiviert ist, werden die Sicherungen ebenfalls verschlüsselt. In allen neuen Azure SQL-Datenbanken ist TDE standardmäßig aktiviert. Weitere Informationen finden Sie unter [Transparent Data Encryption mit Azure SQL-Datenbank](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="how-does-microsoft-ensure-backup-integrity"></a>Wie stellt Microsoft die Sicherungsintegrität sicher?

Fortlaufend testet das Entwicklungsteam von Azure SQL-Datenbank automatisch die Wiederherstellung von automatischen Sicherungen von Datenbanken über den Dienst. Bei der Wiederherstellung werden die Datenbanken außerdem mithilfe von DBCC CHECKDB Integritätsprüfungen unterzogen. Mögliche Probleme, die bei der Integritätsprüfung gefunden werden, führen zu einer Warnung des Entwicklungsteams. Weitere Informationen zur Integrität der Daten in Azure SQL-Datenbank finden Sie unter [Datenintegrität in Azure SQL-Datenbank](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="how-do-automated-backups-impact-compliance"></a>Wie wirken sich automatisierte Sicherungen auf die Konformität aus?

Wenn Sie Ihre Datenbank von einer DTU-basierten Dienstebene mit einer PITR-Standardaufbewahrung von 35 Tagen zu einer Dienstebene auf V-Kern-Basis migrieren, wird die PITR-Aufbewahrung beibehalten. So soll sichergestellt werden, dass die Datenwiederherstellungsrichtlinie Ihrer Anwendung nicht kompromittiert wird. Falls die Standardaufbewahrung Ihre Konformitätsanforderungen nicht erfüllt, können Sie die PITR-Aufbewahrungsdauer per PowerShell oder REST-API ändern. Weitere Informationen finden Sie unter [Ändern des Zeitraums für die Aufbewahrung von Sicherungen](#how-to-change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-the-pitr-backup-retention-period"></a>Ändern des Zeitraums für die Aufbewahrung von PITR-Sicherungen

Sie können den Standardzeitraum für die Aufbewahrung von PITR-Sicherungen mit dem Azure-Portal, PowerShell oder der REST-API ändern. Die unterstützten Werte sind: 7, 14, 21, 28 oder 35 Tage. In den folgenden Beispielen wird veranschaulicht, wie Sie die PITR-Aufbewahrungsdauer in 28 Tage ändern.

> [!NOTE]
> Diese APIs wirken sich nur auf die PITR-Aufbewahrungsdauer aus. Falls Sie für Ihre Datenbank LTR konfiguriert haben, ergeben sich keine Auswirkungen. Weitere Informationen zum Ändern von LTR-Aufbewahrungsdauern finden Sie unter [Langfristige Aufbewahrung](sql-database-long-term-retention.md).

### <a name="change-pitr-backup-retention-period-using-the-azure-portal"></a>Ändern der PITR-Aufbewahrungsdauer über das Azure-Portal

Um die Aufbewahrungsdauer der PITR-Sicherung über das Azure-Portal zu ändern, navigieren Sie zu dem Serverobjekt, dessen Aufbewahrungsdauer Sie innerhalb des Portals ändern möchten, und wählen Sie dann die entsprechende Option basierend auf dem Serverobjekt aus, das Sie ändern.

#### <a name="change-pitr-for-a-sql-database-server"></a>Ändern von PITR für einen SQL-Datenbank-Server

![Ändern von PITR im Azure-Portal](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="change-pitr-for-a-managed-instance"></a>Ändern von PITR für eine verwaltete Instanz

![Ändern von PITR im Azure-Portal](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

### <a name="change-pitr-backup-retention-period-using-powershell"></a>Ändern der PITR-Aufbewahrungsdauer mit PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von der Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az- und in den AzureRm-Modulen sind im Wesentlichen identisch.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>Ändern der PITR-Aufbewahrungsdauer mit der REST-API

#### <a name="sample-request"></a>Beispiel für eine Anforderung

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Anforderungstext

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Beispiel für eine Antwort

Statuscode: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

Weitere Informationen finden Sie unter [REST-API für die Aufbewahrung von Sicherungen](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>Nächste Schritte

- Datenbanksicherungen sind ein wesentlicher Bestandteil jeder Strategie für Geschäftskontinuität und Notfallwiederherstellung, da Ihre Daten vor versehentlichen Beschädigungen und Löschungen geschützt werden. Weitere Informationen zu den anderen Geschäftskontinuitätslösungen von Azure SQL-Datenbank finden Sie unter [Übersicht über die Geschäftskontinuität mit Azure SQL-Datenbank](sql-database-business-continuity.md).
- Informationen zur Wiederherstellung des Zustands zu einem bestimmten Zeitpunkt über das Azure-Portal finden Sie unter [Wiederherstellen des Zustands einer Azure SQL-Datenbank zu einem früheren Zeitpunkt über das Azure-Portal](sql-database-recovery-using-backups.md).
- Informationen zur Wiederherstellung des Zustands zu einem bestimmten Zeitpunkt mithilfe von PowerShell finden Sie unter [Wiederherstellen des Zustands einer Azure SQL-Datenbank zu einem früheren Zeitpunkt mit PowerShell](scripts/sql-database-restore-database-powershell.md).
- Informationen zum Konfigurieren, Verwalten und Wiederherstellen aus der Langzeitaufbewahrung automatisierter Sicherungen in Azure Blob Storage mit dem Azure-Portal finden Sie im Artikel [Verwalten der Langzeitaufbewahrung von Sicherungen mit dem Azure-Portal](sql-database-long-term-backup-retention-configure.md).
- Informationen zum Konfigurieren, Verwalten und Wiederherstellen aus der langfristigen Aufbewahrung automatisierter Sicherungen im Azure-Blobspeicher mit Azure PowerShell finden Sie unter [Verwalten der langfristigen Sicherungsaufbewahrung mit PowerShell](sql-database-long-term-backup-retention-configure.md).

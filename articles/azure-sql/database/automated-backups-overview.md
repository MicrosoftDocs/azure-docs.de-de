---
title: Automatische, georedundante Sicherungen
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL-Datenbank und Azure SQL Managed Instance erstellen alle paar Minuten automatisch eine lokale Datenbanksicherung und verwenden georedundanten Azure-Speicher mit Lesezugriff (Read-Access Geo-Redundant Storage, RA-GRS), um für Georedundanz zu sorgen.
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: references_regions
ms.topic: conceptual
author: shkale-msft
ms.author: shkale
ms.reviewer: mathoma, stevestein, danil
ms.date: 11/18/2020
ms.openlocfilehash: c6754e6f0e3f0d6208bd34c96c8bc473429c943c
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917901"
---
# <a name="automated-backups---azure-sql-database--sql-managed-instance"></a>Automatisierte Sicherungen – Azure SQL-Datenbank und SQL Managed Instance

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-database-backup"></a>Was ist eine Datenbanksicherung?

Datenbanksicherungen sind ein wesentlicher Bestandteil jeder Strategie für Geschäftskontinuität und Notfallwiederherstellung, da Ihre Daten vor Beschädigungen und Löschungen geschützt werden. Diese Sicherungen ermöglichen die Wiederherstellung der Datenbank bis zu einem bestimmten Zeitpunkt innerhalb der konfigurierten Beibehaltungsdauer. Wenn es gemäß Ihren Sicherheitsregeln erforderlich ist, dass Ihre Sicherungen über einen längeren Zeitraum verfügbar sind (bis zu 10 Jahre), können Sie sowohl für Singletons als auch für Pooldatenbanken die [Langzeitaufbewahrung](long-term-retention-overview.md) konfigurieren.

### <a name="backup-frequency"></a>Sicherungshäufigkeit

Sowohl SQL-Datenbank als auch SQL Managed Instance nutzen SQL Server-Technologie, um wöchentlich [vollständige Sicherungen](/sql/relational-databases/backup-restore/full-database-backups-sql-server), alle 12–24 Stunden [differenzielle Sicherungen](/sql/relational-databases/backup-restore/differential-backups-sql-server) und alle 5 bis 10 Minuten [Transaktionsprotokollsicherungen](/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) zu erstellen. Die Häufigkeit von Transaktionsprotokollsicherungen basiert auf der Computegröße und dem Umfang der Datenbankaktivität.

Wenn Sie eine Datenbank wiederherstellen, bestimmt der Dienst, welche vollständigen und differenziellen Sicherungen bzw. Transaktionsprotokollsicherungen wiederhergestellt werden müssen.

### <a name="backup-storage-redundancy"></a>Redundanz für Sicherungsspeicher

Standardmäßig speichern SQL-Datenbank und SQL Managed Instance Daten in georedundanten [Speicherblobs](../../storage/common/storage-redundancy.md), die in einem [Regionspaar](../../best-practices-availability-paired-regions.md) repliziert werden. Dies dient zum Schutz vor Ausfällen, die sich auf den Sicherungsspeicher in der primären Region auswirken, und ermöglicht es Ihnen, Ihren Server bei einem Notfall in einer anderen Region wiederherzustellen. 

Die Option zum Konfigurieren der Redundanz für Sicherungsspeicher bietet Flexibilität bei der Auswahl zwischen lokal redundanten, zonenredundanten oder georedundanten Speicherblobs für eine SQL Managed Instance-Instanz oder eine SQL-Datenbank-Instanz. Damit Ihre Daten in der Region bleiben, in der auch Ihre verwaltete Instanz oder SQL-Datenbank bereitgestellt wurde, können Sie den Standardwert für Redundanz (georedundanter Sicherungsspeicher) ändern und entweder lokal redundante oder zonenredundante Speicherblobs für Sicherungen konfigurieren. Mechanismen der Speicherredundanz speichern mehrere Kopien Ihrer Daten, damit sie vor geplanten und ungeplanten Ereignissen geschützt sind – von vorübergehend auftretenden Hardwarefehlern über Netzwerk- oder Stromausfälle bis hin zu schweren Naturkatastrophen. Die konfigurierte Redundanz für Sicherungsspeicher wird sowohl auf die Einstellungen für kurzfristige Sicherungsaufbewahrung angewendet, die für die Zeitpunktwiederherstellung (Point In Time Restore, PITR) verwendet werden, als auch auf die Langzeitaufbewahrung von Sicherungen (Long-Term Retention, LTR), die für langfristige Sicherungen verwendet wird. 

Die Sicherungsspeicherredundanz für eine SQL-Datenbank-Instanz kann entweder beim Erstellen der Datenbank konfiguriert oder für vorhandene Datenbanken aktualisiert werden. Die an einer vorhandenen Datenbank vorgenommenen Änderungen gelten jedoch nur für zukünftige Sicherungen. Nachdem die Redundanz für Sicherungsspeicher einer vorhandenen Datenbank aktualisiert wurde, kann es bis zu 48 Stunden dauern, bis die Änderungen angewendet werden. Beachten Sie, dass die Geowiederherstellung deaktiviert wird, sobald die Datenbank so aktualisiert wurde, dass lokaler oder zonenredundanter Speicher verwendet wird. 


> [!IMPORTANT]
> Konfigurieren Sie die Sicherungsspeicherredundanz während des Erstellungsprozesses der verwalteten Instanz, da es nach der Bereitstellung der Ressource nicht mehr möglich ist, die Speicherredundanz zu ändern. 

> [!IMPORTANT]
> Zonenredundanter Speicher steht zurzeit nur in [bestimmten Regionen](../../storage/common/storage-redundancy.md#zone-redundant-storage) zur Verfügung. 

> [!NOTE]
> Die konfigurierbare Sicherungsspeicherredundanz für Azure SQL-Datenbank ist zurzeit nur in der Azure-Region „Brasilien, Süden“ als Public Preview und in der Region „Asien, Südosten“ allgemein verfügbar. Für die Hyperscale-Ebene ist dieses Feature noch nicht verfügbar. 

### <a name="backup-usage"></a>Sicherungsverwendung

Sie können diese Sicherungen für Folgendes verwenden:

- **Zeitpunktwiederherstellung einer vorhandenen Datenbank** - [Stellen Sie für eine vorhandene Datenbank den Stand zu einem vergangenen Zeitpunkt wieder her](recovery-using-backups.md#point-in-time-restore), der innerhalb des Aufbewahrungszeitraums liegt. Verwenden Sie dafür das Azure-Portal, Azure PowerShell, die Azure-Befehlszeilenschnittstelle (Azure CLI) oder die REST-API. Bei SQL-Datenbank erstellt dieser Vorgang eine neue Datenbank auf demselben Server wie die ursprüngliche Datenbank, verwendet aber einen anderen Namen, um ein Überschreiben der ursprünglichen Datenbank zu vermeiden. Nach Abschluss der Wiederherstellung können Sie die ursprüngliche Datenbank löschen. Alternativ dazu können Sie die ursprüngliche Datenbank [umbenennen](/sql/relational-databases/databases/rename-a-database) und dann die wiederhergestellte Datenbank auf den ursprünglichen Datenbanknamen umbenennen. In ähnlicher Weise erstellt dieser Vorgang bei SQL Managed Instance eine Kopie der Datenbank in derselben oder einer anderen verwalteten Instanz in demselben Abonnement und derselben Region.
- **Zeitpunktwiederherstellung einer gelöschten Datenbank** - [Stellen Sie bei einer gelöschten Datenbank den Stand zum Zeitpunkt des Löschvorgangs wieder her](recovery-using-backups.md#deleted-database-restore) oder zu einem beliebigen anderen Zeitpunkt innerhalb des Aufbewahrungszeitraums. Die gelöschte Datenbank kann nur auf demselben Server oder in derselben verwalteten Instanz wiederhergestellt werden, auf dem bzw. in der die ursprüngliche Datenbank erstellt wurde. Beim Löschen einer Datenbank nimmt der Dienst zuvor eine abschließende Transaktionsprotokollsicherung vor, um Datenverluste zu vermeiden.
- **Geowiederherstellung** - [Stellen Sie eine Datenbank in einer anderen geografischen Region wieder her](recovery-using-backups.md#geo-restore). Die Geowiederherstellung ermöglicht die Wiederherstellung nach dem Ausfall einer geografischen Region, wenn Sie keinen Zugriff mehr auf Ihre Datenbank oder Ihre Sicherungen in der primären Region haben. Dabei wird eine neue Datenbank auf einem beliebigen vorhandenen Server oder in einer verwalteten Instanz in einer beliebigen Azure-Region erstellt.
   > [!IMPORTANT]
   > Geowiederherstellung ist nur für SQL-Datenbanken oder verwaltete Instanzen verfügbar, die mit einem georedundanten Sicherungsspeicher konfiguriert wurden.
- **Wiederherstellen aus Langzeitaufbewahrung** - [Führen Sie die Wiederherstellung einer Datenbank aus einer bestimmten langfristigen Sicherung eines Singletons oder einer Pooldatenbank durch](long-term-retention-overview.md), wenn die Datenbank mit einer Richtlinie zur Langzeitaufbewahrung (Long-Term Retention, LTR) konfiguriert wurde. Mit LTR können Sie eine alte Version der Datenbank wiederherstellen, indem Sie [das Azure-Portal](long-term-backup-retention-configure.md#using-the-azure-portal) oder [Azure PowerShell](long-term-backup-retention-configure.md#using-powershell) verwenden, um eine Konformitätsanforderung zu erfüllen oder eine alte Version der Anwendung auszuführen. Weitere Informationen finden Sie unter [Langfristige Aufbewahrung](long-term-retention-overview.md).

Informationen zum Durchführen einer Wiederherstellung finden Sie unter [Wiederherstellen einer Azure SQL-Datenbank mit automatisierten Datenbanksicherungen](recovery-using-backups.md).

> [!NOTE]
> In Azure Storage bezieht sich der Begriff *Replikation* auf das Kopieren von Blobs von einem Speicherort an einen anderen. In SQL bezieht sich *Datenbankreplikation* auf verschiedene Technologien, über die mehrere sekundäre Datenbanken mit einer primären Datenbank synchron bleiben.

Sie können Sicherungs- und Wiederherstellungsvorgänge für Konfigurationen anhand der folgenden Beispiele ausprobieren:

| Vorgang | Azure-Portal | Azure PowerShell |
|---|---|---|
| **Ändern der Sicherungsaufbewahrung** | [SQL-Datenbank](automated-backups-overview.md?tabs=single-database#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [SQL Managed Instance](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [SQL-Datenbank](automated-backups-overview.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[SQL Managed Instance](/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| **Ändern der Langzeitaufbewahrung von Sicherungen** | [SQL-Datenbank](long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>SQL Managed Instance – nicht verfügbar  | [SQL-Datenbank](long-term-backup-retention-configure.md)<br/>[SQL Managed Instance](../managed-instance/long-term-backup-retention-configure.md)  |
| **Wiederherstellen einer Datenbank bis zu einem Zeitpunkt** | [SQL-Datenbank](recovery-using-backups.md#point-in-time-restore)<br>[SQL Managed Instance](../managed-instance/point-in-time-restore.md) | [SQL-Datenbank](/powershell/module/az.sql/restore-azsqldatabase) <br/> [SQL Managed Instance](/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| **Wiederherstellen einer gelöschten Datenbank** | [SQL-Datenbank](recovery-using-backups.md)<br>[SQL Managed Instance](../managed-instance/point-in-time-restore.md#restore-a-deleted-database) | [SQL-Datenbank](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [SQL Managed Instance](/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| **Wiederherstellen einer Datenbank aus Azure Blob Storage** | SQL-Datenbank – nicht verfügbar <br/>SQL Managed Instance – nicht verfügbar  | SQL-Datenbank – nicht verfügbar <br/>[SQL Managed Instance](../managed-instance/restore-sample-database-quickstart.md) |

## <a name="backup-scheduling"></a>Sicherungszeitplanung

Die erste vollständige Sicherung wird unmittelbar nach dem Erstellen oder Wiederherstellen einer neuen Datenbank geplant. Diese Sicherung wird normalerweise innerhalb von 30 Minuten abgeschlossen, kann aber länger dauern, wenn die Datenbank groß ist. Die erste Sicherung kann bei einer wiederhergestellten Datenbank oder einer Datenbankkopie beispielsweise länger dauern, da diese normalerweise größer als eine neue Datenbank ist. Nach der ersten vollständigen Sicherung werden alle weiteren Sicherungen automatisch geplant und verwaltet. Der genaue Zeitpunkt für alle Datenbanksicherungen wird vom SQL-Datenbank- oder SQL Managed Instance-Dienst festgelegt, da dort die gesamte Systemworkload verwaltet wird. Sie können den Zeitplan der Sicherungsaufträge nicht ändern oder sie deaktivieren.

> [!IMPORTANT]
> Für eine neue, wiederhergestellte oder kopierte Datenbank wird die Funktion für Point-in-Time-Wiederherstellung ab dem Zeitpunkt der Erstellung der ersten Transaktionsprotokollsicherung verfügbar, die auf die anfängliche vollständige Sicherung folgt.

## <a name="backup-storage-consumption"></a>Sicherungsspeicherverbrauch

Mit der Sicherungs- und Wiederherstellungstechnologie von SQL Server setzt das Wiederherstellen einer Datenbank zu einem bestimmten Zeitpunkt eine ununterbrochene Sicherungskette voraus, die aus einer vollständigen Sicherung, optional einer differenziellen Sicherung und einer oder mehreren Transaktionsprotokollsicherungen besteht. Der Sicherungszeitplan für SQL-Datenbank und SQL Managed Instance umfasst jede Woche eine vollständige Sicherung. Um die Point-in-Time-Wiederherstellung (PITR) innerhalb der gesamten Beibehaltungsdauer zu aktivieren, muss das System daher zusätzlich vollständige, differenzielle und Transaktionsprotokollsicherungen für bis zu eine Woche über die konfigurierte Beibehaltungsdauer hinaus speichern. 

Anders ausgedrückt: Für jeden Zeitpunkt innerhalb der Beibehaltungsdauer muss eine vollständige Sicherung vorhanden sein, die älter als der Beginn der Beibehaltungsdauer ist, sowie eine ununterbrochene Kette von differenziellen und Transaktionsprotokollsicherungen von dieser vollständigen Sicherung bis zur nächsten vollständigen Sicherung.

> [!NOTE]
> Zum Aktivieren der Point-in-Time-Wiederherstellung werden zusätzliche Sicherungen bis zu einer Woche über die konfigurierte Beibehaltungsdauer hinaus gespeichert. Der Sicherungsspeicher wird für alle Sicherungen mit derselben Gebühr abgerechnet. 

Sicherungen, die für die Bereitstellung der PITR-Funktionalität nicht mehr erforderlich sind, werden automatisch gelöscht. Da differenzielle Sicherungen und Protokollsicherungen erst wiederhergestellt werden können, wenn zuvor eine vollständige Sicherung erfolgt ist, werden alle drei Sicherungstypen in wöchentlichen Blöcken gemeinsam bereinigt.

Für alle Datenbanken, einschließlich [TDE-verschlüsselter](transparent-data-encryption-tde-overview.md) Datenbanken, werden Sicherungen komprimiert, um die Auslastung des Sicherungsspeichers und die Kosten zu verringern. Das durchschnittliche Komprimierungsverhältnis für Sicherungen ist das Drei- bis Vierfache, dies kann jedoch je nach Art der Daten und der Verwendung der Datenkomprimierung in der Datenbank erheblich niedriger oder höher ausfallen.

SQL-Datenbank und SQL Managed Instance berechnen den gesamten genutzten Sicherungsspeicher als kumulativen Wert. Jede Stunde wird dieser Wert an die Azure-Abrechnungspipeline gemeldet, die für die Aggregierung dieser stündlichen Nutzung verantwortlich ist, um die Nutzung am Ende jedes Monats zu berechnen. Nach dem Löschen der Datenbank sinkt der Verbrauch mit zunehmendem Alter (und dem schließlichen Löschen) der Sicherungen. Wenn alle Sicherungen gelöscht wurden und keine Point-in-Time-Wiederherstellung mehr möglich ist, endet die Abrechnung.
   
> [!IMPORTANT]
> Sicherungen einer Datenbank werden beibehalten, um Point-in-Time-Wiederherstellungen auch dann zu ermöglichen, wenn die Datenbank gelöscht wurde. Das Löschen und erneute Erstellen einer Datenbank kann möglicherweise Speicher- und Computekosten einsparen. Die Kosten für den Sicherungsspeicher können dadurch jedoch erhöht werden, da der Dienst Sicherungen für jede gelöschte Datenbank bei jedem Löschvorgang beibehält. 

### <a name="monitor-consumption"></a>Überwachen des Verbrauchs

Für Datenbanken auf virtuellen Kernen wird der verbrauchte Speicher für jeden Sicherungstyp (vollständig, differenziell und Protokoll) auf dem Blatt für die Datenbanküberwachung als separate Metrik ausgewiesen. Im folgenden Diagramm wird gezeigt, wie Sie den Sicherungsspeicherverbrauch für eine Einzeldatenbank überwachen. Dieses Feature ist derzeit für verwaltete Instanzen nicht verfügbar.

![Überwachen des Sicherungsspeicherverbrauchs von Datenbanken im Azure-Portal](./media/automated-backups-overview/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>Optimieren des Sicherungsspeicherverbrauchs

Der Speicherverbrauch für Sicherungen bis zur maximalen Datengröße für eine Datenbank wird nicht in Rechnung gestellt. Der zusätzliche Sicherungsspeicherverbrauch hängt von der Workload und der maximalen Größe der einzelnen Datenbanken ab. Ziehen Sie einige der folgenden Optimierungstechniken in Betracht, um den Sicherungsspeicherverbrauch zu reduzieren:

- Reduzieren Sie den [Aufbewahrungszeitraum für Sicherungen](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) auf die Mindestanforderungen für Ihre Zwecke.
- Vermeiden Sie es, große Schreibvorgänge, wie z.B. die Neuerstellung von Indizes, öfter als nötig durchzuführen.
- Bei umfangreichen Datenladevorgängen sollten Sie [gruppierte Columnstore-Indizes](/sql/relational-databases/indexes/columnstore-indexes-overview) verwenden und die entsprechenden [Best Practices](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) befolgen oder die Anzahl der nicht gruppierten Indizes reduzieren.
- Auf der Dienstebene „Universell“ ist der bereitgestellte Datenspeicher günstiger als die Kosten für den Sicherungsspeicher. Wenn ständig hohe Kosten durch zusätzlichen Sicherungsspeicher anfallen, können Sie eine Vergrößerung des Datenspeichers in Betracht ziehen, um beim Sicherungsspeicher zu sparen.
- Verwenden Sie TempDB anstelle permanenter Tabellen in Ihrer Anwendungslogik zum Speichern temporärer Ergebnisse oder vorübergehender Daten.
- Nutzen Sie nach Möglichkeit lokal redundanten Sicherungsspeicher (z. B. dev/Testumgebungen).

## <a name="backup-retention"></a>Sicherungsaufbewahrung

Für alle neuen, wiederhergestellten und kopierten Datenbanken behalten Azure SQL-Datenbank und Azure SQL Managed Instance standardmäßig ausreichende Sicherungen für die Point-in-Time-Wiederherstellung in den letzten 7 Tagen bei. Mit Ausnahme von Hyperscale-Datenbanken können Sie den [Aufbewahrungszeitraum von Sicherungen](#change-the-pitr-backup-retention-period) pro aktiver Datenbank im Bereich von 1 bis 35 Tagen ändern. Wie unter [Sicherungsspeicherverbrauch](#backup-storage-consumption) beschrieben, liegen Sicherungen, die zum Ermöglichen der Point-in-Time-Wiederherstellung gespeichert wurden, möglicherweise zeitlich vor der Beibehaltungsdauer. Nur für Azure SQL Managed Instance ist es möglich, die PITR-Sicherungsaufbewahrungsrate festzulegen, nachdem eine Datenbank innerhalb des Zeitraums von 0 bis 35 Tagen gelöscht wurde. 

Wenn Sie eine Datenbank löschen, behält das System Sicherungen genauso bei, wie dies für eine Onlinedatenbank mit der jeweiligen Beibehaltungsdauer gelten würde. Die Beibehaltungsdauer für eine gelöschte Datenbank kann nicht geändert werden.

> [!IMPORTANT]
> Wenn Sie den Server oder die verwaltete Instanz löschen, werden auch alle Datenbanken auf diesem Server oder in dieser verwalteten Instanz gelöscht und können nicht mehr wiederhergestellt werden. Ein gelöschter Server oder eine gelöschte verwaltete Instanz kann nicht wiederhergestellt werden. Wenn Sie jedoch die Langzeitaufbewahrung (Long-Term Retention, LTR) für eine Datenbank oder eine verwaltete Instanz konfiguriert haben, werden Sicherungen für die Langzeitaufbewahrung nicht gelöscht und können zum Wiederherstellen von Datenbanken auf einem anderen Server bzw. einer anderen verwalteten Instanz im selben Abonnement verwendet werden. Dabei wird der Zeitpunkt wiederhergestellt, an dem die Sicherung die Langzeitaufbewahrung durchgeführt wurde.

Die Beibehaltungsdauer der Sicherung für Point-in-Time-Wiederherstellungen innerhalb der letzten 1–35 Tage wird auch als kurzfristige Beibehaltung der Sicherung bezeichnet. Wenn Sie Sicherungen länger als für die maximale kurzfristige Beibehaltungsdauer von 35 Tagen aufbewahren müssen, können Sie die [Langzeitaufbewahrung](long-term-retention-overview.md) aktivieren.

### <a name="long-term-retention"></a>Langfristige Aufbewahrung

Sowohl für SQL-Datenbank als auch für SQL Managed Instance können Sie eine langfristige Aufbewahrung (Langzeitaufbewahrung, Long-Term Retention, LTR) der vollständigen Sicherung für bis zu 10 Jahre in Azure Blob Storage konfigurieren. Nachdem die LTR-Richtlinie konfiguriert wurde, werden vollständige Sicherungen wöchentlich automatisch in einen anderen Speichercontainer kopiert. Zur Einhaltung verschiedener Complianceanforderungen können Sie verschiedene Aufbewahrungszeiträume für wöchentliche, monatliche oder jährliche vollständige Sicherungen auswählen. Der Speicherverbrauch ist abhängig von der ausgewählten Häufigkeit und den Aufbewahrungszeiträumen für LTR-Sicherungen. Sie können den [LTR-Preisrechner](https://azure.microsoft.com/pricing/calculator/?service=sql-database) verwenden, um die Kosten für den LTR-Speicher zu schätzen.

> [!IMPORTANT]
> Wenn die Sicherungsspeicherredundanz für eine vorhandene Azure SQL-Datenbank-Instanz aktualisiert wird, gilt dies nur für zukünftige Sicherungen, die für die Datenbank erstellt werden. Alle vorhandenen LTR-Sicherungen für die Datenbank sind weiterhin im vorhandenen Speicherblob zu finden, und neue Sicherungen werden mit dem angeforderten Speicherblobtyp gespeichert. 

Weitere Informationen zu LTR finden Sie unter [Langfristiges Aufbewahren von Sicherungen](long-term-retention-overview.md).

## <a name="storage-costs"></a>Speicherkosten

Der Preis für Sicherungsspeicher variiert und ist abhängig von Ihrem Kaufmodell (DTU oder vCore), der ausgewählten Option zur Redundanz für Sicherungsspeicher und auch Ihrer Region. Der Sicherungsspeicher wird pro GB/Verbrauchsmonat in Rechnung gestellt. Die Preise finden Sie auf der Seite [Preise für Azure SQL-Datenbank](https://azure.microsoft.com/pricing/details/sql-database/single/) und der Seite [Preise für Azure SQL Managed Instance](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

### <a name="dtu-model"></a>DTU-Modell

Im DTU-Modell fallen keine zusätzlichen Kosten für den Sicherungsspeicher für Datenbanken und Pools für elastische Datenbanken an. Der Preis für den Sicherungsspeicher ist im Preis für die Datenbank oder den Pool inbegriffen.

### <a name="vcore-model"></a>V-Kern-Modell

Bei Singletons in SQL-Datenbank wird eine Sicherungsspeichermenge, die der maximalen Datenspeichergröße für die Datenbank entspricht, ohne zusätzliche Kosten zur Verfügung gestellt. Bei Pools für elastische Datenbanken und verwalteten Instanzen wird eine Sicherungsspeichermenge, die der Gesamtgröße des maximalen Datenspeichers für den Pool bzw. der maximalen Instanzgröße entspricht, ohne zusätzliche Kosten bereitgestellt. 

Bei Singletons wird der gesamte in Rechnung gestellte Sicherungsspeicherbedarf wie folgt berechnet:

`Total billable backup storage size = (size of full backups + size of differential backups + size of log backups) – maximum data storage`

Bei Pooldatenbanken wird die Gesamtgröße des in Rechnung gestellten Sicherungsspeichers auf Poolebene aggregiert und wie folgt berechnet:

`Total billable backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - maximum pool data storage`

Bei verwalteten Instanzen wird die Gesamtgröße des in Rechnung gestellten Sicherungsspeichers auf Instanzebene aggregiert und wie folgt berechnet:

`Total billable backup storage size = (total size of full backups + total size of differential backups + total size of log backups) – maximum instance data storage`

Sofern Sicherungsspeicher vorhanden ist, für den Kosten anfallen, wird insgesamt in GB/Monat mit der für die Sicherungsspeicherredundanz verwendeten Rate abgerechnet. Der Sicherungsspeicherverbrauch ist von der Workload und der Größe der einzelnen Datenbanken, Pools für elastische Datenbanken und verwalteten Instanzen abhängig. Datenbanken mit vielen Änderungen weisen größere differenzielle Sicherungen und Protokollsicherungen auf, da die Größe dieser Sicherungen proportional zur Menge der Datenänderungen ist. Daher fallen für diese Datenbanken höhere Sicherungsgebühren an.

SQL-Datenbank und SQL Managed Instance berechnen den gesamten in Rechnung gestellten Sicherungsspeicher als kumulativen Wert aller Sicherungsdateien. Dieser Wert wird jede Stunde der Azure-Abrechnungspipeline gemeldet, die diese stündliche Nutzung aggregiert, um den Verbrauch an Sicherungsspeicher am Ende jedes Monats zu berechnen. Wenn eine Datenbank gelöscht wird, nimmt der Speicherverbrauch für die Sicherung allmählich ab, da ältere Sicherungen das maximale Alter erreichen und gelöscht werden. Da differenzielle Sicherungen und Protokollsicherungen erst wiederhergestellt werden können, wenn zuvor eine vollständige Sicherung erfolgt ist, werden alle drei Sicherungstypen in wöchentlichen Blöcken gemeinsam bereinigt. Nachdem alle Sicherungen gelöscht wurden, endet die Abrechnung. 

Als vereinfachtes Beispiel sei angenommen, eine Datenbank hat 744 GB Sicherungsspeicher angesammelt, und diese Menge bleibt während eines ganzen Monats konstant, da die Datenbank vollkommen inaktiv ist. Um diesen kumulativen Speicherverbrauch in eine stündliche Nutzung umzurechnen, dividieren wir ihn durch 744,0 (31 Tage pro Monat x 24 Stunden pro Tag). SQL-Datenbank meldet an die Azure-Abrechnungspipeline, dass die Datenbank mit konstanter Rate pro Stunde 1 GB an PITR-Sicherungen verbraucht hat. Die Azure-Abrechnung aggregiert dies und zeigt eine Nutzung von 744 GB für den gesamten Monat. Die Kosten basierend auf dem EUR/GB/Monat-Satz in Ihrer Region an. Die Kosten basierend auf den Kosten für die Menge in GB pro Monat in Ihrer Region.

Jetzt folgt ein komplexeres Beispiel. Angenommen, für dieselbe inaktive Datenbank wird der Aufbewahrungszeitraum in der Mitte des Monats von 7 Tagen auf 14 Tage erhöht. Diese Zunahme führt dazu, dass sich der gesamte Sicherungsspeicher auf 1.488 GB verdoppelt. SQL-Datenbank meldet eine Nutzung von 1 GB für die Stunden 1 bis 372 (die erste Monatshälfte). Die Nutzung wird als 2 GB für die Stunden 373 bis 744 (die zweite Monatshälfte) gemeldet. Die monatliche Schlussrechnung basiert dann auf dem aggregierten Wert von 1.116 GB.

Die tatsächlichen Abrechnungsszenarien für Sicherungen sind komplexer. Da die Rate von Änderung in der Datenbank von der Workload abhängig und zeitlich variabel ist, variiert auch die Größe der einzelnen differenziellen und Protokollsicherungen, sodass der Speicherverbrauch für stündliche Sicherungen entsprechend schwankt. Darüber hinaus enthält jede differenzielle Sicherung alle Änderungen, die seit der letzten vollständigen Sicherung an der Datenbank vorgenommen wurden. Daher nimmt die Gesamtgröße aller differenziellen Sicherungen im Verlauf einer Woche allmählich zu und fällt dann deutlich ab, wenn ein älterer Satz vollständiger, differenzieller und Protokollsicherungen das Alter zum Löschen erreicht. Wenn beispielsweise eine intensive Schreibaktivität wie etwa eine Indexneuerstellung unmittelbar nach Abschluss einer vollständigen Sicherung ausgeführt wurde, werden die durch die Indexneuerstellung vorgenommenen Änderungen in die Transaktionsprotokollsicherungen während der Neuerstellung, in die nächsten differenziellen Sicherung und in jede differenzielle Sicherung bis zur nächsten vollständigen Sicherung eingeschlossen. Beim letztgenannten Szenario erstellt eine Optimierung am Dienst für größere Datenbanken eine vollständige Sicherung anstelle einer differenziellen Sicherung, wenn eine differenzielle Sicherung andernfalls übermäßig groß wäre. Dadurch wird die Größe aller differenziellen Sicherungen bis zur folgenden vollständigen Sicherung verringert.

Sie können den gesamten Sicherungsspeicherverbrauch für jeden Sicherungstyp (vollständig, differenziell, Transaktionsprotokoll) über einen bestimmten Zeitraum überwachen, wie unter [Überwachen des Verbrauchs](#monitor-consumption) beschrieben.

### <a name="backup-storage-redundancy"></a>Redundanz für Sicherungsspeicher

Die Redundanz für Sicherungsspeicher wirkt sich auf Sicherungskosten folgendermaßen aus:
- Preis für lokale Redundanz: x
- Preis für Zonenredundanz: 1,25x
- Preis für Georedundanz: 2x

Weitere Informationen zu den Preisen für Sicherungsspeicher finden Sie auf der [Seite mit der Preisübersicht für Azure SQL-Datenbank](https://azure.microsoft.com/pricing/details/sql-database/single/) und der [Seite mit der Preisübersicht für Azure SQL Managed Instance](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

> [!IMPORTANT]
> Die Sicherungsspeicherredundanz kann für SQL Managed Instance-Instanzen in allen Azure-Regionen konfiguriert werden. Nur die Verfügbarkeit für SQL-Datenbank ist aktuell auf die Azure-Region „Asien, Südosten“ beschränkt. Bei SQL Managed Instance-Instanzen kann die Redundanz nur während des Erstellprozesses der verwalteten Instanz angegeben werden. Nachdem die Ressource bereitgestellt wurde, können Sie die Option für die Redundanz für Sicherungsspeicher nicht mehr ändern.

### <a name="monitor-costs"></a>Überwachen der Kosten

Um die Kosten für Sicherungsspeicher zu verstehen, wechseln Sie im Azure-Portal zu **Kostenverwaltung + Abrechnung**. Wählen Sie **Kostenverwaltung** und dann **Kostenanalyse** aus. Wählen Sie das gewünschte Abonnement als **Bereich** aus, und filtern Sie dann nach dem gewünschten Zeitraum und Dienst.

Fügen Sie einen Filter für **Dienstname** hinzu, und wählen Sie dann in der Dropdownliste **SQL-Datenbank** aus. Verwenden Sie den Filter **Unterkategorie der Verbrauchseinheit**, um den Abrechnungszähler für Ihren Dienst auszuwählen. Wählen Sie für eine einzelne Datenbank oder einen Pool für elastische Datenbanken den **PITR-Sicherungsspeicher für eine einzelne Datenbank/einen Pool für elastische Datenbanken** aus. Wählen Sie für eine verwaltete Instanz **MI-PITR-Sicherungsspeicher** aus. Die Unterkategorien **Speicher** und **Compute** können für Sie auch von Interesse sein, obwohl sie nicht im Zusammenhang mit den Sicherungsspeicherkosten stehen.

![Analyse der Kosten für Sicherungsspeicher](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

  >[!NOTE]
  > Verbrauchseinheiten sind nur für zurzeit verwendete Zähler sichtbar. Wenn ein Zähler nicht zur Verfügung steht, wird die entsprechende Kategorie zurzeit wahrscheinlich nicht verwendet. Beispielsweise werden für Kunden, die keine verwaltete Instanz bereitgestellt haben, Zähler für verwaltete Instanzen nicht angezeigt. Ebenso sind Speicherzähler für Ressourcen, die keinen Speicher belegen, nicht sichtbar. 

## <a name="encrypted-backups"></a>Verschlüsselte Sicherungen

Wenn Ihre Datenbank mit TDE verschlüsselt ist, werden Sicherungen im Ruhezustand, einschließlich LTR-Sicherungen, automatisch verschlüsselt. Bei allen neuen Datenbanken in Azure SQL ist TDE standardmäßig aktiviert. Weitere Informationen finden Sie unter [Transparent Data Encryption für SQL-Datenbank, SQL Managed Instance und Azure Synapse Analytics](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Sicherungsintegrität

Das Azure SQL-Entwicklungsteam testet fortlaufend automatisch die Wiederherstellung von automatischen Datenbanksicherungen. (Für SQL Managed Instance sind diese Tests derzeit nicht verfügbar.) Bei der Point-in-Time-Wiederherstellung werden die Datenbanken außerdem mithilfe von DBCC CHECKDB Integritätsprüfungen unterzogen.

Mögliche Probleme, die bei der Integritätsprüfung gefunden werden, führen zu einer Warnung des Entwicklungsteams. Weitere Informationen finden Sie unter [Datenintegrität in Azure SQL-Datenbank](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

Alle Datenbanksicherungen werden mit der Option „CHECKSUM“ erstellt, um eine zusätzliche Sicherungsintegrität zu gewährleisten.

## <a name="compliance"></a>Compliance

Wenn Sie Ihre Datenbank von einer DTU-basierten Dienstebene zu einer Dienstebene auf Basis virtueller Kerne migrieren, wird die PITR-Aufbewahrung beibehalten. So soll sichergestellt werden, dass die Datenwiederherstellungsrichtlinie Ihrer Anwendung nicht kompromittiert wird. Falls die Standardaufbewahrung Ihre Complianceanforderungen nicht erfüllt, können Sie die PITR-Aufbewahrungsdauer ändern. Weitere Informationen finden Sie unter [Ändern des PITR-Aufbewahrungszeitraums von Sicherungen](#change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>Ändern des PITR-Aufbewahrungszeitraums von Sicherungen

Sie können den Standardzeitraum für die Aufbewahrung von PITR-Sicherungen im Azure-Portal, mit PowerShell oder der REST-API ändern. In den folgenden Beispielen wird veranschaulicht, wie Sie die PITR-Aufbewahrungsdauer in 28 Tage ändern.

> [!WARNING]
> Wenn Sie die aktuelle Beibehaltungsdauer verringern, verlieren Sie die Möglichkeit, Zeitpunkte wiederherzustellen, die älter als die neue Beibehaltungsdauer sind. Sicherungen, die für die Bereitstellung von Point-in-Time-Wiederherstellungen innerhalb der neuen Beibehaltungsdauer nicht mehr benötigt werden, werden gelöscht. Wenn Sie die aktuelle Beibehaltungsdauer verringern, wird die Möglichkeit, Zeitpunkte innerhalb der neuen Beibehaltungsdauer wiederherzustellen, nicht sofort hergestellt. Sie erhalten diese Möglichkeit im Lauf der Zeit, während das System beginnt, Sicherungen länger aufzubewahren.

> [!NOTE]
> Diese APIs wirken sich nur auf die PITR-Aufbewahrungsdauer aus. Falls Sie für Ihre Datenbank LTR konfiguriert haben, ist sie nicht betroffen. Informationen zum Ändern von LTR-Aufbewahrungsdauern finden Sie unter [Langfristige Aufbewahrung](long-term-retention-overview.md).

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>Ändern der PITR-Aufbewahrungsdauer im Azure-Portal

Um die PITR-Aufbewahrungsdauer von Sicherungen für aktive Datenbanken im Azure-Portal zu ändern, navigieren Sie im Portal zum Server oder zur verwalteten Instanz mit den Datenbanken, deren Aufbewahrungsdauer geändert werden soll. 

#### <a name="sql-database"></a>[SQL-Datenbank](#tab/single-database)

Bei SQL-Datenbank wird die Aufbewahrung der Sicherungen für Point-in-Time-Wiederherstellung auf der Seite für den Server im Portal geändert. Wenn Sie die PITR-Aufbewahrung für Datenbanken auf einem Server ändern möchten, navigieren Sie zum Blatt mit der Übersicht für diesen Server. Wählen Sie im linken Bereich **Sicherungen verwalten**, dann die Datenbanken für Ihre Änderung und dann oben auf dem Bildschirm **Aufbewahrung konfigurieren** aus:

![Ändern der PITR-Aufbewahrung, Serverebene](./media/automated-backups-overview/configure-backup-retention-sqldb.png)

#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

Bei SQL Managed Instance wird die Aufbewahrung für Sicherungen für Zeitpunktwiederherstellung auf Ebene der einzelnen Datenbanken geändert. Um die Aufbewahrung von PITR-Sicherungen für eine Instanzdatenbank im Azure-Portal zu ändern, navigieren Sie zum Übersichtsblatt der jeweiligen Datenbank. Wählen Sie dann oben auf dem Bildschirm **Aufbewahrungsdauer für Sicherungen konfigurieren** aus.

![Ändern der PITR-Aufbewahrung, verwaltete Instanz](./media/automated-backups-overview/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>Ändern der PITR-Aufbewahrungsdauer mit PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das PowerShell-Modul AzureRM wird weiterhin von SQL-Datenbank und SQL Managed Instance unterstützt, in Zukunft wird jedoch nur das Modul Az.Sql weiterentwickelt. Weitere Informationen finden Sie unter [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch.

#### <a name="sql-database"></a>[SQL-Datenbank](#tab/single-database)

Verwenden Sie das folgende PowerShell-Beispiel, um die PITR-Aufbewahrungsdauer von Sicherungen für aktive Azure SQL-Datenbanken zu ändern.

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

Verwenden Sie das folgende PowerShell-Beispiel, um die PITR-Aufbewahrungsdauer von Sicherungen für eine **einzelne aktive** SQL Managed Instance-Datenbank zu ändern.

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase -RetentionDays 1
```

Verwenden Sie das folgende PowerShell-Beispiel, um die PITR-Aufbewahrungsdauer von Sicherungen für **alle aktiven** SQL Managed Instance-Datenbanken zu ändern.

```powershell
# SET new PITR backup retention period for ALL active databases
# Valid backup retention must be between 1 and 35 days
Get-AzSqlInstanceDatabase -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 1
```

Verwenden Sie das folgende PowerShell-Beispiel, um die PITR-Aufbewahrungsdauer von Sicherungen für eine **einzelne gelöschte** SQL Managed Instance-Datenbank zu ändern.
 
```powershell
# SET new PITR backup retention on an individual deleted database
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database.
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

Verwenden Sie das folgende PowerShell-Beispiel, um die PITR-Aufbewahrungsdauer von Sicherungen für **alle gelöschten** SQL Managed Instance-Datenbanken zu ändern.

```powershell
# SET new PITR backup retention for ALL deleted databases
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

Eine Aufbewahrungsdauer von null (0) Tagen bedeutet, dass die Sicherung sofort gelöscht wird und für eine gelöschte Datenbank nicht aufbewahrt wird.
Nachdem die PITR-Aufbewahrungsdauer von Sicherungen für eine gelöschte Datenbank reduziert wurde, kann sie nicht mehr erhöht werden.

---

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>Ändern der PITR-Aufbewahrungsdauer über die REST-API

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

Weitere Informationen finden Sie unter [REST-API für die Aufbewahrung von Sicherungen](/rest/api/sql/backupshorttermretentionpolicies).

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

Weitere Informationen finden Sie unter [REST-API für die Aufbewahrung von Sicherungen](/rest/api/sql/backupshorttermretentionpolicies).

## <a name="configure-backup-storage-redundancy"></a>Konfigurieren der Redundanz für Sicherungsspeicher

> [!NOTE]
> Konfigurierbare Speicherredundanz für Sicherungen steht nur für SQL Managed Instance zur Verfügung und kann nur während des Prozesses zum Erstellen einer verwalteten Instanz angegeben werden. Nachdem die Ressource bereitgestellt wurde, können Sie die Option für die Redundanz für Sicherungsspeicher nicht mehr ändern. Für SQL-Datenbank ist die Public Preview dieses Features aktuell nur in „Brasilien, Süden“ und die allgemein verfügbare Version nur in der Azure-Region „Asien, Südosten“ verfügbar. 

Eine Redundanz für Sicherungsspeicher für eine verwaltete Instanz kann nur während der Instanzerstellung festgelegt werden. Für eine SQL-Datenbank-Instanz kann sie beim Erstellen der Datenbank festgelegt oder für eine vorhandene Datenbank aktualisiert werden. Standardmäßig wird georedundanter Speicher verwendet. Informationen zu den Preisunterschieden zwischen lokal redundantem, zonenredundantem und georedundantem Sicherungsspeicher finden Sie auf der [Seite mit der Preisübersicht für verwaltete Instanzen](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

### <a name="configure-backup-storage-redundancy-by-using-the-azure-portal"></a>Konfigurieren der Redundanz für Sicherungsspeicher über das Azure-Portal

#### <a name="sql-database"></a>[SQL-Datenbank](#tab/single-database)

Im Azure-Portal können Sie die Sicherungsspeicherredundanz auf dem Blatt **SQL-Datenbank erstellen** konfigurieren. Die Option steht im Abschnitt „Redundanz für Sicherungsspeicher“ zur Verfügung. 
![Öffnen des Blatts „SQL-Datenbank erstellen“](./media/automated-backups-overview/sql-database-backup-storage-redundancy.png)

#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

Im Azure-Portal befindet sich die Option zum Ändern der Redundanz für Sicherungsspeicher auf dem Blatt **Compute + Speicher**. Sie können darauf beim Erstellen Ihrer SQL Managed Instance über die Option **Verwaltete Instanz konfigurieren** auf der Registerkarte **Grundlagen** zugreifen.
![Öffnen Sie das Konfigurationsblatt „Compute + Speicher“](./media/automated-backups-overview/open-configuration-blade-managedinstance.png).

Suchen Sie auf dem Blatt **Compute + Speicher** die Option zum Auswählen von Redundanz für Sicherungsspeicher.
![Konfigurieren der Redundanz für Sicherungsspeicher](./media/automated-backups-overview/select-backup-storage-redundancy-managedinstance.png)

---

### <a name="configure-backup-storage-redundancy-by-using-powershell"></a>Konfigurieren der Redundanz für Sicherungsspeicher mithilfe von PowerShell

#### <a name="sql-database"></a>[SQL-Datenbank](#tab/single-database)

Zum Konfigurieren der Redundanz für Sicherungsspeicher beim Erstellen einer neuen Datenbank können Sie den Parameter „-BackupStoageRedundancy“ angeben. Mögliche Werte sind Geo, Zone und Local. Standardmäßig verwenden alle SQL-Datenbank-Instanzen georedundanten Speicher für Sicherungen. Die Geowiederherstellung ist deaktiviert, wenn eine Datenbank mit lokalem oder zonenredundanten Sicherungsspeicher erstellt wird. 

```powershell
# Create a new database with geo-redundant backup storage.  
New-AzSqlDatabase -ResourceGroupName "ResourceGroup01" -ServerName "Server01" -DatabaseName "Database03" -Edition "GeneralPurpose" -Vcore 2 -ComputeGeneration "Gen5" -BackupStorageRedundancy Geo
```

Details hierzu finden Sie unter [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase).

Zum Aktualisieren der Redundanz für Datenspeicher einer vorhandenen Datenbank können Sie den Parameter „-BackupStorageRedundancy“ verwenden. Mögliche Werte sind Geo, Zone und Local.
Beachten Sie, dass es bis zu 48 Stunden dauern kann, bis die Änderungen auf die Datenbank angewendet werden. Der Wechsel von georedundantem Sicherungsspeicher zu lokalem oder zonenredundantem Sicherungsspeicher deaktiviert die Geowiederherstellung. 

```powershell
# Change the backup storage redundancy for Database01 to zone-redundant. 
Set-AzSqlDatabase -ResourceGroupName "ResourceGroup01" -DatabaseName "Database01" -ServerName "Server01" -BackupStorageRedundancy Zone
```

Details hierzu finden Sie unter [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase).

> [!NOTE]
> Zur Verwendung des -BackupStorageRedundancy-Parameters mit Datenwiederherstellung, Datenbankkopien oder Vorgängen zur Erstellung sekundärer Replikate verwenden Sie die Azure PowerShell-Version Az.Sql 2.11.0. 


#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

Zum Konfigurieren der Redundanz für Sicherungsspeicher während der Erstellung einer verwalteten Instanz können Sie den -BackupStorageRedundancy-Parameter angeben. Mögliche Werte sind Geo, Zone und Local.

```powershell
New-AzSqlInstance -Name managedInstance2 -ResourceGroupName ResourceGroup01 -Location westcentralus -AdministratorCredential (Get-Credential) -SubnetId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/resourcegroup01/providers/Microsoft.Network/virtualNetworks/vnet_name/subnets/subnet_name" -LicenseType LicenseIncluded -StorageSizeInGB 1024 -VCore 16 -Edition "GeneralPurpose" -ComputeGeneration Gen4 -BackupStorageRedundancy Geo
```

Details hierzu finden Sie unter [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance).

---

## <a name="use-azure-policy-to-enforce-backup-storage-redundancy"></a>Verwenden von Azure Policy zum Erzwingen der Redundanz für Sicherungsspeicher

Wenn Sie Data-Residency-Anforderungen haben, laut denen Sie Ihre Daten alle in einer einzelnen Azure-Region speichern müssen, sollten Sie zonenredundante oder lokal redundante Sicherungen für Ihre SQL-Datenbank-Instanz oder SQL Managed Instance-Instanz mithilfe von Azure Policy erzwingen. Azure Policy ist ein Dienst, mit dem Sie Richtlinien zum Anwenden von Regeln auf Azure-Ressourcen erstellen, zuweisen und verwalten können. Azure Policy hilft Ihnen, die Konformität dieser Ressourcen mit Ihren Unternehmensstandards und Vereinbarungen zum Servicelevel sicherzustellen. Weitere Informationen finden Sie in der [Übersicht über Azure Policy](../../governance/policy/overview.md). 

### <a name="built-in-backup-storage-redundancy-policies"></a>Integrierte Richtlinien für die Redundanz des Sicherungsspeichers 

Im Folgenden werden neue integrierte Richtlinien hinzugefügt, die auf Ebene des Abonnements oder der Ressourcengruppe zugewiesen werden können, um das Erstellen neuer Datenbanken oder Instanzen mit georedundantem Sicherungsspeicher zu verhindern. 

[Verwendung von GRS-Sicherungsredundanz für SQL-Datenbank vermeiden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb219b9cf-f672-4f96-9ab0-f5a3ac5e1c13)

[Verwendung von GRS-Sicherungsredundanz für SQL Managed Instance-Instanzen vermeiden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9934fd7-29f2-4e6d-ab3d-607ea38e9079)

Eine vollständige Liste integrierter Richtliniendefinitionen für SQL-Datenbank und SQL Managed Instance finden Sie [hier](./policy-reference.md).

Wenn Sie Data-Residency-Anforderungen auf Organisationsebene erzwingen möchten, können diese Richtlinien einem Abonnement zugewiesen werden. Nachdem diese einer Abonnementebene zugewiesen wurden, können Benutzer im jeweiligem Abonnement keine Datenbank oder verwaltete Instanz mit georedundantem Sicherungsspeicher mehr über das Azure-Portal oder Azure PowerShell erstellen. 

> [!IMPORTANT]
> Azure-Richtlinien werden nicht erzwungen, wenn Datenbanken über T-SQL erstellt werden. Wenn Sie Data Residency für das Erstellen einer Datenbank mit T-SQL erzwingen möchten, [verwenden Sie „LOCAL“ oder „ZONE“ als Eingabe für den BACKUP_STORAGE_REDUNDANCY-Parameter der CREATE DATABASE-Anweisung](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current#create-database-using-zone-redundancy-for-backups).

Unter [Schnellstart: Erstellen einer Richtlinienzuweisung zum Identifizieren nicht konformer Ressourcen](../../governance/policy/assign-policy-portal.md) bzw. [Schnellstart: Erstellen einer Richtlinienzuweisung zum Identifizieren nicht konformer Ressourcen mithilfe von Azure PowerShell](../../governance/policy/assign-policy-powershell.md) finden Sie Informationen zum Zuweisen von Richtlinien über das Azure-Portal bzw. über Azure PowerShell.


## <a name="next-steps"></a>Nächste Schritte

- Datenbanksicherungen sind ein wesentlicher Bestandteil jeder Strategie für Geschäftskontinuität und Notfallwiederherstellung, da Ihre Daten vor versehentlichen Beschädigungen und Löschungen geschützt werden. Informationen zu den anderen Geschäftskontinuitätslösungen von SQL-Datenbank finden Sie unter [Übersicht über Geschäftskontinuität mit Azure SQL-Datenbank](business-continuity-high-availability-disaster-recover-hadr-overview.md).
- Erfahren Sie mehr zum [Wiederherstellen einer Datenbank bis zu einem bestimmten Zeitpunkt mithilfe des Azure-Portals](recovery-using-backups.md).
- Erfahren Sie mehr zum [Wiederherstellen einer Datenbank bis zu einem bestimmten Zeitpunkt mit PowerShell](scripts/restore-database-powershell.md).
- Informationen zum Konfigurieren, Verwalten und Wiederherstellen aus der Langzeitaufbewahrung automatisierter Sicherungen in Azure Blob Storage im Azure-Portal finden Sie im Artikel [Verwalten der langfristigen Aufbewahrung von Sicherungen im Azure-Portal](long-term-backup-retention-configure.md).
- Informationen zum Konfigurieren, Verwalten und Wiederherstellen aus der langfristigen Aufbewahrung automatisierter Sicherungen in Azure Blob Storage mit PowerShell finden Sie unter [Verwalten der langfristigen Aufbewahrung von Sicherungen mit PowerShell](long-term-backup-retention-configure.md).
- Informationen zum Optimieren der Aufbewahrung im Sicherungsspeicher und der Kosten für Azure SQL Managed Instance finden Sie unter [Optimieren der Kosten für Sicherungsspeicher einer verwalteten Instanz](https://aka.ms/mi-backup-tuning).

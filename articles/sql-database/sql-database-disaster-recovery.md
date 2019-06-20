---
title: Notfallwiederherstellung für SQL-Datenbank | Microsoft Docs
description: Erfahren Sie, wie Sie eine Datenbank nach Störungen in einem regionalen Rechenzentrum oder dessen Ausfall mithilfe der aktiven Georeplikation oder der Geowiederherstellung von Azure SQL-Datenbank wiederherstellen.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 1e1bc92c684bf6ddbb7dc4ff0f882ad61ddeb27e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61075811"
---
# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Wiederherstellen einer Azure SQL-Datenbank oder Failover auf eine sekundäre Datenbank

Azure SQL-Datenbank bietet die folgenden Features zur Wiederherstellung nach einem Ausfall:

- [Aktive Georeplikation](sql-database-active-geo-replication.md)
- [Autofailover-Gruppen](sql-database-auto-failover-group.md)
- [Geowiederherstellung](sql-database-recovery-using-backups.md#point-in-time-restore)
- [Zonenredundante Datenbanken](sql-database-high-availability.md)

Informationen über Szenarien zum Thema Geschäftskontinuität sowie über die Funktionen, die diese Szenarien unterstützen, finden Sie unter [Geschäftskontinuität](sql-database-business-continuity.md).

> [!NOTE]
> Bei Verwendung zonenredundanter Datenbanken oder -Pools vom Typ „Premium“ oder „Unternehmenskritisch“ erfolgt der Wiederherstellungsprozess automatisch, sodass das verbleibende Material nicht relevant ist.

> [!NOTE]
> Sowohl die primäre als auch die sekundäre Datenbank müssen die gleiche Dienstebene aufweisen. Darüber hinaus wird dringend empfohlen, die sekundäre Datenbank mit der gleichen Computegröße (DTUs oder virtuelle Kerne) wie die primäre Datenbank zu erstellen. Weitere Informationen finden Sie unter [Upgrade oder Downgrade einer primären Datenbank](sql-database-active-geo-replication.md#upgrading-or-downgrading-a-primary-database).

> [!NOTE]
> Verwenden Sie mindestens eine Failovergruppe, um das Failover mehrerer Datenbanken zu verwalten.
> Wenn Sie der Failovergruppe eine vorhandene Georeplikationsbeziehung hinzufügen, stellen Sie sicher, dass die geosekundäre Datenbank mit der gleichen Dienstebene und Computegröße wie die primäre Datenbank konfiguriert ist. Weitere Informationen finden Sie unter [Verwenden von Autofailover-Gruppen für ein transparentes und koordiniertes Failover mehrerer Datenbanken](sql-database-auto-failover-group.md).

## <a name="prepare-for-the-event-of-an-outage"></a>Vorbereiten auf einen Ausfall

Um eine erfolgreiche Wiederherstellung in eine andere Datenregion mithilfe von Failovergruppen oder georedundanter Sicherungen durchzuführen, müssen Sie einen Server in einem anderen Rechenzentrum vorbereiten, um diesen bei einem Ausfall zum neuen primären Server hochstufen zu können. Darüber hinaus müssen Sie sorgfältig definierte Schritte dokumentiert und getestet haben, um eine reibungslose Wiederherstellung sicherzustellen. Folgende Vorbereitungsschritte sind erforderlich:

- Identifizieren Sie den SQL-Datenbank-Server in einer anderen Region, der der neue primäre Server werden soll. Bei der Geowiederherstellung ist dies im Allgemeinen ein Server in der [gekoppelten Region](../best-practices-availability-paired-regions.md) für die Region, in der sich Ihre Datenbank befindet. Hierdurch entfallen Kosten für zusätzlichen Datenverkehr während der Geowiederherstellungsvorgänge.
- Identifizieren Sie die IP-Firewallregeln auf Serverebene, die erforderlich sind, damit Benutzer auf die neue primäre Datenbank zugreifen können. Optional können Sie diese Regeln auch neu definieren.
- Legen Sie fest, wie Benutzer an den neuen primären Server umgeleitet werden sollen, indem Sie z.B. Verbindungszeichenfolgen oder DNS-Einträge ändern.
- Identifizieren Sie die Anmeldeinformationen, die in der Masterdatenbank auf dem neuen primären Server vorhanden sein müssen, und stellen Sie sicher, dass die entsprechenden Benutzer über die geeigneten Berechtigungen in der Masterdatenbank verfügen. Optional können Sie diese Informationen auch neu erstellen. Weitere Informationen finden Sie unter [Verwalten der Sicherheit der Azure SQL-Datenbank nach der Notfallwiederherstellung](sql-database-geo-replication-security-config.md).
- Identifizieren Sie die Warnungsregeln, die aktualisiert werden müssen, um der neuen primären Datenbank zugeordnet werden zu können.
- Dokumentieren Sie die Überwachungskonfiguration in der aktuellen primären Datenbank.
- Führen Sie ein [Notfallwiederherstellungsverfahren](sql-database-disaster-recovery-drills.md) durch. Um einen Ausfall für die Geowiederherstellung zu simulieren, können Sie die Quelldatenbank löschen oder umbenennen, um einen Fehler bei der Konnektivität der Anwendung zu verursachen. Um einen Ausfall unter Verwendung von Failovergruppen zu simulieren, können Sie die Webanwendung oder den virtuellen Computer deaktivieren, die bzw. der mit der Datenbank verbunden ist. Alternativ dazu können Sie ein Failover der Datenbank durchführen, um Fehler bei der Konnektivität der Anwendung zu verursachen.

## <a name="when-to-initiate-recovery"></a>Startzeitpunkt für die Wiederherstellung

Der Wiederherstellungsvorgang wirkt sich auf die Anwendung aus. Er erfordert eine Änderung der SQL-Verbindungszeichenfolge oder eine Umleitung über DNS und kann zu dauerhaftem Datenverlust führen. Er sollte daher nur ausgeführt werden, wenn der Ausfall wahrscheinlich länger als die Anwendungs-RTO (Recovery Time Objective)dauert. Wenn die Anwendung in der Produktionsumgebung bereitgestellt wird, sollten Sie regelmäßige Überwachungen der Anwendungsintegrität durchführen und die folgenden Datenpunkte verwenden, um zu bestätigen, dass die Wiederherstellung erforderlich ist:

1. Permanente Verbindungsfehler zwischen Anwendungsebene und Datenbank.
2. Das Azure-Portal zeigt eine Warnung zu einem Incident in der Region, der weit reichende Auswirkungen hat.

> [!NOTE]
> Wenn Sie Failovergruppen verwenden und sich für das automatische Failover entscheiden, erfolgt der Wiederherstellungsprozesses automatisch und transparent für die Anwendung.

Je nach Toleranz Ihrer Anwendung gegenüber einer Downtime und einer möglichen geschäftlichen Haftung können Sie die folgenden Wiederherstellungsoptionen in Betracht ziehen.

Verwenden Sie die Anforderung [Get Recoverable Database](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) zum Abrufen des neuesten georeplizierten Wiederherstellungspunkts.

## <a name="wait-for-service-recovery"></a>Warten auf die Dienstwiederherstellung

Die Azure-Teams arbeiten intensiv daran, die Dienstverfügbarkeit so schnell wie möglich wiederherzustellen, aber je nach Fehlerursache kann die Wiederherstellung Stunden oder Tage in Anspruch nehmen.  Wenn Ihre Anwendung eine solche Downtime tolerieren kann, können Sie einfach warten, bis der Dienst wiederhergestellt wurde. In diesem Fall ist keine weitere Aktion erforderlich. Sie können den aktuellen Dienststatus auf unserem [Dashboard zur Azure-Dienstintegrität](https://azure.microsoft.com/status/)anzeigen. Nach der Dienstwiederherstellung in Ihrer Region wird die Verfügbarkeit Ihrer Anwendung wiederhergestellt.

## <a name="fail-over-to-geo-replicated-secondary-server-in-the-failover-group"></a>Failover auf den georeplizierten sekundären Server in der Failovergruppe

Wenn eine Ausfallzeit für Ihre Anwendung zu einer geschäftlichen Haftung führen kann, sollten Sie Failovergruppen einsetzen. So kann die Anwendungsverfügbarkeit bei einem Ausfall schnell in einer anderen Region wiederhergestellt werden. Ein Tutorial finden Sie unter [Implementieren einer geografisch verteilten Datenbank](sql-database-implement-geo-distributed-database.md).

Um die Verfügbarkeit der Datenbank(en) wiederherzustellen, müssen Sie mithilfe einer der unterstützten Methoden ein Failover auf den sekundären Server initiieren.

In den folgenden Leitfäden finden Sie Informationen zum Failover auf eine georeplizierte sekundäre Datenbank:

- [Failover auf einen georeplizierten sekundären Server mit dem Azure-Portal](sql-database-geo-replication-portal.md)
- [Failover auf den sekundären Server mithilfe von PowerShell](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [Failover auf einen sekundären Server mit Transact-SQL (T-SQL)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#e-failover-to-a-geo-replication-secondary)

## <a name="recover-using-geo-restore"></a>Wiederherstellen mit Geowiederherstellung

Wenn eine Downtime Ihrer Anwendung keine geschäftliche Haftung nach sich zieht, können Sie [Geowiederherstellung](sql-database-recovery-using-backups.md) als Methode zum Wiederherstellen Ihrer Anwendungsdatenbank(en) nutzen. Hierbei wird eine Kopie der Datenbank aus der letzten georedundanten Sicherung erstellt.

## <a name="configure-your-database-after-recovery"></a>Konfigurieren der Datenbank nach der Wiederherstellung

Wenn Sie die Geowiederherstellung ausführen, um Ihre Anwendung nach einem Ausfall wiederherzustellen, müssen Sie sicherstellen, dass die Verbindung mit der neuen Datenbank ordnungsgemäß konfiguriert ist, sodass der normale Anwendungsbetrieb wieder aufgenommen werden kann. Dies ist eine Prüfliste mit Aufgaben, anhand derer Sie die wiederhergestellte Datenbank für die Produktion vorbereiten können.

### <a name="update-connection-strings"></a>Aktualisieren von Verbindungszeichenfolgen

Da Ihre wiederhergestellte Datenbank auf einem anderen Server vorliegt, müssen Sie die Verbindungszeichenfolge Ihrer Anwendung so anpassen, dass sie auf den neuen Server verweist.

Weitere Informationen zum Ändern von Verbindungszeichenfolgen finden Sie unter der entsprechenden Entwicklungssprache für Ihre [Verbindungsbibliothek](sql-database-libraries.md).

### <a name="configure-firewall-rules"></a>Konfigurieren von Firewallregeln

Sie müssen sicherstellen, dass die für den Server und die Datenbank konfigurierten Firewallregeln mit denen übereinstimmen, die für den primären Server und die primäre Datenbank konfiguriert wurden. Weitere Informationen finden Sie unter [Vorgehensweise: Konfigurieren von Firewalleinstellungen (Azure SQL-Datenbank)](sql-database-configure-firewall-settings.md).

### <a name="configure-logins-and-database-users"></a>Konfigurieren von Anmeldungen und Datenbankbenutzern

Stellen Sie sicher, dass alle von der Anwendung verwendeten Anmeldungen auf dem Server vorhanden sind, der die wiederhergestellte Datenbank hostet. Weitere Informationen finden Sie unter [Sicherheitskonfiguration für die Georeplikation](sql-database-geo-replication-security-config.md).

> [!NOTE]
> Konfigurieren und Testen Sie die Serverfirewallregeln und -anmeldungen (sowie deren Berechtigungen) während eines Notfallwiederherstellungverfahrens. Diese Objekte auf Serverebene und deren Konfiguration sind während des Ausfalls möglicherweise nicht verfügbar.

### <a name="setup-telemetry-alerts"></a>Einrichten von Telemetrie-Warnungen

Sie müssen sicherstellen, dass Ihre vorhandenen Einstellungen zu Warnungsregeln aktualisiert werden, sodass auf die wiederhergestellte Datenbank und den neuen Server verwiesen wird.

Weitere Informationen zu Datenbankwarnungsregeln finden Sie unter [Empfangen von Warnbenachrichtigungen](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) und [Nachverfolgen der Dienstintegrität](../monitoring-and-diagnostics/insights-service-health.md).

### <a name="enable-auditing"></a>Aktivieren der Überwachung

Wenn für den Zugriff auf die Datenbank Überwachung erforderlich ist, müssen Sie nach der Wiederherstellung der Datenbank die Überwachung aktivieren. Weitere Informationen finden Sie unter [Datenbanküberwachung](sql-database-auditing.md).

## <a name="next-steps"></a>Nächste Schritte

- Informationen über automatisierte Sicherungen von Azure SQL-Datenbanken finden Sie unter [automatisierter SQL-Datenbanksicherungen](sql-database-automated-backups.md)
- Informationen über Entwurfs- und Wiederherstellungsszenarien für die Geschäftskontinuität finden Sie unter [Szenarien für die Geschäftskontinuität](sql-database-business-continuity.md)
- Informationen zum Verwenden automatisierter Sicherungen für die Wiederherstellung finden Sie unter [Wiederherstellen einer Datenbank aus vom Dienst initiierten Sicherungen](sql-database-recovery-using-backups.md)

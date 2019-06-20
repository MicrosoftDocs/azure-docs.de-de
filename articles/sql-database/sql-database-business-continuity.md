---
title: Geschäftskontinuität in der Cloud – Datenbankwiederherstellung – SQL-Datenbank | Microsoft Docs
description: Erfahren Sie, wie Azure SQL-Datenbank die Geschäftskontinuität in der Cloud sowie die Datenbankwiederherstellung unterstützt und dafür sorgt, dass geschäftskritische Cloudanwendungen in Betrieb gehalten werden.
keywords: Geschäftskontinuität,Geschäftskontinuität in der Cloud,Notfallwiederherstellung von Datenbanken,Datenbankwiederherstellung
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
ms.date: 04/04/2019
ms.openlocfilehash: dfa5d4cb2d782f1466329300157a64fd17765460
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61412342"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Übersicht über die Geschäftskontinuität mit Azure SQL-Datenbank

**Geschäftskontinuität** in Azure SQL-Datenbank bezieht sich auf die Mechanismen, Richtlinien und Verfahren, die es Ihrem Unternehmen ermöglichen, angesichts von Störungen, insbesondere in der Computerinfrastruktur, weiter zu arbeiten. In den meisten Fällen behandelt Azure SQL-Datenbank die Störungen, die in der Cloudumgebung auftreten können, und hält Ihre Anwendungen und Geschäftsprozesse am Laufen. Es gibt jedoch einige Störungen, die von SQL-Datenbank nicht behandelt werden können, wie z. B.:

- Ein Benutzer hat versehentlich eine Zeile in einer Tabelle gelöscht oder aktualisiert.
- Ein bösartiger Angreifer konnte erfolgreich Daten oder eine Datenbank löschen.
- Ein Erdbeben hat einen Stromausfall verursacht und das Datencenter vorübergehend deaktiviert.

Diese Fälle lassen sich nicht mit Azure SQL-Datenbank kontrollieren, und Sie müssten die Geschäftskontinuitätsfunktionen in SQL-Datenbank verwenden, um Ihre Daten wiederherzustellen und Ihre Anwendungen am Laufen zu halten.

Diese Übersicht beschreibt die Funktionen, die Azure SQL-Datenbank für Geschäftskontinuität und Notfallwiederherstellung bereitstellt. Erfahren Sie etwas über Optionen, Empfehlungen und Tutorials für die Wiederherstellung nach Störungen, die Datenverluste nach sich ziehen oder dazu führen können, dass Ihre Datenbank und Ihre Anwendungen nicht mehr verfügbar sind. Erfahren Sie, was zu tun ist, wenn ein Benutzer- oder Anwendungsfehler die Datenintegrität gefährdet, eine Azure-Region ausfällt oder Wartungsaufgaben für Ihre Anwendung ausgeführt werden müssen.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Funktionen von SQL-Datenbank zum Sicherstellen der Geschäftskontinuität

Aus Datenbankperspektive gibt es vier große potenzielle Störungsszenarien:

- Lokale Hardware- oder Softwarefehler, die Auswirkung auf den Datenbankknoten haben, z.B. Festplattenfehler.
- Beschädigte oder gelöschte Daten – in der Regel durch einen Anwendungs- oder Benutzerfehler verursacht. Solche Fehler sind an sich anwendungsspezifisch und können in der Regel nicht automatisch von der Infrastruktur erkannt oder gemildert werden.
- Ausfall des Rechenzentrums, möglicherweise durch eine Naturkatastrophe verursacht. Dieses Szenario erfordert gewisse Georedundanz mit Anwendungsfailover in ein alternatives Datencenter.
- Upgrade- oder Wartungsfehler – unerwartete Probleme, die während geplanter Upgrades oder Wartungsarbeiten für eine Anwendung oder Datenbank auftreten, können ein schnelles Rollback zu einem früheren Datenbankzustand erfordern.

SQL-Datenbank bietet verschiedene Funktionen für die Sicherstellung der Geschäftskontinuität, einschließlich automatisierter Sicherungen und optionaler Datenbankreplikation, die Auswirkungen dieser Probleme minimieren können. Zunächst müssen Sie verstehen, wie die [Hochverfügbarkeitsarchitektur](sql-database-high-availability.md) von SQL-Datenbank bei bestimmten Störungen, die Ihre Geschäftsprozesse beeinträchtigen können, eine Verfügbarkeit und Ausfallsicherheit von 99,99 % gewährleisten kann.
Anschließend erfahren Sie mehr über die zusätzlichen Mechanismen zur Wiederherstellung nach Störungen, die von der Hochverfügbarkeitsarchitektur von SQL-Datenbank nicht bearbeitet werden, wie z. B.:

- [Temporäre Tabellen](sql-database-temporal-tables.md) ermöglichen es Ihnen, Zeilenversionen eines beliebigen Zeitpunkts wiederherzustellen.
- [Integrierte, automatisierte Sicherungen](sql-database-automated-backups.md) und die [Point-in-Time-Wiederherstellung](sql-database-recovery-using-backups.md#point-in-time-restore) ermöglichen es Ihnen, die vollständige Datenbank zu einem Zeitpunkt innerhalb der letzten 35 Tage wiederherzustellen.
- Sie können [eine gelöschte Datenbank auf den Punkt wiederherstellen](sql-database-recovery-using-backups.md#deleted-database-restore), an dem sie gelöscht wurde, sofern der **SQL-Datenbank-Server noch vorhanden ist**.
- [Langfristige Sicherungsaufbewahrung](sql-database-long-term-retention.md) ermöglicht es Ihnen, die Sicherungen bis zu 10 Jahre aufzubewahren.
- [Aktive Georeplikation](sql-database-active-geo-replication.md) ermöglicht es Ihnen, lesbare Replikate zu erstellen und bei Ausfall eines Rechenzentrums oder einem Anwendungsupgrade ein manuelles Failover auf ein beliebiges Replikat durchzuführen.
- [Autofailover-Gruppe](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) ermöglicht der Anwendung im Falle eines Rechenzentrumsausfalls eine automatische Wiederherstellung.

Jede Funktion weist unterschiedliche Eigenschaften für die geschätzte Wiederherstellungszeit (Estimated Recovery Time, ERT) sowie für mögliche Datenverluste bei kürzlich durchgeführten Transaktionen auf. Wenn Sie diese Optionen kennen, können Sie die richtigen Optionen auswählen und in den meisten Szenarien auch miteinander kombinieren. Wenn Sie Ihren Plan für die Geschäftskontinuität entwickeln, müssen Sie wissen, wie viel Zeit maximal vergehen darf, bis die Anwendung nach einer Störung vollständig wiederhergestellt ist. Die Zeit, die für die vollständige Wiederherstellung einer Anwendung erforderlich ist, wird als RTO (Recovery Time Objective) bezeichnet. Sie müssen auch herausfinden, über welchen Zeitraum kürzlich durchgeführter Datenupdates (in einem bestimmten Zeitraum) maximal verloren gehen dürfen, wenn die Anwendung nach einer Störung wiederhergestellt wird. Der Zeitraum der Updates, der verloren gehen darf, wird als RPO (Recovery Point Objective) bezeichnet.

Die folgende Tabelle vergleicht ERT und RPO für die einzelnen Dienstebenen bei den häufigsten Szenarien.

| Funktion | Basic | Standard | Premium | Allgemeiner Zweck | Unternehmenskritisch
| --- | --- | --- | --- |--- |--- |
| Point-in-Time-Wiederherstellung von Sicherung |Jeder Wiederherstellungspunkt innerhalb von 7 Tagen |Jeder Wiederherstellungspunkt innerhalb von 35 Tagen |Jeder Wiederherstellungspunkt innerhalb von 35 Tagen |Jeder Wiederherstellungspunkt innerhalb des konfigurierten Zeitraums (bis zu 35 Tage)|Jeder Wiederherstellungspunkt innerhalb des konfigurierten Zeitraums (bis zu 35 Tage)|
| Geowiederherstellung von georeplizierten Sicherungen |ERT < 12 Stunden<br> RPO < 1 Stunde |ERT < 12 Stunden<br>RPO < 1 Stunde |ERT < 12 Stunden<br>RPO < 1 Stunde |ERT < 12 Stunden<br>RPO < 1 Stunde|ERT < 12 Stunden<br>RPO < 1 Stunde|
| Autofailover-Gruppen |RTO = 1 Stunde<br>RPO < 5 Sekunden |RTO = 1 Stunde<br>RPO < 5 Sekunden |RTO = 1 Stunde<br>RPO < 5 Sekunden |RTO = 1 Stunde<br>RPO < 5 Sekunden|RTO = 1 Stunde<br>RPO < 5 Sekunden|
| Manuelles Datenbank-Failover |ERT = 30 s<br>RPO < 5 Sekunden |ERT = 30 s<br>RPO < 5 Sekunden |ERT = 30 s<br>RPO < 5 Sekunden |ERT = 30 s<br>RPO < 5 Sekunden|ERT = 30 s<br>RPO < 5 Sekunden|

> [!NOTE]
> *Manuelles Datenbank-Failover* bezieht sich auf das Failover einer einzelnen Datenbank mithilfe des [ungeplanten Modus](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities) auf seine georeplizierte sekundäre Datenbank.

## <a name="recover-a-database-to-the-existing-server"></a>Wiederherstellen einer Datenbank auf dem vorhandenen Server

SQL-Datenbank führt automatisch eine Kombination aus wöchentlichen vollständigen Datenbanksicherungen, differenziellen Datenbanksicherungen alle 12 Stunden sowie Transaktionsprotokollsicherungen im Abstand von 5-10 Minuten durch, um Ihr Unternehmen vor Datenverlusten zu schützen. Die Sicherungen werden für alle Dienstebenen 35 Tage lang in RA-GRS-Speicher gespeichert, mit Ausnahme der Dienstebene „Basic DTU“, bei der die Sicherungen 7 Tage lang gespeichert werden. Weitere Informationen finden Sie unter [Automatische Datenbanksicherungen](sql-database-automated-backups.md). Sie können eine vorhandene Datenbank mit dem Azure-Portal, PowerShell oder der REST-API aus den automatischen Sicherungen zu einem früheren Zeitpunkt als eine neue Datenbank auf demselben SQL-Datenbank-Server wiederherstellen. Weitere Informationen finden Sie unter [Point-in-Time-Wiederherstellung](sql-database-recovery-using-backups.md#point-in-time-restore).

Wenn die maximal unterstützte PITR-Aufbewahrungsdauer (Point-in-Time-Wiederherstellung) für Ihre Anwendung nicht ausreicht, können Sie sie verlängern, indem Sie eine Richtlinie für die langfristige Aufbewahrung (LTR) für die Datenbanken konfigurieren. Weitere Informationen finden Sie unter [Langfristiges Aufbewahren von Sicherungen](sql-database-long-term-retention.md).

Sie können diese automatischen Datenbanksicherungen verwenden, um eine Datenbank nach verschiedenen Störungen und Unterbrechungen wiederherzustellen, sowohl innerhalb Ihres eigenen Rechenzentrums als auch in einem anderen Rechenzentrum. Die Wiederherstellungszeit beträgt für gewöhnlich weniger als 12 Stunden. Das Wiederherstellen einer sehr großen oder aktiven Datenbank kann länger dauern. Bei automatischen Datenbanksicherungen hängt die geschätzte Wiederherstellungszeit von verschiedenen Faktoren ab, beispielsweise von der Gesamtzahl von Datenbanken, die gleichzeitig in der gleichen Region wiederhergestellt werden müssen, von der Größe der Datenbank und der Transaktionsprotokolle sowie von der Netzwerkbandbreite. Weitere Informationen zur Wiederherstellungszeit finden Sie unter [Wiederherstellungszeit für Datenbanken](sql-database-recovery-using-backups.md#recovery-time). Bei der Wiederherstellung in eine andere Datenregion ist der potenzielle Datenverlust bei Verwendung der georedundanten Speicherung auf eine Stunde begrenzt.

Verwenden Sie automatisierte Sicherungen und die [Point-in-Time-Wiederherstellung](sql-database-recovery-using-backups.md#point-in-time-restore) als Mechanismus für Geschäftskontinuität und Wiederherstellung, wenn Folgendes auf Ihre Anwendung zutrifft:

- Es handelt sich nicht um eine geschäftskritische Anwendung.
- Es ist keine bindende SLA vorhanden – eine Ausfallzeit von 24 Stunden oder länger zieht keine finanziellen Verpflichtungen nach sich.
- Daten ändern sich langsam (nur wenige Transaktionen pro Stunde), und der Verlust aller innerhalb einer Stunde erfolgten Datenänderungen ist akzeptabel.
- Die Kosten spielen eine große Rolle.

Wenn Sie eine schnellere Wiederherstellung benötigen, verwenden Sie die [aktive Georeplikation](sql-database-active-geo-replication.md) oder [Autofailover-Gruppen](sql-database-auto-failover-group.md). Wenn Sie Daten, die älter sind als 35 Tage, wiederherstellen müssen, verwenden Sie die [langfristige Aufbewahrung](sql-database-long-term-retention.md).

## <a name="recover-a-database-to-another-region"></a>Wiederherstellen einer Datenbank in einer anderen Region

Es kommt zwar sehr selten vor, aber es ist möglich, dass ein Azure-Rechenzentrum ausfällt. Ein solcher Ausfall kann den Geschäftsbetrieb einige wenige Minuten oder mehrere Stunden unterbrechen.

- Eine Möglichkeit ist, einfach zu warten, bis die Datenbank wieder online ist, wenn der Rechenzentrumsausfall behoben wurde. Dies funktioniert bei Anwendungen, bei denen die Datenbank nicht notwendigerweise online sein muss. Beispiele hierfür sind Entwicklungsprojekte oder kostenlose Testversionen, mit denen Sie nicht ständig arbeiten müssen. Wenn ein Rechenzentrum ausfällt, wissen Sie nicht, wie lange der Ausfall dauern kann, daher ist diese Option nur dann in Erwägung zu ziehen, wenn Sie Ihre Datenbank eine Zeit lang nicht benötigen.
- Eine andere Möglichkeit besteht im Wiederherstellen einer Datenbank auf einem beliebigen Server in einer beliebigen Azure-Region mit [georedundanten Datenbanksicherungen](sql-database-recovery-using-backups.md#geo-restore) (Geowiederherstellung). Die Geowiederherstellung verwendet eine georedundante Sicherung als Quelle und kann selbst dann zum Wiederherstellen einer Datenbank verwendet werden, wenn die Datenbank oder das Rechenzentrum aufgrund eines Ausfalls nicht mehr verfügbar ist.
- Sie können eine schnelle Wiederherstellung nach einem Ausfall durchführen, wenn Sie entweder eine sekundäre Geodatenbank mithilfe der [aktiven Georeplikation](sql-database-active-geo-replication.md) oder eine [Autofailover-Gruppe](sql-database-auto-failover-group.md) für Ihre Datenbank oder Datenbanken konfiguriert haben. Je nach gewählter Technologie können Sie entweder manuelles oder automatisches Failover verwenden. Während das eigentliche Failover nur wenige Sekunden dauert, benötigt der Dienst mindestens 1 Stunde, um es zu aktivieren. Diese Zeit ist erforderlich, um sicherzustellen, dass das Failover durch das Ausmaß des Ausfalls gerechtfertigt ist. Darüber hinaus kann das Failover aufgrund der asynchronen Replikation zu einem geringfügigen Datenverlust führen. Ausführliche Informationen zu RTO und RPO für das automatische Failover finden Sie in der Tabelle weiter oben in diesem Artikel.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>
> [!IMPORTANT]
> Um die aktive Georeplikation und automatische Failovergruppen verwenden zu können, müssen Sie entweder der Besitzer des Abonnements sein oder über Administratorberechtigungen in SQL Server verfügen. Zum Konfigurieren und Durchführen des Failovers können Sie das Azure-Portal, PowerShell oder die REST-API mit den Berechtigungen des Azure-Abonnements oder Transact-SQL mit SQL Server-Berechtigungen verwenden.

Verwenden Sie Autofailover-Gruppen, wenn Ihre Anwendung einen Teil der folgenden Kriterien erfüllt:

- Sie ist geschäftskritisch.
- Es ist eine Vereinbarung zum Servicelevel (SLA) vorhanden, die keine Ausfallzeit von 12 Stunden oder länger erlaubt.
- Ausfallzeiten können Kosten aufgrund finanzieller Haftung nach sich ziehen.
- Daten ändern sich sehr schnell, und ein Datenverlust über eine Stunde ist nicht akzeptabel.
- Die zusätzlichen Kosten für die Georeplikation sind niedriger als die potenziellen Kosten aufgrund der finanziellen Haftung und die damit einhergehenden Geschäftsverluste.

Die Dauer einer Wiederherstellung und die Menge an verlorenen Daten richten sich danach, wie Sie diese Features für die Geschäftskontinuität in Ihrer Anwendung einsetzen. Sie könnten z.B. je nach Anforderungen Ihrer Anwendung eine Kombination aus Datenbanksicherungen und aktiver Georeplikation verwenden. Überlegungen zum Anwendungsentwurf für Einzeldatenbanken und zum Einsatz von Pools für elastische Datenbanken mit diesen Funktionen für die Geschäftskontinuität finden Sie unter [Entwerfen einer Anwendung für die cloudbasierte Notfallwiederherstellung](sql-database-designing-cloud-solutions-for-disaster-recovery.md) und [Strategien für die Notfallwiederherstellung mit Pools für elastische Datenbanken](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).


Die nachstehenden Abschnitte bieten eine Übersicht über die Schritte, die zur Wiederherstellung mithilfe von Datenbanksicherungen oder der aktiven Georeplikation erforderlich sind. Weitere Schritte einschließlich der Planungsanforderungen und Schritte nach der Wiederherstellung sowie Informationen zum Simulieren eines Ausfalls, um eine Strategie für die Notfallwiederherstellung zu entwickeln, finden Sie unter [Notfallwiederherstellung für SQL-Datenbank](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Vorbereiten auf einen Ausfall

Unabhängig von der Funktion für die Geschäftskontinuität, die Sie einsetzen werden, müssen Sie folgende Aktivitäten ausführen:

- Ermitteln und Vorbereiten des Zielservers, einschließlich IP-Firewallregeln auf Serverebene, Anmeldedaten und Berechtigungen auf Ebene der Masterdatenbank.
- Bestimmen, wie Clients und Clientanwendungen an den neuen Server umgeleitet werden sollen.
- Dokumentieren weiterer Abhängigkeiten wie z.B. Überwachungseinstellungen und Warnungen.

Wenn die Vorbereitung nicht sorgfältig durchgeführt wird, dauert es länger, Anwendungen nach einem Failover oder einer Datenbank-Wiederherstellung wieder online zu schalten. Zudem werden voraussichtlich weitere Problembehandlungsmaßnahmen zu einem Zeitpunkt erforderlich sein, zu dem Sie bereits unter Stress stehen – eine ungünstige Kombination.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Failover auf eine georeplizierte sekundäre Datenbank

Wenn Sie aktive Georeplikation oder automatische Failovergruppen als Wiederherstellungsmechanismus verwenden, können Sie eine Richtlinie für automatisches Failover konfigurieren oder [ manuelles ungeplantes Failover](sql-database-active-geo-replication-portal.md#initiate-a-failover) verwenden. Sobald das Failover initiiert wurde, bewirkt es, dass die sekundäre Datenbank zur neuen primären Datenbank wird und neue Transaktionen aufzeichnen sowie auf Abfragen antworten kann. Hierbei entsteht ein minimaler Datenverlust für noch nicht replizierte Daten. Informationen zum Entwerfen des Failoverprozesses finden Sie unter [Entwerfen einer Anwendung für die cloudbasierte Notfallwiederherstellung](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Wenn das Rechenzentrum wieder online geschaltet wird, werden die alten primären Datenbanken automatisch wieder mit der neuen primären Datenbank verbunden und damit zu sekundären Datenbanken. Wenn Sie die primäre Datenbank wieder in den ursprünglichen Bereich verschieben müssen, können Sie manuell ein geplantes Failover initiieren (Failback).

### <a name="perform-a-geo-restore"></a>Ausführen einer Geowiederherstellung

Wenn Sie die automatischen Sicherungen mit georedundanter Speicherung (standardmäßig aktiviert) verwenden, können Sie die Datenbank mit [Geowiederherstellung](sql-database-disaster-recovery.md#recover-using-geo-restore) wiederherstellen. Die Wiederherstellung erfolgt in den meisten Fällen innerhalb von 12 Stunden. Es entsteht ein Datenverlust von bis zu einer Stunde – je nachdem, wann die letzte Protokollsicherung durchgeführt und repliziert wurde. Solange die Wiederherstellung nicht abgeschlossen ist, kann die Datenbank keine Transaktionen aufzeichnen oder auf Abfragen antworten. Beachten Sie, dass die Datenbank mit der Geowiederherstellung nur auf den letzten verfügbaren Zeitpunkt wiederhergestellt wird.

> [!NOTE]
> Wenn das Rechenzentrum wieder online ist, bevor Sie Ihre Anwendung auf die wiederhergestellte Datenbank umstellen, können Sie die Wiederherstellung abbrechen.

### <a name="perform-post-failover--recovery-tasks"></a>Ausführen von Aufgaben nach dem Failover bzw. nach der Wiederherstellung

Nach einer Wiederherstellung müssen Sie unabhängig vom Wiederherstellungsmechanismus folgende zusätzliche Aufgaben ausführen, damit Ihre Anwendungen wieder vollständig einsatzfähig sind und von Ihren Benutzern wieder in vollem Umfang verwendet werden können:

- Umleiten von Clients und Clientanwendungen an den neuen Server
- Sicherstellen, dass geeignete IP-Firewallregeln auf Serverebene vorhanden sind, damit Benutzer eine Verbindung herstellen können (oder Verwenden von [Firewallregeln auf Datenbankebene](sql-database-firewall-configure.md#manage-server-level-ip-firewall-rules-using-the-azure-portal), um entsprechende Regeln zu aktivieren).
- Sicherstellen, dass die geeigneten Anmeldeinformationen und Berechtigungen auf Ebene der Masterdatenbank vorhanden sind (oder Verwenden von [eigenständigen Benutzern](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable))
- Konfigurieren der erforderlichen Überwachung
- Konfigurieren der erforderlichen Warnungen

> [!NOTE]
> Wenn Sie eine Failovergruppe verwenden und eine Verbindung mit den Datenbanken über den Lese-/Schreiblistener herstellen, erfolgt die Umleitung nach einem Failover an die Anwendung automatisch und transparent.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Durchführen eines Anwendungsupgrades mit minimalen Ausfallzeiten

Zuweilen muss eine Anwendung aufgrund geplanter Wartungsmaßnahmen offline geschaltet werden – beispielsweise für Anwendungsupgrades. Der Artikel [Verwalten von Anwendungsupgrades](sql-database-manage-application-rolling-upgrade.md) beschreibt, wie Sie mithilfe der aktiven Georeplikation parallele Upgrades Ihrer Cloudanwendung ermöglichen und so Ausfallzeiten während Upgrades minimieren. Gleichzeitig können Sie mit dieser Funktion einen Wiederherstellungspfad bereitstellen, falls etwas schiefgeht.

## <a name="next-steps"></a>Nächste Schritte

Überlegungen zum Anwendungsentwurf für Einzeldatenbanken und Pools für elastische Datenbanken finden Sie unter [Entwerfen einer Anwendung für die cloudbasierte Notfallwiederherstellung](sql-database-designing-cloud-solutions-for-disaster-recovery.md) und [Strategien für die Notfallwiederherstellung mit Pools für elastische Datenbanken](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

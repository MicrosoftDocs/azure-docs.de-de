---
title: 'Geschäftskontinuität in der Cloud: Datenbankwiederherstellung'
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Erfahren Sie, wie Azure SQL-Datenbank und Azure SQL Managed Instance die Geschäftskontinuität in der Cloud sowie die Datenbankwiederherstellung unterstützen und dafür sorgen, dass geschäftskritische Cloudanwendungen in Betrieb gehalten werden.
keywords: Geschäftskontinuität,Geschäftskontinuität in der Cloud,Notfallwiederherstellung von Datenbanken,Datenbankwiederherstellung
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 06/25/2019
ms.openlocfilehash: eedc3dc1422d4eb6dcce80766077e8056f8509cf
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678052"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Übersicht über die Geschäftskontinuität mit Azure SQL-Datenbank
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

**Geschäftskontinuität** in Azure SQL-Datenbank und Azure SQL Managed Instance bezieht sich auf die Mechanismen, Richtlinien und Verfahren, die es Ihrem Unternehmen ermöglichen, angesichts von Störungen, insbesondere in der Computerinfrastruktur, weiter zu arbeiten. In den meisten Fällen behandeln Azure SQL-Datenbank und Azure SQL Managed Instance die Störungen, die in der Cloudumgebung auftreten können, und halten Ihre Anwendungen und Geschäftsprozesse am Laufen. Es gibt jedoch einige Störungen, die von SQL-Datenbank nicht automatisch behandelt werden können, wie z. B.:

- Ein Benutzer hat versehentlich eine Zeile in einer Tabelle gelöscht oder aktualisiert.
- Ein bösartiger Angreifer konnte erfolgreich Daten oder eine Datenbank löschen.
- Ein Erdbeben hat einen Stromausfall verursacht und das Rechenzentrum vorübergehend deaktiviert.

Diese Übersicht beschreibt die Funktionen, die Azure SQL-Datenbank und Azure SQL Managed Instance für Geschäftskontinuität und Notfallwiederherstellung bereitstellen. Erfahren Sie etwas über Optionen, Empfehlungen und Tutorials für die Wiederherstellung nach Störungen, die Datenverluste nach sich ziehen oder dazu führen können, dass Ihre Datenbank und Ihre Anwendungen nicht mehr verfügbar sind. Erfahren Sie, was zu tun ist, wenn ein Benutzer- oder Anwendungsfehler die Datenintegrität gefährdet, eine Azure-Region ausfällt oder Wartungsaufgaben für Ihre Anwendung ausgeführt werden müssen.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Funktionen von SQL-Datenbank zum Sicherstellen der Geschäftskontinuität

Aus Datenbankperspektive gibt es vier große potenzielle Störungsszenarien:

- Lokale Hardware- oder Softwarefehler, die Auswirkung auf den Datenbankknoten haben, z.B. Festplattenfehler.
- Beschädigte oder gelöschte Daten – in der Regel durch einen Anwendungs- oder Benutzerfehler verursacht. Derartige Fehler sind anwendungsspezifisch und werden normalerweise nicht vom Datenbankdienst erkannt.
- Ausfall des Rechenzentrums, möglicherweise durch eine Naturkatastrophe verursacht. Dieses Szenario erfordert gewisse Georedundanz mit Anwendungsfailover in ein alternatives Datencenter.
- Upgrade- oder Wartungsfehler – unerwartete Probleme, die während geplanter Upgrades oder Wartungsarbeiten an der Infrastruktur auftreten, können ein schnelles Rollback zu einem früheren Datenbankzustand erfordern.

Um das Risiko lokaler Hardware- und Softwarefehler zu mindern, bietet SQL-Datenbank eine [Hochverfügbarkeitsarchitektur](high-availability-sla.md), die nach diesen Fehlern eine automatische Wiederherstellung mit einer SLA von bis zu 99,995 Prozent Verfügbarkeit gewährleistet.  

Um Ihr Unternehmen vor Datenverlusten zu schützen, werden von Azure SQL-Datenbank und Azure SQL Managed Instance automatisch wöchentliche vollständige Datenbanksicherungen, alle 12 Stunden differenzielle Datenbanksicherungen und alle 5–10 Minuten Transaktionsprotokollsicherungen durchgeführt. Die Sicherungen werden für alle Dienstebenen mindestens 7 Tage im RA-GRS-Speicher vorgehalten. Alle Dienstebenen außer „Basic“ unterstützen konfigurierbare Aufbewahrungszeiträume von bis zu 35 Tagen für die Point-in-Time-Wiederherstellung.

Azure SQL-Datenbank und Azure SQL Managed Instance bieten außerdem mehrere Features für Geschäftskontinuität, mit denen sich verschiedene ungeplante Szenarien reduzieren lassen.

- [Temporäre Tabellen](../temporal-tables.md) ermöglichen es Ihnen, Zeilenversionen eines beliebigen Zeitpunkts wiederherzustellen.
- [Integrierte, automatisierte Sicherungen](automated-backups-overview.md) und die [Point-in-Time-Wiederherstellung](recovery-using-backups.md#point-in-time-restore) ermöglichen es Ihnen, die vollständige Datenbank zu einem Zeitpunkt innerhalb des konfigurierten Aufbewahrungszeitraums von bis zu 35 Tagen wiederherzustellen.
- Sie können [eine gelöschte Datenbank auf den Punkt wiederherstellen](recovery-using-backups.md#deleted-database-restore), an dem sie gelöscht wurde, sofern der **Server noch vorhanden ist** .
- [Langfristige Sicherungsaufbewahrung](long-term-retention-overview.md) ermöglicht es Ihnen, die Sicherungen bis zu 10 Jahre aufzubewahren. Dies ist die eingeschränkte öffentliche Vorschauversion für SQL Managed Instance
- Die [aktive Georeplikation](active-geo-replication-overview.md) ermöglicht es Ihnen, lesbare Replikate zu erstellen und bei Ausfall eines Rechenzentrums oder bei einem Anwendungsupgrade ein manuelles Failover auf ein beliebiges Replikat durchzuführen.
- [Autofailover-Gruppe](auto-failover-group-overview.md#terminology-and-capabilities) ermöglicht der Anwendung im Falle eines Rechenzentrumsausfalls eine automatische Wiederherstellung.

## <a name="recover-a-database-within-the-same-azure-region"></a>Wiederherstellen einer Datenbank innerhalb derselben Azure-Region

Mit automatischen Datenbanksicherungen können Sie eine Datenbank auf einen Zeitpunkt in der Vergangenheit zurücksetzen. Auf diese Weise können Sie Datenbeschädigungen aufgrund von menschlichem Versagen wiederherstellen. Mit der Point-in-Time-Wiederherstellung können Sie auf demselben Server eine neue Datenbank erstellen, die dem Zustand der Daten vor der Beschädigung entspricht. Bei den meisten Datenbanken dauern die Wiederherstellungsvorgänge weniger als 12 Stunden. Das Wiederherstellen einer sehr großen oder sehr aktiven Datenbank kann länger dauern. Weitere Informationen zur Wiederherstellungszeit finden Sie unter [Wiederherstellungszeit für Datenbanken](recovery-using-backups.md#recovery-time).

Wenn der maximal unterstützte Aufbewahrungszeitraum für die Point-in-Time-Wiederherstellung für Ihre Anwendung nicht ausreicht, können Sie ihn verlängern, indem Sie eine Richtlinie für die Langzeitaufbewahrung für die Datenbanken konfigurieren. Weitere Informationen finden Sie unter [Langfristiges Aufbewahren von Sicherungen](long-term-retention-overview.md).

## <a name="compare-geo-replication-with-failover-groups"></a>Vergleichen der Georeplikation mit Failovergruppen

[Autofailover-Gruppen](auto-failover-group-overview.md#terminology-and-capabilities) vereinfachen die Bereitstellung und die Verwendung von [Georeplikation](active-geo-replication-overview.md) und fügen die zusätzlichen Funktionen wie in der folgenden Tabelle beschrieben hinzu:

|                                              | Georeplikation | Failovergruppen  |
|:---------------------------------------------| :-------------- | :----------------|
| **Automatisches Failover**                          |     Nein          |      Ja         |
| **Gleichzeitiges Failover für mehrere Datenbanken**  |     Nein          |      Ja         |
| **Benutzer muss Verbindungszeichenfolge nach einem Failover aktualisieren**      |     Ja         |      Nein          |
| **Unterstützung für SQL Managed Instance**                   |     Nein          |      Ja         |
| **Kann sich in derselben Region wie die primäre Instanz befinden**             |     Ja         |      Nein          |
| **Mehrere Replikate**                            |     Ja         |      Nein          |
| **Unterstützung der Leseskalierung**                          |     Ja         |      Ja         |


## <a name="recover-a-database-to-the-existing-server"></a>Wiederherstellen einer Datenbank auf dem vorhandenen Server

Es kommt zwar sehr selten vor, aber es ist möglich, dass ein Azure-Rechenzentrum ausfällt. Ein solcher Ausfall kann den Geschäftsbetrieb einige wenige Minuten oder mehrere Stunden unterbrechen.

- Eine Möglichkeit ist, einfach zu warten, bis die Datenbank wieder online ist, wenn der Rechenzentrumsausfall behoben wurde. Dies funktioniert bei Anwendungen, bei denen die Datenbank nicht notwendigerweise online sein muss. Beispiele hierfür sind Entwicklungsprojekte oder kostenlose Testversionen, mit denen Sie nicht ständig arbeiten müssen. Wenn ein Rechenzentrum ausfällt, wissen Sie nicht, wie lange der Ausfall dauern kann, daher ist diese Option nur dann in Erwägung zu ziehen, wenn Sie Ihre Datenbank eine Zeit lang nicht benötigen.
- Eine andere Möglichkeit besteht im Wiederherstellen einer Datenbank auf einem beliebigen Server in einer beliebigen Azure-Region mit [georedundanten Datenbanksicherungen](recovery-using-backups.md#geo-restore) (Geowiederherstellung). Die Geowiederherstellung verwendet eine georedundante Sicherung als Quelle und kann selbst dann zum Wiederherstellen einer Datenbank verwendet werden, wenn die Datenbank oder das Rechenzentrum aufgrund eines Ausfalls nicht mehr verfügbar ist.
- Sie können eine schnelle Wiederherstellung nach einem Ausfall durchführen, wenn Sie entweder eine sekundäre Geodatenbank mithilfe der [aktiven Georeplikation](active-geo-replication-overview.md) oder eine [Autofailover-Gruppe](auto-failover-group-overview.md) für Ihre Datenbank oder Datenbanken konfiguriert haben. Je nach gewählter Technologie können Sie entweder manuelles oder automatisches Failover verwenden. Während das eigentliche Failover nur wenige Sekunden dauert, benötigt der Dienst mindestens 1 Stunde, um es zu aktivieren. Diese Zeit ist erforderlich, um sicherzustellen, dass das Failover durch das Ausmaß des Ausfalls gerechtfertigt ist. Darüber hinaus kann das Failover aufgrund der asynchronen Replikation zu einem geringfügigen Datenverlust führen.

Wenn Sie Ihren Plan für die Geschäftskontinuität entwickeln, müssen Sie wissen, wie viel Zeit maximal vergehen darf, bis die Anwendung nach einer Störung vollständig wiederhergestellt ist. Die Zeit, die für die vollständige Wiederherstellung einer Anwendung erforderlich ist, wird als RTO (Recovery Time Objective) bezeichnet. Sie müssen auch herausfinden, über welchen Zeitraum kürzlich durchgeführter Datenupdates (in einem bestimmten Zeitraum) maximal verloren gehen dürfen, wenn die Anwendung nach einer unvorhergesehenen Störung wiederhergestellt wird. Der potenzielle Datenverlust wird als RPO (Recovery Point Objective) bezeichnet.

Andere Wiederherstellungsmethoden bieten andere RPO- und RTO-Ebenen. Sie können eine bestimmte Wiederherstellungsmethode auswählen oder verschiedene Methoden kombinieren, um Anwendungen vollständig wiederherzustellen. In der folgenden Tabelle werden die RPO- und RTO-Werte der einzelnen Wiederherstellungsoptionen verglichen. Gruppen für automatisches Failover vereinfachen die Bereitstellung und die Verwendung von Georeplikation und fügen die zusätzlichen Funktionen hinzu, wie in der folgenden Tabelle beschrieben.

| Wiederherstellungsmethode | RTO | RPO |
| --- | --- | --- |
| Geowiederherstellung von georeplizierten Sicherungen | 12 Stunden | 1 Stunde |
| Autofailover-Gruppen | 1 Stunde | 5 s |
| Manuelles Datenbank-Failover | 30 s | 5 s |

> [!NOTE]
> *Manuelles Datenbank-Failover* bezieht sich auf das Failover einer einzelnen Datenbank mithilfe des [ungeplanten Modus](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities) auf seine georeplizierte sekundäre Datenbank.
Ausführliche Informationen zu RTO und RPO für das automatische Failover finden Sie in der Tabelle weiter oben in diesem Artikel.

Verwenden Sie Autofailover-Gruppen, wenn Ihre Anwendung einen Teil der folgenden Kriterien erfüllt:

- Sie ist geschäftskritisch.
- Es ist eine Vereinbarung zum Servicelevel (SLA) vorhanden, die keine Ausfallzeit von 12 Stunden oder länger erlaubt.
- Ausfallzeiten können Kosten aufgrund finanzieller Haftung nach sich ziehen.
- Daten ändern sich sehr schnell, und ein Datenverlust über eine Stunde ist nicht akzeptabel.
- Die zusätzlichen Kosten für die Georeplikation sind niedriger als die potenziellen Kosten aufgrund der finanziellen Haftung und die damit einhergehenden Geschäftsverluste.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

Sie könnten z. B. je nach den Anforderungen Ihrer Anwendung eine Kombination aus Datenbanksicherungen und aktiver Georeplikation verwenden. Überlegungen zum Entwurf eigenständiger Datenbanken und zum Einsatz von Pools für elastische Datenbanken mit diesen Funktionen für Geschäftskontinuität finden Sie unter [Entwerfen einer Anwendung für die cloudbasierte Notfallwiederherstellung](designing-cloud-solutions-for-disaster-recovery.md) und [Strategien für die Notfallwiederherstellung mit Pools für elastische Datenbanken](disaster-recovery-strategies-for-applications-with-elastic-pool.md).

Die nachstehenden Abschnitte bieten eine Übersicht über die Schritte, die zur Wiederherstellung mithilfe von Datenbanksicherungen oder der aktiven Georeplikation erforderlich sind. Weitere Schritte einschließlich der Planungsanforderungen und Schritte nach der Wiederherstellung sowie Informationen zum Simulieren eines Ausfalls, um eine Strategie für die Notfallwiederherstellung zu entwickeln, finden Sie unter [Wiederherstellen einer Datenbank in SQL-Datenbank nach einem Ausfall](disaster-recovery-guidance.md).

### <a name="prepare-for-an-outage"></a>Vorbereiten auf einen Ausfall

Unabhängig von der Funktion für die Geschäftskontinuität, die Sie einsetzen werden, müssen Sie folgende Aktivitäten ausführen:

- Ermitteln und Vorbereiten des Zielservers, einschließlich IP-Firewallregeln auf Serverebene, Anmeldedaten und Berechtigungen auf Ebene der Masterdatenbank.
- Bestimmen, wie Clients und Clientanwendungen an den neuen Server umgeleitet werden sollen.
- Dokumentieren weiterer Abhängigkeiten wie z.B. Überwachungseinstellungen und Warnungen.

Wenn die Vorbereitung nicht sorgfältig durchgeführt wird, dauert es länger, Anwendungen nach einem Failover oder einer Datenbank-Wiederherstellung wieder online zu schalten. Zudem werden voraussichtlich weitere Problembehandlungsmaßnahmen zu einem Zeitpunkt erforderlich sein, zu dem Sie bereits unter Stress stehen – eine ungünstige Kombination.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Failover auf eine georeplizierte sekundäre Datenbank

Wenn Sie aktive Georeplikation oder automatische Failovergruppen als Wiederherstellungsmechanismus verwenden, können Sie eine Richtlinie für automatisches Failover konfigurieren oder [ manuelles ungeplantes Failover](active-geo-replication-configure-portal.md#initiate-a-failover) verwenden. Sobald das Failover initiiert wurde, bewirkt es, dass die sekundäre Datenbank zur neuen primären Datenbank wird und neue Transaktionen aufzeichnen sowie auf Abfragen antworten kann. Hierbei entsteht ein minimaler Datenverlust für noch nicht replizierte Daten. Informationen zum Entwerfen des Failoverprozesses finden Sie unter [Entwerfen einer Anwendung für die cloudbasierte Notfallwiederherstellung](designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Wenn das Rechenzentrum wieder online geschaltet wird, werden die alten primären Datenbanken automatisch wieder mit der neuen primären Datenbank verbunden und damit zu sekundären Datenbanken. Wenn Sie die primäre Datenbank wieder in den ursprünglichen Bereich verschieben müssen, können Sie manuell ein geplantes Failover initiieren (Failback).

### <a name="perform-a-geo-restore"></a>Ausführen einer Geowiederherstellung

Wenn Sie die automatischen Sicherungen mit georedundanter Speicherung (standardmäßig aktiviert) verwenden, können Sie die Datenbank mit [Geowiederherstellung](disaster-recovery-guidance.md#recover-using-geo-restore) wiederherstellen. Die Wiederherstellung erfolgt in den meisten Fällen innerhalb von 12 Stunden. Es entsteht ein Datenverlust von bis zu einer Stunde – je nachdem, wann die letzte Protokollsicherung durchgeführt und repliziert wurde. Solange die Wiederherstellung nicht abgeschlossen ist, kann die Datenbank keine Transaktionen aufzeichnen oder auf Abfragen antworten. Beachten Sie, dass die Datenbank mit der Geowiederherstellung nur auf den letzten verfügbaren Zeitpunkt wiederhergestellt wird.

> [!NOTE]
> Wenn das Rechenzentrum wieder online ist, bevor Sie Ihre Anwendung auf die wiederhergestellte Datenbank umstellen, können Sie die Wiederherstellung abbrechen.

### <a name="perform-post-failover--recovery-tasks"></a>Ausführen von Aufgaben nach dem Failover bzw. nach der Wiederherstellung

Nach einer Wiederherstellung müssen Sie unabhängig vom Wiederherstellungsmechanismus folgende zusätzliche Aufgaben ausführen, damit Ihre Anwendungen wieder vollständig einsatzfähig sind und von Ihren Benutzern wieder in vollem Umfang verwendet werden können:

- Umleiten von Clients und Clientanwendungen an den neuen Server.
- Sicherstellen, dass geeignete IP-Firewallregeln auf Serverebene vorhanden sind, damit Benutzer eine Verbindung herstellen können (oder Verwenden von [Firewallregeln auf Datenbankebene](firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules), um entsprechende Regeln zu aktivieren).
- Sicherstellen, dass die geeigneten Anmeldeinformationen und Berechtigungen auf Ebene der Masterdatenbank vorhanden sind (oder Verwenden von [eigenständigen Benutzern](/sql/relational-databases/security/contained-database-users-making-your-database-portable)).
- Konfigurieren der erforderlichen Überwachung.
- Konfigurieren der erforderlichen Warnungen.

> [!NOTE]
> Wenn Sie eine Failovergruppe verwenden und eine Verbindung mit den Datenbanken über den Lese-/Schreiblistener herstellen, erfolgt die Umleitung nach einem Failover an die Anwendung automatisch und transparent.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Durchführen eines Anwendungsupgrades mit minimalen Ausfallzeiten

Zuweilen muss eine Anwendung aufgrund geplanter Wartungsmaßnahmen offline geschaltet werden – beispielsweise für Anwendungsupgrades. Der Artikel [Verwalten von Anwendungsupgrades](manage-application-rolling-upgrade.md) beschreibt, wie Sie mithilfe der aktiven Georeplikation parallele Upgrades Ihrer Cloudanwendung ermöglichen und so Ausfallzeiten während Upgrades minimieren. Gleichzeitig können Sie mit dieser Funktion einen Wiederherstellungspfad bereitstellen, falls etwas schiefgeht.

## <a name="next-steps"></a>Nächste Schritte

Überlegungen zum Anwendungsentwurf für einzelne Datenbanken und Pools für elastische Datenbanken finden Sie unter [Entwerfen einer Anwendung für die cloudbasierte Notfallwiederherstellung](designing-cloud-solutions-for-disaster-recovery.md) und [Strategien für die Notfallwiederherstellung mit Pools für elastische Datenbanken](disaster-recovery-strategies-for-applications-with-elastic-pool.md).
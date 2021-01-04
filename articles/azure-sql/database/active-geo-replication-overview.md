---
title: Aktive Georeplikation
description: Mit aktiver Georeplikation können Sie lesbare sekundäre Datenbanken für einzelne Datenbanken in Azure SQL-Datenbank im selben oder in einem anderen Rechenzentrum (Region) erstellen.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 08/27/2020
ms.openlocfilehash: c7a24dbe93bf0096e327804be07acc3f67d2f03b
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94985755"
---
# <a name="creating-and-using-active-geo-replication---azure-sql-database"></a>Erstellen und Verwenden der aktiven Georeplikation: Azure SQL-Datenbank
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Die aktive Georeplikation ist ein Feature in Azure SQL-Datenbank, mit der Sie lesbare sekundäre Datenbanken für einzelne Datenbanken auf einem Server im selben oder in einem anderen Rechenzentrum (Region) erstellen können.

> [!NOTE]
> Die aktive Georeplikation wird von Azure SQL Managed Instance nicht unterstützt. Für ein geografisches Failover von Instanzen von Azure SQL Managed Instance können Sie [Autofailover-Gruppen](auto-failover-group-overview.md) verwenden.

Aktive Georeplikation ist als Geschäftskontinuitätslösung konzipiert, die der Anwendung im Falle eines regionalen Notfalls oder größeren Ausfalls eine schnelle Notfallwiederherstellung einzelner Datenbanken ermöglicht. Wenn Georeplikation aktiviert ist, kann die Anwendung ein Failover auf eine sekundäre Datenbank in einer anderen Azure-Region initiieren. Bis zu vier sekundäre Datenbanken werden in derselben oder verschiedenen Regionen unterstützt, und die sekundären Datenbanken können auch für schreibgeschützten Abfragezugriff verwendet werden. Das Failover muss durch die Anwendung oder den Benutzer manuell eingeleitet werden. Nach einem Failover hat die neue primäre Datenbank einen anderen Verbindungsendpunkt.

> [!NOTE]
> Die aktive Georeplikation repliziert Änderungen durch das Transaktionsprotokoll für die Streamingdatenbank. Sie hat keinen Bezug zur [Transaktionsreplikation](/sql/relational-databases/replication/transactional/transactional-replication), bei der Änderungen durch Ausführen von DML-Befehlen (INSERT, UPDATE, DELETE) repliziert werden.

Das folgende Diagramm zeigt eine typische Konfiguration einer georedundanten Cloudanwendung mit aktiver Georeplikation.

![Aktive Georeplikation](./media/active-geo-replication-overview/geo-replication.png )

> [!IMPORTANT]
> SQL-Datenbank unterstützt auch Autofailover-Gruppen. Weitere Informationen finden Sie unter [Autofailover-Gruppen](auto-failover-group-overview.md).

Falls Ihre primäre Datenbank aus irgendeinem Grund ausfällt oder einfach offline geschaltet werden muss, können Sie ein Failover auf eine der sekundären Datenbanken initiieren. Wenn das Failover auf eine sekundäre Datenbank aktiviert ist, werden alle anderen sekundären Datenbanken automatisch mit der neuen primären Datenbank verknüpft.

Sie können Replikation und Failover für eine einzelne Datenbank oder eine Gruppe von Datenbanken auf einem Server oder in einem Pool für elastische Datenbanken mithilfe der aktiven Georeplikation verwalten. Verwenden Sie hierfür Folgendes:

- Das [Azure-Portal](active-geo-replication-configure-portal.md)
- [PowerShell: Einzelne Datenbank](scripts/setup-geodr-and-failover-database-powershell.md)
- [PowerShell: Pool für elastische Datenbanken](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
- [Transact-SQL: Einzelne Datenbank oder Pool für elastische Datenbanken](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST-API: Einzelne Datenbank](/rest/api/sql/replicationlinks)

Die Aktive Georeplikation nutzt die [Always On-Verfügbarkeitsgruppen](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) der Datenbank-Engine, um Transaktionen mit ausgeführtem Commit in der primären Datenbank asynchron mit Momentaufnahmeisolation in eine sekundäre Datenbank zu replizieren. Gruppen für automatisches Failover stellen die Gruppensemantik über der aktiven Georeplikation bereit, es wird aber der gleiche asynchrone Replikationsmechanismus verwendet. Wenngleich die sekundäre Datenbank stets ein wenig hinter der primären Datenbank zurückliegt, sind unvollständige Transaktionen bei sekundären Daten garantiert ausgeschlossen. Regionsübergreifende Redundanz ermöglicht Anwendungen die schnelle Wiederherstellung nach einem dauerhaften Ausfall eines gesamten Rechenzentrums oder von Teilen eines Rechenzentrums aufgrund von Naturkatastrophen, schwerwiegendem menschlichen Versagen oder böswilligen Handlungen. Die spezifischen RPO-Daten finden Sie unter [Übersicht über die Geschäftskontinuität mit Azure SQL-Datenbank](business-continuity-high-availability-disaster-recover-hadr-overview.md).

> [!NOTE]
> Bei einem Netzwerkfehler zwischen zwei Regionen versuchen wir alle 10 Sekunden, die Verbindung erneut herzustellen.

> [!IMPORTANT]
> Um zu gewährleisten, dass eine wichtige Änderung in der primären Datenbank vor dem Failover zur sekundären Datenbank repliziert wird, können Sie die Synchronisierung erzwingen, um sicherzustellen, dass wichtige Änderungen (z.B. Kennwortänderungen) repliziert werden. Die erzwungene Synchronisierung beeinträchtigt die Leistung, da der aufrufende Thread blockiert wird, bis alle durchgeführten Transaktionen repliziert wurden. Weitere Informationen finden Sie unter [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync). Informationen zur Überwachung der Replikationsverzögerung zwischen der primären Datenbank und der geografisch sekundären Datenbank finden Sie unter [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).

Die folgende Abbildung zeigt ein Beispiel für die Konfiguration der aktiven Georeplikation, wobei sich die primäre Datenbank in der Region „USA, Norden-Mitte“ und die sekundäre Datenbank in der Region „USA, Süden-Mitte“ befindet.

![Georeplikationsbeziehung](./media/active-geo-replication-overview/geo-replication-relationship.png)

Weil die sekundären Datenbanken lesbar sind, können sie zum Auslagern schreibgeschützter Workloads wie Berichtsaufträge verwendet werden. Bei Verwendung der aktiven Georeplikation können Sie die sekundäre Datenbank in der gleichen Region wie die primäre erstellen, aber dies steigert nicht die Stabilität der Anwendung gegenüber schwerwiegenden Ausfällen. Wenn Sie Gruppen für automatisches Failover verwenden, werden die sekundären Datenbanken immer in einer anderen Region erstellt.

Zusätzlich zur Wiederherstellung im Notfall kann aktive Georeplikation in den folgenden Szenarien verwendet werden:

- **Datenbankmigration**: Sie können die aktive Georeplikation zur Onlinemigration einer Datenbank von einem Server auf einen anderen mit minimalen Ausfallzeiten nutzen.
- **Anwendungsupgrades**: Sie können bei Anwendungsupgrades eine zusätzliche sekundäre Datenbank als Failbackkopie erstellen.

Wenn Sie echte Geschäftskontinuität erreichen möchten, ist das Bereitstellen von Datenbankredundanz zwischen Rechenzentren jedoch nur ein Teil der Lösung. Für die komplette Wiederherstellung einer Anwendung bzw. eines Diensts nach einem schwerwiegenden Fehler ist das Wiederherstellen aller Komponenten erforderlich, aus denen sich der Dienst und alle abhängigen Dienste zusammensetzen. Beispiele dieser Komponenten sind die Clientsoftware (z. B. ein Browser mit benutzerdefiniertem JavaScript), Web-Front-Ends, Speicher und DNS. Es ist wichtig, dass alle Komponenten hinsichtlich derselben Fehler gegen Ausfälle geschützt und innerhalb des RTO (Recovery Time Objective) der Anwendung wieder verfügbar sind. Daher müssen Sie alle abhängigen Dienste bestimmen und mit dem Leistungsumfang und den Funktionen vertraut sein, die sie bieten. Dann müssen Sie entsprechende Maßnahmen ergreifen, um sicherzustellen, dass Ihr Dienst während des Failovers der Dienste funktioniert, von denen er abhängig ist. Weitere Informationen zum Entwerfen von Lösungen für die Notfallwiederherstellung finden Sie unter [Entwerfen von Cloudlösungen für die Notfallwiederherstellung mithilfe der aktiven Georeplikation](designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-terminology-and-capabilities"></a>Aktive Georeplikation – Terminologie und Funktionen

- **Automatische asynchrone Replikation**

  Sie können eine sekundäre Datenbank nur erstellen, indem Sie diese einer vorhandenen Datenbank hinzufügen. Die sekundäre Datenbank kann auf einem beliebigen Server erstellt werden. Nach der Erstellung wird die sekundäre Datenbank mit den Daten aufgefüllt, die aus der primären Datenbank kopiert wurden. Dieser Prozess wird als Seeding bezeichnet. Nachdem eine sekundäre Datenbank erstellt wurde und das Seeding erfolgt ist, werden Aktualisierungen der primären Datenbank automatisch asynchron in die sekundäre Datenbank repliziert. Asynchrone Replikation bedeutet, dass für Transaktionen in der primären Datenbank ein Commit erfolgt, ehe sie in die sekundäre Datenbank repliziert werden.

- **Lesbare sekundäre Datenbanken**

  Eine Anwendung kann für schreibgeschützte Vorgänge auf eine sekundäre Datenbank zugreifen, indem sie die Sicherheitsprinzipale für den Zugriff auf die primäre Datenbank oder andere Sicherheitsprinzipale verwendet. Die sekundären Datenbanken werden im Momentaufnahme-Isolationsmodus betrieben. Auf diese Weise wird sichergestellt, dass die Replikation von Aktualisierungen der primären Datenbank (Protokollwiedergabe) nicht durch Abfragen verzögert wird, die für die sekundäre Datenbank ausgeführt werden.

> [!NOTE]
> Die Protokollwiedergabe wird in der sekundären Datenbank verzögert, wenn Schemaupdates für die primäre Datenbank vorhanden sind. In diesem Fall ist eine Schemasperre für die sekundäre Datenbank erforderlich.

> [!IMPORTANT]
> Sie können die Georeplikation verwenden, um eine sekundäre Datenbank in derselben Region wie die primäre Datenbank zu erstellen. Sie können diese sekundäre Datenbank verwenden, um einen Lastenausgleich für schreibgeschützte Workloads in derselben Region durchzuführen. Eine sekundäre Datenbank in derselben Region bietet jedoch keine zusätzliche Fehlertoleranz und ist daher kein geeignetes Failoverziel für die Notfallwiederherstellung. Es wird auch keine Garantie für die Isolation der Verfügbarkeitszone geben. Verwenden Sie die Dienstebene „Unternehmenskritisch“ oder „Premium“ mit [zonenredundanter Konfiguration](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) oder die Dienstebene „Universell“ mit [zonenredundanter Konfiguration](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview), um eine Verfügbarkeitszonenisolierung zu erreichen.
>

- **Geplantes Failover**

  Das geplante Failover schaltet die Rollen der primären und sekundären Datenbanken nach Abschluss der vollständigen Synchronisierung um. Es handelt sich um einen Onlinevorgang, der keinen Datenverlust zur Folge hat. Die für den Vorgang benötigte Zeit hängt von der Größe des Transaktionsprotokolls für die primäre Datenbank ab, die synchronisiert werden muss. Ein geplantes Failover wurde für folgenden Szenarien konzipiert: (a) zum Ausführen von DR-Drills in der Produktion, wenn ein Datenverlust nicht akzeptabel ist, (b) zum Verschieben der Datenbank in eine andere Region, (c) für die Rückkehr der Datenbank in die primäre Region, nachdem der Ausfall behoben wurde (Failback).

- **Nicht geplantes Failover**

  Beim ungeplanten oder erzwungenen Failover übernimmt die sekundäre Datenbank sofort die Rolle der primären Datenbank, ohne dass eine Synchronisierung mit der primären Datenbank stattfindet. Alle Transaktionen, die an die primäre Datenbank committet, aber nicht auf die sekundäre Datenbank repliziert werden, gehen verloren. Dieser Vorgang ist als Wiederherstellungsmethode bei Ausfällen konzipiert, wenn auf die primäre Datenbank nicht zugegriffen werden kann, die Datenbankverfügbarkeit aber schnell wiederhergestellt werden muss. Wenn die ursprüngliche primäre Datenbank wieder online ist, stellt sie automatisch wieder eine Verbindung her und wird zur neuen sekundären Datenbank. Alle nicht synchronisierten Transaktionen vor dem Failover bleiben in der Sicherungsdatei erhalten, werden jedoch nicht mit der neuen primären Datenbank synchronisiert, um Konflikte zu vermeiden. Diese Transaktionen müssen manuell mit der neuesten Version der primären Datenbank gemergt werden.

- **Mehrere lesbare sekundäre Datenbanken**

  Es können bis zu vier sekundäre Datenbanken für eine primäre Datenbank erstellt werden. Wenn es nur eine sekundäre Datenbank gibt und diese ausfällt, ist die Anwendung bis zum Erstellen einer neuen sekundären Datenbank einem höheren Risiko ausgesetzt. Wenn mehrere sekundäre Datenbanken vorhanden sind, bleibt die Anwendung auch bei Ausfall einer der sekundären Datenbanken geschützt. Die zusätzlichen sekundären Datenbanken können auch zum Aufskalieren der schreibgeschützten Workloads verwendet werden.

  > [!NOTE]
  > Wenn Sie mit der aktiven Georeplikation eine global verteilte Anwendung erstellen und schreibgeschützten Zugriff auf Daten in mehr als vier Regionen bereitstellen müssen, können Sie eine sekundäre Datenbank einer sekundären Datenbank erstellen (dieser Prozess wird als Verkettung bezeichnet). Auf diese Weise können Sie die Datenbankreplikation praktisch unbegrenzt skalieren. Darüber hinaus verkürzt die Verkettung den Mehraufwand der Replikation von der primären Datenbank. Der Nachteil besteht in der erhöhten Replikationsverzögerung in den äußersten sekundären Datenbanken.

- **Georeplikation von Datenbanken in einem Pool für elastische Datenbanken**

  Jede sekundäre Datenbank kann einzeln in einem Pool für elastische Datenbanken enthalten sein oder sich in keinem Pool befinden. Die Auswahl des Pools für jede sekundäre Datenbank erfolgt einzeln und ist nicht von der Konfiguration einer anderen sekundären Datenbank abhängig (ob primär oder sekundär). Jeder Pool für elastische Datenbanken befindet sich innerhalb einer einzelnen Region, daher können mehrere sekundäre Datenbanken in derselben Topologie einen Pool für elastische Datenbanken nie gemeinsam verwenden.

- **Benutzergesteuertes Failover und Failback**

  Eine sekundäre Datenbank kann jederzeit durch die Anwendung oder den Benutzer explizit die primäre Rolle erhalten. Bei einem echten Ausfall sollte die Option „Nicht geplant“ verwendet werden, die eine sekundäre Datenbank umgehend zu einer primären Datenbank heraufstuft. Wenn die ausgefallene primäre Datenbank wiederhergestellt ist und wieder zur Verfügung steht, kennzeichnet das System diese wiederhergestellte primäre Datenbank automatisch als sekundäre Datenbank und bringt sie auf den aktuellen Stand der neuen primären Datenbank. Aufgrund der asynchronen Natur der Replikation kann eine kleine Menge von Daten bei einem nicht geplanten Failover verloren gehen, wenn eine primäre Datenbank ausfällt, bevor die aktuellen Änderungen in die sekundäre Datenbank repliziert wurden. Wenn für eine primäre Datenbank mit mehreren sekundären Datenbanken ein Failover durchgeführt wird, konfiguriert das System automatisch die Replikationsbeziehungen neu und verknüpft die verbleibenden sekundären Datenbanken mit der soeben heraufgestuften primären Datenbank, ohne dass ein Benutzereingriff erforderlich ist. Wenn der Ausfall behoben ist, durch den das Failover verursacht wurde, kann es wünschenswert sein, die Anwendung wieder in die primäre Region zurückzuführen. Zu diesem Zweck sollte der Failoverbefehl mit der Option „Geplant“ aufgerufen werden.

## <a name="preparing-secondary-database-for-failover"></a>Vorbereiten der sekundären Datenbank für Failover

Um sicherzustellen, dass Ihre Anwendung nach einem Failover sofort auf die neue primäre Datenbank zugreifen kann, müssen Sie sicherstellen, dass die Authentifizierungsanforderungen für den sekundären Server und die Datenbank ordnungsgemäß konfiguriert sind. Weitere Informationen finden Sie unter [Verwalten der Sicherheit der Azure SQL-Datenbank nach der Notfallwiederherstellung](active-geo-replication-security-configure.md). Um die Kompatibilität nach einem Failover zu gewährleisten, stellen Sie sicher, dass die Sicherungsaufbewahrungsrichtlinie für die sekundäre Datenbank mit der der primären Datenbank übereinstimmt. Diese Einstellungen sind nicht Teil der Datenbank und werden nicht repliziert. Standardmäßig wird die sekundäre Datenbank mit einer standardmäßigen PITR-Aufbewahrungsdauer von sieben Tagen konfiguriert. Weitere Informationen finden Sie unter [Übersicht: Automatisierte SQL-Datenbanksicherungen](automated-backups-overview.md).

> [!IMPORTANT]
> Wenn Ihre Datenbank Mitglied einer Failovergruppe ist, können Sie das Failover nicht mit dem Befehl für ein Failover mit Georeplikation initiieren. Verwenden Sie den Failoverbefehl für die Gruppe. Wenn Sie ein Failover für eine einzelne Datenbank durchführen müssen, müssen Sie sie zuerst aus der Failovergruppe entfernen. Weitere Informationen finden Sie unter [Failovergruppen](auto-failover-group-overview.md).

## <a name="configuring-secondary-database"></a>Konfigurieren einer sekundären Datenbank

Sowohl die primäre als auch die sekundäre Datenbank müssen die gleiche Dienstebene aufweisen. Darüber hinaus wird dringend empfohlen, die sekundäre Datenbank mit der gleichen Sicherungsspeicherredundanz und Computegröße (DTUs oder virtuelle Kerne) wie die primäre Datenbank zu erstellen. Wenn in der primären Datenbank schreibintensive Workloads verarbeitet werden, ist eine sekundäre Datenbank mit einer geringeren Computegröße möglicherweise nicht in der Lage mitzuhalten. Dadurch wird die Wiederholungsverzögerung für die sekundäre Datenbank verursacht, sodass sie potenziell nicht verfügbar ist. Um diese Risiken zu mindern, drosselt die aktive Georeplikation die Transaktionsprotokollrate der primären Datenbank, damit die sekundären Datenbanken aufholen können.

Wenn die sekundäre Datenbank nicht angemessen konfiguriert ist, kann es außerdem passieren, dass die Anwendungsleistung nach einem Failover beeinträchtigt wird, da die neue primäre Datenbank nicht genügend Computekapazität aufweist. In diesem Fall muss das Ziel des Datenbankdiensts auf die erforderliche Stufe hochskaliert werden, was beträchtliche Zeit und Computeressourcen beanspruchen kann und ein [Hochverfügbarkeits-Failover](high-availability-sla.md) am Ende des Hochskalierungsprozesses erfordert.

Wenn Sie die sekundäre Datenbank mit einer niedrigeren Computegröße erstellen, können Sie anhand des Diagramms mit dem Protokoll-E/A-Prozentsatz im Azure-Portal gut abschätzen, welche Computegröße für die sekundäre Datenbank mindestens erforderlich ist, um die Replikationslast zu bewältigen. Wenn die Leistungsstufe der primären Datenbank beispielsweise P6 (1.000 DTU) ist und ihr Prozentsatz für Protokollschreibvorgänge 50 % beträgt, muss die Leistungsstufe der sekundären Datenbank mindestens P4 (500 DTU) sein. Verwenden Sie zum Abrufen von Protokoll-E/A-Verlaufsdaten die Sicht [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database). Verwenden Sie die Sicht [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), um die neuesten Daten zu Protokollschreibvorgängen mit höherer Granularität abzurufen, da sie kurzfristige Spitzen der Protokollierungsrate besser widerspiegeln.

Die Drosselung der Transaktionsprotokollrate für die primäre Datenbank aufgrund einer niedrigeren Computegröße in einer sekundären Datenbank wird über den Wartetyp HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO gemeldet, der in den Datenbanksichten [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) und [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) angezeigt wird.

Die Sicherungsspeicherredundanz der sekundären Datenbank ist standardmäßig identisch mit der der primären Datenbank. Sie können die sekundäre Datenbank mit einer anderen Sicherungsspeicherredundanz konfigurieren. Sicherungen werden stets in der primären Datenbank erstellt. Wird die sekundäre Datenbank mit einer anderen Sicherungsspeicherredundanz konfiguriert, gilt Folgendes: Sicherungen werden nach dem Failover beim Heraufstufen der sekundären Datenbank zur primären Datenbank gemäß der Speicherredundanz abgerechnet, die für die neue primäre Datenbank (zuvor die sekundäre Datenbank) ausgewählt wurde. 

> [!NOTE]
> Die Transaktionsprotokollrate der primären Datenbank kann aus Gründen gedrosselt werden, die nicht mit einer niedrigeren Computegröße einer sekundären Datenbank zusammenhängen. Diese Art der Drosselung kann auch auftreten, wenn die sekundäre Datenbank dieselbe Computegröße wie die primäre Datenbank oder eine höhere aufweist. Weitere Einzelheiten, einschließlich der Wartetypen für unterschiedliche Arten von Protokollratendrosselungen, finden Sie unter [Transaktionsprotokollratengovernance](resource-limits-logical-server.md#transaction-log-rate-governance).

> [!NOTE]
> Die konfigurierbare Sicherungsspeicherredundanz von Azure SQL-Datenbank ist zurzeit in der Azure-Region „Brasilien, Süden“ als öffentliche Vorschau und in „Asien, Südosten“ allgemein verfügbar. Wird die Quelldatenbank mit lokal redundanter oder zonenredundanter Sicherungsspeicherredundanz erstellt, so wird das Erstellen einer sekundären Datenbank in einer anderen Azure-Region nicht unterstützt. 

Weitere Informationen zu SQL-Datenbank-Computegrößen finden Sie im Artikel über die [SQL-Datenbank-Dienstebenen](purchasing-models.md).

## <a name="cross-subscription-geo-replication"></a>Abonnementübergreifende Georeplikation

Zum Einrichten der aktiven Georeplikation zwischen zwei Datenbanken, die verschiedenen Abonnements angehören (unabhängig davon, ob sie sich im selben Mandanten befinden oder nicht), müssen Sie die in diesem Abschnitt beschriebene spezielle Vorgehensweise befolgen.  Diese basiert auf SQL-Befehlen und erfordert folgende Schritte:

- Erstellen einer privilegierten Anmeldung auf beiden Servern
- Hinzufügen der IP-Adresse zur Zulassungsliste des Clients, der die Änderung auf beiden Servern durchführt (z. B. die IP-Adresse des Hosts, auf dem SQL Server Management Studio ausgeführt wird)

Der Client, der die Änderungen durchführt, benötigt Netzwerkzugriff auf den primären Server. Zwar muss die gleiche IP-Adresse des Clients der Zulassungsliste auf dem sekundären Server hinzugefügt werden, doch ist eine Netzwerkverbindung mit dem sekundären Server nicht unbedingt erforderlich.

### <a name="on-the-master-of-the-primary-server"></a>Auf dem Master des primären Servers

1. Fügen Sie die IP-Adresse der Zulassungsliste des Clients hinzu, der die Änderungen durchführt (weitere Informationen finden Sie unter [Konfigurieren der Firewall](firewall-configure.md)).
1. Erstellen Sie eine Anmeldung für das Einrichten der aktiven Georeplikation (und passen Sie die Anmeldeinformationen nach Bedarf an):

   ```sql
   create login geodrsetup with password = 'ComplexPassword01'
   ```

1. Erstellen Sie einen entsprechenden Benutzer, und weisen Sie ihn der Rolle „dbmanager“ zu:

   ```sql
   create user geodrsetup for login geodrsetup
   alter role dbmanager add member geodrsetup
   ```

1. Notieren Sie sich die SID der neuen Anmeldung mithilfe der folgenden Abfrage:

   ```sql
   select sid from sys.sql_logins where name = 'geodrsetup'
   ```

### <a name="on-the-source-database-on-the-primary-server"></a>In der Quelldatenbank auf dem primären Server

1. Erstellen Sie einen Benutzer für dieselbe Anmeldung:

   ```sql
   create user geodrsetup for login geodrsetup
   ```

1. Fügen Sie den Benutzer der Rolle „db_owner“ hinzu:

   ```sql
   alter role db_owner add member geodrsetup
   ```

### <a name="on-the-master-of-the-secondary-server"></a>Auf dem Master des sekundären Servers

1. Fügen Sie die IP-Clientadresse für den sekundären Server der Zulassungsliste in den Firewallregeln hinzu. Überprüfen Sie, ob exakt dieselbe IP-Clientadresse, die auf dem primären Server hinzugefügt wurde, auch auf dem sekundären Server hinzugefügt wurde. Dieser Schritt ist erforderlich, bevor Sie den Befehl ALTER DATABASE ADD SECONDARY ausführen, um die Georeplikation zu initiieren.

1. Erstellen Sie dieselbe Anmeldung wie auf dem primären Server, und verwenden Sie dabei denselben Benutzernamen, dasselbe Kennwort und dieselbe SID:

   ```sql
   create login geodrsetup with password = 'ComplexPassword01', sid=0x010600000000006400000000000000001C98F52B95D9C84BBBA8578FACE37C3E
   ```

1. Erstellen Sie einen entsprechenden Benutzer, und weisen Sie ihn der Rolle „dbmanager“ zu:

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup
   ```

### <a name="on-the-master-of-the-primary-server"></a>Auf dem Master des primären Servers

1. Melden Sie sich mit der neuen Anmeldung beim Master des primären Servers an.
1. Erstellen Sie ein sekundäres Replikat der Quelldatenbank auf dem sekundären Server (passen Sie den Datenbanknamen und Servernamen nach Bedarf an):

   ```sql
   alter database dbrep add secondary on server <servername>
   ```

Nach der anfänglichen Einrichtung können die erstellten Benutzer, Anmeldungen und Firewallregeln entfernt werden.

## <a name="keeping-credentials-and-firewall-rules-in-sync"></a>Synchronisieren von Anmeldeinformationen und Firewallregeln

Wir empfehlen die Verwendung von [IP-Firewallregeln auf Datenbankebene](firewall-configure.md) für georeplizierte Datenbanken, damit diese Regeln mit der Datenbank repliziert werden können. So wird sichergestellt, dass alle sekundären Datenbanken die gleichen IP-Firewallregeln wie die primäre Datenbank besitzen. Mit diesem Ansatz müssen Kunden auf Servern, auf denen sowohl die primäre als auch die sekundäre Datenbank gehostet wird, keine Firewallregeln mehr manuell konfigurieren und verwalten. Analog dazu wird durch die Verwendung von [eigenständigen Datenbankbenutzern](logins-create-manage.md) für den Datenzugriff sichergestellt, dass für primäre und sekundäre Datenbanken immer die gleichen Benutzeranmeldeinformationen gelten, damit bei einem Failover keine Unterbrechungen durch Benutzernamen- und Kennwortkonflikte auftreten. Durch Hinzufügen von [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) können Kunden den Benutzerzugriff sowohl für primäre als auch für sekundäre Datenbanken verwalten, sodass die Notwendigkeit der Verwaltung von Anmeldeinformationen in Datenbanken vollständig entfällt.

## <a name="upgrading-or-downgrading-primary-database"></a>Upgrade oder Downgrade einer primären Datenbank

Sie können für eine primäre Datenbank ein Upgrade oder Downgrade auf eine andere Computegröße (innerhalb der gleichen Dienstebene; nicht zwischen „Universell“ und „Unternehmenskritisch“) ausführen, ohne die Verbindung mit sekundären Datenbanken zu trennen. Bei einem Upgrade wird empfohlen, zuerst das Upgrade für die sekundäre Datenbank und anschließend das Upgrade für die primäre Datenbank auszuführen. Drehen Sie bei einem Downgrade die Reihenfolge um: Führen Sie zuerst das Downgrade für die primäre und anschließend das Downgrade für die sekundäre Datenbank aus. Wenn Sie ein Upgrade oder Downgrade der Datenbank auf eine andere Dienstebene durchführen, wird diese Empfehlung erzwungen.

> [!NOTE]
> Wenn Sie eine sekundäre Datenbank als Teil der Konfiguration der Failovergruppe erstellt haben, sollten Sie kein Downgrade der sekundären Datenbank durchführen. So wird sichergestellt, dass Ihre Datenebene nach dem Aktivieren des Failovers ausreichende Kapazität zum Verarbeiten des normalen Workloads hat.

> [!IMPORTANT]
> Die primäre Datenbank in einer Failovergruppe kann nur auf eine höhere Ebene skaliert werden, wenn zuerst die sekundäre Datenbank auf die höhere Ebene skaliert wird. Wenn Sie versuchen, die primäre Datenbank vor der sekundären Datenbank zu skalieren, wird ggf. der folgende Fehler angezeigt:
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>Verhindern des Verlusts wichtiger Daten

Aufgrund der hohen Latenz von WANs wird für die fortlaufende Kopie ein asynchroner Replikationsmechanismus verwendet. Asynchrone Replikation macht Datenverlust unvermeidlich, sobald ein Ausfall auftritt. Bei einigen Anwendung dürfen jedoch ggf. keine Daten verloren gehen. Um diese kritischen Aktualisierungen zu schützen, kann ein Anwendungsentwickler die Systemprozedur [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) aufrufen, unmittelbar nachdem der Commit für die Transaktion erfolgt ist. Das Aufrufen von **sp_wait_for_database_copy_sync** blockiert den aufrufenden Thread so lange, bis die letzte Transaktion mit erfolgtem Commit in die sekundäre Datenbank übertragen wurde. Es wird jedoch nicht abgewartet, bis die übertragenen Transaktionen wiedergegeben und in der sekundären Datenbank committet werden. **sp_wait_for_database_copy_sync** ist auf eine bestimmte fortlaufende Kopierverknüpfung begrenzt. Jeder Benutzer mit den Rechten zum Herstellen der Verbindung mit der primären Datenbank kann diese Prozedur aufrufen.

> [!NOTE]
> **sp_wait_for_database_copy_sync** verhindert Datenverlust nach einem Failover, garantiert aber nicht die vollständige Synchronisierung für den Lesezugriff. Die vom Aufruf der Prozedur **sp_wait_for_database_copy_sync** verursachte Verzögerung kann signifikant sein und hängt von der Größe des Transaktionsprotokolls zum Zeitpunkt des Aufrufs ab.

## <a name="monitoring-geo-replication-lag"></a>Überwachen der Verzögerung bei der Georeplikation

Verwenden Sie zum Überwachen der Verzögerung in Bezug auf RPO in der primären Datenbank die Spalte *replication_lag_sec* von [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database). Die Verzögerung in Sekunden zwischen den Transaktionen, die in der primären Datenbank committet und in der sekundären Datenbank gespeichert werden, wird angezeigt. Beispiel: Falls der Wert der Verzögerung 1 Sekunde beträgt, bedeutet dies Folgendes: Wenn die primäre Datenbank in diesem Moment von einem Ausfall betroffen ist und ein Failover initiiert wird, wird 1 Sekunde der letzten Transaktionen nicht gespeichert.

Vergleichen Sie zum Messen der Verzögerung in Bezug auf Änderungen in der primären Datenbank, die auf die sekundäre Datenbank angewendet wurden (also aus dieser Datenbank gelesen werden können), den Zeitpunkt von *last_commit* in der sekundären Datenbank mit dem gleichen Wert in der primären Datenbank.

> [!NOTE]
> Es kann vorkommen, dass *replication_lag_sec* in der primären Datenbank einen NULL-Wert aufweist. Dies bedeutet, dass die primäre Datenbank derzeit nicht weiß, wie weit die sekundäre Datenbank fortgeschritten ist.   Dieser Fall tritt normalerweise ein, nachdem der Prozess neu gestartet wurde, und es sollte sich um einen vorübergehenden Zustand handeln. Erwägen Sie, in der Anwendung eine Warnung zu verwenden, wenn für *replication_lag_sec* über einen längeren Zeitraum hinweg NULL zurückgegeben wird. So wird darauf hingewiesen, dass die sekundäre Datenbank aufgrund eines dauerhaften Verbindungsausfalls nicht mit der primären Datenbank kommunizieren kann. Es können auch Bedingungen eintreten, unter denen der Unterschied zwischen dem Zeitpunkt von *last_commit* in der sekundären und primären Datenbank groß wird. Beispiel: Wenn in der primären Datenbank nach einem langen Zeitraum ohne Änderungen ein Commit erfolgt, springt der Unterschied auf einen hohen Wert, um dann schnell wieder auf 0 zu fallen. Sehen Sie es als Fehlerzustand an, wenn der Unterschied zwischen diesen beiden Werten längere Zeit groß bleibt.

## <a name="programmatically-managing-active-geo-replication"></a>Programmgesteuertes Verwalten der aktiven Georeplikation

Wie bereits zuvor erwähnt, kann die aktive Georeplikation auch programmgesteuert mit Azure PowerShell und der REST-API verwaltet werden. Die folgenden Tabellen beschreiben den verfügbaren Satz von Befehlen. Die aktive Georeplikation umfasst eine Reihe von Azure Resource Manager-APIs für die Verwaltung. Hierzu zählen unter anderem die [Azure SQL-Datenbank-REST-API](/rest/api/sql/) und [Azure PowerShell-Cmdlets](/powershell/azure/). Diese APIs erfordern die Verwendung von Ressourcengruppen und unterstützen die rollenbasierte Zugriffssteuerung (RBAC) in Azure. Weitere Informationen zur Implementierung von Zugriffsrollen finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)](../../role-based-access-control/overview.md).

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL: Verwalten des Failovers von Einzel- und Pooldatenbanken

> [!IMPORTANT]
> Diese Transact-SQL-Befehle gelten nur für die aktive Georeplikation und nicht für Failovergruppen. Daher gelten sie auch nicht für Instanzen von SQL Managed Instance, da diese nur Failovergruppen unterstützen.

| Get-Help | BESCHREIBUNG |
| --- | --- |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |Verwenden Sie das Argument ADD SECONDARY ON SERVER, um eine sekundäre Datenbank für eine vorhandene Datenbank zu erstellen und die Datenreplikation zu starten. |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |Verwenden Sie FAILOVER oder FORCE_FAILOVER_ALLOW_DATA_LOSS, um die sekundäre Datenbank zur primären zu erklären und zu ihr zu wechseln – damit starten Sie das Failover. |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |Verwenden Sie REMOVE SECONDARY ON SERVER, um die Datenreplikation zwischen einer SQL-Datenbank und der angegebenen sekundären Datenbank zu beenden. |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Gibt Informationen über alle vorhandenen Replikationsverknüpfungen für alle Datenbanken auf einem Server zurück |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Ruft den Zeitpunkt der letzten Replikation, die Verzögerung der letzten Replikation und andere Informationen über die Replikationsverknüpfung für eine angegebene Datenbank ab. |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Zeigt den Status für alle Datenbankvorgänge an, einschließlich des Status der Replikationsverknüpfungen. |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |Bewirkt, dass die Anwendung wartet, bis alle Transaktionen mit erfolgtem Commit repliziert und von der aktiven sekundären Datenbank bestätigt wurden. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>Mit PowerShell: Verwalten des Failovers von Einzel- und Pooldatenbanken

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch.

| Cmdlet | BESCHREIBUNG |
| --- | --- |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Ruft mindestens eine Datenbank ab. |
| [New-AzSqlDatabaseSecondary](/powershell/module/az.sql/new-azsqldatabasesecondary) |Erstellt eine sekundäre Datenbank für eine vorhandene Datenbank und startet die Datenreplikation. |
| [Set-AzSqlDatabaseSecondary](/powershell/module/az.sql/set-azsqldatabasesecondary) |Erklärt die sekundäre Datenbank zur primären und wechselt zu ihr – dadurch wird das Failover gestartet. |
| [Remove-AzSqlDatabaseSecondary](/powershell/module/az.sql/remove-azsqldatabasesecondary) |Beendet die Datenreplikation zwischen einer SQL-Datenbank und der angegebenen sekundären Datenbank. |
| [Get-AzSqlDatabaseReplicationLink](/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Ruft die Georeplikationsverknüpfungen zwischen einer Azure SQL-Datenbank-Instanz und einer Ressourcengruppe oder einer logischen SQL Server-Instanz ab. |
|  | |

> [!IMPORTANT]
> Beispielskripts finden Sie unter [Verwenden von PowerShell zum Konfigurieren der aktiven Georeplikation für eine einzelne Azure SQL-Datenbank](scripts/setup-geodr-and-failover-database-powershell.md) und [Verwenden von PowerShell zum Konfigurieren der aktiven Georeplikation für eine in einem Pool enthaltene Azure SQL-Datenbank](scripts/setup-geodr-and-failover-elastic-pool-powershell.md).

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>REST-API: Verwalten des Failovers von Einzel- und Pooldatenbanken

| API | BESCHREIBUNG |
| --- | --- |
| [Create or Update Database (createMode=Restore)](/rest/api/sql/databases/createorupdate) |Erstellt oder aktualisiert eine primäre oder sekundäre Datenbank oder stellt diese wieder her. |
| [Get Create or Update Database Status](/rest/api/sql/databases/createorupdate) |Ruft den Status während eines Erstellungsvorgangs ab. |
| [Set Secondary Database as Primary (Planned Failover)](/rest/api/sql/replicationlinks/failover) |Legt fest, welche sekundäre Datenbank als primäre Datenbank verwendet wird, indem ein Failover von der aktuellen primären Datenbank durchgeführt wird. **Diese Option wird für SQL Managed Instance nicht unterstützt.**|
| [Set Secondary Database as Primary (Unplanned Failover)](/rest/api/sql/replicationlinks/failoverallowdataloss) |Legt fest, welche sekundäre Datenbank als primäre Datenbank verwendet wird, indem ein Failover von der aktuellen primären Datenbank durchgeführt wird. Bei diesem Vorgang können Daten verloren gehen. **Diese Option wird für SQL Managed Instance nicht unterstützt.**|
| [Get Replication Link](/rest/api/sql/replicationlinks/get) |Ruft eine spezifische Replikationsverknüpfung für eine angegebene Datenbank in einer Georeplikationspartnerschaft ab. Es werden die Informationen abgerufen, die in der Katalogsicht „sys.geo_replication_links“ sichtbar sind. **Diese Option wird für SQL Managed Instance nicht unterstützt.**|
| [Replication Links - List By Database](/rest/api/sql/replicationlinks/listbydatabase) | Ruft alle Replikationsverknüpfungen für eine angegebene Datenbank in einer Georeplikationspartnerschaft ab. Es werden die Informationen abgerufen, die in der Katalogsicht „sys.geo_replication_links“ sichtbar sind. |
| [Delete Replication Link](/rest/api/sql/replicationlinks/delete) | Löscht einen Datenbankreplikationslink. Kann nicht während eines Failovers verwendet werden. |
|  | |

## <a name="next-steps"></a>Nächste Schritte

- Beispielskripts:
  - [Configure and failover a single database using active geo-replication](scripts/setup-geodr-and-failover-database-powershell.md) (Konfiguration und Failover einer einzelnen Datenbank mithilfe von aktiver Georeplikation)
  - [Configure and failover a pooled database using active geo-replication](scripts/setup-geodr-and-failover-elastic-pool-powershell.md) (Konfiguration und Failover einer Pooldatenbank mithilfe von aktiver Georeplikation)
- SQL-Datenbank unterstützt auch Autofailover-Gruppen. Weitere Informationen finden Sie unter [Autofailover-Gruppen](auto-failover-group-overview.md).
- Eine Übersicht und verschiedene Szenarien zum Thema Geschäftskontinuität finden Sie unter [Übersicht über die Geschäftskontinuität](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- Informationen über automatisierte Sicherungen von Azure SQL-Datenbanken finden Sie unter [Automatisierte SQL-Datenbanksicherungen](automated-backups-overview.md).
- Informationen zum Verwenden automatisierter Sicherungen für die Wiederherstellung finden Sie unter [Wiederherstellen einer Datenbank aus vom Dienst initiierten Sicherungen](recovery-using-backups.md).
- Weitere Informationen zu Authentifizierungsanforderungen für einen neuen primären Server und die Datenbank finden Sie unter [Verwalten der Sicherheit der Azure SQL-Datenbank nach der Notfallwiederherstellung](active-geo-replication-security-configure.md).

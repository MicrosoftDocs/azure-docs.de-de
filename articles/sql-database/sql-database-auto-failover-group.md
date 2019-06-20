---
title: Failovergruppen – Azure SQL-Datenbank | Microsoft-Dokumentation
description: Autofailover-Gruppen sind ein SQL-Datenbank-Feature, mit dem Sie die Replikation und das automatische/koordinierte Failover einer Gruppe von Datenbanken auf einem SQL-Datenbank-Server oder aller Datenbanken in einer verwalteten Instanz verwalten können.
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
ms.date: 05/06/2019
ms.openlocfilehash: e999e4d96dcb5a1042806c0905ce331dc0a4dc0b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65522851"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Verwenden von Autofailover-Gruppen für ein transparentes und koordiniertes Failover mehrerer Datenbanken

Autofailover-Gruppen sind ein SQL-Datenbank-Feature, mit dem Sie die Replikation und das Failover einer Gruppe von Datenbanken auf einem SQL-Datenbank-Server oder aller Datenbanken in einer verwalteten Instanz zu einer anderen Region verwalten können. Hierbei wird die gleiche zugrunde liegende Technologie wie bei der [aktiven Georeplikation](sql-database-active-geo-replication.md) verwendet. Sie können ein Failover manuell initiieren oder basierend auf einer benutzerdefinierten Richtlinie an den SQL-Datenbank-Dienst delegieren. Letzteres gibt Ihnen die Möglichkeit, nach schwerwiegenden Ausfällen oder anderen ungeplanten Ereignissen, die zum vollständigen oder teilweisen Verlust der Verfügbarkeit der Dienste von SQL-Datenbank in der primären Region führen, automatisch mehrere verwandte Datenbanken in einer sekundären Region wiederherzustellen. Außerdem können sie die lesbaren sekundären Datenbanken zur Auslagerung schreibgeschützter Abfrageworkloads verwenden. Da Gruppen für automatisches Failover mehrere Datenbanken beinhalten, müssen diese Datenbanken auf dem primären Server konfiguriert werden. Primäre und sekundäre Server für die Datenbanken in der Failovergruppe müssen sich im selben Abonnement befinden. Gruppen für automatisches Failover unterstützen die Replikation aller Datenbanken in der Gruppe auf nur einen sekundären Server in einer anderen Region.

> [!NOTE]
> Wenn Sie mit Einzel- oder Pooldatenbanken auf einem SQL-Datenbank-Server arbeiten und mehrere sekundäre Datenbanken in derselben oder in verschiedenen Regionen nutzen möchten, verwenden Sie die [aktive Georeplikation](sql-database-active-geo-replication.md).

Wenn Sie Autofailover-Gruppen mit einer Richtlinie für automatisches Failover verwenden, führt jeder Ausfall, der eine oder mehrere der Datenbanken in der Gruppe betrifft, zu einem automatischen Failover. Darüber hinaus bieten Gruppen für automatisches Failover Lese-/Schreib-Listenerendpunkte, die während eines Failovers unverändert bleiben. Sowohl bei manueller als auch automatischer Failoveraktivierung schaltet das Failover alle sekundären Datenbanken in der Gruppe zu primären um. Nach Abschluss des Datenbankfailovers wird der DNS-Eintrag automatisch aktualisiert, um die Endpunkte in die neue Region umzuleiten. Die spezifischen RPO- und RTO-Daten finden Sie unter [Übersicht über die Geschäftskontinuität mit Azure SQL-Datenbank](sql-database-business-continuity.md).

Wenn Sie Autofailover-Gruppen mit einer Richtlinie für ein automatisches Failover verwenden, führt jeder Ausfall, der Datenbanken auf dem SQL-Datenbank-Server oder in der verwalteten Instanz betrifft, zu einem automatischen Failover. Sie können Autofailover-Gruppen über folgende Komponenten verwalten:

- Das [Azure-Portal](sql-database-implement-geo-distributed-database.md)
- [PowerShell: Failovergruppe](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- [REST-API: Failovergruppe](https://docs.microsoft.com/rest/api/sql/failovergroups)

Stellen Sie nach dem Failover sicher, dass die Authentifizierungsanforderungen für Ihren Server und Ihre Datenbank auf der neuen primären konfiguriert sind. Weitere Informationen finden Sie unter [Verwalten der Sicherheit der Azure SQL-Datenbank nach der Notfallwiederherstellung](sql-database-geo-replication-security-config.md).

Wenn Sie echte Geschäftskontinuität erreichen möchten, ist das Bereitstellen von Datenbankredundanz zwischen Rechenzentren jedoch nur ein Teil der Lösung. Für die komplette Wiederherstellung einer Anwendung bzw. eines Diensts nach einem schwerwiegenden Fehler ist das Wiederherstellen aller Komponenten erforderlich, aus denen sich der Dienst und alle abhängigen Dienste zusammensetzen. Beispiele dieser Komponenten sind die Clientsoftware (z. B. ein Browser mit benutzerdefiniertem JavaScript), Web-Front-Ends, Speicher und DNS. Es ist wichtig, dass alle Komponenten hinsichtlich derselben Fehler gegen Ausfälle geschützt und innerhalb des RTO (Recovery Time Objective) der Anwendung wieder verfügbar sind. Daher müssen Sie alle abhängigen Dienste bestimmen und mit dem Leistungsumfang und den Funktionen vertraut sein, die sie bieten. Dann müssen Sie entsprechende Maßnahmen ergreifen, um sicherzustellen, dass Ihr Dienst während des Failovers der Dienste funktioniert, von denen er abhängig ist. Weitere Informationen zum Entwerfen von Lösungen für die Notfallwiederherstellung finden Sie unter [Entwerfen von Cloudlösungen für die Notfallwiederherstellung mithilfe der aktiven Georeplikation](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="auto-failover-group-terminology-and-capabilities"></a>Autofailover-Gruppen – Terminologie und Funktionen

- **Failovergruppe**

  Eine Failovergruppe ist eine Gruppe von Datenbanken, die von einem einzelnen SQL-Datenbank-Server oder innerhalb einer einzelnen verwalteten Instanz verwaltet werden. Diese Datenbanken sind eine Einheit, für die ein Failover in eine andere Region durchgeführt werden kann, falls alle oder einige primäre Datenbanken aufgrund eines Ausfalls in der primären Region nicht mehr verfügbar sind. Wenn eine Failovergruppe für verwaltete Instanzen erstellt wird, enthält sie alle Benutzerdatenbanken in der Instanz. Deshalb kann nur eine Failovergruppe auf einer Instanz konfiguriert werden.

- **SQL-Datenbank-Server**

     Bei SQL-Datenbank-Servern können einige oder alle Benutzerdatenbanken auf einem einzelnen SQL-Datenbank-Server in einer Failovergruppe platziert werden. Außerdem unterstützt ein SQL-Datenbank-Server mehrere Failovergruppen auf einem einzelnen SQL-Datenbank-Server.

- **Primärer Server/verwaltete Instanz**

  Der SQL-Datenbank-Server oder die verwaltete Instanz, auf dem bzw. der die primären Datenbanken in der Failovergruppe gehostet werden.

- **Sekundärer Server/verwaltete Instanz**

  Der SQL-Datenbank-Server oder die verwaltete Instanz, auf dem bzw. der die sekundären Datenbanken in der Failovergruppe gehostet werden. Der sekundäre Server/verwaltete Instanz kann sich nicht in der gleichen Region wie der primäre Server/verwaltete Instanz befinden.

- **Hinzufügen einzelner Datenbanken zu Failovergruppe**

  Sie können mehrere einzelne Datenbanken auf demselben SQL-Datenbank-Server in dieselbe Failovergruppe einfügen. Wenn Sie der Failovergruppe eine einzelne Datenbank hinzufügen, wird auf dem sekundären Server automatisch eine sekundäre Datenbank mit derselben Edition und Computegröße erstellt.  Sie haben diesen Server beim Erstellen der Failovergruppe angegeben. Wenn Sie eine Datenbank hinzufügen, die bereits auf dem sekundären Server eine sekundäre Datenbank hat, erbt die Gruppe diese Verknüpfung für die Georeplikation. Wenn Sie eine Datenbank hinzufügen, die bereits eine sekundäre Datenbank auf einem Server hat, der nicht Teil der Failovergruppe ist, wird eine neue sekundäre Datenbank auf dem sekundären Server erstellt.
  
  > [!IMPORTANT]
  > In einer verwalteten Instanz werden alle Benutzerdatenbanken repliziert. Sie können nicht eine Teilmenge der Benutzerdatenbanken für die Replikation in der Failovergruppe auswählen.

- **Hinzufügen von Datenbanken im Pool für elastische Datenbanken zu Failovergruppe**

  Sie können mehrere oder alle Datenbanken in einem Pool für elastische Datenbanken in dieselbe Failovergruppe einfügen. Wenn sich die primäre Datenbank in einem Pool für elastische Datenbanken befindet, wird die sekundäre Datenbank automatisch im Pool für elastische Datenbanken desselben Namens (sekundärer Pool) erstellt. Sie müssen sicherstellen, dass der sekundäre Server einen Pool für elastische Datenbanken mit genau demselben Namen und ausreichend freier Kapazität zum Hosten der sekundären Datenbanken enthält, die von der Failovergruppe erstellt werden. Wenn Sie im Pool eine Datenbank hinzufügen, die bereits im sekundären Pool eine sekundäre Datenbank hat, erbt die Gruppe diese Verknüpfung für die Georeplikation. Wenn Sie eine Datenbank hinzufügen, die bereits eine sekundäre Datenbank auf einem Server hat, der nicht Teil der Failovergruppe ist, wird eine neue sekundäre Datenbank im sekundären Pool erstellt.
  
- **DNS-Zone**

  Eine eindeutige ID, die automatisch generiert wird, wenn eine neue Instanz erstellt wird. Ein Zertifikat mit mehreren Domänen (SAN) für diese Instanz wird bereitgestellt, um die Clientverbindungen mit einer beliebigen Instanz in der gleichen DNS-Zone zu authentifizieren. Die beiden verwalteten Instanzen in der gleichen Failovergruppe müssen die DNS-Zone gemeinsam verwenden. 
  
  > [!NOTE]
  > Eine DNS-Zonen-ID ist für Failovergruppen, die für SQL-Datenbank-Server-Instanzen erstellt werden, nicht erforderlich.

- **Lese-/Schreib-Failovergruppenlistener**

  Ein DNS CNAME-Eintrag, der auf die URL der aktuellen primären Datenbank verweist. Er ermöglicht Lese-/Schreib-SQL-Anwendungen das transparente erneute Herstellen einer Verbindung mit der primären Datenbank, wenn die primäre Datenbank nach einem Failover geändert wird. Wenn die Failovergruppe auf einem SQL-Datenbank-Server erstellt wird, hat der DNS CNAME-Eintrag für die Listener-URL die Form `<fog-name>.database.windows.net`. Wenn die Failovergruppe auf einer verwalteten Instanz erstellt wird, hat der DNS CNAME-Eintrag für die Listener-URL die Form `<fog-name>.zone_id.database.windows.net`.

- **Nur-Lese-Failovergruppenlistener**

  Ein DNS CNAME-Eintrag, der auf den schreibgeschützten Listener verweist, der wiederum auf die URL der sekundären Datenbank verweist. Er ermöglicht den schreibgeschützten SQL-Anwendungen das transparente Herstellen einer Verbindung mit der sekundären Datenbank unter Verwendung der angegebenen Regeln für den Lastenausgleich. Wenn die Failovergruppe auf einem SQL-Datenbank-Server erstellt wird, hat der DNS CNAME-Eintrag für die Listener-URL die Form `<fog-name>.secondary.database.windows.net`. Wenn die Failovergruppe auf einer verwalteten Instanz erstellt wird, hat der DNS CNAME-Eintrag für die Listener-URL die Form `<fog-name>.zone_id.secondary.database.windows.net`.

- **Richtlinie für automatisches Failover**

  Standardmäßig wird eine Failovergruppe mit einer Richtlinie für automatisches Failover konfiguriert. Der SQL-Datenbank-Dienst löst das Failover aus, nachdem der Fehler erkannt und die Toleranzperiode abgelaufen ist. Das System muss sicherstellen, dass der Ausfall aufgrund des Ausmaßes der Auswirkungen nicht durch die integrierte [Hochverfügbarkeitsinfrastruktur des SQL-Datenbank-Diensts](sql-database-high-availability.md) gemildert werden kann. Wenn Sie den Failoverworkflow aus der Anwendung steuern möchten, können Sie automatisches Failover deaktivieren.

- **Schreibgeschützte Failoverrichtlinie**

  Standardmäßig ist das Failover des schreibgeschützten Listeners deaktiviert. Dadurch wird sichergestellt, dass die Leistung der primären Datenbank nicht beeinträchtigt wird, wenn die sekundäre Datenbank offline ist. Es bedeutet jedoch auch, dass die schreibgeschützten Sitzungen erst dann eine Verbindung herstellen können, nachdem die sekundäre Datenbank wiederhergestellt wurde. Wenn Sie keine Ausfallzeiten für die schreibgeschützten Sitzungen tolerieren und die primäre Datenbank vorübergehend sowohl für den schreibgeschützten als auch den Lese-/Schreibdatenverkehr auf Kosten der möglichen Leistungseinbußen der primären Datenbank verwenden möchten, können Sie das Failover für den schreibgeschützten Listener aktivieren. In diesem Fall wird der schreibgeschützte Datenverkehr automatisch zur primären Datenbank umgeleitet, wenn die sekundäre Datenbank nicht verfügbar ist.

- **Geplantes Failover**

   Beim geplanten Failover wird eine vollständige Synchronisierung zwischen primärer und sekundärer Datenbank ausgeführt, bevor die sekundäre Datenbank die Rolle der primären Datenbank übernimmt. Dadurch ist sichergestellt, dass keine Daten verloren gehen. Ein geplantes Failover wird in den folgenden Szenarien verwendet:

  - Ausführen von DR-Drills (Notfallwiederherstellung) in der Produktion, wenn ein Datenverlust nicht akzeptabel ist
  - Verschieben der Datenbanken in eine andere Region
  - Rückkehr der Datenbanken in die primäre Region, nachdem der Ausfall behoben wurde (Failback)

- **Nicht geplantes Failover**

   Beim ungeplanten oder erzwungenen Failover übernimmt die sekundäre Datenbank sofort die Rolle der primären Datenbank, ohne dass eine Synchronisierung mit der primären Datenbank stattfindet. Bei diesem Vorgang gehen Daten verloren. Ein ungeplantes Failover wird als Wiederherstellungsmethode bei Ausfällen verwendet, wenn auf die primäre Datenbank nicht zugegriffen werden kann. Wenn die ursprüngliche primäre Datenbank wieder online ist, stellt sie automatisch wieder eine Verbindung ohne Synchronisierung her und wird zur neuen sekundären Datenbank.

- **Manuelles Failover**

  Sie können ein Failover zu einem beliebigen Zeitpunkt unabhängig von der Konfiguration für automatisches Failover manuell initiieren. Wenn die Richtlinie für automatisches Failover nicht konfiguriert ist, müssen die Datenbanken in der Failovergruppe mit manuellem Failover auf dem sekundären Server wiederhergestellt werden. Sie können ein erzwungenes oder freundliches Failover (mit vollständiger Datensynchronisierung) initiieren. Mit Letzterem könnte die primäre Datenbank in die sekundäre Region verschoben werden. Nach Abschluss des Failovers werden die DNS-Einträge automatisch aktualisiert, um die Konnektivität mit der neuen primären Datenbank sicherzustellen.

- **Toleranzperiode mit Datenverlust**

  Da die primären und sekundären Datenbanken mithilfe der asynchronen Replikation synchronisiert werden, kann das Failover zu Datenverlust führen. Sie können die Richtlinie für automatisches Failover entsprechend der Toleranz Ihrer Anwendung gegenüber Datenverlust anpassen. Durch Konfigurieren von **GracePeriodWithDataLossHours** können Sie steuern, wie lange das System wartet, bevor das Failover initiiert wird, das wahrscheinlich zu Datenverlust führt.

- **Mehrere Failovergruppen**

  Sie können mehrere Failovergruppen für das gleiche Serverpaar konfigurieren, um die Skalierung der Failover zu steuern. Jede Gruppe führt ein unabhängiges Failover durch. Wenn Ihre mehrinstanzenfähige Anwendung Pools für elastische Datenbanken verwendet, können Sie diese Funktion zum Mischen primärer und sekundärer Datenbanken in den einzelnen Pools verwenden. Auf diese Weise können Sie die Auswirkungen eines Ausfalls auf nur die Hälfte der Mandanten reduzieren.

  > [!NOTE]
  > Eine verwaltete Instanz bietet keine Unterstützung für mehrere Failovergruppen.
  
## <a name="permissions"></a>Berechtigungen
Berechtigungen für eine Failovergruppe werden über die [rollenbasierte Zugriffssteuerung (RBAC)](../role-based-access-control/overview.md) verwaltet. Die Rolle [Mitwirkender von SQL Server](../role-based-access-control/built-in-roles.md#sql-server-contributor) verfügt über die erforderlichen Berechtigungen zum Verwalten von Failovergruppen. 

### <a name="create-failover-group"></a>Erstellen einer Failovergruppe
Zum Erstellen einer Failovergruppe benötigen Sie RBAC-Schreibzugriff auf den primären und die sekundären Server sowie auf alle Datenbanken in der Failovergruppe. Bei einer verwalteten Instanz benötigen Sie RBAC-Schreibzugriff auf die primäre und sekundäre verwaltete Instanz. Berechtigungen für einzelne Datenbanken sind dabei nicht relevant, da einzelne Datenbanken der verwalteten Instanz nicht zu einer Failovergruppe hinzugefügt bzw. aus einer Failovergruppe entfernt werden können. 

### <a name="update-a-failover-group"></a>Aktualisieren einer Failovergruppe
Zum Aktualisieren einer Failovergruppe benötigen Sie RBAC-Schreibzugriff auf die Failovergruppe und auf alle Datenbanken auf dem aktuellen primären Server bzw. in der aktuellen verwalteten Instanz.  

### <a name="failover-a-failover-group"></a>Failover für eine Failovergruppe
Zum Erstellen eines Failovers für eine Failovergruppe benötigen Sie RBAC-Schreibzugriff auf die Failovergruppe auf dem neuen primären Server bzw. in der neuen verwalteten Instanz. 

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>Bewährte Methoden für die Verwendung von Failovergruppen für einzelne Datenbanken und Pools für elastische Datenbanken

Die Autofailover-Gruppe muss auf dem primären SQL-Datenbank-Server konfiguriert werden und stellt eine Verbindung zum sekundären SQL-Datenbank-Server in einer anderen Azure-Region her.  Die Gruppen können alle oder einige Datenbanken auf diesen Servern umfassen. Das folgende Diagramm zeigt eine typische Konfiguration einer georedundanten Cloudanwendung mit mehreren Datenbanken und einer Autofailover-Gruppe.

![Autofailover](./media/sql-database-auto-failover-group/auto-failover-group.png)

Beim Entwerfen eines Diensts, der die Geschäftskontinuität aufrechterhalten soll, sind die folgenden allgemeinen Richtlinien zu beachten:

- **Verwenden Sie mindestens eine Failovergruppe, um das Failover mehrerer Datenbanken zu verwalten**

  Eine oder mehrere Failovergruppen können zwischen zwei Servern in verschiedenen Regionen erstellt werden (primäre und sekundäre Server). Jede Gruppe kann eine oder mehrere Datenbanken enthalten, die als Einheit wiederhergestellt werden, falls alle oder einige primäre Datenbanken aufgrund eines Ausfalls in der primären Region nicht mehr verfügbar sind. Die Failovergruppe erstellt eine geosekundäre Datenbank mit dem gleichen Dienstziel wie die primäre Datenbank. Wenn Sie der Failovergruppe eine vorhandene Georeplikationsbeziehung hinzufügen, stellen Sie sicher, dass die geosekundäre Datenbank mit der gleichen Dienstebene und Computegröße wie die primäre Datenbank konfiguriert ist.

- **Lese-/Schreib-Listener für OLTP-Workload verwenden**

  Verwenden Sie beim Durchführen von OLTP-Vorgängen `<fog-name>.database.windows.net` als Server-URL, damit die Verbindungen automatisch an die primäre Datenbank weitergeleitet werden. Diese URL wird nach dem Failover nicht geändert. Beachten Sie, dass das Failover die Aktualisierung von DNS-Einträgen einschließt, damit die Clientverbindungen erst dann an die neue primäre Datenbank weitergeleitet werden, wenn der Client-DNS-Cache aktualisiert wurde.

- **Nur-Lese-Listener für schreibgeschützte Workload verwenden**

  Wenn Sie über eine logisch isolierte schreibgeschützte Workload verfügen, die gegenüber einer bestimmten Veraltung tolerant ist, können Sie die sekundäre Datenbank in der Anwendung verwenden. Verwenden Sie für schreibgeschützte Sitzungen `<fog-name>.secondary.database.windows.net` als Server-URL, damit die Verbindung automatisch an die sekundäre Datenbank weitergeleitet wird. Es wird außerdem empfohlen, in der Verbindungszeichenfolge die Leseabsicht anzugeben. Verwenden Sie dazu **ApplicationIntent=ReadOnly**.

- **Vorbereitung auf die Beeinträchtigung der Leistung**

  Die SQL-Failoverentscheidung ist unabhängig vom Rest der Anwendung oder der anderen verwendeten Dienste. Die Anwendung kann mit einigen Komponenten in einer Region und anderen in einer anderen „vermischt“ werden. Um die Beeinträchtigung zu vermeiden, stellen Sie redundante Anwendungsbereitstellung in der DR-Region sicher, und befolgen Sie diese [Richtlinien für die Netzwerksicherheit](#failover-groups-and-network-security).

  > [!NOTE]
  > Die Anwendung in der DR-Region muss keine andere Verbindungszeichenfolge verwenden.  

- **Vorbereiten auf Datenverluste**

  Wenn ein Ausfall erkannt wird, wartet SQL für den mit **GracePeriodWithDataLossHours** festgelegten Zeitraum. Der Standardwert ist 1 Stunde. Wenn keine Datenverluste auftreten dürfen, legen Sie **GracePeriodWithDataLossHours** auf eine ausreichend große Zahl fest, z.B. 24 Stunden. Verwenden Sie manuelles Gruppenfailover für ein Failback von der sekundären zur primären Datenbank.

  > [!IMPORTANT]
  > Bei Pools für elastische Datenbanken mit 800 oder weniger DTUs und über 250 Datenbanken, die die Georeplikation verwenden, können Probleme wie längere geplante Failover und Leistungseinbußen auftreten.  Diese Probleme treten häufiger bei schreibintensiven Workloads auf, wenn Georeplikations-Endpunkte geographisch weit auseinander liegen, oder wenn mehrere sekundäre Endpunkte für jede Datenbank verwendet werden.  Ein Anzeichen für derartige Probleme ist eine steigende Verzögerung bei der Georeplikation.  Diese Verzögerung können Sie mit [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) überwachen.  Wenn diese Probleme auftreten, können Sie sie entschärfen, indem Sie z.B. die Anzahl von Pool-DTUs erhöhen oder die Anzahl von Datenbanken für die Georeplikation im gleichen Pool verringern.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Bewährte Methoden für die Verwendung von Failovergruppen für verwaltete Instanzen

Die Autofailover-Gruppe muss auf der primären Instanz konfiguriert werden und stellt eine Verbindung zur sekundären Instanz in einer anderen Azure-Region her.  Alle Datenbanken in der Instanz werden in der sekundären Instanz repliziert. Das folgende Diagramm zeigt eine typische Konfiguration einer georedundanten Cloudanwendung mit einer verwalteten Instanz und Autofailover-Gruppe.

![Autofailover](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!IMPORTANT]
> Autofailover-Gruppen für verwaltete Instanzen befinden sich in der Phase der öffentlichen Vorschau.

Wenn Ihre Anwendung die verwaltete Instanz als Datenebene verwendet, beachten Sie beim Entwerfen für Geschäftskontinuität die folgenden allgemeinen Richtlinien:

- **Erstellen der sekundären Instanz in der gleichen DNS-Zone wie die primäre Instanz**

  Um eine unterbrechungsfreie Verbindung mit der primären Instanz nach einem Failover zu gewährleisten, müssen sich primäre und sekundäre Instanz in der gleichen DNS-Zone befinden. Es wird sichergestellt, dass das gleiche Zertifikat mit mehreren Domänen (SAN) zum Authentifizieren der Clientverbindungen mit einer der zwei Instanzen in der Failovergruppe verwendet werden kann. Wenn Ihre Anwendung für die Bereitstellung in der Produktionsumgebung bereit ist, erstellen Sie eine sekundäre Instanz in einer anderen Region, und stellen Sie sicher, dass sie die gleiche DNS-Zone wie die primäre Instanz verwendet. Hierzu können Sie einen optionalen Parameter `DNS Zone Partner` über das Azure-Portal, PowerShell oder die REST-API angegeben. 

  Weitere Informationen zum Erstellen der sekundären Instanz in der gleichen DNS-Zone wie die primäre Instanz finden Sie unter [Verwalten von Failovergruppen mit verwalteten Instanzen (Vorschau)](#powershell-managing-failover-groups-with-managed-instances-preview).

- **Aktivieren des Replikationsdatenverkehrs zwischen zwei Instanzen**

  Da jede Instanz in einem eigenen VNET isoliert ist, muss der bidirektionale Datenverkehr zwischen diesen VNETs zugelassen werden. Informationen dazu finden Sie unter [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

- **Konfigurieren einer Failovergruppe zur Verwaltung des Failovers der gesamten Instanz**

  Die Failovergruppe verwaltet das Failover aller Datenbanken in der Instanz. Beim Erstellen einer Gruppe wird jede Datenbank in der Instanz automatisch in der sekundären Instanz geografisch repliziert. Sie können Failovergruppen nicht verwenden, um ein Teilfailover einer Untergruppe von Datenbanken zu initiieren.

  > [!IMPORTANT]
  > Wird eine Datenbank aus der primären Instanz entfernt, wird sie automatisch auch in der georeplizierten sekundären Instanz gelöscht.

- **Lese-/Schreib-Listener für OLTP-Workload verwenden**

  Verwenden Sie beim Durchführen von OLTP-Vorgängen `<fog-name>.zone_id.database.windows.net` als Server-URL, damit die Verbindungen automatisch an die primäre Datenbank weitergeleitet werden. Diese URL wird nach dem Failover nicht geändert. Das Failover umfasst die Aktualisierung von DNS-Einträgen, damit die Clientverbindungen erst dann an die neue primäre Datenbank weitergeleitet werden, wenn der Client-DNS-Cache aktualisiert wurde. Da die sekundäre Instanz die gleiche DNS-Zone wie die primäre Instanz verwendet, kann die Clientanwendung mit dem gleichen SAN-Zertifikat erneut eine Verbindung herstellen.

- **Direktes Verbinden mit der georeplizierten sekundären Datenbank für schreibgeschützte Abfragen**

  Wenn Sie über eine logisch isolierte schreibgeschützte Workload verfügen, die gegenüber einer bestimmten Veraltung tolerant ist, können Sie die sekundäre Datenbank in der Anwendung verwenden. Verwenden Sie zum direkten Verbinden mit der georeplizierten sekundären Datenbank `server.secondary.zone_id.database.windows.net` als Server-URL, damit die Verbindung direkt mit der georeplizierten sekundären Datenbank hergestellt wird.

  > [!NOTE]
  > Bei bestimmten Dienstebenen unterstützt Azure SQL-Datenbank die Verwendung von [schreibgeschützten Replikaten](sql-database-read-scale-out.md) für den Lastenausgleich schreibgeschützter Abfrageworkloads, wobei die Kapazität eines schreibgeschützten Replikats und der Parameter `ApplicationIntent=ReadOnly` in der Verbindungszeichenfolge verwendet werden. Wenn Sie eine georeplizierte sekundäre Datenbank konfiguriert haben, können Sie mit dieser Funktion eine Verbindung entweder mit einem schreibgeschützten Replikat am primären Standort oder am geografisch replizierten Standort herstellen.
  > - Zum Herstellen einer Verbindung mit einem schreibgeschützten Replikat am primären Standort verwenden Sie `<fog-name>.zone_id.database.windows.net`.
  > - Zum Herstellen einer Verbindung mit einem schreibgeschützten Replikat am sekundären Standort verwenden Sie `<fog-name>.secondary.zone_id.database.windows.net`.

- **Vorbereitung auf die Beeinträchtigung der Leistung**

  Die SQL-Failoverentscheidung ist unabhängig vom Rest der Anwendung oder der anderen verwendeten Dienste. Die Anwendung kann mit einigen Komponenten in einer Region und anderen in einer anderen „vermischt“ werden. Um die Beeinträchtigung zu vermeiden, stellen Sie redundante Anwendungsbereitstellung in der DR-Region sicher, und befolgen Sie diese [Richtlinien für die Netzwerksicherheit](#failover-groups-and-network-security).

- **Vorbereiten auf Datenverluste**

  Wenn ein Ausfall erkannt wird, löst SQL automatisch ein Lese-/Schreib-Failover aus, sofern nach unserem Erkenntnissen keine Daten verloren gegangen sind. Andernfalls wartet es für den mit `GracePeriodWithDataLossHours` festgelegten Zeitraum. Wenn Sie `GracePeriodWithDataLossHours` angegeben haben, müssen Sie auf Datenverluste vorbereitet sein. Bei Ausfällen priorisiert Azure grundsätzlich die Verfügbarkeit. Wenn keine Datenverluste auftreten dürfen, legen Sie „GracePeriodWithDataLossHours“ auf eine ausreichend große Zahl fest, z.B. 24 Stunden.

  Die DNS-Aktualisierung des Lese-/Schreib-Listeners erfolgt sofort nach dem Initiieren des Failovers. Bei diesem Vorgang gehen keine Daten verloren. Allerdings kann der Prozess zum Wechseln der Datenbankrollen unter normalen Bedingungen bis zu 5 Minuten dauern. Bis er abgeschlossen ist, sind einige Datenbanken in der neuen primären Instanz noch schreibgeschützt. Wenn das Failover mithilfe von PowerShell initiiert wird, ist der gesamte Vorgang synchron. Wird es über das Azure-Portal initiiert, wird auf der Benutzeroberfläche der Abschlussstatus angegeben. Bei Initiierung über die REST-API verwenden Sie den Standardabfragemechanismus von Azure Resource Manager, um den Vorgang auf seinen Abschluss zu überwachen.

  > [!IMPORTANT]
  > Verwenden Sie manuelles Gruppenfailover, um primäre Datenbanken wieder an den ursprünglichen Standort zu verschieben. Sobald der Ausfall, der das Failover verursacht hat, behoben ist, können Sie die primären Datenbanken an den ursprünglichen Standort verschieben. Dazu sollten Sie ein manuelles Failover der Gruppe initiieren.

## <a name="failover-groups-and-network-security"></a>Failovergruppen und Netzwerksicherheit

Für einige Anwendungen erfordern die Sicherheitsregeln, dass der Netzwerkzugriff auf die Datenebene auf eine bestimmte Komponente oder Komponenten wie virtueller Computer, Webdienst usw. beschränkt ist. Diese Anforderung stellt für den Entwurf der Geschäftskontinuität und die Verwendung der Failovergruppen eine Herausforderung dar. Berücksichtigen Sie bei der Implementierung eines solchen eingeschränkten Zugriffs die folgenden Optionen.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Verwendung von Failovergruppen und VNET-Regeln

Wenn Sie [Virtual Network-Dienstendpunkte und -Regeln](sql-database-vnet-service-endpoint-rule-overview.md) verwenden, um den Zugriff auf Ihre SQL-Datenbank-Instanz einzuschränken, denken Sie daran, dass jeder Virtual Network-Dienstendpunkt nur für eine einzige Azure-Region gilt. Der Endpunkt ermöglicht anderen Regionen nicht das Akzeptieren von Nachrichten aus dem Subnetz. Aus diesem Grund können nur die Clientanwendungen, die in der gleichen Region bereitgestellt werden, eine Verbindung mit der primären Datenbank herstellen. Da das Failover dazu führt, dass die SQL-Clientsitzungen zu dem Server in der anderen (sekundären) Region umgeleitet werden, treten bei diesen Sitzungen Fehler auf, wenn sie von Clients ausgehen, die sich außerhalb dieser Region befinden. Aus diesem Grund kann die Richtlinie für automatisches Failover nicht aktiviert werden, wenn die beteiligten Server in den VNET-Regeln enthalten sind. Um das manuelle Failover zu unterstützen, gehen Sie folgendermaßen vor:

1. Stellen Sie die redundanten Kopien der Front-End-Komponenten der Anwendung (Webdienst, VMs usw.) in der sekundären Region bereit.
2. Konfigurieren Sie die [VNET-Regeln](sql-database-vnet-service-endpoint-rule-overview.md) individuell für den primären und sekundären Server.
3. Aktivieren Sie das [Front-End-Failover durch eine Traffic Manager-Konfiguration](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime).
4. Initiieren Sie ein manuelles Failover, wenn der Ausfall erkannt wird. Diese Option ist für die Anwendungen optimiert, die konsistente Latenz zwischen Front-End und Datenebene erfordern, und unterstützt die Wiederherstellung, wenn Front-End, Datenebene oder beide vom Ausfall betroffen sind.

> [!NOTE]
> Stellen Sie bei Verwendung des **schreibgeschützten Listeners** für den Lastenausgleich einer schreibgeschützten Workload sicher, dass diese Workload auf einem virtuellen Computer oder einer anderen Ressource in der sekundären Region ausgeführt wird, damit sie eine Verbindung mit der sekundären Datenbank herstellen kann.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Verwenden von Failovergruppen und SQL-Datenbank-Firewallregeln

Wenn Ihr Geschäftskontinuitätsplan das Durchführen eines Failovers mithilfe von Gruppen mit automatischem Failover erfordert, können Sie den Zugriff auf Ihre SQL-Datenbank mithilfe der herkömmlichen Firewallregeln einschränken.  Um das automatische Failover zu unterstützen, gehen Sie folgendermaßen vor:

1. [Erstellen Sie eine öffentliche IP-Adresse.](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Erstellen Sie einen öffentlichen Lastenausgleich](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-a-basic-load-balancer), und weisen Sie ihm die öffentliche IP-Adresse zu.
3. [Erstellen Sie ein virtuelles Netzwerk und die VMs](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-back-end-servers) für Ihre Front-End-Komponenten.
4. [Erstellen Sie eine Netzwerksicherheitsgruppe](../virtual-network/security-overview.md), und konfigurieren Sie eingehende Verbindungen.
5. Stellen Sie mithilfe des [Diensttags](../virtual-network/security-overview.md#service-tags) „Sql“ sicher, dass die ausgehenden Verbindungen für Azure SQL-Datenbank geöffnet sind.
6. Erstellen Sie eine [SQL-Datenbank-Firewallregel](sql-database-firewall-configure.md), um eingehenden Datenverkehr von der öffentlichen IP-Adresse, die Sie in Schritt 1 erstellt haben, zuzulassen.

Weitere Informationen zur Konfiguration des ausgehenden Zugriffs und zu der IP-Adresse, die in den Firewallregeln verwendet werden soll, finden Sie unter [Ausgehende Verbindungen in Azure](../load-balancer/load-balancer-outbound-connections.md).

Die oben genannte Konfiguration sorgt dafür, dass das automatische Failover keine Verbindungen von den Front-End-Komponenten blockiert, und setzt voraus, dass die Anwendung die längeren Wartezeiten zwischen dem Front-End und der Datenebene tolerieren kann.

> [!IMPORTANT]
> Um die Geschäftskontinuität bei regionalen Ausfällen zu gewährleisten, müssen Sie die geografische Redundanz sowohl für die Front-End-Komponenten als auch die Datenbanken sicherstellen.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Aktivieren der geografischen Replikation zwischen verwalteten Instanzen und ihren VNETs

Wenn Sie eine Failovergruppe zwischen primären und sekundären verwalteten Instanzen in zwei verschiedenen Regionen einrichten, wird jede Instanz mit einem unabhängigen VNET isoliert. Um Replikationsdatenverkehr zwischen diesen VNETs zuzulassen, müssen diese Voraussetzungen erfüllt sein:

1. Die beiden verwalteten Instanzen müssen sich in verschiedenen Azure-Regionen befinden.
2. Ihre sekundäre Datenbank muss leer sein (keine Benutzerdatenbanken).
3. Die primäre und sekundäre verwaltete Instanz müssen sich in derselben Ressourcengruppe befinden.
4. Die VNETS, denen die verwalteten Instanzen angehören, müssen über ein [VPN-Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) verbunden sein. Globales VNET-Peering wird nicht unterstützt.
5. Die beiden VNETs der verwalteten Instanzen dürfen keine überlappenden IP-Adressen aufweisen.
6. Sie müssen die Netzwerksicherheitsgruppen (NSG) so einrichten, dass die Ports 5022 und der Bereich von 11000 bis 12000 für ein- und ausgehende Verbindungen vom Subnetz der jeweils anderen verwalteten Instanz offen sind. Dadurch wird der Replikationsdatenverkehr zwischen den Instanzen zugelassen.

   > [!IMPORTANT]
   > Falsch konfigurierte NSG-Sicherheitsregeln führen zu unterbrochenen Datenbankkopiervorgängen.

7. Die zweite Instanz ist mit der richtigen DNS-Zonen-ID konfiguriert. Die DNS-Zone ist eine Eigenschaft einer verwalteten Instanz, und ihre ID ist in der Hostnamenadresse enthalten. Die Zonen-ID wird als zufällige Zeichenfolge generiert, wenn die erste verwaltete Instanz in jedem VNET erstellt und die gleiche ID alle anderen Instanzen im selben Subnetz zugewiesen wird. Nach der Zuweisung kann die DNS-Zone nicht geändert werden. Verwaltete Instanzen in der gleichen Failovergruppe müssen die DNS-Zone gemeinsam verwenden. Sie erreichen dies durch Übergeben der Zonen-ID der primären Instanz als Wert des DnsZonePartner-Parameters beim Erstellen der sekundären Instanz. 

## <a name="upgrading-or-downgrading-a-primary-database"></a>Upgrade oder Downgrade einer primären Datenbank

Sie können für eine primäre Datenbank ein Upgrade oder Downgrade auf eine andere Computegröße (innerhalb der gleichen Dienstebene; nicht zwischen „Universell“ und „Unternehmenskritisch“) ausführen, ohne die Verbindung mit sekundären Datenbanken zu trennen. Bei einem Upgrade wird empfohlen, zuerst das Upgrade für alle sekundären Datenbanken und anschließend das Upgrade für die primäre Datenbank auszuführen. Drehen Sie bei einem Downgrade die Reihenfolge um: Führen Sie zuerst das Downgrade für die primäre Datenbank und anschließend das Downgrade für alle sekundären Datenbanken aus. Wenn Sie ein Upgrade oder Downgrade der Datenbank auf eine andere Dienstebene durchführen, wird diese Empfehlung erzwungen.

Diese Reihenfolge ist besonders zur Vermeidung des Überladens der sekundären Datenbank auf einer niedrigeren SKU empfehlenswert, damit nicht während des Upgrade- oder Downgradevorgangs ein erneutes Seeding durchgeführt werden muss. Sie können dieses Problem auch vermeiden, indem Sie die primäre Datenbank mit Schreibschutz versehen. Das geht allerdings zu Lasten aller Lese-/Schreibworkloads für die primäre Datenbank. 

> [!NOTE]
> Wenn Sie eine sekundäre Datenbank als Teil der Konfiguration der Failovergruppe erstellt haben, sollten Sie kein Downgrade der sekundären Datenbank durchführen. So wird sichergestellt, dass Ihre Datenebene nach dem Aktivieren des Failovers ausreichende Kapazität zum Verarbeiten des normalen Workloads hat.

## <a name="preventing-the-loss-of-critical-data"></a>Verhindern des Verlusts wichtiger Daten

Aufgrund der hohen Latenz von WANs wird für die fortlaufende Kopie ein asynchroner Replikationsmechanismus verwendet. Asynchrone Replikation macht Datenverlust unvermeidlich, sobald ein Ausfall auftritt. Bei einigen Anwendung dürfen jedoch ggf. keine Daten verloren gehen. Um diese kritischen Aktualisierungen zu schützen, kann ein Anwendungsentwickler die Systemprozedur [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) aufrufen, unmittelbar nachdem der Commit für die Transaktion erfolgt ist. Das Aufrufen von **sp_wait_for_database_copy_sync** blockiert den aufrufenden Thread so lange, bis die letzte Transaktion mit erfolgtem Commit in die sekundäre Datenbank übertragen wurde. Es wird jedoch nicht abgewartet, bis die übertragenen Transaktionen wiedergegeben und in der sekundären Datenbank committet werden. **sp_wait_for_database_copy_sync** ist auf eine bestimmte fortlaufende Kopierverknüpfung begrenzt. Jeder Benutzer mit den Rechten zum Herstellen der Verbindung mit der primären Datenbank kann diese Prozedur aufrufen.

> [!NOTE]
> **sp_wait_for_database_copy_sync** verhindert Datenverlust nach einem Failover, garantiert aber nicht die vollständige Synchronisierung für den Lesezugriff. Die vom Aufruf der Prozedur **sp_wait_for_database_copy_sync** verursachte Verzögerung kann signifikant sein und hängt von der Größe des Transaktionsprotokolls zum Zeitpunkt des Aufrufs ab.

## <a name="failover-groups-and-point-in-time-restore"></a>Failovergruppen und Point-in-Time-Wiederherstellung

Informationen zur Verwendung der Point-in-Time-Wiederherstellung mit Failovergruppen finden Sie unter [Point-in-Time-Wiederherstellung](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="programmatically-managing-failover-groups"></a>Programmgesteuertes Verwalten von Failovergruppen

Wie bereits zuvor erwähnt, können Gruppen für automatisches Failover und aktive Georeplikation auch programmgesteuert mit Azure PowerShell und der REST-API verwaltet werden. Die folgenden Tabellen beschreiben den verfügbaren Satz von Befehlen. Die aktive Georeplikation umfasst eine Reihe von Azure Resource Manager-APIs für die Verwaltung. Hierzu zählen unter anderem die [Azure SQL-Datenbank-REST-API](https://docs.microsoft.com/rest/api/sql/) und [Azure PowerShell-Cmdlets](https://docs.microsoft.com/powershell/azure/overview). Diese APIs erfordern die Verwendung von Ressourcengruppen und unterstützen rollenbasierte Sicherheit (RBAC). Weitere Informationen zur Implementierung von Zugriffsrollen finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure](../role-based-access-control/overview.md).

### <a name="powershell-manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>PowerShell: Verwalten eines Failovers von SQL-Datenbanken mit einzelnen Datenbanken und Pools für elastische Datenbanken

| Cmdlet | BESCHREIBUNG |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Dieser Befehl erstellt eine Failovergruppe und registriert sie auf primären und sekundären Servern.|
| [Remove-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Entfernt die Failovergruppe vom Server und löscht alle in der Gruppe enthaltenen sekundäre Datenbanken. |
| [Get-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Ruft die Konfiguration der Failovergruppe ab. |
| [Set-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Ändert die Konfiguration der Failovergruppe. |
| [Switch-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Löst das Failover der Failovergruppe auf den sekundären Server aus. |
| [Add-AzSqlDatabaseToFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Fügt einer Azure SQL-Datenbank-Failovergruppe eine oder mehrere Datenbanken hin zu.|
|  | |

> [!IMPORTANT]
> Ein Beispielskript finden Sie unter [Konfiguration und Failover einer Failovergruppe für eine einzelne Datenbank](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md).
>

### <a name="powershell-managing-failover-groups-with-managed-instances-preview"></a>PowerShell: Verwalten von Failovergruppen mit verwalteten Instanzen (Vorschau)

#### <a name="install-the-newest-pre-release-version-of-powershell"></a>Installieren der neuesten Vorabversion von PowerShell

1. Aktualisieren Sie das PowerShellGet-Modul auf Version 1.6.5 (oder die neueste Vorschauversion). Siehe [Website zur PowerShell-Vorschauversion](https://www.powershellgallery.com/packages/AzureRM.Sql/4.11.6-preview).

   ```powershell
      install-module PowerShellGet -MinimumVersion 1.6.5 -force
   ```

2. Führen Sie in einem neuen PowerShell-Fenster die folgenden Befehle aus:

   ```powershell
      import-module PowerShellGet
      get-module PowerShellGet #verify version is 1.6.5 (or newer)
      install-module azurerm.sql -RequiredVersion 4.5.0-preview -AllowPrerelease –Force
      import-module azurerm.sql
   ```

#### <a name="powershell-commandlets-to-create-an-instance-failover-group"></a>PowerShell-Cmdlets zum Erstellen einer Instanzfailovergruppe

| API | BESCHREIBUNG |
| --- | --- |
| New-AzureRmSqlDatabaseInstanceFailoverGroup |Dieser Befehl erstellt eine Failovergruppe und registriert sie auf primären und sekundären Servern.|
| Set-AzureRmSqlDatabaseInstanceFailoverGroup |Ändert die Konfiguration der Failovergruppe.|
| Get-AzureRmSqlDatabaseInstanceFailoverGroup |Ruft die Konfiguration der Failovergruppe ab.|
| Switch-AzureRmSqlDatabaseInstanceFailoverGroup |Löst das Failover der Failovergruppe auf den sekundären Server aus.|
| Remove-AzureRmSqlDatabaseInstanceFailoverGroup | Entfernt eine Failovergruppe.|

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>REST-API: Verwalten von SQL-Datenbank-Failovergruppen mit Einzel- und Pooldatenbanken

| API | BESCHREIBUNG |
| --- | --- |
| [Create or Update Failover Group](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Erstellt oder aktualisiert eine Failovergruppe. |
| [Delete Failover Group](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Entfernt die Failovergruppe vom Server |
| [Failover (Planned)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Führt ein Failover vom aktuellen primären Server auf diesen Server durch. |
| [Force Failover Allow Data Loss](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) |Führt ein Failover vom aktuellen primären Server auf diesen Server durch. Bei diesem Vorgang können Daten verloren gehen. |
| [Get Failover Group](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | Ruft eine Failovergruppe ab. |
| [List Failover Groups By Server](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Listet die Failovergruppen eines Servers auf. |
| [Update Failover Group](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Aktualisiert eine Failovergruppe. |
|  | |

### <a name="rest-api-manage-failover-groups-with-managed-instances-preview"></a>REST-API: Verwalten von Failovergruppen mit verwalteten Instanzen (Vorschau)

| API | BESCHREIBUNG |
| --- | --- |
| [Create or Update Failover Group](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Erstellt oder aktualisiert eine Failovergruppe. |
| [Delete Failover Group](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | Entfernt die Failovergruppe vom Server |
| [Failover (Planned)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Führt ein Failover vom aktuellen primären Server auf diesen Server durch. |
| [Force Failover Allow Data Loss](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) |Führt ein Failover vom aktuellen primären Server auf diesen Server durch. Bei diesem Vorgang können Daten verloren gehen. |
| [Get Failover Group](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | Ruft eine Failovergruppe ab. |
| [List Failover Groups - List By Location](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Listet die Failovergruppen an einem Standort auf. |

## <a name="next-steps"></a>Nächste Schritte

- Beispielskripts:
  - [Configure and failover a single database using active geo-replication](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) (Konfiguration und Failover einer einzelnen Datenbank mithilfe von aktiver Georeplikation)
  - [Configure and failover a pooled database using active geo-replication](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md) (Konfiguration und Failover einer Pooldatenbank mithilfe von aktiver Georeplikation)
  - [Konfiguration und Failover einer Failovergruppe für eine einzelne Datenbank](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- Eine Übersicht und verschiedene Szenarien zum Thema Geschäftskontinuität finden Sie unter [Übersicht über die Geschäftskontinuität](sql-database-business-continuity.md)
- Informationen über automatisierte Sicherungen von Azure SQL-Datenbanken finden Sie unter [Automatisierte SQL-Datenbanksicherungen](sql-database-automated-backups.md).
- Informationen zum Verwenden automatisierter Sicherungen für die Wiederherstellung finden Sie unter [Wiederherstellen einer Datenbank aus vom Dienst initiierten Sicherungen](sql-database-recovery-using-backups.md).
- Weitere Informationen zu Authentifizierungsanforderungen für einen neuen primären Server und die Datenbank finden Sie unter [Verwalten der Sicherheit der Azure SQL-Datenbank nach der Notfallwiederherstellung](sql-database-geo-replication-security-config.md).

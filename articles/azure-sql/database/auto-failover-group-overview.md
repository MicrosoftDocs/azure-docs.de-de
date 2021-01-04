---
title: Autofailover-Gruppen
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Mit Autofailover-Gruppen können Sie die Replikation und das automatische/koordinierte Failover einer Gruppe von Datenbanken auf einem Server oder aller Datenbanken in einer verwalteten Instanz verwalten.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 11/16/2020
ms.openlocfilehash: 0d2248b9c0a289f5e4f9f2f8e987365ab58c49c0
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94988543"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Verwenden von Autofailover-Gruppen für ein transparentes und koordiniertes Failover mehrerer Datenbanken
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Mit dem Feature „Autofailover-Gruppen“ können Sie die Replikation und das Failover einer Gruppe von Datenbanken auf einem Server oder aller Datenbanken in einer verwalteten Instanz in eine andere Region verwalten. Hierbei handelt es sich um eine deklarative Abstraktion, die auf dem bereits vorhandenen Feature [Aktive Georeplikation](active-geo-replication-overview.md) basiert und dazu dient, die Bereitstellung und Verwaltung georeplizierter Datenbanken zu vereinfachen. Sie können ein Failover manuell einleiten oder basierend auf einer benutzerdefinierten Richtlinie an den Azure-Dienst delegieren. Letzteres gibt Ihnen die Möglichkeit, nach schwerwiegenden Ausfällen oder anderen ungeplanten Ereignissen, die zum vollständigen oder teilweisen Verlust der Verfügbarkeit der SQL-Datenbank-Instanz oder SQL Managed Instance in der primären Region führen, automatisch mehrere verwandte Datenbanken in einer sekundären Region wiederherzustellen. Eine Failovergruppe kann eine oder mehrere Datenbanken enthalten, die in der Regel von der gleichen Anwendung verwendet werden. Außerdem können sie die lesbaren sekundären Datenbanken zur Auslagerung schreibgeschützter Abfrageworkloads verwenden. Da Gruppen für automatisches Failover mehrere Datenbanken beinhalten, müssen diese Datenbanken auf dem primären Server konfiguriert werden. Gruppen für automatisches Failover unterstützen die Replikation aller Datenbanken in der Gruppe auf nur einen sekundären Server oder in einer sekundären Instanz in einer anderen Region.

> [!NOTE]
> Wenn Sie mehrere sekundäre Azure SQL-Datenbank-Instanzen in der gleichen oder in verschiedenen Regionen wünschen, nutzen Sie [aktive Georeplikation](active-geo-replication-overview.md).

Wenn Sie Autofailover-Gruppen mit einer Richtlinie für automatisches Failover verwenden, führt jeder Ausfall, der eine oder mehrere der Datenbanken in der Gruppe betrifft, zu einem automatischen Failover. In der Regel handelt es sich hierbei um Vorfälle, die durch die integrierten Vorgänge für automatische Hochverfügbarkeit nicht selbst behoben werden können. Zu den Beispielen für Failoverauslöser gehört ein Vorfall, der dadurch verursacht wird, dass ein SQL-Datenbank-Mandantenring oder -Steuerungsring aufgrund eines Speicherlecks des Betriebssystemkernels auf mehreren Computeknoten ausgefallen ist, oder ein Vorfall, der dadurch verursacht wird, dass ein oder mehrere Mandantenringe ausgefallen sind, weil während der routinemäßigen Außerbetriebnahme von Hardware ein falsches Netzwerkkabel durchtrennt wurde.  Weitere Informationen finden Sie unter [Hochverfügbarkeit von SQL-Datenbank](high-availability-sla.md).

Darüber hinaus bieten Gruppen für automatisches Failover Lese-/Schreib-Listenerendpunkte, die während eines Failovers unverändert bleiben. Sowohl bei manueller als auch automatischer Failoveraktivierung schaltet das Failover alle sekundären Datenbanken in der Gruppe zu primären um. Nach Abschluss des Datenbankfailovers wird der DNS-Eintrag automatisch aktualisiert, um die Endpunkte in die neue Region umzuleiten. Die spezifischen RPO- und RTO-Daten finden Sie unter [Übersicht über die Geschäftskontinuität mit Azure SQL-Datenbank](business-continuity-high-availability-disaster-recover-hadr-overview.md).

Wenn Sie Autofailover-Gruppen mit einer Richtlinie für automatisches Failover verwenden, führt jeder Ausfall, der Datenbanken auf einem Server oder in der verwalteten Instanz betrifft, zu einem automatischen Failover. Sie können Autofailover-Gruppen über folgende Komponenten verwalten:

- [Azure portal](geo-distributed-application-configure-tutorial.md)
- [Azure-Befehlszeilenschnittstelle: Failovergruppe](scripts/add-database-to-failover-group-cli.md)
- [PowerShell: Failovergruppe](scripts/add-database-to-failover-group-powershell.md)
- [REST-API: Failovergruppe](/rest/api/sql/failovergroups)

Stellen Sie nach dem Failover sicher, dass die Authentifizierungsanforderungen für Ihre Datenbank, Ihren Server oder Ihre Instanz in der neuen primären Instanz konfiguriert sind. Weitere Informationen finden Sie unter [Verwalten der Sicherheit der Azure SQL-Datenbank nach der Notfallwiederherstellung](active-geo-replication-security-configure.md).

Wenn Sie echte Geschäftskontinuität erreichen möchten, ist das Bereitstellen von Datenbankredundanz zwischen Rechenzentren jedoch nur ein Teil der Lösung. Für die komplette Wiederherstellung einer Anwendung bzw. eines Diensts nach einem schwerwiegenden Fehler ist das Wiederherstellen aller Komponenten erforderlich, aus denen sich der Dienst und alle abhängigen Dienste zusammensetzen. Beispiele dieser Komponenten sind die Clientsoftware (z. B. ein Browser mit benutzerdefiniertem JavaScript), Web-Front-Ends, Speicher und DNS. Es ist wichtig, dass alle Komponenten hinsichtlich derselben Fehler gegen Ausfälle geschützt und innerhalb des RTO (Recovery Time Objective) der Anwendung wieder verfügbar sind. Daher müssen Sie alle abhängigen Dienste bestimmen und mit dem Leistungsumfang und den Funktionen vertraut sein, die sie bieten. Dann müssen Sie entsprechende Maßnahmen ergreifen, um sicherzustellen, dass Ihr Dienst während des Failovers der Dienste funktioniert, von denen er abhängig ist. Weitere Informationen zum Entwerfen von Lösungen für die Notfallwiederherstellung finden Sie unter [Entwerfen von Cloudlösungen für die Notfallwiederherstellung mithilfe der aktiven Georeplikation](designing-cloud-solutions-for-disaster-recovery.md).

## <a name="terminology-and-capabilities"></a>Terminologie und Funktionen

- **Failovergruppe**

  Eine Failovergruppe ist eine benannte Gruppe von Datenbanken, die von einem einzelnen Server oder innerhalb einer einzelnen verwalteten Instanz verwaltet werden. Diese Datenbanken sind eine Einheit, für die ein Failover in eine andere Region durchgeführt werden kann, falls alle oder einige primäre Datenbanken aufgrund eines Ausfalls in der primären Region nicht mehr verfügbar sind. Wenn eine Failovergruppe für SQL Managed Instance erstellt wird, enthält sie alle Benutzerdatenbanken in der Instanz. Deshalb kann nur eine Failovergruppe für eine Instanz konfiguriert werden.
  
  > [!IMPORTANT]
  > Der Name der Failovergruppe muss innerhalb der `.database.windows.net`-Domäne global eindeutig sein.

- **Server**

     Bei Servern können einige oder alle Benutzerdatenbanken auf einem Server in einer Failovergruppe platziert werden. Außerdem unterstützt ein Server mehrere Failovergruppen auf einem einzelnen Server.

- **Primärer Server/verwaltete Instanz**

  Der Server oder die verwaltete Instanz, auf dem bzw. in der die primären Datenbanken in der Failovergruppe gehostet werden.

- **Sekundärer Server/verwaltete Instanz**

  Der Server oder die verwaltete Instanz, auf dem bzw. in der die sekundären Datenbanken in der Failovergruppe gehostet werden. Der sekundäre Server/verwaltete Instanz kann sich nicht in der gleichen Region wie der primäre Server/verwaltete Instanz befinden.

- **Hinzufügen einzelner Datenbanken zu Failovergruppe**

  Sie können mehrere einzelne Datenbanken auf demselben Server in dieselbe Failovergruppe einfügen. Wenn Sie der Failovergruppe eine einzelne Datenbank hinzufügen, wird auf dem sekundären Server automatisch eine sekundäre Datenbank mit derselben Edition und Computegröße erstellt.  Sie haben diesen Server beim Erstellen der Failovergruppe angegeben. Wenn Sie eine Datenbank hinzufügen, die bereits auf dem sekundären Server eine sekundäre Datenbank hat, erbt die Gruppe diese Verknüpfung für die Georeplikation. Wenn Sie eine Datenbank hinzufügen, die bereits eine sekundäre Datenbank auf einem Server hat, der nicht Teil der Failovergruppe ist, wird eine neue sekundäre Datenbank auf dem sekundären Server erstellt.

  > [!IMPORTANT]
  > Stellen Sie sicher, dass der sekundäre Server keine Datenbank mit dem gleichen Namen hat, es sei denn, es handelt sich um eine vorhandene sekundäre Datenbank. In Failovergruppen für SQL Managed Instance werden alle Benutzerdatenbanken repliziert. Sie können nicht eine Teilmenge der Benutzerdatenbanken für die Replikation in der Failovergruppe auswählen.

- **Hinzufügen von Datenbanken im Pool für elastische Datenbanken zu Failovergruppe**

  Sie können mehrere oder alle Datenbanken in einem Pool für elastische Datenbanken in dieselbe Failovergruppe einfügen. Wenn sich die primäre Datenbank in einem Pool für elastische Datenbanken befindet, wird die sekundäre Datenbank automatisch im Pool für elastische Datenbanken desselben Namens (sekundärer Pool) erstellt. Sie müssen sicherstellen, dass der sekundäre Server einen Pool für elastische Datenbanken mit genau demselben Namen und ausreichend freier Kapazität zum Hosten der sekundären Datenbanken enthält, die von der Failovergruppe erstellt werden. Wenn Sie im Pool eine Datenbank hinzufügen, die bereits im sekundären Pool eine sekundäre Datenbank hat, erbt die Gruppe diese Verknüpfung für die Georeplikation. Wenn Sie eine Datenbank hinzufügen, die bereits eine sekundäre Datenbank auf einem Server hat, der nicht Teil der Failovergruppe ist, wird eine neue sekundäre Datenbank im sekundären Pool erstellt.
  
- **Anfängliches Seeding**

  Beim Hinzufügen von Datenbanken, Pools für elastische Datenbanken oder verwalteten Instanzen zu einer Failovergruppe gibt es eine anfängliche Seedingphase, bevor die Datenreplikation startet. Die anfängliche Seedingphase ist der längste und aufwendigste Vorgang. Sobald das anfängliche Seeding abgeschlossen ist, werden die Daten synchronisiert, und anschließend werden nur nachfolgende Datenänderungen repliziert. Die Zeit, die für das anfängliche Seeding benötigt wird, hängt von der Größe Ihrer Daten, der Anzahl der replizierten Datenbanken und der Geschwindigkeit der Verbindung zwischen den Entitäten in der Failovergruppe ab. Unter normalen Umständen beträgt die mögliche Seedinggeschwindigkeit bis zu 500 GB pro Stunde für SQL-Datenbank und bis zu 360 GB pro Stunde für SQL Managed Instance. Das Seeding wird für alle Datenbanken parallel ausgeführt.

  Bei SQL Managed Instance muss zum Abschätzen der Zeit für die anfängliche Seedingphase auch die Geschwindigkeit der ExpressRoute-Verbindung zwischen den beiden Instanzen berücksichtigt werden. Wenn die Geschwindigkeit der Verbindung zwischen den beiden Instanzen langsamer als erforderlich ist, wirkt sich dies wahrscheinlich erheblich auf die Zeit für das Seeding aus. Anhand der angegebenen Seedinggeschwindigkeit, der Anzahl der Datenbanken, der Gesamtgröße der Daten und der Verbindungsgeschwindigkeit können Sie abschätzen, wie lange die anfängliche Seedingphase vor dem Starten der Datenreplikation dauern wird. Beispielsweise würde für eine einzelne Datenbank mit 100 GB die anfängliche Seedingphase etwa 1,2 Stunden dauern, wenn die Verbindung eine Pushübertragung von 84 GB pro Stunde ermöglicht und für keine anderen Datenbanken ein Seeding ausgeführt wird. Können über die Verbindung nur 10 GB pro Stunde übertragen werden, dauert das Seeding einer 100-GB-Datenbank ungefähr 10 Stunden. Wenn mehrere Datenbanken zu replizieren sind, wird das Seeding parallel ausgeführt, und in Kombination mit einer niedrigen Verbindungsgeschwindigkeit kann die anfängliche Seedingphase erheblich länger dauern, insbesondere dann, wenn das parallele Seeding von Daten aus allen Datenbanken die verfügbare Verbindungsbandbreite überschreitet. Wenn die Netzwerkbandbreite zwischen zwei Instanzen begrenzt ist und Sie einer Failovergruppe mehrere verwaltete Instanzen hinzufügen, sollten Sie der Failovergruppe mehrere verwaltete Instanzen ggf. einzeln nacheinander hinzufügen. Wenn eine Gateway-SKU mit entsprechender Größe zwischen den beiden verwalteten Instanzen vorhanden ist und die Bandbreite des Unternehmensnetzwerks dies zulässt, können Geschwindigkeiten von bis zu 360 GB pro Stunde erreicht werden.  

- **DNS-Zone**

  Eine eindeutige ID, die automatisch generiert wird, wenn eine neue SQL Managed Instance erstellt wird. Ein Zertifikat mit mehreren Domänen (SAN) für diese Instanz wird bereitgestellt, um die Clientverbindungen mit einer beliebigen Instanz in der gleichen DNS-Zone zu authentifizieren. Die beiden verwalteten Instanzen in der gleichen Failovergruppe müssen die DNS-Zone gemeinsam verwenden.
  
  > [!NOTE]
  > Eine DNS-Zonen-ID ist für Failovergruppen, die für SQL-Datenbank erstellt werden, nicht erforderlich.

- **Lese-/Schreib-Failovergruppenlistener**

  Ein DNS CNAME-Eintrag, der auf die URL der aktuellen primären Datenbank verweist. Er wird automatisch erstellt, wenn die Failovergruppe erstellt wird, und ermöglicht der Lese-/Schreibworkload die transparente Verbindungswiederherstellung mit der primären Datenbank, wenn sich die primäre Datenbank nach dem Failover ändert. Wenn die Failovergruppe auf einem Server erstellt wird, hat der DNS CNAME-Eintrag für die Listener-URL die Form `<fog-name>.database.windows.net`. Wenn die Failovergruppe in einer SQL Managed Instance erstellt wird, hat der DNS CNAME-Eintrag für die Listener-URL die Form `<fog-name>.<zone_id>.database.windows.net`.

- **Nur-Lese-Failovergruppenlistener**

  Ein DNS CNAME-Eintrag, der auf den schreibgeschützten Listener verweist, der wiederum auf die URL der sekundären Datenbank verweist. Er wird automatisch erstellt, wenn die Failovergruppe erstellt wird, und ermöglicht der schreibgeschützten SQL-Workload die transparente Verbindungsherstellung mit der sekundären Datenbank unter Verwendung der angegebenen Lastenausgleichsregeln. Wenn die Failovergruppe auf einem Server erstellt wird, hat der DNS CNAME-Eintrag für die Listener-URL die Form `<fog-name>.secondary.database.windows.net`. Wenn die Failovergruppe in einer SQL Managed Instance erstellt wird, hat der DNS CNAME-Eintrag für die Listener-URL die Form `<fog-name>.secondary.<zone_id>.database.windows.net`.

- **Richtlinie für automatisches Failover**

  Standardmäßig wird eine Failovergruppe mit einer Richtlinie für automatisches Failover konfiguriert. Azure löst das Failover aus, nachdem der Fehler erkannt und die Toleranzperiode abgelaufen ist. Das System muss sicherstellen, dass der Ausfall aufgrund des Ausmaßes der Auswirkungen nicht durch die integrierte [Hochverfügbarkeitsinfrastruktur](high-availability-sla.md) gemildert werden kann. Wenn Sie den Failoverworkflow über die Anwendung oder manuell steuern möchten, können Sie automatisches Failover deaktivieren.
  
  > [!NOTE]
  > Da das Betriebsteam Maßnahmen ergreifen muss, um das Ausmaß des Ausfalls zu überprüfen und festzustellen, wie schnell dieser minimiert werden kann, kann die Toleranzperiode nicht auf einen Wert unter einer Stunde festgelegt werden. Diese Einschränkung gilt unabhängig vom jeweiligen Datensynchronisierungsstatus für alle Datenbanken in der Failovergruppe.

- **Schreibgeschützte Failoverrichtlinie**

  Standardmäßig ist das Failover des schreibgeschützten Listeners deaktiviert. Dadurch wird sichergestellt, dass die Leistung der primären Datenbank nicht beeinträchtigt wird, wenn die sekundäre Datenbank offline ist. Es bedeutet jedoch auch, dass die schreibgeschützten Sitzungen erst dann eine Verbindung herstellen können, nachdem die sekundäre Datenbank wiederhergestellt wurde. Wenn Sie keine Downtime für die schreibgeschützten Sitzungen tolerieren und die primäre Datenbank sowohl für schreibgeschützten Datenverkehr als auch für Lese-/Schreibdatenverkehr verwenden können (auf Kosten der möglichen Leistungseinbußen für die primäre Datenbank), können Sie das Failover für den schreibgeschützten Listener aktivieren, indem Sie die Eigenschaft `AllowReadOnlyFailoverToPrimary` konfigurieren. In diesem Fall wird der schreibgeschützte Datenverkehr automatisch zur primären Datenbank umgeleitet, wenn die sekundäre Datenbank nicht verfügbar ist.

  > [!NOTE]
  > Die Eigenschaft `AllowReadOnlyFailoverToPrimary` ist nur wirksam, wenn die Richtlinie für automatisches Failover aktiviert ist und von Azure ein automatisches Failover ausgelöst wurde. Ist die Eigenschaft in diesem Fall auf „True“ festgelegt, werden von der primären Datenbank sowohl Sitzung mit Lese-/Schreibzugriff als auch schreibgeschützte Sitzungen bedient.

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

  Da die primären und sekundären Datenbanken mithilfe der asynchronen Replikation synchronisiert werden, kann das Failover zu Datenverlust führen. Sie können die Richtlinie für automatisches Failover entsprechend der Toleranz Ihrer Anwendung gegenüber Datenverlust anpassen. Durch Konfiguration von `GracePeriodWithDataLossHours` können Sie steuern, wie lange das System wartet, bevor das Failover initiiert wird, das wahrscheinlich zu Datenverlust führt.

- **Mehrere Failovergruppen**

  Sie können mehrere Failovergruppen für das gleiche Serverpaar konfigurieren, um den Umfang von Failovern zu steuern. Jede Gruppe führt ein unabhängiges Failover durch. Wenn Ihre mehrinstanzenfähige Anwendung Pools für elastische Datenbanken verwendet, können Sie diese Funktion zum Mischen primärer und sekundärer Datenbanken in den einzelnen Pools verwenden. Auf diese Weise können Sie die Auswirkungen eines Ausfalls auf nur die Hälfte der Mandanten reduzieren.

  > [!NOTE]
  > SQL Managed Instance bietet keine Unterstützung für mehrere Failovergruppen.
  
## <a name="permissions"></a>Berechtigungen

Berechtigungen für eine Failovergruppe werden über die [rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/overview.md) verwaltet. Die Rolle [Mitwirkender von SQL Server](../../role-based-access-control/built-in-roles.md#sql-server-contributor) verfügt über die erforderlichen Berechtigungen zum Verwalten von Failovergruppen.

### <a name="create-failover-group"></a>Erstellen einer Failovergruppe

Zum Erstellen einer Failovergruppe benötigen Sie Azure RBAC-Schreibzugriff auf den primären und die sekundären Server sowie auf alle Datenbanken in der Failovergruppe. Bei einer SQL Managed Instance benötigen Sie Azure RBAC-Schreibzugriff auf die primäre und sekundäre SQL Managed Instance. Berechtigungen für einzelne Datenbanken sind dabei nicht relevant, da einzelne Datenbanken der SQL Managed Instance nicht zu einer Failovergruppe hinzugefügt bzw. aus einer Failovergruppe entfernt werden können.

### <a name="update-a-failover-group"></a>Aktualisieren einer Failovergruppe

Zum Aktualisieren einer Failovergruppe benötigen Sie Azure RBAC-Schreibzugriff auf die Failovergruppe und auf alle Datenbanken auf dem aktuellen primären Server bzw. in der aktuellen verwalteten Instanz.  

### <a name="fail-over-a-failover-group"></a>Ausführen des Failovers einer Failovergruppe

Zum Erstellen eines Failovers für eine Failovergruppe benötigen Sie Azure RBAC-Schreibzugriff auf die Failovergruppe auf dem neuen primären Server bzw. in der neuen verwalteten Instanz.

## <a name="best-practices-for-sql-database"></a>Bewährte Methoden für SQL-Datenbank

Die Autofailover-Gruppe muss auf dem primären Server konfiguriert werden und stellt eine Verbindung mit dem sekundären Server in einer anderen Azure-Region her. Die Gruppen können alle oder einige Datenbanken auf diesen Servern umfassen. Das folgende Diagramm zeigt eine typische Konfiguration einer georedundanten Cloudanwendung mit mehreren Datenbanken und einer Autofailover-Gruppe.

![Das Diagramm zeigt eine typische Konfiguration einer georedundanten Cloudanwendung mit mehreren Datenbanken und einer Autofailover-Gruppe.](./media/auto-failover-group-overview/auto-failover-group.png)

> [!NOTE]
> Ein ausführliches Tutorial zum Hinzufügen einer Datenbank in SQL-Datenbank zu einer Failovergruppe finden Sie unter [Hinzufügen einer SQL-Datenbank-Instanz zu einer Failovergruppe](failover-group-add-single-database-tutorial.md).

Beim Entwerfen eines Diensts, der die Geschäftskontinuität aufrechterhalten soll, sind die folgenden allgemeinen Richtlinien zu beachten:

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>Verwenden von mindestens einer Failovergruppe, um das Failover mehrerer Datenbanken zu verwalten

Eine oder mehrere Failovergruppen können zwischen zwei Servern in verschiedenen Regionen erstellt werden (primäre und sekundäre Server). Jede Gruppe kann eine oder mehrere Datenbanken enthalten, die als Einheit wiederhergestellt werden, falls alle oder einige primäre Datenbanken aufgrund eines Ausfalls in der primären Region nicht mehr verfügbar sind. Die Failovergruppe erstellt eine geosekundäre Datenbank mit dem gleichen Dienstziel wie die primäre Datenbank. Wenn Sie der Failovergruppe eine vorhandene Georeplikationsbeziehung hinzufügen, stellen Sie sicher, dass die geosekundäre Datenbank mit der gleichen Dienstebene und Computegröße wie die primäre Datenbank konfiguriert ist.
  
> [!IMPORTANT]
> Das Erstellen von Failovergruppen zwischen zwei Servern in verschiedenen Abonnements wird derzeit für Azure SQL-Datenbank nicht unterstützt. Wenn Sie den primären oder sekundären Server nach dem Erstellen der Failovergruppe in ein anderes Abonnement verschieben, kann dies zu Fehlern bei den Failoveranforderungen und anderen Vorgängen führen.

### <a name="using-read-write-listener-for-oltp-workload"></a>Verwenden eines Lese-/Schreib-Listeners für OLTP-Workloads

Verwenden Sie beim Durchführen von OLTP-Vorgängen `<fog-name>.database.windows.net` als Server-URL, damit die Verbindungen automatisch an die primäre Datenbank weitergeleitet werden. Diese URL wird nach dem Failover nicht geändert. Beachten Sie, dass das Failover die Aktualisierung von DNS-Einträgen einschließt, damit die Clientverbindungen erst dann an die neue primäre Datenbank weitergeleitet werden, wenn der Client-DNS-Cache aktualisiert wurde.

### <a name="using-read-only-listener-for-read-only-workload"></a>Verwenden eines schreibgeschützten Listeners für schreibgeschützte Workloads

Wenn Sie über eine logisch isolierte schreibgeschützte Workload verfügen, die gegenüber einer bestimmten Veraltung tolerant ist, können Sie die sekundäre Datenbank in der Anwendung verwenden. Verwenden Sie für schreibgeschützte Sitzungen `<fog-name>.secondary.database.windows.net` als Server-URL, damit die Verbindung automatisch an die sekundäre Datenbank weitergeleitet wird. Darüber hinaus wird empfohlen, in der Verbindungszeichenfolge die Leseabsicht anzugeben. Verwenden Sie dazu `ApplicationIntent=ReadOnly`.

### <a name="preparing-for-performance-degradation"></a>Vorbereiten auf Leistungsbeeinträchtigungen

Eine typische Azure-Anwendung nutzt mehrere Azure-Dienste und besteht aus mehreren Komponenten. Das automatisierte Failover der Failovergruppe wird ausschließlich anhand des Status der Azure SQL-Komponenten ausgelöst. Andere Azure-Dienste in der primären Region sind vom Ausfall ggf. nicht betroffen, und es kann sein, dass die entsprechenden Komponenten in dieser Region weiterhin verfügbar sind. Nachdem die primären Datenbanken auf die Region für die Notfallwiederherstellung umgestellt wurden, nimmt die Latenz zwischen den abhängigen Komponenten unter Umständen zu. Um Auswirkungen der höheren Latenz auf die Leistung der Anwendung zu vermeiden, sollten Sie sicherstellen, dass in der Region für die Notfallwiederherstellung für die Redundanz aller Anwendungskomponenten gesorgt ist. Befolgen Sie außerdem diese [Richtlinien zur Netzwerksicherheit](#failover-groups-and-network-security).

### <a name="preparing-for-data-loss"></a>Vorbereiten auf Datenverlust

Wenn ein Ausfall erkannt wird, wartet Azure den Zeitraum ab, den Sie mit `GracePeriodWithDataLossHours` angegeben haben. Der Standardwert ist 1 Stunde. Wenn keine Datenverluste auftreten dürfen, legen Sie `GracePeriodWithDataLossHours` unbedingt auf eine ausreichend große Zahl fest, z.B. 24 Stunden. Verwenden Sie manuelles Gruppenfailover für ein Failback von der sekundären zur primären Datenbank.

> [!IMPORTANT]
> Bei Pools für elastische Datenbanken mit 800 oder weniger DTUs und über 250 Datenbanken, die die Georeplikation verwenden, können Probleme wie längere geplante Failover und Leistungseinbußen auftreten.  Diese Probleme treten häufiger bei schreibintensiven Workloads auf, wenn Georeplikations-Endpunkte geographisch weit auseinander liegen, oder wenn mehrere sekundäre Endpunkte für jede Datenbank verwendet werden.  Ein Anzeichen für derartige Probleme ist eine steigende Verzögerung bei der Georeplikation.  Diese Verzögerung können Sie mit [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) überwachen.  Wenn diese Probleme auftreten, können Sie sie entschärfen, indem Sie z.B. die Anzahl von Pool-DTUs erhöhen oder die Anzahl von Datenbanken für die Georeplikation im gleichen Pool verringern.

### <a name="changing-secondary-region-of-the-failover-group"></a>Ändern der sekundären Region der Failovergruppe

Zur Verdeutlichung der Änderungssequenz treffen wir die Annahme, dass Server A der primäre Server, Server B der vorhandene sekundäre Server und Server C der neue sekundäre Server in der dritten Region ist.  Führen Sie diese Schritte aus, um die Umstellung durchzuführen:

1. Erstellen Sie für jede Datenbank zusätzliche sekundäre Replikate von Server A auf Server C, indem Sie die [aktive Georeplikation](active-geo-replication-overview.md) verwenden. Jede Datenbank auf Server A verfügt über zwei sekundäre Replikate: eins auf Server B und eins auf Server C. So wird garantiert, dass die primären Datenbanken während der Umstellung geschützt bleiben.
2. Löschen Sie die Failovergruppe. An diesem Punkt treten für die Anmeldungen Fehler auf. Der Grund ist, dass die SQL-Aliase für die Failovergruppenlistener gelöscht wurden und das Gateway den Namen der Failovergruppe nicht erkennt.
3. Erstellen Sie die Failovergruppe mit dem gleichen Namen zwischen Server A und Server C. Für die Anmeldungen treten dann keine Fehler mehr auf.
4. Fügen Sie alle primären Datenbanken von Server A der neuen Failovergruppe hinzu.
5. Löschen Sie Server B. Alle Datenbanken auf Server B werden automatisch gelöscht.

### <a name="changing-primary-region-of-the-failover-group"></a>Ändern der primären Region der Failovergruppe

Zur Verdeutlichung der Änderungssequenz nehmen wir an, dass Server A der primäre Server, Server B der vorhandene sekundäre Server und Server C der neue primäre Server in der dritten Region ist.  Führen Sie diese Schritte aus, um die Umstellung durchzuführen:

1. Führen Sie ein geplantes Failover durch, um den primären Server auf B umzustellen. Server A wird zum neuen sekundären Server. Beim Failover kann es zu einem Ausfall mit einer Dauer von mehreren Minuten kommen. Die tatsächliche Dauer hängt von der Größe der Failovergruppe ab.
2. Erstellen Sie für jede Datenbank zusätzliche sekundäre Replikate von Server B auf Server C, indem Sie die [aktive Georeplikation](active-geo-replication-overview.md) verwenden. Jede Datenbank auf Server B verfügt über zwei sekundäre Replikate: eins auf Server A und eins auf Server C. So wird garantiert, dass die primären Datenbanken während der Umstellung geschützt bleiben.
3. Löschen Sie die Failovergruppe. An diesem Punkt treten für die Anmeldungen Fehler auf. Der Grund ist, dass die SQL-Aliase für die Failovergruppenlistener gelöscht wurden und das Gateway den Namen der Failovergruppe nicht erkennt.
4. Erstellen Sie die Failovergruppe mit dem gleichen Namen zwischen Server B und Server C. Für die Anmeldungen treten dann keine Fehler mehr auf.
5. Fügen Sie alle primären Datenbanken von Server B der neuen Failovergruppe hinzu.
6. Führen Sie für die Failovergruppe ein geplantes Failover durch, um B und C umzustellen. Server C wird zum primären und Server B zum sekundären Replikat. Alle sekundären Datenbanken auf Server A werden automatisch mit den primären Replikaten auf C verknüpft. Wie in Schritt 1 auch, kann es beim Failover zu einem Ausfall von mehreren Minuten kommen.
7. Löschen Sie Server A. Alle Datenbanken auf A werden automatisch gelöscht.

> [!IMPORTANT]
> Wenn die Failovergruppe gelöscht wird, werden auch die DNS-Einträge für die Listenerendpunkte gelöscht. An diesem Punkt ist nicht völlig ausgeschlossen, dass eine andere Person eine Failovergruppe oder einen Serveralias mit dem gleichen Namen erstellt. In diesem Fall können Sie diese Komponenten nicht mehr nutzen. Verwenden Sie keine generischen Namen für die Failovergruppe, um das Risiko zu minimieren.

## <a name="best-practices-for-sql-managed-instance"></a>Bewährte Methoden für SQL Managed Instance

Die Autofailover-Gruppe muss auf der primären Instanz konfiguriert werden und stellt eine Verbindung zur sekundären Instanz in einer anderen Azure-Region her.  Alle Datenbanken in der Instanz werden in der sekundären Instanz repliziert.

Das folgende Diagramm zeigt eine typische Konfiguration einer georedundanten Cloudanwendung mit einer verwalteten Instanz und Autofailover-Gruppe.

![Autofailoverdiagramm](./media/auto-failover-group-overview/auto-failover-group-mi.png)

> [!NOTE]
> Eine ausführliche schrittweise Anleitung zum Hinzufügen einer SQL Managed Instance zum Verwenden einer Failovergruppe finden Sie unter [Hinzufügen einer verwalteten Instanz zu einer Failovergruppe](../managed-instance/failover-group-add-instance-tutorial.md).

Wenn Ihre Anwendung SQL Managed Instance als Datenebene verwendet, beachten Sie beim Entwerfen für Geschäftskontinuität die folgenden allgemeinen Richtlinien:

### <a name="creating-the-secondary-instance"></a>Erstellen der sekundären Instanz

Um eine unterbrechungsfreie Verbindung mit der primären SQL Managed Instance nach einem Failover zu gewährleisten, müssen sich primäre und sekundäre Instanz in der gleichen DNS-Zone befinden. Es wird sichergestellt, dass das gleiche Zertifikat mit mehreren Domänen (SAN) zum Authentifizieren der Clientverbindungen mit einer der zwei Instanzen in der Failovergruppe verwendet werden kann. Wenn Ihre Anwendung für die Bereitstellung in der Produktionsumgebung bereit ist, erstellen Sie eine sekundäre SQL Managed Instance in einer anderen Region, und stellen Sie sicher, dass sie die gleiche DNS-Zone wie die primäre SQL Managed Instance verwendet. Hierzu können Sie den optionalen Parameter `DNS Zone Partner` über das Azure-Portal, PowerShell oder die REST-API angegeben.

> [!IMPORTANT]
> Die erste im Subnetz erstellte verwaltete Instanz bestimmt die DNS-Zone für alle nachfolgenden Instanzen im gleichen Subnetz. Dies bedeutet, dass zwei Instanzen aus dem gleichen Subnetz nicht zu verschiedenen DNS-Zonen gehören können.

Weitere Informationen zum Erstellen der sekundären SQL Managed Instance in derselben DNS-Zone wie die primäre Instanz finden Sie unter [Erstellen einer sekundären verwalteten Instanz](../managed-instance/failover-group-add-instance-tutorial.md#create-a-secondary-managed-instance).

### <a name="using-geo-paired-regions"></a>Verwenden von geografisch gekoppelten Regionen

Stellen Sie beide verwalteten Instanzen aus Leistungsgründen in [gekoppelten Regionen](../../best-practices-availability-paired-regions.md) bereit. Verwaltete Instanzen in geografisch gekoppelten Regionen weisen eine deutlich bessere Leistung auf als Instanzen in nicht gekoppelten Regionen. 

### <a name="enabling-replication-traffic-between-two-instances"></a>Aktivieren des Replikationsdatenverkehrs zwischen zwei Instanzen

Da jede Instanz in einem eigenen VNET isoliert ist, muss der bidirektionale Datenverkehr zwischen diesen VNETs zugelassen werden. Informationen dazu finden Sie unter [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>Erstellen einer Failovergruppe zwischen verwalteten Instanzen in verschiedenen Abonnements

Sie können eine Failovergruppe zwischen SQL Managed Instances in zwei verschiedenen Abonnements erstellen, solange Abonnements dem gleichen [Azure Active Directory-Mandanten](../../active-directory/fundamentals/active-directory-whatis.md#terminology) zugeordnet sind. Wenn Sie die PowerShell-API verwenden, können Sie dazu den Parameter `PartnerSubscriptionId` für die sekundäre SQL Managed Instance angeben. Wenn Sie die REST-API verwenden, kann jede im Parameter `properties.managedInstancePairs` enthaltene Instanz-ID eine eigene Abonnement-ID haben.
  
> [!IMPORTANT]
> Das Erstellen von Failovergruppen in verschiedenen Abonnements wird im Azure-Portal nicht unterstützt. Für vorhandene Failovergruppen, die verschiedene Abonnements und/oder Ressourcengruppen umfassen, kann das Failover zudem nicht von der primären SQL Managed Instance aus manuell über das Portal initiiert werden. Initiieren Sie das Failover stattdessen über die sekundäre Geoinstanz.

### <a name="managing-failover-to-secondary-instance"></a>Verwalten des Failovers auf der sekundären Instanz

Die Failovergruppe verwaltet das Failover aller Datenbanken in der SQL Managed Instance. Beim Erstellen einer Gruppe wird jede Datenbank in der Instanz automatisch in der sekundären SQL Managed Instance geografisch repliziert. Sie können Failovergruppen nicht verwenden, um ein Teilfailover einer Untergruppe von Datenbanken zu initiieren.

> [!IMPORTANT]
> Wird eine Datenbank aus der primären SQL Managed Instance entfernt, wird sie automatisch auch in der georeplizierten sekundären SQL Managed Instance gelöscht.

### <a name="using-read-write-listener-for-oltp-workload"></a>Verwenden eines Lese-/Schreib-Listeners für OLTP-Workloads

Verwenden Sie beim Durchführen von OLTP-Vorgängen `<fog-name>.zone_id.database.windows.net` als Server-URL, damit die Verbindungen automatisch an die primäre Datenbank weitergeleitet werden. Diese URL wird nach dem Failover nicht geändert. Das Failover umfasst die Aktualisierung von DNS-Einträgen, damit die Clientverbindungen erst dann an die neue primäre Datenbank weitergeleitet werden, wenn der Client-DNS-Cache aktualisiert wurde. Da die sekundäre Instanz die gleiche DNS-Zone wie die primäre Instanz verwendet, kann die Clientanwendung mit dem gleichen SAN-Zertifikat erneut eine Verbindung herstellen.

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>Verwenden eines schreibgeschützten Listeners zum Herstellen einer Verbindung mit der sekundären Instanz

Wenn Sie über eine logisch isolierte schreibgeschützte Workload verfügen, die gegenüber einer bestimmten Veraltung tolerant ist, können Sie die sekundäre Datenbank in der Anwendung verwenden. Verwenden Sie zum direkten Verbinden mit der georeplizierten sekundären Datenbank `<fog-name>.secondary.<zone_id>.database.windows.net` als Server-URL, damit die Verbindung direkt mit der georeplizierten sekundären Datenbank hergestellt wird.

> [!NOTE]
> Auf den Dienstebenen „Premium“, „Unternehmenskritisch“ und „Hyperscale“ unterstützt SQL-Datenbank die Verwendung [schreibgeschützter Replikate](read-scale-out.md) für die Ausführung schreibgeschützter Abfrageworkloads unter Verwendung der Kapazität einzelner oder mehrerer schreibgeschützter Replikate (mit dem Parameter `ApplicationIntent=ReadOnly` in der Verbindungszeichenfolge). Wenn Sie eine georeplizierte sekundäre Datenbank konfiguriert haben, können Sie mit dieser Funktion eine Verbindung entweder mit einem schreibgeschützten Replikat am primären Standort oder am geografisch replizierten Standort herstellen.
>
> - Verwenden Sie `ApplicationIntent=ReadOnly` und `<fog-name>.<zone_id>.database.windows.net`, um eine Verbindung mit einem schreibgeschützten Replikat am primären Standort herzustellen.
> - Wenn Sie eine Verbindung mit einem schreibgeschützten Replikat am sekundären Standort herstellen möchten, verwenden Sie `ApplicationIntent=ReadOnly` und `<fog-name>.secondary.<zone_id>.database.windows.net`.

### <a name="preparing-for-performance-degradation"></a>Vorbereiten auf Leistungsbeeinträchtigungen

Eine typische Azure-Anwendung nutzt mehrere Azure-Dienste und besteht aus mehreren Komponenten. Das automatisierte Failover der Failovergruppe wird ausschließlich anhand des Status der Azure SQL-Komponenten ausgelöst. Andere Azure-Dienste in der primären Region sind vom Ausfall ggf. nicht betroffen, und es kann sein, dass die entsprechenden Komponenten in dieser Region weiterhin verfügbar sind. Nachdem die primären Datenbanken auf die sekundäre Region umgestellt wurden, erhöht sich unter Umständen die Wartezeit zwischen den abhängigen Komponenten. Gewährleisten Sie die Redundanz aller Anwendungskomponenten in der sekundären Region, und führen Sie Failover für Anwendungskomponenten zusammen mit der Datenbank durch, um zu vermeiden, dass die Leistung der Anwendung durch höhere Wartezeit beeinträchtigt wird. Halten Sie sich bei der Konfiguration an die [Richtlinien für die Netzwerksicherheit](#failover-groups-and-network-security), um die Konnektivität mit der Datenbank in der sekundären Region sicherzustellen.

### <a name="preparing-for-data-loss"></a>Vorbereiten auf Datenverlust

Wenn ein Ausfall erkannt wird, wird automatisch ein Lese-/Schreibfailover ausgelöst, sofern nach unserem Erkenntnissen keine Daten verloren gegangen sind. Andernfalls wird das Failover für den mithilfe von `GracePeriodWithDataLossHours` angegebenen Zeitraum zurückgestellt. Wenn Sie `GracePeriodWithDataLossHours` angegeben haben, müssen Sie auf Datenverluste vorbereitet sein. Bei Ausfällen priorisiert Azure grundsätzlich die Verfügbarkeit. Wenn keine Datenverluste auftreten dürfen, legen Sie „GracePeriodWithDataLossHours“ auf einen ausreichend hohen Wert (beispielsweise 24 Stunden) fest, oder deaktivieren Sie das automatische Failover.

Die DNS-Aktualisierung des Lese-/Schreib-Listeners erfolgt sofort nach dem Initiieren des Failovers. Bei diesem Vorgang gehen keine Daten verloren. Allerdings kann der Prozess zum Wechseln der Datenbankrollen unter normalen Bedingungen bis zu 5 Minuten dauern. Bis er abgeschlossen ist, sind einige Datenbanken in der neuen primären Instanz noch schreibgeschützt. Wenn ein Failover mithilfe von PowerShell initiiert wird, ist der Vorgang für den Wechsel der primären Replikatrolle synchron. Wird es über das Azure-Portal initiiert, wird auf der Benutzeroberfläche der Abschlussstatus angegeben. Bei Initiierung über die REST-API verwenden Sie den Standardabfragemechanismus von Azure Resource Manager, um den Vorgang auf seinen Abschluss zu überwachen.

> [!IMPORTANT]
> Verwenden Sie manuelles Gruppenfailover, um primäre Datenbanken wieder an den ursprünglichen Standort zu verschieben. Sobald der Ausfall, der das Failover verursacht hat, behoben ist, können Sie die primären Datenbanken an den ursprünglichen Standort verschieben. Dazu sollten Sie ein manuelles Failover der Gruppe initiieren.
  
### <a name="changing-secondary-region-of-the-failover-group"></a>Ändern der sekundären Region der Failovergruppe

Angenommen, Instanz A ist die primäre Instanz, Instanz B die vorhandene sekundäre Instanz und Instanz C die neue sekundäre Instanz in der dritten Region.  Führen Sie diese Schritte aus, um die Umstellung durchzuführen:

1. Erstellen Sie Instanz C mit der gleichen Größe wie A und in derselben DNS-Zone.
2. Löschen Sie die Failovergruppe zwischen den Instanzen A und B. An diesem Punkt treten bei Anmeldungen Fehler auf, weil die SQL-Aliase für die Failovergruppenlistener gelöscht wurden und das Gateway den Namen der Failovergruppe nicht erkennt. Die sekundären Datenbanken werden von den primären Replikaten getrennt und werden zu Datenbanken mit Lese-/Schreibzugriff.
3. Erstellen Sie eine Failovergruppe mit dem gleichen Namen zwischen Instanz A und C. Befolgen Sie die Anleitung im [Tutorial zu Failovergruppen mit SQL Managed Instance](../managed-instance/failover-group-add-instance-tutorial.md). Dies ist ein Vorgang, bei dem der Umfang der Daten eine Rolle spielt. Er ist abgeschlossen, wenn für alle Datenbanken von Instanz A das Seeding und die Synchronisierung durchgeführt wurde.
4. Löschen Sie Instanz B, wenn sie nicht benötigt wird, um unnötige Kosten zu vermeiden.

> [!NOTE]
> Nach Schritt 2 und bis zum Abschluss von Schritt 3 sind die Datenbanken auf Instanz A vor dem Ausfall von Instanz A aufgrund eines Katastrophenfalls ungeschützt.

### <a name="changing-primary-region-of-the-failover-group"></a>Ändern der primären Region der Failovergruppe

Angenommen, Instanz A ist die primäre Instanz, Instanz B die vorhandene sekundäre Instanz und Instanz C die neue primäre Instanz in der dritten Region.  Führen Sie diese Schritte aus, um die Umstellung durchzuführen:

1. Erstellen Sie Instanz C mit der gleichen Größe wie B und in derselben DNS-Zone.
2. Stellen Sie eine Verbindung mit Instanz B her, und führen Sie ein manuelles Failover durch, um die primäre Instanz auf B umzustellen. Instanz A wird automatisch zur neuen sekundären Instanz.
3. Löschen Sie die Failovergruppe zwischen den Instanzen A und B. An diesem Punkt treten bei Anmeldungen Fehler auf, weil die SQL-Aliase für die Failovergruppenlistener gelöscht wurden und das Gateway den Namen der Failovergruppe nicht erkennt. Die sekundären Datenbanken werden von den primären Replikaten getrennt und werden zu Datenbanken mit Lese-/Schreibzugriff.
4. Erstellen Sie eine Failovergruppe mit dem gleichen Namen zwischen Instanz A und C. Befolgen Sie die Anleitung im [Tutorial zu Failovergruppen mit verwalteten Instanzen](../managed-instance/failover-group-add-instance-tutorial.md). Dies ist ein Vorgang, bei dem der Umfang der Daten eine Rolle spielt. Er ist abgeschlossen, wenn für alle Datenbanken von Instanz A das Seeding und die Synchronisierung durchgeführt wurde.
5. Löschen Sie Instanz A, wenn sie nicht benötigt wird, um unnötige Kosten zu vermeiden.

> [!CAUTION]
> Nach Schritt 3 und bis zum Abschluss von Schritt 4 sind die Datenbanken auf Instanz A vor dem Ausfall von Instanz A aufgrund eines Katastrophenfalls ungeschützt.

> [!IMPORTANT]
> Wenn die Failovergruppe gelöscht wird, werden auch die DNS-Einträge für die Listenerendpunkte gelöscht. An diesem Punkt ist nicht völlig ausgeschlossen, dass eine andere Person eine Failovergruppe oder einen Serveralias mit dem gleichen Namen erstellt. In diesem Fall können Sie diese Komponenten nicht mehr nutzen. Verwenden Sie keine generischen Namen für die Failovergruppe, um das Risiko zu minimieren.

### <a name="enable-scenarios-dependent-on-objects-from-the-system-databases"></a>Aktivieren von Szenarien in Abhängigkeit von Objekten in den Systemdatenbanken
Systemdatenbanken werden nicht in die sekundäre Instanz in einer Failovergruppe repliziert. Um Szenarien zu ermöglichen, die von Objekten in den Systemdatenbanken der sekundären Instanz abhängen, stellen Sie sicher, dass in der sekundären Instanz die gleichen Objekte angelegt werden. Wenn Sie beispielsweise in der sekundären Instanz die gleichen Anmeldungen nutzen möchten, stellen Sie sicher, dass sie mit identischer SID erstellt werden. 
```SQL
-- Code to create login on the secondary instance
CREATE LOGIN foo WITH PASSWORD = '<enterStrongPasswordHere>', SID = <login_sid>;
``` 


## <a name="failover-groups-and-network-security"></a>Failovergruppen und Netzwerksicherheit

Für einige Anwendungen erfordern die Sicherheitsregeln, dass der Netzwerkzugriff auf die Datenebene auf eine bestimmte Komponente oder Komponenten wie virtueller Computer, Webdienst usw. beschränkt ist. Diese Anforderung stellt für den Entwurf der Geschäftskontinuität und die Verwendung der Failovergruppen eine Herausforderung dar. Berücksichtigen Sie bei der Implementierung eines solchen eingeschränkten Zugriffs die folgenden Optionen.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Verwendung von Failovergruppen und VNET-Regeln

Wenn Sie [VNET-Dienstendpunkte und -Regeln](vnet-service-endpoint-rule-overview.md) verwenden, um den Zugriff auf Ihre Datenbank in SQL-Datenbank oder SQL Managed Instance einzuschränken, denken Sie daran, dass jeder VNET-Dienstendpunkt nur für eine einzige Azure-Region gilt. Der Endpunkt ermöglicht anderen Regionen nicht das Akzeptieren von Nachrichten aus dem Subnetz. Aus diesem Grund können nur die Clientanwendungen, die in der gleichen Region bereitgestellt werden, eine Verbindung mit der primären Datenbank herstellen. Da das Failover dazu führt, dass die SQL-Datenbank-Clientsitzungen zu dem Server in der anderen (sekundären) Region umgeleitet werden, treten bei diesen Sitzungen Fehler auf, wenn sie von Clients ausgehen, die sich außerhalb dieser Region befinden. Aus diesem Grund kann die Richtlinie für automatisches Failover nicht aktiviert werden, wenn die beteiligten Server oder Instanzen in den VNET-Regeln enthalten sind. Um das manuelle Failover zu unterstützen, gehen Sie folgendermaßen vor:

1. Stellen Sie die redundanten Kopien der Front-End-Komponenten der Anwendung (Webdienst, VMs usw.) in der sekundären Region bereit.
2. Konfigurieren Sie die [VNET-Regeln](vnet-service-endpoint-rule-overview.md) individuell für den primären und sekundären Server.
3. Aktivieren Sie das [Front-End-Failover durch eine Traffic Manager-Konfiguration](designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime).
4. Initiieren Sie ein manuelles Failover, wenn der Ausfall erkannt wird. Diese Option ist für die Anwendungen optimiert, die konsistente Latenz zwischen Front-End und Datenebene erfordern, und unterstützt die Wiederherstellung, wenn Front-End, Datenebene oder beide vom Ausfall betroffen sind.

> [!NOTE]
> Stellen Sie bei Verwendung des **schreibgeschützten Listeners** für den Lastenausgleich einer schreibgeschützten Workload sicher, dass diese Workload auf einem virtuellen Computer oder einer anderen Ressource in der sekundären Region ausgeführt wird, damit sie eine Verbindung mit der sekundären Datenbank herstellen kann.

### <a name="use-failover-groups-and-firewall-rules"></a>Verwenden von Failovergruppen und Firewallregeln

Wenn Ihr Geschäftskontinuitätsplan das Durchführen eines Failovers mithilfe von Gruppen mit automatischem Failover erfordert, können Sie den Zugriff auf Ihre Datenbank in SQL-Datenbank mithilfe der herkömmlichen Firewallregeln einschränken. Um das automatische Failover zu unterstützen, gehen Sie folgendermaßen vor:

1. [Erstellen Sie eine öffentliche IP-Adresse.](../../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Erstellen Sie einen öffentlichen Lastenausgleich](../../load-balancer/quickstart-load-balancer-standard-public-portal.md), und weisen Sie ihm die öffentliche IP-Adresse zu.
3. [Erstellen Sie ein virtuelles Netzwerk und die VMs](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) für Ihre Front-End-Komponenten.
4. [Erstellen Sie eine Netzwerksicherheitsgruppe](../../virtual-network/network-security-groups-overview.md), und konfigurieren Sie eingehende Verbindungen.
5. Stellen Sie mithilfe des [Diensttags](../../virtual-network/network-security-groups-overview.md#service-tags) „Sql“ sicher, dass die ausgehenden Verbindungen für Azure SQL-Datenbank geöffnet sind.
6. Erstellen Sie eine [SQL-Datenbank-Firewallregel](firewall-configure.md), um eingehenden Datenverkehr von der öffentlichen IP-Adresse, die Sie in Schritt 1 erstellt haben, zuzulassen.

Weitere Informationen zur Konfiguration des ausgehenden Zugriffs und IP-Adresse, die in den Firewallregeln verwendet werden soll, finden Sie unter [Ausgehende Verbindungen in Azure](../../load-balancer/load-balancer-outbound-connections.md).

Die oben genannte Konfiguration sorgt dafür, dass das automatische Failover keine Verbindungen von den Front-End-Komponenten blockiert, und setzt voraus, dass die Anwendung die längeren Wartezeiten zwischen dem Front-End und der Datenebene tolerieren kann.

> [!IMPORTANT]
> Um die Geschäftskontinuität bei regionalen Ausfällen zu gewährleisten, müssen Sie die geografische Redundanz sowohl für die Front-End-Komponenten als auch die Datenbanken sicherstellen.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Aktivieren der geografischen Replikation zwischen verwalteten Instanzen und ihren VNETs

Wenn Sie eine Failovergruppe zwischen primären und sekundären SQL Managed Instances in zwei verschiedenen Regionen einrichten, wird jede Instanz mit einem unabhängigen virtuellen Netzwerk isoliert. Um Replikationsdatenverkehr zwischen diesen VNETs zuzulassen, müssen diese Voraussetzungen erfüllt sein:

- Die beiden Instanzen von SQL Managed Instance müssen sich in verschiedenen Azure-Regionen befinden.
- Die beiden Instanzen von SQL Managed Instance müssen dieselbe Dienstebene und Speichergröße aufweisen.
- Ihre sekundäre Instanz von SQL Managed Instances muss leer sein (ohne Benutzerdatenbanken).
- Die von den Instanzen von SQL Managed Instance verwendeten virtuellen Netzwerke müssen per [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) oder [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) verbunden werden. Wenn zwei virtuelle Netzwerke über ein lokales Netzwerk verbunden sind, müssen Sie sicherstellen, dass die Ports 5022 und 11000-11999 nicht durch Firewallregeln blockiert werden. Globales VNET-Peering wird unterstützt. Die einzige Einschränkung ist im folgenden Hinweis beschrieben.

   > [!IMPORTANT]
   > [Am 22.09.2020 haben wir globales Peering virtueller Netzwerke für neu erstellte virtuelle Cluster angekündigt](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Dies bedeutet, dass globales Peering virtueller Netzwerke sowohl für SQL Managed Instances, die nach dem Ankündigungsdatum in leeren Subnetzen erstellt wurden, als auch für alle späteren verwalteten Instanzen, die in diesen Subnetzen erstellt werden, unterstützt wird. Für alle anderen SQL Managed Instances ist die Peeringunterstützung aufgrund der [Einschränkungen beim globalen Peering virtueller Netzwerke](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) auf die Netzwerke in derselben Region beschränkt. Ausführliche Informationen finden Sie im entsprechenden Abschnitt des Artikels [Azure Virtual Network – häufig gestellte Fragen](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). 

- Die VNETs der beiden SQL Managed Instances dürfen keine überlappenden IP-Adressen aufweisen.
- Sie müssen die Netzwerksicherheitsgruppen (NSGs) so einrichten, dass die Ports 5022 und der Bereich von 11.000 bis 12.000 für ein- und ausgehende Verbindungen vom Subnetz der jeweils anderen verwalteten Instanz geöffnet sind. Hierdurch wird der Replikationsdatenverkehr zwischen den Instanzen zugelassen.

   > [!IMPORTANT]
   > Falsch konfigurierte NSG-Sicherheitsregeln führen zu unterbrochenen Datenbankkopiervorgängen.

- Die sekundäre SQL Managed Instance ist mit der richtigen DNS-Zonen-ID konfiguriert. Die DNS-Zone ist eine Eigenschaft einer SQL Managed Instance und eines zugrunde liegenden virtuellen Clusters, und ihre ID ist in der Hostnamenadresse enthalten. Die Zonen-ID wird als zufällige Zeichenfolge generiert, wenn die erste SQL Managed Instance in jedem VNET erstellt und die gleiche ID alle anderen Instanzen im selben Subnetz zugewiesen wird. Nach der Zuweisung kann die DNS-Zone nicht geändert werden. SQL Managed Instances in der gleichen Failovergruppe müssen die DNS-Zone gemeinsam nutzen. Sie erreichen dies durch Übergeben der Zonen-ID der primären Instanz als Wert des DnsZonePartner-Parameters beim Erstellen der sekundären Instanz.

   > [!NOTE]
   > Ein ausführliches Tutorial zum Konfigurieren von Failovergruppen mit einer SQL Managed Instance finden Sie unter [Hinzufügen einer SQL Managed Instance zu einer Failovergruppe](../managed-instance/failover-group-add-instance-tutorial.md).

## <a name="upgrading-or-downgrading-a-primary-database"></a>Upgrade oder Downgrade einer primären Datenbank

Sie können für eine primäre Datenbank ein Upgrade oder Downgrade auf eine andere Computegröße (innerhalb der gleichen Dienstebene; nicht zwischen „Universell“ und „Unternehmenskritisch“) ausführen, ohne die Verbindung mit sekundären Datenbanken zu trennen. Bei einem Upgrade wird empfohlen, zuerst das Upgrade für alle sekundären Datenbanken und anschließend das Upgrade für die primäre Datenbank auszuführen. Drehen Sie bei einem Downgrade die Reihenfolge um: Führen Sie zuerst das Downgrade für die primäre Datenbank und anschließend das Downgrade für alle sekundären Datenbanken aus. Wenn Sie ein Upgrade oder Downgrade der Datenbank auf eine andere Dienstebene durchführen, wird diese Empfehlung erzwungen.

Diese Reihenfolge ist besonders zur Vermeidung des Überladens der sekundären Datenbank auf einer niedrigeren SKU empfehlenswert, damit nicht während des Upgrade- oder Downgradevorgangs ein erneutes Seeding durchgeführt werden muss. Sie können dieses Problem auch vermeiden, indem Sie die primäre Datenbank mit Schreibschutz versehen. Das geht allerdings zu Lasten aller Lese-/Schreibworkloads für die primäre Datenbank.

> [!NOTE]
> Wenn Sie eine sekundäre Datenbank als Teil der Konfiguration der Failovergruppe erstellt haben, sollten Sie kein Downgrade der sekundären Datenbank durchführen. So wird sichergestellt, dass Ihre Datenebene nach dem Aktivieren des Failovers ausreichende Kapazität zum Verarbeiten des normalen Workloads hat.

## <a name="preventing-the-loss-of-critical-data"></a>Verhindern des Verlusts wichtiger Daten

Aufgrund der hohen Latenz von WANs wird für die fortlaufende Kopie ein asynchroner Replikationsmechanismus verwendet. Asynchrone Replikation macht Datenverlust unvermeidlich, sobald ein Ausfall auftritt. Bei einigen Anwendung dürfen jedoch ggf. keine Daten verloren gehen. Um diese kritischen Aktualisierungen zu schützen, kann ein Anwendungsentwickler die Systemprozedur [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) aufrufen, unmittelbar nachdem der Commit für die Transaktion erfolgt ist. Das Aufrufen von `sp_wait_for_database_copy_sync` blockiert den aufrufenden Thread so lange, bis die letzte Transaktion mit erfolgtem Commit in die sekundäre Datenbank übertragen wurde. Es wird jedoch nicht abgewartet, bis die übertragenen Transaktionen wiedergegeben und in der sekundären Datenbank committet werden. `sp_wait_for_database_copy_sync` ist auf ein bestimmtes fortlaufendes „Link kopieren“ begrenzt. Jeder Benutzer mit den Rechten zum Herstellen der Verbindung mit der primären Datenbank kann diese Prozedur aufrufen.

> [!NOTE]
> `sp_wait_for_database_copy_sync` verhindert Datenverlust nach einem Failover, garantiert aber nicht die vollständige Synchronisierung für den Lesezugriff. Die vom Aufruf einer `sp_wait_for_database_copy_sync`-Prozedur verursachte Verzögerung kann signifikant sein und hängt von der Größe des Transaktionsprotokolls zum Zeitpunkt des Aufrufs ab.

## <a name="failover-groups-and-point-in-time-restore"></a>Failovergruppen und Point-in-Time-Wiederherstellung

Informationen zur Verwendung der Point-in-Time-Wiederherstellung mit Failovergruppen finden Sie unter [Point-in-Time-Wiederherstellung](recovery-using-backups.md#point-in-time-restore).

## <a name="limitations-of-failover-groups"></a>Einschränkungen von Failovergruppen

Bedenken Sie dabei folgende Einschränkungen:

- Failovergruppen können nicht zwischen zwei Servern oder Instanzen in denselben Azure-Regionen erstellt werden.
- Failovergruppen können nicht umbenannt werden. Sie müssen die Gruppe löschen und unter einem anderen Namen neu erstellen.
- Die Datenbankumbenennung wird für Instanzen in der Failovergruppe nicht unterstützt. Sie müssen eine Failovergruppe vorübergehend löschen, um eine Datenbank umzubenennen.
- Systemdatenbanken werden nicht in die sekundäre Instanz in einer Failovergruppe repliziert. Daher sind Szenarien, die von Objekten aus den Systemdatenbanken abhängen, in der sekundären Instanz nicht möglich, es sei denn, die Objekte werden manuell in der sekundären Instanz erstellt.

## <a name="programmatically-managing-failover-groups"></a>Programmgesteuertes Verwalten von Failovergruppen

Wie bereits zuvor erwähnt, können Gruppen für automatisches Failover und aktive Georeplikation auch programmgesteuert mit Azure PowerShell und der REST-API verwaltet werden. Die folgenden Tabellen beschreiben den verfügbaren Satz von Befehlen. Die aktive Georeplikation umfasst eine Reihe von Azure Resource Manager-APIs für die Verwaltung. Hierzu zählen unter anderem die [Azure SQL-Datenbank-REST-API](/rest/api/sql/) und [Azure PowerShell-Cmdlets](/powershell/azure/). Diese APIs erfordern die Verwendung von Ressourcengruppen und unterstützen die rollenbasierte Zugriffssteuerung (Azure RBAC) in Azure. Weitere Informationen zur Implementierung von Zugriffsrollen finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)](../../role-based-access-control/overview.md).

### <a name="manage-sql-database-failover"></a>Verwalten des Failovers von SQL-Datenbank

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Cmdlet | BESCHREIBUNG |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Dieser Befehl erstellt eine Failovergruppe und registriert sie auf primären und sekundären Servern.|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Entfernt eine Failovergruppe vom Server. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Ruft die Konfiguration einer Failovergruppe ab. |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Ändert die Konfiguration einer Failovergruppe. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Löst das Failover einer Failovergruppe auf den sekundären Server aus. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Fügt einer Failovergruppe eine oder mehrere Datenbanken hinzu.|

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

| Get-Help | BESCHREIBUNG |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Dieser Befehl erstellt eine Failovergruppe und registriert sie auf primären und sekundären Servern.|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Entfernt eine Failovergruppe vom Server. |
| [az sql failover-group show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Ruft die Konfiguration einer Failovergruppe ab. |
| [az sql failover-group update](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Ändert die Konfiguration einer Failovergruppe bzw. fügt einer Failovergruppe mindestens eine Datenbank hinzu.|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Löst das Failover einer Failovergruppe auf den sekundären Server aus. |

# <a name="rest-api"></a>[REST-API](#tab/rest-api)

| API | BESCHREIBUNG |
| --- | --- |
| [Create or Update Failover Group](/rest/api/sql/failovergroups/createorupdate) | Erstellt oder aktualisiert eine Failovergruppe. |
| [Delete Failover Group](/rest/api/sql/failovergroups/delete) | Entfernt eine Failovergruppe vom Server. |
| [Failover (Planned)](/rest/api/sql/failovergroups/failover) | Löst das Failover vom aktuellen primären Server auf den sekundären Server mit vollständiger Datensynchronisierung aus.|
| [Force Failover Allow Data Loss](/rest/api/sql/failovergroups/forcefailoverallowdataloss) | Löst das Failover vom aktuellen primären Server auf den sekundären Server ohne Datensynchronisierung aus. Dieser Vorgang kann zu Datenverlust führen. |
| [Get Failover Group](/rest/api/sql/failovergroups/get) | Ruft die Konfiguration einer Failovergruppe ab. |
| [List Failover Groups By Server](/rest/api/sql/failovergroups/listbyserver) | Listet die Failovergruppen eines Servers auf. |
| [Update Failover Group](/rest/api/sql/failovergroups/update) | Aktualisiert die Konfiguration einer Failovergruppe. |

---

### <a name="manage-sql-managed-instance-failover"></a>Verwalten des Failovers von SQL Managed Instance


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Cmdlet | BESCHREIBUNG |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Dieser Befehl erstellt eine Failovergruppe und registriert sie auf primären und sekundären Instanzen.|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Ändert die Konfiguration einer Failovergruppe.|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Ruft die Konfiguration einer Failovergruppe ab.|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Löst das Failover einer Failovergruppe auf der sekundären Instanz aus.|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Entfernt eine Failovergruppe.|


# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

| Get-Help | BESCHREIBUNG |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Dieser Befehl erstellt eine Failovergruppe und registriert sie auf primären und sekundären Servern.|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Entfernt eine Failovergruppe vom Server. |
| [az sql failover-group show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Ruft die Konfiguration einer Failovergruppe ab. |
| [az sql failover-group update](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Ändert die Konfiguration einer Failovergruppe bzw. fügt einer Failovergruppe mindestens eine Datenbank hinzu.|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Löst das Failover einer Failovergruppe auf den sekundären Server aus. |

# <a name="rest-api"></a>[REST-API](#tab/rest-api)

| API | BESCHREIBUNG |
| --- | --- |
| [Create or Update Failover Group](/rest/api/sql/instancefailovergroups/createorupdate) | Erstellt oder aktualisiert die Konfiguration einer Failovergruppe. |
| [Delete Failover Group](/rest/api/sql/instancefailovergroups/delete) | Entfernt eine Failovergruppe von der Instanz. |
| [Failover (Planned)](/rest/api/sql/instancefailovergroups/failover) | Löst ein Failover von der aktuellen primären Instanz auf diese Instanz mit vollständiger Datensynchronisierung aus. |
| [Force Failover Allow Data Loss](/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | Löst das Failover von der aktuellen primären Instanz auf die sekundäre Instanz ohne Datensynchronisierung aus. Dieser Vorgang kann zu Datenverlust führen. |
| [Get Failover Group](/rest/api/sql/instancefailovergroups/get) | Ruft die Konfiguration einer Failovergruppe ab. |
| [List Failover Groups - List By Location](/rest/api/sql/instancefailovergroups/listbylocation) | Listet die Failovergruppen an einem Standort auf. |

---

## <a name="next-steps"></a>Nächste Schritte

- Ausführliche Tutorials finden Sie unter:
  - [Hinzufügen von SQL-Datenbank zu einer Failovergruppe](failover-group-add-single-database-tutorial.md)
  - [Hinzufügen eines Pools für elastische Datenbanken zu einer Failovergruppe](failover-group-add-elastic-pool-tutorial.md)
  - [Hinzufügen einer SQL Managed Instance zu einer Failovergruppe](../managed-instance/failover-group-add-instance-tutorial.md)
- Beispielskripts:
  - [Verwenden von PowerShell zum Konfigurieren der aktiven Georeplikation für Azure SQL-Datenbank](scripts/setup-geodr-and-failover-database-powershell.md)
  - [Verwenden von PowerShell zum Konfigurieren der aktiven Georeplikation für eine Pooldatenbank in Azure SQL-Datenbank](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
  - [Hinzufügen einer Azure SQL-Datenbank-Instanz zu einer Failovergruppe mithilfe von PowerShell](scripts/add-database-to-failover-group-powershell.md)
- Eine Übersicht und verschiedene Szenarien zum Thema Geschäftskontinuität finden Sie unter [Übersicht über die Geschäftskontinuität](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- Informationen über automatisierte Sicherungen von Azure SQL-Datenbanken finden Sie unter [Automatisierte SQL-Datenbanksicherungen](automated-backups-overview.md).
- Informationen zum Verwenden automatisierter Sicherungen für die Wiederherstellung finden Sie unter [Wiederherstellen einer Datenbank aus vom Dienst initiierten Sicherungen](recovery-using-backups.md).
- Weitere Informationen zu Authentifizierungsanforderungen für einen neuen primären Server und die Datenbank finden Sie unter [Verwalten der Sicherheit der Azure SQL-Datenbank nach der Notfallwiederherstellung](active-geo-replication-security-configure.md).

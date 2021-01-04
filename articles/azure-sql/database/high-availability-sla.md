---
title: Hochverfügbarkeit
titleSuffix: Azure SQL Database and SQL Managed Instance
description: Erfahren Sie mehr über die Hochverfügbarkeitsfunktionen des Azure SQL-Datenbank- und SQL Managed Instance-Diensts.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: sashan
ms.author: sashan
ms.reviewer: sstein, sashan
ms.date: 10/28/2020
ms.openlocfilehash: 15067a046d8adc0ba38101bbe24cdc48cd433d56
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095439"
---
# <a name="high-availability-for-azure-sql-database-and-sql-managed-instance"></a>Hochverfügbarkeit für Azure SQL-Datenbank und SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Die Architektur für Hochverfügbarkeit in Azure SQL-Datenbank und SQL Managed Instance sorgt dafür, dass Ihre Datenbank mindestens 99,99 Prozent der Zeit betriebsbereit ist, ohne dass Sie sich Gedanken über Auswirkungen von Wartungsarbeiten und Ausfällen machen müssen. (Weitere Informationen zur spezifischen SLA für verschiedene Tarife finden Sie unter [SLA für Azure SQL-Datenbank und SQL Managed Instance](https://azure.microsoft.com/support/legal/sla/sql-database/)). Azure verarbeitet automatisch wichtige Wartungsaufgaben, z.B. Patches, Sicherungen, Windows- und Azure SQL-Upgrades, aber auch ungeplante Ereignisse wie Ausfälle der zugrunde liegenden Hardware oder Software oder Netzwerkfehler.  Wenn die zugrunde liegende Datenbank in Azure SQL-Datenbank gepatcht oder ein Failover ausgeführt wird, ist im Allgemeinen keine Ausfallzeit festzustellen, wenn Sie in Ihrer App [Wiederholungslogik nutzen](develop-overview.md#resiliency). SQL-Datenbank und SQL Managed Instance können auch unter den kritischsten Umständen schnell wiederhergestellt werden. So wird sichergestellt, dass Ihre Daten immer verfügbar sind.

Die Hochverfügbarkeitslösung soll sicherstellen, dass Daten, für die ein Commit ausgeführt wurde, nie aufgrund von Fehlern verloren gehen, dass sich Wartungsvorgänge nicht auf Ihre Workload auswirken und dass die Datenbank keinen Single Point of Failure in der Softwarearchitektur darstellt. Es gibt keine Wartungsfenster oder Ausfallzeiten, aufgrund derer Sie die Workload während der Aktualisierung oder Wartung der Datenbank beenden müssen.

Es gibt zwei Architekturmodelle für Hochverfügbarkeit:

- Das **Standardverfügbarkeitsmodell**, das auf der Trennung der Compute- und Speicherebene basiert.  Es basiert auf der Hochverfügbarkeit und der Zuverlässigkeit der Remotespeicherebene. Diese Architektur ist auf budgetgebundene Geschäftsanwendungen ausgelegt, die bei Wartungsarbeiten gewisse Leistungseinbußen tolerieren können.
- Das **Premium-Verfügbarkeitsmodell**, das auf einem Cluster von Datenbank-Engine-Prozessen basiert. Dieses beruht auf dem Umstand, dass stets ein Quorum von verfügbaren Datenbank-Engine-Knoten vorhanden ist. Diese Architektur ist auf unternehmenskritische Anwendungen mit hoher E/A-Leistung und einer hohen Transaktionsrate ausgelegt; es garantiert während Wartungsaktivitäten minimale Leistungseinbußen für Ihre Workload.

SQL-Datenbank und SQL Managed Instance werden auf der aktuellen stabilen Version der SQL Server-Datenbank-Engine und des Windows-Betriebssystems ausgeführt. Die meisten Benutzer bemerken nicht, dass laufend Upgrades ausgeführt werden.

## <a name="basic-standard-and-general-purpose-service-tier-locally-redundant-availability"></a>Lokal redundante Verfügbarkeit der Dienstebenen „Basic“, „Standard“ und „Universell“

Die Dienstebenen „Basic“, „Standard“ und „Universell“ nutzen die standardmäßige Verfügbarkeitsarchitektur sowohl für serverloses als auch bereitgestelltes Computing. In der folgenden Abbildung werden vier Knoten mit getrennter Compute- und Speicherebene veranschaulicht.

![Trennung der Compute- und Speicherebene](./media/high-availability-sla/general-purpose-service-tier.png)

Das Standardverfügbarkeitsmodell umfasst zwei Ebenen:

- Eine zustandslose Compute-Ebene, auf der der Prozess `sqlservr.exe` ausgeführt wird und die nur vorübergehende und zwischengespeicherte Daten enthält, z. B. TempDB, Modelldatenbanken auf der angefügten SSD, Plancache, Puffer- und Columnstore-Pool im Arbeitsspeicher. Dieser zustandslose Knoten wird von Azure Service Fabric gesteuert, die `sqlservr.exe` initialisiert, die Integrität des Knotens steuert und bei Bedarf ein Failover zu einem anderen Knoten durchführt.
- Eine zustandsbehaftete Datenebene mit den Datenbankdateien (MDF- und LDF-Dateien), die in Azure Blob Storage gespeichert sind. Azure Blob Storage verfügt über integrierte Datenverfügbarkeits- und Redundanzfunktionen. Dadurch wird sichergestellt, dass jeder Datensatz in der Protokolldatei bzw. jede Seite in der Datendatei erhalten bleibt, auch wenn der `sqlservr.exe`-Prozess abstürzt.

Bei jedem Upgrade der Datenbank-Engine oder des Betriebssystems sowie beim Erkennen eines Fehlers wird der zustandslose `sqlservr.exe`-Prozess in Azure Service Fabric zu einem anderen zustandslosen Computeknoten mit ausreichender freier Kapazität verschoben. Daten in Azure Blob Storage sind vom Verschiebevorgang nicht betroffen, und die Daten- und Protokolldateien werden an den neu initialisierten `sqlservr.exe`-Prozess angefügt. Dieser Prozess garantiert eine Verfügbarkeit von 99,99 %; bei einer starken Workload ist möglicherweise eine gewisse Leistungseinbuße während des Übergangs festzustellen, da der neue `sqlservr.exe`-Prozess mit einem kalten Cache gestartet wird.

## <a name="general-purpose-service-tier-zone-redundant-availability-preview"></a>Zonenredundante Verfügbarkeit der Dienstebene „Universell“ (Vorschau)

Die zonenredundante Konfiguration für die Dienstebene „Universell“ verwendet [Azure-Verfügbarkeitszonen](../../availability-zones/az-overview.md)  , um Datenbanken über mehrere physische Standorte innerhalb einer Azure-Region zu replizieren. Durch die Auswahl der Zonenredundanz können Sie Ihre neuen und vorhandenen Einzeldatenbanken vom Typ „Universell“ und Pools für elastische Datenbanken für eine viel größere Anzahl von Fehlern, einschließlich schwerwiegender Ausfälle des Rechenzentrums, resilient gestalten, ohne dass die Anwendungslogik geändert werden muss.

Die zonenredundante Konfiguration für die Dienstebene „Universell“ besitzt zwei Ebenen:  

- Eine zustandsbehaftete Datenebene mit den Datenbankdateien (.mdf/.ldf), die in ZRS PFS (zonenredundante [Premium-Dateifreigabe](../../storage/files/storage-how-to-create-premium-fileshare.md)) gespeichert sind. Mithilfe des [zonenredundanten Speichers](../../storage/common/storage-redundancy.md) werden die Daten und Protokolldateien synchron über drei physisch isolierte Azure-Verfügbarkeitszonen kopiert.
- Eine zustandslose Compute-Ebene, auf der der Prozess „sqlservr.exe“ ausgeführt wird und die nur vorübergehende und zwischengespeicherte Daten enthält, z. B. TempDB, Modelldatenbanken auf der angefügten SSD, Plancache, Puffer- und Columnstore-Pool im Arbeitsspeicher. Dieser zustandslose Knoten wird von Azure Service Fabric gesteuert, die „sqlservr.exe“ initialisiert, die Integrität des Knotens steuert und bei Bedarf ein Failover zu einem anderen Knoten durchführt. Für zonenredundante Datenbanken vom Typ „Universell“ stehen Knoten mit freier Kapazität in anderen Verfügbarkeitszonen für den Failover bereit.

Die zonenredundante Version der Hochverfügbarkeitsarchitektur für die Dienstebene vom Typ „Universell“ wird im folgenden Diagramm veranschaulicht:

![Zonenredundante Konfiguration für „Universell“](./media/high-availability-sla/zone-redundant-for-general-purpose.png)

> [!IMPORTANT]
> Aktuelle Informationen über die Regionen, die zonenredundante Datenbanken unterstützen, finden Sie unter [Unterstützung der Dienste nach Region](../../availability-zones/az-region.md). Die zonenredundante Konfiguration ist nur verfügbar, wenn die Gen5-Computehardware ausgewählt ist. Dieses Feature steht in einer SQL Managed Instance nicht zur Verfügung.

> [!NOTE]
> Bei Datenbanken vom Typ „Universell“ mit einer Größe von 80 virtuellen Kernen kann es bei zonenredundanter Konfiguration zu Leistungseinbußen kommen. Darüber hinaus können Vorgänge wie Sicherung, Wiederherstellung, Datenbankkopie und das Einrichten von Geo-DR-Beziehungen (georedundante Notfallwiederherstellung) bei einzelnen Datenbanken, die größer als 1 TB sind, zu geringerer Leistung führen. 

## <a name="premium-and-business-critical-service-tier-locally-redundant-availability"></a>Lokal redundante Verfügbarkeit der Dienstebenen „Premium“ und „Unternehmenskritisch“

Die Dienstebenen „Premium“ und „Unternehmenskritisch“ nutzen das Premium-Verfügbarkeitsmodell, das eine Integration von Computeressourcen (`sqlservr.exe`-Prozess) und Speicher (lokal angefügte SSD) auf einem einzigen Knoten bietet. Hochverfügbarkeit wird durch Replizieren von Compute- und Speicherressourcen auf weiteren Knoten erreicht, wodurch ein Cluster mit drei bis vier Knoten erstellt wird.

![Cluster von Datenbank-Engine-Knoten](./media/high-availability-sla/business-critical-service-tier.png)

Die zugrunde liegenden Datenbankdateien (MDF- und LDF-Dateien) werden auf dem angefügten SSD-Speicher platziert, um eine E/A mit äußerst niedriger Latenz für Ihre Workload zu erzielen. Hochverfügbarkeit wird anhand einer ähnlichen Technologie wie [AlwaysOn-Verfügbarkeitsgruppen](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) in SQL Server implementiert. Der Cluster umfasst ein einzelnes primäres Replikat, der für Lese-/Schreib-Workloads der Kunden zugänglich ist, sowie bis zu drei sekundäre Replikate (Compute und Speicher) mit Kopien der Daten. Der primäre Knoten ständig überträgt Änderungen der Reihe nach auf die sekundären Knoten und stellt sicher, dass die Daten vor dem Ausführen eines Commits für jede Transaktion mit mindestens einem sekundären Replikat synchronisiert werden. Durch diesen Prozess wird sichergestellt, dass bei einem Ausfall des primären Knotens stets ein vollständig synchronisierter Knoten vorhanden ist, auf den ein Failover ausgeführt werden kann. Das Failover wird von der Azure Service Fabric initiiert. Sobald das sekundäre Replikat zum neuen primären Knoten wird, wird ein weiteres sekundäres Replikat erstellt, um sicherzustellen, dass der Cluster über eine ausreichende Anzahl von Knoten (Quorumssatz) verfügt. Nach Abschluss des Failovers werden Azure SQL-Verbindungen automatisch an den neuen primären Knoten umgeleitet.

Als weiteren Vorteil bietet das Premium-Verfügbarkeitsmodell die Möglichkeit, Azure SQL-Verbindungen mit Schreibschutz auf eines der sekundären Replikate umzuleiten. Dieses Feature wird als [horizontale Leseskalierung](read-scale-out.md) bezeichnet. Es bietet 100 % zusätzliche Computekapazität ohne anfallende Zusatzkosten, sodass Schreibschutzvorgänge wie analytische Workloads vom primären Replikat ausgelagert werden können.

## <a name="premium-and-business-critical-service-tier-zone-redundant-availability"></a>Lokal redundante Verfügbarkeit der Dienstebenen „Premium“ und „Unternehmenskritisch“ 

In der Standardeinstellung wird der Cluster von Knoten für das Premium-Verfügbarkeitsmodell im selben Rechenzentrum erstellt. Mit der Einführung von [Azure-Verfügbarkeitszonen](../../availability-zones/az-overview.md) kann Azure SQL-Datenbank nun verschiedene Replikate von Datenbanken des Typs „Unternehmenskritisch“ in unterschiedlichen Verfügbarkeitszonen in derselben Region platzieren. Um einen Single Point of Failure auszuschließen, wird der Steuerring zudem in mehreren Zonen als drei Gatewayringe (GW) kopiert. Die Weiterleitung an einen bestimmten Gatewayring wird durch [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) (ATM) gesteuert. Da bei der zonenredundanten Konfiguration in den Dienstebenen „Premium“ oder „Unternehmenskritisch“ keine zusätzliche Datenbankredundanz erzeugt wird, können Sie sie ohne Zusatzkosten aktivieren. Durch die Auswahl einer zonenredundanten Konfiguration können Sie Ihre Datenbanken der Dienstebenen „Premium“ oder „Unternehmenskritisch“ für deutlich mehr Ausfallszenarien resistent machen (z.B. für schwerwiegende Ausfälle von Rechenzentren), ohne Änderungen an der Anwendungslogik vornehmen zu müssen. Sie können zudem alle vorhandenen Datenbanken oder Pools der Dienstebenen „Premium“ oder „Unternehmenskritisch“ in die zonenredundante Konfiguration konvertieren.

Da die zonenredundanten Datenbanken über Replikate in verschiedenen Rechenzentren mit einiger Entfernung dazwischen verfügen, kann sich durch die erhöhte Netzwerklatenz die Commitzeit erhöhen und dadurch die Leistung einiger OLTP-Workloads beeinträchtigt werden. Sie können jederzeit zur Einzelzonenkonfiguration zurückkehren, indem Sie die zonenredundante Einstellung deaktivieren. Dieser Prozess ist ein Onlinevorgang und ähnelt dem regulären Dienstebenen-Upgrade. Am Ende des Prozesses wird die Datenbank oder der Pool aus einem zonenredundanten Ring zum Ring einer einzelnen Zone migriert (oder umgekehrt).

> [!IMPORTANT]
> Bei Verwendung des Tarifs „Unternehmenskritisch“ ist die zonenredundante Konfiguration nur verfügbar, wenn die Gen5-Computehardware ausgewählt ist. Aktuelle Informationen über die Regionen, die zonenredundante Datenbanken unterstützen, finden Sie unter [Unterstützung der Dienste nach Region](../../availability-zones/az-region.md).

> [!NOTE]
> Dieses Feature steht in einer SQL Managed Instance nicht zur Verfügung.

Die zonenredundante Version der Hochverfügbarkeitsarchitektur wird im folgenden Diagramm veranschaulicht:

![Hochverfügbarkeitsarchitektur, zonenredundant](./media/high-availability-sla/zone-redundant-business-critical-service-tier.png)


## <a name="hyperscale-service-tier-availability"></a>Verfügbarkeit der Dienstebene „Hyperscale“

Die Architektur der Dienstebene „Hyperscale“ wird in [Architektur verteilter Funktionen](./service-tier-hyperscale.md#distributed-functions-architecture) beschrieben und ist zurzeit nur für die SQL-Datenbank und nicht für SQL Managed Instance verfügbar.

![Funktionale Hyperscale-Architektur](./media/high-availability-sla/hyperscale-architecture.png)

Das Verfügbarkeitsmodell in Hyperscale umfasst vier Ebenen:

- Eine zustandslose Compute-Ebene, auf der der Prozess `sqlservr.exe` ausgeführt wird und die nur vorübergehende und zwischengespeicherte Daten enthält, z. B. nicht abdeckenden RBPEX-Cache TempDB, Modelldatenbanken usw. auf der angefügten SSD, Plancache, Puffer- und Columnstore-Pool im Arbeitsspeicher. Diese zustandslose Ebene enthält das primäre Computereplikat und optional eine Reihe sekundärer Computereplikate, die als Failoverziele dienen können.
- Eine durch Seitenserver gebildete zustandslose Speicherebene. Diese Ebene ist das verteilte Speichermodul für die `sqlservr.exe`-Prozesse, die auf den Computereplikaten ausgeführt werden. Jeder Seitenserver enthält nur vorübergehende und zwischengespeicherte Daten, wie z.B. abdeckenden RBPEX-Cache auf der angefügten SSD und im Arbeitsspeicher zwischengespeicherte Datenseiten. Jeder Seitenserver verfügt über einen gekoppelten Seitenserver in einer Aktiv-Aktiv-Konfiguration, um Lastenausgleich, Redundanz und Hochverfügbarkeit zu gewährleisten.
- Eine zustandsbehaftete Speicherschicht für Transaktionsprotokolle, die vom Computeknoten gebildet wird, auf dem der Protokolldienstprozess, die Zielzone für Transaktionsprotokolle und der langfristige Speicher für Transaktionsprotokolle ausgeführt werden. Für die Zielzone und den langfristigen Speicher wird Azure Storage eingesetzt. Dieser Dienst bietet Verfügbarkeit und [Redundanz](../../storage/common/storage-redundancy.md) für das Transaktionsprotokoll und gewährleistet Datenbeständigkeit für durchgeführte Transaktionen.
- Eine zustandsbehaftete Datenspeicherebene mit den Datenbankdateien (MDF- und NDF-Dateien), die in Azure Storage gespeichert und von Seitenservern aktualisiert werden. Diese Ebene nutzt die Features von Azure Storage für Datenverfügbarkeit und [Redundanz](../../storage/common/storage-redundancy.md). Sie garantiert, dass jede Seite in einer Datendatei erhalten bleibt, auch wenn Prozesse auf anderen Ebenen der Hyperscale-Architektur abstürzen oder Computeknoten ausfallen.

Computeknoten auf allen Hyperscale-Ebenen werden in Azure Service Fabric ausgeführt. Dieser Dienst kontrolliert die Integrität jedes Knotens und führt bei Bedarf ein Failover auf verfügbare fehlerfreie Knoten durch.

Weitere Informationen zur Hochverfügbarkeit in Hyperscale finden Sie unter [Hochverfügbarkeit der Datenbank in Hyperscale](./service-tier-hyperscale.md#database-high-availability-in-hyperscale).


## <a name="accelerated-database-recovery-adr"></a>Schnellere Datenbankwiederherstellung

Die [schnellere Datenbankwiederherstellung (Accelerated Database Recovery, ADR)](../accelerated-database-recovery.md) ist ein neues Feature der SQL-Datenbank-Engine, das die Datenbankverfügbarkeit erheblich verbessert, insbesondere bei Transaktionen mit langer Ausführungsdauer. ADS ist zurzeit für Azure SQL-Datenbank, Azure SQL Managed Instance und Azure Synapse Analytics verfügbar.

## <a name="testing-application-fault-resiliency"></a>Testen der Resilienz von Anwendungsfehlern

Hochverfügbarkeit ist ein wesentlicher Bestandteil der Azure SQL-Datenbank- und SQL Managed Instance-Plattform, der für Ihre Datenbankanwendung transparent ausgeführt wird. Es ist uns jedoch bewusst, dass Sie möglicherweise testen möchten, wie sich die bei geplanten oder ungeplanten Ereignissen eingeleiteten automatischen Failovervorgänge ggf. auf eine Anwendung auswirken, ehe Sie sie in der Produktionsumgebung einsetzen. Sie können ein Failover manuell auslösen, indem Sie eine spezielle API zum Neustarten einer Datenbank, eines Pools für elastische Datenbanken oder einer verwalteten Instanz aufrufen. Bei einer zonenredundanten Datenbank oder einem Pool für elastische Datenbanken führt der API-Aufruf dazu, dass Clientverbindungen von der Verfügbarkeitszone der alten primären Datenbank zur neuen primären Datenbank in einer anderen Verfügbarkeitszone umgeleitet werden. Zusätzlich zu den Tests, wie sich das Failover auf bestehende Datenbanksitzungen auswirkt, können Sie also auch prüfen, ob sich aufgrund von Änderungen an der Netzwerklatenz auch die Gesamtleistung ändert. Weil Neustartvorgänge aufwendig sind und eine große Anzahl davon die Plattform belasten könnte, ist für jede Datenbank, jeden Pool für elastische Datenbanken oder jede verwaltete Instanz ein Failoveraufruf nur alle 15 Minuten erlaubt.

Ein Failover kann mithilfe von PowerShell, der Rest-API oder Azure CLI initiiert werden:

|Bereitstellungstyp|PowerShell|REST-API| Azure CLI|
|:---|:---|:---|:---|
|Datenbank|[Invoke-AzSqlDatabaseFailover](/powershell/module/az.sql/invoke-azsqldatabasefailover)|[Datenbankfailover](/rest/api/sql/databases/failover)|[az rest](/cli/azure/reference-index#az-rest) kann für einen REST-API-Aufruf über die Azure CLI verwendet werden.|
|Pool für elastische Datenbanken|[Invoke-AzSqlElasticPoolFailover](/powershell/module/az.sql/invoke-azsqlelasticpoolfailover)|[Failover für den Pool für elastische Datenbanken](/rest/api/sql/elasticpools(failover)/failover/)|[az rest](/cli/azure/reference-index#az-rest) kann für einen REST-API-Aufruf über die Azure CLI verwendet werden.|
|SQL-Datenbank-Instanz|[Invoke-AzSqlInstanceFailover](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover/)|[Verwaltete Instanzen – Failover](/rest/api/sql/managed%20instances%20-%20failover/failover)|[az sql mi failover](/cli/azure/sql/mi/#az-sql-mi-failover)|

> [!IMPORTANT]
> Der Befehl „Failover“ steht für lesbare sekundäre Replikate von Hyperscale-Datenbanken nicht zur Verfügung.

## <a name="conclusion"></a>Zusammenfassung

Azure SQL-Datenbank und Azure SQL Managed Instance verfügen über eine integrierte Hochverfügbarkeitslösung, die tief in die Azure-Plattform integriert ist. Sie ist bei der Fehlererkennung und Wiederherstellung von Service Fabric, in Verbindung mit dem Datenschutz von Azure Blob Storage und für höhere Fehlertoleranz von Verfügbarkeitszonen abhängig (wie bereits erwähnt, gilt dies noch nicht für Azure SQL Managed Instance). Darüber hinaus nutzen SQL-Datenbank und SQL Managed Instance die Technologie der AlwaysOn-Verfügbarkeitsgruppen der SQL Server-Instanz für Replikation und Failover. Dank der Kombination dieser Technologien können Anwendungen die Vorteile eines gemischten Speichermodells voll ausschöpfen und sehr anspruchsvolle SLAs unterstützen.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Azure-Verfügbarkeitszonen](../../availability-zones/az-overview.md)
- Weitere Informationen zu [Service Fabric](../../service-fabric/service-fabric-overview.md)
- Weitere Informationen zu [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md)
- Erfahren Sie, wie Sie ein [Vom Benutzer initiiertes manuelles Failover für SQL Managed Instance](../managed-instance/user-initiated-failover.md) durchführen.
- Weitere Optionen für Hochverfügbarkeit und Notfallwiederherstellung finden Sie unter [Geschäftskontinuität](business-continuity-high-availability-disaster-recover-hadr-overview.md).
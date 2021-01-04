---
title: Ressourcenverwaltung in umfangreichen Pools für elastische Datenbanken
description: Es wird beschrieben, wie Sie Computeressourcen in Pools für elastische Azure SQL-Datenbanken verwalten, die eine hohe Zahl von Datenbanken aufweisen.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: sstein
ms.date: 09/16/2020
ms.openlocfilehash: 1398745a485b57c3a739c25dbca77d7b21f19758
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792156"
---
# <a name="resource-management-in-dense-elastic-pools"></a>Ressourcenverwaltung in umfangreichen Pools für elastische Datenbanken
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Pools für elastische Azure SQL-Datenbanken](./elastic-pool-overview.md) sind eine kostengünstige Lösung zum Verwalten einer großen Zahl von Datenbanken mit variierender Ressourcenauslastung. Für alle Datenbanken eines Pools für elastische Datenbanken wird die gleiche Zuordnung von Ressourcen verwendet, z. B. CPU, Arbeitsspeicher, Workerthreads, Speicherplatz und tempdb. Dies erfolgt aufgrund der Annahme, dass **Computeressourcen jeweils nur von einer Teilmenge der Datenbanken im Pool genutzt werden** . Diese Annahme ermöglicht für Pools für elastische Datenbanken die Erzielung von Kosteneffizienz. Anstatt für alle Ressourcen zu bezahlen, die von jeder einzelnen Datenbank unter Umständen benötigt werden, zahlen Kunden für einen deutlich kleineren Ressourcensatz, der für alle Datenbanken des Pools genutzt wird.

## <a name="resource-governance"></a>Ressourcengovernance

Bei der Ressourcenfreigabe muss das System die Ressourcenauslastung sorgfältig kontrollieren, um den „Noisy Neighbor“-Effekt (lauter Nachbar) zu verringern, bei dem eine Datenbank mit hohem Ressourcenverbrauch andere Datenbanken in demselben Pool für elastische Datenbanken beeinträchtigt. Außerdem muss das System genügend Ressourcen für Features bereitstellen, z. B. Hochverfügbarkeit und Notfallwiederherstellung, Sicherung und Wiederherstellung, Überwachung, Abfragespeicher, automatische Optimierung usw., um zuverlässig zu funktionieren.

Diese Ziele werden bei Azure SQL-Datenbank erreicht, indem mehrere Ressourcenkontrollmechanismen verwendet werden, z. B. Windows-[Auftragsobjekte](/windows/win32/procthread/job-objects) für die Ressourcenkontrolle auf Prozessebene, Windows-[Ressourcen-Manager für Dateiserver (File Server Resource Manager, FSRM)](/windows-server/storage/fsrm/fsrm-overview) für die Verwaltung von Speicherkontingenten und eine geänderte und erweiterte Version von SQL Server [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) zum Implementieren der Ressourcenkontrolle in SQL-Datenbank.

Das wichtigste Entwurfsziel von Pools für elastische Datenbanken ist Wirtschaftlichkeit. Aus diesem Grund lässt das System für Kunden absichtlich die Erstellung von _umfangreichen_ Pools zu. Dies sind Pools, bei denen die Anzahl von Datenbanken nahezu oder vollständig erreicht wird, aber die nur über eine relativ geringe Zuordnung von Computeressourcen verfügen. Aus demselben Grund werden nicht alle potenziell benötigen Ressourcen für interne Prozesse reserviert, sondern die Ressourcenfreigabe zwischen internen Prozessen und Benutzerarbeitsauslastungen ermöglicht.

Diese Vorgehensweise ermöglicht Kunden die Verwendung von umfangreichen Pools für elastische Datenbanken, um eine ausreichende Leistung und größere Kosteneinsparungen zu erzielen. Wenn die Workload vieler Datenbanken in einem dichten Pool aber intensiv genug ist, müssen ggf. Ressourcenkonflikte beachtet werden. Bei Ressourcenkonflikten wird die Leistung von Benutzerarbeitsauslastungen reduziert, und es kann sich eine negative Auswirkung auf interne Prozesse ergeben.

> [!IMPORTANT]
> In dichten Pools mit zahlreichen aktiven Datenbanken ist es ggf. nicht möglich, die Anzahl der Datenbanken im Pool bis zu den dokumentierten Höchstwerten für [DTU](resource-limits-dtu-elastic-pools.md) und [vCore](resource-limits-vcore-elastic-pools.md) für Pools mit elastischen Datenbanken zu steigern.
>
> Die Anzahl der Datenbanken, die in dichten Pools platziert werden können, ohne dass es zu Ressourcenkonflikten und Leistungsproblemen kommt, hängt von der Anzahl der gleichzeitig aktiven Datenbanken und vom Ressourcenverbrauch der Benutzerworkloads in den einzelnen Datenbanken ab. Diese Zahl kann sich im Laufe der Zeit ändern, sobald sich die Workloads von Benutzern ändern.

Falls es in einem dicht gepackten Pool zu Ressourcenkonflikten kommt, können Kunden eine oder mehrere der folgenden Aktionen auswählen, um diese zu beheben:

- Optimieren Sie die Abfrageworkload, um den Ressourcenverbrauch zu reduzieren oder diesen mit der Zeit auf mehrere Datenbanken zu verteilen.
- Reduzieren der Pooldichte, indem einige Datenbanken in einen anderen Pool verschoben oder zu eigenständigen Datenbanken gemacht werden
- Hochskalieren des Pools zum Abrufen von weiteren Ressourcen

Vorschläge zur Implementierung der letzten beiden Aktionen finden Sie unter [Empfehlungen zum Betrieb](#operational-recommendations) weiter unten in diesem Artikel. Die Reduzierung von Ressourcenkonflikten wirkt sich sowohl auf Benutzer als auch auf interne Prozesse positiv aus und ermöglicht es dem System, den erwarteten Servicelevel aufrechtzuerhalten.

## <a name="monitoring-resource-consumption"></a>Überwachen des Ressourcenverbrauchs

Zur Vermeidung von Leistungsbeeinträchtigungen aufgrund von Ressourcenkonflikten sollten Kunden, die umfangreiche Pools für elastische Datenbanken nutzen, den Ressourcenverbrauch proaktiv überwachen und rechtzeitig Maßnahmen ergreifen, falls sich zunehmende Ressourcenkonflikte auf die Arbeitsauslastungen auswirken. Eine fortlaufende Überwachung ist wichtig, weil sich die Ressourcenauslastung in einem Pool im Laufe der Zeit verändert, z. B. aufgrund von Änderungen der Benutzerworkloads, der Datenmenge und -verteilung, der Pooldichte und im Azure SQL-Datenbank-Dienst.

Azure SQL-Datenbank verfügt über mehrere Metriken, die für diese Art von Überwachung relevant sind. Wenn Sie den empfohlenen Durchschnittswert für jede Metrik überschreiten, wird ein Ressourcenkonflikt für den Pool angezeigt, den Sie mit einer der oben beschriebenen Maßnahmen beseitigen sollten.

|Metrikname|BESCHREIBUNG|Empfohlener Durchschnittswert|
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|CPU-Auslastung des SQL-Prozesses eines Pools für elastische Datenbanken, gemessen vom zugrunde liegenden Betriebssystem. Verfügbar in der Ansicht [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) in jeder Datenbank und in der Ansicht [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) in der `master`-Datenbank. Diese Metrik wird auch für Azure Monitor ausgegeben, wobei sie den [Namen](../../azure-monitor/platform/metrics-supported.md#microsoftsqlserverselasticpools) `sqlserver_process_core_percent` hat und im Azure-Portal angezeigt werden kann. Dieser Wert ist für jede Datenbank im Pool für elastische Datenbanken gleich.|Unterhalb von 70 %. Gelegentliche kurze Spitzen von bis zu 90 % sind ggf. akzeptabel.|
|`max_worker_percent`|Auslastung von [Arbeitsthreads]( https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide). Wird für jede Datenbank im Pool sowie für den Pool selbst bereitgestellt. Es gibt unterschiedliche Grenzwerte für die Anzahl von Arbeitsthreads auf Datenbank- und Poolebene. Daher wird empfohlen, diese Metrik auf beiden Ebenen zu überwachen. Verfügbar in der Ansicht [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) in jeder Datenbank und in der Ansicht [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) in der `master`-Datenbank. Diese Metrik wird auch für Azure Monitor ausgegeben, wobei sie den [Namen](../../azure-monitor/platform/metrics-supported.md#microsoftsqlserverselasticpools) `workers_percent` hat und im Azure-Portal angezeigt werden kann.|Unterhalb von 80 %. Spitzen von bis zu 100 % führen zu Fehlern bei Verbindungsversuchen und Abfragen.|
|`avg_data_io_percent`|IOPS-Auslastung für physische E/A-Lese- und -Schreibvorgänge. Wird für jede Datenbank im Pool sowie für den Pool selbst bereitgestellt. Es gibt unterschiedliche Grenzwerte für die IOPS-Anzahl auf Datenbank- und Poolebene. Daher wird empfohlen, diese Metrik auf beiden Ebenen zu überwachen. Verfügbar in der Ansicht [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) in jeder Datenbank und in der Ansicht [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) in der `master`-Datenbank. Diese Metrik wird auch für Azure Monitor ausgegeben, wobei sie den [Namen](../../azure-monitor/platform/metrics-supported.md#microsoftsqlserverselasticpools) `physical_data_read_percent` hat und im Azure-Portal angezeigt werden kann.|Unterhalb von 80 %. Gelegentliche kurze Spitzen von bis zu 100 % sind ggf. akzeptabel.|
|`avg_log_write_percent`|Durchsatznutzung für E/A-Schreibvorgänge per Transaktionsprotokoll. Wird für jede Datenbank im Pool sowie für den Pool selbst bereitgestellt. Es gibt unterschiedliche Grenzwerte für den Protokolldurchsatz auf Datenbank- und Poolebene. Daher wird empfohlen, diese Metrik auf beiden Ebenen zu überwachen. Verfügbar in der Ansicht [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) in jeder Datenbank und in der Ansicht [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) in der `master`-Datenbank. Diese Metrik wird auch für Azure Monitor ausgegeben, wobei sie den [Namen](../../azure-monitor/platform/metrics-supported.md#microsoftsqlserverselasticpools) `log_write_percent` hat und im Azure-Portal angezeigt werden kann. Wenn diese Metrik in der Nähe von 100 % liegt, erfolgen alle Datenbankänderungen langsamer (INSERT-, UPDATE-, DELETE-, MERGE-Anweisungen, SELECT … INTO, BULK INSERT usw.).|Unterhalb von 90 %. Gelegentliche kurze Spitzen von bis zu 100 % sind ggf. akzeptabel.|
|`oom_per_second`|Die Rate der Fehler vom Typ „Nicht genügend Arbeitsspeicher“ in einem Pool für elastische Datenbanken. Dies ist ein Indikator für eine hohe Arbeitsspeicherauslastung. Verfügbar in der Ansicht [sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database). Unter [Beispiele](#examples) finden Sie eine Beispielabfrage zum Berechnen dieser Metrik.|0|
|`avg_storage_percent`|Der gesamte Speicherplatz, der von Daten in allen Datenbanken in einem Pool für elastische Datenbanken verwendet wird. Schließt keine leeren Speicherplätze in Datenbankdateien ein. Verfügbar in der Ansicht [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) in der `master`-Datenbank. Diese Metrik wird auch für Azure Monitor ausgegeben, wobei sie den [Namen](../../azure-monitor/platform/metrics-supported.md#microsoftsqlserverselasticpools) `storage_percent` hat und im Azure-Portal angezeigt werden kann.|Unterhalb von 80 %. Kann für Pools ohne Datenwachstum in der Nähe von 100 % liegen.|
|`avg_allocated_storage_percent`|Der gesamte Speicherplatz, der von Datenbankdateien in allen Datenbanken in einem Pool für elastische Datenbanken verwendet wird. Schließt leere Speicherplätze in Datenbankdateien ein. Verfügbar in der Ansicht [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) in der `master`-Datenbank. Diese Metrik wird auch für Azure Monitor ausgegeben, wobei sie den [Namen](../../azure-monitor/platform/metrics-supported.md#microsoftsqlserverselasticpools) `allocated_data_storage_percent` hat und im Azure-Portal angezeigt werden kann.|Unterhalb von 90 %. Kann für Pools ohne Datenwachstum in der Nähe von 100 % liegen.|
|`tempdb_log_used_percent`|Speicherplatznutzung in der Datenbank `tempdb` per Transaktionsprotokoll. Temporäre Objekte, die in einer Datenbank erstellt werden, sind in den anderen Datenbanken desselben Pools für elastische Datenbanken zwar nicht sichtbar, aber `tempdb` ist eine freigegebene Ressource für alle Datenbanken desselben Pools. Eine zeitintensive oder verwaiste Transaktion in `tempdb`, die über eine Datenbank des Pools gestartet wird, kann einen großen Teil des Transaktionsprotokolls verbrauchen und zu Fehlern bei Abfragen in anderen Datenbanken desselben Pools führen. Abgeleitet von den Ansichten [sys.dm_db_log_space_usage](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql) und [sys.database_files](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql). Diese Metrik wird auch für Azure Monitor ausgegeben und kann im Azure-Portal angezeigt werden. Unter den [Beispielen](#examples) finden Sie eine Beispielabfrage zum Zurückgeben des aktuellen Werts dieser Metrik.|Unterhalb von 50 %. Gelegentliche Spitzen von bis zu 80 % sind zulässig.|
|||

Zusätzlich zu diesen Metriken bietet Azure SQL-Datenbank eine Ansicht, in der die tatsächlichen Grenzwerte für die Ressourcenkontrolle zurückgegeben werden. Außerdem gibt es zusätzliche Ansichten, in denen statistische Daten zur Ressourcenauslastung auf Ebene des Ressourcenpools und auf Ebene der Arbeitsauslastungsgruppe zurückgegeben werden.

|Name der Ansicht|BESCHREIBUNG|  
|-----------------|--------------------------------|  
|[sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|Gibt die tatsächlichen Konfigurations- und Kapazitätseinstellungen zurück, die von Ressourcenkontrollmechanismen in der aktuellen Datenbank oder im Pool für elastische Datenbanken verwendet werden.|
|[sys.dm_resource_governor_resource_pools](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|Gibt Informationen zum aktuellen Status und zur aktuellen Konfiguration von Ressourcenpools und kumulative statistische Daten zu Ressourcenpools zurück.|
|[sys.dm_resource_governor_workload_groups](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|Gibt kumulative statistische Daten zu Arbeitsauslastungsgruppen und die aktuelle Konfiguration der Arbeitsauslastungsgruppe zurück. Diese Ansicht kann mit „sys.dm_resource_governor_resource_pools“ in der Spalte `pool_id` verknüpft werden, um Informationen zum Ressourcenpool abzurufen.|
|[sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)|Gibt die Nutzungsstatistik des Ressourcenpools für die letzten 32 Minuten zurück. Jede Zeile steht für ein Intervall von 20 Sekunden. In den `delta_`-Spalten werden jeweils die Änderungen der Statistik für das Intervall zurückgegeben.|
|[sys.dm_resource_governor_workload_groups_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)|Gibt die Nutzungsstatistik der Arbeitsauslastungsgruppe für die letzten 32 Minuten zurück. Jede Zeile steht für ein Intervall von 20 Sekunden. In den `delta_`-Spalten werden jeweils die Änderungen der Statistik für das Intervall zurückgegeben.|
|||

Diese Ansichten können verwendet werden, um die Ressourcennutzung zu überwachen und Probleme aufgrund von Ressourcenkonflikten nahezu in Echtzeit zu behandeln. Die Benutzerarbeitsauslastung auf den primären und lesbaren sekundären Replikaten, einschließlich Georeplikaten, wird als Ressourcenpool `SloSharedPool1` und Arbeitsauslastungsgruppe `UserPrimaryGroup.DBId[N]` klassifiziert, wobei `N` für den Datenbank-ID-Wert steht.

Zusätzlich zur Überwachung der aktuellen Ressourcennutzung können Kunden, die umfangreiche Pools nutzen, Verlaufsdaten zur Ressourcennutzung in einem separaten Datenspeicher aufbewahren. Diese Daten können für Vorhersageanalysen verwendet werden, um die Ressourcennutzung basierend auf verlaufsabhängigen und saisonalen Trends proaktiv zu verwalten.

## <a name="operational-recommendations"></a>Empfehlungen zum Betrieb

**Bereitstellen von genügend Platz für Ressourcen** : Wenn es zu Ressourcenkonflikten und Leistungseinbußen kommt, kann eine Entschärfung – wie bereits erwähnt – das Verschieben einiger Datenbanken aus dem betroffenen Pool für elastische Datenbanken oder das zentrale Hochskalieren des Pools umfassen. Für diese Aktionen sind aber zusätzliche Computeressourcen erforderlich. Vor allem für Premium- und unternehmenskritische Pools müssen für diese Aktionen alle Daten für die zu verschiebenden Datenbanken bzw. für alle Datenbanken des Pools für elastische Datenbanken übertragen werden, wenn der Pool zentral hochskaliert wird. Die Datenübertragung ist ein zeit- und ressourcenintensiver Vorgang. Wenn die Ressourcenauslastung im Pool bereits hoch ist, wird die Leistung durch die Behebungsmaßnahmen noch weiter beeinträchtigt. In Extremfällen ist es unter Umständen nicht möglich, Ressourcenkonflikte durch eine Datenbankverschiebung oder das zentrale Hochskalieren des Pools zu lösen, weil die benötigten Ressourcen nicht verfügbar sind. Die einzige Lösung ist dann ggf. das vorübergehende Reduzieren der Arbeitsauslastung von Abfragen für den betroffenen Pool für elastische Datenbanken.

Kunden, die umfangreiche Pools nutzen, sollten die Nutzungstrends sorgfältig überwachen (wie oben beschrieben) und Maßnahmen zur Entschärfung ergreifen, während die Metriken noch in den empfohlenen Bereichen liegen und im Pool für elastische Datenbanken noch genügend Ressourcen vorhanden sind.

Die Ressourcennutzung hängt von mehreren Faktoren ab, die sich je nach Datenbank und Pool für elastische Datenbanken im Laufe der Zeit ändern. Zur Erzielung eines optimalen Preis-Leistungs-Verhältnisses in umfangreichen Pools sind eine fortlaufende Überwachung und erneute Anpassungen erforderlich, bei denen Datenbanken aus stärker genutzten Pools in weniger stark genutzte Pools verschoben und je nach Bedarf neue Pools erstellt werden, um eine erhöhte Arbeitsauslastung abdecken zu können.

**Vermeiden der Verschiebung von „heißen“ Datenbanken** : Wenn Ressourcenkonflikte auf Poolebene hauptsächlich durch eine geringe Anzahl von hoch ausgelasteten Datenbanken verursacht werden, kann es verlockend sein, diese Datenbanken in einen weniger stark genutzten Pool zu verschieben oder sie zu eigenständigen Datenbanken zu machen. Dies ist aber nicht empfehlenswert, wenn eine Datenbank längere Zeit stark ausgelastet ist, weil die Leistung durch den Verschiebungsvorgang noch mehr beeinträchtigt wird. Dies gilt sowohl für die zu verschiebende Datenbank als auch für den gesamten Pool. Warten Sie stattdessen entweder, bis die hohe Auslastung abnimmt, oder verschieben Sie weniger stark genutzte Datenbanken, um die Ressourcenauslastung auf Poolebene zu reduzieren. Das Verschieben von Datenbanken mit sehr geringer Auslastung ergibt in diesem Fall aber keinen Vorteil, da die Ressourcenauslastung auf Poolebene nicht wesentlich verringert wird.

**Erstellen von neuen Datenbanken in einem „Quarantänepool“** : In Szenarien, in denen häufig neue Datenbanken erstellt werden, z. B. bei Anwendungen mit dem Modell „ein Mandant pro Datenbank“, besteht die folgende Gefahr: Eine neue Datenbank, die in einem vorhandenen Pool für elastische Datenbanken angeordnet wird, verbraucht unerwartet eine sehr große Menge an Ressourcen und beeinträchtigt andere Datenbanken und interne Prozesse im Pool. Erstellen Sie zur Verringerung dieser Gefahr einen separaten „Quarantänepool“ mit einer ausreichenden Ressourcenzuordnung. Verwenden Sie diesen Pool für neue Datenbanken, für die das Muster des Ressourcenverbrauchs noch nicht bekannt ist. Nachdem sich eine Datenbank einen Geschäftszyklus lang in diesem Pool befunden hat, z. B. eine Woche oder einen Monat, und der Ressourcenverbrauch bekannt ist, kann sie in einen Pool mit ausreichender Kapazität verschoben werden, um diese zusätzliche Ressourcennutzung abzudecken.

**Überwachen Sie sowohl den verwendeten als auch den zugewiesenen Speicherplatz.** Wenn der im Pool zugewiesene Speicherplatz (Gesamtgröße aller Datenbankdateien im Speicher für alle Datenbanken in einem Pool) die maximale Poolgröße erreicht, können Fehler aufgrund von fehlendem Speicherplatz auftreten. Wenn die Trends beim zugeordneten Speicherplatz hoch sind und die maximale Poolgröße demnächst erreicht wird, stehen Ihnen zur Entschärfung folgende Optionen zur Verfügung:
- Verschieben einiger Datenbanken aus dem Pool, um den zugeordneten Gesamtspeicherplatz zu reduzieren
- [Verkleinern](file-space-manage.md) von Datenbankdateien, um leeren zugewiesenen Speicherplatz in Dateien zu verringern
- Hochskalieren des Pools auf ein Dienstziel mit einer größeren maximalen Poolgröße

Wenn die Trends beim genutzten Speicherplatz im Pool (Gesamtgröße der Daten in allen Datenbanken in einem Pool, ohne Leerraum in Dateien) hoch sind und demnächst die maximale Poolgröße erreicht wird, stehen Ihnen zur Entschärfung folgende Optionen zur Verfügung:
- Verschieben einiger Datenbanken aus dem Pool, um den genutzten Gesamtspeicherplatz zu reduzieren
- Verschieben (Archivieren) von Daten außerhalb der Datenbank oder Löschen nicht mehr benötigter Daten
- Implementieren einer [Datenkomprimierung](/sql/relational-databases/data-compression/data-compression)
- Hochskalieren des Pools auf ein Dienstziel mit einer größeren maximalen Poolgröße

**Vermeiden Sie Server mit übermäßiger Dichte** . Azure SQL-Datenbank [unterstützt](./resource-limits-logical-server.md) bis zu 5000 Datenbanken pro Server. Kunden, die Pools für elastische Datenbanken mit Tausenden von Datenbanken verwenden, können erwägen, mehrere Pools für elastische Datenbanken auf einem einzelnen Server anzuordnen und in Bezug auf die unterstützte maximale Anzahl von Datenbanken ans Limit zu gehen. Server mit vielen Tausenden Datenbanken sind aber mit besonderen Anforderungen an den Betrieb verbunden, die bewältigt werden müssen. Vorgänge, bei denen alle Datenbanken auf einem Server aufgelistet werden müssen, z. B. beim Anzeigen von Datenbanken im Portal, benötigen mehr Zeit. Betriebsbezogene Fehler, z. B. die fehlerhafte Änderung von Anmeldungen auf Serverebene oder von Firewallregeln, wirken sich auf eine größere Zahl von Datenbanken aus. Nach dem versehentlichen Löschen des Servers benötigen Sie Hilfe vom Microsoft-Support, um die Datenbanken auf dem gelöschten Server wiederherstellen zu lassen, und es kommt für alle betroffenen Datenbanken zu einem längeren Ausfall.

Wir empfehlen Ihnen, die Anzahl von Datenbanken pro Server auf eine niedrigere Anzahl als den maximal unterstützten Wert festzulegen. In vielen Szenarien ist die Nutzung von maximal 1.000 - 2.000 Datenbanken pro Server optimal. Um die Wahrscheinlichkeit zu verringern, dass ein Server versehentlich gelöscht wird, empfehlen wir Ihnen das Einfügen einer [Löschsperre](../../azure-resource-manager/management/lock-resources.md) auf dem Server oder in der zugehörigen Ressourcengruppe.

In der Vergangenheit war es der Fall, dass bestimmte Vorgänge mit einer Verschiebung von Datenbanken in, aus oder zwischen Pools für elastische Datenbanken auf demselben Server schneller als das Verschieben von Datenbanken zwischen Servern waren. Derzeit werden alle Datenbankverschiebungen unabhängig vom Quell- und Zielserver mit der gleichen Geschwindigkeit ausgeführt.

## <a name="examples"></a>Beispiele

### <a name="monitoring-memory-utilization"></a>Überwachen der Arbeitsspeicherauslastung

Mit dieser Abfrage wird die Metrik `oom_per_second` für jeden Ressourcenpool für die letzten 32 Minuten berechnet. Diese Abfrage kann in jeder Datenbank eines beliebigen Pools für elastische Datenbanken ausgeführt werden.

```sql
SELECT pool_id,
       name AS resource_pool_name,
       IIF(name LIKE 'SloSharedPool%' OR name LIKE 'UserPool%', 'user', 'system') AS resource_pool_type,
       SUM(CAST(delta_out_of_memory_count AS decimal))/(SUM(duration_ms)/1000.) AS oom_per_second
FROM sys.dm_resource_governor_resource_pools_history_ex
GROUP BY pool_id, name
ORDER BY pool_id;
```

### <a name="monitoring-tempdb-log-space-utilization"></a>Überwachen der Auslastung des `tempdb`-Protokollspeichers

Die Abfrage gibt den aktuellen Wert der `tempdb_log_used_percent`-Metrik zurück und zeigt die relative Nutzung des `tempdb`-Transaktionsprotokolls im Verhältnis zu seiner maximal zulässigen Größe. Diese Abfrage kann in jeder Datenbank eines beliebigen Pools für elastische Datenbanken ausgeführt werden.

```sql
SELECT (lsu.used_log_space_in_bytes / df.log_max_size_bytes) * 100 AS tempdb_log_space_used_percent
FROM tempdb.sys.dm_db_log_space_usage AS lsu
CROSS JOIN (
           SELECT SUM(CAST(max_size AS bigint)) * 8 * 1024. AS log_max_size_bytes
           FROM tempdb.sys.database_files
           WHERE type_desc = N'LOG'
           ) AS df
;
```

## <a name="next-steps"></a>Nächste Schritte

- Eine Einführung in Pools für elastische Datenbanken finden Sie unter [Pools für elastische Datenbanken als Hilfe beim Verwalten und Skalieren mehrerer Datenbanken in Azure SQL-Datenbank](./elastic-pool-overview.md).
- Informationen zum Optimieren der Arbeitsauslastungen von Abfragen zur Reduzierung der Ressourcennutzung finden Sie unter [Überwachung und Optimierung]( https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-tuning-index) und [Überwachen und Optimieren der Datenbankleistung](./monitor-tune-overview.md).
---
title: Migrieren von DTU zu V-Kern
description: Informationen zum Migrieren einer Datenbank in Azure SQL-Datenbank vom DTU- zum vCore-Modell Das Migrieren zu V-Kern ähnelt dem Up- oder Downgrade zwischen der Standard- und Premium-Dienstebene.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 05/28/2020
ms.openlocfilehash: aa236ecaaa9c38c68e66d1813280cd98b85b9463
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790388"
---
# <a name="migrate-azure-sql-database-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migrieren einer Datenbank in Azure SQL-Datenbank vom DTU- zum vCore-basierten Modell
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In diesem Artikel wird das Migrieren Ihrer Datenbank in Azure SQL-Datenbank vom [DTU-basierten Kaufmodell](service-tiers-dtu.md) (Database Transaction Unit, Datenbanktransaktionseinheit) zum [vCore-basierten Kaufmodell](service-tiers-vcore.md) (virtuellen Kern) beschrieben. 

## <a name="migrate-a-database"></a>Migrieren einer Datenbank

Die Migration einer Datenbank vom DTU- zum vCore-basierten Kaufmodell ist vergleichbar mit der Skalierung zwischen Dienstzielen auf den Dienstebenen Basic, Standard und Premium mit ähnlicher [Dauer](single-database-scale.md#latency) und [minimaler Ausfallzeit](scale-resources.md) am Ende des Migrationsprozesses. Eine zum vCore-basierten Kaufmodell migrierte Datenbank kann jederzeit auf die gleiche Weise zurück zum DTU-basierten Kaufmodell migriert werden. Eine Ausnahme bilden Datenbanken, die zur Dienstebene [Hyperscale](service-tier-hyperscale.md) migriert wurden. 

## <a name="choose-the-vcore-service-tier-and-service-objective"></a>Wählen der vCore-Dienstebene und des Dienstziels

Bei den meisten Migrationsszenarien von DTU zu vCore werden Datenbanken und Pools für elastische Datenbanken auf den Dienstebenen Basic und Standard der Dienstebene [Universell](service-tier-general-purpose.md) zugeordnet. Datenbanken und Pools für elastische Datenbanken auf der Dienstebene Premium werden der Dienstebene [Unternehmenskritisch](service-tier-business-critical.md) zugeordnet. Je nach Anwendungsszenario und Anforderungen kann die Dienstebene [Hyperscale](service-tier-hyperscale.md) häufig als Migrationsziel für einzelne Datenbanken auf allen DTU-Dienstebenen genutzt werden.

Um das Dienstziel oder die Computegröße für die migrierte Datenbank im vCore-Modell zu wählen, können Sie eine einfache, aber ungefähre Faustregel befolgen: Alle 100 DTUs auf den Dienstebenen Basic oder Standard erfordern  *mindestens* 1 virtuellen Kern, und alle 125 DTUs auf der Dienstebene Premium erfordern *mindestens* 1 virtuellen Kern. 

> [!TIP]
> Diese Regel ist eine Annäherung, da sie die für die DTU-Datenbank oder den Pool für elastische Datenbanken genutzte Hardwaregeneration nicht berücksichtigt. 

Im DTU-Modell kann jede verfügbare [Hardwaregeneration](purchasing-models.md#hardware-generations-in-the-dtu-based-purchasing-model) für Ihre Datenbank oder Ihren Pool für elastische Datenbanken genutzt werden. Darüber hinaus haben Sie nur eine indirekte Kontrolle über die Anzahl der virtuellen Kerne (logischen CPUs), indem Sie höhere oder niedrigere DTU- oder eDTU-Werte wählen. 

Beim vCore-Modell muss der Kunde eine explizite Auswahl sowohl der Hardwaregeneration als auch der Anzahl der virtuellen Kerne (logischen CPUs) treffen. Das DTU-Modell bietet diese Wahlmöglichkeiten nicht. Die Hardwaregeneration und Anzahl der logischen CPUs, die für jede Datenbank und jeden Pool für elastische Datenbanken genutzt werden, werden jedoch über dynamische Verwaltungsansichten verfügbar gemacht. Dies ermöglicht es, das passende vCore-Dienstziel genauer zu bestimmen. 

Beim folgenden Ansatz werden diese Informationen zur Bestimmung eines vCore-Dienstziels mit einer ähnlichen Zuteilung von Ressourcen genutzt, um nach der Migration zum vCore-Modell ein ähnliches Leistungsniveau zu erreichen.

### <a name="dtu-to-vcore-mapping"></a>Zuordnung von DTU zu vCore

Die nachstehende T-SQL-Abfrage gibt bei der Ausführung im Kontext einer zu migrierenden DTU-Datenbank eine übereinstimmende Anzahl virtueller Kerne (möglicherweise als Bruchzahl) in jeder Hardwaregeneration im vCore-Modell zurück. Durch Runden dieser Zahl auf die nächstliegende Anzahl virtueller Kerne, die für [Datenbanken](resource-limits-vcore-single-databases.md) und [Pools für elastische Datenbanken](resource-limits-vcore-elastic-pools.md) in jeder Hardwaregeneration im vCore-Modell verfügbar sind, können Kunden das vCore-Dienstziel wählen, das ihrer DTU-Datenbank oder ihrem Pool für elastische Datenbanken am ehesten entspricht. 

Beispiele von Migrationsszenarien, die diesen Ansatz aufgreifen, sind im Abschnitt [Beispiele](#dtu-to-vcore-migration-examples) beschrieben.

Führen Sie diese Abfrage im Kontext der zu migrierenden Datenbank und nicht in der `master`-Datenbank aus. Wenn Sie einen Pool für elastische Datenbanken migrieren, führen Sie die Abfrage im Kontext einer beliebigen Datenbank im Pool aus.

```SQL
WITH dtu_vcore_map AS
(
SELECT TOP (1) rg.slo_name,
               CASE WHEN rg.slo_name LIKE '%SQLG4%' THEN 'Gen4'
                    WHEN rg.slo_name LIKE '%SQLGZ%' THEN 'Gen4'
                    WHEN rg.slo_name LIKE '%SQLG5%' THEN 'Gen5'
                    WHEN rg.slo_name LIKE '%SQLG6%' THEN 'Gen5'
               END AS dtu_hardware_gen,
               s.scheduler_count * CAST(rg.instance_cap_cpu/100. AS decimal(3,2)) AS dtu_logical_cpus,
               CAST((jo.process_memory_limit_mb / s.scheduler_count) / 1024. AS decimal(4,2)) AS dtu_memory_per_core_gb
FROM sys.dm_user_db_resource_governance AS rg
CROSS JOIN (SELECT COUNT(1) AS scheduler_count FROM sys.dm_os_schedulers WHERE status = 'VISIBLE ONLINE') AS s
CROSS JOIN sys.dm_os_job_object AS jo
WHERE dtu_limit > 0
      AND
      DB_NAME() <> 'master'
)
SELECT dtu_logical_cpus,
       dtu_hardware_gen,
       dtu_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.7
       END AS Gen4_vcores,
       7 AS Gen4_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.7
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus
       END AS Gen5_vcores,
       5.05 AS Gen5_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.8
       END AS Fsv2_vcores,
       1.89 AS Fsv2_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.4
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.9
       END AS M_vcores,
       29.4 AS M_memory_per_core_gb
FROM dtu_vcore_map;
```

### <a name="additional-factors"></a>Weitere Faktoren

Neben der Anzahl der virtuellen Kerne (logischen CPUs) und der Hardwaregeneration können verschiedene andere Faktoren die Wahl des vCore-Dienstziels beeinflussen:

- Die T-SQL-Zuordnungsabfrage entspricht den DTU- und vCore-Dienstzielen in Bezug auf ihre CPU-Kapazität. Daher sind die Ergebnisse bei CPU-gebundenen Workloads genauer.
- Bei der gleichen Hardwaregeneration und der gleichen Anzahl virtueller Kerne sind die Ressourcengrenzwerte für IOPS- und Transaktionsprotokolldurchsatz bei vCore-Datenbanken oft höher als bei DTU-Datenbanken. Bei E/A-gebundenen Workloads ist es eventuell möglich, die Anzahl der virtuellen Kerne im vCore-Modell zu verringern, um dasselbe Leistungsniveau zu erreichen. Ressourcengrenzen für DTU- und vCore-Datenbanken werden in der Sicht [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) in absoluten Werten verfügbar gemacht. Der Vergleich dieser Werte zwischen der zu migrierenden DTU-Datenbank und einer vCore-Datenbank unter Verwendung eines annähernd übereinstimmenden vCore-Dienstziels hilft Ihnen, das vCore-Dienstziel genauer auszuwählen.
- Die Zuordnungsabfrage gibt auch die Arbeitsspeichergröße pro Kern für die zu migrierende DTU-Datenbank oder den zu migrierenden Pool für elastische Datenbanken und für jede Hardwaregeneration im vCore-Modell zurück. Die Gewährleistung eines ähnlichen oder größeren Gesamtarbeitsspeichers nach der Migration zu vCore ist wichtig für Workloads, die einen großen Datencache im Arbeitsspeicher benötigen, um eine ausreichende Leistung zu erzielen, oder für Workloads, die für die Abfrageverarbeitung große Speicherzuweisungen erfordern. Bei solchen Workloads kann es je nach tatsächlicher Leistung erforderlich sein, die Anzahl der virtuellen Kerne zu erhöhen, um genügend Gesamtarbeitsspeicher zu erhalten.
- Bei Wahl des vCore-Dienstziels sollte die [bisherige Ressourcennutzung](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) der DTU-Datenbank berücksichtigt werden. DTU-Datenbanken mit durchweg nicht ausgelasteten CPU-Ressourcen benötigen möglicherweise weniger virtuellen Kerne als die von der Zuordnungsabfrage zurückgegebene Anzahl. Umgekehrt können DTU-Datenbanken, bei denen eine konstant hohe CPU-Auslastung eine unzureichende Leistung bei Workloads verursacht, mehr virtuellen Kerne erfordern, als von der Abfrage zurückgegeben werden.
- Wenn Sie Datenbanken mit sporadischen oder unvorhersehbaren Nutzungsmustern migrieren, sollten Sie den Computetarif [Serverlos](serverless-tier-overview.md) erwägen.  Beachten Sie, dass die maximale Anzahl gleichzeitiger Worker (Anforderungen) beim Tarif „Serverlos“ 75 % des Limits für bereitgestelltes Computing bei gleicher Anzahl der maximal konfigurierten virtuellen Kerne beträgt.  Außerdem beträgt der maximale verfügbare Arbeitsspeicher beim Tarif „Serverlos“ 3 GB multipliziert mit der maximalen Anzahl konfigurierter virtueller Kerne. Beispielsweise beläuft sich der maximale Arbeitsspeicher auf 120 GB, wenn 40 virtuelle Kerne konfiguriert wurden.   
- Beim vCore-Modell kann die unterstützte maximale Datenbankgröße je nach Hardwaregeneration variieren. Überprüfen Sie für große Datenbanken die unterstützten Höchstgrößen im vCore-Modell für [einzelne Datenbanken](resource-limits-vcore-single-databases.md) und [Pools für elastische Datenbanken](resource-limits-vcore-elastic-pools.md).
- Bei Pools für elastische Datenbanken weisen das [DTU](resource-limits-dtu-elastic-pools.md)- und das [vCore](resource-limits-vcore-elastic-pools.md)-Modell Unterschiede bei der maximal unterstützten Anzahl von Datenbanken pro Pool auf. Dies muss bei der Migration von Pools für elastische Datenbanken mit vielen Datenbanken berücksichtigt werden.
- Einige Hardwaregenerationen sind möglicherweise nicht in allen Regionen verfügbar. Prüfen Sie die Verfügbarkeit unter [Hardwaregenerationen](service-tiers-vcore.md#hardware-generations).

> [!IMPORTANT]
> Bei den genannten Größenrichtlinien für die Migration von DTU zu vCore handelt es sich um Richtwerte, die eine erste Einschätzung des Dienstziels der Zieldatenbank ermöglichen sollen.
>
> Die optimale Konfiguration der Zieldatenbank ist workloadabhängig. Zur Erzielung des optimalen Preis-Leistungs-Verhältnisses nach der Migration müssen Sie ggf. sowohl die Flexibilität des vCore-Modells nutzen, um die Anzahl virtueller Kerne, die [Hardwaregeneration](service-tiers-vcore.md#hardware-generations) sowie die [Dienst-](service-tiers-vcore.md#service-tiers) und [Compute](service-tiers-vcore.md#compute-tiers)-Ebene anzupassen, als auch andere Parameter für die Datenbankkonfiguration optimieren (etwa den [maximalen Grad an Parallelität](/sql/relational-databases/query-processing-architecture-guide#parallel-query-processing)).
> 

### <a name="dtu-to-vcore-migration-examples"></a>Beispiele für die Migration von DTU zu vCore

> [!NOTE]
> Die Werte in den folgenden Beispielen dienen nur zur Veranschaulichung. Die in den beschriebenen Szenarien zurückgegebenen tatsächlichen Werte können unterschiedlich sein.
> 

**Migrieren einer Standard-S9-Datenbank**

Die Zuordnungsabfrage gibt das folgende Ergebnis zurück (einige Spalten werden der Kürze halber nicht angezeigt):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|24,00|Gen5|5,40|16,800|7|24,000|5,05|

Wir sehen, dass die DTU-Datenbank 24 logische CPUs (virtuelle Kerne) mit 5,4 GB Speicher pro virtuellem Kern hat und Gen5-Hardware nutzt. Die direkte Entsprechung dazu ist eine universelle Datenbank mit 24 virtuellen Kernen auf Gen5-Hardware, d. h. das vCore-Dienstziel **GP_Gen5_24** .

**Migrieren einer Standard-S0-Datenbank**

Die Zuordnungsabfrage gibt das folgende Ergebnis zurück (einige Spalten werden der Kürze halber nicht angezeigt):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|0,25|Gen4|0,42|0,250|7|0,425|5,05|

Wir sehen, dass die DTU-Datenbank das Äquivalent von 0,25 logischen CPUs (virtuellen Kernen) hat, mit 0,42 GB Arbeitsspeicher pro virtuellem Kern, und Gen4-Hardware nutzt. Die kleinsten vCore-Dienstziele in den Hardwaregenerationen Gen4 und Gen5, **GP_Gen4_1** und **GP_Gen5_2** , bieten mehr Computeressourcen als die Standard-S0-Datenbank, sodass eine direkte Entsprechung nicht möglich ist. Da Gen4-Hardware [außer Betrieb genommen wird](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/), wird die Option **GP_Gen5_2** bevorzugt. Wenn außerdem die Workload für den Computetarif [Serverlos](serverless-tier-overview.md) gut geeignet ist, wäre **GP_S_Gen5_1** eine bessere Entsprechung.

**Migrieren einer Premium P15-Datenbank**

Die Zuordnungsabfrage gibt das folgende Ergebnis zurück (einige Spalten werden der Kürze halber nicht angezeigt):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|42,00|Gen5|4,86|29,400|7|42,000|5,05|

Wir sehen, dass die DTU-Datenbank 42 logische CPUs (virtuelle Kerne) mit 4,86 GB Arbeitsspeicher pro virtuellem Kern hat und Gen5-Hardware nutzt. Es gibt zwar kein vCore-Dienstziel mit 42 Kernen, aber das Dienstziel **BC_Gen5_40** ist sowohl in Bezug auf die CPU- als auch die Arbeitsspeicherkapazität sehr ähnlich und eine gute Entsprechung.

**Migrieren eines Pool für elastische Datenbanken mit 200 Basic-eDTUs**

Die Zuordnungsabfrage gibt das folgende Ergebnis zurück (einige Spalten werden der Kürze halber nicht angezeigt):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|4.00|Gen5|5,40|2,800|7|4,000|5,05|

Wir sehen, dass die DTU-Pool für elastische Datenbanken 4 logische CPUs (virtuelle Kerne) mit 5,4 GB Arbeitsspeicher pro virtuellem Kern hat und Gen5-Hardware nutzt. Die direkte Entsprechung im vCore-Modell ist ein Pool für elastische Datenbanken des Typs **GP_Gen5_4** . Dieses Serviceziel unterstützt jedoch maximal 200 Datenbanken pro Pool, während der Pool für elastische Datenbanken mit 200 Basic-eDTU bis zu 500 Datenbanken unterstützt. Wenn der zu migrierende Pool für elastische Datenbanken mehr als 200 Datenbanken umfasst, muss das entsprechende vCore-Dienstziel **GP_Gen5_6** lauten, das bis zu 500 Datenbanken unterstützt.

## <a name="migrate-geo-replicated-databases"></a>Migrieren georeplizierter Datenbanken

Die Migration vom DTU-basierten zum V-Kern-basierten Modell ähnelt dem Up- oder Downgrade der Georeplikationsbeziehungen zwischen Datenbanken auf der Standard- und Premium-Dienstebene. Sie müssen die Georeplikation während der Migration nicht beenden, es müssen jedoch die folgenden Sequenzierungsregeln eingehalten werden:

- Bei einem Upgrade müssen Sie zuerst das Upgrade für die sekundäre Datenbank und anschließend das Upgrade für die primäre Datenbank durchführen.
- Drehen Sie bei einem Downgrade die Reihenfolge um: Führen Sie zuerst das Downgrade für die primäre und anschließend das Downgrade für die sekundäre Datenbank durch.

Bei Verwendung der Georeplikation zwischen zwei Pools für elastische Datenbanken empfiehlt es sich, einen Pool als primäres Element und den anderen als sekundäres Element festzulegen. In diesem Fall sollten Sie sich beim Migrieren von Pools für elastische Datenbanken an die gleiche Sequenzierungsanleitung halten. Falls Sie jedoch über Pools für elastische Datenbanken verfügen, die sowohl primäre als auch sekundäre Datenbanken enthalten, müssen Sie den Pool mit der höheren Auslastung als primäres Element behandeln und die Sequenzierungsregeln entsprechend befolgen.  

Die folgende Tabelle enthält eine Anleitung für spezifische Migrationsszenarien:

|Aktuelle Dienstebene|Zieldienstebene|Migrationstyp|Benutzeraktionen|
|---|---|---|---|
|Standard|Allgemeiner Zweck|Seitwärts|Die Migration ist in einer beliebigen Reihenfolge möglich, aber Sie müssen, wie zuvor beschrieben, für eine geeignete Größe der virtuellen Kerne sorgen.|
|Premium|Unternehmenskritisch|Seitwärts|Die Migration ist in einer beliebigen Reihenfolge möglich, aber Sie müssen, wie zuvor beschrieben, für eine geeignete Größe der virtuellen Kerne sorgen.|
|Standard|Unternehmenskritisch|Aktualisieren|Sekundäre Einheit muss zuerst migriert werden|
|Unternehmenskritisch|Standard|Downgrade|Primäre Einheit muss zuerst migriert werden|
|Premium|Allgemeiner Zweck|Downgrade|Primäre Einheit muss zuerst migriert werden|
|Allgemeiner Zweck|Premium|Aktualisieren|Sekundäre Einheit muss zuerst migriert werden|
|Unternehmenskritisch|Allgemeiner Zweck|Downgrade|Primäre Einheit muss zuerst migriert werden|
|Allgemeiner Zweck|Unternehmenskritisch|Aktualisieren|Sekundäre Einheit muss zuerst migriert werden|
||||

## <a name="migrate-failover-groups"></a>Migrieren von Failovergruppen

Für die Migration von Failovergruppen mit mehreren Datenbanken ist eine individuelle Migration der primären und sekundären Datenbank erforderlich. Während dieses Prozesses gelten die gleichen Aspekte und Sequenzierungsregeln. Nachdem die Datenbanken auf das V-Kern-basierte Kaufmodell umgestellt wurden, bleibt die Failovergruppe mit den gleichen Richtlinieneinstellungen wirksam.

### <a name="create-a-geo-replication-secondary-database"></a>Erstellen einer sekundären Datenbank für die Georeplikation

Eine sekundäre Datenbank für die Georeplikation (Geo-Sekundärdatenbank) kann nur mit der gleichen Dienstebene erstellt werden, die auch für die primäre Datenbank verwendet wurde. Bei Datenbanken mit hoher Protokollgenerierungsrate empfiehlt es sich, die Geo-Sekundärdatenbank mit der gleichen Computegröße zu erstellen wie die primäre Datenbank.

Wenn Sie eine Geo-Sekundärdatenbank im Pool für elastische Datenbanken für eine einzelne primäre Datenbank erstellen, muss die Einstellung `maxVCore` für den Pool der Computegröße der primären Datenbank entsprechen. Wenn Sie eine Geo-Sekundärdatenbank für eine primäre Datenbank in einem anderen Pool für elastische Datenbanken erstellen, sollte die Einstellung `maxVCore` der Pools gleich sein.

## <a name="use-database-copy-to-migrate-from-dtu-to-vcore"></a>Verwenden von Datenbankkopien zum Migrieren von DTU zu vCore

Sie können eine beliebige Datenbank mit einer DTU-basierten Computegröße in eine Datenbank mit einer V-Kern-basierten Computegröße kopieren, ohne dass hierfür Einschränkungen oder spezielle Sequenzierungen gelten, solange die Zielcomputegröße die maximale Datenbankgröße der Quelldatenbank unterstützt. Die Datenbankkopie erstellt eine Momentaufnahme der Daten zum Startzeitpunkt des Kopiervorgangs und synchronisiert keine Daten zwischen Quelle und Ziel.

## <a name="next-steps"></a>Nächste Schritte

- Die spezifischen Computegrößen und Speichergrößenoptionen für Einzeldatenbanken finden Sie unter [V-Kern-basierte Ressourceneinschränkungen in SQL-Datenbank für Einzeldatenbanken](resource-limits-vcore-single-databases.md).
- Die spezifischen Computegrößen und Speichergrößenoptionen für Pools für elastische Datenbanken finden Sie unter [V-Kern-basierte Ressourceneinschränkungen in SQL-Datenbank für Pools für elastische Datenbanken](resource-limits-vcore-elastic-pools.md).
---
title: Ressourcenlimits des auf virtuellen Kernen basierenden Kaufmodells in Azure SQL-Datenbank – Pools für elastische Datenbanken | Microsoft-Dokumentation
description: Diese Seite beschreibt einige allgemeine Ressourcenlimits des auf virtuellen Kernen basierenden Kaufmodells für Pools für elastische Datenbanken in Azure SQL-Datenbank.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 05/23/2019
ms.openlocfilehash: 98bd70d9f6eb70cb7848dfa74e19c78e55a34991
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240351"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-based-purchasing-model-limits"></a>Ressourcenlimits für Pools für elastische Datenbanken, die das vCore-basierte Kaufmodell verwenden

Dieser Artikel enthält ausführliche Angaben zu Ressourcenlimits für Pools für elastische Datenbanken und Pooldatenbanken in Azure SQL-Datenbank mit dem vCore-basierten Kaufmodell.

Informationen zu Einschränkungen bei DTU-basierten Kaufmodellen finden Sie unter [Ressourcenlimits des DTU-basierten Kaufmodells in SQL-Datenbank – Pools für elastische Datenbanken](sql-database-dtu-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> Unter bestimmten Umständen müssen Sie ggf. eine Datenbank verkleinern, um ungenutzten Speicherplatz freizugeben. Weitere Informationen finden Sie unter [Verwalten von Dateispeicherplatz in Azure SQL-Datenbank](sql-database-file-space-management.md).

Sie können im [Azure-Portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), mit [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), mit der [Azure CLI](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases) oder der [REST-API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases) Dienstebene, Computegröße und Speichermenge festlegen.

> [!IMPORTANT]
> Anleitungen und Überlegungen zur Skalierung finden Sie unter [Skalieren eines Pools für elastische Datenbanken](sql-database-elastic-pool-scale.md).
> [!NOTE]
> Die Ressourcengrenzwerte einzelner Datenbanken in Pools für elastische Datenbanken entsprechen im Allgemeinen den Grenzwerten einzelner Datenbanken außerhalb von Pools, welche die gleiche Computegröße aufweisen. Auf eine GP_Gen4_1-Datenbank können z.B. max. 200 Worker gleichzeitig zugreifen. Entsprechend können auch maximal 200 Worker auf eine Datenbank in einem GP_Gen4_1-Pool zugreifen. Beachten Sie, dass die Gesamtanzahl gleichzeitiger Worker in einem GP_Gen4_1-Pool 210 beträgt.

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>Universelle Dienstebene: Speicher- und Computegrößen

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Universelle Dienstebene: Computeplattform der 4. Generation (Teil 1)

|Computegröße|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Hardwaregeneration|4|4|4|4|4|4|
|V-Kerne|1|2|3|4|5|6|
|Arbeitsspeicher (GB)|7|14|21|28|35|42|
|Max. Anzahl Datenbanken pro Pool|100|200|500|500|500|500|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|–|–|
|Maximale Datengröße (GB)|512|756|756|1536|1536|1536|
|Maximale Protokollgröße|154|227|227|461|461|461|
|tempdb-Größe (GB)|32|64|96|128|160|192|
|Speichertyp|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|
|E/A-Wartezeit (ungefähr)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|
|Ziel-IOPS (64 KB)|500|1000|1500|2000|2500|3000|
|Grenzwerte für die Protokollrate (MB/s)|4,6875|9,375|14,0625|18,75|23,4375|28,125|
|Max. gleichzeitige Worker pro Pool (Anforderungen) * |210|420|630|840|1050|1260|
|Max. gleichzeitige Anmeldungen pro Pool * |210|420|630|840|1050|1260|
|Maximal zulässige Sitzungen|30000|30000|30000|30000|30000|30000|
|Min/Max. V-Kern-Auswahl pro Datenbank für Pools für elastische Datenbanken|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1...3|0, 0,25, 0,5, 1...4|0, 0,25, 0,5, 1...5|0, 0,25, 0,5, 1...6|
|Anzahl von Replikaten|1|1|1|1|1|1|
|Multi-AZ|–|–|–|–|–|–|
|Horizontale Leseskalierung|–|–|–|–|–|–|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

\* Den Wert für „Max. gleichzeitige Worker“ (Anforderungen) für einzelne Datenbanken finden Sie unter [Ressourceneinschränkungen für einzelne Datenbanken](sql-database-vcore-resource-limits-single-databases.md). Wenn für den Pool für elastische Datenbanken beispielsweise Gen5 verwendet wird und die maximale Anzahl von V-Kernen pro Datenbank „2“ beträgt, gilt für die Anzahl von gleichzeitigen Workern der Grenzwert „200“.  Wenn die maximale Anzahl von V-Kernen pro Datenbank „0,5“ beträgt, gilt für die Anzahl von gleichzeitigen Workern der Grenzwert „50“, da unter Gen5 maximal 100 gleichzeitige Worker pro V-Kern verwendet werden.  Für andere Einstellungen zur maximalen Anzahl von V-Kernen pro Datenbank (1 V-Kern oder weniger), wird die maximale Anzahl von gleichzeitigen Workern auf ähnliche Weise neu skaliert.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Universelle Dienstebene: Computeplattform der 4. Generation (Teil 2)

|Computegröße|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Hardwaregeneration|4|4|4|4|4|4|
|V-Kerne|7|8|9|10|16|24|
|Arbeitsspeicher (GB)|49|56|63|70|112|168|
|Max. Anzahl Datenbanken pro Pool|500|500|500|500|500|500|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|–|–|
|Maximale Datengröße (GB)|1536|2048|2048|2048|3.584|4096|
|Maximale Protokollgröße (GB)|461|614|614|614|1075|1229|
|tempdb-Größe (GB)|224|256|288|320|384|384|
|Speichertyp|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|
|E/A-Wartezeit (ungefähr)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|
|Ziel-IOPS (64 KB)|3500|4000|4500|5.000|7.000|7.000|
|Grenzwerte für die Protokollrate (MB/s)|32,8125|37,5|37,5|37,5|37,5|37,5|
|Max. gleichzeitige Worker pro Pool (Anforderungen) *|1470|1680|1890|2100|3360|5.040|
|Max. gleichzeitige Anmeldungen (Anforderungen) *|1470|1680|1890|2100|3360|5.040|
|Maximal zulässige Sitzungen|30000|30000|30000|30000|30000|30000|
|Min/Max. V-Kern-Auswahl pro Datenbank für Pools für elastische Datenbanken|0, 0,25, 0,5, 1...7|0, 0,25, 0,5, 1...8|0, 0,25, 0,5, 1...9|0, 0,25, 0,5, 1...10|0, 0,25, 0,5, 1...10, 16|0, 0,25, 0,5, 1...10, 16, 24|
|Anzahl von Replikaten|1|1|1|1|1|1|
|Multi-AZ|–|–|–|–|–|–|
|Horizontale Leseskalierung|–|–|–|–|–|–|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

\* Den Wert für „Max. gleichzeitige Worker“ (Anforderungen) für einzelne Datenbanken finden Sie unter [Ressourceneinschränkungen für einzelne Datenbanken](sql-database-vcore-resource-limits-single-databases.md). Wenn für den Pool für elastische Datenbanken beispielsweise Gen5 verwendet wird und die maximale Anzahl von V-Kernen pro Datenbank „2“ beträgt, gilt für die Anzahl von gleichzeitigen Workern der Grenzwert „200“.  Wenn die maximale Anzahl von V-Kernen pro Datenbank „0,5“ beträgt, gilt für die Anzahl von gleichzeitigen Workern der Grenzwert „50“, da unter Gen5 maximal 100 gleichzeitige Worker pro V-Kern verwendet werden.  Für andere Einstellungen zur maximalen Anzahl von V-Kernen pro Datenbank (1 V-Kern oder weniger), wird die maximale Anzahl von gleichzeitigen Workern auf ähnliche Weise neu skaliert.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Universelle Dienstebene: Computeplattform der 5. Generation (Teil 1)

|Computegröße|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Hardwaregeneration|5|5|5|5|5|5|5|
|V-Kerne|2|4|6|8|10|12|14|
|Arbeitsspeicher (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Max. Anzahl Datenbanken pro Pool|100|200|500|500|500|500|500|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|–|–|–|
|Maximale Datengröße (GB)|512|756|756|1536|1536|1536|
|Maximale Protokollgröße (GB)|154|227|227|461|461|461|461|
|tempdb-Größe (GB)|64|128|192|256|320|384|384|
|Speichertyp|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|
|E/A-Wartezeit (ungefähr)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|
|Ziel-IOPS (64 KB)|1000|2000|3000|4000|5.000|6000|7.000|
|Grenzwerte für die Protokollrate (MB/s)|4,6875|9,375|14,0625|18,75|23,4375|28,125|32,8125|
|Max. gleichzeitige Worker pro Pool (Anforderungen) *|210|420|630|840|1050|1260|1470|
|Max. gleichzeitige Anmeldungen pro Pool (Anforderungen) *|210|420|630|840|1050|1260|1470|
|Maximal zulässige Sitzungen|30000|30000|30000|30000|30000|30000|30000|
|Min/Max. V-Kern-Auswahl pro Datenbank für Pools für elastische Datenbanken|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1...4|0, 0,25, 0,5, 1...6|0, 0,25, 0,5, 1...8|0, 0,25, 0,5, 1...10|0, 0,25, 0,5, 1...12|0, 0,25, 0,5, 1...14|
|Anzahl von Replikaten|1|1|1|1|1|1|1|
|Multi-AZ|–|–|–|–|–|–|–|
|Horizontale Leseskalierung|–|–|–|–|–|–|–|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

\* Den Wert für „Max. gleichzeitige Worker“ (Anforderungen) für einzelne Datenbanken finden Sie unter [Ressourceneinschränkungen für einzelne Datenbanken](sql-database-vcore-resource-limits-single-databases.md). Wenn für den Pool für elastische Datenbanken beispielsweise Gen5 verwendet wird und die maximale Anzahl von V-Kernen pro Datenbank „2“ beträgt, gilt für die Anzahl von gleichzeitigen Workern der Grenzwert „200“.  Wenn die maximale Anzahl von V-Kernen pro Datenbank „0,5“ beträgt, gilt für die Anzahl von gleichzeitigen Workern der Grenzwert „50“, da unter Gen5 maximal 100 gleichzeitige Worker pro V-Kern verwendet werden.  Für andere Einstellungen zur maximalen Anzahl von V-Kernen pro Datenbank (1 V-Kern oder weniger), wird die maximale Anzahl von gleichzeitigen Workern auf ähnliche Weise neu skaliert.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Universelle Dienstebene: Computeplattform der 5. Generation (Teil 2)

|Computegröße|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Hardwaregeneration|5|5|5|5|5|5|5|
|V-Kerne|16|18|20|24|32|40|80|
|Arbeitsspeicher (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Max. Anzahl Datenbanken pro Pool|500|500|500|500|500|500|500|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|–|–|–|
|Maximale Datengröße (GB)|2048|2048|3072|3072|4096|4096|4096|
|Maximale Protokollgröße (GB)|614|614|922|922|1229|1229|1229|
|tempdb-Größe (GB)|384|384|384|384|384|384|384|
|Speichertyp|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|
|E/A-Wartezeit (ungefähr)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|
|Ziel-IOPS (64 KB)|7.000|7.000|7.000|7.000|7.000|7.000|7.000|
|Grenzwerte für die Protokollrate (MB/s)|37,5|37,5|37,5|37,5|37,5|37,5|37,5|
|Max. gleichzeitige Worker pro Pool (Anforderungen) *|1680|1890|2100|2.520|33600|4.200|8.400|
|Max. gleichzeitige Anmeldungen pro Pool (Anforderungen) *|1680|1890|2100|2.520|33600|4.200|8.400|
|Min/Max. V-Kern-Auswahl pro Datenbank für Pools für elastische Datenbanken|0, 0,25, 0,5, 1...16|0, 0,25, 0,5, 1...18|0, 0,25, 0,5, 1...20|0, 0,25, 0,5, 1...20, 24|0, 0,25, 0,5, 1...20, 24, 32|0, 0,25, 0,5, 1...16, 24, 32, 40|0, 0,25, 0,5, 1...16, 24, 32, 40, 80|
|Anzahl von Replikaten|1|1|1|1|1|1|1|
|Multi-AZ|–|–|–|–|–|–|–|
|Horizontale Leseskalierung|–|–|–|–|–|–|–|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

\* Den Wert für „Max. gleichzeitige Worker“ (Anforderungen) für einzelne Datenbanken finden Sie unter [Ressourceneinschränkungen für einzelne Datenbanken](sql-database-vcore-resource-limits-single-databases.md). Wenn für den Pool für elastische Datenbanken beispielsweise Gen5 verwendet wird und die maximale Anzahl von V-Kernen pro Datenbank „2“ beträgt, gilt für die Anzahl von gleichzeitigen Workern der Grenzwert „200“.  Wenn die maximale Anzahl von V-Kernen pro Datenbank „0,5“ beträgt, gilt für die Anzahl von gleichzeitigen Workern der Grenzwert „50“, da unter Gen5 maximal 100 gleichzeitige Worker pro V-Kern verwendet werden.  Für andere Einstellungen zur maximalen Anzahl von V-Kernen pro Datenbank (1 V-Kern oder weniger), wird die maximale Anzahl von gleichzeitigen Workern auf ähnliche Weise neu skaliert.

## <a name="business-critical-service-tier-storage-sizes-and-compute-sizes"></a>Dienstebene „Unternehmenskritisch“: Speicher- und Computegrößen

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Dienstebene „Unternehmenskritisch“: Computeplattform der 4. Generation (Teil 1)

|Computegröße|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Hardwaregeneration|4|4|4|4|4|4|
|V-Kerne|1|2|3|4|5|6|
|Arbeitsspeicher (GB)|7|14|21|28|35|42|
|Max. Anzahl Datenbanken pro Pool|Nur einzelne Datenbanken werden für diese Computegröße unterstützt|50|100|100|100|100|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|1|2|3|4|5|6|
|Speichertyp|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|Maximale Datengröße (GB)|650|650|650|650|650|650|
|Maximale Protokollgröße (GB)|195|195|195|195|195|195|
|tempdb-Größe (GB)|32|64|96|128|160|192|
|E/A-Wartezeit (ungefähr)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|
|Ziel-IOPS (64 KB)|5.000|10000|15000|20000|25000|30000|
|Grenzwerte für die Protokollrate (MB/s)|10|20|30|40|50|60|
|Max. gleichzeitige Worker pro Pool (Anforderungen) *|210|420|630|840|1050|1260|
|Max. gleichzeitige Anmeldungen pro Pool (Anforderungen) *|210|420|630|840|1050|1260|
|Maximal zulässige Sitzungen|30000|30000|30000|30000|30000|30000|
|Min/Max. V-Kern-Auswahl pro Datenbank für Pools für elastische Datenbanken|–|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1...3|0, 0,25, 0,5, 1...4|0, 0,25, 0,5, 1...5|0, 0,25, 0,5, 1...6|
|Anzahl von Replikaten|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Horizontale Leseskalierung|Ja|Ja|Ja|Ja|Ja|Ja|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

\* Den Wert für „Max. gleichzeitige Worker“ (Anforderungen) für einzelne Datenbanken finden Sie unter [Ressourceneinschränkungen für einzelne Datenbanken](sql-database-vcore-resource-limits-single-databases.md). Wenn für den Pool für elastische Datenbanken beispielsweise Gen5 verwendet wird und die maximale Anzahl von V-Kernen pro Datenbank „2“ beträgt, gilt für die Anzahl von gleichzeitigen Workern der Grenzwert „200“.  Wenn die maximale Anzahl von V-Kernen pro Datenbank „0,5“ beträgt, gilt für die Anzahl von gleichzeitigen Workern der Grenzwert „50“, da unter Gen5 maximal 100 gleichzeitige Worker pro V-Kern verwendet werden.  Für andere Einstellungen zur maximalen Anzahl von V-Kernen pro Datenbank (1 V-Kern oder weniger), wird die maximale Anzahl von gleichzeitigen Workern auf ähnliche Weise neu skaliert.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Dienstebene „Unternehmenskritisch“: Computeplattform der 4. Generation (Teil 2)

|Computegröße|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Hardwaregeneration|4|4|4|4|4|4|
|V-Kerne|7|8|9|10|16|24|
|Arbeitsspeicher (GB)|81,6|91,8|102|122,4|163,2|204|
|Max. Anzahl Datenbanken pro Pool|100|100|100|100|100|100|
|Columnstore-Unterstützung|–|–|–|–|–|–|
|In-Memory-OLTP-Speicher (GB)|7|8|9,5|11|20|36|
|Speichertyp|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|Maximale Datengröße (GB)|650|650|650|650|1024|1024|
|Maximale Protokollgröße (GB)|195|195|195|195|307|307|
|tempdb-Größe (GB)|224|256|288|320|384|384|
|E/A-Wartezeit (ungefähr)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|
|Ziel-IOPS (64 KB)|35000|40.000|45000|50000|80.000|120000|
|Grenzwerte für die Protokollrate (MB/s)|70|80|80|80|80|80|
|Max. gleichzeitige Worker pro Pool (Anforderungen) *|1470|1680|1890|2100|3360|5.040|
|Max. gleichzeitige Anmeldungen pro Pool (Anforderungen) *|1470|1680|1890|2100|3360|5.040|
|Maximal zulässige Sitzungen|30000|30000|30000|30000|30000|30000|
|Min/Max. V-Kern-Auswahl pro Datenbank für Pools für elastische Datenbanken|0, 0,25, 0,5, 1...7|0, 0,25, 0,5, 1...8|0, 0,25, 0,5, 1...9|0, 0,25, 0,5, 1...10|0, 0,25, 0,5, 1...10, 16|0, 0,25, 0,5, 1...10, 16, 24|
|Anzahl von Replikaten|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Horizontale Leseskalierung|Ja|Ja|Ja|Ja|Ja|Ja|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

\* Den Wert für „Max. gleichzeitige Worker“ (Anforderungen) für einzelne Datenbanken finden Sie unter [Ressourceneinschränkungen für einzelne Datenbanken](sql-database-vcore-resource-limits-single-databases.md). Wenn für den Pool für elastische Datenbanken beispielsweise Gen5 verwendet wird und die maximale Anzahl von V-Kernen pro Datenbank „2“ beträgt, gilt für die Anzahl von gleichzeitigen Workern der Grenzwert „200“.  Wenn die maximale Anzahl von V-Kernen pro Datenbank „0,5“ beträgt, gilt für die Anzahl von gleichzeitigen Workern der Grenzwert „50“, da unter Gen5 maximal 100 gleichzeitige Worker pro V-Kern verwendet werden.  Für andere Einstellungen zur maximalen Anzahl von V-Kernen pro Datenbank (1 V-Kern oder weniger), wird die maximale Anzahl von gleichzeitigen Workern auf ähnliche Weise neu skaliert.

#### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Dienstebene „Unternehmenskritisch“: Computeplattform der 5. Generation (Teil 1)

|Computegröße|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Hardwaregeneration|5|5|5|5|5|5|5|
|V-Kerne|2|4|6|8|10|12|14|
|Arbeitsspeicher (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Max. Anzahl Datenbanken pro Pool|Nur einzelne Datenbanken werden für diese Computegröße unterstützt|50|100|100|100|100|100|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|1,571|3,142|4,713|6,284|8,655|11,026|13,397|
|Maximale Datengröße (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maximale Protokollgröße (GB)|307|307|307|461|461|922|922|
|tempdb-Größe (GB)|64|128|192|256|320|384|384|
|Speichertyp|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|E/A-Wartezeit (ungefähr)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|
|Ziel-IOPS (64 KB)|5.000|10000|15000|20000|25000|30000|35000|
|Grenzwerte für die Protokollrate (MB/s)|15|30|45|60|75|90|105|
|Max. gleichzeitige Worker pro Pool (Anforderungen) *|210|420|630|840|1050|1260|1470|
|Max. gleichzeitige Anmeldungen pro Pool (Anforderungen) *|210|420|630|840|1050|1260|1470|
|Maximal zulässige Sitzungen|30000|30000|30000|30000|30000|30000|30000|
|Min/Max. V-Kern-Auswahl pro Datenbank für Pools für elastische Datenbanken|–|0, 0,25, 0,5, 1...4|0, 0,25, 0,5, 1...6|0, 0,25, 0,5, 1...8|0, 0,25, 0,5, 1...10|0, 0,25, 0,5, 1...12|0, 0,25, 0,5, 1...14|
|Anzahl von Replikaten|4|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Horizontale Leseskalierung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

\* Den Wert für „Max. gleichzeitige Worker“ (Anforderungen) für einzelne Datenbanken finden Sie unter [Ressourceneinschränkungen für einzelne Datenbanken](sql-database-vcore-resource-limits-single-databases.md). Wenn für den Pool für elastische Datenbanken beispielsweise Gen5 verwendet wird und die maximale Anzahl von V-Kernen pro Datenbank „2“ beträgt, gilt für die Anzahl von gleichzeitigen Workern der Grenzwert „200“.  Wenn die maximale Anzahl von V-Kernen pro Datenbank „0,5“ beträgt, gilt für die Anzahl von gleichzeitigen Workern der Grenzwert „50“, da unter Gen5 maximal 100 gleichzeitige Worker pro V-Kern verwendet werden.  Für andere Einstellungen zur maximalen Anzahl von V-Kernen pro Datenbank (1 V-Kern oder weniger), wird die maximale Anzahl von gleichzeitigen Workern auf ähnliche Weise neu skaliert.

#### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Dienstebene „Unternehmenskritisch“: Computeplattform der 5. Generation (Teil 2)

|Computegröße|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Hardwaregeneration|5|5|5|5|5|5|5|
|V-Kerne|16|18|20|24|32|40|80|
|Arbeitsspeicher (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Max. Anzahl Datenbanken pro Pool|100|100|100|100|100|100|100|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|15,768|18,139|20,51|25,252|37,936|52,22|131,64|
|Maximale Datengröße (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maximale Protokollgröße (GB)|922|922|922|1229|1229|1229|1229|
|tempdb-Größe (GB)|384|384|384|384|384|384|384|
|Speichertyp|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|E/A-Wartezeit (ungefähr)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|
|Ziel-IOPS (64 KB)|40.000|45000|50000|60000|80.000|100.000|200.000|
|Grenzwerte für die Protokollrate (MB/s)|120|120|120|120|120|120|120|
|Max. gleichzeitige Worker pro Pool (Anforderungen) *|1680|1890|2100|2.520|3360|4.200|8.400|
|Max. gleichzeitige Anmeldungen pro Pool (Anforderungen) *|1680|1890|2100|2.520|3360|4.200|8.400|
|Maximal zulässige Sitzungen|30000|30000|30000|30000|30000|30000|30000|
|Min/Max. V-Kern-Auswahl pro Datenbank für Pools für elastische Datenbanken|0, 0,25, 0,5, 1...16|0, 0,25, 0,5, 1...18|0, 0,25, 0,5, 1...20|0, 0,25, 0,5, 1...20, 24|0, 0,25, 0,5, 1...20, 24, 32|0, 0,25, 0,5, 1...20, 24, 32, 40|0, 0,25, 0,5, 1...20, 24, 32, 40, 80|
|Anzahl von Replikaten|4|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Horizontale Leseskalierung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

\* Den Wert für „Max. gleichzeitige Worker“ (Anforderungen) für einzelne Datenbanken finden Sie unter [Ressourceneinschränkungen für einzelne Datenbanken](sql-database-vcore-resource-limits-single-databases.md). Wenn für den Pool für elastische Datenbanken beispielsweise Gen5 verwendet wird und die maximale Anzahl von V-Kernen pro Datenbank „2“ beträgt, gilt für die Anzahl von gleichzeitigen Workern der Grenzwert „200“.  Wenn die maximale Anzahl von V-Kernen pro Datenbank „0,5“ beträgt, gilt für die Anzahl von gleichzeitigen Workern der Grenzwert „50“, da unter Gen5 maximal 100 gleichzeitige Worker pro V-Kern verwendet werden.  Für andere Einstellungen zur maximalen Anzahl von V-Kernen pro Datenbank (1 V-Kern oder weniger), wird die maximale Anzahl von gleichzeitigen Workern auf ähnliche Weise neu skaliert.

Wenn alle virtuellen Kerne eines Pools für elastische Datenbanken verwendet werden, erhält jede Datenbank im Pool gleich viel Computeressourcen zum Verarbeiten von Abfragen. Der SQL-Datenbank-Dienst bietet eine faire gemeinsame Nutzung von Ressourcen durch Datenbanken, indem gleiche Slices an Computezeit zugesichert werden. Diese faire gemeinsame Nutzung in Pools für elastische Datenbanken ergänzt die Ressourcen, die jeder Datenbank auf andere Weise garantiert werden, wenn die Mindestanzahl von virtuellen Kernen pro Datenbank auf einen Wert ungleich null festgelegt ist.

## <a name="database-properties-for-pooled-databases"></a>Eigenschaften von Pooldatenbanken

Die folgende Tabelle beschreibt die Eigenschaften von Pooldatenbanken.

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| Maximale Anzahl virtueller Kerne pro Datenbank |Die maximale Anzahl von virtuellen Kernen, die jede Datenbank im Pool verwenden kann, sofern basierend auf der Nutzung durch andere Datenbanken im Pool verfügbar. Die maximale Anzahl von virtuellen Kernen pro Datenbank ist keine Ressourcengarantie für eine Datenbank. Dies ist eine globale Einstellung, die für alle Datenbanken im Pool gilt. Legen Sie die maximale Anzahl von virtuellen Kernen pro Datenbank hoch genug fest, sodass Lastspitzen bei der Datenbanknutzung verarbeitet werden können. Sie sollten ein gewisses Maß an Mehrlast einplanen, da für den Pool im Allgemeinen von Nutzungsmustern starker und schwacher Auslastung ausgegangen wird, bei der aber nicht alle Datenbanken gleichzeitig stark ausgelastet sind.|
| Minimale Anzahl virtueller Kerne pro Datenbank |Die minimale Anzahl von virtuellen Kernen, die für jede Datenbank im Pool garantiert werden. Dies ist eine globale Einstellung, die für alle Datenbanken im Pool gilt. Die Mindestanzahl von virtuellen Kernen pro Datenbank kann auf 0 festgelegt werden. Dies ist auch der Standardwert. Diese Eigenschaft ist auf einen Wert zwischen 0 und der durchschnittlichen Nutzung der virtuellen Kerne pro Datenbank festgelegt. Das Produkt aus der Anzahl von Datenbanken im Pool und der Mindestzahl von virtuellen Kernen pro Datenbank darf die tatsächliche Anzahl der virtuellen Kerne pro Pool nicht übersteigen.|
| Max. Speicherkapazität pro Datenbank |Die maximale Datenbankgröße, die vom Benutzer für eine Datenbank in einem Pool festgelegt wird. Pooldatenbanken nutzen den zugeordneten Poolspeicher gemeinsam, daher ist die Größe, die eine Datenbank erreichen kann, auf den jeweils kleineren Wert des verbleibenden Poolspeichers oder der Datenbankgröße beschränkt. Die maximale Datenbankgröße bezieht sich auf die maximale Größe der Datendateien und umfasst nicht den von Protokolldateien belegten Speicherplatz. |
|||

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu V-Kern-Ressourcenlimits bei Singletons finden Sie unter [Ressourcenlimits bei Singletons, die das vCore-basierte Kaufmodell verwenden](sql-database-vcore-resource-limits-single-databases.md).
- Informationen zu DTU-Ressourcenlimits bei Singletons finden Sie unter [Ressourcenlimits bei Singletons, die das DTU-basierte Kaufmodell verwenden](sql-database-dtu-resource-limits-single-databases.md).
- Informationen zu Ressourcenlimits für Pools für elastische Datenbanken finden Sie unter [Ressourcenlimits für Pools für elastische Datenbanken, die das DTU-basierte Kaufmodell verwenden](sql-database-dtu-resource-limits-elastic-pools.md).
- Informationen zu den Ressourcenlimits für verwaltete Instanzen finden Sie unter [Ressourcenlimits bei verwalteten Instanzen](sql-database-managed-instance-resource-limits.md).
- Informationen zu allgemeinen Azure-Einschränkungen finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md).
- Informationen zu Ressourcenlimits auf Server- und Abonnementebene auf einem Datenbankserver finden Sie unter [Übersicht über Ressourcenlimits für einen SQL-Datenbank-Server](sql-database-resource-limits-database-server.md).

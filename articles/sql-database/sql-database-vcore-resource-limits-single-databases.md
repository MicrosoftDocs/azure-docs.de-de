---
title: Ressourcenlimits des auf virtuellen Kernen basierenden Kaufmodells in Azure SQL-Datenbank – Einzeldatenbank | Microsoft-Dokumentation
description: Diese Seite beschreibt einige allgemeine Ressourcenlimits des auf virtuellen Kernen basierenden Kaufmodells für eine Einzeldatenbank in Azure SQL-Datenbank.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 04/22/2019
ms.openlocfilehash: ca29bfdb381c5cab0625a320679331c82f63c887
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67118069"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-based-purchasing-model"></a>Ressourcenlimits für Einzeldatenbanken, die das auf virtuellen Kernen (V-Kernen) basierende (vCore-basierte) Kaufmodell verwenden

Dieser Artikel enthält ausführliche Angaben zu Ressourcenlimits für Einzeldatenbanken in Azure SQL-Datenbank, die das vCore-basierte Kaufmodell verwenden.

Das DTU-basierte Kaufmodell weist Ressourcenlimits für Einzeldatenbanken auf einem SQL-Datenbank-Server auf. Informationen dazu finden Sie unter [Übersicht über Ressourcenlimits auf einem SQL-Datenbank-Server](sql-database-resource-limits-database-server.md).

> [!IMPORTANT]
> Unter bestimmten Umständen müssen Sie ggf. eine Datenbank verkleinern, um ungenutzten Speicherplatz freizugeben. Weitere Informationen finden Sie unter [Verwalten von Dateispeicherplatz in Azure SQL-Datenbank](sql-database-file-space-management.md).

Sie können mit dem [Azure-Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), der [Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases) oder der [REST-API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases) Dienstebene, Computegröße und Speichermenge für eine einzelne Datenbank festlegen.

> [!IMPORTANT]
> Anleitungen und Überlegungen zur Skalierung finden Sie unter [Skalieren eines Singletons](sql-database-single-database-scale.md).

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>Universelle Dienstebene: Speicher- und Computegrößen

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Universelle Dienstebene: Computeplattform der 4. Generation (Teil 1)

|Computegröße|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Hardwaregeneration|4|4|4|4|4|4|
|V-Kerne|1|2|3|4|5|6|
|Arbeitsspeicher (GB)|7|14|21|28|35|42|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|–|–|
|Maximale Datengröße (GB)|1024|1024|1024|1536|1536|1536|
|Maximale Protokollgröße (GB)|307|307|307|461|461|461|
|tempdb-Größe (GB)|32|64|96|128|160|192|
|Speichertyp|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|
|E/A-Wartezeit (ungefähr)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|
|Ziel-IOPS (64 KB)|500|1000|1500|2000|2500|3000|
|Grenzwerte für die Protokollrate (MB/s)|3,75|7,5|11,25|15|18,75|22,5|
|Max. gleichzeitige Worker (Anforderungen)|200|400|600|800|1000|1200|
|Maximal zulässige Sitzungen|30000|30000|30000|30000|30000|30000|
|Anzahl von Replikaten|1|1|1|1|1|1|
|Multi-AZ|–|–|–|–|–|–|
|Horizontale Leseskalierung|–|–|–|–|–|–|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Universelle Dienstebene: Computeplattform der 4. Generation (Teil 2)

|Computegröße|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Hardwaregeneration|4|4|4|4|4|4|
|V-Kerne|7|8|9|10|16|24|
|Arbeitsspeicher (GB)|49|56|63|70|112|168|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|–|–|
|Maximale Datengröße (GB)|1536|3072|3072|3072|4096|4096|
|Maximale Protokollgröße (GB)|461|922|922|922|1229|1229|
|tempdb-Größe (GB)|224|256|288|320|384|384|
|Speichertyp|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|
|E/A-Wartezeit (ungefähr)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)
|Ziel-IOPS (64 KB)|3500|4000|4500|5\.000|7\.000|7\.000|
|Grenzwerte für die Protokollrate (MB/s)|26,25|30|30|30|30|30|
|Max. gleichzeitige Worker (Anforderungen)|1400|1600|1800|2000|3200|4800|
|Maximal zulässige Sitzungen|30000|30000|30000|30000|30000|30000|
|Anzahl von Replikaten|1|1|1|1|1|1|
|Multi-AZ|–|–|–|–|–|–|
|Horizontale Leseskalierung|–|–|–|–|–|–|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Universelle Dienstebene: Computeplattform der 5. Generation (Teil 1)

|Computegröße|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Hardwaregeneration|5|5|5|5|5|5|5|
|V-Kerne|2|4|6|8|10|12|14|
|Arbeitsspeicher (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|–|–|–|
|Maximale Datengröße (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maximale Protokollgröße (GB)|307|307|307|461|461|461|461|
|tempdb-Größe (GB)|64|128|192|256|320|384|384|
|Speichertyp|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|
|E/A-Wartezeit (ungefähr)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|
|Ziel-IOPS (64 KB)|1000|2000|3000|4000|5\.000|6000|7\.000|
|Grenzwerte für die Protokollrate (MB/s)|3,75|7,5|11,25|15|18,75|22,5|26,25|
|Max. gleichzeitige Worker (Anforderungen)|200|400|600|800|1000|1200|1400|
|Maximal zulässige Sitzungen|30000|30000|30000|30000|30000|30000|30000|
|Anzahl von Replikaten|1|1|1|1|1|1|1|
|Multi-AZ|–|–|–|–|–|–|–|
|Horizontale Leseskalierung|–|–|–|–|–|–|–|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Universelle Dienstebene: Computeplattform der 5. Generation (Teil 2)

|Computegröße|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Hardwaregeneration|5|5|5|5|5|5|5|
|V-Kerne|16|18|20|24|32|40|80|
|Arbeitsspeicher (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|–|–|–|
|Maximale Datengröße (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maximale Protokollgröße (GB)|922|922|922|1229|1229|1229|1229|
|tempdb-Größe (GB)|384|384|384|384|384|384|384|
|Speichertyp|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|
|E/A-Wartezeit (ungefähr)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|
|Ziel-IOPS (64 KB)|7\.000|7\.000|7\.000|7\.000|7\.000|7\.000|7\.000|
|Grenzwerte für die Protokollrate (MB/s)|30|30|30|30|30|30|30|
|Max. gleichzeitige Worker (Anforderungen)|1600|1800|2000|2400|3200|4000|8\.000|
|Maximal zulässige Sitzungen|30000|30000|30000|30000|30000|30000|30000|
|Anzahl von Replikaten|1|1|1|1|1|1|1|
|Multi-AZ|–|–|–|–|–|–|–|
|Horizontale Leseskalierung|–|–|–|–|–|–|–|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

### <a name="serverless-compute-tier"></a>Serverlose Computeebene

Die [serverlose Computeebene](sql-database-serverless.md) ist in der Vorschauversion und gilt nur für einzelne Datenbanken, die das V-Kern-Kaufmodell verwenden.

#### <a name="generation-5-compute-platform"></a>Computeplattform der 5. Generation

|Computegröße|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|
|:--- | --: |--: |--: |
|Hardwaregeneration|5|5|5|
|Min-Max V-Kerne|0,5 - 1|0,5 - 2|0,5 - 4|
|Min-Max-Arbeitsspeicher (GB)|2,02 - 3|2,05 - 6|2,10 - 12|
|Min. Verzögerung für automatische Pause (Stunden)|6|6|6|
|Columnstore-Unterstützung|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|
|Maximale Datengröße (GB)|512|1024|1024|
|Maximale Protokollgröße (GB)|12|24|48|
|tempdb-Größe (GB)|32|64|128|
|Speichertyp|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|
|E/A-Wartezeit (ungefähr)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|
|Ziel-IOPS (64 KB)|500|1000|2000|
|Grenzwerte für die Protokollrate (MB/s)|2.5|5.6|10|
|Max. gleichzeitige Worker (Anforderungen)|75|150|300|
|Maximal zulässige Sitzungen|30000|30000|30000|
|Anzahl von Replikaten|1|1|1|
|Multi-AZ|–|–|–|
|Horizontale Leseskalierung|–|–|–|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

## <a name="business-critical-service-tier-for-provisioned-compute-tier"></a>Dienstebene „Unternehmenskritisch“ für die bereitgestellte Computeebene

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Dienstebene „Unternehmenskritisch“: Computeplattform der 4. Generation (Teil 1)

|Computegröße|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Hardwaregeneration|4|4|4|4|4|4|
|V-Kerne|1|2|3|4|5|6|
|Arbeitsspeicher (GB)|7|14|21|28|35|42|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|1|2|3|4|5|6|
|Speichertyp|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|Maximale Datengröße (GB)|650|650|650|650|650|650|
|Maximale Protokollgröße (GB)|195|195|195|195|195|195|
|tempdb-Größe (GB)|32|64|96|128|160|192|
|E/A-Wartezeit (ungefähr)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|
|Ziel-IOPS (64 KB)|5\.000|10000|15000|20000|25000|30000|
|Grenzwerte für die Protokollrate (MB/s)|8|16|24|32|40|48|
|Max. gleichzeitige Worker (Anforderungen)|200|400|600|800|1000|1200|
|Maximale Anzahl gleichzeitiger Anmeldungen|200|400|600|800|1000|1200|
|Maximal zulässige Sitzungen|30000|30000|30000|30000|30000|30000|
|Anzahl von Replikaten|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Horizontale Leseskalierung|Ja|Ja|Ja|Ja|Ja|Ja|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Dienstebene „Unternehmenskritisch“: Computeplattform der 4. Generation (Teil 2)

|Computegröße|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Hardwaregeneration|4|4|4|4|4|4|
|V-Kerne|7|8|9|10|16|24|
|Arbeitsspeicher (GB)|49|56|63|70|112|168|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|7|8|9,5|11|20|36|
|Speichertyp|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|Maximale Datengröße (GB)|650|650|650|650|1024|1024|
|Maximale Protokollgröße (GB)|195|195|195|195|307|307|
|tempdb-Größe (GB)|224|256|288|320|384|384|
|E/A-Wartezeit (ungefähr)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|
|Ziel-IOPS (64 KB)|35000|40.000|45000|50000|80.000|120000|
|Grenzwerte für die Protokollrate (MB/s)|56|64|64|64|64|64|
|Max. gleichzeitige Worker (Anforderungen)|1400|1600|1800|2000|3200|4800|
|Max. gleichzeitige Anmeldungen (Anforderungen)|1400|1600|1800|2000|3200|4800|
|Maximal zulässige Sitzungen|30000|30000|30000|30000|30000|30000|
|Anzahl von Replikaten|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Horizontale Leseskalierung|Ja|Ja|Ja|Ja|Ja|Ja|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Dienstebene „Unternehmenskritisch“: Computeplattform der 5. Generation (Teil 1)

|Computegröße|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Hardwaregeneration|5|5|5|5|5|5|5|
|V-Kerne|2|4|6|8|10|12|14|
|Arbeitsspeicher (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|1,571|3,142|4,713|6,284|8,655|11,026|13,397|
|Maximale Datengröße (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maximale Protokollgröße (GB)|307|307|307|461|461|922|922|
|tempdb-Größe (GB)|64|128|192|256|320|384|384|
|Speichertyp|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|E/A-Wartezeit (ungefähr)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|
|Ziel-IOPS (64 KB)|8\.000|16000|24.000|32000|40.000|48000|56000|
|Grenzwerte für die Protokollrate (MB/s)|12|24|36|48|60|72|84|
|Max. gleichzeitige Worker (Anforderungen)|200|400|600|800|1000|1200|1400|
|Maximale Anzahl gleichzeitiger Anmeldungen|200|400|600|800|1000|1200|1400|
|Maximal zulässige Sitzungen|30000|30000|30000|30000|30000|30000|30000|
|Anzahl von Replikaten|4|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Horizontale Leseskalierung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Dienstebene „Unternehmenskritisch“: Computeplattform der 5. Generation (Teil 2)

|Computegröße|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Hardwaregeneration|5|5|5|5|5|5|5|
|V-Kerne|16|18|20|24|32|40|80|
|Arbeitsspeicher (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|15,768|18,139|20,51|25,252|37,936|52,22|131,64|
|Maximale Datengröße (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maximale Protokollgröße (GB)|922|922|922|1229|1229|1229|1229|
|tempdb-Größe (GB)|384|384|384|384|384|384|384|
|Speichertyp|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|E/A-Wartezeit (ungefähr)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|
|Ziel-IOPS (64 KB)|64000|72000|80.000|96000|128000|160000|320000|
|Grenzwerte für die Protokollrate (MB/s)|96|96|96|96|96|96|96|
|Max. gleichzeitige Worker (Anforderungen)|1600|1800|2000|2400|3200|4000|8\.000|
|Maximale Anzahl gleichzeitiger Anmeldungen|1600|1800|2000|2400|3200|4000|8\.000|
|Maximal zulässige Sitzungen|30000|30000|30000|30000|30000|30000|30000|
|Anzahl von Replikaten|4|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Horizontale Leseskalierung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

## <a name="hyperscale-service-tier"></a>Hyperscale-Dienstebene

### <a name="generation-5-compute-platform"></a>Computeplattform der 5. Generation

|Leistungsstufe|HS_Gen5_2|HS_Gen5_4|HS_Gen5_8|HS_Gen5_16|HS_Gen5_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Hardwaregeneration|5|5|5|5|5|5|5|5|
|V-Kerne|2|4|8|16|24|32|40|80|
|Arbeitsspeicher (GB)|10.2|20.4|40.8|81.6|122.4|163,2|204|408|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|–|–|–|–|
|Maximale Datengröße (TB)|100 |100 |100 |100 |100 |100 |100 |100 |
|Maximale Protokollgröße (TB)|1 |1 |1 |1 |1 |1 |1 |1 |
|tempdb-Größe (GB)|64|128|256|384|384|384|384|384|
|Speichertyp|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|Ziel-IOPS (64 KB)|Noch festzulegen|Noch festzulegen|Noch festzulegen|Noch festzulegen|Noch festzulegen|Noch festzulegen|Noch festzulegen|Noch festzulegen|
|E/A-Wartezeit (ungefähr)|Noch festzulegen|Noch festzulegen|Noch festzulegen|Noch festzulegen|Noch festzulegen|Noch festzulegen|Noch festzulegen|Noch festzulegen|
|Max. gleichzeitige Worker (Anforderungen)|200|400|800|1600|2400|3200|4000|8\.000|
|Maximal zulässige Sitzungen|30000|30000|30000|30000|30000|30000|30000|30000|
|Anzahl von Replikaten|2|2|2|2|2|2|2|2|
|Multi-AZ|–|–|–|–|–|–|–|–|
|Horizontale Leseskalierung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Enthaltener Sicherungsspeicher |7|7|7|7|7|7|7|7|
|||

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu DTU-Ressourcenlimits bei Singletons finden Sie unter [Ressourcenlimits bei Singletons, die das DTU-basierte Kaufmodell verwenden](sql-database-dtu-resource-limits-single-databases.md).
- Informationen zu V-Kern-Ressourcenlimits bei Pools für elastische Datenbanken finden Sie unter [Ressourcenlimits bei Pools für elastische Datenbanken, die das vCore-basierte Kaufmodell verwenden](sql-database-vcore-resource-limits-elastic-pools.md).
- Informationen zu Ressourcenlimits für Pools für elastische Datenbanken finden Sie unter [Ressourcenlimits für Pools für elastische Datenbanken, die das DTU-basierte Kaufmodell verwenden](sql-database-dtu-resource-limits-elastic-pools.md).
- Informationen zu den Ressourcenlimits für verwaltete Instanzen finden Sie unter [Ressourcenlimits bei verwalteten Instanzen](sql-database-managed-instance-resource-limits.md).
- Informationen zu allgemeinen Azure-Einschränkungen finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md).
- Informationen zu Ressourcenlimits auf Server- und Abonnementebene auf einem Datenbankserver finden Sie unter [Übersicht über Ressourcenlimits für einen SQL-Datenbank-Server](sql-database-resource-limits-database-server.md).
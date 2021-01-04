---
title: Serverparameter – Azure Database for MariaDB
description: Dieser Artikel enthält Richtlinien für die Konfiguration von Serverparametern in Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/25/2020
ms.openlocfilehash: 4432178d5908d4360cda05a62b62d05687be4235
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541129"
---
# <a name="server-parameters-in-azure-database-for-mariadb"></a>Serverparameter in Azure Database for MariaDB

Dieser Artikel enthält Überlegungen und Richtlinien zur Konfiguration von Serverparametern in Azure Database for MariaDB.

## <a name="what-are-server-parameters"></a>Was sind Serverparameter? 

Die MariaDB-Engine stellt eine Vielzahl verschiedener Servervariablen/-parameter zur Konfiguration und Optimierung des Engine-Verhaltens bereit. Einige der Parameter können dynamisch während der Laufzeit festgelegt werden, während andere „statisch“ sind und ihre Anwendung einen Neustart des Servers erfordert.

Azure Database for MariaDB bietet die Möglichkeit, den Wert verschiedener MariaDB-Serverparameter mithilfe des [Azure-Portals](./howto-server-parameters.md), der [Azure CLI](./howto-configure-server-parameters-cli.md) und von [PowerShell](./howto-configure-server-parameters-using-powershell.md) zu ändern, um sie den Anforderungen Ihrer Workload anzupassen.

## <a name="configurable-server-parameters"></a>Konfigurierbare Serverparameter

Die Liste der unterstützten Serverparameter wächst ständig. Sie können die vollständige Liste im Azure-Portal auf der Registerkarte „Serverparameter“ anzeigen und die Werte der Serverparameter dort konfigurieren.

In den folgenden Abschnitten erfahren Sie mehr über die Grenzen der verschiedenen häufig aktualisierten Serverparameter. Die Grenzwerte werden durch den Tarif und die Anzahl der virtuellen Kerne des Servers bestimmt.

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

In Azure Database for MariaDB sind binäre Protokolle immer aktiviert (d. h., `log_bin` ist auf ON festgelegt). Wenn Sie versuchen, Trigger zu verwenden, erhalten Sie einen Fehler wie *Sie verfügen nicht über die SUPER-Berechtigung, und die binäre Protokollierung ist aktiviert (es kann ratsam sein, die weniger sichere Variable `log_bin_trust_function_creators` zu verwenden)* .

Das Format für binäre Protokollierung ist immer **ROW** (Zeile), und für alle Verbindungen mit dem Server wird **IMMER** die zeilenbasierte binäre Protokollierung verwendet. Bei der zeilenbasierten binären Protokollierung gibt es keine Sicherheitsprobleme, und die binäre Protokollierung kann nicht unterbrochen werden, sodass Sie [`log_bin_trust_function_creators`](https://mariadb.com/docs/reference/mdb/system-variables/log_bin_trust_function_creators/) sicher auf **TRUE** festlegen können.

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

Weitere Informationen zu diesem Parameter finden Sie in der [MariaDB-Dokumentation](https://mariadb.com/kb/en/innodb-system-variables/#innodb_buffer_pool_size).

#### <a name="servers-supporting-up-to-4-tb-storage"></a>Server, die bis zu 4 TB Speicher unterstützen

|**Tarif**|**vCore(s)**|**Standardwert (Bytes)**|**Mindestwert (Bytes)**|**Höchstwert (Bytes)**|
|---|---|---|---|---|
|Basic|1|872415232|134217728|872415232|
|Basic|2|2684354560|134217728|2684354560|
|Universell|2|3758096384|134217728|3758096384|
|Universell|4|8053063680|134217728|8053063680|
|Universell|8|16106127360|134217728|16106127360|
|Universell|16|32749125632|134217728|32749125632|
|Universell|32|66035122176|134217728|66035122176|
|Universell|64|132070244352|134217728|132070244352|
|Arbeitsspeicheroptimiert|2|7516192768|134217728|7516192768|
|Arbeitsspeicheroptimiert|4|16106127360|134217728|16106127360|
|Arbeitsspeicheroptimiert|8|32212254720|134217728|32212254720|
|Arbeitsspeicheroptimiert|16|65498251264|134217728|65498251264|
|Arbeitsspeicheroptimiert|32|132070244352|134217728|132070244352|

#### <a name="servers-support-up-to-16-tb-storage"></a>Server, die bis zu 16 TB Speicher unterstützen

|**Tarif**|**vCore(s)**|**Standardwert (Bytes)**|**Mindestwert (Bytes)**|**Höchstwert (Bytes)**|
|---|---|---|---|---|
|Basic|1|872415232|134217728|872415232|
|Basic|2|2684354560|134217728|2684354560|
|Universell|2|7516192768|134217728|7516192768|
|Universell|4|16106127360|134217728|16106127360|
|Universell|8|32212254720|134217728|32212254720|
|Universell|16|65498251264|134217728|65498251264|
|Universell|32|132070244352|134217728|132070244352|
|Universell|64|264140488704|134217728|264140488704|
|Arbeitsspeicheroptimiert|2|15032385536|134217728|15032385536|
|Arbeitsspeicheroptimiert|4|32212254720|134217728|32212254720|
|Arbeitsspeicheroptimiert|8|64424509440|134217728|64424509440|
|Arbeitsspeicheroptimiert|16|130996502528|134217728|130996502528|
|Arbeitsspeicheroptimiert|32|264140488704|134217728|264140488704|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

> [!NOTE]
> `innodb_file_per_table` kann nur in den Tarifen „Universell“ und „Arbeitsspeicheroptimiert“ aktualisiert werden.

MariaDB speichert die InnoDB-Tabelle in verschiedenen Tabellenbereichen basierend auf der Konfiguration, die Sie während der Tabellenerstellung angegeben haben. Der [Systemtabellenbereich](https://mariadb.com/kb/en/innodb-system-tablespaces/) ist der Speicherbereich für das InnoDB-Datenwörterbuch. Ein [file-per-table-Tabellenbereich](https://mariadb.com/kb/en/innodb-file-per-table-tablespaces/) enthält die Daten und Indizes für eine einzelne InnoDB-Tabelle und wird im Dateisystem in einer eigenen Datendatei gespeichert. Dieses Verhalten wird vom `innodb_file_per_table`-Serverparameter gesteuert. Durch Festlegen von `innodb_file_per_table` auf `OFF` werden Tabellen von InnoDB im Systemtabellenbereich erstellt. Andernfalls werden die Tabellen von InnoDB im „file-per-table“-Tabellenbereich erstellt.

Azure Database for MariaDB unterstützt bis zu **ein Terabyte** in einer einzelnen Datendatei. Wenn die Datenbankgröße ein Terabyte überschreitet, sollten Sie die Tabelle im [innodb_file_per_table](https://mariadb.com/kb/en/innodb-system-variables/#innodb_file_per_table)-Tabellenbereich erstellen. Wenn eine einzelne Tabelle größer als ein Terabyte ist, sollten Sie die Partitionstabelle verwenden.

### <a name="join_buffer_size"></a>join_buffer_size

Weitere Informationen zu diesem Parameter finden Sie in der [MariaDB-Dokumentation](https://mariadb.com/kb/en/server-system-variables/#join_buffer_size).

|**Tarif**|**vCore(s)**|**Standardwert (Bytes)**|**Mindestwert (Bytes)**|**Höchstwert (Bytes)**|
|---|---|---|---|---|
|Basic|1|Nicht konfigurierbar im Basic-Tarif|–|–|
|Basic|2|Nicht konfigurierbar im Basic-Tarif|–|–|
|Universell|2|262144|128|268435455|
|Universell|4|262144|128|536870912|
|Universell|8|262144|128|1073741824|
|Universell|16|262144|128|2147483648|
|Universell|32|262144|128|4294967295|
|Universell|64|262144|128|4294967295|
|Arbeitsspeicheroptimiert|2|262144|128|536870912|
|Arbeitsspeicheroptimiert|4|262144|128|1073741824|
|Arbeitsspeicheroptimiert|8|262144|128|2147483648|
|Arbeitsspeicheroptimiert|16|262144|128|4294967295|
|Arbeitsspeicheroptimiert|32|262144|128|4294967295|

### <a name="max_connections"></a>max_connections

|**Tarif**|**vCore(s)**|**Standardwert**|**Mindestwert**|**Höchstwert**|
|---|---|---|---|---|
|Basic|1|50|10|50|
|Basic|2|100|10|100|
|Universell|2|300|10|600|
|Universell|4|625|10|1250|
|Universell|8|1250|10|2500|
|Universell|16|2500|10|5.000|
|Universell|32|5.000|10|10000|
|Universell|64|10000|10|20000|
|Arbeitsspeicheroptimiert|2|625|10|1250|
|Arbeitsspeicheroptimiert|4|1250|10|2500|
|Arbeitsspeicheroptimiert|8|2500|10|5.000|
|Arbeitsspeicheroptimiert|16|5.000|10|10000|
|Arbeitsspeicheroptimiert|32|10000|10|20000|

Wenn Verbindungen den Grenzwert übersteigen, erhalten Sie möglicherweise den folgenden Fehler:
> FEHLER 1040 (08004): Zu viele Verbindungen

> [!IMPORTANT]
> Für eine optimale Erfahrung empfehlen wir, dass Sie einen Verbindungspooler wie ProxySQL verwenden, um Verbindungen effizient zu verwalten.

Das Erstellen neuer Clientverbindungen mit MariaDB nimmt Zeit in Anspruch, und nach der Herstellung belegen diese Verbindungen Datenbankressourcen, auch wenn Sie sich im Leerlauf befinden. Die meisten Anwendungen fordern viele kurzlebige Verbindungen an, was diese Situation erschwert. Das Ergebnis sind weniger Ressourcen, die für ihre tatsächliche Workload verfügbar sind, was zu verringerter Leistung führt. Ein Verbindungspooler, der Verbindungen im Leerlauf reduziert und vorhandene Verbindungen wiederverwendet, hilft dabei, dies zu vermeiden. Weitere Informationen zum Einrichten von ProxySQL finden Sie in unserem [Blogbeitrag](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

>[!Note]
>ProxySQL ist ein Open Source-Communitytool. Es wird von Microsoft bestmöglich unterstützt. Wenden Sie sich an den [ProxySQL-Produktsupport](https://proxysql.com/services/support/), wenn Sie Produktionssupport mit autoritativen Hinweisen benötigen.

### <a name="max_heap_table_size"></a>max_heap_table_size

Weitere Informationen zu diesem Parameter finden Sie in der [MariaDB-Dokumentation](https://mariadb.com/kb/en/server-system-variables/#max_heap_table_size).

|**Tarif**|**vCore(s)**|**Standardwert (Bytes)**|**Mindestwert (Bytes)**|**Höchstwert (Bytes)**|
|---|---|---|---|---|
|Basic|1|Nicht konfigurierbar im Basic-Tarif|–|–|
|Basic|2|Nicht konfigurierbar im Basic-Tarif|–|–|
|Universell|2|16777216|16384|268435455|
|Universell|4|16777216|16384|536870912|
|Universell|8|16777216|16384|1073741824|
|Universell|16|16777216|16384|2147483648|
|Universell|32|16777216|16384|4294967295|
|Universell|64|16777216|16384|4294967295|
|Arbeitsspeicheroptimiert|2|16777216|16384|536870912|
|Arbeitsspeicheroptimiert|4|16777216|16384|1073741824|
|Arbeitsspeicheroptimiert|8|16777216|16384|2147483648|
|Arbeitsspeicheroptimiert|16|16777216|16384|4294967295|
|Arbeitsspeicheroptimiert|32|16777216|16384|4294967295|

### <a name="query_cache_size"></a>query_cache_size

Der Abfragecache ist in MariaDB standardmäßig mit dem Parameter `have_query_cache` aktiviert. 

Weitere Informationen zu diesem Parameter finden Sie in der [MariaDB-Dokumentation](https://mariadb.com/kb/en/server-system-variables/#query_cache_size).

|**Tarif**|**vCore(s)**|**Standardwert (Bytes)**|**Mindestwert (Bytes)**|**Höchstwert **|
|---|---|---|---|---|
|Basic|1|Nicht konfigurierbar im Basic-Tarif|–|–|
|Basic|2|Nicht konfigurierbar im Basic-Tarif|–|–|
|Universell|2|0|0|16777216|
|Universell|4|0|0|33554432|
|Universell|8|0|0|67108864|
|Universell|16|0|0|134217728|
|Universell|32|0|0|134217728|
|Universell|64|0|0|134217728|
|Arbeitsspeicheroptimiert|2|0|0|33554432|
|Arbeitsspeicheroptimiert|4|0|0|67108864|
|Arbeitsspeicheroptimiert|8|0|0|134217728|
|Arbeitsspeicheroptimiert|16|0|0|134217728|
|Arbeitsspeicheroptimiert|32|0|0|134217728|

### <a name="sort_buffer_size"></a>sort_buffer_size

Weitere Informationen zu diesem Parameter finden Sie in der [MariaDB-Dokumentation](https://mariadb.com/kb/en/server-system-variables/#sort_buffer_size).

|**Tarif**|**vCore(s)**|**Standardwert (Bytes)**|**Mindestwert (Bytes)**|**Höchstwert (Bytes)**|
|---|---|---|---|---|
|Basic|1|Nicht konfigurierbar im Basic-Tarif|–|–|
|Basic|2|Nicht konfigurierbar im Basic-Tarif|–|–|
|Universell|2|524288|32768|4194304|
|Universell|4|524288|32768|8388608|
|Universell|8|524288|32768|16777216|
|Universell|16|524288|32768|33554432|
|Universell|32|524288|32768|33554432|
|Universell|64|524288|32768|33554432|
|Arbeitsspeicheroptimiert|2|524288|32768|8388608|
|Arbeitsspeicheroptimiert|4|524288|32768|16777216|
|Arbeitsspeicheroptimiert|8|524288|32768|33554432|
|Arbeitsspeicheroptimiert|16|524288|32768|33554432|
|Arbeitsspeicheroptimiert|32|524288|32768|33554432|

### <a name="tmp_table_size"></a>tmp_table_size

Weitere Informationen zu diesem Parameter finden Sie in der [MariaDB-Dokumentation](https://mariadb.com/kb/en/server-system-variables/#tmp_table_size).

|**Tarif**|**vCore(s)**|**Standardwert (Bytes)**|**Mindestwert (Bytes)**|**Höchstwert (Bytes)**|
|---|---|---|---|---|
|Basic|1|Nicht konfigurierbar im Basic-Tarif|–|–|
|Basic|2|Nicht konfigurierbar im Basic-Tarif|–|–|
|Universell|2|16777216|1024|67108864|
|Universell|4|16777216|1024|134217728|
|Universell|8|16777216|1024|268435456|
|Universell|16|16777216|1024|536870912|
|Universell|32|16777216|1024|1073741824|
|Universell|64|16777216|1024|1073741824|
|Arbeitsspeicheroptimiert|2|16777216|1024|134217728|
|Arbeitsspeicheroptimiert|4|16777216|1024|268435456|
|Arbeitsspeicheroptimiert|8|16777216|1024|536870912|
|Arbeitsspeicheroptimiert|16|16777216|1024|1073741824|
|Arbeitsspeicheroptimiert|32|16777216|1024|1073741824|

### <a name="time_zone"></a>time_zone

Bei der ersten Bereitstellung enthält ein Server für Azure for MariaDB Systemtabellen für Zeitzoneninformationen, die jedoch nicht aufgefüllt sind. Die Zeitzonentabellen können durch Aufrufen der gespeicherten Prozedur `mysql.az_load_timezone` über ein Tool wie die MySQL-Befehlszeile oder MySQL Workbench aufgefüllt werden. Informationen zum Aufrufen der gespeicherten Prozedur und zum Festlegen der globalen Zeitzonen oder Zeitzonen auf Sitzungsebene finden Sie in den Artikeln für das [Azure-Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) und die [Azure CLI](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter).

## <a name="non-configurable-server-parameters"></a>Nicht konfigurierbare Serverparameter

Die folgenden Serverparameter können im Dienst nicht konfiguriert werden:

|**Parameter**|**Fester Wert**|
| :------------------------ | :-------- |
|innodb_file_per_table (im Tarif „Basic“)|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256 MB|
|innodb_log_files_in_group|2|

Weitere Serverparameter, die hier nicht aufgeführt sind, werden auf die vordefinierten Standardwerte für [MariaDB](https://mariadb.com/kb/en/server-system-variables/) festgelegt.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum [Konfigurieren von Serverparametern mithilfe des Azure-Portals](./howto-server-parameters.md)
- Informationen zum [Konfigurieren von Serverparametern mithilfe der Azure CLI](./howto-configure-server-parameters-cli.md)
- Informationen zum [Konfigurieren von Serverparametern mithilfe von PowerShell](./howto-configure-server-parameters-using-powershell.md)
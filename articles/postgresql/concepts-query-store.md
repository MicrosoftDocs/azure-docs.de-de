---
title: Abfragespeicher in Azure Database for PostgreSQL (Einzelserver)
description: In diesem Artikel wird das Abfragespeicherfeature in Azure Database for PostgreSQL (Einzelserver) beschrieben.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: b622de3e21d26676bb11d81a6facf8fea18cabc1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65067199"
---
# <a name="monitor-performance-with-the-query-store"></a>Überwachen der Leistung mit dem Abfragespeicher

**Anwendungsbereich:** Azure Database for PostgreSQL (Einzelserver) 9.6 und 10

Das Abfragespeicherfeature in Azure Database for PostgreSQL bietet eine Möglichkeit, um die Abfrageleistung im Zeitverlauf nachzuverfolgen. Der Abfragespeicher vereinfacht das Beheben von Leistungsproblemen, da er es Ihnen ermöglicht, die am längsten ausgeführten und ressourcenintensivsten Abfragen schnell zu ermitteln. Der Abfragespeicher erfasst automatisch einen Verlauf der Abfragen und Laufzeitstatistiken und bewahrt diese auf, damit Sie sie überprüfen können. Er unterteilt die Daten nach Zeitfenstern, damit Sie Verwendungsmuster für Datenbanken erkennen können. Die Daten für alle Benutzer, Datenbanken und Abfragen werden in einer Datenbank namens **azure_sys** in der Azure Database for PostgreSQL-Instanz gespeichert.

> [!IMPORTANT]
> Nehmen Sie an der **azure_sys**-Datenbank oder ihren Schemas keine Änderungen vor. Andernfalls funktionieren der Abfragespeicher und die entsprechenden Leistungsfeatures nicht mehr ordnungsgemäß.

## <a name="enabling-query-store"></a>Aktivieren des Abfragespeichers
Der Abfragespeicher ist ein optionales Feature. Daher ist er auf einem Server nicht standardmäßig aktiviert. Der Speicher wird global für alle Datenbanken auf einem bestimmten Server aktiviert oder deaktiviert. Ein Aktivieren oder Deaktivieren für einzelne Datenbanken ist nicht möglich.

### <a name="enable-query-store-using-the-azure-portal"></a>Aktivieren des Abfragespeichers über das Azure-Portal
1. Melden Sie sich beim Azure-Portal an, und wählen Sie Ihren Azure Database for PostgreSQL-Server aus.
2. Wählen Sie im Bereich **Einstellungen** im Menü die Option **Serverparameter**.
3. Suchen Sie nach dem Parameter `pg_qs.query_capture_mode`.
4. Legen Sie für den Wert `TOP` fest und **Speichern**.

So aktivieren Sie Wartestatistiken in Ihrem Abfragespeicher: 
1. Suchen Sie nach dem Parameter `pgms_wait_sampling.query_capture_mode`.
1. Legen Sie für den Wert `ALL` fest und **Speichern**.


Alternativ können Sie diese Parameter über die Azure-Befehlszeilenschnittstelle festlegen.
```azurecli-interactive
az postgres server configuration set --name pg_qs.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value TOP
az postgres server configuration set --name pgms_wait_sampling.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value ALL
```

Es kann bis zu 20 Minuten dauern, bis der erste Datenbatch in der azure_sys-Datenbank gespeichert ist.

## <a name="information-in-query-store"></a>Informationen im Abfragespeicher
Der Abfragespeicher verfügt über zwei Speicher:
- Ein Speicher für Laufzeitstatistiken zum Aufbewahren der Informationen aus den Abfragespeicherstatistiken.
- Ein Speicher für Wartestatistiken zum Aufbewahren der Informationen aus den Wartestatistiken.

Häufige Szenarien für die Verwendung des Abfragespeichers sind u.a.:
- Bestimmen der Häufigkeit zum Ausführen einer Abfrage in einem angegebenen Zeitfenster
- Vergleichen der durchschnittlichen Ausführungsdauer einer Abfrage für bestimmte Zeitfenster zum Anzeigen großer Abweichungen
- Identifizieren der am längsten ausgeführten Abfragen in der vergangenen X Stunden
- Identifizieren der wichtigsten N Abfragen, die auf Ressourcen warten
- Erhalten von Wartedetails einer bestimmten Abfrage

Um die Speicherverwendung zu minimieren, werden die Laufzeit-Ausführungsstatistiken im Speicher für Laufzeitstatistiken für ein festes, konfigurierbares Zeitfenster zusammengefasst. Die Informationen in diesem Speicher können durch Abfragen der Abfragespeicheransichten angezeigt werden.

Die folgende Abfrage gibt Informationen über Abfragen im Abfragespeicher zurück:
```sql
SELECT * FROM query_store.qs_view; 
``` 

Diese Abfrage gibt Informationen über Wartestatistiken zurück:
```sql
SELECT * FROM query_store.pgms_wait_sampling_view;
```

## <a name="finding-wait-queries"></a>Suchen von Warteanfragen
Warteereignistypen kombinieren verschiedene Warteereignisse nach Ähnlichkeit in Buckets. Der Abfragespeicher enthält den Warteereignistyp, den spezifischen Warteereignisnamen und die entsprechende Abfrage. Die Möglichkeit zum Korrelieren dieser Warteinformationen mit den Abfragelaufzeitstatistiken bedeutet, dass Sie einen ausführlicheren Überblick darüber erhalten, welche Faktoren sich auf die Abfrageleistung auswirken.

Im Folgenden finden Sie einige Beispiele dafür, wie Sie mithilfe der Wartestatistiken im Abfragespeicher weitere Erkenntnisse zur Ihrer Workload erhalten:

| **Beobachtung** | **Aktion** |
|---|---|
|Lange Sperrwartevorgänge | Überprüfen Sie die Abfragetexte der betroffenen Abfragen, und identifizieren Sie die Zielentitäten. Suchen Sie im Abfragespeicher nach anderen Abfragen, die die gleiche Entität ändern, welche häufig ausgeführt wird bzw. eine lange Dauer aufweist. Nachdem Sie diese Abfragen ermittelt haben, ändern Sie ggf. die Anwendungslogik, um die Parallelität zu verbessern, oder verwenden Sie eine weniger restriktive Isolationsstufe.|
| Lange Puffer-E/A-Wartevorgänge | Suchen Sie die Abfragen mit einer hohen Anzahl an physischen Lesevorgängen im Abfragespeicher. Wenn diese mit Abfragen mit langen E/A-Wartevorgängen übereinstimmen, führen Sie ggf. einen Index für die zugrunde liegenden Entität ein, um Such- anstelle von Scanvorgängen durchzuführen. Dies verringert den E/A-Aufwand der Abfragen. Überprüfen Sie die **Leistungsempfehlungen** für Ihren Server im Portal, um festzustellen, ob Indexempfehlungen für diesen Server vorhanden sind, die die Abfragen optimieren.|
| Lange Arbeitsspeicher-Wartevorgänge | Suchen Sie die im Abfragespeicher die speicherintensivsten Abfragen. Diese Abfragen verzögern wahrscheinlich zusätzlich den Fortschritt der betroffen Abfragen. Überprüfen Sie die **Leistungsempfehlungen** für Ihren Server im Portal, um festzustellen, ob Indexempfehlungen vorhanden sind, die diese Abfragen optimieren.|

## <a name="configuration-options"></a>Konfigurationsoptionen
Wenn der Abfragespeicher aktiviert ist, speichert er Daten in Aggregationsfenstern von 15 Minuten mit bis zu 500 unterschiedlichen Abfragen pro Fenster. 

Die folgenden Optionen stehen für die Konfiguration der Abfragespeicherparameter zur Verfügung.

| **Parameter** | **Beschreibung** | **Standard** | **Bereich**|
|---|---|---|---|
| pg_qs.query_capture_mode | Legt fest, welche Anweisungen nachverfolgt werden. | none | none, top, all |
| pg_qs.max_query_text_length | Legt die maximale Abfragelänge fest, die gespeichert werden kann. Längere Abfragen werden abgeschnitten. | 6000 | 100 – 10.000 |
| pg_qs.retention_period_in_days | Legt den Aufbewahrungszeitraum fest. | 7 | 1 – 30 |
| pg_qs.track_utility | Legt fest, ob Dienstprogrammbefehle nachverfolgt werden. | on | on, off |

Die folgenden Optionen gelten speziell für Wartestatistiken.

| **Parameter** | **Beschreibung** | **Standard** | **Bereich**|
|---|---|---|---|
| pgms_wait_sampling.query_capture_mode | Legt fest, welche Anweisungen in Bezug auf Wartestatistiken nachverfolgt werden. | none | none, all|
| Pgms_wait_sampling.history_period | Legt die Häufigkeit in Millisekunden fest, mit der Stichproben von Wartezeitereignissen erfasst werden. | 100 | 1 – 600000 |

> [!NOTE] 
> **pg_qs.query_capture_mode** ersetzt **pgms_wait_sampling.query_capture_mode**. Wenn für pg_qs.query_capture_mode NONE festgelegt ist, hat die Einstellung pgms_wait_sampling.query_capture_mode keine Auswirkungen.


Verwenden Sie das [Azure-Portal](howto-configure-server-parameters-using-portal.md) oder die [Azure-Befehlszeilenschnittstelle](howto-configure-server-parameters-using-cli.md) zum Abrufen oder Festlegen eines anderen Werts für einen Parameter.

## <a name="views-and-functions"></a>Ansichten und Funktionen
Mithilfe der folgenden Ansichten und Funktionen können Sie den Abfragespeicher anzeigen und verwalten. In der öffentlichen PostgreSQL-Rolle kann jeder diese Ansichten verwenden, um die Daten im Abfragespeicher anzuzeigen. Diese Ansichten sind nur in der **azure_sys**-Datenbank verfügbar.

Abfragen werden normalisiert, indem ihre Struktur nach dem Entfernen von Literalen und Konstanten untersucht wird. Wenn zwei Abfragen mit Ausnahme von Literalwerten identisch sind, haben sie denselben Hash.

### <a name="querystoreqsview"></a>query_store.qs_view
In dieser Ansicht werden alle Daten im Abfragespeicher zurückgegeben. Es gibt eine Zeile für jede einzelne Datenbank-ID, Benutzer-ID und Abfrage-ID. 

|**Name**   |**Typ** | **Referenzen**  | **Beschreibung**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | Die ID aus der Tabelle runtime_stats_entries|
|user_id    |oid    |pg_authid.oid  |Die OID des Benutzers, der die Anweisung ausgeführt hat|
|db_id  |oid    |pg_database.oid    |Die OID der Datenbank, in der die Anweisung ausgeführt wurde|
|query_id   |bigint  || Interner Hash, der von der Analysestruktur der Anweisung berechnet wurde|
|query_sql_text |Varchar(10000)  || Der Text einer repräsentativen Anweisung. Unterschiedliche Abfragen mit der gleichen Struktur werden gruppiert; dieser Text ist der Text für die erste Abfrage im Cluster.|
|plan_id    |bigint |   |ID des Plans, der dieser Abfrage entspricht, noch nicht verfügbar|
|start_time |timestamp  ||  Abfragen werden nach Zeitrahmen zusammengefasst. Die Zeitspanne eines Zeitrahmens beträgt standardmäßig 15 Minuten. Dies ist die Startzeit des Zeitrahmens für diesen Eintrag.|
|end_time   |timestamp  ||  Dies ist die Endzeit des Zeitrahmens für diesen Eintrag.|
|calls  |bigint  || Häufigkeit der Abfrageausführung|
|total_time |double precision   ||  Gesamte Abfrageausführungsdauer in Millisekunden|
|min_time   |double precision   ||  Mindestwert der Abfrageausführungsdauer in Millisekunden|
|max_time   |double precision   ||  Höchstwert der Abfrageausführungsdauer in Millisekunden|
|mean_time  |double precision   ||  Durchschnittliche Abfrageausführungsdauer in Millisekunden|
|stddev_time|   double precision    ||  Standardabweichung der Abfrageausführungsdauer in Millisekunden |
|rows   |bigint ||  Gesamtanzahl der Zeilen, die abgerufen wurden oder von der Anweisung betroffen sind|
|shared_blks_hit|   bigint  ||  Gesamtanzahl der freigegebenen Blockcachetreffer der Anweisung|
|shared_blks_read|  bigint  ||  Gesamtanzahl der freigegebenen Blöcke, die von der Anweisung gelesen wurden|
|shared_blks_dirtied|   bigint   || Gesamtanzahl der freigegebenen Blöcke, die von der Anweisung geändert wurden |
|shared_blks_written|   bigint  ||  Gesamtanzahl der freigegebenen Blöcke, die von der Anweisung geschrieben wurden|
|local_blks_hit|    bigint ||   Gesamtanzahl der lokalen Blockcachetreffer der Anweisung|
|local_blks_read|   bigint   || Gesamtanzahl der lokalen Blöcke, die von der Anweisung gelesen wurden|
|local_blks_dirtied|    bigint  ||  Gesamtanzahl der lokalen Blöcke, die von der Anweisung geändert wurden|
|local_blks_written|    bigint  ||  Gesamtanzahl der lokalen Blöcke, die von der Anweisung geschrieben wurden|
|temp_blks_read |bigint  || Gesamtanzahl der temporären Blöcke, die von der Anweisung gelesen wurden|
|temp_blks_written| bigint   || Gesamtanzahl der temporären Blöcke, die von der Anweisung geschrieben wurden|
|blk_read_time  |double precision    || Gesamtzeit in Millisekunden, die die Anweisung zum Lesen von Blöcken benötigt hat (wenn track_io_timing aktiviert ist, andernfalls Null)|
|blk_write_time |double precision    || Gesamtzeit in Millisekunden, die die Anweisung zum Schreiben von Blöcken benötigt hat (wenn track_io_timing aktiviert ist, andernfalls Null)|
    
### <a name="querystorequerytextsview"></a>query_store.query_texts_view
Diese Ansicht gibt alle Abfragetextdaten im Abfragespeicher zurück. Für jeden einzelnen query_text gibt es eine Zeile.

|**Name**|  **Typ**|   **Beschreibung**|
|---|---|---|
|query_text_id  |bigint     |ID der Tabelle query_texts|
|query_sql_text |Varchar(10000)     |Der Text einer repräsentativen Anweisung. Unterschiedliche Abfragen mit der gleichen Struktur werden gruppiert; dieser Text ist der Text für die erste Abfrage im Cluster.|

### <a name="querystorepgmswaitsamplingview"></a>query_store.pgms_wait_sampling_view
Diese Ansicht gibt Warteereignisdaten im Abfragespeicher zurück. Es gibt eine Zeile für jede einzelne Datenbank-ID, Benutzer-ID und jedes Ereignis.

|**Name**|  **Typ**|   **Referenzen**| **Beschreibung**|
|---|---|---|---|
|user_id    |oid    |pg_authid.oid  |Die OID des Benutzers, der die Anweisung ausgeführt hat|
|db_id  |oid    |pg_database.oid    |Die OID der Datenbank, in der die Anweisung ausgeführt wurde|
|query_id   |bigint     ||Interner Hash, der von der Analysestruktur der Anweisung berechnet wurde|
|event_type |text       ||Der Typ des Ereignisses, auf das das Back-End wartet|
|event  |text       ||Der Warteereignisname, wenn das Back-End derzeit wartet|
|calls  |Integer        ||Nummer des gleichen erfassten Ereignisses|


### <a name="functions"></a>Functions
Query_store.qs_reset() gibt „void“ zurück

`qs_reset`  verwirft alle Statistiken, die bisher vom Abfragespeicher gesammelt wurden. Diese Funktion kann nur von der Serveradministratorrolle ausgeführt werden.

Query_store.staging_data_reset() gibt „void“ zurück

`staging_data_reset`  verwirft alle Statistiken, die vom Abfragespeicher im Arbeitsspeicher erfasst wurden (d. h. die Daten im Arbeitsspeicher, für die noch kein Flushvorgang in die Datenbank durchgeführt wurde). Diese Funktion kann nur von der Serveradministratorrolle ausgeführt werden.

## <a name="limitations-and-known-issues"></a>Einschränkungen und bekannte Probleme
- Wenn ein PostgreSQL-Server über den Parameter „default_transaction_read_only“ verfügt, kann der Abfragespeicher keine Daten erfassen.
- Die Abfragespeicherfunktionalität kann unterbrochen werden, wenn lange Unicodeabfragen (mindestens 6000 Bytes) festgestellt werden.


## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über [Szenarien, in denen der Abfragespeicher hilfreich ist](concepts-query-store-scenarios.md).
- Erfahren Sie mehr über die [bewährten Methoden für die Verwendung des Abfragespeichers](concepts-query-store-best-practices.md).

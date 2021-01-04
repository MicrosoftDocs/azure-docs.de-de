---
title: 'Tutorial: Entwerfen eines Echtzeitdashboards – Hyperscale (Citus) – Azure Database for PostgreSQL'
description: In diesem Tutorial erfahren Sie, wie Sie Echtzeitdashboard-Abfragen mit Azure Database for PostgreSQL – Hyperscale (Citus) parallelisieren.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 480af87519f8e11c14f009058b0f518bdfc34a1b
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026266"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Tutorial: Entwerfen eines Dashboards für die Echtzeitanalyse mithilfe von Azure Database for PostgreSQL – Hyperscale (Citus)

In diesem Tutorial verwenden Sie Azure Database for PostgreSQL – Hyperscale (Citus), um Folgendes zu lernen:

> [!div class="checklist"]
> * Erstellen einer Hyperscale (Citus)-Servergruppe
> * Verwenden des psql-Hilfsprogramms zum Erstellen eines Schemas
> * Knotenübergreifende Shard-Tabellen
> * Generieren von Beispieldaten
> * Ausführen von Rollups
> * Abfragen von Rohdaten und aggregierten Daten
> * Ablauf von Daten

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Verwenden des psql-Hilfsprogramms zum Erstellen eines Schemas

Nach dem Herstellen der Verbindung mit Azure Database for PostgreSQL – Hyperscale (Citus) mithilfe von psql können Sie ein paar einfache Aufgaben ausführen. Dieses Tutorial führt Sie schrittweise durch das Erfassen von Verkehrsdaten aus Webanalysen und den anschließenden Rollup der Daten, um Echtzeitdashboards auf der Grundlage dieser Daten bereitzustellen.

Erstellen wir eine Tabelle, die unsere gesamten Webdatenverkehrs-Rohdaten nutzt. Führen Sie die folgenden Befehle im psql-Terminal aus:

```sql
CREATE TABLE http_request (
  site_id INT,
  ingest_time TIMESTAMPTZ DEFAULT now(),

  url TEXT,
  request_country TEXT,
  ip_address TEXT,

  status_code INT,
  response_time_msec INT
);
```

Wir erstellen darüber hinaus eine Tabelle, die unsere minütlichen Aggregationen aufnimmt und eine Tabelle, die die Position unseres letzten Rollups verwaltet. Führen Sie die folgenden Befehle ebenfalls in psql aus:

```sql
CREATE TABLE http_request_1min (
  site_id INT,
  ingest_time TIMESTAMPTZ, -- which minute this row represents

  error_count INT,
  success_count INT,
  request_count INT,
  average_response_time_msec INT,
  CHECK (request_count = error_count + success_count),
  CHECK (ingest_time = date_trunc('minute', ingest_time))
);

CREATE INDEX http_request_1min_idx ON http_request_1min (site_id, ingest_time);

CREATE TABLE latest_rollup (
  minute timestamptz PRIMARY KEY,

  CHECK (minute = date_trunc('minute', minute))
);
```

Sie können jetzt die neu erstellten Tabellen in der Liste der Tabellen mit diesem psql-Befehl anzeigen:

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>Knotenübergreifende Shard-Tabellen

Eine Hyperscalebereitstellung speichert Tabellenzeilen basierend auf dem Wert einer vom Benutzer zugeordneten Spalte auf verschiedenen Knoten. Die „Verteilungsspalte“ gibt an, wie die Daten auf die Knoten verteilt sind.

Legen wir die Verteilungsspalte auf „site\_id“ fest, den Shardschlüssel. Führen Sie in psql diese Funktionen aus:

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../includes/azure-postgresql-hyperscale-dist-alert.md)]

## <a name="generate-sample-data"></a>Generieren von Beispieldaten

Jetzt sollte unsere Servergruppe für die Erfassung einiger Daten bereit sein. Wir können den folgenden Code lokal aus unserer `psql`-Verbindung ausführen, um fortlaufend Daten einzufügen.

```sql
DO $$
  BEGIN LOOP
    INSERT INTO http_request (
      site_id, ingest_time, url, request_country,
      ip_address, status_code, response_time_msec
    ) VALUES (
      trunc(random()*32), clock_timestamp(),
      concat('http://example.com/', md5(random()::text)),
      ('{China,India,USA,Indonesia}'::text[])[ceil(random()*4)],
      concat(
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2)
      )::inet,
      ('{200,404}'::int[])[ceil(random()*2)],
      5+trunc(random()*150)
    );
    COMMIT;
    PERFORM pg_sleep(random() * 0.25);
  END LOOP;
END $$;
```

Die Abfrage fügt ca. acht Zeilen pro Sekunde ein. Die Zeilen werden auf verschiedenen Workerknoten gespeichert, gemäß Anweisung durch die Verteilungsspalte `site_id`.

   > [!NOTE]
   > Führen Sie die Abfrage zur Datengenerierung weiter aus, und öffnen Sie für die verbleibenden Befehle in diesem Tutorial eine zweite psql-Verbindung.
   >

## <a name="query"></a>Abfrage

Die Hyperscale-Hostingoption ermöglicht es aus Geschwindigkeitsgründen, dass Abfragen von mehreren Knoten parallel verarbeitet werden. Beispielsweise berechnet die Datenbank Aggregate wie SUM und COUNT auf Workerknoten und kombiniert die Ergebnisse in einer endgültigen Antwort.

Die folgende Abfrage zählt die Webanforderungen pro Minute zusammen mit einer Reihe von Statistikangaben.
Versuchen Sie, sie in psql auszuführen, und beobachten Sie die Ergebnisse.

```sql
SELECT
  site_id,
  date_trunc('minute', ingest_time) as minute,
  COUNT(1) AS request_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
  SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
FROM http_request
WHERE date_trunc('minute', ingest_time) > now() - '5 minutes'::interval
GROUP BY site_id, minute
ORDER BY minute ASC;
```

## <a name="rolling-up-data"></a>Rollup von Daten

Die vorherige Abfrage funktioniert gut in den frühen Phasen, aber mit zunehmender Datenmenge nimmt ihre Leistung ab. Selbst bei verteilter Verarbeitung ist es schneller, diese Daten vorab zu berechnen als sie wiederholt neu zu berechnen.

Wir können sicherstellen, dass unser Dashboard schnell bleibt, indem wir regelmäßig einen Rollup der Rohdaten in eine Aggregattabelle ausführen. Sie können mit der Aggregationsdauer experimentieren. Wir haben eine minütliche Aggregationstabelle verwendet, aber Sie könnten Daten stattdessen nach 5, 15 oder 60 Minuten aufschlüsseln.

Um dieses Rollup einfacher auszuführen, bringen wir es in eine plpgsql-Funktion ein. Führen Sie diese Befehle in psql aus, um die Funktion `rollup_http_request` zu erstellen.

```sql
-- initialize to a time long ago
INSERT INTO latest_rollup VALUES ('10-10-1901');

-- function to do the rollup
CREATE OR REPLACE FUNCTION rollup_http_request() RETURNS void AS $$
DECLARE
  curr_rollup_time timestamptz := date_trunc('minute', now());
  last_rollup_time timestamptz := minute from latest_rollup;
BEGIN
  INSERT INTO http_request_1min (
    site_id, ingest_time, request_count,
    success_count, error_count, average_response_time_msec
  ) SELECT
    site_id,
    date_trunc('minute', ingest_time),
    COUNT(1) as request_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
    SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
  FROM http_request
  -- roll up only data new since last_rollup_time
  WHERE date_trunc('minute', ingest_time) <@
          tstzrange(last_rollup_time, curr_rollup_time, '(]')
  GROUP BY 1, 2;

  -- update the value in latest_rollup so that next time we run the
  -- rollup it will operate on data newer than curr_rollup_time
  UPDATE latest_rollup SET minute = curr_rollup_time;
END;
$$ LANGUAGE plpgsql;
```

Wenn unsere Funktion an Ort und Stelle ist, führen Sie sie aus, um den Rollup der Daten auszuführen:

```sql
SELECT rollup_http_request();
```

Und wenn unsere Daten in voraggregierter Form vorliegen, können wir die Rolluptabelle abfragen, um den gleichen Bericht wie zuvor zu erhalten. Führen Sie die folgende Abfrage aus:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Ablauf alter Daten

Durch die Rollups werden die Abfragen schneller, wir müssen aber trotzdem alte Daten ablaufen lassen, um aus dem Ruder laufende Speicherkosten zu vermeiden. Entscheiden Sie, wie lange Sie Daten für die einzelnen Granularitäten aufbewahren möchten, und verwenden Sie Standardabfragen, um abgelaufene Daten zu löschen. Im folgenden Beispiel haben wir beschlossen, Rohdaten einen Tag lang und minütliche Aggregationen einen Monat lang aufzubewahren:

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

In einer Produktionsumgebung könnten Sie diese Abfragen in einer Funktion verpacken und sie jede Minute in einem cron-Auftrag aufrufen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In den vorherigen Schritten haben Sie Azure-Ressourcen in einer Servergruppe erstellt. Wenn Sie diese Ressourcen nicht mehr benötigen, löschen Sie die Servergruppe. Klicken Sie auf der Seite *Übersicht* für Ihre Servergruppe auf die Schaltfläche *Löschen*. Wenn Sie auf einer Popupseite dazu aufgefordert werden, bestätigen Sie den Namen der Servergruppe, und klicken Sie abschließend auf die Schaltfläche *Löschen*.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie erfahren, wie Sie eine Hyperscale (Citus)-Servergruppe bereitstellen. Sie haben mithilfe von psql eine Verbindung mit ihr hergestellt, haben ein Schema erstellt und Daten verteilt. Sie haben gelernt, Daten in Rohform abzurufen und diese Daten regelmäßig zu aggregieren, die aggregierten Tabellen abzufragen und alte Daten ablaufen zu lassen.

- Weitere Informationen zu [Knotentypen](./concepts-hyperscale-nodes.md) von Servergruppen
- Bestimmen der besten [Anfangsgröße](howto-hyperscale-scale-initial.md) für Ihre Servergruppe

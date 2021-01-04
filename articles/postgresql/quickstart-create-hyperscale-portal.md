---
title: 'Schnellstart: Erstellen einer Servergruppe – Hyperscale (Citus) – Azure Database for PostgreSQL'
description: Schnellstartanleitung zum Erstellen und Abfragen verteilter Tabellen in Azure Database for PostgreSQL – Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 08/17/2020
ms.openlocfilehash: 03a6e927a074067e85f1a3adca38cae386d1af38
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026215"
---
# <a name="quickstart-create-a-hyperscale-citus-server-group-in-the-azure-portal"></a>Schnellstart: Erstellen einer Hyperscale (Citus)-Servergruppe im Azure-Portal

Azure-Datenbank für PostgreSQL ist ein verwalteter Dienst, mit dem Sie hochverfügbare PostgreSQL-Datenbanken in der Cloud ausführen, verwalten und skalieren können. In dieser Schnellstartanleitung erfahren Sie, wie Sie über das Azure-Portal eine Servergruppe vom Typ „Azure Database for PostgreSQL – Hyperscale (Citus)“ erstellen. Dabei erkunden Sie verteilte Daten: knotenübergreifendes Sharding von Tabellen, Erfassen von Beispieldaten und Ausführen von Abfragen, die auf mehreren Knoten ausgeführt werden.

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>Erstellen und Verteilen von Tabellen

Nachdem Sie mithilfe von psql eine Verbindung mit dem Hyperscale-Koordinatorknoten hergestellt haben, können Sie einige grundlegende Aufgaben ausführen.

Innerhalb der Hyperscale (Citus)-Server gibt es drei Arten von Tabellen:

- Verteilte oder Shardtabellen (zur besseren Leistungsskalierung und Parallelisierung aufgeteilt)
- Verweistabellen (mehrere Kopien beibehalten)
- Lokale Tabellen (häufig für interne Administrationstabellen verwendet)

In diesem Schnellstart liegt der Schwerpunkt in erster Linie auf verteilten Tabellen, mit denen Sie sich hier vertraut machen.

Bei dem Datenmodell, mit dem Sie arbeiten, handelt es sich einfach um Benutzer- und Ereignisdaten aus GitHub. Die Ereignisse umfassen das Erstellen einer Verzweigung, Git-Commits im Zusammenhang mit einer Organisation und vieles mehr.

Nachdem Sie über psql eine Verbindung hergestellt haben, können Sie die Tabellen erstellen. Führen Sie in der psql-Konsole Folgendes aus:

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

Das Feld `payload` von `github_events` weist den Datentyp JSONB auf. JSONB ist der JSON-Datentyp im Binärformat in Postgres. Mit diesem Datentyp kann auf einfache Weise ein flexibles Schema in einer einzelnen Spalte gespeichert werden.

Postgres kann einen `GIN`-Index für diesen Typ erstellen, durch den jeder darin enthaltene Schlüssel und Wert indiziert wird. Mit einem Index kann die Nutzlast mit verschiedenen Bedingungen schnell und einfach abgefragt werden. Erstellen Sie nun eine Reihe von Indizes, bevor Sie die Daten laden. In psql:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Als Nächstes verwenden Sie diese Postgres-Tabellen im Koordinatorknoten und weisen Hyperscale (Citus) an, diese per Sharding auf die Worker zu verteilen. Zu diesem Zweck führen Sie eine Abfrage für jede Tabelle aus und geben dabei den Schlüssel für das Sharding an. Im aktuellen Beispiel wird ein Sharding sowohl für die Ereignis- als auch die Benutzertabelle nach `user_id` durchgeführt:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../includes/azure-postgresql-hyperscale-dist-alert.md)]

Jetzt können Daten geladen werden. Stellen Sie in psql an der Shell eine Verbindung her, um die Dateien herunterzuladen:

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

Als Nächstes laden Sie die Daten aus den Dateien in die verteilten Tabellen:

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Ausführen von Abfragen

Nun kommt der interessante Teil – das Ausführen von Abfragen. Beginnen Sie mit einer einfachen Abfrage vom Typ `count (*)`, um festzustellen, wie viele Daten geladen wurden:

```sql
SELECT count(*) from github_events;
```

Das hat gut funktioniert. Auf diese Art der Aggregation wird in Kürze noch genauer eingegangen, doch jetzt sehen Sie sich erst einmal ein paar andere Abfragen an. In der JSONB-Spalte `payload` sind so einige Daten enthalten, doch variieren sie je nach Ereignistyp. `PushEvent`-Ereignisse enthalten eine Größe, die die Anzahl der eindeutigen Commits für den Pushvorgang beinhaltet. Hiermit kann die Gesamtzahl von Commits pro Stunde ermittelt werden:

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Bisher waren bei den Abfragen ausschließlich „github\_events“ einbezogen, doch können diese Informationen auch mit „github\_users“ kombiniert werden. Da ein Sharding sowohl für Benutzer als auch Ereignisse mit demselben Bezeichner (`user_id`) durchgeführt wurde, werden die Zeilen beider Tabellen mit übereinstimmenden Benutzer-IDs auf denselben Datenbankknoten [zusammengestellt](concepts-hyperscale-colocation.md) und können auf einfache Weise verknüpft werden.

Bei einer Verknüpfung basierend auf `user_id` kann Hyperscale (Citus) deren Ausführung per Pushvorgang in Shards für die parallele Ausführung auf Workerknoten übertragen. Suchen Sie beispielsweise nach den Benutzern, die die größte Anzahl von Repositorys erstellt haben:

```sql
SELECT gu.login, count(*)
  FROM github_events ge
  JOIN github_users gu
    ON ge.user_id = gu.user_id
 WHERE ge.event_type = 'CreateEvent'
   AND ge.payload @> '{"ref_type": "repository"}'
 GROUP BY gu.login
 ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In den vorherigen Schritten haben Sie Azure-Ressourcen in einer Servergruppe erstellt. Wenn Sie diese Ressourcen nicht mehr benötigen, löschen Sie die Servergruppe. Klicken Sie auf der Seite **Übersicht** für Ihre Servergruppe auf die Schaltfläche **Löschen**. Wenn Sie auf einer Popupseite dazu aufgefordert werden, bestätigen Sie den Namen der Servergruppe, und klicken Sie abschließend auf die Schaltfläche **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie erfahren, wie Sie eine Hyperscale-Servergruppe (Citus) bereitstellen. Sie haben mithilfe von psql eine Verbindung mit ihr hergestellt, haben ein Schema erstellt und Daten verteilt.

- Tutorial zum [Erstellen skalierbarer mehrinstanzenfähiger Anwendungen](./tutorial-design-database-hyperscale-multi-tenant.md)
- Bestimmen der besten [Anfangsgröße](howto-hyperscale-scale-initial.md) für Ihre Servergruppe

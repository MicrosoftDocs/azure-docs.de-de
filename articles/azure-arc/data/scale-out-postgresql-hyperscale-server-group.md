---
title: Aufskalieren der Azure Database for PostgreSQL Hyperscale-Servergruppe
description: Aufskalieren der Azure Database for PostgreSQL Hyperscale-Servergruppe
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 17bdae658c7095c44a7ae9f30fd85a6c45bf1546
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779973"
---
# <a name="scale-out-your-azure-arc-enabled-postgresql-hyperscale-server-group-by-adding-more-worker-nodes"></a>Aufskalieren der Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe durch Hinzufügen weiterer Workerknoten
In diesem Dokument wird erläutert, wie Sie eine Azure Arc-fähige PostgreSQL Hyperscale-Servergruppe aufskalieren. Dies geschieht anhand eines Szenarios. **Wenn Sie das Szenario nicht durchlaufen möchten und sich lediglich über das Konzept des Aufskalierens informieren möchten, fahren Sie mit dem Absatz [Aufskalieren](#scale-out)** fort.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-started"></a>Erste Schritte
Wenn Sie bereits mit dem Skalierungsmodell von Azure Arc-fähigem PostgreSQL Hyperscale oder Azure Database for PostgreSQL Hyperscale (Citus) vertraut sind, können Sie diesen Absatz überspringen. Falls nicht, empfehlen wir, in der Dokumentation zu Azure Database for PostgreSQL Hyperscale (Citus) zunächst die Seite mit Informationen zu diesem Skalierungsmodell zu lesen. Azure Database for PostgreSQL Hyperscale (Citus) ist dieselbe Technologie, die als Dienst in Azure gehostet wird (Platform-as-a-Service, auch PAAS) und dagegen nicht als Teil von Azure Arc-fähigen Datendiensten angeboten wird:
- [Knoten und Tabellen](../../postgresql/concepts-hyperscale-nodes.md)
- [Festlegen des Anwendungstyps](../../postgresql/concepts-hyperscale-app-type.md)
- [Auswählen einer Verteilungsspalte](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
- [Tabellenzusammenstellung](../../postgresql/concepts-hyperscale-colocation.md)
- [Verteilen und Ändern von Tabellen](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
- [Entwerfen einer Datenbank mit mehreren Mandanten](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
- [Entwerfen eines Dashboards für Echtzeitanalysen](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> \* Überspringen Sie die Abschnitte **Anmelden am Azure-Portal** und **Erstellen einer Azure Database for PostgreSQL-Instanz für Hyperscale (Citus)** in den oben aufgeführten Dokumenten. Implementieren Sie die restlichen Schritte in Ihrer Azure Arc-Bereitstellung. Diese Abschnitte sind speziell für den PaaS-Dienst „Azure Database for PostgreSQL Hyperscale (Citus)“ in der Azure-Cloud vorgesehen. Die anderen Abschnitte der Dokumente sind jedoch direkt auf Ihre Azure Arc-fähige PostgreSQL Hyperscale-Instanz übertragbar.

## <a name="scenario"></a>Szenario
Dieses Szenario bezieht sich auf die PostgreSQL Hyperscale-Servergruppe, die im Dokument [Erstellen einer Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe](create-postgresql-hyperscale-server-group.md) als Beispiel erstellt wurde.

### <a name="load-test-data"></a>Laden von Testdaten
Im Szenario wird eine Stichprobe von öffentlichen GitHub-Daten verwendet, die auf der [Citus Data-Website](https://www.citusdata.com/) verfügbar sind (Citus Data ist Teil von Microsoft).

#### <a name="connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Herstellen einer Verbindung mit einer Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe

##### <a name="list-the-connection-information"></a>Auflisten der Verbindungsinformationen
Stellen Sie eine Verbindung mit Ihrer Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe her, indem Sie zunächst die Verbindungsinformationen abrufen: Das allgemeine Format des Befehls lautet
```console
azdata arc postgres endpoint list -n <server name>
```
Zum Beispiel:
```console
azdata arc postgres endpoint list -n postgres01
```

Beispielausgabe:

```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

##### <a name="connect-with-the-client-tool-of-your-choice"></a>Stellen Sie eine Verbindung mit dem Clienttool Ihrer Wahl her.

Führen Sie die folgende Abfrage aus, um sicherzustellen, dass Sie aktuell über mindestens zwei Hyperscale-Workerknoten verfügen, die jeweils einem Kubernetes-Pod entsprechen:

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(2 rows)
```

#### <a name="create-a-sample-schema"></a>Erstellen eines Beispielschemas
Erstellen Sie zwei Tabellen, indem Sie die folgende Abfrage ausführen:

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

JSONB ist der JSON-Datentyp im Binärformat in PostgreSQL. Im Datentyp wird ein flexibles Schema in einer einzelnen Spalte und mit PostgreSQL gespeichert. Das Schema verfügt über einen GIN-Index, um jeden darin enthaltenen Schlüssel und Wert zu indizieren. Ein GIN-Index beschleunigt und vereinfacht die direkte Abfrage der Nutzlast mit verschiedenen Bedingungen. Erstellen wir nun eine Reihe von Indizes, bevor wir die Daten laden:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Um Standardtabellen horizontal zu partitionieren, führen Sie eine Abfrage für jede Tabelle aus. Geben Sie die Tabelle an, die horizontal partitioniert werden soll, und den Schlüssel, den wir für die horizontale Partitionierung verwenden möchten. Wir führen eine horizontale Partitionierung sowohl für die Ereignis- als auch die Benutzertabelle nach „user_id“ durch:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

#### <a name="load-sample-data"></a>Laden von Beispieldaten
Laden der Daten mit COPY... AUS DEM PROGRAM:

```sql
COPY github_users FROM PROGRAM 'curl "https://examples.citusdata.com/users.csv"' WITH ( FORMAT CSV );
COPY github_events FROM PROGRAM 'curl "https://examples.citusdata.com/events.csv"' WITH ( FORMAT CSV );
```

#### <a name="query-the-data"></a>Abfragen der Daten
Jetzt messen Sie, wie lange eine einfache Abfrage mit zwei Knoten dauert:

```sql
SELECT COUNT(*) FROM github_events;
```
Notieren Sie sich die Ausführungszeit der Abfrage.


## <a name="scale-out"></a>Aufskalieren
Das allgemeine Format des Befehls zum Aufskalieren lautet:
```console
azdata arc postgres server edit -n <server group name> -w <target number of worker nodes>
```

> [!CAUTION]
> Diese Vorschauversion unterstützt kein nachfolgendes Abskalieren. Beispielsweise ist es noch nicht möglich, die Anzahl der Workerknoten zu reduzieren. Falls dies erforderlich wird, müssen Sie die Daten extrahieren/sichern, die Servergruppe löschen, eine neue Servergruppe mit weniger Workerknoten erstellen und die Daten dann importieren.

In diesem Beispiel erhöhen wir die Anzahl der Workerknoten von 2 auf 4, indem wir den folgenden Befehl ausführen:

```console
azdata arc postgres server edit -n postgres01 -w 4
```

Während Sie Knoten hinzufügen, wird für die Servergruppe der Status „Ausstehend“ angezeigt. Zum Beispiel:
```console
azdata arc postgres server list
```

```console
Name        State          Workers
----------  -------------  ---------
postgres01  Pending 4/5    4
```

Sobald die Knoten verfügbar sind, wird der Hyperscale-Shardrebalancer automatisch ausgeführt, um die Daten auf die neuen Knoten zu verteilen. Die Aufskalierung ist ein Onlinevorgang. Während die Knoten hinzugefügt und die Daten neu auf die Knoten verteilt werden, bleiben die Daten für Abfragen verfügbar.

### <a name="verify-the-new-shape-of-the-server-group-optional"></a>Überprüfen der neuen Zusammensetzung der Servergruppe (optional)
Verwenden Sie eine der folgenden Methoden, um zu überprüfen, ob die Servergruppe jetzt die zusätzlichen Workerknoten verwendet, die Sie hinzugefügt haben.

#### <a name="with-azdata"></a>Mit azdata:
Führen Sie den folgenden Befehl aus:
```console
azdata arc postgres server list
```

Mit dem Befehl wird neben der Anzahl der Workerknoten die Liste der Servergruppen zurückgegeben, die in Ihrem Namespace erstellt wurden. Zum Beispiel:
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

#### <a name="with-kubectl"></a>Mit kubectl:
Führen Sie den folgenden Befehl aus:
```console
kubectl get postgresql-12
```

Mit dem Befehl wird neben der Anzahl der Workerknoten die Liste der Servergruppen zurückgegeben, die in Ihrem Namespace erstellt wurden. Zum Beispiel:
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   4/4          10.0.0.4:31066      4d20h
```
> [!NOTE]
> Wenn Sie eine Servergruppe der Version 11 von PostgreSQL anstelle von Version 12 erstellt haben, führen Sie stattdessen den folgenden Befehl aus: _kubectl get postgresql-11_

#### <a name="with-a-sql-query"></a>Mit einer SQL-Abfrage:
Stellen Sie mit dem Clienttool Ihrer Wahl eine Verbindung mit Ihrer Servergruppe her, und führen Sie die folgende Abfrage aus:

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      3 |       3 | pg1-3.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      4 |       4 | pg1-4.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(4 rows)
```

## <a name="return-to-the-scenario"></a>Zurückkehren zum Szenario

Wenn Sie die Ausführungszeit der Select Count-Abfrage mit dem Beispieldataset vergleichen möchten, verwenden Sie dieselbe Count-Abfrage. Sie kann für die vier Workerknoten verwendet werden, ohne dass Änderungen an der SQL-Anweisung vorgenommen werden müssen.

```sql
SELECT COUNT(*) FROM github_events;
```
Notieren Sie sich die Ausführungszeit.


> [!NOTE]
> Je nach Ihrer Umgebung – wenn Sie z. B. Ihre Testservergruppe mit `kubeadm` auf einer VM mit einem einzelnen Knoten bereitgestellt haben – fällt die Ausführungszeit möglicherweise etwas kürzer aus. Sehen Sie sich die folgenden kurzen Videos an, um sich einen besseren Eindruck von der Art der Leistungsverbesserung zu verschaffen, die Sie mit einer Azure Arc-fähigen PostgreSQL Hyperscale-Instanz erreichen könnten:
>* [Hochleistungs-HTAP mit Azure PostgreSQL Hyperscale (Citus)](https://www.youtube.com/watch?v=W_3e07nGFxY)
>* [Entwickeln von HTAP-Anwendungen mit Python und Azure PostgreSQL Hyperscale (Citus)](https://www.youtube.com/watch?v=YDT8_riLLs0)


## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über das [Hochskalieren und Herunterskalieren (von Speicher und virtuellen Kernen) für Ihre Azure Arc-fähige PostgreSQL Hyperscale-Servergruppe](scale-up-down-postgresql-hyperscale-server-group-using-cli.md).
- Informieren Sie sich über das Festlegen von Serverparametern in Ihrer Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe.
- Lesen Sie die Konzepte und Schrittanleitungen zu Azure Database for PostgreSQL Hyperscale, um Ihre Daten auf mehrere PostgreSQL Hyperscale-Knoten zu verteilen und von der gesamten Leistung von Azure Database for Postgres Hyperscale zu profitieren. :
    * [Knoten und Tabellen](../../postgresql/concepts-hyperscale-nodes.md)
    * [Festlegen des Anwendungstyps](../../postgresql/concepts-hyperscale-app-type.md)
    * [Auswählen einer Verteilungsspalte](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Tabellenzusammenstellung](../../postgresql/concepts-hyperscale-colocation.md)
    * [Verteilen und Ändern von Tabellen](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Entwerfen einer Datenbank mit mehreren Mandanten](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Entwerfen eines Dashboards für Echtzeitanalysen](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

 > \* Überspringen Sie die Abschnitte **Anmelden am Azure-Portal** und **Erstellen einer Azure Database for PostgreSQL-Instanz für Hyperscale (Citus)** in den oben aufgeführten Dokumenten. Implementieren Sie die restlichen Schritte in Ihrer Azure Arc-Bereitstellung. Diese Abschnitte sind speziell für den PaaS-Dienst „Azure Database for PostgreSQL Hyperscale (Citus)“ in der Azure-Cloud vorgesehen. Die anderen Abschnitte der Dokumente sind jedoch direkt auf Ihre Azure Arc-fähige PostgreSQL Hyperscale-Instanz übertragbar.

- [Speicherkonfiguration und Kubernetes-Speicherkonzepte](storage-configuration.md)
- [Kubernetes-Ressourcenmodell](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

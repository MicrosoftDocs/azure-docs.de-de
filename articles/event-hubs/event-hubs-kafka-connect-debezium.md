---
title: Integrieren von Apache Kafka Connect in Azure Event Hubs (Vorschau) mit Debezium für Change Data Capture
description: Dieser Artikel enthält Informationen zur Verwendung von Debezium mit Azure Event Hubs für Kafka.
ms.topic: how-to
author: abhirockzz
ms.author: abhishgu
ms.date: 08/11/2020
ms.openlocfilehash: ae3ef2e1f35be432558769c512845543867ef27a
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505408"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-preview-with-debezium-for-change-data-capture"></a>Integrieren der Apache Kafka Connect-Unterstützung in Azure Event Hubs (Vorschau) mit Debezium für Change Data Capture

Bei **Change Data Capture (CDC)** handelt es sich um eine Methode, die verwendet wird, um Änderungen auf Zeilenebene in Datenbanktabellen als Reaktion auf Erstellungs-, Aktualisierungs- und Löschvorgänge nachzuverfolgen. [Debezium](https://debezium.io/) ist eine verteilte Plattform, die auf den in verschiedenen Datenbanken verfügbaren Change Data Capture-Funktionen aufbaut (z. B. [logische Decodierung in PostgreSQL](https://www.postgresql.org/docs/current/static/logicaldecoding-explanation.html)). Es bietet eine Reihe von [Kafka Connect-Connectors](https://debezium.io/documentation/reference/1.2/connectors/index.html), die auf Änderungen auf Zeilenebene in Datenbanktabellen zugreifen und diese in Ereignisstreams konvertieren, die dann an [Apache Kafka](https://kafka.apache.org/) gesendet werden.

In diesem Tutorial werden Sie schrittweise durch den Vorgang geführt, um ein Change Data Capture-basiertes System in Azure mithilfe von [Azure Event Hubs](./event-hubs-about.md?WT.mc_id=devto-blog-abhishgu) (für Kafka), [Azure DB for PostgreSQL](../postgresql/overview.md) und Debezium einzurichten. Es verwendet den [Debezium PostgreSQL-Connector](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html), um Datenbankänderungen aus PostgreSQL in Kafka-Themen in Azure Event Hubs zu streamen.

> [!NOTE]
> Dieser Artikel enthält Verweise auf den Begriff *Whitelist*, den Microsoft nicht länger verwendet. Sobald der Begriff aus der Software entfernt wurde, wird er auch aus diesem Artikel entfernt.

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Erstellen eines Event Hubs-Namespace
> * Einrichten und Konfigurieren von Azure Database for PostgreSQL
> * Konfigurieren und Ausführen von Kafka Connect mit dem PostgreSQL-Connector von Debezium
> * Testen von Change Data Capture
> * (Optional) Nutzen von Datenänderungsereignissen mit einem `FileStreamSink`-Connector

## <a name="pre-requisites"></a>Voraussetzungen
Im Rahmen dieser exemplarischen Vorgehensweise ist Folgendes erforderlich:

- Azure-Abonnement. Falls Sie nicht über ein Abonnement verfügen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/).
- Linux/macOS
- Kafka-Release (Version 1.1.1, Scala-Version 2.11), verfügbar unter [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1)
- Lesen Sie den Einführungsartikel [Event Hubs für Apache Kafka](./event-hubs-for-kafka-ecosystem-overview.md).

## <a name="create-an-event-hubs-namespace"></a>Erstellen eines Event Hubs-Namespace
Ein Event Hubs-Namespace ist erforderlich, um Nachrichten an einen Event Hubs-Dienst zu senden und von diesem zu empfangen. Anweisungen zum Erstellen eines Namespace und eines Event Hub finden Sie unter [Erstellen eines Event Hubs](event-hubs-create.md). Rufen Sie die Event Hubs-Verbindungszeichenfolge und den vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) zur späteren Verwendung ab. Anweisungen hierzu finden Sie unter [Get an Event Hubs connection string](event-hubs-get-connection-string.md) (Abrufen einer Event Hubs-Verbindungszeichenfolge). 

## <a name="setup-and-configure-azure-database-for-postgresql"></a>Einrichten und Konfigurieren von Azure Database for PostgreSQL
[Azure Database for PostgreSQL](../postgresql/overview.md) ist ein relationaler Datenbankdienst, der auf der Communityversion des Open-Source-Datenbankmoduls PostgreSQL basiert und in zwei Bereitstellungsoptionen verfügbar ist: „Einzelserver“ und „Hyperscale (Citus)“ [Befolgen Sie diese Anweisungen](../postgresql/quickstart-create-server-database-portal.md), um einen Azure Database for PostgreSQL-Server über das Azure-Portal zu erstellen. 

## <a name="setup-and-run-kafka-connect"></a>Einrichten und Ausführen von Kafka Connect
In diesem Abschnitt werden die folgenden Themen behandelt:

- Installation des Debezium-Connectors
- Konfigurieren von Kafka Connect für Event Hubs
- Starten des Kafka Connect-Clusters mit dem Debezium-Connector

### <a name="download-and-setup-debezium-connector"></a>Herunterladen und Einrichten des Debezium-Connectors
Befolgen Sie die neuesten Anweisungen in der [Debezium-Dokumentation](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector), um den Connector herunterzuladen und einzurichten.

- Laden Sie das Plug-In-Archiv des Connectors herunter. Um beispielsweise die Version `1.2.0` des Connectors herunterzuladen, verwenden Sie diesen Link: https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.2.0.Final/debezium-connector-postgres-1.2.0.Final-plugin.tar.gz.
- Extrahieren Sie die JAR-Dateien, und kopieren Sie sie in den [Kafka Connect-Plug-In-Pfad](https://kafka.apache.org/documentation/#connectconfigs).


### <a name="configure-kafka-connect-for-event-hubs"></a>Konfigurieren von Kafka Connect für Event Hubs
Es ist nur eine minimale Neukonfiguration erforderlich, wenn Sie den Kafka Connect-Durchsatz von Kafka an Event Hubs umleiten.  Im folgenden Beispiel `connect-distributed.properties` ist dargestellt, wie Sie Connect konfigurieren, um die Authentifizierung und Kommunikation mit dem Kafka-Endpunkt unter Event Hubs einzurichten:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

> [!IMPORTANT]
> Ersetzen Sie `{YOUR.EVENTHUBS.CONNECTION.STRING}` durch die Verbindungszeichenfolge für Ihren Event Hubs-Namespace. Anweisungen zum Abrufen der Verbindungszeichenfolge finden Sie unter [Abrufen einer Event Hubs-Verbindungszeichenfolge](event-hubs-get-connection-string.md). Hier sehen Sie eine Beispielkonfiguration: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`


### <a name="run-kafka-connect"></a>Ausführen von Kafka Connect
In diesem Schritt wird ein Kafka Connect-Worker lokal im verteilten Modus gestartet, indem Event Hubs zum Warten des Clusterzustands verwendet wird.

1. Speichern Sie die obige Datei `connect-distributed.properties` lokal.  Achten Sie darauf, alle Werte in geschweiften Klammern zu ersetzen.
2. Navigieren Sie zum Speicherort des Kafka-Release auf Ihrem Computer.
3. Führen Sie `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties` aus, und warten Sie, bis der Cluster startet.

> [!NOTE]
> Kafka Connect verwendet die Kafka-AdminClient-API, um automatisch Themen mit empfohlenen Konfigurationen, einschließlich Komprimierung, zu erstellen. Eine schnelle Überprüfung des Namespace im Azure-Portal ergibt, dass die internen Themen des Connect-Workers automatisch erstellt wurden.
>
> Interne Kafka Connect-Themen **müssen Komprimierung verwenden**.  Das Event Hubs-Team ist nicht für die Korrektur falscher Konfigurationen zuständig, sollten interne Connect-Themen nicht ordnungsgemäß konfiguriert sein.

### <a name="configure-and-start-the-debezium-postgresql-source-connector"></a>Konfigurieren und Starten des Debezium PostgreSQL-Quellconnectors

Erstellen Sie eine Konfigurationsdatei (`pg-source-connector.json`) für den PostgreSQL-Quellconnector, und ersetzen Sie dabei die Werte gemäß Ihrer Azure PostgreSQL-Instanz.

```json
{
    "name": "todo-connector",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "<replace with Azure PostgreSQL instance name>.postgres.database.azure.com",
        "database.port": "5432",
        "database.user": "<replace with database user name>",
        "database.password": "<replace with database password>",
        "database.dbname": "postgres",
        "database.server.name": "my-server",
        "plugin.name": "wal2json",
        "table.whitelist": "public.todos"
    }
}
```

> [!TIP]
> Das `database.server.name`-Attribut ist ein logischer Name, der einen Namespace für den spezifischen überwachten PostgreSQL-Datenbankserver/-Cluster identifiziert und bereitstellt. Ausführliche Informationen finden Sie in der [Debezium-Dokumentation](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-property-database-server-name).

Verwenden Sie zum Erstellen einer Instanz des Connectors den Kafka Connect-REST-API-Endpunkt:

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-connector.json http://localhost:8083/connectors
```

So überprüfen Sie den Status des Connectors

```bash
curl -s http://localhost:8083/connectors/todo-connector/status
```

## <a name="test-change-data-capture"></a>Testen von Change Data Capture
Um Change Data Capture in Aktion zu sehen, müssen Sie Datensätze in der Azure PostgreSQL-Datenbank erstellen/aktualisieren/löschen.

Beginnen Sie damit, dass Sie eine Verbindung mit Ihrer Azure PostgreSQL-Datenbank herstellen (im folgenden Beispiel wird [psql](https://www.postgresql.org/docs/12/app-psql.html) verwendet).

```bash
psql -h <POSTGRES_INSTANCE_NAME>.postgres.database.azure.com -p 5432 -U <POSTGRES_USER_NAME> -W -d <POSTGRES_DB_NAME> --set=sslmode=require

e.g. 

psql -h my-postgres.postgres.database.azure.com -p 5432 -U testuser@my-postgres -W -d postgres --set=sslmode=require
```

**Erstellen einer Tabelle und Einfügen von Datensätzen**

```sql
CREATE TABLE todos (id SERIAL, description VARCHAR(50), todo_status VARCHAR(10), PRIMARY KEY(id));

INSERT INTO todos (description, todo_status) VALUES ('setup postgresql on azure', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('setup kafka connect', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('configure and install connector', 'in-progress');
INSERT INTO todos (description, todo_status) VALUES ('start connector', 'pending');
```

Der Connector sollte jetzt aktiv werden und Datenänderungsereignisse an ein Event Hubs-Thema mit der folgenden `my-server.public.todos` senden, vorausgesetzt, Sie haben `my-server` als Wert für `database.server.name`, und `public.todos` ist die Tabelle, deren Änderungen Sie nachverfolgen (gemäß `table.whitelist`-Konfiguration).

**Überprüfen des Event Hubs-Themas**

Betrachten wir nun den Inhalt des Themas, um sicherzustellen, dass alles wie erwartet funktioniert. Im folgenden Beispiel wird [`kafkacat`](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) verwendet, aber Sie können auch [einen Consumer mithilfe einer der hier aufgeführten Optionen erstellen](apache-kafka-developer-guide.md).

Erstellen Sie eine Datei namens `kafkacat.conf` mit folgendem Inhalt:

```
metadata.broker.list=<enter event hubs namespace>.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanisms=PLAIN
sasl.username=$ConnectionString
sasl.password=<enter event hubs connection string>
```

> [!NOTE]
> Aktualisieren Sie die Attribute `metadata.broker.list` und `sasl.password` in `kafkacat.conf` gemäß den Event Hubs-Informationen. 

Starten Sie in einem anderen Terminal einen Consumer:

```bash
export KAFKACAT_CONFIG=kafkacat.conf
export BROKER=<enter event hubs namespace>.servicebus.windows.net:9093
export TOPIC=my-server.public.todos

kafkacat -b $BROKER -t $TOPIC -o beginning
```

Die JSON-Nutzlasten, die die Datenänderungsereignisse darstellen, die in Reaktion auf die Zeilen, die Sie gerade in der `todos`Tabelle hinzugefügt haben, in PostgreSQL generiert wurden, sollten angezeigt werden. Hier ist ein Codeausschnitt aus der Nutzlast:


```json
{
    "schema": {...},
    "payload": {
        "before": null,
        "after": {
            "id": 1,
            "description": "setup postgresql on azure",
            "todo_status": "complete"
        },
        "source": {
            "version": "1.2.0.Final",
            "connector": "postgresql",
            "name": "fullfillment",
            "ts_ms": 1593018069944,
            "snapshot": "last",
            "db": "postgres",
            "schema": "public",
            "table": "todos",
            "txId": 602,
            "lsn": 184579736,
            "xmin": null
        },
        "op": "c",
        "ts_ms": 1593018069947,
        "transaction": null
    }
```

Das Ereignis besteht aus der `payload` zusammen mit dem `schema` (aus Gründen der Übersichtlichkeit ausgelassen). Beachten Sie in Abschnitt `payload`, wie der Erstellungsvorgang (`"op": "c"`) dargestellt wird – `"before": null` bedeutet, dass es sich um eine neu eingefügte (`INSERT`) Zeile handelte, `after` stellt Werte für die Spalten in der Zeile bereit, `source` stellt die PostgreSQL-Instanzmetadaten von dem Ort bereit, an dem dieses Ereignis abgerufen wurde, usw.

Sie können dasselbe auch mit Aktualisierungs- oder Löschvorgängen ausprobieren und die Datenänderungsereignisse untersuchen. Um z. B. den Aufgabenstatus für `configure and install connector` zu aktualisieren (vorausgesetzt, seine `id` ist `3`):

```sql
UPDATE todos SET todo_status = 'complete' WHERE id = 3;
```

## <a name="optional-install-filestreamsink-connector"></a>(Optional) Installieren eines FileStreamSink-Connectors
Nachdem nun alle Änderungen an der `todos`-Tabelle in dem Event Hubs-Thema aufgezeichnet werden, verwenden wir den FileStreamSink-Connector (der standardmäßig in Kafka Connect verfügbar ist), um diese Ereignisse zu nutzen.

Erstellen Sie eine Konfigurationsdatei (`file-sink-connector.json`) für den Connector, und ersetzen Sie dabei das `file`-Attribut gemäß Ihrem Dateisystem.

```json
{
    "name": "cdc-file-sink",
    "config": {
        "connector.class": "org.apache.kafka.connect.file.FileStreamSinkConnector",
        "tasks.max": "1",
        "topics": "my-server.public.todos",
        "file": "<enter full path to file e.g. /Users/foo/todos-cdc.txt>"
    }
}
```

So erstellen Sie den Connector und überprüfen seinen Status

```bash
curl -X POST -H "Content-Type: application/json" --data @file-sink-connector.json http://localhost:8083/connectors

curl http://localhost:8083/connectors/cdc-file-sink/status
```

Datensätze einfügen/aktualisieren/löschen und die Datensätze in der konfigurierten Ausgabesenkendatei überwachen:

```bash
tail -f /Users/foo/todos-cdc.txt
```


## <a name="cleanup"></a>Cleanup
Mit Kafka Connect werden Event Hub-Themen zum Speichern von Konfigurationen, Offsets und des Status erstellt, die auch dann beibehalten werden, nachdem der Connect-Cluster heruntergefahren wurde. Wenn diese Beibehaltung nicht erwünscht ist, ist es ratsam, diese Themen zu löschen. Sie können auch den Event Hub `my-server.public.todos` löschen, der in dieser exemplarischen Vorgehensweise erstellt wurde.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Event Hubs für Kafka finden Sie in folgenden Artikeln:  

- [Spiegeln eines Kafka-Brokers in einem Event Hub](event-hubs-kafka-mirror-maker-tutorial.md)
- [Verbinden von Apache Spark mit einem Event Hub](event-hubs-kafka-spark-tutorial.md)
- [Verbinden von Apache Flink mit einem Event Hub](event-hubs-kafka-flink-tutorial.md)
- [Erkunden von Beispielen auf GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Verbinden von Akka Streams mit einem Event Hub](event-hubs-kafka-akka-streams-tutorial.md)
- [Apache Kafka-Entwicklerleitfaden für Azure Event Hubs](apache-kafka-developer-guide.md)
---
title: 'CREATE EXTERNAL STREAM (Transact-SQL): Azure SQL Edge'
description: Erfahren Sie mehr über die CREATE EXTERNAL STREAM-Anweisung in Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: 92658584030fa83da067eceab391d9bba2f034c0
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392298"
---
# <a name="create-external-stream-transact-sql"></a>CREATE EXTERNAL STREAM (Transact-SQL)

Das EXTERNAL STREAM-Objekt dient einem doppelten Zweck als Eingabe- und Ausgabestream. Es kann als Eingabe verwendet werden, um Streamingdaten von Ereigniserfassungsdiensten wie Azure Event Hub, Azure IoT Hub (oder Edge Hub) oder Kafka abzufragen, oder als Ausgabe, um anzugeben, wo und wie Ergebnisse aus einer Streamingabfrage gespeichert werden sollen.

Ein EXTERNAL STREAM kann auch als Ausgabe und Eingabe für Dienste wie Event Hubs oder Blob Storage angegeben und erstellt werden. Dies erleichtert die Verkettung von Szenarien, in denen eine Streamingabfrage Ergebnisse an den externen Stream als Ausgabe sendet und eine andere Streamingabfrage aus dem gleichen externen Stream als Eingabe liest.

Azure SQL Edge unterstützt derzeit nur die folgenden Datenquellen als Streameingaben und -ausgaben.

| Datenquellentyp | Eingabe | Output | BESCHREIBUNG |
|------------------|-------|--------|------------------|
| Azure IoT Edge Hub | J | J | Datenquelle zum Lesen und Schreiben von Streamingdaten in einen Azure IoT Edge Hub. Weitere Informationen finden Sie unter [IoT Edge Hub](../iot-edge/iot-edge-runtime.md#iot-edge-hub).|
| SQL-Datenbank | N | J | Datenquellenverbindung, um Streamingdaten in SQL-Datenbank zu schreiben. Die Datenbank kann eine lokale Datenbank in Azure SQL Edge oder eine Remotedatenbank in SQL Server oder Azure SQL-Datenbank sein.|
| Kafka | J | N | Datenquelle zum Lesen von Streamingdaten aus einem Kafka-Thema. Kafka-Unterstützung ist für die ARM64-Version von Azure SQL Edge nicht verfügbar.|



## <a name="syntax"></a>Syntax

```sql
CREATE EXTERNAL STREAM {external_stream_name}  
( <column_definition> [, <column_definition> ] * ) -- Used for Inputs - optional 
WITH  ( <with_options> )

<column_definition> ::=
  column_name <column_data_type>

<data_type> ::=
[ type_schema_name . ] type_name
    [ ( precision [ , scale ] | max ) ]

<with_options> ::=
  DATA_SOURCE = data_source_name, 
  LOCATION = location_name, 
  [FILE_FORMAT = external_file_format_name], --Used for Inputs - optional 
  [<optional_input_options>],
  [<optional_output_options>], 
  TAGS = <tag_column_value>

<optional_input_options> ::= 
  INPUT_OPTIONS = '[<Input_options_data>]'

<Input_option_data> ::= 
      <input_option_values> [ , <input_option_values> ]

<input_option_values> ::=
  PARTITIONS: [number_of_partitions]
  | CONSUMER_GROUP: [ consumer_group_name ] 
  | TIME_POLICY: [ time_policy ] 
  | LATE_EVENT_TOLERANCE: [ late_event_tolerance_value ] 
  | OUT_OF_ORDER_EVENT_TOLERANCE: [ out_of_order_tolerance_value ]
  
<optional_output_options> ::= 
  OUTPUT_OPTIONS = '[<output_option_data>]'

<output_option_data> ::= 
      <output_option_values> [ , <output_option_values> ]

<output_option_values> ::=
   REJECT_POLICY: [ reject_policy ] 
   | MINIMUM_ROWS: [ row_value ] 
   | MAXIMUM_TIME: [ time_value_minutes] 
   | PARTITION_KEY_COLUMN: [ partition_key_column_name ] 
   | PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ] 
   | SYSTEM_PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ] 
   | PARTITION_KEY: [ partition_key_name ] 
   | ROW_KEY: [ row_key_name ] 
   | BATCH_SIZE: [ batch_size_value ] 
   | MAXIMUM_BATCH_COUNT: [ batch_value ] 
   | STAGING_AREA: [ blob_data_source ]
 
<tag_column_value> ::= -- Reserved for Future Usage
); 
```

## <a name="arguments"></a>Argumente

- [DATA_SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [FILE_FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- **LOCATION** : Gibt den Namen für die tatsächlichen Daten oder den Speicherort in der Datenquelle an. 
   - Bei Edge Hub- oder Kafka-Streamobjekten gibt LOCATION den Namen des Edge Hub- oder Kafka-Themas an, aus dem gelesen bzw. in das geschrieben werden soll.
   - Bei SQL-Streamobjekten (SQL Server, Azure SQL-Datenbank oder Azure SQL Edge) gibt LOCATION den Namen der Tabelle an. Wenn der Stream in derselben Datenbank und im gleichen Schema wie die Zieltabelle erstellt wird, reicht nur der Tabellenname aus. Andernfalls müssen Sie den Tabellennamen (<database_name.schema_name.table_name) vollständig qualifizieren.
   - Bei einem Azure Blob Storage-Streamobjekt bezieht sich LOCATION auf das Pfadmuster, das innerhalb des Blobcontainers verwendet werden soll. Weitere Informationen zu diesem Feature finden Sie unter (/articles/stream-analytics/stream-analytics-define-outputs.md#blob-storage-and-azure-data-lake-gen2).

- **INPUT_OPTIONS** : Geben Sie Optionen als Schlüssel-Wert-Paare für Dienste wie Kafka und IoT Edge Hub an, die Eingaben für Streamingabfragen sind.
    - PARTITIONS: Anzahl der für ein Thema definierten Partitionen. Die maximale Anzahl von Partitionen, die verwendet werden können, beträgt 32.
      - Gilt für Kafka-Eingabestreams
    - CONSUMER_GROUP: Event Hubs und IoT Hubs beschränken die Anzahl der Leser innerhalb einer Consumergruppe (auf 5). Wenn dieses Feld leer bleibt, wird die Consumergruppe „$Default“ verwendet.
      - Zur künftigen Verwendung reserviert. Gilt nicht für Azure SQL Edge.  
    - TIME_POLICY: Beschreibt, ob Ereignisse gelöscht oder die Ereigniszeit angepasst werden soll, wenn zu späte Ereignisse oder Ereignisse außerhalb der Reihenfolge ihren Toleranzwert übergeben.
      - Zur künftigen Verwendung reserviert. Gilt nicht für Azure SQL Edge.
    - LATE_EVENT_TOLERANCE: Die maximal zulässige Zeitverzögerung. Die Verzögerung stellt den Unterschied zwischen dem Zeitstempel des Ereignisses und der Systemuhr dar.
      - Zur künftigen Verwendung reserviert. Gilt nicht für Azure SQL Edge.
    - OUT_OF_ORDER_EVENT_TOLERANCE: Ereignisse können in nicht ordnungsgemäßer Reihenfolge eintreffen, nachdem Sie die Eingabe bis zur Streamingabfrage durchlaufen haben. Diese Ereignisse können so akzeptiert werden, wie sie sind, oder Sie können für einen bestimmten Zeitraum eine Pause festlegen, um sie neu zu ordnen.
      - Zur künftigen Verwendung reserviert. Gilt nicht für Azure SQL Edge.
        
- **OUTPUT_OPTIONS** : Geben Sie Optionen als Schlüssel-Wert-Paare für unterstützte Dienste an, die Ausgaben für Streamingabfragen sind. 
  - REJECT_POLICY:  DROP | RETRY: Gibt die Richtlinien für die Fehlerbehandlung von Daten an, wenn Datenkonvertierungsfehler auftreten. 
    - Gilt für alle unterstützten Ausgaben. 
  - MINIMUM_ROWS:  
    Mindestens erforderliche Zeilen pro Batch, die in eine Ausgabe geschrieben werden. Für Parquet erstellt jeder Batch eine neue Datei. 
    - Gilt für alle unterstützten Ausgaben. 
  - MAXIMUM_TIME:  
    Maximale Wartezeit in Minuten pro Batch. Nach Ablauf dieser Zeit wird der Batch auch dann in die Ausgabe geschrieben, wenn die Anforderung der Mindestanzahl von Zeilen nicht erfüllt ist. 
    - Gilt für alle unterstützten Ausgaben. 
  - PARTITION_KEY_COLUMN:  
    Die Spalte, die für den Partitionsschlüssel verwendet wird. 
    - Zur künftigen Verwendung reserviert. Gilt nicht für Azure SQL Edge.
  - PROPERTY_COLUMNS:  
    Eine durch Trennzeichen getrennte Liste der Namen von Ausgabespalten, die (sofern angegeben) als benutzerdefinierte Eigenschaften an Nachrichten angefügt werden.  
    - Zur künftigen Verwendung reserviert. Gilt nicht für Azure SQL Edge. 
  - SYSTEM_PROPERTY_COLUMNS:  
    Eine JSON-formatierte Sammlung von Name-Wert-Paaren von Systemeigenschaftennamen und Ausgabespalten, die für Service Bus-Nachrichten aufgefüllt werden sollen. Beispiel: { "MessageId": "column1", "PartitionKey": "column2"} 
    - Zur künftigen Verwendung reserviert. Gilt nicht für Azure SQL Edge. 
  - PARTITION_KEY:  
    Der Name der Ausgabespalte mit dem Partitionsschlüssel. Der Partitionsschlüssel ist ein eindeutiger Bezeichner für die Partition innerhalb einer Tabelle, die den ersten Teil des Primärschlüssels einer Entität bildet. Dabei handelt es sich um einen Zeichenfolgenwert, der bis zu 1 KB groß sein kann. 
    - Zur künftigen Verwendung reserviert. Gilt nicht für Azure SQL Edge.
  - ROW_KEY:  
    Der Name der Ausgabespalte, die den Zeilenschlüssel enthält. Der Zeilenschlüssel ist ein eindeutiger Bezeichner für eine Entität innerhalb einer Partition. Er bildet den zweiten Teil des Primärschlüssels einer Entität. Der Zeilenschlüssel ist ein Zeichenfolgenwert, der bis zu 1 KB groß sein kann. 
    - Zur künftigen Verwendung reserviert. Gilt nicht für Azure SQL Edge.
  - BATCH_SIZE:  
    Dieser Wert stellt die Anzahl der Transaktionen für den Tabellenspeicher dar. Dabei kann der Höchstwert bis zu 100 Datensätze erreichen. Für Azure Functions entspricht dies der Batchgröße in Bytes, die pro Aufruf an die Funktion gesendet werden. Der Standardwert ist 256 KB. 
    - Zur künftigen Verwendung reserviert. Gilt nicht für Azure SQL Edge. 
  - MAXIMUM_BATCH_COUNT:  
    Die maximal zulässige Anzahl von Ereignissen, die pro Aufruf für Azure Functions an die Funktion gesendet werden können. Der Standardwert lautet 100. Für SQL-Datenbank stellt dieser Wert die maximale Anzahl von Datensätzen dar, die mit jeder Transaktion zum Masseneinfügen gesendet wird. Der Standardwert ist 10.000. 
    - Gilt für alle SQL-basierten Ausgaben. 
  - STAGING_AREA: EXTERNAL DATA SOURCE-Objekt für Blob Storage. Der Stagingbereich für die Datenerfassung mit hohem Durchsatz in Azure Synapse Analytics. 
    - Zur künftigen Verwendung reserviert. Gilt nicht für Azure SQL Edge.


## <a name="examples"></a>Beispiele

### <a name="example-1---edgehub"></a>Beispiel 1: EdgeHub

Typ: Eingabe oder Ausgabe<br>

Syntax:

```sql
CREATE EXTERNAL DATA SOURCE MyEdgeHub 
WITH  
(      
  LOCATION = 'edgehub://'       
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat  
WITH (  
   FORMAT_TYPE = 'JSON', 
); 
 
CREATE EXTERNAL STREAM Stream_A  
WITH    
(   
   DATA_SOURCE = MyEdgeHub, 
   FILE_FORMAT = myFileFormat, 
   LOCATION = '<mytopicname>', 
   OUTPUT_OPTIONS =   
     'REJECT_TYPE: Drop'
);
```

### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>Beispiel 2: Azure SQL-Datenbank, Azure SQL Edge, SQL Server

Typ: Output<br>

Syntax:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>', 
SECRET = '<password>'; 
 
-- Azure SQL Database 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = '<my_server_name>.database.windows.net', 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <sqlserver://<ipaddress>,<port>', 
  CREDENTIAL = SQLCredName 
); 

CREATE EXTERNAL STREAM Stream_A 
WITH   
(  
    DATA_SOURCE = MyTargetSQLTable, 
    LOCATION = '<DatabaseName>.<SchemaName>.<TableName>' ,
   --Note: If table is contained in the database, <TableName> should be sufficient 
    OUTPUT_OPTIONS =  
      'REJECT_TYPE: Drop'
); 
```

### <a name="example-3---kafka"></a>Beispiel 3: Kafka

Typ: Eingabe<br>

Syntax:
```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets 
WITH 
( 
  --The location maps to KafkaBootstrapServer 
  LOCATION = 'kafka://<kafkaserver>:<ipaddress>', 
  CREDENTIAL = kafkaCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = JSON, 
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
); 

CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyKafka_tweets, 
    LOCATION = '<KafkaTopicName>', 
    FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      'PARTITIONS: 5'
); 
```

## <a name="see-also"></a>Weitere Informationen

- [DROP EXTERNAL STREAM (Transact-SQL)](drop-external-stream-transact-sql.md)
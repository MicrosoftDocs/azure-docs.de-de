---
title: Erstellen eines T-SQL-Streamingauftrags in Azure SQL Edge
description: Erfahren Sie mehr über das Erstellen von Stream Analytics-Aufträgen in Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: 4d420bf45cd705f518df0d52929a331d23537184
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395171"
---
# <a name="create-a-data-streaming-job-in-azure-sql-edge"></a>Erstellen eines Datenstreamingauftrags in Azure SQL Edge 

In diesem Artikel wird das Erstellen eines T-SQL-Streamingauftrags in Azure SQL Edge beschrieben. Sie erstellen die externen Streameingabe- und -Ausgabeobjekte und definieren dann die Abfrage des Streamingauftrags im Rahmen der Erstellung des Streamingauftrags.

## <a name="configure-the-external-stream-input-and-output-objects"></a>Konfigurieren der externen Streameingabe- und -ausgabeobjekte

T-SQL-Streaming verwendet die Funktionen der externen Datenquelle von SQL Server, um die Datenquellen zu definieren, die den externen Streameingaben und -ausgaben des Streamingauftrags zugeordnet sind. Verwenden Sie die folgenden T-SQL-Befehle, um ein externes Streameingabe- oder -ausgabeobjekt zu erstellen:

- [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql)

- [CREATE EXTERNAL DATA SOURCE (Transact-SQL)](/sql/t-sql/statements/create-external-data-source-transact-sql)

- [CREATE EXTERNAL STREAM (Transact-SQL)](#example-create-an-external-stream-object-to-azure-sql-database)

Wenn Azure SQL Edge, SQL Server oder Azure SQL-Datenbank als Ausgabestream verwendet wird, benötigen Sie außerdem den Befehl [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](/sql/t-sql/statements/create-database-scoped-credential-transact-sql). Dieser T-SQL-Befehl definiert die Anmeldeinformationen für den Zugriff auf die Datenbank.

### <a name="supported-input-and-output-stream-data-sources"></a>Unterstützte Datenquellen für Eingabe- und Ausgabestreams

Azure SQL Edge unterstützt derzeit nur die folgenden Datenquellen als Streameingaben und -ausgaben.

| Datenquellentyp | Eingabe | Output | BESCHREIBUNG |
|------------------|-------|--------|------------------|
| Azure IoT Edge Hub | J | J | Datenquelle zum Lesen und Schreiben von Streamingdaten in einen Azure IoT Edge Hub. Weitere Informationen finden Sie unter [IoT Edge Hub](../iot-edge/iot-edge-runtime.md#iot-edge-hub).|
| SQL-Datenbank | N | J | Datenquellenverbindung, um Streamingdaten in SQL-Datenbank zu schreiben. Die Datenbank kann eine lokale Datenbank in Azure SQL Edge oder eine Remotedatenbank in SQL Server oder Azure SQL-Datenbank sein.|
| Kafka | J | N | Datenquelle zum Lesen von Streamingdaten aus einem Kafka-Thema. Dieser Adapter ist zurzeit nur für Intel- oder AMD-Versionen von Azure SQL Edge verfügbar. Er ist für die ARM64-Version von Azure SQL Edge nicht verfügbar.|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>Beispiel: Erstellen eines externen Streameingabe-/-ausgabeobjekts für Azure IoT Edge Hub

Im folgenden Beispiel wird ein externes Streamobjekt für Azure IoT Edge Hub erstellt. Zum Erstellen einer externen Streameingabe-/-ausgabedatenquelle für Azure IoT Edge Hub müssen Sie zunächst ein externes Dateiformat, um das Layout der Daten zu verstehen, die gelesen oder geschrieben werden.

1. Erstellen eines externen Dateiformats vom Typ JSON.

    ```sql
    Create External file format InputFileFormat
    WITH 
    (  
       format_type = JSON,
    )
    go
    ```

2. Erstellen Sie eine externe Datenquelle für den Azure IoT Edge Hub. Das folgende T-SQL-Skript erstellt eine Datenquellenverbindung mit einem IOT Edge Hub, der auf demselben Docker-Host wie Azure SQL Edge ausgeführt wird.

    ```sql
    CREATE EXTERNAL DATA SOURCE EdgeHubInput 
    WITH 
    (
        LOCATION = 'edgehub://'
    )
    go
    ```

3. Erstellen Sie das externe Streamobjekt für den Azure IoT Edge Hub. Das folgende T-SQL-Skript erstellt ein Streamobjekt für den IoT Edge Hub. Im Fall eines IoT Edge Hub-Streamobjekts ist der LOCATION-Parameter der Name des IoT Edge Hub-Themas oder -Kanals für Lese- bzw. Schreibvorgänge.

    ```sql
    CREATE EXTERNAL STREAM MyTempSensors 
    WITH 
    (
        DATA_SOURCE = EdgeHubInput,
        FILE_FORMAT = InputFileFormat,
        LOCATION = N'TemperatureSensors',
        INPUT_OPTIONS = N'',
        OUTPUT_OPTIONS = N''
    );
    go
    ```

### <a name="example-create-an-external-stream-object-to-azure-sql-database"></a>Beispiel: Erstellen eines externen Streamobjekts für Azure SQL-Datenbank

Im folgenden Beispiel wird ein externes Streamobjekt für die lokale Datenbank in Azure SQL Edge erstellt. 

1. Erstellen Sie einen Hauptschlüssel in der Datenbank. Dies ist erforderlich, um das Geheimnis für die Anmeldeinformationen zu verschlüsseln.

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. Erstellen Sie datenbankweit gültige Anmeldeinformationen für den Zugriff auf die SQL Server-Quelle. Das folgende Beispiel erstellt Anmeldeinformationen für die externe Datenquelle mit IDENTITY = username und SECRET = password.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. Erstellen Sie mit CREATE EXTERNAL DATA SOURCE eine externe Datenquelle. Im folgenden Beispiel:

    * Wird eine externe Datenquelle mit dem Namen *LocalSQLOutput* erstellt.
    * Wird die externe Datenquellen (LOCATION = '<vendor>://<server>[:<port>]') identifiziert. Im Beispiel wird auf eine lokale Instanz von Azure SQL Edge verwiesen.
    * Werden die zuvor erstellten Anmeldeinformationen verwendet.

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput 
    WITH 
    (
        LOCATION = 'sqlserver://tcp:.,1433',
        CREDENTIAL = SQLCredential
    )
    go
    ```

4. Erstellen Sie das externe Streamobjekt. Im folgenden Beispiel wird ein externes Streamobjekt erstellt, das auf eine Tabelle *dbo.TemperatureMeasurements* in der Datenbank *MySQLDatabase* verweist.

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurements 
    WITH 
    (
        DATA_SOURCE = LocalSQLOutput,
        LOCATION = N'MySQLDatabase.dbo.TemperatureMeasurements',
        INPUT_OPTIONS = N'',
        OUTPUT_OPTIONS = N''
    );
    ```

### <a name="example-create-an-external-stream-object-for-kafka"></a>Beispiel: Erstellen eines externen Streamobjekts für Kafka

Im folgenden Beispiel wird ein externes Streamobjekt für die lokale Datenbank in Azure SQL Edge erstellt. In diesem Beispiel wird davon ausgegangen, dass der Kafka-Server für den anonymen Zugriff konfiguriert ist. 

1. Erstellen Sie mit CREATE EXTERNAL DATA SOURCE eine externe Datenquelle. Im folgenden Beispiel:

    ```sql
    Create EXTERNAL DATA SOURCE [KafkaInput] 
    With
    (
        LOCATION = N'kafka://<kafka_bootstrap_server_name_ip>:<port_number>'
    )
    GO
    ```
2. Erstellen Sie ein externes Dateiformat für die Kafka-Eingabe. Im folgenden Beispiel wurde ein JSON-Dateiformat mit GZIP-Komprimierung erstellt: 

   ```sql
   CREATE EXTERNAL FILE FORMAT JsonGzipped  
    WITH 
    (  
        FORMAT_TYPE = JSON , 
        DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' 
    )
   ```
    
3. Erstellen Sie das externe Streamobjekt. Im folgenden Beispiel wird ein externes Streamobjekt erstellt, das auf das Kafka-Thema `*TemperatureMeasurement*` verweist:

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurement 
    WITH 
    (  
        DATA_SOURCE = KafkaInput, 
        FILE_FORMAT = JsonGzipped,
        LOCATION = 'TemperatureMeasurement',     
        INPUT_OPTIONS = 'PARTITIONS: 10' 
    ); 
    ```

## <a name="create-the-streaming-job-and-the-streaming-queries"></a>Erstellen des Streamingauftrags und der Streamingabfragen

Verwenden Sie die gespeicherte Systemprozedur `sys.sp_create_streaming_job`, um die Streamingabfragen zu definieren und den Streamingauftrag zu erstellen. Die gespeicherte Prozedur `sp_create_streaming_job` nimmt die folgenden Parameter an:

- `job_name`: Der Name des Streamingauftrags. Die Namen von Streamingaufträgen sind in der gesamten Instanz eindeutig.
- `statement`: Auf der [Stream Analytics-Abfragesprache](/stream-analytics-query/stream-analytics-query-language-reference) basierende Streamingabfrageanweisungen.

Im folgenden Beispiel wird ein einfacher Streamingauftrag mit einer Streamingabfrage erstellt. Diese Abfrage liest die Eingaben aus dem IoT Edge Hub und schreibt in `dbo.TemperatureMeasurements` in der Datenbank.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

Im folgenden Beispiel wird ein komplexerer Streamingauftrag mit mehreren verschiedenen Abfragen erstellt. Diese Abfragen enthalten eine Abfrage, die die integrierte `AnomalyDetection_ChangePoint`-Funktion verwendet, um Anomalien in den Temperaturdaten zu identifizieren.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob2', @statement=
N' Select * INTO TemperatureMeasurements1 from MyEdgeHubInput1

Select * Into TemperatureMeasurements2 from MyEdgeHubInput2

Select * Into TemperatureMeasurements3 from MyEdgeHubInput3

SELECT
Timestamp as [Time],
[Temperature] As [Temperature],
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''Score'') as ChangePointScore,
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''IsAnomaly'') as IsChangePointAnomaly
INTO TemperatureAnomalies FROM MyEdgeHubInput2;
'
go
```

## <a name="start-stop-drop-and-monitor-streaming-jobs"></a>Starten, Beenden, Löschen und Überwachen von Streamingaufträgen

Um einen Streamingauftrag in Azure SQL Edge zu starten, führen Sie die gespeicherte Prozedur `sys.sp_start_streaming_job` aus. Die gespeicherte Prozedur erfordert, dass der Streamingauftrag als Eingabe gestartet wird.

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

Führen Sie die gespeicherte Prozedur `sys.sp_stop_streaming_job` aus, um einen Streamingauftrag zu verhindern. Die gespeicherte Prozedur erfordert, dass der Streamingauftrag als Eingabe beendet wird.

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

Um einen Streamingauftrag zu entfernen (oder zu löschen), führen Sie die gespeicherte Prozedur `sys.sp_drop_streaming_job` aus. Die gespeicherte Prozedur erfordert, dass der Streamingauftrag als Eingabe gelöscht wird.

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

Führen Sie die gespeicherte Prozedur `sys.sp_get_streaming_job` aus, um den aktuellen Status eines Streamingauftrags abzurufen. Die gespeicherte Prozedur erfordert, dass der Streamingauftrag als Eingabe gelöscht wird. Sie gibt den Namen und den aktuellen Status des Streamingauftrags aus.

```sql
exec sys.sp_get_streaming_job @name=N'StreamingJob1'
        WITH RESULT SETS
(
       (
       name nvarchar(256),
       status nvarchar(256),
       error nvarchar(256)
       )
)
```

Der Streamingauftrag kann einen der folgenden Zustände aufweisen:

| Status | BESCHREIBUNG |
|--------| ------------|
| Erstellt | Der Streamingauftrag wurde erstellt, aber noch nicht gestartet. |
| Wird gestartet | Der Streamingauftrag befindet sich in der Startphase. |
| Idle | Der Streamingauftrag wird ausgeführt, es sind aber keine Eingaben für die Verarbeitung vorhanden. |
| Verarbeitung | Der Streamingauftrag wird ausgeführt und verarbeitet Eingaben. Dieser Zustand zeigt einen fehlerfreien Status für den Streamingauftrag an. |
| Heruntergestuft | Der Streamingauftrag wird ausgeführt, aber bei der Eingabeverarbeitung sind einige nicht schwerwiegende Fehler aufgetreten. Der Eingabeauftrag wird weiterhin ausgeführt, löscht jedoch Eingaben, bei denen Fehler auftreten. |
| Beendet | Der Streamingauftrag wurde beendet. |
| Fehler | Der Streamingauftrag ist fehlgeschlagen. Dies ist im Allgemeinen ein Hinweis auf einen schwerwiegenden Fehler während der Verarbeitung. |

## <a name="next-steps"></a>Nächste Schritte

- [Anzeigen von Metadaten für Streamingaufträge in Azure SQL Edge](streaming-catalog-views.md) 
- [Erstellen eines externen Streams](create-external-stream-transact-sql.md)
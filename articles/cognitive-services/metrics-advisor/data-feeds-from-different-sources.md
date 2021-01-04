---
title: Hinzufügen von Datenfeeds aus unterschiedlichen Quellen zu Metrics Advisor
titleSuffix: Azure Cognitive Services
description: Hinzufügen unterschiedlicher Datenfeeds zu Metrics Advisor
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: mbullwin
ms.openlocfilehash: c4d1d23da5fd9678cc5b9477ddeed0daf4f5ac36
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348618"
---
# <a name="add-data-feeds-from-different-data-sources-to-metrics-advisor"></a>Hinzufügen von Datenfeeds aus unterschiedlichen Datenquellen zu Metrics Advisor

Verwenden Sie diesen Artikel, um die Einstellungen und Anforderungen zum Herstellen einer Verbindung von verschiedenen Arten von Datenquellen mit Metrics Advisor zu ermitteln. Lesen Sie unbedingt, wie das [Onboarding Ihrer Daten](how-tos/onboard-your-data.md) funktioniert, um sich mit den wichtigsten Konzepten für die Verwendung Ihrer Daten mit Metrics Advisor vertraut zu machen. 

## <a name="supported-authentication-types"></a>Unterstützte Authentifizierungstypen

| Authentifizierungstypen | BESCHREIBUNG |
| ---------------------|-------------|
|**Grundlegend** | Sie müssen in der Lage sein, grundlegende Parameter für den Zugriff auf Datenquellen bereitzustellen. Beispiel: eine Verbindungszeichenfolge oder einen Schlüssel. Datenfeedadministratoren können diese Anmeldeinformationen anzeigen. |
| **AzureManagedIdentity** | [Verwaltete Identitäten](../../active-directory/managed-identities-azure-resources/overview.md) für Azure-Ressourcen ist eine Funktion von Azure Active Directory. Sie stellt für Azure-Dienste eine automatisch verwaltete Identität in Azure AD bereit. Sie können die Identität für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt.|
| **AzureSQLConnectionString**| Speichern Sie die AzureSQL-Verbindungszeichenfolge als **credential entity** (Anmeldeinformationsentität) in Metrics Advisor, und verwenden Sie sie beim Onboarding von Metrikdaten direkt. Nur Administratoren der Anmeldeinformationsentität können diese Anmeldeinformationen anzeigen, aber sie ermöglicht autorisierten anzeigenden Benutzern das Erstellen von Datenfeeds, ohne dass sie die Details der Anmeldeinformationen kennen müssen. |
| **DataLakeGen2SharedKey**| Speichern Sie den Data Lake-Kontoschlüssel als **credential entity** (Anmeldeinformationsentität) in Metrics Advisor, und verwenden Sie sie direkt bei jedem Onboarding von Metrikdaten. Nur Administratoren der Anmeldeinformationsentität können diese Anmeldeinformationen anzeigen, aber sie ermöglicht autorisierten anzeigenden Benutzern das Erstellen von Datenfeeds, ohne dass sie die Details der Anmeldeinformationen kennen müssen.|
| **Dienstprinzipal**| Speichern Sie den Dienstprinzipal als **credential entity** (Anmeldeinformationsentität) in Metrics Advisor, und verwenden Sie sie beim Onboarding von Metrikdaten direkt. Nur Administratoren der Anmeldeinformationsentität können die Anmeldeinformationen anzeigen, aber sie ermöglicht autorisierten anzeigenden Benutzern das Erstellen von Datenfeeds, ohne dass sie die Details der Anmeldeinformationen kennen müssen.|
| **Dienstprinzipal vom Schlüsseltresor**|Speichern Sie den Dienstprinzipal als **credential entity** (Anmeldeinformationsentität) in einem Schlüsseltresor in Metrics Advisor, und verwenden Sie sie beim Onboarding von Metrikdaten direkt. Nur Administratoren einer **Anmeldeinformationsentität** können diese Anmeldeinformationen anzeigen, aber sie ermöglicht autorisierten anzeigenden Benutzern das Erstellen von Datenfeeds, ohne dass sie die Details der Anmeldeinformationen kennen müssen. |

## <a name="data-sources-supported-and-corresponding-authentication-types"></a>Unterstützte Datenquellen und zugehörige Authentifizierungstypen


| Datenquellen | Authentifizierungstypen |
|-------------| ---------------------|
|[**Azure Application Insights**](#appinsights)|  Basic |
|[**Azure Blob Storage (JSON)**](#blob) | Basic<br>ManagedIdentity|
|[**Azure Cosmos DB (SQL)**](#cosmosdb) | Basic |
|[**Azure Data Explorer (Kusto)**](#kusto) | Basic<br>ManagedIdentity|
|[**Azure Data Lake Storage Gen2**](#adl) | Basic<br>DataLakeGen2SharedKey<br>Dienstprinzipal<br>Dienstprinzipal vom Schlüsseltresor<br> |
|[**Azure SQL-Datenbank/SQL Server**](#sql) | Basic<br>ManagedIdentity<br>Dienstprinzipal<br>Dienstprinzipal vom Schlüsseltresor<br>AzureSQLConnectionString
|[**Azure Table Storage**](#table) | Basic | 
|[**ElasticSearch**](#es) | Basic |
|[**HTTP-Anforderung**](#http) | Basic | 
|[**InfluxDB (InfluxQL)**](#influxdb) | Basic |
|[**MongoDB**](#mongodb) | Basic |
|[**MySQL**](#mysql) | Basic |
|[**PostgreSQL**](#pgsql)| Basic|

Erstellen Sie eine **Credential Entity** (Anmeldeinformationsentität), und verwenden Sie sie zum Authentifizieren bei Ihren Datenquellen. In den folgenden Abschnitten werden die Parameter angegeben, die für die *Standardauthentifizierung* erforderlich sind. 

## <a name="span-idappinsightsazure-application-insightsspan"></a><span id="appinsights">Azure Application Insights</span>

* **Anwendungs-ID**: Dies kann verwendet werden, um die Anwendung identifizieren, wenn die Application Insights-API verwendet wird. Gehen Sie zum Abrufen der Anwendungs-ID folgendermaßen vor:

    1. Klicken Sie in Ihrer Application Insights-Ressource auf API-Zugriff.

    2. Kopieren Sie die generierte Anwendungs-ID in das Feld **Anwendungs-ID** in Metrics Advisor. 
    
    Weitere Informationen finden Sie in der [Dokumentation zu Azure Bot Service](/azure/bot-service/bot-service-resources-app-insights-keys#application-id).

* **API-Schlüssel**: API-Schlüssel werden von Anwendungen außerhalb des Browsers für den Zugriff auf diese Ressource verwendet. Gehen Sie wie folgt vor, um den API-Schlüssel abzurufen:

    1. Klicken Sie in Ihrer Application Insights-Ressource auf API-Zugriff.

    2. Klicken Sie auf API-Schlüssel erstellen.

    3. Geben Sie eine kurze Beschreibung ein, überprüfen Sie die Option „Telementriedaten lesen“, und klicken Sie auf die Schaltfläche „Schlüssel generieren“.

    4. Kopieren Sie den API-Schlüssel in das Feld **API-Schlüssel** in Metrics Advisor.

* **Query** (Abfrage): Azure Application Insights-Protokolle basieren auf Azure Data Explorer, und Azure Monitor-Protokollabfragen verwenden eine Version der gleichen Kusto-Abfragesprache. Die [Dokumentation zur Kusto-Abfragesprache](/azure/data-explorer/kusto/query/) enthält alle Informationen zur Sprache und ist die wichtigste Ressource zum Schreiben einer Abfrage für Application Insights. 


## <a name="span-idblobazure-blob-storage-jsonspan"></a><span id="blob">Azure Blob Storage (JSON)</span>

* **Verbindungszeichenfolge**: Informationen zum Abrufen dieser Zeichenfolge finden Sie im Artikel zur Azure Blob Storage-[Verbindungszeichenfolge](../../storage/common/storage-configure-connection-string.md#configure-a-connection-string-for-an-azure-storage-account).

* **Container**: Metrics Advisor erwartet Zeitreihendaten, die als Blobdateien (ein Blob pro Zeitstempel) unter einem einzelnen Container gespeichert sind. Dies ist das Feld für den Containernamen.

* **Blobvorlage**: Dies ist die Vorlage der Blobdateinamen. Beispiel: `/%Y/%m/X_%Y-%m-%d-%h-%M.json`. Die folgenden Parameter werden unterstützt:
  * `%Y` ist das Jahr, formatiert als `yyyy`.
  * `%m` ist der Monat, formatiert als `MM`.
  * `%d` ist der Tag, formatiert als `dd`.
  * `%h` ist die Stunde, formatiert als `HH`.
  * `%M` ist die Minute, formatiert als `mm`.

* **JSON-Formatversion**: Definiert das Datenschema in den JSON-Dateien. Derzeit unterstützt Metrics Advisor zwei Versionen:
  
  * v1 (Standardwert)

      Nur die Metriken *Name* und *Wert* werden akzeptiert. Zum Beispiel:
    
      ``` JSON
      {"count":11, "revenue":1.23}
      ```

  * V2

      Die Metriken *Dimensionen* und *Zeitstempel* werden ebenfalls akzeptiert. Zum Beispiel:
      
      ``` JSON
      [
        {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
        {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
      ]
      ```

Pro JSON-Datei ist nur ein Zeitstempel zulässig. 

## <a name="span-idcosmosdbazure-cosmos-db-sqlspan"></a><span id="cosmosdb">Azure Cosmos DB (SQL)</span>

* **Verbindungszeichenfolge**: Die Verbindungszeichenfolge für den Zugriff auf Azure Cosmos DB. Diese finden Sie in der Cosmos DB-Ressource unter **Schlüssel**. 
* **Datenbank**: Die Datenbank, die abgefragt werden soll. Diese finden Sie auf der Seite **Durchsuchen** im Abschnitt **Container**.
* **Sammlungs-ID**: Die Sammlungs-ID, die abgefragt werden soll. Diese finden Sie auf der Seite **Durchsuchen** im Abschnitt **Container**.
* **SQL-Abfrage**: Eine SQL-Abfrage zum Abrufen und Formulieren von Daten in mehrdimensionalen Zeitreihendaten. Verwenden Sie die Variablen `@StartTime` und `@EndTime` in der Abfrage. Sie müssen wie folgt formatiert werden: `yyyy-MM-dd HH:mm:ss`.

    Beispielabfrage:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    Beispielabfrage für einen Datenslice vom 12.12.2019:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idkustoazure-data-explorer-kustospan"></a><span id="kusto">Azure Data Explorer (Kusto)</span>

* **Verbindungszeichenfolge**: Metrics Advisor unterstützt den Zugriff auf Azure Data Explorer (Kusto) über die Azure AD-Anwendungsauthentifizierung. Sie müssen eine Azure AD-Anwendung erstellen und registrieren und diese anschließend für den Zugriff auf eine Azure Data Explorer-Datenbank autorisieren. Informationen zum Abrufen der Verbindungszeichenfolge finden Sie in der Dokumentation zu [Azure Data Explorer](/azure/data-explorer/provision-azure-ad-app).

* **Query** (Abfrage): Unter [Kusto-Abfragesprache](/azure/data-explorer/kusto/query) finden Sie Informationen zum Abrufen und Formulieren von Daten in mehrdimensionalen Zeitreihendaten. Verwenden Sie die Variablen `@StartTime` und `@EndTime` in der Abfrage. Sie müssen wie folgt formatiert werden: `yyyy-MM-dd HH:mm:ss`.

## <a name="span-idadlazure-data-lake-storage-gen2span"></a><span id="adl">Azure Data Lake Storage Gen2</span>

* **Kontoname:** Der Name Ihres Azure Data Lake Storage Gen2-Kontos. Diesen finden Sie in der Azure Storage-Kontoressource (Azure Data Lake Storage Gen2) unter **Zugriffsschlüssel**.

* **Kontoschlüssel**: Geben Sie den Kontonamen für den Zugriff auf Ihr Azure Data Lake Storage Gen2-Konto an. Diesen finden Sie in der Azure Storage-Kontoressource (Azure Data Lake Storage Gen2) in der Einstellung für den **Zugriffsschlüssel**.

* **Dateisystemname (Container)** : Metrics Advisor erwartet Zeitreihendaten, die als Blobdateien (ein Blob pro Zeitstempel) unter einem einzelnen Container gespeichert sind. Dies ist das Feld für den Containernamen. Diesen finden Sie in Ihrer Azure Storage-Kontoinstanz (Azure Data Lake Storage Gen2). Klicken Sie im Abschnitt „Blobdienst“ auf „Container“.

* **Verzeichnisvorlage**: Dies ist die Verzeichnisvorlage der Blobdatei. Beispiel: */%Y/%m/%d*. Die folgenden Parameter werden unterstützt:
  * `%Y` ist das Jahr, formatiert als `yyyy`.
  * `%m` ist der Monat, formatiert als `MM`.
  * `%d` ist der Tag, formatiert als `dd`.
  * `%h` ist die Stunde, formatiert als `HH`.
  * `%M` ist die Minute, formatiert als `mm`.

* **Dateivorlage**: Dies ist die Dateivorlage der Blobdatei. Beispiel: *X_%Y-%m-%d-%h-%M.json*. Die folgenden Parameter werden unterstützt:
  * `%Y` ist das Jahr, formatiert als `yyyy`.
  * `%m` ist der Monat, formatiert als `MM`.
  * `%d` ist der Tag, formatiert als `dd`.
  * `%h` ist die Stunde, formatiert als `HH`.
  * `%M` ist die Minute, formatiert als `mm`.

Derzeit unterstützt Metrics Advisor das Datenschema in den JSON-Dateien wie folgt. Zum Beispiel:

``` JSON
[
  {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
  {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
]
```
<!--
## <span id="eventhubs">Azure Event Hubs</span>

* **Connection String**: This can be found in 'Shared access policies' in your Event Hubs instance. Also for the 'EntityPath', it could be found by clicking into your Event Hubs instance and clicking at 'Event Hubs' in 'Entities' blade. Items that listed can be input as EntityPath. 

* **Consumer Group**: A [consumer group](../../event-hubs/event-hubs-features.md#consumer-groups) is a view (state, position, or offset) of an entire event hub.
Event Hubs use the latest offset of a consumer group to consume (subscribe from) the data from data source. Therefore a dedicated consumer group should be created for one data feed in your Metrics Advisor instance.

* **Timestamp**: Metrics Advisor uses the Event Hubs timestamp as the event timestamp if the user data source does not contain a timestamp field.
The timestamp field must match one of these two formats:

    * "YYYY-MM-DDTHH:MM:SSZ" format;

    * Number of seconds or milliseconds from the epoch of 1970-01-01T00:00:00Z.

    No matter which timestamp field it left aligns to granularity.For example, if timestamp is "2019-01-01T00:03:00Z", granularity is 5 minutes, then Metrics Advisor aligns the timestamp to "2019-01-01T00:00:00Z". If the event timestamp is "2019-01-01T00:10:00Z",  Metrics Advisor uses the timestamp directly without any alignment.
-->
## <a name="span-idsqlazure-sql-database--sql-serverspan"></a><span id="sql">Azure SQL-Datenbank | SQL Server</span>

* **Verbindungszeichenfolge**: Metrics Advisor akzeptiert eine [Verbindungszeichenfolge im ADO.NET-Stil](/dotnet/framework/data/adonet/connection-string-syntax) für eine SQL Server-Datenquelle.

    Beispiel-Verbindungszeichenfolge:

    ```
    Data Source=db-server.database.windows.net:[port];initial catalog=[database];User ID=[username];Password=[password];Connection Timeout=10ms;
    ```

* **Query** (Abfrage): Eine SQL-Abfrage zum Abrufen und Formulieren von Daten in mehrdimensionalen Zeitreihendaten. Sie können eine `@StartTime`-Variable in der Abfrage verwenden, um den erwarteten Metrikwert abzurufen.

  * `@StartTime`: ein datetime-Wert im Format `yyyy-MM-dd HH:mm:ss`

    Beispielabfrage:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    Tatsächlich ausgeführte Abfrage für einen Datenslice vom 12.12.2019:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idtableazure-table-storagespan"></a><span id="table">Azure Table Storage</span>

* **Verbindungszeichenfolge**: Informationen zum Abrufen der Verbindungszeichenfolge aus Azure Table Storage finden Sie unter [Anzeigen und Kopieren einer Verbindungszeichenfolge](../../storage/common/storage-account-keys-manage.md?tabs=azure-portal&toc=%2fazure%2fstorage%2ftables%2ftoc.json#view-account-access-keys).

* **Tabellenname**: Geben Sie eine Tabelle an, die abgefragt werden soll. Dies finden Sie in Ihrer Azure Storage-Kontoinstanz. Klicken Sie im Abschnitt **Tabellendienst** auf **Tabellen**.

* **Abfrage**: Sie können `@StartTime` in der Abfrage verwenden. `@StartTime` wird durch eine Zeichenfolge im Format JJJJ-MM-TTThh:mm:ss im Skript ersetzt.

    ``` mssql
    let StartDateTime = datetime(@StartTime); let EndDateTime = StartDateTime + 1d; 
    SampleTable | where Timestamp >= StartDateTime and Timestamp < EndDateTime | project Timestamp, Market, RPM
    ```

## <a name="span-ideselasticsearchspan"></a><span id="es">Elasticsearch</span>

* **Host**: Geben Sie den Masterhost für den Elasticsearch-Cluster an.
* **Port**: Geben Sie den Masterport für den Elasticsearch-Cluster an.
* **Autorisierungsheader**: Geben Sie den Wert des Autorisierungsheaders des Elasticsearch-Clusters an.
* **Abfrage**: Geben Sie die Abfrage an, um Daten abzurufen. Der Platzhalter @StartTime wird unterstützt. (z. B. wenn die Daten von 2020-06-21T00:00:00Z erfasst werden, @StartTime = 2020-06-21T00:00:00).

## <a name="span-idhttphttp-requestspan"></a><span id="http">HTTP-Anforderung</span>

* **Anforderungs-URL:** : Eine HTTP-URL, die einen JSON-Wert zurückgeben kann. Die Platzhalter %Y, %m, %d, %h, %M werden unterstützt: % y = Jahr im Format JJJJ, % m = Monat im Format MM, % d = Tag im Format TT, %h = Stunde im Format HH, % m = Minute im Format MM. Beispiel: `http://microsoft.com/ProjectA/%Y/%m/X_%Y-%m-%d-%h-%M`.
* **HTTP-Methode der Anforderung**: Verwenden Sie GET oder POST.
* **Anforderungsheader:** Kann die Standardauthentifizierung hinzufügen. 
* **Anforderungsnutzlast**: Nur die JSON-Payload wird unterstützt. Der Platzhalter @StartTime wird in der Payload unterstützt. Die Antwort sollte das folgende JSON-Format aufweisen: [{"timestamp": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23}, {"timestamp": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}].(z. B. wenn Daten vom 2020-06-21T00:00:00Z erfasst werden, @StartTime = 2020-06-21T00:00:00.0000000+00:00)

## <a name="span-idinfluxdbinfluxdb-influxqlspan"></a><span id="influxdb">InfluxDB (InfluxQL)</span>

* **Verbindungszeichenfolge**: Die Verbindungszeichenfolge für den Zugriff auf InfluxDB
* **Datenbank**: Die Datenbank, die abgefragt werden soll
* **Query** (Abfrage): Eine Abfrage zum Abrufen und Formulieren von Daten in mehrdimensionalen Zeitreihendaten für die Erfassung
* **Benutzername**: Dies ist für die Authentifizierung optional. 
* **Kennwort**: Dies ist für die Authentifizierung optional. 

## <a name="span-idmongodbmongodbspan"></a><span id="mongodb">MongoDB</span>

* **Verbindungszeichenfolge**: Die Verbindungszeichenfolge für den Zugriff auf MongoDB
* **Datenbank**: Die Datenbank, die abgefragt werden soll
* **Befehl**: Ein Befehl zum Abrufen und Formulieren von Daten in mehrdimensionalen Zeitreihendaten für die Erfassung

## <a name="span-idmysqlmysqlspan"></a><span id="mysql">MySQL</span>

* **Verbindungszeichenfolge**: Die Verbindungszeichenfolge für den Zugriff auf Ihre MySQL-DB
* **Query** (Abfrage): Eine Abfrage zum Abrufen und Formulieren von Daten in mehrdimensionalen Zeitreihendaten für die Erfassung

## <a name="span-idpgsqlpostgresqlspan"></a><span id="pgsql">PostgreSQL</span>

* **Verbindungszeichenfolge**: Die Verbindungszeichenfolge für den Zugriff auf Ihre PostgreSQL-DB
* **Query** (Abfrage): Eine Abfrage zum Abrufen und Formulieren von Daten in mehrdimensionalen Zeitreihendaten für die Erfassung

## <a name="next-steps"></a>Nächste Schritte

* Während Sie darauf warten, dass die Metrikdaten im System erfasst werden, lesen Sie mehr über das [Verwalten von Datenfeedkonfigurationen](how-tos/manage-data-feeds.md).
* Wenn die Metrikdaten erfasst wurden, können Sie [Metriken konfigurieren und die Erkennungskonfiguration optimieren](how-tos/configure-metrics.md).
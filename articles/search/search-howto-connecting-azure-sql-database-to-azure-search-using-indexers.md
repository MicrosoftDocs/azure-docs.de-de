---
title: Durchsuchen von Azure SQL-Daten
titleSuffix: Azure Cognitive Search
description: Hier erfahren Sie, wie Sie Daten aus Azure SQL-Datenbank oder SQL Managed Instance mithilfe von Indexern für die Volltextsuche in Azure Cognitive Search importieren. Dieser Artikel behandelt Verbindungen, Indexerkonfiguration und Datenerfassung.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.openlocfilehash: 04e4801c26b0ac8ef91af0b028d9dc2bb9a3cd1c
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358625"
---
# <a name="connect-to-and-index-azure-sql-content-using-an-azure-cognitive-search-indexer"></a>Verbinden und Indizieren von Azure SQL-Inhalten mithilfe eines Azure Cognitive Search-Indexers

Bevor Sie einen [Index der kognitiven Azure-Suche](search-what-is-an-index.md) abfragen können, müssen Sie ihn mit Daten auffüllen. Wenn sich die Daten in Azure SQL-Datenbank oder SQL Managed Instance befinden, können Sie mit einem **Azure Cognitive Search-Indexer für Azure SQL-Datenbank** (oder kurz **Azure SQL-Indexer** ) den Indizierungsprozess automatisieren. Dadurch müssen Sie weniger Code schreiben und sich um weniger Infrastruktur kümmern.

In diesem Artikel erfahren Sie mehr über die Funktionsweise von [Indexern](search-indexer-overview.md) sowie über Funktionen, die nur für Azure SQL-Datenbank oder SQL Managed Instance verfügbar sind (wie etwa die integrierte Änderungsnachverfolgung). 

Zusätzlich zu Azure SQL-Datenbank und SQL Managed Instance umfasst Azure Cognitive Search Indexer für [Azure Cosmos DB](search-howto-index-cosmosdb.md), [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) und [Azure Table Storage](search-howto-indexing-azure-tables.md). Wenn Sie Unterstützung für andere Datenquellen wünschen, geben Sie dazu im [Feedbackforum für die kognitive Azure-Suche](https://feedback.azure.com/forums/263029-azure-search/) Feedback.

## <a name="indexers-and-data-sources"></a>Indexer und Datenquellen

Eine **Datenquelle** gibt an, welche Daten indiziert werden müssen. Sie legt außerdem die Anmeldeinformationen für den Datenzugriff sowie die Richtlinien fest, mit denen Änderungen an den Daten effizient identifiziert werden können (z.B. neue, geänderte oder gelöschte Zeilen). Die Datenquelle wird als unabhängige Ressource definiert, sodass sie von mehreren Indexern verwendet werden kann.

Ein **Indexer** ist eine Ressource, die eine einzelne Datenquelle mit einem Zielsuchindex verbindet. Ein Indexer kann folgendermaßen verwendet werden:

* Eine einmalige Kopie der Daten zum Auffüllen eines Indexes ausführen.
* Einen Index mit Änderungen an der Datenquelle nach einem Zeitplan aktualisieren.
* Ausführung bei Bedarf, um den Index je nach Notwendigkeit zu aktualisieren.

Ein einzelner Indexer kann nur eine Tabelle oder Sicht verwenden, aber Sie können mehrere Indexer erstellen, wenn Sie mehrere Suchindizes auffüllen möchten. Weitere Informationen zu Konzepten finden Sie unter [Indexer-Vorgänge: Typischer Workflow](/rest/api/searchservice/Indexer-operations#typical-workflow).

Sie können einen Azure SQL-Indexer wie folgt einrichten und konfigurieren:

* Mit dem Datenimport-Assistenten im [Azure-Portal](https://portal.azure.com)
* [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexer) für die kognitive Azure-Suche
* [REST-API](/rest/api/searchservice/indexer-operations) für die kognitive Azure-Suche

In diesem Artikel wird die REST-API verwendet, um **Indexer** und **Datenquellen** zu erstellen.

## <a name="when-to-use-azure-sql-indexer"></a>Gründe für die Verwendung von Azure SQL-Indexern
Abhängig von verschiedenen Faktoren, die mit den Daten zusammenhängen, kann die Verwendung von Azure SQL-Indexern angebracht oder nicht angebracht sein. Wenn Ihre Daten die folgenden Anforderungen erfüllen, können Sie Azure SQL-Indexer verwenden.

| Kriterien | Details |
|----------|---------|
| Die Daten stammen aus einer einzelnen Tabelle oder Sicht. | Wenn die Daten auf mehrere Tabellen verteilt sind, können Sie eine Sicht der Daten erstellen. Bei Verwendung einer Sicht können Sie jedoch nicht die in SQL Server integrierte Änderungserkennung nutzen, um einen Index mit inkrementellen Änderungen zu aktualisieren. Weitere Informationen finden Sie unter [Erfassen geänderter und gelöschter Zeilen](#CaptureChangedRows) weiter unten. |
| Datentypen sind kompatibel | In einem Index der kognitiven Azure-Suche werden die meisten, aber nicht alle SQL-Typen unterstützt. Eine Liste finden Sie unter [Zuordnen von Datentypen](#TypeMapping). |
| Synchronisierung von Daten in Echtzeit ist nicht erforderlich | Ein Indexer kann die Tabelle höchstens alle fünf Minuten erneut indizieren. Wenn sich Ihre Daten häufig ändern und die Änderungen innerhalb von Sekunden oder weniger Minuten im Index widergespiegelt werden müssen, sollten Sie die [REST-API](/rest/api/searchservice/AddUpdate-or-Delete-Documents) oder das [.NET SDK](./search-get-started-dotnet.md) verwenden, um aktualisierte Zeilen direkt zu übertragen. |
| Inkrementelle Indizierung ist möglich | Wenn Sie bei einem großen Dataset den Indexer nach einem Zeitplan ausführen möchten, muss es in der kognitiven Azure-Suche möglich sein, effizient neue, geänderte oder gelöschte Zeilen zu identifizieren. Eine nicht inkrementelle Indizierung ist nur zulässig, wenn Sie bei Bedarf indizieren (nicht gemäß Zeitplan) oder wenn Sie weniger als 100.000 Zeilen indizieren. Weitere Informationen finden Sie unter [Erfassen geänderter und gelöschter Zeilen](#CaptureChangedRows) weiter unten. |

> [!NOTE] 
> Die kognitive Azure-Suche unterstützt nur die SQL Server-Authentifizierung. Sollten Sie die Azure Active Directory-Kennwortauthentifizierung benötigen, stimmen Sie bitte für [diesen UserVoice-Vorschlag](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica) ab.

## <a name="create-an-azure-sql-indexer"></a>Erstellen eines Azure SQL-Indexers

1. Erstellen Sie die Datenquelle:

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   Die Verbindungszeichenfolge kann einem der folgenden Formate vorliegen:
    1. Sie können die Verbindungszeichenfolge mit der Option `ADO.NET connection string` aus dem [Azure-Portal](https://portal.azure.com) abrufen.
    1. Eine Verbindungszeichenfolge für eine verwaltete Identität, die keinen Kontoschlüssel enthält, im folgenden Format: `Initial Catalog|Database=<your database name>;ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Sql/servers/<your SQL Server name>/;Connection Timeout=connection timeout length;`. Um diese Verbindungszeichenfolge zu verwenden, befolgen Sie die Anweisungen unter [Einrichten einer Indexerverbindung mit Azure SQL-Datenbank mithilfe einer verwalteten Identität](search-howto-managed-identities-sql.md).

2. Erstellen Sie den Zielindex der kognitiven Azure-Suche, wenn Sie bislang noch über keinen verfügen. Sie können einen Index über das [Portal](https://portal.azure.com) oder mit der [API zur Indexerstellung](/rest/api/searchservice/Create-Index) erstellen. Stellen Sie sicher, dass das Schema des Zielindex mit dem Schema der Quelltabelle kompatibel ist. Informationen dazu finden Sie unter [Zuordnung zwischen SQL-Datentypen und Datentypen der kognitiven Azure-Suche](#TypeMapping).

3. Erstellen Sie den Indexer, indem Sie ihm einen Namen geben und einen Verweis auf die Datenquelle und den Zielindex hinzufügen:

   ```
    POST https://myservice.search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
   ```

Ein auf diese Weise erstellter Indexer verfügt über keinen Zeitplan. Er wird automatisch einmal ausgeführt, wenn er erstellt wird. Sie können ihn jederzeit mithilfe der Anforderung **run indexer** erneut ausführen:

```
    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2020-06-30
    api-key: admin-key
```

Sie können einige Aspekte des Indexerverhaltens anpassen, z.B. die Batchgröße, und wie viele Dokumente übersprungen werden können, bevor bei einer Indexerausführung ein Fehler auftritt. Weitere Informationen finden Sie unter [Create Indexer API](/rest/api/searchservice/Create-Indexer) (API zum Erstellen eines Indexers).

Möglicherweise müssen Sie Azure Services erlauben, eine Verbindung mit der Datenbank herzustellen. Anleitungen dazu finden Sie unter [Verbinden von Azure](../azure-sql/database/firewall-configure.md) .

Verwenden Sie zum Überwachen des Indexerstatus und Ausführungsverlaufs (Anzahl der indizierten Elemente, Fehler usw.) die Anforderung **indexer status** :

```
    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2020-06-30
    api-key: admin-key
```

Die Antwort sollte etwa wie folgt aussehen:

```
    {
        "\@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null
            },
            ... earlier history items
        ]
    }
```

Der Ausführungsverlauf enthält bis zu 50 der zuletzt abgeschlossenen Ausführungen. Diese sind in umgekehrter chronologischer Reihenfolge sortiert (somit wird die neueste Ausführung als Erstes in der Antwort aufgelistet).
Weitere Informationen zur Antwort finden Sie unter [Abrufen des Indexerstatus](/rest/api/searchservice/get-indexer-status).

## <a name="run-indexers-on-a-schedule"></a>Ausführen von Indexern nach einem Zeitplan
Sie können den Indexer auch so konfigurieren, dass er regelmäßig nach einem Zeitplan ausgeführt wird. Dazu fügen Sie die **schedule** -Eigenschaft beim Erstellen oder Aktualisieren des Indexers hinzu. Das folgende Beispiel zeigt eine PUT-Anforderung den Indexer, um den zu aktualisieren:

```
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }
```

Der Parameter **interval** ist erforderlich. Das Intervall bezieht sich auf den Zeitraum zwischen dem Start von zwei aufeinander folgenden Indexerausführungen. Das kleinste zulässige Intervall beträgt 5 Minuten. Das längste ist ein Tag. Es muss als XSD-Wert „dayTimeDuration“ formatiert sein (eine eingeschränkte Teilmenge eines [ISO 8601-Zeitwerts](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)). Das Muster hierfür lautet wie folgt: `P(nD)(T(nH)(nM))`. Beispiele: `PT15M` = alle 15 Minuten, `PT2H` = alle 2 Stunden.

Weitere Informationen zum Definieren von Indexerzeitplänen finden Sie unter [Festlegen eines Zeitplans für Indexer in der kognitiven Azure-Suche](search-howto-schedule-indexers.md).

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Erfassen neuer, geänderter und gelöschter Zeilen

In der kognitiven Azure-Suche wird eine **inkrementelle Indizierung** verwendet, um zu vermeiden, dass die gesamte Tabelle oder Ansicht jedes Mal neu indiziert wird, wenn ein Indexer ausgeführt wird. Die kognitive Azure-Suche umfasst zwei Richtlinien zur Erkennung von Änderungen, um die inkrementelle Indizierung zu unterstützen. 

### <a name="sql-integrated-change-tracking-policy"></a>Richtlinie für die integrierte SQL-Änderungsnachverfolgung
Wenn die SQL-Datenbank die [Änderungsnachverfolgung](/sql/relational-databases/track-changes/about-change-tracking-sql-server)unterstützt, wird empfohlen, die **Richtlinie für die integrierte SQL-Änderungsnachverfolgung** zu verwenden. Dies ist die effizienteste Richtlinie. Außerdem sorgt sie dafür, dass in der kognitiven Azure-Suche gelöschte Zeilen identifiziert werden, ohne dass Sie eine explizite „Vorläufig löschen“-Spalte in der Tabelle angeben müssen.

#### <a name="requirements"></a>Requirements (Anforderungen) 

+ Anforderungen für die Datenbankversion:
  * SQL Server 2012 SP3 und höher bei Verwendung von SQL Server auf Azure-VMs.
  * Azure SQL-Datenbank oder SQL Managed Instance
+ Nur Tabellen (keine Sichten). 
+ Aktiveren Sie in der Datenbank [die Änderungsnachverfolgung](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) für die Tabelle. 
+ Kein zusammengesetzter Primärschlüssel (ein Primärschlüssel mit mehr als einer Spalte) für die Tabelle.  

#### <a name="usage"></a>Verwendung

Um diese Richtlinie zu verwenden, erstellen oder aktualisieren Sie die Datenquelle wie folgt:

```
    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }
```

Wenn Sie die Richtlinie für die integrierte SQL-Änderungsnachverfolgung verwenden, geben Sie keine separate Richtlinie für das Erkennen gelöschter Daten an. Die Identifizierung gelöschter Zeilen wird von der Richtlinie bereits unterstützt. Damit die Löschvorgänge „automatisch“ erkannt werden, muss der Dokumentschlüssel im Suchindex allerdings mit dem Primärschlüssel in der SQL-Tabelle übereinstimmen. 

> [!NOTE]  
> Wenn Sie [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql) verwenden, um eine große Anzahl von Zeilen aus einer SQL-Tabelle zu entfernen, muss der Indexer [zurückgesetzt](/rest/api/searchservice/reset-indexer) werden, um den Änderungsverfolgungszustand zurückzusetzen und Zeilenlöschungen aufzunehmen.

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Richtlinie zum Erkennen von Änderungen mit oberem Grenzwert

Diese Richtlinie zur Erkennung von Änderungen basiert auf einer Spalte mit oberem Grenzwert, die Version oder Uhrzeit der letzten Aktualisierung einer Zeile erfasst. Bei Verwendung einer Sicht müssen Sie eine Richtlinie mit oberem Grenzwert einsetzen. Die Spalte mit dem oberen Grenzwert muss die folgenden Anforderungen erfüllen.

#### <a name="requirements"></a>Requirements (Anforderungen) 

* Alle Einfügungen geben einen Wert für die Spalte an.
* Alle Updates für ein Element ändern auch den Wert der Spalte.
* Der Wert dieser Spalte wird bei jeder Einfügung oder Aktualisierung erhöht.
* Abfragen mit den folgenden WHERE- und ORDER BY-Klauseln können effizient ausgeführt werden: `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> Es wird dringend empfohlen, den Datentyp [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) für die Spalte mit dem oberen Grenzwert zu verwenden. Wenn ein anderer Datentyp verwendet wird, ist nicht sichergestellt, dass bei der Änderungsnachverfolgung alle Änderungen erfasst werden, wenn Transaktionen gleichzeitig mit einer Indexerabfrage ausgeführt werden. Bei Verwendung von **rowversion** in einer Konfiguration mit schreibgeschützten Replikaten müssen Sie den Indexer auf das primäre Replikat verweisen. Nur ein primäres Replikat kann für Szenarien zur Datensynchronisierung verwendet werden.

#### <a name="usage"></a>Verwendung

Um eine Richtlinie mit oberem Grenzwert anzuwenden, erstellen oder aktualisieren Sie die Datenquelle wie folgt:

```
    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a rowversion or last_updated column name]"
      }
    }
```

> [!WARNING]
> Wenn die Quelltabelle keinen Index für die Spalte mit dem oberen Grenzwert enthält, kann das Zeitlimit für die vom SQL-Indexer verwendeten Abfragen überschritten werden. Insbesondere die Klausel `ORDER BY [High Water Mark Column]` erfordert für die effiziente Ausführung einen Index, wenn die Tabelle viele Zeilen enthält.
>
>

<a name="convertHighWaterMarkToRowVersion"></a>

##### <a name="converthighwatermarktorowversion"></a>convertHighWaterMarkToRowVersion

Wenn Sie für die Spalte mit dem oberen Grenzwert den Datentyp [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) verwenden, sollten Sie für den Indexer die Konfigurationseinstellung `convertHighWaterMarkToRowVersion` in Betracht ziehen. Durch `convertHighWaterMarkToRowVersion` werden die folgenden beiden Aktionen ausgeführt:

* Für die Spalte mit dem oberen Grenzwert wird der Datentyp „rowversion“ für die SQL-Abfrage des Indexers verwendet. Die Verwendung des richtigen Datentyps verbessert die Abfrageleistung des Indexers.
* Vor Ausführen der Indexerabfrage wird vom rowversion-Wert die Zahl 1 subtrahiert. Sichten mit 1:n-Joins können Zeilen mit doppelten rowversion-Werten enthalten. Durch Subtraktion von 1 wird sichergestellt, dass die Indexerabfrage diese Zeilen nicht übergeht.

Wenn Sie diese Funktion aktivieren möchten, erstellen oder aktualisieren Sie den Indexer mit der folgenden Konfiguration:

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "convertHighWaterMarkToRowVersion" : true } }
    }
```

<a name="queryTimeout"></a>

##### <a name="querytimeout"></a>queryTimeout

Wenn Timeoutfehler auftreten, können Sie mithilfe der Indexer-Konfigurationseinstellung `queryTimeout` das Abfragetimeout auf einen höheren Wert als das Standardtimeout von 5 Minuten festlegen. Um das Timeout z.B. auf 10 Minuten festzulegen, erstellen oder aktualisieren Sie den Indexer mit der folgenden Konfiguration:

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }
```

<a name="disableOrderByHighWaterMarkColumn"></a>

##### <a name="disableorderbyhighwatermarkcolumn"></a>disableOrderByHighWaterMarkColumn

Sie können auch die Klausel `ORDER BY [High Water Mark Column]` deaktivieren. Dies wird jedoch nicht empfohlen, da bei Unterbrechung der Indexer-Ausführung aufgrund eines Fehlers der Indexer alle Zeilen erneut verarbeiten muss, wenn er später ausgeführt wird, auch wenn zum Zeitpunkt der Unterbrechung bereits nahezu alle Zeilen verarbeitet wurden. Zum Deaktivieren der Klausel `ORDER BY` verwenden Sie die Einstellung `disableOrderByHighWaterMarkColumn` in der Indexer-Definition:  

```
    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }
```

### <a name="soft-delete-column-deletion-detection-policy"></a>Richtlinie zum Erkennen von Löschungen anhand der Spalte „Vorläufig löschen“
Wenn Zeilen aus der Quelltabelle gelöscht werden, sollten Sie diese Zeilen auch aus dem Suchindex löschen. Wenn Sie die Richtlinie für die integrierte SQL-Änderungsnachverfolgung verwenden, müssen Sie sich nicht darum kümmern, weil es automatisch geschieht. Die Richtlinie zum Erkennen von Änderungen mit oberem Grenzwert hilft Ihnen allerdings nicht beim Löschen von Zeilen. Vorgehensweise

Wenn Zeilen physisch aus der Tabelle entfernt werden, gibt es in der kognitiven Azure-Suche keine Möglichkeit, das Vorhandensein von Datensätzen abzuleiten, die nicht mehr vorhanden sind.  Mit der Methode des „vorläufigen Löschens“ können Sie jedoch Zeilen logisch löschen, ohne sie aus der Tabelle zu entfernen. Fügen Sie dazu der Tabelle eine Spalte hinzu, um Zeilen unter Verwendung dieser Spalte anzuzeigen und als gelöscht zu markieren.

Wenn Sie die Methode des "vorläufigen Löschens" verwenden, können Sie die Richtlinie zum Erkennen von Löschungen anhand der "Vorläufig löschen"-Spalte wie folgt beim Erstellen oder Aktualisieren der Datenquelle angeben:

```
    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }
```

**softDeleteMarkerValue** muss eine Zeichenfolge sein. Verwenden Sie die Zeichenfolgendarstellung des tatsächlichen Werts. Wenn Sie z.B. über eine „integer“-Spalte verfügen, in der gelöschte Zeilen durch den Wert 1 gekennzeichnet sind, verwenden Sie `"1"`. Wenn Sie über eine BIT-Spalte verfügen, in der gelöschte Zeilen durch den booleschen Wert TRUE gekennzeichnet sind, verwenden Sie das Zeichenfolgenliteral `True` oder `true` (die Groß- und Kleinschreibung spielt keine Rolle).

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-cognitive-search-data-types"></a>Zuordnung zwischen SQL-Datentypen und Datentypen der kognitiven Azure-Suche
| SQL-Datentyp | Zulässige Ziel-Index-Feldtypen | Notizen |
| --- | --- | --- |
| bit |Edm.Boolean, Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| BIGINT |Edm.Int64, Edm.String | |
| real, float |Edm.Double, Edm.String | |
| Smallmoney, money decimal numeric |Edm.String |Die kognitive Azure-Suche unterstützt nicht die Konvertierung von Dezimaltypen in Edm.Double-Typen, da es hierbei zu Genauigkeitsverlusten kommt. |
| char, nchar, varchar, nvarchar |Edm.String<br/>Collection(Edm.String) |Eine SQL-Zeichenfolge kann zum Auffüllen eines Collection(Edm.String)-Felds verwendet werden, wenn die Zeichenfolge ein JSON-Array von Zeichenfolgen darstellt: `["red", "white", "blue"]` |
| smalldatetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset, Edm.String | |
| uniqueidentifer |Edm.String | |
| geography |Edm.GeographyPoint |Es werden nur Geography-Instanzen vom Typ POINT mit SRID 4326 (Standard) unterstützt. |
| rowversion |– |rowversion-Spalten können nicht im Suchindex gespeichert werden, sie können jedoch für die Änderungsnachverfolgung verwendet werden. |
| time, timespan, binary, varbinary, image, xml, geometry, CLR types |– |Nicht unterstützt |

## <a name="configuration-settings"></a>Konfigurationseinstellungen
SQL-Indexer verfügt über mehrere Konfigurationseinstellungen:

| Einstellung | Datentyp | Zweck | Standardwert |
| --- | --- | --- | --- |
| queryTimeout |Zeichenfolge |Legt das Timeout für die Ausführung einer SQL-Abfrage fest |5 Minuten („00:05:00“) |
| disableOrderByHighWaterMarkColumn |bool |Bewirkt, dass bei der von der Richtlinie zum Erkennen von Änderungen mit oberem Grenzwert verwendeten SQL-Abfrage die ORDER BY-Klausel weggelassen wird. Siehe [Richtlinie zum Erkennen von Änderungen mit oberem Grenzwert](#HighWaterMarkPolicy). |false |

Diese Einstellungen werden im `parameters.configuration`-Objekt in der Indexer-Definition verwendet. Um das Abfragetimeout z.B. auf 10 Minuten festzulegen, erstellen oder aktualisieren Sie den Indexer mit der folgenden Konfiguration:

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }
```

## <a name="faq"></a>Häufig gestellte Fragen

**F: Kann ich Azure SQL-Indexer mit SQL-Datenbanken verwenden, die auf IaaS-VMs in Azure ausgeführt werden?**

Ja. Sie müssen jedoch die Verbindung des Suchdiensts mit der Datenbank ermöglichen. Weitere Informationen finden Sie unter [Konfigurieren einer Verbindung eines Indexers der kognitiven Azure-Suche mit SQL Server auf einer Azure-VM](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).

**F: Kann ich Azure SQL-Indexer mit SQL-Datenbanken verwenden, die lokal ausgeführt werden?**

Nicht direkt. Weder empfehlen noch unterstützen wir eine direkte Verbindung, da Sie die Datenbanken dann für den Internetdatenverkehr öffnen müssten. Kunden waren bei diesem Szenario mit Brückentechnologien wie Azure Data Factory erfolgreich. Weitere Informationen finden Sie unter [Push-Übertragung von Daten in einen Index der kognitiven Azure-Suche mithilfe von Azure Data Factory](../data-factory/v1/data-factory-azure-search-connector.md).

**F: Kann ich Azure SQL-Indexer mit anderen Datenbanken als SQL Server in IaaS auf Azure verwenden?**

Nein. Wir unterstützen dieses Szenario nicht, da wir den Indexer mit keinen anderen Datenbanken als SQL Server getestet haben.  

**F: Kann ich mehrere Indexer erstellen, die nach einem Zeitplan ausgeführt werden?**

Ja. Allerdings kann zu einem gegebenen Zeitpunkt nur ein Indexer auf einem Knoten ausgeführt werden. Wenn mehrere Indexer gleichzeitig ausgeführt werden sollen, sollten Sie den Suchdienst skalieren und mehrere Sucheinheiten einsetzen.

**F: Wirkt sich die Ausführung eines Indexers auf meine Abfrageworkloads aus?**

Ja. Indexer werden auf einem der Knoten im Suchdienst ausgeführt, und die Ressourcen dieses Knotens werden für die Indizierung und das Bearbeiten des Datenverkehrs für Abfragen und andere API-Anforderungen gemeinsam genutzt. Wenn Sie intensive Indizierungs- und Abfrageworkloads ausführen und viele 503-Fehler oder zunehmend längere Antwortzeiten auftreten, sollten Sie Ihren [Suchdienst skalieren](search-capacity-planning.md).

**F: Kann ich ein sekundäres Replikat in einem [Failovercluster](../azure-sql/database/auto-failover-group-overview.md) als Datenquelle verwenden?**

Das ist unterschiedlich. Für die vollständige Indizierung einer Tabelle oder Sicht können Sie ein sekundäres Replikat verwenden. 

Für die inkrementelle Indizierung unterstützt die kognitive Azure-Suche zwei Richtlinien zur Erkennung von Änderungen: die integrierte SQL-Änderungsnachverfolgung und den oberem Grenzwert.

Bei schreibgeschützten Replikaten wird die integrierte Änderungsnachverfolgung von SQL-Datenbank nicht unterstützt. Aus diesem Grund müssen Sie die Richtlinie mit oberem Grenzwert verwenden. 

Unsere Standardempfehlung ist den Datentyp „rowversion“ für die Spalte mit dem oberen Grenzwert zu verwenden. Allerdings muss für „rowversion“ die Funktion `MIN_ACTIVE_ROWVERSION` verwendet werden, die für schreibgeschützte Replikate nicht unterstützt wird. Aus diesem Grund müssen Sie den Indexer auf ein primäres Replikat verweisen, wenn Sie „rowversion“ verwenden.

Wenn Sie versuchen, „rowversion“ bei einem schreibgeschützten Replikat zu verwenden, wird die folgende Fehlermeldung angezeigt: 

„Die Verwendung einer rowversion-Spalte für die Änderungsnachverfolgung wird für sekundäre (schreibgeschützte) Verfügbarkeitsreplikate nicht unterstützt. Aktualisieren Sie die Datenquelle, und geben Sie eine Verbindung zum primären Verfügbarkeitsreplikat an. Die Datenbankeigenschaft „Updateability“ ist derzeit auf READ_ONLY festgelegt.“

**F: Kann ich eine alternative Spalte mit einem anderen Typ als „rowversion“ für die Änderungsnachverfolgung mit oberem Grenzwert verwenden?**

Dies ist nicht empfehlenswert. Nur **rowversion** ermöglicht eine zuverlässige Datensynchronisierung. Je nach Anwendungslogik kann es jedoch möglicherweise in folgendem Fall machbar sein:

+ Sie können sicherstellen, dass bei der Ausführung des Indexers keine ausstehenden Transaktionen für die Tabelle vorhanden sind, die indiziert wird (z. B. alle Tabellenaktualisierungen werden als ein Batch nach einem Zeitplan ausgeführt, und der Zeitplan des Indexers der kognitiven Azure-Suche ist so festgelegt, dass er sich nicht mit dem Zeitplan der Tabellenaktualisierung überschneidet).  

+ Sie führen in regelmäßigen Abständen eine vollständige erneute Indizierung aus, damit alle fehlenden Zeilen übernommen werden.
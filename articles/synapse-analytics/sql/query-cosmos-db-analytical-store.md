---
title: Abfragen von Azure Cosmos DB-Daten mithilfe eines serverlosen SQL-Pools in Azure Synapse Link (Vorschau)
description: In diesem Artikel erfahren Sie, wie Sie Azure Cosmos DB unter Verwendung eines serverlosen SQL-Pools in Azure Synapse Link (Vorschau) abfragen.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 12/04/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 22103ad580fa474f44eaf42c696d19bbbd137c8e
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095099"
---
# <a name="query-azure-cosmos-db-data-with-a-serverless-sql-pool-in-azure-synapse-link-preview"></a>Abfragen von Azure Cosmos DB-Daten mithilfe eines serverlosen SQL-Pools in Azure Synapse Link (Vorschau)

> [!IMPORTANT]
> Die Unterstützung von serverlosen SQL-Pools für Azure Synapse Link für Azure Cosmos DB befindet sich zurzeit in der Vorschau. Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Mit einem serverlosen SQL-Pool können Sie Daten in Ihren Azure Cosmos DB-Containern, die für [Azure Synapse Link](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) aktiviert sind, nahezu in Echtzeit analysieren, ohne dass sich dies auf die Leistung Ihrer Transaktionsarbeitsauslastungen auswirkt. Dieses Verfahren bietet eine vertraute T-SQL-Syntax zum Abfragen von Daten aus dem [Analysespeicher](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) sowie integrierte Konnektivität mit einer Vielzahl von BI-Tools (Business Intelligence) und Ad-hoc-Abfragetools über die T-SQL-Schnittstelle.

Zum Abfragen von Azure Cosmos DB wird die gesamte [SELECT](/sql/t-sql/queries/select-transact-sql?view=sql-server-ver15)-Oberfläche durch die [OPENROWSET](develop-openrowset.md)-Funktion unterstützt, einschließlich eines Großteils der [SQL-Funktionen und -Operatoren](overview-features.md). Mit [CREATE EXTERNAL TABLE AS SELECT](develop-tables-cetas.md#cetas-in-serverless-sql-pool) (CETAS) können Sie die Ergebnisse der Abfrage, die Daten aus Azure Cosmos DB liest, auch zusammen mit Daten in Azure Blob Storage oder Azure Data Lake Storage speichern. Das Speichern der Abfrageergebnisse aus einem serverlosen SQL-Pool in Azure Cosmos DB mithilfe von CETAS ist derzeit nicht möglich.

In diesem Artikel erfahren Sie, wie Sie eine Abfrage für einen serverlosen SQL-Pool erstellen, die Daten aus für Azure Synapse Link aktivierten Azure Cosmos DB-Containern abfragt. Danach können Sie sich in [diesem Tutorial](./tutorial-data-analyst.md) darüber informieren, wie Sie Sichten serverloser SQL-Pools über Azure Cosmos DB-Container erstellen und diese Sichten mit Power BI-Modellen verbinden.

> [!IMPORTANT]
> In diesem Tutorial wird ein Container mit einem [genau definierten Azure Cosmos DB-Schema](../../cosmos-db/analytical-store-introduction.md#schema-representation) verwendet. Das Verhalten der Abfrageoberfläche, die vom serverlosen SQL-Pool für das [Azure Cosmos DB-Schema mit vollständiger Genauigkeit](#full-fidelity-schema) bereitgestellt wird, gilt nur vorübergehend und wird sich basierend auf dem Feedback zur Vorschauversion noch ändern. Verlassen Sie sich nicht auf das Resultset-Schema der `OPENROWSET`-Funktion ohne `WITH`-Klausel, das Daten aus einem Container mit einem vollständigem Genauigkeitsschema ausliest. Der Grund ist, dass die Abfrageoberfläche unter Umständen noch geändert und an ein genau definiertes Schema angepasst wird. Sie können Ihr Feedback im [Feedbackforum für Azure Synapse Analytics](https://feedback.azure.com/forums/307516-azure-synapse-analytics) bereitstellen. Sie können Ihr Feedback auch an das [Azure Synapse Link-Produktteam](mailto:cosmosdbsynapselink@microsoft.com) senden.

## <a name="overview"></a>Übersicht

Ein serverloser SQL-Pool ermöglicht es Ihnen, den Azure Cosmos DB-Analysespeicher mithilfe der `OPENROWSET`-Funktion abzufragen. 
- `OPENROWSET` mit Inlineschlüssel. Diese Syntax kann verwendet werden, um Azure Cosmos DB-Sammlungen abzufragen, ohne Anmeldeinformationen vorbereiten zu müssen.
- `OPENROWSET`, das auf Anmeldeinformationen verweist, die den Cosmos DB-Kontoschlüssel enthalten. Diese Syntax kann verwendet werden, um Sichten für Azure Cosmos DB-Sammlungen zu erstellen.

### <a name="openrowset-with-key"></a>[OPENROWSET mit Schlüssel](#tab/openrowset-key)

Ein serverloser SQL-Pool verwendet die folgende `OPENROWSET`-Syntax, um das Abfragen und Analysieren von Daten in einem Azure Cosmos DB-Analysespeicher zu unterstützen:

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ] AS alias
```

Die Azure Cosmos DB-Verbindungszeichenfolge übergibt den Namen des Azure Cosmos DB-Kontos, den Datenbanknamen, den Hauptschlüssel des Datenbankkontos sowie optional einen Regionsnamen an die `OPENROWSET`-Funktion.

Die Verbindungszeichenfolge weist das folgende Format auf:
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

Der Name des Azure Cosmos DB-Containers wird in der `OPENROWSET`-Syntax ohne Anführungszeichen angegeben. Wenn der Containername Sonderzeichen (z. B. einen Bindestrich, „-“) enthält, sollte er in der `OPENROWSET`-Syntax mit `[]` (eckige Klammern) umschlossen werden.

### <a name="openrowset-with-credential"></a>[OPENROWSET mit Anmeldeinformationen](#tab/openrowset-credential)

Sie können die `OPENROWSET`-Syntax verwenden, die auf Anmeldeinformationen verweist:

```sql
OPENROWSET( 
       PROVIDER = 'CosmosDB',
       CONNECTION = '<Azure Cosmos DB connection string without account key>',
       OBJECT = '<Container name>',
       [ CREDENTIAL | SERVER_CREDENTIAL ] = '<credential name>'
    )  [ < with clause > ] AS alias
```

Die Azure Cosmos DB-Verbindungszeichenfolge enthält in diesem Fall keinen Schlüssel. Die Verbindungszeichenfolge weist das folgende Format auf:
```sql
'account=<database account name>;database=<database name>;region=<region name>'
```

Der Hauptschlüssel des Datenbankkontos wird in Anmeldeinformationen auf Serverebene oder in datenbankweit gültigen Anmeldeinformationen abgelegt. 

---

> [!IMPORTANT]
> Achten Sie darauf, dass Sie eine UTF-8-Datenbanksortierung (z. B. `Latin1_General_100_CI_AS_SC_UTF8`) verwenden, weil Zeichenfolgenwerte im Azure Cosmos DB-Analysespeicher als UTF-8-Text codiert werden.
> Ein Konflikt zwischen der Textcodierung in der Datei und der Sortierung kann zu unerwarteten Fehlern bei der Textkonvertierung führen.
> Die Standardsortierung der aktuellen Datenbank kann mit der T-SQL-Anweisung `alter database current collate Latin1_General_100_CI_AI_SC_UTF8` problemlos geändert werden.

> [!NOTE]
> Abfragen eines Azure Cosmos DB-Transaktionsspeichers werden im serverlosen SQL-Pool nicht unterstützt.

## <a name="sample-dataset"></a>Beispieldataset

Die Beispiele in diesem Artikel basieren auf Daten aus den Datasets [European Centre for Disease Prevention and Control (ECDC) COVID-19 Cases](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) und [COVID-19 Open Research Dataset (CORD-19), doi:10.5281/zenodo.3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/).

Auf diesen Seiten finden Sie die Lizenz und die Datenstruktur. Sie können auch Beispieldaten für die Datasets [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) und [CORD-19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) herunterladen.

Um die in diesem Artikel beschriebenen Schritte zum Abfragen von Azure Cosmos DB-Daten mit einem serverlosen SQL-Pool ausführen zu können, müssen Sie die folgenden Ressourcen erstellen:

* Ein Azure Cosmos DB-Datenbankkonto mit [aktiviertem Azure Synapse Link](../../cosmos-db/configure-synapse-link.md)
* Eine Azure Cosmos DB-Datenbank namens `covid`
* Zwei Azure Cosmos DB-Container namens `Ecdc` und `Cord19`, in die die obigen Beispieldatasets geladen wurden

Sie können die folgende Verbindungszeichenfolge zu Testzwecken verwenden: `Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==`. Beachten Sie, dass diese Verbindung keine Leistung gewährleistet, da sich dieses Konto möglicherweise in einer entfernten Region im Vergleich zu Ihrem Synapse SQL-Endpunkt befindet.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Untersuchen von Azure Cosmos DB-Daten mit automatischem Schemarückschluss

Die einfachste Möglichkeit zum Untersuchen von Daten in Azure Cosmos DB ist die Funktion des automatischen Schemarückschlusses. Wenn Sie die `OPENROWSET`-Anweisung ohne die `WITH`-Klausel verwenden, können Sie den serverlosen SQL-Pool anweisen, das Schema des Analysespeichers des Azure Cosmos DB-Containers automatisch zu erkennen (Rückschlüsse zu ziehen).

### <a name="openrowset-with-key"></a>[OPENROWSET mit Schlüssel](#tab/openrowset-key)

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc) as documents
```

### <a name="openrowset-with-credential"></a>[OPENROWSET mit Anmeldeinformationen](#tab/openrowset-credential)

```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

---

Im obigen Beispiel haben wir den serverlosen SQL-Pool angewiesen, eine Verbindung mit der `covid`-Datenbank im Azure Cosmos DB-Konto `MyCosmosDbAccount` herzustellen. Die Authentifizierung erfolgte über den Azure Cosmos DB-Schlüssel (Platzhalterwert im obigen Beispiel). Danach haben wir auf den Analysespeicher des Containers `Ecdc` in der Region `West US 2` zugegriffen. Da keine Projektion bestimmter Eigenschaften vorgenommen wurde, gibt die `OPENROWSET`-Funktion alle Eigenschaften aus den Azure Cosmos DB-Elementen zurück.

Unter der Annahme, dass die Elemente im Azure Cosmos DB-Container die Eigenschaften `date_rep`, `cases` und `geo_id` besitzen, werden die Ergebnisse dieser Abfrage in der folgenden Tabelle aufgeführt:

| date_rep | cases | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Wenn Sie Daten aus dem anderen Container in derselben Azure Cosmos DB-Datenbank untersuchen müssen, können Sie dieselbe Verbindungszeichenfolge verwenden und mit dem dritten Parameter auf den erforderlichen Container verweisen:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>Explizites Angeben des Schemas

Der automatische Schemarückschluss in `OPENROWSET` ermöglicht zwar einfache und leicht zu verwendende Abfragen, aber für Ihre Geschäftsszenarien müssen Sie möglicherweise explizit das Schema für schreibgeschützte relevante Eigenschaften aus den Azure Cosmos DB-Daten angeben.

In der `OPENROWSET`-Funktion können Sie die Eigenschaften, die aus den Daten im Container gelesen werden sollen, sowie deren Datentypen explizit angeben.

Angenommen, Sie haben einige Daten aus dem [ECDC-COVID-Dataset](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) mit der folgenden Struktur in Azure Cosmos DB importiert:

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Diese flachen JSON-Dokumente in Azure Cosmos DB können in Synapse SQL als Gruppe von Zeilen und Spalten dargestellt werden. In der `WITH`-Klausel der `OPENROWSET`-Funktion können Sie eine Teilmenge von Eigenschaften, die gelesen werden sollen, sowie die genauen Spaltentypen angeben:

### <a name="openrowset-with-key"></a>[OPENROWSET mit Schlüssel](#tab/openrowset-key)
```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```
### <a name="openrowset-with-credential"></a>[OPENROWSET mit Anmeldeinformationen](#tab/openrowset-credential)
```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```
---
Das Ergebnis dieser Abfrage könnte wie in der folgenden Tabelle aussehen:

| date_rep | cases | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Weitere Informationen zu den SQL-Typen, die für Azure Cosmos DB-Werte verwendet werden sollten, finden Sie in den [Regeln für die SQL-Typzuordnung](#azure-cosmos-db-to-sql-type-mappings) am Ende dieses Artikels.

## <a name="create-view"></a>Erstellen einer Sicht

Nachdem Sie das Schema identifiziert haben, können Sie auf Basis Ihrer Azure Cosmos DB-Daten eine Sicht vorbereiten. Sie sollten Ihren Azure Cosmos DB-Kontoschlüssel in gesonderten Anmeldeinformationen ablegen aus der `OPENROWSET`-Funktion auf diese Anmeldeinformationen verweisen. Lassen Sie Ihren Kontoschlüssel nicht in der Sichtdefinition.

```sql
CREATE CREDENTIAL MyCosmosDbAccountCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
GO
CREATE OR ALTER VIEW Ecdc
AS SELECT *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

Verwenden Sie `OPENROWSET` nicht ohne explizit definiertes Schema, da sich dies auf Ihre Leistung auswirken könnte. Stellen Sie sicher, dass Sie die kleinsten möglichen Größen für ihre Spalten (z. B. VARCHAR(100) anstelle des Standards VARCHAR(8000)) verwenden. Sie sollten eine UTF-8-Sortierung als standardmäßige Datenbanksortierung verwenden oder sie als explizite Spaltensortierung festlegen, um [UTF-8-Konvertierungsprobleme](/azure/synapse-analytics/troubleshoot/reading-utf8-text) zu vermeiden. Die Sortierung `Latin1_General_100_BIN2_UTF8` bietet die beste Leistung, wenn Sie Daten mithilfe von Zeichenfolgenspalten filtern.

## <a name="query-nested-objects-and-arrays"></a>Abfragen von geschachtelten Objekten und Arrays

Mit Azure Cosmos DB können Sie komplexere Datenmodelle in Form von geschachtelten Objekten oder Arrays darstellen. Die automatische Synchronisierungsfunktion von Azure Synapse Link für Azure Cosmos DB verwaltet standardmäßig die Schemadarstellung im Analysespeicher. Dies umfasst auch die Verarbeitung von geschachtelten Datentypen, was umfangreiche Abfragen aus dem serverlosen SQL-Pool ermöglicht.

Beispielsweise enthält das [CORD-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)-Dataset JSON-Dokumente mit folgender Struktur:

```json
{
    "paper_id": <str>,                   # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": <array of objects>    # list of author dicts, in order
        ...
     }
     ...
}
```

Die geschachtelten Objekte und Arrays in Azure Cosmos DB werden im Abfrageergebnis als JSON-Zeichenfolgen dargestellt, wenn sie von der `OPENROWSET`-Funktion gelesen werden. Sie können diese Pfade zu geschachtelten Werten in den Objekten angeben, wenn Sie die `WITH`-Klausel verwenden:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19)
WITH (  paper_id    varchar(8000),
        title        varchar(1000) '$.metadata.title',
        metadata     varchar(max),
        authors      varchar(max) '$.metadata.authors'
) AS docs;
```

Das Ergebnis dieser Abfrage könnte wie in der folgenden Tabelle aussehen:

| paper_id | title | metadata | authors |
| --- | --- | --- |
| bb11206963e831f… | Supplementary Information An eco-epidemi… | `{"title":"Supplementary Informati…` | `[{"first":"Julien","last":"Mélade","suffix":"","af…`| 
| bb1206963e831f1… | The Use of Convalescent Sera in Immune-E… | `{"title":"The Use of Convalescent…` | `[{"first":"Antonio","last":"Lavazza","suffix":"", …` |
| bb378eca9aac649… | Tylosema esculentum (Marama) Tuber and B… | `{"title":"Tylosema esculentum (Ma…` | `[{"first":"Walter","last":"Chingwaru","suffix":"",…` | 

Erfahren Sie mehr über das Analysieren von [komplexen Datentypen in Azure Synapse Link](../how-to-analyze-complex-schema.md) und von [geschachtelten Strukturen in serverlosen SQL-Pools](query-parquet-nested-types.md).

> [!IMPORTANT]
> Wenn in Ihrem Text unerwartete Zeichen angezeigt werden, z. B. `MÃƒÂ©lade` statt `Mélade`, ist die Datenbanksortierung nicht auf [UTF-8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8)-Sortierung festgelegt.
> [Ändern Sie die Sortierung der Datenbank](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) mithilfe einer SQL-Anweisung wie `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` in eine UTF-8-Sortierung.

## <a name="flatten-nested-arrays"></a>Vereinfachen geschachtelter Arrays

Azure Cosmos DB-Daten enthalten möglicherweise geschachtelte Unterarrays wie beispielsweise das Array „authors“ im [CORD-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)-Dataset:

```json
{
    "paper_id": <str>,                      # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": [                        # list of author dicts, in order
            {
                "first": <str>,
                "middle": <list of str>,
                "last": <str>,
                "suffix": <str>,
                "affiliation": <dict>,
                "email": <str>
            },
            ...
        ],
        ...
}
```

In einigen Fällen müssen Sie möglicherweise die Eigenschaften aus dem obersten Element (metadata) mit allen Elementen des Arrays (authors) verknüpfen. Ein serverloser SQL-Pool ermöglicht Ihnen das Vereinfachen von geschachtelten Strukturen durch Anwenden der `OPENJSON`-Funktion auf das geschachtelte Array:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19
    ) WITH ( title varchar(1000) '$.metadata.title',
             authors varchar(max) '$.metadata.authors' ) AS docs
      CROSS APPLY OPENJSON ( authors )
                  WITH (
                       first varchar(50),
                       last varchar(50),
                       affiliation nvarchar(max) as json
                  ) AS a
```

Das Ergebnis dieser Abfrage könnte wie in der folgenden Tabelle aussehen:

| title | authors | first | last | affiliation |
| --- | --- | --- | --- | --- |
| Supplementary Information An eco-epidemi… |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
Supplementary Information An eco-epidemi… | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | 4# |`{"laboratory":"","institution":"U…` | 
| Supplementary Information An eco-epidemi… |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Supplementary Information An eco-epidemi… |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> Wenn in Ihrem Text unerwartete Zeichen angezeigt werden, z. B. `MÃƒÂ©lade` statt `Mélade`, ist die Datenbanksortierung nicht auf [UTF-8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8)-Sortierung festgelegt. [Ändern Sie die Sortierung der Datenbank](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) mithilfe einer SQL-Anweisung wie `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` in eine UTF-8-Sortierung.

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Zuordnung von SQL-Typen zu Azure Cosmos DB

Der Azure Cosmos DB-Transaktionsspeicher ist zwar schemaagnostisch, aber der Analysespeicher ist zur Optimierung der Leistung von Analyseabfragen schematisiert. Mit der automatischen Synchronisierungsfunktion von Azure Synapse Link verwaltet Azure Cosmos DB standardmäßig die Schemadarstellung im Analysespeicher. Dies umfasst auch die Verarbeitung von geschachtelten Datentypen. Da ein serverloser SQL-Pool den Analysespeicher abfragt, müssen Sie wissen, wie Azure Cosmos DB-Eingabedatentypen SQL-Datentypen zugeordnet werden.

Azure Cosmos DB-Konten für die SQL-API (Core-API) unterstützen als JSON-Eigenschaftstypen Zahlen, Zeichenfolgen, boolesche Werte, NULL, geschachtelte Objekte und Arrays. Wenn Sie die `WITH`-Klausel in `OPENROWSET` verwenden, müssen Sie SQL-Typen auswählen, die diesen JSON-Typen entsprechen. Die folgende Tabelle zeigt die SQL-Spaltentypen, die für verschiedene Eigenschaftstypen in Azure Cosmos DB verwendet werden sollten.

| Azure Cosmos DB-Eigenschaftstyp | SQL-Spaltentyp |
| --- | --- |
| Boolean | bit |
| Integer | BIGINT |
| Decimal | float |
| String | varchar (UTF-8-Datenbanksortierung) |
| DateTime (ISO-formatierte Zeichenfolge) | varchar(30) |
| DateTime (UNIX-Zeitstempel) | BIGINT |
| Null | `any SQL type` 
| Geschachteltes Objekt oder Array | varchar(max) (UTF-8-Datenbanksortierung), als JSON-Text serialisiert |

## <a name="full-fidelity-schema"></a>Schema mit vollständiger Genauigkeit

Das Azure Cosmos DB-Schema mit vollständiger Genauigkeit zeichnet sowohl Werte als auch deren beste Übereinstimmungstypen für jede Eigenschaft in einem Container auf. Die `OPENROWSET`-Funktion in einem Container mit einem Schema mit vollständiger Genauigkeit gibt sowohl den Typ als auch den tatsächlichen Wert in jeder Zelle an. Nehmen wir an, dass die folgende Abfrage die Elemente aus einem Container mit vollständigem Genauigkeitsschema liest:

```sql
SELECT *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) as rows
```

Das Ergebnis dieser Abfrage gibt Typen und Werte zurück, die als JSON-Text formatiert sind:

| date_rep | cases | geo_id |
| --- | --- | --- |
| {"date":"2020-08-13"} | {"int32":"254"} | {"string":"RS"} |
| {"date":"2020-08-12"} | {"int32":"235"}| {"string":"RS"} |
| {"date":"2020-08-11"} | {"int32":"316"} | {"string":"RS"} |
| {"date":"2020-08-10"} | {"int32":"281"} | {"string":"RS"} |
| {"date":"2020-08-09"} | {"int32":"295"} | {"string":"RS"} |
| {"string":"2020/08/08"} | {"int32":"312"} | {"string":"RS"} |
| {"date":"2020-08-07"} | {"float64":"339.0"} | {"string":"RS"} |

Für jeden Wert können Sie den in einem Azure Cosmos DB-Containerelement identifizierten Typ sehen. Die meisten Werte für die `date_rep`-Eigenschaft enthalten `date`-Werte, einige werden jedoch in Azure Cosmos DB fälschlicherweise als Zeichenfolgen gespeichert. Das Schema mit vollständiger Genauigkeit gibt sowohl ordnungsgemäß typisierte `date`-Werte als auch falsch formatierte `string`-Werte zurück.
Die Anzahl der Fälle ist eine Information, die als `int32`-Wert gespeichert wird, allerdings gibt es einen Wert, der als Dezimalzahl eingegeben wird. Dieser Wert weist den Typ `float64` auf. Werte, die die größte `int32`-Zahl überschreiten, werden als `int64`-Typ gespeichert. Alle `geo_id`-Werte in diesem Beispiel werden als `string`-Typen gespeichert.

> [!IMPORTANT]
> Die `OPENROWSET`-Funktion ohne `WITH`-Klausel macht sowohl die Werte mit erwarteten Typen als auch die Werte mit falsch eingegebenen Typen verfügbar. Diese Funktion ist für das Durchsuchen von Daten konzipiert, nicht für die Berichterstellung. Analysieren Sie keine JSON-Werte, die von dieser Funktion zurückgegeben werden, um daraus Berichte zu erstellen. Verwenden Sie zum Erstellen Ihrer Berichte eine explizite [WITH-Klausel](#query-items-with-full-fidelity-schema). Sie müssen die Werte, die falsche Typen aufweisen, im Azure Cosmos DB-Container bereinigen, um Korrekturen im Analysespeicher mit vollständiger Genauigkeit anzuwenden.

Wenn Sie Azure Cosmos DB-Konten über die Mongo DB-API abfragen möchten, erfahren Sie unter [Was ist der Azure Cosmos DB-Analysespeicher (Vorschau)?](../../cosmos-db/analytical-store-introduction.md#analytical-schema) mehr über die Schemadarstellung mit vollständiger Genauigkeit im Analysespeicher und die dabei zu verwendenden Namen für erweiterte Eigenschaften.

### <a name="query-items-with-full-fidelity-schema"></a>Abfragen von Elementen mit einem Schema mit vollständiger Genauigkeit

Beim Abfragen eines Schemas mit vollständiger Genauigkeit müssen Sie den SQL-Typ und den erwarteten Azure Cosmos DB-Eigenschaftstyp explizit in der `WITH`-Klausel angeben. Verwenden Sie `OPENROWSET` nicht ohne `WITH`-Klausel in den Berichten, weil das Format des Resultsets in der Vorschau auf Grundlage des Feedbacks geändert werden kann.

Im folgenden Beispiel gehen wir davon aus, dass `string` der richtige Typ für die `geo_id`-Eigenschaft und `int32` der richtige Typ für die `cases`-Eigenschaft ist:

```sql
SELECT geo_id, cases = SUM(cases)
FROM OPENROWSET(
      'CosmosDB'
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string',
             cases INT '$.cases.int32'
    ) as rows
GROUP BY geo_id
```

Werte für `geo_id` und `cases`, die andere Typen aufweisen, werden als `NULL`-Werte zurückgegeben. Diese Abfrage verweist nur auf die `cases` mit dem im Ausdruck angegebenen Typ (`cases.int32`).

Wenn Sie Werte mit anderen Typen haben(`cases.int64`, `cases.float64`), die in einem Azure Cosmos DB-Container nicht bereinigt werden können, müssten Sie in einer `WITH`-Klausel explizit auf diese verweisen und die Ergebnisse kombinieren. Die folgende Abfrage aggregiert sowohl `int32`, `int64` als auch `float64`, die in der Spalte `cases` gespeichert sind:

```sql
SELECT geo_id, cases = SUM(cases_int) + SUM(cases_bigint) + SUM(cases_float)
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string', 
             cases_int INT '$.cases.int32',
             cases_bigint BIGINT '$.cases.int64',
             cases_float FLOAT '$.cases.float64'
    ) as rows
GROUP BY geo_id
```

In diesem Fall wird die Anzahl der Fälle als `int32`-, `int64`- oder `float64`-Wert gespeichert. Alle Werte müssen extrahiert werden, damit die Anzahl von Fällen pro Land berechnet werden kann.

## <a name="known-issues"></a>Bekannte Probleme

- Die Abfrageerfahrung, die der serverlose SQL-Pool für das [Azure Cosmos DB-Schema mit vollständiger Genauigkeit](#full-fidelity-schema) bereitstellt, ist ein temporäres Verhalten, das sich auf der Grundlage des Vorschaufeedbacks ändern wird. Verlassen Sie sich nicht auf das Schema, dass die `OPENROWSET`-Funktion ohne `WITH`-Klausel während der öffentlichen Vorschau bereitstellt – möglicherweise werden die Abfragefunktionen anhand von Kundenfeedback noch an einem gut definierten Schema ausgerichtet. Wenn Sie selber Feedback geben möchten, wenden Sie sich an das [Azure Synapse Link-Produktteam](mailto:cosmosdbsynapselink@microsoft.com).
- Ein serverloser SQL-Pool gibt eine Kompilierzeitwarnung zurück, wenn die `OPENROWSET`-Spaltensortierung keine UTF-8-Codierung aufweist. Sie können die Standardsortierung für alle `OPENROWSET`-Funktionen, die in der aktuellen Datenbank ausgeführt werden, ganz einfach mit der T-SQL-Anweisung `alter database current collate Latin1_General_100_CI_AS_SC_UTF8` ändern.

In der folgenden Tabelle werden mögliche Fehler und entsprechende Problembehebungsmaßnahmen aufgelistet.

| Fehler | Grundursache |
| --- | --- |
| Syntaxfehler:<br/> – Falsche Syntax in der Nähe von `Openrowset`.<br/> - `...` wird nicht als `BULK OPENROWSET`-Anbieteroption erkannt.<br/> – Falsche Syntax in der Nähe von `...`. | Mögliche Grundursachen:<br/> – „CosmosDB“ wird nicht als erster Parameter verwendet.<br/> – Verwendung eines Zeichenfolgenliterals anstelle eines Bezeichners im dritten Parameter.<br/> – Fehlende Angabe des dritten Parameters (Containername). |
| Fehler in der CosmosDB-Verbindungszeichenfolge. | – Keine Angabe von Konto, Datenbank oder Schlüssel. <br/> – Eine Verbindungszeichenfolge enthält eine nicht erkannte Option.<br/> – Am Ende einer Verbindungszeichenfolge befindet sich ein Semikolon (`;`). |
| Beim Auflösen des CosmosDB-Pfads ist der Fehler „Falscher Kontoname“ oder „Falscher Datenbankname“ aufgetreten. | Der angegebene Kontoname, Datenbankname oder Container wurde nicht gefunden, oder der Analysespeicher wurde nicht für die angegebene Sammlung aktiviert.|
| Beim Auflösen des CosmosDB-Pfads ist der Fehler „Falscher Geheimniswert“ oder „Geheimnis ist NULL oder leer“ aufgetreten. | Der Kontoschlüssel ist nicht gültig oder fehlt. |
| Die Spalte `column name` des Typs `type name` ist mit dem externen Datentyp `type name` nicht kompatibel. | Der in der `WITH`-Klausel angegebene Spaltentyp stimmt nicht mit dem Typ im Azure Cosmos DB-Container überein. Versuchen Sie, den Spaltentyp zu ändern, so wie im Abschnitt [Zuordnung von SQL-Typen zu Azure Cosmos DB](#azure-cosmos-db-to-sql-type-mappings) beschrieben, oder verwenden Sie den Typ `VARCHAR`. |
| Die Spalte enthält `NULL`-Werte in allen Zellen. | Möglicherweise falscher Spaltenname oder Pfadausdruck in der `WITH`-Klausel. Der Spaltenname (oder der Pfadausdruck nach dem Spaltentyp) in der `WITH`-Klausel muss mit einem Eigenschaftsnamen in der Azure Cosmos DB-Sammlung übereinstimmen. Beim Vergleich *wird die Groß-/Kleinschreibung beachtet*. Beispielsweise sind `productCode` und `ProductCode` unterschiedliche Eigenschaften. |

Auf der [Azure Synapse Analytics-Feedbackseite](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862) können Sie Vorschläge übermitteln und Probleme melden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Verwenden von Power BI und serverlosem Synapse SQL-Pool zum Analysieren von Azure Cosmos DB-Daten mit Synapse Link (Vorschau)](../../cosmos-db/synapse-link-power-bi.md)
- [Erstellen und Verwenden von Sichten in einem serverlosen SQL-Pool](create-use-views.md)
- [Tutorial: Analysieren von Azure Open Datasets und Visualisieren der Ergebnisse in Azure Synapse Studio mithilfe von SQL On-Demand](./tutorial-data-analyst.md)

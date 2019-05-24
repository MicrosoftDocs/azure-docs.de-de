---
title: 'Tutorial: Laden aus Azure Data Lake Storage in Azure SQL Data Warehouse | Microsoft-Dokumentation'
description: Laden Sie mit externen PolyBase-Tabellen Daten aus Azure Data Lake Storage in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load data
ms.date: 04/26/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 9d6a2c097a6040e7c005cc0feeefd59caf352441
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65850497"
---
# <a name="load-data-from-azure-data-lake-storage-to-sql-data-warehouse"></a>Laden von Daten aus Azure Data Lake Storage in SQL Data Warehouse
Laden Sie mit externen PolyBase-Tabellen Daten aus Azure Data Lake Storage in Azure SQL Data Warehouse. Obwohl Sie Ad-hoc-Abfragen für Daten ausführen können, die in Data Lake Storage gespeichert sind, sollten Sie die Daten zur Leistungsoptimierung in SQL Data Warehouse importieren.

> [!div class="checklist"]
> * Erstellen Sie die erforderlichen Datenbankobjekte zum Laden von Daten aus Data Lake Storage.
> * Stellen Sie eine Verbindung mit einem Data Lake Storage-Verzeichnis her.
> * Laden von Daten in Azure SQL Data Warehouse

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="before-you-begin"></a>Voraussetzungen
Bevor Sie mit diesem Tutorial beginnen, laden Sie die neueste Version von [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) herunter, und installieren Sie sie.

Für dieses Tutorial benötigen Sie Folgendes:

* Azure Active Directory-Anwendung für die Dienst-zu-Dienst-Authentifizierung beim Laden von Gen1. Eine Erstellungsanleitung finden Sie unter [Authentifizieren bei Data Lake Store mithilfe von Azure Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

>[!NOTE] 
> Beim Laden von Azure Data Lake Storage Gen1 benötigen Sie die Client-ID, den Schlüssel und den OAuth2.0-Token-Endpunktwert Ihrer Active Directory-Anwendung, um über SQL Data Warehouse eine Verbindung mit Ihrem Storage-Konto herstellen zu können. Ausführliche Informationen zum Ermitteln dieser Werte finden Sie unter dem oben angegebenen Link. Verwenden Sie für die Registrierung von Azure Active Directory-Apps die Anwendungs-ID als Client-ID.
> 

* Eine Instanz von Azure SQL Data Warehouse. Siehe [Schnellstart: Erstellen und Abfragen einer Azure SQL Data Warehouse-Instanz im Azure-Portal](create-data-warehouse-portal.md).

* Ein Data Lake Storage-Konto. Siehe [Erste Schritte mit Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md). 

##  <a name="create-a-credential"></a>Erstellen einer Anmeldeinformation
Für den Zugriff auf Ihr Data Lake Storage-Konto müssen Sie einen Datenbankhauptschlüssel erstellen, um die geheimen Anmeldeinformationen zu verschlüsseln, die Sie im nächsten Schritt verwenden. Anschließend erstellen Sie datenbankbezogene Anmeldeinformationen. Bei Gen1 werden die in AAD eingerichteten Dienstprinzipal-Anmeldeinformationen in den datenbankbezogenen Anmeldeinformationen gespeichert. Bei Gen2 müssen Sie den Speicherkontoschlüssel in den datenbankbezogenen Anmeldeinformationen verwenden. 

Um eine Verbindung mit Data Lake Storage Gen1 herzustellen, müssen Sie **zuerst** eine Azure Active Directory-Anwendung sowie einen Zugriffsschlüssel erstellen und der Anwendung Zugriff auf die Data Lake Storage Gen1-Ressource gewähren. Anweisungen hierzu finden Sie unter [Dienst-zu-Dienst-Authentifizierung mit Azure Data Lake Storage Gen1 mithilfe von Azure Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B (for Gen1): Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- B (for Gen2): Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;

-- It should look something like this for Gen1:
CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>Erstellen der externen Datenquelle
Verwenden Sie den Befehl [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql), um den Speicherort der Daten zu speichern. 

```sql
-- C (for Gen1): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen1 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<datalakestoregen1accountname>.azuredatalakestore.net',
    CREDENTIAL = ADLSCredential
);

-- C (for Gen2): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen2 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION='abfs://<container>@<AzureDataLake account_name>.dfs.core.windows.net', -- Please note the abfs endpoint
    CREDENTIAL = ADLSCredential
);
```

## <a name="configure-data-format"></a>Konfigurieren des Datenformats
Zum Importieren der Daten aus Data Lake Storage müssen Sie das externe Dateiformat angeben. Dieses Objekt definiert, wie die Dateien in Data Lake Storage geschrieben werden.
Eine vollständige Liste finden Sie in unserer T-SQL-Dokumentation [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql).

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store missing values as default for datatype.

CREATE EXTERNAL FILE FORMAT TextFileFormat
WITH
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE
                    )
);
```

## <a name="create-the-external-tables"></a>Erstellen von externen Tabellen
Nun, da Sie die Datenquelle und das Dateiformat festgelegt haben, können Sie die externen Tabellen erstellen. Externe Tabellen ermöglichen die Interaktion mit externen Daten. Der location-Parameter kann eine Datei oder ein Verzeichnis angeben. Wenn er ein Verzeichnis angibt, werden alle Dateien im Verzeichnis geladen.

```sql
-- D: Create an External Table
-- LOCATION: Folder under the Data Lake Storage root folder.
-- DATA_SOURCE: Specifies which Data Source Object to use.
-- FILE_FORMAT: Specifies which File Format Object to use
-- REJECT_TYPE: Specifies how you want to deal with rejected rows. Either Value or percentage of the total
-- REJECT_VALUE: Sets the Reject value based on the reject type.

-- DimProduct
CREATE EXTERNAL TABLE [dbo].[DimProduct_external] (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureDataLakeStorage
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>Überlegungen zu externen Tabellen
Eine externe Tabelle lässt sich sehr einfach erstellen, es gibt jedoch einige Feinheiten zu beachten.

Externe Tabellen sind stark typisiert. Das bedeutet, dass jede Zeile der erfassten Daten der Tabellenschemadefinition entsprechen muss.
Zeilen, die der Schemadefinition nicht entsprechen, werden nicht geladen.

Mithilfe des Optionen „REJECT_TYPE“ und „REJECT_VALUE“ können Sie definieren, wie viele Zeilen oder wie viel Prozent der Daten in der endgültigen Tabelle vorhanden sein müssen. Wird während des Ladevorgangs der Ablehnungswert erreicht, ist der Vorgang nicht erfolgreich. Die Ablehnung von Zeilen ist in den meisten Fällen auf einen Konflikt mit der Schemadefinition zurückzuführen. Wenn also beispielsweise eine Spalte fälschlicherweise mit einem int-Schema versehen wird, obwohl es sich bei den Daten in der Datei um eine Zeichenfolge handelt, können die Zeilen nicht geladen werden.

Data Lake Storage Gen1 steuert den Zugriff auf die Daten durch die rollenbasierte Zugriffssteuerung (Role Based Access Control, RBAC). Dies bedeutet, dass der Dienstprinzipal mit Leseberechtigungen für die im Speicherortparameter definierten Verzeichnisse sowie die untergeordneten Ordner der endgültigen Verzeichnisse und Dateien ausgestattet sein muss. Dadurch kann PolyBase diese Daten authentifizieren und laden. 

## <a name="load-the-data"></a>Laden der Daten
Verwenden Sie die Anweisung [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse), um Daten aus Data Lake Storage zu laden. 

CTAS erstellt eine neue Tabelle und füllt sie mit den Ergebnissen einer SELECT-Anweisung. CTAS definiert die neue Tabelle, sodass sie die gleichen Spalten und Datentypen wie die Ergebnisse der SELECT-Anweisung aufweist. Wenn Sie alle Spalten einer externen Tabelle auswählen, ist die neue Tabelle ein Replikat der Spalten und Datentypen aus der externen Tabelle.

In diesem Beispiel erstellen wir auf der Grundlage der externen Tabelle „DimProduct_external“ eine verteilte Hashtabelle namens „DimProduct“.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Optimieren der Columnstore-Komprimierung
Standardmäßig speichert SQL Data Warehouse die Tabelle als gruppierten Columnstore-Index. Nach Abschluss eines Ladevorgangs sind einige der Datenzeilen möglicherweise nicht in den Columnstore-Index komprimiert.  Es gibt zahlreiche Gründe, warum dies geschieht. Weitere Informationen finden Sie unter [Verwalten von Columnstore-Indizes](sql-data-warehouse-tables-index.md).

Um die Abfrageleistung und die Columnstore-Komprimierung nach dem Ladevorgang zu optimieren, stellen Sie die Tabelle wieder her, um den Columstore-Index zu zwingen alle Zeilen zu komprimieren.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Optimieren von Statistiken
Es empfiehlt sich, Statistiken für einzelne Spalten sofort nach dem Ladevorgang zu erstellen. Es gibt einige Optionen für Statistiken. Beim Erstellen einer Einspaltenstatistik auf jeder Spalten vergeht möglicherweise viel Zeit bis alle Statistiken wiederhergestellt werden. Wenn Sie wissen, dass bestimmte Spalten nicht in den Abfrageprädikaten erhalten sein werden, können Sie die Erstellung von Statistiken für diese Spalten überspringen.

Wenn Sie Einzelspaltenstatistiken für jede Spalte jeder Tabelle erstellen möchten, können Sie das Codebeispiel für die gespeicherte Prozedur `prc_sqldw_create_stats` im Artikel [Statistiken](sql-data-warehouse-tables-statistics.md) verwenden.

Das folgende Beispiel ist ein guter Ausgangspunkt zum Erstellen von Statistiken. Es werden Statistiken für einzelne Spalten für jede Spalte in der Dimensionstabelle erstellt sowie für jede verknüpfte Spalte in der Faktentabelle. Sie können später immer Statistiken für einzelne oder mehrere Spalten auf anderen Faktentabellenspalten hinzufügen.

## <a name="achievement-unlocked"></a>Der Weg ist frei!
Sie haben erfolgreich Daten in Azure SQL Data Warehouse geladen. Großartig!

## <a name="next-steps"></a>Nächste Schritte 
In diesem Tutorial haben Sie externe Tabellen erstellt, um die Struktur für die in Data Lake Storage Gen1 gespeicherten Daten zu definieren, und dann die PolyBase-Anweisung CREATE TABLE AS SELECT verwendet, um Daten in Ihre Instanz von Data Warehouse zu laden. 

Sie haben folgende Schritte ausgeführt:
> [!div class="checklist"]
> * Erstellen der erforderlichen Datenbankobjekte zum Laden von Daten aus Data Lake Storage Gen1.
> * Herstellen einer Verbindung mit einem Data Lake Storage Gen1-Verzeichnis.
> * Laden von Daten in Azure SQL Data Warehouse.
> 

Das Laden von Daten ist der erste Schritt auf dem Weg zu einer Data Warehouse-Lösung mit SQL Data Warehouse. Sehen Sie sich unsere Entwicklungsressourcen an.

> [!div class="nextstepaction"]
>[Lernen Sie, Tabellen in SQL Data Warehouse zu entwickeln](sql-data-warehouse-tables-overview.md).





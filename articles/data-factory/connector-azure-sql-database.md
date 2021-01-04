---
title: Kopieren und Transformieren von Daten in Azure SQL-Datenbank
description: Hier erfahren Sie, wie Sie mithilfe von Azure Data Factory Daten in und aus Azure SQL-Datenbank kopieren sowie Daten in Azure SQL-Datenbank transformieren.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/12/2020
ms.openlocfilehash: 2e5c04087a9874a01498c70eb3834606069cef13
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021893"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>Kopieren und Transformieren von Daten in Azure SQL-Datenbank mithilfe von Azure Data Factory

> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version von Azure Data Factory aus:"]
>
> - [Version 1](v1/data-factory-azure-sql-connector.md)
> - [Aktuelle Version](connector-azure-sql-database.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie Daten mithilfe der Kopieraktivität in Azure Data Factory aus und in Azure SQL-Datenbank kopieren sowie Daten mithilfe von Datenfluss in Azure SQL-Datenbank transformieren. Informationen zu Azure Data Factory finden Sie im [Einführungsartikel](introduction.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Connector für Azure SQL-Datenbank wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)-Tabelle
- [Mapping Data Flow](concepts-data-flow-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [GetMetadata-Aktivität](control-flow-get-metadata-activity.md)

Für die Kopieraktivität unterstützt dieser Azure SQL-Datenbank-Connector folgende Funktionen:

- Kopieren von Daten mit SQL-Authentifizierung und Azure Active Directory-Anwendungstokenauthentifizierung (Azure AD) mit einem Dienstprinzipal oder verwalteten Identitäten für Azure-Ressourcen
- Als Quelle das Abrufen von Daten mithilfe einer SQL-Abfrage oder gespeicherten Prozedur Sie können sich auch für das parallele Kopieren aus einer Azure SQL-Datenbank-Quelle entscheiden. Einzelheiten finden Sie im Abschnitt [Paralleles Kopieren aus SQL-Datenbank](#parallel-copy-from-sql-database).
- Als Senke das automatische Erstellen einer Zieltabelle, sofern noch nicht vorhanden, basierend auf dem Quellschema sowie das Anfügen von Daten an eine Tabelle oder das Aufrufen einer gespeicherten Prozedur mit benutzerdefinierter Logik während des Kopiervorgangs.

Wenn Sie die [serverlose Dienstebene](../azure-sql/database/serverless-tier-overview.md) von Azure SQL-Datenbank verwenden, sollten Sie beachten, dass die Aktivitätsausführung bei angehaltenem Server zu einem Fehler führt und nicht darauf gewartet wird, dass die automatische Fortsetzung bereit ist. Sie können Aktivitätswiederholungen hinzufügen oder zusätzliche Aktivitäten verketten, um sicherzustellen, dass der Server zum Zeitpunkt der tatsächlichen Ausführung aktiv ist.

>[!NOTE]
> [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) für Azure SQL-Datenbank wird derzeit von diesem Connector nicht unterstützt. Um dies zu umgehen, können Sie einen [generischen ODBC-Connector](connector-odbc.md) und einen SQL Server-ODBC-Treiber über eine selbstgehostete Integration Runtime verwenden. Weitere Informationen finden Sie im Abschnitt [Verwenden von Always Encrypted](#using-always-encrypted). 

> [!IMPORTANT]
> Wenn Sie Daten mithilfe der Azure Integration Runtime kopieren, konfigurieren Sie eine [Firewallregel auf Serverebene](../azure-sql/database/firewall-configure.md), damit Azure-Dienste Zugriff auf den Server erhalten.
> Wenn Sie Daten mithilfe einer selbstgehosteten Integration Runtime kopieren, konfigurieren Sie die Firewall, um den entsprechenden IP-Adressbereich zuzulassen. Dieser Bereich schließt die IP-Adresse des Computers ein, der für die Verbindung mit Azure SQL-Datenbank verwendet wird.

## <a name="get-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Azure Data Factory-Entitäten speziell für einen Azure SQL-Datenbank-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Diese Eigenschaften werden für den mit Azure SQL-Datenbank verknüpften Dienst unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft muss auf **AzureSqlDatabase** festgelegt sein. | Ja |
| connectionString | Geben Sie Informationen, die zur Verbindung mit der Azure SQL-Datenbank-Instanz erforderlich sind, für die **connectionString**-Eigenschaft ein. <br/>Sie können auch ein Kennwort oder einen Dienstprinzipalschlüssel in Azure Key Vault eingeben. Bei Verwendung der SQL-Authentifizierung pullen Sie die `password`-Konfiguration aus der Verbindungszeichenfolge. Weitere Informationen finden Sie im JSON-Beispiel unter der Tabelle sowie unter [Speichern von Anmeldeinformationen in Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| servicePrincipalId | Geben Sie die Client-ID der Anwendung an. | Ja, bei Azure AD-Authentifizierung mit einem Dienstprinzipal |
| servicePrincipalKey | Geben Sie den Schlüssel der Anwendung an. Markieren Sie dieses Feld als **SecureString**, um es sicher in Azure Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja, bei Azure AD-Authentifizierung mit einem Dienstprinzipal |
| tenant | Geben Sie die Mandanteninformationen, wie Domänenname oder Mandanten-ID, für Ihre Anwendung an. Diese können Sie abrufen, indem Sie im Azure-Portal mit der Maus auf den Bereich oben rechts zeigen. | Ja, bei Azure AD-Authentifizierung mit einem Dienstprinzipal |
| azureCloudType | Geben Sie für die Dienstprinzipalauthentifizierung die Art der Azure-Cloudumgebung an, bei der Ihre Azure AD-Anwendung registriert ist. <br/> Zulässige Werte sind **AzurePublic**, **AzureChina**, **AzureUsGovernment** und **AzureGermany**. Standardmäßig wird die Cloudumgebung der Data Factory verwendet. | Nein |
| connectVia | Diese [Integration Runtime](concepts-integration-runtime.md) wird zum Herstellen einer Verbindung mit dem Datenspeicher verwendet. Sie können die Azure Integration Runtime oder eine selbstgehostete Integration Runtime verwenden, sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet. Wenn kein Wert angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. | Nein |

Weitere Voraussetzungen und JSON-Beispiele für die verschiedenen Authentifizierungstypen finden Sie in den folgenden Abschnitten:

- [SQL-Authentifizierung](#sql-authentication)
- [Azure AD-Anwendungstokenauthentifizierung: Dienstprinzipal](#service-principal-authentication)
- [Azure AD-Anwendungstokenauthentifizierung: Verwaltete Identitäten für Azure-Ressourcen](#managed-identity)

>[!TIP]
>Wenn ein Fehler mit dem Fehlercode „UserErrorFailedToConnectToSqlServer“ auftritt und eine Meldung wie „Das Sitzungslimit für die Datenbank ist XXX und wurde erreicht“ angezeigt wird, fügen Sie `Pooling=false` zu Ihrer Verbindungszeichenfolge hinzu, und versuchen Sie es erneut.

### <a name="sql-authentication"></a>SQL-Authentifizierung

**Beispiel: Verwenden der SQL-Authentifizierung**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Beispiel: Kennwort in Azure Key Vault**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
            "password": {
                "type": "AzureKeyVaultSecret",
                "store": {
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                },
                "secretName": "<secretName>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Dienstprinzipalauthentifizierung

Um die Azure AD-Anwendungstokenauthentifizierung basierend auf dem Dienstprinzipal zu verwenden, gehen Sie folgendermaßen vor:

1. [Erstellen Sie eine Azure Active Directory-Anwendung](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) im Azure-Portal. Notieren Sie sich den Anwendungsnamen und die folgenden Werte zum Definieren des verknüpften Diensts:

    - Anwendungs-ID
    - Anwendungsschlüssel
    - Mandanten-ID

2. [Stellen Sie einen Azure Active Directory-Administrator](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database) für Ihren Server im Azure-Portal bereit, falls Sie dies noch nicht getan haben. Der Azure AD-Administrator muss ein Azure AD-Benutzer oder eine Azure AD-Gruppe sein, jedoch kein Dienstprinzipal. Dieser Schritt ist erforderlich, damit Sie im nachfolgenden Schritt eine Azure AD-Identität verwenden können, um einen Benutzer einer eigenständigen Datenbank für den Dienstprinzipal erstellen können.

3. [Erstellen Sie Benutzer der eigenständigen Datenbank](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) für den Dienstprinzipal. Stellen Sie eine Verbindung mit der Datenbank her, aus der bzw. in die Sie Daten mithilfe von Tools wie SQL Server Management Studio kopieren möchten. Verwenden Sie dazu eine Azure AD-Identität, die mindestens die Berechtigung ALTER ANY USER aufweist. Führen Sie folgenden T-SQL-Code aus:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Gewähren Sie dem Dienstprinzipal die notwendigen Berechtigungen, wie bei SQL- oder anderen Benutzern üblich. Führen Sie den folgenden Code aus. Weitere Optionen finden Sie in [diesem Dokument](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql).

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your application name];
    ```

5. Konfigurieren Sie einen mit Azure SQL-Datenbank verknüpften Dienst in Azure Data Factory.

#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Beispiel eines verknüpften Diensts mit Dienstprinzipalauthentifizierung

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Verwaltete Identitäten für Azure-Ressourcenauthentifizierung

Eine Data Factory kann einer [verwalteten Identität für Azure-Ressourcen](data-factory-service-identity.md) zugeordnet werden, die diese spezielle Data Factory darstellt. Sie können diese verwaltete Identität für die Azure SQL-Datenbank-Authentifizierung verwenden. Die angegebene Factory kann mithilfe dieser Identität auf Daten zugreifen und Daten aus der oder in die Datenbank kopieren.

Führen Sie die folgenden Schritte aus, um die Authentifizierung mit einer verwalteten Identität zu verwenden.

1. [Stellen Sie einen Azure Active Directory-Administrator](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database) für Ihren Server im Azure-Portal bereit, falls Sie dies noch nicht getan haben. Der Azure AD-Administrator kann ein Azure AD-Benutzer oder eine Azure AD-Gruppe sein. Wenn Sie der Gruppe mit der verwalteten Identität eine Administratorrolle zuweisen, überspringen Sie die Schritte 3 und 4. Der Administrator erhält Vollzugriff auf die Datenbank.

2. [Erstellen Sie für eine eigenständige Datenbank Benutzer](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) für die verwaltete Azure Data Factory-Identität. Stellen Sie eine Verbindung mit der Datenbank her, aus der bzw. in die Sie Daten mithilfe von Tools wie SQL Server Management Studio kopieren möchten. Verwenden Sie dazu eine Azure AD-Identität, die mindestens die Berechtigung ALTER ANY USER aufweist. Führen Sie folgenden T-SQL-Code aus:
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Gewähren Sie der verwalteten Data Factory-Identität die notwendigen Berechtigungen, und gehen Sie dabei so vor wie für SQL-Benutzer und andere Benutzer. Führen Sie den folgenden Code aus. Weitere Optionen finden Sie in [diesem Dokument](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql).

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your Data Factory name];
    ```

4. Konfigurieren Sie einen mit Azure SQL-Datenbank verknüpften Dienst in Azure Data Factory.

**Beispiel**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den verfügbaren Abschnitten und Eigenschaften zum Definieren von Datasets finden Sie unter [Datasets](./concepts-datasets-linked-services.md).

Die folgenden Eigenschaften werden beim Azure SQL-Datenbank-Dataset unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft des Datasets muss auf **AzureSqlTable** festgelegt sein. | Ja |
| schema | Name des Schemas. |Quelle: Nein, Senke: Ja  |
| table | Name der Tabelle/Ansicht. |Quelle: Nein, Senke: Ja  |
| tableName | Name der Tabelle/Ansicht mit Schema. Diese Eigenschaft wird aus Gründen der Abwärtskompatibilität weiterhin unterstützt. Verwenden Sie für eine neue Workload `schema` und `table`. | Quelle: Nein, Senke: Ja |

### <a name="dataset-properties-example"></a>Beispiel für Dataseteigenschaften

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste der verfügbaren Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie unter [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Azure SQL-Datenbank-Quelle und -Senke unterstützt werden.

### <a name="azure-sql-database-as-the-source"></a>Azure SQL-Datenbank als Quelle

>[!TIP]
>Weitere Informationen zum effizienten Laden von Daten aus Azure SQL-Datenbank mithilfe der Datenpartitionierung finden Sie unter [Paralleles Kopieren aus SQL-Datenbank](#parallel-copy-from-sql-database).

Zum Kopieren von Daten aus Azure SQL-Datenbank werden die folgenden Eigenschaften im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft der Quelle der Kopieraktivität muss auf **AzureSqlSource** festgelegt werden. Der Typ „SqlSource“ wird aus Gründen der Abwärtskompatibilität weiterhin unterstützt. | Ja |
| sqlReaderQuery | Diese Eigenschaft verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. z. B. `select * from MyTable`. | Nein |
| sqlReaderStoredProcedureName | Name der gespeicherten Prozedur, die Daten aus der Quelltabelle liest. Die letzte SQL-Anweisung muss eine SELECT-Anweisung in der gespeicherten Prozedur sein. | Nein |
| storedProcedureParameters | Parameter für die gespeicherte Prozedur.<br/>Zulässige Werte sind Namen oder Name-Wert-Paare. Die Namen und die Groß-/Kleinschreibung von Parametern müssen den Namen und der Groß-/Kleinschreibung der Parameter der gespeicherten Prozedur entsprechen. | Nein |
| isolationLevel | Gibt das Sperrverhalten für Transaktionen für die SQL-Quelle an. Zulässige Werte sind: **ReadCommitted**, **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Ohne Angabe wird die Standardisolationsstufe der Datenbank verwendet. Weitere Informationen finden Sie in [dieser Dokumentation](/dotnet/api/system.data.isolationlevel). | Nein |
| partitionOptions | Hiermit werden die Datenpartitionierungsoptionen angegeben, mit denen Daten aus Azure SQL-Datenbank geladen werden. <br>Zulässige Werte sind: **None** (Standardwert), **PhysicalPartitionsOfTable** und **DynamicRange**.<br>Wenn eine Partitionierungsoption aktiviert ist (d.h. nicht `None`), wird der Parallelitätsgrad für das gleichzeitige Laden von Daten aus einer Oracle-Datenbank durch die Einstellung [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) der Kopieraktivität gesteuert. | Nein |
| partitionSettings | Geben Sie die Gruppe der Einstellungen für die Datenpartitionierung an. <br>Verwenden Sie diese Option, wenn die Partitionsoption nicht `None` lautet. | Nein |
| **_Unter `partitionSettings`:_* _ | | |
| partitionColumnName | Geben Sie den Namen der Quellspalte als _ *„integer“ oder „date/datetime“* * (`int`, `smallint`, `bigint`, `date`, `smalldatetime`, `datetime`, `datetime2` oder `datetimeoffset`) an, der von der Bereichspartitionierung für das parallele Kopieren verwendet wird. Ohne Angabe wird der Index oder der Primärschlüssel der Tabelle automatisch erkannt und als Partitionsspalte verwendet.<br>Verwenden Sie diese Option, wenn die Partitionsoption `DynamicRange` lautet. Wenn Sie die Quelldaten mithilfe einer Abfrage abrufen, integrieren Sie `?AdfDynamicRangePartitionCondition ` in die WHERE-Klausel. Ein Beispiel finden Sie im Abschnitt [Paralleles Kopieren aus SQL-Datenbank](#parallel-copy-from-sql-database). | Nein |
| partitionUpperBound | Der maximale Wert der Partitionsspalte für das Teilen des Partitionsbereichs. Dieser Wert wird zur Entscheidung über den Partitionssprung verwendet, nicht zum Filtern der Zeilen in der Tabelle. Alle Zeilen in der Tabelle oder im Abfrageergebnis werden partitioniert und kopiert. Wenn nicht angegeben, wird der Wert für die Kopieraktivität automatisch erkannt.  <br>Verwenden Sie diese Option, wenn die Partitionsoption `DynamicRange` lautet. Ein Beispiel finden Sie im Abschnitt [Paralleles Kopieren aus SQL-Datenbank](#parallel-copy-from-sql-database). | Nein |
| partitionLowerBound | Der minimale Wert der Partitionsspalte für das Teilen des Partitionsbereichs. Dieser Wert wird zur Entscheidung über den Partitionssprung verwendet, nicht zum Filtern der Zeilen in der Tabelle. Alle Zeilen in der Tabelle oder im Abfrageergebnis werden partitioniert und kopiert. Wenn nicht angegeben, wird der Wert für die Kopieraktivität automatisch erkannt.<br>Verwenden Sie diese Option, wenn die Partitionsoption `DynamicRange` lautet. Ein Beispiel finden Sie im Abschnitt [Paralleles Kopieren aus SQL-Datenbank](#parallel-copy-from-sql-database). | Nein |

**Beachten Sie Folgendes:**

- Wenn **sqlReaderQuery** für **AzureSqlSource** angegeben ist, führt die Kopieraktivität diese Abfrage für die Azure SQL-Datenbankquelle aus, um die Daten abzurufen. Sie können auch eine gespeicherte Prozedur angeben, indem Sie **sqlReaderStoredProcedureName** und **storedProcedureParameters** angeben, sofern die gespeicherten Prozeduren Parameter verwenden.
- Ohne Angabe von **sqlReaderQuery** oder **sqlReaderStoredProcedureName** werden die im Abschnitt „structure“ des JSON-Codes des Datasets definierten Spalten zum Erstellen einer Abfrage verwendet. Die Abfrage `select column1, column2 from mytable` wird für Azure SQL-Datenbank ausgeführt. Falls die Datasetdefinition keinen Abschnitt „structure“ enthält, werden alle Spalten der Tabelle ausgewählt.

#### <a name="sql-query-example"></a>Beispiel für eine SQL-Abfrage

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureSqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>Beispiel für eine gespeicherte Prozedur

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureSqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="stored-procedure-definition"></a>Definition der gespeicherten Prozedur

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-database-as-the-sink"></a>Azure SQL-Datenbank als Senke

> [!TIP]
> Weitere Informationen zum unterstützten Schreibverhalten, zu unterstützten Konfigurationen und bewährten Methoden finden Sie unter [Bewährte Methode zum Laden von Daten in Azure SQL-Datenbank](#best-practice-for-loading-data-into-azure-sql-database).

Zum Kopieren von Daten in Azure SQL-Datenbank werden die folgenden Eigenschaften im Abschnitt **sink** der Kopieraktivität unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft der Senke der Kopieraktivität muss auf **AzureSqlSink** festgelegt werden. Der Typ „SqlSink“ wird aus Gründen der Abwärtskompatibilität weiterhin unterstützt. | Ja |
| preCopyScript | Geben Sie eine auszuführende SQL-Abfrage für die Kopieraktivität an, ehe Sie Daten in Azure SQL-Datenbank schreiben. Sie wird pro Ausführung der Kopieraktivität nur einmal aufgerufen. Sie können diese Eigenschaft nutzen, um vorab geladene Daten zu bereinigen. | Nein |
| tableOption | Gibt an, ob die [Senkentabelle auf Basis des Quellschemas automatisch erstellt werden soll](copy-activity-overview.md#auto-create-sink-tables), wenn sie nicht vorhanden ist. <br>Die automatische Tabellenerstellung wird nicht unterstützt, wenn die Senke eine gespeicherte Prozedur angibt. <br>Zulässige Werte: `none` (Standard), `autoCreate`. | Nein |
| sqlWriterStoredProcedureName | Der Name der gespeicherten Prozedur, die definiert, wie Quelldaten auf eine Zieltabelle angewandt werden. <br/>Diese gespeicherte Prozedur wird *pro Batch aufgerufen*. Für nur einmalig ausgeführte Vorgänge, die nicht mit Quelldaten in Zusammenhang stehen (etwa Löschen/Kürzen), verwenden Sie die `preCopyScript`-Eigenschaft.<br>Ein Beispiel finden Sie unter [Aufrufen einer gespeicherten Prozedur aus einer SQL-Senke](#invoke-a-stored-procedure-from-a-sql-sink). | Nein |
| storedProcedureTableTypeParameterName |Der Parametername des Tabellentyps, der in der gespeicherten Prozedur angegeben ist.  |Nein |
| sqlWriterTableType |Der Tabellentypname, der in der gespeicherten Prozedur verwendet werden soll. Die Kopieraktivität macht die verschobenen Daten in einer temporären Tabelle mit diesem Tabellentyp verfügbar. Der gespeicherte Prozedurcode kann dann die kopierten Daten mit vorhandenen Daten zusammenführen. |Nein |
| storedProcedureParameters |Parameter für die gespeicherte Prozedur.<br/>Zulässige Werte: Name-Wert-Paare. Die Namen und die Groß-/Kleinschreibung von Parametern müssen denen der Parameter der gespeicherten Prozedur entsprechen. | Nein |
| writeBatchSize | Anzahl der Zeilen, die *pro Batch* in die SQL-Tabelle eingefügt werden sollen.<br/> Zulässiger Wert: **integer** (Anzahl der Zeilen) Standardmäßig bestimmt Azure Data Factory die geeignete Batchgröße dynamisch anhand der Zeilengröße. | Nein |
| writeBatchTimeout | Wartezeit für den Abschluss der Batcheinfügung, bis das Timeout wirksam wird.<br/> Zulässiger Wert: **timespan**. Beispiel: 00:30:00 (30 Minuten). | Nein |
| disableMetricsCollection | Data Factory sammelt Metriken wie Azure SQL-Datenbank-DTUs für die Leistungsoptimierung von Kopiervorgängen und Empfehlungen. Wenn Sie sich wegen dieses Verhaltens Gedanken machen, geben Sie `true` an, um es zu deaktivieren. | Nein (Standard = `false`) |

**Beispiel 1: Anfügen von Daten**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Beispiel 2: Aufrufen einer gespeicherten Prozedur während des Kopiervorgangs**

Weitere Informationen finden Sie unter [Aufrufen einer gespeicherten Prozedur aus einer SQL-Senke](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="parallel-copy-from-sql-database"></a>Paralleles Kopieren aus SQL-Datenbank

Der Connector für Azure SQL-Datenbank in der Kopieraktivität verfügt über eine integrierte Datenpartitionierung zum parallelen Kopieren von Daten. Die Datenpartitionierungsoptionen befinden sich auf der Registerkarte **Quelle** der Kopieraktivität.

![Screenshot der Partitionierungsoptionen](./media/connector-sql-server/connector-sql-partition-options.png)

Wenn Sie das partitionierte Kopieren aktivieren, führt die Kopieraktivität parallele Abfragen für Ihre Azure SQL-Datenbank-Quelle aus, um Daten in Partitionen zu laden. Der Parallelitätsgrad wird über die Einstellung [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) der Kopieraktivität gesteuert. Wenn Sie zum Beispiel `parallelCopies` auf vier festlegen, werden von Data Factory vier Abfragen gleichzeitig generiert und ausgeführt. Diese Abfragen basieren auf der Option und den Einstellungen, die Sie für die Partitionierung angegeben haben, und jede Abfrage ruft einen Teil der Daten aus Ihrer Azure SQL-Datenbank-Instanz ab.

Es wird empfohlen, das parallele Kopieren mit Datenpartitionierung zu aktivieren, vor allem, wenn Sie große Datenmengen aus Ihrer Azure SQL-Datenbank-Instanz laden. Im Anschluss finden Sie empfohlene Konfigurationen für verschiedene Szenarien. Beim Kopieren von Daten in einen dateibasierten Datenspeicher wird empfohlen, mehrere Dateien in einen Ordner zu schreiben (nur den Ordnernamen anzugeben). In diesem Fall ist die Leistung besser als beim Schreiben in eine einzelne Datei.

| Szenario                                                     | Empfohlene Einstellungen                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Vollständiges Laden aus einer großen Tabelle mit physischen Partitionen        | **Partitionsoption:** Physische Partitionen der Tabelle. <br><br/>Während der Ausführung erkennt Data Factory automatisch die physischen Partitionen und kopiert Daten nach Partitionen. <br><br/>Um zu überprüfen, ob Ihre Tabelle eine physische Partition besitzt oder nicht, können Sie auf [diese Abfrage](#sample-query-to-check-physical-partition) verweisen. |
| Vollständiges Laden aus einer großen Tabelle ohne physische Partitionen, aber mit einer integer- oder datetime-Spalte für die Datenpartitionierung. | **Partitionsoptionen:** Dynamische Bereichspartitionierung<br>**Partitionsspalte** (optional): Geben Sie die Spalte für die Datenpartitionierung an. Ohne Angabe wird der Index oder die Primärschlüsselspalte verwendet.<br/>**Obergrenze der Partition** und **Untergrenze der Partition** (optional): Geben Sie an, ob Sie den Partitionssprung bestimmen möchten. Dies dient nicht zum Filtern der Zeilen in der Tabelle; alle Zeilen in der Tabelle werden partitioniert und kopiert. Wenn nicht angegeben, werden die Werte für die Kopieraktivität automatisch erkannt.<br><br>Wenn Ihre Partitionsspalte „ID“ also beispielsweise Werte zwischen 1 und 100 enthält, und Sie als Untergrenze „20“ und als Obergrenze „80“ konfigurieren und das parallele Kopieren auf „4“ festgelegt ist, ruft Data Factory Daten anhand von vier Partitionen ab: IDs im Bereich <=20, [21, 50], [51, 80] und >=81. |
| Laden einer großen Datenmenge unter Verwendung einer benutzerdefinierten Abfrage ohne physische Partitionen, aber mit einer integer- oder date/datetime-Spalte für die Datenpartitionierung. | **Partitionsoptionen:** Dynamische Bereichspartitionierung<br>**Abfrage**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br>**Partitionsspalte:** Geben Sie die Spalte für die Datenpartitionierung an.<br>**Obergrenze der Partition** und **Untergrenze der Partition** (optional): Geben Sie an, ob Sie den Partitionssprung bestimmen möchten. Dies dient nicht zum Filtern der Zeilen in der Tabelle; alle Zeilen im Abfrageergebnis werden partitioniert und kopiert. Wenn keine Angaben gemacht wurden, wird der Wert für die Kopieraktivität automatisch erkannt.<br><br>Data Factory ersetzt `?AdfRangePartitionColumnName` während der Ausführung durch den tatsächlichen Spaltennamen und die Wertebereiche für die jeweilige Partition und sendet die Daten an Azure SQL-Datenbank. <br>Wenn Ihre Partitionsspalte „ID“ also beispielsweise Werte von 1 bis 100 enthält und Sie als Untergrenze „20“, als Obergrenze „80“ und für das parallele Kopieren „4“ festlegen, ruft Data Factory Daten nach vier Partitions-IDs im Bereich zwischen <=20, [21, 50], [51, 80] und >=81 ab. <br><br>Hier finden Sie weitere Beispiele für verschiedene Szenarien:<br> 1. Abfrage der gesamten Tabelle: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. Abfrage aus einer Tabelle mit Spaltenauswahl und zusätzlichen Where-Klausel-Filtern: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. Abfragen mit Unterabfragen: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. Abfrage mit Partition in Unterabfrage: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Bewährte Methoden zum Laden von Daten mit Partitionierungsoption:

1. Wählen Sie eine aussagekräftige Spalte als Partitionsspalte (wie Primärschlüssel oder eindeutiger Schlüssel), um Datenabweichungen zu vermeiden. 
2. Wenn die Tabelle eine integrierte Partition aufweist, verwenden Sie die Partitionsoption „Physikalische Partitionen der Tabelle“, um eine bessere Leistung zu erzielen.    
3. Wenn Sie Azure Integration Runtime zum Kopieren von Daten verwenden, können Sie größere „[Data Integration Units (DIU)](copy-activity-performance-features.md#data-integration-units)“ festlegen (> 4), um mehr Computingressourcen zu nutzen. Prüfen Sie dort die anwendbaren Szenarien.
4. „[Grad der Kopierparallelität](copy-activity-performance-features.md#parallel-copy)“ steuert die Partitionsnummern. Ein zu großer Wert schadet manchmal der Leistung. Deshalb wird empfohlen, diesen Wert wie folgt festzulegen: (DIU oder Anzahl der selbstgehosteten IR-Knoten) × (2 bis 4).

**Beispiel: Vollständiges Laden aus einer großen Tabelle mit physischen Partitionen**

```json
"source": {
    "type": "AzureSqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Beispiel: Abfrage mit dynamischer Bereichspartition**

```json
"source": {
    "type": "AzureSqlSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

### <a name="sample-query-to-check-physical-partition"></a>Beispielabfrage zur Überprüfung der physischen Partition

```sql
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, pf.name AS PartitionFunctionName, c.name AS ColumnName, iif(pf.name is null, 'no', 'yes') AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id 
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id 
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id 
LEFT JOIN sys.partition_schemes ps ON i.data_space_id = ps.data_space_id 
LEFT JOIN sys.partition_functions pf ON pf.function_id = ps.function_id 
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

Wenn die Tabelle eine physische Partition besitzt, würde „HasPartition“ wie folgt als „Yes“ (Ja) angezeigt werden.

![SQL-Abfrageergebnis](./media/connector-azure-sql-database/sql-query-result.png)

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Kopieren von Daten nach und aus Azure SQL-Datenbank

Beim Kopieren von Daten in Azure SQL-Datenbank ist möglicherweise ein anderes Schreibverhalten erforderlich:

- [Anfügen:](#append-data) Meine Quelldaten enthalten nur neue Datensätze.
- [Upsert:](#upsert-data) Meine Quelldaten umfassen sowohl Einfügungen als auch Aktualisierungen.
- [Überschreiben:](#overwrite-the-entire-table) Ich möchte eine gesamte Dimensionstabelle jedes Mal neu laden.
- [Schreiben von Daten mit benutzerdefinierter Logik:](#write-data-with-custom-logic) Ich benötige eine zusätzliche Verarbeitung vor dem endgültigen Einfügen in die Zieltabelle.

Informationen zur Konfiguration in Azure Data Factory und zu den bewährten Methoden finden Sie in den entsprechenden Abschnitten.

### <a name="append-data"></a>Anfügen von Daten

Das Anfügen von Daten ist das Standardverhalten dieses Senkenconnectors für Azure SQL-Datenbank. Azure Data Factory führt eine Masseneinfügung aus, um Daten effizient in Ihre Tabelle zu schreiben. Sie können die Quelle und Senke in der Kopieraktivität entsprechend konfigurieren.

### <a name="upsert-data"></a>Durchführen von Upsert für Daten

**Option 1:** Wenn Sie große Datenmengen kopieren möchten, können Sie mithilfe der Kopieraktivität für alle Datensätze einen Massenladevorgang in eine Stagingtabelle ausführen. Führen Sie anschließend eine Aktivität einer gespeicherten Prozedur aus, um eine [MERGE](/sql/t-sql/statements/merge-transact-sql)- oder INSERT/UPDATE-Anweisung in einem Schritt anzuwenden. 

Das Laden von Daten in eine temporäre Datenbanktabelle wird derzeit nicht nativ von der Kopieraktivität unterstützt. Zur Einrichtung dieser Funktion gibt es eine erweiterte Methode, bei der mehrere Aktivitäten kombiniert werden. Informationen hierzu finden Sie unter [Optimieren von Szenarios mit Massenupsert in Azure SQL-Datenbank](https://github.com/scoriani/azuresqlbulkupsert). Das Beispiel unten zeigt die Verwendung einer permanenten Tabelle als Stagingtabelle.

Sie können beispielsweise in Azure Data Factory eine Pipeline mit einer **Kopieraktivität** erstellen, die mit einer **Aktivität der gespeicherten Prozedur** verkettet ist. Dabei kopiert die Kopieraktivität Daten aus Ihrem Quellspeicher in eine Stagingtabelle in Azure SQL-Datenbank (z. B. **UpsertStagingTable** als Tabellenname im Dataset). Die Aktivität der gespeicherten Prozedur ruft dann eine gespeicherte Prozedur auf, um die Quelldaten aus der Stagingtabelle mit der Zieltabelle zusammenzuführen und die Stagingtabelle zu bereinigen.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Definieren Sie in Ihrer Datenbank eine gespeicherte Prozedur mit MERGE-Logik (wie im folgenden Beispiel), auf die über die vorherige Aktivität der gespeicherten Prozedur gezeigt wird. Angenommen, das Ziel ist die Tabelle **Marketing** mit den drei Spalten: **ProfileID**, **State** und **Category**. Führen Sie den Upsert-Vorgang basierend auf der Spalte **ProfileID** aus.

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
   MERGE TargetTable AS target
   USING UpsertStagingTable AS source
   ON (target.[ProfileID] = source.[ProfileID])
   WHEN MATCHED THEN
      UPDATE SET State = source.State
    WHEN NOT matched THEN
       INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    TRUNCATE TABLE UpsertStagingTable
END
```

**Option 2:** Sie können [eine gespeicherte Prozedur in der Kopieraktivität aufrufen](#invoke-a-stored-procedure-from-a-sql-sink). Dabei wird jeder Batch in der Quelltabelle (entsprechend der `writeBatchSize`-Eigenschaft) ausgeführt, statt Masseneinfügen als Standardansatz in der Kopieraktivität zu verwenden.

**Option 3:** Über die Funktion [Zuordnungsdatenfluss](#sink-transformation) können Sie auch integrierte Insert-/Upsert-/Update-Methoden verwenden.

### <a name="overwrite-the-entire-table"></a>Überschreiben der gesamten Tabelle

Sie können die **preCopyScript**-Eigenschaft in der Kopieraktivitätssenke konfigurieren. In diesem Fall führt Azure Data Factory für jede ausgeführte Kopieraktivität zuerst das Skript aus. Dann wird der Kopiervorgang ausgeführt, um die Daten einzufügen. Beispiel: Um die gesamte Tabelle mit den neuesten Daten zu überschreiben, geben Sie ein Skript an, um zunächst alle Datensätze zu löschen, bevor die neuen Daten durch Massenladen aus der Quelle eingefügt werden.

### <a name="write-data-with-custom-logic"></a>Schreiben von Daten mit benutzerdefinierter Logik

Die Schritte zum Schreiben von Daten mit benutzerdefinierter Logik ähneln den im Abschnitt [Durchführen von Upsert für Daten](#upsert-data) beschriebenen Schritten. Wenn Sie die Quelldaten vor dem endgültigen Einfügen in die Zieltabelle zusätzlich verarbeiten möchten, laden Sie sie in eine Stagingtabelle, und rufen Sie dann eine Aktivität der gespeicherten Prozedur oder eine gespeicherte Prozedur in der Senke der Kopieraktivität auf, um die Daten zu verarbeiten. Alternativ dazu können Sie auch einen Zuordnungsdatenfluss verwenden.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Aufrufen der gespeicherten Prozedur von der SQL-Senke

Beim Kopieren von Daten in eine Azure SQL-Datenbankinstanz können Sie auch eine vom Benutzer angegebene gespeicherte Prozedur mit zusätzlichen Parametern für jeden Batch der Quelltabelle konfigurieren und aufrufen. Das Feature der gespeicherten Prozedur nutzt [Tabellenwertparameter](/dotnet/framework/data/adonet/sql/table-valued-parameters).

Sie können eine gespeicherte Prozedur nutzen, wenn integrierte Kopiermechanismen nicht den Zweck erfüllen. Ein Beispiel hierfür ist ein Szenario, in dem Sie vor dem endgültigen Einfügen von Quelldaten in die Zieltabelle eine zusätzliche Verarbeitung anwenden möchten. Beispiele für eine zusätzliche Verarbeitung sind das Zusammenführen von Spalten, das Suchen nach zusätzlichen Werten und das Einfügen in mehr als eine Tabelle.

Das folgende Beispiel zeigt, wie Sie eine gespeicherte Prozedur verwenden, um einen einfachen Upsert-Vorgang in eine Tabelle in Azure SQL-Datenbank auszuführen. Im Beispiel wird angenommen, dass Eingabedaten vorhanden sind und die Senkentabelle **Marketing** drei Spalten enthält: **ProfileID**, **State** und **Category**. Führen Sie den Upsert-Vorgang basierend auf der Spalte **ProfileID** aus, und wenden Sie ihn nur auf die Kategorie „ProductA“ an.

1. Definieren Sie in Ihrer Datenbank den Tabellentyp mit dem gleichen Namen wie **sqlWriterTableType**. Das Schema des Tabellentyps muss mit dem Schema übereinstimmen, das von den Eingabedaten zurückgegeben wird.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. Definieren Sie die gespeicherte Prozedur in Ihrer Datenbank mit demselben Namen wie **SqlWriterStoredProcedureName**. Sie verarbeitet die Eingabedaten aus der angegebenen Quelle und führt sie mit der Ausgabetabelle zusammen. Der Parametername des Tabellentyps in der gespeicherten Prozedur entspricht dem im Dataset definierten **tableName**.

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. Definieren Sie in Azure Data Factory den Abschnitt **SQL-Senke** in der Kopieraktivität wie folgt:

    ```json
    "sink": {
        "type": "AzureSqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="mapping-data-flow-properties"></a>Eigenschaften von Mapping Data Flow

Beim Transformieren von Daten im Zuordnungsdatenfluss können Sie Tabellen in der Azure SQL-Datenbank lesen und in diese schreiben. Weitere Informationen finden Sie unter [Quellentransformation](data-flow-source.md) und [Senkentransformation](data-flow-sink.md) in Zuordnungsdatenflüssen.

### <a name="source-transformation"></a>Quellentransformation

Spezifische Einstellungen für Azure SQL-Datenbank sind auf der Registerkarte **Quellenoptionen** der Quellentransformation verfügbar.

**Eingabe**: Wählen Sie aus, ob Sie Ihre Quelle auf eine Tabelle verweisen (Äquivalent von ```Select * from <table-name>```) oder eine benutzerdefinierte SQL-Abfrage eingeben.

**Query** (Abfrage): Wenn Sie im Eingabefeld „Abfrage“ auswählen, geben Sie eine SQL-Abfrage für die Quelle ein. Diese Einstellung überschreibt jede Tabelle, die Sie im Dataset ausgewählt haben. **Order By**-Klauseln werden hier nicht unterstützt. Sie können aber eine vollständige SELECT FROM-Anweisung festlegen. Sie können auch benutzerdefinierte Tabellenfunktionen verwenden. **select * from udfGetData()** ist eine benutzerdefinierte Funktion in SQL, die eine Tabelle zurückgibt. Diese Abfrage generiert eine Quelltabelle, die Sie in Ihrem Datenfluss verwenden können. Die Verwendung von Abfragen stellt auch eine gute Möglichkeit dar, um die Zeilen für Tests oder Suchvorgänge zu verringern.

- SQL-Beispiel: ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Batchgröße**: Geben Sie eine Batchgröße ein, um große Datenmengen in Leseblöcke zu segmentieren.

**Isolationsstufe**: Der Standardwert für SQL-Quellen in Mapping Data Flow lautet „Lesen nicht zugesichert“. Sie können die Isolationsstufe hier in einen der folgenden Werte ändern:

- Lesen zugesichert
- Lesen nicht zugesichert
- Wiederholbarer Lesevorgang
- Serialisierbar
- Keine (Isolationsstufe ignorieren)

![Isolationsstufe](media/data-flow/isolationlevel.png "Isolationsstufe")

### <a name="sink-transformation"></a>Senkentransformation

Spezifische Einstellungen für Azure SQL-Datenbank sind auf der Registerkarte **Einstellungen** der Senkentransformation verfügbar.

**Updatemethode:** Bestimmt, welche Vorgänge für das Datenbankziel zulässig sind. Standardmäßig sind lediglich Einfügevorgänge zulässig. Wenn Sie für Zeilen Update-, Upsert- oder Löschvorgänge verwenden möchten, fügen Sie zunächst eine Transformation zur Änderung von Zeilen hinzu, um Zeilen für diese Aktionen zu kennzeichnen. Für Update-, Upsert- und Löschvorgänge muss mindestens eine Schlüsselspalte festgelegt werden, um die Zeile zu bestimmen, die geändert werden soll.

![Schlüsselspalten](media/data-flow/keycolumn.png "Schlüsselspalten")

Der Spaltenname, den Sie hier als Schlüssel auswählen, wird von ADF als Teil der nachfolgenden Update-, Upsert- und Löschvorgänge verwendet. Daher müssen Sie eine Spalte auswählen, die in der Senkenzuordnung vorhanden ist. Wenn Sie den Wert nicht in diese Schlüsselspalte schreiben möchten, klicken Sie auf „Skip writing key columns“ (Schreiben von Schlüsselspalten überspringen).

Sie können die hier verwendete Schlüsselspalte zum Aktualisieren der Azure SQL-Datenbank-Zieltabelle parametrisieren. Wenn Sie über mehrere Spalten für einen zusammengesetzten Schlüssel verfügen, klicken Sie auf „Benutzerdefinierter Ausdruck“. Sie können dann dynamischen Inhalt mithilfe der ADF-Datenfluss-Ausdruckssprache hinzufügen, die ein Array von Zeichenfolgen mit Spaltennamen für einen zusammengesetzten Schlüssel enthalten kann.

**Tabellenaktion:** Bestimmt, ob die Zieltabelle vor dem Schreiben neu erstellt werden soll oder alle Zeilen aus der Zieltabelle entfernt werden sollen.

- Keine: Es wird keine Aktion an der Tabelle vorgenommen.
- Neu erstellen: Die Tabelle wird gelöscht und neu erstellt. Erforderlich, wenn eine neue Tabelle dynamisch erstellt wird.
- Abschneiden: Alle Zeilen werden aus der Zieltabelle entfernt.

**Batchgröße**: Steuert, wie viele Zeilen in die einzelnen Buckets geschrieben werden. Durch größere Batches werden zwar Komprimierung und Arbeitsspeicheroptimierung verbessert, beim Zwischenspeichern von Daten besteht aber die Gefahr, dass Ausnahmen wegen unzureichenden Arbeitsspeichers auftreten.

**TempDB verwenden:** Standardmäßig verwendet Data Factory eine globale temporäre Tabelle zum Speichern von Daten als Teil des Ladevorgangs. Sie können alternativ die Option „TempDB verwenden“ deaktivieren und stattdessen Data Factory auffordern, die temporäre Aufbewahrungstabelle in einer Benutzerdatenbank zu speichern, die sich in der für diese Senke verwendeten Datenbank befindet.

![Use TempDB (TempDB verwenden)](media/data-flow/tempdb.png "Use TempDB (TempDB verwenden)")

**Pre- und Post SQL-Skripts**: Geben Sie mehrzeilige SQL-Skripts ein, die ausgeführt werden, bevor Daten in die Senkendatenbank geschrieben werden (Vorverarbeitung) und danach (Nachbearbeitung).

![Vorverarbeitungs- und Nachbearbeitungs-SQL-Skripts](media/data-flow/prepost1.png "SQL-Verarbeitungsskripts")

### <a name="error-row-handling"></a>Fehlerzeilenbehandlung

Beim Schreiben in Azure SQL-Datenbank können aufgrund von Einschränkungen durch das Ziel Fehler bei bestimmten Datenzeilen auftreten. Häufige Fehler sind z. B. folgende:

*    Zeichenfolgen- oder Binärdaten würden in der Tabelle abgeschnitten.
*    Der Wert NULL kann nicht in die Spalte einfügt werden.
*    Die INSERT-Anweisung steht in Konflikt mit der CHECK-Einschränkung.

Standardmäßig scheitert eine Datenflussausführung beim ersten erhaltenen Fehler. Sie können die Option **Bei Fehler fortsetzen** auswählen, die einen Abschluss des Datenflusses auch dann ermöglicht, wenn einzelne Zeilen Fehler aufweisen. Azure Data Factory bietet verschiedene Optionen, mit denen Sie diese Fehlerzeilen behandeln können.

**Transaktionscommit:** Wählen Sie aus, ob Ihre Daten in einer einzelnen Transaktion oder in Batches geschrieben werden. Eine einzelne Transaktion führt zu einer schlechteren Leistung, doch sind keine geschriebenen Daten für andere sichtbar, bis die Transaktion abgeschlossen ist.  

**Abgelehnte Daten ausgeben:** Wenn diese Option aktiviert ist, können Sie die Fehlerzeilen in eine CSV-Datei in Azure Blob Storage oder ein Azure Data Lake Storage Gen2-Konto Ihrer Wahl ausgeben. Dadurch werden die Fehlerzeilen mit drei zusätzlichen Spalten geschrieben: dem SQL-Vorgang wie INSERT oder UPDATE, dem Datenfluss-Fehlercode und der Fehlermeldung für die Zeile.

**Bericht bei Fehler als erfolgreich markieren:** Wenn diese Option aktiviert ist, wird der Datenfluss als erfolgreich markiert, auch wenn Fehlerzeilen gefunden werden. 

![Fehlerzeilenbehandlung](media/data-flow/sql-error-row-handling.png "Fehlerzeilenbehandlung")


## <a name="data-type-mapping-for-azure-sql-database"></a>Datentypzuordnung für Azure SQL-Datenbank

Beim Kopieren von Daten aus bzw. nach Azure SQL-Datenbank werden die folgenden Zuordnungen von Azure SQL-Datenbank-Datentypen zu Azure Data Factory-Zwischendatentypen verwendet. Informationen dazu, wie die Kopieraktivität das Quellschema und den Datentyp zur Senke zuordnet, finden Sie unter [Schema- und Datentypzuordnungen](copy-activity-schema-and-type-mapping.md).

| Azure SQL-Datenbank-Datentyp | Azure Data Factory-Zwischendatentyp |
|:--- |:--- |
| BIGINT |Int64 |
| BINARY |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |Datetime |
| Datetime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| INT |Int32 |
| money |Decimal |
| NCHAR |String, Char[] |
| ntext |String, Char[] |
| NUMERIC |Decimal |
| NVARCHAR |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| SMALLINT |Int16 |
| SMALLMONEY |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| TINYINT |Byte |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |String |

>[!NOTE]
> Für Datentypen, die dem Zwischendatentyp „Decimal“ zugeordnet sind, unterstützt die Kopieraktivität derzeit eine Genauigkeit von bis zu 28. Daten mit einer höheren Genauigkeit als 28 müssen in SQL-Abfragen ggf. in eine Zeichenfolge konvertiert werden.

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Eigenschaften der GetMetadata-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [GetMetadata-Aktivität](control-flow-get-metadata-activity.md).

## <a name="using-always-encrypted"></a>Verwenden von Always Encrypted

Verwenden Sie zum Kopieren von Daten mit [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) aus/in Azure SQL-Datenbank den [generischen ODBC-Connector](connector-odbc.md) und den ODBC-Treiber für SQL Server über die selbstgehostete Integration Runtime. Always Encrypted wird von diesem Azure SQL-Datenbank-Connector derzeit nicht unterstützt. 

Dies gilt insbesondere in folgenden Fällen:

1. Richten Sie eine selbstgehostete Integration Runtime ein, sofern noch nicht vorhanden. Im Artikel [Selbstgehostete Integration Runtime](create-self-hosted-integration-runtime.md) finden Sie Details.

2. Laden Sie [hier](/sql/connect/odbc/download-odbc-driver-for-sql-server) den 64-Bit-ODBC-Treiber für SQL Server herunter, und installieren Sie ihn auf dem Computer mit der Integration Runtime. Weitere Informationen zur Funktionsweise dieses Treibers finden Sie unter [Verwendung von Always Encrypted mit dem ODBC-Treiber für SQL Server](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver#using-the-azure-key-vault-provider).

3. Erstellen Sie einen verknüpften Dienst mit dem ODBC-Typ für die Verbindung mit Ihrer SQL-Datenbank. Nachfolgend sind einige Beispiele gezeigt:

    - Zur Verwendung der **SQL-Authentifizierung**: Geben Sie die ODBC-Verbindungszeichenfolge wie unten gezeigt an, und wählen Sie die **Standardauthentifizierung** zum Festlegen von Benutzername und Kennwort aus.

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
        ```

    - Zur Verwendung der **Authentifizierung der verwalteten Identität über Data Factory**: 

        1. Stellen Sie sicher, dass diese [Voraussetzungen](#managed-identity) erfüllt sind, um einen Datenbankbenutzer für die verwaltete Identität zu erstellen, und weisen Sie in Ihrer Datenbank die entsprechende Rolle zu.
        2. Geben Sie im verknüpften Dienst die ODBC-Verbindungszeichenfolge wie unten gezeigt an, und wählen Sie die **anonyme Authentifizierung** aus, da die Verbindungszeichenfolge selbst `Authentication=ActiveDirectoryMsi` angibt.

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>; Authentication=ActiveDirectoryMsi;
        ```

4. Erstellen Sie ein Dataset und eine Kopieraktivität mit dem ODBC-Typ. Weitere Informationen finden Sie im Artikel [ODBC-Connector](connector-odbc.md).

## <a name="next-steps"></a>Nächste Schritte

Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher und -formate](copy-activity-overview.md#supported-data-stores-and-formats).
---
title: Kopieren von Daten in und aus Azure SQL Data Warehouse mit Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, wie mithilfe von Data Factory Daten aus unterstützten Quellspeichern nach Azure SQL Data Warehouse oder aus SQL Data Warehouse in unterstützte Senkenspeicher kopiert werden.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: jingwang
ms.openlocfilehash: bd02a95f485f45c223fce4c24a72251481c2aa7e
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427896"
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Kopieren von Daten nach und aus Azure SQL Data Warehouse mithilfe von Azure Data Factory 
> [!div class="op_single_selector" title1="[Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)"]
> * [Aktuelle Version](connector-azure-sql-data-warehouse.md)
> * In diesem Artikel wird beschrieben, wie Sie Daten nach und aus Azure SQL Data Warehouse kopieren.

Informationen zu Azure Data Factory finden Sie im [Einführungsartikel](introduction.md). Unterstützte Funktionen

## <a name="supported-capabilities"></a>Dieser Azure-BLOB-Connector wird für die folgenden Aktivitäten unterstützt:

[Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)-Tabelle

- [Mapping Data Flow](concepts-data-flow-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [GetMetadata-Aktivität](control-flow-get-metadata-activity.md)
- Der Azure SQL Data Warehouse-Connector unterstützt insbesondere folgende Funktionen:

Kopieren von Daten mit SQL-Authentifizierung und Azure Active Directory-Anwendungstokenauthentifizierung (Azure AD) mit einem Dienstprinzipal oder verwalteten Identitäten für Azure-Ressourcen.

- Als Quelle das Abrufen von Daten mithilfe einer SQL-Abfrage oder gespeicherten Prozedur
- Als Senke das Laden von Daten mithilfe von PolyBase oder einer Batcheinfügung.
- Um bessere Kopierergebnisse zu erzielen, wird PolyBase empfohlen. Wenn Sie Daten mithilfe der Azure Data Factory Integration Runtime kopieren, konfigurieren Sie die [Azure SQL Server-Firewall](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure), damit Azure-Dienste Zugriff auf den Server erhalten.

> [!IMPORTANT]
> Wenn Sie Daten mithilfe einer selbstgehosteten Integration Runtime kopieren, konfigurieren Sie die Azure SQL-Server-Firewall, um den entsprechenden IP-Adressbereich zuzulassen.
> Dieser Bereich schließt die IP-Adresse des Computers ein, der für die Verbindung mit Azure SQL-Datenbank verwendet wird. Erste Schritte

## <a name="get-started"></a>Das beste Ergebnis erzielen Sie, indem Sie Daten mithilfe von PolyBase in Azure SQL Data Warehouse laden.

> [!TIP]
> Details finden Sie im Abschnitt [Daten unter Verwendung von PolyBase in Azure SQL Data Warehouse laden](#use-polybase-to-load-data-into-azure-sql-data-warehouse) . Eine exemplarische Vorgehensweise mit einem Anwendungsfall finden Sie unter [Laden von 1 TB in Azure SQL Data Warehouse in weniger als 15 Minuten mit Azure Data Factory](load-azure-sql-data-warehouse.md). Die folgenden Abschnitte enthalten Details zu Eigenschaften für das Definieren von Data Factory-Entitäten speziell für den Azure SQL Data Warehouse-Connector.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Eigenschaften des verknüpften Diensts

## <a name="linked-service-properties"></a>Folgende Eigenschaften werden für einen mit Azure SQL Data Warehouse verknüpften Dienst unterstützt:

Eigenschaft

| BESCHREIBUNG | Erforderlich | type |
|:--- |:--- |:--- |
| Die „type“-Eigenschaft muss auf **AzureSqlDW** festgelegt sein. | Ja | connectionString |
| Geben Sie Informationen, die zur Verbindung mit der Azure SQL Data Warehouse-Instanz erforderlich sind, für die **connectionString**-Eigenschaft ein. | Markieren Sie dieses Feld als „SecureString“, um es sicher in Data Factory zu speichern. <br/>Sie können auch das Kennwort/den Dienstprinzipalschlüssel in Azure Key Vault speichern und bei Verwendung der SQL-Authentifizierung die `password`-Konfiguration aus der Verbindungszeichenfolge pullen. Ausführlichere Informationen finden Sie im JSON-Beispiel unter der Tabelle und im Artikel [Speichern von Anmeldeinformationen in Azure Key Vault](store-credentials-in-key-vault.md). Ja | servicePrincipalId |
| Geben Sie die Client-ID der Anwendung an. | Ja, bei Azure AD-Authentifizierung mit einem Dienstprinzipal | servicePrincipalKey |
| Geben Sie den Schlüssel der Anwendung an. | Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). Ja, bei Azure AD-Authentifizierung mit einem Dienstprinzipal | tenant |
| Geben Sie die Mandanteninformationen (Domänenname oder Mandanten-ID) für Ihre Anwendung an. | Diese können Sie abrufen, indem Sie den Mauszeiger über den rechten oberen Bereich im Azure-Portal bewegen. Ja, bei Azure AD-Authentifizierung mit einem Dienstprinzipal | connectVia |
| Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. | Sie können die Azure Integration Runtime oder eine selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. Nein | Weitere Voraussetzungen und JSON-Beispiele für die verschiedenen Authentifizierungstypen finden Sie in den folgenden Abschnitten: |

[SQL-Authentifizierung](#sql-authentication)

- Azure AD-Anwendungstokenauthentifizierung: [Dienstprinzipal](#service-principal-authentication)
- Azure AD-Anwendungstokenauthentifizierung: [Verwaltete Identitäten für Azure-Ressourcen](#managed-identity)
- Wenn ein Fehler mit dem Fehlercode „UserErrorFailedToConnectToSqlServer“ auftritt und eine Meldung wie „Das Sitzungslimit für die Datenbank ist XXX und wurde erreicht“ angezeigt wird, fügen Sie `Pooling=false` zu Ihrer Verbindungszeichenfolge hinzu, und versuchen Sie es erneut.

>[!TIP]
>SQL-Authentifizierung

### <a name="sql-authentication"></a>Beispiel eines verknüpften Diensts mit SQL-Authentifizierung

#### <a name="linked-service-example-that-uses-sql-authentication"></a>**Kennwort in Azure Key Vault:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Dienstprinzipalauthentifizierung

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Um die Azure AD-Anwendungstokenauthentifizierung basierend auf dem Dienstprinzipal zu verwenden, gehen Sie folgendermaßen vor:

**[Erstellen Sie eine Azure Active Directory-Anwendung](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)** im Azure-Portal.

1. Notieren Sie sich den Anwendungsnamen und die folgenden Werte zum Definieren des verknüpften Diensts: Anwendungs-ID

    - Anwendungsschlüssel
    - Mandanten-ID
    - **[Geben Sie einen Azure Active Directory-Administrator](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** für Ihre Azure SQL Server-Instanz im Azure-Portal an, falls dies noch nicht geschehen ist.

2. Der Azure AD-Administrator kann ein Azure AD-Benutzer oder eine Azure AD-Gruppe sein. Wenn Sie der Gruppe mit der verwalteten Identität eine Administratorrolle zuweisen möchten, überspringen Sie die Schritte 3 und 4. Der Administrator erhält Vollzugriff auf die Datenbank. **[Erstellen Sie eigenständige Datenbankbenutzer](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** für den Dienstprinzipal.

3. Stellen Sie eine Verbindung mit dem Data Warehouse her, aus dem bzw. in das Sie Daten mithilfe von Tools wie SSMS kopieren möchten. Verwenden Sie dazu eine Azure AD-Identität, die mindestens die Berechtigung ALTER ANY USER aufweist. Führen Sie den folgenden T-SQL-Befehl aus: **Gewähren Sie dem Dienstprinzipal die notwendigen Berechtigungen**, wie bei SQL- oder anderen Benutzern üblich.
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Führen Sie den folgenden Code aus, oder machen Sie sich mit [weiteren Optionen](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017) vertraut. Wenn Sie PolyBase zum Laden der Daten verwenden möchten, machen Sie sich mit der [benötigten Datenbankberechtigung](#required-database-permission) vertraut. **Konfigurieren Sie einen mit Azure SQL Data Warehouse verknüpften Dienst** in Azure Data Factory.

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. Beispiel eines verknüpften Diensts mit Dienstprinzipalauthentifizierung


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a><a name="managed-identity"></a>Verwaltete Identitäten für Azure-Ressourcenauthentifizierung

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
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

### Eine Data Factory kann einer [verwalteten Identität für Azure-Ressourcen](data-factory-service-identity.md) zugeordnet werden, die diese spezielle Factory darstellt.

Sie können diese verwaltete Identität für die Azure SQL Data Warehouse-Authentifizierung verwenden. Die angegebene Factory kann mithilfe dieser Identität auf Daten in Ihrem Data Warehouse zugreifen und diese kopieren. Führen Sie die folgenden Schritte aus, um die Authentifizierung mit einer verwalteten Identität zu verwenden:

**[Geben Sie einen Azure Active Directory-Administrator](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** für Ihre Azure SQL Server-Instanz im Azure-Portal an, falls dies noch nicht geschehen ist.

1. Der Azure AD-Administrator kann ein Azure AD-Benutzer oder eine Azure AD-Gruppe sein. Wenn Sie der Gruppe mit der verwalteten Identität eine Administratorrolle zuweisen möchten, überspringen Sie die Schritte 3 und 4. Der Administrator erhält Vollzugriff auf die Datenbank. **[Erstellen Sie für eine eigenständige Datenbank Benutzer](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** für die verwaltete Data Factory-Identität.

2. Stellen Sie eine Verbindung mit dem Data Warehouse her, aus dem bzw. in das Sie Daten mithilfe von Tools wie SSMS kopieren möchten. Verwenden Sie dazu eine Azure AD-Identität, die mindestens die Berechtigung ALTER ANY USER aufweist. Führen Sie den folgenden T-SQL-Befehl aus. **Gewähren Sie der verwalteten Data Factory-Identität die notwendigen Berechtigungen**, und gehen Sie dabei so vor wie für SQL-Benutzer und andere Benutzer. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Führen Sie den folgenden Code aus, oder machen Sie sich mit [weiteren Optionen](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017) vertraut. Wenn Sie PolyBase zum Laden der Daten verwenden möchten, machen Sie sich mit der [benötigten Datenbankberechtigung](#required-database-permission) vertraut. **Konfigurieren Sie einen mit Azure SQL Data Warehouse verknüpften Dienst** in Azure Data Factory.

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Beispiel:**

Dataset-Eigenschaften

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md).

Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom Azure SQL Data Warehouse-Dataset unterstützt werden. Zum Kopieren von Daten aus oder nach Azure SQL Data Warehouse werden die folgenden Eigenschaften unterstützt:

Eigenschaft

| BESCHREIBUNG | Erforderlich | type |
|:--- |:--- |:--- |
| Die **type**-Eigenschaft des Datasets muss auf **AzureSqlDWTable** festgelegt sein. | Ja | tableName |
| Name der Tabelle oder Sicht in der Azure SQL Data Warehouse-Instanz, auf die der verknüpfte Dienst verweist | Quelle: Nein, Senke: Ja | Beispiel für Dataseteigenschaften |

#### <a name="dataset-properties-example"></a>Eigenschaften der Kopieraktivität

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md).

Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Azure SQL Data Warehouse-Quelle und -Senke unterstützt werden. Azure SQL Data Warehouse als Quelle

### <a name="azure-sql-data-warehouse-as-the-source"></a>Legen Sie zum Kopieren von Daten aus Azure SQL Data Warehouse die **type**-Eigenschaft der Quelle der Kopieraktivität auf **SqlDWSource** fest.

Die folgenden Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt: Eigenschaft

| BESCHREIBUNG | Erforderlich | type |
|:--- |:--- |:--- |
| Die **type**-Eigenschaft der Quelle der Kopieraktivität muss auf **SqlDWSource** festgelegt sein. | Ja | sqlReaderQuery |
| Verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. | Beispiel: `select * from MyTable`. Nein | sqlReaderStoredProcedureName |
| Name der gespeicherten Prozedur, die Daten aus der Quelltabelle liest. | Die letzte SQL-Anweisung muss eine SELECT-Anweisung in der gespeicherten Prozedur sein. Nein | storedProcedureParameters |
| Parameter für die gespeicherte Prozedur. | Zulässige Werte sind Namen oder Name-Wert-Paare.<br/>Die Namen und die Groß-/Kleinschreibung von Parametern müssen denen der Parameter der gespeicherten Prozedur entsprechen. Nein | Beachten Sie Folgendes |

### <a name="points-to-note"></a>Wenn **sqlReaderQuery** für **SqlSource** angegeben ist, führt die Kopieraktivität diese Abfrage für die Azure SQL Data Warehouse-Quelle aus, um die Daten abzurufen.

- Sie können auch eine gespeicherte Prozedur angeben. Geben Sie dazu **sqlReaderStoredProcedureName** und **storedProcedureParameters** an (sofern die gespeicherte Prozedur Parameter akzeptiert). Ohne Angabe von **sqlReaderQuery** oder **sqlReaderStoredProcedureName** werden die im Abschnitt **structure** des JSON-Codes des Datasets definierten Spalten zum Erstellen einer Abfrage verwendet.
- `select column1, column2 from mytable` wird für Azure SQL Data Warehouse ausgeführt. Falls die Datasetdefinition keinen Abschnitt **structure** enthält, werden alle Spalten der Tabelle ausgewählt. Beispiel für eine SQL-Abfrage

#### <a name="sql-query-example"></a>Beispiel für eine gespeicherte Prozedur

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>Definition der gespeicherten Prozedur

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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

### <a name="stored-procedure-definition"></a><a name="azure-sql-data-warehouse-as-sink"></a> Azure SQL Data Warehouse als Senke

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

### Legen Sie zum Kopieren von Daten nach Azure SQL Data Warehouse den Senkentyp in der Kopieraktivität auf **SqlDWSink** fest.

Die folgenden Eigenschaften werden im Abschnitt **sink** der Kopieraktivität unterstützt: Eigenschaft

| BESCHREIBUNG | Erforderlich | type |
|:--- |:--- |:--- |
| Die **type**-Eigenschaft der Senke der Kopieraktivität muss auf **SqlDWSink** festgelegt sein. | Ja | allowPolyBase |
| Gibt an, ob (falls zutreffend) PolyBase anstelle des BULKINSERT-Mechanismus verwendet werden soll. | Das empfohlene Verfahren zum Laden von Daten in SQL Data Warehouse ist PolyBase. <br/><br/> Einschränkungen und Einzelheiten finden Sie im Abschnitt [Daten unter Verwendung von PolyBase in Azure SQL Data Warehouse laden](#use-polybase-to-load-data-into-azure-sql-data-warehouse). Zulässige Werte sind **true** und **false** (Standard).<br/><br/>Nein  | polyBaseSettings |
| Eine Gruppe von Eigenschaften, die angegeben werden können, wenn die **allowPolybase**-Eigenschaft auf **true** festgelegt ist. | Nein | rejectValue |
| Gibt die Anzahl oder den Prozentsatz von Zeilen an, die abgelehnt werden können, bevor für die Abfrage ein Fehler auftritt. | Weitere Informationen zu den PolyBase-Ablehnungsoptionen finden Sie im Abschnitt „Argumente“ in [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx).<br/><br/>Zulässige Werte sind „0“ (Standard), „1“, „2“ usw. <br/><br/>Nein |rejectType |
| Gibt an, ob die **rejectValue**-Option als Literalwert oder Prozentsatz angegeben ist. | Zulässige Werte sind **Value** (Standard) und **Percentage**.<br/><br/>Nein | rejectSampleValue |
| Gibt die Anzahl von Zeilen an, die abgerufen werden, bevor PolyBase den Prozentsatz der abgelehnten Zeilen neu berechnet. | Zulässige Werte sind „1“, „2“ usw.<br/><br/>Ja, wenn für **rejectType** der Wert **percentage** festgelegt ist. | useTypeDefault |
| Gibt an, wie fehlende Werte in durch Trennzeichen getrennten Textdateien verarbeitet werden sollen, wenn PolyBase Daten aus der Textdatei abruft. | Weitere Informationen zu dieser Eigenschaft finden Sie im Abschnitt zu Argumenten im Thema [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx)verwenden können.<br/><br/>Zulässige Werte sind **true** und **false** (Standard).<br/><br/>**Sehen Sie sich die [Tipps zur Problembehandlung](#polybase-troubleshooting) für diese Einstellung an.**<br><br>Nein | writeBatchSize |
| Anzahl der Zeilen, die in die SQL-Tabelle **pro Batch** eingefügt werden sollen. | Ist nur anwendbar, wenn PolyBase nicht verwendet wird. Zulässiger Wert: **integer** (Anzahl der Zeilen)<br/><br/>Standardmäßig bestimmt Data Factory die geeignete Batchgröße dynamisch anhand der Zeilengröße. Nein | writeBatchTimeout |
| Wartezeit für den Abschluss der Batcheinfügung, bis das Timeout wirksam wird. Ist nur anwendbar, wenn PolyBase nicht verwendet wird. | Zulässiger Wert: **timespan**.<br/><br/>Beispiel: „00:30:00“ (30 Minuten). Nein | preCopyScript |
| Geben Sie eine auszuführende SQL-Abfrage für die Kopieraktivität an, ehe Sie bei der jeder Ausführung Daten in Azure SQL Data Warehouse schreiben. | Sie können diese Eigenschaft nutzen, um vorab geladene Daten zu bereinigen. Nein | Beispiel für eine SQL Data Warehouse-Senke |

#### <a name="sql-data-warehouse-sink-example"></a>Im nächsten Abschnitt erfahren Sie mehr über das effiziente Laden in SQL Data Warehouse mithilfe von PolyBase.

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

Daten unter Verwendung von PolyBase in Azure SQL Data Warehouse laden

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Mit [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) lassen sich große Datenmengen effizient und mit hohem Durchsatz in Azure SQL Data Warehouse laden.

Wenn Sie PolyBase anstelle des standardmäßigen BULKINSERT-Mechanismus verwenden, wird der Durchsatz erheblich gesteigert. Einen ausführlichen Vergleich finden Sie unter [Leistungsreferenz](copy-activity-performance.md#performance-reference). Eine exemplarische Vorgehensweise mit einem Anwendungsfall finden Sie unter [Laden von 1 TB in Azure SQL Data Warehouse](v1/data-factory-load-sql-data-warehouse.md). Wenn sich Ihre Quelldaten in **Azure Blob, Azure Data Lake Storage Gen1 oder Azure Data Lake Storage Gen2** befinden und das **Format PolyBase-kompatibel ist**, können Sie mithilfe der Copy-Aktivität PolyBase direkt aufrufen, damit Azure SQL Data Warehouse die Daten aus der Quelle abrufen kann.

* Details finden Sie unter **[Direktes Kopieren mithilfe von PolyBase](#direct-copy-by-using-polybase)** . Wenn der Speicher und das Format der Quelldaten von PolyBase ursprünglich nicht unterstützt werden, können Sie stattdessen das Feature **[Gestaffeltes Kopieren mit PolyBase](#staged-copy-by-using-polybase)** verwenden.
* Das gestaffelte Kopieren bietet auch einen höheren Durchsatz. Dabei werden die Daten automatisch in ein PolyBase-kompatibles Format konvertiert. Außerdem werden die Daten in Azure Blob Storage gespeichert. Anschließend werden die Daten in SQL Data Warehouse geladen. Weitere Informationen finden Sie unter [Bewährte Methoden für die Verwendung von PolyBase](#best-practices-for-using-polybase).

>[!TIP]
>Direktes Kopieren mithilfe von PolyBase

### <a name="direct-copy-by-using-polybase"></a>Für SQL Data Warehouse unterstützt PolyBase Azure Blob, Azure Data Lake Storage Gen1 und Azure Data Lake Storage Gen2 direkt.

Wenn Ihre Daten die in diesem Abschnitt beschriebenen Kriterien erfüllen, können Sie mithilfe von PolyBase direkt aus dem Quelldatenspeicher in Azure SQL Data Warehouse kopieren. Andernfalls können Sie das [gestaffelte Kopieren mit PolyBase](#staged-copy-by-using-polybase) verwenden. Weitere Informationen zum effizienten Kopieren von Daten nach SQL Data Warehouse finden Sie unter [Wenn Data Lake Store mit SQL Data Warehouse verwendet wird, lassen sich mit Azure Data Factory noch einfacher Erkenntnisse aus Daten gewinnen](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

> [!TIP]
> Falls die Anforderungen nicht erfüllt werden, überprüft Azure Data Factory die Einstellungen und greift bei der Datenverschiebung automatisch auf den BULKINSERT-Mechanismus zurück.

Der **mit der Quelle verknüpfte Dienst** verfügt über die folgenden Typen und Authentifizierungsmethoden:

1. Unterstützter Quelldatenspeichertyp

    | Unterstützter Quellauthentifizierungstyp | [Azure-Blob](connector-azure-blob-storage.md) |
    |:--- |:--- |
    | Kontoschlüsselauthentifizierung, Authentifizierung mit einer verwalteten Identität | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) |
    | Dienstprinzipalauthentifizierung | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) |
    | Kontoschlüsselauthentifizierung, Authentifizierung mit einer verwalteten Identität | Wenn Ihre Azure Storage-Instanz mit einem VNET-Dienstendpunkt konfiguriert ist, müssen Sie die Authentifizierung mit einer verwalteten Identität verwenden. |

    >[!IMPORTANT]
    >Weitere Informationen finden Sie unter [Auswirkungen der Verwendung von VNET-Dienstendpunkten mit Azure Storage](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Das **Quelldatenformat** lautet **Parquet**, **ORC** oder **Durch Trennzeichen getrennter Text** – mit den folgenden Konfigurationen:

2. Der Ordnerpfad enthält keinen Platzhalterfilter.

   1. Der Dateiname verweist auf eine einzelne Datei bzw. entspricht `*` oder `*.*`.
   2. `rowDelimiter` muss **\n** sein.
   3. `nullValue` ist entweder auf **eine leere Zeichenfolge** („“) festgelegt, oder der Standardwert wird übernommen und `treatEmptyAsNull` wird als Standardwert übernommen oder auf „true“ festgelegt.
   4. `encodingName` ist auf **utf-8** festgelegt. (Dies ist der Standardwert.)
   5. `quoteChar`, `escapeChar` und `skipLineCount` wurden nicht angegeben.
   6. PolyBase unterstützt das Überspringen der Kopfzeile, dies kann in ADF als `firstRowAsHeader` konfiguriert werden. `compression` kann auf **keine Komprimierung** oder auf **Gzip** oder **Deflate** (Verkleinern) festgelegt sein.
   7. Gestaffeltes Kopieren mit PolyBase

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>Falls Ihre Quelldaten die Kriterien aus dem vorherigen Abschnitt nicht erfüllen, können Sie die Daten über eine zwischengeschaltete Azure Blob Storage-Instanz mit Staging kopieren.

Azure Storage Premium kann nicht verwendet werden. In diesem Fall führt Azure Data Factory automatisch Transformationen der Daten durch, damit das Datenformat dem von PolyBase entspricht. Anschließend werden die Daten mithilfe von PolyBase in SQL Data Warehouse geladen. Abschließend werden Sie die temporären Daten in Blob Storage bereinigt. Unter [Gestaffeltes Kopieren](copy-activity-performance.md#staged-copy) finden Sie ausführliche Informationen zum Kopieren von Daten über eine Azure Blob Storage-Instanz mit Staging. Um dieses Feature verwenden zu können, erstellen Sie einen [mit Azure Storage verknüpften Dienst](connector-azure-blob-storage.md#linked-service-properties), der auf das Azure Storage-Konto mit der zwischengeschalteten Blob Storage-Instanz verweist.

Geben Sie dann die Eigenschaften `enableStaging` und `stagingSettings` für die Kopieraktivität wie im folgenden Code gezeigt an: Bewährte Methoden für die Verwendung von PolyBase

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

## <a name="best-practices-for-using-polybase"></a>Die folgenden Abschnitte enthalten bewährte Methoden zusätzlich zu den in [Bewährte Methoden für Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md) aufgeführten Vorgängen.

Erforderliche Datenbankberechtigungen

### <a name="required-database-permission"></a>Um PolyBase verwenden zu können, muss der zum Laden von Daten in SQL Data Warehouse verwendete Benutzer über die [Berechtigung „CONTROL“](https://msdn.microsoft.com/library/ms191291.aspx) für die Zieldatenbank verfügen.

Sie können dies beispielsweise erreichen, indem Sie diesen Benutzer als Mitglied der Rolle **db_owner** hinzufügen. Die Vorgehensweise finden Sie in der [Übersicht über SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization). Beschränkungen hinsichtlich Zeilengröße und Datentyp

### <a name="row-size-and-data-type-limits"></a>In PolyBase sind Ladevorgänge auf Zeilen beschränkt, die kleiner als 1 MB sind.

Die Anwendung kann nicht für das Laden von VARCHR(MAX), NVARCHAR(MAX) oder VARBINARY(MAX) genutzt werden. Weitere Informationen finden Sie unter [SQL Data Warehouse – Dienstkapazitätslimits](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads). Wenn die Quelldaten Zeilen enthalten, die größer als 1 MB sind, empfiehlt es sich, die Quelltabellen vertikal in mehrere kleine Tabellen zu teilen.

Stellen Sie sicher, dass die längste Zeile den Grenzwert nicht überschreitet. Die kleineren Tabellen können dann mit PolyBase geladen und in Azure SQL Data Warehouse zusammengeführt werden. Alternativ können Sie für Daten mit breiten Spalten dieser Art eine andere Anwendung als PolyBase nutzen, um die Daten per ADF zu laden. Deaktivieren Sie hierzu die Einstellung „allow PolyBase“.

Problembehandlung in PolyBase

### <a name="polybase-troubleshooting"></a>**Laden in die Spalte „Decimal“**

Wenn Ihre Quelldaten im Textformat vorliegen oder sich in anderen nicht mit PolyBase kompatiblen Speichern befinden (unter Verwendung von gestaffeltem Kopieren und PolyBase) und einen leeren Wert enthalten, der in die Spalte „Decimal“ von SQL Data Warehouse geladen werden soll, tritt ggf. der folgende Fehler auf:

Die Lösung besteht darin, die Option „**Use type default**“ in der Senke der Kopieraktivität unter den PolyBase-Einstellungen zu deaktivieren (Festlegung auf „false“).

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

„[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)“ ist eine native PolyBase-Konfiguration, mit der die Behandlung von fehlenden Werten in durch Trennzeichen getrennten Textdateien angegeben wird, wenn PolyBase Daten aus der Textdatei abruft. **Andere** 

Informationen zu weiteren bekannten PolyBase-Problemen finden Sie im Abschnitt zur [Problembehandlung für PolyBase-Ladevorgänge in Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md#polybase).

SQL Data Warehouse-Ressourcenklasse

### <a name="sql-data-warehouse-resource-class"></a>Um einen optimalen Durchsatz zu erzielen, sollten Sie dem Benutzer, der zum Laden von Daten in SQL Data Warehouse über PolyBase verwendet wird, eine größere Ressourcenklasse zuweisen.

**tableName** in Azure SQL Data Warehouse

### <a name="tablename-in-azure-sql-data-warehouse"></a>Die folgende Tabelle enthält Beispiele zum Angeben der **tableName**-Eigenschaft im JSON-Dataset.

Es zeigt verschiedene Kombinationen von Schema und Tabellennamen. Datenbankschema

| Tabellenname | JSON-Eigenschaft **tableName** | dbo |
| --- | --- | --- |
| MyTable | MyTable oder dbo.MyTable oder [dbo].[MyTable] | dbo1 |
| MyTable | dbo1.MyTable oder [dbo1].[MyTable] | dbo |
| My.Table | [My.Table] oder [dbo].[My.Table] | dbo1 |
| My.Table | [dbo1].[My.Table] | Sollte der folgende Fehler auftreten, liegt dies unter Umständen am Wert für die **tableName**-Eigenschaft. |

Informationen zur korrekten Angabe von Werten für die JSON-Eigenschaft **tableName** finden Sie in der Tabelle oben. Spalten mit Standardwerten

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Das PolyBase-Feature in Data Factory akzeptiert aktuell lediglich dieselbe Anzahl von Spalten wie in der Zieltabelle.

Beispiel: Sie verfügen über eine Tabelle mit vier Spalten, von denen eine mit einem Standardwert definiert ist. Die Eingabedaten müssen weiterhin vier Spalten aufweisen. Bei Bereitstellung eines Eingabedatasets mit drei Spalten tritt ein Fehler wie der folgende auf: Der NULL-Wert ist eine Sonderform des Standardwerts.

```
All columns of the table must be specified in the INSERT BULK statement.
```

Wenn die Spalte NULL-Werte zulässt, können die Eingabedaten im Blob für diese Spalte leer sein. Sie dürfen im Eingabedataset aber nicht fehlen. PolyBase fügt für fehlende Daten in Azure SQL Data Warehouse einen NULL-Wert ein. Eigenschaften von Mapping Data Flow

## <a name="mapping-data-flow-properties"></a>Ausführliche Informationen hierzu finden Sie unter [Quellentransformation](data-flow-source.md) und [Senkentransformation](data-flow-sink.md) in Mapping Data Flow.

Datentypzuordnung für Azure SQL Data Warehouse

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Beim Kopieren von Daten aus bzw. nach Azure SQL Data Warehouse werden die folgenden Zuordnungen von Azure SQL Data Warehouse-Datentypen zu Azure Data Factory-Zwischendatentypen verwendet.

Unter [Schema- und Datentypzuordnungen](copy-activity-schema-and-type-mapping.md) erfahren Sie, wie Sie Aktivitätszuordnungen für Quellschema und Datentyp in die Senke kopieren. Der Artikel [Tabellendatentypen in Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md) enthält Informationen zu den von SQL DW unterstützten Datentypen und zu den Problemumgehungen für Datentypen, die nicht unterstützt werden.

>[!TIP]
>Azure SQL Data Warehouse-Datentyp

| Data Factory-Zwischendatentyp | bigint |
|:--- |:--- |
| Int64 | binary |
| Byte[] | bit |
| Boolean | char |
| String, Char[] | date |
| DateTime | DateTime |
| DateTime | datetime2 |
| DateTime | Datetimeoffset |
| DateTimeOffset | Decimal |
| Decimal | FILESTREAM attribute (varbinary(max)) |
| Byte[] | Float |
| Double | image |
| Byte[] | int |
| Int32 | money |
| Decimal | nchar |
| String, Char[] | numeric |
| Decimal | nvarchar |
| String, Char[] | real |
| Single | rowversion |
| Byte[] | smalldatetime |
| DateTime | smallint |
| Int16 | smallmoney |
| Decimal | time |
| TimeSpan | tinyint |
| Byte | uniqueidentifier |
| Guid | varbinary |
| Byte[] | varchar |
| String, Char[] | Nächste Schritte |

## <a name="next-steps"></a>Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher und -formate](copy-activity-overview.md##supported-data-stores-and-formats).
For a list of data stores supported as sources and sinks by Copy Activity in Azure Data Factory, see <bpt id="p1">[</bpt>supported data stores and formats<ept id="p1">](copy-activity-overview.md##supported-data-stores-and-formats)</ept>.

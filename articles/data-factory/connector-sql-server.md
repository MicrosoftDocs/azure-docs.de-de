---
title: Kopieren von Daten in/aus SQL Server mithilfe von Azure Data Factory | Microsoft-Dokumentation
description: Informationen zum Verschieben von Daten in und aus einer SQL Server-Datenbank, die lokal oder mithilfe von Azure Data Factory in einer Azure-VM gehostet wird.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: jingwang
ms.openlocfilehash: d28f6ed1957f8f6ae7ff7eb49f8ce4cbdec62266
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147420"
---
# <a name="copy-data-to-and-from-sql-server-using-azure-data-factory"></a>Kopieren von Daten in und aus SQL Server mithilfe von Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-sqlserver-connector.md)
> * [Aktuelle Version](connector-sql-server.md)

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten in und aus einer SQL Server-Datenbank zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Sie können Daten aus einem beliebigen unterstützten Senkendatenspeicher in eine oder aus einer SQL Server-Datenbank in einen beliebigen unterstützten Quelldatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser SQL Server-Connector unterstützt insbesondere Folgendes:

- Die SQL Server-Versionen 2016, 2014, 2012, 2008 R2, 2008 und 2005
- Kopieren von Dateien unter Verwendung der Authentifizierung des Typs **SQL** oder **Windows**
- Als Quelle das Abrufen von Daten mithilfe einer SQL-Abfrage oder gespeicherten Prozedur.
- Als Senke das Anfügen von Daten an die Zieltabelle oder Aufrufen einer gespeicherten Prozedur mit benutzerdefinierter Logik während des Kopiervorgangs

SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) wird derzeit nicht unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

Zum Kopieren von Daten aus einer SQL Server-Datenbank, die nicht öffentlich zugänglich ist, müssen Sie eine selbstgehostete Integration Runtime einrichten. Im Artikel [Selbstgehostete Integration Runtime](create-self-hosted-integration-runtime.md) finden Sie Details. Die Integration Runtime bietet einen integrierten SQL Server-Datenbanktreiber. Daher müssen beim Kopieren von Daten in bzw. aus einer SQL Server-Datenbank keine Treiber manuell installiert werden.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den SQL Server-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit SQL Server verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **SqlServer** | Ja |
| connectionString |Geben Sie „connectionString“-Informationen an, die zum Herstellen einer Verbindung mit der SQL Server-Datenbank mithilfe der SQL- oder Windows-Authentifizierung benötigt werden. Sehen Sie sich die folgenden Beispiele an.<br/>Markieren Sie dieses Feld als „SecureString“, um es sicher in Data Factory zu speichern. Sie können auch das Kennwort in Azure Key Vault speichern und bei Verwendung der SQL-Authentifizierung die `password`-Konfiguration aus der Verbindungszeichenfolge pullen. Ausführlichere Informationen finden Sie im JSON-Beispiel unter der Tabelle und im Artikel [Speichern von Anmeldeinformationen in Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| userName |Geben Sie den Benutzernamen an, wenn Sie die Windows-Authentifizierung verwenden. Beispiel: **Domainname\\Benutzername**. |Nein  |
| password |Geben Sie das Kennwort für das Benutzerkonto an, das Sie für „userName“ angegeben haben. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). |Nein  |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden muss. Sie können die selbstgehostete Integration Runtime oder Azure Integration Runtime verwenden (sofern Ihr Datenspeicher öffentlich zugänglich ist). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein  |

>[!TIP]
>Wenn ein Fehler mit dem Fehlercode „UserErrorFailedToConnectToSqlServer“ auftritt und eine Meldung wie „Das Sitzungslimit für die Datenbank ist XXX und wurde erreicht“ angezeigt wird, fügen Sie `Pooling=false` zu Ihrer Verbindungszeichenfolge hinzu, und versuchen Sie es erneut.

**Beispiel 1: SQL-Authentifizierung**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Beispiel 2: Verwenden der SQL-Authentifizierung mit Kennwort in Azure Key Vault**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
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

**Beispiel 3: Verwenden der Windows-Authentifizierung**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;"
            },
            "userName": "<domain\\username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
     }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu Datasets. Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom Dataset „SQL Server“ unterstützt werden.

Zum Kopieren von Daten aus der bzw. in die SQL Server-Datenbank werden die folgenden Eigenschaften unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **SqlServerTable** | Ja |
| tableName |Name der Tabelle oder Sicht in der SQL Server-Datenbankinstanz, auf die der verknüpfte Dienst verweist. | Quelle: Nein, Senke: Ja |

**Beispiel:**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Quelle und Senke „SQL Server“ unterstützt werden.

### <a name="sql-server-as-source"></a>SQL Server als Quelle

Legen Sie zum Kopieren von Daten aus SQL Server den Quellentyp in der Kopieraktivität auf **SqlSource** fest. Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **SqlSource** | Ja |
| sqlReaderQuery |Verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. Beispiel: `select * from MyTable`. |Nein  |
| sqlReaderStoredProcedureName |Der Name der gespeicherten Prozedur, die Daten aus der Quelltabelle liest. Die letzte SQL-Anweisung muss eine SELECT-Anweisung in der gespeicherten Prozedur sein. |Nein  |
| storedProcedureParameters |Parameter für die gespeicherte Prozedur.<br/>Zulässige Werte: Name/Wert-Paare. Die Namen und die Groß-/Kleinschreibung von Parametern müssen denen der Parameter der gespeicherten Prozedur entsprechen. |Nein  |

**Beachten Sie Folgendes:**

- Wenn **sqlReaderQuery** für „SqlSource“ angegeben ist, wendet die Kopieraktivität diese Abfrage auf die Quelle „SQL Server“ an, um die Daten abzurufen. Alternativ dazu können Sie eine gespeicherte Prozedur angeben, indem Sie **sqlReaderStoredProcedureName** und **storedProcedureParameters** angeben (sofern die gespeicherten Prozeduren Parameter verwenden).
- Ohne Angabe von „sqlReaderQuery“ oder „sqlReaderStoredProcedureName“ werden die im Abschnitt „structure“ des JSON-Codes des Datasets definierten Spalten zum Erstellen einer Abfrage (`select column1, column2 from mytable`) verwendet, die auf die SQL Server-Datenbank angewendet wird. Falls die Datasetdefinition nicht den Abschnitt „structure“ enthält, werden alle Spalten der Tabelle ausgewählt.

**Beispiel: Verwenden von SQL-Abfragen**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Beispiel: Verwenden von gespeicherten Prozeduren**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
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

**Die Definition der gespeicherten Prozedur:**

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

### <a name="sql-server-as-sink"></a>SQL Server als Senke

Legen Sie zum Kopieren von Daten in SQL Server den Senkentyp in der Kopieraktivität auf **SqlSink** fest. Folgende Eigenschaften werden im Abschnitt **sink** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Senke der Kopieraktivität muss auf Folgendes festgelegt sein: **SqlSink** | Ja |
| writeBatchSize |Anzahl der Zeilen, die in die SQL-Tabelle **pro Batch** eingefügt werden sollen.<br/>Zulässige Werte: Ganze Zahlen (Anzahl der Zeilen). Standardmäßig bestimmt Data Factory die geeignete Batchgröße dynamisch anhand der Zeilengröße. |Nein  |
| writeBatchTimeout |Die Wartezeit für den Abschluss der Batcheinfügung, bis das Timeout wirksam wird.<br/>Zulässige Werte: Zeitraum Beispiel: „00:30:00“ (30 Minuten). |Nein  |
| preCopyScript |Geben Sie eine auszuführende SQL-Abfrage für die Copy-Aktivität an, ehe Daten in SQL Server geschrieben werden. Sie wird pro Ausführung der Copy-Aktivität nur einmal aufgerufen. Sie können diese Eigenschaft nutzen, um die vorab geladenen Daten zu bereinigen. |Nein  |
| sqlWriterStoredProcedureName |Der Name der gespeicherten Prozedur, die definiert, wie Quelldaten in der Zieltabelle angewendet werden (etwa durch Ausführen von Upserts oder Transformationen mit eigener Geschäftslogik). <br/><br/>Beachten Sie, dass diese gespeicherte Prozedur **pro Batch aufgerufen wird**. Verwenden Sie bei einem Vorgang, der nur einmal ausgeführt wird und nicht mit Quelldaten in Zusammenhang steht (etwa Löschen/Kürzen), die `preCopyScript`-Eigenschaft. |Nein  |
| storedProcedureParameters |Parameter für die gespeicherte Prozedur.<br/>Zulässige Werte: Name/Wert-Paare. Die Namen und die Groß-/Kleinschreibung von Parametern müssen denen der Parameter der gespeicherten Prozedur entsprechen. |Nein  |
| sqlWriterTableType |Geben Sie einen Tabellentypnamen an, der in der gespeicherten Prozedur verwendet werden soll. Die Kopieraktivität macht die verschobenen Daten in einer temporären Tabelle mit diesem Tabellentyp verfügbar. Der gespeicherte Prozedurcode kann dann die kopierten Daten mit vorhandenen Daten zusammenführen. |Nein  |

> [!TIP]
> Beim Kopieren von Daten in SQL Server fügt die Kopieraktivität Daten standardmäßig an die Senkentabelle an. Um einen UPSERT-Vorgang oder zusätzliche Geschäftslogik auszuführen, verwenden Sie die in „SqlSink“ gespeicherte Prozedur. Weitere Informationen finden Sie unter [Aufrufen einer gespeicherten Prozedur für die SQL-Senke](#invoking-stored-procedure-for-sql-sink).

**Beispiel 1: Anfügen von Daten**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Beispiel 2: Aufrufen einer gespeicherten Prozedur während des Kopierens für den upsert-Vorgang**

Weitere Informationen finden Sie unter [Aufrufen einer gespeicherten Prozedur für die SQL-Senke](#invoking-stored-procedure-for-sql-sink).

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="identity-columns-in-the-target-database"></a>Identitätsspalten in der Zieldatenbank

Dieser Abschnitt enthält ein Beispiel zum Kopieren von Daten aus einer Quelltabelle ohne eine Identitätsspalte in eine Zieltabelle mit einer Identitätsspalte.

**Quelltabelle:**

```sql
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```

**Zieltabelle:**

```sql
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```

Beachten Sie, dass die Zieltabelle über eine Identitätsspalte verfügt.

**Definition der Quell-Dataset-JSON**

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTbl"
        }
    }
}
```

**Definition der Ziel-Dataset-JSON**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "TargetTbl"
        }
    }
}
```

Beachten Sie, dass die Quell- und die Zieltabelle unterschiedliche Schemas besitzen (das Ziel verfügt über eine zusätzliche Spalte mit der Identität). In diesem Szenario müssen Sie eine **structure** -Eigenschaft in der Definition des Zieldatasets angeben, die nicht die Identitätsspalte enthält.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Aufrufen der gespeicherten Prozedur von der SQL-Senke

Beim Kopieren von Daten in eine SQL Server-Datenbank kann eine vom Benutzer angegebene gespeicherte Prozedur konfiguriert und mit zusätzlichen Parametern aufgerufen werden.

Eine gespeicherte Prozedur kann genutzt werden, wenn integrierte Kopiermechanismen nicht den Zweck erfüllen. Sie wird in der Regel genutzt, wenn ein Upsert-Vorgang (Insert + Update, Einfügen + Aktualisieren) oder eine zusätzliche Verarbeitung (Zusammenführen von Spalten, Suchen nach zusätzlichen Werten, Einfügen in mehrere Tabellen usw.) vor dem endgültigen Einfügen von Quelldaten in die Zieltabelle durchgeführt werden muss.

Das folgende Beispiel zeigt, wie Sie eine gespeicherte Prozedur verwenden, um einen einfachen Upsert-Vorgang in eine Tabelle in der SQL Server-Datenbank auszuführen. Wenn Eingabedaten vorhanden sind und die Senkentabelle **Marketing** heißt, sind drei Spalten vorhanden: **ProfileID**, **State** und **Category**. Führen Sie den Upsert-Vorgang basierend auf der Spalte **ProfileID** aus, und wenden Sie ihn nur auf eine bestimmte Kategorie an.

**Ausgabedataset:** Der „tableName“ sollte in Ihrer gespeicherten Prozedur denselben Tabellentypparameternamen haben. Im Folgenden finden Sie das Skript für die gespeicherte Prozedur:

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Definieren Sie den Abschnitt **SqlSink** in der Kopieraktivität wie folgt.

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

Definieren Sie die gespeicherte Prozedur in der Datenbank mit demselben Namen wie **SqlWriterStoredProcedureName**. Sie verarbeitet die Eingabedaten aus der angegebenen Quelle und führt sie mit der Ausgabetabelle zusammen. Der Parametername des Tabellentyps in der gespeicherten Prozedur muss mit dem Element **tableName** identisch sein, das im Dataset definiert ist.

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

Definieren Sie in der Datenbank den Tabellentyp mit demselben Namen wie „sqlWriterTableType“. Beachten Sie, dass das Schema des Tabellentyps mit dem Schema übereinstimmen sollte, das von den Eingabedaten zurückgegeben wird.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

Das Feature der gespeicherten Prozedur nutzt [Tabellenwertparameter](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-sql-server"></a>Datentypzuordnung für SQL Server

Beim Kopieren von Daten in/aus SQL Server werden die folgenden Zuordnungen von SQL Server-Datentypen zu Azure Data Factory-Zwischendatentypen verwendet. Unter [Schema- und Datentypzuordnungen](copy-activity-schema-and-type-mapping.md) erfahren Sie, wie Sie Aktivitätszuordnungen für Quellschema und Datentyp in die Senke kopieren.

| SQL Server-Datentyp | Data Factory-Zwischendatentyp |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

>[!NOTE]
> Für Datentypen, die dem Zwischendatentyp „Decimal“ zugeordnet sind, unterstützt ADF derzeit eine maximale Genauigkeit von 28. Daten mit einer höheren Genauigkeit müssen in SQL-Abfragen ggf. in eine Zeichenfolge konvertiert werden.

## <a name="troubleshooting-connection-issues"></a>Beheben von Verbindungsproblemen

1. Konfigurieren Sie Ihren SQL-Server, um Remoteverbindungen zu akzeptieren. Starten Sie **SQL Server Management Studio**, klicken Sie mit der rechten Maustaste auf **Server**, und klicken Sie dann auf **Eigenschaften**. Wählen Sie in der Liste den Eintrag **Verbindungen** aus, und aktivieren Sie das Kontrollkästchen **Remoteverbindungen mit diesem Server zulassen**.

    ![Aktivieren von Remoteverbindungen](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Ausführliche Schritte finden Sie im Abschnitt zum [Konfigurieren der Serverkonfigurationsoption für den Remotezugriff](https://msdn.microsoft.com/library/ms191464.aspx) .

2. Starten Sie den **SQL Server-Konfigurations-Manager**. Erweitern Sie **SQL Server-Netzwerkkonfiguration** für die gewünschte Instanz, und wählen Sie **Protokolle für MSSQLSERVER** aus. Im rechten Bereich werden Protokolle angezeigt. Aktivieren Sie TCP/IP, indem Sie mit der rechten Maustaste auf **TCP/IP** klicken und anschließend auf **Aktivieren** klicken.

    ![TCP/IP aktivieren](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Weitere Methoden zum Aktivieren des TCP/IP-Protokolls finden Sie unter [Aktivieren oder Deaktivieren eines Servernetzwerkprotokolls](https://msdn.microsoft.com/library/ms191294.aspx) .

3. Doppelklicken Sie im gleichen Fenster auf **TCP/IP**, um das Fenster **TCP/IP-Eigenschaften** zu öffnen.
4. Wechseln Sie zur Registerkarte **IP-Adressen** . Scrollen Sie nach unten zum Abschnitt **IPAll** . Notieren Sie sich den **TCP-Port** (Standardwert ist **1433**).
5. Erstellen Sie auf dem Computer eine **Regel für die Windows-Firewall** , um eingehenden Datenverkehr über diesen Port zuzulassen.  
6. **Überprüfen der Verbindung**: Verwenden Sie SQL Server Management Studio auf einem anderen Computer, um mit dem vollqualifizierten Namen eine Verbindung mit der SQL Server-Instanz herzustellen. Beispiel: `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md##supported-data-stores-and-formats).

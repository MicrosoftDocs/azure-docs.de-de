---
title: Kopieren von Daten aus einer SAP-Tabelle
description: Erfahren Sie, wie Daten aus einer SAP-Tabelle mithilfe einer Kopieraktivität in einer Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/01/2021
ms.openlocfilehash: e4f756631b51ce9c5fba32939d1c6651e7b328d0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378518"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Kopieren von Daten aus einer SAP-Tabelle mithilfe von Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus einer SAP-Tabelle zu kopieren. Weitere Informationen finden Sie im Artikel [Übersicht über die Kopieraktivität](copy-activity-overview.md).

>[!TIP]
>Informationen zur allgemeinen Unterstützung des SAP-Datenintegrationsszenarios durch ADF finden Sie im [Whitepaper zur SAP-Datenintegration mit Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf). Dort finden Sie auch eine detaillierte Einführung in jeden SAP-Connector, einen Vergleich und Anleitungen.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser SAP-Tabellenconnector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus einer SAP-Tabelle in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser SAP-Tabellenconnector unterstützt insbesondere Folgendes:

- Kopieren von Daten aus einer SAP-Tabelle in:

  - SAP ERP Central Component (SAP ECC), Version 7.01 oder höher (in einem aktuellen SAP Support Package Stack, das nach 2015 veröffentlicht wurde).
  - SAP Business Warehouse (SAP BW), Version 7.01 oder höher (in einem aktuellen SAP Support Package Stack, das nach 2015 veröffentlicht wurde).
  - SAP S/4HANA.
  - Andere Produkte in SAP Business Suite, Version 7.01 oder höher (in einem aktuellen SAP Support Package Stack, das nach 2015 veröffentlicht wurde).

- Kopieren von Daten aus einer transparenten SAP-Tabelle, einer in einem Pool zusammengefassten Tabelle, einer gruppierten Tabelle und einer Sicht.
- Kopieren von Daten mithilfe von Standardauthentifizierung oder Secure Network Communications (SNC), falls SNC konfiguriert ist.
- Herstellen einer Verbindung mit einem SAP-Anwendungsserver oder einem SAP-Nachrichtenserver.
- Abrufen von Daten über die standardmäßige oder benutzerdefinierte RFC.

Die Version 7.01 oder höher verweist auf die SAP NetWeaver-Version statt auf die SAP ECC-Version. Beispielsweise verfügt SAP ECC 6.0 EHP 7 im Allgemeinen über die NetWeaver-Version > = 7.4. Falls Sie sich bezüglich Ihrer Umgebung unsicher sind, finden Sie hier die Schritte zur Bestätigung der Version aus Ihrem SAP-System:

1. Stellen Sie per SAP GUI eine Verbindung mit dem SAP-System her. 
2. Wechseln Sie zu **System** -> **Status**. 
3. Überprüfen Sie die Version von SAP_BASIS, und stellen Sie sicher, dass sie mindestens 701 ist.  
      ![Überprüfen von SAP_BASIS](./media/connector-sap-table/sap-basis.png)

## <a name="prerequisites"></a>Voraussetzungen

Zum Verwenden dieses SAP-Tabellenconnectors müssen Sie folgende Schritte ausführen:

- Richten Sie eine selbstgehostete Integration Runtime (Version 3.17 oder höher) ein. Weitere Informationen finden Sie unter [Erstellen und Konfigurieren einer selbstgehosteten Integration Runtime](create-self-hosted-integration-runtime.md).

- Laden Sie den [SAP Connector für Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) (64 Bit) von der SAP-Website herunter, und installieren Sie ihn auf dem Computer mit der selbstgehosteten Integration Runtime. Während der Installation müssen Sie im Fenster **Optionale Einrichtungsschritte** die Option **Assemblys in GAC installieren** auswählen.

  ![Installieren des SAP-Connectors für .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Der SAP-Benutzer, der im SAP-Tabellenconnector von Data Factory verwendet wird, muss über die folgenden Berechtigungen verfügen:

  - Autorisierung für die Verwendung von RFC-Zielen (Remote Function Call).
  - Berechtigungen für die Aktivität „Execute“ des Autorisierungsobjekts „S_SDSAUTH“.

## <a name="get-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren der Data Factory-Entitäten speziell für den SAP-Tabellenconnector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit SAP BW Open Hub verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| `type` | Die `type`-Eigenschaft muss auf `SapTable` festgelegt werden. | Ja |
| `server` | Der Name des Servers, auf dem sich die SAP-Instanz befindet.<br/>Verwenden Sie sie zum Herstellen einer Verbindung mit einem SAP-Anwendungsserver. | Nein |
| `systemNumber` | Die Systemnummer des SAP-Systems.<br/>Verwenden Sie sie zum Herstellen einer Verbindung mit einem SAP-Anwendungsserver.<br/>Zulässiger Wert: Eine zweistellige Dezimalzahl, die als Zeichenfolge angegeben wird. | Nein |
| `messageServer` | Der Hostname des SAP-Nachrichtenservers.<br/>Verwenden Sie sie zum Herstellen einer Verbindung mit einem SAP-Nachrichtenserver. | Nein |
| `messageServerService` | Der Dienstname oder die Portnummer des Nachrichtenservers.<br/>Verwenden Sie sie zum Herstellen einer Verbindung mit einem SAP-Nachrichtenserver. | Nein |
| `systemId` | Die ID des SAP-Systems, in dem sich die Tabelle befindet.<br/>Verwenden Sie sie zum Herstellen einer Verbindung mit einem SAP-Nachrichtenserver. | Nein |
| `logonGroup` | Die Anmeldegruppe für das SAP-System.<br/>Verwenden Sie sie zum Herstellen einer Verbindung mit einem SAP-Nachrichtenserver. | Nein |
| `clientId` | Die ID des Clients im SAP-System.<br/>Zulässiger Wert: Eine dreistellige Dezimalzahl, die als Zeichenfolge angegeben wird. | Ja |
| `language` | Die Sprache, die das SAP-System verwendet.<br/>Der Standardwert ist `EN`.| Nein |
| `userName` | Der Name des Benutzers, der Zugriff auf den SAP-Server hat. | Ja |
| `password` | Das Kennwort für den Benutzer. Markieren Sie dieses Feld mit dem Typ `SecureString`, um es in Data Factory sicher zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja |
| `sncMode` | Der SNC-Aktivierungsindikator für den Zugriff auf den SAP-Server, auf dem sich die Tabelle befindet.<br/>Verwenden Sie ihn, wenn Sie mithilfe von SNC eine Verbindung zum SAP-Server herstellen möchten.<br/>Zulässige Werte sind `0` („off“, der Standardwert) oder `1` („on“). | Nein |
| `sncMyName` | Der SNC-Name des+ Initiators für den Zugriff auf den SAP-Server, auf dem sich die Tabelle befindet.<br/>Gilt, wenn `sncMode` „on“ ist. | Nein |
| `sncPartnerName` | Der SNC-Name des Kommunikationspartners für den Zugriff auf den SAP-Server, auf dem sich die Tabelle befindet.<br/>Gilt, wenn `sncMode` „on“ ist. | Nein |
| `sncLibraryPath` | Die Bibliothek des externen Sicherheitsprodukts für den Zugriff auf den SAP-Server, auf dem sich die Tabelle befindet.<br/>Gilt, wenn `sncMode` „on“ ist. | Nein |
| `sncQop` | Die anzuwendende Ebene von „Quality of Protection für SNC“.<br/>Gilt, wenn `sncMode` „on“ ist. <br/>Zulässige Werte sind `1` (Authentifizierung), `2` (Integrität), `3` (Datenschutz), `8` (Standard), `9` (Maximum). | Nein |
| `connectVia` | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Eine selbstgehostete Integration Runtime ist erforderlich, wie weiter oben unter [Voraussetzungen](#prerequisites) erwähnt wurde. |Ja |

### <a name="example-1-connect-to-an-sap-application-server"></a>Beispiel 1: Herstellen einer Verbindung mit einem SAP-Anwendungsserver

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-2-connect-to-an-sap-message-server"></a>Beispiel 2: Herstellen einer Verbindung mit einem SAP-Nachrichtenserver

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>Beispiel 3: Herstellen einer Verbindung mithilfe von SNC

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste der Abschnitte und Eigenschaften zum Definieren von Datasets finden Sie unter [Datasets.](concepts-datasets-linked-services.md) Der folgende Abschnitt enthält eine Liste der Eigenschaften, die vom SAP-Tabellen-Dataset unterstützt werden.

Wenn Sie Daten aus oder in den verknüpften SAP BW Open Hub-Dienst kopieren möchten, werden die folgenden Eigenschaften unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| `type` | Die `type`-Eigenschaft muss auf `SapTableResource` festgelegt werden. | Ja |
| `tableName` | Der Name der SAP-Tabelle, aus der Daten kopiert werden sollen. | Ja |

### <a name="example"></a>Beispiel

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "typeProperties": {
            "tableName": "<SAP table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste der Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie unter [Pipelines.](concepts-pipelines-activities.md) Der folgende Abschnitt enthält eine Liste der Eigenschaften, die von der SAP-Tabellenquelle unterstützt werden.

### <a name="sap-table-as-source"></a>SAP-Tabelle als Quelle

Beim Kopieren von Daten aus einer SAP-Tabelle werden die folgenden Eigenschaften unterstützt:

| Eigenschaft                         | BESCHREIBUNG                                                  | Erforderlich |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | Die `type`-Eigenschaft muss auf `SapTableSource` festgelegt werden.         | Ja      |
| `rowCount`                         | Die Anzahl der Zeilen, die abgerufen werden sollen.                              | Nein       |
| `rfcTableFields`                 | Die Felder (Spalten), die aus der SAP-Tabelle kopiert werden sollen. Beispiel: `column0, column1`. | Nein       |
| `rfcTableOptions`                | Die Optionen zum Filtern der Zeilen in einer SAP-Tabelle. Beispiel: `COLUMN0 EQ 'SOMEVALUE'`. Sehen Sie sich dazu auch die Tabelle „SAP-Abfrageoperator“ weiter unten in diesem Artikel an. | Nein       |
| `customRfcReadTableFunctionModule` | Ein benutzerdefiniertes RFC-Funktionsmodul, das zum Lesen von Daten aus einer SAP-Tabelle verwendet werden kann.<br>Mit einem benutzerdefinierten RFC-Funktionsmodul können Sie festlegen, wie die Daten aus Ihrem SAP-System abgerufen und an Data Factory zurückgegeben werden sollen. Beim benutzerdefinierten Funktionsmodul muss eine Schnittstelle (für Import, Export, Tabellen) implementiert worden sein, die mit `/SAPDS/RFC_READ_TABLE2` vergleichbar ist. Dabei handelt es sich um die von Data Factory verwendete Standardschnittstelle.<br>Data Factory | Nein       |
| `partitionOption`                  | Der Partitionsmechanismus, der zum Lesen aus einer SAP-Tabelle verwendet wird. Folgende Optionen werden unterstützt: <ul><li>`None`</li><li>`PartitionOnInt` (normale ganze Zahl oder ganzzahlige Werte mit führenden Nullen, z.B. `0000012345`)</li><li>`PartitionOnCalendarYear` (4 Ziffern im Format „JJJJ“)</li><li>`PartitionOnCalendarMonth` (6 Ziffern im Format „JJJJMM“)</li><li>`PartitionOnCalendarDate` (8 Ziffern im Format „JJJJMMTT“)</li><li>`PartitionOntime` (6 Ziffern im Format „HHMMSS“, z. B. `235959`)</li></ul> | Nein       |
| `partitionColumnName`              | Der Name der Spalte, die zum Partitionieren der Daten verwendet wird.                | Nein       |
| `partitionUpperBound`              | Der Höchstwert der in `partitionColumnName` angegebenen Spalte, der zum Fortsetzen der Partitionierung verwendet wird. | Nein       |
| `partitionLowerBound`              | Der Mindestwert der in `partitionColumnName` angegebenen Spalte, der zum Fortsetzen der Partitionierung verwendet wird. (Hinweis: `partitionLowerBound` kann nicht „0“ sein, wenn die Partitionsoption `PartitionOnInt` lautet.) | Nein       |
| `maxPartitionsNumber`              | Die maximale Anzahl von Partitionen, in welche die Daten aufgeteilt werden können.     | Nein       |
| `sapDataColumnDelimiter` | Das als Trennzeichen verwendete einzelne Zeichen, das an SAP RFC übergeben wird, um die Ausgabedaten aufzuteilen. | Nein |

>[!TIP]
>Wenn Ihre SAP-Tabelle eine große Menge an Daten enthält, z.B. mehrere Milliarden Zeilen, verwenden Sie `partitionOption` und `partitionSetting`, um die Daten in kleinere Partitionen aufzuteilen. In diesem Fall werden die Daten pro Partition gelesen, und jede Datenpartition wird über einen einzelnen RFC-Aufruf von Ihrem SAP-Server abgerufen.<br/>
<br/>
>Bei dem Beispiel von `partitionOption` als `partitionOnInt` wird die Anzahl der Zeilen in jeder Partition mit dieser Formel berechnet: (Gesamtzahl der Zeilen zwischen `partitionUpperBound` und `partitionLowerBound`)/`maxPartitionsNumber`.<br/>
<br/>
>Wenn Sie Datenpartitionen parallel laden möchten, um den Kopiervorgang zu beschleunigen, wird der Parallelitätsgrad durch die Einstellung [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) in der Kopieraktivität gesteuert. Wenn Sie zum Beispiel `parallelCopies` auf vier festlegen, werden von Data Factory vier Abfragen gleichzeitig generiert und ausgeführt. Diese Abfragen basieren auf den von Ihnen angegebenen Partitionsoptionen und -einstellungen, und jede Abfrage ruft einen Teil der Daten aus Ihrer SAP-Tabelle ab. Es wird dringend empfohlen, `maxPartitionsNumber` als Vielfaches des Werts der `parallelCopies`-Eigenschaft anzugeben. Beim Kopieren von Daten in einen dateibasierten Datenspeicher wird außerdem empfohlen, unter Verwendung von „Mehrere Dateien“ in einen Ordner zu schreiben (Sie müssen nur den Ordnernamen angeben). In diesem Fall ist die Leistung besser als beim Schreiben in eine einzelne Datei.

In `rfcTableOptions` können Sie die folgenden allgemeinen SAP-Abfrageoperatoren zum Filtern von Zeilen verwenden:

| Operator | BESCHREIBUNG |
| :------- | :------- |
| `EQ` | Gleich |
| `NE` | Ungleich |
| `LT` | Kleiner als |
| `LE` | Kleiner als oder gleich |
| `GT` | Größer als |
| `GE` | Größer als oder gleich |
| `IN` | Wie in `TABCLASS IN ('TRANSP', 'INTTAB')` |
| `LIKE` | Wie in `LIKE 'Emma%'` |

### <a name="example"></a>Beispiel

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
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
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="join-sap-tables"></a>Verknüpfen von SAP-Tabellen

Der SAP-Tabellenconnector unterstützt derzeit nur eine einzige Tabelle mit dem Standardfunktionsmodul. Um die verknüpften Daten mehrerer Tabellen zu erhalten, können Sie die [customRfcReadTableFunctionModule](#copy-activity-properties)-Eigenschaft im SAP-Tabellenconnector in den folgenden Schritten verwenden:

- [Schreiben Sie ein benutzerdefiniertes Funktionsmodul](#create-custom-function-module), das eine Abfrage in Form von Optionen übernehmen und Ihre eigene Logik zum Abrufen der Daten anwenden kann.
- Geben Sie für „Benutzerdefiniertes Funktionsmodul“ den Namen Ihres benutzerdefinierten Funktionsmoduls ein.
- Legen Sie als „RFC-Tabellenoptionen“ die join-Anweisung für die Tabellen fest, die als Optionen an das Funktionsmodul übergeben werden soll, z. B. „`<TABLE1>` INNER JOIN `<TABLE2>` ON COLUMN0“.

Unten ist ein Beispiel aufgeführt:

![SAP-Tabellenverknüpfung](./media/connector-sap-table/sap-table-join.png) 

>[!TIP]
>Sie können die verknüpften Daten auch in der Sicht aggregieren. Dies wird vom SAP-Tabellenconnector unterstützt.
>Außerdem können Sie versuchen, die verknüpfte Tabellen zu extrahieren, um sie in Azure zu integrieren (z. B. in Azure Storage, Azure SQL-Datenbank). Anschließend können Sie mit einem Datenfluss die weitere Verarbeitung mit Joins oder Filtern durchführen.

## <a name="create-custom-function-module"></a>Erstellen des benutzerdefinierten Funktionsmoduls

Für SAP-Tabellen wird derzeit die [customRfcReadTableFunctionModule](#copy-activity-properties)-Eigenschaft in der Kopierquelle unterstützt. Mit dieser können Sie Ihre eigene Logik nutzen und Daten verarbeiten.

Im Folgenden finden Sie einen kurzen Leitfaden mit den Anforderungen für die ersten Schritte mit dem benutzerdefinierten Funktionsmodul:

- Definition:

    ![Definition](./media/connector-sap-table/custom-function-module-definition.png) 

- Exportieren Sie die Daten in eine der folgenden Tabellen:

    ![Exporttabelle 1](./media/connector-sap-table/export-table-1.png) 

    ![Exporttabelle 2](./media/connector-sap-table/export-table-2.png)
 
Im Folgenden finden Sie Abbildungen der Funktionsweise des SAP-Tabellenconnectors mit dem benutzerdefinierten Funktionsmodul:

1. Stellen Sie über SAP NCO eine Verbindung mit dem SAP-Server her.

1. Rufen Sie das benutzerdefinierte Funktionsmodul mit den wie folgt festgelegten Parametern auf:

    - QUERY_TABLE: der im ADF-Dataset für die SAP-Tabelle festgelegte Tabellenname 
    - Trennzeichen: das in ADF für die SAP-Tabellenquelle festgelegte Trennzeichen 
    - ROWCOUNT/Option/Felder: die in der ADF-Tabellenquelle festgelegten Werte für „RowCount“, die Option „Aggregiert“ und die Felder

1. Rufen Sie das Ergebnis ab, und analysieren Sie die Daten auf folgende Weise:

    1. Analysieren Sie den Wert in der Tabelle „Fields“ (Felder), um die Schemas abzurufen.

        ![Analysieren von Werten in Feldern](./media/connector-sap-table/parse-values.png)

    1. Rufen Sie die Werte der Ausgabetabelle ab, um festzustellen, welche Tabelle diese Werte enthält.

        ![Abrufen von Werten aus der Ausgabetabelle](./media/connector-sap-table/get-values.png)

    1. Rufen Sie die Werte aus „OUT_TABLE“ ab, analysieren Sie die Daten, und schreiben Sie sie dann in die Senke.

## <a name="data-type-mappings-for-an-sap-table"></a>Datentypzuordnungen für eine SAP-Tabelle

Wenn Sie Daten aus einer SAP-Tabelle kopieren, werden die folgenden Zuordnungen von den SAP-Tabellendatentypen zu den Azure Data Factory-Zwischendatentypen verwendet. Informationen dazu, wie die Kopieraktivität das Quellschema und den Datentyp zur Senke zuordnet, finden Sie unter [Schema- und Datentypzuordnungen](copy-activity-schema-and-type-mapping.md).

| SAP ABAP-Typ | Data Factory-Zwischendatentyp |
|:--- |:--- |
| `C` (Zeichenfolge) | `String` |
| `I` (Integer) | `Int32` |
| `F` (Float) | `Double` |
| `D` (Date) | `String` |
| `T` (Time) | `String` |
| `P` (BCD Packed, Currency, Decimal, Qty) | `Decimal` |
| `N` (Numeric) | `String` |
| `X` (Binary und Raw) | `String` |

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nächste Schritte

Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

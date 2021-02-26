---
title: Kopieren von Daten aus SAP HANA
description: Erfahren Sie, wie Daten aus SAP HANA mithilfe einer Kopieraktivität in eine Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/22/2020
ms.openlocfilehash: ce3c1e22dd030c0730bf4d9859591c00860908a7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100382275"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Kopieren von Daten aus SAP HANA mithilfe von Azure Data Factory
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-sap-hana-connector.md)
> * [Aktuelle Version](connector-sap-hana.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus einer SAP HANA-Datenbank zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

>[!TIP]
>Informationen zur allgemeinen Unterstützung des SAP-Datenintegrationsszenarios durch ADF finden Sie im [Whitepaper zur SAP-Datenintegration mit Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf). Dort finden Sie auch eine detaillierte Einführung in jeden SAP-Connector, einen Vergleich und Anleitungen.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser SAP HANA-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus einer SAP HANA-Datenbank in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser SAP HANA-Connector unterstützt insbesondere Folgendes:

- Kopieren von Daten von einer SAP HANA-Datenbank mit einer beliebigen Version
- Kopieren von Daten aus **HANA-Informationsmodellen** (z.B. Analyse- und Berechnungsansichten) und aus **Zeile/Spalte-Tabellen**.
- Kopieren von Dateien unter Verwendung der **Standard**- oder **Windows**-Authentifizierung
- Paralleles Kopieren von einer SAP HANA-Quelle. Weitere Informationen finden Sie im Abschnitt [Paralleles Kopieren von SAP HANA](#parallel-copy-from-sap-hana).

> [!TIP]
> Verwenden Sie zum Kopieren von Daten **in** SAP HANA-Datenspeicher einen generischen ODBC-Connector. Ausführliche Informationen hierzu finden Sie im Abschnitt [SAP HANA-Senke](#sap-hana-sink). Beachten Sie, dass die verknüpften Dienste für SAP HANA-Connectors und ODBC-Connectors unterschiedlichen Typs sind und daher nicht wiederverwendet werden können.

## <a name="prerequisites"></a>Voraussetzungen

Zum Verwenden dieses SAP HANA-Connectors müssen Sie folgende Schritte durchführen:

- Richten Sie eine selbstgehostete Integration Runtime ein. Im Artikel [Selbstgehostete Integration Runtime](create-self-hosted-integration-runtime.md) finden Sie Details.
- Installieren des SAP HANA-ODBC-Treibers auf dem Computer mit der Integrationslaufzeit. Sie können den SAP HANA-ODBC-Treiber aus dem [SAP Software Download Center](https://support.sap.com/swdc) herunterladen. Suche Sie mit dem Schlüsselwort **SAP HANA CLIENT for Windows**.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den SAP HANA-Connector verwendet werden:

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit SAP HANA verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **SapHana** | Ja |
| connectionString | Geben Sie die Informationen an, die zum Herstellen einer Verbindung mit SAP HANA mithilfe der **Standardauthentifizierung** oder **Windows-Authentifizierung** benötigt werden. Sehen Sie sich die folgenden Beispiele an.<br>In der Verbindungszeichenfolge ist Server/Port obligatorisch (der Standardport ist 30015), und Benutzername und Kennwort sind bei der Verwendung von Standardauthentifizierung obligatorisch. Weitere erweiterte Einstellungen finden Sie unter [Eigenschaften der SAP Hana ODBC-Verbindung](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>).<br/>Sie können das Kennwort auch in Azure Key Vault speichern und die Kennwortkonfiguration aus der Verbindungszeichenfolge pullen. Ausführlichere Informationen finden Sie im Artikel [Speichern von Anmeldeinformationen in Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| userName | Geben Sie den Benutzernamen an, wenn Sie Windows-Authentifizierung verwenden. Beispiel: `user@domain.com` | Nein |
| password | Geben Sie ein Kennwort für das Benutzerkonto an. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Nein |
| connectVia | Die [Integrationslaufzeit](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden muss. Eine selbstgehostete Integrationslaufzeit ist erforderlich, wie unter [Voraussetzungen](#prerequisites) erwähnt wird. |Ja |

**Beispiel: Verwenden von Standardauthentifizierung**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Beispiel: Verwenden von Windows-Authentifizierung**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
            "userName": "<username>", 
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

Wenn Sie den verknüpften SAP HANA-Dienst mit der folgenden Nutzlast verwendet haben, wird er weiterhin unverändert unterstützt. Es wird jedoch empfohlen, zukünftig die neue Version zu verwenden.

**Beispiel:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom SAP HANA-Dataset unterstützt werden.

Beim Kopieren von Daten aus SAP HANA werden die folgenden Eigenschaften unterstützt.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **SapHanaTable** | Ja |
| schema | Der Name des Schemas in der SAP HANA-Datenbank. | Nein (wenn „query“ in der Aktivitätsquelle angegeben ist) |
| table | Der Name der Tabelle in der SAP HANA-Datenbank. | Nein (wenn „query“ in der Aktivitätsquelle angegeben ist) |

**Beispiel:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Wenn Sie das Datenset vom Typ `RelationalTable` verwenden, wird es weiterhin unverändert unterstützt. Es wird jedoch empfohlen, zukünftig die neue Version zu verwenden.

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der SAP HANA-Quelle unterstützt werden.

### <a name="sap-hana-as-source"></a>SAP HANA als Quelle

>[!TIP]
>Weitere Informationen zum effizienten Erfassen von Daten von SAP HANA mithilfe der Datenpartitionierung finden Sie im Abschnitt [Paralleles Kopieren von SAP HANA](#parallel-copy-from-sap-hana).

Wenn Sie Daten aus SAP HANA kopieren möchten, werden die folgenden Eigenschaften im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **SapHanaSource** | Ja |
| Abfrage | Gibt die SQL-Abfrage an, mit der Daten aus der SAP HANA-Instanz gelesen werden. | Ja |
| partitionOptions | Gibt die Datenpartitionierungsoptionen an, mit denen Daten von SAP HANA erfasst werden. Weitere Informationen finden Sie im Abschnitt [Paralleles Kopieren von SAP HANA](#parallel-copy-from-sap-hana).<br>Zulässige Werte sind:  **None** (Standard),  **PhysicalPartitionsOfTable**, **SapHanaDynamicRange**. Weitere Informationen finden Sie im Abschnitt [Paralleles Kopieren von SAP HANA](#parallel-copy-from-sap-hana). `PhysicalPartitionsOfTable` kann nur beim Kopieren von Daten aus einer Tabelle, jedoch nicht bei Abfragen verwendet werden. <br>Wenn eine Partitionsoption aktiviert ist (nicht `None`), wird der Grad an Parallelität zum gleichzeitigen Laden von Daten von SAP HANA durch die Einstellung [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) für die Kopieraktivität gesteuert. | False |
| partitionSettings | Geben Sie die Gruppe der Einstellungen für die Datenpartitionierung an.<br>Verwenden Sie ihn, wenn die Partitionsoption `SapHanaDynamicRange` lautet. | False |
| partitionColumnName | Geben Sie den Namen der Quellspalte an, die von der Partitionierung für den parallelen Kopiervorgang verwendet wird. Ohne Angabe wird der Index oder der Primärschlüssel der Tabelle automatisch erkannt und als Partitionsspalte verwendet.<br>Verwenden Sie diese Option, wenn die Partitionsoption `SapHanaDynamicRange` lautet. Wenn Sie die Quelldaten mithilfe einer Abfrage abrufen, integrieren Sie  `?AdfHanaDynamicRangePartitionCondition` in die WHERE-Klausel. Ein Beispiel finden Sie im Abschnitt [Paralleles Kopieren von SAP HANA](#parallel-copy-from-sap-hana). | Ja, wenn die Partitionierung `SapHanaDynamicRange` verwendet wird. |
| packetSize | Gibt die Netzwerkpaketgröße (in KB) an, mit der Daten auf mehrere Blöcke aufgeteilt werden. Wenn Sie über eine große Datenmenge verfügen, die kopiert werden soll, kann das Erhöhen der Paketgröße in den meisten Fällen die Lesegeschwindigkeit aus SAP Hana erhöhen. Leistungstests werden empfohlen, wenn die Paketgröße angepasst wird. | Nein.<br>Der Standardwert ist 2.048 (2MB). |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Wenn Sie die Kopierquelle vom Typ `RelationalSource` verwenden, wird sie weiterhin unverändert unterstützt. Es wird jedoch empfohlen, zukünftig die neue Version zu verwenden.

## <a name="parallel-copy-from-sap-hana"></a>Paralleles Kopieren von SAP HANA

Der Data Factory-Connector für SAP HANA bietet eine integrierte Datenpartitionierung zum parallelen Kopieren von Daten von SAP HANA. Die Datenpartitionierungsoptionen befinden sich auf der Registerkarte **Quelle** der Kopieraktivität.

![Screenshot der Partitionierungsoptionen](./media/connector-sap-hana/connector-sap-hana-partition-options.png)

Wenn Sie partitioniertes Kopieren aktivieren, führt Data Factory parallele Abfragen für Ihre SAP HANA-Quelle aus, um Daten anhand von Partitionen abzurufen. Der Parallelitätsgrad wird über die Einstellung [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) der Kopieraktivität gesteuert. Wenn Sie zum Beispiel `parallelCopies` auf vier festlegen, generiert Data Factory vier Abfragen gleichzeitig und führt diese gemäß den angegebenen Partitionsoptionen und -einstellungen aus. Jede dieser Abfragen ruft einen Teil der Daten von Ihrer SAP HANA-Instanz ab.

Es wird empfohlen, das parallele Kopieren mit Datenpartitionierung zu aktivieren. Das gilt insbesondere, wenn Sie große Datenmengen von Ihrer SAP HANA-Instanz erfassen. Im Anschluss finden Sie empfohlene Konfigurationen für verschiedene Szenarien. Beim Kopieren von Daten in einen dateibasierten Datenspeicher wird empfohlen, mehrere Dateien in einen Ordner zu schreiben (nur den Ordnernamen anzugeben). In diesem Fall ist die Leistung besser als beim Schreiben in eine einzelne Datei.

| Szenario                                           | Empfohlene Einstellungen                                           |
| -------------------------------------------------- | ------------------------------------------------------------ |
| Vollständiges Laden aus großer Tabelle                        | **Partitionsoption:** Physische Partitionen der Tabelle. <br><br/>Bei der Ausführung erkennt Data Factory automatisch den physischen Partitionstyp der angegebenen SAP HANA-Tabelle und wählt die entsprechende Partitionierungsstrategie aus:<br>- **Bereichspartitionierung:** Ruft die für die Tabelle definierte Partitionsspalte und die Partitionsbereiche ab und kopiert dann die Daten nach Bereich. <br>- **Hashpartitionierung:** Verwendet einen Hashpartitionsschlüssel als Partitionsspalte und partitioniert und kopiert die Daten dann basierend auf den von ADF berechneten Bereichen. <br>- **Roundrobinpartitionierung** oder **Keine Partitionierung**: Verwendet den Primärschlüssel als Partitionsspalte und partitioniert und kopiert die Daten dann basierend auf den von ADF berechneten Bereichen. |
| Laden einer großen Datenmenge unter Verwendung einer benutzerdefinierten Abfrage | **Partitionsoption:** Dynamische Bereichspartitionierung<br>**Abfrage**: `SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>`<br>**Partitionsspalte:** Gibt die Spalte an, die zum Anwenden der dynamischen Bereichspartitionierung verwendet wird. <br><br>Bei der Ausführung berechnet Data Factory zuerst die Wertebereiche der angegebenen Partitionsspalte, indem die Zeilen in mehreren Buckets entsprechend der Anzahl der unterschiedlichen Partitionsspaltenwerte und der ADF-Einstellung für paralleles Kopieren gleichmäßig verteilt werden. Anschließend wird `?AdfHanaDynamicRangePartitionCondition` ersetzt, indem der Wertebereich der Partitionsspalte für jede Partition gefiltert wird, und das Ergebnis wird an SAP HANA gesendet.<br><br>Wenn Sie mehrere Spalten als Partitionsspalten verwenden möchten, können Sie die Werte jeder Spalte in der Abfrage in einer Spalte verketten und als Partitionsspalte in ADF angeben, z. B. `SELECT * FROM (SELECT *, CONCAT(<KeyColumn1>, <KeyColumn2>) AS PARTITIONCOLUMN FROM <TABLENAME>) WHERE ?AdfHanaDynamicRangePartitionCondition`. |

**Beispiel: Abfrage mit physischen Partitionen einer Tabelle**

```json
"source": {
    "type": "SapHanaSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Beispiel: Abfrage mit dynamischer Bereichspartition**

```json
"source": {
    "type": "SapHanaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "SapHanaDynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<Partition_column_name>"
    }
}
```

## <a name="data-type-mapping-for-sap-hana"></a>Datentypzuordnung für SAP HANA

Beim Kopieren von Daten aus SAP HANA werden die folgenden Zuordnungen von SAP HANA-Datentypen zu Azure Data Factory-Zwischendatentypen verwendet. Unter [Schema- und Datentypzuordnungen](copy-activity-schema-and-type-mapping.md) erfahren Sie, wie Sie Aktivitätszuordnungen für Quellschema und Datentyp in die Senke kopieren.

| SAP HANA-Datentyp | Data Factory-Zwischendatentyp |
| ------------------ | ------------------------------ |
| ALPHANUM           | String                         |
| bigint             | Int64                          |
| BINARY             | Byte[]                         |
| BINTEXT            | String                         |
| BLOB               | Byte[]                         |
| BOOL               | Byte                           |
| CLOB               | String                         |
| DATE               | Datetime                       |
| DECIMAL            | Decimal                        |
| Double             | Double                         |
| GLEITKOMMAZAHL              | Double                         |
| INTEGER            | Int32                          |
| NCLOB              | String                         |
| NVARCHAR           | String                         |
| real               | Single                         |
| SECONDDATE         | Datetime                       |
| SHORTTEXT          | String                         |
| SMALLDECIMAL       | Decimal                        |
| SMALLINT           | Int16                          |
| STGEOMETRYTYPE     | Byte[]                         |
| STPOINTTYPE        | Byte[]                         |
| TEXT               | String                         |
| TIME               | TimeSpan                       |
| TINYINT            | Byte                           |
| VARCHAR            | String                         |
| timestamp          | Datetime                       |
| VARBINARY          | Byte[]                         |

## <a name="sap-hana-sink"></a>SAP HANA-Senke

Der SAP HANA-Connector wird zurzeit nicht als Senke unterstützt, aber Sie können den generischen ODBC-Connector mit dem SAP HANA-Treiber verwenden, um Daten in SAP HANA zu schreiben. 

Beachten Sie die [Voraussetzungen](#prerequisites), um zuerst die selbstgehostete Integration Runtime einzurichten und den SAP HANA ODBC-Treiber zu installieren. Erstellen Sie einen verknüpften ODBC-Dienst, um eine Verbindung mit Ihrem SAP HANA-Datenspeicher herzustellen, wie im folgenden Beispiel gezeigt wird. Erstellen Sie dann entsprechend die Senke für die DataSet- und Kopieraktivität mit dem ODBC-Typ. Weitere Informationen finden Sie im Artikel [ODBC-Connector](connector-odbc.md).

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015",
            "authenticationType": "Basic",
            "userName": "<username>",
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

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

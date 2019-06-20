---
title: Kopieren von Daten aus einer SAP-Tabelle mithilfe von Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, wie Daten aus einer SAP-Tabelle mithilfe einer Kopieraktivität in einer Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: jingwang
ms.openlocfilehash: 4dee0e994c9e7be9677a8f1051481850990998e9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245467"
---
# <a name="copy-data-from-sap-table-using-azure-data-factory"></a>Kopieren von Daten aus einer SAP-Tabelle mithilfe von Azure Data Factory

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus einer SAP-Tabelle zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Sie können Daten aus einer SAP-Tabelle in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser SAP-Tabellenconnector unterstützt insbesondere Folgendes:

- Kopieren von Daten aus einer SAP-Tabelle in **SAP Business Suite Version 7.01 oder höher** (in einem aktuellen, nach 2015 veröffentlichten SAP-Supportpaket) oder **S/4HANA**
- Kopieren von Daten aus **transparenten SAP-Tabellen** und **Ansichten**
- Kopieren von Daten mit **Standardauthentifizierung** oder **SNC** (Secure Network Communications), falls SNC konfiguriert ist
- Herstellen einer Verbindung zum **Anwendungsserver** oder **Nachrichtenserver**

## <a name="prerequisites"></a>Voraussetzungen

Zum Verwenden dieses SAP-Tabellenconnectors müssen Sie folgende Schritte durchführen:

- Richten Sie eine selbstgehostete Integration Runtime Version 3.17 oder höher ein. Im Artikel [Selbstgehostete Integration Runtime](create-self-hosted-integration-runtime.md) finden Sie Details.

- Laden Sie den **[SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html)** (64 Bit) von der SAP-Website herunter und installieren Sie ihn auf dem selbstgehosteten IR-Computer. Vergewissern Sie sich bei der Installation im Fenster „Optionale Einrichtungsschritte“, dass Sie die Option **Assemblys in GAC installieren** auswählen. 

    ![Installieren von SAP .NET Connector](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Der SAP-Benutzer, der im SAP-Tabellenconnector von Data Factory verwendet wird, muss über folgende Berechtigungen verfügen: 

    - Autorisierung für RFC. 
    - Berechtigungen für die Aktivität „Execute“ des Autorisierungsobjekts „S_SDSAUTH“.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den SAP-Tabellenconnector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit SAP Business Warehouse Open Hub verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **SapTable** | Ja |
| server | Name des Servers, auf dem sich die SAP-Instanz befindet.<br/>Anwendbar, wenn Sie eine Verbindung zum **SAP-Anwendungsserver** herstellen möchten. | Nein |
| systemNumber | Systemnummer des SAP-Systems.<br/>Anwendbar, wenn Sie eine Verbindung zum **SAP-Anwendungsserver** herstellen möchten.<br/>Zulässiger Wert: Zweistellige Dezimalzahl, die als Zeichenfolge angegeben ist. | Nein |
| messageServer | Hostname des SAP-Nachrichtenservers.<br/>Anwendbar, wenn Sie eine Verbindung zum **SAP-Nachrichtenserver** herstellen möchten. | Nein |
| messageServerService | Name des Diensts oder Portnummer des Nachrichtenservers.<br/>Anwendbar, wenn Sie eine Verbindung zum **SAP-Nachrichtenserver** herstellen möchten. | Nein |
| systemId | SystemID des SAP-Systems, in dem sich die Tabelle befindet.<br/>Anwendbar, wenn Sie eine Verbindung zum **SAP-Nachrichtenserver** herstellen möchten. | Nein |
| logonGroup | Anmeldegruppe für das SAP-System.<br/>Anwendbar, wenn Sie eine Verbindung zum **SAP-Nachrichtenserver** herstellen möchten. | Nein |
| clientId | Client-ID des Clients im SAP-System.<br/>Zulässiger Wert: Dreistellige Dezimalzahl, die als Zeichenfolge angegeben ist. | Ja |
| language | Sprache, die das SAP-System verwendet. | Nein (Standardwert ist **EN**).|
| userName | Der Name des Benutzers, der Zugriff auf den SAP-Server hat. | Ja |
| password | Kennwort für den Benutzer Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja |
| sncMode | SNC-Aktivierungsindikator für den Zugriff auf den SAP-Server, auf dem sich die Tabelle befindet.<br/>Anwendbar, wenn Sie mit SNC eine Verbindung zum SAP-Server herstellen möchten.<br/>Zulässige Werte sind: **0** (Aus, Standardeinstellung) oder **1** (Ein). | Nein |
| sncMyName | SNC-Name des Initiators für den Zugriff auf den SAP-Server, auf dem sich die Tabelle befindet.<br/>Anwendbar, wenn `sncMode` auf „Ein“ eingestellt ist. | Nein |
| sncPartnerName | SNC-Name des Kommunikationspartners für den Zugriff auf den SAP-Server, auf dem sich die Tabelle befindet.<br/>Anwendbar, wenn `sncMode` auf „Ein“ eingestellt ist. | Nein |
| sncLibraryPath | Bibliothek des externen Sicherheitsprodukts für den Zugriff auf den SAP-Server, auf dem sich die Tabelle befindet.<br/>Anwendbar, wenn `sncMode` auf „Ein“ eingestellt ist. | Nein |
| sncQop | SNC-Qualität des Schutzes.<br/>Anwendbar, wenn `sncMode` auf „Ein“ eingestellt ist. <br/>Zulässige Werte sind: **1** (Authentifizierung), **2** (Integrität), **3** (Datenschutz), **8** (Standard), **9** (Maximum). | Nein |
| connectVia | Die [Integrationslaufzeit](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden muss. Eine selbstgehostete Integrationslaufzeit ist erforderlich, wie unter [Voraussetzungen](#prerequisites) erwähnt wird. |Ja |

**Beispiel 1: Herstellen einer Verbindung zum SAP-Anwendungsserver**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
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

**Beispiel 2: Herstellen einer Verbindung zum SAP-Nachrichtenserver**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system id>",
            "logonGroup": "<logon group>",
            "clientId": "<client id>",
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

**Beispiel 3: Herstellen einer Verbindung mit SNC**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "snc myname",
            "sncPartnerName": "snc partner name",
            "sncLibraryPath": "snc library path",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom SAP-Tabellen-Dataset unterstützt werden.

Wenn Sie Daten aus oder in SAP BW Open Hub kopieren möchten, werden die folgenden Eigenschaften unterstützt.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf **SapTableResource** festgelegt werden. | Ja |
| tableName | Name der SAP-Tabelle, aus der Daten kopiert werden sollen. | Ja |

**Beispiel:**

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "linkedServiceName": {
            "referenceName": "<SAP Table linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<SAP table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Quelle „SAP-Tabelle“ unterstützt werden.

### <a name="sap-table-as-source"></a>SAP-Tabelle als Quelle

Beim Kopieren von Daten aus einer SAP-Tabelle werden die folgenden Eigenschaften unterstützt.

| Eigenschaft                         | BESCHREIBUNG                                                  | Erforderlich |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| type                             | Die type-Eigenschaft muss auf **SapTableSource** festgelegt werden.       | Ja      |
| rowCount                         | Anzahl der Zeilen, die abgerufen werden sollen.                              | Nein       |
| rfcTableFields                   | Felder, die aus der SAP-Tabelle kopiert werden sollen. Beispiel: `column0, column1`. | Nein       |
| rfcTableOptions                  | Optionen zum Filtern der Zeilen in der SAP-Tabelle. Beispiel: `COLUMN0 EQ 'SOMEVALUE'`. | Nein       |
| customRfcReadTableFunctionModule | Benutzerdefiniertes RFC-Funktionsmodul, das zum Lesen von Daten aus der SAP-Tabelle verwendet werden kann. | Nein       |
| partitionOption                  | Der Partitionsmechanismus, der zum Lesen aus der SAP-Tabelle verwendet wird. Folgende Optionen werden unterstützt: <br/>- **Kein**<br/>- **PartitionOnInt** (normale ganze Zahl oder ganzzahlige Werte mit führenden Nullen, z.B. 0000012345)<br/>- **PartitionOnCalendarYear** (4 Ziffern im Format „JJJJ“)<br/>- **PartitionOnCalendarMonth** (6 Ziffern im Format „JJJJMM“)<br/>- **PartitionOnCalendarDate** (8 Ziffern im Format „JJJJMMTT“) | Nein       |
| partitionColumnName              | Name der Spalte zum Partitionieren der Daten. | Nein       |
| partitionUpperBound              | Der Höchstwert der in `partitionColumnName` angegebenen Spalte, der zum Fortsetzen der Partitionierung verwendet wird. | Nein       |
| partitionLowerBound              | Der Mindestwert der in `partitionColumnName` angegebenen Spalte, der zum Fortsetzen der Partitionierung verwendet wird. | Nein       |
| maxPartitionsNumber              | Die maximale Anzahl von Partitionen, in welche die Daten aufgeteilt werden können. | Nein       |

>[!TIP]
>- Wenn Ihre SAP-Tabelle ein hohes Datenvolumen (z. B. mehrere Milliarden Zeilen) hat, verwenden Sie `partitionOption` und `partitionSetting`, um die Daten in kleine Partitionen aufzuteilen. In diesem Fall werden die Daten nach Partitionen gelesen, und jede Datenpartition wird von Ihrem SAP-Server jeweils über einen eigenen RFC-Aufruf abgerufen.<br/>
>- Wenn Sie beispielsweise `partitionOption` als `partitionOnInt` verwenden, wird die Anzahl der Zeilen in jeder Partition wie folgt berechnet: (Gesamtzahl der Zeilen zwischen *PartitionUpperBound* und *PartitionLowerBound*)/*MaxPartitionsNumber*.<br/>
>- Wenn Sie zur Beschleunigung des Kopiervorgangs außerdem Partitionen parallel ausführen möchten, wird dringend empfohlen, für `maxPartitionsNumber` ein Vielfaches des Werts von `parallelCopies` zu verwenden (weitere Informationen finden Sie unter [Parallelkopie](copy-activity-performance.md#parallel-copy)).

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP Table input dataset name>",
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
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-table"></a>Datentypzuordnung für SAP-Tabelle

Beim Kopieren von Daten aus einer SAP-Tabelle werden die folgenden Zuordnungen von SAP-Tabellendatentypen zu Azure Data Factory-Zwischendatentypen verwendet. Unter [Schema- und Datentypzuordnungen](copy-activity-schema-and-type-mapping.md) erfahren Sie, wie Sie Aktivitätszuordnungen für Quellschema und Datentyp in die Senke kopieren.

| SAP ABAP-Typ | Data Factory-Zwischendatentyp |
|:--- |:--- |
| C (String) | Zeichenfolge |
| I (Integer) | Int32 |
| F (Float) | Double |
| D (Date) | Zeichenfolge |
| T (Time) | Zeichenfolge |
| P (BCD Packed, Currency, Decimal, Qty) | Decimal |
| N (Numeric) | Zeichenfolge |
| X („Binary“ und „Raw“) | Zeichenfolge |

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

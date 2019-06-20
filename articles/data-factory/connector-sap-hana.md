---
title: Kopieren von Daten aus SAP HANA mithilfe von Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, wie Daten aus SAP HANA mithilfe einer Kopieraktivität in eine Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: cdd83c3ff9d34a5e8b7f2c164136ab82f498ffb5
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022970"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Kopieren von Daten aus SAP HANA mithilfe von Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-sap-hana-connector.md)
> * [Aktuelle Version](connector-sap-hana.md)

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus einer SAP HANA-Datenbank zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Sie können Daten aus einer SAP HANA-Datenbank in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser SAP HANA-Connector unterstützt insbesondere Folgendes:

- Kopieren von Daten von einer SAP HANA-Datenbank mit einer beliebigen Version
- Kopieren von Daten aus **HANA-Informationsmodellen** (z.B. Analyse- und Berechnungsansichten) und aus **Zeile/Spalte-Tabellen** mit SQL-Abfragen
- Kopieren von Dateien unter Verwendung der **Standard**- oder **Windows**-Authentifizierung

> [!NOTE]
> Verwenden Sie zum Kopieren von Daten **in** SAP HANA-Datenspeicher einen generischen ODBC-Connector. Einzelheiten finden Sie unter [SAP HANA-Senke](connector-odbc.md#sap-hana-sink). Beachten Sie, dass die verknüpften Dienste für SAP HANA-Connectors und ODBC-Connectors unterschiedlichen Typs sind und daher nicht wiederverwendet werden können.

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
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **SapHana** | JA |
| server | Der Name des Servers, auf dem sich die SAP HANA-Instanz befindet. Wenn Ihr Server einen benutzerdefinierten Port verwendet, geben Sie `server:port` an. | JA |
| authenticationType | Typ der Authentifizierung für die Verbindung mit der SAP HANA-Datenbank.<br/>Zulässige Werte sind: **Standard** und **Windows** | JA |
| userName | Der Name des Benutzers, der Zugriff auf den SAP-Server hat. | JA |
| password | Kennwort für den Benutzer Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | JA |
| connectVia | Die [Integrationslaufzeit](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden muss. Eine selbstgehostete Integrationslaufzeit ist erforderlich, wie unter [Voraussetzungen](#prerequisites) erwähnt wird. |JA |

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

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu Datasets. Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom SAP HANA-Dataset unterstützt werden.

Legen Sie zum Kopieren von Daten aus SAP HANA die type-Eigenschaft des Datasets auf **RelationalTable** fest. Es sind keine typspezifischen Eigenschaften, die für das SAP HANA-Dataset des Typs „RelationalTable“ unterstützt werden.

**Beispiel:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der SAP HANA-Quelle unterstützt werden.

### <a name="sap-hana-as-source"></a>SAP HANA als Quelle

Legen Sie zum Kopieren von Daten aus SAP HANA den Quelltyp in der Kopieraktivität auf **RelationalSource** fest. Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **RelationalSource** | JA |
| query | Gibt die SQL-Abfrage an, mit der Daten aus der SAP HANA-Instanz gelesen werden. | JA |

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
                "type": "RelationalSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-hana"></a>Datentypzuordnung für SAP HANA

Beim Kopieren von Daten aus SAP HANA werden die folgenden Zuordnungen von SAP HANA-Datentypen zu Azure Data Factory-Zwischendatentypen verwendet. Unter [Schema- und Datentypzuordnungen](copy-activity-schema-and-type-mapping.md) erfahren Sie, wie Sie Aktivitätszuordnungen für Quellschema und Datentyp in die Senke kopieren.

| SAP HANA-Datentyp | Data Factory-Zwischendatentyp |
|:--- |:--- |
| ALPHANUM | String |
| BIGINT | Int64 |
| BLOB | Byte[] |
| BOOLEAN | Byte |
| CLOB | Byte[] |
| DATE | DateTime |
| Decimal | Decimal |
| DOUBLE | Single |
| INT | Int32 |
| NVARCHAR | String |
| REAL | Single |
| SECONDDATE | DateTime |
| SMALLINT | Int16 |
| TIME | TimeSpan |
| TIMESTAMP | DateTime |
| TINYINT | Byte |
| VARCHAR | String |

## <a name="known-limitations"></a>Bekannte Einschränkungen

Es gibt einige bekannte Einschränkungen, wenn Daten aus SAP HANA kopiert werden:

- NVARCHAR-Zeichenfolgen werden auf maximal 4000 Unicode-Zeichen abgeschnitten.
- SMALLDECIMAL wird nicht unterstützt.
- VARBINARY wird nicht unterstützt.
- Gültige Datumsangaben liegen zwischen 1899/12/30 und 9999/12/31.


## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

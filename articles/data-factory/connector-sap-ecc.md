---
title: Kopieren von Daten aus SAP ECC
description: Erfahren Sie, wie Daten aus SAP ECC mithilfe einer Kopieraktivität in einer Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/28/2020
ms.openlocfilehash: 1f3ab61c6030c2871356f494db228711305e5466
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92901586"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Kopieren von Daten aus SAP ECC mithilfe von Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus SAP Enterprise Central Component (ECC) zu kopieren. Weitere Informationen finden Sie im Artikel [Übersicht über die Kopieraktivität](copy-activity-overview.md).

>[!TIP]
>Informationen zur allgemeinen Unterstützung des SAP-Datenintegrationsszenarios durch ADF finden Sie im [Whitepaper zur SAP-Datenintegration mit Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf). Dort finden Sie auch eine detaillierte Einführung in jeden SAP-Connector, einen Vergleich und Anleitungen.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser SAP ECC-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus SAP ECC in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser SAP ECC-Connector unterstützt insbesondere Folgendes:

- Kopieren von Daten aus SAP ECC auf SAP NetWeaver ab Version 7.0
- Kopieren von Daten aus Objekten, die von SAP ECC OData-Diensten verfügbar gemacht werden, z. B. den folgenden:

  - SAP-Tabellen oder -Sichten
  - BAPI-Objekte (Business Application Programming Interface)
  - Datenextraktoren
  - An die SAP Process Integration (PI) gesendete IDOCs (Data Or Intermediate Documents), die über relative Adapter als OData empfangen werden können

- Kopieren von Daten mithilfe der Standardauthentifizierung

Version 7.0 oder höher verweist auf die SAP NetWeaver-Version statt auf die SAP ECC-Version. Beispielsweise verfügt SAP ECC 6.0 EHP 7 im Allgemeinen über die NetWeaver-Version > = 7.4. Falls Sie sich bezüglich Ihrer Umgebung unsicher sind, finden Sie hier die Schritte zur Bestätigung der Version aus Ihrem SAP-System:

1. Stellen Sie per SAP GUI eine Verbindung mit dem SAP-System her. 
2. Wechseln Sie zu **System** -> **Status**. 
3. Überprüfen Sie die Version von SAP_BASIS, und stellen Sie sicher, dass sie mindestens 701 ist.  
      ![Überprüfen von SAP_BASIS](./media/connector-sap-table/sap-basis.png)

>[!TIP]
>Verwenden Sie zum Kopieren von Daten aus SAP ECC über eine SAP-Tabelle oder -Sicht den [SAP-Tabellenconnector](connector-sap-table.md), der schneller und skalierbarer ist.

## <a name="prerequisites"></a>Voraussetzungen

Um diesen SAP ECC-Connector verwenden zu können, müssen Sie die SAP ECC-Entitäten über OData-Dienste über das SAP-Gateway verfügbar machen. Dies gilt insbesondere in folgenden Fällen:

- **Einrichten von SAP-Gateway**. Auf Servern mit SAP NetWeaver in einer höheren Version als 7.4 ist SAP Gateway bereits installiert. In früheren Versionen müssen Sie das eingebettete SAP Gateway oder das SAP Gateway-Hubsystem installieren, bevor Sie SAP ECC-Daten über OData-Dienste verfügbar machen. Informationen zum Einrichten von SAP Gateway finden Sie im [Installationshandbuch](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Aktivieren und Konfigurieren der SAP-OData-Dienste**. Sie können den OData-Dienst über TCODE SICF in Sekunden aktivieren. Sie können auch konfigurieren, welche Objekte für Anforderungen verfügbar gemacht werden sollen. Weitere Informationen finden Sie in den [ausführlichen Anweisungen](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den SAP ECC-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit SAP ECC verknüpften Dienst unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| `type` | Die `type`-Eigenschaft muss auf `SapEcc` festgelegt werden. | Ja |
| `url` | Die URL des SAP ECC OData-Diensts | Ja |
| `username` | Der Benutzername, mit dem die Verbindung mit SAP ECC hergestellt wird | Nein |
| `password` | Das Klartextkennwort, mit dem die Verbindung mit SAP ECC hergestellt wird | Nein |
| `connectVia` | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Weitere Informationen finden Sie im Abschnitt [Voraussetzungen](#prerequisites). Wenn Sie keine Runtime angeben, wird die standardmäßige Azure Integration Runtime verwendet. | Nein |

### <a name="example"></a>Beispiel

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData URL, e.g., http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of integration runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Der folgende Abschnitt enthält eine Liste der Eigenschaften, die vom SAP EEC-Dataset unterstützt werden.

Legen Sie zum Kopieren von Daten aus SAP ECC die `type`-Eigenschaft des Datasets auf `SapEccResource` fest.

Folgende Eigenschaften werden unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| `path` | Pfad der SAP ECC OData-Entität | Ja |

### <a name="example"></a>Beispiel

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path, e.g., dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie unter [Pipelines](concepts-pipelines-activities.md). Der folgende Abschnitt enthält eine Liste der Eigenschaften, die von der SAP EEC-Quelle unterstützt werden.

### <a name="sap-ecc-as-a-source"></a>SAP ECC als Quelle

Legen Sie zum Kopieren von Daten aus SAP ECC die `type`-Eigenschaft im Abschnitt `source` der Kopieraktivität auf `SapEccSource` fest.

Folgende Eigenschaften werden im Abschnitt `source` der Kopieraktivität unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| `type` | Die `type`-Eigenschaft im Abschnitt `source` der Kopieraktivität muss auf `SapEccSource` festgelegt werden. | Ja |
| `query` | Die OData-Abfrageoptionen zum Filtern der Daten. Beispiel:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>Der SAP ECC-Connector kopiert Daten aus der kombinierten URL:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Weitere Informationen finden Sie unter [Komponenten der OData-URL](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nein |
| `sapDataColumnDelimiter` | Das als Trennzeichen verwendete einzelne Zeichen, das an SAP RFC übergeben wird, um die Ausgabedaten aufzuteilen. | Nein |
| `httpRequestTimeout` | Das Timeout (der Wert **TimeSpan** ) für die HTTP-Anforderung, um eine Antwort zu empfangen. Bei diesem Wert handelt es sich um das Timeout zum Empfangen einer Antwort, nicht um das Timeout zum Lesen von Antwortdaten. Wenn Sie hier nichts angeben, lautet der Standardwert **00:30:00** (30 Minuten). | Nein |

### <a name="example"></a>Beispiel

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-sap-ecc"></a>Datentypzuordnungen für SAP ECC

Beim Kopieren von Daten aus SAP ECC werden die folgenden Zuordnungen von OData-Datentypen für SAP ECC zu Azure Data Factory-Zwischendatentypen verwendet. Informationen dazu, wie die Kopieraktivität das Quellschema und den Datentyp zur Senke zuordnet, finden Sie unter [Schema- und Datentypzuordnungen](copy-activity-schema-and-type-mapping.md).

| OData-Datentyp | Data Factory-Zwischendatentyp |
|:--- |:--- |
| `Edm.Binary` | `String` |
| `Edm.Boolean` | `Bool` |
| `Edm.Byte` | `String` |
| `Edm.DateTime` | `DateTime` |
| `Edm.Decimal` | `Decimal` |
| `Edm.Double` | `Double` |
| `Edm.Single` | `Single` |
| `Edm.Guid` | `String` |
| `Edm.Int16` | `Int16` |
| `Edm.Int32` | `Int32` |
| `Edm.Int64` | `Int64` |
| `Edm.SByte` | `Int16` |
| `Edm.String` | `String` |
| `Edm.Time` | `TimeSpan` |
| `Edm.DateTimeOffset` | `DateTimeOffset` |

> [!NOTE]
> Komplexe Datentypen werden derzeit nicht unterstützt.

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nächste Schritte

Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

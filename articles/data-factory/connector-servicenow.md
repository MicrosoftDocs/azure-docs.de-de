---
title: Kopieren von Daten aus ServiceNow
description: Erfahren Sie, wie Daten aus ServiceNow mithilfe einer Kopieraktivität in eine Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: 4e7ebc422a9fd8503c5a3b004e1d06cb5ebfb987
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378450"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Kopieren von Daten aus ServiceNow mithilfe von Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus ServiceNow zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Der ServiceNow-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus ServiceNow in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory enthält einen integrierten Treiber zum Sicherstellen der Konnektivität. Daher müssen Sie mit diesem Connector keinen Treiber manuell installieren.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den ServiceNow-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit ServiceNow verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **ServiceNow** | Ja |
| endpoint | Der Endpunkt des ServiceNow-Servers (`http://<instance>.service-now.com`).  | Ja |
| authenticationType | Der zu verwendende Authentifizierungstyp. <br/>Zulässige Werte sind: **Basic**, **OAuth2** | Ja |
| username | Der Benutzername, der für die Verbindung mit dem ServiceNow-Server für die Authentifizierung „Basic“ und „OAuth2“ verwendet wird.  | Ja |
| password | Das Kennwort für den Benutzernamen für die Authentifizierung „Basic“ und „OAuth2“. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja |
| clientId | Die Client-ID für die Authentifizierung „OAuth2“.  | Nein |
| clientSecret | Der geheime Clientschlüssel für die Authentifizierung „OAuth2“. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Nein |
| useEncryptedEndpoints | Gibt an, ob die Endpunkte der Datenquelle mit HTTPS verschlüsselt sind. Der Standardwert lautet „true“.  | Nein |
| useHostVerification | Gibt an, ob der Hostname im Zertifikat des Servers mit dem Hostnamen des Servers übereinstimmen muss, wenn eine Verbindung über TLS hergestellt wird. Der Standardwert lautet „true“.  | Nein |
| usePeerVerification | Gibt an, ob die Identität des Servers überprüft werden soll, wenn eine Verbindung über TLS hergestellt wird. Der Standardwert lautet „true“.  | Nein |

**Beispiel:**

```json
{
    "name": "ServiceNowLinkedService",
    "properties": {
        "type": "ServiceNow",
        "typeProperties": {
            "endpoint" : "http://<instance>.service-now.com",
            "authenticationType" : "Basic",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom ServiceNow-Dataset unterstützt werden.

Legen Sie zum Kopieren von Daten aus ServiceNow die „type“-Eigenschaft des Datasets auf **ServiceNowObject** fest. Folgende Eigenschaften werden unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **ServiceNowObject** | Ja |
| tableName | Der Name der Tabelle. | Nein (wenn „query“ in der Aktivitätsquelle angegeben ist) |

**Beispiel**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der ServiceNow-Quelle unterstützt werden.

### <a name="servicenow-as-source"></a>ServiceNow als Quelle

Legen Sie zum Kopieren von Daten aus ServiceNow den Quelltyp in der Kopieraktivität auf **ServiceNowSource** fest. Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **ServiceNowSource** | Ja |
| Abfrage | Verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. Beispiel: `"SELECT * FROM Actual.alm_asset"`. | Nein (wenn „tableName“ im Dataset angegeben ist) |

Beachten Sie Folgendes, wenn Sie das Schema und die Spalte für ServiceNow in der Abfrage angeben, und **lesen Sie die [Leistungstipps](#performance-tips) bezüglich der Auswirkung auf die Kopierleistung**.

- **Schema:** Geben Sie in der ServiceNow-Abfrage das Schema mit `Actual` oder `Display` an, das als Parameter `sysparm_display_value` (als TRUE oder FALSE) beim Aufrufen von [ServiceNow-RESTful-APIs](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET) angesehen werden kann. 
- **Spalte:** Der Spaltenname für den tatsächlichen Wert unter dem Schema `Actual` lautet `[column name]_value`. Der Anzeigewert unter dem Schema `Display` lautet dagegen `[column name]_display_value`. Beachten Sie, dass der Spaltenname dem Schema zugeordnet sein muss, das in der Abfrage verwendet wird.

**Beispielabfrage:** 
`SELECT col_value FROM Actual.alm_asset` ODER `SELECT col_display_value FROM Display.alm_asset`

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromServiceNow",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ServiceNow input dataset name>",
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
                "type": "ServiceNowSource",
                "query": "SELECT * FROM Actual.alm_asset"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="performance-tips"></a>Leistungstipps

### <a name="schema-to-use"></a>Zu verwendendes Schema

ServiceNow verwendet 2 verschiedene Schemas: **Actual** gibt die tatsächlichen Daten zurück, und **Display** gibt die Anzeigewerte der Daten zurück. 

Wenn Sie einen Filter in Ihrer Abfrage haben, verwenden Sie das Schema „Actual“, das eine bessere Kopierleistung bietet. Bei Abfragen mit dem Schema „Actual“ bietet ServiceNow beim Abrufen der Daten eine native Filterunterstützung und gibt nur den gefilterten Ergebnissatz zurück, während ADF bei der Abfrage mit dem Schema „Display“ alle Daten abruft und den Filter intern anwendet.

### <a name="index"></a>Index

Der ServiceNow-Tabellenindex kann zur Verbesserung der Abfrageleistung beitragen. Informationen hierzu finden Sie unter [Erstellen eines Tabellenindexes](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/table_administration/task/t_CreateCustomIndex.html).

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

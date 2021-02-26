---
title: Kopieren von Daten aus Concur mithilfe von Azure Data Factory (Vorschau)
description: Erfahren Sie, wie Daten aus Concur mithilfe einer Kopieraktivität in eine Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jingwang
ms.openlocfilehash: 974682aa9e9bebd6e2d17237decd8389174ff28d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383669"
---
# <a name="copy-data-from-concur-using-azure-data-factory-preview"></a>Kopieren von Daten aus Concur mithilfe von Azure Data Factory (Vorschau)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus Concur zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

> [!IMPORTANT]
> Dieser Connector befindet sich derzeit in der Vorschauversion. Sie können ihn ausprobieren und uns Feedback geben. Wenden Sie sich an den [Azure-Support](https://azure.microsoft.com/support/), wenn Sie in Ihrer Lösung eine Abhängigkeit von Connectors verwenden möchten, die sich in der Vorschauphase befinden.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Der Concur-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus Concur in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE]
> Ein Partnerkonto wird derzeit nicht unterstützt.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den Concur-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit Concur verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **Concur** | Ja |
| connectionProperties | Eine Gruppe von Eigenschaften zum Definieren, wie eine Verbindung mit Concur hergestellt werden soll. | Ja |
| ***Unter `connectionProperties`:*** | | |
| authenticationType | Zulässige Werte sind `OAuth_2.0_Bearer` und `OAuth_2.0` (Legacy). Die Option für die OAuth 2.0-Authentifizierung funktioniert mit der alten Concur-API, die seit Februar 2017 als veraltet eingestuft ist. | Ja |
| host | Der Endpunkt des Concur-Servers, z. B. `implementation.concursolutions.com`.  | Ja |
| baseUrl | Die Basis-URL Ihrer Concur-Autorisierungs-URL. | Ja für die `OAuth_2.0_Bearer`-Authentifizierung |
| clientId | Die von der Concur-App-Verwaltung bereitgestellte Client-ID der Anwendung.  | Ja |
| clientSecret | Der geheime Clientschlüssel entsprechend der Client-ID. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja für die `OAuth_2.0_Bearer`-Authentifizierung |
| username | Der Benutzername für den Zugriff auf den Concur-Dienst. | Ja |
| password | Das Kennwort, das dem Benutzernamen entspricht, den Sie im Feld „username“ angegeben haben. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja |
| useEncryptedEndpoints | Gibt an, ob die Endpunkte der Datenquelle mit HTTPS verschlüsselt sind. Der Standardwert lautet „true“.  | Nein |
| useHostVerification | Gibt an, ob der Hostname im Zertifikat des Servers mit dem Hostnamen des Servers übereinstimmen muss, wenn eine Verbindung über TLS hergestellt wird. Der Standardwert lautet „true“.  | Nein |
| usePeerVerification | Gibt an, ob die Identität des Servers überprüft werden soll, wenn eine Verbindung über TLS hergestellt wird. Der Standardwert lautet „true“.  | Nein |

**Beispiel:**

```json
{ 
    "name": "ConcurLinkedService", 
    "properties": {
        "type": "Concur",
        "typeProperties": {
            "connectionProperties": {
                "host":"<host e.g. implementation.concursolutions.com>",
                "baseUrl": "<base URL for authorization e.g. us-impl.api.concursolutions.com>",
                "authenticationType": "OAuth_2.0_Bearer",
                "clientId": "<client id>",
                "clientSecret": {
                    "type": "SecureString",
                    "value": "<client secret>"
                },
                "username": "fakeUserName",
                "password": {
                    "type": "SecureString",
                    "value": "<password>"
                },
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
} 
```

**Beispiel (Legacy):**

Bei Folgendem handelt es sich um ein verknüpftes Legacydienstmodell ohne `connectionProperties` und mit `OAuth_2.0`-Authentifizierung.

```json
{
    "name": "ConcurLinkedService",
    "properties": {
        "type": "Concur",
        "typeProperties": {
            "clientId" : "<clientId>",
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

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom Concur-Dataset unterstützt werden.

Legen Sie zum Kopieren von Daten aus Concur die „type“-Eigenschaft des Datasets auf **ConcurObject** fest. Bei diesem Dataset-Typ gibt es keine zusätzliche typspezifische Eigenschaft. Folgende Eigenschaften werden unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **ConcurObject**. | Ja |
| tableName | Der Name der Tabelle. | Nein (wenn „query“ in der Aktivitätsquelle angegeben ist) |


**Beispiel**

```json
{
    "name": "ConcurDataset",
    "properties": {
        "type": "ConcurObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Concur linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Concur-Quelle unterstützt werden.

### <a name="concursource-as-source"></a>ConcurSource als Quelle

Legen Sie zum Kopieren von Daten aus Concur den Quellentyp in der Kopieraktivität auf **ConcurSource** fest. Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **ConcurSource**. | Ja |
| Abfrage | Verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. Beispiel: `"SELECT * FROM Opportunities where Id = xxx "`. | Nein (wenn „tableName“ im Dataset angegeben ist) |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromConcur",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Concur input dataset name>",
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
                "type": "ConcurSource",
                "query": "SELECT * FROM Opportunities where Id = xxx"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

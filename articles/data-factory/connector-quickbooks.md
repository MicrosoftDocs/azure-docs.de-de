---
title: Kopieren von Daten aus QuickBooks Online mithilfe von Azure Data Factory (Vorschau)
description: Erfahren Sie, wie Sie Daten aus QuickBooks Online mithilfe einer Kopieraktivität in einer Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopieren.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/15/2021
ms.openlocfilehash: eecbcb817ad31480f8f6c3c7272328d06b17c081
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384060"
---
# <a name="copy-data-from-quickbooks-online-using-azure-data-factory-preview"></a>Kopieren von Daten aus QuickBooks Online mithilfe von Azure Data Factory (Vorschau)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel beschreibt, wie Sie mit der Kopieraktivität in Azure Data Factory Daten aus QuickBooks Online kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

> [!IMPORTANT]
> Dieser Connector befindet sich derzeit in der Vorschauversion. Sie können ihn ausprobieren und uns Feedback geben. Wenden Sie sich an den [Azure-Support](https://azure.microsoft.com/support/), wenn Sie in Ihrer Lösung eine Abhängigkeit von Connectors verwenden möchten, die sich in der Vorschauphase befinden.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Der QuickBooks-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus QuickBooks Online in einen beliebigen unterstützten Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser Connector unterstützt QuickBooks OAuth 2.0-Authentifizierung.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den QuickBooks-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit QuickBooks verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| Typ | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **QuickBooks**. | Ja |
| connectionProperties | Eine Gruppe von Eigenschaften zum Definieren, wie eine Verbindung mit QuickBooks hergestellt werden soll. | Ja |
| ***Unter `connectionProperties`:*** | | |
| endpoint | Der Endpunkt des QuickBooks Online-Servers. (quickbooks.api.intuit.com)  | Ja |
| companyId | Die Unternehmens-ID des zu autorisierenden QuickBooks-Unternehmens. Weitere Informationen finden Sie unter [Wie finde ich meine Unternehmens-ID?](https://quickbooks.intuit.com/community/Getting-Started/How-do-I-find-my-Company-ID/m-p/185551) (in englischer Sprache). | Ja |
| consumerKey | Die Client-ID Ihrer QuickBooks Online-Anwendung für die OAuth 2.0-Authentifizierung. Weitere Informationen dazu finden Sie [hier](https://developer.intuit.com/app/developer/qbo/docs/develop/authentication-and-authorization/oauth-2.0#obtain-oauth2-credentials-for-your-app). | Ja |
| consumerSecret | Der geheime Clientschlüssel Ihrer QuickBooks Online-Anwendung für die OAuth 2.0-Authentifizierung. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja |
| refreshToken | Das der QuickBooks-Anwendung zugeordnete OAuth 2.0-Aktualisierungstoken. Weitere Informationen dazu finden Sie [hier](https://developer.intuit.com/app/developer/qbo/docs/develop/authentication-and-authorization/oauth-2.0#obtain-oauth2-credentials-for-your-app). Beachten Sie, dass das Aktualisierungstoken nach 180 Tagen ablaufen wird. Der Kunde muss das Aktualisierungstoken regelmäßig aktualisieren. <br/>Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md).| Ja |
| useEncryptedEndpoints | Gibt an, ob die Endpunkte der Datenquelle mit HTTPS verschlüsselt sind. Der Standardwert lautet „true“.  | Nein |

**Beispiel:**

```json
{
    "name": "QuickBooksLinkedService",
    "properties": {
        "type": "QuickBooks",
        "typeProperties": {
            "connectionProperties": {
                "endpoint": "quickbooks.api.intuit.com",
                "companyId": "<company id>",
                "consumerKey": "<consumer key>", 
                "consumerSecret": {
                     "type": "SecureString",
                     "value": "<clientSecret>"
                },
                "refreshToken": {
                     "type": "SecureString",
                     "value": "<refresh token>"
                },
                "useEncryptedEndpoints": true
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom QuickBooks-Dataset unterstützt werden.

Legen Sie zum Kopieren von Daten aus QuickBooks Online die „type“-Eigenschaft des Datasets auf **QuickBooksObject** fest. Folgende Eigenschaften werden unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| Typ | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **QuickBooksObject**. | Ja |
| tableName | Der Name der Tabelle. | Nein (wenn „query“ in der Aktivitätsquelle angegeben ist) |

**Beispiel**

```json
{
    "name": "QuickBooksDataset",
    "properties": {
        "type": "QuickBooksObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<QuickBooks linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der QuickBooks-Quelle unterstützt werden.

### <a name="quickbooks-as-source"></a>QuickBooks als Quelle

Legen Sie zum Kopieren von Daten aus QuickBooks Online den Quelltyp in der Kopieraktivität auf **QuickBooksSource** fest. Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| Typ | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **QuickBooksSource**. | Ja |
| Abfrage | Verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. Beispiel: `"SELECT * FROM "Bill" WHERE Id = '123'"`. | Nein (wenn „tableName“ im Dataset angegeben ist) |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromQuickBooks",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<QuickBooks input dataset name>",
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
                "type": "QuickBooksSource",
                "query": "SELECT * FROM \"Bill\" WHERE Id = '123' "
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="copy-data-from-quickbooks-desktop"></a>Kopieren von Daten aus QuickBooks Desktop

Mit der Kopieraktivität in Azure Data Factory lassen sich keine Daten direkt aus Quickbooks Desktop kopieren. Um Daten aus Quickbooks Desktop zu kopieren, exportieren Sie Ihre Quickbooks-Daten in eine CSV-Datei mit durch Trennzeichen getrennten Werten, und laden Sie die Datei dann in Azure Blob Storage hoch. Von dort aus können Sie mit Data Factory die Daten in die Senke Ihrer Wahl kopieren.

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

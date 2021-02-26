---
title: Kopieren von Daten aus Square (Vorschau)
description: Erfahren Sie, wie Daten aus Square mithilfe einer Kopieraktivität in eine Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/03/2020
ms.openlocfilehash: ac10e42d338e0ddd44cb3c07709645a69653808d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384791"
---
# <a name="copy-data-from-square-using-azure-data-factory-preview"></a>Kopieren von Daten aus Square mithilfe von Azure Data Factory (Vorschau)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus Square zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

> [!IMPORTANT]
> Dieser Connector befindet sich derzeit in der Vorschauversion. Sie können ihn ausprobieren und uns Feedback geben. Wenden Sie sich an den [Azure-Support](https://azure.microsoft.com/support/), wenn Sie in Ihrer Lösung eine Abhängigkeit von Connectors verwenden möchten, die sich in der Vorschauphase befinden.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Der Square-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus Square in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory enthält einen integrierten Treiber zum Sicherstellen der Konnektivität. Daher müssen Sie mit diesem Connector keinen Treiber manuell installieren.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den Square-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit Square verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **Square** | Ja |
| connectionProperties | Eine Gruppe von Eigenschaften zum Definieren, wie eine Verbindung mit Square hergestellt werden soll. | Ja |
| ***Unter `connectionProperties`:*** | | |
| host | Die URL der Square-Instanz. (mystore.mysquare.com)  | Ja |
| clientId | Die Client-ID, die Ihrer Square-Anwendung zugeordnet ist.  | Ja |
| clientSecret | Der geheime Clientschlüssel, der Ihrer Square-Anwendung zugeordnet ist. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja |
| accessToken | Das von Square erhaltene Zugriffstoken. Gewährt eingeschränkten Zugriff auf ein Square-Konto, indem ein authentifizierter Benutzer um explizite Berechtigungen gebeten wird. OAuth-Zugriffstoken laufen 30 Tage nach der Ausgabe ab, Aktualisierungstoken laufen hingegen nicht ab. Zugriffstoken können durch Aktualisierungstoken aktualisiert werden.<br>Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md).  | Ja |
| refreshToken | Das von Square erhaltene Aktualisierungstoken. Wird zum Abrufen neuer Zugriffstoken verwendet, wenn die aktuellen ablaufen.<br>Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Nein |
| useEncryptedEndpoints | Gibt an, ob die Endpunkte der Datenquelle mit HTTPS verschlüsselt sind. Der Standardwert lautet „true“.  | Nein |
| useHostVerification | Gibt an, ob der Hostname im Zertifikat des Servers mit dem Hostnamen des Servers übereinstimmen muss, wenn eine Verbindung über TLS hergestellt wird. Der Standardwert lautet „true“.  | Nein |
| usePeerVerification | Gibt an, ob die Identität des Servers überprüft werden soll, wenn eine Verbindung über TLS hergestellt wird. Der Standardwert lautet „true“.  | Nein |

Square unterstützt zwei Arten von Zugriffstoken: **persönlich** und **OAuth**.

- Persönliche Zugriffstoken werden verwendet, um unbegrenzten Connect-API-Zugriff auf Ressourcen in Ihrem eigenen Square-Konto zu erhalten.
- OAuth-Zugriffstoken werden verwendet, um authentifizierten und bereichsbezogenen Connect-API-Zugriff auf ein beliebiges Square-Konto zu erhalten. Verwenden Sie diese, wenn Ihre App im Namen der Kontobesitzer auf Ressourcen in anderen Square-Konten zugreift. OAuth-Zugriffstoken können auch für den Zugriff auf Ressourcen in Ihrem eigenen Square-Konto verwendet werden.

In Data Factory ist für die Authentifizierung über das persönliche Zugriffstoken nur `accessToken` erforderlich, während für die Authentifizierung über OAuth `accessToken` und `refreshToken` benötigt werden. [Hier](https://developer.squareup.com/docs/build-basics/access-tokens) erfahren Sie, wie Sie Zugriffstoken abrufen.

**Beispiel:**

```json
{
    "name": "SquareLinkedService",
    "properties": {
        "type": "Square",
        "typeProperties": {
            "connectionProperties": {
                "host": "<e.g. mystore.mysquare.com>", 
                "clientId": "<client ID>", 
                "clientSecrect": {
                    "type": "SecureString",
                    "value": "<clientSecret>"
                }, 
                "accessToken": {
                    "type": "SecureString",
                    "value": "<access token>"
                }, 
                "refreshToken": {
                    "type": "SecureString",
                    "value": "<refresh token>"
                }, 
                "useEncryptedEndpoints": true, 
                "useHostVerification": true, 
                "usePeerVerification": true 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom Square-Dataset unterstützt werden.

Legen Sie zum Kopieren von Daten aus Square die „type“-Eigenschaft des Datasets auf **SquareObject** fest. Folgende Eigenschaften werden unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **SquareObject** | Ja |
| tableName | Der Name der Tabelle. | Nein (wenn „query“ in der Aktivitätsquelle angegeben ist) |

**Beispiel**

```json
{
    "name": "SquareDataset",
    "properties": {
        "type": "SquareObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Square linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Square-Quelle unterstützt werden.

### <a name="square-as-source"></a>Square als Quelle

Legen Sie zum Kopieren von Daten aus Square den Quellentyp in der Kopieraktivität auf **SquareSourceSource** fest. Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **SquareSource** | Ja |
| Abfrage | Verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. Beispiel: `"SELECT * FROM Business"`. | Nein (wenn „tableName“ im Dataset angegeben ist) |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromSquare",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Square input dataset name>",
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
                "type": "SquareSource",
                "query": "SELECT * FROM Business"
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

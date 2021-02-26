---
title: Kopieren von Daten aus Salesforce Marketing Cloud
description: Erfahren Sie, wie Daten aus Salesforce Marketing Cloud mithilfe einer Kopieraktivität in eine Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/17/2020
ms.openlocfilehash: 161b81b196a1e178c7244845b25594440e6d6e1e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369746"
---
# <a name="copy-data-from-salesforce-marketing-cloud-using-azure-data-factory"></a>Kopieren von Daten aus Salesforce Marketing Cloud mithilfe von Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus einer Salesforce Marketing Cloud-Datenbank zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Salesforce Marketing Cloud-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus Salesforce Marketing Cloud in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Der Salesforce Marketing Cloud-Connector unterstützt die OAuth 2-Authentifizierung und sowohl ältere als auch erweiterte Pakettypen. Der Connector basiert auf der [Salesforce Marketing Cloud-REST-API](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/index-api.htm).

>[!NOTE]
>Das Abrufen benutzerdefinierter Objekte oder benutzerdefinierter Datenerweiterungen wird von diesem Connector nicht unterstützt.

## <a name="getting-started"></a>Erste Schritte

Sie können mit dem .NET SDK, Python SDK, Azure PowerShell, der REST-API oder der Azure Resource Manager-Vorlage eine Pipeline mit einer Kopieraktivität erstellen. Im [Tutorial zur Kopieraktivität](quickstart-create-data-factory-dot-net.md) finden Sie detaillierte Anweisungen, wie Sie eine Pipeline mit einer Kopieraktivität erstellen können.

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den Salesforce Marketing Cloud-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit Salesforce Marketing Cloud verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **SalesforceMarketingCloud** | Ja |
| connectionProperties | Eine Gruppe von Eigenschaften, die definieren, wie eine Verbindung mit Salesforce Marketing Cloud hergestellt werden soll. | Ja |
| ***Unter `connectionProperties`:*** | | |
| authenticationType | Gibt die zu verwendende Authentifizierungsmethode an. Zulässige Werte: `Enhanced sts OAuth 2.0` und `OAuth_2.0`.<br><br>Das ältere Salesforce Marketing Cloud-Paket unterstützt nur `OAuth_2.0`, während das erweiterte Paket `Enhanced sts OAuth 2.0` erfordert. <br>Seit dem 1. August 2019 wird die Möglichkeit zum Erstellen älterer Pakete von Salesforce Marketing Cloud nicht mehr unterstützt. Alle neuen Pakete sind erweiterte Pakete. | Ja |
| host | Bei einem erweiterten Paket sollte der Host Ihrer [Unterdomäne](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/your-subdomain-tenant-specific-endpoints.htm) entsprechen, die durch eine 28-stellige Zeichenfolge dargestellt wird und mit den Buchstaben „mc“ beginnt, beispielsweise `mc563885gzs27c5t9-63k636ttgm`. <br>Geben Sie für ein älteres Paket `www.exacttargetapis.com` an. | Ja |
| clientId | Die Client-ID, die der Salesforce Marketing Cloud-Anwendung zugeordnet ist.  | Ja |
| clientSecret | Der geheime Clientschlüssel, der der Salesforce Marketing Cloud-Anwendung zugeordnet ist. Sie können dieses Feld optional als „SecureString“ kennzeichnen, um es sicher in ADF zu speichern, oder das Geheimnis in Azure Key Vault speichern und von dort von der ADF-Kopieraktivität abrufen lassen, wenn Datenkopiervorgänge durchgeführt werden. Weitere Informationen finden Sie unter [Speichern von Anmeldeinformationen in Key Vault](store-credentials-in-key-vault.md). | Ja |
| useEncryptedEndpoints | Gibt an, ob die Endpunkte der Datenquelle mit HTTPS verschlüsselt sind. Der Standardwert lautet „true“.  | Nein |
| useHostVerification | Gibt an, ob der Hostname im Zertifikat des Servers mit dem Hostnamen des Servers übereinstimmen muss, wenn eine Verbindung über TLS hergestellt wird. Der Standardwert lautet „true“.  | Nein |
| usePeerVerification | Gibt an, ob die Identität des Servers überprüft werden soll, wenn eine Verbindung über TLS hergestellt wird. Der Standardwert lautet „true“.  | Nein |

**Beispiel: Verwendung der erweiterten STS OAuth 2-Authentifizierung für das erweiterte Paket** 

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "connectionProperties": {
                "host": "<subdomain e.g. mc563885gzs27c5t9-63k636ttgm>",
                "authenticationType": "Enhanced sts OAuth 2.0",
                "clientId": "<clientId>",
                "clientSecret": {
                     "type": "SecureString",
                     "value": "<clientSecret>"
                },
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}

```

**Beispiel: Verwendung der OAuth 2-Authentifizierung für das ältere Paket** 

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "connectionProperties": {
                "host": "www.exacttargetapis.com",
                "authenticationType": "OAuth_2.0",
                "clientId": "<clientId>",
                "clientSecret": {
                     "type": "SecureString",
                     "value": "<clientSecret>"
                },
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}

```

Wenn Sie den verknüpften Salesforce Marketing Cloud-Dienst mit der folgenden Nutzlast verwendet haben, wird er weiterhin unverändert unterstützt. Es wird jedoch empfohlen, zukünftig die neue Version zu verwenden, wodurch die Paketunterstützung erweitert wird.

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "clientId": "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "useEncryptedEndpoints": true,
            "useHostVerification": true,
            "usePeerVerification": true
        }
    }
}

```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom Salesforce Marketing Cloud-Dataset unterstützt werden.

Legen Sie zum Kopieren von Daten aus Salesforce Marketing Cloud die type-Eigenschaft des Datasets auf **SalesforceMarketingCloudObject** fest. Folgende Eigenschaften werden unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **SalesforceMarketingCloudObject** | Ja |
| tableName | Der Name der Tabelle. | Nein (wenn „query“ in der Aktivitätsquelle angegeben ist) |

**Beispiel**

```json
{
    "name": "SalesforceMarketingCloudDataset",
    "properties": {
        "type": "SalesforceMarketingCloudObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SalesforceMarketingCloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Salesforce Marketing Cloud-Quelle unterstützt werden.

### <a name="salesforce-marketing-cloud-as-source"></a>Salesforce Marketing Cloud als Quelle

Legen Sie zum Kopieren von Daten aus Salesforce Marketing Cloud den Quelltyp in der Kopieraktivität auf **SalesforceMarketingCloudSource** fest. Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **SalesforceMarketingCloudSource** | Ja |
| Abfrage | Verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. Beispiel: `"SELECT * FROM MyTable"`. | Nein (wenn „tableName“ im Dataset angegeben ist) |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromSalesforceMarketingCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SalesforceMarketingCloud input dataset name>",
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
                "type": "SalesforceMarketingCloudSource",
                "query": "SELECT * FROM MyTable"
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

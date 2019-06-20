---
title: Kopieren von Daten aus Google AdWords mithilfe von Azure Data Factory (Vorschauversion) | Microsoft-Dokumentation
description: Erfahren Sie, wie Daten aus Google AdWords mithilfe einer Kopieraktivität in einer Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: jingwang
ms.openlocfilehash: 0f68627e2db3c08049f0273045906057526bd6aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61094917"
---
# <a name="copy-data-from-google-adwords-using-azure-data-factory-preview"></a>Kopieren von Daten aus Google AdWords mithilfe von Azure Data Factory (Vorschauversion)

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus Google AdWords zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

> [!IMPORTANT]
> Dieser Connector befindet sich derzeit in der Vorschauversion. Sie können ihn ausprobieren und Feedback geben. Wenden Sie sich an den [Azure-Support](https://azure.microsoft.com/support/), wenn Sie in Ihrer Lösung eine Abhängigkeit von Connectors verwenden möchten, die sich in der Vorschauphase befinden.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Sie können Daten aus Google AdWords in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory enthält einen integrierten Treiber zum Sicherstellen der Konnektivität. Daher müssen Sie mit diesem Connector keinen Treiber manuell installieren.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten verwendet werden, die für den Google AdWords-Connector spezifisch sind.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit Google AdWords verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **GoogleAdWords** | Ja |
| clientCustomerID | Die Clientkunden-ID des AdWords-Kontos, für das Sie Berichtsdaten abrufen möchten.  | Ja |
| developerToken | Das Entwicklertoken, das dem Managerkonto, mit dem Sie den Zugriff auf die AdWords-API gewähren möchten, zugeordnet ist.  Sie können dieses Feld optional als „SecureString“ markieren, um es sicher in ADF zu speichern, oder dieses Kennwort in Azure Key Vault speichern und von dort von der ADF-Kopieraktivität abrufen lassen, wenn Datenkopiervorgänge durchgeführt werden. Weitere Informationen finden Sie unter [Speichern von Anmeldeinformationen in Key Vault](store-credentials-in-key-vault.md). | Ja |
| authenticationType | Der OAuth 2.0-Authentifizierungsmechanismus, der für die Authentifizierung verwendet wird. „ServiceAuthentication“ kann nur für eine selbstgehostete IR verwendet werden. <br/>Zulässige Werte sind: **ServiceAuthentication**, **UserAuthentication** | Ja |
| refreshToken | Das Aktualisierungstoken, das von Google zum Autorisieren des Zugriffs auf AdWords für UserAuthentication abgerufen wird. Sie können dieses Feld optional als „SecureString“ markieren, um es sicher in ADF zu speichern, oder dieses Kennwort in Azure Key Vault speichern und von dort von der ADF-Kopieraktivität abrufen lassen, wenn Datenkopiervorgänge durchgeführt werden. Weitere Informationen finden Sie unter [Speichern von Anmeldeinformationen in Key Vault](store-credentials-in-key-vault.md). | Nein |
| clientId | Die Client-ID der Google-Anwendung, die zum Abrufen des Aktualisierungstokens verwendet wird. Sie können dieses Feld optional als „SecureString“ markieren, um es sicher in ADF zu speichern, oder dieses Kennwort in Azure Key Vault speichern und von dort von der ADF-Kopieraktivität abrufen lassen, wenn Datenkopiervorgänge durchgeführt werden. Weitere Informationen finden Sie unter [Speichern von Anmeldeinformationen in Key Vault](store-credentials-in-key-vault.md). | Nein |
| clientSecret | Das Clientgeheimnis der Google-Anwendung, die zum Abrufen des Aktualisierungstokens verwendet wird. Sie können dieses Feld optional als „SecureString“ markieren, um es sicher in ADF zu speichern, oder dieses Kennwort in Azure Key Vault speichern und von dort von der ADF-Kopieraktivität abrufen lassen, wenn Datenkopiervorgänge durchgeführt werden. Weitere Informationen finden Sie unter [Speichern von Anmeldeinformationen in Key Vault](store-credentials-in-key-vault.md). | Nein |
| email | Die E-Mail-ID des Dienstkontos, die für die „ServiceAuthentication“ verwendet wird und nur für selbstgehostete IRs verwendet werden kann.  | Nein |
| keyFilePath | Der vollständige Pfad zur P12-Schlüsseldatei, die zur Authentifizierung der E-Mail-Adresse des Dienstkontos verwendet wird und nur für selbstgehostete IRs verwendet werden kann.  | Nein |
| trustedCertPath | Der vollständige Pfad der PEM-Datei mit vertrauenswürdigen Zertifizierungsstellenzertifikaten zur Überprüfung des Servers beim Verbindungsaufbau über SSL. Diese Eigenschaft kann nur festgelegt werden, wenn SSL in einer selbstgehostetem IR verwendet wird. Der Standardwert ist die Datei „cacerts.pem“, die mit der IR installiert wird.  | Nein |
| useSystemTrustStore | Gibt an, ob ein Zertifizierungsstellenzertifikat aus dem Vertrauensspeicher des Systems oder aus einer angegebenen PEM-Datei verwendet werden soll. Der Standardwert ist „false“.  | Nein |

**Beispiel:**

```json
{
    "name": "GoogleAdWordsLinkedService",
    "properties": {
        "type": "GoogleAdWords",
        "typeProperties": {
            "clientCustomerID" : "<clientCustomerID>",
            "developerToken": {
                "type": "SecureString",
                "value": "<developerToken>"
            },
            "authenticationType" : "ServiceAuthentication",
            "refreshToken": {
                "type": "SecureString",
                "value": "<refreshToken>"
            },
            "clientId": {
                "type": "SecureString",
                "value": "<clientId>"
            },
            "clientSecret": {
                "type": "SecureString",
                "value": "<clientSecret>"
            },
            "email" : "<email>",
            "keyFilePath" : "<keyFilePath>",
            "trustedCertPath" : "<trustedCertPath>",
            "useSystemTrustStore" : true,
        }
    }
}

```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom Google AdWords-Dataset unterstützt werden.

Legen Sie zum Kopieren von Daten aus Google AdWords die type-Eigenschaft des Datasets auf **GoogleAdWordsObject** fest. Folgende Eigenschaften werden unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **GoogleAdWordsObject** | Ja |
| tableName | Name der Tabelle. | Nein (wenn „query“ in der Aktivitätsquelle angegeben ist) |

**Beispiel**

```json
{
    "name": "GoogleAdWordsDataset",
    "properties": {
        "type": "GoogleAdWordsObject",
        "linkedServiceName": {
            "referenceName": "<GoogleAdWords linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}

```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Google AdWords-Quelle unterstützt werden.

### <a name="google-adwords-as-source"></a>Google AdWords als Quelle

Legen Sie zum Kopieren von Daten aus Google AdWords den Quelltyp in der Kopieraktivität auf **GoogleAdWordsSource** fest. Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **GoogleAdWordsSource** | Ja |
| query | Verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. Beispiel: `"SELECT * FROM MyTable"`. | Nein (wenn „tableName“ im Dataset angegeben ist) |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromGoogleAdWords",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleAdWords input dataset name>",
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
                "type": "GoogleAdWordsSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

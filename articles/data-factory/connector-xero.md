---
title: Kopieren von Daten aus Xero mithilfe von Azure Data Factory
description: Erfahren Sie, wie Daten aus Xero mithilfe einer Kopieraktivität in eine Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: jingwang
ms.openlocfilehash: 342d0aabe2222393f33aa4ce93646da9f29cf1fb
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92926460"
---
# <a name="copy-data-from-xero-using-azure-data-factory"></a>Kopieren von Daten aus Xero mithilfe von Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus Xero zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Der Xero-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus Xero in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser Xero-Connector unterstützt insbesondere Folgendes:

- [Private Anwendung](https://developer.xero.com/documentation/getting-started/getting-started-guide) von Xero, jedoch keine öffentlich Anwendung.
- Alle Xero-Tabellen (API-Endpunkte) mit Ausnahme von „Reports“.
- OAuth 1.0- und OAuth 2.0-Authentifizierung.

Azure Data Factory enthält einen integrierten Treiber zum Sicherstellen der Konnektivität. Daher müssen Sie mit diesem Connector keinen Treiber manuell installieren.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den Xero-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit Xero verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **Xero** | Ja |
| connectionProperties | Eine Gruppe von Eigenschaften zum Definieren, wie eine Verbindung mit Xero hergestellt werden soll. | Ja |
| **_Unter `connectionProperties`:_* _ | | |
| host | Der Endpunkt des Xero-Servers (`api.xero.com`).  | Ja |
| authenticationType | Zulässige Werte sind `OAuth_2.0` und `OAuth_1.0`. | Ja |
| consumerKey | Der Consumerschlüssel, der der Xero-Anwendung zugeordnet ist. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja |
| privateKey | Der private Schlüssel aus der PEM-Datei, der für Ihre private Xero-Anwendung generiert wurde. Weitere Informationen finden Sie unter [Erstellen eines öffentlichen/privaten Schlüsselpaars](https://developer.xero.com/documentation/auth-and-limits/create-publicprivate-key). Achten Sie darauf, *„privatekey.pem“ mit dem NumBits-Wert 512* mithilfe von `openssl genrsa -out privatekey.pem 512` zu generieren – 1024 wird nicht unterstützt. Schließen Sie gesamten Text der PEM-Datei einschließlich der Unix-Zeilenschaltungen (\n) ein (siehe Beispiel unten).<br/>Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja |
| tenantId | Die Mandanten-ID, die Ihrer Xero-Anwendung zugeordnet ist. Gilt für die OAuth 2.0-Authentifizierung.<br>Informationen zum Abrufen der Mandanten-ID finden Sie im Abschnitt [Check the tenants you're authorized to access](https://developer.xero.com/documentation/oauth2/auth-flow) (Überprüfen der Mandanten, denen Zugriff gewährt wurde). | Ja, für die OAuth 2.0-Authentifizierung |
| refreshToken | Gilt für die OAuth 2.0-Authentifizierung.<br/>Das OAuth 2.0-Aktualisierungstoken ist der Xero-Anwendung zugeordnet und wird zum Aktualisieren des Zugriffstokens verwendet. Das Zugriffstoken läuft nach 30 Minuten ab. In [diesem Artikel](https://developer.xero.com/documentation/oauth2/auth-flow) erfahren Sie, wie der Xero-Autorisierungsflow funktioniert und wie Sie das Aktualisierungstoken abrufen. Um ein Aktualisierungstoken zu erhalten, müssen Sie den [offline_access-Bereich](https://developer.xero.com/documentation/oauth2/scopes) anfordern. <br/>**Bekannte Beschränkung** : Beachten Sie, dass Xero das Aktualisierungstoken zurücksetzt, nachdem es zur Aktualisierung des Zugriffstokens verwendet wurde. Bei operationalisierten Workloads müssen Sie vor jeder Ausführung der Kopieraktivität ein gültiges Aktualisierungstoken festlegen, das von Azure Data Factory verwendet werden kann.<br/>Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja, für die OAuth 2.0-Authentifizierung |
| useEncryptedEndpoints | Gibt an, ob die Endpunkte der Datenquelle mit HTTPS verschlüsselt sind. Der Standardwert lautet „true“.  | Nein |
| useHostVerification | Gibt an, ob der Hostname im Zertifikat des Servers mit dem Hostnamen des Servers übereinstimmen muss, wenn eine Verbindung über TLS hergestellt wird. Der Standardwert lautet „true“.  | Nein |
| usePeerVerification | Gibt an, ob die Identität des Servers überprüft werden soll, wenn eine Verbindung über TLS hergestellt wird. Der Standardwert lautet „true“.  | Nein |

**Beispiel: OAuth 2.0-Authentifizierung**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "connectionProperties": { 
                "host": "api.xero.com",
                "authenticationType":"OAuth_2.0", 
                "consumerKey": {
                    "type": "SecureString",
                    "value": "<consumer key>"
                },
                "privateKey": {
                    "type": "SecureString",
                    "value": "<private key>"
                },
                "tenantId": "<tenant ID>", 
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

**Beispiel: OAuth 1.0-Authentifizierung**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "connectionProperties": {
                "host": "api.xero.com", 
                "authenticationType":"OAuth_1.0", 
                "consumerKey": {
                    "type": "SecureString",
                    "value": "<consumer key>"
                },
                "privateKey": {
                    "type": "SecureString",
                    "value": "<private key>"
                }, 
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}
```

**Beispiel für den Wert eines privaten Schlüssels:**

Schließen Sie gesamten Text der PEM-Datei einschließlich der Unix-Zeilenschaltungen (\n) ein.

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom Xero-Dataset unterstützt werden.

Legen Sie zum Kopieren von Daten aus Xero die „type“-Eigenschaft des Datasets auf **XeroObject** fest. Folgende Eigenschaften werden unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **XeroObject** | Ja |
| tableName | Der Name der Tabelle. | Nein (wenn „query“ in der Aktivitätsquelle angegeben ist) |

**Beispiel**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Xero-Quelle unterstützt werden.

### <a name="xero-as-source"></a>Xero als Quelle

Legen Sie zum Kopieren von Daten aus Xero den Quelltyp in der Kopieraktivität auf **XeroSource** fest. Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **XeroSource** | Ja |
| Abfrage | Verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. Beispiel: `"SELECT * FROM Contacts"`. | Nein (wenn „tableName“ im Dataset angegeben ist) |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromXero",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Xero input dataset name>",
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
                "type": "XeroSource",
                "query": "SELECT * FROM Contacts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Beachten Sie Folgendes, wenn Sie die Xero-Abfrage angeben:

- Tabellen mit komplexen Elementen werden in mehrere Tabellen aufgeteilt. Beispielsweise weist die Tabelle zu den Banktransaktionen eine komplexe Datenstruktur „LineItems“ auf, sodass Daten von Banktransaktion den Tabellen `Bank_Transaction` und `Bank_Transaction_Line_Items` mit `Bank_Transaction_ID` als Fremdschlüssel zugeordnet werden, um sie miteinander verknüpfen.

- Xero-Daten sind über zwei Schemas verfügbar: `Minimal` (Standardeinstellung) und `Complete`. Das gesamte Schema enthält erforderliche Aufruftabellen, die zusätzliche Daten (z.B. eine ID-Spalte) erfordern, bevor Sie die gewünschte Abfrage vornehmen können.

Die folgenden Tabellen enthalten die gleichen Informationen im Schema „Minimal“ und „Complete“. Verwenden Sie zum Verringern der Anzahl der API-Aufrufe das Schema „Minimal“ (Standardeinstellung).

- Bank_Transactions
- Contact_Groups 
- Kontakte 
- Contacts_Sales_Tracking_Categories 
- Contacts_Phones 
- Contacts_Addresses 
- Contacts_Purchases_Tracking_Categories 
- Credit_Notes 
- Credit_Notes_Allocations 
- Expense_Claims 
- Expense_Claim_Validation_Errors
- Invoices 
- Invoices_Credit_Notes
- Invoices_Prepayments 
- Invoices_Overpayments 
- Manual_Journals 
- Overpayments 
- Overpayments_Allocations 
- Prepayments 
- Overpayments_Allocations 
- Receipts 
- Receipt_Validation_Errors 
- Tracking_Categories

Die folgenden Tabellen können nur mit dem Schema „Complete“ abgefragt werden:

- Complete.Bank_Transaction_Line_Items 
- Complete.Bank_Transaction_Line_Item_Tracking 
- Complete.Contact_Group_Contacts 
- Complete.Contacts_Contact_Persons 
- Complete.Credit_Note_Line_Items 
- Complete.Credit_Notes_Line_Items_Tracking 
- Complete.Expense_Claim_Payments 
- Complete.Expense_Claim_Receipts 
- Complete.Invoice_Line_Items 
- Complete.Invoices_Line_Items_Tracking
- Complete.Manual_Journal_Lines 
- Complete.Manual_Journal_Line_Tracking 
- Complete.Overpayment_Line_Items 
- Complete.Overpayment_Line_Items_Tracking 
- Complete.Prepayment_Line_Items 
- Complete.Prepayment_Line_Item_Tracking 
- Complete.Receipt_Line_Items 
- Complete.Receipt_Line_Item_Tracking 
- Complete.Tracking_Category_Options

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nächste Schritte
Eine Liste der von der Kopieraktivität unterstützten Datenspeicher finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

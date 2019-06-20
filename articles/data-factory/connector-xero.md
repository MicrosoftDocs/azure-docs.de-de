---
title: Kopieren von Daten aus Xero mithilfe von Azure Data Factory (Vorschau) | Microsoft-Dokumentation
description: Erfahren Sie, wie Daten aus Xero mithilfe einer Kopieraktivität in eine Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
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
ms.openlocfilehash: 6793fbcc50711e10231b87fa6e1f11f54f90d325
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60445433"
---
# <a name="copy-data-from-xero-using-azure-data-factory-preview"></a>Kopieren von Daten aus Xero mithilfe von Azure Data Factory (Vorschau)

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus Xero zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

> [!IMPORTANT]
> Dieser Connector befindet sich derzeit in der Vorschauversion. Sie können ihn ausprobieren und Feedback geben. Wenden Sie sich an den [Azure-Support](https://azure.microsoft.com/support/), wenn Sie in Ihrer Lösung eine Abhängigkeit von Connectors verwenden möchten, die sich in der Vorschauphase befinden.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Sie können Daten aus Xero in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser Xero-Connector unterstützt insbesondere Folgendes:

- [Private Anwendung](https://developer.xero.com/documentation/getting-started/api-application-types) von Xero, jedoch keine öffentlich Anwendung.
- Alle Xero-Tabellen (API-Endpunkte) mit Ausnahme von „Reports“. 

Azure Data Factory enthält einen integrierten Treiber zum Sicherstellen der Konnektivität. Daher müssen Sie mit diesem Connector keinen Treiber manuell installieren.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den Xero-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit Xero verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **Xero** | Ja |
| host | Der Endpunkt des Xero-Servers (`api.xero.com`).  | Ja |
| consumerKey | Der Consumerschlüssel, der der Xero-Anwendung zugeordnet ist. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja |
| privateKey | Der private Schlüssel aus der PEM-Datei, der für Ihre private Xero-Anwendung generiert wurde. Weitere Informationen finden Sie unter [Erstellen eines öffentlichen/privaten Schlüsselpaars](https://developer.xero.com/documentation/api-guides/create-publicprivate-key). Achten Sie darauf, **„privatekey.pem“ mit NumBits von 512** mithilfe von `openssl genrsa -out privatekey.pem 512` zu generieren; 1024 wird nicht unterstützt. Schließen Sie gesamten Text der PEM-Datei einschließlich der Unix-Zeilenschaltungen (\n) ein (siehe Beispiel unten).<br/><br/>Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja |
| useEncryptedEndpoints | Gibt an, ob die Endpunkte der Datenquelle mit HTTPS verschlüsselt sind. Der Standardwert lautet „true“.  | Nein |
| useHostVerification | Gibt an, ob der Hostname im Zertifikat des Servers mit dem Hostnamen des Servers übereinstimmen muss, wenn eine Verbindung über SSL hergestellt wird. Der Standardwert lautet „true“.  | Nein |
| usePeerVerification | Gibt an, ob die Identität des Servers bei Verbindung über SSL überprüft werden soll. Der Standardwert lautet „true“.  | Nein |

**Beispiel:**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "host" : "api.xero.com",
            "consumerKey": {
                 "type": "SecureString",
                 "value": "<consumerKey>"
            },
            "privateKey": {
                 "type": "SecureString",
                 "value": "<privateKey>"
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

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **XeroObject** | Ja |
| tableName | Name der Tabelle. | Nein (wenn „query“ in der Aktivitätsquelle angegeben ist) |

**Beispiel**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Xero-Quelle unterstützt werden.

### <a name="xero-as-source"></a>Xero als Quelle

Legen Sie zum Kopieren von Daten aus Xero den Quelltyp in der Kopieraktivität auf **XeroSource** fest. Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **XeroSource** | Ja |
| query | Verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. Beispiel: `"SELECT * FROM Contacts"`. | Nein (wenn „tableName“ im Dataset angegeben ist) |

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

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der von der Kopieraktivität unterstützten Datenspeicher finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

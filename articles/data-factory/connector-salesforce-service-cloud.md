---
title: Kopieren von Daten aus und in Salesforce Service Cloud
description: Erfahren Sie, wie Sie mithilfe einer Kopieraktivität in einer Data Factory-Pipeline Daten aus Salesforce Service Cloud in unterstützte Senkendatenspeicher oder aus unterstützten Quelldatenspeichern in Salesforce Service Cloud kopieren.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/02/2021
ms.openlocfilehash: 4075552e2070eba653fba54c7db1d021016644c7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369763"
---
# <a name="copy-data-from-and-to-salesforce-service-cloud-by-using-azure-data-factory"></a>Kopieren von Daten aus und in Salesforce Service Cloud mit Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus und in Salesforce Service Cloud zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Salesforce Service Cloud-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus Salesforce Service Cloud in beliebige unterstützte Senkendatenspeicher kopieren. Zudem können Sie Daten aus einem beliebigen unterstützten Quelldatenspeicher in Salesforce Service Cloud kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser Salesforce Service Cloud-Connector unterstützt insbesondere Folgendes:

- Salesforce Developer, Professional, Enterprise oder Unlimited Edition.
- Datenkopiervorgänge aus der und in die Produktionsumgebung, den Sandkasten und die benutzerdefinierte Domäne von Salesforce.

Der Salesforce-Connector baut auf der Salesforce REST/Bulk-API auf. Beim Kopieren von Daten aus Salesforce verwendet der Connector standardmäßig [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) und wählt basierend auf der Datengröße automatisch zwischen REST-API und Bulk-API. Bei einem umfangreichen Resultset wird die Bulk-API verwendet, um eine bessere Leistung zu erzielen. Beim Schreiben von Daten in Salesforce verwendet der Connector [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) der Bulk-API. Sie können die zum Lesen/Schreiben von Daten verwendete API-Version auch explizit über die Eigenschaft [`apiVersion` ](#linked-service-properties) im verknüpften Dienst festlegen.

## <a name="prerequisites"></a>Voraussetzungen

API-Berechtigungen müssen in Salesforce aktiviert sein. Weitere Informationen finden Sie unter [Enable API access in Salesforce by permission set](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/) (Aktivieren des API-Zugriffs in Salesforce mithilfe eines Berechtigungssatzes).

## <a name="salesforce-request-limits"></a>Anforderungslimits in Salesforce

Salesforce weist Grenzwerte sowohl für die Gesamtanzahl von API-Anforderungen als auch für die Anzahl gleichzeitiger API-Anforderungen auf. Beachten Sie folgende Punkte:

- Wenn die Anzahl von gleichzeitigen Anforderungen das Limit überschreitet, setzt eine Drosselung ein, und es werden zufällig generierte Fehler angezeigt.
- Wenn die Gesamtanzahl von Anforderungen das Limit überschreitet, wird das Salesforce-Konto 24 Stunden lang gesperrt.

In beiden Szenarien erhalten Sie möglicherweise auch die Fehlermeldung „REQUEST_LIMIT_EXCEEDED“. Weitere Informationen finden Sie im Abschnitt „API Request Limits“ (API-Anforderungslimits) im Dokument [Salesforce Developer Limits](https://developer.salesforce.com/docs/atlas.en-us.218.0.salesforce_app_limits_cheatsheet.meta/salesforce_app_limits_cheatsheet/salesforce_app_limits_platform_api.htm) (Salesforce-Entwicklerlimits).

## <a name="get-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den Salesforce Service Cloud-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit Salesforce verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type |Die type-Eigenschaft muss auf **SalesforceServiceCloud** festgelegt werden. |Ja |
| environmentUrl | Geben Sie die URL der Salesforce Service Cloud-Instanz an. <br> – Der Standardwert ist `"https://login.salesforce.com"`. <br> – Um Daten aus einem Sandkasten zu kopieren, geben Sie `"https://test.salesforce.com"` an. <br> – Geben Sie zum Kopieren von Daten aus einer benutzerdefinierten Domäne z.B. `"https://[domain].my.salesforce.com"` an. |Nein |
| username |Geben Sie einen Benutzernamen für das Benutzerkonto an. |Ja |
| password |Geben Sie ein Kennwort für das Benutzerkonto an.<br/><br/>Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). |Ja |
| securityToken |Geben Sie ein Sicherheitstoken für das Benutzerkonto an. <br/><br/>Allgemeine Informationen zu Sicherheitstoken finden Sie unter [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)(Sicherheit und die API). Das Sicherheits Token kann nur übersprungen werden, wenn Sie die IP-Adresse der Integration Runtime zur Liste [vertrauenswürdige IP-Adressen](https://developer.salesforce.com/docs/atlas.en-us.securityImplGuide.meta/securityImplGuide/security_networkaccess.htm) in Salesforce hinzufügen. Weitere Informationen zur Verwendung von Azure Integration Runtime (Azure IR) finden Sie unter [IP-Adressen von Azure Integration Runtime](azure-integration-runtime-ip-addresses.md).<br/><br/>Anleitungen zum Abrufen und Zurücksetzen eines Sicherheitstokens finden Sie unter [Get a security token](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) (Abrufen eines Sicherheitstokens). Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). |Nein |
| apiVersion | Geben Sie die zu verwendende Salesforce REST/Bulk-API-Version an, z. B. `48.0`. Standardmäßig verwendet der Connector [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) zum Kopieren von Daten aus Salesforce und [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) zum Kopieren von Daten in Salesforce. | Nein |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. | Nein |

**Beispiel: Speichern von Anmeldeinformationen in Data Factory**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Beispiel: Speichern von Anmeldeinformationen in Key Vault**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
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

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom Salesforce Service Cloud-Dataset unterstützt werden.

Wenn Sie Daten aus oder in Salesforce Service Cloud kopieren möchten, werden die folgenden Eigenschaften unterstützt.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf **SalesforceServiceCloudObject** festgelegt werden.  | Ja |
| objectApiName | Der Name des Salesforce-Objekts, aus dem Daten abgerufen werden sollen. | Quelle: Nein, Senke: Ja |

> [!IMPORTANT]
> Der Teil „__c“ von **API Name** wird für benutzerdefinierte Objekte benötigt.

![Data Factory – Salesforce-Verbindung – API-Name](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Beispiel:**

```json
{
    "name": "SalesforceServiceCloudDataset",
    "properties": {
        "type": "SalesforceServiceCloudObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce Service Cloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf **RelationalTable** festgelegt werden. | Ja |
| tableName | Name der Tabelle in Salesforce Service Cloud. | Nein (wenn „query“ in der Aktivitätsquelle angegeben ist) |

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Salesforce Service Cloud-Quelle und -Senke unterstützt werden.

### <a name="salesforce-service-cloud-as-a-source-type"></a>Salesforce Service Cloud als Quelltyp

Wenn Sie Daten aus Salesforce Service Cloud kopieren möchten, werden die folgenden Eigenschaften im Abschnitt **source** der Kopieraktivität unterstützt.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf **SalesforceServiceCloudSource** festgelegt werden. | Ja |
| Abfrage |Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. Sie können eine Abfrage vom Typ [Salesforce Object Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) oder eine SQL-92-Abfrage verwenden. Weitere Tipps finden Sie im Abschnitt [Tipps zu Abfragen](#query-tips). Wenn die Abfrage nicht angegeben ist, werden alle Daten des Salesforce Service Cloud-Objekts abgerufen, die im Dataset unter „objectApiName“ angegeben sind. | Nein (wenn „objectApiName“ im Dataset angegeben ist) |
| readBehavior | Gibt an, ob die vorhandenen Datensätze oder alle Datensätze (einschließlich gelöschter Datensätze) abgefragt werden sollen. Wird diese Option nicht angegeben, wird standardmäßig das erste Verhalten angewendet. <br>Zulässige Werte: **query** (Standard), **queryAll**  | Nein |

> [!IMPORTANT]
> Der Teil „__c“ von **API Name** wird für benutzerdefinierte Objekte benötigt.

![Data Factory – Salesforce-Verbindung – Liste der API-Namen](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce Service Cloud input dataset name>",
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
                "type": "SalesforceServiceCloudSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="salesforce-service-cloud-as-a-sink-type"></a>Salesforce Service Cloud als Senkentyp

Wenn Sie Daten in Salesforce Service Cloud kopieren möchten, werden die folgenden Eigenschaften im Abschnitt **sink** der Kopieraktivität unterstützt.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Senke der Kopieraktivität muss auf **SalesforceServiceCloudSink** festgelegt werden. | Ja |
| writeBehavior | Das Schreibverhalten für den Vorgang.<br/>Zulässige Werte: **Insert** und **Upsert**. | Nein (Standardwert ist „Insert“) |
| externalIdFieldName | Der Name des externen ID-Felds für den upsert-Vorgang. Das angegebene Feld muss als „Externes ID-Feld“ im Salesforce Service Cloud-Objekt definiert werden. Es kann keine NULL-Werte in den entsprechenden Eingabedaten haben. | Ja für „Upsert“ |
| writeBatchSize | Die Zeilenanzahl der Daten, die in jedem Batch in Salesforce Service Cloud geschrieben werden. | Nein (Standardwert ist 5000) |
| ignoreNullValues | Gibt an, ob NULL-Werte aus Eingabedaten während eines Schreibvorgangs ignoriert werden sollen.<br/>Zulässige Werte sind **true** und **false**.<br>- **true**: Daten im Zielobjekt bleiben unverändert, wenn Sie einen upsert- oder update-Vorgang ausführen. Fügt beim Ausführen eines insert-Vorgangs einen definierten Standardwert ein.<br/>- **false**: Daten im Zielobjekt werden auf NULL aktualisiert, wenn Sie einen upsert- oder update-Vorgang ausführen. Fügt beim Ausführen eines insert-Vorgangs einen NULL-Wert ein. | Nein (Standardwert ist „false“) |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyToSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce Service Cloud output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceServiceCloudSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Tipps zu Abfragen

### <a name="retrieve-data-from-a-salesforce-service-cloud-report"></a>Abrufen von Daten aus einem Salesforce Service Cloud-Bericht

Sie können Daten aus Salesforce Service Cloud-Berichten abrufen, indem Sie eine Abfrage als `{call "<report name>"}` angeben. z. B. `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-service-cloud-recycle-bin"></a>Abrufen von gelöschten Datensätzen aus dem Salesforce Service Cloud-Papierkorb

Zum Abfragen der vorläufig gelöschten Datensätze aus dem Salesforce Service Cloud-Papierkorb können Sie in der Abfrage `readBehavior` als `queryAll` angeben. 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Unterschied zwischen SOQL- und SQL-Abfragesyntax

Beim Kopieren von Daten aus Salesforce Service Cloud können Sie eine SOQL- oder eine SQL-Abfrage verwenden. Beachten Sie, dass diese beiden Sprachen unterschiedliche Syntax aufweisen und unterschiedliche Funktionen unterstützen. Mischen Sie beides nicht. Es wird empfohlen, die SOQL-Abfrage zu verwenden, die nativ von Salesforce Service Cloud unterstützt wird. In der folgenden Tabelle werden die Hauptunterschiede aufgeführt:

| Syntax | SOQL-Modus | SQL-Modus |
|:--- |:--- |:--- |
| Spaltenauswahl | Die zu kopierenden Felder müssen in der Abfrage aufgezählt werden, z.B. `SELECT field1, filed2 FROM objectname`. | `SELECT *` wird zusätzlich zur Spaltenauswahl unterstützt. |
| Anführungszeichen | Feld-/Objektnamen dürfen nicht in Anführungszeichen eingeschlossen werden. | Feld-/Objektnamen dürfen in Anführungszeichen eingeschlossen werden, z.B. `SELECT "id" FROM "Account"`. |
| Datetime-Format |  Details finden Sie [hier](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm), Beispiele im nächsten Abschnitt. | Details finden Sie [hier](/sql/odbc/reference/develop-app/date-time-and-timestamp-literals), Beispiele im nächsten Abschnitt. |
| Boolesche Werte | Dargestellt als `False` und `True`, z.B. `SELECT … WHERE IsDeleted=True`. | Dargestellt als 0 oder 1, z.B. `SELECT … WHERE IsDeleted=1`. |
| Umbenennen von Spalten | Wird nicht unterstützt. | Unterstützt, z.B. `SELECT a AS b FROM …`. |
| Beziehung | Unterstützt, z.B. `Account_vod__r.nvs_Country__c`. | Wird nicht unterstützt. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Abrufen von Daten mithilfe einer Where-Klausel für die Spalte „DateTime“

Achten Sie beim Angeben der SOQL- oder SQL-Abfrage auf den Unterschied beim DateTime-Format. Beispiel:

* **SOQL-Beispiel**: `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **SQL-Beispiel**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_query-truncated"></a>Fehler MALFORMED_QUERY: Abgeschnitten

Wenn der Fehler „MALFORMED_QUERY: Truncated“ auftritt, ist dies normalerweise darauf zurückzuführen, dass Sie die Spalte vom Typ JunctionIdList in den Daten verwenden und Salesforce die Unterstützung solcher Daten mit einer großen Anzahl von Zeilen einschränkt. Um dies zu verhindern, versuchen Sie, die Spalte JunctionIdList auszuschließen oder die Anzahl der zu kopierenden Zeilen zu begrenzen (Sie können in mehrere Kopiervorgänge partitionieren).

## <a name="data-type-mapping-for-salesforce-service-cloud"></a>Datentypzuordnung für Salesforce Service Cloud

Beim Kopieren von Daten aus Salesforce Service Cloud werden die folgenden Zuordnungen von Salesforce Service Cloud-Datentypen zu Data Factory-Zwischendatentypen verwendet. Weitere Informationen dazu, wie die Kopieraktivität das Quellschema und den Datentyp zur Senke zuordnet, finden Sie unter [Schema- und Datentypzuordnungen](copy-activity-schema-and-type-mapping.md).

| Salesforce Service Cloud-Datentyp | Data Factory-Zwischendatentyp |
|:--- |:--- |
| Auto Number |String |
| Checkbox |Boolean |
| Währung |Decimal |
| Date |Datetime |
| Date/Time |Datetime |
| Email |String |
| id |String |
| Lookup Relationship |String |
| Multi-Select Picklist |String |
| Number |Decimal |
| Percent |Decimal |
| Phone |String |
| Picklist |String |
| Text |String |
| Text Area |String |
| Text Area (Long) |String |
| Text Area (Rich) |String |
| Text (Encrypted) |String |
| URL |String |

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
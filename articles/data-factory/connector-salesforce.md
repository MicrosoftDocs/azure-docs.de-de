---
title: Kopieren von Daten aus und in Salesforce
description: Erfahren Sie, wie Sie mithilfe einer Kopieraktivität in einer Data Factory-Pipeline Daten aus Salesforce in unterstützte Senkendatenspeicher oder aus unterstützten Quelldatenspeichern nach Salesforce kopieren.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/13/2020
ms.openlocfilehash: a82606be62007816d545942161774e776c38a4e3
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637291"
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Kopieren von Daten aus und nach Salesforce mit Azure Data Factory

> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-salesforce-connector.md)
> * [Aktuelle Version](connector-salesforce.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus und nach Salesforce zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Der Salesforce-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus Salesforce in beliebige unterstützte Senkendatenspeicher kopieren. Zudem können Sie Daten aus einem beliebigen unterstützten Quelldatenspeicher nach Salesforce kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser Salesforce-Connector unterstützt insbesondere Folgendes:

- Salesforce Developer, Professional, Enterprise oder Unlimited Edition.
- Datenkopiervorgänge aus der und in die Produktionsumgebung, den Sandkasten und die benutzerdefinierte Domäne von Salesforce.

Der Salesforce-Connector baut auf der Salesforce REST/Bulk-API auf. Standardmäßig verwendet der Connector [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) zum Kopieren von Daten aus Salesforce und [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) zum Kopieren von Daten in Salesforce. Sie können die zum Lesen/Schreiben von Daten verwendete API-Version auch explizit über die Eigenschaft [`apiVersion` ](#linked-service-properties) im verknüpften Dienst festlegen.

## <a name="prerequisites"></a>Voraussetzungen

API-Berechtigungen müssen in Salesforce aktiviert sein. Weitere Informationen finden Sie unter [Enable API access in Salesforce by permission set](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/) (Aktivieren des API-Zugriffs in Salesforce mithilfe eines Berechtigungssatzes).

## <a name="salesforce-request-limits"></a>Anforderungslimits in Salesforce

Salesforce weist Grenzwerte sowohl für die Gesamtanzahl von API-Anforderungen als auch für die Anzahl gleichzeitiger API-Anforderungen auf. Beachten Sie folgende Punkte:

- Wenn die Anzahl von gleichzeitigen Anforderungen das Limit überschreitet, setzt eine Drosselung ein, und es werden zufällig generierte Fehler angezeigt.
- Wenn die Gesamtanzahl von Anforderungen das Limit überschreitet, wird das Salesforce-Konto 24 Stunden lang gesperrt.

In beiden Szenarien erhalten Sie möglicherweise auch die Fehlermeldung „REQUEST_LIMIT_EXCEEDED“. Weitere Informationen finden Sie im Abschnitt „API Request Limits“ (API-Anforderungslimits) im Dokument [Salesforce Developer Limits](https://developer.salesforce.com/docs/atlas.en-us.218.0.salesforce_app_limits_cheatsheet.meta/salesforce_app_limits_cheatsheet/salesforce_app_limits_platform_api.htm) (Salesforce-Entwicklerlimits).

## <a name="get-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den Salesforce-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit Salesforce verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type |Die type-Eigenschaft muss auf **Salesforce** festgelegt sein. |Ja |
| environmentUrl | Geben Sie die URL der Salesforce-Instanz an. <br> – Der Standardwert ist `"https://login.salesforce.com"`. <br> – Um Daten aus einem Sandkasten zu kopieren, geben Sie `"https://test.salesforce.com"` an. <br> – Geben Sie zum Kopieren von Daten aus einer benutzerdefinierten Domäne z.B. `"https://[domain].my.salesforce.com"` an. |Nein  |
| username |Geben Sie einen Benutzernamen für das Benutzerkonto an. |Ja |
| password |Geben Sie ein Kennwort für das Benutzerkonto an.<br/><br/>Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). |Ja |
| securityToken |Geben Sie ein Sicherheitstoken für das Benutzerkonto an. <br/><br/>Allgemeine Informationen zu Sicherheitstoken finden Sie unter [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)(Sicherheit und die API). Das Sicherheits Token kann nur übersprungen werden, wenn Sie die IP-Adresse der Integration Runtime zur Liste [vertrauenswürdige IP-Adressen](https://developer.salesforce.com/docs/atlas.en-us.securityImplGuide.meta/securityImplGuide/security_networkaccess.htm) in Salesforce hinzufügen. Weitere Informationen zur Verwendung von Azure Integration Runtime (Azure IR) finden Sie unter [IP-Adressen von Azure Integration Runtime](azure-integration-runtime-ip-addresses.md).<br/><br/>Anleitungen zum Abrufen und Zurücksetzen eines Sicherheitstokens finden Sie unter [Get a security token](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) (Abrufen eines Sicherheitstokens). Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). |Nein  |
| apiVersion | Geben Sie die zu verwendende Salesforce REST/Bulk-API-Version an, z. B. `48.0`. Standardmäßig verwendet der Connector [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) zum Kopieren von Daten aus Salesforce und [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) zum Kopieren von Daten in Salesforce. | Nein  |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. | Nein für die Quelle. Ja für die Senke, wenn der mit der Quelle verknüpfte Dienst keine Integration Runtime aufweist. |

>[!IMPORTANT]
>Beim Kopieren von Daten in Salesforce kann die standardmäßige Azure Integration Runtime nicht zum Ausführen des Kopiervorgangs verwendet werden. Mit anderen Worten, wenn für den mit der Quelle verknüpften Dienst keine Integration Runtime festgelegt wurde, [erstellen Sie explizit eine Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) in der Nähe Ihrer Salesforce-Instanz. Ordnen Sie den mit Salesforce verknüpften Dienst wie im folgenden Beispiel zu.

**Beispiel: Speichern von Anmeldeinformationen in Data Factory**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
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
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
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

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom Salesforce-Dataset unterstützt werden.

Legen Sie zum Kopieren von Daten aus und nach Salesforce die type-Eigenschaft des Datasets auf **SalesforceObject** fest. Die folgenden Eigenschaften werden unterstützt.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf **SalesforceObject** festgelegt sein.  | Ja |
| objectApiName | Der Name des Salesforce-Objekts, aus dem Daten abgerufen werden sollen. | Quelle: Nein, Senke: Ja |

> [!IMPORTANT]
> Der Teil „__c“ von **API Name** wird für benutzerdefinierte Objekte benötigt.

![Data Factory – Salesforce-Verbindung – API-Name](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Beispiel:**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

>[!NOTE]
>Abwärtskompatibilität: Wenn Sie beim Kopieren von Daten aus Salesforce das vorherige Dataset vom Typ „RelationalTable“ verwenden, funktioniert dieses weiterhin. Es wird jedoch eine Empfehlung angezeigt, stattdessen den neuen Typ „SalesforceObject“ zu verwenden.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf **RelationalTable** festgelegt werden. | Ja |
| tableName | Name der Tabelle in Salesforce. | Nein (wenn „query“ in der Aktivitätsquelle angegeben ist) |

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Salesforce-Quelle und -Senke unterstützt werden.

### <a name="salesforce-as-a-source-type"></a>Salesforce als Quelltyp

Legen Sie zum Kopieren von Daten aus Salesforce den Quelltyp in der Kopieraktivität auf **SalesforceSource** fest. Die folgenden Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf **SalesforceSource** festgelegt werden. | Ja |
| Abfrage |Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. Sie können eine Abfrage vom Typ [Salesforce Object Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) oder eine SQL-92-Abfrage verwenden. Weitere Tipps finden Sie im Abschnitt [Tipps zu Abfragen](#query-tips). Wenn die Abfrage nicht angegeben ist, werden alle Daten des Salesforce-Objekts abgerufen, die im Dataset unter „objectApiName“ angegeben sind. | Nein (wenn „objectApiName“ im Dataset angegeben ist) |
| readBehavior | Gibt an, ob die vorhandenen Datensätze oder alle Datensätze (einschließlich gelöschter Datensätze) abgefragt werden sollen. Wird diese Option nicht angegeben, wird standardmäßig das erste Verhalten angewendet. <br>Zulässige Werte: **query** (Standard), **queryAll**  | Nein  |

> [!IMPORTANT]
> Der Teil „__c“ von **API Name** wird für benutzerdefinierte Objekte benötigt.

![Data Factory – Salesforce-Verbindung – Liste der API-Namen](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
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
                "type": "SalesforceSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

>[!NOTE]
>Abwärtskompatibilität: Wenn Sie beim Kopieren von Daten aus Salesforce den vorherigen Typ „RelationalSource“ verwenden, funktioniert die Quelle weiterhin. Es wird jedoch eine Empfehlung angezeigt, stattdessen den neuen Typ „SalesforceSource“ zu verwenden.

### <a name="salesforce-as-a-sink-type"></a>Salesforce als Senkentyp

Legen Sie zum Kopieren von Daten nach Salesforce den Senkentyp in der Kopieraktivität auf **SalesforceSink** fest. Die folgenden Eigenschaften werden im Abschnitt **sink** der Kopieraktivität unterstützt.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Senke der Kopieraktivität muss auf **SalesforceSink** festgelegt werden. | Ja |
| writeBehavior | Das Schreibverhalten für den Vorgang.<br/>Zulässige Werte: **Insert** und **Upsert** . | Nein (Standardwert ist „Insert“) |
| externalIdFieldName | Der Name des externen ID-Felds für den upsert-Vorgang. Das angegebene Feld muss als „Externes ID-Feld“ im Salesforce-Objekt definiert werden. Es kann keine NULL-Werte in den entsprechenden Eingabedaten haben. | Ja für „Upsert“ |
| writeBatchSize | Die Zeilenanzahl der Daten, die in jedem Batch in Salesforce geschrieben werden. | Nein (Standardwert ist 5000) |
| ignoreNullValues | Gibt an, ob NULL-Werte aus Eingabedaten während eines Schreibvorgangs ignoriert werden sollen.<br/>Zulässige Werte sind **true** und **false** .<br>- **true** : Daten im Zielobjekt bleiben unverändert, wenn Sie einen upsert- oder update-Vorgang ausführen. Fügt beim Ausführen eines insert-Vorgangs einen definierten Standardwert ein.<br/>- **false** : Daten im Zielobjekt werden auf NULL aktualisiert, wenn Sie einen upsert- oder update-Vorgang ausführen. Fügt beim Ausführen eines insert-Vorgangs einen NULL-Wert ein. | Nein (Standardwert ist „false“) |

**Beispiel: Salesforce-Senke in einer Kopieraktivität**

```json
"activities":[
    {
        "name": "CopyToSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceSink",
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

### <a name="retrieve-data-from-a-salesforce-report"></a>Abrufen von Daten aus einem Salesforce-Bericht

Sie können Daten aus Salesforce-Berichten abrufen, indem Sie eine Abfrage als `{call "<report name>"}` angeben. z. B. `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>Abrufen von gelöschten Datensätzen aus dem Salesforce-Papierkorb

Zum Abfragen der vorläufig gelöschten Datensätze aus dem Salesforce-Papierkorb können Sie in der Abfrage `readBehavior` als `queryAll` angeben. 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Unterschied zwischen SOQL- und SQL-Abfragesyntax

Beim Kopieren von Daten aus Salesforce können Sie eine SOQL- oder eine SQL-Abfrage verwenden. Beachten Sie, dass diese beiden Sprachen unterschiedliche Syntax aufweisen und unterschiedliche Funktionen unterstützen. Mischen Sie beides nicht. Es wird empfohlen, die SOQL-Abfrage zu verwenden, die nativ von Salesforce unterstützt wird. In der folgenden Tabelle werden die Hauptunterschiede aufgeführt:

| Syntax | SOQL-Modus | SQL-Modus |
|:--- |:--- |:--- |
| Spaltenauswahl | Die zu kopierenden Felder müssen in der Abfrage aufgezählt werden, z.B. `SELECT field1, filed2 FROM objectname`. | `SELECT *` wird zusätzlich zur Spaltenauswahl unterstützt. |
| Anführungszeichen | Feld-/Objektnamen dürfen nicht in Anführungszeichen eingeschlossen werden. | Feld-/Objektnamen dürfen in Anführungszeichen eingeschlossen werden, z.B. `SELECT "id" FROM "Account"`. |
| Datetime-Format |  Details finden Sie [hier](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm), Beispiele im nächsten Abschnitt. | Details finden Sie [hier](/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017), Beispiele im nächsten Abschnitt. |
| Boolesche Werte | Dargestellt als `False` und `True`, z.B. `SELECT … WHERE IsDeleted=True`. | Dargestellt als 0 oder 1, z.B. `SELECT … WHERE IsDeleted=1`. |
| Umbenennen von Spalten | Wird nicht unterstützt. | Unterstützt, z.B. `SELECT a AS b FROM …`. |
| Beziehung | Unterstützt, z.B. `Account_vod__r.nvs_Country__c`. | Wird nicht unterstützt. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Abrufen von Daten mithilfe einer Where-Klausel für die Spalte „DateTime“

Achten Sie beim Angeben der SOQL- oder SQL-Abfrage auf den Unterschied beim DateTime-Format. Beispiel:

* **SOQL-Beispiel** : `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **SQL-Beispiel** : `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_query-truncated"></a>Fehler MALFORMED_QUERY: Abgeschnitten

Wenn der Fehler „MALFORMED_QUERY: Truncated“ auftritt, ist dies normalerweise darauf zurückzuführen, dass Sie die Spalte vom Typ JunctionIdList in den Daten verwenden und Salesforce die Unterstützung solcher Daten mit einer großen Anzahl von Zeilen einschränkt. Um dies zu verhindern, versuchen Sie, die Spalte JunctionIdList auszuschließen oder die Anzahl der zu kopierenden Zeilen zu begrenzen (Sie können in mehrere Kopiervorgänge partitionieren).

## <a name="data-type-mapping-for-salesforce"></a>Datentypzuordnung für Salesforce

Beim Kopieren von Daten aus Salesforce werden die folgenden Zuordnungen von Salesforce-Datentypen zu Data Factory-Zwischendatentypen verwendet. Weitere Informationen dazu, wie die Kopieraktivität das Quellschema und den Datentyp zur Senke zuordnet, finden Sie unter [Schema- und Datentypzuordnungen](copy-activity-schema-and-type-mapping.md).

| Salesforce-Datentyp | Data Factory-Zwischendatentyp |
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
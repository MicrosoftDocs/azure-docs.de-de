---
title: Kopieren von Daten in oder aus Azure Data Explorer
description: Erfahren Sie, wie Daten mithilfe einer Kopieraktivität in einer Azure Data Factory-Pipeline in oder aus Azure Data Explorer kopiert werden.
ms.author: orspodek
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2020
ms.openlocfilehash: 16126e8b9e5c34529016018273edcf65a31e2280
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379980"
---
# <a name="copy-data-to-or-from-azure-data-explorer-by-using-azure-data-factory"></a>Kopieren von Daten in oder aus Azure Data Explorer mithilfe von Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten in oder aus [Azure Data Explorer](/azure/data-explorer/data-explorer-overview) zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

>[!TIP]
>Weitere Informationen zur Integration von Azure Data Factory und Azure Data Explorer im Allgemeinen finden Sie unter [Integration von Azure Data Explorer und Azure Data Factory](/azure/data-explorer/data-factory-integration).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Azure Data Explorer-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus jedem unterstützten Quelldatenspeicher in Azure Data Explorer kopieren. Zudem können Sie Daten aus Azure Data Explorer in jeden unterstützten Senkendatenspeicher kopieren. Die für die Kopieraktivität als Quellen oder Senken unterstützten Datenspeicher finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

>[!NOTE]
>Das Kopieren von Daten in oder aus Azure Data Explorer über einen lokalen Datenspeicher mithilfe der selbstgehosteten Integration Runtime wird in Version 3.14 und höher unterstützt.

Mit dem Azure Data Explorer-Connector ist Folgendes möglich:

* Kopieren von Daten mithilfe der Azure Active Directory-Anwendungstokenauthentifizierung (Azure AD) mit einem **Dienstprinzipal**
* Als Quelle: Abrufen von Daten mithilfe einer KQL-Abfrage (Kusto)
* Als Senke: Anfügen von Daten an eine Zieltabelle

## <a name="getting-started"></a>Erste Schritte

>[!TIP]
>Eine exemplarische Vorgehensweise zur Verwendung des Azure Data Explorer-Connectors finden Sie unter [Kopieren von Daten in Azure Data Explorer mithilfe von Azure Data Factory](/azure/data-explorer/data-factory-load-data) und [Massenkopieren aus einer Datenbank zu Azure Data Explorer](/azure/data-explorer/data-factory-template).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den Azure Data Explorer-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Der Azure Data Explorer-Connector verwendet die Dienstprinzipalauthentifizierung. Führen Sie die folgenden Schritte aus, um einen Dienstprinzipal zu erstellen und Berechtigungen zu erteilen:

1. Registrieren Sie eine Anwendungsentität in Azure Active Directory durch Ausführen der Schritte unter [Registrieren der Anwendung bei einem Azure AD-Mandanten](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Notieren Sie sich die folgenden Werte, die Sie zum Definieren des verknüpften Diensts verwenden:

    - Anwendungs-ID
    - Anwendungsschlüssel
    - Mandanten-ID

2. Erteilen Sie dem Dienstprinzipal die geeigneten Berechtigungen in Azure Data Explorer. Unter [Verwalten der Berechtigungen für Datenbanken in Azure Data Explorer](/azure/data-explorer/manage-database-permissions) finden Sie ausführliche Informationen zu Rollen und Berechtigungen sowie zur Verwaltung von Berechtigungen. Gehen Sie wie folgt vor:

    - Weisen Sie der Datenbank **als Quelle** mindestens die Rolle **Database viewer** (Anzeigender Datenbankbenutzer) zu.
    - Weisen Sie der Datenbank **als Senke** mindestens die Rolle **Database ingestor** (Datenbankerfasser) zu.

>[!NOTE]
>Wenn Sie die Data Factory-Benutzeroberfläche für die Erstellung verwenden, wird Ihr Anmeldebenutzerkonto verwendet, um Azure Data Explorer-Cluster, -Datenbanken und -Tabellen aufzulisten. Geben Sie den Namen manuell ein, wenn Sie keine Berechtigung für diese Vorgänge besitzen.

Folgende Eigenschaften werden für den mit Azure Data Explorer verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft muss auf **AzureDataExplorer** festgelegt werden. | Ja |
| endpoint | Endpunkt-URL des Azure Data Explorer-Clusters im Format `https://<clusterName>.<regionName>.kusto.windows.net` | Ja |
| database | Name der Datenbank | Ja |
| tenant | Geben Sie die Mandanteninformationen (Domänenname oder Mandanten-ID) für Ihre Anwendung an. Dies wird in der [Kusto-Verbindungszeichenfolge](/azure/kusto/api/connection-strings/kusto#application-authentication-properties) als „Autoritäts-ID“ bezeichnet. Sie können ab abrufen, indem Sie im Azure-Portal mit dem Mauszeiger auf den Bereich oben rechts zeigen. | Ja |
| servicePrincipalId | Geben Sie die Client-ID der Anwendung an. Dies wird in der [Kusto-Verbindungszeichenfolge](/azure/kusto/api/connection-strings/kusto#application-authentication-properties) als „AAD-Anwendungsclient-ID“ bezeichnet. | Ja |
| servicePrincipalKey | Geben Sie den Schlüssel der Anwendung an. Dies wird in der [Kusto-Verbindungszeichenfolge](/azure/kusto/api/connection-strings/kusto#application-authentication-properties) als „AAD-Anwendungsschlüssel“ bezeichnet. Markieren Sie dieses Feld als **SecureString**, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf in Azure Key Vault gespeicherte sichere Daten](store-credentials-in-key-vault.md). | Ja |

**Beispiel für Eigenschaften des verknüpften Diensts:**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie unter [Datasets in Azure Data Factory](concepts-datasets-linked-services.md). Dieser Abschnitt enthält die Eigenschaften, die mit dem Azure Data Explorer-Dataset unterstützt werden.

Legen Sie zum Kopieren von Daten in Azure Data Explorer die type-Eigenschaft des Datasets auf **AzureDataExplorerTable** fest.

Folgende Eigenschaften werden unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft muss auf **AzureDataExplorerTable** festgelegt werden. | Ja |
| table | Der Name der Tabelle, auf die der verknüpfte Dienst verweist. | Quelle: Ja, Senke: Nein |

**Beispiel für Dataseteigenschaften:**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "typeProperties": {
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den verfügbaren Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie unter [Pipelines und Aktivitäten in Azure Data Factory](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die mit Azure Data Explorer-Quellen und -Senken unterstützt werden.

### <a name="azure-data-explorer-as-source"></a>Azure Data Explorer als Quelle

Legen Sie zum Kopieren von Daten aus Azure Data Explorer die **type**-Eigenschaft in der Quelle der Kopieraktivität auf **AzureDataExplorerSource** fest. Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **AzureDataExplorerSource** | Ja |
| Abfrage | Eine in einem [KQL-Format](/azure/kusto/query/) angegebene schreibgeschützte Anforderung. Verwenden Sie die benutzerdefinierte KQL-Abfrage als Verweis. | Ja |
| queryTimeout | Die Wartezeit vor dem Timeout der Abfrageanforderung. Der Standardwert ist 10 Minuten (00:10:00), der zulässige maximale Wert 1 Stunde (01:00:00). | Nein |
| noTruncation | Gibt an, ob das zurückgegebene Resultset abgeschnitten werden soll. Standardmäßig wird das Ergebnis nach 500.000 Datensätzen oder 64 Megabyte (MB) abgeschnitten. Das Abschneiden wird dringend empfohlen, um das richtige Verhalten für die Aktivität sicherzustellen. |Nein |

>[!NOTE]
>Standardmäßig ist die Azure Data Explorer-Quelle auf 500.000 Datensätze oder 64 MB begrenzt. Um alle Datensätze ohne Abschneiden abzurufen, können Sie `set notruncation;` am Anfang Ihrer Abfrage angeben. Weitere Informationen finden Sie unter [Abfragegrenzwerte](/azure/kusto/concepts/querylimits).

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>Azure Data Explorer als Senke

Legen Sie zum Kopieren von Daten in Azure Data Explorer die type-Eigenschaft in der Senke der Kopieraktivität auf **AzureDataExplorerSink** fest. Folgende Eigenschaften werden im Abschnitt **sink** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft der Senke der Kopieraktivität muss auf Folgendes festgelegt werden: **AzureDataExplorerSink**. | Ja |
| ingestionMappingName | Der Name einer vorab erstellten [Zuordnung](/azure/kusto/management/mappings#csv-mapping) für eine Kusto-Tabelle. Zum Zuordnen der Spalten aus der Quelle zu Azure Data Explorer (gilt für [alle unterstützten Quellspeicher und -formate](copy-activity-overview.md#supported-data-stores-and-formats), einschließlich der Formate CSV, JSON und Avro) können Sie die Kopieraktivität [Spaltenzuordnung](copy-activity-schema-and-type-mapping.md) (implizit anhand des Namens oder explizit wie konfiguriert) und/oder Azure Data Explorer-Zuordnungen verwenden. | Nein |
| additionalProperties | Ein Eigenschaftenbehälter, mit dem Sie beliebige Erfassungseigenschaften angeben können, die nicht bereits von der Azure Data Explorer-Senke festgelegt sind. Dies kann besonders nützlich sein, um Erfassungstags anzugeben. Weitere Informationen finden Sie in der [Dokumentation zur Datenerfassung im Azure Data Explorer](/azure/data-explorer/ingestion-properties). | Nein |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>",
                "additionalProperties": {<additional settings for data ingestion>}
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Weitere Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität in Azure Data Factory](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nächste Schritte

* Eine Liste der Datenspeicher, die von der Kopieraktivität in Azure Data Factory als Quellen und Senken unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

* Weitere Informationen finden Sie unter [Kopieren von Daten in Azure Data Explorer mithilfe von Azure Data Factory](/azure/data-explorer/data-factory-load-data).
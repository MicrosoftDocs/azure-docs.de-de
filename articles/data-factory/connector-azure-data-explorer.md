---
title: Kopieren von Daten in oder aus Azure Data Explorer mithilfe von Azure Data Factory
description: Erfahren Sie, wie Daten mithilfe einer Kopieraktivität in einer Azure Data Factory-Pipeline in oder aus Azure Data Explorer kopiert werden.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: orspodek
ms.openlocfilehash: f501257903f3b7c621512f06d1c8c7109e22db1e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60394505"
---
# <a name="copy-data-to-or-from-azure-data-explorer-using-azure-data-factory"></a>Kopieren von Daten in oder aus Azure Data Explorer mithilfe von Azure Data Factory

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten in oder aus [Azure Data Explorer](../data-explorer/data-explorer-overview.md) zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Sie können Daten aus jedem unterstützten Quelldatenspeicher in Azure Data Explorer kopieren. Zudem können Sie Daten aus Azure Data Explorer in jeden unterstützten Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md).

>[!NOTE]
>Das Kopieren von Daten zwischen Azure Data Explorer und einem lokalen Datenspeicher wird mithilfe der selbstgehosteten Integration Runtime seit Version 3.14 unterstützt.

Mit dem Azure Data Explorer-Connector ist Folgendes möglich:

* Kopieren von Daten mithilfe der Azure Active Directory-Anwendungstokenauthentifizierung (Azure AD) mit einem **Dienstprinzipal**
* Als Quelle: Abrufen von Daten mithilfe einer KQL-Abfrage (Kusto)
* Als Senke: Anfügen von Daten an eine Zieltabelle

## <a name="getting-started"></a>Erste Schritte

>[!TIP]
>Eine exemplarische Vorgehensweise zur Verwendung des Azure Data Explorer-Connectors finden Sie unter [Copy data to Azure Data Explorer using Azure Data Factory (Kopieren von Daten aus Azure Data Factory in Azure Data Explorer)](../data-explorer/data-factory-load-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den Azure Data Explorer-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Der Azure Data Explorer-Connector verwendet die Dienstprinzipalauthentifizierung. Führen Sie die folgenden Schritte aus, um einen Dienstprinzipal zu erstellen und Berechtigungen zu erteilen:

1. Registrieren Sie eine Anwendungsentität in Azure Active Directory (Azure AD), indem Sie die Anleitungen unter [Registrieren Ihrer Anwendung bei einem Azure AD-Mandanten](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant) befolgen. Notieren Sie sich die folgenden Werte, die Sie zum Definieren des verknüpften Diensts verwenden:

    - Anwendungs-ID
    - Anwendungsschlüssel
    - Mandanten-ID

2. Erteilen Sie dem Dienstprinzipal in Azure Data Explorer geeignete Berechtigungen. Unter [Verwalten von Azure Data Explorer-Datenbankberechtigungen](../data-explorer/manage-database-permissions.md) finden Sie detaillierte Informationen zu Rollen und Berechtigungen sowie eine exemplarische Vorgehensweise zur Verwaltung von Berechtigungen. Gehen Sie so vor:

    - **Als Quelle**: Gewähren Sie der Datenbank mindestens die Rolle **Database viewer** (Datenbank-Viewer).
    - **Als Senke**: Gewähren Sie der Datenbank mindestens die Rolle **Database ingestor** (Datenbankerfasser).

>[!NOTE]
>Wenn Sie die ADF-Benutzeroberfläche für die Erstellung verwenden, kann es sein, dass für die Vorgänge der Auflistung von Datenbanken mit verknüpften Diensten oder von Tabellen im Dataset eine höhere Berechtigung erforderlich ist, die dem Dienstprinzipal erteilt werden muss. Alternativ können Sie Datenbank- und Tabellennamen manuell eingeben. Die Ausführung der Kopieraktivität funktioniert, solange dem Dienstprinzipal die entsprechende Berechtigung zum Lesen/Schreiben von Daten erteilt wird.

Folgende Eigenschaften werden für den mit Azure Data Explorer verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft muss auf **AzureDataExplorer** festgelegt werden. | Ja |
| endpoint | Endpunkt-URL des Azure Data Explorer-Clusters im Format `https://<clusterName>.<regionName>.kusto.windows.net` | Ja |
| database | Name der Datenbank | Ja |
| tenant | Geben Sie die Mandanteninformationen (Domänenname oder Mandanten-ID) für Ihre Anwendung an. Dies ist, was Sie normalerweise als **Autoritäts-ID** in der [Kusto-Verbindungszeichenfolge](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties) kennen. Diese können Sie abrufen, indem Sie auf den rechten oberen Bereich im Azure-Portal zeigen. | Ja |
| servicePrincipalId | Geben Sie die Client-ID der Anwendung an. Dies ist, was Sie normalerweise als **AAD-Anwendungsclient-ID** in der [Kusto-Verbindungszeichenfolge](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties) kennen. | Ja |
| servicePrincipalKey | Geben Sie den Schlüssel der Anwendung an. Dies ist, was Sie normalerweise als **AAD-Anwendungsschlüssel** in der [Kusto-Verbindungszeichenfolge](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties) kennen. Markieren Sie dieses Feld als **SecureString**, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja |

**Beispiel für Eigenschaften des verknüpften Diensts**

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

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom Azure Data Explorer-Dataset unterstützt werden.

Legen Sie zum Kopieren von Daten in Azure Data Explorer die type-Eigenschaft des Datasets auf **AzureDataExplorerTable** fest.

Folgende Eigenschaften werden unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft muss auf **AzureDataExplorerTable** festgelegt werden. | Ja |
| table | Der Name der Tabelle, auf die der verknüpfte Dienst verweist. | Quelle: Ja, Senke: Nein |

**Beispiel für Dataseteigenschaften**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "table": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Azure Data Explorer-Quelle und -Senke unterstützt werden.

### <a name="azure-data-explorer-as-source"></a>Azure Data Explorer als Quelle

Legen Sie zum Kopieren von Daten aus Azure Data Explorer die **type**-Eigenschaft in der Quelle der Kopieraktivität auf **AzureDataExplorerSource** fest. Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **AzureDataExplorerSource** | Ja |
| query | Eine in einem [KQL-Format](/azure/kusto/query/) angegebene schreibgeschützte Anforderung. Verwenden Sie die benutzerdefinierte KQL-Abfrage als Verweis. | Ja |
| queryTimeout | Die Wartezeit vor dem Timeout der Abfrageanforderung. Der Standardwert ist 10 Minuten (00:10:00), der zulässige maximale Wert 1 Stunde (01:00:00). | Nein |

>[!NOTE]
>Die Azure Data Explorer-Quelle hat in der Standardeinstellung ein Größenlimit von 500.000 Datensätzen oder 64 MB. Um alle Datensätze ohne Abschneiden abzurufen, können Sie `set notruncation;` am Anfang Ihrer Abfrage angeben. Weitere Details finden Sie unter [Abfragelimits](https://docs.microsoft.com/azure/kusto/concepts/querylimits).

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
| type | Die **type**-Eigenschaft der Senke der Kopieraktivität muss auf Folgendes festgelegt werden: **AzureDataExplorerSink** | Ja |
| ingestionMappingName | Der Name einer vorab erstellten **[Zuordnung](/azure/kusto/management/mappings#csv-mapping)** für eine Kusto-Tabelle. Um die Spalten aus der Quelle zu Azure Data Explorer zuzuordnen, was für **[alle unterstützten Quellspeicher/-formate](copy-activity-overview.md#supported-data-stores-and-formats)** einschließlich der Formate CSV/JSON/Avro usw. gilt, können Sie die Kopieraktivität [Spaltenzuordnung](copy-activity-schema-and-type-mapping.md) verwenden (implizit anhand des Namens oder explizit wie konfiguriert) und/oder Azure Data Explorer-Zuordnungen. | Nein |

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
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>"
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

## <a name="next-steps"></a>Nächste Schritte

* Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

* Weitere Informationen finden Sie unter [Copy data to Azure Data Explorer using Azure Data Factory (Kopieren von Daten aus Azure Data Factory in Azure Data Explorer)](/azure/data-explorer/data-factory-load-data).
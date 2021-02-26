---
title: Kopieren von Daten aus Dynamics AX
description: Erfahren Sie, wie Daten aus Dynamics AX mithilfe einer Kopieraktivität in eine Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/12/2020
ms.openlocfilehash: 38ff77ad56f16fbd33b77021b18be77f6a153b3f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380983"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory"></a>Kopieren von Daten aus Dynamics AX mithilfe von Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus einer Dynamics AX-Quelle zu kopieren. Dieser Artikel baut auf dem Artikel zur [Kopieraktivität in Azure Data Factory](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Dynamics AX-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus Dynamics AX in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die die Kopieraktivität als Quellen und Senken unterstützt, finden Sie unter [Unterstützte Datenspeicher und Formate](copy-activity-overview.md#supported-data-stores-and-formats).

Insbesondere unterstützt dieser Dynamics AX-Connector das Kopieren von Daten aus Dynamics AX unter Verwendung des **OData-Protokolls** mit **Dienstprinzipalauthentifizierung**.

>[!TIP]
>Sie können diesen Connector auch zum Kopieren von Daten aus **Dynamics 365 for Finance and Operations** verwenden. Einzelheiten hierzu finden Sie in den Informationen zur [OData-Unterstützung](/dynamics365/unified-operations/dev-itpro/data-entities/odata) und [Authentifizierungsmethode](/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication) für Dynamics 365.

## <a name="get-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In den folgenden Abschnitten finden Sie ausführliche Informationen zu Eigenschaften, mit denen Sie Data Factory-Entitäten definieren können, die spezifisch für den Dynamics AX-Connector sind.

## <a name="prerequisites"></a>Voraussetzungen

Zum Verwenden der Dienstprinzipalauthentifizierung führen Sie die folgenden Schritte aus:

1. Registrieren Sie eine Anwendungsentität in Azure Active Directory (Azure AD), indem Sie die Anleitungen unter [Registrieren Ihrer Anwendung bei einem Azure AD-Mandanten](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant) befolgen. Notieren Sie sich die folgenden Werte, die Sie zum Definieren des verknüpften Diensts verwenden:

    - Anwendungs-ID
    - Anwendungsschlüssel
    - Mandanten-ID

2. Wechseln Sie zu Dynamics AX, und gewähren Sie diesem Dienstprinzipal geeignete Berechtigungen zum Zugriff auf Dynamics AX.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit Dynamics AX verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die Eigenschaft **type** muss auf **DynamicsAX** festgelegt werden. |Ja |
| url | Der OData-Endpunkt für die Dynamics AX-Instanz (oder Dynamics 365 Finance and Operations). |Ja |
| servicePrincipalId | Geben Sie die Client-ID der Anwendung an. | Ja |
| servicePrincipalKey | Geben Sie den Schlüssel der Anwendung an. Markieren Sie dieses Feld als **SecureString**, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja |
| tenant | Geben Sie die Mandanteninformationen (Domänenname oder Mandanten-ID) für Ihre Anwendung an. Diese können Sie abrufen, indem Sie den Mauszeiger über den rechten oberen Bereich im Azure-Portal bewegen. | Ja |
| aadResourceId | Geben Sie die AAD-Ressource an, für die Sie eine Autorisierung anfordern. Wenn Ihre Dynamics URL z. B. `https://sampledynamics.sandbox.operations.dynamics.com/data/` lautet, ist die entsprechende AAD-Ressource normalerweise `https://sampledynamics.sandbox.operations.dynamics.com`. | Ja |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Sie können die Azure Integration Runtime oder eine selbstgehostete Integration Runtime auswählen (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

**Beispiel**

```json
{
    "name": "DynamicsAXLinkedService",
    "properties": {
        "type": "DynamicsAX",
        "typeProperties": {
            "url": "<Dynamics AX instance OData endpoint>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource, e.g. https://sampledynamics.sandbox.operations.dynamics.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}

```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Dieser Abschnitt enthält eine Liste der Eigenschaften, die das Dynamics AX-Dataset unterstützt.

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie unter [Datasets und verknüpfte Dienste](concepts-datasets-linked-services.md). 

Legen Sie zum Kopieren von Daten aus Dynamics AX die Eigenschaft **type** des Datasets auf **DynamicsAXResource** fest. Folgende Eigenschaften werden unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die Eigenschaft **type** des Datasets muss auf **DynamicsAXResource** festgelegt werden. | Ja |
| path | Der Pfad zur Dynamics AX-OData-Entität. | Ja |

**Beispiel**

```json
{
    "name": "DynamicsAXResourceDataset",
    "properties": {
        "type": "DynamicsAXResource",
        "typeProperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Dynamics AX linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Dynamics AX-Quelle unterstützt werden.

Eine vollständige Liste mit den verfügbaren Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie unter [Pipelines](concepts-pipelines-activities.md). 

### <a name="dynamics-ax-as-source"></a>Dynamics AX als Quelle

Legen Sie zum Kopieren von Daten aus Dynamics AX den Typ für **source** in der Kopieraktivität auf **DynamicsAXSource** fest. Die folgenden Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die Eigenschaft **type** der Quelle für die Kopieraktivität muss auf **DynamicsAXSource** festgelegt werden. | Ja |
| Abfrage | OData-Abfrageoptionen zum Filtern von Daten. Beispiel: `"?$select=Name,Description&$top=5"`.<br/><br/>**Hinweis**: Der Connector kopiert Daten aus der kombinierten URL: `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`. Weitere Informationen finden Sie unter [Komponenten der OData-URL](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nein |
| httpRequestTimeout | Das Timeout (der Wert **TimeSpan**) für die HTTP-Anforderung, um eine Antwort zu empfangen. Bei diesem Wert handelt es sich um das Timeout zum Empfangen einer Antwort, nicht um das Timeout zum Lesen von Antwortdaten. Wenn Sie hier nichts angeben, lautet der Standardwert **00:30:00** (30 Minuten). | Nein |

**Beispiel**

```json
"activities":[
    {
        "name": "CopyFromDynamicsAX",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics AX input dataset name>",
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
                "type": "DynamicsAXSource",
                "query": "$top=10"
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

Eine Liste der Datenspeicher, die von der Kopieraktivität als Quellen und Senken in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher und Formate](copy-activity-overview.md#supported-data-stores-and-formats).
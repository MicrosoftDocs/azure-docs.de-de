---
title: Kopieren von Daten aus OData-Quellen mithilfe von Azure Data Factory
description: Erfahren Sie, wie Daten aus OData-Quellen mithilfe einer Kopieraktivität in eine Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/14/2020
ms.author: jingwang
ms.openlocfilehash: 90cc4e3f9915db424cec89cfc764771b5be785e9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389721"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>Kopieren von Daten aus einer OData-Quelle mithilfe von Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-odata-connector.md)
> * [Aktuelle Version](connector-odata.md)

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten von einer OData-Quelle zu kopieren. Dieser Artikel baut auf dem Artikel zur [Kopieraktivität in Azure Data Factory](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Der OData-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus einer OData-Quelle in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die die Kopieraktivität als Quellen und Senken unterstützt, finden Sie unter [Unterstützte Datenspeicher und Formate](copy-activity-overview.md#supported-data-stores-and-formats).

Der OData-Connector unterstützt insbesondere Folgendes:

- OData Version 3.0 und 4.0.
- Kopieren von Daten mithilfe eines der folgenden Authentifizierungstypen: **Anonym**, **Standard**, **Windows** und **AAD-Dienstprinzipal**.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In den folgenden Abschnitten finden Sie Details zu Eigenschaften, mit denen Sie Data Factory-Entitäten definieren können, die spezifisch für einen OData-Connector sind.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für einen mit OData verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft muss auf **OData** festgelegt werden. |Ja |
| url | Die Stamm-URL des OData-Diensts. |Ja |
| authenticationType | Der Typ der Authentifizierung für die Verbindung mit der OData-Quelle. Zulässige Werte: **Anonymous**, **Basic**, **Windows** und **AadServicePrincipal**. OAuth auf Benutzerbasis wird nicht unterstützt. Außerdem können Sie Authentifizierungsheader in der `authHeader`-Eigenschaft konfigurieren.| Ja |
| authHeaders | Zusätzliche HTTP-Anforderungsheader für die Authentifizierung.<br/> Wenn Sie beispielsweise die Authentifizierung mit einem API-Schlüssel verwenden möchten, können Sie als Authentifizierungstyp „Anonym“ auswählen und im Header den API-Schlüssel angeben. | Nein |
| userName | Geben Sie **userName** an, wenn Sie die Standard- oder die Windows-Authentifizierung verwenden. | Nein |
| password | Geben Sie das **password** für das Benutzerkonto an, das Sie für **userName** angegeben haben. Markieren Sie dieses Feld als Typ **SecureString**, um es sicher in Data Factory zu speichern. Sie können auch [auf ein Geheimnis verweisen, das in Azure Key Vault](store-credentials-in-key-vault.md) gespeichert ist. | Nein |
| servicePrincipalId | Geben Sie die Client-ID der Azure Active Directory-Anwendung an. | Nein |
| aadServicePrincipalCredentialType | Geben Sie die Art der Anmeldeinformationen für die Dienstprinzipalauthentifizierung an. Zulässiger Wert: `ServicePrincipalKey` oder `ServicePrincipalCert`. | Nein |
| servicePrincipalKey | Geben Sie den Schlüssel der Azure Active Directory-Anwendung an. Markieren Sie dieses Feld als **SecureString**, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Nein |
| servicePrincipalEmbeddedCert | Geben Sie das in Azure Active Directory registrierte, base64-codierte Zertifikat Ihrer Anwendung an. Markieren Sie dieses Feld als **SecureString**, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Nein |
| servicePrincipalEmbeddedCertPassword | Geben Sie das Kennwort Ihres Zertifikats an, falls Ihr Zertifikat mit einem Kennwort geschützt ist. Markieren Sie dieses Feld als **SecureString**, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md).  | Nein|
| tenant | Geben Sie die Mandanteninformationen (Domänenname oder Mandanten-ID) für Ihre Anwendung an. Diese können Sie abrufen, indem Sie den Mauszeiger über den rechten oberen Bereich im Azure-Portal bewegen. | Nein |
| aadResourceId | Geben Sie die AAD-Ressource an, für die Sie eine Autorisierung anfordern.| Nein |
| azureCloudType | Geben Sie für die Dienstprinzipalauthentifizierung die Art der Azure-Cloudumgebung an, bei der Ihre AAD-Anwendung registriert ist. <br/> Zulässige Werte sind **AzurePublic**, **AzureChina**, **AzureUsGovernment** und **AzureGermany**. Standardmäßig wird die Cloudumgebung der Data Factory verwendet. | Nein |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Weitere Informationen finden Sie im Abschnitt [Voraussetzungen](#prerequisites). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

**Beispiel 1: Verwenden der anonymen Authentifizierung**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Beispiel 2: Verwenden der Standardauthentifizierung**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Basic",
            "userName": "<user name>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Beispiel 3: Verwenden der Windows-Authentifizierung**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Windows",
            "userName": "<domain>\\<user>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Beispiel 4: Verwenden der Dienstprinzipal-Schlüsselauthentifizierung**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**Beispiel 5: Verwenden der Dienstprinzipal-Zertifikatauthentifizierung**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalEmbeddedCert": { 
                "type": "SecureString", 
                "value": "<base64 encoded string of (.pfx) certificate data>"
            },
            "servicePrincipalEmbeddedCertPassword": { 
                "type": "SecureString", 
                "value": "<password of your certificate>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource e.g. https://tenant.sharepoint.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**Beispiel 6: Verwenden der Authentifizierung mit API-Schlüssel**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Anonymous",
            "authHeader": {
                "APIKey": {
                    "type": "SecureString",
                    "value": "<API key>"
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

Dieser Abschnitt enthält eine Liste der Eigenschaften, die das OData-Dataset unterstützt.

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie unter [Datasets und verknüpfte Dienste](concepts-datasets-linked-services.md). 

Legen Sie zum Kopieren von Daten aus OData die **type**-Eigenschaft des Datasets auf **ODataResource** fest. Folgende Eigenschaften werden unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft des Datasets muss auf **ODataResource** festgelegt werden. | Ja |
| path | Der Pfad zur OData-Ressource. | Ja |

**Beispiel**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<OData linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "path": "Products"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Dieser Abschnitt enthält eine Liste der Eigenschaften, die die OData-Quelle unterstützt.

Eine vollständige Liste mit den verfügbaren Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie unter [Pipelines](concepts-pipelines-activities.md). 

### <a name="odata-as-source"></a>OData als Quelle

Beim Kopieren von Daten aus OData werden die folgenden Eigenschaften im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft der Quelle der Kopieraktivität muss auf **ODataSource** festgelegt werden. | Ja |
| Abfrage | OData-Abfrageoptionen zum Filtern von Daten. Beispiel: `"$select=Name,Description&$top=5"`.<br/><br/>**Hinweis**: Der OData-Connector kopiert Daten aus der kombinierten URL: `[URL specified in linked service]/[path specified in dataset]?[query specified in copy activity source]`. Weitere Informationen finden Sie unter [Komponenten der OData-URL](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nein |
| httpRequestTimeout | Das Timeout (der Wert **TimeSpan**) für die HTTP-Anforderung, um eine Antwort zu empfangen. Bei diesem Wert handelt es sich um das Timeout zum Empfangen einer Antwort, nicht um das Timeout zum Lesen von Antwortdaten. Wenn Sie hier nichts angeben, lautet der Standardwert **00:30:00** (30 Minuten). | Nein |

**Beispiel**

```json
"activities":[
    {
        "name": "CopyFromOData",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<OData input dataset name>",
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
                "type": "ODataSource",
                "query": "$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Wenn Sie eine Quelle vom Typ `RelationalSource` verwenden, wird sie weiterhin unverändert unterstützt. Es wird jedoch empfohlen, zukünftig die neue Version zu verwenden.

## <a name="data-type-mapping-for-odata"></a>Datentypzuordnung für OData

Beim Kopieren von Daten aus OData werden die folgenden Zuordnungen zwischen OData-Datentypen und Azure Data Factory-Zwischendatentypen verwendet. Unter [Schema- und Datentypzuordnungen](copy-activity-schema-and-type-mapping.md) erfahren Sie, wie die Kopieraktivität das Quellschema und den Datentyp der Senke zuordnet.

| OData-Datentyp | Data Factory-Zwischendatentyp |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | Datetime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> Komplexe OData-Datentypen (z.B. **Object**) werden nicht unterstützt.


## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nächste Schritte

Eine Liste der Datenspeicher, die von der Kopieraktivität als Quellen und Senken in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher und Formate](copy-activity-overview.md#supported-data-stores-and-formats).
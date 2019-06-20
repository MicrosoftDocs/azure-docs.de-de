---
title: Kopieren von Daten aus einer REST-Quelle mithilfe von Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, wie Daten aus einer Cloud- oder lokalen REST-Quelle mithilfe einer Kopieraktivität in eine Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: jingwang
ms.openlocfilehash: ee47f464c59bd9deed98671f19cfcc6d2c3c1b39
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60546640"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Kopieren von Daten von einem REST-Endpunkt mithilfe von Azure Data Factory

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten von einem REST-Endpunkt zu kopieren. Dieser Artikel baut auf dem Artikel zur [Kopieraktivität in Azure Data Factory](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

Die Unterschiede zwischen diesem REST-Connector, dem [HTTP-Connector](connector-http.md) und dem [Webtabellenconnector](connector-web-table.md) sind die folgenden:

- **REST-Connector:** Dieser unterstützt insbesondere das Kopieren von Daten aus RESTful-APIs. 
- **HTTP-Connector:** Dieser dient allgemein dazu, Daten von jedem HTTP-Endpunkt abzurufen, z. B. um Dateien herunterzuladen. Solange der REST-Connector noch nicht verfügbar ist, verwenden Sie möglicherweise den HTTP-Connector, um Daten aus RESTful-APIs zu kopieren. Dieser wird unterstützt, verfügt jedoch über weniger Funktionen als der REST-Connector.
- **Webtabellenconnector:** Dieser extrahiert Tabelleninhalte aus einer HTML-Webseite.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Sie können Daten aus einer REST-Quelle in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die die Kopieraktivität als Quellen und Senken unterstützt, finden Sie unter [Unterstützte Datenspeicher und Formate](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser allgemeine REST-Connector unterstützt Folgendes:

- Das Abrufen von Daten eines REST-Endpunkt mithilfe der Methoden **GET** und **POST**.
- Das Abrufen von Daten mithilfe eines der folgenden Authentifizierungstypen: **Anonym**, **Standard**, **AAD-Dienstprinzipal** und **verwaltete Identitäten für Azure-Ressourcen**.
- Die **[Paginierung](#pagination-support)** in den REST-APIs.
- Das Kopieren der [unveränderten](#export-json-response-as-is) REST-JSON-Antwort oder die Analyse mithilfe der [Schemazuordnung](copy-activity-schema-and-type-mapping.md#schema-mapping). In **JSON** wird lediglich die Antwortnutzlast unterstützt.

> [!TIP]
> Um eine Anforderung für den Datenabruf zu testen, bevor Sie den REST-Connector in Data Factory konfigurieren, informieren Sie sich über die API-Spezifikation für Header- und Textanforderungen. Sie können Tools wie Postman oder einen Webbrowser für die Überprüfung verwenden.

## <a name="get-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In den folgenden Abschnitten finden Sie Details zu Eigenschaften, mit denen Sie Data Factory-Entitäten definieren können, die für den REST-Connector spezifisch sind.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit REST verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft muss auf **RestService** festgelegt werden. | Ja |
| url | Die Basis-URL des REST-Diensts. | Ja |
| enableServerCertificateValidation | Hiermit wird festgelegt, ob das serverseitige SSL-Zertifikat beim Herstellen einer Verbindung mit dem Endpunkt überprüft werden soll. | Nein<br /> (der Standardwert ist **TRUE**) |
| authenticationType | Typ der Authentifizierung für die Verbindung mit dem REST-Dienst. Zulässige Werte: **Anonymous**, **Basic**, **AadServicePrincipal** und **ManagedServiceIdentity**. Weitere Informationen zu anderen Eigenschaften und Beispiele finden Sie weiter unten in den jeweiligen Abschnitten. | Ja |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Sie können die Azure Integration Runtime oder eine selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn keine Option angegeben ist, verwendet diese Eigenschaft die standardmäßige Azure Integration Runtime. |Nein |

### <a name="use-basic-authentication"></a>Verwenden der Standardauthentifizierung

Legen Sie die **authenticationType**-Eigenschaft auf **Basic** fest. Geben Sie zusätzlich zu den im vorherigen Abschnitt beschriebenen generischen Eigenschaften die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| userName | Der Benutzername, der für den Zugriff auf den REST-Endpunkt verwendet werden soll. | Ja |
| password | Das Kennwort für den Benutzer (der Wert **userName**). Markieren Sie dieses Feld als Typ **SecureString**, um es sicher in Data Factory zu speichern. Sie können auch [auf ein Geheimnis verweisen, das in Azure Key Vault](store-credentials-in-key-vault.md) gespeichert ist. | Ja |

**Beispiel**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<REST endpoint>",
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

### <a name="use-aad-service-principal-authentication"></a>Verwenden der Azure AD-Dienstprinzipalauthentifizierung

Legen Sie die **authenticationType**-Eigenschaft auf **AadServicePrincipal** fest. Geben Sie zusätzlich zu den im vorherigen Abschnitt beschriebenen generischen Eigenschaften die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| servicePrincipalId | Geben Sie die Client-ID der Azure Active Directory-Anwendung an. | Ja |
| servicePrincipalKey | Geben Sie den Schlüssel der Azure Active Directory-Anwendung an. Markieren Sie dieses Feld als **SecureString**, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja |
| tenant | Geben Sie die Mandanteninformationen (Domänenname oder Mandanten-ID) für Ihre Anwendung an. Diese können Sie abrufen, indem Sie den Mauszeiger über den rechten oberen Bereich im Azure-Portal bewegen. | Ja |
| aadResourceId | Geben Sie die Azure AD-Ressource an, für die Sie eine Autorisierung anfordern, z. B. `https://management.core.windows.net`.| Ja |

**Beispiel**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> So verwenden Sie verwaltete Identitäten für die Azure-Ressourcenauthentifizierung

Legen Sie die **authenticationType**-Eigenschaft auf **ManagedServiceIdentity** fest. Geben Sie zusätzlich zu den im vorherigen Abschnitt beschriebenen generischen Eigenschaften die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| aadResourceId | Geben Sie die Azure AD-Ressource an, für die Sie eine Autorisierung anfordern, z. B. `https://management.core.windows.net`.| Ja |

**Beispiel**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Dieser Abschnitt enthält eine Liste der Eigenschaften, die das REST-Dataset unterstützt. 

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie unter [Datasets und verknüpfte Dienste](concepts-datasets-linked-services.md). 

Zum Kopieren von Daten aus REST werden die folgenden Eigenschaften unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft des Datasets muss auf **RestResource** festgelegt sein. | Ja |
| relativeUrl | Eine relative URL zu der Ressource, die die Daten enthält. Wenn die Eigenschaft nicht angegeben ist, wird nur die URL verwendet, die in der Definition des verknüpften Diensts angegeben ist. | Nein |
| requestMethod | Die HTTP-Methode. Zulässige Werte sind **Get** (Standardwert) und **Post**. | Nein |
| additionalHeaders | Zusätzliche HTTP-Anforderungsheader | Nein |
| requestBody | Der Text der HTTP-Anforderung. | Nein |
| paginationRules | Die Paginierungsregeln zum Zusammenstellen der nächsten Seitenanforderungen. Ausführliche Informationen finden Sie im Abschnitt [Unterstützung der Paginierung](#pagination-support). | Nein |

**Beispiel 1: Verwenden der GET-Methode mit der Paginierung**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": {
                "x-user-defined": "helloworld"
            },
            "paginationRules": {
                "AbsoluteUrl": "$.paging.next"
            }
        }
    }
}
```

**Beispiel 2: Verwenden der POST-Methode**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST REST request>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Dieser Abschnitt enthält eine Liste der Eigenschaften, die die REST-Quelle unterstützt.

Eine vollständige Liste mit den verfügbaren Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie unter [Pipelines](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>REST als Quelle

Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft der Quelle der Kopieraktivität muss auf **RestSource** festgelegt werden. | Ja |
| httpRequestTimeout | Das Timeout (der Wert **TimeSpan**) für die HTTP-Anforderung, um eine Antwort zu empfangen. Bei diesem Wert handelt es sich um das Timeout zum Empfangen einer Antwort, nicht um das Timeout zum Lesen von Antwortdaten. Der Standardwert ist **00:01:40**.  | Nein |
| requestInterval | Die Wartezeit vor dem Senden der Anforderung für die nächste Seite. Der Standardwert lautet **00:00:01** |  Nein |

**Beispiel**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="pagination-support"></a>Unterstützung der Paginierung

In der Regel beschränken REST-APIs ihre Antwortnutzlastgröße auf einen angemessenen Wert. Beim Zurückgegeben großer Datenmengen werden die Ergebnisse auf mehrere Seiten aufgeteilt, und Aufrufer werden zum Senden aufeinanderfolgender Anforderungen aufgefordert, um die nächste Seite der Ergebnisse abzurufen. In der Regel ist die Anforderung für eine Seite dynamisch und besteht aus den Informationen, die von der Antwort der vorherigen Seite zurückgegeben werden.

Dieser generische REST-Connector unterstützt die folgenden Paginierungsmuster: 

* Absolute oder relative URL der nächsten Anforderung = Eigenschaftswert im aktuellen Antworttext
* Absolute oder relative URL der nächsten Anforderung = Headerwert in aktuellen Antwortheadern
* Abfrageparameter der nächsten Anforderung = Eigenschaftswert im aktuellen Antworttext
* Abfrageparameter der nächsten Anforderung = Headerwert in aktuellen Antwortheadern
* Header der nächsten Anforderung = Eigenschaftswert im aktuellen Antworttext
* Header der nächsten Anforderung = Headerwert in aktuellen Antwortheadern

**Paginierungsregeln** werden als Wörterbuch in Datasets definiert, die mindestens ein Schlüssel-Wert-Paar enthalten, bei dem die Groß-/Kleinschreibung berücksichtigt wird. Die Konfiguration wird ab der zweiten Seite zum Generieren der Anforderung verwendet. Der Connector beendet die Iteration, wenn er den HTTP-Statuscode 204 (Kein Inhalt) empfängt oder der JSONPath-Ausdruck in „paginationRules“ NULL zurückgibt.

In Paginierungsregeln **unterstützte Schlüssel**:

| Schlüssel | BESCHREIBUNG |
|:--- |:--- |
| AbsoluteUrl | Gibt die URL für die nächste Anforderung an. Sie kann **eine absolute oder eine relative URL sein**. |
| QueryParameters.*request_query_parameter* OR QueryParameters['request_query_parameter'] | „request_query_parameter“ wird vom Benutzer definiert und verweist auf einen Abfrageparameternamen in der nächsten HTTP-Anforderungs-URL. |
| Headers.*request_header* OR Headers['request_header'] | „request_header“ wird vom Benutzer definiert und verweist auf einen Headernamen in der nächsten HTTP-Anforderung. |

In Paginierungsregeln **unterstützte Werte**:

| Wert | BESCHREIBUNG |
|:--- |:--- |
| Headers.*response_header* ODER Headers['response_header'] | „response_header“ wird vom Benutzer definiert und verweist auf einen Headernamen in der aktuellen HTTP-Antwort, den Wert, der zum Ausgeben der nächsten Anforderung verwendet wird. |
| Ein mit „$“ beginnender JSONPath-Ausdruck (stellt den Stamm des Antworttexts dar) | Der Antworttext sollte nur ein JSON-Objekt enthalten. Der JSONPath-Ausdruck sollte einen einzelnen primitiven Wert zurückgeben, der zum Ausgeben der nächsten Anforderung verwendet wird. |

**Beispiel:**

In der folgenden Struktur gibt die Facebook-Graph-API eine Antwort zurück. In diesem Fall wird die URL der nächsten Seite in ***paging.next*** dargestellt:

```json
{
    "data": [
        {
            "created_time": "2017-12-12T14:12:20+0000",
            "name": "album1",
            "id": "1809938745705498_1809939942372045"
        },
        {
            "created_time": "2017-12-12T14:14:03+0000",
            "name": "album2",
            "id": "1809938745705498_1809941802371859"
        },
        {
            "created_time": "2017-12-12T14:14:11+0000",
            "name": "album3",
            "id": "1809938745705498_1809941879038518"
        }
    ],
    "paging": {
        "cursors": {
            "after": "MTAxNTExOTQ1MjAwNzI5NDE=",
            "before": "NDMyNzQyODI3OTQw"
        },
        "previous": "https://graph.facebook.com/me/albums?limit=25&before=NDMyNzQyODI3OTQw",
        "next": "https://graph.facebook.com/me/albums?limit=25&after=MTAxNTExOTQ1MjAwNzI5NDE="
    }
}
```

Die entsprechende Konfiguration des REST-Datasets (insbesondere `paginationRules`) entspricht der folgenden:

```json
{
    "name": "MyFacebookAlbums",
    "properties": {
            "type": "RestResource",
            "typeProperties": {
                "relativeUrl": "albums",
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                }
            },
            "linkedServiceName": {
                "referenceName": "MyRestService",
                "type": "LinkedServiceReference"
            }
    }
}
```

## <a name="export-json-response-as-is"></a>Exportieren der unveränderten JSON-Antwort

Sie können diesen REST-Connector verwenden, um die unveränderte JSON-Antwort der REST-API in verschiedene dateibasierte Speicher zu exportieren. Um eine solche vom Schema unabhängige Kopie zu erzielen, überspringen Sie den Abschnitt „structure“ (auch *schema* genannt) im Dataset und die Schemazuordnung in der Kopieraktivität.

## <a name="schema-mapping"></a>Schemazuordnung

Informationen zum Kopieren von Daten aus dem REST-Endpunkt in eine tabellarische Senke finden Sie unter [Schemazuordnung](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Nächste Schritte

Eine Liste der Datenspeicher, die von der Kopieraktivität als Quellen und Senken in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher und Formate](copy-activity-overview.md#supported-data-stores-and-formats).

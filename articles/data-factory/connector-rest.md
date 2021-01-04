---
title: Kopieren von Daten von und zu einem REST-Endpunkt mithilfe von Azure Data Factory
description: Erfahren Sie, wie Sie eine Kopieraktivität in einer Azure Data Factory-Pipeline verwenden, um Daten aus einer Cloud- oder lokalen REST-Quelle in unterstützte Senkendatenspeicher oder aus unterstützten Quelldatenspeichern in eine REST-Senke zu kopieren.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: jingwang
ms.openlocfilehash: a8cd6386ed6004935b0a1e45a53c01668166c0e4
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96902254"
---
# <a name="copy-data-from-and-to-a-rest-endpoint-by-using-azure-data-factory"></a>Kopieren von Daten von und zu einem REST-Endpunkt mithilfe von Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten von und zu einem REST-Endpunkt zu kopieren. Dieser Artikel baut auf dem Artikel zur [Kopieraktivität in Azure Data Factory](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

Dies sind die Unterschiede zwischen diesem REST-Connector, dem [HTTP-Connector](connector-http.md) und dem [Webtabellenconnector](connector-web-table.md):

- **REST-Connector:** Dieser unterstützt insbesondere das Kopieren von Daten aus RESTful-APIs. 
- **HTTP-Connector:** Dieser dient allgemein dazu, Daten von jedem HTTP-Endpunkt abzurufen, z. B. um Dateien herunterzuladen. Solange der REST-Connector noch nicht verfügbar ist, verwenden Sie möglicherweise den HTTP-Connector, um Daten aus RESTful-APIs zu kopieren. Dieser wird unterstützt, verfügt jedoch über weniger Funktionen als der REST-Connector.
- **Webtabellenconnector:** Dieser extrahiert Tabelleninhalte aus einer HTML-Webseite.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Sie können Daten aus einer REST-Quelle in beliebige unterstützte Senkendatenspeicher kopieren. Sie können Daten auch aus einem beliebigen unterstützten Quelldatenspeicher in eine REST-Senke kopieren. Eine Liste der Datenspeicher, die die Kopieraktivität als Quellen und Senken unterstützt, finden Sie unter [Unterstützte Datenspeicher und Formate](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser allgemeine REST-Connector unterstützt Folgendes:

- Kopieren von Daten von einem REST-Endpunkt mithilfe der Methoden **GET** oder **POST** und Kopieren von Daten in einen REST-Endpunkt mithilfe der Methoden **POST**, **PUT** oder **PATCH**.
- Kopieren von Daten mithilfe eines der folgenden Authentifizierungstypen: **Anonym**, **Standard**, **AAD-Dienstprinzipal** und **verwaltete Identitäten für Azure-Ressourcen**.
- Die **[Paginierung](#pagination-support)** in den REST-APIs.
- Wenn REST die Quelle ist: Kopieren der [unveränderten](#export-json-response-as-is) REST-JSON-Antwort oder Analysieren der Antwort mithilfe der [Schemazuordnung](copy-activity-schema-and-type-mapping.md#schema-mapping). In **JSON** wird lediglich die Antwortnutzlast unterstützt.

> [!TIP]
> Um eine Anforderung für den Datenabruf zu testen, bevor Sie den REST-Connector in Data Factory konfigurieren, informieren Sie sich über die API-Spezifikation für Header- und Textanforderungen. Sie können Tools wie Postman oder einen Webbrowser für die Überprüfung verwenden.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In den folgenden Abschnitten finden Sie Details zu Eigenschaften, mit denen Sie Data Factory-Entitäten definieren können, die für den REST-Connector spezifisch sind.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit REST verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft muss auf **RestService** festgelegt werden. | Ja |
| url | Die Basis-URL des REST-Diensts. | Ja |
| enableServerCertificateValidation | Hiermit wird festgelegt, ob das serverseitige TLS-/SSL-Zertifikat beim Herstellen einer Verbindung mit dem Endpunkt überprüft werden soll. | Nein<br /> (der Standardwert ist **TRUE**) |
| authenticationType | Typ der Authentifizierung für die Verbindung mit dem REST-Dienst. Zulässige Werte: **Anonymous**, **Basic**, **AadServicePrincipal** und **ManagedServiceIdentity**. Weitere Informationen zu anderen Eigenschaften und Beispiele finden Sie weiter unten in den jeweiligen Abschnitten. | Ja |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Weitere Informationen finden Sie im Abschnitt [Voraussetzungen](#prerequisites). Wenn keine Option angegeben ist, verwendet diese Eigenschaft die standardmäßige Azure Integration Runtime. |Nein |

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
| aadResourceId | Geben Sie die AAD-Ressource an, für die Sie eine Autorisierung anfordern, z. B. `https://management.core.windows.net`.| Ja |
| azureCloudType | Geben Sie für die Dienstprinzipalauthentifizierung die Art der Azure-Cloudumgebung an, bei der Ihre AAD-Anwendung registriert ist. <br/> Zulässige Werte sind **AzurePublic**, **AzureChina**, **AzureUsGovernment** und **AzureGermany**. Standardmäßig wird die Cloudumgebung der Data Factory verwendet. | Nein |

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

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> So verwenden Sie verwaltete Identitäten für die Azure-Ressourcenauthentifizierung

Legen Sie die **authenticationType**-Eigenschaft auf **ManagedServiceIdentity** fest. Geben Sie zusätzlich zu den im vorherigen Abschnitt beschriebenen generischen Eigenschaften die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| aadResourceId | Geben Sie die AAD-Ressource an, für die Sie eine Autorisierung anfordern, z. B. `https://management.core.windows.net`.| Ja |

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
| relativeUrl | Eine relative URL zu der Ressource, die die Daten enthält. Wenn die Eigenschaft nicht angegeben ist, wird nur die URL verwendet, die in der Definition des verknüpften Diensts angegeben ist. Der HTTP-Connector kopiert Daten aus der kombinierten URL: `[URL specified in linked service]/[relative URL specified in dataset]`. | Nein |

Wenn Sie `requestMethod`, `additionalHeaders`, `requestBody` und `paginationRules` im Dataset festgelegt haben, wird es weiterhin unverändert unterstützt. Es wird jedoch empfohlen, zukünftig das neue Modell in der Aktivität zu verwenden.

**Beispiel:**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "typeProperties": {
            "relativeUrl": "<relative url>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der REST-Quelle und -Senke unterstützt werden.

Eine vollständige Liste mit den verfügbaren Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie unter [Pipelines](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>REST als Quelle

Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft der Quelle der Kopieraktivität muss auf **RestSource** festgelegt werden. | Ja |
| requestMethod | Die HTTP-Methode. Zulässige Werte sind **GET** (Standardwert) und **POST**. | Nein |
| additionalHeaders | Zusätzliche HTTP-Anforderungsheader | Nein |
| requestBody | Der Text der HTTP-Anforderung. | Nein |
| paginationRules | Die Paginierungsregeln zum Zusammenstellen der nächsten Seitenanforderungen. Ausführliche Informationen finden Sie im Abschnitt [Unterstützung der Paginierung](#pagination-support). | Nein |
| httpRequestTimeout | Das Timeout (der Wert **TimeSpan**) für die HTTP-Anforderung, um eine Antwort zu empfangen. Bei diesem Wert handelt es sich um das Timeout zum Empfangen einer Antwort, nicht um das Timeout zum Lesen von Antwortdaten. Der Standardwert ist **00:01:40**.  | Nein |
| requestInterval | Die Wartezeit vor dem Senden der Anforderung für die nächste Seite. Der Standardwert lautet **00:00:01** |  Nein |

>[!NOTE]
>Der REST-Connector ignoriert jeden in `additionalHeaders` angegebenen "Accept"-Header. Da der REST-Connector nur Antworten in JSON unterstützt, wird automatisch eine Kopfzeile vom Typ `Accept: application/json` generiert.

**Beispiel 1: Verwenden der GET-Methode mit der Paginierung**

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
                "additionalHeaders": {
                    "x-user-defined": "helloworld"
                },
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                },
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Beispiel 2: Verwenden der POST-Methode**

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
                "requestMethod": "Post",
                "requestBody": "<body for POST REST request>",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="rest-as-sink"></a>REST als Senke

Folgende Eigenschaften werden im Abschnitt **sink** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| Typ | Die **type**-Eigenschaft der Senke der Kopieraktivität muss auf **RestSink** festgelegt werden. | Ja |
| requestMethod | Die HTTP-Methode. Zulässige Werte sind **POST** (Standardwert), **PUT** und **PATCH**. | Nein |
| additionalHeaders | Zusätzliche HTTP-Anforderungsheader | Nein |
| httpRequestTimeout | Das Timeout (der Wert **TimeSpan**) für die HTTP-Anforderung, um eine Antwort zu empfangen. Bei diesem Wert handelt es sich um das Timeout für den Empfang einer Antwort, nicht um das Timeout für das Schreiben der Daten. Der Standardwert ist **00:01:40**.  | Nein |
| requestInterval | Das Zeitintervall zwischen verschiedenen Anforderungen in Millisekunden. Der Wert für das Anforderungsintervall sollte eine Zahl zwischen 10 und 60000 sein. |  Nein |
| httpCompressionType | Der HTTP-Komprimierungstyp, der zum Senden von Daten mit der optimalen Komprimierungsstufe verwendet werden soll. Zulässige Werte sind **none** und **gzip**. | Nein |
| writeBatchSize | Die Anzahl von Datensätzen, die pro Batch in die REST-Senke geschrieben werden sollen. Der Standardwert ist 10.000. | Nein |

>[!NOTE]
>Ein REST-Connector als Senke funktioniert mit den REST-Endpunkten, die JSON akzeptieren. Die Daten werden nur im JSON-Format gesendet.

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyToREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<REST output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "RestSink",
                "requestMethod": "POST",
                "httpRequestTimeout": "00:01:40",
                "requestInterval": 10,
                "writeBatchSize": 10000,
                "httpCompressionType": "none",
            },
        }
    }
]
```

## <a name="pagination-support"></a>Unterstützung der Paginierung

In der Regel beschränkt die REST-API ihre Größe der Antwortnutzlast für eine einzelne Anforderung auf einen angemessenen Wert. Beim Zurückgegeben großer Datenmengen werden die Ergebnisse auf mehrere Seiten aufgeteilt, und Aufrufer werden zum Senden aufeinanderfolgender Anforderungen aufgefordert, um die nächste Seite der Ergebnisse abzurufen. In der Regel ist die Anforderung für eine Seite dynamisch und besteht aus den Informationen, die von der Antwort der vorherigen Seite zurückgegeben werden.

Dieser generische REST-Connector unterstützt die folgenden Paginierungsmuster: 

* Absolute oder relative URL der nächsten Anforderung = Eigenschaftswert im aktuellen Antworttext
* Absolute oder relative URL der nächsten Anforderung = Headerwert in aktuellen Antwortheadern
* Abfrageparameter der nächsten Anforderung = Eigenschaftswert im aktuellen Antworttext
* Abfrageparameter der nächsten Anforderung = Headerwert in aktuellen Antwortheadern
* Header der nächsten Anforderung = Eigenschaftswert im aktuellen Antworttext
* Header der nächsten Anforderung = Headerwert in aktuellen Antwortheadern

**Paginierungsregeln** werden als Wörterbuch in einem Dataset definiert, das mindestens ein Schlüssel-Wert-Paar enthält, bei dem die Groß-/Kleinschreibung berücksichtigt wird. Die Konfiguration wird ab der zweiten Seite zum Generieren der Anforderung verwendet. Der Connector beendet die Iteration, wenn er den HTTP-Statuscode 204 (Kein Inhalt) empfängt oder einer der JSONPath-Ausdrücke in „paginationRules“ NULL zurückgibt.

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

Die Facebook-Graph-API gibt eine Antwort in der folgenden Struktur zurück. In diesem Fall wird die URL der nächsten Seite in **_paging.next_* _ dargestellt:

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

Die entsprechende Konfiguration der Quelle für die REST-Kopieraktivität (insbesondere `paginationRules`) entspricht der folgenden:

```json
"typeProperties": {
    "source": {
        "type": "RestSource",
        "paginationRules": {
            "AbsoluteUrl": "$.paging.next"
        },
        ...
    },
    "sink": {
        "type": "<sink type>"
    }
}
```

## <a name="use-oauth"></a>Verwenden von OAuth
In diesem Abschnitt wird beschrieben, wie Sie eine Lösungsvorlage verwenden, um Daten aus dem REST-Connector mithilfe von OAuth in Azure Data Lake Storage im JSON-Format zu kopieren. 

### <a name="about-the-solution-template"></a>Informationen zur Lösungsvorlage

Die Vorlage enthält zwei Aktivitäten:
- Eine Aktivität vom Typ _ *Web** ruft das Bearertoken ab und übergibt es als Autorisierung an die nachfolgende Kopieraktivität.
- **Kopieraktivität** kopiert Daten aus REST in Azure Data Lake Storage.

Die Vorlage definiert zwei Parameter:
- **SinkContainer** ist der Stammordnerpfad, in den die Daten in Ihrem Azure Data Lake Storage kopiert werden. 
- **SinkDirectory** ist der Verzeichnispfad unter dem Stamm, in den die Daten in Ihrem Azure Data Lake Storage kopiert werden. 

### <a name="how-to-use-this-solution-template"></a>So verwenden Sie diese Lösungsvorlage

1. Wechseln Sie zur Vorlage **Copy from REST or HTTP using OAuth** (Kopieren aus REST oder HTTP mithilfe von OAuth). Erstellen Sie eine neue Verbindung für „Quellverbindung“. 
    ![Erstellen neuer Verbindungen](media/solution-template-copy-from-rest-or-http-using-oauth/source-connection.png)

    Nachstehend sind die wichtigsten Schritte für Einstellungen für neue verknüpfte Dienste (REST) aufgeführt:
    
     1. Geben Sie unter **Basis-URL** den URL-Parameter für Ihren eigenen REST-Quelldienst an. 
     2. Wählen Sie als **Authentifizierungstyp** den Typ *Anonym* aus.
        ![Neue REST-Verbindung](media/solution-template-copy-from-rest-or-http-using-oauth/new-rest-connection.png)

2. Erstellen Sie eine neue Verbindung für „Zielverbindung“.  
    ![Neue Gen2-Verbindung](media/solution-template-copy-from-rest-or-http-using-oauth/destination-connection.png)

3. Klicken Sie auf **Diese Vorlage verwenden**.
    ![Diese Vorlage verwenden](media/solution-template-copy-from-rest-or-http-using-oauth/use-this-template.png)

4. Sie sehen, dass die Pipeline erstellt wurde, wie im nachstehenden Beispiel gezeigt wird:  ![Der Screenshot zeigt die aus der Vorlage erstellte Pipeline.](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline.png)

5. Wählen Sie die Aktivität **Web** aus. Geben Sie in **Einstellungen** die entsprechenden Werte für **URL**, **Methode**, **Header** und **Body** an, um das OAuth-Bearertoken aus der Anmelde-API des Diensts abzurufen, aus dem Sie Daten kopieren möchten. Der Platzhalter in der Vorlage zeigt ein Beispiel für Azure Active Directory (AAD) OAuth. Beachten Sie, dass die AAD-Authentifizierung vom REST-Connector systemintern unterstützt wird. Dies hier ist nur ein Beispiel für den OAuth-Fluss. 

    | Eigenschaft | BESCHREIBUNG |
    |:--- |:--- |:--- |
    | URL |Geben Sie die URL an, aus der das OAuth-Bearertoken abgerufen werden soll. Im Beispiel hier ist dies https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token. |. 
    | Methode | Die HTTP-Methode. Zulässige Werte sind **Post** und **Get**. | 
    | Header | Der Header wird vom Benutzer definiert und verweist auf einen einzigen Headernamen in der HTTP-Anforderung. | 
    | Body | Der Text der HTTP-Anforderung. | 

    ![Pipeline](media/solution-template-copy-from-rest-or-http-using-oauth/web-settings.png)

6. Wählen Sie in der Aktivität **Daten kopieren** die Registerkarte *Quelle* aus. Dann können Sie sehen, dass das aus dem vorherigen Schritt abgerufene Bearertoken („access_token“) unter „Zusätzliche Header“ als **Autorisierung** an die Aktivität übergeben wird. Bestätigen Sie die Einstellungen für die folgenden Eigenschaften, bevor Sie eine Pipelineausführung starten.

    | Eigenschaft | BESCHREIBUNG |
    |:--- |:--- |:--- | 
    | Anforderungsmethode | Die HTTP-Methode. Zulässige Werte sind **Get** (Standardwert) und **Post**. | 
    | Zusätzliche Header | Zusätzliche HTTP-Anforderungsheader| 

   ![Kopieren der Quellauthentifizierung](media/solution-template-copy-from-rest-or-http-using-oauth/copy-data-settings.png)

7. Klicken Sie auf **Debuggen**, geben Sie die **Parameter** ein, und klicken Sie dann auf **Fertig stellen**.
   ![Pipelineausführung](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline-run.png) 

8. Wenn die Pipelineausführung erfolgreich abgeschlossen wurde, wird das Ergebnis ähnlich wie im nachstehenden Beispiel angezeigt: ![Ergebnis der Pipelineausführung](media/solution-template-copy-from-rest-or-http-using-oauth/run-result.png) 

9. Klicken Sie in der Spalte **Aktionen** auf das Symbol „Ausgabe“ von „WebActivity“. Dann sehen Sie das vom Dienst zurückgegebene „access_token“.

   ![Tokenausgabe](media/solution-template-copy-from-rest-or-http-using-oauth/token-output.png) 

10. Klicken Sie in der Spalte **Aktionen** auf das Symbol „Eingabe“ von „CopyActivity“. Dann sehen Sie, dass das von „WebActivity“ abgerufene „access_token“ an „CopyActivity“ zur Authentifizierung übergeben wird. 

    ![Tokeneingabe](media/solution-template-copy-from-rest-or-http-using-oauth/token-input.png)
        
    >[!CAUTION] 
    >Wenn Sie vermeiden möchten, dass das Token im Nur-Text-Format protokolliert wird, aktivieren Sie in der Webaktivität „Sichere Ausgabe“ und in der Kopieraktivität „Sichere Eingabe“.


## <a name="export-json-response-as-is"></a>Exportieren der unveränderten JSON-Antwort

Sie können diesen REST-Connector verwenden, um die unveränderte JSON-Antwort der REST-API in verschiedene dateibasierte Speicher zu exportieren. Um eine solche vom Schema unabhängige Kopie zu erzielen, überspringen Sie den Abschnitt „structure“ (auch *schema* genannt) im Dataset und die Schemazuordnung in der Kopieraktivität.

## <a name="schema-mapping"></a>Schemazuordnung

Informationen zum Kopieren von Daten aus dem REST-Endpunkt in eine tabellarische Senke finden Sie unter [Schemazuordnung](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Nächste Schritte

Eine Liste der Datenspeicher, die von der Kopieraktivität als Quellen und Senken in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher und Formate](copy-activity-overview.md#supported-data-stores-and-formats).

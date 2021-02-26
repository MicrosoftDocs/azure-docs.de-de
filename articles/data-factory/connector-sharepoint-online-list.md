---
title: Kopieren von Daten aus einer SharePoint Online-Liste mit Azure Data Factory
description: Erfahren Sie, wie Sie Daten aus einer SharePoint Online-Liste mithilfe einer Kopieraktivität in einer Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopieren.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: jingwang
ms.openlocfilehash: 440dd561beddc9696ec703142fe82655b69fbb48
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99474946"
---
# <a name="copy-data-from-sharepoint-online-list-by-using-azure-data-factory"></a>Kopieren von Daten aus einer SharePoint Online-Liste mit Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus einer SharePoint-Online-Liste zu kopieren. Dieser Artikel baut auf dem Artikel zur [Kopieraktivität in Azure Data Factory](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser SharePoint Online-Listenconnector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus SharePoint Online-Listen in jeden unterstützten Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die die Kopieraktivität als Quellen und Senken unterstützt, finden Sie unter [Unterstützte Datenspeicher und Formate](copy-activity-overview.md#supported-data-stores-and-formats).

Genauer gesagt, verwendet dieser SharePoint Online-Listenconnector die Dienstprinzipalauthentifizierung und ruft Daten über das OData-Protokoll ab.

> [!TIP]
> Dieser Connector unterstützt das Kopieren von Daten aus einer SharePoint Online-**Liste** jedoch nicht aus einer Datei. Im Abschnitt [Kopieren von Dateien aus SharePoint Online](#copy-file-from-sharepoint-online) erfahren Sie, wie Sie eine Datei kopieren.

## <a name="prerequisites"></a>Voraussetzungen

Der SharePoint Online-Listenconnector verwendet die Dienstprinzipalauthentifizierung zum Herstellen einer Verbindung mit SharePoint. Führen Sie die folgenden Schritte aus, um sie einzurichten:

1. Registrieren Sie eine Anwendungsentität in Azure Active Directory (Azure AD), indem Sie die Anleitungen unter [Registrieren Ihrer Anwendung bei einem Azure AD-Mandanten](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant) befolgen. Notieren Sie sich die folgenden Werte, die Sie zum Definieren des verknüpften Diensts verwenden:

    - Anwendungs-ID
    - Anwendungsschlüssel
    - Mandanten-ID

2. Erteilen Sie der SharePoint Online-Site Berechtigungen für Ihre registrierte Anwendung: 

    > [!NOTE]
    > Für diesen Vorgang ist die Besitzerberechtigung für die SharePoint Online-Site erforderlich. Sie finden den Besitzer, indem Sie zur Startseite der Site navigieren, in der rechten Ecke auf „X Mitglieder“ klicken und überprüfen, wer über die Rolle „Besitzer“ verfügt.

    1. Öffnen Sie den Link zur SharePoint Online-Site, z. B. `https://[your_site_url]/_layouts/15/appinv.aspx` (ersetzen Sie dabei die Site-URL).
    2. Suchen Sie die von Ihnen registrierte Anwendungs-ID, füllen Sie die leeren Felder aus, und klicken Sie auf „Erstellen“.

        - App-Domäne: `localhost.com`
        - Umleitungs-URL: `https://www.localhost.com`
        - XML-Code zur Berechtigungsanforderung:

        ```xml
        <AppPermissionRequests AllowAppOnlyPolicy="true">
            <AppPermissionRequest Scope="http://sharepoint/content/sitecollection/web" Right="Read"/>
        </AppPermissionRequests>
        ```

        ![Gewähren der Berechtigung für SharePoint](media/connector-sharepoint-online-list/sharepoint-online-grant-permission.png)

    3. Klicken Sie für diese App auf „Vertrauen“.

## <a name="get-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In den folgenden Abschnitten finden Sie Details zu Eigenschaften, mit denen Sie für den SharePoint Online-Listenconnector spezifische Data Factory-Entitäten definieren können.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für einen mit einer SharePoint Online-Liste verknüpften Dienst unterstützt:

| **Eigenschaft**        | **Beschreibung**                                              | **Erforderlich** |
| ------------------- | ------------------------------------------------------------ | ------------ |
| type                | Die type-Eigenschaft muss auf  **SharePointOnlineList** festgelegt werden.  | Ja          |
| siteUrl             | Die URL der SharePoint Online-Site, z. B. `https://contoso.sharepoint.com/sites/siteName`. | Ja          |
| servicePrincipalId  | Die Anwendungs-ID (Client-ID) für die in Azure Active Directory registrierte Anwendung. | Ja          |
| servicePrincipalKey | Der Schlüssel der Anwendung. Markieren Sie dieses Feld als **SecureString**, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja          |
| tenantId            | Die ID des Mandanten, unter dem sich Ihre Anwendung befindet.          | Ja          |
| connectVia          | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Weitere Informationen finden Sie unter [Voraussetzungen](#prerequisites) weiter oben in diesem Artikel. Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. | Nein           |

**Beispiel:**

```json
{
    "name": "SharePointOnlineList",
    "properties": {
        "type": "SharePointOnlineList",
        "typeProperties": {
            "siteUrl": "<site URL>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenantId": "<tenant ID>"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie unter [Datasets und verknüpfte Dienste](concepts-datasets-linked-services.md). Der folgende Abschnitt enthält eine Liste der Eigenschaften, die vom SAP-Tabellen-Dataset unterstützt werden.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft des Datasets muss auf **SharePointOnlineLResource** festgelegt sein. | Ja |
| listName | Der Name der SharePoint Online-Liste. | Ja |

**Beispiel**

```json
{
    "name": "SharePointOnlineListDataset",
    "properties":
    {
        "type": "SharePointOnlineListResource",
        "linkedServiceName": {
            "referenceName": "<SharePoint Online List linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "listName": "<name of the list>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den verfügbaren Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie unter [Pipelines](concepts-pipelines-activities.md).  Der folgende Abschnitt enthält eine Liste der Eigenschaften, die von der SharePoint Online-Listenquelle unterstützt werden.

### <a name="sharepoint-online-list-as-source"></a>SharePoint Online-Liste als Quelle

Für das Kopieren von Daten aus SharePoint Online-Listen werden folgende Eigenschaften im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft der Quelle der Kopieraktivität muss auf **SharePointOnlineListSource** festgelegt werden. | Ja |
| Abfrage | Benutzerdefinierte OData-Abfrageoptionen zum Filtern von Daten. Beispiel: `"$top=10&$select=Title,Number"`. | Nein |
| httpRequestTimeout | Das Timeout (in Sekunden) für das Empfangen einer Antwort auf die HTTP-Anforderung. Die Standardeinstellung ist 300 (5 Minuten). | Nein |

**Beispiel**

```json
"activities":[
    {
        "name": "CopyFromSharePointOnlineList",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SharePoint Online List input dataset name>",
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
            "source": {
                "type": "SharePointOnlineListSource",
                "query": "<OData query e.g. $top=10&$select=Title,Number>"
            }, 
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!NOTE]
> Sie können in Azure Data Factory nicht mehrere *choice*-Datentypen für eine SharePoint Online-Listenquelle auswählen.

## <a name="data-type-mapping-for-sharepoint-online-list"></a>Datentypzuordnung für SharePoint Online-Listen

Beim Kopieren von Daten aus einer SharePoint Online-Liste werden die folgenden Zuordnungen zwischen SharePoint Online-Listendatentypen und Azure Data Factory-Zwischendatentypen verwendet. 

| **SharePoint Online-Datentyp**                 | **OData-Datentyp**                                  | **Azure Data Factory-Zwischendatentyp** |
| ----------------------------------------------- | ---------------------------------------------------- | ---------------------------------------- |
| Einzelne Textzeile                             | Edm.String                                           | String                                   |
| Mehrere Textzeilen                          | Edm.String                                           | String                                   |
| Auswahl (Menü zum Auswählen)                    | Edm.String                                           | String                                   |
| Zahl (1, 1,0, 100)                            | Edm.Double                                           | Double                                   |
| Währung ($, ¥, €)                              | Edm.Double                                           | Double                                   |
| Datum und Uhrzeit                                   | Edm.DateTime                                         | Datetime                                 |
| Suche (Informationen auf dieser Site)       | Edm.Int32                                            | Int32                                    |
| Ja/nein (Kontrollkästchen)                              | Edm.Boolean                                          | Boolean                                  |
| Person oder Gruppe                                 | Edm.Int32                                            | Int32                                    |
| Hyperlink oder Bild                            | Edm.String                                           | String                                   |
| Berechnet (Berechnung basierend auf anderen Spalten) | Edm.String/Edm.Double/Edm.DateTime/Edm.Boolean | String/Double/DateTime/Boolean     |
| Attachment                                      | Nicht unterstützt                                        |                                          |
| Aufgabenergebnis                                    | Nicht unterstützt                                        |                                          |
| Externe Daten                                   | Nicht unterstützt                                        |                                          |
| Verwaltete Metadaten                                | Nicht unterstützt                                        |                                          |

## <a name="copy-file-from-sharepoint-online"></a>Kopieren von Dateien aus SharePoint Online

Sie können eine Datei aus SharePoint Online kopieren, indem Sie mit einer **Webaktivität** das Zugriffstoken von SPO authentifizieren und erfassen und dann an eine nachfolgende **Kopieraktivität** übergeben, um Daten mit einem **HTTP-Connector als Quelle** zu kopieren.

![SharePoint-Kopierdateiflow](media/connector-sharepoint-online-list/sharepoint-online-copy-file-flow.png)

1. Gehen Sie zum Erstellen einer AAD-Anwendung und Erteilen von Berechtigungen für SharePoint Online gemäß dem Abschnitt [Voraussetzungen](#prerequisites) vor. 

2. Erstellen Sie eine **Webaktivität**, um das Zugriffstoken aus SharePoint Online zu erhalten:

    - **URL:** `https://accounts.accesscontrol.windows.net/[Tenant-ID]/tokens/OAuth/2`. Ersetzen Sie die Mandanten-ID.
    - **Methode**: POST
    - **Headers**:
        - Content-Type: application/x-www-form-urlencoded
    - **Text:** `grant_type=client_credentials&client_id=[Client-ID]@[Tenant-ID]&client_secret=[Client-Secret]&resource=00000003-0000-0ff1-ce00-000000000000/[Tenant-Name].sharepoint.com@[Tenant-ID]`. Ersetzen Sie die Client-ID, das Clientgeheimnis, die Mandanten-ID und den Mandantennamen.

    > [!CAUTION]
    > Legen Sie die Option „Secure Output“ (Sichere Ausgabe) in der Webaktivität auf TRUE fest, um zu verhindern, dass der Tokenwert als Klartext protokolliert wird. Für alle weiteren Aktivitäten, die diesen Wert verwenden, sollte die Option „Sichere Eingabe“ auf „True“ festgelegt werden.

3. Kette mit einer **Kopieraktivität** mit HTTP-Connector als Quelle zum Kopieren von SharePoint Online-Dateiinhalten:

    - Verknüpfter HTTP-Dienst:
        - **Basis-URL:** `https://[site-url]/_api/web/GetFileByServerRelativeUrl('[relative-path-to-file]')/$value`. Ersetzen Sie die Site-URL und den relativen Pfad zur Datei. Beispiel für einen relativen Pfad zu einer Datei: `/sites/site2/Shared Documents/TestBook.xlsx`.
        - **Authentifizierungstyp:** Anonym *(zum Verwenden des später in der Quelle der Kopieraktivität konfigurierten Bearertokens)*
    - Dataset: Wählen Sie das gewünschte Format aus. Um die Datei unverändert zu kopieren, wählen Sie den Typ „Binär“ aus.
    - Quelle der Kopieraktivität:
        - **Anforderungsmethode:** GET
        - **Zusätzliche Header:** Verwenden Sie den Ausdruck `@{concat('Authorization: Bearer ', activity('<Web-activity-name>').output.access_token)}`, der das von der Upstreamwebaktivität generierte Bearertoken als Autorisierungsheader verwendet. Ersetzen Sie den Namen der Webaktivität.
    - Konfigurieren Sie die Senke der Kopieraktivität wie üblich.

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nächste Schritte

Eine Liste der Datenspeicher, die von der Kopieraktivität als Quellen und Senken in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher und Formate](copy-activity-overview.md#supported-data-stores-and-formats).

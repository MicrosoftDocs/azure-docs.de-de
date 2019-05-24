---
title: Hinzufügen von Blobs zu Objekten in Azure Digital Twins | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Objekten in Azure Digital Twins Blobs hinzufügen.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: b77960961a7c032faad7000f7a2ce297802a1497
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967048"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Hinzufügen von Blobs zu Objekten in Azure Digital Twins

Blobs sind unstrukturierte Darstellungen gängiger Dateitypen (z. B. Bilder oder Protokolle). Blobs verfolgen mithilfe eines MIME-Typs (beispielsweise „image/jpeg“) sowie Metadaten (Name, Beschreibung, Typ usw.) die Art der dargestellten Daten nach.

Azure Digital Twins unterstützt das Anfügen von Blobs an Geräte, Räume und Benutzer. Blobs können ein Profilbild für einen Benutzer, ein Gerätefoto, ein Video, eine Karte, ein Firmware-ZIP-Archiv, JSON-Daten, ein Protokoll o.ä. darstellen.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-overview"></a>Hochladen von Blobs – Übersicht

Sie können Blobs unter Verwendung mehrteiliger Anforderungen in bestimmte Endpunkte und deren jeweilige Funktionen hochladen.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Blobmetadaten

Neben **Content-Type** und **Content-Disposition** müssen mehrteilige Anforderungen für Azure Digital Twins-Blobs auch den richtigen JSON-Text angeben. Der zu übermittelnde JSON-Text hängt von der Art des ausgeführten HTTP-Anforderungsvorgangs ab.

Nachfolgend finden Sie die vier wichtigsten JSON-Schemas:

![JSON-Schemas][1]

JSON-Blobmetadaten entsprechen dem folgenden Modell:

```JSON
{
    "parentId": "00000000-0000-0000-0000-000000000000",
    "name": "My First Blob",
    "type": "Map",
    "subtype": "GenericMap",
    "description": "A well chosen description",
    "sharing": "None"
  }
```

| Attribut | Type | BESCHREIBUNG |
| --- | --- | --- |
| **parentId** | Zeichenfolge | Die übergeordnete Entität, der das Blob zugeordnet werden soll (Räume, Geräte oder Benutzer) |
| **name** |Zeichenfolge | Ein benutzerfreundlicher Name für das Blob |
| **type** | Zeichenfolge | Der Blobtyp: *type* und *typeId* können nicht verwendet werden.  |
| **typeId** | Integer | Die Blobtyp-ID: *type* und *typeId* können nicht verwendet werden. |
| **subtype** | Zeichenfolge | Der Blobuntertyp: *subtype* und *subtypeId* können nicht verwendet werden. |
| **subtypeId** | Integer | Die Untertyp-ID des Blobs: *subtype* und *subtypeId* können nicht verwendet werden. |
| **description** | Zeichenfolge | Benutzerdefinierte Beschreibung des Blobs |
| **sharing** | Zeichenfolge | Gibt an, ob das Blob freigegeben werden kann: Enumeration [`None`, `Tree`, `Global`] |

Blobmetadaten werden immer als erster Block mit **Content-Type** `application/json` oder als eine `.json`-Datei angegeben. Dateidaten werden im zweiten Block als einer der unterstützten MIME-Typen angegeben.

In der Swagger-Dokumentation werden diese Modell-Schemas umfassend beschrieben.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Informationen zur Verwendung der Referenzdokumentation finden Sie unter [Verwenden von Azure Digital Twins-Swagger](./how-to-use-swagger.md).

<div id="blobModel"></div>

### <a name="blobs-response-data"></a>Antwortdaten der Blobs

Einzeln zurückgegebene Blobs haben folgendes JSON-Schema:

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "parentId": "00000000-0000-0000-0000-000000000000",
  "type": "string",
  "subtype": "string",
  "typeId": 0,
  "subtypeId": 0,
  "sharing": "None",
  "description": "string",
  "contentInfos": [
    {
      "type": "string",
      "sizeBytes": 0,
      "mD5": "string",
      "version": "string",
      "lastModifiedUtc": "2019-01-12T00:58:08.689Z",
      "metadata": {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    }
  ],
  "fullName": "string",
  "spacePaths": [
    "string"
  ]
}
```

| Attribut | Type | BESCHREIBUNG |
| --- | --- | --- |
| **id** | Zeichenfolge | Der eindeutige Bezeichner für das Blob |
| **name** |Zeichenfolge | Ein benutzerfreundlicher Name für das Blob |
| **parentId** | Zeichenfolge | Die übergeordnete Entität, der das Blob zugeordnet werden soll (Räume, Geräte oder Benutzer) |
| **type** | Zeichenfolge | Der Blobtyp: *type* und *typeId* können nicht verwendet werden.  |
| **typeId** | Integer | Die Blobtyp-ID: *type* und *typeId* können nicht verwendet werden. |
| **subtype** | Zeichenfolge | Der Blobuntertyp: *subtype* und *subtypeId* können nicht verwendet werden. |
| **subtypeId** | Integer | Die Untertyp-ID des Blobs: *subtype* und *subtypeId* können nicht verwendet werden. |
| **sharing** | Zeichenfolge | Gibt an, ob das Blob freigegeben werden kann: Enumeration [`None`, `Tree`, `Global`] |
| **description** | Zeichenfolge | Benutzerdefinierte Beschreibung des Blobs |
| **contentInfos** | Array | Gibt unstrukturierte Metadateninformationen an, einschließlich der Version |
| **fullName** | Zeichenfolge | Der vollständige Name des Blobs |
| **spacePaths** | Zeichenfolge | Der Raumpfad |

Blobmetadaten werden immer als erster Block mit **Content-Type** `application/json` oder als eine `.json`-Datei angegeben. Dateidaten werden im zweiten Block als einer der unterstützten MIME-Typen angegeben.

### <a name="blob-multipart-request-examples"></a>Beispiele für mehrteilige Blobanforderung

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Um eine Textdatei als Blob hochzuladen und einem Bereich zuzuordnen, richten Sie eine authentifizierte HTTP POST-Anforderung an:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs
```

Verwenden Sie folgenden Text:

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "ParentId": "54213cf5-285f-e611-80c3-000d3a320e1e",
  "Name": "My First Blob",
  "Type": "Map",
  "SubType": "GenericMap",
  "Description": "A well chosen description",
  "Sharing": "None"
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="myblob.txt"
Content-Type: text/plain

This is my blob content. In this case, some text, but I could also be uploading a picture, an JSON file, a firmware zip, etc.

--USER_DEFINED_BOUNDARY--
```

| Wert | Ersetzen durch |
| --- | --- |
| USER_DEFINED_BOUNDARY | Name für die Begrenzung mehrteiliger Inhalte |

Der folgende Code ist eine .NET-Implementierung des gleichen Blob-Uploads mit der Klasse [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent):

```csharp
//Supply your metadata in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

//MY_BLOB.txt is the String representation of your text file
var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

Abschließend können [cURL](https://curl.haxx.se/)-Benutzer auf die gleiche Weise mehrteilige Anforderungen ausführen:

![Geräteblobs][5]

```bash
curl
 -X POST "YOUR_MANAGEMENT_API_URL/spaces/blobs"
 -H "Authorization: Bearer YOUR_TOKEN"
 -H "Accept: application/json"
 -H "Content-Type: multipart/form-data"
 -F "meta={\"ParentId\": \"YOUR_SPACE_ID\",\"Name\":\"My CURL Blob",\"Type\":\"Map\",\"SubType\":\"GenericMap\",\"Description\": \"A well chosen description\", \"Sharing\": \"None\"};type=application/json"
 -F "text=PATH_TO_FILE;type=text/plain"
```

| Wert | Ersetzen durch |
| --- | --- |
| YOUR_TOKEN | Das gültige OAuth 2.0-Token |
| YOUR_SPACE_ID | Die ID des Raums, der dem Blob zugeordnet werden soll |
| PATH_TO_FILE | Der Pfad zur Textdatei |

Eine erfolgreiche POST-Anforderung gibt die ID des neuen Blobs zurück (oben rot hervorgehoben).

## <a name="api-endpoints"></a>API-Endpunkte

Die folgenden Abschnitte beschreiben die wichtigsten blobbezogenen API-Endpunkte und ihre Funktionen.

### <a name="devices"></a>Geräte

Blobs können an Geräte angefügt werden. Die folgende Abbildung zeigt die Swagger-Referenzdokumentation für Ihre Verwaltungs-APIs. Sie gibt gerätebezogene API-Endpunkte für die Blobnutzung sowie ggf. zu übergebende erforderliche Pfadparameter an.

![Geräteblobs][2]

Wenn Sie beispielsweise ein Blob aktualisieren oder erstellen und dann an ein Gerät anfügen möchten, richten Sie eine authentifizierte HTTP PATCH-Anforderung an:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parameter | Ersetzen durch |
| --- | --- |
| *YOUR_BLOB_ID* | Die gewünschte Blob-ID |

Erfolgreiche Anforderungen geben ein JSON-Objekt zurück, wie [weiter oben beschrieben](#blobModel).

### <a name="spaces"></a>Leerzeichen

Blobs können auch an Räume angefügt werden. Die folgende Abbildung enthält alle Raum-API-Endpunkte für die Verarbeitung von Blobs. Aufgelistet sind auch alle Pfadparameter, die ggf. an diese Endpunkte übergeben werden müssen.

![Raumblobs][3]

Wenn beispielsweise ein an einen Raum angefügtes Blob zurückgegeben werden soll, richten Sie eine authentifizierte HTTP GET-Anforderung an:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parameter | Ersetzen durch |
| --- | --- |
| *YOUR_BLOB_ID* | Die gewünschte Blob-ID |

Erfolgreiche Anforderungen geben ein JSON-Objekt zurück, wie [weiter oben beschrieben](#blobModel).

Eine PATCH-Anforderung an denselben Endpunkt aktualisiert die Metadatenbeschreibungen und erstellt Versionen des Blobs. Die HTTP-Anforderung wird unter Verwendung der PATCH-Methode zusammen mit den erforderlichen Metadaten und mehrteiligen Formulardaten ausgeführt.

### <a name="users"></a>Benutzer

Blobs können an Benutzermodelle angefügt werden (z. B. um ein Profilbild zuzuordnen). Die folgende Abbildung zeigt die relevanten Benutzer-API-Endpunkte sowie alle ggf. erforderlichen Pfadparameter (z. B. `id`):

![Benutzerblobs][4]

Wenn Sie beispielsweise ein Blob abrufen möchten, das an einen Benutzer angefügt ist, richten Sie eine authentifizierte HTTP GET-Anforderung mit den erforderlichen Formulardaten an:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parameter | Ersetzen durch |
| --- | --- |
| *YOUR_BLOB_ID* | Die gewünschte Blob-ID |

Erfolgreiche Anforderungen geben ein JSON-Objekt zurück, wie [weiter oben beschrieben](#blobModel).

## <a name="common-errors"></a>Häufige Fehler

Ein häufiger Fehler ist die Verwendung falscher Headerinformationen:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

Um diesen Fehler zu beheben, stellen Sie sicher, dass die gesamte Anforderung einen geeigneten **Content-Type**-Header aufweist:

* `multipart/mixed`
* `multipart/form-data`

Vergewissern Sie sich auch, dass jeder mehrteilige Block einen entsprechenden **Content-Type** aufweist.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Swagger-Referenzdokumentation von Azure Digital Twins finden Sie unter [Verwenden von Azure Digital Twins-Swagger](how-to-use-swagger.md).

- Weitere Informationen zum Hochladen von Blobs über Postman finden Sie unter [Konfigurieren von Postman](./how-to-configure-postman.md).

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
[5]: media/how-to-add-blobs/curl.PNG
---
title: 'Azure App Configuration-REST-API: Schlüssel-Wert-Paar'
description: Referenzseiten für die Arbeit mit Schlüssel-Wert-Paaren in der Azure App Configuration-REST-API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 95081d6f8c2770d01f7836e08b6851860bf47ba8
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932556"
---
# <a name="key-values"></a>Schlüsselwerte

Ein Schlüssel-Wert-Paar ist eine Ressource, die über die eindeutige Kombination aus `key` + `label` identifiziert wird. `label` ist optional. Verwenden Sie „\0“ (als ``%00``-codierte URL), um explizit auf ein Schlüssel-Wert-Paar ohne Bezeichnung zu verweisen. Mehr dazu erfahren Sie in den Informationen zum jeweiligen Vorgang.

Dieser Artikel bezieht sich auf die API-Version 1.0.

## <a name="operations"></a>Operations

- Herunterladen
- Auflisten mehrerer Werte
- Set
- Löschen

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="syntax"></a>Syntax

```json
{
  "etag": [string],
  "key": [string],
  "label": [string, optional],
  "content_type": [string, optional],
  "value": [string],
  "last_modified": [datetime ISO 8601],
  "locked": [boolean],
  "tags": [object with string properties, optional]
}
```

## <a name="get-key-value"></a>Abrufen des Schlüssel-Wert-Paars

Erforderlich: ``{key}``, ``{api-version}``  
Optional: ``label`` (Bei Auslassung wird ein Schlüssel-Wert-Paar ohne Bezeichnung impliziert.)

```http
GET /kv/{key}?label={label}&api-version={api-version}
```

**Antworten:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26+00:00",
  "locked": "false",
  "tags": {
    "t1": "value1",
    "t2": "value2"
  }
}
```

Wenn der Schlüssel nicht vorhanden ist, wird folgende Antwort zurückgegeben:

```http
HTTP/1.1 404 Not Found
```

## <a name="get-conditionally"></a>Abrufen (bedingt)

Verwenden Sie `If-Match`- oder `If-None-Match`-Anforderungsheader, um das Zwischenspeichern auf dem Client zu verbessern. Das `etag`-Argument gehört zur Schlüsseldarstellung. Weitere Informationen finden Sie in den [Abschnitten 14.24 und 14.26](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Die folgende Anforderung ruft das Schlüssel-Wert-Paar nur dann ab, wenn die aktuelle Darstellung nicht dem angegebenen `etag` entspricht:

```http
GET /kv/{key}?api-version={api-version} HTTP/1.1
Accept: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "{etag}"
```

**Antworten:**

```http
HTTP/1.1 304 NotModified
```

oder

```http
HTTP/1.1 200 OK
```

## <a name="list-key-values"></a>Auflisten von Schlüssel-Wert-Paaren

Optional: ``key`` (Ohne Angabe wird ein beliebiger Schlüssel impliziert.) Optional: ``label`` (Ohne Angabe wird eine beliebige Bezeichnung impliziert.)

```http
GET /kv?label=*&api-version={api-version} HTTP/1.1
```

**Antwort:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8
```

Weitere Optionen finden Sie weiter unten im Abschnitt „Filtern“ dieses Artikels.

## <a name="pagination"></a>Paginierung

Das Ergebnis ist paginiert, wenn die Anzahl der zurückgegebenen Elemente das Antwortlimit überschreitet. Folgen Sie den optionalen `Link`-Headern, und verwenden Sie `rel="next"` zur Navigation.
Alternativ dazu stellt der Inhalt in Form der Eigenschaft `@nextLink` einen Link zu weiteren Elementen bereit. Der verknüpfte URI enthält das `api-version`-Argument.

```http
GET /kv?api-version={api-version} HTTP/1.1
```

**Antwort:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
Link: <{relative uri}>; rel="next"
```

```json
{
    "items": [
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="filtering"></a>Filtern

Eine Kombination aus Filterung nach `key` und `label` wird unterstützt.
Verwenden Sie die optionalen Abfragezeichenfolgeparameter `key` und `label`.

```http
GET /kv?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>Unterstützte Filter

|Schlüsselfilter|Wirkung|
|--|--|
|`key` wird weggelassen oder lautet `key=*`|Übereinstimmung mit einem **beliebigen** Schlüssel|
|`key=abc`|Übereinstimmung mit einem Schlüssel namens **abc**|
|`key=abc*`|Übereinstimmung mit Schlüsselnamen, die mit **abc** beginnen|
|`key=abc,xyz`|Übereinstimmung mit den Schlüsselnamen **abc** oder **xyz** (auf fünf CSVs begrenzt)|

|Bezeichnungsfilter|Wirkung|
|--|--|
|`label` wird weggelassen oder lautet `label=*`|Übereinstimmung mit einer **beliebigen** Bezeichnung|
|`label=%00`|Übereinstimmung mit Schlüsselwert ohne Bezeichnung|
|`label=prod`|Übereinstimmung mit Bezeichnung **prod**|
|`label=prod*`|Übereinstimmung mit Bezeichnungen, die mit **prod** beginnen|
|`label=prod,test`|Übereinstimmung mit den Bezeichnungen **prod** oder **test** (auf fünf CSVs begrenzt)|

**_Reservierte Zeichen_* _

`_`, `\`, `,`

Wenn ein Wert ein reserviertes Zeichen enthält, muss dieses mit einem Escapezeichen versehen werden: `\{Reserved Character}`. Nicht reservierten Zeichen können ebenfalls mit Escapezeichen versehen werden.

***Filterüberprüfung** _

Im Fall eines Fehlers bei der Filterüberprüfung ist die Antwort ein HTTP-Fehler `400` mit Details:

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter '{filter}'",
  "name": "{filter}",
  "detail": "{filter}(2): Invalid character",
  "status": 400
}
```

_ *Beispiele**

- All

    ```http
    GET /kv?api-version={api-version}
    ```

- Schlüsselname beginnt mit **abc** und schließt alle Bezeichnungen ein

    ```http
    GET /kv?key=abc*&label=*&api-version={api-version}
    ```

- Schlüsselname beginnt mit **abc**, und Bezeichnung ist gleich **v1** oder **v2**

    ```http
    GET /kv?key=abc*&label=v1,v2&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Anfordern bestimmter Felder

Verwenden Sie den optionalen Abfragezeichenfolgenparameter `$select`, und geben Sie eine durch Trennzeichen getrennte Liste der angeforderten Felder an. Wenn der Parameter `$select` ausgelassen wird, enthält die Antwort die Standardgruppe von Feldern.

```http
GET /kv?$select=key,value&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Zeitbasierter Zugriff

Rufen Sie eine Darstellung des Ergebnisses ab, so wie es zu einem bestimmten Zeitpunkt in der Vergangenheit war. Weitere Informationen finden Sie im Abschnitt [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1). Die Paginierung wird weiterhin wie oben in diesem Artikel definiert unterstützt.

```http
GET /kv?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Antwort:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <{relative uri}>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```

## <a name="set-key"></a>Schlüssel festlegen

- Erforderlich: ``{key}``
- Optional: ``label`` (Ohne Angabe oder bei „label=%00“ wird ein Schlüsselwert ohne Bezeichnung impliziert.)

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
```

```json
{
  "value": "example value",         // optional
  "content_type": "user defined",   // optional
  "tags": {                         // optional
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

**Antworten:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": "user defined",
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26.4874615+00:00",
  "tags": {
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

Wenn das Element gesperrt ist, erhalten Sie die folgende Antwort:

```http
HTTP/1.1 409 Conflict
Content-Type: application/problem+json; charset="utf-8"
```

```json
{
    "type": "https://azconfig.io/errors/key-locked",
    "title": "Modifing key '{key}' is not allowed",
    "name": "{key}",
    "detail": "The key is read-only. To allow modification unlock it first.",
    "status": "409"
}
```

## <a name="set-key-conditionally"></a>Schlüssel festlegen (bedingt)

Um Racebedingungen zu verhindern, verwenden Sie Anforderungsheader vom Typ `If-Match` oder `If-None-Match`. Das `etag`-Argument gehört zur Schlüsseldarstellung.
Wenn `If-Match` oder `If-None-Match` ausgelassen werden, ist der Vorgang nicht bedingt.

Die folgende Antwort aktualisiert den Wert nur, wenn die aktuelle Darstellung mit dem angegebenen `etag` übereinstimmt:

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

Die folgende Antwort aktualisiert den Wert nur, wenn die aktuelle Darstellung nicht mit dem angegebenen `etag` übereinstimmt:

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

Die folgende Anforderung fügt den Wert nur hinzu, wenn bereits eine Darstellung vorhanden ist:

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-Match: "*"
```

Die folgende Anforderung fügt den Wert nur hinzu, wenn noch keine Darstellung vorhanden ist:

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-None-Match: "*"
```

**Antworten**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

oder

```http
HTTP/1.1 412 PreconditionFailed
```

## <a name="delete"></a>Löschen

- Erforderlich: `{key}`, `{api-version}`
- Optional: `{label}` (Ohne Angabe oder bei „label=%00“ wird ein Schlüsselwert ohne Bezeichnung impliziert.)

```http
DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**Antwort:** Gibt das gelöschte Schlüssel-Wert-Paar oder nichts zurück, wenn das Schlüssel-Wert-Paar nicht vorhanden war.

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

oder

```http
HTTP/1.1 204 No Content
```

## <a name="delete-key-conditionally"></a>Schlüssel löschen (bedingt)

Dies ähnelt dem Abschnitt „Schlüssel festlegen (bedingt)“ weiter oben in diesem Artikel.

---
title: 'Azure App Configuration-REST-API: Revisionen von Schlüssel-Wert-Paaren'
description: Referenzseiten für die Arbeit mit Revisionen von Schlüssel-Wert-Paaren in der Azure App Configuration-REST-API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: cfa117d1ed017170c279b7c4e0a146ae4edac108
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932471"
---
# <a name="key-value-revisions"></a>Revisionen von Schlüssel-Wert-Paaren

Eine *Revision von Schlüssel-Wert-Paaren* definiert die historische Darstellung einer Schlüssel-Wert-Ressource. Revisionen laufen für Speicher im Free-Tarif nach 7 Tagen und für Speicher im Standard-Tarif nach 30 Tagen ab. Revisionen unterstützen den `List`-Vorgang.

``key`` ist ein optionaler Parameter für alle Vorgänge. Ohne Angabe dieses Parameters wird ein beliebiger Schlüssel impliziert.

``label`` ist ein optionaler Parameter für alle Vorgänge. Ohne Angabe dieses Parameters wird eine beliebige Bezeichnung impliziert.

Dieser Artikel bezieht sich auf die API-Version 1.0.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-revisions"></a>Auflisten von Revisionen

```http
GET /revisions?label=*&api-version={api-version} HTTP/1.1
```

**Antworten:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8"
Accept-Ranges: items
```

```json
{
    "items": [
        {
          "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
          "key": "{key}",
          "label": "{label}",
          "content_type": null,
          "value": "example value",
          "last_modified": "2017-12-05T02:41:26.4874615+00:00",
          "tags": []
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Paginierung

Das Ergebnis ist paginiert, wenn die Anzahl der zurückgegebenen Elemente das Antwortlimit überschreitet. Folgen Sie dem optionalen ``Link``-Antwortheader, und verwenden Sie ``rel="next"`` zur Navigation. Alternativ dazu stellt der Inhalt in Form der ``@nextLink``-Eigenschaft einen Link zu weiteren Elementen bereit.

```http
GET /revisions?api-version={api-version} HTTP/1.1
```

**Antwort:**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
Accept-Ranges: items
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

## <a name="list-subset-of-revisions"></a>Auflisten einer Teilmenge von Revisionen

Verwenden Sie den `Range`-Anforderungsheader. Die Antwort enthält einen `Content-Range`-Header. Wenn der Server den angeforderten Bereich nicht erfüllen kann, lautet die Antwort „HTTP `416` (`RangeNotSatisfiable`)“.

```http
GET /revisions?api-version={api-version} HTTP/1.1
Range: items=0-2
```

**Antwort**

```http
HTTP/1.1 206 Partial Content
Content-Type: application/vnd.microsoft.appconfig.revs+json; charset=utf-8
Content-Range: items 0-2/80
```

## <a name="filtering"></a>Filtern

Eine Kombination aus Filterung nach `key` und `label` wird unterstützt.
Verwenden Sie die optionalen Abfragezeichenfolgeparameter `key` und `label`.

```http
GET /revisions?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>Unterstützte Filter

|Schlüsselfilter|Wirkung|
|--|--|
|`key` wird weggelassen oder lautet `key=*`|Übereinstimmung mit einem **beliebigen** Schlüssel|
|`key=abc`|Übereinstimmung mit einem Schlüssel namens **abc**|
|`key=abc*`|Übereinstimmung mit Schlüsselnamen, die mit **abc** beginnen|
|`key=*abc`|Übereinstimmung mit Schlüsselnamen, die auf **abc** enden|
|`key=*abc*`|Übereinstimmung mit Schlüsselnamen, die **abc** enthalten|
|`key=abc,xyz`|Übereinstimmung mit den Schlüsselnamen **abc** oder **xyz** (auf fünf CSVs begrenzt)|

|Bezeichnungsfilter|Wirkung|
|--|--|
|`label` wird weggelassen oder lautet `label=`|Übereinstimmung mit einem Eintrag ohne Bezeichnung|
|`label=*`|Übereinstimmung mit einer **beliebigen** Bezeichnung|
|`label=prod`|Übereinstimmung mit Bezeichnung **prod**|
|`label=prod*`|Übereinstimmung mit Bezeichnungen, die mit **prod** beginnen|
|`label=*prod`|Übereinstimmung mit Bezeichnungen, die auf **prod** enden|
|`label=*prod*`|Übereinstimmung mit Bezeichnungen, die **prod** enthalten|
|`label=prod,test`|Übereinstimmung mit den Bezeichnungen **prod** oder **test** (auf fünf CSVs begrenzt)|

### <a name="reserved-characters"></a>Reservierte Zeichen

Folgende Zeichen sind reservierte Zeichen:

`*`, `\`, `,`

Wenn ein Wert ein reserviertes Zeichen enthält, muss dieses mit einem Escapezeichen versehen werden: `\{Reserved Character}`. Nicht reservierten Zeichen können ebenfalls mit Escapezeichen versehen werden.

### <a name="filter-validation"></a>Filterüberprüfung

Wenn bei der Filterüberprüfung ein Fehler auftritt, ist die Antwort ein HTTP-Fehler `400` mit Details:

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

### <a name="examples"></a>Beispiele

- Alle:

    ```http
    GET /revisions
    ```

- Elemente, deren Schlüsselname mit **abc** beginnt:

    ```http
    GET /revisions?key=abc*&api-version={api-version}
    ```

- Elemente, deren Schlüsselname **abc** oder **xyz** lautet und deren Bezeichnungen **prod** enthalten:

    ```http
    GET /revisions?key=abc,xyz&label=*prod*&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Anfordern bestimmter Felder

Verwenden Sie den optionalen Abfragezeichenfolgenparameter `$select`, und geben Sie eine durch Trennzeichen getrennte Liste der angeforderten Felder an. Wenn der Parameter `$select` ausgelassen wird, enthält die Antwort die Standardgruppe von Feldern.

```http
GET /revisions?$select=value,label,last_modified&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Zeitbasierter Zugriff

Rufen Sie eine Darstellung des Ergebnisses ab, so wie es zu einem bestimmten Zeitpunkt in der Vergangenheit war. Weitere Informationen finden Sie unter [HTTP Framework for Time-Based Access to Resource States -- Memento](https://tools.ietf.org/html/rfc7089#section-2.1) (HTTP-Framework für den zeitbasierten Zugriff auf Ressourcenzustände – Memento) in Abschnitt 2.1.1.

```http
GET /revisions?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Antwort:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.revs+json"
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

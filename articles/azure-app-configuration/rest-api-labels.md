---
title: 'Azure App Configuration-REST-API: Bezeichnungen'
description: Referenzseiten für die Arbeit mit Bezeichnungen in der Azure App Configuration-REST-API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 5a59f5910d44f2a2b4cd75e7a1d51c2ed5dd51a4
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932505"
---
# <a name="labels"></a>Bezeichnungen

api-version: 1.0

Die Ressource **Bezeichnung** ist folgendermaßen definiert:

```json
{
      "name": [string]             // Name of the label
}
```

Unterstützt die folgenden Vorgänge:

- List

``name`` ist ein optionaler Filterparameter für alle Vorgänge. Ohne Angabe dieses Parameters wird eine **beliebige** Bezeichnung impliziert.

## <a name="prerequisites"></a>Voraussetzungen

- Alle HTTP-Anforderungen müssen authentifiziert werden. Informationen dazu erhalten Sie im Abschnitt [Authentifizierung](./rest-api-authentication-index.md).
- Alle HTTP-Anforderungen müssen explizit die `api-version` angeben. Informationen dazu erhalten Sie im Abschnitt [Versionsverwaltung](./rest-api-versioning.md).

## <a name="list-labels"></a>Auflisten von Bezeichnungen

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**Antworten:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{label-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Paginierung

Das Ergebnis ist paginiert, wenn die Anzahl der zurückgegebenen Elemente das Antwortlimit überschreitet. Folgen Sie den optionalen `Link`-Headern, und verwenden Sie `rel="next"` zur Navigation. Alternativ dazu stellt der Inhalt in Form der Eigenschaft `@nextLink` einen Link zu weiteren Elementen bereit. Der Link zu weiteren Elementen enthält den Parameter `api-version`.

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**Antwort:**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8
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

## <a name="filtering"></a>Filtern

Die Filterung nach `name` wird unterstützt.

```http
GET /labels?name={label-name}&api-version={api-version}
```

### <a name="supported-filters"></a>Unterstützte Filter

|Schlüsselfilter|Wirkung|
|--|--|
|`name` wird weggelassen oder lautet `name=*`|Übereinstimmung mit einer **beliebigen** Bezeichnung|
|`name=abc`|Übereinstimmung mit einer Bezeichnung namens **abc**|
|`name=abc*`|Übereinstimmung mit Bezeichnungsnamen, die mit **abc** beginnen|
|`name=abc,xyz`|Übereinstimmung mit den Bezeichnungsnamen **abc** oder **xyz** (auf fünf CSVs begrenzt)|

### <a name="reserved-characters"></a>Reservierte Zeichen

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
  "title": "Invalid request parameter 'name'",
  "name": "name",
  "detail": "name(2): Invalid character",
  "status": 400
}
```

### <a name="examples"></a>Beispiele

- All

    ```http
    GET /labels?api-version={api-version}
    ```

- Bezeichnungsname beginnt mit **abc**

    ```http
    GET  /labels?name=abc*&api-version={api-version}
    ```

- Bezeichnungsname lautet **abc** oder **xyz**

    ```http
    GET /labels?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Anfordern bestimmter Felder

Verwenden Sie den optionalen Abfragezeichenfolgenparameter `$select`, und geben Sie eine durch Trennzeichen getrennte Liste der angeforderten Felder an. Wenn der Parameter `$select` ausgelassen wird, enthält die Antwort die Standardgruppe von Feldern.

```http
GET /labels?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Zeitbasierter Zugriff

Rufen Sie eine Darstellung des Ergebnisses ab, so wie es zu einem bestimmten Zeitpunkt in der Vergangenheit war. Siehe Abschnitt [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1).

```http
GET /labels&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Antwort:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json"
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

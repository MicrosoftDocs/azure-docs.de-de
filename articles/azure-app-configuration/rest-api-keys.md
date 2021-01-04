---
title: 'Azure App Configuration-REST-API: Schlüssel'
description: Referenzseiten für die Arbeit mit Schlüsseln in der Azure App Configuration-REST-API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 65ca190d7fbd6d8d4df473fbe2112eafbd031fde
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932539"
---
# <a name="keys"></a>Tasten

api-version: 1.0

Die folgende Syntax stellt eine Schlüsselressource dar:

```http
{
    "name": [string]             // Name of the key
}
```

## <a name="operations"></a>Operations

Schlüsselressourcen unterstützen den folgenden Vorgang:

- List

`name` ist ein optionaler Filterparameter für alle Vorgänge. Ohne Angabe dieses Parameters wird ein *beliebiger* Schlüssel impliziert.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-keys"></a>Dient zum Auflisten von Schlüsseln.

```http
GET /keys?api-version={api-version} HTTP/1.1
```

**Antworten:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{key-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Paginierung

Das Ergebnis ist paginiert, wenn die Anzahl der zurückgegebenen Elemente das Antwortlimit überschreitet. Folgen Sie den optionalen `Link`-Headern, und verwenden Sie `rel="next"` zur Navigation. Alternativ dazu stellt der Inhalt in Form der Eigenschaft `@nextLink` einen Link zu weiteren Elementen bereit. Der Link zu weiteren Elementen enthält den Parameter `api-version`.

```http
GET /keys?api-version={api-version} HTTP/1.1
```

**Antwort:**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json; charset=utf-8
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

Die Filterung nach ```name``` wird unterstützt.

```http
GET /keys?name={key-name}&api-version={api-version}
```

Folgende Filter werden unterstützt:

|Schlüsselfilter|Wirkung|
|--|--|
|`name` wird weggelassen oder lautet `name=*`|Übereinstimmung mit einem **beliebigen** Schlüssel|
|`name=abc`|Übereinstimmung mit einem Schlüssel namens **abc**|
|`name=abc*`|Übereinstimmung mit Schlüsselnamen, die mit **abc** beginnen|
|`name=abc,xyz`|Übereinstimmung mit den Schlüsselnamen **abc** oder **xyz** (auf fünf CSVs begrenzt)|

Die folgenden Zeichen sind reserviert: `*`, `\`, `,`

Wenn ein Wert ein reserviertes Zeichen enthält, muss dieses mit einem Escapezeichen versehen werden: `\{Reserved Character}`. Nicht reservierten Zeichen können ebenfalls mit Escapezeichen versehen werden.

## <a name="filter-validation"></a>Filterüberprüfung

Im Fall eines Fehlers bei der Filterüberprüfung ist die Antwort ein HTTP-Fehler `400` mit Details:

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

## <a name="examples"></a>Beispiele

- All

    ```http
    GET /keys?api-version={api-version}
    ```

- Der Schlüsselname beginnt mit **abc**

    ```http
    GET  /keys?name=abc*&api-version={api-version}
    ```

- Der Schlüsselname lautet **abc** oder **xyz**

    ```http
    GET /keys?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Anfordern bestimmter Felder

Verwenden Sie den optionalen Abfragezeichenfolgenparameter `$select`, und geben Sie eine durch Trennzeichen getrennte Liste der angeforderten Felder an. Wenn der Parameter `$select` ausgelassen wird, enthält die Antwort die Standardgruppe von Feldern.

```http
GET /keys?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Zeitbasierter Zugriff

Rufen Sie eine Darstellung des Ergebnisses ab, so wie es zu einem bestimmten Zeitpunkt in der Vergangenheit war. Siehe Abschnitt [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1).

```http
GET /keys&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Antwort:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <relative uri>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```

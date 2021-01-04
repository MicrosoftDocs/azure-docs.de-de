---
title: 'Azure App Configuration-REST-API: Sperren'
description: Referenzseiten für die Arbeit mit Schlüssel-Wert-Sperren in der Azure App Configuration-REST-API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: e99ce5595bae8ed64285317d9249da60e0fc1b83
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932522"
---
# <a name="locks"></a>Sperren

Diese API (Version 1.0) stellt die Semantik zum Sperren und Entsperren für die Schlüssel-Wert-Ressource bereit. Sie unterstützt die folgenden Vorgänge:

- Sperre platzieren
- Sperre entfernen

Wenn vorhanden, muss `label` ein expliziter Bezeichnungswert sein (kein Platzhalter). Dies ist ein optionaler Parameter für alle Vorgänge. Ohne Angabe dieses Parameters wird impliziert, dass keine Bezeichnung vorhanden ist.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="lock-key-value"></a>Sperren eines Schlüssel-Wert-Paars

- Erforderlich: ``{key}``, ``{api-version}``  
- Optional: ``label``

```http
PUT /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**Antworten:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

Wenn das Schlüssel-Wert-Paar nicht vorhanden ist, wird folgende Antwort zurückgegeben:

```http
HTTP/1.1 404 Not Found
```

## <a name="unlock-key-value"></a>Entsperren des Schlüssel-Wert-Paars

- Erforderlich: ``{key}``, ``{api-version}``  
- Optional: ``label``

```http
DELETE /locks/{key}?label={label}?api-version={api-version} HTTP/1.1
```

**Antworten:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

Wenn das Schlüssel-Wert-Paar nicht vorhanden ist, wird folgende Antwort zurückgegeben:

```http
HTTP/1.1 404 Not Found
```

## <a name="conditional-lock-and-unlock"></a>Bedingtes Sperren und Entsperren

Um Racebedingungen zu verhindern, verwenden Sie Anforderungsheader vom Typ `If-Match` oder `If-None-Match`. Das `etag`-Argument gehört zur Schlüsseldarstellung. Wenn `If-Match` oder `If-None-Match` ausgelassen werden, ist der Vorgang nicht bedingt.

Die folgende Anforderung wendet den Vorgang nur an, wenn die aktuelle Schlüssel-Wert-Darstellung mit dem angegebenen `etag` übereinstimmt:

```http
PUT|DELETE /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

Die folgende Anforderung wendet den Vorgang nur an, wenn die aktuelle Schlüssel-Wert-Darstellung vorhanden ist, aber nicht mit dem angegebenen `etag` übereinstimmt:

```http
PUT|DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

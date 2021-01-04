---
title: Anpassen eines Markenmodells mit der Video Indexer-API
titleSuffix: Azure Media Services
description: Erfahren Sie, wie Sie ein Markenmodell mit der Video Indexer-API anpassen.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: kumud
ms.openlocfilehash: 5fc565ecc1b501f52e934784695594dcfef2a83a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020465"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Anpassen eines Markenmodells mit der Video Indexer-API

Video Indexer unterstützt die Markenerkennung aus Sprache und visuellem Text während der Indizierung und Neuindizierung von Video- und Audioinhalten. Das Feature zur Erkennung von Marken identifiziert Erwähnungen von Produkten, Diensten und Unternehmen, die von der Markendatenbank von Bing vorgeschlagen werden. Wenn Microsoft beispielsweise in einem Video- oder Audioinhalt erwähnt wird oder im visuellen Text in einem Video angegeben wird, erkennt Video Indexer dies als Marke im Inhalt. Ein benutzerdefiniertes Markenmodell ermöglicht es Ihnen, bestimmte Marken von der Erkennung auszuschließen und Marken einzubeziehen, die Teil Ihres Modells sein sollten und sich möglicherweise nicht in der Markendatenbank von Bing befinden.

Eine ausführliche Übersicht finden Sie unter [Übersicht](customize-brands-model-overview.md).

Sie können die Video Indexer-APIs zum Erstellen, Verwenden und Bearbeiten von benutzerdefinierten Markenmodellen verwenden, die in einem Video erkannt wurden, wie in diesem Thema beschrieben. Sie können auch die Video Indexer-Website verwenden, wie unter [Anpassen des Markenmodells mit der Video Indexer-Website](customize-brands-model-with-api.md) beschrieben wird.

## <a name="create-a-brand"></a>Erstellen einer Marke

Mit der API zum [Erstellen einer Marke](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand) wird eine neue benutzerdefinierte Marke erstellt und dem benutzerdefinierten Markenmodell für das angegebene Konto hinzugefügt.

> [!NOTE]
> Wenn Sie `enabled` (im Text) auf TRUE festlegen, wird die Marke in die *Einschlussliste* aufgenommen, damit sie von Video Indexer erkannt wird. Wenn Sie `enabled` auf FALSE festlegen, wird die Marke in die *Ausschlussliste* aufgenommen, damit sie von Video Indexer nicht erkannt wird.

Einige weitere Parameter, die Sie im Text festlegen können:

* Der Wert `referenceUrl` kann eine beliebige Referenzwebsite für die Marke sein, z. B. ein Link zur entsprechenden Wikipedia-Seite.
* Der Wert `tags` ist eine Liste der Tags für die Marke. Dieses Tag wird im Feld *Kategorie* der Marke auf der Video Indexer-Website angezeigt. Beispielsweise kann die Marke „Azure“ als „Cloud“ gekennzeichnet oder eingestuft werden.

### <a name="response"></a>Antwort

Die Antwort liefert Informationen über die soeben erstellte Marke gemäß dem Format des folgenden Beispiels.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T14:59:52.7433333",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

## <a name="delete-a-brand"></a>Löschen einer Marke

Die API zum [Löschen einer Marke](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?) entfernt eine Marke aus dem benutzerdefinierten Markenmodell für das angegebene Konto. Das Konto wird im Parameter `accountId` angegeben. Nach einem erfolgreichen Aufruf wird die Marke nicht mehr in den Markenlisten *Einbeziehen* oder *Ausschließen* enthalten sein.

### <a name="response"></a>Antwort

Es wird kein Inhalt zurückgegeben, wenn die Marke erfolgreich gelöscht wurde.

## <a name="get-a-specific-brand"></a>Abrufen einer bestimmten Marke

Mit der API zum [Abrufen einer Marke](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?) können Sie im benutzerdefinierten Markenmodell für das angegebene Konto nach den Details einer Marke anhand der Marken-ID suchen.

### <a name="response"></a>Antwort

Die Antwort liefert Informationen über die gesuchte Marke (mithilfe der Marken-ID) gemäß dem Format des folgenden Beispiels.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 128846,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-01-06T13:51:38.3666667",
  "lastModified": "2018-01-11T13:51:38.3666667",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

> [!NOTE]
> Wenn `enabled` auf `true` festgelegt ist, weist dies darauf hin, dass sich die Marke in der *Einschlussliste* befindet, um von Video Indexer erkannt zu werden. Wenn `enabled` auf „false“ festgelegt ist, weist dies darauf hin, dass sich die Marke in der *Ausschlussliste* befindet, damit sie von Video Indexer nicht erkannt wird.

## <a name="update-a-specific-brand"></a>Aktualisieren einer bestimmten Marke

Mit der API zum [Aktualisieren einer Marke](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?) können Sie im benutzerdefinierten Markenmodell für das angegebene Konto nach den Details einer Marke anhand der Marken-ID suchen.

### <a name="response"></a>Antwort

Die Antwort liefert aktualisierte Informationen über die aktualisierte Marke gemäß dem Format des folgenden Beispiels.

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "Created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T15:37:50.67",
  "enabled": false,
  "description": "This is an update example",
  "tags": [
    "Tag1",
    "NewTag2"
  ]
}
```

## <a name="get-all-of-the-brands"></a>Abrufen aller Marken

Die API zum [Abrufen aller Marken](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?) gibt alle Marken im benutzerdefinierten Markenmodell für das angegebene Konto zurück, unabhängig davon, ob die Marke in der *Einschlussliste* oder der *Ausschlussliste* für Marken enthalten sein soll.

### <a name="response"></a>Antwort

Die Antwort enthält eine Liste mit allen Marken in Ihrem Konto und deren Details im Format wie im folgenden Beispiel.

```json
[
    {
        "ReferenceUrl": null,
        "id": 97974,
        "name": "Example",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-25T14:59:52.7433333",
        "LastModified": "2018-04-25T14:59:52.7433333",
        "enabled": true,
        "description": "This is an example",
        "tags": ["Tag1", "Tag2"]
    },
    {
        "ReferenceUrl": null,
        "id": 97975,
        "name": "Example2",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-26T14:59:52.7433333",
        "LastModified": "2018-04-26T14:59:52.7433333",
        "enabled": false,
        "description": "This is another example",
        "tags": ["Tag1", "Tag2"]
    },
]
```

> [!NOTE]
> Die Marke namens *Example* befindet sich in der *Einschlussliste*, die von Video Indexer erkannt werden soll, und die Marke *Example2* in der *Ausschlussliste*, sodass Video Indexer sie nicht erkennt.

## <a name="get-brands-model-settings"></a>Abrufen der Einstellungen für das Markenmodell

Die API zum [Abrufen der Markeneinstellungen](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands) gibt die Einstellungen des Markenmodells im angegebenen Konto zurück. Die Einstellungen des Markenmodells geben an, ob die Erkennung über die Bing-Markendatenbank aktiviert ist. Wenn Bing-Marken nicht aktiviert sind, erkennt Video Indexer nur Marken aus dem benutzerdefinierten Markenmodell des angegebenen Kontos.

### <a name="response"></a>Antwort

Die Antwort zeigt, ob Bing-Marken im Format des folgenden Beispiels aktiviert sind.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> Wenn `useBuiltIn` auf „true“ festgelegt ist, bedeutet dies, dass die Bing-Marken aktiviert sind. Wenn `useBuiltin` auf „false“ festgelegt ist, bedeutet dies, dass die Bing-Marken deaktiviert sind. Der `state`-Wert kann ignoriert werden, da er veraltet ist.

## <a name="update-brands-model-settings"></a>Aktualisieren der Einstellungen für das Markenmodell

Die API zum [Aktualisieren von Marken](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?) aktualisiert die Einstellungen des Markenmodells im angegebenen Konto. Die Einstellungen des Markenmodells geben an, ob die Erkennung über die Bing-Markendatenbank aktiviert ist. Wenn Bing-Marken nicht aktiviert sind, erkennt Video Indexer nur Marken aus dem benutzerdefinierten Markenmodell des angegebenen Kontos.

Wenn das `useBuiltIn`-Flag auf „true“ festgelegt ist, bedeutet dies, dass die Bing-Marken aktiviert sind. Wenn `useBuiltin` auf „false“ festgelegt ist, bedeutet dies, dass die Bing-Marken deaktiviert sind.

### <a name="response"></a>Antwort

Es wird kein Inhalt zurückgegeben, wenn die Einstellung für das Markenmodell erfolgreich aktualisiert wurde.

## <a name="next-steps"></a>Nächste Schritte

[Anpassen des Markenmodells über die Website](customize-brands-model-with-website.md)

---
title: Verwenden von Azure Video Indexer zur Anpassung des Markenmodells
titlesuffix: Azure Media Services
description: In diesem Artikel wird gezeigt, wie Sie das Markenmodell mit dem Azure Video Indexer anpassen können.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 8d0806bc0262cd45a49e4f97ea629683ac239aa8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799638"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Anpassen eines Markenmodells mit der Video Indexer-API

Video Indexer unterstützt die Markenerkennung aus Sprache und visuellem Text während der Indizierung und Neuindizierung von Video- und Audioinhalten. Das Feature zur Erkennung von Marken identifiziert Erwähnungen von Produkten, Diensten und Unternehmen, die von der Markendatenbank von Bing vorgeschlagen werden. Wenn Microsoft beispielsweise in einem Video- oder Audioinhalt erwähnt wird oder im visuellen Text in einem Video angegeben wird, erkennt Video Indexer dies als Marke im Inhalt. Ein benutzerdefiniertes Markenmodell ermöglicht es Ihnen, bestimmte Marken von der Erkennung auszuschließen und Marken einzubeziehen, die Teil Ihres Modells sein sollten und sich möglicherweise nicht in der Markendatenbank von Bing befinden.

Eine ausführliche Übersicht finden Sie unter [Übersicht](customize-brands-model-overview.md).

Sie können die Video Indexer-APIs zum Erstellen, Verwenden und Bearbeiten von benutzerdefinierten Markenmodellen verwenden, die in einem Video erkannt wurden, wie in diesem Thema beschrieben. Sie können auch die Video Indexer-Website verwenden, wie unter [Anpassen des Markenmodells mit der Video Indexer-Website](customize-brands-model-with-api.md) beschrieben wird.

## <a name="create-a-brand"></a>Erstellen einer Marke

Dadurch wird eine neue benutzerdefinierte Marke erstellt und dem benutzerdefinierten Markenmodell für das angegebene Konto hinzugefügt.

### <a name="request-url"></a>Anfrage-URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Erforderliche Parameter anzeigen und mit dem Video Indexer-Entwicklerportal ausprobieren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand).

### <a name="request-parameters"></a>Anforderungsparameter

|**Name**|**Typ**|**Erforderlich**|**Beschreibung**|
|---|---|---|---|
|location|Zeichenfolge|Ja|Die Azure-Region, an die der Aufruf weitergeleitet werden soll. Weitere Informationen finden Sie unter [Azure-Regionen und Video Indexer](regions.md).|
|accountId|Zeichenfolge|Ja|Global eindeutiger Bezeichner für das Konto|
|accessToken|Zeichenfolge|Ja|Zugriffstoken (der Bereich muss [Kontozugriffstoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) sein) zum Authentifizieren des Aufrufs. Zugriffstoken laufen nach einer Stunde ab.|

### <a name="request-body"></a>Anforderungstext

Zusätzlich zu diesen Parametern müssen Sie ein Anforderungstext-JSON-Objekt angeben, das Informationen über die neue Marke im Format wie im folgenden Beispiel bereitstellt.

```json
{
  "name": "Example",
  "enabled": true,
  "tags": ["Tag1", "Tag2"],
  "description": "This is an example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example"
}
```

Wenn Sie **enabled** auf „true“ festlegen, wird die Marke in die Liste *Einbeziehen* aufgenommen, damit sie von Video Indexer erkannt wird. Wenn Sie **enabled** auf „false“ festlegen, wird die Marke in die Liste *Ausschließen* aufgenommen, damit sie von Video Indexer nicht erkannt wird.

Der Wert für **referenceUrl** kann eine beliebige Referenzwebsite für die Marke sein, z. B. ein Link zur entsprechenden Wikipedia-Seite.

Der Wert **tags** ist eine Liste der Tags für die Marke. Dies wird im Feld *Kategorie* der Marke auf der Video Indexer-Website angezeigt. Beispielsweise kann die Marke „Azure“ als „Cloud“ gekennzeichnet oder eingestuft werden.

### <a name="response"></a>response

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

Entfernt eine Marke aus dem benutzerdefinierten Markenmodell für das angegebene Konto. Das Konto wird im Parameter **accountId** angegeben. Nach einem erfolgreichen Aufruf wird die Marke nicht mehr in den Markenlisten *Einbeziehen* oder *Ausschließen* enthalten sein.

### <a name="request-url"></a>Anfrage-URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[Erforderliche Parameter anzeigen und mit dem Video Indexer-Entwicklerportal ausprobieren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?).

### <a name="request-parameters"></a>Anforderungsparameter

|**Name**|**Typ**|**Erforderlich**|**Beschreibung**|
|---|---|---|---|
|location|Zeichenfolge|Ja|Die Azure-Region, an die der Aufruf weitergeleitet werden soll. Weitere Informationen finden Sie unter [Azure-Regionen und Video Indexer](regions.md).|
|accountId|Zeichenfolge|Ja|Global eindeutiger Bezeichner für das Konto|
|id|integer|Ja|Die Marken-ID (bei der Erstellung der Marke generiert)|
|accessToken|Zeichenfolge|Ja|Zugriffstoken (der Bereich muss [Kontozugriffstoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) sein) zum Authentifizieren des Aufrufs. Zugriffstoken laufen nach einer Stunde ab.|

### <a name="request-body"></a>Anforderungstext

Es ist kein weiterer Anforderungstext für diesen Aufruf erforderlich.

### <a name="response"></a>response

Es wird kein Inhalt zurückgegeben, wenn die Marke erfolgreich gelöscht wurde.

## <a name="get-a-specific-brand"></a>Abrufen einer bestimmten Marke

Auf diese Weise können Sie nach den Details einer Marke im benutzerdefinierten Markenmodell für das angegebene Konto anhand der Marken-ID suchen.

### <a name="request-url"></a>Anfrage-URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Erforderliche Parameter anzeigen und mit dem Video Indexer-Entwicklerportal ausprobieren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?).

### <a name="request-parameters"></a>Anforderungsparameter

|**Name**|**Typ**|**Erforderlich**|**Beschreibung**|
|---|---|---|---|
|location|Zeichenfolge|Ja|Die Azure-Region, an die der Aufruf weitergeleitet werden soll. Weitere Informationen finden Sie unter [Azure-Regionen und Video Indexer](regions.md).|
|accountId|Zeichenfolge|Ja|Global eindeutiger Bezeichner für das Konto|
|id|integer|Ja|Die Marken-ID (bei der Erstellung der Marke generiert)|
|accessToken|Zeichenfolge|Ja|Zugriffstoken (der Bereich muss [Kontozugriffstoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) sein) zum Authentifizieren des Aufrufs. Zugriffstoken laufen nach einer Stunde ab.|

### <a name="request-body"></a>Anforderungstext

Es ist kein weiterer Anforderungstext für diesen Aufruf erforderlich.

### <a name="response"></a>response

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
> Wenn **enabled** auf **true** festgelegt ist, weist dies darauf hin, dass sich die Marke in der Liste *Einbeziehen* befindet, um von Video Indexer erkannt zu werden. Wenn **enabled** auf „false“ festgelegt ist, weist dies darauf hin, dass sich die Marke in der Liste *Ausschließen* befindet, damit sie von Video Indexer nicht erkannt wird.

## <a name="update-a-specific-brand"></a>Aktualisieren einer bestimmten Marke

Auf diese Weise können Sie nach den Details einer Marke im benutzerdefinierten Markenmodell für das angegebene Konto anhand der Marken-ID suchen.

### <a name="request-url"></a>Anfrage-URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[Erforderliche Parameter anzeigen und mit dem Video Indexer-Entwicklerportal ausprobieren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?).

### <a name="request-parameters"></a>Anforderungsparameter

|**Name**|**Typ**|**Erforderlich**|**Beschreibung**|
|---|---|---|---|
|location|Zeichenfolge|Ja|Die Azure-Region, an die der Aufruf weitergeleitet werden soll. Weitere Informationen finden Sie unter [Azure-Regionen und Video Indexer](regions.md).|
|accountId|Zeichenfolge|Ja|Global eindeutiger Bezeichner für das Konto|
|id|integer|Ja|Die Marken-ID (bei der Erstellung der Marke generiert)|
|accessToken|Zeichenfolge|Ja|Zugriffstoken (der Bereich muss [Kontozugriffstoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) sein) zum Authentifizieren des Aufrufs. Zugriffstoken laufen nach einer Stunde ab.|

### <a name="request-body"></a>Anforderungstext

Zusätzlich zu diesen Parametern müssen Sie ein Anforderungstext-JSON-Objekt angeben, das aktualisierte Informationen über die zu aktualisierende Marke im Format wie im folgenden Beispiel bereitstellt.

```json
{
  "name": "Example",
  "enabled": false,
  "tags": ["Tag1", "NewTag2"],
  "description": "This is an update example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-28T15:52:22.3413983",
}
```

> [!NOTE]
> In diesem Beispiel wird die Marke, die im Beispielanforderungstext im Abschnitt **Erstellen einer Marke** erstellt wurde, hier mit einem neuen Tag und einer neuen Beschreibung aktualisiert. Der Wert **enabled** wurde ebenfalls in „false" geändert, um ihn in die Liste *Ausschließen* aufzunehmen.

### <a name="response"></a>response

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

Dies gibt alle Marken im benutzerdefinierten Markenmodell für das angegebene Konto zurück, unabhängig davon, ob die Marke in der Markenliste *Einschließen* oder *Ausschließen* enthalten sein soll.

### <a name="request-url"></a>Anfrage-URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Erforderliche Parameter anzeigen und mit dem Video Indexer-Entwicklerportal ausprobieren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?).

### <a name="request-parameters"></a>Anforderungsparameter

|**Name**|**Typ**|**Erforderlich**|**Beschreibung**|
|---|---|---|---|
|location|Zeichenfolge|Ja|Die Azure-Region, an die der Aufruf weitergeleitet werden soll. Weitere Informationen finden Sie unter [Azure-Regionen und Video Indexer](regions.md).|
|accountId|Zeichenfolge|Ja|Global eindeutiger Bezeichner für das Konto|
|accessToken|Zeichenfolge|Ja|Zugriffstoken (der Bereich muss [Kontozugriffstoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) sein) zum Authentifizieren des Aufrufs. Zugriffstoken laufen nach einer Stunde ab.|

### <a name="request-body"></a>Anforderungstext

Es ist kein weiterer Anforderungstext für diesen Aufruf erforderlich.

### <a name="response"></a>response

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
> Die Marke namens *Example* befindet sich in der Liste *Einschließen*, die von Video Indexer erkannt werden soll, und die Marke *Example2* ist in der Liste *Ausschließen*, sodass Video Indexer sie nicht erkennt.

## <a name="get-brands-model-settings"></a>Abrufen der Einstellungen für das Markenmodell

Dies gibt die Einstellungen des Markenmodells im angegebenen Konto zurück. Die Einstellungen des Markenmodells geben an, ob die Erkennung über die Bing-Markendatenbank aktiviert ist. Wenn Bing-Marken nicht aktiviert sind, erkennt Video Indexer nur Marken aus dem benutzerdefinierten Markenmodell des angegebenen Kontos.

### <a name="request-url"></a>Anfrage-URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Erforderliche Parameter anzeigen und mit dem Video Indexer-Entwicklerportal ausprobieren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands).

### <a name="request-parameters"></a>Anforderungsparameter

|**Name**|**Typ**|**Erforderlich**|**Beschreibung**|
|---|---|---|---|
|location|Zeichenfolge|Ja|Die Azure-Region, an die der Aufruf weitergeleitet werden soll. Weitere Informationen finden Sie unter [Azure-Regionen und Video Indexer](regions.md).|
|accountId|Zeichenfolge|Ja|Global eindeutiger Bezeichner für das Konto|
|accessToken|Zeichenfolge|Ja|Zugriffstoken (der Bereich muss [Kontozugriffstoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) sein) zum Authentifizieren des Aufrufs. Zugriffstoken laufen nach einer Stunde ab.|

### <a name="request-body"></a>Anforderungstext

Es ist kein weiterer Anforderungstext für diesen Aufruf erforderlich.

### <a name="response"></a>response

Die Antwort zeigt, ob Bing-Marken im Format des folgenden Beispiels aktiviert sind.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> Wenn **useBuiltIn** auf „true“ festgelegt ist, bedeutet dies, dass die Bing-Marken aktiviert sind. Wenn *useBuiltIn* auf „false“ festgelegt ist, bedeutet dies, dass die Bing-Marken deaktiviert sind. Der **state**-Wert kann ignoriert werden, da er veraltet ist.

## <a name="update-brands-model-settings"></a>Aktualisieren der Einstellungen für das Markenmodell

Dies aktualisiert die Einstellungen des Markenmodells im angegebenen Konto. Die Einstellungen des Markenmodells geben an, ob die Erkennung über die Bing-Markendatenbank aktiviert ist. Wenn Bing-Marken nicht aktiviert sind, erkennt Video Indexer nur Marken aus dem benutzerdefinierten Markenmodell des angegebenen Kontos.

### <a name="request-url"></a>Anforderungs-URL:
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/BrandsModelSettings?accessToken={accessToken}
```

[Erforderliche Parameter anzeigen und mit dem Video Indexer-Entwicklerportal ausprobieren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?).

### <a name="request-parameters"></a>Anforderungsparameter

|**Name**|**Typ**|**Erforderlich**|**Beschreibung**|
|---|---|---|---|
|location|Zeichenfolge|Ja|Die Azure-Region, an die der Aufruf weitergeleitet werden soll. Weitere Informationen finden Sie unter [Azure-Regionen und Video Indexer](regions.md).|
|accountId|Zeichenfolge|Ja|Global eindeutiger Bezeichner für das Konto|
|accessToken|Zeichenfolge|Ja|Zugriffstoken (der Bereich muss [Kontozugriffstoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) sein) zum Authentifizieren des Aufrufs. Zugriffstoken laufen nach einer Stunde ab.|

### <a name="request-body"></a>Anforderungstext

Zusätzlich zu diesen Parametern müssen Sie ein Anforderungstext-JSON-Objekt angeben, das Informationen über die neue Marke im Format wie im folgenden Beispiel bereitstellt.

```json
{
    "useBuiltIn":true
}
```

> [!NOTE]
> Wenn **useBuiltIn** auf „true“ festgelegt ist, bedeutet dies, dass die Bing-Marken aktiviert sind. Wenn *useBuiltIn* auf „false“ festgelegt ist, bedeutet dies, dass die Bing-Marken deaktiviert sind.

### <a name="response"></a>response

Es wird kein Inhalt zurückgegeben, wenn die Einstellung für das Markenmodell erfolgreich aktualisiert wurde.

## <a name="next-steps"></a>Nächste Schritte

[Anpassen des Markenmodells über die Website](customize-brands-model-with-website.md)

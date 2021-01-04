---
title: Upgrade der API von Version 5 auf Version 7 – Bing-Websuche-API
titleSuffix: Azure Cognitive Services
description: Bestimmen Sie, welche Teile Ihrer Anwendung Updates benötigen, um die Bing-Websuche-APIs der Version 7 zu verwenden.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: d930543671a5328d76a38aa7e1b421c111e89e39
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349519"
---
# <a name="upgrade-from-bing-web-search-api-v5-to-v7"></a>Upgrade der Bing-Websuche-API von Version 5 auf Version 7

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

In diesem Upgradeleitfaden sind die Änderungen zwischen Version 5 und Version 7 der Bing-Websuche-API angegeben. Anhand dieses Leitfadens können Sie die Teile Ihrer Anwendung ermitteln, die Sie zur Verwendung von Version 7 aktualisieren müssen.

## <a name="breaking-changes"></a>Aktuelle Änderungen

### <a name="endpoints"></a>Endpunkte

- Die Versionsnummer des Endpunkts hat sich von v5 in v7 geändert. Beispiel: https:\/\/api.cognitive.microsoft.com/bing/**v7.0**/search.

### <a name="error-response-objects-and-error-codes"></a>Fehlerantwortobjekte und Fehlercodes

- Alle Anforderungen mit Fehlern sollten nun ein `ErrorResponse`-Objekt im Antworttext enthalten.

- Folgende Felder wurden zum `Error`-Objekt hinzugefügt:  
  - `subCode`&mdash;Teilt den Fehlercode nach Möglichkeit in separate Buckets auf
  - `moreDetails`&mdash;Zusätzliche Informationen über den Fehler, der im `message`-Feld beschrieben ist


- Die v5-Fehlercodes wurden durch die folgenden möglichen `code`- und `subCode`-Werte ersetzt.

|Code|SubCode|BESCHREIBUNG
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing gibt ServerError zurück, wenn eine der Untercodebedingungen auftritt. Die Antwort enthält diese Fehler, wenn der HTTP-Statuscode 500 lautet.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blockiert|Bing gibt InvalidRequest zurück, wenn ein beliebiger Teil der Anforderung ungültig ist. Beispielsweise, wenn ein erforderlicher Parameter fehlt oder ein Parameterwert ungültig ist.<br/><br/>Wenn der Fehler ParameterMissing oder ParameterInvalidValue ist, lautet der HTTP-Statuscode 400.<br/><br/>Wenn der Fehler HttpNotAllowed ist, lautet der HTTP-Statuscode 410.
|RateLimitExceeded||Bing gibt RateLimitExceeded zurück, wenn Sie Ihr Kontingent für Abfragen pro Sekunde (Queries Per Second, QPS) oder Abfragen pro Monat (Queries Per Month, QPM) überschreiten.<br/><br/>Bing gibt den HTTP-Statuscode 429 zurück, wenn Sie das QPS-Kontingent überschritten haben, und 403, wenn Sie das QPM-Kontingent überschritten haben.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing gibt InvalidAuthorization zurück, wenn Bing den Aufrufer nicht authentifizieren kann. Beispielsweise, wenn der `Ocp-Apim-Subscription-Key`-Header fehlt oder der Abonnementschlüssel ungültig ist.<br/><br/>Redundanz tritt auf, wenn Sie mehrere Authentifizierungsmethoden angeben.<br/><br/>Wenn der Fehler InvalidAuthorization ist, lautet der HTTP-Statuscode 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Wenn der Aufrufer nicht über Berechtigungen für den Zugriff auf die Ressource verfügt, gibt Bing InsufficientAuthorization zurück. Dieser Fehler kann auftreten, wenn der Abonnementschlüssel deaktiviert wurde oder abgelaufen ist. <br/><br/>Wenn der Fehler InsufficientAuthorization ist, lautet der HTTP-Statuscode 403.

- Nachfolgend sind die vorherigen Fehlercodes den neuen Codes zugeordnet. Wenn Sie eine Abhängigkeit von v5-Fehlercodes eingerichtet haben, aktualisieren Sie den Code entsprechend.

|Version 5-Code|Version 7-Code.untergeordneter Code
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Disabled|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
NotImplemented|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Blockiert|InvalidRequest.Blocked


## <a name="non-breaking-changes"></a>Geringfügige Änderungen  

### <a name="headers"></a>Header

- Der optionale Anforderungsheader [Pragma](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#pragma) wurde hinzugefügt. Als Standardeinstellung gibt Bing zwischengespeicherte Inhalte (sofern vorhanden) zurück. Wenn Sie dies verhindern möchten, legen Sie den Pragma-Header auf „no-cache“ fest (z.B. „Pragma: no-cache“).

### <a name="query-parameters"></a>Abfrageparameter

- Der Abfrageparameter [AnswerCount](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) wurde hinzugefügt. Verwenden Sie diesen Parameter, um die Anzahl der Antworten anzugeben, die enthalten sein sollen. Die Antworten werden basierend auf der Rangfolge ausgewählt. Wenn Sie diesen Parameter beispielsweise auf drei (3) festlegen, sind die drei Antworten mit den höchsten Rangfolgenwerten enthalten.  

- Der Abfrageparameter [promote](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) wurde hinzugefügt. Verwenden Sie diesen Parameter zusammen mit `answerCount`, um einen oder mehrere Antworttypen unabhängig von der jeweiligen Rangfolge explizit einzuschließen. Wenn Sie beispielsweise Videos und Bilder zur Aufnahme in die Antwort höherstufen möchten, legen Sie diesen Parameter auf *videos, images* fest. Die Liste der Antworten, die Sie höherstufen möchten, zählt nicht für den `answerCount`-Grenzwert. Wenn z. B. `answerCount` den Wert 2 hat und `promote` auf *videos, images* festgelegt ist, kann die Antwort Webseiten, News, Videos und Bilder enthalten.

### <a name="object-changes"></a>Änderungen an Objekten

- Das Feld `someResultsRemoved` wurde zum [WebAnswer](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer)-Objekt hinzugefügt. Das Feld enthält einen booleschen Wert, der angibt, ob bei der Antwort einige Ergebnisse aus der Webantwort ausgeschlossen wurden.
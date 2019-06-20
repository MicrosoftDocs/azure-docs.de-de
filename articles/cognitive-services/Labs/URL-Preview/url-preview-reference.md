---
title: Referenz zu Project URL Preview
titlesuffix: Azure Cognitive Services
description: Referenz für den Project URL Preview-Endpunkt.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ms.openlocfilehash: 69db722295c9c81d45913bd078fe9cc5ab74c512
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60462587"
---
# <a name="project-url-preview-v7-reference"></a>Referenz zu Project URL Preview v7

URL Preview unterstützt kurze Beschreibungen der Webressourcen für Blogbeiträge, Forumsdiskussionen, Vorschauseiten usw. Die URL kann eine beliebige Art von Internetressource sein: Webseite, News, Bild oder Video. Die Abfrage muss eine absolute URL mit einem HTTP- oder HTTPS-Schema sein. Relative URLs oder anderen Schemas wie ftp:// werden nicht unterstützt.

Anwendungen, die die URL Preview verwenden, senden Anforderungen an den Endpunkt mit einer URL, um eine Vorschau in einem Abfrageparameter anzuzeigen. Die Anforderung muss den *Ocp-Apim-Subscription-Key*-Header enthalten.

Die JSON-Antwort kann analysiert werden, um die Vorschauinformationen zu ermitteln: Name, Beschreibung der Ressource, *isFamilyFriendly* sowie Links, die Zugriff auf ein repräsentatives Bild und die vollständige Ressource online bereitstellen.

Sie dürfen nur die Daten aus URL Preview verwenden, um Vorschaucodeausschnitte und durch Links mit ihren Quellwebsites verbundene Miniaturbilder in einer vom Endbenutzer initiierten URL-Freigabe in sozialen Medien, Chatbots oder ähnlichen Angeboten anzuzeigen. Sie dürfen keine Daten kopieren, speichern oder zwischenspeichern, die Sie von Project URL Preview erhalten. Sie müssen allen Anforderungen zur Deaktivierung von Vorschauen, die Sie von Website- oder Inhaltsbesitzern erhalten, nachkommen.

## <a name="endpoint"></a>Endpunkt
Um URL Preview-Ergebnisse anzufordern, senden Sie eine Anforderung an den folgenden Endpunkt. Verwenden Sie die Header und die URL-Parameter, um Spezifikationen genauer zu definieren.

Endpunkt GET:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=queryURL

```

Die Anforderung muss das HTTPS-Protokoll verwenden und den folgenden Abfrageparameter enthalten:

q: Die Abfrage, die die URL identifiziert, für die eine Vorschau angezeigt werden soll.

Die folgenden Abschnitte enthalten technische Details zu Antwortobjekten, Abfrageparametern und Headern, die die Suchergebnisse beeinflussen.

Informationen zu Headern, die in Anforderungen enthalten sein sollten, finden Sie unter [Header](#headers).

Informationen zu Abfrageparametern, die in Anforderungen enthalten sein sollten, finden Sie unter [Abfrageparameter](#query-parameters).

Weitere Informationen zu den JSON-Objekten, die die Antwort enthält, finden Sie unter [Antwortobjekte](#response-objects).

Die maximale Länge der Abfrage-URL beträgt 2.048 Zeichen. Um sicherzustellen, dass die URL-Länge diesen Grenzwert nicht überschreitet, sollte die maximale Länge Ihrer Abfrageparameter weniger als 1.500 Zeichen betragen. Wenn die URL länger als 2.048 Zeichen ist, gibt der Server „404: Nicht gefunden“ zurück.

Informationen zur zulässigen Verwendung und Anzeige der Ergebnisse finden Sie unter [Verwendungs- und Anzeigeanforderungen](use-display-requirements.md).

> [!NOTE]
> Einige Anforderungsheader, die für andere Such-APIs eine Bedeutung besitzen, haben keinen Einfluss auf die URL-Vorschau.
> - Pragma: Der Aufrufer hat keine Kontrolle darüber, ob die URL-Vorschau den Cache verwendet.
> - User-Agent: Zurzeit stellt die URL Preview-API keine unterschiedlichen Antworten für Aufrufe bereit, die von PCs, Laptops oder mobilen Geräten ausgehen.
> 
> Darüber hinaus sind einige Parameter für die URL-Vorschau-API zurzeit nicht von Bedeutung, werden aber ggf. für verbesserte Globalisierung in der Zukunft verwendet.

## <a name="headers"></a>Header
Die folgenden Header kann eine Anforderung und Antwort möglicherweise enthalten.

|Header|BESCHREIBUNG|
|------------|-----------------|
|<a name="market" />BingAPIs-Market|Antwortheader.<br /><br /> Der von der Anforderung verwendete Markt. Das Format lautet \<Sprachcode\>-\<Ländercode\>. Beispiel: en-US.|
|<a name="traceid" />BingAPIs-TraceId|Antwortheader.<br /><br /> Die ID des Protokolleintrags, der die Details der Anforderung enthält. Erfassen Sie diese ID, wenn ein Fehler auftritt. Wenn Sie das Problem nicht ermitteln und beheben können, übermitteln Sie diese ID und weitere Informationen an das Supportteam.|
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Erforderlicher Anforderungsheader.<br /><br /> Der Abonnementschlüssel, den Sie bei der Registrierung für diesen Dienst in [Cognitive Services](https://www.microsoft.com/cognitive-services/) erhalten haben.|
|<a name="clientid" />X-MSEdge-ClientID|Optionaler Anforderungs- und Antwortheader.<br /><br /> Bing verwendet diesen Header, um Benutzern beim Aufrufen der Bing-API ein konsistentes Verhalten bereitzustellen. Bing testet häufig neue Funktionen und Verbesserungen und verwendet dabei die Client-ID als Schlüssel für die Zuweisung von Datenverkehr an verschiedene Flights. Wenn Sie für einen Benutzer bei unterschiedlichen Anforderungen nicht dieselbe Client-ID verwenden, weist Bing den Benutzer möglicherweise mehreren widersprüchlichen Flights zu. Die Zuweisung zu mehreren widersprüchlichen Flights kann zu einer inkonsistenten Benutzererfahrung führen. Weist die zweite Anforderung beispielsweise eine andere Flight-Zuweisung als die erste auf, kann dies zu einer unerwarteten Benutzererfahrung führen. Außerdem kann Bing die Client-ID zur Anpassung der Webergebnisse an den Suchverlauf dieser Client-ID verwenden. Dies führt zu einer noch besseren Benutzererfahrung.<br /><br /> Bing verwendet den Header auch, um die Rangfolge der Ergebnisse zu verbessern, indem es die Aktivität der Client-ID analysiert. Durch die verbesserte Relevanz erhöht sich die Qualität der von Bing-APIs bereitgestellten Ergebnisse, was wiederum zu höheren Durchklickraten für den API-Consumer führt.<br /><br />Die folgenden Grundregeln gelten bei der Verwendung dieses Headers.<br /><ul><li>Jeder Benutzer, der die Anwendung auf dem Gerät verwendet, muss über eine eindeutige, von Bing generierte Client-ID verfügen.<br /><br/>Wenn Sie diesen Header nicht in die Anforderung einfügen, generiert Bing eine ID, die im Antwortheader „X-MSEdge-ClientID“ zurückgegeben wird. Der einzige Zeitpunkt, bei dem dieser Header NICHT in eine Anforderung eingeschlossen werden sollte, ist bei der erstmaligen Verwendung der App auf dem Gerät.<br /><br/></li><li>Verwenden Sie die Client-ID für jede Anforderung der Bing-API, die die App für diesen Benutzer auf dem Gerät durchführt.<br /><br/></li><li>**ACHTUNG:** Vergewissern Sie sich, dass die Client-ID nicht mit authentifizierbaren Benutzerkontoinformationen verknüpft werden kann.</li><br/><li>Behalten Sie die Client-ID bei. Verwenden Sie ein permanentes HTTP-Cookie, um sicherzustellen, dass die ID in einer Browser-App bei allen Sitzungen verwendet wird. Verwenden Sie kein Sitzungscookie. Verwenden Sie für andere Apps wie z.B. mobile Apps den permanenten Speicher des Geräts, um die ID beizubehalten.<br /><br/>Rufen Sie bei der nächsten Verwendung der App auf dem Gerät durch den Benutzer die gespeicherte Client-ID ab.</li></ul><br /> **HINWEIS:** Bing-Antworten können diesen Header enthalten, müssen es aber nicht. Enthält die Antwort diesen Header, erfassen Sie die Client-ID, und verwenden Sie sie für alle nachfolgenden Bing-Anforderungen des Benutzers auf diesem Gerät.<br /><br /> **HINWEIS:** Wenn Sie den Header „X-MSEdge-ClientID“ einfügen, dürfen Sie in die Anforderung nicht gleichzeitig Cookies einschließen.|
|<a name="clientip" />X-MSEdge-ClientIP|Optionaler Anforderungsheader.<br /><br /> Die IPv4- oder IPv6-Adresse des Clientgeräts. Die IP-Adresse wird verwendet, um den Standort des Benutzers zu ermitteln. Bing verwendet die Standortinformationen für ein sicheres Suchverhalten.<br /><br /> Verschleiern Sie nicht die Adresse (z.B. durch Ändern des letzten Oktetts in 0). Durch Verschleiern der Adresse befindet sich Ihr Standort nicht in der Nähe des tatsächlichen Gerätestandorts, wodurch Bing möglicherweise fehlerhafte Ergebnisse anzeigt.|

## <a name="query-parameters"></a>Abfrageparameter
Die Anforderung kann die folgenden Abfrageparameter enthalten. Die erforderlichen Parameter Finden Sie in der Spalte „Erforderlich“. Sie müssen die Abfrageparameter URL-codieren. Die Abfrage muss eine absolute URL mit einem HTTP- oder HTTPS-Schema sein. Relative URLs oder anderen Schemas wie ftp:// werden nicht unterstützt.

|NAME|Wert|Type|Erforderlich|
|----------|-----------|----------|--------------|
|<a name="mkt" />mkt|Der Markt, aus dem die Ergebnisse stammen. <br /><br />Eine Liste der möglichen Marktwerte finden Sie unter „Marktcodes“.<br /><br /> **HINWEIS:** Die URL-Vorschau-API unterstützt zurzeit nur die geografische Region „USA“ und die Sprache „Englisch“.<br /><br />|string|Ja|
|<a name="query" />q|Die URL, für die eine Vorschau angezeigt werden soll.|string|Ja|
|<a name="responseformat" />responseFormat|Der Medientyp, der für die Antwort verwendet werden soll. Die folgenden Werte (ohne Beachtung von Groß-/Kleinschreibung) sind möglich.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Der Standardwert ist JSON. Weitere Informationen zu den JSON-Objekten, die die Antwort enthält, finden Sie unter [Antwortobjekte](#response-objects).<br /><br />Wenn Sie JsonLd angeben, enthält der Antworttext die JSON-LD-Objekte, die die Suchergebnisse enthalten. Informationen zu JSON-LD finden Sie unter [JSON-LD](https://json-ld.org/).|string|Nein|
|<a name="safesearch"/>safeSearch|Ungültige, nicht jugendfreie Inhalte oder illegale Inhalte. Diese werden mit dem Fehlercode 400 blockiert, und das Flag *isFamilyFriendly* wird nicht zurückgegeben. <p>Für legale nicht jugendfreie Inhalte wird das Verhalten unten beschrieben. Es wird der Statuscode 200 zurückgegeben, und das Flag *IsFamilyFriendly* wird auf „false“ festgelegt.<ul><li>safeSearch=strict: Titel, Beschreibung, URL und Bild werden nicht zurückgegeben.</li><li>safeSearch=moderate: Titel, URL und Beschreibung werden abgerufen, das beschreibende Bild aber nicht.</li><li>safeSearch=off: Alle Antwortobjekte/-elemente (Titel, URL, Beschreibung und Bild) werden abgerufen.</li></ul> |string|Nicht erforderlich. </br> Standardmäßig wird safeSearch=strict verwendet.|

## <a name="response-objects"></a>Antwortobjekte
Das Antwortschema ist entweder eine [WebPage] oder ErrorResponse (wie in der Websuche-API). Wenn die Anforderung fehlschlägt, ist das Objekt auf oberster Ebene das [ErrorResponse](#errorresponse)-Objekt.

|Object|BESCHREIBUNG|
|------------|-----------------|
|[WebPage](#webpage)|Das JSON-Objekt auf oberster Ebene, das Attribute der Vorschau enthält.|

### <a name="error"></a>Error
Definiert den aufgetretenen Fehler.

|Element|BESCHREIBUNG|Type|
|-------------|-----------------|----------|
|<a name="error-code" />code|Der Fehlercode, der die Kategorie des Fehlers angibt. Eine Liste der möglichen Codes finden Sie unter [Fehlercodes](#error-codes).|string|
|<a name="error-message" />message|Eine Beschreibung des Fehlers.|string|
|<a name="error-moredetails" />moreDetails|Eine Beschreibung, die zusätzliche Informationen zum Fehler enthält.|string|
|<a name="error-parameter" />parameter|Der Abfrageparameter in der Anforderung, der den Fehler verursacht hat.|string|
|<a name="error-subcode" />subCode|Der Fehlercode, der den Fehler identifiziert. Wenn `code` z.B. InvalidRequest ist, kann `subCode` ParameterInvalid oder ParameterInvalidValue sein. |string|
|<a name="error-value" />value|Der Wert des Abfrageparameters, der ungültig war.|string|

### <a name="errorresponse"></a>ErrorResponse
Das Objekt auf oberster Ebene, das die Antwort enthält, wenn die Anforderung fehlschlägt.

|NAME|Wert|Type|
|----------|-----------|----------|
|_type|Der Typhinweis.|string|
|<a name="errors" />errors|Eine Liste von Fehlern, die die Gründe beschreiben, warum die Anforderung fehlgeschlagen ist.|[Error](#error)[]|

### <a name="webpage"></a>WebPage
Definiert Informationen zu einer Webseite in der Vorschau.

|NAME|Wert|Type|
|----------|-----------|----------|
|name|Der Seitentitel, nicht notwendigerweise der HTML-Titel.|string|
|url|Die URL, die tatsächlich durchforstet wurde (die Anforderung wurde möglicherweise weitergeleitet).|string|
|description|Kurze Beschreibung der Seite und des Inhalts|string|
|isFamilyFriendly|Am genauesten für Elemente im Webindex. Abrufvorgänge in Echtzeit führen diese Erkennung ausschließlich basierend auf der URL und nicht auf dem Seiteninhalt aus.|boolean|
|primaryImageOfPage/contentUrl|Die URL zu einem repräsentativen Bild, das in die Vorschau eingeschlossen werden soll.|string|

### <a name="identifiable"></a>Identifiable
|NAME|Wert|Type|
|-------------|-----------------|----------|
|id|Ein Ressourcenbezeichner.|string|

## <a name="error-codes"></a>Fehlercodes

Im Folgenden finden Sie die möglichen HTTP-Statuscodes, die eine Anforderung zurückgeben kann.

|Statuscode|BESCHREIBUNG|
|-----------------|-----------------|
|200|Erfolgreich.|
|400|Einer der Abfrageparameter fehlt oder ist ungültig.|
|400|ServerError, subCode ResourceError: Die angeforderte URL konnte nicht erreicht werden.|
|400|ServerError, subCode ResourceError: Die angeforderte URL hat keinen Erfolgscode zurückgegeben (gilt auch, wenn HTTP 404 zurückgegeben wurde).|
|400|InvalidRequest, subCode blockiert: Die angeforderte URL enthält möglicherweise nicht jugendfreie Inhalte und wurde blockiert.|
|401|Der Abonnementschlüssel fehlt oder ist ungültig.|
|403|Der Benutzer ist authentifiziert (er hat z.B. einen gültigen Abonnementschlüssel verwendet), aber er ist nicht für die angeforderte Ressource berechtigt.<br /><br /> Bing gibt diesen Status möglicherweise auch zurück, wenn der Aufrufer sein Kontingent für Abfragen pro Monat überschritten hat.|
|410|Die Anforderung hat HTTP anstelle des HTTPS-Protokolls verwendet. HTTPS ist das einzige unterstützte Protokoll.|
|429|Der Aufrufer hat sein Kontingent für Abfragen pro Sekunde überschritten.|
|500|Unerwarteter Serverfehler.|

Wenn die Anforderung fehlschlägt, enthält die Antwort ein [ErrorResponse](#errorresponse)-Objekt, das eine Liste der [Fehler](#error)-Objekte enthält, die beschreiben, was den Fehler verursacht hat. Wenn sich der Fehler auf einen Parameter bezieht, identifiziert das `parameter`-Feld den Parameter, der das Problem darstellt. Wenn sich der Fehler auf einen Parameterwert bezieht, identifiziert das `value`-Feld den Wert, der ungültig ist.

```json
{
  "_type": "ErrorResponse",
  "errors": [
    {
      "code": "InvalidRequest",
      "subCode": "ParameterMissing",
      "message": "Required parameter is missing.",
      "parameter": "q"
    }
  ]
}

{
  "_type": "ErrorResponse",
  "errors": [
    {
      "code": "InvalidAuthorization",
      "subCode": "AuthorizationMissing",
      "message": "Authorization is required.",
      "moreDetails": "Subscription key is not recognized."
    }
  ]
}
```

Die folgenden Werte sind für den Fehlercode und Unterfehlercodes möglich.

|Code|SubCode|BESCHREIBUNG
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Der HTTP-Statuscode ist 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blockiert|Bing gibt InvalidRequest zurück, wenn ein beliebiger Teil der Anforderung ungültig ist. Beispielsweise, wenn ein erforderlicher Parameter fehlt oder ein Parameterwert ungültig ist.<br/><br/>Wenn der Fehler ParameterMissing oder ParameterInvalidValue ist, lautet der HTTP-Statuscode 400.<br/><br/>Wenn Sie das HTTP-Protokoll anstelle von HTTPS verwenden, gibt Bing HttpNotAllowed zurück, und der HTTP-Statuscode ist 410.
|RateLimitExceeded|Keine Untercodes|Bing gibt RateLimitExceeded zurück, wenn Sie Ihr Kontingent für Abfragen pro Sekunde (Queries Per Second, QPS) oder Abfragen pro Monat (Queries Per Month, QPM) überschreiten.<br/><br/>Wenn Sie QPS überschreiten, gibt Bing den HTTP-Statuscode 429 zurück. Wenn Sie QPM überschreiten, wird 403 von Bing zurückgegeben.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing gibt InvalidAuthorization zurück, wenn Bing den Aufrufer nicht authentifizieren kann. Beispielsweise, wenn der `Ocp-Apim-Subscription-Key`-Header fehlt oder der Abonnementschlüssel ungültig ist.<br/><br/>Redundanz tritt auf, wenn Sie mehrere Authentifizierungsmethoden angeben.<br/><br/>Wenn der Fehler InvalidAuthorization ist, lautet der HTTP-Statuscode 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Wenn der Aufrufer nicht über Berechtigungen für den Zugriff auf die Ressource verfügt, gibt Bing InsufficientAuthorization zurück. Dies kann der Fall sein, wenn der Abonnementschlüssel deaktiviert wurde oder abgelaufen ist. <br/><br/>Wenn der Fehler InsufficientAuthorization ist, lautet der HTTP-Statuscode 403.

## <a name="next-steps"></a>Nächste Schritte
- [C#-Schnellstart](csharp.md)
- [Java-Schnellstart](java-quickstart.md)
- [JavaScript-Schnellstart](javascript.md)
- [Node-Schnellstart](node-quickstart.md)
- [Python-Schnellstart](python-quickstart.md)

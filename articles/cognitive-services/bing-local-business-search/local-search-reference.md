---
title: API-Referenz für die Bing-Suche für ortsansässige Unternehmen v7
titleSuffix: Azure Cognitive Services
description: Dieser Artikel enthält technische Details zu Antwortobjekten und die Abfrageparameter und Header, die die Suchergebnisse beeinflussen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 9791d99598fe3d043c42a37e2f4993edd6c5b3ba
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487132"
---
# <a name="bing-local-business-search-api-v7-reference"></a>Referenz für die API für die Bing-Suche nach ortsansässigen Unternehmen v7

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang oder bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Die API für die Suche nach ortsansässigen Unternehmen sendet eine Suchabfrage an Bing, um Ergebnisse zu erhalten, die Restaurants, Hotels oder andere lokale Unternehmen umfassen. Für Orte kann die Abfrage den Namen des ortsansässigen Unternehmens oder eine Kategorie angeben (z.B. Restaurants in meiner Nähe). Entitätsergebnisse enthalten Personen, Orte oder Gegenstände. Orte sind in diesem Zusammenhang Geschäftsentitäten, Staaten, Länder/Regionen, usw.  

Dieser Abschnitt enthält technische Details zu Antwortobjekten und die Abfrageparameter und Header, die die Suchergebnisse beeinflussen. Beispiele zum Ausführen von Abfragen finden Sie in [Schnellstart: Suche nach ortsansässigen Unternehmen mit C#](quickstarts/local-quickstart.md) oder [Schnellstart: Suche nach ortsansässigen Unternehmen mit Java](quickstarts/local-search-java-quickstart.md). 
  
Informationen zu Headern, die in Anforderungen enthalten sein sollten, finden Sie unter [Header](#headers).  
  
Informationen zu Abfrageparametern, die in Anforderungen enthalten sein sollten, finden Sie unter [Abfrageparameter](#query-parameters).  
  
Weitere Informationen zu den JSON-Objekten, die die Antwort enthält, finden Sie unter [Antwortobjekte](#response-objects).

Informationen zur zulässigen Verwendung und Anzeige der Ergebnisse finden Sie unter [Verwendungs- und Anzeigeanforderungen](../bing-web-search/use-display-requirements.md).


  
## <a name="endpoint"></a>Endpunkt  
Um Ergebnisse für ortsansässige Unternehmen zu erhalten, senden Sie eine GET-Anforderung an: 

``` 
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search

```
  
Die Anforderung muss das HTTPS-Protokoll verwenden.  
  
> [!NOTE]
> Die maximale URL-Länge beträgt 2.048 Zeichen. Um sicherzustellen, dass die URL-Länge diesen Grenzwert nicht überschreitet, sollte die maximale Länge Ihrer Abfrageparameter weniger als 1.500 Zeichen betragen. Wenn die URL länger als 2.048 Zeichen ist, gibt der Server „404: Nicht gefunden“ zurück.  
  
  
## <a name="headers"></a>Header  
Die folgenden Header kann eine Anforderung und Antwort möglicherweise enthalten.  
  
|Header|BESCHREIBUNG|  
|------------|-----------------|  
|Akzeptieren|Optionaler Anforderungsheader.<br /><br /> Der Standardmedientyp ist application/json. Um anzugeben, dass die Antwort [JSON-LD](https://json-ld.org/) verwenden soll, legen Sie den Accept-Header auf application/ld+json fest.|  
|<a name="acceptlanguage"></a>Accept-Language|Optionaler Anforderungsheader.<br /><br /> Eine durch Kommas getrennte Liste mit Sprachen, die für Zeichenfolgen der Benutzeroberfläche verwendet werden sollen. Die Liste ist absteigend nach Präferenz sortiert. Weitere Informationen hierzu (sowie zum erwarteten Format) finden Sie unter [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Dieser Header und der Abfrageparameter [setLang](#setlang) schließen sich gegenseitig aus. Geben Sie daher nicht beide an.<br /><br /> Wenn Sie diesen Header festlegen, müssen Sie auch den Abfrageparameter „cc“ angeben. Bing verwendet die erste unterstützte Sprache aus der Liste, um den Markt zu bestimmen, für den Ergebnisse zurückgegeben werden sollen, und kombiniert sie mit dem Parameterwert `cc`. Enthält die Liste keine unterstützte Sprache, sucht Bing die nächstgelegene Sprache und den nächstgelegenen Markt, die bzw. der die Anforderung unterstützt. Alternativ dazu wird für die Ergebnisse ein aggregierter oder Standardmarkt verwendet. Wenn Sie sehen möchten, welchen Markt Bing verwendet hat, untersuchen Sie den Header „BingAPIs-Market“.<br /><br /> Verwenden Sie diesen Header und den `cc`-Abfrageparameter nur, wenn Sie mehrere Sprachen angeben. Verwenden Sie andernfalls die Abfrageparameter [mkt](#mkt) und [setLang](#setlang).<br /><br /> Eine Zeichenfolge der Benutzeroberfläche ist eine Zeichenfolge, die als Bezeichnung in einer Benutzeroberfläche verwendet wird. Die JSON-Antwortobjekte enthalten nur wenige Zeichenfolgen für Benutzeroberflächen. Die Links zu Eigenschaften von „bing.com“ in den Antwortobjekten verwenden die angegebene Sprache.|  
|<a name="market"></a>BingAPIs-Market|Antwortheader.<br /><br /> Der von der Anforderung verwendete Markt. Das Format lautet \<languageCode\>-\<countryCode\>. Beispiel: en-US.|  
|<a name="traceid"></a>BingAPIs-TraceId|Antwortheader.<br /><br /> Die ID des Protokolleintrags, der die Details der Anforderung enthält. Erfassen Sie diese ID, wenn ein Fehler auftritt. Wenn Sie das Problem nicht ermitteln und beheben können, übermitteln Sie diese ID und weitere Informationen an das Supportteam.|  
|<a name="subscriptionkey"></a>Ocp-Apim-Subscription-Key|Erforderlicher Anforderungsheader.<br /><br /> Der Abonnementschlüssel, den Sie bei der Registrierung für diesen Dienst in [Cognitive Services](https://www.microsoft.com/cognitive-services/) erhalten haben.|  
|<a name="pragma"></a>Pragma|Optionaler Anforderungsheader.<br /><br /> Als Standardeinstellung gibt Bing zwischengespeicherte Inhalte (sofern vorhanden) zurück. Wenn Sie dies verhindern möchten, legen Sie den Pragma-Header auf „no-cache“ fest (z.B. „Pragma: no-cache“).
|<a name="useragent"></a>User-Agent|Optionaler Anforderungsheader.<br /><br /> Der Benutzer-Agent, von dem die Anforderung stammt. Bing verwendet den Benutzer-Agent, um die Erfahrung mobiler Benutzer zu optimieren. Auch wenn dies optional ist, sollten Sie diesen Header immer angeben.<br /><br /> Der Benutzer-Agent sollte der Zeichenfolge entsprechen, die alle häufig verwendeten Browser senden. Informationen zu Benutzer-Agents finden Sie in [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Nachfolgend einige Beispiele für Zeichenfolgen für Benutzer-Agents.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (kompatibel; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-US; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; wie Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 wie Mac OS X) AppleWebKit/536.26 (KHTML; wie Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) wie Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 wie Mac OS X) AppleWebKit/537.51.1 (KHTML, wie Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid"></a>X-MSEdge-ClientID|Optionaler Anforderungs- und Antwortheader.<br /><br /> Bing verwendet diesen Header, um Benutzern beim Aufrufen der Bing-API ein konsistentes Verhalten bereitzustellen. Bing testet häufig neue Funktionen und Verbesserungen und verwendet dabei die Client-ID als Schlüssel für die Zuweisung von Datenverkehr an verschiedene Flights. Wenn Sie für einen Benutzer bei unterschiedlichen Anforderungen nicht dieselbe Client-ID verwenden, weist Bing den Benutzer möglicherweise mehreren widersprüchlichen Flights zu. Die Zuweisung zu mehreren widersprüchlichen Flights kann zu einer inkonsistenten Benutzererfahrung führen. Weist die zweite Anforderung beispielsweise eine andere Flight-Zuweisung als die erste auf, kann dies zu einer unerwarteten Benutzererfahrung führen. Außerdem kann Bing die Client-ID zur Anpassung der Webergebnisse an den Suchverlauf dieser Client-ID verwenden. Dies führt zu einer noch besseren Benutzererfahrung.<br /><br /> Bing verwendet den Header auch, um die Rangfolge der Ergebnisse zu verbessern, indem es die Aktivität der Client-ID analysiert. Durch die verbesserte Relevanz erhöht sich die Qualität der von Bing-APIs bereitgestellten Ergebnisse, was wiederum zu höheren Durchklickraten für den API-Consumer führt.<br /><br /> **WICHTIG:** Auch wenn er optional ist, sollten Sie diesen Header als erforderlich betrachten. Das Beibehalten der Client-ID für dieselbe Kombination aus Benutzer und Gerät über mehrere Anforderungen hinweg ermöglicht 1) dem API-Consumer eine konsistente Benutzererfahrung sowie 2) höhere Durchklickraten durch eine bessere Qualität der Bing-API-Ergebnisse.<br /><br /> Die folgenden Grundregeln gelten bei der Verwendung dieses Headers.<br /><ul><li>Jeder Benutzer, der die Anwendung auf dem Gerät verwendet, muss über eine eindeutige, von Bing generierte Client-ID verfügen.<br /><br/>Wenn Sie diesen Header nicht in die Anforderung einfügen, generiert Bing eine ID, die im Antwortheader „X-MSEdge-ClientID“ zurückgegeben wird. Der einzige Zeitpunkt, zu dem dieser Header NICHT in eine Anforderung eingeschlossen werden sollte, ist bei der erstmaligen Verwendung der App auf dem Gerät.<br /><br/></li><li>Verwenden Sie die Client-ID für jede Anforderung der Bing-API, die die App für diesen Benutzer auf dem Gerät durchführt.<br /><br/></li><li>**ACHTUNG:** Vergewissern Sie sich, dass die Client-ID nicht mit authentifizierbaren Benutzerkontoinformationen verknüpft werden kann.</li><br/><li>Behalten Sie die Client-ID bei. Verwenden Sie ein permanentes HTTP-Cookie, um sicherzustellen, dass die ID in einer Browser-App bei allen Sitzungen verwendet wird. Verwenden Sie kein Sitzungscookie. Verwenden Sie für andere Apps wie z.B. mobile Apps den permanenten Speicher des Geräts, um die ID beizubehalten.<br /><br/>Rufen Sie bei der nächsten Verwendung der App auf dem Gerät durch den Benutzer die gespeicherte Client-ID ab.</li></ul><br /> **HINWEIS:** Bing-Antworten können diesen Header enthalten, müssen es aber nicht. Enthält die Antwort diesen Header, erfassen Sie die Client-ID, und verwenden Sie sie für alle nachfolgenden Bing-Anforderungen des Benutzers auf diesem Gerät.<br /><br /> **HINWEIS:** Wenn Sie den Header „X-MSEdge-ClientID“ einfügen, dürfen Sie in die Anforderung nicht gleichzeitig Cookies einschließen.|  
|<a name="clientip"></a>X-MSEdge-ClientIP|Optionaler Anforderungsheader.<br /><br /> Die IPv4- oder IPv6-Adresse des Clientgeräts. Die IP-Adresse wird verwendet, um den Standort des Benutzers zu ermitteln. Bing verwendet die Standortinformationen für ein sicheres Suchverhalten.<br /><br /> **HINWEIS:** Auch wenn dies optional ist, sollten Sie diesen Header und den Header „X-Search-Location“ immer angeben.<br /><br /> Verschleiern Sie nicht die Adresse (z.B. durch Ändern des letzten Oktetts in 0). Durch Verschleiern der Adresse stimmen Ihr Standort und der tatsächliche Gerätestandort nicht überein, wodurch Bing möglicherweise fehlerhafte Ergebnisse anzeigt.|  
|<a name="location"></a>X-Search-Location|Optionaler Anforderungsheader.<br /><br /> Eine durch Semikolons getrennte Liste mit Schlüssel/Wert-Paaren, die den geografischen Standort des Clients beschreiben. Bing verwendet die Standortinformationen für ein sicheres Suchverhalten und zur Rückgabe von lokalen relevanten Inhalten. Geben Sie das Schlüssel-Wert-Paar im Format „\<key\>:\<value\>“ an. Verwenden Sie die folgenden Schlüssel zur Angabe des Benutzerstandorts.<br /><br /><ul><li>lat: Der Breitengrad des Clientstandorts (in Grad). Der Breitengrad muss größer als oder gleich -90,0 und kleiner als oder gleich +90,0 sein. Negative Werte geben südliche Breitengrade und positive Werte nördliche Breitengrade an.<br /><br /></li><li>long: Der Längengrad des Clientstandorts (in Grad). Der Längengrad muss größer als oder gleich -180,0 und kleiner als oder gleich +180,0 sein. Negative Werte geben westliche Längengrade und positive Werte östliche Längengrade an.<br /><br /></li><li>re: Der Radius (in Metern), der die horizontale Genauigkeit der Koordinaten angibt. Übergeben Sie den Wert, der vom Standortdienst des Geräts zurückgegeben wird. Typische Werte sind 22 m für GPS/WLAN, 380 m für die Funkmasttriangulation und 18.000 m für die umgekehrte IP-Suche.<br /><br /></li><li>ts: Der UTC UNIX-Zeitstempel des Zeitpunkts, zu dem sich der Client am Standort befand. (Der UNIX-Zeitstempel gibt die Anzahl der Sekunden seit dem 1. Januar 1970 an.)<br /><br /></li><li>head: Optional. Relatives Ziel oder Reiserichtung des Clients. Geben Sie die Reiserichtung von 0 bis 360 (in Grad) im Uhrzeigersinn ausgehend vom geografischen Norden an. Geben Sie diesen Schlüssel nur an, wenn der `sp`-Schlüssel ungleich 0 ist.<br /><br /></li><li>sp: Die horizontale Geschwindigkeit (in Metern pro Sekunde), mit der das Clientgerät unterwegs ist.<br /><br /></li><li>alt: Die Höhe des Clientgeräts (in Metern).<br /><br /></li><li>are: Optional. Der Radius (in Metern), der die vertikale Genauigkeit der Koordinaten angibt. Der Standardwert für den Radius ist 50 Kilometer. Geben Sie diesen Schlüssel nur an, wenn Sie auch den `alt`-Schlüssel angeben.<br /><br /></li></ul> **HINWEIS:** Auch wenn diese Schlüssel optional sind, werden die Standortergebnisse immer genauer, je mehr Informationen Sie bereitstellen.<br /><br /> **HINWEIS:** Sie sollten den geografischen Standort des Benutzers immer angeben. Besonders wichtig ist die Standortangabe dann, wenn die IP-Adresse des Clients den physischen Standort des Benutzers nicht exakt wiedergibt (wenn der Client beispielsweise VPN verwendet). Für optimale Ergebnisse sollten Sie diesen Header und den Header „X-MSEdge-ClientIP“ einfügen. Auf jeden Fall sollten Sie aber zumindest diesen Header verwenden.|

> [!NOTE] 
> Beachten Sie, dass die Nutzungsbedingungen die Einhaltung aller geltenden Gesetze erfordert. Dies gilt auch für die Verwendung dieses Headers. Die Rechtsprechung in bestimmten Ländern (z.B. in Europa) erfordert etwa die Zustimmung des Benutzers, um bestimmte Tracking-Geräte auf Benutzergeräten platzieren zu dürfen.
  

## <a name="query-parameters"></a>Abfrageparameter  
Die Anforderung kann die folgenden Abfrageparameter enthalten. Die erforderlichen Parameter Finden Sie in der Spalte „Erforderlich“. Sie müssen die Abfrageparameter URL-codieren.  
  
  
|Name|Wert|type|Erforderlich|  
|----------|-----------|----------|--------------|
|<a name="count"></a>count|Die Anzahl der zurückzugebenden Ergebnisse, beginnend mit dem durch den Parameter `offset` angegebenen Index.|String|Nein|   
|<a name="localCategories"></a>localCategories|Liste der Optionen, die die Suche nach Unternehmenskategorie definieren.  Weitere Informationen finden Sie unter [Kategorien für die Suche nach ortsansässigen Unternehmen](local-categories.md).|String|Nein|  
|<a name="mkt"></a>mkt|Der Markt, aus dem die Ergebnisse stammen. <br /><br />Eine Liste der möglichen Marktwerte finden Sie unter „Marktcodes“.<br /><br /> **HINWEIS:** Die API für die Suche nach ortsansässigen Unternehmen unterstützt zurzeit nur den Markt und die Sprache „en-us“.<br /><br />|String|Ja|
|<a name="offset"></a>offset|Der Index, der die Ergebnisse startet, die durch den Parameter `count` angegeben werden.|Integer|Nein|  
|<a name="query"></a>q|Der Suchbegriff des Benutzers.|String|Nein|  
|<a name="responseformat"></a>responseFormat|Der Medientyp, der für die Antwort verwendet werden soll. Die folgenden Werte (ohne Beachtung von Groß-/Kleinschreibung) sind möglich.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Der Standardwert ist JSON. Weitere Informationen zu den JSON-Objekten, die die Antwort enthält, finden Sie unter [Antwortobjekte](#response-objects).<br /><br />  Wenn Sie JsonLd angeben, enthält der Antworttext die JSON-LD-Objekte, die die Suchergebnisse enthalten. Informationen zu JSON-LD finden Sie unter [JSON-LD](https://json-ld.org/).|String|Nein|  
|<a name="safesearch"></a>safeSearch|Ein Filter für nicht jugendfreie Inhalte. Die folgenden Filterwerte (ohne Beachtung von Groß-/Kleinschreibung) sind möglich.<br /><ul><li>Off: Es werden Webseiten mit nicht jugendfreiem Text oder Bildern zurückgegeben.<br /><br/></li><li>Moderate: Webseiten mit nicht jugendfreiem Text werden zurückgegeben, nicht jugendfreie Bilder oder Videos jedoch nicht.<br /><br/></li><li>Strict: Es werden keine Webseiten mit nicht jugendfreiem Text bzw. nicht jugendfreien Bildern oder Videos zurückgegeben.</li></ul><br /> Die Standardeinstellung ist „Moderate“.<br /><br /> **HINWEIS:** Stammt die Anforderung aus einem Markt, für den laut Bing-Richtlinien zu nicht jugendfreien Inhalten für `safeSearch` die Einstellung „Strict“ erforderlich ist, ignoriert Bing den `safeSearch`-Wert und verwendet stattdessen „Strict“.<br/><br/>**HINWEIS:** Bei Verwendung des Abfrageoperators `site:` kann es vorkommen, dass die Antwort unabhängig von der Einstellung des `safeSearch`-Abfrageparameters nicht jugendfreie Inhalte enthält. Verwenden Sie `site:` nur, wenn Sie wissen, welche Inhalte die Website enthält, und wenn in Ihrem Szenario ggf. auch nicht jugendfreie Inhalte zulässig sind. |String|Nein|  
|<a name="setlang"></a>setLang|Die Sprache, die für Zeichenfolgen der Benutzeroberfläche verwendet werden soll. Geben Sie die Sprache mithilfe des zweistelligen Sprachcodes nach ISO 639-1 an. Der Sprachcode für Englisch lautet z.B. „EN“. Der Standardwert ist „EN“ (Englisch).<br /><br /> Auch wenn dies optional ist, sollten Sie immer eine Sprache angeben. In der Regel wird bei `setLang` dieselbe Sprache angegeben wie bei `mkt`, sofern der Benutzer die Zeichenfolgen der Benutzeroberfläche nicht in einer anderen Sprache anzeigen möchte.<br /><br /> Dieser Parameter und der Header [Accept-Language](#acceptlanguage) schließen sich gegenseitig aus. Geben Sie daher nicht beide an.<br /><br /> Eine Zeichenfolge der Benutzeroberfläche ist eine Zeichenfolge, die als Bezeichnung in einer Benutzeroberfläche verwendet wird. Die JSON-Antwortobjekte enthalten nur wenige Zeichenfolgen für Benutzeroberflächen. Die angegebene Sprache wird auch in Links zu Eigenschaften von „bing.com“ in den Antwortobjekten verwendet.|String|Nein| 


## <a name="response-objects"></a>Antwortobjekte  
Im Folgenden finden Sie die JSON-Antwortobjekte, die die Antwort enthalten können. Wenn die Anforderung erfolgreich ist, ist das Objekt der obersten Ebene in der Antwort das [SearchResponse](#searchresponse) Objekt. Wenn die Anforderung fehlschlägt, ist das Objekt auf oberster Ebene das [ErrorResponse](#errorresponse)-Objekt.


|Object|BESCHREIBUNG|  
|------------|-----------------|  
|[Place](#place)|Definiert Informationen zu einem ortsansässigen Unternehmen, z.B. ein Restaurant oder Hotel.|  

  
### <a name="error"></a>Fehler  
Definiert den aufgetretenen Fehler.  
  
|Element|Beschreibung|type|  
|-------------|-----------------|----------|  
|<a name="error-code"></a>code|Der Fehlercode, der die Kategorie des Fehlers angibt. Eine Liste der möglichen Codes finden Sie unter [Fehlercodes](#error-codes).|String|  
|<a name="error-message"></a>message|Eine Beschreibung des Fehlers.|String|  
|<a name="error-moredetails"></a>moreDetails|Eine Beschreibung, die zusätzliche Informationen zum Fehler enthält.|String|  
|<a name="error-parameter"></a>parameter|Der Abfrageparameter in der Anforderung, der den Fehler verursacht hat.|String|  
|<a name="error-subcode"></a>subCode|Der Fehlercode, der den Fehler identifiziert. Wenn `code` z.B. InvalidRequest ist, kann `subCode` ParameterInvalid oder ParameterInvalidValue sein. |String|  
|<a name="error-value"></a>value|Der Wert des Abfrageparameters, der ungültig war.|String|  
  

### <a name="errorresponse"></a>ErrorResponse  
Das Objekt auf oberster Ebene, das die Antwort enthält, wenn die Anforderung fehlschlägt.  
  
|Name|Wert|type|  
|----------|-----------|----------|  
|_type|Der Typhinweis.|String|  
|<a name="errors"></a>errors|Eine Liste von Fehlern, die die Gründe beschreiben, warum die Anforderung fehlgeschlagen ist.|[Error](#error)[]|  

  
  
### <a name="license"></a>Lizenz  
Definiert die Lizenz, unter der der Text oder das Foto verwendet werden kann.  
  
|Name|Wert|type|  
|----------|-----------|----------|  
|name|Der Name der Lizenz.|String|  
|url|Die URL zu einer Website, auf der der Benutzer weitere Informationen zur Lizenz erhalten kann.<br /><br /> Verwenden Sie den Namen und die URL, um einen Link zu erstellen.|String|  


### <a name="link"></a>Link  
Definiert die Komponenten eines Links.  
  
|Name|Wert|type|  
|----------|-----------|----------|  
|_type|Der Typhinweis.|String|  
|text|Der Anzeigetext.|String|  
|url|Eine URL. Verwenden Sie die URL und den Anzeigetext, um einen Link zu erstellen.|String|  
  


  
### <a name="organization"></a>Organization  
Definiert einen Herausgeber.  
  
Beachten Sie, dass ein Herausgeber möglicherweise seinen Namen oder seine Website oder beide Angaben bereitstellt.  
  
|Name|Wert|type|  
|----------|-----------|----------|  
|name|Der Name des Herausgebers.|String|  
|url|Die URL zur Website des Herausgebers.<br /><br /> Beachten Sie, dass der Herausgeber möglicherweise keine Website bereitstellt.|String|  
  
  

### <a name="place"></a>Ort  
Definiert Informationen zu einem ortsansässigen Unternehmen, z.B. ein Restaurant oder Hotel.  
  
|Name|Wert|type|  
|----------|-----------|----------|  
|_type|Der Typhinweis, die auf einen der folgenden Werte festgelegt werden kann:<br /><br /><ul><li>Hotel</li><li>LocalBusiness<br /></li><li>Restaurant</ul><li>|String|  
|address|Die Postadresse, an der sich die Entität befindet.|PostalAddress|  
|entityPresentationInfo|Weitere Informationen zur Entität z.B. Hinweise, die Sie zum Bestimmen des Entitätstypen verwenden können. Beispielsweise, ob es sich um ein Restaurant oder Hotel handelt. Die Feld `entityScenario` auf „ListItem“ festgelegt.|EntityPresentationInfo|  
|name|Der Name der Entität.|String|  
|telephone|Die Telefonnummer der Entität.|String|  
|url|Die URL zur Website der Entität.<br /><br /> Verwenden Sie diese URL zusammen mit dem Namen der Entität, um einen Hyperlink zu erstellen, der den Benutzer beim Anklicken auf die Website der Entität führt.|String|  
|webSearchUrl|Die URL zum Bing-Suchergebnis für diesen Ort.|String| 
  
  
### <a name="querycontext"></a>QueryContext  
Definiert den Abfragekontext, den Bing für die Anforderung verwendet hat.  
  
|Element|Beschreibung|type|  
|-------------|-----------------|----------|  
|adultIntent|Ein boolescher Wert, der angibt, ob die angegebene Abfrage nicht jugendfreie Inhalte aufweist. Der Wert ist **TRUE**, wenn die Abfrage nicht jugendfreie Inhalte aufweist, andernfalls ist er **FALSE**.|Boolean|  
|alterationOverrideQuery|Die zu verwendende Abfragezeichenfolge, um Bing zu zwingen, die ursprüngliche Zeichenfolge zu verwenden. Wenn die Abfragezeichenfolge z.B. *saling downwind* lautet, lautet die Abfragezeichenfolge zum Überschreiben *+saling downwind*. Denken Sie daran, die Abfragezeichenfolge mit den Ergebnissen in *%2Bsaling+downwind* zu codieren.<br /><br /> Dieses Feld ist nur enthalten, wenn die ursprüngliche Abfragezeichenfolge einen Rechtschreibfehler enthält.|String|  
|alteredQuery|Die Abfragezeichenfolge, die von Bing verwendet wird, um die Abfrage auszuführen. Bing verwendet die geänderte Abfragezeichenfolge, wenn die ursprüngliche Abfragezeichenfolge Rechtschreibfehler enthielt. Wenn die Abfragezeichenfolge z.B. `saling downwind` lautet, lautet die geänderten Abfragezeichenfolge `sailing downwind`.<br /><br /> Dieses Feld ist nur enthalten, wenn die ursprüngliche Abfragezeichenfolge einen Rechtschreibfehler enthält.|String|  
|askUserForLocation|Ein boolescher Wert, der angibt, ob Bing den Standort des Benutzers benötigt, um genaue Ergebnisse bereitzustellen. Wenn Sie den Standort des Benutzers mithilfe der [X-MSEdge-ClientIP](#clientip)- und [X-Search-Location](#location)-Header angegeben haben, können Sie dieses Feld ignorieren.<br /><br /> Für standortaktivierte Abfragen (z.B. „Wetter heute“ oder „Restaurants in meiner Nähe“), die den Standort des Benutzers benötigen, um genaue Ergebnisse zu liefern, ist dieses Feld auf **TRUE** festgelegt.<br /><br /> Für standortaktivierte Abfragen, die den Standort beinhalten (z.B. „Wetter in Seattle“), ist dieses Feld auf **FALSE** festgelegt. Dieses Feld wird auch für Abfragen auf **FALSE** festgelegt, die nicht standortaktiviert sind, z.B. „Beste Verkäufer“.|Boolean|  
|originalQuery|Die Abfragezeichenfolge wie in der Anforderung angegeben.|String|  

### <a name="identifiable"></a>Identifiable

|Name|Wert|type|  
|-------------|-----------------|----------|
|id|Ein Ressourcenbezeichner.|String|
 
### <a name="rankinggroup"></a>RankingGroup
Definiert eine Suchergebnisgruppe, z.B. „mainline“.

|Name|Wert|type|  
|-------------|-----------------|----------|
|items|Eine Liste der Suchergebnisse, die in der Gruppe angezeigt werden sollen.|RankingItem|

### <a name="rankingitem"></a>RankingItem
Definiert ein anzuzeigendes Suchergebniselement.

|Name|Wert|type|  
|-------------|-----------------|----------|
|resultIndex|Ein nullbasierter Index des Elements in der Antwort, das angezeigt werden soll. Wenn das Element dieses Feld nicht enthält, werden alle Elemente in der Antwort angezeigt. Beispielsweise werden alle Artikel in der News-Antwort angezeigt.|Integer|
|answerType|Die Antwort, die das anzuzeigende Element enthält. Beispiel: News.<br /><br />Verwenden Sie den Typ, um nach der Antwort im SearchResponse-Objekt zu suchen. Der Typ ist der Name eines SearchResponse-Felds.<br /><br /> Verwenden Sie diesen Antworttyp jedoch nur, wenn dieses Objekt das value-Feld enthält. Ignorieren Sie ihn andernfalls.|String|
|textualIndex|Der Index der Antwort in textualAnswers, die angezeigt werden soll.| Ganze Zahl ohne Vorzeichen|
|value|Die ID, die eine anzuzeigende Antwort oder ein anzuzeigendes Element einer Antwort identifiziert. Wenn die ID eine Antwort identifiziert, werden alle Elemente der Antwort angezeigt.|Identifiable|

### <a name="rankingresponse"></a>RankingResponse  
Definiert, wo auf der Suchergebnisseite Inhalt platziert werden soll und in welcher Reihenfolge.  
  
|Name|Wert|  
|----------|-----------|  
|<a name="ranking-mainline"></a>mainline|Die Suchergebnisse, die in der Hauptlinie angezeigt werden sollen.|  
|<a name="ranking-pole"></a>pole|Die Suchergebnisse, die am sichtbarsten sein sollen (die z.B. über der Hauptlinie und der Randleiste angezeigt werden).|  
|<a name="ranking-sidebar"></a>sidebar|Die Suchergebnisse, die in der Randleiste angezeigt werden sollen.| 

### <a name="searchresponse"></a>SearchResponse  
Definiert das Objekt auf oberster Ebene, das die Antwort enthält, wenn die Anforderung erfolgreich ist.  
  
Beachten Sie Folgendes: Wenn der Dienst einen Denial-of-Service-Angriff vermutet, ist die Anforderung erfolgreich (HTTP-Statuscode: 200 OK). Der Antworttext ist jedoch leer.  
  
|Name|Wert|type|  
|----------|-----------|----------|  
|_type|Der Typhinweis, der auf SearchResponse festgelegt wird.|String|  
|places|Eine Liste der Entitäten, die für die Suchabfrage relevant sind.|JSON-Objekt|  
|queryContext|Ein Objekt, das die Abfragezeichenfolge enthält, die Bing für die Anforderung verwendet.<br /><br /> Dieses Objekt enthält die Abfragezeichenfolge, wie Sie vom Benutzer eingegeben wurde. Es kann auch eine geänderte Abfragezeichenfolge enthalten, die Bing für die Abfrage verwendet hat, wenn die Abfragezeichenfolge einen Rechtschreibfehler enthält.|[QueryContext](#querycontext)|  


## <a name="error-codes"></a>Fehlercodes

Im Folgenden finden Sie die möglichen HTTP-Statuscodes, die eine Anforderung zurückgeben kann.  
  
|Statuscode|BESCHREIBUNG|  
|-----------------|-----------------|  
|200|Erfolg.|  
|400|Einer der Abfrageparameter fehlt oder ist ungültig.|  
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
- [Schnellstart: Suche nach ortsansässigen Unternehmen](quickstarts/local-quickstart.md)
- [Schnellstart: Suche nach ortsansässigen Unternehmen mit Java](quickstarts/local-search-java-quickstart.md)
- [Schnellstart: Suche nach ortsansässigen Unternehmen mit Node](quickstarts/local-search-node-quickstart.md)
- [Schnellstart: Suche nach ortsansässigen Unternehmen mit Python](quickstarts/local-search-python-quickstart.md)
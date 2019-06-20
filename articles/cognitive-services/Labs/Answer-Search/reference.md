---
title: 'Referenz zu „Projekt: Suche nach Antworten“'
titlesuffix: Azure Cognitive Services
description: Referenz für den Project Answer Search-Endpunkt.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: reference
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 09fab691ea04ad98472abc4f4dee5ecb4d22e660
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60721010"
---
# <a name="project-answer-search-v7-reference"></a>Referenz zu Project Answer Search v7

Die Bing Answer Search-API nimmt einen Abfrageparameter an und gibt eine `searchResponse` mit `answerType`: `facts` oder `entities` zurück. 

Anwendungen, die die Answer Search-API verwenden, senden Anforderungen an den Endpunkt mit einer URL, um eine Vorschau in einem Abfrageparameter anzuzeigen.  Die Anforderung muss den `q=searchTerm`-Parameter und den *Ocp-Apim-Subscription-Key*-Header enthalten.   

Die JSON-Antwort kann analysiert werden, um Fakten und Entitäten zu ermitteln, die Details zum Objekt der Suche enthalten.

## <a name="endpoint"></a>Endpunkt
Um Answer Search-Ergebnisse anzufordern, senden Sie eine Anforderung an den folgenden Endpunkt. Verwenden Sie die Header und die URL-Parameter, um Spezifikationen genauer zu definieren.

Endpunkt GET: 
```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=<searchTerm>&subscription-key=0123456789ABCDEF&mkt=en-us

```

Die Anforderung muss das HTTPS-Protokoll verwenden und den folgenden Abfrageparameter enthalten:
-  `q=<URL>`: Die Abfrage, die das Objekt der Suche identifiziert.

Beispiele für das Vornehmen von Anforderungen finden Sie im [C#-Schnellstart](c-sharp-quickstart.md) bzw. im [Java-Schnellstart](java-quickstart.md). 

Die folgenden Abschnitte enthalten technische Details zu Antwortobjekten, Abfrageparametern und Headern, die die Suchergebnisse beeinflussen. 
  
Informationen zu Headern, die in Anforderungen enthalten sein sollten, finden Sie unter [Header](#headers).  
  
Informationen zu Abfrageparametern, die in Anforderungen enthalten sein sollten, finden Sie unter [Abfrageparameter](#query-parameters).  
  
Weitere Informationen zu den JSON-Objekten, die die Antwort enthält, finden Sie unter [Antwortobjekte](#response-objects).

Die maximale Länge der Abfrage-URL beträgt 2.048 Zeichen. Um sicherzustellen, dass die URL-Länge diesen Grenzwert nicht überschreitet, sollte die maximale Länge Ihrer Abfrageparameter weniger als 1.500 Zeichen betragen. Wenn die URL länger als 2.048 Zeichen ist, gibt der Server „404: Nicht gefunden“ zurück.  

Informationen zur zulässigen Verwendung und Anzeige der Ergebnisse finden Sie unter [Verwendungs- und Anzeigeanforderungen](use-display-requirements.md). 

> [!NOTE]
> Einige Anforderungsheader, die für andere Such-APIs eine Bedeutung besitzen, haben keinen Einfluss auf die URL-Vorschau.
> - Pragma: Der Aufrufer hat keine Kontrolle darüber, ob die URL-Vorschau den Cache verwendet.
> - Cache-Control: Der Aufrufer hat keine Kontrolle darüber, ob die URL-Vorschau den Cache verwendet.
> - User-Agent
> 
> Darüber hinaus sind einige Parameter für die URL-Vorschau-API zurzeit nicht von Bedeutung, werden aber ggf. für verbesserte Globalisierung in der Zukunft verwendet. 
 
## <a name="headers"></a>Header  
Die folgenden Header kann eine Anforderung und Antwort möglicherweise enthalten.  
  
|Header|BESCHREIBUNG|  
|------------|-----------------|  
|Akzeptieren|Optionaler Anforderungsheader.<br /><br /> Der Standardmedientyp ist application/json. Um anzugeben, dass die Antwort [JSON-LD](https://json-ld.org/) verwenden soll, legen Sie den Accept-Header auf application/ld+json fest.|  
|<a name="acceptlanguage" />Accept-Language|Optionaler Anforderungsheader.<br /><br /> Eine durch Kommas getrennte Liste mit Sprachen, die für Zeichenfolgen der Benutzeroberfläche verwendet werden sollen. Die Liste ist absteigend nach Präferenz sortiert. Weitere Informationen hierzu (sowie zum erwarteten Format) finden Sie unter [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Dieser Header und der Abfrageparameter [setLang](#setlang) schließen sich gegenseitig aus. Geben Sie daher nicht beide an.<br /><br /> Wenn Sie diesen Header festlegen, müssen Sie auch den Abfrageparameter „cc“ angeben. Bing verwendet die erste unterstützte Sprache aus der Liste, um den Markt zu bestimmen, für den Ergebnisse zurückgegeben werden sollen, und kombiniert sie mit dem Parameterwert `cc`. Enthält die Liste keine unterstützte Sprache, sucht Bing die nächstgelegene Sprache und den nächstgelegenen Markt, die bzw. der die Anforderung unterstützt. Alternativ dazu wird für die Ergebnisse ein aggregierter oder Standardmarkt verwendet. Wenn Sie sehen möchten, welchen Markt Bing verwendet hat, untersuchen Sie den Header „BingAPIs-Market“.<br /><br /> Verwenden Sie diesen Header und den `cc`-Abfrageparameter nur, wenn Sie mehrere Sprachen angeben. Verwenden Sie andernfalls die Abfrageparameter [mkt](#mkt) und [setLang](#setlang).<br /><br /> Eine Zeichenfolge der Benutzeroberfläche ist eine Zeichenfolge, die als Bezeichnung in einer Benutzeroberfläche verwendet wird. Die JSON-Antwortobjekte enthalten nur wenige Zeichenfolgen für Benutzeroberflächen. Die Links zu Eigenschaften von Bing.com in den Antwortobjekten verwenden die angegebene Sprache.|  
|<a name="market" />BingAPIs-Market|Antwortheader.<br /><br /> Der von der Anforderung verwendete Markt. Das Format lautet \<Sprachcode\>-\<Ländercode\>. Beispiel: en-US.|  
|<a name="traceid" />BingAPIs-TraceId|Antwortheader.<br /><br /> Die ID des Protokolleintrags, der die Details der Anforderung enthält. Erfassen Sie diese ID, wenn ein Fehler auftritt. Wenn Sie das Problem nicht ermitteln und beheben können, übermitteln Sie diese ID und weitere Informationen an das Supportteam.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Erforderlicher Anforderungsheader.<br /><br /> Der Abonnementschlüssel, den Sie bei der Registrierung für diesen Dienst in [Cognitive Services](https://www.microsoft.com/cognitive-services/) erhalten haben.|  
|<a name="pragma" />Pragma|Optionaler Anforderungsheader.<br /><br /> Als Standardeinstellung gibt Bing zwischengespeicherte Inhalte (sofern vorhanden) zurück. Wenn Sie dies verhindern möchten, legen Sie den Pragma-Header auf „no-cache“ fest (z.B. „Pragma: no-cache“).
|<a name="useragent" />User-Agent|Optionaler Anforderungsheader.<br /><br /> Der Benutzer-Agent, von dem die Anforderung stammt. Bing verwendet den Benutzer-Agent, um die Erfahrung mobiler Benutzer zu optimieren. Auch wenn dies optional ist, sollten Sie diesen Header immer angeben.<br /><br /> Der Benutzer-Agent sollte der Zeichenfolge entsprechen, die alle häufig verwendeten Browser senden. Informationen zu Benutzer-Agents finden Sie in [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Nachfolgend einige Beispiele für Zeichenfolgen für Benutzer-Agents.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (kompatibel; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-US; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; wie Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 wie Mac OS X) AppleWebKit/536.26 (KHTML; wie Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) wie Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 wie Mac OS X) AppleWebKit/537.51.1 (KHTML, wie Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Optionaler Anforderungs- und Antwortheader.<br /><br /> Bing verwendet diesen Header, um Benutzern beim Aufrufen der Bing-API ein konsistentes Verhalten bereitzustellen. Bing testet häufig neue Funktionen und Verbesserungen und verwendet dabei die Client-ID als Schlüssel für die Zuweisung von Datenverkehr an verschiedene Flights. Wenn Sie für einen Benutzer bei unterschiedlichen Anforderungen nicht dieselbe Client-ID verwenden, weist Bing den Benutzer möglicherweise mehreren widersprüchlichen Flights zu. Die Zuweisung zu mehreren widersprüchlichen Flights kann zu einer inkonsistenten Benutzererfahrung führen. Weist die zweite Anforderung beispielsweise eine andere Flight-Zuweisung als die erste auf, kann dies zu einer unerwarteten Benutzererfahrung führen. Außerdem kann Bing die Client-ID zur Anpassung der Webergebnisse an den Suchverlauf dieser Client-ID verwenden. Dies führt zu einer noch besseren Benutzererfahrung.<br /><br /> Bing verwendet den Header auch, um die Rangfolge der Ergebnisse zu verbessern, indem es die Aktivität der Client-ID analysiert. Durch die verbesserte Relevanz erhöht sich die Qualität der von Bing-APIs bereitgestellten Ergebnisse, was wiederum zu höheren Durchklickraten für den API-Consumer führt.<br /><br /> **WICHTIG:** Auch wenn er optional ist, sollten Sie diesen Header als erforderlich betrachten. Das Beibehalten der Client-ID für dieselbe Kombination aus Benutzer und Gerät über mehrere Anforderungen hinweg ermöglicht 1) dem API-Consumer eine konsistente Benutzererfahrung sowie 2) höhere Durchklickraten durch eine bessere Qualität der Bing-API-Ergebnisse.<br /><br /> Die folgenden Grundregeln gelten bei der Verwendung dieses Headers.<br /><ul><li>Jeder Benutzer, der die Anwendung auf dem Gerät verwendet, muss über eine eindeutige, von Bing generierte Client-ID verfügen.<br /><br/>Wenn Sie diesen Header nicht in die Anforderung einfügen, generiert Bing eine ID, die im Antwortheader „X-MSEdge-ClientID“ zurückgegeben wird. Der einzige Zeitpunkt, bei dem dieser Header NICHT in eine Anforderung eingeschlossen werden sollte, ist bei der erstmaligen Verwendung der App auf dem Gerät.<br /><br/></li><li>Verwenden Sie die Client-ID für jede Anforderung der Bing-API, die die App für diesen Benutzer auf dem Gerät durchführt.<br /><br/></li><li>**ACHTUNG:** Vergewissern Sie sich, dass die Client-ID nicht mit authentifizierbaren Benutzerkontoinformationen verknüpft werden kann.</li><br/><li>Behalten Sie die Client-ID bei. Verwenden Sie ein permanentes HTTP-Cookie, um sicherzustellen, dass die ID in einer Browser-App bei allen Sitzungen verwendet wird. Verwenden Sie kein Sitzungscookie. Verwenden Sie für andere Apps wie z.B. mobile Apps den permanenten Speicher des Geräts, um die ID beizubehalten.<br /><br/>Rufen Sie bei der nächsten Verwendung der App auf dem Gerät durch den Benutzer die gespeicherte Client-ID ab.</li></ul><br /> **HINWEIS:** Bing-Antworten können diesen Header enthalten, müssen es aber nicht. Enthält die Antwort diesen Header, erfassen Sie die Client-ID, und verwenden Sie sie für alle nachfolgenden Bing-Anforderungen des Benutzers auf diesem Gerät.<br /><br /> **HINWEIS:** Wenn Sie den Header „X-MSEdge-ClientID“ einfügen, dürfen Sie in die Anforderung nicht gleichzeitig Cookies einschließen.|  
|<a name="clientip" />X-MSEdge-ClientIP|Optionaler Anforderungsheader.<br /><br /> Die IPv4- oder IPv6-Adresse des Clientgeräts. Die IP-Adresse wird verwendet, um den Standort des Benutzers zu ermitteln. Bing verwendet die Standortinformationen für ein sicheres Suchverhalten.<br /><br /> **HINWEIS:** Auch wenn dies optional ist, sollten Sie diesen Header und den Header „X-Search-Location“ immer angeben.<br /><br /> Verschleiern Sie nicht die Adresse (z.B. durch Ändern des letzten Oktetts in 0). Durch Verschleiern der Adresse stimmen Ihr Standort und der tatsächliche Gerätestandort nicht überein, wodurch Bing möglicherweise fehlerhafte Ergebnisse anzeigt.|  
|<a name="location" />X-Search-Location|Optionaler Anforderungsheader.<br /><br /> Eine durch Semikolons getrennte Liste mit Schlüssel/Wert-Paaren, die den geografischen Standort des Clients beschreiben. Bing verwendet die Standortinformationen für ein sicheres Suchverhalten und zur Rückgabe von lokalen relevanten Inhalten. Geben Sie das Schlüssel-Wert-Paar im Format \<Schlüssel\>:\<Wert\> an. Verwenden Sie die folgenden Schlüssel zur Angabe des Benutzerstandorts.<br /><br /><ul><li>lat: Der Breitengrad des Clientstandorts (in Grad). Der Breitengrad muss größer als oder gleich -90,0 und kleiner als oder gleich +90,0 sein. Negative Werte geben südliche Breitengrade und positive Werte nördliche Breitengrade an.<br /><br /></li><li>long: Der Längengrad des Clientstandorts (in Grad). Der Längengrad muss größer als oder gleich -180,0 und kleiner als oder gleich +180,0 sein. Negative Werte geben westliche Längengrade und positive Werte östliche Längengrade an.<br /><br /></li><li>re: Der Radius (in Metern), der die horizontale Genauigkeit der Koordinaten angibt. Übergeben Sie den Wert, der vom Standortdienst des Geräts zurückgegeben wird. Typische Werte sind 22 m für GPS/WLAN, 380 m für Funkmasttriangulation und 18.000 m für umgekehrte IP-Suche.<br /><br /></li><li>ts: Der UTC UNIX-Zeitstempel des Zeitpunkts, zu dem sich der Client am Standort befand. (Der UNIX-Zeitstempel gibt die Anzahl der Sekunden seit dem 1. Januar 1970 an.)<br /><br /></li><li>head: Optional. Relatives Ziel oder Reiserichtung des Clients. Geben Sie die Reiserichtung von 0 bis 360 (in Grad) im Uhrzeigersinn ausgehend vom geografischen Norden an. Geben Sie diesen Schlüssel nur an, wenn der `sp`-Schlüssel ungleich 0 ist.<br /><br /></li><li>sp: Die horizontale Geschwindigkeit (in Metern pro Sekunde), mit der das Clientgerät unterwegs ist.<br /><br /></li><li>alt: Die Höhe des Clientgeräts (in Metern).<br /><br /></li><li>are: Optional. Der Radius (in Metern), der die vertikale Genauigkeit der Koordinaten angibt. Der Standardwert für den Radius ist 50 Kilometer. Geben Sie diesen Schlüssel nur an, wenn Sie auch den `alt`-Schlüssel angeben.<br /><br /></li></ul> **HINWEIS:** Auch wenn diese Schlüssel optional sind, werden die Standortergebnisse immer genauer, je mehr Informationen Sie bereitstellen.<br /><br /> **HINWEIS:** Sie sollten den geografischen Standort des Benutzers immer angeben. Besonders wichtig ist die Standortangabe dann, wenn die IP-Adresse des Clients den physischen Standort des Benutzers nicht exakt wiedergibt (wenn der Client beispielsweise VPN verwendet). Für optimale Ergebnisse sollten Sie diesen Header und den Header „X-MSEdge-ClientIP“ einfügen. Auf jeden Fall sollten Sie aber zumindest diesen Header verwenden.|

> [!NOTE] 
> Beachten Sie, dass die Nutzungsbedingungen die Einhaltung aller geltenden Gesetze erfordert. Dies gilt auch für die Verwendung dieses Headers. Die Rechtsprechung in bestimmten Ländern (z.B. in Europa) erfordert etwa die Zustimmung des Benutzers, um bestimmte Tracking-Geräte auf Benutzergeräten platzieren zu dürfen.
  

## <a name="query-parameters"></a>Abfrageparameter  
Die Anforderung kann die folgenden Abfrageparameter enthalten. Die erforderlichen Parameter Finden Sie in der Spalte „Erforderlich“. Sie müssen die Abfrageparameter URL-codieren.  
  
  
|NAME|Wert|Type|Erforderlich|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|Der Markt, aus dem die Ergebnisse stammen. <br /><br />Eine Liste der möglichen Marktwerte finden Sie unter „Marktcodes“.<br /><br /> **HINWEIS:** Die URL-Vorschau-API unterstützt zurzeit nur den Markt und die Sprache „en-us“.<br /><br />|string|Ja|  
|<a name="query" />q|Die URL, für die eine Vorschau angezeigt werden soll.|string|Ja|  
|<a name="responseformat" />responseFormat|Der Medientyp, der für die Antwort verwendet werden soll. Die folgenden Werte (ohne Beachtung von Groß-/Kleinschreibung) sind möglich.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Der Standardwert ist JSON. Weitere Informationen zu den JSON-Objekten, die die Antwort enthält, finden Sie unter [Antwortobjekte](#response-objects).<br /><br />  Wenn Sie JsonLd angeben, enthält der Antworttext die JSON-LD-Objekte, die die Suchergebnisse enthalten. Informationen zu JSON-LD finden Sie unter [JSON-LD](https://json-ld.org/).|string|Nein|  
|<a name="safesearch" />safeSearch|Ein Filter für nicht jugendfreie Inhalte. Die folgenden Filterwerte (ohne Beachtung von Groß-/Kleinschreibung) sind möglich.<br /><ul><li>Off: Es werden Webseiten mit nicht jugendfreiem Text oder Bildern zurückgegeben.<br /><br/></li><li>Moderate: Webseiten mit nicht jugendfreiem Text werden zurückgegeben, nicht jugendfreie Bilder oder Videos jedoch nicht.<br /><br/></li><li>Strict: Es werden keine Webseiten mit nicht jugendfreiem Text bzw. nicht jugendfreien Bildern oder Videos zurückgegeben.</li></ul><br /> Die Standardeinstellung ist „Moderate“.<br /><br /> **HINWEIS:** Stammt die Anforderung aus einem Markt, für den laut Bing-Richtlinien zu nicht jugendfreien Inhalten für `safeSearch` die Einstellung „Strict“ erforderlich ist, ignoriert Bing den `safeSearch`-Wert und verwendet stattdessen „Strict“.<br/><br/>**HINWEIS:** Bei Verwendung des Abfrageoperators `site:` kann es vorkommen, dass die Antwort unabhängig von der Einstellung des `safeSearch`-Abfrageparameters nicht jugendfreie Inhalte enthält. Verwenden Sie `site:` nur, wenn Sie wissen, welche Inhalte die Website enthält, und wenn in Ihrem Szenario ggf. auch nicht jugendfreie Inhalte zulässig sind. |string|Nein|  
|<a name="setlang" />setLang|Die Sprache, die für Zeichenfolgen der Benutzeroberfläche verwendet werden soll. Geben Sie die Sprache mithilfe des zweistelligen Sprachcodes nach ISO 639-1 an. Der Sprachcode für Englisch lautet z.B. „EN“. Der Standardwert ist „EN“ (Englisch).<br /><br /> Auch wenn dies optional ist, sollten Sie immer eine Sprache angeben. In der Regel wird bei `setLang` dieselbe Sprache angegeben wie bei `mkt`, sofern der Benutzer die Zeichenfolgen der Benutzeroberfläche nicht in einer anderen Sprache anzeigen möchte.<br /><br /> Dieser Parameter und der Header [Accept-Language](#acceptlanguage) schließen sich gegenseitig aus. Geben Sie daher nicht beide an.<br /><br /> Eine Zeichenfolge der Benutzeroberfläche ist eine Zeichenfolge, die als Bezeichnung in einer Benutzeroberfläche verwendet wird. Die JSON-Antwortobjekte enthalten nur wenige Zeichenfolgen für Benutzeroberflächen. Die angegebene Sprache wird auch in Links zu Eigenschaften von „bing.com“ in den Antwortobjekten verwendet.|string|Nein| 


## <a name="response-objects"></a>Antwortobjekte  
Das Antwortschema ist entweder eine [WebPage] oder ErrorResponse (wie in der Websuche-API). Wenn die Anforderung fehlschlägt, ist das Objekt auf oberster Ebene das [ErrorResponse](#errorresponse)-Objekt.


|Object|BESCHREIBUNG|  
|------------|-----------------|  
|[WebPage]|Das JSON-Objekt auf oberster Ebene, das Attribute der Vorschau enthält.|  
|[Fact]|Das JSON-Objekt auf oberster Ebene, das Fakten enthält.| 
|[Entities|Das JSON-Objekt auf oberster Ebene, das Entitätsdetails enthält.| 

  
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
|<a name="errors" />errors|Eine Liste von Fehlern, die die Gründe beschreiben, warum die Anforderung fehlgeschlagen ist.|[Fehler](#error)|  

  
  
### <a name="license"></a>Lizenz  
Definiert die Lizenz, unter der der Text oder das Foto verwendet werden kann.  
  
|NAME|Wert|Type|  
|----------|-----------|----------|  
|name|Der Name der Lizenz.|string|  
|url|Die URL zu einer Website, auf der der Benutzer weitere Informationen zur Lizenz erhalten kann.<br /><br /> Verwenden Sie den Namen und die URL, um einen Link zu erstellen.|string|  
  

### <a name="licenseattribution"></a>LicenseAttribution  
Definiert eine vertragliche Regel für die Lizenzzuordnung.  
  
|NAME|Wert|Type|  
|----------|-----------|----------|  
|_type|Ein Typhinweis, der auf LicenseAttribution festgelegt wird.|string|  
|license|Die Lizenz, unter der der Inhalt verwendet werden kann.|[Lizenz](#license)|  
|licenseNotice|Die Lizenz, die neben dem Zielfeld angezeigt wird. Beispiel: „Text unter CC-BY-SA-Lizenz“.<br /><br /> Verwenden Sie Namen und die URL im `license`-Feld, um einen Link zu der Website zu erstellen, die Details zur Lizenz beschreibt. Ersetzen Sie den Lizenznamen in der `licenseNotice`-Zeichenfolge (z.B. CC-BY-SA) durch den Link, die Sie soeben erstellt haben.|string|  
|mustBeCloseToContent|Ein boolescher Wert, der bestimmt, ob der Inhalt der Regel in großer Nähe zu dem Feld platziert werden muss, für das die Regel gilt. Wenn **TRUE**, muss der Inhalt in großer Nähe platziert werden. Wenn der Wert **FALSE** oder dieses Feld nicht vorhanden ist, kann der Inhalt nach dem Ermessen des Aufrufers platziert werden.|Boolean|  
|targetPropertyName|Der Name des Felds, für das die Regel gilt.|string|  
  

### <a name="link"></a>Link  
Definiert die Komponenten eines Links.  
  
|NAME|Wert|Type|  
|----------|-----------|----------|  
|_type|Der Typhinweis.|string|  
|text|Der Anzeigetext.|string|  
|url|Eine URL. Verwenden Sie die URL und den Anzeigetext, um einen Link zu erstellen.|string|  
  

### <a name="linkattribution"></a>LinkAttribution  
Definiert eine vertragliche Regel für die Linkzuordnung.  
  
|NAME|Wert|Type|  
|----------|-----------|----------|  
|_type|Ein Typhinweis, der auf LinkAttribution festgelegt wird.|string|  
|mustBeCloseToContent|Ein boolescher Wert, der bestimmt, ob der Inhalt der Regel in großer Nähe zu dem Feld platziert werden muss, für das die Regel gilt. Wenn **TRUE**, muss der Inhalt in großer Nähe platziert werden. Wenn der Wert **FALSE** oder dieses Feld nicht vorhanden ist, kann der Inhalt nach dem Ermessen des Aufrufers platziert werden.|Boolean|  
|targetPropertyName|Der Name des Felds, für das die Regel gilt.<br /><br /> Wenn kein Ziel angegeben wird, gilt die Zuordnung für die Entität als Ganzes und sollte unmittelbar nach der Entitätspräsentation angezeigt werden. Wenn es mehrere Text- und Linkzuordnungsregeln gibt, die kein Ziel angeben, sollten Sie diese verketten und mithilfe einer „Data from:“-Bezeichnung anzeigen. Beispiel: „Data from <Anbietername1\> &#124; <Anbietername2\>“.|string|  
|text|Der Zuordnungstext.|string|  
|url|Die URL zur Website des Anbieters. Verwenden Sie `text` und die URL, um den Link zu erstellen.|string|  
  
  
### <a name="mediaattribution"></a>MediaAttribution  
Definiert eine vertragliche Regel für die Medienzuordnung.  
  
|NAME|Wert|Type|  
|----------|-----------|----------|  
|_type|Ein Typhinweis, der auf MediaAttribution festgelegt wird.|string|  
|mustBeCloseToContent|Ein boolescher Wert, der bestimmt, ob der Inhalt der Regel in großer Nähe zu dem Feld platziert werden muss, für das die Regel gilt. Wenn **TRUE**, muss der Inhalt in großer Nähe platziert werden. Wenn der Wert **FALSE** oder dieses Feld nicht vorhanden ist, kann der Inhalt nach dem Ermessen des Aufrufers platziert werden.|Boolean|  
|targetPropertyName|Der Name des Felds, für das die Regel gilt.|string|  
|url|Die URL, die Sie zum Erstellen des Links der Medieninhalte verwenden. Wenn das Ziel ein Bild ist, würden Sie z.B. die URL verwenden, um das Bild klickbar zu machen.|string|  
  
  
  
### <a name="organization"></a>Organisation  
Definiert einen Herausgeber.  
  
Beachten Sie, dass ein Herausgeber möglicherweise seinen Namen oder seine Website oder beide Angaben bereitstellt.  
  
|NAME|Wert|Type|  
|----------|-----------|----------|  
|name|Der Name des Herausgebers.|string|  
|url|Die URL zur Website des Herausgebers.<br /><br /> Beachten Sie, dass der Herausgeber möglicherweise keine Website bereitstellt.|string|  
  
  

### <a name="webpage"></a>WebPage  
Definiert Informationen zu einer Webseite in der Vorschau.  
  
|NAME|Wert|Type|  
|----------|-----------|----------|
|name|Der Seitentitel, nicht notwendigerweise der HTML-Titel.|string|
|url|Die URL, die tatsächlich durchforstet wurde (die Anforderung wurde möglicherweise weitergeleitet).|string|  
|description|Kurze Beschreibung der Seite und des Inhalts|string|  
|isFamilyFriendly|Am genauesten für Elemente im Webindex. Abrufvorgänge in Echtzeit führen diese Erkennung ausschließlich basierend auf der URL und nicht auf dem Seiteninhalt aus.|boolean|
|primaryImageOfPage/contentUrl|Die URL zu einem repräsentativen Bild, das in die Vorschau eingeschlossen werden soll.|string| 
  
  
### <a name="querycontext"></a>QueryContext  
Definiert den Abfragekontext, den Bing für die Anforderung verwendet hat.  
  
|Element|BESCHREIBUNG|Type|  
|-------------|-----------------|----------|  
|adultIntent|Ein boolescher Wert, der angibt, ob die angegebene Abfrage nicht jugendfreie Inhalte aufweist. Der Wert ist **TRUE**, wenn die Abfrage nicht jugendfreie Inhalte aufweist, andernfalls ist er **FALSE**.|Boolean|  
|alterationOverrideQuery|Die zu verwendende Abfragezeichenfolge, um Bing zu zwingen, die ursprüngliche Zeichenfolge zu verwenden. Wenn die Abfragezeichenfolge z.B. *saling downwind* lautet, lautet die Abfragezeichenfolge zum Überschreiben *+saling downwind*. Denken Sie daran, die Abfragezeichenfolge mit den Ergebnissen in *%2Bsaling+downwind* zu codieren.<br /><br /> Dieses Feld ist nur enthalten, wenn die ursprüngliche Abfragezeichenfolge einen Rechtschreibfehler enthält.|string|  
|alteredQuery|Die Abfragezeichenfolge, die von Bing verwendet wird, um die Abfrage auszuführen. Bing verwendet die geänderte Abfragezeichenfolge, wenn die ursprüngliche Abfragezeichenfolge Rechtschreibfehler enthielt. Wenn die Abfragezeichenfolge z.B. `saling downwind` lautet, lautet die geänderten Abfragezeichenfolge `sailing downwind`.<br /><br /> Dieses Feld ist nur enthalten, wenn die ursprüngliche Abfragezeichenfolge einen Rechtschreibfehler enthält.|string|  
|askUserForLocation|Ein boolescher Wert, der angibt, ob Bing den Standort des Benutzers benötigt, um genaue Ergebnisse bereitzustellen. Wenn Sie den Standort des Benutzers mithilfe der [X-MSEdge-ClientIP](#clientip)- und [X-Search-Location](#location)-Header angegeben haben, können Sie dieses Feld ignorieren.<br /><br /> Für standortaktivierte Abfragen (z.B. „Wetter heute“ oder „Restaurants in meiner Nähe“), die den Standort des Benutzers benötigen, um genaue Ergebnisse zu liefern, ist dieses Feld auf **TRUE** festgelegt.<br /><br /> Für standortaktivierte Abfragen, die den Standort beinhalten (z.B. „Wetter in Seattle“), ist dieses Feld auf **FALSE** festgelegt. Dieses Feld wird auch für Abfragen auf **FALSE** festgelegt, die nicht standortaktiviert sind, z.B. „Beste Verkäufer“.|Boolean|  
|originalQuery|Die Abfragezeichenfolge wie in der Anforderung angegeben.|string|  

### <a name="identifiable"></a>Identifiable

|NAME|Wert|Type|  
|-------------|-----------------|----------|
|id|Ein Ressourcenbezeichner.|string|
 
### <a name="rankinggroup"></a>RankingGroup
Definiert eine Suchergebnisgruppe, z.B. „mainline“.

|NAME|Wert|Type|  
|-------------|-----------------|----------|
|items|Eine Liste der Suchergebnisse, die in der Gruppe angezeigt werden sollen.|RankingItem|

### <a name="rankingitem"></a>RankingItem
Definiert ein anzuzeigendes Suchergebniselement.

|NAME|Wert|Type|  
|-------------|-----------------|----------|
|resultIndex|Ein nullbasierter Index des Elements in der Antwort, das angezeigt werden soll. Wenn das Element dieses Feld nicht enthält, werden alle Elemente in der Antwort angezeigt. Beispielsweise werden alle Artikel in der News-Antwort angezeigt.|Integer|
|answerType|Die Antwort, die das anzuzeigende Element enthält. Beispiel: News.<br /><br />Verwenden Sie den Typ, um nach der Antwort im SearchResponse-Objekt zu suchen. Der Typ ist der Name eines SearchResponse-Felds.<br /><br /> Verwenden Sie diesen Antworttyp jedoch nur, wenn dieses Objekt das value-Feld enthält. Ignorieren Sie ihn andernfalls.|string|
|textualIndex|Der Index der Antwort in textualAnswers, die angezeigt werden soll.| Ganze Zahl ohne Vorzeichen|
|value|Die ID, die eine anzuzeigende Antwort oder ein anzuzeigendes Element einer Antwort identifiziert. Wenn die ID eine Antwort identifiziert, werden alle Elemente der Antwort angezeigt.|Identifiable|

### <a name="rankingresponse"></a>RankingResponse  
Definiert, wo auf der Suchergebnisseite Inhalt platziert werden soll und in welcher Reihenfolge.  
  
|NAME|Wert|  
|----------|-----------|  
|<a name="ranking-mainline" />mainline|Die Suchergebnisse, die in der Hauptlinie angezeigt werden sollen.|  
|<a name="ranking-pole" />pole|Die Suchergebnisse, die am sichtbarsten sein sollen (die z.B. über der Hauptlinie und der Randleiste angezeigt werden).|  
|<a name="ranking-sidebar" />sidebar|Die Suchergebnisse, die in der Randleiste angezeigt werden sollen.| 


### <a name="searchresponse"></a>SearchResponse  
Definiert das Objekt auf oberster Ebene, das die Antwort enthält, wenn die Anforderung erfolgreich ist.  
  
Beachten Sie Folgendes: Wenn der Dienst einen Denial-of-Service-Angriff vermutet, ist die Anforderung erfolgreich (HTTP-Statuscode: 200 OK). Der Antworttext ist jedoch leer.  
  
|NAME|Wert|Type|  
|----------|-----------|----------|  
|_type|Der Typhinweis, der auf SearchResponse festgelegt wird.|string|  
|WebPage|Ein JSON-Objekt, das die Vorschau definiert.|Zeichenfolge|  
  
  
### <a name="textattribution"></a>TextAttribution  
Definiert eine vertragliche Regel für die Nur-Text-Zuordnung.  
  
|NAME|Wert|Type|  
|----------|-----------|----------|  
|_type|Ein Typhinweis, der auf TextAttribution festgelegt wird.|string|  
|text|Der Zuordnungstext.<br /><br /> Die Textzuordnung gilt für die Entität als Ganzes und sollte unmittelbar nach der Entitätspräsentation angezeigt werden. Wenn es mehrere Text- oder Linkzuordnungsregeln gibt, die kein Ziel angeben, sollten Sie diese verketten und mithilfe einer „Data from:“-Bezeichnung anzeigen.|string| 


## <a name="error-codes"></a>Fehlercodes

Im Folgenden finden Sie die möglichen HTTP-Statuscodes, die eine Anforderung zurückgeben kann.  
  
|Statuscode|BESCHREIBUNG|  
|-----------------|-----------------|  
|200|Erfolgreich.|  
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
- [C#-Schnellstart](c-sharp-quickstart.md)
- [Java-Schnellstart](java-quickstart.md)
- [Node-Schnellstart](node-quickstart.md)
- [Python-Schnellstart](python-quickstart.md)


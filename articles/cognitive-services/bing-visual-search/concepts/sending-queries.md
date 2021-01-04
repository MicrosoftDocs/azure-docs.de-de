---
title: Senden von Suchabfragen an die API für die visuelle Bing-Suche
titleSuffix: Azure Cognitive Services
description: In diesem Artikel werden die Parameter und Attribute von Anforderungen beschrieben, die an die API für die visuelle Bing-Suche gesendet werden. Außerdem finden Sie hier Informationen zum Antwortobjekt.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: aahi
ms.openlocfilehash: 37d9352b6384ee2b5e95903f35d531bd672b25b1
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490974"
---
# <a name="sending-search-queries-to-the-bing-visual-search-api"></a>Senden von Suchabfragen an die API für die visuelle Bing-Suche

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

In diesem Artikel werden die Parameter und Attribute von Anforderungen beschrieben, die an die API für die visuelle Bing-Suche gesendet werden. Außerdem finden Sie hier Informationen zum Antwortobjekt. 

Erkenntnisse zu Bildern können auf drei Arten gewonnen werden:

- Verwenden des Auswertungstokens zu einem Bild aus einem früheren Aufruf eines der Endpunkte der [Bing-Bildersuche-API](/rest/api/cognitiveservices/bing-images-api-v7-reference)
- Senden der URL eines Bilds
- Hochladen eines Bilds (im Binärformat)

## <a name="bing-visual-search-requests"></a>Anforderungen der visuellen Bing-Suche

Wenn Sie der visuellen Bing-Suche ein Bildtoken oder eine URL senden, zeigt der folgende Codeausschnitt das JSON-Objekt an, das Sie in den Text der POST-Anforderung einbeziehen müssen:

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

Das `imageInfo`-Objekt muss entweder das `url`-Feld oder das `imageInsightsToken`-Feld beinhalten, jedoch nicht beide gleichzeitig. Legen Sie im `url`-Feld die URL eines Bildes fest, das über das Internet zugänglich ist. Die maximal unterstützte Bildgröße beträgt 1 MB.

Legen Sie bei `imageInsightsToken` ein Auswertungstoken fest. Rufen Sie zum Abrufen eines Auswertungstokens die „Bing Images API (API für Bing-Bilder)“ auf. Die Antwort enthält eine Liste mit `Image`-Objekten. Jedes `Image`-Objekt enthält ein `imageInsightsToken`-Feld mit dem Token.

Das Feld `cropArea` ist optional. Mit dem Zuschneidebereich können Sie die linke obere Ecke und die rechte untere Ecke eines relevanten Bereichs angeben. Legen Sie dabei Werte zwischen 0,0 und 1,0 fest. Damit geben Sie den Prozentsatz der gesamten Breite oder Höhe an. Im vorherigen Beispiel wird etwa die rechte Hälfte des Bildes als relevanter Bereich markiert. Soll die Anforderung auf den relevanten Bereich beschränkt sein, fügen Sie Werte in dieses Feld ein.

Das `filters`-Objekt enthält einen Sitefilter (das `site`-Feld). Damit können Sie die Ergebnisse für ähnliche Bilder und ähnliche Produkte auf eine bestimmte Domäne eingrenzen. Zeigt das Bild beispielsweise ein Surface Book, können Sie `site` auf `www.microsoft.com` festlegen.

Wenn Sie Informationen zu der lokalen Kopie eines Bildes erhalten möchten, laden Sie das Bild in Binärform hoch.

Einzelheiten zum Einfügen dieser Optionen in den Text einer POST-Anforderung finden Sie unter [Inhaltsformulartypen](#content-form-types).

### <a name="search-endpoint"></a>Endpunkt für die Suche

Der Endpunkt der visuellen Suche lautet: „https:\/\/api.cognitive.microsoft.com/bing/v7.0/images/visualsearch“.

Anforderungen müssen als HTTP-POST-Anforderungen gesendet werden.

### <a name="query-parameters"></a>Abfrageparameter

Die folgenden Abfrageparameter sollten in der Anforderung angegeben werden. Fügen Sie auf jeden Fall zumindest den `mkt`-Abfrageparameter ein:

| Name | Wert | type | Erforderlich |
| --- | --- | --- | --- |
| <a name="cc"></a>cc  | Ein zweistelliger Ländercode, der angibt, woher die Ergebnisse stammen.<br /><br /> Wenn Sie diesen Parameter festlegen, müssen Sie auch den Header [Accept-Language](#acceptlanguage) angeben. Bing verwendet die erste unterstützte Sprache aus der Liste der Sprachen und kombiniert sie mit dem Ländercode, den Sie für den Markt angeben, aus dem Ergebnisse zurückgegeben werden sollen. Enthält die Liste der Sprachen keine unterstützte Sprache, sucht Bing die nächstgelegene Sprache und den nächstgelegenen Markt, die die Anforderung unterstützen. Alternativ dazu wird anstatt des angegebenen Marktes ein aggregierter oder Standardmarkt für die Ergebnisse verwendet.<br /><br /> Verwenden Sie diesen und den `Accept-Language`-Abfrageparameter nur, wenn Sie mehrere Sprachen angeben. Andernfalls sollten Sie die Abfrageparameter `mkt` und `setLang` verwenden.<br /><br /> Dieser Parameter und der Abfrageparameter [mkt](#mkt) schließen sich gegenseitig aus. Geben Sie daher nicht beide an. | String | Nein       |
| <a name="mkt"></a>mkt   | Der Markt, aus dem die Ergebnisse stammen. <br /><br /> **HINWEIS:** Sie sollten immer den Markt angeben, falls bekannt. Die Angabe des Marktes ermöglicht Bing, die Anforderung weiterzuleiten und eine geeignete und optimale Antwort zurückzugeben.<br /><br /> Dieser Parameter und der Abfrageparameter [cc](#cc) schließen sich gegenseitig aus. Geben Sie daher nicht beide an. | String | Ja      |
| <a name="safesearch"></a>safeSearch | Ein Filter für nicht jugendfreie Inhalte. Die folgenden Filterwerte (ohne Beachtung von Groß-/Kleinschreibung) sind möglich.<br /><ul><li>Off: Es werden Webseiten mit nicht jugendfreiem Text oder Bildern zurückgegeben.<br /><br/></li><li>Moderate: Webseiten mit nicht jugendfreiem Text werden zurückgegeben, nicht jugendfreie Bilder jedoch nicht.<br /><br/></li><li>Strict: Es werden keine Webseiten mit nicht jugendfreiem Text oder Bildern zurückgegeben.</li></ul><br /> Die Standardeinstellung ist „Moderate“.<br /><br /> **HINWEIS:** Stammt die Anforderung aus einem Markt, für den laut Bing-Richtlinien zu nicht jugendfreien Inhalten für `safeSearch` die Einstellung „Strict“ erforderlich ist, ignoriert Bing den `safeSearch`-Wert und verwendet stattdessen „Strict“.<br/><br/>**HINWEIS:** Bei Verwendung des Abfrageoperators `site:` kann es vorkommen, dass die Antwort unabhängig von der Einstellung des `safeSearch`-Abfrageparameters nicht jugendfreie Inhalte enthält. Verwenden Sie `site:` nur, wenn Sie wissen, welche Inhalte die Website enthält, und wenn in Ihrem Szenario ggf. auch nicht jugendfreie Inhalte zulässig sind.  | String | Nein       |
| <a name="setlang"></a>setLang  | Die Sprache, die für Zeichenfolgen der Benutzeroberfläche verwendet werden soll. Geben Sie die Sprache mithilfe des zweistelligen Sprachcodes nach ISO 639-1 an. Der Sprachcode für Englisch lautet z.B. „EN“. Der Standardwert ist „EN“ (Englisch).<br /><br /> Auch wenn dies optional ist, sollten Sie immer eine Sprache angeben. In der Regel wird bei `setLang` dieselbe Sprache angegeben wie bei `mkt`, sofern der Benutzer die Zeichenfolgen der Benutzeroberfläche nicht in einer anderen Sprache anzeigen möchte.<br /><br /> Dieser Parameter und der Header [Accept-Language](#acceptlanguage) schließen sich gegenseitig aus. Geben Sie daher nicht beide an.<br /><br /> Eine Zeichenfolge der Benutzeroberfläche ist eine Zeichenfolge, die als Bezeichnung in einer Benutzeroberfläche verwendet wird. Die JSON-Antwortobjekte enthalten nur wenige Zeichenfolgen für Benutzeroberflächen. Die angegebene Sprache wird auch in Links zu Eigenschaften von „bing.com“ in den Antwortobjekten verwendet. | String | Nein   |

## <a name="headers"></a>Header

Die folgenden Header sollten in der Anforderung angegeben werden. Die Header `Content-Type` und `Ocp-Apim-Subscription-Key` sind die einzigen erforderlichen Header, aber Sie sollten auch `User-Agent`, `X-MSEdge-ClientID`, `X-MSEdge-ClientIP` und `X-Search-Location` hinzufügen.

| Header | BESCHREIBUNG |
| --- | --- |
| <a name="acceptlanguage"></a>Accept-Language  | Optionaler Anforderungsheader.<br /><br /> Eine durch Kommas getrennte Liste mit Sprachen, die für Zeichenfolgen der Benutzeroberfläche verwendet werden sollen. Die Liste ist absteigend nach Präferenz sortiert. Weitere Informationen hierzu (sowie zum erwarteten Format) finden Sie unter [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Dieser Header und der Abfrageparameter [setLang](#setlang) schließen sich gegenseitig aus. Geben Sie daher nicht beide an.<br /><br /> Wenn Sie diesen Header festlegen, müssen Sie auch den Abfrageparameter [cc](#cc) angeben. Bing verwendet die erste unterstützte Sprache aus der Liste, um den Markt zu bestimmen, für den Ergebnisse zurückgegeben werden sollen, und kombiniert sie mit dem Parameterwert `cc`. Enthält die Liste keine unterstützte Sprache, sucht Bing die nächstgelegene Sprache und den nächstgelegenen Markt, die bzw. der die Anforderung unterstützt. Alternativ dazu wird für die Ergebnisse ein aggregierter oder Standardmarkt verwendet. Wenn Sie sehen möchten, welchen Markt Bing verwendet hat, untersuchen Sie den Header `BingAPIs-Market`.<br /><br /> Verwenden Sie diesen Header und den `cc`-Abfrageparameter nur, wenn Sie mehrere Sprachen angeben. Verwenden Sie andernfalls die Abfrageparameter [mkt](#mkt) und [setLang](#setlang).<br /><br /> Eine Zeichenfolge der Benutzeroberfläche ist eine Zeichenfolge, die als Bezeichnung in einer Benutzeroberfläche verwendet wird. Die JSON-Antwortobjekte enthalten nur wenige Zeichenfolgen für Benutzeroberflächen. Die Links zu Eigenschaften von „bing.com“ in den Antwortobjekten verwenden die angegebene Sprache.  |
| <a name="contenttype"></a>Content-Type  |     |
| <a name="market"></a>BingAPIs-Market    | Antwortheader.<br /><br /> Der von der Anforderung verwendete Markt. Das Format lautet \<languageCode\>-\<countryCode\>. Beispiel: en-US.  |
| <a name="traceid"></a>BingAPIs-TraceId  | Antwortheader.<br /><br /> Die ID des Protokolleintrags, der die Details der Anforderung enthält. Erfassen Sie diese ID, wenn ein Fehler auftritt. Wenn Sie das Problem nicht ermitteln und beheben können, übermitteln Sie diese ID und weitere Informationen an das Supportteam. |
| <a name="subscriptionkey"></a>Ocp-Apim-Subscription-Key | Erforderlicher Anforderungsheader.<br /><br /> Der Abonnementschlüssel, den Sie bei der Registrierung für diesen Dienst in [Cognitive Services](https://www.microsoft.com/cognitive-services/) erhalten haben. |
| <a name="pragma"></a>Pragma |   |
| <a name="useragent"></a>User-Agent  | Optionaler Anforderungsheader.<br /><br /> Der Benutzer-Agent, von dem die Anforderung stammt. Bing verwendet den Benutzer-Agent, um die Erfahrung mobiler Benutzer zu optimieren. Auch wenn dies optional ist, sollten Sie diesen Header immer angeben.<br /><br /> Der Benutzer-Agent sollte der Zeichenfolge entsprechen, die alle häufig verwendeten Browser senden. Informationen zu Benutzer-Agents finden Sie in [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Nachfolgend einige Beispiele für Zeichenfolgen für Benutzer-Agents.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (kompatibel; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-US; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; wie Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 wie Mac OS X) AppleWebKit/536.26 (KHTML; wie Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) wie Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 wie Mac OS X) AppleWebKit/537.51.1 (KHTML, wie Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>      |
| <a name="clientid"></a>X-MSEdge-ClientID  | Optionaler Anforderungs- und Antwortheader.<br /><br /> Bing verwendet diesen Header, um Benutzern beim Aufrufen der Bing-API ein konsistentes Verhalten bereitzustellen. Bing testet häufig neue Funktionen und Verbesserungen und verwendet dabei die Client-ID als Schlüssel für die Zuweisung von Datenverkehr an verschiedene Flights. Wenn Sie für einen Benutzer bei unterschiedlichen Anforderungen nicht dieselbe Client-ID verwenden, weist Bing den Benutzer möglicherweise mehreren widersprüchlichen Flights zu. Die Zuweisung zu mehreren widersprüchlichen Flights kann zu einer inkonsistenten Benutzererfahrung führen. Weist die zweite Anforderung beispielsweise eine andere Flight-Zuweisung als die erste auf, kann dies zu einer unerwarteten Benutzererfahrung führen. Außerdem kann Bing die Client-ID zur Anpassung der Webergebnisse an den Suchverlauf dieser Client-ID verwenden. Dies führt zu einer noch besseren Benutzererfahrung.<br /><br /> Bing verwendet den Header auch, um die Rangfolge der Ergebnisse zu verbessern, indem es die Aktivität der Client-ID analysiert. Durch die verbesserte Relevanz erhöht sich die Qualität der von Bing-APIs bereitgestellten Ergebnisse, was wiederum zu höheren Durchklickraten für den API-Consumer führt.<br /><br /> **WICHTIG:** Auch wenn er optional ist, sollten Sie diesen Header als erforderlich betrachten. Das Beibehalten der Client-ID für dieselbe Kombination aus Benutzer und Gerät über mehrere Anforderungen hinweg ermöglicht 1) dem API-Consumer eine konsistente Benutzererfahrung sowie 2) höhere Durchklickraten durch eine bessere Qualität der Bing-API-Ergebnisse.<br /><br /> Die folgenden Grundregeln gelten bei der Verwendung dieses Headers.<br /><ul><li>Jeder Benutzer, der die Anwendung auf dem Gerät verwendet, muss über eine eindeutige, von Bing generierte Client-ID verfügen.<br /><br/>Wenn Sie diesen Header nicht in die Anforderung einfügen, generiert Bing eine ID, die im Antwortheader „X-MSEdge-ClientID“ zurückgegeben wird. Der einzige Zeitpunkt, zu dem dieser Header NICHT in eine Anforderung eingeschlossen werden sollte, ist bei der erstmaligen Verwendung der App auf dem Gerät.<br /><br/></li><li>**ACHTUNG:** Vergewissern Sie sich, dass die Client-ID nicht mit authentifizierten Benutzerkontoinformationen verknüpft werden kann.</li><li>Verwenden Sie die Client-ID für jede Anforderung der Bing-API, die die App für diesen Benutzer auf dem Gerät durchführt.<br /><br/></li><li>Behalten Sie die Client-ID bei. Verwenden Sie ein permanentes HTTP-Cookie, um sicherzustellen, dass die ID in einer Browser-App bei allen Sitzungen verwendet wird. Verwenden Sie kein Sitzungscookie. Verwenden Sie für andere Apps wie z.B. mobile Apps den permanenten Speicher des Geräts, um die ID beizubehalten.<br /><br/>Rufen Sie bei der nächsten Verwendung der App auf dem Gerät durch den Benutzer die gespeicherte Client-ID ab.</li></ul><br /> **HINWEIS:** Bing-Antworten können diesen Header enthalten, müssen es aber nicht. Enthält die Antwort diesen Header, erfassen Sie die Client-ID, und verwenden Sie sie für alle nachfolgenden Bing-Anforderungen des Benutzers auf diesem Gerät.<br /><br /> **HINWEIS:** Wenn Sie den Header „X-MSEdge-ClientID“ einfügen, dürfen Sie in die Anforderung nicht gleichzeitig Cookies einschließen. |
| <a name="clientip"></a>X-MSEdge-ClientIP   | Optionaler Anforderungsheader.<br /><br /> Die IPv4- oder IPv6-Adresse des Clientgeräts. Die IP-Adresse wird verwendet, um den Standort des Benutzers zu ermitteln. Bing verwendet die Standortinformationen für ein sicheres Suchverhalten.<br /><br /> **HINWEIS:** Auch wenn dies optional ist, sollten Sie diesen Header und den Header „X-Search-Location“ immer angeben.<br /><br /> Verschleiern Sie nicht die Adresse (z.B. durch Ändern des letzten Oktetts in 0). Durch Verschleiern der Adresse stimmen Ihr Standort und der tatsächliche Gerätestandort nicht überein, wodurch Bing möglicherweise fehlerhafte Ergebnisse anzeigt. |
| <a name="location"></a>X-Search-Location   | Optionaler Anforderungsheader.<br /><br /> Eine durch Semikolons getrennte Liste mit Schlüssel/Wert-Paaren, die den geografischen Standort des Clients beschreiben. Bing verwendet die Standortinformationen für ein sicheres Suchverhalten und zur Rückgabe von lokalen relevanten Inhalten. Geben Sie das Schlüssel-Wert-Paar im Format „\<key\>:\<value\>“ an. Verwenden Sie die folgenden Schlüssel zur Angabe des Benutzerstandorts.<br /><br /><ul><li>lat (erforderlich): Der Breitengrad des Clientstandorts (in Grad). Der Breitengrad muss größer als oder gleich -90,0 und kleiner als oder gleich +90,0 sein. Negative Werte geben südliche Breitengrade und positive Werte nördliche Breitengrade an.<br /><br /></li><li>long (erforderlich): Der Längengrad des Clientstandorts (in Grad). Der Längengrad muss größer als oder gleich -180,0 und kleiner als oder gleich +180,0 sein. Negative Werte geben westliche Längengrade und positive Werte östliche Längengrade an.<br /><br /></li><li>re (erforderlich): Der Radius (in Metern), der die horizontale Genauigkeit der Koordinaten angibt. Übergeben Sie den Wert, der vom Standortdienst des Geräts zurückgegeben wird. Typische Werte sind 22 m für GPS/WLAN, 380 m für die Funkmasttriangulation und 18.000 m für die umgekehrte IP-Suche.<br /><br /></li><li>ts (optional): Der UTC-Unixzeitstempel des Zeitpunkts, zu dem sich der Client am Standort befand. (Der UNIX-Zeitstempel gibt die Anzahl der Sekunden seit dem 1. Januar 1970 an.)<br /><br /></li><li>head: Optional. Relatives Ziel oder Reiserichtung des Clients. Geben Sie die Reiserichtung von 0 bis 360 (in Grad) im Uhrzeigersinn ausgehend vom geografischen Norden an. Geben Sie diesen Schlüssel nur an, wenn der `sp`-Schlüssel ungleich 0 ist.<br /><br /></li><li>sp (optional): Die horizontale Geschwindigkeit (in Metern pro Sekunde), mit der das Clientgerät unterwegs ist.<br /><br /></li><li>alt (optional): Die Höhe des Clientgeräts (in Metern).<br /><br /></li><li>are: Optional. Der Radius (in Metern), der die vertikale Genauigkeit der Koordinaten angibt. Geben Sie diesen Schlüssel nur an, wenn Sie auch den `alt`-Schlüssel angeben.<br /><br /></li></ul> **HINWEIS:** Auch wenn viele der Schlüssel optional sind, werden die Standortergebnisse immer genauer, je mehr Informationen Sie bereitstellen.<br /><br /> **HINWEIS:** Auch wenn dies optional ist, sollten Sie den geografischen Standort des Benutzers immer angeben. Besonders wichtig ist die Standortangabe dann, wenn die IP-Adresse des Clients den physischen Standort des Benutzers nicht exakt wiedergibt (wenn der Client beispielsweise VPN verwendet). Für optimale Ergebnisse sollten Sie diesen Header und den Header `X-MSEdge-ClientIP` einfügen. Auf jeden Fall sollten Sie aber zumindest diesen Header verwenden.       |

> [!NOTE]
> Beachten Sie, dass die [Anforderungen an die Verwendung und Anzeige der Bing-Suche-API](../../bing-web-search/use-display-requirements.md) die Einhaltung aller geltenden Gesetze erfordern. Dies gilt auch für die Verwendung dieses Headers. Die Rechtsprechung in bestimmten Ländern (z.B. in Europa) erfordert etwa die Zustimmung des Benutzers, um bestimmte Tracking-Geräte auf Benutzergeräten platzieren zu dürfen.

<a name="content-form-types"></a>

### <a name="content-form-types"></a>Inhaltsformulartypen

Jede Anforderung muss den Header `Content-Type` enthalten. Der Header muss auf `multipart/form-data; boundary=\<boundary string\>` festgelegt sein, wobei \<boundary string\> eine eindeutige, nicht transparente Zeichenfolge darstellt, die die Grenze der Formulardaten angibt. Beispiel: `boundary=boundary_1234-abcd`.

Wenn Sie der visuellen Bing-Suche ein Bildtoken oder eine URL senden, zeigt der folgende Codeausschnitt die Formulardaten an, die Sie in den Text der POST-Anforderung einfügen müssen. Die Formulardaten müssen den `Content-Disposition`-Header enthalten. Außerdem müssen Sie den Parameter `name` auf „knowledgeRequest“ festlegen. Einzelheiten zum `imageInfo`-Objekt finden Sie unter der Anforderung.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "url" : "https://contoso.com/2018/05/fashion/red.jpg"
    }
}

--boundary_1234-abcd--
```

Sie können optional das Attribut `enableEntityData` im Header auf `true` festlegen, um ausführliche Informationen zur Hauptentität im von Ihnen hochgeladenen Bild zu erhalten – einschließlich Weblinks und Zuordnungsinformationen. Dieses Feld ist standardmäßig auf `false` festgelegt.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
  "imageInfo" : {
      "url" : "https://contoso.com/2018/05/fashion/red.jpg"
  },
  "knowledgeRequest" : {
    "invokedSkillsRequestData" : {
        "enableEntityData" : "true"
    }
  }
}

--boundary_1234-abcd--
```

Wenn Sie ein lokales Bild hochladen, zeigt der folgende Codeausschnitt die Formulardaten an, die Sie in den Text der POST-Anforderung einfügen müssen. Die Formulardaten müssen den Header `Content-Disposition` enthalten. Der `name`-Parameter muss auf „image“ festgelegt werden. Der `filename`-Parameter kann auf eine beliebige Zeichenfolge festgelegt werden. Der Header `Content-Type` kann auf einen beliebigen gängigen Bild-MIME-Typ festgelegt werden. Der Inhalt des Formulars sind die Binärdaten des Bilds. Sie können eine maximale Bildgröße von 1 MB hochladen. Die Breite oder Höhe darf maximal 1.500 Pixel betragen.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
Content-Type: image/jpeg

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Der folgende Codeausschnitt zeigt, wie der relevante Bereich eines hochgeladenen Bildes angegeben wird:

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "cropArea" : {
            "top" : 0.2,
            "left" : 0.3,
            "bottom" : 0.7,
            "right" : 0.6
        }
    }
}

--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="image"
Content-Type: image/jpeg


ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

### <a name="example-request"></a>Beispielanforderung

Der folgende Codeausschnitt zeigt eine vollständige Anforderung für Bildauswertungen, bei der ein Bildtoken und ein relevanter Bereich übergeben werden. Das Auswertungstoken stammt aus einem früheren Aufruf von „/images/search“:

```  
POST https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=en-us HTTP/1.1  
Content-Type: multipart/form-data; boundary=boundary_1234-abcd
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com 

--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.2,
            "bottom" : 0.7,
            "right" : 0.5
        }
    }
}

--boundary_1234-abcd--
```

## <a name="bing-visual-search-responses"></a>Antworten der visuellen Bing-Suche


[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

Sind Informationen zu dem Bild verfügbar, enthält die Antwort ein oder mehrere `tags` mit den Informationen. Das `image`-Feld enthält das Auswertungstoken für das Eingabebild.

```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {...},
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

Das `tags`-Feld enthält einen Anzeigenamen und eine Liste mit Aktionen (Informationen). Eines der Tags enthält ein `displayName`-Feld, das auf eine leere Zeichenfolge festgelegt ist. Dieses Tag enthält die Standardinformationen, wie z.B. Webseiten mit dem Bild, visuell ähnliche Bilder und Einkaufsquellen für abgebildete Elemente. Da das gesamte Bild relevant ist, enthält das Standardinformationstag keine Begrenzungsrahmen für relevante Bereiche:

```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {
      "displayName" : "",
      "actions" : [
        {...},
        {...},
        {...},
        {...}
      ]
    },
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

Eine Liste mit den Standardinformationen finden Sie unter [Standardinformationstag](../default-insights-tag.md).

Die verbleibenden Tags enthalten weitere Informationen, die für den Benutzer möglicherweise relevant sind. Enthält ein Bild beispielsweise Text, kann ein Tag etwa eine „TextResults“-Information mit dem erkannten Text beinhalten. Anderes Beispiel: Wenn Bing auf dem Bild eine Entität (also eine bekannte/beliebte Person oder einen entsprechenden Ort oder Gegenstand) erkennt, kann eines der Tags etwa die Entität identifizieren. Bei der visuellen Suche werden zudem verschiedene Begriffe (Tags) zurückgegeben, die vom Eingabebild abgeleitet werden. Anhand dieser Tags können Benutzer Konzepte erkunden, die in dem Bild gefunden wurden. Zeigt das Bild beispielsweise einen bekannten Sportler, kann eines der Tags etwa „Sport“ lauten und Links zu Sportbildern enthalten.

Jedes Tag enthält einen Anzeigenamen, mit dem Sie die Information kategorisieren können, einen Begrenzungsrahmen für den relevanten Bereich, auf den sich die Information bezieht, die Informationen selbst sowie eine Miniaturansicht des Bildes. Zeigt das Bild beispielsweise eine Person in einem Sporttrikot, könnte eines der Tags etwa einen Begrenzungsrahmen um das Trikot sowie die Informationen „VisualSearch“ und „ProductVisualSearch“ enthalten. Ein weiteres Tag enthält möglicherweise eine „ImageResults“-Information, die eine URL für eine API-Anforderung zu „/images/search“ beinhaltet, um thematisch verknüpfte Bilder abzurufen. Oder die Information enthält eine URL zu den Ergebnissen einer Bildersuche auf „bing.com“.

Alle weiteren Tags, die nicht das Standardinformationstag darstellen, enthalten Begrenzungsrahmen für relevante Bereiche auf dem Bild. Zeigt das Bild beispielsweise mehrere erkannte Personen, können die Tags Begrenzungsrahmen für jede einzelne Person enthalten. Bei einem Bild mit erkannten Kleidungsstücken können die Tags wiederum Begrenzungsrahmen für jedes erkannte Kleidungsstück enthalten. Sie können die Begrenzungsrahmen zum Erstellen von Hotspots auf dem Bild verwenden, die beim Klicken Einzelheiten zu den Inhalten in diesem Bildbereich bereitstellen. Sie sollten in einem Bild jedoch keine Hotspots für Begrenzungsrahmen einfügen, die das gesamte Bild identifizieren.

### <a name="text-recognition"></a>Texterkennung

Enthält das Bild Text, der vom Dienst erkannt wird, beinhaltet eines der Tags eine „TextResults“-Information (Aktion). Der `displayName` der Information enthält den erkannten Text.

```json
    {
        "image" : {
            "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23Text..."
        },
        "displayName" : "##TextRecognition",
        "boundingBox" : {
            "queryRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            },
            "displayRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            }
        },
        "actions" : [{
            "displayName" : "WALK BIKE ACROSS BRIDGE",
            "actionType" : "TextResults"
        }],
        "sources" : ["OCR"]
    }
```

Da das `displayName`-Feld des Tags den Inhalt „##TextRecognition“ enthält, verwenden Sie den Begriff nicht als Kategorietitel in der Benutzeroberfläche. Dasselbe gilt für alle Anzeigenamen, die mit „##“ beginnen. Verwenden Sie stattdessen den Anzeigenamen der Aktion.

Mit der Texterkennung können auch die Kontaktinformationen auf Visitenkarten wie etwa Telefonnummern und E-Mail-Adressen erkannt werden. Der Begrenzungsrahmen identifiziert den Ort der Kontaktinformationen auf der Karte.

```json
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        }
      },
      "actions" : [
        {
          "url" : "tel:888%20555%201212",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        }
      },
      "actions" : [
        {
          "url" : "mailto:someone@outlook.com",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        }
      },
      "actions" : [
        {
          "displayName" : "CHARLENE WHITNEY Graphic Designer 888 555 1212 someone@outlook.com www.contoso.com",
          "actionType" : "TextResults"
        }
      ],
      "sources" : ["OCR"]
    }
```

Enthält das Bild eine erkannte Entität (beispielsweise eine bekannte/beliebte Person oder einen entsprechenden Ort oder Gegenstand), kann eines der Tags eine Entitätsinformation beinhalten. Die Felder `mainEntity` und `data` sind nur verfügbar, wenn das Attribut `enableEntityData` im Header `Content-Type` auf `true` festgelegt ist.

```json
{
  "image" : {
    "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Statue+of+Liberty..."
  },
  "displayName" : "Statue of Liberty",
  "boundingBox" : {
    "queryRectangle" : {
      "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
      "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
      "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
      "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
    },
    "displayRectangle" : {
      "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
      "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
      "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
      "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
    }
  },
  "actions" : [
    {
      "_type" : "ImageEntityAction",
      "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Statue+of+Liberty",
      "displayName" : "Statue of Liberty",
      "actionType" : "Entity",
      "mainEntity" : {
        "name" = "Statue of liberty",
        "bingId" : "..."
      },
      "data" : {
        "id" : "https://api.cognitive.microsoft.com/api/v7/entities/...",
        "readLink": "https://www.bingapis.com/api/v7/search?q=...",
        "readLinkPingSuffix": "...",
        "contractualRules": [
          {
            "_type": "ContractualRules/LicenseAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "license": {
                "name": "CC-BY-SA",
                "url": "http://creativecommons.org/licenses/by-sa/3.0/",
                "urlPingSuffix": "..."
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "http://en.wikipedia.org/wiki/...",
            "urlPingSuffix": "..."
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q=...",
        "webSearchUrlPingSuffix": "...",
        "name": "Statue of Liberty",
        "image": {
          "thumbnailUrl": "https://tse1.mm.bing.net/th?id=...",
          "hostPageUrl": "http://upload.wikimedia.org/wikipedia/...",
          "hostPageUrlPingSuffix": "...",
          "width": 50,
          "height": 50,
          "sourceWidth": 474,
          "sourceHeight": 598
        },
        "description" : "...",
        "bingId": "..."
        }
      }
  ]
}
```

## <a name="see-also"></a>Weitere Informationen

- [Was ist die API für die Visuelle Bing-Suche?](../overview.md)
- [Tutorial: Erstellen einer Single-Page-Web-App für die Visuelle Suche](../tutorial-bing-visual-search-single-page-app.md)
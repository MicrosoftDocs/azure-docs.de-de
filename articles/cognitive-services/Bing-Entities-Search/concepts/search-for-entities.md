---
title: Suchen nach Entitäten mit der Bing-Entitätssuche-API
titleSuffix: Azure Cognitive Services
description: Verwenden Sie die Bing-Entitätssuche-API zum Extrahieren und Suchen von Entitäten und Orten in Suchabfragen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 9dabceda17defb24f2a916cd641f625feb551c6a
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353288"
---
# <a name="searching-for-entities-with-the-bing-entity-api"></a>Suche nach Entitäten mit der Bing-Entitäts-API

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

## <a name="suggest-search-terms-with-the-bing-autosuggest-api"></a>Vorschlagen von Suchbegriffen mit der Bing-Vorschlagssuche-API

Wenn Sie ein Suchfeld bereitstellen, in das Benutzer ihre Suchbegriffe eingeben, verwenden Sie die [Bing-Vorschlagssuche-API](../../bing-autosuggest/get-suggested-search-terms.md), um die Benutzerfreundlichkeit zu verbessern. Die API gibt vorgeschlagene Abfragezeichenfolgen zurück, während der Benutzer einen Suchbegriff eingibt.

Codieren Sie den vom Benutzer eingegebenen Suchbegriff als URL, bevor Sie den Abfrageparameter [q](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query) festlegen. Wenn der Benutzer z.B. *Marcus Appel* eingibt, legen Sie `q` auf *Marcus+Appel* oder *Marcus%20Appel* fest.

Wenn der Suchbegriff einen Rechtschreibfehler enthält, umfasst die Suchantwort ein [QueryContext](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#querycontext)-Objekt. Das Objekt zeigt die ursprüngliche Schreibweise und die korrigierte Schreibweise, die Bing für die Suche verwendet hat.

```json
"queryContext": {
    "originalQuery": "hollo wrld",
    "alteredQuery": "hello world",
    "alterationOverrideQuery": "+hollo wrld",
    "adultIntent": false
}
```

## <a name="the-bing-entity-search-api-response"></a>API-Antwort für die Bing-Entitätssuche

Die API-Antwort enthält ein [SearchResponse](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#searchresponse)-Objekt. Wenn Bing eine relevante Entität oder einen relevanten Ort findet, enthält das Objekt das `entities`-Feld, das `places`-Feld oder beide Felder. Andernfalls enthält das Antwortobjekt keins der Felder.
> [!NOTE]
> Antworten für Entitäten unterstützen verschiedene Märkte, aber die Antworten für Orte unterstützen nur Standorte von US-Unternehmen. 

Das `entities`-Feld ist ein [EntityAnswer](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)-Objekt, das eine Liste von [Entity](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity)-Objekten enthält (siehe `value`-Feld). Die Liste kann eine einzelne dominante Entität, mehrere Entitäten zur genaueren Bestimmung oder beides enthalten. 

Eine dominante Entität wird zurückgegeben, wenn Bing sie für die einzige Entität hält, die die Anforderung erfüllt (es gibt keine Mehrdeutigkeit dahingehend, welche Entität die Anforderung erfüllt). Wenn mehrere Entitäten die Anforderung erfüllen könnten, enthält die Liste mehrere Entitäten zur genaueren Bestimmung. Wenn die Anforderung z.B. den generischen Titel eines Franchiseangebots für einen Kinofilm verwendet, enthält die Liste wahrscheinlich Entitäten zur genaueren Bestimmung. Wenn die Anforderung jedoch einen spezifischen Titel aus dem Franchiseangebot angibt, enthält die Liste wahrscheinlich eine einzelne dominante Entität.

Zu Entitäten gehören bekannte Persönlichkeiten wie Sänger, Schauspieler, Sportler, Models usw., Orte und Sehenswürdigkeiten wie Mount Rainier im Nordwesten der USA oder das Lincoln Memorial sowie Dinge wie eine Banane, eine Hunderasse, ein Buchtitel oder der Titel eines Kinofilms. Das Feld [entityPresentationInfo](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entitypresentationinfo) enthält Hinweise, die den Entitätstyp identifizieren. Das Feld weist also darauf hin, ob es sich um eine Person, einen Film, ein Tier oder eine Sehenswürdigkeit handelt. Eine Liste aller möglichen Typen finden Sie unter [Entitätstypen](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "DominantEntity",
    "entityTypeHints": ["Attraction"],
    "entityTypeDisplayHint": "Mountain"
}, ...
```

Das folgende Beispiel zeigt eine Antwort, die eine dominante Entität und eine Entität zur genaueren Bestimmung enthält.

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Mount Rainier"
    },
    "entities": {
        "value": [{
            "contractualRules": [{
                "_type": "ContractualRules/LicenseAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "license": {
                    "name": "CC-BY-SA",
                    "url": "https://creativecommons.org/licenses/by-sa/3.0/"
                },
                "licenseNotice": "Text under CC-BY-SA license"
            },
            {
                "_type": "ContractualRules/LinkAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "text": "contoso.com",
                "url": "http://contoso.com/mount_rainier"
            },
            {
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount-rainier"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier...",
            "name": "Mount Rainier",
            "url": "http://www.northwindtraders.com/",
            "image": {
                "name": "Mount Rainier",
                "thumbnailUrl": "https://www.bing.com/th?id=A4ae343983daa4...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier"
                }],
                "hostPageUrl": "http://contoso.com/commons/7/72/mount_rain...",
                "width": 110,
                "height": 110
            },
            "description": "Mount Rainier is 14,411 ft tall and the highest mountain...",
            "entityPresentationInfo": {
                "entityScenario": "DominantEntity",
                "entityTypeHints": ["Attraction"]
            },
            "bingId": "38b9431e-cf91-93be-0584-c42a3ecbfdc7"
        },
        {
            "contractualRules": [{
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount_rainier_national_park"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier%20National...",
            "name": "Mount Rainier National Park",
            "url": "http://worldwideimporters.com/",
            "image": {
                "name": "Mount Rainier National Park",
                "thumbnailUrl": "https://www.bing.com/th?id=A91bdc5a1b648a695a39...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier_national_park"
                }],
                "hostPageUrl": "http://contoso.com/en/7/7a...",
                "width": 50,
                "height": 50
            },
            "description": "Mount Rainier National Park is a United States National Park...",
            "entityPresentationInfo": {
                "entityScenario": "DisambiguationItem",
                "entityTypeHints": ["Organization"]
            },
            "bingId": "29d4b681-227a-3924-7bb1-8a54e8666b8c"
        }]
    }
}
```

Die Entität enthält die Felder `name`, `description` und `image`. Wenn Sie diese Felder auf der Benutzeroberfläche anzeigen, müssen Sie sie zuordnen. Das Feld `contractualRules` enthält eine Liste von Zuordnungen, die Sie anwenden müssen. Eine vertragliche Regel identifiziert das Feld, auf das die Zuordnung angewendet wird. Weitere Informationen zum Anwenden von Zuordnungen finden unter [Zuordnung](#data-attribution).

```json
"contractualRules": [{
    "_type": "ContractualRules/LicenseAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "license": {
        "name": "CC-BY-SA",
        "url": "https://creativecommons.org/licenses/by-sa/3.0/"
    },
    "licenseNotice": "Text under CC-BY-SA license"
},
{
    "_type": "ContractualRules/LinkAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "text": "contoso.com",
    "url": "http://contoso.com/wiki/Mount_Rainier"
},
{
    "_type": "ContractualRules/MediaAttribution",
    "targetPropertyName": "image",
    "mustBeCloseToContent": true,
    "url": "http://contoso.com/wiki/Mount_Rainier"
}], ...
```

Wenn Sie die Entitätsinformationen anzeigen (Name, Beschreibung und Bild), müssen Sie auch die URL im Feld `webSearchUrl` verwenden, um eine Verknüpfung mit der Seite mit den Bing-Suchergebnissen herzustellen, die die Entität enthält.

## <a name="find-places"></a>Suchen nach Orten

Das `places`-Feld ist ein [LocalEntityAnswer](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)-Objekt, das eine Liste von [Place](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#place)-Objekten enthält (weitere Informationen siehe [Entitätstypen](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types)). Die Liste enthält mindestens eine lokale Entität, die die Anforderung erfüllt.

Orte umfassen Restaurants, Hotels oder lokale Geschäfte. Das Feld [entityPresentationInfo](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entitypresentationinfo) enthält Hinweise, die den Typ der lokalen Entität identifizieren. Die Liste enthält Hinweise wie z.B. „Ort“, „lokales Geschäft“ oder „Restaurant“. Jeder nachfolgende Hinweis im Array grenzt den Entitätstyp weiter ein. Eine Liste aller möglichen Typen finden Sie unter [Entitätstypen](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "ListItem",
    "entityTypeHints": ["Place",
    "LocalBusiness",
    "Restaurant"]
}, ...
```
> [!NOTE]
> Antworten für Entitäten unterstützen verschiedene Märkte, aber die Antworten für Orte unterstützen nur Standorte von US-Unternehmen. 

Standortbezogene Entitätsabfragen wie z.B. *Restaurant in meiner Nähe* erfordern den Standort des Benutzers, um exakte Ergebnisse zu liefern. Ihre Anforderungen sollten immer die Header „X-Search-Location“ und „X-MSEdge-ClientIP“ verwenden, um den Standort des Benutzers anzugeben. Wenn Bing davon ausgeht, dass der Standort des Benutzers nützlich für die Abfrage ist, legt es das `askUserForLocation`-Feld von [QueryContext](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#querycontext) auf **TRUE** fest. 

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Sinful Bakery and Cafe",
        "askUserForLocation": true
    },
    ...
}
```

Das Ergebnis für einen Ort umfasst den Namen, die Adresse, die Telefonnummer des Orts sowie die URL zur Website der Entität. Wenn Sie die Entitätsinformationen anzeigen, müssen Sie auch die URL im Feld `webSearchUrl` verwenden, um eine Verknüpfung mit der Seite mit den Bing-Suchergebnissen herzustellen, die die Entität enthält.

```json
"places": {
    "value": [{
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Sinful%20Bakery...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "http://libertysdelightfulsinfulbakeryandcafe.com/",
        "entityPresentationInfo": {
            "entityScenario": "ListItem",
            "entityTypeHints": ["Place",
            "LocalBusiness",
            "Restaurant"]
        },
        "address": {
            "addressLocality": "Seattle",
            "addressRegion": "WA",
            "postalCode": "98112",
            "addressCountry": "US",
            "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
    }]
}
```

> [!NOTE]
> Weder Sie noch Dritte in Ihrem Namen dürfen Daten aus der Entitäts-API für Tests, Entwicklung, Schulung, Verteilung oder Bereitstellung eines Nicht-Microsoft-Diensts oder einer Nicht-Microsoft-Funktion verwenden, beibehalten, speichern, zwischenspeichern, freigeben oder verteilen.  

## <a name="data-attribution"></a>Datenzuordnung

Antworten der Bing-Entitätssuche-API enthalten Informationen, die Eigentum von Dritten sind. Es ist Ihre Aufgabe sicherzustellen, dass Sie diese in geeigneter Form verwenden, z.B. durch Einhaltung von Creative Commons-Lizenzen, auf denen Ihre Benutzeroberfläche möglicherweise basiert.

Wenn eine Antwort oder ein Ergebnis die Felder `contractualRules`, `attributions` oder `provider` enthält, müssen Sie die Daten zuordnen. Enthält die Antwort keines dieser Felder, ist keine Zuordnung erforderlich. Wenn die Antwort das Feld `contractualRules` sowie die Felder `attributions` und/oder `provider` enthält, müssen Sie die vertraglichen Regeln zum Zuordnen der Daten verwenden.

Das folgende Beispiel zeigt eine Entität, die eine vertragliche MediaAttribution-Regel enthält, sowie ein Bild, das ein `provider`-Feld umfasst. Die MediaAttribution-Regel identifiziert das Bild als Ziel der Regel, sodass Sie das `provider`-Feld des Bilds ignorieren und stattdessen die MediaAttribution-Regel für die Zuordnung verwenden müssen.  

```json
"value": [{
    "contractualRules": [
        ...
        {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://contoso.com/mount_rainier"
        }
    ],
    ...
    "image": {
        "name": "Mount Rainier",
        "thumbnailUrl": "https://www.bing.com/th?id=A46378861201...",
        "provider": [{
            "_type": "Organization",
            "url": "http://contoso.com/mount_rainier"
        }],
        "hostPageUrl": "http://www.graphicdesigninstitute.com/Uploaded...",
        "width": 110,
        "height": 110
    },
    ...
}]
```

Wenn eine vertragliche Regel das Feld `targetPropertyName` enthält, gilt die Regel nur für das Zielfeld. Andernfalls gilt die Regel für das übergeordnete Objekt, das das Feld `contractualRules` enthält.

Im folgenden Beispiel enthält die Regel `LinkAttribution` das Feld `targetPropertyName`, sodass die Regel für das Feld `description` gilt. Bei Regeln, die für bestimmte Felder gelten, müssen Sie unmittelbar nach den Zieldaten eine Zeile einfügen, die einen Link zur Website des Anbieters enthält. Um beispielsweise die Beschreibung zuzuordnen, fügen Sie unmittelbar nach dem Beschreibungstext eine Zeile ein, die einen Link zu den Daten auf der Website des Anbieters enthält, also in diesem Fall einen Link zu „contoso.com“.

```json
"entities": {
    "value": [{
            ...
            "description": "Marcus Appel is a former American....",
            ...
            "contractualRules": [{
                    "_type": "ContractualRules/LinkAttribution",
                    "targetPropertyName": "description",
                    "mustBeCloseToContent": true,
                    "text": "contoso.com",
                    "url": "http://contoso.com/cr?IG=B8AD73..."
                 },
            ...
  
```

### <a name="license-attribution"></a>Lizenzzuordnung

Wenn die Liste der vertraglichen Regeln eine [LicenseAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#licenseattribution)-Regel enthält, müssen Sie den Hinweis in der Zeile anzeigen, die unmittelbar auf den Inhalt folgt, für den die Lizenz gilt. Die `LicenseAttribution`-Regel verwendet das Feld `targetPropertyName`, um die Eigenschaft anzugeben, für die die Lizenz gilt.

Nachfolgend sehen Sie ein Beispiel, das eine `LicenseAttribution`-Regel enthält.

![Lizenzzuordnung](../media/cognitive-services-bing-entities-api/licenseattribution.png)

Der von Ihnen angezeigte Lizenzhinweis muss einen Link zu der Website enthalten, die die Informationen zur Lizenz enthält. In der Regel verwenden Sie den Namen der Lizenz als Link. Wenn beispielsweise der Hinweis **Text unter CC-BY-SA-Lizenz** lautet und „CC-BY-SA“ der Name der Lizenz ist, würden Sie „CC-BY-SA“ als Link verwenden.

### <a name="link-and-text-attribution"></a>Link- und Textzuordnung

Die Regeln [LinkAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#linkattribution) und [TextAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#textattribution) werden normalerweise verwendet, um den Anbieter der Daten zu identifizieren. Das Feld `targetPropertyName` bezeichnet das Feld, für das die Regel gilt.

Zum Zuordnen der Anbieter fügen Sie eine Zeile ein, die unmittelbar auf den Inhalt folgt, für den die Zuordnungen gelten (z.B. das Zielfeld). Die Zeile sollte eindeutig gekennzeichnet sein, um anzugeben, dass die Anbieter die Quelle der Daten sind. Beispiel: „Daten von: contoso.com“. Im Fall von `LinkAttribution`-Regeln müssen Sie einen Link zur Website des Anbieters erstellen.

Nachfolgend sehen Sie ein Beispiel, das `LinkAttribution`- und `TextAttribution`-Regeln enthält.

![Link- und Textzuordnung](../media/cognitive-services-bing-entities-api/linktextattribution.png)

### <a name="media-attribution"></a>Medienzuordnung

Wenn die Entität ein Bild enthält und Sie dieses anzeigen, müssen Sie einen Link bereitstellen, über den Benutzer mit einem Klick zur Website des Anbieters gelangen. Wenn die Entität eine [MediaAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#mediaattribution)-Regel enthält, verwenden Sie die URL der Regel, um den Link zum direkten Klicken zu erstellen. Verwenden Sie andernfalls die URL, die im `provider`-Feld des Bilds enthalten ist, um den Link zum direkten Klicken zu erstellen.

Nachfolgend sehen Sie ein Beispiel, das das `provider`-Feld eines Bilds und vertragliche Regeln enthält. Da das Beispiel die vertragliche Regel enthält, ignorieren Sie das `provider`-Feld des Bilds und wenden die `MediaAttribution`-Regel an.

![Medienzuordnung](../media/cognitive-services-bing-entities-api/mediaattribution.png)

### <a name="search-or-search-like-experience"></a>Suchfunktionalität oder einer Suche ähnliche Funktionalität

Ebenso wie bei der Bing-Websuche-API kann die Bing-Entitätssuche-API nur als Ergebnis einer direkten Abfrage oder Suche eines Benutzers oder als Ergebnis einer Aktion in einer App oder Oberfläche verwendet werden, die logisch als Suchanforderung eines Benutzers interpretiert werden kann. Zur Veranschaulichung zeigen die folgenden Beispiele akzeptable Suchfunktionalitäten oder einer Suche ähnlichen Funktionalitäten.

- Der Benutzer gibt eine Abfrage direkt in das Suchfeld einer App ein.
- Der Benutzer wählt einen bestimmtem Text oder ein bestimmtes Bild aus und fordert „weitere Informationen“ oder „zusätzliche Informationen“ an.
- Der Benutzer fragt einen Suchbot nach einem bestimmten Thema.
- Der Benutzer verweilt bei einem bestimmten Objekt oder einer bestimmten Entität in einem Szenario mit visueller Suche.

Wenn Sie nicht sicher sind, ob Ihre Funktionalität als eine der Suche ähnliche Funktionalität betrachtet werden kann, wenden Sie sich an Microsoft, um dies zu in Erfahrung zu bringen.

## <a name="throttling-requests"></a>Drosselungsanforderungen

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Nächste Schritte

* Verwenden Sie einen [Schnellstart](../quickstarts/csharp.md), um unter Verwendung der Bing-Entitätssuche-API mit der Suche nach Entitäten zu beginnen.
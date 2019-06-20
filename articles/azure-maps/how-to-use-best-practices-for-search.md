---
title: Effizientes Suchen mit dem Suchdienst von Azure Maps | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit dem Suchdienst von Azure Maps bewährte Methoden für die Suche verwenden.
ms.author: v-musehg
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 9cb0f89b4a48d7139adb35dcef48c0115b005c57
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65205620"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Bewährte Methoden zum Verwenden des Suchdiensts von Azure Maps

Der [Suchdienst](https://docs.microsoft.com/rest/api/maps/search) von Azure Maps enthält APIs mit verschiedenen Funktionen, z. B. die Adresssuche oder die Suche nach POI-Daten (Point of Interest) an einem bestimmten Ort. In diesem Artikel werden die bewährten Methoden zum Abrufen von Daten über den Suchdienst von Azure Maps vorgestellt. Sie lernen Folgendes:

* Erstellen von Abfragen zum Zurückgeben relevanter Übereinstimmungen
* Einschränken von Suchergebnissen
* Kennenlernen der Unterschiede zwischen verschiedenen Ergebnistypen
* Lesen der Antwortstruktur der Adresssuche


## <a name="prerequisites"></a>Voraussetzungen

Um die Maps-Dienst-APIs aufrufen zu können, benötigen Sie ein Maps-Konto mit zugehörigem Schlüssel. Informationen zum Erstellen eines Kontos und Abrufen eines Schlüssels finden Sie unter [Verwalten Ihres Azure Maps-Kontos und der dazugehörigen Schlüssel](how-to-manage-account-keys.md).

> [!Tip]
> Um den Suchdienst abzufragen, können Sie die [Postman-App](https://www.getpostman.com/apps) verwenden, um REST-Aufrufe zu erstellen, oder Sie können eine beliebige API-Entwicklungsumgebung verwenden, die Sie bevorzugen.


## <a name="best-practices-for-geocoding"></a>Bewährte Methoden für die Geocodierung

Wenn Sie mit dem Suchdienst von Azure Maps nach einer vollständigen oder unvollständigen Adresse suchen, wird Ihr Suchbegriff verwendet und der Längen- und Breitengrad der Adresse zurückgegeben. Dieser Vorgang wird als „Geocodierung“ bezeichnet. Die Möglichkeit der Geocodierung in einem Land hängt von der Abdeckung der Straßendaten und der Geocodierungsgenauigkeit des Geocodierungsdiensts ab.

Weitere Informationen zu den Geocodierungsfunktionen von Azure Maps nach Ländern/Regionen finden Sie unter [Abdeckung durch Geocodierung](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage).

### <a name="limit-search-results"></a>Einschränken von Suchergebnissen

   In diesem Abschnitt erfahren Sie, wie Sie mithilfe der Such-APIs von Azure Maps die Suchergebnisse einschränken können. 

   > [!Note]
   > Nicht alle Such-APIs unterstützen die unten aufgeführten Parameter vollständig.

   **Einschränken von Suchergebnissen auf geografische Räume**

   Um Ihre Ergebnisse in den für Ihren Benutzer relevanten geografischen Raum zu lenken, sollten Sie immer die maximal mögliche Detailstufe für die Standorteingabe hinzufügen. Sie können die Suchergebnisse einschränken, indem Sie die folgenden Eingabetypen hinzufügen:

   1. Legen Sie die `countrySet`-Parameter fest, z.B. „US,FR“. Das Standardsuchverhalten besteht darin, die ganze Welt zu durchsuchen und möglicherweise unerwünschte Ergebnisse zu erhalten. Wenn Ihre Suchanfrage keinen `countrySet`-Parameter enthält, kann die Suche ungenaue Ergebnisse liefern. Die Suche nach einer Stadt namens **Bellevue** liefert z. B. Ergebnisse aus den USA und Frankreich, da es in Frankreich und in den USA Städte namens **Bellevue** gibt.

   2. Sie können die Parameter `btmRight` und `topleft` verwenden, um den Begrenzungsrahmen so festzulegen, damit die Suche auf einen bestimmten Bereich auf der Karte eingeschränkt wird.

   3. Sie können die Koordinatenparameter `lat` und `lon` definieren und den Radius des Suchbereichs mit dem Parameter `radius` festlegen, um den für die Ergebnisse relevanten Bereich zu beeinflussen.


   **Parameter der Fuzzysuche**

   1. `minFuzzyLevel` und `maxFuzzyLevel` helfen dabei, relevante Treffer zurückzugeben, auch wenn die Abfrageparameter nicht genau den gewünschten Informationen entsprechen. Die meisten Suchabfragen sind zur Leistungssteigerung und Verringerung ungewöhnlicher Ergebnisse standardmäßig auf `minFuzzyLevel=1` und `maxFuzzyLevel=2` festgelegt. Nehmen Sie z. B. den Suchbegriff „restrant“, der mit „restaurant“ verglichen wird, wenn `maxFuzzyLevel` auf 2 festgelegt ist. Die standardmäßigen Fuzzyebenen können je nach Bedarf außer Kraft gesetzt werden. 

   2. Sie können auch den genauen Satz der zurückzugebenden Ergebnistypen angeben, indem Sie den Parameter `idxSet` verwenden. Zu diesem Zweck können Sie eine durch Komma getrennte Liste von Indizes übermitteln, wobei die Reihenfolge der Elemente unerheblich ist. Nachfolgend sind die unterstützten Indizes aufgeführt:

       * `Addr` - **Adressbereiche:** Für einige Straßen gibt es Adresspunkte, die über Anfang und Ende der Straße interpoliert und als Adressbereiche dargestellt werden.
       * `Geo` - **Geografische Regionen:** Bereiche auf einer Karte, die die Verwaltungseinheiten eines Landes darstellen, d. h. Land, Bundesland, Stadt.
       * `PAD` - **Punktadresse:**  Punkte auf einer Karte, an denen eine bestimmte Adresse mit Straßenname und Nummer in einem Index zu finden ist, z. B. Soquel Dr 2501. Dies ist die höchste verfügbare Genauigkeit für Adressen.  
       * `POI` - **Points of Interest:** Punkte auf einer Karte, die beachtenswert sind und interessant sein können.  [Suchadresse abrufen](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) gibt keine POIs zurück.  
       * `Str` - **Straßen:** Darstellung von Straßen auf der Karte.
       * `XStr` - **Querstraßen/Kreuzungen:**  Darstellung von Kreuzungen, d. h. Orte, an denen sich zwei Straßen kreuzen.


       **Anwendungsbeispiele**:

       * idxSet=POI (nur nach Points of Interest suchen) 

       * idxSet=PAD,Addr (nur Adressen suchen, PAD= Punktadresse, Addr= Adressbereich)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Inverser Geocode und Filter für Geografieentitätstyp

Wenn Sie eine inverse Geocodesuche mit [API für die umgekehrte Adresssuche](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) durchführen, kann der Dienst Polygone für die Verwaltungsbereiche zurückgeben. Durch die Angabe des Parameters `entityType` in der Anforderung können Sie die Suche auf bestimmte Geografieentitätstypen einschränken. Die resultierende Antwort enthält die Geografie-ID sowie den passenden Entitätstyp. Wenn Sie mehr als eine Entität angeben, gibt der Endpunkt die **kleinste verfügbare Entität** zurück. Die zurückgegebene Geometrie-ID kann dazu verwendet werden, die Geometrie dieser Geografie über [Polygondienst abrufen](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) abzurufen.

**Beispiel für eine Anforderung:**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**Antwort:**

```JSON
{
    "summary": {
        "queryTime": 8,
        "numResults": 1
    },
    "addresses": [
        {
            "address": {
                "routeNumbers": [],
                "countryCode": "US",
                "countrySubdivision": "WA",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "municipality": "Redmond",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Redmond, WA",
                "boundingBox": {
                    "northEast": "47.717105,-122.034537",
                    "southWest": "47.627016,-122.164998",
                    "entity": "position"
                },
                "countrySubdivisionName": "Washington"
            },
            "position": "47.639454,-122.130455",
            "dataSources": {
                "geometry": {
                    "id": "00005557-4100-3c00-0000-0000596ae571"
                }
            },
            "entityType": "Municipality"
        }
    ]
}
```

### <a name="search-results-language"></a>Sprache der Suchergebnisse

Mit dem Parameter `language` können Sie festlegen, in welcher Sprache Suchergebnisse zurückgegeben werden sollen. Wenn die Sprache in der Anfrage nicht festgelegt ist, wird der Suchdienst automatisch auf die am häufigsten verwendete Sprache in dem Land/der Region voreingestellt. Wenn Daten in der angegebenen Sprache nicht verfügbar sind, wird ebenfalls die Standardsprache verwendet. Eine Liste der unterstützten Sprachen in Bezug auf die Azure Maps-Dienste nach Ländern/Regionen finden Sie unter [Unterstützte Sprachen](https://docs.microsoft.com/azure/azure-maps/supported-languages).


### <a name="predictive-mode-auto-suggest"></a>Vorhersagemodus (Vorschlagssuche)

Der Parameter `typeahead` sollte auf „true“ festgelegt werden, um weitere Übereinstimmungen für Teilabfragen zu finden. Die Abfrage wird als Teileingabe interpretiert und die Suche wechselt in den Vorhersagemodus. Andernfalls geht der Dienst davon aus, dass alle relevanten Informationen übermittelt wurden.

In der folgenden Beispielabfrage können Sie sehen, dass der Suchadressdienst nach „Microso“ abgefragt wird, wobei der Parameter `typeahead` auf **true** festgelegt ist. Sie können anhand der Antwort sehen, dass der Suchdienst die Abfrage als Teilabfrage interpretiert hat und die Antwort Ergebnisse für die automatisch vorgeschlagene Abfrage enthält.

**Beispielabfrage:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

**Antwort:**

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 25,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/10294417",
            "score": 2.594,
            "dist": 327.546040632591,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980526399,980528300",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63989,
                "lon": -122.12509
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309
                },
                "btmRightPoint": {
                    "lat": 47.64223,
                    "lon": -122.13061
                }
            }
        },
        ...,
        ...,
        ...,
        ...,
        {
            "type": "Street",
            "id": "US/STR/p0/9063400",
            "score": 2.075,
            "dist": 3655467.6406921702,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "postalCode": "28217",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28217",
                "countrySubdivisionName": "North Carolina"
            },
            "position": {
                "lat": 35.14279,
                "lon": -80.91814
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814
                },
                "btmRightPoint": {
                    "lat": 35.14279,
                    "lon": -80.91824
                }
            }
        }
    ]
}
```


### <a name="uri-encoding-to-handle-special-characters"></a>URI-Codierung zum Verarbeiten von Sonderzeichen 

Um Adressen von Kreuzungen zu finden, d. h. „1st Avenue & Union Street, Seattle“, muss das Sonderzeichen „&“ vor dem Senden der Anforderung codiert werden. Es wird empfohlen, Zeichendaten in einem URI zu codieren, wobei alle Zeichen mit einem „%“-Zeichen und einem zweistelligen hexadezimalen Wert entsprechend ihrem UTF-8-Zeichen codiert werden.

**Anwendungsbeispiele**:

Suchadresse abrufen:

```
query=1st Avenue & E 111th St, New York
```

 muss wie folgt codiert werden:

```
query"=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```


Hier folgen die verschiedenen Methoden, die für verschiedene Sprachen verwendet werden können: 

JavaScript/TypeScript:
```Javascript
encodeURIComponent(query)
```

C#/VB:
```C#
Uri.EscapeDataString(query)
```

Java:
```Java
URLEncoder.encode(query, "UTF-8") 
```

Python:
```Python
import urllib.parse 
urllib.parse.quote(query)
```

C++:
```C++
#include <curl/curl.h>
curl_easy_escape(query)
```

PHP:
```PHP
urlencode(query)
```

Ruby:
```Ruby
CGI::escape(query) 
```

Swift:
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Go:
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-search"></a>Bewährte Methoden für die POI-Suche

Die POI-Suche (Points of Interest) ermöglicht es Ihnen, POI-Ergebnisse nach Namen anzufordern, z. B. Unternehmen anhand ihres Namens suchen. Es wird dringend empfohlen, den Parameter `countrySet` zu verwenden, um Länder anzugeben, die in der Anwendung abgedeckt werden sollen. Beim Standardverhalten wird auf der ganzen Welt gesucht, sodass potenziell unnötige Ergebnisse zurückgegeben werden und/oder dies zu längeren Suchzeiten führt.

### <a name="brand-search"></a>Markensuche

Um die Relevanz der Ergebnisse und der Informationen in der Antwort zu verbessern, umfasst die POI-Suchantwort (Point of Interest) die Markeninformationen, mit denen die Antwort weiter analysiert werden kann.

Lassen Sie uns eine [POI-Kategoriesuche](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) für Tankstellen in der Nähe des Microsoft Campus (Redmond, WA) anfordern. Wenn Sie die Antwort betrachten, können Sie Markeninformationen für jeden zurückgegebenen POI sehen.

**Beispielabfrage:**

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

**Antwort:**

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 206,
        "numResults": 3,
        "offset": 0,
        "totalResults": 742169,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/245813",
            "score": 5.663,
            "dist": 1037.0280221303253,
            "info": "search:ta:840531000004190-US",
            "poi": {
                "name": "Chevron",
                "phone": "+(1)-(425)-6532200",
                "brands": [
                    {
                        "name": "Chevron"
                    }
                ],
                "url": "www.chevron.com",
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2444",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Northeast Bellevue, Bellevue",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "2444 Bel Red Rd, Bellevue, WA 98007",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63223,
                        "lon": -122.13311
                    }
                }
            ]
        },
        ...,
        {
            "type": "POI",
            "id": "US/POI/p0/7727106",
            "score": 5.662,
            "dist": 1458.645407416307,
            "info": "search:ta:840539000488527-US",
            "poi": {
                "name": "BROWN BEAR CAR WASH",
                "phone": "+(1)-(425)-6442868",
                "brands": [
                    {
                        "name": "Texaco"
                    }
                ],
                "url": "www.texaco.com/",
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "15248",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980525511",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "15248 Bel Red Rd, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62826,
                        "lon": -122.13626
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>Flughafensuche

Die POI-Suche unterstützt die Suche nach Flughäfen anhand der offiziellen Flughafencodes. Beispiel: **SEA** (Seattle-Tacoma International Airport). 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Umgebungssuche

Damit nur POI-Ergebnisse in der Nähe eines bestimmten Ortes abgerufen werden, ist die [API für die Umgebungssuche](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) möglicherweise die richtige Wahl. Dieser Endpunkt liefert nur POI-Ergebnisse und übernimmt keinen Suchabfrageparameter. Es wird empfohlen, den Radius festzulegen, um die Ergebnisse einzuschränken.

## <a name="understanding-the-responses"></a>Grundlegendes zu den Antworten

Lassen Sie uns eine Adresssuchanforderung an den Azure Maps-[Suchdienst](https://docs.microsoft.com/rest/api/maps/search) für eine Adresse in Seattle richten. Wenn Sie sich die nachfolgende Anforderungs-URL genau ansehen, wurde der Parameter `countrySet` auf **US** festgelegt, um nach der Adresse in den Vereinigten Staaten von Amerika zu suchen.

**Beispielabfrage:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

Lassen Sie uns ferner einen Blick auf die folgende Antwortstruktur werfen. Die Ergebnistypen der Ergebnisobjekte in der Antwort sind unterschiedlich. Bei genauer Betrachtung erkennen Sie, dass drei verschiedene Arten von Ergebnisobjekten vorliegen: „Punktadresse“, „Straße“ und „Querstraße“. Beachten Sie, dass die Adresssuche keine POIs zurückgibt. Der Parameter `Score` für jedes Antwortobjekt gibt die Bewertung zur relativen Übereinstimmung im Vergleich zu den Bewertungen anderer Objekte in derselben Antwort an. Weitere Informationen zu den Parametern des Antwortobjekts finden Sie unter [Suchadresse abrufen](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress).

**Unterstützte Ergebnistypen:**

* **Punktadresse:** Punkte auf einer Karte mit einer bestimmten Adresse, die Straßenname und Hausnummer umfasst. Dies ist die höchste verfügbare Genauigkeit für Adressen. 

* **Adressbereich:**  Für einige Straßen gibt es Adresspunkte, die über Anfang und Ende der Straße interpoliert und als Adressbereiche dargestellt werden. 

* **Geografie:** Bereiche auf einer Karte, die die Verwaltungseinheiten eines Landes darstellen, d. h. Land, Bundesland, Stadt. 

* **POI (Points of Interest):** Punkte auf einer Karte, die beachtenswert sind und interessant sein können.

* **Straße:** Darstellung von Straßen auf der Karte. Adressen werden in die Längen- und Breitenkoordinate der Straße aufgelöst, die diese Adresse enthält. Die Hausnummer wird möglicherweise nicht verarbeitet. 

* **Querstraße:** Kreuzungen. Darstellung von Kreuzungen, d. h. Orte, an denen sich zwei Straßen kreuzen.

**Antwort:**

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 129,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/43076024",
            "score": 9.894,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, South Lake Union, Lower Queen Anne",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "400 Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/2440854",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Westlake, South Lake Union",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "extendedPostalCode": "981094347,981094700,981094701,981094702",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62553,
                "lon": -122.33936
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62545,
                    "lon": -122.33861
                },
                "btmRightPoint": {
                    "lat": 47.62574,
                    "lon": -122.33974
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8450985",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109,98121",
                "extendedPostalCode": "981094991,981211117,981211237,981213206",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.61691,
                "lon": -122.35251
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61502,
                    "lon": -122.35041
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.35484
                }
            }
        },
        ...,
        ...,
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/3816818",
            "score": 6.759,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street & Valley Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62574,
                "lon": -122.33861
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62664,
                    "lon": -122.33994
                },
                "btmRightPoint": {
                    "lat": 47.62484,
                    "lon": -122.33728
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Geometrie

Wenn der Antworttyp **Geometrie** lautet, kann er die Geometrie-ID enthalten, die im **dataSources**-Objekt unter „geometry“ und „id“ zurückgegeben wird. Mit [Polygondienst abrufen](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) können Sie z. B. die Geometriedaten im GeoJSON-Format anfordern, z. B. einen Stadt- oder Flughafenplan für eine Reihe von Entitäten. Sie können diese Begrenzungsdaten für [Geofencing](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) oder die [Suche von POIs innerhalb der Geometrie](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) verwenden.


Die API-Antworten für [Search Address](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) (Adresse suchen) oder [Search Fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) (Fuzzy suchen) können die **Geometrie-ID** einbeziehen, die im „dataSources“-Objekt unter „geometry“ und „id“ zurückgegeben wird.


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, [wie Sie Suchanforderungen für die Azure Maps-Suche erstellen](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address).
* Machen Sie sich mit der [API-Dokumentation zum Suchdienst](https://docs.microsoft.com/rest/api/maps/search) von Azure Maps vertraut. 
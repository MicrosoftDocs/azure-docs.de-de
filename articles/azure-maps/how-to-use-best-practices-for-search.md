---
title: Bewährte Methoden für den Azure Maps-Suchdienst | Microsoft Azure Maps
description: Erfahren Sie, wie Sie beim Nutzen des Suchdiensts von Microsoft Azure Maps bewährte Methoden verwenden.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 11c1938c3c1ccba533f52336fad81ebeaae53b24
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895476"
---
# <a name="best-practices-for-azure-maps-search-service"></a>Bewährte Methoden für den Suchdienst von Azure Maps

Der Azure Maps-[Suchdienst](/rest/api/maps/search) enthält APIs, die verschiedene Funktionen bieten, die Entwickler bei der Suche nach Adressen, Orten, Brancheneinträgen (nach Name oder Kategorie) und anderen geografischen Informationen unterstützen. Mithilfe der [API für die Fuzzysuche](/rest/api/maps/search/getsearchfuzzy) können Benutzer beispielsweise nach einer Adresse oder einem Point of Interest (POI) suchen.

In diesem Artikel wird erläutert, wie Sie beim Aufrufen von Daten aus dem Suchdienst von Azure Maps vernünftige Methoden anwenden können. Sie lernen Folgendes:
> [!div class="checklist"]
> * Erstellen von Abfragen zum Zurückgeben relevanter Übereinstimmungen
> * Einschränken von Suchergebnissen
> * Kennenlernen der Unterschiede zwischen den Ergebnistypen
> * Lesen der Antwortstruktur der Adresssuche

## <a name="prerequisites"></a>Voraussetzungen

1. [Erstellen eines Azure Maps-Kontos](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt)

In diesem Artikel wird die [Postman-App](https://www.postman.com/downloads/) zum Generieren von REST-Aufrufen verwendet, Sie können aber eine beliebige API-Entwicklungsumgebung verwenden.

## <a name="best-practices-to-geocode-addresses"></a>Bewährte Methoden zur Geocodierung von Adressen

Wenn Sie mit dem Suchdienst von Azure Maps nach einer vollständigen oder unvollständigen Adresse suchen, liest die API Schlüsselwörter aus Ihrer Suchabfrage. Anschließend werden der Längen- und Breitengrad der Adresse zurückgegeben. Dieser Vorgang wird als *Geocodierung* bezeichnet.

Die Möglichkeit der Geocodierung in einem Land/einer Region hängt von der Verfügbarkeit von Straßendaten und der Genauigkeit des Geocodierungsdiensts ab. Weitere Informationen zu den Geocodierungsfunktionen von Azure Maps nach Land oder Region finden Sie unter [Abdeckung durch Geocodierung](./geocoding-coverage.md).

### <a name="limit-search-results"></a>Einschränken von Suchergebnissen

 Mit der Azure Maps-Such-API können Sie die Suchergebnisse wie gewünscht einschränken. Sie schränken die Ergebnisse ein, damit Sie Ihren Benutzern relevante Daten anzeigen können.

> [!NOTE]
> Die Such-APIs unterstützen mehr Parameter als nur die in diesem Artikel besprochenen.

#### <a name="geobiased-search-results"></a>Geografisch gelenkte Suchergebnisse

Um die Ergebnisse geografisch in den für Ihren Benutzer relevanten Bereich zu lenken, fügen Sie immer so viele Standortangaben wie möglich hinzu. Möglicherweise möchten Sie die Suchergebnisse einschränken, indem Sie einige Eingabetypen angeben:

* Legen Sie den Parameter `countrySet` fest. Sie können ihn z. B. auf `US,FR` festlegen. Standardmäßig durchsucht die API die gesamte Welt, sodass sie möglicherweise überflüssige Ergebnisse zurückgeben kann. Wenn Ihre Abfrage keinen `countrySet`-Parameter umfasst, kann die Suche ungenaue Ergebnisse liefern. Die Suche nach einer Stadt namens *Bellevue* liefert z. B. Ergebnisse aus den USA und Frankreich, da in beiden Ländern/Regionen eine Stadt mit dem Namen *Bellevue* existiert.

* Mit den Parametern `btmRight` und `topleft` können Sie die Begrenzungsrahmen festlegen. Diese Parameter schränken die Suche auf ein bestimmtes Gebiet auf der Karte ein.

* Um den für die Ergebnisse relevanten Bereich zu beeinflussen, definieren Sie die Koordinatenparameter `lat` und `lon`. Verwenden Sie den Parameter `radius`, um den Radius des Suchbereichs festzulegen.


#### <a name="fuzzy-search-parameters"></a>Parameter der Fuzzysuche

Es wird empfohlen, die Azure Maps-API für die [Fuzzysuche](/rest/api/maps/search/getsearchfuzzy) zu verwenden, wenn Sie Ihre Benutzereingaben für eine Suchabfrage nicht kennen. Beispielsweise kann die Eingabe des Benutzers eine Adresse oder der POI-Typ sein, wie z. B. *Einkaufszentrum* . Die API kombiniert POI-Suche und Geocodierung zu einer kanonischen *einzeiligen Suche* : 

* Die Parameter `minFuzzyLevel` und `maxFuzzyLevel` helfen, relevante Übereinstimmungen zurückzugeben, selbst wenn die Abfrageparameter nicht genau mit den vom Benutzer gewünschten Informationen übereinstimmen. Um die Leistung zu maximieren und ungewöhnliche Ergebnisse zu verringern, legen Sie Suchabfragen auf die Standardwerte `minFuzzyLevel=1` und `maxFuzzyLevel=2` fest. 

    Wenn z. B. der Parameter `maxFuzzyLevel` auf 2 festgelegt ist, wird der Suchbegriff *restrant* zu *restaurant* zugeordnet. Sie können die standardmäßigen Fuzzyebenen bei Bedarf außer Kraft setzen. 

* Verwenden Sie den Parameter `idxSet`, um den genauen Satz von Ergebnistypen zu priorisieren. Um einen genauen Satz von Ergebnissen zu priorisieren, können Sie eine durch Trennzeichen getrennte Liste von Indizes übermitteln. In der Liste spielt die Reihenfolge der Elemente keine Rolle. Azure Maps unterstützt die folgenden Indizes:

* `Addr` - **Adressbereiche** : Adresspunkte, die über Anfang und Ende der Straße interpoliert werden. Diese Punkte werden als Adressbereiche dargestellt.
* `Geo` - **Geografische Regionen:** Administrative Aufteilung von Land. Eine Geografie kann z. B. ein Land/eine Region, ein Bundesstaat oder eine Stadt sein.
* `PAD` - **Punktadressen** : Adressen, die einen Straßennamen und eine Nummer enthalten. Punktadressen können in einem Index gefunden werden. Ein Beispiel ist *Soquel Dr 2501* . Eine Punktadresse bietet den höchsten Grad an Genauigkeit, der für Adressen verfügbar ist.  
* `POI` - **Points of Interest** : Punkte auf einer Karte, die als beachtenswert angesehen werden oder die interessant sein könnten. Die [Suchadressen-API](/rest/api/maps/search/getsearchaddress) gibt keine Points of Interest (POIs) zurück.  
* `Str` - **Straßen:** Straßen auf der Karte.
* `XStr` - **Querstraßen oder Kreuzungen** : Kreuzungen oder Orte, an denen sich zwei Straßen kreuzen.


#### <a name="usage-examples"></a>Anwendungsbeispiele

* `idxSet=POI` – Nur POIs suchen. 

* `idxSet=PAD,Addr` – Nur Adressen suchen. `PAD` gibt die Punktadresse und `Addr` den Adressbereich an.

### <a name="reverse-geocode-and-filter-for-a-geography-entity-type"></a>Inverser Geocode und Filter für einen Geografieentitätstyp

Wenn Sie eine inverse Geocodesuche mit der [API für die umgekehrte Adresssuche](/rest/api/maps/search/getsearchaddressreverse) durchführen, kann der Dienst Polygone für Verwaltungsbereiche zurückgeben. Ein Beispiel: Sie möchten das Flächenpolygon für einen Ort abrufen.  Nehmen Sie den Parameter `entityType` in Ihre Anforderungen auf, um die Suche auf bestimmte Geografieentitätstypen einzugrenzen. 

Die resultierende Antwort enthält die Geografie-ID und den passenden Entitätstyp. Wenn Sie mehr als eine Entität angeben, gibt der Endpunkt die *kleinste verfügbare Entität* zurück. Sie können die zurückgegebene Geometrie-ID verwenden, um die Geometrie der Geografie über den [Dienst „Polygonsuche“](/rest/api/maps/search/getsearchpolygon) abzurufen.

#### <a name="sample-request"></a>Beispiel für eine Anforderung

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

#### <a name="response"></a>Antwort

```JSON
{
    "summary": {
        "queryTime": 14,
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

### <a name="set-the-results-language"></a>Festlegen der Sprache der Ergebnisse

Verwenden Sie den Parameter `language`, um die Sprache für die zurückgegebenen Suchergebnisse festzulegen. Wenn in der Anforderung die Sprache nicht festgelegt ist, verwendet der Suchdienst standardmäßig die am häufigsten verwendete Sprache des Landes oder der Region. Wenn Daten in der angegebenen Sprache nicht verfügbar sind, wird die Standardsprache verwendet. 

Weitere Informationen finden Sie unter [Unterstützte Sprachen in Azure Maps](./supported-languages.md).


### <a name="use-predictive-mode-automatic-suggestions"></a>Verwenden des Vorhersagemodus (automatische Vorschläge)

Legen Sie den Parameter `typeahead` auf `true` fest, um weitere Übereinstimmungen für Teilabfragen zu finden. Diese Abfrage wird als Teileingabe interpretiert und die Suche wechselt in den Vorhersagemodus. Wenn Sie den Parameter `typeahead` nicht auf `true` festlegen, geht der Dienst davon aus, dass alle relevanten Informationen übergeben wurden.

In der folgenden Beispielabfrage wird der Suchadressdienst nach *Microso* abgefragt. Hier wird der Parameter `typeahead` auf `true` festgelegt. Sie können anhand der Antwort sehen, dass der Suchdienst die Abfrage als Teilabfrage interpretiert hat. Die Antwort enthält Ergebnisse für eine automatisch vorgeschlagene Abfrage.

#### <a name="sample-query"></a>Beispielabfrage

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

#### <a name="response"></a>Antwort

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 18,
        "numResults": 7,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/9438784",
            "score": 2.594099998474121,
            "dist": 314.0590106663596,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
            },
            "position": {
                "lat": 47.63988,
                "lon": -122.12438
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64223,
                    "lon": -122.1256,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1756074",
            "score": 2.592679977416992,
            "dist": 876.0272035824189,
            "address": {
                "streetName": "Microsoft Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64032,
                "lon": -122.1344
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64253,
                    "lon": -122.13535,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63816,
                    "lon": -122.13305,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1470668",
            "score": 2.5290400981903076,
            "dist": 2735.4883918101486,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Bellevue, WA 98007"
            },
            "position": {
                "lat": 47.65784,
                "lon": -122.14335
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.65785,
                    "lon": -122.14335,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65784,
                    "lon": -122.14325,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/12812615",
            "score": 2.527509927749634,
            "dist": 2870.9579016916873,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.66034,
                "lon": -122.1404
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.66039,
                    "lon": -122.14325,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65778,
                    "lon": -122.13749,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/197588",
            "score": 2.4630401134490967,
            "dist": 878.1404663812472,
            "address": {
                "streetName": "157th Avenue Northeast",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "extendedPostalCode": "980525344, 980525398, 980525399",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "157th Avenue Northeast, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64351,
                "lon": -122.13056
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64473,
                    "lon": -122.13058,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.6425,
                    "lon": -122.13016,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/3033991",
            "score": 2.0754499435424805,
            "dist": 3655467.8844475765,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
            },
            "position": {
                "lat": 35.14267,
                "lon": -80.91824
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14287,
                    "lon": -80.91839,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8395877",
            "score": 2.0754499435424805,
            "dist": 3655437.0037482483,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "countrySubdivisionName": "North Carolina",
                "postalCode": "28273",
                "extendedPostalCode": "282738105, 282738106, 282738108, 2827382, 282738200",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28273"
            },
            "position": {
                "lat": 35.14134,
                "lon": -80.9198
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14274,
                    "lon": -80.92159,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14002,
                    "lon": -80.91824,
                    "valid": true
                }
            }
        }
    ]
}
```


### <a name="encode-a-uri-to-handle-special-characters"></a>Codieren von URIs zum Verarbeiten von Sonderzeichen 

Um Querstraßenadressen zu finden, müssen Sie den URI codieren, um Sonderzeichen in der Adresse zu behandeln. Sehen Sie sich folgendes Adressbeispiel an: *1st Avenue & Union Street, Seattle* . Codieren Sie hier das kaufmännische Und-Zeichen (`&`), bevor Sie die Anforderung senden. 

Es wird empfohlen, Zeichendaten in einem URI zu codieren. In einem URI codieren Sie alle Zeichen mit einem Prozentzeichen (`%`) und einem zweistelligen Hexadezimalwert, der dem UTF-8-Code der Zeichen entspricht.

#### <a name="usage-examples"></a>Anwendungsbeispiele

Beginnen Sie mit dieser Adresse:

```
query=1st Avenue & E 111th St, New York
```

Codieren Sie die Adresse:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```

Sie können die folgenden Methoden verwenden.

JavaScript oder TypeScript:
```javascript
encodeURIComponent(query)
```

C#- oder Visual Basic:
```csharp
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


## <a name="best-practices-for-poi-searching"></a>Bewährte Methoden für die POI-Suche

Bei einer POI-Suche können Sie POI-Ergebnisse nach Namen anfordern. Sie können z. B. nach dem Namen eines Unternehmens suchen. 

Es wird dringend empfohlen, den Parameter `countrySet` zu verwenden, um die Länder/Regionen anzugeben, die von Ihrer Anwendung abgedeckt werden sollen. Standardmäßig wird weltweit gesucht. Diese umfassende Suche könnte überflüssige Ergebnisse liefern und sehr lange dauern.

### <a name="brand-search"></a>Markensuche

Um die Relevanz der Ergebnisse und der Informationen in der Antwort zu verbessern, umfasst die POI-Suchantwort (Point of Interest) Markeninformationen. Sie können diese Informationen verwenden, um die Antwort weiter zu analysieren.

In einer Anforderung können Sie eine durch Trennzeichen getrennte Liste von Markennamen übermitteln. Schränken Sie die Ergebnisse mithilfe der Liste auf bestimmte Marken ein, indem Sie den Parameter `brandSet` festlegen. In Ihrer Liste spielt die Reihenfolge der Elemente keine Rolle. Wenn Sie mehrere Markenlisten bereitstellen, müssen die zurückgegebenen Ergebnisse zu mindestens einer Ihrer Listen gehören.

Um die Markensuche zu untersuchen, fordern wir eine [POI-Kategoriensuche](/rest/api/maps/search/getsearchpoicategory) an. Im folgenden Beispiel suchen wir nach Tankstellen in der Nähe des Microsoft-Campus in Redmond, Washington. Die Antwort zeigt Markeninformationen für jeden zurückgegebenen POI.

#### <a name="sample-query"></a>Beispielabfrage

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

#### <a name="response"></a>Antwort

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 276,
        "numResults": 3,
        "offset": 0,
        "totalResults": 762680,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/8831765",
            "score": 5.6631999015808105,
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
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.chevron.com",
                "categories": [
                    "petrol station"
                ],
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
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63222,
                        "lon": -122.13312,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831752",
            "score": 5.662710189819336,
            "dist": 1330.1278248163273,
            "info": "search:ta:840539001100326-US",
            "poi": {
                "name": "76",
                "phone": "+(1)-(425)-7472126",
                "brands": [
                    {
                        "name": "76"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.76.com",
                "categories": [
                    "petrol station"
                ],
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
                "streetNumber": "2421",
                "streetName": "148Th Ave Ne",
                "municipalitySubdivision": "Redmond, Bridle Trails, Bellevue",
                "municipality": "Redmond, Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "2421 148Th Ave Ne, Bellevue, WA 98007",
                "localName": "Bellevue"
            },
            "position": {
                "lat": 47.63187,
                "lon": -122.14365
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63277,
                    "lon": -122.14498,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63097,
                    "lon": -122.14232,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "minor",
                    "position": {
                        "lat": 47.63187,
                        "lon": -122.14374,
                        "valid": true
                    }
                },
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63186,
                        "lon": -122.14313,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831764",
            "score": 5.662449836730957,
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
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.texaco.com/",
                "categories": [
                    "petrol station"
                ],
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
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62827,
                        "lon": -122.13628,
                        "valid": true
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>Flughafensuche

Mithilfe der API „Search POI“ (POI suchen) können Sie nach Flughäfen suchen, indem Sie deren offiziellen Code verwenden. Sie können z. B. *SEA* verwenden, um den Seattle-Tacoma International Airport zu finden: 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Umgebungssuche

Zum Abrufen von POI-Ergebnissen um einen bestimmten Standort herum können Sie versuchen, die [API „Search Nearby“](/rest/api/maps/search/getsearchnearby) (in der Nähe suchen) zu verwenden. Der Endpunkt gibt nur POI-Ergebnisse zurück. Er übernimmt keine Suchabfrageparameter. 

Es wird empfohlen, den Radius festzulegen, um die Ergebnisse einzuschränken.

## <a name="understanding-the-responses"></a>Grundlegendes zu den Antworten

Lassen Sie uns eine Adresse in Seattle finden, indem wir eine Anforderung für eine Adresssuche beim Suchdienst von Azure Maps durchführen. In der folgenden Anforderungs-URL legen wir den Parameter `countrySet` auf `US` fest, um nach der Adresse in den USA zu suchen.

### <a name="sample-query"></a>Beispielabfrage

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

### <a name="supported-types-of-results"></a>Unterstützte Ergebnistypen

* **Punktadresse** : Punkte auf einer Karte mit einer bestimmten Adresse, die Straßenname und Hausnummer umfasst. Eine Punktadresse bietet den höchsten Grad an Genauigkeit für Adressen. 

* **Adressbereich** : Der Bereich der Adresspunkte, die über Anfang und Ende der Straße interpoliert werden.  

* **geography:** Bereiche auf einer Karte, die die Verwaltungseinheiten eines Landes darstellen, z. B. Land/Region, Bundesland, Stadt. 

* **POI** : Punkte auf einer Karte, die beachtenswert sind und interessant sein können.

* **Straße** : Straßen auf der Karte. Adressen werden in die Längen- und Breitenkoordinate der Straße aufgelöst, die diese Adresse enthält. Die Hausnummer wird möglicherweise nicht verarbeitet. 

* **Querstraße** : Kreuzungen. Querstraßen stellen Kreuzungen dar, an denen sich zwei Straßen kreuzen.

### <a name="response"></a>Antwort

Betrachten wir die Antwortstruktur. In der folgenden Antwort sind die Typen der Ergebnisobjekte unterschiedlich. Wenn Sie genau hinsehen, sehen Sie drei Typen von Ergebnisobjekten:

* Punktadresse
* Straße
* Querstraße

Beachten Sie, dass die Adresssuche keine POIs zurückgibt.  

Der Parameter `Score` für jedes Antwortobjekt gibt an, wie sich die übereinstimmende Bewertung zu den Bewertungen anderer Objekte in derselben Antwort verhält. Weitere Informationen über Antwortobjektparameter finden Sie unter [Suchadresse abrufen](/rest/api/maps/search/getsearchaddress).

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 146,
        "numResults": 6,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/28725082",
            "score": 9.893799781799316,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/6700384",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61724,
                "lon": -122.35207
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61825,
                    "lon": -122.35336,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61626,
                    "lon": -122.35078,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/9701953",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61965,
                "lon": -122.349
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62066,
                    "lon": -122.35041,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.34761,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/11721297",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Downtown Seattle, Denny Regrade, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98121",
                "extendedPostalCode": "981211237",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98121"
            },
            "position": {
                "lat": 47.61825,
                "lon": -122.35078
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61857,
                    "lon": -122.35078,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61825,
                    "lon": -122.35041,
                    "valid": true
                }
            }
        },
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/232144",
            "score": 6.754479885101318,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
            },
            "position": {
                "lat": 47.62545,
                "lon": -122.33974
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62635,
                    "lon": -122.34107,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62455,
                    "lon": -122.33841,
                    "valid": true
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Geometrie

Der Antworttyp *Geometrie* kann die Geometrie-ID enthalten, die im Objekt `dataSources` unter `geometry` und `id` zurückgegeben wird. Sie können z. B. mit dem Dienst [Search Polygon](/rest/api/maps/search/getsearchpolygon) (Polygon suchen) die Geometriedaten in einem GeoJSON-Format anfordern. Wenn Sie dieses Format verwenden, können Sie einen Stadt- oder Flughafenumriss für eine Reihe von Entitäten erhalten. Sie können dann diese Begrenzungsdaten zum [Einrichten von Geofencing](./tutorial-geofence.md) oder zum [Suchen von POIs innerhalb der Geometrie](/rest/api/maps/search/postsearchinsidegeometry) verwenden.


Antworten für die [Suchadressen](/rest/api/maps/search/getsearchaddress)-API oder die API für die [Fuzzysuche](/rest/api/maps/search/getsearchfuzzy) können die Geometrie-ID enthalten, die im Objekt `dataSources` unter `geometry` und `id` zurückgegeben wird:


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter:

> [!div class="nextstepaction"]
> [Erstellen von Anforderungen an den Azure Maps-Suchdienst](./how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [Dokumentation der Suchdienst-API](/rest/api/maps/search)
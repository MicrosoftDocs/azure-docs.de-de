---
title: Anfordern von Daten zu öffentlichen Verkehrsmitteln/Routen mit Microsoft Azure Maps-Mobilitätsdiensten (Vorschau)
description: Erfahren Sie, wie Sie die Azure Maps-Mobilitätsdienste (Vorschau) zum Anfordern von Daten zum öffentlichen Nahverkehr nutzen. Zu diesen Daten zählen beispielsweise Stadtbereich-IDs, Haltestellen, Routen und Routenbeschreibungen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 740080d742f535f868b2ae194b24bebe5ac6ac24
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906028"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-services-preview"></a>Anfordern von Daten zu öffentlichen Verkehrsmitteln/Routen mit den Azure Maps-Mobilitätsdiensten (Vorschau) 

> [!IMPORTANT]
> Mobilitätsdienste von Azure Maps befinden sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


In diesem Artikel wird erläutert, wie Sie Azure Maps-[Mobilitätsdienste](/rest/api/maps/mobility) verwenden, um Daten zu öffentlichen Verkehrsmitteln/Routen anzufordern. Daten zu öffentlichen Verkehrsmitteln umfassen Haltestellen, Routen und geschätzte Reisezeiten.

In diesem Artikel lernen Sie Folgendes:

* Rufen Sie die ID für einen Stadtbereich mit der [Get Metro Area-API](/rest/api/maps/mobility/getmetroareapreview) ab.
* Fordern Sie Informationen zu Haltestellen in der Nähe an, indem Sie den Dienst [Get Nearby Transit](/rest/api/maps/mobility/getnearbytransitpreview) verwenden.
* Führen Sie eine Abfrage über die [Get Transit Routes-API](/rest/api/maps/mobility/gettransitroutepreview) durch, um eine Route mit öffentlichen Verkehrsmitteln zu planen.
* Fordern Sie die Geometrie der Verkehrsroute und den zugehörigen detaillierten Zeitplan an, indem Sie die [Get Transit Itinerary-API](https://aka.ms/https://azure.microsoft.com/services/azure-maps/) verwenden.

## <a name="prerequisites"></a>Voraussetzungen

1. [Erstellen eines Azure Maps-Kontos](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt) Weitere Informationen zur Authentifizierung in Azure Maps finden Sie unter [Verwalten der Authentifizierung in Azure Maps](./how-to-manage-authentication.md).

In diesem Tutorial wird die Anwendung [Postman](https://www.postman.com/) verwendet. Sie können aber auch eine andere API-Entwicklungsumgebung verwenden.

## <a name="get-a-metro-area-id"></a>Abrufen einer ID für den Stadtbereich

Um detaillierte Informationen über Anbieter von öffentlichen Verkehrsmittel und unterstützte öffentliche Verkehrsmittelarten für einen bestimmten Stadtbereich anzufordern, benötigen Sie die `metroId` dieses Bereichs. Mit der [Get Metro Area-API](/rest/api/maps/mobility/getmetroareapreview) können Sie Stadtbereiche anfordern, in denen die Azure Maps-Mobilitätsdienste verfügbar sind. Die Antwort enthält dann Details wie `metroId`, `metroName` und eine Darstellung der Geometrie des Stadtbereichs im GeoJSON-Format.

Wir erstellen nun eine Anforderung zum Abrufen der Stadtbereich-ID für „Seattle-Tacoma“. Führen Sie die folgenden Schritte aus, um die ID für einen Stadtbereich anzufordern:

1. Öffnen Sie die Postman-App, und erstellen Sie eine Sammlung zum Speichern der Anforderungen. Wählen Sie oben in der Postman-App **New** (Neu) aus. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **Collection** (Sammlung) aus.  Geben Sie einen Namen für die Sammlung ein, und klicken Sie dann auf **Create** (Erstellen).

2. Klicken Sie erneut auf **New** (Neu), um die Anforderung zu erstellen. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **Request** (Anforderung) aus. Geben Sie einen Anforderungsnamen (**Request name**) ein. Wählen Sie die im vorherigen Schritt erstellte Sammlung als Speicherort für die Anforderung aus. Wählen Sie anschließend **Speichern** aus.
  
    ![Erstellen einer Anforderung in Postman](./media/how-to-request-transit-data/postman-new.png)

3. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode **GET** aus, und geben Sie die folgende URL ein, um eine GET-Anforderung zu erstellen. Ersetzen Sie `{subscription-key}` durch Ihren Primärschlüssel für Azure Maps.

    ```HTTP
    https://atlas.microsoft.com/mobility/metroArea/id/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126
    ```

4. War die Anforderung erfolgreich, erhalten Sie die folgende Antwort:

    ```JSON
    {
        "results": [
            {
                "metroId": 522,
                "metroName": "Seattle–Tacoma–Bellevue, WA",
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 48.5853,
                        "longitude": -124.80934
                    },
                    "btmRightPoint": {
                        "latitude": 46.90534,
                        "longitude": -121.55032
                    }
                },
                "geometry": {
                    "type": "Polygon",
                    "coordinates": [
                        [
                            [
                                -121.99604,
                                47.16147
                            ],
                            [
                                -121.97051,
                                47.17222
                            ],
                            [
                                -121.96308,
                                47.17671
                            ],
                            ...,
                            ...,
                            ...,
                            [
                                -122.01525,
                                47.16008
                            ],
                            [
                                -122.00553,
                                47.15919
                            ],
                            [
                                -121.99604,
                                47.16147
                            ]
                        ]
                    ]
                }
            }
        ]
    }
    ```

## <a name="request-nearby-transit-stops"></a>Anfordern von Verkehrsmittel-Haltestellen in der Nähe

Mit dem Azure Maps-Dienst [Get Nearby Transit](/rest/api/maps/mobility/getnearbytransitpreview) können Sie Verkehrsobjekte durchsuchen. Die API gibt die Details zum Verkehrsobjekt zurück, z. B. Haltestellen öffentlicher Verkehrsmittel und Leihfahrräder im Bereich eines bestimmten Orts. Als Nächstes senden wir eine Anforderung an den Dienst, um nach den Haltestellen von öffentlichen Verkehrsmitteln innerhalb eines Radius von 300 Metern um einen bestimmten Ort zu suchen.

Führen Sie die hier angegebenen Schritte aus, um eine Anforderung an [Get Nearby Transit](/rest/api/maps/mobility/getnearbytransitpreview) zu senden:

1. Klicken Sie in Postman auf **New Request (Neue Anforderung)**  | **GET request (GET-Anforderung)** , und nennen Sie sie **Get Nearby stops** (Haltestellen in der Nähe abrufen).

2. Wählen Sie auf der Registerkarte „Builder“ die HTTP-Methode **GET** aus, geben Sie die folgende Anforderungs-URL für Ihren API-Endpunkt ein, und klicken Sie auf **Send** (Senden).

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126&radius=300&objectType=stop
    ```

3. Nach einer erfolgreichen Anforderung sollte die Antwortstruktur in etwa wie folgt aussehen:

    ```JSON
    {
        "results": [
            {
                "id": "522---2060603",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631504,
                    "longitude": -122.125275
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632413,
                        "longitude": -122.12659
                    },
                    "btmRightPoint": {
                        "latitude": 47.630594,
                        "longitude": -122.123959
                    }
                }
            },
            {
                "id": "522---2061020",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "68372",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "68372",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631409,
                    "longitude": -122.127136
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632318,
                        "longitude": -122.128451
                    },
                    "btmRightPoint": {
                        "latitude": 47.630499,
                        "longitude": -122.12582
                    }
                }
            },
            {
                "id": "522---2060604",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71310",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "71310",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631565,
                    "longitude": -122.127808
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632474,
                        "longitude": -122.129124
                    },
                    "btmRightPoint": {
                        "latitude": 47.630655,
                        "longitude": -122.126492
                    }
                }
            }
        ]
    } 
    ```

Wenn Sie die Antwortstruktur genauer betrachten, sehen Sie, dass für jedes Verkehrsobjekt Parameter vorhanden sind. Jedes Verkehrsobjekt verfügt über Parameter wie `id`, `type`, `stopName`, `mainTransitType`, `mainAgencyName` und die Position des Objekts in Koordinaten.

Zu Lernzwecken verwenden wir im nächsten Abschnitt eine `id` einer Bushaltestelle als Ursprung für unsere Route.  

## <a name="request-a-transit-route"></a>Anfordern einer Verkehrsroute

Die Azure Maps-API [Get Transit Routes](/rest/api/maps/mobility/gettransitroutepreview) ermöglicht die Reiseplanung. Sie gibt die bestmöglichen Routenoptionen von einem Ursprung zu einem Ziel zurück. Der Dienst bietet verschiedene Fortbewegungsmodi, z. B. zu Fuß, mit dem Fahrrad und öffentlichen Verkehrsmitteln. Als Nächstes suchen wir nach einer Route von der nächstgelegenen Bushaltestelle zum Space Needle-Turm in Seattle.

### <a name="get-location-coordinates-for-destination"></a>Abrufen von Standortkoordinaten für ein Ziel

Zum Abrufen der Standortkoordinaten des Space Needle-Turms verwenden wir den [Dienst für die Fuzzysuche](/rest/api/maps/search/getsearchfuzzy) von Azure Maps.

Führen Sie diese Schritte aus, um eine Anforderung an den Dienst für die Fuzzysuche zu senden:

1. Klicken Sie in Postman auf **New Request (Neue Anforderung)**  | **GET request (GET-Anforderung)** , und nennen Sie sie **Get location coordinates** (Standortkoordinaten abrufen).

2. Wählen Sie auf der Registerkarte „Builder“ die HTTP-Methode **GET** aus, geben Sie die folgende Anforderungs-URL ein, und klicken Sie auf **Send** (Senden).

    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```

3. Wenn Sie die Antwort sorgfältig betrachten, sehen Sie, dass die Ergebnisse der Space Needle-Suche mehrere Standorte enthalten. Jedes Ergebnis enthält die Standortkoordinaten unter **position**. Kopieren Sie die Werte unter `lat` und `lon` unter **position** des ersten Ergebnisses.

   ```JSON
   {
        "summary": {
            "query": "space needle",
            "queryType": "NON_NEAR",
            "queryTime": 35,
            "numResults": 8,
            "offset": 0,
            "totalResults": 11,
            "fuzzyLevel": 1
        },
        "results": [
            {
                "type": "POI",
                "id": "US/POI/p0/6993440",
                "score": 4.67369,
                "info": "search:ta:840539001406144-US",
                "poi": {
                    "name": "Space Needle",
                    "phone": "+(1)-(206)-9052100",
                    "categorySet": [
                        {
                            "id": 7376009
                        }
                    ],
                    "url": "www.spaceneedle.com",
                    "categories": [
                        "important tourist attraction",
                        "tower"
                    ],
                    "classifications": [
                        {
                            "code": "IMPORTANT_TOURIST_ATTRACTION",
                            "names": [
                                {
                                    "nameLocale": "en-US",
                                    "name": "important tourist attraction"
                                },
                                {
                                    "nameLocale": "en-US",
                                    "name": "tower"
                                }
                            ]
                        }
                    ]
                },
                "address": {
                    "streetNumber": "400",
                    "streetName": "Broad St",
                    "municipalitySubdivision": "South Lake Union, Seattle, Lower Queen Anne",
                    "municipality": "Seattle",
                    "countrySecondarySubdivision": "King",
                    "countryTertiarySubdivision": "Seattle",
                    "countrySubdivision": "WA",
                    "postalCode": "98109",
                    "countryCode": "US",
                    "country": "United States",
                    "countryCodeISO3": "USA",
                    "freeformAddress": "400 Broad St, Seattle, WA 98109",
                    "localName": "Seattle",
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
            ...,
            ...,
            ...
        ]
    }
    ```

### <a name="request-route"></a>Anfordern einer Route

Führen Sie die folgenden Schritte aus, um eine Route anzufordern:

1. Klicken Sie in Postman auf **New Request (Neue Anforderung)**  | **GET request (GET-Anforderung)** , und nennen Sie sie **Get Route info** (Routeninformationen abrufen).

2. Wählen Sie auf der Registerkarte „Builder“ die HTTP-Methode **GET** aus, geben Sie die folgende Anforderungs-URL für Ihren API-Endpunkt ein, und klicken Sie auf **Send** (Senden).

    Wir fordern die Routen für das öffentliche Verkehrsmittel „Bus“ an, indem wir die Parameter `modeType` und `transitType` angeben. Die Anforderungs-URL enthält die Standorte, die in den vorherigen Abschnitten abgerufen wurden. Für `originType` verfügen wir jetzt über eine **stopId**. Und für `destionationType` haben wir die **position**.

    Sehen Sie sich die [Liste mit den URI-Parametern](/rest/api/maps/mobility/gettransitroutepreview#uri-parameters) an, die Sie in Ihrer Anforderung für die [Get Transit Routes-API](/rest/api/maps/mobility/gettransitroutepreview) verwenden können.
  
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/route/json?subscription-key={subscription-key}&api-version=1.0&originType=stopId&origin=522---2060603&destionationType=position&destination=47.62039,-122.34928&modeType=publicTransit&transitType=bus
    ```

3. Bei einer erfolgreichen Anforderung sollte die Antwortstruktur in etwa wie folgt aussehen:

    ```JSON
    {
        "results": [
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:0---522",
                "departureTime": "2019-09-07T01:01:50Z",
                "arrivalTime": "2019-09-07T02:16:33Z",
                "travelTimeInSeconds": 4483,
                "numberOfLegs": 8,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:01:50Z",
                        "caption": "249"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:26:00Z",
                        "caption": "249",
                        "lengthInMeters": 9139
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:26:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "255"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:57:21Z",
                        "caption": "255",
                        "lengthInMeters": 13136
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:57:22Z",
                        "legEndTime": "2019-09-07T02:01:27Z",
                        "caption": "Denny Way",
                        "lengthInMeters": 308
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T02:01:27Z",
                        "legEndTime": "2019-09-07T02:06:33Z",
                        "caption": "8"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:06:33Z",
                        "legEndTime": "2019-09-07T02:12:41Z",
                        "caption": "8",
                        "lengthInMeters": 1060
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:12:42Z",
                        "legEndTime": "2019-09-07T02:16:33Z",
                        "lengthInMeters": 251
                    }
                ],
                "itineraryFare": {
                    "price": {
                        "amount": 550,
                        "currencyCode": "USD"
                    },
                    "tickets": [
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        },
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        }
                    ]
                }
            },
            ...,
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:2---522",
                "departureTime": "2019-09-07T00:49:32Z",
                "arrivalTime": "2019-09-07T02:20:06Z",
                "travelTimeInSeconds": 5434,
                "numberOfLegs": 10,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T00:49:32Z",
                        "caption": "226"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T01:15:00Z",
                        "caption": "226",
                        "lengthInMeters": 6792
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:15:00Z",
                        "legEndTime": "2019-09-07T01:20:00Z",
                        "caption": "241"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:20:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "241",
                        "lengthInMeters": 3397
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:33:00Z",
                        "caption": "550"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:33:00Z",
                        "legEndTime": "2019-09-07T01:58:00Z",
                        "caption": "550",
                        "lengthInMeters": 12899
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:58:01Z",
                        "legEndTime": "2019-09-07T01:59:21Z",
                        "caption": "4th Avenue South",
                        "lengthInMeters": 99
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:59:21Z",
                        "legEndTime": "2019-09-07T02:01:00Z",
                        "caption": "33"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:01:00Z",
                        "legEndTime": "2019-09-07T02:13:29Z",
                        "caption": "33,24",
                        "lengthInMeters": 2447
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:13:30Z",
                        "legEndTime": "2019-09-07T02:20:06Z",
                        "lengthInMeters": 457
                    }
                ],
                "itineraryFare": {
                    "price": {
                        "amount": 550,
                        "currencyCode": "USD"
                    },
                    "tickets": [
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        },
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        }
                    ]
                }
            }
        ]
    }
    ```

4. Es ist zu erkennen, dass die Antwort mehrere Routen vom Typ **Bus** enthält. Jede Route verfügt über eine eindeutige **Reiserouten-ID** (itineraryID), eine Zusammenfassung, in der die einzelnen Etappen der Route beschrieben sind, sowie eine `itineraryFare` (Reisepreis), die die Einzelpreise und den Gesamtpreis für Bustickets angibt. Eine Etappe der Route ist ein zwischen zwei Haltestellen-Wegpunkten liegender Teil der Route. Als Nächstes fordern wir Details zur schnellsten Route an, indem wir die `itineraryId` in der Antwort verwenden.

## <a name="request-fastest-route-itinerary"></a>Anfordern der schnellsten Reiseroute

Mit dem Dienst [Get Transit Itinerary](/rest/api/maps/mobility/gettransititinerarypreview) von Azure Maps können Sie Daten für eine bestimmte Route anfordern, indem Sie die **Reiserouten-ID** der Route verwenden, die von der [Get Transit Routes-API](/rest/api/maps/mobility/gettransitroutepreview) zurückgegeben wird. Führen Sie die folgenden Schritte aus, um eine Anforderung zu senden:

1. Klicken Sie in Postman auf **New Request (Neue Anforderung)**  | **GET request (GET-Anforderung)** , und nennen Sie sie **Get Transit info** (Verkehrsinformationen abrufen).

2. Wählen Sie auf der Registerkarte „Builder“ (Generator) die **GET**-HTTP-Methode aus. Geben Sie die folgende Anforderungs-URL für Ihren API-Endpunkt ein, und klicken Sie auf **Send** (Senden).

    Wir legen den Parameter `detailType` auf **geometry** fest, damit die Antwort Informationen zu Haltestellen von öffentlichen Verkehrsmitteln und eine genaue Wegbeschreibung für die Fußweg- und Fahrradabschnitte der Route enthält.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/itinerary/json?api-version=1.0&subscription-key={subscription-key}&query={itineraryId}&detailType=geometry
    ```

3. Bei einer erfolgreichen Anforderung sollte die Antwortstruktur in etwa wie folgt aussehen.  Wenn Sie die JSON-Antwort beobachten, werden Sie feststellen, dass jede Busetappe ein `legfare`-Element enthält. Das `legfare`-Element enthält die Kosten für jede Busroute in Cent, die separat gekauft wird. Am Ende der Antwort sehen Sie ein `itineraryFare`-Element, das die Kosten der gesamten Route in Cent enthält. In diesem Beispiel gibt es vier Busrouten zu jeweils `$2.75`. Wenn Sie jedoch ein einzelnes Ticket für die gesamte Route kaufen, belaufen sich die Kosten auf `$5.50`.

    ```JSON
   {
        "departureTime": "2020-07-22T19:54:47Z",
        "arrivalTime": "2020-07-22T21:12:21Z",
        "legs": [
            {
                "legType": "Wait",
                "legStartTime": "2020-07-22T19:54:47Z",
                "legEndTime": "2020-07-22T19:54:47Z",
                "lineGroup": {
                    "lineGroupId": "522---666063",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "caption1": "Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "caption2": "226 Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads"
                },
                "stops": [
                    {
                        "stopId": "522---2060603",
                        "stopKey": "71300",
                        "stopName": "NE 24th St & 162nd Ave NE",
                        "stopCode": "71300",
                        "position": {
                            "latitude": 47.631504,
                            "longitude": -122.125275
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872",
                        "mainAgencyName": "Metro Transit"
                    },
                    {
                        "stopId": "522---2062263",
                        "stopKey": "85630",
                        "stopName": "Bellevue Tc",
                        "stopCode": "85630",
                        "position": {
                            "latitude": 47.615591,
                            "longitude": -122.196491
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872",
                        "mainAgencyName": "Metro Transit"
                    }
                ],
                "waitOnVehicle": false
            },
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T19:54:47Z",
                "legEndTime": "2020-07-22T20:15:00Z",
                "lineGroup": {
                    "lineGroupId": "522---666063",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "caption1": "Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "caption2": "226 Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads"
                },
                "stops": [
                   ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                       ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "pay"
                        }
                    ]
                }
            },
            ...,
            ...,
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T20:20:00Z",
                "legEndTime": "2020-07-22T20:28:00Z",
                "lineGroup": {
                    "lineGroupId": "522---666071",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "241",
                    "caption1": "Eastgate P&R - Bellevue Transit Center",
                    "caption2": "241 Eastgate P&R - Bellevue Transit Center",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---2756619",
                    "lineGroupId": "522---666071",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "241",
                    "lineDestination": "Eastgate P&R Factoria"
                },
                "stops": [
                ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                   ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "transfer"
                        }
                    ]
                }
            },
            ...,
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T20:31:00Z",
                "legEndTime": "2020-07-22T20:54:13Z",
                "lineGroup": {
                    "lineGroupId": "522---312636",
                    "agencyId": "522---854535",
                    "agencyName": "Sound Transit",
                    "lineNumber": "550",
                    "caption1": "Bellevue - Seattle",
                    "caption2": "550 Bellevue - Seattle",
                    "color": "00008B",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---962201",
                    "lineGroupId": "522---312636",
                    "direction": "backward",
                    "agencyId": "522---854535",
                    "lineNumber": "550",
                    "lineDestination": "Seattle"
                },
                "stops": [
                   ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                 ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "pay"
                        }
                    ]
                }
            },
            ...,
            ...,
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T20:57:00Z",
                "legEndTime": "2020-07-22T21:06:00Z",
                "lineGroup": {
                    "lineGroupId": "522---480518",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "13",
                    "caption1": "Seattle Pacific - Downtown Seattle",
                    "caption2": "13 Seattle Pacific - Downtown Seattle",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---1744932",
                    "lineGroupId": "522---480518",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "lineNumber": "13",
                    "lineDestination": "Seattle Pacific University Seattle Center W"
                },
                "stops": [
                   ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                      ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "transfer"
                        }
                    ]
                }
            },
            ...,
        ],
        "itineraryFare": {
            "price": {
                "amount": 550,
                "currencyCode": "USD"
            },
            "tickets": [
                {
                    "amount": 275,
                    "currencyCode": "USD"
                },
                {
                    "amount": 275,
                    "currencyCode": "USD"
                }
            ]
        }
    }

    ```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie mithilfe der Mobilitätsdienste (Vorschau) Echtzeitdaten anfordern:

> [!div class="nextstepaction"]
> [Anfordern von Echtzeitdaten](how-to-request-real-time-data.md)

Machen Sie sich mit der API-Dokumentation für Azure Maps-Mobilitätsdienste (Vorschau) vertraut:

> [!div class="nextstepaction"]
> [Dokumentation zu Mobilitätsdiensten](/rest/api/maps/mobility)
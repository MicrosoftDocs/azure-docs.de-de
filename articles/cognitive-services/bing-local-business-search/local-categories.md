---
title: Suchkategorien der API für die Bing-Suche nach ortsansässigen Unternehmen
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erfahren Sie, wie Suchkategorien für den Endpunkt der API für die Bing-Suche nach ortsansässigen Unternehmen angegeben werden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 19b769d1fff431f95c20e607c17747f2ff483d2f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487183"
---
# <a name="search-categories-for-the-bing-local-business-search-api"></a>Suchkategorien der API für die Bing-Suche nach ortsansässigen Unternehmen

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang oder bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Mit der API für die Bing-Suche nach ortsansässigen Unternehmen können Sie in einer Vielzahl von Kategorien nach lokalen Geschäftsentitäten suchen. Dabei erhalten die Ergebnisse in der Nähe des Standorts eines Benutzers Priorität. Sie können diese Suchvorgänge zusammen mit den [Parametern](specify-geographic-search.md) `localCircularView` und `localMapView` in Suchvorgänge einschließen.


## <a name="toplevel-categories"></a>Kategorien der obersten Ebene 

Die folgenden Typen definieren die Hauptkategorien der Suche.  Sie können mithilfe einer durch Kommas getrennten Liste, die dem Parameter `localCategories` zugewiesen wird, mehr als eine Kategorie angeben.  
- EatDrink 
- SeeDo 
- Shop 
- HotelsAndMotels 
- BanksAndCreditUnions 
- Parking 
- Hospitals 

## <a name="sub-categories"></a>Unterkategorien
Unterkategorien werden auf die gleiche Weise wie `localCategories` übergeben. Unterkategorien sind spezifischere Kategorien. Die Unterordnung bedeutet: Wenn Sie eine Kategorie C und eine der zugehörigen Unterkategorien S in einer durch Kommas getrennten Liste angeben, erhalten Sie die gleichen Ergebnisse, als ob Sie nur C angegeben hätten.

### <a name="eat-drink"></a>EatDrink

> BreweriesAndBrewPubs, CocktailLounges, AfricanRestaurants, AmericanRestaurants, Bagels, BarbecueRestaurants, Taverns, SportsBars, Bars, BarsGrillsAndPubs, BuffetRestaurants| BelgianRestaurants, BritishRestaurants, CafeRestaurants, CaribbeanRestaurants, ChineseRestaurants, CoffeeAndTea, Delicatessens, DeliveryService, Diners, DiscountStores, Donuts, FastFood, FrenchRestaurants, FrozenYogurt, GermanRestaurants, Supermarkets, GreekRestaurants, Grocers, HawaiianRestaurants, HungarianRestaurants, IceCreamAndFrozenDesserts, IndianRestaurants, ItalianRestaurants, JapaneseRestaurants, Juices, KoreanRestaurants, LiquorStores, MexicanRestaurants, MiddleEasternRestaurants, Pizza, PolishRestaurants, PortugueseRestaurants, Pretzels, Restaurants, RussianAndUkrainianRestaurants, Sandwiches, SeafoodRestaurants, SpanishRestaurants, SteakHouseRestaurants, SushiRestaurants, TakeAway, ThaiRestaurants, TurkishRestaurants, VegetarianAndVeganRestaurants, VietnameseRestaurants

### <a name="see-do"></a>SeeDo

> AmusementParks, Attractions, Carnivals, Casinos, LandmarksAndHistoricalSites, MiniatureGolfCourses, MovieTheaters, Museums, Parks, SightseeingTours, TouristInformation, Zoos

### <a name="shop"></a>Shop

> AntiqueStores, Bookstores, CDAndRecordStores, ChildrensClothingStores, CigarAndTobaccoShops, ComicBookStores, DepartmentStores, DiscountStores, FleaMarketsAndBazaars, FurnitureStores, HomeImprovementStores, JewelryAndWatchesStores, KitchenwareStores, LiquorStores, MallsAndShoppingCenters, MensClothingStores, MusicStores, OutletStores, PetShops, PetSupplyStores, SchoolAndOfficeSupplyStores, ShoeStores, SportingGoodsStores, ToyAndGameStores, VitaminAndSupplementStores, WomensClothingStores


## <a name="examples-of-local-categories-search"></a>Beispiele für die Suche mit Kategorien für ortsansässige Unternehmen

In den folgenden Beispielen werden Ergebnisse mit dem Parameter `localCategories` abgerufen:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=HotelsAndMotels`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=EatDrink`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Shop`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Hospitals`

Die folgende Abfrage begrenzt die Anzahl von Ergebnissen für „hospital“ auf die ersten drei, die von der API für die Bing-Suche nach ortsansässigen Unternehmen zurückgegebenen werden:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localCategories=Hospitals&count=3&offset=0`

Die folgende Beispiel-JSON-Antwort enthält drei Krankenhäuser im Großraum von Seattle:

```json
BingAPIs-TraceId: 68AFB51807C6485CAB8AAF20E232EFFF
BingAPIs-SessionId: F89E7B8539B34BF58AAF811485E83B20
X-MSEdge-ClientID: 1C44E64DBFAA6BCA1270EADDBE7D6A22
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 68AFB51807C6485CAB8AAF20E232EFFF Ref B: CO1EDGE0108 Ref C: 2018-10-22T18:52:28Z

{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": ""
   },
   "places": {
      "readLink": "https:\/\/www.bingapis.com\/api\/v7\/places\/search?q=",
      "totalEstimatedMatches": 0,
      "value": [
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.0",
            "name": "Overlake Hospital Medical Center",
            "url": "http:\/\/www.overlakehospital.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6204986572266,
               "longitude": -122.185829162598
            },
            "routablePoint": {
               "latitude": 47.6204986572266,
               "longitude": -122.185668945312
            },
            "address": {
               "streetAddress": "1035 116th Ave NE",
               "addressLocality": "Bellevue",
               "addressRegion": "WA",
               "postalCode": "98004",
               "addressCountry": "US",
               "neighborhood": "",
               "text": "1035 116th Ave NE, Bellevue, WA, 98004"
            },
            "telephone": "(425) 688-5000"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.1",
            "name": "Virginia Mason University Village Medical Center",
            "url": "https:\/\/virginiamason.org\/Seattle",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6095390319824,
               "longitude": -122.327941894531
            },
            "routablePoint": {
               "latitude": 47.6093978881836,
               "longitude": -122.328277587891
            },
            "address": {
               "streetAddress": "1100 9th Ave",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98101",
               "addressCountry": "US",
               "neighborhood": "University District",
               "text": "1100 9th Ave, Seattle, WA, 98101"
            },
            "telephone": "(206) 223-6600"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.2",
            "name": "Seattle Children’s Hospital",
            "url": "http:\/\/www.seattlechildrens.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6625061035156,
               "longitude": -122.283760070801
            },
            "routablePoint": {
               "latitude": 47.6631507873535,
               "longitude": -122.284614562988
            },
            "address": {
               "streetAddress": "4800 Sand Point Way NE",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98105",
               "addressCountry": "US",
               "neighborhood": "Laurelhurst",
               "text": "4800 Sand Point Way NE, Seattle, WA, 98105"
            },
            "telephone": "(206) 987-2000"
         }
      ],
      "searchAction": {

      }
   }
}
```

## <a name="next-steps"></a>Nächste Schritte
- [Geografische Suchgrenzen](specify-geographic-search.md)
- [Abfrage und Antwort](local-search-query-response.md)
- [Schnellstart in C#](quickstarts/local-quickstart.md)
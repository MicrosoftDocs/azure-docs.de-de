---
title: Preise für VM-Angebote | Azure Marketplace
description: Erläutert die drei Methoden zum Festlegen der Preise für VM-Angebote.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: dde3e04dc8f30ea5657139d50dd4456e5dfb57c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935725"
---
<a name="pricing-for-virtual-machine-offers"></a>Preise für VM-Angebote
==================================

Es gibt drei Möglichkeiten, die Preise für VM-Angebote anzugeben: angepasste Kernpreise, Preise pro Kern und Tabellenpreise.


<a name="customized-core-pricing"></a>Angepasste Kernpreise
-----------------------

Die Preise sind für jede Kombination aus Region und Kern unterschiedlich. Jede Region in der Verkaufsliste muss im **virtualMachinePricing**/**regionPrices**-Abschnitt der Definition angegeben werden.  Verwenden Sie für jede [Region](#regions) in Ihrer Anforderung die richtigen Währungscodes.  Das folgende Beispiel veranschaulicht diese Anforderungen:

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 2,
                    "1core": 2,
                    "2core": 3,
                    "4core": 4,
                    "6core": 5,
                    "8core": 2,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 4,
                    "36core": 4,
                    "40core": 4,
                    "64core": 4,
                    "128core": 4
                }
        }
        ...
     }
```


<a name="per-core-pricing"></a>Preise pro Kern
----------------

In diesem Fall geben die Herausgeber einen Preis in US-Dollar für ihre SKU an, und alle anderen Preise werden automatisch generiert. Der Preis pro Kern wird im **einzelnen** Parameter in der Anforderung angegeben.

``` json
     "virtualMachinePricing": 
     {
         ...
         "coreMultiplier": 
         {
             "currency": "USD",
             "single": 1.0
         }
     }
```


<a name="spreadsheet-pricing"></a>Tabellenpreise
-------------------

Der Herausgeber kann auch seine Preistabelle an einen temporären Speicherort hochladen und dann den URI wie andere Dateiartefakte in die Anforderung einbeziehen. Die Tabelle wird dann hochgeladen, übersetzt, um den angegebenen Preisplan auszuwerten, und aktualisiert schließlich das Angebot mit den Preisinformationen. Nachfolgende GET-Anforderungen für das Angebot geben den Tabellen-URI und die ausgewerteten Preise für die Region zurück.

``` json
     "virtualMachinePricing": 
     {
         ...
         "spreadSheetPricing": 
         {
             "uri": "https://blob.storage.azure.com/<your_spreadsheet_location_here>/prices.xlsx",
         }
     }
```

<a name="regions"></a>Regions
-------

Die folgende Tabelle zeigt die verschiedenen Regionen, die Sie für angepasste Kernpreise angeben können, und ihre entsprechenden Währungscodes.

| **Region** | **Name**             | **Währungscode** |
|------------|----------------------|-------------------|
| DZ         | Algerien              | DZD               |
| AR         | Argentinien            | ARS               |
| AU         | Australien            | AUD               |
| AT         | Österreich              | EUR               |
| BH         | Bahrain              | BHD               |
| BY         | Belarus              | RUB               |
| BE         | Belgien              | EUR               |
| BR         | Brasilien               | USD               |
| BG         | Bulgarien             | BGN               |
| CA         | Kanada               | CAD               |
| CL         | Chile                | CLP               |
| CO         | Kolumbien             | COP               |
| CR         | Costa Rica           | CRC               |
| HR         | Kroatien              | HRK               |
| CY         | Zypern               | EUR               |
| CZ         | Tschechische Republik       | CZK               |
| DK         | Dänemark              | DKK               |
| DO         | Dominikanische Republik   | USD               |
| EC         | Ecuador              | USD               |
| EG         | Ägypten                | EGP               |
| SV         | El Salvador          | USD               |
| EE         | Estland              | EUR               |
| FI         | Finnland              | EUR               |
| BV         | Frankreich               | EUR               |
| DE         | Deutschland              | EUR               |
| GR         | Griechenland               | EUR               |
| GT         | Guatemala            | GTQ               |
| HK         | Hongkong (SAR)        | HKD               |
| HU         | Ungarn              | HUF               |
| IS         | Island              | ISK               |
| IN         | Indien                | INR               |
| ID         | Indonesien            | IDR               |
| IE         | Irland              | EUR               |
| IL         | Israel               | ILS               |
| IT         | Italien                | EUR               |
| JP         | Japan                | JPY               |
| JO         | Jordan               | JOD               |
| KZ         | Kasachstan           | KZT               |
| KE         | Kenia                | KES               |
| KR         | Korea                | KRW               |
| KW         | Kuwait               | KWD               |
| LV         | Lettland               | EUR               |
| LI         | Liechtenstein        | CHF               |
| LT         | Litauen            | EUR               |
| LU         | Luxemburg           | EUR               |
| MK         | Nordmazedonien      | MKD               |
| MY         | Malaysia             | MYR               |
| MT         | Malta                | EUR               |
| MX         | Mexiko               | MXN               |
| ME         | Montenegro           | EUR               |
| MA         | Marokko              | MAD               |
| NL         | Niederlande          | EUR               |
| NZ         | Neuseeland          | NZD               |
| NG         | Nigeria              | NGN               |
| NO         | Norwegen               | NOK               |
| OM         | Oman                 | OMR               |
| PK         | Pakistan             | PKR               |
| PA         | Panama               | USD               |
| PY         | Paraguay             | PYG               |
| PE         | Peru                 | PEN               |
| PH         | Philippinen          | PHP               |
| PL         | Polen               | PLN               |
| PT         | Portugal             | EUR               |
| PR         | Puerto Rico          | USD               |
| QA         | Katar                | QAR               |
| RO         | Rumänien              | RON               |
| RU         | Russland               | RUB               |
| SA         | Saudi-Arabien         | SAR               |
| RS         | Serbien               | RSD               |
| SG         | Singapur            | SGD               |
| SK         | Slowakei             | EUR               |
| SI         | Slowenien             | EUR               |
| ZA         | Südafrika         | ZAR               |
| ES         | Spanien                | EUR               |
| LK         | Sri Lanka            | USD               |
| SE         | Schweden               | SEK               |
| CH         | Schweiz          | CHF               |
| TW         | Taiwan               | TWD               |
| TH         | Thailand             | THB               |
| TT         | Trinidad und Tobago  | TTD               |
| TN         | Tunesien              | TND               |
| TR         | Türkei               | TRY               |
| UA         | Ukraine              | UAH               |
| AE         | Vereinigte Arabische Emirate | EUR               |
| GB         | Vereinigtes Königreich       | GBP               |
| US         | USA        | USD               |
| UY         | Uruguay              | UYU               |
| VE         | Venezuela            | USD               |
|  |  |  |

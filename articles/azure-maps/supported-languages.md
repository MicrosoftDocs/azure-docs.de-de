---
title: Unterstützte Sprachen in Microsoft Azure Maps
description: Sehen Sie sich an, welche Regionen Azure Maps mit Diensten für Karten, Suchvorgänge, Routen, Wetter und Verkehrsmeldungen unterstützt. Erfahren Sie, wie Sie den View-Parameter einrichten.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 50e5d0721eb14d1fcdfad26aaf081bfa370e954e
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96904515"
---
# <a name="localization-support-in-azure-maps"></a>Unterstützte Sprachen in Azure Maps

Azure Maps unterstützt basierend auf Land/Region verschiedene Sprachen und Ansichten. Dieser Artikel enthält die unterstützten Sprachen und Ansichten, die Ihnen bei der Implementierung von Azure Maps helfen.


## <a name="azure-maps-supported-languages"></a>Unterstützte Sprachen in Azure Maps

Azure Maps wurde in verschiedene Sprachen für die unterschiedlichen Dienste übersetzt. In der folgenden Tabelle finden Sie die unterstützten Sprachcodes für jeden Dienst.  
  

| id         | Name                   |  Karten | Suchen, | Routing | Weather | Verkehrsmeldungen | JS-Kartensteuerelement |
|------------|------------------------|:-----:|:------:|:-------:|:--------:|:-----------------:|:--------------:|
| af-ZA      | Afrikaans              |       |    ✓   |    ✓    |         |                   |                |
| ar-SA      | Arabisch                 |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| bn-BD      | Bangla (Bangladesch)    |       |       |         |     ✓    |                   |                |
| bn-IN      | Bangla (Indien)         |       |       |         |     ✓    |                   |                |
| bs-BA      | Bosnisch                 |       |       |         |     ✓    |                   |                |
| eu-ES      | Baskisch                 |       |    ✓   |         |         |                   |                |
| bg-BG      | Bulgarisch              |   ✓   |    ✓   |    ✓    |     ✓     |                   |        ✓       |
| ca-ES      | Katalanisch                |       |    ✓   |         |    ✓      |                   |                |
| zh-HanS    | Chinesisch (vereinfacht)   |       |  zh-CN |         |     zh-CN   |                   |                |
| zh-HanT    | Chinesisch (Hongkong SAR)  |  |   |    |    zh-HK   |                   |           |
| zh-HanT    | Chinesisch (Taiwan)  | zh-TW |  zh-TW |  zh-TW  |    zh-TW   |                   |      zh-TW     |
| hr-HR      | Kroatisch               |       |    ✓   |         |    ✓      |                   |                |
| cs-CZ      | Tschechisch                  |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| da-DK      | Dänisch                 |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| nl-BE      | Niederländisch (Belgien)        |       |    ✓   |         |      ✓    |                   |                |
| nl-NL      | Niederländisch (Niederlande)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-AU      | Englisch (Australien)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-NZ      | Englisch (Neuseeland)  |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-GB      | Englisch (Großbritannien) |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| de-DE      | Englisch (USA)          |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| et-EE      | Estnisch               |       |    ✓   |         |      ✓    |         ✓         |                |
| fil-PH     | Filipino               |       |       |         |     ✓    |                   |                |
| fi-FI      | Finnisch                |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-FR      | Französisch                 |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-CA      | Französisch (Kanada)      |       |    ✓   |         |     ✓     |                   |                |
| gl-ES      | Galizisch               |       |    ✓   |         |         |                   |                |
| de-DE      | Deutsch                 |   ✓   |    ✓   |    ✓    |   ✓      |         ✓         |        ✓       |
| el-GR      | Griechisch                  |   ✓   |    ✓   |    ✓    |    ✓     |         ✓         |        ✓       |
| gu-IN      | Gujarati                |       |       |         |     ✓    |                   |                |
| he-IL      | Hebräisch                 |       |    ✓   |         |     ✓    |         ✓         |                |
| hi-IN      | Hindi                  |       |        |         |     ✓    |                   |                |
| hu-HU      | Ungarisch              |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| is-IS      | Isländisch              |       |       |         |     ✓    |                   |                |
| id-ID      | Indonesisch             |   ✓   |    ✓    |    ✓    |     ✓    |         ✓         |        ✓       |
| it-IT      | Italienisch                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| ja-JP      | Japanisch               |       |        |         |     ✓    |                   |                |
| kn-IN      | Kannada                |       |       |         |     ✓    |                   |                |
| kk-KZ      | Kasachisch                 |       |    ✓   |         |     ✓    |                   |                |
| ko-KR      | Koreanisch                 |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| es-419     | Spanisch (Lateinamerika) |       |    ✓   |         |         |                   |                |
| lv-LV      | Lettisch                |       |    ✓   |         |     ✓    |         ✓         |                |
| lt-LT      | Litauisch             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| mk-MK      | Mazedonisch             |       |       |         |     ✓    |                   |                |
| ms-MY      | Malaiisch (Lateinisch)          |   ✓   |    ✓   |    ✓    |    ✓   |                   |        ✓       |
| mr-IN      | Marathi                 |       |       |         |     ✓    |                   |                |
| nb-NO      | Norwegisch (Bokmål)       |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| NGT        | Neutral Ground Truth – Offizielle Sprachen für alle Regionen in lokalen Schriften, falls vorhanden |   ✓     |        |         |       |        |      ✓          |
| NGT-Latn   | Neutral Ground Truth – Lateinische Exonyme Lateinische Schrift wird verwendet, wenn verfügbar |   ✓     |        |         |         |                |        ✓         |
| pl-PL      | Polnisch                 |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| pt-BR      | Portugiesisch (Brasilien)    |   ✓   |    ✓   |    ✓    |      ✓   |                   |        ✓       |
| pt-PT      | Portugiesisch (Portugal)  |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| pa-IN      | Pandschabi                 |       |       |         |     ✓    |                   |                |
| ro-RO      | Rumänisch               |       |    ✓    |         |     ✓    |         ✓         |                |
| ru-RU      | Russisch                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| sr-Cyrl-RS | Serbisch (Kyrillisch)     |       |   sr-RS  |         |    sr-RS     |                   |                |
| sr-Latn-RS | Serbisch (Lateinisch)        |       |       |         |     sr-latn    |                   |                |
| sk-SK      | Slowakisch             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| sl-SL      | Slowenisch              |   ✓   |    ✓   |    ✓    |     ✓    |                   |        ✓       |
| es-ES      | Spanisch                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| es-MX      | Spanisch (Mexiko)       |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| sv-SE      | Schwedisch                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| ta-IN      | Tamil (Indien)                 |       |       |         |     ✓    |                   |                |
| te-IN      | Telugu (Indien)                 |       |       |         |     ✓    |                   |                |
| th-TH      | Thailändisch                   |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| tr-TR      | Türkisch                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| uk-UA      | Ukrainisch               |       |    ✓   |         |     ✓    |                   |                |
| ur-PK      | Urdu                 |       |       |         |     ✓    |                   |                |
| uz-Latn-UZ | Usbekisch                 |       |       |         |     ✓    |                   |                |
| vi-VN      | Vietnamesisch             |       |    ✓   |         |      ✓    |                  |                |


## <a name="azure-maps-supported-views"></a>Unterstützte Ansichten in Azure Maps

> [!Note]
> Am 1. August 2019 wurde Azure Maps in den folgenden Ländern/Regionen veröffentlicht:
>  * Argentinien
>  * Indien
>  * Marokko
>  * Pakistan
>
> Nach dem 1. August 2019 wird mit dem Parameter **View** der zurückgegebene Karteninhalt für die oben aufgeführten neuen Regionen/Länder definiert. Der **View**-Parameter von Azure Maps (auch „Benutzerregionsparameter“ genannt) ist ein zweistelliger ISO-3166-Ländercode, der die richtigen Karten für dieses Land/diese Region anzeigt und angibt, welche geopolitisch umstrittenen Inhalte über die Dienste von Azure Maps zurückgegeben werden, einschließlich der auf der Karte angezeigten Grenzen und Bezeichnungen. 

Stellen Sie sicher, dass Sie den Parameter **View** gemäß den Anforderungen für die von Ihren Diensten verwendeten REST-APIs und SDKs eingerichtet haben.
  

### <a name="rest-apis"></a>REST-APIs
  
Stellen Sie sicher, dass Sie den View-Parameter wie erforderlich eingerichtet haben. Der View-Parameter gibt an, welcher Satz von geopolitisch umstrittenen Inhalten über die Azure Maps-Dienste zurückgegeben wird. 

Betroffene Azure Maps REST-Dienste:
    
 * Get Map Tile (Kartenkachel abrufen)
 * Get Map Image (Kartenbild abrufen) 
 * Get Search Fuzzy (Fuzzy für die Suche abrufen)
 * Get Search POI (POI für die Suche abrufen)
 * Get Search POI Category (POI-Kategorie für die Suche abrufen)
 * Get Search Nearby (Suche in der Nähe abrufen)
 * Get Search Address (Suchadresse abrufen)
 * Get Search Address Structured (Suchadresse strukturiert abrufen)
 * Get Search Address Reverse (Suchadresse invers abrufen)
 * Get Search Address Reverse Cross Street (Suchadresse für Querstraße invers abrufen)
 * Post Search Inside Geometry (Suche innerhalb der Geometrie veröffentlichen)
 * Post Search Address Batch (Batch für Suchadresse veröffentlichen)
 * Post Search Address Reverse Batch (Batch für inverse Suchadresse veröffentlichen)
 * Post Search Along Route (Suche entlang einer Route veröffentlichen)
 * Post Search Fuzzy Batch (Batch für Fuzzysuche veröffentlichen)

 
### <a name="sdks"></a>SDKs

Stellen Sie sicher, dass Sie den Parameter **View** wie erforderlich eingerichtet haben und über die neueste Version des Web SDK und des Android SDK verfügen. Betroffene SDKs:

 * Azure Maps Web SDK
 * Azure Maps Android SDK

Standardmäßig ist der Parameter „View“ auf **Unified** festgelegt, auch wenn Sie ihn in der Anforderung nicht definiert haben. Bestimmen Sie den Standort Ihrer Benutzer. Legen Sie anschließend den Parameter **View** ordnungsgemäß für diesen Standort fest. Alternativ können Sie auch „View=Auto“ festlegen. Hierbei werden die Kartendaten basierend auf der IP-Adresse der Anforderung zurückgegeben.  Der Parameter **View** in Azure Maps muss in Übereinstimmung mit geltenden Gesetzen verwendet werden. Dies gilt auch für die Gesetze in Bezug auf die Kartierung des Landes oder der Region, über die Karten, Bilder und andere Daten und Inhalte von Drittanbietern, auf die Sie über Azure Maps zugreifen dürfen, bereitgestellt werden.


Die folgende Tabelle enthält einige unterstützte Ansichten.

| Sicht         | BESCHREIBUNG                            |  Karten | Suchen, | JS-Kartensteuerelement |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Vereinigte Arabische Emirate (arabische Ansicht)    |   ✓   |        |     ✓          |
| AR           | Argentinien (argentinische Ansicht)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrain (arabische Ansicht)                 |   ✓   |        |     ✓          |
| IN           | Indien (indische Ansicht)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Irak (arabische Ansicht)                    |   ✓   |        |     ✓          |
| JO           | Jordanien (arabische Ansicht)                  |   ✓   |        |     ✓          |
| KW           | Kuwait (arabische Ansicht)                  |   ✓   |        |     ✓          |
| LB           | Libanon (arabische Ansicht)                 |   ✓   |        |     ✓          |
| NI           | Marokko (marokkanische Ansicht)                |   ✓   |   ✓     |     ✓          |
| OM           | Oman (arabische Ansicht)                    |   ✓   |        |     ✓          |
| PK           | Pakistan (pakistanische Ansicht)              |   ✓   |    ✓    |     ✓          |
| PS           | Palästinensische Autonomiegebiete (arabische Ansicht)    |   ✓   |        |     ✓          |
| QA           | Katar (arabische Ansicht)                   |   ✓   |        |     ✓          |
| SA           | Saudi-Arabien (arabische Ansicht)            |   ✓   |        |     ✓          |
| SY           | Syrien (arabische Ansicht)                   |   ✓   |        |     ✓          |
| YE           | Jemen (arabische Ansicht)                   |   ✓   |        |     ✓          |
| Auto         | Gibt die Kartendaten basierend auf der IP-Adresse der Anforderung zurück.|   ✓   |    ✓   |     ✓          |
| Einheitlich      | Einheitliche Ansicht (andere)                  |   ✓   |   ✓     |     ✓          |

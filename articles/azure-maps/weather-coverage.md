---
title: Microsoft Azure Maps-Wetterdienste (Vorschau)
description: Lernen Sie die Microsoft Azure Maps-Wetterdienste (Vorschau) kennen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.custom: references_regions
manager: philmea
ms.openlocfilehash: 6c4e9eb765a72b7a0b495f81a954b484ef6aa2b7
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905484"
---
# <a name="azure-maps-weather-services-preview-coverage"></a>Azure Maps-Wetterdienste (Vorschau)

> [!IMPORTANT]
> Die Azure Maps-Wetterdienste befinden sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


In diesem Artikel finden Sie Informationen zum Umfang der [Wetterdienste](/rest/api/maps/weather) von Azure Maps. Azure Maps-Wetterdatendienste geben Details wie Radarkacheln, aktuelle Wetterbedingungen, Wettervorhersagen und das Wetter entlang einer Strecke zurück.

Azure Maps verfügt nicht für alle Länder und Regionen über das gleiche Maß an Informationen und Genauigkeit.

In der folgenden Tabelle finden Sie Informationen zu der Art von Wetterinformationen, die Sie aus einem jeweiligen Land oder einer Region anfordern können.

| Symbol | Bedeutung |
|--------|---------|
|*       |Umfasst aktuelle Bedingungen, stündliche Vorhersagen, Vorhersagen in 6-Stunden-Intervallen, tägliche Vorhersagen, Weather Along Route-Indizes und tägliche Indizes. |


## <a name="americas"></a>Amerika

| Land/Region              |  Satellitenkacheln | Minütliche Vorhersagen, Radarkacheln | Unwetterwarnungen | Sonstige* |
|-----------------------------|:----------------:|:-----------------:|:-----------------:|:--------:|  
| Anguilla                                 | ✓ |   | |  ✓| 
| Antarktis                               | ✓ |   | |✓|
| Antigua und Barbuda                      | ✓ |   | |✓| 
| Argentinien                                | ✓ |   | |✓| 
| Aruba                                    | ✓ |   | |✓| 
| Bahamas                                  | ✓ |   | |✓| 
| Barbados                                 | ✓ |   | |✓| 
| Belize                                   | ✓ |   | |✓| 
| Bermudas                                  | ✓ |   | |✓| 
| Bolivien                                  | ✓ |   | |✓| 
| Bonaire                                  | ✓ |   | |✓| 
| Brasilien                                   | ✓ |   | ✓ |✓| 
| Britische Jungferninseln                   | ✓ |   | |✓| 
| Canada                                   | ✓ | ✓ | ✓ | ✓| 
| Kaimaninseln                           | ✓ |   | |✓| 
| Chile                                    | ✓ |   | |✓| 
| Kolumbien                                 | ✓ |   | |✓| 
| Costa Rica                               | ✓ |   | |✓| 
| Kuba                                     | ✓ |   | |✓| 
| Curaçao                                  | ✓ |   | |✓| 
| Dominica                                 | ✓ |   | |✓| 
| Dominikanische Republik                       | ✓ |   | |✓| 
| Ecuador                                  | ✓ |   | |✓| 
| El Salvador                              | ✓ |   | |✓| 
| Falklandinseln                         | ✓ |   | |✓| 
| Französisch-Guayana                            | ✓ |   | |✓| 
| Grönland                                | ✓ |   | |✓| 
| Grenada                                  | ✓ |   | |✓| 
| Guadeloupe                               | ✓ |   | |✓| 
| Guatemala                                | ✓ |   | |✓| 
| Guayana                                   | ✓ |   | |✓| 
| Haiti                                    | ✓ |   | |✓| 
| Honduras                                 | ✓ |   | |✓| 
| Jamaika                                  | ✓ |   | |✓| 
| Martinique                               | ✓ |   | |✓| 
| Mexiko                                   | ✓ |   | |✓| 
| Montserrat                               | ✓ |   | |✓| 
| Nicaragua                                | ✓ |   | |✓| 
| Panama                                   | ✓ |   | |✓| 
| Paraguay                                 | ✓ |   | |✓| 
| Peru                                     | ✓ |   | |✓| 
| Puerto Rico                              | ✓ |   | ✓ |✓| 
| St. Barthélemy                         | ✓ |   | |✓| 
| St. Kitts und Nevis                    | ✓ |   | |✓| 
| St. Lucia                              | ✓ |   | |✓| 
| St. Martin                             | ✓ |   | |✓| 
| St. Pierre und Miquelon                | ✓ |   | |✓| 
| St. Vincent und die Grenadinen         | ✓ |   | |✓| 
| Sint Eustatius                           | ✓ |   | |✓|  
| Sint Maarten                             | ✓ |   | |✓| 
| Südgeorgien und Südliche Sandwichinseln | ✓ |   | |✓| 
| Surinam                                 | ✓ |   | |✓| 
| Trinidad und Tobago                      | ✓ |   | |✓| 
| Turks- und Caicosinseln                 | ✓ |   | |✓| 
| USA Amerikanische Überseeinseln                    | ✓ |   | |✓| 
| USA Jungferninseln                      | ✓ |   | ✓|✓| 
| USA                            | ✓ | ✓ | ✓| ✓| 
| Uruguay                                  | ✓ |   | |✓| 
| Venezuela                                | ✓ |   | |✓| 


## <a name="middle-east-and-africa"></a>Naher Osten und Afrika

| Land/Region              |  Satellitenkacheln | Minütliche Vorhersagen, Radarkacheln | Unwetterwarnungen | Sonstige* | 
|-----------------------------|:----------------:|:-----------------:|:--------:|:--------:| 
| Algerien                     | ✓               |                              |     |  ✓| 
| Angola                      | ✓               |                              |     |  ✓| 
| Bahrain                     | ✓               |                              |     |  ✓| 
| Benin                       | ✓               |                              |     |  ✓| 
| Botsuana                    | ✓               |                              |     |  ✓| 
| Bouvetinsel               | ✓               |                              |     |  ✓| 
| Burkina Faso                | ✓               |                              |     |  ✓| 
| Burundi                     | ✓               |                              |     |  ✓| 
| Kamerun                    | ✓               |                              |     |  ✓| 
| Cabo Verde                  | ✓               |                              |     |  ✓| 
| Zentralafrikanische Republik    | ✓               |                              |     |  ✓| 
| Tschad                        | ✓               |                              |     |  ✓| 
| Komoren                     | ✓               |                              |     |  ✓| 
| Kongo (DRC)                 | ✓               |                              |     |  ✓|
| Côte d'Ivoire               | ✓               |                              |     |  ✓| 
| Dschibuti                    | ✓               |                              |     |  ✓| 
| Ägypten                       | ✓               |                              |     |  ✓| 
| Äquatorialguinea           | ✓               |                              |     |  ✓| 
| Eritrea                     | ✓               |                              |     |  ✓| 
| eSwatini                    | ✓               |                              |     |  ✓| 
| Äthiopien                    | ✓               |                              |     |  ✓| 
| Französische Gebiete im südlichen Indischen Ozean | ✓               |                              |     |  ✓| 
| Gabun                       | ✓               |                              |     |  ✓| 
| Gambia                      | ✓               |                              |     |  ✓| 
| Ghana                       | ✓               |                              |     |  ✓| 
| Guinea                      | ✓               |                              |     |  ✓| 
| Guinea-Bissau               | ✓               |                              |     |  ✓| 
| Iran                        | ✓               |                              |     |  ✓| 
| Irak                        | ✓               |                              |     |  ✓| 
| Israel                      | ✓               |                              |   ✓   |  ✓| 
| Jordan                      | ✓               |                              |     |  ✓| 
| Kenia                       | ✓               |                              |     |  ✓| 
| Kuwait                      | ✓               |                              |     |  ✓| 
| Libanon                     | ✓               |                              |     |  ✓| 
| Lesotho                     | ✓               |                              |     |  ✓| 
| Liberia                     | ✓               |                              |     |  ✓| 
| Libyen                       | ✓               |                              |     |  ✓| 
| Madagaskar                  | ✓               |                              |     |  ✓| 
| Malawi                      | ✓               |                              |     |  ✓| 
| Mali                        | ✓               |                              |     |  ✓| 
| Mauretanien                  | ✓               |                              |     |  ✓| 
| Mauritius                   | ✓               |                              |     |  ✓| 
| Mayotte                     | ✓               |                              |     |  ✓| 
| Marokko                     | ✓               |                              |     |  ✓| 
| Mosambik                  | ✓               |                              |     |  ✓| 
| Namibia                     | ✓               |                              |     |  ✓| 
| Niger                       | ✓               |                              |     |  ✓| 
| Nigeria                     | ✓               |                              |     |  ✓| 
| Oman                        | ✓               |                              |     |  ✓| 
| Palästinensische Autonomiegebiete       | ✓               |                              |     |  ✓| 
| Katar                       | ✓               |                              |     |  ✓| 
| Réunion                     | ✓               |                              |     |  ✓| 
| Ruanda                      | ✓               |                              |     |  ✓| 
| St. Helena, Ascension, Tristan da Cunha        | ✓               |            |     |  ✓| 
| São Tomé und Príncipe       | ✓               |                              |     |  ✓| 
| Saudi-Arabien                | ✓               |                              |     |  ✓| 
| Senegal                     | ✓               |                              |     |  ✓| 
| Seychellen                  | ✓               |                              |     |  ✓| 
| Sierra Leone                | ✓               |                              |     |  ✓| 
| Somalia                     | ✓               |                              |     |  ✓| 
| Südafrika                | ✓               |                              |     |  ✓| 
| Südsudan                 | ✓               |                              |     |  ✓| 
| Sudan                       | ✓               |                              |     |  ✓| 
| Syrien                       | ✓               |                              |     |  ✓| 
| Tansania                    | ✓               |                              |     |  ✓| 
| Togo                        | ✓               |                              |     |  ✓| 
| Tunesien                     | ✓               |                              |     |  ✓| 
| Uganda                      | ✓               |                              |     |  ✓| 
| Vereinigte Arabische Emirate        | ✓               |                              |     |  ✓| 
| Jemen                       | ✓               |                              |     |  ✓| 
| Sambia                      | ✓               |                              |     |  ✓| 
| Simbabwe                    | ✓               |                              |     |  ✓| 


## <a name="asia-pacific"></a>Asien-Pazifik

| Land/Region              |  Satellitenkacheln | Minütliche Vorhersagen, Radarkacheln | Unwetterwarnungen | Sonstige* |
|-----------------------------|:----------------:|:-----------------:|:--------:|  :--------:|
| Afghanistan                       | ✓ |   | | ✓| 
| Amerikanisch-Samoa                    | ✓ |   |  ✓| ✓| 
| Australien                         | ✓ | ✓ |  ✓ | ✓| 
| Bangladesch                        | ✓ |   | | ✓| 
| Bhutan                            | ✓ |   | | ✓| 
| Britisches Territorium im Indischen Ozean    | ✓ |   | | ✓| 
| Brunei                            | ✓ |   | | ✓| 
| Kambodscha                          | ✓ |   | | ✓| 
| China                             | ✓ | ✓ |  ✓ | ✓| 
| Weihnachtsinsel                  | ✓ |   | | ✓| 
| Kokosinseln           | ✓ |   | | ✓| 
| Cookinseln                      | ✓ |   | | ✓| 
| Fidschi                              | ✓ |   | | ✓| 
| Französisch-Polynesien                  | ✓ |   | | ✓| 
| Guam                              | ✓ |   |  ✓| ✓| 
| Heard und McDonaldinseln | ✓ |   | | ✓| 
| Hongkong (SAR)                     | ✓ |   | | ✓| 
| Indien                             | ✓ |   | | ✓| 
| Indonesien                         | ✓ |   | | ✓| 
| Japan                             | ✓ | ✓ |  ✓| ✓| 
| Kasachstan                        | ✓ |   | | ✓| 
| Kiribati                          | ✓ |   | | ✓| 
| Korea                             | ✓ | ✓ | ✓ |  ✓| 
| Kirgisistan                        | ✓ |   | | ✓| 
| Laos                              | ✓ |   | | ✓| 
| Macau (SAR)                         | ✓ |   | | ✓| 
| Malaysia                          | ✓ |   | | ✓| 
| Malediven                          | ✓ |   | | ✓| 
| Marshallinseln                  | ✓ |   | ✓ | ✓| 
| Mikronesien                        | ✓ |   | ✓ | ✓| 
| Mongolei                          | ✓ |   | | ✓| 
| Myanmar                           | ✓ |   | | ✓| 
| Nauru                             | ✓ |   | | ✓| 
| Nepal                             | ✓ |   | | ✓| 
| Neukaledonien                     | ✓ |   | | ✓| 
| Neuseeland                       | ✓ |   | ✓ | ✓| 
| Niue                              | ✓ |   | | ✓| 
| Norfolkinsel                    | ✓ |   | | ✓| 
| Nordkorea                       | ✓ |   | | ✓| 
| Nördliche Marianen          | ✓ |   | ✓ | ✓| 
| Pakistan                          | ✓ |   | | ✓| 
| Palau                             | ✓ |   | ✓ | ✓| 
| Papua-Neuguinea                  | ✓ |   | | ✓| 
| Philippinen                       | ✓ |   | ✓ | ✓| 
| Pitcairninseln                  | ✓ |   | | ✓| 
| Samoa                             | ✓ |   | | ✓| 
| Singapur                         | ✓ |   | | ✓| 
| Salomonen                   | ✓ |   | | ✓| 
| Sri Lanka                         | ✓ |   | | ✓| 
| Taiwan                            | ✓ |   | | ✓| 
| Tadschikistan                        | ✓ |   | | ✓| 
| Thailand                          | ✓ |   | | ✓| 
| Timor-Leste                       | ✓ |   | | ✓| 
| Tokelau                           | ✓ |   | | ✓| 
| Tonga                             | ✓ |   | | ✓| 
| Turkmenistan                      | ✓ |   | | ✓| 
| Tuwalu                            | ✓ |   | | ✓| 
| Usbekistan                        | ✓ |   | | ✓| 
| Vanuatu                           | ✓ |   | | ✓| 
| Vietnam                           | ✓ |   | | ✓| 
| Wallis und Futuna                 | ✓ |   | | ✓| 


## <a name="europe"></a>Europa

| Land/Region              |  Satellitenkacheln | Minütliche Vorhersagen, Radarkacheln | Unwetterwarnungen | Sonstige* | 
|-----------------------------|:----------------:|:-----------------:|:--------:|:--------:|
| Albanien                | ✓ |   | | ✓| 
| Andorra                | ✓ |   | ✓ | ✓| 
| Armenien                | ✓ |   | | ✓| 
| Österreich                | ✓ | ✓ | ✓ | ✓|
| Aserbaidschan             | ✓ |   | | ✓| 
| Belarus                | ✓ |   | | ✓| 
| Belgien                | ✓ | ✓ |  ✓| ✓| 
| Bosnien und Herzegowina | ✓ | ✓ | ✓ | ✓| 
| Bulgarien               | ✓ |   |  ✓| ✓| 
| Kroatien                | ✓ | ✓ |  ✓| ✓| 
| Zypern                 | ✓ |   | ✓ | ✓| 
| Tschechien                | ✓ | ✓ | ✓ | ✓| 
| Dänemark                | ✓ | ✓ | ✓ | ✓| 
| Estland                | ✓ | ✓ |  ✓| ✓| 
| Färöer          | ✓ |   | | ✓| 
| Finnland                | ✓ | ✓ | ✓ | ✓| 
| Frankreich                 | ✓ | ✓ | ✓ | ✓| 
| Georgien                | ✓ |   | | ✓| 
| Deutschland                | ✓ | ✓ | ✓ | ✓| 
| Gibraltar              | ✓ | ✓ | | ✓| 
| Griechenland                 | ✓ |   |  ✓| ✓| 
| Guernsey               | ✓ |   | | ✓| 
| Ungarn                | ✓ | ✓ |  ✓| ✓| 
| Island                | ✓ |   | ✓ | ✓| 
| Irland                | ✓ | ✓ |  ✓| ✓| 
| Italien                  | ✓ |   |  ✓| ✓|
| Insel Man            | ✓ |   | | ✓| 
| Jan Mayen              | ✓ |   | | ✓| 
| Jersey                 | ✓ |   | | ✓| 
| Kosovo                 | ✓ |   |  ✓| ✓| 
| Lettland                 | ✓ |   | ✓ | ✓| 
| Liechtenstein          | ✓ | ✓ |  ✓| ✓| 
| Litauen              | ✓ |   | ✓ | ✓| 
| Luxemburg             | ✓ | ✓ |  ✓| ✓| 
| Nordmazedonien        | ✓ |   | ✓ | ✓| 
| Malta                  | ✓ |   | ✓ | ✓| 
| Moldau                | ✓ | ✓ | ✓ | ✓| 
| Monaco                 | ✓ | ✓ |  ✓| ✓| 
| Montenegro             | ✓ | ✓ |  ✓| ✓| 
| Niederlande            | ✓ | ✓ |  ✓| ✓| 
| Norwegen                 | ✓ | ✓ |  ✓| ✓| 
| Polen                 | ✓ | ✓ |  ✓| ✓| 
| Portugal               | ✓ | ✓ |  ✓| ✓| 
| Rumänien                | ✓ | ✓ |  ✓| ✓| 
| Russland                 | ✓ |   |  ✓| ✓| 
| San Marino             | ✓ |   |  ✓| ✓| 
| Serbien                 | ✓ | ✓ |  ✓| ✓| 
| Slowakei               | ✓ | ✓ |  ✓| ✓| 
| Slowenien               | ✓ | ✓ |  ✓| ✓| 
| Spanien                  | ✓ | ✓ |  ✓| ✓| 
| Spitzbergen               | ✓ |   | | ✓|
| Schweden                 | ✓ | ✓ |  ✓| ✓| 
| Schweiz            | ✓ | ✓ | ✓| ✓| 
| Türkei                 | ✓ |   | | ✓| 
| Ukraine                | ✓ |   | | ✓| 
| United Kingdom         | ✓ | ✓ | ✓| ✓| 
| Vatikanstadt           | ✓ |   |✓ | ✓|
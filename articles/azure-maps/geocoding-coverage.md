---
title: Geocodierungsabdeckung im Microsoft Azure Maps-Suchdienst
description: Sehen Sie, welche Regionen von der Azure Maps-Suche abgedeckt werden. Zu den Geocodierungskategorien zählen Adresspunkte, Hausnummern, Straße, Ort und Points of Interest.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/28/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3cf27319fa84ff5d693e74e90664b7a86f4d934a
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897210"
---
# <a name="azure-maps-geocoding-coverage"></a>Geocodierungsabdeckung in Azure Maps

Der Azure Maps-[Suchdienst](/rest/api/maps/search) unterstützt Geocodierung, was bedeutet, dass Ihre API-Anforderung Suchbegriffe wie eine Adresse oder den Namen eines Orts enthalten kann, woraufhin das Ergebnis als Koordinaten mit Breiten- und Längengraden zurückgegeben wird. Beispielsweise empfängt die [API zum Abrufen von Suchadressen](/rest/api/maps/search/getsearchaddress) von Azure Maps Abfragen, die Standortinformationen enthalten, und gibt Ergebnisse als Koordinaten mit Breiten- und Längengraden zurück.

Der Azure Maps-[Suchdienst](/rest/api/maps/search) bietet allerdings nicht für alle Regionen und Länder das selbe Maß an Informationen und Genauigkeit. Bestimmen Sie anhand dieses Artikels, nach welcher Art von Orten Sie zuverlässig in den einzelnen Regionen suchen können.

Die Möglichkeit der Geocodierung in einem Land/einer Region hängt von der Abdeckung der Straßendaten und der Geocodierungsgenauigkeit des Geocodierungsdiensts ab. Die jeweilige Unterstützung der Geocodierung in den einzelnen Ländern/Regionen wird mit den folgenden Kategorisierungen angegeben.

* **Adresspunkte:** Adressdaten können innerhalb des Adresspakets (Eigenschaftsgrenze) in Koordinaten mit Breiten-/Längengraden aufgelöst werden. Adresspunkte werden oft als „hausgenau" bezeichnet. Dies ist die höchste verfügbare Genauigkeit für Adressen.
* **Hausnummern:** Adressen werden in eine Längen- und Breitenkoordinate in der Straße interpoliert.
* **Straße:** Adressen werden in die Längen- und Breitenkoordinate der Straße mit der Adresse aufgelöst. Die Hausnummer wird möglicherweise nicht verarbeitet.
* **Ort:** Ortsnamen werden nicht unterstützt.

## <a name="americas"></a>Amerika

| Land/Region                                       | Adresspunkte | Hausnummern | Straße | Ort | POIs |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Anguilla                                            |                 |                |              |      ✓     |          ✓         |
| Antarktis                                          |                 |                |              |      ✓     |          ✓         |
| Antigua und Barbuda                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Argentinien                                           |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Aruba                                               |                 |                |              |      ✓     |          ✓         |
| Bahamas                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Barbados                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Belize                                              |                 |                |              |      ✓     |          ✓         |
| Bermudas                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bolivien                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bonaire, Sint Eustatius und Saba                   |                 |                |              |      ✓     |          ✓         |
| Brasilien                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Canada                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kaimaninseln                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Chile                                               |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Kolumbien                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Costa Rica                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Kuba                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Dominica                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Dominikanische Republik                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Ecuador                                             |                 |                |       ✓      |      ✓     |          ✓         |
| El Salvador                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Falklandinseln                                    |                 |                |              |      ✓     |          ✓         |
| Französisch-Guayana                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Grenada                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Guadeloupe                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Guam                                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Guatemala                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Guayana                                              |                |             |           |      ✓     |                 |
| Haiti                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Honduras                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Jamaika                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Martinique                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Mexiko                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Montserrat                                          |                 |                |              |      ✓     |          ✓         |
| Nicaragua                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Panama                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Paraguay                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Peru                                                |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Puerto Rico                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| St. Barthélemy                                    |                 |                |       ✓      |      ✓     |          ✓         |
| St. Kitts und Nevis                               |                 |                |       ✓      |      ✓     |          ✓         |
| St. Lucia                                         |                 |                |              |      ✓     |          ✓         |
| St. Martin                                        |                 |                |       ✓      |      ✓     |          ✓         |
| St. Pierre und Miquelon                           |                 |                |       ✓      |      ✓     |          ✓         |
| St. Vincent und die Grenadinen                    |                 |                |              |      ✓     |          ✓         |
| Sint Maarten                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Südgeorgien und Südliche Sandwichinseln        |                 |                |              |      ✓     |          ✓         |
| Surinam                                            |                 |                |              |      ✓     |          ✓         |
| Trinidad und Tobago                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Kleinere Amerikanische Überseeinseln                |                 |                |              |      ✓     |          ✓         |
| Vereinigte Staaten von Amerika                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uruguay                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Venezuela                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Britische Jungferninseln                              |                 |                |              |      ✓     |          ✓         |
| USA Jungferninseln                                 |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |

## <a name="asia-pacific"></a>Asien-Pazifik

| Land/Region                                      | Adresspunkte |Hausnummern | Straße | Ort | POIs |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Amerikanisch-Samoa                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Australien                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bangladesch                                          |                 |                |              |      ✓     |          ✓         |
| Bhutan                                              |                 |                |              |      ✓     |          ✓         |
| Britisches Territorium im Indischen Ozean                      |                 |                |              |      ✓     |          ✓         |
| Brunei                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kambodscha                                            |                 |                |              |      ✓     |          ✓         |
| China                                               |                 |                |              |      ✓     |          ✓         |
| Weihnachtsinsel                                    |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kokosinseln                             |                 |                |              |      ✓     |          ✓         |
| Komoren                                             |                 |                |              |      ✓     |          ✓         |
| Cookinseln                                        |                 |                |              |      ✓     |          ✓         |
| Fidschi                                                |                  |                |              |      ✓     |          ✓        |
| Französisch-Polynesien                                    |                 |                |              |      ✓     |          ✓         |
| Heard und McDonaldinseln                   |                 |                |              |      ✓     |          ✓         |
| Hongkong (SAR)                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Indonesien                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Indien                                               |        ✓        |        ✓       |       ✓      |      ✓     |                   |
| Japan                                               |                 |                |              |      ✓     |          ✓         |
| Kiribati                                            |                 |                |              |      ✓     |          ✓         |
| Korea                                         |                 |                |              |      ✓     |          ✓         |
| Laos                                                |                 |                |              |      ✓     |          ✓         |
| Macau (SAR)                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malaysia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Mikronesien                                          |                 |                |              |      ✓     |          ✓         |
| Mongolei                                            |                 |                |              |      ✓     |          ✓         |
| Nauru                                               |                 |                |              |      ✓     |          ✓         |
| Nepal                                               |                 |                |              |      ✓     |          ✓         |
| Neukaledonien                                       |                 |                |              |      ✓     |          ✓         |
| Neuseeland                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Niue                                                |                 |                |              |      ✓     |          ✓         |
| Norfolkinsel                                      |                 |                |              |      ✓     |          ✓         |
| Nordkorea                                         |                 |                |              |      ✓     |          ✓         |
| Nördliche Marianen                            |                 |                |       ✓      |      ✓     |          ✓         |
| Pakistan                                            |                 |                |              |      ✓     |          ✓         |
| Palau                                               |                 |                |              |      ✓     |          ✓         |
| Papua-Neuguinea                                    |                 |                |              |      ✓     |          ✓         |
| Philippinen                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Pitcairninseln                                            |                 |                |              |      ✓     |          ✓         |
| Samoa                                               |                 |                |              |      ✓     |          ✓         |
| Senkaku-Inseln/Diaoyutai-Inseln                                     |        ✓        |                |              |      ✓     |          ✓         |
| Singapur                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Salomonen                                     |                 |                |              |      ✓     |          ✓         |
| Südliche Kurilen                                     |        ✓        |                |              |      ✓     |          ✓         |
| Sri Lanka                                           |                 |                |              |      ✓     |          ✓         |
| Taiwan                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Thailand                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Tokelau                                             |                 |                |              |      ✓     |          ✓         |
| Tonga                                               |                 |                |              |      ✓     |          ✓         |
| Turks- und Caicosinseln                            |                 |                |              |      ✓     |          ✓         |
| Tuwalu                                              |                 |                |              |      ✓     |          ✓         |
| Vanuatu                                             |                 |                |              |      ✓     |          ✓         |
| Vietnam                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wallis und Futuna                                   |                 |                |              |      ✓     |          ✓         |

## <a name="europe"></a>Europa

| Land/Region                                      | Adresspunkte |Hausnummern | Straße | Ort | POIs |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Albanien                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Andorra                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Armenien                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Österreich                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Aserbaidschan                                          |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Belgien                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bosnien und Herzegowina                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bulgarien                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Belarus                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Kroatien                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Zypern                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Tschechische Republik                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Dänemark                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Estland                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Färöer                                       |                 |                |              |      ✓     |          ✓         |
| Finnland                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Frankreich                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Georgien                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Deutschland                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gibraltar                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Griechenland                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Grönland                                           |                 |                |              |      ✓     |          ✓         |
| Guernsey                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Ungarn                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Island                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Irland                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Isle of Man                                         |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Italien                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jan Mayen                                           |        ✓        |                |              |      ✓     |          ✓         |
| Jersey                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Kasachstan                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kosovo                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Kirgisistan                                          |                 |                |              |      ✓     |          ✓         |
| Lettland                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Liechtenstein                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Litauen                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Luxemburg                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Nordmazedonien                                     |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malta                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Moldau                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Monaco                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Montenegro                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Niederlande                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Norwegen                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Polen                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Portugal                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| +Azoren und Madeira                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Rumänien                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Russische Föderation                                  |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| San Marino                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Serbien                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Slowakei                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Slowenien                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Spanien                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Spitzbergen                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Schweden                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Schweiz                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tadschikistan                                          |                 |                |              |      ✓     |          ✓         |
| Türkei                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Turkmenistan                                        |                 |                |              |      ✓     |          ✓         |
| Ukraine                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| United Kingdom                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Usbekistan                                          |                 |                |              |      ✓     |          ✓         |
| Vatikanstadt                                        |                 |                |       ✓      |      ✓     |          ✓         |

## <a name="middle-east-and-africa"></a>Naher Osten und Afrika

| Land/Region                                      | Adresspunkte |Hausnummern | Straße | Ort | POIs |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Afghanistan                                         |                 |                |              |      ✓     |          ✓         |
| Algerien                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Angola                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bahrain                                             |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Benin                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Botsuana                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Bouvetinsel                                       |                 |                |              |      ✓     |          ✓         |
| Burkina Faso                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Burundi                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Kamerun                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Cabo Verde                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Zentralafrikanische Republik                            |                 |                |       ✓      |      ✓     |          ✓         |
| Tschad                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Kongo (Demokratische Republik)                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Côte d'Ivoire                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Demokratische Republik Kongo                    |                 |                |       ✓      |      ✓     |          ✓         |
| Dschibuti                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Ägypten                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Republik Äquatorialguinea                      |                 |                |       ✓      |      ✓     |          ✓         |
| Eritrea                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Äthiopien                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Französische Gebiete im südlichen Indischen Ozean|                        |                |              |      ✓     |          ✓         |
| Gabun                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Gambia                                              |                 |                |              |      ✓     |          ✓         |
| Ghana                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Guinea                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Guinea-Bissau                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Iran                                                |                 |                |              |      ✓     |          ✓         |
| Irak                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Israel                                              |        ✓        |       ✓        |              |      ✓     |          ✓         |
| Jordan                                              |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Kenia                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Kuwait                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Libanon                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Lesotho                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Liberia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Libyen                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Madagaskar                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Malawi                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Malediven                                            |                 |                |              |      ✓     |          ✓         |
| Mali                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Marshallinseln                                    |                 |                |              |      ✓     |          ✓         |
| Mauretanien                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Mauritius                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Mayotte                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Marokko                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Mosambik                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Myanmar                                             |                 |                |              |      ✓     |          ✓         |
| Namibia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Niger                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Nigeria                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Oman                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Katar                                               |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Réunion                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Ruanda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| St. Helena                                        |                 |                |              |      ✓     |          ✓         |
| Saudi-Arabien                                        |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Senegal                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Seychellen                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Sierra Leone                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Somalia                                             |                 |                |              |      ✓     |          ✓         |
| Südafrika                                        |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Südsudan                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Sudan                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Swasiland                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Syrien                                               |                 |                |              |      ✓     |          ✓         |
| São Tomé und Príncipe                               |                 |                |       ✓      |      ✓     |          ✓         |
| Tansania                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Togo                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Tunesien                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Uganda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Vereinigte Arabische Emirate                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jemen                                               |                 |                |              |      ✓     |          ✓         |
| Sambia                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Simbabwe                                            |                 |                |       ✓      |      ✓     |          ✓         |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure Maps-Geocodierung:
> [!div class="nextstepaction"]
> [Azure Maps-Suchdienst](/rest/api/maps/search)
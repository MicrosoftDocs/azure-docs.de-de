---
title: Renderabdeckung in Azure Maps | Microsoft-Dokumentation
description: Erfahren Sie mehr über Renderabdeckung in Azure Maps
author: jingjing-z
ms.author: jinzh
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 7feea92d48a826000fd8aa878b95658b79d6d908
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64869993"
---
# <a name="azure-maps-render-coverage"></a>Azure Maps-Renderabdeckung

Azure Maps verwendet sowohl Raster- als auch Vektorkacheln zum Erstellen von Karten. Mit der niedrigsten Auflösung passt die ganze Welt auf eine einzige Kachel. Mit der höchsten Auflösung stellt eine einzige Kachel 38 Quadratmeter dar. Wenn Sie eine Karte vergrößern, sehen Sie zunehmend mehr Details von Kontinenten, Regionen, Städten und einzelnen Straßen. Weitere Informationen finden Sie unter [Zoomstufen und Grobraster](zoom-levels-and-tile-grid.md).

Maps bietet allerdings nicht für alle Regionen das gleiche Maß an Informationen und Genauigkeit. Die folgenden Tabellen enthalten Informationen zum Detaillierungsgrad des Renderns, den Sie in den einzelnen Regionen erwarten können.

## <a name="legend"></a>Legende

| Symbol | Bedeutung |
|--------|---------|
| ✓ | Region wird mit detaillierten Daten dargestellt.   |
| Ø | Region wird mit vereinfachten Daten dargestellt. |


## <a name="africa"></a>Afrika 


| Land/Region | Rasterkacheln vereinheitlicht | Vektorkacheln vereinheitlicht |
| ------ | :------------------: | :------------------: |
| Algerien                          | ✓ | ✓ |
| Angola                           | ✓ | ✓ |
| Benin                            | ✓ | ✓ |
| Botsuana                         | ✓ | ✓ |
| Burkina Faso                     | ✓ | ✓ |
| Burundi                          | ✓ | ✓ |
| Cabo Verde                       | ✓ | ✓ |
| Kamerun                         | ✓ | ✓ |
| Zentralafrikanische Republik         | ✓ | Ø |
| Tschad                             | ✓ | Ø |
| Komoren                          | ✓ | Ø |
| Demokratische Republik Kongo | ✓ | ✓ |
| Côte d'Ivoire                    | ✓ | Ø |
| Dschibuti                         | ✓ | Ø |
| Ägypten                            | ✓ | ✓ |
| Äquatorialguinea                | ✓ | Ø |
| Eritrea                          | ✓ | Ø |
| Äthiopien                         | ✓ | Ø |
| Gabun                            | ✓ | ✓ |
| Gambia                           | ✓ | Ø |
| Ghana                            | ✓ | ✓ |
| Guinea                           | ✓ | Ø |
| Guinea-Bissau                    | ✓ | Ø |
| Kenia                            | ✓ | ✓ |
| Lesotho                          | ✓ | ✓ |
| Liberia                          | ✓ | Ø |
| Libyen                            | ✓ | Ø |
| Madagaskar                       | ✓ | Ø |
| Malawi                           | ✓ | ✓ |
| Mali                             | ✓ | ✓ |
| Mauretanien                       | ✓ | ✓ |
| Mauritius                        | ✓ | ✓ |
| Mayotte                          | ✓ | ✓ |
| Marokko                          | ✓ | ✓ |
| Mosambik                       | ✓ | ✓ |
| Namibia                          | ✓ | ✓ |
| Niger                            | ✓ | ✓ |
| Nigeria                          | ✓ | ✓ |
| Réunion                          | ✓ | ✓ |
| Ruanda                           | ✓ | ✓ |
| St. Helena, Ascension und Tristan da Cunha | ✓ | Ø |
| São Tomé und Príncipe            | ✓ | Ø |
| Senegal                          | ✓ | ✓ |
| Sierra Leone                     | ✓ | ✓ |
| Somalia                          | ✓ | ✓ |
| Südafrika                     | ✓ | ✓ |
| Südsudan                      | ✓ | ✓ |
| Sudan                            | ✓ | ✓ |
| Swasiland                        | ✓ | ✓ |
| Vereinigte Republik Tansania      | ✓ | ✓ |
| Togo                             | ✓ | ✓ |
| Tunesien                          | ✓ | ✓ |
| Uganda                           | ✓ | ✓ |
| Sambia                           | ✓ | ✓ |
| Simbabwe                         | ✓ | ✓ |

## <a name="americas"></a>Amerika

| Land/Region | Rasterkacheln vereinheitlicht | Vektorkacheln vereinheitlicht |
| ------ | :------------------: | :------------------: |
| Anguilla                  | ✓ | ✓ |
| Antigua und Barbuda       | ✓ | ✓ |
| Argentinien                 | ✓ | ✓ |
| Aruba                     | ✓ | ✓ |
| Bahamas                   | ✓ | ✓ |
| Barbados                  | ✓ | ✓ |
| Belize                    | ✓ | ✓ |
| Bermudas                   | ✓ | ✓ |
| Plurinationaler Staat Bolivien | ✓ | ✓ |
| Bonaire, Sint Eustatius und Saba | ✓ | ✓ |
| Brasilien                    | ✓ | ✓ |
| Kanada                    | ✓ | ✓ |
| Kaimaninseln            | ✓ | ✓ |
| Chile                     | ✓ | ✓ |
| Kolumbien                  | ✓ | ✓ |
| Costa Rica                | ✓ | ✓ |
| Kuba                      | ✓ | ✓ |
| Curaçao                   | ✓ | ✓ |
| Dominica                  | ✓ | ✓ |
| Dominikanische Republik        | ✓ | ✓ |
| Ecuador                   | ✓ | ✓ |
| Falklandinseln | ✓ | ✓ |
| Französisch-Guayana             | ✓ | ✓ |
| Grönland                 | ✓ | Ø |
| Grenada                   | ✓ | ✓ |
| Guadeloupe                | ✓ | ✓ |
| Guatemala                 | ✓ | ✓ |
| Guayana                    | ✓ | ✓ |
| Haiti                     | ✓ | ✓ |
| Honduras                  | ✓ | ✓ |
| Jamaika                   | ✓ | ✓ |
| Martinique                | ✓ | ✓ |
| Mexiko                    | ✓ | ✓ |
| Montserrat                | ✓ | ✓ |
| Nicaragua                 | ✓ | ✓ |
| Nördliche Marianen  | ✓ | ✓ |
| Panama                    | ✓ | ✓ | 
| Paraguay                  | ✓ | ✓ |
| Peru                      | ✓ | ✓ |
| Puerto Rico               | ✓ | ✓ |
| Quebec (Kanada)           | ✓ | ✓ |
| St. Barthélemy          | ✓ | ✓ |
| St. Kitts und Nevis     | ✓ | ✓ |
| St. Lucia               | ✓ | ✓ |
| St. Martin (französisch)     | ✓ | ✓ |
| St. Pierre und Miquelon | ✓ | ✓ |
| St. Vincent und die Grenadinen | ✓ | ✓ |
| Sint Maarten (niederländisch)      | ✓ | ✓ |
| Südgeorgien und Südliche Sandwichinseln | ✓ | ✓ |
| Surinam                  | ✓ | ✓ |
| Trinidad und Tobago       | ✓ | ✓ |
| Turks- und Caicosinseln  | ✓ | ✓ |
| USA             | ✓ | ✓ |
| Uruguay                   | ✓ | ✓ |
| Venezuela                 | ✓ | ✓ |
| Britische Jungferninseln   | ✓ | ✓ |
| Amerikanische Jungferninseln      | ✓ | ✓ |

## <a name="asia"></a>Asien 

| Land/Region | Rasterkacheln vereinheitlicht | Vektorkacheln vereinheitlicht |
| ------ | :------------------: | :------------------: |
| Afghanistan               |   | Ø |
| Bahrain                   | ✓ | ✓ |
| Bangladesch                |   | Ø |
| Bhutan                    |   | Ø |
| Britisches Territorium im Indischen Ozean |   | Ø |
| Brunei                    | ✓ | ✓ |
| Kambodscha                  |   | Ø |
| China                     |   | Ø |
| Kokosinseln   |   | Ø |
| Demokratische Volksrepublik Korea |   | Ø |
| Dokdo und Takeshima       |   | Ø |
| Hongkong (SAR)             | ✓ | ✓ |
| Indien                     | Ø | ✓ | 
| Indonesien                 | ✓ | ✓ |
| Iran                      |   | Ø |
| Irak                      | ✓ | ✓ |
| Israel                    |   | ✓ |
| Japan                     |   | Ø |
| Jordan                    | ✓ | ✓ |
| Kasachstan                |   | ✓ |
| Kuwait                    | ✓ | ✓ |
| Kirgisistan                |   | Ø |
| Demokratische Volksrepublik Laos |   | Ø |
| Libanon                   | ✓ | ✓ |
| Macau (SAR)                 | ✓ | ✓ |
| Malaysia                  | ✓ | ✓ |
| Malediven                  |   | Ø |
| Mongolei                  |   | Ø |
| Myanmar                   |   | Ø |
| Nepal                     |   | Ø |
| Oman                      | ✓ | ✓ |
| Pakistan                  |   | Ø |
| Philippinen               | ✓ | ✓ |
| Katar                     | ✓ | ✓ |
| Republik Korea         | ✓ | Ø |
| Saudi-Arabien              | ✓ | ✓ |
| Senkaku-Inseln/Diaoyutai-Inseln           |   | ✓ |
| Singapur                 | ✓ | ✓|
| Sri Lanka                 |   | Ø |
| Arabische Republik Syrien      |   | Ø |
| Taiwan                    | ✓ | ✓ |
| Tadschikistan                |   | Ø |
| Thailand                  | ✓ | ✓ |
| Timor-Leste               |   | Ø |
| Turkmenistan              |   | Ø |
| Vereinigte Arabische Emirate      | ✓ | ✓ |
| Kleinere Amerikanische Überseeinseln |   | Ø |
| Usbekistan                |   | Ø |
| Vietnam                   | ✓ | ✓ |
| Jemen                     | ✓ | ✓ |

## <a name="oceania"></a>Ozeanien

| Land/Region | Rasterkacheln vereinheitlicht | Vektorkacheln vereinheitlicht |
| ------ | :------------------: | :------------------: |
| Amerikanisch-Samoa            |   | ✓ |
| Australien                 | ✓ | ✓ |
| Cookinseln              |   | Ø |
| Fidschi                      |   | Ø |
| Französisch-Polynesien          |   | Ø |
| Guam                      | ✓ | ✓ |
| Kiribati                  |   | Ø |
| Marshallinseln          |   | Ø |
| Mikronesien                |   | Ø |
| Nauru                     |   | Ø |
| Neukaledonien             |   | Ø |
| Neuseeland               | ✓ | ✓ |
| Niue                      |   | Ø |
| Norfolkinsel            |   | Ø |
| Palau                     |   | Ø |
| Papua-Neuguinea          |   | Ø |
| Pitcairninseln                  |   | Ø |
| Samoa                     |   | Ø |
| Salomonen           |   | Ø|
| Tokelau                   |   | Ø |
| Tonga                     |   | Ø |
| Tuwalu                    |   | Ø |
| Vanuatu                   |   | Ø |
| Wallis und Futuna         |   | Ø |


## <a name="europe"></a>Europa

| Land/Region | Rasterkacheln vereinheitlicht | Vektorkacheln vereinheitlicht |
| ------ | :------------------: | :------------------: |
| Albanien                   | ✓ | ✓ |
| Andorra                   | ✓ | ✓ |
| Armenien                   | ✓ | Ø |
| Österreich                   | ✓ | ✓ |
| Aserbaidschan                | ✓ | Ø |
| Belarus                   | Ø | ✓ |
| Belgien                   | ✓ | ✓ |
| Bosnien und Herzegowina        | ✓ | ✓ |
| Bulgarien                  | ✓ | ✓ |
| Kroatien                   | ✓ | ✓ |
| Zypern                    | ✓ | ✓ |
| Tschechische Republik            | ✓ | ✓ |
| Dänemark                   | ✓ | ✓ |
| Estland                   | ✓ | ✓ |
| Färöer             | ✓ | Ø |
| Finnland                   | ✓ | ✓ |
| Frankreich                    | ✓ | ✓ |
| Georgien                   | ✓ | Ø |
| Deutschland                   | ✓ | ✓ |
| Gibraltar                 | ✓ | ✓ |
| Griechenland                    | ✓ | ✓ |
| Guernsey                  | ✓ | ✓ |
| Ungarn                   | ✓ | ✓ |
| Island                   | ✓ | ✓ |
| Irland                   | ✓ | ✓ |
| Insel Man               | ✓ | ✓ |
| Italien                     | ✓ | ✓ |
| Jan Mayen                 | ✓ | ✓ |
| Jersey                    | ✓ | ✓ |
| Lettland                    | ✓ | ✓ |
| Liechtenstein             | ✓ | ✓ |
| Litauen                 | ✓ | ✓ |
| Luxemburg                | ✓ | ✓ |
| Nordmazedonien           | ✓ | ✓ |
| Malta                     | ✓ | ✓ |
| Moldau                   | ✓ | ✓ |
| Monaco                    | ✓ | ✓ |
| Montenegro                | ✓ | ✓ |
| Niederlande               | ✓ | ✓ |
| Norwegen                    | ✓ | ✓ |
| Polen                    | ✓ | ✓ |
| Portugal                  | ✓ | ✓ |
| Rumänien                   | ✓ | ✓ |
| Russische Föderation        | ✓ | ✓ |
| San Marino                | ✓ | ✓ |
| Serbien                    | ✓ | ✓ |
| Slowakei                  | ✓ | ✓ |
| Slowenien                  | ✓ | ✓ |
| Südliche Kurilen           | ✓ | ✓ |
| Spanien                     | ✓ | ✓ |
| Spitzbergen                  | ✓ | ✓ |
| Schweden                    | ✓ | ✓ |
| Schweiz               | ✓ | ✓ |
| Türkei                    | ✓ | ✓ |
| Ukraine                   | ✓ | ✓ |
| Vereinigtes Königreich            | ✓ | ✓ |
| Vatikanstadt              | ✓ | ✓ |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Rendern in Azure Maps finden Sie unter [Zoomstufen und Grobraster](zoom-levels-and-tile-grid.md).

Erfahren Sie mehr über die [Abdeckungsbereiche des Routingdiensts von Maps](routing-coverage.md). 
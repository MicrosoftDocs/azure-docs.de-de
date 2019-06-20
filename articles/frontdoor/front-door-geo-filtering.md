---
title: Geofilterung in einer Domäne für Azure Front Door Service | Microsoft-Dokumentation
description: Diese Artikel enthält Informationen zur Geofilterungsrichtlinie für Azure Front Door Service.
services: frontdoor
documentationcenter: ''
author: KumudD
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: kumud;tyao
ms.openlocfilehash: da56c9664ce533709676ded677add4a2391dce03
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64865867"
---
# <a name="what-is-geo-filtering-on-a-domain-for-azure-front-door"></a>Was ist Geofilterung in einer Domäne für Azure Front Door?

Standardmäßig reagiert Azure Front Door Service auf alle Benutzeranforderungen, ganz gleich, wo sich der Benutzer befindet, von dem die Anforderung stammt. Manchmal ist jedoch unter Umständen eine länder-/regionsbasierte Einschränkung des Zugriffs auf Ihre Webanwendungen wünschenswert. Der Web Application Firewall-Dienst (WAF) in Front Door ermöglicht es Ihnen, eine Richtlinie mit benutzerdefinierten Schutzregeln für einen bestimmten Pfad an Ihrem Endpunkt zu definieren, um den Zugriff aus bestimmten Ländern/Regionen zuzulassen oder zu blockieren. 

Eine WAF-Richtlinie enthält üblicherweise einige benutzerdefinierte Regeln. Eine Regel umfasst Übereinstimmungsbedingungen, eine Aktion und eine Priorität. In der Übereinstimmungsbedingung werden eine Übereinstimmungsvariable, ein Operator und ein Übereinstimmungswert definiert.  Eine Geofilterungsregel setzt sich aus der Übereinstimmungsvariablen „REMOTE_ADDR“, dem Operator „GeoMatch“ und einem aus zwei Buchstaben bestehenden Ländercode zusammen. Sie können eine GeoMatch-Bedingung mit einer Zeichenfolgen-Übereinstimmungsbedingung vom Typ „REQUEST_URI“ kombinieren, um eine pfadbasierte Geofilterungsregel zu erstellen.

Eine Geofilterungsrichtlinie für Front Door kann über [Azure PowerShell](front-door-tutorial-geo-filtering.md) oder mithilfe unserer [Schnellstartvorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering) konfiguriert werden.

## <a name="country-code-reference"></a>Ländercodereferenz

|Landesvorwahl | Name des Lands |
| ----- | ----- |
| AD | Andorra |
| AE | Vereinigte Arabische Emirate|
| AF | Afghanistan|
| AG | Antigua und Barbuda|
| AL | Albanien|
| AM | Armenien|
| AO | Angola|
| AR | Argentinien|
| AS | Amerikanisch-Samoa|
| AT | Österreich|
| AU | Australien|
| AZ | Aserbaidschan|
| BA | Bosnien und Herzegowina|
| BB | Barbados|
| BD | Bangladesch|
| BE | Belgien|
| BF | Burkina Faso|
| BG | Bulgarien|
| BH | Bahrain|
| BI | Burundi|
| BJ | Benin|
| BL | St. Barthélemy|
| BN | Brunei Darussalam|
| BO | Bolivien|
| BR | Brasilien|
| BS | Bahamas|
| BT | Bhutan|
| BW | Botsuana|
| BY | Belarus|
| BZ | Belize|
| CA | Kanada|
| CD | Demokratische Republik Kongo|
| CF | Zentralafrikanische Republik|
| CH | Schweiz|
| CI | Côte d'Ivoire|
| CL | Chile|
| CM | Kamerun|
| CN | China|
| CO | Kolumbien|
| CR | Costa Rica|
| CU | Kuba|
| CV | Cabo Verde|
| CY | Zypern|
| CZ | Tschechische Republik|
| DE | Deutschland|
| DK | Dänemark|
| DO | Dominikanische Republik|
| DZ | Algerien|
| EC | Ecuador|
| EE | Estland|
| EG | Ägypten|
| ES | Spanien|
| ET | Äthiopien|
| FI | Finnland|
| FJ | Fidschi|
| FM | Föderierte Staaten von Mikronesien|
| BV | Frankreich|
| GB | Vereinigtes Königreich|
| GE | Georgien|
| GF | Französisch-Guayana|
| GH | Ghana|
| GN | Guinea|
| GP | Guadeloupe|
| GR | Griechenland|
| GT | Guatemala|
| GY | Guayana|
| HK | Hongkong (SAR)|
| HN | Honduras|
| HR | Kroatien|
| HT | Haiti|
| HU | Ungarn|
| ID | Indonesien|
| IE | Irland|
| IL | Israel|
| IN | Indien|
| IQ | Irak|
| IR | Islamische Republik Iran|
| IS | Island|
| IT | Italien|
| JM | Jamaika|
| JO | Jordan|
| JP | Japan|
| KE | Kenia|
| KG | Kirgisistan|
| KH | Kambodscha|
| KI | Kiribati|
| KN | St. Kitts und Nevis|
| KP | Demokratische Volksrepublik Korea|
| KR | Republik Korea|
| KW | Kuwait|
| KY | Kaimaninseln|
| KZ | Kasachstan|
| LA | Demokratische Volksrepublik Laos|
| LB | Libanon|
| LI | Liechtenstein|
| LK | Sri Lanka|
| LR | Liberia|
| LS | Lesotho|
| LT | Litauen|
| LU | Luxemburg|
| LV | Lettland|
| LY | Libyen |
| MA | Marokko|
| MD | Republik Moldau|
| MG | Madagaskar|
| MK | Nordmazedonien|
| ML | Mali|
| MM | Myanmar|
| MN | Mongolei|
| MO | Macau (SAR)|
| MQ | Martinique|
| MR | Mauretanien|
| MT | Malta|
| MV | Malediven|
| MW | Malawi|
| MX | Mexiko|
| MY | Malaysia|
| MZ | Mosambik|
| Nicht verfügbar | Namibia|
| NE | Niger|
| NG | Nigeria|
| NI | Nicaragua|
| NL | Niederlande|
| NO | Norwegen|
| NP | Nepal|
| NR | Nauru|
| NZ | Neuseeland|
| OM | Oman|
| PA | Panama|
| PE | Peru|
| PH | Philippinen|
| PK | Pakistan|
| PL | Polen|
| PR | Puerto Rico|
| PT | Portugal|
| PW | Palau|
| PY | Paraguay|
| QA | Katar|
| RE | Réunion|
| RO | Rumänien|
| RS | Serbien|
| RU | Russische Föderation|
| RW | Ruanda|
| SA | Saudi-Arabien|
| SD | Sudan|
| SE | Schweden|
| SG | Singapur|
| SI | Slowenien|
| SK | Slowakei|
| SN | Senegal|
| SO | Somalia|
| SR | Surinam|
| SS | Südsudan|
| SV | El Salvador|
| SY | Arabische Republik Syrien|
| SZ | Swasiland|
| TC | Turks- und Caicosinseln|
| TG | Togo|
| TH | Thailand|
| TN | Tunesien|
| TR | Türkei|
| TT | Trinidad und Tobago|
| TW | Taiwan|
| TZ | Vereinigte Republik Tansania|
| UA | Ukraine|
| UG | Uganda|
| US | USA|
| UY | Uruguay|
| UZ | Usbekistan|
| VC | St. Vincent und die Grenadinen|
| VE | Venezuela|
| VG | Britische Jungferninseln|
| VI | Amerikanische Jungferninseln|
| VN | Vietnam|
| ZA | Südafrika|
| ZM | Sambia|
| ZW | Simbabwe|

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über [Sicherheit für die Anwendungsschicht mit Front Door](front-door-application-security.md).
- Erfahren Sie mehr über das [Erstellen einer Front Door-Instanz](quickstart-create-front-door.md).

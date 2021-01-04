---
title: Häufig gestellte Fragen (FAQ) zu Microsoft Azure Maps-Wetterinfodiensten (Vorschau)
description: Im Folgenden finden Sie Antworten auf häufig gestellte Fragen zu den Daten und Funktionen der Azure Maps-Wetterinfodienste (Vorschau).
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8b7726e4e8fee0044a7865c8c494ef6451425676
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96903869"
---
# <a name="azure-maps-weather-services-preview-frequently-asked-questions-faq"></a>Häufig gestellte Fragen (FAQ) zu Microsoft Azure Maps-Wetterinfodiensten (Vorschau)

> [!IMPORTANT]
> Die Azure Maps-Wetterinfodienste befinden sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In diesem Artikel finden Sie Antworten auf häufig gestellte Fragen zu den Daten und Funktionen der Azure Maps-[Wetterinfodienste](https://docs.microsoft.com/rest/api/maps/weather). Die folgenden Themen werden erörtert:

* Datenquellen und Datenmodelle
* Umfang und Verfügbarkeit der Wetterinfodienste
* Aktualisierungshäufigkeit der Daten
* Entwickeln mit Azure Maps SDKs
* Optionen zum Visualisieren von Wetterdaten, einschließlich Microsoft Power BI-Integration

## <a name="data-sources-and-data-models"></a>Datenquellen und Datenmodelle

**Wie werden Wetterdaten in Azure Maps erfasst?**

Azure Maps wird in Zusammenarbeit mit erstklassigen Mobilitäts- und Standorttechnologiepartnern erstellt, darunter AccuWeather, das die zugrunde liegenden Wetterdaten bereitstellt. Die Ankündigung der Zusammenarbeit von Azure Maps mit AccuWeather finden Sie unter [Regen oder Sonnenschein: Azure Maps Weather Services bietet Ihrem Unternehmen Erkenntnisse](https://azure.microsoft.com/blog/rain-or-shine-azure-maps-weather-services-will-bring-insights-to-your-enterprise/).

AccuWeather verfügt über Wetter- und Umweltinformationen in Echtzeit, die auf der ganzen Welt verfügbar sind. Dies ist vor allem auf die Partnerschaften mit zahlreichen nationalen staatlichen Wetterdiensten und andere proprietäre Vereinbarungen zurückzuführen. Diese grundlegenden Informationen sind nachfolgend aufgelistet.

* Öffentlich verfügbare globale Oberflächenbeobachtungen von staatlichen Stellen
* Proprietäre Datasets zur Oberflächenbeobachtung von staatlichen Stellen und privaten Unternehmen
* Hochauflösende Radardaten für über 40 Länder und Regionen
* Beste globale Blitzdaten in Echtzeit
* Von staatlichen Stellen ausgegebene Wetterwarnungen für über 60 Länder, Regionen und Gebiete
* Satellitendaten von geostationären Wettersatelliten für die ganze Welt
* Über 150 numerische Vorhersagemodelle, einschließlich interner, proprietärer und staatlicher Modelle wie das U.S. Global Forecast System (GFS) und spezielle herunterskalierte Modelle von privaten Unternehmen
* Beobachtungen der Luftqualität
* Beobachtungen von Verkehrsbehörden

Zehntausende von Oberflächenbeobachtungen werden zusammen mit anderen Daten genutzt, um die aktuellen Bedingungen zu modellieren und anzupassen, die Benutzern zur Verfügung gestellt werden. Dies umfasst nicht nur frei verfügbare Standarddatasets, sondern auch spezielle Beobachtungsdaten von nationalen Wetterdiensten in vielen Ländern und Regionen, einschließlich Indien, Brasilien und Kanada, sowie andere proprietäre Eingaben. Diese einzigartigen Datasets erweitern die räumliche und zeitliche Auflösung der aktuellen Zustandsdaten für unsere Benutzer. 

Diese Datasets werden in Echtzeit auf ihre Genauigkeit für das digitale Vorhersagesystem überprüft, das die proprietären KI-Algorithmen von AccuWeather verwendet, um die Vorhersagen kontinuierlich zu ändern. So wird sichergestellt, dass sie immer die neuesten Daten enthalten und dadurch ihre Genauigkeit kontinuierlich maximiert wird.

**Welche Modelle erstellen Wettervorhersagedaten?**

Für das Erstellen globaler Vorhersagen werden zahlreiche Leitsysteme für Wettervorhersagen genutzt. Über 150 numerische Vorhersagemodelle mit externen und internen Datasets werden täglich verwendet. Dazu gehören staatliche Modelle, z. B. vom Europäischen Zentrum für mittelfristige Wettervorhersage (EZMW) und das U.S. Global Forecast System (GFS). Zusätzlich bezieht AccuWeather auch proprietäre hochauflösende Modelle ein, die Vorhersagen auf bestimmte Orte und strategische regionale Bereiche herunterskalieren, um das Wetter noch genauer vorherzusagen. Die spezifischen Kombinations- und Gewichtungsalgorithmen von AccuWeather wurden in den letzten Jahrzehnten entwickelt. Diese Algorithmen nutzen die zahlreichen Vorhersageeingaben optimal und liefern äußert genaue Vorhersagen.

## <a name="weather-services-preview-coverage-and-availability"></a>Umfang und Verfügbarkeit der Wetterinfodienste (Vorschau)

**Welchen Umfang kann ich für verschiedene Länder und Regionen erwarten?**

Der Umfang der Wetterinfodienste variiert je nach Land und Region. Nicht alle Funktionen sind in jedem Land und jeder Region verfügbar. Weitere Informationen finden Sie in der [Dokumentation zum Umfang](https://docs.microsoft.com/azure/azure-maps/weather-coverage).

## <a name="data-update-frequency"></a>Aktualisierungshäufigkeit der Daten

**Wie oft werden die Daten zu den aktuellen Bedingungen aktualisiert?**

Die Daten zu den aktuellen Bedingungen werden mindestens einmal pro Stunde aktualisiert, können aber bei sich schnell ändernden Bedingungen, z. B. großen Temperaturänderungen, Änderungen der Luftbedingungen, Niederschlagsänderungen usw., häufiger aktualisiert werden. Die meisten Beobachtungsstationen auf der ganzen Welt melden mehrmals pro Stunde, wenn sich Bedingungen ändern. Einige Bereiche werden jedoch weiterhin nur ein-, zwei- oder viermal pro Stunde in geplanten Intervallen aktualisiert.  

In Azure Maps werden die Daten zu den aktuellen Bedingungen bis zu 10 Minuten zwischengespeichert, sodass die Aktualisierungshäufigkeit der Daten nahezu in Echtzeit erfasst werden kann. Um zu sehen, wann die zwischengespeicherte Antwort abläuft, und um die Anzeige veralteter Daten zu vermeiden, können Sie die Informationen des Expires-Headers im HTTP-Header der Azure Maps-API-Antwort nutzen.

**Wie oft werden tägliche und stündliche Vorhersagedaten aktualisiert?**

Die täglichen und stündlichen Vorhersagedaten werden mehrmals am Tag aktualisiert, wenn aktualisierte Beobachtungen empfangen werden.  Wenn z. B. eine vorhergesagte Höchst- oder Tiefsttemperatur über- bzw. unterschritten wird, werden die Vorhersagedaten beim nächsten Aktualisierungszyklus angepasst. Dies kann in unterschiedlichen Intervallen erfolgen – normalerweise jedoch innerhalb einer Stunde. Viele plötzliche Wetterbedingungen können zu einer Änderung der Vorhersagedaten führen. Beispielsweise kann an einem heißen Sommernachmittag plötzlich ein einzelnes Gewitter aufziehen, das starke Bewölkung und Regen mit sich bringt. Das isolierte Gewitter kann die Temperatur effektiv um bis zu 10 Grad senken. Dieser neue Temperaturwert wirkt sich auf die stündlichen und täglichen Vorhersagen für den Rest des Tages aus und wird daher in den Datasets aktualisiert.

Die Vorhersage-APIs in Azure Maps werden bis zu 30 Minuten zwischengespeichert. Um zu sehen, wann die zwischengespeicherte Antwort abläuft, und um die Anzeige veralteter Daten zu vermeiden, können Sie die Informationen des Expires-Headers im HTTP-Header der Azure Maps-API-Antwort nutzen. Wir empfehlen eine Aktualisierung nach Bedarf, basierend auf einem bestimmten Produktanwendungsfall und der Benutzeroberfläche.

## <a name="developing-with-azure-maps-sdks"></a>Entwickeln mit Azure Maps SDKs

**Bietet das Azure Maps Web SDK native Unterstützung für die Integration von Wetterinfodiensten (Vorschau)?**

Das Web SDK für Azure Maps stellt das Modul „Dienste“ bereit. Das Modul „Dienste“ ist eine Hilfsbibliothek, die die Verwendung der REST-Dienste von Azure Maps in Web- oder Node.js-Anwendungen vereinfacht, indem JavaScript oder TypeScript verwendet wird. Informationen zu den ersten Schritten finden Sie in unserer [Dokumentation](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module).

**Bietet das Azure Maps Android SDK native Unterstützung für die Integration von Wetterinfodiensten (Vorschau)?**

Die Azure Maps Android SDKs unterstützen Mercator-Kachelebenen, die die x/y/Zoom-Notation, die Quad-Key-Notation oder die EPSG 3857-Notation mit Begrenzungsrahmen verwenden können.

Die Erstellung des Moduls „Dienste“ für Java und Android ähnlich dem Web SDK-Modul ist in Planung. Das Modul „Dienste“ für Android erleichtert den Zugriff auf alle Azure Maps-Dienste in einer Java- oder Android-App.  

## <a name="data-visualizations"></a>Datenvisualisierungen  

**Unterstützt das Azure Maps-Visual in Power BI Azure Maps-Wetterinfokacheln?**

Ja. Informationen zum Migrieren von Radar- und Infrarot-Satellitenkacheln zum Microsoft Power BI-Visual finden Sie unter [Hinzufügen einer Kachelebene im Power BI-Visual](https://docs.microsoft.com/azure/azure-maps/power-bi-visual-add-tile-layer). 

**Wie sind die für Radar- und Satellitenkacheln verwendeten Farben zu interpretieren?**

Der Artikel [Konzepte für Wetterdienste in Azure Maps](https://docs.microsoft.com/azure/azure-maps/weather-services-concepts#radar-and-satellite-imagery-color-scale) enthält einen Leitfaden zur Interpretation der für Radar- und Satellitenkacheln verwendeten Farben. In diesem Artikel werden Farbbeispiele und hexadezimale Farbcodes beschrieben.
 
**Kann ich Animationen in Radar- und Satellitenkacheln erstellen?**

Ja. Zusätzlich zu den Radar- und Satellitenkacheln in Echtzeit können Azure Maps-Kunden frühere und zukünftige Kacheln anfordern, um Datenvisualisierungen mit Kartenüberlagerungen zu erweitern. Dies kann durch direktes Aufrufen der [Render V2-API zum Abrufen von Kartenkacheln](https://aka.ms/AzureMapsWeatherTiles ) oder durch Anfordern von Kacheln über das Azure Maps Web SDK erfolgen. Radarkacheln werden für bis zu 1,5 Stunden in der Vergangenheit und für bis zu 2 Stunden in der Zukunft bereitgestellt. Die Kacheln sind in 5-Minuten-Intervallen verfügbar. Infrarotkacheln werden für bis zu 3 Stunden in der Vergangenheit bereitgestellt und sind in 10-Minuten-Intervallen verfügbar. Weitere Informationen finden Sie im Open-Source-[Codebeispiel](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Animated%20tile%20layer) zur Animation für Wetterkacheln.  

**Werden Symbole für unterschiedliche Wetterbedingungen angeboten?**

Ja. Die Symbole und die entsprechenden Codes finden Sie [hier](https://docs.microsoft.com/azure/azure-maps/weather-services-concepts#weather-icons). Beachten Sie, dass nur einige der APIs des Wetterinfodiensts (Vorschau), z. B. die [API zum Abrufen der aktuellen Bedingungen](https://aka.ms/azuremapsweathercurrentconditions), den *iconCode* in der Antwort zurückgeben. Weitere Informationen finden Sie im Open-Source-[Codebeispiel](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Get%20current%20weather%20at%20a%20location) zum Abrufen der aktuellen Wetterbedingungen.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie in diesen FAQs keine Antwort auf Ihre Frage finden, können Sie sich über die folgenden Kanäle an uns wenden (Eskalationsreihenfolge):

* Im Abschnitt „Kommentare“ dieses Artikels.
* [MSFT Q&A-Seite für Azure Maps](https://docs.microsoft.com/answers/topics/azure-maps.html).
* Microsoft-Support. Wählen Sie zum Erstellen einer neuen Supportanfrage im Azure-Portal auf der Registerkarte [Hilfe](https://portal.azure.com/) die Schaltfläche **Hilfe und Support** und anschließend die Option **Neue Supportanfrage** aus.
* [Azure Maps UserVoice](https://feedback.azure.com/forums/909172-azure-maps) zum Übermitteln von Funktionsanfragen.

Informationen zum Anfordern von Echtzeit- und prognostizierten Wetterdaten mit Azure Maps-Wetterinfodiensten (Vorschau):
> [!div class="nextstepaction"]
> [Anfordern von Echtzeitwetterdaten](how-to-request-weather-data.md)

Konzepte für Azure Maps-Wetterinfodienste (Vorschau):
> [!div class="nextstepaction"]
> [Konzepte von Wetterdiensten](weather-coverage.md)

Machen Sie sich mit der API-Dokumentation für Azure Maps-Wetterinfodienste (Vorschau) vertraut:

> [!div class="nextstepaction"]
> [Azure Maps-Wetterinfodienste](/rest/api/maps/weather)

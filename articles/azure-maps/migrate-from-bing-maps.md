---
title: 'Tutorial: Migrieren von Bing Karten zu Azure Maps | Microsoft Azure Maps'
description: Hier finden Sie ein Tutorial zum Migrieren von Bing Karten zu Microsoft Azure Maps. In diesem Leitfaden wird die Umstellung auf Azure Maps-APIs und SDKs erläutert.
author: rbrundritt
ms.author: richbrun
ms.date: 9/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 0045520849ea20d3e53a30101e6db0f5d495ab15
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897006"
---
# <a name="tutorial---migrate-from-bing-maps-to-azure-maps"></a>Tutorial: Migrieren von Bing Karten zu Azure Maps

Dieser Leitfaden bietet Einblicke in die Migration von Web-, Mobil- und serverbasierten Anwendungen von Bing Karten zur Azure Maps-Plattform. Dieser Leitfaden enthält vergleichende Codebeispiele, Migrationsvorschläge und Best Practices für die Migration zu Azure Maps.

## <a name="azure-maps-platform-overview"></a>Übersicht über die Azure Maps-Plattform

Azure Maps stellt Entwicklern aus allen Branchen leistungsstarke Geofunktionen mit umfassenden aktuellen Kartendaten zur Verfügung, um geografischen Kontext für Web- und Mobilanwendungen bereitzustellen. Bei Azure Maps handelt es sich um verschiedene, mit der Azure One-API konforme REST-APIs für Karten, Suche, Routenplanung, Verkehr, Zeitzonen, Geofencing, Kartendaten, Wetterdaten und viele weitere Dienste. Diese umfassen sowohl Web- als auch Android-SDKs, um die Entwicklung für verschiedene Plattformen einfach, flexibel und portabel zu gestalten. [Azure Maps ist auch in Power BI verfügbar](power-bi-visual-getting-started.md).

## <a name="high-level-platform-comparison"></a>Allgemeiner Plattformvergleich

Die folgende Tabelle enthält eine allgemeine Liste der Bing Karten-Features und der relativen Unterstützung für diese Features in Azure Maps. Diese Liste enthält keine zusätzlichen Azure Maps-Features, wie z. B. Barrierefreiheit, Geofencing-APIs, Verkehrsinfo, räumliche Operationen, direkter Zugriff auf Kartenkacheln und Batchdienste.

| Feature in Bing-Karten                     | Azure Maps-Unterstützung |
|---------------------------------------|:------------------:|
| Web-SDK                               | ✓                  |
| Android-SDK                           | ✓                  |
| iOS SDK                               | Geplant            |
| UWP SDK                               | Geplant            |
| WPF SDK                               | Geplant            |
| REST-Dienst-APIs                     | ✓                  |
| Vorschlagssuche                           | ✓                  |
| Wegbeschreibungen (einschließlich LKW)          | ✓                  |
| Entfernungsmatrix                       | ✓                  |
| Höhenangaben                            | Geplant            |
| Bilder – statische Karte                  | ✓                  |
| Bild-Metadaten                      | ✓                  |
| Isochrone                            | ✓                  |
| Lokale Einblicke                        | ✓                  |
| Lokale Suche                          | ✓                  |
| Standortbestimmung                  | ✓                  |
| Standorte (Geocodierung vorwärts/reversiert) | ✓                  |
| Optimierte Reiserouten            | Geplant            |
| Ausrichtung an Straßen                         | ✓                  |
| Spatial Data Services (SDS)           | Partial            |
| Zeitzone                             | ✓                  |
| Verkehrsstörungen                     | ✓                  |
| Konfigurationsgestützte Karten             | –                |

Bing Karten bietet eine einfache schlüsselbasierte Authentifizierung. Azure Maps bietet sowohl eine grundlegende schlüsselbasierte Authentifizierung als auch eine sehr sichere, Azure Active Directory-Authentifizierung.

## <a name="licensing-considerations"></a>Lizenzierungsaspekte

Bei der Migration von Bing Karten zu Azure Maps sollten folgende Punkte bei der Lizenzierung beachtet werden.

-   Azure Maps berechnet Gebühren für die Nutzung interaktiver Karten basierend auf der Anzahl der geladenen Kartenkacheln. Bei Bing Karten hingegen werden die Gebühren für das Laden der Kartensteuerelemente (Sitzungen) berechnet. In Azure Maps werden Kartenkacheln automatisch zwischengespeichert, um den Aufwand für den Entwickler zu reduzieren. Für jeweils 15 geladene Kartenkacheln wird eine Azure Maps-Transaktion generiert. Die interaktiven Azure Maps SDKs verwenden 512-Pixel-Kacheln und generieren im Durchschnitt mindestens eine Transaktion pro Seitenansicht.

-   Azure Maps ermöglicht es, Daten von seiner Plattform in Azure zu speichern. Sie können auch gemäß den [Nutzungsbedingungen](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) an anderer Stelle für bis zu sechs Monate zwischengespeichert werden.

Hier finden Sie einige Ressourcen zum Thema Lizenzierung für Azure Maps:

-   [Preisübersicht für Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/)
-   [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
-   [Nutzungsbedingungen für Azure Maps](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (in den Microsoft-Bedingungen für Onlinedienste inbegriffen)
-   [Auswählen des richtigen Tarifs in Azure Maps](./choose-pricing-tier.md)

## <a name="suggested-migration-plan"></a>Empfohlener Migrationsplan

Nachfolgend finden Sie einen allgemeinen Migrationsplan.

1.  Stellen Sie fest, welche SDKs und Dienste von Bing Karten von Ihrer Anwendung verwendet werden, und überprüfen Sie, ob Azure Maps alternative SDKs und Dienste anbietet, zu denen Sie migrieren können.
2.  Erstellen Sie auf <https://azure.com> ein Azure-Abonnement, falls Sie noch keins besitzen.
3.  Erstellen Sie ein Azure Maps-Konto ([Dokumentation](./how-to-manage-account-keys.md)) und einen Authentifizierungsschlüssel oder Azure Active Directory ([Dokumentation](./how-to-manage-authentication.md)).
4.  Migrieren Sie den Anwendungscode.
5.  Testen Sie Ihre migrierte Anwendung.
6.  Stellen Sie die migrierte Anwendung in der Produktionsumgebung bereit.

## <a name="create-an-azure-maps-account"></a>Erstellen eines Azure Maps-Kontos

Führen Sie die folgenden Schritte aus, um ein Azure Maps-Konto zu erstellen und Zugriff auf die Azure Maps-Plattform zu erhalten:

1. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
2. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
3. Erstellen Sie ein [Azure Maps-Konto](./how-to-manage-account-keys.md). 
4. [Rufen Sie den Azure Maps-Abonnementschlüssel ab](./how-to-manage-authentication.md#view-authentication-details), oder richten Sie Azure Active Directory-Authentifizierung ein, um die Sicherheit zu erhöhen.

## <a name="azure-maps-technical-resources"></a>Technische Azure Maps-Ressourcen

Nachfolgend finden Sie eine Liste nützlicher technischer Ressourcen für Azure Maps.

-   Übersicht: https://azure.com/maps
-   Dokumentation: <https://aka.ms/AzureMapsDocs>
-   Web SDK-Codebeispiele:<https://aka.ms/AzureMapsSamples>
-   Entwicklerforen: <https://aka.ms/AzureMapsForums>
-   Videos: <https://aka.ms/AzureMapsVideos>
-   Blog: <https://aka.ms/AzureMapsBlog>
-   Azure Maps-Feedback (UserVoice): <https://aka.ms/AzureMapsFeedback>

## <a name="migration-support"></a>Migrationsunterstützung

Entwickler können über die [Foren](/answers/topics/azure-maps.html) oder über eine der vielen Azure-Supportoptionen Unterstützung bei der Migration erhalten: <https://azure.microsoft.com/support/options/>

## <a name="new-terminology"></a>Neue Terminologie 

Die folgende Liste enthält geläufige Begriffe in Bing Karten, die in Azure Maps unter einem anderen Begriff bekannt sind.

| Begriff in Bing Karten                    | Begriff in Azure Maps                                                |
|-----------------------------------|----------------------------------------------------------------|
| Luftbild                            | Satellit oder Luftbild                                            |
| Wegbeschreibungen                        | Kann auch als Routenplanung bezeichnet werden                             |
| Entitäten                          | Geometrien oder Features                                         |
| `EntityCollection`                | Datenquelle oder Ebene                                           |
| `Geopoint`                        | Position                                                       |
| `GeoXML`                          | XML-Dateien im räumlichen IO-Modul                             |
| Bodenüberlagerung                    | Bildebene                                                    |
| Hybrid (als Bezeichnung eines Kartentyps) | Satellit mit Straßen                                           |
| Infobox                           | Popup                                                          |
| Standort                          | Position                                                       |
| `LocationRect`                    | Begrenzungsrahmen                                                   |
| Zuordnungstyp                          | Kartenstil                                                      |
| Navigationsleiste                    | Kartenstil-Auswahlelement, Zoomsteuerung, Neigungssteuerelement, Kompasssteuerelement |
| Ortsmarke                           | Blasenebene, Symbolebene oder HTML-Marker                      |

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln erfahren Sie im Detail, wie Sie Ihre Bing Karten-Anwendung migrieren:

> [!div class="nextstepaction"]
> [Migrieren einer Web-App](migrate-from-bing-maps-web-app.md)

> [!div class="nextstepaction"]
> [Migrieren eines Webdiensts](migrate-from-bing-maps-web-services.md)
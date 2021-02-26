---
title: 'Tutorial: Migrieren von Google Maps zu Azure Maps | Microsoft Azure Maps'
description: Hier finden Sie ein Tutorial zum Migrieren von Google Maps zu Microsoft Azure Maps. In diesem Leitfaden wird die Umstellung auf Azure Maps-APIs und SDKs erläutert.
author: rbrundritt
ms.author: richbrun
ms.date: 09/23/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6241f6156b01c3c90f00578ae5416e4e77270930
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386797"
---
# <a name="tutorial-migrate-from-google-maps-to-azure-maps"></a>Tutorial: Migrieren von Google Maps zu Azure Maps

Dieser Artikel bietet Einblicke in die Migration von Web- und Mobilanwendungen sowie von serverbasierten Anwendungen von Google Maps zur Microsoft Azure Maps-Plattform. Dieses Tutorial enthält vergleichende Codebeispiele, Migrationsvorschläge und Best Practices für die Migration zu Azure Maps. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Allgemeiner Vergleich von Google Maps-Funktionen mit ihren Gegenstücken in Azure Maps
> * Zu berücksichtigende Unterschiede bei der Lizenzierung
> * Planen Ihrer Migration
> * Technische Ressourcen und Support.

## <a name="prerequisites"></a>Voraussetzungen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
2. [Erstellen eines Azure Maps-Kontos](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt) Weitere Informationen zur Authentifizierung in Azure Maps finden Sie unter [Verwalten der Authentifizierung in Azure Maps](how-to-manage-authentication.md).

## <a name="azure-maps-platform-overview"></a>Übersicht über die Azure Maps-Plattform

Azure Maps bietet leistungsstarke Geofunktionen für Entwickler aus allen Branchen. Die Funktionen werden mit regelmäßig aktualisierten Kartendaten kombiniert, um geografischen Kontext für webbasierte und mobile Anwendungen bereitzustellen. Azure Maps verfügt über verschiedene, mit der Azure One-API konforme REST-APIs. Die Rest-APIs bieten Kartenrendering, Suche, Routenplanung, Verkehrsinfo, Zeitzonen, Geolocation, Geofencing, Kartendaten, Wetter, Mobilität und räumliche Operationen. Für Operationen stehen Web- und Android-SDKs zur Verfügung, um eine einfache, flexible und portable Entwicklung für verschiedene Plattformen zu ermöglichen.

## <a name="high-level-platform-comparison"></a>Allgemeiner Plattformvergleich

Die folgende Tabelle enthält eine allgemeine Liste von Azure Maps-Features mit entsprechenden Google Maps-Features. Hierbei handelt es sich nicht um eine vollständige Liste aller Azure Maps-Features. Azure Maps bietet noch weitere Features wie etwa Barrierefreiheit, Geofencing, Isochrone, räumliche Operationen, direkten Zugriff auf Kartenkacheln, Batchdienste und Vergleiche zur Datenabdeckung (Aufnahmenabdeckung).

| Google Maps-Feature         | Azure Maps-Unterstützung                     |
|-----------------------------|:--------------------------------------:|
| Web-SDK                     | ✓                                      |
| Android-SDK                 | ✓                                      |
| iOS SDK                     | Geplant                                |
| REST-Dienst-APIs           | ✓                                      |
| Richtungen (Routing)        | ✓                                      |
| Entfernungsmatrix             | ✓                                      |
| Elevation                   | ✓ (Vorschau)                            |
| Geocodierung (vorwärts/rückwärts) | ✓                                      |
| Geolocation                 | –                                    |
| Nächstgelegene Straßen               | ✓                                      |
| Suche nach Orten               | ✓                                      |
| Details zu Orten              | N/A – Website und Telefonnummer verfügbar |
| Fotos von Orten               | –                                    |
| AutoVervollständigen von Orten          | ✓                                      |
| Ausrichtung an Straße                | ✓                                      |
| Geschwindigkeitsbegrenzungen                | ✓                                      |
| Statische Karten                 | ✓                                      |
| Statische Straßenansicht          | –                                    |
| Zeitzone                   | ✓                                      |
| In API eingebettete Karten           | –                                    |
| Karten-URLs                    | –                                    |

Google Maps bietet eine grundlegende schlüsselbasierte Authentifizierung. Azure Maps ermöglicht neben der grundlegenden schlüsselbasierten Authentifizierung auch Azure Active Directory-Authentifizierung. Azure Active Directory-Authentifizierung bietet im Vergleich zur grundlegenden schlüsselbasierten Authentifizierung mehr Sicherheitsfeatures.

## <a name="licensing-considerations"></a>Lizenzierungsaspekte

Beachten Sie bei der Migration von Google Maps zu Azure Maps die folgenden Informationen zur Lizenzierung.

* Bei Azure Maps wird die Nutzung interaktiver Karten basierend auf der Anzahl geladener Kartenkacheln abgerechnet. Bei Google Maps werden die Gebühren dagegen für das Laden des Kartensteuerelements berechnet. In den interaktiven Azure Maps-SDKs werden Kartenkacheln automatisch zwischengespeichert, um die Entwicklungskosten zu senken. Für jeweils 15 geladene Kartenkacheln wird eine Azure Maps-Transaktion generiert. Die interaktiven Azure Maps SDKs verwenden 512-Pixel-Kacheln und generieren im Durchschnitt maximal eine Transaktion pro Seitenansicht.
* Es ist oft kostengünstiger, statische Kartenbilder von Google Maps-Webdiensten durch das Azure Maps Web SDK zu ersetzen. Das Azure Maps Web SDK verwendet Kartenkacheln. Solange der Benutzer der Karte nicht schwenkt und zoomt, generiert der Dienst häufig nur einen Bruchteil einer Transaktion pro Kartenladevorgang. Die Möglichkeit zum Schwenken und Zoomen kann auf Wunsch über Optionen des Azure Maps Web SDK deaktiviert werden. Außerdem bietet das Azure Maps Web SDK deutlich mehr Optionen für die Datenvisualisierung als der statische Kartenwebdienst.
* Azure Maps ermöglicht es, Daten von seiner Plattform in Azure zu speichern. Daten können gemäß den [Nutzungsbedingungen](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) auch für bis zu sechs Monate an anderer Stelle zwischengespeichert werden.

Nachfolgend finden Sie einige zugehörige Ressourcen für Azure Maps:

* [Preisübersicht für Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/)
* [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
* [Nutzungsbedingungen für Azure Maps](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (in den Microsoft-Bedingungen für Onlinedienste inbegriffen)
* [Auswählen des richtigen Tarifs in Azure Maps](./choose-pricing-tier.md)

## <a name="suggested-migration-plan"></a>Empfohlener Migrationsplan

Nachfolgend finden Sie einen allgemeinen Migrationsplan.

1. Ermitteln Sie die von Ihrer Anwendung verwendeten Google Maps-SDKs und -Dienste. Vergewissern Sie sich, dass alternative SDKs und Dienste von Azure Maps zur Verfügung stehen.
2. Erstellen Sie unter [https://azure.com](https://azure.com) ein Azure-Abonnement, falls Sie noch keins besitzen.
3. Erstellen Sie ein Azure Maps-Konto ([Dokumentation](./how-to-manage-account-keys.md)) und einen Authentifizierungsschlüssel oder Azure Active Directory ([Dokumentation](./how-to-manage-authentication.md)).
4. Migrieren Sie den Anwendungscode.
5. Testen Sie Ihre migrierte Anwendung.
6. Stellen Sie die migrierte Anwendung in der Produktionsumgebung bereit.

## <a name="create-an-azure-maps-account"></a>Erstellen eines Azure Maps-Kontos

Führen Sie die folgenden Schritte aus, um ein Azure Maps-Konto zu erstellen und Zugriff auf die Azure Maps-Plattform zu erhalten:

1. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
2. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
3. Erstellen Sie ein [Azure Maps-Konto](./how-to-manage-account-keys.md). 
4. [Rufen Sie den Azure Maps-Abonnementschlüssel ab](./how-to-manage-authentication.md#view-authentication-details), oder richten Sie Azure Active Directory-Authentifizierung ein, um die Sicherheit zu erhöhen.

## <a name="azure-maps-technical-resources"></a>Technische Azure Maps-Ressourcen

Nachfolgend finden Sie eine Liste nützlicher technischer Ressourcen für Azure Maps.

- Übersicht: [https://azure.com/maps](https://azure.com/maps)
- Dokumentation: [https://aka.ms/AzureMapsDocs](./index.yml)
- Web SDK-Codebeispiele:[https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- Entwicklerforen: [https://aka.ms/AzureMapsForums](/answers/topics/azure-maps.html)
- Videos: [https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Blog: [https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Technikblog: [https://aka.ms/AzureMapsTechBlog](https://aka.ms/AzureMapsTechBlog)
- Azure Maps-Feedback (UserVoice): [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)
- [Jupyter Notebook für Azure Maps](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)

## <a name="migration-support"></a>Migrationsunterstützung

Entwickler können über die [Foren](/answers/topics/azure-maps.html) oder über eine der vielen Azure-Supportoptionen Unterstützung bei der Migration erhalten: [https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Es muss keine Bereinigung von Ressourcen durchgeführt werden.

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln erfahren Sie im Detail, wie Sie Ihre Google Maps-Anwendung migrieren:

> [!div class="nextstepaction"]
> [Migrieren einer Web-App](migrate-from-google-maps-web-app.md)

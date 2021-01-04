---
title: Erste Schritte mit dem Azure Maps-Visual in Power BI | Microsoft Azure Maps
description: In diesem Artikel erfahren Sie, wie das Microsoft Azure Maps-Visual für Power BI verwendet wird.
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: a563a7776e84289e38743057778e8fe10fd17503
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96904702"
---
# <a name="getting-started-with-the-azure-maps-power-bi-visual"></a>Erste Schritte mit dem Azure Maps-Visual in Power BI

<Token>**GILT FÜR:** ![Grünes Häkchen.](media/power-bi-visual/yes.png) Power BI-Dienst für **_Consumer_* _ ![Grünes Häkchen.](media/power-bi-visual/yes.png) Power BI-Dienst für Designer und Entwickler ![Grünes Häkchen.](media/power-bi-visual/yes.png) Power BI Desktop ![X, das Nein anzeigt.](media/power-bi-visual/no.png) Erfordert eine Pro- oder Premium-Lizenz</Token>

Dieser Artikel zeigt die Verwendung des Microsoft Azure Maps-Visuals für Power BI.

> [!NOTE]
> Dieses Visual kann sowohl in Power BI Desktop als auch im Power BI-Dienst erstellt und angezeigt werden. Die Schritte und Abbildungen in diesem Artikel beziehen sich auf Power BI Desktop.

Das Azure Maps-Visual für Power BI bietet umfangreiche Datenvisualisierungen für räumliche Daten auf einer Karte. Es wird geschätzt, dass mehr als 80 Prozent der Geschäftsdaten einen Standortkontext aufweisen. Anhand des Azure Maps-Visuals können Sie Erkenntnisse zur Beziehung zwischen diesem Standortkontext und Ihren Geschäftsdaten sowie zu den Auswirkungen des Standortkontexts auf Ihre Geschäftsdaten gewinnen.

![Power BI Desktop mit vom Azure Maps-Visual angezeigten Geschäftsdaten](media/power-bi-visual/azure-maps-visual-hero.png)

## <a name="what-is-sent-to-azure"></a>Welche Daten werden an Azure gesendet?

Das Azure Maps-Visual stellt eine Verbindung mit dem in Azure gehosteten Clouddienst her, um Standortdaten wie Kartenabbildungen und Koordinaten zum Erstellen der Kartenvisualisierung abzurufen. 

-   Details über den Bereich im Fokus der Karte werden an Azure gesendet, um die zum Rendern der Karten-Canvas (auch als Kartenkacheln bezeichnet) benötigten Abbildungen abzurufen. 
-   Daten in den Buckets für Standort, Breitengrad und Längengrad können an Azure gesendet werden, um Kartenkoordinaten abzurufen. Dieser Vorgang wird als Geocodierung bezeichnet. 
-   Telemetriedaten können zur Integrität des visuellen Elements (d. h. Absturzberichte) gesammelt werden, wenn die Telemetrieoption in Power BI aktiviert ist.

Abgesehen von den oben beschriebenen Szenarien werden keine weiteren Daten, die auf der Karte überlagert werden, an die Azure Maps-Server gesendet. Das gesamte Rendering von Daten erfolgt lokal innerhalb des Clients.

Möglicherweise müssen Sie bzw. Ihr Administrator Ihre Firewall aktualisieren, um den Zugriff auf die Azure Maps-Plattform zu gestatten. Diese verwendet die folgende URL:

> `https://atlas.microsoft.com`

Weitere Informationen zu Datenschutz und Nutzungsbedingungen im Zusammenhang mit dem Azure Maps-Visual finden Sie unter [Rechtliche Hinweise zu Microsoft Azure](https://azure.microsoft.com/support/legal/).

## <a name="azure-maps-visual-preview-behavior-and-requirements"></a>Verhalten und Anforderungen des Azure Maps-Visuals (Vorschau)

Für das _ *Azure Maps**-Visual sind einige Aspekte und Anforderungen zu beachten. :

-   Das **Azure Maps**-Visual (Vorschau) muss in Power BI Desktop aktiviert werden. Zum Aktivieren des **Azure Maps**-Visuals wählen Sie **Datei** &gt; **Optionen und Einstellungen** &gt; **Optionen** &gt; **Previewfunktionen** aus und aktivieren dann das Kontrollkästchen **Azure Maps-Visual**. Wenn das Azure Maps-Visual anschließend nicht verfügbar ist, muss wahrscheinlich eine Option des Mandantenadministrators im Verwaltungsportal aktiviert werden.
-   Das Dataset muss Felder mit Informationen zu **Breitengrad** und **Längengrad** enthalten. Die Geocodierung von Standortfeldern wird in einem zukünftigen Update hinzugefügt.
-   Das integrierte Legendensteuerelement für Power BI wird in dieser Vorschau derzeit nicht angezeigt. Es wird in einem späteren Update hinzugefügt.

## <a name="use-the-azure-maps-visual-preview"></a>Verwenden des Azure Maps-Visuals (Vorschau)

Wählen Sie nach dem Aktivieren des **Azure Maps**-Visuals im Bereich **Visualisierungen** das Symbol **Azure Maps** aus.

![Schaltfläche des Azure Maps-Visuals im Bereich „Visualisierungen“](media/power-bi-visual/azure-maps-in-visualizations-pane.png)

Power BI erstellt eine leere Design-Canvas für das Azure Maps-Visual. In der Vorschauversion wird zusätzlich ein Haftungsausschluss angezeigt.

![Power BI Desktop mit geladenem Azure Maps-Visual im Anfangszustand](media/power-bi-visual/visual-initial-load.png)

Gehen Sie zum Laden des Azure Maps-Visuals folgendermaßen vor:

1.  Ziehen Sie Datenfelder mit Informationen zu Breitengrad- und Längengradkoordinaten aus dem Bereich **Felder** in die Buckets **Breitengrad** und/oder **Längengrad**. Dies sind die Mindestdaten, die zum Laden des Azure Maps-Visuals erforderlich sind.
    
    > [!div class="mx-imgBorder"]
    > ![Azure Maps-Visual mit Blasendarstellung von Punkten auf der Karte nach Angabe von Feldern für Breiten- und Längengrad](media/power-bi-visual/bubble-layer.png)

2.  Ziehen Sie ein Kategoriefeld in den Bucket **Legende** des Bereichs **Felder**, um die Daten der Kategorisierung entsprechend zu färben. In diesem Beispiel verwenden wir die Spalte **AdminDistrict** (auch als „Staat“ oder „Provinz“ bezeichnet).  
    
    > [!div class="mx-imgBorder"]
    > ![Azure Maps-Visual mit farbiger Blasendarstellung von Punkten auf der Karte nach Angabe des Legendenfelds](media/power-bi-visual/bubble-layer-with-legend-color.png)

    > [!NOTE]
    > Das integrierte Legendensteuerelement für Power BI wird in dieser Vorschau derzeit nicht angezeigt. Es wird in einem späteren Update hinzugefügt.

3.  Um die Daten relativ zu skalieren, ziehen Sie ein Measure in den Bucket **Größe** des Bereichs **Felder**. In diesem Beispiel verwenden wir die Spalte für **Umsatz**.  
    
    > [!div class="mx-imgBorder"]
    > ![Azure Maps-Visual mit farbiger und skalierter Blasendarstellung von Punkten auf der Karte nach Angabe des Größenfelds](media/power-bi-visual/bubble-layer-with-legend-color-and-size.png)

4.  Verwenden Sie die Optionen im Bereich **Format**, um das Datenrendering anzupassen. Die folgende Abbildung zeigt dieselbe Karte wie oben, allerdings wurde die Option für die Fülltransparenz der Blasenebenen auf 50 % festgelegt, und die Option für Umrisse mit hohem Kontrast wurde aktiviert.  
    
    > [!div class="mx-imgBorder"]
    > ![Azure Maps-Visual mit Blasendarstellung von Punkten auf der Karte in einem benutzerdefinierten Stil](media/power-bi-visual/bubble-layer-styled.png)

## <a name="fields-pane-buckets"></a>Buckets des Bereichs „Felder“

Im Bereich **Felder** des Azure Maps-Visuals stehen die folgenden Datenbuckets zur Verfügung.

| Feld     | BESCHREIBUNG  |
|-----------|--------------|
| Breitengrad  | Das Feld, in dem der Breitengradwert der Datenpunkte angegeben wird. Breitengradwerte müssen als Gradangabe zwischen -90 und 90 im Dezimalformat vorliegen.  |
| Längengrad | Das Feld, in dem der Längengradwert der Datenpunkte angegeben wird. Längengradwerte müssen als Gradangabe zwischen -180 und 180 im Dezimalformat vorliegen.  |
| Legende    | Das Feld, das zum Kategorisieren der Daten und zum Zuweisen einer eindeutigen Farbe für Datenpunkte in den einzelnen Kategorien verwendet wird. Wenn dieser Bucket gefüllt wird, erscheint im Bereich **Format** ein Abschnitt **Datenfarben**, der die Anpassung der Farben ermöglicht. |
| Size      | Das Measure, das für die relative Dimensionierung von Datenpunkten auf der Karte verwendet wird.   |
| QuickInfos  | Zusätzliche Datenfelder, die beim Zeigen auf Formen in QuickInfos angezeigt werden. |

## <a name="map-settings"></a>Karteneinstellungen

Mit den Optionen im Abschnitt **Karteneinstellungen** im Bereich „Format“ können Sie die Anzeige der Karte und deren Reaktion auf Aktualisierungen anpassen.

| Einstellung             | BESCHREIBUNG  |
|---------------------|--------------|
| Automatischer Zoom           | Ändert die Größe der Karte automatisch auf die Daten, die über den Bereich **Felder** des Visuals geladen werden. Wenn sich die Daten ändern, wird die Position der Karte entsprechend aktualisiert. Wenn sich der Schieberegler in der Position **Aus** befindet, werden für die Standardkartenansicht zusätzliche Kartenansichtseinstellungen angezeigt. |
| Erdumrundung          | Ermöglicht dem Benutzer das unbegrenzte horizontale Schwenken der Karte. |
| Stilauswahl        | Fügt der Karte eine Schaltfläche hinzu, die es den Lesern des Berichts ermöglicht, den Stil der Karte zu ändern. |
| Navigationssteuerelemente | Fügt der Karte Schaltflächen als weitere Methode hinzu, mit der die Leser des Berichts die Karte drehen sowie die Größe und Neigung der Karte ändern können. Weitere Informationen zu den verschiedenen Navigationsmöglichkeiten der Benutzer in der Karte finden Sie in diesem Dokument unter [Navigieren auf einer Karte](map-accessibility.md#navigating-the-map). |
| Kartenstil           | Der Stil der Karte. Weitere Informationen zu [unterstützten Kartenstilen](supported-map-styles.md) finden Sie in diesem Dokument. |

### <a name="map-view-settings"></a>Kartenansichtseinstellungen

Wenn sich der Schieberegler für **automatischen Zoom** in der Position **Aus** befindet, werden die folgenden Einstellungen angezeigt, und der Benutzer kann die Standardinformationen zur Kartenansicht angeben.

| Einstellung          | BESCHREIBUNG   |
|------------------|---------------|
| Zoom             | Der Standardzoomfaktor der Karte. Diese Angabe kann eine Zahl zwischen 0 und 22 sein. |
| Breitengrad Mitte  | Der Standardbreitengrad in der Mitte der Karte. |
| Längengrad Mitte | Der Standardlängengrad in der Mitte der Karte. |
| Überschrift          | Die Standardausrichtung der Karte in Grad. Hierbei steht 0 für Norden, 90 für Osten, 180 für Süden und 270 für Westen. Diese Angabe kann eine beliebige Zahl zwischen 0 und 360 sein. |
| Neigung            | Die Standardneigung der Karte in Grad zwischen 0 und 60. 0 steht für einen senkrechten Blick auf die Karte. |

## <a name="considerations-and-limitations"></a>Überlegungen und Einschränkungen

Das Azure Maps-Visual ist in den folgenden Diensten und Anwendungen verfügbar:

| Dienst/App                              | Verfügbarkeit |
|------------------------------------------|--------------|
| Power BI Desktop                         | Ja          |
| Power BI-Dienst (app.powerbi.com)       | Ja          |
| Mobile Power BI-Anwendungen             | Ja          |
| Veröffentlichen im Web aus Power BI                  | Nein           |
| Power BI Embedded                        | Nein           |
| Einbettung des Power BI-Diensts (PowerBI.com) | Ja          |

Unterstützung für zusätzliche Power BI-Dienste/-Apps wird in zukünftigen Updates hinzugefügt.

**Wo ist Azure Maps verfügbar?**

Abgesehen von folgenden Ausnahmen ist Azure Maps aktuell in allen Ländern und Regionen verfügbar:

- China
- Südkorea

Details zur Abdeckung der verschiedenen Azure Maps-Dienste, die dieses Visual unterstützen, finden Sie im Dokument [Informationen zur geografischen Abdeckung](geographic-coverage.md).

**Welche Webbrowser werden vom Azure Maps-Visual unterstützt?**

In dieser Dokumentation finden Sie Informationen zu den vom [Azure Maps-Web-SDK unterstützten Browsern](supported-browsers.md).

**Wie viele Datenpunkte kann ich visualisieren?**

Dieses Visual unterstützt bis zu 30.000 Datenpunkte.

**Können Adressen oder andere Standortzeichenfolgen in diesem Visual verwendet werden?**

Die anfängliche Vorschau dieses Visuals unterstützt nur Werte für Breiten- und Längengrade im Dezimalformat. Bei einem zukünftigen Update wird die Unterstützung für Adressen und andere Standortzeichenfolgen hinzugefügt.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Azure Maps-Visual in Power BI:

> [!div class="nextstepaction"]
> [Grundlegendes zu Ebenen im Azure Maps-Visual in Power BI](power-bi-visual-understanding-layers.md)

> [!div class="nextstepaction"]
> [Verwalten des Azure Maps-Visuals in Ihrer Organisation](power-bi-visual-manage-access.md)

Passen Sie das Visual an:

> [!div class="nextstepaction"]
> [Tipps und Tricks zur Farbformatierung in Power BI](/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [Anpassen von Visualisierungstiteln, -hintergründen und -legenden](/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)
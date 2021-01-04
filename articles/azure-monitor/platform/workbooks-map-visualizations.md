---
title: Kachelvisualisierungen von Azure Monitor-Arbeitsmappen
description: Erfahren Sie mehr über Kachelvisualisierungen von Azure Monitor-Arbeitsmappen.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: lagayhar
ms.openlocfilehash: 9b79efeeb90627bee6096c9142d8180896150295
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96439613"
---
# <a name="map-visualization"></a>Kartenvisualisierung

Die Kartenvisualisierung hilft, Probleme in bestimmten Regionen auszumachen und aggregierte allgemeine Ansichten der Überwachungsdaten zu erstellen, indem sie die Möglichkeit bietet, alle Daten zu aggregieren, die den jeweiligen Standorten/Ländern/Regionen zugeordnet sind.

Der folgende Screenshot zeigt die Gesamtanzahl der Transaktionen und die End-to-End-Latenz verschiedener Speicherkonten. Hier wird die Größe durch die Gesamtanzahl der Transaktionen bestimmt. Die Farbmetriken unter der Karte zeigen die End-to-End-Latenz. Auf den ersten Blick ist die Anzahl der Transaktionen in der Region **USA, Westen** im Vergleich zur Region **USA, Osten** gering. Die End-to-End Latenz-für die Region **USA, Westen** ist jedoch höher als für die Region **USA, Osten**. Dies liefert erste Hinweise darauf, dass bei **USA, Westen** etwas nicht stimmt.

![Screenshot einer Karte mit den Azure-Standorten](./media/workbooks-map-visualizations/map-performance-example.png)

## <a name="adding-a-map"></a>Hinzufügen einer Karte

Eine Karte kann visualisiert werden, wenn die zugrundeliegenden Daten/Metriken Informationen zu Breitengrad/Längengrad, Azure-Ressourcen, Azure-Standorten oder Land/Region, Name oder Landeskennzahl/Regionscode enthalten.

### <a name="using-azure-location"></a>Verwenden des Azure-Standorts

1. Stellen Sie die Arbeitsmappe auf den Bearbeitungsmodus um, indem Sie auf der Symbolleiste das Element **Bearbeiten** auswählen.
2. Wählen Sie **Hinzufügen** und dann *Abfrage hinzufügen* aus.
3. Ändern Sie *Datenquelle* in `Azure Resource Graph`. Wählen Sie dann ein Abonnement mit einem Speicherkonto aus.
4. Geben Sie die folgende Abfrage für die Analyse ein, und wählen Sie **Abfrage ausführen** aus.

    ```kusto
    where  type =~ 'microsoft.storage/storageaccounts'
    | summarize count() by location
    ```

5. Legen Sie *Größe* auf `Large` fest.
6. Legen Sie *Visualisierung* auf `Map` fest.
7. Alle Einstellungen werden automatisch aufgefüllt. Wählen Sie für benutzerdefinierte Einstellungen die Schaltfläche **Karteneinstellungen** aus, um den Bereich mit den Einstellungen zu öffnen.
8. Es folgt ein Screenshot der Kartenvisualisierung, in der für das ausgewählte Abonnement Speicherkonten für jede Azure-Region gezeigt werden.

![Screenshot der Karte der Azure-Standorte mit der obigen Abfrage](./media/workbooks-map-visualizations/map-azure-location-example.png)

### <a name="using-azure-resource"></a>Verwenden von Azure-Ressourcen

1. Stellen Sie die Arbeitsmappe auf den Bearbeitungsmodus um, indem Sie auf der Symbolleiste das Element **Bearbeiten** auswählen.
2. Wählen Sie **Hinzufügen** und dann *Metrik hinzufügen* aus.
3. Wählen Sie ein Abonnement mit Speicherkonten.
4. Ändern Sie *Ressourcentyp* in `storage account`, und wählen Sie in *Ressource* mehrere Speicherkonten aus.
5. Wählen Sie **Metrik hinzufügen** aus, und fügen Sie eine Transaktionsmetrik hinzu.
    1. Namespace: `Account`
    2. Metrik: `Transactions`
    3. Aggregation: `Sum`
    
    ![Screenshot der Transaktionsmetrik](./media/workbooks-map-visualizations/map-transaction-metric.png)
1. Wählen Sie **Metrik hinzufügen** aus, und fügen Sie die Metrik „Success E2E“ hinzu.
    1. Namespace: `Account`
    1. Metrik: `Success E2E Latency`
    1. Aggregation: `Average`
    
    ![Screenshot der Erfolgsmetrik für die End-to-End-Latenz](./media/workbooks-map-visualizations/map-e2e-latency-metric.png)
1. Legen Sie *Größe* auf `Large` fest.
1. Legen Sie *Visualisierungsgröße* auf `Map` fest.
1. Legen Sie in **Karteneinstellungen** die folgenden Einstellungen fest:
    1. Standortinformationen unter Verwendung von: `Azure Resource`
    1. Azure-Ressourcenfeld: `Name`
    1. Größe nach: `microsoft.storage/storageaccounts-Transaction-Transactions`
    1. Aggregation für Standort: `Sum of values`
    1. Färbungstyp: `Heatmap`
    1. Farbe nach: `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. Aggregation für Farbe: `Sum of values`
    1. Farbpalette: `Green to Red`
    1. Mindestwert: `0`
    1. Metrikwert: `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. Weitere Metriken aggregieren nach: `Sum of values`
    1. Wählen Sie das Feld **Benutzerdefinierte Formatierung** aus.
    1. Einheit: `Milliseconds`
    1. Stil: `Decimal`
    1. Maximale Nachkommastellen: `2`

### <a name="using-countryregion"></a>Verwenden von Land/Region

1. Stellen Sie die Arbeitsmappe auf den Bearbeitungsmodus um, indem Sie auf der Symbolleiste das Element **Bearbeiten** auswählen.
2. Wählen Sie *Hinzufügen* und dann *Abfrage hinzufügen* aus.
3. Ändern Sie *Datenquelle* in `Log`.
4. Wählen Sie `Application Insights` für *Ressourcentyp* und dann eine beliebige Application Insights-Ressource mit Daten zu Seitenansichten aus.
5. Geben Sie im Abfrage-Editor die KQL für Ihre Analyse ein, und wählen Sie **Abfrage ausführen** aus.

    ```kusto
    pageViews
    | project duration, itemCount, client_CountryOrRegion
    | limit 20
    ```

6. Legen Sie die Größenwerte auf `Large` fest.
7. Legen Sie die Visualisierung auf `Map` fest.
8. Alle Einstellungen werden automatisch aufgefüllt. Wählen Sie für benutzerdefinierte Einstellungen die Option **Karteneinstellungen** aus.

### <a name="using-latitudelocation"></a>Verwenden von Breitengrad/Standort

1. Stellen Sie die Arbeitsmappe auf den Bearbeitungsmodus um, indem Sie auf der Symbolleiste das Element **Bearbeiten** auswählen.
2. Wählen Sie *Hinzufügen* und dann *Abfrage hinzufügen* aus.
3. Ändern Sie *Datenquelle* in `JSON`.
1. Geben Sie im Abfrage-Editor die folgenden JSON-Daten ein, und wählen Sie **Abfrage ausführen** aus.
1. Legen Sie *Größe* auf `Large` fest.
1. Legen Sie *Visualisierung* auf `Map` fest.
1. Legen Sie in **Karteneinstellungen** unter „Metrikeinstellungen“ *Metrikbezeichnung* auf `displayName` fest. Wählen Sie dann **Speichern und schließen** aus.

Die nachstehende Kartenvisualisierung zeigt Benutzer für jeden Standort mit Längen- und Breitengrad mit der ausgewählten Bezeichnung für Metriken.

![Screenshot einer Kartenvisualisierung, die Benutzer für jeden Standort mit Längen- und Breitengrad mit der ausgewählten Bezeichnung für Metriken zeigt](./media/workbooks-map-visualizations/map-latitude-longitude-example.png)

## <a name="map-settings"></a>Karteneinstellungen

### <a name="layout-settings"></a>Layouteinstellungen

| Einstellung | Erklärung |
|:-------------|:-------------|
| `Location info using` | Wählen Sie eine Methode aus, mit der Sie den Standort der auf der Karte gezeigten Elemente bestimmen können. <br> **Breitengrad/Längengrad**: Wählen Sie diese Option aus, wenn es Spalten mit Informationen zu Breiten- und Längengrad gibt. Jede Zeile mit Angaben zu Längen- und Breitengrad wird als eigenes Element auf der Karte gezeigt. <br> **Azure-Standort**: Wählen Sie diese Option aus, wenn es eine Spalte mit Azure-Standortinformationen (eastus, westeurope, centralindia usw.) gibt. Wenn Sie diese Spalte angeben, werden die entsprechenden Breiten- und Längengrade für jeden Azure-Standort abgerufen und die zugehörigen Zeilen des Standorts (basierend auf der angegebenen Aggregation) gruppiert, um die Standorte auf der Karte anzuzeigen. <br> **Azure-Ressource**: Wählen Sie diese Option aus, wenn es eine Spalte mit Informationen zu Azure-Ressourcen (Speicherkonto, Cosmos DB-Konto usw.) gibt. Wenn Sie diese Spalte angeben, werden die entsprechenden Breiten- und Längengrade für jede Azure-Ressource abgerufen und die zugehörigen Zeilen des (Azure-) Standorts (basierend auf der angegebenen Aggregation) gruppiert, um die Standorte auf der Karte anzuzeigen. <br> **Land/Region:** Wählen Sie diese Option aus, wenn eine Spalte mit Informationen zu Land/Region Name/Code (US, USA, IN, India, CN, China) vorhanden ist. Wenn Sie diese Spalte angeben, werden die entsprechenden Breiten- und Längengrade für das jeweilige Land, die jeweilige Region oder den jeweiligen Code abgerufen. Die Zeilen werden dann mit der zugehörigen Landeskennzahl bzw. dem Regionscode oder dem Namen des Landes bzw. der Region gruppiert, um die Standorte auf der Karte anzuzeigen. „Ländername“ und „Landeskennzahl“ werden nicht gemeinsam als einzelne Entität auf der Karte gruppiert.
| `Latitude/Longitude` | Diese beiden Optionen werden angezeigt, wenn der Wert des Felds „Standortinformationen“ wie folgt lautet: Breitengrad/Längengrad. Wählen Sie die Spalte mit dem Breitengrad im Feld „Breitengrad“ und dem Längengrad im Feld „Längengrad“ aus. |
| `Azure location field` | Diese Option wird gezeigt, wenn der Wert des Felds „Standortinformationen“ wie folgt lautet: Azure-Standort. Wählen Sie die Spalte mit den Informationen zum Azure-Standort aus. |
| `Azure resource field` | Diese Option wird gezeigt, wenn der Wert des Felds „Standortinformationen“ wie folgt lautet: Azure-Ressource. Wählen Sie die Spalte mit den Informationen zur Azure-Ressource aus. |
| `Country/Region field` | Diese Option wird gezeigt, wenn der Wert des Felds „Standortinformationen“ wie folgt lautet: Land oder Region. Wählen Sie die Spalte mit den Informationen zu Land/Region aus. |
| `Size by` | Diese Option steuert die Größe der auf der Karte gezeigten Elemente. Die Größe hängt vom Wert in der Spalte ab, die vom Benutzer angegeben wird. Derzeit ist der Radius des Kreises direkt proportional zur Quadratwurzel des Spaltenwerts. Bei Auswahl von „Ohne...“ werden alle Kreise mit der Standardregionsgröße gezeigt.|
| `Aggregation for location` | Dieses Feld gibt an, wie die Spalten **Größe nach** aggregiert werden sollen, die denselben Azure-Standort, dieselbe Azure-Ressource bzw. dasselbe Azure-Land bzw. dieselbe Azure-Region enthalten. |
| `Minimum region size` | Dieses Feld gibt den Mindestradius des auf der Karte gezeigten Elements an. Es kommt zum Einsatz, wenn es einen signifikanten Unterschied bei den Werten in der Spalte „Größe nach“ gibt, sodass kleinere Elemente auf der Karte kaum sichtbar sind. |
| `Maximum region size` | Dieses Feld gibt den maximalen Radius des auf der Karte gezeigten Elements an. Es kommt zum Einsatz, wenn die Werte in der Spalte „Größe nach“ extrem groß sind und einen großen Bereich der Karte einnehmen.|
| `Default region size` | Dieses Feld gibt den Standardradius des auf der Karte gezeigten Elements an. Der Standardradius wird verwendet, wenn die Spalte „Größe nach“ entweder keinen Wert oder 0 enthält.|
| `Minimum value` | Der zum Berechnen der Regionsgröße verwendete Mindestwert. Falls nicht angegeben, ist der Mindestwert der kleinste Wert nach Aggregation. |
| `Maximum value` | Der zum Berechnen der Regionsgröße verwendete Höchstwert. Falls nicht angegeben, ist der Höchstwert der größte Wert nach Aggregation.|
| `Opacity of items on Map` | Dieses Feld gibt an, wie transparent die auf der Karte gezeigten Elemente sind. „Deckkraft 1“ bedeutet keine Transparenz. „Deckkraft 0“ bedeutet, dass Elemente auf der Karte nicht sichtbar sind. Wenn es zu viele Elemente auf der Karte gibt, kann die Deckkraft auf einen niedrigen Wert festgelegt werden, sodass alle sich überlappenden Elemente sichtbar sind.|

### <a name="color-settings"></a>Farbeinstellungen

| Färbungstyp | Erläuterung |
|:------------- |:-------------|
| `None` | Alle Knoten haben die gleiche Farbe. |
| `Thresholds` | Bei diesem Typ werden Zellfarben durch Schwellenwertregeln festgelegt (z. B. _CPU > 90 % => Rot, 60 % > CPU > 90 % => Gelb, CPU < 60 % => Grün_). <ul><li> **Farbe nach**: Der Wert dieser Spalte wird von Logik für Schwellenwerte/Wärmebilder verwendet.</li> <li>**Aggregation für Farbe**: Dieses Feld gibt an, wie die Spalten **Größe nach** aggregiert werden sollen, die denselben Azure-Standort, dieselbe Azure-Ressource bzw. dasselbe Azure-Land bzw. dieselbe Azure-Region enthalten. </li> <ul> |
| `Heatmap` | Bei diesem Typ werden die Zellen auf Grundlage der Farbpalette und des Felds „Farbe nach“ mit Farben versehen. Hierfür gelten dieselben Optionen für **Farbe nach** und **Aggregation für Farbe** wie bei Schwellenwerten. |

### <a name="metric-settings"></a>Metrikeinstellungen
| Einstellung | Erklärung |
|:------------- |:-------------|
| `Metric Label` | Diese Option wird gezeigt, wenn der Wert des Felds „Standortinformationen“ wie folgt lautet: Breitengrad/Längengrad. Mit dieser Funktion kann der Benutzer die Bezeichnung auswählen, die für die Metriken unter der Karte gezeigt werden soll. |
| `Metric Value` | Dieses Feld gibt den Metrikwert an, der unter der Karte gezeigt werden soll. |
| `Create 'Others' group after` | Dieses Feld gibt den Grenzwert an, bevor die Gruppe „Andere“ erstellt wird. |
| `Aggregate 'Others' metrics by` | Dieses Feld gibt die für die Gruppe „Andere“ verwendete Aggregation an, falls gezeigt. |
| `Custom formatting` | In diesem Feld können Sie Einheiten, Stil und Formatierungsoptionen für Zahlenwerte festlegen. Dies entspricht der [benutzerdefinierten Formatierung des Rasters](workbooks-grid-visualizations.md#custom-formatting).|

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [in Arbeitsmappen wabenförmige Visualisierungen](workbooks-honey-comb.md) erstellen.
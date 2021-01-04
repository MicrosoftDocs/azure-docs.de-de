---
title: Visualisierungen
description: Verwenden von Azure Synapse-Notebooks zum Visualisieren Ihrer Daten
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 09/13/2020
ms.openlocfilehash: 73b18d15ad054f1c485d6f61cdefe54993148bc4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450531"
---
# <a name="visualize-data"></a>Visualisieren von Daten
Azure Synapse ist ein integrierter Analysedienst zur schnelleren Gewinnung von Erkenntnissen aus Data Warehouses und Big Data-Analysesystemen. Die Datenvisualisierung ist eine Schlüsselkomponente, um Erkenntnisse über Ihre Daten gewinnen zu können. Sie hilft, sowohl Big Data als auch kleine Datenmengen für den Menschen verständlicher zu gestalten. Sie erleichtert auch die Erkennung von Mustern, Trends und Ausreißern in Datengruppen. 

Bei der Verwendung von Apache Spark in Azure Synapse Analytics gibt es verschiedene integrierte Optionen, die Ihnen bei der Visualisierung Ihrer Daten helfen, einschließlich Diagrammoptionen für Synapse-Notebooks, Zugriff auf beliebte Open-Source-Bibliotheken und die Integration mit Synapse SQL und Power BI.

## <a name="notebook-chart-options"></a>Notebook: Diagrammoptionen
Wenn Sie ein Azure Synapse-Notebook verwenden, können Sie Ihre tabellarische Ergebnisansicht mithilfe von Diagrammoptionen in ein angepasstes Diagramm umwandeln. Hier können Sie Ihre Daten visualisieren, ohne Code schreiben zu müssen. 

### <a name="displaydf-function"></a>display(df): Funktion
Die Funktion ```display``` ermöglicht es Ihnen, SQL-Abfragen und Apache Spark-Datenrahmen und RDDs in umfangreiche Datenvisualisierungen zu verwandeln. Die Funktion ```display``` kann für Datenrahmen oder RDDs verwendet werden, die in PySpark, Scala, Java und .NET erstellt wurden.

So greifen Sie auf die Diagrammoptionen zu
1. Die Ausgabe der ```%%sql```-Magic-Befehle wird standardmäßig in der gerenderten Tabellenansicht angezeigt. Sie können auch ```display(df)``` für Spark-Datenrahmen aufrufen oder die Funktion „Resilient Distributed Datasets“ (RDD) verwenden, um die gerenderte Tabellenansicht zu erzeugen. 
   
2. Sobald Sie über eine gerenderte Tabellenansicht verfügen, wechseln Sie zur Diagrammansicht.
   ![Integrierte Diagramme](./media/apache-spark-development-using-notebooks/synapse-built-in-charts.png#lightbox)

3. Sie können Ihre Visualisierung jetzt anpassen, indem Sie die folgenden Werte angeben:
   | Konfiguration | BESCHREIBUNG |
   |--|--| 
   | Diagrammtyp | Die Funktion ```display``` unterstützt eine breite Palette von Diagrammtypen, einschließlich Balkendiagramme, Punktdiagramme, Liniendiagramme und weitere. |
   | Schlüssel | Geben Sie den Wertebereich für die X-Achse an.|
   | Wert | Geben Sie den Wertebereich für die Werte der Y-Achse an. |
   | Reihengruppe | Dient zum Bestimmen der Gruppen für die Aggregation. | 
   | Aggregation | Methode zum Aggregieren von Daten in der Visualisierung.| 
   
   
    > [!NOTE]
    > Die ```display(df)```-Funktion erfasst standardmäßig nur die ersten 1.000 Zeilen der Daten zum Rendern der Diagramme. Überprüfen Sie die **Gesamtergebnisse der Aggregation**, und klicken Sie auf die Schaltfläche **Anwenden**, um die Diagrammgenerierung auf das gesamte Dataset anzuwenden. Ein Spark-Auftrag wird ausgelöst, wenn die Diagrammeinstellung geändert wird. Beachten Sie, dass es einige Minuten dauern kann, die Berechnung abzuschließen und das Diagramm zu rendern.
   
4. Nachdem Sie den Vorgang abgeschlossen haben, können Sie Ihre endgültige Visualisierung anzeigen und damit interagieren.

### <a name="displaydf-statistic-details"></a>display(df): Statistische Details
Sie können <code>display(df, summary = true)</code> verwenden, um die statistische Zusammenfassung eines jeweiligen Apache Spark-Datenrahmens zu überprüfen, dazu gehören Spaltennamen, Spaltentypen, eindeutige Werte und fehlende Werte für jede Spalte. Sie können auch eine bestimmte Spalte auswählen, um ihren Mindestwert, Höchstwert, Durchschnittswert und die Standardabweichung anzuzeigen.
    ![built-in-charts-summary](./media/apache-spark-development-using-notebooks/synapse-built-in-charts-summary.png#lightbox)
   
### <a name="displayhtmldf-option"></a>displayHTML(df): Option
Azure Synapse Analytics-Notebooks unterstützen HTML-Grafiken mithilfe der Funktion ```displayHTML```.

Die folgende Abbildung ist ein Beispiel für das Erstellen von Visualisierungen mithilfe von [D3.js](https://d3js.org/).

   ![d3-js-Beispiel](./media/apache-spark-data-viz/d3-boxplot.png#lightbox)

Führen Sie den folgenden Code aus, um die obige Visualisierung zu erstellen.

```python
displayHTML("""<!DOCTYPE html>
<meta charset="utf-8">

<!-- Load d3.js -->
<script src="https://d3js.org/d3.v4.js"></script>

<!-- Create a div where the graph will take place -->
<div id="my_dataviz"></div>
<script>

// set the dimensions and margins of the graph
var margin = {top: 10, right: 30, bottom: 30, left: 40},
  width = 400 - margin.left - margin.right,
  height = 400 - margin.top - margin.bottom;

// append the svg object to the body of the page
var svg = d3.select("#my_dataviz")
.append("svg")
  .attr("width", width + margin.left + margin.right)
  .attr("height", height + margin.top + margin.bottom)
.append("g")
  .attr("transform",
        "translate(" + margin.left + "," + margin.top + ")");

// Create Data
var data = [12,19,11,13,12,22,13,4,15,16,18,19,20,12,11,9]

// Compute summary statistics used for the box:
var data_sorted = data.sort(d3.ascending)
var q1 = d3.quantile(data_sorted, .25)
var median = d3.quantile(data_sorted, .5)
var q3 = d3.quantile(data_sorted, .75)
var interQuantileRange = q3 - q1
var min = q1 - 1.5 * interQuantileRange
var max = q1 + 1.5 * interQuantileRange

// Show the Y scale
var y = d3.scaleLinear()
  .domain([0,24])
  .range([height, 0]);
svg.call(d3.axisLeft(y))

// a few features for the box
var center = 200
var width = 100

// Show the main vertical line
svg
.append("line")
  .attr("x1", center)
  .attr("x2", center)
  .attr("y1", y(min) )
  .attr("y2", y(max) )
  .attr("stroke", "black")

// Show the box
svg
.append("rect")
  .attr("x", center - width/2)
  .attr("y", y(q3) )
  .attr("height", (y(q1)-y(q3)) )
  .attr("width", width )
  .attr("stroke", "black")
  .style("fill", "#69b3a2")

// show median, min and max horizontal lines
svg
.selectAll("toto")
.data([min, median, max])
.enter()
.append("line")
  .attr("x1", center-width/2)
  .attr("x2", center+width/2)
  .attr("y1", function(d){ return(y(d))} )
  .attr("y2", function(d){ return(y(d))} )
  .attr("stroke", "black")
</script>

"""
)

```

## <a name="popular-libraries"></a>Beliebte Bibliotheken
Wenn es um Datenvisualisierung geht, bietet Python mehrere Grafikbibliotheken, die über viele verschiedene Features verfügen. Standardmäßig enthält jeder Apache Spark-Pool in Azure Synapse Analytics eine Reihe von zusammengestellten und beliebten Open-Source-Bibliotheken. Sie können auch zusätzliche Bibliotheken und Versionen hinzufügen oder verwalten, indem Sie die Bibliotheksverwaltungsfunktionen von Azure Synapse Analytics verwenden. 

### <a name="bokeh"></a>Bokeh
Sie können HTML-oder interaktive Bibliotheken wie **bokeh** mithilfe von ```displayHTML(df)``` rendern. 

Das folgende Bild ist ein Beispiel für das Zeichnen von Glyphen auf einer Karte mithilfe von **bokeh**.

   ![bokeh-Beispiel](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png#lightbox)

Führen Sie den folgenden Beispielcode aus, um das obige Bild zu zeichnen.

```python
from bokeh.plotting import figure, output_file
from bokeh.tile_providers import get_provider, Vendors
from bokeh.embed import file_html
from bokeh.resources import CDN
from bokeh.models import ColumnDataSource

tile_provider = get_provider(Vendors.CARTODBPOSITRON)

# range bounds supplied in web mercator coordinates
p = figure(x_range=(-9000000,-8000000), y_range=(4000000,5000000),
           x_axis_type="mercator", y_axis_type="mercator")
p.add_tile(tile_provider)

# plot datapoints on the map
source = ColumnDataSource(
    data=dict(x=[ -8800000, -8500000 , -8800000],
              y=[4200000, 4500000, 4900000])
)

p.circle(x="x", y="y", size=15, fill_color="blue", fill_alpha=0.8, source=source)

# create an html document that embeds the Bokeh plot
html = file_html(p, CDN, "my plot1")

# display this html
displayHTML(html)
```

### <a name="matplotlib"></a>Matplotlib
Sie können standardmäßige Zeichnungsbibliotheken wie Matplotlib mithilfe der integrierten Renderingfunktionen für die einzelnen Bibliotheken rendern.

Die folgende Abbildung ist ein Beispiel für die Erstellung eines Balkendiagramms mithilfe von **Matplotlib**.
   ![Beispiel für ein Liniendiagramm.](./media/apache-spark-data-viz/matplotlib-example.png#lightbox)

Führen Sie den folgenden Beispielcode aus, um das obige Bild zu zeichnen.

```python
# Bar chart

import matplotlib.pyplot as plt

x1 = [1, 3, 4, 5, 6, 7, 9]
y1 = [4, 7, 2, 4, 7, 8, 3]

x2 = [2, 4, 6, 8, 10]
y2 = [5, 6, 2, 6, 2]

plt.bar(x1, y1, label="Blue Bar", color='b')
plt.bar(x2, y2, label="Green Bar", color='g')
plt.plot()

plt.xlabel("bar number")
plt.ylabel("bar height")
plt.title("Bar Chart Example")
plt.legend()
plt.show()
```

### <a name="additional-libraries"></a>Weitere Bibliotheken 
Über diese Bibliotheken hinaus enthält die Azure Synapse Analytics-Runtime auch den folgenden Satz von Bibliotheken, die häufig für die Visualisierung von Daten verwendet werden:
- [Matplotlib](https://matplotlib.org/)
- [Bokeh](https://bokeh.org/)
- [Seaborn](https://seaborn.pydata.org/) 

Die aktuellsten Informationen zu den verfügbaren Bibliotheken und Versionen finden Sie in der [Dokumentation](./spark/../apache-spark-version-support.md) zur Azure Synapse Analytics-Runtime.

## <a name="connect-to-power-bi-using-apache-spark--sql-on-demand"></a>Herstellen einer Verbindung mit Power BI mithilfe von Apache Spark und SQL On-Demand
Azure Synapse Analytics ist tief in Power BI integriert und ermöglicht Datentechnikern die Erstellung von Analyselösungen.

Azure Synapse Analytics ermöglicht den verschiedenen Berechnungsengines von Arbeitsbereichen die gemeinsame Nutzung von Datenbanken und Tabellen zwischen den Spark-Pools und dem serverlosen SQL-Pool. Unter Verwendung des [freigegebenen Metadatenmodells](https://docs.microsoft.com/azure/synapse-analytics/metadata/overview) können Sie Ihre Apache Spark-Tabellen mithilfe von SQL On-Demand abfragen. Danach können Sie Ihren SQL On-Demand-Endpunkt mit Power BI verbinden, um Ihre synchronisierten Spark-Tabellen einfach abzufragen.


## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zum Einrichten des Spark SQL DW-Connectors: [Synapse SQL-Connector](./spark/../synapse-spark-sql-pool-import-export.md)
- Anzeigen der Standardbibliotheken: [Azure Synapse Analytics-Runtime](../spark/apache-spark-version-support.md)
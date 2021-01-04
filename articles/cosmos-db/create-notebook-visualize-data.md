---
title: 'Tutorial: Erstellen eines Notebooks in Azure Cosmos DB zum Analysieren und Visualisieren der Daten'
description: 'Tutorial: Erfahren Sie, wie Sie mithilfe von integrierten Jupyter-Notebooks Daten in Azure Cosmos DB importieren, analysieren und die Ausgabe visuell darstellen.'
author: deborahc
ms.topic: tutorial
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 11/05/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: e16a738264e64e37cfa42722832dac7e34fee899
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339494"
---
# <a name="tutorial-create-a-notebook-in-azure-cosmos-db-to-analyze-and-visualize-the-data"></a>Tutorial: Erstellen eines Notebooks in Azure Cosmos DB zum Analysieren und Visualisieren der Daten
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In diesem Artikel wird beschrieben, wie Sie integrierte Jupyter-Notebooks verwenden, um Beispieldaten für den Einzelhandel in Azure Cosmos DB zu importieren. Sie erfahren, wie Sie die Magic-Befehle von SQL und Azure Cosmos DB verwenden, um Abfragen auszuführen, die Daten zu analysieren und die Ergebnisse zu visualisieren.

## <a name="prerequisites"></a>Voraussetzungen

* [Aktivieren der Notebooks-Unterstützung beim Erstellen des Azure Cosmos-Kontos](enable-notebooks.md)

## <a name="create-the-resources-and-import-data"></a>Erstellen der Ressourcen und Importieren von Daten
 
In diesem Abschnitt erstellen Sie die Azure Cosmos-Datenbank sowie den Container und importieren die Einzelhandelsdaten in den Container.

1. Navigieren Sie zu Ihrem Azure Cosmos-Konto, und öffnen Sie den **Daten-Explorer**.

1. Wechseln Sie zur Registerkarte **Notebooks** , wählen Sie `…` neben **Meine Notebooks** aus, und erstellen Sie ein **neues Notebook**. Wählen Sie **Python 3** als Standardkernel aus.

   :::image type="content" source="./media/create-notebook-visualize-data/create-new-notebook.png" alt-text="Erstellen eines neuen Notebooks":::

1. Nachdem ein neues Notebook erstellt wurde, können Sie es z. B. in **EinzelhandelsdatenVisualisierung.ipynb** umbenennen.

1. Als Nächstes erstellen Sie eine Datenbank mit dem Namen „RetailDemo“ und einen Container mit dem Namen „WebsiteData“, um die Einzelhandelsdaten zu speichern. Sie können „/CartID“ als Partitionsschlüssel verwenden. Kopieren Sie den folgenden Code, fügen Sie ihn in eine neue Zelle Ihres Notebooks ein, und führen Sie ihn aus:

   ```python
   import azure.cosmos
   from azure.cosmos.partition_key import PartitionKey

   database = cosmos_client.create_database_if_not_exists('RetailDemo')
   print('Database RetailDemo created')

   container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
   print('Container WebsiteData created')
   ```

   Wählen Sie zum Ausführen einer Zelle `Shift + Enter` aus, oder markieren Sie die Zelle, und wählen Sie in der Navigationsleiste des Daten-Explorers die Option zum **Ausführen der aktiven Zelle**.

   :::image type="content" source="./media/create-notebook-visualize-data/run-active-cell.png" alt-text="Ausführen der aktiven Zelle":::

   Die Datenbank und der Container werden in Ihrem aktuellen Azure Cosmos-Konto erstellt. Der Container wird mit 400 RU/s bereitgestellt. Nachdem die Datenbank und der Container erstellt wurden, wird die folgende Ausgabe angezeigt. 

   ```console
    Database RetailDemo created
    Container WebsiteData created
   ```

   Sie können auch die Registerkarte **Daten** aktualisieren und die neu erstellten Ressourcen anzeigen:

   :::image type="content" source="media/create-notebook-visualize-data/refresh-data-tab.png" alt-text="Aktualisieren der Registerkarte „Daten“ zum Anzeigen des neuen Containers":::

1. Als nächstes importieren Sie die Beispieldaten für den Einzelhandel in den Azure Cosmos-Container. Hier sehen Sie das Format eines Elements aus den Einzelhandelsdaten:

   ```json
    {
       "CartID":5399,
       "Action":"Viewed",
       "Item":"Cosmos T-shirt",
       "Price":350,
       "UserName":"Demo.User10",
       "Country":"Iceland",
       "EventDate":"2015-06-25T00:00:00",
       "Year":2015,"Latitude":-66.8673,
       "Longitude":-29.8214,
       "Address":"852 Modesto Loop, Port Ola, Iceland",
       "id":"00ffd39c-7e98-4451-9b91-b2bcf2f9a32d"
    }
   ```

   Für dieses Tutorial werden die Beispieldaten für den Einzelhandel im Azure-Blobspeicher gespeichert. Sie können sie in den Azure Cosmos-Container importieren, indem Sie den folgenden Code in eine neue Zelle einfügen. Sie können überprüfen, ob die Daten erfolgreich importiert wurden, indem Sie eine Abfrage zum Auswählen der Anzahl der Elemente ausführen.

   ```python
    # Read data from storage
    import urllib.request, json

    with urllib.request.urlopen("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/websiteData.json") as url:
      data = json.loads(url.read().decode())

    print("Importing data. This will take a few minutes...\n")

    for event in data:
     try: 
        container.upsert_item(body=event)
     except errors.CosmosHttpResponseError as e:
        raise
        
    ## Run a query against the container to see number of documents
    query = 'SELECT VALUE COUNT(1) FROM c'
    result = list(container.query_items(query, enable_cross_partition_query=True))

    print('Container with id \'{0}\' contains \'{1}\' items'.format(container.id, result[0]))
   ```

   Wenn Sie die vorherige Abfrage ausführen, wird die folgende Ausgabe zurückgegeben:

   ```console
   Importing data. This will take a few minutes...

   Container with id 'WebsiteData' contains '2654' items
   ```

## <a name="get-your-data-into-a-dataframe"></a>Laden der Daten in einen Datenrahmen

Bevor Sie Abfragen zum Analysieren der Daten ausführen, können Sie die Daten aus dem Container in einen [Pandas-Datenrahmen](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html) für die Analyse einlesen. Verwenden Sie den folgenden SQL-Magic-Befehl, um die Daten in einen Datenrahmen einzulesen:

```bash
%%sql --database {database_id} --container {container_id} --output outputDataframeVar 
{Query text}
```

Weitere Informationen finden Sie im Artikel [Integrierte Notebookbefehle und -features in Azure Cosmos DB](use-python-notebook-features-and-commands.md). Sie führen diese Abfrage aus: `SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c`. Die Ergebnisse werden in einem Pandas-Datenrahmen mit dem Namen „df_cosmos“ gespeichert. Fügen Sie den folgenden Befehl in eine neue Notebookzelle ein, und führen Sie ihn aus.

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```

Führen Sie in einer neuen Notebookzelle den folgenden Code aus, um die ersten 10 Elemente aus der Ausgabe zu lesen:

```python
# See a sample of the result
df_cosmos.head(10)
```

:::image type="content" source="./media/create-notebook-visualize-data/run-query-get-top10-items.png" alt-text="Ausführen der Abfrage, um die ersten 10 Elemente zu erhalten":::

## <a name="run-queries-and-analyze-your-data"></a>Ausführen von Abfragen und Analysieren der Daten

In diesem Abschnitt führen Sie einige Abfragen für die abgerufenen Daten aus.

* **Abfrage 1:** Führen Sie eine Gruppierungsabfrage für den Datenrahmen aus, um die Summe der Gesamtumsätze für jedes Land/jede Region zu erhalten und 5 Elemente aus den Ergebnissen anzuzeigen. Führen Sie in einer neuen Notebookzelle den folgenden Befehl aus:

   ```python
   df_revenue = df_cosmos.groupby("Country").sum().reset_index()
   display(df_revenue.head(5))
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/total-sales-revenue-output.png" alt-text="Ausgabe: Summe der Gesamtumsätze":::

* **Abfrage 2:** Öffnen Sie eine neue Notebookzelle, und führen Sie den folgenden Code aus, um eine Liste der fünf meistgekauften Elemente zu erhalten:

   ```python
   import pandas as pd

   ## What are the top 5 purchased items?
   pd.DataFrame(df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False).head(5), columns=['Count'])
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/top5-purchased-items.png" alt-text="Fünf meistgekaufte Artikel":::

## <a name="visualize-your-data"></a>Visualisieren Ihrer Daten  

1. Nachdem wir jetzt über unsere Umsatzdaten aus dem Azure Cosmos-Container verfügen, können Sie Ihre Daten mit einer Visualisierungsbibliothek Ihrer Wahl visualisieren. In diesem Tutorial verwenden wir die Bokeh-Bibliothek. Öffnen Sie eine neue Notebookzelle, und führen Sie den folgenden Code aus, um die Bokeh-Bibliothek zu installieren. Nachdem alle Anforderungen erfüllt sind, wird die Bibliothek installiert.

   ```python
   import sys
   !{sys.executable} -m pip install bokeh --user
   ```

1. Als nächstes bereiten Sie die grafische Darstellung der Daten auf einer Karte vor. Verknüpfen Sie die Daten in Azure Cosmos DB mit Länder-/Regionsinformationen in Azure Blob Storage, und konvertieren Sie das Ergebnis in das GeoJSON-Format. Kopieren Sie den folgenden Code in eine neue Notebookzelle, und führen Sie ihn aus.

   ```python
   import urllib.request, json
   import geopandas as gpd

   # Load country/region information for mapping
   countries = gpd.read_file("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/countries.json")

   # Merge the countries/regions dataframe with our data in Azure Cosmos DB, joining on country/region code
   df_merged = countries.merge(df_revenue, left_on = 'admin', right_on = 'Country', how='left')

   # Convert to GeoJSON so bokeh can plot it
   merged_json = json.loads(df_merged.to_json())
   json_data = json.dumps(merged_json)
   ```

1. Visualisieren Sie den Umsatz verschiedener Länder/Regionen auf einer Weltkarte, indem Sie den folgenden Code in einer neuen Notebookzelle ausführen:

   ```python
   from bokeh.io import output_notebook, show
   from bokeh.plotting import figure
   from bokeh.models import GeoJSONDataSource, LinearColorMapper, ColorBar
   from bokeh.palettes import brewer
    
   #Input GeoJSON source that contains features for plotting.
   geosource = GeoJSONDataSource(geojson = json_data)
    
   #Choose our choropleth color palette: https://bokeh.pydata.org/en/latest/docs/reference/palettes.html
   palette = brewer['YlGn'][8]
    
   #Reverse color order so that dark green is highest revenue
   palette = palette[::-1]
    
   #Instantiate LinearColorMapper that linearly maps numbers in a range, into a sequence of colors.
   color_mapper = LinearColorMapper(palette = palette, low = 0, high = 1000)
    
   #Define custom tick labels for color bar.
   tick_labels = {'0': '$0', '250': '$250', '500':'$500', '750':'$750', '1000':'$1000', '1250':'$1250', '1500':'$1500','1750':'$1750', '2000': '>$2000'}
    
   #Create color bar. 
   color_bar = ColorBar(color_mapper=color_mapper, label_standoff=8,width = 500, height = 20,
   border_line_color=None,location = (0,0), orientation = 'horizontal', major_label_overrides = tick_labels)
    
   #Create figure object.
   p = figure(title = 'Sales revenue by country', plot_height = 600 , plot_width = 1150, toolbar_location = None)
   p.xgrid.grid_line_color = None
   p.ygrid.grid_line_color = None
    
   #Add patch renderer to figure. 
   p.patches('xs','ys', source = geosource,fill_color = {'field' :'ItemRevenue', 'transform' : color_mapper},
              line_color = 'black', line_width = 0.25, fill_alpha = 1)
    
   #Specify figure layout.
   p.add_layout(color_bar, 'below')
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
   
   #Display figure.
   show(p)
   ```

   In der Ausgabe wird die Weltkarte mit unterschiedlichen Farben angezeigt. Die von dunkel nach hell verlaufenden Farben stellen die Länder/Regionen mit dem höchsten bzw. niedrigsten Umsatz dar.

   :::image type="content" source="./media/create-notebook-visualize-data/countries-revenue-map-visualization.png" alt-text="Visualisierung des Umsatzes nach Ländern/Regionen auf einer Karte":::

1. Sehen wir uns einen weiteren Fall der Datenvisualisierung an. Der Container „WebsiteData“ enthält einen Verlauf der Benutzer, die ein Element angezeigt, zum ihrem Warenkorb hinzugefügt und gekauft haben. Wir stellen die Konvertierungsrate der erworbenen Elemente als Plot dar. Führen Sie den folgenden Code in einer neuen Zelle aus, um die Konvertierungsrate der einzelnen Elemente zu visualisieren:

   ```python
   from bokeh.io import show, output_notebook
   from bokeh.plotting import figure
   from bokeh.palettes import Spectral3
   from bokeh.transform import factor_cmap
   from bokeh.models import ColumnDataSource, FactorRange
       
   # Get the top 10 items as an array
   top_10_items = df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False)[:10].index.values.tolist()
    
   # Filter our data to only these 10 items
   df_top10 = df_cosmos[df_cosmos['Item'].isin(top_10_items)]
    
   # Group by Item and Action, sorting by event count
   df_top10_sorted = df_top10.groupby(['Item', 'Action']).count().rename(columns={'Country':'ResultCount'}, inplace=False).reset_index().sort_values(['Item', 'ResultCount'], ascending = False).set_index(['Item', 'Action'])
    
   # Get sorted X-axis values - this way, we can display the funnel of view -> add -> purchase
   x_axis_values = df_top10_sorted.index.values.tolist()
    
   group = df_top10_sorted.groupby(['Item', 'Action'])
    
   # Specifiy colors for X axis
   index_cmap = factor_cmap('Item_Action', palette=Spectral3, factors=sorted(df_top10.Action.unique()), start=1, end=2)
    
   # Create the plot
    
   p = figure(plot_width=1200, plot_height=500, title="Conversion rate of items from View -> Add to cart -> Purchase", x_range=FactorRange(*x_axis_values), toolbar_location=None, tooltips=[("Number of events", "@ResultCount_max"), ("Item, Action", "@Item_Action")])
    
   p.vbar(x='Item_Action', top='ItemRevenue_max', width=1, source=group,
           line_color="white", fill_color=index_cmap, )
    
   #Configure how the plot looks
   p.y_range.start = 0
   p.x_range.range_padding = 0.05
   p.xgrid.grid_line_color = None
   p.xaxis.major_label_orientation = 1.2
   p.outline_line_color = "black"
   p.xaxis.axis_label = "Item"
   p.yaxis.axis_label = "Count"
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
    
   #Display figure.
   show(p)
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/visualize-purchase-conversion-rate.png" alt-text="Visualisierung der Kaufkonvertierungsrate":::

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Python-Notebookbefehlen finden Sie im Artikel [Verwenden integrierter Notebookbefehle und -features in Azure Cosmos DB](use-python-notebook-features-and-commands.md).

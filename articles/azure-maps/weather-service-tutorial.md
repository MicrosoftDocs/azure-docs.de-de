---
title: 'Tutorial: Verknüpfen von Sensordaten mit Wettervorhersagedaten unter Verwendung von Azure Notebooks (Python) mit Microsoft Azure Maps'
description: Tutorial zum Verknüpfen von Sensordaten mit Wettervorhersagedaten der Microsoft Azure Maps-Wetterdienste unter Verwendung von Azure Notebooks (Python)
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 6d2ede8ab49b22a22d8959ce296182a2210640d0
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905467"
---
# <a name="tutorial-join-sensor-data-with-weather-forecast-data-by-using-azure-notebooks-python"></a>Tutorial: Verknüpfen von Sensordaten mit Wettervorhersagedaten unter Verwendung von Azure Notebooks (Python)

> [!IMPORTANT]
> Die Azure Maps-Wetterdienste befinden sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Windenergie ist eine alternative Energiequelle zu fossilen Brennstoffen im Kampf gegen den Klimawandel. Da der Wind von Natur aus nicht immer gleich stark ist, müssen Betreiber von Windenergieanlagen Machine Learning-Modelle (ML) erstellen, um die Windenergiekapazität vorherzusagen. Diese Vorhersage ist erforderlich, um die Nachfrage nach Strom erfüllen und die Netzstabilität sicherstellen zu können. In diesem Tutorial wird Schritt für Schritt beschrieben, wie die Daten der Azure Maps-Wettervorhersage mit Demodaten für Wettermesswerte kombiniert werden. Wettervorhersagedaten werden durch Aufrufen der Azure Maps-Wetterdienste (Vorschau) angefordert.

In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
> * Verwenden von Datendateien in [Azure Notebooks](../notebooks/index.yml) in der Cloud
> * Laden von Demodaten aus einer Datei
> * Aufrufen von Azure Maps-REST-APIs in Python
> * Rendern von Standortdaten auf der Karte
> * Anreichern der Demodaten mit Azure Maps-Wetterdaten vom Typ [Tägliche Vorhersage](/rest/api/maps/weather/getdailyforecastpreview)
> * Zeichnen von Vorhersagedaten in Diagrammen


## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial sind folgende Vorbereitungen erforderlich:

1. Gehen Sie wie unter [Erstellen eines Kontos und Abrufen Ihres Schlüssels](quick-demo-map-app.md#create-an-azure-maps-account) beschrieben vor, um ein Azure Maps-Kontoabonnement im Tarif S0 zu erstellen.
2. Gehen Sie wie unter [Abrufen des Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) beschrieben vor, um den primären Abonnementschlüssel für Ihr Konto abzurufen.


Weitere Informationen zur Authentifizierung in Azure Maps finden Sie unter [Verwalten der Authentifizierung in Azure Maps](./how-to-manage-authentication.md).

Unter [Erstellen eines Azure-Notebooks](./tutorial-ev-routing.md#create-an-azure-notebooks-project) können Sie sich mit Azure-Notebooks und mit den ersten Schritten vertraut machen.

> [!Note]
> Die Jupyter Notebook-Datei für dieses Projekt kann aus dem [Jupyter Notebook-Repository für Wetterkarten](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data) heruntergeladen werden.

## <a name="load-the-required-modules-and-frameworks"></a>Laden der erforderlichen Module und Frameworks

Führen Sie das folgende Skript aus, um alle erforderlichen Module und Frameworks zu laden:

```python
import pandas as pd
import datetime
from IPython.display import Image, display
!pip install aiohttp
import aiohttp
```

## <a name="import-weather-data"></a>Importieren von Wetterdaten

Im Rahmen dieses Tutorials verwenden wir Wetterdaten von Sensoren, die an vier verschiedenen Windkraftanlagen installiert sind. Die Beispieldaten enthalten die Wettermesswerte für 30 Tage. Diese Messwerte werden über Wetterdatenzentren erfasst, die sich in der Nähe der einzelnen Windkraftstandorte befinden. In den Demodaten sind Messwerte für Temperatur, Windgeschwindigkeit und Windrichtung enthalten. Die Demodaten können [hier](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data/data) heruntergeladen werden. Das folgende Skript importiert Demodaten in das Azure-Notebook:

```python
df = pd.read_csv("./data/weather_dataset_demo.csv")
```

## <a name="request-daily-forecast-data"></a>Anfordern täglicher Vorhersagedaten

In unserem Szenario möchten wir eine tägliche Vorhersage für jeden Sensorstandort anfordern. Mit dem folgenden Skript wird die [API für tägliche Vorhersagen](/rest/api/maps/weather/getdailyforecastpreview) der Azure Maps-Wetterdienste (Vorschau) aufgerufen. Diese API gibt für jede Windkraftanlage Wettervorhersagen für die nächsten 15 Tage ab dem aktuellen Datum zurück.


```python
subscription_key = "Your Azure Maps key"

# Get a lists of unique station IDs and their coordinates 
station_ids = pd.unique(df[['StationID']].values.ravel())
coords = pd.unique(df[['latitude','longitude']].values.ravel())

years,months,days = [],[],[]
dates_check=set()
wind_speeds, wind_direction = [], []

# Call azure maps Weather services (Preview) to get daily forecast data for 15 days from current date
session = aiohttp.ClientSession()
j=-1
for i in range(0, len(coords), 2):
    wind_speeds.append([])
    wind_direction.append([])
    
    query = str(coords[i])+', '+str(coords[i+1])
    forecast_response = await(await session.get("https://atlas.microsoft.com/weather/forecast/daily/json?query={}&api-version=1.0&subscription-key={}&duration=15".format(query, subscription_key))).json()
    j+=1
    for day in range(len(forecast_response['forecasts'])):
            date = forecast_response['forecasts'][day]['date'][:10]
            wind_speeds[j].append(forecast_response['forecasts'][day]['day']['wind']['speed']['value'])
            wind_direction[j].append(forecast_response['forecasts'][day]['day']['windGust']['direction']['degrees'])
            
            if date not in dates_check:
                year,month,day= date.split('-')
                years.append(year)
                months.append(month)
                days.append(day)
                dates_check.add(date)
            
await session.close()
```

Das folgende Skript ruft den [Dienst zum Abrufen des Kartenbilds](/rest/api/maps/render/getmapimage) von Azure Maps auf, um die Anlagenstandorte auf der Karte zu rendern:

```python
# Render the turbine locations on the map by calling the Azure Maps Get Map Image service
session = aiohttp.ClientSession()

pins="default|la-25+60|ls12|lc003C62|co9B2F15||'Location A'{} {}|'Location B'{} {}|'Location C'{} {}|'Location D'{} {}".format(coords[1],coords[0],coords[3],coords[2],coords[5],coords[4], coords[7],coords[6])

image_response = "https://atlas.microsoft.com/map/static/png?subscription-key={}&api-version=1.0&layer=basic&style=main&zoom=6&center={},{}&pins={}".format(subscription_key,coords[7],coords[6],pins)

static_map_response = await session.get(image_response)

poi_range_map = await static_map_response.content.read()

await session.close()

display(Image(poi_range_map))
```

![Anlagenstandorte](./media/weather-service-tutorial/location-map.png)


Wir gruppieren die Vorhersagedaten basierend auf der Stationskennung mit den Demodaten. Die Stationskennung gilt für das Wetterrechenzentrum. Mit dieser Gruppierung werden die Demodaten um die Vorhersagedaten erweitert.

```python
# Group forecasted data for all locations
df = df.reset_index(drop=True)
forecast_data = pd.DataFrame(columns=['StationID','latitude','longitude','Year','Month','Day','DryBulbCelsius','WetBulbFarenheit','WetBulbCelsius','DewPointFarenheit','DewPointCelsius','RelativeHumidity','WindSpeed','WindDirection'])

for i in range(len(station_ids)):
    loc_forecast = pd.DataFrame({'StationID':station_ids[i], 'latitude':coords[0], 'longitude':coords[1], 'Year':years, 'Month':months, 'Day':days, 'WindSpeed':wind_speeds[i], 'WindDirection':wind_direction[i]})
    forecast_data = pd.concat([forecast_data,loc_forecast], axis=0, sort=False)
    
combined_weather_data = pd.concat([df,forecast_data])
grouped_weather_data = combined_weather_data.groupby(['StationID'])
```

Die folgende Tabelle enthält die kombinierten historischen und vorhergesagten Daten für einen der Anlagenstandorte:

```python
# Display data for first location
grouped_weather_data.get_group(station_ids[0]).reset_index()
```

<center>

![Gruppierte Daten](./media/weather-service-tutorial/grouped-data.png)</center>

## <a name="plot-forecast-data"></a>Zeichnen von Vorhersagedaten

Wir stellen die vorhergesagten Werte anhand der Vorhersagetage dar. Hieran können wir erkennen, wie sich die Geschwindigkeit und die Richtung des Winds in den nächsten 15 Tagen ändern wird.

```python
# Plot wind speed
curr_date = datetime.datetime.now().date()
windsPlot_df = pd.DataFrame({ 'Location A': wind_speeds[0], 'Location B': wind_speeds[1], 'Location C': wind_speeds[2], 'Location D': wind_speeds[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind speed")
```

```python
#Plot wind direction 
windsPlot_df = pd.DataFrame({ 'Location A': wind_direction[0], 'Location B': wind_direction[1], 'Location C': wind_direction[2], 'Location D': wind_direction[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind direction")
```

Mit den unten angegebenen Diagrammen werden die Vorhersagedaten visualisiert. Informationen zur Änderung der Windgeschwindigkeit finden Sie im linken Diagramm. Informationen zur Änderung der Windrichtung finden Sie im rechten Diagramm. Diese Daten enthalten die Vorhersage für die nächsten 15 Tage ab dem Anforderungstag der Daten.

<center>

![Diagramm für die Windgeschwindigkeit](./media/weather-service-tutorial/speed-date-plot.png) ![Diagramm für die Windrichtung](./media/weather-service-tutorial/direction-date-plot.png)</center>

In diesem Tutorial haben Sie gelernt, wie Sie Azure Maps-REST-APIs aufrufen, um Wettervorhersagedaten zu erhalten. Außerdem wurde gezeigt, wie Sie die Daten in Diagrammen visualisieren.

Weitere Informationen zum Aufrufen von Azure Maps-REST-APIs in Azure Notebooks finden Sie unter [Tutorial: Routenplanung für Elektrofahrzeuge mit Azure Notebooks (Python)](./tutorial-ev-routing.md).

Informationen zu den in diesem Tutorial verwendeten Azure Maps-APIs finden Sie unter:

* [Wetter: Abrufen einer täglichen Vorhersage (Vorschau)](/rest/api/maps/weather/getdailyforecastpreview)
* [Rendern: Abrufen des Kartenbilds](/rest/api/maps/render/getmapimage)

Eine vollständige Liste mit den Azure Maps-REST-APIs finden Sie unter [Azure Maps-Dokumentation](./consumption-model.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Notebooks finden Sie hier

> [!div class="nextstepaction"]
> [Azure Notebooks](../notebooks/index.yml)
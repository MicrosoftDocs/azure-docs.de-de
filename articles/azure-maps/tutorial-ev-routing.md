---
title: 'Tutorial: Routenplanung für Elektrofahrzeuge mithilfe von Azure Notebooks (Python) mit Microsoft Azure Maps'
description: Tutorial zur Routenplanung für Elektrofahrzeuge mit den Microsoft Azure Maps-Routenplanungs-APIs und Azure Notebooks
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, devx-track-python
ms.openlocfilehash: f30b99a1d9c8303d5b2ed4b02819d0ca837946d2
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905739"
---
# <a name="tutorial-route-electric-vehicles-by-using-azure-notebooks-python"></a>Tutorial: Routenplanung für Elektrofahrzeuge mit Azure Notebooks (Python)

Azure Maps ist ein Portfolio mit Geodienst-APIs, die nativ in Azure integriert sind. Mit diesen APIs können Entwickler, Unternehmen und unabhängige Softwarehersteller (Independent Software Vendors, ISVs) standortbezogene Apps sowie Lösungen für die Bereiche IoT, Mobilität, Logistik und Assetnachverfolgung entwickeln. 

Die Rest-APIs von Azure Maps können in Programmiersprachen wie Python und R aufgerufen werden, um Szenarien mit Geodatenanalyse und maschinellem Lernen zu ermöglichen. Azure Maps verfügt über stabile [APIs für die Routenplanung](/rest/api/maps/route), mit denen Benutzer Routen zwischen mehreren Datenpunkten berechnen können. Die Berechnungen basieren auf unterschiedlichen Bedingungen, z. B. Fahrzeugtyp oder erreichbarer Bereich. 

In diesem Tutorial helfen Sie einem Fahrer, der den Akku seines Elektrofahrzeugs aufladen muss. Der Fahrer muss die nächstgelegene Ladestation (relativ zum Fahrzeugstandort) finden.

In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen und Ausführen einer Jupyter Notebook-Datei in [Azure Notebooks](../notebooks/index.yml) in der Cloud
> * Aufrufen von Azure Maps-REST-APIs in Python
> * Suchen nach einem erreichbaren Bereich auf Grundlage des Verbrauchsmodells des Elektrofahrzeugs
> * Suchen nach Ladestationen für Elektrofahrzeuge im erreichbaren Bereich bzw. in der Isochrone
> * Rendern der Grenze des erreichbaren Bereichs und der Ladestationen auf einer Karte
> * Ermitteln und Visualisieren einer Route zur nächstgelegenen Ladestation basierend auf der Fahrtzeit


## <a name="prerequisites"></a>Voraussetzungen 

Für dieses Tutorial müssen Sie zuerst ein Azure Maps-Konto erstellen und Ihren Primärschlüssel (Abonnementschlüssel) abrufen. 

Gehen Sie zum Erstellen eines Azure Maps-Kontoabonnements wie unter [Erstellen eines Kontos mit Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account) beschrieben vor. Sie benötigen ein Azure Maps-Kontoabonnement mit dem Tarif „S1“. 

Gehen Sie wie unter [Abrufen des Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) beschrieben vor, um den primären Abonnementschlüssel für Ihr Konto abzurufen.

Weitere Informationen zur Authentifizierung in Azure Maps finden Sie unter [Verwalten der Authentifizierung in Azure Maps](./how-to-manage-authentication.md).

## <a name="create-an-azure-notebooks-project"></a>Erstellen eines Azure Notebooks-Projekts

Um die Schritte in diesem Tutorial ausführen zu können, müssen Sie ein Azure Notebooks-Projekt erstellen und die Jupyter Notebook-Datei herunterladen und ausführen. Die Jupyter Notebook-Datei enthält Python-Code, der das Szenario in diesem Tutorial implementiert. Gehen Sie wie folgt vor, um ein Azure Notebooks-Projekt zu erstellen und das Jupyter Notebook-Dokument in das Projekt hochzuladen:

1. Navigieren Sie zu [Azure Notebooks](https://notebooks.azure.com), und melden Sie sich an Weitere Informationen finden Sie unter [Quickstart: Anmelden und Festlegen einer Benutzer-ID](../notebooks/quickstart-sign-in-azure-notebooks.md).
1. Wählen Sie oben auf Ihrer öffentlichen Profilseite die Option **Meine Projekte** aus.

    ![Schaltfläche „Meine Projekte“](./media/tutorial-ev-routing/myproject.png)

1. Wählen Sie auf der Seite **Meine Projekte** die Option **Neues Projekt** aus.
 
   ![Schaltfläche „Neues Projekt“](./media/tutorial-ev-routing/create-project.png)

1. Geben Sie im Bereich **Neues Projekt erstellen** einen Projektnamen und eine Projekt-ID ein.
 
    ![Bereich „Neues Projekt erstellen“](./media/tutorial-ev-routing/create-project-window.png)

1. Klicken Sie auf **Erstellen**.

1. Nachdem das Projekt erstellt wurde, laden Sie [diese Jupyter Notebook-Dokumentdatei](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) aus dem [Repository für Jupyter Notebook in Azure Maps](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook) herunter.

1. Wählen Sie in der Projektliste auf der Seite **Meine Projekte** Ihr Projekt und dann die Option **Hochladen** aus, um die Jupyter Notebook-Dokumentdatei hochzuladen. 

    ![Hochladen der Jupyter Notebook-Datei](./media/tutorial-ev-routing/upload-notebook.png)

1. Laden Sie die Datei von Ihrem Computer hoch, und wählen Sie anschließend **Fertig** aus.

1. Nachdem der Upload erfolgreich abgeschlossen wurde, wird die Datei auf Ihrer Projektseite angezeigt. Doppelklicken Sie auf die Datei, um sie als Jupyter Notebook-Datei zu öffnen.

Versuchen Sie, die in der Jupyter Notebook-Datei implementierten Funktionen nachzuvollziehen. Führen Sie den Code in der Jupyter Notebook-Datei Zelle für Zelle aus. Sie können den Code in jeder Zelle ausführen, indem Sie oben in der Jupyter Notebook-App die Schaltfläche **Ausführen** auswählen.

  ![Schaltfläche „Ausführen“](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Installieren von Paketen auf Projektebene

Installieren Sie Pakete wie folgt auf der Projektebene, um den Code in Jupyter Notebook auszuführen:

1. Laden Sie die Datei [*requirements.txt*](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) aus dem [Repository für Jupyter Notebook in Azure Maps](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook) herunter, und laden Sie sie dann in Ihr Projekt hoch.
1. Klicken Sie auf dem Projektdashboard auf **Project Settings** (Projekteinstellungen). 
1. Wählen Sie im Bereich **Projekteinstellungen** die Registerkarte **Umgebung** und dann die Option **Hinzufügen** aus.
1. Gehen Sie unter **Schritte zum Einrichten der Umgebung** wie folgt vor:   
    a. Wählen Sie in der ersten Dropdownliste **Requirements.txt** aus.  
    b. Wählen Sie in der zweiten Dropdownliste Ihre Datei *requirements.txt* aus.  
    c. Wählen Sie in der dritten Dropdownliste **Python Version 3.6** als Version aus.
1. Wählen Sie **Speichern** aus.

    ![Installieren von Paketen](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-the-required-modules-and-frameworks"></a>Laden der erforderlichen Module und Frameworks

Führen Sie das folgende Skript aus, um alle erforderlichen Module und Frameworks zu laden:

```Python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-the-reachable-range-boundary"></a>Anfordern der Grenze des erreichbaren Bereichs

Ein Kurierunternehmen hat einige Elektrofahrzeuge in seiner Flotte. Im Laufe des Tages müssen die Elektrofahrzeuge aufgeladen werden, ohne dafür zum Lager zurückkehren zu müssen. Sobald die Restkapazität auf unter eine Stunde fällt, wird nach erreichbaren Ladestationen gesucht. Sie suchen also im Grunde nach einer Ladestation, wenn die Akkukapazität niedrig ist, und Sie rufen die Grenzinformationen für die in Frage kommenden Ladestationen ab. 

Da das Unternehmen die Nutzung von Routen bevorzugt, bei denen gleichermaßen auf Wirtschaftlichkeit und Geschwindigkeit geachtet wird, wird *eco* als angeforderter Routentyp (routeType) festgelegt. Mit dem folgenden Skript wird die [API zum Abrufen des Routenbereichs](/rest/api/maps/route/getrouterange) des Azure Maps-Routenplanungsdiensts aufgerufen. Dabei werden Parameter für das Verbrauchsmodell des Fahrzeugs verwendet. Im Skript wird dann die Antwort analysiert, um ein Polygonobjekt im GEOJSON-Format zu erstellen, mit dem der maximal erreichbare Bereich des Fahrzeugs dargestellt wird.

Führen Sie das Skript in der folgenden Zelle aus, um die Grenzen für den erreichbaren Bereich des Elektrofahrzeugs zu ermitteln:

```python
subscriptionKey = "Your Azure Maps key"
currentLocation = [34.028115,-118.5184279]
session = aiohttp.ClientSession()

# Parameters for the vehicle consumption model 
travelMode = "car"
vehicleEngineType = "electric"
currentChargeInkWh=45
maxChargeInkWh=80
timeBudgetInSec=550
routeType="eco"
constantSpeedConsumptionInkWhPerHundredkm="50,8.2:130,21.3"


# Get boundaries for the electric vehicle's reachable range.
routeRangeResponse = await (await session.get("https://atlas.microsoft.com/route/range/json?subscription-key={}&api-version=1.0&query={}&travelMode={}&vehicleEngineType={}&currentChargeInkWh={}&maxChargeInkWh={}&timeBudgetInSec={}&routeType={}&constantSpeedConsumptionInkWhPerHundredkm={}"
                                              .format(subscriptionKey,str(currentLocation[0])+","+str(currentLocation[1]),travelMode, vehicleEngineType, currentChargeInkWh, maxChargeInkWh, timeBudgetInSec, routeType, constantSpeedConsumptionInkWhPerHundredkm))).json()

polyBounds = routeRangeResponse["reachableRange"]["boundary"]

for i in range(len(polyBounds)):
    coordList = list(polyBounds[i].values())
    coordList[0], coordList[1] = coordList[1], coordList[0]
    polyBounds[i] = coordList

polyBounds.pop()
polyBounds.append(polyBounds[0])

boundsData = {
               "geometry": {
                 "type": "Polygon",
                 "coordinates": 
                   [
                      polyBounds
                   ]
                }
             }
```

## <a name="search-for-electric-vehicle-charging-stations-within-the-reachable-range"></a>Suchen nach Ladestationen für Elektrofahrzeuge im erreichbaren Bereich

Nachdem Sie den erreichbaren Bereich (Isochrone) für das Elektrofahrzeug ermittelt haben, können Sie innerhalb dieses Bereichs nach Ladestationen suchen. 

Mit dem folgenden Skript wird die [Post Search Inside Geometry-API](/rest/api/maps/search/postsearchinsidegeometry) von Azure Maps aufgerufen. Hiermit wird innerhalb der Grenzen des maximal erreichbaren Bereichs eines Elektrofahrzeugs nach Ladestationen gesucht. Anschließend analysiert das Skript die Antwort, um ein Array mit erreichbaren Standorten zu erhalten.

Führen Sie das folgende Skript aus, um im erreichbaren Bereich nach Ladestationen für Elektrofahrzeuge zu suchen:

```python
# Search for electric vehicle stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-the-reachable-range-and-charging-points-to-azure-maps-data-service-preview"></a>Hochladen des erreichbaren Bereichs und der Ladestationen in den Azure Maps-Datendienst (Vorschau)

Auf einer Karte empfiehlt es sich, die Ladestationen und die Grenze für den maximal erreichbaren Bereich des Elektrofahrzeugs zu visualisieren. Laden Sie hierzu die Daten zur Grenze und zu den Ladestationen als GeoJSON-Objekte in den Azure Maps-Datendienst (Vorschau) hoch. Verwenden Sie hierzu die [Datenupload-API](/rest/api/maps/data/uploadpreview). 

Führen Sie die beiden folgenden Zellen aus, um die Daten zur Grenze und zu den Ladestationen in den Azure Maps-Datendienst hochzuladen:

```python
rangeData = {
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          polyBounds
        ]
      }
    }
  ]
}

# Upload the range data to Azure Maps Data service (Preview).
uploadRangeResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = rangeData)

rangeUdidRequest = uploadRangeResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getRangeUdid = await (await session.get(rangeUdidRequest)).json()
    if 'udid' in getRangeUdid:
        break
    else:
        time.sleep(0.2)
rangeUdid = getRangeUdid["udid"]
```

```python
poiData = {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "properties": {},
        "geometry": {
            "type": "MultiPoint",
            "coordinates": reachableLocations
        }
    }
  ]
}

# Upload the electric vehicle charging station data to Azure Maps Data service (Preview).
uploadPOIsResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = poiData)

poiUdidRequest = uploadPOIsResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getPoiUdid = await (await session.get(poiUdidRequest)).json()
    if 'udid' in getPoiUdid:
        break
    else:
        time.sleep(0.2)
poiUdid = getPoiUdid["udid"]
```

## <a name="render-the-charging-stations-and-reachable-range-on-a-map"></a>Rendern der Ladestationen und des erreichbaren Bereichs auf einer Karte

Rufen Sie nach dem Hochladen der Daten in den Datendienst den [Dienst zum Abrufen des Kartenbilds](/rest/api/maps/render/getmapimage) von Azure Maps auf. Dieser Dienst wird verwendet, um die Ladestationen sowie die Grenze des maximal erreichbaren Bereichs auf dem statischen Kartenbild zu rendern. Zu diesem Zweck wird das folgende Skript ausgeführt:

```python
# Get boundaries for the bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
    lonBuffer = (maxLon-minLon)*0.1
    minLon -= lonBuffer
    maxLon += lonBuffer

    latBuffer = (maxLat-minLat)*0.1
    minLat -= latBuffer
    maxLat += latBuffer
    
    return [minLon, maxLon, minLat, maxLat]

minLon, maxLon, minLat, maxLat = getBounds(polyBounds)

path = "lcff3333|lw3|la0.80|fa0.35||udid-{}".format(rangeUdid)
pins = "custom|an15 53||udid-{}||https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/images/icons/ev_pin.png".format(poiUdid)

encodedPins = urllib.parse.quote(pins, safe='')

# Render the range and electric vehicle charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![Karte mit dem Bereich um den Standort](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station"></a>Finden der optimalen Ladestation

Als Erstes möchten Sie alle potenziellen Ladestationen im erreichbaren Bereich ermitteln. Anschließend möchten Sie wissen, welche davon am schnellsten zu erreichen ist. 

Mit dem folgenden Skript wird die [API für die Matrixroutenplanung](/rest/api/maps/route/postroutematrix) von Azure Maps aufgerufen. Diese gibt die Fahrtzeit und Entfernung zu den einzelnen Ladestationen für den angegebenen Fahrzeugstandort zurück. Mit dem Skript in der nächsten Zelle wird die Antwort analysiert, um basierend auf der Fahrtzeit die nächstgelegene erreichbare Ladestation zu erhalten.

Führen Sie das Skript in der folgenden Zelle aus, um die nächstgelegene erreichbare Ladestation zu ermitteln, die am schnellsten erreichbar ist:

```python
locationData = {
            "origins": {
              "type": "MultiPoint",
              "coordinates": [[currentLocation[1],currentLocation[0]]]
            },
            "destinations": {
              "type": "MultiPoint",
              "coordinates": reachableLocations
            }
         }

# Get the travel time and distance to each specified charging station.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-the-route-to-the-closest-charging-station"></a>Berechnen der Route zur nächstgelegenen Ladestation

Nachdem Sie nun die nächstgelegene Ladestation gefunden haben, können Sie die [API zum Abrufen von Wegbeschreibungen](/rest/api/maps/route/getroutedirections) aufrufen, um die genaue Route vom aktuellen Standort des Elektrofahrzeugs zur Ladestation anzufordern.

Führen Sie das Skript in der folgenden Zelle aus, um die Route zur Ladestation abzurufen und die Antwort zu analysieren, damit Sie ein GeoJSON-Objekt für die Route erhalten:

```python
# Get the route from the electric vehicle's current location to the closest charging station. 
routeResponse = await (await session.get("https://atlas.microsoft.com/route/directions/json?subscription-key={}&api-version=1.0&query={}:{}".format(subscriptionKey, str(currentLocation[0])+","+str(currentLocation[1]), closestChargeLoc))).json()

route = []
for loc in range(len(routeResponse["routes"][0]["legs"][0]["points"])):
                location = list(routeResponse["routes"][0]["legs"][0]["points"][loc].values())
                location[0], location[1] = location[1], location[0]
                route.append(location)

routeData = {
         "type": "LineString",
         "coordinates": route
     }
```

## <a name="visualize-the-route"></a>Visualisieren der Route

Zum Visualisieren der Route laden Sie zunächst die Routendaten als GeoJSON-Objekt in den Azure Maps-Datendienst (Vorschau) hoch. Verwenden Sie dazu die [Datenupload-API](/rest/api/maps/data/uploadpreview) von Azure Maps. Rufen Sie anschließend den Renderingdienst ([API zum Abrufen des Kartenbilds](/rest/api/maps/render/getmapimage)) auf, um die Route auf der Karte zu rendern und zu visualisieren.

Führen Sie das folgende Skript aus, um ein Bild mit der gerenderten Route auf der Karte abzurufen:

```python
# Upload the route data to Azure Maps Data service (Preview).
routeUploadRequest = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = routeData)

udidRequestURI = routeUploadRequest.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    udidRequest = await (await session.get(udidRequestURI)).json()
    if 'udid' in udidRequest:
        break
    else:
        time.sleep(0.2)

udid = udidRequest["udid"]

destination = route[-1]

destination[1], destination[0] = destination[0], destination[1]

path = "lc0f6dd9|lw6||udid-{}".format(udid)
pins = "default|codb1818||{} {}|{} {}".format(str(currentLocation[1]),str(currentLocation[0]),destination[1],destination[0])


# Get boundaries for the bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

# Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render the route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![Karte mit der Route](./media/tutorial-ev-routing/route.png)

In diesem Tutorial haben Sie erfahren, wie Sie REST-APIs von Azure Maps direkt aufrufen und Azure Maps-Daten mit Python visualisieren.

Informationen zu den in diesem Tutorial verwendeten Azure Maps-APIs finden Sie unter:

* [Abrufen des Routenbereichs](/rest/api/maps/route/getrouterange)
* [Veröffentlichen einer Suche innerhalb der Geometrie](/rest/api/maps/search/postsearchinsidegeometry)
* [Datenupload](/rest/api/maps/data/uploadpreview)
* [Rendern: Abrufen des Kartenbilds](/rest/api/maps/render/getmapimage)
* [Veröffentlichen der Routenmatrix](/rest/api/maps/route/postroutematrix)
* [Abrufen von Wegbeschreibungen](/rest/api/maps/route/getroutedirections)
* [Azure Maps-REST-APIs](./consumption-model.md)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Notebooks finden Sie hier

> [!div class="nextstepaction"]
> [Azure Notebooks](../notebooks/index.yml)
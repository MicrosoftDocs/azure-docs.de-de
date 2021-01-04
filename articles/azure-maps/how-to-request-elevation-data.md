---
title: Anfordern von Höhendaten mithilfe des Azure Maps-Höhenangabendiensts (Vorschau)
description: Erfahren Sie, wie Sie mit dem Azure Maps-Höhenangabendienst (Vorschau) Höhendaten anfordern.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 76232a917e8856a06645fabc0ab4716195c5c0e1
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97094198"
---
# <a name="request-elevation-data-using-the-azure-maps-elevation-service-preview"></a>Anfordern von Höhendaten mithilfe des Azure Maps-Höhenangabendiensts (Vorschau)

> [!IMPORTANT]
> Der Azure Maps-Höhenangabendienst befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Der Azure Maps-[Höhenangabendienst](https://docs.microsoft.com/rest/api/maps/elevation) umfasst APIs, über die Höhendaten an beliebigen Punkten auf der Erdoberfläche abgefragt werden können. Sie können Stichproben für Höhenangaben entlang von Routen, innerhalb eines definierten Begrenzungsrahmens oder an bestimmten Koordinaten anfordern. Außerdem können Sie die [Render V2-API zum Abrufen von Kartenkacheln](https://docs.microsoft.com/rest/api/maps/renderv2) verwenden, um Höhendaten im Kachelformat abzurufen. Die Kacheln werden im GeoTIFF-Rasterformat bereitgestellt. In diesem Artikel erfahren Sie, wie Sie mit dem Azure Maps-Höhenangabendienst und der API zum Abrufen von Kartenkacheln Höhendaten anfordern. Die Höhendaten können im GeoJSON- und im GeoTIFF-Format angefordert werden.

## <a name="prerequisites"></a>Voraussetzungen

1. [Erstellen eines Azure Maps-Kontos mit dem Tarif S1](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt)

Weitere Informationen zur Authentifizierung in Azure Maps finden Sie unter [Verwalten der Authentifizierung in Azure Maps](how-to-manage-authentication.md).

In diesem Artikel wird die Anwendung [Postman](https://www.postman.com/) verwendet. Sie können aber auch eine andere API-Entwicklungsumgebung verwenden.

## <a name="request-elevation-data-in-raster-tiled-format"></a>Anfordern von Höhendaten im Rasterkachelformat

Verwenden Sie die [Render V2-API zum Abrufen von Kartenkacheln](https://docs.microsoft.com/rest/api/maps/renderv2), um Höhendaten im Rasterkachelformat abzurufen. Wenn die Kachel gefunden wird, wird sie von der API im GeoTIFF-Format zurückgegeben. Andernfalls wird „0“ (null) zurückgegeben. Alle DEM-Rasterkacheln verwenden den GeoID-Modus (Meeresspiegel). In diesem Beispiel werden Höhendaten für den Mount Everest angefordert.

>[!TIP]
>Zum Abrufen einer Kachel in einem bestimmten Bereich der Weltkarte müssen Sie die richtige Kachel in der entsprechenden Zoomstufe suchen. Beachten Sie auch, dass WorldDEM die gesamte globale Landmasse, jedoch keine Ozeane abdeckt.  Weitere Informationen finden Sie unter [Zoomstufen und Grobraster](zoom-levels-and-tile-grid.md).

1. Öffnen Sie die Postman-App. Wählen Sie oben in der Postman-App **New** (Neu) aus. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **Collection** (Sammlung) aus.  Geben Sie einen Namen für die Sammlung ein, und klicken Sie dann auf **Create** (Erstellen).

2. Klicken Sie erneut auf **New** (Neu), um die Anforderung zu erstellen. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **Request** (Anforderung) aus. Geben Sie einen Anforderungsnamen (**Request name**) ein. Wählen Sie die im vorherigen Schritt erstellte Sammlung und anschließend **Save** (Speichern) aus.

3. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode **GET** aus, und geben Sie die folgende URL ein, um die Rasterkachel anzufordern. Ersetzen Sie bei dieser Anforderung sowie bei den anderen in diesem Artikel angegebenen Anforderungen jeweils `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel.

    ```http
    https://atlas.microsoft.com/map/tile?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0&tilesetId=microsoft.dem&zoom=13&x=6074&y=3432
    ```

4. Klicken Sie auf die Schaltfläche **Senden**. Es sollte die Rasterkachel mit den Höhendaten im GeoTIFF-Format ausgegeben werden. Jedes Pixel in den Rohdaten der Rasterkachel hat den Typ `float`. Der Wert jedes Pixels stellt die Höhe in Metern dar.

## <a name="request-elevation-data-in-geojson-format"></a>Anfordern von Höhendaten im GeoJSON-Format

Verwenden Sie die APIs des Höhenangabendiensts (Vorschau), um Höhendaten im GeoJSON-Format anzufordern. In diesem Abschnitt werden alle drei APIs beschrieben:

* [Abrufen von Daten für Punkte](/rest/api/maps/elevation/getdataforpoints)
* [Veröffentlichen von Daten für Punkte](/rest/api/maps/elevation/postdataforpoints)
* [Abrufen von Daten für Polylinie](https://docs.microsoft.com/rest/api/maps/elevation/getdataforpolyline)
* [Veröffentlichen von Daten für Polylinie](https://docs.microsoft.com/rest/api/maps/elevation/postdataforpolyline)
* [Abrufen von Daten für Begrenzungsrahmen](https://docs.microsoft.com/rest/api/maps/elevation/getdataforboundingbox)

>[!IMPORTANT]
> Wenn keine Daten zurückgegeben werden können, geben alle APIs `0` zurück.

### <a name="request-elevation-data-for-points"></a>Anfordern von Höhendaten für Punkte

In diesem Beispiel werden über die [API zum Abrufen von Daten für Punkte](/rest/api/maps/elevation/getdataforpoints) Höhendaten für den Mount Everest und den Chamlang angefordert. Anschließend werden über die [API zum Veröffentlichen von Daten für Punkte](/rest/api/maps/elevation/postdataforpoints) Höhendaten anhand der gleichen beiden Punkte angefordert. Es wird davon ausgegangen, dass die Längen- und Breitenangaben in der URL in WGS84-Dezimalgrad (World Geodetic System) angegeben werden.

 >[!IMPORTANT]
 >Da die Länge von URLs auf 2.048 Zeichen beschränkt ist, können nicht mehr als 100 Koordinaten als eine durch senkrechte Striche getrennte Zeichenfolge in der GET-Anforderung einer URL übergeben werden. Wenn Sie mehr als 100 Koordinaten als eine durch senkrechte Striche getrennte Zeichenfolge übergeben möchten, verwenden Sie die API zum Veröffentlichen von Daten für Punkte.

1. Klicken Sie erneut auf **New** (Neu), um die Anforderung zu erstellen. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **Request** (Anforderung) aus. Geben Sie einen Anforderungsnamen (**Request name**) ein. Wählen Sie die im vorherigen Schritt erstellte Sammlung und anschließend **Save** (Speichern) aus.

2. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode **GET** aus, und geben Sie die folgende URL ein. Ersetzen Sie bei dieser Anforderung sowie bei den anderen in diesem Artikel angegebenen Anforderungen jeweils `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel.

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&points=-73.998672,40.714728|150.644,-34.397
    ```

3. Klicken Sie auf die Schaltfläche **Senden**.  Die folgende JSON-Antwort wird zurückgegeben:

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 40.714728,
                "longitude": -73.998672
            },
            "elevationInMeter": 12.142355447638208
        },
        {
            "coordinate": {
                "latitude": -34.397,
                "longitude": 150.644
            },
            "elevationInMeter": 384.47041445517846
        }
    ]
    }
    ```

4. Nun wird die [API zum Veröffentlichen von Daten für Punkte](/rest/api/maps/elevation/postdataforpoints) aufgerufen, um die Höhendaten für die beiden gleichen Punkte abzurufen. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode **POST** aus, und geben Sie die folgende URL ein. Ersetzen Sie bei dieser Anforderung sowie bei den anderen in diesem Artikel angegebenen Anforderungen jeweils `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel.

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. Legen Sie in den **Headern** der Anforderung vom Typ **POST** die Option `Content-Type` auf `application/json` fest. Geben Sie im **Text** die folgenden Informationen zu den Koordinatenpunkten an. Klicken Sie abschließend auf **Send** (Senden).

     ```json
    [
        {
            "lon": -73.998672,
            "lat": 40.714728
        },
        {
            "lon": 150.644,
            "lat": -34.397
        }
    ]
    ```

### <a name="request-elevation-data-samples-along-a-polyline"></a>Anfordern von Stichproben für Höhenangaben entlang einer Polylinie

In diesem Beispiel werden über die [API zum Abrufen von Daten für Polylinie](https://docs.microsoft.com/rest/api/maps/elevation/getdataforpolyline) fünf gleichmäßig verteilte Stichproben für Höhenangaben entlang einer geraden Linie zwischen den Koordinaten für den Mount Everest und den Chamlang angefordert. Beide Koordinaten müssen im Längen- und Breitenformat definiert sein. Wenn Sie keinen Wert für den `samples`-Parameter angeben, wird die Anzahl der Stichproben standardmäßig auf 10 festgelegt. Die maximale Anzahl der Stichproben liegt bei 2.000.

Anschließend werden über die API zum Abrufen von Daten für Polylinie drei gleichmäßig verteilte Stichproben für Höhenangaben entlang einer Route angefordert. Sie definieren die genaue Position für die Stichproben durch Übergeben der drei Koordinatenpaare im Längen- und Breitenformat.

Schließlich werden über die [API zum Veröffentlichen von Daten für Polylinie](https://docs.microsoft.com/rest/api/maps/elevation/postdataforpolyline) Höhendaten für dieselben drei gleichmäßig verteilten Stichproben angefordert.

Es wird davon ausgegangen, dass die Längen- und Breitenangaben in der URL in WGS84-Dezimalgrad (World Geodetic System) angegeben werden.

 >[!IMPORTANT]
 >Da die Länge von URLs auf 2.048 Zeichen beschränkt ist, können nicht mehr als 100 Koordinaten als eine durch senkrechte Striche getrennte Zeichenfolge in der GET-Anforderung einer URL übergeben werden. Wenn Sie mehr als 100 Koordinaten als eine durch senkrechte Striche getrennte Zeichenfolge übergeben möchten, verwenden Sie die API zum Veröffentlichen von Daten für Punkte.

1. Wählen Sie **Neu** aus. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **Request** (Anforderung) aus. Geben Sie einen **Anforderungsnamen** ein, und wählen Sie eine Sammlung aus. Klicken Sie auf **Speichern**.

2. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode **GET** aus, und geben Sie die folgende URL ein. Ersetzen Sie bei dieser Anforderung sowie bei den anderen in diesem Artikel angegebenen Anforderungen jeweils `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel.

   ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&lines=-73.998672,40.714728|150.644,-34.397&samples=5
    ```

3. Klicken Sie auf die Schaltfläche **Senden**.  Die folgende JSON-Antwort wird zurückgegeben:

    ```JSON
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 40.714728,
                    "longitude": -73.998672
                },
                "elevationInMeter": 12.14236
            },
            {
                "coordinate": {
                    "latitude": 21.936796000000001,
                    "longitude": -17.838003999999998
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": 3.1588640000000012,
                    "longitude": 38.322664000000003
                },
                "elevationInMeter": 598.66943
            },
            {
                "coordinate": {
                    "latitude": -15.619067999999999,
                    "longitude": 94.483332000000019
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": -34.397,
                    "longitude": 150.644
                },
                "elevationInMeter": 384.47041
            }
        ]
    }
    ```

4. Nun werden drei Stichproben für Höhenangaben entlang einer Route zwischen den Koordinaten des Mount Everest, des Chamlang und des Jannu angefordert. Kopieren Sie im Abschnitt **Params** das folgende Koordinatenarray für den Wert des Abfrageschlüssels `lines`.

    ```html
        86.9797222, 27.775|86.9252778, 27.9880556 | 88.0444444, 27.6822222
    ```

5. Ändern Sie den Wert `samples` des Abfrageschlüssels in `3`.  In der folgenden Abbildung sind die neuen Werte dargestellt.

     :::image type="content" source="./media/how-to-request-elevation-data/get-elevation-samples.png" alt-text="Abrufen von drei Stichproben für Höhenangaben":::

6. Klicken Sie auf die Schaltfläche **Send** (Senden). Die folgende JSON-Antwort wird zurückgegeben:

    ```json
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 27.775,
                    "longitude": 86.9797222
                },
                "elevationInMeter": 7116.0348851572589
            },
            {
                "coordinate": {
                    "latitude": 27.737403546316028,
                    "longitude": 87.411180791156454
                },
                "elevationInMeter": 1798.6945512521534
            },
            {
                "coordinate": {
                    "latitude": 27.682222199999998,
                    "longitude": 88.0444444
                },
                "elevationInMeter": 7016.9372013588072
            }
        ]
    }
    ```

7. Nun wird die [API zum Veröffentlichen von Daten für Polylinie](https://docs.microsoft.com/rest/api/maps/elevation/postdataforpolyline) aufgerufen, um die Höhendaten für die drei gleichen Punkte abzurufen. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode **POST** aus, und geben Sie die folgende URL ein. Ersetzen Sie bei dieser Anforderung sowie bei den anderen in diesem Artikel angegebenen Anforderungen jeweils `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel.

    ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&samples=5
    ```

8. Legen Sie in den **Headern** der Anforderung vom Typ **POST** die Option `Content-Type` auf `application/json` fest. Geben Sie im **Text** die folgenden Informationen zu den Koordinatenpunkten an. Klicken Sie abschließend auf **Send** (Senden).

     ```json
    [
        {
            "lon": 86.9797222,
            "lat": 27.775
        },
        {
            "lon": 86.9252778,
            "lat": 27.9880556
        },
        {
            "lon": 88.0444444,
            "lat": 27.6822222
        }
    ]
    ```

### <a name="request-elevation-data-by-bounding-box"></a>Anfordern von Höhendaten nach Begrenzungsrahmen

Nun verwenden Sie die [API zum Abrufen von Daten für Begrenzungsrahmen](https://docs.microsoft.com/rest/api/maps/elevation/getdataforboundingbox), um Höhendaten in der Nähe von Mount Rainier im US-Bundesstaat Washington anzufordern. Die Höhendaten werden an gleichmäßig verteilten Positionen innerhalb eines Begrenzungsrahmens zurückgegeben. Der durch zwei (2) Sätze von Längen- und Breitenkoordinaten (südliche Breite, westliche Länge | nördliche Breite, östliche Länge) definierte Begrenzungsrahmen wird in Zeilen und Spalten unterteilt. Die Ränder des Begrenzungsrahmens umfassen zwei (2) der Zeilen und zwei (2) der Spalten. Für die an den Zeilen- und Spaltenschnittpunkten generierten Rasterpunkte werden Höhenangaben zurückgegeben. In einer Anforderung können bis zu 2.000 Höhenangaben zurückgegeben werden.

In diesem Beispiel wird „Rows = 3“ und „Columns = 6“ angegeben. In der Antwort werden 18 Höhenwerte zurückgegeben. Im folgenden Diagramm sind die Höhenwerte beginnend in der südwestlichen Ecke und dann von Westen nach Osten und von Süden nach Norden angeordnet.  Die Höhenpunkte sind in der Reihenfolge nummeriert, in der sie zurückgegeben werden.

:::image type="content" source="./media/how-to-request-elevation-data/bounding-box.png" border="false" alt-text="Koordinaten des Begrenzungsrahmens in den NO- und SO-Ecken":::

1. Wählen Sie **Neu** aus. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **Request** (Anforderung) aus. Geben Sie einen **Anforderungsnamen** ein, und wählen Sie eine Sammlung aus. Klicken Sie auf **Speichern**.

2. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode **GET** aus, und geben Sie die folgende URL ein. Ersetzen Sie bei dieser Anforderung sowie bei den anderen in diesem Artikel angegebenen Anforderungen jeweils `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel.

    ```http
    https://atlas.microsoft.com/elevation/lattice/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&bounds=-121.66853362143818, 46.84646479863713,-121.65853362143818, 46.85646479863713&rows=2&columns=3
    ```

3. Klicken Sie auf die Schaltfläche **Send** (Senden). In der Antwort werden 18 Stichproben für Höhenangaben zurückgegeben, jeweils eine für jeden Scheitelpunkt des Rasters.

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2298.6581875651746
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2306.3980756609963
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2279.3385479564113
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2233.1549264690366
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2196.4485923541492
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2133.1756767157253
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2345.3227848228803
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2292.2449195443587
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2270.5867788258074
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2296.8311427390604
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2266.0729430891065
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2242.216346631234
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2378.460838833359
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2327.6761137260387
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2208.3782743402949
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2106.9526472760981
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2054.3270174034078
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2030.6438331110671
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2318.753153399402
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2253.88875188271
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2136.6145845357587
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2073.6734467948486
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2042.994055784251
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 1988.3631481900356
        }
    ]
    }
    ```

## <a name="samples-use-elevation-service-preview-apis-in-azure-maps-control"></a>Beispiele: Verwenden von APIs des Höhenangabendiensts (Vorschau) im Azure Maps-Steuerelement

### <a name="get-elevation-data-by-coordinate-position"></a>Abrufen von Höhendaten nach Koordinatenposition

Auf der folgenden Beispielwebseite wird gezeigt, wie Sie mit dem Kartensteuerelement Höhendaten an einem Koordinatenpunkt anzeigen. Wenn Sie den Marker bewegen, werden die Höhendaten auf der Karte in einem Popup angezeigt.

<br/>

<iframe height="500" style="width:100%;" scrolling="no" title="Abrufen der Höhenangabe an einer Position" src="https://codepen.io/azuremaps/embed/c840b510e113ba7cb32809591d5f96a2?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Stift <a href='https://codepen.io/azuremaps/pen/c840b510e113ba7cb32809591d5f96a2'>Abrufen der Höhenangabe an einer Position</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="get-elevation-data-by-bounding-box"></a>Abrufen von Höhendaten nach Begrenzungsrahmen

Auf der folgenden Beispielwebseite wird gezeigt, wie Sie mit dem Kartensteuerelement Höhendaten in einem Begrenzungsrahmen anzeigen. Sie definieren den Begrenzungsrahmen, indem Sie auf das Symbol `square` links oben klicken und das Quadrat an die gewünschte Position auf der Karte ziehen. Mit dem Kartensteuerelement werden die Höhendaten dann in den Farben gerendert, die im Schlüssel rechts oben angegeben sind.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Höhenangaben nach Begrenzungsrahmen" src="https://codepen.io/azuremaps/embed/619c888c70089c3350a3e95d499f3e48?height=500&theme-id=default&default-tab=js,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Stift <a href='https://codepen.io/azuremaps/pen/619c888c70089c3350a3e95d499f3e48'>Höhenangaben nach Begrenzungsrahmen</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="get-elevation-data-by-polyline-path"></a>Abrufen von Höhendaten nach Polylinienroute

Auf der folgenden Beispielwebseite wird gezeigt, wie Sie mit dem Kartensteuerelement Höhendaten entlang einer Route anzeigen. Sie definieren die Route, indem Sie auf das Symbol `PolyLine` links oben klicken und die Polylinie an die gewünschte Position auf der Karte ziehen. Mit dem Kartensteuerelement werden die Höhendaten dann in den Farben gerendert, die im Schlüssel oben rechts angegeben sind.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Farbverlauf der Höhenroute" src="https://codepen.io/azuremaps/embed/7bee08e5cb13d05cb0a11636b60f14ca?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Stift <a href='https://codepen.io/azuremaps/pen/7bee08e5cb13d05cb0a11636b60f14ca'>Farbverlauf der Höhenroute</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den APIs des Azure Maps-Höhenangabendiensts (Vorschau) finden Sie in den folgenden Artikeln:

> [!div class="nextstepaction"]
> [Höhenangabendienst (Vorschau): Abrufen von Daten für Längen- und Breitenkoordinaten](/rest/api/maps/elevation/getdataforpoints)

> [!div class="nextstepaction"]
> [Höhenangabendienst (Vorschau): Abrufen von Daten für Begrenzungsrahmen](https://docs.microsoft.com/rest/api/maps/elevation/getdataforboundingbox)

> [!div class="nextstepaction"]
> [Höhenangabendienst (Vorschau): Abrufen von Daten für Polylinie](https://docs.microsoft.com/rest/api/maps/elevation/getdataforpolyline)

> [!div class="nextstepaction"]
> [Render V2: Abrufen von Kartenkacheln](https://docs.microsoft.com/rest/api/maps/renderv2)

Eine vollständige Liste mit Azure Maps-REST-APIs finden Sie unter:

> [!div class="nextstepaction"]
> [Azure Maps-REST-APIs](https://docs.microsoft.com/rest/api/maps/)

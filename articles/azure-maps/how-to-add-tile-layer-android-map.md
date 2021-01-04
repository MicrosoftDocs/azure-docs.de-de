---
title: Hinzufügen einer Kachelebene zu einer Karte mithilfe des Android SDK für Azure Maps
description: Erfahren Sie, wie Sie einer Karte eine Kachelebene hinzufügen. Sehen Sie sich ein Beispiel an, in dem das Android SDK für Microsoft Azure Maps verwendet wird, um einer Karte eine Wetterradarüberlagerung hinzuzufügen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 22618a28f1a87e68c19467aedf639e96ec2fb91e
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532675"
---
# <a name="add-a-tile-layer-to-a-map-using-the-azure-maps-android-sdk"></a>Hinzufügen einer Kachelebene zu einer Karte mithilfe des Android SDK für Azure Maps

In diesem Artikel wird veranschaulicht, wie Sie mit dem Android SDK für Azure Maps eine Kachelebene auf einer Karte rendern können. Mithilfe von Kachelebenen lassen sich Bilder über die Azure Maps-Basiskartenkacheln legen. Weitere Informationen zum Azure Maps-Kachelsystem finden Sie in der Dokumentation [Zoomfaktoren und Linienraster](zoom-levels-and-tile-grid.md).

Eine Kachelebene wird in Kacheln von einem Server geladen. Diese Bilder können wie jedes andere Bild vorab gerendert und auf einem Server gespeichert werden, wobei eine Namenskonvention verwendet wird, die von der Kachelebene verstanden wird. Sie können diese Bilder aber auch mit einem dynamischen Dienst rendern, der die Bilder nahezu in Echtzeit generiert. Es gibt drei verschiedene Namenskonventionen für Kacheldienste, die von der Azure Maps-Klasse TileLayer unterstützt werden:

* Notation von X, Y, Zoomfaktor: Basierend auf dem Zoomfaktor ist X die Spalten- und Y die Zeilenposition der Kachel im Kachelraster.
* Quadkey-Notation: Kombination der Informationen X, Y und Zoomfaktor in einem einzelnen Zeichenfolgenwert, der ein eindeutiger Bezeichner für eine Kachel ist.
* Begrenzungsrahmen: Koordinaten des Begrenzungsrahmens können verwendet werden, um ein Bild im Format `{west},{south},{east},{north}` anzugeben, welches häufig von [Web Mapping Services (WMS)](https://www.opengeospatial.org/standards/wms) verwendet wird.

> [!TIP]
> Ein TileLayer-Element (Kachelebene) ist eine gute Möglichkeit, große Datasets auf der Karte zu visualisieren. Eine Kachelebene lässt sich nicht nur aus einem Bild generieren, sondern auch Vektordaten können als Kachelebene gerendert werden. Durch das Rendern von Vektordaten als Kachelebene muss das Kartensteuerelement nur die Kacheln laden. Diese können eine viel kleinere Dateigröße aufweisen als die Vektordaten, die sie darstellen. Diese Technik wird von vielen verwendet, die Millionen von Datenzeilen auf der Karte rendern müssen.

Die in eine Kachelebene übergebene Kachel-URL muss eine HTTP/HTTPS-URL zu einer TileJSON-Ressource oder eine Kachel-URL-Vorlage sein, die die folgenden Parameter verwendet: 

* `{x}`: X-Position der Kachel. Benötigt auch `{y}` und `{z}`.
* `{y}`: Y-Position der Kachel. Benötigt auch `{x}` und `{z}`.
* `{z}`: Zoomfaktor der Kachel. Benötigt auch `{x}` und `{y}`.
* `{quadkey}`: Kachel-Quadkey-Bezeichner basierend auf der Namenskonvention des Bing Maps-Kachelsystems.
* `{bbox-epsg-3857}`: Eine Begrenzungsrahmen-Zeichenfolge mit dem Format `{west},{south},{east},{north}` im Raumbezugssystem EPSG 3857.
* `{subdomain}`: Ein Platzhalter für die Unterdomänenwerte, sofern angegeben.

## <a name="prerequisites"></a>Voraussetzungen

Um den Vorgang in diesem Artikel abzuschließen, müssen Sie das [Android SDK für Azure Maps](./how-to-use-android-map-control-library.md) installieren, um eine Karte zu laden.


## <a name="add-a-tile-layer-to-the-map"></a>Hinzufügen einer Kachelebene zur Karte

 In diesem Beispiel wird das Erstellen einer Kachelebene veranschaulicht, die auf mehrere Kacheln verweisen. Diese Kacheln verwenden das Kachelsystem „X, Y, Zoom“. Die Quelle dieser Kachelebene ist eine Wetterradarüberlagerung aus dem [Iowa Environmental Mesonet der Iowa State University in den USA](https://mesonet.agron.iastate.edu/ogc/). 

Sie können mithilfe der folgenden Schritte der Karte eine Kachelebene hinzufügen.

1. Bearbeiten Sie **res > layout > „activity_main.xml“**, sodass die Datei wie folgt aussieht:

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >
    
        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:mapcontrol_centerLat="40.75"
            app:mapcontrol_centerLng="-99.47"
            app:mapcontrol_zoom="3"
            />
    
    </FrameLayout>
    ```

2. Kopieren Sie den folgenden Codeausschnitt in die **onCreate()**-Methode Ihrer `MainActivity.java`-Klasse.

    ```Java
    mapControl.onReady(map -> {
        //Add a tile layer to the map, below the map labels.
        map.layers.add(new TileLayer(
            tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
            opacity(0.8f),
            tileSize(256)
        ), "labels");
    });
    ```
    
    Der obige Codeausschnitt ruft zuerst mit der Rückrufmethode **onReady()** eine Instanz des Azure Maps-Kartensteuerelements ab. Anschließend wird ein `TileLayer`-Objekt erstellt und eine formatierte **xyz**-Kachel-URL an die `tileUrl`-Option weitergeleitet. Die Deckkraft der Ebene ist auf `0.8` festgelegt, und da die Kacheln des Kacheldiensts 256 Pixel aufweisen, werden diese Informationen an die `tileSize`-Option übermittelt. Die Kachelebene wird dann an den Kartenebenen-Manager weitergeleitet.

    Nachdem Sie den obigen Codeausschnitt hinzugefügt haben, sollte `MainActivity.java` wie folgt aussehen:
    
    ```Java
    package com.example.myapplication;

    import android.app.Activity;
    import android.os.Bundle;
    import android.support.v7.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.layer.TileLayer;
    import java.util.Arrays;
    import java.util.List;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileSize;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileUrl;
        
    public class MainActivity extends AppCompatActivity {
    
        static{
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }
    
        MapControl mapControl;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
    
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapControl = findViewById(R.id.mapcontrol);
    
            mapControl.onCreate(savedInstanceState);
    
            mapControl.onReady(map -> {

                //Add a tile layer to the map, below the map labels.
                map.layers.add(new TileLayer(
                    tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
                    opacity(0.8f),
                    tileSize(256)
                ), "labels");
            });    
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }    
    }
    ```

Wenn Sie Ihre Anwendung jetzt ausführen, sollten Sie wie unten gezeigt eine Linie auf der Karte sehen:

<center>

![Android-Kartenlinie](./media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)</center>

## <a name="next-steps"></a>Nächste Schritte

Im folgenden Artikel erfahren Sie mehr zum Festlegen von Kartenstilen.

> [!div class="nextstepaction"]
> [Ändern von Kartenstilen auf Android-Karten](./set-android-map-styles.md)
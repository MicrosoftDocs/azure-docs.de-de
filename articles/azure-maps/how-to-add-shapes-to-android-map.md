---
title: Hinzufügen von Formen zu Android-Karten in Azure Maps| Microsoft-Dokumentation
description: Hinzufügen von Formen zu einer Karte mithilfe des Android SDK für Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: c53a3e01d471f2ca9b0878c374b00ce83848ca28
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64871239"
---
# <a name="add-a-shape-to-a-map-using-azure-maps-android-sdk"></a>Hinzufügen einer Form zu einer Karte mithilfe des Android SDK für Azure Maps

Dieser Artikel zeigt Ihnen, wie Sie mit dem Android SDK für Azure Maps Formen auf einer Karte rendern können.

## <a name="prerequisites"></a>Voraussetzungen

Um den Vorgang in diesem Artikel abzuschließen, müssen Sie das [Android SDK für Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) installieren, um eine Karte zu laden.


## <a name="add-a-line-to-the-map"></a>Hinzufügen einer Linie zur Karte

Sie können der Karte eine Linie mithilfe einer **Linienebene** hinzufügen. Führen Sie die folgenden Schritte aus, um eine Linie auf der Karte hinzuzufügen.

1. Bearbeiten Sie **res > layout > „activity_main.xml“** , sodass die Datei wie folgt aussieht:

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
            app:mapcontrol_centerLat="40.743270"
            app:mapcontrol_centerLng="-74.004420"
            app:mapcontrol_zoom="12"
            />
    
    </FrameLayout>
    ```

2. Kopieren Sie den folgenden Codeausschnitt in die **onCreate()** -Methode Ihrer `MainActivity.java`-Klasse.

    ```Java
    mapControl.onReady(map -> {

        //Create a data source and add it to the map.
        DataSource dataSource = new DataSource();
        map.sources.add(dataSource);
    
        //Create a list of points.
        List<Point> points = Arrays.asList(
            Point.fromLngLat(-73.972340, 40.743270),
            Point.fromLngLat(-74.004420, 40.756800));
    
        //Create a LineString feature and add it to the data source.
        dataSource.add(LineString.fromLngLats(points));
    
        //Create a line layer and add it to the map.
        map.layers.add(new LineLayer(dataSource,
            strokeColor("blue"),
            strokeWidth(5f)));
    });

    ```
    
    Der obige Codeausschnitt ruft zuerst mit der Rückrufmethode **onReady()** eine Instanz des Azure Maps-Kartensteuerelements ab. Dann erstellt er ein Datenquellenobjekt mithilfe der **DataSource**-Klasse und fügt es der Karte hinzu. Anschließend wird eine Liste mit **Point**-Objekten erstellt. Eine **LineString** wird anhand der Liste der Punkte erstellt und der Datenquelle hinzugefügt. Eine **Linienebene** rendert Linienobjekte, die von einer Datenquelle auf der Karte umschlossen sind. Anschließend wird eine Linienebene erstellt, der die Datenquelle hinzugefügt wird.

    Nachdem Sie den obigen Codeausschnitt hinzugefügt haben, sollte `MainActivity.java` wie folgt aussehen:
    
    ```Java
    package com.example.myapplication;
    import android.app.Activity;
    import android.os.Bundle;
    import com.mapbox.geojson.LineString;
    import com.mapbox.geojson.Point;
    import android.support.v7.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.layer.LineLayer;
    import com.microsoft.azure.maps.mapcontrol.options.LineLayerOptions;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    import java.util.Arrays;
    import java.util.List;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import static com.microsoft.azure.maps.mapcontrol.options.LineLayerOptions.strokeColor;
    import static com.microsoft.azure.maps.mapcontrol.options.LineLayerOptions.strokeWidth;
    
    
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

                //Create a data source and add it to the map.
                DataSource dataSource = new DataSource();
                map.sources.add(dataSource);
            
                //Create a list of points.
                List<Point> points = Arrays.asList(
                    Point.fromLngLat(-73.972340, 40.743270),
                    Point.fromLngLat(-74.004420, 40.756800));
            
                //Create a LineString feature and add it to the data source.
                dataSource.add(LineString.fromLngLats(points));
            
                //Create a line layer and add it to the map.
                map.layers.add(new LineLayer(dataSource,
                    strokeColor("blue"),
                    strokeWidth(5f)));
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

![Android-Kartenlinie](./media/how-to-add-shapes-to-android-map/android-map-line.png)</center>


## <a name="add-a-polygon-to-the-map"></a>Hinzufügen eines Polygons zur Karte

Mit der **Polygonebene** können Sie den Bereich des Polygons auf der Karte rendern. Gehen Sie folgendermaßen vor, um der Karte ein Polygon hinzuzufügen.

1. Bearbeiten Sie **res > layout > „activity_main.xml“** , sodass die Datei wie folgt aussieht:

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
            app:mapcontrol_centerLat="40.78"
            app:mapcontrol_centerLng="-73.97"
            app:mapcontrol_zoom="12"
            />
    
    </FrameLayout>
    ```

2. Kopieren Sie den folgenden Codeausschnitt in die **onCreate()** -Methode Ihrer `MainActivity.java`-Klasse.

    ```Java
    mapControl.onReady(map -> {
        //Create a data source and add it to the map.
        DataSource dataSource = new DataSource();
        map.sources.add(dataSource);
    
        //Create a list of point arrays to create polygon rings.
        List<List<Point>> rings = Arrays.asList(Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.7968),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)));
    
        //Create a Polygon feature and add it to the data source.
        dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));
    
        //Add a polygon layer for rendering the polygon area.
        map.layers.add(new PolygonLayer(dataSource,
            fillColor("red")));
    
        //Add a line layer for rendering the polygon outline.
        map.layers.add(new LineLayer(dataSource,
            strokeColor("blue"),
            strokeWidth(2f)));
    });
    ```

    Der obige Codeausschnitt ruft zuerst mit der Rückrufmethode **onReady()** eine Instanz des Azure Maps-Kartensteuerelements ab. Dann erstellt er ein Datenquellenobjekt mithilfe der **DataSource**-Klasse und fügt es der Karte hinzu. Ein **Polygon**-Objekt wird dann anhand einer Liste von **Point**-Objekten erstellt und der Datenquelle hinzugefügt. Eine **Polygonebene** rendert Daten, die von der Datenquelle auf der Karte umschlossen sind. Anschließend wird eine Polygonebene erstellt, um den Polygonbereich zu rendern, dem die Datenquelle hinzugefügt wird. Eine **Linienebene** rendert Linienobjekte, die von einer Datenquelle umschlossen sind. Der letzte Teil des Codeausschnitts erzeugt eine Linienebene, um den Umriss des Polygons zu rendern, dem die Datenquelle hinzugefügt wird.

    Nachdem Sie den obigen Codeausschnitt hinzugefügt haben, sollte `MainActivity.java` wie folgt aussehen:

    ```Java
    package com.example.myapplication;
    
    import android.app.Activity;
    import android.os.Bundle;
    import java.util.Arrays;
    import android.util.Log;
    import java.util.Collections;
    import android.support.v7.app.AppCompatActivity;
    import com.mapbox.geojson.Point;
    import com.mapbox.geojson.Polygon;
    import com.mapbox.geojson.Feature;
    import com.microsoft.azure.maps.mapcontrol.layer.LineLayer;
    import com.microsoft.azure.maps.mapcontrol.options.LineLayerOptions;
    import com.microsoft.azure.maps.mapcontrol.layer.PolygonLayer;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import static com.microsoft.azure.maps.mapcontrol.options.LineLayerOptions.strokeColor;
    import static com.microsoft.azure.maps.mapcontrol.options.LineLayerOptions.strokeWidth;
    import static com.microsoft.azure.maps.mapcontrol.options.PolygonLayerOptions.fillColor;
    
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
                //Create a data source and add it to the map.
                DataSource dataSource = new DataSource();
                map.sources.add(dataSource);
            
                //Create a list of point arrays to create polygon rings.
                List<List<Point>> rings = Arrays.asList(Arrays.asList(
                    Point.fromLngLat(-73.98235, 40.76799),
                    Point.fromLngLat(-73.95785, 40.80044),
                    Point.fromLngLat(-73.94928, 40.7968),
                    Point.fromLngLat(-73.97317, 40.76437),
                    Point.fromLngLat(-73.98235, 40.76799)));
            
                //Create a Polygon feature and add it to the data source.
                dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));
            
                //Add a polygon layer for rendering the polygon area.
                map.layers.add(new PolygonLayer(dataSource,
                    fillColor("red")));
            
                //Add a line layer for rendering the polygon outline.
                map.layers.add(new LineLayer(dataSource,
                    strokeColor("blue"),
                    strokeWidth(2f)));
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

Wenn Sie Ihre Anwendung jetzt ausführen, sollten Sie wie unten gezeigt ein Polygon auf der Karte sehen:

<center>

![Android-Kartenpolygon](./media/how-to-add-shapes-to-android-map/android-map-polygon.png)</center>


## <a name="next-steps"></a>Nächste Schritte

Im folgenden Artikel erfahren Sie mehr zum Festlegen von Kartenstilen.

> [!div class="nextstepaction"]
> [Ändern von Kartenstilen auf Android-Karten](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
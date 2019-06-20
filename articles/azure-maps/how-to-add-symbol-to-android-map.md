---
title: Hinzufügen einer Symbolebene zu Android-Karten in Azure Maps | Microsoft-Dokumentation
description: Hinzufügen von Symbolen zu einer Karte mithilfe des Android SDK für Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: add6e23d023753e217c102dc946837a71a64c781
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64871245"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Hinzufügen einer Symbolebene zu einer Karte mithilfe des Android SDK für Azure Maps

Dieser Artikel zeigt Ihnen, wie Sie Punktdaten aus einer Datenquelle als Symbolebene auf einer Karte mit dem Android SDK für Azure Maps rendern können.

## <a name="prerequisites"></a>Voraussetzungen

Um die Schritte in diesem Artikel vollständig abzuschließen, müssen Sie das [Android SDK für Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) installieren, um eine Karte zu laden.

## <a name="add-a-symbol-layer"></a>Hinzufügen einer Symbolebene

Um der Karte über die Symbolebene einen Marker hinzuzufügen, führen Sie die folgenden Schritte aus:

1. Bearbeiten Sie **res** > **layout** > **activity_main.xml** so, dass die Datei wie die folgende XML aussieht:
    
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
            app:mapcontrol_centerLat="47.64"
            app:mapcontrol_centerLng="-122.33"
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
    
        //Create a point feature and add it to the data source.
        dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
    
        //Add a custom image icon to the map resources.
        map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
    
        //Create a symbol layer and add it to the map.
        map.layers.add(new SymbolLayer(dataSource,
            iconImage("my-icon")));
        });
    
    ```
    
    Der obige Codeausschnitt ruft zuerst mit der Rückrufmethode **onReady()** eine Instanz des Azure Maps-Kartensteuerelements ab. Dann erstellt er ein Datenquellenobjekt mithilfe der **DataSource**-Klasse und fügt es der Karte hinzu. Er fügt der Karte anschließend ein **Feature** mit einer Punktgeometrie hinzu. Ein rotes Markerbild wird als Symbol für das Symbol festgelegt. Eine **Symbolebene** verwendet Text oder Symbole zum Rendern punktbasierter Daten, die in der Datenquelle als Symbole auf der Karte umschlossen sind. Anschließend wird eine Symbolebene erstellt. Die Datenquelle wird an diese übergeben, um sie zu rendern, und dann den Ebenen der Karte hinzugefügt.
    
    Nachdem Sie den obigen Codeausschnitt hinzugefügt haben, sollte `MainActivity.java` wie folgt aussehen:
    
    ```Java
    package com.example.myapplication;
    
    import android.app.Activity;
    import android.os.Bundle;
    import com.mapbox.geojson.Feature;
    import com.mapbox.geojson.Point;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    import static com.microsoft.azure.maps.mapcontrol.options.SymbolLayerOptions.iconImage;
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
            
                //Create a point feature and add it to the data source.
                dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
            
                //Add a custom image icon to the map resources.
                map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            
                //Create a symbol layer and add it to the map.
                map.layers.add(new SymbolLayer(dataSource,
                    iconImage("my-icon")));
            });
        }
    
        @Override
        public void onStart() {
            super.onStart();
            mapControl.onStart();
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
    
Wenn Sie Ihre Anwendung ausführen, sollten Sie an dieser Stelle einen Marker auf der Karte sehen, wie hier gezeigt:

<center>

![Android-Kartenpin](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>


## <a name="next-steps"></a>Nächste Schritte

Informationen dazu, wie Sie Ihrer Karte weitere Elemente hinzufügen, finden Sie unter:

> [!div class="nextstepaction"]
> [Hinzufügen von Formen zu einer Android-Karte](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)
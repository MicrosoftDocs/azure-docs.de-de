---
title: Hinzufügen einer Symbolebene zu einer Karte mithilfe des Android SDK für Azure Maps
description: Erfahren Sie, wie Sie einer Karte einen Marker hinzufügen. Sehen Sie sich ein Beispiel an, in dem das Microsoft Azure Maps Android SDK verwendet wird, um eine Symbolebene hinzuzufügen, die punktbasierte Daten aus einer Datenquelle enthält.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/24/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 300a7968b2072459d6d7709e4d89388e1bcf59f3
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531206"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Hinzufügen einer Symbolebene zu einer Karte mithilfe des Android SDK für Azure Maps

Dieser Artikel zeigt Ihnen, wie Sie Punktdaten aus einer Datenquelle als Symbolebene auf einer Karte mit dem Android SDK für Azure Maps rendern können.

## <a name="prerequisites"></a>Voraussetzungen

1. [Erstellen eines Azure Maps-Kontos](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt)
3. Laden Sie das [Android SDK für Azure Maps](./how-to-use-android-map-control-library.md) herunter und installieren Sie des.

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
    
        //Add a red custom image icon to the map resources.
        map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
    
        //Create a symbol layer and add it to the map.
        map.layers.add(new SymbolLayer(dataSource,
            iconImage("my-icon")));
        });
    
    ```
    
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

Wenn Sie die Anwendung ausführen, sollten Sie an dieser Stelle einen Marker auf der Karte sehen, wie hier gezeigt:

![Android-Kartenpin](./media/how-to-add-symbol-to-android-map/android-map-pin.png)

> [!TIP]
> Standardmäßig optimieren Symbolebenen das Rendering von Symbolen, indem sie überlappende Symbole ausblenden. Beim Zoomen werden die ausgeblendeten Symbole angezeigt. Legen Sie die Option `iconAllowOverlap` auf `true` fest, um dieses Feature zu deaktivieren und alle Symbole jederzeit anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

Wie Sie Ihrer Karte weitere Daten hinzufügen, erfahren Sie hier:

> [!div class="nextstepaction"]
> [Hinzufügen von Formen zu einer Android-Karte](./how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Anzeigen von Featureinformationen](display-feature-information-android.md)
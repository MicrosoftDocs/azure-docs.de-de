---
title: 'Tutorial: Migrieren einer Android-App | Microsoft Azure Maps'
description: Tutorial zum Migrieren von Android-Apps aus Google Maps zu Microsoft Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 08/19/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: b096b24acd5cf65f6ad3e9eabb1d536b3aae0168
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187067"
---
# <a name="tutorial---migrate-an-android-app-from-google-maps"></a>Tutorial: Migrieren von Android-Apps aus Google Maps

Das Android SDK für Azure Maps verfügt über eine API-Schnittstelle, die dem Web SDK ähnelt. Wenn Sie bei Ihrer Entwicklung eines dieser SDKs verwendet haben, sind viele der Konzepte, bewährten Methoden und Architekturen identisch. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Laden einer Karte
> * Lokalisieren einer Karte
> * Hinzufügen von Markern, Polylinien und Polygonen.
> * Überlagern einer Kachelebene
> * Anzeigen von Datenverkehrsdaten

Das Android SDK für Azure Maps unterstützt API 21 als Mindestversion von Android: Android 5.0.0 (Lollipop).

Alle Beispiele werden in Java bereitgestellt. Sie können aber auch Kotlin mit dem Azure Maps Android SDK verwenden.

Weitere Informationen zum Entwickeln mit dem Android SDK für Azure Maps finden Sie unter [Erste Schritte mit dem Android SDK für Azure Maps](how-to-use-android-map-control-library.md).

## <a name="prerequisites"></a>Voraussetzungen 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
2. [Erstellen eines Azure Maps-Kontos](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt) Weitere Informationen zur Authentifizierung in Azure Maps finden Sie unter [Verwalten der Authentifizierung in Azure Maps](how-to-manage-authentication.md).

## <a name="load-a-map"></a>Laden einer Karte

Die Vorgehensweise zum Laden einer Karte in einer Android-App ist bei Google Maps und Azure Maps ähnlich. Bei beiden SDKs müssen die folgenden Schritte ausgeführt werden:

* Sie benötigen einen API- oder Abonnementschlüssel, um auf eine der beiden Plattformen zugreifen zu können.
* Fügen Sie XML-Code zu einer Aktivität hinzu, um festzulegen, wo die App gerendert und angeordnet werden soll.
* Überschreiben Sie alle Lebenszyklusmethoden aus der Aktivität, die die Kartenansicht enthält, mit den entsprechenden Methoden in der map-Klasse. Insbesondere müssen die folgenden Methoden überschrieben werden:
    * `onCreate(Bundle)`
    * `onStart()`
    * `onResume()`
    * `onPause()`
    * `onStop()`
    * `onDestroy()`
    * `onSaveInstanceState(Bundle)`
    * `onLowMemory()`
* Warten Sie, bis die Karte bereit ist, bevor Sie versuchen, darauf zuzugreifen und sie zu programmieren.

### <a name="before-google-maps"></a>Vorher: Google Maps

Wenn Sie mithilfe des Google Maps SDK für Android eine Karte abrufen möchten, gehen Sie wie folgt vor:

1. Vergewissern Sie sich, dass die Google Play-Dienste installiert sind.
2. Fügen Sie der Datei **gradle.build** des Moduls eine Abhängigkeit für den Google Maps-Dienst hinzu:

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

3. Fügen Sie im Anwendungsabschnitt der Datei **google\_maps\_api.xml** einen API-Schlüssel für Google Maps hinzu:

    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

4. Fügen Sie der Hauptaktivität ein Kartenfragment hinzu:

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

5. In der Datei **MainActivity.java** muss das Google Maps SDK importiert werden. Leiten Sie alle Lebenszyklusmethoden aus der Aktivität weiter, die die Kartenansicht der zugehörigen Methoden in der map-Klasse enthält. Rufen Sie mithilfe der Methode `getMapAsync(OnMapReadyCallback)` eine Instanz vom Typ `MapView` aus dem Kartenfragment ab. Das `MapView`-Objekt initialisiert automatisch das Kartensystem und die Ansicht. Bearbeiten Sie die Datei **MainActivity.java** wie folgt:

    ```java
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
 
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        MapView mapView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapView = findViewById(R.id.myMap);
    
            mapView.onCreate(savedInstanceState);
            mapView.getMapAsync(this);
        }
    
        @Override
    
        public void onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapView.onResume();
        }
    
        @Override
        protected void onStart(){
            super.onStart();
            mapView.onStart();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapView.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapView.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapView.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapView.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapView.onSaveInstanceState(outState);
        }
    }
    ```

Wenn Sie eine Anwendung ausführen, wird das Kartensteuerelement wie in der folgenden Abbildung geladen:

![Einfache Google Maps-Karte](media/migrate-google-maps-android-app/simple-google-maps.png)

### <a name="after-azure-maps"></a>Nachher: Azure Maps

Wenn Sie mithilfe des Azure Maps SDK für Android eine Karte anzeigen lassen möchten, gehen Sie wie folgt vor:

1. Öffnen Sie die übergeordnete Datei **build.gradle**, und fügen Sie dem Blockabschnitt **all projects** den folgenden Code hinzu:

    ```JAVA
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Aktualisieren Sie **app/build.gradle**, und fügen Sie den folgenden Code hinzu:

    1. Stellen Sie sicher, dass **minSdkVersion** Ihres Projekts mindestens auf API 21 festgelegt ist.

    2. Fügen Sie den folgenden Code dem Abschnitt „Android“ hinzu:

        ```java
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```

    3. Aktualisieren Sie Ihren Abhängigkeitsblock. Fügen Sie eine neue Implementierungsabhängigkeitszeile für das aktuelle Azure Maps Android SDK hinzu:

        ```java
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > Das Azure Maps Android SDK wird regelmäßig aktualisiert und verbessert. Die aktuelle Versionsnummer von Azure Maps finden Sie unter [Erste Schritte mit dem Android-Kartensteuerelement](how-to-use-android-map-control-library.md). Außerdem können Sie die Versionsnummer von „0.2“ auf „0+“ festlegen, damit Ihr Code immer auf die neueste Version verweist.

    4. Wechseln Sie auf der Symbolleiste zu **Datei**, und klicken Sie auf **Sync Project with Gradle Files** (Projekt mit Gradle-Dateien synchronisieren).

3. Fügen Sie der Hauptaktivität („resources pwd“ \> „layout“ \> „activity\_main.xml“) ein Kartenfragment hinzu:

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
            />
    </FrameLayout>
    ```

4. In der Datei **MainActivity.java** müssen folgende Schritte ausgeführt werden:

    * Importieren des Azure Maps SDK
    * Festlegen Ihrer Azure Maps-Authentifizierungsinformationen
    * Abrufen der Kartensteuerelementinstanz in der Methode **onCreate**

     Legen Sie die Authentifizierungsinformationen in der Klasse `AzureMaps` mithilfe der Methode `setSubscriptionKey` oder `setAadProperties` fest. Durch diese globale Aktualisierung wird sichergestellt, dass die Authentifizierungsinformationen jeder Ansicht hinzugefügt werden.

    Das Kartensteuerelement enthält eigene Lebenszyklusmethoden zur Verwaltung des OpenGL-Lebenszyklus von Android. Diese Methoden müssen direkt über die enthaltene Aktivität aufgerufen werden. Zum ordnungsgemäßen Aufrufen der Lebenszyklusmethoden des Kartensteuerelements müssen in der Aktivität, die das Kartensteuerelement enthält, die folgenden Lebenszyklusmethoden überschrieben werden. Rufen Sie die entsprechende Kartensteuerelementmethode auf.

    * `onCreate(Bundle)` 
    * `onStart()` 
    * `onResume()` 
    * `onPause()` 
    * `onStop()` 
    * `onDestroy()` 
    * `onSaveInstanceState(Bundle)` 
    * `onLowMemory()`

    Bearbeiten Sie die Datei **MainActivity.java** wie folgt:

    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
     
        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);
    
            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.
    
            });
        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        protected void onStart(){
            super.onStart();
            mapControl.onStart();
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

Wenn Sie Ihre Anwendung ausführen, wird das Kartensteuerelement wie in der folgenden Abbildung geladen:


![Einfache Azure Maps-Karte](media/migrate-google-maps-android-app/simple-azure-maps.png)

Hinweis: Das Azure Maps-Steuerelement unterstützt das Zoomen und bietet eine Weltansicht.

> [!TIP]
> Wenn Sie einen Android-Emulator auf einem Windows-Computer verwenden, wird die Karte möglicherweise aufgrund von Konflikten mit OpenGL und softwarebeschleunigtem Grafikrendering nicht gerendert. Dies lässt sich unter Umständen wie folgt beheben: Öffnen Sie den AVD-Manager, und wählen Sie das zu bearbeitende virtuelle Gerät aus. Scrollen Sie im Bereich **Verify Configuration** (Konfiguration überprüfen) nach unten. Legen Sie im Abschnitt **Emulated Performance** (Emulierte Leistung) die Option **Grafiken** auf **Hardware** fest.

## <a name="localizing-the-map"></a>Lokalisieren der Karte

Lokalisierung ist ein wichtiger Punkt, wenn sich Ihre Zielgruppe über mehrere Länder/Regionen erstreckt oder verschiedene Sprachen spricht.

### <a name="before-google-maps"></a>Vorher: Google Maps

Fügen Sie der Methode `onCreate` den folgenden Code hinzu, um die Sprache der Karte festzulegen. Der Code muss vor dem Festlegen der Kontextansicht der Karte hinzugefügt werden. Durch den Sprachcode „fr“ wird die Sprache auf Französisch beschränkt.

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

Dies ist ein Beispiel für Google Maps mit der Spracheinstellung „fr“.

![Lokalisierung in Google Maps](media/migrate-google-maps-android-app/google-maps-localization.png)

### <a name="after-azure-maps"></a>Nachher: Azure Maps

Azure Maps bietet drei verschiedene Möglichkeiten, um die Sprache und die regionale Ansicht der Karte festzulegen. Die erste Option besteht darin, die Informationen zur Sprache und zur regionalen Ansicht an die Klasse `AzureMaps` zu übergeben. Bei dieser Option werden die statischen Methoden `setLanguage` und `setView` global verwendet. Das bedeutet, dass die Standardsprache und die regionale Ansicht für alle in Ihrer App geladenen Azure Maps-Steuerelemente festgelegt werden. In diesem Beispiel wird mithilfe des Sprachcodes „fr-FR“ Französisch festgelegt.

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

Die zweite Option besteht darin, die Sprach- und Ansichtsinformationen an den XML-Code des Kartensteuerelements zu übergeben.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

Die dritte Option besteht darin, die Sprache und die regionale Kartenansicht mithilfe der Kartenmethode `setStyle` zu programmieren. Bei dieser Option werden die Sprache und die regionale Ansicht bei jeder Ausführung des Codes aktualisiert.

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Dies ist ein Beispiel für Azure Maps mit der Spracheinstellung „fr-FR“.

![Lokalisierung in Azure Maps](media/migrate-google-maps-android-app/azure-maps-localization.png)

Die vollständige Liste unterstützter Sprachen finden Sie [hier](supported-languages.md).

## <a name="setting-the-map-view"></a>Festlegen der Kartenansicht

Dynamische Karten können sowohl in Azure Maps als auch in Google Maps programmgesteuert durch Aufrufen der entsprechenden Methoden an neue geografische Orte verschoben werden. Als Nächstes konfigurieren wir die Karte für die Anzeige von Satellitenluftaufnahmen, zentrieren die Karte über einem Ort mit Koordinaten und ändern die Zoomstufe. In diesem Beispiel verwenden wir den Breitengrad 35,0272, den Längengrad -111,0225 und die Zoomstufe 15.

### <a name="before-google-maps"></a>Vorher: Google Maps

Die Kamera des Kartensteuerelements von Google Maps kann mithilfe der Methode `moveCamera` programmgesteuert bewegt werden. Die Methode `moveCamera` ermöglicht die Angabe des Kartenmittelpunkts und einer Zoomstufe. Die Methode `setMapType` ändert die Art der anzuzeigenden Karte.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

![Festgelegte Google Maps-Ansicht](media/migrate-google-maps-android-app/google-maps-set-view.png)

> [!NOTE]
> Google Maps verwendet Kacheln mit der Abmessung 256 Pixel, während Azure Maps größere Kacheln mit 512 Pixel verwendet. Dadurch wird die Anzahl der Netzwerkanforderungen reduziert, die Azure Maps zum Laden des gleichen Kartenbereichs wie Google Maps benötigt. Bei Verwendung von Azure Maps muss die in Google Maps verwendete Zoomstufe um den Wert 1 verringert werden, um den gleichen Anzeigebereich zu erhalten wie bei einer Karte in Google Maps.

### <a name="after-azure-maps"></a>Nachher: Azure Maps

Wie bereits erwähnt, muss die in Google Maps verwendete Zoomstufe um den Wert 1 verringert werden, um in Azure Maps den gleichen Anzeigebereich zu erhalten. Verwenden Sie in diesem Beispiel die Zoomstufe 14.

Die ursprüngliche Kartenansicht kann in XML-Attributen auf dem Kartensteuerelement festgelegt werden.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_cameraLat="35.0272"
    app:mapcontrol_cameraLng="-111.0225"
    app:mapcontrol_zoom="14"
    app:mapcontrol_style="satellite"
    />
```

Die Kartenansicht kann mithilfe der Kartenmethoden `setCamera` und `setStyle` programmiert werden.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

![Festgelegte Azure Maps-Ansicht](media/migrate-google-maps-android-app/azure-maps-set-view.png)

**Zusätzliche Ressourcen:**

* [Unterstützte Kartenstile](supported-map-styles.md)

## <a name="adding-a-marker"></a>Hinzufügen eines Markers

Punktdaten werden häufig mithilfe eines Bilds auf der Karte gerendert. Diese Bilder werden als Marker, Stecknadeln, Pins oder Symbole bezeichnet. In den folgenden Beispielen werden Punktdaten als Marker auf der Karte gerendert. Dabei werden folgende Koordinaten verwendet: 51,5 (Breitengrad), -0,2 (Längengrad).

### <a name="before-google-maps"></a>Vorher: Google Maps

In Google Maps werden Marker mithilfe der Kartenmethode `addMarker` hinzugefügt.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

![Google Maps-Marker](media/migrate-google-maps-android-app/google-maps-marker.png)

### <a name="after-azure-maps"></a>Nachher: Azure Maps

Um Punktdaten in Azure Maps auf der Karte zu rendern, müssen die Daten zunächst einer Datenquelle hinzugefügt werden. Anschließend muss diese Datenquelle einer Symbolebene hinzugefügt werden. Die Datenquelle optimiert die Verwaltung räumlicher Daten im Kartensteuerelement. Die Symbolebene gibt an, wie Punktdaten als Bild oder Text gerendert werden sollen.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(dataSource));
});
```

![Azure Maps-Marker](media/migrate-google-maps-android-app/azure-maps-marker.png)

## <a name="adding-a-custom-marker"></a>Hinzufügen eines benutzerdefinierten Markers

Benutzerdefinierte Bilder können zur Darstellung von Punkten auf einer Karte verwendet werden. Bei der Karte in den folgenden Beispielen wird ein benutzerdefiniertes Bild verwendet, um einen Punkt auf der Karte anzuzeigen. Der Punkt befindet sich am Breitengrad 51,5 und am Längengrad -0,2. Die Position des Markers wird durch den Anker so angepasst, dass die Spitze des Stecknadelsymbols auf die korrekte Kartenposition ausgerichtet ist.

<center>

![Bild: gelbe Stecknadel](media/migrate-google-maps-web-app/yellow-pushpin.png)<br/>
yellow-pushpin.png</center>

In beiden Beispielen wird das obige Bild dem Ordner „drawable“ der App-Ressourcen hinzugefügt.

### <a name="before-google-maps"></a>Vorher: Google Maps

Bei Google Maps können benutzerdefinierte Bilder für Marker verwendet werden. Laden Sie benutzerdefinierte Bilder mithilfe der Markeroption `icon`. Mit der Option `anchor` können Sie die Spitze des Bilds auf die Koordinate ausrichten. Der Anker ist relativ zu den Dimensionen des Bilds. In diesem Fall ist der Anker 0,2 Einheiten breit und eine Einheit hoch.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.yellow-pushpin))
    .anchor(0.2f, 1f));
}
```

![Benutzerdefinierter Marker in Google Maps](media/migrate-google-maps-android-app/google-maps-custom-marker.png)

### <a name="after-azure-maps"></a>Nachher: Azure Maps

Von Symbolebenen in Azure Maps werden zwar benutzerdefinierte Bilder unterstützt, diese müssen jedoch zuerst in die Kartenressourcen geladen werden, und ihnen muss jeweils eine eindeutige ID zugewiesen werden. Anschließend muss von der Symbolebene auf diese ID verwiesen werden. Verwenden Sie die Option `iconOffset`, um das Symbol so zu versetzen, dass es auf den korrekten Punkt auf dem Bild ausgerichtet ist. Der Symbolversatz wird in Pixeln angegeben. Der Versatz wird standardmäßig relativ zur Mitte des unteren Bildrands angegeben, dies kann jedoch mithilfe der Option `iconAnchor` angepasst werden. In diesem Beispiel wird die Option `iconAnchor` auf `"center"` festgelegt. Das Bild wird mithilfe eines Symbolversatzes fünf Pixel nach rechts und 15 Pixel nach oben verschoben, um es auf die Spitze des Stecknadelbilds auszurichten.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.yellow_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(dataSource,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER
        iconOffset(new Float[]{5f, -15f})));
});
```

![Benutzerdefinierter Marker in Azure Maps](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)

## <a name="adding-a-polyline"></a>Hinzufügen einer Polylinie

Polylinien werden verwendet, um eine Linie oder einen Pfad auf der Karte darzustellen. In den folgenden Beispielen wird gezeigt, wie Sie eine gestrichelte Polylinie auf der Karte erstellen.

### <a name="before-google-maps"></a>Vorher: Google Maps

Rendern Sie mit Google Maps eine Polylinie mithilfe der Klasse `PolylineOptions`. Verwenden Sie die Methode `addPolyline`, um die Polylinie der Karte hinzuzufügen. Legen Sie die Strichfarbe mithilfe der Option `color` fest. Legen Sie die Strichstärke mithilfe der Option `width` fest. Fügen Sie mithilfe der Option `pattern` ein Stricharray hinzu.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polyline.
    PolylineOptions lineOptions = new PolylineOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .color(Color.RED)
            .width(10f)
            .pattern(Arrays.<PatternItem>asList(
                    new Dash(30f), new Gap(30f)));

    //Add the polyline to the map.
    Polyline polyline = mapView.addPolyline(lineOptions);
}
```

![Google Maps-Polylinie](media/migrate-google-maps-android-app/google-maps-polyline.png)

### <a name="after-azure-maps"></a>Nachher: Azure Maps

In Azure Maps werden Polylinien als Objekt vom Typ `LineString` oder `MultiLineString` bezeichnet. Fügen Sie diese Objekte einer Datenquelle hinzu, und rendern Sie sie mithilfe einer Linienebene. Legen Sie die Strichstärke mithilfe der Option `strokeWidth` fest. Fügen Sie mithilfe der Option `strokeDashArray` ein Stricharray hinzu.

Die Pixeleinheiten für Strichbreite und Stricharray im Azure Maps Web SDK sind mit den Einheiten im Google Maps-Dienst identisch. Beide akzeptieren die gleichen Werte und liefern die gleichen Ergebnisse.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

![Azure Maps-Polylinie](media/migrate-google-maps-android-app/azure-maps-polyline.png)

## <a name="adding-a-polygon"></a>Hinzufügen eines Polygons

Polygone werden verwendet, um einen Bereich auf der Karte darzustellen. In den nächsten Beispielen wird gezeigt, wie Sie ein Polygon erstellen. Dieses Polygon bildet ein Dreieck auf der Grundlage der Mittelpunktkoordinate der Karte.

### <a name="before-google-maps"></a>Vorher: Google Maps

Rendern Sie mit Google Maps ein Polygon mithilfe der Klasse `PolygonOptions`. Verwenden Sie die Methode `addPolygon`, um das Polygon der Karte hinzuzufügen. Legen Sie mithilfe der Optionen `fillColor` und `strokeColor` die Füll- bzw. Strichfarbe fest. Legen Sie die Strichstärke mithilfe der Option `strokeWidth` fest.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    PolygonOptions polygonOptions = new PolygonOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .add(new LatLng(46, -123))  //Close the polygon.
            .fillColor(Color.argb(128, 0, 128, 0))
            .strokeColor(Color.RED)
            .strokeWidth(5f);

    //Add the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions);
}
```

![Google Maps-Polygon](media/migrate-google-maps-android-app/google-maps-polygon.png)

### <a name="after-azure-maps"></a>Nachher: Azure Maps

Fügen Sie in Azure Maps einer Datenquelle Objekte vom Typ `Polygon` und `MultiPolygon` hinzu, und rendern Sie sie mithilfe von Ebenen auf der Karte. Rendern Sie den Bereich eines Polygons auf einer Polygonebene. Rendern Sie den Umriss eines Polygons mithilfe einer Linienebene. Legen Sie Strichfarbe und -stärke mithilfe der Optionen `strokeColor` und `strokeWidth` fest.

Die Pixeleinheiten für Strichbreite und Stricharray im Azure Maps Web SDK sind auf die entsprechenden Einheiten in Google Maps abgestimmt. Beide akzeptieren die gleichen Werte und liefern die gleichen Ergebnisse.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(dataSource,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(2f)));
});
```
![Azure Maps-Polygon](media/migrate-google-maps-android-app/azure-maps-polygon.png)

## <a name="overlay-a-tile-layer"></a>Überlagern einer Kachelebene

 Verwenden Sie Kachelebenen, um große Bilder zu überlagern, die in kleinere gekachelte Bilder aufgeteilt wurden, die sich am Kachelsystem der Karte orientieren. Dies ist eine gängige Methode, um Bilder mit mehreren Ebenen oder große Datasets zu überlagern. In Google Maps werden Kachelebenen als Bildüberlagerungen bezeichnet.

In den folgenden Beispielen wird eine Kachelebene eines Wetterradars aus dem Iowa Environmental Mesonet der Iowa State University überlagert. Die Kacheln sind 256 Pixel groß.

### <a name="before-google-maps"></a>Vorher: Google Maps

Bei Google Maps kann die Karte mit einer Kachelebene überlagert werden. Verwenden Sie die Klasse `TileOverlayOptions`. Fügen Sie die Kachelebene mithilfe der Methode `addTileLauer` zur Karte hinzu. Die Kacheln nehmen ein halbtransparentes Format an, wenn die Option `transparency` auf 0,2 oder 20 % Transparenz festgelegt wird.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the tile layer.
    TileOverlayOptions tileLayer = new TileOverlayOptions()
        .tileProvider(new UrlTileProvider(256, 256) {
            @Override
            public URL getTileUrl(int x, int y, int zoom) {

                try {
                    //Define the URL pattern for the tile images.
                    return new URL(String.format("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png", zoom, x, y));
                }catch (MalformedURLException e) {
                    throw new AssertionError(e);
                }
            }
        }).transparency(0.2f);

    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer);
}
```

![Google Maps-Kachelebene](media/migrate-google-maps-android-app/google-maps-tile-layer.png)

### <a name="after-azure-maps"></a>Nachher: Azure Maps

Eine Kachelebene kann der Karte auf ähnliche Weise hinzugefügt werden wie andere Ebenen. Eine formatierte URL, die x, y und Zoomplatzhalter bzw. `{x}`, `{y}`, `{z}` aufweist, wird dazu verwendet, die Ebene anzuweisen, an welcher Position sie auf die Kacheln zugreifen soll. Kachelebenen in Azure Maps unterstützen außerdem die Platzhalter `{quadkey}`, `{bbox-epsg-3857}` und `{subdomain}`. Die Kachelebene wird halbtransparent angezeigt, wenn der Wert 0,8 für die Deckkraft verwendet wird. Deckkraft und Transparenz ähneln sich zwar, verwenden aber umgekehrte Werte. Wenn Sie die Werte zwischen den beiden Optionen konvertieren möchten, subtrahieren Sie sie von der Zahl 1.

> [!TIP]
> In Azure Maps können Ebenen praktischerweise unter anderen Ebenen gerendert werden. Das gilt auch für Basiskartenebenen. Es ist auch häufig wünschenswert, Kachelebenen unterhalb der Kartenbezeichnungen zu rendern, damit sie leicht zu lesen sind. Die Methode `map.layers.add` nimmt einen zweiten Parameter an, bei dem es sich um die ID der Ebene handelt, unter der die neue Ebene eingefügt werden soll. Mithilfe des folgenden Codes können Sie eine Kachelebene unterhalb der Kartenbezeichnungen einfügen: `map.layers.add(myTileLayer, "labels");`

```java
mapControl.onReady(map -> {
    //Add a tile layer to the map, below the map labels.
    map.layers.add(new TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels");
});
```

![Azure Maps-Kachelebene](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)

## <a name="show-traffic"></a>Anzeigen des Verkehrs

Sowohl in Azure Maps als auch in Google Maps stehen Optionen zum Überlagern von Verkehrsdaten zur Verfügung.

### <a name="before-google-maps"></a>Vorher: Google Maps

Bei Google Maps können Sie „true“ an die Methode `setTrafficEnabled` der Karte übergeben, um die Karte mit Verkehrsflussdaten zu überlagern.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

![Verkehrsinformationen in Google Maps](media/migrate-google-maps-android-app/google-maps-traffic.png)

### <a name="after-azure-maps"></a>Nachher: Azure Maps

Azure Maps bietet verschiedene Optionen zum Anzeigen von Verkehrsinformationen. Ereignisse wie etwa Straßensperrungen und Unfälle können als Symbole auf der Karte angezeigt werden. Die Karte kann mit dem Verkehrsfluss sowie mit farbcodierten Straßen überlagert werden. Die Farben können geändert werden und auf dem geltenden Tempolimit, der normalerweise zu erwartenden Verzögerung oder der absoluten Verzögerung basieren. Vorfallsdaten in Azure Maps werden im Minutentakt aktualisiert, Daten zum Verkehrsfluss alle zwei Minuten.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

![Verkehrsinformationen in Azure Maps](media/migrate-google-maps-android-app/azure-maps-traffic.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Android SDK für Azure Maps:

> [!div class="nextstepaction"]
> [Verwenden des Android-Kartensteuerelements](how-to-use-android-map-control-library.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Symbolebene zu einer Android-Karte](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Hinzufügen von Formen zu einer Android-Karte](./how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Ändern von Kartenstilen auf Android-Karten](./set-android-map-styles.md)
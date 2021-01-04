---
title: Anzeigen von Verkehrsdaten auf einer Android-Karte | Microsoft Azure Maps
description: In diesem Artikel erfahren Sie, wie Sie mithilfe des Android SDK für Microsoft Azure Maps auf einer Karte Verkehrsdaten anzeigen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/25/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 5f7e67d159c2b7dea3ebac7fd4d0856f508cb298
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532753"
---
# <a name="show-traffic-data-on-the-map-using-azure-maps-android-sdk"></a>Anzeigen von Verkehrsdaten auf der Karte mithilfe des Android SDK für Azure Maps

Flussdaten und Störungsdaten sind die zwei Arten von Verkehrsdaten, die auf der Karte angezeigt werden können. In diesem Leitfaden wird gezeigt, wie Sie beide Arten von Verkehrsdaten anzeigen können. Störungsdaten bestehen aus punkt- und linienbasierten Daten für Dinge wie Baustellen, Straßensperrungen und Unfälle. Flussdaten zeigen Metriken zum Verkehrsfluss auf der Straße.

## <a name="prerequisites"></a>Voraussetzungen

1. [Erstellen eines Azure Maps-Kontos](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt)
3. Laden Sie das [Android SDK für Azure Maps](./how-to-use-android-map-control-library.md) herunter, und installieren Sie es.

## <a name="incidents-traffic-data"></a>Verkehrsstörungsdaten

Sie müssen die folgenden Bibliotheken importieren, um `setTraffic` und `incidents` aufzurufen:

```java
import static com.microsoft.com.azure.maps.mapcontrol.options.TrafficOptions.incidents;
```

 Der folgende Codeausschnitt verdeutlicht, wie Sie Verkehrsdaten auf der Karte anzeigen. Wir übergeben einen booleschen Wert an die `incidents`-Methode und übergeben deren Rückgabewert an die `setTraffic`-Methode. 

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map - > {
        map.setTraffic(incidents(true));
    }
}
```

## <a name="flow-traffic-data"></a>Verkehrsflussdaten

Zunächst müssen Sie die folgenden Bibliotheken importieren, um `setTraffic` und `flow` aufzurufen:

```java
import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
```

Verwenden Sie folgenden Codeausschnitt, um Verkehrsflussdaten festzulegen. Ähnlich wie bei dem Code im vorherigen Abschnitt übergeben wir den Rückgabewert der `flow`-Methode an die `setTraffic`-Methode. Es gibt vier Werte, die an `flow` übermittelt werden können, und jeder Wert würde bei `flow` die Rückgabe des entsprechenden Werts auslösen. Der Rückgabewert von `flow` wird dann als Argument an `setTraffic` übergeben. Diese vier Werte finden Sie in der unten stehenden Tabelle:

|Wert des Verkehrsflusses | BESCHREIBUNG|
| :-- | :-- |
| TrafficFlow.NONE | Zeigt keine Verkehrsdaten auf der Karte an. |
| TrafficFlow.RELATIVE | Zeigt Verkehrsdaten an, die relativ zur Geschwindigkeit bei ungehindertem Verkehrsfluss auf der Straße sind. |
| TrafficFlow.RELATIVE_DELAY | Zeigt Bereiche mit einer langsameren Geschwindigkeit als der durchschnittlichen erwarteten Verzögerung an. |
| TrafficFlow.ABSOLUTE | Zeigt die absolute Geschwindigkeit aller Fahrzeuge auf der Straße an. |

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map -> {
        map.setTraffic(flow(TrafficFlow.RELATIVE)));
    }
}
```

## <a name="show-incident-traffic-data-by-clicking-a-feature"></a>Anzeigen von Verkehrsstörungsdaten durch Klicken auf eine Funktion

Zum Abrufen der Störungen für eine bestimmte Funktion können Sie den folgenden Code verwenden. Wenn Sie auf eine Funktion klicken, überprüft die Codelogik auf Störungen und erstellt eine Meldung zu der Störung. Eine Meldung wird am oberen Rand des Bildschirms mit den Details angezeigt.

1. Zunächst müssen Sie `res > layout > activity_main.xml` so bearbeiten, dass die Datei wie die folgende aussieht. Sie können `mapcontrol_centerLat`, `mapcontrol_centerLng` und `mapcontrol_zoom` durch Ihre gewünschten Werte ersetzen. Denken Sie daran, dass der Zoomfaktor ein Wert zwischen 0 und 22 ist. Bei Zoomfaktor 0 würde die ganze Welt auf eine einzige Kachel passen.

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
           app:mapcontrol_centerLat="47.6050"
           app:mapcontrol_centerLng="-122.3344"
           app:mapcontrol_zoom="12"
           />

   </FrameLayout>
   ```

2. Fügen Sie Ihrer Datei **MainActivity.java** den folgenden Code hinzu. Das Paket wird standardmäßig eingeschlossen. Stellen Sie also sicher, dass Ihr Paket ganz oben bleibt.

   ```java
   package <yourpackagename>;
   import androidx.appcompat.app.AppCompatActivity;

   import android.os.Bundle;
   import android.widget.Toast;

   import com.microsoft.azure.maps.mapcontrol.AzureMaps;
   import com.microsoft.azure.maps.mapcontrol.MapControl;
   import com.mapbox.geojson.Feature;
   import com.microsoft.azure.maps.mapcontrol.events.OnFeatureClick;

   import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.incidents;

   public class MainActivity extends AppCompatActivity {

       static {
           AzureMaps.setSubscriptionKey("Your Azure Maps Subscription Key");
       }

       MapControl mapControl;

       @Override
       protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);

           mapControl = findViewById(R.id.mapcontrol);

           mapControl.onCreate(savedInstanceState);

           //Wait until the map resources are ready.
           mapControl.getMapAsync(map -> {

               map.setTraffic(flow(TrafficFlow.RELATIVE));
               map.setTraffic(incidents(true));

               map.events.add((OnFeatureClick) (features) -> {

                   if (features != null && features.size() > 0) {
                       Feature incident = features.get(0);
                       if (incident.properties() != null) {


                           StringBuilder sb = new StringBuilder();
                           String incidentType = incident.getStringProperty("incidentType");
                           if (incidentType != null) {
                               sb.append(incidentType);
                           }
                           if (sb.length() > 0) sb.append("\n");
                           if ("Road Closed".equals(incidentType)) {
                               sb.append(incident.getStringProperty("from"));
                           } else {
                               String description = incident.getStringProperty("description");
                               if (description != null) {
                                   for (String word : description.split(" ")) {
                                       if (word.length() > 0) {
                                           sb.append(word.substring(0, 1).toUpperCase());
                                           if (word.length() > 1) {
                                               sb.append(word.substring(1));
                                           }
                                           sb.append(" ");
                                       }
                                   }
                               }
                           }
                           String message = sb.toString();

                           if (message.length() > 0) {
                               Toast.makeText(this,message,Toast.LENGTH_LONG).show();
                           }
                       }
                   }
               });
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

3. Nachdem Sie den obigen Code in Ihre Anwendung integriert haben, können Sie auf eine Funktion klicken und die Details der Verkehrsstörungen anzeigen. Abhängig von Breitengrad, Längengrad und den Zoomfaktorwerten, die Sie in ihrer Datei **activity_main.xml** verwendet haben, werden ähnliche Ergebnisse wie in der folgenden Abbildung angezeigt:


    ![Incident-traffic-on-the-map](./media/how-to-show-traffic-android/android-traffic.png)


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die folgenden Leitfäden an, um zu erfahren, wie Sie Ihrer Karte weitere Daten hinzufügen:

> [!div class="nextstepaction"]
> [Hinzufügen einer Symbolebene](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Kachelebene](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [Hinzufügen von Formen zu einer Android-Karte](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Anzeigen von Featureinformationen](display-feature-information-android.md)
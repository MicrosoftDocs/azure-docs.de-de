---
title: Erste Schritte mit dem Android SDK für Azure Maps
description: Machen Sie sich mit dem Microsoft Azure Maps Android SDK vertraut. Erfahren Sie, wie Sie ein Projekt in Android Studio erstellen, das SDK installieren und eine interaktive Karte erstellen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 1da003bb8d285dbedde87cbc6cd4708fda2dc38b
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531258"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Erste Schritte mit dem Android SDK für Azure Maps

Das Android SDK in Azure Maps ist eine Vektorenzuordnungsbibliothek für Android. Dieser Artikel führt Sie durch die Installation des Android SDK für Azure Maps und das Laden einer Karte.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="create-an-azure-maps-account"></a>Erstellen eines Azure Maps-Kontos

1. [Erstellen eines Azure Maps-Kontos](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt)
Weitere Informationen zur Authentifizierung in Azure Maps finden Sie unter [Verwalten der Authentifizierung in Azure Maps](./how-to-manage-authentication.md).
3. [Herunterladen und Installieren von Android Studio von Google](https://developer.android.com/studio/)

## <a name="create-a-project-in-android-studio"></a>Erstellen eines Projekts in Android Studio

Gehen Sie wie folgt vor, um ein Android Studio-Projekt zu erstellen:

1. Starten Sie Android Studio.
2. Klicken Sie auf **+ Neues Projekt erstellen**.
3. Klicken Sie auf der Registerkarte **Telefon und Tablet** auf **Empty Activity** (Leere Aktivität). Klicken Sie auf **Weiter**.
4. Wählen Sie unter **Ihr Projekt konfigurieren** die Option `API 21: Android 5.0.0 (Lollipop)` als das minimale SDK aus.
5. Wählen Sie `Java` als Sprache aus.
6. Übernehmen Sie den standardmäßigen `Name` für das Projekt. Klicken Sie auf **Fertig stellen**.

Weitere Informationen zur Installation von Android Studio und zur Erstellung eines neuen Projekts finden Sie in der [Dokumentation zu Android Studio](https://developer.android.com/studio/intro/).

![Erstellen eines Projekts in Android Studio ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-device"></a>Gerät einrichten

Wenn Sie Ihre Anwendung während der Entwicklung testen möchten, können Sie entweder ein Android-Telefon oder einen Android-Emulator verwenden.

Weitere Informationen zum Einrichten eines virtuellen Android-Geräts (Android Virtual Device, AVD) finden Sie in der [Dokumentation zu Android Studio](https://developer.android.com/studio/run/managing-avds).

## <a name="install-the-azure-maps-android-sdk"></a>Installieren des Android SDK für Azure Maps

Als nächstes müssen Sie zur Erstellung Ihrer Anwendung das Android SDK für Azure Maps installieren.

Führen Sie die folgenden Schritte aus, um das SDK zu installieren:

1. Erweitern Sie auf der Registerkarte „Projekt“ die Option **Gradle Scripts** (Gradle-Skripts). Öffnen Sie **build.gradle (Project: My_Application)** , und fügen Sie den folgenden Code zum Abschnitt **Alle Projekte** (`repositories`) hinzu:

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Öffnen Sie **build.gradle (Module: My_Application)** .

3. Stellen Sie sicher, dass **minSdkVersion** im Abschnitt `defaultConfig` mindestens auf API 21 festgelegt ist.

4. Fügen Sie den folgenden Code dem Abschnitt „Android“ hinzu:

    ```
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    ```

5. Fügen Sie den folgenden Code dem Abschnitt `dependencies` hinzu:

    ```
    implementation "com.microsoft.azure.maps:mapcontrol:0.6"
    ```

6. Klicken Sie in der Hauptsymbolleiste auf **Datei**, und wählen Sie dann **Sync Project with Gradle Files** (Projekt mit Gradle-Dateien synchronisieren) aus.

7. Öffnen Sie `res > layout > activity_main.xml`. Klicken Sie in der oberen rechten Ecke auf die Ansicht `Code`. Fügen Sie im `<androidx.constraintlayout.widget.ConstraintLayout>`-Element den folgenden XML-Code hinzu.
    
    ```XML
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

8. In der Datei `java > com.example.myapplication > MainActivity.java` müssen Sie folgende Schritte ausführen:

    * Hinzufügen von Importen für das Azure Maps SDK
    * Festlegen Ihrer Azure Maps-Authentifizierungsinformationen
    * Abrufen der Kartensteuerelementinstanz in der Methode **onCreate**

    Um zu vermeiden, dass für jede Anwendungsansicht Authentifizierungsinformationen hinzugefügt werden müssen, legen wir die Authentifizierungsinformationen global fest, indem wir `AzureMaps.setSubscriptionKey` aufrufen. Sie können auch `AzureMaps.setAadProperties` aufrufen, wenn Sie sich mit Azure Active Directory authentifizieren möchten.

    Die folgenden Lebenszyklusmethoden der MainActivity-Klasse werden vom Kartensteuerelement außer Kraft gesetzt. Diese Methoden sind für die Verwaltung des OpenGL-Lebenszyklus von Android zuständig.

    * onCreate(Bundle)
    * onStart()
    * onResume()
    * onPause()
    * onStop()
    * onDestroy()
    * onSaveInstanceState(Bundle)
    * onLowMemory()

    Bearbeiten Sie die Datei `MainActivity.java` wie folgt:

    ```java
    package com.example.myapplication;

    //For older versions use: import android.support.v7.app.AppCompatActivity; 
    import androidx.appcompat.app.AppCompatActivity;
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

>[!WARNING]
>Die erforderlichen Klassen wurden von Android Studio möglicherweise nicht importiert.  Folglich verfügt der Code über einige nicht auflösbare Verweise. Um die erforderlichen Klassen zu importieren, zeigen Sie einfach auf die einzelnen nicht aufgelösten Verweise und drücken `Alt + Enter` (WAHLTASTE+EINGABE auf einem Mac).

Es dauert ein paar Sekunden, bis Android Studio die Anwendung erstellt hat. Nach Abschluss des Builds können Sie Ihre Anwendung auf dem emulierten Android-Gerät testen. Es sollte eine Zuordnung wie diese angezeigt werden:

:::image type="content" source="./media/how-to-use-android-map-control-library/android-map.png" border="true" alt-text="Azure Maps in Android-Anwendung":::

## <a name="localizing-the-map"></a>Lokalisieren der Karte

Das Android SDK für Azure Maps bietet drei verschiedene Möglichkeiten zum Festlegen der Sprache und der regionalen Einstellungen für die Karte.

1. Legen Sie die Sprache und regionalen Einstellungen fest, indem Sie statische Methoden für die AzureMaps-Klasse aufrufen.

    ```Java
    static {
        //Set your Azure Maps Key.
        AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");

        //Set the regional view.
        AzureMaps.setLanguage("Auto");
    
    }
    ```

2. Definieren Sie die Sprach- und regionalen Einstellungen im XML-Code des Kartensteuerelements.

    ```XML
    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_language="fr-FR"
        app:mapcontrol_view="Auto"
        />
    ```

3. Legen Sie die Sprache und regionalen Einstellungen fest, indem Sie Methoden für das Kartensteuerelement aufrufen. Diese Option ermöglicht es Ihnen, die Einstellungen während der Laufzeit zu ändern.

    ```Java
    mapControl.onReady(map -> {
        map.setStyle(StyleOptions.language("fr-FR"));
        map.setStyle(StyleOptions.view("Auto"));
    
    });
    ```

Hier ein Beispiel für Azure Maps mit der Spracheinstellung `fr-FR`.

:::image type="content" source="./media/how-to-use-android-map-control-library/android-localization.png" border="true" alt-text="Azure Maps, Kartenbild mit Beschriftungen in Französisch":::

Eine vollständige Liste der unterstützten Sprachen und regionalen Ansichten ist [hier](supported-languages.md) dokumentiert.

## <a name="navigating-the-map"></a>Navigieren auf einer Karte

Es gibt verschiedene Möglichkeiten für das Zoomen, Schwenken, Drehen und Neigen der Karte. Im Folgenden werden die verschiedenen Möglichkeiten zum Navigieren auf der Karte erläutert.

**Zoomen der Karte**

- Berühren Sie die Karte mit zwei Fingern, und führen Sie diese zusammen, um zu verkleinern, oder führen Sie die Finger auseinander, um zu vergrößern.
- Doppeltippen Sie auf die Karte, um um eine Ebene zu vergrößern.
- Doppeltippen Sie mit zwei Fingern, um die Karte um eine Ebene zu verkleinern.
- Tippen Sie zweimal, halten Sie beim zweiten Tippen den Finger auf der Karte, und ziehen Sie nach oben, um zu vergrößern, oder nach unten, um zu verkleinern.

**Schwenken der Karte**

- Berühren Sie die Karte, und ziehen Sie sie in eine beliebige Richtung.

**Drehen der Karte**

- Berühren Sie die Karte mit zwei Fingern, und drehen Sie sie.

**Neigen der Karte**

- Berühren Sie die Karte mit zwei Fingern, und ziehen Sie diese zusammen nach oben oder unten.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie der Karte Overlaydaten hinzugefügt werden:

> [!div class="nextstepaction"]
> [Hinzufügen einer Symbolebene zu einer Android-Karte](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Hinzufügen von Formen zu einer Android-Karte](./how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Ändern von Kartenstilen auf Android-Karten](./set-android-map-styles.md)
---
title: Festlegen eines Kartenstils mithilfe des Android SDK von Azure Maps
description: Erlernen Sie zwei Möglichkeiten, den Stil einer Karte festzulegen. Hier erfahren Sie, wie Sie mithilfe des Android SDK für Microsoft Azure Maps den Stil in der Layoutdatei oder in der Aktivitätsklasse anpassen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8c7689fb87575ac6e150f793b43f35e8bf6adc83
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532476"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Festlegen des Kartenstils mithilfe des Android SDK von Azure Maps

In diesem Artikel erfahren Sie, wie Sie Kartenstile mit dem Android SDK von Azure Maps festlegen. In Azure Maps stehen sechs Kartenstile zur Auswahl. Weitere Informationen zu unterstützten Kartenstilen finden Sie unter [Unterstützte Kartenstile in Azure Maps](./supported-map-styles.md).

## <a name="prerequisites"></a>Voraussetzungen

1. [Erstellen eines Azure Maps-Kontos](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt)
3. Laden Sie das [Android SDK für Azure Maps](./how-to-use-android-map-control-library.md) herunter, und installieren Sie es.


## <a name="set-map-style-in-the-layout"></a>Festlegen des Kartenstils im Layout

In der Layoutdatei für Ihre „Activity“-Klasse können Sie einen Kartenstil festlegen. Bearbeiten Sie `res > layout > activity_main.xml` so, dass die Datei wie folgt aussieht:

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
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
        app:mapcontrol_style="grayscale_dark"
        />

</FrameLayout>
```

Das obige `mapcontrol_style`-Attribut legt den Kartenstil auf **grayscale_dark** fest.

:::image type="content" source="./media/set-android-map-styles/grayscale-dark.png" border="true" alt-text="Azure Maps: Kartenbild mit Stil „grayscale_dark“":::

## <a name="set-map-style-in-the-mainactivity-class"></a>Festlegen des Kartenstils in der Klasse „MainActivity“

Der Kartenstil kann auch in der Klasse „MainActivity“ festgelegt werden. Öffnen Sie die Datei `java > com.example.myapplication > MainActivity.java`, und kopieren Sie den folgenden Codeausschnitt in die Methode **onCreate()** . Dieser Code legt den Kartenstil auf **satellite_road_labels** fest.

>[!WARNING]
>Die erforderlichen Klassen wurden von Android Studio möglicherweise nicht importiert.  Folglich verfügt der Code über einige nicht auflösbare Verweise. Um die erforderlichen Klassen zu importieren, zeigen Sie einfach auf die einzelnen nicht aufgelösten Verweise, und drücken Sie jeweils `Alt + Enter` (WAHL+EINGABE auf einem Mac).

```Java
mapControl.onReady(map -> {

    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle((style(SATELLITE_ROAD_LABELS)));
       
});
```

:::image type="content" source="./media/set-android-map-styles/satellite-road-labels.png" border="true" alt-text="Azure Maps: Kartenbild mit Stil „satellite_road_labels“":::
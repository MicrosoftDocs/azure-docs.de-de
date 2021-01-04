---
title: 'Tutorial: Neue Android-App erstellen'
description: In diesem Tutorial wird beschrieben, wie Sie mit Azure Spatial Anchors eine neue Android-App erstellen.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: af0d01a20728d2332d4a8d71819f73baf68a65a4
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95998384"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>Tutorial: Schritt-für-Schritt-Anleitung zum Erstellen einer neuen Android-App mit Azure Spatial Anchors

In diesem Tutorial wird veranschaulicht, wie Sie eine neue Android-App erstellen, bei der ARCore-Funktionalität mit Azure Spatial Anchors integriert wird.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie dieses Tutorial ausführen können, benötigen Sie folgende Komponenten:

- Einen Windows- oder macOS-Computer mit <a href="https://developer.android.com/studio/" target="_blank">mindestens Android Studio 3.4</a>.
- Ein <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">für Entwickler geeignetes</a> und <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore-fähiges</a> Android-Gerät.

## <a name="getting-started"></a>Erste Schritte

Starten Sie Android Studio. Klicken Sie im Fenster **Welcome to Android Studio** (Willkommen bei Android Studio) auf **Start a new Android Studio project** (Neues Android Studio-Projekt starten). Oder wählen Sie **File**->**New Project** (Datei > Neues Projekt), falls Sie bereits ein Projekt geöffnet haben.

Wählen Sie im Fenster **Create New Project** (Neues Projekt erstellen) im Abschnitt **Phone and Tablet** (Telefon und Tablet) die Option **Empty Activity** (Leere Aktivität), und klicken Sie dann auf **Next** (Weiter). Wählen Sie anschließend unter **Minimum API level** (API-Mindestebene) die Option `API 26: Android 8.0 (Oreo)`, und stellen Sie sicher, dass **Language** (Sprache) auf `Java` festgelegt ist. Es kann ratsam sein, den Projektnamen und -speicherort sowie den Paketnamen zu ändern. Lassen Sie die anderen Optionen unverändert. Klicken Sie auf **Fertig stellen**. Das Installationsprogramm für Komponenten (**Component Installer**) wird ausgeführt. Klicken Sie nach Abschluss des Vorgangs auf **Fertig stellen**. Nach einigen Verarbeitungsschritten wird die IDE von Android Studio geöffnet.

## <a name="trying-it-out"></a>Ausprobieren

Schließen Sie Ihr Entwicklergerät per USB-Kabel an Ihren Entwicklungscomputer an, um Ihre neue App zu testen. Klicken Sie auf **Run**->**Run 'app'** (Ausführen > „App“ ausführen). Wählen Sie Ihr Gerät im Fenster **Select Deployment Target** (Bereitstellungsziel auswählen) aus, und klicken Sie auf **OK**. Android Studio installiert die App auf Ihrem verbundenen Gerät und startet sie. In der App auf Ihrem Gerät sollte jetzt „Hello World!“ angezeigt werden. Klicken Sie auf **Run**->**Stop 'app'** (Ausführen > „App“ beenden).

## <a name="integrating-_arcore_"></a>Integrieren von _ARCore_

<a href="https://developers.google.com/ar/discover/" target="_blank">_ARCore_</a> ist eine Google-Plattform für die Entwicklung von Augmented Reality-Umgebungen. Hiermit kann Ihr Gerät seine Position nachverfolgen, während es sich bewegt und ein eigenes Verständnis der realen Welt entwickelt.

Ändern Sie `app\manifests\AndroidManifest.xml` so, dass die folgenden Einträge im Stammknoten `<manifest>` enthalten sind. Mit diesem Codeausschnitt werden einige Schritte ausgeführt:

- Es wird ermöglicht, dass Ihre App auf die Gerätekamera zugreifen kann.
- Darüber hinaus wird sichergestellt, dass Ihre App im Google Play Store nur für Geräte sichtbar ist, die ARCore unterstützen.
- Der Google Play Store wird für das Herunterladen und Installieren von ARCore konfiguriert, falls die Installation nicht bereits bei der Installation Ihrer App durchgeführt wurde.

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera.ar" />

<application>
    ...
    <meta-data android:name="com.google.ar.core" android:value="required" />
    ...
</application>
```

Ergänzen Sie `Gradle Scripts\build.gradle (Module: app)` um den folgenden Eintrag. Mit diesem Code wird sichergestellt, dass Ihre App auf ARCore Version 1.8 ausgerichtet ist. Nach dieser Änderung erhalten Sie unter Umständen eine Benachrichtigung von Gradle, in der Sie zum Synchronisieren aufgefordert werden: Klicken Sie auf **Sync now** (Jetzt synchronisieren).

```
dependencies {
    ...
    implementation 'com.google.ar:core:1.11.0'
    ...
}
```

## <a name="integrating-_sceneform_"></a>Integrieren von _Sceneform_

[_Sceneform_](https://developers.google.com/sceneform/develop/) erleichtert das Rendern von realistischen 3D-Szenen in Augmented Reality-Apps, ohne dass OpenGL erlernt werden muss.

Ergänzen Sie `Gradle Scripts\build.gradle (Module: app)` um die folgenden Einträge. Dieser Code ermöglicht für Ihre App die Verwendung von Sprachkonstrukten aus Java 8, die für `Sceneform` benötigt werden. Außerdem wird sichergestellt, dass Ihre App auf `Sceneform` Version 1.8 ausgerichtet ist, da sie mit der Version von ARCore übereinstimmen sollte, die für Ihre App verwendet wird. Nach dieser Änderung erhalten Sie unter Umständen eine Benachrichtigung von Gradle, in der Sie zum Synchronisieren aufgefordert werden: Klicken Sie auf **Sync now** (Jetzt synchronisieren).

```
android {
    ...

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.google.ar.sceneform.ux:sceneform-ux:1.11.0'
    ...
}
```

Öffnen Sie die Datei `app\res\layout\activity_main.xml`, und ersetzen Sie das vorhandene Hello World-Element `<TextView>` durch das folgende ArFragment-Element. Mit diesem Code wird der Kamerafeed auf Ihrem Bildschirm angezeigt, sodass ARCore die Position Ihres Geräts nachverfolgen kann, während es sich bewegt.

```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

Führen Sie für Ihre App eine [erneute Bereitstellung](#trying-it-out) auf Ihrem Gerät durch, um noch einmal eine Überprüfung zu durchlaufen. Bei diesem Durchlauf sollten Sie eine Abfrage in Bezug auf Kameraberechtigungen erhalten. Nach der Genehmigung sollten Sie sehen können, dass Ihr Kamerafeed auf Ihrem Bildschirm gerendert wird.

## <a name="place-an-object-in-the-real-world"></a>Platzieren eines Objekts in der realen Welt

Wir erstellen über Ihre App jetzt ein Objekt und platzieren es. Fügen Sie zunächst die folgenden Importe für `app\java\<PackageName>\MainActivity` hinzu:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=23-33)]

Fügen Sie anschließend die folgenden Membervariablen in Ihrer `MainActivity`-Klasse hinzu:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-57)]

Fügen Sie als Nächstes den folgenden Code in die Methode `app\java\<PackageName>\MainActivity` `onCreate()` ein. Mit diesem Code wird der Listener `handleTap()` eingebunden, der erkennt, wenn der Benutzer auf den Bildschirm Ihres Geräts tippt. Wenn das Tippen auf einer Oberfläche der realen Welt erfolgt, die von der Nachverfolgungsfunktion von ARCore bereits erkannt wurde, wird der Listener ausgeführt.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-74,85&highlight=6-7)]

Fügen Sie abschließend die folgende `handleTap()`-Methode hinzu, mit der alle Komponenten zusammengefügt werden. Es wird eine Kugel erstellt und an der Position platziert, auf die getippt wurde. Die Kugel ist anfänglich schwarz, da `this.recommendedSessionProgress` derzeit auf Null festgelegt ist. Dieser Wert wird später angepasst.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=151-159,171-172,175-183,199-200)]

Führen Sie für Ihre App eine [erneute Bereitstellung](#trying-it-out) auf Ihrem Gerät durch, um noch einmal eine Überprüfung zu durchlaufen. Sie können Ihr Gerät nun bewegen, damit ARCore mit der Erkennung Ihrer Umgebung beginnen kann. Tippen Sie dann auf den Bildschirm, um die schwarze Kugel zu erstellen und auf der Oberfläche Ihrer Wahl zu platzieren.

## <a name="attach-a-local-azure-spatial-anchor"></a>Anfügen eines lokalen Azure-Raumankers

Ergänzen Sie `Gradle Scripts\build.gradle (Module: app)` um den folgenden Eintrag. Mit diesem Code wird sichergestellt, dass Ihre App auf die Version 2.2.0 von Azure Spatial Anchors ausgerichtet ist. Es sollte aber auch funktionieren, wenn Sie auf eine der neueren Versionen von Azure Spatial Anchors verweisen. Die Versionshinweise finden Sie [hier](https://github.com/Azure/azure-spatial-anchors-samples/releases).

```
dependencies {
    ...
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_jni:[2.2.0]"
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_java:[2.2.0]"
    ...
}
```

Klicken Sie mit der rechten Maustaste auf `app\java\<PackageName>`->**New**->**Java Class** (Neu > Java-Klasse). Legen Sie **Name** auf _MyFirstApp_ und **Superclass** (Übergeordnete Klasse) auf _android.app.Application_ fest. Lassen Sie die anderen Optionen unverändert. Klicken Sie auf **OK**. Eine Datei mit dem Namen `MyFirstApp.java` wird erstellt. Fügen Sie ihr den folgenden Import hinzu:

```java
import com.microsoft.CloudServices;
```

Fügen Sie anschließend den folgenden Code in der neuen `MyFirstApp`-Klasse hinzu, um sicherzustellen, dass Azure Spatial Anchors mit dem Kontext Ihrer Anwendung initialisiert wird.

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

Ändern Sie `app\manifests\AndroidManifest.xml` nun so, dass der folgende Eintrag im Stammknoten `<application>` enthalten ist. Mit diesem Code wird die Anwendungsklasse eingebunden, die Sie in Ihrer App erstellt haben.

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

Wechseln Sie zurück zu `app\java\<PackageName>\MainActivity`, und fügen Sie die folgenden Importe ein:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=33-40&highlight=2-8)]

Fügen Sie anschließend die folgenden Membervariablen in Ihrer `MainActivity`-Klasse hinzu:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=57-60&highlight=3-4)]

Fügen Sie als Nächstes Ihrer `mainActivity`-Klasse die folgende `initializeSession()`-Methode hinzu. Nach dem Aufrufen wird sichergestellt, dass eine Azure Spatial Anchors-Sitzung erstellt und beim Starten Ihrer App richtig initialisiert wird.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-97,147)]

Nun binden wir Ihre `initializeSession()`-Methode in Ihre `onCreate()`-Methode ein. Außerdem stellen wir sicher, dass Frames Ihres Kamerafeeds zur Verarbeitung an das Azure Spatial Anchors SDK gesendet werden.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-85&highlight=9-17)]

Fügen Sie abschließend Ihrer `handleTap()`-Methode den folgenden Code hinzu. An die schwarze Kugel, die wir in der realen Welt platzieren, wird ein lokaler Azure-Raumanker angefügt.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=151-159,171-183,199-200&highlight=12-13)]

Führen Sie für Ihre App noch einmal die [erneute Bereitstellung](#trying-it-out) durch. Bewegen Sie Ihr Gerät, tippen Sie auf den Bildschirm, und platzieren Sie eine schwarze Kugel. Dieses Mal wird mit Ihrem Code aber ein lokaler Azure-Raumanker erstellt und an Ihre Kugel angefügt.

Vor dem Fortfahren müssen Sie ein Azure Spatial Anchors-Konto erstellen, um den Kontobezeichner, den Schlüssel und die Domäne zu erhalten, falls Sie noch nicht darüber verfügen. Befolgen Sie die Anleitung im folgenden Abschnitt, um diese Angaben zu erhalten.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Hochladen Ihres lokalen Ankers in die Cloud

Wenn Ihnen der Azure Spatial Anchors-Kontobezeichner, der Schlüssel und die Domäne vorliegen, können Sie zurück zu `app\java\<PackageName>\MainActivity` wechseln und die folgenden Importe hinzufügen:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=40-45&highlight=3-6)]

Fügen Sie anschließend die folgenden Membervariablen in Ihrer `MainActivity`-Klasse hinzu:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=60-65&highlight=3-6)]

Fügen Sie als Nächstes der `initializeSession()`-Methode den folgenden Code hinzu. Erstens ermöglicht dieser Code Ihrer App die Überwachung des Fortschritts, den das Azure Spatial Anchors SDK beim Erfassen der Frames aus Ihrem Kamerafeed macht. Während dieses Vorgangs beginnt sich die Farbe Ihrer Kugel von Schwarz in Grau zu ändern. Die Farbe ändert sich schließlich in Weiß, wenn genügend Frames für das Übermitteln Ihres Ankers in die Cloud erfasst wurden. Zweitens werden mit diesem Code die Anmeldeinformationen bereitgestellt, die für die Kommunikation mit dem Cloud-Back-End benötigt werden. Hier konfigurieren Sie Ihre App für die Verwendung Ihres Kontobezeichners, des Schlüssels und der Domäne. Diese haben Sie beim [Einrichten der Spatial Anchors-Ressource](#create-a-spatial-anchors-resource) in einen Text-Editor kopiert.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-120,142-148&highlight=11-37)]

Fügen Sie als Nächstes Ihrer `mainActivity`-Klasse die folgende `uploadCloudAnchorAsync()`-Methode hinzu. Nach dem Aufrufen wartet diese Methode asynchron, bis von Ihrem Gerät genügend Frames erfasst wurden. Sobald dieser Zustand erreicht ist, wird die Farbe Ihrer Kugel in Gelb geändert, und dann wird mit dem Hochladen Ihres lokalen Azure-Raumankers in die Cloud begonnen. Nach Abschluss des Uploadvorgangs gibt der Code einen Ankerbezeichner zurück.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

Im letzten Schritt werden alle Komponenten zusammengefügt. Fügen Sie in Ihrer `handleTap()`-Methode den folgenden Code hinzu. Hiermit wird Ihre `uploadCloudAnchorAsync()`-Methode aufgerufen, nachdem Ihre Kugel erstellt wurde. Nach der Methodenrückgabe wird die Kugel mit dem unten angegebenen Code noch ein letztes Mal aktualisiert, indem ihre Farbe in Blau geändert wird.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=151-159,171-200&highlight=24-37)]

Führen Sie für Ihre App noch einmal die [erneute Bereitstellung](#trying-it-out) durch. Bewegen Sie Ihr Gerät, tippen Sie auf den Bildschirm, und platzieren Sie Ihre Kugel. Dieses Mal wird die Farbe der Kugel aber von Schwarz in Weiß geändert, wenn Kameraframes erfasst werden. Nachdem genügend Frames vorhanden sind, ändert sich die Farbe der Kugel in Gelb, und der Upload in die Cloud beginnt. Nach Abschluss des Uploadvorgangs ändert sich die Farbe der Kugel in Blau. Optional können Sie auch das Fenster `Logcat` in Android Studio verwenden, um die Protokollnachrichten zu überwachen, die von Ihrer App gesendet werden. Dies können beispielsweise der Sitzungsstatus während der Erfassung von Frames und der Ankerbezeichner sein, der von der Cloud nach Abschluss des Uploadvorgangs zurückgegeben wird.

## <a name="locate-your-cloud-spatial-anchor"></a>Suchen nach Ihrem Raumanker für die Cloud

Nachdem Ihr Anker in die Cloud hochgeladen wurde, können wir erneut versuchen, danach zu suchen. Fügen Sie Ihrem Code zunächst die folgenden Importe hinzu.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=45-48&highlight=3-4)]

Fügen Sie anschließend der `handleTap()`-Methode den folgenden Code hinzu. Dieser Code bewirkt Folgendes:

- Entfernen der vorhandenen blauen Kugel vom Bildschirm
- Erneutes Initialisieren der Azure Spatial Anchors-Sitzung Mit dieser Aktion wird sichergestellt, dass der zu suchende Anker aus der Cloud stammt und dass es sich nicht um den von uns erstellten lokalen Anker handelt.
- Führen Sie eine Abfrage für den Anker aus, den wir in die Cloud hochgeladen haben.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

Nun binden wir den Code ein, der aufgerufen wird, wenn der abzufragende Anker gesucht und gefunden wird. Fügen Sie in Ihrer `initializeSession()`-Methode den folgenden Code hinzu. Mit diesem Codeausschnitt wird eine grüne Kugel erstellt und platziert, nachdem der Raumanker für die Cloud gefunden wurde. Auch das Tippen auf den Bildschirm wird wieder aktiviert, sodass Sie das gesamte Szenario wiederholen können: weiteren lokalen Anker erstellen, Upload durchführen und anschließend danach suchen.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=34-53)]

Das ist alles! Führen Sie für Ihre App ein letztes Mal die [erneute Bereitstellung](#trying-it-out) durch, um das gesamte Szenario auszuprobieren. Bewegen Sie Ihr Gerät, und platzieren Sie Ihre schwarze Kugel. Fahren Sie dann mit dem Umherbewegen Ihres Geräts fort, um Kameraframes zu erfassen, bis sich die Farbe der Kugel in Gelb ändert. Ihr lokaler Anker wird hochgeladen, und Ihre Kugel nimmt die blaue Farbe an. Tippen Sie abschließend noch einmal auf Ihren Bildschirm, damit Ihr lokaler Anker entfernt wird. Anschließend führen wir eine Abfrage nach der Entsprechung in der Cloud durch. Fahren Sie mit dem Umherbewegen Ihres Geräts fort, bis Ihr Raumanker für die Cloud gefunden wurde. Eine grüne Kugel sollte an der richtigen Position angezeigt werden, und Sie können das gesamte Szenario noch einmal durchführen.

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]

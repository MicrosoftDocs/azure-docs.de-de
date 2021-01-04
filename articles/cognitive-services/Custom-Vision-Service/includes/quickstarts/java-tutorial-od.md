---
author: PatrickFarley
ms.custom: devx-track-java
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/13/2020
ms.openlocfilehash: f0dbe64d6a44d7712d83c018c4db72fac871a838
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021332"
---
Erste Schritte mit der Custom Vision-Clientbibliothek für Java zum Erstellen eines Objekterkennungsmodells. Führen Sie die nachfolgenden Schritte zum Installieren des Pakets aus, und testen Sie den Beispielcode für grundlegende Aufgaben. Verwenden Sie dieses Beispiel als Vorlage für die Erstellung Ihrer eigenen Bilderkennungsanwendung.

> [!NOTE]
> Falls Sie ein Objekterkennungsmodell erstellen und trainieren möchten, _ohne_ Code zu schreiben, sehen Sie sich stattdessen die [browserbasierte Anleitung](../../get-started-build-detector.md) an.

Verwenden Sie die Custom Vision-Clientbibliothek für Java für folgende Aufgaben:

* Erstellen eines neuen Custom Vision-Projekts
* Hinzufügen von Tags zum Projekt
* Hochladen und Kennzeichnen von Bildern
* Trainieren des Projekts
* Veröffentlichen der aktuellen Iteration
* Testen des Vorhersageendpunkts

[Referenzdokumentation](/java/api/overview/azure/cognitiveservices/client/customvision?view=azure-java-stable) | Quellcode der Bibliothek [(Training)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-customvision-training) [(Vorhersage)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-customvision-prediction)| Artefakt (Maven) [(Training)](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-training/1.1.0-preview.2/jar) [(Vorhersage)](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-prediction/1.1.0-preview.2/jar) | 
[Beispiele](/samples/browse/?products=azure&terms=custom%20vision)


## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement: [Kostenloses Azure-Konto](https://azure.microsoft.com/free/cognitive-services/)
* Aktuelle Version des [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle-Buildtool](https://gradle.org/install/) oder einen anderen Abhängigkeit-Manager
* Wenn Sie über Ihr Azure-Abonnement verfügen, können Sie im Azure-Portal eine <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title="Erstellen einer Custom Vision-Ressource"  target="_blank">Custom Vision-Ressource erstellen<span class="docon docon-navigate-external x-hidden-focus"></span></a>, um eine Trainings- und Vorhersageressource erstellen und Ihre Schlüssel und den Endpunkt abrufen zu können. Warten Sie ihre Bereitstellung ab, und klicken Sie auf die Schaltfläche **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressourcen, um Ihre Anwendung mit Custom Vision zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-gradle-project"></a>Erstellen eines neuen Gradle-Projekts

Erstellen Sie in einem Konsolenfenster (etwa cmd, PowerShell oder Bash) ein neues Verzeichnis für Ihre App, und rufen Sie es auf. 

```console
mkdir myapp && cd myapp
```

Führen Sie den Befehl `gradle init` in Ihrem Arbeitsverzeichnis aus. Mit diesem Befehl werden grundlegende Builddateien für Gradle, u. a. die Datei *build.gradle.kts*, erstellt. Diese Datei wird zur Laufzeit zum Erstellen und Konfigurieren Ihrer Anwendung verwendet.

```console
gradle init --type basic
```

Wenn Sie zur Auswahl einer **DSL** aufgefordert werden, wählen Sie **Kotlin** aus.

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Navigieren Sie zur Datei *build.gradle.kts*, und öffnen Sie sie in Ihrer bevorzugten IDE bzw. Ihrem bevorzugten Text-Editor. Kopieren Sie anschließend die folgende Buildkonfiguration. Diese Konfiguration definiert das Projekt als Java-Anwendung, deren Einstiegspunkt die Klasse **CustomVisionQuickstart** ist. Die Custom Vision-Bibliotheken werden davon importiert.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "CustomVisionQuickstart"
}
repositories {
    mavenCentral()
}
dependencies {
    compile(group = "com.azure", name = "azure-cognitiveservices-customvision-training", version = "1.1.0-preview.2")
    compile(group = "com.azure", name = "azure-cognitiveservices-customvision-prediction", version = "1.1.0-preview.2")
}
```

### <a name="create-a-java-file"></a>Erstellen einer Java-Datei


Führen Sie in Ihrem Arbeitsverzeichnis den folgenden Befehl aus, um einen Projektquellordner zu erstellen:

```console
mkdir -p src/main/java
```

Navigieren Sie zu dem neuen Ordner, und erstellen Sie eine Datei namens *CustomVisionQuickstart.java*. Öffnen Sie sie in Ihrem bevorzugten Editor bzw. Ihrer bevorzugten IDE, und fügen Sie die folgenden `import`-Anweisungen hinzu:

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_imports)]

> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java) zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung.


Erstellen Sie in der Klasse **CustomVisionQuickstart** der Anwendung Variablen für die Schlüssel und den Endpunkt Ihrer Ressource.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_creds)]


> [!IMPORTANT]
> Öffnen Sie das Azure-Portal. Wenn die im Abschnitt **Voraussetzungen** erstellte [Produktname]-Ressource erfolgreich bereitgestellt wurde, klicken Sie unter **Nächste Schritte** auf die Schaltfläche **Zu Ressource wechseln**. Schlüssel und Endpunkt finden Sie auf der Seite mit dem **Schlüssel und dem Endpunkt** der Ressource unter **Ressourcenverwaltung**. 
>
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Weitere Informationen finden Sie im Cognitive Services-Artikel zur [Sicherheit](../../../cognitive-services-security.md).

Fügen Sie in der **main**-Methode der Anwendung Aufrufe für die Methoden hinzu, die in dieser Schnellstartanleitung verwendet werden. Diese werden später definiert.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_maincalls_od)]

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen verarbeiten einige der Hauptfunktionen der Custom Vision-Java-Clientbibliothek:

|Name|Beschreibung|
|---|---|
|[CustomVisionTrainingClient](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient?view=azure-java-stable) | Diese Klasse wird für die Erstellung, das Training und die Veröffentlichung Ihrer Modelle verwendet. |
|[CustomVisionPredictionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-java-stable)| Diese Klasse wird zum Abfragen Ihrer Modelle für Vorhersagen zur Objekterkennung verwendet.|
|[ImagePrediction](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.prediction.models.imageprediction?view=azure-java-stable)| Mit dieser Klasse wird eine einzelne Objektvorhersage für ein einzelnes Bild definiert. Sie enthält Eigenschaften für die Objekt-ID und den Namen, die Position des Begrenzungsrahmens für das Objekt und eine Zuverlässigkeitsbewertung.|

## <a name="code-examples"></a>Codebeispiele

Diese Codeausschnitte veranschaulichen, wie die folgenden Aufgaben mit der Custom Vision-Clientbibliothek für Java durchgeführt werden:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Erstellen eines neuen Custom Vision-Projekts](#create-a-new-custom-vision-project)
* [Hinzufügen von Tags zum Projekt](#add-tags-to-the-project)
* [Hochladen und Kennzeichnen von Bildern](#upload-and-tag-images)
* [Trainieren des Projekts](#train-the-project)
* [Veröffentlichen der aktuellen Iteration](#publish-the-current-iteration)
* [Testen des Vorhersageendpunkts](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Instanziieren Sie in Ihrer **main**-Methode Trainings- und Vorhersageclients, indem Sie Ihren Endpunkt und die Schlüssel verwenden.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Erstellen eines neuen Custom Vision-Projekts

Die nächste Methode erstellt ein Objekterkennungsprojekt. Das erstellte Projekt wird auf der [Custom Vision-Website](https://customvision.ai/) angezeigt, die Sie zuvor besucht haben. Informationen zur Angabe weiterer Optionen bei der Erstellung Ihres Projekts finden Sie im Artikel zu Überladungen der Methode [CreateProject](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_&preserve-view=true). Informationen zur Projekterstellung finden Sie unter [Schnellstart: Informationen zum Erstellen einer Objekterkennung mit Custom Vision](../../get-started-build-detector.md).

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create_od)]

## <a name="add-tags-to-your-project"></a>Hinzufügen von Kategorien zu Ihrem Projekt

Mit dieser Methode werden die Tags definiert, die Sie zum Trainieren des Modells verwenden.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags_od)]

## <a name="upload-and-tag-images"></a>Hochladen und Kennzeichnen von Bildern

Laden Sie zunächst die Beispielbilder für dieses Projekt herunter. Speichern Sie den Inhalt des [Ordners mit den Beispielbildern](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) auf Ihrem lokalen Gerät.

Wenn Sie Bilder in Objekterkennungsprojekten mit Tags versehen, müssen Sie mithilfe normalisierter Koordinaten die Region des jeweiligen markierten Objekts angeben. Im folgenden Code wird jedem der Beispielbilder die entsprechende markierte Region zugeordnet.

> [!NOTE]
> Falls Sie über kein Hilfsprogramm zum Klicken und Ziehen verfügen, um die Koordinaten von Regionen zu markieren, können Sie die Webbenutzeroberfläche unter [Customvision.ai](https://www.customvision.ai/) verwenden. In diesem Beispiel sind die Koordinaten bereits vorhanden.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_od_mapping)]

Der nächste Codeblock fügt die Bilder dem Projekt hinzu. Sie müssen die Argumente der Aufrufe vom Typ `GetImage` so ändern, dass sie auf die Speicherorte der heruntergeladenen Ordner **fork** und **scissors** verweisen.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload_od)]

Der vorherige Codeausschnitt verwendet zwei Hilfsfunktionen, die die Bilder als Ressourcenstreams abrufen und in den Dienst hochladen. (Sie können bis zu 64 Bilder in einem Batch hochladen.) Definieren Sie diese Methoden. 

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

## <a name="train-the-project"></a>Trainieren des Projekts

Mit dieser Methode wird die erste Trainingsiteration im Projekt erstellt. Der Dienst wird so lange abgefragt, bis das Training abgeschlossen ist.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train_od)]


## <a name="publish-the-current-iteration"></a>Veröffentlichen der aktuellen Iteration

Mit dieser Methode wird die aktuelle Iteration des Modells zum Abfragen verfügbar gemacht. Sie können den Modellnamen als Referenz zum Senden von Vorhersageanforderungen verwenden. Hierbei ist es erforderlich, dass Sie Ihren eigenen Wert für `predictionResourceId` eingeben. Sie finden die Vorhersageressourcen-ID im Azure-Portal auf der Registerkarte **Übersicht** der Ressource, wo sie als **Abonnement-ID** angegeben ist.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_publishOD)]

## <a name="test-the-prediction-endpoint"></a>Testen des Vorhersageendpunkts

Diese Methode lädt das Testbild, fragt den Modellendpunkt ab und gibt Vorhersagedaten an die Konsole aus.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_predict_od)]

## <a name="run-the-application"></a>Ausführen der Anwendung

Sie können die App mit folgendem Befehl erstellen:

```console
gradle build
```

Führen Sie die Anwendung mit Befehl `gradle run` aus:

```console
gradle run
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-Befehlszeilenschnittstelle](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Nächste Schritte

Sie haben jetzt die einzelnen Schritte des Objekterkennungsprozesses im Code ausgeführt. In diesem Beispiel wird eine einzelne Trainingsiteration ausgeführt. Zur Verbesserung der Genauigkeit muss ein Modell jedoch häufig mehrmals trainiert und getestet werden. In der folgenden Anleitung wird die Bildklassifizierung behandelt. Die Prinzipien sind jedoch mit denen der Objekterkennung vergleichbar.

> [!div class="nextstepaction"]
> [Testen und erneutes Trainieren eines Modells mit Custom Vision Service](../../test-your-model.md)

* [Was ist Custom Vision?](../../overview.md)
* Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java).

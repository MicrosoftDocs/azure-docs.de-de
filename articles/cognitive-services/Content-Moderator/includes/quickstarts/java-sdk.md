---
title: 'Schnellstart: Content Moderator-Clientbibliothek für Java'
titleSuffix: Azure Cognitive Services
description: Informieren Sie sich in diesem Schnellstart über die ersten Schritte mit der Azure Content Moderator-Clientbibliothek für Java. Integrieren Sie Software zur Inhaltsfilterung in Ihre App, um gesetzliche Vorschriften einzuhalten oder Benutzern die für sie beabsichtigte Umgebung zu bieten.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 10/16/2020
ms.custom: devx-track-java, cog-serv-seo-aug-2020
ms.author: pafarley
ms.openlocfilehash: 7713765a36207f0d9da05c4c11629e4a7f1164d9
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561510"
---
Hier erhalten Sie Informationen zu den ersten Schritten mit der Azure Content Moderator-Clientbibliothek für Java. Führen Sie die nachfolgenden Schritte zum Installieren des Maven-Pakets aus, und testen Sie den Beispielcode für grundlegende Aufgaben. 

Content Moderator ist ein KI-Dienst für die Behandlung potenziell anstößiger, riskanter oder anderweitig unerwünschter Inhalte. Verwenden Sie den KI-gestützten Dienst zur Inhaltsmoderation, um Text, Bilder und Videos zu scannen und Inhaltsflags automatisch anzuwenden. Integrieren Sie Software zur Inhaltsfilterung in Ihre App, um gesetzliche Vorschriften einzuhalten oder Benutzern die für sie beabsichtigte Umgebung zu bieten.

Führen Sie mit der Content Moderator-Clientbibliothek für Java die folgenden Aktionen aus:

* Moderieren von Text
* Moderieren von Bildern

[Referenzdokumentation](/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-contentmoderator) |[Artefakt (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) | [Beispiele](/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement: [Kostenloses Azure-Konto](https://azure.microsoft.com/free/cognitive-services/)
* Aktuelle Version des [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle-Buildtool](https://gradle.org/install/) oder einen anderen Abhängigkeit-Manager
* Wenn Sie über ein Azure-Abonnement verfügen, können Sie im Azure-Portal <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title="Erstellen einer Content Moderator-Ressource"  target="_blank">eine Content Moderator-Ressource erstellen <span class="docon docon-navigate-external x-hidden-focus"></span></a>, um Ihren Schlüssel und Endpunkt abzurufen. Warten Sie ihre Bereitstellung ab, und klicken Sie auf die Schaltfläche **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um für Ihre Anwendung eine Verbindung mit Content Moderator herzustellen. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
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

## <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Navigieren Sie zur Datei *build.gradle.kts*, und öffnen Sie sie in Ihrer bevorzugten IDE bzw. Ihrem bevorzugten Text-Editor. Kopieren Sie anschließend die folgende Buildkonfiguration. Diese Konfiguration definiert das Projekt als Java-Anwendung, deren Einstiegspunkt die Klasse **ContentModeratorQuickstart** ist. Dabei werden die Content Moderator-Clientbibliothek sowie das GSON SDK für die JSON-Serialisierung importiert.

```kotlin
plugins {
    java
    application
}

application{ 
    mainClassName = "ContentModeratorQuickstart"
}

repositories{
    mavenCentral()
}

dependencies{
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-contentmoderator", version = "1.0.2-beta")
    compile(group = "com.google.code.gson", name = "gson", version = "2.8.5")
}
```

### <a name="create-a-java-file"></a>Erstellen einer Java-Datei


Führen Sie in Ihrem Arbeitsverzeichnis den folgenden Befehl aus, um einen Projektquellordner zu erstellen:

```console
mkdir -p src/main/java
```

Navigieren Sie zu dem neuen Ordner, und erstellen Sie eine Datei mit dem Namen *ContentModeratorQuickstart.java*. Öffnen Sie sie in Ihrem bevorzugten Editor bzw. Ihrer bevorzugten IDE, und fügen Sie die folgenden `import`-Anweisungen hinzu:

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java) zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung.

Erstellen Sie in der Klasse **ContentModeratorQuickstart** der Anwendung Variablen für den Schlüssel und den Endpunkt Ihrer Ressource.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_creds)]

> [!IMPORTANT]
> Öffnen Sie das Azure-Portal. Wenn die im Abschnitt **Voraussetzungen** erstellte [Produktname]-Ressource erfolgreich bereitgestellt wurde, klicken Sie unter **Nächste Schritte** auf die Schaltfläche **Zu Ressource wechseln**. Schlüssel und Endpunkt finden Sie auf der Seite mit dem **Schlüssel und dem Endpunkt** der Ressource unter **Ressourcenverwaltung**. 
>
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Weitere Informationen finden Sie im Cognitive Services-Artikel zur [Sicherheit](../../../cognitive-services-security.md).

Fügen Sie in der **main**-Methode der Anwendung Aufrufe für die Methoden hinzu, die in dieser Schnellstartanleitung verwendet werden. Diese werden später definiert.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_maincalls)]


## <a name="object-model"></a>Objektmodell

Mit den folgenden Klassen werden einige Hauptfunktionen der Content Moderator-Clientbibliothek für Java verarbeitet:

|Name|BESCHREIBUNG|
|---|---|
|[ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|Diese Klasse wird für alle Content Moderator-Funktionen benötigt. Sie instanziieren sie mit Ihren Abonnementinformationen und verwenden sie zum Generieren von Instanzen anderer Klassen.|
|[ImageModeration](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|Diese Klasse stellt die Funktionalität zum Analysieren von Bildern auf nicht jugendfreie Inhalte, persönliche Informationen oder menschliche Gesichter bereit.|
|[TextModerations](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|Diese Klasse stellt die Funktionalität zum Analysieren von Text auf Sprache, Obszönitäten, Fehler und persönliche Informationen bereit.|
|[Überprüfungen](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|Diese Klasse stellt die Funktionalität der Überprüfungs-APIs bereit, einschließlich der Methoden zum Erstellen von Aufträgen, benutzerdefinierten Workflows und Überprüfungen durch Personen.|


## <a name="code-examples"></a>Codebeispiele

Diese Codeausschnitte veranschaulichen, wie die folgenden Aufgaben mit der Content Moderator-Clientbibliothek für Java ausgeführt werden:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Moderieren von Text](#moderate-text)
* [Moderieren von Bildern](#moderate-images)


## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Erstellen Sie mithilfe des Werts für den Abonnementendpunkt und des Abonnementschlüssels in der Methode `main` der Anwendung ein [ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)-Objekt.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]



## <a name="moderate-text"></a>Moderieren von Text

### <a name="set-up-sample-text"></a>Einrichten von Beispieltext

Definieren Sie am Anfang der Klasse **ContentModeratorQuickstart** einen Verweis auf eine lokale Textdatei. Fügen Sie Ihrem Projektverzeichnis eine TXT-Datei hinzu, und geben Sie den zu analysierenden Text ein.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_var)]

### <a name="analyze-text"></a>Analysieren von Text

Erstellen Sie eine neue Methode, die die TXT-Datei liest und die **screenText**-Methode für jede Zeile aufruft.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod)]

### <a name="print-text-moderation-results"></a>Ausgeben der Ergebnisse für die Textmoderation

Fügen Sie den folgenden Code hinzu, um die Moderationsergebnisse in einer JSON-Datei in Ihrem Projektverzeichnis auszugeben.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_print)]

Schließen Sie die Anweisungen `try` und `catch` aus, um die Methode abzuschließen.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_catch)]


## <a name="moderate-images"></a>Moderieren von Bildern

### <a name="set-up-sample-image"></a>Einrichten eines Beispielbilds

Erstellen Sie in einer neuen Methode ein **[BodyModelModel](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.bodymodelmodel?view=azure-java-stable)** -Objekt mit einer angegebenen URL-Zeichenfolge, die auf ein Bild verweist.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod)]


### <a name="define-helper-class"></a>Definieren der Hilfsklasse

Fügen Sie dann in der Datei *ContentModeratorQuickstart.java* die folgende Klassendefinition innerhalb der Klasse **ContentModeratorQuickstart** hinzu. Diese innere Klasse wird bei der Bildmoderation verwendet.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]


### <a name="analyze-content"></a>Analysieren von Inhalten
Diese Codezeile überprüft das Bild unter der angegebenen URL auf nicht jugendfreien oder freizügigen Inhalt. Informationen zu diesen Begriffen finden Sie im konzeptionellen Leitfaden zur Bildmoderation.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Überprüfen auf Text
Diese Codezeile überprüft das Bild auf sichtbaren Text.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Überprüfen auf Gesichter
Diese Codezeile überprüft das Bild auf menschliche Gesichter.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Speichern Sie abschließend die zurückgegebenen Informationen in der Liste `EvaluationData`.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Ausgeben der Ergebnisse

Fügen Sie nach der Schleife `while` den folgenden Code hinzu. Dieser gibt die Ergebnisse in der Konsole und in einer Ausgabedatei (*src/main/resources/ModerationOutput.json*) aus.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Schließen Sie die `try`-Anweisung aus, und fügen Sie eine `catch`-Anweisung hinzu, um die Methode abzuschließen.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>Ausführen der Anwendung

Sie können die App mit folgendem Befehl erstellen:

```console
gradle build
```

Führen Sie die Anwendung mit Befehl `gradle run` aus:

```console
gradle run
```

Navigieren Sie dann zur Datei *src/main/resources/ModerationOutput.json*, und zeigen Sie die Ergebnisse der Inhaltsmoderation an.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-Befehlszeilenschnittstelle](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie die Content Moderator-Java-Bibliothek zum Ausführen von Moderationsaufgaben verwenden. Im Anschluss können Sie sich im konzeptionellen Leitfaden ausführlicher über die Moderation von Bildern oder anderen Medien informieren.

> [!div class="nextstepaction"]
> [Lernen von Bildmoderationskonzepten](../../image-moderation-api.md)

* [Was ist Azure Content Moderator?](../../overview.md)
* Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java).
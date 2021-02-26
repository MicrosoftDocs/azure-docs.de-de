---
title: 'Schnellstart: Clientbibliothek der Maschinelles Sehen-API für .NET'
description: Beginnen Sie mit dieser Schnellstartanleitung mit dem Einsatz der Clientbibliothek für maschinelles Sehen für .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 8bb0c8e58c7b5eb1a0ba8a8ca4006ef04b2e2423
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99531664"
---
<a name="HOLTop"></a>

Verwenden Sie die Clientbibliothek für maschinelles Sehen für Folgendes:

* Analysieren eines Bilds auf Tags, Textbeschreibungen, Gesichter, nicht jugendfreie Inhalte usw.
* Lesen von gedrucktem und handschriftlichem Text mit der Lese-API

[Referenzdokumentation](/dotnet/api/overview/azure/cognitiveservices/client/computervision) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision) | [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [Beispiele](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement: [Kostenloses Azure-Konto](https://azure.microsoft.com/free/cognitive-services/)
* Die [Visual Studio-IDE](https://visualstudio.microsoft.com/vs/) oder die aktuelle Version von [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie über <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Erstellen einer Ressource für maschinelles Sehen"  target="_blank"> im Azure-Portal eine Ressource für maschinelles Sehen <span class="docon docon-navigate-external x-hidden-focus"></span></a> erstellen, um Ihren Schlüssel und Endpunkt abzurufen. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um eine Verbindung Ihrer Anwendung mit dem Dienst für maschinelles Sehen herzustellen. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-c-application"></a>Erstellen einer neuen C#-Anwendung

#### <a name="visual-studio-ide"></a>[Visual Studio-IDE](#tab/visual-studio)

Erstellen Sie mit Visual Studio eine neue .NET Core-Anwendung. 

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek 

Installieren Sie nach der Erstellung eines neuen Projekts die Clientbibliothek, indem Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Projektmappe klicken und **NuGet-Pakete verwalten** auswählen. Wählen Sie im daraufhin geöffneten Paket-Manager die Option **Durchsuchen** aus, aktivieren Sie das Kontrollkästchen **Vorabversion einbeziehen**, und suchen Sie nach `Microsoft.Azure.CognitiveServices.Vision.ComputerVision`. Wählen Sie die Version `6.0.0-preview.1` und anschließend **Installieren** aus. 

#### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Verwenden Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) den Befehl `dotnet new` zum Erstellen einer neuen Konsolen-App mit dem Namen `computer-vision-quickstart`. Dieser Befehl erstellt ein einfaches „Hallo Welt“-C#-Projekt mit einer einzigen Quelldatei: *Program.cs*.

```console
dotnet new console -n computer-vision-quickstart
```

Wechseln Sie zum Ordner der neu erstellten App. Sie können die Anwendung mit folgendem Befehl erstellen:

```console
dotnet build
```

Die Buildausgabe sollte keine Warnungen oder Fehler enthalten. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Installieren Sie im Anwendungsverzeichnis mit dem folgenden Befehl die Clientbibliothek für maschinelles Sehen für .NET:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0
```

---

> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung.

Öffnen Sie über das Projektverzeichnis die Datei *ComputerVisionQuickstart.cs* in Ihrem bevorzugten Editor oder Ihrer bevorzugten IDE. Fügen Sie die folgenden `using`-Anweisungen hinzu:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using)]

Erstellen Sie in der **Programmklasse** der Anwendung Variablen für den Azure-Endpunkt und -Schlüssel Ihrer Ressource.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_vars)]

> [!IMPORTANT]
> Öffnen Sie das Azure-Portal. Wenn die im Abschnitt **Voraussetzungen** erstellte Ressource für maschinelles Sehen erfolgreich bereitgestellt wurde, klicken Sie unter **Nächste Schritte** auf die Schaltfläche **Zu Ressource wechseln**. Schlüssel und Endpunkt finden Sie auf der Seite mit dem **Schlüssel und dem Endpunkt** der Ressource unter **Ressourcenverwaltung**. 
>
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Weitere Informationen finden Sie im Cognitive Services-Artikel zur [Sicherheit](../../../cognitive-services-security.md).

Fügen Sie in der `Main`-Methode der Anwendung Aufrufe für die Methoden hinzu, die in dieser Schnellstartanleitung verwendet werden. Die Erstellung führen Sie zu einem späteren Zeitpunkt durch.


[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyzeinmain)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extracttextinmain)]

> [!div class="nextstepaction"]
> [Ich habe den Client eingerichtet.](?success=set-up-client#object-model) [Bei mir ist ein Problem aufgetreten.](https://www.research.net/r/7QYZKHL?issue=set-up-client)

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen verarbeiten einige der Hauptfunktionen des Computer Vision .NET SDK.

|Name|BESCHREIBUNG|
|---|---|
| [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | Diese Klasse wird für alle Funktionen der Maschinelles Sehen-API benötigt. Sie instanziieren sie mit Ihren Abonnementinformationen und verwenden sie für die meisten Bildvorgänge.|
|[ComputerVisionClientExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions)| Diese Klasse enthält zusätzliche Methoden für **ComputerVisionClient**.|
|[VisualFeatureTypes](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| Diese Enumeration definiert die verschiedenen Typen der Bildanalyse, die bei einem standardmäßigen Analysevorgang ausgeführt werden können. Sie geben abhängig von Ihren Anforderungen verschiedene VisualFeatureTypes-Werte an. |

## <a name="code-examples"></a>Codebeispiele

Diese Codeausschnitte veranschaulichen, wie die folgenden Aufgaben mit der Clientbibliothek der Maschinelles Sehen-API für .NET ausgeführt werden:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Analysieren eines Bilds](#analyze-an-image)
* [Lesen von gedrucktem und handschriftlichem Text](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

> [!NOTE]
> In dieser Schnellstartanleitung wird vorausgesetzt, dass Sie für den Schlüssel und den Endpunkt für maschinelles Sehen (mit den Namen `COMPUTER_VISION_SUBSCRIPTION_KEY` bzw. `COMPUTER_VISION_ENDPOINT`) [eine Umgebungsvariable erstellt haben](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication).

Instanziieren Sie in einer neuen Methode einen Client mit Ihrem Endpunkt und Schlüssel. Erstellen Sie ein Objekt vom Typ **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials)** mit Ihrem Schlüssel, und verwenden Sie es mit Ihrem Endpunkt, um ein Objekt vom Typ **[ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient)** zu erstellen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

> [!div class="nextstepaction"]
> [Ich habe den Client authentifiziert.](?success=authenticate-client#analyze-an-image) [Bei mir ist ein Problem aufgetreten.](https://www.research.net/r/7QYZKHL?issue=authenticate-client)

## <a name="analyze-an-image"></a>Analysieren von Bildern

Im folgenden Code wird die Methode `AnalyzeImageUrl` definiert, die das Clientobjekt verwendet, um ein Remotebild zu analysieren und die Ergebnisse auszugeben. Die Methode gibt eine Textbeschreibung, eine Kategorisierung, eine Liste mit Tags, erkannte Gesichter, Flags für nicht jugendfreie Inhalte, Hauptfarben und den Bildtyp zurück.

> [!TIP]
> Sie können auch ein lokales Bild analysieren. Sehen Sie sich die [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient)-Methoden an, etwa **AnalyzeImageInStreamAsync**. Alternativ finden Sie im Beispielcode auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) Szenarien zu lokalen Bildern.

### <a name="set-up-test-image"></a>Einrichten eines Testbilds

Speichern Sie in der **Programmklasse** einen Verweis auf die URL des Bilds, das Sie analysieren möchten.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_url)]

### <a name="specify-visual-features"></a>Angeben visueller Merkmale

Definieren Sie Ihre neue Methode für die Bildanalyse. Fügen Sie den folgenden Code hinzu, der angibt, welche visuellen Features Sie bei der Analyse extrahieren möchten. Eine vollständige Liste finden Sie in der Enumeration **[VisualFeatureTypes](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)** .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_visualfeatures)]

Fügen Sie die folgenden Codeblöcke in die Methode **AnalyzeImageUrl** ein, um die Features zu implementieren. Denken Sie daran, am Ende eine schließende eckige Klammer hinzuzufügen.

```csharp
}
```

### <a name="analyze"></a>Analysieren

Die Methode **AnalyzeImageAsync** gibt ein **ImageAnalysis**-Objekt zurück, das alle extrahierten Informationen enthält.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_call)]

In den folgenden Abschnitten wird gezeigt, wie Sie diese Informationen ausführlich analysieren.

### <a name="get-image-description"></a>Abrufen der Bildbeschreibung

Mit dem folgenden Code wird die Liste der generierten Beschriftungen für das Bild abgerufen. Weitere Informationen finden Sie unter [Beschreiben von Bildern in lesbarer Sprache](../../concept-describing-images.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_describe)]

### <a name="get-image-category"></a>Abrufen der Bildkategorie

Mit dem folgenden Code wird die erkannte Kategorie des Bilds abgerufen. Weitere Informationen finden Sie unter [Kategorisieren von Bildern nach Schlüsselinhalt](../../concept-categorizing-images.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_categorize)]

### <a name="get-image-tags"></a>Abrufen von Bildtags

Mit dem folgenden Code werden die erkannten Tags im Bild abgerufen. Weitere Informationen finden Sie unter [Inhaltstags](../../concept-tagging-images.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_tags)]

### <a name="detect-objects"></a>Erkennen von Objekten

Der folgende Code erkennt alltägliche Objekte im Bild und gibt sie in der Konsole aus. Weitere Informationen finden Sie unter [Objekterkennung](../../concept-object-detection.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_objects)]

### <a name="detect-brands"></a>Erkennen von Marken

Der folgende Code erkennt Marken und Firmenlogos im Bild und gibt sie in der Konsole aus. Weitere Informationen finden Sie unter [Markenerkennung](../../concept-brand-detection.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_brands)]

### <a name="detect-faces"></a>Erkennen von Gesichtern

Mit dem folgenden Code werden die erkannten Gesichter im Bild mit den Rechteckkoordinaten und ausgewählten Gesichtsattributen zurückgegeben. Weitere Informationen finden Sie unter [Gesichtserkennung mit maschinellem Sehen](../../concept-detecting-faces.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Erkennen von nicht jugendfreien, freizügigen oder gewaltverherrlichenden Inhalten

Der folgende Code gibt an, ob nicht jugendfreie Inhalte im Bild erkannt wurden. Weitere Informationen finden Sie unter [Erkennen von nicht jugendfreien und freizügigen Inhalten](../../concept-detecting-adult-content.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Abrufen des Farbschemas für ein Bild

Mit dem folgenden Code werden die die erkannten Farbattribute im Bild ausgegeben, etwa die vorherrschenden Farben und die Akzentfarbe. Weitere Informationen finden Sie unter [Erkennen von Farbschemas auf Bildern](../../concept-detecting-color-schemes.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Abrufen des domänenspezifischen Inhalts

Maschinelles Sehen kann spezialisierte Modelle zur weiteren Analyse von Bildern verwenden. Weitere Informationen finden Sie unter [Erkennen domänenspezifischer Inhalte](../../concept-detecting-domain-content.md). 

Der folgende Code analysiert Daten zu erkannten Prominenten im Bild:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_celebs)]

Der folgende Code analysiert Daten zu erkannten Sehenswürdigkeiten im Bild:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Abrufen des Bildtyps

Die folgende Funktion gibt Informationen zur Art des Bilds (ClipArt oder Strichzeichnung) aus.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_type)]

> [!div class="nextstepaction"]
> [Ich habe ein Bild analysiert.](?success=analyze-image#read-printed-and-handwritten-text) [Bei mir ist ein Problem aufgetreten.](https://www.research.net/r/7QYZKHL?issue=analyze-image)

## <a name="read-printed-and-handwritten-text"></a>Lesen von gedrucktem und handschriftlichem Text

Maschinelles Sehen kann sichtbaren Text in einem Bild lesen und in eine Zeichenfolge konvertieren. Weitere Informationen zur Texterkennung finden Sie in der Konzeptdokumentation unter [Optische Zeichenerkennung (OCR)](../../concept-recognizing-text.md#read-api). Der Code in diesem Abschnitt nutzt das aktuelle [SDK-Release für maschinelles Sehen für Read 3.0](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/6.0.0-preview.1) und definiert die Methode `BatchReadFileUrl`. Hierbei wird das Clientobjekt verwendet, um Text im Bild zu erkennen und zu extrahieren.

> [!TIP]
> Sie können auch Text aus einem lokalen Bild extrahieren. Sehen Sie sich die [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient)-Methoden an, etwa **ReadInStreamAsync**. Alternativ finden Sie im Beispielcode auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) Szenarien zu lokalen Bildern.

### <a name="set-up-test-image"></a>Einrichten eines Testbilds

Speichern Sie in der Klasse **Program** einen Verweis auf die URL des Bilds, aus dem Sie Text extrahieren möchten. Dieser Codeausschnitt enthält Beispielbilder für gedruckten und handschriftlichen Text.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readtext_url)]

### <a name="call-the-read-api"></a>Aufrufen der Lese-API

Hiermit wird die neue Methode zum Lesen von Text definiert. Fügen Sie den folgenden Code hinzu, mit dem die **ReadAsync**-Methode für das angegebene Bild aufgerufen wird. Dies gibt eine Vorgangs-ID zurück und startet einen asynchronen Prozess, um den Inhalt des Bilds zu lesen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_1)]

### <a name="get-read-results"></a>Abrufen der Leseergebnisse

Rufen Sie als Nächstes die Vorgangs-ID ab, die vom Aufruf **ReadAsync** zurückgegeben wurde, und verwenden Sie sie zum Abfragen des Diensts auf Vorgangsergebnisse. Der folgende Code überprüft den Vorgang, bis die Ergebnisse zurückgegeben werden. Anschließend werden die extrahierten Textdaten in der Konsole ausgegeben.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_2)]

### <a name="display-read-results"></a>Anzeigen der Leseergebnisse

Fügen Sie den folgenden Code hinzu, um die abgerufenen Textdaten zu analysieren und anzuzeigen, und schließen Sie die Methodendefinition ab.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_3)]

## <a name="run-the-application"></a>Ausführen der Anwendung

#### <a name="visual-studio-ide"></a>[Visual Studio-IDE](#tab/visual-studio)

Führen Sie die Anwendung aus, indem Sie oben im IDE-Fenster auf die Schaltfläche **Debuggen** klicken.

#### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Führen Sie die Anwendung mit dem Befehl `dotnet run` aus dem Anwendungsverzeichnis aus.

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-Befehlszeilenschnittstelle](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
>[Referenz zur Maschinelles Sehen-API (.NET)](/dotnet/api/overview/azure/cognitiveservices/client/computervision)

* [Worum handelt es sich bei maschinellem Sehen?](../../overview.md)
* Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs).

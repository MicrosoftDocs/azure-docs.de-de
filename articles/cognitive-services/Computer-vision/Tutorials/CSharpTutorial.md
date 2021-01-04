---
title: 'Beispiel: Erkunden einer Bildverarbeitungs-App in C#'
titleSuffix: Azure Cognitive Services
description: Hier lernen Sie eine einfache Windows-App kennen, die die Maschinelles Sehen-API in Azure Cognitive Services verwendet. Führen Sie eine optische Zeichenerkennung durch, erstellen Sie Miniaturansichten, und arbeiten Sie mit visuellen Merkmalen in einem Bild.
services: cognitive-services
author: PatrickFarley
manager: nolachar
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 522cf801e7c1bfdd3ed1f452c123a2db701c0c42
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008802"
---
# <a name="sample-explore-an-image-processing-app-with-c"></a>Beispiel: Erkunden einer Bildverarbeitungs-App mit C#

Lernen Sie eine einfache Windows-Anwendung kennen, die maschinelles Sehen verwendet, um eine optische Zeichenerkennung (OCR) durchzuführen, intelligent zugeschnittene Miniaturansichten zu erstellen sowie visuelle Merkmale (einschließlich Gesichter) in einem Bild zu erkennen, zu kategorisieren, zu markieren und zu beschreiben. Im folgenden Beispiel können Sie eine Bild-URL oder eine lokal gespeicherte Datei übermitteln. Sie können dieses Open-Source-Beispiel als Vorlage verwenden, um Ihre eigene App für Windows mit der Maschinelles Sehen-API und Windows Presentation Foundation (WPF), einer Komponenten von .NET Framework, zu erstellen.

> [!div class="checklist"]
> * Abrufen der Beispiel-App aus GitHub
> * Öffnen und Erstellen der Beispiel-App in Visual Studio
> * Ausführen der Beispiel-App und ihre Verwendung für verschiedene Szenarien
> * Untersuchen der verschiedenen Szenarien, die mit der Beispiel-App möglich sind

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie die Beispiel-App erkunden können, müssen die folgenden Voraussetzungen erfüllt sein:

* Sie benötigen [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) oder höher.
* Azure-Abonnement: [Kostenloses Azure-Konto](https://azure.microsoft.com/free/cognitive-services/)
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie über <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Erstellen einer Ressource für maschinelles Sehen"  target="_blank"> im Azure-Portal eine Ressource für maschinelles Sehen <span class="docon docon-navigate-external x-hidden-focus"></span></a> erstellen, um Ihren Schlüssel und Endpunkt abzurufen. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um eine Verbindung Ihrer Anwendung mit dem Dienst für maschinelles Sehen herzustellen. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

## <a name="get-the-sample-app"></a>Abrufen der Beispiel-App

Die Beispiel-App für maschinelles Sehen finden Sie auf GitHub im `Microsoft/Cognitive-Vision-Windows`-Repository. Dieses Repository enthält auch das `Microsoft/Cognitive-Common-Windows`-Repository als Git-Submodul. Sie können dieses Repository, einschließlich des Submoduls, rekursiv klonen, entweder mit dem Befehl `git clone --recurse-submodules` über die Befehlszeile oder mit GitHub Desktop.

Führen Sie beispielsweise den folgenden Befehl aus, um das Repository für die Beispiel-App für maschinelles Sehen rekursiv über eine Eingabeaufforderung zu klonen:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Vision-Windows.git
```

> [!IMPORTANT]
> Laden Sie dieses Repository nicht als ZIP-Datei herunter. Git enthält keine Submodule, wenn das Repository als ZIP-Datei heruntergeladen wird.

### <a name="get-optional-sample-images"></a>Abrufen optionaler Beispielbilder

Sie können die Beispielbilder verwenden, die in der Beispiel-App für die [Gesichtserkennung](../../Face/Overview.md) bereitgestellt werden. Sie finden sie auf GitHub im `Microsoft/Cognitive-Face-Windows`-Repository. Diese Beispiel-App enthält den Ordner `/Data`, der mehrere Bilder von Personen beinhaltet. Sie können dieses Repository ebenfalls rekursiv klonen, und zwar mithilfe der entsprechenden Methoden für die Beispiel-App für maschinelles Sehen.

Führen Sie beispielsweise den folgenden Befehl aus, um das Repository für die Beispiel-App für die Gesichtserkennung rekursiv über eine Eingabeaufforderung zu klonen:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Face-Windows.git
```

## <a name="open-and-build-the-sample-app-in-visual-studio"></a>Öffnen und Erstellen der Beispiel-App in Visual Studio

Sie müssen die Beispiel-App erstellen, bevor Sie sie ausführen oder erkunden können, damit Visual Studio Abhängigkeiten auflösen kann. Führen Sie zum Öffnen und Erstellen der Beispiel-App die folgenden Schritte aus:

1. Öffnen Sie die Visual Studio-Projektmappendatei `/Sample-WPF/VisionAPI-WPF-Samples.sln` in Visual Studio.
1. Überprüfen Sie, ob die Visual Studio-Projektmappe zwei Projekte enthält:  

   * SampleUserControlLibrary
   * VisionAPI-WPF-Samples  

   Wenn das Projekt „SampleUserControlLibrary“ nicht verfügbar ist, bestätigen Sie, dass Sie das `Microsoft/Cognitive-Vision-Windows`-Repository rekursiv geklont haben.
1. Drücken Sie in Visual Studio entweder „Strg+Shift+B“, oder wählen Sie im Menüband **Erstellen** und dann **Projektmappe erstellen** aus, um die Projektmappe zu erstellen.

## <a name="run-and-interact-with-the-sample-app"></a>Ausführen und Verwenden der Beispiel-App

Sie können die Beispiel-App ausführen, um ihre Interaktion mit Ihnen und der Clientbibliothek für maschinelles Sehen zu verstehen, wenn Sie verschiedene Aufgaben ausführen, z.B. Miniaturansichten erzeugen oder Bilder markieren. Führen Sie die folgenden Schritte aus, um die Beispiel-App auszuführen und sie zu verwenden:

1. Drücken Sie nach der Erstellung entweder **F5**, oder wählen Sie im Menüband **Debuggen** und dann **Debuggen starten** aus, um die Beispiel-App auszuführen.
1. Wenn die Beispiel-App angezeigt wird, wählen Sie im Navigationsbereich **Abonnementschlüsselverwaltung** aus, um die Seite „Abonnementschlüsselverwaltung“ anzuzeigen.
   ![Seite „Abonnementschlüsselverwaltung“](../Images/Vision_UI_Subscription.PNG)  
1. Geben Sie in das Feld **Abonnementschlüssel** Ihren Abonnementschlüssel ein.
1. Geben Sie unter **Endpunkt** die Endpunkt-URL ein.  
   [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Wenn Sie den Abonnementschlüssel und die Endpunkt-URL beim nächsten Ausführen der Beispiel-App nicht eingeben möchten, wählen Sie **Einstellung speichern** aus, damit der Abonnementschlüssel und die Endpunkt-URL auf Ihrem Computer gespeichert werden. Wenn Sie Ihren zuvor gespeicherten Abonnementschlüssel und Ihre Endpunkt-URL löschen möchten, wählen Sie **Einstellung löschen** aus.

   > [!NOTE]
   > Die Beispiel-App verwendet isolierten Speicher und `System.IO.IsolatedStorage` zum Speichern Ihres Abonnementschlüssels und Ihrer Endpunkt-URL.

1. Wählen Sie im Navigationsbereich unter **Szenario auswählen** eines der aktuellen Beispiel-App-Szenarien aus:  

   | Szenario | BESCHREIBUNG |
   |----------|-------------|
   |Bild analysieren | Verwendet den Vorgang [Bild analysieren](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) zum Analysieren eines lokalen oder Remotebilds. Sie können die visuellen Merkmale und die Sprache für die Analyse bestimmen und sowohl das Bild als auch die Ergebnisse anzeigen.  |
   |Bild mit Domänenmodell analysieren | Verwendet den Vorgang [Domänenspezifische Modelle auflisten](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) zum Auflisten der verfügbaren Domänenmodelle und den Vorgang [Domänenspezifischen Inhalt erkennen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) zum Analysieren eines lokalen oder Remotebilds mit dem ausgewählten Domänenmodell. Die Sprache für die Analyse lässt sich ebenfalls bestimmen. |
   |Bild beschreiben | Verwendet den Vorgang [Bild beschreiben](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fe) zum Erstellen einer lesbaren Beschreibung eines lokalen oder Remotebilds. Sie können für die Beschreibung auch die Sprache auswählen. |
   |Tags generieren | Verwendet den Vorgang [Bild markieren](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1ff) zum Markieren visueller Merkmale eines lokalen oder Remotebilds. Die Sprache der Tags lässt sich ebenfalls bestimmen. |
   |Text erkennen (OCR) | Verwendet den Vorgang [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) zum Erkennen und Extrahieren von gedrucktem Text aus einem Bild. Die zu verwendende Sprache kann entweder manuell ausgewählt oder von maschinelles Sehen automatisch erkannt werden. |
   |Text erkennen V2 (Englisch) | Verwendet die Vorgänge [Text erkennen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) und [Ergebnis der Texterkennung abrufen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) zum asynchronen Erkennen und Extrahieren von gedrucktem oder handgeschriebenem Text aus einem Bild. |
   |Miniaturansicht abrufen | Verwendet den Vorgang [Miniaturansicht abrufen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) zum Generieren einer Miniaturansicht für ein lokales oder Remotebild. |

   Der folgende Screenshot zeigt die Seite für das Szenario „Bild analysieren“ nach dem Analysieren eines Beispielbilds.
   ![Screenshot der Seite für die Bildanalyse](../Images/Analyze_Image_Example.PNG)

## <a name="explore-the-sample-app"></a>Untersuchen der Beispiel-App

Visual Studio-Projektmappe für die Beispiel-App für maschinelles Sehen enthält zwei Projekte:

* SampleUserControlLibrary  
  Das Projekt „SampleUserControlLibrary“ bietet Funktionen, die mehrere Cognitive Services-Beispiele verwenden. Das neue Projekt enthält die folgenden Elemente:
  * SampleScenarios  
    Ein UserControl-Element, das eine standardisierte Darstellung, z.B. Titelleiste, Navigations- und Inhaltsbereich, für Beispiele bereitstellt. Die Beispiel-App für maschinelles Sehen verwendet dieses Steuerelement im Fenster „MainWindow.xaml“, um Szenarioseiten anzuzeigen und auf Informationen zuzugreifen, die in mehreren Szenarien verwendet werden, z.B. der Abonnementschlüssel und die Endpunkt-URL.
  * SubscriptionKeyPage  
    Eine Seite, die ein standardisiertes Layout zum Eingeben eines Abonnementschlüssels und einer Endpunkt-URL für die Beispiel-App bietet. Die Beispiel-App für maschinelles Sehen verwendet diese Seite zum Verwalten des Abonnementschlüssels und der Endpunkt-URL, die die Szenarioseiten verwenden.
  * VideoResultControl  
    Ein UserControl-Element, das eine standardisierte Darstellung für Videoinformationen bereitstellt. Die Beispiel-App für maschinelles Sehen verwendet dieses Steuerelement nicht.
* VisionAPI-WPF-Samples  
  Dieses Projekt ist das Hauptprojekt der Beispiel-App für maschinelles Sehen und enthält alle wichtigen Funktionen für maschinelles Sehen. Das neue Projekt enthält die folgenden Elemente:
  * AnalyzeInDomainPage.xaml  
    Die Seite für das Szenario „Bild mit Domänenmodell analysieren“.
  * AnalyzeImage.xaml  
    Die Seite für das Szenario „Bild analysieren“.
  * DescribePage.xaml  
    Die Seite für das Szenario „Bild beschreiben“.
  * ImageScenarioPage.cs  
    Die „ImageScenarioPage“-Klasse, von der alle Szenarioseiten in der Beispiel-App abgeleitet werden. Diese Klasse verwaltet Funktionen, z.B. das Bereitstellen von Anmeldeinformationen und das Formatieren der Ausgabe, die alle Szenarioseiten verwenden.
  * MainWindow.xaml  
    Das Hauptfenster der Beispiel-App. Es verwendet das Steuerelement „SampleScenarios“, um „SubscriptionKeyPage“ und die Szenarioseiten darzustellen.
  * OCRPage.xaml  
    Die Seite für das Szenario „Text erkennen (OCR)“.
  * RecognizeLanguage.cs  
    Die „RecognizeLanguage“-Klasse, die Informationen zu den Sprachen bereitstellt, die die verschiedenen Methoden der Beispiel-App unterstützen.
  * TagsPage.xaml  
    Die Seite für das Szenario „Tags generieren“.
  * TextRecognitionPage.xaml  
    Die Seite für das Szenario „Text erkennen V2 (Englisch)“.
  * ThumbnailPage.xaml  
    Die Seite für das Szenario „Miniaturansicht abrufen“.

### <a name="explore-the-sample-code"></a>Untersuchen des Beispielcodes

Wichtige Teile des Beispielcodes befinden sich zwischen Kommentarblöcken, die mit `KEY SAMPLE CODE STARTS HERE` beginnen und mit `KEY SAMPLE CODE ENDS HERE` enden, um Ihnen das Untersuchen der Beispiel-App zu erleichtern. Diese wichtigen Ausschnitte enthalten den Code, der zum Erlernen der Verwendung der Maschinelles Sehen-API-Clientbibliothek für verschiedene Aufgaben am relevantesten ist. Sie können in Visual Studio nach `KEY SAMPLE CODE STARTS HERE` suchen, um zwischen den wichtigsten Codeabschnitten in der Beispiel-App für maschinelles Sehen zu wechseln. 

Die `UploadAndAnalyzeImageAsync`-Methode, die unten dargestellt und in „AnalyzePage.xaml“ enthalten ist, zeigt beispielsweise, wie Sie mit der Clientbibliothek ein lokales Bild durch Aufrufen der `ComputerVisionClient.AnalyzeImageInStreamAsync`-Methode analysieren.

```csharp
private async Task<ImageAnalysis> UploadAndAnalyzeImageAsync(string imageFilePath)
{
    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE STARTS HERE
    // -----------------------------------------------------------------------

    //
    // Create Cognitive Services Vision API Service client.
    //
    using (var client = new ComputerVisionClient(Credentials) { Endpoint = Endpoint })
    {
        Log("ComputerVisionClient is created");

        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features.
            //
            Log("Calling ComputerVisionClient.AnalyzeImageInStreamAsync()...");
            VisualFeatureTypes[] visualFeatures = GetSelectedVisualFeatures();
            string language = (_language.SelectedItem as RecognizeLanguage).ShortCode;
            ImageAnalysis analysisResult = await client.AnalyzeImageInStreamAsync(imageFileStream, visualFeatures, null, language);
            return analysisResult;
        }
    }

    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE ENDS HERE
    // -----------------------------------------------------------------------
}
```

### <a name="explore-the-client-library"></a>Untersuchen der Clientbibliothek

Diese Beispiel-App nutzt die Clientbibliothek der Maschinelles Sehen-API. Dabei handelt es sich um einen C#-Thin Client-Wrapper für die Maschinelles Sehen-API in Azure Cognitive Services. Die Clientbibliothek wird im NuGet-Paket [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) bereitgestellt. Beim Erstellen der Visual Studio-Anwendung rufen Sie die Clientbibliothek aus dem entsprechenden NuGet-Paket ab. Der Quellcode für die Clientbibliothek wird auch im `/ClientLibrary`-Ordner im `Microsoft/Cognitive-Vision-Windows`-Repository angezeigt.

Die Funktionen der Clientbibliothek konzentrieren sich auf die Klasse `ComputerVisionClient` im Namespace `Microsoft.Azure.CognitiveServices.Vision.ComputerVision`. Die Modelle, die die `ComputerVisionClient`-Klasse beim Interagieren mit der Maschinelles Sehen-API verwendet, befinden sich allerdings im Namespace `Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models`. Auf den verschiedenen XAML-Szenarioseiten der Beispiel-App finden Sie die folgenden `using`-Anweisungen für diese Namespaces:

```csharp
// -----------------------------------------------------------------------
// KEY SAMPLE CODE STARTS HERE
// Use the following namespace for ComputerVisionClient.
// -----------------------------------------------------------------------
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
// -----------------------------------------------------------------------
// KEY SAMPLE CODE ENDS HERE
// -----------------------------------------------------------------------
```

Sie erfahren mehr über die verschiedenen Methoden der `ComputerVisionClient`-Klasse, wenn Sie die Szenarien der Beispiel-App für maschinelles Sehen untersuchen.

## <a name="explore-the-analyze-image-scenario"></a>Untersuchen des Szenarios „Bild analysieren“

Dieses Szenario wird von der Seite „AnalyzePage.xaml“ verwaltet. Sie können die visuellen Merkmale und die Sprache für die Analyse bestimmen und sowohl das Bild als auch die Ergebnisse anzeigen. Dazu verwendet die Szenarioseite je nach Bildquelle eine der folgenden Methoden:

* UploadAndAnalyzeImageAsync  
  Diese Methode wird für lokale Bilder verwendet. Dabei muss das Bild als `Stream` codiert und durch Aufrufen der `ComputerVisionClient.AnalyzeImageInStreamAsync`-Methode an die Maschinelles Sehen-API gesendet werden.
* AnalyzeUrlAsync  
  Diese Methode wird für Remotebilder verwendet. Dabei wird die Bild-URL durch Aufrufen der `ComputerVisionClient.AnalyzeImageAsync`-Methode an die Maschinelles Sehen-API gesendet.

Die `UploadAndAnalyzeImageAsync`-Methode erstellt eine neue `ComputerVisionClient`-Instanz unter Verwendung des angegebenen Abonnementschlüssels und der angegebenen Endpunkt-URL. Da die Beispiel-App ein lokales Bild analysiert, muss sie den Bildinhalt an die Maschinelles Sehen-API senden. Sie öffnet die lokale Datei, die in `imageFilePath` zum Lesen als `Stream` angegeben ist, und ruft dann die auf der Szenarioseite ausgewählten visuellen Merkmale sowie die Sprache aus. Sie ruft die `ComputerVisionClient.AnalyzeImageInStreamAsync`-Methode auf, übergibt `Stream` für die Datei, die visuellen Merkmale und die Sprache und gibt das Ergebnis dann als `ImageAnalysis`-Instanz zurück. Die von der `ImageScenarioPage`-Klasse geerbten Methoden stellen die zurückgegebenen Ergebnisse auf der Szenarioseite dar.

Die `AnalyzeUrlAsync`-Methode erstellt eine neue `ComputerVisionClient`-Instanz unter Verwendung des angegebenen Abonnementschlüssels und der angegebenen Endpunkt-URL. Sie ruft die visuellen Merkmale und die Sprache ab, die auf der Szenarioseite ausgewählt wurden. Die Methode ruft die `ComputerVisionClient.AnalyzeImageInStreamAsync`-Methode auf, übergibt die Bild-URL, die visuellen Merkmale und die Sprache und gibt das Ergebnis dann als `ImageAnalysis`-Instanz zurück. Die von der `ImageScenarioPage`-Klasse geerbten Methoden stellen die zurückgegebenen Ergebnisse auf der Szenarioseite dar.

## <a name="explore-the-analyze-image-with-domain-model-scenario"></a>Untersuchen des Szenarios „Bild mit Domänenmodell analysieren“

Dieses Szenario wird von der Seite „AnalyzeInDomainPage.xaml“ verwaltet. Sie können ein Domänenmodell, z.B. `celebrities` oder `landmarks`, und eine Sprache auswählen, um eine domänenspezifische Bildanalyse auszuführen und sowohl das Bild als auch die Ergebnisse anzuzeigen. Dazu verwendet die Szenarioseite je nach Bildquelle die folgenden Methoden:

* GetAvailableDomainModelsAsync  
  Diese Methode ruft die Liste der verfügbaren Domänenmodelle der Maschinelles Sehen-API ab und füllt das `_domainModelComboBox`-ComboBox-Steuerelement auf der Seite mit der `ComputerVisionClient.ListModelsAsync`-Methode auf.
* UploadAndAnalyzeInDomainImageAsync  
  Diese Methode wird für lokale Bilder verwendet. Dabei muss das Bild als `Stream` codiert und durch Aufrufen der `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync`-Methode an die Maschinelles Sehen-API gesendet werden.
* AnalyzeInDomainUrlAsync  
  Diese Methode wird für Remotebilder verwendet. Dabei wird die Bild-URL durch Aufrufen der `ComputerVisionClient.AnalyzeImageByDomainAsync`-Methode an die Maschinelles Sehen-API gesendet.

Die `UploadAndAnalyzeInDomainImageAsync`-Methode erstellt eine neue `ComputerVisionClient`-Instanz unter Verwendung des angegebenen Abonnementschlüssels und der angegebenen Endpunkt-URL. Da die Beispiel-App ein lokales Bild analysiert, muss sie den Bildinhalt an die Maschinelles Sehen-API senden. Sie öffnet die lokale Datei, die in `imageFilePath` zum Lesen als `Stream` angegeben ist, und ruft die auf der Szenarioseite ausgewählte Sprache aus. Sie ruft die `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync`-Methode auf, übergibt `Stream` für die Datei, den Namen des Domänenmodells und die Sprache und gibt das Ergebnis dann als `DomainModelResults`-Instanz zurück. Die von der `ImageScenarioPage`-Klasse geerbten Methoden stellen die zurückgegebenen Ergebnisse auf der Szenarioseite dar.

Die `AnalyzeInDomainUrlAsync`-Methode erstellt eine neue `ComputerVisionClient`-Instanz unter Verwendung des angegebenen Abonnementschlüssels und der angegebenen Endpunkt-URL. Sie ruft die Sprache ab, die auf der Szenarioseite ausgewählt wurde. Die Methode ruft die `ComputerVisionClient.AnalyzeImageByDomainAsync`-Methode auf, übergibt die Bild-URL, die visuellen Merkmale und die Sprache und gibt das Ergebnis dann als `DomainModelResults`-Instanz zurück. Die von der `ImageScenarioPage`-Klasse geerbten Methoden stellen die zurückgegebenen Ergebnisse auf der Szenarioseite dar.

## <a name="explore-the-describe-image-scenario"></a>Untersuchen des Szenarios „Bild beschreiben“

Dieses Szenario wird von der Seite „DescribePage.xaml“ verwaltet. Sie können eine Sprache auswählen, um eine lesbare Bildbeschreibung zu erstellen und sowohl das Bild als auch die Ergebnisse anzuzeigen. Dazu verwendet die Szenarioseite je nach Bildquelle die folgenden Methoden:

* UploadAndDescribeImageAsync  
  Diese Methode wird für lokale Bilder verwendet. Dabei muss das Bild als `Stream` codiert und durch Aufrufen der `ComputerVisionClient.DescribeImageInStreamAsync`-Methode an die Maschinelles Sehen-API gesendet werden.
* DescribeUrlAsync  
  Diese Methode wird für Remotebilder verwendet. Dabei wird die Bild-URL durch Aufrufen der `ComputerVisionClient.DescribeImageAsync`-Methode an die Maschinelles Sehen-API gesendet.

Die `UploadAndDescribeImageAsync`-Methode erstellt eine neue `ComputerVisionClient`-Instanz unter Verwendung des angegebenen Abonnementschlüssels und der angegebenen Endpunkt-URL. Da die Beispiel-App ein lokales Bild analysiert, muss sie den Bildinhalt an die Maschinelles Sehen-API senden. Sie öffnet die lokale Datei, die in `imageFilePath` zum Lesen als `Stream` angegeben ist, und ruft die auf der Szenarioseite ausgewählte Sprache aus. Sie ruft die `ComputerVisionClient.DescribeImageInStreamAsync`-Methode auf, übergibt `Stream` für die Datei, die maximale Anzahl von Kandidaten (hier 3) und die Sprache und gibt das Ergebnis dann als `ImageDescription`-Instanz zurück. Die von der `ImageScenarioPage`-Klasse geerbten Methoden stellen die zurückgegebenen Ergebnisse auf der Szenarioseite dar.

Die `DescribeUrlAsync`-Methode erstellt eine neue `ComputerVisionClient`-Instanz unter Verwendung des angegebenen Abonnementschlüssels und der angegebenen Endpunkt-URL. Sie ruft die Sprache ab, die auf der Szenarioseite ausgewählt wurde. Sie ruft die `ComputerVisionClient.DescribeImageAsync`-Methode auf, übergibt die Bild-URL, die maximale Anzahl von Kandidaten (hier 3) und die Sprache und gibt das Ergebnis dann als `ImageDescription`-Instanz zurück. Die von der `ImageScenarioPage`-Klasse geerbten Methoden stellen die zurückgegebenen Ergebnisse auf der Szenarioseite dar.

## <a name="explore-the-generate-tags-scenario"></a>Untersuchen des Szenarios „Tags generieren“

Dieses Szenario wird von der Seite „TagsPage.xaml“ verwaltet. Sie können eine Sprache zum Markieren der visuellen Merkmale eines Bilds auswählen und sowohl das Bild als auch die Ergebnisse anzeigen. Dazu verwendet die Szenarioseite je nach Bildquelle die folgenden Methoden:

* UploadAndGetTagsForImageAsync  
  Diese Methode wird für lokale Bilder verwendet. Dabei muss das Bild als `Stream` codiert und durch Aufrufen der `ComputerVisionClient.TagImageInStreamAsync`-Methode an die Maschinelles Sehen-API gesendet werden.
* GenerateTagsForUrlAsync  
  Diese Methode wird für Remotebilder verwendet. Dabei wird die Bild-URL durch Aufrufen der `ComputerVisionClient.TagImageAsync`-Methode an die Maschinelles Sehen-API gesendet.

Die `UploadAndGetTagsForImageAsync`-Methode erstellt eine neue `ComputerVisionClient`-Instanz unter Verwendung des angegebenen Abonnementschlüssels und der angegebenen Endpunkt-URL. Da die Beispiel-App ein lokales Bild analysiert, muss sie den Bildinhalt an die Maschinelles Sehen-API senden. Sie öffnet die lokale Datei, die in `imageFilePath` zum Lesen als `Stream` angegeben ist, und ruft die auf der Szenarioseite ausgewählte Sprache aus. Sie ruft die `ComputerVisionClient.TagImageInStreamAsync`-Methode auf, übergibt `Stream` für die Datei und die Sprache und gibt das Ergebnis dann als `TagResult`-Instanz zurück. Die von der `ImageScenarioPage`-Klasse geerbten Methoden stellen die zurückgegebenen Ergebnisse auf der Szenarioseite dar.

Die `GenerateTagsForUrlAsync`-Methode erstellt eine neue `ComputerVisionClient`-Instanz unter Verwendung des angegebenen Abonnementschlüssels und der angegebenen Endpunkt-URL. Sie ruft die Sprache ab, die auf der Szenarioseite ausgewählt wurde. Sie ruft die `ComputerVisionClient.TagImageAsync`-Methode auf, übergibt die Bild-URL und die Sprache und gibt das Ergebnis dann als `TagResult`-Instanz zurück. Die von der `ImageScenarioPage`-Klasse geerbten Methoden stellen die zurückgegebenen Ergebnisse auf der Szenarioseite dar.

## <a name="explore-the-recognize-text-ocr-scenario"></a>Untersuchen des Szenarios „Text erkennen (OCR)“

Dieses Szenario wird von der Seite „OCRPage.xaml“ verwaltet. Sie können eine Sprache zum Erkennen und Extrahieren von gedrucktem Text aus einem Bild auswählen und sowohl das Bild als auch die Ergebnisse anzeigen. Dazu verwendet die Szenarioseite je nach Bildquelle die folgenden Methoden:

* UploadAndRecognizeImageAsync  
  Diese Methode wird für lokale Bilder verwendet. Dabei muss das Bild als `Stream` codiert und durch Aufrufen der `ComputerVisionClient.RecognizePrintedTextInStreamAsync`-Methode an die Maschinelles Sehen-API gesendet werden.
* RecognizeUrlAsync  
  Diese Methode wird für Remotebilder verwendet. Dabei wird die Bild-URL durch Aufrufen der `ComputerVisionClient.RecognizePrintedTextAsync`-Methode an die Maschinelles Sehen-API gesendet.

Die `UploadAndRecognizeImageAsync`-Methode erstellt eine neue `ComputerVisionClient`-Instanz unter Verwendung des angegebenen Abonnementschlüssels und der angegebenen Endpunkt-URL. Da die Beispiel-App ein lokales Bild analysiert, muss sie den Bildinhalt an die Maschinelles Sehen-API senden. Sie öffnet die lokale Datei, die in `imageFilePath` zum Lesen als `Stream` angegeben ist, und ruft die auf der Szenarioseite ausgewählte Sprache aus. Sie ruft die `ComputerVisionClient.RecognizePrintedTextInStreamAsync`-Methode auf, die angibt, dass die Ausrichtung nicht erkannt wird, und übergibt `Stream` für die Datei sowie die Sprache und gibt das Ergebnis dann als `OcrResult`-Instanz zurück. Die von der `ImageScenarioPage`-Klasse geerbten Methoden stellen die zurückgegebenen Ergebnisse auf der Szenarioseite dar.

Die `RecognizeUrlAsync`-Methode erstellt eine neue `ComputerVisionClient`-Instanz unter Verwendung des angegebenen Abonnementschlüssels und der angegebenen Endpunkt-URL. Sie ruft die Sprache ab, die auf der Szenarioseite ausgewählt wurde. Sie ruft die `ComputerVisionClient.RecognizePrintedTextAsync`-Methode auf, die angibt, dass die Ausrichtung nicht erkannt wird, und übergibt die Bild-URL sowie die Sprache und gibt das Ergebnis dann als `OcrResult`-Instanz zurück. Die von der `ImageScenarioPage`-Klasse geerbten Methoden stellen die zurückgegebenen Ergebnisse auf der Szenarioseite dar.

## <a name="explore-the-recognize-text-v2-english-scenario"></a>Untersuchen des Szenarios „Text erkennen V2 (Englisch)“

Dieses Szenario wird von der Seite „TextRecognitionPage.xaml“ verwaltet. Sie können den Erkennungsmodus und eine Sprache auswählen, um asynchron gedruckten oder handgeschriebenen Text in einem Bild zu erkennen und zu extrahieren und sowohl das Bild als auch die Ergebnisse anzuzeigen. Dazu verwendet die Szenarioseite je nach Bildquelle die folgenden Methoden:

* UploadAndRecognizeImageAsync  
  Diese Methode wird für lokale Bilder verwendet. Dabei muss das Bild als `Stream` codiert sowie durch Aufrufen der `RecognizeAsync`-Methode und Übergeben eines parametrisierten Delegaten für die `ComputerVisionClient.RecognizeTextInStreamAsync`-Methode an die Maschinelles Sehen-API gesendet werden.
* RecognizeUrlAsync  
  Diese Methode wird für Remotebilder verwendet. Dabei wird die Bild-URL durch Aufrufen der `RecognizeAsync`-Methode und Übergeben eines parametrisierten Delegaten für die `ComputerVisionClient.RecognizeTextAsync`-Methode an die Maschinelles Sehen-API gesendet.
* RecognizeAsync: Diese Methode verarbeitet den asynchronen Aufruf für die beiden Methoden `UploadAndRecognizeImageAsync` und `RecognizeUrlAsync` sowie das Abrufen von Ergebnissen durch Aufrufen der `ComputerVisionClient.GetTextOperationResultAsync`-Methode.

Im Gegensatz zu den anderen Szenarien für die Beispiel-App für maschinelles Sehen ist dieses Szenario asynchron. Zum Starten des Prozesses wird eine andere Methode aufgerufen als zum Überprüfen des Status und Zurückgeben der Prozessergebnisse. Der logische Ablauf in diesem Szenario unterscheidet sich von dem in den anderen Szenarien.

Die `UploadAndRecognizeImageAsync`-Methode öffnet die lokale Datei, die in `imageFilePath` zum Lesen als `Stream` angegeben ist, ruft dann die `RecognizeAsync`-Methode auf und übergibt:

* Einen Lambdaausdruck für einen parametrisierten asynchronen Delegaten der `ComputerVisionClient.RecognizeTextInStreamAsync`-Methode, mit `Stream` für die Datei und dem Erkennungsmodus als Parameter in `GetHeadersAsyncFunc`.
* Einen Lambdaausdruck für einen Delegaten zum Abrufen des `Operation-Location`-Antwortheaderwerts in `GetOperationUrlFunc`.

Die `RecognizeUrlAsync`-Methode ruft die `RecognizeAsync`-Methode auf und übergibt dabei:

* Einen Lambdaausdruck für einen parametrisierten asynchronen Delegaten der `ComputerVisionClient.RecognizeTextAsync`-Methode, mit der Remotebild-URL und dem Erkennungsmodus als Parameter in `GetHeadersAsyncFunc`.
* Einen Lambdaausdruck für einen Delegaten zum Abrufen des `Operation-Location`-Antwortheaderwerts in `GetOperationUrlFunc`.

Wenn die `RecognizeAsync` Methode abgeschlossen wurde, geben die beiden Methoden `UploadAndRecognizeImageAsync` und `RecognizeUrlAsync` das Ergebnis als eine `TextOperationResult`-Instanz zurück. Die von der `ImageScenarioPage`-Klasse geerbten Methoden stellen die zurückgegebenen Ergebnisse auf der Szenarioseite dar.

Die `RecognizeAsync`-Methode ruft den parametrierten Delegaten für eine der beiden in `GetHeadersAsyncFunc` übergebenen Methoden `ComputerVisionClient.RecognizeTextInStreamAsync` oder `ComputerVisionClient.RecognizeTextAsync` auf und wartet auf die Antwort. Die Methode ruft dann den in `GetOperationUrlFunc` übergebenen Delegaten auf, um den `Operation-Location`-Antwortheaderwert abzurufen. Dieser Wert ist die URL, mit der die Ergebnisse der in `GetHeadersAsyncFunc` übergebenen Methode aus der Maschinelles Sehen-API abgerufen werden.

Die `RecognizeAsync`-Methode ruft dann die `ComputerVisionClient.GetTextOperationResultAsync`-Methode auf und übergibt die aus dem `Operation-Location`-Antwortheader abgerufene URL, um den Status und das Ergebnis der in `GetHeadersAsyncFunc` übergebenen Methode zu erhalten. Wenn der Status nicht angibt, dass die Methode abgeschlossen wurde (erfolgreich oder erfolglos) ruft die `RecognizeAsync`-Methode `ComputerVisionClient.GetTextOperationResultAsync` 3 weitere Male auf und wartet 3 Sekunden zwischen den Aufrufen. Die `RecognizeAsync`-Methode gibt die Ergebnisse an die aufrufende Methode zurück.

## <a name="explore-the-get-thumbnail-scenario"></a>Untersuchen des Szenarios „Miniaturansicht abrufen“

Dieses Szenario wird von der Seite „ThumbnailPage.xaml“ verwaltet. Sie können angeben, ob Sie intelligentes Zuschneiden verwenden möchten, und die gewünschte Höhe und Breite festlegen, um aus einem Bild eine Miniaturansicht zu erzeugen, und sowohl das Bild als auch die Ergebnisse anzeigen. Dazu verwendet die Szenarioseite je nach Bildquelle die folgenden Methoden:

* UploadAndThumbnailImageAsync  
  Diese Methode wird für lokale Bilder verwendet. Dabei muss das Bild als `Stream` codiert und durch Aufrufen der `ComputerVisionClient.GenerateThumbnailInStreamAsync`-Methode an die Maschinelles Sehen-API gesendet werden.
* ThumbnailUrlAsync  
  Diese Methode wird für Remotebilder verwendet. Dabei wird die Bild-URL durch Aufrufen der `ComputerVisionClient.GenerateThumbnailAsync`-Methode an die Maschinelles Sehen-API gesendet.

Die `UploadAndThumbnailImageAsync`-Methode erstellt eine neue `ComputerVisionClient`-Instanz unter Verwendung des angegebenen Abonnementschlüssels und der angegebenen Endpunkt-URL. Da die Beispiel-App ein lokales Bild analysiert, muss sie den Bildinhalt an die Maschinelles Sehen-API senden. Sie öffnet die lokale Datei, die in `imageFilePath` zum Lesen als `Stream` angegeben ist. Sie ruft die `ComputerVisionClient.GenerateThumbnailInStreamAsync`-Methode auf, übergibt Breite, Höhe, `Stream` für die Datei sowie die Information, ob intelligentes Zuschneiden verwendet wird, und gibt das Ergebnis dann als `Stream` zurück. Die von der `ImageScenarioPage`-Klasse geerbten Methoden stellen die zurückgegebenen Ergebnisse auf der Szenarioseite dar.

Die `RecognizeUrlAsync`-Methode erstellt eine neue `ComputerVisionClient`-Instanz unter Verwendung des angegebenen Abonnementschlüssels und der angegebenen Endpunkt-URL. Sie ruft die `ComputerVisionClient.GenerateThumbnailAsync`-Methode auf, übergibt Breite, Höhe, Bild-URL sowie die Information, ob intelligentes Zuschneiden verwendet wird, und gibt das Ergebnis dann als `Stream` zurück. Die von der `ImageScenarioPage`-Klasse geerbten Methoden stellen die zurückgegebenen Ergebnisse auf der Szenarioseite dar.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie den Ordner, in dem sich der Klon des `Microsoft/Cognitive-Vision-Windows`-Repositorys befindet, wenn Sie ihn nicht mehr benötigen. Wenn Sie Beispielbilder verwendet haben, löschen Sie auch den Ordner, der den Klon des `Microsoft/Cognitive-Face-Windows`-Repositorys enthält.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erste Schritte mit dem Gesichtserkennungsdienst](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)

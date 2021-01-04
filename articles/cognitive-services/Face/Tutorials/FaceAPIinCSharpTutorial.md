---
title: 'Tutorial: Erkennen und Anzeigen von Gesichtserkennungsdaten in einem Bild mit dem .NET SDK'
titleSuffix: Azure Cognitive Services
description: In diesem Tutorial erstellen Sie eine Windows-App, die den Gesichtserkennungsdienst verwendet, um Gesichter in einem Bild zu erkennen und zu umranden.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: ab251e9a156005d47a1516d23cb018fb1907ab6e
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95744771"
---
# <a name="tutorial-create-a-windows-presentation-framework-wpf-app-to-display-face-data-in-an-image"></a>Tutorial: Erstellen einer WPF-App (Windows Presentation Framework) zum Anzeigen von Gesichtserkennungsdaten in einem Bild

In diesem Tutorial erfahren Sie, wie sie den Azure-Gesichtserkennungsdienst über das .NET Client SDK verwenden, um Gesichter in einem Bild zu erkennen und diese Daten anschließend auf der Benutzeroberfläche darzustellen. Sie erstellen eine WPF-Anwendung, die Gesichter erkennt, einen Rahmen um jedes Gesicht zeichnet und eine Beschreibung des Gesichts auf der Statusleiste anzeigt. 

Dieses Tutorial veranschaulicht folgende Vorgehensweisen:

> [!div class="checklist"]
> - Erstellen einer WPF-Anwendung
> - Installieren der Clientbibliothek für die Gesichtserkennung
> - Verwenden der Clientbibliothek zum Erkennen von Gesichtern in einem Bild
> - Zeichnen eines Rahmens um jedes erkannte Gesicht
> - Anzeigen einer Beschreibung des hervorgehobenen Gesichts auf der Statusleiste

![Screenshot: Erkannte Gesichter mit Rechtecken als Umrandung](../Images/getting-started-cs-detected.png)

Der vollständige Beispielcode ist im Repository [Cognitive Face CSharp sample](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample) (CSharp-Beispiel für die Gesichtserkennung) auf GitHub verfügbar.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/) erstellen, bevor Sie beginnen. 


## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)
* Sobald Sie über Ihr Azure-Abonnement verfügen, erstellen Sie über <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Erstellen einer Gesichtserkennungsressource"  target="_blank"> eine Gesichtserkennungsressource <span class="docon docon-navigate-external x-hidden-focus"></span></a> im Azure-Portal, um Ihren Schlüssel und Endpunkt zu erhalten. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Gesichtserkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.
* [Erstellen Sie Umgebungsvariablen](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) für die Schlüssel- und Dienstendpunkt-Zeichenfolge, und nennen Sie sie `FACE_SUBSCRIPTION_KEY` bzw. `FACE_ENDPOINT`.
- Eine beliebige Edition von [Visual Studio](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Erstellen des Visual Studio-Projekts

Führen Sie diese Schritte aus, um ein neues WPF-Anwendungsprojekt zu erstellen.

1. Öffnen Sie in Visual Studio das Dialogfeld „Neues Projekt“. Erweitern Sie **Installiert** und dann **Visual C#** , und wählen Sie **WPF-App (.NET Framework)** aus.
1. Geben Sie der Anwendung den Namen **FaceTutorial**, und klicken Sie anschließend auf **OK**.
1. Rufen Sie die erforderlichen NuGet-Pakete ab. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, wählen Sie **NuGet-Pakete verwalten** aus, suchen Sie nach den folgenden Paketen, und installieren Sie sie:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.6.0-preview.1](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.6.0-preview.1)

## <a name="add-the-initial-code"></a>Hinzufügen des anfänglichen Codes

In diesem Abschnitt fügen Sie das einfache Framework der App ohne spezifische Features für die Gesichtserkennung hinzu.

### <a name="create-the-ui"></a>Erstellen der Benutzeroberfläche

Öffnen Sie *MainWindow.xaml*, und ersetzen Sie den Inhalt durch den folgenden Code. Dieser Code erstellt das Fenster für die Benutzeroberfläche. Die Methoden `FacePhoto_MouseMove` und `BrowseButton_Click` sind Ereignishandler, die Sie später definieren.

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?name=snippet_xaml)]

### <a name="create-the-main-class"></a>Erstellen der Hauptklasse

Öffnen Sie *MainWindow.xaml.cs*, und fügen Sie die Namespaces für die Clientbibliothek sowie weitere erforderliche Namespaces hinzu. 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_using)]

Fügen Sie als Nächstes den folgenden Code in die **MainWindow**-Klasse ein. Dieser Code erstellt eine **FaceClient**-Instanz, die den Abonnementschlüssel und den Endpunkt verwendet.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_fields)]

Fügen Sie als Nächstes den Konstruktor **MainWindow** hinzu. Er überprüft die Endpunkt-URL-Zeichenfolge und ordnet sie dem Clientobjekt zu.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_constructor)]

Fügen Sie abschließend die Methoden **BrowseButton_Click** und **FacePhoto_MouseMove** zur Klasse hinzu. Diese Methoden entsprechen den Ereignishandlern, die in *MainWindow.xaml* deklariert wurden. Die Methode **BrowseButton_Click** erstellt ein **OpenFileDialog**-Element, über das der Benutzer ein JPG-Bild auswählen kann. Anschließend wird das Bild im Hauptfenster angezeigt. Sie fügen den übrigen Code für **BrowseButton_Click** und **FacePhoto_MouseMove** später ein. Beachten Sie außerdem den Verweis `faceList`: eine Liste der **DetectedFace**-Objekte. In diesem Verweis werden die tatsächlichen Gesichtserkennungsdaten von Ihrer App gespeichert und aufgerufen.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_end)] -->

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_end)] -->

### <a name="try-the-app"></a>Testen der App

Wählen Sie im Menü die Option **Start**, um Ihre App zu testen. Klicken Sie nach dem Öffnen des App-Fensters unten links auf **Durchsuchen**. Das Dialogfeld **File Open** (Datei öffnen) sollte angezeigt werden. Wählen Sie ein Bild in Ihrem Dateisystem aus, und vergewissern Sie sich, dass es im Fenster angezeigt wird. Schließen Sie dann die App, und fahren Sie mit dem nächsten Schritt fort.

![Screenshot: Anzeige eines unbearbeiteten Bilds mit Gesichtern](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>Hochladen eines Bilds und Erkennen von Gesichtern

Ihre App erkennt Gesichter durch Aufrufen der Methode **FaceClient.Face.DetectWithStreamAsync**, die die [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)-REST-API zum Hochladen eines lokalen Bilds umschließt.

Fügen Sie die folgende Methode in die **MainWindow**-Klasse unterhalb der Methode **FacePhoto_MouseMove** ein. Diese Methode definiert eine Liste mit abzurufenden Gesichtsattributen und liest die übermittelte Bilddatei in einen **Datenstrom** ein. Anschließend werden diese beiden Objekte an den **DetectWithStreamAsync**-Methodenaufruf übergeben.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_uploaddetect)]

## <a name="draw-rectangles-around-faces"></a>Zeichnen von Rechtecken um Gesichter

Im nächsten Schritt fügen Sie den Code hinzu, mit dem um jedes erkannte Gesicht im Bild ein Rechteck gezeichnet wird. Fügen Sie in der **MainWindow**-Klasse den folgenden Code am Ende der Methode **BrowseButton_Click** nach der Zeile `FacePhoto.Source = bitmapSource` ein. Dieser Code füllt über den Aufruf von **UploadAndDetectFaces** eine Liste der erkannten Gesichter auf. Als Nächstes wird um jedes Gesicht ein Rechteck gezeichnet, und das geänderte Bild wird im Hauptfenster angezeigt.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_mid)]

## <a name="describe-the-faces"></a>Beschreibung der Gesichter

Fügen Sie die folgende Methode zur **MainWindow**-Klasse unterhalb der **UploadAndDetectFaces**-Methode ein. Diese Methode konvertiert die abgerufenen Gesichtsattribute in eine Zeichenfolge zur Beschreibung des Gesichts.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_facedesc)]

## <a name="display-the-face-description"></a>Anzeigen der Gesichtsbeschreibung

Fügen Sie den folgenden Code zur Methode **FacePhoto_MouseMove** hinzu. Dieser Ereignishandler zeigt die Zeichenfolge für die Gesichtsbeschreibung in `faceDescriptionStatusBar` an, wenn Sie mit dem Cursor auf das Rechteck für das erkannte Gesicht zeigen.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_mid)]

## <a name="run-the-app"></a>Ausführen der App

Führen Sie die Anwendung aus, und suchen Sie nach einem Bild mit einem Gesicht. Warten Sie einige Sekunden, damit der Gesichtserkennungsdienst reagieren kann. Um jedes Gesicht im Bild sollte ein rotes Rechteck angezeigt werden. Wenn Sie den Mauszeiger auf das Rechteck für ein Gesicht bewegen, sollte die Beschreibung dieses Gesichts auf der Statusleiste angezeigt werden.

![Screenshot: Erkannte Gesichter mit Rechtecken als Umrandung](../Images/getting-started-cs-detected.png)


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde der grundlegende Prozess für die Verwendung des .NET SDK des Gesichtserkennungsdiensts beschrieben und eine Anwendung zum Erkennen und Umranden von Gesichtern in einem Bild erstellt. Machen Sie sich als Nächstes mit den Details zur Gesichtserkennung vertraut.

> [!div class="nextstepaction"]
> [Gewusst wie: Gesichtserkennung in Bildern](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
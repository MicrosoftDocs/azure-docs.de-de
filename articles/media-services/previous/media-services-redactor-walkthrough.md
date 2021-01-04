---
title: 'Exemplarische Vorgehensweise: Bearbeiten von Gesichtern mit Azure Media Analytics| Microsoft Docs'
description: In diesem Thema werden Schritt-für-Schritt-Anweisungen zur Ausführung eines vollständigen Bearbeitungsworkflows mithilfe von Azure Media Services Explorer (AMSE) und Azure Media Redactor Visualizer (Open Source-Tool) vorgestellt.
services: media-services
documentationcenter: ''
author: Lichard
manager: femila
editor: ''
ms.assetid: d6fa21b8-d80a-41b7-80c1-ff1761bc68f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: ril
ms.reviewer: juliako
ms.openlocfilehash: 1a106874277f64a006584f9deb98fb9729263b1b
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040715"
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Exemplarische Vorgehensweise: Bearbeiten von Gesichtern mit Azure Media Analytics

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Übersicht

**Azure Media Redactor** ist ein [Azure Media Analytics](./legacy-components.md)-Medienprozessor (MP), der eine skalierbare Gesichtsbearbeitung in der Cloud ermöglicht. Mit der Gesichtsbearbeitung können Sie Ihr Video ändern, um Gesichter von ausgewählten Personen unscharf anzuzeigen und so unkenntlich zu machen. Es kann beispielsweise sein, dass Sie den Gesichtsbearbeitungsdienst nutzen möchten, wenn es um die öffentliche Sicherheit oder Medienarbeit geht. Die Bearbeitung von Material mit einer Länge von einigen Minuten, das mehrere Gesichter enthält, kann bei manueller Vorgehensweise Stunden dauern. Mit diesem Dienst sind für den Prozess der Gesichtsbearbeitung aber nur einige einfache Schritte erforderlich. Weitere Informationen finden Sie in [diesem](https://azure.microsoft.com/blog/azure-media-redactor/) Blog.

Weitere Informationen zu **Azure Media Redactor** finden Sie im Thema [Gesichtsbearbeitung mit Azure Media Analytics](media-services-face-redaction.md).

In diesem Thema werden Schritt-für-Schritt-Anweisungen zur Ausführung eines vollständigen Bearbeitungsworkflows mithilfe von Azure Media Services Explorer (AMSE) und Azure Media Redactor Visualizer (Open Source-Tool) vorgestellt.

Weitere Informationen finden Sie in [diesem](https://azure.microsoft.com/blog/redaction-preview-available-globally) Blog.

## <a name="azure-media-services-explorer-workflow"></a>Workflow im Azure Media Services Explorer

Die einfachste Einstiegsmöglichkeit in Redactor ist die Verwendung des Open Source-Tools AMSE auf GitHub. Sie können einen vereinfachten Workflow in einem **kombinierten** Modus ausführen, wenn Sie keinen Zugriff auf die Anmerkungs-JSON oder JPG-Gesichtsbilder benötigen.

### <a name="download-and-setup"></a>Herunterladen und Einrichten

1. Laden Sie das AMSE für AMS v2-Tool [hier](https://aka.ms/amseforv2) herunter.
1. Melden Sie sich mit Ihrem Dienstschlüssel bei Ihrem Media Services-Konto an.

    Um den Kontonamen und wichtige Informationen zu erhalten, wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihr AMS-Konto aus. Wählen Sie dann „Einstellungen“ > „Schlüssel“ aus. Im Fenster „Schlüssel verwalten“ werden der Kontoname sowie der Primär- und Sekundärschlüssel angezeigt. Kopieren Sie die Werte für den Kontonamen und den Primärschlüssel.

![Screenshot: Zeigt, wo Sie in Microsoft Azure Media Services Ihren Kontonamen und -schlüssel eingeben können](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>Erster Durchgang – Analysemodus

1. Laden Sie Ihre Mediendatei über „Ressource“ > „Hochladen“ oder per Drag & Drop hoch. 
1. Klicken Sie mit der rechten Maustaste, und verarbeiten Sie die Mediendatei mit „Media Analytics“ > „Azure Media Redactor“ > „Analysemodus“. 


![Screenshot: Menü mit Option zum Verarbeiten von Medienobjekten mit Azure Media Redactor](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Screenshot: Azure Media Redactor mit ausgewählter Option „First Pass: Analyze mode“ (Erster Durchgang: Analysemodus)](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

Die Ausgabe umfasst sowohl eine Anmerkungen-JSON-Datei mit Gesichtsspeicherort-Daten als auch eine JPG-Datei jedes erkannten Gesichts. 

![Screenshot: Ausgabe der Analyse](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>Zweiter Durchgang – Redact-Modus

1. Laden Sie Ihre ursprüngliche Videoressource zur Ausgabe des ersten Durchgangs hoch, und legen Sie sie als primäre Ressource fest. 

    ![Screenshot: Schaltflächen „Hochladen“ und „Als primär festlegen“](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. (optional) Laden Sie eine die „Dance_idlist.txt“ hoch, die eine durch Zeilenvorschübe getrennte Liste der IDs enthält, die Sie bearbeiten möchten. 

    ![Screenshot: Option zum Hochladen der Textdatei](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. (Optional) Nehmen Sie Änderungen an der annotations.json-Datei vor, verstärken Sie z.B. die Rahmen, die den Begrenzungsrahmen bilden. 
4. Klicken Sie mit der rechten Maustaste auf die im ersten Durchgang ausgegebene Ressource, wählen Sie den Redactor aus, und führen Sie den **Redact** -Modus aus. 

    ![Screenshot: Azure Media Redactor mit ausgewählter Option „Second Pass: Redact mode“ (Zweiter Durchgang: Bearbeitungsmodus)](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Laden Sie die endgültig bearbeitete, ausgegebene Ressource herunter, oder geben Sie sie frei. 

    ![Screenshot: Schaltfläche „Herunterladen“](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Open Source-Tool Azure Media Redactor Visualizer

Ein Open Source-[Visualizer-Tool](https://github.com/Microsoft/azure-media-redactor-visualizer) soll Entwicklern, die gerade in das Anmerkungenformat einsteigen, Analyse und Verwendung der Ausgabe erleichtern.

Nachdem Sie das Repository geklont haben, müssen Sie zur Ausführung des Projekts FFMPEG von der [offiziellen Website](https://ffmpeg.org/download.html) herunterladen.

Wenn Sie als Entwickler versuchen, die JSON-Anmerkungsdaten zu analysieren, suchen Sie in Models.MetaData nach Codebeispielen.

### <a name="set-up-the-tool"></a>Einrichten des Tools

1.  Laden Sie die gesamte Lösung herunter, und installieren Sie sie. 

    ![Screenshot: Ausgewählte Menüoption zum Erstellen der Lösung](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  Laden Sie FFMPEG [hier](https://ffmpeg.org/download.html) herunter. Dieses Projekt wurde ursprünglich mit Version be1d324 (2016-10-04) mit statischer Verknüpfung entwickelt. 
3.  Kopieren Sie „ffmpeg.exe“ und „ffprobe.exe“ in den gleichen Ausgabeordner wie „AzureMediaRedactor.exe“. 

    ![Screenshot: Inhalt des Ordners, einschließlich „ffmpeg“ und „ffprobe“](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. Führen Sie „AzureMediaRedactor.exe“ aus. 

### <a name="use-the-tool"></a>Verwenden des Tools

1. Verarbeiten Sie Ihr Video in Ihrem Azure Media Services-Konto mit dem Redactor MP im Analysemodus. 
2. Laden Sie sowohl die ursprüngliche Videodatei als auch die Ausgabe des Redact-/Analyseauftrags herunter. 
3. Führen Sie die Visualizeranwendung aus, und wählen Sie die oben aufgeführten Dateien aus. 

    ![Screenshot: Azure Media Redactor beim Hochladen von Dateien](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. Zeigen Sie Ihre Datei in der Vorschau an. Wählen Sie in der rechten Seitenleiste die Gesichter aus, die Sie verschwommen darstellen möchten. 
    
    ![Screenshot: Zeigt, wo Sie in Azure Media Redactor eine Vorschau anzeigen und Gesichter auswählen können, die unkenntlich gemacht werden sollen](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  Im Textfeld unten werden die IDs der Gesichter aktualisiert. Erstellen Sie eine Datei namens „idlist.txt“ mit diesen IDs als durch Zeilenvorschübe getrennte Liste. 

    >[!NOTE]
    > Die Datei „idlist.txt“ muss im ANSI-Format gespeichert werden. Sie können die Datei in Editor im ANSI-Format speichern.
    
6.  Laden Sie diese Datei in die Ausgaberessource aus Schritt 1 hoch. Laden Sie das ursprüngliche Video ebenfalls in diese Ressource hoch, und legen Sie es als primäre Ressource fest. 
7.  Führen Sie die Bearbeitung dieser Ressource im „Redact“-Modus aus, um das abschließend bearbeitete Video zu erhalten. 

## <a name="next-steps"></a>Nächste Schritte 

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Verwandte Links
[Azure Media Services Analytics – Übersicht](./legacy-components.md)

[Azure Media Analytics-Demos](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

[Ankündigung der Gesichtsbearbeitung mit Azure Media Analytics](https://azure.microsoft.com/blog/azure-media-redactor/)
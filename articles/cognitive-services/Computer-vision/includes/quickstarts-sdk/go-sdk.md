---
title: 'Schnellstart: Clientbibliothek für maschinelles Sehen für Go'
titleSuffix: Azure Cognitive Services
description: Beginnen Sie mit dieser Schnellstartanleitung mit dem Einsatz der Clientbibliothek für maschinelles Sehen für Go.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: df171b74014179c36466a2683a8d0d7c0c3b5e90
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560881"
---
<a name="HOLTop"></a>

Verwenden Sie die Clientbibliothek für maschinelles Sehen für Folgendes:

* Analysieren eines Bilds auf Tags, Textbeschreibungen, Gesichter, nicht jugendfreie Inhalte usw.
* Lesen von gedrucktem und handschriftlichem Text mit der Lese-API

[Referenzdokumentation](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision) | [Paket](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement: [Kostenloses Azure-Konto](https://azure.microsoft.com/free/cognitive-services/)
* Aktuelle Version von [Go](https://golang.org/dl/)
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie über <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Erstellen einer Ressource für maschinelles Sehen"  target="_blank"> im Azure-Portal eine Ressource für maschinelles Sehen <span class="docon docon-navigate-external x-hidden-focus"></span></a> erstellen, um Ihren Schlüssel und Endpunkt abzurufen. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um eine Verbindung Ihrer Anwendung mit dem Dienst für maschinelles Sehen herzustellen. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.
* [Erstellen Sie Umgebungsvariablen](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) für den Schlüssel und die Endpunkt-URL, die Sie `COMPUTER_VISION_SUBSCRIPTION_KEY` bzw. `COMPUTER_VISION_ENDPOINT` benennen.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-go-project-directory"></a>Erstellen eines Go-Projektverzeichnisses

Erstellen Sie in einem Konsolenfenster (cmd, PowerShell, Terminal, Bash) einen neuen Arbeitsbereich für Ihr Go-Projekt namens `my-app`, und navigieren Sie dorthin.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Ihr Arbeitsbereich enthält drei Ordner:

* **src**: Das Verzeichnis für Quellcode und Pakete. Alle mit dem Befehl `go get` installierten Pakete werden in diesem Verzeichnis platziert.
* **pkg**: Das Verzeichnis für die kompilierten Go-Paketobjekte. Diese Dateien haben alle die Erweiterung `.a`.
* **bin**: Das Verzeichnis für die ausführbaren Binärdateien, die bei der Ausführung von `go install` erstellt werden.

> [!TIP]
> Weitere Informationen zur Struktur eines Go-Arbeitsbereichs finden Sie in der [Dokumentation zur Go-Sprache](https://golang.org/doc/code.html#Workspaces). Dieses Handbuch enthält Informationen zum Festlegen von `$GOPATH` und `$GOROOT`.

### <a name="install-the-client-library-for-go"></a>Installieren der Clientbibliothek für Go

Installieren Sie als Nächstes die Clientbibliothek für Go:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

Bei Verwendung von DEP können Sie alternativ Folgendes in Ihrem Repository ausführen:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Erstellen einer Go-Anwendung 

Erstellen Sie als Nächstes im Verzeichnis **src** eine Datei mit dem Namen `sample-app.go`:

```bash
cd src
touch sample-app.go
```

Öffnen Sie `sample-app.go` in Ihrer bevorzugten IDE oder in Ihrem bevorzugten Text-Editor. Fügen Sie dann den Paketnamen hinzu, und importieren Sie die folgenden Bibliotheken:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports)]

Deklarieren Sie außerdem einen Kontext am Skriptstamm. Dieses Objekt wird zum Ausführen der meisten Funktionsaufrufe für maschinelles Sehen benötigt:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_context)]

Beginnen Sie nun damit, Code für verschiedene Vorgänge für maschinelles Sehen hinzuzufügen.

> [!div class="nextstepaction"]
> [Ich habe den Client eingerichtet.](?success=set-up-client#object-model) [Bei mir ist ein Problem aufgetreten.](https://www.research.net/r/7QYZKHL?issue=set-up-client)

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen behandeln einige der Hauptfeatures des Computer Vision Go SDK:

|Name|BESCHREIBUNG|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Diese Klasse wird für alle Funktionen für maschinelles Sehen benötigt, also beispielsweise für die Bildanalyse und das Lesen von Text. Sie instanziieren sie mit Ihren Abonnementinformationen und verwenden sie für die meisten Bildvorgänge.|
|[ImageAnalysis](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| Dieser Typ enthält die Ergebnisse eines Funktionsaufrufs vom Typ **AnalyzeImage**. Für die einzelnen kategoriespezifischen Funktionen stehen ähnliche Typen zur Verfügung.|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| Dieser Typ enthält die Ergebnisse eines Batchlesevorgangs. |
|[VisualFeatureTypes](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| Dieser Typ definiert die verschiedenen Arten der Bildanalyse, die bei einem Standardanalysevorgang ausgeführt werden können. Sie geben abhängig von Ihren Anforderungen verschiedene VisualFeatureTypes-Werte an. |

## <a name="code-examples"></a>Codebeispiele

In den Codeausschnitten weiter unten wird gezeigt, wie die folgenden Aufgaben mit der Clientbibliothek für maschinelles Sehen für Go ausgeführt werden:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Analysieren eines Bilds](#analyze-an-image)
* [Lesen von gedrucktem und handschriftlichem Text](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

> [!NOTE]
> In diesem Schritt wird vorausgesetzt, dass Sie für den Schlüssel und den Endpunkt für maschinelles Sehen (`COMPUTER_VISION_SUBSCRIPTION_KEY` bzw. `COMPUTER_VISION_ENDPOINT`) [Umgebungsvariablen erstellt](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) haben.

Erstellen Sie eine Funktion vom Typ `main`, und fügen Sie ihr den folgenden Code hinzu, um einen Client mit Ihrem Endpunkt und Schlüssel zu instanziieren:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> [Ich habe den Client authentifiziert.](?success=authenticate-client#analyze-an-image) [Bei mir ist ein Problem aufgetreten.](https://www.research.net/r/7QYZKHL?issue=authenticate-client)

## <a name="analyze-an-image"></a>Analysieren von Bildern

Im folgenden Code wird das Clientobjekt verwendet, um ein Remotebild zu analysieren und die Ergebnisse in der Konsole auszugeben. Sie können eine Textbeschreibung, eine Kategorisierung, eine Liste mit Tags, erkannte Objekte, erkannte Marken, erkannte Gesichter, Flags für nicht jugendfreie Inhalte, Hauptfarben und den Bildtyp abrufen.

### <a name="set-up-test-image"></a>Einrichten eines Testbilds

Speichern Sie zunächst einen Verweis auf die URL des Bilds, das Sie analysieren möchten. Fügen Sie diesen in die Funktion `main` ein.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_url)]

> [!TIP]
> Sie können auch ein lokales Bild analysieren. Sehen Sie sich die [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient)-Methoden an, etwa **DescribeImageInStream**. Alternativ finden Sie im Beispielcode auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) Szenarien zu lokalen Bildern.

### <a name="specify-visual-features"></a>Angeben visueller Merkmale

Die folgenden Funktionsaufrufe extrahieren verschiedene visuelle Features aus dem Beispielbild. Diese Funktionen werden in den folgenden Abschnitten definiert.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>Abrufen der Bildbeschreibung

Die folgende Funktion ruft die Liste generierter Beschriftungen für das Bild ab. Weitere Informationen zu Bildbeschreibungen finden Sie unter [Beschreiben von Bildern in lesbarer Sprache](../../concept-describing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>Abrufen der Bildkategorie

Die folgende Funktion ruft die erkannte Kategorie des Bilds ab. Weitere Informationen finden Sie unter [Kategorisieren von Bildern nach Schlüsselinhalt](../../concept-categorizing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>Abrufen von Bildtags

Die folgende Funktion ruft die erkannten Tags für das Bild ab. Weitere Informationen finden Sie unter [Anwenden von Inhaltstags auf Bilder](../../concept-tagging-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>Erkennen von Objekten

Die folgende Funktion erkennt alltägliche Objekte im Bild und gibt sie in der Konsole aus. Weitere Informationen finden Sie unter [Erkennen von alltäglichen Objekten in Bildern](../../concept-object-detection.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>Erkennen von Marken

Der folgende Code erkennt Marken und Firmenlogos im Bild und gibt sie in der Konsole aus. Weitere Informationen finden Sie unter [Erkennen von bekannten Marken in Bildern](../../concept-brand-detection.md).

Deklarieren Sie zunächst innerhalb der Funktion `main` einen Verweis auf ein neues Bild.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brand_url)]

Im folgenden Code wird die Markenerkennungsfunktion definiert:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>Erkennen von Gesichtern

Die folgende Funktion gibt die im Bild erkannten Gesichter mit den jeweiligen Rechteckkoordinaten und bestimmten Gesichtsattributen zurück. Weitere Informationen finden Sie unter [Gesichtserkennung mit maschinellem Sehen](../../concept-detecting-faces.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Erkennen von nicht jugendfreien, freizügigen oder gewaltverherrlichenden Inhalten

Die folgende Funktion gibt an, ob im Bild nicht jugendfreie Inhalte erkannt wurden. Weitere Informationen finden Sie unter [Erkennen nicht jugendfreier Inhalte](../../concept-detecting-adult-content.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Abrufen des Farbschemas für ein Bild

Die folgende Funktion gibt die im Bild erkannten Farbattribute aus (beispielsweise die vorherrschenden Farben und die Akzentfarbe). Weitere Informationen finden Sie unter [Erkennen von Farbschemas auf Bildern](../../concept-detecting-color-schemes.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Abrufen des domänenspezifischen Inhalts

Maschinelles Sehen kann spezialisierte Modelle zur weiteren Analyse von Bildern verwenden. Weitere Informationen finden Sie unter [Erkennen domänenspezifischer Inhalte](../../concept-detecting-domain-content.md). 

Der folgende Code analysiert Daten zu erkannten Prominenten im Bild:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_celebs)]

Der folgende Code analysiert Daten zu erkannten Sehenswürdigkeiten im Bild:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Abrufen des Bildtyps

Die folgende Funktion gibt Informationen zur Art des Bilds (ClipArt oder Strichzeichnung) aus:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_type)]

> [!div class="nextstepaction"]
> [Ich habe ein Bild analysiert.](?success=analyze-image#read-printed-and-handwritten-text) [Bei mir ist ein Problem aufgetreten.](https://www.research.net/r/7QYZKHL?issue=analyze-image)

## <a name="read-printed-and-handwritten-text"></a>Lesen von gedrucktem und handschriftlichem Text

Maschinelles Sehen kann sichtbaren Text in einem Bild lesen und in eine Zeichenfolge konvertieren. Der Code in diesem Abschnitt definiert die Funktion `RecognizeTextReadAPIRemoteImage`. Diese verwendet das Clientobjekt, um gedruckten oder handgeschriebenen Text im Bild zu erkennen und zu extrahieren.

Fügen Sie der Funktion `main` den Beispielbildverweis und den Funktionsaufruf hinzu.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!TIP]
> Sie können auch Text aus einem lokalen Bild extrahieren. Sehen Sie sich die [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient)-Methoden an, etwa **BatchReadFileInStream**. Alternativ finden Sie im Beispielcode auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) Szenarien zu lokalen Bildern.

### <a name="call-the-read-api"></a>Aufrufen der Lese-API

Definieren Sie die neue Funktion zum Lesen von Text (`RecognizeTextReadAPIRemoteImage`). Fügen Sie den folgenden Code hinzu. Der Code ruft die Methode **BatchReadFile** für das angegebene Bild auf. Diese Methode gibt eine Vorgangs-ID zurück und startet einen asynchronen Prozess, um den Inhalt des Bilds zu lesen.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>Abrufen der Leseergebnisse

Rufen Sie als Nächstes die Vorgangs-ID ab, die durch Aufrufen von **BatchReadFile** zurückgegeben wurde, und verwenden Sie sie mit der Methode **GetReadOperationResult**, um die Vorgangsergebnisse vom Dienst abzufragen. Der folgende Code überprüft den Vorgang in Intervallen von einer Sekunde, bis die Ergebnisse zurückgegeben werden. Anschließend werden die extrahierten Textdaten in der Konsole ausgegeben.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>Anzeigen der Leseergebnisse

Fügen Sie den folgenden Code hinzu, um die abgerufenen Textdaten zu analysieren und anzuzeigen, und schließen Sie die Funktionsdefinition ab:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

> [!div class="nextstepaction"]
> [Ich habe Text gelesen.](?success=read-printed-handwritten-text#run-the-application) [Bei mir ist ein Problem aufgetreten.](https://www.research.net/r/7QYZKHL?issue=read-printed-handwritten-text)

## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung mit dem Befehl `go run` aus dem Anwendungsverzeichnis aus.

```bash
go run sample-app.go
```

> [!div class="nextstepaction"]
> [Ich habe die Anwendung ausgeführt.](?success=run-the-application#clean-up-resources) [Bei mir ist ein Problem aufgetreten.](https://www.research.net/r/7QYZKHL?issue=run-the-application)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-Befehlszeilenschnittstelle](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Ich habe die Ressourcen bereinigt.](?success=clean-up-resources#next-steps) [Bei mir ist ein Problem aufgetreten.](https://www.research.net/r/7QYZKHL?issue=clean-up-resources)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Referenz zur Maschinelles Sehen-API (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)


* [Worum handelt es sich bei maschinellem Sehen?](../../overview.md)
* Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).
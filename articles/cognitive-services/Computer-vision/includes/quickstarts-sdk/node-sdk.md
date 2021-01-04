---
title: 'Schnellstart: Clientbibliothek der Maschinelles Sehen-API für Node.js'
description: Beginnen Sie mit dieser Schnellstartanleitung mit dem Einsatz der Clientbibliothek für maschinelles Sehen für Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: 50d1222f5c33b327c92799ccf27ed0650ba0226c
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560864"
---
<a name="HOLTop"></a>

Verwenden Sie die Clientbibliothek für maschinelles Sehen für Folgendes:

* Analysieren eines Bilds auf Tags, Textbeschreibungen, Gesichter, nicht jugendfreie Inhalte usw.
* Lesen von gedrucktem und handschriftlichem Text mit der Lese-API

[Referenzdokumentation](/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision) | [Paket (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision) | [Beispiele](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement: [Kostenloses Azure-Konto](https://azure.microsoft.com/free/cognitive-services/)
* Die aktuelle Version von [Node.js](https://nodejs.org/)
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie über <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Erstellen einer Ressource für maschinelles Sehen"  target="_blank"> im Azure-Portal eine Ressource für maschinelles Sehen <span class="docon docon-navigate-external x-hidden-focus"></span></a> erstellen, um Ihren Schlüssel und Endpunkt abzurufen. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um eine Verbindung Ihrer Anwendung mit dem Dienst für maschinelles Sehen herzustellen. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-nodejs-application"></a>Erstellen einer neuen Node.js-Anwendung

Erstellen Sie in einem Konsolenfenster (etwa cmd, PowerShell oder Bash) ein neues Verzeichnis für Ihre App, und rufen Sie es auf.

```console
mkdir myapp && cd myapp
```

Führen Sie den Befehl `npm init` aus, um eine Knotenanwendung mit der Datei `package.json` zu erstellen.

```console
npm init
```

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Installieren Sie die NPM-Pakete `ms-rest-azure` und `@azure/cognitiveservices-computervision`.

```console
npm install @azure/cognitiveservices-computervision
```

Die Datei `package.json` Ihrer App wird mit den Abhängigkeiten aktualisiert.

Erstellen Sie die neue Datei *index.js*, und öffnen Sie sie in einem Text-Editor. Fügen Sie die folgenden import-Anweisungen hinzu.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports)]

> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung.

Erstellen Sie Variablen für den Azure-Endpunkt und -Schlüssel Ihrer Ressource.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_vars)]

> [!IMPORTANT]
> Öffnen Sie das Azure-Portal. Wenn die im Abschnitt **Voraussetzungen** erstellte [Produktname]-Ressource erfolgreich bereitgestellt wurde, klicken Sie unter **Nächste Schritte** auf die Schaltfläche **Zu Ressource wechseln**. Schlüssel und Endpunkt finden Sie auf der Seite mit dem **Schlüssel und dem Endpunkt** der Ressource unter **Ressourcenverwaltung**. 
>
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Weitere Informationen finden Sie im Cognitive Services-Artikel zur [Sicherheit](../../../cognitive-services-security.md).

> [!div class="nextstepaction"]
> [Ich habe den Client eingerichtet.](?success=set-up-client#object-model) [Bei mir ist ein Problem aufgetreten.](https://www.research.net/r/7QYZKHL?issue=set-up-client)

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen dienen zum Verarbeiten einiger Hauptfunktionen des Node.js SDK für die Maschinelles Sehen-API.

|Name|BESCHREIBUNG|
|---|---|
| [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) | Diese Klasse wird für alle Funktionen der Maschinelles Sehen-API benötigt. Sie instanziieren sie mit Ihren Abonnementinformationen und verwenden sie für die meisten Bildvorgänge.|
|[VisualFeatureTypes](/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes?view=azure-node-latest)| Diese Enumeration definiert die verschiedenen Typen der Bildanalyse, die bei einem standardmäßigen Analysevorgang ausgeführt werden können. Sie geben abhängig von Ihren Anforderungen verschiedene **VisualFeatureTypes**-Werte an. |

## <a name="code-examples"></a>Codebeispiele

Diese Codeausschnitte veranschaulichen, wie die folgenden Aufgaben mit der Clientbibliothek der Maschinelles Sehen-API für Node.js ausgeführt werden:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Analysieren eines Bilds](#analyze-an-image)
* [Lesen von gedrucktem und handschriftlichem Text](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients


Instanziieren Sie einen Client mit Ihrem Endpunkt und Schlüssel. Erstellen Sie ein [ApiKeyCredentials](/python/api/msrest/msrest.authentication.apikeycredentials?view=azure-python)-Objekt mit Ihrem Schlüssel und Endpunkt, und verwenden Sie es zum Erstellen eines [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest)-Objekts.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

Definieren Sie anschließend die Funktion `computerVision`, und deklarieren Sie eine asynchrone Serie mit einer primären Funktion und einer Rückruffunktion. Sie fügen Ihren Schnellstartcode der primären Funktion hinzu und rufen unten im Skript `computerVision` auf. Der restliche Code in dieser Schnellstartanleitung wird in die `computerVision`-Funktion eingefügt.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

> [!div class="nextstepaction"]
> [Ich habe den Client authentifiziert.](?success=authenticate-client#analyze-an-image) [Bei mir ist ein Problem aufgetreten.](https://www.research.net/r/7QYZKHL?issue=authenticate-client)

## <a name="analyze-an-image"></a>Analysieren von Bildern

Mit dem Code in diesem Abschnitt werden Remotebilder analysiert, um verschiedene visuelle Merkmale zu extrahieren. Sie können diese Vorgänge im Rahmen der **analyzeImage**-Methode des Clientobjekts durchführen, oder Sie können sie mit individuellen Methoden aufrufen. Ausführlichere Informationen finden Sie in der [Referenzdokumentation](/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest).

> [!NOTE]
> Sie können auch ein lokales Bild analysieren. Sehen Sie sich die [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest)-Methoden an, etwa **analyzeImageInStream**. Alternativ finden Sie im Beispielcode auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) Szenarien zu lokalen Bildern.

### <a name="get-image-description"></a>Abrufen der Bildbeschreibung

Mit dem folgenden Code wird die Liste der generierten Beschriftungen für das Bild abgerufen. Weitere Informationen finden Sie unter [Beschreiben von Bildern in lesbarer Sprache](../../concept-describing-images.md).

Definieren Sie zuerst die URL eines zu analysierenden Images:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe_image)]

Fügen Sie anschließend den folgenden Code hinzu, um die Bildbeschreibung abzurufen und in der Konsole auszugeben.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>Abrufen der Bildkategorie

Mit dem folgenden Code wird die erkannte Kategorie des Bilds abgerufen. Weitere Informationen finden Sie unter [Kategorisieren von Bildern nach Schlüsselinhalt](../../concept-categorizing-images.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories)]

Definieren Sie die Hilfsfunktion `formatCategories`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>Abrufen von Bildtags

Mit dem folgenden Code werden die erkannten Tags im Bild abgerufen. Weitere Informationen finden Sie unter [Inhaltstags](../../concept-tagging-images.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tags)]

Definieren Sie die Hilfsfunktion `formatTags`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>Erkennen von Objekten

Der folgende Code erkennt alltägliche Objekte im Bild und gibt sie in der Konsole aus. Weitere Informationen finden Sie unter [Objekterkennung](../../concept-object-detection.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objects)]

Definieren Sie die Hilfsfunktion `formatRectObjects`, um die oberen, linken, unteren und rechten Koordinaten zusammen mit der Breite und Höhe zurückzugeben.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>Erkennen von Marken

Der folgende Code erkennt Marken und Firmenlogos im Bild und gibt sie in der Konsole aus. Weitere Informationen finden Sie unter [Markenerkennung](../../concept-brand-detection.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>Erkennen von Gesichtern

Mit dem folgenden Code werden die erkannten Gesichter im Bild mit den Rechteckkoordinaten und ausgewählten Gesichtsattributen zurückgegeben. Weitere Informationen finden Sie unter [Gesichtserkennung mit maschinellem Sehen](../../concept-detecting-faces.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_faces)]

Definieren Sie die Hilfsfunktion `formatRectFaces`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-racy-or-gory-content"></a>Erkennen von nicht jugendfreien, freizügigen oder gewaltverherrlichenden Inhalten

Der folgende Code gibt an, ob nicht jugendfreie Inhalte im Bild erkannt wurden. Weitere Informationen finden Sie unter [Erkennen von nicht jugendfreien und freizügigen Inhalten](../../concept-detecting-adult-content.md).

Definieren Sie die URL des zu verwendenden Bilds:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult_image)]

Fügen Sie anschließend den folgenden Code hinzu, um die Erkennung von nicht jugendfreien Inhalten zu aktivieren und die Ergebnisse in der Konsole auszugeben.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Abrufen des Farbschemas für ein Bild

Mit dem folgenden Code werden die die erkannten Farbattribute im Bild ausgegeben, etwa die vorherrschenden Farben und die Akzentfarbe. Weitere Informationen finden Sie unter [Erkennen von Farbschemas auf Bildern](../../concept-detecting-color-schemes.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors)]

Definieren Sie die Hilfsfunktion `printColorScheme`, um die Details des Farbschemas in der Konsole auszugeben.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>Abrufen des domänenspezifischen Inhalts

Maschinelles Sehen kann spezialisierte Modelle zur weiteren Analyse von Bildern verwenden. Weitere Informationen finden Sie unter [Erkennen domänenspezifischer Inhalte](../../concept-detecting-domain-content.md).

Definieren Sie zuerst die URL eines zu analysierenden Images:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_domain_image)]

Der folgende Code analysiert Daten zu erkannten Sehenswürdigkeiten im Bild:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks)]

Definieren Sie die Hilfsfunktion `formatRectDomain`, um die Standortdaten der erkannten Orientierungspunkte zu analysieren.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks_rect)]

### <a name="get-the-image-type"></a>Abrufen des Bildtyps

Der folgende Code gibt Informationen über den Typ des Bilds aus, und zwar unabhängig davon, ob es sich um ClipArt oder eine linienbasierte Zeichnung handelt.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype)]

Definieren Sie die Hilfsfunktion `describeType`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype_describe)]

> [!div class="nextstepaction"]
> [Ich habe ein Bild analysiert.](?success=analyze-image#read-printed-and-handwritten-text) [Bei mir ist ein Problem aufgetreten.](https://www.research.net/r/7QYZKHL?issue=analyze-image)

## <a name="read-printed-and-handwritten-text"></a>Lesen von gedrucktem und handschriftlichem Text

Maschinelles Sehen kann sichtbaren Text in einem Bild extrahieren und in eine Zeichenfolge konvertieren. In diesem Beispiel werden die Lesevorgänge verwendet.

### <a name="set-up-test-images"></a>Einrichten von Testbildern

Speichern Sie einen Verweis auf die URL der Bilder, aus denen Sie Text extrahieren möchten.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

> [!NOTE]
> Sie können auch Text aus einem lokalen Bild auslesen. Sehen Sie sich die [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest)-Methoden an, etwa **readInStream**. Alternativ finden Sie im Beispielcode auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) Szenarien zu lokalen Bildern.

### <a name="call-the-read-api"></a>Aufrufen der Lese-API

Definieren Sie die folgenden Felder in Ihrer Funktion, um die Statuswerte der Read-Aufrufe anzugeben.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_statuses)]

Fügen Sie den unten angegebenen Code hinzu, mit dem die Funktion `readTextFromURL` für die jeweiligen Bilder aufgerufen wird.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

Definieren Sie die Funktion `readTextFromURL`. Hiermit wird die **read**-Methode für das Clientobjekt aufgerufen, mit der eine Vorgangs-ID zurückgegeben und ein asynchroner Prozess zum Auslesen des Bildinhalts gestartet wird. Anschließend wird die Vorgangs-ID verwendet, um den Vorgangsstatus zu überprüfen, bis die Ergebnisse zurückgegeben werden. Danach werden die extrahierten Ergebnisse zurückgegeben.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

Definieren Sie als Nächstes die Hilfsfunktion `printRecText`, mit der die Ergebnisse der Lesevorgänge in der Konsole ausgegeben werden.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

> [!div class="nextstepaction"]
> [Ich habe Text gelesen.](?success=read-printed-handwritten-text#run-the-application) [Bei mir ist ein Problem aufgetreten.](https://www.research.net/r/7QYZKHL?issue=read-printed-handwritten-text)

## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung mit dem Befehl `node` für die Schnellstartdatei aus.

```console
node index.js
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
>[Referenz zur Maschinelles Sehen-API (Node.js)](/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest)


* [Worum handelt es sich bei maschinellem Sehen?](../../overview.md)
* Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js).

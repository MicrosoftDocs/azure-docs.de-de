---
title: Erste Schritte mit der Bing-Spracheingabe-API unter Verwendung der C#-Desktopbibliothek | Microsoft-Dokumentation
titlesuffix: Azure Cognitive Services
description: Entwickeln Sie einfache Windows-Anwendungen, die gesprochene Audiosignale mithilfe der Bing-Spracheingabe-API in Text konvertieren.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5f3b70a2dd9816210ed61280be38504a3980d205
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515347"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-c35-for-net-on-windows"></a>Schnellstart: Verwenden der Bing-Spracheingabe-API in C&#35; für .NET unter Windows

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Auf dieser Seite erfahren Sie, wie Sie eine einfache Windows-Anwendung entwickeln, die gesprochenes Audio mithilfe der Spracherkennungs-API in Text umwandelt. Die Verwendung der Clientbibliothek ermöglicht Streamen in Echtzeit. Das bedeutet: Wenn Ihre Clientanwendung Audio an den Dienst sendet, werden gleichzeitig und asynchron partielle Erkennungsergebnisse an die Anwendung zurückgegeben.

Entwickler, die den Speech-Dienst in Apps auf einem beliebigen Gerät verwenden möchten, können dazu die C#-Desktopbibliothek verwenden. Installieren Sie zum Verwenden der Bibliothek das [NuGet-Paket „Microsoft.ProjectOxford.SpeechRecognition-x86“](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x86/) für eine 32-Bit-Plattform und das [NuGet-Paket „Microsoft.ProjectOxford.SpeechRecognition-x64](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x64/) für eine 64-Bit-Plattform. Die API-Referenz der Clientbibliothek finden Sie in der [C#-Desktopbibliothek für Microsoft Speech](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html).

In den folgenden Abschnitten erfahren Sie, wie Sie die C#-Beispielanwendung unter Verwendung der C#-Desktopbibliothek installieren, erstellen und ausführen.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="platform-requirements"></a>Plattformanforderungen

Das folgende Beispiel wurde unter Verwendung von [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs) für Windows 8 (und höhere Versionen) und .NET Framework 4.5 (und höhere Versionen) entwickelt.

### <a name="get-the-sample-application"></a>Abrufen der Beispielanwendung

Klonen Sie das Beispiel aus dem Repository mit dem [C#-Desktopbibliothekbeispiel für Speech](https://github.com/microsoft/cognitive-speech-stt-windows).

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Abonnieren der Spracherkennungs-API und Abrufen eines Schlüssels für ein kostenloses Probeabonnement

Die Sprach-API ist Teil von Cognitive Services (ehemals Project Oxford). Über die Seite [Cognitive Services ausprobieren](https://azure.microsoft.com/try/cognitive-services/) können Sie Schlüssel für ein kostenloses Probeabonnement abrufen. Wählen Sie zunächst die Option für die Sprach-API aus, und klicken Sie anschließend auf **API-Schlüssel abrufen**, um den Schlüssel abzurufen. Daraufhin werden ein Primär- und ein Sekundärschlüssel zurückgegeben. Beide Schlüssel sind an das gleiche Kontingent gebunden. Es spielt daher keine Rolle, welchen Schlüssel Sie verwenden.

> [!IMPORTANT]
> * Rufen Sie einen Abonnementschlüssel ab. Für die Verwendung der Speech-Clientbibliotheken ist ein [Abonnementschlüssel](https://azure.microsoft.com/try/cognitive-services/) erforderlich.
>
> * Verwenden Sie Ihren Abonnementschlüssel. Fügen Sie Ihren Abonnementschlüssel bei Verwendung der bereitgestellten C#-Beispieldesktopanwendung in das Textfeld ein, wenn Sie das Beispiel ausführen. Weitere Informationen finden Sie unter [Ausführen der Beispielanwendung](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Schritt 1: Installieren der Beispielanwendung

1. Starten Sie Visual Studio 2015, und klicken Sie auf **Datei** > **Öffnen** > **Projekt/Projektmappe**.

2. Navigieren Sie zu dem Ordner, in dem Sie die heruntergeladenen Dateien für die Spracherkennungs-API gespeichert haben. Klicken Sie auf **Speech** > **Windows**, und wählen Sie den Ordner „Sample-WP“ aus.

3. Doppelklicken Sie auf die SLN-Datei „SpeechToText-WPF-Samples.sln“ (eine Visual Studio 2015-Projektmappe). Die Projektmappe wird in Visual Studio geöffnet.

## <a name="step-2-build-the-sample-application"></a>Schritt 2: Erstellen der Beispielanwendung

1. Wenn Sie eine *Erkennung mit Absicht* verwenden möchten, müssen Sie sich zunächst für [Language Understanding Intelligent Service (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) registrieren. Verwenden Sie dann die Endpunkt-URL Ihrer LUIS-App, um im Ordner „samples/SpeechRecognitionServiceExample“ in der Datei „app.config“ den Wert des Schlüssels `LuisEndpointUrl` festzulegen. Weitere Informationen zur Endpunkt-URL der LUIS-App finden Sie unter [Publish your app](../../luis/luis-get-started-create-app.md#publish-your-app) (Veröffentlichen Ihrer App).

   > [!TIP]
   > Ersetzen Sie das Zeichen `&` in der LUIS-Endpunkt-URL durch `&amp;`, um sicherzustellen, dass die URL vom XML-Parser ordnungsgemäß interpretiert wird.

2. Drücken Sie STRG+UMSCHALT+B, oder klicken Sie auf dem Menüband auf **Erstellen**. Klicken Sie anschließend auf **Projektmappe erstellen**.

## <a name="step-3-run-the-sample-application"></a>Schritt 3: Ausführen der Beispielanwendung

1. Drücken Sie nach Abschluss des Buildvorgangs F5, oder klicken Sie auf dem Menüband auf **Starten**, um das Beispiel auszuführen.

2. Wechseln Sie zum Fenster **Project Oxford Speech to Text Sample** (Project Oxford: Spracherkennungsbeispiel). Fügen Sie Ihren Abonnementschlüssel wie hier gezeigt in das Textfeld **Paste your subscription key here to start** (Abonnementschlüssel hier einfügen, um zu beginnen) ein. Klicken Sie auf **Save Key** (Schlüssel speichern), um den Abonnementschlüssel auf Ihrem PC oder Laptop zu speichern. Wenn Sie den Abonnementschlüssel aus dem System löschen möchten, klicken Sie auf **Delete Key** (Schlüssel löschen), um ihn von Ihrem PC oder Laptop zu entfernen.

   ![Spracherkennung: Einfügen des Schlüssels](../Images/SpeechRecog_paste_key.PNG)

3. Wählen Sie unter **Speech Recognition Source** (Quelle für die Spracherkennung) eine der sechs Sprachquellen aus. Diese fallen in zwei Haupteingabekategorien:

   * Aufzeichnung gesprochener Sprache mithilfe des Mikrofons Ihres Computers oder eines angeschlossenen Mikrofons
   * Wiedergabe einer Audiodatei

   Für jede Kategorie stehen drei Erkennungsmodi zur Verfügung:

    * **ShortPhrase-Modus**: Eine Äußerung mit einer Dauer von bis zu 15 Sekunden. Während der Datenübermittlung an den Server erhält der Client mehrere Teilergebnisse und ein Endergebnis mit mehreren n besten Optionen.
    * **LongDictation-Modus**: Eine Äußerung mit einer Dauer von bis zu zwei Minuten. Während der Datenübermittlung an den Server erhält der Client mehrere Teilergebnisse und mehrere Endergebnisse (abhängig davon, wo der Server Pausen zwischen Sätzen erkennt).
    * **Absichtserkennung**: Der Server gibt zusätzliche strukturierte Informationen zur Spracheingabe zurück. Zur Verwendung der Absichtserkennung müssen Sie zunächst mithilfe von [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) ein Modell trainieren.

Verwenden Sie Beispielaudiodateien für diese Beispielanwendung. Die Dateien befinden sich in dem Repository, das Sie zusammen mit diesem Beispiel heruntergeladen haben (im Ordner „samples/SpeechRecognitionServiceExample“). Diese Beispielaudiodateien werden automatisch ausgeführt, wenn Sie keine anderen Dateien auswählen und als Spracheingabe die Option **Use wav file for ShortPhrase mode** (WAV-Datei für ShortPhrase-Modus auswählen) oder **Use wav file for LongDictation mode** (WAV-Datei für LongDictation-Modus auswählen) verwenden. Derzeit wird nur das WAV-Audioformat unterstützt.

![Spracherkennungsschnittstelle](../Images/HelloJones.PNG)

## <a name="samples-explained"></a>Erläuterung der Beispiele

### <a name="recognition-events"></a>Erkennungsereignisse

* **Ereignisse für Teilergebnisse**: Dieses Ereignis wird jedes Mal aufgerufen, wenn der Speech-Dienst noch während des Sprechens (bei Verwendung von `MicrophoneRecognitionClient`) oder noch vor Abschluss der Datenübermittlung (bei Verwendung von `DataRecognitionClient`) versucht, das Gesagte vorauszusagen.
* **Fehlerereignisse**: Werden aufgerufen, wenn der Dienst einen Fehler erkennt.
* **Absichtsereignisse**: Werden für Clients vom Typ „WithIntent“ (nur im ShortPhrase-Modus) aufgerufen, nachdem das Endergebnis der Erkennung analysiert und in eine strukturierte JSON-Absicht konvertiert wurde.
* **Ergebnisereignisse:**
  * Im Modus `ShortPhrase` wird dieses Ereignis aufgerufen und gibt die n besten Ergebnisse zurück, wenn Sie mit Sprechen fertig sind.
  * Im Modus `LongDictation` kann der Ereignishandler mehrmals aufgerufen werden (abhängig davon, wo der Dienst Pausen zwischen Sätzen erkennt).
  * **Für jede der n besten Optionen** werden ein Zuverlässigkeitswert und mehrere Formen des erkannten Texts zurückgegeben. Weitere Informationen finden Sie unter [Output format](../Concepts.md#output-format) (Ausgabeformat).

Ereignishandler sind bereits im Code kommentiert.

## <a name="related-topics"></a>Verwandte Themen

* [Desktopbibliothekreferenz für Microsoft Speech](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html)
* [Erste Schritte mit der Spracherkennungs-API von Microsoft in Java unter Android](GetStartedJavaAndroid.md)
* [Get started with the Speech Recognition API in Objective-C on iOS](Get-Started-ObjectiveC-iOS.md) (Erste Schritte mit der Spracherkennungs-API in Objective-C unter iOS)
* [Erste Schritte mit der Spracherkennungs-API in JavaScript](GetStartedJSWebsockets.md)
* [Get started with speech recognition by using the REST API](GetStartedREST.md) (Erste Schritte mit der Spracherkennung unter Verwendung der REST-API)

---
title: Testen eines Modells mithilfe von Audiodateien – Speech Studio
titleSuffix: Azure Cognitive Services
description: In dieser Schrittanleitung verwenden Sie Speech Studio, um die Erkennung von Sprache in einer Audiodatei zu testen.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/04/2020
ms.author: v-demjoh
ms.openlocfilehash: 572b3b3459e1d837130f3c987d45ee45629f37ad
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96485075"
---
# <a name="test-a-model-using-an-audio-file-in-speech-studio"></a>Testen eines Modells mithilfe einer Audiodatei in Speech Studio

In dieser Schrittanleitung verwenden Sie Speech Studio, um Spracheingaben aus einer Audiodatei in Text zu konvertieren. Mithilfe von Speech Studio können Sie Spracherkennungsmodelle testen, vergleichen, verbessern und bereitstellen. Dafür können Sie zugehörigen Text, Audiomaterial mit menschenmarkierten Transkripten und Ausspracheanleitungen zur Verfügung stellen.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie das Speech-Portal verwenden, [folgen Sie diesen Anweisungen, um ein Azure-Konto zu erstellen und den Spracherkennungsdienst](../custom-speech-overview.md#set-up-your-azure-account) zu abonnieren. Durch dieses einheitliche Abonnement erhalten Sie Zugriff auf die Spracherkennung, Sprachsynthese, Sprachübersetzung und das Custom Speech-Portal.

## <a name="download-an-audio-file"></a>Herunterladen einer Audiodatei

Führen Sie diese Schritte aus, um eine Audiodatei herunterzuladen, die Sprache enthält, und verpacken Sie sie in eine ZIP-Datei.

1. Laden Sie die **[WAV-Beispieldatei über diesen Link herunter](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav)** , indem Sie mit der rechten Maustaste auf den Link klicken und dann **Link speichern unter** auswählen. Klicken Sie auf **Speichern**, um die `whatstheweatherlike.wav`-Datei herunterzuladen.
2. Erstellen Sie mithilfe eines Datei-Explorers oder Terminalfensters mit einem ZIP-Tool eine ZIP-Datei mit dem Namen `whatstheweatherlike.zip`, die die heruntergeladene `whatstheweatherlike.wav`-Datei enthält. In Windows können Sie den Windows-Explorer öffnen, zum `Downloads`-Ordner navigieren, mit der rechten Maustaste auf `whatstheweatherliike.wav` klicken, auf **Senden an** klicken, auf **ZIP-komprimierter Ordner** klicken und die EINGABETASTE drücken, um den Standarddateinamen zu akzeptieren.

## <a name="create-a-project-in-the-custom-speech-portal"></a>Erstellen eines Projekts im Custom Speech-Portal

Führen Sie die folgenden Schritte aus, um ein Projekt zu erstellen, das die ZIP-Datei einer Audiodatei enthält.

1. Öffnen Sie [Speech Studio](https://speech.microsoft.com/), und klicken Sie auf **Neues Projekt**. Geben Sie einen Namen für das Projekt ein, und klicken Sie auf **Erstellen**. Das Projekt wird in der Custom Speech-Liste angezeigt.
2. Klicken Sie auf den Namen Ihres Projekts. Klicken Sie auf der Registerkarte „Daten“ auf **Daten hochladen**.
3. Der standardmäßige Datentyp für die Spracherkennung ist **Nur Audio**. Klicken Sie deshalb auf **Weiter**.
4. Nennen Sie das neue Sprachdataset `MyZipOfAudio`, und klicken Sie auf **Weiter**.
5. Klicken Sie auf **Dateien durchsuchen**, navigieren Sie zu ihrer `whatstheweatherlike.zip`-Datei, und klicken Sie auf **Öffnen**.
6. Klicken Sie auf die Schaltfläche **Upload** . Der Browser lädt die ZIP-Datei in Speech Studio hoch, und Speech Studio verarbeitet den Inhalt.

## <a name="test-a-model"></a>Testen eines Modells

Nachdem Speech Studio den Inhalt der ZIP-Datei verarbeitet hat, können Sie die Quellaudiodatei wiedergeben, während Sie anhand der Transkription nach Fehlern oder Auslassungen suchen. Führen Sie diese Schritte aus, um die Transkriptionsqualität im Browser zu überprüfen.

1. Klicken Sie auf die Registerkarte **Testen** und dann auf **Test hinzufügen**.
2. In diesem Test überprüfen wir die Qualität von reinen Audiodaten. Klicken Sie daher auf **Weiter**, um diesen Testtyp zu akzeptieren.
3. Nennen Sie diesen Test `MyModelTest`, und klicken Sie auf **Weiter**.
4. Klicken Sie auf das Optionsfeld links von `MyZipOfAudio` und dann auf **Weiter**.
5. Die Dropdownliste **Model 1** zeigt standardmäßig das neueste Erkennungsmodell an. Klicken Sie deshalb auf **Erstellen**. Nach Verarbeitung des Inhalts Ihres Audiodatasets ändert sich der Teststatus in **Erfolgreich**.
6. Klicken Sie auf **MyModelTest**. Die Ergebnisse der Spracherkennung werden angezeigt. Klicken Sie auf das nach rechts zeigende Dreieck im Kreis, um das Audiomaterial zu hören, und vergleichen Sie das, was Sie hören, mit dem Text im Kreis.

## <a name="download-detailed-results"></a>Herunterladen detaillierter Ergebnisse

Sie können Dateien herunterladen, die Transkriptionen ausführlicher beschreiben. Die Dateien enthalten die lexikalische Form der Sprache in den Audiodateien, sowie JSON-Dateien, die Offset-, Dauer- und Transkriptionskonfidenzdetails zu den einzelnen Wörtern enthalten. Gehen Sie wie folgt vor, um diese Dateien anzuzeigen.

1. Klicken Sie auf **Download**.
2. Deaktivieren Sie im Dialogfeld „Download“ die Option **Audio**, und klicken Sie auf **Herunterladen**.
3. Entpacken Sie die heruntergeladene ZIP-Datei, und überprüfen Sie die extrahierten Dateien.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie die Genauigkeit der Spracherkennung durch [Vorbereiten von Daten für Custom Speech](../how-to-custom-speech-test-and-train.md) verbessern.
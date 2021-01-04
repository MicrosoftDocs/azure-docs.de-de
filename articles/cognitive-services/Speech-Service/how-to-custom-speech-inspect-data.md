---
title: Überprüfen der Datenqualität für Custom Speech – Speech-Dienst
titleSuffix: Azure Cognitive Services
description: Custom Speech bietet Tools zur visuellen Überprüfung der Erkennungsqualität eines Modells durch Vergleichen von Audiodaten mit dem entsprechenden Erkennungsergebnis. Sie können hochgeladene Audiodaten wiedergeben und bestimmen, ob das angegebene Erkennungsergebnis korrekt ist.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: erhopf
ms.openlocfilehash: 87bc411903604a7068f346604853a2a61c9a6f1c
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499185"
---
# <a name="inspect-custom-speech-data"></a>Überprüfen von Custom Speech-Daten

> [!NOTE]
> Auf dieser Seite wird vorausgesetzt, dass Sie [Prepare data for Custom Speech](./how-to-custom-speech-test-and-train.md) (Vorbereiten von Testdaten für Custom Speech) gelesen und ein Dataset für die Überprüfung hochgeladen haben.

Custom Speech bietet Tools zur visuellen Überprüfung der Erkennungsqualität eines Modells durch Vergleichen von Audiodaten mit dem entsprechenden Erkennungsergebnis. Über das [Custom Speech-Portal](https://speech.microsoft.com/customspeech) können Sie hochgeladene Audiodaten wiedergeben und bestimmen, ob das angegebene Erkennungsergebnis korrekt ist. Mit diesem Tool können Sie die Qualität des Baselinemodells von Microsoft für die Spracherkennung überprüfen, ein trainiertes benutzerdefiniertes Modell untersuchen oder die Aufzeichnungen von zwei Modellen vergleichen.

In diesem Dokument erfahren Sie, wie Sie die Qualität des Baseline-Spracherkennungsmodells von Microsoft und/oder eines trainierten benutzerdefinierten Modells visuell überprüfen. Sie erfahren auch, wie Sie mithilfe des Editors für Onlinetranskriptionen bezeichnete Audiodatasets erstellen und optimieren können.

## <a name="create-a-test"></a>Erstellen eines Tests

Gehen Sie wie folgt vor, um einen Test zu erstellen:

1. Melden Sie sich beim [Custom Speech-Portal](https://speech.microsoft.com/customspeech) an.
2. Navigieren Sie zu **Spracherkennung > Custom Speech > [Projektname] > Testen**.
3. Klicken Sie auf **Test hinzufügen**.
4. Wählen Sie **Inspect quality (Audio-only data)** (Qualität überprüfen (nur Audiodaten)) aus. Geben Sie einen Namen und eine Beschreibung für den Test ein, und wählen Sie Ihr Audiodataset aus.
5. Wählen Sie bis zu zwei Modelle aus, die Sie testen möchten.
6. Klicken Sie auf **Erstellen**.

Nachdem ein Test erfolgreich erstellt wurde, können Sie sehen, wie ein Modell das angegebene Audiodataset transkribiert, oder die Ergebnisse von zwei Modellen direkt miteinander vergleichen.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>Gegenüberstellung der Modelle

Wenn der Teststatus _Erfolgreich_ lautet, klicken Sie auf den Namen des Testelements, um Testdetails anzuzeigen. Auf dieser Detailseite werden alle Äußerungen in Ihrem Dataset aufgelistet. Außerdem werden die Erkennungsergebnisse der beiden Modelle gezeigt, die Sie vergleichen.

Zur einfacheren Untersuchung der Gegenüberstellung können Sie verschiedene Fehlertypen wie Einfügungen, Löschungen und Ersetzungen aktivieren oder deaktivieren. Indem Sie sich die Audiodaten anhören und mit den Erkennungsergebnissen in den einzelnen Spalten vergleichen (die die menschenmarkierte Transkription sowie die Ergebnisse zweier Spracherkennungsmodelle enthalten), können Sie entscheiden, welches Modell Ihre Anforderungen erfüllt und wo Verbesserungen erforderlich sind.

Parallele Modelltests sind nützlich, um zu überprüfen, welches Spracherkennungsmodell für eine Anwendung am besten geeignet ist. Für eine objektive Genauigkeitsmessung, für die transkribiertes Audio benötigt wird, befolgen Sie die Anweisungen unter [Bewerten der Genauigkeit](how-to-custom-speech-evaluate-data.md).

## <a name="online-transcription-editor"></a>Editor für Onlinetranskriptionen

Der Editor für Onlinetranskriptionen ermöglicht Ihnen die einfache Arbeit mit Audiotranskriptionen in Custom Speech. Die wichtigsten Anwendungsfälle des Editors sind die folgenden: 

* Sie verfügen nur über Audiodaten, möchten aber genaue Audio- und menschenmarkierte Datasets von Grund auf erstellen, um sie beim Modelltraining zu verwenden.
* Sie verfügen bereits über Audio- und menschenmarkierte Datasets, aber es gibt Fehler oder Mängel bei der Transkription. Der Editor ermöglicht es Ihnen, die Transkriptionen schnell zu ändern, um eine optimale Genauigkeit beim Training zu erreichen.

Die einzige Voraussetzung für die Verwendung des Editors für Transkriptionen ist das Hochladen von Audiodaten (entweder nur Audio oder Audio und Transkription).

### <a name="import-datasets-to-editor"></a>Importieren von Datasets in den Editor

Navigieren Sie zum Importieren von Daten in den Editor zunächst zu **Custom Speech > [Ihr Projekt] > Editor**.

![Registerkarte „Editor“](media/custom-speech/custom-speech-editor-detail.png)

Führen Sie zum Importieren der Daten als nächstes die folgenden Schritte aus.

1. Klicken Sie auf **Daten importieren**.
1. Erstellen Sie ein oder mehrere neue Datasets, und weisen Sie ihnen eine Beschreibung zu.
1. Wählen Sie Datasets aus. Die Mehrfachauswahl wird unterstützt, und Sie können sowohl nur Audiodaten als auch Audio- und menschenmarkierte Daten auswählen.
1. Bei reinen Audiodaten können Sie optional die Standardmodelle verwenden, um nach dem Import in den Editor automatisch eine Computertranskription zu generieren.
1. Klicken Sie auf **Importieren**.

Nachdem die Daten erfolgreich importiert wurden, können Sie in die Datasets klicken und mit der Bearbeitung beginnen.

> [!TIP]
> Sie können Datasets auch direkt in den Editor importieren, indem Sie Datasets auswählen und auf **In Editor exportieren** klicken.

### <a name="edit-transcription-by-listening-to-audio"></a>Bearbeiten der Transkription durch Anhören von Audio

Nachdem die Daten erfolgreich hochgeladen wurden, klicken Sie auf den Namen der einzelnen Elemente, um Details zu den Daten anzuzeigen. Sie können auch mithilfe von **Zurück** und **Weiter** zwischen den einzelnen Dateien wechseln.

Die Detailseite listet alle Segmente in jeder Audiodatei auf, und Sie können in die gewünschte Äußerung klicken. Für jede Äußerung können Sie die Audiodaten wiedergeben und die Transkriptionen untersuchen sowie die Transkriptionen bearbeiten, wenn Sie Fehler beim Einfügen, Löschen oder Ersetzen finden. Weitere Details zu Fehlertypen finden Sie unter der [Vorgehensweise zur Datenauswertung](how-to-custom-speech-evaluate-data.md).

![Editor-Seite](media/custom-speech/custom-speech-editor.png)

Nachdem Sie Änderungen vorgenommen haben, klicken Sie auf die Schaltfläche **Speichern**.

### <a name="export-datasets-from-the-editor"></a>Exportieren von Datasets aus dem Editor

Um Datensätze zurück auf die Registerkarte **Daten** zu exportieren, navigieren Sie zur Seite der Datendetails, und klicken Sie auf die Schaltfläche **Exportieren**, um alle Dateien als neues Dataset zu exportieren. Sie können die Dateien auch nach letzter Bearbeitungszeit, Audiodauer usw. filtern, um die gewünschten Dateien teilweise auszuwählen. 

![Daten exportieren](media/custom-speech/custom-speech-editor-export.png)

Die nach „Daten“ exportierten Dateien werden mithilfe eines völlig neuen Datasets verwendet und haben keine Auswirkungen auf die bestehenden Daten-/Trainings-/Testentitäten.

## <a name="next-steps"></a>Nächste Schritte

- [Bewerten Ihrer Daten](how-to-custom-speech-evaluate-data.md)
- [Trainieren Ihres Modells](how-to-custom-speech-train-model.md)
- [Verbessern Ihres Modells](./how-to-custom-speech-evaluate-data.md)
- [Bereitstellen Ihres Modells](./how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Prepare data for Custom Speech](./how-to-custom-speech-test-and-train.md) (Vorbereiten von Daten für Custom Speech)
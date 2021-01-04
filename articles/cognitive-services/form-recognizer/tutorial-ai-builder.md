---
title: 'Tutorial: Erstellen einer Formularverarbeitungs-App mit AI Builder: Formularerkennung'
titleSuffix: Azure Cognitive Services
description: In diesem Tutorial verwenden Sie AI Builder, um eine Formularverarbeitungsanwendung zu erstellen und zu trainieren.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: pafarley
ms.openlocfilehash: 3ba08f11fb52fc0ffe3acda5e81e21b7e7629628
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95757419"
---
# <a name="tutorial-create-a-form-processing-app-with-ai-builder"></a>Tutorial: Erstellen einer Formularverarbeitungs-App mit AI Builder

[AI Builder](/ai-builder/overview) ist eine Power Platform-Funktion, mit der Sie Prozesse automatisieren und Ergebnisse vorhersagen können, um die Unternehmensleistung zu steigern. Die Formularverarbeitung von AI Builder ermöglicht die Erstellung von KI-Modellen, mit denen Schlüssel-Wert-Paare und Tabellendaten in Formulardokumenten erkannt und extrahiert werden können.

> [!NOTE]
> Dieses Projekt ist auch als [Microsoft Learn-Modul](/learn/modules/get-started-with-form-processing/) verfügbar.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines KI-Modells für die Formularverarbeitung
> * Trainieren Ihres Modells
> * Veröffentlichen Ihres Modells für die Verwendung in Azure Power Apps oder Power Automate

## <a name="prerequisites"></a>Voraussetzungen

* Mindestens fünf Formulare gleichen Typs, die als Trainings-/Testdaten verwendet werden können. Tipps und Optionen für die Zusammenstellung eines Trainingsdatasets finden Sie unter [Erstellen eines Trainingsdatasets für ein benutzerdefiniertes Modell](./build-training-data-set.md). In dieser Schnellstartanleitung können Sie die Dateien im Ordner **Trainieren** des [Beispieldatasets](https://go.microsoft.com/fwlink/?linkid=2128080) verwenden.
* Eine Power Apps- oder Power Automate-Lizenz (siehe [Leitfaden zur Lizenzierung](https://go.microsoft.com/fwlink/?linkid=2085130)). Die Lizenz muss [Common Data Service](https://powerplatform.microsoft.com/common-data-service/) beinhalten.
* Eine AI Builder-Instanz ([Add-On oder Testversion](https://go.microsoft.com/fwlink/?LinkId=2113956&clcid=0x409)).


## <a name="create-a-form-processing-project"></a>Erstellen eines Formularverarbeitungsprojekts

1. Navigieren Sie zu [Power Apps](https://make.powerapps.com/) oder [Power Automate](https://flow.microsoft.com/signin), und melden Sie sich mit Ihrem Organisationskonto an.
1. Wählen Sie im linken Bereich **AI Builder** > **Erstellen** aus.
1. Wählen Sie die Karte **Formularverarbeitung** aus.
1. Geben Sie einen Namen für Ihr Modell ein.
1. Klicken Sie auf **Erstellen**.

## <a name="upload-and-analyze-documents"></a>Hochladen und Analysieren von Dokumenten

Auf der Seite **Dokumente hinzufügen** müssen Beispieldokumente bereitgestellt werden, um das Modell für die Art von Formular zu trainieren, aus dem Sie Informationen extrahieren möchten. Die hochgeladenen Dokumente werden von AI Builder analysiert, um zu überprüfen, ob sie zum Trainieren eines Modells ausreichen.

> [!NOTE]
> Folgende Eingabedaten für die Formularverarbeitung werden von AI Builder derzeit nicht unterstützt:
>
> - Komplexe Tabellen (geschachtelte Tabellen, zusammengeführten Header oder Zellen usw.)
> - Kontrollkästchen oder Optionsfelder
> - PDF-Dokumente mit mehr als 50 Seiten
> - Ausfüllbare PDF-Dokumente
>
> Weitere Informationen zu Anforderungen für Eingabedokumente finden Sie unter [Eingabeanforderungen](./overview.md#input-requirements).

### <a name="upload-your-documents"></a>Hochladen Ihrer Dokumente

1. Wählen Sie **Dokumente hinzufügen**, mindestens fünf Dokumente und anschließend **Hochladen** aus.
1. Wählen Sie nach Abschluss des Uploadvorgangs **Schließen** aus.
1. Wählen Sie anschließend **Analysieren** aus.

> [!NOTE] 
> Sie können einige der hochgeladenen Dokumente auch wieder entfernen oder weitere hochladen.

> [!div class="mx-imgBorder"]
> ![Screenshot, auf dem die Schaltfläche „Dokumente hinzufügen“ hervorgehoben ist](./media/tutorial-ai-builder/add-documents-page.png)

### <a name="analyze-your-documents"></a>Analysieren Ihrer Dokumente

Im Rahmen des Analyseschritts werden die hochgeladenen Dokumente von AI Builder untersucht, um die Felder und Tabellen zu erkennen. Die Dauer dieses Vorgangs hängt davon ab, wie viele Dokumente bereitgestellt wurden. In den meisten Fällen sollte der Vorgang jedoch nur wenige Minuten dauern.

Wählen Sie nach Abschluss der Analyse die Miniaturansicht aus, um die Feldauswahl zu öffnen.

> [!IMPORTANT]
> War die Analyse nicht erfolgreich, konnte von AI Builder wahrscheinlich kein strukturierter Text in Ihren Dokumenten erkannt werden. Vergewissern Sie sich, dass die von Ihnen hochgeladenen Dokumente den [Eingabeanforderungen](./overview.md#input-requirements) entsprechen.

## <a name="select-your-form-fields"></a>Auswählen Ihrer Formularfelder

Wählen Sie auf der Seite für die Feldauswahl die Felder aus, die für Sie relevant sind:

1. Klicken Sie zum Auswählen eines Felds auf ein Rechteck, das ein erkanntes Feld im Dokument angibt, oder wählen Sie durch Klicken und Ziehen mehrere Felder aus. Felder können auch über den rechten Bereich ausgewählt werden.
1. Klicken Sie auf den Namen des ausgewählten Felds, wenn Sie es umbenennen oder die extrahierten Beschriftungen normalisieren möchten.

    Wenn Sie auf ein erkanntes Feld klicken, werden folgende Informationen angezeigt:

    - **Feldname:** Der Name der Beschriftung für das erkannte Feld.
    - **Feldwert:** Der Wert für das erkannte Feld.

> [!div class="mx-imgBorder"]
> ![Taggingseite](./media/tutorial-ai-builder/select-fields-page.png)

### <a name="label-undetected-fields"></a>Beschriften nicht erkannter Felder

Wurde das Feld, das Sie beschriften möchten, nicht automatisch durch das Modell erkannt, können Sie ein Rechteck um den Wert des Felds ziehen und im daraufhin angezeigten Dialogfeld einen Beschriftungsnamen eingeben.

## <a name="train-your-model"></a>Trainieren Ihres Modells

1. Wählen Sie **Weiter** aus, um die ausgewählten Formularfelder zu überprüfen. Wenn Sie mit dem Ergebnis zufrieden sind, wählen Sie **Trainieren** aus, um das Modell zu trainieren.

    > [!div class="mx-imgBorder"]
    > ![Trainingsseite](./media/tutorial-ai-builder/summary-train-page.png)
1. Wählen Sie nach Abschluss des Trainings im Bildschirm **Training abgeschlossen** die Option **Zur Seite "Details" wechseln** aus.
## <a name="quick-test-your-model"></a>Schnelles Testen Ihres Modells

Auf der Detailseite können Sie Ihr Modell testen, bevor Sie es veröffentlichen oder verwenden:

1. Wählen Sie auf der Detailseite die Option **Schnelltest** aus.
2. Sie können entweder per Drag & Drop ein Dokument hinzufügen oder **Von meinem Gerät hochladen** auswählen, um Ihre Testdatei hochzuladen. Beim Schnelltest sollten bereits nach wenigen Sekunden Ergebnisse angezeigt werden.
3. Wählen Sie entweder **Neu starten** aus, um einen weiteren Test durchzuführen, oder wählen Sie **Schließen** aus, wenn Sie fertig sind.

### <a name="troubleshooting-tips"></a>Tipps zur Problembehandlung

Im Anschluss finden Sie einige Tipps für den Fall, dass Sie nicht zufriedenstellende Ergebnisse oder niedrige Zuverlässigkeitsbewertungen erhalten:

- Wiederholen Sie das Training unter Verwendung von Formularen mit unterschiedlichen Werten in jedem Feld.
- Wiederholen Sie das Training mit mehr Trainingsdokumenten. Je mehr Dokumente Sie markieren, desto besser können die Felder von AI Builder erkannt werden.
- Sie können PDF-Dateien optimieren, indem Sie nur bestimmte Seiten für das Training auswählen. Verwenden Sie die Option **Drucken** > **Als PDF drucken**, um bestimmte Seiten innerhalb Ihres Dokuments auszuwählen.

## <a name="publish-your-model"></a>Veröffentlichen Ihres Modells

Wenn Sie mit Ihrem Modell zufrieden sind, wählen Sie **Veröffentlichen** aus, um es zu veröffentlichen. Das veröffentlichte Modell wird zu **Veröffentlicht** hochgestuft und ist verwendungsbereit.

> [!div class="mx-imgBorder"]
> ![Seite für die Modellveröffentlichung](./media/tutorial-ai-builder/model-page.png)

Das veröffentlichte Formularverarbeitungsmodell kann nun in einer [Power Apps-Canvas-App](/ai-builder/form-processor-component-in-powerapps) oder in [Power Automate](/ai-builder/form-processing-model-in-flow) verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Verwendung eines Formularverarbeitungsmodells finden Sie in der AI Builder-Dokumentation.

* [Verwenden der Formularverarbeitungskomponente in Power Apps](/ai-builder/form-processor-component-in-powerapps)
* [Ein Formularverarbeitungsmodell in Power Automate verwenden](/ai-builder/form-processing-model-in-flow)
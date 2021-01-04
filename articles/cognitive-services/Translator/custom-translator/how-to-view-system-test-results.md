---
title: 'Anzeigen von Systemtestergebnissen und Bereitstellung: Custom Translator'
titleSuffix: Azure Cognitive Services
description: Überprüfen Sie nach erfolgreichem Training die Systemtests, um Ihre Trainingsergebnisse zu analysieren. Wenn Sie mit den Trainingsergebnissen zufrieden sind, fordern Sie die Bereitstellung des trainierten Modells an.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 542f62f04adc7d4858e4b4621b0ae85e906434ad
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369323"
---
# <a name="view-system-test-results"></a>Anzeigen der Ergebnisse von Systemtests

Überprüfen Sie nach erfolgreichem Training die Systemtests, um Ihre Trainingsergebnisse zu analysieren. Wenn Sie mit den Trainingsergebnissen zufrieden sind, fordern Sie die Bereitstellung des trainierten Modells an.

## <a name="system-test-results-page"></a>Seite mit den Systemtestergebnissen

Wählen Sie ein Projekt und anschließend die Modellregisterkarte des Projekts aus, suchen Sie das Modell, das Sie verwenden möchten, und wählen Sie abschließend die Registerkarte „Test“ aus.

Die Registerkarte „Test“ umfasst Folgendes:

1.  **System Test Results** (Systemtestergebnisse): Das Ergebnis des Testprozesses in den Trainings. Der Testprozess erzeugt die BLEU-Bewertung.

    **Sentence Count** (Satzanzahl): Gibt an, wie viele parallele Sätze im Testsatz verwendet wurden.

     **BLEU Score** (BLEU-Bewertung): Die nach Abschluss des Trainings generierte BLEU-Bewertung für ein Modell.

    **Status:** Gibt an, ob der Testprozess abgeschlossen ist.

    ![Systemtestergebnisse](media/how-to/how-to-system-test-results.png)

2.  Klicken Sie auf die Systemtestergebnisse, um zur Detailseite für die Testergebnisse zu gelangen. Auf dieser Seite wird die maschinelle Übersetzung von Sätzen aus dem Testdatensatz angezeigt.

3.  Die Tabelle auf der Detailseite des Testergebnisses hat zwei Spalten: jeweils eine für die Sprachen des Sprachpaars. Die Spalte für die Ausgangssprache enthält die zu übersetzenden Sätze. Die Spalte für die Zielsprache enthält zwei Sätze in jeder Zeile.

    **Ref:** Die Referenzübersetzung des Ausgangssatzes aus dem Testdatensatz.

    **MT:** Die automatische Übersetzung des Ausgangssatzes auf der Grundlage des Modells, das nach Abschluss des Trainings erstellt wurde.

    ![Gegenüberstellung der Systemtestergebnisse](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>Herunterladen des Tests

Klicken Sie auf den Link zum Herunterladen der Übersetzungen, um eine ZIP-Datei herunterzuladen. Die ZIP-Datei enthält die maschinellen Übersetzungen der Ausgangssätze aus dem Testdatensatz.

![Herunterladen des Tests](media/how-to/how-to-system-test-download.png)

Dieses heruntergeladene ZIP-Archiv enthält drei Dateien:

1.  **custom.mt.txt:** Diese Datei enthält maschinelle Übersetzungen von Sätzen aus der Ausgangssprache in die Zielsprache, die auf der Grundlage des mit Benutzerdaten trainierten Modells generiert wurden.

2.  **ref.txt:** Diese Datei enthält vom Benutzer bereitgestellte Übersetzungen von Sätzen aus der Ausgangssprache in die Zielsprache.

3.  **source.txt:** Diese Datei enthält Sätze in der Ausgangssprache.

    ![Heruntergeladene Systemtestergebnisse](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>Bereitstellen eines Modells

So fordern Sie eine Bereitstellung an:

1.  Wählen Sie ein Projekt aus, und navigieren Sie zur Registerkarte „Modelle“.

2. Für erfolgreich trainierte Modelle, die noch nicht bereitgestellt wurden, wird die Schaltfläche „Bereitstellen“ angezeigt.

    ![Screenshot: Hervorgehobene Schaltfläche „Bereitstellen“ zum Bereitstellen eines Modells](media/how-to/how-to-deploy-model.png)

3.  Klicken Sie auf „Bereitstellen“.
4.  Wählen Sie **Bereitgestellt** für die Regionen aus, in denen das Modell bereitgestellt werden soll, und klicken Sie auf „Speichern“. **Bereitgestellt** kann für mehrere Regionen ausgewählt werden.

    ![Screenshot, der zeigt, wo Sie ein Modell bereitstellen oder die Bereitstellung eines Modells aufheben können](media/how-to/how-to-deploy-model-regions.png)

5.  Der Status Ihres Modells wird in der Spalte „Status“ angezeigt.

>[!Note]
>Der benutzerdefinierte Translator unterstützt gleichzeitig 10 bereitgestellte Modelle innerhalb eines Arbeitsbereichs.

## <a name="update-deployment-settings"></a>Aktualisieren der Bereitstellungseinstellungen

So aktualisieren Sie die Bereitstellungseinstellungen:

1.  Wählen Sie ein Projekt aus, und navigieren Sie zur Registerkarte **Modelle**.

2. Bei erfolgreich bereitgestellten Modellen wird die Schaltfläche **Aktualisieren** angezeigt.

    ![Screenshot: Hervorgehobene Schaltfläche „Aktualisieren“ zum Aktualisieren der Bereitstellungseinstellungen](media/how-to/how-to-update-undeploy-model.png)

3.  Wählen Sie **Update** aus.
4.  Wählen Sie **Bereitgestellt** oder **Undeployed** (Nicht bereitgestellt) für die Regionen aus, in denen das Modell bereitgestellt oder nicht bereitgestellt werden soll, und klicken Sie auf **Speichern**.

    ![Bereitstellen des Modells](media/how-to/how-to-undeploy-model.png)

>[!Note]
>Falls Sie für alle Regionen **Undeployed** (Nicht bereitgestellt) ausgewählt haben, wird die Bereitstellung des Modells in allen Regionen aufgehoben, und das Modell wird in einen nicht bereitgestellten Zustand versetzt. Es ist nun nicht mehr zur Verwendung verfügbar.

## <a name="next-steps"></a>Nächste Schritte

- Beginnen Sie mit der Verwendung Ihres bereitstellten benutzerdefinierten Übersetzungsmodells über die [Microsoft-Textübersetzungs-API (V3)](../reference/v3-0-translate.md?tabs=curl).
- Informieren Sie sich über das [Verwalten von Einstellungen](how-to-manage-settings.md) zum Freigeben Ihres Arbeitsbereichs sowie über die Verwaltung Ihres Abonnementschlüssels.
- Erfahren Sie mehr über das [Migrieren Ihres Arbeitsbereichs und Projekts](how-to-migrate.md) aus [Microsoft Translator Hub](https://hub.microsofttranslator.com).
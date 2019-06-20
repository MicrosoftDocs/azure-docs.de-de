---
title: 'Häufig gestellte Fragen: Custom Translator'
titleSuffix: Azure Cognitive Services
description: Enthält Antworten auf häufig gestellte Fragen zu Custom Translator.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: reference
ms.openlocfilehash: 79dc5baacb19143f2b31de7f3822af382c2e6cf4
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66384036"
---
# <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

Dieser Artikel enthält Antworten auf häufig gestellte Fragen zu [Custom Translator](https://portal.customtranslator.azure.ai).

## <a name="what-are-the-current-restrictions-in-custom-translator"></a>Welche Einschränkungen gelten derzeit für Custom Translator?

Es gibt bestimmte Einschränkungen und Grenzwerte für Dateigröße, Modelltraining und Modellimplementierung. Beachten Sie die folgenden Einschränkungen, wenn Sie das Training für die Modellerstellung in Custom Translator einrichten:

- Übermittelte Dateien müssen kleiner als 100 MB sein.
- Einsprachige Daten werden nicht unterstützt.

## <a name="when-should-i-request-deployment-for-a-translation-system-that-has-been-trained"></a>Wann sollte die Bereitstellung für ein trainiertes Übersetzungssystem angefordert werden?

Die Erstellung eines optimalen Übersetzungssystems für Ihr Projekt erfordert ggf. mehrere Trainings. Es empfiehlt sich unter Umständen, mehr Trainingsdaten oder sorgfältiger gefilterte Daten zu verwenden, wenn die BLEU-Bewertung und/oder die Testergebnisse nicht zufriedenstellend sind. Gehen Sie bei der Gestaltung Ihres Optimierungs- und Ihres Testsatzes konsequent und sorgfältig vor, um die Terminologie und den Stil des zu übersetzenden Materials bestmöglich abzudecken. Bei der Erstellung Ihrer Trainingsdaten sowie bei Experimenten mit verschiedenen Optionen können Sie dagegen etwas freier agieren. Fordern Sie eine Systembereitstellung an, wenn Sie mit den Übersetzungen in den Systemtestergebnissen zufrieden sind, dem Training keine weiteren Daten hinzufügen können, um das trainierte System zu verbessern, und über APIs auf das trainierte Modell zugreifen möchten.

## <a name="how-many-trained-systems-can-be-deployed-in-a-project"></a>Wie viele trainierte Systeme können in einem Projekt bereitgestellt werden?

Pro Projekt kann immer nur ein einzelnes trainiertes System bereitgestellt werden. Die Erstellung eines geeigneten Übersetzungssystems für Ihr Projekt erfordert unter Umständen mehrere Trainings, und es empfiehlt sich, die Bereitstellung eines Trainings anzufordern, mit dem Sie das bestmögliche Ergebnis erzielen. Zur Beurteilung der Qualität des Trainings können Sie die BLEU-Bewertung (je höher, desto besser) heranziehen und sich mit Prüfern austauschen, bevor Sie entscheiden, ob die Übersetzungsqualität für die Bereitstellung ausreicht.

## <a name="when-can-i-expect-my-trainings-to-be-deployed"></a>Wann kann ich mit der Bereitstellung meiner Trainings rechnen?

Die Bereitstellung dauert in der Regel weniger als eine Stunde.

## <a name="how-do-you-access-a-deployed-system"></a>Wie erfolgt der Zugriff auf ein bereitgestelltes System?

Auf bereitgestellte Systeme kann über die Microsoft-Textübersetzungs-API (V3) durch Angabe der Kategorie-ID zugegriffen werden. Weitere Informationen zur Textübersetzungs-API finden Sie auf der [Webseite mit der API-Referenz](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference).

## <a name="how-do-i-skip-alignment-and-sentence-breaking-if-my-data-is-already-sentence-aligned"></a>Wie kann ich Abstimmung und Satzaufteilung überspringen, wenn die Sätze in meinen Daten bereits abgestimmt sind?

Satzabstimmung und -aufteilung werden von Custom Translator für TMX-Dateien sowie für Textdateien mit der Erweiterung `.align` übersprungen. Dateien mit der Erweiterung `.align` bieten Benutzern eine Option zum Überspringen des Satzaufteilungs- und Abstimmungsprozesses von „Benutzerdefinierter Translator“ für Dateien, die optimal abgestimmt sind und nicht weiter verarbeitet werden müssen. Es empfiehlt sich, die Erweiterung `.align` nur für optimal abgestimmte Dateien zu verwenden.

Sollte die Anzahl extrahierter Sätze in den beiden Dateien mit dem gleichen Basisnamen nicht übereinstimmen, führt Custom Translator die Satzabstimmung für Dateien mit der Erweiterung `.align` trotzdem aus.

## <a name="i-tried-uploading-my-tmx-but-it-says-document-processing-failed"></a>Ich habe versucht, meine TMX-Datei hochzuladen, dabei tritt jedoch ein Dokumentverarbeitungsfehler auf.

Vergewissern Sie sich, dass die TMX-Datei den Spezifikationen für TMX 1.4b (<https://www.gala-global.org/tmx-14b>) entspricht.

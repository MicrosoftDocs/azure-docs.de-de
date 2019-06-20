---
title: Einsetzen des Unterhaltungslernmoduls – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Erfahren Sie, wie Sie das Unterhaltungslernmodul einsetzen.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 9e5e6594a74219a22d67af18827bfe2b7cbd0fb8
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66385275"
---
# <a name="how-to-teach-with-conversation-learner"></a>Einsetzen des Unterhaltungslernmoduls 

In diesem Dokument wird erläutert, welche Signale das Unterhaltungslernmodul kennt und wie es lernt.  

Der Lehrvorgang (Teaching) kann in zwei verschiedene Schritte unterteilt werden: Entitätsextraktion und Aktionsauswahl.

## <a name="entity-extraction"></a>Entitätsextraktion

Im Hintergrund verwendet das Unterhaltungslernmodul [LUIS](https://www.luis.ai) für die Entitätsextraktion.  Wenn Sie mit LUIS vertraut sind, gilt die entsprechende Oberfläche für die Entitätsextraktion im Unterhaltungslernmodul.

Die Entitätsextraktionsmodelle kennen den *Inhalt* und den *Kontext* in einer Benutzeräußerung.  Wenn z. B. das Wort „Frankfurt“ in einer Äußerung wie „Wie ist das Wetter in Frankfurt“ als Stadt bezeichnet wurde, kann die Entitätsextraktion den gleichen Inhalt („Frankfurt“) als Stadt in einer anderen Äußerung wie „Bevölkerung von Frankfurt“ erkennen, auch wenn die Äußerungen sehr unterschiedlich sind.  Wenn dagegen „Francis“ als Name in „Eine Besprechung mit Francis planen"“ erkannt wurde, kann ein neuer, bisher nicht vorgekommener Name in einem ähnlichen Kontext wie „Eine Besprechung mit Robin vereinbaren“ erkannt werden.  Das maschinelle Lernen leitet basierend auf Trainingsbeispielen ab, wann man sich mit dem Inhalt, dem Kontext oder beidem befassen sollte.

Zu diesem Zeitpunkt kennt die Entitätsextraktion nur den Inhalt der aktuellen Äußerung.  Im Gegensatz zur Aktionsauswahl (siehe unten) kennt sie nicht den Dialogverlauf wie vorherige Systemsequenzen, vorherige Benutzersequenzen oder zuvor erkannte Entitäten.  Daher wird das Verhalten der Entitätsextraktion für alle Äußerungen „geteilt“.  Wenn z. B. „Apple“ in einer Benutzeräußerung als Entitätstyp „Obst“ bezeichnet wurde, erwartet das Entitätsextraktionsmodell, dass für die Äußerung „I want Apple“ immer „Apple“ als „Obst“ bezeichnet wird.

Nachfolgend werden mögliche Lösungen aufgeführt, die Sie anwenden können, wenn die Entitätsextraktion nicht das erwartete Verhalten zeigt:

- Versuchen Sie als Erstes, weitere Trainingsbeispiele hinzuzufügen – insbesondere Beispiele, die den typischen Entitätskontext (umgebende Wörter) oder Ausnahmen aufzeigen.
- Ziehen Sie in Betracht, einer Aktion gegebenenfalls eine Eigenschaft vom Typ „Erwartete Entität“ hinzuzufügen.  Weitere Informationen finden Sie im Tutorial zu erwarteten Entitäten.
- Es ist zwar möglich, die manuelle Verarbeitung zu `EntityExtractionCallback` hinzuzufügen, um Entitäten mithilfe von Code zu extrahieren, davon wird jedoch eher abgeraten, weil nicht von Verbesserungen beim maschinellen Lernen profitiert wird, wenn sich Ihr System weiterentwickelt.

## <a name="action-selection"></a>Aktionsauswahl

Die Aktionsauswahl verwendet ein wiederkehrendes neuronales Netzwerk, das den gesamten Unterhaltungsverlauf als Eingabe verwendet.  Daher ist die Aktionsauswahl ein zustandsbehafteter Prozess, der vorherige Benutzeräußerungen, Entitätswerte und Systemaussagen kennt.  

Einige Signale werden vom Lernprozesses natürlich bevorzugt.  Mit anderen Worten: Wenn das Unterhaltungslernmodul eine Aktionsauswahlentscheidung mit „bevorzugteren“ Signalen erklären kann, wird es das tun; wenn nicht, wird es „weniger bevorzugte“ Signale verwenden.

In der folgenden Tabelle werden alle Signale im Unterhaltungslernmodul aufgeführt, und es wird angegeben, welche von der Aktionsauswahl verwendet werden.  Beachten Sie, dass die Wortreihenfolge in Benutzeräußerungen ignoriert wird.

Signal | Einstellung (1 = bevorzugt) | Notizen
--- | --- | --- 
Systemaktion in vorheriger Sequenz | 1 | 
In der aktuellen Sequenz vorhandene Entitäten | 1 | 
Ob dies die erste Sequenz ist | 1 |
Genaue Übereinstimmung der Wörter in der aktuellen Benutzeräußerung | 2 | 
Wörter mit ähnlicher Bedeutung in der aktuellen Benutzeräußerung | 3 | 
Systemaktionen vor der vorherigen Sequenz | 4 |
In Sequenzen vor der aktuellen Sequenz vorhandene Entitäten | 4 | 
Benutzeräußerungen vor der aktuellen Sequenz | 5 | 

> [!NOTE]
> Die Aktionsauswahl verwendet nicht den Inhalt von Systemaktionen – den Text, den Karteninhalt oder den API-Namen oder das Verhalten – sondern nur die Identität der Systemaktion.  Durch eine Änderung des Inhalts einer Aktion ändert sich daher nicht das Verhalten des Aktionsauswahlmodells.
>
> Beachten Sie außerdem, dass die Inhalte/Werte von Entitäten nicht verwendet werden. Es wird nur deren Vorhandensein/Abwesenheit berücksichtigt.

Nachfolgend werden mögliche Lösungen aufgeführt, die Sie anwenden können, wenn die Aktionsauswahl nicht das erwartete Verhalten zeigt:

- Fügen Sie weitere Trainingsdialoge hinzu, insbesondere Dialoge, die veranschaulichen, welche Signale von der Aktionsauswahl beachtet werden sollten.  Wenn die Aktionsauswahl z. B. ein Signal vor anderen bevorzugen sollten, geben Sie Beispiele an, die zeigen, dass sich das bevorzugte Signal im gleichen Zustand befindet und die anderen Signale variieren.  Für einige Sequenzen sind zum Erlernen mehrere Trainingsdialoge erforderlich.
- Fügen Sie Aktionsdefinitionen „erforderliche“ und „disqualifizierende“ Entitäten hinzu.  Dies schränkt ein, wenn Aktionen verfügbar sind, und kann hilfreich sein, um Geschäftsregeln und einige sinnvolle Muster auszudrücken. 

## <a name="updates-to-models"></a>Aktualisierungen von Modellen

Jedes Mal, wenn Sie eine Entität, eine Aktion oder einen Trainingsdialog auf der Benutzeroberfläche hinzufügen oder bearbeiten, wird eine Anforderung generiert, um das Entitätsextraktionsmodell und das Aktionsauswahlmodell neu zu trainieren.  Diese Anforderung wird in eine Warteschlange eingefügt, und das Neutrainieren erfolgt asynchron.  Wenn ein neues Modell verfügbar ist, wird es ab diesem Punkt für die Entitätsextraktion und Aktionsauswahl verwendet.  Dieser Prozess des Neutrainierens dauert häufig ca. 5 Sekunden. Er kann jedoch länger dauern, wenn das Modell komplex oder die Auslastung des Trainingsdiensts hoch ist.

Weil das Trainieren asynchron erfolgt, kann es vorkommen, dass die von Ihnen vorgenommenen Änderungen nicht sofort widergespiegelt werden.  Wenn sich die Entitätsextraktion oder die Aktionsauswahl basierend auf den Änderungen, die Sie in den letzten 5 bis 10 Sekunden vorgenommen haben, nicht wie erwartet verhält, kann dies die Ursache sein.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Standardwerte und Grenzen](./cl-values-and-boundaries.md)

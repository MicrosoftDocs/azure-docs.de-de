---
title: 'Relevanzbewertung: Personalisierung'
description: Die Relevanzbewertung gibt an, wie gut die Personalisierungsauswahl – die RewardActionID – für den Benutzer war. Der Wert der Relevanzbewertung richtet sich nach Ihrer Geschäftslogik und basiert auf Beobachtungen zum Benutzerverhalten. Die Personalisierung trainiert die eigenen Machine Learning-Modelle durch Auswertung der Relevanz.
ms.service: cognitive-services
ms.subservice: personalizer
ms.date: 02/20/2020
ms.topic: conceptual
ms.openlocfilehash: f3249ba2089c3d9650aa46f665353ad392d0e773
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365566"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>Relevanzbewertungen geben den Erfolg der Personalisierung an.

Die Relevanzbewertung gibt an, wie gut die Personalisierungsauswahl – die [RewardActionID](/rest/api/cognitiveservices/personalizer/rank/rank#response) – für den Benutzer war. Der Wert der Relevanzbewertung richtet sich nach Ihrer Geschäftslogik und basiert auf Beobachtungen zum Benutzerverhalten.

Die Personalisierung trainiert die eigenen Machine Learning-Modelle durch Auswertung der Relevanz.

Erfahren Sie, [wie](how-to-settings.md#configure-rewards-for-the-feedback-loop) Sie den standardmäßigen Relevanzwert im Azure-Portal für Ihre Personalisierungsressource konfigurieren können.

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>Verwenden der Relevanz-API für die Bewertung der Personalisierung

Die Relevanzergebnisse werden mithilfe der [Relevanz-API](/rest/api/cognitiveservices/personalizer/events/reward) an die Personalisierung übermittelt. In der Regel wird die Relevanz als Zahl zwischen 0 und 1 angegeben. Ein negatives Relevanzergebnis mit dem Wert -1 ist in bestimmten Szenarien möglich und sollte nur verwendet werden, wenn Sie Erfahrung mit dem vertiefenden Lernen (Reinforcement Learning, RL) haben. Die Personalisierung trainiert das Modell, um im Lauf der Zeit die höchstmögliche Relevanzsumme zu erreichen.

Die Relevanzergebnisse werden nach dem Benutzerverhalten übermittelt, wobei ein Zeitraum von mehreren Tagen vergehen kann. Die maximale Zeitspanne, die die Personalisierung wartet, bevor ein Ereignis als ohne Relevanz oder als Standardrelevanz eingestuft wird, kann im Azure-Portal mit der [Reward Wait Time](#reward-wait-time) (Relevanzwartezeit) konfiguriert werden.

Wenn die Relevanzbewertung für ein Ereignis nicht innerhalb der **Reward Wait Time** (Relevanzwartezeit) empfangen wurde, wird die **Default Reward** (Standardrelevanz) angewandt. In der Regel ist die **[Default Reward](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** (Standardrelevanz) als 0 (null) konfiguriert.


## <a name="behaviors-and-data-to-consider-for-rewards"></a>Für Relevanz zu berücksichtigende Verhalten und Daten

Berücksichtigen Sie diese Signale und Verhalten für den Kontext der Relevanzbewertung:

* Direkte Benutzereingabe für Vorschläge, wenn es um Optionen geht („Meinen Sie X?“).
* Sitzungsdauer.
* Zeit zwischen Sitzungen.
* Standpunktanalyse der Interaktionen des Benutzers.
* Direkte Fragen und Mini-Umfragen, bei denen der Bot den Benutzer um Feedback bezüglich Nutzen und Richtigkeit bittet.
* Reaktion auf Warnungen oder Verzögerung bei Reaktion auf Warnungen.

## <a name="composing-reward-scores"></a>Erstellen von Relevanzbewertungen

Ein Relevanzergebnis muss in der Geschäftslogik berechnet werden. Das Ergebnis kann wie folgt dargestellt werden:

* Eine einzelne Zahl, die einmal übermittelt wird
* Eine Bewertung (z. B. 0,8), die sofort übermittelt wird, und eine zusätzliche Bewertung (in der Regel 0,2), die später gesendet wird

## <a name="default-rewards"></a>Standardrelevanz

Wenn innerhalb der [Reward Wait Time](#reward-wait-time) (Relevanzwartezeit) seit dem Aufruf für die Priorisierung kein Relevanzergebnis empfangen wird, wendet die Personalisierung implizit die **Default Reward** (Standardrelevanz) an, um diesem Ereignis einen Rang zuzuweisen.

## <a name="building-up-rewards-with-multiple-factors"></a>Erstellen von Relevanzen mit mehreren Faktoren

Für eine effektive Personalisierung können Sie für die Relevanzbewertung mehrere Faktoren zugrunde legen.

Sie können z. B. die folgenden Regeln für die Personalisierung einer Liste von Videoinhalten anwenden:

|Benutzerverhalten|Partieller Bewertungswert|
|--|--|
|Der Benutzer hat auf das oberste Element geklickt.|Relevanz + 0,5|
|Der Benutzer hat den eigentlichen Inhalt des Elements geöffnet.|Relevanz + 0,3|
|Der Benutzer hat sich 5 Minuten oder 30 % des Inhalts angesehen (je nachdem, was länger ist).|Relevanz + 0,2|
|||

Anschließend können Sie die Summe der Relevanzwerte an die API senden.

## <a name="calling-the-reward-api-multiple-times"></a>Mehrfaches Aufrufen der Relevanz-API

Sie können die Relevanz-API auch mit derselben Ereignis-ID aufrufen und dabei unterschiedliche Relevanzbewertungen übermitteln. Wenn die Personalisierung diese Relevanzwerte empfängt, bestimmt sie die abschließende Relevanz für dieses Ereignis durch Aggregieren der Werte gemäß der Konfiguration der Personalisierung.

Aggregationswerte:

*  **Erstes:** Die erste empfangene Relevanzbewertung für das Ereignis wird angewandt, alle späteren werden verworfen.
* **Sum** : Alle für die eventId erfassten Relevanzbewertungen werden übernommen und zusammenführt.

Sämtliche nach der **Reward Wait Time** (Relevanzwartezeit) empfangenen Bewertungen für ein Ereignis werden verworfen und nicht in das Training von Modellen einbezogen.

Durch die Addition der Relevanzbewertungen kann das Endergebnis für die Relevanz außerhalb des erwarteten Bewertungsbereichs liegen. Dies führt nicht zu einem Fehler beim Dienst.

## <a name="best-practices-for-calculating-reward-score"></a>Bewährte Methoden für die Berechnung von Relevanzbewertungen

* **Berücksichtigen Sie nur „echte“ Indikatoren für eine erfolgreiche Personalisierung:** Auch wenn es so scheint, als seien Klicks ein gutes Kriterium, hängt ein gutes Relevanzergebnis davon ab, was Ihre Benutzer *erreichen* sollen – und nicht davon, was sie *machen* sollen.  Beispielsweise kann eine Relevanz auf der Grundlage von Klicks zur Auswahl von Inhalten führen, die lediglich Clickbait darstellen.

* **Verwenden Sie eine Relevanzbewertung, um die Qualität der Personalisierung einzuschätzen:** Die Personalisierung von Filmvorschlägen sollte im besten Fall dazu führen, dass die Benutzer den Film ansehen und ihm eine hohe Bewertung geben. Da die Filmbewertung wahrscheinlich von vielen Dingen abhängt (Qualität der Schauspieler, Stimmung des Benutzers), stellt sie kein gutes Kriterium für die Relevanz der *Personalisierung* dar. Ein Benutzer, der sich die ersten Minuten des Films ansieht, stellt jedoch ein besseres Kriterium für die Effektivität der Personalisierung dar, sodass die Übermittlung der Relevanzbewertung 1 nach 5 Minuten ein besseres Signal darstellt.

* **Die Relevanz gilt ausschließlich für die RewardActionID:** Die Personalisierung wendet die Relevanz an, um die Wirksamkeit der in der RewardActionID angegebenen Aktion zu verstehen. Wenn Sie andere Aktionen anzeigen und der Benutzer darauf klickt, sollte die Relevanz 0 (null) sein.

* **Berücksichtigen Sie unbeabsichtigte Folgen:** Erstellen Sie Relevanzfunktionen, die zu fundierten Ergebnissen auf der Basis von [Ethik und verantwortungsvoller Verwendung](ethics-responsible-use.md) führen.

* **Nutzen Sie inkrementelle Relevanzwerte:** Das Addieren partieller Relevanzwerte für weniger bedeutsames Benutzerverhalten kann zu einer höheren Gesamtrelevanz durch die Personalisierung führen. Durch diese inkrementelle Relevanz wissen Algorithmen, wann der richtige Zeitpunkt ist, um den Benutzer an das gewünschte Verhalten heranzuführen.
    * Wenn Sie eine Liste mit Filmen präsentieren und der Benutzer eine Weile mit der Maus auf den ersten Film zeigt, um Informationen anzuzeigen, wissen Sie, dass eine gewisse Benutzerbindung eingesetzt hat. Das Verhalten kann mit einer Relevanzbewertung von 0,1 gewertet werden.
    * Wenn der Benutzer die Seite geöffnet und dann geschlossen hat, kann das Relevanzergebnis 0,2 lauten.

## <a name="reward-wait-time"></a>Belohnungswartezeit

Die Personalisierung korreliert die Informationen eines Rangfolgeaufrufs mit den Relevanzwerten, die in Relevanzaufrufen zum Trainieren des Modells übermittelt wurden. Diese können zu unterschiedlichen Zeitpunkten eingehen. Die Personalisierung wartet einen begrenzten Zeitraum, der mit dem Aufruf für die Priorisierung beginnt. Dies gilt auch, wenn der Rangfolgeaufruf als inaktives Ereignis erfolgt ist und erst später aktiviert wurde.

Wenn die **Reward Wait Time** (Relevanzwartezeit) abgelaufen ist und keine Relevanzinformationen eingegangen sind, erhält dieses Ereignis für das Training die Standardrelevanz. Die maximale Wartezeit beträgt 6 Tage.

## <a name="best-practices-for-reward-wait-time"></a>Bewährte Methoden für die Belohnungswartezeit

Befolgen Sie diese Empfehlungen für bessere Ergebnisse.

* Legen Sie die Relevanzwartezeit so kurz wie möglich fest. Sie sollte jedoch ausreichen, um Feedback vom Benutzer zu erhalten.

* Wählen Sie keine Dauer unterhalb der für Feedback erforderlichen Zeit aus. Wenn einige Ihrer Relevanzbewertungen eingehen, nachdem ein Benutzer eine Minute eines Videos angesehen hat, sollte das Experiment mindestens doppelt so lang dauern.

## <a name="next-steps"></a>Nächste Schritte

* [Vertiefendes Lernen](concepts-reinforcement-learning.md)
* [Testen Sie die Rang-API.](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [Testen Sie die Relevanz-API.](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)
---
title: One-vs-One Multiclass
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie das One-vs-One Multiclass-Modul in Azure Machine Learning verwenden, um ein Multiklassenklassifizierungsmodell aus einer Gruppe von Binärklassifizierungsmodellen zu erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 92ef5b372113d0d226335b8ec464a75a5a684bc3
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592905"
---
# <a name="one-vs-one-multiclass"></a>One-vs-One Multiclass

In diesem Artikel erfahren Sie, wie Sie das Modul „One-vs-One Multiclass“ im Azure Machine Learning-Designer verwenden. Das Ziel besteht darin, ein Klassifizierungsmodell mit dem Ansatz *One-vs-One* zu erstellen, das mehrere Klassen vorhersagen kann.

Dieses Modul ist nützlich zum Erstellen von Modellen, mit denen drei oder mehr mögliche Ergebnisse vorhergesagt werden, wenn das Ergebnis von kontinuierlichen oder kategorialen Vorhersagevariablen abhängt. Mit dieser Methode können Sie auch Binärklassifizierungsmethoden für Probleme verwenden, die mehrere Ausgabeklassen erfordern.

### <a name="more-about-one-versus-one-models"></a>Weitere Informationen zu One-vs-One-Modellen

Einige Klassifizierungsalgorithmen sind so gestaltet, dass sie die Verwendung von mehr als zwei Klassen ermöglichen. Bei anderen sind die möglichen Ergebnisse auf einen von zwei Werten beschränkt (binäres Modell oder Zweiklassenmodell). Allerdings gibt es auch für Binärklassifizierungsalgorithmen verschiedene Strategien, um sie an Multiklassen-Klassifizierungsaufgaben anzupassen. 

Dieses Modul implementiert die One-vs-One-Methode, mit der pro Klassenpaar ein binäres Modell erstellt wird. Zum Zeitpunkt der Vorhersage wird die Klasse ausgewählt, die die meisten Stimmen erhalten hat. Da sie `n_classes * (n_classes - 1) / 2` Klassifizierer aufnehmen muss, ist diese Methode aufgrund ihrer O(n_classes^2)-Komplexität in der Regel langsamer als die One-vs-All-Methode. Für Algorithmen wie z. B. Kernelalgorithmen, die sich mit `n_samples` nicht gut skalieren lassen, kann diese Methode allerdings Vorteile bringen. Das liegt daran, dass jedes einzelne Lernproblem nur eine kleine Teilmenge der Daten betrifft, wohingegen bei One-vs-All das vollständige Dataset so häufig verwendet wird wie mit `n_classes` angegeben.

Im Wesentlichen erstellt das Modul eine Zusammenstellung einzelner Modelle und führt die Ergebnisse anschließend zusammen, um ein einzelnes Modell zu erstellen, das alle Klassen vorhersagt. Als Grundlage für ein One-vs-One-Modell kann jeder Binärklassifizierer verwendet werden.  

Ein Beispiel: Angenommen, Sie konfigurieren ein Modell vom Typ [Two-Class Support Vector Machine](two-class-support-vector-machine.md) und stellen dieses Modell als Eingabe für das Modul „One-vs-One Multiclass“ bereit. Das Modul erstellt daraufhin Modelle vom Typ „Two-Class Support Vector Machine“ (2-Klassen-Support Vector Machine) für alle Elemente der Ausgabeklasse. Anschließend wendet es die One-vs-One-Methode an, um die Ergebnisse für alle Klassen zu kombinieren.  

Das Modul verwendet OneVsOneClassifier von „sklearn“, und Sie können [hier](https://scikit-learn.org/stable/modules/generated/sklearn.multiclass.OneVsOneClassifier.html) weitere Details erfahren.

## <a name="how-to-configure-the-one-vs-one-multiclass-classifier"></a>Konfigurieren des Klassifizierers für „One-vs-One Multiclass“  

Dieses Modul erstellt eine Zusammenstellung von Binärklassifizierungsmodellen, um mehrere Klassen zu analysieren. Wenn Sie dieses Modul verwenden möchten, müssen Sie zunächst ein Modell vom Typ *Binärklassifizierung* konfigurieren und trainieren. 

Dann stellen Sie eine Verbindung vom binären Modell mit dem Modul „One-vs-One Multiclass“ her. Anschließend trainieren Sie die Zusammenstellung von Modellen, indem Sie [Train Model](train-model.md) (Modell trainieren) mit einem bezeichneten Trainingsdataset verwenden.

Wenn Sie die Modelle kombinieren, erstellt „One-vs-One Multiclass“ mehrere Binärklassifizierungsmodelle, optimiert den Algorithmus für jede Klasse und führt die Modelle anschließend zusammen. Diese Aufgaben werden auch dann ausgeführt, wenn das Trainingsdataset mehrere Klassenwerte besitzt.

1. Fügen Sie das Modul „One-vs-One Multiclass“ Ihrer Pipeline im Designer hinzu. Sie finden dieses Modul unter **Machine Learning – Initialisieren** in der Kategorie **Klassifizierung**.

   Der Klassifizierer für „One-vs-One Multiclass“ besitzt selbst keine konfigurierbaren Parameter. Alle Anpassungen müssen im Binärklassifizierungsmodell vorgenommen werden, das als Eingabe bereitgestellt wird.

2. Fügen Sie ein Binärklassifizierungsmodell zur Pipeline hinzu, und konfigurieren Sie dieses Modell. Sie können beispielsweise [Two-Class Support Vector Machine](two-class-support-vector-machine.md) (2-Klassen-Support Vector Machine) oder [Two-Class Boosted Decision Tree](two-class-boosted-decision-tree.md) (Verstärkter Entscheidungsbaum mit zwei Klassen) verwenden.

3. Fügen Sie Ihrer Pipeline das Modul [Train Model](train-model.md) (Modell trainieren) hinzu. Stellen Sie eine Verbindung mit dem untrainierten Klassifizierer her, der von „One-vs-One Multiclass“ ausgegeben wird.

4. Stellen Sie für die andere Eingabe von [Train Model](train-model.md) (Modell trainieren) eine Verbindung mit einem bezeichneten Trainingsdataset her, das über mehrere Klassenwerte verfügt.

5. Übermitteln Sie die Pipeline.

## <a name="results"></a>Ergebnisse

Nachdem das Training abgeschlossen ist, können Sie das Modell verwenden, um Multiklassenvorhersagen zu treffen.

Alternativ können Sie den untrainierten Klassifizierer an [Cross-Validate Model](cross-validate-model.md) (Kreuzvalidierung für Modell ausführen) übergeben, um eine Kreuzvalidierung anhand eines bezeichneten Validierungsdatasets auszuführen.


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an. 

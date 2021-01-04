---
title: Featureauswahl im Team Data Science-Prozess
description: Erläutert den Grund zur Featureauswahl und stellt Beispiele der Rolle im Datenaufbereitungsprozess für das maschinelle Lernen vor.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 940d4c465acb5e8478d2b204ab5b9b18c79254f0
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321326"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Featureauswahl im Team Data Science-Prozess (TDSP)
In diesem Artikel werden die Gründe zur Featureauswahl erläutert und Beispiele der Rolle im Datenaufbereitungsprozess für maschinelles Lernen vorgestellt. Diese Beispiele stammen aus Azure Machine Learning Studio.

Entwicklung und Auswahl von Features gehören zum Team Data Science-Prozess (TDSP), der unter [Was ist der Team Data Science-Prozess?](overview.md) beschrieben wird. Entwicklung und Auswahl von Features sind Teile des Schritts **Entwickeln von Features** des TDSP.

* **Featureentwicklung** : Bei diesem Prozess wird versucht, zusätzliche relevante Features aus den vorhandenen Rohfeatures in den Daten zu erstellen und die Vorhersageleistung des Lernalgorithmus zu steigern.
* **Featureauswahl** : Dieser Prozess wählt bei dem Versuch, die Anzahl von Dimensionen des Trainingsproblems zu verringern, die wichtigste Teilmenge der ursprünglichen Datenfeatures aus.

Normalerweise wird die **Featureentwicklung** zuerst ausgeführt, um zusätzliche Features zu generieren, und anschließend wird die **Featureauswahl** ausgeführt, um irrelevante, redundante oder hoch korrelierte Features zu entfernen.

## <a name="filter-features-from-your-data---feature-selection"></a>Filtern von Features aus Ihren Daten – Featureauswahl
Die Featureauswahl kann für Klassifizierungs- oder Regressionsaufgaben verwendet werden. Das Ziel ist, eine Teilmenge der Features aus dem ursprünglichen Dataset auszuwählen, die dessen Dimensionen verringert, indem sie einen minimalen Satz von Features zur Darstellung der maximalen Abweichung in den Daten verwendet. Diese Teilmenge der Features wird zum Trainieren des Modells verwendet. Die Featureauswahl dient zwei wesentlichen Zwecken.

* Zunächst steigert die Featureauswahl häufig die Klassifizierungsgenauigkeit durch Eliminieren irrelevanter, redundanter oder hochgradig korrelierter Features.
* Zweitens sinkt die Anzahl von Features, was den Modelltrainingsvorgang effizienter gestaltet. Effizienz ist wichtig für Lernmodelle, deren Training teuer ist, z. B. Support Vector Machines.

Obwohl die Featureauswahl die Anzahl von Features im Dataset reduzieren soll, die zum Trainieren des Modells verwendet werden, wird sie nicht als „Reduzierung der Anzahl von Dimensionen“ bezeichnet. Featureauswahlmethoden extrahieren eine Teilmenge der ursprünglichen Features in den Daten, ohne sie zu ändern.  Verfahren zur Reduktion der Anzahl von Dimensionen nutzen entwickelte Features, die die ursprünglichen Features transformieren können und sie so ändern. Beispiele für Verfahren zur Verringerung der Anzahl von Dimensionen sind zum Beispiel Principal Component Analysis, kanonische Korrelationsanalyse und Singular Value Decomposition.

Eine unter anderem häufig angewendete Kategorie von Featureauswahlmethoden in einem überwachten Kontext wird als „filterbasierte Featureauswahl“ bezeichnet. Durch Auswerten der Korrelation zwischen den einzelnen Features und dem Zielattribut wenden diese Methoden ein statistisches Maß an, um jedem Feature eine Bewertung zuzuweisen. Die Features werden dann nach dem Ergebnis geordnet. Das kann helfen, den Schwellenwert zum Beibehalten oder Entfernen eines bestimmten Features festzulegen. Beispiele für die statistischen Mittel in diesen Methoden sind Pearson-Korrelation, gegenseitige Information und der Chi-Quadrat-Test.

Azure Machine Learning Studio enthält Module zur Featureauswahl. Hierzu gehören die Module [Filter-Based Feature Selection][filter-based-feature-selection] und [Fisher Linear Discriminant Analysis][fisher-linear-discriminant-analysis], wie in der folgenden Abbildung zu sehen.

![Featureauswahlmodule](./media/select-features/feature-Selection.png)

Betrachten Sie beispielsweise die Verwendung des Moduls [Filter-Based Feature Selection][filter-based-feature-selection]. Verwenden Sie aus praktischen Gründen weiterhin das Textminingbeispiel. Nehmen wir an, dass Sie ein Regressionsmodell erstellen möchten, nachdem ein Satz von 256 Features über das Modul [Feature Hashing][feature-hashing] erstellt wurde, und dass die Antwortvariable „Col1“ lautet, die Buchbewertungen im Bereich von 1 bis 5 enthält. Setzen Sie die "Feature scoring method" (Featurebewertungsmethode) auf "Pearson Correlation" (Pearson-Korrelation), die "Target Column" (Zielspalte) auf "Col1" und die "Number of desired features" (Anzahl von gewünschten Features) auf 50. Dann erzeugt das Modul [Filter-Based Feature Selection][filter-based-feature-selection] ein Dataset mit 50 Features und dem Zieltattribut „Col1“. Die folgende Abbildung zeigt den Ablauf dieses Experiments und die Eingabeparameter:

![Eigenschaften von filterbasierten Featureauswahlmodulen](./media/select-features/feature-Selection1.png)

Die folgende Abbildung zeigt die resultierenden Datasets:

![Resultierendes Dataset für das filterbasierte Featureauswahlmodul](./media/select-features/feature-Selection2.png)

Jedes Feature wird auf Basis der Pearson-Korrelation zwischen sich selbst und dem Zielattribut "Col1" bewertet. Die Features mit den besten Werten werden beibehalten.

Die entsprechenden Ergebnisse der ausgewählten Features sind in der folgenden Abbildung dargestellt:

![Bewertungen für filterbasierte Featureauswahlmodule](./media/select-features/feature-Selection3.png)

Durch Anwenden dieses Moduls [Filter-Based Feature Selection][filter-based-feature-selection] werden 50 von 256 Features ausgewählt, da sie die am meisten korrelierten Features mit der Zielvariable „Col1“ auf Grundlage der Bewertungsmethode „Pearson Correlation“ aufweisen.

## <a name="conclusion"></a>Zusammenfassung
Featureentwicklung und Featureauswahl sind zwei allgemein entwickelte und ausgewählte Features, die die Effizienz des Trainingsprozesses steigern, der versucht, die in den Daten enthaltenen Schlüsselinformationen zu extrahieren. Sie verbessern auch die Leistungsfähigkeit dieser Modelle für eine exakte Klassifizierung der Eingabedaten und verlässlichere Vorhersagen von Ergebnissen, die von Interesse sind. Entwicklung und Auswahl von Features können auch kombiniert werden, damit das Lernen besser rechnerisch verfolgt werden kann. Dies erfolgt durch eine Erweiterung und anschließende Verringerung der Anzahl von Features zum Kalibrieren oder Trainieren eines Modells. Mathematisch gesehen stehen die Features, die zum Trainieren des Modells ausgewählt werden, für einen minimalen Satz von unabhängigen Variablen, die Muster in den Daten erklären und dann erfolgreich Vorhersageergebnisse liefern.

Die Entwicklung bzw. Auswahl von Features muss nicht immer unbedingt erfolgen. Ob sie benötigt werden, hängt von den gesammelten Daten, dem ausgewählten Algorithmus und dem Ziel des Experiments ab.

<!-- Module References -->
[feature-hashing]: /azure/machine-learning/studio-module-reference/feature-hashing
[filter-based-feature-selection]: /previous-versions/azure/dn905854(v=azure.100)
[fisher-linear-discriminant-analysis]: /azure/machine-learning/studio-module-reference/fisher-linear-discriminant-analysis
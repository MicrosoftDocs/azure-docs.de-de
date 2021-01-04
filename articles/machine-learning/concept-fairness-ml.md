---
title: Erhöhen der Fairness von Machine Learning-Modellen (Vorschau)
titleSuffix: Azure Machine Learning
description: Hier finden Sie Informationen zur Fairness bei Machine Learning-Modellen, und Sie erfahren, wie das Python-Paket „Fairlearn“ zur Erstellung gerechterer Modelle beitragen kann.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 08/05/2020
ms.openlocfilehash: 459cb1f7ea6c756b8cf6eba70af5ebabe76cc8b0
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335757"
---
# <a name="mitigate-fairness-in-machine-learning-models-preview"></a>Erhöhen der Fairness von Machine Learning-Modellen (Vorschau)

Hier finden Sie Informationen zur Fairness bei Machine Learning, und Sie erfahren, wie das Open-Source-Python-Paket [Fairlearn](https://fairlearn.github.io/) Fairnessprobleme bei Machine Learning-Modellen beheben kann. Wenn Sie keine Anstrengungen unternehmen, Fragestellungen rund um das Thema Fairness zu verstehen und die Fairness bei der Erstellung von Machine Learning-Modellen zu beurteilen, erstellen Sie möglicherweise Modelle, die keine fairen Ergebnisse liefern.

In der folgenden Zusammenfassung des [Benutzerhandbuchs](https://fairlearn.github.io/master/user_guide/index.html) für das Open-Source-Paket Fairlearn wird beschrieben, wie Sie Fairlearn verwenden, um die Fairness der von Ihnen erstellten KI-Systeme zu bewerten.  Das Open-Source-Paket Fairlearn bietet ferner Optionen, die Ihnen helfen, von Ihnen festgestellte Fairness-Probleme zu reduzieren oder abzumildern.  In der [Anleitung](how-to-machine-learning-fairness-aml.md) und in den [Beispielnotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness) finden Sie weitere Informationen, wie Sie die Fairnessbewertung von KI-Systemen während des Trainings bei Azure Machine Learning aktivieren.


## <a name="what-is-fairness-in-machine-learning-models"></a>Was ist Fairness bei Machine Learning-Modellen?

>[!NOTE]
> Fairness ist eine soziotechnische Herausforderung. Viele Fairnessaspekte wie Gerechtigkeit und ein faires Verfahren werden nicht in quantitativen Fairnessmetriken erfasst. Außerdem können viele quantitative Fairnessmetriken nicht gleichzeitig befriedigt werden. Das Open-Source-Paket Fairlearn möchte Menschen in die Lage versetzen, verschiedene Auswirkungen und Entschärfungsstrategien zu beurteilen. Letztlich obliegt es den menschlichen Benutzern, die Modelle der künstlichen Intelligenz und des Machine Learnings erstellen, ihrem Szenario angemessene Kompromisse einzugehen.

Systeme mit künstlicher Intelligenz und Machine Learning können unfaires Verhalten an den Tag legen. Unfaires Verhalten kann beispielsweise auf der Grundlage entstandener Nachteile oder Auswirkungen auf Menschen definiert werden. KI-Systeme können viele Arten von Nachteilen verursachen. Weitere Informationen finden Sie in der [NeurIPS 2017-Keynote von Kate Crawford](https://www.youtube.com/watch?v=fMym_BKWQzk).

Zwei weit verbreitete Arten KI-bedingter Nachteile sind:

- Nachteile bei der Zuteilung: Ein KI-System stellt Gelegenheiten, Ressourcen oder Informationen für bestimmte Gruppen bereit oder enthält sie diesen vor. Beispiele wären etwa Personalentscheidungen, eine Aufnahme bei einer Bildungseinrichtung und eine Kreditvergabe, bei denen ein Modell geeignete Kandidaten aus einer bestimmten Personengruppe möglicherweise besser auswählen kann als aus anderen Gruppen.

- Nachteile bei der Servicequalität: Ein KI-System funktioniert für eine Personengruppe besser als für eine andere. Beispielsweise kann es vorkommen, dass ein Spracherkennungssystem bei Männern besser funktioniert als bei Frauen.

Diese Nachteile müssen bewertet und beseitigt werden, um unfaires Verhalten von KI-Systemen zu minimieren.

## <a name="fairness-assessment-and-mitigation-with-fairlearn"></a>Bewertung der Fairness und Vermeidung von Unfairness mit Fairlearn

Fairlearn ist ein Open-Source-Python-Paket, mit dem Entwickler von Machine Learning-Systemen die Fairness ihrer Systeme bewerten und die festgestellten Fairnessprobleme behandeln können.

Das Open-Source-Paket Fairlearn verfügt über zwei Komponenten:

- Bewertungsdashboard: Ein Jupyter Notebook-Widget, um die Auswirkungen der Vorhersagen eines Modells auf verschiedene Gruppen zu bewerten. Darüber hinaus können hier verschiedene Modelle anhand von Fairness- und Leistungsmetriken miteinander verglichen werden.
- Entschärfungsalgorithmen: Eine Reihe von Algorithmen zur Behandlung von Unfairness bei der binären Klassifizierung und Regression.

Diese Komponenten ermöglichen es Data Scientists und Führungskräften, Kompromisse zwischen Fairness und Leistung abzuwägen und die optimale Entschärfungsstrategie für ihre Anforderungen zu verfolgen.

## <a name="assess-fairness-in-machine-learning-models"></a>Bewerten der Fairness von Machine Learning-Modellen

Im Open-Source-Paket Fairlearn basiert das Fairnesskonzept auf einem als **Gruppenfairness** bezeichneten Ansatz. Dabei wird folgende Frage gestellt: Bei welchen Personengruppen besteht die Gefahr einer Benachteiligung? Die relevanten Gruppen werden auch als Teilgesamtheiten bezeichnet und durch **sensible Features** oder sensible Attribute definiert. Sensible Features werden als Vektor oder als Matrix namens `sensitive_features` an einen Schätzer im Open-Source-Paket Fairlearn übermittelt. Der Begriff legt nahe, dass Systemdesigner diese Features bei der Bewertung der Gruppenfairness besonders berücksichtigen sollten. 

Dabei ist darauf zu achten, ob diese Features personenbezogene Daten enthalten und somit Auswirkungen auf den Datenschutz haben. Das Wort „sensibel“ bedeutet jedoch nicht, dass diese Features nicht für Vorhersagen herangezogen werden sollen.

>[!NOTE]
> Eine Fairnessbewertung ist keine rein technische Angelegenheit.  Das Open-Source-Paket Fairlearn unterstützt Sie bei der Fairnessbewertung eines Modells, aber es führt keine Bewertung für Sie durch.  Mit dem Open-Source-Paket Fairlearn können Sie leichter quantitative Metriken zur Bewertung der Fairness identifizieren, Entwickler müssen jedoch auch eine qualitative Analyse durchführen, um die Fairness ihrer Modelle zu beurteilen.  Die oben erwähnten sensiblen Features sind ein Beispiel für diese Art von qualitativen Analysen.     

Im Rahmen der Bewertungsphase wird Fairness durch Ungleichheitsmetriken quantifiziert. **Ungleichheitsmetriken** ermöglichen das Auswerten und Vergleichen des Verhaltens eines Modells für verschiedene Gruppen – entweder in Form von Verhältnissen oder in Form von Unterschieden. Das Open-Source-Paket Fairlearn unterstützt zwei Klassen von Ungleichheitsmetriken:


- Ungleichheit bei der Modellleistung: Mit diesen Metriken wird die Ungleichheit (Differenz) der Werte der ausgewählten Leistungsmetrik für verschiedene Untergruppen berechnet. Beispiele hierfür sind:

  - Ungleichheit bei der Genauigkeitsrate
  - Ungleichheit bei der Fehlerrate
  - Ungleichheit bei der Präzision
  - Ungleichheit beim Abruf
  - Ungleichheit beim mittleren absoluten Fehler (Mean Absolute Error, MAE)
  - usw.

- Ungleichheit bei der Auswahlrate: Diese Metrik enthält die Differenz bei der Auswahlrate für verschiedene Untergruppen. Ein Beispiel hierfür ist die Ungleichheit bei der Darlehensbewilligungsrate. Mit Auswahlrate ist der Anteil von Datenpunkten in jeder Klasse, die als „1“ klassifiziert wurden (binäre Klassifizierung), oder die Verteilung von Vorhersagewerten (Regression) gemeint.

## <a name="mitigate-unfairness-in-machine-learning-models"></a>Ausgleichen von Unfairness bei Machine Learning-Modellen

### <a name="parity-constraints"></a>Gleichheitseinschränkungen

Das Open-Source-Paket Fairlearn umfasst eine Reihe von Algorithmen zur Abmilderung von Unfairness. Diese Algorithmen unterstützen eine Reihe von Einschränkungen für das Vorhersageverhalten, die als **Gleichheitseinschränkungen** oder -kriterien bezeichnet werden. Für Gleichheitseinschränkungen müssen einige Aspekte des Vorhersageverhaltens über die Gruppen hinweg vergleichbar sein, durch die sensible Features definiert werden (beispielsweise unterschiedliche Ethnien). Die Entschärfungsalgorithmen im Open-Source-Paket Fairlearn verwenden derartige Gleichheitseinschränkungen, um die beobachteten Fairnessprobleme abzumildern.

>[!NOTE]
> Die Entschärfung von Unfairness in einem Modell bedeutet, dass die Unfairness verringert wird. Durch diese technische Entschärfung lässt sich die Unfairness jedoch nicht vollständig beseitigen.  Die Algorithmen zur Entschärfung von Unfairness im Open-Source-Paket Fairlearn können Entschärfungsstrategien empfehlen, die dazu beitragen, die Unfairness in einem Machine Learning-Modell zu verringern. Sie stellen jedoch keine Lösung dar, um Unfairness vollständig zu beseitigen.  Möglicherweise gibt es weitere Gleichheitseinschränkungen oder Kriterien, die für das Machine Learning-Modell eines jeden Entwicklers berücksichtigt werden sollten. Entwickler, die Azure Machine Learning verwenden, müssen selbst entscheiden, ob die Entschärfung eine etwaige Unfairness in der vorgesehenen Verwendung und Bereitstellung von Machine Learning-Modellen ausreichend beseitigt.  

Das Open-Source-Paket Fairlearn unterstützt folgende Arten von Gleichheitseinschränkungen: 

|Gleichheitseinschränkung  | Zweck  |ML-Aufgabe  |
|---------|---------|---------|
|Demografische Gleichheit     |  Vermeiden von Nachteilen bei der Zuteilung | Binäre Klassifizierung, Regression |
|Ausgeglichene Chancen  | Diagnostizieren von Nachteilen bei der Zuteilung und bei der Servicequalität | Binäre Klassifizierung        |
|Chancengleichheit | Diagnostizieren von Nachteilen bei der Zuteilung und bei der Servicequalität | Binäre Klassifizierung        |
|Begrenzter Gruppenverlust     |  Vermeidung von Nachteilen bei der Servicequalität | Regression |



### <a name="mitigation-algorithms"></a>Entschärfungsalgorithmen

Das Open-Source-Paket Fairlearn bietet Entschärfungsalgorithmen für die Nachbearbeitung und die Reduzierung von Unfairness:

- Reduzierung: Bei diesen Algorithmen wird ein standardmäßiger Blackbox-Machine Learning-Schätzer (z. B. ein LightGBM-Modell) verwendet und unter Verwendung einer Sequenz neu gewichteter Trainingsdatasets eine Reihe neu trainierter Modelle generiert. So kann beispielsweise die Gewichtung von Bewerbern eines bestimmten Geschlechts erhöht oder verringert werden, um Modelle neu zu trainieren und Ungleichheiten zwischen verschiedenen Geschlechtergruppen zu verringern. Benutzer können dann ein Modell auswählen, das den besten Kompromiss zwischen Genauigkeit (oder einer anderen Leistungsmetrik) und der Ungleichheit darstellt, wobei im Allgemeinen Geschäftsregeln und Kostenkalkulationen berücksichtigt werden müssen.  
- Nachbearbeitung: Bei diesen Algorithmen werden als Eingabe ein vorhandener Klassifizierer und das sensible Feature herangezogen. Anschließend wird von der Vorhersage des Klassifizierers eine Transformation abgeleitet, um die angegebenen Fairnesseinschränkungen zu erzwingen. Der größte Vorteil der Schwellenwertoptimierung ist ihre Einfachheit und Flexibilität, da das Modell nicht neu trainiert werden muss. 

| Algorithmus | BESCHREIBUNG | ML-Aufgabe | Sensible Features | Unterstützte Gleichheitseinschränkungen | Algorithmustyp |
| --- | --- | --- | --- | --- | --- |
| `ExponentiatedGradient` | Unter [Ein Reduzierungsansatz zu fairer Klassifizierung](https://arxiv.org/abs/1803.02453) beschriebener Blackbox-Ansatz für eine faire Klassifizierung | Binäre Klassifizierung | Kategorisch | [Demografische Gleichheit](#parity-constraints), [Ausgeglichene Chancen](#parity-constraints) | Reduzierung |
| `GridSearch` | Unter [Ein Reduzierungsansatz zu fairer Klassifizierung](https://arxiv.org/abs/1803.02453) beschriebener Blackbox-Ansatz| Binäre Klassifizierung | Binary | [Demografische Gleichheit](#parity-constraints), [Ausgeglichene Chancen](#parity-constraints) | Reduzierung |
| `GridSearch` | Blackbox-Ansatz, bei dem eine Rastersuchvariante der fairen Regression mit dem Algorithmus für den begrenzten Gruppenverlust implementiert wird, wie unter [Faire Regression: Quantitative Definitionen und reduzierungsbasierte Algorithmen](https://arxiv.org/abs/1905.12843) beschrieben | Regression | Binary | [Begrenzter Gruppenverlust](#parity-constraints) | Reduzierung |
| `ThresholdOptimizer` | Nachbearbeitungsalgorithmus auf der Grundlage von [Chancengleichheit bei beaufsichtigtem Lernen](https://arxiv.org/abs/1610.02413). Bei dieser Technik werden als Eingabe ein vorhandener Klassifizierer und ein sensibles Feature verwendet. Anschließend wird eine monotone Transformation von der Vorhersage des Klassifizierers abgeleitet, um die angegebenen Gleichheitseinschränkungen zu erzwingen. | Binäre Klassifizierung | Kategorisch | [Demografische Gleichheit](#parity-constraints), [Ausgeglichene Chancen](#parity-constraints) | Nachbearbeitung |

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie in der folgenden Dokumentation zu Fairlearn mehr darüber, wie Sie die verschiedenen Komponenten verwenden: [GitHub](https://github.com/fairlearn/fairlearn/), [Benutzerhandbuch](https://fairlearn.github.io/master/user_guide/index.html), [Beispiele](https://fairlearn.github.io/master/auto_examples/) und [Beispielnotebooks](https://github.com/fairlearn/fairlearn/tree/master/notebooks).
- In der [Anleitung](how-to-machine-learning-fairness-aml.md) wird beschrieben, wie Sie die Fairnessbewertung von Machine Learning-Modellen in Azure Machine Learning aktivieren.
- Weitere Szenarios zur Fairnessbewertung in Azure Machine Learning finden Sie in den [Beispielnotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness). 

---
title: Modellinterpretierbarkeit in Azure Machine Learning (Vorschauversion)
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mithilfe des Python-SDK für Azure Machine Learning verstehen und erläutern können, wie Ihr Machine Learning-Modell während des Trainings Vorhersagen macht und Rückschlüsse zieht.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 11/16/2020
ms.openlocfilehash: dff0aeaf84ce87ed728d333cb68aee3a349bc111
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94699388"
---
# <a name="model-interpretability-in-azure-machine-learning-preview"></a>Modellinterpretierbarkeit in Azure Machine Learning (Vorschauversion)


## <a name="overview-of-model-interpretability"></a>Übersicht über die Modellinterpretierbarkeit

Die Interpretierbarkeit ist wichtig für Data Scientists, Prüfer und Entscheidungsträger im Unternehmen, um die Einhaltung von Unternehmensrichtlinien, Industriestandards und behördlichen Vorschriften zu gewährleisten:

+ Data Scientists müssen die Möglichkeit haben, ihre Modelle für Führungskräfte und Projektbeteiligte zu erklären, damit diese den Wert und die Genauigkeit ihrer Ergebnisse verstehen können. Sie erfordern auch die Interpretierbarkeit, um ihre Modelle zu debuggen und fundierte Entscheidungen darüber zu treffen, wie sie verbessert werden können. 

+ Rechtliche Prüfer benötigen Tools zur Validierung von Modellen im Hinblick auf die Einhaltung gesetzlicher Bestimmungen und zur Überwachung, wie sich die Entscheidungen der Modelle auf den Menschen auswirken. 

+ Entscheidungsträger im Unternehmen müssen zu ihrer Entlastung Transparenz für Endbenutzer bereitstellen können. Dadurch können sie sich das Vertrauen dauerhaft erarbeiten.


Die Möglichkeit, ein Machine Learning-Modell erklären zu können, ist in zwei Hauptphasen der Modellentwicklung wichtig:
+ Während der Trainingsphase, in der Modellentwickler und -bewerter die Interpretierbarkeitsausgabe eines Modells verwenden können, um Hypothesen zu überprüfen und Vertrauen bei den Beteiligten aufzubauen. Sie verwenden außerdem die Erkenntnisse aus dem Modell zum Debuggen, zum Überprüfen des Modellverhaltens auf die Erfüllung der Ziele und zum Suchen nach Modellungerechtigkeiten oder nicht signifikanten Features.

+ Während der Rückschlussphase können Führungskräfte bei bereitgestellten Modellen mithilfe von Transparenz im Zusammenhang mit der Modellbereitstellung erfahren, wie das Modell bei der Bereitstellung funktioniert und wie sich seine Entscheidungen in echten Situationen auf Menschen auswirken. 

## <a name="interpretability-with-azure-machine-learning"></a>Interpretierbarkeit mit Azure Machine Learning

Die Interpretierbarkeitsklassen werden über das folgende SDK-Paket zur Verfügung gestellt: (Erfahren Sie, wie Sie [SDK-Pakete für Azure Machine Learning installieren](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).)

* `azureml.interpret`, enthält von Microsoft unterstützte Funktionen.

Verwenden Sie `pip install azureml-interpret` für allgemeine Zwecke.

## <a name="how-to-interpret-your-model"></a>Interpretieren des Modells

Durch Verwenden der Klassen und Methoden im SDK haben Sie folgende Möglichkeiten:
+ Erläutern der Modellvorhersage durch Generieren von Featurerelevanzwerten für das gesamte Modell und/oder einzelne Datenpunkte. 
+ Erreichen der Modellinterpretierbarkeit realer Datasets in beliebigem Umfang während des Trainings und in der Rückschlussphase.
+ Verwenden eines interaktiven Visualisierungsdashboards zum Ermitteln von Mustern in Daten und Erklärungen zur Trainingszeit.


Beim maschinellen Lernen sind **Merkmale** diejenigen Datenfelder, die zum Vorhersagen eines Zieldatenpunkts verwendet werden. Um beispielsweise ein Kreditrisiko vorherzusagen, werden möglicherweise Datenfelder für Alter, Kontogröße und Kontoalter verwendet. In diesem Fall sind Alter, Kontogröße und Kontoalter **Merkmale**. Die Relevanz eines Merkmals informiert darüber, wie sich jedes Datenfeld auf die Vorhersagen des Modells ausgewirkt hat. Das Alter spielt möglicherweise bei der Vorhersage eine schwerer wiegende Rolle, wohingegen Kontogröße und Kontoalter sich nur unwesentlich auf die Vorhersagewerte auswirken. Dank dieses Prozesses können Data Scientists die getroffenen Vorhersagen erklären, sodass alle Beteiligten einen Einblick darin erhalten, welche Features die wichtigsten im Modell sind.

Erfahren Sie hier mehr über unterstützte Interpretierbarkeitstechniken, unterstützte Machine Learning-Modelle und unterstützte Ausführungsumgebungen.


## <a name="supported-interpretability-techniques"></a>Unterstützte Interpretierbarkeitstechniken

 `azureml-interpret` verwendet die Interpretierbarkeitstechniken, die in [Interpret-Community](https://github.com/interpretml/interpret-community/) entwickelt wurden, einem Open-Source-Python-Paket zum Trainieren von interpretierbaren Modellen und zur Unterstützung bei der Erläuterung von Blackbox-KI-Systemen. [Interpret-Community](https://github.com/interpretml/interpret-community/) fungiert als Host für die unterstützten Explainer dieses SDK und unterstützt derzeit die folgenden Interpretierbarkeitstechniken:

|Interpretierbarkeitstechnik|BESCHREIBUNG|type|
|--|--|--------------------|
|SHAP Tree Explainer| Der Tree Explainer von [SHAP](https://github.com/slundberg/shap), der auf den schnellen Schätzalgorithmus für Werte von SHAP für die Polynomialzeit speziell für **Baumstrukturen und Gruppen von Baumstrukturen** ausgelegt ist.|Modellspezifisch|
|SHAP Deep Explainer| Basierend auf der Erläuterung von SHAP ist DeepExplainer „ein schneller Näherungsalgorithmus für SHAP-Werte in Deep-Learning-Modellen, der auf einer im [NIPS-Paper über SHAP](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions) beschriebenen Verbindung mit DeepLIFT aufbaut. **TensorFlow**- und **Keras**-Modelle, die das TensorFlow-Back-End verwenden, werden unterstützt (es gibt auch eine vorläufige Unterstützung für PyTorch)“.|Modellspezifisch|
|SHAP Linear Explainer| LinearExplainer in SHAP berechnet SHAP-Werte für ein **lineares Modell** und erklärt optional Korrelationen zwischen Features.|Modellspezifisch|
|SHAP Kernel Explainer| KernelExplainer in SHAP verwendet eine speziell gewichtete lokale lineare Regression zum Schätzen von SHAP-Werten für **alle Modelle**.|Modellagnostisch|
|Mimic Explainer (Globaler Surrogat)| Der Mimic Explainer basiert auf der Idee des Trainierens von [globalen Surrogatmodellen](https://christophm.github.io/interpretable-ml-book/global.html) zur Nachahmung von Blackboxmodellen. Ein globales Surrogatmodell ist ein eigenständig interpretierbares Modell, das trainiert wird, um die Vorhersagen **eines Blackbox-Modells** so genau wie möglich zu treffen. Data Scientists können das Surrogatmodell interpretieren, um Rückschlüsse in Bezug auf das Blackbox-Modell zu ziehen. Sie können eines der folgenden interpretierbaren Modelle als Surrogatmodell verwenden: LightGBM (LGBMExplainableModel), Linear Regression (LinearExplainableModel), Stochastic Gradient Descent explainable model (SGDExplainableModel) und Decision Tree (DecisionTreeExplainableModel).|Modellagnostisch|
|Permutation Feature Importance Explainer (PFI)| Permutation Feature Importance ist ein Verfahren zum Erklären von Klassifizierungs- und Regressionsmodellen, das auf dem [Random Forests Paper von Breiman](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (siehe Abschnitt 10) basiert. Die grobe Funktionsweise besteht darin, nach dem Zufallsprinzip die Daten des gesamten Datasets für jeweils ein Feature zu vermischen und dann zu berechnen, wie stark sich die gewünschte Leistungsmetrik ändert. Je größer die Änderung, desto wichtiger ist das Feature. PFI kann das Gesamtverhalten **jedes zugrunde liegenden Modells** erklären, erläutert aber nicht einzelne Vorhersagen. |Modellagnostisch|




Neben den oben beschriebenen Interpretierbarkeitstechniken wird ein weiterer SHAP-basierter Explainer namens `TabularExplainer` unterstützt. Je nach Modell verwendet `TabularExplainer` einen der unterstützten SHAP-Explainer:

* TreeExplainer für alle baumstrukturbasierten Modelle
* DeepExplainer für DNN-Modelle
* LinearExplainer für lineare Modelle
* KernelExplainer für alle anderen Modelle

`TabularExplainer` hat auch signifikante Feature- und Leistungsverbesserungen gegenüber den direkten SHAP-Explainern zu verzeichnen:

* **Zusammenfassung des Initialisierungsdatasets**. In den Fällen, in denen die Erklärungsgeschwindigkeit am wichtigsten ist, fassen wir das Initialisierungsdataset zusammen und erzeugen einen kleinen Satz repräsentativer Stichproben, wodurch die Generierung von Gesamt- und Einzelwerten für die Featurerelevanz beschleunigt wird.
* **Stichprobenentnahme im Auswertungsdataset**. Wenn der Benutzer eine Vielzahl von Auswertungsstichproben übergibt, aber nicht alle davon ausgewertet werden müssen, kann der Stichprobenparameter auf TRUE festgelegt werden, um die Berechnung von Gesamtmodellerklärungen zu beschleunigen.

Im folgenden Diagramm ist die aktuelle Struktur von unterstützten Explainers dargestellt.

[![Architektur von Machine Learning Interpretability](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>Unterstützte Machine Learning-Modelle

Das `azureml.interpret`-Paket des SDK unterstützt Modelle, die mit den folgenden Datasetformaten trainiert wurden:
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

Die Erklärungsunktionen akzeptieren sowohl Modelle als auch Pipelines als Eingabe. Wenn ein Modell bereitgestellt wird, muss das Modell die Vorhersagefunktion `predict` oder `predict_proba` implementieren, die der Scikit-Konvention entspricht. Wenn Ihr Modell dies nicht unterstützt, können Sie Ihr Modell mit einer Funktion umschließen, die dasselbe Ergebnis wie `predict` oder `predict_proba` in Scikit erzeugt, und diese Wrapperfunktion mit dem ausgewählten Explainer verwenden. Wenn eine Pipeline angegeben wird, nimmt die Erklärungsfunktion an, dass das ausgeführte Pipelineskript eine Vorhersage zurückgibt. Mit dieser Umschließungstechnik kann `azureml.interpret` sowohl Modelle, die über PyTorch-, TensorFlow- und Keras-Deep Learning Frameworks trainiert werden, als auch klassische Machine Learning-Modelle unterstützen.

## <a name="local-and-remote-compute-target"></a>Lokales- und Remotecomputeziel

Das Paket `azureml.interpret` funktioniert mit lokalen und Remotecomputezielen. Bei lokaler Ausführung werden über die SDK-Funktionen keine Verbindungen mit Azure-Diensten hergestellt. 

Sie können die Erklärung per Remotezugriff in Azure Machine Learning Compute ausführen und die Erklärungsinformationen im Ausführungsverlauf von Azure Machine Learning Service protokollieren. Nachdem diese Informationen protokolliert wurden, stehen Berichte und Visualisierungen aus der Erklärung im Azure Machine Learning Studio für die Analyse durch den Benutzer bereit.


## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Aktivierung der Interpretierbarkeit für das Modelltraining, sowohl lokal als auch auf Azure Machine Learning-Remotecomputeressourcen, finden Sie in der [Schrittanleitung](how-to-machine-learning-interpretability-aml.md). 
- Weitere Szenarien finden Sie in den [Beispielnotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model). 
- Wenn Sie sich für Interpretierbarkeit für Textszenarios interessieren, finden Sie Interpretierbarkeitstechniken für NLP in [Interpret-text](https://github.com/interpretml/interpret-text). Hierbei handelt es sich um ein Open-Source-Repository für [Interpret-Community](https://github.com/interpretml/interpret-community/). Das Paket `azureml.interpret` unterstützt diese Techniken derzeit noch nicht, aber Sie können mit einem [Beispielnotebook für Textklassifizierung](https://github.com/interpretml/interpret-text/blob/master/notebooks/text_classification/text_classification_classical_text_explainer.ipynb) beginnen.
---
title: Welche Möglichkeiten bietet Machine Learning Studio (Classic) in Azure?
description: Azure Machine Learning Studio (Classic) ist ein Drag & Drop-Tool zum schnellen Erstellen von Modellen auf der Grundlage einer sofort einsatzbereiten Bibliothek mit Algorithmen und Modulen.
services: machine-learning
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.topic: overview
ms.date: 08/19/2020
ms.openlocfilehash: 0a18f9adb8c3723619b44b30bfa334edf4300c0d
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95509487"
---
# <a name="what-can-i-do-with-machine-learning-studio-classic"></a>Welche Möglichkeiten bietet Machine Learning Studio (Classic)?

**GILT FÜR:**  ![Dies ist ein Häkchen, d. h., dieser Artikel bezieht sich auf Machine Learning Studio (Classic). ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (Classic)   ![Dies ist ein X, d. h., dieser Artikel bezieht sich auf Azure Machine Learning. ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Machine Learning Studio (Classic) ist ein Drag & Drop-Tool, mit dem Sie Machine Learning-Modelle erstellen, testen und bereitstellen können. Bei Studio (Classic) werden Modelle als Webdienste veröffentlicht, die von benutzerdefinierten Apps oder BI-Tools wie Excel problemlos genutzt werden können.


## <a name="studio-classic--interactive-workspace"></a>Der interaktive Arbeitsbereich von Studio (Classic)

Für die Entwicklung eines prädiktiven Analysemodells verwendet man typischerweise Daten aus einer oder mehreren Quellen, transformiert und analysiert diese Daten mithilfe verschiedener Datenbearbeitungen und Statistikfunktionen und generiert einen Ergebnissatz. Das Entwickeln eines solchen Modells ist ein iterativer Prozess. Sie ändern die verschiedenen Funktionen und deren Parameter, und die Ergebnisse nähern sich an, bis Sie der Ansicht sind, dass Sie ein trainiertes, effektives Modell erreicht haben.

Azure Machine Learning Studio (Classic) stellt Ihnen einen interaktiven, visuellen Arbeitsbereich zur Verfügung, in dem Sie ein Vorhersageanalysemodell ganz einfach entwickeln, testen und durchlaufen können. Dazu ziehen Sie **_Datasets_* _ und _*_Analysemodule_*_ per Drag & Drop auf eine interaktive Canvas und verbinden sie zu einem _*_Experiment_*_, das Sie dann in Machine Learning Studio (klassisch) ausführen. Für die Iteration des Modelldesigns bearbeiten Sie das Experiment, speichern ggf. eine Kopie und führen es erneut aus. Wenn Sie bereit sind, können Sie Ihr _*_Trainingsexperiment_*_ in ein _*_Vorhersageexperiment_*_ konvertieren und anschließend als _*_Webdienst_*_ veröffentlichen, damit andere Benutzer auf das Modell zugreifen können.

Es ist keinerlei Programmierung erforderlich. Sie müssen lediglich Datasets und Module auf der grafischen Benutzeroberfläche miteinander verbinden, um Ihr Predictive Analytics-Modell zu erstellen.

![Diagramm von Machine Learning Studio (Classic): Erstellen von Experimenten, Lesen von Daten aus vielen Quellen, Schreiben der ausgewerteten Daten, Erstellen von Modellen.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-ml-studio-classic-overview-diagram"></a>Herunterladen des Übersichtsdiagramms für ML Studio (Classic)
Laden Sie das _ *Diagramm mit der Übersicht über die Funktionen von Microsoft ML Studio (klassisch)* * herunter, und verschaffen Sie sich einen allgemeinen Überblick über die Funktionen von Machine Learning Studio (klassisch). Drucken Sie das Diagramm in Tabloid-Größe (28 x 43 cm, 11 x 17 Zoll) aus, um schnell darauf zugreifen zu können.

**Laden Sie das Diagramm hier herunter: [Übersicht über die Funktionen von Microsoft Machine Learning Studio (Classic)](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)** 
![Übersicht über die Funktionen von Microsoft Machine Learning Studio (Classic)](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)


## <a name="components-of-a-studio-classic--experiment"></a>Komponenten eines Studio-Experiments (klassisch)
Ein Experiment besteht auf Datasets, die Daten für Analysemodule bereitstellen, die Sie miteinander verbinden und so ein Vorhersagemodell konstruieren. Ein gültiges Experiment hat spezifisch folgende Eigenschaften:

* Das Experiment besteht aus mindestens einem Dataset und einem Modul.
* Datasets können nur mit Modulen verbunden sein.
* Module können entweder mit Datasets oder mit anderen Modulen verbunden sein.
* Alle Eingangsports für Module müssen eine Verbindung zum Datenfluss aufweisen.
* Alle erforderlichen Parameter der einzelnen Module müssen festgelegt sein.

Sie können ein Experiment von Grund auf neu erstellen, oder Sie können ein vorhandenes Beispielexperiment als Vorlage verwenden. Weitere Informationen finden Sie unter [Kopieren von Beispielexperimenten zum Erstellen neuer Machine Learning-Experimente](sample-experiments.md).

Ein Beispiel für die Erstellung eines Experiments finden Sie unter [Schnellstart: Erstellen Ihres ersten Data Science-Experiments in Machine Learning Studio (Classic)](create-experiment.md).

Eine ausführlichere exemplarische Vorgehensweise zum Erstellen einer Lösung für Vorhersageanalysen finden Sie unter [Tutorial 1: Vorhersagen des Kreditrisikos: Azure Machine Learning Studio (Classic)](tutorial-part1-credit-risk.md).

### <a name="datasets"></a>Datasets
Bei einem Dataset handelt es sich um Daten, die in Machine Learning Studio (klassisch) hochgeladen wurden, damit sie für den Modellierungsprozess zur Verfügung stehen. Machine Learning Studio (klassisch) enthält eine Reihe von Beispieldatasets, mit denen Sie experimentieren können. Bei Bedarf können Sie außerdem weitere Datasets hochladen. Hier einige Beispiele der enthaltenen Datasets:

* **MPG-Daten für verschiedene Autos**: Meilen-pro-Gallone-Werte für Autos, die anhand der Anzahl von Zylindern, Motorleistung usw. aufgeführt sind.
* **Brustkrebsdaten:** diagnostische Brustkrebsdaten.
* **Waldbranddaten:** Größe von Waldbränden im nordöstlichen Portugal.

Beim Erstellen eines Experiments können Sie links neben dem Arbeitsbereich aus der Liste der Datasets auswählen.

Eine Liste der in Machine Learning Studio (Classic) enthaltenen Beispieldatasets finden Sie unter [Verwenden von Beispieldatasets in Azure Machine Learning Studio (Classic)](use-sample-datasets.md).

### <a name="modules"></a>Module
Ein Modul ist ein Algorithmus, den Sie auf Ihre Daten anwenden können. Machine Learning Studio (Classic) hat eine Reihe von Modulen, die von Funktionen für die Dateneinspeisung bis zu Trainings-, Bewertungs- und Überprüfungsvorgängen reichen. Hier einige Beispiele der enthaltenen Module:

* [In ARFF konvertieren:][convert-to-arff] Konvertiert ein serialisiertes .NET-Dataset in ARFF (Attribute-Relation File Format).
* [Elementare Statistiken berechnen:][elementary-statistics] Berechnet elementare Statistiken wie Mittelwert, Standardabweichung usw.
* [Lineare Regression:][linear-regression] Erstellt ein lineares Regressionsmodell, das auf einem Modell für den Onlinegradientenabstieg beruht.
* [Bewertungsmodell:][score-model] Bewertet ein trainiertes Klassifizierungs- oder Regressionsmodell.

Beim Erstellen eines Experiments können Sie links neben dem Arbeitsbereich aus der Liste der Module auswählen.

Ein Modul kann eine Reihe von Parametern haben, die Sie zum Konfigurieren der internen Algorithmen des Moduls einsetzen können. Wenn Sie ein Modul im Arbeitsbereich auswählen, werden dessen Parameter rechts neben dem Arbeitsbereich im Bereich **Eigenschaften** angezeigt. Sie können die Parameter in diesem Bereich zur Abstimmung Ihres Modells verändern.

Hilfe zur Navigation durch die umfassende Bibliothek verfügbarer Machine Learning-Algorithmen finden Sie unter [Auswählen von Algorithmen für Azure Machine Learning](../how-to-select-algorithms.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Bereitstellen eines Predictive Analytics-Webdiensts
Wenn Ihr Predictive Analytics-Modell bereit ist, können Sie es als Webdienst direkt von Machine Learning Studio (klassisch) aus bereitstellen. Weitere Informationen zu diesem Verfahren finden Sie unter [Bereitstellen eines Azure Machine Learning-Webdiensts](deploy-a-machine-learning-web-service.md).

## <a name="next-steps"></a>Nächste Schritte
Die Grundlagen von Predictive Analytics und maschinellem Lernen werden anhand einer [Schritt-für-Schritt-Schnellstartanleitung](create-experiment.md) und mithilfe von [Beispielen](sample-experiments.md) vermittelt.

<!-- Module References -->
[convert-to-arff]: /azure/machine-learning/studio-module-reference/convert-to-arff
[elementary-statistics]: /azure/machine-learning/studio-module-reference/compute-elementary-statistics
[linear-regression]: /azure/machine-learning/studio-module-reference/linear-regression
[score-model]: /azure/machine-learning/studio-module-reference/score-model
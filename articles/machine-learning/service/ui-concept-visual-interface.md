---
title: Grafische Benutzeroberfläche
titleSuffix: Azure Machine Learning service
description: Hier erhalten Sie Informationen zu Terminologie, Konzepten und Workflows der grafischen Benutzeroberfläche (Vorschauversion) für Azure Machine Learning Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/15/2019
ms.openlocfilehash: be07e0f3438ea93312d4eb440e7e63b8f98e11b8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077376"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning-service"></a>Was ist die grafische Benutzeroberfläche für Azure Machine Learning Service? 

Die grafischen Benutzeroberfläche (Vorschauversion) für Azure Machine Learning Service unterstützt Sie bei der Datenaufbereitung für und beim Trainieren, Testen, Bereitstellen, Verwalten und Überwachen von Machine Learning-Modellen, ohne dass Sie dafür Code schreiben müssen.

Für das Erstellen Ihres Modells ist keine Programmierung erforderlich, sondern einfach nur das visuelle Verbinden von [Datasets](#dataset) und [Modulen](#module).

Die grafische Benutzeroberfläche verwendet Ihren Azure Machine Learning Service-[Workspace](concept-workspace.md) für die folgenden Zwecke:

+ Schreiben von Artefakten von [Experimentausführungen](#experiment) im Workspace
+ Zugriff auf [Datasets](#dataset)
+ Verwenden der [Computeressourcen](#compute) im Workspace, um das Experiment auszuführen 
+ Registrieren von [Modellen](concept-azure-machine-learning-architecture.md#models)
+ [Bereitstellen](#deployment) von Modellen als Webdienste auf Computeressourcen im Workspace

![Übersicht über die grafische Benutzeroberfläche](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Workflow

Im Rahmen der grafischen Benutzeroberfläche erhalten Sie eine interaktive, visuelle Canvas, um ein Modell schnell erstellen, testen und durchlaufen zu können. 

+ [Module](#module) lassen sich per Drag & Drop-Verfahren auf der Canvas platzieren.
+ Verbinden Sie die Module miteinander, um ein [Experiment](#experiment) zu erstellen.
+ Führen Sie das Experiment mithilfe der Computeressource des Machine Learning Service-Workspace aus.
+ Durchlaufen Sie Ihr Modellentwurf, indem Sie das Experiment bearbeiten und erneut ausführen.
+ Wenn Sie soweit sind, konvertieren Sie Ihr **Trainingsexperiment** in ein **Vorhersageexperiment**.
+ Dann können Sie Ihr Vorhersageexperiment als Webdienst [bereitstellen](#deployment), damit andere Personen auf Ihr Modell zugreifen können.

## <a name="experiment"></a>Experiment

Sie können ein Experiment von Grund auf neu erstellen, oder Sie können ein vorhandenes Beispielexperiment als Vorlage verwenden.  Jedes Mal, wenn Sie ein Experiment ausführen, werden Artefakte in Ihrem Workspace gespeichert.

Ein Experiment besteht aus Datasets und Analysemodulen, die Sie miteinander verbinden und so ein Modell konstruieren. Ein gültiges Experiment hat spezifisch folgende Eigenschaften:

* Datasets können nur mit Modulen verbunden sein.
* Module können entweder mit Datasets oder mit anderen Modulen verbunden sein.
* Alle Eingangsports für Module müssen eine Verbindung zum Datenfluss aufweisen.
* Alle erforderlichen Parameter der einzelnen Module müssen festgelegt sein.

Ein Beispiel eines einfachen Experiments finden Sie unter [Schnellstart: Vorbereiten und Visualisieren von Daten in Azure Machine Learning ohne Schreiben von Code](ui-quickstart-run-experiment.md).

Eine vollständige exemplarische Vorgehensweise für eine Predictive Analytics-Lösung finden Sie unter [Tutorial: Prognostizieren von Automobilpreisen mithilfe der grafischen Benutzeroberfläche](ui-tutorial-automobile-price-train-score.md).

## <a name="dataset"></a>Datensatz

Ein Dataset besteht aus Daten, die in die grafische Benutzeroberfläche zur Verwendung im Modellierungsprozess hochgeladen wurden. Eine Reihe von Beispiel-Datasets sind bereits enthalten, mit denen Sie experimentieren können, und bei Bedarf lassen sich weitere hochladen.

## <a name="module"></a>Modul

Ein Modul ist ein Algorithmus, den Sie auf Ihre Daten anwenden können. Die grafische Benutzeroberfläche verfügt über eine Reihe von Modulen, die von Funktionen für die Dateneinspeisung bis zu Trainings-, Bewertungs- und Überprüfungsvorgängen reichen.

Ein Modul kann eine Reihe von Parametern haben, die Sie zum Konfigurieren der internen Algorithmen des Moduls einsetzen können. Wenn Sie ein Modul auf der Canvas auswählen, werden dessen Parameter rechts neben der Canvas im Bereich „Eigenschaften“ angezeigt. Sie können die Parameter in diesem Bereich zur Abstimmung Ihres Modells verändern.

![Moduleigenschaften](media/ui-concept-visual-interface/properties.png)

Unterstützung bei der Navigation durch die Bibliothek der verfügbaren Machine Learning-Algorithmen finden Sie unter [Algorithmen und Module: Referenzübersicht](../algorithm-module-reference/module-reference.md).

## <a name="compute"></a> Computeressourcen

Verwenden Sie Computeressourcen aus Ihrem Workspace, um Ihr Experiment auszuführen oder Ihre bereitgestellten Modelle als Webdienste zu hosten. Die unterstützten Computeziele lauten:


| Computeziel | Training | Bereitstellung |
| ---- |:----:|:----:|
| Azure Machine Learning Compute | ✓ | |
| Azure Kubernetes Service | | ✓ |

Computeziele sind in Ihren Machine Learning-[Workspace](concept-workspace.md) integriert. Ihre Computeziele verwalten Sie in Ihrem Workspace im [Azure-Portal](https://portal.azure.com).

## <a name="deployment"></a>Bereitstellung

Wenn Ihr Predictive Analytics-Modell bereit ist, können Sie es als Webdienst direkt über die grafische Benutzeroberfläche bereitstellen.

Webdienste stellen eine Schnittstelle zwischen einer Anwendung und Ihrem Bewertungsmodell zur Verfügung. Eine externe Anwendung kann in Echtzeit mit einem Bewertungsmodell kommunizieren. Mit einem Aufruf eines Webdiensts werden Vorhersageergebnisse an eine externe Anwendung zurückgegeben. Zur Durchführung eines Webdienstaufrufs übergeben Sie einen API-Schlüssel, der beim Bereitstellen des Webdiensts erstellt wurde. Ein Webdienst basiert auf REST, einer verbreiteten Architektur für Webprogrammierungsprojekte.

Informationen dazu, wie Sie Ihr Modell bereitstellen, finden Sie unter [Tutorial: Bereitstellen eines Machine Learning-Modells mithilfe der grafischen Benutzeroberfläche](ui-tutorial-automobile-price-deploy.md).

## <a name="next-steps"></a>Nächste Schritte

* Lernen Sie die Grundlagen von Predictive Analytics und maschinellem Lernen kennen mit [Schnellstart: Vorbereiten und Visualisieren von Daten in Azure Machine Learning ohne Schreiben von Code](ui-quickstart-run-experiment.md).
* Verwenden Sie eines der Beispiele und passen Sie es an Ihre Anforderungen an:
    * [Beispiel 1 – Regression: Preisprognose](ui-sample-regression-predict-automobile-price-basic.md)
    * [Beispiel 2 – Regression: Vorhersagen des Preises und Vergleichen von Algorithmen](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [Beispiel 3 – Klassifizierung: Vorhersagen des Kreditrisikos](ui-sample-classification-predict-credit-risk-basic.md)
    * [Beispiel 4 – Klassifizierung: Vorhersagen des Kreditrisikos (kostensensibel)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [Beispiel 5 – Klassifizierung: Vorhersage von Kundenabwanderung, Kauflust und Up-Selling](ui-sample-classification-predict-churn.md)

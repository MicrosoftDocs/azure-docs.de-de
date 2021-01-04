---
title: Was ist Azure Machine Learning Studio?
description: Studio ist ein Webportal für Azure Machine Learning-Arbeitsbereiche. In Studio werden codefreie Umgebungen und Code First-Umgebungen zu einer inklusiven Data Science-Plattform kombiniert.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: peterclu
ms.author: peterlu
ms.date: 08/24/2020
ms.openlocfilehash: 018a0e3a244d1f0e36ce638d646ab05861c10e38
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565907"
---
# <a name="what-is-azure-machine-learning-studio"></a>Was ist Azure Machine Learning Studio?

In diesem Artikel erfahren Sie mehr über Azure Machine Learning Studio, das Webportal für Data Scientist-Entwickler in [Azure Machine Learning](overview-what-is-azure-ml.md). In Studio werden codefreie Umgebungen und Code First-Umgebungen zu einer inklusiven Data Science-Plattform kombiniert.

In diesem Artikel wird Folgendes behandelt:
>[!div class="checklist"]
> - [Erstellen von Machine Learning-Projekten](#author-machine-learning-projects) in Studio
> - [Verwalten von Assets und Ressourcen](#manage-assets-and-resources) in Studio
> - Unterschiede zwischen [Azure Machine Learning Studio und Machine Learning Studio (Classic)](#ml-studio-classic-vs-azure-machine-learning-studio)


## <a name="author-machine-learning-projects"></a>Erstellen von Machine Learning-Projekten

Studio bietet abhängig vom Projekttyp und von der Erfahrung der Benutzer verschiedene Erstellungsumgebungen.

+ **Notebooks**

  Schreiben Sie eigenen Code, und führen Sie ihn auf verwalteten [Jupyter Notebook-Servern](how-to-run-jupyter-notebooks.md) aus, die direkt in Studio integriert sind. 

+ **Azure Machine Learning-Designer**

  Verwenden Sie den Designer zum Trainieren und Bereitstellen von Machine Learning-Modellen, ohne Code schreiben zu müssen. Verschieben Sie Datasets und Module per Drag & Drop, um ML-Pipelines zu erstellen. Probieren Sie das [Tutorial zum Designer](tutorial-designer-automobile-price-train-score.md) aus.

    ![Azure Machine Learning-Designer: Beispiel](media/concept-designer/designer-drag-and-drop.gif)

+ **Benutzeroberfläche für automatisiertes maschinelles Lernen**

  Informieren Sie sich darüber, wie Sie mit einer benutzerfreundlichen Oberfläche [automatisierte ML-Experimente](tutorial-first-experiment-automated-ml.md) erstellen. 

  [![Navigationsbereich von Azure Machine Learning Studio](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

+ **Datenbeschriftung**

    Verwenden Sie die [Azure Machine Learning-Datenbeschriftung](how-to-create-labeling-projects.md), um Datenbeschriftungsprojekte effizient zu koordinieren.

## <a name="manage-assets-and-resources"></a>Verwalten von Assets und Ressourcen

Verwalten Sie Ihre Machine Learning-Assets direkt im Browser. Assets werden im gleichen Arbeitsbereich zwischen dem SDK und Studio freigegeben, um eine einheitliche Oberfläche bereitzustellen. Verwenden Sie Studio für die Verwaltung folgender Elemente:

- Modelle
- Datasets
- Datenspeicher
- Computeressourcen
- Notebooks
- Experimente
- Ausführungsprotokolle
- Pipelines 
- Pipelineendpunkte

Selbst wenn Sie ein erfahrener Entwickler sind, kann Studio die Verwaltung von Arbeitsbereichsressourcen vereinfachen.

## <a name="ml-studio-classic-vs-azure-machine-learning-studio"></a>ML Studio (Classic) und Azure Machine Learning Studio

**ML Studio (Classic)** wurde 2015 veröffentlicht und war der erste Drag & Drop-Generator für maschinelles Lernen. Es handelt sich dabei um einen eigenständigen Dienst, der nur eine visuelle Umgebung bietet. Studio (Classic) funktioniert nicht mit Azure Machine Learning.

**Azure Machine Learning** ist ein separater und modernisierter Dienst, der eine umfassende Data Science-Plattform bereitstellt. Er unterstützt sowohl den Code First-Ansatz als auch Umgebungen mit wenig Code.

**Azure Machine Learning Studio** ist ein Webportal *in* Azure Machine Learning, das Optionen mit wenig Code und ohne Code für die Projekterstellung und Ressourcenverwaltung enthält. 

Neuen Benutzern wird empfohlen, **Azure Machine Learning** anstelle von ML Studio (Classic) zu wählen, um die aktuellen Data Science-Tools nutzen zu können.

### <a name="feature-comparison"></a>Funktionsvergleiche

In der folgenden Tabelle sind die wichtigsten Unterschiede zwischen ML Studio (Classic) und Azure Machine Learning zusammengefasst:

| Funktion | ML Studio (klassisch) | Azure Machine Learning |
|---| --- | --- |
| Drag & Drop-Oberfläche | Klassische Umgebung | Aktualisierte Umgebung – [Azure Machine Learning-Designer](concept-designer.md)| 
| Code SDKs | Nicht unterstützt | Vollständig integriert in [Azure Machine Learning Python](/python/api/overview/azure/ml/) und [R](tutorial-1st-r-experiment.md) SDKs |
| Experiment | Skalierbar (Limit für Trainingsdaten: 10 GB) | Skalieren mit Computeziel |
| Trainieren von Computezielen | Proprietäres Computeziel, nur CPU-Unterstützung | Viele anpassbare [Computeziele für das Training](concept-compute-target.md#train). GPU- und CPU-Unterstützung | 
| Computeziele für die Bereitstellung | Proprietäres Webdienstformat, nicht anpassbar | Viele anpassbare [Computeziele für die Bereitstellung](concept-compute-target.md#deploy). GPU- und CPU-Unterstützung |
| ML-Pipeline | Nicht unterstützt | Erstellung von flexiblen modularen [Pipelines](concept-ml-pipelines.md) zum Automatisieren von Workflows |
| MLOps | Grundlegende Modellverwaltung und Bereitstellung; reine CPU-Bereitstellungen | Entitätsversionierung (Modell, Daten, Workflows), Workflowautomatisierung, Integration in CICD-Tools, CPU- und GPU-Bereitstellungen [und mehr](concept-model-management-and-deployment.md) |
| Modellformat | Proprietäres Format, nur Studio (klassisch) | Mehrere unterstützte Formate, je nach Typ des Trainingsauftrags |
| Automatisiertes Modelltraining und Optimieren von Hyperparametern |  Nicht unterstützt | [Unterstützt](concept-automated-ml.md). Code First-Optionen und codefreie Optionen | 
| Datendrifterkennung | Nicht unterstützt | [Unterstützt](how-to-monitor-datasets.md) |
| Datenbezeichnungsprojekte | Nicht unterstützt | [Unterstützt](how-to-create-labeling-projects.md) |


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich [Studio](https://ml.azure.com) an, oder informieren Sie sich in den folgenden Tutorials ausführlicher über die verschiedenen Erstellungsoptionen:  

- + [Erste Schritte in Ihrer eigenen Entwicklungsumgebung](tutorial-1st-experiment-sdk-setup-local.md)
  + [Verwenden von Jupyter-Notebooks auf einer Compute-Instanz zum Trainieren und Bereitstellen von Modellen](tutorial-1st-experiment-sdk-setup.md)
  + [Tutorial: Erstellen eines Klassifizierungsmodells mit automatisiertem maschinellem Lernen in Azure Machine Learning](tutorial-first-experiment-automated-ml.md)  
  + [Tutorial: Prognostizieren von Automobilpreisen mit dem Designer (Vorschau)](tutorial-designer-automobile-price-train-score.md)
  + [Verwenden von Azure Machine Learning Studio in einem virtuellen Netzwerk](how-to-enable-studio-virtual-network.md)
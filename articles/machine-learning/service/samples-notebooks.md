---
title: Jupyter-Beispiel-Notebooks
titleSuffix: Azure Machine Learning service
description: Hier erfahren Sie, wie Sie Beispiele für Jupyter-Notebooks finden und verwenden, um sich mit dem Python-SDK für Azure Machine Learning Service vertraut zu machen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 05/29/2019
ms.custom: seodec18
ms.openlocfilehash: ea4d5a807c25ea0406b49dac8a83ef1a34e0e8b3
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391781"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Verwenden von Jupyter-Notebooks zum Erkunden des Azure Machine Learning-Diensts

Das [Notebookrepository für Azure Machine Learning](https://github.com/azure/machinelearningnotebooks) enthält die neuesten Python-SDK-Beispiele für Azure Machine Learning. Diese Juypter-Notebooks helfen Ihnen dabei, sich mit dem SDK vertraut zu machen, und fungieren als Modelle für Ihre eigenen Machine Learning-Projekte.

In diesem Artikel erfahren Sie, wie Sie von den folgenden Umgebungen aus auf das Repository zugreifen:

- [Azure Machine Learning-Notebook (virtueller Computer)](#azure-machine-learning-notebook-vm)
- [Eigener Notebookserver](#bring-your-own-jupyter-notebook-server)
- [Virtueller Computer für Data Science](#data-science-virtual-machine)
- [Azure Notebooks](#azure-notebooks)

> [!NOTE]
> Nach dem Klonen des Repositorys finden Sie Tutorialnotebooks im Ordner **tutorials** und featurespezifische Notebooks im Ordner **how-to-use-azureml**.

## <a name="azure-machine-learning-notebook-vm"></a>Azure Machine Learning-Notebook (virtueller Computer)

Den einfachsten Einstieg in die Verwendung der Beispiele bietet die [Schnellstartanleitung für cloudbasierte Notebooks](quickstart-run-cloud-notebook.md). Nach Ausführung der entsprechenden Schritte verfügen Sie über einen dedizierten Notebookserver mit vorab geladenem SDK und Beispielrepository. Ganz ohne Downloads oder Installation.

## <a name="bring-your-own-jupyter-notebook-server"></a>Eigener Jupyter Notebook-Server

Falls Sie für die lokale Entwicklung einen eigenen Notebookserver verwenden möchten, gehen Sie wie folgt vor:

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Mit diesen Schritten werden die erforderlichen SDK-Basispakete für die Schnellstart- und Tutorialnotebooks installiert. Für andere Beispielnotebooks müssen ggf. zusätzliche Komponenten installiert werden. Weitere Informationen finden Sie unter [Install the Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/install) (Installieren des Azure Machine Learning SDK für Python).

## <a name="data-science-virtual-machine"></a>Virtueller Computer für Data Science

Data Science Virtual Machine (DSVM) ist ein benutzerdefiniertes VM-Image, das speziell für Data Science erstellt wurde. Wenn Sie [eine DSVM-Instanz erstellen](how-to-configure-environment.md#dsvm), werden das SDK und der Notebookserver für Sie installiert und konfiguriert. Sie müssen jedoch noch einen Arbeitsbereich erstellen und das Beispielrepository klonen.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="azure-notebooks"></a>Azure Notebooks

Bei Verwendung von [Azure Notebooks](https://notebooks.azure.com/) werden das SDK und der Notebookserver für Sie installiert und konfiguriert. Azure Notebooks bietet Ihnen eine einfache und vollständig verwaltete Notebookumgebung.

Navigieren Sie über das [Azure-Portal](https://portal.azure.com) zu Ihrem Azure Machine Learning-Arbeitsbereich, um auf das Beispielrepository für Azure Notebooks zuzugreifen. Wählen Sie im Abschnitt **Übersicht** die Option **Erste Schritte in Azure Notebooks** aus.

## <a name="next-steps"></a>Nächste Schritte

Erkunden Sie anhand der [Beispielnotebooks](https://aka.ms/aml-notebooks), welche Möglichkeiten Ihnen Azure Machine Learning Service bietet, oder probieren Sie die folgenden Tutorials aus:

- [Trainieren eines Bildklassifizierungsmodells mit dem Azure Machine Learning-Dienst](tutorial-train-models-with-aml.md)

- [Vorbereiten von Daten für die Regressionsmodellierung](tutorial-data-prep.md)
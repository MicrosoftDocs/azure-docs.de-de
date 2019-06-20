---
title: Beispiele und exemplarische Vorgehensweisen für Data Science Virtual Machines – Azure | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Beispiele und exemplarischen Vorgehensweisen, mit denen Sie allgemeine Aufgaben und Szenarios mit Data Science Virtual Machine bewältigen können.
keywords: Data Science-Tools, virtuelle Computer für Data Science, Tools für Data Science, Linux Data Science
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: gokuma
ms.openlocfilehash: e61f0f4ba30b29fea1b2fd5f2a2ab253d3a6710c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60502868"
---
# <a name="samples-on-data-science-virtual-machines"></a>Beispiele auf Data Science Virtual Machines

Azure Data Science Virtual Machines umfassen eine umfangreiche Sammlung von Beispielcode. Der Beispielcode liegt in Form von Jupyter-Notebooks und Skripts in Sprachen wie Python und R vor. 
> [!NOTE]
> Weitere Informationen zum Ausführen von Jupyter-Notebooks auf Ihren Data Science Virtual Machines finden Sie im Abschnitt [Zugriff auf Jupyter](#access-jupyter).

## <a name="quick-reference-of-samples"></a>Kurzübersicht der Beispiele
| Beispielkategorie | BESCHREIBUNG | Standorte |
| ------------- | ------------- | ------------- |
| R (Programmiersprache)  | Beispiele in R erläutern Szenarien wie das Herstellen von Verbindungen mit Azure-Clouddatenspeichern. Sie erläutern außerdem den Vergleich zwischen Open-Source-R und Microsoft R. Und sie verdeutlichen die Operationalisierung von Modellen auf Microsoft R Server oder SQL Server. <br/> [R (Programmiersprache)](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Python (Programmiersprache)  | Beispiele in Python erläutern Szenarien wie das Herstellen von Verbindungen mit Azure-Clouddatenspeichern und das Verwenden von Azure Machine Learning.  <br/> [Python (Programmiersprache)](#python-language) | <br/>`~notebooks` <br/><br/>|
| Julia (Programmiersprache)  | Beispiel in Julia, das Details zum Plotten und Deep Learning in Julia verdeutlicht. Es erläutert darüber hinaus Aufrufe an C und Python aus Julia. <br/> [Julia (Programmiersprache)](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Erstellen Sie mithilfe von Machine Learning Modelle für maschinelles Lernen und Deep Learning. Stellen Sie die Modelle an beliebigen Speicherorten bereit. Verwenden Sie automatisiertes maschinelles Lernen und intelligente Optimierung von Hyperparametern. Nutzen Sie darüber hinaus Modellverwaltung und verteiltes Training. <br/> [Machine Learning](#azureml) | <br/>`~notebooks/AzureML`<br/> <br/>|
| PyTorch-Notebooks  | Deep Learning-Beispiele, die neuronale Netze auf der Grundlage von PyTorch verwenden. Mit Notebooks von Einsteiger- bis zu fortgeschrittenen Szenarien.  <br/> [PyTorch-Notebooks](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  Verschiedene Beispiele und Techniken für neuronale Netzwerke, die mithilfe des TensorFlow-Frameworks implementiert werden. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Cognitive Toolkit <br/>   | Deep Learning-Beispiele des Cognitive Toolkit-Teams von Microsoft.  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | Deep Learning-Beispiele, die neuronale Netze auf der Grundlage von caffe2 verwenden. Verschiedene Notebooks machen Benutzer mit caffe2 und seinem effektiven Einsatz vertraut. Zu den Beispielen gehören Bildvorverarbeitung und Dataseterstellung. Regression und die Verwendung vortrainierter Modelle werden ebenfalls behandelt. <br/> [Caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | Python-basierte Beispiele, die H2O für praxisorientierte Aufgabenstellungen einsetzen. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| SparkML (Programmiersprache)  | Beispiele, die Features des Spark MLLib-Toolkits mithilfe von pySpark und MMLSpark nutzen – Microsoft Machine Learning für Apache Spark in Apache Spark 2.x.  <br/> [SparkML (Programmiersprache)](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | Machine Learning-Standardbeispiele in XGBoost für Szenarien wie Klassifizierung, Regression und dergleichen. <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Zugreifen auf Jupyter 

Wählen Sie zum Aufrufen von Jupyter das Symbol `Jupyter` auf dem Desktop oder im Anwendungsmenü aus. Der Zugriff auf Jupyter ist auch in Linux-Editionen von Data Science Virtual Machines möglich. Remotezugriff unter Ubuntu kann aus einem Webbrowser durch Aufsuchen von `https://<Full Domain Name or IP Address of the DSVM>:8000` erfolgen.

Informationen zum Hinzufügen von Ausnahmen und das Zugreifen auf Jupyter über einen Browser finden Sie im folgenden Screenshot.


![Aktivieren von Jupyter-Ausnahmen](./media/ubuntu-jupyter-exception.png)


Melden Sie sich mit dem gleichen Kennwort an, das Sie für den Zugriff auf Data Science Virtual Machines verwenden.
<br/>

**Jupyter-Startseite**
<br/>![Jupyter-Startseite](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>R (Programmiersprache) 
<br/>![Beispiele für R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Python (Programmiersprache)
<br/>![Python-Beispiele](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Julia (Programmiersprache) 
<br/>![Beispiele für Julia](./media/julia-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![Beispiele für AzureML](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![Beispiele für PyTorch](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Beispiele für TensorFlow](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![Beispiele für CNTK](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>Caffe2 
<br/>![Beispiele für caffe2](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![Beispiele für H2O](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![Beispiele für SparkML](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![Beispiele für XGBoost](./media/xgboost-samples.png)<br/>


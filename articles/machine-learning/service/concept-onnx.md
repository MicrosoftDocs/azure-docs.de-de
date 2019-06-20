---
title: Plattformübergreifender Rückschluss mit hoher Leistung dank ONNX
titleSuffix: Azure Machine Learning service
description: Erfahren Sie mehr über ONNX und die ONNX-Runtime zur Beschleunigung von Modellen
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 04/24/2019
ms.custom: seodec18
ms.openlocfilehash: f1eca5bdd81a384efe04f769ebd12be9d91fc78a
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65849723"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX und Azure Machine Learning: Erstellen und Beschleunigen von ML-Modellen

Erfahren Sie, wie der Einsatz von [Open Neural Network Exchange](https://onnx.ai) (ONNX) zur Optimierung des Rückschlusses Ihres Machine Learning-Modells beitragen kann. Rückschlüsse oder Modellbewertungen bilden die Phase, in der das bereitgestellte Modell für die Vorhersage verwendet wird, meist für Produktionsdaten. 

Die Optimierung von Machine Learning-Modellen hinsichtlich Rückschlüssen (oder Modellbewertungen) ist schwierig, da Sie das Modell und die Rückschlussbibliothek so anpassen müssen, dass sie das Beste aus den Möglichkeiten der Hardware machen. Das Problem wird extrem kompliziert, wenn Sie eine optimale Leistung auf verschiedenen Arten von Plattformen (Cloud/Edge, CPU/GPU usw.) erzielen möchten, da jede davon unterschiedliche Fähigkeiten und Eigenschaften hat. Die Komplexität steigt, wenn Sie Modelle aus einer Vielzahl von Frameworks haben, die auf einer Vielzahl von Plattformen ausgeführt werden müssen. Es ist sehr zeitaufwendig, die verschiedenen Kombinationen von Frameworks und Hardware zu optimieren. Eine Lösung, die ein einmaliges Training in Ihrem bevorzugten Framework und die Ausführung überall in der Cloud oder am Edge ermöglicht, ist erforderlich. An dieser Stelle tritt ONNX auf den Plan.

Microsoft und eine Gruppe von Partnern haben ONNX als offenen Standard für die Darstellung von Machine Learning-Modellen entwickelt. Modelle aus [vielen Frameworks](https://onnx.ai/supported-tools) wie TensorFlow, PyTorch, SciKit-Learn, Keras, Chainer, MXNet und MATLAB können exportiert oder in das ONNX-Standardformat konvertiert werden. Sobald die Modelle im ONNX-Format vorliegen, können sie auf einer Vielzahl von Plattformen und Geräten ausgeführt werden.

Die [ONNX-Runtime](https://github.com/Microsoft/onnxruntime) ist eine leistungsstarke Rückschluss-Engine für die Bereitstellung von ONNX-Modellen in der Produktionsumgebung. Sie wurde für Cloud und Edge optimiert und funktioniert unter Linux, Windows und Mac. Sie ist in C++ geschrieben, bietet aber auch C-, Python- und C#-APIs. Die ONNX-Runtime unterstützt die gesamte ONNX-ML-Spezifikation und kann auch mit Beschleunigern auf anderer Hardware wie TensorRT auf NVidia-GPUs integriert werden.

Die ONNX-Runtime kommt in weit verbreiteten Microsoft-Diensten wie Bing, Office und Cognitive Services zum Einsatz. Leistungssteigerungen hängen von einer Reihe von Faktoren ab, aber diese Microsoft-Dienste konnten bei der __CPU einen Leistungszuwachs um durchschnittlich das Doppelte__ verzeichnen. Die ONNX-Runtime wird auch als Teil von Windows ML auf Hunderten von Millionen von Geräten genutzt. Sie können die Runtime mit Azure Machine Learning Services verwenden. Durch den Einsatz der ONNX-Runtime können Sie von den umfangreichen Optimierungen, Tests und laufenden Verbesserungen für Produktionsumgebungen profitieren.

[![ONNX-Flussdiagramm mit Training, Konvertern und Bereitstellung](media/concept-onnx/onnx.png)](./media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Abrufen von ONNX-Modellen

Es gibt verschiedene Möglichkeiten, an ONNX-Modelle zu gelangen:
+ Trainieren eines neuen ONNX-Modells in Azure Machine Learning Service (siehe dazu das Beispiel am Ende dieses Artikels)
+ Konvertieren eines vorhandenen Modells aus einem anderen Format in ONNX (siehe die [Tutorials](https://github.com/onnx/tutorials)) 
+ Abrufen eines vortrainierten ONNX-Modells aus dem [ONNX-Modellzoo](https://github.com/onnx/models) (siehe die Beispiele unten in diesem Artikel)
+ Generieren eines benutzerdefinierten ONNX-Modells über [Azure Custom Vision Service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 

Viele Modelle, z.B. zur Bildklassifizierung, Objekterkennung und Textverarbeitung, können als ONNX-Modelle abgebildet werden. Einige Modelle können jedoch möglicherweise nicht erfolgreich konvertiert werden. Wenn Sie in diese Situation geraten, melden Sie Ihr Problem im GitHub-Repository des jeweiligen von Ihnen verwendeten Konverters. Sie können Ihr bestehendes Formatmodell weiter verwenden, bis das Problem behoben ist.

## <a name="deploy-onnx-models-in-azure"></a>Bereitstellen von ONNX-Modellen in Azure

Mit dem Azure Machine Learning-Dienst können Sie Ihre ONNX-Modelle bereitstellen, verwalten und überwachen. Wenn Sie den [Standardbereitstellungsworkflow](concept-model-management-and-deployment.md) und ONNX Runtime verwenden, können Sie einen REST-Endpunkt erstellen, der in der Cloud gehostet wird. Sehen Sie sich am Ende dieses Artikels ein Beispiel für ein Jupyter Notebook an, um es selbst auszuprobieren. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Installieren und Verwenden der ONNX-Runtime mit Python

Python-Pakete für die ONNX-Runtime stehen auf [PyPi.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)) zur Verfügung. Lesen Sie vor der Installation die [Systemanforderungen](https://github.com/Microsoft/onnxruntime#system-requirements). 

 Führen Sie zum Installieren der ONNX-Runtime für Python einen der folgenden Befehle aus: 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
``` 

Um ONNX Runtime in Ihrem Python-Skript aufzurufen, verwenden Sie den folgenden Code:    
```python   
import onnxruntime  
session = onnxruntime.InferenceSession("path to model") 
``` 

In der dem Modell beiliegenden Dokumentation finden Sie in der Regel die Ein- und Ausgaben für die Verwendung des Modells. Sie können auch ein Visualisierungstool wie [Netron](https://github.com/lutzroeder/Netron) einsetzen, um das Modell anzuzeigen. Die ONNX-Runtime kann zudem verwendet werden, um Metadaten, Eingaben und Ausgaben des Modells abzufragen:    
```python   
session.get_modelmeta() 
first_input_name = session.get_inputs()[0].name 
first_output_name = session.get_outputs()[0].name   
``` 

Um Rückschlüsse zu Ihrem Modell zu erhalten, verwenden Sie `run` und übergeben die Liste der Ausgaben, die zurückgegeben werden sollen (lassen Sie sie leer, falls alle gewünscht sind), und eine Zuordnung der Eingabewerte. Als Ergebnis erhalten Sie eine Liste der Ausgaben.  
```python   
results = session.run(["output1", "output2"], {"input1": indata1, "input2": indata2})   
results = session.run([], {"input1": indata1, "input2": indata2})   
``` 

Die vollständige API-Referenz zu Python finden Sie im Dokument zur [Referenzdokumentation zu ONNX Runtime](https://aka.ms/onnxruntime-python).    

## <a name="examples"></a>Beispiele

Beispiele für Notebooks, die ONNX-Modelle erstellen und bereitstellen, finden Sie unter [how-to-use azureml/deployment/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx).

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Weitere Informationen

Erfahren Sie mehr über ONNX, oder leisten Sie einen Beitrag zum Projekt:
+ [ONNX-Projektwebsite](https://onnx.ai)
+ [ONNX-Code auf GitHub](https://github.com/onnx/onnx)

Erfahren Sie mehr über ONNX Runtime, oder leisten Sie einen Beitrag zum Projekt:
+ [ONNX Runtime-GitHub-Repository](https://github.com/Microsoft/onnxruntime)



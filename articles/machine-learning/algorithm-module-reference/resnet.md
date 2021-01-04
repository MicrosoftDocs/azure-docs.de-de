---
title: ResNet
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mit dem ResNet-Algorithmus ein Bildklassifizierungsmodell im Azure Machine Learning-Designer erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 88a820d0f1fa9515b4f2992a8305a2d1065e0987
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421208"
---
# <a name="resnet"></a>ResNet

In diesem Artikel wird beschrieben, wie Sie mit dem **ResNet**-Modul in Azure Machine Learning-Designer ein Bildklassifizierungsmodell mithilfe des ResNet-Algorithmus erstellen.  

Dieser Klassifizierungsalgorithmus ist eine überwachte Lernmethode und erfordert ein Dataset mit Bezeichnungen. 
> [!NOTE]
> Dieses Modul unterstützt nicht das Dataset mit Bezeichnungen, das aus *Datenbeschriftung* in Studio generiert wurde, sondern nur das Bildverzeichnis mit Bezeichnungen, das aus dem Modul [In Bildverzeichnis konvertieren](convert-to-image-directory.md) generiert wurde. 

Sie können das Modell trainieren, indem Sie ein Modell und ein Bildverzeichnis mit Bezeichnungen als Eingaben für [Trainieren eines PyTorch-Modells](train-pytorch-model.md) angeben. Mit dem trainierten Modell können Sie anschließend Werte für neue Eingabebeispiele mithilfe von [Bewerten eines Bildmodells](score-image-model.md) vorhersagen.

### <a name="more-about-resnet"></a>Weitere Informationen zu ResNet

Weitere Einzelheiten zu ResNet finden Sie in [diesem Artikel](https://pytorch.org/docs/stable/torchvision/models.html?highlight=resnext101_32x8d#torchvision.models.resnext101_32x8d).

## <a name="how-to-configure-resnet"></a>Konfigurieren von ResNet

1.  Fügen Sie das **ResNet**-Modul Ihrer Pipeline im Designer hinzu.  

2.  Geben Sie für **Modellname** den Namen einer bestimmten ResNet-Struktur an, und Sie können unter den unterstützten ResNet-Optionen auswählen: 'resnet18', 'resnet34', 'resnet50', 'resnet101', 'resnet152', 'resnet152', 'resnext50\_32x4d', 'resnext101\_32x8d', 'wide_resnet50\_2', 'wide_resnet101\_2'.

3.  Geben Sie für **Vortrainiert** an, ob ein in ImageNet vortrainiertes Modell verwendet werden soll. Bei Auswahl dieser Option können Sie das Modell basierend auf dem ausgewählten vortrainierten Modell optimieren. Wird die Auswahl aufgehoben, können Sie von Grund auf neu trainieren.

4.  Verbinden Sie die Ausgabe des **DenseNet**-Moduls, des Trainings- und Überprüfungsmoduls für das Bilddataset mit [Trainieren eines Pytorch-Modells](train-pytorch-model.md). 

5. Übermitteln Sie die Pipeline.

## <a name="results"></a>Ergebnisse

Nach Abschluss der Pipelineausführung verbinden Sie zum Bewerten mithilfe des Modells [Pytorch-Modell trainieren](train-pytorch-model.md) mit [Bewerten eines Bildmodells](score-image-model.md), um Werte für neue Eingabebeispiele vorherzusagen.

## <a name="technical-notes"></a>Technische Hinweise  

###  <a name="module-parameters"></a>Modulparameter  

| Name       | Range | type    | Standard           | BESCHREIBUNG                              |
| ---------- | ----- | ------- | ----------------- | ---------------------------------------- |
| Modellname | Any   | Mode    | resnext101\_32x8d | Name einer bestimmten ResNet-Struktur       |
| Vortrainiert | Any   | Boolean | True              | Gibt an, ob ein in ImageNet vortrainiertes Modell verwendet werden soll |
|            |       |         |                   |                                          |

###  <a name="output"></a>Output  

| Name            | type                    | BESCHREIBUNG                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Untrainiertes Modell | UntrainedModelDirectory | Ein untrainiertes ResNet-Modell, das mit „Trainieren eines Pytorch-Modells“ verbunden werden kann. |

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an. 
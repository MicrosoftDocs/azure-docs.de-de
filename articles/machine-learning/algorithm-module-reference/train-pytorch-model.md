---
title: Trainieren eines PyTorch-Modells
titleSuffix: Azure Machine Learning
description: Verwenden Sie das Train Pytorch Models-Modul im Azure Machine Learning-Designer, um Modelle von Grund auf neu zu trainieren oder vorhandene Modelle zu optimieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 2d88069f33995bdbe9dd479afe9a4e72ab9939b6
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420664"
---
# <a name="train-pytorch-model"></a>Trainieren eines PyTorch-Modells

In diesem Artikel wird beschrieben, wie Sie mit dem Modul **Train Pytorch Model** (Trainieren eines PyTorch-Modells) im Azure Machine Learning-Designer PyTorch-Modelle wie DenseNet trainieren. Das Training findet statt, nachdem Sie ein Modell definiert und die zugehörigen Parameter festgelegt haben. Für das Training werden markierte Daten benötigt. 

## <a name="how-to-use-train-pytorch-model"></a>Verwenden von „Train Pytorch Model“ 

1. Fügen Sie das [DenseNet](densenet.md)-Modul oder [ResNet](resnet.md) Ihrem Pipelineentwurf im Designer hinzu.

2. Fügen Sie der Pipeline das Modul **Train Pytorch Model** hinzu. Sie finden dieses Modul unter der Kategorie **Modelltraining**. Erweitern Sie **Trainieren**, und ziehen Sie dann das Modul **Train Pytorch Model** in Ihre Pipeline.

   > [!NOTE]
   > Das Modul **Train Pytorch Model** sollte besser unter **GPU**-Compute für große Datasets ausgeführt werden, andernfalls tritt in der Pipeline ein Fehler auf. Sie können im rechten Bereich des Moduls Compute für ein bestimmtes Modul auswählen, indem Sie **Anderes Computeziel verwenden** festlegen.

3.  Fügen Sie in der linken Eingabe ein untrainiertes Modell an. Fügen Sie das Trainingsdataset und das Validierungsdataset in der mittleren und der rechten Eingabe von **Train Pytorch Model** an.

    Bei einem untrainierten Modell muss es sich um ein PyTorch-Modell wie DenseNet handeln; andernfalls wird der Fehler „InvalidModelDirectoryError“ ausgelöst.

    Für das Dataset muss das Trainingsdataset ein Bildverzeichnis mit Bezeichnungen sein. Weitere Informationen zum Abrufen eines Bildverzeichnisses mit Bezeichnungen finden Sie unter **Konvertieren in ein Bildverzeichnis**. Liegen keine Bezeichnungen vor, wird der Fehler „NotLabeledDatasetError“ ausgelöst.

    Das Trainingsdataset und das Validierungsdataset weisen dieselben Bezeichnungskategorien auf; andernfalls wird der Fehler „InvalidDatasetError“ ausgelöst.

4.  Geben Sie für **Epochs** (Epochen) an, wie viele Epochen trainiert werden sollen. Das gesamte Dataset wird in jeder Epoche (standardmäßig 5) durchlaufen.

5.  Geben Sie für **Batchgröße** an, wie viele Instanzen in einem Batch trainiert werden sollen (standardmäßig 16).

6.  Geben Sie für **Learning rate** (Lernrate) einen Wert für die *Lernrate* an. Die Lernratenwerte steuern die Größe des Schritts, der jedes Mal für den Optimierer (wie SGD) verwendet wird, wenn das Modell getestet und korrigiert wird.

    Indem Sie die Rate verkleinern, testen Sie das Modell häufiger, mit dem Risiko, dass Sie auf einem lokalen Plateau hängen bleiben. Mit größeren Schritten können Sie schneller konvergieren, mit dem Risiko, den echten Mindestwert zu unterschreiten. Standardmäßig ist dies 0,001.

7.  Geben Sie für **Random seed** (Zufällig gewählter Startwert) optional einen ganzzahligen Wert ein, der als Startwert verwendet wird. Sie sollten einen Startwert verwenden, wenn Sie die Ausführungen übergreifende Reproduzierbarkeit des Experiments sicherstellen möchten.

8.  Geben Sie für **Patience** (Geduld) an, nach wie vielen Epochen das Training vorzeitig abgebrochen werden soll, wenn der Validierungsverlust nicht konsekutiv abnimmt. Die Standardeinstellung ist 3.

9.  Übermitteln Sie die Pipeline. Wenn es sich um ein größeres Dataset handelt, kann dies eine Weile dauern, und es wird eine GPU-Computeressource empfohlen.

## <a name="results"></a>Ergebnisse

Wenn Sie das Modell zur Bewertung verwenden möchten, verbinden Sie nach Abschluss der Pipelineausführung [Train Pytorch Model](train-pytorch-model.md) (Trainieren eines Pytorch-Modells) mit [Score Image Model](score-image-model.md) (Bewerten eines Bildmodells), um Werte für neue Eingabebeispiele vorherzusagen.

## <a name="technical-notes"></a>Technische Hinweise
###  <a name="expected-inputs"></a>Erwartete Eingaben  

| Name               | type                    | BESCHREIBUNG                              |
| ------------------ | ----------------------- | ---------------------------------------- |
| Untrainiertes Modell    | UntrainedModelDirectory | Untrainiertes Modell, PyTorch erforderlich         |
| Trainingsdataset   | ImageDirectory          | Trainingsdataset                         |
| Validierungsdataset | ImageDirectory          | Validierungsdataset für die Bewertung jeder Epoche |

###  <a name="module-parameters"></a>Modulparameter  

| Name          | Range            | type    | Standard | BESCHREIBUNG                              |
| ------------- | ---------------- | ------- | ------- | ---------------------------------------- |
| Epochs (Epochen)        | >0               | Integer | 5       | Auswählen der Spalte, die die Bezeichnung enthält, oder der Ergebnisspalte |
| Batchgröße    | >0               | Integer | 16      | Anzahl der Instanzen, die in einem Batch trainiert werden sollen   |
| Learning rate (Lernrate) | >=double.Epsilon | Float   | 0,001   | Die anfängliche Lernrate für den Stochastic Gradient Descent-Optimierer (stochastischer Gradientenabfall) |
| Random seed (Zufälliger Ausgangswert)   | Any              | Integer | 1       | Der Ausgangswert für den Zufallszahlen-Generator, der vom Modell verwendet wird |
| Patience (Geduld)      | >0               | Integer | 3       | Anzahl der Epochen bis zum vorzeitigen Abbruch des Trainings   |

###  <a name="outputs"></a>Ausgaben  

| Name          | type           | BESCHREIBUNG   |
| ------------- | -------------- | ------------- |
| Trainiertes Modell | ModelDirectory | Trainiertes Modell |

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an. 




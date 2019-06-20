---
title: 'Train Clustering Model: Modulreferenz'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie das Modul „Train Clustering Model“ (Trainieren von Clusteringmodellen) in Azure Machine Learning Service verwenden, um Clusteringmodelle zu trainieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 41cdec1d7f1c3932b17da6f9b1de518071f3f542
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027640"
---
# <a name="train-clustering-model"></a>Trainieren eines Clusteringmodells

In diesem Artikel wird ein Modul der grafischen Benutzeroberfläche (Vorschau) für Azure Machine Learning Service beschrieben.

Verwenden Sie dieses Modul, um ein Clusteringmodell zu trainieren.

In diesem Modul wird ein untrainiertes Clusteringmodell verwendet, das Sie bereits mithilfe des Moduls [K-Means Clustering](k-means-clustering.md) konfiguriert haben. Das Modul trainiert das Modell mit einem bezeichneten oder unbezeichneten Datensatz. Das Modul erstellt sowohl ein trainiertes Modell, das Sie für Vorhersagen verwenden können, als auch eine Reihe von Clusterzuweisungen für jeden Fall in den Trainingsdaten.

> [!NOTE]
> Ein Clusteringmodell kann nicht mit dem Modul [Train Model](train-model.md) (Trainieren des Modells) trainiert werden, bei dem es sich um das generische Modul zum Training von Machine Learning-Modellen handelt. Das liegt daran, dass [Train Model](train-model.md) nur mit überwachten Lernalgorithmen funktioniert. k-Means- und andere Clusteringalgorithmen ermöglichen unüberwachtes Lernen, was bedeutet, dass der Algorithmus aus unbezeichneten Daten lernen kann.  
  
## <a name="how-to-use-train-clustering-model"></a>Verwenden des Moduls „Train Clustering Model“  
  
1.  Fügen Sie das Modul **Train Clustering Model** Ihrem Experiment in Studio hinzu. Sie finden das Modul unter **Machine Learning Modules**in der Kategorie **Train**.  
  
2. Fügen Sie das Modul [K-Means Clustering](k-means-clustering.md) oder ein anderes benutzerdefiniertes Modul hinzu, mit dem ein kompatibles Clusteringmodell erstellt wird, und legen Sie dessen Parameter fest.  
    
3.  Fügen Sie das Trainingsdataset an die rechte Eingabe von **Train Clustering Model** an.
  
5.  Wählen Sie unter **Column Set** (Spaltengruppe) die Spalten aus dem Dataset aus, die zum Erstellen von Clustern verwendet werden sollen. Achten Sie darauf, Spalten auszuwählen, die gute Features bieten. Vermeiden Sie z.B. die Verwendung von IDs oder anderen Spalten mit eindeutigen oder identischen Werten.

    Wenn eine Bezeichnung verfügbar ist, können Sie sie entweder als Feature verwenden oder weglassen.  
  
6. Aktivieren Sie die Option **Check for Append or Uncheck for Result Only** (Für Anfügen aktivieren oder für reine Ergebnisse deaktivieren), wenn Sie die Trainingsdaten zusammen mit der neuen Clusterbezeichnung ausgeben möchten.

    Wenn Sie diese Option deaktivieren, werden nur die Clusterzuweisungen ausgegeben. 

7. Führen Sie das Experiment aus, oder klicken Sie auf das Modul **Train Clustering Model** und dann auf **Run Selected** (Ausgewählte ausführen).  
  
### <a name="results"></a>Ergebnisse

Nach Abschluss des Trainings:


+  Um die Werte im Dataset anzuzeigen, klicken Sie mit der rechten Maustaste auf das Modul. Wählen Sie **Result datasets** (Ergebnisdatasets) aus, und klicken Sie auf **Visualize** (Visualisieren).

+ Um das trainierte Modell für die spätere Wiederverwendung zu speichern, klicken Sie mit der rechten Maustaste auf das Modul. Wählen Sie **Trained model** (Trainiertes Modell) aus, und klicken Sie auf **Save As Trained Model** (Als trainiertes Modell speichern).

+ Verwenden Sie zum Generieren von Ergebnissen aus dem Modell [Assign Data to Clusters](assign-data-to-clusters.md) (Zuweisen von Daten zu Clustern).



## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Gruppe der verfügbaren Module](module-reference.md) für Azure Machine Learning Service an. 
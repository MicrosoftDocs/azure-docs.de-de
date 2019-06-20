---
title: 'Add Rows: Modulreferenz'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie das Modul „Add Rows“ (Hinzufügen von Zeilen) in Azure Machine Learning Service verwenden können, um zwei Datensätze zu verketten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ffd693ea3452ef48dc3e05e7bc4a6d3988a487b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027718"
---
# <a name="add-rows-module"></a>Modul „Add Rows“ (Hinzufügen von Zeilen)

In diesem Artikel wird ein Modul der grafischen Benutzeroberfläche (Vorschau) für Azure Machine Learning Service beschrieben.

Verwenden Sie dieses Modul zum Verketten zweier Datasets. Bei der Verkettung werden die Zeilen des zweiten Datasets am Ende des ersten Datasets hinzugefügt.  
  
Die Verkettung von Zeilen ist in folgenden Szenarien nützlich:  
  
+ Sie haben eine Reihe von Auswertungsstatistiken erstellt und möchten diese zur besseren Berichterstellung in einer Tabelle zusammenfassen.  
  
+ Sie haben mit verschiedenen Datasets gearbeitet und möchten die Datasets zu einem endgültigen Dataset zusammenfassen.  

## <a name="how-to-use-add-rows"></a>Verwenden von „Add Rows“  

Um Zeilen aus zwei Datasets zu verketten, müssen die Zeilen exakt das gleiche Schema haben. Das heißt, dass die Anzahl der Spalten und der Datentyp in den Spalten identisch sein müssen.

1.  Ziehen Sie das Modul **Add Rows** in Ihr Experiment. Sie finden es unter **Data Transformation** in der Kategorie **Manipulate**.

2. Verbinden Sie die Datasets mit den beiden Eingabeports. Das Dataset, das Sie anfügen möchten, muss mit dem zweiten (rechten) Port verbunden werden. 
  
3.  Führen Sie das Experiment aus. Die Anzahl der Zeilen im Ausgabedataset muss der Summe der Zeilen beider Eingabedatasets entsprechen.

    Wenn Sie das gleiche Dataset beiden Eingaben des Moduls **Add Rows** hinzufügen, wird das Dataset dupliziert. 

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Gruppe der verfügbaren Module](module-reference.md) für Azure Machine Learning Service an. 
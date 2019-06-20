---
title: 'Add Columns: Modulreferenz'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie das Modul „Add Columns“ (Hinzufügen von Spalten) in Azure Machine Learning Service verwenden können, um zwei Datensätze zu verketten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f1e087e97007c6ba271651a9791c7c3b38a9b9b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027786"
---
# <a name="add-columns-module"></a>Modul „Add Columns“ (Hinzufügen von Spalten)

In diesem Artikel wird ein Modul der grafischen Benutzeroberfläche (Vorschau) für Azure Machine Learning Service beschrieben.

Verwenden Sie dieses Modul zum Verketten zweier Datasets. Sie kombinieren alle Spalten aus den beiden Datasets, die Sie als Eingaben angeben, zu einem einzigen Dataset. Wenn Sie mehr als zwei Datasets verketten müssen, verwenden Sie mehrere Instanzen von **Add Columns**.



## <a name="how-to-configure-add-columns"></a>Konfigurieren von „Add Columns“
1. Fügen Sie das Modul **Add Columns** Ihrem Experiment hinzu.

2. Verbinden Sie die beiden Datasets, die Sie verketten möchten. Wenn Sie mehr als zwei Datasets kombinieren möchten, können Sie mehrere Kombinationen von **Add Columns** miteinander verketten.

    - Es ist möglich, zwei Spalten mit einer unterschiedlichen Anzahl von Zeilen zu kombinieren. Der Ausgabedataset wird mit fehlenden Werten für jede Zeile in der kleineren Quellspalte aufgefüllt.

    - Sie können keine einzelnen hinzuzufügenden Spalten auswählen. Alle Spalten aus jedem Dataset werden verkettet, wenn Sie **Add Columns** verwenden. Wenn Sie daher nur eine Teilmenge der Spalten hinzufügen möchten, verwenden Sie „Select Columns in Dataset“ (Spalten im Dataset auswählen), um ein Dataset mit den gewünschten Spalten zu erstellen.

3. Führen Sie das Experiment aus.

### <a name="results"></a>Ergebnisse
Nach Ausführung des Experiments:

- Um die ersten Zeilen des neuen Datasets anzuzeigen, klicken Sie mit der rechten Maustaste auf die Ausgabe von **Add Columns**, und wählen Sie „Visualize“ (Visualisieren) aus.

Die Anzahl der Spalten im neuen Dataset entspricht der Summe der Spalten beider Eingabedatasets.

Wenn es in den Eingabedatasets zwei Spalten mit dem gleichen Namen gibt, wird dem Namen der Spalte ein numerisches Suffix hinzugefügt. Wenn es beispielsweise zwei Instanzen einer Spalte namens „TargetOutcome“ gibt, wird die linke Spalte in „TargetOutcome_1“ und die rechte Spalte in „TargetOutcome_2“ umbenannt.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Gruppe der verfügbaren Module](module-reference.md) für Azure Machine Learning Service an. 
---
title: 'Add Columns: Modulreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie das Add Columns-Modul im Azure Machine Learning-Designer mit Drag & Drop-Funktion verwenden, um zwei Datensätze zu verketten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 36de827dff239dbeebc66e330a76b7a65fefb909
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421956"
---
# <a name="add-columns-module"></a>Modul „Add Columns“ (Hinzufügen von Spalten)

In diesem Artikel wird ein Modul im Azure Machine Learning-Designer beschrieben.

Verwenden Sie dieses Modul zum Verketten zweier Datasets. Sie kombinieren alle Spalten aus den beiden Datasets, die Sie als Eingaben angeben, zu einem einzigen Dataset. Wenn Sie mehr als zwei Datasets verketten müssen, verwenden Sie mehrere Instanzen von **Add Columns**.



## <a name="how-to-configure-add-columns"></a>Konfigurieren von „Add Columns“
1. Fügen Sie Ihrer Pipeline das Modul **Add Columns** (Spalten hinzufügen) hinzu.

2. Verbinden Sie die beiden Datasets, die Sie verketten möchten. Wenn Sie mehr als zwei Datasets kombinieren möchten, können Sie mehrere Kombinationen von **Add Columns** miteinander verketten.

    - Es ist möglich, zwei Spalten mit einer unterschiedlichen Anzahl von Zeilen zu kombinieren. Der Ausgabedataset wird mit fehlenden Werten für jede Zeile in der kleineren Quellspalte aufgefüllt.

    - Sie können keine einzelnen hinzuzufügenden Spalten auswählen. Alle Spalten aus jedem Dataset werden verkettet, wenn Sie **Add Columns** verwenden. Wenn Sie daher nur eine Teilmenge der Spalten hinzufügen möchten, verwenden Sie „Select Columns in Dataset“ (Spalten im Dataset auswählen), um ein Dataset mit den gewünschten Spalten zu erstellen.

3. Übermitteln Sie die Pipeline.

### <a name="results"></a>Ergebnisse
Gehen Sie nach der Pipelineausführung wie folgt vor:

- Um die ersten Zeilen des neuen Datasets anzuzeigen, klicken Sie mit der rechten Maustaste auf das Modul **Add Columns** (Spalten hinzufügen), und wählen Sie „Visualisieren“ aus. Oder wählen Sie das Modul aus, wechseln Sie zur Registerkarte **Ausgaben** im rechten Bereich. Klicken Sie dann auf das Histogrammsymbol in **Portausgaben**, um das Ergebnis zu visualisieren.

Die Anzahl der Spalten im neuen Dataset entspricht der Summe der Spalten beider Eingabedatasets.

Wenn es in den Eingabedatasets zwei Spalten mit dem gleichen Namen gibt, wird dem Namen der Spalte ein numerisches Suffix hinzugefügt. Wenn es beispielsweise zwei Instanzen einer Spalte namens „TargetOutcome“ gibt, wird die linke Spalte in „TargetOutcome_1“ und die rechte Spalte in „TargetOutcome_2“ umbenannt.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an. 
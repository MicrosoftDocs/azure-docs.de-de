---
title: 'Azure Data Factory Mapping Data Flow: Fenstertransformation'
description: 'Azure Data Factory Mapping Data Flow: Fenstertransformation'
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 6f3f06ff54fc76416ba63f4f09835897d546f8dc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61349741"
---
# <a name="azure-data-factory-window-transformation"></a>Azure Data Factory: Fenstertransformation

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

In der Fenstertransformation definieren Sie fensterbasierte Aggregationen von Spalten in Ihren Datenströmen. Im Ausdrucks-Generator können Sie verschiedene Typen von Aggregationen definieren, die auf Daten- oder Zeitfenstern (SQL OVER-Klausel) basieren, wie z.B. LEAD, LAG, NTILE, CUMEDIST, RANK usw.). Ein neues Feld wird in Ihrer Ausgabe generiert, das diese Aggregationen enthält. Sie können auch optionale „Group by“-Felder einschließen.

![Fensteroptionen](media/data-flow/windows1.png "Fenster 1")

## <a name="over"></a>Over
Legen Sie die Partitionierung von Spaltendaten für Ihre Fenstertransformation fest. Die SQL-Entsprechung ist ```Partition By``` in der Over-Klausel in SQL. Wenn Sie eine Berechnung erstellen möchten, oder einen für die Partitionierung zu verwendenden Ausdruck, können Sie hierzu den Mauszeiger auf den Namen der Spalte setzen und „berechnete Spalte“ auswählen.

![Fensteroptionen](media/data-flow/windows4.png "Fenster 4")

## <a name="sort"></a>Sortieren
Ein anderer Teil der Over-Klausel ist das Festlegen von ```Order By```. Hiermit wird die Reihenfolge der Datensortierung festgelegt. Sie können auch einen Ausdruck für einen berechneten Wert in diesem Spaltenfeld für die Sortierung erstellen.

![Fensteroptionen](media/data-flow/windows5.png "Fenster 5")

## <a name="range-by"></a>Range By
Legen Sie als Nächstes den Fensterrahmen als „Unbounded“ oder „Bounded“ fest. Um einen unbegrenzten Fensterrahmen festzulegen, positionieren Sie den Schieberegler an beiden Enden auf „Unbounded“. Wenn Sie eine Einstellung zwischen „Unbounded“ und „Current Row“ auswählen, müssen Sie den Offset-Start- und Endwert festlegen. Beide Werte sind positive ganze Zahlen. Sie können entweder relativen Zahlen oder Werte aus Ihren Daten verwenden.

Der Fensterschieberegler muss zwei Werte festlegen: die Werte vor der aktuellen Zeile und die Werte nach der aktuellen Zeile. Offset-Start- und Endwert entsprechen den beiden Selektoren auf dem Schieberegler.

![Fensteroptionen](media/data-flow/windows6.png "Fenster 6")

## <a name="window-columns"></a>Fensterspalten
Verwenden Sie den Ausdrucks-Generator schließlich, um die Aggregationen zu definieren, die Sie mit Datenfenstern wie RANK, COUNT, MIN, MAX, DENSE RANK, LEAD, LAG usw. verwenden möchten.

![Fensteroptionen](media/data-flow/windows7.png "Fenster 7")

Die vollständige Liste der Aggregations- und Analysefunktionen, die Ihnen in der ADF Data Flow Expression Language über den Ausdrucks-Generator zur Verfügung stehen, ist hier aufgeführt: https://aka.ms/dataflowexpressions.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie eine einfache Group by-Aggregation suchen, verwenden Sie die [Transformation für das Aggregieren](data-flow-aggregate.md).

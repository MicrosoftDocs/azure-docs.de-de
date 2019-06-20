---
title: 'Azure Data Factory Mapping Data Flow: Filtertransformation'
description: 'Azure Data Factory Mapping Data Flow: Filtertransformation'
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: e0b41850c149ff7095333cf77b780dec1f03b882
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234410"
---
# <a name="azure-data-factory-filter-transformation"></a>Azure Data Factory: Filtertransformation

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Die Filtertransformation ermöglicht die Zeilenfilterung. Erstellen Sie einen Ausdruck, der die Filterbedingung definiert. Klicken Sie in das Textfeld, um den Ausdrucks-Generator zu starten. Erstellen Sie im Ausdrucks-Generator einen Filterausdruck, um zu steuern, welche Zeilen aus dem aktuellen Datenstrom zur nächsten Transformation durchlaufen (filtern) dürfen. Stellen Sie sich die Filtertransformation als die WHERE-Klausel einer SQL-Anweisung vor.

## <a name="filter-on-loanstatus-column"></a>Filtern nach der Spalte „loan_status“:

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Filtern Sie in der Filmedemo nach der Spalte „year“:

```
year > 1980
```

## <a name="next-steps"></a>Nächste Schritte

Probieren Sie eine Spaltenfiltertransformation aus, die [Auswahltransformation](data-flow-select.md)

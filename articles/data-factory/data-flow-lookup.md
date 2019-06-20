---
title: 'Azure Data Factory Mapping Data Flow: Suchtransformation'
description: 'Azure Data Factory Mapping Data Flow: Suchtransformation'
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 197f5ba9d6921f4a9921b7074b9e05162d3e37b8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64868122"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure Data Factory Mapping Data Flow: Suchtransformation

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Verwenden Sie die Transformation für die Suche, um Ihrem Datenfluss Verweisdaten aus einer anderen Quelle hinzuzufügen. Für die Suchtransformation ist eine definierte Quelle erforderlich, die auf Ihre Verweistabelle verweist und mit Schlüsselfeldern übereinstimmt.

![Suchtransformation](media/data-flow/lookup1.png "Suche")

Wählen Sie die Schlüsselfelder aus, die zwischen den Feldern für den eingehenden Datenstrom und den Feldern der Referenzquelle abgeglichen werden sollen. Zuerst muss auf dem Datenfluss-Entwurfscanvas eine neue Quelle erstellt worden sein, die als rechte Seite für die Suche verwendet wird.

Werden Übereinstimmungen gefunden, werden Ihrem Datenfluss die resultierenden Zeilen und Spalten der Referenzquelle hinzugefügt. Sie können die relevanten Felder auswählen, die Sie in Ihre Senke am Ende des Datenflusses einschließen möchten.

## <a name="match--no-match"></a>Übereinstimmung/Keine Übereinstimmung

Nach Ihrer Lookup-Transformation können Sie nachfolgende Transformationen verwenden, um die Ergebnisse der einzelnen übereinstimmenden Zeilen zu überprüfen, indem Sie die Ausdrucksfunktion `isMatch()` verwenden, um weitere Entscheidungen in Ihrer Logik zu treffen, je nachdem, ob der Lookup-Vorgang zu einer Übereinstimmung in einer Zeile führte.

## <a name="optimizations"></a>Optimierungen

In Data Factory werden Datenflüsse in horizontal skalierten Spark-Umgebungen ausgeführt. Wenn Ihr Dataset in den Workerknotenspeicher eingepasst werden kann, können wir Ihre Lookup-Leistung optimieren.

![Broadcastjoin](media/data-flow/broadcast.png "Broadcastjoin")

### <a name="broadcast-join"></a>Broadcastjoin

Wählen Sie für den Broadcastjoin „Links“ und/oder „Rechts“ aus, um anzufordern, dass die Anwendungsdefinitionsdatei (Application Definition File, ADF) das gesamte Dataset mithilfe von Push von jeder der beiden Seiten der Lookup-Beziehung in den Speicher überträgt.

### <a name="data-partitioning"></a>Datenpartitionierung

Sie können auch die Partitionierung Ihrer Daten angeben, indem Sie auf der Registerkarte „Optimize“ (Optimieren) der Lookup-Transformation „Set Partitioning“ (Partitionierung festlegen) auswählen, um Datasets zu erstellen, die besser in den Speicher pro Worker passen.

## <a name="next-steps"></a>Nächste Schritte

[Join](data-flow-join.md)- und [Exists](data-flow-exists.md)-Transformationen führen in ADF-Mappingdatenflüssen ähnliche Aufgaben aus. Sehen Sie sich als Nächstes diese Transformationen an.

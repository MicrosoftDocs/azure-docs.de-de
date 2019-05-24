---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 065cb4daa9501ee658d364dad43b9e03798e4083
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66160952"
---
Mit dem Auftrag wird eine JSON-Ausgabedatei erzeugt, die Metadaten zu erkannten und nachverfolgten Gesichtern enthält. Die Metadaten enthalten Koordinaten, die die Position der Gesichter sowie eine Gesichts-ID angeben, um die Nachverfolgung einer einzelnen Person kenntlich zu machen. Gesichts-IDs können zurückgesetzt werden, wenn die Frontalansicht des Gesichts verloren geht oder im Frame überlappt wird, sodass einigen Personen möglicherweise mehrere IDs zugewiesen werden.

Die JSON-Ausgabe enthält die folgenden Elemente:

### <a name="root-json-elements"></a>JSON-Stammelemente

| Element | BESCHREIBUNG |
| --- | --- |
| version |Dies bezieht sich auf die Version der Video-API. |
| timescale |„Ticks“ pro Sekunde des Videos. |
| offset |Dies ist der Zeitoffset für Zeitstempel. In Version 1.0 von Video-APIs wird dies immer 0 sein. In zukünftigen Szenarien, die wir unterstützen, kann sich dieser Wert ändern. |
| width, height |Breite und Höhe des Ausgabevideobilds in Pixel.|
| framerate |Frames des Videos pro Sekunde. |
| [fragments](#fragments-json-elements) |Die Metadaten werden in verschiedene, als Fragmente bezeichnete Segmente aufgeteilt. Jedes Fragment enthält Startzeitpunkt, Dauer, Intervallnummer und Ereignis(se). |

### <a name="fragments-json-elements"></a>JSON-Elemente für Fragmente

|Element|BESCHREIBUNG|
|---|---|
| Start |Die Startzeit des ersten Ereignisses in „Ticks“. |
| duration |Die Länge des Fragments in „Ticks“. |
| index | Definiert den Frameindex des aktuellen Ereignisses (gilt nur für Azure Media Redactor). |
| interval |Das Intervall jedes Ereigniseintrags innerhalb des Fragments in „Ticks“. |
| events |Jedes Ereignis enthält die innerhalb dieser Zeitspanne erkannten und nachverfolgten Gesichter. Es ist ein Array von Ereignissen. Das äußere Array stellt ein Zeitintervall dar. Das innere Array besteht aus 0 oder mehr Ereignissen, die zu diesem Zeitpunkt aufgetreten sind. Eine leere Klammer ( [] ) bedeutet, dass keine Gesichter erkannt wurden. |
| id |Die ID des Gesichts, das nachverfolgt wird. Diese Nummer kann sich unbeabsichtigt ändern, wenn ein Gesicht nicht mehr erkannt wird. Eine Person sollte während des gesamten Videos dieselbe ID behalten, dies kann jedoch aufgrund von Einschränkungen im Erkennungsalgorithmus (Verdecken usw.) nicht garantiert werden. |
| x, y |Die oberen linken X- und Y-Koordinaten des Rahmens, der das Gesicht umgibt, in einem normalisierten Maßstab von 0,0 bis 1,0. <br/>X- und Y-Koordinaten sind immer relativ zum Querformat, d.h. bei einem Video im Hochformat (oder, im Fall von iOS, einem kopfstehenden) müssen Sie die Koordinaten entsprechend vertauschen. |
| width, height |Die Breite und Höhe des Rahmens, der das Gesicht umgibt, in einem normalisierten Maßstab von 0,0 bis 1,0. |
| facesDetected |Dieses Element befindet sich am Ende der JSON-Ergebnisse und fasst die Anzahl der Gesichter zusammen, die der Algorithmus während des Videos erkannt hat. Da die IDs unabsichtlich zurückgesetzt werden können, wenn ein Gesicht nicht mehr erkannt wird (z.B. wenn sich das Gesicht aus dem Bild bewegt oder die Person sich abwendet), entspricht diese Zahl möglicherweise nicht immer der tatsächlichen Anzahl der Gesichter im Video. |

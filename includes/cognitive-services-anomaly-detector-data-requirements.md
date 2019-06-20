---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 03/20/2019
ms.openlocfilehash: 41ac1478b1028a847fc0d5e7e70802375e910837
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178425"
---
> [!NOTE]
> Damit bei der Verwendung der API für die Anomalieerkennung die [besten Ergebnisse](../articles/cognitive-services/anomaly-detector/concepts/anomaly-detection-best-practices.md) erzielt werden, sollten Ihre JSON-formatierten Zeitreihendaten Folgendes enthalten:
> * Datenpunkte, die durch dasselbe Intervall getrennt sind, wobei nicht mehr als 10 % der erwarteten Anzahl von Punkten fehlen.
> * Mindestens 12 Datenpunkte, wenn Ihre Daten kein eindeutiges saisonales Muster aufweisen.
> * Mindestens vier Mustervorkommen, wenn Ihre Daten ein eindeutiges saisonales Muster aufweisen. 

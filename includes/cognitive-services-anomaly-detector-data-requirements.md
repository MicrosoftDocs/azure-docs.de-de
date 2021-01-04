---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 03/20/2019
ms.openlocfilehash: 41ac1478b1028a847fc0d5e7e70802375e910837
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999099"
---
> [!NOTE]
> Damit bei der Verwendung der API für die Anomalieerkennung die [besten Ergebnisse](../articles/cognitive-services/anomaly-detector/concepts/anomaly-detection-best-practices.md) erzielt werden, sollten Ihre JSON-formatierten Zeitreihendaten Folgendes enthalten:
> * Datenpunkte, die durch dasselbe Intervall getrennt sind, wobei nicht mehr als 10 % der erwarteten Anzahl von Punkten fehlen.
> * Mindestens 12 Datenpunkte, wenn Ihre Daten kein eindeutiges saisonales Muster aufweisen.
> * Mindestens vier Mustervorkommen, wenn Ihre Daten ein eindeutiges saisonales Muster aufweisen. 

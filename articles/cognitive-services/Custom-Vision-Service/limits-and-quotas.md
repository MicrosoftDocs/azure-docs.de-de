---
title: Grenzwerte und Kontingente – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Erfahren Sie über Grenzwerte und Kontingente für Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: 85abc4a50710629d3485d05115698e59a93fc96e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60995626"
---
# <a name="limits-and-quotas"></a>Grenzen und Kontingente

Es gibt zwei Schlüsselebenen für Custom Vision Service. Sie können sich über das Azure-Portal für ein F0- (kostenlos) oder S0-Abonnement (Standard) registrieren. Auf der entsprechenden [Cognitive Services-Preisseite](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) finden Sie Informationen zu Preisen und Transaktionen.

Es wird erwartet, dass die Anzahl der Bilder pro Projekt und die Anzahl der Tags pro Projekt für S0-Projekte mit der Zeit ansteigen.

||**F0**|**S0**|
|-----|-----|-----|
|Projekte|2|100|
|Trainingsbilder pro Projekt |5\.000|100\.000|
|Vorhersagen pro Monat|10\.000 |Unbegrenzt|
|Tags pro Projekt|50|500|
|Iterationen |10|10|
|Mindestanzahl der gekennzeichneten Bilder pro Tag, Klassifizierung (> 50 empfohlen) |5|5|
|Mindestanzahl der gekennzeichneten Bilder pro Tag, Objekterkennung (> 50 empfohlen)|15|15|
|Dauer der Speicherung von Vorhersagebildern|30 Tage|30 Tage|
|[Vorhersagevorgänge](https://go.microsoft.com/fwlink/?linkid=865445) mit Speicher (Transaktionen pro Sekunde)|2|10|
|[Vorhersagevorgänge](https://go.microsoft.com/fwlink/?linkid=865445) ohne Speicher (Transaktionen pro Sekunde)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (API-Aufrufe pro Sekunde)|2|10|
|[Sonstige API-Aufrufe](https://go.microsoft.com/fwlink/?linkid=865446) (Transaktionen pro Sekunde)|10|10|
|Maximale Bildgröße (Upload des Trainingsbilds) |6 MB|6 MB|
|Maximale Bildgröße (Vorhersage)|4 MB|4 MB|

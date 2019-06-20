---
title: Ausgabefehlerrichtlinien in Azure Stream Analytics
description: Hier erfahren Sie mehr über die Richtlinien zur Ausgabefehlerbehandlung, die in Azure Stream Analytics verfügbar sind.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: b31530966d2c5ca9a3f82f3e74ba349e66053a83
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478918"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Azure Stream Analytics-Ausgabefehlerrichtlinie
In diesem Artikel werden die Richtlinien zur Ausgabefehlerbehandlung beschrieben, die in Azure Stream Analytics konfiguriert werden können.

Richtlinien zur Ausgabefehlerbehandlung gelten nur für Konvertierungsfehler, die auftreten, wenn das von einem Stream Analytics-Auftrag generierte Ausgabeereignis nicht dem Schema der Zielsenke entspricht. Sie können diese Richtlinie durch Auswählen von **Wiederholen** oder **Verwerfen** konfigurieren. Wählen Sie im Azure-Portal in einem Stream Analytics-Auftrag unter **Konfigurieren** die Option **Fehlerrichtlinie**, um Ihre Auswahl zu treffen.

![Speicherort der Azure Stream Analytics-Ausgabefehlerrichtlinie](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Wiederholen
Wenn ein Fehler auftritt, wiederholt Azure Stream Analytics den Schreibvorgang für das Ereignis unbegrenzt, bis er erfolgreich ist. Für Wiederholungen gilt kein Timeout. Letztendlich wird die Verarbeitung aller nachfolgenden Ereignisse durch das Ereignis blockiert, für das Wiederholungen ausgeführt werden. Diese Option ist die Standardrichtlinie für die Ausgabefehlerbehandlung.

## <a name="drop"></a>Verwerfen
Azure Stream Analytics verwirft jedes Ausgabeereignis, das zu einem Konvertierungsfehler führt. Die verworfenen Ereignisse können nicht zur späteren erneuten Verarbeitung wiederhergestellt werden.


Alle vorübergehenden Fehler (z.B. Netzwerkfehler) werden unabhängig von der Konfiguration der Richtlinien für die Ausgabefehlerbehandlung wiederholt.


## <a name="next-steps"></a>Nächste Schritte
[Leitfaden zur Behandlung von Problemen bei Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)

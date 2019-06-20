---
title: Intelligente Erkennung – Von Azure Application Insights erkannter potenzieller Arbeitsspeicherverlust | Microsoft Docs
description: Überwachen Sie Anwendungen mit Azure Application Insights auf potenzielle Arbeitsspeicherverluste.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: e430b1e976ac26f7320b28d50dd39923066cfa41
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60306342"
---
# <a name="memory-leak-detection-preview"></a>Arbeitsspeicherverlusterkennung (Vorschau)

Application Insights analysiert die Arbeitsspeichernutzung der einzelnen Prozesse in Ihrer Anwendung automatisch und kann Sie vor potenziellen Arbeitsspeicherverlusten oder einer erhöhten Arbeitsspeichernutzung warnen.

Diese Funktion erfordert keine spezielle Einrichtung, abgesehen von der [Konfiguration von Leistungsindikatoren](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) für Ihre App. Sie ist aktiv, wenn Ihre App genügend Telemetriedaten für Arbeitsspeicher-Leistungsindikatoren (z. B. Private Bytes) generiert.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Wann erhalte ich diese Art von Benachrichtigung der intelligenten Erkennung?
Eine typische Benachrichtigung folgt auf einen stetigen Anstieg der Arbeitsspeichernutzung über einen längeren Zeitraum eines oder mehrerer Prozesse und/oder eines oder mehrerer Computer, die Teil Ihrer Anwendung sind. Machine Learning-Algorithmen werden für die Erkennung einer erhöhten Arbeitsspeichernutzung verwendet, die dem Muster eines Speicherverlusts entspricht.

## <a name="does-my-app-really-have-a-problem"></a>Liegt wirklich ein Problem mit meiner App vor?
Nein, eine Benachrichtigung bedeutet nicht zwingend, dass ein Problem mit Ihrer App vorliegt. Obwohl Muster von Arbeitsspeicherverlusten in der Regel auf ein Anwendungsproblem hinweisen, könnten diese Muster typisch für Ihren spezifischen Prozess sein oder einen natürlichen geschäftlichen Grund haben und ignoriert werden.

## <a name="how-do-i-fix-it"></a>Wie behebe ich das Problem?
Die Benachrichtigungen umfassen Diagnoseinformationen zur Unterstützung des diagnostischen Analyseprozesses:
1. **Selektierung.** Die Benachrichtigung zeigt den Umfang der Arbeitsspeichererhöhung (in GB) und den Zeitraum an, in dem die Größe des Arbeitsspeichers zugenommen hat. Dadurch können Sie dem Problem eine Priorität zuweisen.
2. **Umfang.** Wie viele Computer wiesen das Muster des Arbeitsspeicherverlusts auf? Wie viele Ausnahmen wurden während des potenziellen Arbeitsspeicherverlusts ausgelöst? Diese Informationen können Sie der Benachrichtigung entnehmen.
3. **Diagnose.** Die Erkennung enthält das Muster des Arbeitsspeicherverlusts, wobei die Arbeitsspeichernutzung des Prozesses im Laufe der Zeit aufgezeigt wird. Zur weiteren Diagnose des Problems können Sie auch die entsprechenden Elemente und Berichte verwenden, die mit weiterführenden Informationen verknüpft sind.

---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 2f5b03dd0170da9a9869183d7a412688509525ef
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116684"
---
Die `ApplicationInsights`-Einstellung ermöglicht das Hinzufügen von Unterstützung für [Azure Application Insights](https://docs.microsoft.com/azure/application-insights)-Telemetriedaten in Ihrem Container. Application Insights ermöglicht eine tief greifende Überwachung Ihrer Container. Sie können ganz einfach die Verfügbarkeit, Leistung und Nutzung Ihrer Container überwachen. Außerdem können Sie schnell Fehler in Ihrem Container erkennen und diagnostizieren.

In der folgenden Tabelle werden die Konfigurationseinstellungen beschrieben, die unter dem Abschnitt `ApplicationInsights` unterstützt werden.

|Erforderlich| NAME | Datentyp | BESCHREIBUNG |
|--|------|-----------|-------------|
|Nein | `InstrumentationKey` | Zeichenfolge | Der Instrumentierungsschlüssel der Application Insights-Instanz, an die Telemetriedaten für den Container gesendet werden. Weitere Informationen finden Sie unter [Application Insights für ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Beispiel:<br>`InstrumentationKey=123456789`|


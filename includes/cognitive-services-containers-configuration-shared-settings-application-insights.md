---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: d74279c9c4d5d88e5837046e9484f5af7aad1442
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001164"
---
Die `ApplicationInsights`-Einstellung ermöglicht das Hinzufügen von Unterstützung für [Azure Application Insights](/azure/application-insights)-Telemetriedaten in Ihrem Container. Application Insights ermöglicht eine tief greifende Überwachung Ihrer Container. Sie können ganz einfach die Verfügbarkeit, Leistung und Nutzung Ihrer Container überwachen. Außerdem können Sie schnell Fehler in Ihrem Container erkennen und diagnostizieren.

In der folgenden Tabelle werden die Konfigurationseinstellungen beschrieben, die unter dem Abschnitt `ApplicationInsights` unterstützt werden.

|Erforderlich| Name | Datentyp | BESCHREIBUNG |
|--|------|-----------|-------------|
|Nein| `InstrumentationKey` | String | Der Instrumentierungsschlüssel der Application Insights-Instanz, an die Telemetriedaten für den Container gesendet werden. Weitere Informationen finden Sie unter [Application Insights für ASP.NET Core](../articles/azure-monitor/app/asp-net-core.md). <br><br>Beispiel:<br>`InstrumentationKey=123456789`|
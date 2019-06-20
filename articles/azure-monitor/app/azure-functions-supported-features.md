---
title: 'Azure Application Insights: Unterstützte Features von Azure Functions | Microsoft-Dokumentation'
description: In Application Insights unterstützte Features von Azure Functions
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: ''
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: 0199d8f0c4a76a10fffcab7cf2819643d0ac2d68
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075355"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Unterstützte Features in Application Insights für Azure Functions

Azure Functions bietet [von Haus aus Integration](https://docs.microsoft.com/azure/azure-functions/functions-monitoring) in Application Insights, die über die ILogger-Schnittstelle verfügbar ist. Unten folgt die Liste der aktuell unterstützten Features. Arbeiten Sie zum [Einstieg](https://github.com/Azure/Azure-Functions/wiki/App-Insights) das Azure Functions-Handbuch durch.

## <a name="supported-features"></a>Unterstützte Features

| Azure-Funktionen                       | V1                | V2 (Ignite 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Application Insights .NET SDK**   | **2.5.0**       | **2.9.1**         |
| | | | 
| **Automatische Sammlung von**        |                 |                   |               
| &bull;Anforderungen                     | Ja             | Ja               | 
| &bull;Ausnahmen                   | Ja             | Ja               | 
| &bull; Leistungsindikatoren         | Ja             | Ja               |
| &bull;Abhängigkeiten                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | Ja               | 
| &nbsp;&nbsp;&nbsp;&mdash; ServiceBus|                 | Ja               | 
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |                 | Ja               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | Ja               | 
| | | | 
| **Unterstützte Features**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | Ja             | Ja               | 
| &nbsp;&nbsp;&nbsp;&mdash; Sicherer Steuerkanal|                 | Ja               | 
| &bull; Stichprobenentnahme                     | Ja             | Ja               | 
| &bull; Heartbeats                   |                 | Ja               | 
| | | | 
| **Korrelation**                       |                   |                   |               
| &bull; ServiceBus                     |                   | Ja               | 
| &bull; EventHub                       |                   | Ja               | 
| | | | 
| **Konfigurierbar**                      |                   |                   |           
| &bull;Vollständig konfigurierbar.<br/>Anweisungen finden Sie unter [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852).<br/>Alle Optionen finden Sie unter [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration).               |                   | Ja                   | 


## <a name="performance-counters"></a>Leistungsindikatoren

Automatische Erfassung von Leistungsindikatoren kann nur für Windows-Computer verwendet werden.


## <a name="live-metrics--secure-control-channel"></a>Livemetriken und sicherer Steuerkanal

Die von Ihnen angegebenen benutzerdefinierten Filterkriterien werden an die Livemetrikkomponente des Application Insights SDK zurückgesendet. Der Filter können potenziell vertrauliche Informationen wie z.B. Kunden-IDs enthalten. Sie können den Kanal mit einem geheimen API-Schlüssel sicher machen. Anleitungen dazu finden Sie unter [Sichern des Steuerkanals](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel).

## <a name="sampling"></a>Stichproben

Azure Functions aktiviert die Stichprobenentnahme in der Konfiguration standardmäßig. Weitere Informationen finden Sie unter [Configure Sampling](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling) (Konfigurieren der Stichprobenentnahme).

Wenn Ihr Projekt zur manuellen Nachverfolgung der Telemetrie vom Application Insights SDK abhängig ist, kann ein unerwartetes Verhalten auftreten, wenn Ihre Konfiguration der Stichprobenentnahme von der Konfiguration in Functions abweicht. 

Es wird empfohlen, die gleiche Konfiguration wie Functions zu verwenden. Bei **Functions v2** können Sie mithilfe der Abhängigkeitsinjektion in Ihrem Konstruktor die gleiche Konfiguration erzielen:

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;

public class Function1 
{

    private readonly TelemetryClient telemetryClient;

    public Function1(TelemetryConfiguration configuration)
    {
        this.telemetryClient = new TelemetryClient(configuration);
    }

    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger logger)
    {
        this.telemetryClient.TrackTrace("C# HTTP trigger function processed a request.");
    }
}
```

---
title: Azure Service Bus – End-to-End-Ablaufverfolgung und Diagnose | Microsoft Docs
description: Übersicht über Service Bus-Clientdiagnose und End-to-End-Ablaufverfolgung (Client durch alle Dienste, die an der Verarbeitung beteiligt sind).
ms.topic: article
ms.date: 02/03/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 19b284aceb83fbbc2bcf662b2b58941e6a5b36f9
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539212"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Verteilte Ablaufverfolgung und Korrelation über Service Bus-Messaging

Eine der häufigsten Herausforderungen bei der Microservice-Entwicklung besteht darin, Abläufe von einem Client aus über alle an der Verarbeitung beteiligten Dienste zu verfolgen. Eine solche Fähigkeit ist nützlich für das Debuggen, die Leistungsanalyse, A/B-Tests und andere gängige Diagnoseszenarien.
Ein Teil des Problems besteht darin, die logischen Arbeitsschritte nachzuverfolgen. Dies umfasst neben der Auswertung der Nachrichtenverarbeitung auch die Latenz und externe Abhängigkeitsaufrufe. Der andere Teil ist die Korrelation dieser Diagnoseereignisse über Prozessgrenzen hinweg.

Wenn ein Producer eine Nachricht über eine Warteschlange sendet, geschieht dies in der Regel im Rahmen eines anderen logischen Vorgangs, der von einem anderen Client oder Dienst initiiert wird. Derselbe Vorgang wird vom Consumer fortgesetzt, sobald dieser eine Nachricht empfängt. Meistens gibt sowohl der Producer als auch der Consumer (sowie andere an der Verarbeitung beteiligten Dienste) Telemetrieereignisse aus, die zur Nachverfolgung des Vorgangsflusses und des Ergebnisses herangezogen werden. Um diese Ereignisse zu korrelieren und den Vorgang bis zum Ende zu verfolgen, muss jeder Dienst, der Telemetriedaten meldet, die einzelnen Ereignisse mit einem Ablaufverfolgungskontext kennzeichnen.

Microsoft Azure Service Bus-Messaging verfügt über definierte Nutzlasteigenschaften, die sowohl von Producern als auch von Consumern zur Übergabe dieser Ablaufverfolgungskontexte verwendet werden müssen.
Das Protokoll basiert auf dem [HTTP-Korrelationsprotokoll](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md).

# <a name="azuremessagingservicebus-sdk-latest"></a>[Azure.Messaging.ServiceBus-SDK (neuste Version)](#tab/net-standard-sdk-2)
| Eigenschaftenname        | BESCHREIBUNG                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostic-Id       | Ein eindeutiger Bezeichner eines externen Aufrufs, der vom Producer an die Warteschlange gesendet wird. Unter [Request-Id im HTTP-Protokoll](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) finden Sie weitere Begründungen, Überlegungen und Informationen zum Format |

## <a name="service-bus-net-client-autotracing"></a>Automatische Ablaufverfolgung im Service Bus-Client für .NET
Die Klasse `ServiceBusProcessor` des [Azure Messaging-Service Bus-Clients für .NET](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) stellt Instrumentierungspunkte für die Ablaufverfolgung bereit, die von Ablaufverfolgungssystemen oder von Clientcode verwendet werden können. Die Instrumentierung ermöglicht die Nachverfolgung aller Aufrufe, die von Clientseite an den Service Bus-Messagingdienst gesendet werden. Wenn die Nachrichtenverarbeitung über [`ProcessMessageAsync` von `ServiceBusProcessor`](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) (Meldungshandlermuster) erfolgt, wird die Nachrichtenverarbeitung ebenfalls instrumentiert.

### <a name="tracking-with-azure-application-insights"></a>Nachverfolgung mithilfe von Azure Application Insights

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) bietet umfassende Funktionen zur Leistungsüberwachung, darunter die automatische Nachverfolgung von Anforderungen und Abhängigkeiten.

Installieren Sie je nach Projekttyp ggf. das Application Insights SDK:
- [ASP.NET](../azure-monitor/app/asp-net.md): Installieren Sie Version 2.5-beta2 oder höher.
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md): Installieren Sie Version 2.2.0-beta2 oder höher.
Über diese Links können Sie Details zur Erstellung von Ressourcen sowie zur Installation und Konfiguration des SDKs (falls erforderlich) abrufen. Weitere Informationen zu Nicht-ASP.NET-Anwendungen finden Sie im Artikel [Azure Application Insights für Konsolenanwendungen](../azure-monitor/app/console.md).

Wenn Sie [`ProcessMessageAsync` von `ServiceBusProcessor`](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) (Meldungshandlermuster) zur Verarbeitung von Nachrichten verwenden, wird die Nachrichtenverarbeitung ebenfalls instrumentiert. Alle von Ihrem Dienst durchgeführten Service Bus-Aufrufe werden automatisch nachverfolgt und mit anderen Telemetrieelementen korreliert. Andernfalls finden Sie im folgenden Beispiel Informationen zur manuellen Nachverfolgung der Nachrichtenverarbeitung.

#### <a name="trace-message-processing"></a>Nachrichtenverarbeitung nachverfolgen

```csharp
async Task ProcessAsync(ProcessMessageEventArgs args)
{
    ServiceBusReceivedMessage message = args.Message;
    if (message.ApplicationProperties.TryGetValue("Diagnostic-Id", out var objectId) && objectId is string diagnosticId)
    {
        var activity = new Activity("ServiceBusProcessor.ProcessMessage");
        activity.SetParentId(diagnosticId);
        // If you're using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
        using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
        {
            telemetryClient.TrackTrace("Received message");
            try 
            {
            // process message
            }
            catch (Exception ex)
            {
                telemetryClient.TrackException(ex);
                operation.Telemetry.Success = false;
                throw;
            }

            telemetryClient.TrackTrace("Done");
        }
    }
}
```

In diesem Beispiel wird für jede verarbeitete Nachricht Anforderungstelemetrie einschließlich Zeitstempel, Dauer und Ergebnis (Erfolg) gemeldet. Zur Telemetrie gehört auch eine Gruppe von Korrelationseigenschaften. Geschachtelte Ablaufverfolgungen und Ausnahmen, die während der Nachrichtenverarbeitung gemeldet werden, werden ebenfalls mit Korrelationseigenschaften gekennzeichnet, um sie als "untergeordnete Elemente" von `RequestTelemetry` auszuweisen.

Falls Sie während der Nachrichtenverarbeitung unterstützte externe Komponenten aufrufen, werden diese ebenfalls automatisch nachverfolgt und korreliert. Weitere Informationen zur manuellen Nachverfolgung und Korrelation finden Sie unter [Nachverfolgen benutzerdefinierter Vorgänge mit dem Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md).

Wenn Sie zusätzlich zum Application Insights SDK externen Code ausführen, sollten Sie bei der Anzeige von Application Insights-Protokollen von einer längeren **Dauer** ausgehen. 

![Längere Dauer in Application Insights-Protokoll](./media/service-bus-end-to-end-tracing/longer-duration.png)

Dies bedeutet nicht, dass eine Verzögerung beim Empfangen der Nachricht aufgetreten ist. In diesem Szenario wurde die Nachricht bereits empfangen, weil die Nachricht als Parameter an den SDK-Code übergeben wird. Außerdem gibt das Tag **Name** in App Insights-Protokollen (**Process**) an, dass die Nachricht nun von Ihrem externen Ereignisverarbeitungscode verarbeitet wird. Dieses Problem bezieht sich nicht auf Azure. Stattdessen verweisen diese Metriken auf die Effizienz Ihres externen Codes, wenn die Nachricht bereits von Service Bus empfangen wurde. 

### <a name="tracking-without-tracing-system"></a>Nachverfolgung ohne Ablaufverfolgungssystem
Falls die automatische Nachverfolgung von Service Bus-Aufrufen von Ihrem Ablaufverfolgungssystem nicht unterstützt wird, empfiehlt es sich, das Ablaufverfolgungssystem oder die Anwendung entsprechend zu erweitern. In diesem Abschnitt werden die Diagnoseereignisse beschrieben, die vom Service Bus .NET-Client gesendet werden.  

Der Service Bus .NET-Client verwendet zwei primitive Typen, um die .NET-Ablaufverfolgung zu instrumentieren: [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) und [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity` dient als Ablaufverfolgungskontext, während `DiagnosticSource` ein Benachrichtigungsmechanismus ist. 

Falls kein Listener für die DiagnosticSource-Ereignisse vorhanden ist, wird die Instrumentierung deaktiviert. In diesem Fall liegen die Instrumentierungskosten bei null. DiagnosticSource übergibt die Kontrolle vollständig an den Listener:
- Der Listener steuert, welche Datenquellen und Ereignisse überwacht werden.
- Der Listener steuert die Ereignisrate und den Stichprobenumfang.
- Ereignisse werden mit einer Nutzlast gesendet, die vollständigen Kontext bereitstellt. Auf diese Weise können Sie das Message-Objekt während des Ereignisses abrufen und ändern.

Machen Sie sich mit dem [DiagnosticSource-Benutzerleitfaden](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) vertraut, bevor Sie mit der Implementierung fortfahren.

Als Nächstes erstellen wir einen Listener für Service Bus-Ereignisse in der ASP.NET Core-App, die die Protokollierung mithilfe von Microsoft.Extension.Logger durchführt.
Dabei wird die [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core)-Bibliothek verwendet, um DiagnosticSource zu abonnieren (aber auch ohne Bibliothek kann DiagnosticSource ganz einfach abonniert werden).

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Azure.Messaging.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Azure.Messaging.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

In diesem Beispiel werden die Dauer, das Ergebnis, der eindeutige Bezeichner und die Startzeit der einzelnen Service Bus-Vorgänge vom Listener protokolliert.

### <a name="events"></a>Events
Alle Ereignisse verfügen über die folgenden Eigenschaften, die der Spezifikation für offene Telemetrie entsprechen: https://github.com/open-telemetry/opentelemetry-specification/blob/master/specification/trace/api.md.

- `message_bus.destination`: Pfad zu Warteschlange/Thema/Abonnement.
- `peer.address`: Vollqualifizierter Namespace.
- `kind`: Producer, Consumer oder Client. Der Producer wird beim Senden, der Consumer beim Empfangen und der Client beim Speichern von Nachrichten verwendet.
- `component` – `servicebus`

Alle Ereignisse verfügen außerdem über die Eigenschaften „Entity“ und „Endpoint“, die in der folgenden Tabelle nicht berücksichtigt werden.
  * `string Entity` – der Name der Entität (z. B. Warteschlange oder Thema)
  * `Uri Endpoint` – die URL des Service Bus-Endpunkts

### <a name="instrumented-operations"></a>Instrumentierte Vorgänge
Im Folgenden finden Sie eine vollständige Liste der instrumentierten Vorgänge:

| Vorgangsname | Nachverfolgte API |
| -------------- | ----------- | 
| ServiceBusSender.Send | ServiceBusSender.SendMessageAsync<br/>ServiceBusSender.SendMessagesAsync |
| ServiceBusSender.Schedule | ServiceBusSender.ScheduleMessageAsync<br/>ServiceBusSender.ScheduleMessagesAsync | 
| ServiceBusSender.Cancel | ServiceBusSender.CancelScheduledMessageAsync<br/>ServiceBusSender.CancelScheduledMessagesAsync |
| ServiceBusReceiver.Receive | ServiceBusReceiver.ReceiveMessageAsync<br/>ServiceBusReceiver.ReceiveMessagesAsync |
| ServiceBusReceiver.ReceiveDeferred | ServiceBusReceiver.ReceiveDeferredMessagesAsync |
| ServiceBusReceiver.Peek | ServiceBusReceiver.PeekMessageAsync<br/>ServiceBusReceiver.PeekMessagesAsync |
| ServiceBusReceiver.Abandon | ServiceBusReceiver.AbandonMessagesAsync |
| ServiceBusReceiver.Complete | ServiceBusReceiver.CompleteMessagesAsync |
| ServiceBusReceiver.DeadLetter | ServiceBusReceiver.DeadLetterMessagesAsync |
| ServiceBusReceiver.Defer |  ServiceBusReceiver.DeferMessagesAsync |
| ServiceBusReceiver.RenewMessageLock | ServiceBusReceiver.RenewMessageLockAsync |
| ServiceBusSessionReceiver.RenewSessionLock | ServiceBusSessionReceiver.RenewSessionLockAsync |
| ServiceBusSessionReceiver.GetSessionState | ServiceBusSessionReceiver.GetSessionStateAsync |
| ServiceBusSessionReceiver.SetSessionState | ServiceBusSessionReceiver.SetSessionStateAsync |
| ServiceBusProcessor.ProcessMessage | Der für ServiceBusProcessor festgelegte Prozessorrückruf. Eigenschaft „ProcessMessageAsync“ |
| ServiceBusSessionProcessor.ProcessSessionMessage | Der für ServiceBusSessionProcessor festgelegte Prozessorrückruf. Eigenschaft „ProcessMessageAsync“ |

### <a name="filtering-and-sampling"></a>Filtern und Stichprobenumfang

In einigen Fällen ist es wünschenswert, nur einen Teil der Ereignisse zu protokollieren, um den zusätzlichen Leistungsaufwand oder die Speicherauslastung zu reduzieren. Beispielsweise können Sie nur Stop-Ereignisse (wie im vorangehenden Beispiel) oder einen Stichprobenprozentsatz protokollieren. 
`DiagnosticSource` bietet zu diesem Zweck das `IsEnabled`-Prädikat. Weitere Informationen finden Sie unter [Kontextbasierte Filterung in DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering).

`IsEnabled` kann mehrmals für einen einzelnen Vorgang aufgerufen werden, um Leistungseinbußen zu minimieren.

`IsEnabled` wird in der folgenden Abfolge aufgerufen:

1. `IsEnabled(<OperationName>, string entity, null)` entspricht `IsEnabled("ServiceBusSender.Send", "MyQueue1")`. Hinweis: Das Start- und das Stop-Ereignis am Ende fehlen. Verwenden Sie das Element, um bestimmte Vorgänge oder Warteschlangen herauszufiltern. Wenn durch die Rückrufmethode `false` zurückgegeben wird, werden keine Ereignisse für den Vorgang gesendet.

   * Für den Process-Vorgang und den ProcessSession-Vorgang wird ebenfalls ein `IsEnabled(<OperationName>, string entity, Activity activity)`-Rückruf empfangen. Verwenden Sie das Element, um Ereignisse auf der Basis von `activity.Id`-Eigenschaften oder Tags-Eigenschaften zu filtern.
  
2. `IsEnabled(<OperationName>.Start)` entspricht `IsEnabled("ServiceBusSender.Send.Start")`. Durch diesen Code wird überprüft, ob das Start-Ereignis ausgelöst werden soll. Das Ergebnis wirkt sich nur auf das Start-Ereignis, nicht aber auf die weitere Instrumentierung aus.

Für das Stop-Ereignis ist keine `IsEnabled`-Eigenschaft vorhanden.

Wenn ein Vorgang eine Ausnahme zurückgibt, wird `IsEnabled("ServiceBusSender.Send.Exception")` aufgerufen. Sie können auch nur Exception-Ereignisse abonnieren und die übrigen Instrumentierungsschritte auslassen. In diesem Fall müssen solche Ausnahmen trotzdem behandelt werden. Da die weitere Instrumentierung deaktiviert ist, werden die Nachrichten zwischen Consumer und Producer ohne Ablaufverfolgungskontext weitergeleitet.

Sie können `IsEnabled` auch zur Implementierung von Stichprobenstrategien verwenden. Durch die Stichprobenentnahme auf der Grundlage von `Activity.Id` oder `Activity.RootId` wird über alle Ebenen eine konsistente Stichprobenentnahme sichergestellt (sofern die Weiterleitung über das Ablaufverfolgungssystem oder den eigenen Code erfolgt).

Falls mehrere `DiagnosticSource`-Listener für dieselbe Quelle vorhanden sind, muss nur ein Listener das Ereignis akzeptieren. Es kann also sein, dass `IsEnabled` nicht aufgerufen wird.



# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

| Eigenschaftenname        | BESCHREIBUNG                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostic-Id       | Ein eindeutiger Bezeichner eines externen Aufrufs, der vom Producer an die Warteschlange gesendet wird. Unter [Request-Id im HTTP-Protokoll](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) finden Sie weitere Begründungen, Überlegungen und Informationen zum Format |
|  Correlation-Context | des Vorgangskontexts, der über alle Dienste hinweg weitergegeben wird, die an der Vorgangsverarbeitung beteiligt sind. Weitere Informationen finden Sie unter [Correlation-Context im HTTP-Protokoll](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context). |

## <a name="service-bus-net-client-autotracing"></a>Automatische Ablaufverfolgung im Service Bus-Client für .NET

Ab Version 3.0.0 stellt der [Microsoft Azure Service Bus-Client für .NET](/dotnet/api/microsoft.azure.servicebus.queueclient) Instrumentierungspunkte für die Ablaufverfolgung bereit, die von Ablaufverfolgungssystemen oder von Clientcode verwendet werden können.
Die Instrumentierung ermöglicht die Nachverfolgung aller Aufrufe, die von Clientseite an den Service Bus-Messagingdienst gesendet werden. Nachdem die Nachrichtenverarbeitung nach dem [Muster „Meldungshandler“](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) abgeschlossen wurde, wird die Nachrichtenverarbeitung ebenfalls instrumentiert.

### <a name="tracking-with-azure-application-insights"></a>Nachverfolgung mithilfe von Azure Application Insights

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) bietet umfassende Funktionen zur Leistungsüberwachung, darunter die automatische Nachverfolgung von Anforderungen und Abhängigkeiten.

Installieren Sie je nach Projekttyp ggf. das Application Insights SDK:
- [ASP.NET](../azure-monitor/app/asp-net.md): Installieren Sie Version 2.5-beta2 oder höher.
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md): Installieren Sie Version 2.2.0-beta2 oder höher.
Über diese Links können Sie Details zur Erstellung von Ressourcen sowie zur Installation und Konfiguration des SDKs (falls erforderlich) abrufen. Weitere Informationen zu Nicht-ASP.NET-Anwendungen finden Sie im Artikel [Azure Application Insights für Konsolenanwendungen](../azure-monitor/app/console.md).

Wenn Sie das [Muster „Meldungshandler“](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) zum Verarbeiten von Nachrichten verwenden, sind Sie bereits fertig: Alle Service Bus-Aufrufe, die von Ihrem Dienst durchgeführt werden, werden automatisch nachverfolgt und mit anderen Telemetrieelementen korreliert. Andernfalls finden Sie im folgenden Beispiel Informationen zur manuellen Nachverfolgung der Nachrichtenverarbeitung.

#### <a name="trace-message-processing"></a>Nachrichtenverarbeitung nachverfolgen

```csharp
private readonly TelemetryClient telemetryClient;

async Task ProcessAsync(Message message)
{
    var activity = message.ExtractActivity();
    
    // If you're using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
    using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
    {
        telemetryClient.TrackTrace("Received message");
        try 
        {
           // process message
        }
        catch (Exception ex)
        {
             telemetryClient.TrackException(ex);
             operation.Telemetry.Success = false;
             throw;
        }

        telemetryClient.TrackTrace("Done");
   }
}
```

In diesem Beispiel wird `RequestTelemetry` für jede verarbeitete Nachricht einschließlich Zeitstempel, Dauer und Ergebnis (Erfolg) gemeldet. Zur Telemetrie gehört auch eine Gruppe von Korrelationseigenschaften.
Geschachtelte Ablaufverfolgungen und Ausnahmen, die während der Nachrichtenverarbeitung gemeldet werden, werden ebenfalls mit Korrelationseigenschaften gekennzeichnet, um sie als "untergeordnete Elemente" von `RequestTelemetry` auszuweisen.

Falls Sie während der Nachrichtenverarbeitung unterstützte externe Komponenten aufrufen, werden diese ebenfalls automatisch nachverfolgt und korreliert. Weitere Informationen zur manuellen Nachverfolgung und Korrelation finden Sie unter [Nachverfolgen benutzerdefinierter Vorgänge mit dem Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md).

Wenn Sie zusätzlich zum Application Insights SDK externen Code ausführen, sollten Sie bei der Anzeige von Application Insights-Protokollen von einer längeren **Dauer** ausgehen. 

![Längere Dauer in Application Insights-Protokoll](./media/service-bus-end-to-end-tracing/longer-duration.png)

Dies bedeutet nicht, dass eine Verzögerung beim Empfangen der Nachricht aufgetreten ist. In diesem Szenario wurde die Nachricht bereits empfangen, weil die Nachricht als Parameter an den SDK-Code übergeben wird. Außerdem gibt das Tag **Name** in App Insights-Protokollen (**Process**) an, dass die Nachricht nun von Ihrem externen Ereignisverarbeitungscode verarbeitet wird. Dieses Problem bezieht sich nicht auf Azure. Stattdessen verweisen diese Metriken auf die Effizienz Ihres externen Codes, wenn die Nachricht bereits von Service Bus empfangen wurde. Weitere Informationen dazu, wo das **Prozess**-Tag generiert und zugewiesen wird, sobald die Nachricht von Service Bus empfangen wurde, finden Sie [in dieser Datei auf GitHub](https://github.com/Azure/azure-sdk-for-net/blob/4bab05144ce647cc9e704d46d3763de5f9681ee0/sdk/servicebus/Microsoft.Azure.ServiceBus/src/ServiceBusDiagnosticsSource.cs). 

### <a name="tracking-without-tracing-system"></a>Nachverfolgung ohne Ablaufverfolgungssystem
Falls die automatische Nachverfolgung von Service Bus-Aufrufen von Ihrem Ablaufverfolgungssystem nicht unterstützt wird, empfiehlt es sich, das Ablaufverfolgungssystem oder die Anwendung entsprechend zu erweitern. In diesem Abschnitt werden die Diagnoseereignisse beschrieben, die vom Service Bus .NET-Client gesendet werden.  

Der Service Bus .NET-Client verwendet zwei primitive Typen, um die .NET-Ablaufverfolgung zu instrumentieren: [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) und [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity` dient als Ablaufverfolgungskontext, während `DiagnosticSource` ein Benachrichtigungsmechanismus ist. 

Falls kein Listener für die DiagnosticSource-Ereignisse vorhanden ist, wird die Instrumentierung deaktiviert. In diesem Fall liegen die Instrumentierungskosten bei null. DiagnosticSource übergibt die Kontrolle vollständig an den Listener:
- Der Listener steuert, welche Datenquellen und Ereignisse überwacht werden.
- Der Listener steuert die Ereignisrate und den Stichprobenumfang.
- Ereignisse werden mit einer Nutzlast gesendet, die vollständigen Kontext bereitstellt. Auf diese Weise können Sie das Message-Objekt während des Ereignisses abrufen und ändern.

Machen Sie sich mit dem [DiagnosticSource-Benutzerleitfaden](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) vertraut, bevor Sie mit der Implementierung fortfahren.

Als Nächstes erstellen wir einen Listener für Service Bus-Ereignisse in der ASP.NET Core-App, die die Protokollierung mithilfe von Microsoft.Extension.Logger durchführt.
Dabei wird die [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core)-Bibliothek verwendet, um DiagnosticSource zu abonnieren (aber auch ohne Bibliothek kann DiagnosticSource ganz einfach abonniert werden).

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Microsoft.Azure.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Microsoft.Azure.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

In diesem Beispiel werden die Dauer, das Ergebnis, der eindeutige Bezeichner und die Startzeit der einzelnen Service Bus-Vorgänge vom Listener protokolliert.

#### <a name="events"></a>Events

Für jeden Vorgang werden zwei Ereignisse gesendet: „Start“ und „Stop“. In den meisten Fällen kommt es nur auf die Stop-Ereignisse an. Diese enthalten neben dem Ergebnis des Vorgangs die Startzeit und die Dauer in Form von Activity-Eigenschaften.

Die Ereignisnutzlast stellt einen Listener mit dem Vorgangskontext bereit, repliziert eingehende API-Parameter und gibt einen Wert zurück. Die Stop-Ereignisnutzlast verfügt über alle Eigenschaften der Start-Ereignisnutzlast, sodass Sie das Start-Ereignis vollständig außer Acht lassen können.

Alle Ereignisse verfügen außerdem über die Eigenschaften „Entity“ und „Endpoint“, die in der folgenden Tabelle nicht berücksichtigt werden.
  * `string Entity` – der Name der Entität (Warteschlange, Thema usw.)
  * `Uri Endpoint` – die URL des Service Bus-Endpunkts

Jedes Stop-Ereignis verfügt über die `Status`-Eigenschaft, mit der der asynchrone `TaskStatus`-Vorgang abgeschlossen wurde. Aus Gründen der Übersichtlichkeit wird die Eigenschaft in der folgenden Tabelle nicht berücksichtigt.

Im Folgenden finden Sie eine vollständige Liste der instrumentierten Vorgänge:

| Vorgangsname | Nachverfolgte API | Bestimmte Nutzlasteigenschaften|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MessageSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | `IList<Message> Messages`: Liste der gesendeten Nachrichten |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | `Message Message`: die gerade verarbeitete Nachricht<br/>`DateTimeOffset ScheduleEnqueueTimeUtc`: Offset der geplanten Nachricht<br/>`long SequenceNumber`: Sequenznummer der geplanten Nachricht (Stop-Ereignisnutzlast) |
| Microsoft.Azure.ServiceBus.Cancel | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | `long SequenceNumber`: Sequenznummer der abzubrechenden Nachricht | 
| Microsoft.Azure.ServiceBus.Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) | `int RequestedMessageCount`: maximale Anzahl von Nachrichten, die empfangen werden können<br/>`IList<Message> Messages`: Liste der empfangenen Nachrichten (Stop-Ereignisnutzlast) |
| Microsoft.Azure.ServiceBus.Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | `int FromSequenceNumber`: Startpunkt für das Durchsuchen eines Nachrichtenbatches<br/>`int RequestedMessageCount`: Anzahl von Nachrichten, die abgerufen werden sollen<br/>`IList<Message> Messages`: Liste der empfangenen Nachrichten (Stop-Ereignisnutzlast) |
| Microsoft.Azure.ServiceBus.ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | `IEnumerable<long> SequenceNumbers`: Liste mit den zu empfangenden Sequenznummern<br/>`IList<Message> Messages`: Liste der empfangenen Nachrichten (Stop-Ereignisnutzlast) |
| Microsoft.Azure.ServiceBus.Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | `IList<string> LockTokens`: Liste mit den Sperrtoken der entsprechenden Nachrichten, die abgeschlossen werden sollen|
| Microsoft.Azure.ServiceBus.Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | `string LockToken`: Sperrtoken der Nachricht, die abgebrochen werden soll |
| Microsoft.Azure.ServiceBus.Defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | `string LockToken`: Sperrtoken der Nachricht, die zurückgestellt werden soll | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | `string LockToken`: Sperrtoken der Nachricht, die als unzustellbar markiert werden soll | 
| Microsoft.Azure.ServiceBus.RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | `string LockToken`: Sperrtoken der Nachricht, für die die Sperre verlängert werden soll<br/>`DateTime LockedUntilUtc`: Neue Datums-/Uhrzeitangabe für den Ablauf des Sperrtokens im UTC-Format (Stop-Ereignisnutzlast)|
| Microsoft.Azure.ServiceBus.Process | Die in [IReceiverClient.RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) bereitgestellte Lambda-Funktion des Meldungshandlers | `Message Message`: die gerade verarbeitete Nachricht |
| Microsoft.Azure.ServiceBus.ProcessSession | Die in [IQueueClient.RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) bereitgestellte Lambda-Funktion des Handlers der Nachrichtensitzung | `Message Message`: die gerade verarbeitete Nachricht<br/>`IMessageSession Session`: die gerade verarbeitete Sitzung |
| Microsoft.Azure.ServiceBus.AddRule | [SubscriptionClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | `RuleDescription Rule`: Regelbeschreibung mit der hinzuzufügenden Regel |
| Microsoft.Azure.ServiceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | `string RuleName`: Name der Regel, die entfernt werden soll |
| Microsoft.Azure.ServiceBus.GetRules | [SubscriptionClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | `IEnumerable<RuleDescription> Rules`: alle Regeln, die dem Abonnement zugeordnet sind (nur Stop-Nutzlast) |
| Microsoft.Azure.ServiceBus.AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | `string SessionId`: in den Nachrichten enthaltene sessionId |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | `string SessionId`: in den Nachrichten enthaltene sessionId<br/>`byte [] State`: Sitzungszustand (Stop-Ereignisnutzlast) |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | `string SessionId`: in den Nachrichten enthaltene sessionId<br/>`byte [] State`: Sitzungszustand |
| Microsoft.Azure.ServiceBus.RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | `string SessionId`: in den Nachrichten enthaltene sessionId |
| Microsoft.Azure.ServiceBus.Exception | eine beliebige instrumentierte API| `Exception Exception`: Ausnahmeinstanz |

In jedem Ereignis haben Sie Zugriff auf das `Activity.Current`-Element, das den aktuellen Vorgangskontext enthält.

#### <a name="logging-more-properties"></a>Protokollieren weiterer Eigenschaften

`Activity.Current` stellt detaillierte Kontextinformationen zum aktuellen Vorgang und untergeordneten Vorgängen bereit. Weitere Informationen finden Sie in der [Dokumentation zu Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md).
Die Service Bus-Instrumentierung stellt weitere Informationen in `Activity.Current.Tags` bereit. Diese enthalten `MessageId` und `SessionId`, sofern verfügbar.

Aktivitäten, durch die die Ereignisse Receive, Peek und ReceiveDeferred nachverfolgt werden, können ebenfalls das `RelatedTo`-Tag aufweisen. Das Tag enthält eine genaue Liste mit den `Diagnostic-Id`(s) der Nachrichten, die als Ergebnis empfangen wurden.
Ein solcher Vorgang kann dazu führen, dass mehrere unverbundene Nachrichten empfangen werden. Da die `Diagnostic-Id` zu Beginn des Vorgangs nicht bekannt ist, kann nur dieses Tag helfen, Receive-Vorgänge mit Process-Vorgängen zu korrelieren. Es ist außerdem nützlich für die Analyse von Leistungsproblemen, um zu überprüfen, wie lange der Nachrichtenempfang gedauert hat.

Sie können Tags auch durchlaufen, um sie effizient zu protokollieren. Im Folgenden ist dargestellt, wie der vorangehende Code mit Tags aussieht: 

```csharp
Activity currentActivity = Activity.Current;
TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");

var tagsList = new StringBuilder();
foreach (var tags in currentActivity.Tags)
{
    tagsList.Append($", {tags.Key}={tags.Value}");
}

serviceBusLogger.LogInformation($"{currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}{tagsList}");
```

#### <a name="filtering-and-sampling"></a>Filtern und Stichprobenumfang

In einigen Fällen ist es wünschenswert, nur einen Teil der Ereignisse zu protokollieren, um den zusätzlichen Leistungsaufwand oder die Speicherauslastung zu reduzieren. Beispielsweise können Sie nur Stop-Ereignisse (wie im vorangehenden Beispiel) oder einen Stichprobenprozentsatz protokollieren. 
`DiagnosticSource` bietet zu diesem Zweck das `IsEnabled`-Prädikat. Weitere Informationen finden Sie unter [Kontextbasierte Filterung in DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering).

`IsEnabled` kann mehrmals für einen einzelnen Vorgang aufgerufen werden, um Leistungseinbußen zu minimieren.

`IsEnabled` wird in der folgenden Abfolge aufgerufen:

1. `IsEnabled(<OperationName>, string entity, null)` entspricht `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")`. Hinweis: Das Start- und das Stop-Ereignis am Ende fehlen. Verwenden Sie das Element, um bestimmte Vorgänge oder Warteschlangen herauszufiltern. Wenn durch die Rückrufmethode `false` zurückgegeben wird, werden keine Ereignisse für den Vorgang gesendet.

   * Für den Process-Vorgang und den ProcessSession-Vorgang wird ebenfalls ein `IsEnabled(<OperationName>, string entity, Activity activity)`-Rückruf empfangen. Verwenden Sie das Element, um Ereignisse auf der Basis von `activity.Id`-Eigenschaften oder Tags-Eigenschaften zu filtern.
  
2. `IsEnabled(<OperationName>.Start)` entspricht `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")`. Durch diesen Code wird überprüft, ob das Start-Ereignis ausgelöst werden soll. Das Ergebnis wirkt sich nur auf das Start-Ereignis, nicht aber auf die weitere Instrumentierung aus.

Für das Stop-Ereignis ist keine `IsEnabled`-Eigenschaft vorhanden.

Wenn ein Vorgang eine Ausnahme zurückgibt, wird `IsEnabled("Microsoft.Azure.ServiceBus.Exception")` aufgerufen. Sie können auch nur Exception-Ereignisse abonnieren und die übrigen Instrumentierungsschritte auslassen. In diesem Fall müssen solche Ausnahmen trotzdem behandelt werden. Da die weitere Instrumentierung deaktiviert ist, werden die Nachrichten zwischen Consumer und Producer ohne Ablaufverfolgungskontext weitergeleitet.

Sie können `IsEnabled` auch zur Implementierung von Stichprobenstrategien verwenden. Durch die Stichprobenentnahme auf der Grundlage von `Activity.Id` oder `Activity.RootId` wird über alle Ebenen eine konsistente Stichprobenentnahme sichergestellt (sofern die Weiterleitung über das Ablaufverfolgungssystem oder den eigenen Code erfolgt).

Falls mehrere `DiagnosticSource`-Listener für dieselbe Quelle vorhanden sind, muss nur ein Listener das Ereignis akzeptieren. Es kann also sein, dass `IsEnabled` nicht aufgerufen wird.

---

## <a name="next-steps"></a>Nächste Schritte

* [Application Insights-Korrelation](../azure-monitor/app/correlation.md)
* [Abhängigkeitsüberwachung in Application Insights](../azure-monitor/app/asp-net-dependencies.md), um zu überprüfen, ob REST, SQL oder andere externe Ressourcen Ihr System verlangsamen.
* [Nachverfolgen von benutzerdefinierten Vorgängen mit dem Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md)


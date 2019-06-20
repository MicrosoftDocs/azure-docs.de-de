---
title: Nachverfolgen von benutzerdefinierten Vorgängen mit dem Azure Application Insights .NET SDK | Microsoft-Dokumentation
description: Nachverfolgen von benutzerdefinierten Vorgängen mit dem Azure Application Insights .NET-SDK
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/30/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: ae6e0e186f5cc0c9e3f0cd02d45d57c079eb3539
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60900888"
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Nachverfolgen von benutzerdefinierten Vorgängen mit dem Application Insights .NET SDK

Mit Azure Application Insights SDKs werden eingehende HTTP-Anforderungen und Aufrufe abhängiger Dienste wie HTTP-Anforderungen und SQL-Abfragen automatisch nachverfolgt. Dank der Nachverfolgung und Korrelation von Anforderungen und Abhängigkeiten erhalten Sie Einblicke in die Reaktionsfähigkeit und Zuverlässigkeit der gesamten Anwendung für alle Microservices, die diese Anwendung vereint. 

Es gibt eine Klasse von Anwendungsmustern, die nicht generisch unterstützt werden kann. Für die richtige Überwachung dieser Muster ist eine manuelle Codeinstrumentierung erforderlich. In diesem Artikel werden einige Muster behandelt, die möglicherweise manuelle Instrumentierung erfordern, darunter benutzerdefinierte Warteschlangenverarbeitung und das Ausführen von zeitintensiven Hintergrundaufgaben.

Dieser Artikel enthält eine Anleitung zum Nachverfolgen von benutzerdefinierten Vorgängen mit dem Application Insights SDK. Diese Dokumentation ist relevant für:

- Application Insights für .NET (auch als Basis SDK bezeichnet) ab Version 2.4.
- Application Insights für Webanwendungen (mit Ausführung von ASP.NET) ab Version 2.4.
- Application Insights ab ASP.NET Core Version 2.1.

## <a name="overview"></a>Übersicht
Ein Vorgang ist ein logisches Stück Arbeit, das von einer Anwendung ausgeführt wird. Er verfügt über Name, Startzeit, Dauer, Ergebnis und Ausführungskontext, z.B. Benutzername, Eigenschaften und Ergebnis. Wenn Vorgang A von Vorgang B initiiert wurde, dann ist Vorgang B ein übergeordneter Vorgang von A. Ein Vorgang kann nur über einen übergeordneten Vorgang verfügen, aber über mehrere untergeordnete Vorgänge. Weitere Informationen zu Vorgängen und zur Telemetriekorrelation finden Sie unter [Korrelation der Azure Application Insights-Telemetrie](correlation.md).

Im Application Insights .NET SDK wird ein Vorgang mit der abstrakten [OperationTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs)-Klasse und ihren Nachfolgerelementen [RequestTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) und [DependencyTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs) beschrieben.

## <a name="incoming-operations-tracking"></a>Nachverfolgen von eingehenden Vorgängen 
Das Application Insights-Web SDK sammelt automatisch HTTP-Anforderungen für ASP.NET-Anwendungen, die in einer IIS-Pipeline und allen ASP.NET Core-Anwendungen ausgeführt werden. Es sind auch Lösungen mit Community-Support für andere Plattformen und Frameworks vorhanden. Falls die Anwendung von keiner Standardlösung oder Lösung mit Community-Support unterstützt wird, können Sie sie manuell instrumentieren.

Ein weiteres Beispiel, für das die benutzerdefinierte Nachverfolgung erforderlich ist, ist der Worker, der Elemente aus der Warteschlange erhält. Für einige Warteschlangen wird der Aufruf zum Hinzufügen einer Nachricht zur Warteschlange als Abhängigkeit nachverfolgt. Der allgemeine Vorgang, der die Nachrichtenverarbeitung beschreibt, wird aber nicht automatisch erfasst.

Wir sehen uns nun an, wie Vorgänge dieser Art nachverfolgt werden können.

Auf allgemeiner Ebene besteht die Aufgabe darin, `RequestTelemetry` zu erstellen und bekannte Eigenschaften festzulegen. Nachdem der Vorgang abgeschlossen ist, wird die Telemetrie nachverfolgt. Das folgende Beispiel veranschaulicht diese Aufgabe.

### <a name="http-request-in-owin-self-hosted-app"></a>HTTP-Anforderung in selbstgehosteter Owin-App
In diesem Beispiel wird der Kontext der Ablaufverfolgung gemäß dem [HTTP-Protokoll für die Korrelation](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) verteilt. Sie sollten davon ausgehen, die hier beschriebenen Header zu erhalten.

```csharp
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    private readonly TelemetryClient telemetryClient = new TelemetryClient(TelemetryConfiguration.Active);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry.
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // If there is a Request-Id received from the upstream service, set the telemetry context accordingly.
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows correlation of 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry items.
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // Process the request.
        try
        {
            await Next.Invoke(context);
        }
        catch (Exception e)
        {
            requestTelemetry.Success = false;
            telemetryClient.TrackException(e);
            throw;
        }
        finally
        {
            // Update status code and success as appropriate.
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // Now it's time to stop the operation (and track telemetry).
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // Returns the root ID from the '|' to the first '.' if any.
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

Das HTTP-Protokoll für die Korrelation deklariert außerdem den `Correlation-Context`-Header. Dieser wird hier jedoch zur Vereinfachung weggelassen.

## <a name="queue-instrumentation"></a>Warteschlangeninstrumentierung
Es ist zwar ein [HTTP-Protokoll für die Korrelation](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) vorhanden, um Korrelationsdetails mit der HTTP-Anforderung zu übergeben, aber für jedes Warteschlangenprotokoll muss definiert werden, wie die gleichen Details für die Warteschlangennachricht übergeben werden. Einige Warteschlangenprotokolle (z.B. AMQP) ermöglichen das Übergeben von zusätzlichen Metadaten, und für einige andere (z.B. Azure Storage Queue) ist es erforderlich, dass der Kontext in der Nachrichtennutzlast codiert wird.

### <a name="service-bus-queue"></a>Service Bus-Warteschlange
Application Insights verfolgt Service Bus Messaging-Aufrufe mit dem neuen [Microsoft Azure ServiceBus-Client für .NET](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) (Version 3.0.0 und höher) nach.
Wenn Sie das [Muster „Meldungshandler“](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) zum Verarbeiten von Nachrichten verwenden, sind Sie bereits fertig: Alle Service Bus-Aufrufe, die von Ihrem Dienst durchgeführt werden, werden automatisch nachverfolgt und mit anderen Telemetrieelementen korreliert. Wenn Sie Nachrichten manuell verarbeiten, helfen Ihnen die Informationen unter [Service Bus client tracing with Microsoft Application Insights](../../service-bus-messaging/service-bus-end-to-end-tracing.md) (Service Bus-Clientablaufverfolgung mit Microsoft Application Insights) weiter.

Lesen Sie weiter, wenn Sie das [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) Paket verwenden. In den folgenden Beispielen wird gezeigt, wie Sie Aufrufe von Service Bus nachverfolgen (und korrelieren), da für die Service Bus-Warteschlange das AMQP-Protokoll verwendet wird und Warteschlangenvorgänge von Application Insights nicht automatisch nachverfolgt werden.
Korrelations-IDs werden in den Nachrichteneigenschaften übergeben.

#### <a name="enqueue"></a>Einreihen in die Warteschlange

```csharp
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes the telemetry item
    // and allows correlation of this operation with its parent and children.
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // Service Bus queue allows the property bag to pass along with the message.
    // We will use them to pass our correlation identifiers (and other context)
    // to the consumer.
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = false;
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

#### <a name="process"></a>Prozess
```csharp
public async Task Process(BrokeredMessage message)
{
    // After the message is taken from the queue, create RequestTelemetry to track its processing.
    // It might also make sense to get the name from the message.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
    requestTelemetry.Context.Operation.Id = rootId;
    requestTelemetry.Context.Operation.ParentId = parentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>Azure Storage-Warteschlange
Im folgenden Beispiel wird veranschaulicht, wie Sie Vorgänge der [Azure Storage-Warteschlange](../../storage/queues/storage-dotnet-how-to-use-queues.md) nachverfolgen und die Telemetrie zwischen Producer, Consumer und Azure Storage korrelieren. 

Die Storage-Warteschlange verfügt über eine HTTP-API. Alle Aufrufe der Warteschlange werden von der Application Insights-Abhängigkeitserfassung für HTTP-Anforderungen nachverfolgt.
Stellen Sie sicher, dass sich `Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer` in `applicationInsights.config` befindet. Fügen Sie es andernfalls programmgesteuert hinzu, wie unter [Filterung und Vorverarbeitung im Azure Application Insights SDK](../../azure-monitor/app/api-filtering-sampling.md) beschrieben.

Stellen Sie beim manuellen Konfigurieren von Application Insights sicher, dass Sie `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule` in etwa wie folgt erstellen und initialisieren:
 
```csharp
DependencyTrackingTelemetryModule module = new DependencyTrackingTelemetryModule();

// You can prevent correlation header injection to some domains by adding it to the excluded list.
// Make sure you add a Storage endpoint. Otherwise, you might experience request signature validation issues on the Storage service side.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
module.Initialize(TelemetryConfiguration.Active);

// Do not forget to dispose of the module during application shutdown.
```

Zudem sollten Sie die Vorgangs-ID von Application Insights mit der Anforderungs-ID von Storage korrelieren. Informationen zum Einrichten eines Storage-Anforderungsclients und Abrufen einer Serveranforderungs-ID erhalten Sie unter [Überwachung, Diagnose und Problembehandlung in Azure Storage](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### <a name="enqueue"></a>Einreihen in die Warteschlange
Da Storage-Warteschlangen die HTTP-API unterstützen, werden alle Vorgänge der Warteschlange von Application Insights automatisch nachverfolgt. In vielen Fällen sollte diese Instrumentierung ausreichend sein. Zum Korrelieren von Ablaufverfolgungen auf Consumerseite mit Ablaufverfolgungen für Producer müssen Sie aber auf ähnliche Weise Korrelationskontext übergeben, wie wir dies für das HTTP-Protokoll für die Korrelation getan haben. 

In diesem Beispiel wird gezeigt, wie Sie den `Enqueue`-Vorgang nachverfolgen. Ihre Möglichkeiten:

 - **Neuversuche korrelieren (falls vorhanden)** : Diese verfügen allesamt über ein gemeinsames übergeordnetes Element, den `Enqueue`-Vorgang. Andernfalls werden sie als untergeordnete Elemente der eingehenden Anforderung nachverfolgt. Wenn also mehrere logische Anforderungen für die Warteschlange vorhanden sind, lässt sich unter Umständen nur schwer ermitteln, welcher Aufruf zu den erneuten Versuchen geführt hat.
 - **Speicherprotokolle korrelieren (falls erforderlich)** : Sie sind mit der Telemetrie von Application Insights korreliert.

Der `Enqueue`-Vorgang ist einem übergeordneten Vorgang untergeordnet (zum Beispiel einer eingehenden HTTP-Anforderung). Der HTTP-Abhängigkeitsaufruf ist dem `Enqueue`-Vorgang untergeordnet und der eingehenden Anforderung auf zweiter Ebene untergeordnet:

```csharp
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload.
    // For example, if you choose to pass payload serialized to JSON, it might look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Storage logs and Application Insights telemetry.
    OperationContext context = new OperationContext { ClientRequestID = operation.Telemetry.Id};

    try
    {
        await queue.AddMessageAsync(queueMessage, null, null, new QueueRequestOptions(), context);
    }
    catch (StorageException e)
    {
        operation.Telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.Telemetry.Success = false;
        operation.Telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}  
```

Wenn Sie die Menge an Telemetriedaten, die von Ihrer Anwendung gemeldet werden, reduzieren oder den `Enqueue`-Vorgang aus anderen Gründen nachverfolgen möchten, können Sie die `Activity`-API direkt verwenden:

- Erstellen (und starten) Sie eine neue `Activity`, anstatt den Application Insights-Vorgang zu starten. Sie müssen ihr dabei *keine* Eigenschaften zuweisen außer den Namen des Vorgangs.
- Serialisieren Sie `yourActivity.Id` anstelle von `operation.Telemetry.Id` für die Nachrichtennutzlast. Sie können auch `Activity.Current.Id` verwenden.


#### <a name="dequeue"></a>Entfernen aus der Warteschlange
Ähnlich wie bei `Enqueue` werden die tatsächlichen HTTP-Anforderungen für die Storage-Warteschlange von Application Insights automatisch nachverfolgt. Der `Enqueue`-Vorgang wird normalerweise aber im Kontext des übergeordneten Elements durchgeführt, z.B. im Kontext einer eingehenden Anforderung. Application Insights SDKs korrelieren Vorgänge dieser Art (und den dazugehörigen HTTP-Teil) automatisch mit der übergeordneten Anforderung und anderen Telemetriedaten, die für denselben Bereich gemeldet werden.

Der `Dequeue`-Vorgang ist nicht ganz einfach. Das Application Insights SDK verfolgt HTTP-Anforderungen automatisch. Allerdings kennt es den Korrelationskontext erst, wenn die Nachricht analysiert wird. Es ist nicht möglich, die HTTP-Anforderung zu korrelieren, um die Nachricht mit den restlichen Telemetriedaten zu erhalten.

In vielen Fällen kann es nützlich sein, die HTTP-Anforderung zusammen mit anderen Ablaufverfolgungen mit der Warteschlange zu korrelieren. Dies wird im folgenden Beispiel veranschaulicht:

```csharp
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var telemetry = new DependencyTelemetry
    {
        Type = "Queue",
        Name = "Dequeue " + queue.Name
    };

    telemetry.Start();

    try
    {
        var message = await queue.GetMessageAsync();

        if (message != null)
        {
            var payload = JsonConvert.DeserializeObject<MessagePayload>(message.AsString);

            // If there is a message, we want to correlate the Dequeue operation with processing.
            // However, we will only know what correlation ID to use after we get it from the message,
            // so we will report telemetry after we know the IDs.
            telemetry.Context.Operation.Id = payload.RootId;
            telemetry.Context.Operation.ParentId = payload.ParentId;

            // Delete the message.
            return payload;
        }
    }
    catch (StorageException e)
    {
        telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        telemetry.Success = false;
        telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetry.Stop();
        telemetryClient.Track(telemetry);
    }

    return null;
}
```

#### <a name="process"></a>Prozess

Im folgenden Beispiel wird eine eingehende Nachricht auf ähnliche Weise wie eine eingehende HTTP-Anforderung nachverfolgt:

```csharp
public async Task Process(MessagePayload message)
{
    // After the message is dequeued from the queue, create RequestTelemetry to track its processing.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };
    // It might also make sense to get the name from the message.
    requestTelemetry.Context.Operation.Id = message.RootId;
    requestTelemetry.Context.Operation.ParentId = message.ParentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

Auch andere Warteschlangenvorgänge können so instrumentiert werden. Ein Peekvorgang sollte ähnlich wie ein Vorgang zur Entfernung aus der Warteschlange instrumentiert werden. Das Instrumentieren von Vorgängen der Warteschlangenverwaltung ist nicht erforderlich. Mit Application Insights werden Vorgänge wie HTTP-Vorgänge nachverfolgt, und in den meisten Fällen ist dies ausreichend.

Stellen Sie beim Instrumentieren der Nachrichtenlöschung sicher, dass Sie die Vorgangsbezeichner (für die Korrelation) festlegen. Alternativ können Sie auch die `Activity`-API verwenden. Das Festlegen von Vorgangsbezeichnern für die Telemetrieelemente ist dann nicht erforderlich, da das Application Insights SDK diese Aufgabe für Sie übernimmt:

- Erstellen Sie nach Erhalt eines Elements aus der Warteschlange eine neue `Activity`.
- Verwenden Sie `Activity.SetParentId(message.ParentId)` zum Korrelieren von Consumer- und Producerprotokollen.
- Starten Sie die `Activity`.
- Verfolgen Sie Vorgänge zum Entfernen aus der Warteschlange, Verarbeiten und Löschen nach, indem Sie `Start/StopOperation`-Hilfsprogramme verwenden. Führen Sie dies über die gleiche asynchrone Ablaufsteuerung (Ausführungskontext) durch. Auf diese Weise werden sie richtig korreliert.
- Beenden Sie die `Activity`.
- Verwenden Sie `Start/StopOperation`, oder rufen Sie `Track` der Telemetrie manuell auf.

### <a name="batch-processing"></a>Batchverarbeitung
Bei einigen Warteschlangen können Sie mehrere Nachrichten mit einer Anforderung aus der Warteschlange entfernen. Die Verarbeitung solcher Nachrichten ist vermutlich unabhängig und gehört zu den verschiedenen logischen Vorgängen. In diesem Fall ist es nicht möglich, den `Dequeue`-Vorgang mit einer bestimmten Nachrichtenverarbeitung zu korrelieren.

Jede Nachricht sollte in einer eigenen asynchronen Ablaufsteuerung verarbeitet werden. Weitere Informationen erhalten Sie im Abschnitt [Nachverfolgung von ausgehenden Abhängigkeiten](#outgoing-dependencies-tracking).

## <a name="long-running-background-tasks"></a>Hintergrundaufgaben mit langer Ausführungsdauer

Einige Anwendungen starten einen Vorgang mit langer Ausführungsdauer, der unter Umständen durch Benutzeranforderungen verursacht wird. Aus Sicht der Nachverfolgung bzw. Instrumentierung unterscheidet sich dies nicht von der Instrumentierung von Anforderungen oder Abhängigkeiten: 

```csharp
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // Process the task.
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // Update status code and success as appropriate.
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Update status code and success as appropriate.
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

In diesem Beispiel wird mit `telemetryClient.StartOperation` das Element `DependencyTelemetry` erstellt und der Korrelationskontext eingefügt. Angenommen, Sie verfügen über einen übergeordneten Vorgang, der von eingehenden Anforderungen für die Planung des Vorgangs erstellt wurde. Sofern `BackgroundTask` in derselben asynchronen Ablaufsteuerung wie eine eingehende Anforderung gestartet wird, wird die Korrelation mit diesem übergeordneten Vorgang durchgeführt. `BackgroundTask` und alle geschachtelten Telemetrieelemente werden automatisch mit der Anforderung korreliert, die der Auslöser war. Dies gilt auch nach Abschluss der Anforderung.

Wenn der Task über den Hintergrundthread gestartet wird, dem kein Vorgang (`Activity`) zugeordnet ist, weist `BackgroundTask` kein übergeordnetes Element auf. Geschachtelte Vorgänge können jedoch vorhanden sein. Alle Telemetrieelemente, die von dem Task gemeldet werden, werden mit dem in `BackgroundTask` erstellten `DependencyTelemetry`-Element korreliert.

## <a name="outgoing-dependencies-tracking"></a>Nachverfolgung von ausgehenden Abhängigkeiten
Sie können Ihre eigene Art von Abhängigkeit oder einen Vorgang nachverfolgen, der nicht von Application Insights unterstützt wird.

Die `Enqueue`-Methode in der Service Bus-Warteschlange oder Storage-Warteschlange kann als Beispiel für diese benutzerdefinierte Nachverfolgung dienen.

Der allgemeine Ansatz für die benutzerdefinierte Nachverfolgung von Abhängigkeiten lautet wie folgt:

- Rufen Sie die `TelemetryClient.StartOperation`-Methode (Erweiterungsmethode) auf, mit der die für die Korrelation erforderlichen `DependencyTelemetry`-Eigenschaften und einige andere Eigenschaften (Startzeitstempel, Dauer) aufgefüllt werden.
- Legen Sie weitere benutzerdefinierte Eigenschaften wie den Namen und anderen benötigten Kontext für `DependencyTelemetry` fest.
- Rufen Sie eine Abhängigkeit auf und warten Sie darauf.
- Beenden Sie den Vorgang mit `StopOperation`, wenn er abgeschlossen wurde.
- Behandeln Sie Ausnahmen.

```csharp
public async Task RunMyTaskAsync()
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1"))
    {
        try 
        {
            var myTask = await StartMyTaskAsync();
            // Update status code and success as appropriate.
        }
        catch(...) 
        {
            // Update status code and success as appropriate.
        }
    }
}
```

Beim Löschvorgang wird der Vorgang beendet, sodass Sie hierfür stattdessen auch `StopOperation` aufrufen können.

*Warnung*: In einigen Fällen kann durch eine unbehandelte Ausnahme [verhindert ](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-finally) werden, dass `finally` aufgerufen wird, und die Vorgänge werden dann ggf. nicht nachverfolgt.

### <a name="parallel-operations-processing-and-tracking"></a>Verarbeitung und Nachverfolgung von parallelen Vorgängen

`StopOperation` beendet nur den Vorgang, der gestartet wurde. Wenn der aktuell ausgeführte Vorgang nicht mit dem zu stoppenden übereinstimmt, wird von `StopOperation` keine Aktion ausgeführt. Dies kann der Fall sein, wenn Sie mehrere Vorgänge parallel in demselben Ausführungskontext starten:

```csharp
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// FAILURE!!! This will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active.
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

Stellen Sie sicher, dass Sie immer `StartOperation` aufrufen und den Vorgang in derselben **async**-Methode verarbeiten, um parallel ausgeführte Vorgänge zu isolieren. Wenn der Vorgang synchron (bzw. nicht asynchron) ist, können Sie den Vorgang per Wrapper umschließen und mit `Task.Run` nachverfolgen:

```csharp
public void RunMyTask(string name)
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>(name))
    {
        Process();
        // Update status code and success as appropriate.
    }
}

public async Task RunAllTasks()
{
    var task1 = Task.Run(() => RunMyTask("task 1"));
    var task2 = Task.Run(() => RunMyTask("task 2"));
    
    await Task.WhenAll(task1, task2);
}
```

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die Grundlagen der [Telemetriekorrelation](correlation.md) in Application Insights.
- Lesen Sie die Informationen zu den Application Insights-Typen und zum Datenmodell unter [Datenmodell](../../azure-monitor/app/data-model.md).
- Informieren Sie sich über das Melden von benutzerdefinierten [Ereignissen und Metriken](../../azure-monitor/app/api-custom-events-metrics.md) an Application Insights.
- Informationen zur [Standardkonfiguration](configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) der Sammlung von Kontexteigenschaften.
- Lesen Sie den [System.Diagnostics.Activity User Guide](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) (System.Diagnostics.Activity-Benutzerhandbuch), um zu erfahren, wie wir Telemetriedaten korrelieren.

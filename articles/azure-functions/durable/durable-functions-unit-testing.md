---
title: Azure Durable Functions-Unittest
description: Erfahren Sie, wie Sie den Durable Functions-Unittest durchführen.
services: functions
author: kadimitr
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: kadimitr
ms.openlocfilehash: 69cf91f1448e36353f83de7a271abb3b53858bb0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60648464"
---
# <a name="durable-functions-unit-testing"></a>Durable Functions-Unittest

Der Unittest ist ein wichtiger Bestandteil moderner Softwareentwicklungsverfahren. Mit Unittests wird das Verhalten der Geschäftslogik überprüft und verhindert, dass in der Zukunft unbemerkte gravierende Änderungen eingeführt werden. Die Komplexität von Durable Functions kann problemlos wachsen, sodass die Einführung von Unittests dazu beiträgt, gravierende Änderungen zu vermeiden. In den folgenden Abschnitten wird erläutert, wie Unittests der drei Funktionstypen durchgeführt werden – Orchestrierungsclient-, Orchestrator- und Aktivitätsfunktionen.

## <a name="prerequisites"></a>Voraussetzungen

Die Beispiele in diesem Artikel setzen Kenntnisse der folgenden Konzepte und Frameworks voraus:

* Komponententests

* Langlebige Funktionen

* [xUnit](https://xunit.github.io/) – Testframework

* [moq](https://github.com/moq/moq4) – Simulationsframework

## <a name="base-classes-for-mocking"></a>Basisklassen für Simulation

Simulation wird in Durable Functions über drei abstrakte Klassen unterstützt:

* [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html)

* [DurableOrchestrationContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContextBase.html)

* [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html)

Diese Klassen sind die Basisklassen für [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html), [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) und [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html), die Orchestrierungsclient-, Orchestrator- und Aktivitätsmethoden definieren. Die Simulationen legen erwartetes Verhalten für Basisklassenmethoden fest, sodass der Unittest die Geschäftslogik überprüfen kann. Der Unittest der Geschäftslogik wird als Workflow in zwei Schritten im Orchestrierungsclient und Orchestrator durchgeführt:

1. Verwenden Sie beim Definieren der Signaturen von Orchestrierungsclient und Orchestrator die Basisklassen anstelle der konkreten Implementierung.
2. Simulieren Sie in den Unittests das Verhalten der Basisklassen, und überprüfen Sie die Geschäftslogik.

In den folgenden Abschnitten finden Sie weitere Informationen zu Testfunktionen, die die Orchestrierungsclientbindung und Orchestratorbindung verwenden.

## <a name="unit-testing-trigger-functions"></a>Unittest-Triggerfunktionen

In diesem Abschnitt überprüft der Unittest die Logik der folgenden HTTP-Triggerfunktion zum Starten neuer Orchestrierungen.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

Die Unittestaufgabe überprüft den Wert des in der Antwortnutzlast bereitgestellten `Retry-After`-Headers. Dazu simuliert der Unittest einige der [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html)-Methoden, um vorhersagbares Verhalten sicherzustellen.

Zunächst ist eine Simulation der Basisklasse erforderlich, [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). Die Simulation kann eine neue Klasse sein, die [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) implementiert. Die Verwendung eines Simulationsframeworks wie [moq](https://github.com/moq/moq4) vereinfacht jedoch den Prozess:

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Dann wird die `StartNewAsync`-Methode simuliert, um eine gut bekannte Instanz-ID zurückzugeben.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

Als Nächstes wird `CreateCheckStatusResponse` simuliert, um immer eine leere HTTP 200-Antwort zurückzugeben.

```csharp
    // Mock CreateCheckStatusResponse method
    durableOrchestrationClientBaseMock
        .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId))
        .Returns(new HttpResponseMessage
        {
            StatusCode = HttpStatusCode.OK,
            Content = new StringContent(string.Empty),
            Headers =
            {
                RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
            }
        });
```

`TraceWriter` wird auch simuliert:

```csharp
    // Mock TraceWriter
    var traceWriterMock = new Mock<TraceWriter>(TraceLevel.Info);

```  

Jetzt wird die `Run`-Methode vom Unittest aufgerufen:

```csharp
    // Call Orchestration trigger function
    var result = await HttpStart.Run(
        new HttpRequestMessage()
        {
            Content = new StringContent("{}", Encoding.UTF8, "application/json"),
            RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
        },
        durableOrchestrationClientBaseMock.Object,
        functionName,
        traceWriterMock.Object);
 ```

 Der letzte Schritt ist der Vergleich der Ausgabe mit dem erwarteten Wert:

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Nach der Kombination aller Schritte hat der Unittest folgenden Code:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Unittest-Orchestratorfunktionen

Orchestratorfunktionen sind für Unittests noch interessanter, da sie in der Regel viel mehr Geschäftslogik aufweisen.

In diesem Abschnitt überprüfen die Unittests die Ausgabe der `E1_HelloSequence`-Orchestratorfunktion:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Der Unittestcode beginnt mit dem Erstellen einer Simulation:

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

Dann werden die Aktivitätsmethodenaufrufe simuliert:

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Als Nächstes ruft der Unittest die `HelloSequence.Run`-Methode auf:

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

Schließlich wird die Ausgabe überprüft:

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

Nach der Kombination aller Schritte hat der Unittest folgenden Code:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Unittest-Aktivitätsfunktionen

Der Unittest von Aktivitätsfunktionen kann in gleicher Weise wie der nicht dauerhafter Funktionen durchgeführt werden.

In diesem Abschnitt überprüft der Unittest das Verhalten der `E1_SayHello`-Aktivitätsfunktion:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Die Komponententests überprüfen auch das Format der Ausgabe. Die Komponententests können die Parametertypen direkt oder über die Pseudoklasse [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html) verwenden:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen zu xUnit](https://xunit.github.io/docs/getting-started-dotnet-core)
> 
> [Weitere Informationen zu moq](https://github.com/Moq/moq4/wiki/Quickstart)

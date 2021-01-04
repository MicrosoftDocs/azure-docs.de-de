---
title: Bewährte Methoden zur Verbesserung der Leistung mit Azure Service Bus
description: Beschreibt, wie Service Bus verwendet wird, um die Leistung beim Austausch von Brokernachrichten zu optimieren.
ms.topic: article
ms.date: 11/11/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 12de7edd5dec42b01c46307febbef7d739d0495d
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95811666"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Bewährte Methoden für Leistungsoptimierungen mithilfe von Service Bus Messaging

In diesem Artikel erfahren Sie, wie Sie mithilfe von Azure Service Bus die Leistung beim Austausch von im Broker gespeicherten Nachrichten optimieren. Im ersten Teil dieses Artikels werden die verschiedenen Mechanismen zur Leistungssteigerung beschrieben. Der zweite Teil bietet eine Anleitung zur Verwendung von Service Bus auf eine Weise, die die beste Leistung in einem bestimmten Szenario ermöglichen kann.

Im Rahmen dieses Artikels bezieht sich der Begriff „Client“ auf eine Entität, die auf Service Bus zugreift. Ein Client kann die Rolle eines Absenders oder eines Empfängers annehmen. Der Begriff „Absender“ wird für einen Service Bus-Warteschlangenclient oder für einen Service Bus-Themaclient verwendet, der Nachrichten an eine Service Bus-Warteschlange oder an ein Service Bus-Thema sendet. Der Begriff „Empfänger“ bezieht sich auf einen Service Bus-Warteschlangenclient oder einen auf einen Service Bus-Abonnementclient, der Nachrichten von einer Service Bus-Warteschlange oder einem Service Bus-Abonnement empfängt.

## <a name="protocols"></a>Protokolle
Service Bus ermöglicht Clients das Senden und Empfangen von Nachrichten über eins von drei Protokollen:

1. Advanced Message Queuing Protocol (AMQP)
2. Service Bus Messaging Protocol (SBMP)
3. Hypertext Transfer-Protokoll (HTTP)

AMQP ist am effizientesten, weil hierbei die Verbindung mit Service Bus aufrechterhalten wird. Außerdem implementiert es die Batchverarbeitung und Vorabrufe. Sofern nicht anders angegeben, wird für alle Inhalte in diesem Artikel AMQP oder SBMP verwendet.

> [!IMPORTANT]
> SBMP ist nur für .NET Framework verfügbar. AMQP ist die Standardeinstellung für .NET Standard.

## <a name="choosing-the-appropriate-service-bus-net-sdk"></a>Auswählen des geeigneten Service Bus .NET SDK
Es gibt zwei unterstützte Azure Service Bus .NET SDKs. Da sich die zugehörigen APIs ähneln, kann es ggf. zu Unsicherheit bei der Auswahl kommen. Die folgende Tabelle enthält hierzu hilfreiche Informationen. Wir empfehlen Ihnen die Verwendung des Microsoft.Azure.ServiceBus SDK, da es moderner, leistungsfähiger und plattformübergreifend kompatibel ist. Darüber hinaus wird AMQP über WebSockets unterstützt, und es ist Teil der Azure .NET SDK-Sammlung mit Open-Source-Projekten.

| NuGet-Paket | Primäre Namespaces | Mindestplattform | Protokolle |
|---------------|----------------------|---------------------|-------------|
| <a href="https://www.nuget.org/packages/Microsoft.Azure.ServiceBus" target="_blank">Microsoft.Azure.ServiceBus <span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.Azure.ServiceBus`<br>`Microsoft.Azure.ServiceBus.Management` | .NET Core 2.0<br>.NET Framework 4.6.1<br>Mono 5.4<br>Xamarin.iOS 10.14<br>Xamarin.Mac 3.8<br>Xamarin.Android 8.0<br>Universelle Windows-Plattform 10.0.16299 | AMQP<br>HTTP |
| <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus" target="_blank">WindowsAzure.ServiceBus <span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.ServiceBus`<br>`Microsoft.ServiceBus.Messaging` | .NET Framework 4.6.1 | AMQP<br>SBMP<br>HTTP |

Weitere Informationen zu den Mindestplattformen für die Unterstützung von .NET Standard finden Sie unter [Unterstützung der .NET-Implementierung](/dotnet/standard/net-standard#net-implementation-support).

## <a name="reusing-factories-and-clients"></a>Wiederverwenden von Factorys und Clients

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Service Bus-Clientobjekte, z. B. Implementierungen von [`IQueueClient`][QueueClient] oder [`IMessageSender`][MessageSender], sollten für die Abhängigkeitsinjektion als Singletons registriert werden (oder einmal instanziiert und dann freigegeben werden). Es empfiehlt sich, Messagingfactorys oder Warteschlangen-, Themen- oder Abonnementclients nicht zu schließen, nachdem Sie eine Nachricht gesendet haben, und dann erneut zu erstellen, wenn Sie die nächste Nachricht senden. Durch das Schließen einer Messagingfactory wird die Verbindung mit dem Service Bus-Dienst gelöscht. Beim erneuten Erstellen der Factory wird eine neue Verbindung hergestellt. Das Herstellen einer Verbindung ist ein aufwendiger Vorgang, den Sie vermeiden können, indem Sie eine Factory und die Clientobjekte für mehrere Vorgänge verwenden. Sie können diese Clientobjekte sicher für gleichzeitige asynchrone Vorgänge und von mehreren Threads verwenden.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Service Bus-Clientobjekte, z. B. `QueueClient` oder `MessageSender`, werden über ein [MessagingFactory][MessagingFactory]-Objekt erstellt, das die interne Verwaltung von Verbindungen ermöglicht. Es empfiehlt sich, Messagingfactorys oder Warteschlangen-, Themen- oder Abonnementclients nicht zu schließen, nachdem Sie eine Nachricht gesendet haben, und dann erneut zu erstellen, wenn Sie die nächste Nachricht senden. Durch Schließen einer Messagingfactory wird die Verbindung mit dem Service Bus-Dienst gelöscht, und eine neue Verbindung wird beim erneuten Erstellen der Factory hergestellt. Das Herstellen einer Verbindung ist ein aufwendiger Vorgang, den Sie vermeiden können, indem Sie eine Factory und die Clientobjekte für mehrere Vorgänge verwenden. Sie können diese Clientobjekte sicher für gleichzeitige asynchrone Vorgänge und von mehreren Threads verwenden.

---

## <a name="concurrent-operations"></a>Parallele Vorgänge
Vorgänge wie Senden, Empfangen, Löschen usw. nehmen einige Zeit in Anspruch. Dieser Zeitraum umfasst die Zeit, die der Service-Bus-Dienst für die Bearbeitung des Vorgangs benötigt, sowie die Latenzzeit der Anforderung und der Antwort. Die Vorgänge müssen parallel ausgeführt werden, um die Anzahl von Vorgängen pro Zeitraum zu erhöhen.

Der Client plant gleichzeitige Vorgänge durch Ausführen von **asynchronen** Vorgängen. Die nächste Anforderung wird gestartet, bevor die vorherige Anforderung abgeschlossen ist. Im folgenden Codeausschnitt sehen Sie ein Beispiel für einen asynchronen Sendevorgang:

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

```csharp
var messageOne = new Message(body);
var messageTwo = new Message(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

```csharp
var messageOne = new BrokeredMessage(body);
var messageTwo = new BrokeredMessage(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

---

Im folgenden Code sehen Sie ein Beispiel für einen asynchronen Empfangsvorgang.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Vollständige <a href="https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues" target="_blank">Quellcodebeispiele<span class="docon docon-navigate-external x-hidden-focus"></span></a> finden Sie im GitHub-Repository:

```csharp
var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);

static Task LogErrorAsync(Exception exception)
{
    Console.WriteLine(exception);
    return Task.CompletedTask;
};

receiver.RegisterMessageHandler(
    async (message, cancellationToken) =>
    {
        Console.WriteLine("Handle message");
        await receiver.CompleteAsync(message.SystemProperties.LockToken);
    },
    new MessageHandlerOptions(e => LogErrorAsync(e.Exception))
    {
        AutoComplete = false,
        MaxConcurrentCalls = 20
    });
```

Das `MessageReceiver`-Objekt wird mit der Verbindungszeichenfolge, dem Warteschlangennamen und einem Peek-Lock-Empfangsmodus instanziiert. Als Nächstes wird die `receiver`-Instanz verwendet, um den Meldungshandler zu registrieren.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Vollständige <a href="https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/SendersReceiversWithQueues" target="_blank">Quellcodebeispiele<span class="docon docon-navigate-external x-hidden-focus"></span></a> finden Sie im GitHub-Repository:

```csharp
var factory = MessagingFactory.CreateFromConnectionString(connectionString);
var receiver = await factory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);

// Register the handler to receive messages asynchronously
receiver.OnMessageAsync(
    async message =>
    {
        Console.WriteLine("Handle message");
        await message.CompleteAsync();
    },
    new OnMessageOptions
    {
        AutoComplete = false,
        MaxConcurrentCalls = 20
    });
```

Von `MessagingFactory` wird aus der Verbindungszeichenfolge ein `factory`-Objekt erstellt. Mit der `factory`-Instanz wird ein `MessageReceiver` instanziiert. Als Nächstes wird die `receiver`-Instanz verwendet, um den OnMessage-Handler zu registrieren.

---

## <a name="receive-mode"></a>Empfangsmodus

Beim Erstellen eines Warteschlangen- oder Abonnementclients können Sie einen Empfangsmodus angeben: *PeekLock* oder *ReceiveAndDelete*. Der Standardempfangsmodus ist `PeekLock`. Beim Betrieb im Standardmodus sendet der Client eine Anforderung zum Empfangen einer Nachricht von Service Bus. Nachdem der Client die Nachricht empfangen hat, sendet er eine Anforderung zum Abschließen der Nachricht.

Wenn der Empfangsmodus auf `ReceiveAndDelete` festgelegt wird, werden beide Schritte in einer Anforderung kombiniert. Dieser Schritt reduziert die Anzahl der Vorgänge und kann den Gesamtnachrichtendurchsatz verbessern. Diese Leistungssteigerung kann zu Nachrichtenverlusten führen.

Service Bus unterstützt keine Transaktionen für ReceiveAndDelete-Vorgänge. Außerdem ist PeekLock-Semantik für alle Szenarien erforderlich, in denen der Client eine Nachricht zurückstellen oder [in eine Warteschlange für unzustellbare Nachrichten](service-bus-dead-letter-queues.md) verschieben möchte.

## <a name="client-side-batching"></a>Clientseitige Batchverarbeitung

Durch die clientseitige Batchverarbeitung kann ein Warteschlangen- oder Themenclient das Senden einer Nachricht für einen bestimmten Zeitraum verzögern. Wenn der Client während dieses Zeitraums weitere Nachrichten sendet, werden die Nachrichten in einem einzigen Batch übertragen. Die clientseitige Batchverarbeitung bewirkt auch, dass ein Warteschlangen- oder Abonnementclient mehrere Anforderungen zum **Abschließen** als Batch in eine einzelne Anforderung aufnehmen kann. Die Batchverarbeitung ist nur für asynchrone Vorgänge zum **Senden** und **Abschließen** verfügbar. Synchrone Vorgänge werden sofort an den Service Bus-Dienst gesendet. Die Batchverarbeitung erfolgt nicht für Vorschau- oder Empfangsvorgänge und auch nicht clientübergreifend.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Bei der Batchverarbeitung für das .NET Standard SDK wird keine Eigenschaft für die Bearbeitung verfügbar gemacht.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Ein Client verwendet standardmäßig ein Batchintervall von 20 ms. Sie können das Batchintervall ändern, indem Sie die Eigenschaft [BatchFlushInterval][BatchFlushInterval] vor dem Erstellen der Messagingfactory festlegen. Diese Einstellung wirkt sich auf alle Clients aus, die von dieser Factory erstellt werden.

Legen Sie zum Deaktivieren der Batchverarbeitung die Eigenschaft [BatchFlushInterval][BatchFlushInterval] auf **TimeSpan.Zero** fest. Beispiel:

```csharp
var settings = new MessagingFactorySettings
{
    NetMessagingTransportSettings =
    {
        BatchFlushInterval = TimeSpan.Zero
    }
};
var factory = MessagingFactory.Create(namespaceUri, settings);
```

Die Batchverarbeitung wirkt sich nicht auf die Anzahl der abrechenbaren Messagingvorgänge aus und ist nur für das Service Bus-Clientprotokoll unter Verwendung der [Microsoft.ServiceBus.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)-Bibliothek verfügbar. Das HTTP-Protokoll unterstützt keine Batchverarbeitung.

> [!NOTE]
> Durch das Festlegen von `BatchFlushInterval` wird sichergestellt, dass die Batchverarbeitung aus Perspektive der Anwendung implizit erfolgt. Dies bedeutet, dass die Anwendung keine spezifischen Batchaufrufe durchführt, sondern `SendAsync` und `CompleteAsync`.
>
> Die explizite clientseitige Batchverarbeitung kann mithilfe des folgenden Methodenaufrufs implementiert werden:
> ```csharp
> Task SendBatchAsync(IEnumerable<BrokeredMessage> messages);
> ```
> Hier muss die kombinierte Größe der Nachrichten kleiner sein als die maximale Größe, die vom Tarif unterstützt wird.

---

## <a name="batching-store-access"></a>Batchverarbeitung des Speicherzugriffs

Um den Durchsatz einer Warteschlange, eines Themas oder eines Abonnements zu erhöhen, verarbeitet Service Bus beim Schreiben in den internen Speicher mehrere Nachrichten als Batch. 

- Wenn Sie Batchverarbeitung für eine Warteschlange aktivieren, wird das Schreiben von Nachrichten in den Speicher und das Löschen von Nachrichten aus dem Speicher als Batch verarbeitet. 
- Wenn Sie Batchverarbeitung für ein Thema aktivieren, wird das Schreiben von Nachrichten in den Speicher als Batch verarbeitet. 
- Wenn Sie Batchverarbeitung für ein Abonnement aktivieren, wird das Löschen von Nachrichten aus dem Speicher als Batch verarbeitet. 
- Wenn für eine Entität Speicherzugriff als Batch aktiviert ist, verzögert Service Bus einen Schreibvorgang in den Speicher für diese Entität für bis zu 20 ms.

> [!NOTE]
> Es besteht keine Gefahr, dass bei der Batchverarbeitung Nachrichten verloren gehen, auch wenn am Ende eines Batchintervalls von 20 ms ein Service Bus-Fehler auftritt.

Weitere Speichervorgänge, die während dieses Intervalls auftreten, werden dem Batch hinzugefügt. Speicherzugriff als Batch wirkt sich nur auf Vorgänge zum **Senden** und **Abschließen** aus, Empfangsvorgänge sind nicht betroffen. Speicherzugriff als Batch ist eine Eigenschaft einer Entität. Die Batchverarbeitung erfolgt für alle Entitäten, die Speicherzugriff als Batch ermöglichen.

Beim Erstellen neuer Warteschlangen, Themen oder Abonnements ist der Speicherzugriff als Batch standardmäßig aktiviert.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Zum Deaktivieren des Speicherzugriffs als Batch benötigen Sie eine `ManagementClient`-Instanz. Erstellen Sie eine Warteschlange aus einer Warteschlangenbeschreibung, wobei die `EnableBatchedOperations`-Eigenschaft auf `false` festgelegt wird.

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = await managementClient.CreateQueueAsync(queueDescription);
```

Weitere Informationen finden Sie in den folgenden Artikeln:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Zum Deaktivieren des Speicherzugriffs als Batch benötigen Sie eine `NamespaceManager`-Instanz. Erstellen Sie eine Warteschlange aus einer Warteschlangenbeschreibung, wobei die `EnableBatchedOperations`-Eigenschaft auf `false` festgelegt wird.

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = namespaceManager.CreateQueue(queueDescription);
```

Weitere Informationen finden Sie in den folgenden Artikeln:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

Batchspeicherzugriff wirkt sich nicht auf die Anzahl der abrechenbaren Messagingvorgänge aus. Dabei handelt es sich um eine Eigenschaft einer Warteschlange, eines Themas oder eines Abonnements. Sie ist unabhängig vom Empfangsmodus und dem Protokoll, das zwischen einem Client und dem Service Bus-Dienst verwendet wird.

## <a name="prefetching"></a>Vorabrufe

[Vorabrufe](service-bus-prefetch.md) ermöglichen es dem Warteschlangen- oder Abonnementclient, weitere Nachrichten aus dem Dienst zu laden, wenn Nachrichten empfangen werden. Der Client speichert diese Nachrichten in einem lokalen Cache. Die Größe des Caches wird durch die Eigenschaft `QueueClient.PrefetchCount` bzw. `SubscriptionClient.PrefetchCount` bestimmt. Jeder Client, der Vorabrufe ermöglicht, verwaltet seinen eigenen Cache. Ein Cache wird nicht für andere Clients freigegeben. Wenn der Client einen Empfangsvorgang startet und sein Cache leer ist, überträgt der Dienst ein Nachrichtenbatch. Die Größe des Batches entspricht der Größe des Caches oder 256 KB, je nachdem, welcher Wert kleiner ist. Wenn der Client einen Empfangsvorgang startet und der Cache eine Nachricht enthält, wird die Nachricht aus dem Cache abgerufen.

Wenn eine Nachricht vorab abgerufen wird, sperrt der Dienst die vorab abgerufene Nachricht. Durch die Sperre kann die vorabgerufene Nachricht nicht von einem anderen Empfänger empfangen werden. Wenn der Empfänger die Nachricht nicht abschließen kann, bevor die Sperre abläuft, wird die Nachricht für andere Empfänger verfügbar gemacht. Die vorab abgerufene Kopie der Nachricht verbleibt im Cache. Der Empfänger, der die abgelaufene Kopie aus dem Cache verarbeitet, empfängt eine Ausnahme, wenn er versucht, diese Nachricht abzuschließen. Standardmäßig läuft die Nachrichtensperrre nach 60 Sekunden ab. Dieser Wert kann auf 5 Minuten erhöht werden. Um die Nutzung abgelaufener Nachrichten zu verhindern, legen Sie die Cachegröße kleiner als die Anzahl der Nachrichten fest, die ein Client innerhalb des Sperrtimeoutintervalls nutzen kann.

Wenn der Standardsperrenablauf von 60 Sekunden verwendet wird, gilt als geeigneter Wert für `PrefetchCount` das 20-fache der maximalen Verarbeitungsraten aller Empfänger der Factory. Beispiel: Eine Factory erstellt 10 Empfänger, und jeder Empfänger kann bis zu 10 Nachrichten pro Sekunde verarbeiten. Der Wert für den Vorabruf sollte 20 × 3 × 10 = 600 nicht überschreiten. Standardmäßig ist `PrefetchCount` auf „0“ festgelegt. Dies bedeutet, dass keine weiteren Nachrichten aus dem Dienst abgerufen werden.

Der Vorabruf von Nachrichten vergrößert den Gesamtdurchsatz für eine Warteschlange oder ein Abonnement, da sich dadurch die Gesamtzahl von Nachrichtenvorgängen bzw. Roundtrips verringert. Das Abrufen der ersten Nachricht dauert jedoch länger (aufgrund der gestiegenen Nachrichtengröße). Das Empfangen vorab abgerufener Nachrichten aus dem Cache ist schneller, weil diese Nachrichten bereits vom Client heruntergeladen wurden.

Die Eigenschaft für die Gültigkeitsdauer (Time-to-Live, TTL) einer Nachricht wird vom Server zu dem Zeitpunkt überprüft, wenn der Server die Nachricht an den Client sendet. Der Client überprüft die TTL-Eigenschaft der Nachricht nicht, wenn die Nachricht empfangen wird. Stattdessen kann die Nachricht auch dann empfangen werden, wenn die Gültigkeitsdauer der Nachricht abgelaufen ist, während sich die Nachricht im Cache des Clients befunden hat.

Der Vorabruf wirkt sich nicht auf die Anzahl der abrechenbaren Messagingvorgänge aus und ist nur für das Service Bus-Clientprotokoll verfügbar. Das HTTP-Protokoll unterstützt keinen Vorabruf. Vorabrufe sind für synchrone und asynchrone Empfangsvorgänge verfügbar.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Weitere Informationen finden Sie unter den folgenden `PrefetchCount`-Eigenschaften:

* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Weitere Informationen finden Sie unter den folgenden `PrefetchCount`-Eigenschaften:

* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

## <a name="prefetching-and-receivebatch"></a>Vorabruf und „ReceiveBatch“

> [!NOTE]
> Dieser Abschnitt gilt nur für das WindowsAzure.ServiceBus SDK, da vom Microsoft.Azure.ServiceBus SDK keine Batchfunktionen verfügbar gemacht werden.

Die beim gemeinsamen Vorabrufen mehrerer Nachrichten angewendeten Konzepte ähneln hinsichtlich der Semantik der Nachrichtenverarbeitung in einem Batch (`ReceiveBatch`). Es gibt aber einige geringfügige Unterschiede, die Sie beachten müssen, wenn Sie diese beiden Optionen gemeinsam verwenden.

Der Vorabruf ist eine Konfiguration (bzw. ein Modus) auf dem Client (`QueueClient` und `SubscriptionClient`), und `ReceiveBatch` ist ein Vorgang (mit Anforderung/Antwort-Semantik).

Bedenken Sie bei der gemeinsamen Verwendung dieser beiden Ansätze Folgendes:

* Die Vorabrufanzahl muss größer oder gleich der Anzahl von Nachrichten sein, die Sie voraussichtlich mit `ReceiveBatch` empfangen.
* Die Vorabrufanzahl kann bis zu n-/dreimal so groß sein wie die Anzahl von verarbeiteten Nachrichten pro Sekunde, wobei „n“ die Standardsperrdauer ist.

Ein „gieriger“ Ansatz (also das Aufrechterhalten einer sehr hohen Vorabrufanzahl) bringt einige Probleme mit sich, da er voraussetzt, dass die Nachricht auf einen bestimmten Empfänger beschränkt ist. Es wird empfohlen, Vorabrufwerte zwischen den oben genannten Schwellenwerte auszuprobieren und empirisch zu ermitteln, welcher Wert geeignet ist.

## <a name="multiple-queues"></a>Mehrere Warteschlangen

Wenn eine einzelne Warteschlange oder ein Thema die erwartete Menge nicht verarbeiten kann, verwenden Sie mehrere Messagingentitäten. Wenn Sie mehrere Entitäten verwenden, erstellen Sie einen dedizierten Client für jede Entität, statt den gleichen Client für alle Entitäten zu nutzen.

## <a name="development-and-testing-features"></a>Entwicklungs- und Testfunktionen

> [!NOTE]
> Dieser Abschnitt gilt nur für das WindowsAzure.ServiceBus SDK, da diese Funktionalität vom Microsoft.Azure.ServiceBus SDK nicht verfügbar gemacht wird.

Service Bus verfügt über ein Feature, das speziell für die Entwicklung verwendet wird und **nie in Produktionskonfigurationen eingesetzt werden sollte**[`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering].

Wenn dem Thema neue Regeln oder Filter hinzugefügt werden, können Sie mit [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] überprüfen, ob der neue Filterausdruck wie erwartet funktioniert.

## <a name="scenarios"></a>Szenarien

In den folgenden Abschnitten werden normale Messagingszenarien und die bevorzugten Service Bus-Einstellungen beschrieben. Durchsatzraten werden als klein (weniger als 1 Nachricht/Sekunde), mittel (1 Nachricht/Sekunde oder mehr, aber weniger als 100 Nachrichten/Sekunde) und hoch (100 Nachrichten/Sekunde oder mehr) klassifiziert. Die Anzahl der Clients wird als klein (5 oder weniger), mittel (mehr als 5, aber weniger als oder gleich 20) und groß (mehr als 20) klassifiziert.

### <a name="high-throughput-queue"></a>Warteschlange mit hohem Durchsatz

Zielsetzung: Maximieren des Durchsatzes einer einzelnen Warteschlange. Die Anzahl der Absender und Empfänger ist klein.

* Verwenden Sie zum Erhöhen der Gesamtsenderate an die Warteschlange mehrere Messagingfactorys, um Absender zu erstellen. Verwenden Sie für jeden Absender asynchrone Vorgänge oder mehrere Threads.
* Verwenden Sie zum Erhöhen der Gesamtempfangsrate von der Warteschlange mehrere Messagingfactorys, um Empfänger zu erstellen.
* Verwenden Sie asynchrone Vorgänge, um die clientseitige Batchverarbeitung zu nutzen.
* Legen Sie das Batchintervall auf 50 ms fest, um die Anzahl der Service Bus-Clientprotokollübertragungen zu verringern. Wenn mehrere Absender verwendet werden, erhöhen Sie das Batchintervall auf 100 ms.
* Lassen Sie den Speicherzugriff als Batch aktiviert. Dies erhöht die Gesamtrate, mit der Nachrichten in die Warteschlange geschrieben werden können.
* Legen Sie den Wert für Vorabrufe auf das 20-fache der maximalen Verarbeitungsraten aller Empfänger einer Factory fest. Dies verringert die Anzahl der Service Bus-Clientprotokollübertragungen.

### <a name="multiple-high-throughput-queues"></a>Mehrere Warteschlangen mit hohem Durchsatz

Zielsetzung: Maximieren des Gesamtdurchsatzes mehrerer Warteschlangen. Der Durchsatz einer einzelnen Warteschlange ist mittel oder hoch.

Um den maximalen Durchsatz für mehrere Warteschlangen zu erhalten, verwenden Sie die beschriebenen Einstellungen, um den Durchsatz einer einzelnen Warteschlange zu maximieren. Verwenden Sie außerdem unterschiedliche Factorys zum Erstellen von Clients, die an verschiedene Warteschlangen senden bzw. von ihnen empfangen.

### <a name="low-latency-queue"></a>Warteschlange mit niedriger Latenz

Zielsetzung: Minimieren der Latenz einer Warteschlange oder eines Themas. Die Anzahl der Absender und Empfänger ist klein. Der Durchsatz der Warteschlange ist klein oder mittel.

* Deaktivieren Sie die clientseitige Batchverarbeitung. Der Client sendet eine Nachricht sofort.
* Deaktivieren Sie den Speicherzugriff als Batch. Der Dienst schreibt die Nachricht sofort in den Speicher.
* Wenn Sie einen einzelnen Client verwenden, legen Sie den Wert für den Vorabruf auf das 20-fache der Verarbeitungsrate des Empfängers fest. Wenn mehrere Nachrichten gleichzeitig in der Warteschlange ankommen, überträgt das Service Bus-Clientprotokoll sie alle zur gleichen Zeit. Wenn der Client die nächste Nachricht empfängt, befindet sich diese Nachricht bereits im lokalen Cache. Der Cache sollte klein sein.
* Wenn mehrere Clients verwendet werden, legen Sie den Wert für den Vorabruf auf 0 fest. Auf diese Weise kann der zweite Client die zweite Nachricht empfangen, während der erste Client noch die erste Nachricht verarbeitet.

### <a name="queue-with-a-large-number-of-senders"></a>Warteschlange mit einer großen Anzahl von Absendern

Zielsetzung: Maximieren des Durchsatzes einer Warteschlange oder eines Themas mit einer großen Anzahl von Absendern. Jeder Absender sendet Nachrichten mit einer mittleren Rate. Die Anzahl der Empfänger ist klein.

Service Bus ermöglicht bis zu 1.000 gleichzeitige Verbindungen mit einer Messagingentität. Dieser Grenzwert wird auf Namespaceebene erzwungen, und Warteschlangen, Themen oder Abonnements werden durch den Grenzwert für gleichzeitige Verbindungen pro Namespace begrenzt. Bei Warteschlangen wird diese Anzahl zwischen Absendern und Empfängern aufgeteilt. Wenn alle 1000 Verbindungen für Absender benötigt werden, ersetzen Sie die Warteschlange durch ein Thema und ein einzelnes Abonnement. Ein Thema akzeptiert bis zu 1.000 gleichzeitige Verbindungen von Absendern. Das Abonnement nimmt weitere 1.000 gleichzeitige Verbindungen von Empfängern an. Wenn mehr als 1000 gleichzeitige Absender erforderlich sind, sollten die Absender über HTTP Nachrichten an das Service Bus-Protokoll senden.

Gehen Sie folgendermaßen vor, um den Durchsatz zu maximieren:

* Wenn sich jeder Absender in einem anderen Prozess befindet, verwenden Sie nur eine Factory pro Prozess.
* Verwenden Sie asynchrone Vorgänge, um die clientseitige Batchverarbeitung zu nutzen.
* Verwenden Sie das standardmäßige Batchintervall von 20 ms, um die Anzahl der Service Bus-Clientprotokollübertragungen zu verringern.
* Lassen Sie den Speicherzugriff als Batch aktiviert. Dies erhöht die Gesamtrate, mit der Nachrichten in die Warteschlange oder in das Thema geschrieben werden können.
* Legen Sie den Wert für Vorabrufe auf das 20-fache der maximalen Verarbeitungsraten aller Empfänger einer Factory fest. Dies verringert die Anzahl der Service Bus-Clientprotokollübertragungen.

### <a name="queue-with-a-large-number-of-receivers"></a>Warteschlange mit einer großen Anzahl von Empfängern

Zielsetzung: Maximieren der Empfangsrate einer Warteschlange oder eines Abonnements mit einer großen Anzahl von Empfängern. Jeder Empfänger empfängt Nachrichten mit einer mittleren Rate. Die Anzahl der Absender ist klein.

Service Bus ermöglicht bis zu 1000 gleichzeitige Verbindungen mit einer Messagingentität. Wenn für eine Warteschlange mehr als 1000 Empfänger erforderlich sind, ersetzen Sie die Warteschlange durch ein Thema und mehrere Abonnements. Jedes Abonnement kann bis zu 1000 gleichzeitige Verbindungen unterstützen. Alternativ können Empfänger über das HTTP-Protokoll auf die Warteschlange zugreifen.

Verwenden Sie die folgenden Richtlinien, um den Durchsatz zu maximieren:

* Wenn sich jeder Empfänger in einem anderen Prozess befindet, verwenden Sie nur eine Factory pro Prozess.
* Empfänger können synchrone oder asynchrone Vorgänge verwenden. Bei der mittleren Empfangsrate eines einzelnen Empfängers wirkt sich die clientseitige Batchverarbeitung einer Anforderung zum Abschließen nicht auf den Empfängerdurchsatz aus.
* Lassen Sie den Speicherzugriff als Batch aktiviert. Dies verringert die Gesamtlast der Entität. Es verringert zudem die Gesamtrate, mit der Nachrichten in die Warteschlange oder in das Thema geschrieben werden können.
* Legen Sie den Wert für den Vorabruf auf einen kleinen Wert fest (z. B. "PrefetchCount" = 10). Dadurch wird verhindert, dass Empfänger über freie Kapazitäten verfügen, während andere Empfänger eine große Anzahl von Nachrichten zwischengespeichert haben.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Thema mit einer geringen Anzahl von Abonnements

Zielsetzung: Maximieren des Durchsatzes eines Themas mit einer geringen Anzahl von Abonnements. Eine Nachricht wird von zahlreichen Abonnements empfangen, was bedeutet, dass die kombinierte Empfangsrate aller Abonnements größer als die Senderate ist. Die Anzahl der Absender ist klein. Die Empfängeranzahl pro Abonnement ist gering.

Verwenden Sie die folgenden Richtlinien, um den Durchsatz zu maximieren:

* Verwenden Sie zur Erhöhung der Gesamtsenderate für das Thema mehrere Messagingfactorys, um Absender zu erstellen. Verwenden Sie für jeden Absender asynchrone Vorgänge oder mehrere Threads.
* Verwenden Sie zur Erhöhung der Gesamtempfangsrate aus einem Abonnement mehrere Messagingfactorys, um Empfänger zu erstellen. Verwenden Sie für jeden Empfänger asynchrone Vorgänge oder mehrere Threads.
* Verwenden Sie asynchrone Vorgänge, um die clientseitige Batchverarbeitung zu nutzen.
* Verwenden Sie das standardmäßige Batchintervall von 20 ms, um die Anzahl der Service Bus-Clientprotokollübertragungen zu verringern.
* Lassen Sie den Speicherzugriff als Batch aktiviert. Dies erhöht die Gesamtrate, mit der Nachrichten in das Thema geschrieben werden können.
* Legen Sie den Wert für Vorabrufe auf das 20-fache der maximalen Verarbeitungsraten aller Empfänger einer Factory fest. Dies verringert die Anzahl der Service Bus-Clientprotokollübertragungen.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Thema mit einer großen Anzahl von Abonnements

Zielsetzung: Maximieren des Durchsatzes eines Themas mit einer großen Anzahl von Abonnements. Eine Nachricht wird von zahlreichen Abonnements empfangen, was bedeutet, dass die kombinierte Empfangsrate aller Abonnements viel größer als die Senderate ist. Die Anzahl der Absender ist klein. Die Empfängeranzahl pro Abonnement ist gering.

Themen mit einer großen Anzahl von Abonnements weisen normalerweise einen geringen Gesamtdurchsatz auf, wenn alle Nachrichten an alle Abonnements weitergeleitet werden. Dies liegt daran, dass jede Nachricht mehrmals empfangen wird, und alle Nachrichten in einem Thema sowie dessen Abonnements werden im gleichen Speicher gespeichert. Die Annahme besteht hier darin, dass von einer geringen Anzahl von Absendern und Empfängern pro Abonnement ausgegangen wird. Service Bus unterstützt bis zu 2000 Abonnements pro Thema.

Probieren Sie die folgenden Schritte aus, um den Durchsatz zu maximieren:

* Verwenden Sie asynchrone Vorgänge, um die clientseitige Batchverarbeitung zu nutzen.
* Verwenden Sie das standardmäßige Batchintervall von 20 ms, um die Anzahl der Service Bus-Clientprotokollübertragungen zu verringern.
* Lassen Sie den Speicherzugriff als Batch aktiviert. Dies erhöht die Gesamtrate, mit der Nachrichten in das Thema geschrieben werden können.
* Legen Sie den Wert für den Vorabruf auf das 20-fache der erwarteten Empfangsrate in Sekunden fest. Dies verringert die Anzahl der Service Bus-Clientprotokollübertragungen.

<!-- .NET Standard SDK, Microsoft.Azure.ServiceBus -->
[QueueClient]: /dotnet/api/microsoft.azure.servicebus.queueclient
[MessageSender]: /dotnet/api/microsoft.azure.servicebus.core.messagesender

<!-- .NET Framework SDK, Microsoft.Azure.ServiceBus -->
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[BatchFlushInterval]: /dotnet/api/microsoft.servicebus.messaging.messagesender.batchflushinterval
[ForcePersistence]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence
[EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnableFiltering]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing

<!-- Local links -->
[Partitioned messaging entities]: service-bus-partitioning.md
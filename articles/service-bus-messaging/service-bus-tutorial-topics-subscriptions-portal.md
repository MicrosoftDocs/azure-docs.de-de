---
title: Aktualisieren des Bestands über das Azure-Portal und mithilfe von Themen/Abonnements
description: In diesem Tutorial erfahren Sie, wie Sie Nachrichten für ein Thema und ein Abonnement senden und empfangen und Filterregeln per .NET hinzufügen und verwenden.
author: spelluru
ms.author: spelluru
ms.date: 10/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: f2c8d107c6de4965472c3fb04ff626841fb1f6ea
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95810734"
---
# <a name="tutorial-update-inventory-using-azure-portal-and-topicssubscriptions"></a>Tutorial: Aktualisieren des Bestands mit dem Azure-Portal und mit Themen/Abonnements

Microsoft Azure Service Bus ist ein mehrinstanzenfähiger Cloudmessagingdienst, mit dem Informationen zwischen Anwendungen und Diensten gesendet werden. Asynchrone Vorgänge ermöglichen ein flexibles Brokermessaging sowie strukturiertes First-In-First-Out-Messaging (FIFO) und Funktionen zum Veröffentlichen/Abonnieren. In diesem Tutorial wird veranschaulicht, wie Sie Service Bus-Themen und -Abonnements in einem Einzelhandelsbestands-Szenario mit Veröffentlichen/Abonnieren-Kanälen über das Azure-Portal und .NET verwenden.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Erstellen eines Service Bus-Themas und eines oder mehrerer Abonnements für das Thema mit dem Azure-Portal
> * Hinzufügen von Themenfiltern per .NET-Code
> * Erstellen von zwei Nachrichten mit unterschiedlichem Inhalt
> * Senden der Nachrichten und Überprüfen des Eingangs in den entsprechenden Abonnements
> * Empfangen von Nachrichten aus den Abonnements

Ein Beispiel dieses Szenarios ist eine Aktualisierung des Bestandssortiments für mehrere Einzelhandelsgeschäfte. Hierbei erhält jedes Geschäft bzw. jede Gruppe von Geschäften Nachrichten, die jeweils die Aktualisierung der Sortimente betreffen. In diesem Tutorial wird veranschaulicht, wie Sie dieses Szenario mit Abonnements und Filtern implementieren. Zuerst erstellen Sie ein Thema mit drei Abonnements, fügen einige Regeln und Filter hinzu und führen dann das Senden und Empfangen von Nachrichten aus den Themen und Abonnements durch.

![topic](./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png)

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto][] erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie dieses Tutorial ausführen können, müssen folgende Komponenten installiert sein:

- [Visual Studio 2017 Update 3 (Version 15.3, 26730.01)](https://www.visualstudio.com/vs) oder höher
- [NET Core SDK](https://www.microsoft.com/net/download/windows) ab Version 2.0

## <a name="service-bus-topics-and-subscriptions"></a>Service Bus-Themen und -Abonnements

Jedes [Abonnement eines Themas](service-bus-messaging-overview.md#topics) kann eine Kopie jeder Nachricht empfangen. Die Themen sind in Bezug auf das Protokoll und die Semantik vollständig mit Service Bus-Warteschlangen kompatibel. Service Bus-Themen unterstützen ein umfassendes Auswahlregel-Array mit Filterbedingungen. Es sind optionale Aktionen vorhanden, mit denen Nachrichteneigenschaften festgelegt oder geändert werden. Jedes Mal, wenn sich für eine Regel eine Übereinstimmung ergibt, wird eine Nachricht erzeugt. Weitere Informationen zu Regeln, Filtern und Aktionen finden Sie unter diesem [Link](topic-filters.md).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]



## <a name="create-filter-rules-on-subscriptions"></a>Erstellen von Filterregeln zu Abonnements

Nachdem der Namespace und das Thema bzw. die Abonnements bereitgestellt wurden und Sie über die erforderlichen Anmeldeinformationen verfügen, sind Sie zum Erstellen von Filterregeln für die Abonnements bereit und können dann Nachrichten senden und empfangen. Sie können sich den Code in [diesem GitHub-Ordner mit Beispielen](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters) ansehen.

## <a name="send-and-receive-messages"></a>Senden und Empfangen von Nachrichten

Gehen Sie wie folgt vor, um den Code auszuführen:

1. Klonen Sie in einem Eingabeaufforderungsfenster bzw. einer PowerShell-Eingabeaufforderung das [Service Bus-GitHub-Repository](https://github.com/Azure/azure-service-bus/), indem Sie den folgenden Befehl ausführen:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Navigieren Sie zum Beispielordner `azure-service-bus\samples\DotNet\Azure.Messaging.ServiceBus\BasicSendReceiveTutorialwithFilters`.

3. Halten Sie die Verbindungszeichenfolge bereit, die Sie im Abschnitt „Abrufen der Verwaltungsanmeldeinformationen“ dieses Tutorials in den Editor kopiert haben. Außerdem benötigen Sie den Namen des Themas, das Sie im vorherigen Abschnitt erstellt haben.

4. Geben Sie an der Eingabeaufforderung den folgenden Befehl ein:

   ```shell
   dotnet build
   ```

5. Navigieren Sie zum Ordner `BasicSendReceiveTutorialwithFilters\bin\Debug\netcoreapp3.1`.

6. Geben Sie den folgenden Befehl ein, um das Programm auszuführen. Ersetzen Sie `myConnectionString` durch den abgerufenen Wert und `myTopicName` durch den Namen des von Ihnen erstellten Themas:

   ```shell
   dotnet BasicSendReceiveTutorialwithFilters.dll -ConnectionString "myConnectionString" -TopicName "myTopicName"
   ``` 
7. Befolgen Sie die Anleitung in der Konsole, um zuerst die Filtererstellung auszuwählen. Ein Teil der Erstellung von Filtern ist das Entfernen der Standardfilter. Beim Verwenden von PowerShell oder der CLI müssen Sie den Standardfilter nicht entfernen. Dies ist nur erforderlich, wenn Sie die Erstellung per Code durchführen. Die Konsolenbefehle 1 und 3 dienen zum Verwalten der Filter für die Abonnements, die Sie zuvor erstellt haben:

   - Ausführung von Befehl 1: Standardfilter entfernen
   - Ausführung von Befehl 2: Eigene Filter hinzufügen
   - Ausführung von Befehl 3: Optionales Entfernen Ihrer eigenen Filter Beachten Sie, dass die Standardfilter bei diesem Vorgang nicht neu erstellt werden.

     ![Anzeige der Ausgabe von 2](./media/service-bus-tutorial-topics-subscriptions-portal/create-rules.png)

8. Nach der Filtererstellung können Sie Nachrichten senden. Drücken Sie 4, und verfolgen Sie, wie zehn Nachrichten an das Thema gesendet werden:

    ![Senden der Ausgabe](./media/service-bus-tutorial-topics-subscriptions-portal/send-output.png)

9. Drücken Sie 5, und verfolgen Sie, wie die Nachrichten empfangen werden. Falls Sie nicht zehn Nachrichten zurückerhalten, können Sie durch Drücken von „m“ das Menü anzeigen und dann erneut 5 drücken.

    ![Empfangen der Ausgabe](./media/service-bus-tutorial-topics-subscriptions-portal/receive-output.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können den Namespace und die Warteschlange löschen, wenn diese Elemente nicht mehr benötigt werden. Wählen Sie diese Ressourcen hierzu im Portal aus, und klicken Sie auf **Löschen**.

## <a name="understand-the-sample-code"></a>Grundlagen des Beispielcodes

Dieser Abschnitt enthält weitere Details zur Funktionsweise des Beispielcodes.

### <a name="get-connection-string-and-topic"></a>Abrufen der Verbindungszeichenfolge und des Themas

Zuerst wird im Code ein Satz mit Variablen für die verbleibende Ausführung des Programms deklariert.

```csharp
string ServiceBusConnectionString;
string TopicName;

static string[] Subscriptions = { "S1", "S2", "S3" };
static IDictionary<string, string[]> SubscriptionFilters = new Dictionary<string, string[]> {
    { "S1", new[] { "StoreId IN('Store1', 'Store2', 'Store3')", "StoreId = 'Store4'"} },
    { "S2", new[] { "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'" } },
    { "S3", new[] { "sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')" } }
};
// You can have only have one action per rule and this sample code supports only one action for the first filter, which is used to create the first rule. 
static IDictionary<string, string> SubscriptionAction = new Dictionary<string, string> {
    { "S1", "" },
    { "S2", "" },
    { "S3", "SET sys.Label = 'SalesEvent'"  }
};
static string[] Store = { "Store1", "Store2", "Store3", "Store4", "Store5", "Store6", "Store7", "Store8", "Store9", "Store10" };
static string SysField = "sys.To";
static string CustomField = "StoreId";
static int NrOfMessagesPerStore = 1; // Send at least 1.
```

Die Verbindungszeichenfolge und der Themenname werden wie gezeigt über Befehlszeilenparameter übergeben und dann in der `Main()`-Methode gelesen:

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string TopicName = "";

    for (int i = 0; i < args.Length; i++)
    {
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i + 1]}");
            ServiceBusConnectionString = args[i + 1]; // Alternatively enter your connection string here.
        }
        else if (args[i] == "-TopicName")
        {
            Console.WriteLine($"TopicName: {args[i + 1]}");
            TopicName = args[i + 1]; // Alternatively enter your queue name here.
        }
    }

    if (ServiceBusConnectionString != "" && TopicName != "")
    {
        Program P = StartProgram(ServiceBusConnectionString, TopicName);
        P.PresentMenu().GetAwaiter().GetResult();
    }
    else
    {
        Console.WriteLine("Specify -Connectionstring and -TopicName to execute the example.");
        Console.ReadKey();
    }
}
```

### <a name="remove-default-filters"></a>Entfernen von Standardfiltern

Wenn Sie ein Abonnement erstellen, erstellt Service Bus einen Standardfilter pro Abonnement. Dieser Filter ermöglicht den Empfang aller Nachrichten, die an das Thema gesendet werden. Falls Sie benutzerdefinierte Filter verwenden möchten, können Sie den Standardfilter entfernen, wie im folgenden Code gezeigt:

```csharp
private async Task RemoveDefaultFilters()
{
    Console.WriteLine($"Starting to remove default filters.");

    try
    {
        var client = new ServiceBusAdministrationClient(ServiceBusConnectionString);
        foreach (var subscription in Subscriptions)
        {
            await client.DeleteRuleAsync(TopicName, subscription, CreateRuleOptions.DefaultRuleName);
            Console.WriteLine($"Default filter for {subscription} has been removed.");
        }

        Console.WriteLine("All default Rules have been removed.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="create-filters"></a>Erstellen von Filtern

Mit dem folgenden Code werden die in diesem Tutorial definierten benutzerdefinierten Filter hinzugefügt:

```csharp
private async Task CreateCustomFilters()
{
    try
    {
        for (int i = 0; i < Subscriptions.Length; i++)
        {
            var client = new ServiceBusAdministrationClient(ServiceBusConnectionString);
            string[] filters = SubscriptionFilters[Subscriptions[i]];
            if (filters[0] != "")
            {
                int count = 0;
                foreach (var myFilter in filters)
                {
                    count++;

                    string action = SubscriptionAction[Subscriptions[i]];
                    if (action != "")
                    {
                        await client.CreateRuleAsync(TopicName, Subscriptions[i], new CreateRuleOptions
                        {
                            Filter = new SqlRuleFilter(myFilter),
                            Action = new SqlRuleAction(action),
                            Name = $"MyRule{count}"
                        });
                    }
                    else
                    {
                        await client.CreateRuleAsync(TopicName, Subscriptions[i], new CreateRuleOptions
                        {
                            Filter = new SqlRuleFilter(myFilter),
                            Name = $"MyRule{count}"
                        });
                    }
                }
            }

            Console.WriteLine($"Filters and actions for {Subscriptions[i]} have been created.");
        }

        Console.WriteLine("All filters and actions have been created.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="remove-your-custom-created-filters"></a>Entfernen Ihrer erstellten benutzerdefinierten Filter

Falls Sie alle Filter für Ihr Abonnement entfernen möchten, können Sie die Vorgehensweise dem folgenden Code entnehmen:

```csharp
private async Task CleanUpCustomFilters()
{
    foreach (var subscription in Subscriptions)
    {
        try
        {
            var client = new ServiceBusAdministrationClient(ServiceBusConnectionString);
            IAsyncEnumerator<RuleProperties> rules = client.GetRulesAsync(TopicName, subscription).GetAsyncEnumerator();
            while (await rules.MoveNextAsync())
            {
                await client.DeleteRuleAsync(TopicName, subscription, rules.Current.Name);
                Console.WriteLine($"Rule {rules.Current.Name} has been removed.");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }
    Console.WriteLine("All default filters have been removed.\n");

    await PresentMenu();
}
```

### <a name="send-messages"></a>Senden von Nachrichten

Das Senden von Nachrichten an ein Thema ähnelt dem Senden von Nachrichten an eine Warteschlange. In diesem Beispiel wird veranschaulicht, wie Sie Nachrichten senden, indem Sie eine Aufgabenliste und die asynchrone Verarbeitung verwenden:

```csharp
public async Task SendMessages()
{
    try
    {
        await using var client = new ServiceBusClient(ServiceBusConnectionString);
        var taskList = new List<Task>();
        for (int i = 0; i < Store.Length; i++)
        {
            taskList.Add(SendItems(client, Store[i]));
        }

        await Task.WhenAll(taskList);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }
    Console.WriteLine("\nAll messages sent.\n");
}

private async Task SendItems(ServiceBusClient client, string store)
{
    // create the sender
    ServiceBusSender tc = client.CreateSender(TopicName);

    for (int i = 0; i < NrOfMessagesPerStore; i++)
    {
        Random r = new Random();
        Item item = new Item(r.Next(5), r.Next(5), r.Next(5));

        // Note the extension class which is serializing an deserializing messages
        ServiceBusMessage message = item.AsMessage();
        message.To = store;
        message.ApplicationProperties.Add("StoreId", store);
        message.ApplicationProperties.Add("Price", item.GetPrice().ToString());
        message.ApplicationProperties.Add("Color", item.GetColor());
        message.ApplicationProperties.Add("Category", item.GetItemCategory());

        await tc.SendMessageAsync(message);
        Console.WriteLine($"Sent item to Store {store}. Price={item.GetPrice()}, Color={item.GetColor()}, Category={item.GetItemCategory()}"); ;
    }
}
```

### <a name="receive-messages"></a>Empfangen von Nachrichten

Die Nachrichten werden wieder über eine Aufgabenliste empfangen, und im Code wird die Batchverarbeitung verwendet. Sie können das Senden und Empfangen per Batchverarbeitung durchführen, aber in diesem Beispiel wird dies nur für den Empfangsvorgang gezeigt. In einem realen Fall würden Sie die Schleife nicht unterbrechen, sondern fortführen und einen längeren Zeitraum festlegen, z.B. eine Minute. Der Empfangsaufruf für den Broker wird für diesen Zeitraum offen gehalten. Wenn Nachrichten eingehen, werden sie sofort zurückgegeben, und es wird ein neuer Aufruf durchgeführt. Dieses Konzept wird als *Long Polling* (langes Abrufintervall) bezeichnet. Die Verwendung des „Empfangspump“-Vorgangs, der in der [Schnellstartanleitung](service-bus-quickstart-portal.md) und in verschiedenen anderen Beispielen im Repository beschrieben wird, ist eine gängigere Option.

```csharp
public async Task Receive()
{
    var taskList = new List<Task>();
    for (var i = 0; i < Subscriptions.Length; i++)
    {
        taskList.Add(this.ReceiveMessages(Subscriptions[i]));
    }

    await Task.WhenAll(taskList);
}

private async Task ReceiveMessages(string subscription)
{
    await using var client = new ServiceBusClient(ServiceBusConnectionString);
    ServiceBusReceiver receiver = client.CreateReceiver(TopicName, subscription);

    // In reality you would not break out of the loop like in this example but would keep looping. The receiver keeps the connection open
    // to the broker for the specified amount of seconds and the broker returns messages as soon as they arrive. The client then initiates
    // a new connection. So in reality you would not want to break out of the loop. 
    // Also note that the code shows how to batch receive, which you would do for performance reasons. For convenience you can also always
    // use the regular receive pump which we show in our Quick Start and in other github samples.
    while (true)
    {
        try
        {
            //IList<Message> messages = await receiver.ReceiveAsync(10, TimeSpan.FromSeconds(2));
            // Note the extension class which is serializing an deserializing messages and testing messages is null or 0.
            // If you think you did not receive all messages, just press M and receive again via the menu.
            IReadOnlyList<ServiceBusReceivedMessage> messages = await receiver.ReceiveMessagesAsync(maxMessages: 100);

            if (messages.Any())
            {
                foreach (ServiceBusReceivedMessage message in messages)
                {
                    lock (Console.Out)
                    {
                        Item item = message.As<Item>();
                        IReadOnlyDictionary<string, object> myApplicationProperties = message.ApplicationProperties;
                        Console.WriteLine($"StoreId={myApplicationProperties["StoreId"]}");
                        if (message.Subject != null)
                        {
                            Console.WriteLine($"Subject={message.Subject}");
                        }
                        Console.WriteLine(
                            $"Item data: Price={item.GetPrice()}, Color={item.GetColor()}, Category={item.GetItemCategory()}");
                    }

                    await receiver.CompleteMessageAsync(message);
                }
            }
            else
            {
                break;
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }
}
```

> [!NOTE]
> Sie können Service Bus-Ressourcen mit dem [Service Bus-Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) verwalten. Mit dem Service Bus-Explorer können Benutzer eine Verbindung mit einem Service Bus-Namespace herstellen und Messagingentitäten auf einfache Weise verwalten. Das Tool stellt erweiterte Features wie Import-/Exportfunktionen oder Testmöglichkeiten für Themen, Warteschlangen, Abonnements, Relaydienste, Notification Hubs und Event Hubs zur Verfügung. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie mit dem Azure-Portal Ressourcen bereitgestellt und anschließend Nachrichten für ein Service Bus-Thema und die dazugehörigen Abonnements gesendet und empfangen. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen eines Service Bus-Themas und eines oder mehrerer Abonnements für das Thema mit dem Azure-Portal
> * Hinzufügen von Themenfiltern per .NET-Code
> * Erstellen von zwei Nachrichten mit unterschiedlichem Inhalt
> * Senden der Nachrichten und Überprüfen des Eingangs in den entsprechenden Abonnements
> * Empfangen von Nachrichten aus den Abonnements

Falls Sie sich weitere Beispiele für das Senden und Empfangen von Nachrichten ansehen möchten, sind die [Service Bus-Beispiele auf GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted) ein guter Einstieg.

Fahren Sie mit dem nächsten Tutorial fort, um weitere Informationen zur Verwendung der Veröffentlichen/Abonnieren-Funktionen von Service Bus zu erhalten.

> [!div class="nextstepaction"]
> [Reagieren auf Ereignisse über Event Grid](service-bus-to-event-grid-integration-example.md)

[kostenloses Konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Azure portal]: https://portal.azure.com/

[connection-string]: ./media/service-bus-tutorial-topics-subscriptions-portal/connection-string.png
[service-bus-flow]: ./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png

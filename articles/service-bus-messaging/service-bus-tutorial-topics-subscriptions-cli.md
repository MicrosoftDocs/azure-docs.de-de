---
title: Tutorial – Aktualisieren des Einzelhandelsbestands-Sortiments mit Veröffentlichen/Abonnieren-Kanälen und Themenfiltern über die Azure CLI | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie Nachrichten für ein Thema und ein Abonnement senden und empfangen und Filterregeln per Azure CLI hinzufügen und verwenden.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.author: spelluru
ms.date: 09/22/2018
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: e55c8c6a579489c8ed5a13ccb93f0e72c286ab13
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991940"
---
# <a name="tutorial-update-inventory-using-cli-and-topicssubscriptions"></a>Tutorial: Aktualisieren des Bestands über die CLI und mit Themen/Abonnements

Microsoft Azure Service Bus ist ein mehrinstanzenfähiger Cloudmessagingdienst, mit dem Informationen zwischen Anwendungen und Diensten gesendet werden. Asynchrone Vorgänge ermöglichen ein flexibles Brokermessaging sowie strukturiertes First-In-First-Out-Messaging (FIFO) und Funktionen zum Veröffentlichen/Abonnieren. In diesem Tutorial wird veranschaulicht, wie Sie Service Bus-Themen und -Abonnements in einem Einzelhandelsbestands-Szenario mit Veröffentlichen/Abonnieren-Kanälen über die Azure CLI und Java verwenden.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Erstellen eines Service Bus-Themas und eines oder mehrerer Abonnements für das Thema mit der Azure CLI
> * Hinzufügen von Themenfiltern per Azure CLI
> * Erstellen von zwei Nachrichten mit unterschiedlichem Inhalt
> * Senden der Nachrichten und Überprüfen des Eingangs in den entsprechenden Abonnements
> * Empfangen von Nachrichten aus den Abonnements

Ein Beispiel dieses Szenarios ist eine Aktualisierung des Bestandssortiments für mehrere Einzelhandelsgeschäfte. Hierbei erhält jedes Geschäft bzw. jede Gruppe von Geschäften Nachrichten, die jeweils die Aktualisierung der Sortimente betreffen. In diesem Tutorial wird veranschaulicht, wie Sie dieses Szenario mit Abonnements und Filtern implementieren. Zuerst erstellen Sie ein Thema mit drei Abonnements, fügen einige Regeln und Filter hinzu und führen dann das Senden und Empfangen von Nachrichten aus den Themen und Abonnements durch.

![Thema](./media/service-bus-tutorial-topics-subscriptions-cli/about-service-bus-topic.png)

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto][] erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für die Entwicklung einer Service Bus-App mit Java muss Folgendes installiert sein:

- [Java Development Kit](https://aka.ms/azure-jdks), aktuelle Version
- [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure)
- [Apache Maven](https://maven.apache.org), Version 3.0 oder höher

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial die Azure CLI-Version 2.0.4 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli).

## <a name="service-bus-topics-and-subscriptions"></a>Service Bus-Themen und -Abonnements

Jedes [Abonnement eines Themas](service-bus-messaging-overview.md#topics) kann eine Kopie jeder Nachricht empfangen. Die Themen sind in Bezug auf das Protokoll und die Semantik vollständig mit Service Bus-Warteschlangen kompatibel. Service Bus-Themen unterstützen ein umfassendes Auswahlregel-Array mit Filterbedingungen. Es sind optionale Aktionen vorhanden, mit denen Nachrichteneigenschaften festgelegt oder geändert werden. Jedes Mal, wenn sich für eine Regel eine Übereinstimmung ergibt, wird eine Nachricht erzeugt. Weitere Informationen zu Regeln, Filtern und Aktionen finden Sie unter diesem [Link](topic-filters.md).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Öffnen Sie nach der Installation der CLI eine Eingabeaufforderung, und geben Sie die folgenden Befehle ein, um sich bei Azure anzumelden. Diese Schritte sind nicht erforderlich, wenn Sie Cloud Shell nutzen:

1. Führen Sie bei lokaler Verwendung der Azure CLI den folgenden Befehl aus, um sich bei Azure anzumelden. Dieser Anmeldeschritt ist nicht erforderlich, wenn Sie diese Befehle in Cloud Shell ausführen:

   ```azurecli-interactive
   az login
   ```

2. Legen Sie den aktuellen Abonnementkontext auf das gewünschte Azure-Abonnement fest:

   ```azurecli-interactive
   az account set --subscription Azure_subscription_name
   ```

## <a name="use-cli-to-provision-resources"></a>Verwenden der CLI zum Bereitstellen von Ressourcen

Führen Sie die folgenden Befehle aus, um Service Bus-Ressourcen bereitzustellen. Achten Sie darauf, dass Sie alle Platzhalter durch die entsprechenden Werte ersetzen:

```azurecli-interactive
# Create a resource group
az group create --name myResourcegroup --location eastus

# Create a Service Bus messaging namespace with a unique name
namespaceName=myNameSpace$RANDOM
az servicebus namespace create \
   --resource-group myResourceGroup \
   --name $namespaceName \
   --location eastus

# Create a Service Bus topic
az servicebus topic create --resource-group myResourceGroup \
   --namespace-name $namespaceName \
   --name myTopic

# Create subscription 1 to the topic
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S1

# Create filter 1 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MyFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3')"

# Create filter 2 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MySecondFilter --filter-sql-expression "StoreId = 'Store4'"

# Create subscription 2
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S2

# Create filter 3 - use message header properties via IN list and 
# combine with custom properties.
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S2 --name MyFilter --filter-sql-expression "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'"

# Create subscription 3
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S3

# Create filter 4 - Get everything except messages for subscription 1 and 2. 
# Also modify and add an action; in this case set the label to a specified value. 
# Assume those stores might not be part of your main store, so you only add 
# specific items to them. For that, you flag them specifically.
az servicebus rule create --resource-group DemoGroup --namespace-name DemoNamespaceSB --topic-name tutorialtest1
 --subscription-name S3 --name MyFilter --filter-sql-expression "sys.To NOT IN ('Store1','Store2','Store3','Store4','Sto
re5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Stor
e8')" --action-sql-expression "SET sys.Label = 'SalesEvent'"

# Get the connection string
connectionString=$(az servicebus namespace authorization-rule keys list \
   --resource-group myResourceGroup \
   --namespace-name  $namespaceName \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString --output tsv)
```

Kopieren Sie nach Ausführung des letzten Befehls die Verbindungszeichenfolge und den von Ihnen ausgewählten Warteschlangennamen an einen temporären Ort, z.B. den Editor. Sie benötigen diese Angaben im nächsten Schritt.

## <a name="create-filter-rules-on-subscriptions"></a>Erstellen von Filterregeln zu Abonnements

Nachdem der Namespace und das Thema bzw. die Abonnements bereitgestellt wurden und Sie über die erforderlichen Anmeldeinformationen verfügen, sind Sie zum Erstellen von Filterregeln für die Abonnements bereit und können dann Nachrichten senden und empfangen. Sie können sich den Code in [diesem GitHub-Ordner mit Beispielen](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters) ansehen.

## <a name="send-and-receive-messages"></a>Senden und Empfangen von Nachrichten

1. Stellen Sie sicher, dass Cloud Shell geöffnet ist und dass die Bash-Eingabeaufforderung angezeigt wird.

2. Klonen Sie das [Service Bus-GitHub-Repository](https://github.com/Azure/azure-service-bus/), indem Sie den folgenden Befehl ausführen:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Navigieren Sie zum Ordner `azure-service-bus/samples/Java/quickstarts-and-tutorials/quickstart-java/tutorial-topics-subscriptions-filters-java` mit den Beispielen. Beachten Sie, dass in der Bash-Shell für die Befehle die Groß-/Kleinschreibung berücksichtigt wird und normale Schrägstriche als Pfadtrennzeichen verwendet werden müssen.

3. Führen Sie den folgenden Befehl aus, um die Anwendung zu erstellen:
   
   ```shell
   mvn clean package -DskipTests
   ```
4. Führen Sie den folgenden Befehl aus, um das Programm auszuführen. Achten Sie darauf, dass Sie die Platzhalter durch die Verbindungszeichenfolge und den Themennamen aus dem vorherigen Schritt ersetzen:

   ```shell
   java -jar .\target\tutorial-topics-subscriptions-filters-1.0.0-jar-with-dependencies.jar -c "myConnectionString" -t "myTopicName"
   ```

   Verfolgen Sie, wie zehn Nachrichten an das Thema gesendet und anschließend von den einzelnen Abonnements empfangen werden:

   ![Programmausgabe](./media/service-bus-tutorial-topics-subscriptions-cli/service-bus-tutorial-topics-subscriptions-cli.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Führen Sie den folgenden Befehl aus, um die Ressourcengruppe, den Namespace und alle zugehörigen Ressourcen zu entfernen:

```azurecli-interactive
az group delete --resource-group my-resourcegroup
```

## <a name="understand-the-sample-code"></a>Grundlagen des Beispielcodes

Dieser Abschnitt enthält weitere Details zur Funktionsweise des Beispielcodes.

### <a name="get-connection-string-and-queue"></a>Abrufen der Verbindungszeichenfolge und der Warteschlange

Zuerst wird im Code ein Satz mit Variablen für die verbleibende Ausführung des Programms deklariert:

```java
    public String ConnectionString = null;
    public String TopicName = null;
    static final String[] Subscriptions = {"S1","S2","S3"};
    static final String[] Store = {"Store1","Store2","Store3","Store4","Store5","Store6","Store7","Store8","Store9","Store10"};
    static final String SysField = "sys.To";
    static final String CustomField = "StoreId";
    int NrOfMessagesPerStore = 1; // Send at least 1.
```

Die Verbindungszeichenfolge und der Themenname sind die einzigen Werte, die über die Befehlszeilenparameter hinzugefügt und an `main()` übergeben werden. Die eigentliche Codeausführung wird in der `run()`-Methode ausgelöst, und es werden Nachrichten gesendet und dann vom Thema empfangen:

```java
public static void main(String[] args) {
    TutorialTopicsSubscriptionsFilters app = new TutorialTopicsSubscriptionsFilters();
        try {
            app.runApp(args);
            app.run();
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
        }
        System.exit(0);
    }
}

public void run() throws Exception {
    // Send sample messages.
    this.sendMessagesToTopic();

    // Receive messages from subscriptions.
    this.receiveAllMessages();
}
```

### <a name="create-topic-client-to-send-messages"></a>Erstellen eines Themenclients zum Senden von Nachrichten

Für das Senden und Empfangen von Nachrichten erstellt die `sendMessagesToTopic()`-Methode eine Themenclientinstanz, die die Verbindungszeichenfolge und den Themennamen nutzt. Anschließend wird eine weitere Methode zum Senden der Nachrichten aufgerufen:

```java
public void sendMessagesToTopic() throws Exception, ServiceBusException {
    // Create client for the topic.
    TopicClient topicClient = new TopicClient(new ConnectionStringBuilder(ConnectionString, TopicName));

    // Create a message sender from the topic client.

    System.out.printf("\nSending orders to topic.\n");

    // Now we can start sending orders.
    CompletableFuture.allOf(
            SendOrders(topicClient,Store[0]),
            SendOrders(topicClient,Store[1]),
            SendOrders(topicClient,Store[2]),
            SendOrders(topicClient,Store[3]),
            SendOrders(topicClient,Store[4]),
            SendOrders(topicClient,Store[5]),
            SendOrders(topicClient,Store[6]),
            SendOrders(topicClient,Store[7]),
            SendOrders(topicClient,Store[8]),
            SendOrders(topicClient,Store[9])
    ).join();

    System.out.printf("\nAll messages sent.\n");
}

    public CompletableFuture<Void> SendOrders(TopicClient topicClient, String store) throws Exception {

        for(int i = 0;i<NrOfMessagesPerStore;i++) {
            Random r = new Random();
            final Item item = new Item(r.nextInt(5),r.nextInt(5),r.nextInt(5));
            IMessage message = new Message(GSON.toJson(item,Item.class).getBytes(UTF_8));
            // We always set the Sent to field
            message.setTo(store);
            final String StoreId = store;
            Double priceToString = item.getPrice();
            final String priceForPut = priceToString.toString();
            message.setProperties(new HashMap<String, String>() {{
                // Additionally we add a customer store field. In reality you would use sys.To or a customer property but not both.
                // This is just for demo purposes.
                put("StoreId", StoreId);
                // Adding more potential filter / rule and action able fields
                put("Price", priceForPut);
                put("Color", item.getColor());
                put("Category", item.getItemCategory());
            }});

            System.out.printf("Sent order to Store %s. Price=%f, Color=%s, Category=%s\n", StoreId, item.getPrice(), item.getColor(), item.getItemCategory());
            topicClient.sendAsync(message);
        }

        return new CompletableFuture().completedFuture(null);
    }
```

### <a name="receive-messages-from-the-individual-subscriptions"></a>Empfangen von Nachrichten von den einzelnen Abonnements

Die `receiveAllMessages()`-Methode ruft die `receiveAllMessageFromSubscription()`-Methode auf, die dann einen Abonnementclient pro Aufruf erstellt und Nachrichten von den einzelnen Abonnements empfängt:

```java
public void receiveAllMessages() throws Exception {
    System.out.printf("\nStart Receiving Messages.\n");

    CompletableFuture.allOf(
            receiveAllMessageFromSubscription(Subscriptions[0]),
            receiveAllMessageFromSubscription(Subscriptions[1]),
            receiveAllMessageFromSubscription(Subscriptions[2])
            ).join();
}

public CompletableFuture<Void> receiveAllMessageFromSubscription(String subscription) throws Exception {

    int receivedMessages = 0;

    // Create subscription client.
    IMessageReceiver subscriptionClient = ClientFactory.createMessageReceiverFromConnectionStringBuilder(new ConnectionStringBuilder(ConnectionString, TopicName+"/subscriptions/"+ subscription), ReceiveMode.PEEKLOCK);

    // Create a receiver from the subscription client and receive all messages.
    System.out.printf("\nReceiving messages from subscription %s.\n\n", subscription);

    while (true)
    {
        // This will make the connection wait for N seconds if new messages are available.
        // If no additional messages come we close the connection. This can also be used to realize long polling.
        // In case of long polling you would obviously set it more to e.g. 60 seconds.
        IMessage receivedMessage = subscriptionClient.receive(Duration.ofSeconds(1));
        if (receivedMessage != null)
        {
            if ( receivedMessage.getProperties() != null ) {
                System.out.printf("StoreId=%s\n", receivedMessage.getProperties().get("StoreId"));
                
                // Show the label modified by the rule action
                if(receivedMessage.getLabel() != null)
                    System.out.printf("Label=%s\n", receivedMessage.getLabel());
            }
            
            byte[] body = receivedMessage.getBody();
            Item theItem = GSON.fromJson(new String(body, UTF_8), Item.class);
            System.out.printf("Item data. Price=%f, Color=%s, Category=%s\n", theItem.getPrice(), theItem.getColor(), theItem.getItemCategory());
            
            subscriptionClient.complete(receivedMessage.getLockToken());
            receivedMessages++;
        }
        else
        {
            // No more messages to receive.
            subscriptionClient.close();
            break;
        }
    }
    System.out.printf("\nReceived %s messages from subscription %s.\n", receivedMessages, subscription);
    
    return new CompletableFuture().completedFuture(null);
}
```

> [!NOTE]
> Sie können Service Bus-Ressourcen mit dem [Service Bus-Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) verwalten. Mit dem Service Bus-Explorer können Benutzer eine Verbindung mit einem Service Bus-Namespace herstellen und Messagingentitäten auf einfache Weise verwalten. Das Tool stellt erweiterte Features wie Import-/Exportfunktionen oder Testmöglichkeiten für Themen, Warteschlangen, Abonnements, Relaydienste, Notification Hubs und Event Hubs zur Verfügung. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie mit der Azure CLI Ressourcen bereitgestellt und anschließend Nachrichten für ein Service Bus-Thema und die dazugehörigen Abonnements gesendet und empfangen. Es wurde Folgendes vermittelt:

> [!div class="checklist"]
> * Erstellen eines Service Bus-Themas und eines oder mehrerer Abonnements für das Thema mit dem Azure-Portal
> * Hinzufügen von Themenfiltern per .NET-Code
> * Erstellen von zwei Nachrichten mit unterschiedlichem Inhalt
> * Senden der Nachrichten und Überprüfen des Eingangs in den entsprechenden Abonnements
> * Empfangen von Nachrichten aus den Abonnements

Falls Sie sich weitere Beispiele für das Senden und Empfangen von Nachrichten ansehen möchten, sind die [Service Bus-Beispiele auf GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted) ein guter Einstieg.

Fahren Sie mit dem nächsten Tutorial fort, um weitere Informationen zur Verwendung der Veröffentlichen/Abonnieren-Funktionen von Service Bus zu erhalten.

> [!div class="nextstepaction"]
> [Aktualisieren des Bestands mit PowerShell und Themen/Abonnements](service-bus-tutorial-topics-subscriptions-portal.md)

[kostenloses Konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create

---
title: 'Schnellstart: Verwenden der Azure-Befehlszeilenschnittstelle zum Erstellen einer Service Bus-Warteschlange | Microsoft-Dokumentation'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mit der Azure-Befehlszeilenschnittstelle eine Service Bus-Warteschlange erstellen. Anschließend nutzen Sie eine Java-Beispielanwendung, um Nachrichten an die Warteschlange zu senden und Nachrichten von der Warteschlange zu empfangen.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.devlang: java
ms.topic: quickstart
ms.date: 04/10/2019
ms.author: spelluru
ms.openlocfilehash: e34599b12b8b0c487c6813038951b051d1eaf425
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66400260"
---
# <a name="quickstart-use-the-azure-cli-to-create-a-service-bus-queue"></a>Schnellstart: Erstellen einer Service Bus-Warteschlange mithilfe der Azure CLI
In dieser Schnellstartanleitung wird beschrieben, wie Sie Nachrichten mit Service Bus senden und empfangen, indem Sie die Azure CLI und die Service Bus-Java-Bibliothek verwenden. Falls Sie an technischeren Details interessiert sind, können Sie auch die [Beschreibung](#understand-the-sample-code) der wichtigen Elemente des Beispielcodes lesen.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Voraussetzungen
Falls Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto][free account] erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Anmelden bei Azure
Wenn Sie zum Starten von Cloud Shell auf die Schaltfläche **Ausprobieren** klicken, melden Sie sich mit Ihren Anmeldeinformationen bei Azure an. 

Wenn Sie Cloud Shell in Ihrem Webbrowser entweder direkt oder im Azure-Portal gestartet haben, wechseln Sie zu **Bash**, sofern **PowerShell** links oben in Cloud Shell angezeigt wird. 

## <a name="use-the-azure-cli-to-create-resources"></a>Verwenden der Azure CLI zum Erstellen von Ressourcen
Führen Sie in Cloud Shell an der Bash-Eingabeaufforderung die folgenden Befehle aus, um Service Bus-Ressourcen bereitzustellen. Achten Sie darauf, dass Sie alle Platzhalter durch die entsprechenden Werte ersetzen: Das Java-Beispielprogramm erwartet, dass der Warteschlangenname „BasicQueue“ lautet. Ändern Sie ihn daher nicht. Sie können Befehle nacheinander kopieren und einfügen, damit Sie die Werte ersetzen können, bevor Sie sie ausführen. 

```azurecli-interactive
# Create a resource group
resourceGroupName="myResourceGroup"

az group create --name $resourceGroupName --location eastus

# Create a Service Bus messaging namespace with a unique name
namespaceName=myNameSpace$RANDOM
az servicebus namespace create --resource-group $resourceGroupName --name $namespaceName --location eastus

# Create a Service Bus queue
az servicebus queue create --resource-group $resourceGroupName --namespace-name $namespaceName --name BasicQueue

# Get the connection string for the namespace
connectionString=$(az servicebus namespace authorization-rule keys list --resource-group $resourceGroupName --namespace-name $namespaceName --name RootManageSharedAccessKey --query primaryConnectionString --output tsv)
```

Kopieren Sie nach Ausführung des letzten Befehls die Verbindungszeichenfolge und den von Ihnen ausgewählten Warteschlangennamen an einen temporären Ort, z.B. den Editor. Sie benötigen diese Angaben im nächsten Schritt.

## <a name="send-and-receive-messages"></a>Senden und Empfangen von Nachrichten

Nachdem Sie den Namespace und die Warteschlange erstellt haben und Sie über die erforderlichen Anmeldeinformationen verfügen, sind Sie zum Senden und Empfangen von Nachrichten bereit. Sie können sich den Code in [diesem GitHub-Ordner mit Beispielen](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters) ansehen.

1. Klonen Sie das [Service Bus-GitHub-Repository](https://github.com/Azure/azure-service-bus/) auf Ihrem Computer, indem Sie den folgenden Befehl ausführen:

   ```bash
   git clone https://github.com/Azure/azure-service-bus.git
   ```

1. Ändern Sie das aktuelle Verzeichnis in den Ordner mit den Beispielen, indem Sie normale Schrägstriche als Pfadtrennzeichen verwenden:

   ```bash
   cd azure-service-bus/samples/Java/azure-servicebus/QueuesGettingStarted
   ```

1. Führen Sie den folgenden Befehl aus, um die Anwendung zu erstellen:
   
   ```bash
   mvn clean package -DskipTests
   ```

1. Führen Sie den folgenden Befehl aus, um das Programm auszuführen, und ersetzen Sie dabei die Verbindungszeichenfolge durch den zuvor kopierten Wert:

   ```bash
   java -jar ./target/queuesgettingstarted-1.0.0-jar-with-dependencies.jar -c "<SERVICE BUS NAMESPACE CONNECTION STRING>" 
   ```

1. Verfolgen Sie, wie zehn Nachrichten an die Warteschlange gesendet werden. Die Reihenfolge der Nachrichten wird nicht garantiert. Sie können aber sehen, dass die Nachrichten gesendet und dann bestätigt und empfangen werden, und Sie sehen die Nutzlastdaten:

    ```
    Message sending: Id = 0
    Message sending: Id = 1
    Message sending: Id = 2
    Message sending: Id = 3
    Message sending: Id = 4
    Message sending: Id = 5
    Message sending: Id = 6
    Message sending: Id = 7
    Message sending: Id = 8
    Message sending: Id = 9
            Message acknowledged: Id = 9
            Message acknowledged: Id = 3
                                    Message received:
                                                    MessageId = 9,
                                                    SequenceNumber = 54324670505156609,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:20.972Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:20.972Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Nikolaus, name = Kopernikus ]
    
            Message acknowledged: Id = 2
            Message acknowledged: Id = 5
            Message acknowledged: Id = 1
            Message acknowledged: Id = 8
            Message acknowledged: Id = 7
            Message acknowledged: Id = 0
            Message acknowledged: Id = 6
            Message acknowledged: Id = 4
                                    Message received:
                                                    MessageId = 3,
                                                    SequenceNumber = 58828270132527105,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:20.972Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:20.972Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Steven, name = Hawking ]
    
                                    Message received:
                                                    MessageId = 2,
                                                    SequenceNumber = 9288674231451649,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.012Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.012Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Marie, name = Curie ]
    
                                    Message received:
                                                    MessageId = 1,
                                                    SequenceNumber = 22799473113563137,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.025Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.025Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Werner, name = Heisenberg ]
    
                                    Message received:
                                                    MessageId = 8,
                                                    SequenceNumber = 67835469387268097,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.028Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.028Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Johannes, name = Kepler ]
    
                                    Message received:
                                                    MessageId = 7,
                                                    SequenceNumber = 4785074604081153,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.020Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.020Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Galileo, name = Galilei ]
    
                                    Message received:
                                                    MessageId = 5,
                                                    SequenceNumber = 13792273858822145,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.027Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.027Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Niels, name = Bohr ]
    
                                    Message received:
                                                    MessageId = 0,
                                                    SequenceNumber = 18295873486192641,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.021Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.021Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Albert, name = Einstein ]
    
                                    Message received:
                                                    MessageId = 6,
                                                    SequenceNumber = 281474976710657,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.019Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.019Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Michael, name = Faraday ]
    
                                    Message received:
                                                    MessageId = 4,
                                                    SequenceNumber = 63331869759897601,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:20.964Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:20.964Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Isaac, name = Newton ]
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Führen Sie in Azure Cloud Shell den folgenden Befehl aus, um die Ressourcengruppe, den Namespace und alle dazugehörigen Ressourcen zu entfernen:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="understand-the-sample-code"></a>Grundlagen des Beispielcodes

Dieser Abschnitt enthält weitere Details zu wichtigen Abschnitten des Beispielcodes. Sie finden den Code [hier](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters) im GitHub-Repository.

### <a name="get-connection-string"></a>Abrufen der Verbindungszeichenfolge

Die runApp-Methode liest den Wert der Verbindungszeichenfolge aus den Argumenten für das Programm. 

```java
public static void main(String[] args) {

    System.exit(runApp(args, (connectionString) -> {
        QueuesGettingStarted app = new QueuesGettingStarted();
        try {
            app.run(connectionString);
            return 0;
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 1;
        }
    }));
}

    static final String SB_SAMPLES_CONNECTIONSTRING = "SB_SAMPLES_CONNECTIONSTRING";

    public static int runApp(String[] args, Function<String, Integer> run) {
        try {

            String connectionString = null;

            // parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // get overrides from the environment
            String env = System.getenv(SB_SAMPLES_CONNECTIONSTRING);
            if (env != null) {
                connectionString = env;
            }

            if (connectionString == null) {
                HelpFormatter formatter = new HelpFormatter();
                formatter.printHelp("run jar with", "", options, "", true);
                return 2;
            }
            return run.apply(connectionString);
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 3;
        }
    }
```

### <a name="create-queue-clients-to-send-and-receive"></a>Erstellen von Warteschlangenclients zum Senden und Empfangen

Für das Senden und Empfangen von Nachrichten erstellt die `run()`-Methode Warteschlangenclient-Instanzen, die aus der Verbindungszeichenfolge und dem Warteschlangennamen erstellt werden. Mit diesem Code werden zwei Warteschlangenclients erstellt, und zwar einer für das Senden und einer für das Empfangen:

```java
public void run(String connectionString) throws Exception {

    // Create a QueueClient instance for receiving using the connection string builder
    // We set the receive mode to "PeekLock", meaning the message is delivered
    // under a lock and must be acknowledged ("completed") to be removed from the queue
    QueueClient receiveClient = new QueueClient(new ConnectionStringBuilder(connectionString, "BasicQueue"), ReceiveMode.PEEKLOCK);
    // We are using single thread executor as we are only processing one message at a time
    ExecutorService executorService = Executors.newSingleThreadExecutor();
    this.registerReceiver(receiveClient, executorService);

    // Create a QueueClient instance for sending and then asynchronously send messages.
    // Close the sender once the send operation is complete.
    QueueClient sendClient = new QueueClient(new ConnectionStringBuilder(connectionString, "BasicQueue"), ReceiveMode.PEEKLOCK);
    this.sendMessagesAsync(sendClient).thenRunAsync(() -> sendClient.closeAsync());

    // wait for ENTER or 10 seconds elapsing
    waitForEnter(10);

    // shut down receiver to close the receive loop
    receiveClient.close();
    executorService.shutdown();
}
``` 

### <a name="construct-and-send-messages"></a>Erstellen und Senden von Nachrichten

Die `sendMessagesAsync()`-Methode erstellt eine Gruppe von zehn Nachrichten und sendet diese asynchron mithilfe des Warteschlangenclients:

```java
CompletableFuture<Void> sendMessagesAsync(QueueClient sendClient) {
    List<HashMap<String, String>> data =
            GSON.fromJson(
                    "[" +
                            "{'name' = 'Einstein', 'firstName' = 'Albert'}," +
                            "{'name' = 'Heisenberg', 'firstName' = 'Werner'}," +
                            "{'name' = 'Curie', 'firstName' = 'Marie'}," +
                            "{'name' = 'Hawking', 'firstName' = 'Steven'}," +
                            "{'name' = 'Newton', 'firstName' = 'Isaac'}," +
                            "{'name' = 'Bohr', 'firstName' = 'Niels'}," +
                            "{'name' = 'Faraday', 'firstName' = 'Michael'}," +
                            "{'name' = 'Galilei', 'firstName' = 'Galileo'}," +
                            "{'name' = 'Kepler', 'firstName' = 'Johannes'}," +
                            "{'name' = 'Kopernikus', 'firstName' = 'Nikolaus'}" +
                            "]",
                    new TypeToken<List<HashMap<String, String>>>() {}.getType());

    List<CompletableFuture> tasks = new ArrayList<>();
    for (int i = 0; i < data.size(); i++) {
        final String messageId = Integer.toString(i);
        Message message = new Message(GSON.toJson(data.get(i), Map.class).getBytes(UTF_8));
        message.setContentType("application/json");
        message.setLabel("Scientist");
        message.setMessageId(messageId);
        message.setTimeToLive(Duration.ofMinutes(2));
        System.out.printf("\nMessage sending: Id = %s", message.getMessageId());
        tasks.add(
                sendClient.sendAsync(message).thenRunAsync(() -> {
                    System.out.printf("\n\tMessage acknowledged: Id = %s", message.getMessageId());
                }));
    }
    return CompletableFuture.allOf(tasks.toArray(new CompletableFuture<?>[tasks.size()]));
}
```

### <a name="receive-messages"></a>Empfangen von Nachrichten

Die `registerReceiver()`-Methode registriert den `RegisterMessageHandler`-Rückruf und legt außerdem einige Nachrichtenhandleroptionen fest:

```java
void registerReceiver(QueueClient queueClient, ExecutorService executorService) throws Exception {

    
    // register the RegisterMessageHandler callback with executor service
    queueClient.registerMessageHandler(new IMessageHandler() {
                                            // callback invoked when the message handler loop has obtained a message
                                            public CompletableFuture<Void> onMessageAsync(IMessage message) {
                                                // receives message is passed to callback
                                                if (message.getLabel() != null &&
                                                        message.getContentType() != null &&
                                                        message.getLabel().contentEquals("Scientist") &&
                                                        message.getContentType().contentEquals("application/json")) {

                                                    byte[] body = message.getBody();
                                                    Map scientist = GSON.fromJson(new String(body, UTF_8), Map.class);

                                                    System.out.printf(
                                                            "\n\t\t\t\tMessage received: \n\t\t\t\t\t\tMessageId = %s, \n\t\t\t\t\t\tSequenceNumber = %s, \n\t\t\t\t\t\tEnqueuedTimeUtc = %s," +
                                                                    "\n\t\t\t\t\t\tExpiresAtUtc = %s, \n\t\t\t\t\t\tContentType = \"%s\",  \n\t\t\t\t\t\tContent: [ firstName = %s, name = %s ]\n",
                                                            message.getMessageId(),
                                                            message.getSequenceNumber(),
                                                            message.getEnqueuedTimeUtc(),
                                                            message.getExpiresAtUtc(),
                                                            message.getContentType(),
                                                            scientist != null ? scientist.get("firstName") : "",
                                                            scientist != null ? scientist.get("name") : "");
                                                }
                                                return CompletableFuture.completedFuture(null);
                                            }

                                            // callback invoked when the message handler has an exception to report
                                            public void notifyException(Throwable throwable, ExceptionPhase exceptionPhase) {
                                                System.out.printf(exceptionPhase + "-" + throwable.getMessage());
                                            }
                                        },
            // 1 concurrent call, messages are auto-completed, auto-renew duration
            new MessageHandlerOptions(1, true, Duration.ofMinutes(1)),
            executorService);

}
```

> [!NOTE]
> Sie können Service Bus-Ressourcen mit dem [Service Bus-Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) verwalten. Mit dem Service Bus-Explorer können Benutzer eine Verbindung mit einem Service Bus-Namespace herstellen und Messagingentitäten auf einfache Weise verwalten. Das Tool stellt erweiterte Features wie Import-/Exportfunktionen oder Testmöglichkeiten für Themen, Warteschlangen, Abonnements, Relaydienste, Notification Hubs und Event Hubs zur Verfügung. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie einen Service Bus-Namespace und weitere erforderliche Ressourcen zum Senden und Empfangen von Nachrichten für eine Warteschlange erstellt. Weitere Informationen zum Schreiben von Code und Senden und Empfangen von Nachrichten finden Sie in den Tutorials im Abschnitt **Senden und Empfangen von Nachrichten**. 

> [!div class="nextstepaction"]
> [Senden und Empfangen von Nachrichten](service-bus-dotnet-get-started-with-queues.md)

[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create

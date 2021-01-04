---
title: Verwenden von Azure Service Bus-Warteschlangen mit Java
description: In diesem Tutorial erfahren Sie, wie Sie Java-Anwendungen erstellen, um Nachrichten an eine Azure Service Bus-Warteschlange zu senden und Antworten zu empfangen.
ms.devlang: Java
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 774f9e485c58712ea8648a148c92b6f9ef6a58c0
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452610"
---
# <a name="quickstart-use-azure-service-bus-queues-with-java-to-send-and-receive-messages"></a>Schnellstart: Senden und Empfangen von Nachrichten mithilfe von Azure Service Bus-Warteschlangen und Java

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
In diesem Tutorial erfahren Sie, wie Sie Java-Anwendungen erstellen, um Nachrichten an eine Azure Service Bus-Warteschlange zu senden und Antworten zu empfangen. 

> [!WARNING]
>  In dieser Schnellstartanleitung werden alte Pakete vom Typ „azure-servicebus“ verwendet. Eine Schnellstartanleitung mit dem aktuellen Paket „azure-messaging-servicebus“ finden Sie unter [Senden und Empfangen von Nachrichten für Azure Service Bus-Warteschlangen (Java)](service-bus-java-how-to-use-queues.md). 


## <a name="prerequisites"></a>Voraussetzungen
1. Ein Azure-Abonnement. Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Sie können Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF) registrieren.
2. Wenn Sie über keine Warteschlange verfügen, führen Sie die Schritte im Artikel [Schnellstart: Erstellen einer Service Bus-Warteschlange mithilfe des Azure-Portals](service-bus-quickstart-portal.md) aus, um eine Warteschlange zu erstellen.
    1. Lesen Sie die kurze **Übersicht** über Service Bus-**Warteschlangen**. 
    2. Erstellen Sie einen Service Bus-**Namespace**. 
    3. Rufen Sie die **Verbindungszeichenfolge** ab.
    4. Erstellen Sie eine Service Bus-**Warteschlange**.
3. Installieren Sie das [Azure SDK für Java][Azure SDK for Java]. 


## <a name="configure-your-application-to-use-service-bus"></a>Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus
Stellen Sie sicher, dass Sie das [Azure SDK für Java][Azure SDK for Java] vor dem Erstellen dieses Beispiels installiert haben. 

Wenn Sie Eclipse verwenden, können Sie das [Azure Toolkit für Eclipse][Azure Toolkit for Eclipse] installieren, das das Azure SDK für Java enthält. Sie können dann Ihrem Projekt die **Microsoft Azure-Bibliotheken für Java** hinzufügen. Lesen Sie bei Verwendung von IntelliJ die Informationen unter [Installieren des Azure-Toolkits für IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 

![Hinzufügen von Microsoft Azure-Bibliotheken für Java zu Ihrem Eclipse-Projekt](./media/service-bus-java-how-to-use-queues/eclipse-azure-libraries-java.png)


Fügen Sie die folgenden `import`-Anweisungen am Anfang der Java-Datei ein:

```java
// Include the following imports to use Service Bus APIs
import com.google.gson.reflect.TypeToken;
import com.microsoft.azure.servicebus.*;
import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import com.google.gson.Gson;

import static java.nio.charset.StandardCharsets.*;

import java.time.Duration;
import java.util.*;
import java.util.concurrent.*;

import org.apache.commons.cli.*;

```

## <a name="send-messages-to-a-queue"></a>Senden von Nachrichten an eine Warteschlange
Um Nachrichten an eine Service Bus-Warteschlange zu senden, instanziiert Ihre Anwendung ein **QueueClient**-Objekt und sendet Nachrichten asynchron. Der folgende Code zeigt, wie eine Nachricht für eine Warteschlange gesendet wird, die über das Portal erstellt wurde.

```java
public void run() throws Exception {
    // Create a QueueClient instance and then asynchronously send messages.
    // Close the sender once the send operation is complete.
    QueueClient sendClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
    this.sendMessageAsync(sendClient).thenRunAsync(() -> sendClient.closeAsync());

    sendClient.close();
}

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

Nachrichten, die an die Service Bus-Warteschlangen gesendet bzw. von diesen empfangen werden, sind Instanzen der [Message](/java/api/com.microsoft.azure.servicebus.message)-Klasse. Nachrichtenobjekte verfügen über einen Satz von Standardeigenschaften (z.B. „Label“ und „TimeToLive“), ein Wörterbuch, in dem benutzerdefinierte anwendungsspezifische Eigenschaften enthalten sind, sowie einen Textteil mit beliebigen Anwendungsdaten. Eine Anwendung kann den Text der Nachricht festlegen, indem ein beliebiges serialisierbares Objekt an den Konstruktor der Nachricht übergeben wird. Anschließend erfolgt die Serialisierung des Objekts mit dem entsprechenden Serialisierer. Alternativ können Sie ein **java.IO.InputStream**-Objekt bereitstellen.


Service Bus-Warteschlangen unterstützen eine maximale Nachrichtengröße von 256 KB für den [Standard-Tarif](service-bus-premium-messaging.md) und 1 MB für den [Premium-Tarif](service-bus-premium-messaging.md). Der Header, der die standardmäßigen und benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 KB haben. Bei der Anzahl der Nachrichten, die in einer Warteschlange aufgenommen werden können, besteht keine Beschränkung. Allerdings gilt eine Deckelung bei der Gesamtgröße der in einer Warteschlange aufzunehmenden Nachrichten. Die Warteschlangengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB.

## <a name="receive-messages-from-a-queue"></a>Empfangen von Nachrichten aus einer Warteschlange
Der einfachste Weg zum Empfangen von Nachrichten aus Warteschlangen sind **ServiceBusContract**-Objekte. Empfangene Nachrichten können in zwei unterschiedlichen Modi funktionieren: **ReceiveAndDelete** und **PeekLock**.

Bei Verwendung des **ReceiveAndDelete**-Modus ist der Nachrichtenempfang ein Single-Shot-Vorgang. Dies bedeutet, wenn Service Bus eine Leseanforderung für eine Nachricht in eine Warteschlange erhält, wird die Nachricht als verarbeitet gekennzeichnet und an die Anwendung zurückgesendet. Der **ReceiveAndDelete**-Modus (der Standardmodus) ist das einfachste Modell. Er wird am besten für Szenarios eingesetzt, bei denen es eine Anwendung tolerieren kann, wenn eine Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird.
Da die Nachricht von Service Bus als verarbeitet gekennzeichnet wurde, wird die vor dem Absturz verarbeitete Nachricht nicht berücksichtigt, wenn die Anwendung neu gestartet und die Verarbeitung von Nachrichten wieder aufgenommen wird.

Im **PeekLock**-Modus ist der Empfangsvorgang zweistufig. Dadurch können Anwendungen unterstützt werden, die das Auslassen bzw. Fehlen von Nachrichten nicht zulassen können. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann zurück an die Anwendung. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt sie die zweite Phase des Empfangsprozesses per Aufruf von **complete()** für die empfangene Nachricht durch. Wenn Service Bus den **complete()** -Aufruf registriert, wird die Nachricht als verarbeitet markiert und aus der Warteschlange entfernt. 

Das folgende Beispiel zeigt, wie Nachrichten mit dem Modus **PeekLock** (nicht der Standardmodus) empfangen und verarbeitet werden können. Das folgende Beispiel verwendet das Rückrufmodell mit einem registrierten Nachrichtenhandler und verarbeitet Nachrichten bei ihrem Eingang in `TestQueue`. In diesem Modus wird **complete()** automatisch aufgerufen, wenn der Rückruf einen normalen Rückgabewert liefert. **abandon()** wird aufgerufen, wenn der Rückruf eine Ausnahme auslöst. 

```java
    public void run() throws Exception {
        // Create a QueueClient instance for receiving using the connection string builder
        // We set the receive mode to "PeekLock", meaning the message is delivered
        // under a lock and must be acknowledged ("completed") to be removed from the queue
        QueueClient receiveClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
        this.registerReceiver(receiveClient);

        // shut down receiver to close the receive loop
        receiveClient.close();
    }
    void registerReceiver(QueueClient queueClient) throws Exception {
        // register the RegisterMessageHandler callback
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
        new MessageHandlerOptions(1, true, Duration.ofMinutes(1)));
    }

```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Behandeln von Anwendungsabstürzen und nicht lesbaren Nachrichten
Service Bus stellt Funktionen zur Verfügung, die Sie bei der ordnungsgemäßen Behandlung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht unterstützen. Wenn eine Empfängeranwendung die Nachricht aus einem beliebigen Grund nicht verarbeiten kann, kann sie die **abandon()** -Methode für das Clientobjekt mit dem (über **getLockToken()** abgerufenen) Sperrtoken der empfangenen Nachricht aufrufen. Dies führt dazu, dass Service Bus die Nachricht innerhalb der Warteschlange entsperrt und verfügbar macht, damit sie erneut empfangen werden kann, und zwar entweder durch dieselbe verarbeitende Anwendung oder durch eine andere verarbeitende Anwendung.

Mit einer innerhalb der Warteschlange gesperrten Nachricht ist außerdem eine Zeitlimitüberschreitung verknüpft. Wenn die Anwendung die Nachricht nicht verarbeiten kann, bevor die Zeitlimitüberschreitung der Sperre abläuft (z. B. falls die Anwendung abstürzt), so entsperrt Service Bus die Nachricht automatisch und macht sie verfügbar, sodass sie wieder empfangen werden kann.

Falls die Anwendung nach der Verarbeitung der Nachricht, jedoch vor Ausgabe der **complete()** -Anforderung abstürzt, wird die Nachricht der Anwendung bei ihrem Neustart erneut zugestellt. Dies wird häufig als *At Least Once Processing*(Verarbeitung mindestens einmal) bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise erneut zugestellt wird. Wenn eine doppelte Verarbeitung im betreffenden Szenario nicht geeignet ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Übermittlung doppelter Nachrichten hinzufügen. Dies wird häufig durch die Verwendung der Methode **getMessageId** der Nachricht erzielt, die über mehrere Zustellversuche hinweg konstant bleibt.

> [!NOTE]
> Sie können Service Bus-Ressourcen mit dem [Service Bus-Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) verwalten. Mit dem Service Bus-Explorer können Benutzer eine Verbindung mit einem Service Bus-Namespace herstellen und Messagingentitäten auf einfache Weise verwalten. Das Tool stellt erweiterte Features wie Import-/Exportfunktionen oder Testmöglichkeiten für Themen, Warteschlangen, Abonnements, Relaydienste, Notification Hubs und Event Hubs zur Verfügung. 

## <a name="next-steps"></a>Nächste Schritte
Java-Beispiele finden Sie auf GitHub im [azure-service-bus-Repository](https://github.com/Azure/azure-service-bus/tree/master/samples/Java).


[Azure SDK for Java]: /azure/developer/java/sdk/java-sdk-azure-get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
---
title: Verwenden von AMQP 1.0 mit der Java JMS Service Bus-API | Microsoft Docs
description: Erfahren Sie, wie Sie den Java Message Service (JMS) mit Azure Service Bus und Advanced Message Queuing Protocol (AMQP) 1.0 verwenden.
services: service-bus-messaging
documentationcenter: java
author: axisc
manager: timlt
editor: spelluru
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 03/05/2019
ms.author: aschhab
ms.openlocfilehash: a7e4282a176794fe885049173ba56ce2461cd6fa
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60310960"
---
# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>Verwenden der JMS (Java Message Service)-API mit Service Bus und AMQP 1.0
AMQP (Advanced Message Queuing Protocol) 1.0 ist ein effizientes, zuverlässiges Messagingprotokoll auf Wire-Ebene, mit dem Sie robuste und plattformübergreifende Messaginganwendungen erstellen können.

Unterstützung für AMQP 1.0 im Service Bus bedeutet, dass Sie die gebrokerten Messagingfunktionen für Warteschlangen und Veröffentlichen/Abonnieren mithilfe eines effizienten binären Protokolls auf unterschiedlichen Plattformen nutzen können. Zudem können Sie Anwendungen erstellen, deren Komponenten mit einer Mischung aus Sprachen, Frameworks und Betriebssystemen erstellt wurden.

In diesem Artikel wird beschrieben, wie die Messagingfunktionen von Service Bus (Warteschlange und Veröffentlichen/Abonnieren von Themen) in Java-Anwendungen mit dem beliebten API-Standard Java Message Service (JMS) verwendet werden. In einer [separaten Anleitung](service-bus-amqp-dotnet.md) wird erklärt, wie Sie dieselbe Aufgabe mithilfe der .NET-API für Service Bus durchführen. Sie können diese beiden Anleitungen verwenden, um weitere Informationen zur plattformübergreifenden Nachrichtenübermittlung unter Verwendung von AMQP 1.0 zu erhalten.

## <a name="get-started-with-service-bus"></a>Erste Schritte mit Service Bus
In diesem Leitfaden wird davon ausgegangen, dass Sie bereits über einen Service Bus-Namespace verfügen, der eine Warteschlange mit dem Namen **basicqueue** enthält. Falls nicht, können Sie [den Namespace und die Warteschlange](service-bus-create-namespace-portal.md) im [Azure-Portal](https://portal.azure.com) erstellen. Weitere Informationen zum Erstellen von Namespaces und Warteschlangen für Service Bus finden Sie unter [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Partitionierte Warteschlangen und Themen unterstützen zudem AMQP. Weitere Informationen finden Sie unter [Partitionierte Messagingentitäten](service-bus-partitioning.md) und [AMQP 1.0-Unterstützung für partitionierte Warteschlangen und Themen von Service Bus](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Herunterladen der AMQP 1.0 JMS-Clientbibliothek
Informationen zum Herunterladen der neuesten Version der Apache Qpid JMS AMQP 1.0-Clientbibliothek finden Sie unter [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html).

Folgende vier JAR-Dateien müssen aus dem Apache Qpid JMS AMQP 1.0-Verteilungsarchiv zu dem Java-KLASSENPFAD hinzugefügt werden, wenn JMS-Anwendungen mit Service Bus erstellt und ausgeführt werden:

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-jms-client-[version].jar

> [!NOTE]
> JMS-JAR-Namen und -Versionen haben sich möglicherweise geändert. Ausführliche Informationen finden Sie unter [Qpid JMS (AMQP 1.0)](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="coding-java-applications"></a>Programmieren von Java-Anwendungen
### <a name="java-naming-and-directory-interface-jndi"></a>JNDI (Java Naming and Directory Interface; Java Benennungs- und Verzeichnisschnittstelle)
JMS verwendet die Java Naming and Directory Interface (JNDI), um eine Trennung zwischen logischen und physischen Namen umzusetzen. Mit JNDI werden zwei Arten von JMS-Objekten aufgelöst: „ConnectionFactory“ und „Destination“. JNDI verwendet ein Anbietermodell, das Sie mit verschiedenen Verzeichnisdiensten verbinden können, um Namensauflösungsfunktionen zu implementieren. Die Apache Qpid JMS AMQP 1.0-Bibliothek enthält einen einfachen JNDI-Anbieter, der mithilfe von properties-Dateien im folgenden Format konfiguriert wird:

```
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="setup-jndi-context-and-configure-the-connectionfactory"></a>Einrichten des JNDI-Kontexts und Konfigurieren der ConnectionFactory

Die Verbindungszeichenfolge (**ConnectionString**), auf die verwiesen wird, ist in den freigegebenen Zugriffsrichtlinien im [Azure-Portal](https://portal.azure.com) unter **Primäre Verbindungszeichenfolge** angegeben.
```java
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

// Look up queue
Destination queue = (Destination) context.lookup("QUEUE");
```

#### <a name="configure-producer-and-consumer-destination-queues"></a>Konfigurieren von Zielwarteschlangen für Producer und Consumer
Der Eintrag zum Definieren eines Ziels in der Qpid Properties-Datei für den JNDI-Anbieter hat das folgende Format:

So erstellen Sie die Zielwarteschlange für den Producer: 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Producer
MessageProducer producer = session.createProducer(queue);
```

So erstellen Sie eine Zielwarteschlange für den Consumer: 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>Schreiben der JMS-Anwendung
Für die Verwendung von JMS mit Service Bus werden keine speziellen Programmierschnittstellen oder Optionen benötigt. Es gibt jedoch einige Einschränkungen, die an späterer Stelle erläutert werden. Wie bei allen JMS-Anwendungen müssen Sie zuerst die JNDI-Umgebung konfigurieren, um eine **ConnectionFactory** und Ziele auflösen zu können.

#### <a name="configure-the-jndi-initialcontext"></a>Konfigurieren des JNDI InitialContext
Zur Konfiguration der JNDI-Umgebung wird eine Hashtabelle mit Konfigurationsinformationen an den Konstruktor der Klasse javax.naming.InitialContext übergeben. Obligatorisch sind in der Hashtabelle zwei Elemente: der Klassenname der Initial Context Factory und die Anbieter-URL. Der folgende Code zeigt, wie Sie die JNDI-Umgebung mithilfe des auf Eigenschaftendateien basierenden JNDI-Anbieters Qpid mit der Eigenschaftendatei **servicebus.properties** konfigurieren.

```java
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Einfache JMS-Anwendung mit einer Service Bus-Warteschlange
Das folgende Beispielprogramm sendet JMS-TextMessages an eine Service Bus-Warteschlange mit dem logischen JNDI-Namen QUEUE und empfängt die zurückkommenden Nachrichten.

Den gesamten Quellcode sowie Konfigurationsinformationen finden Sie in den [Azure Service Bus-Beispielen der Schnellstartanleitung zu JMS-Warteschlangen](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart).

```java
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE file in the project root for full license information.

package com.microsoft.azure.servicebus.samples.jmsqueuequickstart;

import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import org.apache.commons.cli.*;
import org.apache.log4j.*;

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.util.Hashtable;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.function.Function;

/**
 * This sample demonstrates how to send messages from a JMS Queue producer into
 * an Azure Service Bus Queue, and receive them with a JMS message consumer.
 * JMS Queue. 
 */
public class JmsQueueQuickstart {

    // Number of messages to send
    private static int totalSend = 10;
    //Tracking counter for how many messages have been received; used as termination condition
    private static AtomicInteger totalReceived = new AtomicInteger(0);
    // log4j logger 
    private static Logger logger = Logger.getRootLogger();

    public void run(String connectionString) throws Exception {

        // The connection string builder is the only part of the azure-servicebus SDK library
        // we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
        // connection string. 
        ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
        // set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // we create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter
        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

            // Create producer
            MessageProducer producer = session.createProducer(queue);

            // Send messages
            for (int i = 0; i < totalSend; i++) {
                BytesMessage message = session.createBytesMessage();
                message.writeBytes(String.valueOf(i).getBytes());
                producer.send(message);
                System.out.printf("Sent message %d.\n", i + 1);
            }

            producer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            connection.start();
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // receives message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // wait on the main thread until all sent messages have been received
            while (totalReceived.get() < totalSend) {
                Thread.sleep(1000);
            }
            consumer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        System.out.printf("Received all messages, exiting the sample.\n");
        System.out.printf("Closing queue client.\n");
    }

    public static void main(String[] args) {

        System.exit(runApp(args, (connectionString) -> {
            JmsQueueQuickstart app = new JmsQueueQuickstart();
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
}
```

### <a name="run-the-application"></a>Ausführen der Anwendung
Übergeben Sie die **Verbindungszeichenfolge** aus den freigegebenen Zugriffsrichtlinien, um die Anwendung auszuführen.
Hier sehen Sie die Ausgabe des Formulars:

```
> mvn clean package
>java -jar ./target/jmsqueuequickstart-1.0.0-jar-with-dependencies.jar -c "<CONNECTION_STRING>"

Sent message 1.
Sent message 2.
Sent message 3.
Sent message 4.
Sent message 5.
Sent message 6.
Sent message 7.
Sent message 8.
Sent message 9.
Sent message 10.
Received message 1 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-1
Received message 2 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-2
Received message 3 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-3
Received message 4 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-4
Received message 5 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-5
Received message 6 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-6
Received message 7 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-7
Received message 8 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-8
Received message 9 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-9
Received message 10 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-10
Received all messages, exiting the sample.
Closing queue client.

```

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>AMQP-Disposition und Service Bus-Vorgangszuordnung
Hier sehen Sie die Umwandlung einer AMQP-Disposition in einen Service Bus-Vorgang:

```
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>JMS-Themen im Vergleich zu Service Bus-Themen
Ein Verwenden von Azure Service Bus-Themen und -Abonnements über die JMS-API (Java Message Service) bietet grundlegende Sende- und Empfangsfunktionen. Dies ist eine praktische Wahl, wenn es darum geht, Anwendungen von anderen Nachrichtenbrokern mit JMS-konformen APIs zu portieren, obwohl die Service Bus-Themen sich von den JMS-Themen unterscheiden und einige Anpassungen erfordern. 

Azure Service Bus-Themen leiten Nachrichten in benannte, freigegebene, dauerhafte Abonnements weiter, die über die Azure-Benutzeroberfläche zur Ressourcenverwaltung, die Azure-Befehlszeilentools oder das Azure-Portal verwaltet werden. Jedes Abonnement kann bis zu 2000 Auswahlregeln haben, wobei jede Regel eine Filterbedingung und für SQL-Filter auch eine Metadatentransformationsaktion haben kann. Für jede Übereinstimmung einer Filterbedingung wird die Eingabenachricht ausgewählt, die in das Abonnement kopiert werden soll.  

Ein Empfangen von Nachrichten aus Abonnements ist identisch mit einem Empfangen von Nachrichten aus Warteschlangen. Jedes Abonnement hat eine Warteschlange für unzustellbare Nachrichten sowie die Möglichkeit, Nachrichten automatisch an eine andere Warteschlange oder andere Themen weiterzuleiten. 

JMS-Themen ermöglichen es Clients, dynamisch nicht dauerhafte und dauerhafte Abonnenten zu erstellen, die optional ein Filtern von Nachrichten mit Nachrichtenselektoren ermöglichen. Diese nicht freigegebenen Elemente werden von Service Bus nicht unterstützt. Die Syntax von SQL-Filterregeln für Service Bus ist aber der von JMS unterstützten Nachrichtenselektorsyntax sehr ähnlich. 

Die Verlegerseite eines JMS-Themas ist mit Service Bus kompatibel, wie in diesem Beispiel gezeigt, dynamische Abonnenten sind dies aber nicht. Die folgenden topologiebezogenen JMS-APIs werden mit Service Bus nicht unterstützt. 

## <a name="unsupported-features-and-restrictions"></a>Nicht unterstützte Funktionen und Einschränkungen
Bei der Verwendung von JMS über AMQP 1.0 mit Service Bus gelten die folgenden Einschränkungen:

* Pro **Sitzung** ist nur ein **MessageConsumer** oder **MessageProducer** erlaubt. Falls Sie mehrere **MessageProducer** oder **MessageConsumer** in einer Anwendung benötigen, müssen Sie für diese jeweils eine eigene **Sitzung** erstellen.
* Flüchtige Themenabonnements werden momentan nicht unterstützt.
* **MessageSelectors** werden momentan nicht unterstützt.
* Durchgeführte Sitzungen und verteilte Transaktionen werden nicht unterstützt.

Darüber hinaus trennt Azure Service Bus die Steuerungsebene von der Datenebene und unterstützt daher viele der dynamischen Topologiefunktionen von JMS nicht:

| Nicht unterstützte Methode          | Ersetzen durch                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | Erstellen Sie ein Themenabonnement, das den Nachrichtenselektor portiert.                                 |
| createDurableConsumer       | Erstellen Sie ein Themenabonnement, das den Nachrichtenselektor portiert.                                 |
| createSharedConsumer        | Service Bus-Themen können immer freigegeben werden (siehe oben).                                       |
| createSharedDurableConsumer | Service Bus-Themen können immer freigegeben werden (siehe oben).                                       |
| createTemporaryTopic        | Erstellen Sie per Verwaltungs-API/-tools/-portal ein Thema, bei dem *AutoDeleteOnIdle* auf einen Ablaufzeitraum festgelegt ist. |
| createTopic                 | Erstellen Sie ein Thema per Verwaltungs-API/-tools/-portal.                                           |
| unsubscribe                 | Löschen Sie das Thema per Verwaltungs-API/-tools/-portal.                                             |
| createBrowser               | Nicht unterstützt. Verwenden Sie die Peek()-Funktion der Service Bus-API.                         |
| createQueue                 | Erstellen Sie eine Warteschlange per Verwaltungs-API/-tools/-portal.                                           | 
| createTemporaryQueue        | Erstellen Sie per Verwaltungs-API/-tools/-portal eine Warteschlange, bei der *AutoDeleteOnIdle* auf einen Ablaufzeitraum festgelegt ist. |

## <a name="summary"></a>Zusammenfassung
In diesem Leitfaden wurde gezeigt, wie die gebrokerten Messagingfunktionen von Service Bus (Warteschlange und Themen veröffentlichen/abonnieren) aus Java-Anwendungen mit der beliebten Standard-Programmierschnittstelle JMS und AMQP 1.0 verwendet werden.

Sie können Service Bus AMQP 1.0 auch mit anderen Sprachen verwenden, unter anderem .NET, C, Python und PHP. Komponenten, die mit diesen verschiedenen Sprachen geschrieben wurden, können mit der AMQP 1.0-Unterstützung in Service Bus Nachrichten zuverlässig und bei voller Vertraulichkeit austauschen.

## <a name="next-steps"></a>Nächste Schritte
* [AMQP 1.0-Unterstützung in Azure Service Bus](service-bus-amqp-overview.md)
* [Gewusst wie: Verwenden von AMQP 1.0 mit der .NET-API für Service Bus](service-bus-dotnet-advanced-message-queuing.md)
* [Entwicklerhandbuch für Service Bus AMQP 1.0](service-bus-amqp-dotnet.md)
* [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md)
* [Java Developer Center](https://azure.microsoft.com/develop/java/)


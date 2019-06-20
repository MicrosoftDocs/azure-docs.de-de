---
title: Verarbeiten von Apache Kafka-Ereignissen mithilfe von Stream Analytics – Azure Event Hubs | Microsoft-Dokumentation
description: Dieser Artikel zeigt, wie Kafka-Ereignisse verarbeitet werden, die mithilfe von Azure Stream Analytics über Event Hubs erfasst werden.
services: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: spelluru
ms.openlocfilehash: b6bba4ed45530ba66a1adde274022a80091cd199
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60747992"
---
# <a name="process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Verarbeiten von Apache Kafka für Event Hubs-Ereignisse mithilfe von Stream Analytics 
Dieser Artikel zeigt, wie Daten in Kafka-fähige Event Hubs gestreamt und mit Azure Stream Analytics verarbeitet werden können. Die folgenden Schritte werden behandelt: 

1. Erstellen eines Kafka-fähigen Event Hubs-Namespace
2. Erstellen eines Kafka-Clients, der Nachrichten an den Event Hub sendet
3. Erstellen eines Stream Analytics-Auftrags, der Daten aus dem Event Hub in Azure Blob Storage kopiert 

Sie müssen Ihre Protokollclients nicht ändern oder eigene Cluster betreiben, wenn Sie den Kafka-Endpunkt verwenden, der von einem Event Hub verfügbar gemacht wird. Azure Event Hubs unterstützt [Apache Kafka Version 1.0](https://kafka.apache.org/10/documentation.html). und höher. 


## <a name="prerequisites"></a>Voraussetzungen

Für diese Schnellstartanleitung benötigen Sie Folgendes:

* Ein Azure-Abonnement. Wenn Sie keins besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erstellen, bevor Sie beginnen.
* [Java Development Kit (JDK) 1.7 oder höher](https://aka.ms/azure-jdks)
* Ein binäres Maven-Archiv ([Download](https://maven.apache.org/download.cgi)/[Installationsanleitung](https://maven.apache.org/install.html))
* [Git-Client](https://www.git-scm.com/)
* Ein **Azure Storage-Konto** Wenn keine öffentliche IP-Adresse vorhanden ist, [erstellen Sie jetzt eine](../storage/common/storage-quickstart-create-account.md), ehe Sie fortfahren. Der Stream Analytics-Auftrag in dieser exemplarischen Vorgehensweise speichert die Ausgabedaten in Azure Blob Storage. 


## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Erstellen eines Kafka-fähigen Event Hubs-Namespace

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und klicken Sie links oben auf **Ressource erstellen**.
2. Suchen Sie nach **Event Hubs**, und wählen Sie die hier gezeigten Optionen aus:
    
    ![Suchen nach Event Hubs im Portal](./media/event-hubs-kafka-stream-analytics/select-event-hubs.png) 
3. Klicken Sie auf der Seite **Event Hubs** auf **Erstellen**.
4. Gehen Sie auf der Seite **Namespace erstellen** wie folgt vor: 
    1. Geben Sie einen eindeutigen **Namen** für den Namespace an. 
    2. Wählen Sie einen **Tarif** aus. 
    3. Wählen Sie **Kafka aktivieren** aus. Dies ist ein **wichtiger** Schritt. 
    4. Wählen Sie das **Azure-Abonnement** aus, in dem der Event Hub-Namespace erstellt werden soll. 
    5. Erstellen Sie eine neue **Ressourcengruppe**, oder wählen Sie eine vorhandene Ressourcengruppe aus. 
    6. Wählen Sie einen **Standort** aus. 
    7. Klicken Sie auf **Create**.
    
        ![Erstellen eines Namespace](./media/event-hubs-kafka-stream-analytics/create-event-hub-namespace-page.png) 
4. Wählen Sie in der **Benachrichtigungsmeldung** den Namen der **Ressourcengruppe** aus. 

    ![Erstellen eines Namespace](./media/event-hubs-kafka-stream-analytics/creation-station-message.png)
1. Wählen Sie den **Event Hub-Namespace** in der Ressourcengruppe aus. 
2. Wählen nach dem Erstellen des Namespace unter **EINSTELLUNGEN** die Option **Freigegebene Zugriffsrichtlinien** aus.

    ![Klicken auf „SAS-Richtlinien“](./media/event-hubs-kafka-stream-analytics/shared-access-policies.png)
5. Sie können die Standardrichtlinie **RootManageSharedAccessKey** auswählen oder eine neue Richtlinie hinzufügen. Klicken Sie auf den Richtliniennamen, und kopieren Sie die **Verbindungszeichenfolge**. Sie verwenden die Verbindungszeichenfolge, um den Kafka-Client zu konfigurieren. 
    
    ![Auswählen einer Richtlinie](./media/event-hubs-kafka-stream-analytics/connection-string.png)  

Nun können Sie Ereignisse von Ihren Anwendungen streamen, die das Kafka-Protokoll in Event Hubs verwenden.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Senden von Nachrichten mit Kafka in Event Hubs

1. Klonen Sie das [Azure Event Hubs für Kafka-Repository](https://github.com/Azure/azure-event-hubs-for-kafka) auf Ihrem Computer.
2. Navigieren Sie zum Ordner `azure-event-hubs-for-kafka/quickstart/java/producer`. 
4. Aktualisieren Sie die Konfigurationsdetails für die Producer in `src/main/resources/producer.config`. Geben Sie den **Namen** und die **Verbindungszeichenfolge** des **Event Hub-Namespace** an. 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Navigieren Sie zu `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/com/example/app`, und öffnen Sie die Datei **TestDataReporter.java** in einem Editor Ihrer Wahl. 
6. Kommentieren Sie die folgenden Codezeilen aus:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Fügen Sie anstelle des auskommentierten Codes die folgende Codezeile hinzu: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Dieser Code sendet die Ereignisdaten im **JSON**-Format. Wenn Sie die Eingabe für einen Stream Analytics-Auftrag konfigurieren, geben Sie JSON als Format für die Eingabedaten an. 
7. **Führen Sie den Producer aus**, und streamen Sie Daten an Kafka-fähige Event Hubs: Wechseln Sie auf einem Windows-Computer bei Verwendung einer **Node.js-Eingabeaufforderung** zum Ordner `azure-event-hubs-for-kafka/quickstart/java/producer`, bevor Sie diese Befehle ausführen. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Sicherstellen, dass der Event Hub die Daten empfängt

1. Wählen Sie unter **ENTITÄTEN** die Option **Event Hubs** aus. Bestätigen Sie, dass ein Event Hub mit dem Namen **test** angezeigt wird. 

    ![Event Hub „test“](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Bestätigen Sie, dass im Event Hub Nachrichten eingehen. 

    ![Event Hub-Nachrichten](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Verarbeiten von Ereignisdaten mithilfe eines Stream Analytics-Auftrags
In diesem Abschnitt erstellen Sie einen Azure Stream Analytics-Auftrag. Der Kafka-Client sendet Ereignisse an den Event Hub. Sie erstellen einen Stream Analytics-Auftrag, der Ereignisdaten als Eingabe verwendet und diese in Azure Blob Storage ausgibt. Wenn Sie kein **Azure Storage-Konto** haben, müssen Sie eines [erstellen](../storage/common/storage-quickstart-create-account.md).

Die Abfrage im Stream Analytics-Auftrag durchläuft die Daten, ohne eine Analyse vorzunehmen. Sie können eine Abfrage erstellen, die die Eingabedaten transformiert, um Ausgabedaten in einem anderen Format oder mit gewonnenen Erkenntnissen zu erzeugen.  

### <a name="create-a-stream-analytics-job"></a>Erstellen eines Stream Analytics-Auftrags 

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **+ Ressource erstellen**.
2. Wählen Sie **Analytics** im Menü **Azure Marketplace** und dann **Stream Analytics-Auftrag** aus. 
3. Gehen Sie auf der Seite **Neuer Stream Analytics-Auftrag** wie folgt vor: 
    1. Geben Sie einen **Namen** für den Auftrag ein. 
    2. Wählen Sie Ihr **Abonnement**aus.
    3. Klicken Sie für die **Ressourcengruppe** auf **Neu erstellen**, und geben Sie den Namen ein. Sie können auch eine **vorhandene** Ressourcengruppe verwenden. 
    4. Wählen Sie einen **Speicherort** für den Auftrag aus.
    5. Klicken Sie auf **Erstellen**, um den Auftrag zu erstellen. 

        ![Neuer Stream Analytics-Auftrag](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Konfigurieren einer Auftragseingabe

1. Wählen Sie in der Benachrichtigungsmeldung **Zur Ressource wechseln** aus, um die Seite **Stream Analytics-Auftrag** einzublenden. 
2. Wählen Sie im Abschnitt **AUFTRAGSTOPOLOGIE** im linken Menü **Eingaben** aus.
3. Klicken Sie auf **Datenstromeingabe hinzufügen**, und wählen Sie **Event Hub** aus. 

    ![Hinzufügen eines Event Hubs als Eingabe](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Führen Sie auf der Konfigurationsseite **Event Hub-Eingabe** die folgenden Schritte aus: 

    1. Geben Sie einen **Alias** für die Eingabe an. 
    2. Wählen Sie Ihr Azure-**Abonnement** aus.
    3. Wählen Sie den **Event Hub-Namespace** aus, den Sie zuvor erstellt haben. 
    4. Wählen Sie **test** als **Event Hub** aus. 
    5. Wählen Sie **Speichern** aus. 

        ![Event Hub-Eingabekonfiguration](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Konfigurieren der Auftragsausgabe 

1. Wählen Sie im Abschnitt **AUFTRAGSTOPOLOGIE** im linken Menü **Ausgaben** aus. 
2. Klicken Sie auf der Symbolleiste auf **+ Hinzufügen**, und wählen Sie **Blob Storage** aus.
3. Gehen Sie auf der Seite mit den Blob Storage-Ausgabeeinstellungen folgendermaßen vor: 
    1. Geben Sie einen **Alias** für die Ausgabe an. 
    2. Wählen Sie Ihr Azure- **Abonnement**aus. 
    3. Wählen Sie Ihr **Azure Storage-Konto** aus. 
    4. Geben Sie einen **Namen für den Container** ein, in dem die Ausgabedaten der Stream Analytics-Abfrage gespeichert werden.
    5. Wählen Sie **Speichern** aus.

        ![Blob Storage-Ausgabekonfiguration](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Definieren einer Abfrage
Nachdem Sie einen Stream Analytics-Auftrag eingerichtet haben, um einen eingehenden Datenstrom zu lesen, ist der nächste Schritt die Erstellung einer Transformation, bei der Daten in Echtzeit analysiert werden. Sie definieren die Transformationsabfrage mit der [Stream Analytics-Abfragesprache](https://msdn.microsoft.com/library/dn834998.aspx). In dieser exemplarischen Vorgehensweise definieren Sie eine Abfrage, die die Daten ohne jegliche Transformation durchläuft.

1. Wählen Sie **Abfrage**.
2. Ersetzen Sie im Abfragefenster `[YourOutputAlias]` durch den Ausgabealias, den Sie zuvor erstellt haben.
3. Ersetzen Sie `[YourInputAlias]` durch den Eingabealias, den Sie zuvor erstellt haben. 
4. Wählen Sie auf der Symbolleiste **Speichern** aus. 

    ![Abfragen](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Ausführen des Stream Analytics-Auftrags

1. Klicken Sie im linken Menü auf **Übersicht**. 
2. Wählen Sie **Starten** aus. 

    ![Startmenü](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. Klicken Sie auf der Seite **Auftrag starten** auf **Jetzt**. 

    ![Seite „Auftrag starten“](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Warten Sie, bis sich der Status des Auftrags von **Wird gestartet** in **Wird ausgeführt** ändert. 

    ![Auftragsstatus: Wird ausgeführt](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>Prüfen des Szenarios
1. Führen Sie die **Kafka Producer** erneut aus, um Ereignisse an den Event Hub zu senden. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Überprüfen Sie, ob **Ausgabedaten** in **Azure Blob Storage** generiert werden. Sie sehen im Container eine JSON-Datei mit 100 Zeilen, die wie die folgenden Beispielzeilen aussehen: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    Der Azure Stream Analytics-Auftrag hat in diesem Szenario Eingabedaten vom Event Hub empfangen und diese in Azure Blob Storage gespeichert. 



## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie erfahren, wie Daten an Kafka-fähige Event Hubs gestreamt werden können, ohne dass Sie Protokollclients ändern oder eigene Cluster betreiben müssen. Weitere Informationen zu Event Hubs und Event Hubs für Kafka finden Sie unter folgendem Thema:  

- [Informationen zu Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs für Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Erstellen von Kafka-fähigen Event Hubs](event-hubs-create-kafka-enabled.md)
- [Streamen von Ereignissen in Event Hubs aus Kafka-Anwendungen](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Spiegeln eines Kafka-Brokers in einem Kafka-fähigen Event Hub](event-hubs-kafka-mirror-maker-tutorial.md)
- [Verbinden von Apache Spark mit einem Kafka-fähigen Event Hub](event-hubs-kafka-spark-tutorial.md)
- [Verbinden von Apache Flink mit einem Kafka-fähigen Event Hub](event-hubs-kafka-flink-tutorial.md)
- [Integrieren von Kafka Connect in einen Kafka-fähigen Event Hub](event-hubs-kafka-connect-tutorial.md)
- [Verbinden von Akka Streams mit einem Kafka-fähigen Event Hub](event-hubs-kafka-akka-streams-tutorial.md)
- [Erkunden von Beispielen auf GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) 

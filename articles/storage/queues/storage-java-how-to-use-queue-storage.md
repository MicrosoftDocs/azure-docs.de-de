---
title: 'Verwenden von Queue Storage mit Java: Azure Storage'
description: Erfahren Sie, wie Sie den Azure-Warteschlangendienst zum Erstellen und Löschen von Warteschlangen sowie zum Einfügen, Abrufen und Löschen von Nachrichten verwenden. Die Beispiele wurden in Java geschrieben.
services: storage
author: mhopkins-msft
ms.service: storage
ms.devlang: java
ms.topic: article
ms.date: 12/08/2016
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: 3cbd1445640f37cbc63d74d1366c390c774aecd5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65151107"
---
# <a name="how-to-use-queue-storage-from-java"></a>Verwenden des Warteschlangenspeichers mit Java
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="overview"></a>Übersicht
In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem Azure-Warteschlangen-Speicherdienst demonstriert. Die Beispiele wurden in Java geschrieben und verwenden das [Azure Storage-SDK für Java][Azure Storage SDK for Java]. Zu den Szenarien gehören das **Einfügen**, **Einsehen**, **Abrufen** und **Löschen** von Warteschlangennachrichten sowie das **Erstellen** und **Löschen** von Warteschlangen. Weitere Informationen zu Warteschlangen finden Sie im Abschnitt [Nächste Schritte](#next-steps).

Hinweis: Ein SDK steht für Entwickler zur Verfügung, die Azure Storage auf Android-Geräten verwenden. Weitere Informationen finden Sie unter [Azure Storage-SDK für Android][Azure Storage SDK for Android].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Erstellen einer Java-Anwendung
In diesem Leitfaden verwenden Sie Speicherfunktionen, die lokal innerhalb einer Java-Anwendung oder in Code innerhalb einer Webrolle oder Workerrolle in Azure ausgeführt werden können.

Dafür müssen Sie das Java Development Kit (JDK) installieren und ein Azure Storage-Konto in Ihrem Azure-Abonnement erstellen. Sobald Sie dies erledigt haben, müssen Sie sicherstellen, dass Ihr Entwicklungssystem die minimalen Anforderungen und Abhängigkeiten erfüllt, die im Repository [Azure Storage-SDK für Java][Azure Storage SDK for Java] auf GitHub aufgelistet sind. Wenn Ihr System diese Anforderungen erfüllt, können Sie die Anweisungen für das Herunterladen und Installieren der Azure Storage-Bibliotheken für Java auf Ihr System von diesem Repository befolgen. Sobald Sie diese Aufgaben abgeschlossen haben, können Sie eine Java-Anwendung erstellen, die die Beispiele in diesem Artikel verwendet.

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurieren Ihrer Anwendung für den Zugriff auf den Warteschlangenspeicher
Fügen Sie folgende Import-Anweisungen am Anfang der Java-Datei dort ein, wo Azure-Speicher-APIs auf Warteschlangen zugreifen sollen:

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

## <a name="setup-an-azure-storage-connection-string"></a>Einrichten einer Azure-Speicherverbindungszeichenfolge
Ein Azure-Speicherclient verwendet eine Speicherverbindungszeichenfolge zum Speichern von Endpunkten und Anmeldeinformationen für den Zugriff auf Datenverwaltungsdienste. Bei der Ausführung in einer Clientanwendung muss die Speicherverbindungszeichenfolge in dem unten gezeigten Format angegeben werden. Dabei müssen der Name Ihres Speicherkontos und der primäre Zugriffsschlüssel für das im [Azure-Portal](https://portal.azure.com) aufgeführte Speicherkonto als Werte für *AccountName* und *AccountKey* eingegeben werden. Dieses Beispiel zeigt, wie Sie ein statisches Feld für die Verbindungszeichenfolge deklarieren:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

In einer Anwendung, die in einer Microsoft Azure-Rolle ausgeführt wird, kann diese Zeichenfolge in der Dienstkonfigurationsdatei *ServiceConfiguration.cscfg* gespeichert werden. Der Zugriff darauf kann dann durch Aufruf der Methode **RoleEnvironment.getConfigurationSettings** erfolgen. Dieses Beispiel zeigt, wie die Verbindungszeichenfolge von einem **Setting**-Element mit der Bezeichnung *StorageConnectionString* in der Dienstkonfigurationsdatei abgerufen wird:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

In den folgenden Beispielen wird davon ausgegangen, dass Sie eine dieser zwei Methoden verwendet haben, um die Speicherverbindungszeichenfolge abzurufen.

## <a name="how-to-create-a-queue"></a>Gewusst wie: Erstellen einer Warteschlange
Mit einem **CloudQueueClient**-Objekt können Sie Referenzobjekte für Warteschlangen abrufen. Der folgende Code erstellt ein **CloudQueueClient**-Objekt. (Hinweis: **CloudStorageAccount**-Objekte können auch noch auf andere Weise erstellt werden. Weitere Informationen finden Sie in der [Referenz für Azure Storage-Client-SDKs] unter **CloudStorageAccount**.

Mithilfe des **CloudQueueClient** -Objekts können Sie einen Verweis auf die Warteschlange abrufen, die Sie verwenden möchten. Sie können die Warteschlange erstellen, wenn sie nicht vorhanden ist.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

   // Create the queue client.
   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

   // Retrieve a reference to a queue.
   CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Create the queue if it doesn't already exist.
   queue.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-add-a-message-to-a-queue"></a>Gewusst wie: Hinzufügen von Nachrichten zu einer Warteschlange
Um eine Nachricht in eine vorhandene Warteschlange einzufügen, erstellen Sie zuerst ein neues **CloudQueueMessage**-Objekt. Anschließend rufen Sie die **addMessage** -Methode auf. Die **CloudQueueMessage** kann entweder aus einer Zeichenfolge (im UTF-8-Format) oder aus einem Bytearray erstellt werden. Dieser Code erstellte eine Warteschlange (falls noch nicht vorhanden) und fügt die Nachricht "Hello, World" ein.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.createIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-peek-at-the-next-message"></a>Gewusst wie: Einsehen der nächsten Nachricht
Sie können einen Blick auf die Nachricht am Anfang einer Warteschlange werfen, ohne sie aus der Warteschlange zu entfernen, indem Sie die Methode **peekMessage**aufrufen.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Peek at the next message.
    CloudQueueMessage peekedMessage = queue.peekMessage();

    // Output the message value.
    if (peekedMessage != null)
    {
      System.out.println(peekedMessage.getMessageContentAsString());
   }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Gewusst wie: Ändern des Inhalts von Nachrichten in der Warteschlange
Sie können den Inhalt einer Nachricht vor Ort in der Warteschlange ändern. Wenn die Nachricht eine Arbeitsaufgabe darstellt, können Sie diese Funktion verwenden, um den Status der Aufgabe zu aktualisieren. Mit dem folgenden Code wird die Warteschlangennachricht mit neuem Inhalt aktualisiert und das Sichtbarkeits-Zeitlimit um weitere 60 Sekunden verlängert. Dadurch wird der mit der Nachricht verknüpfte Arbeitsstatus gespeichert, und der Client erhält eine weitere Minute zur Bearbeitung der Nachricht. Sie können diese Technik verwenden, um Workflows mit mehreren Schritten in Warteschlangennachrichten zu verfolgen, ohne von vorn beginnen zu müssen, wenn ein Verarbeitungsschritt aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. In der Regel behalten Sie auch die Anzahl der Wiederholungen bei, und wenn die Nachricht mehr als *n* Mal wiederholt wurde, wird sie gelöscht. Dies verhindert, dass eine Nachricht bei jeder Verarbeitung einen Anwendungsfehler auslöst.

Im folgenden Codebeispiel wird die Nachrichtenwartschlange durchsucht, und es wird die erste Nachricht, die mit "Hello, World" übereinstimmt, ausfindig gemacht. Anschließend wird der Nachrichteninhalt geändert, bevor sie verlassen wird.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // The maximum number of messages that can be retrieved is 32.
    final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

    // Loop through the messages in the queue.
    for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
    {
        // Check for a specific string.
        if (message.getMessageContentAsString().equals("Hello, World"))
        {
            // Modify the content of the first matching message.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 30 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 30, updateFields, null, null);
            break;
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

Alternativ aktualisiert das folgende Codebeispiel einfach die erste sichtbare Nachricht in der Warteschlange.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage message = queue.retrieveMessage();

    if (message != null)
    {
        // Modify the message content.
        message.setMessageContent("Updated contents.");
        // Set it to be visible in 60 seconds.
        EnumSet<MessageUpdateFields> updateFields =
            EnumSet.of(MessageUpdateFields.CONTENT,
            MessageUpdateFields.VISIBILITY);
        // Update the message.
        queue.updateMessage(message, 60, updateFields, null, null);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-get-the-queue-length"></a>Gewusst wie: Abrufen der Warteschlangenlänge
Sie können die Anzahl der Nachrichten in einer Warteschlange schätzen lassen. Die **downloadAttributes** -Methode fragt verschiedene aktuelle Werte, darunter die Anzahl der Nachrichten in einer Warteschlange, aus dem Warteschlangendienst ab. Die Anzahl ist nur ein ungefährer Wert, da seit der Antwort des Warteschlangendiensts möglicherweise bereits Nachrichten hinzugefügt oder gelöscht wurden. Die **getApproximateMessageCount**-Methode gibt den letzten von **downloadAttributes** abgerufenen Wert zurück, ohne den Warteschlangendienst aufzurufen.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Download the approximate message count from the server.
    queue.downloadAttributes();

    // Retrieve the newly cached approximate message count.
    long cachedMessageCount = queue.getApproximateMessageCount();

    // Display the queue length.
    System.out.println(String.format("Queue length: %d", cachedMessageCount));
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-dequeue-the-next-message"></a>Gewusst wie: Entfernen der nächsten Nachricht aus der Warteschlange
Dieser Code entfernt eine Nachricht in zwei Schritten aus der Warteschlange. Wenn Sie **retrieveMessage**aufrufen, wird die nächste Nachricht aus der Warteschlange abgerufen. Die für **retrieveMessage** zurückgegebene Nachricht ist für andere Codes nicht mehr sichtbar, die Nachrichten aus dieser Warteschlange lesen. Standardmäßig bleibt die Nachricht 30 Sekunden lang unsichtbar. Um die Nachricht endgültig aus der Warteschlange zu entfernen, müssen Sie außerdem **deleteMessage**aufrufen. Dieser zweistufige Prozess zum Entfernen von Nachrichten stellt sicher, dass eine andere Codeinstanz dieselbe Nachricht erneut abrufen kann, falls die Verarbeitung aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. Der Code ruft **deleteMessage** direkt nach der Verarbeitung der Nachricht auf.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    if (retrievedMessage != null)
    {
        // Process the message in less than 30 seconds, and then delete the message.
        queue.deleteMessage(retrievedMessage);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="additional-options-for-dequeuing-messages"></a>Zusätzliche Optionen für das Entfernen von Nachrichten aus der Warteschlange
Es gibt zwei Möglichkeiten, wie Sie das Abrufen von Nachrichten aus der Warteschlange anpassen können. Erstens können Sie einen Nachrichtenstapel abrufen (bis zu 32). Zweitens können Sie das Unsichtbarkeits-Zeitlimit verkürzen oder verlängern, sodass der Code mehr oder weniger Zeit zur vollständigen Verarbeitung jeder Nachricht benötigt.

Im folgenden Codebeispiel wird **retrieveMessages** verwendet, um 20 Nachrichten mit einem Aufruf abzurufen. Anschließend wird jede Nachricht mithilfe einer **for** -Schleife verarbeitet. Außerdem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten (300 Sekunden) pro Nachricht festgelegt. Beachten Sie, dass die fünf Minuten für alle Nachrichten gleichzeitig beginnen, sodass fünf Minuten nach dem Aufruf von **retrieveMessages**alle Nachrichten, die nicht gelöscht wurden, wieder sichtbar werden.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
        // Do processing for all messages in less than 5 minutes,
        // deleting each message after processing.
        queue.deleteMessage(message);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-list-the-queues"></a>Gewusst wie: Auflisten der Warteschlangen
Rufen Sie zum Abrufen einer Liste der aktuellen Warteschlangen die Methode **CloudQueueClient.listQueues()** auf, die eine Sammlung von **CloudQueue**-Objekten zurückgibt.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient =
        storageAccount.createCloudQueueClient();

    // Loop through the collection of queues.
    for (CloudQueue queue : queueClient.listQueues())
    {
        // Output each queue name.
        System.out.println(queue.getName());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-delete-a-queue"></a>Gewusst wie: Löschen einer Warteschlange
Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die Methode **deleteIfExists** im Objekt **CloudQueue** auf.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Delete the queue if it exists.
    queue.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie sich nun mit den Grundlagen des Warteschlangenspeichers vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

* [Azure Storage-SDK für Java][Azure Storage SDK for Java]
* [Referenz für Azure Storage-Client-SDKs][Referenz für Azure Storage-Client-SDKs]
* [REST-API für Azure Storage-Dienste][Azure Storage Services REST API]
* [Azure Storage-Teamblog][Azure Storage Team Blog]

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Referenz für Azure Storage-Client-SDKs]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage Services REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/

---
title: Erste Schritte mit Warteschlangenspeicher und Visual Studio (Clouddienste)
description: Erfahren Sie etwas über die ersten Schritte mit Azure-Warteschlangenspeicher in einem Clouddienstprojekt in Visual Studio, nachdem Sie mithilfe von verbundenen Visual Studio-Diensten eine Verbindung mit einem Speicherkonto hergestellt haben.
services: storage
author: ghogen
manager: jillfra
ms.assetid: da587aac-5e64-4e9a-8405-44cc1924881d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 94f248edfebd6c6fedb78a54eee220c0ef38b4ab
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545862"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Erste Schritte mit Azure-Warteschlangenspeicher und verbundenen Visual Studio-Diensten (Clouddienstprojekte)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Übersicht
Dieser Artikel beschreibt, wie die ersten Schritte des Verwendens von Azure Queue Storage in Visual Studio aussehen, nachdem Sie über das Visual Studio-Dialogfeld **Verbundene Dienste hinzufügen** in einem Clouddienst-Projekt ein Azure Storage-Konto erstellt oder auf ein solches Konto verwiesen haben.

Wir zeigen Ihnen, wie Sie eine Warteschlange in Code erstellen. Außerdem wird gezeigt, wie Sie grundlegende Warteschlangenvorgänge, etwa Hinzufügen, Ändern, Lesen und Entfernen von Warteschlangennachrichten ausführen. Die Beispiele sind in C# geschrieben und greifen auf die [Microsoft Azure-Speicherclientbibliothek für .NET](/previous-versions/azure/dn261237(v=azure.100))zurück.

Beim Vorgang **Verbundene Dienste hinzufügen** werden die entsprechenden NuGet-Pakete installiert, um auf Azure-Speicher in Ihrem Projekt zuzugreifen, und Ihren Projektkonfigurationsdateien die Verbindungszeichenfolge für das Speicherkonto hinzugefügt.

* Weitere Informationen zum Bearbeiten von Warteschlangen im Code finden Sie unter [Erste Schritte mit Azure Queue Storage mit .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) .
* Allgemeine Informationen zum Azure-Speicher finden Sie unter [Speicherdokumentation](https://azure.microsoft.com/documentation/services/storage/) .
* Allgemeine Informationen zu Azure-Clouddiensten finden Sie unter [Cloud Services-Dokumentation](https://azure.microsoft.com/documentation/services/cloud-services/) .
* Weitere Informationen über das Programmieren von ASP.NET-Anwendungen finden Sie unter [ASP.NET](https://www.asp.net) .

Die Warteschlangenspeicherung in Azure ist ein Dienst zur Speicherung großer Anzahlen von Nachrichten, auf die von überall auf der Welt mit authentifizierten Anrufen über HTTP oder HTTPS zugegriffen werden kann. Eine einzelne Warteschlangennachricht kann bis zu 64 KB groß sein, und eine Warteschlange kann Millionen von Nachrichten enthalten. Deren Anzahl ist nur durch die Kapazität des Speicherkontos begrenzt.

## <a name="access-queues-in-code"></a>Zugriff auf Warteschlangen in Code
Um auf Warteschlangen in Projekten der Visual Studio-Clouddienste zuzugreifen, müssen Sie die folgenden Elemente zu jeder C#-Quelldatei hinzufügen, in der auf Azure-Warteschlangenspeicher zugegriffen wird.

1. Vergewissern Sie sich, dass die Namespacedeklarationen am Anfang der C#-Datei diese **using** -Anweisungen enthalten.
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
2. Rufen Sie ein **CloudStorageAccount** -Objekt ab, das die Informationen zu Ihrem Speicherkonto enthält. Verwenden Sie den folgenden Code, um Ihre Speicherverbindungszeichenfolge und Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
3. Rufen Sie ein **CloudQueueClient** -Objekt ab, um auf die Warteschlangenobjekte in Ihrem Speicherkonto zu verweisen.  
   
    ```csharp
    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
4. Abrufen eines **CloudQueue** -Objekts, das auf eine bestimmte Warteschlange verweist.
   
    ```csharp
    // Get a reference to a queue named "messageQueue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");
    ```

**HINWEIS:** Verwenden Sie den gesamten obigen Code vor dem Code in den folgenden Beispielen.

## <a name="create-a-queue-in-code"></a>Erstellen einer Warteschlange in Code
Fügen Sie zum Erstellen einer Warteschlange in Code einfach einen Aufruf von **CreateIfNotExists** hinzu.

```csharp
// Create the CloudQueue if it does not exist
messageQueue.CreateIfNotExists();
```

## <a name="add-a-message-to-a-queue"></a>Hinzufügen von Nachrichten zu einer Warteschlange
Wenn Sie eine Nachricht in eine vorhandene Warteschlange einfügen möchten, erstellen Sie zuerst ein neues **CloudQueueMessage**-Objekt, und rufen Sie dann die **AddMessage**-Methode auf.

Ein **CloudQueueMessage** -Objekt kann aus einer Zeichenfolge (im UTF-8-Format) oder aus einem Bytearray erstellt werden.

Hier ist ein Beispiel, dass die Nachricht "Hello, World" eingefügt.

```csharp
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
messageQueue.AddMessage(message);
```

## <a name="read-a-message-in-a-queue"></a>Lesen von Nachrichten in einer Warteschlange
Sie können einen Blick auf die Nachricht am Anfang einer Warteschlange werfen, ohne sie aus der Warteschlange zu entfernen, indem Sie die Methode **PeekMessage** aufrufen.

```csharp
// Peek at the next message
CloudQueueMessage peekedMessage = messageQueue.PeekMessage();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Lesen und Entfernen von Nachrichten in einer Warteschlange
Ihr Code kann eine Nachricht in zwei Schritten aus der Warteschlange entfernen.

1. Rufen Sie **GetMessage** auf, um die nächste Nachricht in einer Warteschlange abzurufen. Die für **GetMessage** zurückgegebene Nachricht ist für andere Codes nicht mehr sichtbar, die Nachrichten aus dieser Warteschlange lesen. Standardmäßig bleibt die Nachricht 30 Sekunden lang unsichtbar.
2. Wenn Sie die Nachricht endgültig aus der Warteschlange entfernen möchten, rufen Sie **DeleteMessage** auf.

Dieser zweistufige Prozess zum Entfernen von Nachrichten stellt sicher, dass eine andere Codeinstanz dieselbe Nachricht erneut abrufen kann, falls die Verarbeitung aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. Der folgende Code ruft **DeleteMessage** direkt nach der Verarbeitung der Nachricht auf.

```csharp
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

// Process the message in less than 30 seconds

// Then delete the message.
await messageQueue.DeleteMessage(retrievedMessage);
```


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>Verwenden zusätzlicher Optionen zum Verarbeiten und Entfernen von Nachrichten in Warteschlangen
Es gibt zwei Möglichkeiten, wie Sie das Abrufen von Nachrichten aus der Warteschlange anpassen können.

* Sie können einen Nachrichtenstapel abrufen (bis zu 32).
* Außerdem können Sie das Unsichtbarkeits-Zeitlimit verkürzen oder verlängern, sodass der Code mehr oder weniger Zeit zur vollständigen Verarbeitung jeder Nachricht benötigt. Im folgenden Codebeispiel wird **GetMessages** verwendet, um 20 Nachrichten mit einem Aufruf abzurufen. Anschließend wird jede Nachricht mithilfe einer **foreach** -Schleife verarbeitet. Außerdem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten pro Nachricht festgelegt. Beachten Sie, dass die 5 Minuten für alle Nachrichten gleichzeitig beginnen, sodass 5 Minuten nach dem Aufruf von **GetMessages** alle Nachrichten, die nicht gelöscht wurden, wieder sichtbar werden.

Hier sehen Sie ein Beispiel:

```csharp
foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.

    // Then delete the message after processing
    messageQueue.DeleteMessage(message);

}
```

## <a name="get-the-queue-length"></a>Abrufen der Warteschlangenlänge
Sie können die Anzahl der Nachrichten in einer Warteschlange schätzen lassen. Die Methode **FetchAttributes** fordert den Warteschlangendienst auf, die Warteschlangenattribute einschließlich der Nachrichtenanzahl abzurufen. Die **ApproximateMethodCount**-Eigenschaft gibt den letzten von der **FetchAttributes**-Methode abgerufenen Wert zurück, ohne den Warteschlangendienst aufzurufen.

```csharp
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>Verwenden des Async-Await-Musters mit allgemeinen Azure-Warteschlangen-APIs
In diesem Beispiel wird veranschaulicht, wie das Async-Await-Muster mit allgemeinen Azure-Warteschlangen-APIs verwendet wird. Im Beispiel werden jeweils die asynchronen Versionen der angegebenen Methoden aufgerufen, was am Postfix **Async** der einzelnen Methoden erkennbar ist. Wenn eine asynchrone Methode verwendet wird, hält das Async-Await-Muster die lokale Ausführung an, bis der Aufruf abgeschlossen ist. Durch dieses Verhalten kann der aktuelle Thread eine andere Aktion ausführen, wodurch Leistungsengpässe vermieden werden und die allgemeine Reaktionsfähigkeit der Anwendung verbessert wird. Weitere Informationen zur Verwendung des Async-Await-Musters in .NET finden Sie unter [Async und Await (C# und Visual Basic)](/previous-versions/hh191443(v=vs.140))

```csharp
// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Add the message asynchronously
await messageQueue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Delete the message asynchronously
await messageQueue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="delete-a-queue"></a>Löschen einer Warteschlange
Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die Methode **Delete** für das Warteschlangenobjekt auf.

```csharp
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Nächste Schritte
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
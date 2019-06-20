---
title: Erste Schritte mit Queue Storage und verbundenen Visual Studio-Diensten (ASP.NET Core) | Microsoft-Dokumentation
description: Erste Schritte mit Azure Queue Storage in einem ASP.NET Core-Projekt in Visual Studio
services: storage
author: ghogen
manager: douge
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: 8a91614e7dfb804e6a902967ce60f898ed0e54ad
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60508472"
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Erste Schritte mit Queue Storage und verbundenen Visual Studio-Diensten (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

Dieser Artikel beschreibt, wie die ersten Schritte beim Verwenden von Azure Queue Storage in Visual Studio aussehen, nachdem Sie mit dem Feature **Verbundene Dienste** in einem ASP.NET Core-Projekt ein Azure Storage-Konto erstellt oder auf ein solches Konto verwiesen haben. Beim Vorgang für **Verbundene Dienste** werden die entsprechenden NuGet-Pakete installiert, um auf Azure Storage in Ihrem Projekt zuzugreifen, und die Verbindungszeichenfolge für das Speicherkonto wird in Ihren Konfigurationsdateien des Projekts hinzugefügt. (Allgemeine Informationen zu Azure Storage finden Sie in der [Speicherdokumentation](https://azure.microsoft.com/documentation/services/storage/).)

Der Azure-Warteschlangenspeicher ist ein Dienst zum Speichern einer großen Anzahl von Nachrichten, auf die weltweit mit authentifizierten Aufrufen mithilfe von HTTP oder HTTPS zugegriffen werden kann. Eine einzelne Warteschlangennachricht kann bis zu 64 Kilobytes (KB) groß sein, und eine Warteschlange kann Millionen von Nachrichten enthalten. Deren Anzahl ist nur durch die Kapazität des Speicherkontos begrenzt. Details zum programmgesteuerten Bearbeiten von Warteschlangen finden Sie unter [Erste Schritte mit Azure Queue Storage mit .NET](../storage/queues/storage-dotnet-how-to-use-queues.md).

Erstellen Sie zuerst eine Azure-Warteschlange in Ihrem Speicherkonto. In diesem Artikel wird dann beschrieben, wie Sie eine Warteschlange in C# erstellen und grundlegende Warteschlangenvorgänge durchführen, z.B. Hinzufügen, Ändern, Lesen und Entfernen von Warteschlangennachrichten.  Im Code wird die Azure Storage-Clientbibliothek für .NET verwendet. Weitere Informationen zu ASP.NET finden Sie unter [ASP.NET](https://www.asp.net).

Einige Azure Storage-APIs sind asynchron, und im Code in diesem Artikel wird vorausgesetzt, dass asynchrone Methoden verwendet werden. Weitere Informationen finden Sie unter [Asynchrone Programmierung](https://docs.microsoft.com/dotnet/csharp/async).

## <a name="access-queues-in-code"></a>Zugriff auf Warteschlangen in Code

Fügen Sie in jeder C#-Quelldatei, in der auf Azure Queue Storage zugegriffen wird, die folgenden Elemente hinzu, um auf Warteschlangen in ASP.NET Core-Projekten zuzugreifen. Verwenden Sie diesen gesamten Code jeweils vor dem Code in den folgenden Abschnitten.

1. Fügen Sie die erforderlichen `using`-Anweisungen hinzu:
    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Rufen Sie ein `CloudStorageAccount`-Objekt ab, das die Informationen zu Ihrem Speicherkonto enthält. Verwenden Sie den folgenden Code, um die Speicherverbindungszeichenfolge und Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Rufen Sie ein `CloudQueueClient`-Objekt ab, um auf die Warteschlangenobjekte in Ihrem Speicherkonto zu verweisen:

    ```cs
    // Create the CloudQueueClient object for the storage account.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Abrufen eines `CloudQueue`-Objekts, das auf eine bestimmte Warteschlange verweist:

    ```cs
    // Get a reference to the CloudQueue named "messagequeue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messagequeue");
    ```

### <a name="create-a-queue-in-code"></a>Erstellen einer Warteschlange in Code

Rufen Sie zum Erstellen der Azure-Warteschlange im Code `CreateIfNotExistsAsync` auf:

```cs
// Create the CloudQueue if it does not exist.
await messageQueue.CreateIfNotExistsAsync();
```

## <a name="add-a-message-to-a-queue"></a>Hinzufügen von Nachrichten zu einer Warteschlange

Erstellen Sie zum Einfügen einer Nachricht in eine vorhandene Warteschlange ein neues `CloudQueueMessage`-Objekt, und rufen Sie dann die `AddMessageAsync`-Methode auf. Ein `CloudQueueMessage`-Objekt kann aus einer Zeichenfolge (im UTF-8-Format) oder aus einem Bytearray erstellt werden.

```cs
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await messageQueue.AddMessageAsync(message);
```

## <a name="read-a-message-in-a-queue"></a>Lesen von Nachrichten in einer Warteschlange

Sie können einen Blick auf die Nachricht am Anfang einer Warteschlange werfen, ohne sie aus der Warteschlange zu entfernen, indem Sie die `PeekMessageAsync`-Methode aufrufen:

```cs
// Peek the next message in the queue.
CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Lesen und Entfernen von Nachrichten in einer Warteschlange

Ihr Code kann eine Nachricht in zwei Schritten aus der Warteschlange entfernen.

1. Rufen Sie `GetMessageAsync` auf, um die nächste Nachricht in einer Warteschlange abzurufen. Die für `GetMessageAsync` zurückgegebene Nachricht ist für anderen Code, mit dem Nachrichten aus dieser Warteschlange gelesen werden, nicht mehr sichtbar. Standardmäßig bleibt die Nachricht 30 Sekunden lang unsichtbar.
1. Wenn Sie die Nachricht endgültig aus der Warteschlange entfernen möchten, rufen Sie `DeleteMessageAsync` auf.

Dieser zweistufige Prozess zum Entfernen von Nachrichten stellt sicher, dass eine andere Codeinstanz dieselbe Nachricht erneut abrufen kann, falls die Verarbeitung aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. Im folgenden Code wird `DeleteMessageAsync` direkt nach der Verarbeitung der Nachricht aufgerufen:

```cs
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

// Process the message in less than 30 seconds.

// Then delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
```

## <a name="additional-options-for-dequeuing-messages"></a>Zusätzliche Optionen für das Entfernen von Nachrichten aus der Warteschlange

Es gibt zwei Möglichkeiten, das Abrufen von Nachrichten aus der Warteschlange anzupassen. Erstens können Sie einen Nachrichtenstapel abrufen (bis zu 32). Zweitens können Sie das Unsichtbarkeits-Zeitlimit verkürzen oder verlängern, sodass der Code mehr oder weniger Zeit zur vollständigen Verarbeitung jeder Nachricht benötigt. Im folgenden Codebeispiel wird `GetMessages` verwendet, um 20 Nachrichten mit einem Aufruf abzurufen. Anschließend wird jede Nachricht mithilfe einer `foreach`-Schleife verarbeitet. Außerdem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten pro Nachricht festgelegt. Beachten Sie Folgendes: Der 5-Minuten-Timer beginnt für alle Nachrichten gleichzeitig, sodass nach fünf Minuten alle nicht gelöschten Nachrichten wieder sichtbar sind.

```cs
// Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
//   delete each message after processing.

foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

## <a name="get-the-queue-length"></a>Abrufen der Warteschlangenlänge

Sie können die Anzahl der Nachrichten in einer Warteschlange schätzen lassen. Die `FetchAttributes`-Methode fordert den Warteschlangendienst auf, die Warteschlangenattribute einschließlich der Nachrichtenanzahl abzurufen. Mit der `ApproximateMethodCount`-Eigenschaft wird der letzte Wert zurückgegeben, der mit der `FetchAttributes`-Methode abgerufen wurde, ohne den Warteschlangendienst aufzurufen.

```cs
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display the number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Verwenden des Async-Await-Musters mit allgemeinen Warteschlangen-APIs

In diesem Beispiel wird veranschaulicht, wie das Async-Await-Muster mit allgemeinen Warteschlangen-APIs verwendet wird, die auf `Async` enden. Wenn eine asynchrone Methode verwendet wird, hält das Async-Await-Muster die lokale Ausführung an, bis der Aufruf abgeschlossen ist. Durch dieses Verhalten kann der aktuelle Thread eine andere Aktion ausführen, wodurch Leistungsengpässe vermieden werden und die allgemeine Reaktionsfähigkeit der Anwendung verbessert wird.

```cs
// Create a message to add to the queue.
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message.
await messageQueue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="delete-a-queue"></a>Löschen einer Warteschlange

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die `Delete`-Methode für das Warteschlangenobjekt auf:

```cs
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]

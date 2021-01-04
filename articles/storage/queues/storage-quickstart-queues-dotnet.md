---
title: 'Schnellstart: Azure Queue Storage-Bibliothek v12 – .NET'
description: Erfahren Sie, wie Sie die Azure Queue Storage-Bibliothek v12 für .NET verwenden, um eine Warteschlange zu erstellen und dieser Nachrichten hinzuzufügen. Dann lernen Sie, wie Sie Nachrichten in der Warteschlange lesen und löschen. Zudem erfahren Sie, wie Sie eine Warteschlange löschen.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: f8900db8ed43b8c255915bf5429e1211f04e7338
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96491960"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-net"></a>Schnellstart: Azure Queue Storage-Clientbibliothek v12 für .NET

Beginnen Sie mit dem Einsatz der Azure Queue Storage-Clientbibliothek v12 für .NET. Azure Queue Storage ist ein Dienst für die Speicherung einer großen Anzahl von Nachrichten, die später abgerufen und verarbeitet werden. Führen Sie die nachfolgenden Schritte aus, um das Paket zu installieren und den Beispielcode für grundlegende Aufgaben zu testen.

Mit der Azure Queue Storage-Clientbibliothek v12 für .NET können Sie Folgendes ausführen:

- Erstellen einer Warteschlange
- Hinzufügen von Nachrichten zu einer Warteschlange
- Einsehen von Nachrichten in einer Warteschlange
- Aktualisieren einer Nachricht in einer Warteschlange
- Empfangen von Nachrichten aus einer Warteschlange
- Löschen von Nachrichten aus einer Warteschlange
- Löschen einer Warteschlange

Zusätzliche Ressourcen:

- [API-Referenzdokumentation](/dotnet/api/azure.storage.queues)
- [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues)
- [Paket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Queues/12.0.0)
- [Beispiele](../common/storage-samples-dotnet.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)
- Azure Storage-Konto – [Erstellen eines Speicherkontos](../common/storage-account-create.md)
- Aktuelles [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) für Ihr Betriebssystem. Laden Sie unbedingt das SDK und nicht die Runtime herunter.

## <a name="setting-up"></a>Einrichten

In diesem Abschnitt wird beschrieben, wie Sie ein Projekt zur Verwendung mit der Azure Queue Storage-Clientbibliothek v12 für .NET vorbereiten.

### <a name="create-the-project"></a>Erstellen des Projekts

Erstellen Sie eine .NET Core-Anwendung mit dem Namen *QueuesQuickstartV12*.

1. Verwenden Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) den Befehl `dotnet new` zum Erstellen einer neuen Konsolen-App mit dem Namen *QueuesQuickstartV12*. Dieser Befehl erstellt ein einfaches „Hallo Welt“-C#-Projekt mit einer einzigen Quelldatei: *Program.cs*.

   ```console
   dotnet new console -n QueuesQuickstartV12
   ```

1. Wechseln Sie zum neu erstellten Verzeichnis *QueuesQuickstartV12*.

   ```console
   cd QueuesQuickstartV12
   ```

### <a name="install-the-package"></a>Installieren des Pakets

Installieren Sie im Anwendungsverzeichnis mit dem Befehl `dotnet add package` das Paket für die Azure Queue Storage-Clientbibliothek für .NET.

```console
dotnet add package Azure.Storage.Queues
```

### <a name="set-up-the-app-framework"></a>Einrichten des App-Frameworks

Über das Projektverzeichnis:

1. Öffnen Sie die Datei *Program.cs* im Editor.
1. Entfernen Sie die Anweisung `Console.WriteLine("Hello World!");`.
1. Fügen Sie Anweisungen vom Typ `using` hinzu.
1. Aktualisieren der `Main`-Methodendeklaration zur [Unterstützung von asynchronem Code](/dotnet/csharp/whats-new/csharp-7#async-main)

Der Code lautet wie folgt:

```csharp
using Azure;
using Azure.Storage.Queues;
using Azure.Storage.Queues.Models;
using System;
using System.Threading.Tasks;

namespace QueuesQuickstartV12
{
    class Program
    {
        static async Task Main(string[] args)
        {
        }
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektmodell

Azure Queue Storage ist ein Dienst für die Speicherung einer großen Anzahl von Nachrichten. Eine Warteschlangennachricht kann bis zu 64 KB groß sein. Eine Warteschlange kann Millionen Nachrichten enthalten, bis die maximale Kapazität eines Speicherkontos erreicht ist. Warteschlangen werden häufig verwendet, um ein Arbeits-Backlog zur asynchronen Verarbeitung zu erstellen. Queue Storage bietet drei Arten von Ressourcen:

- Das Speicherkonto
- Eine Warteschlange im Speicherkonto
- Nachrichten in der Warteschlange

Im folgenden Diagramm ist die Beziehung zwischen diesen Ressourcen dargestellt.

![Diagramm der Queue Storage-Architektur](./media/storage-queues-introduction/queue1.png)

Verwenden Sie die folgenden .NET-Klassen zur Interaktion mit folgenden Ressourcen:

- [QueueServiceClient](/dotnet/api/azure.storage.queues.queueserviceclient): Mit dem `QueueServiceClient` können Sie alle Warteschlangen in Ihrem Speicherkonto verwalten.
- [QueueClient](/dotnet/api/azure.storage.queues.queueclient): Mit der `QueueClient`-Klasse können Sie eine einzelne Warteschlange und die darin enthaltenen Nachrichten verwalten und bearbeiten.
- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage): Die `QueueMessage`-Klasse repräsentiert die einzelnen Objekte, die beim Aufrufen von [ReceiveMessages](/dotnet/api/azure.storage.queues.queueclient.receivemessages) in einer Warteschlange zurückgegeben werden.

## <a name="code-examples"></a>Codebeispiele

Diese Beispielcodeausschnitte veranschaulichen, wie folgende Vorgänge mit der Azure Queue Storage-Clientbibliothek für .NET ausgeführt werden:

- [Abrufen der Verbindungszeichenfolge](#get-the-connection-string)
- [Erstellen einer Warteschlange](#create-a-queue)
- [Hinzufügen von Nachrichten zu einer Warteschlange](#add-messages-to-a-queue)
- [Einsehen von Nachrichten in einer Warteschlange](#peek-at-messages-in-a-queue)
- [Aktualisieren einer Nachricht in einer Warteschlange](#update-a-message-in-a-queue)
- [Empfangen von Nachrichten aus einer Warteschlange](#receive-messages-from-a-queue)
- [Löschen von Nachrichten aus einer Warteschlange](#delete-messages-from-a-queue)
- [Löschen einer Warteschlange](#delete-a-queue)

### <a name="get-the-connection-string"></a>Abrufen der Verbindungszeichenfolge

Der unten gezeigte Code ruft die Verbindungszeichenfolge für das Speicherkonto ab. Die Verbindungszeichenfolge ist in der Umgebungsvariablen gespeichert, die Sie im Abschnitt [Konfigurieren der Speicherverbindungszeichenfolge](#configure-your-storage-connection-string) erstellt haben.

Fügen Sie diesen Code in der `Main`-Methode hinzu:

```csharp
Console.WriteLine("Azure Queue storage v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable called
// AZURE_STORAGE_CONNECTION_STRING on the machine running the application.
// If the environment variable is created after the application is launched
// in a console or with Visual Studio, the shell or application needs to be
// closed and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>Erstellen einer Warteschlange

Legen Sie einen Namen für die neue Warteschlange fest. Der folgende Code hängt einen GUID-Wert an den Warteschlangennamen an, damit dieser eindeutig ist.

> [!IMPORTANT]
> Warteschlangennamen dürfen nur Kleinbuchstaben, Ziffern und Bindestriche enthalten und müssen mit einem Buchstaben oder einer Ziffer beginnen. Vor und nach jedem Bindestrich muss ein Zeichen stehen, das kein Bindestrich ist. Der Name muss außerdem zwischen 3 und 63 Zeichen lang sein. Weitere Informationen zum Benennen von Warteschlangen finden Sie unter [Benennen von Warteschlangen und Metadaten](/rest/api/storageservices/naming-queues-and-metadata).

Erstellen Sie eine Instanz der [QueueClient](/dotnet/api/azure.storage.queues.queueclient)-Klasse. Rufen Sie dann die [CreateAsync](/dotnet/api/azure.storage.queues.queueclient.createasync)-Methode auf, um die Warteschlange in Ihrem Speicherkonto zu erstellen.

Fügen Sie diesen Code am Ende der `Main`-Methode hinzu:

```csharp
// Create a unique name for the queue
string queueName = "quickstartqueues-" + Guid.NewGuid().ToString();

Console.WriteLine($"Creating queue: {queueName}");

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClient(connectionString, queueName);

// Create the queue
await queueClient.CreateAsync();
```

### <a name="add-messages-to-a-queue"></a>Hinzufügen von Nachrichten zu einer Warteschlange

Der folgende Codeausschnitt fügt der Warteschlange asynchron durch Aufrufen der [SendMessageAsync](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync)-Methode Nachrichten hinzu. Darüber hinaus wird ein [SendReceipt](/dotnet/api/azure.storage.queues.models.sendreceipt) gespeichert, das von einem `SendMessageAsync`-Aufruf zurückgegeben wird. Die Bestätigung wird später im Programm zum Aktualisieren der Nachricht verwendet.

Fügen Sie diesen Code am Ende der `Main`-Methode hinzu:

```csharp
Console.WriteLine("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.SendMessageAsync("First message");
await queueClient.SendMessageAsync("Second message");

// Save the receipt so we can update this message later
SendReceipt receipt = await queueClient.SendMessageAsync("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Einsehen von Nachrichten in einer Warteschlange

Durch Aufrufen der [PeekMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.peekmessagesasync)-Methode können Sie die Nachrichten in der Warteschlange einsehen. Die `PeekMessagesAsync`-Methode ruft mindestens eine Nachricht vom Anfang der Warteschlange ab, ändert aber nicht die Sichtbarkeit der Nachricht.

Fügen Sie diesen Code am Ende der `Main`-Methode hinzu:

```csharp
Console.WriteLine("\nPeek at the messages in the queue...");

// Peek at messages in the queue
PeekedMessage[] peekedMessages = await queueClient.PeekMessagesAsync(maxMessages: 10);

foreach (PeekedMessage peekedMessage in peekedMessages)
{
    // Display the message
    Console.WriteLine($"Message: {peekedMessage.MessageText}");
}
```

### <a name="update-a-message-in-a-queue"></a>Aktualisieren einer Nachricht in einer Warteschlange

Aktualisieren Sie den Inhalt einer Nachricht, indem Sie die [UpdateMessageAsync](/dotnet/api/azure.storage.queues.queueclient.updatemessageasync)-Methode aufrufen. Die `UpdateMessageAsync`-Methode kann das Sichtbarkeitstimeout und den Inhalt einer Nachricht ändern. Beim Nachrichteninhalt muss es sich um eine UTF-8-codierte Zeichenfolge handeln, die bis zu 64 KB groß sein darf. Übergeben Sie zusammen mit dem neuen Inhalt für die Nachricht Werte aus dem `SendReceipt`, das weiter oben im Code gespeichert wurde. Die `SendReceipt`-Werte identifizieren die Nachricht, die aktualisiert werden soll.

```csharp
Console.WriteLine("\nUpdating the third message in the queue...");

// Update a message using the saved receipt from sending the message
await queueClient.UpdateMessageAsync(receipt.MessageId, receipt.PopReceipt, "Third message has been updated");
```

### <a name="receive-messages-from-a-queue"></a>Empfangen von Nachrichten aus einer Warteschlange

Laden Sie zuvor hinzugefügte Nachrichten durch Aufrufen der [ReceiveMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync)-Methode herunter.

Fügen Sie diesen Code am Ende der `Main`-Methode hinzu:

```csharp
Console.WriteLine("\nReceiving messages from the queue...");

// Get messages from the queue
QueueMessage[] messages = await queueClient.ReceiveMessagesAsync(maxMessages: 10);
```

### <a name="delete-messages-from-a-queue"></a>Löschen von Nachrichten aus einer Warteschlange

Löschen Sie Nachrichten aus der Warteschlange, nachdem sie verarbeitet wurden. In diesem Fall besteht die Verarbeitung nur darin, dass die Nachricht in der Konsole angezeigt wird.

Die App wird angehalten und wartet auf Benutzereingaben, indem `Console.ReadLine` aufgerufen wird, bevor die Nachrichten verarbeitet und gelöscht werden. Überprüfen Sie im [Azure-Portal](https://portal.azure.com), ob die Ressourcen ordnungsgemäß erstellt wurden, bevor sie gelöscht werden. Alle Nachrichten, die nicht explizit gelöscht werden, werden schlussendlich wieder in der Warteschlange angezeigt und ggf. erneut verarbeitet.

Fügen Sie diesen Code am Ende der `Main`-Methode hinzu:

```csharp
Console.WriteLine("\nPress Enter key to 'process' messages and delete them from the queue...");
Console.ReadLine();

// Process and delete messages from the queue
foreach (QueueMessage message in messages)
{
    // "Process" the message
    Console.WriteLine($"Message: {message.MessageText}");

    // Let the service know we're finished with
    // the message and it can be safely deleted.
    await queueClient.DeleteMessageAsync(message.MessageId, message.PopReceipt);
}
```

### <a name="delete-a-queue"></a>Löschen einer Warteschlange

Der folgende Code bereinigt die von der App erstellten Ressourcen, indem die Warteschlange mithilfe der [DeleteAsync](/dotnet/api/azure.storage.queues.queueclient.deleteasync)-Methode gelöscht wird.

Fügen Sie diesen Code am Ende der `Main`-Methode hinzu:

```csharp
Console.WriteLine("\nPress Enter key to delete the queue...");
Console.ReadLine();

// Clean up
Console.WriteLine($"Deleting queue: {queueClient.Name}");
await queueClient.DeleteAsync();

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>Ausführen des Codes

Diese App erstellt drei Nachrichten und fügt sie einer Azure-Warteschlange hinzu. Der Code listet die Nachrichten in der Warteschlange auf, ruft sie ab und löscht sie, bevor er letztendlich die Warteschlange löscht.

Navigieren Sie im Konsolenfenster zu Ihrem Anwendungsverzeichnis, erstellen Sie die Anwendung, und führen Sie sie aus.

```console
dotnet build
```

```console
dotnet run
```

Die Ausgabe der App sieht etwa wie das folgende Beispiel aus:

```output
Azure Queue storage v12 - .NET quickstart sample

Creating queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2
Done
```

Wenn die App vor dem Empfangen von Nachrichten angehalten wird, überprüfen Sie Ihr Speicherkonto im [Azure-Portal](https://portal.azure.com). Überprüfen Sie, ob sich in der Warteschlange Nachrichten befinden.

Drücken Sie die **EINGABETASTE**, um Nachrichten zu empfangen und zu löschen. Wenn Sie dazu aufgefordert werden, drücken Sie erneut die **EINGABETASTE**, um die Warteschlange zu löschen und die Demo zu beenden.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie mithilfe von asynchronem .NET-Code eine Warteschlange erstellen und dieser Nachrichten hinzufügen. Danach haben Sie erfahren, wie Sie Nachrichten einsehen, abrufen und löschen. Zum Schluss haben Sie gelernt, wie Sie eine Nachrichtenwarteschlange löschen.

Tutorials, Beispiele, Schnellstartanleitungen und weiteres Dokumentationsmaterial finden Sie hier:

> [!div class="nextstepaction"]
> [Azure für .NET- und .NET Core-Entwickler](/dotnet/azure/)

- Weitere Informationen finden Sie unter [Azure Storage libraries for .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage) (Azure Storage-Bibliotheken für .NET).
- Weitere Beispiel-Apps für Azure Queue Storage finden Sie bei den [Azure.Storage.Queues Samples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples) (Azure.Storage.Queues-Beispielen).
- Weitere Informationen zu .NET Core finden Sie unter [Get started with .NET in 10 minutes](https://www.microsoft.com/net/learn/get-started/) (Einstieg in .NET in 10 Minuten).

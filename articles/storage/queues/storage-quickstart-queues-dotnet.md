---
title: 'Schnellstart: Erstellen einer Warteschlange in Azure Storage mithilfe von .NET'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der Azure Storage-Clientbibliothek für .NET eine Warteschlange erstellen und ihr Nachrichten hinzufügen. Im Anschluss erfahren Sie dann, wie Sie Nachrichten aus der Warteschlange lesen und verarbeiten.
services: storage
author: mhopkins-msft
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/06/2018
ms.author: mhopkins
ms.reviewer: cbrooks
ms.openlocfilehash: 41cb37eb9d96752d4732731d2a36d9bc892cbaa5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159810"
---
# <a name="quickstart-use-net-to-create-a-queue-in-azure-storage"></a>Schnellstart: Erstellen einer Warteschlange in Azure Storage mithilfe von .NET

In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der Azure Storage-Clientbibliothek für .NET eine Warteschlange erstellen und ihr Nachrichten hinzufügen. Im Anschluss erfahren Sie dann, wie Sie Nachrichten aus der Warteschlange lesen und verarbeiten. 

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Laden Sie als Nächstes .NET Core 2.0 für Ihr Betriebssystem herunter, und führen Sie die Installation durch. Beim Ausführen von Windows können Sie Visual Studio installieren und bei Bedarf .NET Framework verwenden. Sie können auch einen Editor für die Verwendung mit Ihrem Betriebssystem installieren.

### <a name="windows"></a>Windows

- Installieren Sie [.NET Core für Windows](https://www.microsoft.com/net/download/windows) oder das [.NET Framework](https://www.microsoft.com/net/download/windows) (in Visual Studio für Windows enthalten).
- Installieren Sie [Visual Studio für Windows](https://www.visualstudio.com/). Bei Verwendung von .NET Core ist die Installation von Visual Studio optional.  

Informationen zur Wahl zwischen .NET Core und .NET Framework finden Sie unter [Wahl zwischen .NET Core und .NET Framework für Server-Apps](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server).

### <a name="linux"></a>Linux

- Installieren Sie [.NET Core für Linux](https://www.microsoft.com/net/download/linux).
- Optional können Sie [Visual Studio Code](https://www.visualstudio.com/) und die [C#-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp&dotnetid=963890049.1518206068) installieren.

### <a name="macos"></a>macOS

- Installieren Sie [.NET Core für macOS](https://www.microsoft.com/net/download/macos).
- Optional können Sie [Visual Studio für Mac](https://www.visualstudio.com/vs/visual-studio-mac/) installieren.

## <a name="download-the-sample-application"></a>Herunterladen der Beispielanwendung

Die in diesem Schnellstart verwendete Beispielanwendung ist eine einfache Konsolenanwendung. Sie können die Beispielanwendung auf [GitHub](https://github.com/Azure-Samples/storage-queues-dotnet-quickstart) erkunden.

Verwenden Sie [Git](https://git-scm.com/), um eine Kopie der Anwendung in Ihre Entwicklungsumgebung herunterzuladen. 

```bash
git clone https://github.com/Azure-Samples/storage-queues-dotnet-quickstart.git
```

Mit diesem Befehl wird das Repository in Ihren lokalen Git-Ordner geklont. Suchen Sie zum Öffnen der Visual Studio-Projektmappe nach dem Ordner *storage-queues-dotnet-quickstart*, öffnen Sie ihn, und doppelklicken Sie auf *storage-queues-dotnet-quickstart.sln*. 

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurieren der Speicherverbindungszeichenfolge

Zum Ausführen der Anwendung müssen Sie die Verbindungszeichenfolge für das Speicherkonto angeben. Die Beispielanwendung liest die Verbindungszeichenfolge aus einer Umgebung und verwendet sie zum Autorisieren von Anforderungen an Azure Storage.

Schreiben Sie die Verbindungszeichenfolge nach dem Kopieren in eine neue Umgebungsvariable auf dem lokalen Computer, auf dem die Anwendung ausgeführt wird. Öffnen Sie zum Festlegen der Umgebungsvariablen ein Konsolenfenster, und befolgen Sie die Anleitung für Ihr Betriebssystem. Ersetzen Sie `<yourconnectionstring>` durch Ihre Verbindungszeichenfolge:

### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

Nachdem Sie die Umgebungsvariable hinzugefügt haben, müssen Sie unter Umständen alle ausgeführten Programme neu starten, von denen die Umgebungsvariable gelesen werden muss, z.B. das Konsolenfenster. Wenn Sie beispielsweise Visual Studio als Editor verwenden, müssen Sie Visual Studio neu starten, bevor Sie das Beispiel ausführen. 

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```

Führen Sie nach dem Hinzufügen der Umgebungsvariablen im Konsolenfenster `source ~/.bashrc` aus, damit die Änderungen wirksam werden.

### <a name="macos"></a>macOS

Bearbeiten Sie Ihr „.bash_profile“, und fügen Sie die Umgebungsvariable hinzu:

```bash
export STORAGE_CONNECTION_STRING=<yourconnectionstring>
```

Führen Sie nach dem Hinzufügen der Umgebungsvariablen im Konsolenfenster `source .bash_profile` aus, damit die Änderungen wirksam werden.

## <a name="run-the-sample"></a>Ausführen des Beispiels

Die Beispielanwendung erstellt eine Warteschlange und fügt ihr eine Nachricht hinzu. Die Anwendung prüft die Nachricht zunächst kurz, ohne sie aus der Warteschlange zu entfernen. Danach ruft die Anwendung die Nachricht ab und löscht sie aus der Warteschlange.

### <a name="windows"></a>Windows

Wenn Sie Visual Studio als Editor verwenden, können Sie **F5** drücken, um die Ausführung zu starten. 

Navigieren Sie andernfalls zu Ihrem Anwendungsverzeichnis, und führen Sie die Anwendung mit dem Befehl `dotnet run` aus.

```
dotnet run
```

### <a name="linux"></a>Linux

Navigieren Sie zu Ihrem Anwendungsverzeichnis, und führen Sie die Anwendung mit dem Befehl `dotnet run` aus.

```
dotnet run
```

### <a name="macos"></a>macOS

Navigieren Sie zu Ihrem Anwendungsverzeichnis, und führen Sie die Anwendung mit dem Befehl `dotnet run` aus.

```
dotnet run
```

Die Ausgabe der Beispielanwendung ähnelt dem folgenden Beispiel:

```
Azure Queues - .NET Quickstart sample

Created queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'

Added message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' to queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'
Message insertion time: 2/7/2019 4:30:46 AM +00:00
Message expiration time: 2/14/2019 4:30:46 AM +00:00

Contents of peeked message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7': Hello, World

Message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' becomes visible again at 2/7/2019 4:31:16 AM +00:00

Processed and deleted message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7'

Press any key to delete the sample queue.
```

## <a name="understand-the-sample-code"></a>Grundlagen des Beispielcodes

Erkunden Sie als Nächstes den Beispielcode, um sich mit der Funktionsweise vertraut zu machen.

### <a name="try-parsing-the-connection-string"></a>Versuchtes Analysieren der Verbindungszeichenfolge

In dem Beispiel wird als Erstes überprüft, ob die Umgebungsvariable eine Verbindungszeichenfolge enthält, die analysiert werden kann, um ein Objekt vom Typ [CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount) zu erstellen, das auf das Speicherkonto verweist. Um zu überprüfen, ob die Verbindungszeichenfolge gültig ist, wird in dem Beispiel die Methode [TryParse](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount.tryparse) verwendet. Wenn **TryParse** erfolgreich ist, wird die Variable *storageAccount* initialisiert, und **true** wird zurückgegeben.

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable called storageconnectionstring, on the machine where the application is running.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");

// Check whether the connection string can be parsed.
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with calls to Azure Queues here.
    ...    
}
else
{
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add an environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
}
```

### <a name="create-the-queue"></a>Erstellen der Warteschlange

In dem Beispiel wird zunächst eine Warteschlange erstellt und eine Nachricht hinzugefügt. 

```csharp
// Create a queue called 'quickstartqueues' and append a GUID value so that the queue name 
// is unique in your storage account. 
queue = cloudQueueClient.GetQueueReference("quickstartqueues-" + Guid.NewGuid().ToString());
await queue.CreateAsync();

Console.WriteLine("Created queue '{0}'", queue.Name);
Console.WriteLine();
```

### <a name="add-a-message"></a>Hinzufügen einer Nachricht

Als Nächstes wird am Ende der Warteschlange eine Nachricht hinzugefügt. 

Die Nachricht muss in einem Format vorliegen, das in eine XML-Anforderung mit UTF-8-Codierung eingeschlossen werden kann, und kann bis zu 64 KB groß sein. Für Nachrichten mit Binärdaten empfiehlt Microsoft die Verwendung der Base64-Codierung.

Die maximale Gültigkeitsdauer für eine Nachricht beträgt standardmäßig sieben Tage. Für die Gültigkeitsdauer der Nachricht kann eine beliebige positive Zahl angegeben werden. Soll die Gültigkeit der Nachricht nicht ablaufen, geben Sie „-1“ an.

```csharp
// Create a message and add it to the queue. Set expiration time to 14 days.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await queue.AddMessageAsync(message, new TimeSpan(14,0,0,0), null, null, null);
Console.WriteLine("Added message '{0}' to queue '{1}'", message.Id, queue.Name);
Console.WriteLine("Message insertion time: {0}", message.InsertionTime.ToString());
Console.WriteLine("Message expiration time: {0}", message.ExpirationTime.ToString());
Console.WriteLine();
```

### <a name="peek-a-message-from-the-queue"></a>Einsehen einer Nachricht aus der Warteschlange

Das Beispiel zeigt, wie Sie eine Nachricht aus der Warteschlange einsehen. Beim Einsehen einer Nachricht können Sie den Inhalt der Nachricht lesen. Die Nachricht bleibt jedoch für andere Clients sichtbar und kann anschließend von einem anderen Client abgerufen und verarbeitet werden.

```csharp
// Peek at the message at the front of the queue. Peeking does not alter the message's 
// visibility, so that another client can still retrieve and process it. 
CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

// Display the ID and contents of the peeked message.
Console.WriteLine("Contents of peeked message '{0}': {1}", peekedMessage.Id, peekedMessage.AsString);
Console.WriteLine();
```

### <a name="dequeue-a-message"></a>Entfernen einer Nachricht aus der Warteschlange

Das Beispiel zeigt auch, wie Sie eine Nachricht aus der Warteschlange entfernen. Wenn Sie eine Nachricht aus der Warteschlange entfernen, rufen Sie die Nachricht vom Anfang der Warteschlange ab und machen sie vorübergehend für andere Clients unsichtbar. Eine Nachricht ist standardmäßig 30 Sekunden lang unsichtbar. Während dieser Zeit kann Ihr Code die Nachricht verarbeiten. Löschen Sie die Nachricht direkt nach der Verarbeitung, um das Entfernen der Nachricht aus der Warteschlange abzuschließen und zu verhindern, dass ein anderer Client die gleiche Nachricht aus der Warteschlange entfernt.

Sollte Ihr Code eine Nachricht aufgrund eines Hardware- oder Softwarefehlers nicht verarbeiten können, wird sie nach Ablauf des Unsichtbarkeitszeitraums wieder sichtbar. Ein anderer Client kann daraufhin die gleiche Nachricht abrufen und es erneut versuchen.

```csharp
// Retrieve the message at the front of the queue. The message becomes invisible for 
// a specified interval, during which the client attempts to process it.
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();

// Display the time at which the message will become visible again if it is not deleted.
Console.WriteLine("Message '{0}' becomes visible again at {1}", retrievedMessage.Id, retrievedMessage.NextVisibleTime);
Console.WriteLine();

//Process and delete the message within the period of invisibility.
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Processed and deleted message '{0}'", retrievedMessage.Id);
Console.WriteLine();
```

### <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Das Beispiel löscht die Warteschlange, um die erstellten Ressourcen zu bereinigen. Beim Löschen der Warteschlange werden auch alle darin enthaltenen Nachrichten gelöscht.

```csharp
Console.WriteLine("Press any key to delete the sample queue.");
Console.ReadLine();
Console.WriteLine("Deleting the queue and any messages it contains...");
Console.WriteLine();
if (queue != null)
{
    await queue.DeleteIfExistsAsync();
}
```

## <a name="resources-for-developing-net-applications-with-queues"></a>Ressourcen für die Entwicklung von .NET-Anwendungen mit Warteschlangen

Für die .NET-Entwicklung mit Azure-Warteschlangen stehen folgende zusätzliche Ressourcen zur Verfügung:

### <a name="binaries-and-source-code"></a>Binärdateien und Quellcode

- Laden Sie die NuGet-Pakete für die aktuelle Version der [Azure Storage-Clientbibliothek für .NET](/dotnet/api/overview/azure/storage/client) herunter.
    - [Common](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
    - [Warteschlangen](https://www.nuget.org/packages/Azure.Storage.Queues/)
- Zeigen Sie den [Quellcode der .NET-Clientbibliothek](https://github.com/Azure/azure-storage-net) auf GitHub an.

### <a name="client-library-reference-and-samples"></a>Clientbibliothek – Referenz und Beispiele

- Weitere Informationen zur .NET-Clientbibliothek finden Sie in der [Referenz zur .NET-API](https://docs.microsoft.com/dotnet/api/overview/azure/storage).
- Erkunden Sie [Queue Storage-Beispiele](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=queues), die unter Verwendung der .NET-Clientbibliothek geschrieben wurden.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie mithilfe von .NET einer Warteschlange Nachrichten hinzufügen, Nachrichten aus einer Warteschlange einsehen und Nachrichten aus der Warteschlange entfernen und verarbeiten. 

> [!div class="nextstepaction"]
> [Kommunikation zwischen Anwendungen mit Azure Queue Storage](https://docs.microsoft.com/learn/modules/communicate-between-apps-with-azure-queue-storage/index)

- Weitere Informationen zu .NET Core finden Sie unter [Get started with .NET in 10 minutes](https://www.microsoft.com/net/learn/get-started/) (Einstieg in .NET in 10 Minuten).

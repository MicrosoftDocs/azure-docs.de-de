---
title: Erste Schritte mit Queue Storage und verbundenen Visual Studio-Diensten (WebJob-Projekte) | Microsoft Docs
description: Erste Schritte mit Azure-Warteschlangenspeicher in einem WebJob-Projekt nach dem Herstellen einer Verbindung mit einem Speicherkonto mithilfe von verbundenen Visual Studio-Diensten.
services: storage
author: ghogen
manager: douge
ms.assetid: 5c3ef267-2a67-44e9-ab4a-1edd7015034f
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: f6f1a3a7f0a406e1dbb40f4bfc6a358da7ac68fa
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60391166"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-webjob-projects"></a>Erste Schritte mit Azure-Warteschlangenspeicher und verbundenen Visual Studio-Diensten (WebJob-Projekte)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Übersicht
Dieser Artikel beschreibt die ersten Schritte bei der Verwendung von Azure Queue Storage in einem Visual Studio-Azure-WebJob-Projekt, nachdem Sie über das Visual Studio-Dialogfeld **Add Connected Services** (Verbundene Dienste hinzufügen) ein Azure Storage-Konto erstellt oder referenziert haben. Wenn Sie über das Visual Studio-Dialogfeld **Verbundene Dienste hinzufügen** einem WebJob-Projekt ein Speicherkonto hinzufügen, werden die entsprechenden Azure Storage-NuGet-Pakete installiert, die entsprechenden .NET-Verweise dem Projekt hinzugefügt und die Verbindungszeichenfolgen für das Speicherkonto in der Datei "App.config" aktualisiert.  

Dieser Artikel enthält C#-Codebeispiele, die zeigen, wie das Azure WebJobs-SDK (Version 1.x) mit dem Azure-Warteschlangenspeicherdienst verwendet wird.

Die Warteschlangenspeicherung in Azure ist ein Dienst zur Speicherung großer Anzahlen von Nachrichten, auf die von überall auf der Welt mit authentifizierten Anrufen über HTTP oder HTTPS zugegriffen werden kann. Eine einzelne Warteschlangennachricht kann bis zu 64 KB groß sein, und eine Warteschlange kann Millionen von Nachrichten enthalten. Deren Anzahl ist nur durch die Kapazität des Speicherkontos begrenzt. Weitere Informationen finden Sie unter [Erste Schritte mit Azure Queue Storage mit .NET](../storage/queues/storage-dotnet-how-to-use-queues.md). Weitere Informationen zu ASP.NET finden Sie unter [ASP.NET](https://www.asp.net).

## <a name="how-to-trigger-a-function-when-a-queue-message-is-received"></a>Auslösen einer Funktion, wenn eine Warteschlangennachricht empfangen wird
Zum Schreiben einer Funktion, die das WebJobs-SDK aufruft, wenn eine Warteschlangennachricht empfangen wird, verwenden Sie das **QueueTrigger** -Attribut. Der Attributkonstruktor verwendet einen Zeichenfolgenparameter, der den Namen der Warteschlange angibt. Informationen zum dynamischen Festlegen des Warteschlangennamens finden Sie unter [Gewusst wie: Festlegen von Konfigurationsoptionen](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Zeichenfolgen-Warteschlangennachrichten
Im folgenden Beispiel enthält die Warteschlange eine Zeichenfolgenmeldung, damit **QueueTrigger** auf einen Parameter mit dem Namen **logMessage** angewendet wird, der den Inhalt der Warteschlangennachricht enthält. Die Funktion [schreibt eine Protokollnachricht in das Dashboard](#how-to-write-logs).

```csharp
public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    logger.WriteLine(logMessage);
}
```

Neben **string** kann der Parameter Folgendes sein: ein Bytearray, ein **CloudQueueMessage**-Objekt oder ein POCO-Objekt, das Sie definieren.

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO-Warteschlangennachrichten [(Plain Old CLR Object)](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)
Im folgenden Beispiel enthält die Warteschlangenmeldung JSON-Code für ein **BlobInformation**-Objekt, das eine Eigenschaft **BlobName** enthält. Das SDK deserialisiert das Objekt automatisch.

```csharp
public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
{
    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
}
```

Das SDK verwendet das [Newtonsoft.Json NuGet-Paket](https://www.nuget.org/packages/Newtonsoft.Json) zum Serialisieren und Deserialisieren von Nachrichten. Wenn Sie Warteschlangennachrichten in einem Programm erstellen, das nicht das WebJobs-SDK verwendet, können Sie Code wie im folgenden Beispiel zum Erstellen einer POCO-Warteschlangennachricht schreiben, die das SDK analysieren kann.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

### <a name="async-functions"></a>Asynchrone Funktionen
Die folgende asynchrone Funktion [schreibt ein Protokoll in das Dashboard](#how-to-write-logs).

```csharp
public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    await logger.WriteLineAsync(logMessage);
}
```

Async-Funktion können, wie in der folgenden Abbildung dargestellt, über ein [Abbruchtoken](https://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken)verfügen, das ein Blob kopiert. (Eine Erläuterung des Platzhalters **queueTrigger** finden Sie im Abschnitt [Blobs](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message) .)

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
    CancellationToken token)
{
    await blobInput.CopyToAsync(blobOutput, 4096, token);
}
```

## <a name="types-the-queuetrigger-attribute-works-with"></a>Typen, mit denen das "QueueTrigger"-Attribut arbeitet
Sie können **QueueTrigger** mit den folgenden Typen verwenden:

* **string**
* Einem als JSON serialisierten POCO-Typ
* **byte[]**
* **CloudQueueMessage**

## <a name="polling-algorithm"></a>Abrufalgorithmus
Das SDK implementiert einen zufälligen exponentiellen Backoffalgorithmus, um die Auswirkungen des Abfragens von Warteschlangen im Leerlauf auf Speichertransaktionskosten zu reduzieren.  Wenn eine Nachricht gefunden wird, wartet das SDK zwei Sekunden und prüft dann, ob eine andere Meldung vorhanden ist. Wenn keine Nachricht gefunden wird, wartet es ungefähr vier Sekunden vor dem erneuten Versuch. Nach aufeinander folgenden fehlgeschlagenen Versuchen, eine Warteschlangennachricht abzurufen, erhöht sich die Wartezeit immer mehr, bis die maximale Wartezeit, standardmäßig eine Minute, erreicht ist. [Die maximale Wartezeit ist konfigurierbar.](#how-to-set-configuration-options)

## <a name="multiple-instances"></a>Mehrere Instanzen
Wenn Ihre Web-App auf mehreren virtuellen Instanzen läuft, wird ein kontinuierlicher Webauftrag auf allen Computern ausgeführt, und jeder Computer wartet auf Auslöser und versucht, Funktionen auszuführen. In manchen Szenarien kann dies dazu führen, dass manche Funktionen dieselben Daten doppelt verarbeiten. Die Funktionen sollten daher "idempotent" geschrieben sein, d. h. beim wiederholten Aufrufen mit denselben Eingangsdaten dürfen keine duplizierten Resultate entstehen.  

## <a name="parallel-execution"></a>Parallele Ausführung
Wenn mehrere Funktionen unterschiedliche Warteschlangen überwachen, ruft das SDK diese parallel auf, wenn Nachrichten gleichzeitig empfangen werden.

Das Gleiche gilt auch, wenn mehrere Nachrichten für eine einzige Warteschlange empfangen werden. Das SDK ruft standardmäßig jeweils einen Batch von 16 Warteschlangennachrichten auf und führt die Funktion, die diese verarbeitet, parallel aus. [Die Batchgröße ist konfigurierbar](#how-to-set-configuration-options). Wenn die zu verarbeitende Anzahl die Hälfte der Batchgröße erreicht, ruft das SDK einen weiteren Batch ab und beginnt mit der Verarbeitung dieser Nachrichten. Aus diesem Grund beträgt die maximale Anzahl der pro Funktion verarbeiteten Nachrichten die 1,5-fache Batchgröße. Dieser Grenzwert gilt separat für jede Funktion, die über ein **QueueTrigger** -Attribut verfügt. Wenn Sie keine parallele Ausführung für in einer Warteschlange empfangene Nachrichten wünschen, legen Sie die Batchgröße auf 1 fest.

## <a name="get-queue-or-queue-message-metadata"></a>Abrufen der Warteschlangen-der Warteschlangennchrichtenmetadaten
Sie können die folgenden Nachrichteneigenschaften abrufen, indem Sie Parameter zur Methodensignatur hinzufügen:

* **DateTimeOffset** expirationTime
* **DateTimeOffset** insertionTime
* **DateTimeOffset** nextVisibleTime
* **string** queueTrigger (enthält Nachrichtentext)
* **string** id
* **string** popReceipt
* **int** dequeueCount

Wenn Sie direkt mit der Azure-Speicher-API arbeiten möchten, können Sie auch einen **CloudStorageAccount** -Parameter hinzufügen.

Im folgenden Beispiel werden all diese Metadaten in ein INFO-Anwendungsprotokoll geschrieben. In dem Beispiel enthalten sowohl logMessage als auch queueTrigger den Inhalt der Warteschlangennachricht.

```csharp
public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
    DateTimeOffset expirationTime,
    DateTimeOffset insertionTime,
    DateTimeOffset nextVisibleTime,
    string id,
    string popReceipt,
    int dequeueCount,
    string queueTrigger,
    CloudStorageAccount cloudStorageAccount,
    TextWriter logger)
{
    logger.WriteLine(
        "logMessage={0}\n" +
        "expirationTime={1}\ninsertionTime={2}\n" +
        "nextVisibleTime={3}\n" +
        "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
        "queue endpoint={7} queueTrigger={8}",
        logMessage, expirationTime,
        insertionTime,
        nextVisibleTime, id,
        popReceipt, dequeueCount,
        cloudStorageAccount.QueueEndpoint,
        queueTrigger);
}
```

Nachfolgend finden Sie ein Beispielprotokoll, das vom Beispielcode geschrieben wurde:

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

## <a name="graceful-shutdown"></a>Ordnungsgemäßes Herunterfahren
Eine Funktion, die in einem fortlaufenden WebJob ausgeführt wird, kann einen **CancellationToken** -Parameter annehmen, der es dem Betriebssystem ermöglicht, die Funktion vor dem Beenden des WebJob zu benachrichtigen. Sie können diese Benachrichtigung verwenden, um sicherzustellen, dass die Funktion nicht auf eine Weise unerwartet beendet wird, die die Daten in einem inkonsistenten Zustand hinterlässt.

Das folgende Beispiel zeigt, wie Sie in einer Funktion nach einer bevorstehenden Beendigung eines Webauftrags suchen.

```csharp
public static void GracefulShutdownDemo(
            [QueueTrigger("inputqueue")] string inputText,
            TextWriter logger,
            CancellationToken token)
{
    for (int i = 0; i < 100; i++)
    {
        if (token.IsCancellationRequested)
        {
            logger.WriteLine("Function was cancelled at iteration {0}", i);
            break;
        }
        Thread.Sleep(1000);
        logger.WriteLine("Normal processing for queue message={0}", inputText);
    }
}
```

**Hinweis:** Im Dashboard werden Status und Ausgabe von Funktionen, die beendet wurden, möglicherweise nicht ordnungsgemäß angezeigt.

Weitere Informationen finden Sie unter [Ordnungsgemäßes Herunterfahren von WebJobs](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a name="how-to-create-a-queue-message-while-processing-a-queue-message"></a>Erstellen einer Warteschlangennachricht beim Verarbeiten einer Warteschlangennachricht
Um eine Funktion zu schreiben, die eine neue Warteschlangennachricht erstellt, verwenden Sie das **Queue** -Attribut. Wie bei **QueueTrigger**auch, übergeben Sie den Warteschlangennamen als Zeichenfolge, oder Sie können den [Namen der Warteschlange dynamisch festlegen](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Zeichenfolgen-Warteschlangennachrichten
Im folgenden nicht asynchronen Beispiel wird eine neue Warteschlangennachricht in der Warteschlange mit dem Namen "outputqueue" mit dem gleichen Inhalt wie die Warteschlangennachricht erstellt, die in der Warteschlange mit dem Namen "inputqueue" empfangen wird. (Verwenden Sie für asynchrone Funktionen **IAsyncCollector<T>** , wie weiter unten in diesem Abschnitt gezeigt.)

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] out string outputQueueMessage )
{
    outputQueueMessage = queueMessage;
}
```

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO-Warteschlangennachrichten [(Plain Old CLR Object)](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)
Zum Erstellen einer Warteschlangennachricht, die ein POCO-Objekt anstelle einer Zeichenfolge enthält, übergeben Sie den POCO-Typ als Ausgabeparameter an den **Queue** -Attributkonstruktor.

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
{
    blobInfoOutput = blobInfoInput;
}
```

Das SDK serialisiert das Objekt automatisch an JSON. Es wird immer eine Warteschlangennachricht erstellt, selbst wenn das Objekt Null ist.

### <a name="create-multiple-messages-or-in-async-functions"></a>Erstellen mehrerer Nachrichten oder in asynchronen Funktionen
Um mehrere Nachrichten zu erstellen, legen Sie den Parametertyp für die Ausgabewarteschlange auf **ICollector<T>** oder **IAsyncCollector<T>** fest, wie im folgenden Beispiel gezeigt.

```csharp
public static void CreateQueueMessages(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
    TextWriter logger)
{
    logger.WriteLine("Creating 2 messages in outputqueue");
    outputQueueMessage.Add(queueMessage + "1");
    outputQueueMessage.Add(queueMessage + "2");
}
```

Alle Warteschlangennachrichten werden sofort erstellt, wenn die **Add** -Methode aufgerufen wird.

### <a name="types-that-the-queue-attribute-works-with"></a>Typen, mit denen das Queue-Attribut arbeitet
Sie können das **Queue** -Attribut für die folgenden Parametertypen verwenden:

* **out string** (erstellt eine Warteschlangennachricht, wenn der Parameterwert nicht null ist, sobald die Funktion beendet wird)
* **out byte** (funktioniert wie **string**)
* **out CloudQueueMessage** (funktioniert wie **string**)
* **out POCO** (ein serialisierbarer Typ, erstellt eine Nachricht mit einem NULL-Objekt, wenn der Parameter NULL ist, sobald die Funktion beendet wird)
* **ICollector**
* **IAsyncCollector**
* **CloudQueue** (zum manuellen Erstellen von Nachrichten, die direkt die Azure-Speicher-API verwenden)

### <a name="use-webjobs-sdk-attributes-in-the-body-of-a-function"></a>Verwenden von WebJobs-SDK-Attributen im Hauptteil einer Funktion
Wenn Sie weitere Aufgaben in Ihrer Funktion vor der Verwendung eines Attributs des WebJobs-SDK erledigen möchten, z.B. **Queue**, **Blob** oder **Table**, können Sie die **IBinder**-Schnittstelle verwenden.

Im folgenden Beispiel wird aus einer Nachricht der Eingabewarteschlange eine neue Nachricht mit dem gleichen Inhalt in einer Ausgabewarteschlange erstellt. Der Name der Ausgabewarteschlangenname wird durch Code im Text der Funktion festgelegt.

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
}
```

Sie können die **IBinder**-Schnittstelle auch mit den Attributen **Table** und **Blob** verwenden.

## <a name="how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message"></a>Lesen und Bearbeiten von Blobs beim Verarbeiten einer Warteschlangennachricht
Die Attribute **Blob** und **Table** ermöglichen Ihnen das Lesen und Schreiben von Blobs und Tabellen. Die Beispiele in diesem Abschnitt gelten für Blobs. Codebeispiele, die zeigen, wie Prozesse beim Erstellen oder Aktualisieren von Blobs ausgelöst werden, finden Sie unter [Vorgehensweise zum Verwenden von Azure Blob Storage mit dem WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
<!-- , and for code samples that read and write tables, see [How to use Azure table storage with the WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-tables-how-to.md). -->

### <a name="string-queue-messages-triggering-blob-operations"></a>Zeichenfolge-Warteschlangennachrichten, die Blobvorgänge auslösen
Für eine Warteschlangennachricht, die eine Zeichenfolge enthält, ist **QueueTrigger** ein Platzhalter, der im Parameter **blobPath** des **Blob**-Attributs verwendet werden kann, der den Inhalt der Nachricht enthält.

Im folgenden Beispiel werden **Stream** -Objekte zum Lesen und Schreiben von Blobs verwendet. Die Warteschlangennachricht enthält den Namen eines Blobs, das sich im Container "textblobs" befindet. Eine Kopie des Blobs, an deren Namen "-new" angefügt wird, wird im gleichen Container erstellt.

```csharp
public static void ProcessQueueMessage(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

Der Attributkonstruktor **Blob** verwendet den Parameter **blobPath**, der den Container und den Blobnamen angibt. Weitere Informationen zu diesem Platzhalter finden Sie unter [Verwenden von Azure Blob Storage mit dem WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Wenn das Attribut ein **Stream**-Objekt anpasst, gibt ein anderer Konstruktorparameter den Modus **FileAccess** als Lesen, Schreiben oder Lesen/Schreiben an.

Im folgenden Beispiel wird ein **CloudBlockBlob** -Objekt verwendet, um einen Blob zu löschen. Die Warteschlangennachricht ist der Name des Blobs.

```csharp
public static void DeleteBlob(
    [QueueTrigger("deleteblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
{
    blobToDelete.Delete();
}
```

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO-Warteschlangennachrichten [(Plain Old CLR Object)](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)
Für ein POCO-Objekt, das als JSON-Code in der Warteschlangenmeldung gespeichert wird, können Sie Platzhalter verwenden, mit denen Eigenschaften des Parameters **blobPath** des **Queue**-Attributs benannt werden. Sie können auch Metadateneigenschaftennamen der Warteschlange als Platzhalter verwenden. Siehe [Abrufen der Warteschlange oder von Metadaten der Warteschlangennachricht](#get-queue-or-queue-message-metadata).

Das folgende Beispiel kopiert ein Blob in ein neues Blob mit einer anderen Erweiterung. Die Warteschlangennachricht ist ein **BlobInformation**-Objekt, das die Eigenschaften **BlobName** und **BlobNameWithoutExtension** enthält. Die Eigenschaftsnamen dienen als Platzhalter im Blobpfad für die **Blob** -Attribute.

```csharp
public static void CopyBlobPOCO(
    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

Das SDK verwendet das [Newtonsoft.Json NuGet-Paket](https://www.nuget.org/packages/Newtonsoft.Json) zum Serialisieren und Deserialisieren von Nachrichten. Wenn Sie Warteschlangennachrichten in einem Programm erstellen, das nicht das WebJobs-SDK verwendet, können Sie Code wie im folgenden Beispiel zum Erstellen einer POCO-Warteschlangennachricht schreiben, die das SDK analysieren kann.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

Wenn Sie vor dem Binden eines Blobs an ein Objekt Änderungen an Ihrer Funktion vornehmen müssen, können Sie das Attribut im Hauptteil der Funktion verwenden, wie unter [Verwenden von WebJobs SDK-Attributen im Hauptteil einer Funktion](#use-webjobs-sdk-attributes-in-the-body-of-a-function)beschrieben.

### <a name="types-you-can-use-the-blob-attribute-with"></a>Typen, mit denen das "Blob"-Attribut arbeitet
Das **Blob** -Attribut kann mit den folgenden Typen verwendet werden:

* **Stream** (Lesen oder Schreiben, wird mit dem „FileAccess“-Konstruktorparameter angegeben)
* **TextReader**
* **TextWriter**
* **string** (Lesen)
* **out string** (Schreiben, erstellt ein Blob nur dann, wenn der Parameter bei Rückgabe der Funktion ungleich NULL ist)
* POCO (Lesen)
* POCO (Schreiben; erstellt immer ein Blob, erstellt ein NULL-Objekt , wenn der POCO-Parameter bei Rückgabe der Funktion NULL ist)
* **CloudBlobStream** (Schreiben)
* **ICloudBlob** (Lesen oder Schreiben)
* **CloudBlockBlob** (Lesen oder Schreiben)
* **CloudPageBlob** (Lesen oder Schreiben)

## <a name="how-to-handle-poison-messages"></a>Behandeln von beschädigten Nachrichten
Nachrichten, deren Inhalt bewirkt, dass bei einer Funktion ein Fehler auftritt, werden als *nicht verarbeitbare Nachrichten*bezeichnet. Wenn bei der Funktion ein Fehler auftritt, wird die Warteschlangennachricht nicht gelöscht, sondern schließlich erneut aufgegriffen, wodurch verursacht wird, dass der Zyklus wiederholt wird. Das SDK kann den Zyklus nach einer begrenzten Anzahl von Iterationen automatisch unterbrechen, oder Sie können dies manuell vornehmen.

### <a name="automatic-poison-message-handling"></a>Automatische Behandlung von nicht verarbeitbaren Nachrichten
Das SDK ruft eine Funktion bis zu fünfmal auf, um eine Warteschlangennachricht zu verarbeiten. Wenn der fünfte Versuch fehlschlägt, wird die Nachricht in eine Warteschlange für nicht verarbeitete Nachrichten verschoben. Hinweise zum Konfigurieren der maximalen Anzahl von erneuten Versuchen finden Sie unter [Gewusst wie: Festlegen von Konfigurationsoptionen](#how-to-set-configuration-options).

Die Warteschlange für nicht verarbeitete Nachrichten weist den Namen " *{UrsprünglicherWarteschlangenname}* -poison" auf. Sie können eine Funktion schreiben, um Nachrichten aus der Warteschlange für nicht verarbeitete Nachrichten zu verarbeiten, indem Sie diese protokollieren oder eine Benachrichtigung senden, dass ein manueller Eingriff erforderlich ist.

Im folgenden Beispiel schlägt die **CopyBlob** -Funktion fehl, wenn eine Warteschlangennachricht den Namen eines Blobs enthält, der nicht vorhanden ist. In diesem Fall wird die Nachricht aus der Warteschlange "copyblobqueue" in die Warteschlange "copyblobqueue-poison" verschoben. **ProcessPoisonMessage** protokolliert dann die beschädigte Nachricht.

```csharp
public static void CopyBlob(
    [QueueTrigger("copyblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}

public static void ProcessPoisonMessage(
    [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
{
    logger.WriteLine("Failed to copy blob, name=" + blobName);
}
```

Die folgende Abbildung zeigt die Konsolenausgabe dieser Funktionen, wenn eine nicht verarbeitbare Nachricht verarbeitet wird.

![Konsolenausgabe für nicht verarbeitbare Nachrichten](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### <a name="manual-poison-message-handling"></a>Manuelle Behandlung von nicht verarbeitbaren Nachrichten
Sie erhalten die Anzahl der Male, die eine Nachricht für die Verarbeitung übernommen wurde, indem Sie Ihrer Funktion einen Parameter **int** mit dem Namen **dequeueCount** hinzufügen. Anschließend können Sie die Häufigkeit des Entfernens aus der Warteschlange im Funktionscode überprüfen und Ihre eigene Behandlung von nicht verarbeitbaren Nachrichten ausführen, wenn die Anzahl einen Schwellenwert überschreitet, wie in dem folgendem Beispiel dargestellt.

```csharp
public static void CopyBlob(
    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
    TextWriter logger)
{
    if (dequeueCount > 3)
    {
        logger.WriteLine("Failed to copy blob, name=" + blobName);
    }
    else
    {
        blobInput.CopyTo(blobOutput, 4096);
    }
}
```

## <a name="how-to-set-configuration-options"></a>Gewusst wie: Festlegen von Konfigurationsoptionen
Den Typ **JobHostConfiguration** können Sie verwenden, um die folgenden Konfigurationsoptionen festzulegen:

* Festlegen der SDK-Verbindungszeichenfolgen im Code
* Konfigurieren von Einstellungen für **QueueTrigger** wie der maximalen Anzahl der Entfernungen aus der Warteschlange.
* Rufen Sie Warteschlangennamen aus der Konfiguration ab.

### <a name="set-sdk-connection-strings-in-code"></a>Festlegen von SDK-Verbindungszeichenfolgen im Code
Durch Festlegen der SDK-Verbindungszeichenfolgen im Code können Sie Ihre eigenen Verbindungszeichenfolgennamen in Konfigurationsdateien oder Umgebungsvariablen verwenden, wie im folgenden Beispiel gezeigt.

```csharp
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    var _dashboardConn = ConfigurationManager
        .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    var _serviceBusConn = ConfigurationManager
        .ConnectionStrings["MyServiceBusConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    config.DashboardConnectionString = _dashboardConn;
    config.ServiceBusConnectionString = _serviceBusConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="configure-queuetrigger--settings"></a>Konfigurieren von QueueTrigger-Einstellungen
Sie können die folgenden Einstellungen konfigurieren, die für die Verarbeitung von Warteschlangennachrichten gelten:

* Die maximale Anzahl von Warteschlangennachrichten, die gleichzeitig abgerufen werden, um parallel ausgeführt zu werden (der Standardwert ist 16).
* Die maximale Anzahl von Wiederholungen, bevor eine Warteschlangennachricht an eine Warteschlange mit beschädigten Nachrichten gesendet wird (der Standardwert ist 5).
* Die maximale Wartezeit bis zum erneuten Abrufen, wenn eine Warteschlange leer ist (der Standardwert ist 1 Minute).

Das folgende Beispiel zeigt, wie diese Einstellungen konfiguriert werden können:

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="set-values-for-webjobs-sdk-constructor-parameters-in-code"></a>Festlegen von Werten für WebJobs SDK-Konstruktorparametern im Code
Mitunter möchten Sie einen Warteschlangennamen, einen Blobnamen oder Container oder einen Tabellennamen im Code angeben, anstatt ihn hart zu codieren. Sie möchten z.B. den Warteschlangennamen für **QueueTrigger** in einer Datei oder Umgebungsvariable angeben.

Dies ist möglich, indem Sie ein **NameResolver**-Objekt an den Typ **JobHostConfiguration** übergeben. Sie fügen in Konstruktorparametern von WebJobs-SDK-Attributen spezielle Platzhalter hinzu, die von Prozentzeichen (%) umgeben werden, und Ihr **NameResolver** -Code gibt die tatsächlichen Werte an, die anstelle dieser Platzhalter verwendet werden sollen.

Angenommen, Sie möchten eine Warteschlange mit dem Namen "logqueuetest" in der Testumgebung und eine Warteschlange mit dem Namen "logqueueprod" in einer Produktionsumgebung verwenden. Anstatt eines hartcodierten Warteschlangennamens möchten Sie den Namen eines Eintrags in der Auflistung **appSettings** angeben, der den tatsächlichen Namen der Warteschlange hätte. Wenn der **appSettings** -Schlüssel „logqueue“ ist, könnte die Funktion wie im folgenden Beispiel aussehen.

```csharp
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Die **NameResolver**-Klasse kann dann den Namen der Warteschlange aus **appSettings** abrufen, wie im folgenden Beispiel gezeigt:

```csharp
public class QueueNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

Sie übergeben die **NameResolver**-Klasse an das **JobHost**-Objekt, wie im folgenden Beispiel gezeigt.

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new QueueNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

**Hinweis:** Warteschlangen-, Tabellen- und Blobnamen werden bei jedem Aufruf einer Funktion aufgelöst, doch Blobcontainernamen werden nur beim Starten der Anwendung aufgelöst. Der Blobcontainername kann nicht geändert werden, während der Auftrag ausgeführt wird.

## <a name="how-to-trigger-a-function-manually"></a>Manuelles Auslösen einer Funktion
Um eine Funktion manuell auszulösen, verwenden Sie eine der Methoden **Call** oder **CallAsync** für das **JobHost**-Objekt und das **NoAutomaticTrigger**-Attribut für die Funktion, wie im folgenden Beispiel gezeigt.

```csharp
public class Program
{
    static void Main(string[] args)
    {
        JobHost host = new JobHost();
        host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
    }

    [NoAutomaticTrigger]
    public static void CreateQueueMessage(
        TextWriter logger,
        string value,
        [Queue("outputqueue")] out string message)
    {
        message = value;
        logger.WriteLine("Creating queue message: ", message);
    }
}
```

## <a name="how-to-write-logs"></a>Gewusst wie: Schreiben von Protokollen
Das Dashboard zeigt Protokolle an zwei Stellen: auf der Seite für den Webauftrag und auf der Seite für eines bestimmten Webauftragsaufrufs.

![Protokolle auf Webauftragsseiten](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![Protokolle auf Funktionsaufrufseiten](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Methoden für die Konsolenausgabe, die Sie in einer Funktion oder in der **Main()** -Methode aufrufen, werden auf der Dashboard-Seite für den Webauftrag und nicht auf der Seite eines bestimmten Methodenaufrufs angezeigt. Die Ausgabe des TextWriter-Objekts, die Sie von einem Parameter in Ihrer Methodensignatur erhalten, wird auf der Dashboard-Seite eines bestimmten Methodenaufrufs angezeigt.

Die Konsolenausgabe kann nicht an einen bestimmten Methodenaufruf geknüpft werden, da die Konsole als Singlethread ausgeführt wird, während viele Aufgaben ggf. gleichzeitig ausgeführt werden. Deshalb versieht das SDK jeden Funktionsaufruf mit einem eigenen eindeutigen Protokollschreibobjekt.

Verwenden Sie zum Schreiben von [Ablaufverfolgungsprotokollen für die Anwendung](../app-service/troubleshoot-dotnet-visual-studio.md#logsoverview) **Console.Out** (erstellt als INFO markierte Protokolle) und **Console.Error** (erstellt als ERROR markierte Protokolle). Eine Alternative ist die [Verwendung von Trace oder TraceSource](https://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), um zusätzlich zu INFO und FEHLER die Stufen AUSFÜHRLICH, WARNUNG und KRITISCH bereitzustellen. Ablaufverfolgungsprotokolle von Anwendungen werden in den Web-App-Protokolldateien, Azure-Tabellen oder Azure-Blobs angezeigt, je nachdem, wie Sie Ihre Azure-Web-App konfigurieren. Wie bei sämtlichen Konsolenausgaben werden die 100 letzten Anwendungsprotokolle auch auf der Seite „Dashboard“ für den Webauftrag und nicht auf der Seite für die Funktionsaufruf angezeigt.

Die Konsolenausgabe wird nur im Dashboard angezeigt, wenn das Programm in einem Azure-Webauftrag ausgeführt wird, und nicht, wenn die Anwendung lokal oder in einer anderen Umgebung ausgeführt wird.

Sie können die Protokollierung deaktivieren, indem Sie die Dashboard-Verbindungszeichenfolge auf null festlegen. Weitere Informationen finden Sie unter [Gewusst wie: Festlegen von Konfigurationsoptionen](#how-to-set-configuration-options).

Im folgenden Beispiel sind mehrere Möglichkeiten zum Schreiben von Protokollen dargestellt:

```csharp
public static void WriteLog(
    [QueueTrigger("logqueue")] string logMessage,
    TextWriter logger)
{
    Console.WriteLine("Console.Write - " + logMessage);
    Console.Out.WriteLine("Console.Out - " + logMessage);
    Console.Error.WriteLine("Console.Error - " + logMessage);
    logger.WriteLine("TextWriter - " + logMessage);
}
```

Im Dashboard des WebJobs SDK wird die Ausgabe aus dem **TextWriter**-Objekt angezeigt, wenn Sie die Seite für einen bestimmten Funktionsaufruf anzeigen und **Toggle Output** auswählen:

![Aufruflink](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![Protokolle auf Funktionsaufrufseiten](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Im Dashboard des WebJobs SDK werden die letzten 100 Zeilen der Konsolenausgabe angezeigt, wenn Sie zur Seite für den WebJob (nicht für den Funktionsaufruf) wechseln und **Toggle Output**auswählen.

![Toggle Output](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

In einem fortlaufenden WebJob werden Anwendungsprotokolle in „/data/jobs/continuous/ *{Webjobname}* /job_log.txt“ im Dateisystem der Web-App angezeigt.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

In einem Azure-Blob sehen die Anwendungsprotokolle folgendermaßen aus: 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!, 2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!, 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

Und in einer Azure-Tabelle sehen Protokolle für **Console.Out** und **Console.Error** wie folgt aus:

![Informationsprotokoll in Tabelle](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![Fehlerprotokoll in Tabelle](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel wurden Codebeispiele bereitgestellt, in denen veranschaulicht wird, wie häufige Szenarien für das Arbeiten mit Azure-Warteschlangen behandelt werden. Weitere Informationen zur Verwendung von Azure WebJobs und des WebJobs SDK finden Sie unter [Dokumentationsressourcen für Azure WebJobs](https://go.microsoft.com/fwlink/?linkid=390226).


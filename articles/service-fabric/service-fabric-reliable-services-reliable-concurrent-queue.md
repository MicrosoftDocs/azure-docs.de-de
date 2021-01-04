---
title: ReliableConcurrentQueue in Azure Service Fabric
description: ReliableConcurrentQueue ist eine Warteschlange mit hohem Durchsatz, die paralleles Einreihen in die und Entfernen aus der Warteschlange ermöglicht.
ms.topic: conceptual
ms.date: 5/1/2017
ms.openlocfilehash: 423ef3d1898176d7c25c596ad186a9c000108aa4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997119"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Einführung in ReliableConcurrentQueue in Azure Service Fabric
ReliableConcurrentQueue ist eine asynchrone, transaktionsbasierte und replizierte Warteschlange, die ein hohes Maß an Parallelität für Einreihungs- und Entfernungsvorgänge bietet. Die Warteschlange bietet hohen Durchsatz und niedrige Latenz, indem die strikte FIFO-Reihenfolge von [ReliableQueue](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1?view=azure-dotnet#microsoft_servicefabric_data_collections_ireliablequeue_1) gelockert wird. Stattdessen wird eine Reihenfolge nach dem Prinzip „beste Leistung“ angewendet.

## <a name="apis"></a>APIs

|ConcurrentQueue                |ReliableConcurrentQueue                                         |
|--------------------------------|------------------------------------------------------------------|
| void Enqueue(T item)           | Task EnqueueAsync(ITransaction tx, T item)                       |
| bool TryDequeue(out T result)  | Task< ConditionalValue < T > > TryDequeueAsync(ITransaction tx)  |
| int Count()                    | long Count()                                                     |

## <a name="comparison-with-reliable-queue"></a>Vergleich mit [ReliableQueue](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1?view=azure-dotnet#microsoft_servicefabric_data_collections_ireliablequeue_1)

ReliableConcurrentQueue wird als Alternative zu [ReliableQueue](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1?view=azure-dotnet#microsoft_servicefabric_data_collections_ireliablequeue_1) angeboten. Dieser Warteschlangentyp sollte verwendet werden, wenn eine strikte FIFO-Reihenfolge nicht erforderlich ist, da eine garantierte FIFO-Reihenfolge Abstriche bei der Parallelität bedeutet.  [ReliableQueue](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1?view=azure-dotnet#microsoft_servicefabric_data_collections_ireliablequeue_1) verwendet Sperren, um die FIFO-Reihenfolge zu erzwingen. Dabei darf maximal je eine Transaktion gleichzeitig in die Warteschlange eingereiht und aus der Warteschlange entfernt werden. Im Vergleich dazu lockert ReliableConcurrentQueue die Einschränkung hinsichtlich der Reihenfolge und macht es möglich, dass sich die Einreihungs- und Entfernungsvorgänge einer beliebigen Anzahl von gleichzeitigen Transaktionen überschneiden. Es wird eine Reihenfolge nach dem Prinzip „beste Leistung“ angewendet, die relative Reihenfolge von zwei Werten in ReliableConcurrentQueue kann jedoch nie garantiert werden.

ReliableConcurrentQueue bietet einen höheren Durchsatz und eine niedrigere Latenz als [ReliableQueue](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1?view=azure-dotnet#microsoft_servicefabric_data_collections_ireliablequeue_1), wenn mehrere parallel ausgeführte Transaktionen Einreihungs- oder Entfernungsvorgänge durchführen.

Ein Beispiel für ReliableConcurrentQueue ist ein Szenario mit [Nachrichtenwarteschlangen](https://en.wikipedia.org/wiki/Message_queue). In diesem Szenario erstellt mindestens ein Nachrichtenproducer Elemente in der Warteschlange und fügt der Warteschlange Elemente hinzu. Gleichzeitig ruft mindestens ein Nachrichtenconsumer Nachrichten aus der Warteschlange ab und verarbeitet sie. Mehrere Producer und Consumer können unabhängig voneinander arbeiten und verwenden gleichzeitige Transaktionen, um die Warteschlange zu verarbeiten.

## <a name="usage-guidelines"></a>Verwendungsrichtlinien
* Die Warteschlange erwartet, dass die Elemente in der Warteschlange eine kurze Beibehaltungsdauer aufweisen. Anders gesagt: Die Elemente verbleiben nicht lange in der Warteschlange.
* Die Warteschlange garantiert keine strikte FIFO-Reihenfolge.
* Die Warteschlange liest die eigenen Schreibvorgänge nicht. Wenn ein Element innerhalb einer Transaktion in eine Warteschlange eingereiht wird, ist es für einen Dequeuer innerhalb derselben Transaktion nicht sichtbar.
* Entfernungsvorgänge sind nicht voneinander isoliert. Wenn Element *A* in Transaktion *txnA* aus der Warteschlange entfernt wird, ist Element *A* für eine gleichzeitig ausgeführte Transaktion *txnB* nicht sichtbar, selbst wenn für *txnA* kein Commit ausgeführt wurde.  Wenn *txnA* abgebrochen wird, wird *A* sofort für *txnB* sichtbar.
* Das *TryPeekAsync*-Verhalten kann implementiert werden, indem *TryDequeueAsync* verwendet und die Transaktion dann abgebrochen wird. Ein Beispiel für dieses Verhalten finden Sie im Abschnitt „Muster für die Programmierung“.
* „Count“ ist nicht transaktional und kann verwendet werden, um die ungefähre Anzahl von Elementen in der Warteschlange zu ermitteln. Der Wert bezieht sich aber auf einen Zeitpunkt und ist daher nicht zuverlässig.
* Die aus der Warteschlange entfernten Elemente sollten nicht umfangreich verarbeitet werden, während die Transaktion aktiv ist. So werden Transaktionen mit langer Ausführungsdauer vermieden, die sich negativ auf die Systemleistung auswirken können.

## <a name="code-snippets"></a>Codeausschnitte
Sehen wir uns einige Codeausschnitte und die zugehörigen erwarteten Ausgaben an. Die Ausnahmebehandlung wird in diesem Abschnitt ignoriert.

### <a name="instantiation"></a>Instanziierung
Das Erstellen einer Instanz einer zuverlässigen gleichzeitigen Warteschlange ähnelt allen anderen zuverlässigen Sammlungen.

```csharp
IReliableConcurrentQueue<int> queue = await this.StateManager.GetOrAddAsync<IReliableConcurrentQueue<int>>("myQueue");
```

### <a name="enqueueasync"></a>EnqueueAsync
Hier finden Sie einige Codeausschnitte für die Verwendung von EnqueueAsync sowie die erwarteten Ausgaben.

- *Fall 1: einzelner Einreihungstask*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Wir gehen davon aus, dass der Task erfolgreich abgeschlossen wurde und es keine gleichzeitigen Transaktionen gab, die zu einer Änderung der Warteschlange geführt haben. Es ist zu erwarten, dass die Warteschlange die Elemente in einer der folgenden Reihenfolgen enthält:

> 10, 20
> 
> 20, 10


- *Fall 2: paralleler Einreihungstask*

```
// Parallel Task 1
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}

// Parallel Task 2
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 30, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 40, cancellationToken);

    await txn.CommitAsync();
}
```

Wir gehen davon aus, dass die Tasks erfolgreich abgeschlossen wurden, die Tasks parallel ausgeführt wurden und es keine gleichzeitigen Transaktionen gab, die zu einer Änderung der Warteschlange geführt haben. Ein Rückschluss auf die Reihenfolge der Elemente in der Warteschlange ist nicht möglich. Für diesen Codeausschnitt können die Elemente in jeder der 4! möglichen Reihenfolgen auftreten.  Die Warteschlange versucht, die ursprüngliche Reihenfolge der Elemente (in der sie eingereiht wurden) beizubehalten, wird jedoch aufgrund von Fehlern oder gleichzeitigen Vorgängen möglicherweise gezwungen, die Reihenfolge zu ändern.


### <a name="dequeueasync"></a>DequeueAsync
Hier finden Sie einige Codeausschnitte für die Verwendung von „TryDequeueAsync“ sowie die erwarteten Ausgaben. Wir gehen davon aus, dass die Warteschlange bereits mit den folgenden Elementen aufgefüllt wurde:
> 10, 20, 30, 40, 50, 60

- *Fall 1: einzelner Entfernungstask*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Wir gehen davon aus, dass der Task erfolgreich abgeschlossen wurde und es keine gleichzeitigen Transaktionen gab, die zu einer Änderung der Warteschlange geführt haben. Da ein Rückschluss auf die Reihenfolge der Elemente in der Warteschlange nicht möglich ist, können alle drei Elemente in beliebiger Reihenfolge entfernt werden. Die Warteschlange versucht, die ursprüngliche Reihenfolge der Elemente (in der sie eingereiht wurden) beizubehalten, wird jedoch aufgrund von Fehlern oder gleichzeitigen Vorgängen möglicherweise gezwungen, die Reihenfolge zu ändern.  

- *Fall 2: paralleler Entfernungstask*

```
// Parallel Task 1
List<int> dequeue1;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}

// Parallel Task 2
List<int> dequeue2;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}
```

Wir gehen davon aus, dass die Tasks erfolgreich abgeschlossen wurden, die Tasks parallel ausgeführt wurden und es keine gleichzeitigen Transaktionen gab, die zu einer Änderung der Warteschlange geführt haben. Da ein Rückschluss auf die Reihenfolge der Elemente in der Warteschlange nicht möglich ist, enthalten die Listen *dequeue1* und *dequeue2* jeweils zwei beliebige Elemente in beliebiger Reihenfolge.

Ein und dasselbe Element wird *nicht* in beiden Listen aufgeführt. Wenn daher „dequeue1“ die Elemente *10*, *30* enthält, befinden sich *20*, *40* in „dequeue2“.

- *Fall 3: Reihenfolge beim Entfernen aus der Warteschlange mit Transaktionsabbruch*

Wenn eine Transaktion abgebrochen wird, während Elemente aus der Warteschlange entfernt werden, werden die Elemente wieder an der Spitze der Warteschlange eingereiht. Die Reihenfolge, in der die Elemente wieder an der Spitze der Warteschlange eingereiht werden, ist nicht garantiert. Betrachten wir folgenden Code:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Wir gehen davon aus, dass die Elemente in der folgenden Reihenfolge aus der Warteschlange entfernt wurden:
> 10, 20

Wenn wir die Transaktion abbrechen, werden die Elemente in einer beliebigen der folgenden Reihenfolgen wieder an der Spitze der Warteschlange eingereiht:
> 10, 20
> 
> 20, 10

Dasselbe gilt für alle Fälle, in denen für eine Transaktion kein *Commit* ausgeführt werden konnte.

## <a name="programming-patterns"></a>Muster für die Programmierung
In diesen Abschnitt betrachten wir einige Muster für die Programmierung, die bei der Verwendung von ReliableConcurrentQueue nützlich sein können.

### <a name="batch-dequeues"></a>Entfernen aus der Warteschlange im Batch
Ein empfohlenes Programmiermuster besteht darin, dass der Consumertask Elemente nicht nacheinander aus der Warteschlange entfernt, sondern den Entfernungsvorgang im Batch ausführt. Der Benutzer kann Verzögerungen zwischen jedem Batch oder die Batchgröße drosseln. Der folgende Codeausschnitt zeigt das Programmiermuster. Beachten Sie, dass in diesem Beispiel die Verarbeitung nach dem Commit der Transaktion erfolgt. Wenn also während der Verarbeitung ein Fehler auftritt, gehen die nicht verarbeiteten Elemente ohne Verarbeitung verloren.  Alternativ dazu kann die Verarbeitung auch innerhalb des Geltungsbereichs der Transaktion erfolgen. Dies kann sich jedoch negativ auf die Leistung auswirken und erfordert eine Behandlung der Elemente, die bereits verarbeitet wurden.

```
int batchSize = 5;
long delayMs = 100;

while(!cancellationToken.IsCancellationRequested)
{
    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        for(int i = 0; i < batchSize; ++i)
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
            else
            {
                // else break the for loop
                break;
            }
        }

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }

    int delayFactor = batchSize - processItems.Count;
    await Task.Delay(TimeSpan.FromMilliseconds(delayMs * delayFactor), cancellationToken);
}
```

### <a name="best-effort-notification-based-processing"></a>Benachrichtigungsbasierte Verarbeitung für eine Reihenfolge nach dem Prinzip „beste Leistung“
Ein weiteres interessantes Programmiermuster verwendet die Count-API. Hier können wir eine benachrichtigungsbasierte Verarbeitung für eine Reihenfolge nach dem Prinzip „beste Leistung“ für die Warteschlange implementieren. Mithilfe von „Count“ kann ein Einreihungs- oder Entfernungstask für die Warteschlange gedrosselt werden.  Beachten Sie, dass die Verarbeitung, ebenso wie im vorherigen Beispiel, außerhalb der Transaktion erfolgt. Daher gehen nicht verarbeitete Elemente möglicherweise verloren, wenn bei der Verarbeitung ein Fehler auftritt.

```
int threshold = 5;
long delayMs = 1000;

while(!cancellationToken.IsCancellationRequested)
{
    while (this.Queue.Count < threshold)
    {
        cancellationToken.ThrowIfCancellationRequested();

        // If the queue does not have the threshold number of items, delay the task and check again
        await Task.Delay(TimeSpan.FromMilliseconds(delayMs), cancellationToken);
    }

    // If there are approximately threshold number of items, try and process the queue

    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
        } while (processItems.Count < threshold && ret.HasValue);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
}
```

### <a name="best-effort-drain"></a>Leerung nach dem Prinzip „beste Leistung“
Aufgrund der Gleichzeitigkeit der Datenstruktur kann eine Leerung der Warteschlange nicht garantiert werden.  Es ist möglich, dass (selbst wenn derzeit keine Benutzervorgänge in der Warteschlange ausgeführt werden) ein bestimmter Aufruf von TryDequeueAsync kein Element zurückgibt, das zuvor in die Warteschlange eingereiht und commitet wurde.  Es ist garantiert, dass das eingereihte Element *letztlich* für die Entfernung aus der Warteschlange sichtbar wird. Ohne Out-of-Band-Kommunikationsmechanismus kann ein unabhängiger Consumer jedoch nicht wissen, dass die Warteschlange einen stabilen Zustand erreicht hat, auch wenn alle Producer angehalten wurden und keine neuen Einreihungsvorgänge zulässig sind. Daher wird beim Leerungsvorgang das Prinzip „beste Leistung“ angewendet, wie unten implementiert.

Der Benutzer muss alle weiteren Producer- und Consumertasks anhalten und darauf warten, dass sämtliche noch ausgeführten Transaktionen bestätigt oder abgebrochen werden, bevor er versucht, die Warteschlange zu leeren.  Wenn der Benutzer die erwartete Anzahl von Elementen in der Warteschlange kennt, kann er eine Benachrichtigung einrichten, die signalisiert, dass alle Elemente aus der Warteschlange entfernt wurden.

```
int numItemsDequeued;
int batchSize = 5;

ConditionalValue ret;

do
{
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if(ret.HasValue)
            {
                // Buffer the dequeues
                processItems.Add(ret.Value);
            }
        } while (ret.HasValue && processItems.Count < batchSize);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
} while (ret.HasValue);
```

### <a name="peek"></a>„Peek“
ReliableConcurrentQueue stellt die *TryPeekAsync*-API nicht bereit. Benutzer können die Peek-Semantik abrufen, indem sie *TryDequeueAsync* verwenden und die Transaktion dann abbrechen. In diesem Beispiel werden Entfernungsvorgänge nur verarbeitet, wenn der Wert des Elements größer als *10* ist.

```
using (var txn = this.StateManager.CreateTransaction())
{
    ConditionalValue ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);
    bool valueProcessed = false;

    if (ret.HasValue)
    {
        if (ret.Value > 10)
        {
            // Process the item
            Console.WriteLine("Value : " + ret.Value);
            valueProcessed = true;
        }
    }

    if (valueProcessed)
    {
        await txn.CommitAsync();    
    }
    else
    {
        await txn.AbortAsync();
    }
}
```

## <a name="must-read"></a>Wichtige Informationen
* [Reliable Services – Schnellstart](service-fabric-reliable-services-quick-start.md)
* [Arbeiten mit Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Reliable Services – Benachrichtigungen](service-fabric-reliable-services-notifications.md)
* [Sichern und Wiederherstellen von Reliable Services (Notfallwiederherstellung)](service-fabric-reliable-services-backup-restore.md)
* [Konfigurieren des Reliable State Managers](service-fabric-reliable-services-configuration.md)
* [Erste Schritte mit Web-API-Diensten von Service Fabric](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Erweiterte Verwendung des Reliable Services-Programmiermodells](./service-fabric-reliable-services-lifecycle.md)
* [Entwicklerreferenz für Reliable Collections](/dotnet/api/microsoft.servicefabric.data.collections?view=azure-dotnet#microsoft_servicefabric_data_collections)

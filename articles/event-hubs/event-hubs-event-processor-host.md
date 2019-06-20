---
title: 'Empfangen von Ereignissen unter Verwendung von Event Processor Host: Azure Event Hubs | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt den Event Processor Host in Azure Event Hubs, der die parallele Verwaltung von Prüfpunkten, Leases und das Lesen von Ereignissen vereinfacht.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 26f0abb48ba268f79167ed5d00e4f96d8b5e5998
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60821871"
---
# <a name="receive-events-from-azure-event-hubs-using-event-processor-host"></a>Empfangen von Ereignissen von Azure Event Hubs mithilfe von Event Processor Host

Azure Event Hubs ist ein leistungsfähiger Dienst zur Erfassung von Telemetriedaten, der zum Streamen von Millionen von Ereignissen zu geringen Kosten verwendet werden kann. In diesem Artikel wird beschrieben, wie erfasste Ereignisse mithilfe des *Ereignisprozessorhosts* (Event Processor Host, EPH) genutzt und verarbeitet werden. Dabei handelt es sich um einen intelligenten Consumer-Agent, der die Verwaltung von Prüfpunkten, Leasing und parallelen Ereignislesern vereinfacht.  

Der Schlüssel zur Skalierung in Event Hubs ist das Konzept der partitionierten Consumer. Im Gegensatz zum Muster der [konkurrierenden Consumer](https://msdn.microsoft.com/library/dn568101.aspx) ermöglicht das Muster der partitionierten Consumer hohe Skalierbarkeit durch Beseitigen des Konfliktengpasses und Vereinfachen der End-to-End-Parallelität.

## <a name="home-security-scenario"></a>Szenario zur Haussicherheit

Als Beispielszenario soll ein Unternehmen für Alarmsysteme dienen, das 100.000 Häuser überwacht. Jede Minute gehen Daten von verschiedenen Sensoren und Meldern im Unternehmen ein, z.B. von Bewegungsmeldern, Tür- und Fensteröffnungsmeldern oder Glasbruchmeldern, die in jedem Haus montiert sind. Das Unternehmen stellt für Kunden eine Website zum Überwachen der Aktivitäten im Haus nahezu in Echtzeit bereit.

Jeder Melder überträgt Daten an einen Event Hub. Der Event Hub ist mit 16 Partitionen konfiguriert. Auf der Consumerseite benötigen Sie einen Mechanismus, der diese Ereignisse lesen und konsolidieren (filtern, aggregieren usw.) und das Aggregat in einem Speicherblob sichern kann, das dann in eine benutzerfreundliche Webseite übertragen wird.

## <a name="write-the-consumer-application"></a>Schreiben der Consumeranwendung

Beim Entwerfen des Consumers in einer verteilten Umgebung muss das Szenario die folgenden Anforderungen erfüllen:

1. **Skalierung**: Erstellen mehrerer Consumer, wobei jeder Consumer die Besitzrechte für das Lesen einiger Event Hubs-Partitionen übernimmt.
2. **Lastenausgleich**: Dynamisches Erhöhen oder Verringern der Consumer. Wenn beispielsweise in jedem Haus ein neuer Melder (z.B. ein Kohlenmonoxidmelder) montiert wird, erhöht sich die Anzahl der Ereignisse. In diesem Fall erhöht der (menschliche) Bediener die Anzahl der Consumerinstanzen. Anschließend kann der Pool von Consumern die Anzahl der Partitionen in ihrem Besitz neu ausgleichen, um die Last auch auf die neu hinzugefügten Consumer zu verteilen.
3. **Nahtloses Fortsetzen nach Fehlern**: Wenn bei einem Consumer (**Consumer A**) ein Fehler auftritt (der virtuelle Computer, der den Consumer hostet, stürzt z.B. plötzlich ab), müssen andere Consumer die Partitionen im Besitz von **Consumer A** aufnehmen und fortfahren können. Außerdem muss der Fortsetzungspunkt, der als *Prüfpunkt* oder *Offset* bezeichnet wird, genau an dem Punkt oder etwas früher liegen, an dem der Fehler bei **Consumer A** aufgetreten ist.
4. **Nutzen von Ereignissen**: Während die drei vorherigen Punkte die Verwaltung des Consumers betreffen, ist außerdem Code zum Nutzen und sinnvollen Verarbeiten der Ereignisse erforderlich, z.B. Aggregieren und Hochladen in Blob Storage.

Damit Sie dafür nicht Ihre eigene Lösung entwickeln müssen, umfasst Event Hubs diese Funktionalität über die [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor)-Schnittstelle und die [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)-Klasse.

## <a name="ieventprocessor-interface"></a>IEventProcessor-Schnittstelle

Zunächst implementieren die Consumeranwendungen die Schnittstelle [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor), die über vier Methoden verfügt: [OpenAsync, CloseAsync, ProcessErrorAsync und ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods). Diese Schnittstelle enthält den eigentlichen Code für die Nutzung der von Event Hubs gesendeten Ereignisse. Der folgende Code ist ein Beispiel für eine einfache Implementierung:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
       Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
       return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
       Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
       return Task.CompletedTask;
     }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
       Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
       return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
       foreach (var eventData in messages)
       {
          var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
             Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
       }
       return context.CheckpointAsync();
    }
}
```

Anschließend wird eine [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)-Instanz instanziiert. Je nach Überladung werden beim Erstellen der [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)-Instanz im Konstruktor die folgenden Parameter verwendet:

- **hostName:** der Name der einzelnen Consumerinstanzen. Jede Instanz von **EventProcessorHost** muss innerhalb einer Consumergruppe einen eindeutigen Wert für diese Variable enthalten. Sie sollten diesen Wert daher nicht hartcodieren.
- **eventHubPath:** Der Name des Event Hubs.
- **consumerGroupName:** Event Hubs verwendet **$Default** als Namen für die Standardconsumergruppe. Es empfiehlt sich jedoch, eine Consumergruppe für den spezifischen Verarbeitungsaspekt zu erstellen.
- **eventHubConnectionString:** Die Verbindungszeichenfolge für den Event Hub, die über das Azure-Portal abgerufen werden kann. Diese Verbindungszeichenfolge muss über Berechtigungen zum **Lauschen** für den Event Hub verfügen.
- **storageConnectionString:** Das für die interne Ressourcenverwaltung verwendete Speicherkonto.

Schließlich registrieren die Consumer die [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)-Instanz bei dem Event Hubs-Dienst. Die Registrierung einer Ereignisprozessorklasse mit einer Instanz von EventProcessorHost startet die Ereignisverarbeitung. Durch die Registrierung wird der Event Hubs-Dienst angewiesen, dass eine Verarbeitung der Ereignisse aus einigen zugehörigen Partitionen durch die Consumeranwendung zu erwarten ist, uns dass der [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor)-Implementierungscode bei jedem Übertragen von zu verarbeitenden Ereignissen aufgerufen werden soll. 


### <a name="example"></a>Beispiel

Angenommen, fünf virtuelle Computer sind für Consumerereignisse zugeordnet, und die eigentlichen Nutzungsvorgänge erfolgen über eine einfache Konsolenanwendung auf jedem virtuellen Computer. Jede Konsolenanwendung erstellt dann eine [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)-Instanz, und registriert sie beim Event Hubs-Dienst.

In diesem Beispielszenario sind den fünf **EventProcessorHost**-Instanzen z.B. 16 Partitionen zugeordnet. Einige **EventProcessorHost**-Instanzen besitzen eventuell mehr Partitionen als andere. Für jede Partition, die im Besitz einer **EventProcessorHost**-Instanz ist, wird eine Instanz der `SimpleEventProcessor`-Klasse erstellt. Daher gibt es insgesamt 16 Instanzen der `SimpleEventProcessor`-Klasse, wobei eine jeweils jeder Partition zugewiesen ist.

Dieses Beispiel ist in der folgenden Aufstellung zusammengefasst:

- 16 Event Hubs-Partitionen
- 5 virtuelle Computer, 1 Consumeranwendung (z.B. „Consumer.exe“) auf jedem virtuellen Computer
- 5 registrierte EPH-Instanzen, jeweils 1 auf jedem virtuellen Computer pro „Consumer.exe“
- 16 von den 5 EPH-Instanzen erstellte `SimpleEventProcessor`-Objekte
- 1 „Consumer.exe“-App kann 4 `SimpleEventProcessor`-Objekte enthalten, da die 1 EPH-Instanz 4 Partitionen besitzen kann.

## <a name="partition-ownership-tracking"></a>Nachverfolgen des Partitionsbesitzes

Der Besitz einer Partition durch eine EPH-Instanz (oder einen Consumer) wird über das Azure Storage-Konto nachverfolgt, das für die Nachverfolgung angegeben wird. Sie können die Nachverfolgung wie folgt als einfache Tabelle visualisieren. Sie können die tatsächliche Implementierung anzeigen, indem Sie die Blobs unter dem angegebenen Storage-Konto überprüfen:

| **Name der Consumergruppe** | **Partitions-ID** | **Hostname (Besitzer)** | **Uhrzeit der Leaseübernahme (oder Besitzübernahme)** | **Offset in der Partition (Prüfpunkt)** |
| --- | --- | --- | --- | --- |
| $Default | 0 | Consumer\_VM3 | 2018-04-15T01:23:45 | 156 |
| $Default | 1 | Consumer\_VM4 | 2018-04-15T01:22:13 | 734 |
| $Default | 2 | Consumer\_VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Default | 15 | Consumer\_VM3 | 2018-04-15T01:22:56 | 976 |

In diesem Beispiel übernimmt jeder Host den Besitz einer Partition für eine bestimmte Dauer (Leasedauer). Wenn bei einem Host ein Fehler auftritt (virtueller Computer wird heruntergefahren), läuft die Lease ab. Andere Hosts versuchen, den Besitz der Partition zu übernehmen. Einer der Hosts ist erfolgreich. Bei diesem Prozess wird die Lease für die Partition mit einem neuen Besitzer zurückgesetzt. Auf diese Weise kann nur jeweils ein einzelner Leser Daten von einer bestimmten Partition in einer Consumergruppe lesen.

## <a name="receive-messages"></a>Empfangen von Nachrichten

Bei jedem Aufruf von [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) wird eine Sammlung von Ereignissen bereitgestellt. Die Verarbeitung dieser Ereignisse liegt in Ihrer Verantwortung. Wenn Sie sicherstellen möchten, dass der Prozessorhost jede Nachricht mindestens einmal verarbeitet, müssen Sie Ihren eigenen Wiederholungscode schreiben. Lassen Sie bei nicht verarbeitbaren Nachrichten jedoch Vorsicht walten.

Es wird empfohlen, relativ schnell vorzugehen, möglichst wenige Verarbeitungsvorgänge durchzuführen. Verwenden Sie stattdessen Consumergruppen. Wenn Ereignisse in den Speicher geschrieben und Routingvorgänge durchgeführt werden sollen, empfiehlt es sich, zwei Consumergruppen zu verwenden und zwei [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor)-Implementierungen separat auszuführen.

Zu einem Zeitpunkt während der Verarbeitung möchten Sie möglicherweise nachverfolgen, welche Daten Sie gelesen und abgeschlossen haben. Die Nachverfolgung ist wichtig: Wenn Sie das Lesen wieder aufnehmen, müssen Sie dann nicht zum Anfang des Datenstroms zurückkehren. In [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) wird diese Nachverfolgung durch Verwenden von *Prüfpunkten* vereinfacht. Ein Prüfpunkt ist eine Position (oder ein Offset) für eine bestimmte Partition in einer bestimmten Consumergruppe, bis zu der Sie die Nachrichten bereits verarbeitet haben. Das Markieren eines Prüfpunkts in **EventProcessorHost** erfolgt durch Aufrufen der [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync)-Methode für das [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext)-Objekt. Dieser Vorgang erfolgt innerhalb der [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)-Methode, kann jedoch auch in [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) durchgeführt werden.

## <a name="checkpointing"></a>Setzen von Prüfpunkten

Die [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync)-Methode verfügt über zwei Überladungen: Die erste, ohne Parameter, setzt Prüfpunkte für den Offset des höchsten Ereignisses in der von [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) zurückgegebenen Sammlung. Dieser Offset ist eine Obergrenzenmarkierung: Es wird angenommen, dass Sie alle aktuellen Ereignisse verarbeitet haben, wenn Sie sie aufrufen. Wenn Sie diese Methode auf diese Weise verwenden, beachten Sie, dass Sie sie aufrufen müssen, nachdem anderer Ereignisverarbeitungscode zurückgegeben wurde. Mit der zweiten Überladung können Sie eine [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata)-Instanz für Prüfpunkte angeben. Mit dieser Methode können Sie einen anderen Typ von Grenzwert für Prüfpunkte verwenden. Mit diesem Grenzwert können Sie eine Untergrenzenmarkierung implementieren, d.h. das niedrigste sequenzierte Ereignis, das Sie mit Sicherheit verarbeitet haben. Diese Überladung soll Flexibilität bei der Offsetverwaltung ermöglichen.

Wenn der Prüfpunkt ausgeführt wird, wird eine JSON-Datei mit partitionsspezifischen Informationen (insbesondere des Offsets) in das Speicherkonto geschrieben, das im Konstruktor für [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) angegeben wird. Diese Datei wird laufend aktualisiert. Es ist wichtig, das Setzen von Prüfpunkten im Kontext zu berücksichtigen: Das Setzen von Prüfpunkten für jede Nachricht empfiehlt sich nicht. Das zum Setzen von Prüfpunkten verwendete Speicherkonto wird diese Last wahrscheinlich nicht verarbeiten. Wichtiger ist jedoch, dass das Setzen von Prüfpunkten für jedes einzelne Ereignis auf ein Muster des Messaging mit Warteschlange hindeutet, für das eine Service Bus-Warteschlange eine bessere Option darstellt als ein Event Hub. Das Konzept hinter Event Hubs ist, dass mindestens eine Übermittlung in großem Umfang erfolgt. Indem Sie Ihre Downstreamsysteme idempotent machen, ist es einfach, nach Fehlern oder Neustarts, die dazu führen, dass dieselben Ereignisse mehrmals empfangen werden, eine Wiederherstellung durchzuführen.

## <a name="thread-safety-and-processor-instances"></a>Threadsicherheit und Prozessorinstanzen

[EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) ist standardmäßig threadsicher und verhält sich in Bezug auf die Instanz von [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) synchron. Beim Eintreffen von Ereignissen für eine Partition wird [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) für die **IEventProcessor**-Instanz dieser Partition aufgerufen und blockiert weitere Aufrufe von **ProcessEventsAsync** für die Partition. Nachfolgende Nachrichten und Aufrufe von **ProcessEventsAsync** sammeln sich im Hintergrund in der Warteschlange an, da das Nachrichtensystem weiterhin im Hintergrund in anderen Threads ausgeführt wird. Mit dieser Threadsicherheit entfällt die Notwendigkeit für threadsichere Sammlungen. Dadurch wird die Leistung erheblich gesteigert.

## <a name="shut-down-gracefully"></a>Ordnungsgemäßes Herunterfahren

Schließlich ermöglicht [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) ein sauberes Herunterfahren aller Partitionsleser und sollte beim Herunterfahren einer Instanz von [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) immer aufgerufen werden. Andernfalls kann dies zu Verzögerungen beim Starten anderer Instanzen von **EventProcessorHost** aufgrund von Leaseablauf und Epochenkonflikten führen. Die Verwaltung von Epochen wird im Abschnitt [Epochen](#epoch) dieses Artikels näher erläutert. 

## <a name="lease-management"></a>Leaseverwaltung
Die Registrierung einer Ereignisprozessorklasse mit einer Instanz von EventProcessorHost startet die Ereignisverarbeitung. Die Hostinstanz ruft Leases für einige Partitionen des Event Hubs (möglicherweise auch für andere Hostinstanzen) in einer Weise ab, die zu einer gleichmäßigen Verteilung der Partitionen über alle Hostinstanzen führt. Für jede geleaste Partition erstellt die Hostinstanz eine Instanz der angegebenen Ereignisprozessorklasse, empfängt dann Ereignisse von dieser Partition und übergibt sie an die Ereignisprozessorinstanz. Wenn mehr Instanzen hinzugefügt und mehr Leases in Anspruch genommen werden, gleicht EventProcessorHost schließlich die Last zwischen allen Consumern aus.

Wie zuvor erläutert, wird das Konzept der automatischen Skalierung von [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) durch die Nachverfolgungstabelle sehr vereinfacht. Wenn eine Instanz von **EventProcessorHost** gestartet wird, übernimmt sie so viele Leases wie möglich und beginnt mit dem Lesen von Ereignissen. Wenn die Leases kurz vor dem Ablauf stehen, versucht **EventProcessorHost**, sie durch eine Reservierung zu verlängern. Wenn die Leasedauer verlängert werden kann, setzt der Prozessor das Lesen fort. Wenn dies jedoch nicht der Fall ist, wird der Leser geschlossen und [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) aufgerufen. **CloseAsync** ist ein guter Zeitpunkt zum Ausführen letzter Bereinigungen für die entsprechende Partition.

**EventProcessorHost** enthält eine [PartitionManagerOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions)-Eigenschaft. Diese Eigenschaft ermöglicht die Steuerung der Leaseverwaltung. Legen Sie diese Optionen vor der Registrierung der [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor)-Implementierung fest.

## <a name="control-event-processor-host-options"></a>Optionen zum Steuern des Ereignisprozessorhosts

Zusätzlich verwendet eine Überladung von [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) ein [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_)-Objekt als Parameter. Verwenden Sie diesen Parameter, um das Verhalten von [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) zu steuern. [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) definiert vier Eigenschaften und ein Ereignis:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): Die maximale Größe der Sammlung, die bei einem Aufruf von [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) empfangen werden soll. Mit dieser Größe wird nicht die minimale, sondern nur die maximale Größe definiert. Wenn weniger zu empfangende Nachrichten vorliegen, wird **ProcessEventsAsync** mit den jeweiligen verfügbaren Nachrichten ausgeführt.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): Ein Wert, der vom zugrunde liegenden AMQP-Kanal verwendet wird, um zu ermitteln, wie viele Nachrichten der Client maximal empfangen soll. Dieser Wert muss größer oder gleich [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize) sein.
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): Wenn dieser Parameter auf **TRUE** festgelegt ist, wird [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) aufgerufen, wenn für den zugrunde liegenden Aufruf zum Empfangen von Ereignissen für eine Partition ein Timeout auftritt. Diese Methode eignet sich für zeitbasierte Aktionen während inaktiver Zeiträume der Partition.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): Ermöglicht das Festlegen eines Funktionszeigers oder Lambdaausdrucks, der aufgerufen wird, um den anfänglichen Offset anzugeben, wenn ein Leser mit dem Lesen einer Partition beginnt. Ohne Angabe dieses Offsets beginnt der Leser bei dem ältesten Ereignis, es sei denn, eine JSON-Datei mit einem Offset wurde bereits in dem Speicherkonto gespeichert, das für den Konstruktor [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) angegeben ist. Diese Methode ist nützlich, wenn Sie das Verhalten für den Start des Lesers ändern möchten. Wenn diese Methode aufgerufen wird, enthält der Objektparameter die Partitions-ID, für die der Leser gestartet wird.
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): Ermöglicht, dass Sie Benachrichtigungen zu allen zugrunde liegenden Ausnahmen empfangen, die in [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) auftreten. Wenn Vorgänge nicht wie erwartet ausgeführt werden, ist dieses Ereignis ein guter Ausgangspunkt für die Überprüfung.

## <a name="epoch"></a>Epochen

So funktioniert der Empfang von Epochen:

### <a name="with-epoch"></a>Mit Epochen
Bei „epoch“ handelt es sich um einen eindeutigen Bezeichner (epoch-Wert), den der Dienst verwendet, um den Besitz von Partitionen oder Leases zu erzwingen. Mithilfe der [CreateEpochReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver?view=azure-dotnet)-Methode können Sie einen epochenbasierten Empfänger erstellen. Der Empfänger wird für eine bestimmte Event Hub-Partition aus der angegebenen Consumergruppe erstellt.

Mithilfe des Epochenfeatures können Benutzer sicherstellen, dass immer nur ein Empfänger in einer Consumergruppe vorhanden ist. Hierfür gelten folgende Regeln:

- Wenn kein Empfänger in einer Consumergruppe vorhanden ist, kann der Benutzer mit einem beliebigen epoch-Wert einen Empfänger erstellen.
- Wenn ein Empfänger mit einem epoch-Wert von e1 vorhanden ist, ein neuer Empfänger mit einem epoch-Wert von e2 erstellt wird und „e1 <= e2“ gilt, wird der Empfänger mit dem Wert e1 automatisch getrennt und der Empfänger mit dem Wert e2 erfolgreich erstellt.
- Wenn ein Empfänger mit einem epoch-Wert von e1 vorhanden ist, ein neuer Empfänger mit einem epoch-Wert von e2 erstellt wird und „e1 > e2“ gilt, schlägt die Erstellung des Empfängers mit dem Wert e2 mit folgender Fehlermeldung fehl: A receiver with epoch e1 already exists. (Ein Empfänger mit dem epoch-Wert e1 ist bereits vorhanden.)

### <a name="no-epoch"></a>Ohne Epochen
Mithilfe der [CreateReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver?view=azure-dotnet)-Methode können Sie einen nicht auf Epochen basierenden Empfänger erstellen. 

In einigen Szenarios bei der Streamverarbeitung müssen Benutzer mehrere Empfänger in einer Consumergruppe erstellen. Für solche Szenarios besteht die Möglichkeit, einen Empfänger ohne epoch-Wert zu erstellen. In diesem Fall sind bis zu fünf gleichzeitige Empfänger in der Consumergruppe möglich.

### <a name="mixed-mode"></a>Gemischter Modus
Es wird nicht empfohlen, einen Empfänger mit epoch-Wert zu erstellen und dann in derselben Consumergruppe in den nicht auf Epochen basierenden Modus zu wechseln (oder umgekehrt). Wenn dieser Fall jedoch eintritt, wendet der Dienst folgende Regeln an:

- Wenn bereits ein Empfänger mit einem epoch-Wert von e1 erstellt wurde, der aktiv Ereignisse empfängt, und ein neuer Empfänger ohne epoch-Wert erstellt wird, schlägt die Erstellung des neuen Empfängers fehl. Empfänger mit epoch-Wert haben im System immer Vorrang.
- Wenn bereits ein Empfänger mit einem epoch-Wert von e1 erstellt wurde, der getrennt wurde, und ein neuer Empfänger ohne epoch-Wert in einer neuen MessagingFactory-Instanz erstellt wird, ist die Erstellung des neuen Empfängers erfolgreich. Hier besteht allerdings der Nachteil, dass das System die Trennung des Empfängers erst nach etwa 10 Minuten erkennt.
- Wenn mindestens ein Empfänger ohne epoch-Wert erstellt wird und ein neuer Empfänger mit einem epoch-Wert von e1 erstellt wird, werden alle älteren Empfänger getrennt.


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit dem Ereignisprozessorhost vertraut gemacht haben, finden Sie in den folgenden Artikeln weitere Informationen zu Event Hubs:

* Erste Schritte mit einem [Event Hubs-Tutorial](event-hubs-dotnet-standard-getstarted-send.md)
* [Programmierleitfaden für Event Hubs](event-hubs-programming-guide.md)
* [Verfügbarkeit und Konsistenz in Event Hubs](event-hubs-availability-and-consistency.md)
* [Event Hubs – häufig gestellte Fragen](event-hubs-faq.md)
* [Event Hubs-Beispiele auf GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)

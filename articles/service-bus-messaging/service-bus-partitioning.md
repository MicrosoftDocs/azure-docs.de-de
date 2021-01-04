---
title: Erstellen von Azure Service Bus-Warteschlangen und -Themen | Microsoft-Dokumentation
description: Es wird beschrieben, wie Service Bus-Warteschlangen und -Themen mit mehreren Nachrichtenbrokern partitioniert werden.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 8fd845ba24fd96ad6de566a7f55b25bd7129074d
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930431"
---
# <a name="partitioned-queues-and-topics"></a>Partitionierte Warteschlangen und Themen

Für Azure Service Bus werden mehrere Nachrichtenbroker verwendet, um Nachrichten zu verarbeiten, sowie mehrere Nachrichtenspeicher, um Nachrichten zu speichern. Eine herkömmliche Warteschlange oder ein Thema werden von einem einzelnen Nachrichtenbroker verarbeitet und in einem Nachrichtenspeicher gespeichert. Service Bus-*Partitionen* ermöglichen das Partitionieren von Warteschlangen und Themen oder *Nachrichtenentitäten* über mehrere Nachrichtenbroker und -speicher. Eine Partitionierung bedeutet, dass der Gesamtdurchsatz einer partitionierten Entität nicht mehr durch die Leistung eines einzelnen Nachrichtenbrokers oder Nachrichtenspeichers beschränkt wird. Außerdem führt ein vorübergehender Ausfall eines Nachrichtenspeichers nicht dazu, dass eine partitionierte Warteschlange oder ein Thema nicht verfügbar ist. Partitionierte Warteschlangen und Themen können alle erweiterten Service Bus-Features enthalten, z. B. die Unterstützung von Transaktionen und Sitzungen.

> [!NOTE]
> Die Partitionierung ist bei der Erstellung der Entität für alle Warteschlangen und Themen in den SKUs „Basic“ oder „Standard“ verfügbar. Für die Premium-Messaging-SKU ist sie verfügbar, aber alle bereits vorhandenen partitionierten Entitäten in Premium-Namespaces funktionieren weiterhin wie erwartet.
 
Es ist nicht möglich, die Partitionierungsoption für eine vorhandene Warteschlange oder ein Thema zu ändern. Sie können die Option nur bei der Erstellung der Entität festlegen.

## <a name="how-it-works"></a>Funktionsweise

Jede partitionierte Warteschlange bzw. jedes Thema besteht aus mehreren Partitionen. Jede Partition wird in einem anderen Nachrichtenspeicher gespeichert und von einem anderen Nachrichtenbroker verarbeitet. Wenn eine Nachricht an eine partitionierte Warteschlange bzw. ein partitioniertes Thema gesendet wird, weist Service Bus die Nachricht einem der Partitionen zu. Service Bus nimmt die Auswahl willkürlich oder mithilfe eines Partitionsschlüssels vor, den der Absender angeben kann.

Wenn ein Client eine Nachricht von einer partitionierten Warteschlange oder von einem Abonnement eines partitionierten Themas empfangen möchte, fragt Service Bus alle Partitionen auf Nachrichten ab. Anschließend wird die erste Nachricht zurückgegeben, die von einem der Nachrichtenspeicher an den Empfänger gesendet wird. Service Bus speichert die anderen Nachrichten zwischen und gibt sie zurück, wenn zusätzliche Empfangsanforderungen eingehen. Ein empfangender Client ist sich der Partitionierung nicht bewusst. Das Verhalten einer partitionierten Warteschlange oder eines Themas (z.B. „read“, „complete“, „defer“, „deadletter“, „prefetching“) dem Client gegenüber ist mit dem Verhalten einer normalen Entität identisch.

Der Peekvorgang auf einer nicht partitionierten Entität gibt immer die älteste Nachricht zurück. Dies ist jedoch auf einer partitionierten Entität nicht der Fall. Stattdessen wird hier die älteste Nachricht in einer der Partitionen zurückgegeben, deren Nachrichtenbroker zuerst reagiert hat. Es gibt keine Garantie, dass es sich bei der zurückgegebenen Nachricht um die älteste auf allen Partitionen handelt. 

Es fallen keine zusätzlichen Kosten an, wenn eine Nachricht an eine partitionierte Warteschlange oder ein Thema gesendet oder von dort empfangen wird.
>[!NOTE]
> Der Peek-Vorgang gibt die älteste Nachricht aus der Partition basierend auf ihrer SequenceNumber zurück. Bei partitionierten Entitäten wird die Sequenznummer relativ zur Partition ausgestellt. Weitere Informationen finden Sie unter [Nachrichtensequenzierung und Zeitstempel](../service-bus-messaging/message-sequencing.md).

## <a name="enable-partitioning"></a>Aktivieren der Partitionierung

Setzen Sie zum Verwenden von partitionierten Warteschlangen und Themen mit Azure Service Bus die Azure SDK-Version 2.2 oder höher ein, oder geben Sie `api-version=2013-10` oder höher in Ihren HTTP-Anforderungen an.

### <a name="standard"></a>Standard

Im Standard-Messaging-Tarif können Sie Service Bus-Warteschlangen und -Themen in Größen von 1, 2, 3, 4 oder 5 GB erstellen (die Standardgröße ist 1 GB). Bei aktivierter Partitionierung erstellt Service Bus 16 Kopien (16 Partitionen) der Entität mit der jeweils angegebenen Größe. Wenn Sie also eine Warteschlange mit einer Größe von 5 GB erstellen, beträgt die maximale Warteschlangengröße bei 16 Partitionen 5 \* 16 = 80 GB. Die maximale Größe der partitionierten Warteschlange oder des Themas wird im zugehörigen Eintrag im [Azure-Portal][Azure portal] auf dem Blatt **Übersicht** für diese Entität angezeigt.

### <a name="premium"></a>Premium

In einem Namespace des Premium-Tarifs werden Partitionierungsentitäten nicht unterstützt. Sie können jedoch weiterhin Service Bus-Warteschlangen und -Themen in Größen von 1, 2, 3, 4, 5, 10, 20, 40 oder 80 GB erstellen (die Standardgröße ist 1 GB). Die Größe der Warteschlange oder des Themas wird im zugehörigen Eintrag im [Azure-Portal][Azure portal] auf dem Blatt **Übersicht** für diese Entität angezeigt.

### <a name="create-a-partitioned-entity"></a>Erstellen einer partitionierten Entität

Es gibt mehrere Möglichkeiten, eine partitionierte Warteschlange oder ein partitioniertes Thema zu erstellen. Wenn Sie die Warteschlange oder das Thema über Ihre Anwendung erstellen, können Sie die Partitionierung für die Warteschlange oder das Thema aktivieren, indem Sie die [QueueDescription.EnablePartitioning][QueueDescription.EnablePartitioning]- bzw. [TopicDescription.EnablePartitioning][TopicDescription.EnablePartitioning]-Eigenschaft auf **TRUE** festlegen. Diese Eigenschaften müssen zum Zeitpunkt der Erstellung der Warteschlange oder des Themas festgelegt werden und sind nur in der älteren [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)-Bibliothek verfügbar. Wie bereits erwähnt, ist es nicht möglich, diese Eigenschaften für eine vorhandene Warteschlange oder ein Thema zu ändern. Beispiel:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Alternativ können Sie eine partitionierte Warteschlange oder ein Thema im [Azure-Portal][Azure portal] erstellen. Wenn Sie eine Warteschlange oder ein Thema im Portal erstellen, wird die Option **Partitionierung aktivieren** im Dialogfeld **Erstellen** der Warteschlange oder des Themas standardmäßig aktiviert. Sie können diese Option nur in einer Entität des Standard-Tarifs deaktivieren. Im Premium-Tarif wird die Partitionierung nicht unterstützt, sodass eine Aktivierung des Kontrollkästchens keine Auswirkungen hätte. 

## <a name="use-of-partition-keys"></a>Verwenden von Partitionsschlüsseln

Wenn eine Nachricht in eine partitionierte Warteschlange oder ein Thema eingereiht wird, wird von Service Bus das Vorhandensein eines Partitionsschlüssels überprüft. Wenn ein Schlüssel gefunden wird, wird die Partition basierend auf diesem Schlüssel ausgewählt. Falls kein Partitionsschlüssel gefunden wird, wird die Partition basierend auf einem internen Algorithmus ausgewählt.

### <a name="using-a-partition-key"></a>Verwendung eines Partitionsschlüssels

Einige Szenarien, z. B. Sitzungen oder Transaktionen, erfordern es, dass Nachrichten in einer bestimmten Partition gespeichert werden. Für all diese Szenarien ist die Verwendung eines Partitionsschlüssels erforderlich. Alle Nachrichten, für die der gleiche Partitionsschlüssel verwendet wird, sind derselben Partition zugewiesen. Wenn die Partition vorübergehend nicht verfügbar ist, wird von Service Bus ein Fehler zurückgegeben.

Je nach Szenario werden verschiedene Nachrichteneigenschaften als Partitionsschlüssel verwendet:

**SessionId**: Wenn für eine Nachricht die [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid)-Eigenschaft festgelegt ist, verwendet Service Bus **SessionID** als Partitionsschlüssel. Auf diese Weise werden alle Nachrichten, die zu derselben Sitzung gehören, von demselben Nachrichtenbroker verarbeitet. Durch Sitzungen kann Service Bus sowohl die Nachrichtensortierung als auch die Einheitlichkeit von Sitzungszuständen garantieren.

**PartitionKey**: Wenn für eine Nachricht die [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)-Eigenschaft festgelegt ist, aber nicht die [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid)-Eigenschaft, verwendet Service Bus den [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)-Eigenschaftswert als Partitionsschlüssel. Wenn für die Nachricht sowohl die [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid)- als auch die [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)-Eigenschaft festgelegt ist, müssen beide Eigenschaften identisch sein. Falls die [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)-Eigenschaft auf einen anderen Wert als die [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid)-Eigenschaft festgelegt ist, gibt Service Bus eine Ausnahme für einen ungültigen Vorgang zurück. Die [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)-Eigenschaft sollte verwendet werden, wenn ein Absender nicht sitzungsorientierte Transaktionsnachrichten sendet. Mit dem Partitionsschlüssel wird sichergestellt, dass alle Nachrichten, die innerhalb einer Transaktion gesendet werden, von demselben Nachrichtenbroker verarbeitet werden.

**MessageId**: Wenn für die Warteschlange bzw. das Thema die [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection)-Eigenschaft auf **TRUE** festgelegt ist und die Eigenschaften [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) oder [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) nicht festgelegt sind, dient der [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid)-Eigenschaftswert als Partitionsschlüssel. (Die Microsoft .NET- und AMQP-Bibliotheken weisen automatisch eine Nachrichten-ID zu, wenn dies von der sendenden Anwendung nicht übernommen wird.) In diesem Fall werden alle Kopien einer Nachricht von demselben Nachrichtenbroker verarbeitet. Mit dieser ID kann Service Bus doppelte Nachrichten erkennen und verwerfen. Wenn die [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection)-Eigenschaft nicht auf **TRUE** festgelegt ist, sieht Service Bus die [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid)-Eigenschaft nicht als Partitionsschlüssel an.

### <a name="not-using-a-partition-key"></a>Keine Verwendung eines Partitionsschlüssels

Wenn kein Partitionsschlüssel vorhanden ist, verteilt Service Bus Nachrichten nach einem Roundrobinverfahren auf alle Partitionen der partitionierten Warteschlange bzw. des partitionierten Themas. Wenn die ausgewählte Partition nicht verfügbar ist, weist Service Bus die Nachricht einer anderen Partition zu. Auf diese Weise kann der Sendevorgang erfolgreich durchgeführt werden, obwohl ein Nachrichtenspeicher vorübergehend nicht verfügbar ist. Sie erzielen jedoch nicht die garantierte Reihenfolge, die ein Partitionsschlüssel liefert.

Eine ausführlichere Erläuterung des Kompromisses zwischen Verfügbarkeit (kein Partitionsschlüssel) und Konsistenz (Verwendung eines Partitionsschlüssels) finden Sie in [diesem Artikel](../event-hubs/event-hubs-availability-and-consistency.md). Diese Informationen gelten auch für partitionierte Service Bus-Entitäten.

Um Service Bus ausreichend Zeit zum Einreihen der Nachricht in eine andere Partition zu geben, muss der Wert [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) des Clients, der die Nachricht sendet, größer als 15 Sekunden sein. Es wird empfohlen, die [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout)-Eigenschaft auf den Standardwert von 60 Sekunden festzulegen.

Ein Partitionsschlüssel „heftet“ eine Nachricht an eine bestimmte Partition an. Wenn der Nachrichtenspeicher, der diese Partition enthält, nicht verfügbar ist, wird von Service Bus ein Fehler zurückgegeben. Wenn kein Partitionsschlüssel vorhanden ist, kann Service Bus eine andere Partition auswählen, damit der Vorgang erfolgreich ist. Aus diesem Grund wird empfohlen, nur dann einen Partitionsschlüssel anzugeben, wenn dies erforderlich ist.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Weiterführende Themen: Verwenden von Transaktionen mit partitionierten Entitäten

Nachrichten, die als Teil einer Transaktion gesendet werden, müssen einen Partitionsschlüssel angeben. Folgende Eigenschaften können als Schlüssel verwendet werden: [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) oder [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid). Für alle Nachrichten, die als Teil derselben Transaktion gesendet werden, muss derselbe Partitionsschlüssel angegeben werden. Wenn Sie versuchen, eine Nachricht innerhalb einer Transaktion ohne Partitionsschlüssel zu senden, wird von Service Bus eine Ausnahme für einen ungültigen Vorgang zurückgegeben. Wenn Sie versuchen, mehrere Nachrichten mit verschiedenen Partitionsschlüsseln innerhalb derselben Transaktion zu senden, gibt Service Bus eine Ausnahme für einen ungültigen Vorgang zurück. Beispiel:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.PartitionKey = "myPartitionKey";
    await messageSender.SendAsync(msg); 
    await ts.CompleteAsync();
}
committableTransaction.Commit();
```

Wenn Eigenschaften, die als Partitionsschlüssel dienen, festgelegt werden, heftet Service Bus die Nachricht an eine bestimmte Partition an. Dieses Verhalten tritt unabhängig davon auf, ob eine Transaktion verwendet wird. Es wird empfohlen, keinen Partitionsschlüssel anzugeben, wenn dies nicht erforderlich ist.

## <a name="using-sessions-with-partitioned-entities"></a>Verwenden von Sitzungen mit partitionierten Entitäten

Um eine Transaktionsnachricht an ein sitzungsorientiertes Thema bzw. eine sitzungsorientierte Warteschlange zu senden, muss für die Nachricht die [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid)-Eigenschaft festgelegt sein. Wenn die [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)-Eigenschaft ebenfalls angegeben wird, muss diese mit der [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid)-Eigenschaft identisch sein. Falls sie sich unterscheiden, gibt Service Bus eine Ausnahme für einen ungültigen Vorgang aus.

Im Gegensatz zu normalen (nicht partitionierten) Warteschlangen oder Themen ist es nicht möglich, eine einzelne Transaktion zu verwenden, um mehrere Nachrichten an verschiedene Sitzungen zu senden. Bei einem entsprechenden Versuch gibt Service Bus eine Ausnahme für einen ungültigen Vorgang aus. Beispiel:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.SessionId = "mySession";
    await messageSender.SendAsync(msg); 
    await ts.CompleteAsync();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Automatische Nachrichtenweiterleitung mit partitionierten Entitäten

Service Bus unterstützt die automatische Nachrichtenweiterleitung von und zwischen partitionierten Entitäten sowie an diese. Legen Sie zum Aktivieren der automatischen Nachrichtenweiterleitung die [QueueDescription.ForwardTo][QueueDescription.ForwardTo]-Eigenschaft für die Quellwarteschlange oder das Abonnement fest. Wenn für die Nachricht ein Partitionsschlüssel ([SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) oder [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid)) angegeben wird, wird er für die Zielentität verwendet.

## <a name="considerations-and-guidelines"></a>Aspekte und Richtlinien
* **Features für hohe Konsistenz**: Wenn eine Entität Features wie Sitzungen, Duplikaterkennung oder die explizite Steuerung eines Partitionierungsschlüssels verwendet, werden die Messagingvorgänge immer an bestimmte Partitionen geleitet. Wenn für eine Partition hoher Datenverkehr auftritt oder der zugrunde liegende Speicher fehlerhaft ist, schlagen diese Vorgänge fehl, und die Verfügbarkeit wird reduziert. Insgesamt ist die Konsistenz trotzdem deutlich höher als bei nicht partitionierten Entitäten: Nur für eine Teilmenge des Datenverkehrs treten Probleme auf, und nicht für den gesamten Datenverkehr. Weitere Informationen hierzu finden Sie in dieser [Erläuterung von Verfügbarkeit und Konsistenz](../event-hubs/event-hubs-availability-and-consistency.md).
* **Verwaltung**: Vorgänge wie das Erstellen, Aktualisieren und Löschen müssen für alle Partitionen der Entität durchgeführt werden. Wenn eine Partition fehlerhaft ist, kann dies zu Fehlern bei diesen Vorgängen führen. Für den Get-Vorgang müssen Informationen aus allen Partitionen aggregiert werden, z. B. die Nachrichtenanzahl. Wenn eine Partition einen Fehler aufweist, wird der Verfügbarkeitsstatus der Entität als „Eingeschränkt“ gemeldet.
* **Nachrichtenszenarios mit geringem Volumen**: Für diese Szenarios, vor allem bei Verwendung des HTTP-Protokolls, müssen Sie ggf. mehrere Empfangsvorgänge durchführen, um alle Nachrichten zu erhalten. Für Empfangsanforderungen führt das Front-End den Empfangsvorgang für alle Partitionen durch und speichert alle empfangenen Antworten zwischen. Eine nachfolgende Empfangsanforderung derselben Verbindung profitiert von dieser Zwischenspeicherung, und die Latenzen für den Empfang sind niedriger. Wenn Sie über mehrere Verbindungen verfügen oder HTTP verwenden, wird für jede Anforderung eine neue Verbindung hergestellt. Es besteht also keine Garantie, dass sie auf demselben Knoten eintrifft. Wenn alle vorhandenen Nachrichten gesperrt sind und auf einem anderen Front-End zwischengespeichert werden, gibt der Empfangsvorgang **NULL** zurück. Nachrichten laufen nach einer gewissen Zeit ab und können von Ihnen dann erneut empfangen werden. Hierfür wird HTTP-Keep-Alive empfohlen. Wenn Partitionierung in Szenarien mit geringem Volumen verwendet wird, können Empfangsvorgänge länger als erwartet dauern. Daher wird empfohlen, in diesen Szenarien keine Partitionierung zu verwenden. Löschen Sie alle vorhandenen partitionierten Entitäten, und erstellen Sie sie mit deaktivierter Partitionierung neu.
* **Durchsuchen/Einsehen von Nachrichten**: Nur in der älteren [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)-Bibliothek verfügbar. [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) gibt nicht immer die Anzahl von Nachrichten zurück, die in der [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount)-Eigenschaft angegeben sind. Für dieses Verhalten gibt es zwei häufige Ursachen. Ein Grund ist, dass die aggregierte Größe der Sammlung mit Nachrichten die maximale Größe von 256 KB übersteigt. Ein weiterer Grund ist: Wenn für die Warteschlange oder das Thema die [EnablePartitioning-Eigenschaft](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) auf **TRUE** festgelegt ist, verfügt eine Partition ggf. nicht über genügend Nachrichten, um die angeforderte Anzahl von Nachrichten abzuarbeiten. Wenn eine Anwendung eine bestimmte Anzahl von Nachrichten empfangen möchte, sollte sie im Allgemeinen mehrfach [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) aufrufen, bis die benötigte Anzahl von Nachrichten erreicht ist oder keine einzusehenden Nachrichten mehr vorhanden sind. Weitere Informationen und Codebeispiele finden Sie in der API-Dokumentation unter [QueueClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) oder [SubscriptionClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch).

## <a name="latest-added-features"></a>Neueste Funktionen

* Das Hinzufügen und Entfernen von Regeln wird jetzt für partitionierte Entitäten unterstützt. Diese Vorgänge werden nicht unter Transaktionen unterstützt, was einen Unterschied zu nicht partitionierten Entitäten darstellt. 
* Für AMQP wird jetzt das Senden und Empfangen von Nachrichten an bzw. von einer partitionierten Entität unterstützt.
* AMQP wird jetzt für die folgenden Vorgänge unterstützt: [Batchsendevorgang](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch), [Batchempfangsvorgang](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch), [Nach Sequenznummer empfangen](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive), [Einsehen](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek), [Sperre erneuern](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock), [Nachricht planen](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync), [Geplante Nachricht abbrechen](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync), [Regel hinzufügen](/dotnet/api/microsoft.azure.servicebus.ruledescription), [Regel entfernen](/dotnet/api/microsoft.azure.servicebus.ruledescription), [Sitzungssperre erneuern](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock), [Sitzungsstatus festlegen](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate), [Sitzungsstatus abrufen](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate) und [Sitzungen aufzählen](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions).

## <a name="partitioned-entities-limitations"></a>Einschränkungen partitionierter Entitäten

Derzeit gelten bei Service Bus die folgenden Einschränkungen für partitionierte Warteschlangen und Themen:

* Partitionierte Warteschlangen und Themen werden im Premium-Messaging-Tarif nicht unterstützt. Sitzungen werden im Premium-Tarif mithilfe von SessionId unterstützt. 
* Für partitionierte Warteschlangen und Themen wird nicht das Senden von Nachrichten unterstützt, die unterschiedlichen Sitzungen einer einzelnen Transaktion angehören.
* Service Bus erlaubt derzeit bis zu 100 partitionierte Warteschlangen oder Themen pro Namespace. Jede partitionierte Warteschlange bzw. jedes partitionierte Thema wird in das zulässige Kontingent von 10.000 Entitäten pro Namespace eingerechnet (gilt nicht für Premium-Tarif).

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich im [AMQP 1.0-Protokollhandbuch](service-bus-amqp-protocol-guide.md) über die grundlegenden Konzepte der Messagingspezifikation von AMQP 1.0.

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: ./service-bus-amqp-protocol-guide.md

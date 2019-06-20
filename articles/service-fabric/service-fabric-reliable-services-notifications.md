---
title: Reliable Services – Benachrichtigungen | Microsoft Docs
description: Dokumentation zu Reliable Services-Benachrichtigungen mit Service Fabric
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: chackdan
editor: masnider,vturecek
ms.assetid: cdc918dd-5e81-49c8-a03d-7ddcd12a9a76
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/29/2017
ms.author: mcoskun
ms.openlocfilehash: a3df5f28475b03f1799dc1e245c3a7e904b49cb3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60772923"
---
# <a name="reliable-services-notifications"></a>Reliable Services – Benachrichtigungen
Mit Benachrichtigungen können Clients Änderungen an einem Objekt verfolgen, an dem sie interessiert sind. Zwei Typen von Objekten unterstützen Benachrichtigungen: *Reliable State Manager* und *Reliable Dictionary*.

Häufige Gründe für die Verwendung von Benachrichtigungen:

* Erstellen von materialisierten Sichten, z.B. sekundäre Indizes oder aggregierte, gefilterte Sichten mit dem Replikatstatus. Ein Beispiel hierfür ist ein sortierter Index aller Schlüssel in Reliable Dictionary.
* Senden von Überwachungsdaten, z.B. die Anzahl von in der letzten Stunde hinzugefügten Benutzern.

Benachrichtigungen werden während der Anwendung von Vorgängen ausgelöst. Aus diesem Grund sollten Benachrichtigungen so schnell wie möglich behandelt werden und synchrone Ereignisse keine aufwändigen Vorgänge enthalten.

## <a name="reliable-state-manager-notifications"></a>Reliable State Manager-Benachrichtigungen
Der Reliable State Manager liefert Benachrichtigungen für folgende Ereignisse:

* Transaktion
  * Commit
* Zustands-Manager
  * Neu erstellen
  * Hinzufügen eines Reliable State
  * Entfernen eines Reliable State

Der Reliable State Manager verfolgt die aktuellen In-Flight-Transaktionen. Die einzige Änderung des Transaktionsstatus, bei der eine Benachrichtigung ausgelöst wird, ist der Commit einer Transaktion.

Der Reliable State Manager verwaltet eine Sammlung von Reliable States, z.B. Reliable Dictionary und Reliable Queue. Der Reliable State Manager löst Benachrichtigungen aus, wenn sich diese Auflistung wie folgt verändert: Ein Reliable State wird hinzugefügt oder entfernt, oder die gesamte Sammlung wird neu erstellt.
Die Sammlung des Reliable State Manager wird in drei Fällen neu erstellt:

* Wiederherstellung: Wenn ein Replikat gestartet wird, stellt es den vorherigen Zustand vom Datenträger wieder her. Nach Abschluss der Wiederherstellung wird **NotifyStateManagerChangedEventArgs** zum Auslösen eines Ereignisses verwendet, das den Satz mit den wiederhergestellten Reliable States enthält.
* Vollständige Kopie: Bevor ein Replikat dem Konfigurationssatz hinzugefügt werden kann, muss es erstellt werden. In einigen Fällen muss eine vollständige Kopie des Reliable State Manager-Zustands vom primären Replikat auf das inaktive sekundäre Replikat angewendet werden. Reliable State Manager auf dem sekundären Replikat nutzt **NotifyStateManagerChangedEventArgs** zum Auslösen eines Ereignisses, das den Satz mit den Reliable States enthält, die über das primäre Replikat beschafft wurden.
* Wiederherstellung: Bei einer Notfallwiederherstellung kann der Replikatzustand mit **RestoreAsync**aus einer Sicherung wiederhergestellt werden. In solchen Fällen nutzt Reliable State Manager auf dem primären Replikat **NotifyStateManagerChangedEventArgs** zum Auslösen eines Ereignisses, das den Satz mit den Reliable States enthält, die aus der Sicherung wiederhergestellt wurden.

Um sich für Transaktionsbenachrichtigungen und/oder Zustands-Manager-Benachrichtigungen zu registrieren, müssen Sie sich bei den Ereignissen **TransactionChanged** oder **StateManagerChanged** im Reliable State Manager registrieren. Üblicherweise wird die Registrierung bei diesen Ereignishandlern im Konstruktor Ihres zustandsbehafteten Diensts durchgeführt. Wenn Sie sich auf dem Konstruktor registrieren, entgeht Ihnen keine Benachrichtigung, die durch eine Änderung während der Lebensdauer von **IReliableStateManager** verursacht wird.

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

Der **TransactionChanged**-Ereignishandler verwendet **NotifyTransactionChangedEventArgs**, um Details zum Ereignis bereitzustellen. Darin ist die Action-Eigenschaft enthalten (z.B. **NotifyTransactionChangedAction.Commit**), mit der die Art der Änderung angegeben wird. Außerdem ist die Transaction-Eigenschaft enthalten, mit der ein Verweis auf die geänderte Transaktion bereitgestellt wird.

> [!NOTE]
> **TransactionChanged** -Ereignisse werden derzeit nur ausgelöst, wenn für die Transaktion ein Commit durchgeführt wird. Die Aktion ist dann mit **NotifyTransactionChangedAction.Commit**identisch. In Zukunft können Ereignisse aber auch für andere Arten von Änderungen des Transaktionsstatus ausgelöst werden. Wir empfehlen Ihnen, die Aktion zu überprüfen und das Ereignis nur zu verarbeiten, wenn es sich um das erwartete Ereignis handelt.
> 
> 

Es folgt ein Beispiel für den **TransactionChanged** -Ereignishandler.

```csharp
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

Der **StateManagerChanged**-Ereignishandler verwendet **NotifyStateManagerChangedEventArgs**, um Details zum Ereignis bereitzustellen.
**NotifyStateManagerChangedEventArgs** verfügt über zwei Unterklassen: **NotifyStateManagerRebuildEventArgs** und **NotifyStateManagerSingleEntityChangedEventArgs**.
Sie verwenden die Action-Eigenschaft in **NotifyStateManagerChangedEventArgs**, um **NotifyStateManagerChangedEventArgs** in die richtige Unterklasse umzuwandeln:

* **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
* **NotifyStateManagerChangedAction.Add** und **NotifyStateManagerChangedAction.Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

Es folgt ein Beispiel für einen **StateManagerChanged** -Benachrichtigungshandler.

```csharp
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStateManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>Reliable Dictionary-Benachrichtigungen
Reliable Dictionary liefert Benachrichtigungen für folgende Ereignisse:

* Neuerstellung: Wird aufgerufen, wenn **ReliableDictionary** seinen Zustand aus einem wiederhergestellten oder kopierten lokalen Status oder einer Sicherung wiederhergestellt hat.
* Löschen: Wird aufgerufen, wenn der Status von **ReliableDictionary** über die **ClearAsync**-Methode gelöscht wurde.
* Hinzufügen: Wird aufgerufen, wenn **ReliableDictionary** ein Element hinzugefügt wurde.
* Aktualisieren: Wird aufgerufen, wenn ein Element in **IReliableDictionary** aktualisiert wurde.
* Entfernen: Wird aufgerufen, wenn ein Element aus **IReliableDictionary** gelöscht wurde.

Um Reliable Dictionary-Benachrichtigungen zu erhalten, müssen Sie sich mit dem **DictionaryChanged**-Ereignishandler bei **IReliableDictionary** registrieren. Üblicherweise wird die Registrierung bei diesen Ereignishandlern in der **ReliableStateManager.StateManagerChanged** -Hinzufügungsbenachrichtigung durchgeführt.
Durch die Registrierung beim Hinzufügen von **IReliableDictionary** zu **IReliableStateManager** wird sichergestellt, dass Ihnen keine Benachrichtigungen entgehen.

```csharp
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
        }
    }
}
```

> [!NOTE]
> **ProcessStateManagerSingleEntityNotification** ist die Beispielmethode, die im vorherigen **OnStateManagerChangedHandler**-Beispiel aufgerufen wird.
> 
> 

Im vorherigen Code wird die **IReliableNotificationAsyncCallback**-Schnittstelle zusammen mit **DictionaryChanged** festgelegt. Da **NotifyDictionaryRebuildEventArgs** eine **IAsyncEnumerable**-Schnittstelle enthält, die asynchron aufgelistet werden muss, werden Neuerstellungsbenachrichtigungen nicht mit **OnDictionaryChangedHandler** ausgelöst, sondern mit **RebuildNotificationAsyncCallback**.

```csharp
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

> [!NOTE]
> Als Teil der Verarbeitung der Neuerstellungsbenachrichtigung wird im obigen Code zuerst der beibehaltene aggregierte Zustand gelöscht. Alle vorherigen Benachrichtigungen sind irrelevant, da die Reliable Collection mit einem neuen Zustand wiederhergestellt wird.
> 
> 

Der **DictionaryChanged**-Ereignishandler verwendet **NotifyDictionaryChangedEventArgs**, um Details zum Ereignis bereitzustellen.
**NotifyDictionaryChangedEventArgs** verfügt über fünf Unterklassen. Verwenden Sie die Action-Eigenschaft in **NotifyDictionaryChangedEventArgs**, um **NotifyDictionaryChangedEventArgs** in die richtige Unterklasse umzuwandeln:

* **NotifyDictionaryChangedAction.Rebuild**: **NotifyDictionaryRebuildEventArgs**
* **NotifyDictionaryChangedAction.Clear**: **NotifyDictionaryClearEventArgs**
* **NotifyDictionaryChangedAction.Add** und **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemAddedEventArgs**
* **NotifyDictionaryChangedAction.Update**: **NotifyDictionaryItemUpdatedEventArgs**
* **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemRemovedEventArgs**

```csharp
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## <a name="recommendations"></a>Empfehlungen
* *so schnell wie möglich* durch.
* Führen Sie *keine* aufwändigen Vorgänge (z.B. E/A-Vorgänge) im Rahmen von synchronen Ereignissen aus.
* *so schnell wie möglich* der Bearbeitung des Ereignisses den Aktionstyp. Es kann sein, dass in Zukunft neue Aktionstypen hinzugefügt werden.

Hier folgen einige Punkte, die es zu beachten gilt:

* Benachrichtigungen werden während der Ausführung eines Vorgangs ausgelöst. Als letzter Schritt eines Wiederherstellungsvorgangs wird beispielsweise eine Wiederherstellungsbenachrichtigung ausgelöst. Eine Wiederherstellung wird erst abgeschlossen, nachdem das Benachrichtigungsereignis verarbeitet wurde.
* Da Benachrichtigungen als Teil der Anwendung von Vorgängen ausgelöst werden, werden für Clients nur Benachrichtigungen für Vorgänge mit lokaler Commitausführung angezeigt. Vorgänge können in Zukunft eventuell rückgängig gemacht werden, da für sie nur ein lokaler Commit (d.h. Protokollierung) garantiert wird.
* Im Wiederholungspfad wird eine einzelne Benachrichtigung für jeden angewendeten Vorgang ausgelöst. Dies bedeutet Folgendes: Wenn Transaktion T1 „Create(X)2“, „Delete(X)“ und „Create(X)“ enthält, erhalten Sie eine Benachrichtigung für die Erstellung von X, eine für den Löschvorgang und eine für die erneute Erstellung (in dieser Reihenfolge).
* Für Transaktionen, die mehrere Vorgänge enthalten, werden die Vorgänge in der Reihenfolge angewendet, in der sie auf dem primären Replikat vom Benutzer empfangen wurden.
* Im Rahmen der Verarbeitung von falschen Fortschritten können einige Vorgänge rückgängig gemacht werden. Für solche Vorgänge zum Rückgängigmachen, die den Zustand des Replikats auf einen stabilen Punkt zurücksetzen, werden Benachrichtigungen ausgelöst. Ein wichtiger Unterschied bei Benachrichtigungen zu Vorgängen zum Rückgängigmachen ist, dass Ereignisse mit doppelten Schlüsseln aggregiert werden. Wird beispielsweise die Transaktion T1 rückgängig gemacht, wird eine einzelne Löschbenachrichtigung „Delete(X)“ angezeigt.

## <a name="next-steps"></a>Nächste Schritte
* [Zuverlässige Auflistungen](service-fabric-work-with-reliable-collections.md)
* [Reliable Services – Schnellstart](service-fabric-reliable-services-quick-start.md)
* [Sichern und Wiederherstellen von Reliable Services (Notfallwiederherstellung)](service-fabric-reliable-services-backup-restore.md)
* [Entwicklerreferenz für zuverlässige Auflistungen](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)


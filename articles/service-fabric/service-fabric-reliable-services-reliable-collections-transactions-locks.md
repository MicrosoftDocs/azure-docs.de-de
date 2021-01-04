---
title: Transaktionen und Sperrmodi in Reliable Collections
description: Transaktionen und Sperren in Azure Service Fabric Reliable State Manager und Reliable Collections.
ms.topic: conceptual
ms.date: 5/1/2017
ms.openlocfilehash: 57ca46047641b79d5e4c50ede4a27e16dcec5d89
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576722"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transaktionen und Sperrmodi in Azure Service Fabric Reliable Collections

## <a name="transaction"></a>Transaktion

Eine Transaktion ist eine Folge von Operationen, die als einzelne logische Arbeitseinheit ausgeführt wird. Sie zeigt die allgemeinen [ACID](https://en.wikipedia.org/wiki/ACID)-Eigenschaften (*Atomicity (Unteilbarkeit)* , *Consistency (Konsistenz)* , *Isolation*, *Durability (Dauerhaftigkeit)* ) von Datenbanktransaktionen an:

* **Unteilbarkeit**: Eine Transaktion muss eine unteilbare Arbeitseinheit sein. Dies bedeutet, dass entweder alle oder keine Datenänderungen ausgeführt werden.
* **Konsistenz:** Am Ende einer Transaktion müssen sich alle Daten in einem konsistenten Status befinden. Alle internen Datenstrukturen müssen am Ende der Transaktion korrekt sein.
* **Isolation:** : Änderungen, die von gleichzeitigen Transaktionen ausgeführt werden, müssen von allen Änderungen, die von anderen gleichzeitigen Transaktionen ausgeführt werden, isoliert sein. Die Isolationsstufe, die innerhalb einer [ITransaction](/dotnet/api/microsoft.servicefabric.data.itransaction?view=azure-dotnet) für einen Vorgang verwendet wird, ist durch den [IReliableState](/dotnet/api/microsoft.servicefabric.data.ireliablestate?view=azure-dotnet) festgelegt, der den betreffenden Vorgang ausführt.
* **Dauerhaftigkeit**: Nach dem Abschluss einer Transaktion sind deren Auswirkungen dauerhaft im System vorhanden. Die Änderungen bleiben auch bei einem Systemfehler persistent.

### <a name="isolation-levels"></a>Isolationsgrade

Isolationsstufen definiert den Grad, zu dem eine Transaktion von Änderungen isoliert werden muss, die von anderen Transaktionen ausgeführt werden.
Es gibt zwei Isolationsstufen, die von zuverlässigen Auflistungen unterstützt werden:

* **Wiederholbarer Lesevorgang**: Gibt an, dass Anweisungen keine Daten lesen können, die geändert wurden, für die aber von anderen Transaktionen noch kein Commit ausgeführt wurde. Darüber hinaus können von der aktuellen Transaktion gelesene Daten erst nach Abschluss dieser Transaktion von anderen Transaktionen geändert werden.
* **Momentaufnahme**: Gibt an, dass von Anweisungen in einer Transaktion gelesene Daten der im Hinblick auf Transaktionen konsistenten Version der Daten entsprechen, die zu Beginn der Transaktion vorhanden waren.
  Die Transaktion kann nur Datenänderungen erkennen, die vor dem Start der Transaktion festgeschrieben wurden.
  Datenänderungen, die nach Beginn der aktuellen Transaktion von anderen Transaktionen vorgenommen wurden, sind für in der aktuellen Transaktion ausgeführte Anweisungen nicht sichtbar.
  Es erscheint daher, als ob die Anweisungen in einer Transaktion eine Momentaufnahme der festgeschriebenen Daten erhalten, die zu Beginn der Transaktion vorhanden waren.
  Momentaufnahmen sind über zuverlässige Sammlungen hinweg konsistent.

Reliable Collections wählen die Isolationsstufe für einen bestimmten Lesevorgang automatisch je nach Vorgang und nach Rolle des Replikats zum Zeitpunkt der Erstellung der Transaktion.
Im Folgenden finden Sie eine Tabelle, die Standardwerte für Isolationsstufen für Reliable Dictionary- und Reliable Queue-Vorgänge veranschaulicht.

| Vorgang\Rolle | Primär | Secondary |
| --- |:--- |:--- |
| Lesevorgang für eine einzelne Entität |Wiederholbarer Lesevorgang |Momentaufnahme |
| Aufzählung, Anzahl |Momentaufnahme |Momentaufnahme |

> [!NOTE]
> Typische Beispiele für Vorgänge mit einer einzigen Entität sind `IReliableDictionary.TryGetValueAsync` und `IReliableQueue.TryPeekAsync`.
> 

Das zuverlässige Wörterbuch und die zuverlässige Warteschlange unterstützen beide *Read Your Writes*.
Mit anderen Worten sind jegliche Schreibvorgänge innerhalb einer Transaktion für den nachfolgenden Lesevorgang sichtbar, wenn dieser derselben Transaktion angehört.

## <a name="locks"></a>Locks

In Reliable Collections werden Sperren von allen Transaktionen streng in zwei Phasen verwaltet: Die von einer Transaktion angeforderten Sperren werden erst aufgehoben, wenn die Transaktion durch Abbruch oder Commit beendet wird.

Reliable Dictionary verwendet Sperren auf Zeilenebene für alle Vorgänge mit nur einer Entität.
Reliable Queue wägt Parallelität gegen die strikte transaktionsbezogene FIFO-Eigenschaft ab.
Reliable Queue verwendet Sperren auf Vorgangsebene und lässt gleichzeitig eine Transaktion mit `TryPeekAsync` und/oder `TryDequeueAsync` und eine Transaktion mit `EnqueueAsync` zu.
Beachten Sie Folgendes: Wenn ein `TryPeekAsync`- oder ein `TryDequeueAsync`-Vorgang feststellt, dass die Reliable Queue leer ist, sperrt er auch `EnqueueAsync`, um das FIFO-Prinzip beizubehalten.

Schreibvorgänge verwenden immer exklusive Sperren.
Bei Lesevorgängen richtet sich die Sperrung nach verschiedenen Faktoren:

- Alle Lesevorgänge, die mithilfe der Momentaufnahmeisolierung ausgeführt wurden, sind frei von Sperren.
- Bei allen wiederholbaren Lesevorgängen werden standardmäßig freigegebene Sperren angewendet.
- Bei Lesevorgängen, die wiederholbare Lesevorgänge unterstützen, können Benutzer anstelle der freigegebenen Sperre eine Aktualisierungssperre anfordern.
Eine Aktualisierungssperre ist eine asymmetrische Sperre, mit der eine häufig auftretende Form von Deadlock verhindert wird. Der Deadlock tritt auf, wenn mehrere Transaktionen Ressourcen für potenzielle Updates zu einem späteren Zeitpunkt sperren.

Die Kompatibilitätsmatrix für Sperren finden Sie in der folgenden Tabelle:

| Anforderung \ Gewährt | Keine | Shared | Aktualisieren | Exklusiv |
| --- |:--- |:--- |:--- |:--- |
| Shared |Kein Konflikt |Kein Konflikt |Konflikt: |Konflikt: |
| Aktualisieren |Kein Konflikt |Kein Konflikt |Konflikt: |Konflikt: |
| Exklusiv |Kein Konflikt |Konflikt: |Konflikt: |Konflikt: |

Zum Erkennen von Deadlocks werden in den Reliable Collections-APIs Timeoutargumente verwendet.
Angenommen, zwei Transaktionen (T1 und T2) versuchen, K1 zu lesen und zu aktualisieren.
Beide können ein Deadlock durchführen, da im Endeffekt beide die freigegebene Sperre haben.
In diesem Fall tritt bei einem oder beiden Vorgängen ein Timeout auf. In diesem Szenario könnte eine Updatesperre einen solchen Deadlock verhindern.

## <a name="next-steps"></a>Nächste Schritte

* [Arbeiten mit Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Reliable Services – Benachrichtigungen](service-fabric-reliable-services-notifications.md)
* [Sichern und Wiederherstellen von Reliable Services (Notfallwiederherstellung)](service-fabric-reliable-services-backup-restore.md)
* [Konfigurieren des Reliable State Managers](service-fabric-reliable-services-configuration.md)
* [Entwicklerreferenz für zuverlässige Auflistungen](/dotnet/api/microsoft.servicefabric.data.collections?view=azure-dotnet#microsoft_servicefabric_data_collections)

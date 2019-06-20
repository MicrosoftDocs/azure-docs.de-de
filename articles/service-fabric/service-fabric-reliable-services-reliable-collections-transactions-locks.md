---
title: Transaktionen und Sperrmodi in Azure Service Fabric Reliable Collections | Microsoft-Dokumentation
description: Transaktionen und Sperren in Azure Service Fabric Reliable State Manager und Reliable Collections.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: masnider,rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: aljo
ms.openlocfilehash: 9785a09a3ac3e119507b4ac28075d887c7edc619
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60774062"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transaktionen und Sperrmodi in Azure Service Fabric Reliable Collections

## <a name="transaction"></a>Transaktion
Eine Transaktion ist eine Folge von Vorgängen, die als einzelne logische Arbeitseinheit ausgeführt werden.
Eine Transaktion muss die folgenden ACID-Eigenschaften aufweisen. (Siehe https://technet.microsoft.com/library/ms190612).)
* **Unteilbarkeit**: Eine Transaktion muss eine unteilbare Arbeitseinheit sein. Dies bedeutet, dass entweder alle oder keine Datenänderungen ausgeführt werden.
* **Konsistenz**: Nach dem Abschluss der Transaktion müssen alle Daten in einem konsistenten Zustand belassen werden. Alle internen Datenstrukturen müssen am Ende der Transaktion korrekt sein.
* **Isolation:** : Änderungen, die von parallelen Transaktionen durchgeführt werden, müssen von den Änderungen aller anderen parallelen Transaktionen isoliert werden. Die Isolationsstufe, die innerhalb einer ITransaction für einen Vorgang verwendet wird, ist durch den IReliableState festgelegt, der den betreffenden Vorgang ausführt.
* **Dauerhaftigkeit**: Nach dem Abschluss einer Transaktion sind deren Auswirkungen dauerhaft im System vorhanden. Die Änderungen bleiben sogar auch bei Systemausfällen erhalten.

### <a name="isolation-levels"></a>Isolationsgrade
Isolationsstufen definiert den Grad, zu dem eine Transaktion von Änderungen isoliert werden muss, die von anderen Transaktionen ausgeführt werden.
Es gibt zwei Isolationsstufen, die von zuverlässigen Auflistungen unterstützt werden:

* **Wiederholbarer Lesevorgang**: Gibt an, dass Anweisungen keine Daten lesen können, die geändert wurden, für die aber von anderen Transaktionen noch kein Commit ausgeführt wurde. Darüber hinaus können von der aktuellen Transaktion gelesene Daten erst nach Abschluss dieser Transaktion von anderen Transaktionen geändert werden. Weitere Informationen finden Sie unter [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).
* **Momentaufnahme**: Gibt an, dass von Anweisungen in einer Transaktion gelesene Daten der im Hinblick auf Transaktionen konsistenten Version der Daten entsprechen, die zu Beginn der Transaktion vorhanden waren.
  Die Transaktion kann nur Datenänderungen erkennen, die vor dem Start der Transaktion festgeschrieben wurden.
  Nach dem Start der aktuellen Transaktion von anderen Transaktionen vorgenommene Datenänderungen sind für Anweisungen, die in der aktuellen Transaktion ausgeführt werden, nicht sichtbar.
  Es erscheint daher, als ob die Anweisungen in einer Transaktion eine Momentaufnahme der festgeschriebenen Daten erhalten, die zu Beginn der Transaktion vorhanden waren.
  Momentaufnahmen sind über zuverlässige Sammlungen hinweg konsistent.
  Weitere Informationen finden Sie unter [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).

Reliable Collections wählen die Isolationsstufe für einen bestimmten Lesevorgang automatisch je nach Vorgang und nach Rolle des Replikats zum Zeitpunkt der Erstellung der Transaktion.
Im Folgenden finden Sie eine Tabelle, die Standardwerte für Isolationsstufen für Reliable Dictionary- und Reliable Queue-Vorgänge veranschaulicht.

| Vorgang\Rolle | Primär | Sekundär |
| --- |:--- |:--- |
| Lesevorgang für eine einzelne Entität |Wiederholbarer Lesevorgang |Momentaufnahme |
| Aufzählung, Anzahl |Momentaufnahme |Momentaufnahme |

> [!NOTE]
> Typische Beispiele für Vorgänge mit einer einzigen Entität sind `IReliableDictionary.TryGetValueAsync` und `IReliableQueue.TryPeekAsync`.
> 

Das zuverlässige Wörterbuch und die zuverlässige Warteschlange unterstützen beide "Read Your Writes".
Mit anderen Worten sind jegliche Schreibvorgänge innerhalb einer Transaktion für den nachfolgenden Lesevorgang sichtbar, wenn dieser derselben Transaktion angehört.

## <a name="locks"></a>Sperren
In Reliable Collections werden Sperren von allen Transaktionen streng in zwei Phasen verwaltet: Die von einer Transaktion angeforderten Sperren werden erst aufgehoben, wenn die Transaktion durch Abbruch oder Commit beendet wird.

Reliable Dictionary verwendet Sperren auf Zeilenebene für alle Vorgänge mit nur einer Entität.
Reliable Queue wägt Parallelität gegen die strikte transaktionsbezogene FIFO-Eigenschaft ab.
Reliable Queue verwendet Sperren auf Vorgangsebene und lässt gleichzeitig eine Transaktion mit `TryPeekAsync` und/oder `TryDequeueAsync` und eine Transaktion mit `EnqueueAsync` zu.
Beachten Sie Folgendes: Wenn ein `TryPeekAsync`- oder ein `TryDequeueAsync`-Vorgang feststellt, dass die Reliable Queue leer ist, sperrt er auch `EnqueueAsync`, um das FIFO-Prinzip beizubehalten.

Schreibvorgänge verwenden immer exklusive Sperren.
Bei Lesevorgängen richtet sich die Sperrung nach verschiedenen Faktoren.
Jegliche Lesevorgänge, die mithilfe der Momentaufnahmeisolierung ausgeführt wurden, sind frei von Sperren.
Bei allen wiederholbaren Lesevorgängen werden standardmäßig freigegebene Sperren angewendet.
Bei Lesevorgängen, die wiederholbare Lesevorgänge unterstützen, können Benutzer anstelle der freigegebenen Sperre eine Aktualisierungssperre anfordern.
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
In diesem Fall tritt bei einem oder beiden Vorgängen ein Timeout auf.

Dieses Deadlockszenario ist ein hervorragendes Beispiel, wie Aktualisierungssperren Deadlocks verhindern können.

## <a name="next-steps"></a>Nächste Schritte
* [Arbeiten mit Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Reliable Services – Benachrichtigungen](service-fabric-reliable-services-notifications.md)
* [Sichern und Wiederherstellen von Reliable Services (Notfallwiederherstellung)](service-fabric-reliable-services-backup-restore.md)
* [Konfigurieren des Reliable State Managers](service-fabric-reliable-services-configuration.md)
* [Entwicklerreferenz für zuverlässige Auflistungen](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)


---
title: Richtlinien und Empfehlungen für Reliable Collections in Azure Service Fabric | Microsoft-Dokumentation
description: Richtlinien und Empfehlungen für die Verwendung von Reliable Collections in Service Fabric
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: masnider,rajak,zhol
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 12/10/2017
ms.author: aljo
ms.openlocfilehash: 810427c394c3912142e0a21cf1b5c29b81620afb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60774096"
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Richtlinien und Empfehlungen für Reliable Collections in Azure Service Fabric
Dieser Abschnitt enthält Richtlinien für die Verwendung von Reliable State Manager und Reliable Collections. Er soll Benutzern helfen, häufige Fehlerquellen zu vermeiden.

Die *Richtlinien*,*werden* als *einfache* Empfehlungen *präsentiert*.

* Ändern Sie kein benutzerdefiniertes Objekt, das von Lesevorgängen (z.B. `TryPeekAsync` oder `TryGetValueAsync`) zurückgegeben wurde. Zuverlässige Auflistungen geben ebenso wie gleichzeitige Auflistungen anstelle einer Kopie einen Verweis auf die Objekte zurück.
* Tiefenkopieren Sie zurückgegebene benutzerdefinierte Objekte, bevor Sie diese ändern. Da bei Strukturen und integrierten Typen eine Wertübergabe erfolgt, ist für sie nur dann eine Tiefenkopie erforderlich, wenn sie Verweistypfelder oder Eigenschaften enthalten, die Sie ändern möchten.
* Verwenden Sie `TimeSpan.MaxValue` nicht für Timeouts. Timeouts sollten verwendet werden, um Deadlocks zu erkennen.
* Verwenden Sie keine Transaktion, nachdem für sie ein Commit ausgeführt bzw. sie verworfen oder abgebrochen wurde.
* Verwenden Sie eine Enumeration nicht außerhalb des Transaktionsbereichs, in dem sie erstellt wurde.
* Erstellen Sie keine Transaktion innerhalb der `using` -Anweisung einer anderen Transaktion, da dies zu Deadlocks führen kann.
* Erstellen Sie keinen zuverlässigen Zustand mit `IReliableStateManager.GetOrAddAsync`, und verwenden Sie den zuverlässigen Zustand in derselben Transaktion. Dies führt zu einer „InvalidOperationException“.
* Stellen Sie sicher, dass Ihre `IComparable<TKey>` -Implementierung richtig ist. `IComparable<TKey>` ist erforderlich, damit das System Prüfpunkte und Zeilen zusammenfügen kann.
* Verwenden Sie Aktualisierungssperren beim Lesen eines Elements, das aktualisiert werden soll, um eine bestimmte Klasse von Deadlocks zu vermeiden.
* Erwägen Sie, die Anzahl der zuverlässigen Sammlungen pro Partition auf weniger als 1000 zu begrenzen. Bevorzugen Sie zuverlässige Sammlungen mit mehr Elementen vor zuverlässigen Sammlungen mit weniger Elementen.
* Sie sollten auch erwägen, Ihre Elemente (z.B. TKey + TValue für das Reliable Dictionary) unter 80 KB zu halten: je kleiner, desto besser. Damit verringern Sie die Nutzung großer Objektheaps sowie die Anforderungen an Datenträger und Netzwerk-E/A. Häufig wird damit auch die Replikation doppelter Daten reduziert, wenn nur ein kleiner Teil des Werts aktualisiert wird. Um dies im zuverlässigen Wörterbuch zu erreichen, werden häufig einzelne Zeilen in mehrere Zeilen aufgeteilt.
* Sie sollten zwecks Notfallwiederherstellung die Verwendung der Funktionen „Backup“ und „Wiederherstellung“ in Betracht ziehen.
* Verwenden Sie Vorgänge mit einer einzigen Entität und Vorgänge mit mehreren Entitäten (z.B. `GetCountAsync` und `CreateEnumerableAsync`) aufgrund der unterschiedlichen Isolationsstufen nicht in der gleichen Transaktion.
* Behandeln Sie „InvalidOperationException“. Benutzertransaktionen können aus verschiedenen Gründen vom System abgebrochen werden, z.B. wenn der Reliable State Manager seine primäre Rolle ändert, oder wenn eine Transaktion mit langer Laufzeit das Abschneiden des Transaktionsprotokolls einschränkt. In solchen Fällen erhalten die Benutzer möglicherweise eine InvalidOperationException, die angibt, dass ihre Transaktion bereits beendet wurde. Vorausgesetzt, das Beenden der Transaktion wurde nicht durch den Benutzer angefordert, ist der beste Weg, diese Ausnahme zu behandeln, die Transaktion zu beenden und zu überprüfen, ob das Abbruchtoken signalisiert wurde (oder ob sich die Rolle des Replikats geändert hat). Falls dies nicht der Fall ist, erstellen Sie eine neue Transaktion und versuchen Sie es erneut.  

Hier folgen einige Punkte, die es zu beachten gilt:

* Das Standardtimeout beträgt 4 Sekunden für alle Reliable Collections-APIs. Die meisten Benutzer sollten das Standardtimeout verwenden.
* Das Standardabbruchtoken ist `CancellationToken.None` in allen APIs für zuverlässige Auflistungen.
* Der Schlüsseltyp-Parameter (*TKey*) für Reliable Dictionary muss `GetHashCode()` und `Equals()` ordnungsgemäß implementieren. Schlüssel müssen unveränderlich sein.
* Zum Erreichen der Hochverfügbarkeit der zuverlässigen Auflistungen sollte jeder Dienst mindestens ein Ziel und eine Mindestgröße von 3 bei der Replikatgruppe haben.
* Lesevorgänge auf dem sekundären Replikat dürfen Versionen lesen, die nicht im Quorum committet wurden.
  Dies bedeutet, dass Datenversionen, die von einem einzelnen sekundären Replikat gelesen werden, falsch weiterverarbeitet werden können.
  Da Lesevorgänge von primären Replikaten immer stabil sind, können hier nie fehlerhafte Versionen auftreten.
* Die Vorgehensweise in Bezug auf die Sicherheit bzw. den Datenschutz der Daten, die von Ihrer Anwendung in einer zuverlässigen Sammlung aufbewahrt werden, ist Ihre Entscheidung und unterliegt den Schutzmechanismen Ihrer Speicherverwaltung. Beispielsweise kann die Verschlüsselung von Betriebssystem-Datenträgern genutzt werden, um Ihre ruhenden Daten zu schützen.  

### <a name="next-steps"></a>Nächste Schritte
* [Arbeiten mit Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transaktionen und Sperren](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Verwalten von Daten
  * [Sichern und Wiederherstellen](service-fabric-reliable-services-backup-restore.md)
  * [Notifications](service-fabric-reliable-services-notifications.md)
  * [Serialisierung und Upgrade](service-fabric-application-upgrade-data-serialization.md)
  * [Konfigurieren des Reliable State Managers](service-fabric-reliable-services-configuration.md)
* Andere
  * [Reliable Services – Schnellstart](service-fabric-reliable-services-quick-start.md)
  * [Entwicklerreferenz für zuverlässige Auflistungen](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

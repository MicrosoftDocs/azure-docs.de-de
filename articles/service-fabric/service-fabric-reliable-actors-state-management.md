---
title: Reliable Actors-Zustandsverwaltung
description: Hier wird beschrieben, wie der Reliable Actors-Zustand für Hochverfügbarkeit verwaltet, persistent gespeichert und repliziert wird.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: badfc490f26b71881e7970c2c0be3472abfec25a
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575600"
---
# <a name="reliable-actors-state-management"></a>Reliable Actors-Zustandsverwaltung
Reliable Actors sind Singlethread-Objekte, die sich zum Kapseln von Logik und Zustand eignen. Da Akteure unter Reliable Services ausgeführt werden, können Sie den Zustand zuverlässig beibehalten, indem sie die gleichen Persistenz- und Replikationsmechanismen verwenden. Daher verlieren Akteure ihren Zustand nicht nach Fehlern, bei der Reaktivierung nach einer Garbage Collection oder beim Verschieben zwischen Knoten in einem Cluster aufgrund von Ressourcenausgleich oder Upgrades.

## <a name="state-persistence-and-replication"></a>Zustandspersistenz und -replikation
Alle Reliable Actors werden als *zustandsbehaftet* betrachtet, da jeder Akteurinstanz eine eindeutige ID zugewiesen ist. Das bedeutet, dass wiederholte Aufrufe derselben Akteur-ID an dieselbe Akteurinstanz weitergeleitet werden. Bei einem zustandslosen System werden Clientaufrufe dagegen nicht unbedingt jedes Mal an denselben Server weitergeleitet. Aus diesem Grund sind Akteurdienste immer zustandsbehaftete Dienste.

Obwohl Akteure als zustandsbehaftet gelten, bedeutet das nicht, dass sie den Zustand zuverlässig speichern müssen. Akteure können die Ebene der Zustandspersistenz und -replikation basierend auf ihren Datenspeicheranforderungen auswählen:

* **Persistenter Zustand**: Der Zustand wird persistent auf einem Datenträger gespeichert, und es werden mindestens drei Replikate davon erstellt. Der persistente Zustand ist die dauerhafteste Option für die Zustandsspeicherung, mit der der Zustand bei einem vollständigen Clusterausfall beibehalten werden kann.
* **Flüchtiger Zustand**: Es werden mindestens drei Replikate des Zustands erstellt, und der Zustand wird nur im Arbeitsspeicher gespeichert. Mit dem flüchtigen Zustand wird Ausfallsicherheit bei Knotenausfällen, Akteurausfällen, Upgrades und Ressourcenausgleich gewährleistet. Der Zustand wird jedoch nicht persistent auf einem Datenträger gespeichert. Wenn alle Replikate gleichzeitig verloren gehen, geht auch der Zustand verloren.
* **Kein persistenter Zustand**: Der Zustand wird weder repliziert noch auf den Datenträger geschrieben; nur für Akteure zu verwenden, die den Zustand nicht zuverlässig beibehalten müssen.

Jede Persistenzebene stellt einfach einen anderen *Zustandsanbieter* und eine andere *Replikationskonfiguration* Ihres Diensts dar. Ob der Zustand auf einen Datenträger geschrieben wird, ist abhängig vom Zustandsanbieter – der Komponente in einer Reliable Services-Instanz, die den Zustand speichert. Die Replikation hängt davon ab, mit wie vielen Replikaten ein Dienst bereitgestellt wird. Wie bei Reliable Services können der Zustandsanbieter und die Replikatanzahl einfach manuell festgelegt werden. Das Akteurframework enthält ein Attribut, das bei Verwendung für einen Akteur automatisch einen Standardzustandsanbieter auswählt und Einstellungen für die Replikatanzahl erstellt, um eine der folgenden drei Persistenzeinstellungen zu erreichen. Das StatePersistence-Attribut wird nicht von abgeleiteten Klasse geerbt. Jeder Typ „Actor“ muss seinen „StatePersistence“-Grad angeben.

### <a name="persisted-state"></a>Persistenter Zustand
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl  extends FabricActor implements MyActor
{
}
```  
Für diese Einstellung wird ein Zustandsanbieter verwendet, der Daten auf dem Datenträger speichert und automatisch die Anzahl der Replikat auf drei festlegt.

### <a name="volatile-state"></a>Flüchtiger Zustand
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Volatile)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Diese Einstellung verwendet einen rein speicherinternen Zustandsanbieter und legt die Replikatanzahl auf drei fest.

### <a name="no-persisted-state"></a>Kein persistenter Zustand
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.None)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Diese Einstellung verwendet einen rein speicherinternen Zustandsanbieter und legt die Replikatanzahl auf eins fest.

### <a name="defaults-and-generated-settings"></a>Standardeinstellungen und generierte Einstellungen
Bei Verwendung des `StatePersistence`-Attributs wird zur Laufzeit automatisch ein Zustandsanbieter für Sie ausgewählt, wenn der Akteurdienst gestartet wird. Die Replikatanzahl wird jedoch zum Zeitpunkt der Kompilierung von den Visual Studio-Akteur-Buildtools festgelegt. Die Buildtools generieren in der Datei „ApplicationManifest.xml“ automatisch einen *Standarddienst* für den Actordienst. Für die **Mindestgröße der Replikatgruppe** und die **Zielgröße der Replikatgruppe** werden Parameter erstellt.

Sie können diese Parameter manuell ändern. Bei jeder Änderung des `StatePersistence`-Attributs werden die Parameter jedoch auf die Standardwerte der Replikatgruppengröße für das ausgewählte `StatePersistence`-Attribut festgelegt. Dabei werden vorherige Werte überschrieben. Das bedeutet, dass die in „ServiceManifest.xml“ festgelegten Werte *nur* zum Erstellungszeitpunkt überschrieben werden, wenn Sie den Attributwert `StatePersistence` ändern.

```xml
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="3" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## <a name="state-manager"></a>Zustands-Manager
Jede Akteurinstanz verfügt über einen eigenen Zustands-Manager: Dabei handelt es sich um eine wörterbuchähnliche Datenstruktur, die zuverlässig Schlüssel-Wert-Paare speichert. Der Zustands-Manager ist ein Wrapper für einen Zustandsanbieter. Sie können ihn unabhängig von der verwendeten Persistenzeinstellung zum Speichern von Daten verwenden. Es kann nicht garantiert werden, dass sich ein ausgeführter Akteurdienst mithilfe eines parallelen Upgrades und unter Beibehaltung der Daten von einem flüchtigen (ausschließlich speicherinternen) Zustand in einen persistenten Zustand überführen lässt. Die Replikatanzahl kann jedoch für einen ausgeführten Dienst geändert werden.

Schlüssel des Zustands-Managers müssen Zeichenfolgen sein. Werte sind generisch und können von beliebiger Art (u. a. benutzerdefiniert) sein. Im Zustands-Manager gespeicherte Werte müssen für den Datenvertrag serialisierbar sein, da sie abhängig von der Zustandspersistenzeinstellung eines Akteurs während der Replikation möglicherweise über das Netzwerk an andere Knoten übertragen und auf Datenträger geschrieben werden.

Der Zustands-Manager macht allgemeine Wörterbuchmethoden zum Verwalten des Zustands verfügbar. Diese ähneln den Methoden in Reliable Dictionary.

Beispiele für die Verwaltung von Actorzuständen finden Sie unter [Zugreifen auf die Zustände sowie Speichern und Entfernen der Zustände von Reliable Actors](service-fabric-reliable-actors-access-save-remove-state.md).

## <a name="best-practices"></a>Bewährte Methoden
Hier finden einige empfohlene Vorgehensweisen und Tipps zur Problembehandlung für die Verwaltung des Akteurzustands.

### <a name="make-the-actor-state-as-granular-as-possible"></a>Möglichst präziser Akteurzustand
Dies ist besonders wichtig für die Leistung und Ressourcennutzung Ihrer Anwendung. Bei jedem Schreibvorgang im „benannten Zustand“ eines Akteurs bzw. einer Aktualisierung davon, wird der gesamte, diesem „benannten Zustand“ entsprechende Wert serialisiert und über das Netzwerk an die sekundären Replikate gesendet.  Die sekundären Replikate schreiben ihn auf die lokale Festplatte und senden die Antwort zurück an das primäre Replikat. Wenn das primäre Replikat Bestätigungen von einem Quorum von sekundären Replikaten empfängt, schreibt es den Zustand auf seine lokale Festplatte. Nehmen wir beispielsweise an, dass der Wert eine Klasse mit 20 Membern und einer Größe von 1 MB ist. Auch wenn nur einer der Klassenmember mit einer Größe von 1 KB geändert wurde, bezahlen Sie letztendlich die Kosten der Serialisierung sowie der Netzwerk- und Datenträger-Schreibvorgänge für die Gesamtgröße von 1 MB. Wenn der Wert eine Auflistung (z.B. eine Liste, ein Array oder Wörterbuch) ist, fallen ebenso die Kosten für die vollständige Auflistung an, wenn Sie einen der zugehörigen Member ändern. Die StateManager-Schnittstelle der Akteurklasse ist wie ein Wörterbuch. Zusätzlich zu diesem Wörterbuch müssen Sie immer die Datenstruktur modellieren, die den Akteurzustand darstellt.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>Vorschriftsmäßige Verwaltung des Akteur-Lebenszyklus
Sie benötigen eindeutige Richtlinien zum Verwalten der Zustandsgröße in jeder Partition eines Akteurdiensts. Ihr Akteurdienst muss eine feste Anzahl von Akteuren aufweisen und diese nach Möglichkeit wiederverwenden. Wenn Sie laufend neue Akteure erstellen, müssen Sie diese löschen, nachdem sie ihren Zweck erfüllt haben. Das Akteur-Framework speichert einige Metadaten zu jedem vorhandenen Akteur. Beim Löschen des gesamten Zustands eines Akteurs werden dessen Metadaten nicht entfernt. Sie müssen den Akteur löschen (siehe [Löschen von Akteuren und deren Zuständen](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)), um alle im System dazu gespeicherten Informationen zu entfernen. Als eine zusätzliche Überprüfung sollten Sie den Akteurdienst bisweilen abfragen (siehe [Aufzählen von Akteuren](service-fabric-reliable-actors-enumerate.md)), um sicherzustellen, dass die Anzahl von Akteuren innerhalb des erwarteten Bereichs liegt.
 
Sollten Sie jemals feststellen, dass die Größe der Datenbankdatei eines Akteurdiensts die erwartete Größe überschreitet, stellen Sie sicher, dass die hier angegebenen Richtlinien eingehalten werden. Wenn Sie diese Richtlinien einhalten und die dennoch Probleme mit der Größe der Datenbankdatei auftreten, [öffnen Sie ein Supportticket](service-fabric-support.md) beim Produktteam, um Hilfe zu erhalten.

## <a name="next-steps"></a>Nächste Schritte

Zustand, der in Reliable Actors gespeichert wurde, muss serialisiert werden, bevor er in den Datenträger geschrieben und zur Hochverfügbarkeit repliziert wird. Erfahren Sie mehr über [Typserialisierung von Actors](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Und erfahren Sie mehr über [Diagnose und Leistungsüberwachung für Reliable Actors](service-fabric-reliable-actors-diagnostics.md).

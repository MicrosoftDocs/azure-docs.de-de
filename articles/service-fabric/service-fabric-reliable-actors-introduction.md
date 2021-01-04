---
title: 'Service Fabric Reliable Actors: Übersicht'
description: Einführung in das Programmiermodell Service Fabric Reliable Actors basierend auf dem Muster „Virtual Actor“.
ms.topic: conceptual
ms.date: 11/01/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 1a8a7003a69deaf6b74d6fbb8a3cf84b0a78eecf
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576382"
---
# <a name="introduction-to-service-fabric-reliable-actors"></a>Einführung in Service Fabric Reliable Actors
Reliable Actors ist ein Service Fabric-Anwendungsframework, das auf dem Muster [Virtueller Akteur](https://research.microsoft.com/en-us/projects/orleans/) basiert. Die Reliable Actors-API bietet ein Singlethread-Programmiermodell, das auf der Skalierbarkeit und Zuverlässigkeit aufbaut, die Service Fabric gewährleistet.

## <a name="what-are-actors"></a>Was sind Akteure?
Ein Akteur ist eine isolierte, unabhängige Einheit aus Computedienst und Zustand mit Singlethread-Ausführung. Das [Akteurmuster](https://en.wikipedia.org/wiki/Actor_model) ist ein Rechenmodell für parallel ausgeführte oder verteilte Systeme, in denen eine große Anzahl von diesen Akteuren gleichzeitig und unabhängig voneinander ausgeführt werden können. Akteure können miteinander kommunizieren und weitere Akteure erstellen.

### <a name="when-to-use-reliable-actors"></a>Anwendungsbereiche für Reliable Actors
Service Fabric Reliable Actors ist eine Implementierung des Akteurentwurfsmusters. Die Entscheidung, ob ein bestimmtes Muster verwendet werden soll, wird basierend darauf getroffen, ob ein Softwareentwurfsproblem dem Muster entspricht.

Obwohl das Akteurentwurfsmuster für eine Reihe von Problemen und Szenarien mit verteilten Systemen gut geeignet sein kann, müssen die Einschränkungen des Musters und des Frameworks für die Implementierung genau bedacht werden. Berücksichtigen Sie in Verbindung mit Ihrem Problem oder Szenario das Muster in den folgenden Fällen:

* Der Problembereich enthält zahlreiche (mehrere Tausend) kleine, unabhängige und isolierte Zustands- und Logikeinheiten.
* Sie möchten mit Singlethread-Objekten arbeiten, die keine wesentliche Interaktion von externen Komponenten erfordern (etwa, um den Zustand für einen Satz von Akteuren abzufragen).
* Ihre Akteurinstanzen blockieren Aufrufer durch die Ausgabe von E/A-Vorgängen nicht mit unvorhersagbaren Verzögerungen.

## <a name="actors-in-service-fabric"></a>Akteure in Service Fabric
In Service Fabric werden Akteure im Reliable Actors-Framework implementiert: Ein auf dem Akteurmuster basierendes Anwendungsframework, das auf [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md) aufbaut. Jeder von Ihnen geschriebene Reliable Actors-Dienst ist tatsächlich eine partitionierte, zustandsbehaftete Reliable Services-Instanz.

Jeder Akteur wird als Instanz eines Akteurtyps definiert (genau wie ein .NET-Objekt eine Instanz eines .NET-Typs darstellt). Beispielsweise kann es einen Actor-Typ geben, der die Funktionalität eines Rechners implementiert, und es kann viele Actors dieses Typs geben, die auf verschiedenen Knoten in einem Cluster verteilt sind. Jeder solche Actor wird durch eine Actor-ID eindeutig identifiziert.

## <a name="actor-lifetime"></a>Actor-Lebensdauer
Service Fabric-Actors sind virtuell. Daher ist ihre Lebensdauer nicht an die Darstellung im Arbeitsspeicher gebunden. Sie brauchen daher nicht explizit erstellt oder zerstört zu werden. Die Reliable Actors-Laufzeit aktiviert automatisch einen Akteur, wenn sie zum ersten Mal eine Anforderung für diese Akteur-ID empfängt. Wenn ein Akteur für einen bestimmten Zeitraum nicht verwendet wird, erfasst die Reliable Actors-Laufzeit das In-Memory-Objekt in der Garbage Collection. Sie verwaltet außerdem Informationen zur Existenz des Actors, falls dieser später erneut aktiviert werden muss. Weitere Informationen finden Sie unter [Actor-Lebenszyklus, automatische Garbage Collection und manuelles Löschen](service-fabric-reliable-actors-lifecycle.md).

Diese Abstraktion der Lebensdauer virtueller Akteure birgt infolge des Modells für virtuelle Akteure einige Nachteile, und die Reliable Actors-Implementierung weicht tatsächlich manchmal von diesem Modell ab.

* Ein Akteur wird automatisch aktiviert (und löst die Erstellung eines Akteurobjekts aus), sobald eine Nachricht zum ersten Mal an die Akteur-ID gesendet wird. Nach einer gewissen Zeit wird für das Akteurobjekt eine Garbage Collection ausgeführt. Wenn die Akteur-ID in Zukunft erneut verwendet wird, wird ein neues Akteurobjekt erstellt. Die Lebensdauer eines Akteurzustands ist länger als die Lebensdauer des Objekts, wenn der Akteur im Zustands-Manager gespeichert ist.
* Durch den Aufruf einer Akteurmethode für eine Akteur-ID wird dieser Akteur aktiviert. Aus diesem Grund wird der Konstruktor von Akteurtypen implizit von der Laufzeit aufgerufen. Daher kann Clientcode keine Parameter an den Konstruktor des Akteurtyps übergeben, obwohl Parameter vom Dienst selbst an den Akteurkonstruktor übergeben werden können. Das führt dazu, dass Akteure unter Umständen mit einem teilweise initialisierten Zustand erstellt werden, wenn andere Methoden dafür aufgerufen werden, falls der Akteur Initialisierungsparameter vom Client anfordert. Es gibt keinen einzelnen Einstiegspunkt für die Aktivierung eines Akteurs auf dem Client.
* Obwohl Reliable Actors Akteurobjekte implizit erstellt, haben Sie die Möglichkeit, einen Akteur und seinen Zustand explizit zu löschen.

## <a name="distribution-and-failover"></a>Verteilung und Failover
Um Skalierbarkeit und Zuverlässigkeit zu gewährleisten, verteilt Service Fabric Akteure im gesamten Cluster und migriert sie bei Bedarf automatisch von fehlerhaften zu fehlerfreien Knoten. Dies stellt eine Abstraktion einer [partitionierten, zustandsbehafteten Reliable Services-Instanz](service-fabric-concepts-partitioning.md)dar. Verteilung, Skalierbarkeit, Zuverlässigkeit und automatisches Failover werden aufgrund der Tatsache bereitgestellt, dass die Akteure in einer zustandsbehafteten Reliable Services-Instanz ausgeführt werden, die als *Actordienst* bezeichnet wird.

Akteure werden auf die Partitionen des Actordiensts und diese Partitionen auf die Knoten in einem Service Fabric-Cluster verteilt. Jede Dienstpartition enthält einen Satz von Akteuren. Service Fabric verwaltet die Verteilung und das Failover für die Dienstpartitionen.

Ein Actordienst mit neun Partitionen, der mithilfe der standardmäßigen Akteurpartitionsplatzierung auf drei Knoten bereitgestellt wird, wird beispielsweise wie folgt verteilt:

![Reliable Actors-Verteilung][2]

Das Akteurframework verwaltet für Sie die Einstellungen für das Partitionsschema und den Schlüsselbereich. Dadurch wird die Auswahl etwas vereinfacht, es müssen jedoch auch einige Punkte berücksichtigt werden:

* In Reliable Services können Sie ein Partitionierungsschema, einen Schlüsselbereich (bei Verwendung eines Bereichspartitionierungsschemas) und eine Partitionsanzahl auswählen. Reliable Actors ist auf das Bereichspartitionierungsschema (das einheitliche Int64-Schema) beschränkt und erfordert die Verwendung des vollständigen Int64-Schlüsselbereichs.
* Akteure werden standardmäßig nach dem Zufallsprinzip auf Partitionen platziert, was zu einer gleichmäßigen Verteilung führt.
* Da Akteure zufällig platziert werden, ist zu erwarten, dass Akteurvorgänge immer Netzwerkkommunikation erfordern (etwa zur Serialisierung und Deserialisierung von Methodenaufrufdaten), was zu Latenzen und Mehraufwand führt.
* In erweiterten Szenarien kann die Akteurpartitionsplatzierung mithilfe von Int64-Akteur-IDs gesteuert werden, die bestimmten Partitionen zugeordnet werden. Dies kann jedoch zu einer unausgeglichenen Verteilung von Akteuren auf Partitionen führen.

Weitere Informationen zur Partitionierung von Actordiensten finden Sie in den [Partitionierungskonzepten für Akteure](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

## <a name="actor-communication"></a>Actor-Kommunikation
Akteurinteraktionen werden in einer Schnittstelle definiert, die vom Akteur, der die Schnittstelle implementiert, und vom Client, der einen Proxy für einen Akteur über dieselbe Schnittstelle abruft, genutzt wird. Da diese Schnittstelle zum asynchronen Aufrufen von Akteurmethoden verwendet wird, muss jede Methode in der Schnittstelle einen Task zurückgeben.

Methodenaufrufe und die entsprechenden Antworten führen letztendlich zu Netzwerkanforderungen im Cluster, daher müssen die Argumente und die Ergebnistypen der zurückgegebenen Tasks von der Plattform serialisierbar sein. Sie müssen vor allem [Datenvertrag-serialisierbar](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)sein.

### <a name="the-actor-proxy"></a>Actor-Proxy
Die Reliable Actors-Client-API ermöglicht die Kommunikation zwischen einer Actor-Instanz und einem Actor-Client. Für die Kommunikation mit einem Actor erstellt ein Client ein Actor-Proxy-Objekt, das die Actor-Schnittstelle implementiert. Der Client interagiert mit dem Actor , indem er Methoden auf dem Proxy-Objekt aufruft. Der Actor-Proxy kann für die Client-zu-Actor- sowie für die Actor-zu-Actor-Kommunikation verwendet werden.

```csharp
// Create a randomly distributed actor ID
ActorId actorId = ActorId.CreateRandom();

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
IMyActor myActor = ActorProxy.Create<IMyActor>(actorId, new Uri("fabric:/MyApp/MyActorService"));

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
await myActor.DoWorkAsync();
```

```java
// Create actor ID with some name
ActorId actorId = new ActorId("Actor1");

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
MyActor myActor = ActorProxyBase.create(actorId, new URI("fabric:/MyApp/MyActorService"), MyActor.class);

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
myActor.DoWorkAsync().get();
```


Beachten Sie die beiden Informationen, die zum Erstellen des Actor-Proxyobjekts verwendet werden: die Actor-ID und der Name der Anwendung. Die Akteur-ID dient zur eindeutigen Identifikation des Akteurs, während der Anwendungsname die [Service Fabric-Anwendung](service-fabric-reliable-actors-platform.md#application-model) bezeichnet, in der der Akteur bereitgestellt wird.

Die Klasse `ActorProxy`(C#) / `ActorProxyBase`(Java) auf dem Client nimmt die erforderliche Auflösung vor, um den Akteur nach ID zu suchen und einen Kommunikationskanal mit ihm zu öffnen. Außerdem versucht sie bei Kommunikationsfehlern und Failovern, den Akteur zu finden. Die Nachrichtenübermittlung weist daher folgende Merkmale auf:

* Die Nachrichtenübermittlung ist vom Typ „Beste Leistung“.
* Akteure können doppelte Nachrichten vom selben Client empfangen.

## <a name="concurrency"></a>Parallelität
Die Reliable Actors-Laufzeit stellt ein einfaches Turn-basiertes Zugriffsmodell für den Zugriff auf Akteurmethoden bereit. Dies bedeutet, dass zu jedem beliebigen Zeitpunkt nur ein Thread innerhalb des Akteurobjektcodes aktiv sein kann. Der Turn-basierte Zugriff vereinfacht parallel ausgeführte Systeme, da keine Notwendigkeit für Synchronisierungsmechanismen für den Datenzugriff besteht. Das bedeutet auch, dass beim Entwurf dieser Systeme besonderes Augenmerk auf den Aspekt des Singlethread-Zugriffs der einzelnen Akteurinstanzen gelegt werden muss.

* Eine einzelne Akteurinstanz kann nicht mehrere Anforderungen gleichzeitig verarbeiten. Eine Akteurinstanz kann einen Engpass beim Durchsatz verursachen, wenn von ihr die Verarbeitung paralleler Anforderungen erwartet wird.
* Akteure können eine gegenseitige Sperre hervorrufen, wenn eine zirkuläre Anforderung zwischen zwei Akteuren besteht, während gleichzeitig eine externe Anforderung an einen der Akteure gesendet wird. Bei der Akteurlaufzeit tritt bei Akteuraufrufen automatisch ein Timeout auf, und es wird eine Ausnahme an den Aufrufer gesendet, um mögliche Sperren zu unterbrechen.

![Reliable Actors-Kommunikation][3]

### <a name="turn-based-access"></a>Rundenbasierter Zugriff
Ein Turn umfasst die vollständige Ausführung einer Akteurmethode als Antwort auf eine Anforderung von anderen Akteuren oder Clients oder die vollständige Ausführung eines [Timer-/Erinnerungs](service-fabric-reliable-actors-timers-reminders.md) -Rückrufs. Obwohl diese Methoden und Rückrufe asynchron sind, tritt keine Überschneidung mit der Actors-Laufzeit auf. Ein Turn muss vollständig abgeschlossen sein, bevor ein neuer Turn ausgeführt werden kann. Oder anders ausgedrückt: Ein aktuell ausgeführter Actor-Methoden- oder Timer-/Erinnerungs-Rückruf muss vollständig abgeschlossen sein, bevor ein neuer Methodenaufruf oder ein Rückruf ausgeführt werden kann. Eine Methode oder ein Rückruf wird als abgeschlossen betrachtet, wenn die Ausführung von der Methode oder dem Rückruf zurückgegeben wurde und die von der Methode oder dem Rückruf zurückgegebene Aufgabe abgeschlossen wurde. Es sollte hervorgehoben werden, dass Turn-basierte Parallelität selbst über verschiedene Methoden, Timer und Rückrufe hinweg eingehalten wird.

Die Actors-Laufzeit erzwingt Turn-basierte Parallelität durch Einsetzen einer Sperre pro Actor am Anfang des Turns und Aufheben der Sperre am Ende des Turns. Turn-basierte Parallelität wird daher auf einer Pro-Actor-Basis und nicht für alle Actors erzwungen. Actor-Methoden und Timer-/Erinnerungs-Rückrufe können für unterschiedliche Actors gleichzeitig ausgeführt werden.

Im folgenden Beispiel werden die obigen Konzepte veranschaulicht. Stellen Sie sich einen Akteurtyp vor, bei dem zwei asynchrone Methoden (z.B. *Method1* und *Method2*), ein Timer und eine Erinnerung implementiert werden. Das folgende Diagramm zeigt ein Zeitachsenbeispiel für die Ausführung dieser Methoden und Rückrufe für zwei Akteure (*ActorId1* und *ActorId2*), die zu diesem Akteurtyp gehören.

![Turn-basierte Parallelität und Zugriff in der Reliable Actors-Laufzeit][1]

In diesem Diagramm werden folgende Konventionen befolgt:

* Jede vertikale Linie zeigt den logischen Fluss der Ausführung einer Methode oder eines Rückrufs für einen bestimmten Actor.
* Die in jeder vertikalen Zeile gekennzeichneten Ereignisse treten in chronologischer Reihenfolge auf, wobei neuere Ereignisse unter den älteren angegeben werden.
* Für die Zeitachsen werden unterschiedliche Farben verwendet, die unterschiedlichen Actors entsprechen.
* Durch Hervorhebung wird die Dauer angegeben, während der die Sperre pro Actor für eine Methode oder einen Rückruf bestehen bleibt.

Einige wichtige Punkte sind zu beachten:

* Bei der Ausführung von *Method1* für *ActorId2* als Antwort auf Clientanforderung *xyz789* trifft eine weitere Clientanforderung (*abc123*) ein, für die ebenfalls die Ausführung von *Method1* für *ActorId2* erforderlich ist. Die zweite Ausführung von *Method1* beginnt aber erst, nachdem die vorherige Ausführung abgeschlossen wurde. Analog gilt: Eine von *ActorId2* registrierte Erinnerung wird ausgelöst, während *Method1* als Antwort auf die Clientanforderung *xyz789* ausgeführt wird. Der Erinnerungs-Rückruf wird erst ausgeführt, nachdem beide Ausführungen von *Method1* abgeschlossen sind. All dies basiert auf der für *ActorId2* erzwungenen Turn-basierten Parallelität.
* Analog dazu wird Turn-basierte Parallelität auch für *ActorId1* erzwungen, wie die serielle Ausführung von *Method1*, *Method2* und des Timer-Rückrufs für *ActorId1* zeigt.
* Die Ausführung von *Method1* für *ActorId1* überschneidet sich mit deren Ausführung für *ActorId2*. Der Grund dafür ist, dass Turn-basierte Parallelität nur innerhalb eines Actors und nicht Actor-übergreifend erzwungen wird.
* Bei einigen Ausführungen von Methode/Rückruf wird der von der Methode/dem Rückruf zurückgegebene `Task`(C#) / `CompletableFuture`(Java) nach der Rückkehr der Methode abgeschlossen. Bei einigen anderen ist der asynchrone Vorgang bereits zum Zeitpunkt der Rückkehr der Methode/des Rückrufs abgeschlossen. In beiden Fällen wird die Sperre pro Akteur erst freigegeben, nachdem sowohl die Methode/der Rückruf als auch der asynchrone Vorgang abgeschlossen ist.

### <a name="reentrancy"></a>Eintrittsinvarianz
Die Actors-Laufzeit lässt standardmäßig Eintrittsinvarianz zu. Dies bedeutet: Wenn eine Akteurmethode von *Actor A* eine Methode auf *Actor B* aufruft, die wiederum eine andere Methode auf *Actor A* aufruft, wird die Ausführung dieser Methode erlaubt. Der Grund dafür ist, dass sie zum selben logischen Kontext der Aufrufkette gehört. Alle Timer- und Erinnerungs-Aufrufe beginnen mit dem neuen logischen Aufrufkontext. Weitere Details finden Sie unter [Reliable Actors-Eintrittsinvarianz](service-fabric-reliable-actors-reentrancy.md) .

### <a name="scope-of-concurrency-guarantees"></a>Umfang der Parallelitätsgarantien
Die Actors-Laufzeit bietet diese Parallelitätsgarantien in Situationen, in denen sie den Aufruf dieser Methoden steuert. Beispielsweise bietet sie diese Garantien für die Methodenaufrufe, die als Antwort auf eine Clientanforderung erfolgen, sowie für Timer- und Erinnerungsrückrufe. Wenn jedoch der Actor-Code diese Methoden außerhalb der von der Actors-Laufzeit bereitgestellten Mechanismen direkt aufruft, kann die Laufzeit keine Parallelitätsgarantien bieten. Wenn z. B. die Methode im Kontext eines Tasks aufgerufen wird, der nicht mit dem von den Actor-Methoden zurückgegebenen Task verknüpft ist, kann die Laufzeitumgebung keine Parallelitätsgarantien bieten. Auch wenn die Methode von einem Thread aufgerufen wird, den der Actor selbst erstellt, kann die Laufzeit keine Parallelitätsgarantien bieten. Zum Durchführen von Hintergrundoperationen sollten Akteure daher [Actor-Timer oder Actor-Erinnerungen](service-fabric-reliable-actors-timers-reminders.md) verwenden, bei denen die Turn-basierte Parallelität berücksichtigt wird.

## <a name="next-steps"></a>Nächste Schritte
Beginnen Sie, indem Sie Ihren ersten Reliable Actors-Dienst erstellen:
   * [Erste Schritte mit Reliable Actors in .NET](service-fabric-reliable-actors-get-started.md)
   * [Erste Schritte mit Reliable Actors in Java](./service-fabric-create-your-first-linux-application-with-java.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
[2]: ./media/service-fabric-reliable-actors-introduction/distribution.png
[3]: ./media/service-fabric-reliable-actors-introduction/actor-communication.png

---
title: Reliable Actors in Service Fabric
description: Erfahren Sie, wie Reliable Actors auf den Ebenen der Reliable Services angeordnet sind und die Features der Service Fabric-Plattform verwenden.
ms.topic: conceptual
ms.date: 3/9/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: c3bd67e0f71e20a8b5a74b4b38e5b423a14dc08d
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575617"
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Verwendung der Service Fabric-Plattform durch Reliable Actors
In diesem Artikel wird die Funktionsweise von Reliable Actors auf der Azure Service Fabric-Plattform beschrieben. Reliable Actors arbeiten in einem Framework, das in einer Implementierung des zustandsbehafteten zuverlässigen Diensts gehostet wird, der als *Akteurdienst* bezeichnet wird. Der Akteurdienst enthält alle Komponenten, die zum Verwalten des Lebenszyklus und zum Übermitteln von Nachrichten für Ihre Akteure erforderlich sind:

* Die Actor Runtime verwaltet den Lebenszyklus und die Garbage Collection und erzwingt den Singlethread-Zugriff.
* Ein Actordienst-Remotelistener akzeptiert Remotezugriffsaufrufe für Akteure und sendet diese an einen Verteiler, damit sie an die entsprechende Akteurinstanz weitergeleitet werden.
* Der Akteurzustandsanbieter umschließt Zustandsanbieter (z.B. den Reliable Collections-Zustandsanbieter) und stellt einen Adapter für die Akteurzustandsverwaltung bereit.

Diese Komponenten bilden zusammen das Reliable Actors-Framework.

## <a name="service-layering"></a>Dienstebenen
Da der Akteurdienst selbst einer der Reliable Services ist, gelten alle Konzepte für Reliable Services wie [Anwendungsmodell](service-fabric-application-model.md), Lebenszyklus, [Verpackung](service-fabric-package-apps.md), [Bereitstellung](service-fabric-deploy-remove-applications.md), Upgrade und Skalierung auch für Akteurdienste.

![Akteurdienstebenen][1]

Das vorherige Diagramm zeigt die Beziehung zwischen dem Service Fabric-Anwendungsframework und dem Benutzercode. Blaue Elemente stellen das Reliable Services-Anwendungsframework dar, Orange stellt das Reliable Actors-Framework dar, und Grün steht für den Benutzercode.

In Reliable Services erbt der Dienst die `StatefulService`-Klasse. Diese Klasse ist selbst von `StatefulServiceBase` (oder `StatelessService` für zustandslose Dienste) abgeleitet. In Reliable Actors verwenden Sie den Akteurdienst. Der Akteurdienst stellt eine andere Implementierung der `StatefulServiceBase`-Klasse dar, die das Akteurmuster implementiert, wo Ihr Akteur ausgeführt wird. Da der Akteurdienst selbst nur eine Implementierung von `StatefulServiceBase` ist, können Sie einen eigenen Dienst schreiben, der von `ActorService` abgeleitet ist, und Funktionen auf Dienstebene auf die gleiche Weise implementieren wie bei der Vererbung von `StatefulService`. Beispiel:

* Dienstsicherung und -wiederherstellung
* Gemeinsame Verwendung von Funktionen für alle Akteure, z. B. ein Schaltkreisunterbrecher
* Remoteprozeduraufrufe für den Akteurdienst selbst sowie für einzelne Akteure

Weitere Informationen finden Sie unter [Implementieren von Features auf Dienstebene in Ihren Actordienst](service-fabric-reliable-actors-using.md).

## <a name="application-model"></a>Anwendungsmodell
Actordienste sind Reliable Services, deshalb ist auch das Anwendungsmodell dasselbe. Die Buildtools im Akteurframework erstellen jedoch einige der Anwendungsmodelldateien für Sie.

### <a name="service-manifest"></a>Dienstmanifest
Der Inhalt der Datei „ServiceManifest.xml“ zu Ihrem Akteurdienst wird automatisch von den Buildtools des Akteurframeworks generiert. Diese Datei enthält Folgendes:

* Typ des Akteurdiensts. Der Typname wird basierend auf den Projektnamen für Ihren Akteur generiert. Basierend auf dem Persistenzattribut des Akteurs wird das Flag HasPersistedState ebenfalls entsprechend festgelegt.
* Codepaket
* Konfigurationspaket
* Ressourcen und Endpunkte

### <a name="application-manifest"></a>Anwendungsmanifest
Die Buildtools des Akteurframeworks erstellen automatisch eine Standarddienstdefinition für den Actordienst. Die Standarddiensteigenschaften werden von den Buildtools aufgefüllt:

* Die Anzahl von Replikatgruppen wird durch das Persistenzattribut für den Akteur bestimmt. Jedes Mal, wenn das Persistenzattribut für den Akteur geändert wird, wird die Anzahl der Replikatgruppen in der Standarddienstdefinition entsprechend zurückgesetzt.
* Partitionsschema und Bereich werden auf Uniform Int64 mit dem kompletten Int64-Schlüsselbereich festgelegt.

## <a name="service-fabric-partition-concepts-for-actors"></a>Service Fabric-Partitionskonzepte für Actors
Actordienste sind partitionierte zustandsbehaftete Dienste. Jede Partition eines Actordiensts enthält einen Satz von Akteuren. Die Dienstpartitionen werden automatisch über mehrere Knoten in Service Fabric verteilt. Folglich werden die Akteurinstanzen verteilt.

![Akteurpartitionierung und -verteilung][5]

Reliable Services können mit anderen Partitionsschemas und Partitionsschlüsselbereichen erstellt werden. Der Akteurdienst verwendet das Int64-Partitionierungsschema mit dem vollständigen Int64-Schlüsselbereich für das Zuordnen von Akteuren zu Partitionen.

### <a name="actor-id"></a>Akteur-ID
Jedem im Dienst erstellten Akteur wird eine eindeutige ID zugeordnet, die durch die `ActorId` -Klasse dargestellt wird. Die `ActorId` ist ein nicht transparenter ID-Wert, der für die gleichmäßige Verteilung von Akteuren über die Dienstpartitionen verwendet werden kann, indem zufällige IDs generiert werden:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Für jedes `ActorId`-Element wird ein Int64-Hashwert erstellt. Aus diesem Grund muss der Akteurdienst ein Int64-Partitionierungsschema mit dem vollständigen Int64-Schlüsselbereich verwenden. Es können aber benutzerdefinierte ID-Werte für ein `ActorID`-Element verwendet werden (einschließlich GUIDs/UUIDs, Zeichenfolgen und Int64-Werten).

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```
```Java
ActorProxyBase.create(MyActor.class, new ActorId(UUID.randomUUID()));
ActorProxyBase.create(MyActor.class, new ActorId("myActorId"));
ActorProxyBase.create(MyActor.class, new ActorId(1234));
```

Bei Verwendung von GUIDs/UUIDs und Zeichenfolgen werden für die Werte Int64-Hashwerte erstellt. Allerdings wird bei der expliziten Bereitstellung eines Int64-Werts für eine `ActorId` der Int64-Wert direkt einer Partition zugeordnet, ohne dass ein weiteres Hashing erfolgt. Sie können dieses Verfahren verwenden, um zu steuern, in welcher Partition die Akteure platziert werden.


## <a name="next-steps"></a>Nächste Schritte
* [Actor-Zustandsverwaltung](service-fabric-reliable-actors-state-management.md)
* [Actor-Lebenszyklus und Garbage Collection](service-fabric-reliable-actors-lifecycle.md)
* [Actors API reference documentation](/dotnet/api/microsoft.servicefabric.actors?view=azure-dotnet)
* [.NET-Beispielcode](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-Beispielcode](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png

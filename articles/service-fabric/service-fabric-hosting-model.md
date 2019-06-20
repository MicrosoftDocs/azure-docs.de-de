---
title: Azure Service Fabric-Hostingmodell | Microsoft-Dokumentation
description: Dieser Artikel beschreibt die Beziehung zwischen Replikaten (oder Instanzen) eines bereitgestellten Service Fabric-Diensts und dem Dienst-Host-Prozess.
services: service-fabric
documentationcenter: .net
author: harahma
manager: chackdan
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2017
ms.author: harahma
ms.openlocfilehash: d2d958a89bff40483e1cd473538f7d1a6971d266
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60483570"
---
# <a name="azure-service-fabric-hosting-model"></a>Azure Service Fabric-Hostingmodell
Dieser Artikel bietet einen Überblick über von Azure Service Fabric bereitgestellte Anwendungshostingmodelle und beschreibt die Unterschiede zwischen dem Modell mit einem **gemeinsam genutzten Prozess** und dem Modell mit einem **exklusiven Prozess**. Er veranschaulicht grafisch die Bereitstellung einer Anwendung auf einem Service Fabric-Knoten beschreibt und die Beziehung zwischen Replikaten (oder Instanzen) des Diensts und dem Dienst-Host-Prozess.

Bevor Sie fortfahren, müssen Sie die verschiedenen Konzepte und Beziehungen kennen, die unter [Modellieren von Anwendungen in Service Fabric][a1] erläutert werden. 

> [!NOTE]
> In diesem Artikel werden folgende Begriffe mit folgenden Bedeutungen verwendet, sofern nicht explizit erwähnt wird, dass sie in einer anderen Bedeutung verwendet werden:
>
> - *Replikat* bezieht sich sowohl auf ein Replikat eines zustandsbehafteten Diensts als auch auf eine Instanz eines zustandslosen Diensts.
> - *CodePackage* wird gleichbedeutend mit dem *ServiceHost*-Prozess verwendet, der einen *ServiceType* registriert und Replikate von Diensten dieses *ServiceType* hostet.
>

Um das Hostingmodell zu verstehen, betrachten wir ein Beispiel im Detail. Angenommen, Sie verfügen über einen *ApplicationType* „MyAppType“, der den *ServiceType* „MyServiceType“ aufweist. „MyServiceType“ wird vom *ServicePackage* „MyServicePackage“ bereitgestellt, das über ein *CodePackage* „MyCodePackage“ verfügt. „MyCodePackage“ registriert *ServiceType* „MyServiceType“, sobald dieser ausgeführt wird.

Weiterhin angenommen, Sie verfügen über einen Cluster mit drei Knoten und erstellen die *Anwendung* **fabric:/App1** vom Typ „MyAppType“. In dieser Anwendung **fabric:/App1** erstellen Sie einen Dienst **fabric:/App1/ServiceA** vom Typ „MyServiceType“. Dieser Dienst verfügt über zwei Partitionen (**P1** und **P2**) und drei Replikate pro Partition. Das folgende Diagramm zeigt die Sicht dieser Anwendung, wie sie auf einem Knoten bereitgestellt wird.


![Diagramm der Knotenansicht der bereitgestellten Anwendung][node-view-one]


Service Fabric hat „MyServicePackage“ aktiviert, das „MyCodePackage“ gestartet hat, das wiederum Replikate aus beiden Partitionen hostet. Alle Knoten im Cluster weisen dieselbe Ansicht auf, da eine Anzahl von Replikaten pro Partition ausgewählt wurde, die der Anzahl der Knoten im Cluster entspricht. Jetzt erstellen wir einen weiteren Dienst, **fabric:/App1/ServiceB**, in der Anwendung **fabric:/App1**. Dieser Dienst verfügt über eine Partition (**P3**) und drei Replikate pro Partition. Das folgende Diagramm zeigt die neue Sicht auf dem Knoten:


![Diagramm der Knotenansicht der bereitgestellten Anwendung][node-view-two]


Service Fabric hat das neue Replikat für die Partition **P3** des Diensts **fabric:/App1/ServiceB** in die vorhandene Aktivierung von „MyServicePackage“ platziert. Jetzt erstellen wir eine weitere Anwendung, **fabric:/App2**, vom Typ „MyAppType“. In **fabric:/App2** erstellen wir den Dienst **fabric:/App2/ServiceA**. Dieser Dienst verfügt über zwei Partitionen (**P4** und **P5**) und drei Replikate pro Partition. Das folgende Diagramm zeigt die neue Knotenansicht:


![Diagramm der Knotenansicht der bereitgestellten Anwendung][node-view-three]


Service Fabric aktiviert eine neue Kopie von „MyServicePackage“, die eine neue Kopie von „MyCodePackage“ startet. Replikate aus beiden Partitionen des Diensts **fabric:/App2/ServiceA** (**P4** und **P5**) werden in diese neue Kopie „MyCodePackage“ eingefügt.

## <a name="shared-process-model"></a>Modell mit einem gemeinsam genutzten Prozess
Im vorherigen Abschnitt wurde das von Service Fabric bereitgestellte Standardhostingmodell beschrieben, das als Modell mit einem gemeinsam genutzten Prozess bezeichnet wird. In diesem Modell ist für jede Anwendung nur eine Kopie eines bestimmten *ServicePackage*-Elements auf einem Knoten aktiviert (diese Kopie startet alle darin enthaltenen *CodePackages*). Alle Replikate aller Dienste von einem bestimmten *ServiceType* werden in dem *CodePackage* platziert, das diesen *ServiceType* registriert. Dies bedeutet, dass alle Replikate aller Dienste auf einem Knoten eines bestimmten *ServiceType* denselben Prozess gemeinsam nutzen.

## <a name="exclusive-process-model"></a>Modell mit einem exklusiven Prozess
Das andere von Service Fabric bereitgestellte Hostingmodell ist das Modell mit einem exklusiven Prozess. In diesem Modell wird jedes Replikat auf einem Knoten in einem eigenen dedizierten Prozess ausgeführt. Service Fabric aktiviert eine neue Kopie von *ServicePackage* (diese startet alle darin enthaltenen *CodePackages*). Replikate werden in dem *CodePackage* platziert, das den *ServiceType* des Diensts registriert, zu dem das Replikat gehört. 

Wenn Sie Service Fabric Version 5.6 oder höher verwenden, können Sie das Modell mit einem exklusiven Prozess zum Zeitpunkt der Diensterstellung auswählen (mithilfe von [PowerShell][p1], [REST][r1] oder [FabricClient][c1]). Legen Sie **ServicePackageActivationMode** als „ExclusiveProcess“ fest.

```powershell
PS C:\>New-ServiceFabricService -ApplicationName "fabric:/App1" -ServiceName "fabric:/App1/ServiceA" -ServiceTypeName "MyServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1 -ServicePackageActivationMode "ExclusiveProcess"
```

```csharp
var serviceDescription = new StatelessServiceDescription
{
    ApplicationName = new Uri("fabric:/App1"),
    ServiceName = new Uri("fabric:/App1/ServiceA"),
    ServiceTypeName = "MyServiceType",
    PartitionSchemeDescription = new SingletonPartitionSchemeDescription(),
    InstanceCount = -1,
    ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
};

var fabricClient = new FabricClient(clusterEndpoints);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Wenn Sie über einen Standarddienst im Anwendungsmanifest verfügen, können Sie das Modell mit einem exklusiven Prozess auswählen, indem Sie das Attribut **ServicePackageActivationMode** festlegen:

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Jetzt erstellen wir einen weiteren Dienst, **fabric:/App1/ServiceC**, in der Anwendung **fabric:/App1**. Dieser Dienst verfügt über zwei Partitionen (**P6** und **P7**) und drei Replikate pro Partition. Legen Sie **ServicePackageActivationMode** als „ExclusiveProcess“ fest. Das folgende Diagramm zeigt die neue Ansicht des Knotens:


![Diagramm der Knotenansicht der bereitgestellten Anwendung][node-view-four]


Wie Sie sehen können, hat Service Fabric zwei neue Kopien von „MyServicePackage“ aktiviert (eine für jedes Replikat aus den Partitionen **P6** und **P7**). Service Fabric hat jedes Replikat in eine dedizierte Kopie von *CodePackage* platziert. Bei Verwendung des Modells mit einem exklusiven Prozess können für eine bestimmte Anwendung mehrere Kopien eines bestimmten *ServicePackage* auf einem Knoten aktiv sein. Im vorherigen Beispiel sind drei Kopien von „MyServicePackage“ für **fabric:/App1** aktiv. Jeder dieser aktiven Kopien von „MyServicePackage“ ist eine **ServicePackageActivationId** zugeordnet. Diese ID identifiziert diese Kopie in der Anwendung **fabric:/App1**.

Wenn Sie nur das Modell mit einem gemeinsam genutzten Prozess für eine Anwendung verwenden, ist auf einem Knoten nur eine aktive Kopie von *ServicePackage* vorhanden. Die **ServicePackageActivationId** für diese Aktivierung von *ServicePackage* ist eine leere Zeichenfolge. Dies ist z.B. bei **fabric:/App2** der Fall.

> [!NOTE]
>- Das Hostingmodell mit einem gemeinsam genutzten Prozess entspricht **ServicePackageActivationMode** = **SharedProcess**. Dies ist das Standardhostingmodell, und **ServicePackageActivationMode** muss zum Zeitpunkt der Erstellung des Diensts nicht festgelegt werden.
>
>- Das Hostingmodell mit einem exklusiven Prozess entspricht **ServicePackageActivationMode** = **ExclusiveProcess**. Um diese Einstellung zu verwenden, müssen Sie sie zum Zeitpunkt der Diensterstellung explizit angeben. 
>
>- Um das Hostingmodell eines Diensts zu ermitteln, fragen Sie die [Dienstbeschreibung][p2] ab und sehen sich den Wert von **ServicePackageActivationMode** an.
>
>

## <a name="work-with-a-deployed-service-package"></a>Arbeiten mit einem bereitgestellten Dienstpaket
Eine aktive Kopie eines *ServicePackage* auf einem Knoten wird als [bereitgestelltes Dienstpaket][p3] bezeichnet. Wenn Sie das Modell mit einem exklusiven Prozess zum Erstellen von Diensten verwenden, können für eine bestimmte Anwendung mehrere bereitgestellte Dienstpakete für dasselbe *ServicePackage* vorliegen. Wenn Sie Vorgänge ausführen, die für ein bestimmtes bereitgestelltes Dienstpaket gelten sollen, sollten Sie eine **ServicePackageActivationId** angeben, um dieses bestimmte bereitgestellte Dienstpaket zu identifizieren. Geben Sie z.B. eine ID an, wenn Sie [Integritätsberichte für ein bereitgestelltes Dienstpaket senden][p4] oder [das Codepaket eines bereitgestellten Dienstpakets neu starten][p5].

Sie können die **ServicePackageActivationId** eines bereitgestellten Dienstpakets ermitteln, indem Sie die Liste der auf einem Knoten [bereitgestellten Dienstpakete][p3] abfragen. Wenn Sie die [bereitgestellten Diensttypen][p6], [bereitgestellten Replikate][p7] und [bereitgestellten Codepakete][p8] auf einem Knoten abfragen, enthält das Abfrageergebnis auch die **ServicePackageActivationId** des übergeordneten bereitgestellten Dienstpakets.

> [!NOTE]
>- Im Hostingmodell mit einem gemeinsam genutzten Prozess wird auf einem bestimmten Knoten für eine bestimmte Anwendung nur eine Kopie eines *ServicePackage* aktiviert. Die zugehörige **ServicePackageActivationId** ist eine *leere Zeichenfolge* und muss bei der Ausführung von Vorgängen im Zusammenhang mit dem bereitgestellten Dienstpaket nicht angegeben werden. 
>
> - Im Hostingmodell mit einem exklusiven Prozess können auf einem bestimmten Knoten für eine bestimmte Anwendung eine oder mehrere Kopien eines *ServicePackage* aktiv sein. Jede Aktivierung verfügt über eine *nicht leere* **ServicePackageActivationId**, die bei der Ausführung von Vorgängen im Zusammenhang mit dem bereitgestellten Dienstpaket angegeben wird. 
>
> - Wird **ServicePackageActivationId** nicht angegeben, wird der Wert standardmäßig auf eine *leere Zeichenfolge* festgelegt. Wenn ein bereitgestelltes Dienstpaket vorhanden ist, das im Modell mit einem gemeinsam genutzten Prozess aktiviert wurde, wird der Vorgang in diesem Dienstpaket ausgeführt. Andernfalls ist der Vorgang nicht erfolgreich.
>
> - Fragen Sie die **ServicePackageActivationId** nicht nur einmal ab und speichern Sie im Cache. Die ID wird dynamisch generiert und kann sich aus verschiedenen Gründen ändern. Vor dem Ausführen eines Vorgangs, bei dem die **ServicePackageActivationId** benötigt wird, sollten Sie zunächst die Liste der auf einem Knoten [bereitgestellten Dienstpakete][p3] abfragen. Verwenden Sie dann die **ServicePackageActivationId** aus den Abfrageergebnissen, um den ursprünglichen Vorgang auszuführen.
>
>

## <a name="guest-executable-and-container-applications"></a>Ausführbare Gastanwendungsdatei- und Containeranwendungen
Service Fabric behandelt Anwendungen mit [ausführbaren Gastdateien][a2] und [Containern][a3] als zustandslose Dienste, die eigenständig sind. Es gibt keine Service Fabric-Runtime in *ServiceHost* (ein Prozess oder Container). Da diese Dienste eigenständig sind, gilt die Anzahl der Replikate pro *ServiceHost* für diese Dienste nicht. Die mit diesen Diensten am häufigsten verwendete Konfiguration ist eine einzelne Partition mit einem [InstanceCount][c2] von -1 (auf jedem Knoten des Clusters wird eine Kopie des Dienstcodes ausgeführt). 

Der standardmäßige **ServicePackageActivationMode** für diese Dienste ist **SharedProcess**. In diesem Fall aktiviert Service Fabric nur eine Kopie des *ServicePackage* auf einem Knoten für eine bestimmte Anwendung.  Das bedeutet, dass auf einem Knoten nur eine Kopie des Dienstcodes ausgeführt wird. Wenn Sie mehrere Kopien Ihres Dienstcodes auf einem Knoten ausführen möchten, geben Sie zum Zeitpunkt der Diensterstellung **ServicePackageActivationMode** als **ExclusiveProcess** an. Dies ist z.B. der Fall, wenn Sie mehrere Dienste (*Service1* bis *ServiceN*) eines *ServiceType* (im *ServiceManifest* angegeben) erstellen oder wenn Ihr Dienst über mehrere Partitionen verfügt. 

## <a name="change-the-hosting-model-of-an-existing-service"></a>Ändern des Hostingmodells eines vorhandenen Diensts
Zurzeit können Sie das Hostingmodell eines vorhandenen Diensts nicht von „gemeinsam genutzter Prozess“ zu „exklusiver Prozess“ (oder umgekehrt) ändern.

## <a name="choose-between-the-hosting-models"></a>Auswählen des richtigen Hostingmodells
Sie müssen überlegen, welches Hostingmodell Ihre Anforderungen am besten erfüllt. Das Modell mit einem gemeinsam genutzten Prozess nutzt Betriebssystemressourcen besser, da weniger Prozesse erzeugt werden und mehrere Replikate im selben Prozess Ports gemeinsam verwenden können. Wenn in einem der Replikate jedoch ein Fehler auftritt, durch den der Diensthost beendet werden muss, hat dies Auswirkungen auf alle anderen Replikate im selben Prozess.

 Das Modell mit einem exklusiven Prozess bietet eine bessere Isolierung, da jedes Replikat in einem eigenen Prozess ausgeführt wird. Wenn in einem der Replikate ein Fehler auftritt, wirkt sich dies nicht auf die anderen Replikate aus. Dieses Modell ist nützlich, wenn die gemeinsame Portnutzung durch das Kommunikationsprotokoll nicht unterstützt wird. Es erleichtert die Ressourcenkontrolle auf Replikatebene. Exklusive Prozesse verbrauchen jedoch mehr Betriebssystemressourcen, da für jedes Replikat auf dem Knoten ein Prozess erzeugt wird.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Überlegungen zum Modell mit einem exklusiven Prozess und zum Anwendungsmodell
Die meisten Anwendungen können in Service Fabric modelliert werden, indem Sie einen *ServiceType* pro *ServicePackage* angeben. 

In bestimmten Fällen erlaubt Service Fabric auch mehrere *ServiceTypes* pro *ServicePackage* (und ein *CodePackage* kann mehrere  *ServiceTypes* registrieren). In den folgenden Szenarien können diese Konfigurationen nützlich sein:

- Sie möchten die Verwendung von Betriebssystemressourcen dadurch optimieren, dass weniger Prozesse mit einer erhöhten Replikatdichte pro Prozess erzeugt werden.
- Replikate aus verschiedenen *ServiceTypes* müssen einige Daten mit hohen Initialisierungs- oder Arbeitsspeicherkosten gemeinsam nutzen.
- Sie verfügen über ein Angebot kostenloser Dienste, und Sie möchten einen Grenzwert für die Ressourcenverwendung festlegen, indem Sie alle Replikate des Diensts im gleichen Prozess platzieren.

Das Hostingmodell mit einem exklusiven Prozess ist nicht kohärent mit einem Anwendungsmodell, das über mehrere *ServiceTypes* pro *ServicePackage* verfügt. Grund hierfür ist, dass mehrere *ServiceTypes* pro *ServicePackage* dafür ausgelegt sind, eine höhere gemeinsame Ressourcennutzung von Replikaten zu erreichen. Zudem ermöglichen sie eine höhere Replikatdichte pro Prozess. Das Modell mit einem exklusiven Prozess ist für andere Ergebnisse konzipiert.

Betrachten Sie den Fall mehrerer *ServiceTypes* pro *ServicePackage*, wobei jeder *ServiceType* von einem anderen *CodePackage* registriert wird. Angenommen, Sie haben ein *ServicePackage* „MultiTypeServicePackage“ mit zwei *CodePackages*:

- „MyCodePackageA“ registriert *ServiceType* „MyServiceTypeA“.
- „MyCodePackageB“ registriert *ServiceType* „MyServiceTypeB“.

Jetzt erstellen Sie die Anwendung **fabric:/SpecialApp**. In **fabric:/SpecialApp** erstellen Sie die folgenden beiden Dienste mit dem Modell mit einem exklusiven Prozess:

- Den Dienst **fabric:/SpecialApp/ServiceA** vom Typ „MyServiceTypeA“ mit zwei Partitionen (**P1** und **P2**) und drei Replikaten pro Partition.
- Den Dienst **fabric:/SpecialApp/ServiceB** vom Typ „MyServiceTypeB“ mit zwei Partitionen (**P3** und **P4**) und drei Replikaten pro Partition.

Beide Dienste verfügen auf jedem Knoten über je zwei Replikate. Da Sie das Modell mit einem exklusiven Prozess zum Erstellen der Dienste verwendet haben, aktiviert Service Fabric eine neue Kopie von „MyServicePackage“ für jedes Replikat. Jede Aktivierung von „MultiTypeServicePackage“ startet eine Kopie von „MyCodePackageA“ und „MyCodePackageB“. Allerdings wird das Replikat, für das „MultiTypeServicePackage“ aktiviert wurde, nur entweder von „MyCodePackageA“ oder „MyCodePackageB“ gehostet. Das folgende Diagramm zeigt die Knotenansicht:


![Diagramm der Knotenansicht der bereitgestellten Anwendung][node-view-five]


In der Aktivierung von „MultiTypeServicePackage“ für das Replikat der Partition **P1** des Diensts **fabric:/SpecialApp/ServiceA** hostet „MyCodePackageA“ das Replikat. „MyCodePackageB“ wird ausgeführt. Gleiches gilt für die Aktivierung von „MultiTypeServicePackage“ für das Replikat der Partition **P3** des Diensts **fabric:/SpecialApp/ServiceB**: „MyCodePackageB“ hostet das Replikat. „MyCodePackageA“ wird ausgeführt. Daher gilt: Je höher die Anzahl von *CodePackages* (die verschiedene *ServiceTypes* registrieren) pro *ServicePackage* ist, desto höher wird die redundante Ressourcenverwendung. 
 
 Wenn Sie jedoch die Dienste **fabric:/SpecialApp/ServiceA** und **fabric:/SpecialApp/ServiceB** mit dem Modell mit einem gemeinsam genutzten Prozess erstellen, aktiviert Service Fabric nur eine Kopie von „MultiTypeServicePackage“ für die Anwendung **fabric:/SpecialApp**. „MyCodePackageA“ hostet alle Replikate für den Dienst **fabric:/SpecialApp/ServiceA**. „MyCodePackageB“ hostet alle Replikate für den Dienst **fabric:/SpecialApp/ServiceB**. Das folgende Diagramm zeigt die Knotensicht in dieser Einstellung: 


![Diagramm der Knotenansicht der bereitgestellten Anwendung][node-view-six]


Sie könnten einwenden, dass im vorangehenden Beispiel keine redundante *CodePackage*-Ausführung erfolgt, wenn „MyCodePackageA“ sowohl „MyServiceTypeA“ als auch „MyServiceTypeB“ registriert, aber kein „MyCodePackageB“ existiert. Das ist zwar richtig, aber dieses Anwendungsmodell steht nicht in Einklang mit dem Hostingmodell mit einem exklusive Prozess. Wenn das Ziel darin besteht, jedes Replikat in einem eigenen dedizierten Prozess zu verarbeiten, müssen nicht beide *ServiceTypes* aus demselben *CodePackage* registriert werden. Stattdessen platzieren Sie einfach jeden *ServiceType* in einem eigenen *ServicePackage*.

## <a name="next-steps"></a>Nächste Schritte
[Packen][a4] und Vorbereiten einer Anwendung für die Bereitstellung.

[Bereitstellen und Entfernen von Anwendungen][a5]. Dieser Artikel beschreibt, wie Sie Anwendungsinstanzen mithilfe von PowerShell verwalten.

<!--Image references-->
[node-view-one]: ./media/service-fabric-hosting-model/node-view-one.png
[node-view-two]: ./media/service-fabric-hosting-model/node-view-two.png
[node-view-three]: ./media/service-fabric-hosting-model/node-view-three.png
[node-view-four]: ./media/service-fabric-hosting-model/node-view-four.png
[node-view-five]: ./media/service-fabric-hosting-model/node-view-five.png
[node-view-six]: ./media/service-fabric-hosting-model/node-view-six.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-guest-executables-introduction.md
[a3]: service-fabric-containers-overview.md
[a4]: service-fabric-package-apps.md
[a5]: service-fabric-deploy-remove-applications.md

[r1]: https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-createservice

[c1]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync
[c2]: https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription.instancecount

[p1]: https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice
[p2]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricservicedescription
[p3]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicePackage
[p4]: https://docs.microsoft.com/powershell/module/servicefabric/send-servicefabricdeployedservicepackagehealthreport
[p5]: https://docs.microsoft.com/powershell/module/servicefabric/restart-servicefabricdeployedcodepackage
[p6]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicetype
[p7]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedreplica
[p8]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedcodepackage

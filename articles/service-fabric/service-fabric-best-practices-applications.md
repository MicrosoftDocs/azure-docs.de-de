---
title: Bewährte Methoden für den Azure Service Fabric-Anwendungsentwurf | Microsoft-Dokumentation
description: Bewährte Methoden für die Entwicklung von Service Fabric-Anwendungen.
services: service-fabric
documentationcenter: .net
author: markfussell
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/26/2019
ms.author: msfussell
ms.openlocfilehash: 55f043effc7cdb102acea856e89c58f660d0cde5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65238116"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Bewährte Methoden für den Azure Service Fabric-Anwendungsentwurf

Dieser Artikel enthält bewährte Methoden für das Erstellen von Anwendungen und Dienste in Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Kennenlernen von Service Fabric
* [Sie möchten sich über Service Fabric informieren?](service-fabric-content-roadmap.md)
* Informationen zu [Service Fabric-Anwendungsszenarien](service-fabric-application-scenarios.md)
* Machen Sie sich dann mit den Auswahlmöglichkeiten für das Programmiermodell anhand der [Übersicht über das Service Fabric-Programmiermodell](service-fabric-choose-framework.md) vertraut.



## <a name="application-design-guidance"></a>Leitfaden für den Anwendungsentwurf
Machen Sie sich mit der [allgemeinen Architektur](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) einer Service Fabric-Anwendung und ihren [Entwurfsüberlegungen](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations) vertraut.

### <a name="choose-an-api-gateway"></a>Auswählen eines API-Gateways
Verwenden Sie einen API-Gatewaydienst, der mit Back-End-Diensten kommuniziert, die dann horizontal hochskaliert werden können. Die folgenden API-Gatewaydienste werden am häufigsten verwendet:

- [Azure API Management](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview). Dieser Dienst ist [in Service Fabric integriert](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management).
- [Azure IoT-Hub](https://docs.microsoft.com/azure/iot-hub/) oder [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/) mit [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) zum Lesen aus Event Hub-Partitionen.
- [Træfik-Reverseproxy](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/) mit dem [Azure Service Fabric-Anbieter](https://docs.traefik.io/configuration/backends/servicefabric/).
- [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/). Hinweis: Dieser Dienst ist nicht direkt in Service Fabric integriert, und Azure API Management stellt in der Regel die bevorzugte Option dar.
- Erstellen eines eigenen [ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore)-Webanwendungsgateways

### <a name="choose-stateless-services"></a>Auswählen zustandsloser Dienste
Wir empfehlen, dass Sie immer damit beginnen, zustandslose Dienste mit [Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) zu erstellen, die den Zustand in einer Azure-Datenbank, in Azure CosmosDB oder Azure Storage speichern. Die Auslagerung des Status ist für die meisten Entwickler der gängigere Ansatz und ermöglicht es Ihnen, auch die Abfragemöglichkeiten für den Speicher zu nutzen.  

### <a name="when-to-choose-stateful-services"></a>Auswählen zustandsbehafteter Dienste
Ziehen Sie zustandsbehaftete Dienste in Betracht, wenn Sie ein Szenario mit geringer Latenzzeit haben und die Daten in der Nähe des Computes halten müssen. Einige Beispiele sind digitale IoT-Zwillingsgeräte, Spielstatus, Sitzungsstatus, Zwischenspeicherung von Daten aus einer Datenbank und zeitintensive Workflows zur Verfolgung von Aufrufen anderer Dienste.

Auswählen der Aufbewahrungszeit für Daten:

- Zwischengespeicherte Daten – Sie verwenden Zwischenspeicherung, wenn die Latenz zu externen Speichern ein Problem darstellt. Verwenden Sie einen zustandsbehafteten Dienst als eigenen Datencache, oder verwenden Sie die [Open-Source-Option „SoCreate service-fabric-distributed-cache“](https://github.com/SoCreate/service-fabric-distributed-cache). In diesem Szenario können Sie alle Daten im Cache verlieren, ohne dass es eine Rolle spielt.
- Zeitgebundene Daten – Sie müssen Daten aus Latenzgründen eine Zeit lang nah am Compute halten, aber diese Daten dürfen in einem *Notfallszenario* verloren gehen. So müssen beispielsweise in vielen IoT-Lösungen die Daten nah am Compute sein, etwa bei der Berechnung der Durchschnittstemperatur der letzten Tage. Wenn diese Daten aber verloren gehen, dann sind die aufgezeichneten spezifischen Datenpunkte nicht so wichtig. In diesem Szenario ist auch die Sicherung der einzelnen Datenpunkte in der Regel nicht von Bedeutung, sondern nur der berechneten Durchschnittswerte, die regelmäßig in den externen Speicher geschrieben werden.  
- Langfristige Daten – Zuverlässige Sammlungen können Ihre Daten dauerhaft speichern. In diesem Fall müssen Sie sich jedoch auf eine [Notfallwiederherstellung](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery) vorbereiten, einschließlich der [Konfiguration von regelmäßigen Sicherungsrichtlinien](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) für Ihre Cluster. Im Endeffekt konfigurieren Sie, was passiert, wenn Ihr Cluster in einem Katastrophenszenario zerstört wird, wo Sie einen neuen Cluster erstellen müssen und wie Sie neue Anwendungsinstanzen bereitstellen und nach der letzten Sicherung wiederherstellen.

Sparen von Kosten und Verbessern der Verfügbarkeit:
- Mit zustandsbehafteten Diensten können Sie die Kosten senken, da Ihnen keine Datenzugriffs- und Transaktionskosten aus dem Remotespeicher entstehen und Sie keinen weiteren Dienst wie etwa Azure Redis verwenden müssen.
- Die Verwendung von zustandsbehafteten Diensten hauptsächlich für die Speicherung und nicht für Compute ist teuer und wird nicht empfohlen. Betrachten Sie zustandsbehaftete Dienste als Compute mit günstigem lokalem Speicher.
- Durch das Entfernen von Abhängigkeiten von anderen Diensten können Sie die Dienstverfügbarkeit verbessern. Wenn Sie den Zustand mit Hochverfügbarkeit im Cluster verwalten, sind Sie vor anderen Dienstausfällen oder Latenzproblemen geschützt.

## <a name="how-to-properly-work-with-reliable-services"></a>Ordnungsgemäßes Arbeiten mit Reliable Services
Reliable Services ermöglichen Ihnen das einfache Erstellen von zustandslosen und zustandsbehafteten Diensten. Lesen Sie die [Einführung in Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction).
- Berücksichtigen Sie immer das [Abbruchtoken](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) in der `RunAsync()`-Methode für zustandslose und zustandsbehaftete Dienste und die `ChangeRole()`-Methode für zustandsbehaftete Dienste. Ohne diese Angaben weiß Service Fabric nicht, ob Ihr Dienst geschlossen werden kann. Beispielsweise kann die Nichtbeachtung des Abbruchtokens zu wesentlich längeren Upgradezeiten für Anwendungen führen.
-   Öffnen und schließen Sie [Kommunikationslistener](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) rechtzeitig, und berücksichtigen Sie das Abbruchtoken.
-   Kombinieren Sie niemals synchronen Code mit asynchronem Code. Verwenden Sie beispielsweise nicht `.GetAwaiter().GetResult()` in Ihren asynchronen Aufrufen. Der Code muss *in der gesamten* Aufrufliste asynchron sein.

## <a name="how-to-properly-work-with-reliable-actors"></a>Ordnungsgemäßes Arbeiten mit Reliable Actors
Reliable Actors ermöglicht Ihnen das einfache Erstellen von zustandsbehafteten virtuellen Akteuren. Lesen Sie die [Einführung in Reliable Actors](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction).

- Ziehen Sie unbedingt Veröffentlichen/Abonnieren-Messaging zwischen Ihren Akteuren für die Skalierung der Anwendung in Betracht. Beispielsweise [Open-Source SoCreate Pub/Sub](https://service-fabric-pub-sub.socreate.it/) oder [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).
- Stellen Sie den Akteurzustand so [präzise wie möglich](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices) dar.
- Verwalten Sie den [Lebenszyklus des Akteurs](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices). Löschen Sie Akteure, wenn Sie nicht vorhaben, sie jemals wieder zu verwenden. Dies gilt insbesondere bei Verwendung des [VolatileState-Anbieters](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication), da alle Zustände im Arbeitsspeicher gespeichert werden.
- Aufgrund ihrer [turnusbasierten Parallelität](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency) werden Akteure am besten als unabhängige Objekte verwendet. Erstellen Sie keine Graphen mit synchronen Methodenaufrufen mit mehreren Akteuren (von denen jeder höchstwahrscheinlich zu einem separaten Netzwerkaufruf wird), und verwenden Sie keine zirkulären Akteuranforderungen. Diese beeinflussen die Leistung und Skalierung erheblich.
- Kombinieren Sie synchronen Code nicht mit asynchronem Code. Der gesamte Code muss asynchron sein, um Leistungsprobleme zu vermeiden.
- Nehmen Sie keine zeitintensiven Aufrufe in Akteuren vor. Diese blockieren andere Aufrufe des gleichen Akteurs aufgrund der turnusbasierten Parallelität.
- Wenn Sie mit anderen Diensten über [Service Fabric-Remoting](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) kommunizieren und eine `ServiceProxyFactory` erstellen, dann erstellen Sie die Factory auf der [Akteurdienst](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using)ebene und *nicht* auf der Akteurebene.


## <a name="application-diagnostics"></a>Anwendungsdiagnose
- Fügen Sie konsequent [Anwendungsprotokollierung](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) in Dienstaufrufen hinzu. Sie hilft bei der Diagnose in Szenarien, in denen Dienste sich gegenseitig aufrufen. Wenn beispielsweise A->B->C->D gilt, kann der Aufruf überall fehlschlagen. Wenn nicht genügend Protokollierung vorhanden ist, ist ein solcher Fehler schwierig zu diagnostizieren. Wenn die Dienste aufgrund des Aufrufaufkommens zu viel protokollieren, sollten Sie zumindest Fehler und Warnungen protokollieren.

## <a name="iot-and-messaging-applications"></a>IoT und Messaginganwendungen
Wenn Sie Nachrichten aus [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) oder [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/) lesen, verwenden Sie [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) (in Service Fabric Reliable Services integriert), um den Zustand des Lesens von den Event Hub-Partitionen zu verwalten und neue Nachrichten über die `IEventProcessor::ProcessEventsAsync()`-Methode an Ihre Dienste zu pushen.


## <a name="design-guidance-on-azure"></a>Entwurfsleitfaden für Azure
* Besuchen Sie das [Azure Architecture Center](https://docs.microsoft.com/azure/architecture/microservices/). Dort finden Sie einen Entwurfsleitfaden zum [Erstellen von Microservices in Azure](https://docs.microsoft.com/azure/architecture/microservices/).

* Lesen Sie [Erste Schritte mit Azure für Gaming](https://docs.microsoft.com/gaming/azure/). Dort finden Sie einen Entwurfsleitfaden zum [Verwenden von Service Fabric in Gamingdiensten](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf).

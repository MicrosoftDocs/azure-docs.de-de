---
title: Dienstremoting mit Java in Azure Service Fabric | Microsoft-Dokumentation
description: Service Fabric-Remoting ermöglicht Clients und Diensten die Kommunikation mit Java-Diensten über einen Remoteprozeduraufruf.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: chackdan
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 51c8c689bd3fe3e8967bab77e776ad02f9cb59f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62123634"
---
# <a name="service-remoting-in-java-with-reliable-services"></a>Dienstremoting in Java mit Reliable Services
> [!div class="op_single_selector"]
> * [C# unter Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java unter Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Für WebAPI, WCF (Windows Communication Foundation) und andere Dienste, die nicht an ein bestimmtes Kommunikationsprotokoll oder einen bestimmten Kommunikationsstapel gebunden sind, stellt das Reliable Services-Framework einen Remotingmechanismus für das schnelle, einfache Einrichten von Remoteprozeduraufrufen für Dienste bereit.  In diesem Artikel wird das Einrichten von Remoteprozeduraufrufen für Dienste, die mit Java geschrieben wurden, erläutert.

## <a name="set-up-remoting-on-a-service"></a>Einrichten von Remoting für einen Dienst
Die Einrichtung von Remoting für einen Dienst erfolgt in zwei einfachen Schritten:

1. Erstellen Sie eine Schnittstelle, die vom Dienst implementiert werden soll. Diese Schnittstelle definiert die Methoden, die für den Remoteprozeduraufruf für Ihren Dienst verfügbar sind. Bei den Methoden muss es sich um asynchrone Methoden handeln, die einen Task zurückgeben. Die Schnittstelle muss `microsoft.serviceFabric.services.remoting.Service` implementieren, um zu signalisieren, dass der Dienst über eine Remotingschnittstelle verfügt.
2. Verwenden Sie einen Remoting-Listener in Ihrem Dienst. Dies ist eine `CommunicationListener` -Implementierung, die Remotingfunktionen bereitstellt. `FabricTransportServiceRemotingListener` kann verwendet werden, um einen Remotinglistener zu erstellen, der das standardmäßige Remoting-Transportprotokoll nutzt.

Der folgende zustandslose Dienst macht beispielsweise eine einzelne Methode verfügbar, um „Hello World“ per Remoteprozeduraufruf abzurufen:

```java
import java.util.ArrayList;
import java.util.concurrent.CompletableFuture;
import java.util.List;
import microsoft.servicefabric.services.communication.runtime.ServiceInstanceListener;
import microsoft.servicefabric.services.remoting.Service;
import microsoft.servicefabric.services.runtime.StatelessService;

public interface MyService extends Service {
    CompletableFuture<String> helloWorldAsync();
}

class MyServiceImpl extends StatelessService implements MyService {
    public MyServiceImpl(StatelessServiceContext context) {
       super(context);
    }

    public CompletableFuture<String> helloWorldAsync() {
        return CompletableFuture.completedFuture("Hello!");
    }

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
        listeners.add(new ServiceInstanceListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));
        return listeners;
    }
}
```

> [!NOTE]
> Bei den Argumenten und Rückgabetypen in der Dienstschnittstelle kann es sich um einfache, komplexe oder benutzerdefinierte Typen handeln. Sie müssen jedoch serialisierbar sein.
>
>

## <a name="call-remote-service-methods"></a>Aufrufen von Remotedienstmethoden
Methoden für einen Dienst, der den Remotingstapel nutzt, werden mithilfe eines lokalen Proxys für den Dienst über die `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` -Klasse abgerufen. Die `ServiceProxyBase` -Methode erstellt einen lokalen Proxy unter Verwendung der gleichen Schnittstelle, die auch der Dienst implementiert. Mit diesem Proxy können Sie Methoden für die Schnittstelle einfach remote aufrufen.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

Das Remotingframework gibt beim Dienst aufgetretene Ausnahmen an den Client weiter. Somit kann die Ausnahmebehandlungslogik auf dem Client Ausnahmen, die vom Dienst ausgegeben werden, mithilfe von `ServiceProxyBase` direkt behandeln.

## <a name="service-proxy-lifetime"></a>Gültigkeitsdauer von Dienstproxys
Die Erstellung von Dienstproxys ist ein einfacher Vorgang, der sehr wenige Ressourcen verbraucht, sodass Sie so viele erstellen können, wie Sie benötigen. Dienstproxyinstanzen können erneut verwendet werden, solange sie benötigt werden. Wenn ein Remoteprozeduraufruf eine Ausnahme auslöst, können Sie weiterhin dieselbe Proxyinstanz wiederverwenden. Jeder Dienstproxy enthält einen Kommunikationsclient zum Senden von Nachrichten im Netzwerk. Beim Aufruf von Remoteaufrufen wird intern geprüft, ob der Kommunikationsclient gültig ist. Basierend auf den Ergebnissen dieser Überprüfungen wird der Kommunikationsclient bei Bedarf neu erstellt. Darum müssen Sie im Falle einer Ausnahme `ServiceProxy` nicht neu erstellen.

### <a name="serviceproxyfactory-lifetime"></a>Gültigkeitsdauer von „ServiceProxyFactory“
[FabricServiceProxyFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.fabricserviceproxyfactory) ist eine Factory, die einen Proxy für verschiedene Remotingschnittstellen erstellt. Wenn Sie die API `ServiceProxyBase.create` verwenden, um Proxys zu erstellen, erstellt das Framework eine `FabricServiceProxyFactory`.
Es ist sinnvoll, eine Factory manuell zu erstellen, wenn Sie [IServiceRemotingClientFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.serviceremotingclientfactory)-Eigenschaften überschreiben müssen.
„Factory“ ist ein aufwendiger Vorgang. `FabricServiceProxyFactory` verwaltet den Cache von Kommunikationsclients.
Eine bewährte Methode besteht darin, `FabricServiceProxyFactory` solange wie möglich zwischenzuspeichern.

## <a name="remoting-exception-handling"></a>Behandlung von Remotingausnahmen
Alle von der Dienstverwaltungs-API ausgelösten remoten Ausnahmen werden entweder als „RuntimeException“ oder „FabricException“ an den Client zurückgesendet.

„ServiceProxy“ verarbeitet sämtliche Failoverausnahmen für die Dienstpartition, für die seine Erstellung erfolgt ist. Dieser Proxy löst die Endpunkte erneut auf, falls Failoverausnahmen (nicht vorübergehende Ausnahmen) vorliegen, und wiederholt den Aufruf mit dem richtigen Endpunkt. Die Anzahl der Wiederholungen bei Failoverausnahmen ist unbegrenzt.
Im Falle vorübergehender Ausnahmen wird nur der Aufruf wiederholt.

Standardparameter für die Wiederholung werden von [OperationRetrySettings](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.communication.client.operationretrysettings) angegeben.
Sie können diese Werte jetzt konfigurieren, indem Sie das OperationRetrySettings-Objekt an den ServiceProxyFactory-Konstruktor übergeben.

## <a name="next-steps"></a>Nächste Schritte
* [Absichern der Kommunikation für Reliable Services](service-fabric-reliable-services-secure-communication-java.md)

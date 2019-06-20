---
title: WCF-Kommunikationsstapel für Reliable Services | Microsoft Docs
description: Der integrierte WCF-Kommunikationsstapel in Service Fabric bietet Clientdienst-WCF-Kommunikation für Reliable Services.
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: chackdan
editor: vturecek
ms.assetid: 75516e1e-ee57-4bc7-95fe-71ec42d452b2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/07/2017
ms.author: bharatn
ms.openlocfilehash: ae8a0ab0382083ebfca0834d2238403668efa71d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60725600"
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>WCF-basierter Kommunikationsstapel für Reliable Services
Das Reliable Services-Framework ermöglicht Dienstautoren, den Kommunikationsstapel für ihren Dienst zu wählen. Sie können den Kommunikationsstapel ihrer Wahl über den von der **CreateServiceReplicaListeners- oder CreateServiceInstanceListeners** -Methode zurückgegebenen [ICommunicationListener](service-fabric-reliable-services-communication.md) implementieren. Das Framework bietet eine Implementierung des auf WCF (Windows Communication Foundation) basierenden Kommunikationsstapels für Dienstautoren, die eine WCF-basierte Kommunikation verwenden möchten.

## <a name="wcf-communication-listener"></a>WCF-Kommunikationslistener
Die WCF-spezifische Implementierung von **ICommunicationListener** wird von der **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener**-Klasse bereitgestellt.

Angenommen, wir haben einen Dienstvertrag vom Typ `ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

Wir können auf folgende Weise einen WCF-Kommunikationslistener im Dienst erstellen.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[] { new ServiceReplicaListener((context) =>
        new WcfCommunicationListener<ICalculator>(
            wcfServiceObject:this,
            serviceContext:context,
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            endpointResourceName: "WcfServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            listenerBinding: WcfUtility.CreateTcpListenerBinding()
        )
    )};
}

```

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Schreiben von Clients für den WCF-Kommunikationsstapel
Für das Schreiben von Clients, die über WCF mit Diensten kommunizieren, bietet das Framework **WcfClientCommunicationFactory**. Dies ist die WCF-spezifische Implementierung von [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md).

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

Auf den WCF-Kommunikationskanal kann vom **WcfCommunicationClient** aus zugegriffen werden, der von **WcfCommunicationClientFactory** erstellt wurde.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Clientcode kann **WcfCommunicationClientFactory** zusammen mit dem **WcfCommunicationClient** verwenden, welcher den **ServicePartitionClient** implementiert. Damit kann der Code den Dienstendpunkt festlegen und mit dem Dienst kommunizieren.

```csharp
// Create binding
Binding binding = WcfUtility.CreateTcpClientBinding();
// Create a partition resolver
IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();
// create a  WcfCommunicationClientFactory object.
var wcfClientFactory = new WcfCommunicationClientFactory<ICalculator>
    (clientBinding: binding, servicePartitionResolver: partitionResolver);

//
// Create a client for communicating with the ICalculator service that has been created with the
// Singleton partition scheme.
//
var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
                wcfClientFactory,
                ServiceUri,
                ServicePartitionKey.Singleton);

//
// Call the service to perform the operation.
//
var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
                client => client.Channel.Add(2, 3)).Result;

```
> [!NOTE]
> Der Standard-ServicePartitionResolver geht davon aus, dass der Client im selben Cluster wie der Dienst ausgeführt wird. Wenn das nicht der Fall ist, erstellen Sie ein ServicePartitionResolver-Objekt, und übergeben Sie die Cluster-Verbindungsendpunkte.
> 
> 

## <a name="next-steps"></a>Nächste Schritte
* [Remoteprozeduraufruf mit Reliable Services-Remoting](service-fabric-reliable-services-communication-remoting.md)
* [Web-API mit OWIN in Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Absichern der Kommunikation für Reliable Services](service-fabric-reliable-services-secure-communication-wcf.md)


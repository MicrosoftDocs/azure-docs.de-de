---
title: Schützen der WFC-basierten Dienstkommunikation in Azure Service Fabric | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie die WFC-basierte Kommunikation für Reliable Services schützen, die in einem Azure Service Fabric-Cluster ausgeführt werden.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: chackdan
editor: vturecek
ms.assetid: fc129c1a-fbe4-4339-83ae-0e69a41654e0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: suchiagicha
ms.openlocfilehash: 26d34f0473dec5e0767041df400b84887a0d1778
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60725583"
---
# <a name="secure-wcf-based-communications-for-a-service"></a>Schützen der WFC-basierten Kommunikation für einen Dienst
Sicherheit ist einer der wichtigsten Aspekte der Kommunikation. Das Reliable Services-Anwendungsframework stellt einige fertige Kommunikationsstapel und Tools bereit, die Sie verwenden können, um die Sicherheit zu verbessern. In diesem Artikel erfahren Sie, wie Sie die Sicherheit bei Verwendung von Dienstremoting verbessern können.

Wir verwenden ein vorhandenes [Beispiel](service-fabric-reliable-services-communication-wcf.md), um zu erläutern, wie der WCF-basierte Kommunikationsstapel für Reliable Services eingerichtet wird. Um die Sicherung eines Diensts zu unterstützen, wenn Sie einen WCF-basierten Kommunikationsstapel verwenden, befolgen Sie diese Schritte:

1. Für den Dienst müssen Sie die Sicherung des WCF-Kommunikationslisteners (`WcfCommunicationListener`) unterstützen, den Sie erstellen. Ändern Sie dazu die `CreateServiceReplicaListeners` -Methode.

    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]
        {
            new ServiceReplicaListener(
                this.CreateWcfCommunicationListener)
        };
    }

    private WcfCommunicationListener<ICalculator> CreateWcfCommunicationListener(StatefulServiceContext context)
    {
       var wcfCommunicationListener = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example, we will be using NetTcpBinding.
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");

        // Add certificate details in the ServiceHost credentials.
        wcfCommunicationListener.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListener;
    }

    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```
2. Auf dem Client bleibt die `WcfCommunicationClient` -Klasse, die wir im vorherigen [Beispiel](service-fabric-reliable-services-communication-wcf.md) erstellt haben, unverändert. Sie müssen aber die `CreateClientAsync`-Methode von `WcfCommunicationClientFactory` überschreiben:

    ```csharp
    public class SecureWcfCommunicationClientFactory<TServiceContract> : WcfCommunicationClientFactory<TServiceContract> where TServiceContract : class
    {
        private readonly Binding clientBinding;
        private readonly object callbackObject;
        public SecureWcfCommunicationClientFactory(
            Binding clientBinding,
            IEnumerable<IExceptionHandler> exceptionHandlers = null,
            IServicePartitionResolver servicePartitionResolver = null,
            string traceId = null,
            object callback = null)
            : base(clientBinding, exceptionHandlers, servicePartitionResolver,traceId,callback)
        {
            this.clientBinding = clientBinding;
            this.callbackObject = callback;
        }

        protected override Task<WcfCommunicationClient<TServiceContract>> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
        {
            var endpointAddress = new EndpointAddress(new Uri(endpoint));
            ChannelFactory<TServiceContract> channelFactory;
            if (this.callbackObject != null)
            {
                channelFactory = new DuplexChannelFactory<TServiceContract>(
                this.callbackObject,
                this.clientBinding,
                endpointAddress);
            }
            else
            {
                channelFactory = new ChannelFactory<TServiceContract>(this.clientBinding, endpointAddress);
            }
            // Add certificate details to the ChannelFactory credentials.
            // These credentials will be used by the clients created by
            // SecureWcfCommunicationClientFactory.  
            channelFactory.Credentials.ClientCertificate.SetCertificate(
                StoreLocation.LocalMachine,
                StoreName.My,
                X509FindType.FindByThumbprint,
                "9DC906B169DC4FAFFD1697AC781E806790749D2F");
            var channel = channelFactory.CreateChannel();
            var clientChannel = ((IClientChannel)channel);
            clientChannel.OperationTimeout = this.clientBinding.ReceiveTimeout;
            return Task.FromResult(this.CreateWcfCommunicationClient(channel));
        }
    }
    ```

    Verwenden Sie `SecureWcfCommunicationClientFactory`, um einen WCF-Kommunikationsclient (`WcfCommunicationClient`) zu erstellen. Verwenden Sie den Client, um Dienstmethoden aufzurufen.

    ```csharp
    IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();

    var wcfClientFactory = new SecureWcfCommunicationClientFactory<ICalculator>(clientBinding: GetNetTcpBinding(), servicePartitionResolver: partitionResolver);

    var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
        wcfClientFactory,
        ServiceUri,
        ServicePartitionKey.Singleton);

    var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
        client => client.Channel.Add(2, 3)).Result;
    ```

Lesen Sie als Nächstes den Artikel [Web-API mit OWIN in Reliable Services](service-fabric-reliable-services-communication-webapi.md).

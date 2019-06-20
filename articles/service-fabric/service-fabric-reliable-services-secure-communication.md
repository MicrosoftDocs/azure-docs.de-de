---
title: Schützen der Dienstremotingkommunikation mit C# in Azure Service Fabric | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie die dienstremotingbasierte Kommunikation für in C# geschriebene Reliable Services schützen, die in einem Azure Service Fabric-Cluster ausgeführt werden.
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
ms.openlocfilehash: f247142f26490e1899256917b64fbec7308fb281
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62107669"
---
# <a name="secure-service-remoting-communications-in-a-c-service"></a>Schützen der Dienstremotingkommunikation in einem C#-Dienst
> [!div class="op_single_selector"]
> * [C# unter Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java unter Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

Sicherheit ist einer der wichtigsten Aspekte der Kommunikation. Das Reliable Services-Anwendungsframework stellt einige fertige Kommunikationsstapel und Tools bereit, die Sie verwenden können, um die Sicherheit zu verbessern. In diesem Artikel erfahren Sie, wie Sie die Sicherheit bei Verwendung von Dienstremoting in einem C#-Dienst verbessern können. Der Artikel erläutert anhand eines [Beispiels](service-fabric-reliable-services-communication-remoting.md), wie das Remoting für in C# geschriebene Reliable Services eingerichtet wird. 

Gehen Sie wie folgt vor, um zum Schutz eines Diensts beizutragen, wenn Sie Dienstremoting mit C#-Diensten verwenden:

1. Erstellen Sie eine Schnittstelle, `IHelloWorldStateful`, die definiert, welche Methoden für einen Remoteprozeduraufruf Ihres Diensts verfügbar sind. Ihr Dienst verwendet `FabricTransportServiceRemotingListener`, der im Namespace `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` deklariert wird. Dies ist eine `ICommunicationListener` -Implementierung, die Remotingfunktionen bereitstellt.

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```
2. Fügen Sie Listenereinstellungen und Sicherheitsanmeldeinformationen hinzu.

    Stellen Sie sicher, dass das Zertifikat, das Sie zum Schutz Ihrer Dienstkommunikation verwenden möchten, auf allen Knoten im Cluster installiert ist. 
    
    > [!NOTE]
    > Auf Linux-Knoten muss das Zertifikat in Form von Dateien im PEM-Format im Verzeichnis */var/lib/sfcerts* vorgelegt werden. Weitere Informationen finden Sie unter [Speicherort und Format von X.509-Zertifikaten auf Linux-Knoten](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes). 

    Es gibt zwei Möglichkeiten, wie Sie Listenereinstellungen und Sicherheitsanmeldeinformationen bereitstellen können:

   1. Direkte Bereitstellung im Dienstcode:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           FabricTransportRemotingListenerSettings  listenerSettings = new FabricTransportRemotingListenerSettings
           {
               MaxMessageSize = 10000000,
               SecurityCredentials = GetSecurityCredentials()
           };
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(context,this,listenerSettings))
           };
       }

       private static SecurityCredentials GetSecurityCredentials()
       {
           // Provide certificate details.
           var x509Credentials = new X509Credentials
           {
               FindType = X509FindType.FindByThumbprint,
               FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
               StoreLocation = StoreLocation.LocalMachine,
               StoreName = "My",
               ProtectionLevel = ProtectionLevel.EncryptAndSign
           };
           x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
           x509Credentials.RemoteCertThumbprints.Add("9FEF3950642138446CC364A396E1E881DB76B483");
           return x509Credentials;
       }
       ```
   2. Bereitstellung mithilfe eines [Konfigurationspakets](service-fabric-application-and-service-manifests.md):

       Fügen Sie in der Datei „settings.xml“ einen Abschnitt namens `TransportSettings` hinzu.

       ```xml
       <Section Name="HelloWorldStatefulTransportSettings">
           <Parameter Name="MaxMessageSize" Value="10000000" />
           <Parameter Name="SecurityCredentialsType" Value="X509" />
           <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
           <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
           <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
           <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
           <Parameter Name="CertificateStoreName" Value="My" />
           <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
           <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
       </Section>
       ```

       In diesem Fall sieht die `CreateServiceReplicaListeners` -Methode wie folgt aus:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(
                       context,this,FabricTransportRemotingListenerSettings .LoadFrom("HelloWorldStatefulTransportSettings")))
           };
       }
       ```

        Wenn Sie in der Datei „settings.xml“ den Abschnitt `TransportSettings` hinzufügen, lädt `FabricTransportRemotingListenerSettings` standardmäßig alle Einstellungen aus diesem Abschnitt.

        ```xml
        <!--"TransportSettings" section .-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        In diesem Fall sieht die `CreateServiceReplicaListeners` -Methode wie folgt aus:

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                return new[]{
                        new ServiceReplicaListener(
                            (context) => new FabricTransportServiceRemotingListener(context,this))};
            };
        }
        ```
3. Wenn Sie Methoden für einen gesicherten Dienst mit dem Remotingstapel aufrufen, statt mit der `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy`-Klasse einen Dienstproxy zu erstellen, verwenden Sie `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`. Übergeben Sie `FabricTransportRemotingSettings`, worin `SecurityCredentials` enthalten ist.

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "9FEF3950642138446CC364A396E1E881DB76B483",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
    x509Credentials.RemoteCertThumbprints.Add("4FEF3950642138446CC364A396E1E881DB76B48C");

    FabricTransportRemotingSettings transportSettings = new FabricTransportRemotingSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Wenn der Clientcode als Teil eines Diensts ausgeführt wird, können Sie das `FabricTransportRemotingSettings` -Element aus der Datei „settings.xml“ laden. Erstellen Sie den Abschnitt „HelloWorldClientTransportSettings“, der dem obigen Dienstcode ähnelt. Nehmen Sie die folgenden Änderungen am Clientcode vor:

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Wenn der Client nicht als Teil eines Diensts ausgeführt wird, können Sie die Datei „client_name.settings.xml“ an dem gleichen Speicherort erstellen, an dem sich auch die Datei „client_name.exe“ befindet. Erstellen Sie dann einen Abschnitt „TransportSettings“ in dieser Datei.

    Ähnlich wie beim Dienst gilt Folgendes: Wenn Sie in der Datei „client settings.xml/client_name.settings.xml“ den Abschnitt `TransportSettings` hinzufügen, lädt `FabricTransportRemotingSettings` standardmäßig alle Einstellungen aus diesem Abschnitt.

    In diesem Fall wird der frühere Code noch weiter vereinfacht:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```


Lesen Sie als Nächstes den Artikel [Web-API mit OWIN in Reliable Services](service-fabric-reliable-services-communication-webapi.md).

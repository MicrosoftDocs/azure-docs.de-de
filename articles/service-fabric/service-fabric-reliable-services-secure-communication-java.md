---
title: Schützen der Dienstremotingkommunikation mit Java in Azure Service Fabric | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie die dienstremotingbasierte Kommunikation für Reliable Services in Java schützen, die in einem Azure Service Fabric-Cluster ausgeführt werden.
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
ms.openlocfilehash: b465ab602a14285f8cf40b24ce1dfa9c763fecb8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60773348"
---
# <a name="secure-service-remoting-communications-in-a-java-service"></a>Schützen der Dienstremotingkommunikation in einem Java-Dienst
> [!div class="op_single_selector"]
> * [C# unter Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java unter Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

Sicherheit ist einer der wichtigsten Aspekte der Kommunikation. Das Reliable Services-Anwendungsframework stellt einige fertige Kommunikationsstapel und Tools bereit, die Sie verwenden können, um die Sicherheit zu verbessern. In diesem Artikel erfahren Sie, wie Sie die Sicherheit bei Verwendung des Dienstremotings in einem Java-Dienst verbessern können. Der Artikel erläutert anhand eines [Beispiels](service-fabric-reliable-services-communication-remoting-java.md), wie das Remoting für in Java geschriebene Reliable Services eingerichtet wird. 

Führen Sie die folgenden Schritte aus, um einen Dienst zu schützen, wenn Sie das Dienstremoting mit Java-Diensten verwenden:

1. Erstellen Sie eine Schnittstelle, `HelloWorldStateless`, die definiert, welche Methoden für einen Remoteprozeduraufruf Ihres Diensts verfügbar sind. Ihr Dienst verwendet `FabricTransportServiceRemotingListener`, der im Paket `microsoft.serviceFabric.services.remoting.fabricTransport.runtime` deklariert wird. Dies ist eine `CommunicationListener` -Implementierung, die Remotingfunktionen bereitstellt.

    ```java
    public interface HelloWorldStateless extends Service {
        CompletableFuture<String> getHelloWorld();
    }

    class HelloWorldStatelessImpl extends StatelessService implements HelloWorldStateless {
        @Override
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
        return listeners;
        }

        public CompletableFuture<String> getHelloWorld() {
            return CompletableFuture.completedFuture("Hello World!");
        }
    }
    ```
2. Fügen Sie Listenereinstellungen und Sicherheitsanmeldeinformationen hinzu.

    Stellen Sie sicher, dass das Zertifikat, das Sie zum Schutz Ihrer Dienstkommunikation verwenden möchten, auf allen Knoten im Cluster installiert ist. Bei unter Linux ausgeführten Diensten muss das Zertifikat als Datei im PEM-Format verfügbar sein: Entweder muss eine `.pem`-Datei vorhanden sein, die das Zertifikat und den privaten Schlüssel enthält, oder eine `.crt`-Datei mit dem Zertifikat und eine `.key`-Datei mit dem privaten Schlüssel. Weitere Informationen finden Sie unter [Speicherort und Format von X.509-Zertifikaten auf Linux-Knoten](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes).
    
    Es gibt zwei Möglichkeiten, wie Sie Listenereinstellungen und Sicherheitsanmeldeinformationen bereitstellen können:

   1. Bereitstellung mithilfe eines [Konfigurationspakets](service-fabric-application-and-service-manifests.md):

       Fügen Sie in der Datei „settings.xml“ einen Abschnitt namens `TransportSettings` hinzu.

       ```xml
       <!--Section name should always end with "TransportSettings".-->
       <!--Here we are using a prefix "HelloWorldStateless".-->
        <Section Name="HelloWorldStatelessTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509_2" />
            <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
            <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
        </Section>

       ```

       In diesem Fall sieht die `createServiceInstanceListeners` -Methode wie folgt aus:

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        Wenn Sie in der Datei „settings.xml“ den Abschnitt `TransportSettings` ohne Präfix hinzufügen, lädt `FabricTransportListenerSettings` standardmäßig alle Einstellungen aus diesem Abschnitt.

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        In diesem Fall sieht die `CreateServiceInstanceListeners` -Methode wie folgt aus:

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. Wenn Sie Methoden für einen gesicherten Dienst mit dem Remotingstapel aufrufen, statt mit der `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase`-Klasse einen Dienstproxy zu erstellen, verwenden Sie `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`.

    Wenn der Clientcode als Teil eines Diensts ausgeführt wird, können Sie das `FabricTransportSettings` -Element aus der Datei „settings.xml“ laden. Erstellen Sie einen TransportSettings-Abschnitt, der dem obigen Dienstcode ähnelt. Nehmen Sie die folgenden Änderungen am Clientcode vor:

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```

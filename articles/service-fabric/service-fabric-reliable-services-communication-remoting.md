---
title: Dienstremoting mit C# in Service Fabric
description: Service Fabric-Remoting ermöglicht Clients und Diensten die Kommunikation mit C#-Diensten über einen Remoteprozeduraufruf.
ms.topic: conceptual
ms.date: 09/20/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: c3659fea73abae3c9c5264f227b90d0af95a93e7
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576654"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Dienstremoting in C# mit Reliable Services

> [!div class="op_single_selector"]
> * [C# unter Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java unter Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Für Web-APIs, Windows Communication Foundation und andere Dienste, die nicht an ein bestimmtes Kommunikationsprotokoll oder einen bestimmten Kommunikationsstapel gebunden sind, stellt das Reliable Services-Framework einen Remotingmechanismus für das schnelle und einfache Einrichten von Remoteprozeduraufrufen für Dienste bereit. Dieser Artikel beschreibt das Einrichten von Remoteprozeduraufrufen für Dienste, die in C# geschrieben wurden.

## <a name="set-up-remoting-on-a-service"></a>Einrichten von Remoting für einen Dienst

Sie können das Remoting für einen Dienst in zwei einfachen Schritten einrichten:

1. Erstellen Sie eine Schnittstelle, die vom Dienst implementiert werden soll. Diese Schnittstelle definiert die Methoden, die für den Remoteprozeduraufruf für Ihren Dienst verfügbar sind. Bei den Methoden muss es sich um asynchrone Methoden handeln, die einen Task zurückgeben. Die Schnittstelle muss `Microsoft.ServiceFabric.Services.Remoting.IService` implementieren, um zu signalisieren, dass der Dienst über eine Remotingschnittstelle verfügt.
2. Verwenden Sie einen Remoting-Listener in Ihrem Dienst. Ein Remotinglistener ist eine `ICommunicationListener`-Implementierung, die Remotingfunktionen bereitstellt. Der Namespace `Microsoft.ServiceFabric.Services.Remoting.Runtime` enthält die `CreateServiceRemotingInstanceListeners`-Erweiterungsmethode für zustandslose und zustandsbehaftete Dienste, die zum Erstellen eines Remotinglisteners mit dem standardmäßigen Remotingtransportprotokoll verwendet werden kann.

>[!NOTE]
>Der Namespace `Remoting` steht als separates NuGet-Paket mit dem Namen `Microsoft.ServiceFabric.Services.Remoting` zur Verfügung.

Der folgende zustandslose Dienst macht beispielsweise eine einzelne Methode verfügbar, um „Hello World“ per Remoteprozeduraufruf abzurufen:

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task<string> HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
     return this.CreateServiceRemotingInstanceListeners();
    }
}
```

> [!NOTE]
> Bei den Argumenten und Rückgabetypen in der Dienstschnittstelle kann es sich um einfache, komplexe oder benutzerdefinierte Typen handeln. Sie müssen jedoch durch [DataContractSerializer](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer) von .NET serialisierbar sein.
>
>

## <a name="call-remote-service-methods"></a>Aufrufen von Remotedienstmethoden

Methoden für einen Dienst, der den Remotingstapel nutzt, werden mithilfe eines lokalen Proxys für den Dienst über die `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` -Klasse abgerufen. Die `ServiceProxy` -Methode erstellt einen lokalen Proxy unter Verwendung der gleichen Schnittstelle, die auch der Dienst implementiert. Mit diesem Proxy können Sie Methoden für die Schnittstelle remote aufrufen.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

Das Remotingframework gibt vom Dienst ausgelöste Ausnahmen an den Client weiter. Folglich ist der Client bei der Verwendung von `ServiceProxy` für die Behandlung der vom Dienst ausgelösten Ausnahmen verantwortlich.

## <a name="service-proxy-lifetime"></a>Lebensdauer von Dienstproxys

Die Erstellung von Dienstproxys ist ein einfacher Vorgang, der sehr wenige Ressourcen verbraucht, sodass Sie so viele erstellen können, wie Sie benötigen. Dienstproxyinstanzen können beliebig lange wiederverwendet werden. Wenn ein Remoteprozeduraufruf eine Ausnahme auslöst, können Sie weiterhin dieselbe Proxyinstanz wiederverwenden. Jeder Dienstproxy enthält einen Kommunikationsclient zum Senden von Nachrichten im Netzwerk. Beim Aufruf von Remoteaufrufen wird intern geprüft, ob der Kommunikationsclient gültig ist. Basierend auf den Ergebnissen dieser Überprüfungen wird der Kommunikationsclient bei Bedarf erneut erstellt. Darum müssen Sie im Fall einer Ausnahme `ServiceProxy` nicht neu erstellen.

### <a name="service-proxy-factory-lifetime"></a>Lebensdauer von Dienstproxyfactorys

[ServiceProxyFactory](/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) ist eine Factory, die Proxyinstanzen für verschiedene Remotingschnittstellen erstellt. Wenn Sie die `ServiceProxyFactory.CreateServiceProxy`-API zum Erstellen eines Proxys verwenden, erstellt das Framework einen Singletondienstproxy.
Es ist sinnvoll, eine manuell zu erstellen, wenn Sie [IServiceRemotingClientFactory](/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory)-Eigenschaften überschreiben müssen.
Die Factoryerstellung ist ein kostenintensiver Vorgang. Dienstproxyfactorys verwalten einen internen Cache des Kommunikationsclients.
Es empfiehlt sich, die Dienstproxyfactory möglichst lange zwischenzuspeichern.

## <a name="remoting-exception-handling"></a>Behandlung von Remotingausnahmen

Alle von der Dienst-API ausgelösten Remoteausnahmen werden als „AggregateException“ an den Client zurückgesendet. Remoteausnahmen sollten von DataContract serialisiert werden können. Ist das nicht der Fall, löst die Proxy-API [ServiceException](/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) mit dem enthaltenem Serialisierungsfehler aus.

Der Dienstproxy verarbeitet sämtliche Failoverausnahmen für die Dienstpartition, für die er erstellt wurde. Dieser Proxy löst die Endpunkte erneut auf, falls Failoverausnahmen (nicht vorübergehende Ausnahmen) vorliegen, und wiederholt den Aufruf mit dem richtigen Endpunkt. Die Anzahl der Wiederholungen bei Failoverausnahmen ist unbegrenzt.
Wenn vorübergehende Ausnahmen auftreten, versucht der Proxy erneut, den Aufruf auszuführen.

Standardparameter für die Wiederholung werden von [OperationRetrySettings](/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings) angegeben.

Ein Benutzer kann diese Werte konfigurieren, indem er das OperationRetrySettings-Objekt an den ServiceProxyFactory-Konstruktor übergibt.

## <a name="use-the-remoting-v2-stack"></a>Verwenden des Remoting V2-Stapels

Ab Version 2.8 des Remoting-NuGet-Pakets haben Sie die Möglichkeit, den Remoting V2-Stapel zu verwenden. Der V2-Remotingstapel bietet eine höhere Leistung. Außerdem weist er Features wie benutzerdefinierte Serialisierung und besser erweiterbare APIs auf.
Im Vorlagencode wird weiterhin der Remoting V1-Stapel verwendet.
Remoting V2 ist nicht mit V1 (dem vorherigen Remotingstapel) kompatibel. Befolgen Sie die Anweisungen im Artikel [Aktualisieren von V1 auf V2](#upgrade-from-remoting-v1-to-remoting-v2), um Auswirkungen auf die Dienstverfügbarkeit zu vermeiden.

Zum Aktivieren des V2-Stapels stehen folgende Methoden zur Verfügung.

### <a name="use-an-assembly-attribute-to-use-the-v2-stack"></a>Verwenden eines assembly-Attributs zum Einsetzen des V2-Stapels

Diese Schritte ändern den Vorlagencode so, dass der V2-Stapel mit einem assembly-Attribut verwendet wird.

1. Ändern Sie die Endpunktressource im Dienstmanifest von `"ServiceEndpoint"` in `"ServiceEndpointV2"`.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Verwenden Sie die Erweiterungsmethode `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners`, um Remotinglistener zu erstellen (diese sind für V1 und V2 gleich).

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Markieren Sie die Assembly, die die Remotingschnittstellen enthält, mit einem `FabricTransportServiceRemotingProvider`-Attribut.

   ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
   ```

Im Clientprojekt sind keine Codeänderungen erforderlich.
Erstellen Sie die Clientassembly mit der Schnittstellenassembly, um sicherzustellen, dass das oben gezeigte assembly-Attribut verwendet wird.

### <a name="use-explicit-v2-classes-to-use-the-v2-stack"></a>Verwenden von expliziten V2-Klassen zum Einsetzen des V2-Stapels

Alternativ zur Verwendung eines assembly-Attributs kann der V2-Stapel auch mithilfe von expliziten V2-Klassen aktiviert werden.

Diese Schritte ändern den Vorlagencode so, dass der V2-Stapel mit expliziten V2-Klassen verwendet wird.

1. Ändern Sie die Endpunktressource im Dienstmanifest von `"ServiceEndpoint"` in `"ServiceEndpointV2"`.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Verwenden Sie [FabricTransportServiceRemotingListener](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet) aus dem Namespace `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime`.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                return new FabricTransportServiceRemotingListener(c, this);

            })
        };
    }
   ```

3. Verwenden Sie [FabricTransportServiceRemotingClientFactory](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) aus dem Namespace `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client`, um Clients zu erstellen.

   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2"></a>So aktualisieren Sie von Remoting V1 auf Remoting V2

Zum Aktualisieren von V1 auf V2 sind 2 Aktualisierungsschritte erforderlich. Führen Sie die Schritte in dieser Reihenfolge aus.

1. Führen Sie ein Upgrade des V1-Diensts auf den V2-Dienst durch, indem Sie das nachfolgende Attribut verwenden.
Mit dieser Änderung wird sichergestellt, dass der Dienst am V1- und am V2-Listener lauscht.

    a. Fügen Sie im Dienstmanifest eine Endpunktressource namens „ServiceEndpointV2“ hinzu.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b. Verwenden Sie die folgende Erweiterungsmethode, um einen Remotinglistener zu erstellen.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Fügen Sie Remotingschnittstellen ein assembly-Attribut hinzu, damit der V1- und V2-Listener und der V2-Client verwendet werden.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. Aktualisieren Sie den V1-Client auf den V2-Client, indem Sie das Attribut für den V2-Client verwenden.
Durch diesen Schritt wird sichergestellt, dass der Client den V2-Stapel verwendet.
Im Clientprojekt/-dienst ist keine Änderung erforderlich. Es genügt, Clientprojekte mit der aktualisierten Schnittstellenassembly zu erstellen.

3. Dieser Schritt ist optional. Verwenden Sie das V2-Listenerattribut, und aktualisieren Sie dann den V2-Dienst.
Mit diesem Schritt wird sichergestellt, dass der Dienst nur am V2-Listener lauscht.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
    ```


## <a name="use-the-remoting-v2-interface-compatible-stack"></a>Verwenden Sie den Remoting V2-Stapel (schnittstellenkompatibel).

 Der Remoting V2-Stapel (schnittstellenkompatibel, auch als „V2_1“ bezeichnet) verfügt über alle Features des Remoting V2-Stapels. Der Schnittstellenstapel ist mit dem Remoting V1-Stapel kompatibel, jedoch nicht abwärtskompatibel mit V2 und V1. Um ohne Beeinträchtigung der Dienstverfügbarkeit ein Upgrade von V1 auf V2_1 durchzuführen, führen Sie die Schritte im Artikel „Durchführen eines Upgrades von V1 auf V2 (schnittstellenkompatibel)“ durch.


### <a name="use-an-assembly-attribute-to-use-the-remoting-v2-interface-compatible-stack"></a>Verwenden eines assembly-Attributs zum Einsetzen des Remoting V2-Stapels (schnittstellenkompatibel)

Gehen Sie folgendermaßen vor, um zu einem V2_1-Stapel zu wechseln.

1. Fügen Sie im Dienstmanifest eine Endpunktressource namens „ServiceEndpointV2_1“ hinzu.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Verwenden Sie die Remotingerweiterungsmethode, um einen Remotinglistener zu erstellen.

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Fügen Sie Remotingschnittstellen ein [assembly-Attribut](/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet) hinzu.

   ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

   ```

Im Clientprojekt sind keine Änderungen erforderlich.
Erstellen Sie die Clientassembly mit der Schnittstellenassembly, um sicherzustellen, dass das zuvor genannte assembly-Attribut verwendet wird.

### <a name="use-explicit-remoting-classes-to-create-a-listenerclient-factory-for-the-v2-interface-compatible-version"></a>Verwenden von expliziten Remotingklassen zum Erstellen einer Listener-/Clientfactory für die V2-Version (schnittstellenkompatibel)

Folgen Sie diesen Schritten:

1. Fügen Sie im Dienstmanifest eine Endpunktressource namens „ServiceEndpointV2_1“ hinzu.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Verwenden Sie den [Remoting V2-Listener](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet). Der Standardname für die Dienstendpunktressource lautet „ServiceEndpointV2_1“. Er muss im Dienstmanifest definiert werden.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                var settings = new FabricTransportRemotingListenerSettings();
                settings.UseWrappedMessage = true;
                return new FabricTransportServiceRemotingListener(c, this,settings);

            })
        };
    }
   ```

3. Verwenden Sie die V2-[Clientfactory](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet).
   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2-interface-compatible"></a>Durchführen eines Upgrades von Remoting V1 auf Remoting V2 (schnittstellenkompatibel)

Zum Durchführen eines Upgrades von V1 auf V2 (schnittstellenkompatibel, auch als „V2_1“ bezeichnet) sind zwei Upgradeschritte erforderlich. Führen Sie die Schritte in dieser Reihenfolge aus.

> [!NOTE]
> Wenn Sie von V1 auf V2 aktualisieren, müssen Sie den `Remoting`-Namespace so aktualisieren, dass für ihn V2 verwendet wird. Beispiel: 'Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client`
>
>

1. Aktualisieren Sie den V1-Dienst auf den V2_1-Dienst, indem Sie das folgende Attribut verwenden.
Mit dieser Änderung wird sichergestellt, dass der Dienst am V1- und V2_1-Listener lauscht.

    a. Fügen Sie im Dienstmanifest eine Endpunktressource namens „ServiceEndpointV2_1“ hinzu.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    b. Verwenden Sie die folgende Erweiterungsmethode, um einen Remotinglistener zu erstellen.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Fügen Sie Remotingschnittstellen ein assembly-Attribut hinzu, damit der V1- und V2_1-Listener und der V2_1-Client verwendet werden.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. Führen Sie ein Upgrade des V1-Clients auf den V2_1-Client durch, indem Sie das Attribut für den V2_1-Client verwenden.
Mit diesem Schritt wird sichergestellt, dass der Client den V2_1-Stapel verwendet.
Im Clientprojekt/-dienst ist keine Änderung erforderlich. Es genügt, Clientprojekte mit der aktualisierten Schnittstellenassembly zu erstellen.

3. Dieser Schritt ist optional. Entfernen Sie den V1-Listener aus dem Attribut, und aktualisieren Sie dann den V2-Dienst.
Mit diesem Schritt wird sichergestellt, dass der Dienst nur am V2-Listener lauscht.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```
  
### <a name="use-custom-serialization-with-a-remoting-wrapped-message"></a>Verwenden der benutzerdefinierten Serialisierung mit umschlossener Remotingnachricht

In umschlossenen Remotingnachrichten wird ein einzelnes umschlossenes Objekt mit allen Parametern als enthaltenem Feld erstellt.
Folgen Sie diesen Schritten:

1. Implementieren Sie die `IServiceRemotingMessageSerializationProvider`-Schnittstelle, um die Implementierung für benutzerdefinierte Serialisierung bereitzustellen.
    Dieser Codeausschnitt veranschaulicht, wie die Implementierung aussieht.

      ```csharp
      public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
      {
        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
          return new JsonMessageFactory();
        }

        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> requestWrappedType, IEnumerable<Type> requestBodyTypes = null)
        {
          return new ServiceRemotingRequestJsonMessageBodySerializer();
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> responseWrappedType, IEnumerable<Type> responseBodyTypes = null)
        {
          return new ServiceRemotingResponseJsonMessageBodySerializer();
        }
      }
      ```
      ```csharp
        class JsonMessageFactory : IServiceRemotingMessageBodyFactory
            {

              public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName, int numberOfParameters, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }

              public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }
            }
      ```
      ```csharp
      class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
        {
            private JsonSerializer serializer;

            public ServiceRemotingRequestJsonMessageBodySerializer()
            {
              serializer = JsonSerializer.Create(new JsonSerializerSettings()
              {
                TypeNameHandling = TypeNameHandling.All
                });
              }

              public IOutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
             {
               if (serviceRemotingRequestMessageBody == null)
               {
                 return null;
               }          
               using (var writeStream = new MemoryStream())
               {
                 using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
                 {
                   serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);
                   jsonWriter.Flush();
                   var bytes = writeStream.ToArray();
                   var segment = new ArraySegment<byte>(bytes);
                   var segments = new List<ArraySegment<byte>> { segment };
                   return new OutgoingMessageBody(segments);
                 }
               }
              }

              public IServiceRemotingRequestMessageBody Deserialize(IIncomingMessageBody messageBody)
             {
               using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
               {
                 using (JsonReader reader = new JsonTextReader(sr))
                 {
                   var ob = serializer.Deserialize<JsonBody>(reader);
                   return ob;
                 }
               }
             }
            }
      ```
      ```csharp
      class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
       {
         private JsonSerializer serializer;

        public ServiceRemotingResponseJsonMessageBodySerializer()
        {
          serializer = JsonSerializer.Create(new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
            });
          }

          public IOutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
          {
            if (responseMessageBody == null)
            {
              return null;
            }

            using (var writeStream = new MemoryStream())
            {
              using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
              {
                serializer.Serialize(jsonWriter, responseMessageBody);
                jsonWriter.Flush();
                var bytes = writeStream.ToArray();
                var segment = new ArraySegment<byte>(bytes);
                var segments = new List<ArraySegment<byte>> { segment };
                return new OutgoingMessageBody(segments);
              }
            }
          }

          public IServiceRemotingResponseMessageBody Deserialize(IIncomingMessageBody messageBody)
          {

             using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
             {
               using (var reader = new JsonTextReader(sr))
               {
                 var obj = serializer.Deserialize<JsonBody>(reader);
                 return obj;
               }
             }
           }
       }
    ```
    ```csharp
    class JsonBody : WrappedMessage, IServiceRemotingRequestMessageBody, IServiceRemotingResponseMessageBody
    {
          public JsonBody(object wrapped)
          {
            this.Value = wrapped;
          }

          public void SetParameter(int position, string parameName, object parameter)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object GetParameter(int position, string parameName, Type paramType)
          {
            //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public void Set(object response)
          { //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object Get(Type paramType)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }
    }
    ```

2. Überschreiben Sie den Standardserialisierungsanbieter für einen Remotinglistener mit `JsonSerializationProvider`.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
   {
       return new[]
       {
           new ServiceInstanceListener((c) =>
           {
               return new FabricTransportServiceRemotingListener(c, this,
                   new ServiceRemotingJsonSerializationProvider());
           })
       };
   }
   ```

3. Überschreiben Sie den Standardserialisierungsanbieter für eine Remotingclientfactory mit `JsonSerializationProvider`.

    ```csharp
    var proxyFactory = new ServiceProxyFactory((c) =>
    {
        return new FabricTransportServiceRemotingClientFactory(
        serializationProvider: new ServiceRemotingJsonSerializationProvider());
      });
      ```

## <a name="next-steps"></a>Nächste Schritte

* [Web-API mit OWIN in Reliable Services](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Windows Communication Foundation-Kommunikation mit Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Sichere Kommunikation für Reliable Services](service-fabric-reliable-services-secure-communication.md)

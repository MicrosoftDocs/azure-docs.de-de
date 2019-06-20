---
title: Kommunikation für Rollen in Clouddiensten | Microsoft-Dokumentation
description: Rolleninstanzen in Clouddiensten können Endgeräte (HTTP, HTTPS, TCP und UDP) besitzen, die mit der Außenseite oder anderen Rolleninstanzen kommunizieren.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 7008a083-acbe-4fb8-ae60-b837ef971ca1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: jeconnoc
ms.openlocfilehash: 4fa3885f9c273cf6aaf9173ebd3fee3d4499be34
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808101"
---
# <a name="enable-communication-for-role-instances-in-azure"></a>Ermöglichen der Kommunikation für Rolleninstanzen in Azure
Clouddienstrollen kommunizieren über interne und externe Verbindungen miteinander. Externe Verbindungen werden als **Eingabeendpunkte** bezeichnet, interne Verbindungen dagegen als **interne Endpunkte**. In diesem Thema wird beschrieben, wie Sie die [Dienstdefinition](cloud-services-model-and-package.md#csdef) zum Erstellen von Endgeräten ändern.

## <a name="input-endpoint"></a>Eingabeendpunkt
Eingabeendpunkte werden verwendet, wenn Sie einen Port für externe Verbindungen verfügbar machen möchten. Sie geben den Protokolltyp und den Port des Endpunkts an, der dann sowohl für die externen als auch die internen Ports für den Endpunkt gilt. Wenn Sie möchten, können Sie mit dem [localPort](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) -Attribut einen anderen internen Port für den Endpunkt angeben.

Der Eingabeendpunkt kann die folgenden Protokolle verwenden: **http, https, tcp, udp**.

Um einen Eingabeendpunkt zu erstellen, fügen Sie dem **Endpoints**-Element einer Web- oder Workerrolle das untergeordnete Element **InputEndpoint** hinzu.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Instanz-Eingabeendpunkt
Instanz-Eingabeendpunkte ähneln Eingabeendpunkten, bieten Ihnen aber die Möglichkeit, mithilfe der Portweiterleitung des Load Balancers bestimmte öffentliche Ports für jede einzelne Rolleninstanz zuzuordnen. Sie können einen einzelnen öffentlichen Port oder einen Portbereich angeben.

Der Instanz-Eingabeendpunkt kann nur die Protokolle **tcp** oder **udp** verwenden.

Um einen Instanz-Eingabeendpunkt zu erstellen, fügen Sie dem **Endpoints**-Element einer Web- oder Workerrolle das untergeordnete Element **InstanceInputEndpoint** hinzu.

```xml
<Endpoints>
  <InstanceInputEndpoint name="Endpoint2" protocol="tcp" localPort="10100">
    <AllocatePublicPortFrom>
      <FixedPortRange max="10109" min="10105" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
</Endpoints>
```

## <a name="internal-endpoint"></a>Interner Endpunkt
Interne Endpunkte stehen für die Kommunikation zwischen Instanzen zur Verfügung. Der Port ist bei diesem Endpunkttyp optional. Wird er nicht angegeben, wird dem Endpunkt ein dynamischer Port zugewiesen. Es kann auch ein Portbereich verwendet werden. Pro Rolle sind maximal fünf interne Endpunkte zulässig.

Der interne Endpunkt kann die folgenden Protokolle verwenden: **http, tcp, udp, any**.

Um einen internen Eingabeendpunkt zu erstellen, fügen Sie dem **Endpoints**-Element einer Web- oder Workerrolle das untergeordnete Element **InternalEndpoint** hinzu.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" port="8999" />
</Endpoints> 
```

Sie können auch einen Portbereich verwenden.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any">
    <FixedPortRange max="8999" min="8995" />
  </InternalEndpoint>
</Endpoints>
```


## <a name="worker-roles-vs-web-roles"></a>Workerrollen im Vergleich zu Webrollen
Wenn Sie sowohl Worker- als auch Webrollen verwenden, ist bezüglich der Endpunkte ein kleiner Unterschied zu beachten. Die Webrolle muss mindestens über einen einzigen Eingabeendpunkt mit dem **HTTP** -Protokoll verfügen.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>Verwenden des .NET-SDK zum Zugreifen auf einen Endpunkt
Die verwaltete Azure-Bibliothek enthält Methoden, mit deren Hilfe Rolleninstanzen zur Laufzeit kommunizieren können. Sie können über Code, der in einer Rolleninstanz ausgeführt wird, Informationen zum Vorhandensein anderer Rolleninstanzen und zu ihren Endpunkten sowie Informationen zur aktuellen Rolleninstanz abrufen.

> [!NOTE]
> Es können nur Informationen über Rolleninstanzen abgerufen werden, die im Clouddienst ausgeführt werden und mindestens einen internen Endpunkt definieren. Es ist nicht möglich, Daten über Rolleninstanzen abzurufen, die in einem anderen Dienst ausgeführt werden.
> 
> 

Sie können die [Instances](/previous-versions/azure/reference/ee741904(v=azure.100)) -Eigenschaft verwenden, um Instanzen einer Rolle abzurufen. Verwenden Sie zuerst [CurrentRoleInstance](/previous-versions/azure/reference/ee741907(v=azure.100)), um einen Verweis auf die aktuelle Rolleninstanz zurückzugeben, und anschließend die [Role](/previous-versions/azure/reference/ee741918(v=azure.100))-Eigenschaft, um einen Verweis auf die Rolle selbst zurückzugeben.

Wenn Sie programmgesteuert über das .NET-SDK eine Verbindung mit einer Rolleninstanz herstellen, ist es relativ einfach, auf die Endpunktinformationen zuzugreifen. Nachdem Sie eine Verbindung mit einer bestimmten Rollenumgebung hergestellt haben, können Sie z. B. mit folgendem Code den Port eines bestimmten Endpunkts abrufen:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

Die **Instances**-Eigenschaft gibt eine Sammlung von **RoleInstance**-Objekten zurück. Diese Sammlung enthält immer die aktuelle Instanz. Falls die Rolle keinen internen Endpunkt definiert, enthält die Sammlung die aktuelle Instanz, aber keine anderen Instanzen. Die Anzahl von Rolleninstanzen in der Sammlung ist immer 1, wenn kein interner Endpunkt für die Rolle definiert ist. Wenn die Rolle einen internen Endpunkt definiert, können ihre Instanzen zur Laufzeit ermittelt werden. In diesem Fall entspricht die Anzahl von Instanzen in der Sammlung der Anzahl von Instanzen, die in der Dienstkonfigurationsdatei für die Rolle angegeben ist.

> [!NOTE]
> Die verwaltete Azure-Bibliothek bietet nicht die Möglichkeit, die Integrität anderer Rolleninstanzen zu ermitteln. Sie können aber selbst Integritätsbewertungen implementieren, wenn diese Funktion für Ihren Dienst erforderlich ist. Informationen zu ausgeführten Rolleninstanzen können mithilfe der [Azure-Diagnose](cloud-services-dotnet-diagnostics.md) abgerufen werden.
> 
> 

Um die Portnummer für einen internen Endpunkt einer Rolleninstanz zu bestimmen, können Sie mithilfe der [InstanceEndpoints](/previous-versions/azure/reference/ee741917(v=azure.100)) -Eigenschaft ein Dictionary-Objekt zurückgeben, das Endpunktnamen und die entsprechenden IP-Adressen und Ports enthält. Die [IPEndpoint](/previous-versions/azure/reference/ee741919(v=azure.100)) -Eigenschaft gibt die IP-Adresse und den Port für einen angegebenen Endpunkt zurück. Die **PublicIPEndpoint** -Eigenschaft gibt den Port für einen Endpunkt mit Lastenausgleich zurück. Der IP-Adressteil der **PublicIPEndpoint** -Eigenschaft wird nicht verwendet.

Hier sehen Sie ein Beispiel, das Rolleninstanzen durchläuft.

```csharp
foreach (RoleInstance roleInst in RoleEnvironment.CurrentRoleInstance.Role.Instances)
{
    Trace.WriteLine("Instance ID: " + roleInst.Id);
    foreach (RoleInstanceEndpoint roleInstEndpoint in roleInst.InstanceEndpoints.Values)
    {
        Trace.WriteLine("Instance endpoint IP address and port: " + roleInstEndpoint.IPEndpoint);
    }
}
```

Das folgende Beispiel zeigt eine Workerrolle, die den über die Dienstdefinition verfügbar gemachten Endpunkt abruft und dann auf Verbindungen lauscht.

> [!WARNING]
> Dieser Code funktioniert nur bei einem bereitgestellten Dienst. Bei der Ausführung im Azure-Serveremulator werden Dienstkonfigurationselemente, die direkte Portendpunkte erstellen (**InstanceInputEndpoint** -Elemente) ignoriert.
> 
> 

```csharp
using System;
using System.Diagnostics;
using System.Linq;
using System.Net;
using System.Net.Sockets;
using System.Threading;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.Diagnostics;
using Microsoft.WindowsAzure.ServiceRuntime;
using Microsoft.WindowsAzure.StorageClient;

namespace WorkerRole1
{
  public class WorkerRole : RoleEntryPoint
  {
    public override void Run()
    {
      try
      {
        // Initialize method-wide variables
        var epName = "Endpoint1";
        var roleInstance = RoleEnvironment.CurrentRoleInstance;

        // Identify direct communication port
        var myPublicEp = roleInstance.InstanceEndpoints[epName].PublicIPEndpoint;
        Trace.TraceInformation("IP:{0}, Port:{1}", myPublicEp.Address, myPublicEp.Port);

        // Identify public endpoint
        var myInternalEp = roleInstance.InstanceEndpoints[epName].IPEndpoint;

        // Create socket listener
        var listener = new Socket(
          myInternalEp.AddressFamily, SocketType.Stream, ProtocolType.Tcp);

        // Bind socket listener to internal endpoint and listen
        listener.Bind(myInternalEp);
        listener.Listen(10);
        Trace.TraceInformation("Listening on IP:{0},Port: {1}",
          myInternalEp.Address, myInternalEp.Port);

        while (true)
        {
          // Block the thread and wait for a client request
          Socket handler = listener.Accept();
          Trace.TraceInformation("Client request received.");

          // Define body of socket handler
          var handlerThread = new Thread(
            new ParameterizedThreadStart(h =>
            {
              var socket = h as Socket;
              Trace.TraceInformation("Local:{0} Remote{1}",
                socket.LocalEndPoint, socket.RemoteEndPoint);

              // Shut down and close socket
              socket.Shutdown(SocketShutdown.Both);
              socket.Close();
            }
          ));

          // Start socket handler on new thread
          handlerThread.Start(handler);
        }
      }
      catch (Exception e)
      {
        Trace.TraceError("Caught exception in run. Details: {0}", e);
      }
    }

    public override bool OnStart()
    {
      // Set the maximum number of concurrent connections 
      ServicePointManager.DefaultConnectionLimit = 12;

      // For information on handling configuration changes
      // see the MSDN topic at https://go.microsoft.com/fwlink/?LinkId=166357.
      return base.OnStart();
    }
  }
}
```

## <a name="network-traffic-rules-to-control-role-communication"></a>Netzwerkdatenverkehrsregeln zum Steuern der Kommunikation zwischen Rollen
Nachdem Sie interne Endpunkte definiert haben, können Sie (basierend auf den erstellten Endpunkten) Netzwerkdatenverkehrsregeln hinzufügen, um zu steuern, wie Rolleninstanzen miteinander kommunizieren können. Das folgende Diagramm zeigt einige allgemeine Szenarien zum Steuern der Rollenkommunikation:

![Szenarien für Netzwerk-Datenverkehrsregeln](./media/cloud-services-enable-communication-role-instances/scenarios.png "Szenarien für Netzwerk-Datenverkehrsregeln")

Das folgende Codebeispiel zeigt Rollendefinitionen für die im obigen Diagramm dargestellten Rollen. Jede Rollendefinition enthält mindestens einen definierten internen Endpunkt:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalTCP1" protocol="tcp" />
    </Endpoints>
  </WebRole>
  <WorkerRole name="WorkerRole1">
    <Endpoints>
      <InternalEndpoint name="InternalTCP2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
  <WorkerRole name="WorkerRole2">
    <Endpoints>
      <InternalEndpoint name="InternalTCP3" protocol="tcp" />
      <InternalEndpoint name="InternalTCP4" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

> [!NOTE]
> Bei internen Endpunkten mit festen und automatisch zugewiesenen Ports kann die Kommunikation zwischen Rollen eingeschränkt sein.
> 
> 

Standardmäßig ist der Kommunikationsfluss von beliebigen Rollen zum internen Endpunkt einer Rolle ohne Einschränkungen möglich, nachdem ein interner Endpunkt definiert wurde. Wenn Sie die Kommunikation einschränken möchten, fügen Sie dem **ServiceDefinition**-Element in der Dienstdefinitionsdatei ein **NetworkTrafficRules**-Element hinzu.

### <a name="scenario-1"></a>Szenario 1
Dieses Beispiel lässt nur Netzwerkdatenverkehr von **WebRole1** zu **WorkerRole1** zu.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-2"></a>Szenario 2:
Dieses Beispiel lässt nur Netzwerkdatenverkehr von **WebRole1** zu **WorkerRole1** und **WorkerRole2** zu.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-3"></a>Szenario 3
Dieses Beispiel lässt nur Netzwerkdatenverkehr von **WebRole1** zu **WorkerRole1** und von **WorkerRole1** zu **WorkerRole2** zu.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-4"></a>Szenario 4
Dieses Beispiel lässt nur Netzwerkdatenverkehr von **WebRole1** zu **WorkerRole1**, von **WebRole1** zu **WorkerRole2** und von **WorkerRole1** zu **WorkerRole2** zu.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP4" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

Eine XML-Schemareferenz für die oben verwendeten Elemente finden Sie [hier](/previous-versions/azure/reference/gg557551(v=azure.100)).

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über das [Clouddienstmodell](cloud-services-model-and-package.md).


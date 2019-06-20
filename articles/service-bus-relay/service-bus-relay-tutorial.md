---
title: Verfügbarmachen eines lokalen WCF REST-Diensts für einen externen Client mithilfe von Azure WCF Relay | Microsoft-Dokumentation
description: Erstellen einer Client- und Dienstanwendung mit WCF-Relay.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 53dfd236-97f1-4778-b376-be91aa14b842
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: db73363a05734db5d7e3375a5755a807eb7ce2a5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60790044"
---
# <a name="expose-an-on-premises-wcf-rest-service-to-external-client-by-using-azure-wcf-relay"></a>Verfügbarmachen eines lokalen WCF REST-Diensts für einen externen Client mithilfe von Azure WCF Relay

Dieses Tutorial beschreibt das Erstellen einer einfachen Clientanwendung und eines Diensts für WCF-Relay mithilfe von Azure Relay. Ein ähnliches Tutorial, in dem die [Messagingfunktionen von Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) beschrieben werden, finden Sie unter [Erste Schritte mit Service Bus-Warteschlangen](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

Dieses Tutorial vermittelt Ihnen Grundkenntnisse zu den Schritten, die zum Erstellen einer Client- und Dienstanwendung für WCF-Relay erforderlich sind. Ein Dienst ist wie sein ursprüngliches Gegenstück in WCF ein Konstrukt, das mindestens einen Endpunkt bereitstellt. Dabei stellt jeder Endpunkt mindestens einen Dienstvorgang zur Verfügung. Der Endpunkt eines Diensts gibt eine Adresse an, unter der der Dienst gefunden werden kann, eine Bindung, die die Information enthält, dass ein Client mit dem Dienst kommunizieren muss, und einen Vertrag, der die Funktionen definiert, die der Dienst für seine Clients bereitstellt. Der Hauptunterschied zwischen WCF und WCF-Relay besteht darin, dass der Endpunkt in der Cloud und nicht lokal auf Ihrem Computer bereitgestellt wird.

Nachdem Sie die Themen in diesem Tutorial nacheinander durchgearbeitet haben, verfügen Sie über einen ausgeführten Dienst und über einen Client, der die Vorgänge des Diensts aufrufen kann. Im ersten Thema wird die Einrichtung eines Kontos beschrieben. Die nächsten drei Schritte beschreiben das Definieren eines Diensts, der einen Vertrag verwendet, das Implementieren des Diensts und das Konfigurieren des Diensts mithilfe von Code. Außerdem wird erläutert, wie der Dienst gehostet und ausgeführt wird. Der erstellte Dienst ist selbstgehostet, und der Client und der Dienst werden auf dem gleichen Computer ausgeführt. Sie können den Dienst mithilfe von Code oder einer Konfigurationsdatei konfigurieren.

Die letzten drei Schritte beschreiben das Erstellen einer Clientanwendung, das Konfigurieren der Clientanwendung sowie das Erstellen und Verwenden eines Clients, der auf die Funktionen des Hosts zugreifen kann.

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Erstellen eines Relaynamespace
> * Erstellen eines WCF-Dienstvertrags
> * Implementieren des WCF-Vertrags
> * Hosten und Ausführen des WCF-Diensts für die Registrierung beim Relaydienst
> * Erstellen eines WCF-Clients für den Dienstvertrag
> * Konfigurieren des WCF-Clients
> * Implementieren des WCF-Clients
> * Ausführen der Anwendungen 

## <a name="prerequisites"></a>Voraussetzungen

Zum Durchführen dieses Tutorials benötigen Sie Folgendes:

- Ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/), bevor Sie beginnen.
- [Visual Studio 2015 oder eine höhere Version.](https://www.visualstudio.com) Für die Beispiele in diesem Tutorial wird Visual Studio 2017 verwendet.
- Azure SDK für .NET. Installieren Sie das SDK über die [SDK-Downloadseite](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Erstellen eines Relaynamespace
Der erste Schritt umfasst die Einrichtung eines Namespaces und das Abrufen eines [Shared Access Signature](../service-bus-messaging/service-bus-sas.md)-Schlüssels (SAS). Ein Namespace stellt eine Anwendungsgrenze für jede Anwendung bereit, die über den Relaydienst verfügbar gemacht wird. Das System generiert automatisch einen SAS-Schlüssel, wenn ein Dienstnamespace erstellt wird. Dienstnamespace und SAS-Schlüssel bilden gemeinsam die Anmeldeinformationen, mit denen sich Azure für den Zugriff auf die Anwendung authentifiziert.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract"></a>Definieren eines WCF-Dienstvertrags
Der Dienstvertrag gibt an, welche Vorgänge (Webdienstterminologie für Methoden oder Funktionen) der Dienst unterstützt. Verträge werden durch die Definition einer C++-, C#- oder Visual Basic-Schnittstelle erstellt. Jede Methode in der Schnittstelle entspricht einem bestimmten Dienstvorgang. Auf jede Schnittstelle muss das Attribut [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) und auf jeden Vorgang das Attribut [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) angewendet werden. Wenn eine Methode in einer Schnittstelle das Attribut [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx), nicht jedoch das Attribut [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) besitzt, wird diese Methode nicht bereitgestellt. Der Code für diese Aufgaben wird im Beispiel im Anschluss an das Verfahren bereitgestellt. Eine ausführlichere Darstellung von Verträgen und Diensten finden Sie in der WCF-Dokumentation unter [Entwerfen und Implementieren von Diensten](https://msdn.microsoft.com/library/ms729746.aspx).

### <a name="create-a-relay-contract-with-an-interface"></a>Erstellen eines Relayvertrags mit einer Schnittstelle

1. Öffnen Sie Visual Studio als Administrator, indem Sie im **Startmenü** mit der rechten Maustaste auf das Programm klicken und dann **Als Administrator ausführen** auswählen.
2. Erstellen Sie ein neues Konsolenanwendungsprojekt. Klicken Sie auf das Menü **Datei**, wählen Sie **Neu** aus, und klicken Sie auf **Projekt**. Klicken Sie im Dialogfeld **Neues Projekt** auf **Visual C#** (wenn **Visual C#** nicht angezeigt wird, suchen Sie unter **Andere Sprachen**). Klicken Sie auf die Vorlage **Konsolenanwendung (.NET Framework)** , und geben Sie ihr den Namen **EchoService**. Klicken Sie auf **OK**, um das Projekt zu erstellen.

    ![Erstellen einer Konsolen-App][2]

3. Installieren Sie das NuGet-Paket für Service Bus. Dieses Paket fügt automatisch Verweise auf die Service Bus-Bibliotheken sowie **System.ServiceModel** (WCF) hinzu. [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) ist der Namespace, der den programmgesteuerten Zugriff auf die grundlegenden Funktionen von WCF ermöglicht. Service Bus verwendet viele Objekte und Attribute von WCF, um Dienstverträge zu definieren.

    Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und klicken Sie dann auf **NuGet-Pakete verwalten**. Klicken Sie auf die Registerkarte Durchsuchen, und suchen Sie nach **WindowsAzure.ServiceBus**. Vergewissern Sie sich, dass im Feld **Version(en)** der Projektname ausgewählt ist. Klicken Sie auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen.

    ![Service Bus-Paket][3]
4. Doppelklicken Sie im Projektmappen-Explorer auf die Datei „Program.cs“, um sie im Editor zu öffnen (sofern sie nicht bereits geöffnet ist).
5. Fügen Sie am Anfang der Datei die folgenden using-Anweisungen ein:

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```
6. Ändern Sie den Namespacenamen vom Standardnamen **EchoService** zu **Microsoft.ServiceBus.Samples**.

   > [!IMPORTANT]
   > In diesem Tutorial wird der C#-Namespace **Microsoft.ServiceBus.Samples** verwendet. Hierbei handelt es sich um den Namespace des durch den Vertrag verwalteten Typs, der im Schritt [Konfigurieren des WCF-Clients](#configure-the-wcf-client) in der Konfigurationsdatei verwendet wird. Sie können beim Erstellen dieses Beispiels jeden gewünschten Namespace angeben, das Tutorial funktioniert jedoch nur, wenn Sie anschließend die Namespaces des Vertrags und des Diensts in der Anwendungskonfigurationsdatei entsprechend ändern. Der in der Datei "App.config" angegebene Namespace muss mit dem in Ihren C#-Dateien angegebenen Namespace identisch sein.
   >
   >
7. Definieren Sie direkt nach der Namespacedeklaration `Microsoft.ServiceBus.Samples` (jedoch innerhalb des Namespaces) eine neue Schnittstelle namens `IEchoContract`, und wenden Sie das Attribut `ServiceContractAttribute` mit dem Namespacewert `https://samples.microsoft.com/ServiceModel/Relay/` auf die Schnittstelle an. Der Namespacewert unterscheidet sich von dem Namespace, den Sie im gesamten Codebereich verwenden. Der Namespacewert wird stattdessen als eindeutiger Bezeichner für diesen Vertrag verwendet. Das explizite Angeben des Namespace verhindert, dass der Standardwert für den Namespace dem Vertragsnamen hinzugefügt wird. Fügen Sie nach der Namespacedeklaration den folgenden Code ein:

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

   > [!NOTE]
   > In der Regel enthält der Dienstvertragsnamespace ein Benennungsschema, das Versionsinformationen umfasst. Durch das Einbeziehen von Versionsinformationen im Dienstvertragsnamespace können Dienste größere Änderungen isolieren, indem ein neuer Dienstvertrag mit einem neuen Namespace definiert und an einem neuen Endpunkt bereitgestellt wird. Auf diese Weise können Clients weiterhin den alten Dienstvertrag verwenden, ohne dass sie aktualisiert werden müssen. Versionsinformationen können aus einer Datumsangabe oder einer Buildnummer bestehen. Weitere Informationen finden Sie unter [Dienstversionsverwaltung](https://go.microsoft.com/fwlink/?LinkID=180498). In diesem Tutorial enthält das Benennungsschema des Dienstvertragsnamespace keine Versionsinformationen.
   >
   >
8. Deklarieren Sie innerhalb der Schnittstelle `IEchoContract` eine Methode für den einzelnen Vorgang, den der Vertrag `IEchoContract` in der Schnittstelle bereitstellt, und wenden Sie das Attribut `OperationContractAttribute` auf die Methode an, die Sie als Teil des öffentlichen WCF-Relayvertrags bereitstellen möchten:

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```
9. Deklarieren Sie direkt im Anschluss an die `IEchoContract`-Schnittstellendefinition einen Kanal mit Vererbung von `IEchoContract` und Vererbung an die `IClientChannel`-Schnittstelle, wie im folgenden Beispiel gezeigt:

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    Ein Kanal ist das WCF-Objekt, über das der Host und der Client einander Informationen übergeben. Später schreiben Sie Code für den Kanal, um ein Echo der Informationen zwischen den beiden Anwendungen zu erzeugen.
10. Klicken Sie im Menü **Erstellen** auf **Projektmappe erstellen**, oder drücken Sie **STRG+UMSCHALT+B**, um Ihre bisherigen Arbeitsschritte zu überprüfen.

### <a name="example"></a>Beispiel

Das folgende Codebeispiel zeigt eine Basisschnittstelle, die einen WCF-Relayvertrag definiert.

```csharp
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Nachdem nun die Schnittstelle erstellt ist, können Sie die Schnittstelle implementieren.

## <a name="implement-the-wcf-contract"></a>Implementieren des WCF-Vertrags

Zum Erstellen eines Azure-Relays müssen Sie zuerst den Vertrag erstellen. Dieser wird mithilfe einer Schnittstelle definiert. Weitere Informationen zum Erstellen der Schnittstelle finden Sie im vorherigen Schritt. Der nächste Schritt ist das Implementieren der Schnittstelle. Dies umfasst das Erstellen einer Klasse namens `EchoService`, welche die benutzerdefinierte `IEchoContract`-Schnittstelle implementiert. Nachdem Sie die Schnittstelle implementiert haben, konfigurieren Sie die Schnittstelle mithilfe einer "App.config"-Konfigurationsdatei. Die Konfigurationsdatei enthält die erforderlichen Informationen für die Anwendung, wie z.B. den Namen des Diensts, den Namen des Vertrags und den Typ des Protokolls, das für die Kommunikation mit dem Relaydienst verwendet wird. Der für diese Aufgaben verwendete Code wird im Beispiel nach dem Verfahren bereitgestellt. Eine allgemeinere Darstellung der Implementierung eines Dienstvertrags finden Sie in der WCF-Dokumentation unter [Implementieren von Dienstverträgen](https://msdn.microsoft.com/library/ms733764.aspx).

1. Erstellen Sie eine neue Klasse namens `EchoService` direkt unterhalb der Definition der `IEchoContract`-Schnittstelle. Die `EchoService`-Klasse implementiert die `IEchoContract`-Schnittstelle.

    ```csharp
    class EchoService : IEchoContract
    {
    }
    ```

    Ähnlich wie bei anderen Schnittstellenimplementierungen können Sie die Definition in einer anderen Datei implementieren. In diesem Tutorial befindet sich die Implementierung allerdings in derselben Datei wie die Schnittstellendefinition und die `Main`-Methode.
2. Wenden Sie das [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx)-Attribut auf die `IEchoContract`-Schnittstelle an. Das Attribut gibt den Dienstnamen und den Namespace an. Die `EchoService`-Klasse sieht dann wie folgt aus:

    ```csharp
    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```
3. Implementieren Sie die `Echo`-Methode, die in der `IEchoContract`-Schnittstelle in der `EchoService`-Klasse definiert ist.

    ```csharp
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```
4. Klicken Sie auf **Build** und dann auf **Projektmappe erstellen**, um die Richtigkeit Ihrer bisherigen Arbeitsschritte zu überprüfen.

### <a name="define-the-configuration-for-the-service-host"></a>Definieren der Konfiguration für den Diensthost

1. Die Konfigurationsdatei ist einer WCF-Konfigurationsdatei sehr ähnlich. Sie enthält den Dienstnamen, den Endpunkt (den Speicherort, den Azure Relay für Clients und Hosts zur Kommunikation bereitstellt) und die Bindung (den Typ des Protokolls, das für die Kommunikation verwendet wird). Der Hauptunterschied besteht darin, dass dieser konfigurierte Dienstendpunkt auf eine [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding)-Bindung verweist, die nicht Bestandteil von .NET Framework ist. [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) ist eine der Bindungen, die durch den Dienst definiert werden.
2. Doppelklicken Sie im **Projektmappen-Explorer** auf die Datei „App.config“, um sie im Visual Studio-Editor zu öffnen.
3. Ersetzen Sie im `<appSettings>`-Element die Platzhalter durch den Namen Ihres Dienstnamespace und den SAS-Schlüssel, den Sie in einem vorherigen Schritt kopiert haben.
4. Fügen Sie innerhalb der `<system.serviceModel>`-Tags ein `<services>`-Element hinzu. Sie können mehrere Relayanwendungen in einer einzigen Konfigurationsdatei definieren. In diesem Tutorial wird jedoch nur eine Anwendung definiert.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```
5. Fügen Sie im `<services>`-Element ein `<service>`-Element hinzu, um den Namen des Diensts zu definieren.

    ```xml
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```
6. Definieren Sie im `<service>`-Element den Speicherort des Endpunktvertrags sowie den Bindungstyp für den Endpunkt.

    ```xml
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    Der Endpunkt definiert, wo der Client nach der Hostanwendung sucht. Dieser Schritt wird später in diesem Tutorial verwendet, um einen URI zu erstellen, der den Host über Azure Relay uneingeschränkt verfügbar macht. Die Bindung deklariert die Verwendung von TCP als Protokoll für die Kommunikation mit dem Relaydienst.
7. Klicken Sie im Menü **Build** auf **Projektmappe erstellen**, um die Richtigkeit Ihrer bisherigen Arbeitsschritte zu überprüfen.

### <a name="example"></a>Beispiel

Der folgende Code zeigt die Implementierung des Dienstvertrags.

```csharp
[ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

Der folgende Code zeigt das grundlegende Format der Datei "App.config", die dem Diensthost zugeordnet ist.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="host-and-run-the-wcf-service-to-register-with-the-relay-service"></a>Hosten und Ausführen des WCF-Diensts für die Registrierung beim Relaydienst

Dieser Schritt beschreibt, wie ein Azure Relay-Dienst ausgeführt wird.

### <a name="create-the-relay-credentials"></a>Erstellen der Relayanmeldeinformationen

1. Erstellen Sie in `Main()` zwei Variablen zum Speichern des Namespace und des SAS-Schlüssels, die aus dem Konsolenfenster gelesen werden.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    Der SAS-Schlüssel wird später für den Zugriff auf Ihr Projekt verwendet. Der Namespace wird als Parameter an `CreateServiceUri` übergeben, um einen Dienst-URI zu erstellen.
2. Deklarieren Sie mithilfe eines [TransportClientEndpointBehavior](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior)-Objekts, dass ein SAS-Schlüssel als Anmeldeinformationstyp verwendet wird. Fügen Sie den folgenden Code direkt unter dem Code hinzu, den Sie im letzten Schritt hinzugefügt haben.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>Erstellen einer Basisadresse für den Dienst

Erstellen Sie nach dem Code, den Sie im letzten Schritt hinzugefügt haben, eine `Uri`-Instanz für die Basisadresse des Diensts. Dieser URI gibt das Service Bus-Schema, den Namespace und den Pfad der Dienstschnittstelle an.

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

"sb" ist eine Abkürzung für das Service Bus-Schema und zeigt an, dass TCP als Protokoll verwendet wird. Dies wurde zuvor bereits in der Konfigurationsdatei durch Angeben von [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) als Bindung festgelegt.

Für dieses Tutorial lautet der URI `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="create-and-configure-the-service-host"></a>Erstellen und Konfigurieren des Diensthosts

1. Legen Sie den Verbindungsmodus auf `AutoDetect` fest.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    Der Verbindungsmodus beschreibt das Protokoll, das der Dienst für die Kommunikation mit dem Relaydienst verwendet: HTTP oder TCP. Mithilfe der Standardeinstellung `AutoDetect` versucht der Dienst, eine Verbindung mit Azure Relay über TCP herzustellen, wenn verfügbar. Wenn TCP nicht verfügbar ist, wird HTTP verwendet. Beachten Sie, dass sich diese Vorgehensweise von dem Protokoll unterscheidet, das der Dienst für die Clientkommunikation angibt. Dieses Protokoll wird durch die verwendete Bindung festgelegt. Ein Dienst kann z.B. die Bindung [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx) verwenden, die angibt, dass ihr Endpunkt über HTTP mit Clients kommuniziert. Der gleiche Dienst kann **ConnectivityMode.AutoDetect** angeben, sodass der Dienst über TCP mit Azure Relay kommuniziert.
2. Erstellen Sie den Diensthost unter Verwendung des URIs, den Sie zuvor in diesem Abschnitt erstellt haben.

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    Der Diensthost ist das WCF-Objekt, das den Dienst instanziiert. Hier wird der Diensttyp übergeben, den Sie erstellen möchten (ein `EchoService`-Typ), sowie die Adresse, unter der der Dienst bereitgestellt werden soll.
3. Fügen Sie am Anfang der Datei „Program.cs“ Verweise auf [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) und [Microsoft.ServiceBus.Description](/dotnet/api/microsoft.servicebus.description) hinzu.

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```
4. Konfigurieren Sie in `Main()` den Endpunkt so, dass öffentlicher Zugriff ermöglicht wird.

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Durch diesen Schritt wird der Relaydienst darüber informiert, dass Ihre Anwendung durch Untersuchen des ATOM-Feeds für Ihr Projekt öffentlich angezeigt werden kann. Wenn Sie **DiscoveryType** auf **private** festlegen, ist ein Client trotzdem in der Lage, auf den Dienst zuzugreifen. Der Dienst wird jedoch nicht angezeigt, wenn der Relaynamespace durchsucht wird. Der Client muss stattdessen bereits vorher den Endpunktpfad kennen.
5. Wenden Sie die Dienstanmeldeinformationen auf die in der Datei "App.config" definierten Dienstendpunkte an:

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Wie bereits im vorherigen Schritt erwähnt wurde, können Sie in der Konfigurationsdatei mehrere Dienste und Endpunkte deklarieren. Wenn dies der Fall ist, durchläuft der Code die Konfigurationsdatei und sucht nach jedem Endpunkt, auf den die Anmeldeinformationen angewendet werden sollen. Für dieses Tutorial weist die Konfigurationsdatei jedoch nur einen Endpunkt auf.

### <a name="open-the-service-host"></a>Öffnen des Diensthosts

1. Öffnen Sie den Dienst.

    ```csharp
    host.Open();
    ```
2. Informieren Sie den Benutzer, dass der Dienst ausgeführt wird, und erläutern Sie, wie der Dienst heruntergefahren wird.

    ```csharp
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```
3. Schließen Sie danach den Diensthost.

    ```csharp
    host.Close();
    ```
4. Drücken Sie **STRG+UMSCHALT+B**, um das Projekt zu erstellen.

### <a name="example"></a>Beispiel

Der abgeschlossene Dienstcode sollte folgendermaßen aussehen. Der Code schließt den Dienstvertrag und die Implementierung aus früheren Schritten des Tutorials ein und hostet den Dienst in einer Konsolenanwendung.

```csharp
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         

            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Relay credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }

            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## <a name="create-a-wcf-client-for-the-service-contract"></a>Erstellen eines WCF-Clients für den Dienstvertrag

Der nächste Schritt besteht darin, eine Clientanwendung zu erstellen und den Dienstvertrag zu definieren, der in späteren Schritten implementiert wird. Beachten Sie, dass viele dieser Schritte den Schritten ähneln, die zum Erstellen eines Diensts verwendet werden: Definieren eines Vertrags, Bearbeiten einer App.config-Datei, Verwenden von Anmeldeinformationen zum Herstellen einer Verbindung mit dem Relaydienst usw. Der für diese Aufgaben verwendete Code wird im Beispiel nach dem Verfahren bereitgestellt.

1. Erstellen Sie ein neues Projekt in der aktuellen Visual Studio-Projektmappe für den Client, indem Sie folgendermaßen vorgehen:

   1. Klicken Sie im Projektmappen-Explorer in der gleichen Projektmappe, die den Dienst enthält, mit der rechten Maustaste auf die aktuelle Projektmappe (nicht auf das Projekt), und klicken Sie auf **Hinzufügen**. Klicken Sie dann auf **Neues Projekt**.
   2. Klicken Sie im Dialogfeld **Neues Projekt hinzufügen** auf **Visual C#** (wenn **Visual C#** nicht angezeigt wird, suchen Sie unter **Andere Sprachen**). Wählen Sie die Vorlage **Konsolenanwendung (.NET Framework)** aus, und nennen Sie sie **EchoClient**.
   3. Klicken Sie auf **OK**.
      <br />
2. Doppelklicken Sie im Projektmappen-Explorer im Projekt **EchoClient** auf die Datei „Program.cs“, um sie im Editor zu öffnen (sofern sie nicht bereits geöffnet ist).
3. Ändern Sie den Namespacenamen vom Standardnamen `EchoClient` in `Microsoft.ServiceBus.Samples`.
4. Installieren Sie das [Service Bus-NuGet-Paket](https://www.nuget.org/packages/WindowsAzure.ServiceBus): Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **EchoClient**, und klicken Sie anschließend auf **NuGet-Pakete verwalten**. Klicken Sie auf die Registerkarte **Durchsuchen**, und suchen Sie nach `Microsoft Azure Service Bus`. Klicken Sie auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen.

    ![][3]
5. Fügen Sie der „Program.cs“-Datei eine `using`-Anweisung für den Namespace [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) hinzu.

    ```csharp
    using System.ServiceModel;
    ```
6. Fügen Sie dem Namespace die Dienstvertragsdefinition hinzu, wie im folgenden Beispiel gezeigt. Beachten Sie, dass diese Definition mit der im Projekt **Service** verwendeten Definition identisch ist. Sie sollten diesen Code am Anfang des Namespace `Microsoft.ServiceBus.Samples` hinzufügen.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```
7. Drücken Sie **STRG+UMSCHALT+B**, um den Client zu erstellen.

### <a name="example"></a>Beispiel

Der folgende Code zeigt den aktuellen Status der Datei „Program.cs“ im Projekt **EchoClient**.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="configure-the-wcf-client"></a>Konfigurieren des WCF-Clients

In diesem Schritt erstellen Sie eine App.config-Datei für eine Clientbasisanwendung, die auf den zuvor in diesem Tutorial erstellten Dienst zugreift. Diese App.config-Datei definiert den Vertrag, die Bindung und den Namen des Endpunkts. Der für diese Aufgaben verwendete Code wird im Beispiel nach dem Verfahren bereitgestellt.

1. Doppelklicken Sie im Projektmappen-Explorer im Projekt **EchoClient** auf **App.config**, um die Datei im Visual Studio-Editor zu öffnen.
2. Ersetzen Sie im `<appSettings>`-Element die Platzhalter durch den Namen Ihres Dienstnamespace und den SAS-Schlüssel, den Sie in einem vorherigen Schritt kopiert haben.
3. Fügen Sie im Element "system.serviceModel" ein Element `<client>` hinzu.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    In diesem Schritt wird deklariert, dass Sie eine Clientanwendung im WCF-Stil definieren.
4. Definieren Sie im Element `client` den Namen, den Vertrag und den Bindungstyp für den Endpunkt.

    ```xml
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    In diesem Schritt werden der Name des Endpunkts, der im Dienst definierte Vertrag und die Verwendung von TCP durch die Clientanwendung für die Kommunikation mit Azure Relay definiert. Der Endpunktname wird im nächsten Schritt verwendet, um diese Endpunktkonfiguration mit dem Dienst-URI zu verknüpfen.
5. Klicken Sie auf **Datei** und anschließend auf **Alles speichern**.

### <a name="example"></a>Beispiel

Der folgende Code zeigt die App.config-Datei für den Echo-Client.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="implement-the-wcf-client"></a>Implementieren des WCF-Clients
In diesem Schritt implementieren Sie eine Clientbasisanwendung, die auf den zuvor in diesem Tutorial erstellten Dienst zugreift. Ähnlich wie der Dienst führt der Client viele derselben Vorgänge aus, um auf Azure Relay zuzugreifen:

1. Festlegen des Verbindungsmodus.
2. Erstellen des URIs, der den Hostdienst ermittelt.
3. Definieren der Sicherheitsanmeldeinformationen.
4. Anwenden der Anmeldeinformationen auf die Verbindung.
5. Öffnen der Verbindung.
6. Ausführen der anwendungsspezifischen Aufgaben.
7. Schließen der Verbindung.

Einer der Hauptunterschiede besteht jedoch darin, dass die Clientanwendung einen Kanal für die Verbindung mit dem Relaydienst verwendet, während der Dienst einen Aufruf an **ServiceHost** verwendet. Der für diese Aufgaben verwendete Code wird im Beispiel nach dem Verfahren bereitgestellt.

### <a name="implement-a-client-application"></a>Implementieren einer Clientanwendung
1. Legen Sie den Verbindungsmodus auf **AutoDetect** fest. Fügen Sie der Anwendung **EchoClient** in der Methode `Main()` den folgenden Code hinzu.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```
2. Definieren Sie Variablen zum Speichern der Werte für den Dienstnamespace und den SAS-Schlüssel, die aus der Konsole gelesen werden.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```
3. Erstellen Sie den URI, der den Speicherort des Hosts in Ihrem Relayprojekt definiert.

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```
4. Erstellen Sie das Objekt für die Anmeldeinformationen für Ihren Dienstnamespace-Endpunkt.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```
5. Erstellen Sie die Kanalfactory, welche die in der App.config-Datei beschriebene Konfiguration lädt.

    ```csharp
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    Bei einer Kanalfactory ist handelt es sich um ein WCF-Objekt, das einen Kanal erstellt, über den der Dienst und die Clientanwendungen kommunizieren.
6. Wenden Sie die Anmeldeinformationen an.

    ```csharp
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```
7. Erstellen und öffnen Sie den Kanal zum Dienst.

    ```csharp
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```
8. Schreiben Sie die grundlegende Benutzeroberfläche und die Funktionen für das Echo.

    ```csharp
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    Beachten Sie, dass der Code die Instanz des Kanalobjekts als Proxy für den Dienst verwendet.
9. Schließen Sie den Kanal und die Factory.

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

### <a name="example"></a>Beispiel

Der abgeschlossene Code sollte wie folgt aussehen. Er zeigt, wie eine Clientanwendung erstellt wird, wie die Dienstvorgänge aufgerufen werden und wie der Client geschlossen wird, nachdem der Vorgangsaufruf abgeschlossen wurde.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;


            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## <a name="run-the-applications"></a>Ausführen der Anwendungen

1. Drücken Sie **STRG+UMSCHALT+B**, um die Lösung zu erstellen. Dadurch werden das Clientprojekt und das Dienstprojekt erstellt, die Sie in den vorherigen Schritten erstellt haben.
2. Vergewissern Sie sich vor dem Ausführen der Clientanwendung, dass die Dienstanwendung ausgeführt wird. Klicken Sie in Visual Studio im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe **EchoService**, und klicken Sie anschließend auf **Eigenschaften**.
3. Klicken Sie im Dialogfeld mit den Projektmappeneigenschaften auf **Startprojekt** und dann auf die Schaltfläche **Mehrere Startprojekte**. Vergewissern Sie sich, dass **EchoService** als erster Eintrag in der Liste angezeigt wird.
4. Legen Sie das Feld **Aktion** sowohl für das Projekt **EchoService** als auch für das Projekt **EchoClient** auf **Starten** fest.

    ![][5]
5. Klicken Sie auf **Projektabhängigkeiten**. Wählen Sie im Feld **Projekte** die Option **EchoClient** aus. Vergewissern Sie sich im Feld **Abhängigkeiten**, dass **EchoService** aktiviert ist.

    ![][6]
6. Klicken Sie auf **OK**, um das Dialogfeld mit den **Eigenschaften** zu schließen.
7. Drücken Sie **F5**, um beide Projekte auszuführen.
8. Beide Konsolenfenster werden geöffnet, und Sie werden jeweils zur Angabe des Namespacenamens aufgefordert. Der Dienst muss zuerst ausgeführt werden. Geben Sie daher im Konsolenfenster **EchoService** den Namespace ein, und drücken Sie dann die **EINGABETASTE**.
9. Als Nächstes werden Sie aufgefordert, Ihren SAS-Schlüssel einzugeben. Geben Sie den SAS-Schlüssel ein, und drücken Sie die EINGABETASTE.

    Im Folgenden wird eine Beispielausgabe des Konsolenfensters gezeigt. Beachten Sie, dass die hier bereitgestellten Werte nur Beispiele sind.

    `Your Service Namespace: myNamespace``Your SAS Key: <SAS key value>`

    Die Dienstanwendung gibt die Adresse, an der sie lauscht, wie im folgenden Beispiel gezeigt im Konsolenfenster aus:

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/``Press [Enter] to exit`
10. Geben Sie im Konsolenfenster **EchoClient** die gleichen Informationen wie für die Dienstanwendung ein. Geben Sie anhand der vorherigen Schritte die gleichen Dienstnamespace- und SAS-Schlüsselwerte für die Clientanwendung ein.
11. Nachdem Sie diese Werte eingegeben haben, öffnet der Client einen Kanal zum Dienst und fordert Sie auf, Text einzugeben, wie im folgenden Beispiel für die Konsolenausgabe gezeigt.

    `Enter text to echo (or [Enter] to exit):`

    Geben Sie Text ein, der an die Dienstanwendung gesendet werden soll, und drücken Sie die EINGABETASTE. Dieser Text wird über den Echo-Dienstvorgang an den Dienst gesendet und im Dienstkonsolenfenster ähnlich wie in der folgenden Beispielausgabe angezeigt.

    `Echoing: My sample text`

    Die Clientanwendung empfängt den Rückgabewert des `Echo`-Vorgangs (den ursprünglichen Text) und gibt diesen in ihrem Konsolenfenster aus. Im Folgenden wird eine Beispielausgabe des Clientkonsolenfensters gezeigt.

    `Server echoed: My sample text`
12. Sie können auf diese Weise weitere Textnachrichten vom Client an den Dienst senden. Wenn Sie fertig sind, drücken Sie die EINGABETASTE in den Client- und Dienstkonsolenfenstern, um beide Anwendungen zu beenden.

## <a name="next-steps"></a>Nächste Schritte
Fahren Sie mit dem folgenden Tutorial fort: 

> [!div class="nextstepaction"]
>[Verfügbarmachen eines lokalen WCF-REST-Diensts für einen Client außerhalb Ihres Netzwerks](service-bus-relay-rest-tutorial.md)

[2]: ./media/service-bus-relay-tutorial/create-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png

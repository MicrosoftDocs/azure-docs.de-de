---
title: Erste Schritte mit Azure Relay-WCF-Relays in .NET | Microsoft-Dokumentation
description: Erfahren Sie etwas über die Verwendung von Azure Relay-WCF-Relays zum Herstellen einer Verbindung zwischen zwei Anwendungen, die an unterschiedlichen Standorten gehostet sind.
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 5493281a-c2e5-49f2-87ee-9d3ffb782c75
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/20/2017
ms.author: spelluru
ms.openlocfilehash: ee78227f645cbeded7a5c689750db835faf1055f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60420123"
---
# <a name="how-to-use-azure-relay-wcf-relays-with-net"></a>Verwenden von Azure Relay-WCF-Relays mit .NET
In diesem Artikel wird die Verwendung des Azure Relay-Diensts beschrieben. Die Beispiele sind in C# geschrieben und nutzen die Windows Communication Foundation (WCF)-API mit den Erweiterungen, die in der Service Bus-Assembly enthalten sind. Weitere Informationen zum Azure Relay-Dienst finden Sie in der [Übersicht über Azure Relay](relay-what-is-it.md).

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="what-is-wcf-relay"></a>Was ist WCF Relay?

Der Azure [*WCF Relay*](relay-what-is-it.md)-Dienst ermöglicht die Erstellung von Hybridanwendungen, die sowohl in einem Azure-Rechenzentrum als auch in Ihrer eigenen lokalen Unternehmensumgebung ausgeführt werden. Der Relay-Dienst erleichtert dies, indem er Ihnen die Möglichkeit bietet, WCF-Dienste (Windows Communication Foundation), die sich in einem Unternehmensnetzwerk befinden, sicher in der öffentlichen Cloud bereitzustellen, ohne dass eine Firewallverbindung geöffnet werden muss oder umfassende Änderungen an der unternehmensinternen Netzwerkinfrastruktur erforderlich werden.

![WCF Relay-Konzepte](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

Azure Relay bietet Ihnen die Möglichkeit, WCF-Dienste in Ihrer bestehenden Unternehmensumgebung zu hosten. Sie können das Lauschen auf eingehende Sitzungen und Anforderungen für diese WCF-Dienste dann an den Relay-Dienst unter Azure delegieren. Auf diese Weise erhalten Sie die Möglichkeit, diese Dienste für Anwendungscode unter Azure, für mobile Mitarbeiter oder für Extranetpartnerumgebungen bereitzustellen. Mit Relay können Sie auf sichere Weise steuern, welche Personen auf welcher Optimierungsstufe Zugriff auf diese Dienste erhalten. Der Dienst stellt ein leistungsstarkes und sicheres Verfahren zur Verfügung, um Anwendungsfunktionen und Daten aus bestehenden Unternehmenslösungen bereitzustellen und in der Cloud davon zu profitieren.

In diesem Artikel wird beschrieben, wie mit Azure Relay ein mithilfe der TCP-Channelbindung bereitgestellter WCF-Webdienst erstellt wird, der eine geschützte Unterhaltung zwischen zwei Parteien implementiert.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="get-the-service-bus-nuget-package"></a>Abrufen des NuGet-Pakets "Service Bus"
Das [Service Bus NuGet-Paket](https://www.nuget.org/packages/WindowsAzure.ServiceBus) stellt die einfachste Möglichkeit zum Abrufen der Service Bus-API und zum Konfigurieren der Anwendung mit allen Service Bus-Abhängigkeiten dar. Gehen Sie wie folgt vor, um das NuGet-Paket in Ihrem Projekt zu installieren:

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Verweise**, und klicken Sie anschließend auf **NuGet-Pakete verwalten**.
2. Suchen Sie nach „Service Bus“, und wählen Sie das Element **Microsoft Azure Service Bus** aus. Klicken Sie auf **Installieren**, um die Installation durchzuführen. Schließen Sie danach das folgende Dialogfeld:
   
   ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)

## <a name="expose-and-consume-a-soap-web-service-with-tcp"></a>Verfügbarmachen und Nutzen eines SOAP-Webdiensts mit TCP
Um einen bestehenden WCF SOAP-Webdienst für die externe Nutzung bereitzustellen, müssen Sie Änderungen an den Dienstbindungen und Adressen vornehmen. Dies kann Änderungen an der Konfigurationsdatei oder Codeänderungen erforderlich machen, je nachdem, wie Ihre WCF-Dienste eingerichtet und konfiguriert sind. Beachten Sie, dass WCF Ihnen die Möglichkeit bietet, mehrere Netzwerkendpunkte über denselben Dienst zu verwalten, sodass Sie die bestehenden internen Endpunkte beibehalten können, während Sie gleichzeitig Relay-Endpunkte für den externen Zugriff hinzufügen.

Bei dieser Aufgabe erstellen Sie einen einfachen WCF-Dienst und fügen ihm einen Relaylistener hinzu. Für diese Übung wird eine gewisse Vertrautheit mit Visual Studio vorausgesetzt. Es werden daher nicht alle Details der Projekterstellung erläutert. Stattdessen steht der Code im Vordergrund.

Richten Sie vor dem Ausführen dieser Schritte zunächst mit dem folgenden Verfahren Ihre Umgebung ein:

1. Erstellen Sie in Visual Studio eine Konsolenanwendung, die die beiden Projekte "Client" und "Service" innerhalb der Lösung enthält.
2. Fügen Sie beiden Projekte das NuGet-Paket für Service Bus hinzu. Mit diesem Paket werden den Projekten alle benötigten Assemblyverweise hinzugefügt.

### <a name="how-to-create-the-service"></a>Erstellen des Diensts
Erstellen Sie zunächst den Dienst selbst Ein WCF-Dienst besteht aus mindestens drei verschiedenen Teilen:

* Definition eines Vertrags, der beschreibt, welche Nachrichten ausgetauscht werden und welche Operationen aufgerufen werden sollen
* Implementierung des Vertrags
* Dem Host, der diesen WCF-Dienst hostet und mehrere Endpunkte bereitstellt

Die Codebeispiele in diesem Abschnitt beziehen sich auf jede dieser Komponenten.

Der Vertrag definiert einen einzelnen Vorgang (`AddNumbers`), der zwei Zahlen hinzufügt und das Ergebnis zurückgibt. Die Schnittstelle `IProblemSolverChannel` ermöglicht dem Client die einfachere Verwaltung der Proxylebensdauer. Das Erstellen einer solchen Schnittstelle wird als eine bewährte Methode betrachtet. Es ist sinnvoll, die Vertragsdefinition in einer separaten Datei abzulegen, sodass Sie von beiden Projekten aus ("Client" und "Service") auf diese Datei verweisen können. Sie können den Code aber auch in beide Projekte kopieren.

```csharp
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

Nachdem der Vertrag etabliert wurde, erfolgt die Implementierung wie folgt:

```csharp
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### <a name="configure-a-service-host-programmatically"></a>Programmgesteuertes Konfigurieren eines Diensthosts
Nachdem der Vertrag erstellt und die Implementierung durchgeführt wurde, kann nun der Dienst gehostet werden. Das Hosting erfolgt innerhalb eines [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/system.servicemodel.servicehost.aspx)-Objekts, das die Verwaltung der Dienstinstanzen übernimmt und die Endpunkte hostet, die auf Nachrichten warten. Der folgende Code konfiguriert den Dienst sowohl mit einem regulären lokalen Endpunkt als auch mit einem Relay-Endpunkt, um interne und externe Endpunkte nebeneinander zu veranschaulichen. Ersetzen Sie die Zeichenfolge *namespace* durch Ihren Namespacenamen und *yourKey* durch den SAS-Schlüssel, den Sie im vorherigen Setupschritt erhalten haben.

```csharp
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

sh.AddServiceEndpoint(
   typeof (IProblemSolver), new NetTcpBinding(),
   "net.tcp://localhost:9358/solver");

sh.AddServiceEndpoint(
   typeof(IProblemSolver), new NetTcpRelayBinding(),
   ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
    .Behaviors.Add(new TransportClientEndpointBehavior {
          TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "<yourKey>")});

sh.Open();

Console.WriteLine("Press ENTER to close");
Console.ReadLine();

sh.Close();
```

In dem Beispiel erstellen Sie zwei Endpunkte für dieselbe Vertragsimplementierung. Einer ist lokal, und einer wird über Azure Relay projiziert. Die zentralen Unterschiede sind die Bindungen: [NetTcpBinding](https://msdn.microsoft.com/library/system.servicemodel.nettcpbinding.aspx) für den lokalen Endpunkt und [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) für den Relay-Endpunkt und die Adressen. Der lokale Endpunkt verfügt über eine lokale Netzwerkadresse mit einem bestimmten Port. Der Relay-Endpunkt hat eine Endpunktadresse, die sich aus der Zeichenfolge `sb`, Ihrem Namespacenamen und dem Pfad „solver“ zusammensetzt. Dies ergibt den URI `sb://[serviceNamespace].servicebus.windows.net/solver`, mit dem der Dienstendpunkt als Service Bus-TCP-Endpunkt (Relay) mit vollqualifiziertem externem DNS-Namen identifiziert wird. Wenn Sie Code, der die Platzhalter ersetzt, in die `Main`-Funktion der Anwendung **Service** einfügen, erhalten Sie einen funktionierenden Dienst. Wenn Ihr Dienst exklusiv für Relay lauschen soll, entfernen Sie die lokale Endpunktdeklaration.

### <a name="configure-a-service-host-in-the-appconfig-file"></a>Konfigurieren eines Diensthosts in der Datei „App.config“
Sie können den Host auch mithilfe der Datei "App.config" konfigurieren. Der Diensthostingcode wird in diesem Fall im nächsten Beispiel angezeigt.

```csharp
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

Die Endpunktdefinitionen werden in die Datei "App.config" verschoben. Das NuGet-Paket hat der Datei „App.config“ bereits eine Reihe von Definitionen hinzugefügt, die die erforderlichen Konfigurationserweiterungen für Azure Relay darstellen. Das folgende Beispiel, das mit dem vorherigen Beispiel identisch ist, sollte direkt unter dem **system.serviceModel**-Element angezeigt werden. In dem Codebeispiel wird davon ausgegangen, dass der C#-Namespace des Projekts den Namen **Service** hat.
Ersetzen Sie die Platzhalter durch den Namespacenamen und den SAS-Schlüssel von Relay.

```xml
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://<namespaceName>.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </service>
</services>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

Nachdem Sie diese Änderungen vorgenommen haben, startet der Dienst wie zuvor, jedoch mit zwei aktiven Endpunkten: einem lokalen Endpunkt und einem, der in der Cloud lauscht.

### <a name="create-the-client"></a>Erstellen des Clients
#### <a name="configure-a-client-programmatically"></a>Programmgesteuertes Konfigurieren eines Clients
Um den Dienst zu nutzen, können Sie einen WCF-Client mit einem [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx)-Objekt erstellen. Service Bus verwendet ein Token-basiertes Sicherheitsmodell, das mit SAS implementiert wird. Die [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider)-Klasse stellt einen Sicherheitstokenanbieter mit integrierten Factory-Methoden dar, die einige bekannte Tokenanbieter zurückgeben. Im folgenden Beispiel wird die [CreateSharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider)-Methode zum Behandeln der Beschaffung des richtigen SAS-Tokens verwendet. Die Name und der Schlüssel wurden aus dem Portal abgerufen, wie im vorherigen Abschnitt beschrieben wurde.

Verweisen Sie zuerst auf den `IProblemSolver`-Vertragscode, oder kopieren Sie ihn aus dem Dienst in Ihr Clientprojekt.

Ersetzen Sie anschließend den Code in der `Main`-Methode des Clients, und ersetzen Sie dabei den Platzhaltertext erneut durch Ihren Relay-Namespace und SAS-Schlüssel.

```csharp
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "<namespaceName>", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","<yourKey>") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Sie können jetzt den Client und den Dienst erstellen und ausführen (führen Sie zuerst den Dienst aus). Der Client ruft den Dienst auf und gibt **9** aus. Der Client und der Server können auf verschiedenen Computern ausgeführt werden (selbst über Netzwerke hinweg). Die Kommunikation funktioniert weiterhin. Der Clientcode kann ebenfalls in der Cloud oder lokal ausgeführt werden.

#### <a name="configure-a-client-in-the-appconfig-file"></a>Konfigurieren eines Clients in der Datei „App.config“
Der folgende Code zeigt, wie Sie den Client auch mithilfe der Datei "App.config" konfigurieren können.

```csharp
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Die Endpunktdefinitionen werden in die Datei "App.config" verschoben. Das folgende Beispiel, das mit dem vorherigen Code identisch ist, sollte direkt unter dem `<system.serviceModel>`-Element angezeigt werden. Wie zuvor müssen Sie auch hier die Platzhalter durch den Relay-Namespace und SAS-Schlüssel ersetzen.

```xml
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://<namespaceName>.servicebus.windows.net/solver"
              behaviorConfiguration="sbTokenProvider"/>
</client>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun mit den Grundlagen zu Azure Relay vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

* [Was ist Azure Relay?](relay-what-is-it.md)
* Laden Sie Service Bus-Beispiele unter [Azure-Beispiele][Azure samples] herunter, oder sehen Sie sich die [Übersicht über Service Bus-Beispiele][overview of Service Bus samples] an.

[Shared Access Signature Authentication with Service Bus]: ../service-bus-messaging/service-bus-shared-access-signature-authentication.md
[Azure samples]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
[overview of Service Bus samples]: ../service-bus-messaging/service-bus-samples.md

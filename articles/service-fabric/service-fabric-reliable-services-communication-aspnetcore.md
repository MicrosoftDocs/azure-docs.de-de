---
title: Dienstkommunikation mit ASP.NET Core
description: Hier erfahren Sie, wie Sie ASP.NET Core in zustandslosen und zustandsbehafteten Azure Service Fabric-Reliable Services-Anwendungen verwenden.
ms.topic: conceptual
ms.date: 10/12/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: ba5626d477bbd6aa07d89703cc37b157f4cfd4d5
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576790"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET Core in Azure Service Fabric Reliable Services

ASP.NET Core ist ein Open-Source- und plattformübergreifendes Framework. Dieses Framework wurde entwickelt, um cloudbasierte, mit dem Internet verbundene Anwendungen wie Web-Apps, IoT-Apps und mobile Back-Ends zu erstellen.

Dieser Artikel ist eine ausführliche Anleitung zum Hosten von ASP.NET Core-Diensten in Service Fabric Reliable Services mithilfe der **Microsoft.ServiceFabric.AspNetCore.** - NuGet-Pakete.

Ein einführendes Tutorial auf ASP.NET Core in Service Fabric und Anweisungen zum Abrufen des Setups für Ihre Entwicklungsumgebung finden Sie unter [Tutorial: Erstellen und Bereitstellen einer Anwendung mit einem ASP.NET Core-Web-API-Front-End-Dienst und einem zustandsbehafteten Back-End-Dienst](service-fabric-tutorial-create-dotnet-app.md).

Im weiteren Verlauf dieses Artikels wird vorausgesetzt, dass Sie bereits mit ASP.NET Core vertraut sind. Wenn nicht, empfehlen wir Ihnen, die [ASP.NET Core-Grundlagen](/aspnet/core/fundamentals/index) zu lesen.

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core in der Service Fabric-Umgebung

ASP.NET Core- und Service Fabric-Apps können unter .NET Core oder unter des vollständigen Version von .NET Framework ausgeführt werden. ASP.NET Core kann in Service Fabric auf zwei unterschiedliche Arten verwendet werden:
 - **Gehostet als ausführbare Gastanwendungsdatei:** Diese Methode wird hauptsächlich verwendet, um bereits vorhandene ASP.NET Core-Anwendungen ohne Codeänderungen unter Service Fabric auszuführen.
 - **Im Rahmen eines zuverlässigen Diensts:** Diese Methode ermöglicht eine bessere Integration in die Service Fabric-Laufzeit und die Verwendung zustandsbehafteter ASP.NET Core-Dienste.

Im weiteren Verlauf dieses Artikels erfahren Sie, wie Sie ASP.NET Core mithilfe der im Service Fabric SDK enthaltenen ASP.NET Core-Integrationskomponenten in einem zuverlässigen Dienst verwenden.

## <a name="service-fabric-service-hosting"></a>Service Fabric-Diensthosting

In Service Fabric werden einzelne oder mehrere Instanzen und/oder Replikate Ihres Diensts in einem *Diensthostprozess* in Form einer ausführbaren Datei mit Ihrem Dienstcode ausgeführt. Als Dienstautor sind Sie für den Diensthostprozess zuständig. Dieser wird von Service Fabric für Sie aktiviert und überwacht.

Die herkömmliche ASP.NET-Version (bis MVC 5) ist über „System.Web.dll“ eng an IIS gekoppelt. Bei ASP.NET Core sind der Webserver und Ihre Webanwendung getrennt. Durch diese Trennung können Webanwendungen zwischen verschiedenen Webservern portiert werden. Außerdem ermöglicht sie, dass Webserver *selbstgehostet* sein können. Das bedeutet, dass Sie einen Webserver in Ihrem eigenen Prozess starten können, im Gegensatz zu einem Prozess, der sich im Besitz einer dedizierten Webserversoftware befindet (beispielsweise IIS).

Wenn Sie einen Service Fabric-Dienst und ASP.NET als ausführbare Gastanwendungsdatei oder in einem zuverlässigen Dienst kombinieren möchten, müssen Sie innerhalb Ihres Diensthostprozesses ASP.NET starten können. Dies ist dank des Selbsthostings von ASP.NET Core möglich.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hosten von ASP.NET Core in einem zuverlässigen Dienst
In der Regel erstellen selbst gehostete ASP.NET Core Anwendungen einen Webhost im Einstiegspunkt einer Anwendung (wie etwa die `static void Main()`-Methode in `Program.cs`). In diesem Fall ist der Lebenszyklus des Webhosts an den Lebenszyklus des Prozesses gebunden.

![Hosten von ASP.NET Core in einen Prozess][0]

Aber der Anwendungseinstiegspunkt ist nicht der richtige Ort, um einen WebHost in einem zuverlässigen Dienst zu erstellen. Dies liegt daran, dass der Anwendungseinstiegspunkt nur verwendet wird, um einen Diensttyp bei der Service Fabric Runtime zu registrieren, sodass er Instanzen dieses Diensttyps erstellen kann. Der Webhost muss stattdessen direkt in einem zuverlässigen Dienst erstellt werden. Innerhalb des Diensthostprozesses können Dienstinstanzen und/oder -replikate mehrere Lebenszyklen durchlaufen. 

Eine zuverlässige Dienstinstanz wird durch Ihre von `StatelessService` oder `StatefulService` abgeleitete Dienstklasse dargestellt. Der Kommunikationsstapel für einen Dienst befindet sich in Ihrer Dienstklasse in einer `ICommunicationListener`-Implementierung. Die NuGet-Pakete vom Typ `Microsoft.ServiceFabric.AspNetCore.*` enthalten Implementierungen von `ICommunicationListener`, die den ASP.NET Core-Webhost für Kestrel oder „HTTP.sys“ in einem zuverlässigen Dienst starten und verwalten.

![Abbildung zum Hosten von ASP.NET Core in einem zuverlässigen Dienst][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ICommunicationListener-Implementierungen für ASP.NET Core
Die `ICommunicationListener`-Implementierungen für Kestrel und „HTTP.sys“ in den `Microsoft.ServiceFabric.AspNetCore.*`-NuGet-Paketen weisen ähnliche Verwendungsmuster auf. Aber sie führen geringfügig unterschiedliche Aktionen aus, die für jeden Webserver spezifisch sind. 

Beide Kommunikationslistener bieten einen Konstruktor, der folgende Argumente akzeptiert:
 - **`ServiceContext serviceContext`** : Dies ist das `ServiceContext`-Objekt mit Informationen zum ausgeführten Dienst.
 - **`string endpointName`** : Der Name einer `Endpoint`-Konfiguration in „ServiceManifest.xml“. Hier liegt der Hauptunterschied zwischen den beiden Kommunikationslistenern. „HTTP.sys“ *erfordert* eine `Endpoint`-Konfiguration, Kestrel hingegen nicht.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`** : Ein von Ihnen implementiertes Lambda, in dem Sie ein `IWebHost`-Element erstellen und zurückgeben. Dadurch können Sie `IWebHost` in einer ASP.NET Core-Anwendung wie gewohnt konfigurieren. Das Lambda stellt eine URL bereit, die für Sie in Abhängigkeit von den verwendeten Service Fabric-Integrationsoptionen und der angegebenen `Endpoint`-Konfiguration generiert wird. Sie können diese URL dann ändern oder verwenden, um den Webserver zu starten.

## <a name="service-fabric-integration-middleware"></a>Middleware für die Service Fabric-Integration
Das NuGet-Paket `Microsoft.ServiceFabric.AspNetCore` enthält die `UseServiceFabricIntegration`-Erweiterungsmethode für `IWebHostBuilder`, die Service Fabric-fähige Middleware hinzufügt. Diese Middleware konfiguriert den Kestrel- oder HTTP.sys-`ICommunicationListener`, um eine eindeutige Dienst-URL beim Service Fabric Naming Service zu registrieren. Anschließend überprüft sie Clientanforderungen, um sicherzustellen, dass Clients eine Verbindung mit dem richtigen Dienst herstellen. 

Dieser Schritt ist erforderlich, um zu verhindern, dass Clients versehentlich eine Verbindung mit dem falschen Dienst herstellen. Das liegt daran, dass in einer gemeinsam verwendeten Hostumgebung wie Service Fabric mehrere Webanwendungen auf demselben physischen oder virtuellen Computer ausgeführt werden können, diese aber keine eindeutigen Hostnamen verwenden. Dieses Szenario wird im nächsten Abschnitt ausführlicher beschrieben.

### <a name="a-case-of-mistaken-identity"></a>Ein Fall von Identitätsverwechslung
Dienstreplikate lauschen unabhängig vom Protokoll an einer eindeutigen Kombination aus IP-Adresse und Port. Nachdem ein Dienstreplikat damit begonnen hat, an einem IP:Port-Endpunkt zu lauschen, meldet es die entsprechende Endpunktadresse an den Service Fabric Naming Service. Dort kann sie von Clients oder anderen Diensten erkannt werden. Wenn Dienste dynamisch zugewiesene Anwendungsports verwenden, kann ein Dienstreplikat zufällig den gleichen IP:Port-Endpunkt eines anderen Diensts verwenden, der sich zuvor auf dem gleichen physischen oder virtuellen Computer befand. Das kann dazu führen, dass ein Client irrtümlich eine Verbindung mit dem falschen Dienst herstellt. Dieses Szenario kann bei folgendem Ereignisablauf eintreten:

 1. Dienst A lauscht an 10.0.0.1:30000 über HTTP. 
 2. Der Client löst Dienst A auf und erhält die Adresse 10.0.0.1:30000.
 3. Dienst A wird auf einen anderen Knoten verschoben.
 4. Dienst B wird unter 10.0.0.1 platziert und verwendet zufällig ebenfalls den Port 30000.
 5. Der Client versucht, mit Dienst A unter Verwendung der zwischengespeicherten Adresse 10.0.0.1:30000 eine Verbindung herzustellen.
 6. Der Client ist nun mit Dienst B verbunden und weiß nicht, dass es sich dabei um den falschen Dienst handelt.

Das kann zu gelegentlich auftretenden Fehlern führen, die sich nur schwer diagnostizieren lassen.

### <a name="using-unique-service-urls"></a>Verwenden eindeutiger Dienst-URLs
Um diese Fehler zu verhindern, können Dienste für den Naming Service einen Endpunkt mit einem eindeutigen Bezeichner veröffentlichen und dann bei Clientanforderungen den eindeutigen Bezeichner überprüfen. Hierbei handelt es sich um einen kooperativen Vorgang zwischen Diensten in einer vertrauenswürdigen Umgebung ohne schädliche Mandanten. Die Methode bietet keine sichere Dienstauthentifizierung in einer Umgebung mit schädlichen Mandanten.

In einer vertrauenswürdigen Umgebung fügt die durch die `UseServiceFabricIntegration`-Methode hinzugefügte Middleware automatisch einen eindeutigen Bezeichner an die Adresse an, die für den Naming Service veröffentlicht wird. Sie überprüft diesen Bezeichner bei jeder Anforderung. Stimmt der Bezeichner nicht überein, gibt die Middleware sofort eine Antwort vom Typ „HTTP 410 Fehlend“ zurück.

Dienste mit dynamisch zugewiesenem Port sollten diese Middleware verwenden.

Bei Diensten mit einem festen eindeutigen Port tritt dieses Problem in einer kooperativen Umgebung nicht auf. Ein fester eindeutiger Port wird üblicherweise für externe Dienste verwendet, die einen bekannten Port benötigen, mit dem Clientanwendungen eine Verbindung herstellen können. So verwenden beispielsweise die meisten Webanwendungen mit Internetzugriff Port 80 oder 443 für Webbrowserverbindungen. In diesem Fall sollte der eindeutige Bezeichner nicht aktiviert werden.

Das folgende Diagramm zeigt die Abfolge der Anforderungen bei aktivierter Middleware:

![Service Fabric ASP.NET Core-integration][2]

Die `ICommunicationListener`-Implementierungen von Kestrel und „HTTP.sys“ verwenden diesen Mechanismus auf die gleiche Weise. Obwohl „HTTP.sys“ intern Anforderungen basierend auf eindeutigen URL-Pfaden unterscheiden kann, indem das zugrunde liegende **HTTP.sys**-Portfreigabefeature verwendet wird, wird diese Funktionalität von der `ICommunicationListener`-Implementierung von „HTTP.sys“ *nicht* verwendet. Das liegt daran, dass es im zuvor beschriebenen Szenario zu HTTP 503- und HTTP 404-Fehlerstatuscodes kommen würde. Clients können dadurch wiederum nur schwer die Absicht des Fehlers bestimmen, da „HTTP 503“ und „HTTP 404“ häufig zur Angabe anderer Fehler verwendet werden. 

Daher verwenden `ICommunicationListener`-Implementierungen von Kestrel und „HTTP.sys“ von der Erweiterungsmethode `UseServiceFabricIntegration` bereitgestellte Middleware als Standard. Aus diesem Grund müssen Clients nur eine erneute Auflösungsaktion des Dienstendpunkts für Antworten vom Typ HTTP 410 ausführen.

## <a name="httpsys-in-reliable-services"></a>„HTTP.sys“ in Reliable Services
Sie können „HTTP.sys“ in Reliable Services verwenden, indem Sie das NuGet-Paket **Microsoft.ServiceFabric.AspNetCore.HttpSys** importieren. Dieses Paket enthält `HttpSysCommunicationListener`, eine Implementierung von `ICommunicationListener`. `HttpSysCommunicationListener` ermöglicht das Erstellen eines ASP.NET Core-Webhosts innerhalb eines zuverlässigen Diensts mit „HTTP.sys“ als Webserver.

„HTTP.sys“ basiert auf der [Windows-HTTP-Server-API](/windows/win32/http/http-api-start-page). Diese API verwendet den **HTTP.sys**-Kerneltreiber, um HTTP-Anforderungen zu verarbeiten und sie an Prozesse weiterzuleiten, die Webanwendungen ausführen. Dadurch können mehrere Prozesse auf dem gleichen physischen oder virtuellen Computer Webanwendungen am gleichen Port hosten. Die Unterscheidung erfolgt entweder anhand eines eindeutigen URL-Pfads oder anhand eines eindeutigen Hostnamens. Mit diesen Features können in Service Fabric mehrere Websites im gleichen Cluster gehostet werden.

>[!NOTE]
>Die HTTP.sys-Implementierung funktioniert nur auf der Windows-Plattform.

Sie folgende Abbildung veranschaulicht für „HTTP.sys“ die Verwendung des **HTTP.sys**-Kerneltreibers unter Windows für die Portfreigabe:

![HTTP.sys-Abbildung][3]

### <a name="httpsys-in-a-stateless-service"></a>„HTTP.sys“ in einem zustandslosen Dienst
Wenn Sie `HttpSys` in einem zustandslosen Dienst verwenden möchten, müssen Sie die `CreateServiceInstanceListeners`-Methode überschreiben und eine `HttpSysCommunicationListener`-Instanz zurückgeben:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseHttpSys()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="httpsys-in-a-stateful-service"></a>„HTTP.sys“ in einem zustandsbehafteten Dienst

`HttpSysCommunicationListener` ist aufgrund von Schwierigkeiten mit dem zugrunde liegenden **HTTP.sys**-Portfreigabefeature derzeit nicht für die Verwendung in zustandsbehafteten Diensten geeignet. Weitere Informationen finden Sie im folgenden Abschnitt zur dynamischen Portzuordnung mit „HTTP.sys“. Für zustandsbehaftete Dienste wird Kestrel als Webserver empfohlen.

### <a name="endpoint-configuration"></a>Endpunktkonfiguration

Eine `Endpoint`-Konfiguration ist für Webserver erforderlich, die die Windows-HTTP-Server-API verwenden. Hierzu zählt auch „HTTP.sys“. Webserver, die die Windows-HTTP-Server-API verwenden, müssen ihre URL zuerst mit „HTTP.sys.“ reservieren. (Hierzu wird üblicherweise das Tool [netsh](/windows/win32/http/netsh-commands-for-http) verwendet.) 

Für diese Aktion sind erhöhte Rechte erforderlich, über die Ihre Dienste nicht standardmäßig verfügen. Mithilfe der Optionen „http“ und „https“ für die `Protocol`-Eigenschaft der `Endpoint`-Konfiguration in „ServiceManifest.xml“ wird die Service Fabric-Laufzeit spezifisch angewiesen, in Ihrem Auftrag eine URL mit „HTTP.sys“ zu registrieren. Dies geschieht mithilfe des URL-Präfixes für einen [*starken Platzhalter*](/windows/win32/http/urlprefix-strings).

Wenn Sie also beispielsweise `http://+:80` für einen Dienst reservieren möchten, verwenden Sie in „ServiceManifest.xml“ die folgende Konfiguration:

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

Und der Endpunktname muss an den `HttpSysCommunicationListener`-Konstruktor übergeben werden:

```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseHttpSys()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-httpsys-with-a-static-port"></a>Verwenden von „HTTP.sys“ mit einem statischen Port
Wenn Sie „HTTP.sys“ mit einem statischen Port verwenden möchten, geben Sie in der `Endpoint`-Konfiguration die Portnummer an:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Verwenden von „HTTP.sys“ mit einem dynamischen Port
Wenn Sie „HTTP.sys“ mit einem dynamisch zugewiesenen Port verwenden möchten, lassen Sie die `Port`-Eigenschaft in der `Endpoint`-Konfiguration weg:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Ein von einer `Endpoint`-Konfiguration zugeordneter dynamischer Port stellt nur einen einzelnen Port *pro Hostprozess* bereit. Das aktuelle Service Fabric-Hostingmodell ermöglicht es, mehrere Dienstinstanzen und/oder Replikate im gleichen Prozess zu hosten. Das bedeutet, dass sich jede dieser Ressourcen den gleichen Port gemeinsam verwendet, wenn sie über die `Endpoint`-Konfiguration zugewiesen wird. Mehrere **HTTP.sys** Instanzen können einen Port gemeinsam verwenden, indem Sie das zugrunde liegende **HTTP.sys**-Portfreigabefeature verwenden. Aber dies wird von `HttpSysCommunicationListener` nicht unterstützt, da es zu Komplikationen bei Clientanforderungen führt. Für die Verwendung eines dynamischen Ports wird Kestrel als Webserver empfohlen.

## <a name="kestrel-in-reliable-services"></a>Kestrel in zuverlässigen Diensten
Sie können Kestrel in Reliable Services durch Importieren des NuGet-Pakets **Microsoft.ServiceFabric.AspNetCore.Kestrel** verwenden. Dieses Paket enthält `KestrelCommunicationListener`, eine Implementierung von `ICommunicationListener`. `KestrelCommunicationListener` ermöglicht das Erstellen eines ASP.NET Core-Webhosts innerhalb eines zuverlässigen Diensts mit Kestrel als Webserver.

Kestrel ist ein plattformübergreifender Webserver für ASP.NET Core. Im Gegensatz zu „HTTP.sys“ verwendet Kestrel keinen zentralen Endpunkt-Manager. Und im Gegensatz zu „HTTP.sys“ unterstützt Kestrel keine Portfreigabe zwischen mehreren Prozessen. Jede Instanz von Kestrel muss einen eindeutigen Port verwenden. Weitere Informationen zu Kestrel finden Sie in den [Implementierungsdetails ](/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-2.2).

![Kestrel-Abbildung][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel in einem zustandslosen Dienst
Wenn Sie `Kestrel` in einem zustandslosen Dienst verwenden möchten, müssen Sie die `CreateServiceInstanceListeners`-Methode überschreiben und eine `KestrelCommunicationListener`-Instanz zurückgeben:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>Kestrel in einem zustandsbehafteten Dienst
Wenn Sie `Kestrel` in einem zustandsbehafteten Dienst verwenden möchten, müssen Sie die `CreateServiceReplicaListeners`-Methode überschreiben und eine `KestrelCommunicationListener`-Instanz zurückgeben:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

In diesem Beispiel wird eine Singletoninstanz von `IReliableStateManager` für den Webhost-Abhängigkeitsinjektionscontainer bereitgestellt. Das ist zwar nicht zwingend erforderlich, ermöglicht aber die Verwendung von `IReliableStateManager` und zuverlässigen Sammlungen in Ihren MVC-Controlleraktionsmethoden.

In einem zustandsbehafteten Dienst für `KestrelCommunicationListener` wird *kein*`Endpoint`-Konfigurationsname bereitgestellt. Dies wird im folgenden Abschnitt ausführlicher erläutert.

### <a name="configure-kestrel-to-use-https"></a>Konfigurieren von Kestrel für die Verwendung von HTTPS
Wenn Sie HTTPS mit Kestrel in Ihrem Dienst aktivieren, müssen Sie mehrere Optionen zum Lauschen festlegen. Aktualisieren Sie `ServiceInstanceListener`, um einen *EndpointHttps*-Endpunkt zu verwenden und an einen bestimmten Port (z.B. Port 443) zu lauschen. Wenn Sie den Webhost für die Verwendung des Kestrel-Webservers konfigurieren, müssen Sie Kestrel so konfigurieren, dass an allen Netzwerkschnittstellen auf IPv6-Adressen gelauscht wird: 

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

Ein vollständiges Beispiel in einem Tutorial finden Sie unter [Konfigurieren von Kestrel für die Verwendung von HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Endpunktkonfiguration
Eine `Endpoint`-Konfiguration ist für die Verwendung von Kestrel nicht erforderlich. 

Kestrel ist ein einfacher eigenständiger Webserver. Im Gegensatz zu „HTTP.sys“ (oder HttpListener) benötigt er keine `Endpoint`-Konfiguration in „ServiceManifest.xml“, da er vor dem Start keine URL-Registrierung benötigt. 

#### <a name="use-kestrel-with-a-static-port"></a>Verwenden von Kestrel mit einem statischen Port
Sie können einen statischen Port in der `Endpoint`-Konfiguration von „ServiceManifest.xml“ für die Verwendung mit Kestrel konfigurieren. Das ist zwar nicht unbedingt nötig, bietet jedoch zwei potenzielle Vorteile:
 - Wenn der Port nicht im Anwendungsportbereich liegt, wird er von Service Fabric durch die Betriebssystemfirewall geöffnet.
 - Die durch `KestrelCommunicationListener` bereitgestellte URL verwendet diesen Port.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Wenn ein `Endpoint`-Element konfiguriert ist, muss dessen Name an den `KestrelCommunicationListener`-Konstruktor übergeben werden: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Wenn „ServiceManifest.xml“ keine `Endpoint`-Konfiguration verwendet, lassen Sie den Namen im `KestrelCommunicationListener`-Konstruktor weg. In diesem Fall wird ein dynamischer Port verwendet. Weitere Informationen dazu finden Sie im nächsten Abschnitt.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Verwenden von Kestrel mit einem dynamischen Port
Kestrel kann die automatische Portzuweisung aus der `Endpoint`-Konfiguration in „ServiceManifest.xml“ nicht verwenden. Dies liegt daran, dass die automatische Portzuweisung aus einer `Endpoint`-Konfiguration einen eindeutigen Port pro *Hostprozess* zuweist und ein einzelner Hostprozess mehrere Kestrel-Instanzen enthalten kann. Dies funktioniert nicht mit Kestrel, da dieser Server keine Portfreigabe unterstützt. Aus diesem Grund muss jede Kestrel-Instanz für einen eindeutigen Port geöffnet werden.

Wenn Sie die dynamische Portzuweisung mit Kestrel verwenden möchten, lassen Sie die `Endpoint`-Konfiguration in „ServiceManifest.xml“ vollständig weg, und übergeben Sie keinen Endpunktnamen an den `KestrelCommunicationListener`-Konstruktor:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

In dieser Konfiguration wählt `KestrelCommunicationListener` automatisch einen nicht verwendeten Port aus dem Anwendungsportbereich aus.

Für HTTPS sollte der Endpunkt mit dem HTTPS-Protokoll ohne einen in „ServiceManifest.xml“ angegebenen Port konfiguriert sein und den Endpunktnamen an den KestrelCommunicationListener-Konstruktor übergeben.


## <a name="service-fabric-configuration-provider"></a>Service Fabric-Konfigurationsanbieter
Die App-Konfiguration in ASP.NET Core basiert auf Schlüssel-Wert-Paaren, die vom Konfigurationsanbieter eingerichtet werden. Lesen Sie [Konfiguration in ASP.NET Core](/aspnet/core/fundamentals/configuration/), um mehr über die allgemeine Unterstützung der ASP.NET Core-Konfiguration zu erfahren.

In diesem Abschnitt wird beschrieben, wie sich der Service Fabric-Konfigurationsanbieter in die ASP.NET Core-Konfiguration integriert, indem er das NuGet-Paket `Microsoft.ServiceFabric.AspNetCore.Configuration` importiert.

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricConfiguration-Starterweiterungen
Nachdem Sie das NuGet-Paket `Microsoft.ServiceFabric.AspNetCore.Configuration` importiert haben, müssen Sie die Service Fabric-Konfigurationsquelle bei der ASP.NET Core-Konfigurations-API registrieren. Überprüfen Sie dazu die **AddServiceFabricConfiguration**-Erweiterungen im `Microsoft.ServiceFabric.AspNetCore.Configuration`-Namespace im Vergleich zu `IConfigurationBuilder`.

```csharp
using Microsoft.ServiceFabric.AspNetCore.Configuration;

public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(env.ContentRootPath)
        .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
        .AddServiceFabricConfiguration() // Add Service Fabric configuration settings.
        .AddEnvironmentVariables();
    Configuration = builder.Build();
}

public IConfigurationRoot Configuration { get; }
```

Nun kann der ASP.NET Core-Dienst ganz normal auf die Service Fabric-Konfigurationseinstellungen zugreifen. Sie können beispielsweise das Optionsmuster verwenden, um Einstellungen in stark typisierte Objekte zu laden.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Standardschlüsselzuordnung
Standardmäßig enthält der Service Fabric-Konfigurationsanbieter den Paketnamen, den Abschnittsnamen und den Eigenschaftsnamen. Zusammen bilden diese Angaben den ASP.NET Core-Konfigurationsschlüssel:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Wenn Sie also beispielsweise über ein Konfigurationspaket namens `MyConfigPackage` mit dem folgenden Inhalt verfügen, steht der Konfigurationswert in `IConfiguration` von ASP.NET Core über *MyConfigPackage:MyConfigSection:MyParameter* zur Verfügung.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Service Fabric-Konfigurationsoptionen
Der Service Fabric-Konfigurationsanbieter unterstützt auch `ServiceFabricConfigurationOptions` zum Ändern des Standardverhaltens der Schlüsselzuordnung.

#### <a name="encrypted-settings"></a>Verschlüsselte Einstellungen
Service Fabric unterstützt verschlüsselte Einstellungen, ebenso wie der Service Fabric-Konfigurationsanbieter. Die verschlüsselten Einstellungen werden nicht standardmäßig in `IConfiguration` von ASP.NET Core entschlüsselt. Die verschlüsselten Werte werden dort stattdessen gespeichert. Wenn Sie den Wert entschlüsseln möchten, um ihn in IConfiguration von ASP.NET Core zu speichern, können Sie das *DecryptValue*-Flag in der `AddServiceFabricConfiguration`-Erweiterung wie folgt auf FALSE festlegen:

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = false); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>Mehrere Konfigurationspakete
Service Fabric unterstützt mehrere Konfigurationspakete. Der Paketname ist standardmäßig im Konfigurationsschlüssel enthalten. Sie können jedoch das `IncludePackageName`-Flag wie folgt auf FALSE festlegen:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        // exclude package name from key.
        .AddServiceFabricConfiguration(activationContext, (options) => options.IncludePackageName = false); 
    Configuration = builder.Build();
}
```
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Benutzerdefinierte Schlüsselzuordnung, Wertextrahierung und Datenauffüllung
Der Service Fabric-Konfigurationsanbieter unterstützt auch komplexere Szenarien zur Anpassung der Schlüsselzuordnung mit `ExtractKeyFunc` sowie zur benutzerdefinierten Extrahierung der Werte mit `ExtractValueFunc`. Mithilfe von `ConfigAction` können Sie bei Bedarf sogar den gesamten Prozess zum Auffüllen von Daten aus der Service Fabric-Konfiguration in der ASP.NET Core-Konfiguration ändern.

Die folgenden Beispiele veranschaulichen die Anpassung der Datenauffüllung unter Verwendung von `ConfigAction`:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    
    this.valueCount = 0;
    this.sectionCount = 0;
    var builder = new ConfigurationBuilder();
    builder.AddServiceFabricConfiguration(activationContext, (options) =>
        {
            options.ConfigAction = (package, configData) =>
            {
                ILogger logger = new ConsoleLogger("Test", null, false);
                logger.LogInformation($"Config Update for package {package.Path} started");

                foreach (var section in package.Settings.Sections)
                {
                    this.sectionCount++;

                    foreach (var param in section.Parameters)
                    {
                        configData[options.ExtractKeyFunc(section, param)] = options.ExtractValueFunc(section, param);
                        this.valueCount++;
                    }
                }

                logger.LogInformation($"Config Update for package {package.Path} finished");
            };
        });
  Configuration = builder.Build();
}
```

### <a name="configuration-updates"></a>Konfigurationsupdates
Der Service Fabric-Konfigurationsanbieter unterstützt auch Konfigurationsupdates. Sie können `IOptionsMonitor` von ASP.NET Core verwenden, um Änderungsbenachrichtigungen zu erhalten, und dann `IOptionsSnapshot` verwenden, um Konfigurationsdaten erneut zu laden. Weitere Informationen finden Sie unter [ASP.NET Core-Optionen](/aspnet/core/fundamentals/configuration/options).

Diese Optionen werden standardmäßig unterstützt. Es ist keine weitere Codierung erforderlich, um Konfigurationsupdates zu aktivieren.

## <a name="scenarios-and-configurations"></a>Szenarien und Konfigurationen
In diesem Abschnitt finden Sie die Kombination aus Webserver, Portkonfiguration, Service Fabric-Integrationsoptionen und verschiedenen Einstellungen, die wir zur Problembehandlung in den folgenden Szenarien empfehlen:
 - Zustandslose ASP.NET Core-Dienste (extern verfügbar gemacht)
 - Zustandsloser ASP.NET Core-Dienst (nur intern)
 - Zustandsbehafteter ASP.NET Core-Dienst (nur intern)

Ein **extern verfügbar gemachter** Dienst stellt einen Endpunkt bereit, der von außerhalb des Clusters aufgerufen werden kann (üblicherweise von einem Load Balancer).

Bei einem **rein internen** Dienst kann der Endpunkt nur innerhalb des Clusters aufgerufen werden.

> [!NOTE]
> Zustandsbehaftete Dienstendpunkte sollten grundsätzlich nicht für das Internet verfügbar gemacht werden. Cluster hinter Load Balancern, die keine Kenntnis der Service Fabric-Dienstauflösung haben (z.B. Azure Load Balancer), können keine zustandsbehafteten Dienste bereitstellen. Das liegt daran, dass der Load Balancer nicht in der Lage ist, den Datenverkehr zu ermitteln und an das entsprechende zustandsbehaftete Dienstreplikat weiterzuleiten. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Zustandslose ASP.NET Core-Dienste (extern verfügbar gemacht)
Kestrel ist der empfohlene Webserver für Front-End-Dienste, die externe HTTP-Endpunkte mit Internetzugriff bereitstellen. Unter Windows kann „HTTP.sys“ die Portfreigabefunktion bereitstellen, sodass Sie mehrere Webdienste auf derselben Gruppe von Knoten mit dem gleichen Port hosten können. In diesem Szenario werden die Webdienste nach Hostname oder Pfad unterschieden, ohne dass sie ein Front-End-Proxy oder ein Gateway verwenden müssen, um HTTP-Routing bereitzustellen.
 
Ein für das Internet verfügbar gemachter zustandsloser Dienst muss einen bekannten und stabilen Endpunkt verwenden, der über einen Load Balancer erreichbar ist. Sie müssen diese URL für die Benutzer Ihrer Anwendung bereitstellen. Folgende Konfigurationen werden empfohlen:

| type | Empfehlung | Notizen |
| ---- | -------------- | ----- |
| Webserver | Kestrel | Kestrel ist der bevorzugte Webserver, da er unter Windows und Linux unterstützt wird. |
| Portkonfiguration | Statisch | In der `Endpoints`-Konfiguration von „ServiceManifest.xml“ muss ein bekannter statischer Port konfiguriert werden – beispielsweise 80 für HTTP oder 443 für HTTPS. |
| ServiceFabricIntegrationOptions | Keine | Verwenden Sie die Option `ServiceFabricIntegrationOptions.None`, wenn Sie Middleware für die Service Fabric-Integration konfigurieren, damit der Dienst nicht versucht, eingehende Anforderungen auf einen eindeutigen Bezeichner zu prüfen. Die eindeutig identifizierbaren Informationen, die von der Middleware verwendet werden, sind externen Benutzern Ihrer Anwendung nicht bekannt. |
| Anzahl der Instanzen | -1 | In typischen Anwendungsfällen sollte die Einstellung für die Instanzenanzahl auf *-1* festgelegt werden. Dies geschieht, damit auf allen Knoten, die Datenverkehr von einem Load Balancer empfangen, eine Instanz verfügbar ist. |

Wenn sich mehrere extern verfügbar gemachte Dienste die gleichen Knoten teilen, können Sie „HTTP.sys“ mit einem eindeutigen, aber stabilen URL-Pfad verwenden. Hierzu kann die URL geändert werden, die beim Konfigurieren von IWebHost angegeben wurde. Beachten Sie, dass dies nur für „HTTP.sys“ gilt.

 ```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseHttpSys()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>Zustandsloser ASP.NET Core-Dienst (nur intern)
Zustandslose Dienste, die nur innerhalb des Clusters aufgerufen werden, sollten eindeutige URLs und dynamisch zugewiesene Ports verwenden, um die Zusammenarbeit zwischen mehreren Diensten zu gewährleisten. Folgende Konfigurationen werden empfohlen:

| type | Empfehlung | Notizen |
| ---- | -------------- | ----- |
| Webserver | Kestrel | Für interne zustandslose Dienste kann „HTTP.sys“ zwar verwendet werden, als Server wird jedoch Kestrel empfohlen, um die gemeinsame Verwendung eines Hosts durch mehrere Dienstinstanzen zu ermöglichen.  |
| Portkonfiguration | Dynamisch zugewiesen | Mehrere Replikate eines zustandsbehafteten Diensts können gemeinsam einen Hostprozess oder ein Hostbetriebssystem verwenden und benötigen daher eindeutige Ports. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Bei dynamischer Portzuweisung verhindert diese Einstellung das weiter oben beschriebene Verwechslungsproblem. |
| InstanceCount | any | Die Einstellung für die Instanzenanzahl kann auf einen beliebigen Wert festgelegt werden, der für den Betrieb des Diensts erforderlich ist. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Zustandsbehafteter ASP.NET Core-Dienst (nur intern)
Zustandsbehaftete Dienste, die nur innerhalb des Clusters aufgerufen werden, sollten dynamisch zugewiesene Ports verwenden, um die Zusammenarbeit zwischen mehreren Diensten zu gewährleisten. Folgende Konfigurationen werden empfohlen:

| type | Empfehlung | Notizen |
| ---- | -------------- | ----- |
| Webserver | Kestrel | `HttpSysCommunicationListener` ist nicht für die Verwendung durch zustandsbehaftete Dienste konzipiert, in denen sich Replikate einen Hostprozess teilen. |
| Portkonfiguration | Dynamisch zugewiesen | Mehrere Replikate eines zustandsbehafteten Diensts können gemeinsam einen Hostprozess oder ein Hostbetriebssystem verwenden und benötigen daher eindeutige Ports. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Bei dynamischer Portzuweisung verhindert diese Einstellung das weiter oben beschriebene Verwechslungsproblem. |

## <a name="next-steps"></a>Nächste Schritte
[Debuggen der Service Fabric-Anwendung mithilfe von Visual Studio](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png

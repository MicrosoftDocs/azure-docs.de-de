---
title: 'Gewusst wie: Skalieren mit mehreren Instanzen für Azure SignalR Service'
description: In vielen Skalierungsszenarien muss der Kunde häufig mehrere Instanzen bereitstellen und für die gemeinsame Nutzung konfigurieren, um eine umfangreiche Bereitstellung zu erzielen. Beispielsweise wird für das Sharding die Unterstützung mehreren Instanzen benötigt.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: e284a0492774e02cab79db6d9006c1718a7fcfc9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60809247"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Skalieren von SignalR Service mit mehreren Instanzen
Das neueste SignalR Service SDK unterstützt mehrere Endpunkte für SignalR Service-Instanzen. Sie können dieses Feature zum Skalieren der gleichzeitigen Verbindungen oder für das regionsübergreifende Messaging verwenden.

## <a name="for-aspnet-core"></a>Für ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>Hinzufügen mehrerer Endpunkte über die Config-Datei

Verwenden Sie eine Config-Datei mit dem Schlüssel `Azure:SignalR:ConnectionString` oder `Azure:SignalR:ConnectionString:` als SignalR Service-Verbindungszeichenfolge.

Wenn der Schlüssel mit `Azure:SignalR:ConnectionString:` beginnt, sollte er das Format `Azure:SignalR:ConnectionString:{Name}:{EndpointType}` aufweisen. Hierbei sind `Name` und `EndpointType` Eigenschaften des `ServiceEndpoint`-Objekts, die über den Code zugänglich sind.

Sie können Verbindungszeichenfolgen für mehrere Instanzen hinzufügen, indem Sie die folgenden `dotnet`-Befehle verwenden:

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Hinzufügen mehrerer Endpunkte über den Code

Es wurde eine `ServicEndpoint`-Klasse eingeführt, um die Eigenschaften eines Azure SignalR Service-Endpunkts zu beschreiben.
Sie können mehrere Instanzen für Endpunkte konfigurieren, wenn Sie das Azure SignalR Service SDK verwenden:
```cs
services.AddSignalR()
        .AddAzureSignalR(options => 
        {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString0>"),
                new ServiceEndpoint("<ConnectionString1>", type: EndpointType.Primary, name: "east-region-a"),
                new ServiceEndpoint("<ConnectionString2>", type: EndpointType.Primary, name: "east-region-b"),
                new ServiceEndpoint("<ConnectionString3>", type: EndpointType.Secondary, name: "backup"),
            };
        });
```

### <a name="how-to-customize-endpoint-router"></a>Anpassen des Endpunktrouters

Standardmäßig wird vom SDK das [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs)-Element verwendet, um Endpunkte zu erfassen.

#### <a name="default-behavior"></a>Standardverhalten 
1. Routing von Clientanforderungen

    Wird verwendet, wenn Clients die Aushandlung (`/negotiate`) mit dem App-Server durchführen. Standardmäßig nimmt das SDK eine **zufällige Auswahl** eines Endpunkts aus den verfügbaren Dienstendpunkten vor.

2. Routing von Servernachrichten

    Wenn eine Nachricht an eine bestimmte „Verbindung“ gesendet und die Zielverbindung an den aktuellen Server geleitet wird, geht die Nachricht direkt an diesen verbundenen Endpunkt. Andernfalls werden die Nachrichten an jeden Azure SignalR-Endpunkt übertragen.

#### <a name="customize-routing-algorithm"></a>Anpassen des Routingalgorithmus
Sie können Ihre eigenen Router erstellen, wenn Sie über spezielle Kenntnisse verfügen und ermitteln können, an welche Endpunkte die Nachrichten gesendet werden sollen.

Unten im Beispiel wird ein benutzerdefinierter Router definiert, in dem Gruppen, die mit `east-` beginnen, immer an den Endpunkt mit dem Namen `east` geleitet werden:

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override IEnumerable<ServiceEndpoint> GetEndpointsForGroup(string groupName, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the group broadcast behavior, if the group name starts with "east-", only send messages to endpoints inside east
        if (groupName.StartsWith("east-"))
        {
            return endpoints.Where(e => e.Name.StartsWith("east-"));
        }

        return base.GetEndpointsForGroup(groupName, endpoints);
    }
}
```

Unten ist noch ein weiteres Beispiel angegeben, in dem das Standardverhalten für die Aushandlung außer Kraft gesetzt wird. Die Auswahl der Endpunkte hängt davon ab, wo sich der App-Server befindet.

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(HttpContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (endpointName.Count == 0)
        {
            context.Response.StatusCode = 400;
            var response = Encoding.UTF8.GetBytes("Invalid request");
            context.Response.Body.Write(response, 0, response.Length);
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Vergessen Sie nicht, den Router wie folgt für den DI-Container zu registrieren:

```cs
services.AddSingleton(typeof(IEndpointRouter), typeof(CustomRouter));
services.AddSignalR()
        .AddAzureSignalR(
            options => 
            {
                options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
            });
```

## <a name="for-aspnet"></a>Für ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>Hinzufügen mehrerer Endpunkte über die Config-Datei

Verwenden Sie eine Config-Datei mit dem Schlüssel `Azure:SignalR:ConnectionString` oder `Azure:SignalR:ConnectionString:` als SignalR Service-Verbindungszeichenfolge.

Wenn der Schlüssel mit `Azure:SignalR:ConnectionString:` beginnt, sollte er das Format `Azure:SignalR:ConnectionString:{Name}:{EndpointType}` aufweisen. Hierbei sind `Name` und `EndpointType` Eigenschaften des `ServiceEndpoint`-Objekts, die über den Code zugänglich sind.

Sie können `web.config` Verbindungszeichenfolgen für mehrere Instanzen hinzufügen:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <connectionStrings>
    <add name="Azure:SignalR:ConnectionString" connectionString="<ConnectionString1>"/>
    <add name="Azure:SignalR:ConnectionString:en-us" connectionString="<ConnectionString2>"/>
    <add name="Azure:SignalR:ConnectionString:zh-cn:secondary" connectionString="<ConnectionString3>"/>
    <add name="Azure:SignalR:ConnectionString:Backup:secondary" connectionString="<ConnectionString4>"/>
  </connectionStrings>
  ...
</configuration>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Hinzufügen mehrerer Endpunkte über den Code

Es wurde eine `ServicEndpoint`-Klasse eingeführt, um die Eigenschaften eines Azure SignalR Service-Endpunkts zu beschreiben.
Sie können mehrere Instanzen für Endpunkte konfigurieren, wenn Sie das Azure SignalR Service SDK verwenden:

```cs
app.MapAzureSignalR(
    this.GetType().FullName, 
    options => {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString1>"),
                new ServiceEndpoint("<ConnectionString2>"),
                new ServiceEndpoint("<ConnectionString3>"),
            }
        });
```

### <a name="how-to-customize-router"></a>Anpassen des Routers

Der einzige Unterschied zwischen ASP.NET SignalR und ASP.NET Core SignalR ist der HTTP-Kontexttyp für `GetNegotiateEndpoint`. Für ASP.NET SignalR lautet der Typ [IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19).

Unten ist ein Beispiel für die benutzerdefinierte Aushandlung für ASP.NET SignalR angegeben:

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(IOwinContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (string.IsNullOrEmpty(endpointName))
        {
            context.Response.StatusCode = 400;
            context.Response.Write("Invalid request.");
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Vergessen Sie nicht, den Router wie folgt für den DI-Container zu registrieren:

```cs
var hub = new HubConfiguration();
var router = new CustomRouter();
hub.Resolver.Register(typeof(IEndpointRouter), () => router);
app.MapAzureSignalR(GetType().FullName, hub, options => {
    options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
});
```

## <a name="configuration-in-cross-region-scenarios"></a>Konfiguration in regionsübergreifenden Szenarien

Das `ServiceEndpoint`-Objekt verfügt über eine `EndpointType`-Eigenschaft mit dem Wert `primary` oder `secondary`.

Endpunkte vom Typ `primary` werden als Endpunkte zum Empfangen von Clientdatenverkehr bevorzugt und verfügen meist über zuverlässigere Netzwerkverbindungen. Endpunkte vom Typ `secondary` verfügen meist über weniger zuverlässige Netzwerkverbindungen und werden nur für Datenverkehr vom Server zum Client verwendet, z. B. Broadcastmeldungen. Für Datenverkehr vom Client zum Server werden sie dagegen nicht genutzt.

In Fällen mit regionsübergreifender Nutzung kann das Netzwerk instabil sein. Für einen App-Server in der Region *USA, Osten* kann der SignalR Service-Endpunkt in derselben Region (also ebenfalls *USA, Osten*) als `primary` und die Endpunkte in anderen Regionen als `secondary` konfiguriert werden. Bei dieser Konfiguration können Dienstendpunkte in anderen Regionen Nachrichten von diesem App-Server in der Region *USA, Osten* **empfangen**, aber es werden keine **regionsübergreifenden** Clients an diesen App-Server geleitet. Die Architektur ist in diesem Diagramm dargestellt:

![Regionsübergreifende Infrastruktur](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

Wenn ein Client versucht, per `/negotiate` die Aushandlung mit dem App-Server über den Standardrouter durchzuführen, wird vom SDK ein Endpunkt aus den verfügbaren Endpunkten vom Typ `primary` **zufällig ausgewählt**. Wenn der Endpunkt verfügbar ist, führt das SDK dann eine **zufällige Auswahl** aus allen verfügbaren Endpunkten vom Typ `secondary` durch. Der Endpunkt wird als **verfügbar** gekennzeichnet, wenn die Verbindung zwischen Server und Dienstendpunkt aktiv ist.

Wenn ein Client in einem regionsübergreifenden Szenario versucht, per `/negotiate` die Aushandlung mit dem App-Server durchzuführen, der in *USA, Osten* gehostet wird, wird standardmäßig immer der Endpunkt vom Typ `primary` zurückgegeben, der sich in derselben Region befindet. Falls alle Endpunkte der Region *USA, Osten* nicht verfügbar sind, wird der Client an die Endpunkte in anderen Regionen umgeleitet. Unten im Abschnitt „Failover“ ist das Szenario ausführlich beschrieben.

![Normale Aushandlung](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Failover

Falls alle Endpunkte vom Typ `primary` nicht verfügbar sind, wird für den Client per `/negotiate` eine Auswahl aus den Endpunkten vom Typ `secondary` getroffen. Für diesen Failovermechanismus ist es erforderlich, dass jeder Endpunkt für mindestens einen App-Server als Endpunkt vom Typ `primary` dient.

![Failover](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Leitfaden wurde beschrieben, wie Sie in derselben Anwendung mehrere Instanzen für die Skalierung, das Sharding und regionsübergreifende Szenarien konfigurieren.

Die Unterstützung mehrerer Endpunkte kann auch in Szenarien für Hochverfügbarkeit und Notfallwiederherstellung genutzt werden.

> [!div class="nextstepaction"]
> [Resilienz und Notfallwiederherstellung](./signalr-concept-disaster-recovery.md)

---
title: Problembehandlungsleitfaden für Azure SignalR Service
description: Informieren Sie sich über das Beheben häufig auftretender Probleme.
author: YanJin
ms.service: signalr
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: yajin1
ms.openlocfilehash: cc17dcef7a554bee2715c79ba7d0c2356db2c6b3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185656"
---
# <a name="troubleshooting-guide-for-azure-signalr-service-common-issues"></a>Problembehandlungsleitfaden für häufig bei Azure SignalR Service auftretende Probleme

Dieser Leitfaden hilft Ihnen beim Beheben von häufig auftretenden Problemen, die Kunden in den letzten Jahren gemeldet und gelöst haben.

## <a name="access-token-too-long"></a>Zugriffstoken zu lang

### <a name="possible-errors"></a>Mögliche Fehler:

* `ERR_CONNECTION_` auf Clientseite
* 414 – URI zu lang
* 413 Nutzlast zu groß
* Das Zugriffstoken darf nicht länger als 4 K sein. 413 – Anforderungsentität zu groß

### <a name="root-cause"></a>Grundursache:

Bei HTTP/2 beträgt die Höchstlänge für einen einzelnen Header **4 K**, wenn Sie daher mit einem Browser auf den Azure-Dienst zugreifen, tritt aufgrund dieser Begrenzung ein `ERR_CONNECTION_`-Fehler auf.

Bei HTTP/1.1 und C#-Clients beträgt die Höchstlänge von URIs **12 K**, und die Höchstlänge für Header ist **16 K**.

Ab der SDK-Version **1.0.6** löst `/negotiate` den Fehler `413 Payload Too Large` aus, wenn das generierte Zugriffstoken größer als **4 K** ist.

### <a name="solution"></a>Projektmappe:

Standardmäßig werden Ansprüche von `context.User.Claims` beim Generieren des JWT-Zugriffstokens für **ASRS**(**A** zure **S** ignal **R** **S** ervice) eingeschlossen, sodass die Ansprüche beibehalten werden und von **ASRS** an den `Hub` übergeben werden können, wenn der Client eine Verbindung mit dem `Hub` herstellt.

In einigen Fällen werden `context.User.Claims` genutzt, um große Informationsmengen für App-Server zu speichern, von denen die meisten nicht von `Hub`s, sondern von anderen Komponenten verwendet werden.

Das generierte Zugriffstoken wird über das Netzwerk übermittelt, und bei WebSocket-/SSE-Verbindungen werden Zugriffstoken über Abfragezeichenfolgen übermittelt. Als bewährte Methode wird empfohlen, nur **erforderliche** Ansprüche vom Client über **ASRS** an Ihren App-Server zu übergeben, sofern der Hub dies verlangt.

Es wird ein `ClaimsProvider` bereitgestellt, mit dem Sie die Ansprüche anpassen können, die im Zugriffstoken an **ASRS** übermittelt werden.

Für ASP.NET Core:
```cs
services.AddSignalR()
        .AddAzureSignalR(options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context => context.User.Claims.Where(...);
            });
```

Für ASP.NET:
```cs
services.MapAzureSignalR(GetType().FullName, options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context.Authentication?.User.Claims.Where(...);
            });
```

## <a name="tls-12-required"></a>TLS 1.2 erforderlich

### <a name="possible-errors"></a>Mögliche Fehler:

* ASP.NET-Fehler „Kein Server verfügbar“[#279](https://github.com/Azure/azure-signalr/issues/279)
* ASP.NET-Fehler „Die Verbindung ist nicht aktiv. Es können keine Daten an den Dienst gesendet werden.“ [#324](https://github.com/Azure/azure-signalr/issues/324)
* „Fehler beim Erstellen der HTTP-Anforderung an https://<API endpoint>. Dies könnte daran liegen, dass das Serverzertifikat nicht richtig mit „HTTP.SYS“ im HTTPS-Fall konfiguriert wurde. Eine andere mögliche Ursache kann eine fehlende Übereinstimmung bei der Sicherheitsbindung zwischen Client und Server sein.“

### <a name="root-cause"></a>Grundursache:

Der Azure-Dienst unterstützt aus Sicherheitsgründen nur TLS 1.2. Bei .NET Framework ist es möglich, dass TLS 1.2 nicht das Standardprotokoll ist. Daher können keine Serververbindungen mit ASRS erfolgreich hergestellt werden.

### <a name="troubleshooting-guide"></a>Handbuch zur Problembehandlung

1. Wenn dieser Fehler lokal reproduziert werden kann, deaktivieren Sie *Nur eigenen Code*, lösen Sie alle CLR-Ausnahmen aus, und debuggen Sie den Anwendungsserver lokal, um zu ermitteln, welche Ausnahme ausgelöst wird.
    * Deaktivieren Sie *Nur eigenen Code*:

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/uncheck-just-my-code.png" alt-text="Deaktivieren Sie „Nur eigenen Code“.":::

    * Lösen Sie CLR-Ausnahmen aus:

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/throw-clr-exceptions.png" alt-text="Lösen Sie CLR-Ausnahmen aus.":::

    * Überprüfen Sie, welche Ausnahmen beim Debuggen des serverseitigen App-Codes ausgelöst werden:
        
        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/tls-throws.png" alt-text="Ausgelöste Ausnahme":::

2. Bei ASP.NET-Dateien können Sie auch folgenden Code der Datei `Startup.cs` hinzufügen, um eine ausführliche Ablaufverfolgung zu aktivieren und die Fehler aus dem Protokoll anzuzeigen.
```cs
app.MapAzureSignalR(this.GetType().FullName);
// Make sure this switch is called after MapAzureSignalR
GlobalHost.TraceManager.Switch.Level = SourceLevels.Information;
```

### <a name="solution"></a>Projektmappe:

Fügen Sie folgenden Code zu „Startup.cs“ hinzu:
```cs
ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12;
```

## <a name="400-bad-request-returned-for-client-requests"></a>Rückgabe von „400 – Ungültige Anforderung“ für Clientanforderungen

### <a name="root-cause"></a>Grundursache

Überprüfen Sie, ob Ihre Clientanforderung mehrere `hub`-Abfragezeichenfolgen enthält. `hub` ist ein persistenter Abfrageparameter, und Fehler 400 wird ausgelöst, wenn der Dienst mehr als einen `hub`-Parameter in der Abfrage erkennt.

## <a name="401-unauthorized-returned-for-client-requests"></a>Rückgabe von „401 – Nicht autorisiert“ für Clientanforderungen

### <a name="root-cause"></a>Grundursache

Der Standardwert für die Lebensdauer von JWT-Token beträgt derzeit eine Stunde.

Dies ist kein Problem, wenn für ASP.NET Core SignalR der WebSocket-Transporttyp verwendet wird.

Bei anderen Transporttypen für ASP.NET Core SignalR und langen Abrufintervallen bedeutet dies, dass die Verbindung standardmäßig höchstens eine Stunde lang aufrechterhalten wird.

Bei ASP.NET SignalR sendet der Client gelegentlich eine `/ping`-KeepAlive-Anforderung an den Dienst. Wenn `/ping` einen Fehler zurückgibt, wird die Verbindung vom Client **abgebrochen** und auch nicht wiederhergestellt. Dies bedeutet, dass bei ASP.NET SignalR die Verbindung bei allen Transporttypen aufgrund der standardmäßigen Tokengültigkeitsdauer **höchstens** eine Stunde lang aufrechterhalten wird.

### <a name="solution"></a>Lösung

Aus Sicherheitsgründen wird ein Verlängern der Gültigkeitsdauer nicht empfohlen. Sie sollten Logik zum Wiederherstellen der Verbindung durch den Client hinzufügen, um die Verbindung bei Fehler 401 wiederherzustellen. Wenn der Client die Verbindung wiederherstellt, wird das JWT-Token bei einer Aushandlung mit dem App-Server nochmals abgerufen, und er empfängt ein verlängertes Token.

[Hier](#restart_connection) erfahren Sie, wie Sie Clientverbindungen wiederherstellen.

## <a name="404-returned-for-client-requests"></a>Rückgabe von „404“ für Clientanforderungen

Bei einer dauerhaften SignalR-Verbindung wird zunächst `/negotiate` für den Azure SignalR Service ausgeführt, und anschließend wird die eigentliche Verbindung mit dem Azure SignalR Service hergestellt.

### <a name="troubleshooting-guide"></a>Handbuch zur Problembehandlung

* Folgen Sie der Anleitung unter [Anzeigen ausgehender Anforderungen vom Client](#view_request), um die Anforderung des Clients an den Dienst abzurufen.
* Überprüfen Sie die URL der Anforderung, wenn Fehler 404 auftritt. Wenn die URL auf Ihre Web-App verweist und ähnlich wie `{your_web_app}/hubs/{hubName}` lautet, überprüfen Sie, ob der Clientparameter `SkipNegotiation` `true` ist. Bei Verwendung von Azure SignalR erhält der Client eine Umleitungs-URL, wenn er zum ersten Mal eine Aushandlung mit dem App-Server ausführt. Der Client sollte die Aushandlung bei Verwendung von Azure SignalR **NICHT** überspringen.
* Ein weiterer Fehler 404 kann auftreten, wenn die Verbindungsanforderung mehr als **5** Sekunden nach dem Aufruf von `/negotiate` verarbeitet wird. Überprüfen Sie den Zeitstempel der Clientanforderung, und öffnen Sie ein Issue bei einer langsamen Reaktion auf die Anforderung an den Dienst.

## <a name="404-returned-for-aspnet-signalrs-reconnect-request"></a>Rückgabe des Fehlers 404 bei einer ASP.NET SignalR-Anforderung zum Wiederherstellen der Verbindung

Wenn bei ASP.NET SignalR die [Clientverbindung getrennt](#client_connection_drop) wird, wird dreimal die gleiche `connectionId` zum Wiederherstellen der Verbindung verwendet, bevor die Verbindung beendet wird. `/reconnect` kann nützlich sein, wenn die Verbindung aufgrund von vorübergehenden Netzwerkproblemen unterbrochen wird, sodass die permanente Verbindung erfolgreich mit `/reconnect` wiederhergestellt werden kann. Unter anderen Umständen wird die Clientverbindung z. B. getrennt, weil die Verbindung mit dem Routingserver getrennt wurde oder beim SignalR Service interne Fehler wie Neustart, Failover oder Bereitstellung der Instanz aufgetreten sind und die Verbindung daher nicht mehr besteht, sodass `/reconnect` `404` zurückgibt. Dabei handelt es sich um das erwartete Verhalten von `/reconnect`. Nach drei Wiederholungsversuchen wird die Verbindung beendet. Es wird empfohlen, für den Fall einer getrennten Verbindung Logik zum [Wiederherstellen der Verbindung](#restart_connection) zu implementieren.

## <a name="429-too-many-requests-returned-for-client-requests"></a>Rückgabe von Fehler 429 (Zu viele Anforderungen) für Clientanforderungen

Fehler 429 wird zurückgegeben, wenn die Anzahl der **gleichzeitigen** Verbindungen den Grenzwert überschreitet.

Bei **Free**-Instanzen beträgt der Grenzwert für die Anzahl der **gleichzeitigen** Verbindungen 20 für **Standard** Instanzen. Der Grenzwert für die Anzahl **gleichzeitiger** Verbindungen **pro Einheit** beträgt 1 K, d. h., Unit100 erlaubt 100 K gleichzeitige Verbindungen.

Die Anzahl der Verbindungen schließt Client- und Serververbindungen mit ein. [Hier](./signalr-concept-messages-and-connections.md#how-connections-are-counted) erfahren Sie, wie Verbindungen gezählt werden.

## <a name="500-error-when-negotiate-azure-signalr-service-is-not-connected-yet-please-try-again-later"></a>Fehler 500 bei Aushandlung: Der Azure SignalR Service ist noch nicht verbunden. Versuchen Sie es später noch mal.

### <a name="root-cause"></a>Grundursache

Dieser Fehler wird gemeldet, wenn keine Serververbindung mit dem Azure SignalR Service besteht.

### <a name="troubleshooting-guide"></a>Handbuch zur Problembehandlung

Aktivieren Sie die serverseitige Ablaufverfolgung, um die Fehlerdetails zu ermitteln, wenn der Server versucht, eine Verbindung mit dem Azure SignalR Service herzustellen.

#### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>Aktivieren der serverseitigen Protokollierung für ASP.NET Core SignalR

Die serverseitige Protokollierung für ASP.NET Core SignalR ist in `ILogger` integriert und beruht auf der [Protokollierung](/aspnet/core/fundamentals/logging/?tabs=aspnetcore2x&view=aspnetcore-2.1), die im ASP.NET Core-Framework bereitgestellt wird. Sie können die serverseitige Protokollierung mithilfe von `ConfigureLogging` aktivieren, wie im folgenden Beispiel gezeigt:
```cs
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```
Protokollierungskategorien für Azure SignalR beginnen immer mit `Microsoft.Azure.SignalR`. Wenn Sie detaillierte Protokolle von Azure SignalR aktivieren möchten, konfigurieren Sie die Präfixe in der Datei **appsettings.json** mit der Ebene `Debug`, wie hier gezeigt:
```JSON
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Debug",
            ...
        }
    }
}
```

#### <a name="enable-server-side-traces-for-aspnet-signalr"></a>Aktivieren serverseitiger Ablaufverfolgungen für ASP.NET SignalR

Ab der SDK-Version `1.0.0` können Sie Ablaufverfolgungen aktivieren, indem Sie in `web.config` Folgendes hinzufügen: ([Details](https://github.com/Azure/azure-signalr/issues/452#issuecomment-478858102))
```xml
<system.diagnostics>
    <sources>
      <source name="Microsoft.Azure.SignalR" switchName="SignalRSwitch">
        <listeners>
          <add name="ASRS" />
        </listeners>
      </source>
    </sources>
    <!-- Sets the trace verbosity level -->
    <switches>
      <add name="SignalRSwitch" value="Information" />
    </switches>
    <!-- Specifies the trace writer for output -->
    <sharedListeners>
      <add name="ASRS" type="System.Diagnostics.TextWriterTraceListener" initializeData="asrs.log.txt" />
    </sharedListeners>
    <trace autoflush="true" />
  </system.diagnostics>
```

<a name="client_connection_drop"></a>

## <a name="client-connection-drops"></a>Trennen der Clientverbindung

Wenn der Client mit Azure SignalR verbunden ist, kann es vorkommen, dass die permanente Verbindung zwischen dem Client und Azure SignalR aus unterschiedlichen Gründen getrennt wird. In diesem Abschnitt werden verschiedene mögliche Ursachen für die Trennung der Verbindung beschrieben, und es wird erläutert, wie Sie die Ursache ermitteln können.

### <a name="possible-errors-seen-from-the-client-side"></a>Mögliche Fehler auf Clientseite

* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`
* `{"type":7,"error":"Connection closed with an error."}`
* `{"type":7,"error":"Internal server error."}`

### <a name="root-cause"></a>Grundursache:

Clientverbindungen können unter verschiedenen Umständen getrennt werden:
* Wenn `Hub` Ausnahmen bei Eingehen der Anforderung auslöst.
* Wenn die Serververbindung, die für die Clientweiterleitung verwendet wird, getrennt wird, finden Sie weiter unten im Abschnitt zum [Trennen der Serververbindung](#server_connection_drop) weitere Informationen.
* Wenn ein Problem mit der Netzwerkkonnektivität zwischen dem Client und dem SignalR Service auftritt.
* Wenn beim SignalR Service interne Fehler auftreten, z. B. bei einem Neustart, einem Failover oder einer Bereitstellung.

### <a name="troubleshooting-guide"></a>Handbuch zur Problembehandlung

1. Öffnen Sie ein serverseitiges App-Protokoll, um festzustellen, ob ein ungewöhnliches Ereignis eingetreten ist.
2. Überprüfen Sie im serverseitigen Ereignisprotokoll, ob der App-Server neu gestartet wurde.
3. Erstellen Sie ein Issue mit Angabe des Zeitrahmens, und senden Sie uns den Ressourcennamen per E-Mail zu.


## <a name="client-connection-increases-constantly"></a>Anzahl der Clientverbindungen nimmt beständig zu

Dies kann durch eine falsche Verwendung der Clientverbindung verursacht werden. Wenn ein Benutzer vergisst, den SignalR-Client anzuhalten oder zu verwerfen, bleibt die Verbindung geöffnet.

### <a name="possible-errors-seen-from-the-signalrs-metrics-that-is-in-monitoring-section-of-azure-portal-resource-menu"></a>Mögliche Fehler in den SignalR-Metriken im Abschnitt „Überwachung“ des Ressourcenmenüs im Azure-Portal

Clientverbindungen nehmen in den Metriken von Azure SignalR ständig über einen längeren Zeitraum zu.

:::image type="content" source="./media/signalr-howto-troubleshoot-guide/client-connection-increasing-constantly.jpg" alt-text="Anzahl der Clientverbindungen nimmt beständig zu":::

### <a name="root-cause"></a>Grundursache:

Für die SignalR-Clientverbindung wird `DisposeAsync` nie aufgerufen, sodass die Verbindung geöffnet bleibt.

### <a name="troubleshooting-guide"></a>Handbuch zur Problembehandlung

1. Überprüfen Sie, ob der SignalR-Client **nie** geschlossen wird.

### <a name="solution"></a>Lösung

Überprüfen Sie, ob Sie die Verbindung geschlossen haben. Rufen Sie `HubConnection.DisposeAsync()` manuell auf, wenn Sie die Verbindung nicht mehr benötigen.

Beispiel:

```C#
var connection = new HubConnectionBuilder()
    .WithUrl(...)
    .Build();
try
{
    await connection.StartAsync();
    // Do your stuff
    await connection.StopAsync();
}
finally
{
    await connection.DisposeAsync();
}
```

### <a name="common-improper-client-connection-usage"></a>Unsachgemäße Verwendung der Clientverbindung

#### <a name="azure-function-example"></a>Beispiel zu einer Azure-Funktion 

Dieses Problem tritt häufig auf, wenn eine SignalR-Clientverbindung in der Methode einer Azure-Funktion hergestellt wird, anstatt sie als statischen Member der Function-Klasse zu erstellen. Möglicherweise erwarten Sie, dass nur eine Clientverbindung hergestellt wird, Sie stellen jedoch in den Metriken im Bereich „Überwachung“ des Ressourcenmenüs im Azure-Portal fest, dass die Anzahl der Clientverbindungen ständig zunimmt. Alle Verbindungen werden erst getrennt, nachdem die Azure-Funktion oder der Azure SignalR Service neu gestartet wurde. Dies liegt daran, dass die Azure-Funktion für **jede** Anforderung **eine** Clientverbindung herstellt. Wenn Sie die Clientverbindung in der Funktionsmethode nicht beenden, behält der Client die Verbindungen mit dem Azure SignalR Service bei.

#### <a name="solution"></a>Lösung

* Denken Sie daran, die Clientverbindung zu schließen, wenn Sie SignalR-Clients in einer Azure-Funktion verwenden oder den SignalR-Client als Singleton verwenden.
* Anstatt SignalR-Clients in Azure-Funktionen zu verwenden, können Sie SignalR-Clients anderweitig erstellen und mithilfe von [Azure Functions-Bindungen für den Azure SignalR Service](https://github.com/Azure/azure-functions-signalrservice-extension) die [Aushandlung](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L22) zwischen Client und Azure SignalR ausführen. Außerdem können Sie die Bindung verwenden, um [Nachrichten zu senden](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L40). Beispiele für die Clientaushandlung und das Senden von Nachrichten finden Sie [hier](https://github.com/Azure/azure-functions-signalrservice-extension/tree/dev/samples). Weitere Informationen finden Sie [hier](https://github.com/Azure/azure-functions-signalrservice-extension).
* Wenn Sie SignalR-Clients in einer Azure-Funktion verwenden, gibt es für Ihr Szenario möglicherweise eine besser geeignete Architektur. Überprüfen Sie, ob Sie eine ordnungsgemäße serverlose Architektur konzipiert haben. Weitere Informationen finden Sie unter [Erstellen von serverlosen Echtzeitanwendungen mit SignalR Service-Bindungen für Azure Functions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService).

<a name="server_connection_drop"></a>

## <a name="server-connection-drops"></a>Trennen der Serververbindung

Wenn der App-Server gestartet wird, wird das Azure SDK im Hintergrund gestartet, um Serververbindungen mit dem Remote-Azure SignalR Service zu initiieren. Wie in den [ausführlichen Informationen zu Azure SignalR Service](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) beschrieben, leitet Azure SignalR eingehenden Clientdatenverkehr an diese Serververbindungen weiter. Nachdem eine Serververbindung getrennt wurde, werden alle Clientverbindungen, die er behandelt, ebenfalls geschlossen.

Da es sich bei den Verbindungen zwischen App-Server und SignalR Service um persistente Verbindungen handelt, treten möglicherweise Netzwerkverbindungsprobleme auf. Im Server-SDK ist die Strategie **Verbindungen immer wiederherstellen** für Serververbindungen definiert. Als bewährte Vorgehensweise empfehlen wir Benutzern auch, Clients Logik zum fortlaufenden Wiederherstellen von Verbindungen mit einer zufälligen Verzögerungszeit hinzuzufügen, um zahlreiche gleichzeitige Anforderungen an den Server zu vermeiden.

In regelmäßigen Abständen werden neue Versionen des Azure SignalR Service veröffentlicht. Azure-weite Betriebssystempatches oder Upgrades führen gelegentlich zu Unterbrechung bei abhängigen Diensten. Dies kann kurze Dienstunterbrechungen zur Folge haben. Solange die Clientseite über den Mechanismus zum Trennen und Wiederherstellen der Verbindung verfügt, sind die Auswirkungen jedoch minimal (wie bei jeder clientseitig verursachten Verbindungstrennung/-wiederherstellung).

In diesem Abschnitt werden verschiedene mögliche Ursachen für die Trennung der Serververbindung beschrieben, und es wird erläutert, wie Sie die Ursache ermitteln können.

### <a name="possible-errors-seen-from-server-side"></a>Mögliche Fehler auf Serverseite:

* `[Error]Connection "..." to the service was dropped`
* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`

### <a name="root-cause"></a>Grundursache:

Die Serverdienstverbindung wird von **ASRS**(**A** zure **S** ignal **R** **S** ervice) geschlossen.

### <a name="troubleshooting-guide"></a>Handbuch zur Problembehandlung

1. Öffnen Sie ein serverseitiges App-Protokoll, um festzustellen, ob ein ungewöhnliches Ereignis eingetreten ist.
2. Überprüfen Sie im serverseitigen Ereignisprotokoll, ob der App-Server neu gestartet wurde.
3. Erstellen Sie ein Issue mit Angabe des Zeitrahmens, und senden Sie uns den Ressourcennamen per E-Mail zu.

## <a name="tips"></a>Tipps

<a name="view_request"></a>

* Anzeigen ausgehender Anforderungen vom Client
Als Beispiel wird hier ASP.NET Core verwendet (ASP.NET ist vergleichbar):
    * Im Browser:

        Wenn Sie beispielsweise Chrome verwenden, können Sie mit **F12** das Konsolenfenster öffnen und zur Registerkarte **Netzwerk** wechseln. Möglicherweise müssen Sie die Seite mit **F5** aktualisieren, um das Netzwerk von Anfang an zu erfassen.

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/chrome-network.gif" alt-text="Chrome-Ansicht „Netzwerk“":::

    * Im C#-Client:

        Sie können lokalen Webdatenverkehr mit [Fiddler](https://www.telerik.com/fiddler) anzeigen. WebSocket-Datenverkehr wird ab Fiddler 4.5 unterstützt.

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/fiddler-view-network-inline.png" alt-text="Fiddler-Ansicht „Netzwerk“" lightbox="./media/signalr-howto-troubleshoot-guide/fiddler-view-network.png":::

<a name="restart_connection"></a>

* Wiederherstellen der Clientverbindung
    
    Im Folgenden finden Sie [Beispielcode](https://github.com/Azure/azure-signalr/tree/dev/samples) mit Logik zum Wiederherstellen von Verbindungen mit der Strategie *Verbindung immer wiederherstellen*:

    * [ASP.NET Core-C#-Client](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample.CSharpClient/Program.cs#L64)

    * [ASP.NET Core-JavaScript-Client](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample/wwwroot/index.html#L164)

    * [ASP.NET-C#-Client](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.CSharpClient/Program.cs#L78)

    * [ASP.NET-JavaScript-Client](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.JavaScriptClient/wwwroot/index.html#L71)

## <a name="next-steps"></a>Nächste Schritte

In diesem Leitfaden wurde beschrieben, wie Sie häufig auftretende Probleme behandeln. Außerdem konnten Sie sich über eher allgemeine Verfahren zur Problembehandlung informieren. 

> [!div class="nextstepaction"]
> [Behandeln von Konnektivitätsproblemen und von Problemen bei der Nachrichtenübermittlung](./signalr-howto-troubleshoot-method.md)
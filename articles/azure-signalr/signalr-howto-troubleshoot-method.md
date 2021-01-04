---
title: Problembehandlungsübung für Azure SignalR Service
description: Hier erfahren Sie, wie Sie Konnektivitätsprobleme und Probleme bei der Nachrichtenübermittlung behandeln.
author: YanJin
ms.service: signalr
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: yajin1
ms.openlocfilehash: 413bb88deac96c1ca12e8a9d25fc9cd16edf4616
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183956"
---
# <a name="how-to-troubleshoot-connectivity-and-message-delivery-issues"></a>Behandeln von Konnektivitätsproblemen und von Problemen bei der Nachrichtenübermittlung

In diesem Leitfaden werden verschiedene Methoden zur Selbstdiagnose vorgestellt, mit denen Sie direkt die Ursache ermitteln oder das Problem zumindest eingrenzen können. Das Ergebnis der Selbstdiagnose ist auch hilfreich, wenn Sie das Problem zur weiteren Untersuchung an uns melden.

Überprüfen Sie als Erstes im Azure-Portal, mit welchem Dienstmodus ([ServiceMode](./concept-service-mode.md)) Azure SignalR Service (auch **ASRS** genannt) konfiguriert ist.

:::image type="content" source="./media/signalr-howto-troubleshoot-method/service-mode.png" alt-text="ServiceMode":::

* Informationen zum Modus `Default` finden Sie in der [Problembehandlung für den Standardmodus](#default_mode_tsg).

* Informationen zum Modus `Serverless` finden Sie in der [Problembehandlung für den serverlosen Modus](#serverless_mode_tsg).

* Informationen zum Modus `Classic` finden Sie in der [Problembehandlung für den klassischen Modus](#classic_mode_tsg).

<a name="default_mode_tsg"></a>

## <a name="default-mode-troubleshooting"></a>Problembehandlung für den Standardmodus

Wenn sich **ASRS** im Modus *Standard* befindet, gibt es **drei** Rollen: *Client*, *Server* und *Dienst*:

* *Client:* *Client* steht für Clients, die mit **ASRS** verbunden sind. Die dauerhaften Verbindungen zwischen Client und **ASRS** werden in diesem Leitfaden als *Clientverbindungen* bezeichnet.

* *Server*: *Server* steht für den Server, der die Clientaushandlung verarbeitet und die SignalR-Logik vom Typ `Hub` hostet. Die dauerhaften Verbindungen zwischen *Server* und **ASRS** werden in diesem Leitfaden als *Serververbindungen* bezeichnet.

* *Dienst*: *Dienst* ist der Kurzname für **ASRS** in diesem Leitfaden.

Die [ausführlichen Informationen zu Azure SignalR Service](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) enthalten eine detaillierte Einführung in die gesamte Architektur und den Workflow.

Das Problem kann auf verschiedene Arten eingegrenzt werden. 

* Wenn das Problem direkt im Verlauf auftritt oder reproduzierbar ist, empfiehlt sich eine Betrachtung des aktuellen Datenverkehrs. 

* Sollte sich das Problem nicht ohne Weiteres reproduzieren lassen, können Ablaufverfolgungen und Protokolle weiterhelfen.

### <a name="how-to-view-the-traffic-and-narrow-down-the-issue"></a>Anzeigen des Datenverkehrs und Eingrenzen des Problems

Die Erfassung des aktuellen Datenverkehrs ist die einfachste Möglichkeit zur Eingrenzung des Problems. Die [Netzwerkablaufverfolgungen](/aspnet/core/signalr/diagnostics#network-traces) können mithilfe der unten beschriebenen Optionen erfasst werden:

* [Erfassen einer Netzwerkablaufverfolgung mit Fiddler (bevorzugte Option)](/aspnet/core/signalr/diagnostics#network-traces)

* [Erfassen einer Netzwerkablaufverfolgung mit tcpdump (nur macOS und Linux)](/aspnet/core/signalr/diagnostics#collect-a-network-trace-with-tcpdump-macos-and-linux-only)

* [Erfassen einer Netzwerkablaufverfolgung im Browser](/aspnet/core/signalr/diagnostics#collect-a-network-trace-in-the-browser)

<a name="view_traffic_client"></a>

#### <a name="client-requests"></a>Clientanforderungen

Für eine dauerhafte SignalR-Verbindung wird zunächst eine Aushandlung (`/negotiate`) mit dem Server Ihrer gehosteten App durchgeführt. Anschließend erfolgt eine Umleitung zum Azure SignalR-Dienst, bevor schließlich die eigentliche dauerhafte Verbindung mit dem Azure SignalR-Dienst hergestellt wird. Die detaillierten Schritte finden Sie in den [ausführlichen Informationen zu Azure SignalR Service](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md).

Überprüfen Sie anhand der clientseitigen Netzwerkablaufverfolgung, welche Anforderung nicht erfolgreich war und welcher Statuscode und welche Antwort zurückgegeben wurde, und suchen Sie im [Leitfaden zur Problembehandlung](./signalr-howto-troubleshoot-guide.md) nach Lösungen.

#### <a name="server-requests"></a>Serveranforderungen

Der *Server* von SignalR verwaltet die *Serververbindung* zwischen *Server* und *Service*. Beim Start des App-Servers wird die **WebSocket**-Verbindung mit dem Azure SignalR-Dienst gestartet. Der gesamte Clientdatenverkehr wird über den Azure SignalR-Dienst an diese *Serververbindungen* und anschließend an den `Hub` weitergeleitet. Wenn eine *Serververbindung* ausfällt, werden die an diese *Serververbindung* weitergeleiteten Clients beeinträchtigt. Das Azure SignalR SDK verfügt über eine Wiederholungslogik, die dafür sorgt, dass die *Serververbindung* nach höchstens einer Minute Verzögerung wiederhergestellt wird, um die Auswirkungen zu minimieren.

*Serververbindungen* können aufgrund von Netzwerkinstabilität oder regulärer Wartungsmaßnahmen für Azure SignalR Service oder aufgrund von Updates/Wartungsmaßnahmen für den Server Ihrer gehosteten App ausfallen. Solange die Clientseite über den Mechanismus zum Trennen und Wiederherstellen der Verbindung verfügt, sind die Auswirkungen minimal (wie bei jeder clientseitig verursachten Verbindungstrennung/-wiederherstellung).

Sehen Sie sich die serverseitige Netzwerkablaufverfolgung an, um anhand des Statuscodes und der Fehlerdetails zu ermitteln, warum die *Serververbindung* ausfällt oder vom *Dienst* abgelehnt wird, und suchen Sie im [Leitfaden zur Problembehandlung](./signalr-howto-troubleshoot-guide.md) nach der Grundursache.


### <a name="how-to-add-logs"></a>Hinzufügen von Protokollen

Protokolle können hilfreich sein, um Probleme zu diagnostizieren und den Ausführungsstatus zu überwachen.

<a name="add_logs_client"></a>

#### <a name="how-to-enable-client-side-log"></a>Aktivieren der clientseitigen Protokollierung

Die clientseitige Protokollierung ist die gleiche wie bei der selbstgehosteten Variante von SignalR.

##### <a name="enable-client-side-logging-for-aspnet-core-signalr"></a>Aktivieren der clientseitigen Protokollierung für `ASP.NET Core SignalR`

* [JavaScript-Clientprotokollierung](/aspnet/core/signalr/diagnostics#javascript-client-logging)

* [.NET-Clientprotokollierung](/aspnet/core/signalr/diagnostics#net-client-logging)


##### <a name="enable-client-side-logging-for-aspnet-signalr"></a>Aktivieren der clientseitigen Protokollierung für `ASP.NET SignalR`

* [.NET-Client](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-net-client-windows-desktop-apps)

* [Aktivieren der Ablaufverfolgung in Windows Phone 8-Clients](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-windows-phone-8-clients)

* [Aktivieren der Ablaufverfolgung im JavaScript-Client](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-javascript-client)

<a name="add_logs_server"></a>

#### <a name="how-to-enable-server-side-log"></a>Aktivieren der serverseitigen Protokollierung

##### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>Aktivieren der serverseitigen Protokollierung für `ASP.NET Core SignalR`

Die serverseitige Protokollierung für `ASP.NET Core SignalR` wird in die `ILogger`-basierte [Protokollierung](/aspnet/core/fundamentals/logging/?tabs=aspnetcore2x&view=aspnetcore-2.1) integriert, der im `ASP.NET Core`-Framework bereitgestellt wird. Sie können die serverseitige Protokollierung mithilfe von `ConfigureLogging` aktivieren, wie im folgenden Beispiel gezeigt:

```cs
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```

Protokollierungskategorien für Azure SignalR beginnen immer mit `Microsoft.Azure.SignalR`. Wenn Sie detaillierte Protokolle von Azure SignalR aktivieren möchten, konfigurieren Sie die Präfixe in der Datei **appsettings.json** mit der Ebene `Information`, wie hier gezeigt:

```JSON
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Information",
            ...
        }
    }
}
```

Überprüfen Sie, ob ungewöhnliche Warnungs-/Fehlerprotokolle erfasst wurden. 


##### <a name="enable-server-side-traces-for-aspnet-signalr"></a>Aktivieren serverseitiger Ablaufverfolgungen für `ASP.NET SignalR`

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

Überprüfen Sie, ob ungewöhnliche Warnungs-/Fehlerprotokolle erfasst wurden. 


#### <a name="how-to-enable-logs-inside-azure-signalr-service"></a>Aktivieren von Protokollen in Azure SignalR Service

Sie können auch für Azure SignalR Service [Diagnoseprotokolle aktivieren](./signalr-howto-diagnostic-logs.md). Diese Protokolle enthalten ausführliche Informationen zu jeder Verbindung mit Azure SignalR Service.

<a name="serverless_mode_tsg"></a>

## <a name="serverless-mode-troubleshooting"></a>Problembehandlung für den serverlosen Modus

Wenn sich **ASRS** im *serverlosen* Modus befindet, wird der Modus `Serverless` nur von **ASP.NET Core SignalR** unterstützt. Von **ASP.NET SignalR** wird dieser Modus **NICHT** unterstützt.

Die einfachste Methode, um Konnektivitätsprobleme im Modus `Serverless` zu diagnostizieren, besteht in der [Betrachtung des clientseitigen Datenverkehrs](#view_traffic_client). Das Aktivieren der [clientseitigen Protokollierung](#add_logs_client) und der [serverseitigen Protokollierung](#add_logs_server) kann ebenfalls hilfreich sein.

<a name="classic_mode_tsg"></a>

## <a name="classic-mode-troubleshooting"></a>Problembehandlung für den klassischen Modus

Der Modus `Classic` ist veraltet und sollte nicht mehr verwendet werden. In diesem Modus ermittelt Azure SignalR Service anhand der bestehenden *Serververbindungen*, ob sich der aktuelle Dienst im Modus `default` oder im Modus `serverless` befindet. Dies kann zu Clientkonnektivitätsproblemen führen, da Azure SignalR bei einem plötzlichen Ausfall aller bestehenden *Serververbindungen* (beispielsweise aufgrund von Netzwerkinstabilität) von einem Wechsel zum Modus `serverless` ausgeht und Clients, die während dieses Zeitraums eine Verbindung herstellen, niemals an den Server der gehosteten App weitergeleitet werden. Aktivieren Sie die [serverseitige Protokollierung](#add_logs_server), und überprüfen Sie, ob Clients als `ServerlessModeEntered` erfasst wurden, wenn Sie über einen Server für Ihre gehostete App verfügen, aber einige Clients den App-Server nicht erreichen. Ist dies der Fall, kann es hilfreich sein, [diese Clientverbindungen abzubrechen](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md#API) und die Clients neu starten zu lassen.

Die Behandlung von Konnektivitätsproblemen und von Problemen bei der Nachrichtenübermittlung im Modus `classic` ist vergleichbar mit der [Problembehandlung für den Standardmodus](#default_mode_tsg).

## <a name="service-health"></a>Dienstintegrität

Die Dienstintegrität kann mithilfe der Integritäts-API überprüft werden.

* Anforderung: GET `https://{instance_name}.service.signalr.net/api/v1/health`

* Antwortstatuscode:
  * 200: Fehlerfrei
  * 503: Der Dienst ist fehlerhaft. Ihre Möglichkeiten:
    * Warten Sie einige Minuten auf eine automatische Wiederherstellung.
    * Überprüfen Sie, ob die IP-Adresse der IP-Adresse aus dem Portal entspricht.
    * Starten Sie die Instanz neu.
    * Sollte keine der obigen Optionen funktionieren, fügen Sie im Azure-Portal eine neue Supportanfrage hinzu.

Weitere Informationen zur Notfallwiederherstellung finden Sie [hier](./signalr-concept-disaster-recovery.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Leitfaden haben Sie gelernt, wie Sie Konnektivitätsprobleme und Probleme bei der Nachrichtenübermittlung behandeln. Informationen zur Behandlung allgemeiner Probleme finden Sie im folgenden Artikel: 

> [!div class="nextstepaction"]
> [Handbuch zur Problembehandlung](./signalr-howto-troubleshoot-guide.md)
---
title: Telemetriekanäle in Azure Application Insights | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Telemetriekanäle in Azure Application Insights SDKs für .NET/.NET Core anpassen.
services: application-insights
documentationcenter: .net
author: cijothomas
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: fa99e29e9ec6c2bef7296a50dd381ee5fc60a1cb
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255805"
---
# <a name="telemetrychannel-in-application-insights"></a>TelemetryChannel in Application Insights

TelemetryChannel ist ein integraler Bestandteil der [Azure Application Insights SDKs](../../azure-monitor/app/app-insights-overview.md). Hiermit wird die Pufferung und Übertragung von Telemetriedaten an den Application Insights-Dienst verwaltet. Die .NET- und .NET Core-Versionen der SDKs verfügen über zwei integrierte TelemetryChannels: `InMemoryChannel` und `ServerTelemetryChannel`. In diesem Artikel werden beide Kanäle ausführlich beschrieben. Außerdem sind Angaben dazu enthalten, wie Benutzer das Kanalverhalten anpassen können.

## <a name="what-is-a-telemetrychannel"></a>Was ist ein TelemetryChannel?

`TelemetryChannel` ist für das Puffern und Senden von Telemetrieelementen an den Application Insights-Dienst verantwortlich, wo sie für Abfragen und Analysen gespeichert werden. Es ist eine beliebige Klasse, die die Schnittstelle [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) implementiert.

Die `Send(ITelemetry item)`-Methode von TelemetryChannel wird aufgerufen, nachdem alle `TelemetryInitializer` und `TelemetryProcessor` aufgerufen wurden. Das bedeutet, dass alle Elemente, die von `TelemetryProcessor` verworfen wurden, den Kanal nicht erreichen. Mit `Send()` werden die Elemente in der Regel nicht sofort an das Back-End gesendet. Normalerweise werden Sie im Arbeitsspeicher gepufferten und für eine effiziente Übertragung in Batches gesendet.

[LiveMetrics](live-stream.md) verfügt auch über einen benutzerdefinierten Kanal, der für das Livestreaming von Telemetriedaten verwendet wird. Dieser Kanal ist vom regulären Telemetriekanal unabhängig, und dieses Dokument trifft auf den von `LiveMetrics` verwendeten Kanal nicht zu.

## <a name="built-in-telemetrychannels"></a>Integrierte TelemetryChannels

Im Lieferumfang des Application Insights SDK für .NET/.NET Core sind zwei integrierte Kanäle enthalten:

* **InMemoryChannel**
`InMemoryChannel` ist ein einfacher Kanal, der Elemente im Arbeitsspeicher puffert, bis sie gesendet werden. Elemente werden im Arbeitsspeicher gepuffert, und dieser wird einmal alle 30 Sekunden geleert oder sobald 500 Elemente gepuffert sind. Dieser Kanal bietet minimale Zuverlässigkeitsgarantien, da das Senden von Telemetriedaten bei Fehlern nicht wiederholt wird. Dieser Kanal bewahrt keine Elemente auf einem Datenträger, sodass nicht gesendete Elemente beim Herunterfahren der Anwendung (ordnungsgemäß oder nicht) dauerhaft verloren gehen. Es wird eine `Flush()`-Methode von diesem Kanal implementiert, mit der das synchrone Leeren aller Telemetrieelemente aus dem Arbeitsspeicher erzwungen werden kann. Dies eignet sich gut für Anwendungen mit kurzer Ausführungszeit, bei denen eine synchrone Leerung ideal ist.

    Dieser Kanal wird als Bestandteil des Nuget-Pakets `Microsoft.ApplicationInsights` geliefert und ist der Standardkanal, der vom SDK verwendet wird, wenn keine andere Konfiguration vorhanden ist.

* **ServerTelemetryChannel**
`ServerTelemetryChannel` ist ein komplexerer Kanal, der über Wiederholungsrichtlinien verfügt und Daten auf einem lokalen Datenträger speichern kann. Dieser Kanal wiederholt das Senden von Telemetriedaten, wenn vorübergehende Fehler auftreten. Dieser Kanal verwendet auch lokalen Datenträgerspeicher, um Elemente während Netzwerkausfällen oder bei großen Telemetriedatenmengen auf dem Datenträger aufzubewahren. Aufgrund dieser Wiederholungsmechanismen und des lokalen Datenträgerspeichers gilt dieser Kanal als zuverlässiger und wird für alle Produktionsszenarien empfohlen. Dieser Kanal ist die Standardeinstellung für [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)- und [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)-Anwendungen, die gemäß der verknüpften offiziellen Dokumentation konfiguriert werden. Dieser Kanal ist für Serverszenarien mit lang andauernden Prozessen optimiert. Die von diesem Kanal implementierte [`Flush()`](#which-channel-should-i-use)-Methode ist nicht synchron.

    Dieser Kanal wird als NuGet-Paket `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel` geliefert, und ist bei Verwendung des NuGet-Pakets `Microsoft.ApplicationInsights.Web` oder `Microsoft.ApplicationInsights.AspNetCore` automatisch enthalten.

## <a name="configuring-telemetrychannel"></a>Konfigurieren von TelemetryChannel

TelemetryChannel kann konfiguriert werden, indem Sie den gewünschten `TelemetryChannel` in der aktiven `TelemetryConfiguration` festlegen. Bei ASP.NET-Anwendungen umfasst die Konfiguration das Festlegen von `TelemetryChannel` auf `TelemetryConfiguration.Active` oder das Ändern von `ApplicationInsights.config`. Bei ASP.NET Core-Anwendungen schließt die Konfiguration das Hinzufügen des gewünschten Kanals zum Abhängigkeitsinjektionscontainer ein.

Nachfolgend ist ein Beispiel angegeben, in dem der Benutzer `StorageFolder` für den Kanal konfiguriert. `StorageFolder` ist nur eine der konfigurierbaren Einstellungen. Die vollständige Liste der Konfigurationseinstellungen ist im [Abschnitt mit den Einstellungen](telemetry-channels.md#configurable-settings-in-channel) enthalten.

## <a name="configuration-using-applicationinsightsconfig-for-aspnet-applications"></a>Konfiguration mit „ApplicationInsights.config“ für ASP.NET-Anwendungen

Der folgende Abschnitt von [ApplicationInsights.config](configuration-with-applicationinsights-config.md) zeigt die Konfiguration von ServerTelemetryChannel, bei der `StorageFolder` auf einen benutzerdefinierten Speicherort festgelegt ist.

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!--TelemetryProcessors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

## <a name="configuration-in-code-for-aspnet-applications"></a>Konfiguration im Code für ASP.NET-Anwendungen

Der folgende Code richtet ServerTelemetryChannel so ein, dass `StorageFolder` auf einen benutzerdefinierten Speicherort festgelegt ist. Dieser Code muss am Anfang der Anwendung hinzugefügt werden, normalerweise in der Application_Start()-Methode in `Global.aspx.cs`.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
protected void Application_Start()
{
    var serverTelemetryChannel = new ServerTelemetryChannel();
    serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
    serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
}
```

## <a name="configuration-in-code-for-aspnet-core-applications"></a>Konfiguration im Code für ASP.NET Core-Anwendungen

Das Ändern der `ConfigureServices`-Methode der `Startup.cs`-Klasse ist nachfolgend dargestellt.

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

public void ConfigureServices(IServiceCollection services)
{
    // This sets up ServerTelemetryChannel with StorageFolder set to a custom location.
    services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel() {StorageFolder = @"d:\temp\applicationinsights" });

    services.AddApplicationInsightsTelemetry();
}

```

> [!NOTE]
> Es ist wichtig zu beachten, dass eine Konfiguration des Kanals mithilfe von `TelemetryConfiguration.Active` für ASP.NET Core-Anwendungen nicht empfohlen wird.

## <a name="configuring-telemetrychannel-in-code-for-netnet-core-console-applications"></a>Konfigurieren von TelemetryChannel im Code für .NET/.NET Core-Konsolenanwendungen

Bei Konsolen-Apps ist der Code für .NET und .NET Core identisch. Dieser Code ist nachfolgend dargestellt.

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Funktionsdetails zu ServerTelemetryChannel

`ServerTelemetryChannel` puffert eingehende Elemente in einem Puffer im Arbeitsspeicher. Diese werden einmal alle 30 Sekunden, oder sobald 500 Elemente gepuffert sind, serialisiert, komprimiert und in einer `Transmission`-Instanz gespeichert. Eine einzelne `Transmission`-Instanz enthält bis zu 500 Elemente und stellt einen Batch von Telemetriedaten dar, die über einen einzigen HTTPS-Aufruf an den Application Insights-Dienst gesendet werden. Standardmäßig können maximal 10 `Transmission`-Instanzen parallel gesendet werden. Wenn Telemetriedaten schneller eintreffen oder das Netzwerk/Application Insights-Back-End langsam ist, werden `Transmission`-Instanzen im Arbeitsspeicher gespeichert. Die Standardkapazität dieses Übertragungspuffers im Arbeitsspeicher beträgt 5 MB. Sobald die Kapazität des Arbeitsspeichers überschritten ist, werden `Transmission`-Instanzen auf einem lokalen Datenträger für bis zu 50 MB gespeichert. `Transmission`-Instanzen werden auch bei Auftreten von Netzwerkproblemen auf dem lokalen Datenträger gespeichert. Nur Elemente, die auf dem lokalen Datenträger gespeichert sind, überstehen einen Absturz der Anwendung und werden gesendet, sobald die Anwendung wieder gestartet wird.

## <a name="configurable-settings-in-channel"></a>Konfigurierbare Einstellungen für den Kanal

Die vollständige Liste der konfigurierbaren Einstellungen für jeden Kanal finden Sie hier:

[InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

[ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Die am häufigsten verwendeten Einstellungen für `ServerTelemetryChannel` sind nachfolgend aufgeführt:

1. `MaxTransmissionBufferCapacity`: Die maximale Menge an Arbeitsspeicher in Byte, die vom Kanal zum Puffern von Übertragungen im Arbeitsspeicher verwendet wird. Sobald diese Kapazitätsgrenze erreicht ist, werden neue Elemente direkt auf dem lokalen Datenträger gespeichert. Der Standardwert ist 5 MB. Das Festlegen eines höheren Werts führt zu einer geringeren Datenträgerauslastung, doch muss beachtet werden, dass Elemente im Arbeitsspeicher bei einem Absturz der Anwendung verloren gehen.

2. `MaxTransmissionSenderCapacity`: Die maximale Menge an `Transmission`-Instanzen, die gleichzeitig an Application Insights gesendet werden. Der Standardwert ist 10, doch kann auch ein höherer Wert konfiguriert werden. Dies wird empfohlen, wenn eine große Menge von Telemetriedaten generiert wird, in der Regel bei Auslastungstests und/oder wenn die Stichprobenerstellung deaktiviert ist.

3. `StorageFolder`: Der Ordner, der vom Kanal verwendet wird, um Elemente bei Bedarf auf dem Datenträger zu speichern. In Windows wird entweder „%LocalAppData%“ oder „% Temp%“ verwendet, wenn keine explizite Konfiguration vorhanden ist. In Nicht-Windows-Umgebungen **muss** der Benutzer einen gültigen Speicherort konfigurieren, da sonst keine Telemetriedaten auf dem lokalen Datenträger gespeichert werden.

## <a name="which-channel-should-i-use"></a>Welchen Kanal sollte ich verwenden?

`ServerTelemetryChannel` wird für die meisten Produktionsszenarien mit Anwendungen mit langer Ausführungsdauer empfohlen. Die Implementierung der `Flush()`-Methode von `ServerTelemetryChannel` ist nicht synchron, und `Flush()` garantiert nicht, dass alle ausstehenden Elemente aus dem Arbeitsspeicher/vom Datenträger gesendet werden. Wenn dieser Kanal in Szenarien verwendet wird, in denen die Anwendung heruntergefahren werden soll, wird empfohlen, eine Verzögerung nach dem Aufruf von `Flush()` für diesen Kanal zu erwirken. Die genaue Dauer der erforderlichen Verzögerung ist nicht vorhersagbar, da sie von Faktoren abhängt, wie z.B. der Menge an Elementen oder `Transmissions` im Arbeitsspeicher, der Menge auf dem Datenträger, der Menge in Übertragung an das Back-End, und ob sich der Kanal in der Mitte von exponentiellen Backoff-Szenarien befindet. Wenn eine synchrone Leerung erforderlich ist, wird `InMemoryChannel` empfohlen.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="does-applicationinsights-channel-offer-guaranteed-telemetry-delivery-or-what-are-the-scenarios-where-telemetry-can-be-lost"></a>*Garantiert der ApplicationInsights-Kanal die Übermittlung von Telemetriedaten bzw. in welchen Szenarien können Telemetriedaten verloren gehen?*

* Die kurze Antwort ist, dass keiner der integrierten Kanäle als Transaktionstyp die Übermittlung von Telemetriedaten an das Back-End garantiert. Zwar bietet `ServerTelemetryChannel` im Vergleich zu `InMemoryChannel` eine zuverlässigere Übermittlung von Telemetriedaten und unternimmt den besten Versuch, die Daten zu senden, es können aber dennoch Telemetriedaten in verschiedenen Szenarien verloren gehen. Nachfolgend sind einige häufige Szenarien aufgeführt, in denen Telemetriedaten verloren gehen:

1. Elemente im Arbeitsspeicher gehen verloren, sobald die Anwendung abstürzt.
1. Telemetriedaten werden bei Netzwerkausfällen oder Problemen mit dem Application Insights-Back-End auf dem lokalen Datenträger gespeichert. Allerdings werden Elemente verworfen, die älter als 24 Stunden sind. Daher gehen Telemetriedaten bei länger andauernden Netzwerkproblemen verloren.
1. Die Standardspeicherorte auf dem Datenträger zum Speichern von Telemetriedaten unter Windows sind „% LocalAppData%“ oder „%Temp%“. Diese Speicherorte befinden sich normalerweise lokal auf dem Computer. Wenn die Anwendung physisch von einem Speicherort an einen anderen migriert, gehen alle Telemetriedaten verloren, die an diesem Speicherort gespeichert sind.
1. In Azure-Web-Apps (Windows) ist der Standardspeicherort auf dem Datenträger „D:\local\LocalAppData“. Dieser Speicherort ist nicht permanent und wird bei App-Neustarts, horizontalem Hochskalierung usw. gelöscht, was zu einem Verlust von Telemetriedaten führt, die an diesen Orten gespeichert sind. Benutzer können die Speicherung überschreiben und einen permanenten Speicherort wie „D:\home“ festlegen, doch basieren diese permanenten Speicherorte auf Remotespeicher und können langsam sein.

### <a name="does-servertelemetrychannel-work-in-non-windows-systems"></a>*Kann ServerTelemetryChannel in Nicht-Windows-Systemen verwendet werden?*

* Obwohl der Name des Pakets/Namespace WindowsServer lautet, wird dieser Kanal in Nicht-Windows-Systemen mit folgender Ausnahme unterstützt. In Nicht-Windows-Systemen erstellt der Kanal nicht standardmäßig einen lokalen Speicherordner. Die Benutzer müssen einen lokalen Speicherordner erstellen und den Kanal für die Verwendung dieses Ordners konfigurieren. Sobald der lokale Speicher konfiguriert ist, funktioniert der Kanal in Windows- und Nicht-Windows-Systemen gleich.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>*Erstellt das SDK einen temporären lokalen Speicher? Werden die Daten beim Speichern verschlüsselt?*

* Das SDK speichert Telemetrieelemente bei Netzwerkproblemen oder Drosselung im lokalen Speicher. Diese Daten werden nicht lokal verschlüsselt.
Bei Windows-Systemen erstellt das SDK automatisch einen temporären lokalen Ordner im Verzeichnis „TEMP“ oder „APPDATA“ und schränkt den Zugriff auf Administratoren und den aktuellen Benutzer ein.
In Nicht-Windows-Systemen wird kein lokaler Speicher automatisch vom SDK erstellt, und daher werden Daten nicht standardmäßig lokal gespeichert. Die Benutzer können selbst ein Speicherverzeichnis erstellen und den Kanal für dessen Verwendung konfigurieren. In diesem Fall muss der Benutzer sicherstellen, dass dieses Verzeichnis gesichert ist.
Lesen Sie weitere Informationen zum [Datenschutz](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>Open Source SDK
Wie alle Application Insights-SDKs sind auch Kanäle Open Source. Lesen Sie den Code, tragen Sie zum Code bei, oder melden Sie Probleme im [offiziellen GitHub-Repository](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Nächste Schritte

* [Stichproben](../../azure-monitor/app/sampling.md)
* [SDK-Problembehandlung](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)

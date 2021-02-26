---
title: Remote Rendering-Sitzungen
description: Es wird beschrieben, was eine Remote Rendering-Sitzung ist.
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 79f3f93338d15562dcc37857d63bc8b2d7e96b05
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530551"
---
# <a name="remote-rendering-sessions"></a>Remote Rendering-Sitzungen

In Azure Remote Rendering (ARR) ist eine *Sitzung* ein wichtiges Konzept. In diesem Artikel wird genau beschrieben, was eine Sitzung ist.

## <a name="overview"></a>Übersicht

Bei Azure Remote Rendering werden komplexe Renderingaufgaben in die Cloud verlagert. Diese Renderingaufgaben können nicht von allen Servern übernommen werden, da die meisten Cloudserver nicht über GPUs verfügen. Aufgrund der hohen Datenmenge und der geforderten Erzielung von Ergebnissen bei interaktiven Bildfrequenzen kann die Zuständigkeit dafür, welche Benutzeranforderung von welchem Server verarbeitet wird, auch nicht einfach an einen anderen Computer übergeben werden. Dies ist ggf. nur für allgemeineren Webdatenverkehr möglich.

Aus diesem Grund muss bei der Nutzung von Azure Remote Rendering ein Cloudserver mit den benötigten Hardwarefunktionen exklusiv für die Verarbeitung Ihrer Renderinganforderungen reserviert werden. Eine *Sitzung* umfasst alle Vorgänge, die für die Interaktion mit dem Server durchgeführt werden müssen. Zu Beginn wird die anfängliche Anforderung zur Reservierung (*Lease*) eines Computers für Sie gesendet. Anschließend folgen alle Befehle zum Laden und Bearbeiten von Modellen, und am Ende des Prozesses erfolgt die Freigabe der Lease auf dem Cloudserver.

## <a name="managing-sessions"></a>Verwalten von Sitzungen

Es gibt mehrere Möglichkeiten, wie Sie Sitzungen verwalten und damit interagieren können. Bei der sprachunabhängigen Vorgehensweise zum Erstellen, Aktualisieren und Herunterfahren von Sitzungen wird die [REST-API für die Sitzungsverwaltung](../how-tos/session-rest-api.md) verwendet. In C# und C++ werden diese Vorgänge über die Klassen `RemoteRenderingClient` und `RenderingSession` verfügbar gemacht. Für Unity-Anwendungen werden über die Komponente `ARRServiceUnity` weitere Hilfsfunktionen bereitgestellt.

Nachdem Sie eine *Verbindung* mit einer aktiven Sitzung hergestellt haben, werden Vorgänge wie das [Laden von Modellen](models.md) und die Interaktion mit der Szene über die `RenderingSession`-Klasse verfügbar gemacht.

### <a name="managing-multiple-sessions-simultaneously"></a>Gleichzeitiges Verwalten mehrerer Sitzungen

Es ist nicht möglich, über ein Gerät eine vollständige *Verbindung* mit mehreren Sitzungen herzustellen. Sie haben aber die Möglichkeit, aus einer Anwendung beliebig viele Sitzungen zu erstellen, zu beobachten und herunterzufahren. Solange für die App niemals die Verbindungsherstellung mit einer Sitzung vorgesehen ist, muss sie auch nicht auf einem Gerät, z. B. HoloLens 2, ausgeführt werden. Ein Anwendungsfall für eine Implementierung dieser Art ist beispielsweise das Steuern von Sitzungen über einen zentralen Mechanismus. Sie können beispielsweise eine Web-App erstellen, bei der sich mehrere Tablets und HoloLens-Geräte anmelden können. Anschließend können über die App Optionen auf den Tablets angezeigt werden, z. B. zum Auswählen der Anzeige von CAD-Modellen. Wenn ein Benutzer eine Auswahl trifft, werden diese Informationen an alle HoloLens-Geräte übermittelt, damit sie überall verfügbar sind.

## <a name="session-phases"></a>Sitzungsphasen

Jede Sitzung durchläuft mehrere Phasen.

### <a name="session-startup"></a>Sitzungsstart

Wenn Sie in ARR die [Erstellung einer neuen Sitzung](../how-tos/session-rest-api.md) anfordern, wird als Erstes eine [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) für die Sitzung zurückgegeben. Diese UUID ermöglicht Ihnen das Abfragen von Informationen zur Sitzung. Die UUID und einige grundlegende Informationen zur Sitzung werden 30 Tage lang gespeichert, damit Sie diese auch dann noch abfragen können, nachdem die Sitzung beendet wurde. An diesem Punkt wird der **Sitzungszustand** als **Wird gestartet** angegeben.

Als Nächstes sucht Azure Remote Rendering nach einem Server, auf dem Ihre Sitzung gehostet werden kann. Bei dieser Suche sind zwei Faktoren zu berücksichtigen. Erstens werden nur Server in Ihrer [Region](../reference/regions.md) reserviert. Der Grund ist, dass die Netzwerklatenz zwischen den Regionen unter Umständen zu hoch ist, um eine angemessene Benutzerfreundlichkeit sicherstellen zu können. Der zweite Faktor ist die gewünschte *Größe*, die Sie angegeben haben. In jeder Region gibt es eine begrenzte Anzahl von Servern, die die Größenanforderung für [*Standard*](../reference/vm-sizes.md) oder [*Premium*](../reference/vm-sizes.md) erfüllen. Daher ist die Sitzungserstellung nicht erfolgreich, wenn alle Server der angeforderten Größe in Ihrer Region gerade verwendet werden. Sie können den Grund für den Fehler [abfragen](../how-tos/session-rest-api.md).

> [!IMPORTANT]
> Wenn Sie eine Servergröße vom Typ *Standard* anfordern und die Anforderung aufgrund einer hohen Nachfrage nicht erfolgreich ist, bedeutet dies nicht, dass auch beim Anfordern eines Servers vom Typ *Premium* ein Fehler auftritt. Falls dies für Sie eine Option ist, können Sie also auch versuchen, als Fallbacklösung auf eine *Premium*-Servergröße zurückzugreifen.

Wenn der Dienst einen geeigneten Server findet, muss er den richtigen virtuellen Computer (VM) auf den Server kopieren, um ihn zu einem Azure Remote Rendering-Host zu machen. Dieser Vorgang dauert einige Minuten. Anschließend wird der virtuelle Computer gestartet, und der **Sitzungszustand** ändert sich in **Bereit**.

Der Server wartet nun ausschließlich auf Ihre Eingaben. Dies ist auch der Punkt, ab dem Ihnen Gebühren für den Dienst berechnet werden.

### <a name="connecting-to-a-session"></a>Herstellen einer Verbindung mit einer Sitzung

Wenn sich die Sitzung im Zustand *Bereit* befindet, können Sie eine *Verbindung* damit herstellen. Bei hergestellter Verbindung kann das Gerät Befehle zum Laden und Ändern von Modellen senden. Jeder ARR-Host ist jeweils nur für ein Clientgerät bestimmt. Wenn ein Client eine Verbindung mit einer Sitzung herstellt, verfügt er also über die exklusive Kontrolle über die gerenderten Inhalte. Dies bedeutet auch, dass die Renderingleistung niemals aus Gründen variieren kann, die außerhalb Ihrer Kontrolle liegen.

> [!IMPORTANT]
> Obwohl nur ein Client eine *Verbindung* mit einer Sitzung herstellen kann, können grundlegende Informationen zu Sitzungen, z. B. der aktuelle Zustand, ohne Verbindungsherstellung abgefragt werden.

Während ein Gerät mit einer Sitzung verbunden ist, tritt für Verbindungsversuche anderer Geräte ein Fehler auf. Nachdem die Verbindung des Geräts getrennt wurde – entweder absichtlich oder aufgrund eines Fehlers –, wird eine neue Verbindungsanforderung von der Sitzung akzeptiert. Alle vorherigen Zustände (geladene Modelle usw.) werden verworfen, damit für das nächste verbundene Gerät neu begonnen werden kann. Sitzungen können viele Male von unterschiedlichen Geräten wiederverwendet werden, und in den meisten Fällen ist es ggf. möglich, den Mehraufwand für das Starten der Sitzung vor den Endbenutzern zu verbergen.

> [!IMPORTANT]
> Der Remoteserver ändert den Zustand von clientseitigen Daten niemals. Alle Änderungen an den Daten (z. B. Transformieren von Updates und Laden von Anforderungen) müssen von der Clientanwendung vorgenommen werden. Bei allen Aktionen wird der Clientzustand sofort aktualisiert.

### <a name="session-end"></a>Sitzungsende

Wenn Sie eine neue Sitzung anfordern, geben Sie eine *maximale Leasedauer* an, die normalerweise bei 1 bis 8 Stunden liegt. Dies ist der Zeitraum, in dem Ihre Eingaben vom Host akzeptiert werden.

Es gibt zwei reguläre Gründe für das Ende einer Sitzung. Entweder fordern Sie die Beendigung der Sitzung manuell an, oder die maximale Leasedauer läuft ab. In beiden Fällen werden aktive Verbindungen mit dem Host sofort getrennt, und der Dienst wird auf dem Server heruntergefahren. Der Server wird dann an den Azure-Pool zurückgegeben und kann ggf. für andere Zwecke eingesetzt werden. Die Beendigung einer Sitzung kann nicht rückgängig gemacht oder abgebrochen werden. Das Abfragen des **Sitzungszustands** für eine beendete Sitzung führt entweder zur Rückgabe von **Beendet** oder **Abgelaufen**. Dies hängt davon ab, ob das Herunterfahren manuell durchgeführt oder die maximale Leasedauer erreicht wurde.

Eine Sitzung kann auch aufgrund eines Fehlers beendet werden.

In allen Fällen werden Ihnen keine weiteren Gebühren berechnet, nachdem eine Sitzung beendet wurde.

> [!WARNING]
> Ob Sie eine Verbindung mit einer Sitzung herstellen und wie lange diese aktiv ist, hat keinerlei Auswirkung auf die Abrechnung. Die Kosten für den Dienst richten sich nach der *Sitzungsdauer* (Zeitraum, in dem ein Server exklusiv für Sie reserviert ist) und den angeforderten Hardwarefunktionen (der [zugeordneten Größe](../reference/vm-sizes.md)). Wenn Sie eine Sitzung starten, eine Verbindung mit einer Dauer von fünf Minuten herstellen und die Sitzung dann nicht abbrechen, wird Ihnen die gesamte Leasedauer der Sitzung in Rechnung gestellt. Der Vorgang läuft weiter, bis die Lease abgelaufen ist. Die *maximale Leasedauer* dient quasi nur als Sicherheitsmechanismus. Es spielt keine Rolle, ob Sie eine Sitzung mit einer Leasedauer von acht Stunden anfordern und diese dann nur fünf Minuten lang nutzen, sofern Sie die Sitzung anschließend manuell beenden.

#### <a name="extend-a-sessions-lease-time"></a>Verlängern der Leasedauer einer Sitzung

Für eine aktive Sitzung können Sie die [Leasedauer verlängern](../how-tos/session-rest-api.md), falls dies erforderlich sein sollte.

## <a name="example-code"></a>Beispielcode

Im Code unten ist eine einfache Implementierung eines Sitzungsstarts veranschaulicht. Es wird auf den Zustand *Bereit* gewartet, und dann wird die Verbindung hergestellt. Anschließend folgen die Trennung und das Herunterfahren.

```cs
RemoteRenderingInitialization init = new RemoteRenderingInitialization();
// fill out RemoteRenderingInitialization parameters...

RemoteManagerStatic.StartupRemoteRendering(init);

SessionConfiguration sessionConfig = new SessionConfiguration();
// fill out sessionConfig details...

RemoteRenderingClient client = new RemoteRenderingClient(sessionConfig);

RenderingSessionCreationOptions rendererOptions = new RenderingSessionCreationOptions();
// fill out rendererOptions...

CreateRenderingSessionResult result = await client.CreateNewRenderingSessionAsync(rendererOptions);

RenderingSession session = result.Session;
RenderingSessionProperties sessionProperties;
while (true)
{
    var propertiesResult = await session.GetPropertiesAsync();
    sessionProperties = propertiesResult.SessionProperties;
    if (sessionProperties.Status != RenderingSessionStatus.Starting &&
        sessionProperties.Status != RenderingSessionStatus.Unknown)
    {
        break;
    }
    // REST calls must not be issued too frequently, otherwise the server returns failure code 429 ("too many requests"). So we insert the recommended delay of 10s
    await Task.Delay(TimeSpan.FromSeconds(10));
}

if (sessionProperties.Status != RenderingSessionStatus.Ready)
{
    // Do some error handling and either terminate or retry.
}

// Connect to server
ConnectionStatus connectStatus = await session.ConnectAsync(new RendererInitOptions());

// Connected!

while (...)
{
    // per frame update

    session.Connection.Update();
}

// Disconnect
session.Disconnect();

// stop the session
await session.StopAsync();

// shut down the remote rendering SDK
RemoteManagerStatic.ShutdownRemoteRendering();
```

Mehrere Instanzen von `RemoteRenderingClient` und `RenderingSession` können per Code verwaltet, bearbeitet und abgefragt werden. Es kann aber nur jeweils ein Gerät eine Verbindung mit einer `RenderingSession` herstellen.

Die Lebensdauer eines virtuellen Computers ist nicht an die Instanz `RemoteRenderingClient` oder `RenderingSession` gebunden. `RenderingSession.StopAsync` muss aufgerufen werden, um eine Sitzung zu beenden.

Die persistente Sitzungs-ID kann mit `RenderingSession.SessionUuid()` abgefragt und lokal zwischengespeichert werden. Mit dieser ID kann eine Anwendung `RemoteRenderingClient.OpenRenderingSessionAsync` aufrufen, um die Bindung an diese Sitzung durchzuführen.

Wenn `RenderingSession.IsConnected` „true“ ist, gibt `RenderingSession.Connection` eine Instanz von `RenderingConnection` zurück. Hierin sind die Funktionen zum [Laden von Modellen](models.md), Bearbeiten von [Entitäten](entities.md) und [Abfragen von Informationen](../overview/features/spatial-queries.md) zur gerenderten Szene enthalten.

## <a name="api-documentation"></a>API-Dokumentation

* [C# RenderingSession (Klasse)](/dotnet/api/microsoft.azure.remoterendering.renderingsession)
* [C# RemoteRenderingClient.CreateNewRenderingSessionAsync()](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient.createnewrenderingsessionasync)
* [C# RemoteRenderingClient.OpenRenderingSessionAsync()](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient.openrenderingsessionasync)
* [C++ RenderingSession (Klasse)](/cpp/api/remote-rendering/renderingsession)
* [C++ RemoteRenderingClient::CreateNewRenderingSessionAsync](/cpp/api/remote-rendering/remoterenderingclient#createnewrenderingsessionasync)
* [C++ RemoteRenderingClient::OpenRenderingSession](/cpp/api/remote-rendering/remoterenderingclient#openrenderingsession)

## <a name="next-steps"></a>Nächste Schritte

* [Entitäten](entities.md)
* [Modelle](models.md)
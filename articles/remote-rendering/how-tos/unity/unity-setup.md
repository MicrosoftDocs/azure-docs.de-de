---
title: Einrichten von Remote Rendering für Unity
description: Initialisieren von Azure Remote Rendering in einem Unity-Projekt
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 48f01058d8e879a9610e76638215214c059982fa
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594213"
---
# <a name="set-up-remote-rendering-for-unity"></a>Einrichten von Remote Rendering für Unity

Um Azure Remote Rendering (ARR) in Unity zu aktivieren, stellen wir dedizierte Methoden bereit, die einige Unity-spezifische Aspekte berücksichtigen.

## <a name="startup-and-shutdown"></a>Starten und Herunterfahren

Verwenden Sie `RemoteManagerUnity`, um Remote Rendering zu initialisieren. Diese Klasse ruft den generischen `RenderingConnection` auf, implementiert aber bereits Unity-spezifische Details für Sie. Unity verwendet beispielsweise ein bestimmtes Koordinatensystem. Wenn Sie `RemoteManagerUnity.Initialize` aufrufen, wird die richtige Konvention eingerichtet. Der Aufruf erfordert auch, dass Sie die Unity-Kamera bereitstellen, die zum Anzeigen der remote gerenderten Inhalte verwendet werden soll.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

Um Remote Rendering herunterzufahren, rufen Sie `RemoteManagerStatic.ShutdownRemoteRendering()` auf.

Nachdem eine `RenderingSession` erstellt und als primäre Renderingsitzung ausgewählt wurde, muss sie bei `RemoteManagerUnity` registriert werden:

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>Vollständiger Beispielcode

Der folgende Code veranschaulicht alle Schritte, die erforderlich sind, um Azure Remote Rendering in Unity zu initialisieren:

```cs
// initialize Remote Rendering
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);

// create a frontend
SessionConfiguration sessionConfig = new SessionConfiguration();
// ... fill out sessionConfig ...
RemoteRenderingClient client = new RemoteRenderingClient(sessionConfig);

// start a session
CreateRenderingSessionResult result = await client.CreateNewRenderingSessionAsync(new RenderingSessionCreationOptions(RenderingSessionVmSize.Standard, 0, 30));
RenderingSession session = result.Session;

// let RemoteManagerUnity know about the session we want to use
RemoteManagerUnity.CurrentSession = session;

await session.ConnectAsync(new RendererInitOptions());

/// When connected, load and modify content

RemoteManagerStatic.ShutdownRemoteRendering();
```

## <a name="convenience-functions"></a>Hilfsfunktionen

### <a name="session-state-events"></a>Sitzungsstatusereignisse

`RemoteManagerUnity.OnSessionUpdate` gibt Ereignisse für die Änderung des Sitzungszustands aus. Weitere Informationen dazu finden Sie in der Codedokumentation.

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity` ist eine optionale Komponente zum Optimieren der Einrichtung und der Sitzungsverwaltung. Sie enthält Optionen zum automatischen Beenden der Sitzung, wenn die Anwendung beendet oder der Wiedergabemodus im Editor beendet wird. Außerdem wird die Sitzungslease bei Bedarf automatisch erneuert. Sie speichert Daten, z. B. die Sitzungseigenschaften (siehe Variable `LastProperties`), und stellt Ereignisse für Sitzungszustandsänderungen und Sitzungsfehler bereit.

Es darf nicht mehr als eine Instanz von `ARRServiceUnity` gleichzeitig vorhanden sein. Dies soll Ihren Einstieg beschleunigen, indem einige allgemeine Funktionen implementiert werden. Für eine größere Anwendung ist es jedoch möglicherweise besser, diese Dinge selbst auszuführen.

Ein Beispiel zum Einrichten und Verwenden von `ARRServiceUnity` finden Sie im [Tutorial: Anzeigen eines remote gerenderten Modells](../../tutorials/unity/view-remote-models/view-remote-models.md).

## <a name="next-steps"></a>Nächste Schritte

* [Installieren des Remote Rendering-Pakets für Unity](install-remote-rendering-unity-package.md)
* [Tutorial: Anzeigen eines remote gerenderten Modells](../../tutorials/unity/view-remote-models/view-remote-models.md)

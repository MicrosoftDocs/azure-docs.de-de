---
title: Integrieren von Remote Rendering in eine C++/DirectX11 Holographic-App
description: Erläutert das Integrieren von Remote Rendering in eine einfache C++/DirectX11 Holographic-App, wie sie mit dem Projekt-Assistenten von Visual Studio erstellt wird
author: florianborn71
ms.author: flborn
ms.date: 05/04/2020
ms.topic: tutorial
ms.openlocfilehash: a07a8a9c50e0f71daa48f65ebf8c2e7a7f166cc5
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594298"
---
# <a name="tutorial-integrate-remote-rendering-into-a-hololens-holographic-app"></a>Tutorial: Integrieren von Remote Rendering in eine HoloLens Holographic-App

In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Verwenden von Visual Studio zum Erstellen einer Holographic-App, die in HoloLens bereitgestellt werden kann
> * Hinzufügen der erforderlichen Codeausschnitte und Projekteinstellungen, um lokales Rendering mit remote gerenderten Inhalten zu kombinieren

Dieses Tutorial legt den Schwerpunkt darauf, einem Beispiel für eine native `Holographic App` den erforderlichen Code hinzuzufügen, um lokales Rendering mit Azure Remote Rendering zu kombinieren. Die einzige Art Statusfeedback in dieser App erfolgt über den Debugausgabebereich in Visual Studio, es empfiehlt sich daher, das Beispiel in Visual Studio zu starten. Das Hinzufügen eines ordnungsgemäßen in die App integrierten Feedbacks würde den Rahmen dieses Beispiels sprengen, da das Erstellen eines dynamischen Bereichs für die Texteingabe von Grund auf eine Menge neu zu erstellenden Code mit sich bringt. Ein guter Ausgangspunkt ist die Klasse `StatusDisplay`, die Teil des [Remoting Player-Beispielprojekts auf GitHub](https://github.com/microsoft/MixedReality-HolographicRemoting-Samples/tree/master/player/common/Content) ist. Tatsächlich verwendet die vorkonfigurierte Version dieses Tutorials eine lokale Kopie dieser Klasse.

> [!TIP]
> Das [ARR-Beispielrepository](https://github.com/Azure/azure-remote-rendering) enthält das Ergebnis dieses Tutorials als gebrauchsfertiges Visual Studio-Projekt. Es ist außerdem dank der UI-Klasse `StatusDisplay` mit ordnungsgemäßer Fehler- und Statusberichterstattung ausgestattet. Im Tutorial wird der Bereich aller ARR-spezifischen Ergänzungen durch `#ifdef USE_REMOTE_RENDERING` / `#endif` definiert, die Remote Rendering-Erweiterungen sind daher leicht zu erkennen.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Ihre Kontoinformationen (Konto-ID, Kontoschlüssel, Kontodomäne, Abonnement-ID). Falls Sie über kein Konto verfügen, [erstellen Sie ein Konto](../../../how-tos/create-an-account.md).
* Windows SDK 10.0.18362.0 [(Download)](https://developer.microsoft.com/windows/downloads/windows-10-sdk).
* Die aktuellste Version von Visual Studio 2019 [(Download)](https://visualstudio.microsoft.com/vs/older-downloads/).
* [Visual Studio-Tools für Mixed Reality](/windows/mixed-reality/install-the-tools). Insbesondere sind die folgenden *Workloadinstallationen* obligatorisch:
  * **Desktopentwicklung mit C++**
  * **Entwicklung für die universelle Windows-Plattform (UWP)**
* Die Windows Mixed Reality-App-Vorlagen für Visual Studio [(Download)](https://marketplace.visualstudio.com/items?itemName=WindowsMixedRealityteam.WindowsMixedRealityAppTemplatesVSIX).

## <a name="create-a-new-holographic-app-sample"></a>Erstellen eines neuen Beispiels für eine Holographic-App

Als ersten Schritt erstellen wir ein Standardbeispiel, das die Basis für die Integration von Remote Rendering bildet. Öffnen Sie Visual Studio, wählen Sie „Neues Projekt erstellen“ aus, und suchen Sie nach „DirectX 11 Holographic-App (Universal Windows) (C++/WinRT)“.

![Erstellen eines neuen Projekts](media/new-project-wizard.png)

Geben Sie einen Projektnamen Ihrer Wahl ein, wählen Sie einen Pfad und dann die Schaltfläche „Erstellen“ aus.
Ändern Sie im neuen Projekt die Konfiguration in **„Debug/ARM64“** . Sie sollten jetzt in der Lage sein, das Projekt zu kompilieren und auf einem verbundenen HoloLens 2-Gerät bereitzustellen. Wenn Sie es auf HoloLens ausführen, sollten Sie vor sich einen rotierenden Würfel sehen.

## <a name="add-remote-rendering-dependencies-through-nuget"></a>Hinzufügen von Remote Rendering-Abhängigkeiten mithilfe von NuGet

Der erste Schritt beim Hinzufügen von Remote Rendering-Funktionen besteht darin, die clientseitigen Abhängigkeiten hinzuzufügen. Die relevanten Abhängigkeiten sind als NuGet-Paket verfügbar.
Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie im Kontextmenü **NuGet-Pakete verwalten...“** aus.

Navigieren Sie in dem nun angezeigten Dialogfeld zum NuGet-Paket mit dem Namen **„Microsoft.Azure.RemoteRendering.Cpp“** :

![Navigieren Sie zum NuGet-Paket,](media/add-nuget.png)

und fügen Sie es dem Projekt hinzu, indem Sie das Paket auswählen und dann auf die Schaltfläche „Installieren“ klicken.

Das NuGet-Paket fügt dem Projekt die Remote Rendering-Abhängigkeiten hinzu. Dies gilt insbesondere in folgenden Fällen:
* Link zur Clientbibliothek (RemoteRenderingClient.lib)
* Einrichten der DLL-Abhängigkeiten
* Festlegen des richtigen Pfads, um das Verzeichnis einzuschließen.

## <a name="project-preparation"></a>Projektvorbereitung

Wir müssen geringfügige Änderungen am vorhandenen Projekt vornehmen. Diese Änderungen sind klein, aber ohne sie würde das Remote Rendering nicht funktionieren.

### <a name="enable-multithread-protection-on-directx-device"></a>Aktivieren des Multithreadschutzes auf dem DirectX-Gerät
Auf dem `DirectX11`-Gerät muss Multithreadschutz aktiviert sein. Um diese Einstellung zu ändern, öffnen Sie die Datei „DeviceResources.cpp“ im Ordner „Common“, und fügen Sie am Ende der Funktion `DeviceResources::CreateDeviceResources()` den folgenden Code ein:

```cpp
// Enable multi thread protection as now multiple threads use the immediate context.
Microsoft::WRL::ComPtr<ID3D11Multithread> contextMultithread;
if (context.As(&contextMultithread) == S_OK)
{
    contextMultithread->SetMultithreadProtected(true);
}
```

### <a name="enable-network-capabilities-in-the-app-manifest"></a>Aktivieren von Netzwerkfunktionen im App-Manifest
Netzwerkfunktionen müssen für die bereitgestellte App explizit aktiviert werden. Wird dies nicht konfiguriert, führen Verbindungsabfragen sonst letztlich zu Timeouts. Doppelklicken Sie zum Aktivieren auf das `package.appxmanifest`-Element im Projektmappen-Explorer. Wechseln Sie auf dem nächsten Bildschirm zur Registerkarte **Funktionen**, und wählen Sie Folgendes aus:
* Internet (Client und Server)
* Internet (Client)

![Netzwerkfunktionen](media/appx-manifest-caps.png)


## <a name="integrate-remote-rendering"></a>Integrieren von Remote Rendering

Da das Projekt jetzt vorbereitet ist, können wir mit dem Code beginnen. Ein guter Einstiegspunkt in die Anwendung ist die Klasse `HolographicAppMain` (Datei „HolographicAppMain.h/cpp“), da sie über alle notwendigen Hooks für Initialisierung, De-Initialisierung und Rendering verfügt.

### <a name="includes"></a>Includes

Wir beginnen, indem wir die erforderlichen Includes hinzufügen. Fügen Sie der Datei „HolographicAppMain.h“ das folgende Include hinzu:

```cpp
#include <AzureRemoteRendering.h>
```

...und diese zusätzlichen `include`-Anweisungen zur Datei „ HolographicAppMain.cpp“:

```cpp
#include <AzureRemoteRendering.inl>
#include <RemoteRenderingExtensions.h>
#include <windows.perception.spatial.h>
```

Um den Code einfach zu halten, definieren wir die folgende Namespaceverknüpfung oben in der Datei „HolographicAppMain.h“ nach den `include`-Anweisungen:

```cpp
namespace RR = Microsoft::Azure::RemoteRendering;
```

Diese Verknüpfung ist nützlich, damit wir nicht überall den gesamten Namespace auszuschreiben brauchen und die ARR-spezifischen Datenstrukturen besser erkennen können. Natürlich könnten wir auch die `using namespace...`-Anweisung verwenden.

### <a name="remote-rendering-initialization"></a>Initialisierung von Remote Rendering
 
Wir müssen während der Lebensdauer der Anwendung ein paar Objekte für die Sitzung usw. aufbewahren. Die Lebensdauer fällt mit der Lebensdauer des `HolographicAppMain`-Objekts der Anwendung zusammen, wir fügen unsere Objekte daher der Klasse `HolographicAppMain` als Member hinzu. Der nächste Schritt besteht darin, der Datei „HolographicAppMain.h“ die folgenden Klassenmember hinzuzufügen:

```cpp
class HolographicAppMain
{
    ...
    // members:
    std::string m_sessionOverride;                // if we have a valid session ID, we specify it here. Otherwise a new one is created
    RR::ApiHandle<RR::RemoteRenderingClient> m_client;  // the client instance
    RR::ApiHandle<RR::RenderingSession> m_session;    // the current remote rendering session
    RR::ApiHandle<RR::RenderingConnection> m_api;       // the API instance, that is used to perform all the actions. This is just a shortcut to m_session->Connection()
    RR::ApiHandle<RR::GraphicsBindingWmrD3d11> m_graphicsBinding; // the graphics binding instance
}
```

Ein guter Ort zum Vornehmen der eigentlichen Implementierung ist der Konstruktor der Klasse `HolographicAppMain`. Wir müssen hier drei Arten von Initialisierung vornehmen:
1. Die einmalige Initialisierung des Remote Rendering-Systems
1. Clienterstellung (Authentifizierung)
1. Sitzungserstellung

All dies erledigen wir nacheinander im Konstruktor. In echten Anwendungsfällen kann es aber sinnvoll sein, diese Schritte einzeln auszuführen.

Fügen Sie den folgenden Code am Anfang des Konstruktor-Textkörpers in der Datei „HolographicAppMain.cpp“ hinzu:

```cpp
HolographicAppMain::HolographicAppMain(std::shared_ptr<DX::DeviceResources> const& deviceResources) :
    m_deviceResources(deviceResources)
{
    // 1. One time initialization
    {
        RR::RemoteRenderingInitialization clientInit;
        clientInit.ConnectionType = RR::ConnectionType::General;
        clientInit.GraphicsApi = RR::GraphicsApiType::WmrD3D11;
        clientInit.ToolId = "<sample name goes here>"; // <put your sample name here>
        clientInit.UnitsPerMeter = 1.0f;
        clientInit.Forward = RR::Axis::NegativeZ;
        clientInit.Right = RR::Axis::X;
        clientInit.Up = RR::Axis::Y;
        if (RR::StartupRemoteRendering(clientInit) != RR::Result::Success)
        {
            // something fundamental went wrong with the initialization
            throw std::exception("Failed to start remote rendering. Invalid client init data.");
        }
    }


    // 2. Create Client
    {
        // Users need to fill out the following with their account data and model
        RR::SessionConfiguration init;
        init.AccountId = "00000000-0000-0000-0000-000000000000";
        init.AccountKey = "<account key>";
        init.RemoteRenderingDomain = "westus2.mixedreality.azure.com"; // <change to the region that the rendering session should be created in>
        init.AccountDomain = "westus2.mixedreality.azure.com"; // <change to the region the account was created in>
        m_modelURI = "builtin://Engine";
        m_sessionOverride = ""; // If there is a valid session ID to re-use, put it here. Otherwise a new one is created
        m_client = RR::ApiHandle(RR::RemoteRenderingClient(init));
    }

    // 3. Open/create rendering session
    {
        auto SessionHandler = [&](RR::Status status, RR::ApiHandle<RR::CreateRenderingSessionResult> result)
        {
            if (status == RR::Status::OK)
            {
                auto ctx = result->GetContext();
                if (ctx.Result == RR::Result::Success)
                {
                    SetNewSession(result->GetSession());
                }
                else
                {
                    SetNewState(AppConnectionStatus::ConnectionFailed, ctx.ErrorMessage.c_str());
                }
            }
            else
            {
                SetNewState(AppConnectionStatus::ConnectionFailed, "failed");
            }
        };

        // If we had an old (valid) session that we can recycle, we call async function m_client->OpenRenderingSessionAsync
        if (!m_sessionOverride.empty())
        {
            m_client->OpenRenderingSessionAsync(m_sessionOverride, SessionHandler);
            SetNewState(AppConnectionStatus::CreatingSession, nullptr);
        }
        else
        {
            // create a new session
            RR::RenderingSessionCreationOptions init;
            init.MaxLeaseInMinutes = 10; // session is leased for 10 minutes
            init.Size = RR::RenderingSessionVmSize::Standard;
            m_client->CreateNewRenderingSessionAsync(init, SessionHandler);
            SetNewState(AppConnectionStatus::CreatingSession, nullptr);
        }
    }

    // Rest of constructor code:
    ...
}
```

Der Code ruft die Memberfunktionen `SetNewSession` und `SetNewState` auf, die Sie im nächsten Absatz zusammen mit dem restlichen Code des Zustandsautomaten implementieren.

Beachten Sie, dass die Anmeldeinformationen im Beispiel hartcodiert sind und an Ort und Stelle eingegeben werden müssen ([Konto-ID, Kontoschlüssel, Kontodomäne](../../../how-tos/create-an-account.md#retrieve-the-account-information) und [Remote Rendering-Domäne](../../../reference/regions.md)).

Am Ende des Destruktor-Textkörpers nehmen wir die De-Initialisierung symmetrisch und in umgekehrter Reihenfolge vor:

```cpp
HolographicAppMain::~HolographicAppMain()
{
    // Existing destructor code:
    ...
    
    // Destroy session:
    if (m_session != nullptr)
    {
        m_session->Disconnect();
        m_session = nullptr;
    }

    // Destroy front end:
    m_client = nullptr;

    // One-time de-initialization:
    RR::ShutdownRemoteRendering();
}
```

## <a name="state-machine"></a>Zustandsautomat

Beim Remote Rendering sind wichtige Funktionen, etwa das Erstellen einer Sitzung und das Laden eines Modells, asynchrone Funktionen. Um dem Rechnung zu tragen, benötigen wir einen einfachen Zustandsautomaten, der den Übergang durch die folgenden Zustände im Wesentlichen automaisch durchläuft:

*Initialisierung > Sitzungserstellung > Sitzungsstart > Laden von Modellen (mit Statusanzeige)*

Entsprechend fügen wir der Klasse im nächsten Schritt Routinen zur Behandlung des Zustandsautomaten hinzu. Wir deklarieren eine eigene Enumeration `AppConnectionStatus` für die verschiedenen Zustände, in denen sich unsere Anwendung befinden kann. Sie ist der `RR::ConnectionStatus` ähnlich, weist aber einen zusätzlichen Zustand für fehlgeschlagene Verbindungen auf.

Fügen Sie der Klassendeklaration die folgenden Member und Funktionen hinzu:

```cpp
namespace HolographicApp
{
    // Our application's possible states:
    enum class AppConnectionStatus
    {
        Disconnected,

        CreatingSession,
        StartingSession,
        Connecting,
        Connected,

        // error state:
        ConnectionFailed,
    };

    class HolographicAppMain
    {
        ...
        // Member functions for state transition handling
        void OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error);
        void SetNewState(AppConnectionStatus state, const char* statusMsg);
        void SetNewSession(RR::ApiHandle<RR::RenderingSession> newSession);
        void StartModelLoading();

        // Members for state handling:

        // Model loading:
        std::string m_modelURI;
        RR::ApiHandle<RR::LoadModelAsync> m_loadModelAsync;

        // Connection state machine:
        AppConnectionStatus m_currentStatus = AppConnectionStatus::Disconnected;
        std::string m_statusMsg;
        RR::Result m_connectionResult = RR::Result::Success;
        RR::Result m_modelLoadResult = RR::Result::Success;
        bool m_isConnected = false;
        bool m_sessionStarted = false;
        RR::ApiHandle<RR::SessionPropertiesAsync> m_sessionPropertiesAsync;
        bool m_modelLoadTriggered = false;
        float m_modelLoadingProgress = 0.f;
        bool m_modelLoadFinished = false;
        double m_timeAtLastRESTCall = 0;
        bool m_needsCoordinateSystemUpdate = true;
    }
```

Fügen Sie der CPP-Datei implementierungsseitig diese Funktionstextkörper hinzu:

```cpp
void HolographicAppMain::StartModelLoading()
{
    m_modelLoadingProgress = 0.f;

    RR::LoadModelFromSasOptions options;
    options.ModelUri = m_modelURI.c_str();
    options.Parent = nullptr;

    // start the async model loading
    m_api->LoadModelFromSasAsync(options,
        // completed callback
        [this](RR::Status status, RR::ApiHandle<RR::LoadModelResult> result)
        {
            m_modelLoadResult = RR::StatusToResult(status);
            m_modelLoadFinished = true; // successful if m_modelLoadResult==RR::Result::Success
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading completed. Result: %s\n", RR::ResultToString(m_modelLoadResult));
            OutputDebugStringA(buffer);
        },
        // progress update callback
            [this](float progress)
        {
            // progress callback
            m_modelLoadingProgress = progress;
            m_needsStatusUpdate = true;
        });
}



void HolographicAppMain::SetNewState(AppConnectionStatus state, const char* statusMsg)
{
    m_currentStatus = state;
    m_statusMsg = statusMsg ? statusMsg : "";

    // Some log for the VS output panel:
    const char* appStatus = nullptr;

    switch (state)
    {
        case AppConnectionStatus::Disconnected: appStatus = "Disconnected"; break;
        case AppConnectionStatus::CreatingSession: appStatus = "CreatingSession"; break;
        case AppConnectionStatus::StartingSession: appStatus = "StartingSession"; break;
        case AppConnectionStatus::Connecting: appStatus = "Connecting"; break;
        case AppConnectionStatus::Connected: appStatus = "Connected"; break;
        case AppConnectionStatus::ConnectionFailed: appStatus = "ConnectionFailed"; break;
    }

    char buffer[1024];
    sprintf_s(buffer, "Remote Rendering: New status: %s, result: %s\n", appStatus, m_statusMsg.c_str());
    OutputDebugStringA(buffer);
}

void HolographicAppMain::SetNewSession(RR::ApiHandle<RR::RenderingSession> newSession)
{
    SetNewState(AppConnectionStatus::StartingSession, nullptr);

    m_sessionStartingTime = m_timeAtLastRESTCall = m_timer.GetTotalSeconds();
    m_session = newSession;
    m_api = m_session->Connection();
    m_graphicsBinding = m_session->GetGraphicsBinding().as<RR::GraphicsBindingWmrD3d11>();
    m_session->ConnectionStatusChanged([this](auto status, auto error)
        {
            OnConnectionStatusChanged(status, error);
        });

};

void HolographicAppMain::OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error)
{
    const char* asString = RR::ResultToString(error);
    m_connectionResult = error;

    switch (status)
    {
    case RR::ConnectionStatus::Connecting:
        SetNewState(AppConnectionStatus::Connecting, asString);
        break;
    case RR::ConnectionStatus::Connected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Connected, asString);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, asString);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = error == RR::Result::Success;
        break;
    case RR::ConnectionStatus::Disconnected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Disconnected, asString);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, asString);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = false;
        break;
    default:
        break;
    }
    
}
```

### <a name="per-frame-update"></a>Update pro Bild

Der Client muss einmal für jeden Takt der Simulation aktualisiert werden. Außerdem müssen einige weitere Statusupdates durchgeführt werden. Die Funktion `HolographicAppMain::Update` bietet einen guten Hook für Updates pro Bild.

#### <a name="state-machine-update"></a>Aktualisierung des Zustandsautomats

Sie müssen den Status der Sitzung abrufen und überprüfen, ob sie in den Status `Ready` übergegangen ist. Wurde eine Verbindung hergestellt, wird das Laden des Modells über `StartModelLoading` gestartet.

Fügen Sie dem Text der Funktion `HolographicAppMain::Update` den folgenden Code hinzu:

```cpp
// Updates the application state once per frame.
HolographicFrame HolographicAppMain::Update()
{
    if (m_session != nullptr)
    {
        // Tick the client to receive messages
        m_api->Update();

        if (!m_sessionStarted)
        {
            // Important: To avoid server-side throttling of the requests, we should call GetPropertiesAsync very infrequently:
            const double delayBetweenRESTCalls = 10.0;

            // query session status periodically until we reach 'session started'
            if (m_sessionPropertiesAsync == nullptr && m_timer.GetTotalSeconds() - m_timeAtLastRESTCall > delayBetweenRESTCalls)
            {
                m_timeAtLastRESTCall = m_timer.GetTotalSeconds();
                m_session->GetPropertiesAsync([this](RR::Status status, RR::ApiHandle<RR::RenderingSessionPropertiesResult> propertiesResult)
                    {
                        if (status == RR::Status::OK)
                        {
                            auto ctx = propertiesResult->GetContext();
                            if (ctx.Result == RR::Result::Success)
                            {
                                auto res = propertiesResult->GetSessionProperties();
                                switch (res.Status)
                                {
                                case RR::RenderingSessionStatus::Ready:
                                {
                                    // The following ConnectAsync is async, but we'll get notifications via OnConnectionStatusChanged
                                    m_sessionStarted = true;
                                    SetNewState(AppConnectionStatus::Connecting, nullptr);
                                    RR::RendererInitOptions init;
                                    init.IgnoreCertificateValidation = false;
                                    init.RenderMode = RR::ServiceRenderMode::Default;
                                    m_session->ConnectAsync(init, [](RR::Status, RR::ConnectionStatus) {});
                                }
                                break;
                                case RR::RenderingSessionStatus::Error:
                                    SetNewState(AppConnectionStatus::ConnectionFailed, "Session error");
                                    break;
                                case RR::RenderingSessionStatus::Stopped:
                                    SetNewState(AppConnectionStatus::ConnectionFailed, "Session stopped");
                                    break;
                                case RR::RenderingSessionStatus::Expired:
                                    SetNewState(AppConnectionStatus::ConnectionFailed, "Session expired");
                                    break;
                                }
                            }
                            else
                            {
                                SetNewState(AppConnectionStatus::ConnectionFailed, ctx.ErrorMessage.c_str());
                            }
                        }
                        else
                        {
                            SetNewState(AppConnectionStatus::ConnectionFailed, "Failed to retrieve session status");
                        }
                        m_sessionPropertiesQueryInProgress = false; // next try
                    });                }
            }
        }
        if (m_isConnected && !m_modelLoadTriggered)
        {
            m_modelLoadTriggered = true;
            StartModelLoading();
        }
    }

    if (m_needsCoordinateSystemUpdate && m_stationaryReferenceFrame && m_graphicsBinding)
    {
        // Set the coordinate system once. This must be called again whenever the coordinate system changes.
        winrt::com_ptr<ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem> ptr{ m_stationaryReferenceFrame.CoordinateSystem().as<ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem>() };
        m_graphicsBinding->UpdateUserCoordinateSystem(ptr.get());
        m_needsCoordinateSystemUpdate = false;
    }

    // Rest of the body:
    ...
}
```

#### <a name="coordinate-system-update"></a>Aktualisierung des Koordinatensystems

Sie müssen sich mit dem Renderingdienst auf ein Koordinatensystem einigen, das verwendet werden soll. Für den Zugriff auf das zu verwendende Koordinatensystem benötigen Sie das `m_stationaryReferenceFrame`-Element, das am Ende der Funktion `HolographicAppMain::OnHolographicDisplayIsAvailableChanged` erstellt wird.

Dieses Koordinatensystem ändert sich in der Regel nicht, sodass dies eine einmalige Initialisierung ist. Sie muss erneut aufgerufen werden, wenn die Anwendung das Koordinatensystem ändert.

Mit dem obigen Code wird das Koordinatensystem einmal innerhalb der Funktion `Update` festgelegt, sobald Sie über ein Referenzkoordinatensystem und eine verbundene Sitzung verfügen.

#### <a name="camera-update"></a>Kameraaktualisierung

Sie müssen die Clippingebenen der Kamera aktualisieren, damit die Serverkamera mit der lokalen Kamera synchronisiert wird. Dieser Schritt kann ganz am Ende der Funktion `Update` ausgeführt werden:

```cpp
    ...
    if (m_isConnected)
    {
        // Any near/far plane values of your choosing.
        constexpr float fNear = 0.1f;
        constexpr float fFar = 10.0f;
        for (HolographicCameraPose const& cameraPose : prediction.CameraPoses())
        {
            // Set near and far to the holographic camera as normal
            cameraPose.HolographicCamera().SetNearPlaneDistance(fNear);
            cameraPose.HolographicCamera().SetFarPlaneDistance(fFar);
        }

        // The API to inform the server always requires near < far. Depth buffer data will be converted locally to match what is set on the HolographicCamera.
        auto settings = m_api->GetCameraSettings();
        settings->SetNearAndFarPlane(std::min(fNear, fFar), std::max(fNear, fFar));
        settings->SetEnableDepth(true);
    }

    // The holographic frame will be used to get up-to-date view and projection matrices and
    // to present the swap chain.
    return holographicFrame;
}

```

### <a name="rendering"></a>Darstellung

Als letzten Schritt müssen wir das Rendering der Remoteinhalte aufrufen. Sie müssen diesen Aufruf an genau der richtigen Position in der Renderingpipeline ausführen, und zwar nach dem Löschen des Renderziels und dem Festlegen des Viewports. Fügen Sie der Sperre `UseHolographicCameraResources` innerhalb von Funktion `HolographicAppMain::Render` den folgenden Codeausschnitt hinzu:

```cpp
        ...
        // Existing clear function:
        context->ClearDepthStencilView(depthStencilView, D3D11_CLEAR_DEPTH | D3D11_CLEAR_STENCIL, 1.0f, 0);
        
        // ...

        // Existing check to test for valid camera:
        bool cameraActive = pCameraResources->AttachViewProjectionBuffer(m_deviceResources);


        // Inject remote rendering: as soon as we are connected, start blitting the remote frame.
        // We do the blitting after the Clear, and before cube rendering.
        if (m_isConnected && cameraActive)
        {
            m_graphicsBinding->BlitRemoteFrame();
        }

        ...
```

## <a name="run-the-sample"></a>Ausführen des Beispiels

Das Beispiel sollte jetzt einen Zustand erreicht haben, in dem es sich kompilieren und ausführen lässt.

Wenn das Beispiel ordnungsgemäß ausgeführt wird, zeigt es den rotierenden Würfel direkt vor Ihnen, und nach einiger Zeit für die Sitzungserstellung und das Laden des Modells wird das Engine-Modell an der aktuellen Kopfposition wiedergegeben. Sitzungserstellung und Laden des Modells können mehrere Minuten in Anspruch nehmen. Der aktuelle Status wird nur in den Ausgabebereich von Visual Studio geschrieben. Daher wird empfohlen, das Beispiel aus Visual Studio zu starten.

> [!CAUTION]
> Der Client trennt die Serververbindung, wenn die Taktfunktion einige Sekunden lang nicht aufgerufen wird. Das Auslösen von Haltepunkten kann also sehr leicht zu einem Trennen der Verbindung führen.

Eine ordnungsgemäße Statusanzeige mit einem Textbereich finden Sie in der vorkonfigurierten Version dieses Tutorials auf GitHub.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie alle erforderlichen Schritte zum Hinzufügen von Remote Rendering zu einem standardmäßigen C++/DirectX11-Beispiel einer **Holographic-App** kennengelernt.
Informationen zum Konvertieren Ihres eigenen Modells finden Sie in der folgenden Schnellstartanleitung:

> [!div class="nextstepaction"]
> [Schnellstart: Konvertieren eines Modells für das Rendering](../../../quickstarts/convert-model.md)
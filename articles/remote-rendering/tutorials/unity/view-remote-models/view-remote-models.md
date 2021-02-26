---
title: Anzeigen eines per Remotezugriff gerenderten Modells
description: Das „Hallo Welt“-Tutorial zu Azure Remote Rendering zeigt, wie ein per Remotezugriff von Azure gerendertes Modell angezeigt werden kann.
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: bfcd1e600c722cf3a4951da60097c7c373f9b1a6
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592040"
---
# <a name="tutorial-viewing-a-remotely-rendered-model"></a>Tutorial: Anzeigen eines per Remotezugriff gerenderten Modells

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Bereitstellen einer Azure Remote Rendering-Instanz (ARR)
> * Erstellen und Beenden einer Renderingsitzung
> * Erneutes Verwenden einer vorhandenen Renderingsitzung
> * Herstellen und Trennen von Verbindungen mit Sitzungen
> * Laden von Modellen in eine Renderingsitzung

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Ein aktives Azure-Abonnement mit nutzungsbasierter Bezahlung, [Konto erstellen](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)
* Windows SDK 10.0.18362.0 [(herunterladen)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Aktuelle Version von Visual Studio 2019 [(herunterladen)](https://visualstudio.microsoft.com/vs/older-downloads/)
* Git [(herunterladen)](https://git-scm.com/downloads)
* Unity, die aktuelle Version von 2019.3, wir empfehlen die Verwendung von Unity Hub hierfür [(herunterladen)](https://unity3d.com/get-unity/download)
  * Installieren Sie in Unity diese Module:
    * **UWP**: Buildunterstützung für Universelle Windows-Plattform
    * **IL2CPP**: Windows-Buildunterstützung (IL2CPP)
* Grundkenntnisse von Unity und der Sprache C# (z. B. Erstellen von Skripts und Objekten, Verwenden von Prefabs, Konfigurieren von Unity-Ereignissen usw.)

## <a name="provision-an-azure-remote-rendering-arr-instance"></a>Bereitstellen einer Azure Remote Rendering-Instanz (ARR)

Sie müssen zunächst ein [Konto erstellen](../../../how-tos/create-an-account.md#create-an-account), um Zugriff auf Azure Remote Rendering zu erhalten.

## <a name="create-a-new-unity-project"></a>Erstellen eines neuen Unity-Projekts

> [!TIP]
> Das [ARR-Beispielrepository](https://github.com/Azure/azure-remote-rendering) enthält ein Projekt, in dem alle Tutorials abgeschlossen sind, dieses kann als Referenz verwendet werden. Suchen Sie unter *Unity\Tutorial-Complete* nach dem gesamten Unity-Projekt.

Erstellen Sie im Unity Hub ein neues Projekt.
Bei diesem Beispiel gehen wir davon aus, dass das Projekt in einem Ordner namens **RemoteRendering** erstellt wird.

:::image type="content" source="./media/unity-new-project.PNG" alt-text="Neues Unity-Projekt":::

## <a name="include-the-azure-remote-rendering-package"></a>Einbeziehen des Azure Remote Rendering-Pakets

Sie müssen die Datei `Packages/manifest.json` ändern, die sich in Ihrem Unity-Projektordner befindet. Öffnen Sie die Datei in einem Text-Editor, und fügen Sie oben im Manifest die folgenden Zeilen hinzu:

```json
{
    "scopedRegistries": [
    {
        "name": "Azure Mixed Reality Services",
        "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
        "scopes": ["com.microsoft.azure"]
    }
    ],
    "dependencies": {
        "com.unity.render-pipelines.universal": "7.3.1",
        "com.microsoft.azure.remote-rendering": "0.1.31",
        ...existing dependencies...
    }
}
```

Nachdem Sie das Manifest geändert und gespeichert haben, wird Unity automatisch aktualisiert. Vergewissern Sie sich, dass die Pakete in das Fenster *Project* (Projekt) geladen wurden:

:::image type="content" source="./media/confirm-packages.png" alt-text="Bestätigen des Imports von Paketen":::

Wenn Ihre Pakete nicht geladen werden, überprüfen Sie die Unity-Konsole auf Fehler. Wenn keine Fehler vorliegen und die Pakete im Ordner **Packages** (Pakete) nach wie vor nicht angezeigt werden, überprüfen Sie die Umschaltfläche zur Paketsichtbarkeit.\
![Screenshot: Pfeil, der auf die Umschaltfläche für die Paketsichtbarkeit zeigt](./media/unity-package-visibility.png)

## <a name="ensure-you-have-the-latest-version-of-the-package"></a>Stellen Sie sicher, dass Sie über die neueste Version des Pakets verfügen.

Die folgenden Schritte stellen sicher, dass das Projekt die neueste Version des Remoterenderingpakets verwendet.

1. Öffnen Sie im oberen Menü des Unity-Editors *Fenster > Paket-Manager* (Fenster > Paket-Manager).
1. Wählen Sie das Paket **Microsoft Azure Remote Rendering** aus.
1. Überprüfen Sie auf der Seite mit dem Paket-Manager für das Paket **Microsoft Azure Remote Rendering**, ob die Schaltfläche **Update** (Aktualisieren) verfügbar ist. Falls ja, wird das Paket durch Klicken auf diese Schaltfläche in die neueste verfügbare Version geändert:\
![Das ARR-Paket im Paket-Manager](./media/package-manager.png)
1. Das Aktualisieren des Pakets kann gelegentlich zu Konsolenfehlern führen. Wenn dies auftritt, schließen Sie das Projekt und öffnen es erneut.
1. Wenn das Paket aktualisiert wurde, zeigt der Paket-Manager anstelle der Aktualisierungsschaltfläche **Up to date** (Aktuell) an.\
![Aktuelles Paket](./media/package-up-to-date.png)
## <a name="configure-the-camera"></a>Konfigurieren der Kamera

1. Wählen Sie den Knoten **Main Camera** (Hauptkamera) aus.

1. Öffnen Sie das Kontextmenü, indem Sie mit der rechten Maustaste auf die Komponente *Transform* (Transformation) klicken und die Option **Reset** (Zurücksetzen) auswählen:

    ![Zurücksetzen der Kameratransformation](./media/camera-reset-transform.png)

1. Legen Sie **Clear flags** (Kennzeichnungen löschen) auf *Solid Color* (Volltonfarbe) fest.

1. Legen Sie **Background** (Hintergrund) auf *Black* (Schwarz) (#000000) mit vollständig transparent (0) Alpha (A) fest.

    ![Farbenrad](./media/color-wheel-black.png)

1. Legen Sie **Clipping Planes** (Clippingebenen) auf *Near (Nahe) = 0,3* und *Far (Entfernt) = 20* fest. Das bedeutet, dass beim Rendern Geometrien beschnitten werden, die näher als 30 cm oder entfernter als 20 Meter sind.

    ![Unity-Kameraeigenschaften](./media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>Anpassen der Projekteinstellungen

1. Öffnen Sie *Edit > Project Settings...* (Bearbeiten > Projekteinstellungen).
1. Wählen Sie im Menü auf der linken Seite **Quality** (Qualität) aus.
1. Ändern Sie **Default Quality Level** (Standardqualitätsstufe) für alle Plattformen in *Low* (Niedrig). Diese Einstellung ermöglicht eine effizientere Darstellung von lokalem Inhalt und wirkt sich nicht auf die Qualität von per Remotezugriff gerendertem Inhalt aus.

    ![Ändern der Qualitätseinstellungen des Projekts](./media/settings-quality.png)

1. Wählen Sie im Menü auf der linken Seite **Graphics** (Grafik) aus.
1. Ändern Sie die Einstellung **Scriptable Rendering Pipeline** (Skriptfähige Renderingpipeline) in *HybridRenderingPipeline*.\
    ![Screenshot: Veranschaulichung der Option zum Ändern der Einstellung „Scriptable Render Pipeline settings“ (Skriptfähige Renderingpipeline) in „HybridRenderingPipeline“](./media/settings-graphics-render-pipeline.png)\
    Manchmal füllt die Benutzeroberfläche die Liste der verfügbaren Pipelinetypen aus den Paketen nicht auf. In diesem Fall muss das Objekt *HybridRenderingPipeline* manuell auf das Feld gezogen werden:\
    ![Ändern der Projektgrafikeinstellungen](./media/hybrid-rendering-pipeline.png)

    > [!NOTE]
    > Wenn Sie das Objekt *HybridRenderingPipeline* nicht in das Feld „Rendering Pipeline Asset“ (Pipelineobjekt rendern) ziehen und dort ablegen können (möglicherweise weil das Feld nicht vorhanden ist), stellen Sie sicher, dass die Paketkonfiguration das Paket `com.unity.render-pipelines.universal` enthält.

1. Wählen Sie im Menü auf der linken Seite **Player** aus.
1. Wählen Sie die Registerkarte **Universal Windows Platform settings** (UWP-Einstellungen) aus, die als Windows-Symbol dargestellt ist.
1. Ändern Sie **XR Settings** (XR-Einstellungen) so, dass Windows Mixed Reality wie im Folgenden angezeigt unterstützt wird:
    1. Aktivieren Sie **Virtual Reality Supported** (Unterstützung für virtuelle Realität).
    1. Betätigen Sie die Schaltfläche „+“, und fügen Sie **Windows Mixed Reality** hinzu.
    1. Legen Sie **Depth Format** (Tiefenformat) auf *16-Bit Depth* (16-Bit-Tiefe) fest.
    1. Stellen Sie sicher, dass **Depth Buffer Sharing** (Tiefenpufferfreigabe) aktiviert ist.
    1. Legen Sie **Stereo Rendering Mode** (Stereorenderingmodus) auf *Single Pass Instanced* (Einzeldurchlauf instanziiert) fest.

    ![Playereinstellungen](./media/xr-player-settings.png)

1. Erweitern Sie im selben Fenster oberhalb von **XR Settings** (XR-Einstellungen) die Option **Publishing Settings** (Veröffentlichungseinstellungen).
1. Scrollen Sie nach unten zu **Capabilities** (Funktionen), und wählen Sie Folgendes aus:
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception**
    * **PrivateNetworkClientServer** (*optional*). Wählen Sie diese Option aus, wenn Sie den Unity-Remotedebugger mit Ihrem Gerät verbinden möchten.

1. Aktivieren Sie unter **Supported Device Families** (Unterstützte Gerätefamilien) sowohl **Holographic** als auch **Desktop**.
1. Schließen Sie das Panel **Project Settings** (Projekteinstellungen), oder docken Sie es an.
1. Öffnen Sie *File->Build Settings* (Datei > Buildeinstellungen).
1. Wählen Sie **Universal Windows Platform** (Universelle Windows-Plattform) aus.
1. Konfigurieren Sie die Einstellungen so, dass Sie den unten aufgeführten entsprechen.
1. Klicken Sie auf die Schaltfläche **Switch Platform** (Plattform wechseln).\
![Buildeinstellungen](./media/build-settings.png)
1. Nachdem Unity die Plattformen geändert hat, schließen Sie das Buildpanel.

## <a name="validate-project-setup"></a>Überprüfen der Projekteinrichtung

Führen Sie die folgenden Schritte aus, um zu überprüfen, ob die Projekteinstellungen stimmen.

1. Wählen Sie auf der Symbolleiste des Unity-Editors im Menü **RemoteRendering** den Eintrag **ValidateProject** aus.
1. Überprüfen Sie das Fenster **ValidateProject** auf Fehler und beheben Sie ggf. die Projekteinstellungen.

    ![Projektüberprüfung im Unity-Editor](./media/remote-render-unity-validation.png)

## <a name="create-a-script-to-coordinate-azure-remote-rendering-connection-and-state"></a>Erstellen eines Skripts zum Koordinieren der Azure Remote Rendering-Verbindung und des Zustands

Es gibt vier grundlegende Phasen zum Anzeigen von per Remotezugriff gerenderten Modellen, die im Flussdiagramm unten beschrieben werden. Jede Phase muss in der richtigen Reihenfolge ausgeführt werden. Der nächste Schritt besteht darin, ein Skript zu erstellen, das den Anwendungszustand verwaltet und die einzelnen erforderlichen Phasen durchläuft.

![ARR-Stapel 0](./media/remote-render-stack-0.png)

1. Erstellen Sie im Bereich *Project* (Projekt) unter **Assets** (Objekte) einen neuen Ordner mit dem Namen *RemoteRenderingCore*. Erstellen Sie anschließend in *RemoteRenderingCore* einen weiteren Ordner mit dem Namen *Scripts* (Skripts).

1. Erstellen Sie ein [neues C#-Skript](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) mit dem Namen **RemoteRenderingCoordinator**.
Ihr Projekt sollte wie folgt aussehen:

    ![Projekthierarchie](./media/project-structure.png)

    Dieses Koordinatorskript verfolgt den Remote Rendering-Zustand nach und verwaltet ihn. Beachten Sie, dass ein Teil dieses Codes für die Aufrechterhaltung des Zustands, das Verfügbarmachen von Funktionen für andere Komponenten, das Auslösen von Ereignissen und das Speichern anwendungsspezifischer Daten verwendet wird, die nicht *direkt* mit Azure Remote Rendering in Verbindung stehen. Verwenden Sie den folgenden Code als Ausgangspunkt, und wir behandeln und implementieren den spezifischen Azure Remote Rendering-Code später in diesem Tutorial.

1. Öffnen Sie **RemoteRenderingCoordinator** im Code-Editor, und ersetzen Sie den gesamten Inhalt durch den folgenden Code:

```cs
// Copyright (c) Microsoft Corporation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Linq;
using System.Threading.Tasks;
using UnityEngine;
using UnityEngine.Events;

#if UNITY_WSA
using UnityEngine.XR.WSA;
#endif

/// <summary>
/// Remote Rendering Coordinator is the controller for all Remote Rendering operations.
/// </summary>

// Require the GameObject with a RemoteRenderingCoordinator to also have the ARRServiceUnity component
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRenderingCoordinator : MonoBehaviour
{
    public enum RemoteRenderingState
    {
        NotSet,
        NotInitialized,
        NotAuthorized,
        NoSession,
        ConnectingToExistingRemoteSession,
        ConnectingToNewRemoteSession,
        RemoteSessionReady,
        ConnectingToRuntime,
        RuntimeConnected
    }

    public static RemoteRenderingCoordinator instance;

    // AccountDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // The list of regions is available at https://docs.microsoft.com/azure/remote-rendering/reference/regions
    [SerializeField]
    private string accountDomain = "westus2.mixedreality.azure.com";
    public string AccountDomain
    {
        get => accountDomain.Trim();
        set => accountDomain = value;
    }

    [Header("Development Account Credentials")]
    [SerializeField]
    private string accountId = "<enter your account id here>";
    public string AccountId {
        get => accountId.Trim();
        set => accountId = value;
    }

    [SerializeField]
    private string accountAuthenticationDomain = "<enter your account authentication domain here>";
    public string AccountAuthenticationDomain
    {
        get => accountAuthenticationDomain.Trim();
        set => accountAuthenticationDomain = value;
    }   

    [SerializeField]
    private string accountKey = "<enter your account key here>";
    public string AccountKey {
        get => accountKey.Trim();
        set => accountKey = value;
    }

    // These settings are important. All three should be set as low as possible, while maintaining a good user experience
    // See the documentation around session management and the technical differences in session VM size
    [Header("New Session Defaults")]

    public RenderingSessionVmSize renderingSessionVmSize = RenderingSessionVmSize.Standard;
    public uint maxLeaseHours = 0;
    public uint maxLeaseMinutes = 20;

    [Header("Other Configuration")]

    [Tooltip("If you have a known active SessionID, you can fill it in here before connecting")]
    [SerializeField]
    private string sessionIDOverride;
    public string SessionIDOverride {
        get => sessionIDOverride.Trim();
        set => sessionIDOverride = value;
    }

    // When Automatic Mode is true, the coordinator will attempt to automatically proceed through the process of connecting and loading a model
    public bool automaticMode = true;

    public event Action RequestingAuthorization;
    public UnityEvent OnRequestingAuthorization = new UnityEvent();

    public event Action AuthorizedChanged;
    public UnityEvent OnAuthorizationChanged = new UnityEvent();
    private bool authorized;
    public bool Authorized
    {
        get => authorized;
        set
        {
            if (value == true) //This is a one-way value, once we're authorized it lasts until the app is shutdown.
            {
                authorized = value;
                AuthorizedChanged?.Invoke();
            }
        }
    }

    public delegate Task<SessionConfiguration> AccountInfoGetter();

    public static AccountInfoGetter ARRCredentialGetter
    {
        private get;
        set;
    }

    private RemoteRenderingState currentCoordinatorState = RemoteRenderingState.NotSet;
    public RemoteRenderingState CurrentCoordinatorState
    {
        get => currentCoordinatorState;
        private set
        {
            if (currentCoordinatorState != value)
            {
                currentCoordinatorState = value;
                Debug.Log($"State changed to: {currentCoordinatorState}");
                CoordinatorStateChange?.Invoke(currentCoordinatorState);
            }
        }
    }

    public static event Action<RemoteRenderingState> CoordinatorStateChange;

    public static RenderingSession CurrentSession => instance?.ARRSessionService?.CurrentActiveSession;

    private ARRServiceUnity arrSessionService;

    private ARRServiceUnity ARRSessionService
    {
        get
        {
            if (arrSessionService == null)
                arrSessionService = GetComponent<ARRServiceUnity>();
            return arrSessionService;
        }
    }

    private async Task<SessionConfiguration> GetDevelopmentCredentials()
    {
        Debug.LogWarning("Using development credentials! Not recommended for production.");
        return await Task.FromResult(new SessionConfiguration(AccountAuthenticationDomain, AccountDomain, AccountId, AccountKey));
    }

    /// <summary>
    /// Keep the last used SessionID, when launching, connect to this session if its available
    /// </summary>
    private string LastUsedSessionID
    {
        get
        {
            if (!string.IsNullOrEmpty(SessionIDOverride))
                return SessionIDOverride;

            if (PlayerPrefs.HasKey("LastUsedSessionID"))
                return PlayerPrefs.GetString("LastUsedSessionID");
            else
                return null;
        }
        set
        {
            PlayerPrefs.SetString("LastUsedSessionID", value);
        }
    }

    public void Awake()
    {
        //Forward events to Unity events
        RequestingAuthorization += () => OnRequestingAuthorization?.Invoke();
        AuthorizedChanged += () => OnAuthorizationChanged?.Invoke();

        //Attach to event
        AuthorizedChanged += RemoteRenderingCoordinator_AuthorizedChanged;

        if (instance == null)
            instance = this;
        else
            Destroy(this);

        CoordinatorStateChange += AutomaticMode;

        CurrentCoordinatorState = RemoteRenderingState.NotInitialized;
    }

    private void RemoteRenderingCoordinator_AuthorizedChanged()
    {
        if (CurrentCoordinatorState != RemoteRenderingState.NotAuthorized)
            return; //This isn't valid from any other state than NotAuthorized


        //We just became authorized to connect to Azure
        InitializeSessionService();
    }

    /// <summary>
    /// Automatic mode attempts to automatically progress through the connection and loading steps. Doesn't handle error states.
    /// </summary>
    /// <param name="currentState">The current state</param>
    private async void AutomaticMode(RemoteRenderingState currentState)
    {
        if (!automaticMode)
            return;

        //Add a small delay for visual effect
        await Task.Delay(1500);
        switch (currentState)
        {
            case RemoteRenderingState.NotInitialized:
                InitializeARR();
                break;
            case RemoteRenderingState.NotAuthorized:
                RequestAuthorization();
                break;
            case RemoteRenderingState.NoSession:
                JoinRemoteSession();
                break;
            case RemoteRenderingState.RemoteSessionReady:
                ConnectRuntimeToRemoteSession();
                break;
        }
    }

    /// <summary>
    /// Initializes ARR, associating the main camera
    /// Note: This must be called on the main Unity thread
    /// </summary>
    public void InitializeARR()
    {
        //Implement me
    }

    /// <summary>
    /// Create a new remote session manager
    /// If the ARRCredentialGetter is set, use it as it, otherwise use the development credentials 
    /// </summary>
    public async void InitializeSessionService()
    {
        //Implement me
    }

    /// <summary>
    /// Trigger the event for checking authorization, respond to this event by prompting the user for authentication
    /// If authorized, set Authorized = true
    /// </summary>
    public void RequestAuthorization()
    {
        RequestingAuthorization?.Invoke();
    }

    public void BypassAuthorization()
    {
        Authorized = true;
    }

    /// <summary>
    /// Attempts to join an existing session or start a new session
    /// </summary>
    public async void JoinRemoteSession()
    {
        //Implement me
    }

    public void StopRemoteSession()
    {
        //Implement me
    }

    private async Task<bool> IsSessionAvailable(string sessionID)
    {
        var allSessions = await ARRSessionService.Client.GetCurrentRenderingSessionsAsync();
        return allSessions.SessionProperties.Any(x => x.Id == sessionID && (x.Status == RenderingSessionStatus.Ready || x.Status == RenderingSessionStatus.Starting));
    }

    /// <summary>
    /// Connects the local runtime to the current active session, if there's a session available
    /// </summary>
    public void ConnectRuntimeToRemoteSession()
    {
        //Implement me
    }

    public void DisconnectRuntimeFromRemoteSession()
    {
        //Implement me
    }

    /// <summary>
    /// The session must have its runtime pump updated.
    /// The Connection.Update() will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
    /// </summary>
    private void LateUpdate()
    {
        ARRSessionService?.CurrentActiveSession?.Connection?.Update();
    }

    /// <summary>
    /// Loads a model into the remote session for rendering
    /// </summary>
    /// <param name="modelPath">The model's path</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <returns>An awaitable Remote Rendering Entity</returns>
    public async Task<Entity> LoadModel(string modelPath, Transform parent = null, Action<float> progress = null)
    {
        //Implement me
        return null;
    }

    private async void OnRemoteSessionStatusChanged(ARRServiceUnity caller, RenderingSession session)
    {
        var properties = await session.GetPropertiesAsync();

        switch (properties.SessionProperties.Status)
        {
            case RenderingSessionStatus.Error:
            case RenderingSessionStatus.Expired:
            case RenderingSessionStatus.Stopped:
            case RenderingSessionStatus.Unknown:
                CurrentCoordinatorState = RemoteRenderingState.NoSession;
                break;
            case RenderingSessionStatus.Starting:
                CurrentCoordinatorState = RemoteRenderingState.ConnectingToNewRemoteSession;
                break;
            case RenderingSessionStatus.Ready:
                CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
                break;
        }
    }

    private void OnLocalRuntimeStatusChanged(ConnectionStatus status, Result error)
    {
        switch (status)
        {
            case ConnectionStatus.Connected:
                CurrentCoordinatorState = RemoteRenderingState.RuntimeConnected;
                break;
            case ConnectionStatus.Connecting:
                CurrentCoordinatorState = RemoteRenderingState.ConnectingToRuntime;
                break;
            case ConnectionStatus.Disconnected:
                CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
                break;
        }
    }
}
```

## <a name="create-the-azure-remote-rendering-gameobject"></a>Erstellen Des Azure Remote Rendering-GameObject-Elements

Der Remote Rendering-Koordinator und das erforderliche Skript (*ARRServiceUnity*) sind beide MonoBehaviours-Elemente, die an ein GameObject-Element in der Szene angefügt werden müssen. Das Skript *ARRServiceUnity* wird von ARR bereitgestellt, um die Funktionen von ARR verfügbar zu machen, mit denen eine Verbindung mit Remotesitzungen hergestellt wird und diese verwaltet werden.

1. Erstellen Sie ein neues GameObject-Element in der Szene (STRG + UMSCHALT + N oder *GameObject->Creaty Empy* (Leer erstellen)), und nennen Sie es **RemoteRenderingCoordinator**.
1. Fügen Sie das Skript *RemoteRenderingCoordinator* zum GameObject-Element **RemoteRenderingCoordinator** hinzu.\
![Hinzufügen der RemoteRenderingCoordinator-Komponente](./media/add-coordinator-script.png)
1. Überprüfen Sie, ob das Skript *ARRServiceUnity*, das als *Service* (Dienst) im Inspektor angezeigt wird, automatisch zum GameObject-Element hinzugefügt wird. Wenn Sie sich unsicher sind, ist dies ein Ergebnis mit `[RequireComponent(typeof(ARRServiceUnity))]` am Anfang des **RemoteRenderingCoordinator**-Skripts.
1. Fügen Sie Ihre Azure Remote Rendering-Anmeldeinformationen, Ihre Kontoauthentifizierungsdomäne und die Kontodomäne zum Koordinatorskript hinzu:\
![Hinzufügen Ihrer Anmeldeinformationen](./media/configure-coordinator-script.png)

## <a name="initialize-azure-remote-rendering"></a>Initialisieren von Azure Remote Rendering

Nachdem wir nun über das Framework für den Koordinator verfügen, implementieren wir jede der vier Phasen, beginnend mit dem **Initialisieren von Remote Rendering**.

![ARR-Stapel 1](./media/remote-render-stack-1.png)

**Initialisieren** weist Azure Remote Rendering an, welches Kameraobjekt zum Rendern verwendet wird, und versetzt den Zustandsautomaten in **NotAuthorized**. Dies bedeutet, dass es initialisiert wurde, aber noch nicht autorisiert ist, eine Verbindung mit einer Sitzung herzustellen. Da das Starten einer ARR-Sitzung Kosten verursacht, muss bestätigt werden, dass der Benutzer den Vorgang fortsetzen möchte.

Beim Erreichen des Zustands **NotAuthorized** wird **CheckAuthorization** aufgerufen, wodurch das Ereignis **RequestingAuthorization** aufgerufen und festgelegt wird, welche Kontoanmeldeinformationen verwendet werden sollen (**AccountInfo** wird in der Nähe des oberen Rands der Klasse definiert und verwendet die Anmeldeinformationen, die Sie im obigen Schritt über den Unity Inspector definiert haben).

   > [!NOTE]
   > Die Neukompilierung der Runtime wird von ARR nicht unterstützt. Wenn Sie das Skript ändern und speichern, solange der Wiedergabemodus aktiv ist, kann es zum Einfrieren von Unity kommen. Sie sind dann gezwungen, das Skript über den Task-Manager herunterzufahren. Stellen Sie immer sicher, dass Sie den Wiedergabemodus beendet haben, bevor Sie Ihre Skripts bearbeiten.

1. Ersetzen Sie den Inhalt von **InitializeARR** und **InitializeSessionService** durch den folgenden abgeschlossenen Code:

 ```cs
/// <summary>
/// Initializes ARR, associating the main camera
/// Note: This must be called on the main Unity thread
/// </summary>
public void InitializeARR()
{
    RemoteManagerUnity.InitializeManager(new RemoteUnityClientInit(Camera.main));

    CurrentCoordinatorState = RemoteRenderingState.NotAuthorized;
}

/// <summary>
/// Create a new remote session manager
/// If the ARRCredentialGetter is set, use it as it, otherwise use the development credentials 
/// </summary>
public async void InitializeSessionService()
{
    if (ARRCredentialGetter == null)
        ARRCredentialGetter = GetDevelopmentCredentials;

    var accountInfo = await ARRCredentialGetter.Invoke();

    ARRSessionService.OnSessionStatusChanged += OnRemoteSessionStatusChanged;

    ARRSessionService.Initialize(accountInfo);

    CurrentCoordinatorState = RemoteRenderingState.NoSession;
}
```

Um von **NotAuthorized** zu **NoSession** zu gelangen, stellen wir den Benutzern normalerweise ein modales Dialogfeld zur Verfügung, in denen sie eine Auswahl treffen können (das behandeln wir in einem anderen Kapitel). Derzeit umgehen wir die Autorisierungsüberprüfung durch Aufrufen von **ByPassAuthentication**, sobald das Ereignis **RequestingAuthorization** ausgelöst wird.

1. Wählen Sie das GameObject-Element **RemoteRenderingCoordinator** aus, und suchen Sie nach dem Unity-Ereignis **OnRequestingAuthorization**, das im Inspektor der Komponente **RemoteRenderingCoordinator** verfügbar gemacht wird.

1. Fügen Sie ein neues Ereignis hinzu, indem Sie in der unteren rechten Ecke auf „+“ klicken.
1. Ziehen Sie die Komponente auf ihr eigenes Ereignis, damit sie auf sich selbst verweist.\
![Umgehen der Authentifizierung](./media/bypass-authorization-add-event.png)\
1. Wählen Sie in der Dropdownliste **RemoteRenderingCoordinator > BypassAuthorization** aus.\
![Screenshot: Option „RemoteRenderingCoordinator.BypassAuthorization“](./media/bypass-authorization-event.png)

## <a name="create-or-join-a-remote-session"></a>Erstellen einer Remotesitzung oder Teilnehmen

Die zweite Phase besteht darin, eine Remote Rendering-Sitzung zu erstellen oder an ihr teilzunehmen (weitere Informationen finden Sie unter [Remote Rendering-Sitzungen](../../../concepts/sessions.md)).

![ARR-Stapel 2](./media/remote-render-stack-2.png)

In der Remotesitzung werden die Modelle gerendert. Die Methode **JoinRemoteSession( )** versucht, an einer vorhandenen Sitzung teilzunehmen, die mit der Eigenschaft **LastUsedSessionID** nachverfolgt wird, oder – wenn eine aktive Sitzungs-ID zugewiesen ist – mit **SessionIDOverride**. **SessionIDOverride** ist nur für Debuggingzwecke gedacht und sollte nur verwendet werden, wenn Sie wissen, dass die Sitzung vorhanden ist und Sie explizit eine Verbindung mit ihr herstellen möchten.

Wenn keine Sitzungen verfügbar sind, wird eine neue Sitzung erstellt. Das Erstellen einer neuen Sitzung ist allerdings ein zeitaufwendiger Vorgang. Daher sollten Sie versuchen, nur dann Sitzungen zu erstellen, wenn dies erforderlich ist, und sie nach Möglichkeit wiederzuverwenden (weitere Informationen zum Verwalten von Sitzungen finden Sie unter [Anwendungen für gewerbliche Zwecke: Sitzungspools, Planung und bewährte Methoden](../commercial-ready/commercial-ready.md#fast-startup-time-strategies)).

> [!TIP]
> **StopRemoteSession()** beendet die aktive Sitzung. Um nicht erforderliche Kosten zu vermeiden, sollten Sie Sitzungen immer dann beenden, wenn sie nicht mehr benötigt werden.

Der Zustandsautomat wechselt nun in Abhängigkeit von den verfügbaren Sitzungen entweder zu **ConnectingToNewRemoteSession** oder **ConnectingToNewRemoteSession**. Sowohl das Öffnen einer vorhandenen Sitzung als auch das Erstellen einer neuen Sitzung löst das Ereignis **ARRSessionService.OnSessionStatusChanged** aus, wobei die Methode **OnRemoteSessionStatusChanged** ausgeführt wird. Im Idealfall führt dies dazu, dass der Zustandsautomat in **RemoteSessionReady** verschoben wird.

1. Um einer neuen Sitzung beizutreten, ändern Sie den Code so, dass die Methoden **JoinRemoteSession( )** und **StopRemoteSession( )** durch die folgenden abgeschlossenen Beispiele ersetzt werden:

```cs
/// <summary>
/// Attempts to join an existing session or start a new session
/// </summary>
public async void JoinRemoteSession()
{
    //If there's a session available that previously belonged to us, and it's ready, use it. Otherwise start a new session.
    RenderingSessionProperties joinResult;
    if (await IsSessionAvailable(LastUsedSessionID))
    {
        CurrentCoordinatorState = RemoteRenderingState.ConnectingToExistingRemoteSession;
        joinResult = await ARRSessionService.OpenSession(LastUsedSessionID);
    }
    else
    {
        CurrentCoordinatorState = RemoteRenderingState.ConnectingToNewRemoteSession;
        joinResult = await ARRSessionService.StartSession(new RenderingSessionCreationOptions(renderingSessionVmSize, maxLeaseHours, maxLeaseMinutes));
    }

    if (joinResult.Status == RenderingSessionStatus.Ready || joinResult.Status == RenderingSessionStatus.Starting)
    {
        LastUsedSessionID = joinResult.Id;
    }
    else
    {
        //The session should be ready or starting, if it's not, something went wrong
        await ARRSessionService.StopSession();
        if(LastUsedSessionID == SessionIDOverride)
            SessionIDOverride = "";
        CurrentCoordinatorState = RemoteRenderingState.NoSession;
    }
}

public void StopRemoteSession()
{
    if (ARRSessionService.CurrentActiveSession != null)
    {
        ARRSessionService.CurrentActiveSession.StopAsync();
    }
}
```

Wenn Sie Zeit sparen möchten, indem Sie Sitzungen wiederverwenden, stellen Sie sicher, dass Sie die Option **Auto-Stop Session** (Sitzung automatisch beenden) in der Komponente *ARRServiceUnity* deaktivieren. Beachten Sie, dass dadurch die Sitzungen weiterhin ausgeführt werden, auch wenn keine Verbindung mit Ihnen besteht. Ihre Sitzung kann so lange ausgeführt werden, wie unter *MaxLeaseTime* festgelegt, bevor sie vom Server heruntergefahren wird (der Wert für *MaxLeaseTime* kann im Remote Rendering-Koordinator unter *New Session Defaults* (Neue Sitzungsstandardwerte) geändert werden). Wenn Sie hingegen jede Sitzung automatisch herunterfahren, wenn die Verbindung getrennt wird, müssen Sie jedes Mal warten, bis eine neue Sitzung gestartet wird. Dies kann ein etwas langwieriger Prozess sein.

> [!NOTE]
> Das Beenden einer Sitzung erfolgt sofort und kann nicht rückgängig gemacht werden. Einmal beendet, müssen Sie eine neue Sitzung mit dem gleichen Startaufwand erstellen.

## <a name="connect-the-local-runtime-to-the-remote-session"></a>Herstellen einer Verbindung zwischen der lokalen Runtime und der Remotesitzung

Als Nächstes muss die Anwendung eine Verbindung zwischen der lokalen Runtime und der Remotesitzung herstellen.

![ARR-Stapel 3](./media/remote-render-stack-3.png)

Die Anwendung muss auch auf Ereignisse über die Verbindung zwischen der Runtime und der aktuellen Sitzung lauschen. Diese Zustandsänderungen werden in **OnLocalRuntimeStatusChanged** behandelt. Mit diesem Code wird der Zustand in **ConnectingToRuntime** geändert. Sobald die Verbindung in **OnLocalRuntimeStatusChanged** hergestellt wurde, wechselt der Zustand zu **RuntimeConnected**. Das Herstellen einer Verbindung mit der Runtime ist der letzte Zustand, mit dem sich der Koordinator selbst beschäftigt. Das bedeutet, dass die Anwendung mit der gesamten allgemeinen Konfiguration erfolgt und bereit ist, die sitzungsspezifischen Aufgaben zum Laden und Rendern von Modellen zu beginnen.

 1. Ersetzen Sie die Methoden **ConnectRuntimeToRemoteSession( )** und **DisconnectRuntimeFromRemoteSession( )** durch die folgenden abgeschlossenen Versionen.
 1. Es ist wichtig, die Unity-Methode **LateUpdate** zu notieren und die aktuelle aktive Sitzung zu aktualisieren. Dies ermöglicht der aktuellen Sitzung das Senden/Empfangen von Nachrichten und das Aktualisieren des Framepuffers mit den Frames, die von der Remotesitzung empfangen werden. Es ist wichtig, dass ARR ordnungsgemäß funktioniert.

```cs
/// <summary>
/// Connects the local runtime to the current active session, if there's a session available
/// </summary>
public void ConnectRuntimeToRemoteSession()
{
    if (ARRSessionService == null || ARRSessionService.CurrentActiveSession == null)
    {
        Debug.LogError("Not ready to connect runtime");
        return;
    }

    //Connect the local runtime to the currently connected session
    //This session is set when connecting to a new or existing session

    ARRSessionService.CurrentActiveSession.ConnectionStatusChanged += OnLocalRuntimeStatusChanged;
    ARRSessionService.CurrentActiveSession.ConnectAsync(new RendererInitOptions());
    CurrentCoordinatorState = RemoteRenderingState.ConnectingToRuntime;
}

public void DisconnectRuntimeFromRemoteSession()
{
    if (ARRSessionService == null || ARRSessionService.CurrentActiveSession == null || ARRSessionService.CurrentActiveSession.ConnectionStatus != ConnectionStatus.Connected)
    {
        Debug.LogError("Runtime not connected!");
        return;
    }

    ARRSessionService.CurrentActiveSession.Disconnect();
    ARRSessionService.CurrentActiveSession.ConnectionStatusChanged -= OnLocalRuntimeStatusChanged;
    CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
}

/// <summary>
/// The session must have its runtime pump updated.
/// The Connection.Update() will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
/// </summary>
private void LateUpdate()
{
    ARRSessionService?.CurrentActiveSession?.Connection?.Update();
}
```

> [!NOTE]
> Das Herstellen einer Verbindung zwischen der lokalen Runtime und einer Remotesitzung hängt davon ab, dass **Update** (Aktualisieren) regelmäßig für die derzeit aktive Sitzung aufgerufen wird. Wenn Sie feststellen, dass Ihre Anwendung nie über den Zustand **ConnectingToRuntime** hinausgeht, stellen Sie sicher, dass Sie **Update** (Aktualisieren) regelmäßig in der aktiven Sitzung aufrufen.

## <a name="load-a-model"></a>Laden eines Modells

Wenn die erforderliche Grundlage erfüllt ist, können Sie ein Modell in die Remotesitzung laden und mit dem Empfangen von Frames beginnen.

![Abbildung des Prozessablaufs zum Vorbereiten des Ladens und Anzeigen eines Modells](./media/remote-render-stack-4.png)

Die Methode **LoadModel** ist so konzipiert, dass ein Modellpfad, ein Fortschrittshandler und eine übergeordnete Transformation akzeptiert werden. Diese Argumente werden verwendet, um ein Modell in die Remotesitzung zu laden, die Benutzer über den Ladefortschritt zu informieren und das per Remotezugriff gerenderte Modell basierend auf der übergeordneten Transformation auszurichten.

1. Ersetzen Sie die Methode **LoadModel** vollständig durch den folgenden Code:

    ```cs
    /// <summary>
    /// Loads a model into the remote session for rendering
    /// </summary>
    /// <param name="modelName">The model's path</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <returns>An awaitable Remote Rendering Entity</returns>
    public async Task<Entity> LoadModel(string modelPath, Transform parent = null, Action<float> progress = null)
    {
        //Create a root object to parent a loaded model to
        var modelEntity = ARRSessionService.CurrentActiveSession.Connection.CreateEntity();

        //Get the game object representation of this entity
        var modelGameObject = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        //Ensure the entity will sync its transform with the server
        var sync = modelGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        //Parent the new object under the defined parent
        if (parent != null)
        {
            modelGameObject.transform.SetParent(parent, false);
            modelGameObject.name = parent.name + "_Entity";
        }

    #if UNITY_WSA
        //Anchor the model in the world, prefer anchoring parent if there is one
        if (parent != null)
        {
            parent.gameObject.AddComponent<WorldAnchor>();
        }
        else
        {
            modelGameObject.AddComponent<WorldAnchor>();
        }
    #endif

        //Load a model that will be parented to the entity
        var loadModelParams = new LoadModelFromSasParams(modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Connection.LoadModelFromSasAsync(loadModelParams, progress);
        var result = await loadModelAsync;
        return modelEntity;
    }
    ```

Dieser Code führt die folgenden Schritte aus:

1. Erstellen Sie eine [Remoteentität](../../../concepts/entities.md).
1. Erstellen Sie ein lokales GameObject-Element, das die Remoteentität darstellt.
1. Konfigurieren Sie das lokale GameObject-Element so, dass es seinen Zustand (d. h. Transformation) für jedes Frame mit der Remoteentität synchronisiert.
1. Legen Sie einen Namen fest, und fügen Sie ein [**WorldAnchor**](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html)-Element hinzu, um die Stabilisierung zu unterstützen.
1. Laden von Modelldaten aus Blob Storage in die Remoteentität.
1. Geben Sie die übergeordnete Entität zum späteren Verweis zurück.

## <a name="view-the-test-model"></a>Anzeigen des Testmodells

Wir verfügen jetzt über den gesamten Code, der zum Anzeigen eines per Remotezugriff gerenderten Modells erforderlich ist. Alle vier für das Remote Rendering erforderlichen Phasen sind vollständig. Nun müssen wir ein wenig Code hinzufügen, um den Ladeprozess für das Modell zu starten.

![ARR-Stapel 4](./media/remote-render-stack-5.png)

1. Fügen Sie den folgenden Code zur Klasse **RemoteRenderingCoordinator** hinzu, am besten direkt unterhalb der Methode **LoadModel**:

    ```cs
    private bool loadingTestModel = false;
    [ContextMenu("Load Test Model")]
    public async void LoadTestModel()
    {
        if(CurrentCoordinatorState != RemoteRenderingState.RuntimeConnected)
        {
            Debug.LogError("Please wait for the runtime to connect before loading the test model. Try again later.");
            return;
        }
        if(loadingTestModel)
        {
            Debug.Log("Test model already loading or loaded!");
            return;
        }
        loadingTestModel = true;
    
        // Create a parent object to use for positioning
        GameObject testParent = new GameObject("TestModelParent");
        testParent.transform.position = new Vector3(0f, 0f, 3f);
    
        // The 'built in engine path' is a special path that references a test model built into Azure Remote Rendering.
        await LoadModel("builtin://Engine", testParent.transform, (progressValue) => Debug.Log($"Loading Test Model progress: {Math.Round(progressValue * 100, 2)}%"));
    }
    ```
    
    Mit diesem Code wird ein GameObject-Element erstellt, das als übergeordnetes Element für das Testmodell fungiert. Anschließend wird die **LoadModel**-Methode aufgerufen, um das Modell „builtin://Engine“ zu laden. Dabei handelt es sich um ein Objekt, das in Azure Remote Rendering integriert ist, um das Rendering zu testen.

1. Speichern Sie Ihren Code.
1. Betätigen Sie im Unity-Editor die Wiedergabeschaltfläche, um das Herstellen einer Verbindung mit Azure Remote Rendering zu starten und eine neue Sitzung zu erstellen.
1. In der Spielansicht wird nicht viel angezeigt, aber in der Konsole sehen Sie, wie sich der Zustand der Anwendung ändert. Er ändert sich wahrscheinlich in `ConnectingToNewRemoteSession`, was sich möglicherweise bis zu fünf Minuten nicht mehr ändert.
1. Wählen Sie das GameObject-Element **RemoteRenderingCoordinator** aus, um die angefügten Skripts im Inspektor anzuzeigen. Sie sehen, wie die Komponente **Service** (Dienst) aktualisiert wird, während die Initialisierung und die Verbindungsschritte durchlaufen werden.
1. Überwachen Sie die Konsolenausgabe, und warten Sie darauf, dass sich der Zustand in **RuntimeConnected** ändert.
1. Sobald die Verbindung der Runtime hergestellt wurde, klicken Sie mit der rechten Maustaste auf **RemoteRenderingCoordinator** im Inspektor, um das Kontextmenü anzuzeigen. Klicken Sie anschließend im Kontextmenü auf die Option **Load Test Model** (Testmodell laden), die vom Teil `[ContextMenu("Load Test Model")]` des oben angegebenen Code hinzugefügt wurde.

    ![Laden aus dem Kontextmenü](./media/load-test-model.png)

1. Achten Sie in der Konsole auf die Ausgabe von **ProgressHandler**, die in die Methode **LoadModel** übergeben wurde.
1. Zeigen Sie das per Remotezugriff gerenderte Modell an.

> [!NOTE]
> Das Remotemodell wird in der Szenenansicht niemals angezeigt, nur in der Spielansicht. Dies liegt daran, dass ARR die Frames per Remotezugriff speziell für die Perspektive der Spielansichtskamera rendert und die Editorkamera nicht kennt (wird zum Rendern der Szenenansicht verwendet).

## <a name="next-steps"></a>Nächste Schritte

![Geladenes Modell](./media/test-model-rendered.png)

Glückwunsch! Sie haben eine einfache Anwendung erstellt, mit der per Remotezugriff gerenderte Modelle mithilfe von Azure Remote Rendering angezeigt werden können. Im nächsten Tutorial integrieren wir MRTK und importieren unsere eigenen Modelle.

> [!div class="nextstepaction"]
> [Nächster Schritt: Schnittstellen und benutzerdefinierte Modelle](../custom-models/custom-models.md)

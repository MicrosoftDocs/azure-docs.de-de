---
title: Schnittstellen und benutzerdefinierte Modelle
description: Hinzufügen von Ansichtscontrollern und Erfassen benutzerdefinierter Modelle, die von Azure Remote Rendering gerendert werden sollen
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 3a278b6e725488d6107e6b0819e002d1dafe4774
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591662"
---
# <a name="tutorial-interfaces-and-custom-models"></a>Tutorial: Schnittstellen und benutzerdefinierte Modelle

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Hinzufügen des Mixed Reality-Toolkits zum Projekt
> * Verwalten des Modellzustands
> * Konfigurieren von Azure Blob Storage für die Modellerfassung
> * Hochladen und Verarbeiten von Modellen für das Rendering

## <a name="prerequisites"></a>Voraussetzungen

* Dieses Tutorial baut auf [Tutorial: Anzeigen eines per Remotezugriff gerenderten Modells](../view-remote-models/view-remote-models.md) auf.

## <a name="get-started-with-the-mixed-reality-toolkit-mrtk"></a>Erste Schritte mit dem Mixed Reality-Toolkit (MRTK)

Das Mixed Reality-Toolkit (MRTK) ist ein plattformübergreifendes Toolkit für die Mixed Reality-Entwicklung. Wir verwenden die MRTK-Version 2.5.1 aufgrund ihrer Interaktions- und Visualisierungsfeatures.

Führen Sie zum Hinzufügen des MRTK die [erforderlichen Schritte](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#required) aus dem [MRTK-Installationshandbuch](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html) aus.

Diese Schritte sind:
 - [Herunterladen der neuesten MRTK-Unity-Pakete](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#1-get-the-latest-mrtk-unity-packages)
     - Verwenden Sie die Version 2.5.1 von der MRTK-Releaseseite, auch wenn hier „neueste“ steht.
     - In diesem Tutorial wird nur das Paket *Foundation* verwendet. Die Pakete *Extensions*, *Tools* und *Examples* werden nicht benötigt.
 - [Importieren der MRTK-Pakete in Ihr Unity-Projekt](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#2-import-mrtk-packages-into-your-unity-project)
 - [Festlegen Ihres Unity-Projekts auf die Zielplattform](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#3-switch-your-unity-project-to-the-target-platform)
     - Dieser Schritt sollte bereits im ersten Kapitel durchgeführt worden sein. Es empfiehlt sich aber, das an dieser Stelle noch einmal zu überprüfen.
 - [Hinzufügen des MRTK zu einer neuen Szene oder zu einem neuen Projekt](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#4-add-and-configure-mrtk-with-a-new-scene)
     - Sie können das MRTK einer neuen Szene hinzufügen und Ihren Koordinator sowie Ihre Modellobjekte/Skripts wieder hinzufügen. Alternativ können Sie das MRTK mithilfe des Menübefehls *Mixed Reality Toolkit > Add to Scene and Configure* („Mixed Reality-Toolkit“ > „Zu Szene hinzufügen und konfigurieren“) Ihrer vorhandenen Szene hinzufügen.

## <a name="import-assets-used-by-this-tutorial"></a>Importieren der in diesem Tutorial verwendeten Ressourcen

In diesem Kapitel implementieren wir zunächst ein einfaches [Muster vom Typ „Model View Controller“](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) für einen Großteil des behandelten Materials. Bei der Modellkomponente (*Model*) des Musters handelt es sich um den Azure Remote Rendering-spezifischen Code und um die Zustandsverwaltung für Azure Remote Rendering. Die Ansichts-/Controllerkomponente (*View* und *Controller*) des Musters wird mithilfe von MRTK-Ressourcen und einigen benutzerdefinierten Skripts implementiert. Das *Modell* in diesem Tutorial kann ohne die hier implementierte Ansichts-/Controllerkomponente (*View/Controller*) verwendet werden. Dank dieser Trennung können Sie den Code in diesem Tutorial ganz einfach in Ihre eigene Anwendung integrieren, wo er die Ansichts-/Controllerkomponente (*View/Controller*) des Entwurfsmusters übernimmt.

Mit der Einführung des MRTK stehen verschiedene Skripts, Prefabs und Ressourcen zur Verfügung, die nun dem Projekt hinzugefügt werden können, um Interaktionen und visuelles Feedback zu unterstützen. Diese als **Tutorialressourcen** bezeichneten Ressourcen werden in einem [Unity-Ressourcenpaket](https://docs.unity3d.com/Manual/AssetPackages.html) gebündelt, das im [GitHub-Repository für Azure Remote Rendering](https://github.com/Azure/azure-remote-rendering) in „\Unity\TutorialAssets\TutorialAssets.unitypackage“ enthalten ist.

1. Klonen Sie das Git-Repository [Azure Remote Rendering](https://github.com/Azure/azure-remote-rendering), oder laden Sie es herunter, und extrahieren Sie die ZIP-Datei an einem bekannten Speicherort.
1. Wählen Sie in Ihrem Unity-Projekt *Assets > Import Package > Custom Package* („Ressourcen“ > „Paket importieren“ > „Benutzerdefiniertes Paket“) aus.
1. Navigieren Sie im Datei-Explorer zu dem Verzeichnis, in dem Sie das Azure Remote Rendering-Repository geklont oder entzippt haben, und wählen Sie anschließend die UNITYPACKAGE-Datei unter **Unity > TutorialAssets > TutorialAssets.unitypackage** aus.
1. Wählen Sie die Schaltfläche **Import** (Importieren) aus, um die Paketinhalte in Ihr Projekt zu importieren.
1. Wählen Sie im Unity-Editor auf der oberen Menüleiste *Mixed Reality Toolkit > Utilities > Upgrade MRTK Standard Shader for Lightweight Render Pipeline* („Mixed Reality-Toolkit“ > „Hilfsprogramme“ > „MRTK-Standardshader für einfache Renderpipeline upgraden“) aus, und folgen Sie den Anweisungen, um ein Shaderupgrade durchzuführen.

Nachdem das MRTK und die Tutorialressourcen in das Projekt integriert wurden, legen wir das MRTK-Profil auf ein Profil fest, das besser für das Tutorial geeignet ist.

1. Wählen Sie in der Szenehierarchie das Spielobjekt (GameObject) **MixedRealityToolkit** aus.
1. Ändern Sie das Konfigurationsprofil im Inspektor unter der Komponente **MixedRealityToolkit** in *ARRMixedRealityToolkitConfigurationProfile*.
1. Drücken Sie zum Speichern der Änderungen *STRG+S*.

Dadurch wird das MRTK (in erster Linie) mit den standardmäßigen HoloLens 2-Profilen konfiguriert. Die bereitgestellten Profile sind wie folgt vorkonfiguriert:
 - Der Profiler ist deaktiviert. (Drücken Sie 9, um ihn ein- oder auszuschalten, oder sagen Sie „Profiler anzeigen/ausblenden“ auf dem Gerät.)
 - Der Anvisier-Cursor ist deaktiviert.
 - Unity-Mausklicks sind aktiviert, um mit der Maus auf Elemente der MRTK-Benutzeroberfläche klicken zu können, anstatt die simulierte Hand zu verwenden.

## <a name="add-the-app-menu"></a>Hinzufügen des App-Menüs

Die meisten der Ansichtscontroller in diesem Tutorial arbeiten nicht mit konkreten Klassen, sondern mit abstrakten Basisklassen. Dieses Muster bietet mehr Flexibilität und ermöglicht es uns, die Ansichtscontroller für Sie bereitzustellen und Sie trotzdem mit dem Azure Remote Rendering-Code vertraut zu machen. Für die Klasse **RemoteRenderingCoordinator** wurde der Einfachheit halber keine abstrakte Klasse bereitgestellt, und der zugehörige Ansichtscontroller arbeitet direkt mit der konkreten Klasse.

Nun können Sie der Szene das vorgefertigte App-Menü (**AppMenu**) hinzufügen, um visuelles Feedback zum aktuellen Sitzungszustand zu erhalten. Durch diesen Ansichtscontroller werden weitere untergeordnete Menüansichtscontroller verfügbar gemacht, während wir weitere ARR-Features implementieren und in die Szene integrieren. Das App-Menü (**AppMenu**) verfügt vorerst über eine visuelle Darstellung des ARR-Zustands und zeigt den modalen Bereich an, über den Benutzer die Anwendung für die Verbindungsherstellung mit ARR autorisiert.

1. Navigieren Sie in *Assets/RemoteRenderingTutorial/Prefabs/AppMenu* zum Prefab **AppMenu**.
1. Ziehen Sie das Prefab **AppMenu** in die Szene.
1. Wahrscheinlich wird ein Dialogfeld für **TMP Importer** (TMP-Importprogramm) angezeigt, da wir der Szene erstmals Ressourcen vom Typ *Text Mesh Pro* hinzufügen. Folgen Sie den Anweisungen, um grundlegende TMP-Komponenten zu importieren (**Import TMP Essentials**). Schließen Sie anschließend das Importdialogfeld. Die Beispiele und Extras werden nicht benötigt.
1. Das App-Menü (**AppMenu**) ist so konfiguriert, dass automatisch eine Verknüpfung eingerichtet und das modale Fenster bereitgestellt wird, über das der Verbindungsherstellung mit einer Sitzung zugestimmt werden kann. Die zuvor verwendete Umgehung kann somit entfernt werden. Entfernen Sie im GameObject-Element **RemoteRenderingCoordinator** die zuvor implementierte Autorisierungsumgehung, indem Sie beim Ereignis **On Requesting Authorization** (Beim Anfordern der Autorisierung) auf die Schaltfläche „-“ klicken.
 ![Entfernen der Umgehung](./media/remove-bypass-event.png)
1. Klicken Sie im Unity-Editor auf **Play** (Spielen), um den Ansichtscontroller zu testen.
1. Nachdem das MRTK nun konfiguriert ist, können Sie im Editor mithilfe der Tasten W, A, S und D die Position Ihrer Ansicht ändern und Ihre Blickrichtung ändern, indem Sie bei gedrückter rechter Maustaste die Maus bewegen. Bewegen Sie sich ein wenig durch die Szene, um ein Gefühl für die Steuerung zu bekommen.
1. Auf dem Gerät können Sie durch Heben Ihrer Handfläche das App-Menü (**AppMenu**) anzeigen. Im Unity-Editor können Sie dazu die M-TASTE drücken.
1. Sollte sich das Menü nicht mehr in Ihrem Blickfeld befinden, drücken Sie die M-TASTE, um das Menü anzuzeigen. Das Menü wird zur einfachen Interaktion in der Nähe der Kamera platziert.
1. Die Autorisierung wird nun als Anforderung rechts neben dem App-Menü (**AppMenu**) angezeigt und von nun an verwendet, um die Verwaltung von Remote Rendering-Sitzungen durch die App zu autorisieren.
 ![Autorisierung über die Benutzeroberfläche](./media/authorize-request-ui.png)
1. Beenden Sie das Spiel in Unity, um mit dem Tutorial fortzufahren.

## <a name="manage-model-state"></a>Verwalten des Modellzustands

Als Nächstes implementieren wir ein neues Skript (**RemoteRenderedModel**). Dieses dient zum Nachverfolgen des Zustands, zum Reagieren auf Ereignisse, zum Auslösen von Ereignissen sowie zur Konfiguration. Von **RemoteRenderedModel** wird im Wesentlichen der Remotepfad für die Modelldaten in `modelPath` gespeichert. Es lauscht auf Zustandsänderungen in **RemoteRenderingCoordinator**, um zu ermitteln, ob das definierte Modell automatisch geladen oder entladen werden soll. Das Spielobjekt, an das **RemoteRenderedModel** angefügt wird, wird zum lokalen übergeordneten Element für den Remoteinhalt.

Beachten Sie, dass durch das Skript **RemoteRenderedModel** das Element **BaseRemoteRenderedModel** aus den **Tutorialressourcen** implementiert wird. Dies ermöglicht die Bindung des Remotemodell-Ansichtscontrollers mit Ihrem Skript.

1. Erstellen Sie ein neues Skript mit dem Namen **RemoteRenderedModel** im gleichen Ordner wie **RemoteRenderingCoordinator**. Ersetzen Sie den gesamten Inhalt durch den folgenden Code:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;
    using UnityEngine.Events;

    public class RemoteRenderedModel : BaseRemoteRenderedModel
    {
        public bool AutomaticallyLoad = true;

        private ModelState currentModelState = ModelState.NotReady;

        [SerializeField]
        [Tooltip("The friendly name for this model")]
        private string modelDisplayName;
        public override string ModelDisplayName { get => modelDisplayName; set => modelDisplayName = value; }

        [SerializeField]
        [Tooltip("The URI for this model")]
        private string modelPath;
        public override string ModelPath
        {
            get => modelPath.Trim();
            set => modelPath = value;
        }

        public override ModelState CurrentModelState
        {
            get => currentModelState;
            protected set
            {
                if (currentModelState != value)
                {
                    currentModelState = value;
                    ModelStateChange?.Invoke(value);
                }
            }
        }

        public override event Action<ModelState> ModelStateChange;
        public override event Action<float> LoadProgress;
        public override Entity ModelEntity { get; protected set; }

        public UnityEvent OnModelNotReady = new UnityEvent();
        public UnityEvent OnModelReady = new UnityEvent();
        public UnityEvent OnStartLoading = new UnityEvent();
        public UnityEvent OnModelLoaded = new UnityEvent();
        public UnityEvent OnModelUnloading = new UnityEvent();

        public UnityFloatEvent OnLoadProgress = new UnityFloatEvent();

        public void Awake()
        {
            // Hook up the event to the Unity event
            LoadProgress += (progress) => OnLoadProgress?.Invoke(progress);

            ModelStateChange += HandleUnityStateEvents;
        }

        private void HandleUnityStateEvents(ModelState modelState)
        {
            switch (modelState)
            {
                case ModelState.NotReady:  OnModelNotReady?.Invoke();  break;
                case ModelState.Ready:     OnModelReady?.Invoke();     break;
                case ModelState.Loading:   OnStartLoading?.Invoke();   break;
                case ModelState.Loaded:    OnModelLoaded?.Invoke();    break;
                case ModelState.Unloading: OnModelUnloading?.Invoke(); break;
            }
        }

        private void Start()
        {
            //Attach to and initialize current state (in case we're attaching late)
            RemoteRenderingCoordinator.CoordinatorStateChange += Instance_CoordinatorStateChange;
            Instance_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        /// <summary>
        /// Listen for state changes on the coordinator, clean up this model's remote objects if we're no longer connected.
        /// Automatically load if required
        /// </summary>
        private void Instance_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    CurrentModelState = ModelState.Ready;
                    if (AutomaticallyLoad)
                        LoadModel();
                    break;
                default:
                    UnloadModel();
                    break;
            }
        }

        private void OnDestroy()
        {
            RemoteRenderingCoordinator.CoordinatorStateChange -= Instance_CoordinatorStateChange;
            UnloadModel();
        }

        /// <summary>
        /// Asks the coordinator to create a model entity and listens for coordinator state changes
        /// </summary>
        [ContextMenu("Load Model")]
        public override async void LoadModel()
        {
            if (CurrentModelState != ModelState.Ready)
                return; //We're already loaded, currently loading, or not ready to load

            CurrentModelState = ModelState.Loading;

            ModelEntity = await RemoteRenderingCoordinator.instance?.LoadModel(ModelPath, this.transform, SetLoadingProgress);

            if (ModelEntity != null)
                CurrentModelState = ModelState.Loaded;
            else
                CurrentModelState = ModelState.Error;
        }

        /// <summary>
        /// Clean up the local model instances
        /// </summary>
        [ContextMenu("Unload Model")]
        public override void UnloadModel()
        {
            CurrentModelState = ModelState.Unloading;

            if (ModelEntity != null)
            {
                var modelGameObject = ModelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
                Destroy(modelGameObject);
                ModelEntity.Destroy();
                ModelEntity = null;
            }

            if (RemoteRenderingCoordinator.instance.CurrentCoordinatorState == RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected)
                CurrentModelState = ModelState.Ready;
            else
                CurrentModelState = ModelState.NotReady;
        }

        /// <summary>
        /// Update the Unity progress event
        /// </summary>
        /// <param name="progressValue"></param>
        public override void SetLoadingProgress(float progressValue)
        {
            LoadProgress?.Invoke(progressValue);
        }
    }
    ```

Stark vereinfacht enthält **RemoteRenderedModel** die zum Laden eines Modells erforderlichen Daten (in diesem Fall den SAS-URI oder den URI *builtin://* ) und verfolgt den Remotemodellzustand nach. Beim Laden wird die Methode `LoadModel` für **RemoteRenderingCoordinator** aufgerufen, und die Entität, die das Modell enthält, wird zur Referenz und zum Entladen zurückgegeben.

## <a name="load-the-test-model"></a>Laden des Testmodells

Testen wir nun das neue Skript, indem wir noch einmal das Testmodell laden. Wir erstellen ein Spielobjekt, das das Skript enthält und dem Testmodell übergeordnet ist.

1. Erstellen Sie ein neues leeres Spielobjekt in der Szene, und nennen Sie es **TestModel**.
1. Fügen Sie **TestModel** das Skript *RemoteRenderedModel* hinzu.
![Hinzufügen der Komponente „RemoteRenderedModel“](./media/add-remote-rendered-model-script.png)
1. Geben Sie für `Model Display Name` und `Model Path` den Wert *TestModel* bzw. *builtin://Engine* an.
![Angeben der Modelldetails](./media/add-model-script.png)
1. Positionieren Sie das Objekt **TestModel** vor der Kamera an der Position **x = 0, y = 0, z = 3**.
![Positionieren des Objekts](./media/test-model-position.png)
1. Vergewissern Sie sich, dass **AutomaticallyLoad** aktiviert ist.
1. Klicken Sie im Unity-Editor auf **Play** (Spielen), um die Anwendung zu testen.
1. Klicken Sie zur Autorisierung auf die Schaltfläche *Connect* (Verbinden), um der App die Erstellung einer Sitzung zu ermöglichen. Daraufhin wird von der App eine Verbindung mit einer Sitzung hergestellt und das Modell automatisch geladen.

Beobachten Sie die Konsole, während die Anwendung die Zustände durchläuft. Beachten Sie, dass manche Zustände unter Umständen etwas länger andauern und kein Fortschritt zu sehen ist. Irgendwann sehen Sie die Protokolle des geladenen Modells. Danach wird das Testmodell in der Szene gerendert.

Bewegen und drehen Sie das Spielobjekt **TestModel** mithilfe der Transformation im Inspektor oder in der Szenenansicht. In der Spielansicht sehen Sie, dass das Modell bewegt und gedreht wird.

![Unity-Protokoll](./media/unity-loading-log.png)

## <a name="provision-blob-storage-in-azure-and-custom-model-ingestion"></a>Bereitstellen von Blob Storage in Azure und Erfassung eines benutzerdefinierten Modells

Nun können wir Ihr eigenes Modell laden. Hierzu müssen Sie Blob Storage konfigurieren und in Azure ein Modell hochladen und konvertieren. Anschließend wird das Modell mithilfe des Skripts **RemoteRenderedModel** geladen. Sollten Sie noch nicht über ein eigenes Modell zum Laden verfügen, können Sie die Schritte zum Laden eines benutzerdefinierten Modells problemlos überspringen.

Gehen Sie wie unter [Schnellstart: Konvertieren eines Modells für das Rendering](../../../quickstarts/convert-model.md) beschrieben vor. Überspringen Sie für dieses Tutorial den Abschnitt **Einfügen eines neuen Modells in die Beispiel-App der Schnellstartanleitung**. Fahren Sie hier mit dem nächsten Schritt fort, wenn Sie über den SAS-URI (*Shared Access Signature*) Ihres erfassten Modells verfügen.

## <a name="load-and-rendering-a-custom-model"></a>Laden und Rendern eines benutzerdefinierten Modells

1. Erstellen Sie in der Szene ein neues leeres Spielobjekt, und nennen Sie es ähnlich wie ihr benutzerdefiniertes Modell.
1. Fügen Sie dem neu erstellten GameObject-Element das Skript *RemoteRenderedModel* hinzu.
 ![Hinzufügen der Komponente „RemoteRenderedModel“](./media/add-remote-rendered-model-script.png)
1. Geben Sie für `Model Display Name` einen geeigneten Namen für Ihr Modell an.
1. Geben Sie für `Model Path` den SAS-URI (*Shared Access Signature*) des Modells an, den Sie in den oben beschriebenen Erfassungsschritten erstellt haben.
1. Positionieren Sie das Spielobjekt vor der Kamera an der Position **x = 0, y = 0, z = 3**.
1. Vergewissern Sie sich, dass **AutomaticallyLoad** aktiviert ist.
1. Klicken Sie im Unity-Editor auf **Play** (Spielen), um die Anwendung zu testen.

    In der Konsole werden nach und nach der aktuelle Zustand und schließlich Meldungen zum Ladefortschritt des Modells angezeigt. Anschließend wird Ihr benutzerdefiniertes Modell in die Szene geladen.

1. Entfernen Sie Ihr benutzerdefiniertes Modellobjekt aus der Szene. Es empfiehlt sich, für dieses Tutorial das Testmodell zu verwenden. In ARR werden zwar mehrere Modelle unterstützt, dieses Tutorial wurde jedoch für die bestmögliche Unterstützung eines einzelnen Remotemodells gestaltet.

## <a name="next-steps"></a>Nächste Schritte

Sie können jetzt Ihre eigenen Modelle in Azure Remote Rendering laden und in Ihrer Anwendung anzeigen. Im nächsten Tutorial zeigen wir Ihnen, wie Sie Ihre Modelle manipulieren.

> [!div class="nextstepaction"]
> [Nächster Schritt: Manipulieren von Modellen](../manipulate-models/manipulate-models.md)

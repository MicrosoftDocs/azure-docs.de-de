---
title: Optimieren von Materialien, Beleuchtung und Effekten
description: Modifizieren Sie Modellmaterialien und Beleuchtung. Fügen Sie zusätzliche Effekte wie Gliederungen und Schnittebenen hinzu.
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 8eb73fcfde7e294896a12289486ff71794a00ae6
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591717"
---
# <a name="tutorial-refining-materials-lighting-and-effects"></a>Tutorial: Optimieren von Materialien, Beleuchtung und Effekten

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Hervorheben und Gliedern von Modellen und Modellkomponenten
> * Anwenden verschiedener Materialien auf Modelle
> * Segmentieren von Modellen mit Schnittebenen
> * Hinzufügen von einfachen Animationen für remote gerenderte Objekte

## <a name="prerequisites"></a>Voraussetzungen

* Dieses Tutorial baut auf dem Tutorial [ auf: Bearbeiten von Modellen](..\manipulate-models\manipulate-models.md).

## <a name="highlighting-and-outlining"></a>Hervorheben und Gliedern

Visuelles Feedback ist für den Benutzer ein wichtiger Bestandteil der Benutzererfahrung in jeder Anwendung. Azure Remote Rendering bietet über [hierarchische Zustandsaußerkraftsetzungen](../../../overview/features/override-hierarchical-state.md) visuelle Feedbackmechanismen. Die hierarchischen Zustandsaußerkraftsetzungen werden mit Komponenten implementiert, die an lokale Modellinstanzen angefügt sind. Wir haben gelernt, wie diese lokalen Instanzen in [Synchronisieren des Remoteobjektgraphs mit der Unity-Hierarchie](../manipulate-models/manipulate-models.md#synchronizing-the-remote-object-graph-into-the-unity-hierarchy) erstellt werden.

Zunächst erstellen wir einen Wrapper um die Komponente [**HierarchicalStateOverrideComponent**](/dotnet/api/microsoft.azure.remoterendering.hierarchicalstateoverridecomponent). Die Komponente **HierarchicalStateOverrideComponent** ist das lokale Skript, das die Außerkraftsetzungen für die Remoteentität steuert. Die [**Tutorialressourcen**](../custom-models/custom-models.md#import-assets-used-by-this-tutorial) beinhalten eine abstrakte Basisklasse mit dem Namen **BaseEntityOverrideController**, die zum Erstellen des Wrappers erweitert wird.

1. Erstellen Sie ein neues Skript namens **EntityOverrideController**, und ersetzen Sie seinen Inhalt durch den folgenden Code:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    public class EntityOverrideController : BaseEntityOverrideController
    {
        public override event Action<HierarchicalStates> FeatureOverrideChange;

        private ARRHierarchicalStateOverrideComponent localOverride;
        public override ARRHierarchicalStateOverrideComponent LocalOverride
        {
            get
            {
                if (localOverride == null)
                {
                    localOverride = gameObject.GetComponent<ARRHierarchicalStateOverrideComponent>();
                    if (localOverride == null)
                    {
                        localOverride = gameObject.AddComponent<ARRHierarchicalStateOverrideComponent>();
                    }

                    var remoteStateOverride = TargetEntity.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

                    if (remoteStateOverride == null)
                    {
                        // if there is no HierarchicalStateOverrideComponent on the remote side yet, create one
                        localOverride.Create(RemoteManagerUnity.CurrentSession);
                    }
                    else
                    {
                        // otherwise, bind our local stateOverride component to the remote component
                        localOverride.Bind(remoteStateOverride);

                    }
                }
                return localOverride;
            }
        }

        private RemoteEntitySyncObject targetEntity;
        public override RemoteEntitySyncObject TargetEntity
        {
            get
            {
                if (targetEntity == null)
                    targetEntity = gameObject.GetComponent<RemoteEntitySyncObject>();
                return targetEntity;
            }
        }

        private HierarchicalEnableState ToggleState(HierarchicalStates feature)
        {
            HierarchicalEnableState setToState = HierarchicalEnableState.InheritFromParent;
            switch (LocalOverride.RemoteComponent.GetState(feature))
            {
                case HierarchicalEnableState.ForceOff:
                case HierarchicalEnableState.InheritFromParent:
                    setToState = HierarchicalEnableState.ForceOn;
                    break;
                case HierarchicalEnableState.ForceOn:
                    setToState = HierarchicalEnableState.InheritFromParent;
                    break;
            }

            return SetState(feature, setToState);
        }

        private HierarchicalEnableState SetState(HierarchicalStates feature, HierarchicalEnableState enableState)
        {
            if (GetState(feature) != enableState) //if this is actually different from the current state, act on it
            {
                LocalOverride.RemoteComponent.SetState(feature, enableState);
                FeatureOverrideChange?.Invoke(feature);
            }

            return enableState;
        }

        public override HierarchicalEnableState GetState(HierarchicalStates feature) => LocalOverride.RemoteComponent.GetState(feature);

        public override void ToggleHidden() => ToggleState(HierarchicalStates.Hidden);

        public override void ToggleSelect() => ToggleState(HierarchicalStates.Selected);

        public override void ToggleSeeThrough() => ToggleState(HierarchicalStates.SeeThrough);

        public override void ToggleTint(Color tintColor = default)
        {
            if (tintColor != default) LocalOverride.RemoteComponent.TintColor = tintColor.toRemote();
            ToggleState(HierarchicalStates.UseTintColor);
        }

        public override void ToggleDisabledCollision() => ToggleState(HierarchicalStates.DisableCollision);

        public override void RemoveOverride()
        {
            var remoteStateOverride = TargetEntity.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();
            if (remoteStateOverride != null)
            {
                remoteStateOverride.Destroy();
            }

            if (localOverride == null)
                localOverride = gameObject.GetComponent<ARRHierarchicalStateOverrideComponent>();

            if (localOverride != null)
            {
                Destroy(localOverride);
            }
        }
    }
    ```

Die Hauptaufgabe von **LocalOverride** besteht darin, eine Verknüpfung zwischen sich selbst und `RemoteComponent` zu erstellen. Mit **LocalOverride** können wir Statuskennzeichnungen für die lokale Komponente festlegen, die an die Remoteentität gebunden sind. Die Außerkraftsetzungen und ihre Zustände werden auf der Seite [Hierarchische Zustandsaußerkraftsetzungen](../../../overview/features/override-hierarchical-state.md) beschrieben. 

Diese Implementierung schaltet jeweils nur einen Zustand um. Es ist jedoch durchaus möglich, mehrere Außerkraftsetzungen für einzelne Entitäten zu kombinieren und Kombinationen auf unterschiedlichen Ebenen in der Hierarchie zu erstellen. Beispielsweise würde die Kombination von `Selected` und `SeeThrough` auf einer einzigen Komponente dieser eine Gliederung geben und sie gleichzeitig transparent machen. Wenn Sie die Außerkraftsetzung der Stammentität `Hidden` auf `ForceOn` setzen, während die Außerkraftsetzung einer untergeordneten Entität `Hidden` auf `ForceOff` gesetzt wird, wird alles außer dem untergeordneten Element mit der Außerkraftsetzung ausgeblendet.

Um Zustände auf Entitäten anzuwenden, können wir das zuvor erstellten **RemoteEntityHelper**-Element verwenden.

1. Ändern Sie die Klasse **RemoteEntityHelper**, um die abstrakte Klasse **BaseRemoteEntityHelper** zu implementieren. Diese Änderung ermöglicht die Verwendung eines Ansichtscontrollers, der in den **Tutorialressourcen** bereitgestellt wird. Dies sollte nach der Änderung wie folgt aussehen:

    ```cs
    public class RemoteEntityHelper : BaseRemoteEntityHelper
    ```

2. Setzen Sie die abstrakten Methoden mit dem folgenden Code außer Kraft:

    ```cs
    public override BaseEntityOverrideController EnsureOverrideComponent(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var overrideComponent = entityGameObject.GetComponent<EntityOverrideController>();
        if (overrideComponent == null)
            overrideComponent = entityGameObject.AddComponent<EntityOverrideController>();
        return overrideComponent;
    }

    public override HierarchicalEnableState GetState(Entity entity, HierarchicalStates feature)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        return overrideComponent.GetState(feature);
    }

    public override void ToggleHidden(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleHidden();
    }

    public override void ToggleSelect(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleSelect();
    }

    public override void ToggleSeeThrough(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleSeeThrough();
    }

    public Color TintColor = new Color(0.0f, 1.0f, 0.0f, 0.1f);
    public override void ToggleTint(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleTint(TintColor);
    }

    public override void ToggleDisableCollision(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleHidden();
    }

    public override void RemoveOverrides(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var overrideComponent = entityGameObject.GetComponent<EntityOverrideController>();
        if (overrideComponent != null)
        {
            overrideComponent.RemoveOverride();
            Destroy(overrideComponent);
        }
    }
    ```

Dieser Code stellt sicher, dass der Zielentität eine **EntityOverrideController**-Komponente hinzugefügt wird und ruft dann eine der Umschaltmethoden auf. Wenn gewünscht, können Sie auf dem **TestModel**-GameObject diese Hilfsmethoden aufrufen, indem Sie **RemoteEntityHelper** als Rückruf dem `OnRemoteEntityClicked`-Ereignis auf der Komponente **RemoteRayCastPointerHandler** hinzufügen.

![Zeigerrückrufe](./media/pointer-event-callbacks.png)

Nachdem diese Skripte dem Modell hinzugefügt wurden, sollte der **AppMenu**-Ansichtscontroller nach der Verbindungsherstellung mit der Runtime über zusätzliche Schnittstellen verfügen, welche die Interaktion mit dem **EntityOverrideController**-Skript ermöglichen. Sehen Sie sich das Menü **Modelltools** an, um die entsperrenden Ansichtscontroller anzuzeigen.

Zu diesem Zeitpunkt sollten die Komponenten Ihres **TestModel**-GameObjects in etwa wie folgt aussehen:

![Testmodell mit zusätzlichen Skripts](./media/test-model-updated.png)

Im folgenden finden Sie ein Beispiel für das Stapeln von Außerkraftsetzungen für eine einzelne Entität. Wir haben `Select` und `Tint` verwendet, um eine Gliederung und eine Farbgebung bereitzustellen:

![Auswahl – Testmodelltönung](./media/select-tint-test-model.png)

## <a name="cut-planes"></a>Schnittebenen

[Schnittebenen](../../../overview/features/cut-planes.md) sind eine Funktion, die einer beliebigen Remoteentität hinzugefügt werden kann. In den meisten Fällen erstellen Sie eine neue Remoteentität, die keinen Gittermodelldaten zugeordnet ist, um die Komponente der Schnittebene zu speichern. Die Position und die Ausrichtung der Schnittebene werden durch die Position und Ausrichtung der Remoteentität bestimmt, an die Sie angefügt ist.

Wir erstellen ein Skript, das automatisch eine Remoteentität erstellt, eine Schnittebenenkomponente hinzufügt und die Transformation eines lokalen Objekts mit der Schnittebenenentität synchronisiert. Anschließend können wir **CutPlaneViewController** verwenden, um die Schnittebene in eine Schnittstelle einzubinden, mit der wir sie bearbeiten können.

1. Erstellen Sie ein neues Skript mit dem Namen **RemoteCutPlane**, und ersetzen Sie den Code durch den folgenden Code:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    public class RemoteCutPlane : BaseRemoteCutPlane
    {
        public Color SliceColor = new Color(0.5f, 0f, 0f, .5f);
        public float FadeLength = 0.01f;
        public Axis SliceNormal = Axis.Y_Neg;

        public bool AutomaticallyCreate = true;

        private CutPlaneComponent remoteCutPlaneComponent;
        private bool cutPlaneReady = false;

        public override bool CutPlaneReady 
        { 
            get => cutPlaneReady;
            set 
            { 
                cutPlaneReady = value;
                CutPlaneReadyChanged?.Invoke(cutPlaneReady);
            }
        }

        public override event Action<bool> CutPlaneReadyChanged;

        public UnityBoolEvent OnCutPlaneReadyChanged = new UnityBoolEvent();

        public void Start()
        {
            // Hook up the event to the Unity event
            CutPlaneReadyChanged += (ready) => OnCutPlaneReadyChanged?.Invoke(ready);

            RemoteRenderingCoordinator.CoordinatorStateChange += RemoteRenderingCoordinator_CoordinatorStateChange;
            RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        private void RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    if (AutomaticallyCreate)
                        CreateCutPlane();
                    break;
                default:
                    DestroyCutPlane();
                    break;
            }
        }

        public override void CreateCutPlane()
        {
            //Implement me
        }

        public override void DestroyCutPlane()
        {
            //Implement me
        }
    }
    ```

    Mit diesem Code wird die **BaseRemoteCutPlane**-Klasse erweitert, die in den **Tutorialressourcen** enthalten ist. Ähnlich wie das remote gerenderte Modell hängt dieses Skript Änderungen von `RemoteRenderingState` des Remotekoordinators an und hört sie ab. Wenn der Koordinator den Zustand `RuntimeConnected` erreicht, wird ggf. versucht, automatisch eine Verbindung herzustellen. Es gibt auch eine `CutPlaneComponent`-Variable, die wir nachverfolgen. Dies ist die Azure Remote Rendering-Komponente, die mit der Schnittebene in der Remotesitzung synchronisiert wird. Werfen wir einen Blick auf die zur Erstellung der Schnittebene erforderlichen Schritte.

2. Ersetzen Sie die `CreateCutPlane()`-Methode durch die folgende abgeschlossene Version:

    ```cs
    public override void CreateCutPlane()
    {
        if (remoteCutPlaneComponent != null)
            return; //Nothing to do!

        //Create a root object for the cut plane
        var cutEntity = RemoteRenderingCoordinator.CurrentSession.Connection.CreateEntity();

        //Bind the remote entity to this game object
        cutEntity.BindToUnityGameObject(this.gameObject);

        //Sync the transform of this object so we can move the cut plane
        var syncComponent = this.gameObject.GetComponent<RemoteEntitySyncObject>();
        syncComponent.SyncEveryFrame = true;

        //Add a cut plane to the entity
        remoteCutPlaneComponent = RemoteRenderingCoordinator.CurrentSession.Connection.CreateComponent(ObjectType.CutPlaneComponent, cutEntity) as CutPlaneComponent;

        //Configure the cut plane
        remoteCutPlaneComponent.Normal = SliceNormal;
        remoteCutPlaneComponent.FadeColor = SliceColor.toRemote();
        remoteCutPlaneComponent.FadeLength = FadeLength;
        CutPlaneReady = true;
    }
    ```

    Hier erstellen wir eine Remoteentität und binden Sie an ein lokales GameObject. Wir stellen sicher, dass die Transformation für die Remoteentität mit der lokalen Transformation synchronisiert wird, indem `SyncEveryFrame` auf `true` festgelegt wird. Anschließend verwenden wir den `CreateComponent`-Aufruf, um dem Remoteobjekt `CutPlaneComponent` hinzuzufügen. Zum Schluss konfigurieren wir die Schnittebene mit den Einstellungen, die oben in MonoBehaviour definiert sind. Sehen wir uns an, was zum Bereinigen einer Schnittebene erforderlich ist, indem wir die `DestroyCutPlane()`-Methode implementieren.

3. Ersetzen Sie die `DestroyCutPlane()`-Methode durch die folgende abgeschlossene Version:

    ```cs
    public override void DestroyCutPlane()
    {
        if (remoteCutPlaneComponent == null)
            return; //Nothing to do!

        remoteCutPlaneComponent.Owner.Destroy();
        remoteCutPlaneComponent = null;
        CutPlaneReady = false;
    }
    ```

Da das Remoteobjekt recht einfach ist und wir nur das Remoteende bereinigen (und das lokale Objekt beibehalten), können wir einfach nur `Destroy` für das Remoteobjekt aufrufen und den Verweis darauf löschen.

Das **AppMenu**-Element enthält einen Ansichtscontroller, der automatisch an die Schnittebene angefügt wird und so das Interagieren ermöglicht. Es ist nicht erforderlich, dass Sie das **AppMenu**-Element oder einen der Ansichtscontroller verwenden, sie optimieren jedoch die Benutzererfahrung. Kommen wir nun zur Testschnittebene und dem Ansichtscontroller.

1. Erstellen Sie ein neues leeres GameObject in der Szene, und nennen Sie es **CutPlane**.
1. Fügen Sie die **RemoteCutPlane**-Komponente dem **CutPlane**-GameObject hinzu.

   ![Konfigurieren der Schnittebenenkomponente](./media/cut-plane-config.png)

1. Klicken Sie im Unity-Editor auf „Abspielen“, um eine Remotesitzung zu laden und eine Verbindung herzustellen.
1. Mithilfe der Handsimulation von MRTK können Sie die CutPlane drehen (halten Sie die STRG-Taste gedrückt), um sie innerhalb der Szene zu verschieben. Segmentieren Sie damit das **TestModel**, um interne Komponenten offenzulegen.

![Beispiel für eine Schnittebene](./media/cut-plane-example-engine.png)

## <a name="configuring-the-remote-lighting"></a>Konfigurieren der Remotebeleuchtung

Die Remoterenderingsitzung unterstützt ein vollständiges Spektrum an [Beleuchtungsoptionen](../../../overview/features/lights.md). Wir erstellen Skripts für die [Himmeltextur](../../../overview/features/sky.md) und eine einfache Zuordnung für zwei Unity-Lichttypen, die mit Remote Rendering verwendet werden.

### <a name="sky-texture"></a>Himmeltextur

Es gibt eine Reihe integrierter Cubemaps, aus denen Sie auswählen können, wenn Sie die Himmeltextur ändern. Diese werden in die Sitzung geladen und auf die Himmeltextur angewendet. Ebenso können Sie [Ihre eigenen Texturen laden](../../../concepts/textures.md), um Sie als Himmellicht zu verwenden.

Wir erstellen ein **RemoteSky**-Skript, das eine Liste der integrierten, verfügbaren Cubemaps in Form von Load-Parametern enthält. Dann gestatten wir dem Benutzer, eine der Optionen auszuwählen und zu laden.

1. Erstellen Sie ein neues Skript namens **RemoteSky**, und ersetzen Sie seinen gesamten Inhalt durch den nachstehenden Code:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using System;
    using System.Collections.Generic;
    using UnityEngine;

    public class RemoteSky : BaseRemoteSky
    {
        public override Dictionary<string, LoadTextureFromSasOptions> AvailableCubemaps => builtInTextures;

        private bool canSetSky;
        public override bool CanSetSky
        {
            get => canSetSky;
            set
            {
                canSetSky = value;
                CanSetSkyChanged?.Invoke(canSetSky);
            }
        }

        private string currentSky = "DefaultSky";
        public override string CurrentSky
        {
            get => currentSky;
            protected set
            {
                currentSky = value;
                SkyChanged?.Invoke(value);
            }
        }

        private Dictionary<string, LoadTextureFromSasOptions> builtInTextures = new Dictionary<string, LoadTextureFromSasOptions>()
        {
            {"Autoshop",new LoadTextureFromSasOptions("builtin://Autoshop", TextureType.CubeMap)},
            {"BoilerRoom",new LoadTextureFromSasOptions("builtin://BoilerRoom", TextureType.CubeMap)},
            {"ColorfulStudio",new LoadTextureFromSasOptions("builtin://ColorfulStudio", TextureType.CubeMap)},
            {"Hangar",new LoadTextureFromSasOptions("builtin://Hangar", TextureType.CubeMap)},
            {"IndustrialPipeAndValve",new LoadTextureFromSasOptions("builtin://IndustrialPipeAndValve", TextureType.CubeMap)},
            {"Lebombo",new LoadTextureFromSasOptions("builtin://Lebombo", TextureType.CubeMap)},
            {"SataraNight",new LoadTextureFromSasOptions("builtin://SataraNight", TextureType.CubeMap)},
            {"SunnyVondelpark",new LoadTextureFromSasOptions("builtin://SunnyVondelpark", TextureType.CubeMap)},
            {"Syferfontein",new LoadTextureFromSasOptions("builtin://Syferfontein", TextureType.CubeMap)},
            {"TearsOfSteelBridge",new LoadTextureFromSasOptions("builtin://TearsOfSteelBridge", TextureType.CubeMap)},
            {"VeniceSunset",new LoadTextureFromSasOptions("builtin://VeniceSunset", TextureType.CubeMap)},
            {"WhippleCreekRegionalPark",new LoadTextureFromSasOptions("builtin://WhippleCreekRegionalPark", TextureType.CubeMap)},
            {"WinterRiver",new LoadTextureFromSasOptions("builtin://WinterRiver", TextureType.CubeMap)},
            {"DefaultSky",new LoadTextureFromSasOptions("builtin://DefaultSky", TextureType.CubeMap)}
        };

        public UnityBoolEvent OnCanSetSkyChanged;
        public override event Action<bool> CanSetSkyChanged;

        public UnityStringEvent OnSkyChanged;
        public override event Action<string> SkyChanged;

        public void Start()
        {
            // Hook up the event to the Unity event
            CanSetSkyChanged += (canSet) => OnCanSetSkyChanged?.Invoke(canSet);
            SkyChanged += (key) => OnSkyChanged?.Invoke(key);

            RemoteRenderingCoordinator.CoordinatorStateChange += ApplyStateToView;
            ApplyStateToView(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        private void ApplyStateToView(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    CanSetSky = true;
                    break;
                default:
                    CanSetSky = false;
                    break;
            }
        }

        public override async void SetSky(string skyKey)
        {
            if (!CanSetSky)
            {
                Debug.Log("Unable to set sky right now");
                return;
            }

            if (AvailableCubemaps.ContainsKey(skyKey))
            {
                Debug.Log("Setting sky to " + skyKey);
                //Load the texture into the session
                var texture = await RemoteRenderingCoordinator.CurrentSession.Connection.LoadTextureFromSasAsync(AvailableCubemaps[skyKey]);

                //Apply the texture to the SkyReflectionSettings
                RemoteRenderingCoordinator.CurrentSession.Connection.SkyReflectionSettings.SkyReflectionTexture = texture;
                SkyChanged?.Invoke(skyKey);
            }
            else
            {
                Debug.Log("Invalid sky key");
            }
        }
    }
    ```

    Der wichtigste Teil dieses Codes besteht aus nur wenigen Zeilen:

    ```cs
    //Load the texture into the session
    var texture = await RemoteRenderingCoordinator.CurrentSession.Connection.LoadTextureFromSasAsync(AvailableCubemaps[skyKey]);

    //Apply the texture to the SkyReflectionSettings
    RemoteRenderingCoordinator.CurrentSession.Connection.SkyReflectionSettings.SkyReflectionTexture = texture;
    ```

    Hier erhalten Sie einen Verweis auf die zu verwendende Textur, indem Sie sie aus dem integrierten Blob-Speicher in die Sitzung laden. Anschließend müssen wir diese Textur nur dem `SkyReflectionTexture`-Element der Sitzung zuweisen, um Sie anzuwenden.

1. Erstellen Sie ein leeres GameObject in Ihrer Szene, und geben Sie ihm den Namen **SkyLight**.

1. Fügen Sie das **RemoteSky**-Skript Ihrem **SkyLight**-GameObject hinzu.

    Der Wechsel zwischen den Himmelslichtern kann durch Aufrufen von `SetSky` mit einem der in `AvailableCubemaps` definierten Zeichenfolgenschlüsseln erfolgen. Der in **AppMenu** integrierte Ansichtscontroller erstellt automatisch Schaltflächen und verknüpft ihre Ereignisse, um `SetSky` mit dem jeweiligen Schlüssel aufzurufen.
1. Klicken Sie im Unity Editor auf „Abspielen“, und autorisieren Sie eine Verbindung.
1. Nachdem Sie die lokale Runtime mit einer Remotesitzung verbunden haben, navigieren Sie zu **AppMenu > Sitzungstools > RemoteSky**, um sich die verschiedenen Himmelsoptionen anzusehen und herauszufinden, wie diese sich auf das **TestModel** auswirken.

### <a name="scene-lights"></a>Szenenlichter

Zu Remoteszenenlichtern zählen Folgende: punktuelle, spotweise und diffuse Lichter. Ähnlich wie bei der zuvor erstellten Schnittebene handelt es sich bei diesen Szenenlichtern um Remoteentitäten, denen Komponenten angefügt sind. Ein wichtiger Aspekt bei der Beleuchtung Ihrer Remoteszene besteht darin, die Beleuchtung in Ihrer lokalen Szene anzugleichen. Diese Strategie ist nicht immer möglich, da viele Unity-Anwendungen für HoloLens 2 kein physisch basiertes Rendering für lokal gerenderte Objekte verwenden. Bis zu einem gewissen Punkt können wir jedoch die einfachere Standardbeleuchtung von Unity simulieren.

1. Erstellen Sie ein neues Skript mit dem Namen **RemoteLight**, und ersetzen Sie den Code durch den folgenden Code:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    [RequireComponent(typeof(Light))]
    public class RemoteLight : BaseRemoteLight
    {
        public bool AutomaticallyCreate = true;

        private bool lightReady = false;
        public override bool LightReady 
        {
            get => lightReady;
            set
            {
                lightReady = value;
                LightReadyChanged?.Invoke(lightReady);
            }
        }

        private ObjectType remoteLightType = ObjectType.Invalid;
        public override ObjectType RemoteLightType => remoteLightType;

        public UnityBoolEvent OnLightReadyChanged;

        public override event Action<bool> LightReadyChanged;

        private Light localLight; //Unity Light

        private Entity lightEntity;
        private LightComponentBase remoteLightComponent; //Remote Rendering Light

        private void Awake()
        {
            localLight = GetComponent<Light>();
            switch (localLight.type)
            {
                case LightType.Directional:
                    remoteLightType = ObjectType.DirectionalLightComponent;
                    break;
                case LightType.Point:
                    remoteLightType = ObjectType.PointLightComponent;
                    break;
                case LightType.Spot:
                case LightType.Area:
                    //Not supported in tutorial
                case LightType.Disc:
                    // No direct analog in remote rendering
                    remoteLightType = ObjectType.Invalid;
                    break;
            }
        }

        public void Start()
        {
            // Hook up the event to the Unity event
            LightReadyChanged += (ready) => OnLightReadyChanged?.Invoke(ready);

            RemoteRenderingCoordinator.CoordinatorStateChange += RemoteRenderingCoordinator_CoordinatorStateChange;
            RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        public void OnDestroy()
        {
            lightEntity?.Destroy();
        }

        private void RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    if (AutomaticallyCreate)
                        CreateLight();
                    break;
                default:
                    DestroyLight();
                    break;
            }
        }

        public override void CreateLight()
        {
            if (remoteLightComponent != null)
                return; //Nothing to do!

            //Create a root object for the light
            if(lightEntity == null)
                lightEntity = RemoteRenderingCoordinator.CurrentSession.Connection.CreateEntity();

            //Bind the remote entity to this game object
            lightEntity.BindToUnityGameObject(this.gameObject);

            //Sync the transform of this object so we can move the light
            var syncComponent = this.gameObject.GetComponent<RemoteEntitySyncObject>();
            syncComponent.SyncEveryFrame = true;

            //Add a light to the entity
            switch (RemoteLightType)
            {
                case ObjectType.DirectionalLightComponent:
                    var remoteDirectional = RemoteRenderingCoordinator.CurrentSession.Connection.CreateComponent(ObjectType.DirectionalLightComponent, lightEntity) as DirectionalLightComponent;
                    //No additional properties
                    remoteLightComponent = remoteDirectional;
                    break;

                case ObjectType.PointLightComponent:
                    var remotePoint = RemoteRenderingCoordinator.CurrentSession.Connection.CreateComponent(ObjectType.PointLightComponent, lightEntity) as PointLightComponent;
                    remotePoint.Radius = 0;
                    remotePoint.Length = localLight.range;
                    //remotePoint.AttenuationCutoff = //No direct analog in Unity legacy lights
                    //remotePoint.ProjectedCubeMap = //No direct analog in Unity legacy lights

                    remoteLightComponent = remotePoint;
                    break;
                default:
                    LightReady = false;
                    return;
            }

            // Set the common values for all light types
            UpdateRemoteLightSettings();

            LightReady = true;
        }

        public override void UpdateRemoteLightSettings()
        {
            remoteLightComponent.Color = localLight.color.toRemote();
            remoteLightComponent.Intensity = localLight.intensity;
        }

        public override void DestroyLight()
        {
            if (remoteLightComponent == null)
                return; //Nothing to do!

            remoteLightComponent.Destroy();
            remoteLightComponent = null;
            LightReady = false;
        }

        [ContextMenu("Sync Remote Light Configuration")]
        public override void RecreateLight()
        {
            DestroyLight();
            CreateLight();
        }

        public override void SetIntensity(float intensity)
        {
            localLight.intensity = Mathf.Clamp(intensity, 0, 1);
            UpdateRemoteLightSettings();
        }

        public override void SetColor(Color color)
        {
            localLight.color = color;
            UpdateRemoteLightSettings();
        }
    }
    ```

    Mit diesem Skript werden unterschiedliche Arten von Remotelichtern erstellt, abhängig vom Typ des lokalen Unity-Lichts, an das das Skript angefügt wird. Das Remotelicht dupliziert das lokale Licht in seiner Position, Orientierung, Farbe und Intensität. Wenn möglich, wird vom Remotelicht auch eine zusätzliche Konfiguration festgelegt. Diese ist keine perfekte Entsprechung, da es sich bei Unity-Lichtern nicht um PBR-Lichter handelt.

1. Suchen Sie das **DirectionalLight**-GameObject in Ihrer Szene. Wenn Sie das standardmäßige **DirectionalLight** aus Ihrer Szene entfernt haben, wählen Sie in der oberen Menüleiste *GameObject > Licht > DirectionalLight* aus, um in der Szene ein neues Licht zu erstellen.

1. Wählen Sie das **DirectionalLight-** -GameObject aus, und fügen Sie mithilfe der Schaltfläche **Komponente hinzufügen** das Skript **RemoteLight** hinzu.

1. Da dieses Skript die Basisklasse `BaseRemoteLight` implementiert, können Sie den bereitgestellten **AppMenu**-Ansichtscontroller verwenden, um mit dem Remotelicht zu interagieren. Navigieren Sie zu **AppMenu > Sitzungstools > Diffuses Licht**.

    > [!NOTE]
    > Die Benutzeroberfläche in **AppMenu** ist aus Gründen der Einfachheit auf ein einzelnes diffuses Licht beschränkt. Es ist jedoch weiterhin möglich und empfehlenswert, punktuelles Licht hinzuzufügen und das **RemoteLight**-Skript daran anzufügen. Diese zusätzlichen Lichter können Sie ändern, indem Sie die Eigenschaften des Unity-Lichts im Editor bearbeiten. Sie müssen die lokalen Änderungen manuell mit dem Remotelicht synchronisieren, indem Sie im Inspektor das Kontextmenü **RemoteLight** verwenden:
    >
    > ![Manuelles Synchronisieren des Remotelichts](./media/sync-remote-light.png)

1. Klicken Sie im Unity Editor auf „Abspielen“, und autorisieren Sie eine Verbindung.

1. Nachdem Sie eine Verbindung zwischen der Runtime und einer Remotesitzung hergestellt haben, positionieren Sie Ihre Kamera (verwenden Sie die Tasten W, A, S und D, klicken Sie mit der rechten Maustaste, und bewegen Sie die Maus), um den Ansichtscontroller für diffuses Licht anzuzeigen. 
1. Verwenden Sie den Ansichtscontroller für Remotelicht, um die Eigenschaften des Lichts zu ändern. Mithilfe der Handsimulation von MRTK können Sie das diffuse Licht greifen und drehen (halten Sie die STRG-Taste gedrückt), um die Auswirkung auf die Beleuchtung der Szene anzuzeigen.

    ![Gerichtetes Licht](./media/directional-light-test.png)

## <a name="editing-materials"></a>Material zur Bearbeitung

Remote gerendertes [Material](../../../concepts/materials.md) kann geändert werden, um zusätzliche visuelle Effekte bereitzustellen, visuelle Elemente von gerenderten Modellen zu optimieren oder Benutzern zusätzliches Feedback zu geben. Es gibt viele Methoden und viele Gründe, ein Material zu ändern. Hier zeigen wir Ihnen, wie Sie die Albedo-Farbe eines Materials sowie die Rauheit und die Metallartigkeit eines PBR-Materials ändern.

> [!NOTE]
> Wenn eine Funktion oder ein Effekt mithilfe von **HierarchicalStateOverrideComponent** implementiert werden kann, ist die Verwendung dieser Komponente in vielen Fällen dem Ändern des Materials vorzuziehen.

Wir erstellen ein Skript, das eine Zielentität annimmt und einige `OverrideMaterialProperty`-Objekte konfiguriert, um die Eigenschaften des Materials der Zielentität zu ändern. Wir beginnen mit dem Abrufen von [**MeshComponent**](../../../concepts/meshes.md#meshcomponent) der Zielentität, die eine Liste aller im Gittermodell verwendeten Materialien enthält. Der Einfachheit halber verwenden wir einfach das erste Material, das wir finden. Diese naive Strategie kann aufgrund der Erstellungsweise des Inhalts schnell fehlschlagen. Daher sollten Sie einen komplexeren Ansatz wählen, um das geeignete Material auszuwählen.

Aus dem Material können wir auf allgemeine Werte wie „Albedo“ zugreifen. Zuerst müssen die Materialien zum Abrufen der Werte in den entsprechenden Typ `PbrMaterial` oder `ColorMaterial` umgewandelt werden. Dies wird in der **GetMaterialColor**-Methode dargestellt. Sobald ein Verweis auf das gewünschte Material vorhanden ist, legen Sie einfach die Werte fest, und ARR erledigt die Synchronisierung zwischen den lokalen Materialeigenschaften und dem Remotematerial.

1. Erstellen Sie ein Skript namens **EntityOverrideController**, und ersetzen Sie seinen Inhalt durch den folgenden Code:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using System.Linq;
    using UnityEngine;
    // to prevent namespace conflicts
    using ARRMaterial = Microsoft.Azure.RemoteRendering.Material;

    public class EntityMaterialController : BaseEntityMaterialController
    {
        public override bool RevertOnEntityChange { get; set; } = true;

        public override OverrideMaterialProperty<Color> ColorOverride { get; set; }
        public override OverrideMaterialProperty<float> RoughnessOverride { get; set; }
        public override OverrideMaterialProperty<float> MetalnessOverride { get; set; }

        private Entity targetEntity;
        public override Entity TargetEntity
        {
            get => targetEntity;
            set
            {
                if (targetEntity != value)
                {
                    if (targetEntity != null && RevertOnEntityChange)
                    {
                        Revert();
                    }

                    targetEntity = value;
                    ConfigureTargetEntity();
                    TargetEntityChanged?.Invoke(value);
                }
            }
        }

        private ARRMaterial targetMaterial;
        private ARRMeshComponent meshComponent;

        public override event Action<Entity> TargetEntityChanged;
        public UnityRemoteEntityEvent OnTargetEntityChanged;

        public void Start()
        {
            // Forward events to Unity events
            TargetEntityChanged += (entity) => OnTargetEntityChanged?.Invoke(entity);

            // If there happens to be a remote RayCaster on this object, assume we should listen for events from it
            if (GetComponent<BaseRemoteRayCastPointerHandler>() != null)
                GetComponent<BaseRemoteRayCastPointerHandler>().RemoteEntityClicked += (entity) => TargetEntity = entity;
        }

        protected override void ConfigureTargetEntity()
        {
            //Get the Unity object, to get the sync object, to get the mesh component, to get the material.
            var targetEntityGameObject = TargetEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

            var localSyncObject = targetEntityGameObject.GetComponent<RemoteEntitySyncObject>();
            meshComponent = targetEntityGameObject.GetComponent<ARRMeshComponent>();
            if (meshComponent == null)
            {
                var mesh = localSyncObject.Entity.FindComponentOfType<MeshComponent>();
                if (mesh != null)
                {
                    targetEntityGameObject.BindArrComponent<ARRMeshComponent>(mesh);
                    meshComponent = targetEntityGameObject.GetComponent<ARRMeshComponent>();
                }
            }

            meshComponent.enabled = true;

            targetMaterial = meshComponent.RemoteComponent.Mesh.Materials.FirstOrDefault();
            if (targetMaterial == default)
            {
                return;
            }

            ColorOverride = new OverrideMaterialProperty<Color>(
                GetMaterialColor(targetMaterial), //The original value
                targetMaterial, //The target material
                ApplyMaterialColor); //The action to take to apply the override

            //If the material is a PBR material, we can override some additional values
            if (targetMaterial.MaterialSubType == MaterialType.Pbr)
            {
                var firstPBRMaterial = (PbrMaterial)targetMaterial;

                RoughnessOverride = new OverrideMaterialProperty<float>(
                    firstPBRMaterial.Roughness, //The original value
                    targetMaterial, //The target material
                    ApplyRoughnessValue); //The action to take to apply the override

                MetalnessOverride = new OverrideMaterialProperty<float>(
                    firstPBRMaterial.Metalness, //The original value
                    targetMaterial, //The target material
                    ApplyMetalnessValue); //The action to take to apply the override
            }
            else //otherwise, ensure the overrides are cleared out from any previous entity
            {
                RoughnessOverride = null;
                MetalnessOverride = null;
            }
        }

        public override void Revert()
        {
            if (ColorOverride != null)
                ColorOverride.OverrideActive = false;

            if (RoughnessOverride != null)
                RoughnessOverride.OverrideActive = false;

            if (MetalnessOverride != null)
                MetalnessOverride.OverrideActive = false;
        }

        private Color GetMaterialColor(ARRMaterial material)
        {
            if (material == null)
                return default;

            if (material.MaterialSubType == MaterialType.Color)
                return ((ColorMaterial)material).AlbedoColor.toUnity();
            else
                return ((PbrMaterial)material).AlbedoColor.toUnity();
        }

        private void ApplyMaterialColor(ARRMaterial material, Color color)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Color)
                ((ColorMaterial)material).AlbedoColor = color.toRemoteColor4();
            else
                ((PbrMaterial)material).AlbedoColor = color.toRemoteColor4();
        }

        private void ApplyRoughnessValue(ARRMaterial material, float value)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Pbr) //Only PBR has Roughness
                ((PbrMaterial)material).Roughness = value;
        }

        private void ApplyMetalnessValue(ARRMaterial material, float value)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Pbr) //Only PBR has Metalness
                ((PbrMaterial)material).Metalness = value;
        }
    }
    ```

Der `OverrideMaterialProperty`-Typ muss flexibel genug sein, damit falls gewünscht einige andere Materialwerte geändert werden können. Der `OverrideMaterialProperty`-Typ verfolgt den Status einer Außerkraftsetzung, behält den alten und neuen Wert bei und verwendet einen Delegaten, um die Außerkraftsetzung festzulegen. Sehen Sie sich als Beispiel `ColorOverride` an:

```cs
ColorOverride = new OverrideMaterialProperty<Color>(
    GetMaterialColor(targetMaterial), //The original value
    targetMaterial, //The target material
    ApplyMaterialColor); //The action to take to apply the override
```

Dadurch wird eine neue `OverrideMaterialProperty`-Eigenschaft erstellt, bei der die Außerkraftsetzung den Typ `Color` umschließt. Zum Erstellungszeitpunkt der Außerkraftsetzung wird die aktuelle oder die ursprüngliche Farbe bereitgestellt. Wir stellen außerdem ein ARR-Material für den Prozess bereit. Anschließend wird ein Delegat bereitgestellt, der die Außerkraftsetzung anwendet. Der Delegat ist eine Methode, die ein ARR-Material und den Typ, den die Außerkraftsetzung umhüllt, akzeptiert. Diese Methode ist der wichtigste Teil zum Verstehen der Anpassung der Materialwerte durch ARR.

`ColorOverride` verwendet die `ApplyMaterialColor`-Methode zum Erfüllen von Aufgaben:

```cs
private void ApplyMaterialColor(ARRMaterial material, Color color)
{
    if (material.MaterialSubType == MaterialType.Color)
        ((ColorMaterial)material).AlbedoColor = color.toRemoteColor4();
    else
        ((PbrMaterial)material).AlbedoColor = color.toRemoteColor4();
}
```

Dieser Code akzeptiert ein Material und eine Farbe. Zunächst wird die Art des Materials überprüft, und anschließend wird ein Abdruck des Materials erstellt, um die Farbe anzuwenden.

`RoughnessOverride` und `MetalnessOverride` funktionieren ähnlich, indem sie die `ApplyRoughnessValue`- und `ApplyMetalnessValue`-Methoden verwenden, um Aufgaben zu erfüllen.

Als nächstes testen wir den Materialcontroller.

1. Fügen Sie das Skript **EntityMaterialController** Ihrem **TestModel**-GameObject hinzu.
1. Klicken Sie in Unity auf „Abspielen“, um die Szene zu starten und eine Verbindung mit ARR herzustellen.
1. Nachdem Sie eine Verbindung zwischen der Runtime und einer Remotesitzung hergestellt und das Modell geladen haben, navigieren Sie zu **AppMenu > Modeltools > Material bearbeiten**.
1. Wählen Sie mithilfe der simulierten Hände eine Entität aus dem Modell aus, und klicken Sie auf **TestModel**.
1. Vergewissern Sie sich, dass der Materialansichtscontroller (**AppMenu > Modeltools > Material bearbeiten**) auf die Zielentität aktualisiert wurde.
1. Verwenden Sie den Materialansichtscontroller, um das Material für die Zielentität anzupassen.

Da wir nur das erste Material des Gittermodells ändern, wird Ihnen die Änderung des Materials möglicherweise nicht angezeigt. Verwenden Sie die hierarchische Außerkraftsetzung **SeeThrough**, um festzustellen, ob sich das von Ihnen geänderte Material im Gittermodell befindet.

![Beispiel für Materialbearbeitung](./media/material-edit-example.png)

## <a name="next-steps"></a>Nächste Schritte

Glückwunsch! Sie haben jetzt alle Kernfunktionen von Azure Remote Rendering implementiert. Im nächsten Kapitel wird beschrieben, wie Sie Azure Remote Rendering und den Blob-Speicher schützen. Es werden die ersten Schritte zum Freigeben einer kommerziellen Anwendung beschrieben, für die Azure Remote Rendering genutzt wird.

> [!div class="nextstepaction"]
> [Nächster Schritt: Schützen von Azure-Remote Rendering und Modellspeicher](../security/security.md)
---
title: Bearbeiten von Modellen
description: Bearbeiten von per Remotezugriff gerenderten Modellen durch Verschieben, Drehen, Skalieren und mehr
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: cec97134173cfc7879baf1d914d8f224a0736430
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593043"
---
# <a name="tutorial-manipulating-models"></a>Tutorial: Bearbeiten von Modellen

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Hinzufügen von visuellen Begrenzungen und Bearbeitungsbegrenzungen für per Remotezugriff gerenderte Modellen
> * Verschieben, Drehen und Skalieren
> * Raycast mit räumlichen Abfragen
> * Hinzufügen einfacher Animationen für per Remotezugriff gerenderte Objekte

## <a name="prerequisites"></a>Voraussetzungen

* Dieses Tutorial baut auf dem Tutorial [ Schnittstellen und benutzerdefinierte Modelle](../custom-models/custom-models.md) auf.

## <a name="query-remote-object-bounds-and-apply-to-local-bounds"></a>Abfragen von Remoterojektbegrenzungen und Anwenden auf lokale Begrenzungen

Für die Interaktion mit Remoteobjekten benötigen wir eine lokale Darstellung, mit der zuerst interagiert wird. [Objektbegrenzungen](../../../concepts/object-bounds.md) sind für die schnelle Bearbeitung eines Remoteobjekts hilfreich. Remotebegrenzungen können mithilfe der lokalen Entität als Verweis von ARR abgefragt werden. Die Begrenzungen werden abgefragt, nachdem das Modell in die Remotesitzung geladen wurde.

Die Begrenzungen eines Modells werden durch das Feld definiert, das das gesamte Modell enthält – wie z. B. [**BoxCollider**](https://docs.unity3d.com/Manual/class-BoxCollider.html) von Unity, für das ein Mittelpunkt und eine Größe für die x-, y- und z-Achsen definiert sind. **BoxCollider** von Unity wird verwendet, um die Begrenzungen des Remotemodells darzustellen.

1. Erstellen Sie ein neues Skript im gleichen Verzeichnis wie **RemoteRenderedModel**, und nennen Sie es **RemoteBounds**.
1. Ersetzen Sie den Inhalt des Skripts durch folgenden Code:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    [RequireComponent(typeof(BaseRemoteRenderedModel))]
    public class RemoteBounds : BaseRemoteBounds
    {
        //Remote bounds works with a specific remotely rendered model
        private BaseRemoteRenderedModel targetModel = null;

        private RemoteBoundsState currentBoundsState = RemoteBoundsState.NotReady;

        public override RemoteBoundsState CurrentBoundsState
        {
            get => currentBoundsState;
            protected set
            {
                if (currentBoundsState != value)
                {
                    currentBoundsState = value;
                    BoundsStateChange?.Invoke(value);
                }
            }
        }

        public override event Action<RemoteBoundsState> BoundsStateChange;

        public void Awake()
        {
            BoundsStateChange += HandleUnityEvents;
            targetModel = GetComponent<BaseRemoteRenderedModel>();

            targetModel.ModelStateChange += TargetModel_OnModelStateChange;
            TargetModel_OnModelStateChange(targetModel.CurrentModelState);
        }

        private void TargetModel_OnModelStateChange(ModelState state)
        {
            switch (state)
            {
                case ModelState.Loaded:
                    QueryBounds();
                    break;
                default:
                    BoundsBoxCollider.enabled = false;
                    CurrentBoundsState = RemoteBoundsState.NotReady;
                    break;
            }
        }

        // Create an async query using the model entity
        async private void QueryBounds()
        {
            //Implement me
        }
    }
    ```

    > [!NOTE]
    > Wenn in Visual Studio die Fehlermeldung *Das Feature "X" ist in C# 6 nicht verfügbar. Verwenden Sie die Sprachversion 7.0 oder höher.* angezeigt wird, können Sie sie ignorieren. Dies bezieht sich auf die Lösungs- und Projektgenerierung von Unity.

    Dieses Skript muss dem gleichen GameObject-Element hinzugefügt werden wie das Skript, das **BaseRemoteRenderedModel** implementiert. In diesem Fall heißt es **BaseRemoteRenderedModel**. So ähnlich wie bei vorherigen Skripts behandelt dieser anfängliche Code alle Zustandsänderungen, Ereignisse und Daten, die mit Remotebegrenzungen in Verbindung stehen.

    Es muss nur noch eine Methode implementiert werden: **QueryBounds**. **QueryBounds** ruft die Begrenzungen asynchron ab, und wendet das Ergebnis der Abfrage auf das lokale **BoxCollider**-Element an.

    Die **QueryBounds**-Methode ist unkompliziert: Senden Sie eine Abfrage an die Remote Rendering-Sitzung, und warten Sie auf das Ereignis.

1. Ersetzen Sie die **QueryBounds**-Methode durch die folgende abgeschlossene Methode:

    ```cs
    // Create a query using the model entity
    async private void QueryBounds()
    {
        remoteBoundsQuery = targetModel.ModelEntity.QueryLocalBoundsAsync();
        CurrentBoundsState = RemoteBoundsState.Updating;
        await remoteBounds;

        if (remoteBounds.IsCompleted)
        {
            var newBounds = remoteBounds.Result.toUnity();
            BoundsBoxCollider.center = newBounds.center;
            BoundsBoxCollider.size = newBounds.size;
            BoundsBoxCollider.enabled = true;
            CurrentBoundsState = RemoteBoundsState.Ready;
        }
        else
        {
            CurrentBoundsState = RemoteBoundsState.Error;
        }
    }
    ```

    Sie überprüfen die Abfrage, um festzustellen, ob sie erfolgreich war. Wenn ja, konvertieren Sie die zurückgegebenen Begrenzungen in ein Format, das von **BoxCollider** akzeptiert werden kann.

Wenn nun das **RemoteBounds**-Skript dem gleichen Spielobjekt hinzugefügt wird wie **RemoteRenderedModel**, wird bei Bedarf ein **BoxCollider**-Element hinzugefügt, und wenn das Modell seinen `Loaded`-Zustand erreicht, werden die Begrenzungen automatisch abgefragt und auf das **BoxCollider**-Element angewendet.

1. Fügen Sie mithilfe des zuvor erstellten GameObject-Elements **TestModel** die Komponente **RemoteBounds** hinzu.
1. Überprüfen Sie, ob das Skript hinzugefügt wurde.

     ![Hinzufügen der RemoteBounds-Komponente](./media/remote-bounds-script.png)

1. Erneutes Ausführen der Anwendung Kurz nach dem Laden des Modells sehen Sie die Begrenzungen für das Remoteobjekt. Sie sehen dann in etwa die folgenden Werte:

     ![Screenshot: Beispiel mit Remoteobjektbegrenzungen](./media/updated-bounds.png)

Nun haben wir ein lokales **BoxCollider**-Element, das mit exakten Begrenzungen für das Unity-Objekt konfiguriert ist. Die Begrenzungen ermöglichen die Visualisierung und Interaktion mithilfe derselben Strategien, die wir für ein lokal gerendertes Objekt verwenden würden. Beispielsweise Skripts, die die Transformation, die Physik und vieles mehr ändern.

## <a name="move-rotate-and-scale"></a>Verschieben, Drehen und Skalieren  

Das Verschieben, Drehen und Skalieren von per Remotezugriff gerenderten Objekten funktioniert genauso wie bei jedem anderen Unity-Objekt. **RemoteRenderingCoordinator** ruft in der `LateUpdate`-Methode `Update` in der aktuell aktiven Sitzung auf. Ein Teil davon, was `Update` macht, ist das Synchronisieren lokaler Modellentitätstransformationen mit ihren Remoteentsprechungen. Um ein per Remotezugriff gerendertes Modell zu verschieben, zu drehen oder zu skalieren, müssen Sie nur die Transformation des GameObject-Objekts, das das Remotemodell darstellt, verschieben, drehen oder skalieren. Hier wird die Transformation des übergeordneten GameObject-Elements geändert, dem das **RemoteRenderedModel**-Skript angefügt ist.

In diesem Tutorial wird MRTK für die Objektinteraktion verwendet. Der größte Teil der MRTK-spezifischen Implementierung zum Verschieben, Drehen und Skalieren eines Objekts wird in diesem Tutorial nicht behandelt. Es gibt einen Modellansichtscontroller, der innerhalb von **AppMenu** im Menü **Model Tools** (Modelltools) vorkonfiguriert ist.

1. Stellen Sie sicher, dass das zuvor erstellte GameObject-Element **TestModel** in der Szene enthalten ist.
1. Stellen Sie sicher, dass das Prefab **AppMenu** in der Szene enthalten ist.
1. Betätigen Sie die Wiedergabeschaltfläche von Unity, um die Szene wiederzugeben, und öffnen Sie das Menü **Model Tools** (Modelltools) innerhalb von **AppMenu**.
![Ansichtscontroller](./media/model-with-view-controller.png)

**AppMenu** verfügt über ein Untermenü **Model Tools** (Modelltools), das einen Ansichtscontroller für die Bindung mit dem Modell implementiert. Wenn das GameObject-Element eine **RemoteBounds**-Komponente enthält, fügt der Ansichtscontroller eine [**BoundingBox**](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_BoundingBox.html)-Komponente hinzu. Dabei handelt es sich um eine MRTK-Komponente, die einen Begrenzungsrahmen um ein Objekt mit einem **BoxCollider**-Element rendert. Ein [**ObjectManipulator**](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/api/Microsoft.MixedReality.Toolkit.UI.ObjectManipulator.html)-Element ist für Handinteraktionen zuständig. Diese Skripts ermöglichen es uns, das per Remotezugriff gerenderte Modell zu verschieben, zu drehen und zu skalieren.

1. Bewegen Sie die Maus in den Spielbereich, und klicken Sie darauf, um den Fokus zu legen.
1. Halten Sie die linke UMSCHALTTASTE mithilfe der [MRTK-Handsimulation](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/InputSimulation/InputSimulationService.html#hand-simulation) gedrückt.
1. Steuern Sie die simulierte Hand so, dass der Handstrahl auf das Testmodell zeigt.

    ![Zeigender Handstrahl](./media/handray-engine.png)

1. Halten Sie die linke Maustaste gedrückt, und ziehen Sie das Modell, um es zu verschieben.

Der per Remotezugriff gerenderte Inhalt sollte zusammen mit dem Begrenzungsrahmen angezeigt werden. Unter Umständen tritt zwischen dem Begrenzungsrahmen und dem Remoteinhalt eine Verzögerung auf. Diese Verzögerung hängt von Ihrer Internetlatenz und Bandbreite ab.

## <a name="ray-cast-and-spatial-queries-of-remote-models"></a>Raycast und räumliche Abfragen von Remotemodellen

Ein Box-Collider um Modelle eignet sich für die Interaktion mit dem gesamten Modell, er ist aber nicht detailliert genug, um mit einzelnen Teilen eines Modells zu interagieren. Hierfür können wir [Remote-Raycasts](../../../overview/features/spatial-queries.md#ray-casts) verwenden. Remote-Raycasting ist eine von Azure Remote Rendering bereitgestellte API zum Umwandeln von Strahlen in die Remoteszene und zum lokalen Zurückgeben der Trefferergebnisse. Diese Technik kann zum Auswählen untergeordneter Entitäten eines großen Modells oder zum Abrufen von Informationen zu Trefferergebnissen wie Position, Oberflächennormale und Entfernung verwendet werden.

Das Testmodell verfügt über eine Reihe untergeordneter Entitäten, die abgefragt und ausgewählt werden können. Derzeit gibt die Auswahl den Namen der ausgewählten Entität in der Unity-Konsole aus. Informationen zum Hervorheben der ausgewählten Entität finden Sie im Kapitel zu [Materialien, Beleuchtung und Effekten](../materials-lighting-effects/materials-lighting-effects.md#highlighting-and-outlining).

Zunächst erstellen wir einen statischen Wrapper um die Remote-Raycastabfragen. Dieses Skript akzeptiert eine Position und Richtung im Unity-Bereich, konvertiert es in die Datentypen, die vom Remote-Raycast akzeptiert werden, und gibt die Ergebnisse zurück. Das Skript verwendet die `RayCastQueryAsync`-API.

1. Erstellen Sie ein neues Skript namens **RemoteRayCaster**, und ersetzen Sie seinen Inhalt durch den folgenden Code:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System.Linq;
    using System.Threading.Tasks;
    using UnityEngine;

    /// <summary>
    /// Wraps the Azure Remote Rendering RayCast queries to easily send requests using Unity data types
    /// </summary>
    public class RemoteRayCaster
    {
        public static double maxDistance = 30.0;

        public static async Task<RayCastHit[]> RemoteRayCast(Vector3 origin, Vector3 dir, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            if(RemoteRenderingCoordinator.instance.CurrentCoordinatorState == RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected)
            {
                var rayCast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), maxDistance, hitPolicy);
                var result = await RemoteRenderingCoordinator.CurrentSession.Connection.RayCastQueryAsync(rayCast);
                return result.Hits;
            }
            else
            {
                return new RayCastHit[0];
            }
        }

        public static async Task<Entity[]> RemoteRayCastEntities(Vector3 origin, Vector3 dir, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            var hits = await RemoteRayCast(origin, dir, hitPolicy);
            return hits.Select(hit => hit.HitEntity).Where(entity => entity != null).ToArray();
        }
    }
    ```

    > [!NOTE]
    > Unity verfügt über eine Klasse mit dem Namen [**RaycastHit**](https://docs.unity3d.com/ScriptReference/RaycastHit.html), und Azure Remote Rendering verfügt über eine Klasse mit dem Namen [**RayCastHit**](/dotnet/api/microsoft.azure.remoterendering.raycasthit). Der Großbuchstabe **C** ist ein wichtiger Unterschied, der Kompilierungsfehler verhindert.

    **RemoteRayCaster** bietet einen allgemeinen Zugriffspunkt zum Umwandeln von Remotestrahlen in die aktuelle Sitzung. Als Nächstes soll ein MRTK-Zeigerhandler implementiert werden. Das Skript implementiert die `IMixedRealityPointerHandler`-Schnittstelle, die MRTK anweist, dass dieses Skript auf [Mixed Reality-Zeiger](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/Input/Pointers.html)-Ereignisse lauschen soll.

1. Erstellen Sie ein neues Skript namens **RemoteRayCastPointerHandler**, und ersetzen Sie den Code durch den folgenden Code:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.MixedReality.Toolkit.Input;
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using UnityEngine;

    public class RemoteRayCastPointerHandler : BaseRemoteRayCastPointerHandler, IMixedRealityPointerHandler
    {
        public UnityRemoteEntityEvent OnRemoteEntityClicked = new UnityRemoteEntityEvent();

        public override event Action<Entity> RemoteEntityClicked;

        public void Awake()
        {
            // Forward events to Unity events
            RemoteEntityClicked += (entity) => OnRemoteEntityClicked?.Invoke(entity);
        }

        public async void OnPointerClicked(MixedRealityPointerEventData eventData)
        {
            if (RemoteEntityClicked != null) //Ensure someone is listening before we do the work
            {
                var firstHit = await PointerDataToRemoteRayCast(eventData.Pointer);
                if (firstHit.success)
                    RemoteEntityClicked.Invoke(firstHit.hit.HitEntity);
            }
        }

        public void OnPointerDown(MixedRealityPointerEventData eventData) { }

        public void OnPointerDragged(MixedRealityPointerEventData eventData) { }

        public void OnPointerUp(MixedRealityPointerEventData eventData) { }

        private async Task<(bool success, RayCastHit hit)> PointerDataToRemoteRayCast(IMixedRealityPointer pointer, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            RayCastHit hit;
            var result = pointer.Result;
            if (result != null)
            {
                var endPoint = result.Details.Point;
                var direction = pointer.Rays[pointer.Result.RayStepIndex].Direction;
                Debug.DrawRay(endPoint, direction, Color.green, 0);
                hit = (await RemoteRayCaster.RemoteRayCast(endPoint, direction, hitPolicy)).FirstOrDefault();
            }
            else
            {
                hit = new RayCastHit();
            }
            return (hit.HitEntity != null, hit);
        }
    }
    ```

Die Methode `OnPointerClicked` von **RemoteRayCastPointerHandler** wird von MRTK aufgerufen, wenn ein Zeiger auf einen Collider wie z. B. unseren Box-Collider „klickt“. Anschließend wird `PointerDataToRemoteRayCast` aufgerufen, um das Ergebnis des Zeigers in einen Punkt und eine Richtung zu konvertieren. Dieser Punkt und diese Richtung werden dann verwendet, um einen Remotestrahl in der Remotesitzung umzuwandeln.

![Aktualisierte Begrenzungen](./media/raycast-local-remote.png)

Das Senden von Anforderungen für Raycasting auf Klick ist eine effiziente Strategie zum Abfragen von Remoteobjekten. Die Benutzererfahrung ist dabei jedoch nicht ideal, da der Cursor auf den Box-Collider und nicht auf das eigentliche Modell trifft.

Sie können auch einen neuen MRTK-Zeiger erstellen, der seine Strahlen in der Remotesitzung häufiger umwandelt. Dieser Ansatz ist zwar komplexer, die Benutzererfahrung ist dabei jedoch besser. Diese Strategie ist nicht Gegenstand dieses Tutorials, ein Beispiel für diesen Ansatz finden Sie jedoch in der Showcase-App, die im [ARR-Beispielrepository](https://github.com/Azure/azure-remote-rendering/tree/master/Unity/Showcase) zu finden ist.

Wenn ein Raycast im **RemoteRayCastPointerHandler**-Element erfolgreich abgeschlossen ist, wird der Treffer `Entity` aus dem Unity-Ereignis`OnRemoteEntityClicked` ausgegeben. Um auf dieses Ereignis zu reagieren, erstellen wir ein Hilfsskript, das `Entity` akzeptiert und eine Aktion dafür durchführt. Beginnen wir mit dem Erstellen des Skripts, um den Namen von `Entity` im Debugprotokoll auszugeben.

1. Erstellen Sie ein neues Skript mit dem Namen **RemoteEntityHelper**, und ersetzen Sie seinen Inhalt durch Folgendes:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.
    
    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using UnityEngine;
    
    public class RemoteEntityHelper : MonoBehaviour
    {
        public void EntityToDebugLog(Entity entity)
        {
            Debug.Log(entity.Name);
        }
    }
    ```

2. Fügen Sie im zuvor erstellten GameObject-Element **TestModel** sowohl die Komponente **RemoteRayCastPointerHandler** als auch die Komponente **RemoteEntityHelper** hinzu.
1. Weisen Sie die Methode `EntityToDebugLog` dem Ereignis `OnRemoteEntityClicked` zu. Wenn der Ausgabetyp des Ereignisses und der Eingabetyp der Methode stimmen, können wir die dynamische Ereigniseinbindung von Unity verwenden, die den Ereigniswert automatisch an die Methode übergibt.
    1. Erstellen Sie ein neues Rückruffeld: ![Rückruf hinzufügen](./media/add-callback-remote-entity-clicked.png)
    1. Ziehen Sie die Komponente **Remote Entity Helper** (Remoteentität-Hilfsprogramm) in das Objektfeld, um auf das übergeordnete GameObject-Element zu verweisen: ![Objekt zuweisen](./media/assign-object.png)
    1. Weisen Sie `EntityToDebugLog` als Rückruf zu: ![Rückruf zuweisen](./media/remote-entity-event.png)
1. Klicken Sie im Unity-Editor auf die Wiedergabeschaltfläche, um die Szene zu starten, eine Verbindung mit einer Remotesitzung herzustellen und das Testmodell zu laden.
1. Halten Sie die linke UMSCHALTTASTE mithilfe der MRTK-Handsimulation gedrückt.
1. Steuern Sie die simulierte Hand so, dass der Handstrahl auf das Testmodell zeigt.
1. Klicken Sie lange, um eine Tippbewegung zu simulieren und das `OnPointerClicked`-Ereignis auszuführen.
1. Achten Sie in der Unity-Konsole auf eine Protokollmeldung mit dem Namen der ausgewählten untergeordneten Entität. Beispiel: ![Beispiel für eine untergeordnete Entität](./media/child-entity-example.png)

## <a name="synchronizing-the-remote-object-graph-into-the-unity-hierarchy"></a>Synchronisieren des Remoteobjektgraphs in der Unity-Hierarchie

Bisher haben wir nur ein einzelnes lokales GameObject-Element gesehen, das das gesamte Modell darstellt. Dies funktioniert gut zum Rendern und Bearbeiten des gesamten Modells. Wenn wir jedoch Effekte anwenden oder bestimmte untergeordnete Entitäten bearbeiten möchten, müssen wir lokale GameObjects-Elemente erstellen, um diese Entitäten darzustellen. Als Erstes können wir das Testmodell manuell untersuchen.

1. Starten Sie die Szene, und laden Sie das Testmodell.
1. Erweitern Sie die untergeordneten Elemente des GameObject-Elements **TestModel** in der Unity-Hierarchie, und wählen Sie das GameObject-Element **TestModel_Entity** aus.
1. Klicken Sie im Inspektor auf die Schaltfläche *Show Children* (Untergeordnete Elemente anzeigen).
![Untergeordnete Elemente anzeigen](./media/show-remote-children.png)
1. Erweitern Sie untergeordnete Elemente in der Hierarchie, und klicken Sie auf *Show Children* (Untergeordnete Elemente anzeigen), bis eine lange Liste von untergeordneten Elementen angezeigt wird.
![Alle untergeordneten Objekte](./media/test-model-children.png)

Eine Liste mit Dutzenden von Entitäten füllt nun die Hierarchie auf. Wenn Sie eines dieser Elemente auswählen, werden die Komponenten `Transform` und `RemoteEntitySyncObject` im Inspektor angezeigt. Standardmäßig wird nicht jede Entität automatisch in jedem Frame synchronisiert, sodass lokale Änderungen an `Transform` nicht mit dem Server synchronisiert werden. Aktivieren Sie das Kontrollkästchen *Sync Every Frame* (Jeden Frame synchronisieren), und verschieben, skalieren oder drehen Sie anschließend die Transformation in der Szenenansicht. Das gerenderte Modell wird in der Szenenansicht nicht angezeigt. Eine visuelle Aktualisierung der Position und Drehung des Modells sehen Sie in der Spielansicht.

Der gleiche Prozess kann programmgesteuert ausgeführt werden und ist der erste Schritt beim Ändern bestimmter Remoteentitäten.

1. Ändern Sie das Skript **RemoteEntityHelper**, sodass es auch die folgende Methode enthält:

    ```cs
    public void MakeSyncedGameObject(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var sync = entityGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;
    }
    ```

1. Fügen Sie dem **RemoteRayCastPointerHandler**-Ereignis `OnRemoteEntityClicked` einen zusätzlichen Rückruf hinzu, und legen Sie ihn auf `MakeSyncedGameObject` fest.
![Zusätzlicher Rückruf](./media/additional-callback.png)
1. Halten Sie die linke UMSCHALTTASTE mithilfe der MRTK-Handsimulation gedrückt.
1. Steuern Sie die simulierte Hand so, dass der Handstrahl auf das Testmodell zeigt.
1. Klicken Sie lange, um eine Tippbewegung zu simulieren und das `OnPointerClicked`-Ereignis auszuführen.
1. Aktivieren und erweitern Sie die Hierarchie, um ein neues untergeordnetes Objekt anzuzeigen, das die angeklickte Entität darstellt.
![GameObject-Darstellung](./media/gameobject-representing-entity.png)
1. Entfernen Sie nach dem Testen den Rückruf für `MakeSyncedGameObject`, da wir ihn später in andere Effekte integrieren.

> [!NOTE]
> Das Synchronisieren jedes Frames ist nur erforderlich, wenn Sie die Transformationsdaten synchronisieren müssen. Der Aufwand für die Synchronisierung von Transformationen ist mit einem gewissen Aufwand verbunden. Daher sollten Sie dies nur sehr sparsam einsetzen.

Das Erstellen einer lokalen Instanz und die automatische Synchronisierung ist der erste Schritt bei der Bearbeitung von untergeordneten Entitäten. Die gleichen Techniken, die wir zum Bearbeiten des Modells als Ganzes verwendet haben, können auch in den untergeordneten Entitäten verwendet werden. Nachdem Sie z. B. eine synchronisierte lokale Instanz einer Entität erstellt haben, können Sie ihre Begrenzungen abfragen und Bearbeitungshandler hinzufügen, um die Navigation durch die Handstrahlen des Benutzers zu ermöglichen.

## <a name="next-steps"></a>Nächste Schritte

Sie können Ihre per Remotezugriff gerenderten Modelle jetzt bearbeiten und mit ihnen interagieren. Im nächsten Tutorial wird das Ändern von Materialien, das Ändern der Beleuchtung und das Anwenden von Effekten auf per Remotezugriff gerenderte Modelle behandelt.

> [!div class="nextstepaction"]
> [Nächster Schritt: Optimieren von Materialien, Beleuchtung und Effekten](../materials-lighting-effects/materials-lighting-effects.md)
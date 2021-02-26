---
title: Unity-Spielobjekte und -Komponenten
description: Hier werden spezifische Unity-Methoden zum Arbeiten mit Remote Rendering-Entitäten und -Komponenten beschrieben.
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 15822c357db63db81e6c1efda2467279a98d7c34
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594145"
---
# <a name="interact-with-unity-game-objects-and-components"></a>Interagieren mit Unity-Spielobjekten und -Komponenten

Azure Remote Rendering (ARR) ist für eine große Anzahl von Objekten optimiert (siehe [Einschränkungen](../../reference/limits.md)). Große und komplexe Hierarchien können zwar auf dem Host verwaltet werden, aber es ist unmöglich, diese Hierarchien in Unity auf Geräten mit geringer Leistung zu replizieren.

Daher geschieht Folgendes: Wenn ein Modell auf den Host geladen wird, spiegelt Azure Remote Rendering die Informationen zur Modellstruktur auf das Clientgerät (wodurch Netzwerkdatenverkehr entsteht), aber repliziert nicht die Objekte und Komponenten in Unity. Stattdessen müssen Sie die erforderlichen Unity-Spielobjekte und -Komponenten manuell anfordern – so lässt sich die Auslastung auf die tatsächlich benötigten Elemente reduzieren. Auf diese Weise haben Sie mehr Kontrolle über die Leistung auf Clientseite.

Folglich enthält die Unity-Integration von Azure Remote Rendering zusätzliche Funktionen, um die Remote Rendering-Struktur bei Bedarf zu replizieren.

## <a name="load-a-model-in-unity"></a>Laden eines Modells in Unity

Wenn Sie ein Modell laden, erhalten Sie einen Verweis auf das Stammobjekt des geladenen Modells. Dieser Verweis ist kein Unity-Spielobjekt, lässt sich aber mit der Erweiterungsmethode `Entity.GetOrCreateGameObject()` in ein solches umwandeln. Diese Funktion erwartet ein Argument vom Typ `UnityCreationMode`. Wenn Sie `CreateUnityComponents` übergeben, wird das neu erstellte Unity-Spielobjekt darüber hinaus mit Proxykomponenten für alle Remote Rendering-Komponenten aufgefüllt, die auf dem Host vorhanden sind. Um die Auslastung möglichst gering zu halten, wird jedoch die Verwendung von `DoNotCreateUnityComponents` empfohlen.

### <a name="load-model-with-unity-coroutines"></a>Laden eines Modells mit Unity-Coroutinen

```cs
IEnumerator LoadModelWithCoroutine(RenderingSession session)
{
    float currentProgress = 0.0f;
    var task = session.Connection.LoadModelFromSasAsync(new LoadModelFromSasOptions("builtin://Engine"),
        (float progress) =>
        {
            currentProgress = progress;
        });

    while (!task.IsCompleted && !task.IsFaulted)
    {
        int percentage = (int)(currentProgress * 100.0f);
        yield return null;
    }

    if (!task.IsFaulted)
    {
        var gameObject = task.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
    }
}
```

### <a name="load-model-with-await-pattern"></a>Laden eines Modells mit await-Mustern

```cs
async void LoadModelWithAwait(RenderingSession session)
{
    var result = await session.Connection.LoadModelFromSasAsync(new LoadModelFromSasOptions("builtin://Engine"), null);
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

Die oben stehenden Codebeispiele verwenden den Modellladepfad über SAS, weil das integrierte Modell geladen wird. Das Verarbeiten des Modells über Blobcontainer (mit `LoadModelAsync` und `LoadModelOptions`) funktioniert vollständig analog.

## <a name="remoteentitysyncobject"></a>RemoteEntitySyncObject

Beim Erstellen eines Unity-Spielobjekts wird implizit eine `RemoteEntitySyncObject`-Komponente zum Spielobjekt hinzugefügt. Diese Komponente wird zum Synchronisieren der Entitätstransformation mit dem Server verwendet. Standardmäßig erfordert `RemoteEntitySyncObject`, dass der Benutzer `SyncToRemote()` explizit aufruft, um den lokalen Unity-Status mit dem Server zu synchronisieren. Durch Aktivieren von `SyncEveryFrame` wird das Objekt automatisch synchronisiert.

Für Objekte mit einem `RemoteEntitySyncObject` können die untergeordneten Remoteelemente über die Schaltfläche **:::no-loc text="Show children":::** (Untergeordnete Elemente anzeigen) instanziiert und im Unity-Editor angezeigt werden.

![RemoteEntitySyncObject](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>Wrapperkomponenten

[Komponenten](../../concepts/components.md), die an Remote Rendering-Entitäten angefügt sind, werden in Unity über `MonoBehavior`-Proxys verfügbar gemacht. Diese Proxys repräsentieren die Remotekomponente in Unity und leiten alle Änderungen an den Host weiter.

Um Remote Rendering-Proxykomponenten zu erstellen, verwenden Sie die Erweiterungsmethode `GetOrCreateArrComponent`:

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>Gekoppelte Lebensdauerzeiträume

Die Lebensdauerzeiträume einer [Remoteentität](../../concepts/entities.md) und eines Unity-Spielobjekts sind gekoppelt, während die Entität und das Objekt über ein `RemoteEntitySyncObject` aneinander gebunden sind. Wenn Sie `UnityEngine.Object.Destroy(...)` mit einem solchen Spielobjekt aufrufen, wird auch die Remoteentität entfernt.

Um das Unity-Spielobjekt ohne Auswirkungen auf die Remoteentität zu zerstören, müssen Sie zuerst `Unbind()` im `RemoteEntitySyncObject` aufrufen.

Dies gilt für alle Proxykomponenten. Um nur die clientseitige Darstellung zu zerstören, müssen Sie zuerst `Unbind()` in der Proxykomponente aufrufen:

```cs
var cutplane = gameObject.GetComponent<ARRCutPlaneComponent>();
if (cutplane != null)
{
    cutplane.Unbind();
    UnityEngine.Object.Destroy(cutplane);
}
```

## <a name="next-steps"></a>Nächste Schritte

* [Einrichten von Remote Rendering für Unity](unity-setup.md)
* [Tutorial: Bearbeiten von Remoteentitäten in Unity](../../tutorials/unity/manipulate-models/manipulate-models.md)

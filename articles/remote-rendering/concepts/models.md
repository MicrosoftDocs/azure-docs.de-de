---
title: Modelle
description: Enthält eine Beschreibung der Bedeutung von Modellen in Azure Remote Rendering.
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 0d1e66d09db3e3934871ed15493feb685d1cbe6a
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593873"
---
# <a name="models"></a>Modelle

Ein *Modell* ist in Azure Remote Rendering eine vollständige Objektdarstellung, die aus [Entitäten](entities.md) und [Komponenten](components.md) besteht. Modelle stellen das Hauptverfahren dar, mit dem benutzerdefinierte Daten in den Remote Rendering-Dienst eingebunden werden.

## <a name="model-structure"></a>Modellstruktur

Ein Modell verfügt auf dem Stammknoten über genau eine [Entität](entities.md). Hierunter kann sich eine beliebige Hierarchie mit untergeordneten Entitäten befinden. Beim Laden eines Modells wird ein Verweis auf diese Stammentität zurückgegeben.

An jede Entität können [Komponenten](components.md) angefügt werden. In den meisten Fällen verfügen die Entitäten über *MeshComponents*, die auf [Gittermodellressourcen](meshes.md) verweisen.

## <a name="creating-models"></a>Erstellen von Modellen

Zur Erstellung von Modellen für die Runtime werden aus Dateiformaten wie FBX und GLTF [Eingabemodelle konvertiert](../how-tos/conversion/model-conversion.md). Beim Konvertierungsprozess werden alle Ressourcen extrahiert, z. B. Texturen, Materialien und Gittermodelle, und in optimierte Runtimeformate konvertiert. Außerdem werden die strukturellen Informationen extrahiert und in die Graphstruktur der Entitäten bzw. Komponenten von ARR konvertiert.

> [!IMPORTANT]
> Die [Modellkonvertierung](../how-tos/conversion/model-conversion.md) ist die einzige Möglichkeit, [Gittermodelle](meshes.md) zu erstellen. Gittermodelle können zur Runtime von Entitäten zwar gemeinsam genutzt werden, aber ein Gittermodell kann nur in die Runtime eingebunden werden, indem ein Modell geladen wird.

## <a name="loading-models"></a>Laden von Modellen

Nachdem ein Modell konvertiert wurde, kann es aus Azure-Blobspeicher in die Runtime geladen werden.

Es gibt zwei unterschiedliche Ladefunktionen, die sich danach unterscheiden, wie die Ressource im Blobspeicher hinterlegt ist:

* Auf das Modell kann direkt über Blobspeicherparameter zugegriffen werden (vorausgesetzt, der [Blobspeicher ist mit dem Konto verknüpft](../how-tos/create-an-account.md#link-storage-accounts)). In diesem Fall ist `LoadModelAsync` mit dem Parameter `LoadModelOptions` die relevante Ladefunktion.
* Auf das Modell kann über den SAS-URI zugegriffen werden. Die relevante Ladefunktion ist `LoadModelFromSasAsync` mit dem Parameter `LoadModelFromSasOptions`. Nutzen Sie diese Variante auch beim Laden von [integrierten Modellen](../samples/sample-model.md).

Die folgenden Codeausschnitte veranschaulichen das Laden von Modellen mit den Funktionen. Um ein Modell mithilfe seiner Blobspeicherparameter zu laden, verwenden Sie einen Code wie den folgenden:


```cs
async void LoadModel(RenderingSession session, Entity modelParent, string storageAccount, string containerName, string assetFilePath)
{
    // load a model that will be parented to modelParent
    var modelOptions = new LoadModelOptions(
        storageAccount, // storage account name + '.blob.core.windows.net', e.g., 'mystorageaccount.blob.core.windows.net'
        containerName,  // name of the container in your storage account, e.g., 'mytestcontainer'
        assetFilePath,  // the file path to the asset within the container, e.g., 'path/to/file/myAsset.arrAsset'
        modelParent
    );

    var loadOp = session.Connection.LoadModelAsync(modelOptions, (float progress) =>
    {
        Debug.WriteLine($"Loading: {progress * 100.0f}%");
    });

    await loadOp;
}
```

```cpp
void LoadModel(ApiHandle<RenderingSession> session, ApiHandle<Entity> modelParent, std::string storageAccount, std::string containerName, std::string assetFilePath)
{
    LoadModelOptions modelOptions;
    modelOptions.Parent = modelParent;
    modelOptions.Blob.StorageAccountName = std::move(storageAccount);
    modelOptions.Blob.BlobContainerName = std::move(containerName);
    modelOptions.Blob.AssetPath = std::move(assetFilePath);

    ApiHandle<LoadModelResult> result;
    session->Connection()->LoadModelAsync(modelOptions,
        // completion callback
        [](Status status, ApiHandle<LoadModelResult> result)
        {
            printf("Loading: finished.");
        },
        // progress callback
        [](float progress)
        {
            printf("Loading: %.1f%%", progress * 100.f);
        }
    );
}
```

Wenn Sie ein Modell mithilfe eines SAS-Tokens laden möchten, verwenden Sie einen Code ähnlich dem folgenden Codeausschnitt:

```cs
async void LoadModel(RenderingSession session, Entity modelParent, string modelUri)
{
    // load a model that will be parented to modelParent
    var modelOptions = new LoadModelFromSasOptions(modelUri, modelParent);

    var loadOp = session.Connection.LoadModelFromSasAsync(modelOptions, (float progress) =>
    {
        Debug.WriteLine($"Loading: {progress * 100.0f}%");
    });

    await loadOp;
}
```

```cpp
void LoadModel(ApiHandle<RenderingSession> session, ApiHandle<Entity> modelParent, std::string modelUri)
{
    LoadModelFromSasOptions modelOptions;
    modelOptions.ModelUri = modelUri;
    modelOptions.Parent = modelParent;

    ApiHandle<LoadModelResult> result;
    session->Connection()->LoadModelFromSasAsync(modelOptions,
        // completion callback
        [](Status status, ApiHandle<LoadModelResult> result)
        {
            printf("Loading: finished.");
        },
        // progress callback
        [](float progress)
        {
            printf("Loading: %.1f%%", progress * 100.f);
        }
    );
}
```

Anschließend können Sie die Entitätshierarchie durchlaufen und die Entitäten und Komponenten ändern. Beim mehrfachen Laden desselben Modells werden mehrere Instanzen erstellt, die jeweils über eine eigene Kopie der Struktur mit Entitäten bzw. Komponenten verfügen. Da es sich bei Gittermodellen, Materialien und Texturen um [gemeinsam verwendete Ressourcen](../concepts/lifetime.md) handelt, werden die entsprechenden Daten aber nicht noch einmal geladen. Aus diesem Grund fällt für das mehrmalige Instanziieren eines Modells relativ wenig Mehraufwand in Bezug auf den Arbeitsspeicher an.

## <a name="api-documentation"></a>API-Dokumentation

* [C# RenderingConnection.LoadModelAsync()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadmodelasync)
* [C# RenderingConnection.LoadModelFromSasAsync()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadmodelfromsasasync)
* [C++ RenderingConnection::LoadModelAsync()](/cpp/api/remote-rendering/renderingconnection#loadmodelasync)
* [C++ RenderingConnection::LoadModelFromSasAsync()](/cpp/api/remote-rendering/renderingconnection#loadmodelfromsasasync)

## <a name="next-steps"></a>Nächste Schritte

* [Entitäten](entities.md)
* [Gittermodelle](meshes.md)
---
title: Texturen
description: Workflow für Texturressourcen
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: e01ddf0690f11d41021e0a5ae5958c7c80646743
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594416"
---
# <a name="textures"></a>Texturen

Texturen sind unveränderliche [freigegebene Ressourcen](../concepts/lifetime.md). Texturen können aus dem [Blobspeicher](../how-tos/conversion/blob-storage.md) geladen und direkt auf Modelle angewendet werden, wie hier veranschaulicht: [Tutorial: Ändern der Umgebung und der Materialien](../tutorials/unity/materials-lighting-effects/materials-lighting-effects.md). Am häufigsten sind Texturen jedoch in einem [konvertierten Modell](../how-tos/conversion/model-conversion.md) zu finden, in dem sie anhand ihrer [Materialien](materials.md) referenziert werden.

## <a name="texture-types"></a>Typen von Texturen

Verschiedene Typen von Texturen weisen verschiedene Anwendungsfälle auf:

* **2D-Texturen** werden hauptsächlich in [Materialien](materials.md) verwendet.
* **Cubemaps** können für den [Himmel](../overview/features/sky.md) verwendet werden.

## <a name="supported-texture-formats"></a>Unterstützte Texturformate

Alle in Azure Remote Rendering (ARR) bereitgestellten Texturen müssen im [DDS-Format](https://en.wikipedia.org/wiki/DirectDraw_Surface) vorliegen – vorzugsweise mit Mipmaps und Texturkomprimierung.

## <a name="loading-textures"></a>Laden von Texturen

Wenn Sie eine Textur laden, müssen Sie den erwarteten Typ angeben. Sollten die Typen nicht übereinstimmen, kann die Textur nicht geladen werden.
Wenn eine Textur zweimal mit demselben URI geladen wird, wird dasselbe Texturobjekt zurückgegeben, da es sich um eine [freigegebene Ressource](../concepts/lifetime.md) handelt.

Ähnlich wie beim Laden von Modellen gibt es zwei Möglichkeiten, ein Texturobjekt im Quellblobspeicher zu adressieren:

* Die Adressierung der Textur kann direkt über Blobspeicherparameter erfolgen (vorausgesetzt, der [Blobspeicher ist mit dem Konto verknüpft](../how-tos/create-an-account.md#link-storage-accounts)). In diesem Fall ist `LoadTextureAsync` mit dem Parameter `LoadTextureOptions` die relevante Ladefunktion.
* Die Adressierung eines Texturobjekts kann über den SAS-URI erfolgen. Die relevante Ladefunktion ist `LoadTextureFromSasAsync` mit dem Parameter `LoadTextureFromSasOptions`. Nutzen Sie diese Variante auch beim Laden von [integrierten Texturen](../overview/features/sky.md#built-in-environment-maps).

Im folgenden Beispielcode wird das Laden einer Textur veranschaulicht:

```cs
async void LoadMyTexture(RenderingSession session, string storageContainer, string blobName, string assetPath)
{
    try
    {
        LoadTextureOptions options = new LoadTextureOptions(storageContainer, blobName, assetPath, TextureType.Texture2D);
        Texture texture = await session.Connection.LoadTextureAsync(options);
    
        // use texture...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void LoadMyTexture(ApiHandle<RenderingSession> session, std::string storageContainer, std::string blobName, std::string assetPath)
{
    LoadTextureOptions params;
    params.TextureType = TextureType::Texture2D;
    params.Blob.StorageAccountName = std::move(storageContainer);
    params.Blob.BlobContainerName = std::move(blobName);
    params.Blob.AssetPath = std::move(assetPath);
    session->Connection()->LoadTextureAsync(params, [](Status status, ApiHandle<Texture> texture)
    {
        // use texture...
    });
}
```

Beachten Sie, dass sich bei Verwendung ihrer SAS-Variante nur die Ladefunktion/Parameter unterscheiden.

Je nachdem, wofür die Textur verwendet werden soll, können Einschränkungen hinsichtlich Texturtyp und -inhalt gelten. Die Rauheitszuordnung eines [PBR-Materials](../overview/features/pbr-materials.md) beispielsweise muss in Graustufen vorliegen.

## <a name="api-documentation"></a>API-Dokumentation

* [C# Texture-Klasse](/dotnet/api/microsoft.azure.remoterendering.texture)
* [C# RenderingConnection.LoadTextureAsync()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtextureasync)
* [C# RenderingConnection.LoadTextureFromSasAsync()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtexturefromsasasync)
* [C++ Texture-Klasse](/cpp/api/remote-rendering/texture)
* [C++ RenderingConnection::LoadTextureAsync()](/cpp/api/remote-rendering/renderingconnection#loadtextureasync)
* [C++ RenderingConnection::LoadTextureFromSasAsync()](/cpp/api/remote-rendering/renderingconnection#loadtexturefromsasasync)

## <a name="next-steps"></a>Nächste Schritte

* [Materialien](materials.md)
* [Himmel](../overview/features/sky.md)
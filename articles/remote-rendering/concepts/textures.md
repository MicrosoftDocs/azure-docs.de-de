---
title: Texturen
description: Workflow für Texturressourcen
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: b951dab1ad01187c7612fad047bc52eb6aa9700e
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701873"
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

* Die Adressierung eines Texturobjekts kann über den SAS-URI erfolgen. Die relevante Ladefunktion ist `LoadTextureFromSASAsync` mit dem Parameter `LoadTextureFromSASParams`. Nutzen Sie diese Variante auch beim Laden von [integrierten Texturen](../overview/features/sky.md#built-in-environment-maps).
* Die Adressierung der Textur kann direkt über Blobspeicherparameter erfolgen (vorausgesetzt, der [Blobspeicher ist mit dem Konto verknüpft](../how-tos/create-an-account.md#link-storage-accounts)). In diesem Fall ist `LoadTextureAsync` mit dem Parameter `LoadTextureParams` die relevante Ladefunktion.

Der folgende Beispielcode zeigt, wie Sie eine Textur über ihren SAS-URI (oder die integrierte Textur) laden. Beachten Sie, dass sich nur die Ladefunktion bzw. der Ladeparameter ändert:

```cs
LoadTextureAsync _textureLoad = null;
void LoadMyTexture(AzureSession session, string textureUri)
{
    _textureLoad = session.Actions.LoadTextureFromSASAsync(new LoadTextureFromSASParams(textureUri, TextureType.Texture2D));
    _textureLoad.Completed +=
        (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
            _textureLoad = null;
        };
}
```

```cpp
void LoadMyTexture(ApiHandle<AzureSession> session, std::string textureUri)
{
    LoadTextureFromSASParams params;
    params.TextureType = TextureType::Texture2D;
    params.TextureUrl = std::move(textureUri);
    ApiHandle<LoadTextureAsync> textureLoad = *session->Actions()->LoadTextureFromSASAsync(params);
    textureLoad->Completed([](ApiHandle<LoadTextureAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            //use res->Result()
        }
        else
        {
            printf("Texture loading failed!");
        }
    });
}
```

Je nachdem, wofür die Textur verwendet werden soll, können Einschränkungen hinsichtlich Texturtyp und -inhalt gelten. Die Rauheitszuordnung eines [PBR-Materials](../overview/features/pbr-materials.md) beispielsweise muss in Graustufen vorliegen.

> [!CAUTION]
> Alle *Async*-Funktionen in ARR geben asynchrone Vorgangsobjekte zurück. Sie müssen einen Verweis auf diese Objekte speichern, bis der Vorgang abgeschlossen ist. Andernfalls wird der Vorgang vom Garbage Collector von C# unter Umständen zu einem frühen Zeitpunkt gelöscht und kann nicht beendet werden. Im obigen Beispielcode wird die Membervariable „_textureLoad“ verwendet, um einen Verweis zu speichern, bis das Ereignis *Completed* eintrifft.

## <a name="api-documentation"></a>API-Dokumentation

* [C# Texture-Klasse](/dotnet/api/microsoft.azure.remoterendering.texture)
* [C# RemoteManager.LoadTextureAsync()](/dotnet/api/microsoft.azure.remoterendering.remotemanager.loadtextureasync)
* [C# RemoteManager.LoadTextureFromSASAsync()](/dotnet/api/microsoft.azure.remoterendering.remotemanager.loadtexturefromsasasync)
* [C++ Texture-Klasse](/cpp/api/remote-rendering/texture)
* [C++ RemoteManager::LoadTextureAsync()](/cpp/api/remote-rendering/remotemanager#loadtextureasync)
* [C++ RemoteManager::LoadTextureFromSASAsync()](/cpp/api/remote-rendering/remotemanager#loadtexturefromsasasync)

## <a name="next-steps"></a>Nächste Schritte

* [Materialien](materials.md)
* [Himmel](../overview/features/sky.md)
---
title: Himmelsreflexionen
description: In diesem Artikel wird beschrieben, wie Sie Environment Maps für Himmelsspiegelungen einrichten.
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c5ad4b21b428f38bbd4d9f7d19fa633c5161b5c
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594179"
---
# <a name="sky-reflections"></a>Himmelsreflexionen

In Azure Remote Rendering wird eine Himmelstextur verwendet, um Objekte realistisch zu beleuchten. In Augmented-Reality-Anwendungen sollte diese Textur der realen Umgebung ähneln, damit die Objekte überzeugend echt erscheinen. In diesem Artikel wird beschrieben, wie Sie die Himmelstextur ändern.

> [!NOTE]
> Die Himmelstextur wird auch als *Environment Map* bezeichnet. Beide Begriffe werden synonym verwendet.

## <a name="object-lighting"></a>Objektbeleuchtung

In Azure Remote Rendering werden mithilfe von *Physically Based Rendering* (PBR) realistische Lichteffekte berechnet. Obwohl Sie auch [Lichtquellen](lights.md) zu Ihrer Szene hinzufügen können, ist die Verwendung einer guten Himmelstextur die effektivste Methode.

Die folgenden Bilder zeigen, was geschieht, wenn verschiedene Oberflächen nur mit einer Himmeltextur beleuchtet werden:

| Rauheit  | 0                                        | 0,25                                          | 0.5                                          | 0,75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| Nicht metallisch  | ![Dielektrisch, Rauheit=0](media/dielectric-0.png)   | ![Dielektrisch, Rauheit=0,25](media/dielectric-0.25.png)  | ![Dielektrisch, Rauheit=0,5](media/dielectric-0.5.png)  | ![Dielektrisch, Rauheit=0,75](media/dielectric-0.75.png)  | ![Dielektrisch, Rauheit=1](media/dielectric-1.png)  |
| Metallisch      | ![Metallisch, Rauheit=0](media/metallic-0.png)  | ![Metallisch, Rauheit=0,25](media/metallic-0.25.png)    | ![Metallisch, Rauheit=0,5](media/metallic-0.5.png)    | ![Metallisch, Rauheit=0,75](media/metallic-0.75.png)    | ![Metallisch, Rauheit=1](media/metallic-1.png)    |

Weitere Informationen zum Belichtungsmodell finden Sie im Kapitel [Materialien](../../concepts/materials.md).

> [!IMPORTANT]
> Himmelstexturen werden in Azure Remote Rendering nur für Belichtungsmodelle verwendet. Damit lässt sich nicht der Himmel als Hintergrund rendern, da Augmented-Reality-Anwendungen bereits über einen angemessenen Hintergrund verfügen: die echte Welt.

## <a name="changing-the-sky-texture"></a>Ändern der Himmelstextur

Zum Ändern der Environment Map müssen Sie lediglich [eine Textur laden](../../concepts/textures.md) und das `SkyReflectionSettings`-Objekt der Sitzung ändern:

```cs
async void ChangeEnvironmentMap(RenderingSession session)
{
    try
    {
        Texture skyTex = await session.Connection.LoadTextureFromSasAsync(new LoadTextureFromSasOptions("builtin://VeniceSunset", TextureType.CubeMap));
        session.Connection.SkyReflectionSettings.SkyReflectionTexture = skyTex;
    }
    catch (RRException exception)
    {
        System.Console.WriteLine($"Setting sky reflection failed: {exception.Message}");
    }
}
```

```cpp
void ChangeEnvironmentMap(ApiHandle<RenderingSession> session)
{
    LoadTextureFromSasOptions params;
    params.TextureType = TextureType::CubeMap;
    params.TextureUri = "builtin://VeniceSunset";
    session->Connection()->LoadTextureFromSasAsync(params, [&](Status status, ApiHandle<Texture> res) {
        if (status == Status::OK)
        {
            ApiHandle<SkyReflectionSettings> settings = session->Connection()->GetSkyReflectionSettings();
            settings->SetSkyReflectionTexture(res);
        }
        else
        {
            printf("Texture loading failed!\n");
        }
    });
}
```

Beachten Sie, dass oben die Variante `LoadTextureFromSasAsync` verwendet wird, da eine integrierte Textur geladen wird. Wenn Sie Daten aus [verknüpften Blobspeichern](../../how-tos/create-an-account.md#link-storage-accounts) laden, verwenden Sie die Variante `LoadTextureAsync`.

## <a name="sky-texture-types"></a>Typen von Himmelstexturen

Sie können sowohl *[Cube Maps](https://en.wikipedia.org/wiki/Cube_mapping)* als auch *2D-Texturen* als Environment Map verwenden.

Alle Texturen müssen in einem [unterstützten Texturformat](../../concepts/textures.md#supported-texture-formats) vorliegen. Für Himmelstexturen müssen Sie keine MipMaps bereitstellen.

### <a name="cube-environment-maps"></a>Cube-Environment-Maps

Dies ist ein Beispiel für eine aufgeklappte Cube Map:

![Aufgeklappte Cube Map](media/Cubemap-example.png)

Verwenden Sie `RenderingSession.Connection.LoadTextureAsync`/ `LoadTextureFromSasAsync` mit `TextureType.CubeMap`, um Cube-Map-Texturen zu laden.

### <a name="sphere-environment-maps"></a>Sphärische Environment Maps

Bei einer 2D-Textur als Environment Map muss sich das Bild in einem [sphärischen Koordinatenraum](https://en.wikipedia.org/wiki/Spherical_coordinate_system) befinden.

![Ein Himmelsbild in einem sphärischen Koordinatenraum](media/spheremap-example.png)

Verwenden Sie `RenderingSession.Connection.LoadTextureAsync` mit `TextureType.Texture2D`, um sphärische Environment Maps zu laden.

## <a name="built-in-environment-maps"></a>Integrierte Environment Maps

Azure Remote Rendering bietet einige integrierte Environment Maps, die immer verfügbar sind. Alle integrierten Environment Maps sind Cube Maps.

|Bezeichner                         | BESCHREIBUNG                                              | Abbildung                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | Vielfalt von Lichtstreifen, helle Innenraumgrundbeleuchtung    | ![Autoshop-Skybox zum Beleuchten eines Objekts verwendet](media/autoshop.png)
|builtin://BoilerRoom               | Helle Innenraum-Lichtszene, mehrere Fensterlichter      | ![BoilerRoom-Skybox zum Beleuchten eines Objekts verwendet](media/boiler-room.png)
|builtin://ColorfulStudio           | Unterschiedlich farbige Lichter in einer mittelhellen Innenraum-Lichtszene  | ![ColorfulStudio-Skybox zum Beleuchten eines Objekts verwendet](media/colorful-studio.png)
|builtin://Hangar                   | Mäßig helles Umgebungsflurlicht                     | ![SmallHangar-Skybox zum Beleuchten eines Objekts verwendet](media/hangar.png)
|builtin://IndustrialPipeAndValve   | Gedimmte Innenraum-Lichtszene mit einem Hell-Dunkel-Kontrast              | ![IndustrialPipeAndValve-Skybox zum Beleuchten eines Objekts verwendet](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Tagesumgebungslicht, Innenraum, helles Fensterlicht     | ![Lebombo-Skybox zum Beleuchten eines Objekts verwendet](media/lebombo.png)
|builtin://SataraNight              | Dunkler Nachthimmel und Boden mit vielen umgebenden Lichtern   | ![SataraNight-Skybox zum Beleuchten eines Objekts verwendet](media/satara-night.png)
|builtin://SunnyVondelpark          | Kontrast zwischen hellem Sonnenlicht und Schatten                      | ![SunnyVondelpark-Skybox zum Beleuchten eines Objekts verwendet](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Klares Himmelslicht mit mäßiger Bodenbeleuchtung            | ![Syferfontein-Skybox zum Beleuchten eines Objekts verwendet](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Moderater Übergang zwischen Licht (Sonne) und Schatten                         | ![TearsOfSteelBridge-Skybox zum Beleuchten eines Objekts verwendet](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | Licht des Sonnenuntergangs am Abend, fast Dämmerung                    | ![VeniceSunset-Skybox zum Beleuchten eines Objekts verwendet](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Helle, üppig grüne und weiße helle Töne, dunklerer Boden | ![WhippleCreekRegionalPark-Skybox zum Beleuchten eines Objekts verwendet](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | Tagsüber, helles Umgebungsbodenlicht                 | ![WhiteRiver-Skybox zum Beleuchten eines Objekts verwendet](media/winter-river.png)
|builtin://DefaultSky               | Identisch mit TearsOfSteelBridge                               | ![DefaultSky-Skybox zum Beleuchten eines Objekts verwendet](media/tears-of-steel-bridge.png)

## <a name="api-documentation"></a>API-Dokumentation

* [C# RenderingConnection.SkyReflectionSettings (Eigenschaft)](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.skyreflectionsettings)
* [C++ RenderingConnection::SkyReflectionSettings()](/cpp/api/remote-rendering/renderingconnection#skyreflectionsettings)

## <a name="next-steps"></a>Nächste Schritte

* [Lichter](../../overview/features/lights.md)
* [Materialien](../../concepts/materials.md)
* [Texturen](../../concepts/textures.md)

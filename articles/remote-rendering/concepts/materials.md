---
title: Materialien
description: Rendern von Materialbeschreibung und Materialeigenschaften
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 4406e3875bd2d56a33bcc9a94a24ad874fecf8da
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592176"
---
# <a name="materials"></a>Materialien

Materialien sind [freigegebene Ressourcen](../concepts/lifetime.md), die definieren, wie [Gittermodelle](meshes.md) gerendert werden. Mit Materialien wird angegeben, welche [Texturen](textures.md) angewendet und ob Objekte als transparent festgelegt werden sollen und wie die Beleuchtung berechnet wird.

Material werden während der [Modellkonvertierung](../how-tos/conversion/model-conversion.md) automatisch erstellt und können zur Laufzeit aufgerufen werden. Sie können auch benutzerdefinierte Materialien auf der Grundlage von Code erstellen und vorhandene Materialien ersetzen. Dieses Szenario empfiehlt sich besonders, wenn Sie das gleiche Material für viele Gittermodelle gemeinsam nutzen möchten. Da Änderungen an einem Material in jedem Gittermodell sichtbar sind, das darauf verweist, können mithilfe dieser Methode Änderungen einfach angewendet werden.

> [!NOTE]
> Einige Anwendungsfälle, wie z. B. das Markieren eines ausgewählten Objekts, können durch das Ändern von Materialien realisiert werden; sie werden jedoch über die [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md) viel einfacher erreicht.

## <a name="material-types"></a>Materialtypen

In Azure Remote Rendering gibt es zwei verschiedene Materialtypen:

* [PBR-Materialien](../overview/features/pbr-materials.md), die für Oberflächen verwendet werden, die mit möglichst hoher physischer Genauigkeit gerendert werden sollen. Realistische Beleuchtung wird für diese Materialien mithilfe von *Physically Based Rendering* (PBR) berechnet. Um diesen Materialtyp optimal zu nutzen, ist es wichtig, qualitativ hochwertige Eingabedaten bereitzustellen, z. B. Rauheit und Normal Maps.

* [Farbmaterialien](../overview/features/color-materials.md) werden für Fälle verwendet, in denen keine weitere Beleuchtung erwünscht ist. Diese Materialien zeichnen sich immer durch volle Helligkeit aus und können einfacher eingerichtet werden. Farbmaterialien werden für Daten verwendet, die entweder über keinerlei Beleuchtung verfügen oder die sich bereits durch eine statische Beleuchtung auszeichnen, beispielsweise Modelle, die mittels [Fotogrammetrie](https://en.wikipedia.org/wiki/Photogrammetry) erhalten wurden.

## <a name="mesh-vs-meshcomponent-material-assignment"></a>Gittermodell- im Vergleich zu MeshComponent-Materialzuweisung

[Gittermodelle](meshes.md) verfügen über ein oder mehrere untergeordnete Gittermodelle aufweisen. Jedes untergeordnete Gittermodell verweist auf ein Material. Sie können das zu verwendende Material entweder direkt im Gittermodell ändern, oder Sie können das für ein untergeordnetes Gittermodell zu verwendende Material in einer [MeshComponent](meshes.md#meshcomponent) außer Kraft setzen.

Wenn Sie ein Material direkt in der Gittermodellressource ändern, wirkt sich diese Änderung auf alle Instanzen des betreffenden Gittermodells aus. Eine Änderung in der MeshComponent wirkt sich hingegen nur auf die betreffende Gittermodellinstanz aus. Welche Methode besser geeignet ist, hängt vom gewünschten Verhalten ab. Der gängigste Ansatz ist aber das Ändern einer MeshComponent.

## <a name="material-de-duplication"></a>Deduplizierung von Materialien

Bei der Konvertierung werden mehrere Materialien mit denselben Eigenschaften und Texturen automatisch in ein einzelnes Material dedupliziert. Sie können dieses Feature in den [Konvertierungseinstellungen](../how-tos/conversion/configure-model-conversion.md) deaktivieren, zum Erzielen der optimalen Leistung empfiehlt es sich aber, das Feature beizubehalten.

## <a name="material-classes"></a>Materialklassen

Alle von der API bereitgestellten Materialien sind von der Basisklasse `Material` abgeleitet. Ihr Typ kann mit `Material.MaterialSubType` oder durch direkte Umwandlung abgefragt werden:

```cs
void SetMaterialColorToGreen(Material material)
{
    if (material.MaterialSubType == MaterialType.Color)
    {
        ColorMaterial colorMaterial = material as ColorMaterial;
        colorMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }

    PbrMaterial pbrMat = material as PbrMaterial;
    if (pbrMat != null)
    {
        pbrMat.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }
}
```

```cpp
void SetMaterialColorToGreen(ApiHandle<Material> material)
{
    if (material->GetMaterialSubType() == MaterialType::Color)
    {
        ApiHandle<ColorMaterial> colorMaterial = material.as<ColorMaterial>();
        colorMaterial->SetAlbedoColor({ 0, 1, 0, 1 });
        return;
    }

    if (material->GetMaterialSubType() == MaterialType::Pbr)
    {
        ApiHandle<PbrMaterial> pbrMat = material.as<PbrMaterial>();
        pbrMat->SetAlbedoColor({ 0, 1, 0, 1 });
        return;
    }
}
```

## <a name="api-documentation"></a>API-Dokumentation

* [C#-Klasse „Material“](/dotnet/api/microsoft.azure.remoterendering.material)
* [C#-Klasse „ColorMaterial“](/dotnet/api/microsoft.azure.remoterendering.colormaterial)
* [C#-Klasse „PbrMaterial“](/dotnet/api/microsoft.azure.remoterendering.pbrmaterial)
* [C# RenderingConnection.CreateMaterial()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.creatematerial)
* [C++-Klasse „Material“](/cpp/api/remote-rendering/material)
* [C++-Klasse „ColorMaterial“](/cpp/api/remote-rendering/colormaterial)
* [C++-Klasse „PbrMaterial“](/cpp/api/remote-rendering/pbrmaterial)
* [C++ RenderingConnection::CreateMaterial()](/cpp/api/remote-rendering/renderingconnection#creatematerial)

## <a name="next-steps"></a>Nächste Schritte

* [PBR-Materialien](../overview/features/pbr-materials.md)
* [Farbmaterialien](../overview/features/color-materials.md)
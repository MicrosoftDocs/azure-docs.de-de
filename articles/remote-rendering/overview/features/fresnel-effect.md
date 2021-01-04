---
title: Fresnel-Effekt
description: Seite zur Featureerläuterung für den Fresnel-Materialeffekt
author: jumeder
ms.author: jumeder
ms.date: 11/09/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: c8ecd3afc95cf4b8054cd7db3530c15afebea3ce
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498233"
---
# <a name="fresnel-effect"></a>Fresnel-Effekt

Das Materialfeature „Fresnel-Effekt“ ist ein physikalisch nicht korrekter Ad-hoc-Effekt. Das Feature basiert auf der physikalischen Beobachtung von Objekten, die unter diesen Winkeln stärker reflektieren. Die Fresnel-Reflexion selbst ist bereits physisch im [PBR-Materialmodell](../../overview/features/pbr-materials.md) enthalten, das in Azure Remote Rendering verwendet wird. Im Gegensatz dazu ist das Materialfeature „Fresnel-Effekt“ lediglich ein additiver Farbeffekt ohne Abhängigkeit von [Licht](../../overview/features/lights.md) oder der [Himmelumgebung](../../overview/features/sky.md).

Der Fresnel-Effekt verleiht den betroffenen Objekten an ihren Kanten einen farbigen Glanz. Informationen zur Effektanpassung und Beispiele für die Renderingergebnisse finden Sie in den folgenden Abschnitten.

## <a name="enabling-the-fresnel-effect"></a>Aktivieren des Fresnel-Effekts

Damit das Fresnel-Effektefeature verwendet werden kann, muss es für die betreffenden Materialien aktiviert werden. Sie können es aktivieren, indem Sie das FresnelEffekt-Bit von [PbrMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures) für das [PBR-Material](../../overview/features/pbr-materials.md) festlegen. Dasselbe Muster gilt für [ColorMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering) und [ColorMaterial](../../overview/features/color-materials.md). Eine Veranschaulichung der Verwendung finden Sie im Abschnitt der Codebeispiele.

Nach der Aktivierung ist der Fresnel-Effekt sofort sichtbar. Standardmäßig ist der Glanz weiß (1, 1, 1, 1, 1) und weist einen Exponenten von 1 auf. Sie können diese Einstellungen mithilfe der folgenden Parameter-Setter anpassen.

## <a name="customizing-the-effect-appearance"></a>Anpassen der Effektdarstellung

Gegenwärtig kann der Fresnel-Effekt mithilfe der folgenden Eigenschaften pro Material angepasst werden:

| Materialeigenschaft | type | Erklärung |
|-------------------|------|-------------|
| FresnelEffectColor | Color4 | Die Farbe, die höchstens durch den Fresnel-Glanz hinzugefügt wird. Der Alphakanal wird derzeit ignoriert. |
| FresnelEffectExponent | float | Die Verteilung des Fresnel-Glanzes. Diese reicht von 0,01 (über das gesamte Objekt verteilt) bis 10 (nur die am stärksten zierenden Winkel). |

In der Praxis werden verschiedene Farb- und Exponenteneinstellungen wie folgt aussehen:

![Beispiele zum Fresnel-Effekt](./media/fresnel-effect-examples.png)

Der Exponent des Fresnel-Effekts wird für jede Farbreihe progressiv von 1 bis 10 erhöht. Dadurch wird der Fresnel-Glanz progressiv an die Kanten der betrachteten Objekte gezogen. Der Fresnel-Effekt wird auch von der Transparenz nicht beeinflusst, wie Sie im folgenden Beispiel sehen können:

![Beispiele für die Fresnel-Effekttransparenz](./media/fresnel-effect-transparent-examples.png)

Wie gezeigt, sind die Objekte auf der Diagonale völlig transparent, aber der Fresnel-Glanz bleibt erhalten. Der Effekt ahmt in dieser Hinsicht ein physikalisch basiertes Fresnel nach, was auch in diesen Screenshots vorhanden ist.

## <a name="code-samples"></a>Codebeispiele

Die folgenden Codebeispiele zeigen die Aktivierung und Anpassung des Fresnel-Effekts sowohl für ein [PBR-Material](../../overview/features/pbr-materials.md) als auch für ein [Farbmaterial](../../overview/features/color-materials.md):

```cs
    void SetFresnelEffect(AzureSession session, Material material)
    {
        if (material.MaterialSubType == MaterialType.Pbr)
        {
            var pbrMaterial = material as PbrMaterial;
            pbrMaterial.PbrFlags |= PbrMaterialFeatures.FresnelEffect;
            pbrMaterial.FresnelEffectColor = new Color4(1.0f, 0.5f, 0.1f, 1.0f);
            pbrMaterial.FresnelEffectExponent = 3.141592f;
        }
        else if (material.MaterialSubType == MaterialType.Color)
        {
            var colorMaterial = material as ColorMaterial;
            colorMaterial.ColorFlags |= ColorMaterialFeatures.FresnelEffect;
            colorMaterial.FresnelEffectColor = new Color4(0.25f, 1.0f, 0.25f, 1.0f);
            colorMaterial.FresnelEffectExponent = 7.654321f;
        }
    }
```

```cpp
void SetFresnelEffect(ApiHandle<AzureSession> session, ApiHandle<Material> material)
{
    if (material->GetMaterialSubType() == MaterialType::Pbr)
    {
        auto pbrMaterial = material.as<PbrMaterial>();
        auto featureFlags = PbrMaterialFeatures((int32_t)pbrMaterial->GetPbrFlags() | (int32_t)PbrMaterialFeatures::FresnelEffect);
        pbrMaterial->SetPbrFlags(featureFlags);
        pbrMaterial->SetFresnelEffectColor(Color4{ 1.f, 0.5f, 0.1f, 1.f });
        pbrMaterial->SetFresnelEffectExponent(3.141592f);

    }
    else if (material->GetMaterialSubType() == MaterialType::Color)
    {
        auto colorMaterial = material.as<ColorMaterial>();
        auto featureFlags = ColorMaterialFeatures((int32_t)colorMaterial->GetColorFlags() | (int32_t)ColorMaterialFeatures::FresnelEffect);
        colorMaterial->SetColorFlags(featureFlags);
        colorMaterial->SetFresnelEffectColor(Color4{ 0.25f, 1.f, 0.25f, 1.f });
        colorMaterial->SetFresnelEffectExponent(7.654321f);
    }
}
```

## <a name="api-documentation"></a>API-Dokumentation

* [C# PbrMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures)
* [C++ PbrMaterialFeatures](/cpp/api/remote-rendering/pbrmaterialfeatures)
* [C# ColorMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.colormaterialfeatures)
* [C++ ColorMaterialFeatures](/cpp/api/remote-rendering/colormaterialfeatures)

## <a name="next-steps"></a>Nächste Schritte

* [Materialien](../../concepts/materials.md)
* [PBR-Materialien](../../overview/features/pbr-materials.md)
* [Farbmaterialien](../../overview/features/color-materials.md)
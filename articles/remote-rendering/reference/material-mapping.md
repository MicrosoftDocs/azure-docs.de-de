---
title: Materialzuordnung für Modellformate
description: In diesem Artikel wird die Standardkonvertierung von Modellquellformaten in PBR-Material beschrieben.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 8313243bf680ea1a1d63f2719b647149a04935a9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024043"
---
# <a name="material-mapping-for-model-formats"></a>Materialzuordnung für Modellformate

Wenn eine Quellressource [als Modell konvertiert](../how-tos/conversion/model-conversion.md) wird, erstellt der Konverter [Materialien](../concepts/materials.md) für alle [Gittermodelle](../concepts/meshes.md). Die Art und Weise, in der Materialien erstellt werden, kann [überschrieben](../how-tos/conversion/override-materials.md) werden. Bei der Konvertierung werden jedoch standardmäßig [PBR-Materialien](../overview/features/pbr-materials.md) erstellt. Da alle Quelldateiformate wie FVX ihre eigenen Konventionen zum Definieren von Materialien besitzen, müssen diese den PBR-Materialparametern von Azure Remote Rendering zugeordnet werden. 

In diesem Artikel werden die genauen Zuordnungen zum Konvertieren von Materialien aus Quellressourcen in Runtimematerialien aufgeführt.

## <a name="gltf"></a>glTF

Nahezu alle Zuordnungen der glTF 2.0-Spezifikation werden in Azure Remote Rendering unterstützt, mit Ausnahme von *EmissiveFactor* und *EmissiveTexture*.

Die folgende Tabelle zeigt die Zuordnung:

| glTF | Azure Remote Rendering |
|:-------------------|:--------------------------|
|   baseColorFactor   |   albedoColor              |
|   baseColorTexture  |   albedoMap                |
|   metallicFactor    |   metalness                |
|   metallicTexture   |   metalnessMap             |
|   roughnessFactor   |   roughness                |
|   roughnessTexture  |   roughnessMap             |
|   occlusionFactor   |   occlusion                |
|   occlusionTexture  |   occlusionMap             |
|   normalTexture     |   normalMap                |
|   alphaCutoff       |   alphaClipThreshold       |
|   alphaMode.OPAQUE  |   alphaClipEnabled = false, isTransparent = false |
|   alphaMode.MASK    |   alphaClipEnabled = true, isTransparent = false  |
|   alphaMode.BLEND   |   isTransparent = true     |
|   doubleSided       |   isDoubleSided            |
|   emissiveFactor    |   -                        |
|   emissiveTexture   |   -                        |

Alle Texturen in glTF können über einen `texCoord`-Wert verfügen, der ebenfalls in den Azure Remote Rendering-Materialien unterstützt wird.

### <a name="embedded-textures"></a>Eingebettete Texturen

In *\*.bin*- oder *\*.glb*-Dateien eingebettete Texturen werden unterstützt.

### <a name="supported-gltf-extension"></a>Unterstützte glTF-Erweiterung

Zusätzlich zu den Basisfeatures unterstützt Azure Remote Rendering die folgenden glTF-Erweiterungen:

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [KHR_materials_unlit:](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md) Diese Erweiterung entspricht dem Typ von [Farbmaterialien](../overview/features/color-materials.md). Diese Erweiterung wird für *selbstleuchtendes* Material empfohlen.
* [KHR_materials_pbrSpecularGlossiness:](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/README.md) Anstelle von groben metallischen Texturen können Sie „diffuse-specular-glossiness“ (Streuung-Glanzfarbe-Hochglanz) Texturen bereitstellen. Die Azure Remote Rendering-Implementierung unterliegt direkt den Konvertierungsformeln der Erweiterung.

## <a name="fbx"></a>FBX

Das FBX-Format ist proprietär, und FBX-Materialien sind im Allgemeinen nicht mit PBR-Materialien kompatibel. FBX verwendet komplexe Beschreibungen der Oberflächen mit vielen eindeutigen Parametern und Eigenschaften, **von denen nicht alle in der Azure Remote Rendering-Pipeline verwendet werden**.

> [!IMPORTANT]
> Die Modellkonvertierungspipeline von Azure Remote Rendering unterstützt nur **FBX 2011 und höher**.

Das FBX-Format definiert einen konservativen Ansatz für Materialien. In der offiziellen FBX-Spezifikation gibt es nur zwei Typen:

* *Lambert:* Dieser Typ wird seid einiger Zeit nicht mehr oft verwendet, wird jedoch weiterhin unterstützt, indem zur Konvertierungszeit zu Phong konvertiert wird.
* *Phong:* Fast alle Materialien und Inhaltstools verwenden diesen Typ.

Das Phong-Modell ist präziser und wird als *einziges* Modell für FBX-Materialien verwendet. Im Folgenden wird es als das *FBX-Material* bezeichnet.

> Die Maya-Anwendung verwendet zwei benutzerdefinierte Erweiterungen für FBX, indem benutzerdefinierte Eigenschaften für PBR- und Stingray-Materialtypen definiert werden. Diese Details sind nicht in der FBX-Spezifikation enthalten, daher werden sie derzeit nicht von Azure Remote Rendering unterstützt.

FBX-Materialien verwenden das Konzept „Diffuse-Specular-SpecularLevel“ (Streuung-Glanzfarbe-Glanzfarbenstärke), daher müssen die anderen Parameter für die Konvertierung einer Streuungstextur in eine Albedo-Zuordnung berechnet werden, um diese von der Streuung zu subtrahieren.

> Alle Farben und Texturen in FBX befinden sich im sRGB-Farbraum (auch als Gamma-Raum bekannt), jedoch verwendet Azure Remote Rendering bei der Visualisierung einen linearen Raum und konvertiert am Ende des Frame alles wieder in den sRGB-Farbraum. Die Azure Remote Rendering-Ressourcenpipeline konvertiert alles in den linearen Raum, um es als vorbereitete Daten an den Renderer zu senden.

In dieser Tabelle wird die Zuordnung von Texturen der FBX-Materialien zu Azure Remote Rendering-Materialien veranschaulicht. Einige werden nicht direkt, sondern in Kombination mit anderen Texturen in den Formeln verwendet (z. B. die Streuungstextur):

| FBX | Azure Remote Rendering |
|:-----|:----|
| AmbientColor | Occlusion Map   |
| DiffuseColor | *Wird für Albedo verwendet, Metallartigkeit* |
| TransparentColor | *Wird für den Alphakanal von Albedo verwendet* |
| TransparencyFactor | *Wird für den Alphakanal von Albedo verwendet* |
| Opacity | *Wird für den Alphakanal von Albedo verwendet* |
| SpecularColor | *Wird für Albedo verwendet, Metallartigkeit, Rauheit* |
| SpecularFactor| *Wird für Albedo verwendet, Metallartigkeit, Rauheit* |
| ShininessExponent | *Wird für Albedo verwendet, Metallartigkeit, Rauheit* |
| NormalMap | NormalMap |
| Bump | *Wird in NormalMap konvertiert* |
| EmissiveColor | - |
| EmissiveFactor | - |
| ReflectionColor | - |
| DisplacementColor | - |

Die obige Zuordnung ist der komplexeste Teil der Materialkonvertierung, da viele Annahmen getroffen werden müssen. Diese Annahmen werden im Folgenden erläutert.

Einige der folgenden Definitionen werden unten verwendet:

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`.Red × 0.2125 + `Specular`.Green × 0.7154 + `Specular`.Blue × 0.0721
* `DiffuseBrightness` = 0.299 × `Diffuse`.Red<sup>2</sup> + 0.587 × `Diffuse`.Green<sup>2</sup> + 0.114 × `Diffuse`.Blue<sup>2</sup>
* `SpecularBrightness` = 0.299 × `Specular`.Red<sup>2</sup> + 0.587 × `Specular`.Green<sup>2</sup> + 0.114 × `Specular`.Blue<sup>2</sup>
* `SpecularStrength` = max(`Specular`.Red, `Specular`.Green, `Specular`.Blue)

Die SpecularIntensity-Formel stammt von [hier](https://en.wikipedia.org/wiki/Luma_(video)).
Die Helligkeitsformel wird in dieser [Spezifikation](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf) beschrieben.

### <a name="roughness"></a>Rauheit

`Roughness` wird mithilfe [dieser Formel](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf) aus `Specular` und `ShininessExponent` berechnet. Die Formel ist eine Schätzung der Rauheit des Phong-Glanzfarbenexponenten:

```cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>Metallartigkeit

`Metalness` wird mithilfe [dieser Formel der glTF-Spezifikation](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js) aus `Diffuse` und `Specular` berechnet.

Hierbei wird folgende Gleichung gelöst: Ax<sup>2</sup> + Bx + C = 0.
Im Grunde reflektieren dielektrische Oberflächen etwa 4 % des Lichts auf eine glänzende Art und Weise, der Rest wird verstreut. Metallische Oberflächen reflektieren kein Licht verstreut, sondern komplett glänzend.
Diese Formel hat einige Nachteile, da es keine Möglichkeit gibt, zwischen glänzenden Plastik- und glänzenden Metalloberflächen zu unterscheiden. In den meisten Fällen wird davon ausgegangen, dass die Oberfläche metallische Eigenschaften aufweist, weshalb glänzende Plastik- oder Gummioberflächen nicht erwartungsgemäß aussehen.

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

A = dielectricSpecularReflectance
B = (DiffuseBrightness * (oneMinusSpecularStrength / (1 - A)) + SpecularBrightness) - 2 * A
C = A - SpecularBrightness
squareRoot = sqrt(max(0.0, B * B - 4 * A * C))
value = (-B + squareRoot) / (2 * A)
Metalness = clamp(value, 0.0, 1.0);
```

### <a name="albedo"></a>Albedo

`Albedo` wird aus `Diffuse`, `Specular` und `Metalness` berechnet.

Wie im Abschnitt zur Metallartigkeit beschrieben, reflektieren dielektrische Oberflächen etwa 4 % des Lichts.  
Hierbei wird der Wert `Metalness` als Faktor für die lineare Interpolation zwischen den Farben `Dielectric` und `Metal` verwendet. Wenn der metalness-Wert (Metallartigkeit) `0.0` lautet, liegt je nach Glanzfarbe entweder eine dunkle Farbe (bei hohem specular-Wert) vor oder die Streuung wird nicht geändert (wenn kein specular-Wert vorhanden ist). Wenn ein großer metalness-Wert vorliegt, verschwindet die Streufarbe zugunsten der Glanzfarbe.

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

`AlbedoRGB` wurde mithilfe der obigen Formel berechnet, jedoch erfordert der Alphakanal zusätzliche Berechnungen. Das FBX-Format ist bezüglich der Transparenz ungenau und verfügt über viele Möglichkeiten, diese zu definieren. Verschiedene Inhaltstools nutzen verschiedene Methoden. Die Idee besteht darin, sie in einer Formel zu kombinieren. Jedoch werden einige Ressourcen fälschlicherweise transparent angezeigt, wenn sie nicht auf gleiche Weise erstellt wurden.

Dies wird aus `TransparentColor`, `TransparencyFactor` und `Opacity` berechnet:

Wenn `Opacity` definiert ist, verwenden Sie den Wert direkt: `AlbedoAlpha` = `Opacity`.  
Wenn `TransparencyColor` definiert ist, dann gilt: `AlbedoAlpha` = 1.0 - ((`TransparentColor`.Red + `TransparentColor`.Green + `TransparentColor`.Blue)/3.0).  
Wenn `TransparencyFactor` definiert ist, dann gilt: `AlbedoAlpha` = 1.0 - `TransparencyFactor`.

Die letzte `Albedo`-Farbe besitzt vier Kanäle, die `AlbedoRGB` mit `AlbedoAlpha` kombinieren.

### <a name="summary"></a>Zusammenfassung

Zusammenfassend gilt, dass der `Albedo`-Wert dem originalen `Diffuse`-Wert sehr ähnlich ist, wenn der `Specular`-Wert nah an null (0) ist. Andernfalls ähnelt die Oberfläche einer metallischen Oberfläche und verliert die Streufarbe. Die Oberfläche sieht polierter und spiegelnder aus, wenn `ShininessExponent` groß genug und `Specular` hell ist. Andernfalls sieht die Oberfläche rau aus und reflektiert die Umgebung kaum.

### <a name="known-issues"></a>Bekannte Probleme

* Die aktuelle Formel funktioniert nicht sonderlich gut für einfache farbige Geometrie. Wenn `Specular` ausreichend hell ist, werden alle Geometrien reflektierende metallische Oberflächen ohne Farbe. Dieses Problem können Sie umgehen, indem Sie den `Specular`-Wert auf etwa 30 % des ursprünglichen Werts festlegen oder indem Sie die Konvertierungseinstellung [fbxAssumeMetallic](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model) verwenden.
* PBR-Materialien wurden vor kurzem zu den Inhaltserstellungstools `Maya` und `3DS Max` hinzugefügt. Diese verwenden benutzerdefinierte Blackboxeigenschaften, um sie an FBX zu übergeben. Azure Remote Rendering liest diese zusätzlichen Eigenschaften nicht, da sie nicht dokumentiert sind und das Format proprietär ist.

## <a name="next-steps"></a>Nächste Schritte

* [Modellkonvertierung](../how-tos/conversion/model-conversion.md)
* [Überschreiben von Materialien während der Modellkonvertierung](../how-tos/conversion/override-materials.md)

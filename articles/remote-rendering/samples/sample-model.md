---
title: Beispielmodelle
description: Hier finden Sie eine Liste mit Quellen für Beispielmodelle.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 6817601659c841ca98031f4e3e1590743bbed171
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530534"
---
# <a name="sample-models"></a>Beispielmodelle

In diesem Artikel werden einige Ressourcen für Beispieldaten aufgelistet, die zum Testen des Azure Remote Rendering-Diensts verwendet werden können.

## <a name="built-in-sample-model"></a>Integriertes Beispielmodell

Wir stellen ein integriertes Beispielmodell bereit, das immer mithilfe der URL **builtin://Engine** geladen werden kann.

![Beispielmodell](./media/sample-model.png "Beispielmodell")

Modellstatistik:

| Name | Wert |
|-----------|:-----------|
| [Erforderliche Servergröße](../reference/vm-sizes.md) | Standard |
| Anzahl von Dreiecken | 18,7 Mio. |
| Anzahl beweglicher Teile | 2\.073 |
| Materialanzahl | 94 |

## <a name="third-party-data"></a>Drittanbieterdaten

Die Khronos Group pflegt eine Reihe von glTF-Beispielmodellen zu Testzwecken. Das glTF-Format wird von ARR sowohl in Textform ( *. gltf*) als auch in Binärform ( *.glb*) unterstützt. Die besten visuellen Ergebnisse lassen sich mit den PBR-Modellen erzielen:

* [glTF-Beispielmodelle](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Rendern eines Modells mit Unity](../quickstarts/render-model.md)
* [Schnellstart: Konvertieren eines Modells für das Rendering](../quickstarts/convert-model.md)

---
title: Shell-Rendering
description: Erläutert die Verwendung des Shell-Renderingeffekts
author: jumeder
ms.author: jumeder
ms.date: 10/23/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f59c4f8225d31b61df08f30863c8b9300e20e820
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94447313"
---
# <a name="shell-rendering"></a>Shell-Rendering

Der Shell-Status der [Komponente für die hierarchische Zustandsüberschreibung](../../overview/features/override-hierarchical-state.md) ist ein Transparenzeffekt. Im Gegensatz zum [Durchsichts](../../overview/features/override-hierarchical-state.md)-Rendering ist nur die vorderste Ebene des Objekts sichtbar, ähnlich wie beim deckenden Rendering. Darüber hinaus kann die normale Erscheinung des Objekts geändert werden, wenn es in Form von Shells gerendert wird. Der Effekt ist für Anwendungsfälle gedacht, in denen der Benutzer visuell von nicht wichtigen Teilen fortgeleitet werden soll und zugleich räumliche Informationen für die Gesamtszene erhalten bleiben sollen.

Sie können die Darstellung von als Shell gerenderten Objekten mithilfe des globalen Zustands `ShellRenderingSettings` konfigurieren. Für alle Objekte, die Shell-Rendering verwenden, gilt die gleiche Einstellung. Es gibt keine objektweisen Parameter.

## <a name="shellrenderingsettings-parameters"></a>ShellRenderingSettings-Parameter

Die Klasse `ShellRenderingSettings` enthält die Einstellungen für die globalen Shell-Renderingeigenschaften:

| Parameter      | Typ    | BESCHREIBUNG                                             |
|----------------|---------|---------------------------------------------------------|
| `Desaturation` | float   | Das Maß an Entsättigung, das auf die übliche endgültige Objektfarbe angewendet werden soll, im Bereich von 0 (keine Entsättigung) bis 1 (vollständige Entsättigung) |
| `Opacity`      | float   | Die Deckkraft der als Shell gerenderten Objekte im Bereich von 0 (unsichtbar) bis 1 (vollständig deckend) |

Beispiele für die Wirkung der Parameter bei der Anwendung auf eine gesamte Szene finden Sie auch in der folgenden Tabelle:

|                | 0 | 0,25 | 0,5 | 0,75 | 1,0 | 
|----------------|:-:|:----:|:---:|:----:|:---:|
| **Entsättigung** | ![Desaturation-0.0](./media/shell-desaturation-00.png) | ![Desaturation-0.25](./media/shell-desaturation-025.png) | ![Desaturation-0.5](./media/shell-desaturation-05.png) | ![Desaturation-0.75](./media/shell-desaturation-075.png) | ![Desaturation-1.0](./media/shell-desaturation-10.png) |
| **Deckkraft**      | ![Opacity-0.0](./media/shell-opacity-00.png) | ![Opacity-0.25](./media/shell-opacity-025.png) | ![Opacity-0.5](./media/shell-opacity-05.png) | ![Opacity-0.75](./media/shell-opacity-075.png) | ![Opacity-1.0](./media/shell-opacity-10.png) |

Der Shelleffekt wird auf die endgültige deckende Farbe angewendet, mit der die Szene andernfalls gerendert würde. Dies schließt die [hierarchische Zustandsüberschreibung der Tönungsfarbe](../../overview/features/override-hierarchical-state.md) ein.

## <a name="example"></a>Beispiel

Der folgende Code zeigt eine Beispielnutzung des `ShellRenderingSettings`-Zustands mithilfe der API:

```cs
void SetShellSettings(AzureSession session)
{
    ShellRenderingSettings shellRenderingSettings = session.Actions.ShellRenderingSettings;
    shellRenderingSettings.Desaturation = 0.5f;
    shellRenderingSettings.Opacity = 0.1f;
}
```

```cpp
void SetShellSettings(ApiHandle<AzureSession> session)
{
    ApiHandle<ShellRenderingSettings> shellRenderingSettings = session->Actions()->GetShellRenderingSettings();
    shellRenderingSettings->SetDesaturation(0.5f);
    shellRenderingSettings->SetOpacity(0.1f);
}
```

## <a name="performance"></a>Leistung

Das Shell-Renderingfeature bedeutet einen geringen ständigen Mehraufwand im Vergleich zum standardmäßigen deckenden Rendering. Es ist bedeutend schneller als die Verwendung von transparenten Materialien auf Objekten oder [Durchsichts](../../overview/features/override-hierarchical-state.md)-Rendering. Die Leistung kann stärker abfallen, wenn nur Teile der Szene auf Shell-Rendering umgestellt werden. Dieser Leistungsabfall kann durch die zusätzlich offenbarten Objekte hervorgerufen werden, die nun gerendert werden müssen. In dieser Hinsicht ist das Leistungsverhalten ähnlich wie beim [Schnittebenen](../../overview/features/cut-planes.md)-Feature.

## <a name="next-steps"></a>Nächste Schritte

* [Komponente für hierarchische Zustandsüberschreibung](../../overview/features/override-hierarchical-state.md)
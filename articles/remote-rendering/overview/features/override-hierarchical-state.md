---
title: Hierarchische Zustandsüberschreibung
description: Erläutert das Konzept der Komponenten zur hierarchischen Zustandsüberschreibung
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 851a87885ac765c829e8c2be9fd1205e22906ca9
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445153"
---
# <a name="hierarchical-state-override"></a>Hierarchische Zustandsüberschreibung

In vielen Fällen ist es notwendig, die Darstellung von Teilen eines [Modells](../../concepts/models.md) dynamisch zu ändern, z. B. das Ausblenden von Unterdiagrammen oder das Tauschen von Teilen für das transparente Rendering. Es ist nicht sehr praktikabel, die Materialien jedes involvierten Teils zu ändern, da es erforderlich ist, das gesamte Szenendiagramm zu durchlaufen und die Materialklonung sowie die Zuweisung auf jedem Knoten zu verwalten.

Verwenden Sie die Komponente `HierarchicalStateOverrideComponent`, um diesen Anwendungsfall mit dem geringstmöglichen Aufwand zu lösen. Diese Komponente implementiert Updates für den hierarchischen Zustand für beliebige Branches des Szenendiagramms. Das bedeutet, dass der Zustand auf jeder Ebene im Szenendiagramm definiert werden kann. Die Hierarchie wird so lange durchsucht, bis der Zustand entweder von einem neuen Zustand überschrieben oder auf ein Blattobjekt angewendet wird.

Nehmen Sie als Beispiel das Modell eines Autos: Sie möchten, dass das gesamte Auto transparent dargestellt wird, mit Ausnahme des Motors. Der Anwendungsfall umfasst nur zwei Instanzen der Komponente:

* Die erste Komponente wird dem Stammknoten des Modells zugewiesen und aktiviert das transparente Rendering für das gesamte Auto.
* Die zweite Komponente wird dem Stammknoten des Motors zugewiesen und überschreibt den Zustand erneut, indem der durchsichtige Modus explizit überschrieben wird.

## <a name="features"></a>Features

Die festgelegten Zustände, die überschrieben werden können, sind die folgenden:

* **`Hidden`** : Entsprechende Gittermodelle im Szenendiagramm werden ausgeblendet oder angezeigt.
* **`Tint color`** : Ein gerendertes Objekt kann mit einzelnen Farben und Sättigung getönt werden. Im Bild unten ist die Felge eines Rads getönt dargestellt.
  
  ![Tönungsfarbe, um ein Objekt in Grün darzustellen](./media/color-tint.png)

* **`See-through`** : Die Geometrie wird semitransparent gerendert, um beispielsweise die inneren Teile eines Objekts anzuzeigen. Die folgende Abbildung zeigt das gesamte Auto, das mit Ausnahme des roten Bremssattels im durchsichtigen Modus gerendert wird:

  ![Durchsichtiger Modus, mit dem ausgewählte Objekte transparent gemacht werden](./media/see-through.png)

  > [!IMPORTANT]
  > Der durchsichtige Effekt funktioniert nur, wenn der [Renderingmodus](../../concepts/rendering-modes.md) *TileBasedComposition* verwendet wird.

* **`Shell`** : Die Geometrie wird als transparente, entsättigte Shell gerendert. In diesem Modus können unwichtige Teile einer Szene ausgeblendet werden, während trotzdem ein Gefühl für die Form und relative Positionierung erhalten bleibt. Verwenden Sie den Status [ShellRenderingSettings](shell-effect.md), um die Renderingdarstellung der Shell zu ändern. In der folgenden Abbildung sehen Sie das Automodell vollständig als Shell gerendert, mit Ausnahme der blauen Federn:

  ![Der Shellmodus wird zum Ausblenden bestimmter Objekte verwendet.](./media/shell.png)

  > [!IMPORTANT]
  > Der Shellmodus funktioniert nur, wenn der [Renderingmodus](../../concepts/rendering-modes.md) *TileBasedComposition* verwendet wird.

* **`Selected`** : Die Geometrie wird mit einer [Auswahlgliederung](outlines.md) gerendert.

  ![Konturoption zum Hervorheben eines ausgewählten Teils](./media/selection-outline.png)

* **`DisableCollision`** : Die Geometrie ist von [räumlichen Abfragen](spatial-queries.md) ausgenommen. Das **`Hidden`** -Flag wirkt sich nicht auf das Kollisionsstatus-Flag aus, deshalb werden diese beiden Flags häufig zusammen festgelegt.

* **`UseCutPlaneFilterMask`** : Verwenden Sie eine einzelne Filterbitmaske, um die Auswahl der Schnittebene zu steuern. Dieses Flag bestimmt, ob die jeweilige Filtermaske verwendet oder von der übergeordneten Filtermaske geerbt werden soll. Die Filterbitmaske selbst wird mit der `CutPlaneFilterMask`-Eigenschaft festgelegt. Ausführliche Informationen zur Funktionsweise der Filterung finden Sie im Abschnitt [Selektive Schnittebenen](cut-planes.md#selective-cut-planes). Im folgenden Beispiel sind nur der Reifen und die Felge angeschnitten, während der Rest der Szene unbeeinflusst bleibt.
![Selektive Schnittebenen](./media/selective-cut-planes-hierarchical-override.png)


> [!TIP]
> Als Alternative zum Deaktivieren der Sichtbarkeit und räumlicher Abfragen für ein vollständiges Unterdiagramm kann der Status `enabled` eines Spielobjekts umgeschaltet werden. Wenn eine Hierarchie deaktiviert ist, hat dies Vorrang vor jeder `HierarchicalStateOverrideComponent`.

## <a name="hierarchical-overrides"></a>Hierarchische Überschreibungen

Die `HierarchicalStateOverrideComponent` kann auf mehreren Ebenen einer Objekthierarchie angefügt werden. Da auf einer Entität nur eine Komponente jedes Typs vorhanden sein darf, verwaltet jede `HierarchicalStateOverrideComponent` die Zustände für „ausgeblendet“, „durchsichtig“, „ausgewählt“, „getönt“ und „collision“

Deshalb kann jeder Zustand auf eine der folgenden Optionen festgelegt werden:

* `ForceOn`: Der Zustand wird für alle Gittermodelle auf und unterhalb dieses Knotens aktiviert.
* `ForceOff`: Der Zustand wird für alle Gittermodelle auf und unterhalb dieses Knotens deaktiviert.
* `InheritFromParent`: Der Zustand ist von dieser Überschreibungskomponente nicht betroffen.

Sie können Zustände direkt oder über die `SetState`-Funktion ändern:

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

```cpp
ApiHandle<HierarchicalStateOverrideComponent> component = ...;

// set one state directly
component->SetHiddenState(HierarchicalEnableState::ForceOn);

// or: set a state with the SetState function
component->SetState(HierarchicalStates::SeeThrough, HierarchicalEnableState::InheritFromParent);

// set multiple states at once with the SetState function
component->SetState(
    (HierarchicalStates)((int32_t)HierarchicalStates::Hidden | (int32_t)HierarchicalStates::DisableCollision), HierarchicalEnableState::ForceOff);

```

### <a name="tint-color"></a>Tönungsfarbe

Das Überschreiben der `tint color` ist etwas Besonderes, da es sowohl den Zustand „ on/off/inherit“ (an, aus, erben) als auch eine Tönungsfarbeneigenschaft gibt. Der Alpha-Teil der Tönungsfarbe definiert die Sättigung des Tönungseffekts: Wenn der Wert auf 0,0 festgelegt ist, ist keine Tönung sichtbar. Wenn der Wert auf 1,0 festgelegt ist, wird das Objekt mit reiner Farbe dargestellt. Für Zwischenwerte wird die endgültige Farbe mit der Tönungsfarbe gemischt. Die Tönungsfarbe kann auf Pro-Frame-Basis geändert werden, um eine Farbanimation zu erreichen.

## <a name="performance-considerations"></a>Überlegungen zur Leistung

Eine Instanz von `HierarchicalStateOverrideComponent` selbst führt nicht zu einem großen Runtimemehraufwand. Jedoch bewährt es sich immer, die Anzahl der aktiven Komponenten niedrig zu halten. Wenn Sie beispielsweise ein Auswahlsystem implementieren, das das ausgewählte Objekt hervorhebt, wird empfohlen, die Komponente zu löschen, wenn die Hervorhebung entfernt wird. Die Beibehaltung der Komponenten mit neutralen Features kann sich schnell häufen.

Durch das transparente Rendering werden die GPUs des Servers durch mehr Workload belastet als beim Standardrendering. Wenn große Teile des Szenendiagramms zu *durchsichtig* wechseln und viele Geometrieebenen sichtbar sind, kann dies zu einem Leistungsengpass führen. Das gleiche gilt für Objekte mit [Auswahlkonturen](../../overview/features/outlines.md#performance) und für das [Shell-Rendering](../../overview/features/shell-effect.md#performance) . 

## <a name="api-documentation"></a>API-Dokumentation

* [HierarchicalStateOverrideComponent-Klasse (C#)](/dotnet/api/microsoft.azure.remoterendering.hierarchicalstateoverridecomponent)
* [HierarchicalStateOverrideComponent-Klasse (C++)](/cpp/api/remote-rendering/hierarchicalstateoverridecomponent)

## <a name="next-steps"></a>Nächste Schritte

* [Gliederungen](../../overview/features/outlines.md)
* [Renderingmodi](../../concepts/rendering-modes.md)
* [Räumliche Abfragen](../../overview/features/spatial-queries.md)
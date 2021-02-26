---
title: Schnittebenen
description: Erläutert, was Schnittebenen sind und wie sie verwendet werden.
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: b3348e5a999b507aa0d286528970beb0e03f26cd
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594371"
---
# <a name="cut-planes"></a>Schnittebenen

Eine *Schnittebene* ist eine visuelle Funktion, die Pixel auf einer Seite einer virtuellen Ebene beschneidet und das Innere von [Gittermodellen](../../concepts/meshes.md) enthüllt.
In der folgenden Abbildung wird der Effekt veranschaulicht. Auf der linken Seite wird das ursprüngliche Gittermodell angezeigt, auf der rechten Seite sehen Sie das Innere des Gittermodells:

![Schnittebene](./media/cutplane-1.png)

## <a name="cutplanecomponent"></a>CutPlaneComponent

Sie fügen der Szene eine Schnittebene hinzu, indem Sie eine *CutPlaneComponent* erstellen. Die Position und die Ausrichtung der Ebene werden von der [Besitzerentität](../../concepts/entities.md) der Komponente bestimmt.

```cs
void CreateCutPlane(RenderingSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Connection.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

```cpp
void CreateCutPlane(ApiHandle<RenderingSession> session, ApiHandle<Entity> ownerEntity)
{
    ApiHandle<CutPlaneComponent> cutPlane = session->Connection()->CreateComponent(ObjectType::CutPlaneComponent, ownerEntity)->as<CutPlaneComponent>();;
    cutPlane->SetNormal(Axis::X); // normal points along the positive x-axis of the owner object's orientation
    Color4Ub fadeColor;
    fadeColor.channels = { 255, 0, 0, 128 }; // fade to 50% red
    cutPlane->SetFadeColor(fadeColor);
    cutPlane->SetFadeLength(0.05f); // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>CutPlaneComponent-Eigenschaften

Die folgenden Eigenschaften werden für eine Schnittebenenkomponente bereitgestellt:

* `Enabled`: Sie können Schnittebenen temporär ausschalten, indem Sie die Komponente deaktivieren. Deaktivierte Schnittebenen verursachen keinen Renderingmehraufwand und werden auch nicht in den globalen Grenzwert von Schnittebenen einbezogen.

* `Normal`: Gibt an, welche Richtung (+X, -X, +Y, -Y, +Z, -Z) als normale Ebene verwendet wird. Diese Richtung ist relativ zur Ausrichtung der Besitzerentität. Verschieben und drehen Sie die Besitzerentität zur genauen Platzierung.

* `FadeColor` und `FadeLength`:

  Wenn der Alphawert von *FadeColor* ungleich NULL ist, werden Pixel in der Nähe der Schnittebene in Richtung des RGB-Anteils von FadeColor ausgeblendet. Die Stärke des Alphakanals bestimmt, ob die Ausblendung vollständig bis in die Ausblendfarbe oder nur teilweise erfolgt. *FadeLength* definiert, über welche Entfernung diese Ausblendung stattfindet.

* `ObjectFilterMask`: Eine Filterbitmaske, die bestimmt, welche Geometrie von der Schnittebene betroffen ist. Ausführliche Informationen finden Sie im nächsten Absatz.

### <a name="selective-cut-planes"></a>Selektive Schnittebenen

Es ist möglich, einzelne Schnittebenen so zu konfigurieren, dass sie nur bestimmte Geometrien betreffen. Die folgende Abbildung veranschaulicht, wie dieses Setup in der Praxis aussehen kann:

![Selektive Schnittebenen](./media/selective-cut-planes.png)

Die Filterung erfolgt durch den **Vergleich logischer Bitmasken** zwischen einer Bitmaske auf der Seite der Schnittebene und einer zweiten Bitmaske, die für die Geometrie festgelegt ist. Wenn das Ergebnis eines logischen `AND`-Vorgangs zwischen den Masken nicht null ist, wirkt sich die Schnittebene auf die Geometrie aus.

* Die Bitmaske für die Schnittebenenkomponente wird über deren `ObjectFilterMask`-Eigenschaft festgelegt
* Die Bitmaske für eine Geometrie-Unterhierarchie wird über die Eigenschaft [HierarchicalStateOverrideComponent](override-hierarchical-state.md#features) festgelegt

Beispiele:

| Schnittebenen-Filtermaske | Geometrie-Filtermaske  | Ergebnis des logischen `AND` | Wirkt sich die Schnittebene auf die Geometrie aus?  |
|--------------------|-------------------|-------------------|:----------------------------:|
| (0000 0001) == 1   | (0000 0001) == 1  | (0000 0001) == 1  | Ja |
| (1111 0000) == 240 | (0001 0001) == 17 | (0001 0000) == 16 | Ja |
| (0000 0001) == 1   | (0000 0010) == 2  | (0000 0000) == 0  | Nein |
| (0000 0011) == 3   | (0000 1000) == 8  | (0000 0000) == 0  | Nein |

>[!TIP]
> Das Festlegen der `ObjectFilterMask` einer Schnittebene auf 0 bedeutet, dass sie sich in keiner Weise auf Geometrie auswirkt, da das Ergebnis eines logischen `AND` niemals einen anderen Wert als null ergeben kann. Das Renderingsystem berücksichtigt solche Ebenen von vornherein nicht, dies stellt also ein unaufwändiges Verfahren zum Deaktivieren einzelner Schnittebenen dar. Außerdem werden diese Schnittebenen im Grenzwert von 8 aktiven Ebenen nicht berücksichtigt.

## <a name="limitations"></a>Einschränkungen

* Azure Remote Rendering unterstützt **maximal acht aktive Schnittebenen** . Sie können weitere Schnittebenenkomponenten erstellen, aber wenn Sie versuchen, mehrere Komponenten gleichzeitig zu aktivieren, wird die Aktivierung ignoriert. Deaktivieren Sie zuerst andere Ebenen, wenn Sie die Komponenten austauschen möchten, die sich auf die Szene auswirken sollen.
* Schnittebenen sind ein rein visuelles Feature, das sich nicht auf das Ergebnis [räumlicher Abfragen](spatial-queries.md) auswirkt. Wenn Sie einen Strahl in ein aufgeschnittenes Gittermodell werfen möchten, können Sie den Ausgangspunkt des Strahls so anpassen, dass er auf der Schnittebene liegt. Auf diese Weise kann der Strahl nur auf sichtbare Teile treffen.

## <a name="performance-considerations"></a>Überlegungen zur Leistung

Jede aktive Schnittebene verursacht beim Rendern geringfügige Kosten. Deaktivieren oder löschen Sie Schnittebenen, wenn Sie nicht benötigt werden.

## <a name="api-documentation"></a>API-Dokumentation

* [C# CutPlaneComponent-Klasse](/dotnet/api/microsoft.azure.remoterendering.cutplanecomponent)
* [C++ CutPlaneComponent-Klasse](/cpp/api/remote-rendering/cutplanecomponent)

## <a name="next-steps"></a>Nächste Schritte

* [Einseitiges Rendering](single-sided-rendering.md)
* [Räumliche Abfragen](spatial-queries.md)
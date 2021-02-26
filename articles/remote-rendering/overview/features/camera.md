---
title: Kamera
description: Beschreibt Kameraeinstellungen und Anwendungsfälle
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: dbe86313054706af974ccb324a39e942e9b5ca44
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594128"
---
# <a name="camera"></a>Kamera

Um sicherzustellen, dass lokal und remote gerenderte Inhalte nahtlos zusammengesetzt werden können, müssen verschiedene Kameraeigenschaften zwischen Server und Client synchron bleiben. Vor allem die Kameratransformation und -projektion. Beispielsweise muss für die lokal gerenderten Inhalte die gleiche Kameratransformation und -projektion verwendet werden, mit denen der letzte Remoteframe gerendert wurde.

![Lokale und Remote-Kamera](./media/camera.png)

In der Abbildung oben hat sich die lokale Kamera gegenüber dem vom Server gesendeten Remoteframe bewegt. Das hat zur Folge, dass die lokalen Inhalte aus der gleichen Perspektive wie der Remoteframe gerendert werden müssen, und schließlich wird das resultierende Bild erneut in den lokalen Kameraraum projiziert, was im Detail unter [Late Stage Reprojection (LSR)](late-stage-reprojection.md) erläutert wird.

Die Verzögerung zwischen dem Remote- und dem lokalen Rendering bedeutet, dass jede Änderung dieser Einstellungen mit einer Verzögerung angewendet wird. Daher kann jeder neue Wert nicht sofort innerhalb des gleichen Frames verwendet werden.

Die Abstände für nahe und ferne Ebenen können in den Kameraeinstellungen festgelegt werden. In der HoloLens 2 sind die verbleibenden Transformations- und Projektionsdaten durch die Hardware definiert. Wenn Sie die [Desktopsimulation](../../concepts/graphics-bindings.md) verwenden, werden sie über die Eingabe ihrer `Update`-Funktion festgelegt.

Die geänderten Werte können lokal verwendet werden, sobald das erste Remoteframe ankommt, das mit ihnen gerendert wurde. In HoloLens 2 werden die Daten, die für das Rendering verwendet werden sollen, vom *HolographicFrame* bereitgestellt, ganz wie in jeder anderen Holografieanwendung. In der [Desktopsimulation](../../concepts/graphics-bindings.md) werden sie über die Ausgabe der `Update`-Funktion abgerufen.

## <a name="camera-settings"></a>Kameraeinstellungen

Die folgenden Eigenschaften können in den Kameraeinstellungen geändert werden:

**Nahe und ferne Ebene:**

Damit sichergestellt ist, dass keine ungültigen Bereiche festgelegt werden können, sind die Eigenschaften **NearPlane** und **FarPlane** schreibgeschützt, und es ist eine separate Funktion **SetNearAndFarPlane** zum Ändern des Bereichs implementiert. Diese Daten werden am Ende des Frames an den Server gesendet. Wenn Sie diese Werte festlegen, muss **NearPlane** kleiner als **FarPlane** sein. Andernfalls tritt ein Fehler auf.

> [!IMPORTANT]
> In Unity geschieht dies automatisch, wenn die nahe und ferne Ebene der Hauptkamera geändert werden.

**EnableDepth**:

Manchmal ist es nützlich, den Schreibvorgang des Tiefenpuffers für das Remotebild zu Debuggingzwecken zu deaktivieren. Durch das Deaktivieren der Tiefe beendet der Server außerdem das Senden von Tiefeninformationen, wodurch sich die Bandbreite verringert.

> [!TIP]
> In Unity steht eine Debugkomponente mit dem Namen **EnableDepthComponent** zur Verfügung, die zum Umschalten dieses Features in der Editor-Benutzeroberfläche verwendet werden kann.

**InverseDepth:**

> [!NOTE]
> Diese Einstellung ist nur wichtig, wenn `EnableDepth` auf `true` festgelegt ist. Andernfalls hat diese Einstellung keine Auswirkungen.

Tiefenpuffer zeichnen normalerweise z-Werte in einem Gleitkommabereich von [0;1] auf, wobei 0 die Tiefe der nahen Ebene und 1 die Tiefe der fernen Ebene bezeichnet. Es ist auch möglich, diesen Bereich umzukehren und Tiefenwerte im Bereich [1;0] aufzuzeichnen, sodass 1 die Tiefe der nahen Ebene und 0 die Tiefe der fernen Ebene wird. Im Allgemeinen wird durch letzteren Ansatz die Verteilung der Gleitkommagenauigkeit im nicht linearen Z-Bereich erhöht.

> [!WARNING]
> Ein gängiger Ansatz besteht darin, die Werte der nahen und fernen Ebenen für die Kameraobjekte umzukehren. Wenn Sie dies für `CameraSettings` versuchen, tritt in Azure Remote Rendering ein Fehler auf.

Die Azure Remote Rendering-API muss die Tiefenpufferkonvention Ihres lokalen Renderers kennen, um die Remotetiefe im lokalen Tiefenpuffer ordnungsgemäß zusammenstellen zu können. Wenn Ihr Tiefenpufferbereich [0;1] ist, übernehmen Sie `false` für dieses Flag. Wenn Sie einen umgekehrten Tiefenpuffer mit dem Bereich [1;0] verwenden, legen Sie das `InverseDepth`-Flag auf `true` fest.

> [!NOTE]
> Für Unity wird die richtige Einstellung bereits vom `RenderingConnection` angewendet, sodass kein manueller Eingriff erforderlich ist.

Das Ändern der Kameraeinstellungen kann wie folgt erfolgen:

```cs
void ChangeCameraSetting(RenderingSession session)
{
    CameraSettings settings = session.Connection.CameraSettings;

    settings.SetNearAndFarPlane(0.1f, 20.0f);
    settings.EnableDepth = false;
    settings.InverseDepth = false;
}
```

```cpp
void ChangeCameraSetting(ApiHandle<RenderingSession> session)
{
    ApiHandle<CameraSettings> settings = session->Connection()->GetCameraSettings();

    settings->SetNearAndFarPlane(0.1f, 20.0f);
    settings->SetEnableDepth(false);
    settings->SetInverseDepth(false);
}
```

## <a name="api-documentation"></a>API-Dokumentation

* [C# CameraSettings](/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [C++ CameraSettings](/cpp/api/remote-rendering/camerasettings)
* [C# GraphicsBindingSimD3d11.Update-Funktion](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [C++ GraphicsBindingSimD3d11::Update-Funktion](/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>Nächste Schritte

* [Grafikbindung](../../concepts/graphics-bindings.md)
* [Late Stage Reprojection (LSR)](late-stage-reprojection.md)
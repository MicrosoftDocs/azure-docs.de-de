---
title: Einseitiges Rendering
description: Beschreibt Einstellungen für einseitiges Rendering sowie Anwendungsfälle.
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: fea9deae3948b36732b5ea5203fceea6bec07fb9
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594077"
---
# <a name="no-loc-textsingle-sided-rendering"></a>:::no-loc text="Single-sided"::: Rendering

Die meisten Renderer verwenden [Backface Culling](https://en.wikipedia.org/wiki/Back-face_culling), um die Leistung zu verbessern. Wenn die Gittermodelle jedoch mit [Schnittebenen](cut-planes.md) aufgeschnitten werden, sehen Benutzer häufig die Rückseite von Dreiecken. Wenn diese Dreiecke mit Culling entfernt werden, sieht das Ergebnis nicht überzeugend aus.

Sie können dieses Problem zuverlässig vermeiden, indem Sie Dreiecke *doppelseitig* rendern. Da die Nichtverwendung von Backface Culling Auswirkungen auf die Leistung besitzt, schaltet Azure Remote Rendering standardmäßig nur zum doppelseitigen Rendering bei Gittermodelln um, die sich mit einer Schnittebene überschneiden.

Mit der Einstellung für *:::no-loc text="single-sided"::: Rendering* können Sie dieses Verhalten anpassen.

> [!CAUTION]
> Die Einstellung für :::no-loc text="single-sided"::: Rendering ist ein experimentelles Feature. Möglicherweise wird es in Zukunft wieder entfernt. Ändern Sie die Standardeinstellung nicht, es sei denn, dies löst tatsächlich ein wichtiges Problem in Ihrer Anwendung.

## <a name="prerequisites"></a>Voraussetzungen

Die Einstellung für :::no-loc text="single-sided"::: Rendering wirkt sich nur auf Gittermodelle aus, die mit der auf `SingleSided` festgelegten Option `opaqueMaterialDefaultSidedness` [konvertiert wurden](../../how-tos/conversion/configure-model-conversion.md). Standardmäßig ist diese Option auf `DoubleSided` festgelegt.

## <a name="no-loc-textsingle-sided-rendering-setting"></a>Renderingeinstellung :::no-loc text="Single-sided":::

Es gibt drei verschiedene Modi:

**Normal:** In diesem Modus werden die Gittermodelle beim Konvertieren immer gerendert. Das bedeutet, dass Gittermodelle, die mit `opaqueMaterialDefaultSidedness` auf `SingleSided` festgelegt konvertiert wurden, immer mit aktiviertem Backface Culling gerendert werden, auch wenn sie sich mit einer Schnittebene überschneiden.

**DynamicDoubleSiding:** In diesem Modus wird automatisch in doppelseitiges Rendering umgeschaltet, wenn sich eine Schnittebene mit einem Gittermodell überschneidet. Dieser Modus ist der Standardmodus.

**AlwaysDoubleSided:** Erzwingt, dass die gesamte einseitige Geometrie immer doppelseitig gerendert wird. Dieser Modus wird meistens verwendet. Sie können daher die Leistungseinbußen zwischen :::no-loc text="single-sided"::: und :::no-loc text="double-sided"::: Rendering problemlos vergleichen.

Das Ändern der Einstellungen für :::no-loc text="single-sided"::: Rendering kann wie folgt erfolgen:

```cs
void ChangeSingleSidedRendering(RenderingSession session)
{
    SingleSidedSettings settings = session.Connection.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

```cpp
void ChangeSingleSidedRendering(ApiHandle<RenderingSession> session)
{
    ApiHandle<SingleSidedSettings> settings = session->Connection()->GetSingleSidedSettings();

    // Single-sided geometry is rendered as is
    settings->SetMode(SingleSidedMode::Normal);

    // Single-sided geometry is always rendered double-sided
    settings->SetMode(SingleSidedMode::AlwaysDoubleSided);
}
```

## <a name="api-documentation"></a>API-Dokumentation

* [C# RenderingConnection.SingleSidedSettings (Eigenschaft)](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.singlesidedsettings)
* [C++ RenderingConnection::SingleSidedSettings()](/cpp/api/remote-rendering/renderingconnection#singlesidedsettings)

## <a name="next-steps"></a>Nächste Schritte

* [Schnittebenen](cut-planes.md)
* [Konfigurieren der Modellkonvertierung](../../how-tos/conversion/configure-model-conversion.md)
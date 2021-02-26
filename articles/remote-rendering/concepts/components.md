---
title: Komponenten
description: Definition von Komponenten im Bereich von Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 9679be03c69090a0c11d007cfc542bae70bd3cbc
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592193"
---
# <a name="components"></a>Komponenten

Azure Remote Rendering verwendet das Muster [Entitätskomponentensystem](https://en.wikipedia.org/wiki/Entity_component_system). Während [Entitäten](entities.md) die Position und die hierarchische Komposition von Objekten darstellen, sind Komponenten für das Implementieren von Verhalten verantwortlich.

Die am häufigsten verwendeten Komponententypen sind [:::no-loc text="mesh components":::](meshes.md), die der Renderingpipeline Gittermodelle hinzufügen. Analog dazu werden [Lichtkomponenten](../overview/features/lights.md) verwendet, um Beleuchtung hinzuzufügen, und [Schnittebenenkomponenten](../overview/features/cut-planes.md) werden genutzt, um Gittermodelle aufzuschneiden.

Alle diese Komponenten verwenden die Transformation (Position, Drehung, Maßstab) der Entität, an die sie angefügt sind, als ihren Bezugspunkt.

## <a name="working-with-components"></a>Arbeiten mit Komponenten

Sie können Komponenten problemlos programmgesteuert hinzufügen, entfernen und bearbeiten:

```cs
// create a point light component
RenderingSession session = GetCurrentlyConnectedSession();
PointLightComponent lightComponent = session.Connection.CreateComponent(ObjectType.PointLightComponent, ownerEntity) as PointLightComponent;

lightComponent.Color = new Color4Ub(255, 150, 20, 255);
lightComponent.Intensity = 11;

// ...

// destroy the component
lightComponent.Destroy();
lightComponent = null;
```

```cpp
// create a point light component
ApiHandle<RenderingSession> session = GetCurrentlyConnectedSession();

ApiHandle<PointLightComponent> lightComponent = session->Connection()->CreateComponent(ObjectType::PointLightComponent, ownerEntity)->as<PointLightComponent>();

// ...

// destroy the component
lightComponent->Destroy();
lightComponent = nullptr;
```

Eine Komponente wird zum Erstellungszeitpunkt an eine Entität angefügt. Anschließend kann sie nicht mehr in eine andere Entität verschoben werden. Komponenten werden explizit mit `Component.Destroy()` oder automatisch gelöscht, wenn die Besitzerentität der Komponente zerstört wird.

Der Entität kann jeweils nur eine Instanz eines einzelnen Komponentyps hinzugefügt werden.

## <a name="unity-specific"></a>Unity-spezifisch

Die Unity-Integration stellt zusätzliche Erweiterungsfunktionen für die Interaktion mit-Komponenten bereit. Weitere Informationen dazu finden Sie unter [Interagieren mit Spielobjekten und Komponenten](../how-tos/unity/objects-components.md).

## <a name="api-documentation"></a>API-Dokumentation

* [ComponentBase (C#)](/dotnet/api/microsoft.azure.remoterendering.componentbase)
* [C# RenderingConnection.CreateComponent()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.createcomponent)
* [Entity.FindComponentOfType() (C#)](/dotnet/api/microsoft.azure.remoterendering.entity.findcomponentoftype)
* [ComponentBase (C++)](/cpp/api/remote-rendering/componentbase)
* [C++ RenderingConnection::CreateComponent()](/cpp/api/remote-rendering/renderingconnection#createcomponent)
* [Entity::FindComponentOfType() (C++)](/cpp/api/remote-rendering/entity#findcomponentoftype)

## <a name="next-steps"></a>Nächste Schritte

* [Objektbegrenzungen](object-bounds.md)
* [Gittermodelle](meshes.md)
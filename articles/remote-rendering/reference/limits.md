---
title: Einschränkungen
description: Codeeinschränkungen für SDK-Features
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: f62a07ef7109c669c6abc90d4e1b4a2ee83ece23
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530245"
---
# <a name="limitations"></a>Einschränkungen

Eine Reihe von Features besitzen Einschränkungen hinsichtlich Größe, Anzahl oder anderer Aspekte.

## <a name="azure-frontend"></a>Azure-Front-End

Für die Front-End-API (C++ und C#) gelten die folgenden Einschränkungen:
* Gesamte [RemoteRenderingClient](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient)-Instanzen pro Prozess: 16.
* Gesamte [RenderingSession](/dotnet/api/microsoft.azure.remoterendering.renderingsession)-Instanzen pro [RemoteRenderingClient](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient): 16.

## <a name="objects"></a>erzwingen

* Zulässige Gesamtzahl von Objekten eines einzelnen Typs ([Entity](../concepts/entities.md), [CutPlaneComponent](../overview/features/cut-planes.md) usw.): 16.777.215.
* Zulässige Gesamtanzahl aktiver Schnittebenen: 8.

## <a name="geometry"></a>Geometrie

* **Animation:** Animationen sind auf die Animation einzelner Transformationen von [Spielobjekten](../concepts/entities.md) beschränkt. Skelettanimationen („Rigging“) mit Skinning- oder Vertexanimationen werden nicht unterstützt. Animationsspuren aus der Quellmedienobjektdatei werden nicht beibehalten. Stattdessen müssen Transformationsanimationen von Objekten durch Clientcode gesteuert werden.
* **Benutzerdefinierte Shader:** Das Erstellen benutzerdefinierter Shader wird nicht unterstützt. Es können nur [Farbmaterialien](../overview/features/color-materials.md) oder [PBR-Materialien](../overview/features/pbr-materials.md) verwendet werden.
* **Maximale Anzahl unterschiedlicher Materialien** in einem Medienobjekt: 65.535. Weitere Informationen zur Verringerung der automatischen Materialanzahl finden Sie im Kapitel [Deduplizierung von Materialien](../how-tos/conversion/configure-model-conversion.md#material-de-duplication).
* **Maximale Dimension einer einzelnen Textur:** 16.384 x 16.384. Größere Quelltexturen werden durch den Konvertierungsprozess verkleinert.

### <a name="overall-number-of-polygons"></a>Gesamtanzahl von Polygonen

Die zulässige Anzahl von Polygonen für alle geladenen Modelle ist von der Größe der VM abhängig, die an die [Sitzungsverwaltungs-REST-API](../how-tos/session-rest-api.md) übergeben wurde:

| Servergröße | Maximale Anzahl von Polygonen |
|:--------|:------------------|
|Standard| 20 Mio. |
|premium| Keine Begrenzung |

Ausführliche Informationen zu dieser Einschränkung finden Sie im Kapitel [Servergröße](../reference/vm-sizes.md).

## <a name="platform-limitations"></a>Plattformeinschränkungen

**Windows 10 Desktop**

* Win32/x64 ist die einzige unterstützte Win32-Plattform. Win32/x86 wird nicht unterstützt.

**HoloLens 2**

* Die Funktion [Von PV-Kamera rendern](/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) wird nicht unterstützt.
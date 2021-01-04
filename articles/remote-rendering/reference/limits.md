---
title: Einschränkungen
description: Codeeinschränkungen für SDK-Features
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 33f5314c80dc33dbec50dc21a71f4cb507979e12
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427427"
---
# <a name="limitations"></a>Einschränkungen

Eine Reihe von Features besitzen Einschränkungen hinsichtlich Größe, Anzahl oder anderer Aspekte.

## <a name="azure-frontend"></a>Azure-Front-End

Für die Front-End-API (C++ und C#) gelten die folgenden Einschränkungen:
* Gesamtzahl der [AzureFrontend](/dotnet/api/microsoft.azure.remoterendering.azurefrontend)-Instanzen pro Prozess: 16.
* Gesamtzahl der [AzureSession](/dotnet/api/microsoft.azure.remoterendering.azuresession)-Instanzen pro [AzureFrontend](/dotnet/api/microsoft.azure.remoterendering.azurefrontend): 16.

## <a name="objects"></a>erzwingen

* Zulässige Gesamtzahl von Objekten eines einzelnen Typs ([Entity](../concepts/entities.md), [CutPlaneComponent](../overview/features/cut-planes.md) usw.): 16.777.215.
* Zulässige Gesamtanzahl aktiver Schnittebenen: 8.

## <a name="geometry"></a>Geometrie

* **Animation:** Animationen sind auf die Animation einzelner Transformationen von [Spielobjekten](../concepts/entities.md) beschränkt. Skelettanimationen („Rigging“) mit Skinning- oder Vertexanimationen werden nicht unterstützt. Animationsspuren aus der Quellmedienobjektdatei werden nicht beibehalten. Stattdessen müssen Transformationsanimationen von Objekten durch Clientcode gesteuert werden.
* **Benutzerdefinierte Shader:** Das Erstellen benutzerdefinierter Shader wird nicht unterstützt. Es können nur [Farbmaterialien](../overview/features/color-materials.md) oder [PBR-Materialien](../overview/features/pbr-materials.md) verwendet werden.
* **Maximale Anzahl unterschiedlicher Materialien** in einem Medienobjekt: 65.535. Weitere Informationen zur Verringerung der automatischen Materialanzahl finden Sie im Kapitel [Deduplizierung von Materialien](../how-tos/conversion/configure-model-conversion.md#material-de-duplication).
* **Maximale Dimension einer einzelnen Textur:** 16.384 x 16.384. Größere Quelltexturen werden durch den Konvertierungsprozess verkleinert.

### <a name="overall-number-of-polygons"></a>Gesamtanzahl von Polygonen

Die zulässige Anzahl von Polygonen für alle geladenen Modelle ist von der Größe der VM abhängig, die an die [Sitzungsverwaltungs-REST-API](../how-tos/session-rest-api.md#create-a-session) übergeben wurde:

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
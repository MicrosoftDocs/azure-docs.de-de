---
title: Stagebereich
description: Beschreibt Einstellungen und Anwendungsfälle für den Stagebereich.
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a739907ce0a3b0b6dfcb8791b51d0ea5e7e76e7
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594009"
---
# <a name="stage-space"></a>Stagebereich

Wenn ARR auf einem Gerät ausgeführt wird, das Nachverfolgungsdaten zu Kopfbewegungen bereitstellt, wie etwa HoloLens 2, wird die Kopfhaltung an die Benutzeranwendung und den Server gesendet. Der Bereich, in dem die Kopftransformation definiert ist, wird als *Stagebereich* bezeichnet.

Um lokale Inhalte und Remoteinhalte abzustimmen, wird davon ausgegangen, dass der Stagebereich und der Raum auf Client und Server identisch sind. Wenn der Benutzer beschließt, eine zusätzlich zur Kamera eine weitere Transformation hinzuzufügen, muss diese auch an den Server gesendet werden, um lokale Inhalte und Remoteinhalte korrekt aufeinander abzustimmen.

Typische Gründe für das Verschieben des Stagebereichs sind [Sperrtools für den Raum](https://microsoft.github.io/MixedReality-WorldLockingTools-Unity/README.html) oder andere Verfahren zum Verankern in der realen Welt sowie das Verschieben einer virtuellen Figur in VR.

## <a name="stage-space-settings"></a>Einstellungen für den Stagebereich

> [!CAUTION]
> EXPERIMENTELL: Diese Funktion ist experimentell, und Änderungen sind vorbehalten. Folglich ist nach einem Update auf neuere Client-SDK-Versionen möglicherweise zusätzlicher Aufwand erforderlich, um den Code zu aktualisieren. Bei der aktuellen Implementierung funktioniert die Abstimmung von lokalen Inhalten und Remoteinhalten für einen kurzen Moment nicht, wenn der Ursprung des Stagebereichs geändert wird.
Daher ist sie zurzeit nur zum Sperren des Raums vorgesehen, z. B. durch Anker, die sich im Laufe der Zeit nur wenig ändern.

Um dem Server mitzuteilen, dass eine zusätzliche Transformation auf den Stagebereich angewendet wird, muss der Ursprung übermittelt werden, der durch eine Position und eine Drehung im Raum definiert ist. Auf diese Einstellung kann über die *Stagebereichseinstellung* zugegriffen werden.

> [!IMPORTANT]
> In der [Desktopsimulation](../../concepts/graphics-bindings.md) wird die Position der Kamera im Raum von der Benutzeranwendung bereitgestellt. In diesem Fall muss das Festlegen des Ursprungs der Stage im Raum ausgelassen werden, da er bereits mit der Kameratransformation multipliziert ist.

```cs
void ChangeStageSpace(RenderingSession session)
{
    StageSpaceSettings settings = session.Connection.StageSpaceSettings;

    // Set position and rotation to the world-space transform of the stage space.
    settings.Position = new Double3(0, 0, 0);
    settings.Rotation = new Quaternion(0, 0, 0, 1);
}
```

```cpp
void ChangeStageSpace(ApiHandle<RenderingSession> session)
{
    ApiHandle<StageSpaceSettings> settings = session->Connection()->GetStageSpaceSettings();

    // Set position and rotation to the world-space transform of the stage space.
    settings->SetPosition({0, 0, 0});
    settings->SetRotation({0, 0, 0, 1});
}
```

## <a name="unity-stage-space-script"></a>Unity-Skript für den Stagebereich

Die Unity-Integration bietet ein Skript mit dem Namen **StageSpace**, das dem übergeordneten GameObject der Kamera hinzugefügt werden kann. Sobald dieses Skript vorhanden ist, wird es verwendet, um den Ursprung des Stageraums festzulegen.

## <a name="next-steps"></a>Nächste Schritte

* [Grafikbindung](../../concepts/graphics-bindings.md)
* [Late Stage Reprojection (LSR)](late-stage-reprojection.md)

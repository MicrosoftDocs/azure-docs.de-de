---
title: LSR (Late State Reprojection)
description: Informationen zu LSR (Late Stage Projection) und ihrer Verwendung.
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: f0951415bba22a226dadb7f2a115cede451399bc
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205641"
---
# <a name="late-stage-reprojection"></a>LSR (Late State Reprojection)

*Late Stage Projection* (LSR) ist ein Hardwarefeature, mit dem Hologramme bei Bewegungen des Benutzers stabilisiert werden.

Bei statischen Modellen wird erwartet, dass sie ihre Position visuell beibehalten, wenn sich Benutzer darin bewegen. Wenn Sie instabil scheinen, kann dieses Verhalten auf LSR-Probleme hindeuten. Beachten Sie, dass zusätzliche dynamische Transformationen (etwa Animationen oder Explosionsansichten) dieses Verhalten maskieren können.

Sie können zwischen zwei verschiedenen LSR-Modi wählen, nämlich zwischen **Planar-LSR** und **Tiefen-LSR** . Welcher Modus aktiv ist, hängt davon ab, ob die Clientanwendung einen Tiefenpuffer übermittelt.

Beide LSR-Modi verbessern die Hologrammstabilität, auch wenn sie jeweils Einschränkungen aufweisen. Beginnen Sie mit dem Testen von Tiefen-LSR, da damit in den meisten Fällen bessere Ergebnisse erzielt werden.

## <a name="choose-lsr-mode-in-unity"></a>Auswählen des LSR-Modus in Unity

Wechseln Sie im Unity-Editor zu *:::no-loc text="File > Build Settings":::* . Wählen Sie links unten *:::no-loc text="Player Settings":::* aus, und überprüfen Sie dann unter *:::no-loc text="Player > XR Settings > Virtual Reality SDKs > Windows Mixed Reality":::* , ob **:::no-loc text="Enable Depth Buffer Sharing":::** aktiviert ist:

![Flag für aktivierte Tiefenpufferfreigabe](./media/unity-depth-buffer-sharing-enabled.png)

Wenn dies der Fall ist, verwendet Ihre App Tiefen-LSR, andernfalls wird Planar-LSR verwendet.

## <a name="depth-lsr"></a>Tiefen-LSR

Damit Tiefen-LSR funktioniert, muss die Clientanwendung einen gültigen Tiefenpuffer bereitstellen, der die gesamte relevante Geometrie enthält, die während des LSR-Vorgangs berücksichtigt werden muss.

Tiefen-LSR versucht, den Videoframe basierend auf dem Inhalt des angegebenen Tiefenpuffers zu stabilisieren. Folglich können Inhalte, die nicht in ihm gerendert wurden (z. B. transparente Objekte), nicht von LSR angepasst werden, und es kann zu Instabilität und Umprojizierungsartefakten kommen. 

Um Instabilitäten der Neuprojektion bei transparenten Objekten zu vermeiden, können Sie das Schreiben von Tiefenpuffern erzwingen. Weitere Informationen zu den [Farb](color-materials.md)- und [PBR](pbr-materials.md)-Materialien finden Sie im Materialflag *TransparencyWritesDepth* . Beachten Sie jedoch, dass die visuelle Qualität der Interaktion zwischen transparenten/nicht transparenten Objekten beim Aktivieren dieses Flags beeinträchtigt werden kann.

## <a name="planar-lsr"></a>Planar-LSR

Planar-LSR nutzt keine Tiefeninformationen pro Pixel wie Tiefen-LSR. Stattdessen wird der gesamte Inhalt auf Grundlage einer Ebene umprojiziert, die Sie für jeden Frame bereitstellen müssen.

Planar-LSR projiziert diese Objekte am besten um, die in der Nähe der angegebenen Ebene liegen. Je weiter entfernt ein Objekt ist, desto instabiler sieht es aus. Obwohl Tiefen-LSR bei der Umprojizierung von Objekten in unterschiedlichen Tiefen besser ist, kann Planar-LSR besser für Inhalte geeignet sein, die sich gut an einer Ebene ausrichten lassen.

### <a name="configure-planar-lsr-in-unity"></a>Konfigurieren von Planar-LSR in Unity

Die Ebenenparameter werden von einem so genannten *Fokuspunkt* abgeleitet, den Sie für jeden Frame über `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame` bereitstellen müssen. Ausführliche Informationen dazu finden Sie in der [Fokuspunkt-API von Unity](/windows/mixed-reality/focus-point-in-unity). Wenn Sie keinen Fokuspunkt festlegen, wird ein Fallback für Sie ausgewählt. Dieser automatische Fallback führt jedoch häufig zu suboptimalen Ergebnissen.

Sie können den Fokuspunkt selbst berechnen, aber es kann sinnvoll sein, ihn auf dem vom Remote Rendering-Host berechneten Wert basieren zu lassen. Rufen Sie `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` auf, um diesen Wert abzurufen. Sie werden aufgefordert, einen Koordinatenrahmen anzugeben, in dem der Fokuspunkt ausgedrückt werden soll. In den meisten Fällen möchten Sie hier nur das Ergebnis aus `UnityEngine.XR.WSA.WorldManager.GetNativeISpatialCoordinateSystemPtr` bereitstellen.

Normalerweise rendern sowohl der Client als auch der Host Inhalte, die der jeweils anderen Seite unbekannt sind, z. B. Benutzeroberflächenelemente auf dem Client. Daher ist es möglicherweise sinnvoll, den Remotefokuspunkt mit einem lokal berechneten zu kombinieren.

Die Fokuspunkte, die in zwei aufeinander folgenden Frames berechnet werden, können sich erheblich unterscheiden. Wenn Sie diese einfach unverändert verwenden, kann dies dazu führen, dass Hologramme zu „springen“ scheinen. Um dieses Verhalten zu verhindern, ist eine Interpolation zwischen den vorherigen und den aktuellen Fokuspunkten empfehlenswert.

## <a name="next-steps"></a>Nächste Schritte

* [Serverseitige Leistungsabfragen](performance-queries.md)
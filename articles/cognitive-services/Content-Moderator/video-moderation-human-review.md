---
title: Videomoderation mit dem Überprüfungstool – Content Moderator
titleSuffix: Azure Cognitive Services
description: Verwenden Sie die computergestützte Videomoderation und Überprüfungstools, um unangemessene Inhalte zu moderieren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: pafarley
ms.openlocfilehash: 05ece4d8ea24fb9da106ec3daa2cc1a63706dab9
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96853497"
---
# <a name="video-moderation-with-the-review-tool"></a>Videomoderation mit dem Überprüfungstool

Nutzen Sie die computergestützte [Videomoderation](video-moderation-api.md) von Content Moderator und [Überprüfungstools](Review-Tool-User-Guide/human-in-the-loop.md) zum Moderieren von Videos und Transkripts auf anstößige und zweideutige Inhalte, um optimale Ergebnisse für Ihr Unternehmen zu erhalten.

## <a name="view-videos-under-review"></a>Anzeigen von zu prüfenden Videos

Wählen Sie im Dashboard eine der Überprüfungswarteschlangen innerhalb des Videoinhaltstyps aus. Dadurch wird eine Überprüfung gestartet und die Seite für die Moderation von Videoinhalten geöffnet.

> [!div class="mx-imgBorder"]
> ![Auf Content Moderator (Vorschau): Schieberegler hervorgehoben und auf 4 Überprüfungen festgelegt. Zudem: Schieberegler „Blur All“ (Alles weichzeichnen) und „Black and white“ (Schwarzweiß) sind hervorgehoben und festgelegt.](./Review-Tool-User-Guide/images/video-moderation-detailed.png)

### <a name="review-count"></a>Review count

Verwenden Sie den Schieberegler oben rechts, um die Anzahl der Überprüfungen einzustellen, die Sie auf der Seite anzeigen möchten.

### <a name="view-type"></a>Ansichtstyp

Sie können die verschiedenen Inhaltseinträge als Kacheln oder in einer Detailansicht betrachten. Die **Detailansicht** ermöglicht es Ihnen, Keyframes und andere Informationen zum ausgewählten Video anzuzeigen. 

> [!NOTE]
> Anstatt in regelmäßigen Abständen Frames auszugeben, identifiziert der Video Moderation Service nur potenziell vollständige (gute) Frames und gibt nur diese aus. Dieses Feature ermöglicht eine effiziente Frame-Generierung für Analyse von Inhalten für Erwachsene und von freizügigen Inhalten auf Frameebene.

In der **Kachelansicht** wird jedes Video als eine einzelne Kachel angezeigt. Wählen Sie die Schaltfläche zum Erweitern über einem Videoframe aus, um dieses Video zu vergrößern und die anderen auszublenden.

### <a name="content-obscuring-effects"></a>Inhaltsverzerrende Effekte

Verwenden Sie die Schalter für **Blur all** (Alles weichzeichnen) und **Black and white** (Schwarzweiß), um diese inhaltsverzerrenden Effekte festzulegen. Sie sind standardmäßig aktiviert. In der **Kachelansicht** können Sie die Effekte für jedes Video einzeln umschalten.

## <a name="check-video-details"></a>Überprüfen von Videodetails

In der **Detailansicht** werden im rechten Bereich mehrere Registerkarten angezeigt, die Einzelheiten zum Video enthalten.

* Wählen Sie die Registerkarte **Notes** (Hinweise) aus, um benutzerdefinierte Hinweise zu Videos hinzuzufügen.
* Wählen Sie die Registerkarte **Transcript** (Transkript) aus, um das Videotranskript anzuzeigen. Der Dienst extrahiert automatisch ein Transkript einer beliebigen Spracheingabe im Video. Wenn Sie einen Textabschnitt auswählen, springt der Videoplayer zu diesem Teil des Videos.
* Wählen Sie die Registerkarte **Meta-data** (Metadaten) aus, um die Metadaten der Videodatei anzuzeigen.
* Wählen Sie die Registerkarte **History** (Verlauf) aus, um den Verlauf der Überprüfung anzuzeigen, z. B. wann sie erstellt und wie sie geändert wurde.

> [!div class="mx-imgBorder"]
> ![Hervorgehobener rechter Bereich mit ausgewählter Registerkarte „Notes“ (Notizen); Zudem Testbereich „Add a note“ (Notiz hinzufügen)](./Review-Tool-User-Guide/images/video-moderation-video-details.png)

## <a name="apply-moderation-tags"></a>Anwenden von Moderationstags

Die Hauptaufgabe einer Videoüberprüfung besteht darin, Moderationstags auf Videos oder Teile von Videos anzuwenden oder diese zu entfernen.

### <a name="bulk-tagging"></a>Massenkennzeichnung

Mit der Symbolleiste für **Massentags** können Sie Tags zu mehreren ausgewählten Videos gleichzeitig hinzufügen. Wählen Sie ein oder mehrere Videos und dann die Tags aus, die Sie anwenden möchten, und klicken Sie auf **Submit** (Übermitteln). 

> [!div class="mx-imgBorder"]
> ![Hervorgehobene Schaltfläche „+“ im Bereich zu Massentags](./Review-Tool-User-Guide/images/video-moderation-bulk-tags.png)


### <a name="key-frame-tagging"></a>Kennzeichnung von Keyframes

Sie können auch Moderationstags zu bestimmten Keyframes hinzufügen. Wählen Sie die Frames aus dem Kachelbereich für Keyframes und dann **Keyframe tags +** (Keyframetags +) aus, um die gewünschten Tags anzuwenden.

> [!NOTE]
> Wenn der Dienst keine Keyframes extrahieren konnte, zeigt der Kachelbereich für Keyframes **No frames available** (Keine Frames verfügbar) an und die Option zur Auswahl von Keyframes ist abgeblendet. In diesem Fall können Sie Tags nur auf das Video als Ganzes anwenden (mithilfe der Schaltfläche **Video tags +** (Videotags +)).

> [!div class="mx-imgBorder"]
> ![Kachelbereiche, Videoplayer und Bereiche „Keyframetags“ und „Videotags“ werden angezeigt. Die Schaltflächen „Keyframetags“ und „Videotags“ sind hervorgehoben.](./Review-Tool-User-Guide/images/video-moderation-tagging-options.png)

## <a name="put-a-review-on-hold"></a>Anhalten einer Überprüfung

Mit der Schaltfläche **Hold** (Anhalten) am unteren Rand des Videobereichs können Sie eine Überprüfung anhalten, sodass Sie sie abrufen und später vervollständigen können. Sie können dies für eine Überprüfung durchführen, die eine Beratung durch ein anderes Teammitglied oder einen Vorgesetzten erfordert, das/der derzeit nicht verfügbar ist. 

Sie können die angehaltenen Videos anzeigen, indem Sie auf die Schaltfläche **Hold** (Anhalten) oben auf dem Bildschirm klicken. Auf der rechten Seite wird der Bereich „Hold“ (Anhalten) angezeigt. Von hier aus können Sie mehrere zurückgestellte Überprüfungen auswählen und sie entweder wieder in die Warteschlange freigeben oder ihre Ablaufzeit festlegen. Nach der vorkonfigurierten Zeitspanne werden zurückgestellte Überprüfungen wieder in die Warteschlange freigegeben. Wählen Sie **Save** (Speichern) aus, um mit dem Herunterzählen der aktuell gewählten Ablaufzeit zu beginnen.

> [!div class="mx-imgBorder"]
> ![Im Videobereich wird die Schaltfläche „Hold“ (Anhalten) hervorgehoben. Am unteren Rand des Bereichs wird das Kombinationsfeld „Hold Time“ (Zeit anhalten) zusammen mit den Schaltflächen „Release“ (Freigeben) und „Save“ (Speichern) hervorgehoben.](./Review-Tool-User-Guide/images/video-moderation-hold.png)

## <a name="submit-a-review"></a>Übermitteln einer Überprüfung

Nachdem Sie Ihre Tags angewendet haben, wählen Sie die Schaltfläche **Submit** (Übermitteln) unten im Videobereich aus. Wenn Sie mehrere Videos mit Tags versehen haben, können Sie sie im Rahmen einer einzigen Überprüfung oder als separate Überprüfungen übermitteln.

## <a name="limbo-state"></a>Limbo-Status

Nachdem Sie eine Überprüfung übermittelt haben, wird das Video in den Zustand **Limbo** versetzt, den Sie durch Auswahl der Schaltfläche **Limbo** oben auf dem Bildschirm anzeigen können. Videos verbleiben für eine vorkonfigurierte Zeitspanne im Limbo-Zustand (die Sie im Menü unten ändern können), oder bis sie erneut überprüft oder manuell übermittelt werden.

Sobald die Videos im Limbo-Zustand ablaufen, werden ihre Überprüfungen als abgeschlossen gekennzeichnet.

## <a name="next-steps"></a>Nächste Schritte

- Beginnen Sie mit dem [Schnellstart für die Videomoderation](video-moderation-api.md).
- Erfahren Sie mehr über das Generieren von [Videoüberprüfungen](video-reviews-quickstart-dotnet.md) für Ihre menschlichen Prüfer aus Ihrer moderierten Ausgabe.
- Fügen Sie [Überprüfungen von Videotranskripten](video-transcript-reviews-quickstart-dotnet.md) zu Ihren Videoüberprüfungen hinzu.

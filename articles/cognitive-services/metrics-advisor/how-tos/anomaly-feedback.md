---
title: Bereitstellen von Feedback zu Anomalien an den Metrics Advisor-Dienst
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie Feedback zu Anomalien, die von Ihrer Metrics Advisor-Instanz gefunden wurden, senden und die Ergebnisse optimieren.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbullwin
ms.openlocfilehash: 96a6dfebdd0047d5a6f4ff3e295ab96dd8c7226a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184968"
---
# <a name="provide-anomaly-feedback"></a>Bereitstellen von Anomaliefeedback

Benutzerfeedback ist eine der wichtigsten Methoden, um Fehler innerhalb des Systems zur Anomalieerkennung zu ermitteln. In diesem Artikel wird erläutert, wie Benutzer falsche Erkennungsergebnisse direkt in einer Zeitreihe kennzeichnen und das Feedback sofort anwenden. Auf diese Weise können Benutzer das System zur Anomalieerkennung durch aktive Interaktionen darauf trainieren, die Anomalieerkennung für eine bestimmte Zeitreihe durchzuführen. 

> [!NOTE]
> Derzeit wirkt sich das Feedback nur auf die Ergebnisse der Anomalieerkennung aus, die durch die **intelligente Erkennung**, aber nicht durch einen **harten Schwellenwert** und einen **Änderungsschwellenwert** erzielt wurden.

## <a name="how-to-give-time-series-feedback"></a>Angeben von Zeitreihenfeedback

Sie können über die Seite mit den Metrikdetails Feedback zu jeder beliebigen Zeitreihe bereitstellen. Klicken Sie einfach auf einen beliebigen Punkt, und das folgende Feedbackdialogfeld wird angezeigt. Darin werden die Dimensionen der ausgewählten Reihe angezeigt. Sie können Dimensionswerte neu auswählen oder sogar entfernen, um einen Batch von Zeitreihendaten abzurufen. Nachdem Sie die Zeitreihe ausgewählt haben, klicken Sie auf **Hinzufügen**, um Feedback hinzuzufügen. Sie können vier Arten von Feedback geben. Wenn Sie mehrere Feedbackelemente anfügen möchten, klicken Sie auf **Speichern**, nachdem Sie Ihre Anmerkungen verfasst haben.

:::image type="content" source="../media/feedback/click-on-any-point.png" alt-text="Diagramm mit Zeitreihendaten und einer blauen Linie sowie roten Punkten an verschiedenen Stellen. Ein roter Kasten umgibt einen Punkt. Daneben ist folgender Text zu lesen: Click on any point (Klicken Sie auf einen beliebigen Punkt)":::

:::image type="content" source="../media/feedback/select-or-remove.png" alt-text="Dialogfeld „Add feedback“ (Feedback hinzufügen) mit zwei Dimensionen und der Option zum Auswählen oder Entfernen von Dimensionen und zum Hinzufügen von Feedback":::

### <a name="mark-the-anomaly-point-type"></a>Markieren des Typs des Anomaliepunkts

Wie im folgenden Screenshot gezeigt, wird der Zeitstempel des ausgewählten Punkts im Feedbackdialogfeld automatisch ausgewählt. Sie können diesen Wert jedoch bearbeiten und entscheiden, ob dieses Element den Typ `Anomaly`, `NotAnomaly` oder `AutoDetect` aufweisen soll.

:::image type="content" source="../media/feedback/anomaly-value.png" alt-text="Dropdownmenü mit den Optionen „Anomaly“, „NotAnomaly“ und „AutoDetect“":::

Durch die ausgewählte Option wird Ihr Feedback in Zukunft auf die Verarbeitung der Anomalieerkennung der gleichen Zeitreihe angewendet. Die verarbeiteten Punkte werden nicht neu berechnet. Dies bedeutet, dass wenn Sie einen Punkt des Typs „Anomaly“ als „NotAnomaly“ gekennzeichnet haben, ähnliche Anomalien in Zukunft unterdrückt werden. Ebenso werden ähnliche Punkte in Zukunft häufiger als `Anomaly` erkannt, wenn Sie einen `NotAnomaly`-Punkt als `Anomaly` markiert haben. Wenn `AutoDetect` ausgewählt wird, werden alle vorherigen Feedbacks zu diesem Zeitreihenpunkt in Zukunft ignoriert.

## <a name="provide-feedback-for-multiple-continuous-points"></a>Bereitstellen von Feedback zu mehreren aufeinanderfolgenden Punkten 

Wenn Sie Anomaliefeedback für mehrere aufeinanderfolgende Punkte gleichzeitig geben möchten, wählen Sie die Gruppe von Punkten aus, die Sie mit Anmerkungen versehen möchten. Der gewünschte Zeitbereich ist automatisch ausgewählt, wenn Sie Anomaliefeedback geben.

:::image type="content" source="../media/feedback/continuous-anomaly-feedback.png" alt-text="Menü für Anomaliefeedback mit ausgewählter Anomalie und einem bestimmten Zeitbereich":::

Wählen Sie beim Durchsuchen einer Zeitreihe einen einzelnen Punkt aus, um anzuzeigen, ob ein einzelner Punkt von Ihrem Anomaliefeedback betroffen ist. Wenn das Ergebnis der Anomalieerkennung durch Feedback geändert wurde, zeigt die QuickInfo **Affected by feedback: true** (Von Feedback betroffen: wahr) an. Wenn **Affected by feedback: false** (Von Feedback betroffen: falsch) angezeigt wird, bedeutet dies, dass das Anomaliefeedback für diesen Punkt berechnet wurde, das Ergebnis der Anomalieerkennung jedoch nicht geändert werden soll.

:::image type="content" source="../media/feedback/affected-by-feedback.png" alt-text="Die QuickInfo mit folgendem Text wird angezeigt: „Von Feedback betroffen: wahr“ (rot hervorgehoben)":::

In bestimmten Situationen ist es nicht empfehlenswert, Feedback zu geben:

- Die Anomalie wird durch einen Feiertag verursacht. Da die Maßnahme präziser ist, sollten Sie in diesem Fall ein vordefiniertes Ereignis verwenden, um diese Art von Fehlalarm zu beheben.
- Die Anomalie wird durch eine bekannte Änderung an der Datenquelle verursacht, z. B. durch eine Änderung am Upstreamsystem zu diesem Zeitpunkt. In diesem Fall wird eine Anomaliewarnung erwartet, da das System nicht weiß, was die Wertänderung verursacht hat und wann ähnliche Wertänderungen wieder auftreten werden. Daher empfiehlt es sich nicht, diese Art von Problem als `NotAnomaly` zu markieren.

## <a name="change-points"></a>Änderungspunkte

Unter Umständen wirkt sich eine Trendänderung in Daten auf die Ergebnisse der Anomalieerkennung aus. Wenn die Entscheidung getroffen wird, ob es sich bei einem Punkt um eine Anomalie handelt oder nicht, wird das letzte Zeitfenster der historischen Daten berücksichtigt. Wenn Ihre Zeitreihe eine Trendänderung aufweist, können Sie den genauen Zeitpunkt der Änderung, den Änderungspunkt, kennzeichnen. Dies hilft der Anomalieerkennung bei zukünftigen Analysen.

Wie die folgende Abbildung zeigt, können Sie `ChangePoint` als Feedbacktyp und `ChangePoint`, `NotChangePoint` oder `AutoDetect` aus der Dropdownliste auswählen.

:::image type="content" source="../media/feedback/changepoint.png" alt-text="Menü für Änderungspunkte mit einer Dropdownliste mit den Optionen für „ChangePoint“, „NotChangePoint“ und „AutoDetect“":::

> [!NOTE]
> Wenn sich Ihre Daten weiterhin ändern, müssen Sie nur einen Punkt als Änderungspunkt (`ChangePoint`) markieren. Wenn Sie also einen Zeitbereich (`timerange`) markiert haben, werden der Zeitstempel und die Uhrzeit des letzten Punkts automatisch ausgefüllt. In diesem Fall wirkt sich Ihre Annotation erst nach 12 Punkten auf die Ergebnisse der Anomalieerkennung aus.

## <a name="seasonality"></a>Saisonalität

Wird die Anomalieerkennung für saisonale Daten durchgeführt, muss u. a. der Zeitraum (die Saisonalität) der Zeitreihen eingeschätzt und auf die Phase der Anomalieerkennung angewendet werden. In manchen Fällen ist es schwierig, einen exakten Zeitraum zu ermitteln. Außerdem kann sich der Zeitraum auch ändern. Ein falsch definierter Zeitraum kann Nebenwirkungen auf die Ergebnisse der Anomalieerkennung haben. Den aktuellen Zeitraum finden Sie in der QuickInfo unter `Min Period`.

:::image type="content" source="../media/feedback/min-period.png" alt-text="QuickInfo-Überlagerung, in der das Wort „Period“ (Zeitraum) und die Zahl 7 rot hervorgehoben sind":::

Sie können Feedback zu einem Zeitraum geben, um diese Art von Fehler der Anomalieerkennung zu beheben. Wie in der Abbildung gezeigt, können Sie einen Wert für den Zeitraum festlegen. Der Einheit `interval` steht für eine Granularitätsebene. Hier bedeuten null Intervalle, dass die Daten nicht saisonal sind. Sie können auch `AutoDetect` auswählen, wenn das vorherige Feedback entfernt und der Zeitraum automatisch durch die Pipeline ermittelt werden soll. 
 
> [!NOTE]
> Sie müssen beim Festlegen des Zeitraums keinen Zeitstempel oder Zeitbereich zuweisen. Der Zeitraum wirkt sich ab dem Zeitpunkt, an dem Sie Feedback geben, für ganze Zeiträume auf zukünftige Anomalieerkennungen aus.


:::image type="content" source="../media/feedback/period-feedback.png" alt-text="Menü mit automatisch erkanntem Zeitraum, der auf 28 festgelegt ist, und einem Intervall, das mit 0 (Null) angibt, dass die Daten nicht saisonal sind":::

## <a name="provide-comment-feedback"></a>Angeben von Kommentarfeedback

Sie können auch Kommentare hinzufügen, um Anmerkungen einzufügen und Kontext für Ihre Daten bereitzustellen. Wählen Sie zum Hinzufügen von Kommentaren einen Zeitbereich aus, und fügen Sie Ihren Kommentartext hinzu.

:::image type="content" source="../media/feedback/feedback-comment.png" alt-text="Menü zum Festlegen eines Zeitbereichs und einem Feld zum Hinzufügen eines textbasierten Kommentars":::

## <a name="time-series-batch-feedback"></a>Batchfeedback zu Zeitreihen

Wie bereits beschrieben, können Sie im modalen Feedbackdialog Dimensionswerte neu auswählen oder entfernen, um einen Zeitreihenbatch abzurufen, der von einem Dimensionsfilter definiert wird. Sie können dieses modale Dialogfeld durch einen Klick auf „+“ im linken Bereich öffnen und Dimensionen sowie Dimensionswerte auswählen.

:::image type="content" source="../media/feedback/feedback-time-series.png" alt-text="Menü mit einem blauen Pluszeichen, das neben dem Wort „Feedbacks“ rot hervorgehoben ist":::

:::image type="content" source="../media/feedback/feedback-dimension.png" alt-text="Menü „Feedback hinzufügen“ mit zwei Dimensionen, die durch Dim1 und Dim2 angegeben werden":::

## <a name="how-to-view-feedback-history"></a>Anzeigen des Feedbackverlaufs

Es gibt zwei Möglichkeiten, den Feedbackverlauf anzuzeigen. Sie können im linken Bereich auf die Schaltfläche für den Feedbackverlauf klicken. Daraufhin wird ein modales Dialogfeld mit einer Feedbackliste angezeigt. Es enthält Ihr gesamtes Feedback, das Sie vor dem Einzelreihen- oder Dimensionsfilter angegeben haben.

:::image type="content" source="../media/feedback/feedback-history-options.png" alt-text="Menü für die Feedbackliste":::

Der Feedbackverlauf kann alternativ auch über eine Zeitreihe angezeigt werden. Rechts über jeder Zeitreihe werden mehrere Schaltflächen angezeigt. Klicken Sie auf die Schaltfläche zum Anzeigen von Feedback. Die Linie zeigt dann keine Anomaliepunkte mehr, sondern Feedbackeinträgen an. Das grüne Flag steht für einen Änderungspunkt, und die blauen Punkte sind andere Feedbackpunkte. Sie können sie auch auswählen und ein modales Dialogfeld mit einer Feedbackliste aufrufen, in dem die Details zu diesem Punkt aufgeführt werden.

:::image type="content" source="../media/feedback/feedback-history-graph.png" alt-text="Diagramm zum Feedbackverlauf":::

:::image type="content" source="../media/feedback/feedback-list.png" alt-text="Menü mit Feedbackliste mit zwei Dimensionen":::

> [!NOTE]
> Jeder Benutzer, der Zugriff auf die Metrik hat, kann Feedback geben, sodass Ihnen möglicherweise Feedback von anderen Datenfeedbesitzern angezeigt wird. Wenn Sie den gleichen Punkt wie eine andere Person bearbeiten, überschreibt Ihr Feedback den vorherigen Feedbackeintrag.       

## <a name="next-steps"></a>Nächste Schritte
- [Diagnostizieren eines Incidents](diagnose-incident.md)
- [Konfigurieren von Metriken und Optimieren der Konfiguration](configure-metrics.md)
- [Konfigurieren von Warnungen und Erhalten von Benachrichtigungen per Hook](../how-tos/alerts.md)
---
title: Konfigurieren von Richtlinien für die Ereignisreihenfolge in Azure Stream Analytics
description: In diesem Artikel erfahren Sie, wie Sie die Einstellungen für die Ereignisreihenfolge in Stream Analytics konfigurieren.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: 80567a211f08d6322c80b6645f8b70ec7df64b59
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130662"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Konfigurieren von Richtlinien für die Ereignisreihenfolge in Azure Stream Analytics

Dieser Artikel beschreibt die Erstellung und Verwendung von Richtlinien für die Eingangsverzögerung und Ereignisse mit falscher Reihenfolge in Azure Stream Analytics. Diese Richtlinien werden nur angewendet, wenn Sie die Klausel [TIMESTAMP BY](/stream-analytics-query/timestamp-by-azure-stream-analytics) in Ihrer Abfrage verwenden, und sie werden nur auf Cloudeingabequellen angewendet.

## <a name="event-time-and-arrival-time"></a>Ereigniszeit und Eingangszeit

Ihr Stream Analytics-Auftrag kann Ereignisse basierend auf der *Ereigniszeit* oder der *Eingangszeit* verarbeiten. Die **Ereignis-/Anwendungszeit** ist der Zeitstempel in der Ereignisnutzlast (als das Ereignis generiert wurde). Die **Eingangszeit** ist der Zeitstempel, zu dem das Ereignis an der Eingabequelle (Event Hubs/IoT Hub/Blob Storage) eingegangen ist. 

Stream Analytics verarbeitet Ereignisse standardmäßig nach der *Eingangszeit*. Sie können Ereignisse jedoch auch nach der *Ereigniszeit* verarbeiten, indem Sie die Klausel [TIMESTAMP BY](/stream-analytics-query/timestamp-by-azure-stream-analytics) in Ihrer Abfrage verwenden. Richtlinien für die Eingangsverzögerung und die falsche Reihenfolge werden nur angewendet, wenn Sie Ereignisse nach der Ereigniszeit verarbeiten. Berücksichtigen Sie beim Konfigurieren dieser Einstellungen die Anforderungen an die Latenz und die Richtigkeit für Ihr Szenario. 

## <a name="what-is-late-arrival-policy"></a>Was ist eine Richtlinie für die Eingangsverzögerung?

Manchmal verspäten sich Ereignisse aus verschiedenen Gründen. Bei einem Ereignis, das 40 Sekunden zu spät eingeht, beträgt die Ereigniszeit beispielsweise 00:10:00 und die Ankunftszeit 00:10:40. Wenn Sie die Richtlinie für die Eingangsverzögerung auf 15 Sekunden festlegen, werden Ereignisse, die später als 15 Sekunden eingehen, entweder gelöscht (nicht von Stream Analytics verarbeitet), oder ihre Ereigniszeit wird angepasst. Im obigen Beispiel geht das Ereignis 40 Sekunden zu spät ein (mehr als in der Richtlinie festgelegt), und die Ereigniszeit wird an den Maximalwert der Richtlinie angepasst: 00:10:25 (Eingangszeit - Wert der Richtlinie für die Eingangsverzögerung). Der Standardwert der Richtlinie für die Eingangsverzögerung beträgt 5 Sekunden.

## <a name="what-is-out-of-order-policy"></a>Was ist die Richtlinie für die falsche Reihenfolge? 

Ereignisse können auch in der falschen Reihenfolge eingehen. Wenn die Ereigniszeit auf Basis der Richtlinie für die Eingangsverzögerung angepasst wurde, können Sie sie auch automatisch löschen oder die Ereignisse in falscher Reihenfolge anpassen. Wenn Sie diese Richtlinie auf 8 Sekunden festlegen, werden alle Ereignisse, die in der falschen Reihenfolge innerhalb des Zeitfensters von 8 Sekunden eingehen, anhand der Ereigniszeit neu angeordnet. Ereignisse, die später eingehen, werden gelöscht oder auf den maximalen Wert der Richtlinie für die falsche Reihenfolge angepasst. Der Standardwert der Richtlinie für die falsche Reihenfolge beträgt 0 Sekunden. 

## <a name="adjust-or-drop-events"></a>Anpassen oder Löschen von Ereignissen

Wenn Ereignisse basierend auf Ihren Richtlinien zu spät oder in falscher Reihenfolge eingehen, können Sie diese Ereignisse löschen (nicht von Stream Analytics verarbeiten lassen) oder die Ereigniszeit anpassen.

Nachfolgend sehen Sie ein Beispiel für die Anwendung dieser Richtlinien.
<br> **Richtlinie für die Eingangsverzögerung:** 15 Sekunden
<br> **Richtlinie für die falsche Reihenfolge:** 8 Sekunden

| Ereignisnummer | Ereigniszeit | Ankunftszeit | System.Timestamp | Erklärung |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | Das Ereignis ist zu spät und außerhalb des Toleranzbereichs eingegangen. Daher wird die Ereigniszeit auf die maximale Toleranz für Eingangsverzögerung angepasst.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | Das Ereignis ist zu spät, aber innerhalb des Toleranzbereichs eingegangen. Daher wird die Ereigniszeit nicht angepasst.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | Das Ereignis ist rechtzeitig eingegangen. Keine Anpassung erforderlich.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | Das Ereignis ist in falscher Reihenfolge, aber innerhalb des Toleranzbereichs von 8 Sekunden eingegangen. Daher wird die Ereigniszeit nicht angepasst. Zu Analysezwecken wird dieses Ereignis als vorhergehendes Ereignis mit der Nummer 4 behandelt.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | Das Ereignis ist in falscher Reihenfolge und außerhalb des Toleranzbereichs von 8 Sekunden eingegangen. Daher wird die Ereigniszeit an die maximale Toleranz für die falsche Reihenfolge angepasst. |

## <a name="can-these-settings-delay-output-of-my-job"></a>Können diese Einstellungen die Ausgabe des Auftrags verzögern? 

Ja. Der Standardwert der Richtlinie für die falsche Reihenfolge beträgt 0 (Null) (00 Minuten und 00 Sekunden). Wenn Sie den Standardwert ändern, wird die erste Ausgabe des Auftrags um diesen Wert (oder höher) verzögert. 

Wenn eine der Partitionen Ihrer Eingaben keine Ereignisse empfängt, wird sich die Ausgabe um den Wert der Richtlinie für die Eingangsverzögerung verzögern. Die Gründe dafür finden Sie im Abschnitt zu Fehlern vom Typ „InputPartition“. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>In meinem Aktivitätsprotokoll werden Meldungen vom Typ „LateInputEvents“ angezeigt.

Mit diesen Meldungen werden Sie darüber informiert, dass Ereignisse zu spät eingetroffen sind und (je nach Konfiguration) gelöscht oder angepasst wurden. Sie können diese Meldungen ignorieren, wenn Sie die Richtlinie für die Eingangsverzögerung ordnungsgemäß konfiguriert haben. 

Hier sehen Sie ein Beispiel für diese Meldung: <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>In meinem Aktivitätsprotokoll wird „InputPartitionNotProgressing“ angezeigt.

Ihre Eingabequelle (Event Hub/IoT Hub) verfügt wahrscheinlich über mehrere Partitionen. Azure Stream Analytics generiert eine Ausgabe für den Zeitstempel „t1“ erst, wenn alle kombinierten Quellen mindestens den Zeitpunkt „t1“ erreicht haben. Angenommen, die Abfrage liest aus einer Event Hub-Partition, die zwei Partitionen umfasst. „P1“, eine der Partitionen, verfügt über Ereignisse bis zum Zeitpunkt „t1“. „P2“, die andere Partition, verfügt über Ereignisse bis zum Zeitpunkt „t1 + x“. Die Ausgabe wird dann bis zum Zeitpunkt „t1“ erzeugt. Wenn jedoch eine explizite Klausel vom Typ „Partition by PartitionId“ vorhanden ist, werden beide Partitionen unabhängig voneinander verarbeitet. 

Wenn mehrere Teile des gleichen Eingabedatenstroms kombiniert werden, ist die Toleranz für die Eingangsverzögerung der maximale Zeitraum, in dem jede Partition auf neue Daten wartet. Wenn in Ihrem Event Hub eine Partition vorhanden ist oder IoT Hub keine Eingaben empfängt, wird die Zeitachse für diese Partition erst nach Erreichen des Schwellenwerts der Toleranz für die Eingangsverzögerung fortgesetzt. Dadurch verzögert sich Ihre Ausgabe um den Schwellenwert der Toleranz für die Eingangsverzögerung. In solchen Fällen wird möglicherweise die folgende Fehlermeldung angezeigt:
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
Mit dieser Meldung werden Sie informiert, dass mindestens eine Partition in Ihrer Eingabe leer ist. Ihre Ausgabe verzögert sich somit um den Schwellenwert für die Eingangsverzögerung. Sie haben zwei Möglichkeiten, um das Problem zu beheben:  
1. Stellen Sie sicher, dass alle Partitionen Ihres Event Hubs oder IoT Hubs Eingaben empfangen. 
2. Verwenden Sie in Ihrer Abfrage die Klausel „Partition by PartitionId“. 

## <a name="why-do-i-see-a-delay-of-5-seconds-even-when-my-late-arrival-policy-is-set-to-0"></a>Warum wird auch dann eine Verzögerung von 5 Sekunden angezeigt, wenn meine Richtlinie für Eingangsverzögerung auf 0 festgelegt ist?
Dies geschieht, wenn eine Eingabepartition vorliegt, die nie Eingaben empfangen hat. Sie können die Eingabemetrik nach Partition überprüfen, um dieses Verhalten zu überprüfen. 

Wenn eine Partition keine Daten für mehr als den konfigurierten Schwellenwert für die Eingangsverzögerung aufweist, verlegt Stream Analytics den Anwendungszeitstempel vor, wie im Abschnitt zu Überlegungen zur Ereignisreihenfolge erläutert. Dies erfordert die geschätzte Ankunftszeit. Wenn die Partition niemals Daten enthielt, schätzt Stream Analytics die Ankunftszeit nach der Formel *Ortszeit - 5 Sekunden*. Aufgrund dieser Partitionen, die niemals Daten enthielten, könnte eine Wasserzeichenverzögerung von 5 Sekunden angezeigt werden.  

## <a name="next-steps"></a>Nächste Schritte
* [Grundlegendes zur Behandlung von Zeitangaben](stream-analytics-time-handling.md)
* [In Stream Analytics verfügbare Metriken](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics)
---
title: Nachverfolgen der Sensoraktivität
description: Die Ereigniszeitachse zeigt eine Zeitskala der in Ihrem Netzwerk erkannten Aktivität an, einschließlich Warnungen und Warnungsverwaltungsaktionen, Netzwerkereignisse und Benutzervorgänge wie Benutzeranmeldung und Benutzerlöschung.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/10/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: b8a7679521096cf3d98e8f55748eb0398fdc4ab8
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523599"
---
# <a name="track-sensor-activity"></a>Nachverfolgen der Sensoraktivität

## <a name="event-timeline"></a>Ereigniszeitachse

Die Ereigniszeitachse zeigt eine Zeitskala der Aktivität an, die der Sensor erkannt hat. Beispiel:

  - Warnungen und Warnungsverwaltungsaktionen

  - Netzwerkereignisse

  - Benutzervorgänge, wie z. B. Benutzeranmeldung und Benutzerlöschung

Die Ereigniszeitachse bietet eine chronologische Ansicht von Ereignissen, die im Netzwerk aufgetreten sind. Die Ereigniszeitachse ermöglicht das Verständnis und die Analyse der Ereigniskette, die einem Angriff oder Incident vorausgegangen ist, was zur Untersuchung und Forensik beitragen kann.

> [!NOTE]
> *Administratoren* und *Sicherheitsanalysten* können die in diesem Abschnitt beschriebenen Verfahren ausführen.

So zeigen Sie die Ereignisprotokolle an:

- Wählen Sie im Seitenmenü **Event Timeline** (Ereigniszeitachse) aus.

   :::image type="content" source="media/how-to-track-sensor-activity/event-timeline.png" alt-text="Ansicht der Ereignisse auf der Ereigniszeitachse":::

Zusätzlich zum Anzeigen der Ereignisse, die der Sensor erkannt hat, können Sie der Zeitachse Ereignisse manuell hinzufügen. Dieser Vorgang ist nützlich, wenn das Ereignis in einem externen System aufgetreten ist, sich jedoch auf Ihr Netzwerk auswirkt und es wichtig ist, das Ereignis aufzuzeichnen und als Teil der Zeitachse darzustellen.

So fügen Sie Ereignisse manuell hinzu:

- Wählen Sie **Ereignis erstellen** aus.

So exportieren Sie Ereignisprotokollinformationen in eine CSV-Datei:

- Wählen Sie **Exportieren** aus.

## <a name="filter-the-event-timeline"></a>Filtern der Ereigniszeitachse

Filtern Sie die Zeitachse, um die für Sie interessanten Geräte und Ereignisse anzuzeigen.

So filtern Sie die Zeitachse:

1. Wählen Sie **Erweiterte Filter** aus.

   :::image type="content" source="media/how-to-track-sensor-activity/advance-filters.png" alt-text="Verwendung des Fensters „Events Advanced Filters“ (Erweiterte Filter für Ereignisse) zum Filtern der Ereignisse":::

2. Legen Sie Ereignisfilter wie folgt fest:

   - **Include Address** (Adresse einschließen): Hiermit werden Ereignisse für bestimmte Geräte angezeigt.

   - **Exclude Address** (Adresse ausschließen): Hiermit werden Ereignisse für bestimmte Geräte ausgeblendet.

   - **Include Event Types** (Ereignistypen einschließen): Hiermit werden bestimmte Ereignistypen angezeigt.

   - **Exclude Event Types** (Ereignistypen ausschließen): Hiermit werden bestimmte Ereignistypen ausgeblendet.

   - **Device Group** (Gerätegruppe): Auswählen einer Gerätegruppe, so wie in der Gerätezuordnung definiert. Es werden nur Ereignisse aus dieser Gruppe angezeigt.

3. Wählen Sie **Clear All** (Auswahl aufheben) aus, um alle ausgewählten Filter zu löschen.

4. Suchen Sie nach **Alerts Only** (Nur Warnungen), **Alerts and Notices** (Warnungen und Benachrichtigungen) oder **All Events** (Alle Ereignisse).

5. Klicken Sie auf **Select Date** (Datum auswählen), um ein bestimmtes Datum auszuwählen. Wählen Sie einen Tag, eine Stunde und eine Minute aus. Es werden Ereignisse aus dem ausgewählten Zeitrahmen angezeigt.

6.  Wählen Sie **User Operations** (Benutzervorgänge) aus, um Benutzervorgangsereignisse einzuschließen oder auszuschließen.

7.  Klicken Sie auf den Pfeil (**V**), um weitere Informationen zum Ereignis anzuzeigen:

    - Wählen Sie ggf. die entsprechenden Warnungen aus, um eine ausführliche Beschreibung der Warnung anzuzeigen.

    - Wählen Sie das Gerät aus, um das Gerät auf der Karte anzuzeigen.

    - Wählen Sie **Filter events by related devices** (Ereignisse nach verwandten Geräten filtern) aus, wenn Sie nach verwandten Geräten filtern möchten.

    - Wählen Sie **PCAP-Datei** aus, um die PCAP-Datei herunterladen (falls vorhanden), die eine Paketerfassung des gesamten Netzwerk zu einem bestimmten Zeitpunkt enthält. 
    
      Die PCAP-Datei enthält technische Informationen, die Netzwerktechniker dabei helfen, die genauen Parameter des Ereignisses zu ermitteln. Sie können die PCAP-Datei mit einem Tool für die Netzwerkprotokollanalyse wie der Open-Source-Anwendung „Wireshark“ analysieren.

## <a name="next-steps"></a>Nächste Schritte

[Anzeigen von Warnungen](how-to-view-alerts.md)

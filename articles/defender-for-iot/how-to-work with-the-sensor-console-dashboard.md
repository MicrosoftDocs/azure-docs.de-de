---
title: Verwenden des Sensorkonsolendashboards
description: Das Dashboard ermöglicht es Ihnen, sich schnell einen Überblick über den Sicherheitsstatus Ihres Netzwerks zu machen. Es bietet eine allgemeine Übersicht über Bedrohungen für Ihr gesamtes System auf einer Zeitachse sowie Informationen zu den betroffenen Geräten.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/03/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: eb37434213dd756ba5d7137b93a1cd37da5bb9ae
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523633"
---
# <a name="the-dashboard"></a>Das Dashboard

Das Dashboard ermöglicht es Ihnen, sich schnell einen Überblick über den Sicherheitsstatus Ihres Netzwerks zu machen. Es bietet eine allgemeine Übersicht über Bedrohungen für Ihr gesamtes System auf einer Zeitachse sowie Informationen zu den betroffenen Geräten. Dazu gehören folgende Informationen:

- Warnungen mit unterschiedlichem Schweregrad:

- Kritisch

- Hauptversion

- Nebenversion

- Warnungen

- Die beiden Indikatoren in der Mitte der Seite zeigen die Werte für „Pakete pro Sekunde“ (Packets per Second, PPS) und „Nicht bestätigte Warnungen“ (Unacknowledged Alerts, UA) an. **PPS** ist die Anzahl der vom System pro Sekunde erkannten Pakete. **UA** ist die Anzahl der Warnungen, die noch nicht bestätigt wurden.

- Liste nicht bestätigter Warnungen mit jeweiliger Beschreibung

- Zeitachse mit Warnungsbeschreibung

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/dashboard-alert-screen-v2.png" alt-text="Dashboard":::

## <a name="viewing-the-latest-alerts"></a>Anzeigen aktueller Warnungen

Das Messgerät für nicht bestätigte Warnungen (UA) in der Mitte der Seite zeigt die Anzahl solcher Warnungen an. Wenn Sie eine Liste der Warnungen anzeigen möchten, klicken Sie unten auf der Dashboardseite auf **Mehr Warnungen**, oder klicken Sie im Menü auf der linken Seite auf **Warnungen**.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unhandled-alerts-list.png" alt-text="Nicht bestätigte Warnungen":::

## <a name="status-boxes"></a>Statusfelder

In diesem Abschnitt werden die einzelnen Statusfelder beschrieben.

| Statusfeld und Messgerät | BESCHREIBUNG |
| -------------- | -------------- |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/critical-alert-status-box-v2.png" alt-text="Kritische Warnungen"::: | **Kritische Warnungen (Critical):** Im Feld, das sich oben mittig auf der Seite befindet, wird die Anzahl kritischer Warnung angezeigt. Wenn Sie auf dieses Feld klicken, werden Beschreibungen der Warnungen (sofern vorliegend) auf einer Zeitachse sowie auf der Liste unter den Messgeräten angezeigt.                              |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/major-alert-status-box-v2.png" alt-text="Wichtige Warnungen"::: | **Wichtige Warnungen (Major):** Im Feld, das sich oben rechts auf der Seite befindet, wird die Anzahl wichtiger Warnungen angezeigt. Wenn Sie auf dieses Feld klicken, werden Beschreibungen der Warnungen (sofern vorliegend) auf einer Zeitachse sowie auf der Liste unter den Messgeräten angezeigt.                                     |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/minor-alert-status-box-v2.png" alt-text="Benachrichtigungen"::: | **Benachrichtigungen (Minor):** Im Feld, das sich oben links auf der Seite befindet, wird die Anzahl von Benachrichtigungen angezeigt. Wenn Sie auf dieses Feld klicken, werden Beschreibungen der Warnungen (sofern vorliegend) auf einer Zeitachse sowie auf der Liste unter den Messgeräten angezeigt.                                   |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/warnings-alert-status-box-v2.png" alt-text="Warnungen"::: | **Warnungen (Warnings):** Im Feld, das sich unten mittig auf der Seite befindet, wird die Anzahl von Warnungen angezeigt. Wenn Sie auf dieses Feld klicken, werden Beschreibungen der Warnungen (sofern vorliegend) auf einer Zeitachse sowie auf der Liste unter den Messgeräten angezeigt.                             |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/all-alert-status-box-v2.png" alt-text="Alle Warnungen"::: | **Alle Warnungen (All):** Im Feld, das sich unten rechts auf der Seite befindet, wird die Anzahl aller kritischen Warnungen, wichtigen Warnungen, Benachrichtigungen und Warnungen angezeigt. Wenn Sie auf dieses Feld klicken, werden Beschreibungen der Warnungen (sofern vorliegend) auf einer Zeitachse sowie auf der Liste unter den Messgeräten angezeigt. |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/packets-per-second-gauge-v2.png" alt-text="Pakete pro Sekunde"::: | **Pakete pro Sekunden (PPS):** Die PPS-Metrik ist ein Indikator für die Leistung des Netzwerks. |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unacknowledged-events-gauge-v2.png" alt-text="Nicht bestätigte Ereignisse"::: | **Nicht bestätigte Ereignisse (UA):** Diese Metrik zeigt die Anzahl nicht bestätigter Ereignisse.

## <a name="using-the-timeline"></a>Verwenden der Zeitachse

Die Warnungen werden auf einer vertikalen Zeitachse angezeigt, auf der Informationen zu Datum und Uhrzeit zu finden sind.

Folgendes wird auf der Zeitachse grafisch dargestellt:

- Kritische Warnungen

- Wichtige Warnungen (Major)

- Benachrichtigungen (Minor)

- Warnungen

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/timeline-of-events.png" alt-text="Abbildung der Zeitachse":::

## <a name="viewing-alerts"></a>Anzeigen von Warnungen

Klicken Sie auf den Abwärtspfeil **V** im unteren Teil eines Warnungsfelds, damit der Warnungseintrag sowie Geräteinformationen angezeigt werden.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/extended-alert-screen.png" alt-text="Warnungseintrag und Geräteinformationen":::

- Wählen Sie das Gerät aus, um die physische Zuordnungsübersicht anzuzeigen. Die betroffenen Geräte werden hervorgehoben.

- Klicken Sie im Feld „Warnung“ auf eine beliebige Stelle, um weitere Details zur Warnung anzuzeigen. Es wird ein dem folgenden Bildschirm ähnliches Popup angezeigt.

- Klicken Sie auf :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/excel-icon.png" alt-text="Excel":::, um eine CSV-Datei zur Warnung zu exportieren.

- Nur für Administratoren und Sicherheitsanalysten: Klicken Sie auf :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/approve-all-icon.png" alt-text="Acknowledge all"::: (Alle bestätigen), um **alle entsprechenden Warnungen zu bestätigen**.

- Klicken Sie auf :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pdf-icon.png" alt-text="PDF":::, um einen Warnungsbericht als PDF-Datei herunterzuladen.

- Klicken Sie auf :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pin-icon.png" alt-text="Pin":::, um die Warnung anzupinnen oder loszulösen. Wenn Sie die Option zum Anheften auswählen, wird sie im Fenster **Angeheftete Warnungen** des Bildschirms **Warnungen** angeheftet.

- Wählen Sie :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/download-icon.png" alt-text="Download"::: aus, um die Warnung weiter zu untersuchen, indem Sie die zugehörige PCAP-Datei mit einer Analyse des Netzwerkprotokolls herunterladen.

- Klicken Sie auf :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/cloud-download-icon.png" alt-text="Cloud":::, um eine zugehörige gefilterte PCAP-Datei herunterzuladen, die nur die warnungsrelevanten Pakete enthält. So kann die Größe der Ausgabedatei reduziert werden, was eine fokussiertere Analyse ermöglicht. Sie können sie mithilfe von [Wireshark](https://www.wireshark.org/) anzeigen.

- Klicken Sie auf :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/navigate-icon.png" alt-text="Navigation":::, um zur Ereigniszeitachse zum Zeitpunkt der angeforderten Warnung zu navigieren. Dies ermöglicht es Ihnen, andere Ereignisse zu bewerten, die um die jeweilige Warnung herum auftreten können.

- Nur für Administratoren und Sicherheitsanalysten: Ändern Sie den Status einer Warnung von nicht bestätigt in bestätigt. Klicken Sie auf „Learn“, um die erkannte Aktivität zu genehmigen.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unauthorized-internet-connectivity-detection-v3.png" alt-text="Nicht autorisierte Internetverbindung erkannt":::

## <a name="next-steps"></a>Nächste Schritte

[Arbeiten mit Sensorwarnungen](how-to-work-with-alerts-on-your-sensor.md)

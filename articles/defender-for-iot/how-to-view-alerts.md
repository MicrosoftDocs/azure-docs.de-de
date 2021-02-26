---
title: Anzeigen von Warnungen
description: Zeigen Sie Warnungen nach verschiedenen Kategorien an, und verwenden Sie Suchfunktionen, um nach bestimmten Warnungen zu suchen.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: bce83e11a0d4567d37e78388445c108385ca9a61
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509068"
---
# <a name="view-alerts"></a>Anzeigen von Warnungen

In diesem Artikel wird beschrieben, wie Sie von Ihrem Sensor ausgelöste Warnungen anzeigen und mit Benachrichtigungstools verwalten.

Sie können Warnungen nach verschiedenen Kategorien anzeigen, wie z. B. archivierte oder angeheftete Warnungen. Oder Sie können nach bestimmten Warnungen suchen, z. B. nach Warnungen, die auf einer IP- oder MAC-Adresse basieren.  

Sie können auch Warnungen aus dem Sensordashboard anzeigen.

So zeigen Sie Warnungen an:

- Wählen Sie im Seitenmenü die Option **Warnungen** aus. Im Fenster **Warnungen** werden die vom Sensor erkannten Warnungen angezeigt.

  :::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-screen.png" alt-text="Anzeige des Bildschirms „Warnungen“":::

## <a name="view-alerts-by-category"></a>Anzeigen von Warnungen nach Kategorie

In der Hauptansicht **Warnungen** können Sie Warnungen nach verschiedenen Kategorien anzeigen. Wählen Sie eine Warnung aus, um Details zu überprüfen und das Ereignis zu verwalten.

| Sortieren nach Typ | BESCHREIBUNG |
|--|--|
| **Wichtige Warnungen** | Nach Wichtigkeit sortierte Warnungen. |
| **Angeheftete Warnungen** | Vom Benutzer zur weiteren Untersuchung angeheftete Warnungen. Angeheftete Warnungen werden nicht archiviert. Sie werden im angehefteten Ordner 14 Tage lang gespeichert. |
| **Letzte Warnungen** | Nach Zeit sortierte Warnungen. |
| **Bestätigte Warnungen** | Warnungen, die bestätigt und nicht behandelt wurden oder die stummgeschaltet wurden und deren Stummschaltung aufgehoben wurde. |
| **Archivierte Warnungen** | Vom System automatisch archivierte Warnungen. Nur der Administratorbenutzer kann darauf zugreifen. |

## <a name="search-for-alerts-of-interest"></a>Suchen nach bestimmten Warnungen

Die Hauptansicht **Warnungen** bietet verschiedene Suchfunktionen, mit denen Sie nach bestimmten Warnungen suchen können.

:::image type="content" source="media/how-to-work-with-alerts-sensor/main-alerts-view.png" alt-text="Screenshot: Erlernen von Warnungen":::

### <a name="text-search"></a>Textsuche 

Verwenden Sie die Option **Freie Suche**, um anhand von Text, Zahlen oder Zeichen nach Warnungen zu suchen.

Gehen Sie zum Suchen wie folgt vor:

- Geben Sie im Feld **Freie Suche** den erforderlichen Text ein, und drücken Sie auf der Tastatur die EINGABETASTE.

Gehen Sie wie folgt vor, um die Suche zu löschen:

- Löschen Sie den Text im Feld **Freie Suche**, und drücken Sie auf der Tastatur die EINGABETASTE.

### <a name="device-group-or-device-ip-address-search"></a>Suche nach Gerätegruppen oder IP-Adressen von Geräten

Sie können nach Warnungen suchen, die auf bestimmte IP-Adressen oder Gerätegruppen verweisen. Gerätegruppen werden in der Gerätezuordnung erstellt.

Gehen Sie wie folgt vor, um erweiterte Filter zu verwenden:

1. Wählen Sie in der Hauptansicht **Warnungen** die Option **Erweiterte Filter** aus.

2. Wählen Sie eine Gerätegruppe oder ein Gerät aus.

3. Klicken Sie auf **Bestätigen**.

4. Um die Suche zu löschen, wählen Sie **Auswahl aufheben** aus.

### <a name="security-versus-operational-alert-search"></a>Suchen nach Sicherheitswarnungen oder Suchen nach Vorgangswarnungen

Wechseln Sie zwischen der Anzeige von Vorgangs- und Sicherheitswarnungen:

- **Sicherheitswarnungen** stellen potenziellen Malwaredatenverkehr, Netzwerkanomalien, Richtlinienverstöße und Protokollverletzungen dar.

- **Vorgangswarnungen** stellen Netzwerkanomalien, Richtlinienverstöße und Protokollverletzungen dar.

Wenn Sie keine der Optionen auswählen, werden alle Warnungen angezeigt.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-security.png" alt-text="Bildschirm „Warnungen“ mit ausgewählter Option „Sicherheit“":::

## <a name="alert-page-options"></a>Optionen auf der Seite „Warnung“

Für Warnmeldungen sind die folgenden Aktionen verfügbar:

- Wählen Sie :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-an-alert-icon.png" border="false"::: aus, um eine Warnung zu bestätigen.

- Wählen Sie :::image type="icon" source="media/how-to-work-with-alerts-sensor/unacknowledge-an-alert-icon.png" border="false"::: aus, um die Bestätigung einer Warnung aufzuheben.

- Wählen Sie :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: aus, um eine Warnung anzuheften.

- Wählen Sie :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: aus, um eine Warnung zu lösen.

- Wählen Sie :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-all-alerts-icon.png" border="false"::: aus, um alle Warnungen zu bestätigen.

- Wählen Sie :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-and-acknowledge-all-alerts.png" border="false"::: aus, um alle Warnungen zu erlernen und zu bestätigen.

- Wählen Sie :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-csv.png" border="false"::: aus, um die Warnungsliste in eine CSV-Datei zu exportieren, und wählen Sie die entsprechende Exportoption aus. Wählen Sie **Warnungsexport** für die Standardoption zum Exportieren in das CSV-Format aus. Oder wählen Sie **Erweiterter Warnungsexport** aus, um separate Zeilen für zusätzliche Informationen zu einer Warnung in der CSV-Datei hinzufügen zu können.

## <a name="alert-pop-up-window-options"></a>Optionen im Popupfenster „Warnung“

- Wählen Sie das Symbol :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-pdf.png" border="false"::: aus, um einen Warnungsbericht als PDF-Datei herunterzuladen.

- Wählen Sie das Symbol :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-pcap.png" border="false"::: aus, um die PCAP-Datei herunterzuladen. Die Datei kann mit Wireshark, dem kostenlosen Netzwerkprotokoll-Analysetool, angezeigt werden.

- Wählen Sie :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-filtered-pcap.png" border="false"::: aus, um eine gefilterte PCAP-Datei herunterzuladen, die nur die relevanten Warnungspakete enthält. So kann die Größe der Ausgabedatei reduziert werden, was eine fokussiertere Analyse ermöglicht. Sie können die Datei mithilfe von Wireshark anzeigen.

- Wählen Sie das Symbol :::image type="icon" source="media/how-to-work-with-alerts-sensor/show-alert-in-timeline.png" border="false"::: aus, um die Warnung in der Ereigniszeitachse anzuzeigen.

- Wählen Sie das Symbol :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: aus, um die Warnung anzuheften.

- Wählen Sie das Symbol :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: aus, um die Warnung zu lösen.

- Wählen Sie :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-icon.png" border="false"::: aus, um den Datenverkehr zu genehmigen (gilt nur für Sicherheitsanalysten und Administratoren).

- Wählen Sie ein Gerät aus, um es in der Gerätezuordnung anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

[Verwalten des Warnungsereignisses](how-to-manage-the-alert-event.md)

[Beschleunigen des Warnungsworkflows](how-to-accelerate-alert-incident-response.md)

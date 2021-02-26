---
title: Importieren von Geräteinformationen
description: Defender für IoT-Sensoren überwachen und analysieren gespiegelten Datenverkehr. In diesen Fällen möchten Sie eventuell Daten importieren, um die Informationen zu bereits erkannten Geräten zu ergänzen.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/06/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 53c342005d2c33e2ee18057fe0e667ebdec5166c
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522239"
---
# <a name="import-device-information-to-a-sensor"></a>Importieren von Geräteinformationen in einen Sensor

Ein Azure Defender für IoT-Sensor überwacht und analysiert gespiegelten Datenverkehr. In einigen Fällen wird möglicherweise aufgrund organisationsspezifischer Netzwerk-Konfigurationsrichtlinien ein Teil der Informationen nicht übertragen.

In diesen Fällen möchten Sie eventuell Daten importieren, um die Informationen zu bereits erkannten Geräten zu ergänzen. Zum Importieren von Informationen in Sensoren stehen zwei Möglichkeiten zur Verfügung:

- **Importieren aus der Übersicht**: Aktualisieren Sie den Namen, den Typ, die Gruppe oder die Purdue-Ebene des Geräts in der Übersicht.

- **Importieren aus Importeinstellungen**: Importieren Sie das Betriebssystem, die IP-Adresse, die Patchebene oder den Autorisierungsstatus des Geräts.

## <a name="import-from-the-map"></a>Importieren aus der Übersicht

In diesem Abschnitt wird beschrieben, wie Namen, Typen, Gruppen oder Purdue-Ebenen von Geräten in die Geräteübersicht importiert werden. Dieser Vorgang erfolgt in der Übersicht.

Nachfolgend sind die Importanforderungen aufgeführt:

- **Namen**: Können bis zu 30 Zeichen umfassen.

- **Typ** oder **Purdue-Ebene**: Verwenden Sie die Optionen, die im Dialogfeld **Geräteeigenschaften** angezeigt werden. (Klicken Sie mit der rechten Maustaste auf das Gerät, und wählen Sie **Eigenschaften anzeigen** aus.)

- **Gerätegruppe**: Erstellen Sie eine neue Gruppe mit bis zu 30 Zeichen. 

> [!NOTE]
> Um Konflikte zu vermeiden, importieren Sie nicht die Daten, die Sie von einem Sensor exportiert haben, in einen anderen Sensor.

Zum Importieren gehen Sie folgendermaßen vor:

1. Wählen Sie im seitlichen Menü **Geräte** aus.

2. Wählen Sie oben rechts im Fenster **Geräte** das Symbol :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: aus.

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="Screenshot des Gerätefensters":::

3. Wählen Sie **Geräte exportieren** aus. Eine Vielzahl von Informationen wird in der exportierten Datei angezeigt. Diese Informationen umfassen vom Gerät verwendete Protokolle und den Autorisierungsstatus des Geräts.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="Informationen in der exportierten Datei":::

4. Ändern Sie in der CSV-Datei nur den Namen, den Typ, die Gruppe und die Purdue-Ebene des Geräts. Speichern Sie dann die Datei. 

   Verwenden Sie die in der exportierten Datei gezeigte Groß-/Kleinschreibung. Geben Sie z. B. für die Purdue-Ebene die Anfangsbuchstaben als Großbuchstaben ein.

5. Wählen Sie im Fenster **Gerät** im Dropdownmenü **Importieren/Exportieren** die Option **Geräte importieren** aus.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="Importieren von Geräten über das Gerätefenster":::

6. Wählen Sie **Geräte importieren** und dann die CSV-Datei aus, die Sie importieren möchten. Es werden Meldungen zum Importstatus auf dem Bildschirm angezeigt, bis das Dialogfeld **Geräte importieren** geschlossen wird.

## <a name="import-from-import-settings"></a>Importieren aus Importeinstellungen

In diesem Abschnitt wird beschrieben, wie die IP-Adresse, das Betriebssystem, die Patchebene oder der Autorisierungsstatus des Geräts in die Geräteübersicht importiert werden. Dazu verwenden Sie das Dialogfeld **Einstellungen importieren**.

Zum Importieren von IP-Adresse, Betriebssystem und Patchebene gehen Sie folgendermaßen vor:

1. Laden Sie die Datei [devices_info_2.2.8 and up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) aus dem [Hilfecenter](https://cyberx-labs.zendesk.com/hc/en-us) herunter, und geben Sie die Informationen wie folgt ein:

   - **IP-Adresse**: Geben Sie die IP-Adresse des Geräts ein.

   - **Betriebssystem:** Wählen Sie einen Eintrag in der Dropdownliste aus.

   - **Letzte Aktualisierung**: Verwenden Sie das Format JJJJ-MM-TT.

     :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="Bildschirm mit Optionen":::

2. Wählen Sie im seitlichen Menü **Einstellungen importieren** aus.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="Importieren der Einstellungen":::

3. Um die erforderliche Konfiguration hochzuladen, wählen Sie im Abschnitt **Geräteinformationen** die Option **Hinzufügen** aus, und laden Sie die von Ihnen vorbereitete CSV-Datei hoch.

Zum Importieren des Autorisierungsstatus gehen Sie folgendermaßen vor:

1. Laden Sie die Datei [authorized_devices.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) aus dem Defender für IoT-Hilfecenter herunter, und speichern Sie sie. Vergewissern Sie sich, dass Sie die Datei als CSV-Datei gespeichert haben.

2. Geben Sie die Informationen wie folgt ein:

   - **IP-Adresse**: Die IP-Adresse des Geräts.

   - **Name**: Der Name des autorisierten Geräts. Stellen Sie sicher, dass Namen korrekt angegeben sind. Die Namen der Geräte in der importierten Liste überschreiben die in der Geräteübersicht angezeigten Namen.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="Excel-Datei mit importierter Geräteliste":::

3. Wählen Sie im seitlichen Menü **Einstellungen importieren** aus.

4. Wählen Sie im Abschnitt **Autorisierte Geräte** die Option **Hinzufügen** aus, und laden Sie die gespeicherte CSV-Datei hoch.

Beim Importieren der Informationen werden Warnungen zu nicht autorisierten Geräten für alle Geräte angezeigt, die nicht in dieser Liste enthalten sind.

## <a name="import-device-information-to-the-sensor"></a>Importieren von Geräteinformationen in den Sensor

Der Sensor überwacht und analysiert gespiegelten Datenverkehr. In einigen Fällen wird möglicherweise aufgrund organisationsspezifischer Netzwerk-Konfigurationsrichtlinien ein Teil der Informationen nicht übertragen.

In diesen Fällen möchten Sie eventuell Daten importieren, um die Informationen zu bereits erkannten Geräten zu ergänzen. Zum Importieren von Informationen in Sensoren stehen zwei Möglichkeiten zur Verfügung:

- **Importieren aus der Übersicht**: Aktualisieren Sie den Namen, den Typ, die Gruppe oder die Purdue-Ebene des Geräts in der Übersicht.

- **Importieren aus Importeinstellungen**: Importieren Sie das Betriebssystem, die IP-Adresse, die Patchebene oder den Autorisierungsstatus des Geräts.

### <a name="import-from-the-map"></a>Importieren aus der Übersicht

In diesem Abschnitt wird beschrieben, wie Namen, Typen, Gruppen oder Purdue-Ebenen von Geräten in die Geräteübersicht importiert werden. Dieser Vorgang erfolgt in der Übersicht.

Nachfolgend sind die Importanforderungen aufgeführt:

- **Namen**: Können bis zu 30 Zeichen umfassen.

- **Typ** oder **Purdue-Ebene**: Verwenden Sie die Optionen, die im Dialogfeld **Geräteeigenschaften** angezeigt werden. (Klicken Sie mit der rechten Maustaste auf das Gerät, und wählen Sie **Eigenschaften anzeigen** aus.)

- **Gerätegruppe**: Erstellen Sie eine neue Gruppe mit bis zu 30 Zeichen. 

> [!NOTE]
> Um Konflikte zu vermeiden, importieren Sie nicht die Daten, die Sie von einem Sensor exportiert haben, in einen anderen Sensor.

Zum Importieren gehen Sie folgendermaßen vor:

1. Wählen Sie im seitlichen Menü **Geräte** aus.

2. Wählen Sie oben rechts im Fenster **Geräte** das Symbol :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: aus.

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="Fenster zum Auswählen des Geräts":::

3. Wählen Sie **Geräte exportieren** aus. Eine Vielzahl von Informationen wird in der exportierten Datei angezeigt. Beispiele umfassen vom Gerät verwendete Protokolle und den Autorisierungsstatus des Geräts.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="Informationen in der exportierten Datei":::

4. Ändern Sie in der CSV-Datei nur den Namen, den Typ, die Gruppe und die Purdue-Ebene des Geräts. Speichern Sie dann die Datei. 

   Verwenden Sie die in der exportierten Datei gezeigte Groß-/Kleinschreibung. Geben Sie z. B. für die Purdue-Ebene die Anfangsbuchstaben als Großbuchstaben ein.

5. Wählen Sie im Fenster **Gerät** im Dropdownmenü **Importieren/Exportieren** die Option **Geräte importieren** aus.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="Importieren der Geräte":::

6. Wählen Sie **Geräte importieren** und dann die CSV-Datei aus, die Sie importieren möchten. Es werden Meldungen zum Importstatus auf dem Bildschirm angezeigt, bis das Dialogfeld **Geräte importieren** geschlossen wird.

### <a name="import-from-import-settings"></a>Importieren aus Importeinstellungen 

In diesem Abschnitt wird beschrieben, wie die IP-Adresse, das Betriebssystem, die Patchebene oder der Autorisierungsstatus des Geräts in die Geräteübersicht importiert werden. Dazu verwenden Sie das Dialogfeld **Einstellungen importieren**.

Zum Importieren von IP-Adresse, Betriebssystem und Patchebene gehen Sie folgendermaßen vor:

1. Laden Sie die Datei [devices_info_2.2.8 and up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) aus dem [Hilfecenter](https://cyberx-labs.zendesk.com/hc/en-us) herunter, und geben Sie die Informationen wie folgt ein:

   - **IP-Adresse**: Die IP-Adresse des Geräts.

   - **Betriebssystem:** Wählen Sie einen Eintrag in der Dropdownliste aus.

   - **Datum der letzten Aktualisierung**: Verwenden Sie das Format JJJJ-MM-TT.

    :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="Der Inhalt auf dem Bildschirm.":::

2. Wählen Sie im seitlichen Menü **Einstellungen importieren** aus.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="Ausfüllen des Bildschirms „Einstellungen importieren“":::

3. Um die erforderliche Konfiguration hochzuladen, wählen Sie im Abschnitt **Geräteinformationen** die Option **Hinzufügen** aus, und laden Sie die von Ihnen vorbereitete CSV-Datei hoch.

Zum Importieren des Autorisierungsstatus gehen Sie folgendermaßen vor:

1. Laden Sie die Datei [authorized_devices - examples.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) aus dem Defender für IoT-Hilfecenter herunter, und speichern Sie sie. Vergewissern Sie sich, dass Sie die Datei als CSV-Datei gespeichert haben.

2. Geben Sie die Informationen wie folgt ein:

   - **IP-Adresse**: Die IP-Adresse des Geräts.

   - **Name**: Der Name des autorisierten Geräts. Vergewissern Sie sich, dass Namen korrekt angegeben sind. Die Namen der Geräte in der importierten Liste überschreiben die in der Geräteübersicht angezeigten Namen.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="Importliste für die Geräteübersicht":::

3. Wählen Sie im seitlichen Menü **Einstellungen importieren** aus.

4. Wählen Sie im Abschnitt **Autorisierte Geräte** die Option **Hinzufügen** aus, und laden Sie die gespeicherte CSV-Datei hoch.

Beim Importieren der Informationen werden Warnungen zu nicht autorisierten Geräten für alle Geräte angezeigt, die nicht in dieser Liste enthalten sind.

## <a name="next-steps"></a>Nächste Schritte

[Steuern, welcher Datenverkehr überwacht wird](how-to-control-what-traffic-is-monitored.md)

[Untersuchen von Sensorerkennungen in einem Gerätebestand](how-to-investigate-sensor-detections-in-a-device-inventory.md)

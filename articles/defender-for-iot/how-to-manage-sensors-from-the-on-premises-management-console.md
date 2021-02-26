---
title: Verwalten von Sensoren über die lokale Verwaltungskonsole
description: Erfahren Sie, wie Sie Sensoren über die Verwaltungskonsole verwalten können. Dazu gehören das Aktualisieren von Sensorversionen, Übertragen von Systemeinstellungen per Push an Sensoren sowie das Aktivieren und Deaktivieren von Engines für Sensoren.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 10051bfdf7a93a8d6c83d649a7b0ad008b5e2caf
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523956"
---
# <a name="manage-sensors-from-the-management-console"></a>Verwalten von Sensoren über die Verwaltungskonsole

In diesem Artikel wird beschrieben, wie Sensoren über die Verwaltungskonsole verwaltet werden. Dazu gehört Folgendes:

- Übertragen von Systemeinstellungen per Push an Sensoren

- Aktivieren und Deaktivieren von Engines für Sensoren

- Aktualisieren von Sensorversionen

## <a name="push-configurations"></a>Pushkonfigurationen

Sie können verschiedene Systemeinstellungen definieren und automatisch auf Sensoren anwenden, die mit der-Verwaltungskonsole verbunden sind. Das spart Zeit und sorgt für optimierte Einstellungen auf allen Unternehmenssensoren.

Die folgenden Systemeinstellungen für Sensoren können über die Verwaltungskonsole definiert werden:

- Mailserver

- SNMP-MIB-Überwachung

- Active Directory

- DNS-Einstellungen

- Subnetze

- Portaliase

Zum Anwenden von Systemeinstellungen gehen Sie folgendermaßen vor:

1. Wählen Sie im linken Bereich der Konsole die Option **Systemeinstellungen** aus.

2. Wählen Sie im Bereich **Sensoren konfigurieren** eine der Optionen aus.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-system-setting-options.png" alt-text="Systemeinstellungsoptionen für einen Sensor":::

   Im folgenden Beispiel wird das Definieren von Mailserverparametern für Unternehmenssensoren beschrieben.

3. Wählen Sie **Mailserver** aus.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="Auswählen Ihres Mailservers auf dem Bildschirm für Systemeinstellungen":::

4. Wählen Sie links einen Sensor aus.

5. Legen Sie die Mailserverparameter fest, und wählen Sie **Duplizieren** aus. Neben jedem Element in der Sensorstruktur wird ein Kontrollkästchen angezeigt.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/check-off-each-sensor.png" alt-text="Sicherstellen der Auswahl von Kontrollkästchen für Sensoren":::

6. Wählen Sie in der Sensorstruktur die Elemente aus, auf die Sie die Konfiguration anwenden möchten.

7. Wählen Sie **Speichern** aus.

## <a name="update-versions"></a>Aktualisieren von Versionen

Über die lokale Verwaltungskonsole können Sie mehrere Sensoren gleichzeitig aktualisieren.

Zum Aktualisieren mehrerer Sensoren gehen Sie folgendermaßen vor:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/).

2. Wechseln Sie zu Azure Defender für IoT.

3. Navigieren Sie zur Seite **Updates**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/update-screen.png" alt-text="Screenshot des Dashboardansicht „Updates“":::

4. Wählen Sie im Abschnitt **Sensoren** die Option **Herunterladen** aus, und speichern Sie die Datei.

5. Melden Sie sich bei der Verwaltungskonsole an, und wählen Sie **Systemeinstellungen** aus.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/admin-system-settings.png" alt-text="Screenshot des Menüs „Verwaltung“ zum Auswählen von „Systemeinstellungen“":::

6. Markieren Sie die zu aktualisierenden Sensoren im Abschnitt **Sensor-Engine-Konfiguration**, und wählen Sie dann **Automatische Updates** aus.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensors-select.png" alt-text="Zwei Sensoren mit Lernmodus und automatischen Updates":::

7. Wählen Sie **Änderungen speichern** aus.

8. Wählen Sie im Bereich für das **Versionsupgrade für Sensoren** das Symbol :::image type="icon" source="media/how-to-manage-sensors-from-the-on-premises-management-console/plus-icon.png" border="false"::: aus.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/display-files.png" alt-text="Bildschirm für das Versionsupgrade für Sensoren zum Anzeigen von Dateien":::

9. Ein Dialogfeld **Datei hochladen** wird geöffnet. Laden Sie die Datei hoch, die Sie von der Seite **Updates** heruntergeladen haben.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/upload-file.png" alt-text="Auswählen der Schaltfläche zum Durchsuchen für das Hochladen der Datei":::

10. Während des Aktualisierungsvorgangs wird der Aktualisierungsstatus der einzelnen Sensoren im Fenster **Standortverwaltung** angezeigt.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/progress.png" alt-text="Verfolgen des Aktualisierungsfortschritts":::

## <a name="update-threat-intelligence-packages"></a>Aktualisieren von Threat Intelligence-Paketen 

Das Datenpaket für Threat Intelligence wird mit jeder neuen Defender für IoT-Version oder bei Bedarf zwischen den Releases bereitgestellt. Das Paket enthält Signaturen (einschließlich Schadsoftwaresignaturen), CVEs und andere Sicherheitsinhalte. 

Sie können diese Datei manuell von der Seite **Updates** im Defender für IoT-Portal hochladen und automatisch auf Sensoren aktualisieren. 

Zum Aktualisieren von Threat Intelligence-Daten gehen Sie folgendermaßen vor: 

1. Navigieren Sie zur Defender für IoT-Seite **Updates**. 

2. Laden Sie die Datei herunter, und speichern Sie sie.

3. Melden Sie sich bei der Verwaltungskonsole an. 

4. Wählen Sie im seitlichen Menü **Systemeinstellungen** aus. 

5. Wählen Sie die Sensoren, die die Aktualisierung erhalten sollen, im Abschnitt **Sensor-Engine-Konfiguration** aus.  

6. Wählen Sie im Abschnitt **Threat Intelligence-Daten auswählen** das Pluszeichen ( **+** ) aus. 

7. Laden Sie das Paket hoch, das Sie von der Seite **Updates** in Defender für IoT heruntergeladen haben.

## <a name="understand-sensor-disconnection-events"></a>Grundlegendes zu Ereignissen zur Trennung der Sensorverbindung

Im Fenster **Site Manager** werden Informationen zur Trennung der Verbindung angezeigt, wenn Sensoren von der zugewiesenen lokalen Verwaltungskonsole getrennt werden. Die folgenden Informationen zur Trennung der Sensorverbindung sind verfügbar:

- „Die vom Sensor empfangenen Daten können von der lokalen Verwaltungskonsole nicht verarbeitet werden.“

- „Ein Zeitunterschied wurde erkannt. Die lokale Verwaltungskonsole wurde vom Sensor getrennt.“

- „Der Sensor kommuniziert nicht mit der lokalen Verwaltungskonsole. Überprüfen Sie die Netzwerkkonnektivität oder die Zertifikatüberprüfung.“

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="Screenshot der Zone 1-Ansicht":::

Sie können Warnungen mit Informationen über getrennte Sensoren an Dritte senden. Weitere Informationen finden Sie unter [Weiterleiten von Warnungen für Sensorfehler](how-to-manage-individual-sensors.md#forward-sensor-failure-alerts).

## <a name="enable-or-disable-sensors"></a>Aktivieren oder Deaktivieren von Sensoren

Sensoren werden von fünf Defender für IoT-Engines geschützt. Sie können die Engines für verbundene Sensoren aktivieren oder deaktivieren.

| Engine | BESCHREIBUNG | Beispielszenario |
|--|--|--|
| Protokollverletzungs-Engine | Eine Protokollverletzung tritt ein, wenn die Paketstruktur oder die Feldwerte nicht der Protokollspezifikation entsprechen. | Warnung „Illegal MODBUS Operation (Function Code Zero)“ (Illegaler MODBUS-Vorgang (Funktionscode null)). Diese Warnung weist darauf hin, dass ein primäres Gerät eine Anforderung mit einem Funktionscode 0 an ein sekundäres Gerät gesendet hat. Dies ist gemäß der Protokollspezifikation unzulässig, und das sekundäre Gerät behandelt die Eingabe möglicherweise nicht ordnungsgemäß. |
| Richtlinienverletzungs-Engine | Eine Richtlinienverletzung tritt ein, wenn eine Abweichung von dem in der gelernten oder konfigurierten Richtlinie definierten Baselineverhalten auftritt. | Warnung „Unauthorized HTTP User Agent“ (Nicht autorisierter HTTP-Benutzeragent). Diese Warnung zeigt an, dass eine Anweisung, die nicht gelernt oder durch die Richtlinie genehmigt wurde, als HTTP-Client auf einem Gerät verwendet wird. Dabei kann es sich um einen neuen Webbrowser oder eine Anwendung auf diesem Gerät handeln. |
| Schadsoftware-Engine | Die Schadsoftware-Engine erkennt böswillige Netzwerkaktivitäten. | Warnung „Suspicion of Malicious Activity (Stuxnet)“ (Verdacht auf böswillige Aktivität (Stuxnet)). Diese Warnung weist darauf hin, dass der Sensor verdächtige Netzwerkaktivität erkannt hat, von der bekannt ist, dass sie im Zusammenhang mit der Schadsoftware Stuxnet steht. Dies stellt eine fortgeschrittene ständige Bedrohung dar, die Industriesteuerungs- und SCADA-Netzwerke als Ziel hat. |
| Anomalie-Engine | Die Anomalie-Engine erkennt eine Anomalie im Netzwerkverhalten. | Warnung „Periodic Behavior in Communication Channel“ (Periodisches Verhalten im Kommunikationskanal). Hierbei handelt es sich um eine Komponente, die Netzwerkverbindungen überprüft und periodisches oder zyklisches Verhalten bei der Datenübertragung erkennt, das in Industrienetzwerken häufig vorkommt. |
| Betriebs-Engine | Diese Engine erkennt Betriebsvorfälle oder Entitäten mit Funktionsfehlern. | `Device is Suspected to be Disconnected (Unresponsive)`Warnung. Diese Warnung wird ausgelöst, wenn ein Gerät für einen vordefinierten Zeitraum auf keine Anforderungen reagiert. Sie kann auf einen Ausfall, eine Trennung oder eine Fehlfunktion des Geräts hindeuten.
|

Zum Aktivieren oder Deaktivieren von Engines für verbundene Sensoren gehen Sie folgendermaßen vor:

1. Wählen Sie im linken Bereich der Konsole die Option **Systemeinstellungen** aus.

2. Wählen Sie im Abschnitt **Sensor-Engine-Konfiguration** die Option **Aktivieren** oder **Deaktivieren** für die Engines aus.
         
3. Wählen Sie **Änderungen speichern** aus.

   Wenn aktivierte Engines für einen der Unternehmenssensoren nicht übereinstimmen, wird ein rotes Ausrufezeichen angezeigt. Die Engine wurde möglicherweise direkt vom Sensor deaktiviert.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/red-exclamation-example.png" alt-text="Keine Übereinstimmung bei aktivierten Engines"::: 

## <a name="define-sensor-backup-schedules"></a>Definieren von Zeitplänen für Sensorsicherungen

Über die lokale Verwaltungskonsole können Sie Sensorsicherungen planen und bedarfsgesteuerte Sensorsicherungen ausführen. Dies trägt zum Schutz vor Festplattenausfällen und Datenverlusten bei.

- Gesicherte Elemente: Konfigurationen und Daten.

- Nicht gesicherte Elemente: PCAP-Dateien und -Protokolle. Sie können PCAP-Dateien und -Protokolle manuell sichern und wiederherstellen.

Standardmäßig werden Sensoren automatisch täglich um 3:00 Uhr morgens gesichert. Mit dem Sicherungszeitplanfeature für Sensoren können Sie diese Sicherungen sammeln und in der lokalen Verwaltungskonsole oder auf einem externen Sicherungsserver speichern. Das Kopieren der Dateien von Sensoren in die lokale Verwaltungskonsole erfolgt über einen verschlüsselten Kanal.

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-backup-schedule-screen.png" alt-text="Ansicht des Bildschirms zur Sensorsicherung":::

Wenn der Standardspeicherort für die Sensorsicherung geändert wird, ruft die lokale Verwaltungskonsole die Dateien automatisch vom neuen Speicherort auf dem Sensor oder einem externen Speicherort ab, vorausgesetzt, dass die Konsole über die Zugriffsberechtigung für den Speicherort verfügt. 

Wenn die Sensoren nicht bei der lokalen Verwaltungskonsole registriert sind, ist im Dialogfeld **Sensorsicherungszeitplan** angegeben, dass keine Sensoren verwaltet werden.  

Der Wiederherstellungsvorgang ist unabhängig davon, wo die Dateien gespeichert sind, immer gleich.

### <a name="backup-storage-for-sensors"></a>Sicherungsspeicher für Sensoren

Mithilfe der lokalen Verwaltungskonsole können Sie bis zu neun Sicherungen für jeden verwalteten Sensor aufbewahren, vorausgesetzt, dass die gesicherten Dateien den maximal zugeordneten Sicherungsspeicherplatz nicht überschreiten. 

Der verfügbare Speicherplatz wird basierend auf dem Verwaltungskonsolenmodell berechnet, mit dem Sie arbeiten: 

- **Produktionsmodell**: Der Standardspeicher beträgt 40 GB. Der Grenzwert liegt bei 100 GB. 

- **Mittleres Modell**: Der Standardspeicher beträgt 20 GB. Der Grenzwert liegt bei 50 GB. 

- **Laptop-Modell**: Der Standardspeicher beträgt 10 GB. Der Grenzwert liegt bei 25 GB. 

- **Dünnes Modell**: Der Standardspeicher beträgt 2 GB. Der Grenzwert liegt bei 4 GB. 

- **Robustes Modell**: Der Standardspeicher beträgt 10 GB. Der Grenzwert liegt bei 25 GB. 

Die Standardzuordnung wird im Dialogfeld **Sensorsicherungszeitplan** angezeigt. 

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-mail-server-configuration.png" alt-text="Bildschirm zum Bearbeiten der Mailserverkonfiguration":::

Bei der Sicherung auf einem externen Server besteht keine Speicherbeschränkung. Sie müssen jedoch eine Obergrenze für die Zuordnung im Feld **Sensorsicherungszeitplan** > **Benutzerdefinierter Pfad** definieren. Die folgenden Zahlen und Zeichen werden unterstützt: `/, a-z, A-Z, 0-9, and _`. 

Nachfolgend sind Informationen zur Überschreitung der Grenzwerte für die Zuordnung von Speicher aufgeführt:

- Wenn Sie den zugeordneten Speicherplatz überschreiten, wird der Sensor nicht gesichert. 

- Wenn Sie mehrere Sensoren sichern, versucht die Verwaltungskonsole, Sensordateien für die verwalteten Sensoren abzurufen.  

- Wenn das Abrufen von einem Sensor den Grenzwert überschreitet, versucht die Verwaltungskonsole, Sicherungsinformationen vom nächsten Sensor abzurufen. 

Wenn Sie die definierte Anzahl beibehaltener Sicherungen überschreiten, wird die älteste gesicherte Datei gelöscht, um Platz für die neue zu schaffen.

Sensorsicherungsdateien werden automatisch im folgenden Format benannt: `<sensor name>-backup-version-<version>-<date>.tar`. Beispiel: `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`. 

Zum Sichern von Sensoren gehen Sie folgendermaßen vor:

1. Wählen Sie im Fenster **Systemeinstellungen** die Option **Sensorsicherung planen** aus. Sensoren, die von der lokalen Verwaltungskonsole verwaltet werden, werden im Dialogfeld **Sensorsicherungszeitplan** angezeigt.  

2. Aktivieren Sie den Umschalter **Sicherungen sammeln**.  

3. Wählen Sie ein Zeitintervall, ein Datum und eine Zeitzone aus. Das Zeitformat basiert auf einem 24-Stunden-Format. Geben Sie beispielsweise 6:00 Uhr abends als **18:00** ein. 

4. Geben Sie im Feld **Sicherungsspeicherzuordnung** den Speicher ein, den Sie für die Sicherungen zuordnen möchten. Sie werden benachrichtigt, wenn Sie den maximalen Speicherplatz überschreiten.

5. Geben Sie im Feld **Letzte beibehalten** die Anzahl der Sicherungen pro Sensor an, die Sie beibehalten möchten. Wenn der Grenzwert überschritten wird, wird die älteste Sicherung gelöscht.  

6. Wählen Sie einen Sicherungsspeicherort aus:  

   - Zum Sichern in der lokalen Verwaltungskonsole deaktivieren Sie den Umschalter **Benutzerdefinierter Pfad**. Der Standardspeicherort ist `/var/cyberx/sensor-backups`.  

   - Zum Sichern auf einem externen Server aktivieren Sie den Umschalter **Benutzerdefinierter Pfad**, und geben Sie einen Speicherort ein. Die folgenden Zahlen und Zeichen werden unterstützt: `/, a-z, A-Z, 0-9, and, _`. 

7. Wählen Sie **Speichern** aus. 

Zum sofortigen Sichern gehen Sie folgendermaßen vor: 

- Wählen Sie **Jetzt sichern** aus. Die lokale Verwaltungskonsole erstellt und sammelt Sensorsicherungsdateien. 

### <a name="receiving-backup-notifications-for-sensors"></a>Empfangen von Sicherungsbenachrichtigungen für Sensoren 

Im Dialogfeld **Sensorsicherungszeitplan** und im Sicherungsprotokoll werden automatisch Informationen zu erfolgreichen und fehlerhaften Sicherungen aufgelistet.  

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-location.png" alt-text="Anzeigen der Sensoren, der Standorte und aller relevanten Informationen":::

Fehler können aus folgenden Gründen auftreten:    

- Es wird keine Sicherungsdatei gefunden. 

- Eine Datei wurde gefunden, kann aber nicht abgerufen werden.  

- Es ist ein Fehler bei der Netzwerkverbindung aufgetreten. 

- Der lokalen Verwaltungskonsole ist nicht genügend Speicherplatz zugeordnet, um die Sicherung abzuschließen.  

Wenn ein Fehler auftritt, können Sie eine E-Mail-Benachrichtigung, Syslog-Updates und Systembenachrichtigungen senden. Dazu erstellen Sie eine Weiterleitungsregel unter **Systembenachrichtigungen**. 

### <a name="restoring-sensors"></a>Wiederherstellen von Sensoren 

Sie können Sicherungen über die lokale Verwaltungskonsole und mithilfe der CLI wiederherstellen.  

Zum Wiederherstellen über die Konsole gehen Sie folgendermaßen vor: 

- Wählen Sie im Einstellungsfenster **Sensorsystem** die Option **Abbild wiederherstellen** aus.

  :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/restore.png" alt-text="Wiederherstellen einer Sicherung des Abbilds":::

  In der Konsole werden dann Wiederherstellungsfehler angezeigt.  

Zum Wiederherstellen mithilfe der CLI gehen Sie folgendermaßen vor: 

- Melden Sie sich bei einem Administratorkonto an, und geben Sie `$ sudo cyberx-xsense-system-restore` ein. 

### <a name="save-a-sensor-backup-to-an-external-smb-server"></a>Speichern einer Sensorsicherung auf einem externen SMB-Server

Zum Einrichten eines SMB-Servers für das Speichern einer Sensorsicherung auf einem externen Laufwerk gehen Sie folgendermaßen vor: 

1. Erstellen Sie einen freigegebenen Ordner auf dem externen SMB-Server. 

2. Rufen Sie den Ordnerpfad, den Benutzernamen und das Kennwort ab, die für den Zugriff auf den SMB-Server erforderlich sind. 

3. Erstellen Sie in Defender für IoT ein Verzeichnis für die Sicherungen: 

   `sudo mkdir /<backup_folder_name_on_server>` 

   `sudo chmod 777 /<backup_folder_name_on_server>/` 

4. Bearbeiten Sie „fstab“:  

   `sudo nano /etc/fstab` 

   `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

5. Bearbeiten oder erstellen Sie die Anmeldeinformationen für die Freigabe. Dies sind die Anmeldeinformationen für den SMB-Server: 

   `sudo nano /etc/samba/user` 

6. Hinzufügen:  

   `username=<user name>` 

   `password=<password>` 

7. Stellen Sie das Verzeichnis bereit: 

   `sudo mount -a` 

8. Konfigurieren Sie ein Sicherungsverzeichnis für den freigegebenen Ordner auf dem Defender für IoT-Sensor:  

   `sudo nano /var/cyberx/properties/backup.properties` 

9. Legen Sie `Backup.shared_location` auf `<backup_folder_name_on_cyberx_server>` fest.

## <a name="next-steps"></a>Nächste Schritte

[Verwalten einzelner Sensoren](how-to-manage-individual-sensors.md)

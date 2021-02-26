---
title: Behandeln von Problemen mit dem Sensor und der lokalen Verwaltungskonsole
description: Beheben Sie Probleme mit dem Sensor und der lokalen Verwaltungskonsole, um möglicherweise bestehende Probleme zu beseitigen.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/3/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 624f60441f56646c3633a2afa79d326c92ba1a37
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523718"
---
# <a name="troubleshoot-the-sensor-and-on-premises-management-console"></a>Behandeln von Problemen mit dem Sensor und der lokalen Verwaltungskonsole

In diesem Artikel werden die grundlegenden Tools zur Problembehandlung für den Sensor und die lokale Verwaltungskonsole beschrieben. Zusätzlich zu den hier beschriebenen Elementen können Sie die Integrität Ihres Systems wie folgt überprüfen:

**Warnungen**: Eine Warnung wird erstellt, wenn die Sensorschnittstelle, die den Datenverkehr überwacht, nicht mehr angezeigt wird. 

**SNMP**: Die Sensorintegrität wird über SNMP überwacht. Azure Defender für IoT antwortet auf SNMP-Abfragen, die von einem autorisierten Überwachungsserver gesendet wurden. 

**Systembenachrichtigungen**: Wenn der Sensor von einer Verwaltungskonsole gesteuert wird, können Sie Warnungen über fehlgeschlagene Sensorsicherungen und getrennte Sensoren weiterleiten.

## <a name="sensor-troubleshooting-tools"></a>Tools für die Sensorproblembehandlung

### <a name="investigate-password-failure-at-initial-sign-in"></a>Untersuchen von Kennwortfehlern bei der anfänglichen Anmeldung

Wenn Sie sich zum ersten Mal bei einem vorkonfigurierten Pfeilsensor anmelden, müssen Sie eine Kennwortwiederherstellung ausführen.

So stellen Sie Ihr Kennwort wieder her:

1. Wählen Sie auf dem Anmeldebildschirm von Defender für IoT **Password Recovery** (Kennwortwiederherstellung) aus. Der Bildschirm **Password Recovery** (Kennwortwiederherstellung) wird geöffnet.

1. Wählen Sie entweder **CyberX** oder **Support** aus, und kopieren Sie den eindeutigen Bezeichner.

1. Navigieren Sie zum Azure-Portal, und wählen Sie **Sites und Sensoren** aus.  

1. Klicken Sie auf die Registerkarte **Recover on-premises management console password** (Kennwort der lokalen Verwaltungskonsole wiederherstellen).

   :::image type="content" source="media/password-recovery-images/recover-button.png" alt-text="Wählen Sie die Schaltfläche „Recover on-premises management“ (Lokale Verwaltung wiederherstellen) aus, um die Wiederherstellungsdatei herunterzuladen.":::

1. Geben Sie den eindeutigen Bezeichner ein, der auf dem Bildschirm **Password Recovery** (Kennwortwiederherstellung) angezeigt wurde, und wählen Sie **Recover** (Wiederherstellen) aus. Die Datei `password_recovery.zip` wird heruntergeladen.

    > [!NOTE]
    > Ändern Sie die Kennwortwiederherstellungsdatei nicht. Es handelt sich um eine signierte Datei, die nicht funktioniert, wenn Sie sie ändern.

1. Wählen Sie auf dem Bildschirm **Password Recovery** (Kennwortwiederherstellung) die Option **Upload** (Hochladen) aus. Das Fenster **Upload Password Recovery File** (Kennwortwiederherstellungsdatei hochladen) wird geöffnet.

1. Wählen Sie **Durchsuchen** aus, um nach Ihrer Datei `password_recovery.zip` zu suchen, oder ziehen Sie die Datei `password_recovery.zip` in das Fenster.

1. Wählen Sie **Weiter** aus. Ihr Benutzer und das vom System generierte Kennwort für Ihre Verwaltungskonsole werden angezeigt.

    > [!NOTE]
    > Wenn Sie sich zum ersten Mal bei einem Sensor oder einer lokalen Verwaltungskonsole anmelden, wird er bzw. sie mit dem Abonnement verknüpft, mit dem Sie eine Verbindung hergestellt haben. Wenn Sie das Kennwort für das den Benutzer „CyberX“ oder „Support“ zurücksetzen müssen, müssen Sie dieses Abonnement auswählen. Weitere Informationen zum Wiederherstellen eines CyberX- oder Support-Kennworts finden Sie unter [Zurücksetzen eines Benutzerkennworts für den Sensor oder die lokale Verwaltungskonsole](how-to-create-and-manage-users.md#resetting-a-users-password-for-the-sensor-or-on-premises-management-console).

### <a name="investigate-a-lack-of-traffic"></a>Untersuchen eines fehlenden Datenverkehrs

Oben in der Konsole erscheint ein Hinweis, wenn der Sensor erkennt, dass an einem der konfigurierten Ports kein Datenverkehr vorhanden ist. Dieser Hinweis ist für alle Benutzer sichtbar.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/no-traffic-detected.png" alt-text="Screenshot der Warnung, dass kein Datenverkehr erkannt wurde.":::
 
Wenn diese Meldung angezeigt wird, können Sie untersuchen, wo kein Datenverkehr vorhanden ist. Vergewissern Sie sich, dass das SPAN-Kabel angeschlossen ist und es keine Änderung in der SPAN-Architektur gab.  

Informationen zum Support und zur Problembehandlung erhalten Sie vom [Microsoft-Support](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

### <a name="check-system-performance"></a>Überprüfen der Systemleistung 

Wenn ein neuer Sensor bereitgestellt wird oder der Sensor z. B. langsam reagiert oder keine Warnungen anzeigt, sollten Sie die Systemleistung überprüfen.

So überprüfen Sie die Systemleistung:

1. Stellen Sie sicher, dass im Dashboard `PPS > 0` angezeigt wird.

   :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/dashboard-view-v2.png" alt-text="Screenshot eines Beispieldashboards."::: 

1. Wählen Sie im Seitenmenü **Geräte** aus.

1. Überprüfen Sie im Fenster **Geräte**, ob Geräte erkannt werden.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/discovered-devices.png" alt-text="Sicherstellen, dass Geräte erkannt werden.":::

1. Wählen Sie im Seitenmenü **Data Mining** aus.

1. Wählen Sie im Fenster **Data Mining** die Option **ALLE** aus, und generieren Sie einen Bericht.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="Generieren eines neuen Berichts mithilfe von Data Mining.":::

1. Stellen Sie sicher, dass der Bericht Daten enthält.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="Sicherstellen, dass der Bericht Daten enthält.":::

1. Wählen Sie im Seitenmenü die Option **Trends & Statistics** (Trends und Statistik) aus.

1. Wählen Sie im Fenster **Trends & Statistics** (Trends und Statistik) die Option **Widget hinzufügen** aus.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/add-widget.png" alt-text="Hinzufügen eines Widgets durch Auswahl.":::

1. Fügen Sie ein Widget hinzu, und stellen Sie sicher, dass es Daten anzeigt.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/widget-data.png" alt-text="Sicherstellen, dass im Widget Daten angezeigt werden.":::

1. Wählen Sie im Seitenmenü **Warnungen** aus. Das Fenster **Warnungen** wird angezeigt.

1. Stellen Sie sicher, dass die Warnungen erstellt wurden.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alerts-created.png" alt-text="Sicherstellen, dass die Warnungen erstellt wurden.":::


### <a name="investigate-a-lack-of-expected-alerts"></a>Untersuchen fehlender erwarteter Warnungen

Wenn im Fenster **Warnungen** keine von Ihnen erwartete Warnung angezeigt wird, überprüfen Sie Folgendes:

- Überprüfen Sie, ob dieselbe Warnung bereits im Fenster **Warnungen** als Reaktion auf eine andere Sicherheitsinstanz angezeigt wird. Wenn ja, und diese Warnung noch nicht verarbeitet wurde, zeigt die Sensorkonsole keine neue Warnung an.

- Vergewissern Sie sich, dass Sie diese Warnung nicht in der Verwaltungskonsole mithilfe der Regeln für **Alert Exclusion** (Warnungsausschluss) ausgeschlossen haben. 

### <a name="investigate-widgets-that-show-no-data"></a>Untersuchen von Widgets, die keine Daten anzeigen

Wenn die Widgets im Fenster **Trends & Statistics** (Trends und Statistik) keine Daten anzeigen, gehen Sie wie folgt vor:

- [Überprüfen Sie die Systemleistung.](#check-system-performance)

- Stellen Sie sicher, dass die Einstellungen für Zeit und Region ordnungsgemäß konfiguriert sind und nicht auf einen späteren Zeitpunkt festgelegt sind. 

### <a name="investigate-a-device-map-that-shows-only-broadcasting-devices"></a>Untersuchen einer Gerätezuordnung, in der nur Broadcastgeräte angezeigt werden

Wenn Geräte, die in der Zuordnung angezeigt werden, nicht miteinander verbunden sind, liegt bei der SPAN-Portkonfiguration möglicherweise ein Fehler vor. Das heißt, Sie sehen ggf. nur die Broadcastgeräte und keinen Unicastdatenverkehr.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/broadcasting-devices.png" alt-text="Anzeigen der Broadcastgeräte.":::

In einem solchen Fall müssen Sie überprüfen, ob nur der Broadcastdatenverkehr angezeigt wird. Bitten Sie den Netzwerktechniker dann, die SPAN-Portkonfiguration zu korrigieren, damit Sie den Unicastdatenverkehr sehen können.

So überprüfen Sie, ob nur der Broadcastdatenverkehr angezeigt wird:

- Erstellen Sie auf dem Bildschirm **Data Mining** einen Bericht mithilfe der Option **Alle**. Überprüfen Sie dann, ob nur Broadcast- und Multicastdatenverkehr (und kein Unicastdatenverkehr) im Bericht angezeigt wird.

Oder:

- Zeichnen Sie direkt vom Switch eine PCAP-Datei auf, oder verbinden Sie einen Laptop mithilfe von Wireshark.

### <a name="connect-the-sensor-to-ntp"></a>Verbinden des Sensors mit NTP

Sie können einen eigenständigen Sensor und eine Verwaltungskonsole mit den dazugehörigen Sensoren konfigurieren, um eine Verbindung mit NTP herzustellen.

So verbinden Sie einen eigenständigen Sensor mit NTP:

- [Wenden Sie sich an das Supportteam, um Hilfe zu erhalten.](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)

So verbinden Sie einen über die Verwaltungskonsole gesteuerten Sensor mit NTP:

- Die Verbindung mit NTP wird in der Verwaltungskonsole konfiguriert. Allen Sensoren, die über die Verwaltungskonsole gesteuert werden, wird die NTP-Verbindung automatisch zugewiesen.

### <a name="investigate-when-devices-arent-shown-on-the-map-or-you-have-multiple-internet-related-alerts"></a>Untersuchen, wenn Geräte nicht in der Zuordnung angezeigt werden oder über mehrere internetbezogene Warnungen verfügen

Manchmal werden ICS-Geräte mit externen IP-Adressen konfiguriert. Diese ICS-Geräte werden nicht in der Zuordnung angezeigt. Anstelle der Geräte wird eine Webcloud in der Zuordnung angezeigt. Die IP-Adressen dieser Geräte sind im Cloudimage enthalten.

Ein weiterer Hinweis auf dasselbe Problem ist, dass mehrere internetbezogene Warnungen angezeigt werden.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alert-problems.png" alt-text="Mehrere internetbezogene Warnungen.":::

So beheben Sie den Konfigurationsfehler:

1. Klicken Sie in der Gerätezuordnung mit der rechten Maustaste auf das Cloudsymbol, und wählen Sie **IP-Adressen exportieren** aus. Kopieren Sie die öffentlichen Bereiche, die privat sind, und fügen Sie sie der Subnetzliste hinzu. Weitere Informationen finden Sie unter [Configure subnets](how-to-control-what-traffic-is-monitored.md#configure-subnets) (Konfigurieren von Subnetzen).

1. Generieren Sie einen neuen Data Mining-Bericht für Internetverbindungen.

1. Wählen Sie im Data Mining-Bericht :::image type="icon" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/administrator-mode.png" border="false"::: aus, um in den Administratormodus zu wechseln und die IP-Adressen der ICS-Geräte zu löschen.

### <a name="tweak-the-sensors-quality-of-service"></a>Optimieren der Dienstqualität des Sensors

Um Ihre Netzwerkressourcen zu speichern, können Sie die Schnittstellenbandbreite einschränken, die der Sensor für alltägliche Prozeduren verwendet.

Um die Bandbreite der Schnittstelle einzuschränken, verwenden Sie das CLI-Tool `cyberx-xsense-limit-interface`, das mit sudo-Berechtigungen ausgeführt werden muss. Das Tool ruft die folgenden Argumente ab:

  - `* -i`: Schnittstellen (Beispiel: eth0).

  - `* -l`: Limit (Beispiel: 30 KBit/1 MBit). Sie können die folgenden Bandbreiteneinheiten verwenden: KBit/s, MBit/s, KBit, MBit oder Bit/s.

  - `* -c`: Löschen (um die Einschränkung der Schnittstellenbandbreite zu löschen).

So optimieren Sie die Dienstqualität:

1. Melden Sie sich an der Sensor-CLI als Defender für IoT-Benutzer an, und geben Sie `sudo cyberx-xsense-limit-interface-I eth0 -l value` ein.

   Beispiel: `sudo cyberx-xsense-limit-interface -i eth0 -l 30kbit`

   > [!NOTE]
   > Verwenden Sie für ein physisches Gerät die em1-Schnittstelle.

1. Geben Sie `sudo cyberx-xsense-limit-interface -i eth0 -l 1mbps -c` ein, um die Schnittstelleneinschränkung zu löschen.

## <a name="on-premises-management-console-troubleshooting-tools"></a>Tools zur Problembehandlung für die lokale Verwaltungskonsole

### <a name="investigate-a-lack-of-expected-alerts"></a>Untersuchen fehlender erwarteter Warnungen

Wird eine erwartete Warnung im Fenster **Warnungen** nicht angezeigt, überprüfen Sie Folgendes:

- Überprüfen Sie, ob dieselbe Warnung bereits im Fenster **Warnungen** als Reaktion auf eine andere Sicherheitsinstanz angezeigt wird. Wenn ja, und diese Warnung noch nicht verarbeitet wurde, wird keine neue Warnung angezeigt.

- Vergewissern Sie sich, dass Sie diese Warnung nicht in der lokalen Verwaltungskonsole mithilfe der Regeln für **Alert Exclusion** (Warnungsausschluss) ausgeschlossen haben.  

### <a name="tweak-the-quality-of-service"></a>Optimieren der Dienstqualität

Um Netzwerkressourcen zu sparen, können Sie die Anzahl der an externe Systeme (z. B. per E-Mail oder SIEM) gesendeten Warnungen auf einen Synchronisierungsvorgang zwischen einer Appliance und der lokalen Verwaltungskonsole eingrenzen.

Der Standardwert ist 50. Das bedeutet, dass in einer Kommunikationssitzung einer Appliance mit der lokalen Verwaltungskonsole nicht mehr als 50 Warnungen an externe Systeme ausgegeben werden. 

Um die Anzahl der Warnungen einzuschränken, verwenden Sie die `notifications.max_number_to_report`-Eigenschaft, die in `/var/cyberx/properties/management.properties`verfügbar ist. Nachdem Sie diese Eigenschaft geändert haben, ist kein Neustart erforderlich.

So optimieren Sie die Dienstqualität:

1. Melden Sie sich als Defender für IoT-Benutzer an. 

1. Überprüfen Sie die Standardwerte:

   ```bash
   grep \"notifications\" /var/cyberx/properties/management.properties
   ```

   Die folgenden Standardwerte werden angezeigt:

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

1. Ändern Sie die Standardeinstellungen:

   ```bash
   sudo nano /var/cyberx/properties/management.properties
   ```

1. Ändern Sie die Einstellungen der folgenden Zeilen:

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

1. Speichern Sie die Änderungen. Es ist kein Neustart erforderlich.

## <a name="export-information-for-troubleshooting"></a>Exportieren von Informationen zur Problembehandlung

Zusätzlich zu den Tools zur Überwachung und Analyse Ihres Netzwerks können Sie Informationen zur weiteren Untersuchung an das Supportteam senden. Beim Exportieren von Protokollen generiert der Sensor automatisch ein Einmalkennwort, das für die exportierten Protokolle eindeutig ist, in einer separaten Textdatei. 

So exportieren Sie Protokolle:

1. Wählen Sie im linken Bereich **Systemeinstellungen** aus.

1. Wählen Sie **Protokolle exportieren**.

    :::image type="content" source="media/how-to-export-information-for-troubleshooting/export-a-log.png" alt-text="Exportieren Sie ein Protokoll für den Systemsupport.":::

1. Geben Sie im Feld **Dateiname** den Dateinamen ein, den Sie für den Protokollexport verwenden möchten. Als Standardeinstellung wird das aktuelle Datum festgelegt.

1. Wählen Sie die Datenkategorien aus, um festzulegen, welche Daten Sie exportieren möchten:  

    | Exportkategorie | BESCHREIBUNG |
    |--|--|
    | **Protokolle des Betriebssystems** | Wählen Sie diese Option aus, um Informationen zum Betriebssystem Status zu erhalten. |
    | **Installations-/Upgradeprotokolle** | Wählen Sie diese Option aus, um die Konfigurationsparameter für die Installation und Upgrades zu untersuchen. |
    | **Systemintegritätsausgabe** | Wählen Sie diese Option aus, um die Systemleistung zu prüfen. |
    | **Sektionsprotokolle** | Wählen Sie diese Option aus, um die erweiterte Überprüfung der Protokollsektion zuzulassen. |
    | **Betriebssystem-Kernelabbilder** | Wählen Sie diese Option aus, um das Kernelspeicherabbild zu exportieren. Ein Kernelspeicherabbild umfasst den gesamten Arbeitsspeicher, der vom Kernel zum Zeitpunkt des in diesem Kernel aufgetretenen Problems verwendet wird. Die Größe der Sicherungsdatei ist kleiner als das gesamte Speicherabbild. Normalerweise ist die Sicherungsdatei etwa ein Drittel so groß wie der physische Speicher des Systems. |
    | **Weiterleitungsprotokolle** | Wählen Sie diese Option aus, um die Weiterleitungsregeln zu untersuchen. |
    | **SNMP-Protokolle** | Wählen Sie diese Option aus, um Informationen zur SNMP-Integritätsprüfung zu empfangen. |
    | **Kernanwendungsprotokolle** | Wählen Sie diese Option aus, um Daten über die Konfiguration und den Betrieb der Kernanwendung zu exportieren. |
    | **Kommunikation mit CM-Protokollen** | Wählen Sie diese Option aus, wenn es fortlaufende Probleme oder Unterbrechungen der Verbindung mit der Verwaltungskonsole gibt. |
    | **Webanwendungsprotokolle** | Wählen Sie diese Option aus, um Informationen zu allen Anforderungen zu erhalten, die von der Webschnittstelle der Anwendung gesendet werden. |
    | **Systemsicherung** | Wählen Sie diese Option aus, um eine Sicherung aller Systemdaten zu exportieren, um den genauen Zustand des Systems zu untersuchen. |
    | **Sektionsstatistiken** | Wählen Sie diese Option aus, um die erweiterte Überprüfung der Protokollstatistiken zuzulassen. |
    | **Datenbankprotokolle** | Wählen Sie diese Option aus, um Protokolle aus der Systemdatenbank zu exportieren. Die Untersuchung von Systemprotokollen hilft bei der Identifizierung von Systemproblemen. |
    | **Configuration** | Wählen Sie diese Option aus, um Informationen zu allen konfigurierbaren Parametern zu exportieren und sicherzustellen, dass alles ordnungsgemäß konfiguriert wurde. |

1. Wenn Sie alle Optionen auswählen möchten, wählen Sie neben **Kategorien auswählen** die Option **Alle auswählen** aus.

1. Wählen Sie **Protokolle exportieren**.

Die exportierten Protokolle werden der Liste **Archived Logs** (Archivierte Protokolle) hinzugefügt. Senden Sie das Einmalkennwort in einer separaten Nachricht und einem Medium aus den exportierten Protokollen an das Supportteam. Das Supportteam kann exportierte Protokolle nur mithilfe des eindeutigen Einmalkennworts extrahieren, das zum Verschlüsseln der Protokolle verwendet wird.

Die Liste der archivierten Protokolle kann bis zu fünf Elemente enthalten. Wenn die Anzahl der Elemente in der Liste über diese Zahl hinausgeht, wird das älteste Element gelöscht.

## <a name="next-steps"></a>Nächste Schritte

- [Anzeigen von Warnungen](how-to-view-alerts.md)

- [Einrichten der SNMP-MIB-Überwachung](how-to-set-up-snmp-mib-monitoring.md)

- [Understand sensor disconnection events](how-to-manage-sensors-from-the-on-premises-management-console.md#understand-sensor-disconnection-events) (Grundlegendes zu Ereignissen zur Trennung der Sensorverbindung)

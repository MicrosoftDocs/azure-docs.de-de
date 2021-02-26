---
title: Steuern, welcher Datenverkehr überwacht wird
description: Sensoren führen automatisch eine umfassende Paketerkennung für IT- und OT-Datenverkehr durch und lösen Informationen über Netzwerkgeräte auf, wie etwa Geräteattribute und Netzwerkverhalten. Es stehen verschiedene Tools zur Verfügung, um den Typ von Datenverkehr zu steuern, der von den einzelnen Sensoren erkannt wird.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: a7af817074400bedd8f07adaefc35309566dc931
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522392"
---
# <a name="control-what-traffic-is-monitored"></a>Steuern, welcher Datenverkehr überwacht wird

Sensoren führen automatisch eine umfassende Paketerkennung für IT- und OT-Datenverkehr durch und lösen Informationen über Netzwerkgeräte auf, wie etwa Geräteattribute und -verhalten. Es stehen verschiedene Tools zur Verfügung, um den Typ von Datenverkehr zu steuern, der von den einzelnen Sensoren erkannt wird.

## <a name="learning-and-smart-it-learning-modes"></a>Lernmodus und Smart IT-Lernmodus

Der Lernmodus weist Ihren Sensor an, die übliche Aktivität Ihres Netzwerks zu erlernen. Beispiele hierfür sind Geräte, die in Ihrem Netzwerk ermittelt werden, im Netzwerk erkannte Protokolle, Dateiübertragungen zwischen bestimmten Geräten usw. Diese Aktivität wird zur Baseline Ihres Netzwerks.

Der Lernmodus ist nach der Installation automatisch aktiviert und bleibt aktiviert, bis er ausgeschaltet wird. Der ungefähre Zeitraum für den Lernmodus beträgt zwischen zwei und sechs Wochen, abhängig von der Komplexität und Größe des Netzwerks. Wenn der Lernmodus nach diesem Zeitraum deaktiviert wird, lösen alle neu erkannten Aktivitäten Benachrichtigungen aus. Benachrichtigungen werden ausgelöst, wenn die Richtlinienengine Abweichungen von der erlernten Baseline erkennt.

Nach dem Abschluss des Lernzeitraums und dem Deaktivieren des Lernmodus stellt der Sensor möglicherweise ein ungewöhnlich hohes Niveau an Baselineänderungen fest, die das Ergebnis normaler IT-Aktivitäten sind, wie etwa DNS- und HTTP-Anforderungen. Die Aktivität wird als nicht deterministisches IT-Verhalten bezeichnet. Dieses Verhalten kann außerdem unnötige Warnungen wegen Richtlinienverletzung und Systembenachrichtigungen auslösen. Zum Verringern der Anzahl dieser Warnungen und Benachrichtigungen können Sie die Funktion **Smart IT Learning** (Intelligentes IT-Lernen) aktivieren.

Wenn Smart IT Learning aktiviert ist, verfolgt der Sensor auf der Grundlage spezifischer Benachrichtigungsszenarien Datenverkehr nach, der nicht deterministisches IT-Verhalten hervorruft.

Dieser Datenverkehr wird vom Sensor sieben Tage lang überwacht. Wenn innerhalb dieser sieben Tage der gleiche nicht deterministische IT-Datenverkehr erkannt wird, fährt der Monitor weitere sieben Tage lang mit der Überwachung des Datenverkehrs fort. Wenn der Datenverkehr volle sieben Tage lang nicht erkannt wird, wird Smart IT Learning für das betreffende Szenario deaktiviert. Neuer für dieses Szenario erkannte Datenverkehr löst nur in diesem Fall Warnungen und Benachrichtigungen aus.

Durch das Arbeiten mit Smart IT Learning können Sie die Anzahl der von rauschbehafteten IT-Szenarien verursachten unnötigen Warnungen und Benachrichtigungen reduzieren.

Wenn Ihr Sensor durch die lokale Verwaltungskonsole gesteuert wird, können Sie die Lernmodi nicht deaktivieren. In derartigen Fällen kann der Lernmodus nur in der Verwaltungskonsole deaktiviert werden.

Die Lernfunktionen (Learning und Smart IT Learning) sind standardmäßig aktiviert.

So aktivieren oder deaktivieren Sie den Lernmodus:

- Wählen Sie **System Settings** (Systemeinstellungen) aus, und schalten Sie die Optionen **Learning** (Lernen) und **Smart IT Learning** (Intelligentes IT-Lernen) ein oder aus.

:::image type="content" source="media/concept-learning-modes/toggle-options-for-learning-and-smart-it-learning.png" alt-text="Bildschirm zum Umschalten der Systemeinstellungen.":::

## <a name="configure-subnets"></a>Konfigurieren von Subnetzen

Die Konfiguration von Subnetzen hat Einfluss darauf, wie Geräte in der Geräteübersicht angezeigt werden.

Standardmäßig erkennt der Sensor Ihr Subnetzsetup und füllt das Dialogfeld **Subnet Configuration** (Subnetzkonfiguration) mit diesen Informationen auf.

Damit der Schwerpunkt auf den OT-Geräten betont wird, werden IT-Geräte in der Geräteübersicht automatisch nach Subnetz aggregiert. Jedes Subnetz wird in der Übersicht als einzelne Entität dargestellt, einschließlich einer interaktiven Funktion zum Aufklappen/Zuklappen, die den Drilldown in die IT-Subnetze und die Rückkehr ermöglicht.

Wählen Sie beim Arbeiten mit Subnetzen die ICS-Subnetze aus, um die OT-Subnetze zu identifizieren. Dadurch können Sie den Schwerpunkt in der Übersicht auf OT- und ICS-Netzwerke legen und die Darstellung der IT-Netzwerkelemente zu einem Minimum reduzieren. Durch diese Mühe reduziert sich die Gesamtzahl der in der Übersicht dargestellten Geräte, und es ergibt sich ein klares Bild der OT- und ICS-Netzwerkelemente.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/expand-network-option.png" alt-text="Screenshot zum Filtern in einem Netzwerk":::

Sie können die Konfiguration ändern oder die Subnetzinformationen ändern, indem Sie die ermittelten Daten manuell exportieren, sie manuell bearbeiten und dann die Liste der von Ihnen manuell definierten Subnetze wieder importieren. Weitere Informationen zum Importieren und Exportieren finden Sie unter [Importieren von Geräteinformationen](how-to-import-device-information.md).

In manchen Fällen, etwa in Umgebungen, die öffentliche Bereiche als interne Bereiche verwenden, können Sie den Sensor anweisen, alle Subnetze als interne Subnetze aufzulösen, indem Sie die Option **Do Not Detect Internet Activity** (Keine Internetaktivitäten ermitteln) aktivieren. In diesen Fällen sollten Sie diese Option auswählen:

- Öffentliche IP-Adressen werden als lokale Adressen behandelt.

- Es werden keine Warnungen über unberechtigte Internetaktivitäten gesendet, was die Anzahl der an externen Adressen empfangenen Benachrichtigungen und Warnungen reduziert.

So konfigurieren Sie Subnetze:

1. Wählen Sie im Menü an der Seite **System Settings** (Systemeinstellungen) aus.

2. Wählen Sie im Fenster **System Setting** (Systemeinstellungen) **Subnets** (Subnetze) aus.

   :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-subnets-configuration-screen.png" alt-text="Screenshot, der den Subnetz-Konfigurationsbildschirm zeigt."::: 

3. Wenn bei der Ermittlung neuer Geräte automatisch Subnetze hinzugefügt werden sollen, lassen Sie **Auto Subnets Learning** (Automatisches Lernen von Subnetzen) aktiviert.

4. Wenn alle Subnetze als interne Subnetze aufgelöst werden sollen, aktivieren Sie **Don't Detect Internet Activity** (Keine Internetaktivitäten ermitteln).

5. Wählen Sie **Add network** (Netzwerk hinzufügen) aus, und definieren Sie die folgenden Parameter für jedes Subnetz:

    - Die Subnetz-IP-Adresse.
    - Die Subnetzmaskenadresse.
    - Den Subnetznamen. Wir empfehlen Ihnen, jedes Subnetz mit einem aussagekräftigen Namen zu benennen, den Sie leicht identifizieren können, damit Sie zwischen IT- und OT-Netzwerken unterscheiden können. Der Name kann bis zu 60 Zeichen lang sein.

6. Zum Markieren eines Subnetzes als OT-Subnetz wählen Sie **ICS-Subnet** aus.

7. Um das Subnetz separat anzuzeigen, wenn Sie die Übersicht nach der Purdue-Ebene anzeigen, wählen Sie **Segregated** (Isoliert) aus.

8. Zum Löschen eines Subnetzes wählen Sie :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/delete-icon.png" border="false"::: aus.

9. Zum Löschen aller Subnetze wählen Sie **Clear All** (Alle löschen) aus.

10. Zum Exportieren der konfigurierten Subnetze wählen Sie **Export** (Exportieren) aus. Die Subnetztabelle wird auf Ihre Arbeitsstation heruntergeladen.

11. Wählen Sie **Speichern** aus.

### <a name="importing-information"></a>Importieren von Informationen 

Wählen Sie zum Importieren von Subnetzinformationen **Import** (Importieren) und dann eine zu importierende CSV-Datei aus. Die Subnetzinformationen werden mit den von Ihnen importierten Informationen aktualisiert. Wenn Sie ein leeres Feld importieren, verlieren Sie Ihre Daten.

## <a name="detection-engines"></a>Ermittlungsengines

Durch selbstlernende Analyse-Engines entfällt die Notwendigkeit, Signaturen oder definierende Regeln zu aktualisieren. Die Engines verwenden ICS-spezifische Verhaltensanalysen und Data Science, um den OT-Netzwerkdatenverkehr fortlaufend auf Anomalien, Schadsoftware, Betriebsprobleme, Protokollverletzungen und Abweichungen von der Netzwerkaktivitätsbaseline zu untersuchen.

> [!NOTE]
> Es empfiehlt sich, alle Sicherheitsengines zu aktivieren.

Wenn eine Engine eine Abweichung erkennt, wird eine Warnung ausgelöst. Sie können Warnungen auf dem Warnungsbildschirm oder auf einem Partnersystem anzeigen und verwalten.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/deviation-alert-screen.png" alt-text="Screenshot, der die Ermittlung von Abweichungen zeigt.":::

Der Name der Engine, die die Warnung ausgelöst hat, wird unter dem Titel der Warnung angezeigt.

### <a name="protocol-violation-engine"></a>Protokollverletzungs-Engine 

Eine Protokollverletzung tritt ein, wenn die Paketstruktur oder die Feldwerte nicht der Protokollspezifikation entsprechen.

Beispielszenario: Warnung *„Illegal MODBUS Operation (Function Code Zero)“* (Illegaler MODBUS-Vorgang (Funktionscode null)). Diese Warnung weist darauf hin, dass ein primäres Gerät eine Anforderung mit einem Funktionscode 0 an ein sekundäres Gerät gesendet hat. Diese Aktion ist gemäß der Protokollspezifikation unzulässig, und das sekundäre Gerät behandelt die Eingabe möglicherweise nicht ordnungsgemäß.

### <a name="policy-violation-engine"></a>Richtlinienverstoß-Engine

Eine Richtlinienverletzung tritt ein, wenn eine Abweichung von dem in den gelernten oder konfigurierten Einstellungen definierten Baselineverhalten auftritt.

Beispielszenario: Warnung *„Unauthorized HTTP User Agent“* (Nicht autorisierter HTTP-Benutzeragent). Diese Warnung zeigt an, dass eine Anweisung, die nicht gelernt oder durch eine Richtlinie genehmigt wurde, als HTTP-Client auf einem Gerät verwendet wird. Dabei kann es sich um einen neuen Webbrowser oder eine Anwendung auf diesem Gerät handeln.

### <a name="malware-engine"></a>Schadsoftware-Engine

Die Schadsoftware-Engine erkennt böswillige Netzwerkaktivitäten.

Beispielszenario: Warnung *„Suspicion of Malicious Activity (Stuxnet)“* (Verdacht auf böswillige Aktivität (Stuxnet)). Diese Warnung weist darauf hin, dass der Sensor verdächtige Netzwerkaktivität erkannt hat, von der bekannt ist, dass sie im Zusammenhang mit der Schadsoftware Stuxnet steht. Diese Schadsoftware stellt eine fortgeschrittene ständige Bedrohung dar, die Industriesteuerungs- und SCADA-Netzwerke als Ziel hat.

### <a name="anomaly-engine"></a>Anomalie-Engine

Die Anomalie-Engine erkennt Anomalien im Netzwerkverhalten.

Beispielszenario: Warnung *„Periodic Behavior in Communication Channel“* (Periodisches Verhalten im Kommunikationskanal). Die Komponente untersucht Netzwerkverbindungen und findet ein periodisches und zyklisches Verhalten in der Datenübermittlung. Dieses Verhalten kommt in Industrienetzwerken häufig vor.

### <a name="operational-engine"></a>Betriebs-Engine

Die Betriebs-Engine erkennt Betriebsvorfälle oder Entitäten mit Funktionsfehlern.

Beispielszenario: Warnung *„Device is Suspected to be Disconnected (Unresponsive)“* („Das Gerät steht im Verdacht, getrennt zu sein (nicht zu reagieren)). Diese Warnung wird ausgelöst, wenn ein Gerät für einen vordefinierten Zeitraum auf keinerlei Art von Anforderung reagiert. Diese Warnung kann auf einen Ausfall, eine Trennung oder eine Fehlfunktion des Geräts hindeuten.

### <a name="enable-and-disable-engines"></a>Aktivieren und Deaktivieren von Engines

Wenn Sie eine Richtlinienengine deaktivieren, stehen die von der Engine generierten Informationen dem Sensor nicht zur Verfügung. Wenn Sie beispielsweise die Anomalie-Engine deaktivieren, erhalten Sie keine Warnungen zu Netzwerkanomalien. Wenn Sie eine Weiterleitungsregel erstellt haben, werden keine Anomalien gesendet, die von der Engine gelernt wurden. Zum Aktivieren oder Deaktivieren einer RIchtlinienengine wählen Sie für die bestimmte Engine **Enabled** (Aktiviert) oder **Disabled** (Deaktiviert) aus.

Die Gesamtbewertung wird in der unteren rechten Ecke des Bildschirms **System Settings** (Systemeinstellungen) angezeigt. Die Bewertung gibt den Prozentsatz des verfügbaren Schutzes an, der in Form der Bedrohungsschutz-Engines aktiviert ist. Jede Engine leistet 20 Prozent des verfügbaren Schutzes.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/protection-score-screen.png" alt-text="Screenshot mit einer Bewertung.":::

## <a name="configure-dhcp-address-ranges"></a>Konfigurieren von DHCP-Adressbereichen

Ihr Netzwerk enthält möglicherweise sowohl statische als auch dynamische IP-Adressen. In der Regel werden statische Adressen in OT-Netzwerken über Historians, Controller und Netzwerkinfrastrukturgeräte wie Switches und Router gefunden. Die dynamische Zuweisung von IP-Adressen ist normalerweise in Gastnetzwerken mit Laptops PCs, Smartphones und anderen tragbaren Geräten implementiert (an verschiedenen Standorten kommen physische WLAN- oder LAN-Verbindungen zur Anwendung).

Wenn Sie mit dynamischen Netzwerken arbeiten, behandeln Sie IP-Adressänderungen, die eintreten, wenn neue IP-Adressen zugewiesen werden. Dies geschieht durch das Zuweisen von DHCP-Adressbereichen.

Änderungen können beispielsweise auftreten, wenn ein DHCP-Server IP-Adressen zuweist.

Das Definieren von dynamischen IP-Adressen ermöglicht umfassende, transparente Unterstützung in Fällen von IP-Adresswechseln. Dadurch wird umfassende Berichterstellung für jedes eindeutige Gerät sichergestellt.

Die Sensorkonsole stellt die aktuellste IP-Adresse dar, die dem Gerät zugeordnet ist, und gibt an, welche Geräte dynamisch sind. Beispiel:

- Der Data Mining-Bericht und der Geräteinventurbericht konsolidieren alle vom Gerät gelernten Aktivitäten als eine Entität, unabhängig von den Änderungen der IP-Adresse. Diese Berichte geben an, welche Adressen als DHCP-Adressen definiert wurden.

  :::image type="content" source="media/how-to-control-what-traffic-is-monitored/populated-device-inventory-screen-v2.png" alt-text="Screenshot, der das Geräteinventar zeigt":::

- Das Fenster **Device Properties** (Geräteeigenschaften) gibt an, ob das Gerät als DHCP-Gerät definiert wurde.

So legen Sie einen DHCP-Adressbereich fest:

1.  Wählen Sie im seitlichen Menü im Fenster **System Settings** (Systemeinstellungen) **DHCP Ranges** (DHCP-Bereiche) aus.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/dhcp-address-range-screen.png" alt-text="Screenshot: Auswahl von DHCP-Bereichen.":::

2.  Definieren Sie einen Bereich, indem Sie Werte **From** (Von) und **To** (Bis) festlegen.

3.  Optional: Definieren Sie den Bereichsnamen, bis zu 256 Zeichen.

4.  Wählen Sie **Export** (Exportieren) aus, um die Bereiche in eine CSV-Datei zu exportieren.

5. Wenn Sie manuell mehrere Bereiche aus einer CSV-Datei hinzufügen möchten, wählen Sie **Import** (Importieren) und dann die Datei aus.

    > [!NOTE]
    > Bereiche, die Sie aus einer CSV-Datei importieren, überschreiben die vorhandenen Bereichseinstellungen.

6. Wählen Sie **Speichern** aus.

## <a name="configure-dns-servers-for-reverse-lookup-resolution"></a>Konfigurieren von DNS-Servern für die Reverse Lookup-Auflösung

Zur Verbesserung der Geräteerweiterung können Sie mehrere DNS-Server für die Ausführung von Reverse Lookups konfigurieren. Sie können Hostnamen oder FQDNs auflösen, die den in Netzwerksubnetzen erkannten IP-Adressen zugeordnet sind. Wenn beispielsweise ein Sensor eine IP-Adresse ermittelt, fragt er möglicherweise mehrere DNS-Server ab, um den Hostnamen aufzulösen.

Alle CIDR-Formate werden unterstützt.

Der Hostname wird im Gerätebestand, der Gerätezuordnung und in Berichten angezeigt.

Sie können Zeitpläne für die Reverse Lookup-Auflösung für bestimmte stündliche Intervalle planen, beispielsweise alle 12 Stunden. Oder Sie können eine bestimmte Uhrzeit planen.

So definieren Sie DNS-Server:

1. Wählen Sie **System Settings** (Systemeinstellungen) und dann **DNS Settings** (DNS-Einstellungen) aus.

2. Wählen Sie **Add DNS Server** (DNS-Server hinzufügen) aus.

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-configuration-screen.png" alt-text="Screenshot: Auswahl in „Add DNS Server“ (DNS-Server hinzufügen).":::

3. Wählen Sie im Feld **Schedule reverse DNS lookup** (Reverse DNS-Lookup planen) eine dieser Optionen aus:

    - Intervalle (pro Stunde).
  
    - Eine bestimmte Uhrzeit. Verwenden Sie das europäische Format. Verwenden Sie beispielsweise **14:30**, nicht **2:30 PM**.

4. Geben Sie im Feld **DNS Server Address** (DNS-Serveradresse) die DNS-IP-Adresse ein.

5. Geben Sie im Feld **DNS Server Port** (DNS-Serverport) den DNS-Port ein.

6. Lösen Sie die Netzwerk-IP-Adressen in FQDNs von Geräten auf. Fügen Sie im Feld **Number of Labels** (Anzahl der Bezeichnungen) die Anzahl der anzuzeigenden Domänenbezeichnungen ein. Es werden bis zu 30 Bezeichnungen von links nach rechts angezeigt.

7. Geben Sie im Feld **Subnets** (Subnetze) die Subnetze ein, die vom DNS-Server abgefragt werden sollen.

8. Aktivieren Sie den Umschalter **Enable** (Aktivieren), wenn Sie den Reverse Lookup einleiten möchten.

### <a name="test-the-dns-configuration"></a>Testen der DNS-Konfiguration 

Überprüfen Sie mithilfe eines Testgeräts, ob die von Ihnen definierten Einstellungen ordnungsgemäß funktionieren:

1. Aktivieren Sie den Umschalter **DNS Lookup** (DNS-Lookup).

2. Klicken Sie auf **Test**.

3. Geben Sie eine Adresse in **Lookup Address** (Lookup-Adresse) im Dialogfeld **DNS reverse lookup test for server** (Test des DNS-Reverse Lookups für Server) ein.

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-test-screen.png" alt-text="Screenshot des Lookupadressbereichs.":::

4. Klicken Sie auf **Test**.

## <a name="configure-windows-endpoint-monitoring"></a>Konfigurieren der Windows-Endpunktüberwachung

Mit der Windows-Funktion zur Endpunktüberwachung können Sie in Azure Defender für IoT selektive Stichproben auf Windows-Systemen konfigurieren. Dadurch erhalten Sie stärker fokussierte und genauere Informationen über Ihre Geräte, etwa über die Service Pack-Stufen.

Sie können die Erfassung von Stichproben mit bestimmten Bereichen und Hosts und nur im gewünschten Umfang konfigurieren. Sie erreichen die selektive Erfassung von Stichproben mithilfe der Windows-Verwaltungsinstrumentation (WMI), der Standard-Skriptsprache von Microsoft zur Verwaltung von Windows-Systemen.

> [!NOTE]
> - Es kann jederzeit nur jeweils eine Überprüfung durchgeführt werden.
> - Optimale Ergebnisse erhalten Sie mit Benutzern, die Domänenberechtigungen oder lokale Administratorrechte besitzen.
> - Bevor Sie mit der WMI-Konfiguration beginnen, konfigurieren Sie eine Firewallregel, die ausgehenden Datenverkehr vom Sensor in das überprüfte Subnetz über den UDP-Port 135 und alle TCP-Ports oberhalb von 1024 zulässt.

Wenn die Stichprobe abgeschlossen ist, steht in der Option zum Protokollexport eine Protokolldatei mit allen unternommen Versuchen zur Stichprobenerfassung bereit. Das Protokoll enthält alle IP-Adressen, für die eine Stichprobe erfolgt ist. Für jede IP-Adresse weist das Protokoll Erfolgs- und Fehlerinformationen aus. Ferner gibt es einen Fehlercode, bei dem es sich um eine freie Zeichenfolge handelt, die aus der Ausnahme abgeleitet wurde. Im System wird nur die Überprüfung des letzten Protokolls aufbewahrt.

Sie können die Überprüfungen nach einem Zeitplan oder manuell ausführen. Wenn eine Überprüfung abgeschlossen wurde, können Sie die Ergebnisse in einer CSV-Datei anzeigen.

**Voraussetzungen**

Konfigurieren Sie eine Firewallregel, die ausgehenden Datenverkehr vom Sensor in das überprüfte Subnetz über den UDP-Port 135 und alle TCP-Ports oberhalb von 1024 zulässt.

So konfigurieren Sie eine automatische Überprüfung:

1. Wählen Sie im Menü an der Seite **System Settings** (Systemeinstellungen) aus.

2. Wählen Sie **Windows Endpoint Monitoring** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: (Windows-Endpunktüberwachung) aus.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Screenshot der Auswahl von Windows Endpoint Monitoring (Windows-Endpunktüberwachung).":::

3. Konfigurieren Sie die Optionen im Bereich **Scan Schedule** (Zeitplan für die Überprüfung) wie folgt:

      - **By fixed intervals (in hours)** (Nach festen Intervallen (in Stunden)): Legen Sie den Überprüfungszeitplan nach Intervallen in Stunden fest.

      - **By specific times** (Nach bestimmter Uhrzeit): Legen Sie den Überprüfungszeitplan nach bestimmten Uhrzeiten fest, und wählen Sie **Save Scan** (Überprüfung speichern) aus.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/schedule-a-scan-screen-v2.png" alt-text="Screenshot der Schaltfläche „Save Scan“"::: (Überprüfung speichern)

4. Zum Definieren des Überprüfungsbereichs wählen Sie **Set scan ranges** (Überprüfungsbereiche festlegen) aus.

5. Legen Sie den IP-Adressbereich fest, und fügen Sie Ihren Benutzer und das Kennwort hinzu.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-scan-range-screen.png" alt-text="Screenshot: Hinzufügen von Benutzer und Kennwort":::

6. Wenn Sie einen IP-Adressbereich aus einer Überprüfung ausschließen möchten, wählen Sie neben dem Bereich **Disable** (Deaktivieren) aus.

7. Zum Entfernen eines Bereichs wählen Sie :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/remove-scan-icon.png" border="false"::: neben dem Bereich aus.

8. Wählen Sie **Speichern** aus. Das Dialogfeld **Edit Scan Ranges Configuration** (Konfiguration der Überprüfungsbereiche bearbeiten) wird geschlossen, und die Anzahl der Bereiche wird im Bereich **Scan Ranges** (Überprüfungsbereiche) angezeigt.

So führen Sie eine manuelle Überprüfung aus:

1. Wählen Sie im Menü an der Seite **System Settings** (Systemeinstellungen) aus.

2. Wählen Sie **Windows Endpoint Monitoring** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: (Windows-Endpunktüberwachung) aus.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Screenshot des Setupbildschirms für Windows Endpoint Monitoring (Windows-Endpunktüberwachung).":::

3. Wählen Sie im Bereich **Actions** (Aktionen) **Start scan** (Überprüfung starten) aus. Eine Statusleiste wird im Bereich **Actions** (Aktionen) angezeigt, die den Fortschritt des Überprüfungsvorgangs darstellt.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/started-scan-screen-v2.png" alt-text="Screenshot der Schaltfläche „Start scan“ (Überprüfung starten).":::

So zeigen Sie die Überprüfungsergebnisse an:

1. Wenn die Überprüfung abgeschlossen ist, wählen Sie im Bereich **Actions** (Aktionen) **View Scan Results** (Überprüfungsergebnisse anzeigen) aus. Die CSV-Datei mit den Scanergebnissen wird auf Ihren Computer heruntergeladen.

## <a name="next-steps"></a>Nächste Schritte

[Untersuchen von Sensorerkennungen in einem Gerätebestand](how-to-investigate-sensor-detections-in-a-device-inventory.md)
[Untersuchen von Sensorerkennungen in der Geräteübersicht](how-to-work-with-the-sensor-device-map.md)

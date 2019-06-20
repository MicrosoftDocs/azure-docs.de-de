---
title: Nachverfolgen von Änderungen mit Azure Automation
description: Mit der Lösung für die Änderungsnachverfolgung können Sie Änderungen an Software und Windows-Diensten in Ihrer Umgebung besser erkennen.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 04/29/2019
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4f917c45030ad70a2ab76fed877bd822d1902f82
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64928659"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Nachverfolgen von Änderungen in Ihrer Umgebung mit der Lösung für die Änderungsnachverfolgung

Dieser Artikel unterstützt Sie bei der Verwendung der Lösung für die Änderungsnachverfolgung, damit Sie Änderungen an Ihrer Umgebung einfach erkennen können. Die Lösung verfolgt Änderungen an Windows- und Linux-Software, an Windows- und Linux-Dateien sowie an Windows-Registrierungsschlüsseln, Windows-Diensten und Linux-Daemons. Durch Ermitteln von Konfigurationsänderungen können Sie Betriebsprobleme präzise bestimmen.

Änderungen an installierter Software, an Windows-Diensten, an der Windows-Registrierung und an Windows-Dateien sowie an Linux-Daemons auf den überwachten Servern werden zur Verarbeitung an den Azure Monitor-Dienst in der Cloud gesendet. Auf die empfangenen Daten wird Logik angewendet, und der Clouddienst zeichnet die Daten auf. Mithilfe der Informationen im Change Tracking-Dashboard können Sie ganz leicht die Änderungen erkennen, die in Ihrer Serverinfrastruktur vorgenommen wurden.

> [!NOTE]
> Die Azure Automation-Änderungsnachverfolgung verfolgt Änderungen in virtuellen Computern nach. Informationen zum Nachverfolgen von Eigenschaftenänderungen in Azure Resource Manager finden Sie im [Änderungsverlauf](../governance/resource-graph/how-to/get-resource-changes.md) von Azure Resource Graph.

## <a name="supported-windows-operating-systems"></a>Unterstützte Windows-Betriebssysteme

Die folgenden Versionen des Windows-Betriebssystems werden für den Windows-Agent offiziell unterstützt:

* Windows Server 2008 R2 oder höher

## <a name="supported-linux-operating-systems"></a>Unterstützte Linux-Betriebssysteme

Die folgenden Linux-Distributionen werden offiziell unterstützt. Der Linux-Agent kann jedoch auch auf anderen Distributionen ausgeführt werden, die hier nicht aufgeführt sind. Sofern nicht anders angegeben, werden alle Nebenversionen für jede aufgeführte Hauptversion unterstützt.  

### <a name="64-bit"></a>64 Bit

* CentOS 6 und 7
* Amazon Linux 2017.09
* Oracle Linux 6 und 7
* Red Hat Enterprise Linux Server 6 und 7
* Debian GNU/Linux 8 und 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS und 18.04 LTS
* SUSE Linux Enterprise Server 12

### <a name="32-bit"></a>32 Bit

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 und 9
* Ubuntu Linux 14.04 LTS und 16.04 LTS

## <a name="onboard"></a>Aktivieren der Lösung für Änderungsnachverfolgung und Bestand

Um mit der Nachverfolgung von Änderungen zu beginnen, müssen Sie die Lösung für die Änderungsnachverfolgung und den Bestand aktivieren. Es gibt viele Methoden, Computer in die Änderungsnachverfolgung und den Bestand zu integrieren. Die folgenden Methoden zum Integrieren der Lösung werden empfohlenen und unterstützt.

* [Über einen virtuellen Computer](automation-onboard-solutions-from-vm.md)
* [Über Durchsuchen mehrerer Computer](automation-onboard-solutions-from-browse.md)
* [Über Ihr Automation-Konto](automation-onboard-solutions-from-automation-account.md)
* [Über ein Azure Automation-Runbook](automation-onboard-solutions.md)

## <a name="configuring-change-tracking-and-inventory"></a>Konfigurieren der Änderungs- und Bestandsnachverfolgung

Wie Sie Computer in die Lösung integrieren, erfahren Sie unter: [Integrieren von Automation-Lösungen](automation-onboard-solutions-from-automation-account.md). Nachdem ein Computeronboarding mit der Lösung „Änderungsnachverfolgung“ und „Bestand“ vorgenommen wurde, können Sie die nachzuverfolgenden Elemente konfigurieren. Wenn Sie eine neue Datei oder einen Registrierungsschlüssel zum Nachverfolgen aktivieren, wird das entsprechende Element sowohl für die Änderungsnachverfolgung als auch die Bestandsverfolgung aktiviert.

Zum Nachverfolgen von Änderungen unter Windows und Linux werden MD5-Hashes der Dateien verwendet. Diese Hashes werden dann verwendet, um zu ermitteln, ob seit der letzten Bestandsaufnahme eine Änderung vorgenommen wurde.

### <a name="configure-linux-files-to-track"></a>Konfigurieren von Linux-Dateien für die Nachverfolgung

Führen Sie zum Konfigurieren der Dateinachverfolgung auf Linux-Computern die folgenden Schritte aus:

1. Wählen Sie in Ihrem Automation-Konto unter **KONFIGURATIONSVERWALTUNG** die Option **Änderungsnachverfolgung** aus. Klicken Sie auf **Einstellungen bearbeiten** (das Zahnradsymbol).
2. Wählen Sie auf der Seite **Änderungsnachverfolgung** die Option **Linux-Dateien** aus, und klicken Sie dann auf **+ Hinzufügen**, um eine neue nachzuverfolgende Datei hinzuzufügen.
3. Geben Sie unter **Linux-Datei für Änderungsnachverfolgung hinzufügen** die Informationen zu der Datei oder dem Verzeichnis ein, die bzw. das nachverfolgt werden soll, und klicken Sie auf **Speichern**.

|Eigenschaft  |BESCHREIBUNG  |
|---------|---------|
|Enabled     | Bestimmt, ob die Einstellung angewendet wird        |
|Item Name     | Anzeigename der nachzuverfolgenden Datei        |
|Group     | Gruppenname für die logische Gruppierung von Dateien        |
|Pfad eingeben     | Der zu überprüfende Pfad für die Datei, Beispiel: „/etc/*.conf“       |
|Pfadtyp     | Typ des nachzuverfolgenden Elements (mögliche Werte sind „Datei“ und „Verzeichnis“)        |
|Rekursion     | Bestimmt, ob beim Suchen nach dem nachzuverfolgenden Element die Rekursion verwendet wird        |
|Sudo verwenden     | Diese Einstellung bestimmt, ob „sudo“ bei der Suche nach dem Element verwendet wird         |
|Links     | Diese Einstellung bestimmt, wie symbolische Verknüpfungen beim Durchlaufen von Verzeichnissen behandelt werden<br> **Ignorieren**: Symbolische Verknüpfungen werden ignoriert, und die referenzierten Dateien/Verzeichnisse werden nicht einbezogen.<br>**Folgen**: Folgt den symbolischen Verknüpfungen bei der Rekursion und bindet auch die referenzierten Dateien/Verzeichnisse ein<br>**Verwalten**: Folgt den symbolischen Verknüpfungen und ermöglicht das Ändern von zurückgegebenen Inhalten     |
|Hochladen von Dateiinhalt für alle Einstellungen| Aktiviert oder deaktiviert den Upload des Dateiinhalts für nachverfolgte Änderungen. Verfügbare Optionen: **True** und **False**.|

> [!NOTE]
> Die Linkoption „Verwalten“ wird nicht empfohlen. Das Abrufen von Dateiinhalten wird nicht unterstützt.

### <a name="configure-windows-files-to-track"></a>Konfigurieren der nachzuverfolgenden Windows-Dateien

Führen Sie zum Konfigurieren der Dateinachverfolgung auf Windows-Computern die folgenden Schritte aus:

1. Wählen Sie in Ihrem Automation-Konto unter **KONFIGURATIONSVERWALTUNG** die Option **Änderungsnachverfolgung** aus. Klicken Sie auf **Einstellungen bearbeiten** (das Zahnradsymbol).
2. Wählen Sie auf der Seite **Änderungsnachverfolgung** die Option **Windows-Dateien** aus, und klicken Sie dann auf **+ Hinzufügen**, um eine neue nachzuverfolgende Datei hinzuzufügen.
3. Geben Sie unter **Windows-Datei für Änderungsnachverfolgung hinzufügen** die Informationen zu der nachzuverfolgenden Datei ein, und klicken Sie auf **Speichern**.

|Eigenschaft  |BESCHREIBUNG  |
|---------|---------|
|Enabled     | Bestimmt, ob die Einstellung angewendet wird        |
|Item Name     | Anzeigename der nachzuverfolgenden Datei        |
|Group     | Gruppenname für die logische Gruppierung von Dateien        |
|Pfad eingeben     | Der zu überprüfende Pfad für die Datei, z.B. „c:\temp\\\*.txt“<br>Sie können auch Umgebungsvariablen verwenden, beispielsweise „%winDir%\System32\\\*.*“.       |
|Rekursion     | Bestimmt, ob beim Suchen nach dem nachzuverfolgenden Element die Rekursion verwendet wird        |
|Hochladen von Dateiinhalt für alle Einstellungen| Aktiviert oder deaktiviert den Upload des Dateiinhalts für nachverfolgte Änderungen. Verfügbare Optionen: **True** und **False**.|

## <a name="wildcard-recursion-and-environment-settings"></a>Platzhalter, Rekursion und Umgebungseinstellungen

Rekursion ermöglicht die Angabe von Platzhaltern, um die verzeichnisübergreifende Nachverfolgung zu vereinfachen, sowie die Angabe von Umgebungsvariablen, um Dateien in Umgebungen mit mehreren oder dynamischen Laufwerksnamen nachzuverfolgen. Die folgende Liste enthält allgemeine Informationen, mit denen Sie beim Konfigurieren der Rekursion vertraut sein sollten:

* Platzhalter werden zum Nachverfolgen mehrerer Dateien benötigt.
* Platzhalter können immer nur im letzten Segment eines Pfads verwendet werden. (z. B. `c:\folder\*file*` oder `/etc/*.conf`)
* Wenn eine Umgebungsvariable einen ungültigen Pfad besitzt, ist die Überprüfung zwar erfolgreich, bei der Ausführung der Inventur tritt jedoch ein Fehler für den Pfad auf.
* Vermeiden Sie die Verwendung allgemeiner Pfade wie `c:\*.*`, da in diesem Fall zu viele Ordner durchlaufen werden müssen.

## <a name="configure-file-content-tracking"></a>Konfigurieren der Nachverfolgung von Dateiinhalten

Mithilfe der Änderungsnachverfolgung für Dateiinhalte können Sie die Inhalte vor und nach den vorgenommenen Änderungen an einer Datei anzeigen. Dies ist für Windows- und Linux-Dateien verfügbar. Bei jeder Dateiänderung werden die Inhalte der Datei in einem Speicherkonto gespeichert, und die Datei wird in den Versionen vor und nach der Änderung angezeigt, wahlweise inline oder nebeneinander. Weitere Informationen finden Sie unter [View the contents of a tracked file](change-tracking-file-contents.md) (Anzeigen der Inhalte einer nachverfolgten Datei).

![Anzeigen von Änderungen in einer Datei](./media/change-tracking-file-contents/view-file-changes.png)

### <a name="configure-windows-registry-keys-to-track"></a>Konfigurieren der nachzuverfolgenden Windows-Registrierungsschlüssel

Führen Sie zum Konfigurieren der Nachverfolgung von Registrierungsschlüsseln auf Windows-Computern die folgenden Schritte aus:

1. Wählen Sie in Ihrem Automation-Konto unter **KONFIGURATIONSVERWALTUNG** die Option **Änderungsnachverfolgung** aus. Klicken Sie auf **Einstellungen bearbeiten** (das Zahnradsymbol).
2. Wählen Sie auf der Seite **Änderungsnachverfolgung** die Option **Windows-Registrierung** aus, und klicken Sie dann auf **+ Hinzufügen**, um einen neuen nachzuverfolgenden Registrierungsschlüssel hinzuzufügen.
3. Geben Sie unter **Windows-Registrierung für Änderungsnachverfolgung hinzufügen** die Informationen zu dem Schlüssel ein, der nachverfolgt werden soll, und klicken Sie auf **Speichern**.

|Eigenschaft  |BESCHREIBUNG  |
|---------|---------|
|Enabled     | Bestimmt, ob die Einstellung angewendet wird        |
|Item Name     | Anzeigename des nachzuverfolgenden Registrierungsschlüssels        |
|Group     | Gruppenname für die logische Gruppierung von Registrierungsschlüsseln        |
|Windows-Registrierungsschlüssel   | Der zu überprüfende Pfad für den Registrierungsschlüssel Beispiel:  "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

## <a name="limitations"></a>Einschränkungen

Die Lösung für die Änderungsnachverfolgung unterstützt derzeit folgende Elemente nicht:

* Rekursion für die Nachverfolgung der Windows-Registrierung
* Netzwerkdateisysteme

Weitere Einschränkungen:

* Die Spalte **Maximale Dateigröße** und ihre Werte werden in der aktuellen Implementierung nicht genutzt.
* Wenn Sie im 30-minütigen Sammlungszyklus mehr als 2.500 Dateien erfassen, wird die Leistung der Lösung möglicherweise beeinträchtigt.
* Wenn der Netzwerkverkehr hoch ist, kann es bis zu sechs Stunden dauern, bis Änderungsdatensätze angezeigt werden.
* Wenn Sie die Konfiguration ändern, während ein Computer heruntergefahren ist, stellt der Computer möglicherweise Änderungen bereit, die zur vorherigen Konfiguration gehörten.

## <a name="known-issues"></a>Bekannte Probleme

Die Lösung für die Änderungsnachverfolgung weist derzeit die folgenden Probleme auf:

* Hotfix-Updates werden für Computer mit Windows Server 2016 Core RS3 nicht erfasst.
* Für Linux-Daemons wird möglicherweise ein geänderter Zustand angezeigt, obwohl keine Änderung vorgenommen wurde. Dies liegt an der Art, in der das `SvcRunLevels`-Feld erfasst wird.

## <a name="change-tracking-data-collection-details"></a>Details zur Datensammlung für die Änderungsnachverfolgung

Die folgende Tabelle zeigt die Datensammlungshäufigkeit für die Änderungstypen. Für jeden Typ wird die Datenmomentaufnahme des aktuellen Status außerdem mindestens alle 24 Stunden aktualisiert:

| **Änderungstyp** | **Frequency** |
| --- | --- |
| Windows-Registrierung | 50 Minuten |
| Windows-Datei | 30 Minuten |
| Linux-Datei | 15 Minuten |
| Windows-Dienste | 10 Sekunden bis 30 Minuten</br> Standardwert: 30 Minuten |
| Linux-Daemons | 5 Minuten |
| Windows-Software | 30 Minuten |
| Linux-Software | 5 Minuten |

Die folgende Tabelle zeigt die Grenzen der nachverfolgten Elemente pro Computer für die Änderungsnachverfolgung.

| **Ressource** | **Begrenzung**| **Hinweise** |
|---|---|---|
|Datei|500||
|Registrierung|250||
|Windows-Software|250|Softwareupdates nicht inbegriffen|
|Linux-Pakete|1250||
|Dienste|250||
|Daemon|250||

Die durchschnittliche Nutzung von Log Analytics-Daten für einen Computer mit Änderungsnachverfolgung und Bestand beträgt ungefähr 40 MB pro Monat. Dieser Wert ist nur ein Näherungswert und kann sich abhängig von Ihrer Umgebung ändern. Es wird empfohlen, Ihre Umgebung zu überwachen, um die genaue Nutzung zu ermitteln.

### <a name="windows-service-tracking"></a>Windows-Dienstnachverfolgung

Die Standard-Sammelhäufigkeit für Windows-Dienste beträgt 30 Minuten. Um die Häufigkeit zu konfigurieren, wechseln Sie zu **Änderungsnachverfolgung**. Auf der Registerkarte **Windows-Dienste** befindet sich unter **Bearbeitungseinstellungen** ein Schieberegler, mit dem Sie die Sammelhäufigkeit für Windows-Dienste vom kleinsten Wert von 10 Sekunden bis zum größten von 30 Minuten ändern können. Bewegen Sie den Schieberegler auf die gewünschte Häufigkeit; sie wird automatisch gespeichert.

![Windows-Dienste-Schieberegler](./media/change-tracking/windowservices.png)

Der Agent verfolgt nur Änderungen nach, dies optimiert seine Leistung. Durch das Einstellen eines hohen Schwellenwerts werden Änderungen möglicherweise übergangen, wenn der Dienst in seinen ursprünglichen Zustand zurückversetzt wurde. Das Festlegen der Häufigkeit auf einen kleineren Wert ermöglicht es Ihnen, Änderungen zu erfassen, die sonst verpasst würden.

> [!NOTE]
> Zwar kann der Agent Änderungen bis hinab zu einem 10-Sekunden-Intervall nachverfolgen, bis zur Anzeige der Daten im Portal vergehen jedoch einige Minuten. Änderungen, die zwischen der Erfassung und der Anzeige im Portal erfolgen, werden trotzdem nachverfolgt und protokolliert.
  
### <a name="registry-key-change-tracking"></a>Registrierungsschlüssel-Änderungsnachverfolgung

Die Überwachung von Änderungen der Registrierungsschlüssel dient dem Ermitteln von Erweiterungspunkten, an denen Code von Drittanbietern und Schadsoftware aktiv werden können. Die folgende Liste enthält die Liste der vorkonfigurierten Registrierungsschlüssel. Diese Schlüssel sind konfiguriert, aber nicht aktiviert. Um diese Registrierungsschlüssel nachzuverfolgen, müssen Sie jeden einzeln aktivieren.

> [!div class="mx-tdBreakAll"]
> |  |
> |---------|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Überwacht gängige Autostart-Einträge, die sich direkt bei Windows-Explorer einklinken und in der Regel In-Process mit „Explorer.exe“ ausgeführt werden.    |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Überwacht Skripts, die beim Start ausgeführt werden.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown**    |
|&nbsp;&nbsp;&nbsp;&nbsp;Überwacht Skripts, die beim Herunterfahren ausgeführt werden.     |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Überwacht Schlüssel, die geladen werden, bevor sich der Benutzer bei seinem Windows-Konto anmeldet. Der Schlüssel wird für 32-Bit-Programme verwendet, die auf 64-Bit-Computern ausgeführt werden.    |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Überwacht Änderungen an Anwendungseinstellungen.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Überwacht gängige Autostart-Einträge, die sich direkt bei Windows-Explorer einklinken und in der Regel In-Process mit „Explorer.exe“ ausgeführt werden.|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Überwacht gängige Autostart-Einträge, die sich direkt bei Windows-Explorer einklinken und in der Regel In-Process mit „Explorer.exe“ ausgeführt werden.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Überwacht die Symboloverlayhandler-Registrierung.|
|**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Überwacht die Symboloverlayhandler-Registrierung für 32-Bit-Programme, die auf 64-Bit-Computern ausgeführt werden.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;Überwacht neue Browserhilfsobjekt-Plug-Ins für Internet Explorer. Wird für den Zugriff auf das Dokumentobjektmodell (DOM) der aktuellen Seite und zum Steuern der Navigation verwendet.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;Überwacht neue Browserhilfsobjekt-Plug-Ins für Internet Explorer. Wird für den Zugriff auf das Dokumentobjektmodell (DOM) der aktuellen Seite und zum Steuern der Navigation für 32-Bit-Programme, die auf 64-Bit-Computern ausgeführt werden, verwendet.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Überwacht, ob neue Internet Explorer-Erweiterungen vorliegen, z. B. benutzerdefinierte Toolmenüs und benutzerdefinierte Symbolleistenschaltflächen.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Überwacht, ob neue Internet Explorer-Erweiterungen vorliegen, z. B. benutzerdefinierte Toolmenüs und benutzerdefinierte Symbolleisten-Schaltflächen für 32-Bit-Programme, die auf 64-Bit-Computern ausgeführt werden.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Überwacht die mit wavemapper zugeordneten 32-Bit-Treiber, wave1 und wave2, msacm.imaadpcm, .msadpcm, .msgsm610 und vidc. Ähnlich dem Abschnitt „[drivers]“ in der SYSTEM. INI-Datei.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Überwacht die mit wavemapper zugeordneten 32-Bit-Treiber, wave1 und wave2, msacm.imaadpcm, .msadpcm, .msgsm610 und vidc für 32-Bit-Programme, die auf 64-Bit-Computern ausgeführt werden. Ähnlich dem Abschnitt „[drivers]“ in der SYSTEM. INI-Datei.|
> |**HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls**|
|&nbsp;&nbsp;&nbsp;&nbsp;Überwacht die Liste der bekannten oder häufig verwendete System-DLLs. Dieses System verhindert, dass schwache Anwendungsverzeichnisberechtigungen durch Infiltration mit Trojanerversionen von System-DLLs ausgenutzt werden.|
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify**|
|&nbsp;&nbsp;&nbsp;&nbsp;Überwacht die Liste der Pakete, die Ereignisbenachrichtigungen von Winlogon, dem interaktiven Anmeldungsunterstützungsmodell für das Windows-Betriebssystem, empfangen können.|

## <a name="network-requirements"></a>Netzwerkanforderungen

Die folgenden Adressen sind speziell für die Änderungsnachverfolgung erforderlich. Die Kommunikation mit diesen Adressen erfolgt über Port 443.

|Azure – Öffentlich  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

## <a name="use-change-tracking"></a>Verwenden von Change Tracking

Nachdem die Lösung aktiviert ist, können Sie die Zusammenfassung der Änderungen für Ihre überwachten Computern anzeigen. Wählen Sie dazu in Ihrem Automation-Konto unter **KONFIGURATIONSVERWALTUNG** die Option **Änderungsnachverfolgung** aus.

Sie können zuerst die Änderungen an Ihren Computern und dann Einzelheiten für jedes Ereignis anzeigen. Dropdownmenüs stehen am oberen Rand des Diagramms zur Verfügung, damit Sie anhand des Änderungstyps und Zeitbereichs das Diagramm und die detaillierten Informationen einschränken können. Sie können auch auf dem Diagramm klicken und ziehen, um einen benutzerdefinierten Zeitbereich auszuwählen. **Änderungstyp** nimmt einen der folgenden Werte an: **Ereignisse**, **Daemons**, **Dateien**, **Registrierung**,  **Software**, **Windows-Dienste**. „Kategorie“ zeigt Ihnen den Typ der Änderung und kann **Hinzugefügt**, **Geändert** oder **Entfernt** sein.

![Abbildung des Change Tracking-Dashboards](./media/change-tracking/change-tracking-dash01.png)

Detaillierte Informationen zu einer Änderung oder einem Ereignis können Sie durch Klicken auf die Änderung bzw. das Ereignis aufrufen. Wie aus dem Beispiel hervorgeht, wurde der Starttyp des Diensts von „Manuell“ in „Automatisch“ geändert.

![Abbildung mit Details der Änderungsnachverfolgung](./media/change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Suchprotokolle

Zusätzlich zu den Details, die im Portal bereitgestellt werden, können Sie auch die Protokolle durchsuchen. Öffnen Sie die Seite **Änderungsnachverfolgung**, und klicken Sie auf **Log Analytics**; daraufhin wird die Seite **Protokolle** geöffnet.

### <a name="sample-queries"></a>Beispielabfragen

Die folgende Tabelle enthält Beispiele für Protokollsuchen für Änderungsdatensätze, die mit dieser Lösung erfasst wurden:

|Abfragen  |BESCHREIBUNG  |
|---------|---------|
|ConfigurationData<br>&#124; where   ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"<br>&#124; where SvcState == "Stopped"<br>&#124; summarize arg_max(TimeGenerated, *) by SoftwareName, Computer         | Zeigt die aktuellen Bestandsdatensätze für Windows-Dienste an, die auf „Auto“ festgelegt, aber als „Beendet“ gemeldet wurden.<br>Die Ergebnisse werden auf den aktuellen Datensatz für den betreffenden Softwarenamen und Computer beschränkt.      |
|ConfigurationChange<br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Removed"<br>&#124; order by TimeGenerated desc|Zeigt die Änderungsdatensätze für entfernte Software an.|

## <a name="alert-on-changes"></a>Warnung zu Änderungen

Eine Schlüsselfunktion von Änderungsnachverfolgung und des Bestands ist die Möglichkeit, Warnungen zum Konfigurationsstatus und zu allen Änderungen am Konfigurationsstatus Ihrer Hybridumgebung auszugeben.  

Im folgenden Beispiel zeigt der Screenshot, dass die Datei `C:\windows\system32\drivers\etc\hosts` auf einem Computer geändert wurde. Diese Datei ist wichtig, da die Hostdatei von Windows verwendet wird, um Hostnamen in IP-Adressen aufzulösen, und sogar vor DNS Vorrang hat, was zu Verbindungsproblemen oder der Umleitung von Datenverkehr auf bösartige oder anderweitig gefährliche Websites führen kann.

![Ein Diagramm mit der Änderung an der Hostdatei](./media/change-tracking/changes.png)

Um diese Änderung weiter zu analysieren, gehen Sie zur Protokollsuche, indem Sie auf **Log Analytics** klicken. Suchen Sie in der Protokollsuche nach Inhaltsänderungen in der Hostdatei mit der Abfrage `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`. Diese Abfrage sucht nach Änderungen, die eine Änderung des Dateiinhalts für Dateien beinhalten, deren vollqualifizierter Pfad das Wort „hosts“ enthält. Sie können auch eine bestimmte Datei abfragen, indem Sie den Pfadabschnitt in seine vollqualifizierte Form ändern (z.B. `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`).

Nachdem die Abfrage die gewünschten Ergebnisse geliefert hat, klicken Sie auf die Schaltfläche **Neue Warnungsregel** in der Protokollsuchfunktion, um die Seite zur Erstellung von Warnungen zu öffnen. Sie können zu dieser Erfahrung auch über **Azur Monitor** im Azure-Portal navigieren. Überprüfen Sie die Abfrage bei der Warnungserstellung erneut, und ändern Sie die Warnungslogik. In diesem Fall soll die Warnung ausgelöst werden, wenn auch nur eine Änderung auf allen Computern in der Umgebung erkannt wird.

![Ein Bild, das die Änderungsanfrage zeigt, um Änderungen an der Hostsdatei zu verfolgen](./media/change-tracking/change-query.png)

Nachdem die Bedingungslogik festgelegt wurde, weisen Sie Aktionsgruppen zu, um Aktionen als Reaktion auf die Auslösung der Warnung durchzuführen. In diesem Fall habe ich die zu versendenden E-Mails eingerichtet und ein ITSM-Ticket erstellt.  Viele andere nützliche Aktionen können ebenfalls ausgeführt werden, wie z.B. das Auslösen einer Azure-Funktion, eines Automation-Runbooks, eines Webhook oder einer Logik-App.

![Ein Bild, das eine Aktionsgruppe konfiguriert, um hinsichtlich Änderungen zu warnen](./media/change-tracking/action-groups.png)

Nachdem alle Parameter und die Logik festgelegt sind, können wir die Warnung auf die Umgebung anwenden.

### <a name="alert-suggestions"></a>Warnungsvorschläge

Die Warnung zu Änderungen an der Hostdatei ist eine gute Anwendung von Warnungen für Änderungsnachverfolgung oder Bestandsdaten, aber es gibt noch viel mehr Szenarien für die Warnung, wie zum Beispiel die Fälle, die zusammen mit ihren Beispielabfragen im folgenden Abschnitt definiert sind.

|Abfragen  |BESCHREIBUNG  |
|---------|---------|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Files" and FileSystemPath contains " c:\\windows\\system32\\drivers\\"|Nützlich für das Nachverfolgen von Änderungen an kritischen Systemdateien|
|ConfigurationChange <br>&#124; where FieldsChanged contains "FileContentChecksum" and FileSystemPath == "c:\\windows\\system32\\drivers\\etc\\hosts"|Nützlich für das Nachverfolgen von Änderungen an wichtige Konfigurationsdateien|
|ConfigurationChange <br>&#124; where ConfigChangeType == "WindowsServices" and SvcName contains "w3svc" and SvcState == "Stopped"|Nützlich für das Nachverfolgen von Änderungen an wichtigen Systemdienste|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Daemons" and SvcName contains "ssh" and SvcState != "Running"|Nützlich für das Nachverfolgen von Änderungen an wichtigen Systemdienste|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Added"|Nützlich für Umgebungen, in denen Softwarekonfigurationen gesperrt werden müssen|
|ConfigurationData <br>&#124; where SoftwareName contains "Monitoring Agent" and CurrentVersion != "8.0.11081.0"|Nützlich, um zu sehen, auf welchen Computern eine veraltete oder nicht konforme Softwareversion installiert ist Es wird der letzte gemeldete Konfigurationsstatus ausgegeben, keine Änderungen.|
|ConfigurationChange <br>&#124; where RegistryKey == "HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| Nützlich für das Nachverfolgen von Änderungen an wichtigen Virenschutzschlüsseln|
|ConfigurationChange <br>&#124; where RegistryKey contains "HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\SharedAccess\\Parameters\\FirewallPolicy"| Nützlich für das Nachverfolgen von Änderungen an Firewalleinstellungen|

## <a name="next-steps"></a>Nächste Schritte

Besuchen Sie das Tutorial zur Änderungsnachverfolgung, um mehr über die Verwendung der Lösung zu erfahren:

> [!div class="nextstepaction"]
> [Problembehandlung für Änderungen in Ihrer Umgebung](automation-tutorial-troubleshoot-changes.md)

* Verwenden Sie [Protokollsuchen in Azure Monitor-Protokolle](../log-analytics/log-analytics-log-searches.md), um ausführliche Nachverfolgungsdaten anzuzeigen.
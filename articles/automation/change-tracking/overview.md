---
title: 'Azure Automation: Übersicht über Änderungsnachverfolgung und Bestand'
description: In diesem Artikel wird das Feature „Änderungsnachverfolgung und Bestand“ beschrieben, mit dem Sie Änderungen an Software und Microsoft-Diensten in Ihrer Umgebung erkennen können.
services: automation
ms.subservice: change-inventory-management
ms.date: 11/10/2020
ms.topic: conceptual
ms.openlocfilehash: b5390e4b3dc6d77390c3fca6323cbd52544c638a
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445420"
---
# <a name="change-tracking-and-inventory-overview"></a>Übersicht über Änderungsnachverfolgung und Bestand

Dieser Artikel bietet eine Einführung in Änderungsnachverfolgung und Bestand in Azure Automation. Mit diesem Feature werden Änderungen an virtuellen Computern nachverfolgt, die in Azure, lokal oder in anderen Cloudumgebungen gehostet werden, damit Sie Betriebs- und Umgebungsprobleme mit Software ermitteln können, die vom Paket-Manager der Distribution verwaltet wird. Folgende Elemente werden u. a. mit Änderungsnachverfolgung und Bestand nachverfolgt:

- Windows-Software
- Linux-Software (Pakete)
- Windows- und Linux-Dateien
- Windows-Registrierungsschlüssel
- Microsoft-Dienste
- Linux-Daemons

> [!NOTE]
> Informationen zum Nachverfolgen von Eigenschaftenänderungen in Azure Resource Manager finden Sie im [Änderungsverlauf](../../governance/resource-graph/how-to/get-resource-changes.md) von Azure Resource Graph.

Beim Feature „Änderungsnachverfolgung und Bestand“ werden die [Dateiintegritätsüberwachung (File Integrity Monitoring, FIM) von Azure Security Center](../../security-center/security-center-file-integrity-monitoring.md) zum Untersuchen von Betriebssystem- und Anwendungsdateien und die Windows-Registrierung verwendet. Diese Entitäten werden per FIM überwacht, und von Änderungsnachverfolgung und Bestand wird Folgendes standardmäßig nachverfolgt:

- Softwareänderungen
- Microsoft-Dienste
- Linux-Daemons

Die Aktivierung aller Funktionen von Änderungsnachverfolgung und Bestand kann dazu führen, dass weitere Kosten anfallen. Lesen Sie [Automation – Preise](https://azure.microsoft.com/pricing/details/automation/) und [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/), bevor Sie fortfahren. 

Änderungsnachverfolgung und Bestand leitet Daten an Azure Monitor-Protokolle weiter, und diese gesammelten Daten werden in einem Log Analytics-Arbeitsbereich gespeichert. Das Feature zur Dateiintegritätsüberwachung (FIM) ist nur verfügbar, wenn **Azure Defender für Server** aktiviert ist. Weitere Informationen finden Sie unter [Azure Security Center – Preise](../../security-center/security-center-pricing.md). Bei der Dateiintegritätsüberwachung werden Daten in denselben Log Analytics-Arbeitsbereich hochgeladen, der zum Speichern von Daten für Änderungsnachverfolgung und Bestand erstellt wurde. Wir empfehlen Ihnen, Ihren verknüpften Log Analytics-Arbeitsbereich zu überwachen, um die exakte Nutzung nachzuverfolgen. Weitere Informationen zum Analysieren des Datenverbrauchs mit Azure Monitor-Protokollen finden Sie unter [Verwalten von Verbrauch und Kosten](../../azure-monitor/platform/manage-cost-storage.md).

Computer, die mit dem Log Analytics-Arbeitsbereich verbunden sind, verwenden den [Log Analytics-Agent](../../azure-monitor/platform/log-analytics-agent.md), um Daten zu Änderungen an installierter Software, Microsoft-Diensten, Windows-Registrierung und -Dateien sowie Linux-Daemons auf überwachten Servern zu sammeln. Wenn Daten verfügbar sind, sendet der Agent diese zur Verarbeitung an Azure Monitor-Protokolle. Von Azure Monitor-Protokollen wird Logik auf die empfangenen Daten angewendet, und die Daten werden aufgezeichnet und zu Analysezwecken verfügbar gemacht.

> [!NOTE]
> Die Funktion „Änderungsnachverfolgung und Bestand“ setzt voraus, dass ein Log Analytics-Arbeitsbereich mit Ihrem Automation-Konto verknüpft wird. Eine aktuelle Liste der unterstützten Regionen finden Sie unter [Arbeitsbereichzuordnungen in Azure](../how-to/region-mappings.md). Die Regionszuordnungen haben keine Auswirkung auf die Möglichkeit, virtuelle Computer in einer anderen Region als der Ihres Automation-Kontos zu verwalten.

## <a name="current-limitations"></a>Aktuelle Einschränkungen

Für Änderungsnachverfolgung und Bestand werden die folgenden Einschränkungen nicht unterstützt bzw. verwendet:

- Rekursion für die Nachverfolgung der Windows-Registrierung
- Netzwerkdateisysteme
- Verschiedene Installationsmethoden
- Unter Windows gespeicherte **_.exe_*-Dateien
- Die Spalte **Maximale Dateigröße** und ihre Werte werden in der aktuellen Implementierung nicht genutzt.
- Wenn Sie versuchen, anhand eines 30-minütigen Sammlungszyklus mehr als 2.500 Dateien zu erfassen, wird das Feature „Änderungsnachverfolgung und Bestand“ unter Umständen beeinträchtigt.
- Bei einem hohen Netzwerkdatenverkehr-Aufkommen kann es bis zu sechs Stunden dauern, bis Änderungsdatensätze angezeigt werden.
- Wenn Sie eine Konfiguration ändern, während ein Computer oder Server heruntergefahren ist, werden ggf. Änderungen bereitgestellt, die zur vorherigen Konfiguration gehören.
- Erfassung von Hotfix-Updates für Computer mit Windows Server 2016 Core RS3.
- Für Linux-Daemons wird möglicherweise ein geänderter Zustand angezeigt, obwohl keine Änderung aufgetreten ist. Dieses Problem wird durch die Art des Schreibens von `SvcRunLevels`-Daten in der Azure Monitor-Tabelle [ConfigurationChange](/azure/azure-monitor/reference/tables/configurationchange) verursacht.

## <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

Änderungsnachverfolgung und Bestand werden unter allen Betriebssystemen unterstützt, die die Anforderungen des Log Analytics-Agent erfüllen. Eine Liste der Windows- und Linux-Betriebssystemversionen, die derzeit vom Log Analytics-Agent unterstützt werden, finden Sie unter [Unterstützte Betriebssysteme](../../azure-monitor/platform/agents-overview.md#supported-operating-systems).

Informationen zu den Clientanforderungen für TLS 1.2 finden Sie unter [Erzwingen von TLS 1.2 für Azure Automation](../automation-managing-data.md#tls-12-enforcement-for-azure-automation).

### <a name="python-requirement"></a>Python-Anforderung

Änderungsnachverfolgung und Bestand unterstützen nur Python 2. Wenn Ihr Computer eine Distribution verwendet, in der Python 2 nicht standardmäßig enthalten ist, müssen Sie es installieren. Mithilfe der folgenden Beispielbefehle wird Python 2 auf verschiedenen Distributionen installiert.

- Red Hat, CentOS, Oracle: `yum install -y python2`
- Ubuntu, Debian: `apt-get install -y python2`
- SUSE: `zypper install -y python2`

Die ausführbare python2-Datei muss dem Alias *python* zugewiesen werden.

## <a name="network-requirements"></a>Netzwerkanforderungen

Die folgenden Adressen sind speziell für Änderungsnachverfolgung und Bestand erforderlich. Die Kommunikation mit diesen Adressen erfolgt über Port 443.

|Azure – Öffentlich  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|*.azure-automation.net | *.azure-automation.us|

Wenn Sie Netzwerkgruppen-Sicherheitsregeln erstellen oder Azure Firewall so konfigurieren, dass Datenverkehr an den Automation-Dienst und den Log Analytics-Arbeitsbereich zulässig ist, verwenden Sie die [Diensttags](../../virtual-network/service-tags-overview.md#available-service-tags) **GuestAndHybridManagement** und **AzureMonitor**. Dies vereinfacht die laufende Verwaltung Ihrer Netzwerksicherheitsregeln. Wenn Sie von Ihren virtuellen Azure-Computern aus sicher und privat eine Verbindung mit dem Automation-Dienst herstellen möchten, lesen Sie [Verwenden von Azure Private Link](../how-to/private-link-security.md). Um die aktuellen Informationen zu Diensttag und Bereich abzurufen, um sie als Teil Ihrer lokalen Firewallkonfigurationen einzuschließen, sehen Sie die [herunterladbaren JSON-Dateien](../../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

## <a name="enable-change-tracking-and-inventory"></a>Aktivieren der Lösung für Änderungsnachverfolgung und Bestand

Sie können „Änderungsnachverfolgung und Bestand“ auf folgende Arten aktivieren:

- Aus Ihrem [Automation-Konto](enable-from-automation-account.md) für einen oder mehrere Azure- und Nicht-Azure-Computer.

- Manuell für Nicht-Azure-Computer, einschließlich Computer oder Server, die mit [Servern mit Azure Arc-Unterstützung](../../azure-arc/servers/overview.md) registriert sind. Für Hybridcomputer empfehlen wir, den Log Analytics-Agent für Windows zu installieren, indem Sie zuerst Ihren Computer mit [Azure Arc-fähigen Servern](../../azure-arc/servers/overview.md) verbinden und dann Azure Policy verwenden, um die integrierte Richtlinie „[Log Analytics-Agent auf Azure Arc-Computern unter *Linux* oder *Windows* bereitstellen](../../governance/policy/samples/built-in-policies.md#monitoring)“ zuzuweisen. Wenn Sie auch die Überwachung der Computer mit Azure Monitor für VMs planen, verwenden Sie stattdessen die Initiative [Azure Monitor für VMs aktivieren](../../governance/policy/samples/built-in-initiatives.md#monitoring).

- Für einen einzelnen virtuellen Azure-Computer auf der [Seite für virtuelle Computer](enable-from-vm.md) im Azure-Portal. Dieses Szenario steht für virtuelle Computer unter Linux oder Windows zur Verfügung.

- Für [mehrere Azure-VMs](enable-from-portal.md), indem Sie diese auf der Seite für virtuelle Computer im Azure-Portal auswählen.

## <a name="tracking-file-changes"></a>Nachverfolgen von Dateiänderungen

Zum Nachverfolgen von Änderungen unter Windows und Linux verwendet Änderungsnachverfolgung und Bestand MD5-Hashes der Dateien. Das Feature verwendet die Hashes, um zu ermitteln, ob seit der letzten Inventur Änderungen vorgenommen wurden.

## <a name="tracking-file-content-changes"></a>Nachverfolgen von Änderungen des Dateiinhalts

„Änderungsnachverfolgung und Bestand“ ermöglicht Ihnen, den Inhalt einer Windows- oder Linux-Datei anzuzeigen. Für jede Dateiänderung speichert Änderungsnachverfolgung und Bestand den Inhalt der Datei in einem [Azure Storage-Konto](../../storage/common/storage-account-create.md). Wenn Sie eine Datei nachverfolgen, können Sie ihren Inhalt vor oder nach einer Änderung anzeigen. Der Inhalt der Datei kann entweder inline oder nebeneinander angezeigt werden.

![Anzeigen von Änderungen in einer Datei](./media/overview/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>Nachverfolgen von Registrierungsschlüsseln

„Änderungsnachverfolgung und Bestand“ ermöglicht die Überwachung von Änderungen an Windows-Registrierungsschlüsseln. Die Überwachung dient dem Ermitteln von Erweiterungspunkten, an denen Code von Drittanbietern und Schadsoftware aktiv werden können. In der folgenden Tabelle sind vorkonfigurierte (aber nicht aktivierte) Registrierungsschlüssel aufgeführt. Um diese Schlüssel nachzuverfolgen, müssen Sie jeden einzeln aktivieren.

> [!div class="mx-tdBreakAll"]
> |Registrierungsschlüssel | Zweck |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Überwacht Skripts, die beim Start ausgeführt werden.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Überwacht Skripts, die beim Herunterfahren ausgeführt werden.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Überwacht Schlüssel, die geladen werden, bevor sich der Benutzer bei seinem Windows-Konto anmeldet. Der Schlüssel wird für 32-Bit-Anwendungen verwendet, die auf 64-Bit-Computern ausgeführt werden.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Überwacht Änderungen an Anwendungseinstellungen.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Überwacht Kontextmenühandler, die sich direkt bei Windows-Explorer einklinken und in der Regel In-Process mit **explorer.exe** ausgeführt werden.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Überwacht Kopierhookhandler, die sich direkt bei Windows-Explorer einklinken und in der Regel In-Process mit **explorer.exe** ausgeführt werden.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Überwacht die Symboloverlayhandler-Registrierung.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Überwacht die Symboloverlayhandler-Registrierung für 32-Bit-Anwendungen, die auf 64-Bit-Computern ausgeführt werden.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Überwacht neue Browserhilfsobjekt-Plug-Ins für Internet Explorer. Wird für den Zugriff auf das Dokumentobjektmodell (DOM) der aktuellen Seite und zum Steuern der Navigation verwendet.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Überwacht neue Browserhilfsobjekt-Plug-Ins für Internet Explorer. Wird für den Zugriff auf das Dokumentobjektmodell (DOM) der aktuellen Seite und zum Steuern der Navigation für 32-Bit-Anwendungen verwendet, die auf 64-Bit-Computern ausgeführt werden.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Überwacht, ob neue Internet Explorer-Erweiterungen vorliegen, z.B. benutzerdefinierte Toolmenüs und benutzerdefinierte Symbolleistenschaltflächen.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Überwacht, ob neue Internet Explorer-Erweiterungen vorliegen, z. B. benutzerdefinierte Toolmenüs und benutzerdefinierte Symbolleisten-Schaltflächen für 32-Bit-Anwendungen, die auf 64-Bit-Computern ausgeführt werden.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Überwacht die mit wavemapper zugeordneten 32-Bit-Treiber, wave1 und wave2, msacm.imaadpcm, .msadpcm, .msgsm610 und vidc. Ähnlich dem Abschnitt „[drivers]“ in der Datei **system.ini**.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Überwacht die mit wavemapper zugeordneten 32-Bit-Treiber, wave1 und wave2, msacm.imaadpcm, .msadpcm, .msgsm610 und vidc für 32-Bit-Anwendungen, die auf 64-Bit-Computern ausgeführt werden. Ähnlich dem Abschnitt „[drivers]“ in der Datei **system.ini**.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Überwacht die Liste der bekannten oder häufig verwendeten System-DLL-Dateien. Überwachung verhindert, dass schwache Anwendungsverzeichnisberechtigungen durch Infiltration mit Trojanerversionen von System-DLLs ausgenutzt werden.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Überwacht die Liste der Pakete, die Ereignisbenachrichtigungen von **winlogon.exe**, dem interaktiven Anmeldungsunterstützungsmodell für Windows, empfangen können.

## <a name="recursion-support"></a>Rekursionsunterstützung

Änderungsnachverfolgung und Bestand unterstützt Rekursion, sodass Sie Platzhalter angeben können, um die Nachverfolgung über Verzeichnisse hinweg zu vereinfachen. Die Rekursion bietet auch Umgebungsvariablen, mit denen Sie Dateien in mehreren Umgebungen mit unterschiedlichen oder dynamischen Laufwerknamen nachverfolgen können. Die folgende Liste enthält allgemeine Informationen, mit denen Sie beim Konfigurieren der Rekursion vertraut sein sollten:

- Platzhalter werden zum Nachverfolgen mehrerer Dateien benötigt.

- Platzhalter können Sie nur im letzten Segment eines Pfads verwenden, z. B. **C:\Ordner\\Datei** _ oder _ */etc/* .conf**.

- Wenn eine Umgebungsvariable einen ungültigen Pfad besitzt, verläuft die Überprüfung zwar erfolgreich, doch bei der Ausführung tritt ein Fehler für den Pfad auf.

- Sie sollten allgemeine Pfadnamen beim Festlegen des Pfads vermeiden, da diese Art von Einstellung dazu führen kann, dass zu viele Ordner durchlaufen werden müssen.

## <a name="change-tracking-and-inventory-data-collection"></a>Datensammlung durch Änderungsnachverfolgung und Bestand

Die nächste Tabelle zeigt die Häufigkeit der Datensammlung für die Typen von Änderungen, die von Änderungsnachverfolgung und Bestand unterstützt werden. Für jeden Typ wird die Datenmomentaufnahme des aktuellen Zustands außerdem mindestens alle 24 Stunden aktualisiert.

| **Änderungstyp** | **Frequency** |
| --- | --- |
| Windows-Registrierung | 50 Minuten |
| Windows-Datei | 30 Minuten |
| Linux-Datei | 15 Minuten |
| Microsoft-Dienste | 10 Sekunden bis 30 Minuten</br> Standardwert: 30 Minuten |
| Linux-Daemons | 5 Minuten |
| Windows-Software | 30 Minuten |
| Linux-Software | 5 Minuten |

Die folgende Tabelle zeigt die Grenzwerte der nachverfolgten Elemente pro Computer für Änderungsnachverfolgung und Bestand.

| **Ressource** | **Begrenzung** |
|---|---|---|
|Datei|500|
|Registrierung|250|
|Windows-Software (ohne Hotfixes) |250|
|Linux-Pakete|1250|
|Dienste|250|
|Daemons|250|

Die durchschnittliche Nutzung von Log Analytics-Daten für einen Computer mit Änderungsnachverfolgung und Bestand beträgt je nach Umgebung ungefähr 40 MB pro Monat. Mithilfe der Funktion „Nutzung und geschätzte Kosten“ im Log Analytics-Arbeitsbereich können Sie die von „Änderungsnachverfolgung und Bestand“ erfassten Daten in einem Nutzungsdiagramm anzeigen. Verwenden Sie diese Datenansicht, um die Datennutzung und die damit verbundenen Kosten zu analysieren. Weitere Informationen finden Sie unter [Verstehen Ihrer Nutzung und Schätzen von Kosten](../../azure-monitor/platform/manage-cost-storage.md#understand-your-usage-and-estimate-costs).

### <a name="microsoft-service-data"></a>Daten zu Microsoft-Diensten

Die Standardsammelhäufigkeit für Microsoft-Dienste beträgt 30 Minuten. Sie können die Häufigkeit mithilfe eines Schiebereglers auf der Registerkarte **Microsoft-Dienste** unter **Einstellungen bearbeiten** konfigurieren.

![Schieberegler für Microsoft-Dienste](./media/overview/windowservices.png)

Zum Optimieren der Leistung verfolgt der Log Analytics-Agent ausschließlich Änderungen nach. Durch das Einstellen eines hohen Schwellenwerts werden Änderungen möglicherweise übergangen, wenn der Dienst in seinen ursprünglichen Zustand zurückversetzt wird. Das Festlegen der Häufigkeit auf einen kleineren Wert ermöglicht Ihnen, Änderungen zu erfassen, die sonst verpasst würden.

> [!NOTE]
> Zwar kann der Agent Änderungen bis hinab zu einem 10-Sekunden-Intervall nachverfolgen, bis zur Anzeige der Daten im Azure-Portal vergehen jedoch einige Minuten. Änderungen, die zwischen der Erfassung und der Anzeige im Portal erfolgen, werden trotzdem nachverfolgt und protokolliert.

## <a name="support-for-alerts-on-configuration-state"></a>Unterstützung für Warnungen zum Konfigurationszustand

Eine Schlüsselfunktion von Änderungsnachverfolgung und Bestand sind Warnungen bei Änderungen am Konfigurationszustand Ihrer Hybridumgebung. Es sind viele nützliche Aktionen verfügbar, die als Reaktion auf Warnungen ausgelöst werden können. Dies können beispielsweise Aktionen für Azure-Funktionen, Automation-Runbooks, Webhooks und Ähnliches sein. Warnungen zu Änderungen an der Datei **c:\windows\system32\drivers\etc\hosts** für einen Computer sind eine gute Anwendung von Warnungen für Daten von „Änderungsnachverfolgung und Bestand“. Es gibt noch viele weitere Szenarien für Warnungen, einschließlich der in der nächsten Tabelle definierten Abfrageszenarien.

|Abfrage  |BESCHREIBUNG  |
|---------|---------|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Files" and FileSystemPath contains " c:\\windows\\system32\\drivers\\"|Nützlich für das Nachverfolgen von Änderungen an kritischen Systemdateien|
|ConfigurationChange <br>&#124; where FieldsChanged contains "FileContentChecksum" and FileSystemPath == "c:\\windows\\system32\\drivers\\etc\\hosts"|Nützlich für das Nachverfolgen von Änderungen an wichtige Konfigurationsdateien|
|ConfigurationChange <br>&#124; where ConfigChangeType == "WindowsServices" and SvcName contains "w3svc" and SvcState == "Stopped"|Nützlich für das Nachverfolgen von Änderungen an für das System wichtigen Diensten.|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Daemons" and SvcName contains "ssh" and SvcState!= "Running"|Nützlich für das Nachverfolgen von Änderungen an für das System wichtigen Diensten.|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Added"|Nützlich für Umgebungen, in denen Softwarekonfigurationen gesperrt werden müssen|
|ConfigurationData <br>&#124; where SoftwareName contains "Monitoring Agent" and CurrentVersion!= "8.0.11081.0"|Nützlich, um zu ermitteln, auf welchen Computern eine veraltete oder nicht konforme Softwareversion installiert ist. Mit dieser Abfrage wird der letzte gemeldete Konfigurationszustand ausgegeben, aber es werden keine Änderungen gemeldet.|
|ConfigurationChange <br>&#124; mit RegistryKey == @"HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| Nützlich für das Nachverfolgen von Änderungen an wichtigen Virenschutzschlüsseln|
|ConfigurationChange <br>&#124; mit RegistryKey contains @"HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\SharedAccess\\Parameters\\FirewallPolicy"| Nützlich für das Nachverfolgen von Änderungen an Firewalleinstellungen|

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Aktivierung über ein Automation-Konto finden Sie unter [Aktivieren von Änderungsnachverfolgung und Bestand über ein Automation-Konto](enable-from-automation-account.md).

- Informationen zur Aktivierung über das Azure-Portal finden Sie unter [Aktivieren von „Änderungsnachverfolgung und Bestand“ über das Azure-Portal](enable-from-portal.md).

- Informationen zur Aktivierung aus einem Runbook finden Sie unter [Aktivieren von Änderungsnachverfolgung und Bestand über ein Runbook](enable-from-runbook.md).

- Informationen zur Aktivierung über eine Azure-VM finden Sie unter [Aktivieren von Änderungsnachverfolgung und Bestand über einen virtuellen Azure-Computer](enable-from-vm.md).

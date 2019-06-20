---
title: Konfigurieren von Service Map in Azure | Microsoft Docs
description: Service Map ist eine Lösung in Azure, die Anwendungskomponenten auf Windows- und Linux-Systemen automatisch ermittelt und die Kommunikation zwischen Diensten abbildet. Dieser Artikel enthält Informationen zum Bereitstellen von Service Map in Ihrer Umgebung und zur Verwendung der Lösung in einer Vielzahl von Szenarien.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: d3d66b45-9874-4aad-9c00-124734944b2e
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2019
ms.author: magoedte
ms.openlocfilehash: a50f080ee4d72ad098ca87f5b3a62b42f8cd5907
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65142734"
---
# <a name="configure-service-map-in-azure"></a>Konfigurieren von Service Map in Azure

Service Map ermittelt automatisch Anwendungskomponenten auf Windows- und Linux-Systemen und stellt die Kommunikation zwischen Diensten dar. In dieser Lösung können Sie die Server ihrer Funktion gemäß anzeigen – als verbundene Systeme, die wichtige Dienste bereitstellen. Service Map zeigt Verbindungen zwischen Servern, Prozessen und Ports über die gesamte TCP-Verbindungsarchitektur an. Außer der Installation eines Agents ist keine weitere Konfiguration erforderlich.

In diesem Artikel werden das Konfigurieren von Service Map und das Onboarding von Agents beschrieben. Weitere Informationen zum Verwenden von Service Map finden Sie unter [Verwenden der Service Map-Lösung in Azure]( service-map.md).

## <a name="supported-azure-regions"></a>Unterstützte Azure-Regionen

Service Map ist derzeit in den folgenden Azure-Regionen verfügbar:
- USA (Ost)
- USA, Westen-Mitte
- Kanada, Mitte
- UK, Süden
- Europa, Westen
- Asien, Südosten

## <a name="supported-windows-operating-systems"></a>Unterstützte Windows-Betriebssysteme

Im folgenden Abschnitt sind die unterstützten Betriebssysteme für den Dependency-Agent unter Windows aufgeführt. 

>[!NOTE]
>Beachten Sie, dass Service Map nur 64-Bit-Plattformen unterstützt.
>

### <a name="windows-server"></a>Windows Server

- Windows Server 2019
- Windows Server 2016 1803
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Windows Desktop

- Windows 10 1803
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

## <a name="supported-linux-operating-systems"></a>Unterstützte Linux-Betriebssysteme

Im folgenden Abschnitt sind die unterstützten Betriebssysteme für den Dependency-Agent unter Linux aufgeführt.  

- Es werden nur die Standardversion und SMP-Version des Linux-Kernels unterstützt.
- Nicht-Standardversionen des Kernels, z.B. PAE und Xen, werden für keine Linux-Distribution unterstützt. Beispielsweise wird ein System mit der Versionszeichenfolge „2.6.16.21-0.8-xen“ nicht unterstützt.
- Benutzerdefinierte Kernels, einschließlich Neukompilierungen von Standardkernels, werden nicht unterstützt.

### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Betriebssystemversion | Kernelversion |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7,5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Betriebssystemversion | Kernelversion |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

### <a name="centosplus"></a>CentOSPlus

| Betriebssystemversion | Kernelversion |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

### <a name="ubuntu-server"></a>Ubuntu Server

| Betriebssystemversion | Kernelversion |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15.\*<br>4.18* |
| Ubuntu 16.04.3 | Kernel 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| Betriebssystemversion | Kernelversion
|:--|:--|
| 11 SP4 | 3.0.* |

### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Betriebssystemversion | Kernelversion
|:--|:--|
| 12 SP2 | 4.4.* |
| 12 SP3 | 4.4.* |

## <a name="dependency-agent-downloads"></a>Dependency-Agent – Downloads

| Datei | Betriebssystem | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.8.1 | 622C99924385CBF539988D759BCFDC9146BB157E7D577C997CDD2674E27E08DD |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.8.1 | 3037934A5D3FB7911D5840A9744AE9F980F87F620A7F7B407F05E276FE7AE4A8 |

## <a name="connected-sources"></a>Verbundene Quellen

Die Dienstzuordnung ruft ihre Daten vom Microsoft Dependency-Agent ab. Der Dependency-Agent basiert auf dem Log Analytics-Agent, da er dessen Verbindungen mit Log Analytics benötigt. Dies bedeutet, dass auf einem Server der Log Analytics-Agent mit dem Dependency-Agent installiert und konfiguriert sein muss.  In der folgenden Tabelle sind die verbundenen Quellen beschrieben, die von Service Map unterstützt werden.

| Verbundene Quelle | Unterstützt | BESCHREIBUNG |
|:--|:--|:--|
| Windows-Agents | Ja | Die Dienstzuordnung analysiert und erfasst Daten von Windows-Computern. <br><br>Zusätzlich zum [Log Analytics-Agent für Windows](../../azure-monitor/platform/log-analytics-agent.md) erfordern Windows-Agents den Microsoft Dependency-Agent. Eine vollständige Liste der Betriebssystemversionen finden Sie unter „Unterstützte Betriebssysteme“. |
| Linux-Agents | Ja | Die Dienstzuordnung analysiert und erfasst Daten von Linux-Computern. <br><br>Zusätzlich zum [Log Analytics-Agent für Linux](../../azure-monitor/platform/log-analytics-agent.md) erfordern Linux-Agents den Microsoft Dependency-Agent. Eine vollständige Liste der Betriebssystemversionen finden Sie unter „Unterstützte Betriebssysteme“. |
| System Center Operations Manager-Verwaltungsgruppe | Ja | Service Map analysiert und erfasst Daten von Windows- und Linux-Agents in einer verbundenen [System Center Operations Manager-Verwaltungsgruppe](../../azure-monitor/platform/om-agents.md). <br><br>Es ist eine direkte Verbindung des System Center Operations Manager-Agents mit Log Analytics erforderlich. |
| Azure-Speicherkonto | Nein | Da Service Map Daten von Agent-Computern erfasst, sind keine Daten aus dem Azure-Speicher zu erfassen. |

Unter Windows wird der Microsoft Monitoring Agent (MMA) von System Center Operations Manager und Log Analytics zum Erfassen und Senden von Überwachungsdaten verwendet. (Dieser Agent wird je nach Kontext als System Center Operations Manager-Agent, Log Analytics-Agent, MMA oder Direkt-Agent bezeichnet.) System Center Operations Manager und Log Analytics bieten unterschiedliche vorkonfigurierte Versionen des MMA. Jede dieser Versionen kann Berichte an System Center Operations Manager, Log Analytics oder beide senden.  

Unter Linux erfasst der Log Analytics-Agent für Linux Überwachungsdaten und sendet sie an Log Analytics. Sie können die Dienstzuordnung auf Servern mit Log Analytics-Agents verwenden, die direkt mit dem Dienst verbunden sind oder Berichte an eine in Log Analytics integrierte Operations Manager-Verwaltungsgruppe senden.  

In diesem Artikel werden alle Agents (ob unter Linux oder Windows, ob mit einer System Center Operations Manager-Verwaltungsgruppe oder direkt mit Log Analytics verbunden) als *Log Analytics-Agent* bezeichnet. 

Der Service Map-Agent selbst überträgt keine Daten und erfordert keine Änderungen an Firewalls oder Ports. Die Daten in der Dienstzuordnung werden immer vom Log Analytics-Agent an den Log Analytics-Dienst übermittelt, entweder direkt oder über das Log Analytics-Gateway.

![Service Map-Agents](media/service-map-configure/agents.png)

Wenn Sie ein System Center Operations Manager-Kunde mit einer Verwaltungsgruppe sind, die mit Log Analytics verbunden ist:

- Wenn die System Center Operations Manager-Agents die Verbindung mit Log Analytics über das Internet herstellen können, ist keine weitere Konfiguration erforderlich.  
- Wenn die System Center Operations Manager-Agents nicht über das Internet auf Log Analytics zugreifen können, müssen Sie das Log Analytics-Gateway für System Center Operations Manager konfigurieren.
  
Wenn Ihre Windows- oder Linux-Computer nicht direkt mit dem Dienst verbunden werden können, müssen Sie den Log Analytics-Agent so konfigurieren, dass er die Verbindung mit dem Log Analytics-Arbeitsbereich über das Gateway herstellt. Weitere Informationen zum Bereitstellen und Konfigurieren des Log Analytics-Gateways finden Sie unter [Verbinden von Computern ohne Internetzugriff über das Log Analytics-Gateway](../../azure-monitor/platform/gateway.md).  

### <a name="management-packs"></a>Management Packs

Wenn die Dienstzuordnung in einem Log Analytics-Arbeitsbereich aktiviert ist, wird an alle Windows-Server in diesem Arbeitsbereich ein Management Pack von 300 KB weitergeleitet. Wenn Sie System Center Operations Manager-Agents in einer [verbundenen Verwaltungsgruppe](../../azure-monitor/platform/om-agents.md) verwenden, wird das Service Map-Management Pack von System Center Operations Manager bereitgestellt. 

Der Name des Management Packs lautet „Microsoft.IntelligencePacks.ApplicationDependencyMonitor“. Es wird in das Verzeichnis „%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\“ geschrieben. Die vom Management Pack verwendete Datenquelle lautet „%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll“.

## <a name="data-collection"></a>Datensammlung

Je nach Komplexität der Systemabhängigkeiten können Sie davon ausgehen, dass jeder Agent etwa 25MB pro Tag überträgt. Jeder Agent sendet alle 15 Sekunden Service Map-Abhängigkeitsdaten.  

Der Dependency-Agent verbraucht in der Regel 0,1 % des Systemspeichers und 0,1 % der System-CPU.

## <a name="diagnostic-and-usage-data"></a>Diagnose- und Nutzungsdaten

Wenn Sie den Service Map-Dienst verwenden, sammelt Microsoft automatisch Nutzungs- und Leistungsdaten. Microsoft verwendet diese Daten, um die Qualität, Sicherheit und Integrität des Service Map-Diensts sicherzustellen und zu verbessern. Zu den Daten gehören Informationen zur Konfiguration Ihrer Software, z.B. Betriebssystem und Betriebssystemversion. Zudem gehören zu den Daten IP-Adresse, DNS-Name und Name der Arbeitsstation, um exakte und effiziente Funktionen für die Problembehandlung bereitzustellen. Wir erfassen weder Namen noch Adressen oder andere Kontaktinformationen.

Weitere Informationen zur Sammlung und Nutzung von Daten finden Sie in den [Datenschutzbestimmungen für Onlinedienste von Microsoft](https://go.microsoft.com/fwlink/?LinkId=512132).

## <a name="installation"></a>Installation

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="azure-vm-extension"></a>Azure-VM-Erweiterung

Es ist eine Erweiterung für Windows (DependencyAgentWindows) und Linux (DependencyAgentLinux) verfügbar, und Sie können den Dependency-Agent ganz einfach mithilfe einer [Azure-VM-Erweiterung](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-features) auf Ihren Azure-VMs bereitstellen.  Mit der Azure-VM-Erweiterung können Sie den Dependency-Agent mithilfe eines PowerShell-Skripts oder direkt in der VM anhand einer Azure Resource Manager-Vorlage auf Ihren Windows- und Linux-VMs bereitstellen.  Wenn Sie den Agent mit der Azure-VM-Erweiterung bereitstellen, werden Ihre Agents automatisch auf die neueste Version aktualisiert.

Zum Bereitstellen der Azure-VM-Erweiterung über PowerShell können Sie das folgende Beispiel verwenden:

```powershell
#
# Deploy the Dependency agent to every VM in a Resource Group
#

$version = "9.4"
$ExtPublisher = "Microsoft.Azure.Monitoring.DependencyAgent"
$OsExtensionMap = @{ "Windows" = "DependencyAgentWindows"; "Linux" = "DependencyAgentLinux" }
$rmgroup = "<Your Resource Group Here>"

Get-AzVM -ResourceGroupName $rmgroup |
ForEach-Object {
    ""
    $name = $_.Name
    $os = $_.StorageProfile.OsDisk.OsType
    $location = $_.Location
    $vmRmGroup = $_.ResourceGroupName
    "${name}: ${os} (${location})"
    Date -Format o
    $ext = $OsExtensionMap.($os.ToString())
    $result = Set-AzVMExtension -ResourceGroupName $vmRmGroup -VMName $name -Location $location `
    -Publisher $ExtPublisher -ExtensionType $ext -Name "DependencyAgent" -TypeHandlerVersion $version
    $result.IsSuccessStatusCode
}
```

Ein noch einfacherer Weg, um sicherzustellen, dass der Dependency-Agent auf jeder Ihrer VMs installiert ist, ist die Einbindung des Agents in Ihre Azure Resource Manager-Vorlage.  Das folgende JSON-Codebeispiel kann dem Abschnitt *resources* Ihrer Vorlage hinzugefügt werden.

```JSON
"type": "Microsoft.Compute/virtualMachines/extensions",
"name": "[concat(parameters('vmName'), '/DependencyAgent')]",
"apiVersion": "2017-03-30",
"location": "[resourceGroup().location]",
"dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
],
"properties": {
    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
    "type": "DependencyAgentWindows",
    "typeHandlerVersion": "9.4",
    "autoUpgradeMinorVersion": true
}

```

### <a name="install-the-dependency-agent-on-microsoft-windows"></a>Installieren des Dependency-Agents unter Microsoft Windows

Der Dependency-Agent kann durch Ausführen von `InstallDependencyAgent-Windows.exe` manuell auf Windows-Computern installiert werden. Wenn Sie diese ausführbare Datei ohne Optionen ausführen, wird ein Setup-Assistent gestartet, mit dem Sie die Installation interaktiv durchführen können.  

>[!NOTE]
>Zum Installieren oder Deinstallieren des Agent sind Administratorrechte erforderlich.

Führen Sie die folgenden Schritte aus, um den Dependency-Agent auf jedem Windows-Computer zu installieren:

1.  Installieren Sie den Log Analytics-Agent für Windows mit einer der Methoden, die in der [Übersicht über den Log Analytics-Agent](../../azure-monitor/platform/log-analytics-agent.md) beschrieben werden.
2.  Laden Sie den Windows-Agent herunter, und starten Sie ihn mit dem folgenden Befehl: 
    
    `InstallDependencyAgent-Windows.exe`

3.  Folgen Sie den Anweisungen im Setup-Assistenten, um den Agent zu installieren.
4.  Falls der Dependency-Agent nicht gestartet wird, suchen Sie in den Protokollen nach ausführlichen Fehlerinformationen. Für Windows-Agents lautet das Protokollverzeichnis „%Programfiles%\Microsoft Dependency Agent\logs“. 

#### <a name="windows-command-line"></a>Windows-Befehlszeile

Verwenden Sie Optionen aus der folgenden Tabelle, um über die Befehlszeile zu installieren. Um eine Liste der Installationsflags anzuzeigen, führen Sie das Installationsprogramm wie folgt mit dem Flag „/?“ aus.

    InstallDependencyAgent-Windows.exe /?

| Flag | BESCHREIBUNG |
|:--|:--|
| /? | Ruft eine Liste der Befehlszeilenoptionen ab. |
| /S | Führt eine automatische Installation ohne Benutzereingaben aus. |

Der Standardspeicherort von Dateien für den Dependency-Agent für Windows lautet „C:\Programme\Microsoft Dependency Agent“.

### <a name="install-the-dependency-agent-on-linux"></a>Installieren des Dependency-Agents unter Linux

Der Dependency-Agent wird auf Linux-Computern mit `InstallDependencyAgent-Linux64.bin`, einem Shellskript mit einer selbstextrahierenden Binärdatei, installiert. Sie können die Datei mit `sh` ausführen oder der Datei selbst Ausführungsberechtigungen hinzufügen.

>[!NOTE]
> Zum Installieren oder Konfigurieren des Agent ist Root-Zugriff erforderlich.

Führen Sie die folgenden Schritte aus, um den Dependency-Agent auf jedem Linux-Computer zu installieren:

1.  Installieren Sie den Log Analytics-Agent mit einer der Methoden, die in der [Übersicht über den Log Analytics-Agent](../../azure-monitor/platform/log-analytics-agent.md) beschrieben werden.
2.  Installieren Sie den Dependency-Agent für Linux mit Root-Berechtigungen, indem Sie den folgenden Befehl ausführen:
    
    `sh InstallDependencyAgent-Linux64.bin`

3.  Falls der Dependency-Agent nicht gestartet wird, suchen Sie in den Protokollen nach ausführlichen Fehlerinformationen. Für Linux-Agents lautet das Protokollverzeichnis „/var/opt/microsoft/dependency-agent/log“.

Um eine Liste der Installationsflags anzuzeigen, führen Sie das Installationsprogramm wie folgt mit dem Flag „-help“ aus.

    InstallDependencyAgent-Linux64.bin -help

| Flag | BESCHREIBUNG |
|:--|:--|
| -help | Ruft eine Liste der Befehlszeilenoptionen ab. |
| -s | Führt eine automatische Installation ohne Benutzereingaben aus. |
| --check | Überprüft Berechtigungen und das Betriebssystem, ohne den Agent zu installieren. |

Dateien für den Dependency-Agent werden in den folgenden Verzeichnissen gespeichert:

| Dateien | Location |
|:--|:--|
| Hauptdateien | /opt/microsoft/dependency-agent |
| Protokolldateien | /var/opt/microsoft/dependency-agent/log |
| Konfigurationsdateien | /etc/opt/microsoft/dependency-agent/config |
| Ausführbare Dienstdateien | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binäre Speicherdateien | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Beispiele für Installationsskripts

Mit dem folgenden Skriptbeispiel zum Herunterladen und Installieren des Dependency-Agents unter Windows oder Linux können Sie den Dependency-Agent ganz einfach auf mehreren Servern gleichzeitig bereitstellen.

### <a name="powershell-script-for-windows"></a>PowerShell-Skript für Windows

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Shellskript für Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Desired State Configuration

Zum Bereitstellen des Dependency-Agents über Desired State Configuration (DSC) können Sie das Modul „xPSDesiredStateConfiguration“ mit dem folgenden Beispielcode verwenden:

```powershell
configuration ServiceMap {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage 
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```

## <a name="remove-the-dependency-agent"></a>Entfernen des Dependency-Agents

### <a name="uninstall-agent-on-windows"></a>Deinstallieren des Agents unter Windows

Ein Administrator kann den Dependency-Agent für Windows über die Systemsteuerung deinstallieren.

Ein Administrator kann auch „%Programfiles%\Microsoft Dependency Agent\Uninstall.exe“ ausführen, um den Dependency-Agent zu deinstallieren.

### <a name="uninstall-agent-on-linux"></a>Deinstallieren des Agents unter Linux

Sie können den Dependency-Agent unter Linux mit dem folgenden Befehl deinstallieren.

RHEL, CentOs oder Oracle:

```
sudo rpm -e dependency-agent
```

Ubuntu:

```
sudo apt -y purge dependency-agent
```

## <a name="troubleshooting"></a>Problembehandlung

Wenn beim Installieren oder Ausführen von Service Map Probleme auftreten, finden Sie in diesem Abschnitt Lösungen, wie Sie schnell wieder einsatzbereit sind. Wenn Sie Ihr Problem immer noch nicht beheben können, wenden Sie sich an den Microsoft Support.

### <a name="dependency-agent-installation-problems"></a>Probleme bei der Installation des Dependency-Agents

#### <a name="installer-prompts-for-a-reboot"></a>Installationsprogramm fordert Neustart an

Der Dependency-Agent erfordert *im Allgemeinen* keinen Neustart nach der Installation oder Deinstallation. In bestimmten, seltenen Fällen kann jedoch ein Neustart von Windows Server erforderlich sein, um die Installation fortzusetzen. Dies geschieht, wenn eine Abhängigkeit, in der Regel die verteilbaren Microsoft Visual C++-Dateien, einen Neustart aufgrund einer gesperrten Datei erfordern.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--codenumber-appears"></a>Die Meldung „Der Dependency-Agent kann nicht installiert werden: Fehler bei der Installation der Laufzeitbibliotheken für Visual Studio (code = [Codenummer])“ wird angezeigt.

Der Microsoft Dependency-Agent basiert auf den Microsoft Visual Studio-Laufzeitbibliotheken. Wenn bei der Installation der Bibliotheken ein Problem auftritt, wird eine Meldung angezeigt. 

Die Laufzeitbibliothek-Installationsprogramme erstellen Protokolle im Ordner „%LOCALAPPDATA%\temp“. Die Datei erhält den Namen „dd_vcredist_arch_yyyymmddhhmmss.log“, wobei *arch* für „x86“ oder „amd64“ und *yyyymmddhhmmss* für das Datum und die Uhrzeit der Protokollerstellung (im 24-Stunden-Format) steht. Das Protokoll enthält Details zu dem Problem, das die Installation blockiert.

Es kann hilfreich sein, zuerst selbst die [neuesten Laufzeitbibliotheken](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) zu installieren.

Die folgende Tabelle enthält Codenummern und Lösungsvorschläge.

| Code | BESCHREIBUNG | Lösung |
|:--|:--|:--|
| 0x17 | Für das Bibliothekinstallationsprogramm ist ein Windows-Update erforderlich, das noch nicht installiert wurde. | Suchen Sie im letzten Protokoll des Bibliothekinstallationsprogramms.<br><br>Wenn einem Verweis auf „Windows8.1-KB2999226-x64.msu“ die Zeile „Fehler 0x80240017: Fehler beim Ausführen des MSU-Pakets“ folgt, wurden nicht alle Voraussetzungen für die Installation von KB2999226 erfüllt. Befolgen Sie die Anweisungen im Abschnitt mit den Voraussetzungen in [Universal C Runtime in Windows](https://support.microsoft.com/kb/2999226). Möglicherweise müssen Sie Windows Update ausführen und mehrere Neustarts durchführen müssen, um die Voraussetzungen zu installieren.<br><br>Führen Sie das Installationsprogramm für den Microsoft Dependency-Agent erneut aus. |

### <a name="post-installation-issues"></a>Probleme nach der Installation

#### <a name="server-doesnt-appear-in-service-map"></a>Server wird in Service Map nicht angezeigt

Wenn die Installation des Dependency-Agents erfolgreich war, der Server aber nicht in der Dienstzuordnungslösung angezeigt wird:
* Wurde der Dependency-Agent erfolgreich installiert? Überprüfen Sie, ob der Dienst installiert wurde und ausgeführt wird.<br><br>
**Windows:** Suchen Sie nach dem Dienst „Microsoft Dependency-Agent“.<br>
**Linux:** Suchen Sie nach dem laufenden Prozess „microsoft-dependency-agent“.

* Nutzen Sie den [Free-Tarif von Operations Management Suite/Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)? Der kostenlose Plan („Free“) erlaubt bis zu fünf einzelne Service Map-Server. Alle weiteren Server werden in der Service Map nicht angezeigt, selbst wenn die vorherigen fünf keine Daten mehr senden.

* Sendet Ihr Server Protokoll- und Leistungsdaten an Log Analytics? Wechseln Sie zur Protokollsuche, und führen Sie die folgende Abfrage für Ihren Computer aus: 

    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType

Haben Sie eine Vielzahl von Ereignissen in den Ergebnissen erhalten? Sind die Daten aktuell? Wenn dies der Fall ist, funktioniert Ihr Log Analytics-Agent ordnungsgemäß und kommuniziert mit Log Analytics. Wenn nicht, überprüfen Sie den Agent auf dem Server: [Behandeln von Problemen mit dem Log Analytics-Agent für Windows](https://support.microsoft.com/help/3126513/how-to-troubleshoot-monitoring-onboarding-issues) oder [Behandeln von Problemen beim Linux-Agent für Log Analytics](../../azure-monitor/platform/agent-linux-troubleshoot.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>Der Server wird in Service Map angezeigt, enthält aber keine Prozesse

Wenn Ihr Server in der Dienstzuordnung angezeigt wird, aber keine Prozess- oder Verbindungsdaten enthält, weist dies darauf hin, dass der Dependency-Agent installiert ist und ausgeführt wird, der Kerneltreiber aber nicht geladen wurde. 

Überprüfen Sie die Datei „C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log“ (Windows) bzw. die Datei „/var/opt/microsoft/dependency-agent/log/service.log“ (Linux). Die letzten Zeilen der Datei sollten den Grund angeben, warum der Kernel nicht geladen wurde. Beispielsweise, weil der Kernel nicht unterstützt wird, was unter Linux nach der Aktualisierung des Kernels auftreten kann.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie nach Bereitstellung und Konfiguration [Service Map verwenden]( service-map.md) können.
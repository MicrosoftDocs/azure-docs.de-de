---
title: Wire Data-Lösung in Azure Monitor | Microsoft-Dokumentation
description: Daten zur Kommunikation sind konsolidierte Netzwerk- und Leistungsdaten von Computern mit Log Analytics-Agents. Netzwerkdaten werden mit Ihren Protokolldaten kombiniert, um Ihnen das Korrelieren von Daten zu ermöglichen.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: fc3d7127-0baa-4772-858a-5ba995d1519b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/03/2018
ms.author: magoedte
ms.openlocfilehash: b7968de8ad4357241eb8d86d51e991f4bbab959d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65601574"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor"></a>Wire Data 2.0-Lösung (Vorschauversion) in Azure Monitor

![Wire Data-Symbol](media/wire-data/wire-data2-symbol.png)

Daten zur Kommunikation sind konsolidierte Netzwerk- und Leistungsdaten, die von mit Windows verbundenen und mit Linux verbundenen Computern mit dem Log Analytics-Agent gesammelt werden, einschließlich der in Ihrer Umgebung von Operations Manager überwachten Computer. Netzwerkdaten werden mit Ihren sonstigen Protokolldaten kombiniert, um Ihnen das Korrelieren von Daten zu ermöglichen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Zusätzlich zum Log Analytics-Agent verwendet die Wire Data-Lösung Microsoft Dependency-Agents, die Sie auf Computern in Ihrer IT-Infrastruktur installieren. Dependency-Agents überwachen Netzwerkdaten, die für die Netzwerkschichten 2 und 3 des [OSI-Modells](https://en.wikipedia.org/wiki/OSI_model) an ihre Computer und von ihnen gesendet werden, z.B. die verschiedenen verwendeten Protokolle und Ports. Die Daten werden dann mit Agents an Azure Monitor gesendet.  

>[!NOTE]
>Sie haben die Dienstzuordnung bereits bereitgestellt oder möchten die Dienstzuordnung bzw. [Azure Monitor für VMs](../../azure-monitor/insights/vminsights-overview.md) verwenden? Es gibt ein neues Verbindungsmetrik-Dataset, das gesammelt und in Azure Monitor gespeichert wird und ähnliche Informationen wie Wire Data zur Verfügung stellt.

Standardmäßig protokolliert Azure Monitor Daten für CPU, Arbeitsspeicher, Datenträger sowie Netzwerkleistungsdaten von Leistungsindikatoren, die in Windows und Linux integriert sind. Zudem werden Daten von anderen Leistungsindikatoren protokolliert, die Sie angeben können. Die Erfassung von Netzwerkdaten und anderen Daten wird für jeden Agent in Echtzeit durchgeführt, einschließlich der vom Computer verwendeten Subnetze und Anwendungsebenenprotokolle.  Wire Data untersucht Netzwerkdaten auf Anwendungsebene und nicht unten auf der TCP-Transportebene.  Die Lösung betrachtet keine einzelnen ACKs und SYNs.  Sobald der Handshake abgeschlossen ist, wird er als Liveverbindung betrachtet und als „Verbunden“ gekennzeichnet. Diese Liveverbindung bleibt so lange bestehen, wie beide Seiten zustimmen, dass der Socket offen ist und Daten in beide Richtungen übertragen werden können.  Sobald eine Seite die Verbindung schließt, wird sie als „Getrennt“ markiert.  Aus diesem Grund wird nur die Bandbreite von erfolgreich abgeschlossenen Paketen gezählt, während erneut gesendete oder fehlerhafte Pakete nicht gemeldet werden.

Wenn Sie [sFlow](http://www.sflow.org/) oder andere Software mit dem [NetFlow-Protokoll von Cisco](https://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html) verwendet haben, werden Ihnen die in den Daten zur Kommunikation angezeigten Statistiken und Daten bekannt vorkommen.

Beispiele für die Typen von integrierten Abfragen von Protokollsuchen:

- Agents, die Wire Data bereitstellen
- IP-Adresse der Agents, die Wire Data bereitstellen
- Ausgehende Kommunikation über IP-Adressen
- Anzahl von gesendeten Bytes nach Anwendungsprotokollen
- Anzahl von gesendeten Bytes eines Anwendungsdiensts
- Von unterschiedlichen Protokollen empfangene Bytes
- Gesamte Bytes, die von einer IP-Version gesendet und empfangen wurden
- Durchschnittliche Latenz für Verbindungen mit zuverlässiger Messung
- Computerprozesse, die Netzwerk-Datenverkehr initiiert oder empfangen haben
- Menge des Netzwerk-Datenverkehrs für einen Prozess

Wenn Sie mit Wire Data eine Suche durchführen, können Sie Daten filtern und gruppieren, um Informationen zu den wichtigsten Agents und Protokollen anzuzeigen. Außerdem können Sie sich ansehen, wann bestimmte Computer (IP-Adressen/MAC-Adressen) miteinander kommuniziert haben, wie lange dies gedauert hat und wie viele Daten gesendet wurden. Im Grunde genommen zeigen Sie die Metadaten zum Netzwerkdatenverkehr an. Dies ist ein suchbasierter Vorgang.

Da Sie Metadaten anzeigen, ist der Vorgang nicht unbedingt gut für die eingehende Problembehandlung geeignet. Bei Wire Data in Azure Monitor wird keine vollständige Erfassung aller Netzwerkdaten durchgeführt.  Die eingehende Problembehandlung auf Paketebene ist nicht vorgesehen. Der Vorteil einer Verwendung des Agents gegenüber anderen Erfassungsmethoden besteht darin, dass Sie keine Appliances installieren, Netzwerkswitches neu konfigurieren oder komplizierten Konfigurationen durchführen müssen. Wire Data ist einfach Agent-basiert. Sie installieren den Agent auf einem Computer, um damit den Netzwerkdatenverkehr zu überwachen. Ein weiterer Vorteil ergibt sich, wenn Sie Workloads überwachen möchten, die bei Cloudanbietern, Hostinganbietern oder unter Microsoft Azure ausgeführt werden und bei denen sich die Fabric-Ebene nicht im Besitz des Benutzers befindet.

## <a name="connected-sources"></a>Verbundene Quellen

Wire Data ruft ihre Daten vom Microsoft Dependency-Agent ab. Der Dependency-Agent ist abhängig vom Log Analytics-Agent, da er dessen Verbindungen mit Azure Monitor benötigt. Dies bedeutet, dass auf einem Server der Log Analytics-Agent mit dem Dependency-Agent installiert und konfiguriert sein muss. In der folgenden Tabelle sind die verbundenen Quellen beschrieben, die von Wire Data unterstützt werden.

| **Verbundene Quelle** | **Unterstützt** | **Beschreibung** |
| --- | --- | --- |
| Windows-Agents | Ja | Wire Data analysiert und erfasst Daten von Windows-Agent-Computern. <br><br> Zusätzlich zum [Log Analytics-Agent für Windows](../../azure-monitor/platform/agent-windows.md) benötigen Windows-Agents den Microsoft Dependency-Agent. Eine vollständige Liste der Betriebssystemversionen finden Sie unter [Unterstützte Betriebssysteme](../../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems). |
| Linux-Agents | Ja | Wire Data analysiert und erfasst Daten von Linux-Agent-Computern.<br><br> Zusätzlich zum [Log Analytics-Agent für Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) benötigen Linux-Agents den Microsoft Dependency-Agent. Eine vollständige Liste der Betriebssystemversionen finden Sie unter [Unterstützte Betriebssysteme](../../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems). |
| System Center Operations Manager-Verwaltungsgruppe | Ja | Wire Data analysiert und erfasst Daten von Windows- und Linux-Agents in einer verbundenen [System Center Operations Manager-Verwaltungsgruppe](../../azure-monitor/platform/om-agents.md). <br><br> Es ist eine direkte Verbindung des System Center Operations Manager-Agents mit Azure Monitor erforderlich. |
| Azure-Speicherkonto | Nein | Da Wire Data Daten von Agent-Computern erfasst, sind keine Daten aus Azure Storage zu erfassen. |

Unter Windows wird der Microsoft Monitoring Agent (MMA) von System Center Operations Manager und Azure Monitor zum Erfassen und Senden von Daten verwendet. Dieser Agent wird je nach Kontext als System Center Operations Manager-Agent, Log Analytics-Agent, MMA oder Direkt-Agent bezeichnet. System Center Operations Manager und Azure Monitor bieten leicht unterschiedliche Versionen der MMA. Jede dieser Versionen kann Berichte an System Center Operations Manager, Azure Monitor oder beide senden.

Unter Linux erfasst der Log Analytics-Agent für Linux Daten und sendet sie an Azure Monitor. Sie können Wire Data auf Servern mit Agents verwenden, die direkt mit Azure Monitor verbunden sind, oder auf Servern, die über System Center Operations Manager-Verwaltungsgruppen mit Azure Monitor verbunden sind.

Der Dependency-Agent selbst überträgt keine Daten und erfordert keine Änderungen an Firewalls oder Ports. Die Daten in Wire Data werden immer vom Log Analytics-Agent an Azure Monitor übertragen, entweder direkt oder über das Log Analytics-Gateway.

![Agent-Diagramm](./media/wire-data/agents.png)

Wenn Sie ein System Center Operations Manager-Benutzer mit einer Verwaltungsgruppe sind, die mit Azure Monitor verbunden ist:

- Wenn die System Center Operations Manager-Agents die Verbindung mit Azure Monitor über das Internet herstellen können, ist keine weitere Konfiguration erforderlich.
- Wenn die System Center Operations Manager-Agents nicht über das Internet auf Azure Monitor zugreifen können, müssen Sie das Log Analytics-Gateway für System Center Operations Manager konfigurieren.

Wenn Ihre Windows- oder Linux-Computer nicht direkt mit dem Dienst verbunden werden können, müssen Sie den Log Analytics-Agent so konfigurieren, dass er die Verbindung mit Azure Monitor über das Log Analytics-Gateway herstellt. Sie können das Log Analytics-Gateway aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=52666) herunterladen.

## <a name="prerequisites"></a>Voraussetzungen

- Das [Insight & Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing)-Lösungsangebot ist erforderlich.
- Wenn Sie die vorherige Version der Wire Data-Lösung verwenden, müssen Sie sie zuerst entfernen. Alle über die ursprüngliche Wire Data-Lösung erfassten Daten stehen jedoch noch in Wire Data 2.0 und der Protokollsuche zur Verfügung.
- Zum Installieren oder Deinstallieren des Dependency-Agents sind Administratorrechte erforderlich.
- Der Dependency-Agent muss auf einem Computer mit einem 64-Bit-Betriebssystem installiert werden.

### <a name="operating-systems"></a>Betriebssysteme

In den folgenden Abschnitten sind die unterstützten Betriebssysteme für den Dependency-Agent aufgeführt. 32-Bit-Architekturen werden von Wire Data für kein Betriebssystem unterstützt.

#### <a name="windows-server"></a>Windows Server

- Windows Server 2019
- Windows Server 2016 1803
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Windows Desktop

- Windows 10 1803
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="supported-linux-operating-systems"></a>Unterstützte Linux-Betriebssysteme
In den folgenden Abschnitten sind die unterstützten Betriebssysteme für den Dependency-Agent unter Linux aufgeführt.  

- Es werden nur die Standardversion und SMP-Version des Linux-Kernels unterstützt.
- Nicht-Standardversionen des Kernels, z.B. PAE und Xen, werden für keine Linux-Distribution unterstützt. Beispielsweise wird ein System mit der Versionszeichenfolge „2.6.16.21-0.8-xen“ nicht unterstützt.
- Benutzerdefinierte Kernels, einschließlich Neukompilierungen von Standardkernels, werden nicht unterstützt.

##### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Betriebssystemversion | Kernelversion |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7,5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

##### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Betriebssystemversion | Kernelversion |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

##### <a name="centosplus"></a>CentOSPlus
| Betriebssystemversion | Kernelversion |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

##### <a name="ubuntu-server"></a>Ubuntu Server

| Betriebssystemversion | Kernelversion |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15.\*<br>4.18* |
| Ubuntu 16.04.3 | Kernel 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

##### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| Betriebssystemversion | Kernelversion
|:--|:--|
| 11 SP4 | 3.0.* |

##### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Betriebssystemversion | Kernelversion
|:--|:--|
| 12 SP2 | 4.4.* |
| 12 SP3 | 4.4.* |

### <a name="dependency-agent-downloads"></a>Dependency-Agent – Downloads

| Datei | Betriebssystem | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |



## <a name="configuration"></a>Konfiguration

Führen Sie die folgenden Schritte aus, um die Wire Data-Lösung für Ihre Arbeitsbereiche zu konfigurieren.

1. Aktivieren Sie die Log Analytics-Lösung für Aktivitätsprotokolle in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) oder entsprechend den unter [Hinzufügen von Überwachungslösungen aus dem Lösungskatalog](../../azure-monitor/insights/solutions.md) beschriebenen Schritten.
2. Installieren Sie den Dependency-Agent auf jedem Computer, von dem Sie Daten abrufen möchten. Der Dependency-Agent kann Verbindungen zu unmittelbaren Nachbarn überwachen, sodass Sie nicht auf jedem Computer einen Agent benötigen.

> [!NOTE]
> Sie können die vorherige Version der Wire Data-Lösung nicht neuen Arbeitsbereichen hinzufügen. Wenn Sie die ursprüngliche Wire Data-Lösung aktiviert haben, können Sie diese Lösung weiterhin verwenden. Um jedoch Wire Data 2.0 zu verwenden, müssen Sie zuerst die ursprüngliche Version entfernen.
> 
 
### <a name="install-the-dependency-agent-on-windows"></a>Installieren des Dependency-Agents unter Windows

Zum Installieren oder Deinstallieren des Agent sind Administratorrechte erforderlich.

Der Dependency-Agent wird auf Windows-Computern mithilfe von „InstallDependencyAgent-Windows.exe“ installiert. Wenn Sie diese ausführbare Datei ohne Optionen ausführen, wird ein Assistent gestartet, mit dem Sie die Installation interaktiv ausführen können.

Führen Sie die folgenden Schritte aus, um den Dependency-Agent auf jedem Windows-Computer zu installieren:

1. Installieren Sie den Log Analytics-Agent mithilfe der in [Sammeln von Daten von Windows-Computern, die in Ihrer Umgebung gehostet werden](../../azure-monitor/platform/agent-windows.md) aufgeführten Schritte.
2. Laden Sie den Dependency-Agent von Windows über den Link aus dem vorherigen Abschnitt herunter, und führen Sie ihn dann mithilfe des folgenden Befehls aus: `InstallDependencyAgent-Windows.exe`.
3. Folgen Sie den Anweisungen des Assistenten, um den Assistenten zu installieren.
4. Wenn der Dependency-Agent nicht gestartet wird, suchen Sie in den Protokollen ausführliche Fehlerinformationen. Für Windows-Agents lautet das Protokollverzeichnis „%Programfiles%\Microsoft Dependency Agent\logs“.

#### <a name="windows-command-line"></a>Windows-Befehlszeile

Verwenden Sie Optionen aus der folgenden Tabelle, um über die Befehlszeile zu installieren. Um eine Liste der Installationsflags anzuzeigen, führen Sie das Installationsprogramm wie folgt mit dem Flag „/?“ aus.

InstallDependencyAgent-Windows.exe /?

| **Flag** | **Beschreibung** |
| --- | --- |
| <code>/?</code> | Ruft eine Liste der Befehlszeilenoptionen ab. |
| <code>/S</code> | Führt eine automatische Installation ohne Benutzereingaben aus. |

Der Standardspeicherort von Dateien des Dependency-Agents für Windows lautet „C:\Programme\Microsoft Dependency Agent“.

### <a name="install-the-dependency-agent-on-linux"></a>Installieren des Dependency-Agents unter Linux

Zum Installieren oder Konfigurieren des Agent ist Root-Zugriff erforderlich.

Der Dependency-Agent wird auf Linux-Computern mit „InstallDependencyAgent-Linux64.bin“ installiert, einem Shellskript mit einer selbstextrahierenden Binärdatei. Sie können die Datei mit _sh_ ausführen oder der Datei selbst Ausführungsberechtigungen hinzufügen.

Führen Sie die folgenden Schritte aus, um den Dependency-Agent auf jedem Linux-Computer zu installieren:

1. Installieren Sie den Log Analytics-Agent mithilfe der in [Sammeln von Daten von Linux-Computern, die in Ihrer Umgebung gehostet werden](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key) aufgeführten Schritte.
2. Laden Sie den Dependency-Agent von Linux über den Link aus dem vorherigen Abschnitt herunter, und installieren Sie ihn dann mithilfe des folgenden Befehls als Root: „InstallDependencyAgent-Linux64.bin“.
3. Wenn der Dependency-Agent nicht gestartet wird, suchen Sie in den Protokollen ausführliche Fehlerinformationen. Für Linux-Agents lautet das Protokollverzeichnis: „/var/opt/microsoft/dependency-agent/log“.

Um eine Liste der Installationsflags anzuzeigen, führen Sie das Installationsprogramm wie folgt mit dem `-help`-Flag aus.

```
InstallDependencyAgent-Linux64.bin -help
```

| **Flag** | **Beschreibung** |
| --- | --- |
| <code>-help</code> | Ruft eine Liste der Befehlszeilenoptionen ab. |
| <code>-s</code> | Führt eine automatische Installation ohne Benutzereingaben aus. |
| <code>--check</code> | Überprüft Berechtigungen und das Betriebssystem, ohne den Agent zu installieren. |

Dateien für den Dependency-Agent befinden sich in den folgenden Verzeichnissen:

| **Dateien** | **Location** |
| --- | --- |
| Hauptdateien | /opt/microsoft/dependency-agent |
| Protokolldateien | /var/opt/microsoft/dependency-agent/log |
| Konfigurationsdateien | /etc/opt/microsoft/dependency-agent/config |
| Ausführbare Dienstdateien | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binäre Speicherdateien | /var/opt/microsoft/dependency-agent/storage |

### <a name="installation-script-examples"></a>Beispiele für Installationsskripts

Der Dependency-Agent kann mithilfe eines Skripts auf mehreren Servern gleichzeitig bereitgestellt werden. Sie können die folgenden Skriptbeispiele verwenden, um den Dependency-Agent herunterzuladen und unter Windows oder Linux zu installieren.

#### <a name="powershell-script-for-windows"></a>PowerShell-Skript für Windows

```powershell

Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S

```

#### <a name="shell-script-for-linux"></a>Shellskript für Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
```

```
sh InstallDependencyAgent-Linux64.bin -s
```

### <a name="desired-state-configuration"></a>Desired State Configuration

Zum Bereitstellen des Dependency-Agents über Desired State Configuration können Sie das Modul „xPSDesiredStateConfiguration“ und ein paar Codezeilen wie die folgenden verwenden:

```powershell
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"



Node $NodeName

{

    # Download and install the Dependency Agent

    xRemoteFile DAPackage

    {

        Uri = "https://aka.ms/dependencyagentwindows"

        DestinationPath = $DAPackageLocalPath

        DependsOn = "[Package]OI"

    }

    xPackage DA

    {

        Ensure = "Present"

        Name = "Dependency Agent"

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = ""

        InstalledCheckRegKey = "HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"

        InstalledCheckRegValueName = "DisplayName"

        InstalledCheckRegValueData = "Dependency Agent"

    }

}

```

### <a name="uninstall-the-dependency-agent"></a>Deinstallieren des Dependency-Agents

In den folgenden Abschnitten erfahren Sie, wie Sie den Dependency-Agent entfernen können.

#### <a name="uninstall-the-dependency-agent-on-windows"></a>Deinstallieren des Dependency-Agents unter Windows

Ein Administrator kann den Dependency-Agent für Windows über die Systemsteuerung deinstallieren.

Ein Administrator kann auch „%Programfiles%\Microsoft Dependency Agent\Uninstall.exe“ ausführen, um den Dependency-Agent zu deinstallieren.

#### <a name="uninstall-the-dependency-agent-on-linux"></a>Deinstallieren des Dependency-Agents unter Linux

Wenn Sie den Dependency-Agent unter Linux vollständig deinstallieren möchten, müssen Sie den Agent selbst und den Connector entfernen, der automatisch mit dem Agent installiert wird. Mit dem folgenden einzelnen Befehl können Sie beide Komponenten deinstallieren:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>Management Packs

Wenn Wire Data in einem Log Analytics-Arbeitsbereich aktiviert ist, wird an alle Windows-Server in diesem Arbeitsbereich ein Management Pack von 300 KB gesendet. Wenn Sie System Center Operations Manager-Agents in einer [verbundenen Verwaltungsgruppe](../platform/om-agents.md) verwenden, wird das Dependency Monitor-Management Pack von System Center Operations Manager bereitgestellt. Wenn die Agents direkt verbunden sind, wird das Management Pack von Azure Monitor bereitgestellt.

Der Name des Management Packs lautet „Microsoft.IntelligencePacks.ApplicationDependencyMonitor“. Es wird in das Verzeichnis „%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\“ geschrieben. Die vom Management Pack verwendete Datenquelle lautet „%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll“.

## <a name="using-the-solution"></a>Verwenden der Lösung

Verwenden Sie die folgenden Informationen zum Installieren und Konfigurieren der Lösung.

- Mit der Wire Data-Lösung werden Daten von Computern erfasst, auf denen Windows Server 2012 R2, Windows 8.1 und neuere Betriebssysteme ausgeführt werden.
- Microsoft .NET Framework 4.0 oder höher ist auf Computern erforderlich, von denen Wire Data-Daten erfasst werden sollen.
- Fügen Sie mithilfe des unter [Hinzufügen von Überwachungslösungen aus dem Lösungskatalog](solutions.md) beschriebenen Prozesses die Wire Data-Lösung Ihrem Log Analytics-Arbeitsbereich hinzu. Es ist keine weitere Konfiguration erforderlich.
- Wenn Sie Wire Data-Daten für eine bestimmte Lösung anzeigen möchten, muss die Lösung bereits Ihrem Arbeitsbereich hinzugefügt worden sein.

Nachdem Sie Agents installiert haben, und Sie die Lösung installieren, wird die Wire Data 2.0-Kachel im Arbeitsbereich angezeigt.

![Wire Data-Kachel](./media/wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>Verwenden der Wire Data 2.0-Lösung

Klicken Sie im Azure-Portal auf der Seite **Übersicht** für Ihren Log Analytics-Arbeitsbereich auf die Kachel **Wire Data 2.0**, um das Wire Data-Dashboard zu öffnen. Das Dashboard enthält die Blätter, die in der folgenden Tabelle angegeben sind. Auf jedem Blatt sind bis zu 10 Einträge aufgeführt, die die Kriterien des Blatts für den angegebenen Bereich und Zeitraum erfüllen. Sie können eine Protokollsuche durchführen, mit der alle Einträge zurückgegeben werden, indem Sie unten auf dem Blatt auf **Alle anzeigen** oder auf die Blattüberschrift klicken.

| **Blatt** | **Beschreibung** |
| --- | --- |
| Agents, die Netzwerkdatenverkehr erfassen | Zeigt die Anzahl der Agents an, die Netzwerkdatenverkehr erfassen, und listet die 10 Computer auf, die am meisten Datenverkehr erfassen. Klicken Sie auf die Zahl, um eine Protokollsuche für <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code> durchzuführen. Klicken Sie auf einen Computer in der Liste, um eine Protokollsuche durchzuführen, die die Gesamtanzahl erfasster Bytes zurückgibt. |
| Lokale Subnetze | Zeigt die Anzahl von lokalen Subnetzen an, die Agents ermittelt haben.  Klicken Sie auf die Anzahl, um eine Protokollsuche für <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code> durchzuführen, die alle Subnetze mit der Anzahl von Bytes auflistet, die über jedes einzelne gesendet werden. Klicken Sie auf ein Subnetz in der Liste, um eine Protokollsuche durchzuführen, die die Gesamtanzahl über das Subnetz gesendeter Bytes zurückgibt. |
| Protokolle auf Anwendungsebene | Zeigt die von Agents ermittelte Anzahl der auf Anwendungsebene verwendeten Protokolle. Klicken Sie auf die Zahl, um eine Protokollsuche für <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code> durchzuführen. Klicken Sie auf ein Protokoll, um eine Protokollsuche durchzuführen, die die Gesamtanzahl mithilfe des Protokolls gesendeter Bytes zurückgibt. |

![Wire Data-Dashboard](./media/wire-data/wire-data-dash.png)

Mit dem Blatt **Agents, die Netzwerkdatenverkehr erfassen** können Sie bestimmen, wie viel Netzwerkbandbreite von Computern in Anspruch genommen wird. Mit diesem Blatt können Sie leicht den _„geschwätzigsten“_ Computer in Ihrer Umgebung ermitteln. Solche Computer könnten überlastet sein, nicht normal arbeiten oder mehr Netzwerkressourcen als normal verwenden.

![Beispiel: Protokollsuche](./media/wire-data/log-search-example01.png)

Auf ähnliche Weise können Sie das Blatt **Lokale Subnetze** verwenden, um zu bestimmen, wie viel Netzwerkdatenverkehr über die Subnetze verschoben wird. Benutzer definieren Subnetze oft um kritische Bereiche für ihre Anwendungen herum. Dieses Blatt bietet einen Einblick in die Bereiche.

![Beispiel: Protokollsuche](./media/wire-data/log-search-example02.png)

Das Blatt **Protokolle auf Anwendungsebene** ist nützlich, da es hilfreich ist, zu wissen, welche Protokolle verwendet werden. Sie könnten z.B. erwarten, dass SSH nicht in Ihrer Netzwerkumgebung verwendet wird. Die im Blatt angezeigten Informationen können Ihre Erwartungen schnell bestätigen oder widerlegen.

![Beispiel: Protokollsuche](./media/wire-data/log-search-example03.png)

Es ist auch hilfreich zu wissen, ob der Protokolldatenverkehr mit der Zeit steigt oder sinkt. Wenn z.B. die Menge der von einer Anwendung übertragenen Daten steigt, dann könnte dies ein Aspekt sein, über den Sie informiert sein müssen, oder den Sie erwähnenswert finden.

## <a name="input-data"></a>Eingabedaten

Wire Data sammelt Metadaten über den Netzwerkverkehr mit den Agents, die Sie aktiviert haben. Jeder Agent sendet alle 15 Sekunden Daten.

## <a name="output-data"></a>Ausgabedaten

Ein Datensatz mit dem Typ _WireData_ wird für jeden Eingabedatentyp erstellt. Die Eigenschaften der WireData-Datensätze sind in der folgenden Tabelle aufgeführt:

| Eigenschaft | BESCHREIBUNG |
|---|---|
| Computer | Name des Computers, auf dem Daten gesammelt wurden |
| TimeGenerated | Uhrzeit des Datensatzes |
| LocalIP | IP-Adresse des lokalen Computers |
| SessionState | Verbunden oder getrennt |
| ReceivedBytes | Summe empfangener Bytes |
| ProtocolName | Name des verwendeten Netzwerkprotokolls |
| IPVersion | IP-Version |
| Direction | Eingehend oder ausgehend |
| MaliciousIP | IP-Adresse einer bekannten schädlichen Quelle |
| Severity | Vermuteter Malwareschweregrad |
| RemoteIPCountry | Land/Region der Remote-IP-Adresse |
| ManagementGroupName | Name der Operations Manager-Verwaltungsgruppe |
| SourceSystem | Quelle, auf der Daten gesammelt wurden |
| SessionStartTime | Startzeit der Sitzung |
| SessionEndTime | Endzeit der Sitzung |
| LocalSubnet | Subnetz, in dem Daten gesammelt wurden |
| LocalPortNumber | Lokale Portnummer |
| RemoteIP | Vom Remotecomputer verwendete Remote-IP-Adresse |
| RemotePortNumber | Von der Remote-IP-Adresse verwendete Portnummer |
| SessionID | Ein eindeutiger Wert, der die Kommunikationssitzung zwischen zwei IP-Adressen identifiziert |
| SentBytes | Anzahl der gesendeten Bytes |
| TotalBytes | Gesamtanzahl der während der Sitzung gesendeten Bytes |
| ApplicationProtocol | Typ des verwendeten Netzwerkprotokolls   |
| ProcessID | Windows-Prozess-ID |
| ProcessName | Pfad und Dateiname des Prozesses |
| RemoteIPLongitude | IP-Längengradwert |
| RemoteIPLatitude | IP-Breitengradwert |


## <a name="next-steps"></a>Nächste Schritte

- [Durchsuchen von Protokollen](../../azure-monitor/log-query/log-query-overview.md) und darüber, wie Sie ausführliche Wire Data-Suchdatensätze anzeigen.
---
title: Grundlegende Informationen zu den Windows-Agent-Überprüfungsergebnissen in der Azure-Updateverwaltung
description: Erfahren Sie, wie Sie Fehler mit dem Updateverwaltungs-Agent beheben können.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 864fe70d7702680f21234a1a15c02515b19f770b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60597712"
---
# <a name="understand-the-windows-agent-check-results-in-update-management"></a>Grundlegende Informationen zu den Windows-Agent-Überprüfungsergebnissen in der Updateverwaltung

Es gibt viele mögliche Gründe dafür, dass Ihr Computer in der Updateverwaltung nicht als **Bereit** angezeigt wird. In der Updateverwaltung können Sie die Integrität eines Hybrid Worker-Agents überprüfen, um das zugrunde liegende Problem zu ermitteln. In diesem Artikel wird erläutert, wie Sie die Problembehandlung für Azure-Computer im Azure-Portal und für Azure-fremde Computer im [Offlineszenario](#troubleshoot-offline) ausführen.

In der folgende Liste finden Sie die drei Bereitschaftszustände, in denen sich ein Computer befinden kann:

* **Bereit**: Der Update-Agent ist bereitgestellt und wurde vor weniger als 1 Stunde zuletzt angezeigt.
* **Getrennt**: Der Update-Agent ist bereitgestellt und wurde vor mehr als 1 Stunde zuletzt angezeigt.
* **Nicht konfiguriert**: Der Update-Agent wurde nicht gefunden oder hat das Onboarding noch nicht abgeschlossen.

> [!NOTE]
> Möglicherweise gibt es eine kleine Verzögerung zwischen der Anzeige des Azure-Portals und dem aktuellen Zustand des Computers.

## <a name="start-the-troubleshooter"></a>Starten der Problembehandlung

Klicken Sie für Azure-Computer im Portal unter der Spalte **Bereitschaft des Update-Agents** auf den Link **Problembehandlung**, um die Seite **Problembehandlung von Update-Agent** zu öffnen. Bei Azure-fremden Computern gelangen Sie über den Link zu diesem Artikel. In der [Offlineanleitung](#troubleshoot-offline) erfahren Sie, wie Sie Probleme mit einem Azure-fremden Computer behandeln.

![Updateverwaltung: Liste von virtuellen Computern](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Um die Integrität eines Agents zu überprüfen, muss der virtuelle Computer ausgeführt werden. Wenn der virtuelle Computer nicht ausgeführt wird, wird die Schaltfläche **Start the VM** (Virtuellen Computer starten) angezeigt.

Wählen Sie auf der Seite **Problembehandlung von Update-Agent** die Option **Überprüfungen ausführen**, um mit der Problembehandlung zu beginnen. Die Problembehandlung verwendet [Befehl ausführen](../../virtual-machines/windows/run-command.md), um ein Skript auf dem Computer auszuführen, mit dem Agent-Abhängigkeiten überprüft werden. Wenn die Problembehandlung abgeschlossen ist, werden die Ergebnisse der Überprüfungen zurückgegeben.

![Seite „Problembehandlung von Update-Agent“](../media/update-agent-issues/troubleshoot-page.png)

Ergebnisse werden bei Verfügbarkeit auf der Seite angezeigt. In den Überprüfungsabschnitten wird aufgeführt, was in den einzelnen Überprüfungen enthalten ist.

![Überprüfungen bei „Problembehandlung von Update-Agent“](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Voraussetzungsprüfungen

### <a name="operating-system"></a>Betriebssystem

Die Betriebssystemprüfung untersucht, ob der Hybrid Runbook Worker unter einem der folgenden Betriebssysteme ausgeführt wird:

|Betriebssystem  |Notizen  |
|---------|---------|
|Windows Server 2008 R2 RTM, Windows Server 2008 | Unterstützt nur Updatebewertungen.         |
|Windows Server 2008 R2 SP1 und höher |.NET Framework 4.5.1 oder höher ist erforderlich. ([.NET Framework herunterladen](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 4.0 oder höher ist erforderlich. ([Windows Management Framework 4.0 herunterladen](https://www.microsoft.com/download/details.aspx?id=40855))<br/> Für eine höhere Zuverlässigkeit wird Windows PowerShell 5.1 empfohlen.  ([Windows Management Framework 5.1 herunterladen](https://www.microsoft.com/download/details.aspx?id=54616))        |

### <a name="net-451"></a>.NET 4.5.1

Bei der .NET Framework-Überprüfung wird untersucht, ob auf dem System mindestens [.NET Framework 4.5.1](https://www.microsoft.com/download/details.aspx?id=30653) installiert ist.

### <a name="wmf-51"></a>WMF 5.1

Bei der WMF-Überprüfung wird untersucht, ob auf dem System die erforderliche Version des Windows Management Framework (WMF) installiert ist. [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) ist die niedrigste unterstützte Version. Es wird empfohlen, [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616) zu installieren, um die Zuverlässigkeit des Hybrid Runbook Workers zu erhöhen.

### <a name="tls-12"></a>TLS 1.2

Bei dieser Überprüfung wird untersucht, ob Sie zum Verschlüsseln der Kommunikation TLS 1.2 verwenden. TLS 1.0 wird von der Plattform nicht mehr unterstützt. Es wird empfohlen, dass Clients für die Kommunikation mit der Updateverwaltung TLS 1.2 verwenden.

## <a name="connectivity-checks"></a>Konnektivitätsprüfungen

### <a name="registration-endpoint"></a>Registrierungsendpunkt

Bei dieser Überprüfung wird untersucht, ob der Agent ordnungsgemäß mit dem Agent-Dienst kommunizieren kann.

Die Proxy- und Firewallkonfigurationen müssen die Kommunikation des Hybrid Runbook Worker-Agents mit dem Registrierungsendpunkt zulassen. Eine Liste der Adressen und zu öffnenden Ports finden Sie unter [Netzwerkplanung für Hybrid Worker](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Endpunkt für Vorgänge

Bei dieser Überprüfung wird untersucht, ob der Agent ordnungsgemäß mit dem Auftragsruntime-Datendienst kommunizieren kann.

Die Proxy- und Firewallkonfigurationen müssen die Kommunikation des Hybrid Runbook Worker-Agents mit dem Auftragsruntime-Datendienst zulassen. Eine Liste der Adressen und zu öffnenden Ports finden Sie unter [Netzwerkplanung für Hybrid Worker](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>Integritätsprüfungen für den VM-Dienst

### <a name="monitoring-agent-service-status"></a>Status des Monitoring Agent-Diensts

Bei dieser Überprüfung wird untersucht, ob Microsoft Monitoring Agent, `HealthService`, auf dem Computer ausgeführt wird.

Weitere Informationen zur Behandlung von Problemen mit dem Dienst finden Sie unter [Der Microsoft Monitoring Agent wird nicht ausgeführt](hybrid-runbook-worker.md#mma-not-running).

Informationen zum erneuten Installieren von Microsoft Monitoring Agent finden Sie unter [Installieren und Konfigurieren von Microsoft Monitoring Agent](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

### <a name="monitoring-agent-service-events"></a>Ereignisse des Monitoring Agent-Diensts

Bei dieser Überprüfung wird untersucht, ob für die letzten 24 Stunden `4502`-Ereignisse im Azure Operations Manager-Protokoll auf dem Computer zu finden sind.

Weitere Informationen zu diesem Ereignis finden Sie im [Leitfaden zur Problembehandlung](hybrid-runbook-worker.md#event-4502) für dieses Ereignis.

## <a name="access-permissions-checks"></a>Überprüfung von Zugriffsberechtigungen

### <a name="machinekeys-folder-access"></a>Zugriff auf MachineKeys-Ordner

Bei der Überprüfung des Zugriffs auf den Crypto-Ordner wird untersucht, ob das lokale Systemkonto auf „C:\ProgramData\Microsoft\Crypto\RSA“ zugreifen kann.

## <a name="troubleshoot-offline"></a>Offlineproblembehandlung

Sie können die Problembehandlung offline auf einem Hybrid Runbook Worker ausführen, indem Sie das Skript lokal ausführen. Sie finden das Skript [Troubleshoot-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration) im PowerShell-Katalog. Die Ausgabe dieses Skripts ähnelt dem folgenden Beispiel:

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.1.7601 (Windows Server 2008 R2 SP1) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .NET Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             : 
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Microsoft Monitoring Agent, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Microsoft Monitoring Agent, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Behandlung weiterer Probleme mit Ihren Hybrid Runbook Workern finden Sie unter [Problembehandlung für Hybrid Runbook Worker](hybrid-runbook-worker.md).


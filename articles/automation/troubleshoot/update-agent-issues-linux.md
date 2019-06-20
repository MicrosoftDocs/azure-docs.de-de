---
title: Grundlegende Informationen zu den Linux-Agent-Überprüfungsergebnissen in der Azure-Updateverwaltung
description: Erfahren Sie, wie Sie Fehler mit dem Updateverwaltungs-Agent beheben können.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 075cf254dbf7c5d03f1afac64315f6c6f773159c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60597775"
---
# <a name="understand-the-linux-agent-check-results-in-update-management"></a>Grundlegende Informationen zu den Linux-Agent-Überprüfungsergebnissen in der Updateverwaltung

Es gibt viele mögliche Gründe dafür, dass Ihr Computer in der Updateverwaltung nicht als **Bereit** angezeigt wird. In der Updateverwaltung können Sie die Integrität eines Hybrid Worker-Agents überprüfen, um das zugrunde liegende Problem zu ermitteln. In diesem Artikel wird erläutert, wie Sie die Problembehandlung für Azure-Computer im Azure-Portal und für Azure-fremde Computer im [Offlineszenario](#troubleshoot-offline) ausführen.

In der folgende Liste finden Sie die drei Bereitschaftszustände, in denen sich ein Computer befinden kann:

* **Bereit**: Der Update-Agent ist bereitgestellt und wurde vor weniger als 1 Stunde zuletzt angezeigt.
* **Getrennt**: Der Update-Agent ist bereitgestellt und wurde vor mehr als 1 Stunde zuletzt angezeigt.
* **Nicht konfiguriert**: Der Update-Agent wurde nicht gefunden oder hat das Onboarding noch nicht abgeschlossen.

> [!NOTE]
> Möglicherweise gibt es eine kleine Verzögerung zwischen der Anzeige des Azure-Portals und dem aktuellen Zustand des Computers.

## <a name="start-the-troubleshooter"></a>Starten der Problembehandlung

Klicken Sie für Azure-Computer im Portal unter der Spalte **Bereitschaft des Update-Agents** auf den Link **Problembehandlung**, um die Seite **Problembehandlung von Update-Agent** zu öffnen. Bei Azure-fremden Computern gelangen Sie über den Link zu diesem Artikel. In der Offlineanleitung erfahren Sie, wie Sie Probleme mit einem Nicht-Azure-Computer behandeln.

![Seite „VM-Liste“](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> Für die Überprüfungen muss die VM ausgeführt werden. Wenn die VM nicht ausgeführt wird, wird eine Schaltfläche angezeigt, mit der Sie die **VM starten** können.

Klicken Sie auf der Seite **Problembehandlung von Update-Agent** auf **Überprüfungen ausführen**. Die Problembehandlung verwendet [Befehl ausführen](../../virtual-machines/linux/run-command.md), um ein Skript auf einem Computer auszuführen, mit dem die Abhängigkeiten überprüft werden, die der Agent aufweist. Wenn die Problembehandlung abgeschlossen ist, werden die Ergebnisse der Überprüfungen zurückgegeben.

![Seite „Problembehandlung“](../media/update-agent-issues-linux/troubleshoot-page.png)

Nach Abschluss werden die Ergebnisse im Fenster zurückgegeben. Die verschiedenen Überprüfungsabschnitte liefern Informationen zu den Aspekten, die jede Überprüfung untersucht.

![Seite mit Überprüfungen des Update-Agents](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Voraussetzungsprüfungen

### <a name="operating-system"></a>Betriebssystem

Die Betriebssystemprüfung untersucht, ob der Hybrid Runbook Worker unter einem der folgenden Betriebssysteme ausgeführt wird:

|Betriebssystem  |Notizen  |
|---------|---------|
|CentOS 6 (x86/x64) und 7 (x64)      | Für Linux-Agents muss Zugriff auf ein Updaterepository bestehen. Für klassifizierungsbasiertes Patchen muss yum Sicherheitsdaten zurückgeben, über die CentOS nicht standardmäßig verfügt.         |
|Red Hat Enterprise 6 (x86/x64) und 7 (x64)     | Für Linux-Agents muss Zugriff auf ein Updaterepository bestehen.        |
|SUSE Linux Enterprise Server 11 (x86/x64) und 12 (x64)     | Für Linux-Agents muss Zugriff auf ein Updaterepository bestehen.        |
|Ubuntu 14.04 LTS, 16.04 LTS und 18.04 LTS (x86/x64)      |Für Linux-Agents muss Zugriff auf ein Updaterepository bestehen.         |

## <a name="monitoring-agent-service-health-checks"></a>Integritätsüberprüfungen für den Monitoring Agent-Dienst

### <a name="oms-agent"></a>OMS-Agent

Durch diese Überprüfungen wird sichergestellt, dass der OMS-Agent für Linux installiert ist. Anweisungen zum Installieren finden Sie unter [Installieren des Agents für Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
).

### <a name="oms-agent-status"></a>OMS-Agent-Status

Durch diese Überprüfungen wird sichergestellt, dass der OMS-Agent für Linux ausgeführt wird. Wenn der Agent nicht aktiv ist, können Sie den folgenden Befehl ausführen, um ihn neu zu starten. Weitere Informationen zur Problembehandlung für den Agent finden Sie unter [Problembehandlung für Linux-Hybrid Runbook Worker](hybrid-runbook-worker.md#linux).

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Multi-Homing

Durch diese Überprüfung wird ermittelt, ob der Agent Meldungen an mehrere Arbeitsbereiche ausgibt. Multi-Homing wird von der Updateverwaltung nicht unterstützt.

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

Hiermit wird überprüft, ob der OMS-Agent für Linux über das Hybrid Runbook Worker-Paket verfügt. Dieses Paket ist erforderlich, damit die Updateverwaltung funktioniert.

### <a name="hybrid-runbook-worker-status"></a>Hybrid Runbook Worker-Status

Durch diese Überprüfung wird sichergestellt, dass der Hybrid Runbook Worker auf dem Computer ausgeführt wird. Die folgenden Prozesse sollten vorhanden sein, wenn der Hybrid Runbook Worker ordnungsgemäß ausgeführt wird. Weitere Informationen finden Sie unter [Problembehandlung für den Log Analytics-Agent für Linux](hybrid-runbook-worker.md#oms-agent-not-running).

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Konnektivitätsprüfungen

### <a name="general-internet-connectivity"></a>Allgemeine Internetkonnektivität

Durch diese Überprüfung wird sichergestellt, dass der Computer Zugriff auf das Internet hat.

### <a name="registration-endpoint"></a>Registrierungsendpunkt

Bei dieser Überprüfung wird untersucht, ob der Agent ordnungsgemäß mit dem Agent-Dienst kommunizieren kann.

Die Proxy- und Firewallkonfigurationen müssen die Kommunikation des Hybrid Runbook Worker-Agents mit dem Registrierungsendpunkt zulassen. Eine Liste der Adressen und zu öffnenden Ports finden Sie unter [Netzwerkplanung für Hybrid Worker](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Endpunkt für Vorgänge

Bei dieser Überprüfung wird untersucht, ob der Agent ordnungsgemäß mit dem Auftragsruntime-Datendienst kommunizieren kann.

Die Proxy- und Firewallkonfigurationen müssen die Kommunikation des Hybrid Runbook Worker-Agents mit dem Auftragsruntime-Datendienst zulassen. Eine Liste der Adressen und zu öffnenden Ports finden Sie unter [Netzwerkplanung für Hybrid Worker](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="log-analytics-endpoint-1"></a>Log Analytics-Endpunkt 1

Durch diese Überprüfung wird sichergestellt, dass der Computer Zugriff auf die Endpunkte hat, die vom Log Analytics-Agent benötigt werden.

### <a name="log-analytics-endpoint-2"></a>Log Analytics-Endpunkt 2

Durch diese Überprüfung wird sichergestellt, dass der Computer Zugriff auf die Endpunkte hat, die vom Log Analytics-Agent benötigt werden.

### <a name="log-analytics-endpoint-3"></a>Log Analytics-Endpunkt 3

Durch diese Überprüfung wird sichergestellt, dass der Computer Zugriff auf die Endpunkte hat, die vom Log Analytics-Agent benötigt werden.

## <a name="troubleshoot-offline"></a>Offlineproblembehandlung

Sie können die Problembehandlung offline auf einem Hybrid Runbook Worker ausführen, indem Sie das Skript lokal ausführen. Das Python-Skript [update_mgmt_health_check.py](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6) finden Sie im Script Center. Hier sehen Sie eine Beispielausgabe dieses Skripts:

```output
Debug: Machine Information:   Static hostname: LinuxVM2
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 00000000000000000000000000000000
           Boot ID: 00000000000000000000000000000000
    Virtualization: microsoft
  Operating System: Ubuntu 16.04.5 LTS
            Kernel: Linux 4.15.0-1025-azure
      Architecture: x86-64


Passed: Operating system version is supported

Passed: Microsoft Monitoring agent is installed

Debug: omsadmin.conf file contents:
        WORKSPACE_ID=00000000-0000-0000-0000-000000000000
        AGENT_GUID=00000000-0000-0000-0000-000000000000
        LOG_FACILITY=local0
        CERTIFICATE_UPDATE_ENDPOINT=https://00000000-0000-0000-0000-000000000000.oms.opinsights.azure.com/ConfigurationService.Svc/RenewCertificate
        URL_TLD=opinsights.azure.com
        DSC_ENDPOINT=https://scus-agentservice-prod-1.azure-automation.net/Accou            nts/00000000-0000-0000-0000-000000000000/Nodes\(AgentId='00000000-0000-0000-0000-000000000000'\)
        OMS_ENDPOINT=https://00000000-0000-0000-0000-000000000000.ods.opinsights            .azure.com/OperationalData.svc/PostJsonDataItems
        AZURE_RESOURCE_ID=/subscriptions/00000000-0000-0000-0000-000000000000/re            sourcegroups/myresourcegroup/providers/microsoft.compute/virtualmachines/linuxvm            2
        OMSCLOUD_ID=0000-0000-0000-0000-0000-0000-00
        UUID=00000000-0000-0000-0000-000000000000


Passed: Microsoft Monitoring agent is running

Passed: Machine registered with log analytics workspace:['00000000-0000-0000-0000-000000000000']

Passed: Hybrid worker package is present

Passed: Hybrid worker is running

Passed: Machine is connected to internet

Passed: TCP test for {scus-agentservice-prod-1.azure-automation.net} (port 443)             succeeded

Passed: TCP test for {eus2-jobruntimedata-prod-su1.azure-automation.net} (port 4            43) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.ods.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.oms.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {ods.systemcenteradvisor.com} (port 443) succeeded

```

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Behandlung weiterer Probleme mit Ihren Hybrid Runbook Workern finden Sie unter [Problembehandlung für Hybrid Runbook Worker](hybrid-runbook-worker.md).


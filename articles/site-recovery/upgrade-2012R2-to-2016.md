---
title: Aktualisieren von Windows Server 2012 R2-Hosts und SCVMM-Instanzen, die mit Azure Site Recovery konfiguriert sind, auf Windows Server 2016
description: Hier erfahren Sie, wie Sie die Notfallwiederherstellung in Azure für virtuelle Azure Stack-Computer mit dem Azure Site Recovery-Dienst einrichten.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/03/2018
ms.author: rajanaki
ms.openlocfilehash: b67290f72f762331a6d699fb79aef0c0d7f9fb65
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61275522"
---
# <a name="upgrade-windows-server-2012-r2-hosts-scvmm-2012-r2-configured-with-azure-site-recovery-to-windows-server-2016--scvmm-2016"></a>Aktualisieren von Windows Server 2012 R2-Hosts und SCVMM 2012 R2-Instanzen, die mit Azure Site Recovery konfiguriert sind, auf Windows Server 2016 und SCVMM 2016

In diesem Artikel erfahren Sie, wie Sie Windows Server 2012 R2-Hosts und Instanzen von System Center Virtual Machine Manager 2012 R2 (SCVMM), die mit Azure Site Recovery konfiguriert sind, auf Windows Server 2016 und SCVMM 2016 aktualisieren.

Site Recovery unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung (Business Continuity and Disaster Recovery, BCDR). Der Dienst stellt sicher, dass Ihre VM-Workloads bei erwarteten und unerwarteten Ausfällen weiterhin verfügbar sind.

> [!IMPORTANT]
> Wenn Sie Windows Server 2012 R2-Hosts aktualisieren, die bereits für die Replikation mit Azure Site Recovery konfiguriert sind, müssen Sie die in diesem Dokument beschriebenen Schritte befolgen. Andere Upgradepfade können zu nicht unterstützten Zuständen und einer Unterbrechung der Replikation oder Durchführbarkeit eines Failovers führen.


In diesem Artikel erfahren Sie, wie Sie die folgenden Konfigurationen in Ihrer Umgebung aktualisieren:

> [!div class="checklist"]
> * **Windows Server 2012 R2-Hosts, die nicht von SCVMM verwaltet werden** 
> * **Windows Server 2012 R2-Hosts, die von einem eigenständigen SCVMM 2012 R2-Server verwaltet werden** 
> * **Windows Server 2012 R2-Hosts, die von einem hoch verfügbaren SCVMM 2012 R2-Server verwaltet werden**


## <a name="prerequisites--factors-to-consider"></a>Voraussetzungen und zu berücksichtigende Faktoren

Beachten Sie Folgendes, bevor Sie das Upgrade durchführen:

- Wenn Sie Windows Server 2012 R2-Hosts haben, die nicht von SCVMM verwaltet werden, und es sich dabei um ein eigenständiges Umgebungssetup handelt, wird die Replikation unterbrochen, sobald Sie versuchen, das Upgrade durchzuführen.
- Wenn Sie bei der Installation von SCVMM 2012 R2 zuerst *Do not store my Keys in Active Directory under Distributed Key Management* (Meine Schlüssel nicht in Active Directory unter „Verwaltung verteilter Schlüssel“ speichern) ausgewählt haben, werden die Upgrades nicht erfolgreich abgeschlossen.

- Wenn Sie System Center 2012 R2 Virtual Machine Manager (VMM) verwenden: 

    - Überprüfen Sie die Datenbankinformationen in VMM: **VMM-Konsole** -> **Einstellungen** -> **Allgemein** -> **Datenbankverbindung**
    - Überprüfen Sie die Dienstkonten, die für den System Center Virtual Machine Manager-Agent-Dienst verwendet werden.
    - Stellen Sie sicher, dass Sie über eine Sicherung der VMM-Datenbank verfügen.
    - Notieren Sie den Datenbanknamen der betroffenen SCVMM-Server. Navigieren Sie dazu zu **VMM-Konsole** -> **Einstellungen** -> **Allgemein** -> **Datenbankverbindung**.
    - Notieren Sie die VMM-ID des primären 2012 R2-VMM-Servers und des 2012 R2-VMM-Wiederherstellungsservers. Die VMM-ID finden Sie in der Registrierung unter „HKLM:\SOFTWARE\Microsoft\Microsoft System Center Virtual Machine Manager Server\Setup“.
    - Stellen Sie sicher, dass die neuen SCVMM-Instanzen, die Sie dem Cluster hinzufügen, die gleichen Namen wie zuvor aufweisen. 

- Wenn Sie zwischen zwei Standorten replizieren, die auf beiden Seiten von SCVMM verwaltet werden, müssen Sie zuerst ein Upgrade des Wiederherstellungsstandorts und danach ein Upgrade des primären Standort durchführen.
  > [!WARNING]
  > Wählen Sie während des Upgrades von SCVMM 2012 R2 unter „Verwaltung verteilter Schlüssel“ die Option **Store encryption keys in Active Directory** (Verschlüsselungsschlüssel in Active Directory speichern) aus. Gehen Sie bei der Auswahl der Einstellungen für das Dienstkonto und die Verwaltung verteilter Schlüssel sorgfältig vor. Abhängig von Ihrer Auswahl sind verschlüsselte Daten (z. B. Kennwörter in Vorlagen) nach dem Upgrade möglicherweise nicht verfügbar, wodurch die Replikation mit Azure Site Recovery beeinträchtigt werden kann.

> [!IMPORTANT]
> Weitere Informationen finden Sie in der ausführlichen SCVMM-Dokumentation zu den [Voraussetzungen](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#requirements-and-limitations).

## <a name="windows-server-2012-r2-hosts-which-arent-managed-by-scvmm"></a>Windows Server 2012 R2-Hosts, die nicht von SCVMM verwaltet werden 
Die folgenden Schritte gelten für die Benutzerkonfiguration von [Hyper-V-Hosts zu Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-architecture), deren Durchführung in diesem [Tutorial](https://docs.microsoft.com/azure/site-recovery/hyper-v-prepare-on-premises-tutorial) beschrieben wird.

> [!WARNING]
> Wie in den Voraussetzungen erwähnt, gelten diese Schritte nur für ein Szenario mit einer Clusterumgebung und nicht für eine eigenständige Hyper-V-Hostkonfiguration.

1. Folgen Sie den Schritten zum Durchführen des [parallelen Clusterupgrades](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process), um den parallelen Clusterupgradeprozess auszuführen.
2. Entfernen Sie bei jedem neuen Windows Server 2016-Host, der dem Cluster hinzugefügt wird, den Verweis eines Windows Server 2012 R2-Hosts aus Azure Site Recovery, indem Sie die [hier] beschriebenen Schritte ausführen. Dabei muss es sich um den Host handeln, den Sie entladen und aus dem Cluster entfernen möchten.
3. Sobald der Befehl *Update-VMVersion* für alle VMs ausgeführt wurde, sind die Upgrades abgeschlossen. 
4. Folgen Sie den [hier](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-the-source-environment) beschriebenen Schritten, um den neuen Windows Server 2016-Host in Azure Site Recovery zu registrieren. Beachten Sie, dass die Hyper-V-Site bereits aktiv ist und Sie nur den neuen Host im Cluster registrieren müssen. 
5.  Wechseln Sie zum Azure-Portal, und überprüfen Sie den replizierten Integritätsstatus in Recovery Services.

## <a name="upgrade-windows-server-2012-r2-hosts-managed-by-stand-alone-scvmm-2012-r2-server"></a>Aktualisieren von Windows Server 2012 R2-Hosts, die von einem eigenständigen SCVMM 2012 R2-Server verwaltet werden
Bevor Sie Ihre Windows Server 2012 R2-Hosts aktualisieren, müssen Sie SCVMM 2012 R2 auf SCVMM 2016 aktualisieren. Führen Sie die folgenden Schritte aus:

**Aktualisieren eines eigenständigen SCVMM 2012 R2-Servers auf SCVMM 2016**

1.  Deinstallieren Sie den ASR-Anbieter, indem Sie zu „Systemsteuerung“ -> „Programme“ -> „Programme und Features“ -> „Microsoft Azure Site Recovery“ navigieren und auf „Deinstallieren“ klicken.
2. [Behalten Sie die SCVMM-Datenbank bei, und aktualisieren Sie das Betriebssystem.](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#back-up-and-upgrade-the-operating-system)
3. Klicken Sie unter **Software** auf **VMM** > **Deinstallieren**. b. Klicken Sie auf **Features entfernen**, und wählen Sie dann die Einträge für **VMM-Verwaltungsserver und VMM-Konsole** aus. c. Wählen Sie unter **Datenbankoptionen** die Option **Datenbank beibehalten** aus. d. Überprüfen Sie die Zusammenfassung, und klicken Sie auf **Deinstallieren**.

4. [Installieren Sie VMM 2016.](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#install-vmm-2016)
5. Starten Sie SCVMM, und überprüfen Sie den Status jedes Hosts auf der Registerkarte **Fabrics**. Klicken Sie zum Abrufen des aktuellen Status auf **Aktualisieren**. Der angezeigte Status sollte „Eingreifen erforderlich“ lauten. 
17. Installieren Sie den aktuellen [Microsoft Azure Site Recovery-Anbieter](https://aka.ms/downloaddra) auf dem SCVMM-Server.
16. Installieren Sie den aktuellen [Microsoft Azure Recovery Service-Agent (MARS)](https://aka.ms/latestmarsagent) auf jedem Host im Cluster. Klicken Sie auf „Aktualisieren“, um sicherzustellen, dass SCVMM die Hosts erfolgreich abfragen kann.

**Aktualisieren der Windows Server 2012 R2-Hosts auf Windows Server 2016**

1. Folgen Sie den [hier](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) beschriebenen Schritten, um den parallelen Clusterupgradeprozess auszuführen. 
2. Nachdem Sie den neuen Host dem Cluster hinzugefügt haben, aktualisieren Sie ihn in der SCVMM-Konsole, um den VMM-Agent auf diesem aktualisierten Host zu installieren.
3. Führen Sie *Update-VMVersion* aus, um die VM-Versionen der VMs zu aktualisieren. 
4.  Wechseln Sie zum Azure-Portal, und überprüfen Sie den replizierten Integritätsstatus der VMs im Recovery Services-Tresor. 

## <a name="upgrade-windows-server-2012-r2-hosts-are-managed-by-highly-available-scvmm-2012-r2-server"></a>Aktualisieren von Windows Server 2012 R2-Hosts, die von einem hoch verfügbaren SCVMM 2012 R2-Server verwaltet werden
Bevor Sie Ihre Windows Server 2012 R2-Hosts aktualisieren, müssen Sie SCVMM 2012 R2 auf SCVMM 2016 aktualisieren. Die folgenden Upgrademodi werden beim Aktualisieren von SCVMM 2012 R2-Servern, die mit Azure Site Recovery konfiguriert sind, unterstützt: gemischter Modus ohne zusätzliche VMM-Server und gemischter Modus mit zusätzlichen VMM-Servern.

**Aktualisieren von SCVMM 2012 R2 auf SCVMM 2016**

1.  Deinstallieren Sie den ASR-Anbieter, indem Sie zu „Systemsteuerung“ -> „Programme“ -> „Programme und Features“ -> „Microsoft Azure Site Recovery“ navigieren und auf „Deinstallieren“ klicken.
2. Führen Sie die [hier](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#upgrade-a-standalone-vmm-server) beschriebenen Schritte für den Upgrademodus aus, den Sie verwenden möchten.
3. Starten Sie die SCVMM-Konsole, und überprüfen Sie den Status jedes Hosts auf der Registerkarte **Fabrics**. Klicken Sie zum Abrufen des aktuellen Status auf **Aktualisieren**. Der angezeigte Status sollte „Eingreifen erforderlich“ lauten.
4. Installieren Sie den aktuellen [Microsoft Azure Site Recovery-Anbieter](https://aka.ms/downloaddra) auf dem SCVMM-Server.
5. Installieren Sie den aktuellen [Microsoft Azure Recovery Service-Agent (MARS)](https://aka.ms/latestmarsagent) auf jedem Host im Cluster. Klicken Sie auf „Aktualisieren“, um sicherzustellen, dass SCVMM die Hosts erfolgreich abfragen kann.


**Aktualisieren der Windows Server 2012 R2-Hosts auf Windows Server 2016**

1. Folgen Sie den [hier](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) beschriebenen Schritten, um den parallelen Clusterupgradeprozess auszuführen.
2. Nachdem Sie den neuen Host dem Cluster hinzugefügt haben, aktualisieren Sie ihn in der SCVMM-Konsole, um den VMM-Agent auf diesem aktualisierten Host zu installieren.
3. Führen Sie *Update-VMVersion* aus, um die VM-Versionen der VMs zu aktualisieren. 
4.  Wechseln Sie zum Azure-Portal, und überprüfen Sie den replizierten Integritätsstatus der VMs im Recovery Services-Tresor. 

## <a name="next-steps"></a>Nächste Schritte
Nachdem das Upgrade der Hosts abgeschlossen ist, können Sie ein [Testfailover](tutorial-dr-drill-azure.md) ausführen, um die Integrität Ihres Replikations- und Notfallwiederherstellungsstatus zu testen.


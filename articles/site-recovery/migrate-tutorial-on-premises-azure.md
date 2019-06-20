---
title: Migrieren von lokalen Computern mit Azure Site Recovery zu Azure | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie Sie lokale Computer mithilfe von Azure Site Recovery zu Azure migrieren.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: df4f89bd1b2e3c0423f5d758cfa637e4da9e04d0
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66396541"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrieren von lokalen Computern zu Azure


In diesem Artikel wird erläutert, wie Sie lokale Computer mithilfe von [Azure Site Recovery](site-recovery-overview.md) zu Azure migrieren. Site Recovery wird in der Regel für die Verwaltung und Orchestrierung der Notfallwiederherstellung von lokalen Computern sowie virtuellen Azure-Computern (VMs) verwendet. Der Dienst kann jedoch auch für die Migration verwendet werden. Bei der Migration werden mit einer Ausnahme die gleichen Schritte wie bei der Notfallwiederherstellung ausgeführt. Bei einer Migration ist das Failover der Computer vom lokalen Standort der letzte Schritt. Im Gegensatz zur Notfallwiederherstellung ist in einem Migrationsszenario kein Failback auf den lokalen Standort möglich.


In diesem Tutorial wird veranschaulicht, wie lokale VMs und physische Server in Azure migriert werden. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Einrichten der Quell- und Zielumgebung für die Migration
> * Einrichten einer Replikationsrichtlinie
> * Aktivieren der Replikation
> * Führen Sie eine Testmigration aus, um sicherzustellen, dass alles wie erwartet funktioniert.
> * Ausführen eines einmaligen Failovers in Azure


> [!TIP]
> Der Azure Migrate-Dienst bietet nun eine Vorschauversion einer neuen Umgebung ohne Agent für die Migration virtueller VMware-Computer zu Azure. [Mehr erfahren](https://aka.ms/migrateVMs-signup)


## <a name="before-you-start"></a>Vorbereitung

Beachten Sie, das von paravirtualisierten Treibern exportierte Geräte nicht unterstützt werden.


## <a name="prepare-azure-and-on-premises"></a>Vorbereiten von Azure und der lokalen Ressourcen

1. Bereiten Sie Azure wie in [diesem Artikel](tutorial-prepare-azure.md) beschrieben vor. In diesem Artikel sind zwar die Vorbereitungsschritte für die Notfallwiederherstellung beschrieben, diese gelten jedoch auch für die Migration.
2. Bereiten Sie lokale [VMware](vmware-azure-tutorial-prepare-on-premises.md)- oder [Hyper-V](hyper-v-prepare-on-premises-tutorial.md)-Server vor. Bei der Migration von physischen Computern müssen Sie keine Vorbereitungen treffen. Überprüfen Sie lediglich die [Supportmatrix](vmware-physical-azure-support-matrix.md).


## <a name="select-a-replication-goal"></a>Auswählen eines Replikationsziels

Wählen Sie aus, was Sie replizieren möchten und wohin die Daten repliziert werden sollen.
1. Klicken Sie auf **Recovery Services-Tresore** > „Tresor“.
2. Klicken Sie im Ressourcenmenü auf **Site Recovery** > **Infrastruktur vorbereiten** > **Schutzziel**.
3. Wählen Sie in **Schutzziel** aus, was Sie migrieren möchten.
    - **VMware**: Wählen Sie **To Azure** (Zu Azure) > **Ja, mit VMware vSphere-Hypervisor**.
    - **Physischer Computer:** Wählen Sie **To Azure** (Zu Azure) > **Nicht virtualisiert/Andere** aus.
    - **Hyper-V:** Wählen Sie **To Azure** (Zu Azure) > **Ja, mit Hyper-V** aus. Wenn Hyper-V-VMs von VMM verwaltet werden, wählen Sie **Ja** aus.


## <a name="set-up-the-source-environment"></a>Einrichten der Quellumgebung

**Szenario** | **Details**
--- | --- 
VMware | Einrichten der [Quellumgebung](vmware-azure-set-up-source.md) und des [Konfigurationsservers](vmware-azure-deploy-configuration-server.md)
Physischer Computer | [Einrichten](physical-azure-set-up-source.md) der Quellumgebung und des Konfigurationsservers
Hyper-V | Einrichten der [Quellumgebung](hyper-v-azure-tutorial.md#set-up-the-source-environment)<br/><br/> Einrichten der [Quellumgebung](hyper-v-vmm-azure-tutorial.md#set-up-the-source-environment) für Hyper-V mit System Center VMM-Bereitstellung

## <a name="set-up-the-target-environment"></a>Einrichten der Zielumgebung

Wählen Sie Zielressourcen aus, und überprüfen Sie sie.

1. Klicken Sie auf **Infrastruktur vorbereiten** > **Ziel**, und wählen Sie das gewünschte Azure-Abonnement aus.
2. Geben Sie das Ressourcen-Manager-Bereitstellungsmodell an.
3. Site Recovery überprüft die Azure-Ressourcen.
    - Bei der Migration von virtuellen VMware-Computern oder physischen Servern überprüft Site Recovery, ob ein Azure-Netzwerk vorhanden ist, in dem die virtuellen Azure-Computer nach einem Failover erstellt werden.
    - Bei der Migration von virtuellen Hyper-V-Computern überprüft Site Recovery, ob Sie über ein kompatibles Azure-Speicherkonto und -Netzwerk verfügen.
4. Richten Sie für die Migration von virtuellen Hyper-V-Computern, die von System Center VMM verwaltet werden, die [Netzwerkzuordnung](hyper-v-vmm-azure-tutorial.md#configure-network-mapping) ein.

## <a name="set-up-a-replication-policy"></a>Einrichten einer Replikationsrichtlinie

**Szenario** | **Details**
--- | --- 
VMware | Einrichten einer [Replikationsrichtlinie](vmware-azure-set-up-replication.md) für virtuelle VMware-Computer
Physischer Computer | Einrichten einer [Replikationsrichtlinie](physical-azure-disaster-recovery.md#create-a-replication-policy) für physische Computer
Hyper-V | Einrichten einer [Replikationsrichtlinie](hyper-v-azure-tutorial.md#set-up-a-replication-policy)<br/><br/> Einrichten einer [Replikationsrichtlinie](hyper-v-vmm-azure-tutorial.md#set-up-a-replication-policy) für Hyper-V mit System Center VMM-Bereitstellung

## <a name="enable-replication"></a>Aktivieren der Replikation

**Szenario** | **Details**
--- | --- 
VMware | [Aktivieren Sie die Replikation](vmware-azure-enable-replication.md) für VMware-VMs.
Physischer Computer | [Aktivieren der Replikation](physical-azure-disaster-recovery.md#enable-replication) für physische Computer
Hyper-V | [Replikation aktivieren](hyper-v-azure-tutorial.md#enable-replication)<br/><br/> [Aktivieren der Replikation](hyper-v-vmm-azure-tutorial.md#enable-replication) für Hyper-V mit System Center VMM-Bereitstellung


## <a name="run-a-test-migration"></a>Ausführen einer Testmigration

Führen Sie ein [Testfailover](tutorial-dr-drill-azure.md) in Azure aus, um sicherzustellen, dass alles wie erwartet funktioniert.


## <a name="migrate-to-azure"></a>Migrieren zu Azure

Führen Sie ein Failover für die zu migrierenden Computer aus.

1. Klicken Sie in **Einstellungen** > **Replizierte Elemente** auf den Computer > **Failover**.
2. Wählen Sie in **Failover** einen **Wiederherstellungspunkt** für das Failover aus. Wählen Sie den letzten Wiederherstellungspunkt aus.
3. Die Einstellung für den Verschlüsselungsschlüssel ist für dieses Szenario nicht relevant.
4. Wählen Sie **Computer vor Beginn des Failovers herunterfahren** aus. Site Recovery versucht, virtuelle Computer herunterzufahren, bevor das Failover ausgelöst wird. Das Failover wird auch dann fortgesetzt, wenn das Herunterfahren nicht erfolgreich ist. Der Fortschritt des Failovers wird auf der Seite **Aufträge** angezeigt.
5. Überprüfen Sie, ob die Azure-VM wie erwartet in Azure angezeigt wird.
6. Klicken Sie in **Replizierte Elemente** mit der rechten Maustaste auf die VM > **Migration abschließen**. Die folgenden Schritte werden ausgeführt:

   - Der Migrationsvorgang wird abgeschlossen, die Replikation für den lokalen virtuellen Computer wird beendet, und die Site Recovery-Abrechnung für den virtuellen Computer wird eingestellt.
   - In diesem Schritt werden die Replikationsdaten entfernt. Die migrierten virtuellen Computer werden nicht gelöscht.

     ![Abschließen der Migration](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Brechen Sie ein aktuell ausgeführtes Failover nicht ab**: Die VM-Replikation wird beendet, bevor das Failover gestartet wird. Wenn Sie ein Failover in Bearbeitung abbrechen, wird das Failover beendet, die Replikation der VM wird jedoch nicht erneut durchgeführt.

In einigen Szenarien erfordert ein Failover zusätzliche Verarbeitungsschritte, die etwa 8 bis 10 Minuten dauern können. Bei physischen Servern, VMware-Linux-Computern, VMware-VMs ohne aktivierten DHCP-Dienst und VMware-VMs ohne die folgenden Starttreiber kann das Testfailover länger dauern: storvsc, vmbus, storflt, intelide, atapi.

## <a name="after-migration"></a>Nach der Migration

Nach der Migration von Computern zu Azure sind eine Reihe von Schritten durchzuführen.

Einige Schritte können im Rahmen des Migrationsvorgangs mithilfe der integrierten Automatisierungsskriptfunktion in [Wiederherstellungsplänen](site-recovery-runbook-automation.md) automatisiert werden.   


### <a name="post-migration-steps-in-azure"></a>Schritte nach der Migration in Azure

- Führen Sie App-Anpassungen nach der Migration durch, z.B. die Aktualisierung von Datenbankverbindungszeichenfolgen und Webserverkonfigurationen. 
- Führen Sie endgültige Anwendungs- und Migrationsakzeptanztests für die migrierte Anwendung durch, die nun in Azure ausgeführt wird.
- Der [Azure-VM-Agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) verwaltet VM-Interaktionen mit dem Azure Fabric Controller. Dieser ist für einige Azure-Dienste wie Azure Backup, Site Recovery und das Azure Security Center erforderlich.
    - Wenn Sie VMware-Computer und physische Server migrieren, installiert das Mobility Service-Installationsprogramm verfügbare Azure-VM-Agents auf Windows-Computern. Auf Linux-VMs empfehlen wir, den Agent nach einem Failover zu installieren.
    - Wenn Sie Azure-VMs in eine sekundäre Region migrieren, muss der Azure-VM-Agent vor der Migration auf der VM bereitgestellt werden.
    - Wenn Sie Hyper-V-VMs zu Azure migrieren, installieren Sie nach der Migration den Azure-VM-Agent auf der Azure-VM.
- Entfernen Sie manuell alle Site Recovery-Anbieter/-Agents von der VM. Wenn Sie virtuelle VMware-Computer oder physische Server migrieren, deinstallieren Sie den Mobilitätsdienst auf dem virtuellen Computer.
- Beachten Sie zur Steigerung der Resilienz Folgendes:
    - Schützen Sie Daten, indem Sie Azure-VMs mit dem Azure Backup-Dienst sichern. [Weitere Informationen]( https://docs.microsoft.com/azure/backup/quick-backup-vm-portal)
    - Sorgen Sie für die kontinuierliche Ausführung und Verfügbarkeit von Workloads, indem Sie Azure-VMs mithilfe von Site Recovery in eine sekundäre Region replizieren. [Weitere Informationen](azure-to-azure-quickstart.md)
- Beachten Sie zur Steigerung der Sicherheit Folgendes:
    - Sperren und beschränken Sie den Zugriff von eingehendem Datenverkehr mit der [Just-in-Time-Verwaltung]( https://docs.microsoft.com/azure/security-center/security-center-just-in-time) im Azure Security Center.
    - Beschränken Sie den Netzwerkdatenverkehr mithilfe von [Netzwerksicherheitsgruppen](https://docs.microsoft.com/azure/virtual-network/security-overview) auf Verwaltungsendpunkte.
    - Stellen Sie [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) bereit, um Datenträger und Daten vor Diebstahl und unbefugtem Zugriff zu schützen.
    - Erfahren Sie mehr über das [Sichern von IaaS-Ressourcen]( https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/ ), und besuchen Sie die Website [Azure Security Center](https://azure.microsoft.com/services/security-center/ ).
- Beachten Sie zur Überwachung und Verwaltung Folgendes:
    - Ziehen Sie die Bereitstellung von [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) in Erwägung, um den Ressourceneinsatz und die Ausgaben zu überwachen.

### <a name="post-migration-steps-on-premises"></a>Schritte nach der Migration in der lokalen Umgebung

- Migrieren Sie den App-Datenverkehr zur App, die auf der migrierten Azure-VM-Instanz ausgeführt wird.
- Entfernen Sie die lokalen VMs aus Ihrem lokalen VM-Bestand.
- Entfernen Sie die lokalen VMs aus lokalen Sicherungen.
- Aktualisieren Sie die interne Dokumentation zum Anzeigen des neuen Speicherorts und der IP-Adresse der Azure-VMs.




## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie lokale VMs in Azure-VMs migriert. Now

> [!div class="nextstepaction"]
> [Einrichten der Notfallwiederherstellung](azure-to-azure-replicate-after-migration.md) in einer sekundären Azure-Region für die virtuellen Azure-Computer

  

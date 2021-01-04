---
title: Migrieren von VMware-VMs ohne Agent per Azure Migrate-Servermigration
description: Hier wird beschrieben, wie Sie eine Migration virtueller VMware-Computer ohne Agent mit Azure Migrate ausführen.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: mvc
ms.openlocfilehash: 88b3879a648117329f2485012542b48d6a7a7755
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96752684"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>Migrieren von VMware-VMs zu Azure (ohne Agent)

In diesem Artikel wird gezeigt, wie Sie lokale VMware-VMs mithilfe des Tools [Azure Migrate-Servermigration](migrate-services-overview.md#azure-migrate-server-migration-tool) ohne Agent zu Azure migrieren. Sie können VMware-VMs auch mithilfe der agentbasierten Migration migrieren. [Vergleichen Sie](server-migrate-overview.md#compare-migration-methods) die Methoden.

Dieses Tutorial ist das dritte in einer Reihe zur Bewertung und Migration von VMware-VMs zu Azure. 

> [!NOTE]
> In den Tutorials wird der einfachste Bereitstellungspfad für ein Szenario erläutert, damit Sie schnell einen Proof of Concept einrichten können. Die Tutorials verwenden nach Möglichkeit Standardoptionen und zeigen nicht alle möglichen Einstellungen und Pfade. 


In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Hinzufügen des Tools „Azure Migrate-Servermigration“
> * Ermitteln der zu migrierenden VMs
> * Beginnen mit der Replikation von VMs
> * Führen Sie eine Testmigration aus, um sicherzustellen, dass alles wie erwartet funktioniert.
> * Durchführen einer vollständigen VM-Migration

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, sollten folgende Voraussetzungen erfüllt sein:

1. [Arbeiten Sie das erste Tutorial durch](./tutorial-discover-vmware.md), um Azure und VMware für die Migration vorzubereiten.
2. Wir empfehlen, dass Sie das zweite Tutorial zum [Bewerten von VMware-VMS](./tutorial-assess-vmware-azure-vm.md) durcharbeiten, bevor Sie sie zu Azure migrieren. Dies ist aber nicht unbedingt erforderlich. 
3. Rufen Sie das bereits erstellte Projekt auf, oder [erstellen Sie ein neues Projekt](./create-manage-projects.md).
4. Überprüfen Sie die Berechtigungen für Ihr Azure-Konto: Ihr Azure-Konto benötigt Berechtigungen zum Erstellen eines virtuellen Computers sowie zum Schreiben auf einen verwalteten Azure-Datenträger.

## <a name="set-up-the-azure-migrate-appliance"></a>Einrichten der Azure Migrate-Appliance

Die Azure Migrate-Servermigration führt eine einfache VMware VM-Appliance aus, die für die Ermittlung, Bewertung und Migration ohne Agent von VMware-VMs verwendet wird. Wenn Sie dem [Bewertungs-Tutorial](./tutorial-assess-vmware-azure-vm.md) folgen, haben Sie die Appliance bereits eingerichtet. Falls dies noch nicht geschehen ist, richten Sie die Appliance jetzt mit einer der folgenden Methoden ein:

- **OVA-Vorlage**: [Einrichten](how-to-set-up-appliance-vmware.md) auf einer VMware-VM mit einer heruntergeladenen OVA-Vorlage.
- **Script:** [Einrichten](deploy-appliance-script.md) auf einer VMware-VM oder einem physischen Computer mithilfe eines PowerShell-Installationsskripts. Diese Methode sollte verwendet werden, wenn Sie eine VM nicht mithilfe einer OVA-Vorlage einrichten können oder wenn Sie in Azure Government arbeiten.

Überprüfen Sie nach der Erstellung der Appliance, ob diese eine Verbindung mit der Azure Migrate-Serverbewertung herstellen kann. Führen Sie dann die erstmalige Konfiguration durch, und registrieren Sie sie für das Azure Migrate-Projekt.

## <a name="replicate-vms"></a>Replizieren von VMs

Nachdem Sie die Appliance eingerichtet und die Ermittlung abgeschlossen haben, können Sie mit der Replikation von VMware-VMs in Azure beginnen. 

- Sie können bis zu 300 Replizierungen gleichzeitig ausführen.
- Sie können im Portal bis zu 10 VMs gleichzeitig für die Migration auswählen. Wenn Sie weitere Computer migrieren möchten, fügen Sie sie in Batches von 10 zu Gruppen hinzu.

Aktivieren Sie die Replikation:

1. Klicken Sie im Azure Migrate-Projekt unter **Server** > **Azure Migrate: Servermigration** auf **Replizieren**.

    ![Replizieren von VMs](./media/tutorial-migrate-vmware/select-replicate.png)

2. Wählen Sie unter **Replizieren** > **Quelleinstellungen** > **Sind Ihre Computer virtualisiert?** die Option **Ja, mit VMware vSphere-Hypervisor** aus.
3. Wählen Sie unter **Lokale Appliance** den Namen der Azure Migrate-Appliance aus, die Sie eingerichtet haben, und dann **OK**. 

    ![Quelleinstellungen](./media/tutorial-migrate-vmware/source-settings.png)

4. Wählen Sie unter **Virtuelle Computer** die Computer aus, die Sie replizieren möchten. Zum Anwenden der VM-Größe und des Datenträgertyps aus einer Bewertung (wenn Sie einen ausgeführt haben) wählen Sie in **Migrationseinstellungen aus einer Azure Migrate-Bewertung importieren?** die Antwort **Ja** und dann die VM-Gruppe sowie den Bewertungsnamen aus. Wenn Sie keine Bewertungseinstellungen verwenden, wählen Sie **Nein** aus.
   
    ![Auswählen der Bewertung](./media/tutorial-migrate-vmware/select-assessment.png)

5. Wählen Sie in **Virtuelle Computer** die VMs aus, die Sie migrieren möchten. Klicken Sie anschließend auf **Next: Zieleinstellungen**.

    ![Auswählen von VMs](./media/tutorial-migrate-vmware/select-vms.png)

6. Wählen Sie in **Zieleinstellungen** das Abonnement und die Zielregion aus. Geben Sie die Ressourcengruppe an, in der sich die Azure-VMS nach der Migration befinden werden.
7. Wählen Sie unter **Virtuelles Netzwerk** das Azure VNet/-Subnetz aus, in das die Azure-VMs nach der Migration eingebunden werden.
8. Wählen Sie unter **Verfügbarkeitsoptionen** Folgendes aus:
    -  Verfügbarkeitszone, um den migrierten Computer einer bestimmten Verfügbarkeitszone in der Region anzuheften. Verteilen Sie mit dieser Option Server, die eine Anwendungsebene mit mehreren Knoten bilden, über Verfügbarkeitszonen. Wenn Sie diese Option auswählen, müssen Sie die Verfügbarkeitszone angeben, die für jeden auf der Registerkarte „Compute“ ausgewählten Computer verwendet werden soll. Diese Option ist nur verfügbar, wenn die für die Migration ausgewählte Zielregion Verfügbarkeitszonen unterstützt.
    -  Verfügbarkeitsgruppe, um den migrierten Computer in einer Verfügbarkeitsgruppe zu platzieren. Um diese Option verwenden zu können, muss die ausgewählte Zielressourcengruppe über mindestens eine Verfügbarkeitsgruppe verfügen.
    - Die Infrastrukturredundanzoption ist nicht erforderlich, wenn Sie keine dieser Verfügbarkeitskonfigurationen für die migrierten Computer benötigen.

9. Wählen Sie unter **Azure-Hybridvorteil**

    - die Option **Nein** aus, falls Sie den Azure-Hybridvorteil nicht anwenden möchten. Klicken Sie dann auf **Weiter**.
    - Wählen Sie **Ja** aus, wenn Sie über Windows Server-Computer verfügen, die durch aktive Software Assurance- oder Windows Server-Abonnements abgedeckt sind, und den Vorteil auf die zu migrierenden Computer anwenden möchten. Klicken Sie dann auf **Weiter**.

    ![Zieleinstellungen](./media/tutorial-migrate-vmware/target-settings.png)

10. Überprüfen Sie in **Compute** Name, Größe, Betriebssystem-Datenträger und Verfügbarkeitskonfiguration der VM (falls im vorherigen Schritt ausgewählt). Die VMs müssen die [Azure-Anforderungen](migrate-support-matrix-vmware-migration.md#azure-vm-requirements) erfüllen.

    - **VM-Größe**: Bei Verwendung von Bewertungsempfehlungen zeigt die Dropdownliste für die VM-Größe die empfohlene Größe. Andernfalls wählt Azure Migrate eine Größe basierend auf der höchsten Übereinstimmung im Azure-Abonnement aus. Alternativ können Sie unter **Azure-VM-Größe** manuell eine Größe auswählen. 
    - **Betriebssystemdatenträger**: Geben Sie den Betriebssystemdatenträger (Startdatenträger) für die VM an. Der Betriebssystemdatenträger enthält den Bootloader und das Installationsprogramm des Betriebssystems. 
    - **Verfügbarkeitszone**: Geben Sie die zu verwendende Verfügbarkeitszone an.
    - **Verfügbarkeitsgruppe**: Geben Sie die zu verwendende Verfügbarkeitsgruppe an.

> [!NOTE]
>Wenn Sie eine andere Verfügbarkeitsoption für eine Gruppe von virtuellen Computern auswählen möchten, fahren Sie mit Schritt 1 fort, und wiederholen Sie die Schritte, indem Sie nach dem Starten der Replikation für eine Gruppe von virtuellen Computern unterschiedliche Verfügbarkeitsoptionen auswählen.


 ![VM-Computeeinstellungen](./media/tutorial-migrate-vmware/compute-settings.png)

11. Geben Sie unter **Datenträger** an, ob die VM-Datenträger in Azure repliziert werden sollen, und wählen Sie in Azure den Datenträgertyp aus (SSD Standard/HDD Standard oder Managed Disks Premium). Klicken Sie dann auf **Weiter**.
   
    ![Screenshot: Registerkarte „Datenträger“ des Dialogfelds „Replizieren“](./media/tutorial-migrate-vmware/disks.png)

12. Überprüfen Sie unter **Replikation prüfen und starten** die Einstellungen, und klicken Sie auf **Replizieren**, um die erste Replikation für die Server zu starten.

> [!NOTE]
> Sie können die Replikationseinstellungen vor Beginn der Replikation jederzeit aktualisieren (über **Verwalten** > **Aktuell replizierte Computer**). Nach Beginn der Replikation können Sie Einstellungen nicht mehr ändern.

### <a name="provisioning-for-the-first-time"></a>Erste Bereitstellung

Wenn dies die erste VM ist, die Sie im Projekt replizieren, werden diese Ressourcen von der Servermigration automatisch in derselben Ressourcengruppe wie das Projekt bereitgestellt.

- **Service Bus**: Bei der Servermigration wird der Service Bus verwendet, um Nachrichten zur Replikationsorchestrierung an die Appliance zu senden.
- **Gatewayspeicherkonto**: Bei der Servermigration wird das Gatewayspeicherkonto verwendet, um Statusinformationen zu den replizierten VMs zu speichern.
- **Protokollspeicherkonto**: Die Azure Migrate-Appliance lädt Replikationsprotokolle für VMs in ein Protokollspeicherkonto hoch. Azure Migrate wendet die Replikationsinformationen auf die verwalteten Replikatdatenträger an.
- **Schlüsseltresor**: Von der Azure Migrate-Appliance wird der Schlüsseltresor verwendet, um Verbindungszeichenfolgen für den Service Bus zu verwalten, und Zugriffsschlüssel werden für die Speicherkonten genutzt, die bei der Replikation eingesetzt werden.

## <a name="track-and-monitor"></a>Nachverfolgen und Überwachen

1. Verfolgen Sie den Auftragsstatus in den Portalbenachrichtigungen.
2. Überwachen Sie den Replikationsstatus durch Klicken auf **Server werden repliziert** (in **Azure Migrate: Servermigration**).

     ![Überwachen der Replikation](./media/tutorial-migrate-vmware/replicating-servers.png)

Die Replikation wird wie folgt durchgeführt:
- Nachdem der Auftrag „Replikation starten“ erfolgreich abgeschlossen wurde, beginnen die Computer mit der ersten Replikation in Azure.
- Während der ersten Replikation wird eine VM-Momentaufnahme erstellt. Die Datenträgerdaten der Momentaufnahme werden auf verwalteten Replikatdatenträgern in Azure repliziert.
- Nach Abschluss der ersten Replikation beginnt die Deltareplikation. Inkrementelle Änderungen an lokalen Datenträgern werden regelmäßig auf den Replikatdatenträgern in Azure repliziert.

## <a name="run-a-test-migration"></a>Ausführen einer Testmigration


Wenn die Deltareplikation beginnt, können Sie zunächst eine Testmigration für die VMs ausführen, bevor eine vollständige Migration zu Azure erfolgt. Dieser Schritt sollte vor der Migration mindestens einmal für jeden Computer ausgeführt werden.

- Bei einer Testmigration wird überprüft, ob die Migration wie erwartet funktioniert, ohne die lokalen Computer zu beeinträchtigen, und ob diese während der Replikation betriebsbereit bleiben. 
- Mit der Testmigration wird die Migration simuliert, indem eine Azure-VM mit replizierten Daten erstellt wird (normalerweise per Migration zu einem nicht für die Produktion bestimmten VNET unter Ihrem Azure-Abonnement).
- Sie können den replizierten virtuellen Azure-Testcomputer verwenden, um die Migration zu überprüfen, App-Tests durchzuführen und Probleme zu beheben, bevor die vollständige Migration erfolgt.

Führen Sie die Testmigration wie folgt durch:


1. Klicken Sie unter **Migrationsziele** > **Server** > **Azure Migrate: Servermigration** auf **Migrierte Server testen**.

     ![Testen der migrierten Server](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Klicken Sie mit der rechten Maustaste auf die zu testende VM, und klicken Sie anschließend auf **Testmigration**.

    ![Testmigration](./media/tutorial-migrate-vmware/test-migrate.png)

3. Wählen Sie unter **Testmigration** das Azure VNET aus, in dem sich die Azure-VM nach der Migration befindet. Es empfiehlt sich, ein nicht für die Produktion bestimmtes VNET zu verwenden.
4. Der Auftrag **Testmigration** wird gestartet. Überwachen Sie den Auftrag anhand der Portalbenachrichtigungen.
5. Zeigen Sie die migrierte Azure-VM nach Abschluss der Migration im Azure-Portal unter **Virtuelle Computer** an. Der Computername enthält das Suffix **-Test**.
6. Klicken Sie nach Abschluss des Tests mit der rechten Maustaste unter **Aktuell replizierte Computer** auf die Azure-VM, und klicken Sie anschließend auf **Testmigration bereinigen**.

    ![Bereinigen der Migration](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>Migrieren virtueller Computer

Nachdem Sie sich vergewissert haben, dass die Testmigration wie erwartet funktioniert, können Sie die lokalen Computer migrieren.

1. Klicken Sie im Azure Migrate-Projekt unter **Server** > **Azure Migrate: Servermigration** auf **Server werden repliziert**.

    ![Replizieren der Server](./media/tutorial-migrate-vmware/replicate-servers.png)

2. Klicken Sie unter **Aktuell replizierte Computer** mit der rechten Maustaste auf die VM und dann auf **Migrieren**.
3. Wählen Sie unter **Migrieren** > **Virtuelle Computer herunterfahren und eine geplante Migration ohne Datenverlust durchführen?** die Option **Ja** >  und anschließend **OK** aus.
    - Azure Migrate fährt die lokale VM standardmäßig herunter und führt eine bedarfsabhängige Replikation aus, um alle VM-Änderungen zu synchronisieren, die seit der letzten Replikation vorgenommen wurden. So wird sichergestellt, dass keine Daten verloren gehen.
    - Falls Sie die VM nicht herunterfahren möchten, wählen Sie **Nein** aus.
4. Für den virtuellen Computer wird ein Migrationsauftrag gestartet. Verfolgen Sie den Auftrag anhand der Azure-Benachrichtigungen nach.
5. Nach Abschluss des Auftrags können Sie die VM auf der Seite **Virtuelle Computer** anzeigen und verwalten.

## <a name="complete-the-migration"></a>Fertigstellen der Migration

1. Klicken Sie nach Abschluss der Migration mit der rechten Maustaste auf den virtuellen Computer, und wählen Sie **Replikation beenden** aus. Daraufhin wird Replikation für den lokalen Computer beendet, und die Informationen zum Replikationsstatus der VM werden bereinigt.
2. Installieren Sie den [Windows](../virtual-machines/extensions/agent-windows.md)- oder [Linux](../virtual-machines/extensions/agent-linux.md)-Agent für die Azure-VM auf den migrierten Computern.
3. Führen Sie App-Anpassungen nach der Migration durch, z.B. die Aktualisierung von Datenbankverbindungszeichenfolgen und Webserverkonfigurationen.
4. Führen Sie endgültige Anwendungs- und Migrationsakzeptanztests für die migrierte Anwendung durch, die nun in Azure ausgeführt wird.
5. Leiten Sie den Datenverkehr auf die migrierte Instanz der Azure-VM um.
6. Entfernen Sie die lokalen VMs aus Ihrem lokalen VM-Bestand.
7. Entfernen Sie die lokalen VMs aus lokalen Sicherungen.
8. Aktualisieren Sie die interne Dokumentation zum Anzeigen des neuen Speicherorts und der IP-Adresse der Azure-VMs. 

## <a name="post-migration-best-practices"></a>Bewährte Methoden nach der Migration

- Beachten Sie zur Steigerung der Resilienz Folgendes:
    - Schützen Sie Daten, indem Sie Azure-VMs mit dem Azure Backup-Dienst sichern. [Weitere Informationen](../backup/quick-backup-vm-portal.md)
    - Sorgen Sie für die kontinuierliche Ausführung und Verfügbarkeit von Workloads, indem Sie Azure-VMs mithilfe von Site Recovery in eine sekundäre Region replizieren. [Weitere Informationen](../site-recovery/azure-to-azure-tutorial-enable-replication.md)
- Beachten Sie zur Steigerung der Sicherheit Folgendes:
    - Sperren und beschränken Sie den Zugriff von eingehendem Datenverkehr mit der [Just-in-Time-Verwaltung im Azure Security Center](../security-center/security-center-just-in-time.md).
    - Beschränken Sie den Netzwerkdatenverkehr mithilfe von [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md) auf Verwaltungsendpunkte.
    - Stellen Sie [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) bereit, um Datenträger und Daten vor Diebstahl und unbefugtem Zugriff zu schützen.
    - Erfahren Sie mehr über das [Sichern von IaaS-Ressourcen](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/), und besuchen Sie die Website [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Beachten Sie zur Überwachung und Verwaltung Folgendes:
-  Ziehen Sie die Bereitstellung von [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) in Erwägung, um den Ressourceneinsatz und die Ausgaben zu überwachen.


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den Artikel zur [Cloudmigration](/azure/architecture/cloud-adoption/getting-started/migrate) des Frameworks für die Cloudeinführung (Cloud Adoption Framework) an.
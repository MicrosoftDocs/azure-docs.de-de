---
title: Einrichten von Azure Site Recovery für Azure VMware Solution-VMs
description: Erfahren Sie, wie Sie mit Azure Site Recovery Notfallwiederherstellung in Azure für VMs von Azure VMware Solution einrichten.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 3ac1f5bd3d44b7f98284cead60b34689f3d7be30
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395494"
---
# <a name="setup-azure-site-recovery-for-azure-vmware-solution-vms"></a>Einrichten von Azure Site Recovery für Azure VMware Solution-VMs

In diesem Artikel wird beschrieben, wie Sie mithilfe des Diensts [Azure Site Recovery](site-recovery-overview.md) Replikation für VMs von Azure VMware Solution für die Notfallwiederherstellung in Azure aktivieren.

Dies ist das dritte in einer Reihe von Tutorials zur Einrichtung der Notfallwiederherstellung in Azure für VMs von Azure VMware Solution. Im vorherigen Tutorial haben Sie sich mit der [Vorbereitung der Azure VMware Solution-Umgebung](avs-tutorial-prepare-avs.md) für die Notfallwiederherstellung in Azure befasst.


In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Einrichten der Einstellungen der Replikationsquelle und eines Site Recovery-Konfigurationsservers in der privaten Azure VMware Solution-Cloud
> * Einrichten der Einstellungen des Replikationsziels
> * Erstellen einer Replikationsrichtlinie
> * Aktivieren der Replikation für eine VMware-VM

> [!NOTE]
> In den Tutorials wird der einfachste Bereitstellungspfad für ein Szenario erläutert. Sie verwenden nach Möglichkeit Standardoptionen und zeigen nicht alle möglichen Einstellungen und Pfade. Ausführliche Anweisungen finden Sie in dem Artikel, der im Anleitungsabschnitt des Site Recovery-Inhaltsverzeichnisses angegeben ist.

## <a name="before-you-start"></a>Vorbereitung

Absolvieren Sie die vorherigen Tutorials:

1. Vergewissern Sie sich, dass Sie die Schritte zum [Einrichten von Azure](avs-tutorial-prepare-azure.md) für die Notfallwiederherstellung in Azure ausgeführt haben.
2. Führen Sie [diese Schritte](avs-tutorial-prepare-avs.md) aus, um Ihre Azure VMware Solution-Bereitstellung für die Notfallwiederherstellung in Azure vorzubereiten.
3. In diesem Tutorial wird gezeigt, wie Sie einen einzelnen virtuellen Computer replizieren. Wenn Sie mehrere VMs bereitstellen, sollten Sie das [Bereitstellungsplaner-Tool](https://aka.ms/asr-deployment-planner) verwenden. [Hier](site-recovery-deployment-planner.md) finden Sie weitere Informationen zu diesem Tool.
4. In diesem Tutorial wird eine Reihe von Optionen verwendet, für die Sie möglicherweise eine andere Vorgehensweise vorziehen:
    - Im Tutorial wird eine OVA-Vorlage (Open Virtualization Application) verwendet, um die VM für den VMware-Konfigurationsserver zu erstellen. Falls dies aus irgendeinem Grund nicht möglich ist, befolgen Sie [diese Anweisungen](physical-manage-configuration-server.md), um den Konfigurationsserver manuell einzurichten.
    - In diesem Tutorial wird MySQL automatisch von Site Recovery heruntergeladen und auf dem Konfigurationsserver installiert. Sie können die Einrichtung jedoch auch manuell vornehmen. [Weitere Informationen](vmware-azure-deploy-configuration-server.md#configure-settings)




## <a name="select-a-protection-goal"></a>Wählen eines Schutzziels

1. Wählen Sie unter **Recovery Services-Tresore** den Tresornamen aus. In diesem Szenario verwenden wir **ContosoVMVault**.
2. Wählen Sie unter **Erste Schritte** die Option „Site Recovery“ aus. Klicken Sie anschließend auf **Infrastruktur vorbereiten**.
3. Wählen Sie in **Schutzziel** > **Wo befinden sich Ihre Computer?** die Option **Lokal** aus.
4. Wählen Sie in **Wohin möchten Sie Ihre Computer replizieren?** die Option **Nach Azure** aus.
5. Wählen Sie in **Sind Ihre Computer virtualisiert?** die Option **Ja, mit VMware vSphere Hypervisor** aus. Klicken Sie anschließend auf **OK**.



## <a name="set-up-the-source-environment"></a>Einrichten der Quellumgebung

In Ihrer Quellumgebung benötigen Sie einen einzelnen, hochverfügbaren, lokalen Computer, um die folgenden lokalen Site Recovery-Komponenten zu hosten:

- **Konfigurationsserver** : Der Konfigurationsserver koordiniert die Kommunikation zwischen der privaten Azure VMware Solution-Cloud und Azure und verwaltet die Datenreplikation.
- **Prozessserver** Der Prozessserver fungiert als Replikationsgateway. Er empfängt Replikationsdaten, optimiert sie durch Zwischenspeicherung, Komprimierung und Verschlüsselung und sendet sie an ein Cachespeicherkonto in Azure. Der Prozessserver installiert auch den Mobility Service-Agent auf VMs, die Sie replizieren möchten, und führt eine automatische Ermittlung der VMs von Azure VMware Solution durch.
- **Masterzielserver** : Der Masterzielserver verarbeitet die Replikationsdaten während des Failbacks von Azure.


Alle diese Komponenten werden zusammen auf dem einzelnen Azure VMware Solution-Computer installiert, der als *Konfigurationsserver* bezeichnet wird. Für die Azure VMware Solution-Notfallwiederherstellung richten wir den Konfigurationsserver standardmäßig als hochverfügbare VMware-VM ein. Hierzu laden Sie eine vorbereitete OVA-Vorlage (Open Virtualization Application) herunter, die Sie dann zum Erstellen der VM in VMware importieren.

- Die aktuelle Version des Konfigurationsservers steht im Portal zur Verfügung. Sie können sie auch direkt aus dem [Microsoft Download Center](https://aka.ms/asrconfigurationserver) herunterladen.
- Falls Sie die OVA-Vorlage aus irgendeinem Grund nicht zum Einrichten einer VM verwenden können, befolgen Sie [diese Anweisungen](physical-manage-configuration-server.md), um den Konfigurationsserver manuell einzurichten.
- Bei der mit der OVF-Vorlage bereitgestellten Lizenz handelt es sich um eine Evaluierungslizenz mit einer Gültigkeit von 180 Tagen. Die auf dem virtuellen Computer ausgeführte Windows-Anwendung muss mit der erforderlichen Lizenz aktiviert werden.


### <a name="download-the-vm-template"></a>Herunterladen der Vorlage

1. Navigieren Sie im Tresor zu **Infrastruktur vorbereiten** > **Quelle**.
2. Klicken Sie unter **Quelle vorbereiten** auf **+Konfigurationsserver**.
3. Überprüfen Sie unter **Server hinzufügen** , ob unter **Servertyp** die Option **Konfigurationsserver für VMware** angezeigt wird.
4. Laden Sie die OVA-Vorlage für den Konfigurationsserver herunter.



## <a name="import-the-template-in-vmware"></a>Importieren der Vorlage in VMware


1. Melden Sie sich mit dem VMware vSphere-Client beim VMware vCenter-Server oder beim vSphere ESXi-Host an.
2. Wählen Sie im Menü **Datei** die Option **OVF-Vorlage bereitstellen** aus, um den **Assistenten zum Bereitstellen von OVF-Vorlagen** zu starten.

     ![Screenshot: Befehl „OVF-Vorlage bereitstellen“ im VMWare vSphere-Client](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. Geben Sie unter **Quelle auswählen** den Speicherort der heruntergeladenen OVF-Vorlage ein.
4. Klicken Sie unter **Bewertungsdetails** auf **Weiter**.
5. Übernehmen Sie unter **Name und Ordner auswählen** und **Konfiguration auswählen** die Standardeinstellungen.
6. Wählen Sie unter **Speicher auswählen** für **Select virtual disk format** (Format für virtuellen Datenträger auswählen) die Option **Thick Provision Eager Zeroed** (Breite Bereitstellung gegen null) aus, um eine optimale Leistung zu erzielen.
7. Übernehmen Sie auf den übrigen Seiten des Assistenten die Standardeinstellungen.
8. Wählen Sie unter **Zum Abschluss bereit** zum Einrichten des virtuellen Computers mit den Standardeinstellungen **Power on after deployment** (Nach Bereitstellung einschalten) > **Fertig stellen** aus.

   > [!TIP]
   > Wenn Sie einen weiteren Netzwerkadapter hinzufügen möchten, deaktivieren Sie **Power on after deployment** (Nach Bereitstellung einschalten) > **Fertig stellen**. Standardmäßig enthält die Vorlage einen einzelnen Netzwerkadapter. Weitere NICs können nach der Bereitstellung hinzugefügt werden.

## <a name="add-an-additional-adapter"></a>Hinzufügen zusätzlicher Adapter

Wenn Sie dem Konfigurationsserver eine zusätzliche NIC hinzufügen möchten, führen Sie diesen Schritt aus, bevor Sie den Server im Tresor registrieren. Das Hinzufügen von zusätzlichen Adaptern nach der Registrierung wird nicht unterstützt.

1. Klicken Sie im vSphere-Client mit der rechten Maustaste auf den virtuellen Computer, und wählen Sie **Einstellungen bearbeiten** aus.
2. Klicken Sie unter **Hardware** auf **Hinzufügen** > **Ethernet-Adapter**. Wählen Sie **Weiter** aus.
3. Wählen Sie einen Adaptertyp und ein Netzwerk aus.
4. Um die virtuelle NIC zu verbinden, wenn der virtuelle Computer eingeschaltet wird, wählen Sie **Connect at power on** (Beim Einschalten verbinden) aus. Klicken Sie auf **Weiter** > **Fertig stellen**. Klicken Sie anschließend auf **OK**.


## <a name="register-the-configuration-server"></a>Registrieren des Konfigurationsservers

Nachdem Sie den Konfigurationsserver eingerichtet haben, registrieren Sie ihn im Tresor.

1. Schalten Sie den virtuellen Computer über die Konsole des VMware vSphere-Clients ein.
2. Der virtuelle Computer wird mit der Benutzeroberfläche für die Installation von Windows Server 2016 hochgefahren. Akzeptieren Sie den Lizenzvertrag, und geben Sie ein Administratorkennwort ein.
3. Melden Sie sich nach Abschluss der Installation als Administrator am virtuellen Computer an.
4. Bei der ersten Anmeldung wird das Azure Site Recovery-Konfigurationstool innerhalb weniger Sekunden gestartet.
5. Geben Sie einen Namen ein, der für die Registrierung des Konfigurationsservers bei Site Recovery verwendet wird. Wählen Sie **Weiter** aus.
6. Das Tool überprüft, ob der virtuelle Computer eine Verbindung mit Azure herstellen kann. Klicken Sie nach der Verbindungsherstellung auf **Anmelden** , um sich bei Ihrem Azure-Abonnement anzumelden. Die Anmeldeinformationen müssen über Zugriff auf den Tresor verfügen, in dem Sie den Konfigurationsserver registrieren möchten. Stellen Sie sicher, dass diesem Benutzer die erforderlichen [Rollen](vmware-azure-deploy-configuration-server.md#azure-active-directory-permission-requirements) zugewiesen sind.
7. Das Tool führt einige Konfigurationsaufgaben und anschließend einen Neustart durch.
8. Melden Sie sich erneut am Computer an. Der Assistent für die Konfigurationsserververwaltung wird innerhalb weniger Sekunden automatisch gestartet.


### <a name="configure-settings-and-add-the-vmware-server"></a>Konfigurieren der Einstellungen und Hinzufügen des VMware-Servers

Schließen Sie die Einrichtung und Registrierung des Konfigurationsservers ab. Stellen Sie vor dem Fortfahren sicher, dass alle [Voraussetzungen](vmware-azure-deploy-configuration-server.md#prerequisites) für eine erfolgreiche Einrichtung des Konfigurationsservers erfüllt sind.


1. Wählen Sie im Assistent für die Konfigurationsserververwaltung die Option **Konnektivität einrichten**. Wählen Sie in den Dropdownlisten zunächst den Netzwerkadapter aus, den der integrierte Prozessserver für die Ermittlung und die Pushinstallation des Mobilitätsdiensts auf Quellcomputern verwendet. Wählen Sie anschließend den Netzwerkadapter aus, den der Konfigurationsserver zum Herstellen einer Verbindung mit Azure nutzt. Klicken Sie dann auf **Speichern**. Diese Einstellung kann nach der Konfiguration nicht mehr geändert werden.
2. Wählen Sie unter **Recovery Services-Tresor auswählen** Ihr Azure-Abonnement, die entsprechende Ressourcengruppe und den entsprechenden Tresor aus.
3. Akzeptieren Sie unter **Drittanbietersoftware installieren** den Lizenzvertrag. Klicken Sie auf **Herunterladen und installieren** , um MySQL-Server zu installieren. Wenn Sie MySQL im Pfad platziert haben, kann dieser Schritt übersprungen werden. Weitere Informationen finden Sie [hier](vmware-azure-deploy-configuration-server.md#configure-settings).
4. Unter **Anwendungskonfiguration überprüfen** werden die Voraussetzungen überprüft, bevor der Vorgang fortgesetzt wird.
5. Geben Sie unter **vCenter-Server/vSphere ESXi-Server konfigurieren** den FQDN oder die IP-Adresse des vCenter-Servers oder vSphere-Hosts ein, auf dem sich die virtuellen Computer befinden, die repliziert werden sollen. Geben Sie den Port ein, über den der Server lauscht. Geben Sie einen Anzeigenamen ein, der für den VMware-Server im Tresor verwendet werden soll.
6. Geben Sie die Benutzeranmeldeinformationen ein, die der Konfigurationsserver zum Herstellen der Verbindung mit dem VMware-Server verwenden soll. Stellen Sie sicher, dass der Benutzername und das Kennwort korrekt sind und dieser Benutzer der Gruppe „Administratoren“ der zu schützenden VM angehört. Site Recovery verwendet diese Anmeldeinformationen für die automatische Erkennung von VMware-VMs, die für die Replikation verfügbar sind. Klicken Sie auf **Hinzufügen** und anschließend auf **Weiter**.
7. Geben Sie unter **Anmeldeinformationen für virtuelle Computer konfigurieren** den Benutzernamen und das Kennwort an, die für die automatische Installation von Mobility Service auf virtuellen Computern verwendet werden sollen, wenn die Replikation aktiviert ist.
    - Für Windows-Computer benötigt das Konto lokale Administratorrechte auf den Computern, die Sie replizieren möchten.
    - Bei Linux geben Sie die Anmeldeinformationen für das root-Konto an.
8. Klicken Sie auf **Konfiguration abschließen** , um die Registrierung abzuschließen.
9. Nachdem die Registrierung abgeschlossen ist, öffnen Sie das Azure-Portal, und stellen Sie sicher, dass der Konfigurationsserver und der VMware-Server unter **Recovery Services-Tresor** > **Verwalten** > **Site Recovery-Infrastruktur** > **Konfigurationsserver** aufgeführt sind.


Site Recovery stellt nach der Registrierung des Konfigurationsservers anhand der angegebenen Einstellungen eine Verbindung mit VMware-Servern her und ermittelt VMs.

> [!NOTE]
> Es kann 15 Minuten oder länger dauern, bis der Kontoname im Portal angezeigt wird. Klicken Sie zur sofortigen Aktualisierung auf **Konfigurationsserver** > **_Servername_ *_ > _* Server aktualisieren**.

## <a name="set-up-the-target-environment"></a>Einrichten der Zielumgebung

Wählen Sie Zielressourcen aus, und überprüfen Sie sie.

1. Klicken Sie auf **Infrastruktur vorbereiten** > **Ziel**. Wählen Sie das Azure-Abonnement aus, das Sie verwenden möchten. Hier wird ein Resource Manager-Modell verwendet.
2. Site Recovery überprüft, ob virtuelle Netzwerke vorhanden sind. Diese sollten vorhanden sein, wenn Sie die Azure-Komponenten im [ersten Tutorial](tutorial-prepare-azure.md) in dieser Tutorialreihe eingerichtet haben.

   ![Screenshot: „Infrastruktur vorbereiten“ und Zieloptionen](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>Erstellen einer Replikationsrichtlinie

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Suchen Sie nach **Recovery Services-Tresoren** , und wählen Sie die Option aus.
2. Wählen Sie den Recovery Services-Tresor (in diesem Tutorial: **ContosoVMVault** ) aus.
3. Klicken Sie zum Erstellen einer Replikationsrichtlinie auf **Site Recovery-Infrastruktur** > **Replikationsrichtlinien** >  **+Replikationsrichtlinie**.
4. Geben Sie unter **Replikationsrichtlinie erstellen** den Richtliniennamen ein. Hier wird **VMwareRepPolicy** verwendet.
5. Übernehmen Sie in **RPO-Schwellenwert** den Standardwert von 60 Minuten. Mit diesem Wert wird festgelegt, wie oft Wiederherstellungspunkte erstellt werden. Wenn dieser Grenzwert bei der fortlaufenden Replikation überschritten wird, wird eine Warnung generiert.
6. Geben Sie unter **Aufbewahrungszeitraum des Wiederherstellungspunkts** an, wie lange die einzelnen Wiederherstellungspunkte aufbewahrt werden. Für dieses Tutorial legen wir 72 Stunden fest. Replizierte virtuelle Computer können für jeden Punkt im Aufbewahrungszeitfenster wiederhergestellt werden.
7. Geben Sie unter **App-konsistente Momentaufnahmehäufigkeit** an, wie häufig anwendungskonsistente Momentaufnahmen erstellt werden. Wir verwenden den Standardwert von 60 Minuten. Wählen Sie **OK** aus, um die Richtlinie zu erstellen.

   ![Screenshot: Optionen zum Erstellen einer Replikationsrichtlinie](./media/vmware-azure-tutorial/replication-policy.png)

- Die Richtlinie wird dem Konfigurationsserver automatisch zugeordnet.
- Für das Failback wird standardmäßig automatisch eine passende Richtlinie erstellt. Bei Verwendung der Replikationsrichtlinie **rep-policy** lautet die Failbackrichtlinie beispielsweise **rep-policy-failback**. Diese Richtlinie wird erst verwendet, wenn Sie ein Failback über Azure initiieren.

Hinweis: Im Szenario „VMware zu Azure“ wird die absturzkonsistente Momentaufnahme in einem Intervall von fünf Minuten erstellt.

## <a name="enable-replication"></a>Aktivieren der Replikation

Aktivieren Sie die Replikation für VMs wie folgt:

1. Klicken Sie auf **Anwendung replizieren** > **Quelle**.
2. Wählen Sie unter **Quelle** die Option **Lokal** und dann unter **Quellpfad** den Konfigurationsserver aus.
3. Wählen Sie unter **Computertyp** die Option **Virtuelle Computer**.
4. Wählen Sie unter **vCenter-/vSphere-Hypervisor** den vSphere-Host oder den vCenter-Server aus, der den Host verwaltet.
5. Wählen Sie den Prozessserver aus (wird standardmäßig auf dem virtuellen Computer mit dem Konfigurationsserver installiert). Klicken Sie anschließend auf **OK**. Der Integritätsstatus der einzelnen Prozessserver wird gemäß der empfohlenen Grenzwerte und anderer Parameter angezeigt. Wählen Sie einen fehlerfreien Prozessserver aus. Ein [kritischer](vmware-physical-azure-monitor-process-server.md#process-server-alerts) Prozessserver kann nicht ausgewählt werden. Sie können entweder die Fehler [behandeln und beheben](vmware-physical-azure-troubleshoot-process-server.md)**oder** einen [horizontal hochskalierten Prozessserver](vmware-azure-set-up-process-server-scale.md) einrichten.
6. Wählen Sie unter **Ziel** das Abonnement und die Ressourcengruppe aus, in dem bzw. in der Sie die virtuellen Computer erstellen möchten, für die ein Failover durchgeführt wurde. Hier wird das Resource Manager-Bereitstellungsmodell verwendet.
7. Wählen Sie das Azure-Netzwerk und das Subnetz aus, mit dem virtuelle Azure-Computer, die nach einem Failover erstellt werden, eine Verbindung herstellen sollen.
8. Wählen Sie **Jetzt für die ausgewählten Computer konfigurieren** aus, um die Netzwerkeinstellungen auf alle virtuellen Computer anzuwenden, auf denen Sie die Replikation aktivieren. Wählen Sie **Später konfigurieren** aus, um das Azure-Netzwerk pro Computer auszuwählen.
9. Wählen Sie auf **Virtuelle Computer** > **Virtuelle Computer auswählen** die Computer aus, die Sie replizieren möchten. Sie können nur Computer auswählen, für die die Replikation aktiviert werden kann. Klicken Sie anschließend auf **OK**. Sollten Sie eine bestimmte VM nicht anzeigen/auswählen können, finden Sie [hier](./vmware-azure-troubleshoot-replication.md) Informationen zum Beheben des Problems.
10. Wählen Sie unter **Eigenschaften** > **Eigenschaften konfigurieren** das Konto aus, das der Prozessserver zum automatischen Installieren von Mobility Service auf dem Computer verwenden soll.
11. Überprüfen Sie unter **Replikationseinstellungen** > **Replikationseinstellungen konfigurieren** , ob die richtige Replikationsrichtlinie ausgewählt ist.
12. Klicken Sie auf **Replikation aktivieren**. Site Recovery installiert den Mobility Service, wenn die Replikation für einen virtuellen Computer aktiviert wird.
13. Sie können den Fortschritt des Auftrags **Schutz aktivieren** unter **Einstellungen** > **Aufträge** > **Site Recovery-Aufträge** verfolgen. Wenn der Auftrag **Schutz abschließen** ausgeführt und ein Wiederherstellungspunkt erstellt wurde, ist der Computer bereit für das Failover.
14. Es kann länger als 15 Minuten dauern, bis die Änderungen wirksam und im Portal angezeigt werden.
15. Überprüfen Sie zur Überwachung der hinzugefügten virtuellen Computer den Zeitpunkt der letzten Ermittlung für virtuelle Computer unter **Konfigurationsserver** > **Letzter Kontakt um**. Wenn Sie virtuelle Computer hinzufügen möchten, ohne auf die geplante Ermittlung zu warten, markieren Sie den Konfigurationsserver (wählen Sie ihn aber nicht aus), und klicken Sie auf **Aktualisieren**.

## <a name="next-steps"></a>Nächste Schritte
Führen Sie nach dem Aktivieren der Replikation eine Übung durch, um sicherzustellen, dass alles wie erwartet funktioniert.
> [!div class="nextstepaction"]
> [Durchführen eines Notfallwiederherstellungsverfahrens](avs-tutorial-dr-drill-azure.md)

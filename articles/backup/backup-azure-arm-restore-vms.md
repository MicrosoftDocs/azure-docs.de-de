---
title: 'Azure Backup: Wiederherstellen virtueller Computer über das Azure-Portal'
description: Wiederherstellen eines virtuellen Azure-Computers mithilfe eines Wiederherstellungspunkts über das Azure-Portal
services: backup
author: geethalakshmig
manager: vijayts
keywords: Sicherung wiederherstellen; Wiederherstellungsschritte; Wiederherstellungspunkt;
ms.service: backup
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: geg
ms.openlocfilehash: 19b249a76a339ce870609fbcdceaf70bf79a6ea2
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65906509"
---
# <a name="restore-azure-vms"></a>Wiederherstellen virtueller Azure-Computer

Dieser Artikel beschreibt, wie Sie Azure VM-Daten aus den Wiederherstellungspunkten wiederherstellen können, die in [Azure Backup](backup-overview.md) Recovery Services-Tresoren gespeichert sind.



## <a name="restore-options"></a>Wiederherstellungsoptionen

Azure Backup bietet eine Reihe von Möglichkeiten zum Wiederherstellen einer VM.

**Wiederherstellungsoptionen** | **Details**
--- | ---
**Eine neue VM erstellen** | Sie können schnell eine einfache VM erstellen und können Sie über einen Wiederherstellungspunkt betriebsbereit machen.<br/><br/> Sie können einen Namen für die VM angeben, die Ressourcengruppe und das virtuelle Netzwerk (VNet) auswählen, in dem sie platziert werden soll, und ein Speicherkonto für die wiederhergestellte VM angeben.
**Datenträger wiederherstellen** | Stellt einen VM-Datenträger wieder her, der dann zum Erstellen einer neuen VM verwendet werden kann.<br/><br/> Azure Backup bietet eine Vorlage, mit der Sie eine VM anpassen und erstellen können. <br/><br> Der Wiederherstellungsauftrag generiert eine Vorlage, die Sie herunterladen und verwenden können, um benutzerdefinierte VM-Einstellungen festzulegen und eine VM zu erstellen.<br/><br/> Die Datenträger werden in das von Ihnen angegebene Speicherkonto kopiert.<br/><br/> Alternativ können Sie den Datenträger an eine vorhandene VM anhängen oder mit PowerShell eine neue VM erstellen.<br/><br/> VM anpassen, Konfigurationseinstellungen hinzufügen möchten, die zum Zeitpunkt der Sicherung nicht vorhanden waren, oder Einstellungen hinzufügen möchten, die mithilfe der Vorlage oder von PowerShell konfiguriert werden müssen.
**Vorhandene ersetzen** | Sie können einen Datenträger wiederherstellen und damit einen Datenträger auf der vorhandenen VM ersetzen.<br/><br/> Der aktuelle virtuelle Computer muss jedoch vorhanden sein. Wenn dieser gelöscht wurde, kann diese Option nicht verwendet werden.<br/><br/> Vor dem Ersetzen des Datenträgers erstellt Azure Backup eine Momentaufnahme der vorhandenen VM und speichert sie im von Ihnen Stagingspeicherort. Vorhandene Datenträger, die mit der VM verbunden sind, werden durch den ausgewählten Wiederherstellungspunkt ersetzt.<br/><br/> Die Momentaufnahme wird in den Tresor kopiert und entsprechend der Aufbewahrungsrichtlinie beibehalten. <br/><br/> Die Option „Vorhandene ersetzen“ wird für nicht verschlüsselte verwaltete VMs unterstützt. Für nicht verwaltete Datenträger, [generalisierte VMs](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource) oder für virtuelle Computer, die mit [benutzerdefinierten Images](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/) erstellt wurden, wird sie nicht unterstützt.<br/><br/> Wenn der Wiederherstellungspunkt mehr oder weniger Datenträger als die aktuelle VM aufweist, dann spiegelt die Anzahl der Datenträger im Wiederherstellungspunkt nur die VM-Konfiguration wieder.<br/><br/>


> [!NOTE]
> Sie können auch bestimmte Dateien und Ordner auf einer Azure-VM wiederherstellen. [Weitere Informationen](backup-azure-restore-files-from-vm.md)
>
> Wenn Sie die [neueste Version](backup-instant-restore-capability.md) von Azure Backup für Azure-VMs (bekannt als sofortige Wiederherstellung) verwenden, werden Momentaufnahmen bis zu sieben Tage lang aufbewahrt, und Sie können eine VM aus Momentaufnahmen wiederherstellen, bevor die Sicherungsdaten an den Tresor gesendet werden. Wenn Sie eine VM aus einer Sicherung der letzten sieben Tage wiederherstellen möchten, geht es schneller, sie aus der Momentaufnahme und nicht aus dem Tresor wiederherzustellen.

## <a name="storage-accounts"></a>Speicherkonten

Einige Details zu Speicherkonten:

- **Virtuellen Computer erstellen:** Wenn Sie einen neuen virtuellen Computer erstellen, wird er im von Ihnen angegebenen Speicherkonto platziert.
- **Datenträger wiederherstellen**: Wenn Sie einen Datenträger wiederherstellen, wird er in das von Ihnen angegebene Speicherkonto kopiert. Der Wiederherstellungsauftrag generiert eine Vorlage, die Sie herunterladen und verwenden können, um benutzerdefinierte VM-Einstellungen festzulegen. Diese Vorlage wird im angegebenen Speicherkonto platziert.
- **Datenträger ersetzen**: Wenn Sie einen Datenträger auf einem vorhandenen virtuellen Computer ersetzen, erstellt Azure Backup eine Momentaufnahme von diesem Computer, bevor der Datenträger ersetzt wird. Die Momentaufnahme wird im von Ihnen angegebenen Stagingspeicherort (Speicherkonto) gespeichert. Dieses Speicherkonto wird zum vorübergehenden Speichern der Momentaufnahme während des Wiederherstellungsvorgangs verwendet. Wir empfehlen, dass Sie dafür ein neues Konto erstellen, dass später problemlos entfernt werden kann.
- **Standort des Speicherkontos**: Das Speicherkonto muss sich in derselben Region wie der Tresor befinden. Nur diese Konten werden angezeigt. Wenn es am Standort keine Speicherkonten gibt, müssen Sie ein Konto erstellen.
- **Speichertyp**: Blobspeicher wird nicht unterstützt.
- **Speicherredundanz**: Der zonenredundante Speicher (Zone Redundant Storage, ZRS) wird nicht unterstützt. Die Informationen zu Replikation und Redundanz für das Konto stehen hinter in Klammern hinter dem Kontonamen. 
- **Storage Premium**:
    - Beim Wiederherstellen von Nicht-Premium-VMs werden Storage Premium-Konten nicht unterstützt.
    - Beim Wiederherstellen von verwalteten VMs werden Storage Premium-Konten, die mit Netzwerkregeln konfiguriert wurden, nicht unterstützt.


## <a name="before-you-start"></a>Vorbereitung

Stellen Sie zum Wiederherstellen einer VM (neue VM erstellen) sicher, dass Sie über die richtigen [Berechtigungen](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) für rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) für den Vorgang „VM wiederherstellen“ verfügen.

Wenn dies nicht zutrifft, können Sie [einen Datenträger wiederherstellen](#restore-disks) und anschließend [mithilfe der Vorlage](#use-templates-to-customize-a-restored-vm), die im Rahmen des Wiederherstellungsvorgangs generiert wurde, eine neue VM erstellen.



## <a name="select-a-restore-point"></a>Auswählen eines Wiederherstellungspunkts

1. Klicken Sie in dem Tresor, der der wiederherzustellenden VM zugeordnet ist, auf **Sicherungselemente** > **Virtuelle Azure-Computer**.
2. Klicken Sie auf eine VM. Standardmäßig werden im VM-Dashboard die Wiederherstellungspunkte der letzten 30 Tage angezeigt. Sie können Wiederherstellungspunkte anzeigen, die älter als 30 Tage sind, oder nach Wiederherstellungspunkten filtern, die auf Daten, Zeitbereichen und verschiedenen Arten von Momentaufnahmen basieren.
3. Um eine VM wiederherzustellen, klicken Sie auf **VM wiederherstellen**.

    ![Wiederherstellungspunkt](./media/backup-azure-arm-restore-vms/restore-point.png)

4. Wählen Sie einen Wiederherstellungspunkt für die Wiederherstellung aus.

## <a name="choose-a-vm-restore-configuration"></a>Auswählen einer Konfiguration für die VM-Wiederherstellung

1. Wählen Sie unter **Konfiguration wiederherstellen** eine Wiederherstellungsoption aus:
    - **Neue erstellen**: Verwenden Sie diese Option, wenn Sie eine neue VM erstellen möchten. Sie können eine VM mit einfachen Einstellungen erstellen oder einen Datenträger wiederherstellen und eine benutzerdefinierte VM erstellen.
    - **Vorhandene ersetzen**: Verwenden Sie diese Option, wenn Sie Datenträger auf einer vorhandenen VM ersetzen möchten.

        ![Assistent für die Wiederherstellungskonfiguration](./media/backup-azure-arm-restore-vms/restore-configuration.png)

2. Geben Sie die Einstellungen für die ausgewählte Wiederherstellungsoption an.

## <a name="create-a-vm"></a>Erstellen einer VM

Als eine der [Wiederherstellungsoptionen](#restore-options) können Sie von einem Wiederherstellungspunkt aus schnell eine VM mit Grundeinstellungen erstellen.

1. Wählen Sie unter **Konfiguration wiederherstellen** > **Neu erstellen** > **Wiederherstellungstyp** die Option **Virtuellen Computer erstellen** aus.
2. Geben Sie unter **Name des virtuellen Computers** einen virtuellen Computer an, der nicht im Abonnement vorhanden ist.
3. Wählen Sie unter **Ressourcengruppe** eine vorhandene Ressourcengruppe für den neuen virtuellen Computer aus, oder erstellen Sie eine neue Ressourcengruppe mit einem global eindeutigen Namen. Wenn Sie einen Namen zuweisen, der bereits vorhanden ist, weist Azure der Gruppe denselben Namen wie der VM zu.
4. Wählen Sie unter **Virtuelles Netzwerk** das virtuelle Netzwerk aus, in das der virtuelle Computer platziert wird. Alle dem Abonnement zugeordneten VNETs werden angezeigt. Wählen Sie das Subnetz aus. Das erste Subnetz wird standardmäßig ausgewählt.
5. Geben Sie unter **Speicherort** das Speicherkonto für die VM an. [Weitere Informationen](#storage-accounts)

    ![Assistent für die Wiederherstellungskonfiguration](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. Klicken Sie unter **Konfiguration wiederherstellen** auf **OK**. Klicken Sie unter **Wiederherstellen** auf **Wiederherstellen**, um den Wiederherstellungsvorgang auszulösen.


## <a name="restore-disks"></a>Wiederherstellen von Datenträgern

Als eine der [Wiederherstellungsoptionen](#restore-options) können einen Datenträger aus einem Wiederherstellungspunkt erstellen. Anschließend haben Sie mit dem Datenträger folgende Möglichkeiten:

- Verwenden Sie die Vorlage, die während des Wiederherstellungsvorgangs generiert wurde, um Einstellungen anzupassen und die Bereitstellung der VM auszulösen. Bearbeiten Sie die Standardeinstellungen für die Vorlage, und übermitteln Sie die Vorlage für die Bereitstellung des virtuellen Computers.
- [Fügen Sie wiederhergestellte Datenträger](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) an eine vorhandene VM an.
- [Erstellen Sie eine neue VM](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks) aus den wiederhergestellten Datenträgern mit PowerShell.

1. Wählen Sie unter **Konfiguration wiederherstellen** > **Neu erstellen** > **Wiederherstellungstyp** die Option **Datenträger wiederherstellen** aus.
2. Wählen Sie unter **Ressourcengruppe** eine vorhandene Ressourcengruppe für den wiederhergestellten Datenträger aus, oder erstellen Sie eine neue Ressourcengruppe mit einem global eindeutigen Namen.
3. Geben Sie für das **Speicherkonto** das Konto an, in das die virtuellen Festplatten kopiert werden sollen. [Weitere Informationen](#storage-accounts)

    ![Konfiguration der Wiederherstellung abgeschlossen](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. Klicken Sie unter **Konfiguration wiederherstellen** auf **OK**. Klicken Sie unter **Wiederherstellen** auf **Wiederherstellen**, um den Wiederherstellungsvorgang auszulösen.

Während der VM-Wiederherstellung verwendet Azure Backup nicht das Speicherkonto. In den Fällen **Wiederherstellen von Datenträgern** und **Sofortige Wiederherstellung** dagegen wird das Speicherkonto zum Speichern der Vorlage verwendet.

### <a name="use-templates-to-customize-a-restored-vm"></a>Verwenden von Vorlagen zum Anpassen eines wiederhergestellten virtuellen Computers

Nachdem der Datenträger wiederhergestellt wurde, verwenden Sie die Vorlage, die im Rahmen der Wiederherstellung erstellt wurde, um eine neue VM anzupassen und zu erstellen:

1. Öffnen Sie **Details zum Wiederherstellungsauftrag** für den entsprechenden Auftrag.

2. Klicken Sie auf **Details zum Wiederherstellungsauftrag** auf **Vorlage bereitstellen**, um die Vorlagenbereitstellung zu initiieren.

    ![Drilldown des Wiederherstellungsauftrags](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. Um die in der Vorlage angegebene VM-Einstellung anzupassen, klicken Sie auf **Vorlage bearbeiten**. Wenn Sie weitere Anpassungen hinzufügen möchten, klicken Sie auf **Parameter bearbeiten**.
    - [Erfahren Sie mehr](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) zum Bereitstellen von Ressourcen aus einer benutzerdefinierten Vorlage.
    - [Erfahren Sie mehr](../azure-resource-manager/resource-group-authoring-templates.md) zum Erstellen von Vorlagen.

   ![Laden der Vorlagenbereitstellung](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Geben Sie die benutzerdefinierten Werte für die VM ein, akzeptieren Sie die **Geschäftsbedingungen** und klicken Sie auf **Kaufen**.

   ![Übermitteln der Vorlagenbereitstellung](./media/backup-azure-arm-restore-vms/submitting-template1.png)


## <a name="replace-existing-disks"></a>Ersetzen vorhandener Datenträger

Als eine der [Wiederherstellungsoptionen](#restore-options) können Sie einen vorhandenen VM-Datenträger durch den ausgewählten Wiederherstellungspunkt ersetzen. [Schauen Sie sich alle Wiederherstellungsoptionen an](#restore-options).

1. Klicken Sie unter **Konfiguration wiederherstellen** auf **Vorhandene ersetzen**.
2. Wählen Sie unter **Wiederherstellungstyp** die Option **Datenträger wiederherstellen**. Dies ist der Wiederherstellungspunkt, der zum Ersetzen vorhandener VM-Datenträger verwendet wird.
3. Geben Sie unter **Stagingspeicherort** an, wo Momentaufnahmen der aktuellen verwalteten Datenträger während des Wiederherstellungsvorgangs gespeichert werden sollen. [Weitere Informationen](#storage-accounts)

   ![Assistent für die Wiederherstellungskonfiguration – Vorhandene ersetzen](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)


## <a name="restore-vms-with-special-configurations"></a>Wiederherstellen von VMs mit besonderen Konfigurationen

Es gibt eine Reihe von häufigen Szenarien, in denen Sie möglicherweise VMs wiederherstellen müssen.

**Szenario** | **Leitfaden**
--- | ---
**Wiederherstellen von VM mit dem Vorteil der Hybridnutzung** | Wenn eine Windows-VM die [Lizenzierung für die Vorteile der Hybridnutzeung (HUB)](../virtual-machines/windows/hybrid-use-benefit-licensing.md) verwendet, stellen Sie die Datenträger wieder her, und erstellen Sie eine neue VM mit der bereitgestellten Vorlage (wobei **Lizenztyp** auf **Windows_Server** festgelegt ist) oder PowerShell.  Diese Einstellung kann auch nach dem Erstellen des virtuellen Computers angewendet werden.
**Wiederherstellen einer VM während einer Azure-Datencenter-Notfallwiederherstellung** | Wenn der Tresor GRS verwendet und das primäre Rechenzentrum für die VM ausfällt, unterstützt Azure Backup die Wiederherstellung von gesicherten VMs im gekoppelten Rechenzentrum. Sie wählen ein Speicherkonto im gekoppelten Rechenzentrum aus, und stellen es wie gewohnt wieder her. Azure Backup verwendet den Computedienst des gekoppelten Speicherorts, um die wiederhergestellte VM zu erstellen. Erfahren Sie mehr über die [Resilienz von Rechenzentren](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md).
**Wiederherstellen einer einzelnen Domänencontroller-VM in einer einzelnen Domäne** | Stellen Sie die VM wie jede andere VM wieder her. Beachten Sie Folgendes:<br/><br/> Aus Active Directory-Sicht entspricht die Azure-VM beliebigen anderen VMs.<br/><br/> Der Verzeichnisdienste-Wiederherstellungsmodus (Directory Services Restore Mode, DSRM) ist ebenfalls verfügbar, sodass alle Active Directory-Wiederherstellungsszenarien realisierbar sind. [Weitere Informationen](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v) zu den Überlegungen zum Sichern und Wiederherstellen virtualisierter Domänencontroller.
**Wiederherstellen mehrerer Domänencontroller-VMs in einer einzelnen Domäne** | Wenn andere Domänencontroller in derselben Domäne über das Netzwerk erreicht werden können, kann der Domänencontroller wie ein beliebiger virtueller Computer wiederhergestellt werden. Wenn es sich um den letzten verbleibenden Domänencontroller in der Domäne handelt oder eine Wiederherstellung in einem isolierten Netzwerk erfolgt, muss ein Vorgang zur [Wiederherstellung der Gesamtstruktur](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery) ausgeführt werden.
**Wiederherstellen mehrerer Domänen in einer Gesamtstruktur** | Wir empfehlen eine [Wiederherstellung der Gesamtstruktur](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Bare-Metal-Wiederherstellung** | Der Hauptunterschied zwischen Azure-VMs und lokalen Hypervisoren ist, dass keine VM-Konsole in Azure verfügbar ist. Eine Konsole ist für bestimmte Szenarien erforderlich, z.B. zur Wiederherstellung mithilfe einer Sicherung vom Typ „Bare-Metal-Recovery“ (BMR). Die VM-Wiederherstellung aus dem Tresor ist jedoch ein vollständiger Ersatz für BMR.
**Wiederherstellen von VMs mit speziellen Netzwerkkonfigurationen** | Spezielle Netzwerkkonfigurationen beinhalten VMs mit internem oder externem Lastausgleich, mit mehreren NICS oder mehreren reservierten IP-Adressen. Diese VMs können Sie mit der [Option zum Wiederherstellen von Datenträgern](#restore-disks) wiederherstellen. Diese Option kopiert die VHDs in das angegebene Speicherkonto, und Sie können dann gemäß Ihrer Konfiguration eine VM mit einem [internen](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/) oder [externen](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/) Lastenausgleich, [mehreren NICS](../virtual-machines/windows/multiple-nics.md) oder [mehreren reservierten IP-Adressen](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md) erstellen.
**Netzwerksicherheitsgruppe (NSG) für Netzwerkadapter oder Subnetz** | Die Azure-VM-Sicherung unterstützt die Sicherung und Wiederherstellung von NSG-Informationen auf Vnet-, Subnetz- und NIC-Ebene.
**Zonenkonfigurierte angeheftete VMs** | Azure Backup unterstützt die Sicherung und Wiederherstellung von zonenkonfigurierten angehefteten VMs. [Weitere Informationen](https://azure.microsoft.com/global-infrastructure/availability-zones/)

## <a name="track-the-restore-operation"></a>Nachverfolgen des Wiederherstellungsvorgangs
Nachdem Sie den Wiederherstellungsvorgang ausgelöst haben, erstellt der Sicherungsdienst einen Auftrag zum Nachverfolgen. Azure Backup zeigt Benachrichtigungen zum Auftrag im Portal an. Wenn keine Benachrichtigungen angezeigt werden, klicken Sie auf das Symbol **Benachrichtigungen**.

![Wiederherstellung ausgelöst](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Die Wiederherstellung können Sie wie folgt nachverfolgen:

1. Um die Vorgänge für den Auftrag anzuzeigen, klicken Sie auf den Hyperlink für Benachrichtigungen. Alternativ können Sie im Tresor auf **Sicherungsaufträge** und dann auf die entsprechende VM klicken.

    ![Liste der virtuellen Computer in einem Tresor](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. Um den Fortschritt der Wiederherstellung zu überwachen, klicken Sie auf einen beliebigen Wiederherstellungsauftrag mit dem Status **Wird ausgeführt**. Damit wird die Statusanzeige mit Informationen zum Fortschritt der Wiederherstellung anzeigt:

    - **Geschätzte Dauer der Wiederherstellung**: Gibt zunächst an, wieviel Zeit benötigt wird, um den Wiederherstellungsvorgang abzuschließen. Während der Vorgang weiter ausgeführt wird, verringert sich die benötigte Zeit und erreicht schließlich 0, sobald der Wiederherstellungsvorgang abgeschlossen ist.
    - **Prozentsatz der Wiederherstellung**. Zeigt an, wie viel Prozent des Wiederherstellungsvorgangs abgeschlossen sind.
    - **Anzahl der übertragenen Bytes**: Wenn Sie die Wiederherstellung durch Erstellen einer neuen VM durchführen, werden die übertragenen Bytes im Verhältnis zur Gesamtzahl der zu übertragenden Bytes angezeigt.

## <a name="post-restore-steps"></a>Schritte nach der Wiederherstellung

Nach dem Wiederherstellen einer VM gibt es eine Reihe von Dingen zu beachten:

- Erweiterungen, die während der Konfiguration der Sicherung installiert, aber nicht aktiviert sind. Falls ein Problem auftritt, installieren Sie die Erweiterungen neu.
- Wenn die gesicherte VM eine statische IP-Adresse hatte, hat die wiederhergestellte VM eine dynamische IP-Adresse, um Konflikte zu vermeiden. Sie können [eine statische IP-Adresse zur wiederhergestellten VM hinzufügen](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm).
- Für eine wiederhergestellte VM gibt es keine Verfügbarkeitsgruppe. Wenn Sie die Option „Datenträger wiederherstellen“ verwenden, können Sie [eine Verfügbarkeitsgruppe](../virtual-machines/windows/tutorial-availability-sets.md) angeben, wenn Sie eine VM vom Datenträger aus mit der angegebenen Vorlage oder PowerShell erstellen.
- Bei Verwendung einer Cloud-Init-basierten Linux-Verteilung wie etwa Ubuntu wird das Kennwort aus Sicherheitsgründen nach der Wiederherstellung blockiert. Verwenden Sie zum [Zurücksetzen des Kennworts](../virtual-machines/linux/reset-password.md) die VMAccess-Erweiterung auf dem wiederhergestellten virtuellen Computer. Wir empfehlen, bei diesen Verteilungen SSH-Schlüssel zu verwenden, sodass Sie das Kennwort nach der Wiederherstellung nicht zurücksetzen müssen.


## <a name="backing-up-restored-vms"></a>Sichern von wiederhergestellten VMs

- Wenn Sie eine VM in derselben Ressourcengruppe mit dem Namen der ursprünglich gesicherten VM wiederhergestellt haben, wird die VM nach der Wiederherstellung weiterhin gesichert.
- Wenn Sie die VM in einer anderen Ressourcengruppe wiederhergestellt haben oder einen anderen Namen für die wiederhergestellte VM angegeben haben, müssen Sie die Sicherung für die wiederhergestellte VM einrichten.

## <a name="next-steps"></a>Nächste Schritte

- Wenn während des Wiederherstellungsprozesses Probleme auftreten, lesen Sie die [Informationen](backup-azure-vms-troubleshoot.md#restore) zu häufigen Problemen und Fehlern.
- Lesen Sie nach der Wiederherstellung der VM mehr über [die Verwaltung virtueller Computer](backup-azure-manage-vms.md).

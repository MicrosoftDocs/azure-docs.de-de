---
title: Sichern und Wiederherstellen von Active Directory
description: Hier erfahren Sie, wie Sie Active Directory-Domänencontroller sichern und wiederherstellen können.
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: afbc538b84c20f9baaa664c7d47140365c747cb0
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754538"
---
# <a name="back-up-and-restore-active-directory-domain-controllers"></a>Sichern und Wiederherstellen von Active Directory-Domänencontrollern

Das Sichern von Active Directory und die Sicherstellung erfolgreicher Wiederherstellungen bei Beschädigung, Gefährdung oder einem Notfall ist ein wichtiger Bestandteil der Active Directory-Wartung.

In diesem Artikel werden die richtigen Verfahren zum Sichern und Wiederherstellen von Active Directory-Domänencontrollern mit Azure Backup beschrieben – unabhängig davon, ob es sich dabei um virtuelle Azure-Computer oder lokale Server handelt. Es wird ein Szenario erläutert, in dem Sie einen ganzen Domänencontroller auf seinen Zustand zum Zeitpunkt der Sicherung wiederherstellen müssen. Wenn Sie erfahren möchten, welches Wiederherstellungsszenario für Sie geeignet ist, lesen Sie [diesen Artikel](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-determine-how-to-recover).  

>[!NOTE]
> In diesem Artikel wird nicht das Wiederherstellen von Elementen aus [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) erläutert. Informationen zum Wiederherstellen von Azure Active Directory-Benutzern finden Sie in [diesem Artikel](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-restore).

## <a name="best-practices"></a>Bewährte Methoden

- Sorgen Sie dafür, dass mindestens ein Domänencontroller gesichert wird. Wenn Sie mehrere Domänencontroller sichern, sorgen Sie dafür, dass alle mit den [FSMO-Rollen (Flexible Single Master Operation)](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/planning-operations-master-role-placement) gesichert werden.

- Sichern Sie Active Directory häufig. Die Sicherung sollte nie mehr als die Tombstone-Lebensdauer (standardmäßig 60 Tage) sein, weil Objekte, die älter als die Tombstone-Lebensdauer sind, als „veraltet“ markiert und nicht mehr als gültig betrachtet werden.

- Sorgen Sie für einen klaren Plan zur Notfallwiederherstellung mit Anleitungen dazu, wie Ihre Domänencontroller wiederhergestellt werden. Informationen zum Vorbereiten der Wiederherstellung einer Active Directory-Gesamtstruktur finden Sie im [Active Directory Forest Recovery Guide](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-guide) (Handbuch zur Wiederherstellung der Active Directory-Gesamtstruktur).

- Wenn Sie einen Domänencontroller wiederherstellen müssen und die Domäne noch einen funktionsfähigen Domänencontroller enthält, können Sie einen neuen Server erstellen, statt ihn aus einer Sicherung wiederherzustellen. Fügen Sie dem neuen Server die Serverrolle **Active Directory Domain Services** hinzu, um ihn zu einem Domänencontroller in der vorhandenen Domäne zu machen. Anschließend werden die Active Directory-Daten auf den neuen Server repliziert. Wenn Sie den vorherigen Domänencontroller aus Active Directory entfernen möchten, führen Sie die [in diesem Artikel](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/ad-ds-metadata-cleanup) beschriebenen Schritte zur Durchführung einer Metadatenbereinigung aus.

>[!NOTE]
>Azure Backup enthält keine Wiederherstellung auf Elementebene für Active Directory. Wenn Sie gelöschte Objekte wiederherstellen möchten und auf einen Domänencontroller zugreifen können, verwenden Sie den [Active Directory-Papierkorb](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#ad_recycle_bin_mgmt). Wenn diese Methode nicht verfügbar ist, können Sie Ihre Domänencontrollersicherung verwenden, um die gelöschten Objekte mit dem Tool **Ntdsutil.exe** wiederherzustellen, wie [hier](https://support.microsoft.com/help/840001/how-to-restore-deleted-user-accounts-and-their-group-memberships-in-ac) erläutert wird.
>
>Informationen zum Durchführen einer autoritativen Wiederherstellung von SYSVOL finden Sie in [diesem Artikel](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-authoritative-recovery-sysvol).

## <a name="backing-up-azure-vm-domain-controllers"></a>Sichern von Azure-VM-Domänencontrollern

Wenn der Domänencontroller eine Azure-VM ist, können Sie den Server mithilfe der [Azure-VM-Sicherung](backup-azure-vms-introduction.md) sichern.

Informieren Sie sich über [operative Überlegungen für virtualisierte Domänencontroller](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v#operational-considerations-for-virtualized-domain-controllers), um für erfolgreiche Sicherungen (und zukünftige Wiederherstellungen) Ihrer Azure-VM-Domänencontroller zu sorgen.

## <a name="backing-up-on-premises-domain-controllers"></a>Sichern von lokalen Domänencontrollern

Zum Sichern eines lokalen Domänencontrollers müssen Sie die Systemstatusdaten des Servers sichern.

- Wenn Sie MARS verwenden, folgen Sie [diesen Anleitungen](backup-azure-system-state.md).
- Wenn Sie MABS (Azure Backup Server) verwenden, folgen Sie [diesen Anleitungen](backup-mabs-system-state-and-bmr.md).

>[!NOTE]
> Die Wiederherstellung von lokalen Domänencontrollern (entweder aus dem Systemstatus oder aus VMs) in der Azure-Cloud wird nicht unterstützt. Wenn Sie die Option eines Failovers aus einer lokalen Active Directory-Umgebung zu Azure verwenden möchten, erwägen Sie die Verwendung von [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory).

## <a name="restoring-active-directory"></a>Wiederherstellen von Active Directory

Active Directory-Daten können in einem von zwei Modi wiederhergestellt werden: **autoritativ** oder **nicht autoritativ**. Bei einer autoritativen Wiederherstellung überschreiben die wiederhergestellten Active Directory-Daten die Daten auf den anderen Domänencontrollern in der Gesamtstruktur.

Weil aber bei diesem Szenario ein Domänencontroller in einer vorhandenen Domäne neu erstellt wird, sollte eine **nicht autoritative** Wiederherstellung durchgeführt werden.

Während der Wiederherstellung wird der Server im Verzeichnisdienste-Wiederherstellungsmodus (Directory Services Restore Mode, DSRM) gestartet. Sie müssen das Administratorkennwort für diesen Modus angeben.

>[!NOTE]
>Wenn Sie das DSRM-Kennwort vergessen haben, können Sie es anhand [dieser Anleitungen](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc754363(v=ws.11)) zurücksetzen.

### <a name="restoring-azure-vm-domain-controllers"></a>Wiederherstellen von Azure-VM-Domänencontrollern

Informationen zum Wiederherstellen eines Azure-VM-Domänencontrollers finden Sie unter [Wiederherstellen von virtuellen Computern mit Domänencontroller](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).

Wenn Sie eine einzelne Domänencontroller-VM oder mehrere Domänencontroller-VMs in einer einzelnen Domäne wiederherstellen, stellen Sie sie wie jede beliebige andere VM wieder her. Der Verzeichnisdienste-Wiederherstellungsmodus (Directory Services Restore Mode, DSRM) ist ebenfalls verfügbar, sodass alle Active Directory-Wiederherstellungsszenarien realisierbar sind.

Wenn Sie eine einzelne Domänencontroller-VM in einer Konfiguration mit mehreren Domänen wiederherstellen müssen, stellen Sie die Datenträger wieder her, und erstellen Sie dann eine VM [mithilfe von PowerShell](backup-azure-vms-automation.md#restore-the-disks).

Wenn Sie den letzten verbliebenen Domänencontroller in der Domäne oder mehrere Domänen in einer einzigen Gesamtstruktur wiederherstellen, empfehlen wir eine [Wiederherstellung der Gesamtstruktur](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).

>[!NOTE]
> Virtualisierte Domänencontroller ab Windows 2012 verwenden [virtualisierungsbasierte Sicherheitsmechanismen](https://docs.microsoft.com/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100#virtualization-based-safeguards). Bei diesen Sicherheitsmechanismen erkennt Active Directory, ob die wiederhergestellte VM ein Domänencontroller ist, und führt die erforderlichen Schritte zum Wiederherstellen der Active Directory-Daten aus.

### <a name="restoring-on-premises-domain-controllers"></a>Wiederherstellen von lokalen Domänencontrollern

Wenn Sie einen lokalen Domänencontroller wiederherstellen möchten, folgen Sie den Anleitungen zum Wiederherstellen des Systemstatus auf einem Windows-Server anhand des Leitfadens [Besondere Überlegungen bei der Wiederherstellung des Systemstatus auf einem Domänencontroller](backup-azure-restore-system-state.md#special-considerations-for-system-state-recovery-on-a-domain-controller).

## <a name="next-steps"></a>Nächste Schritte

- [Supportmatrix für Azure Backup](backup-support-matrix.md)

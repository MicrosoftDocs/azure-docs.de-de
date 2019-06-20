---
title: Übersicht über Recovery Services-Tresore
description: Übersicht über Recovery Services-und Azure Backup-Tresore sowie Vergleich dieser Tresore
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 8/10/2018
ms.author: raynew
ms.openlocfilehash: 924b36701ecf21f6bd84938aeefbf25e47fcbaa7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60699249"
---
# <a name="recovery-services-vaults-overview"></a>Übersicht über Recovery Services-Tresore

In diesem Artikel werden die Funktionen eines Recovery Services-Tresors beschrieben. Ein Recovery Services-Tresor ist eine Speicherentität in Azure, die Daten enthält. Bei den Daten handelt es sich in der Regel um Kopien von Daten oder Konfigurationsinformationen für virtuelle Computer (VMs), Workloads, Server oder Arbeitsstationen. Mit Recovery Services-Tresoren können Sie Sicherungsdaten für verschiedene Azure-Dienste speichern, z. B. IaaS-VMs (Linux oder Windows) und Azure SQL-Datenbanken. Recovery Services-Tresore unterstützen System Center DPM, Windows Server, Azure Backup Server etc. Recovery Services-Tresore vereinfachen die Organisation Ihrer Sicherungsdaten und minimieren gleichzeitig den Verwaltungsaufwand.

Innerhalb eines Azure-Abonnements können Sie bis zu 500 Recovery Services-Tresore pro Abonnement erstellen.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Vergleich zwischen Recovery Services- und Sicherungstresoren

Wenn Sie weiterhin Sicherungstresore besitzen, werden sie automatisch auf Recovery Services-Tresore aktualisiert. Bis November 2017 werden alle Sicherungstresore auf Recovery Services-Tresore aktualisiert sein.

Recovery Services-Tresore basieren auf dem Azure Resource Manager-Modell von Azure, wohingegen Sicherungstresore auf dem Azure Service Manager-Modell beruhen. Beim Upgrade eines Sicherungstresors auf einen Recovery Services-Tresor bleiben die Sicherungsdaten während und nach dem Upgradevorgang intakt. Recovery Services-Tresore bieten Funktionen, die nicht für Sicherungstresore verfügbar sind. Hierzu zählen z.B. Folgende:

- **Erweiterte Funktionen zum Schutz von Sicherungsdaten**: Durch Recovery Services-Tresore bietet Azure Backup Sicherheitsfunktionen zum Schutz von Cloudsicherungen. Mit diesen Sicherheitsfunktionen wird sichergestellt, dass Sie Ihre Sicherungen schützen und Daten sicher wiederherstellen können, selbst wenn Produktions- und Sicherungsserver kompromittiert sind. [Weitere Informationen](backup-azure-security-feature.md)

- **Zentrale Überwachung Ihrer Hybrid-IT-Umgebung**: Mit Recovery Services-Tresoren können Sie nicht nur Ihre [Azure-IaaS-VMs](backup-azure-manage-vms.md), sondern auch Ihre [lokalen Ressourcen](backup-azure-manage-windows-server.md#manage-backup-items) über ein zentrales Portal überwachen. [Weitere Informationen](https://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC)** : Die RBAC ermöglicht eine präzise Verwaltung der Zugriffssteuerung in Azure. [Azure bietet verschiedene integrierte Rollen](../role-based-access-control/built-in-roles.md), während Azure Backup über drei [integrierte Rollen zum Verwalten von Wiederherstellungspunkten](backup-rbac-rs-vault.md) verfügt. Recovery Services-Tresore sind mit der RBAC kompatibel, die den Zugriff auf Sicherungen und Wiederherstellungen auf den definierten Satz von Benutzerrollen beschränkt. [Weitere Informationen](backup-rbac-rs-vault.md)

- **Schutz aller Konfigurationen von Azure Virtual Machines**: Recovery Services-Tresore schützen Resource Manager-basierte VMs, einschließlich Premium-Datenträger, verwalteter Datenträger und verschlüsselter VMs. Durch die Durchführung eines Upgrades eines Sicherungstresors auf einen Recovery Services-Tresor erhalten Sie die Möglichkeit, ein Upgrade für Ihre Service Manager-basierten VMs auf Resource Manager-basierte VMs durchzuführen. Während des Upgrades des Tresors können Sie die Wiederherstellungspunkte für Ihre Service Manager-basierten VMs beibehalten und den Schutz für die aktualisierten (Resource Manager-fähigen) VMs konfigurieren. [Weitere Informationen](https://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Sofortige Wiederherstellung für IaaS-VMs**: Durch Recovery Services-Tresore können Sie Dateien und Ordner von einer IaaS-VM wiederherstellen, ohne die gesamte VM wiederherstellen zu müssen. So werden die Wiederherstellungszeiten verkürzt. Die sofortige Wiederherstellung für IaaS-VMs ist für Windows- und Linux-VMs verfügbar. [Weitere Informationen](https://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>Verwalten von Recovery Services-Tresoren im Portal
Die Erstellung und Verwaltung von Recovery Services-Tresoren im Azure-Portal ist einfach, da der Sicherungsdienst in andere Azure-Dienste integriert ist. Eine derartige Integration bedeutet, dass Sie einen Recovery Services-Tresor *im Kontext des Zieldiensts* erstellen oder verwalten können. Um beispielsweise die Wiederherstellungspunkte für eine VM anzuzeigen, wählen Sie Ihre VM aus, und klicken Sie im Menü „Vorgänge“ auf **Sicherung**.

![Details zum Recovery Services-Tresor über die VM](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context-vm.png)

Wenn für den virtuellen Computer keine Sicherung konfiguriert wurde, werden Sie zum Konfigurieren der Sicherung aufgefordert. Wurde eine Sicherung konfiguriert, werden die Sicherungsinformationen über den virtuellen Computer angezeigt, einschließlich einer Liste von Wiederherstellungspunkten.  

![Details zum Recovery Services-Tresor über die VM](./media/backup-azure-recovery-services-vault-overview/vm-recovery-point-list.png)

Im vorherigen Beispiel ist **ContosoVM** der Name des virtuellen Computers. **ContosoVM-demovault** ist der Name des Recovery Services-Tresors. Sie müssen sich den Namen des Recovery Services-Tresors, in dem die Wiederherstellungspunkte gespeichert werden, nicht merken, sondern können über den virtuellen Computer auf diese Informationen zugreifen.  

Wenn ein Recovery Services-Tresor mehrere Server schützt, kann es sinnvoller sein, den Recovery Services-Tresor zu überprüfen. Sie können nach allen Recovery Services-Tresoren im Abonnement suchen und einen aus der Liste auswählen.

Die folgenden Abschnitte enthalten Links zu Artikeln, in denen erläutert wird, wie Sie einen Recovery Services-Tresor in jedem Aktivitätstyp verwenden.

> [!NOTE]
> Ein Recovery Services-Tresor kann nach dem Löschen innerhalb von 24 Stunden nicht mit dem gleichen Namen erstellt werden. Verwenden Sie einen anderen Ressourcennamen, wählen Sie eine andere Ressourcengruppe aus, oder versuchen Sie es nach 24 Stunden erneut.

### <a name="back-up-data"></a>Sichern von Daten
- [Sichern einer Azure-VM](backup-azure-vms-first-look-arm.md)
- [Sichern von Daten von Windows Server oder einer Windows-Arbeitsstation](backup-try-azure-backup-in-10-mins.md)
- [Sichern von DPM-Workloads in Azure](backup-azure-dpm-introduction.md)
- [Vorbereiten der Sicherung von Workloads per Azure Backup Server](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>Verwalten von Wiederherstellungspunkten
- [Verwalten von Azure-VM-Sicherungen](backup-azure-manage-vms.md)
- [Verwalten von Dateien und Ordnern](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>Wiederherstellen von Daten aus dem Tresor
- [Wiederherstellen einzelner Dateien aus einer Azure-VM](backup-azure-restore-files-from-vm.md)
- [Wiederherstellen einer Azure-VM](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>Sichern des Tresors
- [Sichern von Cloudsicherungsdaten in Recovery Services-Tresoren](backup-azure-security-feature.md)



## <a name="next-steps"></a>Nächste Schritte
Verwenden Sie die folgenden Artikel für:</br>
[Sichern eines virtuellen IaaS-Computers](backup-azure-arm-vms-prepare.md)</br>
[Sichern eines Azure Backup Server-Computers](backup-azure-microsoft-azure-backup.md)</br>
[Sichern eines Windows Server-Computers](backup-configure-vault.md)

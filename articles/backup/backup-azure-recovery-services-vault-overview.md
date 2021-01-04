---
title: Übersicht über Recovery Services-Tresore
description: Übersicht über Recovery Services-Tresore
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: ab6b27bdc7ac9b01385ed43830d0af5900210d43
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017320"
---
# <a name="recovery-services-vaults-overview"></a>Übersicht über Recovery Services-Tresore

In diesem Artikel werden die Funktionen eines Recovery Services-Tresors beschrieben. Ein Recovery Services-Tresor ist eine Speicherentität in Azure, die Daten enthält. Bei den Daten handelt es sich in der Regel um Kopien von Daten oder Konfigurationsinformationen für virtuelle Computer (VMs), Workloads, Server oder Arbeitsstationen. Mit Recovery Services-Tresoren können Sie Sicherungsdaten für verschiedene Azure-Dienste speichern, z. B. IaaS-VMs (Linux oder Windows) und Azure SQL-Datenbanken. Recovery Services-Tresore unterstützen System Center DPM, Windows Server, Azure Backup Server etc. Recovery Services-Tresore vereinfachen die Organisation Ihrer Sicherungsdaten und minimieren gleichzeitig den Verwaltungsaufwand. Recovery Services-Tresore basieren auf dem Azure Resource Manager-Modell von Azure. Dieses bietet die folgenden Features:

- **Erweiterte Funktionen zum Schutz von Sicherungsdaten**: Durch Recovery Services-Tresore bietet Azure Backup Sicherheitsfunktionen zum Schutz von Cloudsicherungen. Mit diesen Sicherheitsfunktionen wird sichergestellt, dass Sie Ihre Sicherungen schützen und Daten sicher wiederherstellen können, selbst wenn Produktions- und Sicherungsserver kompromittiert sind. [Weitere Informationen](backup-azure-security-feature.md)

- **Zentrale Überwachung Ihrer Hybrid-IT-Umgebung**: Mit Recovery Services-Tresoren können Sie nicht nur Ihre [Azure-IaaS-VMs](backup-azure-manage-vms.md), sondern auch Ihre [lokalen Ressourcen](backup-azure-manage-windows-server.md#manage-backup-items) über ein zentrales Portal überwachen. [Weitere Informationen](backup-azure-monitoring-built-in-monitor.md)

- **Rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)** : Azure RBAC ermöglicht eine differenzierte Verwaltung der Zugriffssteuerung in Azure. [Azure bietet verschiedene integrierte Rollen](../role-based-access-control/built-in-roles.md), während Azure Backup über drei [integrierte Rollen zum Verwalten von Wiederherstellungspunkten](backup-rbac-rs-vault.md) verfügt. Recovery Services-Tresore sind mit Azure RBAC kompatibel, die den Zugriff auf Sicherungen und Wiederherstellungen auf den definierten Satz von Benutzerrollen beschränkt. [Weitere Informationen](backup-rbac-rs-vault.md)

- **Vorläufiges Löschen:**  Beim vorläufigen Löschen werden die Sicherungsdaten 14 Tage länger aufbewahrt, damit das jeweilige Sicherungselement auch dann ohne Datenverluste wiederhergestellt werden kann, wenn ein böswilliger Akteur eine Sicherung löscht oder die Sicherungsdaten versehentlich gelöscht werden. Für die zusätzlichen 14 Tage der Aufbewahrung von Sicherungsdaten mit dem Status „Vorläufiges Löschen“ fallen für Sie keine Kosten an. [Weitere Informationen](backup-azure-security-feature-cloud.md)

- **Regionsübergreifende Wiederherstellung:**  Die regionsübergreifende Wiederherstellung (Cross-Region Restore, CRR) ermöglicht es Ihnen, virtuelle Azure-Computer in einer sekundären Region wiederherzustellen, bei der es sich um eine gekoppelte Azure-Region handelt. Wenn Azure einen Notfall in der primären Region feststellt, stehen die in der sekundären Region replizierten Daten für eine Wiederherstellung in der sekundären Region zur Verfügung, um auf einen Notfall mit echter Downtime in der primären Region für die Umgebung zu reagieren. [Weitere Informationen](backup-azure-arm-restore-vms.md#cross-region-restore)

## <a name="storage-settings-in-the-recovery-services-vault"></a>Speichereinstellungen im Recovery Services-Tresor

Bei einem Recovery Services-Tresor handelt es sich um eine Entität, in der alle im Laufe der Zeit erstellten Sicherungen und Wiederherstellungspunkte gespeichert werden. Der Recovery Services-Tresor enthält auch die Sicherungsrichtlinien, die den geschützten virtuellen Computern zugeordnet sind.

- Azure Backup übernimmt automatisch die Speicherung für den Tresor. Informieren Sie sich, wie [Speichereinstellungen geändert werden können](./backup-create-rs-vault.md#set-storage-redundancy).

- Weitere Informationen zur Speicherredundanz finden Sie in diesen Artikeln zur [geografischen](../storage/common/storage-redundancy.md#geo-zone-redundant-storage), [lokalen](../storage/common/storage-redundancy.md#locally-redundant-storage) und [zonalen](../storage/common/storage-redundancy.md#zone-redundant-storage) Redundanz.

## <a name="encryption-settings-in-the-recovery-services-vault"></a>Verschlüsselungseinstellungen im Recovery Services-Tresor

In diesem Abschnitt werden die verfügbaren Optionen zur Verschlüsselung Ihrer im Recovery Services-Tresor gespeicherten Sicherungsdaten behandelt.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Verschlüsselung von Sicherungsdaten mit von der Plattform verwalteten Schlüsseln

Standardmäßig werden alle Ihre Daten mit von der Plattform verwalteten Schlüsseln verschlüsselt. Sie müssen Ihrerseits keine besonderen Maßnahmen ergreifen, um diese Verschlüsselung zu aktivieren. Dies gilt für alle Workloads, die in Ihrem Recovery Services-Tresor gesichert werden.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Verschlüsselung von Sicherungsdaten mit von Kunden verwalteten Schlüsseln

Sie können Ihre Daten mit eigenen, von Ihnen selbst verwalteten Verschlüsselungsschlüsseln verschlüsseln. Mit Azure Backup können Sie die in Azure Key Vault gespeicherten RSA-Schlüssel zum Verschlüsseln Ihrer Sicherungen verwenden. Der Verschlüsselungsschlüssel, der zum Verschlüsseln von Sicherungen verwendet wird, kann sich von dem für die Quelle verwendeten Verschlüsselungsschlüssel unterscheiden. Die Daten werden mithilfe eines AES-256 basierten Datenverschlüsselungsschlüssels (DEK) geschützt, der wiederum mit Ihren Schlüsseln geschützt wird. Dadurch erhalten Sie vollständige Kontrolle über die Daten und Schlüssel. Um die Verschlüsselung zu ermöglichen, muss dem Recovery Services-Tresor der Zugriff auf den Verschlüsselungsschlüssel in Azure Key Vault gewährt werden. Bei Bedarf können Sie den Schlüssel deaktivieren oder den Zugriff widerrufen. Sie müssen jedoch die Verschlüsselung mit ihren Schlüsseln aktivieren, bevor Sie versuchen, Elemente im Tresor zu schützen.

Weitere Informationen zum Verschlüsseln Ihrer Sicherungsdaten [mit kundenseitig verwalteten Schlüsseln](encryption-at-rest-with-cmk.md).

## <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](../advisor/index.yml) ist ein personalisierter Cloudberater, der Ihnen hilft, Azure optimal zu nutzen. Er analysiert Ihre Azure-Nutzung und bietet zeitnahe Empfehlungen, um Sie beim Optimieren und Schützen Ihrer Bereitstellungen zu unterstützen. Die Empfehlungen werden in vier Kategorien bereitgestellt: Hochverfügbarkeit, Sicherheit, Leistung und Kosten.

Azure Advisor stellt stündlich [Empfehlungen](../advisor/advisor-high-availability-recommendations.md#protect-your-virtual-machine-data-from-accidental-deletion) für VMs bereit, die nicht gesichert werden, sodass Sie nie die Sicherung wichtiger VMs verpassen. Sie können die Empfehlungen auch steuern, indem Sie sie zurückstellen.  Sie können die Empfehlung auswählen und die Sicherung von VMs inline aktivieren, indem Sie den Tresor (in dem die Sicherungen gespeichert werden) und die Sicherungsrichtlinie (Sicherungszeitplan und Aufbewahrung von Sicherungskopien) angeben.

![Azure Advisor](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Unterstützung für Tresore](backup-support-matrix.md#vault-support)
- [Azure Backup – häufig gestellte Fragen](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie die folgenden Artikel für:

- [Sichern eines virtuellen IaaS-Computers](backup-azure-arm-vms-prepare.md)
- [Sichern eines Azure Backup Server-Computers](backup-azure-microsoft-azure-backup.md)
- [Sichern eines Windows Server-Computers](backup-windows-with-mars-agent.md)

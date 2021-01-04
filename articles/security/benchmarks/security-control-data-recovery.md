---
title: 'Azure-Sicherheitskontrolle: Datenwiederherstellung'
description: 'Azure-Sicherheitskontrolle: Datenwiederherstellung'
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 835e4f681d514bb6b92caa5ee076e3794ed59236
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698817"
---
# <a name="security-control-data-recovery"></a>Sicherheitskontrolle: Datenwiederherstellung

Sorgen Sie dafür, dass alle Systemdaten, Konfigurationen und Geheimnisse in regelmäßigen Abständen automatisch gesichert werden.

## <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 9.1 | 10.1 | Kunde |

Aktivieren Sie Azure Backup, und konfigurieren Sie die Sicherungsquelle (virtuelle Azure-Computer, SQL Server oder Dateifreigaben) sowie die gewünschte Häufigkeit und Beibehaltungsdauer.

- [Dokumentation zum Azure Backup-Dienst](../../backup/index.yml)

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller von Kunden verwalteten Schlüssel

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 9.2 | 10.2 | Kunde |

Aktivieren Sie Azure Backup und die virtuellen Zielcomputer sowie die gewünschte Häufigkeit und Beibehaltungsdauer. Sichern Sie die von Kunden verwalteten Schlüssel in Azure Key Vault.

- [Aktivieren von Azure Backup](../../backup/index.yml)

- [Sichern von Schlüsseltresorschlüsseln in Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich der von Kunden verwalteten Schlüssel

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 9.3 | 10.3 | Kunde |

Stellen Sie regelmäßig sicher, dass die Inhalte in Azure Backup wiederhergestellt werden können. Testen Sie die Wiederherstellung von gesicherten von Kunden verwalteten Schlüsseln.

- [Wiederherstellen von Dateien aus einer Sicherung von virtuellen Azure-Computern](../../backup/backup-azure-restore-files-from-vm.md)

- [Wiederherstellen von Schlüsseltresorschlüsseln in Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 9,4 | 10.4 | Kunde |

Für die lokale Sicherung erfolgt eine Verschlüsselung im Ruhezustand über die beim Sichern in Azure bereitgestellte Passphrase. Für virtuelle Azure-Computer werden die Daten mit der Speicherdienstverschlüsselung (Storage Service Encryption, SSE) im Ruhezustand verschlüsselt. Verwenden Sie rollenbasierte Zugriffssteuerung in Azure, um Sicherungen und kundenseitig verwaltete Schlüssel zu schützen.  

Aktivieren Sie vorläufiges Löschen und den Löschschutz in Key Vault, um Schlüssel vor versehentlichem oder böswilligem Löschen zu schützen.  Wenn Azure Storage zum Speichern von Sicherungen verwendet wird, aktivieren Sie vorläufiges Löschen, um Ihre Daten zu speichern und wiederherzustellen, wenn Blobs oder Blobmomentaufnahmen gelöscht werden. 

- [Grundlegendes zu Azure RBAC](../../role-based-access-control/overview.md)

- [Aktivieren des vorläufigen Löschens und des Löschschutzes in Key Vault](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Vorläufiges Löschen für Azure Storage-Blobs](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)


## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie in der nächsten Sicherheitskontrolle:  [Reaktion auf Vorfälle](security-control-incident-response.md)
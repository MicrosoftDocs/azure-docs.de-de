---
title: Unterstützung beim Verschieben von Azure SQL-Ressourcen zwischen Regionen mit Azure Resource Mover
description: Informieren Sie sich über die Unterstützung beim Verschieben von Azure SQL-Ressourcen zwischen Regionen mit Azure Resource Mover.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 22a7738c2d4d3cc02c03c233e0821f07b459dd94
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452089"
---
# <a name="support-for-moving-azure-sql-resources-between-azure-regions"></a>Unterstützung beim Verschieben von Azure SQL-Ressourcen zwischen Azure-Regionen

Dieser Artikel enthält eine Zusammenfassung der Unterstützung und Voraussetzungen im Zusammenhang mit dem Verschieben von Azure SQL-Ressourcen zwischen Azure-Regionen mit Azure Resource Mover.

## <a name="requirements"></a>Anforderungen

Die Anforderungen werden in der folgenden Tabelle zusammengefasst.

**Feature** | **Unterstützt/Nicht unterstützt.** | **Details**
--- | --- | ---
**Hyperscale in Azure SQL-Datenbank** | Nicht unterstützt | Mit Resource Mover können keine Datenbanken der Azure SQL Hyperscale-Dienstebene verschoben werden.
**Zonenredundanz** | Unterstützt |  Unterstützte Optionen für den Verschiebungsvorgang:<br/><br/> - Zwischen Regionen, die Zonenredundanz unterstützen<br/><br/> - Zwischen Regionen, die keine Zonenredundanz unterstützen<br/><br/> - Zwischen einer Region, die Zonenredundanz unterstützt, und einer Region, die keine Zonenredundanz unterstützt<br/><br/> - Zwischen einer Region, die keine Zonenredundanz unterstützt, und einer Region, die Zonenredundanz unterstützt 
**Datensynchronisierung** | Hub-/Synchronisierungsdatenbank: Nicht unterstützt<br/><br/> Synchronisierungsmitglied: Unterstützt. | Wird ein Synchronisierungsmitglied verschoben, müssen Sie die Datensynchronisierung mit der neuen Zieldatenbank einrichten.
**Vorhandene Georeplikation** | Unterstützt | Vorhandene Georeplikate werden der neuen primären Datenbank in der Zielregion zugeordnet.<br/><br/> Nach dem Verschieben muss das Seeding initialisiert werden. [Weitere Informationen](../azure-sql/database/active-geo-replication-configure-portal.md)
**Transparent Data Encryption (TDE) mit Bring Your Own Key (BYOK)** | Unterstützt | Weitere Informationen zum Verschieben von Schlüsseltresoren zwischen Regionen finden Sie [hier](../key-vault/general/move-region.md).
**TDE mit dienstseitig verwaltetem Schlüssel** | Unterstützt. |  Weitere Informationen zum Verschieben von Schlüsseltresoren zwischen Regionen finden Sie [hier](../key-vault/general/move-region.md).
**Regeln für die dynamische Datenmaskierung** | Unterstützt. | Regeln werden im Rahmen der Verschiebung automatisch in die Zielregion kopiert. [Weitere Informationen](../azure-sql/database/dynamic-data-masking-configure-portal.md)
**Erweiterte Datensicherheit** | Wird nicht unterstützt. | Problemumgehung: Führen Sie die Einrichtung auf der SQL Server-Ebene in der Zielregion durch. [Weitere Informationen](../azure-sql/database/azure-defender-for-sql.md)
**Firewallregeln** | Wird nicht unterstützt. | Problemumgehung: Richten Sie Firewallregeln für SQL Server in der Zielregion ein. Firewallregeln auf der Datenbankebene werden vom Quellserver auf den Zielserver kopiert. [Weitere Informationen](../azure-sql/database/firewall-create-server-level-portal-quickstart.md)
**Überwachungsrichtlinien** | Wird nicht unterstützt. | Richtlinien werden nach dem Verschieben auf die Standardeinstellung zurückgesetzt. Informationen zum Zurücksetzen finden Sie [hier](../azure-sql/database/auditing-overview.md).
**die Aufbewahrung der Sicherung** | Unterstützt. | Sicherungsaufbewahrungsrichtlinien für die Quelldatenbank werden in die Zieldatenbank übertragen. Informationen zum Ändern von Einstellungen nach dem Verschieben finden Sie [hier](../azure-sql/database/long-term-backup-retention-configure.md).
**Automatische Optimierung** | Wird nicht unterstützt. | Problemumgehung: Legen Sie nach dem Verschieben Einstellungen für die automatische Optimierung fest. [Weitere Informationen](../azure-sql/database/automatic-tuning-enable.md)
**Datenbankwarnungen** | Wird nicht unterstützt. | Problemumgehung: Richten Sie nach dem Verschieben Warnungen ein. [Weitere Informationen](../azure-sql/database/alerts-insights-configure-portal.md)
**Azure SQL Server Stretch Database** | Nicht unterstützt | SQL Server Stretch Database-Instanzen können nicht mit Resource Mover verschoben werden.
**Azure Synapse Analytics** | Nicht unterstützt | Azure Synapse Analytics kann nicht mit Resource Mover verschoben werden.
## <a name="next-steps"></a>Nächste Schritte

Versuchen Sie, [Azure SQL-Ressourcen](tutorial-move-region-sql.md) mit Resource Mover in eine andere Region zu verschieben.
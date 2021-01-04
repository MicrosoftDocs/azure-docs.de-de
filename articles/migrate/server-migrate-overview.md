---
title: Auswählen einer VMware-Migrationsoption mit der Azure Migrate-Servermigration
description: Bietet eine Übersicht über die Optionen zum Migrieren von VMware-VMs zu Azure mit der Azure Migrate-Servermigration
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 5e4aaea5b565e126f633b04215bbc3a24faf2cde
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753602"
---
# <a name="select-a-vmware-migration-option"></a>Auswählen einer VMware-Migrationsoption

Sie können VMware-VMs zu Azure migrieren, indem Sie das Tool für die Azure Migrate-Servermigration verwenden. Dieses Tool verfügt über Optionen für die Migration von VMware-VMs:

- Durchführen einer Migration per Replikation ohne Agent Migrieren von VMs, ohne dass darauf Software installiert werden muss
- Durchführen einer Migration mit einem Agent für die Replikation Installieren eines Agents für die Replikation auf der VM


## <a name="compare-migration-methods"></a>Vergleichen von Migrationsmethoden

Die folgende Tabelle hilft Ihnen bei der Wahl der geeigneten Methode. Sie können sich auch die Anforderungen der vollständigen Unterstützung für die Migration [ohne Agent](migrate-support-matrix-vmware-migration.md#agentless-migration) und die [Agent-basierte](migrate-support-matrix-vmware-migration.md#agent-based-migration) Migration ansehen.

**Einstellung** | **Ohne Agent** | **Agent-basiert**
--- | --- | ---
**Azure-Berechtigungen** | Sie benötigen Berechtigungen zum Erstellen eines Azure Migrate-Projekts sowie zum Registrieren von Azure AD-Apps, die beim Bereitstellen der Azure Migrate-Appliance erstellt werden. | Sie benötigen Berechtigungen vom Typ „Mitwirkender“ für das Azure-Abonnement. 
**Replikation** | Es können maximal 300 VMs gleichzeitig von einem vCenter-Server repliziert werden.<br/> Wenn mehr als 50 VMs migriert werden sollen, erstellen Sie mehrere Batches von VMs.<br/> Wenn eine größere Anzahl als diese gleichzeitig migriert wird, kommt es zu einer Beeinträchtigung der Leistung.<br/><br/> Im Portal können für die Replikation bis zu zehn virtuelle Computer gleichzeitig ausgewählt werden. Wenn Sie mehr Computer replizieren möchten, fügen Sie jeweils Batches mit zehn Stück hinzu.| Die Replikationskapazität erhöht sich durch Skalieren der Replikationsappliance.
**Bereitstellung einer Appliance** | Die [Azure Migrate-Appliance](migrate-appliance.md) wird lokal bereitgestellt. | Die [Azure Migrate-Replikationsappliance](migrate-replication-appliance.md) wird lokal bereitgestellt.
**Site Recovery-Kompatibilität** | Kompatibel | Wenn Sie die Replikation für einen Computer mit Site Recovery eingerichtet haben, können Sie keine Replikation mit der Azure Migrate-Servermigration durchführen.
**Zieldatenträger** | Verwaltete Datenträger | Verwaltete Datenträger
**Einschränkungen für Datenträger** | Betriebssystemdatenträger: 2 TB<br/><br/> Datenträger für Daten: 32 TB<br/><br/> Maximale Datenträger: 60 | Betriebssystemdatenträger: 2 TB<br/><br/> Datenträger für Daten: 8 TB<br/><br/> Maximale Datenträger: 63
**Pass-Through-Datenträger** | Nicht unterstützt | Unterstützt
**UEFI-Start** | Unterstützt. | Unterstützt.

## <a name="compare-deployment-steps"></a>Vergleichen der Bereitstellungsschritte

Nachdem Sie sich über die Einschränkungen informiert haben, hilft Ihnen die Kenntnis der Schritte zum Bereitstellen der einzelnen Lösungen möglicherweise bei der Auswahl der Option.

**Aufgabe** | **Details** |**Ohne Agent** | **Agent-basiert**
--- | --- | --- | ---
**Bereitstellen der Azure Migrate-Appliance** | Eine Lightweight-Appliance, die auf einer VMware-VM ausgeführt wird.<br/><br/> Die Appliance dient zum Ermitteln und Bewerten von Computern und zum Migrieren von Computern mithilfe der Migration ohne Agent. | Erforderlich.<br/><br/> Wenn Sie die Appliance bereits für die Bewertung eingerichtet haben, können Sie dieselbe Appliance auch für die Migration ohne Agent verwenden. | Nicht erforderlich.<br/><br/> Wenn Sie eine Appliance für die Bewertung eingerichtet haben, können Sie sie nach Abschluss der Bewertung an Ort und Stelle belassen oder entfernen.
**Verwenden des Serverbewertungstools** | Bewerten Sie Computer mit dem Tool „Azure Migrate: Serverbewertung“. | Sie können die Computer bewerten, bevor Sie sie migrieren, aber Sie müssen nicht so vorgehen. | Die Bewertung ist optional. | Die Bewertung ist optional.
**Verwenden des Tools für die Servermigration** | Fügen Sie das Tool für die Azure Migrate-Servermigration im Azure Migrate-Projekt hinzu. | Erforderlich | Erforderlich
**Vorbereiten von VMware auf die Migration** | Konfigurieren Sie Einstellungen für VMware-Server und -VMs. | Erforderlich | Erforderlich
**Installieren des Mobilitätsdiensts auf VMs** | Der Mobilitätsdienst wird auf jeder VM ausgeführt, die Sie replizieren möchten. | Nicht erforderlich | Erforderlich
**Bereitstellen der Replikationsappliance** | Die [Replikationsappliance](migrate-replication-appliance.md) wird für die Agent-basierte Migration verwendet. Es wird eine Verbindung zwischen dem Mobilitätsdienst auf virtuellen Computern und der Servermigration hergestellt. | Nicht erforderlich | Erforderlich
**Replizieren von VMs** Aktivieren Sie die VM-Replikation. | Konfigurieren Sie Replikationseinstellungen, und wählen Sie VMs für die Replikation aus. | Erforderlich | Erforderlich
**Ausführen einer Testmigration** | Führen Sie eine Testmigration aus, um sicherzustellen, dass alles wie erwartet funktioniert. | Erforderlich | Erforderlich
**Durchführen einer vollständigen Migration** | Migrieren Sie die VMs. | Erforderlich | Erforderlich



## <a name="next-steps"></a>Nächste Schritte

[Migrieren von virtuellen VMware-Computern](tutorial-migrate-vmware.md) ohne Agents




---
title: Neuerungen in Microsoft Azure Backup Server
description: Microsoft Azure Backup Server bietet Ihnen erweiterte Sicherungsfunktionen für den Schutz von u.a. virtuellen Computern, Dateien, Ordnern und Workloads. Erfahren Sie, wie Sie Azure Backup Server V3 installieren oder ein Upgrade auf diese Version ausführen.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adigan
ms.openlocfilehash: 5718064994a80266c216ae6040746be29194adc9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60254704"
---
# <a name="whats-new-in-microsoft-azure-backup-server"></a>Neuerungen in Microsoft Azure Backup Server

Version 3 von Microsoft Azure Backup Server (MABS V3) ist das neueste Upgrade und enthält wichtige Fehlerbehebungen, Windows Server 2019-Unterstützung, SQL 2017-Unterstützung sowie weitere Features und Verbesserungen. Die Liste der behobenen Fehler und die Installationsanweisungen für MABS V3 finden Sie im KB-Artikel [4457852](https://support.microsoft.com/en-us/help/4457852/microsoft-azure-backup-server-v3).

Die folgenden Features sind in MABS V3 enthalten:

## <a name="volume-to-volume-migration"></a>Volume-zu-Volume-Migration
Mit Modern Backup Storage (MBS) in MABS V2 wurde ein workloadbewusster Speicher veröffentlicht. Bestimmte Workloads lassen sich so konfigurieren, dass sie auf einem bestimmten Speicher gesichert werden, basierend auf den Speichereigenschaften. Nach der Konfiguration ist es jedoch ggf. erforderlich, Sicherungen bestimmter Datenquellen auf einen anderen Speicher zu verschieben, um die Ressourcenverwendung zu optimieren. MABS V3 bietet Ihnen die Möglichkeit, Ihre Sicherungen zu migrieren und so zu konfigurieren, dass sie in [3 Schritten](https://blogs.technet.microsoft.com/dpm/2017/10/24/storage-migration-with-dpm-2016-mbs/) auf einem anderen Volume gespeichert werden.

## <a name="prevent-unexpected-data-loss"></a>Vermeidung unerwarteter Kosten
In Unternehmen wird MABS von einem Team von Administratoren verwaltet. Es gibt zwar Speicherrichtlinien, die für Sicherungen gelten, doch wenn MABS das falsche Volume als Sicherungsspeicher zugewiesen wird, kann dies zum Verlust wichtiger Daten führen. Mit MABS V3 können Sie das verhindern, indem Sie mit [diesen PowerShell-Cmdlets](https://docs.microsoft.com/system-center/dpm/add-storage#volume-exclusion) die Volumes konfigurieren, die nicht für den Speicher verfügbar sind.

## <a name="custom-size-allocation"></a>Benutzerdefinierte Größenzuteilung
Modern Backup Storage verbraucht sehr wenig Speicherplatz, nämlich nur nach Bedarf. Dazu berechnet MABS die Größe der zu sichernden Daten, wenn sie für den Schutz konfiguriert sind. Wenn viele Dateien und Ordner gemeinsam gesichert werden – wie bei einem Dateiserver – kann die Größenberechnung lange dauern. Mit MABS V3 können Sie MABS so konfigurieren, dass die Komponente Volumegröße als Standard akzeptiert, anstatt die Größe jeder Datei zu berechnen. Dadurch sparen Sie Zeit.

## <a name="optimized-cc-for-rct-vms"></a>Optimierte Konsistenzprüfungen für VMs mit RCT
MABS verwendet RCT, die systemeigene Änderungsnachverfolgung in Hyper-V, wodurch zeitaufwändige Konsistenzprüfungen etwa bei VM-Abstürzen entfallen. RCT bietet eine bessere Resilienz als die Änderungsnachverfolgung, die von Sicherungen bereitgestellt wird, die auf VSS-Momentaufnahmen basieren. MABS V3 optimiert den Netzwerk- und Speicherverbrauch außerdem, indem bei Konsistenzprüfungen nur die geänderten Daten übertragen werden.

## <a name="support-to-tls-12"></a>Unterstützung von TLS 1.2
TLS 1.2 ist die von Microsoft empfohlene Datenübertragungsmethode mit der stärksten Verschlüsselung. MABS unterstützt nun die TLS 1.2-Kommunikation zwischen MABS und den geschützten Servern, für zertifikatsbasierte Authentifizierung und für Cloudsicherungen.

## <a name="vmware-vm-protection-support"></a>Unterstützung des VMware-VM-Schutzes
Die VMware-VM-Sicherung wird nun für Produktionsbereitstellungen unterstützt. MABS V3 bietet Folgendes für den VMware-VM-Schutz:

-   Unterstützung für vCenter und ESXi 6.5, 5.5 und 6.0.
- Automatischer Schutz von VMware-VMs in der Cloud. Wenn neue VMware-VMs einem geschützten Ordner hinzugefügt werden, werden sie automatisch auf dem Datenträger und in der Cloud geschützt.
- Effizientere VMware-Wiederherstellungen an einem anderen Speicherort.

## <a name="sql-2017-support"></a>Unterstützung von SQL 2017
MABS V3 kann mit SQL 2017 als MABS-Datenbank installiert werden. Sie können SQL Server von SQL 2016 auf SQL 2017 aktualisieren oder das Upgrade neu installieren. Außerdem lassen sich mit MABS V3 SQL 2017-Workloads sowohl in gruppierten als auch in nicht gruppierten Umgebungen sichern.

## <a name="windows-server-2019-support"></a>Unterstützung für Windows Server 2019
MABS V3 kann auf Windows Server 2019 installiert werden. Um MABS V3 mit Windows Server 2019 zu verwenden, aktualisieren Sie Ihr Betriebssystem auf Windows Server 2016 vor oder nach der Installation von MABS V3 bzw. dem entsprechenden Upgrade.

MABS V3 ist eine Vollversion und kann direkt auf Windows Server 2016, Windows Server 2019 oder einem Upgrade von MABS V2 installiert werden. Bevor Sie Azure Backup Server V3 installieren oder ein Upgrade dafür ausführen, lesen Sie die Installationsvoraussetzungen.
[Hier](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#software-package) finden Sie weitere Informationen zur Installation und zum Upgrade von MABS.


> [!NOTE]
> 
> MABS hat die gleiche Codebasis wie System Center Data Protection Manager. MABS V3 entspricht Data Protection Manager, Version 1807.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie Ihren Server vorbereiten oder mit dem Schutz einer Workload beginnen:
- [Bekannte Probleme in MABS V3](backup-mabs-release-notes-v3.md)
- [Vorbereiten von Backup Server-Workloads](backup-azure-microsoft-azure-backup.md)
- [Sichern eines VMware-Servers mit Backup Server](backup-azure-backup-server-vmware.md)
- [Sichern von SQL Server mit Backup Server](backup-azure-sql-mabs.md)
- [Verwenden von Modern Backup Storage mit Backup Server](backup-mabs-add-storage.md)

---
title: Sicherung und Wiederherstellung für SQL Server auf Azure-VMs | Microsoft-Dokumentation
description: Erläutert Überlegungen zur Sicherung und Wiederherstellung für SQL Server-Datenbanken auf virtuellen Azure-Computern.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/04/2018
ms.author: mikeray
ms.openlocfilehash: ab239d0546508d74874c6b6be03f6afc06b08fa7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60563415"
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Sicherung und Wiederherstellung für SQL Server auf virtuellen Azure-Computern

Dieser Artikel bietet Informationen zu den Sicherungs- und Wiederherstellungsoptionen für SQL Server-Instanzen, die auf einem virtuellen Windows-Computer in Azure ausgeführt werden. Azure Storage verwaltet drei Kopien jedes Azure-VM-Datenträgers, um den Schutz vor Datenverlust oder physischer Datenbeschädigung zu gewährleisten. Anders als in einem lokalen Szenario müssen Sie sich also keine Gedanken über Hardwarefehler machen. Sie sollten weiterhin Ihre SQL Server-Datenbanken sichern, um sich vor Anwendungs- oder Benutzerfehlern zu schützen, z.B. durch unbeabsichtigte Dateneinfügungen oder -löschungen. In einer solchen Situation ist wichtig, die Datenbanken zu einem bestimmten Zeitpunkt wiederherstellen zu können.

Der erste Teil dieses Artikels bietet eine Übersicht über die verfügbaren Sicherungs- und Wiederherstellungsoptionen. Danach folgen einige Abschnitte mit Informationen zu den einzelnen Strategien.

## <a name="backup-and-restore-options"></a>Sicherungs- und Wiederherstellungsoptionen

Die folgende Tabelle enthält Informationen zu verschiedenen Sicherungs- und Wiederherstellungsoptionen für SQL Server auf Azure-VMs:

| Strategie | SQL-Versionen | BESCHREIBUNG |
|---|---|---|
| [Automatisierte Sicherung](#automated) | 2014<br/> 2016<br/> 2017 | Bei der automatisierten Sicherung können Sie regelmäßige Sicherungen für alle Datenbanken auf einer SQL Server-VM planen. Die Sicherungen werden bis zu 30 Tage lang in Azure Storage gespeichert. Ab SQL Server 2016 bietet Version 2 der automatisierten Sicherung zusätzliche Optionen. Sie können z.B. manuelle Pläne sowie die Häufigkeit von vollständigen und Protokollsicherungen konfigurieren. |
| [Azure Backup für SQL-VMs](#azbackup) | 2012<br/> 2014<br/> 2016<br/> 2017 | Azure Backup bietet Sicherungsfunktionen auf Unternehmensniveau für SQL Server-Instanzen auf Azure-VMs. Mit diesem Dienst können Sie Sicherungen für mehrere Server und Tausende von Datenbanken zentral verwalten. Datenbanken können im Portal auf einen bestimmten Zeitpunkt wiederhergestellt werden. Der Dienst bietet eine anpassbare Aufbewahrungsrichtlinie, mit der sich Sicherungen auf Jahre hinaus speichern lassen. Dieses Feature ist zurzeit als öffentliche Preview verfügbar. |
| [Manuelle Sicherung](#manual) | Alle | Je nach SQL Server-Version gibt es verschiedene Methoden, um SQL Server-Instanzen, die auf einer Azure-VM ausgeführt werden, manuell zu sichern und wiederherzustellen. In diesem Szenario sind Sie dafür verantwortlich, auf welche Weise Ihre Datenbanken gesichert werden. Sie sind ebenfalls für den Speicherort und die Verwaltung dieser Sicherungen zuständig. |

In den folgenden Abschnitten wird jede Option ausführlicher beschrieben. Der letzte Abschnitt dieses Artikels enthält eine Zusammenfassung in Form einer Funktionsmatrix.

## <a id="automated"></a> Automatisierte Sicherung

Die automatisierte Sicherung ist ein automatischer Sicherungsdienst für die SQL Server-Editionen Standard und Enterprise, die auf einem virtuellen Windows-Computer in Azure ausgeführt werden. Dieser Dienst wird von der [SQL Server-IaaS-Agent-Erweiterung](virtual-machines-windows-sql-server-agent-extension.md) bereitgestellt, die auf SQL Server Windows-VM-Images im Azure-Portal automatisch installiert wird.

Alle Datenbanken werden in einem von Ihnen konfigurierten Azure-Speicherkonto gesichert. Sicherungen können verschlüsselt und bis zu 30 Tage lang aufbewahrt werden.

VMs mit SQL Server 2016 und höher bieten dank Version 2 der automatisierten Sicherung mehr Anpassungsoptionen. Diese Verbesserungen umfassen Folgendes:

- Systemdatenbanksicherungen
- Manuell konfigurierbare Sicherungszeitpläne und Zeitfenster
- Konfigurierbare Häufigkeit von vollständigen und Protokolldateisicherungen

Um eine Datenbank wiederherzustellen, müssen Sie die erforderlichen Sicherungsdateien im Speicherkonto ermitteln und die Wiederherstellung auf Ihrer SQL-VM mithilfe von SQL Server Management Studio (SSMS) oder Transact-SQL-Befehlen durchführen.

Informationen zum Konfigurieren der automatisierten Sicherung für SQL-VMs finden Sie in den folgenden Artikeln:

- **SQL Server 2016/2017**: [Automatisierte Sicherung v2 für virtuelle Azure-Computer](virtual-machines-windows-sql-automated-backup-v2.md)
- **SQL Server 2014**: [Automatisierte Sicherung für SQL Server 2014-VMs](virtual-machines-windows-sql-automated-backup.md)

## <a id="azbackup"></a> Azure Backup für SQL-VMs

[Azure Backup](/azure/backup/) bietet Sicherungsfunktionen auf Unternehmensniveau für SQL Server-Instanzen auf Azure-VMs. Alle Sicherungen werden in einem Recovery Services-Tresor gespeichert und verwaltet. Diese Lösung bietet insbesondere für Unternehmen viele Vorteile:

- **Sicherungen ohne Infrastruktur**: Sie müssen keine Sicherungsserver oder Speicherorte verwalten.
- **Skalierung**: Sie können eine Vielzahl von SQL-VMs und Tausende von Datenbanken schützen.
- **Nutzungsbasierte Bezahlung**: Dies ist ein separater, von Azure Backup bereitgestellter Dienst. Wie bei allen Azure-Diensten zahlen Sie nur für das, was Sie tatsächlich nutzen.
- **Zentrale Verwaltung und Überwachung**: Verwalten Sie sämtliche Sicherungen – einschließlich anderer Workloads, die von Azure Backup unterstützt werden – zentral in einem einzigen Dashboard in Azure.
- **Richtliniengesteuerte Sicherung und Aufbewahrung**: Erstellen Sie standardmäßige Sicherungsrichtlinien für regelmäßige Sicherungen. Richten Sie Aufbewahrungsrichtlinien ein, um Sicherungen auf Jahre hinaus zu speichern.
- **Unterstützung für SQL Always On**: Ermitteln und schützen Sie eine SQL Server Always On-Konfiguration, und berücksichtigen Sie die Sicherungseinstellungen der Sicherungsverfügbarkeitsgruppe.
- **Recovery Point Objective (RPO) von 15 Minuten**: Konfigurieren Sie Sicherungen der SQL-Transaktionsprotokolle so, dass die Sicherung alle 15 Minuten erfolgt.
- **Point-in-Time-Wiederherstellung**: Verwenden Sie das Portal, um für Datenbanken den Stand zu einem bestimmten Zeitpunkt wiederherzustellen, ohne mehrere vollständige, differenzielle und Protokollsicherungen manuell wiederherstellen zu müssen.
- **Konsolidierte E-Mail-Benachrichtigungen bei Fehlern**: Konfigurieren Sie konsolidierte E-Mail-Benachrichtigungen für jede Art von Fehler.
- **Rollenbasierte Zugriffssteuerung**: Legen Sie fest, wer Sicherungs- und Wiederherstellungsvorgänge über das Portal verwalten darf.

Eine kurze Übersicht und Demo der Funktionsweise finden Sie im folgenden Video:

> [!VIDEO https://www.youtube.com/embed/wmbANpHos_E]

Diese Azure Backup-Lösung für SQL-VMs ist allgemein verfügbar. Weitere Informationen finden Sie unter [Sichern einer SQL Server-Datenbank in Azure](../../../backup/backup-azure-sql-database.md).

## <a id="manual"></a> Manuelle Sicherung

Wenn Sie Sicherungs- und Wiederherstellungsvorgänge auf Ihren SQL-VMs manuell verwalten möchten, stehen Ihnen je nach SQL Server-Version verschiedene Optionen zur Verfügung. Eine Übersicht über die Sicherung und Wiederherstellung finden Sie in einem der folgenden Artikel, je nachdem, welche Version von SQL Server Sie verwenden:

- SQL Server 2016 und höher: [Sichern und Wiederherstellen von SQL Server-Datenbanken](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- SQL Server 2014: [Sichern und Wiederherstellen von SQL Server-Datenbanken](https://msdn.microsoft.com/library/ms187048%28v=sql.120%29.aspx)
- SQL Server 2012: [Sichern und Wiederherstellen von SQL Server-Datenbanken](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)
- SQL Server 2008 R2: [Sichern und Wiederherstellen von Datenbanken in SQL Server](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)
- SQL Server 2008: [Sichern und Wiederherstellen von Datenbanken in SQL Server](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)

In den folgenden Abschnitten werden verschiedene Optionen für die manuelle Sicherung und Wiederherstellung detailliert beschrieben.

### <a name="backup-to-attached-disks"></a>Sicherung auf angefügten Datenträgern

Für SQL Server auf Azure-VMs können Sie native Sicherungs- und Wiederherstellungsverfahren mit angefügten Datenträgern auf der VM als Ziel der Sicherungsdateien verwenden. Die Anzahl von Datenträgern, die an einen virtuellen Azure-Computer angefügt werden können, ist allerdings je nach [Größe des virtuellen Computers](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) begrenzt. Zudem muss der Mehraufwand für die Datenträgerverwaltung berücksichtigt werden.

Ein Beispiel für die manuelle Erstellung einer vollständigen Datenbanksicherung mithilfe von SQL Server Management Studio (SSMS) oder Transact-SQL finden Sie unter [Erstellen einer vollständigen Datenbanksicherung](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server).

### <a name="backup-to-url"></a>Sicherung über URLs

Ab SQL Server 2012 SP1 CU2 können Sie Sicherungen und Wiederherstellungen direkt in Microsoft Azure-Blobspeicher durchführen, ein Verfahren, das auch als „Sicherung über URLs“ bekannt ist. In SQL Server 2016 wurden auch folgende Erweiterungen für dieses Feature eingeführt:

| 2016-Erweiterung | Details |
| --- | --- |
| **Striping** |Für Sicherungen in Microsoft Azure Blob Storage unterstützt SQL Server 2016 das Sichern in mehreren Blobs, um die Sicherung großer Datenbanken mit bis zu 12,8 TB zu ermöglichen. |
| **Momentaufnahmesicherung** |Durch die Verwendung von Azure-Momentaufnahmen ermöglicht die Dateimomentaufnahme-Sicherung von SQL Server nahezu sofortige Backups und schnelle Wiederherstellungen für Datenbankdateien, die mit dem Azure Blob Storage-Dienst gespeichert werden. Sicherungs- und Wiederherstellungsrichtlinien lassen sich dank dieser Funktion vereinfachen. Die Dateimomentaufnahme-Sicherung unterstützt auch Point-in-Time-Wiederherstellungen. Weitere Informationen finden Sie im Artikel zu [Momentaufnahmesicherungen für Datenbankdateien in Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure). |

Weitere Informationen finden Sie in einem der folgenden Artikel, je nachdem, welche Version von SQL Server Sie verwenden:

- **SQL Server 2016/2017**: [SQL Server-Sicherung über URLs](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [SQL Server 2014-Sicherung über URLs](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)
- **SQL Server 2012**: [SQL Server 2012-Sicherung über URLs](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)

### <a name="managed-backup"></a>Managed Backup

Ab SQL Server 2014 automatisiert Managed Backup die Erstellung von Sicherungen in Azure Storage. Hinter den Kulissen nutzt Managed Backup das Feature „Sicherung über URLs“, das im vorherigen Abschnitt dieses Artikels beschrieben wird. Managed Backup ist auch das zugrunde liegende Feature, das den Dienst für automatisierte Sicherungen für SQL Server-VMs unterstützt.

Ab SQL Server 2016 wurde Managed Backup um zusätzliche Optionen für die Planung, die Sicherung von Systemdatenbanken und die Häufigkeit von vollständigen und Protokollsicherungen ergänzt.

Weitere Informationen finden Sie in einem der folgenden Artikel, je nachdem, welche Version von SQL Server Sie verwenden:

- SQL Server 2016 und höher: [SQL Server Managed Backup für Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- SQL Server 2014: [SQL Server Managed Backup für Microsoft Azure](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)

## <a name="decision-matrix"></a>Entscheidungsmatrix

In der folgenden Tabelle sind die Funktionen jeder Sicherungs- und Wiederherstellungsoption für virtuelle SQL Server-Computer in Azure zusammengefasst.

|| **Automatisierte Sicherung** | **Azure Backup für SQL** | **Manuelle Sicherung** |
|---|---|---|---|
| Erfordert zusätzlichen Azure-Dienst |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Konfigurieren einer Sicherungsrichtlinie im Azure-Portal | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Wiederherstellen von Datenbanken im Azure-Portal |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Verwalten mehrerer Server in einem Dashboard |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Point-in-Time-Wiederherstellung | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Recovery Point Objective (RPO) von 15 Minuten | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Richtlinie für kurzfristige Sicherungsaufbewahrung (Tage) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Richtlinie für langfristige Sicherungsaufbewahrung (Monate, Jahre) |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Integrierte Unterstützung für SQL Server Always On |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Sicherung in Azure Storage-Konten | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automatisch) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automatisch) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(vom Kunden verwaltet) |
| Verwaltung von Speicher- und Sicherungsdateien | | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |  |
| Sicherung auf angefügten Datenträgern auf der VM |   |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Zentrale, anpassbare Sicherungsberichte |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Konsolidierte E-Mail-Benachrichtigungen bei Fehlern |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Anpassen der Überwachung auf der Grundlage von Azure Monitor-Protokollen |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Überwachen von Sicherungsaufträgen mit SSMS oder Transact-SQL-Skripts | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Wiederherstellen von Datenbanken mit SSMS oder Transact-SQL-Skripts | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |

## <a name="next-steps"></a>Nächste Schritte

Beim Planen Ihrer Bereitstellung von SQL Server auf einer Azure-VM sind die Informationen im folgenden Leitfaden hilfreich: [Bereitstellen eines virtuellen Windows-Computers mit SQL Server im Azure-Portal](virtual-machines-windows-portal-sql-server-provision.md).

Obwohl Sie Ihre Daten durch Sicherung und Wiederherstellung migrieren können, sind möglicherweise einfachere Migrationspfade für SQL Server auf einer Azure-VM verfügbar. Eine vollständige Erläuterung der Migrationsoptionen und Empfehlungen finden Sie unter [Migrieren einer Datenbank zu SQL Server auf einem virtuellen Azure-Computer](virtual-machines-windows-migrate-sql.md).

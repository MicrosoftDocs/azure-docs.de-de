---
title: Sicherung und Wiederherstellung für SQL Server auf Azure-VMs | Microsoft-Dokumentation
description: Erläutert Überlegungen zur Sicherung und Wiederherstellung für SQL Server-Datenbanken auf virtuellen Azure-Computern.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/04/2018
ms.author: mikeray
ms.openlocfilehash: 4a102fc805750f7dd460c1878a730b9ca4ce1c37
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359369"
---
# <a name="backup-and-restore-for-sql-server-on-azure-vms"></a>Sicherung und Wiederherstellung für SQL Server auf Azure-VMs
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Dieser Artikel bietet Informationen zu den Sicherungs- und Wiederherstellungsoptionen für SQL Server-Instanzen, die auf einem virtuellen Windows-Computer (VM) in Azure ausgeführt werden. Azure Storage verwaltet drei Kopien jedes Azure-VM-Datenträgers, um den Schutz vor Datenverlust oder physischer Datenbeschädigung zu gewährleisten. Anders als bei einem lokalen SQL Server müssen Sie sich also keine Gedanken über Hardwarefehler machen. Sie sollten weiterhin Ihre SQL Server-Datenbanken sichern, um sich vor Anwendungs- oder Benutzerfehlern zu schützen, z. B. durch unbeabsichtigte Dateneinfügungen oder -löschungen. In einer solchen Situation ist wichtig, die Datenbanken zu einem bestimmten Zeitpunkt wiederherstellen zu können.

Der erste Teil dieses Artikels bietet eine Übersicht über die verfügbaren Sicherungs- und Wiederherstellungsoptionen. Danach folgen einige Abschnitte mit Informationen zu den einzelnen Strategien.

## <a name="backup-and-restore-options"></a>Sicherungs- und Wiederherstellungsoptionen

Die folgende Tabelle enthält Informationen zu verschiedenen Sicherungs- und Wiederherstellungsoptionen für SQL Server auf Azure-VMs:

| Strategie | SQL-Versionen | BESCHREIBUNG |
|---|---|---|
| [Automatisierte Sicherung](#automated) | 2014<br/> 2016<br/> 2017 | Bei der automatisierten Sicherung können Sie regelmäßige Sicherungen für alle Datenbanken auf einer SQL Server-VM planen. Die Sicherungen werden bis zu 30 Tage lang in Azure Storage gespeichert. Ab SQL Server 2016 bietet Version 2 der automatisierten Sicherung zusätzliche Optionen. Sie können z.B. manuelle Pläne sowie die Häufigkeit von vollständigen und Protokollsicherungen konfigurieren. |
| [Azure Backup für SQL-VMs](#azbackup) | 2008<br/> 2012<br/> 2014<br/> 2016<br/> 2017 | Azure Backup bietet Sicherungsfunktionen auf Unternehmensniveau für SQL Server auf Azure-VMs. Mit diesem Dienst können Sie Sicherungen für mehrere Server und Tausende von Datenbanken zentral verwalten. Datenbanken können im Portal auf einen bestimmten Zeitpunkt wiederhergestellt werden. Der Dienst bietet eine anpassbare Aufbewahrungsrichtlinie, mit der sich Sicherungen auf Jahre hinaus speichern lassen. |
| [Manuelle Sicherung](#manual) | All | Je nach SQL Server-Version gibt es verschiedene Methoden, um SQL Server, die auf einer Azure-VM ausgeführt werden, manuell zu sichern und wiederherzustellen. In diesem Szenario sind Sie dafür verantwortlich, auf welche Weise Ihre Datenbanken gesichert werden. Sie sind ebenfalls für den Speicherort und die Verwaltung dieser Sicherungen zuständig. |

In den folgenden Abschnitten wird jede Option ausführlicher beschrieben. Der letzte Abschnitt dieses Artikels enthält eine Zusammenfassung in Form einer Funktionsmatrix.

## <a name="automated-backup"></a><a id="automated"></a> Automatisierte Sicherung

Die automatisierte Sicherung ist ein automatischer Sicherungsdienst für die SQL Server-Editionen Standard und Enterprise, die auf einem virtuellen Windows-Computer in Azure ausgeführt werden. Dieser Dienst wird von der [SQL Server-IaaS-Agent-Erweiterung](sql-server-iaas-agent-extension-automate-management.md) bereitgestellt, die auf SQL Server Windows-VM-Images im Azure-Portal automatisch installiert wird.

Alle Datenbanken werden in einem von Ihnen konfigurierten Azure-Speicherkonto gesichert. Sicherungen können verschlüsselt und bis zu 30 Tage lang aufbewahrt werden.

VMs mit SQL Server 2016 und höher bieten dank Version 2 der automatisierten Sicherung mehr Anpassungsoptionen. Diese Verbesserungen umfassen Folgendes:

- Systemdatenbanksicherungen
- Manuell konfigurierbare Sicherungszeitpläne und Zeitfenster
- Konfigurierbare Häufigkeit von vollständigen und Protokolldateisicherungen

Um eine Datenbank wiederherzustellen, müssen Sie die erforderlichen Sicherungsdateien im Speicherkonto ermitteln und die Wiederherstellung auf Ihrer SQL-VM mithilfe von SQL Server Management Studio (SSMS) oder Transact-SQL-Befehlen durchführen.

Informationen zum Konfigurieren der automatisierten Sicherung für SQL-VMs finden Sie in den folgenden Artikeln:

- **SQL Server 2016/2017**: [Automatisierte Sicherung v2 für virtuelle Azure-Computer](automated-backup.md)
- **SQL Server 2014**: [Automatisierte Sicherung für SQL Server 2014-VMs](automated-backup-sql-2014.md)

## <a name="azure-backup-for-sql-vms"></a><a id="azbackup"></a> Azure Backup für SQL-VMs

[Azure Backup](../../../backup/index.yml) bietet Sicherungsfunktionen auf Unternehmensniveau für SQL Server auf Azure-VMs. Alle Sicherungen werden in einem Recovery Services-Tresor gespeichert und verwaltet. Diese Lösung bietet insbesondere für Unternehmen viele Vorteile:

- **Sicherungen ohne Infrastruktur**: Sie müssen keine Sicherungsserver oder Speicherorte verwalten.
- **Skalierung**: Sie können eine Vielzahl von SQL-VMs und Tausende von Datenbanken schützen.
- **Nutzungsbasierte Bezahlung**: Dies ist ein separater, von Azure Backup bereitgestellter Dienst. Wie bei allen Azure-Diensten zahlen Sie nur für das, was Sie tatsächlich nutzen.
- **Zentrale Verwaltung und Überwachung**: Verwalten Sie sämtliche Sicherungen – einschließlich anderer Workloads, die von Azure Backup unterstützt werden – zentral in einem einzigen Dashboard in Azure.
- **Richtliniengesteuerte Sicherung und Aufbewahrung**: Erstellen Sie standardmäßige Sicherungsrichtlinien für regelmäßige Sicherungen. Richten Sie Aufbewahrungsrichtlinien ein, um Sicherungen auf Jahre hinaus zu speichern.
- **Unterstützung für SQL Always On**: Ermitteln und schützen Sie eine SQL Server Always On-Konfiguration, und berücksichtigen Sie die Sicherungseinstellungen der Sicherungsverfügbarkeitsgruppe.
- **Recovery Point Objective (RPO) von 15 Minuten**: Konfigurieren Sie Sicherungen der SQL-Transaktionsprotokolle so, dass die Sicherung alle 15 Minuten erfolgt.
- **Point-in-Time-Wiederherstellung**: Verwenden Sie das Portal, um für Datenbanken den Stand zu einem bestimmten Zeitpunkt wiederherzustellen, ohne mehrere vollständige, differenzielle und Protokollsicherungen manuell wiederherstellen zu müssen.
- **Konsolidierte E-Mail-Benachrichtigungen bei Fehlern**: Konfigurieren Sie konsolidierte E-Mail-Benachrichtigungen für jede Art von Fehler.
- **Rollenbasierte Zugriffssteuerung in Azure:** Legen Sie fest, wer Sicherungs- und Wiederherstellungsvorgänge über das Portal verwalten darf.

Eine kurze Übersicht und Demo der Funktionsweise finden Sie im folgenden Video:

> [!VIDEO https://www.youtube.com/embed/wmbANpHos_E]

Diese Azure Backup-Lösung für SQL-VMs ist allgemein verfügbar. Weitere Informationen finden Sie unter [Sichern einer SQL Server-Datenbank in Azure](../../../backup/backup-azure-sql-database.md).

## <a name="manual-backup"></a><a id="manual"></a> Manuelle Sicherung

Wenn Sie Sicherungs- und Wiederherstellungsvorgänge auf Ihren SQL-VMs manuell verwalten möchten, stehen Ihnen je nach SQL Server-Version verschiedene Optionen zur Verfügung. Eine Übersicht über die Sicherung und Wiederherstellung finden Sie in einem der folgenden Artikel, je nachdem, welche Version von SQL Server Sie verwenden:

- SQL Server 2016 und höher: [Sichern und Wiederherstellen von SQL Server-Datenbanken](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- SQL Server 2014: [Sichern und Wiederherstellen von Datenbanken in SQL Server](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases?viewFallbackFrom=sql-server-2014)
- SQL Server 2012: [Sichern und Wiederherstellen von Datenbanken in SQL Server](/previous-versions/sql/sql-server-2012/ms187048(v=sql.110))
- SQL Server 2008 R2: [Sichern und Wiederherstellen von Datenbanken in SQL Server](/previous-versions/sql/sql-server-2008-r2/ms187048(v=sql.105))
- SQL Server 2008: [Sichern und Wiederherstellen von Datenbanken in SQL Server](/previous-versions/sql/sql-server-2008/ms187048(v=sql.100))

In den folgenden Abschnitten werden verschiedene Optionen für die manuelle Sicherung und Wiederherstellung detailliert beschrieben.

### <a name="backup-to-attached-disks"></a>Sicherung auf angefügten Datenträgern

Für SQL Server auf Azure-VMs können Sie native Sicherungs- und Wiederherstellungsverfahren mit angefügten Datenträgern auf der VM als Ziel der Sicherungsdateien verwenden. Die Anzahl von Datenträgern, die an einen virtuellen Azure-Computer angefügt werden können, ist allerdings je nach [Größe des virtuellen Computers](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) begrenzt. Zudem muss der Mehraufwand für die Datenträgerverwaltung berücksichtigt werden.

Ein Beispiel für die manuelle Erstellung einer vollständigen Datenbanksicherung mithilfe von SQL Server Management Studio (SSMS) oder Transact-SQL finden Sie unter [Erstellen einer vollständigen Datenbanksicherung](/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server).

### <a name="backup-to-url"></a>Sicherung über URLs

Ab SQL Server 2012 SP1 CU2 können Sie Sicherungen und Wiederherstellungen direkt in Microsoft Azure-Blobspeicher durchführen, ein Verfahren, das auch als „Sicherung über URLs“ bekannt ist. In SQL Server 2016 wurden auch folgende Erweiterungen für dieses Feature eingeführt:

| 2016-Erweiterung | Details |
| --- | --- |
| **Striping** |Für Sicherungen in Microsoft Azure Blob Storage unterstützt SQL Server 2016 das Sichern in mehreren Blobs, um die Sicherung großer Datenbanken mit bis zu 12,8 TB zu ermöglichen. |
| **Momentaufnahmesicherung** |Durch die Verwendung von Azure-Momentaufnahmen ermöglicht die Dateimomentaufnahme-Sicherung von SQL Server nahezu sofortige Backups und schnelle Wiederherstellungen für Datenbankdateien, die mit dem Azure Blob Storage-Dienst gespeichert werden. Sicherungs- und Wiederherstellungsrichtlinien lassen sich dank dieser Funktion vereinfachen. Die Dateimomentaufnahme-Sicherung unterstützt auch Point-in-Time-Wiederherstellungen. Weitere Informationen finden Sie im Artikel zu [Momentaufnahmesicherungen für Datenbankdateien in Azure](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure). |

Weitere Informationen finden Sie in einem der folgenden Artikel, je nachdem, welche Version von SQL Server Sie verwenden:

- **SQL Server 2016/2017**: [SQL Server-Sicherung über URLs](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [SQL Server 2014-Sicherung über URLs](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service?viewFallbackFrom=sql-server-2014)
- **SQL Server 2012**: [SQL Server 2012-Sicherung über URLs](/previous-versions/sql/sql-server-2012/jj919148(v=sql.110))

### <a name="managed-backup"></a>Managed Backup

Ab SQL Server 2014 automatisiert Managed Backup die Erstellung von Sicherungen in Azure Storage. Hinter den Kulissen nutzt Managed Backup das Feature „Sicherung über URLs“, das im vorherigen Abschnitt dieses Artikels beschrieben wird. Managed Backup ist auch das zugrunde liegende Feature, das den Dienst für automatisierte Sicherungen für SQL Server-VMs unterstützt.

Ab SQL Server 2016 wurde Managed Backup um zusätzliche Optionen für die Planung, die Sicherung von Systemdatenbanken und die Häufigkeit von vollständigen und Protokollsicherungen ergänzt.

Weitere Informationen finden Sie in einem der folgenden Artikel, je nachdem, welche Version von SQL Server Sie verwenden:

- SQL Server 2016 und höher: [SQL Server Managed Backup für Microsoft Azure](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- SQL Server 2014: [SQL Server Managed Backup für Microsoft Azure](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?viewFallbackFrom=sql-server-2014)

## <a name="decision-matrix"></a>Entscheidungsmatrix

In der folgenden Tabelle sind die Funktionen jeder Sicherungs- und Wiederherstellungsoption für virtuelle SQL Server-Computer in Azure zusammengefasst.

| Option | Automatisierte Sicherung | Azure Backup für SQL | Manuelle Sicherung |
|---|---|---|---|
| Erfordert zusätzlichen Azure-Dienst |   | ![Grünes Häkchen](./media/backup-restore/yes.png) |   |
| Konfigurieren einer Sicherungsrichtlinie im Azure-Portal | ![Grünes Häkchen](./media/backup-restore/yes.png) | ![Grünes Häkchen](./media/backup-restore/yes.png) |   |
| Wiederherstellen von Datenbanken im Azure-Portal |   | ![Grünes Häkchen](./media/backup-restore/yes.png) |   |
| Verwalten mehrerer Server in einem Dashboard |   | ![Grünes Häkchen](./media/backup-restore/yes.png) |   |
| Wiederherstellung bis zu einem bestimmten Zeitpunkt | ![Grünes Häkchen](./media/backup-restore/yes.png) | ![Grünes Häkchen](./media/backup-restore/yes.png) | ![Grünes Häkchen](./media/backup-restore/yes.png) |
| Recovery Point Objective (RPO) von 15 Minuten | ![Grünes Häkchen](./media/backup-restore/yes.png) | ![Grünes Häkchen](./media/backup-restore/yes.png) | ![Grünes Häkchen](./media/backup-restore/yes.png) |
| Richtlinie für kurzfristige Sicherungsaufbewahrung (Tage) | ![Grünes Häkchen](./media/backup-restore/yes.png) | ![Grünes Häkchen](./media/backup-restore/yes.png) |   |
| Richtlinie für langfristige Sicherungsaufbewahrung (Monate, Jahre) |   | ![Grünes Häkchen](./media/backup-restore/yes.png) |   |
| Integrierte Unterstützung für SQL Server Always On |   | ![Grünes Häkchen](./media/backup-restore/yes.png) |   |
| Sicherung in Azure Storage-Konten | ![Grünes Häkchen](./media/backup-restore/yes.png)(automatisch) | ![Grünes Häkchen](./media/backup-restore/yes.png)(automatisch) | ![Grünes Häkchen](./media/backup-restore/yes.png)(vom Kunden verwaltet) |
| Verwaltung von Speicher- und Sicherungsdateien | | ![Grünes Häkchen](./media/backup-restore/yes.png) |  |
| Sicherung auf angefügten Datenträgern auf der VM |   |   | ![Grünes Häkchen](./media/backup-restore/yes.png) |
| Zentrale, anpassbare Sicherungsberichte |   | ![Grünes Häkchen](./media/backup-restore/yes.png) |   |
| Konsolidierte E-Mail-Benachrichtigungen bei Fehlern |   | ![Grünes Häkchen](./media/backup-restore/yes.png) |   |
| Anpassen der Überwachung auf der Grundlage von Azure Monitor-Protokollen |   | ![Grünes Häkchen](./media/backup-restore/yes.png) |   |
| Überwachen von Sicherungsaufträgen mit SSMS oder Transact-SQL-Skripts | ![Grünes Häkchen](./media/backup-restore/yes.png) | ![Grünes Häkchen](./media/backup-restore/yes.png) | ![Grünes Häkchen](./media/backup-restore/yes.png) |
| Wiederherstellen von Datenbanken mit SSMS oder Transact-SQL-Skripts | ![Grünes Häkchen](./media/backup-restore/yes.png) |   | ![Grünes Häkchen](./media/backup-restore/yes.png) |

## <a name="next-steps"></a>Nächste Schritte

Beim Planen Ihrer Bereitstellung von SQL Server auf einer Azure-VM sind die Informationen im folgenden Leitfaden hilfreich: [Bereitstellen eines virtuellen Windows-Computers mit SQL Server im Azure-Portal](create-sql-vm-portal.md).

Obwohl Sie Ihre Daten durch Sicherung und Wiederherstellung migrieren können, sind möglicherweise einfachere Migrationspfade für SQL Server auf einer VM verfügbar. Eine vollständige Erläuterung der Migrationsoptionen und Empfehlungen finden Sie unter [Migrieren einer Datenbank zu SQL Server auf einem virtuellen Azure-Computer](migrate-to-vm-from-sql-server.md).
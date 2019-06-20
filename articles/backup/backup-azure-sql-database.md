---
title: Sichern von SQL Server-Datenbanken in Azure | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie SQL Server in Azure sichern. In diesem Artikel wird auch die SQL Server-Wiederherstellung beschrieben.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: raynew
ms.openlocfilehash: 2a6319565aa05f34ce31a14c5fc57e591248f4ee
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399694"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Informationen zur SQL Server-Sicherung auf virtuellen Azure-Computern

SQL Server-Datenbanken sind kritische Workloads, die eine niedrige Recovery Point Objective (RPO) und eine Langzeitaufbewahrung erfordern. Sie können auf virtuellen Azure-Computern ausgeführte SQL Server-Datenbanken mithilfe von [Azure Backup](backup-overview.md) sichern.

## <a name="backup-process"></a>Sicherungsprozess

Die Lösung nutzt die nativen SQL-APIs, um Sicherungen Ihrer SQL-Datenbankinstanzen zu erstellen.

* Nachdem Sie die SQL Server-VM angegeben haben, die Sie schützen und deren Datenbanken Sie abfragen möchten, installiert der Azure Backup-Dienst eine Erweiterung zur Workloadsicherung mit dem Namen `AzureBackupWindowsWorkload` auf dem virtuellen Computer.
* Diese Erweiterung besteht aus einem Koordinator und einem SQL-Plug-In. Während der Koordinator für das Auslösen von Workflows für verschiedene Vorgänge wie Konfigurieren der Sicherung, Sicherung und Wiederherstellung zuständig ist, ist das Plug-In für den tatsächlichen Datenfluss verantwortlich.
* Um die Datenbanken auf diesem virtuellen Computer ermitteln zu können, erstellt Azure Backup das Konto `NT SERVICE\AzureWLBackupPluginSvc`. Dieses Konto wird zum Sichern und Wiederherstellen verwendet und erfordert SQL-Systemadministratorberechtigungen. Azure Backup verwendet das `NT AUTHORITY\SYSTEM` -Konto für die Ermittlung von Datenbanken und Anfragen an Datenbanken. Dieses Konto muss also über eine öffentliche Anmeldung in SQL verfügen. Wenn Sie den virtuellen SQL Server-Computer nicht in Azure Marketplace erstellt haben, erhalten Sie möglicherweise eine **UserErrorSQLNoSysadminMembership**-Fehlermeldung. Befolgen Sie in diesem Fall [diese Anweisungen](backup-azure-sql-database.md).
* Sobald Sie die Konfiguration des Schutzes der ausgewählten Datenbanken auslösen, richtet der Sicherungsdienst den Koordinator mit den Sicherungszeitplänen und anderen Richtliniendetails ein, die die Erweiterung lokal auf dem virtuellen Computer zwischenspeichert. 
* Zum geplanten Zeitpunkt kommuniziert der Koordinator mit dem Plug-In, und es startet das Streaming der Sicherungsdaten von der SQL Server-Instanz mit VDI.  
* Das Plug-In sendet die Daten direkt an den Recovery Services-Tresor, sodass kein Stagingspeicherort erforderlich ist. Die Daten werden verschlüsselt und vom Azure Backup-Dienst in Speicherkonten gespeichert.
* Wenn die Datenübertragung abgeschlossen ist, bestätigt der Koordinator den Commit mit dem Sicherungsdienst.

  ![Die Architektur von SQL Backup](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Vorbereitung

Überprüfen Sie zunächst Folgendes:

1. Vergewissern Sie sich, dass in Azure eine SQL Server-Instanz ausgeführt wird. Im Marketplace können Sie [schnell eine SQL Server-Instanz erstellen](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md).
2. Informieren Sie sich über [Funktionsaspekte](#feature-consideration-and-limitations) und [Unterstützung von Szenarien](#scenario-support).
3. [Lesen Sie häufig gestellte Fragen](faq-backup-sql-server.md) zu diesem Szenario.

## <a name="scenario-support"></a>Unterstützung von Szenarien

**Unterstützung** | **Details**
--- | ---
**Unterstützte Bereitstellungen** | SQL-Marketplace-Azure-VMs und Nicht-Marketplace-VMs (manuelle SQL Server-Installation) werden unterstützt.
**Unterstützte geografische Räume** | „Australien, Südosten (ASE)“, „Australien, Osten (AE)“ <br> Brasilien, Süden (BRS)<br> „Kanada, Mitte (CNC)“, „Kanada, Osten (CE)“<br> „Asien, Südosten (SEA)“, „Asien, Osten (EA)“ <br> „USA, Osten (EUS)“, „USA, Osten 2 (EUS2)“, „USA, Westen-Mitte (WCUS)“, „USA, Westen (WUS)“, „USA, Westen 2 (WUS 2)“, „USA, Norden-Mitte (NCUS)“, „USA, Mitte (CUS)“, „USA, Süden-Mitte (SCUS)“ <br> „Indien, Mitte (INC)“, „Indien, Süden (INS)“ <br> „Japan, Osten (JPE)“, „Japan, Westen (JPW)“ <br> „Südkorea, Mitte (KRC)“, „Südkorea, Süden (KRS)“ <br> „Europa, Norden (NE)“, „Europa, Westen“ <br> „Vereinigtes Königreich, Süden (UKS)“, „Vereinigtes Königreich, Westen (UKW)“
**Unterstützte Betriebssysteme** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012<br/><br/> Linux wird derzeit nicht unterstützt.
**Unterstützte SQL Server-Versionen** | SQL Server 2017; SQL Server 2016, SQL Server 2014, SQL Server 2012.<br/><br/> Enterprise, Standard, Web, Developer, Express
**Unterstützte .NET-Versionen** | .NET Framework 4.5.2 und höher auf dem virtuellen Computer installiert

## <a name="feature-consideration-and-limitations"></a>Funktionsaspekte und Einschränkungen

- SQL Server-Sicherung kann im Azure-Portal oder mit **PowerShell** konfiguriert werden. CLI wird nicht unterstützt.
- Die Lösung wird für beide Arten von [Bereitstellungen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) unterstützt: Azure Resource Manager-VMs und klassische VMs.
- Der virtuelle Computer, auf dem SQL Server ausgeführt wird, benötigt eine Internetverbindung, um auf öffentliche IP-Adressen von Azure zuzugreifen.
- Die **Failoverclusterinstanz (FCI)** für SQL Server und die Failoverclusterinstanz für SQL Server Always On werden nicht unterstützt.
- Sicherungs- und Wiederherstellungsvorgänge für Spiegeldatenbanken und Datenbankmomentaufnahmen werden nicht unterstützt.
- Die Verwendung mehrerer Sicherungslösungen zum Sichern Ihrer eigenständigen SQL Server-Instanz oder SQL Always On-Verfügbarkeitsgruppe kann zu Fehlern bei der Sicherung führen. Es ist daher ratsam, von dieser Vorgehensweise abzusehen.
- Das Sichern von zwei Knoten einer Verfügbarkeitsgruppe mit derselben Lösung oder anderen Lösungen kann auch zu Fehlern bei der Sicherung führen.
- Azure Backup unterstützt für **schreibgeschützte** Datenbanken nur die Sicherungstypen „Vollständig“ und „Nur vollständig kopieren“.
- Datenbanken mit einer großen Anzahl von Dateien können nicht geschützt werden. Die maximale Anzahl von unterstützten Dateien beträgt **~1.000**.  
- Sie können bis zu **~2.000** SQL Server-Datenbanken in einem Tresor sichern. Sie können mehrere Tresore erstellen, falls Sie über eine größere Anzahl von Datenbanken verfügen.
- Sie können die Sicherung für bis zu **50** Datenbanken auf einmal konfigurieren; diese Einschränkung trägt dazu bei, Sicherungslasten zu optimieren.
- Wir unterstützen Datenbanken bis zu einer Größe von **2TB**; darüber könnten Leistungsprobleme auftreten.
- Um einen Eindruck davon zu bekommen, wie viele Datenbanken pro Server geschützt werden können, müssen wir Faktoren wie z.B. Bandbreite, VM-Größe, Sicherungshäufigkeit, Datenbankgröße usw. berücksichtigen. Wir arbeiten an einem Planer, mit dem Sie diese Anzahl jeweils selbst berechnen können. Wir werden ihn bald veröffentlichen.
- Bei Verfügbarkeitsgruppen werden Sicherungen basierend auf einigen Faktoren auf den verschiedenen Knoten erstellt. Das Sicherungsverhalten für eine Verfügbarkeitsgruppe ist unten zusammengefasst.

### <a name="back-up-behavior-in-case-of-always-on-availability-groups"></a>Sicherungsverhalten von Always On-Verfügbarkeitsgruppen

Es wird empfohlen, die Sicherung nur auf einem Knoten der Verfügbarkeitsgruppe zu konfigurieren. Die Sicherung sollte immer in derselben Region wie der primäre Knoten konfiguriert werden. Anders ausgedrückt: Der primäre Knoten muss immer in der Region vorhanden sein, in der Sie die Sicherung konfigurieren. Wenn sich alle Knoten der Verfügbarkeitsgruppe in derselben Region befinden, in der die Sicherung konfiguriert ist, ist alles in Ordnung.

**Für regionsübergreifende Verfügbarkeitsgruppe**
- Unabhängig von der Sicherungseinstellung werden Sicherungen nicht für die Knoten durchgeführt, die sich nicht in derselben Region befinden, in der die Sicherung konfiguriert ist. Dies liegt daran, dass regionsübergreifende Sicherungen nicht unterstützt werden. Wenn Sie nur über zwei Knoten verfügen und sich der sekundäre Knoten in der anderen Region befindet: In diesem Fall werden die Sicherungen weiterhin über den primären Knoten durchgeführt (sofern Ihre Sicherungseinstellung nicht „Nur sekundär“ lautet).
- Wenn ein Failover in einer anderen Region als der Region durchgeführt wird, in der die Sicherung konfiguriert ist, tritt für Sicherungen auf den Knoten in der Failoverregion ein Fehler auf.

Abhängig von der Sicherungseinstellung und den Sicherungstypen (Vollständig/Differenziell/Protokoll/Nur vollständig kopieren) werden die Sicherungen von einem bestimmten Knoten (Primär/Sekundär) erstellt.

- **Sicherungseinstellung: Primär**

**Sicherungstyp** | **Node**
    --- | ---
    Vollständig | Primär
    Differenziell | Primär
    Protokoll |  Primär
    Nur vollständig kopieren |  Primär

- **Sicherungseinstellung: Nur sekundär**

**Sicherungstyp** | **Node**
--- | ---
Vollständig | Primär
Differenziell | Primär
Protokoll |  Sekundär
Nur vollständig kopieren |  Sekundär

- **Sicherungseinstellung: Sekundär**

**Sicherungstyp** | **Node**
--- | ---
Vollständig | Primär
Differenziell | Primär
Protokoll |  Sekundär
Nur vollständig kopieren |  Sekundär

- **Keine Sicherungseinstellung**

**Sicherungstyp** | **Node**
--- | ---
Vollständig | Primär
Differenziell | Primär
Protokoll |  Sekundär
Nur vollständig kopieren |  Sekundär

## <a name="fix-sql-sysadmin-permissions"></a>Beheben von Problemen mit SQL-Systemadministratorberechtigungen

  Führen Sie die folgenden Schritte aus, wenn Sie Berechtigungen aufgrund des Fehlers **UserErrorSQLNoSysadminMembership** korrigieren müssen:

  1. Verwenden Sie ein Konto mit SQL Server-Systemadministratorberechtigungen, um sich bei SQL Server Management Studio (SSMS) anzumelden. Wenn Sie keine speziellen Berechtigungen benötigen, sollte die Windows-Authentifizierung funktionieren.
  2. Öffnen Sie auf der SQL Server-Instanz den Ordner **Sicherheit/Anmeldungen**.

      ![Öffnen von Ordner „Sicherheit/Anmeldungen“, um Konten anzuzeigen](./media/backup-azure-sql-database/security-login-list.png)

  3. Klicken Sie mit der rechten Maustaste auf den Ordner **Anmeldungen**, und wählen Sie **Neue Anmeldung** aus. Wählen Sie in **Anmeldung – Neu** die Option **Suche** aus.

      ![Auswählen von „Suchen“ im Dialogfeld „Anmeldung – Neu“](./media/backup-azure-sql-database/new-login-search.png)

  4. Das virtuelle Windows-Dienstkonto **NT SERVICE\AzureWLBackupPluginSvc** wurde bei der Registrierung des virtuellen Computers und der SQL-Ermittlungsphase erstellt. Geben Sie den Kontonamen ein, wie in **Namen des auszuwählenden Objekts eingeben** dargestellt. Wählen Sie **Namen überprüfen** aus, um den Namen aufzulösen. Klicken Sie auf **OK**.

      ![Klicken auf „Namen überprüfen“, um den unbekannten Dienstnamen aufzulösen](./media/backup-azure-sql-database/check-name.png)

  5. Stellen Sie in **Serverrollen** sicher, dass die Rolle **sysadmin** ausgewählt ist. Klicken Sie auf **OK**. Die erforderlichen Berechtigungen sollten jetzt vorhanden sein.

      ![Sicherstellen, dass die Serverrolle „Sysadmin“ ausgewählt ist](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Ordnen Sie nun die Datenbank dem Recovery Services-Tresor zu. Klicken Sie im Azure-Portal in der Liste **Geschützte Server** mit der rechten Maustaste auf den fehlerhaften Server, und wählen Sie **Datenbanken neu ermitteln** aus.

      ![Überprüfen der entsprechenden Serverberechtigungen](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Der Fortschritt kann im Bereich **Benachrichtigungen** verfolgt werden. Wenn die ausgewählten Datenbanken gefunden wurden, wird eine Erfolgsmeldung angezeigt.

      ![Meldung über erfolgreiche Bereitstellung](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> Sind auf Ihrem SQL-Server mehrere Instanzen von SQL Server installiert, müssen Sie allen SQL-Instanzen Systemadministratorberechtigungen für das Konto **NT Service\AzureWLBackupPluginSvc** hinzufügen.

## <a name="next-steps"></a>Nächste Schritte

- [Informieren Sie sich über](backup-sql-server-database-azure-vms.md) das Sichern von SQL Server-Datenbanken.
- [Informieren Sie sich über](restore-sql-database-azure-vm.md) das Wiederherstellen von gesicherten SQL Server-Datenbanken.
- [Informieren Sie sich über](manage-monitor-sql-database-backup.md) das Verwalten von gesicherten SQL Server-Datenbanken.

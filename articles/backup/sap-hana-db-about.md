---
title: Informationen zur SAP HANA-Datenbanksicherung in Azure Virtual Machines
description: In diesem Artikel erfahren Sie mehr über das Sichern von SAP HANA-Datenbanken, die in Azure Virtual Machines ausgeführt werden.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: efb9c3f786e429df404e261f053a9c9a9b032e11
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96296453"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>Informationen zur SAP HANA-Datenbanksicherung in Azure Virtual Machines

SAP HANA-Datenbanken sind geschäftskritische Workloads, die eine niedrige Recovery Point Objective (RPO) und schnelle Recovery Time Objective (RTO) erfordern. Mithilfe von [Azure Backup](./backup-overview.md) können Sie jetzt [auf virtuellen Azure-Computern ausgeführte SAP HANA-Datenbanken sichern](./tutorial-backup-sap-hana-db.md).

Azure Backup ist SAP [Backint-zertifiziert](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) und bietet native Sicherungsunterstützung durch Verwendung der nativen APIs von SAP HANA. Dieses Angebot von Azure Backup orientiert sich an dem Azure Backup-Mantra von **Sicherungen ohne Infrastruktur**, sodass keine Sicherungsinfrastruktur bereitgestellt und verwaltet werden muss. Sie können SAP HANA Datenbanken, die auf virtuellen Azure-Computern ausgeführt werden, jetzt nahtlos sichern und wiederherstellen ([virtuelle Computer der M-Serie](../virtual-machines/m-series.md) werden jetzt auch unterstützt) und die von Azure Backup bereitgestellten Unternehmensverwaltungsfunktionen nutzen.

## <a name="added-value"></a>Hinzugefügter Wert

Die Verwendung von Azure Backup zum Sichern und Wiederherstellen von SAP HANA-Datenbanken bietet die folgenden Vorteile:

* **Recovery Point Objective (RPO) von 15 Minuten**: Die Wiederherstellung kritischer Daten von bis zu 15 Minuten ist nun möglich.
* **Zeitpunktwiederherstellungen mit einem Klick**: Die Wiederherstellung von Produktionsdaten auf alternativen HANA-Servern ist jetzt einfach. Die Verkettung von Sicherungen und Katalogen zum Ausführen von Wiederherstellungen wird von Azure im Hintergrund verwaltet.
* **Langfristige Aufbewahrung**: Gemäß strengen Compliance- und Überwachungsanforderungen. Bewahren Sie Ihre Sicherungen basierend auf der Aufbewahrungsdauer über Jahre hinweg auf, nach deren Ablauf die Wiederherstellungspunkte automatisch durch die integrierte Funktionen zur Lebenszyklusverwaltung gelöscht werden.
* **Sicherungsverwaltung von Azure**: Verwenden Sie die Verwaltungs- und Überwachungsfunktionen von Azure Backup, um die Verwaltung zu verbessern. Azure CLI wird ebenfalls unterstützt.

Informationen zu den zurzeit unterstützten Sicherungs- und Wiederherstellungsszenarien finden Sie in der [Unterstützungsmatrix für SAP HANA-Szenarien](./sap-hana-backup-support-matrix.md#scenario-support).

## <a name="backup-architecture"></a>Sichern der Architektur

![Diagramm der Sicherungsarchitektur](./media/sap-hana-db-about/backup-architecture.png)

* Der Sicherungsvorgang beginnt mit dem [Erstellen eines Recovery Services-Tresors](./tutorial-backup-sap-hana-db.md#create-a-recovery-services-vault) in Azure. Dieser Tresor dient zum Speichern der Sicherungen und Wiederherstellungspunkte, die im Laufe der Zeit erstellt werden.
* Der virtuelle Azure-Computer, auf dem der SAP HANA-Server ausgeführt wird, ist beim Tresor registriert, und die zu sichernden Datenbanken werden [erkannt](./tutorial-backup-sap-hana-db.md#discover-the-databases). Um dem Azure Backup-Dienst das Ermitteln von Datenbanken zu ermöglichen, muss ein [Vorregistrierungsskript](https://aka.ms/scriptforpermsonhana) auf dem HANA-Server als root-Benutzer ausgeführt werden.
* Mit diesem Skript wird **AZUREWLBACKUPHANAUSER** DB-Benutzer und ein entsprechender Schlüssel mit demselben Namen in **hdbuserstore** erstellt. Weitere Informationen zu den Funktionen des Skripts finden Sie im Abschnitt [Aufgaben des Vorregistrierungsskripts](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).
* Der Azure Backup Dienst installiert jetzt das **Azure Backup-Plug-In für HANA** auf dem registrierten SAP HANA-Server.
* Der durch das Vorregistrierungsskript erstellte **AZUREWLBACKUPHANAUSER** DB-Benutzer wird vom **Azure Backup-Plug-In für HANA** verwendet, um alle Sicherungs- und Wiederherstellungsvorgänge auszuführen. Wenn Sie versuchen, die Sicherung für SAP HANA-Datenbanken zu konfigurieren, ohne dieses Skript auszuführen, wird möglicherweise der folgende Fehler angezeigt: **UserErrorHanaScriptNotRun**.
* Um die [Sicherung der ermittelten Datenbanken zu konfigurieren](./tutorial-backup-sap-hana-db.md#configure-backup), wählen Sie die erforderliche Sicherungsrichtlinie aus, und aktivieren Sie Sicherungen.

* Nachdem die Sicherung konfiguriert wurde, richtet der Azure Backup-Dienst die folgenden Backint-Parameter auf dem geschützten SAP HANA-Server auf DATABASE-Ebene ein:
  * [catalog_backup_using_backint:true]
  * [enable_accumulated_catalog_backup:false]
  * [parallel_data_backup_backint_channels:1]
  * [log_backup_timeout_s:900)]
  * [backint_response_timeout:7200]

>[!NOTE]
>Stellen Sie sicher, dass diese Parameter *nicht* auf HOST-Ebene vorhanden sind. Parameter auf HOST-Ebene überschreiben diese Parameter und können zu unerwartetem Verhalten führen.
>

* Das **Azure Backup-Plug-In für HANA** verwaltet alle Sicherungszeitpläne und Richtliniendetails. Die geplanten Sicherungen werden ausgelöst, und es wird über die Backint-APIs mit der **HANA-Sicherungs-Engine** kommuniziert.
* Die **HANA-Sicherungs-Engine** gibt einen Backint-Stream mit den zu sichernden Daten zurück.
* Alle vollständigen oder differenziellen geplanten und bedarfsgesteuerten (über das Azure-Portal ausgelösten) Sicherungen werden durch das **Azure Backup-Plug-In für HANA** initiiert. Protokollsicherungen werden jedoch von der **HANA-Sicherungs-Engine** selbst verwaltet und ausgelöst.
* Dank der Backint-Zertifizierung von Azure Backup für SAP HANA ist die Lösung nicht auf zugrunde liegende Datenträger- oder VM-Typen angewiesen. Für die Sicherung werden von HANA generierte Datenströme verwendet.

## <a name="using-azure-vm-backup-with-azure-sap-hana-backup"></a>Verwenden der Azure-VM-Sicherung mit der Azure-SAP HANA-Sicherung

Zusätzlich zur SAP HANA-Sicherung in Azure für die Sicherung und Wiederherstellung auf Datenbankebene können Sie die Azure-VM-Sicherungslösung verwenden, um Betriebssystemdatenträger und datenbankfremde Datenträger zu sichern.

Die [Backint-zertifizierte Azure-SAP HANA-Sicherungslösung](#backup-architecture) kann für die Datenbanksicherung und -wiederherstellung verwendet werden.

Die [Azure-VM-Sicherung](backup-azure-vms-introduction.md) kann zum Sichern der Betriebssystemdatenträger sowie anderer datenbankfremder Datenträger verwendet werden. Die VM-Sicherung wird einmal täglich erstellt und umfasst alle Datenträger (mit Ausnahme von **Betriebssystemdatenträgern mit Schreibbeschleunigung (Write Accelerator, WA)** und **Ultra Disks**). Da die Datenbank mithilfe der Azure-SAP HANA-Sicherungslösung gesichert wird, können Sie mithilfe des Features [Selektives Sichern und Wiederherstellen von Datenträgern auf Azure-VMs](selective-disk-backup-restore.md) eine dateikonsistente Sicherung erstellen, die nur die Betriebssystemdatenträger und datenbankfremden Datenträger umfasst.

Ein virtueller Computer mit SAP HANA kann wie folgt wiederhergestellt werden:

* [Stellen Sie einen neuen virtuellen Computer aus der Azure-VM-Sicherung wieder her](backup-azure-arm-restore-vms.md), und verwenden Sie dabei den aktuellen Wiederherstellungspunkt. Alternativ können Sie auch einen neuen leeren virtuellen Computer erstellen und die Datenträger aus dem aktuellen Wiederherstellungspunkt anfügen.
* Wenn WA-Datenträger ausgeschlossen werden, werden sie nicht wiederhergestellt. In diesem Fall erstellen Sie leere Datenträger mit Schreibbeschleunigung und einen Protokollbereich.
* Nachdem alle anderen Konfigurationen (IP-Adresse, Systemname usw.) festgelegt wurden, kann der Computer Datenbankdaten von Azure Backup empfangen.
* Stellen Sie nun auf dem virtuellen Computer den gewünschten Zustand (Zeitpunkt) der Datenbank aus der [Azure-SAP HANA-Datenbanksicherung](sap-hana-db-restore.md#restore-to-a-point-in-time-or-to-a-recovery-point) wieder her.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [eine auf einem virtuellen Azure-Computer ausgeführte SAP HANA-Datenbank wiederherstellen](./sap-hana-db-restore.md).
* [Verwalten und Überwachen gesicherter SAP HANA-Datenbanken](./sap-hana-db-manage.md)

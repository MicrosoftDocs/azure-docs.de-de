---
title: Sichern von Workloads in Azure mithilfe von Azure Backup Server
description: Verwenden Sie Azure Backup Server, um Workloads im Azure-Portal zu schützen und zu sichern.
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: kasinh
ms.openlocfilehash: 26f25a0dcbeef0d5b7456d42caaca392c3ca6a1a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62098861"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Installieren und Durchführen eines Upgrades für Azure Backup Server
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

In diesem Artikel wird das Vorbereiten Ihrer Umgebung für die Sicherung von Workloads per Microsoft Azure Backup Server (MABS) beschrieben. Mit Azure Backup Server können Sie Anwendungsworkloads wie Hyper-V-VMs, Microsoft SQL Server, SharePoint Server, Microsoft Exchange und Windows-Clients über eine zentrale Konsole schützen.

> [!NOTE]
> Azure Backup Server kann jetzt virtuelle VMware-Computer schützen und umfasst zudem verbesserte Sicherheitsfunktionen. Installieren Sie das Produkt entsprechend den Anweisungen in den nachfolgenden Abschnitten, und wenden Sie den aktuellen Azure Backup-Agent an. Weitere Informationen zum Sichern von VMware-Servern mit Azure Backup Server finden Sie im Artikel [Sichern eines VMware-Servers in Azure](backup-azure-backup-server-vmware.md). Weitere Informationen zu den Sicherheitsfunktionen finden Sie unter [Sicherheitsfeatures für den Schutz von Hybridsicherungen mit Azure Backup](backup-azure-security-feature.md).
>
>

Bei Bereitstellung von MABS auf einem virtuellen Azure-Computer können VMs in Azure gesichert werden, aber sie müssen sich in der gleichen Domäne befinden, um den Sicherungsvorgang zu ermöglichen. Der Prozess zum Sichern eines virtuellen Azure-Computers entspricht dem lokalen Sichern von VMs, für das Bereitstellen von MABS in Azure gelten jedoch einige Einschränkungen. Weitere Informationen zu den Einschränkungen finden Sie unter [Installieren von DPM – DPM als virtuellen Azure-Computer bereitstellen](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-1807#setup-prerequisites).

> [!NOTE]
> Azure verfügt über zwei Bereitstellungsmodelle zum Erstellen und Verwenden von Ressourcen: [Resource Manager-Bereitstellungen und klassische Bereitstellungen](../azure-resource-manager/resource-manager-deployment-model.md). Dieser Artikel enthält die Informationen und Verfahren für die Wiederherstellung virtueller Computer, die über das Resource Manager-Bereitstellungsmodell bereitgestellt werden.
>
>

Azure Backup Server erbt einen Großteil der Funktionalität für die Workloadsicherung von Data Protection Manager (DPM). Dieser Artikel enthält Links zur DPM-Dokumentation mit einer Beschreibung der gemeinsamen Funktionen. Obwohl Azure Backup Server einen Großteil der Funktionen als DPM freigegeben hat, führt Azure Backup Server weder Sicherungen auf Band durch noch wird der Dienst in System Center integriert.

## <a name="choose-an-installation-platform"></a>Auswahl der Installationsplattform
Der erste Schritt zur Inbetriebnahme von Azure Backup Server ist die Einrichtung eines Computers mit Windows Server. Der Server kann sich unter Azure oder am lokalen Standort befinden.

### <a name="using-a-server-in-azure"></a>Verwenden eines Servers in Azure
Beim Auswählen eines Servers zum Ausführen von Azure Backup Server wird empfohlen, mit einem Katalogimage von Windows Server 2012 R2 Datacenter, Windows Server 2016 Datacenter oder Windows Server 2019 Datacenter zu beginnen. Der Artikel [Erstellen Ihres ersten virtuellen Windows-Computers im Azure-Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) enthält ein Tutorial zu den ersten Schritten für den empfohlenen virtuellen Computer in Azure. Dies gilt auch, wenn Sie Azure noch nie verwendet haben. Die empfohlenen Mindestanforderungen für den virtuellen Servercomputer (VM) lauten: A2-Standard mit zwei Kernen und 3,5 GB RAM.

Beim Schützen von Workloads mit Azure Backup Server sind viele Feinheiten zu beachten. Dies wird im Artikel [Installieren von DPM als virtuellen Azure-Computer](https://technet.microsoft.com/library/jj852163.aspx)beschrieben. Lesen Sie sich den Artikel ganz durch, bevor Sie den Computer bereitstellen.

### <a name="using-an-on-premises-server"></a>Verwenden eines lokalen Servers
Wenn Sie den Basisserver nicht in Azure ausführen möchten, können Sie den Server auf einer Hyper-V-VM, einer VMware-VM oder einem physischen Host ausführen. Die empfohlenen Mindestanforderungen für die Serverhardware lauten: zwei Kerne und 4 GB RAM. In der folgenden Tabelle sind die unterstützten Betriebssysteme aufgeführt:

| Betriebssystem | Plattform | SKU |
|:--- | --- |:--- |
| Windows Server 2019 |64-Bit |Standard, Datacenter, Essentials (MABS v3 und höher) |
| Windows Server 2016 und die neuesten SPs |64-Bit |Standard, Datacenter, Essentials (MABS v2 und höher) |
| Windows Server 2012 R2 und die neuesten SPs |64-Bit |Standard, Datacenter, Foundation |
| Windows Server 2012 und die neuesten SPs |64-Bit |Datacenter, Foundation, Standard |
| Windows Storage Server 2012 R2 und die neuesten SPs |64-Bit |Standard, Workgroup |
| Windows Storage Server 2012 und neuesten SPs |64-Bit |Standard, Workgroup |

Sie können die DPM-Speicherung deduplizieren, indem Sie die Windows Server-Deduplizierung verwenden. Lesen Sie die weiteren Informationen zur Zusammenarbeit von [DPM und Deduplizierung](https://technet.microsoft.com/library/dn891438.aspx) bei Bereitstellung auf Hyper-V-VMs.

> [!NOTE]
> Azure Backup Server ist für die Ausführung auf einem dedizierten Server konzipiert, der nur zu einem Zweck verwendet wird. Azure Backup Server kann nicht auf folgenden Computern installiert werden:
> - Einem Computer, der als Domänencontroller ausgeführt wird
> - Einem Computer, auf dem die Anwendungsserverrolle installiert ist
> - Eine Computer, der als System Center Operations Manager-Verwaltungsserver fungiert
> - Einem Computer, auf dem Exchange Server ausgeführt wird
> - Einem Computer, der einen Knoten eines Clusters darstellt

Binden Sie Azure Backup Server immer in eine Domäne ein. Wenn Sie den Server in eine andere Domäne verschieben möchten, installieren Sie zuerst Azure Backup Server, und binden Sie dann den Server in die neue Domäne ein. Das Verschieben eines vorhandenen Azure Backup Server-Computers in eine neue Domäne nach der Bereitstellung wird *nicht unterstützt*.

Unabhängig davon, ob Sie Sicherungsdaten an Azure senden oder lokal speichern, muss Azure Backup Server bei einem Recovery Services-Tresor registriert sein.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Festlegen der Speicherreplikation
Bei der Speicherreplikation haben Sie die Wahl zwischen georedundantem Speicher und lokal redundantem Speicher. Recovery Services-Tresore verwenden standardmäßig georedundanten Speicher. Behalten Sie den georedundanten Speicher bei, wenn es sich hierbei um Ihren primären Tresor handelt. Wählen Sie lokal redundanten Speicher, wenn Sie eine günstigere und weniger langfristige Option wünschen. Weitere Informationen zu den Optionen für [georedundanten](../storage/common/storage-redundancy-grs.md) und [lokal redundanten](../storage/common/storage-redundancy-lrs.md) Speicher finden Sie in der [Übersicht über die Azure Storage-Replikation](../storage/common/storage-redundancy.md).

So bearbeiten Sie die Einstellung für die Speicherreplikation:

1. Wählen Sie Ihren Tresor aus, um das Tresordashboard und das Menü „Einstellungen“ zu öffnen. Sollte das Menü **Einstellungen** nicht geöffnet werden, klicken Sie im Tresordashboard auf **Alle Einstellungen**.
2. Klicken Sie im Menü **Einstellungen** auf **Sicherungsinfrastruktur** > **Sicherungskonfiguration**, um das Blatt **Sicherungskonfiguration** zu öffnen. Wählen Sie im Menü **Speicherkonfiguration** die Speicherreplikationsoption für Ihren Tresor aus.

    ![Liste der Sicherungstresore](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    Wenn Sie die Speicheroption für Ihren Tresor ausgewählt haben, können Sie den virtuellen Computer dem Tresor zuordnen. Ermitteln und registrieren Sie die virtuellen Azure-Computer, um mit der Zuordnung zu beginnen.

## <a name="software-package"></a>Softwarepaket
### <a name="downloading-the-software-package"></a>Herunterladen des Softwarepakets
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.
2. Falls Sie bereits über einen geöffneten Recovery Services-Tresor verfügen, können Sie mit Schritt 3 fortfahren. Wenn Sie keinen Recovery Services-Tresor geöffnet haben, sich aber im Azure-Portal befinden, klicken Sie im Hauptmenü auf **Durchsuchen**.

   * Geben Sie in der Liste mit den Ressourcen **Recovery Services**ein.
   * Wenn Sie mit der Eingabe beginnen, wird die Liste anhand Ihrer Eingaben gefiltert. Klicken Sie auf **Recovery Services-Tresore**, wenn der Eintrag angezeigt wird.

     ![Erstellen eines Recovery Services-Tresors – Schritt 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     Die Liste mit den Recovery Services-Tresoren wird angezeigt.
   * Wählen Sie in der Liste mit den Recovery Services-Tresoren einen Tresor aus.

     Das ausgewählte Tresor-Dashboard wird geöffnet.

     ![Tresorblatt öffnen](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. Standardmäßig wird das Blatt **Einstellungen** geöffnet. Wenn es geschlossen ist, klicken Sie auf **Einstellungen** , um das Blatt mit den Einstellungen zu öffnen.

    ![Tresorblatt öffnen](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Klicken Sie auf **Sicherung**, um den Assistenten für erste Schritte zu öffnen.

    ![Erste Schritte zur Sicherung](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    Beim Öffnen des Blatts **Erste Schritte mit der Sicherung** wird automatisch **Sicherungsziele** ausgewählt.

    ![Backup-goals-default-opened](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. Wählen Sie auf dem Blatt **Sicherungsziel** im Menü **Wo wird Ihre Workload ausgeführt?** die Option **Lokal** aus.

    ![lokal und Workloads als Ziele](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    Wählen Sie im Dropdownmenü **Was möchten Sie sichern?** die Workloads aus, die Sie mithilfe von Azure Backup Server schützen möchten, und klicken Sie anschließend auf **OK**.

    Der Assistent **Erste Schritte mit der Sicherung** ändert die Option **Infrastruktur vorbereiten**, sodass Workloads in Azure gesichert werden.

   > [!NOTE]
   > Falls Sie nur Dateien und Ordner sichern möchten, empfiehlt es sich, den Azure Backup-Agent zu verwenden und gemäß der Anleitung im Artikel [Einführung: Sichern von Dateien und Ordnern in einer Resource Manager-Bereitstellung](backup-try-azure-backup-in-10-mins.md) vorzugehen. Falls Sie nicht nur Dateien und Ordner schützen möchten oder beabsichtigen, die Schutzanforderungen auszudehnen, wählen Sie diese Workloads aus.
   >
   >

    ![Ändern des Erste-Schritte-Assistenten](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. Klicken Sie auf dem Blatt **Infrastruktur vorbereiten**, das geöffnet wird, auf die **Downloadlinks**, um Azure Backup Server zu installieren und Tresoranmeldedaten herunterzuladen. Sie verwenden die Tresoranmeldedaten bei der Registrierung von Azure Backup Server beim Recovery Services-Tresor. Mit den Links gelangen Sie zum Download Center, in dem Sie das Softwarepaket herunterladen können.

    ![Vorbereiten der Infrastruktur für Azure Backup Server](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Wählen Sie alle Dateien aus, und klicken Sie auf **Weiter**. Laden Sie alle Dateien herunter, die von der Microsoft Azure Backup-Downloadseite eingehen, und speichern Sie alle Dateien in dem gleichen Ordner.

    ![Download Center 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Da die Downloadgröße aller Dateien zusammen mehr als 3 G beträgt, dauert es über einen Downloadlink mit 10 MBit/s bis zu 60 Minuten, bis der Download abgeschlossen ist.

### <a name="extracting-the-software-package"></a>Extrahieren des Softwarepakets
Nachdem Sie alle Dateien heruntergeladen haben, klicken Sie auf **MicrosoftAzureBackupInstaller.exe**. Der **Setup-Assistent von Microsoft Azure Backup** wird gestartet, um die Setupdateien an einem Speicherort Ihrer Wahl zu extrahieren. Fahren Sie mit dem Assistenten fort, und klicken Sie auf die Schaltfläche **Extrahieren** , um den Extrahierungsprozess zu starten.

> [!WARNING]
> Zum Extrahieren der Setupdateien sind mindestens 4 GB freier Speicherplatz erforderlich.
>
>

![Setup-Assistent von Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Aktivieren Sie nach Abschluss der Extrahierung das Kontrollkästchen, um die gerade extrahierte Datei *setup.exe* zu starten und mit der Installation von Microsoft Azure Backup Server zu beginnen. Klicken Sie dann auf die Schaltfläche **Fertig stellen**.

### <a name="installing-the-software-package"></a>Installieren des Softwarepakets
1. Klicken Sie auf **Microsoft Azure Backup** , um den Setup-Assistenten zu starten.

    ![Setup-Assistent von Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Klicken Sie auf der Willkommensseite auf die Schaltfläche **Weiter** . Sie gelangen zum Abschnitt *Voraussetzungsüberprüfungen* . Klicken Sie auf diesem Bildschirm auf **Überprüfen**, um zu ermitteln, ob die Hardware- und Softwarevoraussetzungen für Azure Backup Server erfüllt sind. Wenn alle Voraussetzungen erfolgreich erfüllt wurden, sehen Sie eine Meldung, die darauf hinweist, dass der Computer die Anforderungen erfüllt. Klicken Sie auf die Schaltfläche **Weiter** .

    ![Azure Backup Server – Willkommen und Voraussetzungsüberprüfung](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Für Microsoft Azure Backup Server ist SQL Server Enterprise erforderlich. Außerdem sind dem Azure Backup Server-Installationspaket die passenden SQL Server-Binärdateien beigefügt, die Sie benötigen, falls Sie keine eigene SQL-Instanz nutzen möchten. Beim Starten einer neuen Azure Backup Server-Installation sollten Sie die Option **Neue Instanz von SQL Server bei diesem Setup installieren** auswählen und auf die Schaltfläche **Prüfen und installieren** klicken. Nachdem die erforderlichen Komponenten erfolgreich installiert wurden, klicken Sie auf **Weiter**.

    ![Azure Backup Server – SQL-Überprüfung](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Tritt ein Fehler mit der Empfehlung auf, den Computer neu zu starten, tun Sie dies, und klicken Sie auf **Erneut prüfen**. Konfigurieren Sie SQL bei Problemen mit der SQL-Konfiguration gemäß den SQL-Richtlinien neu, und versuchen Sie, MABS mithilfe der vorhandenen SQL-Instanz zu installieren bzw. ein Upgrade für diese durchzuführen.

   > [!NOTE]
   > Azure Backup Server funktioniert nicht mit einer Remoteinstanz von SQL Server. Die von Azure Backup Server verwendete Instanz muss lokal vorliegen. Für den Fall, dass Sie eine vorhandene SQL Server-Instanz für MABS verwenden, unterstützt das MABS-Setup nur die Verwendung von *benannten Instanzen* von SQL Server.

   **Manuelle Konfiguration**

   Wenn Sie Ihre eigene Instanz von SQL verwenden, stellen Sie sicher, dass Sie builtin\Administrators zur Rolle „sysadmin“ zur Masterdatenbank hinzufügen.

    **SSRS-Konfiguration mit SQL 2017**

    Wenn Sie eine eigene Instanz von SQL 2017 verwenden, müssen Sie die SSRS manuell konfigurieren. Stellen Sie nach der SSRS-Konfiguration sicher, dass die SSRS-Eigenschaft *IsInitialized* auf *True* festgelegt ist. Wenn sie auf „True“ festgelegt ist, geht MABS davon aus, dass die SSRS bereits konfiguriert sind, und MABS überspringt die SSRS-Konfiguration.

    Verwenden Sie für die SSRS-Konfiguration die folgenden Werte:

        - Service Account: ‘Use built-in account’ should be Network Service
        - Web Service URL: ‘Virtual Directory’ should be ReportServer_<SQLInstanceName>
        - Database: DatabaseName should be ReportServer$<SQLInstanceName>
        - Web Portal URL: ‘Virtual Directory’ should be Reports_<SQLInstanceName>

    Weitere Informationen zur SSRS-Konfiguration finden Sie [hier](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017).

4. Geben Sie einen Speicherort für die Installation der Microsoft Azure Backup-Serverdateien an, und klicken Sie auf **Weiter**.

    ![Voraussetzungen 2 für Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    Das Scratchverzeichnis ist eine Anforderung für die Sicherung in Azure. Stellen Sie sicher, dass das Scratchverzeichnis eine Größe von mindestens 5 % der Daten aufweist, die in der Cloud gesichert werden. Für den Datenträgerschutz müssen separate Datenträger nach Abschluss der Installation konfiguriert werden. Weitere Informationen zu Speicherpools finden Sie unter [Konfigurieren von Speicherpools und Datenträgerspeicher](https://technet.microsoft.com/library/hh758075.aspx).
5. Geben Sie ein sicheres Kennwort für eingeschränkte lokale Benutzerkonten an, und klicken Sie auf **Weiter**.

    ![Voraussetzungen 2 für Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Bestimmen Sie, ob Sie mit *Microsoft Update* nach Updates suchen möchten, und klicken Sie auf **Weiter**.

   > [!NOTE]
   > Es wird empfohlen, Windows Update an Microsoft Update umzuleiten, da Microsoft Update Sicherheit und wichtige Updates für Windows und andere Produkte wie Microsoft Azure Backup Server bietet.
   >
   >

    ![Voraussetzungen 2 für Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Überprüfen Sie die *Zusammenfassung der Einstellungen* , und klicken Sie auf **Installieren**.

    ![Voraussetzungen 2 für Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. Die Installation erfolgt in mehreren Phasen. In der ersten Phase wird der Microsoft Azure Recovery Services-Agent auf dem Server installiert. Der Assistent überprüft auch das Vorhandensein der Internetverbindung. Wenn eine Internetverbindung verfügbar ist, können Sie mit der Installation fortfahren, andernfalls müssen Sie Proxydetails angeben, um die Verbindung mit dem Internet herzustellen.

    Der nächste Schritt ist die Konfiguration des Microsoft Azure Recovery Services-Agents. Bei der Konfiguration müssen Sie Ihre Tresoranmeldeinformationen zum Registrieren des Computers beim Recovery Services-Tresor angeben. Sie geben auch eine Passphrase zum Verschlüsseln bzw. Entschlüsseln von Daten an, die zwischen Azure und Ihrem lokalen Standort ausgetauscht werden. Sie können eine automatische Passphrase generieren oder eine eigene Passphrase mit mindestens 16 Zeichen angeben. Fahren Sie mit dem Assistenten fort, bis der Agent konfiguriert wurde.

    ![Voraussetzungen 2 für Azure Backup Server](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Nach Abschluss der Registrierung von Microsoft Azure Backup Server fährt der übergreifende Setup-Assistent mit der Installation und Konfiguration von SQL Server und der Azure Backup Server-Komponenten fort. Nachdem die Installation der SQL Server-Komponenten abgeschlossen ist, werden die Azure Backup Server-Komponenten installiert.

    ![Azure Backup Server](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Wenn der Installationsschritt abgeschlossen ist, wurden auch die Desktopsymbole des Produkts bereits erstellt. Doppelklicken Sie einfach auf das Symbol, um das Produkt zu starten.

### <a name="add-backup-storage"></a>Hinzufügen von Backup Storage
Die erste Sicherungskopie wird in einem Speicherbereich vorgehalten, der dem Azure Backup Server-Computer zugeordnet ist. Weitere Informationen zum Hinzufügen von Datenträgern finden Sie unter [Konfigurieren von Speicherpools und Datenträgerspeicher](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

> [!NOTE]
> Sie müssen auch dann Backup Storage hinzufügen, wenn Sie Daten an Azure senden möchten. Bei der aktuellen Architektur von Azure Backup Server enthält der Azure Backup-Tresor die *zweite* Kopie der Daten, und der lokale Speicher enthält die erste (obligatorische) Sicherungskopie.
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Installieren und Aktualisieren des Data Protection Manager-Schutz-Agents

MABS verwendet den System Center Data Protection Manager-Schutz-Agent. Die Schritte zum Installieren des Schutz-Agents auf Ihren Schutzservern werden [hier](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-1807) aufgeführt.

In den folgenden Abschnitten wird beschrieben, wie Schutz-Agents für Clientcomputer aktualisiert werden.

1. Wählen Sie in der Backup Server-Administratorkonsole **Verwaltung** > **Agents** aus.

2. Wählen Sie im Anzeigebereich die Clientcomputer aus, für die Sie den Schutz-Agent aktualisieren möchten.

   > [!NOTE]
   > Die Spalte **Agent-Updates** gibt an, wann ein Update des Schutz-Agents für jeden geschützten Computer verfügbar ist. Im Bereich **Aktionen** ist die Aktion **Aktualisieren** nur verfügbar, wenn ein geschützter Computer ausgewählt ist und Updates verfügbar sind.
   >
   >

3. Zum Installieren aktualisierter Schutz-Agents auf den ausgewählten Computern wählen Sie im Bereich **Aktionen** den Befehl **Aktualisieren** aus.

4. Für einen Clientcomputer, der nicht mit dem Netzwerk verbunden ist, enthält die Spalte **Agent-Status** so lange den Status **Update ausstehend**, bis der Computer mit dem Netzwerk verbunden wird.

   Sobald ein Clientcomputer mit dem Netzwerk verbunden ist, enthält die Spalte **Agent-Updates** für den Clientcomputer den Status **Wird aktualisiert**.

## <a name="move-mabs-to-a-new-server"></a>Verschieben von MABS auf einen neuen Server

Im Folgenden werden die Schritte aufgeführt, wenn Sie MABS auf einen neuen Server verschieben und gleichzeitig den Speicher beibehalten müssen. Dies kann nur dann erreicht werden, wenn sich alle Daten in Modern Backup Storage befinden.


  > [!IMPORTANT]
  > - Der neue Servername muss den gleichen Namen wie für die ursprüngliche Azure Backup Server-Instanz aufweisen. Der Name der neuen Azure Backup Server-Instanz kann nicht geändert werden, wenn Sie die Wiederherstellungspunkte über den vorherigen Speicherpool und der Data Protection Manager-Datenbank speichern möchten.
  > - Sie müssen über eine Sicherung der Data Protection Manager-Datenbank verfügen. Sie müssen die Datenbank wiederherstellen.

1. Wählen Sie im Anzeigebereich die Clientcomputer aus, für die Sie den Schutz-Agent aktualisieren möchten.
2. Fahren Sie den ursprünglichen Azure Backup-Server herunter, oder stellen Sie ihn außer Dienst.
3. Setzen Sie das Computerkonto in Active Directory zurück.
4. Installieren Sie Server 2016 auf dem neuen Computer, und vergeben Sie den gleichen Namen wie für den ursprünglichen Azure Backup-Server.
5. Führen Sie den Domänenbeitritt durch.
6. Installieren Sie Azure Backup Server v2 oder höher. (Verschieben Sie den DPM-Speicherpool vom alten Server, und führen Sie den Import durch.)
7. Stellen Sie die DPMDB aus Schritt 1 wieder her.
8. Fügen Sie den Speicher vom ursprünglichen Sicherungsserver an den neuen Server an.
9. Stellen Sie die DPMDB über SQL wieder her.
10. Wechseln Sie in der Administrator-Befehlszeile auf dem neuen Server per „cd“ zum Installationsspeicherort von Microsoft Azure Backup und in den Ordner „bin“.

    Pfadbeispiel: C:\windows\system32>cd "c:\Programme\Microsoft Azure Backup\DPM\DPM\bin\" 

11. Zum Sichern von Azure führen Sie „DPMSYNC -SYNC“ aus.

    Wenn Sie dem DPM-Speicherpool NEUE Datenträger hinzugefügt haben, anstatt alte zu verschieben, müssen Sie Folgendes ausführen: „DPMSYNC -Reallocatereplica“.

## <a name="network-connectivity"></a>Netzwerkverbindung
Azure Backup Server muss mit dem Azure Backup-Dienst verbunden sein, um erfolgreich ausgeführt werden zu können. Verwenden Sie zum Überprüfen, ob der Computer über eine Verbindung mit Azure verfügt, das Cmdlet ```Get-DPMCloudConnection``` in der Azure Backup Server-PowerShell-Konsole. Wenn die Ausgabe des Cmdlets „TRUE“ lautet, besteht eine Verbindung, andernfalls nicht.

Gleichzeitig muss das Azure-Abonnement einen fehlerfreien Zustand aufweisen. Um den Status Ihres Abonnements zu ermitteln und es zu verwalten, melden Sie sich beim [Abonnementportal](https://account.windowsazure.com/Subscriptions)an.

Nachdem Sie den Status der Azure-Verbindung und des Azure-Abonnements kennen, können Sie anhand der Tabelle unten ermitteln, welche Auswirkungen mit einer Sicherungs-/Wiederherstellungsfunktion verbunden sind.

| Verbindungszustand | Azure-Abonnement | Sicherung in Azure | Sicherung auf Datenträger | Wiederherstellung von Azure | Wiederherstellung von einem Datenträger |
| --- | --- | --- | --- | --- | --- |
| Verbunden |Aktiv |Zulässig |Zulässig |Zulässig |Zulässig |
| Verbunden |Abgelaufen |Beendet |Beendet |Zulässig |Zulässig |
| Verbunden |Bereitstellung aufgehoben |Beendet |Beendet |Beendet und Azure-Wiederherstellungspunkte gelöscht |Beendet |
| Verbindung vor mehr als 15 Tagen verloren |Aktiv |Beendet |Beendet |Zulässig |Zulässig |
| Verbindung vor mehr als 15 Tagen verloren |Abgelaufen |Beendet |Beendet |Zulässig |Zulässig |
| Verbindung vor mehr als 15 Tagen verloren |Bereitstellung aufgehoben |Beendet |Beendet |Beendet und Azure-Wiederherstellungspunkte gelöscht |Beendet |

### <a name="recovering-from-loss-of-connectivity"></a>Wiederherstellung nach Verbindungsverlust
Wenn Sie über eine Firewall oder einen Proxy verfügen, die bzw. der den Zugriff auf Azure verhindert, müssen Sie im Profil der Firewall bzw. des Proxys die folgenden Domänenadressen auf die Positivliste setzen:

* `http://www.msftncsi.com/ncsi.txt`
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

Nach dem Wiederherstellen der Verbindung mit Azure für den Azure Backup Server-Computer wird anhand des Azure-Abonnementstatus ermittelt, welche Vorgänge durchgeführt werden können. Die obige Tabelle enthält Details zu den Vorgängen, die nach dem „Verbinden“ des Computers zulässig sind.

### <a name="handling-subscription-states"></a>Behandeln von Abonnementstatus
Es ist möglich, den Status eines Azure-Abonnements von *Abgelaufen* oder *Bereitstellung aufgehoben* in *Aktiv* zu ändern. Dies ist aber mit Auswirkungen auf das Produktverhalten verbunden, solange der Status nicht *Aktiv*lautet:

* Ein Abonnement mit dem Status *Bereitstellung aufgehoben* verliert für den Zeitraum der Aufhebung die Funktionalität. Beim Festlegen auf *Aktiv*wird die Produktfunktionalität für Sicherung/Wiederherstellung wieder aktiviert. Die Sicherungsdaten auf der lokalen Festplatte können auch abgerufen werden, sofern sie mit einer ausreichend langen Beibehaltungsdauer versehen sind. Die Sicherungsdaten in Azure gehen aber unwiederbringlich verloren, wenn das Abonnement in den Status *Bereitstellung aufgehoben* versetzt wird.
* Für ein Abonnement mit dem Status *Abgelaufen* geht die Funktionalität nur so lange verloren, bis es wieder in den Status *Aktiv* versetzt wird. Alle Sicherungen für den Zeitraum, in dem sich das Abonnement im Status *Abgelaufen* befindet, werden nicht ausgeführt.

## <a name="upgrade-mabs"></a>Durchführen eines Upgrades von MABS
Verwenden Sie die folgenden Verfahren, um ein Upgrade für MABS durchzuführen.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Durchführen eines Upgrades von MABS v2 auf v3

> [!NOTE]
> 
> MABS v2 ist für die Installation von MABS v3 keine Voraussetzung. Allerdings können Sie nur ein Upgrade von MABS v2 auf MABS v3 durchführen.

Führen Sie die folgenden Schritte aus, um ein Upgrade für MABS durchzuführen:

1. Um ein Upgrade von MABS v2 auf MABS v3 durchzuführen, führen Sie bei Bedarf ein Upgrade für Ihr Betriebssystem auf Windows Server 2016 oder Windows Server 2019 durch.

2. Führen Sie ein Upgrade für Ihren Server durch. Die Schritte sind vergleichbar mit den Schritten zur [Installation](#install-and-upgrade-azure-backup-server). Für die SQL-Einstellungen besteht jedoch die Option, ein Upgrade für Ihre SQL-Instanz auf SQL 2017 oder die eigene Instanz von SQL Server 2017 zu verwenden.

   > [!NOTE]
   > 
   > Beenden Sie den Vorgang nicht, während das Upgrade für die SQL-Instanz durchgeführt wird, da die SQL-Berichtsinstanz durch eine Beendigung deinstalliert wird und bei dem Versuch, erneut ein Upgrade für MABS durchzuführen, ein Fehler auftritt.

   Wichtige Hinweise:

   > [!IMPORTANT]
   > 
   >  Bei dem SQL 2017-Upgrade werden die SQL-Verschlüsselungsschlüssel gesichert und die Reporting Services deinstalliert. Nach dem Upgrade von SQL Server werden die Reporting Services (14.0.6827.4788) installiert und die Verschlüsselungsschlüssel werden wiederhergestellt.
   > 
   > Informationen zur manuellen Konfiguration von SQL 2017 finden Sie in den Installationsanweisungen im Abschnitt *SSRS-Konfiguration mit SQL 2017*.

3. Aktualisieren Sie die Schutz-Agents auf den geschützten Servern.
4. Sicherungen sollten fortgesetzt werden, ohne dass Ihre Produktionsserver neugestartet werden müssen.
5. Nun können Sie mit der Konfiguration von Schutzmaßnahmen für Ihre Daten beginnen. Wenn Sie bei bestehendem Schutz ein Upgrade auf Modern Backup Storage durchführen, können Sie auch die Volumes auswählen, in denen die Sicherungen gespeichert werden sollen und auf unzureichend bereitgestellten Speicher prüfen. [Weitere Informationen](backup-mabs-add-storage.md)

> [!NOTE]
> 
> Falls Sie ein Upgrade von MABS v1 auf v2 durchführen, stellen Sie sicher, dass Ihr Betriebssystem Windows Server 2016 oder Windows Server 2012 R2 ist. Um neue System Center 2016 Data Protection Manager-Features wie Modern Backup Storage nutzen zu können, müssen Sie Backup Server v2 unter Windows Server 2016 installieren. Machen Sie sich vor der Installation von oder einem Upgrade auf Backup Server v2 mit den für MABS geltenden [Installationsvoraussetzungen](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-1807#setup-prerequisites) vertraut.

## <a name="troubleshooting"></a>Problembehandlung
Wenn auf dem Microsoft Azure Backup-Server während der Installationsphase (oder bei der Sicherung oder der Wiederherstellung) Fehler auftreten, verwenden Sie dieses [Dokument mit Fehlercodes](https://support.microsoft.com/kb/3041338), um weitere Informationen zu erhalten.
Sie können auch [Azure Backup – Häufig gestellte Fragen](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Nächste Schritte
Ausführliche Informationen zum [Vorbereiten der Umgebung für DPM](https://technet.microsoft.com/library/hh758176.aspx) finden Sie auf der Microsoft TechNet-Website. Sie finden dort auch Informationen zu den unterstützten Konfigurationen, unter denen Azure Backup Server bereitgestellt und verwendet werden kann. Sie können eine Reihe von [PowerShell-Cmdlets](https://docs.microsoft.com/powershell/module/dataprotectionmanager/?view=systemcenter-ps-2016) für verschiedene Vorgänge verwenden.

In den folgenden Artikeln finden Sie zusätzliche Informationen zum Workloadschutz mit einem Microsoft Azure Backup-Server.

* [SQL Server-Sicherung](backup-azure-backup-sql.md)
* [SharePoint Server-Sicherung](backup-azure-backup-sharepoint.md)
* [Sicherung eines anderen Servers](backup-azure-alternate-dpm-server.md)

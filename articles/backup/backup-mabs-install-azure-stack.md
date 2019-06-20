---
title: Installieren von Azure Backup Server in Azure Stack | Microsoft-Dokumentation
description: Verwenden Sie Azure Backup Server, um Workloads in Azure Stack zu schützen oder zu sichern.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: raynew
ms.openlocfilehash: d3a2ffdedda7f541fb1a3f37a8b40bc7af3dcb57
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60851859"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Installieren von Azure Backup Server in Azure Stack

In diesem Artikel wird das Installieren von Azure Backup Server in Azure Stack erläutert. Mit Azure Backup Server können Sie IaaS-Workloads (Infrastructure as a Service) wie z.B. in Azure Stack ausgeführte virtuelle Computer schützen. Ein Vorteil der Verwendung von Azure Backup Server zum Schutz Ihrer Workloads ist, dass Sie den gesamten Workloadschutz über eine einzige zentrale Konsole verwalten können.

> [!NOTE]
> Weitere Informationen zu den Sicherheitsfunktionen finden Sie unter [Sicherheitsfeatures für den Schutz von Hybridsicherungen mit Azure Backup](backup-azure-security-feature.md).
>

## <a name="azure-backup-server-protection-matrix"></a>Azure Backup Server-Schutzmatrix
Azure Backup Server schützt die folgenden Workloads für virtuelle Azure Stack-Computer.

| Geschützte Datenquelle | Schutz und Wiederherstellung |
| --------------------- | ----------------------- |
| Windows Server – Halbjährlicher Kanal – Datacenter/Enterprise/Standard | Volumes, Dateien, Ordner |
| Windows Server 2016 – Datacenter/Enterprise/Standard | Volumes, Dateien, Ordner |
| Windows Server 2012 R2 – Datacenter/Enterprise/Standard | Volumes, Dateien, Ordner |
| Windows Server 2012 – Datacenter/Enterprise/Standard | Volumes, Dateien, Ordner |
| Windows Server 2008 R2 – Datacenter/Enterprise/Standard | Volumes, Dateien, Ordner |
| SQL Server 2016 | Datenbank |
| SQL Server 2014 | Datenbank |
| SQL Server 2012 SP1 | Datenbank |
| SharePoint 2016 | Farm, Datenbank, Front-End, Webserver |
| SharePoint 2013 | Farm, Datenbank, Front-End, Webserver |
| SharePoint 2010 | Farm, Datenbank, Front-End, Webserver |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Voraussetzungen für die Azure Backup Server-Umgebung

Berücksichtigen Sie die Empfehlungen in diesem Abschnitt bei der Installation von Azure Backup Server in Ihrer Azure Stack-Umgebung. Das Azure Backup Server-Installationsprogramm prüft, ob Ihre Umgebung die notwendigen Voraussetzungen erfüllt. Allerdings sparen Sie Zeit, wenn Sie die Installation vorbereiten.

### <a name="determining-size-of-virtual-machine"></a>Bestimmen der Größe des virtuellen Computers
Verwenden Sie die Größe A2 oder höher, um Azure Backup Server auf einem virtuellen Azure Stack-Computer auszuführen. Laden Sie als Hilfe bei der Auswahl einer VM-Größe den [Rechner für die Azure Stack-VM-Größe](https://www.microsoft.com/download/details.aspx?id=56832) herunter.

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Virtuelle Netzwerke auf virtuellen Azure Stack-Computern
Alle virtuellen Computer, die in einer Azure Stack-Workload verwendet werden, müssen demselben virtuellen Azure-Netzwerk und Azure-Abonnement angehören.

### <a name="azure-backup-server-vm-performance"></a>Azure Backup Server-VM-Leistung
Bei einer gemeinsamen Nutzung mit anderen virtuellen Computern können sich die Speicherkontogröße und IOPS-Grenzwerte auf die Leistung der Azure Backup Server-VM auswirken. Aus diesem Grund sollten Sie ein separates Speicherkonto für den virtuellen Azure Backup Server-Computer verwenden. Der Azure Backup-Agent, der auf dem Azure Backup Server ausgeführt wird, benötigt temporären Speicher für folgende Zwecke:
- Eigene Nutzung (Cachespeicherort)
- Aus der Cloud wiederhergestellte Daten (lokaler Stagingbereich)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Konfigurieren des temporären Azure Backup-Speichers
Jeder virtuelle Azure Stack-Computer verfügt über temporären Datenträgerspeicher, der für den Benutzer als Volume `D:\` verfügbar ist. Der von Azure Backup benötigte lokale Stagingbereich kann so konfiguriert werden, dass er sich auf `D:\` befindet, und der Cachespeicherort kann auf `C:\` angeordnet werden. Auf diese Weise muss kein Speicher der Datenträger für Daten in Anspruch genommen werden, die an den virtuellen Azure Backup Server-Computer angefügt sind.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Speichern von Sicherungsdaten auf einem lokalen Datenträger in Azure
Azure Backup Server speichert Sicherungsdaten auf Azure-Datenträgern, die an den virtuellen Computer angefügt sind, um die Wiederherstellung des Betriebs zu ermöglichen. Nachdem die Datenträger und der Speicherplatz an den virtuellen Computer angefügt wurden, wird der Speicher für Sie von Azure Backup Server verwaltet. Die Menge an Sicherungsdatenspeicher richtet sich nach der Anzahl und Größe von Datenträgern, die an die einzelnen [virtuellen Azure Stack-Computer](/azure-stack/user/azure-stack-storage-overview) angefügt sind. Jede Azure Stack-VM-Größe verfügt über eine maximale Anzahl von Datenträgern, die an den virtuellen Computer angefügt werden kann. Beispielsweise umfasst A2 vier Datenträger. A3 umfasst acht Datenträger. A4 umfasst 16 Datenträger. Auch hier bestimmt die Größe und Anzahl von Datenträgern die Gesamtgröße des Sicherungsspeicherpools.

> [!IMPORTANT]
> Sie sollten Sicherungsdaten für die Wiederherstellung des Betriebs **nicht** länger als fünf Tage auf Datenträgern aufbewahren, die an Azure Backup Server angefügt sind.
>

Durch das Speichern von Sicherungsdaten in Azure wird die Sicherungsinfrastruktur in Azure Stack reduziert. Wenn Daten mehr als fünf Tage alt sind, sollten sie in Azure gespeichert werden.

Erstellen bzw. verwenden Sie einen Recovery Services-Tresor, um Sicherungsdaten in Azure zu speichern. Beim Vorbereiten einer Sicherung der Azure Backup Server-Workload [konfigurieren Sie den Recovery Services-Tresor](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). Nach der Konfiguration wird bei jeder Ausführung eines Sicherungsauftrags im Tresor ein Wiederherstellungspunkt erstellt. Jeder Recovery Services-Tresor enthält bis zu 9999 Wiederherstellungspunkte. Je nach Anzahl von erstellten Wiederherstellungspunkten und Dauer ihrer Aufbewahrung können Sie Sicherungsdaten viele Jahre lang vorhalten. Beispielsweise können Sie monatliche Wiederherstellungspunkte erstellen und fünf Jahre lang aufbewahren.
 
### <a name="scaling-deployment"></a>Skalieren der Bereitstellung
Wenn Sie Ihre Bereitstellung skalieren möchten, haben Sie die folgenden Optionen:
  - Zentral hochskalieren: Erhöhen Sie die Größe des virtuellen Azure Backup Server-Computers von der A-Serie auf die D-Serie, und erhöhen Sie den lokalen Speicher [gemäß der Anleitung zum virtuellen Azure Stack-Computer](/azure-stack/user/azure-stack-manage-vm-disks).
  - Daten auslagern: Senden Sie ältere Daten an Azure, und behalten Sie nur die neuesten Daten in dem Speicher bei, der dem Azure Backup Server angefügt ist.
  - Horizontal hochskalieren: Fügen Sie weitere Azure Backup Server-Instanzen hinzu, um die Workloads zu schützen.

### <a name="net-framework"></a>.NET Framework

.NET Framework 3.5 SP1 oder höher muss auf dem virtuellen Computer installiert sein.

### <a name="joining-a-domain"></a>Beitreten zu einer Domäne

Die Azure Backup Server-VM muss einer Domäne angehören. Ein Domänenbenutzer mit Administratorrechten muss Azure Backup Server auf dem virtuellen Computer installieren.

## <a name="using-an-iaas-vm-in-azure-stack"></a>Verwenden einer IaaS-VM in Azure Stack

Beim Wählen eines Servers für Azure Backup Server starten Sie mit einem Katalogimage von Windows Server 2012 R2 Datacenter oder Windows Server 2016 Datacenter. Der Artikel [Erstellen Ihres ersten virtuellen Windows-Computers im Azure-Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) enthält ein Tutorial zu den ersten Schritten mit dem empfohlenen virtuellen Computer. Die empfohlenen Mindestanforderungen für den virtuellen Servercomputer (VM) lauten: A2-Standard mit zwei Kernen und 3,5 GB RAM.

Beim Schützen von Workloads mit Azure Backup Server sind viele Feinheiten zu beachten. Dies wird im Artikel [Installieren von DPM als virtuellen Azure-Computer](https://technet.microsoft.com/library/jj852163.aspx)beschrieben. Lesen Sie sich den Artikel ganz durch, bevor Sie den Computer bereitstellen.

> [!NOTE]
> Azure Backup Server ist für die Ausführung auf einer dedizierten VM konzipiert, die nur zu diesem Zweck verwendet wird. Azure Backup Server kann nicht auf folgenden Computern installiert werden:
> - Einem Computer, der als Domänencontroller ausgeführt wird
> - Einem Computer, auf dem die Anwendungsserverrolle installiert ist
> - Einem Computer, auf dem Exchange Server ausgeführt wird
> - Einem Computer, der einen Knoten eines Clusters darstellt

Binden Sie Azure Backup Server immer in eine Domäne ein. Wenn Sie Azure Backup Server in eine andere Domäne verschieben müssen, installieren Sie zuerst Azure Backup Server, und binden Sie dann die zugehörige VM in die neue Domäne ein. Nachdem Sie Azure Backup Server bereitgestellt haben, können Sie die Anwendung nicht in eine neue Domäne verschieben.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Festlegen der Speicherreplikation

Die Option zur Replikation des Recovery Services-Tresorspeichers ermöglicht Ihnen, zwischen georedundantem und lokal redundantem Speicher zu wählen. Recovery Services-Tresore verwenden standardmäßig georedundanten Speicher. Behalten Sie den georedundanten Speicher bei, wenn es sich hierbei um Ihren primären Tresor handelt. Wählen Sie lokal redundanten Speicher, wenn Sie eine kostengünstigere und weniger langlebige Option wünschen. Weitere Informationen zu den Optionen für [georedundanten](../storage/common/storage-redundancy-grs.md) und [lokal redundanten](../storage/common/storage-redundancy-lrs.md) Speicher finden Sie in der [Übersicht über die Azure Storage-Replikation](../storage/common/storage-redundancy.md).

So bearbeiten Sie die Einstellung für die Speicherreplikation:

1. Wählen Sie Ihren Tresor aus, um das Tresordashboard und das Menü „Einstellungen“ zu öffnen. Sollte das Menü **Einstellungen** nicht geöffnet werden, klicken Sie im Tresordashboard auf **Alle Einstellungen**.
2. Klicken Sie im Menü **Einstellungen** auf **Sicherungsinfrastruktur** > **Sicherungskonfiguration**, um das Menü **Sicherungskonfiguration** zu öffnen. Wählen Sie im Menü **Speicherkonfiguration** die Speicherreplikationsoption für Ihren Tresor aus.

    ![Liste der Sicherungstresore](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Herunterladen des Azure Backup Server-Installationsprogramms

Es gibt zwei Möglichkeiten zum Herunterladen des Azure Backup Server-Installationsprogramms. Sie können es aus dem [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55269) herunterladen. Sie können das Installationsprogramm auch beim Konfigurieren eines Recovery Services-Tresors herunterladen. Die folgenden Schritte begleiten Sie beim Herunterladen des Installationsprogramms aus dem Azure-Portal, während Sie einen Recovery Services-Tresor konfigurieren.

1. [Melden Sie sich auf Ihrer Azure Stack-VM im Azure-Portal bei Ihrem Azure-Abonnement an](https://portal.azure.com/).
2. Klicken Sie im linken Menü auf **Alle Dienste**.

    ![Auswählen der Option „Alle Dienste“ im Hauptmenü](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. Geben Sie *Recovery Services*  im Dialogfeld **Alle Dienste** ein. Sobald Sie mit der Eingabe beginnen, wird die Ressourcenliste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Recovery Services-Tresore** aus, wenn der Eintrag angezeigt wird.

    ![Eingeben von Recovery Services im Dialogfeld „Alle Dienste“](./media/backup-mabs-install-azure-stack/all-services.png)

    Die Liste mit den Recovery Services-Tresoren im Abonnement wird angezeigt.

4. Wählen Sie in der Liste mit den Recovery Services-Tresoren Ihren Tresor aus, um dessen Dashboard zu öffnen.

    ![Eingeben von „Recovery Services“ im Dialogfeld „Alle Dienste“](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. Klicken Sie im Menü „Erste Schritte“ des Tresors auf **Sicherung**, um den Assistenten für erste Schritte zu öffnen.

    ![Erste Schritte zur Sicherung](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    Das Menü „Sicherung“ wird geöffnet.

    ![Backup-goals-default-opened](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. Wählen Sie im Menü „Sicherung“ auf dem Blatt **Wo wird Ihre Workload ausgeführt?** die Option **Lokal** aus. Wählen Sie im Dropdownmenü **Was möchten Sie sichern?** die Workloads aus, die Sie mithilfe von Azure Backup Server schützen möchten. Wenn Sie nicht sicher sind, welche Workloads Sie auswählen sollen, wählen Sie **Virtuelle Hyper-V-Computer**, und klicken Sie dann auf **Infrastruktur vorbereiten**.

    ![lokal und Workloads als Ziele](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    Das Menü **Infrastruktur vorbereiten** wird geöffnet.

7. Klicken Sie im Menü **Infrastruktur vorbereiten** auf **Herunterladen**, um eine Webseite zum Herunterladen von Azure Backup Server-Installationsdateien zu öffnen.

    ![Ändern des Erste-Schritte-Assistenten](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    Die Microsoft-Webseite, die die herunterladbaren Dateien für Azure Backup Server enthält, wird geöffnet.

8. Wählen Sie auf der Seite zum Herunterladen von Microsoft Azure Backup Server eine Sprache aus, und klicken Sie auf **Herunterladen**.

    ![Das Download Center wird geöffnet.](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. Das Azure Backup Server-Installationsprogramm besteht aus acht Dateien: einem Installationsprogramm und sieben BIN-Dateien. Versehen Sie **Dateiname** mit einem Häkchen, um alle erforderlichen Dateien auszuwählen, und klicken Sie auf **Weiter**. Laden Sie alle Dateien in den gleichen Ordner herunter.

    ![Download Center 1](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    Die Downloadgröße aller Installationsdateien beträgt mehr als 3 GB. Über einen Downloadlink mit 10 MBit/s kann das Herunterladen aller Installationsdateien bis zu 60 Minuten dauern. Die Dateien werden an den angegebenen Speicherort heruntergeladen.

## <a name="extract-azure-backup-server-install-files"></a>Extrahieren der Azure Backup Server-Installationsdateien

Nachdem Sie alle Dateien auf Ihre Azure Stack-VM heruntergeladen haben, wechseln Sie zum Speicherort des Downloads. In der ersten Phase der Installation von Azure Backup Server werden die Dateien extrahiert.

![Download Center 1](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. Klicken Sie zum Starten der Installation in der Liste der heruntergeladenen Dateien auf **MicrosoftAzureBackupserverInstaller.exe**.

    > [!WARNING]
    > Zum Extrahieren der Setupdateien sind mindestens 4 GB freier Speicherplatz erforderlich.
    >

2. Klicken Sie im Azure Backup Server-Assistenten auf **Weiter**, um den Vorgang fortzusetzen.

    ![Setup-Assistent von Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Wählen Sie den Pfad für die Azure Backup Server-Dateien, und klicken Sie auf **Weiter**.

   ![Setup-Assistent von Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Überprüfen Sie den Speicherort für die Extraktion, und klicken Sie auf **Extrahieren**.

   ![Setup-Assistent von Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. Der Assistent extrahiert die Dateien und bereitet den Installationsvorgang vor.

   ![Setup-Assistent von Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. Klicken Sie nach Abschluss des Extrahierungsvorgangs auf **Fertig stellen**. Standardmäßig ist **Execute setup.exe** ausgewählt. Wenn Sie auf **Fertig stellen** klicken, wird Microsoft Azure Backup Server mithilfe von „Setup.exe“ am angegebenen Speicherort installiert.

   ![Setup-Assistent von Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Installieren des Softwarepakets

Im vorherigen Schritt haben Sie auf **Fertig stellen** geklickt, um die Extrahierungsphase zu beenden und den Setup-Assistenten für Azure Backup Server zu starten.

![Setup-Assistent von Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Azure Backup Server und Data Protection Manager teilen sich Code. Deshalb finden Sie im Installationsprogramm von Azure Backup Server Verweise auf Data Protection Manager und DPM. Azure Backup Server und Data Protection Manager sind zwar getrennte Produkte, aber eng miteinander verknüpft.

1. Klicken Sie auf **Microsoft Azure Backup Server**, um den Setup-Assistenten zu starten.

   ![Setup-Assistent von Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. Klicken Sie auf dem Bildschirm **Willkommen** auf **Weiter**.

    ![Azure Backup Server – Willkommen und Voraussetzungsüberprüfung](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. Klicken Sie auf dem Bildschirm **Überprüfungen der Voraussetzungen** auf **Überprüfen**, um zu ermitteln, ob die Hardware- und Softwarevoraussetzungen für Azure Backup Server erfüllt sind.

    ![Azure Backup Server – Willkommen und Voraussetzungsüberprüfung](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Wenn Ihre Umgebung die Voraussetzungen erfüllt, sehen Sie eine Meldung mit dem Hinweis, dass der Computer die Anforderungen erfüllt. Klicken Sie auf **Weiter**.  

    ![Azure Backup Server – Überprüfung der Voraussetzungen bestanden](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Wenn Ihre Umgebung nicht die Voraussetzungen erfüllt, werden die Probleme präzisiert. Die nicht erfüllten Voraussetzungen sind zudem in der Datei „DpmSetup.log“ zu finden. Beheben Sie die Fehler, die das Erfüllen der Voraussetzungen verhindern, und führen Sie dann **Erneut überprüfen** aus. Die Installation kann erst fortgesetzt werden, sobald alle Voraussetzungen erfüllt sind.

    ![Azure Backup Server – Voraussetzungen für die Installation nicht erfüllt](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. Für Microsoft Azure Backup Server ist SQL Server erforderlich. Zum Azure Backup Server-Installationspaket gehören die passenden SQL Server-Binärdateien. Sie können nach Wunsch Ihre eigene SQL Server-Installation verwenden. Allerdings wird empfohlen, das Installationsprogramm eine neue Instanz von SQL Server hinzufügen zu lassen. Um sicherzustellen, dass Ihre Wahl mit Ihrer Umgebung funktioniert, klicken Sie auf **Überprüfen und installieren**.

   > [!NOTE]
   > Azure Backup Server funktioniert nicht mit einer Remoteinstanz von SQL Server. Die von Azure Backup Server verwendete Instanz muss lokal sein.
   >

    ![Azure Backup Server – Willkommen und Voraussetzungsüberprüfung](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Nachdem Sie geprüft haben, ob die VM die Voraussetzungen für die Installation von Azure Backup Server erfüllt, klicken Sie auf **Weiter**.

    ![Azure Backup Server – Willkommen und Voraussetzungsüberprüfung](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Wenn ein Fehler mit der Empfehlung auftritt, die VM neu zu starten, dann starten Sie die VM neu. Starten Sie nach dem Neustart der VM das Installationsprogramm neu. Wenn Sie zum Bildschirm **SQL Server-Einstellungen** gelangen, klicken Sie auf **Erneut überprüfen**.

5. Geben Sie in **Installationseinstellungen** einen Speicherort für die Installation der Microsoft Azure Backup Server-Dateien an, und klicken Sie auf **Weiter**.

    ![Voraussetzungen 2 für Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    Das Scratchverzeichnis ist eine Anforderung für die Sicherung in Azure. Stellen Sie sicher, dass die Größe des Scratchverzeichnisses mindestens 5 % der Datenmenge entspricht, die in Azure gesichert werden soll. Für den Datenträgerschutz müssen separate Datenträger nach Abschluss der Installation konfiguriert werden. Weitere Informationen zu Speicherpools finden Sie unter [Konfigurieren von Speicherpools und Datenträgerspeicher](https://technet.microsoft.com/library/hh758075.aspx).

6. Geben Sie auf dem Bildschirm **Sicherheitseinstellungen** ein sicheres Kennwort für eingeschränkte lokale Benutzerkonten an, und klicken Sie auf **Weiter**.

    ![Voraussetzungen 2 für Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. Bestimmen Sie auf dem Bildschirm **Microsoft Update-Abonnement**, ob Sie mit *Microsoft Update* nach Updates suchen möchten, und klicken Sie auf **Weiter**.

   > [!NOTE]
   > Es wird empfohlen, Windows Update an Microsoft Update umzuleiten, da Microsoft Update Sicherheit und wichtige Updates für Windows und andere Produkte wie Microsoft Azure Backup Server bietet.
   >

    ![Voraussetzungen 2 für Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Überprüfen Sie die *Zusammenfassung der Einstellungen* , und klicken Sie auf **Installieren**.

    ![Voraussetzungen 2 für Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    Wenn die Installation von Azure Backup Server abgeschlossen ist, startet das Installationsprogramm sofort das Installationsprogramm des Microsoft Azure Recovery Services-Agents.

9. Das Installationsprogramm des Microsoft Azure Recovery Services-Agents wird geöffnet und prüft, ob eine Internetverbindung besteht. Falls ja, fahren Sie mit der Installation fort. Falls nein, geben Sie die Proxydetails für die Verbindung mit dem Internet ein. Nachdem Sie Ihre Proxyeinstellungen angegeben haben, klicken Sie auf **Weiter**.

    ![Voraussetzungen 2 für Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. Klicken Sie zum Installieren des Microsoft Azure Recovery Services-Agents auf **Installieren**.

    ![Voraussetzungen 2 für Azure Backup Server](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    Der Microsoft Azure Recovery Services-Agent, auch Azure Backup-Agent genannt, konfiguriert Azure Backup Server für den Recovery Services-Tresor. Nach erfolgter Konfiguration sichert Azure Backup Server die Daten immer im selben Recovery Services-Tresor.

11. Nach der Installation des Microsoft Azure Recovery Services-Agents klicken Sie auf **Weiter**, um die nächste Phase zu starten: Registrierung von Azure Backup Server beim Recovery Services-Tresor.

    ![Voraussetzungen 2 für Azure Backup Server](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    Das Installationsprogramm startet den **Assistenten zum Registrieren von Servern**.

12. Wechseln Sie zu Ihrem Azure-Abonnement und Recovery Services-Tresor. Klicken Sie im Menü **Infrastruktur vorbereiten** auf **Herunterladen**, um Anmeldeinformationen für den Tresor herunterzuladen. Wenn die Schaltfläche **Herunterladen** in Schritt 2 nicht aktiv ist, wählen Sie **Bereits heruntergeladen oder die letzte Azure Backup Server-Installation verwenden**, um die Schaltfläche zu aktivieren. Die Anmeldeinformationen für den Tresor werden an den Speicherort heruntergeladen, an dem Sie Downloads speichern. Merken Sie sich diesen Speicherort, da Sie ihn für den nächsten Schritt benötigen.

    ![Voraussetzungen 2 für Azure Backup Server](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. Klicken Sie im Menü **Tresoridentifikation** auf **Durchsuchen**, um die Anmeldeinformationen für den Recovery Services-Tresor zu finden.

    ![Voraussetzungen 2 für Azure Backup Server](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    Navigieren Sie im Dialogfeld **Tresoranmeldeinformationen auswählen** zum Speicherort des Downloads, wählen Sie Ihre Anmeldeinformationen für den Tresor aus, und klicken Sie auf **Öffnen**.

    Der Pfad zu den Anmeldeinformationen wird im Menü „Tresoridentifikation“ angezeigt. Klicken Sie auf **Weiter**, um zur Verschlüsselungseinstellung zu gelangen.

14. Geben Sie im Dialogfeld **Verschlüsselungseinstellung** eine Passphrase für die Sicherungsverschlüsselung und einen Speicherort für die Passphrase ein. Klicken Sie anschließend auf **Weiter**.

    ![Voraussetzungen 2 für Azure Backup Server](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    Sie können Ihre eigene Passphrase angeben oder den Passphrasen-Generator verwenden, um eine erstellen zu lassen. Für die Passphrase sind allein Sie zuständig, denn Microsoft speichert oder verwaltet diese Passphrase nicht. Um sich auf eine Notfallsituation vorzubereiten, speichern Sie Ihre Passphrase an einem zugänglichen Ort.

    Nach Klicken auf **Weiter** wird Azure Backup Server beim Recovery Services-Tresor registriert. Das Installationsprogramm wird mit der Installation von SQL Server und Azure Backup Server fortgesetzt.

    ![Voraussetzungen 2 für Azure Backup Server](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. Wenn das Installationsprogramm abgeschlossen ist, zeigt der Status an, dass die Software erfolgreich installiert wurde.

    ![Voraussetzungen 2 für Azure Backup Server](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    Nach Abschluss der Installation werden die Symbole für die Azure Backup Server-Konsole und Azure Backup Server PowerShell auf dem Serverdesktop erstellt.

## <a name="add-backup-storage"></a>Hinzufügen von Backup Storage

Die erste Sicherungskopie wird in einem Speicherbereich vorgehalten, der dem Azure Backup Server-Computer zugeordnet ist. Weitere Informationen zum Hinzufügen von Datenträgern finden Sie unter [Hinzufügen von Speicher zu DPM 2016](https://docs.microsoft.com/system-center/dpm/add-storage?view=sc-dpm-1801).

> [!NOTE]
> Sie müssen auch dann Backup Storage hinzufügen, wenn Sie Daten an Azure senden möchten. In der Architektur von Azure Backup Server enthält der Recovery Services-Tresor die *zweite* Kopie der Daten, während der lokale Speicher die erste (obligatorische) Sicherungskopie enthält.
>
>

## <a name="network-connectivity"></a>Netzwerkverbindung

Azure Backup Server muss mit dem Azure Backup-Dienst verbunden sein, um erfolgreich ausgeführt werden zu können. Verwenden Sie zum Überprüfen, ob der Computer über eine Verbindung mit Azure verfügt, das Cmdlet ```Get-DPMCloudConnection``` in der Azure Backup Server-PowerShell-Konsole. Wenn die Ausgabe des Cmdlets „TRUE“ lautet, besteht eine Verbindung, andernfalls nicht.

Gleichzeitig muss das Azure-Abonnement einen fehlerfreien Zustand aufweisen. Um den Status Ihres Abonnements zu ermitteln und es zu verwalten, melden Sie sich beim [Abonnementportal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)an.

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

Wenn eine Firewall oder ein Proxy den Zugriff auf Azure verhindert, setzen Sie im Profil der Firewall bzw. des Proxys die folgenden Domänenadressen auf die Positivliste:

- `http://www.msftncsi.com/ncsi.txt`
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

Sobald die Verbindung mit Azure für den Computer mit Azure Backup Server wiederhergestellt ist, bestimmt der Azure-Abonnementzustand, welche Vorgänge ausgeführt werden können. Wenn für den Server **Verbunden** angezeigt wird, verwenden Sie die Tabelle in [Netzwerkkonnektivität](backup-mabs-install-azure-stack.md#network-connectivity), um die verfügbaren Vorgänge zu sichten.

### <a name="handling-subscription-states"></a>Behandeln von Abonnementstatus

Es ist möglich, den Zustand eines Azure-Abonnements von *Abgelaufen* oder *Bereitstellung aufgehoben* in *Aktiv* zu ändern. Wenn der Abonnementzustand nicht *Aktiv* ist:

- Wenn ein Abonnement den Zustand *Bereitstellung aufgehoben* hat, geht Funktionalität verloren. Durch Zurücksetzen des Abonnements auf *Aktiv* erhält es die Funktionalität zur Sicherung/Wiederherstellung zurück. Wenn Sicherungsdaten auf dem lokalen Datenträger mit einer ausreichend langen Aufbewahrungsfrist aufbewahrt wurden, können diese Sicherungsdaten wiederhergestellt werden. Sicherungsdaten in Azure gehen jedoch unwiederbringlich verloren, sobald das Abonnement in den Zustand *Bereitstellung aufgehoben* versetzt wird.
- Wenn ein Abonnement den Zustand *Abgelaufen* hat, geht Funktionalität verloren. Geplante Sicherungen werden nicht ausgeführt, wenn ein Abonnement den Zustand *Abgelaufen* hat.

## <a name="troubleshooting"></a>Problembehandlung

Wenn auf dem Computer mit Microsoft Azure Backup Server während des Setups (oder bei der Sicherung oder Wiederherstellung) Fehler auftreten, konsultieren Sie dieses [Dokument mit Fehlercodes](https://support.microsoft.com/kb/3041338).
Sie können auch [Azure Backup – Häufig gestellte Fragen](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Nächste Schritte

Der Artikel [Vorbereiten der Umgebung für DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1801) enthält Informationen zu unterstützten Azure Backup Server-Konfigurationen.

In den folgenden Artikeln finden Sie weiteführende Informationen zum Schutz von Workloads mit Microsoft Azure Backup Server.

- [SQL Server-Sicherung](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
- [SharePoint Server-Sicherung](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Sicherung eines anderen Servers](backup-azure-alternate-dpm-server.md)

---
title: Einrichten von Azure Backup Server für Azure VMware Solution
description: Richten Sie Ihre Azure VMware Solution-Umgebung so ein, dass virtuelle Computer über Azure Backup Server gesichert werden.
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: e9204b9f86c7e9ef67d2e3d6b45ccf4248d00b32
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581481"
---
# <a name="set-up-azure-backup-server-for-azure-vmware-solution"></a>Einrichten von Azure Backup Server für Azure VMware Solution

Azure Backup Server unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung (Business Continuity and Disaster Recovery, BCDR). Mit Azure VMware Solution können Sie nur eine Sicherung auf VM-Ebene unter Verwendung von Azure Backup Server konfigurieren. 

Azure Backup Server kann Sicherungsdaten speichern auf:

- **Datenträger**: Für eine kurzfristige Speicherung sichert Azure Backup Server Daten in Datenträgerpools.
- **Azure**: Sowohl für eine kurzfristige als auch für eine langfristige externe Speicherung können Azure Backup Server-Daten, die in Datenträgerpools gespeichert sind, über Azure Backup in der Microsoft Azure-Cloud gesichert werden.

Verwenden Sie Azure Backup Server, um Daten am Quell- oder einem alternativen Speicherort wiederherzustellen. Auf diese Weise können Sie, wenn die Originaldaten aufgrund vorhersehbarer oder unerwarteter Probleme nicht verfügbar sind, Daten an einem alternativen Speicherort wiederherstellen.

In diesem Artikel erfahren Sie, wie Sie Ihre Azure VMware Solution-Umgebung so vorbereiten, dass VMs mithilfe von Azure Backup Server gesichert werden. Dazu führen Sie die folgenden Schritte aus: 

> [!div class="checklist"]
> * Bestimmen Sie den empfohlenen Typ und die empfohlene Größe des VM-Datenträgers.
> * Erstellen Sie einen Recovery Services-Tresor, in dem die Wiederherstellungspunkte gespeichert werden.
> * Legen der Speicherreplikation für den Recovery Services-Tresor fest.
> * Fügen Sie Speicher zu Azure Backup Server hinzu.

## <a name="supported-vmware-features"></a>Unterstützte VMware-Features

- **Sicherung ohne Agents:** Für Azure Backup Server ist es nicht erforderlich, einen Agent auf dem vCenter- oder ESXi-Server zu installieren, um die VM zu sichern. Stattdessen geben Sie einfach die IP-Adresse oder den vollqualifizierten Domänennamen (FQDN) und die Anmeldeinformationen an, mit denen der VMware-Server bei Azure Backup Server authentifiziert wird.
- **In die Cloud integrierte Sicherung:** Azure Backup Server schützt Workloads auf einem Datenträger und in der Cloud. Mit dem Sicherungs- und Wiederherstellungsworkflow von Azure Backup Server können Sie Langzeitaufbewahrung und externe Sicherungen verwalten.
- **Erkennen und Schützen von virtuellen Computern, die von vCenter verwaltet werden:** Azure Backup Server erkennt und schützt virtuelle Computer, die auf einem vCenter- oder ESXi-Server bereitgestellt sind. Azure Backup Server erkennt auch virtuelle Computer, die von vCenter verwaltet werden, sodass Sie große Bereitstellungen schützen können.
- **Automatischer Schutz auf Ordnerebene:** vCenter ermöglicht Ihnen, Ihre VMs in VM-Ordnern zu organisieren. Azure Backup Server erkennt diese Ordner. Sie können mit der Lösung VMs auf Ordnerebene einschließlich aller Unterordner schützen. Beim Schützen von Ordnern schützt Azure Backup Server die VMs in diesem Ordner und auch später hinzugefügte VMs. Azure Backup Server erkennt neue VMs täglich und schützt sie automatisch. Wenn Sie Ihre VMs in rekursiven Ordnern anordnen, erkennt und schützt Azure Backup Server automatisch die neuen VMs, die in den rekursiven Ordnern bereitgestellt werden.
- **Azure Backup Server schützt auch virtuell verschobene (vMotioned) VMs im Cluster:** Wenn VMs wegen Lastenausgleich im Cluster virtuell verschoben werden, wird der VM-Schutz automatisch von Azure Backup Server erkannt und fortgesetzt.
- **Schnelleres Wiederherstellen von erforderlichen Dateien:** Azure Backup Server kann Dateien oder Ordner von einer Windows-VM wiederherstellen, ohne die gesamte VM wiederherzustellen.

## <a name="limitations"></a>Einschränkungen

- Updaterollup 1 für Azure Backup Server v3 muss installiert sein.
- Es ist nicht möglich, Benutzermomentaufnahmen vor der ersten Azure Backup Server-Sicherung zu sichern. Nachdem Azure Backup Server die erste Sicherung abgeschlossen hat, können Sie Benutzermomentaufnahmen sichern.
- Azure Backup Server kann keine VMware-VMs mit Pass-Through-Datenträgern und physischen Raw Device-Zuordnungen schützen.
- Azure Backup Server kann VMware vApps weder erkennen noch schützen.

Sie müssen die folgenden Schritte ausführen, um Azure Backup Server für Azure VMware Solution einzurichten:

- Richten Sie die erforderlichen Komponenten und die Umgebung ein.
- Erstellen Sie einen Recovery Services-Tresor.
- Laden Sie Azure Backup Server herunter, und installieren Sie diese Komponente.
- Fügen Sie Speicher zu Azure Backup Server hinzu.

### <a name="deployment-architecture"></a>Bereitstellungsarchitektur

Azure Backup Server wird als Azure-IaaS-VM (Infrastructure-as-a-Service) bereitgestellt, um Azure VMware Solution-VMs zu schützen.

:::image type="content" source="media/azure-vmware-solution-backup/deploy-backup-server-azure-vmware-solution-diagram.png" alt-text="Azure Backup Server wird als Azure-IaaS-VM (Infrastructure-as-a-Service) bereitgestellt, um Azure VMware Solution-VMs zu schützen." border="false":::

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Voraussetzungen für die Azure Backup Server-Umgebung

Berücksichtigen Sie die Empfehlungen in diesem Abschnitt, wenn Sie Azure Backup Server in Ihrer Azure-Umgebung installieren.

### <a name="azure-virtual-network"></a>Virtuelles Azure-Netzwerk

Sie müssen die Schritte zum [Konfigurieren des Netzwerks für Ihre private VMware-Cloud in Azure](tutorial-configure-networking.md) ausführen.

### <a name="determine-the-size-of-the-vm"></a>Bestimmen der Größe des virtuellen Computers

Folgen Sie den Anweisungen im Tutorial [Erstellen Ihrer ersten Windows-VM im Azure-Portal](../virtual-machines/windows/quick-create-portal.md).  Sie erstellen die VM im virtuellen Netzwerk, das Sie im vorherigen Schritt angelegt haben. Beginnen Sie mit einem Katalogimage von Windows Server 2019 Datacenter, um Azure Backup Server auszuführen. 

Die Tabelle bietet eine Übersicht über die maximale Anzahl geschützter Workloads für jede Größe von Azure Backup Server-VMs. Die Informationen basieren auf internen Leistungs- und Skalierungstests mit kanonischen Werten für die Workloadgröße und -änderung. Die tatsächliche Workload kann größer sein, sollte jedoch mit den Datenträgern bewältigt werden können, die der Azure Backup Server-VM zugeordnet sind.

| Maximale Anzahl geschützter Workloads | Durchschnittliche Workloadgröße | Durchschnittliche Workloadänderung (täglich) | Mindestanzahl Speicher-IOPS | Empfohlener Datenträger(Typ/Größe)      | Empfohlene VM-Größe |
|-------------------------|-----------------------|--------------------------------|------------------|-----------------------------------|---------------------|
| 20                      | 100 GB                | Nettoänderung 5 %                   | 2\.000             | HDD Standard (8 TB oder mehr pro Datenträger)  | A4V2       |
| 40                      | 150 GB                | Nettoänderung 10 %                  | 4\.500             | SSD Premium* (1 TB oder mehr pro Datenträger) | DS3_V2     |
| 60                      | 200 GB                | Nettoänderung 10 %                  | 10.500            | SSD Premium* (8 TB oder mehr pro Datenträger) | DS3_V2     |

*Um den erforderlichen IOPS-Wert zu erreichen, verwenden Sie Datenträger, die mindestens die jeweils empfohlene Größe haben. Kleinere Datenträger bieten niedrigere IOPS-Werte.

> [!NOTE]
> Azure Backup Server ist für die Ausführung auf einem dedizierten Server konzipiert, der nur zu einem Zweck verwendet wird. Es ist nicht möglich, Azure Backup Server auf einem Computer zu installieren, für den Folgendes zutrifft:
> * Er wird als Domänencontroller ausgeführt.
> * Er hat die Anwendungsserver-Rolle installiert.
> * Er ist ein System Center Operations Manager-Verwaltungsserver.
> * Auf ihm wird Exchange Server ausgeführt.
> * Er ist ein Knoten eines Clusters.

### <a name="disks-and-storage"></a>Datenträger und Speicher

Für die Installation von Azure Backup Server sind Datenträger erforderlich. 

| Anforderung                      | Empfohlene Größe  |
|----------------------------------|-------------------------|
| Azure Backup Server-Installation                | Installationsspeicherort: 3 GB<br />Laufwerk für Datenbankdateien: 900 MB<br />Systemlaufwerk: 1 GB für SQL Server-Installation<br /><br />Außerdem benötigen Sie Speicherplatz für Azure Backup Server, damit der Dateikatalog in einen temporären Installationsspeicherort kopiert werden kann, wenn Sie archivieren.      |
| Datenträger für Speicherpool<br />(Verwendet Basisvolumes, darf sich nicht auf einem dynamischen Datenträger befinden) | Die doppelte bis dreifache Größe der geschützten Daten.<br />Eine Möglichkeit zur ausführlichen Speicherberechnung finden Sie unter [SC 2016 DPM Capacity Planner](https://www.microsoft.com/download/details.aspx?id=54301).   |

Informationen dazu, wie einer vorhandenen Azure-VM ein neuer verwalteter Datenträger zugeordnet wird, finden Sie unter [Anfügen eines verwalteten Datenträgers an einen virtuellen Windows-Computer im Azure-Portal](../virtual-machines/windows/attach-managed-disk-portal.md).

> [!NOTE]
> Ein einzelner Azure Backup Server-Computer hat einen weichen Grenzwert von 120 TB für den Speicherpool.

### <a name="store-backup-data-on-local-disk-and-in-azure"></a>Speichern von Sicherungsdaten auf einem lokalen Datenträger und in Azure

Durch das Speichern von Sicherungsdaten in Azure wird die Sicherungsinfrastruktur auf der Azure Backup Server-VM verkleinert. Zur Wiederherstellung der Betriebsbereitschaft (Sicherung) speichert Azure Backup Server Sicherungsdaten auf Azure-Datenträgern, die der VM zugeordnet sind. Nachdem die Datenträger und der Speicherplatz an die VM angefügt (ihr zugeordnet) wurden, wird der Speicher für Sie von Azure Backup Server verwaltet. Die Speichermenge richtet sich nach Anzahl und Größe der Datenträger, die an die jeweilige Azure-VM angefügt sind. Für jede Größe einer Azure-VM gibt es eine maximale Anzahl von Datenträgern, die angefügt werden können. Für A2 sind beispielsweise vier Datenträger, für A3 acht Datenträger und für A4 16 Datenträger möglich. Auch hier bestimmen Größe und Anzahl der Datenträger die Gesamtkapazität des Sicherungsspeicherpools.

> [!IMPORTANT]
> Sie sollten Sicherungsdaten für die Wiederherstellung der Betriebsbereitschaft *nicht* länger als fünf Tage auf Datenträgern aufbewahren, die an Azure Backup Server angefügt sind. Wenn Daten älter sind als fünf Tage, speichern Sie sie in einem Recovery Services-Tresor.

Erstellen bzw. verwenden Sie einen Recovery Services-Tresor, um Sicherungsdaten in Azure zu speichern. Wenn Sie das Sichern der Azure Backup Server-Workload vorbereiten, [konfigurieren Sie den Recovery Services-Tresor](#create-a-recovery-services-vault). Nach Abschluss der Konfiguration wird jedes Mal, wenn ein Online-Sicherungsauftrag ausgeführt wird, im Tresor ein Wiederherstellungspunkt erstellt. Jeder Recovery Services-Tresor enthält bis zu 9.999 Wiederherstellungspunkte. Abhängig von der Anzahl der erstellten Wiederherstellungspunkte und der Aufbewahrungsdauer können Sie Sicherungsdaten viele Jahre aufbewahren. Beispielsweise können Sie monatliche Wiederherstellungspunkte erstellen und diese fünf Jahre aufbewahren.

> [!IMPORTANT]
> Unabhängig davon, ob Sie Sicherungsdaten an Azure senden oder lokal speichern, müssen Sie Azure Backup Server bei einem Recovery Services-Tresor registrieren.

### <a name="scale-deployment"></a>Skalieren einer Bereitstellung

Wenn Sie Ihre Bereitstellung skalieren möchten, haben Sie die folgenden Optionen:

- **Hochskalieren**: Erhöhen Sie die Größe der Azure Backup Server-VM aus der A-Serie in die DS3-Serie, und vergrößern Sie den lokalen Speicher.
- **Auslagern von Daten**: Senden Sie ältere Daten an Azure, und behalten Sie nur die neuesten Daten in dem Speicher bei, der der Azure Backup Server-VM angefügt ist.
- **Aufskalieren:** Fügen Sie weitere Azure Backup Server-Computer hinzu, um die Workloads zu schützen.

### <a name="net-framework"></a>.NET Framework

Auf der VM muss .NET Framework 3.5 SP1 oder höher installiert sein.

### <a name="join-a-domain"></a>Einbinden in eine Domäne

Die Azure Backup Server-VM muss einer Domäne angehören. Ein Domänenbenutzer mit Administratorrechten auf der VM muss Azure Backup Server installieren.

Eine auf einer Azure-VM bereitgestellte Azure Backup Server-Instanz kann Workloads auf den VMs in Azure VMware Solution sichern. Die Workloads müssen sich in derselben Domäne befinden, damit der Sicherungsvorgang aktiviert werden kann.

## <a name="create-a-recovery-services-vault"></a>Erstellen eines Recovery Services-Tresors

Bei einem Recovery Services-Tresor handelt es sich um eine Speicherentität zum Speichern der im Laufe der Zeit erstellten Wiederherstellungspunkte. Er enthält auch die Sicherungsrichtlinien, die geschützten Elementen zugewiesen sind.

1. Melden Sie sich bei Ihrem Abonnement im [Azure-Portal](https://portal.azure.com/) an.

1. Wählen Sie im Menü links **Alle Dienste** aus.

   ![Wählen Sie im Menü links Alle Dienste aus.](../backup/media/backup-create-rs-vault/click-all-services.png)

1. Geben Sie im Dialogfeld **Alle Dienste** den Begriff **Recovery Services** ein, und wählen Sie **Recovery Services-Tresore** in der Liste aus.

   ![Eingeben und Auswählen von „Recovery Services-Tresore“.](../backup/media/backup-create-rs-vault/all-services.png)

   Die Liste mit den Recovery Services-Tresoren im Abonnement wird angezeigt.

1. Wählen Sie auf dem Dashboard **Recovery Services-Tresore** die Option **Hinzufügen** aus.

   ![Hinzufügen eines Recovery Services-Tresors.](../backup/media/backup-create-rs-vault/add-button-create-vault.png)

   Das Dialogfeld **Recovery Services-Tresor** wird geöffnet.

1. Geben Sie Werte für **Name**, **Abonnement**, **Ressourcengruppe** und **Speicherort** ein.

   ![Konfigurieren des Recovery Services-Tresors.](../backup/media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Name**: Geben Sie einen Anzeigenamen ein, über den der Tresor identifiziert wird. Der Name muss für das Azure-Abonnement eindeutig sein. Geben Sie einen Namen ein, der mindestens zwei, aber nicht mehr als 50 Zeichen enthält. Der Name muss mit einem Buchstaben beginnen und darf nur Buchstaben, Zahlen und Bindestriche enthalten.
   - **Abonnement**: Wählen Sie das zu verwendende Abonnement aus. Wenn Sie nur in einem Abonnement Mitglied sind, wird dessen Name angezeigt. Falls Sie nicht sicher sind, welches Abonnement geeignet ist, können Sie das Standardabonnement bzw. das vorgeschlagene Abonnement verwenden. Es sind nur dann mehrere Auswahlmöglichkeiten verfügbar, wenn Ihr Geschäfts-, Schul- oder Unikonto mehreren Azure-Abonnements zugeordnet ist.
   - **Ressourcengruppe**: Verwenden Sie eine vorhandene Ressourcengruppe, oder erstellen Sie eine neue Ressourcengruppe. Um eine Liste der verfügbaren Ressourcengruppen in Ihrem Abonnement anzuzeigen, wählen Sie **Vorhandene verwenden** und dann eine Ressource in der Dropdownliste aus. Wählen Sie zum Erstellen einer neuen Ressourcengruppe **Neu erstellen** aus, und geben Sie den Namen ein.
   - **Standort**: Wählen Sie die geografische Region für den Tresor aus. Um einen Tresor zu erstellen, mit dem virtuelle Azure VMware Solution-Computer geschützt werden sollen, *muss* sich der Tresor in derselben Region befinden wie die private Azure VMware Solution-Cloud.

1. Wenn Sie den Recovery Services-Tresor erstellen möchten, wählen Sie **Erstellen** aus.

   ![Erstellen des Recovery Services-Tresors.](../backup/media/backup-create-rs-vault/click-create-button.png)

   Es kann einige Zeit dauern, denn Recovery Services-Tresor zu erstellen. Verfolgen Sie die Statusbenachrichtigungen im **Benachrichtigungsbereich** in der oberen rechten Ecke im Portal. Nach seiner Erstellung wird der Tresor in der Liste der Recovery Services-Tresore angezeigt. Sollte der Tresor nicht angezeigt werden, wählen Sie **Aktualisieren**.

   ![Aktualisieren der Liste der Sicherungstresore.](../backup/media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-replication"></a>Festlegen der Speicherreplikation

Für die Speicherreplikation haben Sie die Wahl zwischen georedundantem Speicher (die Standardeinstellung) und lokal redundantem Speicher. Bei georedundantem Speicher werden die Daten in Ihrem Speicherkonto in eine sekundäre Region kopiert, wodurch Ihre Daten dauerhaft sicher sind. Lokal redundanter Speicher ist eine kostengünstigere Option, die nicht so dauerhaft sicher ist. Weitere Informationen zu den Optionen für georedundanten und lokal redundanten Speicher finden Sie unter [Azure Storage-Redundanz](../storage/common/storage-redundancy.md).

> [!IMPORTANT]
> Ein Ändern der Einstellung von **Speicherreplikationstyp Lokal redundant/Georedundant** für einen Recovery Services-Tresor muss vorgenommen werden, bevor Sicherungen im Tresor konfiguriert werden. Nachdem Sie Sicherungen konfiguriert haben, ist die Option zum Ändern des Speicherreplikationstyps deaktiviert, sodass Sie den Typ nicht mehr ändern können.

1. Wählen Sie in **Recovery Services-Tresore** den neuen Tresor aus. 

1. Wählen Sie unter **Einstellungen** die Option **Eigenschaften** aus. Wählen Sie unter **Sicherungskonfiguration** die Option **Aktualisieren** aus.

1. Wählen Sie den Speicherreplikationstyp und dann **Speichern** aus.

## <a name="download-and-install-the-software-package"></a>Herunterladen und Installieren des Softwarepakets

Führen Sie die Schritte in diesem Abschnitt aus, um das Softwarepaket herunterzuladen, zu extrahieren und zu installieren.

### <a name="download-the-software-package"></a>Herunterladen des Softwarepakets

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Falls Sie bereits einen geöffneten Recovery Services-Tresor haben, fahren Sie mit dem nächsten Schritt fort. Wenn Sie keinen Recovery Services-Tresor geöffnet haben und sich im Azure-Portal befinden, klicken Sie im Hauptmenü auf **Durchsuchen**.

   1. Geben Sie in der Liste mit den Ressourcen **Recovery Services** ein.

   1. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Recovery Services-Tresore**, wenn der Eintrag angezeigt wird.

   ![Erstellen eines Recovery Services-Tresors – Schritt 1](../backup/media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

1. Wählen Sie in der Liste mit den Recovery Services-Tresoren einen Tresor aus.

   Das ausgewählte Tresor-Dashboard wird geöffnet.

   ![Das ausgewählte Tresor-Dashboard wird geöffnet.](../backup/media/backup-azure-microsoft-azure-backup/vault-dashboard.png)

   Die Option **Einstellungen** wird standardmäßig geöffnet. Ist die Option geschlossen sind, wählen Sie **Einstellungen** aus, um sie zu öffnen.

   ![Die Option Einstellungen wird standardmäßig geöffnet. Ist die Option geschlossen sind, wählen Sie Einstellungen aus, um sie zu öffnen.](../backup/media/backup-azure-microsoft-azure-backup/vault-setting.png)

1. Wählen Sie **Sicherung** aus, um den Assistenten für **Erste Schritte** zu öffnen.

   ![Wählen Sie Sicherung aus, um den Assistenten für Erste Schritte zu öffnen.](../backup/media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

1. Gehen Sie im eingeblendeten Fenster so vor:

   1. Wählen Sie im Menü **Wo wird Ihre Workload ausgeführt?** die Option **Lokal** aus.

      :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-on-premises-workload.png" alt-text="Wo wird Ihre Workload ausgeführt?":::

   1. Wählen Sie im Menü **Was möchten Sie sichern?** die Workloads aus, die Sie durch Verwenden von Azure Backup Server schützen möchten.

   1. Wählen Sie **Infrastruktur vorbereiten** aus, um Azure Backup Server und die Tresoranmeldeinformationen herunterzuladen und zu installieren.

      :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-prepare-infrastructure.png" alt-text="Infrastruktur vorbereiten":::

1. Gehen Sie im eingeblendeten Fenster **Infrastruktur vorbereiten** so vor:

   1. Wählen Sie den Link **Herunterladen** aus, um Azure Backup Server zu installieren.

   1. Aktivieren Sie das Kontrollkästchen **Bereits heruntergeladen oder Verwendung der neuesten Installation von Azure Backup Server**, und wählen Sie dann **Herunterladen** aus, um die Anmeldeinformationen des Tresors herunterzuladen. Sie verwenden diese Anmeldeinformationen, wenn Sie die Azure Backup Server-Instanz beim Recovery Services-Tresor registrieren. Mit den Links gelangen Sie zum Download Center, aus dem Sie das Softwarepaket herunterladen.

   :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-prepare-infrastructure2.png" alt-text="Infrastruktur vorbereiten: Azure Backup Server":::

1. Wählen Sie auf der Downloadseite alle Dateien aus, und wählen Sie **Weiter** aus.

   > [!NOTE]
   > Sie müssen alle Dateien in denselben Ordner herunterladen. Da die Downloadgröße aller Dateien zusammen mehr als 3 GB beträgt, kann es bis zu 60 Minuten dauern, bis der Download abgeschlossen ist. 

   ![Wählen Sie auf der Downloadseite alle Dateien aus, und wählen Sie Weiter aus.](../backup/media/backup-azure-microsoft-azure-backup/downloadcenter.png)

### <a name="extract-the-software-package"></a>Extrahieren des Softwarepakets

Wenn Sie das Softwarepaket auf einen anderen Server heruntergeladen haben, kopieren Sie die Dateien auf die VM, die Sie erstellt haben, um Azure Backup Server bereitzustellen.

> [!WARNING]
> Zum Extrahieren der Setupdateien sind mindestens 4 GB freier Speicherplatz erforderlich.

1. Nachdem Sie alle Dateien heruntergeladen haben, doppelklicken Sie auf **MicrosoftAzureBackupInstaller.exe**, um den **Microsoft Azure Backup**-Setup-Assistenten zu öffnen, und wählen Sie dann **Weiter** aus.

1. Wählen Sie den Speicherort aus, in den die Dateien extrahiert werden sollen, und dann **Weiter** aus.

1. Wählen Sie **Extrahieren** aus, um den Extrahierungsprozess zu starten.

   ![Wählen Sie Extrahieren aus, um den Extrahierungsprozess zu starten.](../backup/media/backup-azure-microsoft-azure-backup/extract/03.png)

1. Sobald die Dateien extrahiert sind, wählen Sie die Option für **setup.exe auszuführen** aus, und wählen Sie dann **Fertigstellen** aus.

> [!TIP]
> Sie können auch die „setup.exe“-Datei in dem Ordner ausführen, in dem Sie das Softwarepaket extrahiert haben.

### <a name="install-the-software-package"></a>Installieren des Softwarepakets

1. Wählen Sie im Setupfenster unter **Installieren** die Option **Microsoft Azure Backup** aus, um den Setup-Assistenten zu öffnen.

   ![Wählen Sie im Setupfenster unter Installieren die Option Microsoft Azure Backup aus, um den Setup-Assistenten zu öffnen.](../backup/media/backup-azure-microsoft-azure-backup/launch-screen2.png)

1. Wählen Sie auf dem **Willkommen**-Bildschirm die Schaltfläche **Weiter** aus, um zur Seite **Überprüfungen der Voraussetzungen** zu gelangen.

1. Wählen Sie **Erneut überprüfen** aus, um zu ermitteln, ob die Hardware- und Softwarevoraussetzungen für Azure Backup Server erfüllt sind. Ist dies der Fall, wählen Sie **Weiter** aus.

   ![ Wählen Sie „Erneut überprüfen“ aus, um zu ermitteln, ob die Hardware- und Softwarevoraussetzungen für Azure Backup Server erfüllt sind. Ist dies der Fall, wählen Sie Weiter aus.](../backup/media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

1. Zum Azure Backup Server-Installationspaket gehören die entsprechenden SQL Server-Binärdateien, di erforderlich sind. Wenn Sie eine neue Azure Backup Server-Installation starten, aktivieren Sie die Option **Mit diesem Setup neue Instanz von SQL Server installieren**. Wählen Sie dann **Prüfen und installieren** aus.

   ![Zum Azure Backup Server-Installationspaket gehören die entsprechenden SQL Server-Binärdateien, di erforderlich sind.](../backup/media/backup-azure-microsoft-azure-backup/sql/01.png)

   > [!NOTE]
   > Möchten Sie Ihre eigene SQL Server-Instanz verwenden, werden die folgenden SQL Server-Versionen unterstützt: SQL Server 2014 SP1 oder höher, 2016 und 2017. Alle SQL Server-Versionen müssen Standard- oder Enterprise-Versionen (64-Bit) sein. Die von Azure Backup Server verwendete Instanz darf nur lokal und kann nicht remote sein. Falls Sie eine vorhandene SQL Server-Instanz für Azure Backup Server verwenden, wird für Setup nur die Verwendung von *benannten Instanzen* von SQL Server unterstützt.

   Wenn ein Fehler mit der Empfehlung auftritt, den Computer neu zu starten, tun Sie dies, und wählen Sie **Erneut prüfen** aus. Bei allen Problemen mit der SQL Server-Konfiguration müssen Sie SQL Server gemäß den SQL Server-Richtlinien neu konfigurieren. Versuchen Sie dann erneut, Azure Backup Server zu installieren oder zu aktualisieren, indem Sie die vorhandene SQL Server-Instanz verwenden.

   **Manuelle Konfiguration**

   Wenn Sie Ihre eigene SQL Server-Instanz verwenden, stellen Sie sicher, dass Sie „builtin\Administrators“ zur Rolle „sysadmin“ der Masterdatenbank hinzufügen.

   **Konfigurieren von Reporting Services mit SQL Server 2017**

   Wenn Sie eine eigene Instanz von SQL Server 2017 verwenden, müssen Sie SQL Server 2017 Reporting Services (SSRS) manuell konfigurieren. Stellen Sie nach der Konfiguration von SSRS sicher, dass die Eigenschaft **IsInitialized** von SSRS auf **TRUE** festgelegt ist. Wenn diese Eigenschaft auf **TRUE** festgelegt ist, geht Azure Backup Server davon aus, dass SSRS bereits konfiguriert ist, und überspringt die SSRS-Konfiguration.

   Zum Überprüfen des Status der SSRS-Konfiguration führen Sie Folgendes aus:

   ```powershell
   $configset =Get-WmiObject –namespace 
   "root\Microsoft\SqlServer\ReportServer\RS_SSRS\v14\Admin" -class 
   MSReportServer_ConfigurationSetting -ComputerName localhost

   $configset.IsInitialized
   ```

   Verwenden Sie für die SSRS-Konfiguration die folgenden Werte:

   * **Dienstkonto**: **Integriertes Konto verwenden** sollte **Netzwerkdienst** lauten.
   * **Webdienst-URL**: **Virtuelles Verzeichnis** sollte **ReportServer_\<SQLInstanceName>** lauten.
   * **Datenbank**: **DatabaseName** sollte **ReportServer$\<SQLInstanceName>** lauten.
   * **Webportal-URL**: **Virtuelles Verzeichnis** sollte **Reports_\<SQLInstanceName>** lauten.

   Weitere Informationen zur SSRS-Konfiguration finden Sie [hier](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode).

   > [!NOTE]
   > In den [Microsoft-Bestimmungen für Onlinedienste](https://www.microsoft.com/licensing/product-licensing/products) (Online Services Terms, OST) ist die Lizenzierung für die SQL Server-Instanz geregelt, die als Datenbank für Azure Backup Server verwendet wird. Gemäß OST kann die SQL Server-Instanz, die mit Azure Backup Server gebündelt ist, nur als Datenbank für Azure Backup Server verwendet werden.

1. Nachdem die Installation erfolgreich abgeschlossen wurde, wählen Sie **Weiter** aus.

1. Geben Sie einen Speicherort für die Installation der Microsoft Azure Backup Server-Dateien an, und wählen Sie **Weiter** aus.

   > [!NOTE]
   > Der Scratchspeicherort (Scratchverzeichnis) ist zum Sichern in Azure erforderlich. Stellen Sie sicher, dass das Scratchverzeichnis eine Größe von mindestens 5 % der Daten aufweist, die in der Cloud gesichert werden sollen. Für den Datenträgerschutz müssen nach Abschluss der Installation separate Datenträger konfiguriert werden. Weitere Informationen zu Speicherpools finden Sie unter [Konfigurieren von Speicherpools und Datenträgerspeicher](/previous-versions/system-center/system-center-2012-r2/hh758075(v=sc.12)).

   ![Geben Sie einen Speicherort für die Installation der Microsoft Azure Backup Server-Dateien an, und wählen Sie „Weiter“ aus.](../backup/media/backup-azure-microsoft-azure-backup/space-screen.png)

1. Geben Sie ein sicheres Kennwort für eingeschränkte lokale Benutzerkonten an, und wählen Sie **Weiter** aus.

   ![Geben Sie ein sicheres Kennwort für eingeschränkte lokale Benutzerkonten an, und wählen Sie „Weiter“ aus.](../backup/media/backup-azure-microsoft-azure-backup/security-screen.png)

1. Bestimmen Sie, ob Sie mit Microsoft Update nach Updates suchen möchten, und wählen Sie **Weiter** aus.

   > [!NOTE]
   > Es wird empfohlen, Windows Update an Microsoft Update umzuleiten, da Microsoft Update Sicherheit und wichtige Updates für Windows und andere Produkte wie Azure Backup Server bietet.

   ![Bestimmen Sie, ob Sie mit Microsoft Update nach Updates suchen möchten, und wählen Sie „Weiter“ aus.](../backup/media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

1. Überprüfen Sie die **Zusammenfassung der Einstellungen**, und wählen Sie **Installieren** aus.

   Die Installation erfolgt in mehreren Phasen. 
   - In der ersten Phase wird der Microsoft Azure Recovery Services-Agent installiert.
   - In der zweiten Phase wird die Internetverbindung überprüft. Falls verfügbar, können Sie mit der Installation fortfahren. Falls nicht, müssen Sie die Proxydetails bereitstellen, um eine Verbindung mit dem Internet herzustellen. 
   - In der abschließenden Phase wird die erforderliche Software überprüft. Jegliche erforderliche Software, die noch nicht installiert ist, wird zusammen mit dem Microsoft Azure Recovery Services-Agent installiert.

1. Wählen Sie **Durchsuchen** aus, um nach Ihren Tresoranmeldeinformationen zu suchen, damit der Computer im Recovery Services-Tresor registriert werden kann, und wählen Sie **Weiter** aus.

1. Wählen Sie eine Passphrase aus, um die Daten zu ver- oder entschlüsseln, die zwischen Azure und Ihrem lokalen Standort ausgetauscht werden.

   > [!TIP]
   > Sie können eine automatische Passphrase generieren oder eine eigene Passphrase mit mindestens 16 Zeichen angeben.

1. Geben Sie den Speicherort zum Speichern der Passphrase ein, und wählen Sie dann **Weiter** aus, um den Server zu registrieren.

   > [!IMPORTANT]
   > Speichern Sie die Passphrase an einem sicheren Speicherort, der sich nicht auf dem lokalen Server befindet. Es wird dringend empfohlen, die Passphrase in Azure Key Vault zu speichern.

   Nachdem die Installation des Microsoft Azure Recovery Services-Agents abgeschlossen ist, wird der Installationsschritt mit der Installation und Konfiguration von SQL Server und der Azure Backup Server-Komponenten fortgesetzt.

   ![Nachdem die Installation des Microsoft Azure Recovery Services-Agents abgeschlossen ist, wird der Installationsschritt mit der Installation und Konfiguration von SQL Server und der Azure Backup Server-Komponenten fortgesetzt.](../backup/media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

1. Nachdem der Installationsschritt abgeschlossen ist, wählen Sie **Schließen** aus.

### <a name="install-update-rollup-1"></a>Installieren von Updaterollup 1

Die Installation von Updaterollup 1 für Azure Backup Server v3 ist obligatorisch, bevor Sie die Workloads schützen können.  Die Fehlerkorrekturen und Installationsanweisungen finden Sie im [Knowledge Base-Artikel](https://support.microsoft.com/en-us/help/4534062/).

## <a name="add-storage-to-azure-backup-server"></a>Hinzufügen von Speicher zu Azure Backup Server

Azure Backup Server v3 unterstützt Modern Backup Storage, das Folgendes bietet:

-  Speichereinsparungen von 50 %
-  Sicherungen, die dreimal schneller ausgeführt werden
-  Effizienteren Speicher
-  Workloadorientierten Speicher

### <a name="volumes-in-azure-backup-server"></a>Volumes in Azure Backup Server

Fügen Sie die Datenträger mit der für die Azure Backup Server-VM erforderlichen Speicherkapazität hinzu, falls noch nicht geschehen.

Azure Backup Server v3 unterstützt nur Speichervolumes. Wenn Sie ein Volume hinzufügen, formatiert Azure Backup Server das Volume mit ReFS (Resilient File System), was für Modern Backup Storage erforderlich ist.

### <a name="add-volumes-to-azure-backup-server-disk-storage"></a>Hinzufügen von Volumes zu Azure Backup Server-Datenträgerspeicher

1. Lesen Sie im Bereich **Verwaltung** den Speicher erneut ein, und wählen Sie dann **Hinzufügen** aus. 

1. Wählen Sie die verfügbaren Volumes aus, die Sie dem Speicherpool hinzufügen möchten. 

1. Nachdem Sie die verfügbaren Volumes hinzugefügt haben, geben Sie ihnen Anzeigenamen, um sie besser verwalten zu können. 

1. Wählen Sie **OK** aus, um diese Volumes mit ReFS zu formatieren, damit Azure Backup Server die Vorteile von Modern Backup Storage nutzen kann.


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit dem Einrichten von Azure Backup Server für Azure VMware Solution vertraut gemacht haben, sollten Sie sich über die folgenden Themen informieren:

- [Konfigurieren von Sicherungen für Azure VMware Solution-VMs](backup-azure-vmware-solution-virtual-machines.md)
- [Schützen von Azure VMware Solution-VMs mit Azure Security Center-Integration](azure-security-integration.md)

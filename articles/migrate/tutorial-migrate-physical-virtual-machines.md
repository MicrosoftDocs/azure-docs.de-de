---
title: Migrieren von Computern als physischer Server zu Azure mit Azure Migrate
description: In diesem Artikel wird beschrieben, wie Sie physische Computer mit Azure Migrate zu Azure migrieren.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: tutorial
ms.date: 01/02/2021
ms.custom: MVC
ms.openlocfilehash: aeeb810174ff5c21a81bcec8aa9265ff100edf91
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626324"
---
# <a name="migrate-machines-as-physical-servers-to-azure"></a>Migrieren von Computern als physische Server zu Azure

In diesem Artikel wird veranschaulicht, wie Sie Computer als physische Server zu Azure migrieren, indem Sie das Tool „Azure Migrate-Servermigration“ verwenden. Die Behandlung von Computern als physische Server für die Migration ist in verschiedenen Szenarien hilfreich:

- Migrieren von lokalen physischen Servern
- Migrieren von VMs, die über eine Plattform, z. B. Xen oder KVM, virtualisiert wurden
- Migrieren von Hyper-V- oder VMware-VMs, falls Sie aus einem bestimmten Grund den Standardmigrationsprozess für [Hyper-V](tutorial-migrate-hyper-v.md) oder die [VMware](server-migrate-overview.md)-Migration nicht nutzen können.
- Migrieren von VMs, die in privaten Clouds ausgeführt werden
- Migrieren von VMs, die in öffentlichen Clouds ausgeführt werden, z. B. Amazon Web Services (AWS) oder Google Cloud Platform (GCP)


Dieses Tutorial ist das dritte in einer Reihe zur Bewertung physischer Server und deren Migration zu Azure. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Vorbereiten der Verwendung von Azure mit Azure Migrate: Servermigration.
> * Überprüfen der Anforderungen für zu migrierende Computer und Vorbereiten eines Computers für die Azure Migrate-Replikationsappliance, die zum Ermitteln und Migrieren von Computern zu Azure verwendet wird
> * Fügen Sie das Azure Migrate- Servermigrationstool im Azure Migrate-Hub zu.
> * Einrichten der Replikationsappliance
> * Installieren des Mobility Service auf Computern, die Sie migrieren möchten
> * Aktivieren Sie die Replikation.
> * Führen Sie eine Testmigration aus, um sicherzustellen, dass alles wie erwartet funktioniert.
> * Durchführen einer vollständigen Migration zu Azure

> [!NOTE]
> In den Tutorials wird der einfachste Bereitstellungspfad für ein Szenario erläutert, damit Sie schnell einen Proof of Concept einrichten können. Die Tutorials verwenden nach Möglichkeit Standardoptionen und zeigen nicht alle möglichen Einstellungen und Pfade. Ausführliche Beschreibungen finden Sie in den Anleitungen zu Azure Migrate.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen, bevor Sie beginnen.


## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, sollten folgende Voraussetzungen erfüllt sein:

[Überprüfen Sie die Migrationsarchitektur](./agent-based-migration-architecture.md).

## <a name="prepare-azure"></a>Vorbereiten von Azure

Vorbereiten von Azure für die Migration mit Azure Migrate: Servermigration.

**Aufgabe** | **Details**
--- | ---
**Erstellen eines Azure Migrate-Projekts** | Ihr Azure-Konto benötigt zum [Erstellen eines neuen Projekts](./create-manage-projects.md) Berechtigungen vom Typ „Mitwirkender“ oder „Besitzer“.
**Überprüfen der Berechtigungen für Ihr Azure-Konto** | Ihr Azure-Konto benötigt Berechtigungen zum Erstellen eines virtuellen Computers sowie zum Schreiben auf einen verwalteten Azure-Datenträger.


### <a name="assign-permissions-to-create-project"></a>Zuweisen von Berechtigungen für die Projekterstellung

1. Öffnen Sie im Azure-Portal das Abonnement, und wählen Sie **Zugriffssteuerung (IAM)** aus.
2. Suchen Sie unter **Zugriff überprüfen** nach dem relevanten Konto, und klicken Sie darauf, um Berechtigungen anzuzeigen.
3. Sie sollten über die Berechtigung **Mitwirkender** oder **Besitzer** verfügen.
    - Wenn Sie gerade erst ein kostenloses Azure-Konto erstellt haben, sind Sie der Besitzer Ihres Abonnements.
    - Wenn Sie nicht der Besitzer des Abonnements sind, müssen Sie mit dem Besitzer zusammenarbeiten, um die Rolle zuzuweisen.


### <a name="assign-azure-account-permissions"></a>Zuweisen der Azure-Kontoberechtigungen

Weisen Sie dem Azure-Konto die Rolle „Mitwirkender für virtuelle Computer“ zu. Dadurch werden Berechtigungen für folgende Aktionen erteilt:

- Erstellen einer VM in der ausgewählten Ressourcengruppe
- Erstellen einer VM im ausgewählten virtuellen Netzwerk
- Schreiben auf einen verwalteten Azure-Datenträger 

### <a name="create-an-azure-network"></a>Erstellen eines Azure-Netzwerks

Sie müssen ein virtuelles Azure-Netzwerk (VNET) [einrichten](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Wenn Sie die Replikation in Azure durchführen, werden virtuelle Azure-Computer erstellt und in das Azure-VNET eingebunden, das Sie beim Einrichten der Migration angeben.

## <a name="prepare-for-migration"></a>Vorbereiten der Migration

Zur Vorbereitung auf die Migration physischer Server müssen Sie Einstellungen des physischen Servers überprüfen sowie die Bereitstellung einer Replikationsappliance vorbereiten.

### <a name="check-machine-requirements-for-migration"></a>Überprüfen der Computeranforderungen für die Migration

Stellen Sie sicher, dass die Computer die Anforderungen für die Migration zu Azure erfüllen. 

> [!NOTE]
> Bei der Migration physischer Computer verwendet die Azure Migrate-Servermigration die gleiche Replikationsarchitektur wie die agentbasierte Notfallwiederherstellung des Azure Site Recovery-Diensts. Darüber hinaus weisen einige Komponenten die gleiche Codebasis auf. Einige Inhalte sind daher ggf. mit der Site Recovery-Dokumentation verknüpft.

1. [Überprüfen](migrate-support-matrix-physical-migration.md#physical-server-requirements) Sie die Anforderungen physischer Server.
2. Stellen Sie sicher, dass lokale virtuelle Computer, die Sie in Azure replizieren möchten, die [Azure-VM-Anforderungen](migrate-support-matrix-physical-migration.md#azure-vm-requirements) erfüllen.
3. Für virtuelle Computer müssen vor der Migration zu Azure ein paar Änderungen vorgenommen werden.
    - Bei einigen Betriebssystemen führt Azure Migrate diese Änderungen automatisch durch. 
    - Es ist wichtig, diese Änderungen vorzunehmen, bevor Sie mit der Migration beginnen. Wenn Sie den virtuellen Computer migrieren, bevor Sie die Änderung vorgenommen haben, wird der virtuelle Computer in Azure unter Umständen nicht gestartet. Überprüfen Sie die erforderlichen Änderungen für [Windows](prepare-for-migration.md#windows-machines) und [Linux](prepare-for-migration.md#linux-machines).

### <a name="prepare-a-machine-for-the-replication-appliance"></a>Vorbereiten eines Computers für die Replikationsappliance

Von der Azure Bei der Servermigration wird eine Replikationsappliance verwendet, um Computer in Azure zu replizieren. Die Replikationsappliance führt die hier angegebenen Komponenten aus.

- **Konfigurationsserver**: Der Konfigurationsserver koordiniert die Kommunikation zwischen der lokalen Umgebung und Azure und verwaltet die Datenreplikation.
- **Prozessserver** Der Prozessserver fungiert als Replikationsgateway. Er empfängt Replikationsdaten, optimiert sie durch Zwischenspeicherung, Komprimierung und Verschlüsselung und sendet sie an ein Cachespeicherkonto in Azure. 

Bereiten Sie die Bereitstellung der Appliance wie folgt vor:

- Sie bereiten einen Computers für das Hosten der Replikationsappliance vor. [Überprüfen](migrate-replication-appliance.md#appliance-requirements) Sie die Computeranforderungen.
- Die Replikationsappliance verwendet MySQL. Überprüfen Sie die [Optionen](migrate-replication-appliance.md#mysql-installation) für die Installation von MySQL auf der Appliance.
- Überprüfen Sie die Azure-URLs, die die Replikationsappliance für den Zugriff auf [öffentliche](migrate-replication-appliance.md#url-access) und [behördliche](migrate-replication-appliance.md#azure-government-url-access) Clouds benötigt.
- Überprüfen Sie die [Portzugriffsanforderungen](migrate-replication-appliance.md#port-access) für die Replikationsappliance.

> [!NOTE]
> Die Replikationsappliance sollte nicht auf einem zu replizierenden Quellcomputer oder auf der Ermittlungs- und Bewertungsappliance von Azure Migrate installiert werden, die Sie unter Umständen bereits installiert haben.

## <a name="set-up-the-replication-appliance"></a>Einrichten der Replikationsappliance

Der erste Schritt bei der Migration besteht darin, die Replikationsappliance einzurichten. Zum Einrichten der Appliance für die Migration physischer Server laden Sie die Installationsdatei für die Appliance herunter, und führen Sie diese dann auf dem [vorbereiteten Computer](#prepare-a-machine-for-the-replication-appliance) aus. Nachdem Sie die Appliance installiert haben, registrieren Sie sie bei Azure Migrate- Servermigration.


### <a name="download-the-replication-appliance-installer"></a>Herunterladen des Installationsprogramms für die Replikationsappliance

1. Klicken Sie im Azure Migrate-Projekt unter **Server** > **Azure Migrate: Servermigration** auf **Ermitteln**.

    ![VMs ermitteln](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. Klicken Sie unter **Computer ermitteln** > **Sind Ihre Computer virtualisiert?** auf **Nicht virtualisiert/Andere**.
4. Wählen Sie unter **Zielregion** die Azure-Region aus, zu der Sie die Computer migrieren möchten.
5. Aktivieren Sie das Kontrollkästchen **Bestätigen Sie, dass die Zielregion für die Migration „<Name der Region>“ lautet**.
6. Klicken Sie auf **Ressourcen erstellen**. Daraufhin wird im Hintergrund ein Azure Site Recovery-Tresor erstellt.
    - Falls Sie die Migration bereits mit dem Tool für die Azure Migrate-Servermigration eingerichtet haben, kann die Zieloption nicht konfiguriert werden, da die Ressourcen bereits vorher eingerichtet wurden.    
    - Nach dem Klicken auf diese Schaltfläche kann die Zielregion für dieses Projekt nicht mehr geändert werden.
    - Alle anschließenden Migrationen erfolgen zu dieser Region.

7. Wählen Sie unter **Möchten Sie eine neue Replikationsappliance installieren oder ein vorhandenes Setup horizontal hochskalieren?** die Option **Replikationsappliance installieren**.
9. Führen Sie unter **Laden Sie die Software für die Replikationsappliance herunter, und installieren Sie sie** den Download des Installationsprogramms für die Appliance und des Registrierungsschlüssels durch. Sie benötigen den Schlüssel zum Registrieren der Appliance. Der Schlüssel ist nach dem Herunterladen fünf Tage lang gültig.

    ![Anbieter herunterladen](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. Kopieren Sie die Setupdatei für die Appliance und die Schlüsseldatei auf den Windows Server 2016-Computer, den Sie für die Appliance erstellt haben.
11. Nach Abschluss der Installation wird automatisch der Assistent zur Appliancekonfiguration gestartet. (Sie können den Assistenten auch manuell über die cspsconfigtool-Verknüpfung starten, die auf dem Desktop der Appliance erstellt wird.) Fügen Sie über die Registerkarte „Konten verwalten“ des Assistenten Kontodetails hinzu, die für die Pushinstallation des Mobilitätsdiensts verwendet werden. In diesem Tutorial installieren wir den Mobilitätsdienst manuell auf den Quell-VMs, die repliziert werden sollen. Erstellen Sie daher in diesem Schritt ein Dummykonto, und fahren Sie fort. Sie können die folgenden Details zum Erstellen des Dummykontos angeben: „guest“ als Anzeigename, „username“ als Benutzername und „password“ als Kennwort für das Konto. Sie verwenden dieses Dummykonto in der Phase zum Aktivieren der Replikation. 

12. Nachdem die Appliance nach Abschluss des Setupvorgangs neu gestartet wurde, wählen Sie im Bereich **Computer ermitteln** die neue Appliance unter **Konfigurationsserver auswählen** aus. Klicken Sie anschließend auf **Finalize registration** (Registrierung abschließen). Beim Abschließen der Registrierung werden einige letzte Schritte ausgeführt, um die Replikationsappliance vorzubereiten.

    ![Registrierung abschließen](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

Nach dem Abschluss der Registrierung kann es einige Zeit dauern, bis ermittelte Computer in „Azure Migrate-Servermigration“ angezeigt werden. Wenn VMs ermittelt werden, erhöht sich die Anzahl unter **Ermittelte Server**.

![Ermittelte Server](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>Installieren des Mobilitätsdiensts

Auf Computern, die Sie migrieren möchten, müssen Sie den Mobility Service-Agent installieren. Die Installationsprogramme für den Agent sind auf der Replikationsappliance verfügbar. Sie können nach dem richtigen Installationsprogramm suchen und den Agent auf jedem Computer installieren, den Sie migrieren möchten. Gehen Sie hierzu wie folgt vor:

1. Melden Sie sich an der Replikationsappliance an.
2. Navigieren Sie zu **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Suchen Sie nach dem richtigen Installationsprogramm für das Betriebssystem und die Version des Computers. Lesen Sie den Artikel zu den [unterstützten Betriebssystemen](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines). 
4. Kopieren Sie die Installationsdatei auf den Computer, den Sie migrieren möchten.
5. Stellen Sie sicher, dass Sie über die Passphrase verfügen, die bei der Bereitstellung des Geräts generiert wurde.
    - Speichern Sie die Datei in einer temporären Textdatei auf dem Computer.
    - Sie können die Passphrase über die Replikationsappliance abrufen. Führen Sie in der Befehlszeile **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v** aus, um die aktuelle Passphrase anzuzeigen.
    - Generieren Sie die Passphrase nicht neu. Hierbei wird die Verbindung getrennt, und Sie müssen die Replikationsappliance erneut registrieren.

> [!NOTE]
> Im Parameter */Platform* geben Sie *VMware* an, wenn Sie virtuelle VMware-Computer oder physische Computer migrieren.

### <a name="install-on-windows"></a>Installieren unter Windows

1. Extrahieren Sie den Inhalt der Installationsprogrammdatei wie folgt in einen lokalen Ordner des Computers (z. B. „C:\Temp“):

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. Führen Sie das Mobility Service-Installationsprogramm aus:
    ```
   UnifiedAgent.exe /Role "MS" /Platform "VmWare" /Silent
    ```
3. Registrieren Sie den Agent bei der Replikationsappliance:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Installation unter Linux

1. Extrahieren Sie den Inhalt des Tarball-Installationsprogramms wie folgt in einen lokalen Ordner des Computers (z. B. „/tmp/MobSvcInstaller“):
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Führen Sie das Installationsskript aus:
    ```
    sudo ./install -r MS -v VmWare -q
    ```
3. Registrieren Sie den Agent bei der Replikationsappliance:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>Replizieren von Computern

Wählen Sie nun Computer für die Migration aus. 

> [!NOTE]
> Sie können bis zu zehn Computer gleichzeitig replizieren. Müssen Sie mehr Computer replizieren, führen Sie die Replikation in Batches mit jeweils zehn Computern durch.

1. Klicken Sie im Azure Migrate-Projekt unter **Server** > **Azure Migrate: Servermigration** auf **Replizieren**.

    ![Screenshot: Bildschirm „Azure Migrate – Server“ mit hervorgehobener Schaltfläche „Replizieren“ unter „Azure Migrate: Servermigration“ im Abschnitt „Migrationstools“](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. Wählen Sie unter **Replizieren** > **Quelleinstellungen** > **Sind Ihre Computer virtualisiert?** die Option **Nicht virtualisiert/Andere** aus.
3. Wählen Sie unter **Lokale Appliance** den Namen der Azure Migrate-Appliance aus, die Sie eingerichtet haben.
4. Wählen Sie unter **Prozessserver** den Namen der Replikationsappliance aus.
6. Wählen Sie unter **Gastanmeldeinformationen** das Dummykonto aus, das Sie zuvor während des [Replikationsinstaller-Setups](#download-the-replication-appliance-installer) erstellt haben, um den Mobilitätsdienst manuell zu installieren. (Die Pushinstallation wird nicht unterstützt.) Klicken Sie anschließend auf **Next: Virtuelle Computer**.   

    ![Screenshot: Registerkarte „Quelleinstellungen“ im Bildschirm „Replizieren“ mit hervorgehobenem Feld „Gastanmeldeinformationen“](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. Übernehmen Sie im Bereich **Virtuelle Computer** unter **Migrationseinstellungen aus einer Bewertung importieren?** die Standardeinstellung **Nein, ich gebe die Migrationseinstellungen manuell an**.
8. Wählen Sie alle VMs aus, die Sie migrieren möchten. Klicken Sie anschließend auf **Next: Zieleinstellungen**.

    ![Auswählen von VMs](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. Wählen Sie unter **Zieleinstellungen** das Abonnement und die Zielregion für die Migration aus, und geben Sie die Ressourcengruppe an, in der sich die Azure-VMs nach der Migration befinden.
10. Wählen Sie unter **Virtuelles Netzwerk** das Azure-VNET/-Subnetz aus, in das die Azure-VMs nach der Migration eingebunden werden.
11. Wählen Sie unter **Verfügbarkeitsoptionen** Folgendes aus:
    -  Verfügbarkeitszone, um den migrierten Computer einer bestimmten Verfügbarkeitszone in der Region anzuheften. Verteilen Sie mit dieser Option Server, die eine Anwendungsebene mit mehreren Knoten bilden, über Verfügbarkeitszonen. Wenn Sie diese Option auswählen, müssen Sie die Verfügbarkeitszone angeben, die für jeden auf der Registerkarte „Compute“ ausgewählten Computer verwendet werden soll. Diese Option ist nur verfügbar, wenn die für die Migration ausgewählte Zielregion Verfügbarkeitszonen unterstützt.
    -  Verfügbarkeitsgruppe, um den migrierten Computer in einer Verfügbarkeitsgruppe zu platzieren. Um diese Option verwenden zu können, muss die ausgewählte Zielressourcengruppe über mindestens eine Verfügbarkeitsgruppe verfügen.
    - Die Infrastrukturredundanzoption ist nicht erforderlich, wenn Sie keine dieser Verfügbarkeitskonfigurationen für die migrierten Computer benötigen.
    
12. Wählen Sie unter **Datenträgerverschlüsselungstyp** Folgendes aus:
    - Verschlüsselung ruhender Daten mit plattformseitig verwaltetem Schlüssel
    - Verschlüsselung ruhender Daten mit kundenseitig verwaltetem Schlüssel
    - Mehrfachverschlüsselung mit plattformseitig und kundenseitig verwalteten Schlüsseln

   > [!NOTE]
   > Um VMs mit CMK zu replizieren, müssen Sie unter der Zielressourcengruppe einen [Datenträgerverschlüsselungssatz erstellen](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set). Ein Datenträgerverschlüsselungssatz-Objekt ordnet verwaltete Datenträger einer Key Vault-Instanz zu, die den für SSE zu verwendenden CMK enthält.
  
13. Wählen Sie unter **Azure-Hybridvorteil**

    - die Option **Nein** aus, falls Sie den Azure-Hybridvorteil nicht anwenden möchten. Klicken Sie dann auf **Weiter**.
    - Wählen Sie **Ja** aus, wenn Sie über Windows Server-Computer verfügen, die durch aktive Software Assurance- oder Windows Server-Abonnements abgedeckt sind, und den Vorteil auf die zu migrierenden Computer anwenden möchten. Klicken Sie dann auf **Weiter**.

    ![Zieleinstellungen](./media/tutorial-migrate-vmware/target-settings.png)

14. Überprüfen Sie in **Compute** Name, Größe, Betriebssystem- Datenträger und Verfügbarkeitskonfiguration der VM (falls im vorherigen Schritt ausgewählt). Die VMs müssen die [Azure-Anforderungen](migrate-support-matrix-physical-migration.md#azure-vm-requirements) erfüllen.

    - **VM-Größe**: Bei Verwendung von Bewertungsempfehlungen zeigt die Dropdownliste für die VM-Größe die empfohlene Größe. Andernfalls wählt Azure Migrate eine Größe basierend auf der höchsten Übereinstimmung im Azure-Abonnement aus. Alternativ können Sie unter **Azure-VM-Größe** manuell eine Größe auswählen.
    - **Betriebssystemdatenträger**: Geben Sie den Betriebssystemdatenträger (Startdatenträger) für die VM an. Der Betriebssystemdatenträger enthält den Bootloader und das Installationsprogramm des Betriebssystems.
    - **Verfügbarkeitszone**: Geben Sie die zu verwendende Verfügbarkeitszone an.
    - **Verfügbarkeitsgruppe**: Geben Sie die zu verwendende Verfügbarkeitsgruppe an.

![Computeeinstellungen](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

15. Geben Sie unter **Datenträger** an, ob die VM-Datenträger in Azure repliziert werden sollen, und wählen Sie den Datenträgertyp (SSD Standard/HDD Standard oder Managed Disks Premium) in Azure aus. Klicken Sie dann auf **Weiter**.
    - Sie können Datenträger von der Replikation ausschließen.
    - Wenn Sie Datenträger ausschließen, sind diese nach der Migration nicht auf der Azure-VM vorhanden. 

    ![Datenträgereinstellungen](./media/tutorial-migrate-physical-virtual-machines/disks.png)

16. Überprüfen Sie unter **Replikation prüfen und starten** die Einstellungen, und klicken Sie auf **Replizieren**, um die erste Replikation für die Server zu starten.

> [!NOTE]
> Sie können die Replikationseinstellungen vor Beginn der Replikation jederzeit unter **Verwalten** > **Aktuell replizierte Computer** aktualisieren. Die Einstellungen können nach dem Beginn der Replikation nicht mehr geändert werden.

## <a name="track-and-monitor"></a>Nachverfolgen und Überwachen

- Wenn Sie auf **Replizieren** klicken, beginnt die Durchführung des Auftrags „Replikation starten“. 
- Nachdem der Auftrag „Replikation starten“ erfolgreich abgeschlossen wurde, beginnen die Computer mit der ersten Replikation in Azure.
- Nach Abschluss der ersten Replikation beginnt die Deltareplikation. Inkrementelle Änderungen an lokalen Datenträgern werden regelmäßig auf den Replikatdatenträgern in Azure repliziert.


Sie haben die Möglichkeit, den Auftragsstatus über die Portalbenachrichtigungen nachzuverfolgen.

Sie können den Replikationsstatus überwachen, indem Sie auf **Server werden repliziert** klicken (unter **Azure Migrate: Servermigration**).
![Überwachen der Replikation](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Ausführen einer Testmigration


Wenn die Deltareplikation beginnt, können Sie zunächst eine Testmigration für die VMs ausführen, bevor eine vollständige Migration zu Azure erfolgt. Dieser Schritt sollte vor der Migration mindestens einmal für jeden Computer ausgeführt werden.

- Bei einer Testmigration wird überprüft, ob die Migration wie erwartet funktioniert, ohne die lokalen Computer zu beeinträchtigen, und ob diese während der Replikation betriebsbereit bleiben. 
- Mit der Testmigration wird die Migration simuliert, indem eine Azure-VM mit replizierten Daten erstellt wird (normalerweise per Migration zu einem nicht für die Produktion bestimmten VNET unter Ihrem Azure-Abonnement).
- Sie können den replizierten virtuellen Azure-Testcomputer verwenden, um die Migration zu überprüfen, App-Tests durchzuführen und Probleme zu beheben, bevor die vollständige Migration erfolgt.

Führen Sie die Testmigration wie folgt durch:


1. Klicken Sie unter **Migrationsziele** > **Server** > **Azure Migrate: Servermigration** auf **Migrierte Server testen**.

     ![Testen der migrierten Server](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Klicken Sie mit der rechten Maustaste auf die zu testende VM, und klicken Sie anschließend auf **Testmigration**.

    ![Testmigration](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. Wählen Sie unter **Testmigration** das Azure VNET aus, in dem sich die Azure-VM nach der Migration befindet. Es empfiehlt sich, ein nicht für die Produktion bestimmtes VNET zu verwenden.
4. Der Auftrag **Testmigration** wird gestartet. Überwachen Sie den Auftrag anhand der Portalbenachrichtigungen.
5. Zeigen Sie die migrierte Azure-VM nach Abschluss der Migration im Azure-Portal unter **Virtuelle Computer** an. Der Computername enthält das Suffix **-Test**.
6. Klicken Sie nach Abschluss des Tests mit der rechten Maustaste unter **Aktuell replizierte Computer** auf die Azure-VM, und klicken Sie anschließend auf **Testmigration bereinigen**.

    ![Bereinigen der Migration](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>Migrieren virtueller Computer

Nachdem Sie sich vergewissert haben, dass die Testmigration wie erwartet funktioniert, können Sie die lokalen Computer migrieren.

1. Klicken Sie im Azure Migrate-Projekt unter **Server** > **Azure Migrate: Servermigration** auf **Server werden repliziert**.

    ![Replizieren der Server](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. Klicken Sie unter **Aktuell replizierte Computer** mit der rechten Maustaste auf die VM und dann auf **Migrieren**.
3. Wählen Sie unter **Migrieren** > **Virtuelle Computer herunterfahren und eine geplante Migration ohne Datenverlust durchführen?** die Option **Ja** >  und anschließend **OK** aus.
    - Falls Sie die VM nicht herunterfahren möchten, wählen Sie **Nein** aus.
    
    Hinweis: Bei der Migration physischer Server empfiehlt es sich, die Anwendung während des Migrationszeitfensters offline zu schalten. (Die Anwendungen dürfen keine Verbindungen akzeptieren.) Initiieren Sie anschließend die Migration. (Der Server muss weiterhin ausgeführt werden, damit Änderungen vor Abschluss der Migration synchronisiert werden können.)

4. Für den virtuellen Computer wird ein Migrationsauftrag gestartet. Verfolgen Sie den Auftrag anhand der Azure-Benachrichtigungen nach.
5. Nach Abschluss des Auftrags können Sie die VM auf der Seite **Virtuelle Computer** anzeigen und verwalten.

## <a name="complete-the-migration"></a>Fertigstellen der Migration

1. Klicken Sie nach Abschluss der Migration mit der rechten Maustaste auf die VM und dann auf **Migration beenden**. Die folgenden Schritte werden ausgeführt:
    - Beendet die Replikation für den lokalen Computer.
    - Entfernt den Computer aus dem Zähler **Server werden repliziert.** in Azure Migrate: Servermigration.
    - Bereinigt die Replikationsstatusinformationen für den Computer.
2. Installieren Sie den [Windows](../virtual-machines/extensions/agent-windows.md)- oder [Linux](../virtual-machines/extensions/agent-linux.md)-Agent für die Azure-VM auf den migrierten Computern.
3. Führen Sie App-Anpassungen nach der Migration durch, z.B. die Aktualisierung von Datenbankverbindungszeichenfolgen und Webserverkonfigurationen.
4. Führen Sie endgültige Anwendungs- und Migrationsakzeptanztests für die migrierte Anwendung durch, die nun in Azure ausgeführt wird.
5. Leiten Sie den Datenverkehr auf die migrierte Instanz der Azure-VM um.
6. Entfernen Sie die lokalen VMs aus Ihrem lokalen VM-Bestand.
7. Entfernen Sie die lokalen VMs aus lokalen Sicherungen.
8. Aktualisieren Sie die interne Dokumentation zum Anzeigen des neuen Speicherorts und der IP-Adresse der Azure-VMs. 

## <a name="post-migration-best-practices"></a>Bewährte Methoden nach der Migration

- Beachten Sie zur Steigerung der Resilienz Folgendes:
    - Schützen Sie Daten, indem Sie Azure-VMs mit dem Azure Backup-Dienst sichern. [Weitere Informationen](../backup/quick-backup-vm-portal.md)
    - Sorgen Sie für die kontinuierliche Ausführung und Verfügbarkeit von Workloads, indem Sie Azure-VMs mithilfe von Site Recovery in eine sekundäre Region replizieren. [Weitere Informationen](../site-recovery/azure-to-azure-tutorial-enable-replication.md)
- Beachten Sie zur Steigerung der Sicherheit Folgendes:
    - Sperren und beschränken Sie den Zugriff von eingehendem Datenverkehr mit der [Just-in-Time-Verwaltung im Azure Security Center](../security-center/security-center-just-in-time.md).
    - Beschränken Sie den Netzwerkdatenverkehr mithilfe von [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md) auf Verwaltungsendpunkte.
    - Stellen Sie [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) bereit, um Datenträger und Daten vor Diebstahl und unbefugtem Zugriff zu schützen.
    - Erfahren Sie mehr über das [Sichern von IaaS-Ressourcen](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/), und besuchen Sie die Website [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Beachten Sie zur Überwachung und Verwaltung Folgendes:
    - Ziehen Sie die Bereitstellung von [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) in Erwägung, um den Ressourceneinsatz und die Ausgaben zu überwachen.


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den Artikel zur [Cloudmigration](/azure/architecture/cloud-adoption/getting-started/migrate) des Frameworks für die Cloudeinführung (Cloud Adoption Framework) an.

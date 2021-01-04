---
title: Migrieren von virtuellen VMware-Computern mit Agent-basierter Servermigration mit Azure Migrate
description: Hier erfahren, wie Sie eine Agent-basierte Migration virtueller VMware-Computer mit Azure Migrate ausführen.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: MVC
ms.openlocfilehash: 0c3b86fc823e4b563dc747a5604fcb2e04995fed
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96752650"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>Migrieren von VMware-VMs zu Azure (Agent-basiert)

In diesem Artikel wird gezeigt, wie Sie lokale VMware-VMs mithilfe des Tools [Azure Migrate-Servermigration](migrate-services-overview.md#azure-migrate-server-migration-tool) mit Agent zu Azure migrieren.  Sie können mit der Agent-basierten Migration auch VMware-VMs migrieren. [Vergleichen Sie](server-migrate-overview.md#compare-migration-methods) die Methoden.


 In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Vorbereiten von Azure auf die Arbeit mit Azure Migrate
> * Vorbereiten der Agent-basierten Migration Sie richten ein VMware-Konto ein, damit von Azure Migrate Computer für die Migration ermittelt werden können. Sie richten ein Konto ein, damit der Mobilitätsdienst-Agent auf zu migrierenden Computern installiert werden kann, und bereiten einen Computer vor, der als Replikationsappliance fungiert.
> * Hinzufügen des Tools für die Azure Migrate-Servermigration
> * Einrichten der Replikationsappliance
> * Replizieren von VMs
> * Führen Sie eine Testmigration aus, um sicherzustellen, dass alles wie erwartet funktioniert.
> * Durchführen einer vollständigen Migration zu Azure

> [!NOTE]
> In den Tutorials wird der einfachste Bereitstellungspfad für ein Szenario erläutert, damit Sie schnell einen Proof of Concept einrichten können. Die Tutorials verwenden nach Möglichkeit Standardoptionen und zeigen nicht alle möglichen Einstellungen und Pfade. 

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen, bevor Sie beginnen.


## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, sollten Sie sich [über die Agent-basierte VMware-Migrationsarchitektur informieren](./agent-based-migration-architecture.md).

## <a name="prepare-azure"></a>Vorbereiten von Azure

Führen Sie die Aufgaben in der Tabelle durch, um Azure für die Agent-basierte Migration vorzubereiten.

**Aufgabe** | **Details**
--- | ---
**Erstellen eines Azure Migrate-Projekts** | Ihr Azure-Konto benötigt zum Erstellen eines Projekts Berechtigungen vom Typ „Mitwirkender“ oder „Besitzer“.
**Überprüfen der Azure-Kontoberechtigungen** | Ihr Azure-Konto benötigt Berechtigungen zum Erstellen eines virtuellen Computers sowie zum Schreiben auf einen verwalteten Azure-Datenträger.
**Einrichten eines Azure-Netzwerks** | Richten Sie ein Netzwerk ein, dem Azure-VMs nach der Migration beitreten können.

### <a name="assign-permissions-to-create-project"></a>Zuweisen von Berechtigungen für die Projekterstellung
Falls Sie noch kein Azure Migrate-Projekt besitzen, sollten Sie überprüfen, ob Sie über die erforderlichen Berechtigungen für die Erstellung verfügen.


1. Öffnen Sie im Azure-Portal das Abonnement, und wählen Sie **Zugriffssteuerung (IAM)** aus.
2. Suchen Sie unter **Zugriff überprüfen** nach dem relevanten Konto, und klicken Sie darauf, um Berechtigungen anzuzeigen.
3. Vergewissern Sie sich, dass Sie über die Berechtigung **Mitwirkender** oder **Besitzer** verfügen.

    - Wenn Sie gerade erst ein kostenloses Azure-Konto erstellt haben, sind Sie der Besitzer Ihres Abonnements.
    - Wenn Sie nicht der Besitzer des Abonnements sind, müssen Sie mit dem Besitzer zusammenarbeiten, um die Rolle zuzuweisen.
    
### <a name="assign-azure-account-permissions"></a>Zuweisen der Azure-Kontoberechtigungen

Weisen Sie dem Konto die Rolle „Mitwirkender für virtuelle Computer“ zu, damit Sie über die folgenden Berechtigungen verfügen:

- Erstellen einer VM in der ausgewählten Ressourcengruppe
- Erstellen einer VM im ausgewählten virtuellen Netzwerk
- Schreiben auf einen verwalteten Azure-Datenträger 


### <a name="set-up-an-azure-network"></a>Richten Sie ein Azure-Netzwerk ein

[Richten Sie ein Azure-Netzwerk ein](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Lokale Computer werden auf verwalteten Azure-Datenträgern repliziert. Wenn Sie für die Migration ein Failover zu Azure durchführen, werden aus diesen verwalteten Datenträgern Azure-VMs erstellt und mit dem von Ihnen eingerichteten Azure-Netzwerk verknüpft.

## <a name="prepare-for-migration"></a>Vorbereiten der Migration

Überprüfen Sie die Supportanforderungen und die Berechtigungen, und bereiten Sie die Bereitstellung einer Replikationsappliance vor. 

### <a name="prepare-an-account-to-discover-vms"></a>Vorbereiten eines Kontos für die Ermittlung von VMs

Die Azure Migrate-Servermigration benötigt Zugriff auf VMware-Server, um die zu migrierenden VMs ermitteln zu können. Erstellen Sie das Konto wie folgt:

1. Erstellen Sie zum Verwenden eines dedizierten Kontos eine Rolle auf vCenter-Ebene. Geben Sie der Rolle einen Namen, z. B. **Azure_Migrate**.
2. Weisen Sie der Rolle die Berechtigungen, die in der folgenden Tabelle aufgeführt sind, zu.
3. Erstellen Sie einen Benutzer auf dem vCenter-Server oder vSphere-Host. Weisen Sie dem Benutzer die Rolle zu.

#### <a name="vmware-account-permissions"></a>VMware-Kontoberechtigungen

**Aufgabe** | **Rolle/Berechtigungen** | **Details**
--- | --- | ---
**VM-Ermittlung** | Mindestens ein Benutzer mit Lesezugriff<br/><br/> Data Center object (Rechenzentrenobjekt) –> Propagate to Child Object (An untergeordnetes Objekt weitergeben), role=Read-only (Rolle=schreibgeschützt) | Der Benutzer wird auf Datencenterebene zugewiesen und hat Zugriff auf alle Objekte im Datencenter.<br/><br/> Um den Zugriff einzuschränken, weisen Sie den untergeordneten Objekten (vSphere-Hosts, Datenspeicher, VMs und Netzwerke) die Rolle **No access** (Kein Zugriff) mit dem Objekt **Propagate to child** (An untergeordnetes Element weitergeben) zu.
**Replikation** |  Erstellen Sie eine Rolle („Azure_Site_Recovery“) mit den erforderlichen Berechtigungen, und weisen Sie die Rolle dann einem VMware-Benutzer oder einer VMware-Gruppe zu.<br/><br/> Rechenzentrumsobjekt -> An untergeordnetes Objekt weitergeben, Rolle=Azure_Site_Recovery<br/><br/> Datenspeicher -> Speicherplatz zuordnen, Datenspeicher durchsuchen, Low-Level-Dateivorgänge, Datei entfernen, Dateien virtueller Computer aktualisieren<br/><br/> Netzwerk -> Netzwerk zuweisen<br/><br/> Ressource -> Zuweisen der VM zu einem Ressourcenpool, ausgeschaltete VM migrieren, eingeschaltete VM migrieren<br/><br/> Tasks (Aufgaben) -> Create task (Aufgabe erstellen), update task (Aufgabe aktualisieren)<br/><br/> Virtueller Computer -> Konfiguration<br/><br/> Virtueller Computer -> Interagieren -> Frage beantworten, Geräteverbindung, CD-Medien konfigurieren, Diskettenmedien konfigurieren, Ausschalten, Einschalten, VMware-Tools installieren<br/><br/> Virtueller Computer -> Inventar -> Erstellen, Registrieren, Registrierung aufheben<br/><br/> Virtueller Computer -> Bereitstellung -> Download virtueller Computer zulassen, Upload von Dateien virtueller Computer zulassen<br/><br/> Virtual machine -> Snapshots -> Remove snapshots | Der Benutzer wird auf Datencenterebene zugewiesen und hat Zugriff auf alle Objekte im Datencenter.<br/><br/> Um den Zugriff einzuschränken, weisen Sie den untergeordneten Objekten (vSphere-Hosts, Datenspeicher, VMs und Netzwerke) die Rolle **No access** (Kein Zugriff) mit dem Objekt **Propagate to child** (An untergeordnetes Element weitergeben) zu.

### <a name="prepare-an-account-for-mobility-service-installation"></a>Vorbereiten eines Kontos für die Installation des Mobility Services

Der Mobilitätsdienst muss auf Computern installiert sein, die Sie replizieren möchten.

- Die Azure Migrate-Replikationsappliance kann eine Pushinstallation dieses Diensts ausführen, wenn Sie die Replikation für einen Computer aktivieren. Sie können den Dienst aber auch manuell oder mithilfe von Installationstools installieren.
- In diesem Tutorial installieren wir den Mobilitätsdienst mit der Pushinstallation.
- Für die Pushinstallation müssen Sie ein Konto vorbereiten, über das die Azure Migrate-Servermigration auf die VM zugreifen kann. Dieses Konto wird nur für die Pushinstallation verwendet, wenn Sie den Mobilitätsdienst nicht manuell installieren.

Bereiten Sie das Konto wie folgt vor:

1. Bereiten Sie ein Domänen- oder lokales Konto mit den Berechtigungen zum Installieren auf dem virtuellen Computer vor.
2. Für Windows-VMs gilt: Wenn Sie kein Domänenkonto verwenden, deaktivieren Sie die Remote-Benutzerzugriffssteuerung auf dem lokalen Computer, indem Sie in der Registrierung unter **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** den DWORD-Eintrag **LocalAccountTokenFilterPolicy** mit dem Wert „1“ hinzufügen.
3. Bereiten Sie für Linux-VMs ein root-Konto auf dem Linux-Quellserver vor.


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Vorbereiten eines Computers für die Replikationsappliance

Die Appliance wird verwendet, um Computer in Azure zu replizieren. Bei der Appliance handelt es sich um eine einzelne hochverfügbare lokale VMware-VM, auf der folgende Komponenten gehostet werden:

- **Konfigurationsserver**: Der Konfigurationsserver koordiniert die Kommunikation zwischen der lokalen Umgebung und Azure und verwaltet die Datenreplikation.
- **Prozessserver** Der Prozessserver fungiert als Replikationsgateway. Er empfängt Replikationsdaten, optimiert sie durch Zwischenspeicherung, Komprimierung und Verschlüsselung und sendet sie an ein Cachespeicherkonto in Azure. Der Prozessserver installiert auch den Mobility Service-Agent auf VMs, die Sie replizieren möchten, und führt eine automatische Ermittlung der lokalen VMware-VMs durch.

Bereiten Sie die Appliance wie folgt vor:

- [Lesen Sie die Applianceanforderungen](migrate-replication-appliance.md#appliance-requirements). Im Allgemeinen richten Sie die Replikationsappliance als VMware-VM ein, indem Sie eine heruntergeladene OVA-Datei verwenden. Mit der Vorlage wird eine Appliance erstellt, die alle Anforderungen erfüllt.
- MySQL muss auf der Appliance installiert sein. [Informieren Sie sich über die Installationsmethoden](migrate-replication-appliance.md#mysql-installation).
- Lesen Sie die Informationen zu den [URLs für die öffentliche Cloud](migrate-replication-appliance.md#url-access) und den [Azure Government-URLs](migrate-replication-appliance.md#azure-government-url-access), auf die der Appliancecomputer Zugriff haben muss.
- [Informieren Sie sich über die Ports](migrate-replication-appliance.md#port-access), auf die der Replikationsappliance-Computer Zugriff haben muss.



### <a name="check-vmware-requirements"></a>Überprüfen der VMware-Anforderungen

Vergewissern Sie sich, dass die VMware-Server und -VMs die Anforderungen für die Migration zu Azure erfüllen. 

1. [Überprüfen](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) Sie die Anforderungen für VMware-Server.
2. [Überprüfen](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) Sie die VM-Anforderungen für die Migration.
3. Überprüfen Sie die Azure-Einstellungen. Lokale VMs, die Sie zu Azure replizieren möchten, müssen die [Azure-VM-Anforderungen](migrate-support-matrix-vmware-migration.md#azure-vm-requirements) erfüllen.
4. Auf VMs sind einige Änderungen erforderlich, bevor Sie sie zu Azure migrieren.
    - Es ist wichtig, diese Änderungen vorzunehmen, bevor Sie mit der Migration beginnen. Wenn Sie den virtuellen Computer migrieren, bevor Sie die Änderung vorgenommen haben, wird der virtuelle Computer in Azure unter Umständen nicht gestartet.
    - Überprüfen Sie die [Windows](prepare-for-migration.md#windows-machines)- und [Linux](prepare-for-migration.md#linux-machines)-Änderungen, die Sie vornehmen müssen.

> [!NOTE]
> Die Agent-basierte Migration mit der Azure Migrate-Servermigration basiert auf Features des Azure Site Recovery-Diensts. Einige Anforderungen sind daher ggf. mit der Site Recovery-Dokumentation verknüpft.

## <a name="set-up-the-replication-appliance"></a>Einrichten der Replikationsappliance

In diesem Verfahren wird beschrieben, wie Sie die Appliance mit einer heruntergeladenen OVA-Vorlage (Open Virtualization Application) einrichten. Falls diese Methode für Sie nicht möglich ist, können Sie die Appliance auch [mit einem Skript](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance) einrichten. 

### <a name="download-the-replication-appliance-template"></a>Herunterladen der Vorlage für die Replikationsappliance

Gehen Sie wie folgt vor, um die Vorlage herunterzuladen:

1. Klicken Sie im Azure Migrate-Projekt unter **Migrationsziele** auf **Server**.
2. Klicken Sie unter **Azure Migrate – Server** > **Azure Migrate: Servermigration** auf **Ermitteln**.

    ![VMs ermitteln](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. Wählen Sie unter **Computer ermitteln** > **Sind Ihre Computer virtualisiert?** die Option **Ja, mit VMware vSphere Hypervisor** aus.
4. Wählen Sie unter **Wie möchten Sie die Migration durchführen?** die Option **Mit der Agent-basierten Replikation** aus.
5. Wählen Sie unter **Zielregion** die Azure-Region aus, zu der Sie die Computer migrieren möchten.
6. Aktivieren Sie das Kontrollkästchen **Bestätigen Sie, dass die Zielregion für die Migration „<Name der Region>“ lautet**.
7. Klicken Sie auf **Ressourcen erstellen**. Daraufhin wird im Hintergrund ein Azure Site Recovery-Tresor erstellt. Nachdem Sie auf diese Schaltfläche geklickt haben, können Sie die Zielregion für dieses Projekt nicht mehr ändern, und alle nachfolgenden Migrationen werden zu dieser Region durchgeführt.

    ![Erstellen eines Recovery Services-Tresors](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. Wählen Sie unter **Möchten Sie eine neue Replikationsappliance installieren oder ein vorhandenes Setup horizontal hochskalieren?** die Option **Replikationsappliance installieren**.
9. Klicken Sie auf **Download**. Eine OVF-Vorlage wird heruntergeladen.
    ![Herunterladen der OVA-Vorlage](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Notieren Sie sich den Namen der Ressourcengruppe und des Recovery Services-Tresors. Sie werden bei der Appliancebereitstellung benötigt.


### <a name="import-the-template-in-vmware"></a>Importieren der Vorlage in VMware

Die heruntergeladene OVF-Vorlage muss in VMware importiert werden, um die Replikationsappliance auf einer VMware-VM unter Windows Server 2016 zu erstellen.

1. Melden Sie sich mit dem VMware vSphere-Client beim VMware vCenter-Server oder beim vSphere ESXi-Host an.
2. Wählen Sie im Menü **Datei** die Option **OVF-Vorlage bereitstellen** aus, um den **Assistenten zum Bereitstellen von OVF-Vorlagen** zu starten. 
3. Geben Sie unter **Quelle auswählen** den Speicherort der heruntergeladenen OVF-Vorlage ein.
4. Klicken Sie unter **Bewertungsdetails** auf **Weiter**.
5. Übernehmen Sie in **Name und Ordner auswählen** und **Konfiguration auswählen** die Standardeinstellungen.
6. Wählen Sie unter **Speicher auswählen** > **Select virtual disk format** (Format für virtuellen Datenträger auswählen) die Option **Thick Provision Eager Zeroed** (Breite Bereitstellung gegen null) aus, um eine optimale Leistung zu erzielen.
7. Übernehmen Sie auf den übrigen Seiten des Assistenten die Standardeinstellungen.
8. Wählen Sie unter **Zum Abschluss bereit** zum Einrichten des virtuellen Computers mit den Standardeinstellungen **Power on after deployment** (Nach Bereitstellung einschalten) > **Fertig stellen** aus.

   > [!TIP]
   > Wenn Sie einen weiteren Netzwerkadapter hinzufügen möchten, deaktivieren Sie **Power on after deployment** (Nach Bereitstellung einschalten) > **Fertig stellen**. Standardmäßig enthält die Vorlage einen einzelnen Netzwerkadapter. Weitere NICs können nach der Bereitstellung hinzugefügt werden.

### <a name="start-appliance-setup"></a>Starten der Einrichtung der Appliance

1. Schalten Sie den virtuellen Computer über die Konsole des VMware vSphere-Clients ein. Der virtuelle Computer wird mit der Benutzeroberfläche für die Installation von Windows Server 2016 hochgefahren.
2. Akzeptieren Sie den Lizenzvertrag, und geben Sie ein Administratorkennwort ein.
3. Melden Sie sich nach Abschluss der Installation unter Verwendung des Administratorkennworts als Administrator bei der VM an. Bei der ersten Anmeldung wird innerhalb weniger Sekunden das Setuptool für die Replikationsappliance (Azure Site Recovery-Konfigurationstool) gestartet.
5. Geben Sie einen Namen ein, um die Appliance für die Servermigration zu registrieren. Klicken Sie dann auf **Weiter**.
6. Das Tool überprüft, ob der virtuelle Computer eine Verbindung mit Azure herstellen kann. Klicken Sie nach der Verbindungsherstellung auf **Anmelden**, um sich bei Ihrem Azure-Abonnement anzumelden.
7. Warten Sie, bis das Tool eine Azure AD-App zur Identifizierung der Appliance registriert hat. Die Appliance wird neu gestartet.
1. Melden Sie sich erneut am Computer an. Der Assistent für die Konfigurationsserververwaltung wird innerhalb weniger Sekunden automatisch gestartet.

### <a name="register-the-replication-appliance"></a>Registrieren der Replikationsappliance

Schließen Sie die Einrichtung und Registrierung der Replikationsappliance ab.

1. Wählen Sie beim Einrichten der Appliance die Option **Konnektivität einrichten** aus.
2. Wählen Sie die NIC aus, die die Replikationsappliance für die VM-Ermittlung sowie für eine Pushinstallation des Mobility-Diensts auf Quellcomputern verwendet. (Standardmäßig ist nur eine NIC verfügbar.)
3. Wählen Sie die NIC aus, die die Replikationsappliance für die Konnektivität mit Azure verwendet. Klicken Sie dann auf **Speichern**. Diese Einstellung kann nach der Konfiguration nicht mehr geändert werden.
4. Falls sich die Appliance hinter einem Proxyserver befindet, müssen Proxyeinstellungen angegeben werden.
    - Geben Sie den Proxynamen im Format **http://ip-address** oder **http://FQDN** an. HTTPS-Proxyserver werden nicht unterstützt.
5. Wenn Sie zur Eingabe des Abonnements, der Ressourcengruppen und der Tresordetails aufgefordert werden, fügen Sie die Angaben hinzu, die Sie sich beim Herunterladen der Appliancevorlage notiert haben.
6. Akzeptieren Sie unter **Drittanbietersoftware installieren** den Lizenzvertrag. Klicken Sie auf **Herunterladen und installieren**, um MySQL-Server zu installieren.
7. Klicken Sie auf **VMware PowerCLI installieren**. Stellen Sie sicher, dass alle Browserfenster geschlossen sind, bevor Sie diesen Schritt durchführen. Klicken Sie anschließend auf **Weiter**.
8. Unter **Anwendungskonfiguration überprüfen** werden die Voraussetzungen überprüft, bevor der Vorgang fortgesetzt wird.
9. Geben Sie unter **vCenter-Server/vSphere ESXi-Server konfigurieren** den FQDN oder die IP-Adresse des vCenter-Servers oder vSphere-Hosts ein, auf dem sich die virtuellen Computer befinden, die repliziert werden sollen. Geben Sie den Port ein, über den der Server lauscht. Geben Sie einen Anzeigenamen ein, der für den VMware-Server im Tresor verwendet werden soll.
10. Geben Sie die Anmeldeinformationen für das Konto ein, das Sie für die VMware-Ermittlung [erstellt](#prepare-an-account-to-discover-vms) haben. Wählen Sie **Hinzufügen** > **Weiter** aus.
11. Geben Sie beim Aktivieren der VM-Replikation unter **Anmeldeinformationen für virtuelle Computer konfigurieren** die Anmeldeinformationen ein, die Sie für die Pushinstallation des Mobility-Diensts [erstellt](#prepare-an-account-for-mobility-service-installation) haben.  
    - Für Windows-Computer benötigt das Konto lokale Administratorrechte auf den Computern, die Sie replizieren möchten.
    - Bei Linux geben Sie die Anmeldeinformationen für das root-Konto an.
12. Klicken Sie auf **Konfiguration abschließen**, um die Registrierung abzuschließen.


Nachdem die Replikationsappliance registriert wurde, stellt die Azure Migrate-Serverbewertung unter Verwendung der angegebenen Einstellungen eine Verbindung mit VMware-Servern her und ermittelt VMs. Die ermittelten VMs werden unter **Verwalten** > **Ermittelte Elemente** auf der Registerkarte **Sonstiges** angezeigt.



## <a name="replicate-vms"></a>Replizieren von VMs

Wählen Sie virtuelle Computer für die Migration aus.

> [!NOTE]
> Im Portal können für die Replikation bis zu zehn virtuelle Computer gleichzeitig ausgewählt werden. Wenn Sie eine größere Zahl replizieren müssen, können Sie jeweils Zehnergruppen verwenden.

1. Klicken Sie im Azure Migrate-Projekt unter **Server** > **Azure Migrate: Servermigration** auf **Replizieren**.

    ![Screenshot: Bildschirm „Server“ in Azure Migrate. Die Schaltfläche „Replizieren“ ist unter „Azure Migrate: Servermigration“ im Abschnitt „Migrationstools“ hervorgehoben.](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. Wählen Sie unter **Replizieren** > **Quelleinstellungen** > **Sind Ihre Computer virtualisiert?** die Option **Ja, mit VMware vSphere-Hypervisor** aus.
3. Wählen Sie unter **Lokale Appliance** den Namen der Azure Migrate-Appliance aus, die Sie eingerichtet haben.
4. Geben Sie unter **vCenter Server** den Namen des vCenter-Servers an, der die virtuellen Computer verwaltet, oder den vSphere-Server, auf dem die VMs gehostet werden.
5. Wählen Sie unter **Prozessserver** den Namen der Replikationsappliance aus.
6. Geben Sie unter **Gastanmeldeinformationen** das VM-Administratorkonto an, das für die Pushinstallation des Mobility-Diensts verwendet wird. Klicken Sie anschließend auf **Next: Virtuelle Computer**.

    ![Screenshot: Registerkarte „Quelleinstellungen“ auf dem Bildschirm „Replizieren“. Das Feld „Gastanmeldeinformationen“ ist hervorgehoben, und der Wert ist auf „VM-admin-account“ festgelegt.](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. Wählen Sie unter **Virtuelle Computer** die Computer aus, die Sie replizieren möchten.

    - Wenn Sie eine Bewertung für die VMs ausgeführt haben, können Sie die Empfehlungen zur VM-Größenanpassung und zum Datenträgertyp (Premium/Standard) aus den Bewertungsergebnissen anwenden. Wählen Sie hierzu unter **Migrationseinstellungen aus einer Azure Migrate-Bewertung importieren?** die Option **Ja** aus.
    - Wählen Sie **Nein** aus, wenn Sie keine Bewertung ausgeführt haben oder die Bewertungseinstellungen nicht verwenden möchten.
    - Falls Sie sich für die Verwendung der Bewertung entschieden haben, wählen Sie die VM-Gruppe und den Bewertungsnamen aus.
8. Wählen Sie unter **Verfügbarkeitsoptionen** Folgendes aus:
    -  Verfügbarkeitszone, um den migrierten Computer einer bestimmten Verfügbarkeitszone in der Region anzuheften. Verteilen Sie mit dieser Option Server, die eine Anwendungsebene mit mehreren Knoten bilden, über Verfügbarkeitszonen. Wenn Sie diese Option auswählen, müssen Sie die Verfügbarkeitszone angeben, die für jeden auf der Registerkarte „Compute“ ausgewählten Computer verwendet werden soll. Diese Option ist nur verfügbar, wenn die für die Migration ausgewählte Zielregion Verfügbarkeitszonen unterstützt.
    -  Verfügbarkeitsgruppe, um den migrierten Computer in einer Verfügbarkeitsgruppe zu platzieren. Um diese Option verwenden zu können, muss die ausgewählte Zielressourcengruppe über mindestens eine Verfügbarkeitsgruppe verfügen.
    - Die Infrastrukturredundanzoption ist nicht erforderlich, wenn Sie keine dieser Verfügbarkeitskonfigurationen für die migrierten Computer benötigen.
9. Wählen Sie alle VMs aus, die Sie migrieren möchten. Klicken Sie anschließend auf **Next: Zieleinstellungen**.
10. Wählen Sie unter **Zieleinstellungen** das Abonnement und die Zielregion für die Migration aus, und geben Sie die Ressourcengruppe an, in der sich die Azure-VMs nach der Migration befinden.
11. Wählen Sie unter **Virtuelles Netzwerk** das Azure-VNET/-Subnetz aus, in das die Azure-VMs nach der Migration eingebunden werden.
12. Wählen Sie unter **Verfügbarkeitsoptionen** Folgendes aus:
    -  Verfügbarkeitszone, um den migrierten Computer einer bestimmten Verfügbarkeitszone in der Region anzuheften. Verteilen Sie mit dieser Option Server, die eine Anwendungsebene mit mehreren Knoten bilden, über Verfügbarkeitszonen. Wenn Sie diese Option auswählen, müssen Sie die Verfügbarkeitszone angeben, die für jeden auf der Registerkarte „Compute“ ausgewählten Computer verwendet werden soll. Diese Option ist nur verfügbar, wenn die für die Migration ausgewählte Zielregion Verfügbarkeitszonen unterstützt.
    -  Verfügbarkeitsgruppe, um den migrierten Computer in einer Verfügbarkeitsgruppe zu platzieren. Um diese Option verwenden zu können, muss die ausgewählte Zielressourcengruppe über mindestens eine Verfügbarkeitsgruppe verfügen.
    - Die Infrastrukturredundanzoption ist nicht erforderlich, wenn Sie keine dieser Verfügbarkeitskonfigurationen für die migrierten Computer benötigen.
    
13. Wählen Sie unter **Azure-Hybridvorteil**

    - die Option **Nein** aus, falls Sie den Azure-Hybridvorteil nicht anwenden möchten. Klicken Sie dann auf **Weiter**.
    - Wählen Sie **Ja** aus, wenn Sie über Windows Server-Computer verfügen, die durch aktive Software Assurance- oder Windows Server-Abonnements abgedeckt sind, und den Vorteil auf die zu migrierenden Computer anwenden möchten. Klicken Sie dann auf **Weiter**.

14. Überprüfen Sie in **Compute** Name, Größe, Betriebssystem- Datenträger und Verfügbarkeitskonfiguration der VM (falls im vorherigen Schritt ausgewählt). Die VMs müssen die [Azure-Anforderungen](migrate-support-matrix-vmware-migration.md#azure-vm-requirements) erfüllen.

   - **VM-Größe**: Bei Verwendung von Bewertungsempfehlungen zeigt die Dropdownliste für die VM-Größe die empfohlene Größe. Andernfalls wählt Azure Migrate eine Größe basierend auf der höchsten Übereinstimmung im Azure-Abonnement aus. Alternativ können Sie unter **Azure-VM-Größe** manuell eine Größe auswählen. 
    - **Betriebssystemdatenträger**: Geben Sie den Betriebssystemdatenträger (Startdatenträger) für die VM an. Der Betriebssystemdatenträger enthält den Bootloader und das Installationsprogramm des Betriebssystems. 
    - **Verfügbarkeitszone**: Geben Sie die zu verwendende Verfügbarkeitszone an.
    - **Verfügbarkeitsgruppe**: Geben Sie die zu verwendende Verfügbarkeitsgruppe an.

15. Geben Sie unter **Datenträger** an, ob die VM-Datenträger in Azure repliziert werden sollen, und wählen Sie den Datenträgertyp (SSD Standard/HDD Standard oder Managed Disks Premium) in Azure aus. Klicken Sie dann auf **Weiter**.
    - Sie können Datenträger von der Replikation ausschließen.
    - Wenn Sie Datenträger ausschließen, sind diese nach der Migration nicht auf der Azure-VM vorhanden. 

16. Überprüfen Sie unter **Replikation prüfen und starten** die Einstellungen, und klicken Sie auf **Replizieren**, um die erste Replikation für die Server zu starten.

> [!NOTE]
> Sie können die Replikationseinstellungen vor Beginn der Replikation jederzeit unter **Verwalten** > **Aktuell replizierte Computer** aktualisieren. Die Einstellungen können nach dem Beginn der Replikation nicht mehr geändert werden.


## <a name="track-and-monitor"></a>Nachverfolgen und Überwachen

1. Verfolgen Sie den Auftragsstatus in den Portalbenachrichtigungen. 

    ![Nachverfolgen des Auftrags](./media/tutorial-migrate-vmware-agent/jobs.png)
    
2. Klicken Sie zum Überwachen des Replikationsstatus auf **Server werden repliziert** (in **Azure Migrate: Servermigration**).

    ![Überwachen der Replikation](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

Die Replikation wird wie folgt durchgeführt:
- Nachdem der Auftrag „Replikation starten“ erfolgreich abgeschlossen wurde, beginnen die Computer mit der ersten Replikation in Azure.
- Nach Abschluss der ersten Replikation beginnt die Deltareplikation. Inkrementelle Änderungen an lokalen Datenträgern werden regelmäßig auf den Replikatdatenträgern in Azure repliziert.


## <a name="run-a-test-migration"></a>Ausführen einer Testmigration


Wenn die Deltareplikation beginnt, können Sie zunächst eine Testmigration für die VMs ausführen, bevor eine vollständige Migration zu Azure erfolgt. Dieser Schritt sollte vor der Migration mindestens einmal für jeden Computer ausgeführt werden.

- Bei einer Testmigration wird überprüft, ob die Migration wie erwartet funktioniert, ohne die lokalen Computer zu beeinträchtigen, und ob diese während der Replikation betriebsbereit bleiben. 
- Mit der Testmigration wird die Migration simuliert, indem eine Azure-VM mit replizierten Daten erstellt wird (normalerweise per Migration zu einem nicht für die Produktion bestimmten VNET unter Ihrem Azure-Abonnement).
- Sie können den replizierten virtuellen Azure-Testcomputer verwenden, um die Migration zu überprüfen, App-Tests durchzuführen und Probleme zu beheben, bevor die vollständige Migration erfolgt.

Führen Sie die Testmigration wie folgt durch:


1. Klicken Sie unter **Migrationsziele** > **Server** > **Azure Migrate: Servermigration** auf **Migrierte Server testen**.

     ![Testen der migrierten Server](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Klicken Sie mit der rechten Maustaste auf die zu testende VM, und klicken Sie anschließend auf **Testmigration**.

    ![Testmigration](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. Wählen Sie unter **Testmigration** das Azure VNET aus, in dem sich die Azure-VM nach der Migration befindet. Es empfiehlt sich, ein nicht für die Produktion bestimmtes VNET zu verwenden.
4. Der Auftrag **Testmigration** wird gestartet. Überwachen Sie den Auftrag anhand der Portalbenachrichtigungen.
5. Zeigen Sie die migrierte Azure-VM nach Abschluss der Migration im Azure-Portal unter **Virtuelle Computer** an. Der Computername enthält das Suffix **-Test**.
6. Klicken Sie nach Abschluss des Tests mit der rechten Maustaste unter **Aktuell replizierte Computer** auf die Azure-VM, und klicken Sie anschließend auf **Testmigration bereinigen**.

    ![Bereinigen der Migration](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Migrieren virtueller Computer

Nachdem Sie sich vergewissert haben, dass die Testmigration wie erwartet funktioniert, können Sie die lokalen Computer migrieren.

1. Klicken Sie im Azure Migrate-Projekt unter **Server** > **Azure Migrate: Servermigration** auf **Server werden repliziert**.

    ![Replizieren der Server](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. Klicken Sie unter **Aktuell replizierte Computer** mit der rechten Maustaste auf die VM und dann auf **Migrieren**.
3. Wählen Sie unter **Migrieren** > **Virtuelle Computer herunterfahren und eine geplante Migration ohne Datenverlust durchführen?** die Option **Ja** >  und anschließend **OK** aus.
    - Azure Migrate fährt den lokalen virtuellen Computer standardmäßig herunter, um minimalen Datenverlust sicherzustellen. 
    - Falls Sie die VM nicht herunterfahren möchten, wählen Sie **Nein** aus.
4. Für den virtuellen Computer wird ein Migrationsauftrag gestartet. Verfolgen Sie den Auftrag anhand der Azure-Benachrichtigungen nach.
5. Nach Abschluss des Auftrags können Sie die VM auf der Seite **Virtuelle Computer** anzeigen und verwalten.

## <a name="complete-the-migration"></a>Fertigstellen der Migration

1. Klicken Sie nach Abschluss der Migration mit der rechten Maustaste auf die VM und dann auf **Migration beenden**. Die folgenden Schritte werden ausgeführt:
    - Beendet die Replikation für den lokalen Computer.
    - Entfernt den Computer aus dem Zähler **Server werden repliziert.** in Azure Migrate: Servermigration.
    - Bereinigt die Replikationsstatusinformationen für den virtuellen Computer.
2. Installieren Sie den [Windows](../virtual-machines/extensions/agent-windows.md)- oder [Linux](../virtual-machines/extensions/agent-linux.md)-Agent für die Azure-VM auf den migrierten Computern.
3. Führen Sie App-Anpassungen nach der Migration durch, z.B. die Aktualisierung von Datenbankverbindungszeichenfolgen und Webserverkonfigurationen.
4. Führen Sie endgültige Anwendungs- und Migrationsakzeptanztests für die migrierte Anwendung durch, die nun in Azure ausgeführt wird.
5. Leiten Sie den Datenverkehr auf die migrierte Instanz der Azure-VM um.
6. Entfernen Sie die lokalen VMs aus Ihrem lokalen VM-Bestand.
7. Entfernen Sie die lokalen VMs aus lokalen Sicherungen.
8. Aktualisieren Sie die interne Dokumentation zum Anzeigen des neuen Speicherorts und der IP-Adresse der Azure-VMs. 

## <a name="post-migration-best-practices"></a>Bewährte Methoden nach der Migration

- Lokal
    - Migrieren Sie den App-Datenverkehr zur App, die auf der migrierten Azure-VM-Instanz ausgeführt wird.
    - Entfernen Sie die lokalen VMs aus Ihrem lokalen VM-Bestand.
    - Entfernen Sie die lokalen VMs aus lokalen Sicherungen.
    - Aktualisieren Sie die interne Dokumentation zum Anzeigen des neuen Speicherorts und der IP-Adresse der Azure-VMs.
- Optimieren der Azure-VM-Einstellungen nach der Migration:
    - Der [Azure-VM-Agent](../virtual-machines/extensions/agent-windows.md) verwaltet VM-Interaktionen mit dem Azure Fabric Controller. Dieser ist für einige Azure-Dienste wie Azure Backup, Site Recovery und das Azure Security Center erforderlich. Beim Migrieren von virtuellen VMare-Computern mit Agent-basierter Migration installiert das Mobility Service-Installationsprogramm den Azure-VM-Agent auf Windows-Computern. Auf virtuellen Linux-Computern empfehlen wir, den Agent nach der Migration zu installieren.
    - Deinstallieren Sie den Mobility Service nach der Migration manuell von dem virtuellen Azure-Computer.
    - Deinstallieren Sie VMware-Tools nach der Migration manuell.
- In Azure:
    - Führen Sie App-Anpassungen nach der Migration durch, z.B. die Aktualisierung von Datenbankverbindungszeichenfolgen und Webserverkonfigurationen.
    - Führen Sie endgültige Anwendungs- und Migrationsakzeptanztests für die migrierte Anwendung durch, die nun in Azure ausgeführt wird.
- Geschäftskontinuität/Notfallwiederherstellung
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
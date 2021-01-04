---
title: Sichern von VMware-VMs mit Azure Backup Server
description: In diesem Artikel erfahren Sie, wie Sie Azure Backup Server verwenden, um VMware-VMs zu sichern, die auf einem VMware vCenter-/ESXi-Server ausgeführt werden.
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: db5e5c4bdac64e2faf5babb107ecec61a02d6468
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002952"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Sichern von VMware-VMs mit Azure Backup Server

In diesem Artikel wird erläutert, wie Sie auf VMware ESXi-Hosts/vCenter Server-Instanzen ausgeführte VMware-VMs mithilfe von Microsoft Azure Backup Server in Azure (MABS) sichern.

In diesem Artikel wird Folgendes erläutert:

- Einrichten eines sicheren Kanals, damit Azure Backup Server mit VMware-Servern über HTTPS kommunizieren kann
- Einrichten eines VMware-Kontos, das Azure Backup Server zum Zugreifen auf den VMware-Server verwendet
- Hinzufügen der Kontoanmeldeinformationen zu Azure Backup
- Hinzufügen des vCenter- oder ESXi-Servers zu Azure Backup Server
- Einrichten einer Schutzgruppe, die die zu sichernden VMware-VMs enthält, Angeben der Sicherungseinstellungen und Planen der Sicherung

## <a name="supported-vmware-features"></a>Unterstützte VMware-Features

MABS bietet beim Sichern von VMware-VMs die folgenden Features:

- Sicherung ohne Agents: Für MABS ist es nicht erforderlich, einen Agent auf dem vCenter- oder ESXi-Server zu installieren, um den virtuellen Computer zu sichern. Stattdessen geben Sie einfach die IP-Adresse oder den vollqualifizierten Domänennamen (FQDN) und die Anmeldeinformationen an, mit denen der VMware-Server bei MABS authentifiziert wird.
- In die Cloud integrierte Sicherung: MABS schützt Workloads durch Sicherung auf Datenträgern und in der Cloud. Mit dem Sicherungs- und Wiederherstellungsworkflow von MABS können Sie die Langzeitaufbewahrung und externe Sicherungen verwalten.
- Erkennen und Schützen von mit vCenter verwalteten VMs: Azure Backup Server erkennt und schützt VMs, die auf einem VMware-Server (vCenter oder ESXi) bereitgestellt sind. Wenn die Größe Ihrer Bereitstellung zunimmt, verwenden Sie vCenter zur Verwaltung der VMware-Umgebung. MABS erkennt auch von vCenter verwaltete VMs, sodass Sie große Bereitstellungen schützen können.
- Automatischer Schutz auf Ordnerebene: Mit vCenter können Sie Ihre VMs in VM-Ordnern organisieren. MABS erkennt diese Ordner, ermöglicht das Schützen von VMs auf Ordnerebene und schließt alle Unterordner ein. Beim Schützen von Ordnern schützt MABS nicht nur die VMs in diesen Ordnern, sondern auch später hinzugefügte VMs. MABS führt täglich eine VM-Erkennung durch und schützt sie automatisch. Wenn Sie Ihre VMs in rekursiven Ordnern anordnen, erkennt und schützt MABS automatisch die neuen VMs, die in den rekursiven Ordnern bereitgestellt werden.
- MABS schützt VMs, die auf einem lokalen Datenträger, in Network File System (NFS) oder Clusterspeicher gespeichert sind.
- MABS schützt zum Lastenausgleich migrierte VMs: Wenn VMs zum Lastenausgleich migriert werden, erkennt MABS automatisch den VM-Schutz und hält ihn aufrecht.
- MABS kann Dateien und Ordner in einer Windows-VM wiederherstellen, ohne die gesamte VM wiederherstellen zu müssen, wodurch notwendige Dateien schneller wiederhergestellt werden können.

## <a name="prerequisites-and-limitations"></a>Voraussetzungen und Einschränkungen

Bevor Sie damit beginnen, eine virtuelle VMware-Maschine zu sichern, überprüfen Sie die folgende Liste von Einschränkungen und Voraussetzungen.

- Wenn Sie MABS verwendet haben, um einen (unter Windows ausgeführten) vCenter-Server als Windows-Server unter Verwendung des FQDN des Servers zu schützen, können Sie diesen vCenter-Server nicht als VMware-Server unter Verwendung des FQDN des Servers schützen.
  - Sie können zur Problemumgehung die statische IP-Adresse des vCenter-Servers verwenden.
  - Wenn Sie den FQDN verwenden möchten, müssen Sie den Schutz als Windows-Server deaktivieren, den Schutz-Agent entfernen und den Server dann als VMware-Server unter Verwendung des FQDN hinzufügen.
- Wenn Sie vCenter zur Verwaltung von ESXi-Servern in Ihrer Umgebung verwenden, fügen Sie vCenter (nicht ESXi) zur MABS-Schutzgruppe hinzu.
- Vor der ersten MABS-Sicherung können Sie keine Momentaufnahmen von Benutzern sichern. Sobald MABS die erste Sicherung abgeschlossen hat, können Sie Momentaufnahmen von Benutzern sichern.
- MABS kann keine VMware-VMs mit Passthrough-Datenträgern und physischen Raw Device Mappings (pRDM) schützen.
- MABS kann keine VMware-vApps erkennen oder schützen.
- MABS kann keine VMware-VMs mit vorhandenen Momentaufnahmen schützen.

## <a name="before-you-start"></a>Vorbereitung

- Stellen Sie sicher, dass eine für die Sicherung unterstützte vCenter-/ESXi-Version ausgeführt wird. Weitere Informationen finden Sie [hier](./backup-mabs-protection-matrix.md) in der Unterstützungsmatrix.
- Stellen Sie sicher, dass Sie Azure Backup Server eingerichtet haben. Falls Sie dies noch nicht getan haben, [führen Sie diese Aufgabe aus](backup-azure-microsoft-azure-backup.md), bevor Sie beginnen. Azure Backup Server sollte mit den neuesten Updates ausgeführt werden.
- Stellen Sie sicher, dass die folgenden Netzwerkports geöffnet sind:
  - TCP 443 zwischen MABS und vCenter
  - TCP 443 und TCP 902 zwischen MABS und dem ESXi-Host

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Erstellen einer sichere Verbindung mit dem vCenter-Server

Azure Backup Server kommuniziert mit VMware-Servern standardmäßig über HTTPS. Zum Einrichten der HTTPS-Verbindung laden Sie das Zertifikat der VMware-Zertifizierungsstelle (Certificate Authority, CA) herunter und importieren es in Azure Backup Server.

### <a name="before-you-begin"></a>Voraussetzungen

- Wenn Sie HTTPS nicht verwenden möchten, können Sie die [Überprüfung des HTTPS-Zertifikats für alle VMware-Server deaktivieren](backup-azure-backup-server-vmware.md#disable-https-certificate-validation).
- In der Regel stellen Sie über einen Browser auf dem Azure Backup Server-Computer mithilfe von vSphere Web Client eine Verbindung mit dem vCenter-/ESXi-Server her. Beim ersten Mal ist die Verbindung nicht sicher, und es wird Folgendes angezeigt.
- Es ist wichtig, dass Sie verstehen, wie Azure Backup Server Sicherungen behandelt.
  - Im ersten Schritt sichert Azure Backup Server Daten auf dem lokalen Datenträgerspeicher. Azure Backup Server verwendet einen Speicherpool. Ein Speicherpool ist eine Gruppe von Datenträgern und Volumes, auf denen Azure Backup Server die Datenträger-Wiederherstellungspunkte für die geschützten Daten speichert. Bei dem Speicherpool kann es sich um direkt angeschlossenen Speicher (Directly Attached Storage, DAS), ein Fibre Channel-SAN oder ein iSCSI-Speichergerät oder-SAN handeln. Sie müssen unbedingt sicherstellen, dass genügend Speicherplatz für die lokale Sicherung Ihrer VMware-VM-Daten vorhanden ist.
  - Azure Backup Server sichert dann die Daten aus dem lokalen Datenträgerspeicher in Azure.
  - [Hier finden Sie Informationen](/system-center/dpm/create-dpm-protection-groups#figure-out-how-much-storage-space-you-need) zum Ermitteln des erforderlichen Speicherplatzes. Die Informationen gelten für DPM, können jedoch auch für Azure Backup Server verwendet werden.

### <a name="set-up-the-certificate"></a>Einrichten des Zertifikats

Richten Sie einen sicheren Kanal wie folgt ein:

1. Geben Sie im Browser auf dem Azure Backup Server-Computer die URL von vSphere Web Client ein. Wenn die Anmeldeseite nicht angezeigt wird, überprüfen Sie die Verbindungs- und Browserproxyeinstellungen.

    ![vSphere-Webclient](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. Wählen Sie auf der Anmeldeseite von vSphere Web Client **Vertrauenswürdige Zertifikate der Stammzertifizierungsstelle herunterladen** aus.

    ![Vertrauenswürdige Zertifikate der Stammzertifizierungsstelle herunterladen](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. Eine Datei namens **download** wird heruntergeladen. Abhängig von Ihrem Browser werden Sie gefragt, ob Sie die Datei öffnen oder speichern möchten.

    ![Zertifizierungsstellenzertifikat herunterladen](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Speichern Sie die Datei mit der Erweiterung ZIP auf dem Azure Backup Server-Computer.

5. Klicken Sie mit der rechten Maustaste auf **download.zip** > **Alle extrahieren**. Der Inhalt der ZIP-Datei wird in den Ordner **certs** extrahiert, der Folgendes enthält:
   - Die Stammzertifikatdatei mit einer Erweiterung, die mit einer nummerierten Sequenz wie „.0“ und „.1“ beginnt.
   - Die Erweiterung der CRL-Datei beginnt mit „.r0“ oder „.r1“. Die CRL-Datei ist einem Zertifikat zugeordnet.

    ![Heruntergeladene Zertifikate](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

6. Klicken Sie im Ordner **certs** mit der rechten Maustaste auf die Stammzertifikatdatei, und klicken Sie dann auf **Umbenennen**.

    ![Umbenennen des Stammzertifikats](./media/backup-azure-backup-server-vmware/rename-cert.png)

7. Ändern Sie die Erweiterung des Stammzertifikats in CRT, und bestätigen Sie den Vorgang. Das Dateisymbol ändert sich in das Symbol eines Stammzertifikats.

8. Klicken Sie mit der rechten Maustaste auf das Stammzertifikat, und wählen Sie im Kontextmenü die Option **Zertifikat installieren** aus.

9. Wählen Sie im Dialogfeld **Zertifikatimport-Assistent** als Ziel für das Zertifikat die Option **Lokaler Computer** und dann **Weiter** aus. Wenn Sie gefragt werden, ob Sie Änderungen am Computer zulassen möchten, bestätigen Sie dies.

    ![Willkommensseite des Assistenten](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

10. Wählen Sie auf der Seite **Zertifikatspeicher** die Option **Alle Zertifikate in folgendem Speicher speichern** und dann **Durchsuchen** aus, um den Zertifikatspeicher auszuwählen.

    ![Zertifikatspeicher](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

11. Wählen Sie im Dialogfeld **Zertifikatspeicher auswählen** als Zielordner für die Zertifikate die Option **Vertrauenswürdige Stammzertifizierungsstellen** und dann **OK** aus.

    ![Zielordner für Zertifikate](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

12. Überprüfen Sie unter **Fertigstellen des Assistenten** den angegebenen Ordner, und wählen Sie dann **Fertig stellen** aus.

    ![Prüfen, ob sich das Zertifikat im richtigen Ordner befindet](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

13. Melden Sie sich nach der Bestätigung des Zertifikatimports bei vCenter Server an, um sich zu vergewissern, dass Ihre Verbindung sicher ist.

### <a name="disable-https-certificate-validation"></a>Deaktivieren der Überprüfung des HTTPS-Zertifikats

Wenn in Ihrer Organisation sichere Grenzen eingerichtet wurden und Sie nicht das Protokoll HTTPS zwischen VMware-Servern und dem Azure Backup Server-Computer verwenden möchten, deaktivieren Sie HTTPS wie folgt:

1. Kopieren Sie den folgenden Text, und fügen Sie ihn in eine TXT-Datei ein.

    ```text
    Windows Registry Editor Version 5.00
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
    "IgnoreCertificateValidation"=dword:00000001
    ```

2. Speichern Sie die Datei unter dem Namen **DisableSecureAuthentication.reg** auf dem Azure Backup Server-Computer.

3. Doppelklicken Sie auf die Datei, um den Registrierungseintrag zu aktivieren.

## <a name="create-a-vmware-role"></a>Erstellen einer VMware-Rolle

Azure Backup Server benötigt ein Benutzerkonto mit Berechtigungen für den Zugriff auf vCenter Server/den ESXi-Host. Erstellen Sie eine VMware-Rolle mit bestimmten Berechtigungen, und ordnen Sie dann einem Benutzerkonto die Rolle zu.

1. Melden Sie sich beim vCenter Server-Computer an (bzw. beim ESXi-Host, wenn Sie nicht vCenter Server verwenden).
2. Wählen Sie im Bereich **Navigator** die Option **Verwaltung** aus.

    ![Verwaltung](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. Wählen Sie unter **Verwaltung** > **Rollen** das Symbol zum Hinzufügen von Rollen (das +-Symbol) aus.

    ![Hinzufügen einer Rolle](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

4. Geben Sie in **Rolle erstellen** > **Rollenname** die Zeichenfolge *BackupAdminRole* ein. Sie können als Rollennamen einen beliebigen Namen auswählen, es empfiehlt sich jedoch, einen aussagekräftigen Namen zu verwenden.

5. Wählen Sie die Privilegien, wie in der Tabelle unten zusammengefasst, und dann **OK** aus.  Die neue Rolle wird in der Liste im Bereich **Rollen** angezeigt.
   - Wählen Sie das Symbol neben der übergeordneten Bezeichnung aus, um das übergeordnete Element zu erweitern und die untergeordneten Berechtigungen anzuzeigen.
   - Um die Berechtigung „VirtualMachine“ auszuwählen, müssen Sie mehrere Ebenen der Hierarchie über- und untergeordneter Berechtigungen durchlaufen.
   - Sie müssen nicht alle untergeordneten Berechtigungen einer übergeordneten Berechtigung auswählen.

    ![Hierarchie über- und untergeordneter Berechtigungen](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Rollenberechtigungen

In der folgenden Tabelle werden die Berechtigungen erfasst, die Sie dem von Ihnen erstellten Benutzerkonto zuweisen müssen:

| Berechtigungen für vCenter 6.5-Benutzerkonto                          | Berechtigungen für vCenter 6.7-Benutzerkonto                            |
|----------------------------------------------------------------------------|----------------------------------------------------------------------------|
| Datastore cluster.Configure a datastore cluster                           | Datastore cluster.Configure a datastore cluster                           |
| Datastore.AllocateSpace                                                    | Datastore.AllocateSpace                                                    |
| Datastore.Browse datastore                                                 | Datastore.Browse datastore                                                 |
| Datastore.Low-level file operations                                        | Datastore.Low-level file operations                                        |
| Global.Disable methods                                                     | Global.Disable methods                                                     |
| Global.Enable methods                                                      | Global.Enable methods                                                      |
| Global.Licenses                                                            | Global.Licenses                                                            |
| Global.Log event                                                           | Global.Log event                                                           |
| Global.Manage custom attributes                                            | Global.Manage custom attributes                                            |
| Global.Set custom attribute                                                | Global.Set custom attribute                                                |
| Host.Local operations.Create virtual machine                               | Host.Local operations.Create virtual machine                               |
| Network.Assign network                                                     | Network.Assign network                                                     |
| Resource. Virtuellen Computer dem Ressourcenpool zuweisen                          | Resource. Virtuellen Computer dem Ressourcenpool zuweisen                          |
| vApp.Add virtual machine                                                   | vApp.Add virtual machine                                                   |
| vApp.Assign resource pool                                                  | vApp.Assign resource pool                                                  |
| vApp.Unregister                                                            | vApp.Unregister                                                            |
| VirtualMachine.Configuration. Hinzufügen oder Entfernen eines Geräts                         | VirtualMachine.Configuration. Hinzufügen oder Entfernen eines Geräts                         |
| Virtual machine.Configuration.Disk lease                                   | Virtual machine.Configuration.Acquire disk lease                           |
| Virtual machine.Configuration.Add new disk                                 | Virtual machine.Configuration.Add new disk                                 |
| Virtual machine.Configuration.Advanced                                     | Virtual machine.Configuration.Advanced configuration                       |
| Virtual machine.Configuration.Disk change tracking                         | Virtual machine.Configuration.Toggle disk change tracking                  |
| Virtual machine.Configuration.Host USB device                              | Virtual machine.Configuration.Configure Host USB device                    |
| Virtual machine.Configuration.Extend virtual disk                          | Virtual machine.Configuration.Extend virtual disk                          |
| Virtual machine.Configuration.Query unowned files                          | Virtual machine.Configuration.Query unowned files                          |
| Virtual machine.Configuration.Swapfile placement                           | Virtual machine.Configuration.Change Swapfile placement                    |
| Virtual machine.Guest Operations.Guest Operation Program Execution         | Virtual machine.Guest Operations.Guest Operation Program Execution         |
| Virtual machine.Guest Operations.Guest Operation Modifications             | Virtual machine.Guest Operations.Guest Operation Modifications             |
| Virtual machine.Guest Operations.Guest Operation Queries                   | Virtual machine.Guest Operations.Guest Operation Queries                   |
| Virtual machine .Interaction .Device connection                            | Virtual machine .Interaction .Device connection                            |
| Virtual machine .Interaction .Guest operating system management by VIX API | Virtual machine .Interaction .Guest operating system management by VIX API |
| Virtual machine.Interaction.Power Off                                    | Virtual machine.Interaction.Power Off                                    |
| Virtual machine.Inventory.Create new                                      | Virtual machine.Inventory.Create new                                      |
| Virtual machine .Inventory.Remove                                          | Virtual machine .Inventory.Remove                                          |
| Virtual machine .Inventory.Register                                        | Virtual machine .Inventory.Register                                        |
| Virtual machine .Provisioning.Allow disk access                            | Virtual machine .Provisioning.Allow disk access                            |
| Virtual machine .Provisioning.Allow file access                            | Virtual machine .Provisioning.Allow file access                            |
| Virtual machine .Provisioning.Allow read-only disk access                  | Virtual machine .Provisioning.Allow read-only disk access                  |
| Virtual machine .Provisioning.Allow virtual machine download               | Virtual machine .Provisioning.Allow virtual machine download               |
| Virtual machine .Snapshot management. Erstellen einer Momentaufnahme                      | Virtual machine .Snapshot management. Erstellen einer Momentaufnahme                      |
| Virtual machine .Snapshot management.Remove Snapshot                       | Virtual machine .Snapshot management.Remove Snapshot                       |
| Virtual machine .Snapshot management.Revert to snapshot                    | Virtual machine .Snapshot management.Revert to snapshot                    |

> [!NOTE]
> In der folgenden Tabelle sind die Berechtigungen für vCenter 6.0- und vCenter 5.5-Benutzerkonten aufgeführt.

| Berechtigungen für vCenter 6.0-Benutzerkonto | Berechtigungen für vCenter 5.5-Benutzerkonto |
| --- | --- |
| Datastore.AllocateSpace | Network.Assign |
| Global.Manage custom attributes | Datastore.AllocateSpace |
| Global.Set custom attribute | VirtualMachine.Config.ChangeTracking |
| Host.Local operations.Create virtual machine | VirtualMachine.State.RemoveSnapshot |
| Netzwerk Netzwerk zuweisen | VirtualMachine.State.CreateSnapshot |
| Resource. Virtuellen Computer dem Ressourcenpool zuweisen | VirtualMachine.Provisioning.DiskRandomRead |
| Virtual machine.Configuration.Add new disk | VirtualMachine.Interact.PowerOff |
| Virtual machine.Configuration.Advanced | VirtualMachine.Inventory.Create |
| Virtual machine.Configuration.Disk change tracking | VirtualMachine.Config.AddNewDisk |
| Virtual machine.Configuration.Host USB device | VirtualMachine.Config.HostUSBDevice |
| Virtual machine.Configuration.Query unowned files | VirtualMachine.Config.AdvancedConfig |
| Virtual machine.Configuration.Swapfile placement | VirtualMachine.Config.SwapPlacement |
| Virtual machine.Interaction.Power Off | Global.ManageCustomFields |
| Virtual machine.Inventory. Neu erstellen |   |
| Virtual machine.Provisioning.Allow disk access |   |
| Virtual machine.Provisioning. Schreibgeschützten Datenträgerzugriff zulassen |   |
| Virtual machine.Snapshot management.Create snapshot |   |
| Virtual machine.Snapshot management.Remove Snapshot |   |

## <a name="create-a-vmware-account"></a>Erstellen eines VMware-Kontos

1. Wählen Sie im vCenter Server-Bereich **Navigator** die Option **Benutzer und Gruppen** aus. Wenn Sie vCenter Server nicht verwenden, erstellen Sie das Konto auf dem entsprechenden ESXi-Host.

    ![Option „Benutzer und Gruppen“](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    Der vCenter-Bereich **Benutzer und Gruppen** wird angezeigt.

2. Wählen Sie im vCenter-Bereich **Benutzer und Gruppen** auf der Registerkarte **Benutzer** das Symbol zum Hinzufügen von Benutzern (+-Symbol) aus.

    ![Der vCenter-Bereich „Benutzer und Gruppen“](./media/backup-azure-backup-server-vmware/usersandgroups.png)

3. Fügen Sie im Dialogfeld **Neuer Benutzer** die Benutzerinformationen hinzu, und klicken Sie dann auf **OK**. In diesem Verfahren lautet der Benutzername „BackupAdmin“.

    ![Dialogfeld „Neuer Benutzer“](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

4. Um das Benutzerkonto der Rolle zuzuordnen, wählen Sie im Bereich **Navigator** die Option **Globale Berechtigungen** aus. Wählen Sie im Bereich **Globale Berechtigungen** auf der Registerkarte **Verwalten** das Symbol zum Hinzufügen (+-Symbol) aus.

    ![Bereich „Globale Berechtigungen“](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

5. Wählen Sie im Dialogfeld **Stamm: Globale Berechtigungen – Berechtigung hinzufügen** die Option **Hinzufügen** aus, um den Benutzer oder die Gruppe auszuwählen.

    ![Auswählen eines Benutzers oder einer Gruppe](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. Gehen Sie im Dialogfeld **Benutzer/Gruppen auswählen** wie folgt vor: Wählen Sie **BackupAdmin** > **Hinzufügen** aus. In **Benutzer** wird das Format *Domäne\Benutzername* für das Benutzerkonto verwendet. Wenn Sie eine andere Domäne verwenden möchten, wählen Sie sie in der Liste **Domäne** aus. Wählen Sie **OK** aus, um die ausgewählten Benutzer dem Dialogfeld **Berechtigung hinzufügen** hinzuzufügen.

    ![Benutzer „BackupAdmin“ hinzufügen](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

7. Wählen Sie im Bereich **Zugewiesene Rolle** in der Dropdownliste den Eintrag **BackupAdminRole** > **OK** aus.

    ![Zuweisen des Benutzers zur Rolle](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

Im Bereich **Globale Berechtigungen** werden auf der Registerkarte **Verwalten** das neue Benutzerkonto und die zugeordnete Rolle in der Liste angezeigt.

## <a name="add-the-account-on-azure-backup-server"></a>Hinzufügen des Kontos in Azure Backup Server

1. Öffnen Sie Azure Backup Server. Wenn Sie das Symbol auf dem Desktop nicht finden können, öffnen Sie Microsoft Azure Backup über die Liste der Apps.

    ![Azure Backup Server-Symbol](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. Wählen Sie in der Azure Backup Server-Konsole **Verwaltung** >  **Produktionsserver** > **VMware verwalten** aus.

    ![Azure Backup Server-Konsole](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

3. Wählen Sie im Dialogfeld **Anmeldeinformationen verwalten** die Option **Hinzufügen** aus.

    ![Dialogfeld „Anmeldeinformationen verwalten“](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. Geben Sie im Dialogfeld **Anmeldeinformationen hinzufügen** einen Namen und eine Beschreibung für die neuen Anmeldeinformationen sowie den auf dem VMware-Server definierten Benutzernamen und das zugehörige Kennwort ein. Der Name *Contoso Vcenter credential* (Contoso vCenter-Anmeldeinformationen) wird zum Identifizieren der Anmeldeinformationen in diesem Verfahren verwendet. Wenn sich der VMware-Server und Azure Backup Server nicht in derselben Domäne befinden, geben Sie die Domäne im Benutzernamen an.

    ![Azure Backup Server-Dialogfeld „Anmeldeinformationen hinzufügen“](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Wählen Sie **Hinzufügen** aus, um die neuen Anmeldeinformationen hinzuzufügen.

    ![Neue Anmeldeinformationen hinzufügen](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server"></a>Hinzufügen des vCenter-Servers

Fügen Sie den vCenter-Server zu Azure Backup Server hinzu.

1. Wählen Sie in der Azure Backup Server-Konsole **Verwaltung** > **Produktionsserver** > **Hinzufügen** aus.

    ![Öffnen des Assistenten zum Hinzufügen von Produktionsservern](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

2. Wählen Sie auf der Seite **Assistent zum Hinzufügen von Produktionsservern** > **Produktionsservertyp auswählen** die Option **VMware-Server** und dann **Weiter** aus.

    ![Assistent zum Hinzufügen von Produktionsservern](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. Geben Sie unter **Computer auswählen** im Feld **Servername/IP-Adresse** den vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) oder die IP-Adresse des VMware-Servers ein. Wenn alle ESXi-Server von der gleichen vCenter-Instanz verwaltet werden, geben Sie den vCenter-Namen an. Fügen Sie andernfalls den ESXi-Host hinzu.

    ![VMware-Server angeben](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. Geben Sie im Feld **SSL-Port** den Port ein, der für die Kommunikation mit dem VMware-Server verwendet wird. 443 ist der Standardport, Sie können diesen jedoch ändern, wenn der VMware-Server einen anderen Port abhört.

5. Wählen Sie im Dialogfeld **Anmeldeinformationen angeben** die zuvor erstellten Anmeldeinformationen aus.

    ![Angeben von Anmeldeinformationen](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Wählen Sie **Hinzufügen** aus, um den VMware-Server zur Liste der Server hinzuzufügen. Wählen Sie **Weiter** aus.

    ![Hinzufügen des VMware-Servers und von Anmeldeinformationen](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Wählen Sie auf der Seite **Zusammenfassung** die Option **Hinzufügen** aus, um den VMware-Server zu Azure Backup Server hinzuzufügen. Der neue Server wird sofort hinzugefügt. Auf dem VMware-Server wird kein Agent benötigt.

    ![Hinzufügen eines VMware-Servers zu Azure Backup Server](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. Überprüfen Sie die Einstellungen auf der Seite **Fertig stellen**.

   ![Seite „Fertig stellen“](./media/backup-azure-backup-server-vmware/summary-screen.png)

Wenn Sie über mehrere nicht von vCenter Server verwaltete ESXi-Hosts oder über mehrere vCenter Server-Instanzen verfügen, müssen Sie den Assistenten erneut ausführen, um die Server hinzuzufügen.

## <a name="configure-a-protection-group"></a>Konfigurieren einer Schutzgruppe

Fügen Sie VMware-VMs für die Sicherung hinzu. Schutzgruppen erfassen mehrere VMs und wenden auf alle VMs in der Gruppe die gleichen Datenaufbewahrungs- und Sicherungseinstellungen an.

1. Wählen Sie in der Azure Backup Server-Konsole **Schutz** > **Neu** aus.

    ![Öffnen des Assistenten zum Erstellen einer neuen Schutzgruppe](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Klicken Sie auf der Willkommensseite des Assistenten **Neue Schutzgruppe erstellen** auf **Weiter**.

    ![Dialogfeld „Assistent zum Erstellen einer neuen Schutzgruppe“](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Klicken Sie auf der Seite **Schutzgruppentyp auswählen** auf **Server** und dann auf **Weiter**. Die Seite **Gruppenmitglieder auswählen** wird angezeigt.

1. Wählen Sie auf der Seite **Gruppenmitglieder auswählen** die VMs (oder die VM-Ordner) aus, die Sie sichern möchten. Wählen Sie **Weiter** aus.

    - Wenn Sie einen Ordner auswählen, werden auch die VMs oder die Ordner in diesem Ordner für die Sicherung ausgewählt. Sie können Ordner oder VMs deaktivieren, die nicht gesichert werden sollen.
1. Wenn ein virtueller Computer oder ein Ordner bereits gesichert wird, kann er nicht ausgewählt werden. Dadurch wird sichergestellt, dass keine doppelten Wiederherstellungspunkte für einen virtuellen Computer erstellt werden.

    ![Gruppenmitglieder auswählen](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Geben Sie auf der Seite **Datenschutzmethode auswählen** einen Namen für die Schutzgruppe ein, und wählen Sie die Schutzeinstellungen aus. Legen Sie zum Sichern in Azure den kurzfristigen Schutz auf **Datenträger** fest, und aktivieren Sie den Onlineschutz. Wählen Sie **Weiter** aus.

    ![Datenschutzmethode auswählen](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Legen Sie auf der Seite **Kurzfristige Ziele angeben** fest, wie lange die gesicherten Daten auf dem Datenträger beibehalten werden sollen.
   - Geben Sie im Feld **Beibehaltungsdauer** die Anzahl von Tagen für die Aufbewahrung der Datenträger-Wiederherstellungspunkte an.
   - Geben Sie für **Synchronisierungsfrequenz** an, wie oft Datenträger-Wiederherstellungspunkte erstellt werden sollen.
       - Wenn Sie kein Sicherungsintervall festlegen möchten, können Sie **Direkt vor einem Wiederherstellungspunkt** aktivieren, damit unmittelbar vor jedem geplanten Wiederherstellungspunkt eine Sicherung ausgeführt wird.
       - Kurzfristige Sicherungen sind vollständige Sicherungen und keine inkrementellen Sicherungen.
       - Wählen Sie **Ändern** aus, um die Zeiten/Datumsangaben für die Ausführung von kurzfristigen Sicherungen zu ändern.

         ![Kurzfristige Ziele angeben](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. Überprüfen Sie auf der Seite **Datenträgerzuordnung überprüfen** den für die VM-Sicherungen bereitgestellten Speicherplatz. für die virtuellen Computer.

   - Die empfohlenen Datenträgerzuordnungen basieren auf der von Ihnen angegebenen Beibehaltungsdauer, dem Typ der Workload und der Größe der geschützten Daten. Nehmen Sie die erforderlichen Änderungen vor, und wählen Sie dann **Weiter** aus.
   - **Datengröße:** Die Größe der Daten in der Schutzgruppe.
   - **Speicherplatz:** Der für die Schutzgruppe empfohlene Speicherplatz auf dem Datenträger. Wenn Sie diese Einstellung ändern möchten, muss der zugewiesene Gesamtspeicherplatz geringfügig größer sein als das voraussichtliche Wachstum der einzelnen Datenquellen.
   - **Daten zusammenstellen:** Wenn Sie die Zusammenstellung aktivieren, können mehrere Datenquellen im Schutzumfang einem einzelnen Volume für Replikate und Wiederherstellungspunkte zugeordnet werden. Die Zusammenstellung wird nicht für alle Workloads unterstützt.
   - **Automatisch erweitern:** Wenn Sie diese Einstellung aktivieren, versucht Azure Backup Server, die Datenträgergröße um 25 Prozent zu erhöhen, wenn Daten in der Schutzgruppe die ursprüngliche Zuordnung überschreiten.
   - **Speicherpooldetails:** Zeigt den Status des Speicherpools an, einschließlich der Gesamtgröße des Datenträgers und des verbleibenden Speicherplatzes.

    ![Überprüfen der Datenträgerzuordnung](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. Geben Sie auf der Seite **Replikaterstellungsmethode auswählen** an, wie die Erstsicherung erfolgen soll, und wählen Sie dann **Weiter** aus.
   - Die Standardeinstellungen lauten **Automatisch über das Netzwerk** und **Jetzt**.
   - Bei Verwendung der Standardeinstellungen wird die Angabe einer Nebenzeit empfohlen. Wählen Sie **Später**, und geben Sie Datum und Uhrzeit an.
   - Ziehen Sie bei großen Datenmengen oder nicht optimalen Netzwerkbedingungen die Offlinereplikation der Daten mit Wechselmedien in Betracht.

    ![Replikaterstellungsmethode auswählen](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. Wählen Sie auf der Seite **Konsistenzprüfungsoptionen** aus, wie und wann Konsistenzprüfungen automatisiert werden sollen. Wählen Sie **Weiter** aus.
      - Sie können Konsistenzprüfungen bei inkonsistenten Replikatdaten oder gemäß einem festgelegten Zeitplan ausführen.
      - Wenn Sie keine automatische Konsistenzprüfung konfigurieren möchten, können Sie eine manuelle Überprüfung ausführen. Klicken Sie dazu mit der rechten Maustaste auf die Schutzgruppe, und klicken Sie dann auf **Konsistenzprüfung ausführen**.

1. Wählen Sie auf der Seite **Online zu schützende Daten angeben** die zu sichernden VMs oder VM-Ordner aus. Sie können die Mitglieder einzeln auswählen oder auf **Alle auswählen** klicken, um alle Mitglieder auszuwählen. Wählen Sie **Weiter** aus.

    ![Onlineschutzdaten angeben](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Legen Sie auf der Seite **Onlinesicherungszeitplan angeben** fest, wie oft Sie Daten aus dem lokalen Speicher in Azure sichern möchten.

    - Für die Daten werden Cloud-Wiederherstellungspunkte gemäß dem Zeitplan generiert. Wählen Sie **Weiter** aus.
    - Nach der Erstellung des Wiederherstellungspunkts wird er in den Recovery Services-Tresor in Azure übertragen.

    ![Zeitplan für Onlinesicherung angeben](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Geben Sie auf der Seite **Onlineaufbewahrungsrichtlinie angeben** an, wie lange die aus den täglichen/wöchentlichen/monatlichen/jährlichen Sicherungen erstellten Wiederherstellungspunkte in Azure aufbewahrt werden sollen. Wählen Sie anschließend **Weiter** aus.

    - Für die Datenaufbewahrung in Azure gibt es kein Zeitlimit.
    - Die einzige Einschränkung ist, dass pro geschützter Instanz maximal 9.999 Wiederherstellungspunkte erstellt werden können. In diesem Beispiel ist die geschützte Instanz der VMware-Server.

    ![Online-Aufbewahrungsrichtlinie angeben](./media/backup-azure-backup-server-vmware/retention-policy.png)

1. Überprüfen Sie auf der Seite **Zusammenfassung** die Einstellungen, und wählen Sie dann **Gruppe erstellen** aus.

    ![Zusammenfassung mit den Schutzgruppenmitgliedern und Einstellungen](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-parallel-backups"></a>Parallele VMware-Sicherungen

>[!NOTE]
> Diese Funktion ist für MABS V3 UR1 anwendbar.

Bei früheren Versionen von MABS wurden parallele Sicherungen nur für Schutzgruppen durchgeführt. Mit MABS V3 UR1 werden die Sicherungen aller VMware-VMs innerhalb einer Schutzgruppe parallel durchgeführt. Dies führt zu schnelleren VM-Sicherungen. Alle VMware-Deltareplikationsaufträge werden parallel ausgeführt. Standardmäßig ist die Anzahl der parallel auszuführenden Aufträge auf „8“ festgelegt.

Sie können die Anzahl der Aufträge ändern, indem Sie wie unten gezeigt den Registrierungsschlüssel verwenden (standardmäßig nicht vorhanden, Sie müssen ihn hinzufügen):

**Schlüsselpfad:** `Software\Microsoft\Microsoft Data Protection Manager\Configuration\ MaxParallelIncrementalJobs\VMWare`<BR>
**Schlüsseltyp:** DWORD-Wert (32 Bit)

> [!NOTE]
> Sie können die Anzahl der Aufträge in einen höheren Wert ändern. Wenn Sie die Anzahl der Aufträge auf „1“ festlegen, werden Replikationsaufträge nacheinander ausgeführt. Um die Anzahl auf einen höheren Wert zu erhöhen, müssen Sie die VMware-Leistung berücksichtigen. Berücksichtigen Sie die Anzahl der verwendeten Ressourcen und die zusätzliche erforderliche Verwendung auf dem VMware vSphere-Server, und bestimmen Sie die Anzahl der parallel auszuführenden Deltareplikationsaufträge. Diese Änderung wirkt sich lediglich auf die neu erstellten Schutzgruppen aus. Bei vorhandenen Schutzgruppen müssen Sie der jeweiligen Schutzgruppe vorübergehend einen anderen virtuellen Computer hinzufügen. Dadurch sollte die Konfiguration der Schutzgruppe entsprechend aktualisiert werden. Nach Abschluss des Vorgangs können Sie diesen virtuellen Computer aus der Schutzgruppe entfernen.

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6.7

Für eine Sicherung von vSphere 6.7 gehen Sie wie folgt vor:

- Aktivieren Sie auf dem MABS-Server TLS 1.2.

>[!NOTE]
>Ab VMWare-6.7 ist TLS als Kommunikationsprotokoll aktiviert.

- Legen Sie die Registrierungsschlüssel wie folgt fest:

```text
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v2.0.50727]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v2.0.50727]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001
```

## <a name="exclude-disk-from-vmware-vm-backup"></a>Ausschließen eines Datenträgers von der VMware-VM-Sicherung

> [!NOTE]
> Diese Funktion ist für MABS V3 UR1 anwendbar.

Mit MABS V3 UR1 können Sie einen spezifischen Datenträger von der VMware-VM-Sicherung ausschließen. Das Konfigurationsskript **ExcludeDisk.ps1** befindet sich unter `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin folder`.

Führen Sie die nachfolgenden Schritte aus, um den Ausschluss eines Datenträgers zu konfigurieren:

### <a name="identify-the-vmware-vm-and-disk-details-to-be-excluded"></a>Identifizieren der VMware-VM und des auszuschließenden Datenträgers

  1. Navigieren Sie in der VMware-Konsole zu den Einstellungen der VM, für die der Datenträger ausgeschlossen werden soll.
  2. Wählen Sie den Datenträger aus, der ausgeschlossen werden soll, und notieren Sie sich den Pfad für diesen Datenträger.

        Um beispielsweise die Festplatte 2 von TestVM4 auszuschließen, lautet der Pfad für Festplatte 2 **[datastore1] TestVM4/TestVM4\_1.vmdk**.

        ![Auszuschließende Festplatte](./media/backup-azure-backup-server-vmware/test-vm.png)

### <a name="configure-mabs-server"></a>Konfigurieren des MABS-Servers

Navigieren Sie zu dem MABS-Server, auf dem die VMware-VM für den Schutz konfiguriert ist, um den Ausschluss des Datenträgers zu konfigurieren.

  1. Rufen Sie die Details des VMware-Hosts ab, der auf dem MABS-Server geschützt ist.

        ```powershell
        $psInfo = get-DPMProductionServer
        $psInfo
        ```

        ```output
        ServerName   ClusterName     Domain            ServerProtectionState
        ----------   -----------     ------            ---------------------
        Vcentervm1                   Contoso.COM       NoDatasourcesProtected
        ```

  2. Wählen Sie den VMware-Host aus, und listen Sie die geschützten VMs für den VMware-Host auf.

        ```powershell
        $vmDsInfo = get-DPMDatasource -ProductionServer $psInfo[0] -Inquire
        $vmDsInfo
        ```

        ```output
        Computer     Name     ObjectType
        --------     ----     ----------
        Vcentervm1  TestVM2      VMware
        Vcentervm1  TestVM1      VMware
        Vcentervm1  TestVM4      VMware
        ```

  3. Wählen Sie die VM aus, für die ein Datenträger ausgeschlossen werden soll.

        ```powershell
        $vmDsInfo[2]
        ```

        ```output
        Computer     Name      ObjectType
        --------     ----      ----------
        Vcentervm1   TestVM4   VMware
        ```

  4. Navigieren Sie zum Ausschließen des Datenträgers zum Ordner `Bin`, und führen Sie das Skript *ExcludeDisk.ps1* mit den folgenden Parametern aus:

        > [!NOTE]
        > Beenden Sie vor dem Ausführen dieses Befehls den DPMRA-Dienst auf dem MABS-Server. Andernfalls gibt das Skript die erfolgreiche Ausführung zurück, aktualisiert aber die Ausschlussliste nicht. Stellen Sie sicher, dass keine Aufträge ausgeführt werden, bevor Sie den Dienst beenden.

     **Führen Sie den folgenden Befehl aus, um den Datenträger in der Ausschlussliste hinzuzufügen oder zu entfernen:**

      ```powershell
      ./ExcludeDisk.ps1 -Datasource $vmDsInfo[0] [-Add|Remove] "[Datastore] vmdk/vmdk.vmdk"
      ```

     **Beispiel:**

     Um den Ausschluss des Datenträgers für TestVM4 hinzuzufügen, führen Sie folgenden Befehl aus:

       ```powershell
      C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -Add "[datastore1] TestVM4/TestVM4\_1.vmdk"
       ```

      ```output
       Creating C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\excludedisk.xml
       Disk : [datastore1] TestVM4/TestVM4\_1.vmdk, has been added to disk exclusion list.
      ```

  5. Überprüfen Sie, ob der Datenträger der Ausschlussliste hinzugefügt wurde.

     **Führen Sie den folgenden Befehl aus, um den vorhandenen Ausschluss für bestimmte VMs anzuzeigen:**

        ```powershell
        ./ExcludeDisk.ps1 -Datasource $vmDsInfo[0] [-view]
        ```

     **Beispiel**

        ```powershell
        C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -view
        ```

        ```output
        <VirtualMachine>
        <UUID>52b2b1b6-5a74-1359-a0a5-1c3627c7b96a</UUID>
        <ExcludeDisk>[datastore1] TestVM4/TestVM4\_1.vmdk</ExcludeDisk>
        </VirtualMachine>
        ```

     Nachdem Sie den Schutz für die VM konfiguriert haben, wird der ausgeschlossene Datenträger während des Schutzes nicht aufgelistet.

        > [!NOTE]
        > Wenn Sie diese Schritte für eine bereits geschützte VM ausführen, müssen Sie die Konsistenzprüfung manuell ausführen, nachdem Sie den Datenträger für den Ausschluss hinzugefügt haben.

### <a name="remove-the-disk-from-exclusion"></a>Entfernen des Datenträgers aus der Ausschlussliste

Um den Datenträger aus der Ausschlussliste zu entfernen, führen Sie den folgenden Befehl aus:

```powershell
C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -Remove "[datastore1] TestVM4/TestVM4\_1.vmdk"
```

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie den [Leitfaden zur Problembehandlung für Azure Backup Server](./backup-azure-mabs-troubleshoot.md). Darin finden Sie Informationen zur Behandlung von Problemen beim Einrichten von Sicherungen.

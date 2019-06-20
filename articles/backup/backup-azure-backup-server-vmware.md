---
title: Sichern von VMware-VMs mit Azure Backup Server
description: Verwenden Sie Azure Backup Server, um VMware-VMs zu sichern, die auf einem VMware vCenter-/ESXi-Server ausgeführt werden.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: raynew
ms.openlocfilehash: f034f31f2c8c49bbdfb88e2ba0a009ff5b795fa2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65789605"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Sichern von VMware-VMs mit Azure Backup Server

In diesem Artikel wird erläutert, wie Sie auf VMware ESXi-Hosts/vCenter Server-Instanzen ausgeführte VMware-VMs mithilfe von Azure Backup Server in Azure sichern.

In diesem Artikel werden die folgenden Aufgaben erläutert:

- Einrichten eines sicheren Kanals, damit Azure Backup Server mit VMware-Servern über HTTPS kommunizieren kann
- Einrichten eines VMware-Kontos, das Azure Backup Server zum Zugreifen auf den VMware-Server verwendet
- Hinzufügen der Kontoanmeldeinformationen zu Azure Backup
- Hinzufügen des vCenter- oder ESXi-Servers zu Azure Backup Server
- Einrichten einer Schutzgruppe, die die zu sichernden VMware-VMs enthält, Angeben der Sicherungseinstellungen und Planen der Sicherung

## <a name="before-you-start"></a>Vorbereitung
- Stellen Sie sicher, dass eine für die Sicherung unterstützte vCenter-/ESXi-Version ausgeführt wird. Dazu zählen die Versionen 6.5, 6.0 und 5.5.
- Stellen Sie sicher, dass Sie Azure Backup Server eingerichtet haben. Falls Sie dies noch nicht getan haben, [führen Sie diese Aufgabe aus](backup-azure-microsoft-azure-backup.md), bevor Sie beginnen. Azure Backup Server sollte mit den neuesten Updates ausgeführt werden.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Erstellen einer sichere Verbindung mit dem vCenter-Server

Azure Backup Server kommuniziert mit VMware-Servern standardmäßig über HTTPS. Zum Einrichten der HTTPS-Verbindung laden Sie das Zertifikat der VMware-Zertifizierungsstelle (Certificate Authority, CA) herunter und importieren es in Azure Backup Server.


### <a name="before-you-start"></a>Vorbereitung

- Wenn Sie HTTPS nicht verwenden möchten, können Sie die [Überprüfung des HTTPS-Zertifikats für alle VMware-Server deaktivieren](backup-azure-backup-server-vmware.md#disable-https-certificate-validation).
- In der Regel stellen Sie über einen Browser auf dem Azure Backup Server-Computer mithilfe von vSphere Web Client eine Verbindung mit dem vCenter-/ESXi-Server her. Beim ersten Mal ist die Verbindung nicht sicher, und es wird Folgendes angezeigt.
- Es ist wichtig, dass Sie verstehen, wie Azure Backup Server Sicherungen behandelt.
    - Im ersten Schritt sichert Azure Backup Server Daten auf dem lokalen Datenträgerspeicher. Azure Backup Server verwendet einen Speicherpool. Ein Speicherpool ist eine Gruppe von Datenträgern und Volumes, auf denen Azure Backup Server die Datenträger-Wiederherstellungspunkte für die geschützten Daten speichert. Bei dem Speicherpool kann es sich um direkt angeschlossenen Speicher (Directly Attached Storage, DAS), ein Fibre Channel-SAN oder ein iSCSI-Speichergerät oder-SAN handeln. Sie müssen unbedingt sicherstellen, dass genügend Speicherplatz für die lokale Sicherung Ihrer VMware-VM-Daten vorhanden ist.
    - Azure Backup Server sichert dann die Daten aus dem lokalen Datenträgerspeicher in Azure.
    - [Hier finden Sie Informationen](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1807#figure-out-how-much-storage-space-you-need) zum Ermitteln des erforderlichen Speicherplatzes. Die Informationen gelten für DPM, können jedoch auch für Azure Backup Server verwendet werden.

### <a name="set-up-the-certificate"></a>Einrichten des Zertifikats

Richten Sie einen sicheren Kanal wie folgt ein:

1. Geben Sie im Browser auf dem Azure Backup Server-Computer die URL von vSphere Web Client ein. Wenn die Anmeldeseite nicht angezeigt wird, überprüfen Sie die Verbindungs- und Browserproxyeinstellungen.

    ![vSphere-Webclient](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. Klicken Sie auf der Anmeldeseite von vSphere Web Client auf **Vertrauenswürdige Zertifikate der Stammzertifizierungsstelle herunterladen**.

    ![Vertrauenswürdige Zertifikate der Stammzertifizierungsstelle herunterladen](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. Eine Datei namens **download** wird heruntergeladen. Abhängig von Ihrem Browser werden Sie gefragt, ob Sie die Datei öffnen oder speichern möchten.

    ![Zertifizierungsstellenzertifikat herunterladen](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Speichern Sie die Datei mit der Erweiterung ZIP auf dem Azure Backup Server-Computer.

5. Klicken Sie mit der rechten Maustaste auf **download.zip** > **Alle extrahieren**. Der Inhalt der ZIP-Datei wird in den Ordner **certs** extrahiert, der Folgendes enthält:
   - Die Stammzertifikatdatei mit einer Erweiterung, die mit einer nummerierten Sequenz wie „.0“ und „.1“ beginnt.
   - Die Erweiterung der CRL-Datei beginnt mit „.r0“ oder „.r1“. Die CRL-Datei ist einem Zertifikat zugeordnet.

     ![Heruntergeladene Zertifikate](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. Klicken Sie im Ordner **certs** mit der rechten Maustaste auf die Stammzertifikatdatei, und klicken Sie dann auf **Umbenennen**.

    ![Umbenennen des Stammzertifikats](./media/backup-azure-backup-server-vmware/rename-cert.png)

6. Ändern Sie die Erweiterung des Stammzertifikats in CRT, und bestätigen Sie den Vorgang. Das Dateisymbol ändert sich in das Symbol eines Stammzertifikats.

7. Klicken Sie mit der rechten Maustaste auf das Stammzertifikat, und wählen Sie im Kontextmenü die Option **Zertifikat installieren** aus.

8. Wählen Sie im Dialogfeld **Zertifikatimport-Assistent** als Ziel für das Zertifikat die Option **Lokaler Computer** aus, und klicken Sie dann auf **Weiter**. Wenn Sie gefragt werden, ob Sie Änderungen am Computer zulassen möchten, bestätigen Sie dies.

    ![Willkommensseite des Assistenten](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)


9. Wählen Sie auf der Seite **Zertifikatspeicher** die Option **Alle Zertifikate in folgendem Speicher speichern** aus, und klicken Sie auf **Durchsuchen**, um den Zertifikatspeicher auszuwählen.

     ![Zertifikatspeicher](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

10. Wählen Sie im Dialogfeld **Zertifikatspeicher auswählen** als Zielordner für die Zertifikate die Option **Vertrauenswürdige Stammzertifizierungsstellen** aus, und klicken Sie dann auf **OK**.

    ![Zielordner für Zertifikate](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

11. Überprüfen Sie unter **Fertigstellen des Assistenten** den angegebenen Ordner, und klicken Sie dann auf **Fertig stellen**.

    ![Prüfen, ob sich das Zertifikat im richtigen Ordner befindet](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)


12. Melden Sie sich nach der Bestätigung des Zertifikatimports bei vCenter Server an, um sich zu vergewissern, dass Ihre Verbindung sicher ist.




### <a name="disable-https-certificate-validation"></a>Deaktivieren der Überprüfung des HTTPS-Zertifikats

Wenn in Ihrer Organisation sichere Grenzen eingerichtet wurden und Sie nicht das Protokoll HTTPS zwischen VMware-Servern und dem Azure Backup Server-Computer verwenden möchten, deaktivieren Sie HTTPS wie folgt:
1. Kopieren Sie den folgenden Text, und fügen Sie ihn in eine TXT-Datei ein.

      ```
      Windows Registry Editor Version 5.00
      [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
      "IgnoreCertificateValidation"=dword:00000001
      ```

2. Speichern Sie die Datei unter dem Namen **DisableSecureAuthentication.reg** auf dem Azure Backup Server-Computer.

3. Doppelklicken Sie auf die Datei, um den Registrierungseintrag zu aktivieren.


## <a name="create-a-vmware-role"></a>Erstellen einer VMware-Rolle

Azure Backup Server benötigt ein Benutzerkonto mit Berechtigungen für den Zugriff auf vCenter Server/den ESXi-Host. Erstellen Sie eine VMware-Rolle mit bestimmten Berechtigungen, und ordnen Sie dann einem Benutzerkonto die Rolle zu.

1. Melden Sie sich beim vCenter Server-Computer an (bzw. beim ESXi-Host, wenn Sie nicht vCenter Server verwenden).
2. Klicken Sie im Bereich **Navigator** auf **Verwaltung**.

    ![Verwaltung](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. Klicken Sie unter **Verwaltung** > **Rollen** auf das Symbol zum Hinzufügen von Rollen (das Symbol „+“).

    ![Hinzufügen einer Rolle](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)


4. Geben Sie in **Rolle erstellen** > **Rollenname** die Zeichenfolge *BackupAdminRole* ein. Sie können als Rollennamen einen beliebigen Namen auswählen, es empfiehlt sich jedoch, einen aussagekräftigen Namen zu verwenden.

5. Wählen Sie die Berechtigungen (wie in der folgenden Tabelle zusammengefasst) aus, und klicken Sie dann auf **OK**.  Die neue Rolle wird in der Liste im Bereich **Rollen** angezeigt.
   - Klicken Sie auf das Symbol neben der übergeordneten Bezeichnung, um die übergeordnete Berechtigung zu erweitern und die untergeordneten Berechtigungen anzuzeigen.
   - Um die Berechtigung „VirtualMachine“ auszuwählen, müssen Sie mehrere Ebenen der Hierarchie über- und untergeordneter Berechtigungen durchlaufen.
   - Sie müssen nicht alle untergeordneten Berechtigungen einer übergeordneten Berechtigung auswählen.

     ![Hierarchie über- und untergeordneter Berechtigungen](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Rollenberechtigungen
**6.5/6.0** | **5.5**
--- | ---
Datastore.AllocateSpace | Datastore.AllocateSpace
Global.ManageCustomFields | Global.ManageCustomFields
Global.SetCustomField |
Host.Local.CreateVM | Network.Assign
Network.Assign |
Resource.AssignVMToPool |
VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk   
VirtualMachine.Config.AdvancedConfig| VirtualMachine.Config.AdvancedConfig
VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking
VirtualMachine.Config.HostUSBDevice |
VirtualMachine.Config.QueryUnownedFiles |
VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement
VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff
VirtualMachine.Inventory.Create| VirtualMachine.Inventory.Create
VirtualMachine.Provisioning.DiskRandomAccess |
VirtualMachine.Provisioning.DiskRandomRead | VirtualMachine.Provisioning.DiskRandomRead
VirtualMachine.State.CreateSnapshot | VirtualMachine.State.CreateSnapshot
VirtualMachine.State.RemoveSnapshot | VirtualMachine.State.RemoveSnapshot




## <a name="create-a-vmware-account"></a>Erstellen eines VMware-Kontos

1. Klicken Sie im vCenter Server-Bereich **Navigator** auf **Benutzer und Gruppen**. Wenn Sie vCenter Server nicht verwenden, erstellen Sie das Konto auf dem entsprechenden ESXi-Host.

    ![Option „Benutzer und Gruppen“](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    Der vCenter-Bereich **Benutzer und Gruppen** wird angezeigt.


2. Klicken Sie im vCenter-Bereich **Benutzer und Gruppen** auf der Registerkarte **Benutzer** auf das Symbol zum Hinzufügen von Benutzern (+-Symbol).

     ![Der vCenter-Bereich „Benutzer und Gruppen“](./media/backup-azure-backup-server-vmware/usersandgroups.png)


3. Fügen Sie im Dialogfeld **Neuer Benutzer** die Benutzerinformationen hinzu, und klicken Sie dann auf **OK**. In diesem Verfahren lautet der Benutzername „BackupAdmin“.

    ![Dialogfeld „Neuer Benutzer“](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)


4. Um das Benutzerkonto der Rolle zuzuordnen, klicken Sie im Bereich **Navigator** auf **Globale Berechtigungen**. Klicken Sie im Bereich **Globale Berechtigungen** auf der Registerkarte **Verwalten** auf das Symbol zum Hinzufügen (+-Symbol).

    ![Bereich „Globale Berechtigungen“](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)


5. Klicken Sie im Dialogfeld **Stamm: Globale Berechtigungen – Berechtigung hinzufügen** auf **Hinzufügen**, um den Benutzer oder die Gruppe auszuwählen.

    ![Auswählen eines Benutzers oder einer Gruppe](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. Gehen Sie im Dialogfeld **Benutzer/Gruppen auswählen** wie folgt vor: Wählen Sie **BackupAdmin** > **Hinzufügen** aus. In **Benutzer** wird das Format *Domäne\Benutzername* für das Benutzerkonto verwendet. Wenn Sie eine andere Domäne verwenden möchten, wählen Sie sie in der Liste **Domäne** aus. Klicken Sie auf **OK**, um die ausgewählten Benutzer dem Dialogfeld **Berechtigung hinzufügen** hinzuzufügen.

    ![Benutzer „BackupAdmin“ hinzufügen](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)


7.  Wählen Sie im Bereich **Zugewiesene Rolle** in der Dropdownliste den Eintrag **BackupAdminRole** > **OK** aus.

    ![Zuweisen des Benutzers zur Rolle](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)


Im Bereich **Globale Berechtigungen** werden auf der Registerkarte **Verwalten** das neue Benutzerkonto und die zugeordnete Rolle in der Liste angezeigt.


## <a name="add-the-account-on-azure-backup-server"></a>Hinzufügen des Kontos in Azure Backup Server


1. Öffnen Sie Azure Backup Server. Wenn Sie das Symbol auf dem Desktop nicht finden können, öffnen Sie Microsoft Azure Backup über die Liste der Apps.

    ![Azure Backup Server-Symbol](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. Klicken Sie in der Azure Backup Server-Konsole auf **Verwaltung** >  **Produktionsserver** > **VMware verwalten**.

    ![Azure Backup Server-Konsole](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)


3. Klicken Sie im Dialogfeld **Anmeldeinformationen verwalten** auf **Hinzufügen**.

    ![Azure Backup Server-Dialogfeld „Anmeldeinformationen verwalten“](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. Geben Sie im Dialogfeld **Anmeldeinformationen hinzufügen** einen Namen und eine Beschreibung für die neuen Anmeldeinformationen sowie den auf dem VMware-Server definierten Benutzernamen und das zugehörige Kennwort ein. Der Name *Contoso Vcenter credential* (Contoso vCenter-Anmeldeinformationen) wird zum Identifizieren der Anmeldeinformationen in diesem Verfahren verwendet. Wenn sich der VMware-Server und Azure Backup Server nicht in derselben Domäne befinden, geben Sie die Domäne im Benutzernamen an.

    ![Azure Backup Server-Dialogfeld „Anmeldeinformationen hinzufügen“](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Klicken Sie auf **Hinzufügen**, um die neuen Anmeldeinformationen hinzuzufügen.

    ![Azure Backup Server-Dialogfeld „Anmeldeinformationen verwalten“](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)


## <a name="add-the-vcenter-server"></a>Hinzufügen des vCenter-Servers

Fügen Sie den vCenter-Server zu Azure Backup Server hinzu.


1. Klicken Sie in der Azure Backup Server-Konsole auf **Verwaltung** > **Produktionsserver** > **Hinzufügen**.

    ![Öffnen des Assistenten zum Hinzufügen von Produktionsservern](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)


2. Wählen Sie auf der Seite **Assistent zum Hinzufügen von Produktionsservern** > **Produktionsservertyp auswählen** die Option **VMware-Server** aus, und klicken Sie dann auf **Weiter**.

     ![Assistent zum Hinzufügen von Produktionsservern](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. Geben Sie unter **Computer auswählen** im Feld **Servername/IP-Adresse** den vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) oder die IP-Adresse des VMware-Servers ein. Wenn alle ESXi-Server von der gleichen vCenter-Instanz verwaltet werden, geben Sie den vCenter-Namen an. Fügen Sie andernfalls den ESXi-Host hinzu.

    ![VMware-Server angeben](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. Geben Sie im Feld **SSL-Port** den Port ein, der für die Kommunikation mit dem VMware-Server verwendet wird. 443 ist der Standardport, Sie können diesen jedoch ändern, wenn der VMware-Server einen anderen Port abhört.

5. Wählen Sie im Dialogfeld **Anmeldeinformationen angeben** die zuvor erstellten Anmeldeinformationen aus.

    ![Angeben von Anmeldeinformationen](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Klicken Sie auf **Hinzufügen**, um den VMware-Server zur Liste der Server hinzuzufügen. Klicken Sie auf **Weiter**.

    ![Hinzufügen des VMware-Servers und von Anmeldeinformationen](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Klicken Sie auf der Seite **Zusammenfassung** auf **Hinzufügen**, um den VMware-Server zu Azure Backup Server hinzuzufügen. Der neue Server wird sofort hinzugefügt. Auf dem VMware-Server wird kein Agent benötigt.

    ![Hinzufügen eines VMware-Servers zu Azure Backup Server](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. Überprüfen Sie die Einstellungen auf der Seite **Fertig stellen**.

   ![Seite „Fertig stellen“](./media/backup-azure-backup-server-vmware/summary-screen.png)

Wenn Sie über mehrere nicht von vCenter Server verwaltete ESXi-Hosts oder über mehrere vCenter Server-Instanzen verfügen, müssen Sie den Assistenten erneut ausführen, um die Server hinzuzufügen.




## <a name="configure-a-protection-group"></a>Konfigurieren einer Schutzgruppe

Fügen Sie VMware-VMs für die Sicherung hinzu. Schutzgruppen erfassen mehrere VMs und wenden auf alle VMs in der Gruppe die gleichen Datenaufbewahrungs- und Sicherungseinstellungen an.


1. Klicken Sie in der Azure Backup Server-Konsole auf **Schutz** > **Neu**.

    ![Öffnen des Assistenten zum Erstellen einer neuen Schutzgruppe](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Klicken Sie im Dialogfeld **Neue Schutzgruppe erstellen** (auf der Willkommensseite des Assistenten) auf **Weiter**.

    ![Dialogfeld „Assistent zum Erstellen einer neuen Schutzgruppe“](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Klicken Sie auf der Seite **Schutzgruppentyp auswählen** auf **Server** und dann auf **Weiter**. Die Seite **Gruppenmitglieder auswählen** wird angezeigt.

1. Wählen Sie auf der Seite **Gruppenmitglieder auswählen** die VMs (oder die VM-Ordner) aus, die Sie sichern möchten. Klicken Sie auf **Weiter**.

    - Wenn Sie einen Ordner auswählen, werden auch die VMs oder die Ordner in diesem Ordner für die Sicherung ausgewählt. Sie können Ordner oder VMs deaktivieren, die nicht gesichert werden sollen.
1. Wenn ein virtueller Computer oder ein Ordner bereits gesichert wird, kann er nicht ausgewählt werden. Dadurch wird sichergestellt, dass keine doppelten Wiederherstellungspunkte für einen virtuellen Computer erstellt werden. .

     ![Gruppenmitglieder auswählen](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)


1. Geben Sie auf der Seite **Datenschutzmethode auswählen** einen Namen für die Schutzgruppe ein, und wählen Sie die Schutzeinstellungen aus. Legen Sie zum Sichern in Azure den kurzfristigen Schutz auf **Datenträger** fest, und aktivieren Sie den Onlineschutz. Klicken Sie auf **Weiter**.

    ![Datenschutzmethode auswählen](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Legen Sie auf der Seite **Kurzfristige Ziele angeben** fest, wie lange die gesicherten Daten auf dem Datenträger beibehalten werden sollen.
   - Geben Sie im Feld **Beibehaltungsdauer** die Anzahl von Tagen für die Aufbewahrung der Datenträger-Wiederherstellungspunkte an.
   - Geben Sie für **Synchronisierungsfrequenz** an, wie oft Datenträger-Wiederherstellungspunkte erstellt werden sollen.
       - Wenn Sie kein Sicherungsintervall festlegen möchten, können Sie **Direkt vor einem Wiederherstellungspunkt** aktivieren, damit unmittelbar vor jedem geplanten Wiederherstellungspunkt eine Sicherung ausgeführt wird.
       - Kurzfristige Sicherungen sind vollständige Sicherungen und keine inkrementellen Sicherungen.
       - Klicken Sie auf **Ändern**, um die Zeiten/Datumsangaben für die Ausführung von kurzfristigen Sicherungen zu ändern.

     ![Kurzfristige Ziele angeben](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. Überprüfen Sie auf der Seite **Datenträgerzuordnung überprüfen** den für die VM-Sicherungen bereitgestellten Speicherplatz. für die virtuellen Computer.

   - Die empfohlenen Datenträgerzuordnungen basieren auf der von Ihnen angegebenen Beibehaltungsdauer, dem Typ der Workload und der Größe der geschützten Daten. Nehmen Sie die erforderlichen Änderungen vor, und klicken Sie dann auf **Weiter**.
   - **Datengröße:** Die Größe der Daten in der Schutzgruppe.
   - **Speicherplatz:** Der für die Schutzgruppe empfohlene Speicherplatz auf dem Datenträger. Wenn Sie diese Einstellung ändern möchten, muss der zugewiesene Gesamtspeicherplatz geringfügig größer sein als das voraussichtliche Wachstum der einzelnen Datenquellen.
   - **Daten zusammenstellen:** Wenn Sie die Zusammenstellung aktivieren, können mehrere Datenquellen im Schutzumfang einem einzelnen Volume für Replikate und Wiederherstellungspunkte zugeordnet werden. Die Zusammenstellung wird nicht für alle Workloads unterstützt.
   - **Automatisch erweitern:** Wenn Sie diese Einstellung aktivieren, versucht Azure Backup Server, die Datenträgergröße um 25 Prozent zu erhöhen, wenn Daten in der Schutzgruppe die ursprüngliche Zuordnung überschreiten.
   - **Speicherpooldetails:** Zeigt den Status des Speicherpools an, einschließlich der Gesamtgröße des Datenträgers und des verbleibenden Speicherplatzes.

     ![Überprüfen der Datenträgerzuordnung](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. Geben Sie auf der Seite **Replikaterstellungsmethode auswählen** an, wie die Erstsicherung erfolgen soll, und klicken Sie dann auf **Weiter**.
   - Die Standardeinstellungen lauten **Automatisch über das Netzwerk** und **Jetzt**.
   - Bei Verwendung der Standardeinstellungen wird die Angabe einer Nebenzeit empfohlen. Wählen Sie **Später**, und geben Sie Datum und Uhrzeit an.
   - Ziehen Sie bei großen Datenmengen oder nicht optimalen Netzwerkbedingungen die Offlinereplikation der Daten mit Wechselmedien in Betracht.

     ![Replikaterstellungsmethode auswählen](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. Wählen Sie auf der Seite **Konsistenzprüfungsoptionen** aus, wie und wann Konsistenzprüfungen automatisiert werden sollen. Klicken Sie auf **Weiter**.
      - Sie können Konsistenzprüfungen bei inkonsistenten Replikatdaten oder gemäß einem festgelegten Zeitplan ausführen.
      - Wenn Sie keine automatische Konsistenzprüfung konfigurieren möchten, können Sie eine manuelle Überprüfung ausführen. Klicken Sie dazu mit der rechten Maustaste auf die Schutzgruppe, und klicken Sie dann auf **Konsistenzprüfung ausführen**.

1. Wählen Sie auf der Seite **Online zu schützende Daten angeben** die zu sichernden VMs oder VM-Ordner aus. Sie können die Mitglieder einzeln auswählen oder auf **Select All** (Alles markieren) klicken, um alle Mitglieder auszuwählen. Klicken Sie auf **Weiter**.

      ![Onlineschutzdaten angeben](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Legen Sie auf der Seite **Onlinesicherungszeitplan angeben** fest, wie oft Sie Daten aus dem lokalen Speicher in Azure sichern möchten.

    - Für die Daten werden Cloud-Wiederherstellungspunkte gemäß dem Zeitplan generiert. Klicken Sie auf **Weiter**.
    - Nach der Erstellung des Wiederherstellungspunkts wird er in den Recovery Services-Tresor in Azure übertragen.

      ![Zeitplan für Onlinesicherung angeben](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Geben Sie auf der Seite **Onlineaufbewahrungsrichtlinie angeben** an, wie lange die aus den täglichen/wöchentlichen/monatlichen/jährlichen Sicherungen erstellten Wiederherstellungspunkte in Azure aufbewahrt werden sollen. Klicken Sie dann auf **Weiter**.

    - Für die Datenaufbewahrung in Azure gibt es kein Zeitlimit.
    - Die einzige Einschränkung ist, dass pro geschützter Instanz maximal 9.999 Wiederherstellungspunkte erstellt werden können. In diesem Beispiel ist die geschützte Instanz der VMware-Server.

      ![Online-Aufbewahrungsrichtlinie angeben](./media/backup-azure-backup-server-vmware/retention-policy.png)


1. Überprüfen Sie auf der Seite **Zusammenfassung** die ausgewählten Einstellungen, und klicken Sie dann auf **Gruppe erstellen**.

     ![Zusammenfassung mit den Schutzgruppenmitgliedern und Einstellungen](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6.7

Für eine Sicherung von vSphere 6.7 gehen Sie wie folgt:

- Aktivieren von TLS 1.2 auf DPM-Server
  >[!Note]
  >Ab VMWare-6.7 ist TLS als Kommunikationsprotokoll aktiviert.

- Legen Sie die Registrierungsschlüssel wie folgt fest:  

  Windows-Registrierungs-Editor-Version 5.00

  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v2.0.50727] "SystemDefaultTlsVersions"=dword:00000001 "SchUseStrongCrypto"=dword:00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v4.0.30319] "SystemDefaultTlsVersions"=dword:00000001 "SchUseStrongCrypto"=dword:00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v2.0.50727] "SystemDefaultTlsVersions"=dword:00000001 "SchUseStrongCrypto"=dword:00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v4.0.30319] "SystemDefaultTlsVersions"=dword:00000001 s"SchUseStrongCrypto"=dword:00000001


## <a name="next-steps"></a>Nächste Schritte

Lesen Sie den [Leitfaden zur Problembehandlung für Azure Backup Server](./backup-azure-mabs-troubleshoot.md). Darin finden Sie Informationen zur Behandlung von Problemen beim Einrichten von Sicherungen.

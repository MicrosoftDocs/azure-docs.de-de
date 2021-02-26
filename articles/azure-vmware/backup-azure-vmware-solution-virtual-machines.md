---
title: Sichern von Azure VMware Solution-VMs mit Azure Backup Server
description: Konfigurieren Sie Ihre Azure VMware Solution-Umgebung zum Sichern von VMs mit Azure Backup Server.
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: ea2164ba19703660e54879c25b975342d824662c
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585023"
---
# <a name="back-up-azure-vmware-solution-vms-with-azure-backup-server"></a>Sichern von Azure VMware Solution-VMs mit Azure Backup Server

In diesem Artikel sichern Sie VMware-VMs, die in Azure VMware Solution ausgeführt werden, mithilfe von Azure Backup Server. Lesen Sie den Artikel zum [Einrichten von Microsoft Azure Backup Server für Azure VMware Solution](set-up-backup-server-for-azure-vmware-solution.md) sorgfältig durch, bevor Sie mit den hier beschriebenen Verfahren beginnen.

Danach werden alle erforderlichen Schritte beschrieben:

> [!div class="checklist"] 
> * Einrichten eines sicheren Kanals, damit Azure Backup Server mit VMware-Servern über HTTPS kommunizieren kann 
> * Hinzufügen der Kontoanmeldeinformationen zu Azure Backup Server 
> * Hinzufügen von vCenter zu Azure Backup Server 
> * Einrichten einer Schutzgruppe, die die zu sichernden VMware-VMs enthält, Angeben der Sicherungseinstellungen und Planen der Sicherung

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Erstellen einer sicheren Verbindung mit der vCenter Server-Instanz

Azure Backup Server kommuniziert mit VMware-Servern standardmäßig über HTTPS. Zum Einrichten der HTTPS-Verbindung laden Sie das Zertifikat der VMware-Zertifizierungsstelle (ZS) herunter und importieren es in Azure Backup Server.

### <a name="set-up-the-certificate"></a>Einrichten des Zertifikats

1. Geben Sie auf dem Azure Backup Server-Computer im Browser die URL des vSphere-Webclients ein.

   > [!NOTE] 
   > Falls die Seite **Erste Schritte** von VMware nicht angezeigt wird, überprüfen Sie die Verbindungs- und Browserproxyeinstellungen, und versuchen Sie es dann nochmal.

1. Wählen Sie auf der Seite **Erste Schritte** von VMware die Option **Vertrauenswürdige Zertifikate der Stammzertifizierungsstelle herunterladen** aus.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/vsphere-web-client.png" alt-text="vSphere-Webclient":::

1. Speichern Sie die Datei **download.zip** auf dem Azure Backup Server-Computer, und extrahieren Sie den Inhalt dann in den Ordner **certs**, der Folgendes enthält:

   - Eine Stammzertifikatsdatei, deren Erweiterung mit einer Nummerierung wie „.0“ und „.1“ beginnt
   - Eine CRL-Datei, deren Erweiterung mit einer Nummerierung wie „.r0“ oder „.r1“ beginnt

1. Klicken Sie im Ordner **certs** mit der rechten Maustaste auf die Stammzertifikatsdatei, und wählen Sie **Umbenennen** aus, um die Erweiterung in **.crt** zu ändern.

   Das Dateisymbol ändert sich in das Symbol eines Stammzertifikats.

1. Klicken Sie mit der rechten Maustaste auf das Stammzertifikat, und wählen Sie **Zertifikat installieren** aus.

1. Wählen Sie im **Zertifikatimport-Assistenten** als Ziel für das Zertifikat **Lokaler Computer** aus, und klicken Sie dann auf **Weiter**.

   ![Seite „Willkommen“ des Assistenten](../backup/media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

   > [!NOTE] 
   > Wenn Sie gefragt werden, ob Sie Änderungen am Computer zulassen möchten, bestätigen Sie dies.

1. Wählen Sie **Alle Zertifikate in folgendem Speicher speichern** aus, und klicken Sie auf **Durchsuchen**, um den Zertifikatspeicher auszuwählen.

   ![Zertifikatspeicher](../backup/media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

1. Wählen Sie **Vertrauenswürdige Stammzertifizierungsstellen** als Zielordner aus, und klicken Sie auf **OK**.

1. Überprüfen Sie die Einstellungen, und wählen Sie **Fertig stellen** aus, um mit dem Import des Zertifikats zu beginnen.

   ![Prüfen, ob sich das Zertifikat im richtigen Ordner befindet](../backup/media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

1. Melden Sie sich nach der Bestätigung des Zertifikatimports bei vCenter Server an, um sich zu vergewissern, dass Ihre Verbindung sicher ist.

### <a name="enable-tls-12-on-azure-backup-server"></a>Aktivieren von TLS 1.2 in Azure Backup Server

Ab VMware 6.7 ist TLS als Kommunikationsprotokoll aktiviert. 

1. Kopieren Sie die folgenden Registrierungseinstellungen, und fügen Sie sie in Editor ein. Speichern Sie die Datei dann unter dem Namen TLS.REG ohne die Erweiterung „.txt“.

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

1. Klicken Sie mit der rechten Maustaste auf die Datei TLS.REG, und wählen Sie **Zusammenführen** oder **Öffnen** aus, um die Einstellungen der Registrierung hinzuzufügen.


## <a name="add-the-account-on-azure-backup-server"></a>Hinzufügen des Kontos in Azure Backup Server

1. Öffnen Sie Azure Backup Server, und wählen Sie in der Azure Backup Server-Konsole **Verwaltung** > **Produktionsserver** > **VMware verwalten** aus.

   ![Azure Backup Server-Konsole](../backup/media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

1. Wählen Sie im Dialogfeld **Anmeldeinformationen verwalten** die Option **Hinzufügen** aus.

   ![Wählen Sie im Dialogfeld Anmeldeinformationen verwalten die Option Hinzufügen aus.](../backup/media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

1. Geben Sie im Dialogfeld **Anmeldeinformationen hinzufügen** einen Namen und eine Beschreibung der neuen Anmeldeinformationen ein. Geben Sie die Anmeldeinformationen (Benutzername und Kennwort) an, die Sie auf dem VMware-Server festgelegt haben.

   > [!NOTE] 
   > Wenn sich der VMware-Server und Azure Backup Server nicht in derselben Domäne befinden, geben Sie im Feld **Benutzername** die Domäne an.

   ![Azure Backup Server-Dialogfeld „Anmeldeinformationen hinzufügen“](../backup/media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

1. Wählen Sie **Hinzufügen** aus, um die neuen Anmeldeinformationen hinzuzufügen.

   ![Screenshot des Azure Backup Server-Dialogfelds „Anmeldeinformationen verwalten“ mit den neuen Anmeldeinformationen](../backup/media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Hinzufügen von vCenter Server zu Azure Backup Server

1. Wählen Sie in der Azure Backup Server-Konsole **Verwaltung** > **Produktionsserver** > **Hinzufügen** aus.

   ![Öffnen des Assistenten zum Hinzufügen von Produktionsservern](../backup/media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

1. Wählen Sie **VMware-Server** und dann **Weiter** aus.

   ![Assistent zum Hinzufügen von Produktionsservern](../backup/media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

1. Geben Sie die IP-Adresse der vCenter Server-Instanz an.

   ![VMware-Server angeben](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

1. Geben Sie im Feld **SSL-Port** den Port ein, der für die Kommunikation mit der vCenter Server-Instanz verwendet wird.

   > [!TIP] 
   > 443 ist der Standardport, Sie können diesen jedoch ändern, wenn Ihre vCenter Server-Instanz an einem anderen Port lauscht.

1. Wählen Sie im Feld **Anmeldeinformationen angeben** die Anmeldeinformationen aus, die Sie im vorherigen Abschnitt erstellt haben.

1. Wählen Sie **Hinzufügen** aus, um die vCenter Server-Instanz der Liste der Server hinzuzufügen, und klicken Sie dann auf **Weiter**.

   ![Hinzufügen des VMware-Servers und der Anmeldeinformationen](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

1. Wählen Sie auf der Seite **Zusammenfassung** die Option **Hinzufügen** aus, um die vCenter Server-Instanz zu Azure Backup Server hinzuzufügen.

   Der neue Server wird sofort hinzugefügt. vCenter erfordert keinen Agent.

   ![Hinzufügen eines VMware-Servers zu Azure Backup Server](../backup/media/backup-azure-backup-server-vmware/tasks-screen.png)

1. Überprüfen Sie auf der Seite **Fertig stellen** die Einstellungen, und wählen Sie **Schließen** aus.

   ![Seite „Fertig stellen“](../backup/media/backup-azure-backup-server-vmware/summary-screen.png)

   Der vCenter-Server, der unter **Produktionsserver** aufgeführt ist, sieht wie folgt aus:
   - Typ: **VMware-Server** 
   - Agent-Status: **OK** 
   
      Wenn für **Agent-Status** der Wert **Unbekannt** angezeigt wird, wählen Sie **Aktualisieren** aus.

## <a name="configure-a-protection-group"></a>Konfigurieren einer Schutzgruppe

Schutzgruppen erfassen mehrere VMs und wenden auf alle VMs in der Gruppe die gleichen Datenaufbewahrungs- und Sicherungseinstellungen an.

1. Wählen Sie in der Azure Backup Server-Konsole **Schutz** > **Neu** aus.

   ![Öffnen des Assistenten zum Erstellen einer neuen Schutzgruppe](../backup/media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Klicken Sie auf der Willkommensseite des Assistenten **Neue Schutzgruppe erstellen** auf **Weiter**.

   ![Dialogfeld „Assistent zum Erstellen einer neuen Schutzgruppe“](../backup/media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Klicken Sie auf der Seite **Schutzgruppentyp auswählen** auf **Server** und dann auf **Weiter**. Die Seite **Gruppenmitglieder auswählen** wird angezeigt.

1. Wählen Sie auf der Seite **Gruppenmitglieder auswählen** die VMs (oder VM-Ordner) aus, die Sie sichern möchten, und klicken Sie dann auf **Weiter**.

   > [!NOTE]
   > Wenn Sie einen Ordner oder VMs auswählen, werden auch die darin enthaltenen Ordner für die Sicherung ausgewählt. Sie können Ordner oder VMs deaktivieren, die nicht gesichert werden sollen. VMs oder Ordner, die bereits gesichert werden, können nicht ausgewählt werden. Dadurch wird sichergestellt, dass für eine VM keine doppelten Wiederherstellungspunkte erstellt werden.

   ![Gruppenmitglieder auswählen](../backup/media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Geben Sie auf der Seite **Datenschutzmethode auswählen** einen Namen für die Schutzgruppe ein, und wählen Sie die Schutzeinstellungen aus. 

1. Legen Sie den kurzfristigen Schutz auf **Datenträger** fest, aktivieren Sie den Onlineschutz, und wählen Sie dann **Weiter** aus.

   ![Datenschutzmethode auswählen](../backup/media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Legen Sie fest, wie lange die gesicherten Daten auf dem Datenträger beibehalten werden sollen.

   - **Beibehaltungsdauer**: Gibt die Anzahl von Tagen an, für die Datenträgerwiederherstellungspunkte beibehalten werden.
   - **Schnelle vollständige Sicherung**: Gibt an, wie oft Datenträgerwiederherstellungspunkte erstellt werden. Wählen Sie **Ändern** aus, um die Zeit- oder Datumsangaben für die Ausführung von kurzfristigen Sicherungen zu ändern.

   :::image type="content" source="media/azure-vmware-solution-backup/new-protection-group-specify-short-term-goals.png" alt-text="Angeben der kurzfristigen Ziele für den datenträgerbasierten Schutz":::

1. Überprüfen Sie auf der Seite **Datenspeicherzuordnung überprüfen** den bereitgestellten Speicherplatz für die VM-Sicherungen.

   - Die empfohlenen Datenträgerzuordnungen basieren auf der von Ihnen angegebenen Beibehaltungsdauer, dem Typ der Workload und der Größe der geschützten Daten. Nehmen Sie die erforderlichen Änderungen vor, und wählen Sie dann **Weiter** aus.
   - **Datengröße:** Die Größe der Daten in der Schutzgruppe.
   - **Speicherplatz:** Für die Schutzgruppe empfohlener Speicherplatz auf dem Datenträger. Falls Sie diese Einstellung ändern möchten, ordnen Sie Speicherplatz zu, der geringfügig größer ist als das voraussichtliche Wachstum der einzelnen Datenquellen.
   - **Speicherpooldetails:** Zeigt den Status des Speicherpools an, einschließlich der Gesamtgröße und verbleibenden Größe des Datenträgers.

   :::image type="content" source="media/azure-vmware-solution-backup/review-disk-allocation.png" alt-text="Überprüfen des zugeordneten Speicherplatzes auf dem Datenträger im Speicherpool":::

   > [!NOTE]
   > In einigen Szenarien ist die gemeldete Datengröße höher als die tatsächliche VM-Größe. Dieses Problem ist uns bekannt und wird derzeit untersucht.

1. Geben Sie auf der Seite **Replikaterstellungsmethode auswählen** an, wie die Erstsicherung erfolgen soll, und wählen Sie dann **Weiter** aus.

   - Die Standardeinstellungen lauten **Automatisch über das Netzwerk** und **Jetzt**. Bei Verwendung der Standardeinstellung empfehlen wir, eine Nebenzeit anzugeben. Wenn Sie **Später** auswählen, geben Sie ein Datum und eine Uhrzeit an.
   - Ziehen Sie bei großen Datenmengen oder nicht optimalen Netzwerkbedingungen die Offlinereplikation der Daten mit Wechselmedien in Betracht.

   ![Replikaterstellungsmethode auswählen](../backup/media/backup-azure-backup-server-vmware/replica-creation.png)

1. Wählen Sie unter **Konsistenzprüfungsoptionen** aus, wie und wann Konsistenzprüfungen automatisiert werden sollen, und klicken Sie auf **Weiter**.

   - Sie können Konsistenzprüfungen bei inkonsistenten Replikatdaten oder gemäß einem festgelegten Zeitplan ausführen.
   - Wenn Sie keine automatischen Konsistenzprüfungen konfigurieren möchten, können Sie eine manuelle Überprüfung ausführen, indem Sie mit der rechten Maustaste auf die Schutzgruppe klicken und **Konsistenzprüfung ausführen** auswählen.

1. Wählen Sie auf der Seite **Online zu schützende Daten angeben** die zu sichernden VMs oder VM-Ordner aus, und klicken Sie dann auf **Weiter**. 

   > [!TIP]
   > Sie können die Mitglieder einzeln auswählen oder auf **Alle auswählen** klicken, um alle Mitglieder auszuwählen.

   ![Onlineschutzdaten angeben](../backup/media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Legen Sie auf der Seite **Onlinesicherungszeitplan angeben** fest, wie oft Sie Daten aus dem lokalen Speicher in Azure sichern möchten. 

   - Für die Daten werden entsprechend dem Zeitplan Cloudwiederherstellungspunkte generiert. 
   - Der generierte Wiederherstellungspunkt wird dann in den Recovery Services-Tresor in Azure übertragen.

   ![Zeitplan für Onlinesicherung angeben](../backup/media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Geben Sie auf der Seite **Onlineaufbewahrungsrichtlinie angeben** an, wie lange die aus den Sicherungen erstellten Wiederherstellungspunkte in Azure aufbewahrt werden sollen.

   - Für die Datenaufbewahrung in Azure gibt es kein Zeitlimit.
   - Die einzige Einschränkung ist, dass pro geschützte Instanz maximal 9.999 Wiederherstellungspunkte erstellt werden können. In diesem Beispiel ist die geschützte Instanz der VMware-Server.

   ![Online-Aufbewahrungsrichtlinie angeben](../backup/media/backup-azure-backup-server-vmware/retention-policy.png)

1. Überprüfen Sie auf der Seite **Zusammenfassung** die Einstellungen, und wählen Sie dann **Gruppe erstellen** aus.

   ![Zusammenfassung mit den Schutzgruppenmitgliedern und Einstellungen](../backup/media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="monitor-with-the-azure-backup-server-console"></a>Überwachen mit der Azure Backup Server-Konsole

Nachdem Sie die Schutzgruppe zur Sicherung von Azure VMware Solution-VMs konfiguriert haben, können Sie den Status des Sicherungsauftrags und Warnungen in der Azure Backup Server-Konsole überwachen. Folgendes können Sie überwachen:

- Aufgabenbereich **Überwachung**:
   - Unter **Warnungen** können Sie Fehler, Warnungen und allgemeine Informationen überwachen.  Sie können aktive und inaktive Warnungen anzeigen und E-Mail-Benachrichtigungen einrichten.
   - Unter **Aufträge** können Sie von Azure Backup Server gestartete Aufträge für eine bestimmte geschützte Datenquelle oder Schutzgruppe anzeigen. Sie können den Auftragsstatus verfolgen oder den Ressourcenverbrauch von Aufträgen überprüfen.
- Im Aufgabenbereich **Schutz** können Sie den Status von Volumes und Freigaben in der Schutzgruppe überprüfen. Sie können auch Konfigurationseinstellungen überprüfen, z. B. die Wiederherstellungseinstellungen, die Datenträgerzuordnung und den Sicherungszeitplan.
- Im Aufgabenbereich **Verwaltung** können Sie auf den Registerkarten **Datenträger, Online** und **Agents** den Status von Datenträgern im Speicherpool, die Registrierung bei Azure und den Status des bereitgestellten DPM-Agents überprüfen.

:::image type="content" source="media/azure-vmware-solution-backup/monitor-backup-jobs.png" alt-text="Überwachen des Status von Sicherungsaufträgen in Azure Backup Server":::

## <a name="restore-vmware-virtual-machines"></a>Wiederherstellen von virtuellen VMware-Computern

In der Azure Backup Server-Administratorkonsole haben Sie zwei Möglichkeiten, um wiederherstellbare Daten zu ermitteln: Suchen oder Durchsuchen. Beim Wiederherstellen von Daten kann es auch vorkommen, dass Sie Daten oder eine VM nicht an demselben Speicherort wiederherstellen möchten. Aus diesem Grund unterstützt Azure Backup Server drei Wiederherstellungsoptionen für VMware-VM-Sicherungen:

- **Wiederherstellung am ursprünglichen Speicherort**: Verwenden Sie diese Wiederherstellungsoption, um eine geschützte VM an ihrem ursprünglichen Speicherort wiederherzustellen. Sie können eine VM nur dann am ursprünglichen Speicherort wiederherstellen, wenn seit der Sicherung keine Datenträger hinzugefügt oder gelöscht wurden. Wenn Datenträger hinzugefügt oder gelöscht wurden, müssen Sie die Option zur Wiederherstellung an einem alternativen Speicherort verwenden.
- **Wiederherstellung an einem alternativen Speicherort**: Verwenden Sie diese Option, wenn die ursprüngliche VM nicht vorhanden ist oder Sie sie nicht beeinträchtigen möchten. Geben Sie den Speicherort eines ESXi-Hosts, Ressourcenpools und Ordners sowie den Speicherdatenspeicher mit Pfad an. Zur Unterscheidung der wiederhergestellten VM von der ursprünglichen VM fügt Azure Backup Server den Zusatz *„-Recovered“* an den Namen der VM an.
- **Wiederherstellung einzelner Dateien an einem Speicherort**: Wenn es sich bei der geschützten VM um eine Windows Server-VM handelt, können Sie mit dieser Option von Azure Backup Server einzelne Dateien oder Ordner der VM wiederherstellen. Informationen zur Wiederherstellung einzelner Dateien finden Sie in den Schritten weiter unten in diesem Artikel. Die Wiederherstellung einzelner Dateien von einer VM ist nur für Windows-VM- und Datenträgerwiederherstellungspunkte verfügbar.

### <a name="restore-a-recovery-point"></a>Wiederherstellen eines Wiederherstellungspunkts

1. Wählen Sie in der Azure Backup Server-Administratorkonsole die Ansicht **Wiederherstellung** aus. 

1. Suchen oder filtern Sie im Bereich **Durchsuchen**, um die wiederherzustellende VM zu ermitteln. Nachdem Sie eine VM oder einen Ordner ausgewählt haben, werden im Bereich **Wiederherstellungspunkte für** die verfügbaren Wiederherstellungspunkte angezeigt.

   ![Verfügbare Wiederherstellungspunkte](../backup/media/restore-azure-backup-server-vmware/recovery-points.png)

1. Wählen Sie im Bereich **Wiederherstellungspunkte für** ein Datum aus, an dem ein Wiederherstellungspunkt erstellt wurde. Für fett formatierte Kalenderdaten sind Wiederherstellungspunkte verfügbar. Alternativ können Sie mit der rechten Maustaste auf die VM klicken, **Alle Wiederherstellungspunkte anzeigen** und dann den Wiederherstellungspunkt in der Liste auswählen.

   > [!NOTE] 
   > Wählen Sie für den kurzfristigen Schutz einen datenträgerbasierten Wiederherstellungspunkt aus, um die Wiederherstellung zu beschleunigen. Nach Ablauf der kurzfristigen Wiederherstellungspunkte sind nur **Online**-Wiederherstellungspunkte zur Wiederherstellung verfügbar.

1. Vergewissern Sie sich vor dem Wiederherstellen eines Online-Wiederherstellungspunkts, dass am Stagingspeicherort ausreichend freier Speicherplatz für die vollständige nicht komprimierte Größe der VM verfügbar ist, die Sie wiederherstellen möchten. Sie können den Stagingspeicherort anzeigen oder ändern, indem Sie den **Assistenten zum Konfigurieren von Abonnementeinstellungen** ausführen.

   :::image type="content" source="media/azure-vmware-solution-backup/mabs-recovery-folder-settings.png" alt-text="Einstellungen für Azure Backup Server-Wiederherstellungsordner":::

1. Wählen Sie **Wiederherstellen** aus, um den **Wiederherstellungs-Assistenten** zu starten.

   ![Wiederherstellungs-Assistent: Seite „Wiederherstellungsauswahl überprüfen“](../backup/media/restore-azure-backup-server-vmware/recovery-wizard.png)

1. Wählen Sie **Weiter** aus, um zum Bildschirm **Wiederherstellungsoptionen angeben** zu wechseln. Wählen Sie nochmals **Weiter** aus, um zum Bildschirm **Wiederherstellungstyp auswählen** zu wechseln. 

   > [!NOTE]
   > VMware-Workloads unterstützen die Aktivierung der Netzwerk-Bandbreiteneinschränkung nicht.

1. Wählen Sie auf der Seite **Wiederherstellungstyp auswählen** aus, ob die Wiederherstellung auf der ursprünglichen Instanz oder an einem anderen Speicherort durchgeführt werden soll.

   - Bei Auswahl von **In ursprünglicher Instanz wiederherstellen**  müssen Sie im Assistenten keine weiteren Angaben machen. Die Daten der ursprünglichen Instanz werden verwendet.
   - Wenn Sie **Als virtuellen Computer auf einem beliebigen Host wiederherstellen** auswählen, müssen Sie auf dem Bildschirm **Ziel angeben** die Informationen für **ESXi-Host**, **Ressourcenpool**, **Ordner** und **Pfad** angeben.

   ![Seite „Wiederherstellungstyp auswählen“](../backup/media/restore-azure-backup-server-vmware/recovery-type.png)

1. Überprüfen Sie auf der Seite **Zusammenfassung** Ihre Einstellungen, und wählen Sie **Wiederherstellen** aus, um den Wiederherstellungsprozess zu starten. 

   Auf dem Bildschirm **Wiederherstellungsstatus** wird der Status des Wiederherstellungsvorgangs angezeigt.

### <a name="restore-an-individual-file-from-a-vm"></a>Wiederherstellen einer einzelnen Datei von einem virtuellen Computer

Sie können einzelne Dateien eines geschützten VM-Wiederherstellungspunkts wiederherstellen. Dieses Feature ist nur für Windows Server-VMs verfügbar. Die Wiederherstellung einzelner Dateien ähnelt dem Wiederherstellen der gesamten VM. Der einzige Unterschied besteht darin, dass Sie den Datenträger der VM (VMDK) durchsuchen und die gewünschten Dateien auswählen, bevor Sie den Wiederherstellungsprozess starten. 

> [!NOTE]
> Die Wiederherstellung einzelner Dateien von einer VM ist nur für Windows-VM- und Datenträgerwiederherstellungspunkte verfügbar.

1. Wählen Sie in der Azure Backup Server-Administratorkonsole die Ansicht **Wiederherstellung** aus.

1. Suchen oder filtern Sie im Bereich **Durchsuchen**, um die wiederherzustellende VM zu ermitteln. Nachdem Sie eine VM oder einen Ordner ausgewählt haben, werden im Bereich **Wiederherstellungspunkte für** die verfügbaren Wiederherstellungspunkte angezeigt.

   ![Verfügbare Wiederherstellungspunkte](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

1. Wählen Sie im Bereich **Wiederherstellungspunkte für** über den Kalender das Datum mit den gewünschten Wiederherstellungspunkten aus. Je nach Konfiguration der Sicherungsrichtlinie können Daten ggf. über mehrere Wiederherstellungspunkte verfügen. 

1. Denken Sie daran, nach der Auswahl des Tags, an dem der Wiederherstellungspunkt erstellt wurde, die richtige **Wiederherstellungszeit** auszuwählen. 

   > [!NOTE]
   > Falls für das ausgewählte Datum mehrere Wiederherstellungspunkte vorhanden sind, wählen Sie den gewünschten Wiederherstellungspunkt im Dropdownmenü **Wiederherstellungszeit** aus. 

   Nachdem Sie den Wiederherstellungspunkt ausgewählt haben, wird die Liste der wiederherstellbaren Elemente im Bereich **Pfad** angezeigt.

1. Ermitteln Sie die wiederherzustellenden Dateien, indem Sie im Bereich **Pfad** auf das Element in der Spalte **Wiederherstellbares Element** doppelklicken, um es zu öffnen. Wählen Sie dann die Dateien bzw. Ordner aus, die Sie wiederherstellen möchten. Um mehrere Elemente auszuwählen, halten Sie beim Auswählen der einzelnen Elemente die **STRG**-Taste gedrückt. Im Bereich **Pfad** können Sie die Liste der Dateien oder Ordner durchsuchen, die in der Spalte **Wiederherstellbares Element** angezeigt werden.
    
   > [!NOTE]
   > Bei der Option **Liste unten durchsuchen** werden die Unterordner nicht in die Suche einbezogen. Doppelklicken Sie auf den Ordner, um die Unterordner zu durchsuchen. Verwenden Sie die Schaltfläche **Nach oben**, um von einem untergeordneten Ordner in den übergeordneten Ordner zu wechseln. Sie können mehrere Elemente (Dateien und Ordner) auswählen, aber diese müssen in demselben übergeordneten Ordner enthalten sein. Es ist nicht möglich, in einem einzigen Wiederherstellungsauftrag Elemente aus mehreren Ordnern wiederherzustellen.

   ![Wiederherstellungsauswahl überprüfen](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

1. Klicken Sie nach dem Auswählen der wiederherzustellenden Elemente im Menüband der Administratorkonsole auf **Wiederherstellen**, um den **Wiederherstellungs-Assistenten** zu öffnen. Im **Wiederherstellungs-Assistenten** werden auf dem Bildschirm **Wiederherstellungsauswahl überprüfen** die zur Wiederherstellung ausgewählten Elemente angezeigt.

1. Führen Sie auf der Seite **Wiederherstellungsoptionen angeben** einen der folgenden Schritte aus:

   - Wählen Sie **Ändern** aus, um die Netzwerk-Bandbreiteneinschränkung zu aktivieren. Aktivieren Sie auf der Registerkarte **Drosselung** das Kontrollkästchen **Netzwerk-Bandbreiteneinschränkung aktivieren**. Konfigurieren Sie nach der Aktivierung die Optionen **Einstellungen** und **Arbeitszeitplan**.
   - Wählen Sie **Weiter** aus, wenn die Netzwerk-Bandbreiteneinschränkung deaktiviert bleiben soll.

1. Wählen Sie auf dem Bildschirm **Wiederherstellungstyp auswählen** die Option **Weiter** aus. Sie können Ihre Dateien oder Ordner nur in einem Netzwerkordner wiederherstellen.

1. Wählen Sie auf dem Bildschirm **Ziel angeben** die Option **Durchsuchen** aus, um eine Netzwerkadresse für Ihre Dateien bzw. Ordner auszuwählen. Azure Backup Server erstellt einen Ordner, in den alle wiederhergestellten Elemente kopiert werden. Der Ordnername hat das Präfix „MABS_Tag-Monat-Jahr“. Wenn Sie einen Speicherort für die wiederhergestellten Dateien oder den Ordner auswählen, werden die Details für diesen Speicherort angegeben.

   ![Angeben des Speicherorts zum Wiederherstellen von Dateien](../backup/media/restore-azure-backup-server-vmware/specify-destination.png)

1. Wählen Sie auf dem Bildschirm **Wiederherstellungsoptionen angeben** aus, welche Sicherheitseinstellung angewendet werden soll. Sie können sich für eine Änderung der Drosselung der Netzwerkbandbreite entscheiden, aber die Drosselung ist standardmäßig deaktiviert. Auch die Optionen **SAN-Wiederherstellung** und **Benachrichtigung** sind nicht aktiviert.

1. Überprüfen Sie Ihre Einstellungen auf dem Bildschirm **Zusammenfassung**, und wählen Sie **Wiederherstellen** aus, um den Wiederherstellungsprozess zu starten. Auf dem Bildschirm **Wiederherstellungsstatus** wird der Status des Wiederherstellungsvorgangs angezeigt.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit dem Sichern Ihrer Azure VMware Solution-VMs mit Azure Backup Server vertraut gemacht haben, sollten Sie sich über die folgenden Themen informieren: 

- [Behandeln von Problemen mit Azure Backup Server](../backup/backup-azure-mabs-troubleshoot.md)
- [Lebenszyklusverwaltung von Azure VMware Solution-VMs](lifecycle-management-of-azure-vmware-solution-vms.md)

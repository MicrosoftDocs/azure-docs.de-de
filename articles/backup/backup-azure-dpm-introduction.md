---
title: Vorbereiten des DPM-Servers zum Sichern von Workloads
description: In diesem Artikel erfahren Sie, wie Sie System Center Data Protection Manager-Sicherungen (DPM) in Azure mithilfe des Azure Backup-Diensts vorbereiten.
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: 0089c3d86eb36b82287570ecdfd6e8c782e6fb8a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002857"
---
# <a name="prepare-to-back-up-workloads-to-azure-with-system-center-dpm"></a>Vorbereiten der Sicherung von Workloads in Azure mit System Center DPM

In diesem Artikel wird erläutert, wie Sie System Center Data Protection Manager-Sicherungen (DPM) in Azure mithilfe des Azure Backup-Diensts vorbereiten.

Der Artikel enthält Folgendes:

- Eine Übersicht über die Bereitstellung von DPM mit Azure Backup
- Voraussetzungen und Einschränkungen für die Verwendung von Azure Backup mit DPM
- Schritte zum Vorbereiten von Azure, darunter das Einrichten eines Recovery Services-Sicherungstresors und das optionale Ändern des Typs von Azure Storage für den Tresor
- Schritte zum Vorbereiten des DPM-Servers, darunter der Download von Tresoranmeldeinformationen, das Installieren des Azure Backup-Agents und das Registrieren des DPM-Servers im Tresor
- Tipps zur Problembehandlung bei häufigen Fehlern

## <a name="why-back-up-dpm-to-azure"></a>Gründe für die Sicherung von DPM in Azure

[System Center DPM](/system-center/dpm/dpm-overview) sichert Datei- und Anwendungsdaten. DPM interagiert mit Azure Backup wie folgt:

- **Auf einem physischen Server oder lokalen virtuellen Computer ausgeführter DPM**: Sie können Daten zusätzlich zu Sicherungen auf Festplatte und Band in einem Sicherungstresor in Azure sichern.
- **Auf einem virtuellen Azure-Computer ausgeführter DPM**: Sie können von System Center 2012 R2 mit Update 3 oder höher DPM auf einem virtuellen Azure-Computer bereitstellen. Sie können Daten auf mit dem virtuellen Computer verbundenen Azure-Datenträgern sichern oder die Daten mit Azure Backup in einem Sicherungstresor sichern.

Durch die Sicherung von DPM-Servern in Azure ergeben sich folgende Geschäftsvorteile:

- Für einen lokalen DPM bietet Azure Backup eine Alternative zur langfristigen Bereitstellung auf Band.
- Für einen auf einem virtuellen Azure-Computer ausgeführten DPM ermöglicht Azure Backup das Auslagern von Speicher vom Azure-Datenträger. Die Speicherung älterer Daten in einem Sicherungstresor ermöglicht den Ausbau Ihres Unternehmens, indem Sie neue Daten auf Datenträgern speichern.

## <a name="prerequisites-and-limitations"></a>Voraussetzungen und Einschränkungen

**Einstellung** | **Anforderung**
--- | ---
DPM auf einem virtuellen Azure-Computer | System Center 2012 R2 mit DPM 2012 R2 Updaterollup 3 oder höher.
DPM auf einem physischen Server | System Center 2012 SP1 oder höher, System Center 2012 R2.
DPM auf einem virtuellen Hyper-V-Computer | System Center 2012 SP1 oder höher, System Center 2012 R2.
DPM auf einem virtuellen VMware-Computer | System Center 2012 R2 mit Updaterollup 5 oder höher.
Komponenten | Auf dem DPM-Server müssen die Komponenten Windows PowerShell und .NET Framework 4.5 installiert sein.
Unterstützte Apps | [Erfahren Sie](/system-center/dpm/dpm-protection-matrix), was DPM sichern kann.
Unterstützte Dateitypen | Diese Dateitypen können mit Azure Backup gesichert werden:<br> <li>Verschlüsselt (nur vollständige Sicherungen)<li> Komprimiert (inkrementelle Sicherungen werden unterstützt) <li> Platzsparend (inkrementelle Sicherungen werden unterstützt)<li> Komprimiert und platzsparend (als platzsparend behandelt)
Nicht unterstützte Dateitypen | <li>Server auf Dateisystemen, bei denen Groß-/Kleinschreibung unterschieden wird<li> feste Links (übersprungen)<li> Analysepunkte (übersprungen)<li> verschlüsselt und komprimiert (übersprungen)<li> verschlüsselt und platzsparend (übersprungen)<li> Komprimierter Stream<li> Stream analysieren
Lokaler Speicher | Jeder Computer, den Sie sichern möchten, muss mindestens 5 % der zu sichernden Datengröße als freien lokalen Speicher aufweisen. Beispielsweise erfordert das Sichern von 100GB an Daten mindestens 5GB freien Speicherplatz im Scratchverzeichnis.
Tresorspeicher | Es gibt keine Beschränkung der Datenmenge, die Sie in einem Azure Backup-Tresor sichern, aber die Größe einer Datenquelle (beispielsweise ein virtueller Computer oder eine Datenbank) darf 54.400 GB nicht überschreiten.
Azure ExpressRoute | Sie können Ihre Daten über Azure ExpressRoute mit öffentlichem Peering (verfügbar für alte Verbindungen) und Microsoft-Peering sichern. Die Sicherung über privates Peering wird nicht unterstützt.<br/><br/> **Bei öffentlichem Peering**: Stellen Sie den Zugriff auf die folgenden Domänen/Adressen sicher:<br/><br/>- `http://www.msftncsi.com/ncsi.txt` <br/><br/>- `microsoft.com` <br/><br/>-`.WindowsAzure.com`<br/><br/>-`.microsoftonline.com`<br/><br/>-`.windows.net`<br/><br/> **Mit Microsoft-Peering**: Wählen Sie die folgenden Dienste, Regionen und relevanten Communitywerte aus:<br/><br/>– Azure Active Directory (12076:5060)<br/><br/>– Microsoft Azure-Region (entsprechend dem Standort Ihres Recovery Services-Tresors)<br/><br/>– Azure Storage (entsprechend dem Standort Ihres Recovery Services-Tresors)<br/><br/>Weitere Informationen finden Sie unter [ExpressRoute-Routinganforderungen](../expressroute/expressroute-routing.md).<br/><br/>**Hinweis**: Öffentliches Peering gilt für neue Leitungen als veraltet.
Azure Backup-Agent | Wenn DPM in System Center 2012 SP1 ausgeführt wird, installieren Sie mindestens Updaterollup 2 für DPM SP1. Dies ist für die Installation des Agents erforderlich.<br/><br/> In diesem Artikel wird beschrieben, wie Sie die neueste Version des Azure Backup-Agents, auch als MARS-Agent (Microsoft Azure Recovery Service) bezeichnet, bereitstellen. Wenn Sie eine frühere Version bereitgestellt haben, aktualisieren Sie sie auf die neueste Version, um sicherzustellen, dass die Sicherung wie erwartet funktioniert.

Vor dem Beginn benötigen Sie ein Azure-Konto mit aktiviertem Azure Backup-Feature. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Erfahren Sie mehr über [Preisgestaltung von Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-settings"></a>Anpassen von Speichereinstellungen

Sie haben die Wahl zwischen georedundantem Speicher und lokal redundantem Speicher.

- Standardmäßig verfügt Ihr Tresor über einen georedundanten Speicher.
- Behalten Sie den georedundanten Speicher bei, wenn der Tresor Ihre primäre Sicherung ist. Befolgen Sie die unten aufgeführten Schritte, um einen lokal redundanten Speicher zu konfigurieren, wenn Sie eine günstigere und weniger langfristige Option wünschen.
- Informieren Sie sich über [Azure Storage](../storage/common/storage-redundancy.md) sowie die [georedundanten](../storage/common/storage-redundancy.md#geo-redundant-storage), [lokal redundanten](../storage/common/storage-redundancy.md#locally-redundant-storage) und [zonenredundanten](../storage/common/storage-redundancy.md#zone-redundant-storage) Speicheroptionen.
- Ändern Sie die Speichereinstellungen vor der ersten Sicherung. Wenn Sie ein Element bereits gesichert haben, beenden Sie die Sicherung im Tresor, bevor Sie Speichereinstellungen ändern.

So bearbeiten Sie die Einstellung für die Speicherreplikation:

1. Öffnen Sie das Dashboard des Tresors.

2. Wählen Sie unter **Verwalten** die Option **Sicherungsinfrastruktur** aus.

3. Wählen Sie im Menü **Sicherungskonfiguration** eine Speicheroption für den Tresor aus.

    ![Liste der Sicherungstresore](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

## <a name="download-vault-credentials"></a>Herunterladen der Tresoranmeldedaten

Sie verwenden Anmeldeinformationen beim Registrieren des DPM-Servers im Tresor.

- Die Datei mit Tresoranmeldeinformationen ist ein Zertifikat, das vom Portal für jeden Sicherungstresor generiert wird.
- Das Portal lädt anschließend den öffentlichen Schlüssel in den Access Control Service (ACS) hoch.
- Während des Registrierungsworkflows für den Computer, wird der private Schlüssel des Zertifikats für den Benutzer zur Verfügung gestellt, der den Computer authentifiziert.
- Basierend auf der Authentifizierung sendet der Azure Backup-Dienst Daten an den festgelegte Tresor.

### <a name="best-practices-for-vault-credentials"></a>Bewährte Methoden für Tresoranmeldeinformationen

Um die Anmeldeinformationen abzurufen, laden Sie die Datei mit Tresoranmeldeinformationen über einen sicheren Kanal aus dem Azure-Portal herunter:

- Die Tresoranmeldeinformationen werden nur während des Registrierungsworkflows verwendet.
- Sie müssen sicherstellen, dass die Datei mit den Tresoranmeldeinformationen sicher aufbewahrt und nicht kompromittiert wird.
  - Wenn die Anmeldeinformationen verloren gehen, können die Anmeldeinformationen des Tresors zum Registrieren anderer Computer als Tresor verwendet werden.
  - Die Sicherungsdaten sind jedoch durch eine Passphrase verschlüsselt, die Ihnen gehört. Daher sind vorhandene Sicherungsdaten nicht gefährdet.
- Stellen Sie sicher, dass die Datei an einem Ort gespeichert wird, auf den vom DPM-Server aus zugegriffen werden kann. Wenn sie in einer Dateifreigabe/einem SMB gespeichert sind, überprüfen Sie die Zugriffsberechtigungen.
- Tresoranmeldeinformationen laufen nach 48 Stunden ab. Sie können so oft wie erforderlich neue Tresoranmeldeinformationen herunterladen. Allerdings kann nur die neueste Datei mit Tresoranmeldeinformationen während des Registrierungsworkflows verwendet werden.
- Der Azure Backup-Dienst kennt nicht den privaten Schlüssel des Zertifikats, und der private Schlüssel ist weder im Portal noch im Dienst verfügbar.

Laden Sie die Datei mit den Tresoranmeldeinformationen wie folgt auf einen lokalen Computer herunter:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Öffnen Sie den Tresor, in dem Sie den DPM-Server registrieren möchten.
3. Wählen Sie unter **Einstellungen** die Option **Eigenschaften** aus.

    ![Tresormenü öffnen](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. Wählen Sie unter **Eigenschaften** > **Sicherungsanmeldeinformationen** die Option **Herunterladen** aus. Das Portal generiert die Datei mit Tresoranmeldeinformationen über eine Kombination aus dem Tresornamen und dem aktuellen Datum und macht sie zum Download verfügbar.

    ![Anmeldeinformationen herunterladen](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. Wählen Sie **Speichern** aus, um die Tresoranmeldeinformationen in den Ordner herunterzuladen, oder auf **Speichern unter**, um einen Speicherort anzugeben. Es dauert bis zu einer Minute, bis die Datei generiert ist.

## <a name="install-the-backup-agent"></a>Installieren des Backup-Agents

Auf jedem Computer, der von Azure Backup gesichert wird, muss der Backup-Agent (auch als MARS-Agent (Microsoft Azure Recovery Service) bezeichnet) installiert sein. Installieren Sie den Agent wie folgt auf dem DPM-Server:

1. Öffnen Sie den Tresor, in dem Sie den DPM-Server registrieren möchten.
2. Wählen Sie unter **Einstellungen** die Option **Eigenschaften** aus.

    ![Einstellungen für Tresor öffnen](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Laden Sie auf der Seite **Eigenschaften** den Azure Backup-Agent herunter.

    ![Download](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

4. Führen Sie nach dem Download „MARSAgentInstaller.exe“ aus, um den Agent auf dem DPM-Computer zu installieren.
5. Wählen Sie einen Installationsordner und einen Cacheordner für den Agent aus. Der freie Speicherplatz am Cachespeicherort muss mindestens 5 % der Sicherungsdaten umfassen.
6. Wenn Sie einen Proxyserver für die Verbindung mit dem Internet verwenden, geben Sie im Bildschirm **Proxykonfiguration** die Details des Proxyservers ein. Wenn Sie einen authentifizierten Proxy verwenden, geben Sie in diesem Bildschirm die Informationen zum Benutzernamen und zum Kennwort ein.
7. Der Azure Backup-Agent installiert .NET Framework 4.5 und Windows PowerShell (falls noch nicht geschehen), um die Installation abzuschließen.
8. Nachdem der Agent installiert ist, **schließen** Sie das Fenster.

    ![Close (Schließen)](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

## <a name="register-the-dpm-server-in-the-vault"></a>Registrieren des DPM-Servers im Tresor

1. Wählen Sie in der DPM-Verwaltungskonsole > **Verwaltung** die Option **Online** aus. Wählen Sie **Registrieren**. Der Assistent zum Registrieren von Servern wird geöffnet.
2. Geben Sie unter **Proxykonfiguration** die erforderlichen Proxyeinstellungen an.

    ![Proxykonfiguration](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
3. Navigieren Sie im **Backup-Tresor** zu der Datei mit Tresoranmeldeinformationen, die Sie heruntergeladen haben, und wählen Sie sie aus.

    ![Tresoranmeldeinformationen](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

4. Unter **Einstellung für die Bandbreiteneinschränkung** können Sie optional die Bandbreitenbeschränkung für Sicherungen aktivieren. Sie können Geschwindigkeitsgrenzwerte für Arbeitsstunden und -tage festlegen.

    ![Einstellung für die Bandbreiteneinschränkung](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

5. Geben Sie unter **Einstellungen für den Wiederherstellungsordner** einen Speicherort an, der für die Datenwiederherstellung verwendet werden kann.

    - Azure Backup verwendet diesen Speicherort als temporären Aufbewahrungsbereich für wiederhergestellte Daten.
    - Nach Abschluss der Datenwiederherstellung bereinigt Azure Backup die Daten in diesem Bereich.
    - Der Speicherort muss ausreichend Speicherplatz für Elemente aufweisen, die voraussichtlich parallel wiederhergestellt werden.

    ![Einstellungen für den Wiederherstellungsordner](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

6. Generieren Sie unter **Verschlüsselungseinstellung** eine Passphrase, oder geben Sie eine an.

    - Mithilfe der Passphrase werden die Sicherungen in der Cloud verschlüsselt.
    - Geben Sie mindestens 16 Zeichen ein.
    - Speichern Sie die Datei an einem sicheren Ort, da sie für die Wiederherstellung benötigt wird.

    ![Verschlüsselung](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Die Verschlüsselungspassphrase befindet sich in Ihrem Besitz, und Microsoft kann nicht auf sie zugreifen.
    > Wenn die Passphrase verloren geht oder vergessen wird, kann Microsoft Ihnen bei der Wiederherstellung der Sicherungsdaten nicht behilflich sein.

7. Wählen Sie **Registrieren** aus, um den DPM-Server im Tresor zu registrieren.

Nach dem Registrieren des Servers beim Tresor können Sie mit der Sicherung in Microsoft Azure beginnen. Zum Sichern von Workloads in Azure müssen Sie die Schutzgruppe in der DPM-Verwaltungskonsole konfigurieren. [Erfahren Sie](/system-center/dpm/create-dpm-protection-groups), wie Sie Schutzgruppen bereitstellen.

## <a name="troubleshoot-vault-credentials"></a>Problembehandlung von Tresoranmeldeinformationen

### <a name="expiration-error"></a>Ablauffehler

Die Datei mit den Tresoranmeldeinformationen gilt nur für 48 Stunden (nachdem sie aus dem Portal heruntergeladen wurde). Wenn in diesem Bildschirm Fehler auftreten (z. B. „Datei mit Tresoranmeldeinformationen abgelaufen“), melden Sie sich beim Azure-Portal an, und laden Sie die Datei mit den Tresoranmeldeinformationen erneut herunter.

### <a name="access-error"></a>Zugriffsfehler

Stellen Sie sicher, dass die Datei mit den Tresoranmeldeinformationen an einem Speicherort verfügbar ist, der für die Setupanwendung zugänglich ist. Wenn Zugriffsfehler auftreten, kopieren Sie die Datei mit den Tresoranmeldeinformationen in einen temporären Speicherort auf diesem Computer, und wiederholen Sie den Vorgang.

### <a name="invalid-credentials-error"></a>Fehler durch ungültige Anmeldeinformationen

Wenn ein Fehler wegen ungültiger Tresoranmeldeinformationen angezeigt wird (z. B. „Ungültige Tresoranmeldeinformationen angegeben“), ist die Datei entweder beschädigt oder enthält nicht die aktuellen Anmeldeinformationen, die dem Wiederherstellungsdienst zugeordnet sind.

- Wiederholen Sie den Vorgang, nachdem Sie eine neue Datei mit Tresoranmeldeinformationen vom Portal heruntergeladen haben.
- Dieser Fehler tritt in der Regel auf, wenn Sie im Azure-Portal zweimal in schneller Folge die Option **Tresoranmeldedaten herunterladen** auswählen. In diesem Fall ist nur die zweite Datei mit Tresoranmeldeinformationen gültig.

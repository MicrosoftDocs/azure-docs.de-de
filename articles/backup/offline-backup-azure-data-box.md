---
title: Offlinesicherung unter Verwendung von Azure Data Box
description: In diesem Artikel erfahren Sie, wie Sie Azure Data Box verwenden können, um ein Offlineseeding für eine große Menge Erstsicherungsdaten vom MARS-Agent in einen Recovery Services-Tresor auszuführen.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: e789b6c9f4ff2e8cd168e6b5c138d423911d4743
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96752582"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Offlinesicherung in Azure Backup unter Verwendung von Azure Data Box

Sie können [Azure Data Box](../databox/data-box-overview.md) verwenden, um ein Offlineseeding (ohne Verwendung eines Netzwerks) für eine große Menge an Microsoft Azure Recovery Services (MARS)-Erstsicherungsdateien in einen Recovery Services-Tresor auszuführen. Mit diesem Verfahren können Sie sowohl Zeit als auch Netzwerkbandbreite einsparen, die andernfalls erforderlich wären, um die große Menge an Sicherungsdaten über ein Netzwerk mit hoher Latenz online zu stellen. Diese Optimierung befindet sich zurzeit in der Vorschauphase. Mit einer Offlinesicherung mit Azure Data Box entstehen Ihnen zwei entscheidende Vorteile gegenüber der [Offlinesicherung mit dem Azure Import/Export-Dienst](./backup-azure-backup-import-export.md):

- Sie benötigen keine eigenen mit Azure kompatiblen Datenträger und Connectors. Die der ausgewählten [Data Box-SKU](https://azure.microsoft.com/services/databox/data/) zugeordneten Datenträger sind in Azure Data Box enthalten.
- Azure Backup (der MARS-Agent) kann die Sicherungsdaten direkt in die unterstützten SKUs des Azure Data Box-Diensts schreiben. Dadurch entfällt die Notwendigkeit, einen Stagingspeicherort für Ihre Erstsicherungsdaten bereitzustellen. Sie benötigen auch keine Hilfsprogramme zum Formatieren dieser Daten und zum Kopieren der Daten auf die Datenträger.

## <a name="azure-data-box-with-the-mars-agent"></a>Azure Data Box mit dem MARS-Agent

In diesem Artikel erfahren Sie, wie Sie Azure Data Box verwenden können, um ein Offlineseeding für eine große Menge Erstsicherungsdaten vom MARS-Agent in einen Recovery Services-Tresor auszuführen.

## <a name="supported-platforms"></a>Unterstützte Plattformen

Der Seedingprozess für Daten vom MARS-Agent mithilfe von Azure Data Box wird für folgende Windows-SKUs unterstützt.

| **Betriebssystem**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **Arbeitsstation**                        |                                                              |
| Windows 10, 64 Bit                     | Enterprise, Pro, Home                                       |
| Windows 8.1, 64 Bit                    | Enterprise, Pro                                             |
| Windows 8, 64 Bit                      | Enterprise, Pro                                             |
| Windows 7, 64 Bit                      | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| **Server**                             |                                                              |
| Windows Server 2019, 64 Bit            | Standard, Datacenter, Essentials                            |
| Windows Server 2016, 64 Bit            | Standard, Datacenter, Essentials                            |
| Windows Server 2012 R2, 64 Bit         | Standard, Datacenter, Foundation                            |
| Windows Server 2012, 64 Bit            | Datacenter, Foundation, Standard                            |
| Windows Storage Server 2016, 64 Bit    | Standard, Workgroup                                         |
| Windows Storage Server 2012 R2, 64 Bit | Standard, Workgroup, Essential                              |
| Windows Storage Server 2012, 64 Bit    | Standard, Workgroup                                         |
| Windows Server 2008 R2 SP1, 64 Bit     | Standard, Enterprise, Datacenter, Foundation                |
| Windows Server 2008 SP2, 64 Bit        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Größe von Sicherungsdaten und unterstützte Data Box-SKUs

| Größe der Sicherungsdaten (nach MARS-Komprimierung)* pro Server | Unterstützte Azure Data Box-SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <= 7,2 TB                                                    | [Azure Data Box Disk](../databox/data-box-disk-overview.md) |
| > 7,2 TB und <= 80 TB**                                      | [Azure Data Box (100 TB)](../databox/data-box-overview.md) |

* Typische Komprimierungsraten variieren zwischen 10 und 20 Prozent. <br>
** Wenden Sie sich an [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com), wenn Sie vermuten, dass Sie über mehr als 80 TB an Erstsicherungsdaten für einen einzelnen MARS-Server verfügen.

>[!IMPORTANT]
>Erstsicherungsdaten von einem einzelnen Server müssen in einer einzelnen Azure Data Box- oder Azure Data Box Disk-Instanz enthalten sein und können nicht auf mehrere Geräte derselben oder verschiedener SKUs aufgeteilt werden. Ein Azure Data Box-Gerät kann jedoch Erstsicherungsdaten von mehreren Servern enthalten.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="azure-subscription-and-required-permissions"></a>Azure-Abonnement und erforderliche Berechtigungen

- Für den Prozess ist ein Azure-Abonnement erforderlich.
- Für den Prozess ist erforderlich, dass der Benutzer, der die Offlinesicherungsrichtlinie ausführen soll, „Besitzer“ des Azure-Abonnements ist.
- Der Data Box-Auftrag und der Recovery Services-Tresor (das Ziel des Datenseedings) müssen sich im selben Abonnement befinden.
- Das Zielspeicherkonto für den Azure Data Box-Auftrag und der Recovery Services-Tresor sollten sich in derselben Region befinden. Dies ist jedoch keine Voraussetzung.

### <a name="get-azure-powershell-370"></a>Abrufen von Azure PowerShell 3.7.0

*Hierbei handelt es sich um die wichtigste Voraussetzung für den Prozess.* Bevor Sie Azure PowerShell, Version 3.7.0, installieren, führen Sie die folgenden Prüfungen durch.

#### <a name="step-1-check-the-powershell-version"></a>Schritt 1: Überprüfen der PowerShell-Version

1. Öffnen Sie Windows PowerShell, und führen Sie den folgenden Befehl aus:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1. Führen Sie Schritt 2 aus, wenn in der Ausgabe eine Version höher als Version 3.7.0 angezeigt wird. Fahren Sie andernfalls mit Schritt 3 fort.

#### <a name="step-2-uninstall-the-powershell-version"></a>Schritt 2: Deinstallieren der PowerShell-Version

Deinstallieren Sie die aktuelle Version von PowerShell.

1. Entfernen Sie die abhängigen Module, indem Sie den folgenden Befehl in PowerShell ausführen:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. Führen Sie den folgenden Befehl aus, um sicherzustellen, dass alle abhängigen Module erfolgreich entfernt wurden:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>Schritt 3: Installieren der PowerShell-Version 3.7.0

Nachdem Sie sich vergewissert haben, dass keine AzureRM-Module mehr vorhanden sind, installieren Sie die Version 3.7.0 mithilfe einer der folgenden Methoden:

- Verwenden Sie [diesen Link](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017) in GitHub.

Alternative:

- Führen Sie den folgenden Befehl im PowerShell-Fenster aus:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell kann auch mithilfe einer MSI-Datei installiert worden sein. Wenn Sie Azure PowerShell wieder entfernen möchten, deinstallieren Sie die Umgebung mithilfe der Option **Programme deinstallieren** in der Systemsteuerung.

### <a name="order-and-receive-the-data-box-device"></a>Bestellen und Empfangen des Data Box-Geräts

Für den Offlinesicherungsvorgang mithilfe von MARS und Azure Data Box ist es erforderlich, dass die Data Box-Geräte den Zustand „Übermittelt“ aufweisen, bevor die Offlinesicherung mithilfe des MARS-Agents ausgelöst wird. Im Abschnitt [Größe von Sicherungsdaten und unterstützte Data Box-SKUs](#backup-data-size-and-supported-data-box-skus) finden Sie die nötigen Informationen, mit denen Sie die für Ihre Anforderung am besten geeignete SKU bestellen können. Führen Sie die Schritte im [Tutorial: Bestellen von Azure Data Box Disk](../databox/data-box-disk-deploy-ordered.md) aus, um Ihre Data Box-Geräte zu bestellen und zu empfangen.

> [!IMPORTANT]
> Wählen Sie als **Kontoart** nicht *BlobStorage* aus. Für den MARS-Agent ist ein Konto erforderlich, das Seitenblobs unterstützt, was nicht der Fall ist, wenn *BlobStorage* ausgewählt wird. Bei der Erstellung des Zielspeicherkontos für Ihren Azure Data Box-Auftrag wählen Sie als **Kontoart** die Option **StorageV2 (universell, Version 2)** aus.

![Auswählen der Kontoart in den Instanzdetails](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>Installieren und Einrichten des MARS-Agents

1. Vergewissern Sie sich, dass Sie alle vorherigen Installationen des MARS-Agents deinstalliert haben.
1. Laden Sie die aktuelle Version des MARS-Agents von [dieser Seite](https://aka.ms/azurebackup_agent) herunter.
1. Führen Sie *MARSAgentInstaller.exe* aus, und führen Sie *nur* die Schritte zum [Installieren und Registrieren des Agents](./install-mars-agent.md#install-and-register-the-agent)auf dem Recovery Services-Tresor aus, in dem Ihre Sicherungen gespeichert werden sollen.

   > [!NOTE]
   > Der Recovery Services-Tresor muss sich im selben Abonnement wie der Azure Data Box-Auftrag befinden.

   Nachdem der Agent für den Recovery Services-Tresor registriert wurde, führen Sie die Schritte in nächsten Abschnitt aus.

## <a name="set-up-azure-data-box-devices"></a>Einrichten von Azure Data Box-Geräten

Führen Sie je nach der von Ihnen bestellten Azure Data Box-SKU die unten in den jeweiligen Abschnitten erläuterten Schritte aus, um die Data Box-Geräte für den MARS-Agent einzurichten und vorzubereiten und dann die Erstsicherungsdaten identifizieren und übertragen zu können.

### <a name="set-up-azure-data-box-disks"></a>Einrichten von Azure Data Box Disk-Instanzen

Führen Sie die hier genannten Schritte zum [Entpacken, Verbinden und Entsperren von Azure Data Box Disk](../databox/data-box-disk-deploy-set-up.md) aus, wenn Sie mindestens eine Azure Data Box Disk-Bestellung getätigt haben (jeweils bis zu 8 TB).

>[!NOTE]
>Möglicherweise hat der Server für den MARS-Agent keinen USB-Anschluss. In diesem Fall können Sie Azure Data Box Disk mit einem anderen Server oder Client verbinden und den Gerätepfad als Netzwerkfreigabe angeben.

### <a name="set-up-azure-data-box"></a>Einrichten von Azure Data Box

Führen Sie die hier genannten Schritte zum [Einrichten Ihrer Data Box-Instanz](../databox/data-box-deploy-set-up.md) aus, wenn Sie eine Azure Data Box-Instanz bestellt haben (bis zu 100 TB).

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>Einbinden von Azure Data Box-Instanzen als lokales System

Der MARS-Agent wird im Kontext des lokalen Systems ausgeführt. Deshalb ist es erforderlich, dass für den Einbindungspfad, mit dem die Azure Data Box-Instanz verbunden ist, dieselben Berechtigungen gewährt werden.

So stellen Sie sicher, dass Sie Ihr Data Box-Gerät mithilfe des NFS-Protokolls als lokales System einbinden können

1. Aktivieren Sie das Feature „Client für NFS“ auf dem Windows-Server, auf dem der MARS-Agent installiert ist. Geben Sie die alternative Quelle *WIM:D:\Sources\Install.wim:4* an.
1. Laden Sie PsExec von der [Sysinternals](/sysinternals/downloads/psexec)-Seite auf den Server herunter, auf dem der MARS-Agent installiert ist.
1. Öffnen Sie eine Eingabeaufforderung mit erhöhten Rechten, und führen Sie den folgenden Befehl aus. Das Verzeichnis muss dabei *PSExec.exe* als aktuelles Verzeichnis enthalten.

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   Das Befehlsfenster, das durch den vorstehenden Befehl geöffnet wird, befindet sich im lokalen Systemkontext. Führen Sie die Schritte zum Einbinden der Seitenblobfreigabe von Azure als Netzwerklaufwerk auf Ihrem Windows-Server in diesem Befehlsfenster aus.
1. Befolgen Sie die Schritte unter [Herstellen einer Verbindung zu Data Box](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box), um Ihren Server mit dem MARS-Agent über NFS mit dem Data Box-Gerät zu verbinden. Führen Sie den folgenden Befehl an der Eingabeaufforderung des lokalen Systems aus, um die Azure-Seitenblobfreigabe einzubinden.

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   Überprüfen Sie, ob Sie über Ihren Server auf X: zugreifen können, sobald die Freigabe eingebunden wurde. Wenn dies der Fall ist, fahren Sie mit dem nächsten Abschnitt dieses Artikels fort.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Übertragen von Erstsicherungsdaten auf Azure Data Box-Geräte

1. Öffnen Sie die **Microsoft Azure Backup**-Anwendung auf Ihrem Server.
1. Wählen Sie im Bereich **Aktionen** die Option **Sicherung planen** aus.

    ![„Sicherung planen“ auswählen](./media/offline-backup-azure-data-box/schedule-backup.png)

1. Führen Sie die Schritte im **Assistenten zum Planen der Sicherung** aus.

1. Fügen Sie Elemente hinzu, indem Sie die Schaltfläche **Elemente hinzufügen** auswählen. Die Gesamtgröße der Elemente darf die [von der bestellten und empfangenen Azure Data Box-SKU unterstützte zulässige Größe nicht überschreiten](#backup-data-size-and-supported-data-box-skus).

    ![Hinzufügen von Elementen zur Sicherung](./media/offline-backup-azure-data-box/add-items.png)

1. Wählen Sie den entsprechenden Sicherungszeitplan und die Aufbewahrungsrichtlinie für **Dateien und Ordner** und **Systemstatus** aus. Der Systemstatus gilt nur für Windows-Server und nicht für Windows-Clients.
1. Wählen Sie auf der Seite **Typ der Erstsicherung auswählen (Dateien und Ordner)** des Assistenten die Option **Übertragung mithilfe von Microsoft Azure Data Box Disk-Instanzen** und dann **Weiter** aus.

    ![Erstsicherungstyp wählen](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. Melden Sie sich, wenn Sie dazu aufgefordert werden, unter Verwendung der Benutzeranmeldeinformationen, mit denen Sie als „Besitzer“ Zugriff auf das Azure-Abonnement haben, bei Azure an. Nachdem die Anmeldung erfolgreich war, sollte eine Seite ähnlich der folgenden angezeigt werden.

    ![Erstellen von Ressourcen und Anwenden von erforderlichen Berechtigungen](./media/offline-backup-azure-data-box/creating-resources.png)

   Der MARS-Agent ruft dann die Data Box-Aufträge im Abonnement ab, die den Zustand „Übermittelt“ aufweisen.

    ![Abrufen von Data Box-Aufträgen nach Abonnement-ID](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. Wählen Sie den Data Box-Auftrag aus, für den Sie Ihren Data Box-Datenträger entpackt, verbunden und entsperrt haben. Wählen Sie **Weiter** aus.

    ![Data Box-Aufträge auswählen](./media/offline-backup-azure-data-box/select-databox-order.png)

1. Wählen Sie auf der Seite **Data Box-Geräteerkennung** die Option **Gerät erkennen** aus. Dadurch beginnt der MARS-Agent, lokal angefügte Azure Data Box-Datenträger zu suchen und zu erkennen.

    ![Data Box-Geräteerkennung](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Wenn Sie die Azure Data Box-Instanz als Netzwerkfreigabe verbunden haben (weil USB-Ports nicht verfügbar waren oder weil Sie das 100-TB Data Box-Gerät bestellt und eingebunden haben), schlägt die Erkennung zunächst fehl. Sie haben die Möglichkeit, den Netzwerkpfad zum Data Box-Gerät einzugeben.

    ![Eingeben des Netzwerkpfads](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Geben Sie den Netzwerkpfad zum Stammverzeichnis des Azure Data Box-Datenträgers an. Dieses Verzeichnis muss ein Verzeichnis namens *PageBlob* beinhalten.
    >
    >![Stammverzeichnis des Azure Data Box-Datenträgers](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Wenn der Pfad auf dem Datenträger beispielsweise `\\mydomain\myserver\disk1\` lautet und *disk1* ein Verzeichnis namens *PageBlob* enthält, muss auf dem MARS-Agent-Assistenten `\\mydomain\myserver\disk1\` als Pfad angegeben werden.
    >
    >Wenn Sie [ein Azure Data Box-Geräts mit 100 TB einrichten](#set-up-azure-data-box-devices), geben Sie `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` als Netzwerkpfad zum Gerät an.

1. Wählen Sie zunächst **Weiter** und dann auf dem nächsten Bildschirm **Fertig stellen** aus, um die Sicherungs- und Aufbewahrungsrichtlinie mit der Konfiguration der Offlinesicherung mithilfe von Azure Data Box zu speichern.

   Auf der folgenden Seite wird bestätigt, dass die Richtlinie erfolgreich gespeichert wurde.

    ![Bestätigung, dass die Richtlinie erfolgreich gespeichert wurde](./media/offline-backup-azure-data-box/policy-saved.png)

1. Wählen Sie auf der vorherigen Seite **Schließen** aus.

1. Wählen Sie im Bereich **Aktionen** der MARS-Agent-Konsole **Jetzt sichern** aus. Wählen Sie auf der Seite des Assistenten **Sichern** aus.

    ![Assistent für die sofortige Sicherung](./media/offline-backup-azure-data-box/backup-now.png)

Der MARS-Agent beginnt dann mit der Sicherung der von Ihnen ausgewählten Daten auf dem Azure Data Box-Gerät. Dieser Prozess kann mehrere Stunden bis zu einigen Tagen dauern. Die Dauer hängt von der Anzahl der Dateien und der Verbindungsgeschwindigkeit zwischen dem Server mit dem MARS-Agent und Azure Data Box Disk ab.

Nachdem die Sicherung der Daten abgeschlossen ist, wird eine Seite des MARS-Agent angezeigt, die in etwa folgendermaßen aussieht.

![Angezeigter Sicherungsstatus](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Schritte nach der Sicherung

In diesem Abschnitt werden die Schritte erläutert, die ausgeführt werden müssen, nachdem die Datensicherung auf Azure Data Box Disk erfolgreich war.

- Führen Sie die Schritte in diesem Artikel zum [Zurücksenden des Azure Data Box-Datenträgers](../databox/data-box-disk-deploy-picked-up.md) aus. Führen Sie diese Schritte zum [Zurücksenden des Azure Data Box-Geräts an Azure](../databox/data-box-deploy-picked-up.md) aus, wenn Sie ein Azure Data Box-Gerät mit 100 TB verwendet haben.

- [Überprüfen Sie den Datenupload aus Azure Data Box Disk](../databox/data-box-disk-deploy-upload-verify.md) im Azure-Portal. Nachdem der Azure Data Box-Auftrag abgeschlossen ist, verschiebt der MARS-Agent die Daten zum Zeitpunkt der nächsten geplanten Sicherung automatisch vom Speicherkonto in den Recovery Services-Tresor. Der Sicherungsauftrag wird dann als *Auftrag abgeschlossen* gekennzeichnet, sobald erfolgreich ein Wiederherstellungspunkt erstellt wurde.

    >[!NOTE]
    >Der MARS-Agent löst zu den Zeitpunkten, die während der Erstellung der Richtlinie festgelegt wurden, Sicherungen aus. Diese Aufträge werden als „Warten auf Abschluss des Azure Data Box-Auftrags“ gekennzeichnet, bis der Auftrag abgeschlossen ist.

- Nachdem der MARS-Agent erfolgreich einen der Erstsicherung entsprechenden Wiederherstellungspunkt erstellt hat, können Sie das mit dem Azure Data Box-Auftrag verbundene Speicherkonto oder darin enthaltene spezifische Inhalte löschen.

## <a name="troubleshooting"></a>Problembehandlung

Der Microsoft Azure Recovery Services-Agent (MARS) erstellt für Sie eine Azure Active Directory-Anwendung (Azure AD) in Ihrem Mandanten. Für diese Anwendung ist zu Authentifizierungszwecken ein Zertifikat erforderlich, das erstellt und hochgeladen wird, wenn Sie eine Offlineseedingrichtlinie konfigurieren. Für das Erstellen und Hochladen des Zertifikats für die Azure AD-Anwendung wird Azure PowerShell verwendet.

### <a name="problem"></a>Problem

Wenn Sie eine Offlinesicherung konfigurieren, tritt möglicherweise ein Problem aufgrund eines Fehlers im Azure PowerShell-Cmdlet auf. Sie können möglicherweise nicht mehrere Zertifikate zur selben, vom MAB-Agent erstellten Azure AD-Anwendung hinzufügen. Dieses Problem betrifft Sie, wenn Sie eine Offlineseedingrichtlinie für denselben oder einen anderen Server konfiguriert haben.

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>Überprüfen, ob das Problem durch diese spezifische Grundursache verursacht wird

Um zu ermitteln, ob Ihr Problem mit dem zuvor beschriebenen übereinstimmt, führen Sie einen der folgenden Schritte aus.

#### <a name="step-1-of-verification"></a>Schritt 1 der Überprüfung

Prüfen Sie, ob die folgende Fehlermeldung in der MAB-Konsole angezeigt wird, wenn Sie die Offlinesicherung konfiguriert haben.

![Fehlermeldung „Die Richtlinie zur Offlinesicherung für das aktuelle Azure-Konto konnte nicht erstellt werden […]“](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2-of-verification"></a>Schritt 2 der Überprüfung

1. Öffnen Sie den Ordner **Temp** im Installationspfad. Der Standardpfad für den temporären Ordner ist *C:\Programme\Microsoft Azure Recovery Services Agent\Temp*. Suchen Sie nach der *CBUICurr*-Datei, und öffnen Sie sie.

1. Scrollen Sie in der Datei *CBUICurr* bis zur letzten Zeile, und prüfen Sie, ob das Problem mit dem in dieser Fehlermeldung übereinstimmt: `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`.

### <a name="workaround"></a>Problemumgehung

Als Umgehung dieses Problems können Sie die folgenden Schritte ausführen und noch mal versuchen, die Richtlinie zu konfigurieren.

#### <a name="step-1-of-workaround"></a>Schritt 1 der Problemumgehung

Melden Sie sich bei PowerShell an (wird auf der MAB-Benutzeroberfläche angezeigt). Verwenden Sie dazu ein anderes Konto mit Administratorzugriff auf das Abonnement, für das der Data Box-Auftrag erstellt werden soll.

#### <a name="step-2-of-workaround"></a>Schritt 2 der Problemumgehung

Wenn auf keinem anderen Server Offlineseeding konfiguriert und kein anderer Server von der Anwendung `AzureOfflineBackup_<Azure User Id>` abhängig ist, löschen Sie diese Anwendung. Wählen Sie **Azure-Portal** > **Azure Active Directory** > **App-Registrierungen** aus.

>[!NOTE]
> Überprüfen Sie, ob für die Anwendung `AzureOfflineBackup_<Azure User Id>` kein weiteres Offlineseeding konfiguriert wurde, und vergewissern Sie sich, dass kein weiterer Server von dieser Anwendung abhängig ist. Navigieren Sie zu **Einstellungen** > **Schlüssel** im Abschnitt **Öffentliche Schlüssel**. Es sollten keine weiteren öffentlichen Schlüssel hinzugefügt worden sein. Als Referenz schauen Sie sich den folgenden Screenshot an.
>
>![Öffentliche Schlüssel](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>Schritt 3

Führen Sie auf dem Server, den Sie für die Offlinesicherung konfigurieren möchten, die folgenden Aktionen aus.

1. Navigieren Sie zur Registerkarte **Computerzertifikatanwendung verwalten** > **Persönlich**, und suchen Sie nach dem Zertifikat mit dem Namen `CB_AzureADCertforOfflineSeeding_<ResourceId>`.

2. Wählen Sie das Zertifikat aus, klicken Sie mit der rechten Maustaste auf **Alle Aufgaben**, und wählen Sie **Exportieren** ohne privaten Schlüssel im CER-Format aus.

3. Navigieren Sie zur in Schritt 2 genannten Anwendung, die mit Azure Backup offline gesichert werden soll. Wählen Sie **Einstellungen** > **Schlüssel** > **Öffentlichen Schlüssel hochladen** aus. Laden Sie das Zertifikat hoch, das Sie im vorherigen Schritt exportiert haben.

    ![Öffentlichen Schlüssel hochladen](./media/offline-backup-azure-data-box/upload-public-key.png)

4. Öffnen Sie auf dem Server die Registrierung, indem Sie im Fenster „Ausführen“ **regedit** eingeben.

5. Wechseln Sie zur Registrierung *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider*. Klicken Sie mit der rechten Maustaste auf **CloudBackupProvider**, und fügen Sie einen neuen Zeichenfolgenwert mit dem Namen `AzureADAppCertThumbprint_<Azure User Id>` hinzu.

    >[!NOTE]
    > Führen Sie einen der folgenden Aktionen aus, um die Azure-Benutzer-ID zu ermitteln:
    >
    >- Führen Sie in PowerShell mit Azure-Verbindung den Befehl `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` aus.
    > - Navigieren Sie zum Registrierungspfad `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` mit dem Namen *CurrentUserId*.

6. Klicken Sie mit der rechten Maustaste auf die im vorherigen Schritt hinzugefügte Zeichenfolge, und wählen Sie **Ändern** aus. Geben Sie als Wert den Fingerabdruck des Zertifikats an, das Sie in Schritt 2 exportiert haben. Klicken Sie auf **OK**.

7. Um den Wert des Fingerabdrucks zu erhalten, doppelklicken Sie auf das Zertifikat. Wählen Sie die Registerkarte **Details** aus, und scrollen Sie nach unten, bis das Fingerabdruckfeld angezeigt wird. Wählen Sie **Fingerabdruck** aus, und kopieren Sie den Wert.

    ![Feld „Fingerabdruck“ des Zertifikats](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Fragen

Für Fragen oder Klarstellungen zu Problemen, auf die Sie gestoßen sind, wenden Sie sich bitte an [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com).

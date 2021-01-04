---
title: Offlinesicherung mit Azure Data Box für DPM und MABS
description: Sie können Azure Data Box verwenden, um anfängliche Sicherungsdaten offline von DPM und MABS zu erstellen.
ms.topic: conceptual
ms.date: 08/12/2020
ms.openlocfilehash: 1cfd9131099ad6a8ccd3d43e93f3d97641514f03
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96752548"
---
# <a name="offline-seeding-using-azure-data-box-for-dpm-and-mabs-preview"></a>Offlineseeding mit Azure Data Box für DPM und MABS (Vorschau)

> [!NOTE]
> Diese Funktion gilt für Data Protection Manager (DPM) 2019 UR2 und höher.<br><br>
> Diese Funktion befindet sich derzeit in der Vorschauphase für Microsoft Azure Backup Server (MABS). Falls Sie an der Verwendung von Azure Data Box für das Offlineseeding mit MABS interessiert sind, wenden Sie sich unter [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com) an uns.

In diesem Artikel erfahren Sie, wie Sie Azure Data Box verwenden können, um ein Offlineseeding für Erstsicherungsdaten von DPM und MABS in einen Recovery Services-Tresor von Azure auszuführen.

Sie können [Azure Data Box](../databox/data-box-overview.md) verwenden, um ein Offlineseeding (ohne Verwendung des Netzwerks) für eine große Menge DPM/MABS-Erstsicherungsdaten in einen Recovery Services-Tresor auszuführen. Mit diesem Verfahren können Sie sowohl Zeit als auch Netzwerkbandbreite einsparen, die andernfalls erforderlich wären, um die große Menge an Sicherungsdaten über ein Netzwerk mit hoher Latenz online zu stellen. Diese Funktion steht derzeit als Vorschau zur Verfügung.

Mit einer Offlinesicherung mit Azure Data Box entstehen Ihnen zwei entscheidende Vorteile gegenüber der [Offlinesicherung mit dem Azure Import/Export-Dienst](backup-azure-backup-server-import-export.md):

- Sie benötigen keine eigenen mit Azure kompatiblen Datenträger und Connectors. Die der ausgewählten [Data Box-SKU](https://azure.microsoft.com/services/databox/data/) zugeordneten Datenträger sind in Azure Data Box enthalten.

- Azure Backup (der MARS-Agent) kann die Sicherungsdaten direkt in die unterstützten SKUs des Azure Data Box-Diensts schreiben. Dadurch entfällt die Notwendigkeit, einen Stagingspeicherort für Ihre Erstsicherungsdaten bereitzustellen. Sie benötigen auch keine Hilfsprogramme zum Formatieren dieser Daten und zum Kopieren der Daten auf die Datenträger.

## <a name="supported-platforms"></a>Unterstützte Plattformen

Die folgenden Plattformen werden unterstützt:

- Windows Server 2019 64-Bit (Standard, Rechenzentrum, Essentials)
- Windows Server 2016 64-Bit (Standard, Rechenzentrum, Essentials)

## <a name="backup-data-size-and-supported-data-box-skus"></a>Größe von Sicherungsdaten und unterstützte Data Box-SKUs

Die folgenden Data Box SKUs werden unterstützt:

| Größe der Sicherungsdaten (nach MARS-Komprimierung)\* pro Server | Unterstützte Azure Data Box-SKU |
| --- | --- |
| \<= 7,2 TB | [Azure Data Box Disk](../databox/data-box-disk-overview.md) |
| > 7,2 TB und <= 80 TB\*\* | [Azure Data Box (100 TB)](../databox/data-box-overview.md) |

\*Typische Komprimierungsraten variieren zwischen 10 und 20 Prozent <br>
\*\*Wenden Sie sich an [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com), wenn Sie vermuten, dass Sie über mehr als 80 TB an Erstsicherungsdaten für eine einzelne Datenquelle verfügen.

> [!IMPORTANT]
> Erstsicherungsdaten von einer einzelnen Datenquelle müssen in einer einzelnen Azure Data Box oder Azure Data Box Disk enthalten sein und können nicht für mehrere Geräte derselben oder verschiedener SKUs freigegeben werden. Eine Azure Data Box kann jedoch Erstsicherungsdaten von mehreren Datenquellen enthalten.

## <a name="before-you-begin"></a>Voraussetzungen

Der auf DPM/MABS ausgeführte MARS-Agent sollte auf die [neueste Version](https://aka.ms/azurebackup_agent) (2.0.9171.0 oder höher) aktualisiert werden.

Stellen Sie Folgendes sicher:

### <a name="azure-subscription-and-required-permissions"></a>Azure-Abonnement und erforderliche Berechtigungen

- Ein gültiges Azure-Abonnement.
- Der Benutzer, der die Offlinesicherungsrichtlinie ausführen soll, muss ein Besitzer des Azure-Abonnements sein.
- Der Data Box-Auftrag und der Recovery Services-Tresor, der Ziel des Datenseedings ist, müssen sich in denselben Abonnements befinden.
    > [!NOTE]
    > Das Zielspeicherkonto und der Recovery Services-Tresor sollten sich am besten in derselben Region befinden. Dies ist aber nicht zwingend erforderlich.

### <a name="order-and-receive-the-data-box-device"></a>Bestellen und Empfangen des Data Box-Geräts

Stellen Sie sicher, dass die erforderlichen Data Box-Geräte sich im Zustand *Geliefert* befinden, bevor Sie eine Offlinesicherung auslösen. Im Abschnitt [Größe von Sicherungsdaten und unterstützte Data Box-SKUs](#backup-data-size-and-supported-data-box-skus) finden Sie die nötigen Informationen, um die für Ihre Anforderung am besten geeignete SKU zu bestellen. Führen Sie die Schritte in [diesem Artikel](../databox/data-box-disk-deploy-ordered.md) aus, um Ihre Data Box-Geräte zu bestellen und zu empfangen.

> [!IMPORTANT]
> Wählen Sie als **Kontoart** nicht *BlobStorage* aus. Für den DPM/MABS-Server wird ein Konto benötigt, das Seitenblobs unterstützt. Dies ist nicht der Fall, wenn *BlobStorage* ausgewählt wird. Wählen Sie bei der Erstellung des Zielspeicherkontos für Ihren Azure Data Box-Auftrag als **Kontoart** die Option **StorageV2 (universell, Version 2)** aus.

![Einrichten von Azure Data Box](./media/offline-backup-azure-data-box-dpm-mabs/setup-azure-databox.png)

## <a name="setup-azure-data-box-devices"></a>Einrichten von Azure Data Box-Geräten

Wenn Sie das Azure Data Box-Gerät erhalten haben, führen Sie je nach der von Ihnen bestellten Azure Data Box-SKU die unten in den jeweiligen Abschnitten erläuterten Schritte aus, um die Data Box-Geräte für den DPM/MABS-Server einzurichten und vorzubereiten und dann die Erstsicherungsdaten identifizieren und übertragen zu können.

### <a name="setup-azure-data-box-disk"></a>Einrichten von Azure Data Box Disk

Führen Sie die [hier](../databox/data-box-disk-deploy-set-up.md) genannten Schritte zum Entpacken, Verbinden und Entsperren der Data Box Disk aus, wenn Sie mindestens eine Azure Data Box Disk bestellt haben (jeweils bis zu 8 TB).

> [!NOTE]
> Es ist möglich, dass der DPM/MABS-Server keinen USB-Anschluss hat. In diesem Szenario können Sie Ihre Azure Data Box Disk mit einem anderen Server/Client verbinden und den Gerätepfad als Netzwerkfreigabe angeben.

## <a name="setup-azure-data-box"></a>Einrichten von Azure Data Box

Führen Sie die [hier](../databox/data-box-deploy-set-up.md) genannten Schritte zum Einrichten Ihrer Data Box aus, wenn Sie eine Azure Data Box (bis zu 100 TB) bestellt haben.

### <a name="mount-your-azure-data-box-as-local-system"></a>Einbinden Ihrer Azure Data Box als lokales System

Der DPM/MABS-Server wird im Kontext des Systems ausgeführt. Deshalb müssen für den Einbindungspfad, über den die Azure Data Box verbunden ist, dieselben Berechtigungen gewährt werden. Führen Sie die unten aufgeführten Schritte aus, damit Sie Ihr Data Box-Gerät mithilfe des NFS-Protokolls als lokales System einbinden können.

1. Aktivieren Sie die „Client für NFS“-Funktion auf dem DPM/MABS-Server.
Geben Sie als alternative Quelle Folgendes an: *WIM:D:\Sources\Install.wim:4*
2. Laden Sie **PSExec** von [https://download.sysinternals.com/files/PSTools.zip](https://download.sysinternals.com/files/PSTools.zip) auf den DPM/MABS-Server herunter.
3. Öffnen Sie eine Eingabeaufforderung mit erhöhten Rechten, und führen Sie den folgenden Befehl aus. Darin muss *PSExec.exe* als aktuelles Verzeichnis enthalten sein.

   ```cmd
   psexec.exe  -s  -i  cmd.exe
   ```

4. Das Befehlsfenster, das durch den obigen Befehl geöffnet wird, befindet sich im lokalen Systemkontext. Führen Sie die Schritte zum Einbinden der Seitenblobfreigabe von Azure als Netzwerklaufwerk auf Ihrem Windows-Server in diesem Befehlsfenster aus.
5. Führen Sie die [hier](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box) aufgeführten Schritte aus, um Ihren DPM/MABS-Server über NFS mit dem Data Box-Gerät zu verbinden. Führen Sie dann den folgenden Befehl in der Eingabeaufforderung des lokalen Systems aus, um die Seitenblobfreigabe von Azure einzubinden:

    ```cmd
    mount -o nolock \\<DeviceIPAddres>\<StorageAccountName_PageBlob X:
    ```

6. Überprüfen Sie, ob Sie über Ihren Server auf X: zugreifen können, sobald die Einbindung abgeschlossen ist. Wenn dies der Fall ist, fahren Sie mit dem nächsten Abschnitt dieses Artikels fort.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Übertragen von Erstsicherungsdaten auf Azure Data Box-Geräte

1. Führen Sie auf dem DPM/MABS-Server die Schritte zum [Erstellen einer neuen Schutzgruppe](/system-center/dpm/create-dpm-protection-groups) aus. Wenn Sie der vorhandenen Schutzgruppe einen Onlineschutz hinzufügen, klicken Sie mit der rechten Maustaste auf die vorhandene Schutzgruppe, wählen Sie **Onlineschutz hinzufügen aus**, und beginnen Sie mit **Schritt 8**.
2. Geben Sie auf der Seite **Gruppenmitglieder auswählen** die Computer und Quellen an, die Sie sichern möchten.
3. Wählen Sie auf der Seite **Datenschutzmethode auswählen** aus, wie Sie die kurz- und langfristige Sicherung handhaben möchten. Stellen Sie sicher, dass Sie **Onlineschutz** auswählen.

   ![Erstellen einer neuen Schutzgruppe](./media/offline-backup-azure-data-box-dpm-mabs/create-new-protection-group.png)

4. Wählen Sie auf der Seite **Kurzfristige Ziele auswählen** aus, wie die Sicherung in kurzfristigem Speicher auf Datenträger erfolgen soll.
5. Überprüfen Sie auf der Seite **Datenträgerzuordnungen überprüfen** den Speicherplatz im Speicherpool, der der Schutzgruppe zugeordnet ist.
6. Wählen Sie auf der Seite **Replikaterstellungsmethode auswählen** die Option **Automatisch über das Netzwerk** aus.
7. Wählen Sie auf dem Bildschirm **Konsistenzprüfungsoptionen auswählen** aus, wie Konsistenzprüfungen automatisiert werden sollen.
8. Wählen Sie auf der Seite **Onlineschutzdaten angeben** das Mitglied aus, für das Sie den Onlineschutz aktivieren möchten.

    ![Onlineschutzdaten angeben](./media/offline-backup-azure-data-box-dpm-mabs/specify-online-protection-data.png)

9. Geben Sie auf der Seite **Onlinesicherungszeitplan angeben** an, wie oft inkrementelle Sicherungen in Azure erfolgen sollen.
10. Geben Sie auf der Seite **Onlineaufbewahrungsrichtlinie angeben** an, wie die Wiederherstellungspunkte aus den täglichen, wöchentlichen, monatlichen und jährlichen Sicherungen in Azure aufbewahrt werden.
11. Wählen Sie auf dem Bildschirm **Onlinereplikation auswählen** des Assistenten die Option **Mit Datenträgern von Microsoft übertragen** und dann **Weiter** aus.

    ![Auswählen der anfänglichen Onlinereplikation](./media/offline-backup-azure-data-box-dpm-mabs/choose-initial-online-replication.png)

    >[!NOTE]
    > Die Option **Mit Datenträgern von Microsoft übertragen** steht für MABS v3 nicht zur Verfügung, weil sich das Feature in der Vorschauphase befindet. Wenden Sie sich unter [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com) an uns, wenn Sie dieses Feature für MABS v3 verwenden möchten.

12. Melden Sie sich bei einer entsprechenden Aufforderung bei Azure an, und verwenden Sie dazu die Benutzeranmeldeinformationen, mit denen Sie als Besitzer Zugriff auf das Azure-Abonnement haben. Nach erfolgreicher Anmeldung wird der folgende Bildschirm angezeigt:

    ![Nach erfolgreicher Anmeldung](./media/offline-backup-azure-data-box-dpm-mabs/after-successful-login.png)

     Der DPM/MABS-Server ruft dann die Data Box-Aufträge im Abonnement ab, die den Zustand *Geliefert* aufweisen.

     > [!NOTE]
     > Die erste Anmeldung dauert länger als üblich. Das Azure PowerShell-Modul wird im Hintergrund installiert, und außerdem wird die Azure AD Anwendung registriert.
     >
     >  - Die folgenden PowerShell-Module werden installiert:<br>
          – AzureRM.Profile     *5.8.3*<br>
          – AzureRM.Resources   *6.7.3*<br>
          – AzureRM.Storage     *5.2.0*<br>
          – Azure.Storage       *4.6.1*<br>
     >  - Die Azure AD-Anwendung wird als *AzureOfflineBackup_\<object GUID of the user>* registriert.

13. Wählen Sie den Data Box-Auftrag aus, für den Sie Ihren Data Box-Datenträger entpackt, verbunden und entsperrt haben. Wählen Sie **Weiter** aus.

    ![Auswählen der Databox](./media/offline-backup-azure-data-box-dpm-mabs/select-databox.png)

14. Geben Sie auf dem Bildschirm **Data Box erkennen** den Pfad Ihres Data Box-Geräts ein, und wählen Sie dann **Gerät erkennen** aus.

    ![Eingeben des Netzwerkpfads](./media/offline-backup-azure-data-box-dpm-mabs/enter-network-path.png)

    > [!IMPORTANT]
    > Geben Sie den Netzwerkpfad zum Stammverzeichnis des Azure Data Box-Datenträgers an. Wie Sie unten sehen, muss dieses Verzeichnis ein Verzeichnis namens *PageBlob* beinhalten:
    >
    > ![USB-Laufwerk](./media/offline-backup-azure-data-box-dpm-mabs/usb-drive.png)
    >
    > Wenn der Pfad auf dem Datenträger beispielsweise `\\mydomain\myserver\disk1\` ist und *disk1* ein Verzeichnis namens *PageBlob* enthält, muss im DPM/MABS-Server-Assistenten `\\mydomain\myserver\disk1\` als Pfad angegeben werden.
    > Beim [Einrichten eines Azure Data Box-Geräts mit 100 TB](./offline-backup-azure-data-box.md#set-up-azure-data-box) müssen Sie `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` als Netzwerkpfad zum Gerät angeben.

15. Wählen Sie **Weiter** aus. Überprüfen Sie auf der Seite **Zusammenfassung** Ihre Einstellungen, und wählen Sie dann **Gruppe erstellen** aus.

    ![Erkennen der Data Box](./media/offline-backup-azure-data-box-dpm-mabs/detect-databox.png)

    Im folgenden Bildschirm wird bestätigt, dass die Schutzgruppe erfolgreich gespeichert wurde.

    ![Erstellte Schutzgruppe](./media/offline-backup-azure-data-box-dpm-mabs/protection-group-created.png)

16. Wählen Sie oben im Bildschirm **Schließen** aus.

    Dadurch erfolgt die erste Replikation der Daten auf dem DPM/MABS-Datenträger. Nach Abschluss des Schutzes wird der Status der Gruppe auf der Seite **Status** als **OK** angezeigt.

17. Um die Offlinesicherungskopie auf Ihr Azure Data Box-Gerät zu initiieren, klicken Sie mit der rechten Maustaste auf die **Schutzgruppe**, und wählen Sie dann die Option **Wiederherstellungspunkt erstellen** aus. Wählen Sie anschließend die Option **Onlineschutz** aus.

    ![Wiederherstellungspunkt erstellen](./media/offline-backup-azure-data-box-dpm-mabs/create-recovery-point.png)

    ![Wiederherstellungspunkt](./media/offline-backup-azure-data-box-dpm-mabs/recovery-point.png)

   Der DPM/MABS-Server beginnt dann mit der Sicherung der von Ihnen ausgewählten Daten auf dem Azure Data Box-Gerät. Das kann je nach Größe der Daten und der Geschwindigkeit der Verbindung zwischen dem DPM/MABS-Server und der Azure Data Box Disk ein paar Stunden bis hin zu einigen Tagen dauern.

   Sie können den Fortschritt des Auftrags im Bereich **Überwachung** überwachen. Sobald die Sicherung der Daten abgeschlossen ist, wird ein Bildschirm ähnlich dem folgenden angezeigt:

   ![Verwaltungskonsole](./media/offline-backup-azure-data-box-dpm-mabs/administrator-console.png)

## <a name="post-backup-steps"></a>Schritte nach der Sicherung

Führen Sie die folgenden Schritte aus, nachdem die Datensicherung auf der Azure Data Box Disk erfolgreich war.

- Führen Sie die Schritte in [diesem Artikel](../databox/data-box-disk-deploy-picked-up.md) zum Zurücksenden der Azure Data Box Disk aus. Führen Sie [diese Schritte](../databox/data-box-deploy-picked-up.md) zum Zurücksenden der Azure Data Box an Azure aus, wenn Sie ein Azure Data Box-Gerät mit 100 TB verwendet haben.
- [Überprüfen Sie den Datenupload aus Azure Data Box Disk](../databox/data-box-disk-deploy-upload-verify.md) im Azure-Portal. Sobald der Azure Data Box-Auftrag *Abgeschlossen* ist, verschiebt der DPM/MABS-Server die Daten zum Zeitpunkt der nächsten geplanten Sicherung automatisch aus dem Speicherkonto in den Recovery Services-Tresor. Der Sicherungsauftrag wird dann mit *Auftrag abgeschlossen* markiert, sobald erfolgreich ein Wiederherstellungspunkt erstellt wurde.

  > [!NOTE]
  > Der DPM/MABS-Server löst die Sicherungen zu den Zeitpunkten aus, die während der Erstellung der Schutzgruppe geplant worden sind. Diese Aufträge werden jedoch als *Warten auf Abschluss des Azure Data Box-Auftrags* gekennzeichnet, bis der Auftrag abgeschlossen ist.

- Nachdem der DPM/MABS-Server erfolgreich einen der Erstsicherung entsprechenden Wiederherstellungspunkt erstellt hat, können Sie das mit dem Azure Data Box-Auftrag verbundene Speicherkonto oder darin enthaltene spezifische Inhalte löschen.

## <a name="troubleshooting"></a>Problembehandlung

Der Microsoft Azure Backup-Agent (MAB) auf dem DPM-Server erstellt für Sie eine Azure AD-Anwendung in Ihrem Mandanten. Für diese Anwendung ist zu Authentifizierungszwecken ein Zertifikat erforderlich, das bei der Konfiguration der Offlineseedingrichtlinie erstellt und hochgeladen wird.

Für das Erstellen und Hochladen des Zertifikats für die Azure AD-Anwendung wird Azure PowerShell verwendet.

### <a name="issue"></a>Problem

Beim Konfigurieren der Offlinesicherung können Sie aufgrund eines unbekannten Codedefekts im Azure PowerShell-Cmdlet nicht mehrere Zertifikate für dieselbe vom MAB-Agent erstellte Azure AD-Anwendung hinzufügen. Dies kann hinderlich sein, wenn Sie eine Offlineseedingrichtlinie für denselben oder einen anderen Server konfiguriert haben.

### <a name="verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>Überprüfen, ob das Problem durch diese spezifische Grundursache verursacht wird

Führen Sie einen der folgenden Schritte aus, um zu überprüfen, ob der Fehler auf obiges [Problem](#issue) zurückzuführen ist:

#### <a name="step-1"></a>Schritt 1

Überprüfen Sie, ob die folgende Fehlermeldung in der DPM/MAB-Konsole angezeigt wird, wenn Sie die Offlinesicherung konfigurieren:

![Azure Recovery Services-Agent](./media/offline-backup-azure-data-box-dpm-mabs/azure-recovery-services-agent.png)

#### <a name="step-2"></a>Schritt 2

1. Öffnen Sie den **Temp**-Ordner im Installationspfad. Der Standardpfad zum Temp-Ordner ist *C:\Programme\Microsoft Azure Recovery Services Agent\Temp*. Suchen Sie nach der *CBUICurr*-Datei, und öffnen Sie sie.
2. Scrollen Sie in der Datei *CBUICurr* zur letzten Zeile, und überprüfen Sie, ob der Fehler auf „Anmeldeinformationen für Azure AD-Anwendung können nicht im Kundenkonto erstellt werden. Ausnahme: Aktualisieren auf vorhandene Anmeldeinformationen mit KeyId \<some guid> ist nicht zulässig“ zurückzuführen ist.

### <a name="workaround"></a>Problemumgehung

Um dieses Problems zu lösen, führen Sie die folgenden Schritte aus und versuchen Sie erneut, die Richtlinie zu konfigurieren.

1. Melden Sie sich auf der Azure-Anmeldeseite an, die auf der DPM/MABS-Server-Benutzeroberfläche angezeigt wird. Verwenden Sie dazu ein anderes Konto mit Administratorzugriff auf das Abonnement, für das der Data Box-Auftrag erstellt werden soll.
2. Wenn das Offlineseeding für keinen anderen Server konfiguriert wurde und kein anderer Server von der `AzureOfflineBackup_<Azure User Id>`-Anwendung abhängig ist, löschen Sie diese Anwendung über **Azure-Portal > Azure Active Directory > App-Registrierungen**.

   > [!NOTE]
   > Überprüfen Sie, ob für die Anwendung `AzureOfflineBackup_<Azure User Id>` kein weiteres Offlineseeding konfiguriert wurde, und außerdem, ob kein weiterer Server von dieser Anwendung abhängig ist. Navigieren Sie unter dem Abschnitt „Öffentliche Schlüssel“ zu **Einstellungen > Schlüssel**. Es sollten keine weiteren **öffentlichen Schlüssel** hinzugefügt worden sein. Als Referenz können Sie sich den folgenden Screenshot ansehen:
   >
   > ![Öffentliche Schlüssel](./media/offline-backup-azure-data-box-dpm-mabs/public-keys.png)

#### <a name="step-3"></a>Schritt 3

Führen Sie auf dem DPM/MABS-Server, den Sie für die Offlinesicherung konfigurieren möchten, die folgenden Aktionen aus:

1. Öffnen Sie die Registerkarte **Computerzertifikatanwendung verwalten**  > **Persönlich**, und suchen Sie nach dem Zertifikat mit dem Namen `CB_AzureADCertforOfflineSeeding_<ResourceId>`.
2. Wählen Sie das Zertifikat oben aus, klicken Sie mit der rechten Maustaste auf **Alle Aufgaben**, und führen Sie einen **Export** ohne privaten Schlüssel im CER-Format aus.
3. Navigieren Sie zur in **Schritt 2** genannten Anwendung, die mit Azure Backup offline gesichert werden soll. Laden Sie über **Einstellungen** > **Schlüssel** > **Öffentlichen Schlüssel hochladen** das im obigen Schritt exportierte Zertifikat hoch.

   ![Hochladen der öffentlichen Schlüssel](./media/offline-backup-azure-data-box-dpm-mabs/upload-public-keys.png)

4. Öffnen Sie durch Eingabe von **regedit** im Fenster **Ausführen** die Registrierung auf dem Server.
5. Wechseln Sie zur Registrierung *Computer\HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider*. Klicken Sie mit der rechten Maustaste auf **CloudBackupProvider**, und fügen Sie einen neuen Zeichenfolgenwert mit dem Namen `AzureADAppCertThumbprint_<Azure User Id>` hinzu.

    >[!NOTE]
    > Führen Sie einen der folgenden Aktionen aus, um die Azure-Benutzer-ID zu ermitteln:
    >
    >- Führen Sie in PowerShell mit Azure-Verbindung den Befehl `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` aus.
    > - Navigieren Sie zum Registrierungspfad `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` mit dem Namen *CurrentUserId*.

6. Klicken Sie zunächst mit der rechten Maustaste auf die im vorherigen Schritt hinzugefügte Zeichenfolge, und wählen Sie dann **Ändern** aus. Geben Sie als Wert den Fingerabdruck des Zertifikats an, das Sie in **Schritt 2** exportiert haben, und wählen Sie **OK** aus.
7. Doppelklicken Sie auf das Zertifikat, wählen Sie **Details** aus, und scrollen Sie nach unten, bis das Feld „Fingerabdruck“ angezeigt wird, um den Wert des Fingerabdrucks zu erhalten. Wählen Sie **Fingerabdruck** aus, und kopieren Sie den Wert.

   ![Fingerabdruckwert](./media/offline-backup-azure-data-box-dpm-mabs/certificate.png)

## <a name="next-steps"></a>Nächste Schritte

- [Offlineseeding mit eigenem Datenträger (unter Verwendung des Azure Import/Export-Diensts)](backup-azure-backup-server-import-export.md)

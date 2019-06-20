---
title: 'Azure Backup: Offlinesicherung für DPM und Azure Backup Server'
description: Erfahren Sie, wie Sie mit Azure Backup mithilfe des Azure Import/Export-Diensts Daten aus dem Netzwerk senden können. Dieser Artikel erläutert das Offlineseeding der ersten Sicherungsdaten mit dem Azure Import/Export-Dienst.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 5/8/2018
ms.author: saurse
ms.openlocfilehash: 18f84062bcaf2766ee0abd5248f876c3d8acef3f
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304017"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>Offlinesicherungsworkflow für DPM und Azure Backup Server
Azure Backup verfügt über mehrere integrierte effizienzsteigernde Funktionen, die die Netzwerk- und Speicherkosten bei den ersten vollständigen Datensicherungen in Azure reduzieren. Bei den ersten vollständigen Sicherungen werden meist große Datenmengen übertragen, sodass eine höhere Netzwerkbandbreite als bei den nachfolgenden Sicherungen erforderlich ist, bei denen nur die Deltamengen bzw. Inkremente übertragen werden. Azure Backup komprimiert die Erstsicherungen. Durch den Prozess des Offlineseedings kann Azure Backup Datenträger verwenden, um die komprimierten Daten der Erstsicherungen offline in Azure hochzuladen.

Der Offlineseeding-Prozess von Azure Backup ist eng in den [Import/Export-Dienst von Azure](../storage/common/storage-import-export-service.md) integriert, der Ihnen die Übertragung von Daten mithilfe von Datenträgern in Azure ermöglicht. Wenn Sie bei der Erstsicherung mehrere Terabyte an Daten über ein Netzwerk mit hoher Latenz und niedriger Bandbreite übertragen müssen, können Sie die erste Sicherungskopie mithilfe des Offlineseeding-Workflows auf einer oder mehreren Festplatten an ein Azure-Datencenter senden. Dieser Artikel bietet eine Übersicht sowie Details zu den für diesen Workflow erforderlichen Schritten für System Center DPM und Azure Backup Server.

> [!NOTE]
> Der Prozess der Offlinesicherung für den MARS-Agent (Microsoft Azure Recovery Services) unterscheidet sich vom Prozess für System Center DPM und Azure Backup Server. Informationen zur Verwendung der Offlinesicherung mit MARS-Agent finden Sie in [diesem Artikel](backup-azure-backup-import-export.md). Die Offlinesicherung wird für Sicherungen des Systemstatus mithilfe des Azure Backup-Agents nicht unterstützt.
>

## <a name="overview"></a>Übersicht
Mit der Offlineseeding-Funktion von Azure Backup und Azure Import/Export können die Daten einfach mithilfe von Datenträgern offline in Azure hochgeladen werden. Der Vorgang der Offlinesicherung umfasst die folgenden Schritte:

> [!div class="checklist"]
> * Die Sicherungsdaten werden nicht über das Netzwerk gesendet, sondern an einen *Stagingspeicherort* gespeichert.
> * Die Daten am *Stagingspeicherort* werden dann mithilfe des Hilfsprogramms *AzureOfflineBackupDiskPrep* auf SATA-Datenträger geschrieben.
> * Ein Azure-Importauftrag wird automatisch vom Hilfsprogramm erstellt.
> * Die SATA-Datenträger werden dann an das nächste Azure-Rechenzentrum gesendet.
> * Nachdem die Sicherungsdaten in Azure hochgeladen wurden, kopiert Azure Backup die Sicherungsdaten in den Sicherungstresor, und die inkrementellen Sicherungen werden geplant.

## <a name="supported-configurations"></a>Unterstützte Konfigurationen
Die Offlinesicherung wird für alle Bereitstellungsmodelle von Azure Backup unterstützt, die lokale Sicherungsdaten extern in die Microsoft Cloud auslagern. Dies umfasst:

> [!div class="checklist"]
> * Sichern von Dateien und Ordnern mit dem Microsoft Azure Recovery Services-Agent (MARS) oder dem Azure Backup-Agent
> * Sichern aller Workloads und Dateien mit dem System Center Data Protection Manager (DPM SC)
> * Sichern aller Workloads und Dateien mit dem Microsoft Azure Backup Server <br/>

## <a name="prerequisites"></a>Voraussetzungen
Stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt sind, bevor Sie den Offlinesicherungs-Workflow initiieren.
* Es wurde ein [Recovery Services-Tresor](backup-azure-recovery-services-vault-overview.md) erstellt. Weitere Informationen zur Erstellung finden Sie in den Schritten in [diesem Artikel](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault).
* Der Azure Backup-Agent oder der Azure Backup Server oder SC DPM wurden auf dem Windows-Server oder dem Windows-Client installiert, und der Computer ist beim Recovery Services-Tresor registriert. Vergewissern Sie sich, dass [die neueste Version von Azure Backup](https://go.microsoft.com/fwlink/?linkid=229525) verwendet wird.
* [Laden Sie die Datei mit den Azure-Veröffentlichungseinstellungen](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) auf den Computer herunter, von dem aus Sie Ihre Daten sichern möchten. Das Abonnement, aus dem Sie die Datei mit den Veröffentlichungseinstellungen herunterladen, kann ein anderes als das mit dem Recovery Services-Tresor sein. Wenn sich Ihr Abonnement in Sovereign Clouds von Azure befindet, verwenden Sie die folgenden Links zum Herunterladen der Datei mit den Veröffentlichungseinstellungen für Azure.

    | Region der Sovereign Cloud | Link zur Datei mit den Azure-Veröffentlichungseinstellungen |
    | --- | --- |
    | USA | [Link](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | China | [Link](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Es wurde ein Azure Storage-Konto mit dem *klassischen* Bereitstellungsmodell in dem Abonnement erstellt, aus dem Sie die Datei mit den Veröffentlichungseinstellungen heruntergeladen haben, wie im Folgenden dargestellt:

  ![Erstellen klassischer Speicherkonten](./media/backup-azure-backup-import-export/storageaccountclassiccreate.png)

* Es wurde ein Stagingspeicherort, bei dem es sich um eine Netzwerkfreigabe oder ein zusätzliches Laufwerk auf dem Computer (intern oder extern) handeln kann, mit genügend Speicherplatz zum Speichern der Erstkopie erstellt. Wenn Sie beispielsweise einen 500-GB-Dateiserver sichern möchten, muss der Stagingbereich mindestens 500 GB groß sein. (Aufgrund der Komprimierung wird weniger Speicherplatz genutzt.)
* Bei Datenträgern, die an Azure gesendet werden sollen, stellen Sie sicher, dass nur interne 2,5-Zoll-SSD- oder 2,5-Zoll- bzw. 3,5-Zoll-SATA II/III-Festplatten verwendet werden. Sie können Festplatten mit bis zu 10 TB verwenden. Schlagen Sie in der [Dokumentation zum Azure Import/Export-Dienst](../storage/common/storage-import-export-requirements.md#supported-hardware) die aktuell vom Dienst unterstützten Laufwerke nach.
* Die SATA-Laufwerke müssen mit einem Computer verbunden sein (als *Kopiercomputer* bezeichnet), auf dem die Sicherungsdaten vom *Stagingspeicherort* auf die SATA Laufwerke kopiert werden. Stellen Sie sicher, dass BitLocker auf dem *Kopiercomputer* aktiviert ist.

## <a name="workflow"></a>Workflow
In diesem Abschnitt wird erläutert, wie Sie den Workflow zur Offlinesicherung durchführen, sodass Ihre Daten an ein Azure-Datencenter gesendet und in Azure Storage hochgeladen werden können. Falls Sie Fragen zum Import-Dienst oder zu einem anderen Aspekt des Prozesses haben, finden Sie in der oben erwähnten Dokumentation eine [Übersicht über den Import-Dienst](../storage/common/storage-import-export-service.md) .

### <a name="initiate-offline-backup"></a>Initiieren der Offlinesicherung
1. Beim Planen einer Sicherung wird der folgende Bildschirm angezeigt (in Windows Server, auf dem Windows-Client oder in System Center Data Protection Manager).

    ![Importbildschirm](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Hier sehen Sie den entsprechenden Bildschirm in System Center Data Protection Manager:  <br/>
    ![SC DPM und Azure Backup Server – Importbildschirm](./media/backup-azure-backup-import-export/dpmoffline.png)

    Die Beschreibung der Eingaben lautet wie folgt:

   * **Stagingspeicherort**: Der temporäre Speicherort, an den die erste Sicherungskopie geschrieben wird. Beim Stagingspeicherort kann es sich um eine Netzwerkfreigabe oder einen lokalen Computer handeln. Wenn der Kopiercomputer und der Quellcomputer nicht identisch sind, wird empfohlen, den vollständigen Netzwerkpfad des Stagingspeicherorts anzugeben.
   * **Name des Azure Importauftrags**: Der eindeutige Name, anhand dessen der Azure Import-Dienst und Azure Backup Datenübertragungen nachverfolgen, die auf Datenträgern an Azure gesendet werden.
   * **Azure-Veröffentlichungseinstellungen**: Geben Sie den lokalen Pfad zu der Datei mit den Veröffentlichungseinstellungen an.
   * **Azure-Abonnement-ID**: Azure-Abonnement-ID für das Abonnement, aus dem Sie die Datei mit den Azure-Veröffentlichungseinstellungen heruntergeladen haben.
   * **Azure Storage-Konto**: Name des Speicherkontos im Azure-Abonnement, das der Datei mit den Azure-Veröffentlichungseinstellungen zugeordnet ist.
   * **Azure Storage-Container**: Name des Zielspeicherblobs im Azure Storage-Konto, in das die Sicherungsdaten importiert werden.

     Speichern Sie den *Stagingspeicherort* und den angegebenen *Namen des Azure-Importauftrags*, da Sie diese Informationen für die Vorbereitung der Datenträger benötigen.  

2. Schließen Sie den Workflow ab, und klicken Sie in der Verwaltungskonsole des Azure Backup-Agents auf **Jetzt sichern**, um die Offlinesicherungskopie zu initiieren. Die erste Sicherung wird bei diesem Schritt in den Stagingbereich geschrieben.

    ![Jetzt sichern](./media/backup-azure-backup-import-export/backupnow.png)

    Um den entsprechenden Workflow im System Center Data Protection Manager oder auf dem Azure Backup-Server auszuführen, klicken Sie mit der rechten Maustaste auf **Schutzgruppe**, und wählen Sie dann die Option **Wiederherstellungspunkt erstellen** aus. Wählen Sie anschließend die Option **Onlineschutz** aus.

    ![SC DPM und Azure Backup Server – „Jetzt sichern“](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Nach Abschluss des Vorgangs kann der Stagingspeicherort für die Datenträgervorbereitung verwendet werden.

    ![Sicherungsstatus](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Vorbereiten der SATA-Laufwerke und Senden an Azure
Das Hilfsprogramm *AzureOfflineBackupDiskPrep* wird zum Vorbereiten der SATA-Laufwerke verwendet, die an das nächste Azure-Rechenzentrum gesendet werden. Dieses Hilfsprogramm steht im Installationsverzeichnis des Recovery Services-Agents unter folgendem Pfad zur Verfügung:

*\\Microsoft Azure Recovery Services-Agent\\Hilfsprogramme\\*

1. Navigieren Sie zu diesem Verzeichnis, und kopieren Sie das Verzeichnis **AzureOfflineBackupDiskPrep** auf einen Kopiercomputer, mit dem die vorzubereitenden SATA-Laufwerke verbunden sind. Überprüfen Sie für den Kopiercomputer Folgendes:

   * Der Kopiercomputer kann über denselben Netzwerkpfad, der während des Workflows **Initiieren der Offlinesicherung** angegeben wurde, auf den Stagingspeicherort für den Offlineseeding-Workflow zugreifen.
   * BitLocker ist auf dem Kopiercomputer aktiviert.
   * Der Kopiercomputer kann auf das Azure-Portal zugreifen.

     Sofern erforderlich, kann der Kopiercomputer mit dem Quellcomputer identisch sein.

     > [!IMPORTANT]
     > Wenn der Quellcomputer ein virtueller Computer ist, muss ein anderer physischer Server- oder Clientcomputer als Kopiercomputer verwendet werden.


2. Öffnen Sie auf dem Kopiercomputer eine Eingabeaufforderung mit erhöhten Rechten und dem Verzeichnis des Hilfsprogramms *AzureOfflineBackupDiskPrep* als aktuelles Verzeichnis. Führen Sie dann den folgenden Befehl aus:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parameter | BESCHREIBUNG |
    | --- | --- |
    | s:&lt;*Pfad zum Stagingspeicherort*&gt; |Obligatorische Eingabe zum Angeben des Pfads zum Stagingspeicherort, den Sie im Workflow **Initiieren der Offlinesicherung** eingegeben haben. |
    | p:&lt;*Pfad zu PublishSettingsFile*&gt; |Optionale Eingabe zum Angeben des Pfads zur Datei **Azure-Veröffentlichungseinstellungen**, den Sie im Workflow **Initiieren der Offlinesicherung** eingegeben haben. |

    > [!NOTE]
    > Der Wert &lt;Pfad zu PublishSettingFile&gt; ist obligatorisch, wenn der Kopiercomputer und der Quellcomputer nicht identisch sind.
    >
    >

    Beim Ausführen des Befehls fordert das Hilfsprogramm die Auswahl des Azure-Importauftrags an, der den vorzubereitenden Datenträgern entspricht. Falls dem angegebenen Stagingspeicherort nur ein einzelner Importauftrag zugeordnet ist, wird ein Bildschirm wie in der folgenden Abbildung angezeigt.

    ![Azure-Tool für die Datenträgervorbereitung – Eingabe](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>

3. Geben Sie den Laufwerkbuchstaben ohne nachgestellten Doppelpunkt für den bereitgestellten Datenträger an, den Sie für die Übertragung an Azure vorbereiten möchten. Bestätigen Sie die Formatierung des Laufwerks, wenn Sie dazu aufgefordert werden.

    Das Tool beginnt dann, den Datenträger vorzubereiten und die Sicherungsdaten zu kopieren. Falls auf dem bereitgestellten Datenträger nicht genügend Speicherplatz für die Sicherungsdaten verfügbar ist, müssen Sie unter Umständen weitere Datenträger anfügen, wenn Sie vom Tool dazu aufgefordert werden. <br/>

    Nach der erfolgreichen Ausführung des Tools sind ein oder mehrere Datenträger, die Sie angegeben haben, für den Versand an Azure vorbereitet. Außerdem wird in Azure ein Importauftrag mit dem Namen erstellt, den Sie im Workflow **Initiieren der Offlinesicherung** angegeben haben. Zum Schluss zeigt das Hilfsprogramm die Lieferadresse des Azure-Rechenzentrums an, an das die Datenträger gesendet werden müssen.

    ![Azure-Tool für die Datenträgervorbereitung – Abgeschlossen](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

4. Am Ende der Befehlsausführung wird auch die Option zum Aktualisieren der Versandinformationen wie unten dargestellt angezeigt:

    ![Option zum Aktualisieren der Versandinformationen](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

5. Sie können die Details sofort eingeben. Das Tool führt Sie durch die verschiedenen Eingaben. Wenn Ihnen Informationen wie die Nachverfolgungsnummer oder andere Daten zum Versand nicht bekannt sind, können Sie die Sitzung auch beenden. Sie finden in diesem Artikel die Schritte zur späteren Aktualisierung der Versandinformationen.

6. Senden Sie die Datenträger an die im Tool angegebene Adresse, und bewahren Sie die Nachverfolgungsnummer zur späteren Nutzung auf.

   > [!IMPORTANT]
   > Zwei Azure-Importaufträge dürfen nicht dieselbe Nachverfolgungsnummer aufweisen. Stellen Sie sicher, dass die vom Hilfsprogramm unter einem gemeinsamen Azure-Importauftrag vorbereiteten Laufwerke zusammen in einem einzelnen Paket versendet werden und dass das Paket eine einzelne eindeutige Nachverfolgungsnummer besitzt. Fassen Sie Laufwerke, die im Rahmen **unterschiedlicher** Azure Importaufträge vorbereitet wurden, nicht in einem Paket zusammen.

5. Wenn Sie die Verfolgungsinformationen kennen, wechseln Sie zum Quellcomputer, der auf den Abschluss des Importauftrags wartet, und führen Sie den folgenden Befehl in einem Eingabeaufforderungsfenster mit erhöhten Rechten und dem Verzeichnis des Hilfsprogramms *AzureOfflineBackupDiskPrep* als aktuelles Verzeichnis aus:

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Sie können alternativ auch den folgenden Befehl auf einem anderen Computer als dem *Kopiercomputer* mit dem Verzeichnis des Hilfsprogramms *AzureOfflineBackupDiskPrep* als aktuelles Verzeichnis ausführen:

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parameter | BESCHREIBUNG |
    | --- | --- |
    | u: | Erforderliche Eingabe zum Aktualisieren der Versanddetails für einen Azure-Importauftrag |
    | s:&lt;*Pfad zum Stagingspeicherort*&gt; | Erforderliche Eingabe, wenn der Befehl nicht auf dem Quellcomputer ausgeführt wird. Dient dem Angeben des Pfads zum Stagingspeicherort, den Sie im Workflow **Initiieren der Offlinesicherung** eingegeben haben. |
    | p:&lt;*Pfad zu PublishSettingsFile*&gt; | Erforderliche Eingabe, wenn der Befehl nicht auf dem Quellcomputer ausgeführt wird. Dient dem Angeben des Pfads zur Datei mit den **Azure-Veröffentlichungseinstellungen**, den Sie im Workflow **Initiieren der Offlinesicherung** eingegeben haben. |

    Das Hilfsprogramm erkennt automatisch den Importauftrag, auf den der Quellcomputer wartet, oder die Importaufträge mit dem Stagingspeicherort, sofern der Befehl auf einem anderen Computer ausgeführt wird. Es zeigt dann die Option zum Aktualisieren der Versandinformationen über eine Reihe von Eingaben an, wie unten dargestellt:

    ![Versandinformationen eingeben](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

6. Nachdem alle Eingaben bereitgestellt wurden, überprüfen Sie die Details sorgfältig, und committen Sie die bereitgestellten Versandinformationen durch Eingabe von *yes*.

    ![Versandinformationen überprüfen](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

7. Wenn die Versandinformationen erfolgreich aktualisiert wurden, stellt das Hilfsprogramm einen lokalen Speicherort bereit, an dem die von Ihnen eingegebenen Versandinformationen wie unten dargestellt gespeichert werden.

    ![Versandinformationen speichern](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT]
   > Stellen Sie sicher, dass die Laufwerke das Azure-Rechenzentrum innerhalb von zwei Wochen nach der Bereitstellung der Versandinformationen mithilfe des Hilfsprogramms *AzureOfflineBackupDiskPrep* erreichen. Andernfalls werden die Laufwerke nicht bearbeitet.  

Nachdem Sie die oben beschriebenen Schritte abgeschlossen haben, kann das Azure-Rechenzentrum die Laufwerke annehmen und weiter verarbeiten, um die zu sichernden Daten von den Laufwerken in das klassische Azure Storage-Konto zu übertragen, das Sie erstellt haben.

### <a name="time-to-process-the-drives"></a>Verarbeitungsdauer der Laufwerke
Die Verarbeitungsdauer eines Azure-Importauftrags kann aufgrund von verschiedenen Faktoren variieren, z.B. Versanddauer, Auftragstyp, Typ und Größe der zu kopierenden Daten und Größe der geschickten Festplatten. Der Azure Import/Export-Dienst verfügt nicht über eine SLA. Nach Erhalt der Datenträger versucht der Dienst jedoch, das Kopieren der Sicherungsdaten in Ihr Azure Storage-Konto innerhalb von sieben bis zehn Tagen fertigzustellen. Im nächsten Abschnitt wird erläutert, wie Sie den Status des Azure-Importauftrags überwachen können.

### <a name="monitoring-azure-import-job-status"></a>Überwachen des Status von Azure-Importaufträgen
Während Ihre Laufwerke gesendet werden oder sich im Azure-Rechenzentrum befinden, um in das Speicherkonto kopiert zu werden, zeigt der Azure Backup-Agent oder SC DPM oder die Azure Backup Server-Konsole auf dem Quellcomputer den folgenden Auftragsstatus für geplante Sicherungen an.

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Führen Sie die Schritte unten aus, um den Status des Importauftrags zu überprüfen.
1. Öffnen Sie auf dem Quellcomputer eine Eingabeaufforderung mit erhöhten Rechten, und führen Sie den folgenden Befehl aus:

     `AzureOfflineBackupDiskPrep.exe u:`

2.  Die Ausgabe zeigt den aktuellen Status des Importauftrags an, wie unten dargestellt:

    ![Status des Importauftrags überprüfen](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Weitere Informationen zu den verschiedenen Status von Azure-Importaufträgen finden Sie in [diesem Artikel](../storage/common/storage-import-export-view-drive-status.md).

### <a name="complete-the-workflow"></a>Ausführen des Workflows
Nach Abschluss des Importauftrags sind die Daten der Erstsicherung in Ihrem Speicherkonto verfügbar. Zum Zeitpunkt der nächsten geplanten Sicherung kopiert Azure Backup den Inhalt der Daten aus dem Speicherkonto in den Recovery Services-Tresor, wie im Folgenden dargestellt:

   ![Daten in den Recovery Services-Tresor kopieren](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Zum nächsten geplanten Sicherungszeitpunkt führt Azure Backup eine inkrementelle Sicherung für die erste Sicherungskopie durch.

## <a name="next-steps"></a>Nächste Schritte
* Falls Sie Fragen zum Azure Import/Export-Workflow haben, finden Sie unter [Verwenden des Microsoft Azure Import/Export-Diensts zum Übertragen von Daten in den Blobspeicher](../storage/common/storage-import-export-service.md)weitere Informationen.
* Bei Fragen zum Workflow finden Sie im Abschnitt zur Offlinesicherung in den [häufig gestellten Fragen](backup-azure-backup-faq.md) zu Azure Backup weitere Informationen.

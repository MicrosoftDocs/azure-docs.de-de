---
title: Azure Backup – Offlinesicherung oder anfängliches Seeding mithilfe des Azure Import/Export-Diensts
description: Erfahren Sie, wie Sie mit Azure Backup mithilfe des Azure Import/Export-Diensts Daten aus dem Netzwerk senden können. Dieser Artikel erläutert das Offlineseeding der ersten Sicherungsdaten mit dem Azure Import/Export-Dienst.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: saurse
ms.openlocfilehash: b6f0ce1939b2a78ca191d2feb0140506d130b9b0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60648349"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Workflow zur Offlinesicherung in Azure Backup
Azure Backup verfügt über mehrere integrierte effizienzsteigernde Funktionen, die die Netzwerk- und Speicherkosten bei den ersten vollständigen Datensicherungen in Azure reduzieren. Bei den ersten vollständigen Sicherungen werden meist große Datenmengen übertragen, sodass eine höhere Netzwerkbandbreite als bei den nachfolgenden Sicherungen erforderlich ist, bei denen nur die Deltamengen bzw. Inkremente übertragen werden. Durch den Prozess des Offlineseedings kann Azure Backup Datenträger verwenden, um die Daten der Offlinesicherung in Azure hochzuladen.

Der Offlineseeding-Prozess von Azure Backup ist eng in den [Import/Export-Dienst von Azure](../storage/common/storage-import-export-service.md) integriert, mit dem die Daten der Erstsicherung unter Verwendung von Datenträgern an Azure übertragen werden können. Wenn Sie bei der Erstsicherung mehrere Terabytes an Daten über ein Netzwerk mit hoher Wartezeit und geringer Bandbreite übertragen müssen, können Sie die erste Sicherungskopie mithilfe des Offlineseeding-Workflows auf einer oder mehreren Festplatten speichern und an ein Azure-Datencenter senden. Die folgende Abbildung bietet eine Übersicht über die Workflowschritte:

  ![Übersicht über den Offlineimport-Workflowprozess](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Der Offlinesicherungsprozess umfasst folgende Schritte:

1. Senden Sie die Sicherungsdaten nicht über das Netzwerk, sondern schreiben Sie sie an einen Stagingspeicherort.
2. Schreiben Sie die Daten am Stagingspeicherort mithilfe des Hilfsprogramms „AzureOfflineBackupDiskPrep“ auf einen oder mehrere SATA-Datenträger.
3. Das Hilfsprogramm „AzureOfflineBackupDiskPrep“ erstellt zur Vorbereitung einen Azure-Importauftrag. Senden Sie die SATA-Laufwerke an das nächstgelegene Azure-Datencenter, und geben Sie den Importauftrag an, um die Aktivitäten zu verknüpfen.
4. Im Azure-Datencenter werden die Daten auf den Datenträgern in ein Azure-Speicherkonto kopiert.
5. Azure Backup kopiert die Sicherungsdaten aus dem Speicherkonto in den Recovery Services-Tresor, und inkrementelle Sicherungen werden geplant.

## <a name="supported-configurations"></a>Unterstützte Konfigurationen 
Die Verwendung der Offlinesicherung wird von folgenden Azure Backup-Features oder -Workloads unterstützt.

> [!div class="checklist"]
> * Sichern von Dateien und Ordnern mit dem Microsoft Azure Recovery Services-Agent (MARS; auch Azure Backup-Agent genannt) 
> * Sichern aller Workloads und Dateien mit dem System Center Data Protection Manager (DPM SC) 
> * Sichern aller Workloads und Dateien mit dem Microsoft Azure Backup Server <br/>

   > [!NOTE]
   > Die Offlinesicherung wird für Sicherungen des Systemstatus mithilfe des Azure Backup-Agents nicht unterstützt. 

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Voraussetzungen

  > [!NOTE]
  > Die folgenden Voraussetzungen und der folgende Workflow gelten nur für Offlinesicherungen von Dateien und Ordnern mit dem [neuesten MARS-Agent](https://aka.ms/azurebackup_agent). Informationen zum Durchführen von Offlinesicherungen für Workloads mit System Center DPM oder Azure Backup Server finden Sie in [diesem Artikel](backup-azure-backup-server-import-export-.md). 

Führen Sie vor der Initiierung des Workflows zur Offlinesicherung die folgenden Schritte aus: 
* Erstellen Sie einen [Recovery Services-Tresor](backup-azure-recovery-services-vault-overview.md). Informationen zur Tresorerstellung finden Sie in den Schritten in [diesem Artikel](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault).
* Vergewissern Sie sich, dass auf dem Windows-Server bzw. auf dem Windows-Client die [neueste Version des Azure Backup-Agents](https://aka.ms/azurebackup_agent) installiert und der Computer beim Recovery Services-Tresor registriert ist.
* Der Computer, auf dem der Azure Backup-Agent ausgeführt wird, muss über Azure PowerShell 3.7.0 verfügen. Es wird empfohlen, die Sie [die Version 3.7.0 von Azure PowerShell herunterladen und installieren](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* Auf dem Computer mit dem Azure Backup-Agent müssen Microsoft Edge oder Internet Explorer 11 installiert und JavaScript aktiviert sein. 
* Erstellen Sie ein Azure Storage-Konto in dem Abonnement, in dem sich auch der Recovery Services-Tresor befindet. 
* Vergewissern Sie sich, dass Sie über die [erforderlichen Berechtigungen](../active-directory/develop/howto-create-service-principal-portal.md) für die Erstellung der Azure Active Directory-Anwendung verfügen. Der Workflow zur Offlinesicherung erstellt eine Azure Active Directory-Anwendung in dem Abonnement, das dem Azure Storage-Konto zugeordnet ist. Die Anwendung dient dazu, Azure Backup sicheren und bereichsbezogenen Zugriff auf den Azure-Importdienst zu ermöglichen, der für den Workflow zur Offlinesicherung erforderlich ist. 
* Registrieren Sie den Ressourcenanbieter „Microsoft.ImportExport“ bei dem Abonnement mit dem Azure Storage-Konto. So registrieren Sie den Ressourcenanbieter:
    1. Klicken Sie im Hauptmenü auf **Abonnements**.
    2. Wählen Sie bei Verwendung mehrerer Abonnements das Abonnement aus, das Sie für die Offlinesicherung verwenden. Falls Sie nur ein einzelnes Abonnement verwenden, wird Ihr Abonnement angezeigt.
    3. Klicken Sie im Abonnementmenü auf **Ressourcenanbieter**, um die Anbieterliste anzuzeigen.
    4. Scrollen Sie in der Anbieterliste nach unten zu „Microsoft.ImportExport“. Falls der Status „NotRegistered“ lautet, klicken Sie auf **Registrieren**.
    ![Registrieren des Ressourcenanbieters](./media/backup-azure-backup-import-export/registerimportexport.png)
* Es wurde ein Stagingspeicherort, bei dem es sich um eine Netzwerkfreigabe oder ein zusätzliches Laufwerk auf dem Computer (intern oder extern) handeln kann, mit genügend Speicherplatz zum Speichern der Erstkopie erstellt. Wenn Sie beispielsweise einen 500-GB-Dateiserver sichern möchten, muss der Stagingbereich mindestens 500 GB groß sein. (Aufgrund der Komprimierung wird weniger Speicherplatz genutzt.)
* Senden Sie nur interne Festplatten vom Typ SSD (2,5 Zoll) oder SATA II/III (2,5 Zoll oder 3,5 Zoll) an Azure. Sie können Festplatten mit bis zu 10 TB verwenden. Schlagen Sie in der [Dokumentation zum Azure Import/Export-Dienst](../storage/common/storage-import-export-requirements.md#supported-hardware) die aktuell vom Dienst unterstützten Laufwerke nach.
* Die SATA-Laufwerke müssen mit einem Computer verbunden sein (als *Kopiercomputer* bezeichnet), auf dem die Sicherungsdaten vom *Stagingspeicherort* auf die SATA Laufwerke kopiert werden. Stellen Sie sicher, dass BitLocker auf dem *Kopiercomputer* aktiviert ist.

## <a name="workflow"></a>Workflow
In diesem Abschnitt wird erläutert, wie Sie den Workflow zur Offlinesicherung durchführen, damit Ihre Daten an ein Azure-Datencenter gesendet und in Azure Storage hochgeladen werden können. Weitere Informationen zum Import-Dienst sowie zu anderen Aspekten des Prozesses finden Sie in der [Übersicht über den Import-Dienst](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Initiieren der Offlinesicherung
1. Wenn Sie eine Sicherung mit dem MARS-Agent planen, wird der folgende Bildschirm angezeigt:

    ![Importbildschirm](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

   Die Beschreibung der Eingaben lautet wie folgt:

    * **Stagingspeicherort**: Der temporäre Speicherort, an den die erste Sicherungskopie geschrieben wird. Beim Stagingspeicherort kann es sich um eine Netzwerkfreigabe oder einen lokalen Computer handeln. Wenn der Kopiercomputer und der Quellcomputer nicht identisch sind, wird empfohlen, den vollständigen Netzwerkpfad des Stagingspeicherorts anzugeben.
    * **Azure Resource Manager-Speicherkonto**: Der Name des Resource Manager-Speicherkontos in einem beliebigen Azure-Abonnement.
    * **Azure Storage-Container**: Der Name des Zielspeicherblobs im Azure-Speicherkonto, in das die Sicherungsdaten vor dem Kopieren in den Recovery Services-Tresor importiert werden.
    * **Azure-Abonnement-ID**: Die ID für das Azure-Abonnement, in dem das Azure-Speicherkonto erstellt wird.
    * **Name des Azure Importauftrags**: Der eindeutige Name, anhand dessen der Azure Import-Dienst und Azure Backup Datenübertragungen nachverfolgen, die auf Datenträgern an Azure gesendet werden. 
  
   Geben Sie die erforderlichen Werte in den Bildschirm ein, und klicken Sie anschließend auf **Weiter**. Speichern Sie die Angaben für *Stagingspeicherort* und *Name des Azure-Importauftrags*. Diese werden zur Vorbereitung der Datenträger benötigt.

2. Melden Sie sich bei Ihrem Azure-Abonnement an, wenn Sie dazu aufgefordert werden. Die Anmeldung ist erforderlich, damit Azure Backup die Azure Active Directory-Anwendung erstellen und die erforderlichen Berechtigungen für den Zugriff auf den Azure-Import-Dienst bereitstellen kann.

    ![Jetzt sichern](./media/backup-azure-backup-import-export/azurelogin.png)

3. Schließen Sie den Workflow ab, und klicken Sie in der Konsole des Azure Backup-Agents auf **Jetzt sichern**.

    ![Jetzt sichern](./media/backup-azure-backup-import-export/backupnow.png)

4. Klicken Sie auf der Bestätigungsseite des Assistenten auf **Sichern**. Im Rahmen der Einrichtung wird die Erstsicherung in den Stagingbereich geschrieben.

   ![Bestätigen der Sicherungsbereitschaft](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Nach Abschluss des Vorgangs kann der Stagingspeicherort für die Datenträgervorbereitung verwendet werden.

   ![Jetzt sichern](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Vorbereiten der SATA-Laufwerke und Senden an Azure
Das Hilfsprogramm *AzureOfflineBackupDiskPrep* bereitet die SATA-Laufwerke vor, die an das nächstgelegene Azure-Datencenter gesendet werden. Dieses Hilfsprogramm steht im Installationsverzeichnis des Azure Backup-Agents unter folgendem Pfad zur Verfügung:

   *\Microsoft Azure Recovery Services Agent\Utils\\*

1. Navigieren Sie zu diesem Verzeichnis, und kopieren Sie das Verzeichnis **AzureOfflineBackupDiskPrep** auf einen anderen Computer, mit dem die SATA-Laufwerke verbunden sind. Vergewissern Sie sich, dass auf dem Computer mit den verbundenen SATA-Laufwerken Folgendes erfüllt ist:

   * Der Kopiercomputer kann über denselben Netzwerkpfad, der während des Workflows **Initiieren der Offlinesicherung** angegeben wurde, auf den Stagingspeicherort für den Offlineseeding-Workflow zugreifen.
   * BitLocker ist auf dem Kopiercomputer aktiviert.
   * Azure PowerShell 3.7.0 wurde installiert.
   * Die neuesten kompatiblen Browser (Microsoft Edge oder Internet Explorer 11) sind installiert, und JavaScript ist aktiviert. 
   * Der Kopiercomputer kann auf das Azure-Portal zugreifen. Sofern erforderlich, kann der Kopiercomputer mit dem Quellcomputer identisch sein.
    
     > [!IMPORTANT] 
     > Wenn der Quellcomputer ein virtueller Computer ist, muss der Kopiercomputer ein anderer physischer Server- oder Clientcomputer sein.

2. Öffnen Sie auf dem Kopiercomputer eine Eingabeaufforderung mit erhöhten Rechten und dem Verzeichnis des Hilfsprogramms *AzureOfflineBackupDiskPrep* als aktuelles Verzeichnis. Führen Sie dann den folgenden Befehl aus:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parameter | BESCHREIBUNG |
    | --- | --- |
    | s:&lt;*Pfad zum Stagingspeicherort*&gt; |Obligatorische Eingabe zum Angeben des Pfads für den Stagingspeicherort, den Sie im Workflow **Initiieren der Offlinesicherung** eingegeben haben. |
    | p:&lt;*Pfad zu PublishSettingsFile*&gt; |Optionale Eingabe zum Angeben des Pfads zur Datei **Azure-Veröffentlichungseinstellungen**, den Sie im Workflow **Initiieren der Offlinesicherung** eingegeben haben. |

    Beim Ausführen des Befehls fordert das Hilfsprogramm die Auswahl des Azure-Importauftrags an, der den vorzubereitenden Datenträgern entspricht. Falls dem angegebenen Stagingspeicherort nur ein einzelner Importauftrag zugeordnet ist, wird ein Bildschirm wie in der folgenden Abbildung angezeigt.

    ![Azure-Tool für die Datenträgervorbereitung – Eingabe](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

3. Geben Sie den Laufwerkbuchstaben ohne nachgestellten Doppelpunkt für den bereitgestellten Datenträger an, den Sie für die Übertragung an Azure vorbereiten möchten. 
4. Bestätigen Sie die Formatierung des Laufwerks, wenn Sie dazu aufgefordert werden.
5. Sie werden aufgefordert, sich bei Ihrem Azure-Abonnement anzumelden. Geben Sie Ihre Anmeldeinformationen ein.

    ![Azure-Tool für die Datenträgervorbereitung – Eingabe](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Das Tool beginnt dann, den Datenträger vorzubereiten und die Sicherungsdaten zu kopieren. Falls auf dem bereitgestellten Datenträger nicht genügend Speicherplatz für die Sicherungsdaten verfügbar ist, müssen Sie unter Umständen weitere Datenträger anfügen, wenn Sie vom Tool dazu aufgefordert werden. <br/>

    Nach erfolgreicher Ausführung des Tools gibt die Befehlszeile drei Informationen zurück:
   1. Die von Ihnen bereitgestellten Datenträger werden für den Versand an Azure vorbereitet. 
   2. Sie erhalten eine Erstellungsbestätigung für Ihren Importauftrag. Der Importauftrag hat den von Ihnen angegebenen Namen.
   3. Das Tool zeigt die Lieferadresse für das Azure-Datencenter an.

      ![Azure-Tool für die Datenträgervorbereitung – Abgeschlossen](./media/backup-azure-backup-import-export/console2.png)<br/>

6. Nach Abschluss der Befehlsausführung können Sie die Versandinformationen aktualisieren.

7. Senden Sie die Datenträger an die im Tool angegebene Adresse, und bewahren Sie die Nachverfolgungsnummer zur späteren Nutzung auf.

   > [!IMPORTANT] 
   > Zwei Azure-Importaufträge dürfen nicht dieselbe Nachverfolgungsnummer aufweisen. Stellen Sie sicher, dass die vom Hilfsprogramm unter einem gemeinsamen Azure-Importauftrag vorbereiteten Laufwerke zusammen in einem einzelnen Paket versendet werden und dass das Paket eine einzelne eindeutige Nachverfolgungsnummer besitzt. Fassen Sie Laufwerke, die im Rahmen separater Azure Importaufträge vorbereitet wurden, nicht in einem Paket zusammen.



## <a name="update-shipping-details-on-the-azure-import-job"></a>Aktualisieren der Versanddetails für den Azure-Importauftrag

Das folgende Verfahren dient zum Aktualisieren der Versanddetails für den Azure-Importauftrag. Dazu zählen folgende Informationen:
* Der Name des Transportunternehmens, das die Datenträger an Azure liefert.
* Die Details für den Rückversand Ihrer Datenträger.

1. Melden Sie sich bei Ihrem Azure-Abonnement an.
2. Klicken Sie im Hauptmenü auf **Alle Dienste**, und geben Sie im Dialogfeld „Alle Dienste“ den Text „Import“ ein. Wenn die Option **Import-/Exportaufträge** erscheint, klicken Sie darauf.
    ![Versandinformationen eingeben](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Das Menü **Import-/Exportaufträge** wird geöffnet, und eine Liste mit allen Import-/Exportaufträgen im ausgewählten Abonnement wird angezeigt. 

3. Wenn Sie über mehrere Abonnements verfügen, wählen Sie das Abonnement aus, das zum Importieren der Sicherungsdaten verwendet wird. Wählen Sie anschließend den neu erstellten Importauftrag aus, um dessen Details anzuzeigen.

    ![Versandinformationen überprüfen](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

4. Klicken Sie im Einstellungsmenü für den Importauftrag auf **Versandinformationen verwalten**, und geben Sie die Details für den Rückversand an.

    ![Versandinformationen speichern](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

5. Wenn Sie die Nachverfolgungsnummer von Ihrem Transportunternehmen erhalten haben, klicken Sie auf der Übersichtsseite des Azure-Importauftrags auf das Banner, und geben Sie folgende Details ein:

   > [!IMPORTANT] 
   > Die Angaben zum Transportunternehmen und die Nachverfolgungsnummer müssen innerhalb von zwei Wochen nach Erstellung des Azure-Importauftrags aktualisiert werden. Andernfalls wird der Auftrag unter Umständen gelöscht, und die Datenträger werden nicht verarbeitet.

   ![Versandinformationen speichern](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Versandinformationen speichern](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Verarbeitungsdauer der Laufwerke 
Die Verarbeitungsdauer eines Azure-Importauftrags kann aufgrund von verschiedenen Faktoren variieren, z.B. Versanddauer, Auftragstyp, Typ und Größe der zu kopierenden Daten und Größe der geschickten Festplatten. Der Azure Import/Export-Dienst verfügt nicht über eine SLA. Nach Erhalt der Datenträger versucht der Dienst jedoch, das Kopieren der Sicherungsdaten in Ihr Azure Storage-Konto innerhalb von sieben bis zehn Tagen fertigzustellen. 

### <a name="monitoring-azure-import-job-status"></a>Überwachen des Status von Azure-Importaufträgen
Sie können den Status des Importauftrags über das Azure-Portal überwachen. Navigieren Sie hierzu zur Seite **Import-/Exportaufträge**, und wählen Sie Ihren Auftrag aus. Weitere Informationen zum Status von Importaufträgen finden Sie im Artikel [Verwenden des Microsoft Azure Import/Export-Diensts zum Übertragen von Daten in Azure Store](../storage/common/storage-import-export-service.md).

### <a name="complete-the-workflow"></a>Ausführen des Workflows
Nach erfolgreichem Abschluss des Importauftrags sind die Daten der Erstsicherung in Ihrem Speicherkonto verfügbar. Bei der nächsten geplanten Sicherung kopiert Azure Backup den Inhalt der Daten aus dem Speicherkonto in den Recovery Services-Tresor, wie hier zu sehen: 

   ![Daten in den Recovery Services-Tresor kopieren](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Bei der nächsten geplanten Sicherung führt Azure Backup eine inkrementelle Sicherung durch.

### <a name="cleaning-up-resources"></a>Bereinigen der Ressourcen
Nach Abschluss der Erstsicherung können die in den Azure-Speichercontainer importierten Daten und die Sicherungsdaten am Stagingspeicherort gefahrlos gelöscht werden.

## <a name="next-steps"></a>Nächste Schritte
* Falls Sie Fragen zum Azure Import/Export-Workflow haben, finden Sie unter [Verwenden des Microsoft Azure Import/Export-Diensts zum Übertragen von Daten in den Blobspeicher](../storage/common/storage-import-export-service.md)weitere Informationen.
* Bei Fragen zum Workflow finden Sie im Abschnitt zur Offlinesicherung in den [häufig gestellten Fragen](backup-azure-backup-faq.md) zu Azure Backup weitere Informationen.

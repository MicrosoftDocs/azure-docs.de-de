---
title: Transformieren von Daten mithilfe von Hive in Azure Virtual Network unter Verwendung des Azure-Portals
description: Dieses Tutorial bietet Schrittanleitungen zum Transformieren von Daten mithilfe einer Hive-Aktivität in Azure Data Factory.
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.topic: tutorial
ms.custom: seo-dt-2019
ms.date: 01/04/2018
ms.openlocfilehash: f6e1f80ebba0aa67cf2bab4a772ebc1765cd8298
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92634367"
---
# <a name="transform-data-in-azure-virtual-network-using-hive-activity-in-azure-data-factory-using-the-azure-portal"></a>Transformieren von Daten in Azure Virtual Network mithilfe einer Hive-Aktivität in Azure Data Factory mithilfe des Azure-Portals

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Tutorial verwenden Sie das Azure-Portal, um eine Data Factory-Pipeline zu erstellen, die Daten mithilfe einer Hive-Aktivität in einem HDInsight-Cluster transformiert, der sich in einem virtuellen Azure-Netzwerk (VNet) befindet. In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Erstellen einer Data Factory. 
> * Erstellen einer selbstgehosteten Integration Runtime
> * Erstellen verknüpfter Azure Storage- und HDInsight-Dienste
> * Erstellen einer Pipeline mit Hive-Aktivität
> * Auslösen einer Pipelineausführung
> * Überwachen der Pipelineausführung 
> * Überprüfen der Ausgabe

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure Storage-Konto** . Erstellen Sie ein Hive-Skript, und laden Sie es in Azure Storage hoch. Die Ausgabe des Hive-Skripts wird in diesem Storage-Konto gespeichert. In diesem Beispiel verwendet der HDInsight-Cluster dieses Azure Storage-Konto als primären Speicher. 
- **Azure Virtual Network.** Wenn Sie noch nicht über ein Azure Virtual Network verfügen, erstellen Sie ein solches anhand [dieser Anweisungen](../virtual-network/quick-create-portal.md). In diesem Beispiel befindet sich HDInsight in einem Azure Virtual Network. Hier finden Sie die Beispielkonfiguration von Azure Virtual Network. 

    ![Virtuelles Netzwerk erstellen](media/tutorial-transform-data-using-hive-in-vnet-portal/create-virtual-network.png)
- **HDInsight-Cluster.** Erstellen Sie einen HDInsight-Cluster, und binden Sie ihn in das virtuelle Netzwerk ein, das Sie im vorherigen Schritt erstellt haben. Befolgen Sie dazu die Anweisungen in folgendem Artikel: [Erweitern von Azure HDInsight per Azure Virtual Network](../hdinsight/hdinsight-plan-virtual-network-deployment.md). Hier finden Sie die Beispielkonfiguration von HDInsight in einem virtuellen Netzwerk. 

    ![HDInsight in einem virtuellen Netzwerk](media/tutorial-transform-data-using-hive-in-vnet-portal/hdinsight-virtual-network-settings.png)
- **Azure PowerShell** . Befolgen Sie die Anweisungen unter [Get started with Azure PowerShell cmdlets](/powershell/azure/install-Az-ps) (Erste Schritte mit Azure PowerShell-Cmdlets).
- **Ein virtueller Computer.** Erstellen Sie einen virtuellen Azure-Computer, und binden Sie ihn in das virtuelle Netzwerk ein, das Ihren HDInsight-Cluster enthält. Ausführliche Informationen finden Sie unter [Erstellen virtueller Computer](../virtual-network/quick-create-portal.md#create-virtual-machines). 

### <a name="upload-hive-script-to-your-blob-storage-account"></a>Hochladen eines Hive-Skripts in Ihr Blob Storage-Konto

1. Erstellen Sie eine Hive SQL-Datei mit dem Namen **hivescript.hql** und dem folgenden Inhalt:

   ```sql
   DROP TABLE IF EXISTS HiveSampleOut; 
   CREATE EXTERNAL TABLE HiveSampleOut (clientid string, market string, devicemodel string, state string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
   STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

   INSERT OVERWRITE TABLE HiveSampleOut
   Select 
       clientid,
       market,
       devicemodel,
       state
   FROM hivesampletable
   ```
2. Erstellen Sie in Azure Blob Storage einen Container mit dem Namen **adftutorial** , falls dieser noch nicht vorhanden ist.
3. Erstellen Sie einen Ordner mit dem Namen **hivescripts** .
4. Laden Sie die Datei **hivescript.hql** in den Unterordner **hivescripts** hoch.

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Starten Sie den Webbrowser **Microsoft Edge** oder **Google Chrome** . Die Data Factory-Benutzeroberfläche wird zurzeit nur in den Webbrowsern Microsoft Edge und Google Chrome unterstützt.
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.    
2. Klicken Sie im Menü auf der linken Seite nacheinander auf **Neu** , **Data + Analytics** und **Data Factory** . 
   
   ![Neu -> Data Factory](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-data-factory-menu.png)
3. Geben Sie auf der Seite **Neue Data Factory** unter **Name** die Zeichenfolge **ADFTutorialHiveFactory** ein. 
      
     ![Seite „Neue Data Factory“](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-azure-data-factory.png)
 
   Der Name der Azure Data Factory muss **global eindeutig** sein. Sollte der folgende Fehler auftreten, ändern Sie den Namen der Data Factory (beispielsweise in „<IhrName>MyAzureSsisDataFactory“), und wiederholen Sie den Vorgang. Benennungsregeln für Data Factory-Artefakte finden Sie im Artikel [Azure Data Factory – Benennungsregeln](naming-rules.md).
  
    *Der Data Factory-Name „MyAzureSsisDataFactory“ ist nicht verfügbar.*
3. Wählen Sie Ihr **Azure-Abonnement** aus, in dem die Data Factory erstellt werden soll. 
4. Führen Sie für die **Ressourcengruppe** einen der folgenden Schritte aus:
     
   - Wählen Sie die Option **Use existing** (Vorhandene verwenden) und dann in der Dropdownliste eine vorhandene Ressourcengruppe. 
   - Wählen Sie **Neu erstellen** , und geben Sie den Namen einer Ressourcengruppe ein.   
         
     Weitere Informationen über Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/overview.md).  
4. Wählen Sie **V2** als **Version** aus.
5. Wählen Sie den **Standort** für die Data Factory aus. Die Liste enthält nur Standorte, die für die Erstellung von Data Factorys geeignet sind.
6. Wählen Sie die Option **An Dashboard anheften** aus.     
7. Klicken Sie auf **Erstellen** .
8. Auf dem Dashboard sehen Sie die folgende Kachel mit dem Status: **Deploying data factory** (Data Factory wird bereitgestellt...). 

     ![Kachel „Die Data Factory wird bereitgestellt“](media/tutorial-transform-data-using-hive-in-vnet-portal/deploying-data-factory.png)
9. Nach Abschluss der Erstellung wird die Seite **Data Factory** wie in der Abbildung angezeigt.
   
    ![Data Factory-Startseite](./media/tutorial-transform-data-using-hive-in-vnet-portal/data-factory-home-page.png)
10. Klicken Sie auf **Erstellen und überwachen** , um die Data Factory-Benutzeroberfläche (User Interface, UI) auf einer separaten Registerkarte zu starten.
11. Wechseln Sie im linken Bereich der Seite **Erste Schritte** zur Registerkarte **Bearbeiten** , wie in der folgenden Abbildung gezeigt: 

    ![Registerkarte „Bearbeiten“](./media/tutorial-transform-data-using-hive-in-vnet-portal/get-started-page.png)

## <a name="create-a-self-hosted-integration-runtime"></a>Erstellen einer selbstgehosteten Integration Runtime
Da sich der Hadoop-Cluster in einem virtuellen Netzwerk befindet, müssen Sie in diesem virtuellen Netzwerk eine selbstgehostete Integration Runtime (IR) installieren. In diesem Abschnitt erstellen Sie einen neuen virtuellen Computer, binden ihn in das gleiche virtuelle Netzwerk ein und installieren eine selbstgehostete IR auf dem Computer. Mit der selbstgehosteten IR kann der Data Factory-Dienst innerhalb eines virtuellen Netzwerks Verarbeitungsanforderungen an einen Computedienst wie HDInsight verteilen. Außerdem können Sie Daten aus Datenspeichern in einem virtuellen Netzwerk nach Azure verschieben (und umgekehrt). Eine selbstgehostete IR wird verwendet, wenn sich der Datenspeicher oder die Computeressource ebenfalls in einer lokalen Umgebung befindet. 

1. Klicken Sie auf der Azure Data Factory-Benutzeroberfläche im unteren Bereich des Fensters auf **Verbindungen** , wechseln Sie zur Registerkarte **Integration Runtimes** , und klicken Sie anschließend auf der Symbolleiste auf **+ Neu** . 

   ![Menü für neue Integration Runtime](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-integration-runtime-menu.png)
2. Wählen Sie im Fenster **Integration Runtime Setup** (Integration Runtime-Setup) die Option **Perform data movement and dispatch activities to external computes** (Datenverschiebung und -verteilung an externe Computeressourcen ausführen), und klicken Sie auf **Weiter** . 

   ![Auswählen der Option für Datenverschiebung und -verteilung](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-perform-data-movement-compute-option.png)
3. Wählen Sie **Privates Netzwerk** , und klicken Sie auf **Weiter** .
    
   ![Auswählen von „Privates Netzwerk“](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-private-network.png)
4. Geben Sie unter **Name** die Zeichenfolge **MySelfHostedIR** ein, und klicken Sie auf **Weiter** . 

   ![Angeben des Namens der Integration Runtime](./media/tutorial-transform-data-using-hive-in-vnet-portal/integration-runtime-name.png) 
5. Klicken Sie auf die Kopierschaltfläche, um den **Authentifizierungsschlüssel** für die Integration Runtime zu kopieren, und speichern Sie ihn. Lassen Sie das Fenster geöffnet. Mit diesem Schlüssel wird die auf einem virtuellen Computer installierte IR registriert. 

   ![Kopieren des Authentifizierungsschlüssels](./media/tutorial-transform-data-using-hive-in-vnet-portal/copy-key.png)

### <a name="install-ir-on-a-virtual-machine"></a>Installieren der IR auf einem virtuellen Computer

1. Laden Sie die [selbstgehostete Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) auf den virtuellen Azure-Computer herunter. Verwenden Sie den **Authentifizierungsschlüssel** , den Sie im vorherigen Schritt abgerufen haben, um die selbstgehostete Integration Runtime manuell zu registrieren. 

    ![Registrieren der Integration Runtime](media/tutorial-transform-data-using-hive-in-vnet-portal/register-integration-runtime.png)

2. Wenn die selbstgehostete Integration Runtime erfolgreich registriert wurde, wird die folgende Meldung angezeigt: 
   
    ![Erfolgreich registriert](media/tutorial-transform-data-using-hive-in-vnet-portal/registered-successfully.png)
3. Klicken Sie auf **Konfigurations-Manager starten** . Folgende Seite wird angezeigt, wenn der Knoten mit dem Clouddienst verbunden ist: 
   
    ![Knoten ist verbunden](media/tutorial-transform-data-using-hive-in-vnet-portal/node-is-connected.png)

### <a name="self-hosted-ir-in-the-azure-data-factory-ui"></a>Selbstgehostete IR auf der Azure Data Factory-Benutzeroberfläche

1. Auf der **Azure Data Factory-Benutzeroberfläche** sollten der Name des selbstgehosteten virtuellen Computers sowie dessen Status angezeigt werden.

   ![Vorhandene selbstgehostete Knoten](./media/tutorial-transform-data-using-hive-in-vnet-portal/existing-self-hosted-nodes.png)
2. Klicken Sie auf **Fertig stellen** , um das Fenster **Integration Runtime Setup** (Integration Runtime-Setup) zu schließen. Die selbstgehostete IR wird in der Liste mit den Integration Runtimes angezeigt.

   ![Selbstgehostete IR in der Liste](./media/tutorial-transform-data-using-hive-in-vnet-portal/self-hosted-ir-in-list.png)


## <a name="create-linked-services"></a>Erstellen von verknüpften Diensten

In diesem Abschnitt erstellen Sie zwei verknüpfte Dienste und stellen sie bereit:
- Einen **verknüpften Azure Storage-Dienst** , der ein Azure Storage-Konto mit der Data Factory verknüpft. Dies ist der primäre Speicher, der von Ihrem HDInsight-Cluster verwendet wird. In diesem Fall wird das Azure Storage-Konto zum Speichern des Hive-Skripts und der Skriptausgabe verwendet.
- Einen **verknüpften HDInsight-Dienst** . Azure Data Factory übermittelt das Hive-Skript zur Ausführung an diesen HDInsight-Cluster.

### <a name="create-azure-storage-linked-service"></a>Erstellen des mit Azure Storage verknüpften Diensts

1. Wechseln Sie zur Registerkarte **Verknüpfte Dienste** , und klicken Sie auf **Neu** .

   ![Schaltfläche für neuen verknüpften Dienst](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png)    
2. Wählen Sie im Fenster **New Linked Service** (Neuer verknüpfter Dienst) die Option **Azure Blob Storage** , und klicken Sie dann auf **Weiter** . 

   ![Auswählen von „Azure Blob Storage“](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-azure-storage.png)
3. Führen Sie im Fenster **New Linked Service** (Neuer verknüpfter Dienst) die folgenden Schritte aus:

    1. Geben Sie unter **Name** die Zeichenfolge **AzureStorageLinkedService** ein.
    2. Wählen Sie unter **Connect via integration runtime** (Verbindung per Integration Runtime herstellen) die Option **MySelfHostedIR** aus.
    3. Wählen Sie unter **Speicherkontoname** Ihr Azure-Speicherkonto aus. 
    4. Klicken Sie auf **Verbindung testen** , um die Verbindung mit dem Speicherkonto zu testen.
    5. Klicken Sie auf **Speichern** .
   
        ![Angeben des Azure Blob Storage-Kontos](./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-storage-account.png)

### <a name="create-hdinsight-linked-service"></a>Erstellen eines verknüpften HDInsight-Diensts

1. Klicken Sie erneut auf **Neu** , um einen weiteren verknüpften Dienst zu erstellen. 
    
   ![Schaltfläche für neuen verknüpften Dienst](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png)    
2. Wechseln Sie zur Registerkarte **Compute** , wählen Sie **Azure HDInsight** aus, und klicken Sie anschließend auf **Weiter** .

    ![Auswählen von „Azure HDInsight“](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight.png)
3. Führen Sie im Fenster **New Linked Service** (Neuer verknüpfter Dienst) die folgenden Schritte aus:

    1. Geben Sie unter **Name** die Zeichenfolge **AzureHDInsightLinkedService** ein.
    2. Wählen Sie **Bring your own HDInsight** (Eigenes HDInsight verwenden) aus. 
    3. Wählen Sie unter **Hdi cluster** (HDI-Cluster) Ihren HDInsight-Cluster aus. 
    4. Geben Sie unter **Benutzername** den Benutzernamen für den HDInsight-Cluster ein.
    5. Geben Sie das **Kennwort** für den Benutzer ein. 
    
        ![Azure HDInsight-Einstellungen](./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-hdinsight.png)

In diesem Artikel wird davon ausgegangen, dass Sie über das Internet auf den Cluster zugreifen können. Sie müssen z.B. unter `https://clustername.azurehdinsight.net` eine Verbindung mit dem Cluster herstellen können. Diese Adresse verwendet das öffentliche Gateway, das nicht verfügbar ist, wenn Sie über Netzwerksicherheitsgruppen (Network Security Groups, NSGs) oder benutzerdefinierte Routen (User-Defined Routes, UDRs) den Zugriff aus dem Internet beschränken. Damit Data Factory Aufträge an den HDInsight-Cluster in Ihrem Azure Virtual Network übermitteln kann, müssen Sie das Netzwerk so konfigurieren, dass die URL in die private IP-Adresse des von HDInsight verwendeten Gateways aufgelöst werden kann.

1. Öffnen Sie im Azure-Portal das virtuelle Netzwerk, in dem sich HDInsight befindet. Öffnen Sie die Netzwerkschnittstelle, deren Name mit `nic-gateway-0` beginnt. Notieren Sie die private IP-Adresse. Beispiel: 10.6.0.15. 
2. Wenn in Ihrem Azure Virtual Network DNS-Server vorhanden sind, aktualisieren Sie den DNS-Eintrag, sodass die URL des HDInsight-Clusters, `https://<clustername>.azurehdinsight.net`, in `10.6.0.15` aufgelöst werden kann. Wenn in Ihrem virtuellen Azure-Netzwerk kein DNS-Server vorhanden ist, können Sie dieses Problem vorübergehend umgehen, indem Sie die Hostdatei (C:\Windows\System32\drivers\etc) aller virtuellen Computer bearbeiten, die als Knoten für die selbstgehostete Integration Runtime registriert sind. Fügen Sie hierzu einen Eintrag wie den folgenden hinzu: 

    `10.6.0.15 myHDIClusterName.azurehdinsight.net`

## <a name="create-a-pipeline"></a>Erstellen einer Pipeline 
In diesem Schritt erstellen Sie eine neue Pipeline mit einer Hive-Aktivität. Die Aktivität führt ein Hive-Skript aus, um Daten aus einer Beispieltabelle zurückzugeben und in einem von Ihnen definierten Pfad zu speichern.

Beachten Sie folgende Punkte:

- **scriptPath** zeigt auf den Pfad zu dem Hive-Skript im Azure Storage-Konto, das Sie für „MyStorageLinkedService“ verwendet haben. Der Pfad berücksichtigt die Groß- und Kleinschreibung.
- **Output** ist ein im Hive-Skript verwendetes Argument. Verwenden Sie das Format `wasbs://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/`, um auf einen vorhandenen Ordner in Ihrem Azure Storage zu zeigen. Der Pfad berücksichtigt die Groß- und Kleinschreibung. 

1. Klicken Sie im linken Bereich der Data Factory-Benutzeroberfläche auf **+** (Pluszeichen) und dann auf **Pipeline** . 

    ![Menü für neue Pipeline](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-pipeline-menu.png)
2. Erweitern Sie in der Toolbox **Aktivitäten** die Option **HDInsight** , und ziehen Sie die Aktivität **Hive** auf die Oberfläche des Pipeline-Designers. 

    ![Ziehen und Ablegen der Hive-Aktivität](./media/tutorial-transform-data-using-hive-in-vnet-portal/drag-drop-hive-activity.png)
3. Wechseln Sie im Eigenschaftenfenster zur Registerkarte **HDI-Cluster** , und wählen Sie unter **HDInsight Linked Service** (Verknüpfter HDInsight-Dienst) die Option **AzureHDInsightLinkedService** aus.

    ![Auswählen des verknüpften HDInsight-Diensts](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight-linked-service.png)
4. Wechseln Sie zur Registerkarte **Skripts** , und führen Sie die folgenden Schritte aus: 

    1. Wählen Sie unter **Mit dem Skript verknüpfter Dienst** die Option **AzureStorageLinkedService** aus. 
    2. Klicken Sie unter **Dateipfad** auf **Speicher durchsuchen** . 
 
        ![Durchsuchen des Speichers](./media/tutorial-transform-data-using-hive-in-vnet-portal/browse-storage-hive-script.png)
    3. Navigieren Sie im Fenster **Choose a file or folder** (Datei oder Ordner auswählen) zum Ordner **hivescripts** des Containers **adftutorial** , wählen Sie die Datei **hivescript.hql** aus, und klicken Sie auf **Fertig stellen** .  
        
        ![Auswählen einer Datei oder eines Ordners](./media/tutorial-transform-data-using-hive-in-vnet-portal/choose-file-folder.png) 
    4. Vergewissern Sie sich, dass **Dateipfad** die Zeichenfolge **adftutorial/hivescripts/hivescript.hql** enthält.

        ![Skripteinstellungen](./media/tutorial-transform-data-using-hive-in-vnet-portal/confirm-hive-script-settings.png)
    5. Erweitern Sie auf der Registerkarte **Skript** den Abschnitt **Erweitert** . 
    6. Klicken Sie unter **Parameter** auf **Auto-fill from script** (Automatisch anhand des Skripts ausfüllen). 
    7. Geben Sie den Wert für den Parameter **Ausgabe** im folgenden Format ein: `wasbs://<Blob Container>@<StorageAccount>.blob.core.windows.net/outputfolder/`. Beispiel: `wasbs://adftutorial@mystorageaccount.blob.core.windows.net/outputfolder/`.
 
        ![Skriptargumente](./media/tutorial-transform-data-using-hive-in-vnet-portal/script-arguments.png)
1. Klicken Sie auf **Veröffentlichen** , um Artefakte für Data Factory zu veröffentlichen.

    ![Screenshot der Option zum Veröffentlichen einer Data Factory](./media/tutorial-transform-data-using-hive-in-vnet-portal/publish.png)

## <a name="trigger-a-pipeline-run"></a>Auslösen einer Pipelineausführung

1. Klicken Sie zum Überprüfen der Pipeline zunächst auf der Symbolleiste auf die Schaltfläche **Überprüfen** . Klicken Sie auf **>>** (Pfeil nach rechts), um das Fenster mit der Ausgabe der **Pipelineüberprüfung** zu schließen. 

    ![Überprüfen der Pipeline](./media/tutorial-transform-data-using-hive-in-vnet-portal/validate-pipeline.png) 
2. Klicken Sie auf der Symbolleiste auf „Trigger“ und anschließend auf „Trigger Now“ (Jetzt auslösen), um eine Pipelineausführung auszulösen. 

    ![Manuelles Auslösen](./media/tutorial-transform-data-using-hive-in-vnet-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>Überwachen der Pipelineausführung

1. Wechseln Sie im linken Bereich zur Registerkarte **Überwachen** . In der Liste mit den **Pipelineausführungen** wird eine Pipelineausführung angezeigt. 

    ![Überwachen der Pipelineausführungen](./media/tutorial-transform-data-using-hive-in-vnet-portal/monitor-pipeline-runs.png)
2. Klicken Sie zum Aktualisieren der Liste auf **Aktualisieren** .
4. Wenn Sie mit der Pipelineausführung verknüpfte Aktivitätsausführungen anzeigen möchten, klicken Sie in der Spalte **Aktionen** auf **View activity runs** (Aktivitätsausführungen anzeigen). Andere Aktionslinks dienen zum Beenden/erneuten Ausführen der Pipeline. 

    ![Anzeigen der Aktivitätsausführungen](./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs-link.png)
5. Da die Pipeline nur eine einzelne Aktivität vom Typ **HDInsightHive** enthält, wird nur eine einzelne Aktivitätsausführung angezeigt. Klicken Sie im oberen Bereich auf den Link **Pipelines** , um zur vorherigen Ansicht zurückzukehren.

    ![Aktivitätsausführungen](./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs.png)
6. Vergewissern Sie sich, dass im Ausgabeordner ( **outputfolder** ) des Containers **adftutorial** eine Ausgabedatei angezeigt wird. 

    ![Ausgabedatei](./media/tutorial-transform-data-using-hive-in-vnet-portal/output-file.png)

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie die folgenden Schritte ausgeführt: 

> [!div class="checklist"]
> * Erstellen einer Data Factory. 
> * Erstellen einer selbstgehosteten Integration Runtime
> * Erstellen verknüpfter Azure Storage- und HDInsight-Dienste
> * Erstellen einer Pipeline mit Hive-Aktivität
> * Auslösen einer Pipelineausführung
> * Überwachen der Pipelineausführung 
> * Überprüfen der Ausgabe

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie mithilfe eines Spark-Clusters in Azure Daten transformieren:

> [!div class="nextstepaction"]
>[Branchen und Verketten der Data Factory-Ablaufsteuerung](tutorial-control-flow-portal.md)
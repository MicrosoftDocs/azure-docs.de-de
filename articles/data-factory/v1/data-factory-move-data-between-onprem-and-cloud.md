---
title: Verschieben von Daten – Datenverwaltungsgateway
description: Richten Sie ein Datengateway ein, um Daten zwischen dem lokalen Speicher und der Cloud zu verschieben. Verwenden Sie das Datenverwaltungsgateway in Azure Data Factory zum Verschieben Ihrer Daten.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.assetid: 7bf6d8fd-04b5-499d-bd19-eff217aa4a9c
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 7f07f08cd320d94495403b0f5ae65d60d8dc93b5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019683"
---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Verschieben von Daten zwischen lokalen Quellen und der Cloud mit dem Datenverwaltungsgateway
> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [Kopieren von Daten zwischen lokalen Quellen und der Cloud mithilfe von Data Factory](../tutorial-hybrid-copy-powershell.md).

Dieser Artikel enthält eine Übersicht über die Datenintegration zwischen lokalen Datenspeichern und Clouddatenspeichern mit Data Factory. Er baut auf dem Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) und anderen Artikeln über zentrale Konzepte von Data Factory auf: [Datasets](data-factory-create-datasets.md) und [Pipelines](data-factory-create-pipelines.md).

## <a name="data-management-gateway"></a>Gateway zur Datenverwaltung
Sie müssen das Datenverwaltungsgateway auf dem lokalen Computer installieren, um das Verschieben von Daten in einen bzw. aus einem lokalen Datenspeicher zu ermöglichen. Das Gateway kann auf dem gleichen Computer wie der Datenspeicher oder auf einem anderen Computer installiert werden, solange das Gateway eine Verbindung mit dem Datenspeicher herstellen kann.

> [!IMPORTANT]
> Im Artikel [Datenverwaltungsgateway](data-factory-data-management-gateway.md) finden Sie Einzelheiten zum Datenverwaltungsgateway.

In der folgenden exemplarischen Vorgehensweise wird gezeigt, wie Sie eine Data Factory mit einer Pipeline erstellen, die Daten aus einer lokalen **SQL Server**-Datenbank in eine Azure Blob Storage-Instanz verschiebt. Im Rahmen der exemplarischen Vorgehensweise installieren und konfigurieren Sie das Datenverwaltungsgateway auf Ihrem Computer.

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Exemplarische Vorgehensweise: Kopieren lokaler Daten in die Cloud
In dieser exemplarischen Vorgehensweise führen Sie die folgenden Schritte aus:

1. Erstellen einer Data Factory.
2. Erstellen eines Datenverwaltungsgateways
3. Erstellen eines verknüpften Dienstes für Quell- und Senkendatenspeicher
4. Erstellen von Datasets zur Darstellung von Eingabe- und Ausgabedaten
5. Erstellen einer Pipeline mit Kopieraktivität zum Verschieben der Daten

## <a name="prerequisites-for-the-tutorial"></a>Voraussetzungen für das Tutorial
Bevor Sie mit dieser exemplarischen Vorgehensweise beginnen, müssen folgende Voraussetzungen erfüllt sein:

* **Azure-Abonnement**.  Wenn Sie über kein Abonnement verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Im Artikel [Kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/) finden Sie Details.
* **Azure Storage-Konto**. In diesem Tutorial verwenden Sie den Blobspeicher als **Ziel-/Senkendatenspeicher**. Wenn Sie kein Azure Storage-Konto haben, finden Sie im Artikel [Erstellen eines Speicherkontos](../../storage/common/storage-account-create.md) Schritte zum Erstellen eines Azure Storage-Kontos.
* **SQL Server**. In diesem Tutorial verwenden Sie eine SQL Server-Datenbank als **Quelldatenspeicher**.

## <a name="create-data-factory"></a>Erstellen einer Data Factory
In diesem Schritt verwenden Sie das Azure-Portal zum Erstellen einer Azure Data Factory-Instanz mit dem Namen **ADFTutorialOnPremDF**.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
2. Klicken Sie auf **Ressource erstellen**, dann auf **Intelligence + Analyse** und schließlich auf **Data Factory**.

   ![Neu -> Data Factory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. Geben Sie auf der Seite **Neue Data Factory** unter „Name“ die Zeichenfolge **ADFTutorialOnPremDF** ein.

    ![Zum Startmenü hinzufügen](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > Der Name der Azure Data Factory muss global eindeutig sein. Bei Anzeige der Fehlermeldung **Data factory name ADFTutorialOnPremDF is not available**. Ändern Sie den Namen der Data Factory (z.B.in „IhrNameADFTutorialOnPremDF“), und wiederholen den Vorgang. Verwenden Sie diesen Namen beim Ausführen der restlichen Schritte in diesem Lernprogramm anstelle von "ADFTutorialOnPremDF".
   >
   > Der Name der Data Factory kann in Zukunft als **DNS**-Name registriert und so öffentlich sichtbar gemacht werden.
   >
   >
4. Wählen Sie das **Azure-Abonnement** , in dem die Data Factory erstellt werden soll.
5. Wählen Sie eine vorhandene **Ressourcengruppe** aus, oder erstellen Sie eine Ressourcengruppe. Erstellen Sie für das Tutorial eine Ressourcengruppe mit dem folgenden Namen: **ADFTutorialResourceGroup**.
6. Klicken Sie auf der Seite **Neue Data Factory** auf **Erstellen**.

   > [!IMPORTANT]
   > Zum Erstellen von Data Factory-Instanzen müssen Sie Mitglied der Rolle [Data Factory-Mitwirkender](../../role-based-access-control/built-in-roles.md#data-factory-contributor) auf Abonnement- bzw. Ressourcengruppenebene sein.
   >
   >
7. Nach Abschluss der Erstellung wird die Seite **Data Factory** wie in der folgenden Abbildung dargestellt angezeigt:

   ![Data Factory-Startseite](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Erstellen des Gateways
1. Klicken Sie auf der Seite **Data Factory** auf die Kachel **Erstellen und bereitstellen**, um den **Editor** für die Data Factory zu starten.

    ![Kachel „Erstellen und bereitstellen“](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. Klicken Sie im Data Factory-Editor auf der Symbolleiste auf **... Mehr** und dann auf **Neues Datengateway**. Sie können auch in der Strukturansicht mit der rechten Maustaste auf **Datengateways** und dann auf **Neues Datengateway** klicken.

   !["Neues Daten-Gateway" auf der Symbolleiste](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. Geben Sie auf der Seite **Erstellen** den Namen **adftutorialgateway** unter **Name** ein, und klicken Sie auf **OK**.     

    ![Seite „Gateway erstellen“](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

    > [!NOTE]
    > In dieser exemplarischen Vorgehensweise erstellen Sie ein logisches Gateway mit nur einem Knoten (lokaler Windows-Computer). Sie können ein Datenverwaltungsgateway aufskalieren, indem Sie ihm mehrere lokale Computer zuordnen. Sie können hochskalieren, indem Sie die Anzahl von Datenverschiebungsaufträgen erhöhen, die auf einem Knoten gleichzeitig ausgeführt werden können. Diese Funktion ist auch für ein logisches Gateway mit einem einzelnen Knoten verfügbar. Ausführliche Informationen hierzu finden Sie im Artikel [Data Management Gateway – high availability and scalability (Preview)](data-factory-data-management-gateway-high-availability-scalability.md) (Datenverwaltungsgateway – Hohe Verfügbarkeit und Skalierbarkeit (Vorschauversion)).  
4. Klicken Sie auf der Seite **Konfigurieren** auf **Direkt auf diesem Computer installieren**. Diese Aktion lädt das Installationspaket für das Gateway herunter und installiert, konfiguriert und registriert das Gateway auf dem Computer.  

   > [!NOTE]
   > Verwenden Sie Internet Explorer oder einen mit Microsoft ClickOnce kompatiblen Webbrowser.
   >
   > Navigieren Sie bei Verwendung von Chrome zum [Chrome Web Store](https://chrome.google.com/webstore/), und suchen Sie nach „ClickOnce“. Wählen Sie eine der ClickOnce-Erweiterungen aus, und installieren Sie sie.
   >
   > Gehen Sie bei Firefox genauso vor (Installation des Add-Ins). Klicken Sie auf der Symbolleiste auf die Schaltfläche **Menü öffnen** (**drei waagerechte Striche oben rechts**), klicken Sie auf **Add-Ons**, suchen Sie nach dem Stichwort „ClickOnce“, wählen Sie eine der ClickOnce-Erweiterungen aus, und installieren Sie sie.    
   >
   >

    ![Seite „Gateway konfigurieren“](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    Dies ist die einfachste Möglichkeit (One-Click) zum Herunterladen, Installieren, Konfigurieren und Registrieren des Gateways in einem einzigen Schritt. Sie können sehen, dass die Anwendung **Microsoft Datenverwaltungsgateway – Konfigurations-Manager** auf Ihrem Computer installiert ist. Im folgenden Ordner ist auch die ausführbare Datei **ConfigManager.exe** enthalten: **C:\Programme\Microsoft Data Management Gateway\2.0\Shared**.

    Sie können das Gateway auch manuell über die Links auf dieser Seite herunterladen und installieren und mit dem Schlüssel im Textfeld **NEUER SCHLÜSSEL** registrieren.

    Im Artikel [Datenverwaltungsgateway](data-factory-data-management-gateway.md) finden Sie alle Informationen zum Gateway.

   > [!NOTE]
   > Sie müssen ein Administrator auf dem lokalen Computer sein, um das Datenverwaltungsgateway erfolgreich installieren und konfigurieren zu können. Sie können der lokalen Windows-Gruppe **Datenverwaltungsgateway-Benutzer** zusätzliche Benutzer hinzufügen. Die Mitglieder dieser Gruppe können das Gateway mithilfe des Datenverwaltungsgateway-Konfigurations-Managers konfigurieren.
   >
   >
5. Warten Sie einige Minuten, oder warten Sie, bis die folgende Benachrichtigung angezeigt wird:

    ![Gatewayinstallation erfolgreich](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. Starten Sie den **Datenverwaltungsgateway-Konfigurations-Manager** auf Ihrem Computer. Geben Sie im Fenster **Suchen** den Begriff **Datenverwaltungsgateway** ein, um auf dieses Hilfsprogramm zuzugreifen. Im folgenden Ordner ist auch die ausführbare Datei **ConfigManager.exe** enthalten: **C:\Programme\Microsoft Data Management Gateway\2.0\Shared**.

    ![Gatewaykonfigurations-Manager](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. Vergewissern Sie sich, dass die Meldung `adftutorialgateway is connected to the cloud service` angezeigt wird. Die Statusleiste im unteren Bereich zeigt **Verbunden mit Clouddienst** sowie ein **grünes Häkchen** an.

    Auf der Registerkarte **Start** sind die folgenden Vorgänge möglich:

   * **Registrieren** eines Gateways mit einem Schlüssel aus dem Azure-Portal mithilfe der Schaltfläche „Registrieren“
   * **Beenden** des Datenverwaltungsgateway-Hostdiensts, der auf Ihrem Gatewaycomputer ausgeführt wird
   * **Planen von Updates**, die zu einem bestimmten Zeitpunkt des Tages installiert werden
   * Anzeigen, wann das Gateway **zuletzt aktualisiert** wurde
   * Angeben des Zeitpunkts, an dem ein Update für das Gateway installiert werden kann
8. Wechseln Sie zur Registerkarte **Einstellungen**. Das Zertifikat im Abschnitt **Zertifikat** dient zum Verschlüsseln/Entschlüsseln von Anmeldeinformationen für den lokalen Datenspeicher, den Sie im Portal angeben. (optional) Klicken Sie auf **Ändern** , um stattdessen Ihr eigenes Zertifikat zu verwenden. Standardmäßig verwendet das Gateway das Zertifikat, das vom Data Factory-Dienst automatisch generiert wird.

    ![Konfiguration des Gatewayzertifikats](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    Auf der Registerkarte **Einstellungen** können Sie zudem die folgenden Aktionen ausführen:

   * Anzeigen oder Exportieren des Zertifikats, das vom Gateway verwendet wird
   * Ändern des HTTPS-Endpunkts, der vom Gateway verwendet wird    
   * Festlegen eines HTTP-Proxys, der vom Gateway verwendet werden soll     
9. (Optional) Wechseln Sie zur Registerkarte **Diagnose**, und aktivieren Sie die Option **Ausführliche Protokollierung aktivieren**, wenn Sie die ausführliche Protokollierung aktivieren möchten, um Probleme mit dem Gateway behandeln zu können. Die Protokollinformationen finden Sie in der **Ereignisanzeige** unter **Anwendungs- und Dienstprotokolle** -> Knoten **Datenverwaltungsgateway**.

    ![Registerkarte „Diagnose“](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    Sie können auch wie folgt auf der Registerkarte **Diagnose** vorgehen:

   * Verwenden Sie den Abschnitt **Verbindung testen** für eine lokale Datenquelle unter Verwendung des Gateways.
   * Klicken Sie auf **Protokolle anzeigen** , um das Protokoll des Datenverwaltungsgateways in einem Ereignisanzeigenfenster anzuzeigen.
   * Klicken Sie auf **Protokolle senden** , um eine ZIP-Datei mit den Protokollen der letzten sieben Tage an Microsoft zu senden und dadurch die Behebung Ihrer Probleme zu erleichtern.
10. Wählen Sie auf der Registerkarte **Diagnose** im Abschnitt **Verbindung testen** als Typ des Datenspeichers die Option **SqlServer** aus, geben Sie den Namen des Datenbankservers, den Namen der Datenbank, den Authentifizierungstyp, den Benutzernamen und das Kennwort ein, und klicken Sie auf **Testen**, um zu prüfen, ob das Gateway eine Verbindung mit der Datenbank herstellen kann.
11. Wechseln Sie zum Webbrowser, und klicken Sie im **Azure-Portal** auf der Seite **Konfigurieren** und dann auf der Seite **Neues Datengateway** auf **OK**.
12. In der Strukturansicht links sollte **adftutorialgateway** unter **Datengateways** angezeigt werden.  Wenn Sie darauf klicken, sollte die zugehörige JSON angezeigt werden.

## <a name="create-linked-services"></a>Erstellen von verknüpften Diensten
In diesem Schritt erstellen Sie zwei verknüpfte Dienste: **AzureStorageLinkedService** und **SqlServerLinkedService**. **SqlServerLinkedService** verknüpft eine SQL Server-Datenbank und der verknüpfte Dienst **AzureStorageLinkedService** einen Azure-Blobspeicher mit der Data Factory. Sie erstellen weiter unten in dieser exemplarischen Vorgehensweise eine Pipeline, die Daten aus der SQL Server-Datenbank in Azure Blob Storage kopiert.

#### <a name="add-a-linked-service-to-a-sql-server-database"></a>Hinzufügen eines verknüpften Diensts zu einer SQL Server-Datenbank
1. Klicken Sie in **Data Factory Editor** auf der Symbolleiste auf **Neuer Datenspeicher**, und wählen Sie **SQL Server**.

   ![Neuer mit SQL Server verknüpfter Dienst](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. Gehen Sie rechts im **JSON-Editor** folgendermaßen vor:

   1. Geben Sie unter **gatewayName** den Namen **adftutorialgateway** an.    
   2. Führen Sie für **connectionString** die folgenden Schritte aus:    

      1. Geben Sie für **Servername** den Namen des Servers ein, auf dem die SQL Server-Datenbank gehostet wird.
      2. Geben Sie für **Datenbankname** den Namen der Datenbank ein.
      3. Klicken Sie auf der Symbolleiste auf die Schaltfläche **Verschlüsseln**. Jetzt sehen Sie die Anwendung „Anmeldeinformations-Manager“

         ![Anwendung „Anmeldeinformations-Manager“](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. Geben Sie im Dialogfeld **Anmeldeinformationen festlegen** den Authentifizierungstyp, den Benutzernamen und das Kennwort an, und klicken Sie auf **OK**. Wenn die Verbindung erfolgreich hergestellt wurde, werden die verschlüsselten Anmeldeinformationen im JSON-Editor gespeichert, und das Dialogfeld wird geschlossen.
      5. Schließen Sie die leere Browserregisterkarte, über die das Dialogfeld gestartet wurde, wenn sie nicht automatisch geschlossen wird, und wechseln Sie wieder zur Registerkarte mit dem Azure-Portal.

         Auf dem Gatewaycomputer werden die Anmeldeinformationen mithilfe eines Zertifikats, das der Data Factory-Dienst besitzt, **verschlüsselt**. Wenn Sie stattdessen das Zertifikat verwenden möchten, das dem Datenverwaltungsgateway zugeordnet ist, lesen Sie „Sicheres Festlegen von Anmeldeinformationen“.    
   3. Klicken Sie auf der Befehlsleiste auf **Bereitstellen** , um den verknüpften SQL Server-Dienst bereitzustellen. Der verknüpfte Dienst sollte in der Strukturansicht angezeigt werden.

      ![Mit SQL Server verknüpfter Dienst in der Strukturansicht](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Hinzufügen eines verknüpften Diensts für Azure-Speicherkonten
1. Klicken Sie im **Data Factory-Editor** auf der Befehlsleiste auf **Neuer Datenspeicher** und dann auf **Azure-Speicher**.
2. Geben Sie den Namen des Azure-Speicherkontos für **Kontoname** ein.
3. Geben Sie den Schlüssel des Azure-Speicherkontos für **Kontoschlüssel** ein.
4. Klicken Sie auf **Bereitstellen**, um **AzureStorageLinkedService** bereitzustellen.

## <a name="create-datasets"></a>Erstellen von Datasets
In diesem Schritt erstellen Sie die Eingabe- und Ausgabedatasets, die ein- und ausgehende Daten für den Kopiervorgang darstellen (lokale SQL Server-Datenbank =&gt; Azure Blob Storage). Führen Sie vor dem Erstellen von Datasets die folgenden Schritte aus (ausführliche Schritte finden Sie im Anschluss an die Liste):

* Erstellen Sie eine Tabelle mit dem Namen **emp** in der SQL Server-Datenbank, die Sie der Data Factory als verknüpften Dienst hinzugefügt haben, und fügen Sie verschiedene Beispieleinträge in die Tabelle ein.
* Erstellen Sie einen Blobcontainer mit dem Namen **adftutorial** im Azure Blob Storage-Konto, das Sie der Data Factory als verknüpften Dienst hinzugefügt haben.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>Vorbereitung des lokalen SQL Servers für das Lernprogramm
1. Verwenden Sie in der Datenbank, die Sie für den verknüpften SQL Server-Dienst (**SqlServerLinkedService**) angegeben haben, das folgende SQL-Skript zum Erstellen der Tabelle **emp** in der Datenbank.

    ```SQL   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )
    GO
    ```
2. Fügen Sie einige Beispiele in die Tabelle ein:

    ```SQL
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="create-input-dataset"></a>Erstellen eines Eingabedatasets

1. Klicken Sie im **Data Factory-Editor** in der Befehlszeile auf **... Mehr**, auf der Befehlszeile auf **Neues Dataset** und dann auf **SQL Server-Tabelle**.
2. Ersetzen Sie das JSON-Skript im rechten Bereich durch folgenden Text:

    ```JSON   
    {        
        "name": "EmpOnPremSQLTable",
        "properties": {
            "type": "SqlServerTable",
            "linkedServiceName": "SqlServerLinkedService",
            "typeProperties": {
                "tableName": "emp"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }     
    ```     
   Beachten Sie folgende Punkte:

   * **type** ist auf **SqlServerTable** festgelegt.
   * **tableName** ist auf **emp** festgelegt.
   * **linkedServiceName** ist auf **SqlServerLinkedService** festgelegt (diesen verknüpften Dienst haben Sie zuvor in dieser exemplarischen Vorgehensweise erstellt).
   * Für ein Eingabedataset, das nicht durch eine andere Pipeline in Azure Data Factory generiert wird, müssen Sie **external** auf **true** festlegen. Dies bedeutet, dass die eingegebenen Daten außerhalb des Azure Data Factory-Diensts erstellt werden. Optional können Sie externe Datenrichtlinien mit dem **externalData**-Element im **policy**-Abschnitt angeben.    

   Details zu JSON-Eigenschaften finden Sie unter [Verschieben von Daten in und aus SQL Server](data-factory-sqlserver-connector.md).
3. Klicken Sie in der Befehlsleiste auf **Bereitstellen** , um das Dataset bereitzustellen.  

### <a name="create-output-dataset"></a>Erstellen des Ausgabedatasets

1. Klicken Sie im **Data Factory-Editor** auf der Symbolleiste auf **Neues Dataset** und dann auf **Azure Blob Storage** .
2. Ersetzen Sie das JSON-Skript im rechten Bereich durch folgenden Text:

    ```JSON   
    {
        "name": "OutputBlobTable",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/outfromonpremdf",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
     }
    ```   
   Beachten Sie folgende Punkte:

   * **type** ist auf **AzureBlob** festgelegt.
   * **linkedServiceName** ist auf **AzureStorageLinkedService** festgelegt (diesen verknüpften Dienst haben Sie in Schritt 2 erstellt).
   * **folderPath** ist auf **adftutorial/outfromonpremdf** festgelegt, wobei „outfromonpremdf“ der Ordner im „adftutorial“-Container ist. ErsteIlen Sie den Container **adftutorial** , falls er nicht bereits vorhanden ist.
   * Die Verfügbarkeit (**availability**) ist auf **hourly**, (**frequency** auf **hour** und **interval** auf **1**) festgelegt.  Der Data Factory-Dienst generiert in der Tabelle **emp** in der Azure SQL-Datenbank stündlich einen Ausgabedatenslice.

   Wenn Sie kein **fileName**-Element für eine **Ausgabetabelle** angeben, werden die generierten Dateien unter **folderPath** nach dem folgenden Format benannt: `Data.<Guid>.txt` (Beispiel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt).

   Um **folderPath** und **fileName** dynamisch basierend auf der **SliceStart**-Zeit festzulegen, verwenden Sie die Eigenschaft „partitionedBy“. Im folgenden Beispiel verwendet folderPath die Angaben für Jahr, Monat und Tag aus „SliceStart“ (Startzeit des zu verarbeitenden Slices) und „fileName“ die Angabe für Stunde aus „SliceStart“. Wenn beispielsweise ein Slice für den Zeitpunkt "2014-10-20T08:00:00" erzeugt wird, wird "folderName" auf "wikidatagateway/wikisampledataout/2014/10/20" und "filName" auf "08.csv" festgelegt.

    ```JSON
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
    [

        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],
    ```

    Details zu JSON-Eigenschaften finden Sie unter [Verschieben von Daten in und aus Azure Blob Storage](data-factory-azure-blob-connector.md).
3. Klicken Sie in der Befehlsleiste auf **Bereitstellen** , um das Dataset bereitzustellen. Vergewissern Sie sich, dass beide Datasets in der Strukturansicht angezeigt werden.  

## <a name="create-pipeline"></a>Erstellen der Pipeline
In diesem Schritt erstellen Sie eine **Pipeline** mit einer **Kopieraktivität**, die **EmpOnPremSQLTable** als Eingabe und **OutputBlobTable** als Ausgabe verwendet.

1. Klicken Sie im Data Factory-Editor auf **... More** (Mehr), und klicken Sie auf **Neue Pipeline**.
2. Ersetzen Sie das JSON-Skript im rechten Bereich durch folgenden Text:    

    ```JSON   
     {
         "name": "ADFTutorialPipelineOnPrem",
         "properties": {
         "description": "This pipeline has one Copy activity that copies data from an on premises SQL to Azure blob",
         "activities": [
           {
             "name": "CopyFromSQLtoBlob",
             "description": "Copy data from on premises SQL server to blob",
             "type": "Copy",
             "inputs": [
               {
                 "name": "EmpOnPremSQLTable"
               }
             ],
             "outputs": [
               {
                 "name": "OutputBlobTable"
               }
             ],
             "typeProperties": {
               "source": {
                 "type": "SqlSource",
                 "sqlReaderQuery": "select * from emp"
               },
               "sink": {
                 "type": "BlobSink"
               }
             },
             "Policy": {
               "concurrency": 1,
               "executionPriorityOrder": "NewestFirst",
               "style": "StartOfInterval",
               "retry": 0,
               "timeout": "01:00:00"
             }
           }
         ],
         "start": "2016-07-05T00:00:00Z",
         "end": "2016-07-06T00:00:00Z",
         "isPaused": false
       }
     }
    ```   
   > [!IMPORTANT]
   > Ersetzen Sie den Wert der **start**-Eigenschaft durch den aktuellen Tag und den der **end**-Eigenschaft durch den nächsten Tag.
   >
   >

   Beachten Sie folgende Punkte:

   * Der Abschnitt „activities“ enthält nur eine Aktivität, deren **type** auf **Copy** festgelegt ist.
   * **Input** für die Aktivität ist auf **EmpOnPremSQLTable** und **output** auf **OutputBlobTable** festgelegt.
   * Im Abschnitt **typeProperties** ist **SqlSource** als **Quelltyp** und **BlobSink** als **Senkentyp** angegeben.
   * Die SQL-Abfrage `select * from emp` ist für die **sqlReaderQuery**-Eigenschaft von **SqlSource** angegeben.

   Die Start- und Endzeit von Datums-/Uhrzeitangaben müssen im [ISO-Format](https://en.wikipedia.org/wiki/ISO_8601)angegeben werden. Beispiel: 2014-10-14T16:32:41Z. Die Zeitangabe **end** ist optional, wird aber in diesem Tutorial verwendet.

   Wenn für die **end**-Eigenschaft kein Wert angegeben wird, wird sie als „**start + 48 Stunden**“ berechnet. Um die Pipeline auf unbestimmte Zeit auszuführen, geben Sie als Wert für die **end**-Eigenschaft **9/9/9999** an.

   Sie definieren die Verarbeitungsdauer der Datenslices basierend auf den **Verfügbarkeitseigenschaften**, die für jedes Azure Data Factory-Dataset definiert wurden.

   In diesem Beispiel ergeben sich 24 Datenslices, da jede Stunde ein Datenslice erstellt wird.        
3. Klicken Sie auf der Befehlsleiste auf **Bereitstellen** , um das Dataset bereitzustellen (eine Tabelle ist ein rechteckiges Dataset). Überprüfen Sie, ob die Pipeline in der Strukturansicht unter dem Knoten **Pipelines** angezeigt wird.  
4. Klicken Sie nun zweimal auf **X**, um die Seiten zu schließen und zur Seite **Data Factory** für **ADFTutorialOnPremDF** zurückzukehren.

**Glückwunsch!** Sie haben erfolgreich eine Azure Data Factory, verknüpfte Dienste, Datasets und eine Pipeline erstellt und die Pipeline geplant.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>Anzeigen einer Diagrammansicht der Data Factory
1. Klicken Sie im **Azure-Portal** auf der Startseite für die Data Factory **ADFTutorialOnPremDF** auf die Kachel **Diagramm**: decodiert werden:

    ![Link „Diagramm“](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. Ein Diagramm wie in der folgenden Abbildung wird angezeigt:

    ![Diagrammansicht](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    Sie können die Ansicht vergrößern, verkleinern, auf 100 % anpassen, an die Fenstergröße anpassen, Pipelines und Datasets automatisch positionieren und Informationen zur Datenherkunft anzeigen (d.h. vor- und nachgelagerte Elemente ausgewählter Elemente hervorheben).  Sie können auf ein Objekt (im Ein-/Ausgabedataset oder in der Pipeline) doppelklicken, um seine Eigenschaften anzuzeigen.

## <a name="monitor-pipeline"></a>Überwachen der Pipeline
In diesem Schritt verwenden Sie das Azure-Portal zur Überwachung der Aktivitäten in einer Azure Data Factory. Sie können auch PowerShell-Cmdlets zum Überwachen von Datasets und Pipelines verwenden. Einzelheiten zur Überwachung finden Sie unter [Überwachen und Verwalten von Pipelines](data-factory-monitor-manage-pipelines.md).

1. Doppelklicken Sie im Diagramm auf **EmpOnPremSQLTable**.  

    ![EmpOnPremSQLTable-Slices](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. Beachten Sie, dass alle Datenslices den Status **Bereit** aufweisen, da die Dauer der Pipeline (Startzeit bis Endzeit) in der Vergangenheit liegt. Das liegt auch daran, dass Sie die Daten in die SQL Server-Datenbank eingefügt haben und sie dort immer vorhanden sind. Überprüfen Sie, ob keine Slices im Abschnitt **Problemslices** am unteren Rand angezeigt werden. Um alle Slices anzuzeigen, klicken Sie unten in der Liste der Slices auf **Weitere anzeigen**.
3. Klicken Sie jetzt auf der Seite **Datasets** auf **OutputBlobTable**.

    ![OutputBlobTable-Slices](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. Klicken Sie in der Liste auf einen beliebigen Datenslice. Die Seite **Datenslice** wird angezeigt. Die Aktivitätsausführungen für den Slice werden angezeigt. Normalerweise wird nur eine ausgeführte Aktivität angezeigt.  

    ![Blatt "Datenslice"](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Wenn der Slice nicht den Status **Bereit** hat, sehen Sie die vorgelagerten Slices, die nicht bereit sind und das Ausführen des aktuellen Slices blockieren, in der Liste **Vorgelagerte Slices, die nicht bereit sind**.
5. Klicken Sie in der Liste unten auf die **Aktivitätsausführung**, um **Aktivitätsausführung – Details** anzuzeigen.

   ![Detailseite „Aktivitätsausführung“](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   Hier werden Informationen wie z.B. der Durchsatz, die Dauer und das Gateway, über das die Daten übertragen werden, angezeigt.
6. Klicken Sie auf **X** , um alle Seiten zu schließen, bis wieder
7. die Startseite für **ADFTutorialOnPremDF** angezeigt wird.
8. Optional: Klicken Sie auf **Pipelines**, dann auf **ADFTutorialOnPremDF**, und zeigen Sie Details zu den Eingabetabellen (**Genutzt**) oder Ausgabedatasets (**Erstellt**) an.
9. Verwenden Sie Tools wie z.B. den [Microsoft Storage-Explorer](https://storageexplorer.com/), um zu überprüfen, ob ein Blob oder eine Datei für jede Stunde erstellt wird.

   ![Azure Storage-Explorer](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>Nächste Schritte
* Im Artikel [Datenverwaltungsgateway](data-factory-data-management-gateway.md) finden Sie alle Informationen zum Datenverwaltungsgateway.
* Unter [Kopieren von Daten aus Azure-Blob in Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) erfahren Sie, wie Sie die Kopieraktivität verwenden, um Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher zu verschieben.

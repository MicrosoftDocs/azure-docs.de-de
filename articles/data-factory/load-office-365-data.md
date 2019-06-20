---
title: Laden von Daten aus Office 365 mithilfe von Azure Data Factory | Microsoft-Dokumentation
description: Kopieren von Daten aus Office 365 mithilfe von Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: jingwang
ms.openlocfilehash: fe3a3b673f6512856f3640b3e103db8623570a88
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60548110"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Laden von Daten aus Office 365 mithilfe von Azure Data Factory

In diesem Artikel wird gezeigt, wie Sie mithilfe von Data Factory _Daten aus Office 365 in Azure Blob Storage laden_. Zum Kopieren von Daten in Azure Data Lake Gen1 oder Gen2 werden ähnliche Schritte ausgeführt. Allgemeine Informationen zum Kopieren von Daten aus Office 365 finden Sie im Artikel zum [Office 365-Connector](connector-office-365.md).

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Wählen Sie im Menü auf der linken Seite **Ressource erstellen** > **Daten + Analysen** > **Data Factory** aus: 
   
   ![Auswählen von „Data Factory“ im Bereich „Neu“](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Geben Sie auf der Seite **Neue Data Factory** die in der folgenden Abbildung gezeigten Werte für die Felder an:
      
   ![Seite „Neue Data Factory“](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **Name**: Geben Sie einen global eindeutigen Namen für die Azure Data Factory ein. Wenn die Fehlermeldung „Data Factory mit dem Namen \"LoadFromOffice365Demo\" ist nicht verfügbar“ angezeigt wird, geben Sie einen anderen Namen für die Data Factory ein. Sie können beispielsweise den Namen _**IhrName**_ **LoadFromOffice365Demo** verwenden. Versuchen Sie erneut, die Data Factory zu erstellen. Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](naming-rules.md).
    * **Abonnement**: Wählen Sie Ihr Azure-Abonnement aus, in dem die Data Factory erstellt werden soll. 
    * **Ressourcengruppe**: Wählen Sie eine vorhandene Ressourcengruppe aus der Dropdownliste aus, oder wählen Sie die Option **Neu erstellen** aus, und geben Sie dann den Namen einer Ressourcengruppe ein. Weitere Informationen über Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/resource-group-overview.md).  
    * **Version**: Wählen Sie **V2** aus.
    * **Standort**: Wählen Sie den Standort für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Die von der Data Factory verwendeten Datenspeicher können sich an anderen Standorten bzw. in anderen Regionen befinden. Diese Datenspeicher umfassen Azure Data Lake Store, Azure Storage, Azure SQL-Datenbank usw.

3. Klicken Sie auf **Erstellen**.
4. Nach Abschluss der Erstellung navigieren Sie zu Ihrer Data Factory. Die Startseite **Data Factory** wird wie in der folgenden Abbildung dargestellt angezeigt:
   
   ![Data Factory-Startseite](./media/load-office-365-data/data-factory-home-page.png)

5. Wählen Sie die Kachel **Erstellen und überwachen** aus, um die Datenintegrationsanwendung auf einer separaten Registerkarte zu starten.

## <a name="create-a-pipeline"></a>Erstellen einer Pipeline

1. Wählen Sie auf der Seite „Erste Schritte“ die Option **Pipeline erstellen** aus.
 
    ![Erstellen der Pipeline](./media/load-office-365-data/create-pipeline-entry.png)

2. Geben Sie auf der Registerkarte **Allgemein** der Pipeline als **Name** der Pipeline „CopyPipeline“ ein.

3. Verschieben Sie in der Toolbox „Aktivitäten“ in der Kategorie „Move & Transform“ (Verschieben und transformieren) die **Copy**-Aktivität aus der Toolbox auf die Oberfläche des Pipeline-Designers. Geben Sie „CopyFromOffice365ToBlob“ als Aktivitätsnamen an.

### <a name="configure-source"></a>Konfigurieren der Quelle

1. Navigieren Sie zur Pipeline und zur Registerkarte **Quelle**, und klicken Sie auf **+ Neu**, um ein Quelldataset zu erstellen. 

2. Wählen Sie im Fenster „Neues Dataset“ die Option **Office 365** aus, und klicken Sie dann auf **Fertig stellen**.

    ![Neues Office 365-Dataset](./media/load-office-365-data/new-office-365-dataset.png)
 
3. Eine neue Registerkarte für das Office 365-Dataset wird geöffnet. Geben Sie unten im Eigenschaftenfenster auf der Registerkarte **Allgemein** unter „Name“ den Namen „SourceOffice365Dataset“ ein.

    ![Konfigurieren des Office 365-Datasets unter „Allgemein“](./media/load-office-365-data/config-office-365-dataset-general.png)
 
4. Wechseln Sie im Eigenschaftenfenster zur Registerkarte **Verbindung**. Klicken Sie neben dem Textfeld „Verknüpfter Dienst“ auf **+ Neu**.
 
    ![Konfigurieren des Office 365-Datasets unter „Verbindung“](./media/load-office-365-data/config-office-365-dataset-connection.png)

5. Geben Sie im Fenster „New Linked Service“ (Neuer verknüpfter Dienst) als Name „Office365LinkedService“ sowie die Dienstprinzipal-ID und den Dienstprinzipalschlüssel ein, und wählen Sie dann „Speichern“, um den verknüpften Dienst bereitzustellen.

    ![Neuer verknüpfter Office 365-Dienst](./media/load-office-365-data/new-office-365-linked-service.png)
 
6. Nach der Erstellung des verknüpften Diensts werden wieder die Dataseteinstellungen angezeigt. Klicken Sie neben „Tabelle“ auf den Pfeil nach unten, um die Liste der verfügbaren Office 365-Datasets zu erweitern, und wählen Sie dann in der Dropdownliste „BasicDataSet_v0.Contact_v0“ aus:

    ![Konfigurieren des Office 365-Datasets unter „Tabelle“](./media/load-office-365-data/config-office-365-dataset-table.png)
 
7. Wechseln Sie im Eigenschaftenfenster zur Registerkarte **Schema**, und wählen Sie **Schema importieren** aus.  Die Schema- und Beispielwerte für das Kontaktdataset werden angezeigt.

    ![Konfigurieren des Office 365-Datasets unter „Schema“](./media/load-office-365-data/config-office-365-dataset-schema.png)

8. Gehen Sie nun zurück zur Pipeline und zur Registerkarte „Quelle“, und überprüfen Sie, ob „SourceBlobDataset“ ausgewählt ist.
 
### <a name="configure-sink"></a>Konfigurieren der Senke

1. Wechseln Sie zur Pipeline und zur Registerkarte **Senke**, und wählen Sie **+ Neu** aus, um ein Senkendataset zu erstellen.
 
2. Im Fenster „Neues Dataset“ wird beim Kopieren aus Office 365 nur das unterstützte Ziel ausgewählt. Wählen Sie **Azure Blob Storage** und dann **Fertig stellen** aus.  In diesem Tutorial kopieren Sie Office 365-Daten in Azure Blob Storage.

    ![Neues Blobdataset](./media/load-office-365-data/new-blob-dataset.png)

4. Geben Sie im Eigenschaftenfenster auf der Registerkarte **Allgemein** unter „Name“ den Namen „OutputBlobDataset“ ein.

5. Wechseln Sie im Eigenschaftenfenster zur Registerkarte **Verbindung**. Klicken Sie neben dem Textfeld „Verknüpfter Dienst“ auf **+ Neu**.

    ![Konfigurieren des Blobdatasets unter „Verbindung“](./media/load-office-365-data/config-blob-dataset-connection.png) 

6. Geben Sie im Fenster „New Linked Service“ (Neuer verknüpfter Dienst) als Name „AzureStorageLinkedService“ ein, wählen Sie in der Dropdownliste der Authentifizierungsmethoden die Option „Dienstprinzipal“ aus, und geben Sie den Dienstendpunkt, die Mandantendienstprinzipal-ID und den Dienstprinzipalschlüssel ein. Wählen Sie anschließend „Speichern“, um den verknüpften Dienst bereitzustellen.  [Hier](connector-azure-blob-storage.md#service-principal-authentication) finden Sie Informationen zum Einrichten der Dienstprinzipalauthentifizierung für Azure Blob Storage.

    ![Neuer verknüpfter Blobdienst](./media/load-office-365-data/new-blob-linked-service.png)

7. Nach der Erstellung des verknüpften Diensts werden wieder die Dataseteinstellungen angezeigt. Wählen Sie neben „Dateipfad“ die Option **Durchsuchen** aus, um den Ausgabeordner festzulegen, in dem die Office 365-Daten extrahiert werden.  Wählen Sie unter „Dateiformateinstellungen“ neben „Dateiformat“ die Option **JSON-Format** und neben „File Pattern“ (Dateimuster) die Option **Set of objects** (Objektgruppe) aus.

    ![Konfigurieren von Pfad und Format des Blobdatasets](./media/load-office-365-data/config-blob-dataset-path-and-format.png) 

8. Gehen Sie zurück zur Pipeline und zur Registerkarte „Senke“, und überprüfen Sie, ob „OutputBlobDataset“ ausgewählt ist.

## <a name="validate-the-pipeline"></a>Überprüfen der Pipeline

Klicken Sie auf der Symbolleiste auf **Überprüfen**, um die Pipeline zu überprüfen.

Sie können den JSON-Code der Pipeline auch anzeigen, indem Sie oben rechts auf „Code“ klicken.

## <a name="publish-the-pipeline"></a>Veröffentlichen der Pipeline

Wählen Sie auf der oberen Symbolleiste **Publish All** (Alle veröffentlichen). Mit dieser Aktion werden erstellte Entitäten (Datasets und Pipelines) in Data Factory veröffentlicht.

![Veröffentlichen von Änderungen](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>Manuelles Auslösen der Pipeline

Wählen Sie in der Symbolleiste die Option **Trigger** und dann **Trigger Now** (Jetzt auslösen). Klicken Sie auf der Seite „Pipeline Run“ (Pipelineausführung) auf **Fertig stellen**. 

## <a name="monitor-the-pipeline"></a>Überwachen der Pipeline

Wechseln Sie links zur Registerkarte **Überwachen**. Sie sehen eine Pipelineausführung, die von einem manuellen Trigger ausgelöst wird. Sie können über Links in der Spalte **Aktionen** Aktivitätsdetails anzeigen und die Pipeline erneut ausführen.

![Überwachen der Pipeline](./media/load-office-365-data/pipeline-monitoring.png) 

Klicken Sie in der Spalte „Aktionen“ auf den Link **View Activity Runs** (Aktivitätsausführungen anzeigen), um mit der Pipelineausführung verknüpfte Aktivitätsausführungen anzuzeigen. Da in diesem Beispiel nur eine Aktivität vorhanden ist, wird in der Liste nur ein Eintrag angezeigt. Wenn Sie Details zum Kopiervorgang anzeigen möchten, klicken Sie in der Spalte „Aktionen“ auf den Link **Details** (Brillensymbol).

![Überwachen der Aktivität](./media/load-office-365-data/activity-monitoring.png) 

Wenn Sie erstmals Daten für diesen Kontext anfordern (also für eine Kombination daraus, auf welche Datentabelle zugegriffen wird, in welches Zielkonto die Daten geladen werden und welche Benutzeridentität die Datenzugriffsanforderung sendet), wird als Status der Copy-Aktivität **In Bearbeitung** angezeigt. Nur wenn Sie unter „Aktionen“ auf den Link „Details“ klicken, wird als Status **RequestingConsent** angezeigt.  Ein Mitglied aus der Gruppe der Datenzugriffsgenehmiger muss die Anforderung in Privileged Access Management genehmigen, bevor die Datenextraktion fortgesetzt werden kann.

_Status, in dem Zustimmung angefordert wird:_ 
![Details zur Aktivitätsausführung: Anfordern der Zustimmung](./media/load-office-365-data/activity-details-request-consent.png) 

_Status, in dem Daten extrahiert werden:_

![Details zur Aktivitätsausführung: Extrahieren der Daten](./media/load-office-365-data/activity-details-extract-data.png) 

Nach dem Erteilen der Zustimmung wird die Datenextraktion fortgesetzt, und nach einiger Zeit wird die Pipelineausführung als abgeschlossen angezeigt.

![Überwachen der Pipeline: Erfolgreich](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

Navigieren Sie nun zur Azure Blob Storage-Zielinstanz, und überprüfen Sie, ob die Office 365-Daten im JSON-Format extrahiert wurden.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie den folgenden Artikel, um mehr über die Unterstützung von Azure SQL Data Warehouse zu erfahren: 

> [!div class="nextstepaction"]
>[Office 365-Connector](connector-office-365.md)
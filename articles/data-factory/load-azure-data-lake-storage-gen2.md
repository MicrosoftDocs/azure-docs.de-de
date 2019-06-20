---
title: Laden von Daten in Azure Data Lake Storage Gen2 mit Azure Data Factory
description: Kopieren von Daten in Azure Data Lake Storage Gen2 mithilfe von Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: jingwang
ms.openlocfilehash: f8af34207eddb613f7a59bd3e3d300555e10f985
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65560731"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Laden von Daten in Azure Data Lake Storage Gen2 mit Azure Data Factory

Azure Data Lake Storage Gen2 baut auf [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) auf und bietet eine Reihe von Funktionen für die Big Data-Analyse. Er ermöglicht Ihnen die Arbeit mit Ihren Daten über das Dateisystem und den Objektspeicher.

Azure Data Factory (ADF) ist ein vollständig verwalteter, cloudbasierter Datenintegrationsdienst. Mithilfe dieses Diensts können Sie den Lake mit Daten aus zahlreichen lokalen und cloudbasierten Datenspeichern füllen und Zeit beim Erstellen von Analyselösungen sparen. Eine ausführliche Liste der unterstützten Connectors finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory bietet eine Lösung zur horizontalen Skalierung und Verschiebung verwalteter Daten. Aufgrund der horizontal skalierbaren Architektur von ADF können Daten mit hohem Durchsatz erfasst werden. Weitere Informationen finden Sie unter [Leistung der Kopieraktivität](copy-activity-performance.md).

In diesem Artikel erfahren Sie, wie Sie das Tool zum Kopieren von Daten in Data Factory zum Laden von Daten aus _Amazon Web Services S3_ in _Azure Data Lake Storage Gen2_ verwenden. Sie können ähnliche Schritte zum Kopieren von Daten aus anderen Typen von Datenspeichern ausführen.

>[!TIP]
>Informationen zum Kopieren von Daten aus Azure Data Lake Storage Gen1 nach Gen2 finden Sie in [dieser exemplarischen Vorgehensweise](load-azure-data-lake-storage-gen2-from-gen1.md).

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement: Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Azure Storage-Konto mit aktiviertem Data Lake Storage Gen2: [Erstellen Sie ein Speicherkonto](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM), wenn Sie noch keines besitzen.
* AWS-Konto mit einem S3-Bereich, der Daten enthält: Dieser Artikel zeigt, wie Daten aus Amazon S3 kopiert werden. Sie können andere Datenspeicher verwenden, indem Sie ähnliche Schritte ausführen.

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Wählen Sie im Menü auf der linken Seite **Ressource erstellen** > **Daten + Analysen** > **Data Factory** aus:
   
   ![Auswählen von „Data Factory“ im Bereich „Neu“](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Geben Sie auf der Seite **Neue Data Factory** die in der folgenden Abbildung gezeigten Werte für die Felder an: 
      
   ![Seite „Neue Data Factory“](./media/load-azure-data-lake-storage-gen2//new-azure-data-factory.png)
 
    * **Name**: Geben Sie einen global eindeutigen Namen für die Azure Data Factory ein. Wenn die Fehlermeldung „Data Factory mit dem Namen \"LoadADLSDemo\" ist nicht verfügbar“ angezeigt wird, geben Sie einen anderen Namen für die Data Factory ein. Sie können beispielsweise den Namen _**IhrName**_ **ADFTutorialDataFactory** verwenden. Versuchen Sie erneut, die Data Factory zu erstellen. Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](naming-rules.md).
    * **Abonnement**: Wählen Sie Ihr Azure-Abonnement aus, in dem die Data Factory erstellt werden soll. 
    * **Ressourcengruppe**: Wählen Sie eine vorhandene Ressourcengruppe aus der Dropdownliste aus, oder wählen Sie die Option **Neu erstellen** aus, und geben Sie dann den Namen einer Ressourcengruppe ein. Weitere Informationen über Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/resource-group-overview.md).  
    * **Version**: Wählen Sie **V2** aus.
    * **Standort**: Wählen Sie den Standort für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Die von der Data Factory verwendeten Datenspeicher können sich an anderen Standorten bzw. in anderen Regionen befinden. 

3. Klicken Sie auf **Erstellen**.
4. Nach Abschluss der Erstellung navigieren Sie zu Ihrer Data Factory. Die Startseite **Data Factory** wird wie in der folgenden Abbildung dargestellt angezeigt: 
   
   ![Data Factory-Startseite](./media/load-azure-data-lake-storage-gen2/data-factory-home-page.png)

   Wählen Sie die Kachel **Erstellen und überwachen** aus, um die Datenintegrationsanwendung auf einer separaten Registerkarte zu starten.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Laden von Daten in Azure Data Lake Storage Gen2

1. Wählen Sie auf der Seite **Erste Schritte** die Kachel **Daten kopieren** aus, um das Tool zum Kopieren von Daten zu starten: 

   ![Kachel für das Tool zum Kopieren von Daten](./media/load-azure-data-lake-storage-gen2/copy-data-tool-tile.png)
2. Geben Sie auf der Seite **Eigenschaften** im Feld **Aufgabenname** den Namen **CopyFromAmazonS3ToADLS** ein, und klicken Sie dann auf **Weiter**:

    ![Eigenschaftenseite](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. Klicken Sie auf der Seite **Quelldatenspeicher** auf **+ Neue Verbindung erstellen**:

    ![Seite „Quelldatenspeicher“](./media/load-azure-data-lake-storage-gen2/source-data-store-page.png)
    
    Wählen Sie im Connectorkatalog **Amazon S3** aus, und klicken Sie auf **Weiter**.
    
    ![Seite „Quelldatenspeicher“ für S3](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. Führen Sie auf der Seite **Amazon S3-Verbindung angeben** die folgenden Schritte aus:

   1. Geben Sie den Wert für die **Zugriffsschlüssel-ID** an.
   2. Geben Sie den Wert für den **geheimen Zugriffsschlüssel** an.
   3. Klicken Sie auf **Verbindung testen**, um die Einstellungen zu überprüfen, und wählen Sie dann **Fertig stellen** aus.
   4. Eine neue Verbindung wird erstellt. Klicken Sie auf **Weiter**.
   
      ![Angeben des Amazon S3-Kontos](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
      
5. Navigieren Sie auf der Seite **Eingabedatei oder -ordner auswählen** zu dem Ordner und der Datei, die Sie kopieren möchten. Wählen Sie den Ordner/die Datei aus, und klicken Sie auf **Auswählen**:

    ![Auswählen der Eingabedatei bzw. des Eingabeordners](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Geben Sie das Kopierverhalten an, indem Sie die Optionen **Dateien rekursiv kopieren** und **Binärkopie** aktivieren. Klicken Sie auf **Weiter**:

    ![Angeben des Ausgabeordners](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. Klicken Sie auf der Seite **Zieldatenspeicher** auf **+ Neue Verbindung erstellen**, und wählen Sie anschließend **Azure Data Lake Storage Gen2** und dann **Weiter** aus:

    ![Seite „Zieldatenspeicher“](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. Führen Sie auf der Seite **Specify Azure Data Lake Storage connection** (Azure Data Lake Storage-Verbindung angeben) die folgenden Schritte aus:

   1. Wählen Sie in der Dropdownliste „Speicherkontoname“ das Data Lake Storage Gen2-fähige Konto aus.
   2. Wählen Sie **Fertig stellen** aus, um die Verbindung zu erstellen. Klicken Sie anschließend auf **Weiter**.
   
   ![Angeben eines Azure Data Lake Storage Gen2-Kontos](./media/load-azure-data-lake-storage-gen2/specify-adls.png)

9. Geben Sie auf der Seite **Choose the output file or folder** (Ausgabedatei oder -ordner auswählen) die Zeichenfolge **copyfroms3** als Name für den Ausgabeordner ein, und klicken Sie dann auf **Weiter**. Die ADF erstellt das entsprechende ADLS Gen2-Dateisystem und die Unterordner während des Kopierens, wenn diese noch nicht existieren.

    ![Angeben des Ausgabeordners](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. Klicken Sie auf der Seite **Einstellungen** auf **Weiter**, um die Standardeinstellungen zu verwenden:

    ![Seite "Einstellungen"](./media/load-azure-data-lake-storage-gen2/copy-settings.png)
11. Überprüfen Sie auf der Seite **Zusammenfassung** die Einstellungen, und klicken Sie dann auf **Weiter**:

    ![Seite „Zusammenfassung“](./media/load-azure-data-lake-storage-gen2/copy-summary.png)
12. Klicken Sie auf der Seite **Bereitstellung** auf **Überwachen**, um die Pipeline zu überwachen:

    ![Bereitstellungsseite](./media/load-azure-data-lake-storage-gen2/deployment-page.png)
13. Beachten Sie, dass die Registerkarte **Überwachen** auf der linken Seite automatisch ausgewählt ist. In der Spalte **Aktionen** werden Links zum Anzeigen von Aktivitätsausführungsdetails und zum erneuten Ausführen der Pipeline angezeigt:

    ![Überwachen der Pipelineausführungen](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Klicken Sie in der Spalte **Aktionen** auf den Link **Aktivitätsausführungen anzeigen**, um mit der Pipelineausführung verknüpfte Aktivitätsausführungen anzuzeigen. Da die Pipeline nur eine Aktivität (Copy-Aktivität) enthält, wird nur ein Eintrag angezeigt. Klicken Sie oben auf den Link **Pipelines**, um zurück zur Ansicht mit den Pipelineausführungen zu wechseln. Klicken Sie zum Aktualisieren der Liste auf **Aktualisieren**. 

    ![Überwachung der Aktivitätsausführungen](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)

15. Zum Überwachen der Ausführungsdetails jeder Kopieraktivität klicken Sie in der Aktivitätsüberwachungsansicht unter **Aktionen** auf den Link **Details** (Brillensymbol). Sie können Details wie die Menge der Daten, die aus der Quelle in die Senke kopiert wurden, den Datendurchsatz, die Ausführungsschritte mit entsprechender Dauer sowie die verwendeten Konfigurationen überwachen:

    ![Überwachen der Details zur Aktivitätsausführung](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

16. Stellen Sie sicher, dass die Daten in Ihr Data Lake Storage Gen2-Konto kopiert werden.

## <a name="next-steps"></a>Nächste Schritte

* [Kopieraktivität – Übersicht](copy-activity-overview.md)
* [Azure Data Lake Storage Gen2-Connector](connector-azure-data-lake-storage.md)

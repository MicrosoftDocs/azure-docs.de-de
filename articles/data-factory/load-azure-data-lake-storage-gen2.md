---
title: Laden von Daten in Azure Data Lake Storage Gen2
description: Kopieren von Daten in Azure Data Lake Storage Gen2 mithilfe von Azure Data Factory
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/09/2020
ms.openlocfilehash: 61e34d41d9f7a60b6ad74e12331864e6ba08e4f5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370800"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Laden von Daten in Azure Data Lake Storage Gen2 mit Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

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

1. Wählen Sie im Menü auf der linken Seite **Ressource erstellen** > **Integration** > **Data Factory** aus:
   
   ![Auswählen von „Data Factory“ im Bereich „Neu“](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Geben Sie auf der Seite **Neue Data Factory** Werte für folgende Felder an:
 
    * **Name**: Geben Sie einen global eindeutigen Namen für die Azure Data Factory ein. Wenn die Fehlermeldung „Data Factory mit dem Namen *YourDataFactoryName* ist nicht verfügbar“ angezeigt wird, geben Sie einen anderen Namen für die Data Factory ein. Sie können beispielsweise den Namen _**IhrName**_**ADFTutorialDataFactory** verwenden. Versuchen Sie erneut, die Data Factory zu erstellen. Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](naming-rules.md).
    * **Abonnement**: Wählen Sie Ihr Azure-Abonnement aus, in dem die Data Factory erstellt werden soll. 
    * **Ressourcengruppe**: Wählen Sie eine vorhandene Ressourcengruppe aus der Dropdownliste aus, oder wählen Sie die Option **Neu erstellen** aus, und geben Sie dann den Namen einer Ressourcengruppe ein. Weitere Informationen über Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/overview.md).  
    * **Version**: Wählen Sie **V2** aus.
    * **Standort**: Wählen Sie den Standort für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Die von der Data Factory verwendeten Datenspeicher können sich an anderen Standorten bzw. in anderen Regionen befinden. 

3. Klicken Sie auf **Erstellen**.

4. Nach Abschluss der Erstellung navigieren Sie zu Ihrer Data Factory. Die Startseite **Data Factory** wird wie in der folgenden Abbildung dargestellt angezeigt: 
   
   ![Data Factory-Startseite](./media/doc-common-process/data-factory-home-page.png)

   Wählen Sie die Kachel **Erstellen und überwachen** aus, um die Datenintegrationsanwendung auf einer separaten Registerkarte zu starten.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Laden von Daten in Azure Data Lake Storage Gen2

1. Wählen Sie auf der Seite **Erste Schritte** die Kachel **Daten kopieren** aus, um das Tool Daten kopieren zu starten.

2. Geben Sie auf der Seite **Eigenschaften** im Feld **Aufgabenname** den Namen **CopyFromAmazonS3ToADLS** ein, und klicken Sie dann auf **Weiter**.

    ![Eigenschaftenseite](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. Klicken Sie auf der Seite **Quelldatenspeicher** auf **+ Neue Verbindung erstellen**. Wählen Sie im Connectorkatalog **Amazon S3** aus, und klicken Sie auf **Weiter**.
    
    ![Seite „Quelldatenspeicher“ für S3](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. Führen Sie auf der Seite **Neuer verknüpfter Dienst (Amazon S3)** die folgenden Schritte aus:

   1. Geben Sie den Wert für die **Zugriffsschlüssel-ID** an.
   2. Geben Sie den Wert für den **geheimen Zugriffsschlüssel** an.
   3. Klicken Sie auf **Verbindung testen**, um die Einstellungen zu überprüfen, und wählen Sie dann **Erstellen** aus.

      ![Angeben des Amazon S3-Kontos](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
   4. Eine neue AmazonS3-Verbindung wird erstellt. Wählen Sie **Weiter** aus. 

5. Navigieren Sie auf der Seite **Eingabedatei oder -ordner auswählen** zu dem Ordner und der Datei, die Sie kopieren möchten. Wählen Sie den Ordner/die Datei und dann **Auswählen** aus.

    ![Auswählen der Eingabedatei bzw. des Eingabeordners](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Geben Sie das Kopierverhalten an, indem Sie die Optionen **Rekursiv** und **Binärkopie** aktivieren. Wählen Sie **Weiter** aus.

    ![Screenshot der Seite „Eingabedatei oder -ordner auswählen“ mit den auswählbaren Optionen „Binärkopie“ und „Rekursiv“](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. Klicken Sie auf der Seite **Zieldatenspeicher** auf **+ Neue Verbindung erstellen**, und wählen Sie anschließend **Azure Data Lake Storage Gen2** und dann **Weiter** aus.

    ![Seite „Zieldatenspeicher“](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. Führen Sie auf der Seite **Neuer verknüpfter Dienst (Azure Data Lake Storage Gen2)** die folgenden Schritte aus:

   1. wählen Sie in der Dropdownliste „Speicherkontoname“ das Data Lake Storage Gen2-fähige Konto aus.
   2. Wählen Sie **Erstellen** aus, um die Verbindung zu erstellen. Wählen Sie **Weiter** aus.   

        ![Angeben eines Azure Data Lake Storage Gen2-Kontos](./media/load-azure-data-lake-storage-gen2/specify-azure-data-lake-storage.png)

9. Geben Sie auf der Seite **Choose the output file or folder** (Ausgabedatei oder -ordner auswählen) die Zeichenfolge **copyfroms3** als Name für den Ausgabeordner ein, und klicken Sie dann auf **Weiter**. Die ADF erstellt das entsprechende ADLS Gen2-Dateisystem und die Unterordner während des Kopierens, wenn diese noch nicht existieren.

    ![Screenshot des eingegebenen Ordnerpfads](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. Klicken Sie auf der Seite **Einstellungen** auf **Weiter**, um die Standardeinstellungen zu verwenden.

    ![Seite "Einstellungen"](./media/load-azure-data-lake-storage-gen2/copy-settings.png)

11. Überprüfen Sie auf der Seite **Zusammenfassung** die Einstellungen, und klicken Sie dann auf **Weiter**.

    ![Seite „Zusammenfassung“](./media/load-azure-data-lake-storage-gen2/copy-summary.png)

12. Klicken Sie auf der Seite **Bereitstellung** auf **Überwachen**, um die Pipeline (Task) zu überwachen. 
 
13. Wenn die Pipelineausführung erfolgreich abgeschlossen ist, sehen Sie eine Pipelineausführung, die durch einen manuellen Trigger ausgelöst wird. Über die Links unter der Spalte **PIPELINENAME** können Sie Aktivitätsdetails anzeigen und die Pipeline erneut ausführen.

    ![Überwachen der Pipelineausführungen](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Um die der Pipelineausführung zugeordneten Aktivitätsausführungen anzuzeigen, wählen Sie unter der Spalte PIPELINENAME den Link **CopyFromAmazonS3ToADLS** aus. Wenn Sie Details zum Kopiervorgang anzeigen möchten, wählen Sie in der Spalte ACTIVTIY NAME (AKTIVITÄTSNAME) den Link **Details** (das Brillensymbol) aus. Sie können Details wie die Menge der Daten, die aus der Quelle in die Senke kopiert wurden, den Datendurchsatz, die Ausführungsschritte mit entsprechender Dauer sowie die verwendete Konfiguration überwachen.
 
    ![Überwachung der Aktivitätsausführungen](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)
    
    ![Überwachen der Details zur Aktivitätsausführung](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

15. Klicken Sie zum Aktualisieren der Ansicht auf „Aktualisieren“. Wählen Sie oben **Alle Pipelineausführungen** aus, um zurück zur Ansicht mit den Pipelineausführungen zu wechseln.

16. Stellen Sie sicher, dass die Daten in Ihr Data Lake Storage Gen2-Konto kopiert werden.

## <a name="next-steps"></a>Nächste Schritte

* [Kopieraktivität – Übersicht](copy-activity-overview.md)
* [Azure Data Lake Storage Gen2-Connector](connector-azure-data-lake-storage.md)

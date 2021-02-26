---
title: Laden von Daten in Azure Data Lake Storage Gen1
description: Kopieren von Daten in Azure Data Lake Storage Gen1 mithilfe von Azure Data Factory
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/17/2018
ms.openlocfilehash: 2d307a279bee56440f7354ad2c92664fd2af86b9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370766"
---
# <a name="load-data-into-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Laden von Daten in Azure Data Lake Storage Gen1 mit Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

[Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) (bisher als Azure Data Lake Store bezeichnet) ist ein unternehmensweites Repository mit Hyperskalierung für Big Data-Analyseworkloads. Mit Data Lake Storage Gen1 können Sie Daten von beliebiger Größe, Art und Erfassungsgeschwindigkeit erfassen. Die Daten werden an einer einzelnen Stelle zur Durchführung operativer und explorativer Analysen erfasst.

Azure Data Factory ist ein vollständig verwalteter, cloudbasierter Datenintegrationsdienst. Mithilfe dieses Diensts können Sie den Lake mit Daten aus dem vorhandenen System füllen und Zeit beim Erstellen von Analyselösungen sparen.

Azure Data Factory bietet die folgenden Vorteile beim Laden von Daten in Data Lake Storage Gen1:

* **Mühelose Einrichtung**: Intuitiver Assistent mit 5 Schritten. Keine Skripterstellung erforderlich.
* **Unterstützung für umfangreiche Datenspeicher**: Integrierte Unterstützung für umfangreiche lokale und cloudbasierte Datenspeicher. Eine ausführliche Liste finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
* **Sicher und kompatibel**: Daten werden über HTTPS oder ExpressRoute übertragen. Globale Dienste stellen sicher, dass Ihre Daten nie die geografische Grenze verlassen.
* **Hohe Leistung**: Bis zu 1GB/s Datenladegeschwindigkeit in Data Lake Storage Gen1. Weitere Informationen finden Sie unter [Leistung der Kopieraktivität](copy-activity-performance.md).

In diesem Artikel erfahren Sie, wie Sie das Tool zum Kopieren von Daten in Data Factory zum _Laden von Daten aus Amazon S3 in Data Lake Storage Gen1_ verwenden. Sie können ähnliche Schritte zum Kopieren von Daten aus anderen Typen von Datenspeichern ausführen.

> [!NOTE]
> Weitere Informationen finden Sie unter [Kopieren von Daten nach und aus Data Lake Storage Gen1 mithilfe von Azure Data Factory](connector-azure-data-lake-store.md).

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement: Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Data Lake Storage Gen1-Konto: Wenn Sie nicht über ein Data Lake Storage Gen1-Konto verfügen, lesen Sie die Anweisungen unter [Erstellen eines Data Lake Storage Gen1-Kontos](../data-lake-store/data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account).
* Amazon S3: Dieser Artikel zeigt, wie Daten aus Amazon S3 kopiert werden. Sie können andere Datenspeicher verwenden, indem Sie ähnliche Schritte ausführen.

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Klicken Sie im Menü auf der linken Seite auf **Ressource erstellen** > **Analytics** > **Data Factory**:
   
   ![Auswählen von „Data Factory“ im Bereich „Neu“](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Geben Sie auf der Seite **Neue Data Factory** die in der folgenden Abbildung gezeigten Werte für die Felder an: 
      
   ![Seite „Neue Data Factory“](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Name**: Geben Sie einen global eindeutigen Namen für die Azure Data Factory ein. Wenn die Fehlermeldung „Data Factory mit dem Namen \"LoadADLSG1Demo\" ist nicht verfügbar“ angezeigt wird, geben Sie einen anderen Namen für die Data Factory ein. Sie können beispielsweise den Namen _**IhrName**_**ADFTutorialDataFactory** verwenden. Versuchen Sie erneut, die Data Factory zu erstellen. Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](naming-rules.md).
    * **Abonnement**: Wählen Sie Ihr Azure-Abonnement aus, in dem die Data Factory erstellt werden soll. 
    * **Ressourcengruppe**: Wählen Sie eine vorhandene Ressourcengruppe aus der Dropdownliste aus, oder wählen Sie die Option **Neu erstellen** aus, und geben Sie dann den Namen einer Ressourcengruppe ein. Weitere Informationen über Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/overview.md).  
    * **Version**: Wählen Sie **V2** aus.
    * **Standort**: Wählen Sie den Standort für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Die von der Data Factory verwendeten Datenspeicher können sich an anderen Standorten bzw. in anderen Regionen befinden. Diese Datenspeicher umfassen Azure Data Lake Storage Gen1, Azure Storage, Azure SQL-Datenbank usw.

3. Klicken Sie auf **Erstellen**.
4. Nach Abschluss der Erstellung navigieren Sie zu Ihrer Data Factory. Die Startseite **Data Factory** wird wie in der folgenden Abbildung dargestellt angezeigt: 
   
   ![Data Factory-Startseite](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Wählen Sie die Kachel **Erstellen und überwachen** aus, um die Datenintegrationsanwendung auf einer separaten Registerkarte zu starten.

## <a name="load-data-into-data-lake-storage-gen1"></a>Laden von Daten in Data Lake Storage Gen1

1. Wählen Sie auf der Seite **Erste Schritte** die Kachel **Daten kopieren** aus, um das Tool zum Kopieren von Daten zu starten: 

   ![Kachel für das Tool zum Kopieren von Daten](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. Geben Sie auf der Seite **Eigenschaften** im Feld **Aufgabenname** den Namen **CopyFromAmazonS3ToADLS** ein, und klicken Sie dann auf **Weiter**:

    ![Eigenschaftenseite](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. Klicken Sie auf der Seite **Quelldatenspeicher** auf **+ Neue Verbindung erstellen**:

    ![Seite „Quelldatenspeicher“](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
    
    Wählen Sie **Amazon S3** und dann **Weiter** aus.
    
    ![Seite „Quelldatenspeicher“ für S3](./media/load-data-into-azure-data-lake-store/source-data-store-page-s3.png)
    
4. Führen Sie auf der Seite **Amazon S3-Verbindung angeben** die folgenden Schritte aus: 
   1. Geben Sie den Wert für die **Zugriffsschlüssel-ID** an.
   2. Geben Sie den Wert für den **geheimen Zugriffsschlüssel** an.
   3. Wählen Sie **Fertig stellen** aus.
   
      ![Screenshot des Bereichs „Neuer verknüpfter Dienst“ zum Eingeben von Werten](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
   
   4. Es wird eine neue Verbindung angezeigt. Wählen Sie **Weiter** aus.
   
   ![Screenshot der neuen Verbindung](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account-created.png)
   
5. Navigieren Sie auf der Seite **Eingabedatei oder -ordner auswählen** zu dem Ordner und der Datei, die Sie kopieren möchten. Wählen Sie den Ordner/die Datei aus, klicken Sie auf **Auswählen**, und klicken Sie dann auf **Weiter**:

    ![Auswählen der Eingabedatei bzw. des Eingabeordners](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Wählen Sie das Kopierverhalten aus, indem Sie die Optionen **Dateien rekursiv kopieren** und **Binärkopie** (unverändertes Kopieren von Dateien) aktivieren. Klicken Sie auf **Weiter**:

    ![Screenshot der Seite „Eingabedatei oder -ordner auswählen“ mit den Optionen „Dateien rekursiv kopieren“ und „Binärkopie“](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)
    
7. Klicken Sie auf der Seite **Zieldatenspeicher** auf **+ Neue Verbindung erstellen**, und wählen Sie anschließend **Azure Data Lake Storage Gen1** und dann **Weiter** aus:

    ![Seite „Zieldatenspeicher“](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

8. Führen Sie auf der Seite **Neuer verknüpfter Dienst (Azure Data Lake Storage Gen1)** die folgenden Schritte aus: 

   1. Wählen Sie Ihr Data Lake Storage Gen1-Konto für den **Data Lake Store-Kontonamen** aus.
   2. Geben Sie den **Mandanten** an, und wählen Sie „Fertig stellen“ aus.
   3. Wählen Sie **Weiter** aus.
   
   > [!IMPORTANT]
   > In dieser exemplarischen Vorgehensweise verwenden Sie eine verwaltete Identität für Azure-Ressourcen, um Ihr Data Lake Storage Gen1-Konto zu authentifizieren. Achten Sie darauf, dass Sie der verwalteten Dienstidentität (Managed Service Identity, MSI) die entsprechenden Berechtigungen in Data Lake Storage Gen1 erteilen. Befolgen Sie dazu [diese Anweisungen](connector-azure-data-lake-store.md#managed-identity).
   
   ![Angeben des Data Lake Storage Gen1-Kontos](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. Geben Sie auf der Seite **Ausgabedatei oder -ordner auswählen** die Zeichenfolge **copyfroms3** als Name für den Ausgabeordner ein, und klicken Sie dann auf **Weiter**: 

    ![Screenshot des eingegebenen Ordnerpfads](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. Klicken Sie auf der Seite **Einstellungen** auf **Weiter**:

    ![Seite "Einstellungen"](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. Überprüfen Sie auf der Seite **Zusammenfassung** die Einstellungen, und klicken Sie dann auf **Weiter**:

    ![Seite „Zusammenfassung“](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. Klicken Sie auf der Seite **Bereitstellung** auf **Überwachen**, um die Pipeline (Task) zu überwachen:

    ![Bereitstellungsseite](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Beachten Sie, dass die Registerkarte **Überwachen** auf der linken Seite automatisch ausgewählt ist. In der Spalte **Aktionen** werden Links zum Anzeigen von Aktivitätsausführungsdetails und zum erneuten Ausführen der Pipeline angezeigt:

    ![Überwachen der Pipelineausführungen](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Klicken Sie in der Spalte **Aktionen** auf den Link **Aktivitätsausführungen anzeigen**, um mit der Pipelineausführung verknüpfte Aktivitätsausführungen anzuzeigen. Da die Pipeline nur eine Aktivität (Copy-Aktivität) enthält, wird nur ein Eintrag angezeigt. Klicken Sie oben auf den Link **Pipelines**, um zurück zur Ansicht mit den Pipelineausführungen zu wechseln. Klicken Sie zum Aktualisieren der Liste auf **Aktualisieren**. 

    ![Überwachung der Aktivitätsausführungen](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Zum Überwachen der Ausführungsdetails jeder Kopieraktivität wählen Sie in der Aktivitätsüberwachungsansicht unter **Aktionen** den Link **Details** aus. Sie können Details wie die Menge der Daten, die aus der Quelle in die Senke kopiert wurden, den Datendurchsatz, die Ausführungsschritte mit entsprechender Dauer sowie die verwendeten Konfigurationen überwachen:

    ![Überwachen der Details zur Aktivitätsausführung](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Stellen Sie sicher, dass die Daten in Ihr Data Lake Storage Gen1-Konto kopiert werden: 

    ![Überprüfen der Data Lake Storage Gen1-Ausgabe](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie den folgenden Artikel, um mehr über die Unterstützung von Data Lake Storage Gen1 zu erfahren: 

> [!div class="nextstepaction"]
>[Azure Data Lake Storage Gen1-Connector](connector-azure-data-lake-store.md)

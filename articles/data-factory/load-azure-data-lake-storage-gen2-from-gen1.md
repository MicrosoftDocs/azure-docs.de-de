---
title: Kopieren von Daten aus Azure Data Lake Storage Gen1 in Gen2
description: Kopieren von Daten aus Azure Data Lake Storage Gen1 in Gen2 mithilfe von Azure Data Factory
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/13/2019
ms.openlocfilehash: 1b90ff8969edf3ce2886630dc88c50bc9185b85e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370851"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Kopieren von Daten aus Azure Data Lake Storage Gen1 in Gen2 mit Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Lake Storage Gen2 bietet eine Reihe von Funktionen für die Big Data-Analyse, die auf [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) aufbaut. So haben Sie eine Schnittstelle zu Ihren Daten sowohl über das Dateisystem als auch den Objektspeicher.

Wenn Sie zurzeit Azure Data Lake Storage Gen1 verwenden, können Sie Azure Data Lake Storage Gen2 bewerten, indem Sie mithilfe von Azure Data Factory Daten aus Data Lake Storage Gen1 in Gen2 kopieren.

Azure Data Factory ist ein vollständig verwalteter, cloudbasierter Datenintegrationsdienst. Mithilfe dieses Diensts können Sie den Lake mit Daten aus zahlreichen lokalen und cloudbasierten Datenspeichern füllen und Zeit beim Erstellen von Analyselösungen sparen. Eine Liste der unterstützten Connectors finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory bietet eine Lösung zur horizontalen Skalierung und Verschiebung verwalteter Daten. Aufgrund der horizontal skalierbaren Architektur von Data Factory können Daten mit hohem Durchsatz erfasst werden. Weitere Informationen finden Sie unter [Leistung der Kopieraktivität](copy-activity-performance.md).

In diesem Artikel erfahren Sie, wie Sie das Tool zum Kopieren von Daten in Data Factory zum Kopieren von Daten aus Azure Data Lake Storage Gen1 in Azure Data Lake Storage Gen2 verwenden. Sie können ähnliche Schritte zum Kopieren von Daten aus anderen Typen von Datenspeichern ausführen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Sie müssen ein Azure Data Lake Storage Gen1-Konto auswählen, das Daten enthält.
* Azure Storage-Konto mit aktiviertem Data Lake Storage Gen2. [Erstellen Sie ein Speicherkonto](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM), wenn Sie noch keines besitzen.

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Wählen Sie im Menü auf der linken Seite **Ressource erstellen** > **Daten + Analysen** > **Data Factory** aus.
   
   ![Auswählen von „Data Factory“ im Bereich „Neu“](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Geben Sie auf der Seite **Neue Data Factory** die in der folgenden Abbildung gezeigten Werte für die Felder an: 
      
   ![Seite „Neue Data Factory“](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Name**: Geben Sie einen global eindeutigen Namen für die Azure Data Factory ein. Wenn die Fehlermeldung „Data Factory mit dem Namen \"LoadADLSDemo\" ist nicht verfügbar“ angezeigt wird, geben Sie einen anderen Namen für die Data Factory ein. Verwenden Sie beispielsweise den Namen _**IhrName**_ **ADFTutorialDataFactory**. Erstellen Sie die Data Factory erneut. Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](naming-rules.md).
    * **Abonnement**: Wählen Sie Ihr Azure-Abonnement aus, in dem die Data Factory erstellt werden soll. 
    * **Ressourcengruppe**: Wählen Sie in der Dropdownliste eine vorhandene Ressourcengruppe aus. Sie können auch die Option **Neu erstellen** auswählen und den Namen einer Ressourcengruppe eingeben. Weitere Informationen zu Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/overview.md). 
    * **Version**: Wählen Sie **V2** aus.
    * **Standort**: Wählen Sie den Standort für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Die von der Data Factory verwendeten Datenspeicher können sich an anderen Standorten bzw. in anderen Regionen befinden. 

3. Klicken Sie auf **Erstellen**.
4. Nach Abschluss der Erstellung navigieren Sie zu Ihrer Data Factory. Die Startseite **Data Factory** wird wie in der folgenden Abbildung dargestellt angezeigt: 
   
   ![Data Factory-Startseite](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. Wählen Sie die Kachel **Erstellen und überwachen** aus, um die Datenintegrationsanwendung auf einer separaten Registerkarte zu starten.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Laden von Daten in Azure Data Lake Storage Gen2

1. Wählen Sie auf der Seite **Erste Schritte** die Kachel **Daten kopieren** aus, um das Tool zum Kopieren von Daten zu starten. 

   ![Kachel für das Tool zum Kopieren von Daten](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. Geben Sie auf der Seite **Eigenschaften** im Feld **Taskname** den Namen **CopyFromADLSGen1ToGen2** an. Wählen Sie **Weiter** aus.

    ![Eigenschaftenseite](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. Klicken Sie auf der Seite **Quelldatenspeicher** auf **+ Neue Verbindung erstellen**.

    ![Seite „Quelldatenspeicher“](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. Wählen Sie im Connectorkatalog **Azure Data Lake Storage Gen1** aus, und klicken Sie auf **Weiter**.
    
    ![Seite „Quelldatenspeicher von Azure Data Lake Storage Gen1“](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. Führen Sie auf der Seite **Azure Data Lake Storage Gen1-Verbindung angeben** die folgenden Schritte aus:

   a. Wählen Sie Ihr Data Lake Storage Gen1 für den Kontonamen aus, und geben Sie den **Mandanten** an oder überprüfen Sie den angegebenen Mandanten.
  
   b. Wählen Sie **Verbindung testen** aus, um die Einstellungen zu überprüfen. Wählen Sie dann **Fertig stellen** aus.
  
   c. Sie sehen, dass eine neue Verbindung erstellt wurde. Wählen Sie **Weiter** aus.
   
   > [!IMPORTANT]
   > In dieser exemplarischen Vorgehensweise verwenden Sie eine verwaltete Identität für Azure-Ressourcen, um Azure Data Lake Storage Gen1 zu authentifizieren. Um der verwalteten Identität die entsprechenden Berechtigungen in Azure Data Lake Storage Gen1 zu erteilen, befolgen Sie [diese Anweisungen](connector-azure-data-lake-store.md#managed-identity).
   
   ![Angeben eines Azure Data Lake Storage Gen1-Kontos](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. Navigieren Sie auf der Seite **Eingabedatei oder -ordner auswählen** zu dem Ordner und der Datei, die Sie kopieren möchten. Wählen Sie den Ordner oder die Datei aus, und klicken Sie auf **Auswählen**.

    ![Auswählen der Eingabedatei bzw. des Eingabeordners](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. Geben Sie das Kopierverhalten an, indem Sie die Optionen **Dateien rekursiv kopieren** und **Binärkopie** auswählen. Wählen Sie **Weiter** aus.

    ![Screenshot der Auswahl der Eingabedatei oder des Eingabeordners mit den Optionen zum rekursiven Kopieren von Dateien und zum Kopieren von Binärdateien](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. Klicken Sie auf der Seite **Zieldatenspeicher** auf **+ Neue Verbindung erstellen** > **Azure Data Lake Storage Gen2** > **Weiter**.

    ![Seite „Zieldatenspeicher“](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. Führen Sie auf der Seite **Azure Data Lake Storage Gen2-Verbindung angeben** die folgenden Schritte aus:

   a. Wählen Sie in der Dropdownliste **Speicherkontoname** das Data Lake Storage Gen2-fähige Konto aus.
   
   b. Wählen Sie **Fertig stellen** aus, um die Verbindung zu erstellen. Wählen Sie **Weiter** aus.
   
   ![Angeben eines Azure Data Lake Storage Gen2-Kontos](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. Geben Sie auf der Seite **Ausgabedatei oder -ordner auswählen** die Zeichenfolge **copyfromadlsgen1** als Name für den Ausgabeordner ein, und klicken Sie dann auf **Weiter**. Data Factory erstellt während des Kopiervorgangs das entsprechende Dateisystem und die Unterordner für Azure Data Lake Storage Gen2, wenn diese noch nicht vorhanden sind.

    ![Screenshot der Eingabe des Ordnerpfads](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. Klicken Sie auf der Seite **Einstellungen** auf **Weiter**, um die Standardeinstellungen zu verwenden.

12. Überprüfen Sie auf der Seite **Zusammenfassung** die Einstellungen, und klicken Sie dann auf **Weiter**.

    ![Seite „Zusammenfassung“](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. Klicken Sie auf der Seite **Bereitstellung** auf **Überwachen**, um die Pipeline zu überwachen.

    ![Bereitstellungsseite](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. Beachten Sie, dass die Registerkarte **Überwachen** auf der linken Seite automatisch ausgewählt ist. In der Spalte **Aktionen** werden Links zum Anzeigen von Aktivitätsausführungsdetails und zum erneuten Ausführen der Pipeline angezeigt.

    ![Überwachen der Pipelineausführungen](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. Klicken Sie in der Spalte **Aktionen** auf den Link **Aktivitätsausführungen anzeigen**, um mit der Pipelineausführung verknüpfte Aktivitätsausführungen anzuzeigen. Da die Pipeline nur eine Aktivität (Copy-Aktivität) enthält, wird nur ein Eintrag angezeigt. Klicken Sie oben auf den Link **Pipelines**, um zurück zur Ansicht mit den Pipelineausführungen zu wechseln. Klicken Sie zum Aktualisieren der Liste auf **Aktualisieren**. 

    ![Überwachung der Aktivitätsausführungen](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. Zum Überwachen der Ausführungsdetails jeder Kopieraktivität klicken Sie in der Aktivitätsüberwachungsansicht unter **Aktionen** auf den Link **Details** (Brillensymbol). Sie können Details wie die Menge der Daten, die aus der Quelle in die Senke kopiert wurden, den Datendurchsatz, die Ausführungsschritte mit entsprechender Dauer sowie die verwendeten Konfigurationen überwachen.

    ![Überwachen der Details zur Aktivitätsausführung](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. Stellen Sie sicher, dass die Daten in Ihr Azure Data Lake Storage Gen2-Konto kopiert werden.

## <a name="best-practices"></a>Bewährte Methoden

Informationen zum allgemeinen Upgrade von Azure Data Lake Storage Gen1 auf Azure Data Lake Storage Gen2 finden Sie unter [Upgrade von Big Data-Analyselösungen von Azure Data Lake Storage Gen1 auf Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md). In den folgenden Abschnitten werden bewährte Methoden für die Verwendung von Data Factory für ein Datenupgrade von Data Lake Storage Gen1 auf Data Lake Storage Gen2 vorgestellt.

### <a name="data-partition-for-historical-data-copy"></a>Datenpartition für Verlaufsdatenkopie

- Wenn die Gesamtdatengröße in Data Lake Storage Gen1 kleiner als 30 TB ist und die Anzahl der Dateien kleiner als 1 Million ist, können Sie alle Daten in einer einzelnen Kopieraktivitätsausführung kopieren.
- Wenn Sie eine größere Datenmenge zu kopieren haben oder die Flexibilität wünschen, die Datenmigration in Batches zu verwalten und diese jeweils innerhalb eines bestimmten Zeitraums abzuschließen, partitionieren Sie die Daten. Durch die Partitionierung verringert sich auch das Risiko unerwarteter Probleme.

Verwenden Sie einen Proof of Concept, um die End-to-End-Lösung zu überprüfen und den Kopierdurchsatz in Ihrer Umgebung zu testen. Wichtigste Schritte für den Proof of Concept: 

1. Erstellen Sie eine Data Factory-Pipeline mit einer einzelnen Kopieraktivität, um mehrere TBs an Daten von Data Lake Storage Gen1 nach Data Lake Storage Gen2 zu kopieren und eine Basislinie für die Kopierleistung zu erhalten. Beginnen Sie mit [Datenintegrationseinheiten (DIUs)](copy-activity-performance-features.md#data-integration-units) von 128. 
2. Auf der Grundlage des Kopierdurchsatzes, den Sie im ersten Schritt erhalten, berechnen Sie den geschätzten Zeitaufwand für die gesamte Datenmigration. 
3. (Optional) Erstellen Sie eine Steuertabelle, und definieren Sie den Dateifilter, um die zu migrierenden Dateien zu partitionieren. Möglichkeiten zum Partitionieren der Dateien: 

    - Partitionierung nach Ordnername oder Ordnername mit Platzhalterfilter. Diese Methode wird empfohlen.
    - Partitionierung nach Uhrzeit der letzten Änderung einer Datei

### <a name="network-bandwidth-and-storage-io"></a>Netzwerkbandbreite und Speicher-E/A 

Sie können die Parallelität von Data Factory-Kopieraufträgen steuern, die Daten aus Data Lake Storage Gen1 lesen und Daten in Data Lake Storage Gen2 schreiben. Auf diese Weise können Sie die Nutzung dieser Speicher-E/A verwalten, um Auswirkungen auf die normale Arbeit an Data Lake Storage Gen1 während der Migration zu vermeiden.

### <a name="permissions"></a>Berechtigungen 

In Data Factory unterstützt der [Azure Data Lake Storage Gen1-Connector](connector-azure-data-lake-store.md) den Dienstprinzipal und die verwaltete Identität für Azure-Ressourcenauthentifizierungen. Der [Azure Data Lake Storage Gen2-Connector](connector-azure-data-lake-storage.md) unterstützt den Kontoschlüssel, den Dienstprinzipal und die verwaltete Identität für Azure-Ressourcenauthentifizierungen. Damit Data Factory in der Lage ist, nach Bedarf zu navigieren und alle benötigten Dateien oder Zugriffssteuerungslisten (ACLs) zu kopieren, gewähren Sie dem von Ihnen bereitgestellten Konto ausreichende Berechtigungen für den Zugriff, das Lesen oder das Schreiben aller Dateien und das Festlegen von ACLs. Erteilen Sie dem Konto während der Migrationsphase die Administrator- oder Besitzerrolle. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Bewahren von Zugriffssteuerungslisten für Azure Data Lake Storage Gen1

Wenn Sie die Zugriffssteuerungslisten zusammen mit Datendateien beim Upgrade von Data Lake Storage Gen1 auf Gen2 replizieren möchten, finden Sie weitere Informationen unter [Bewahren von Zugriffssteuerungslisten von Data Lake Storage Gen1](connector-azure-data-lake-storage.md#preserve-acls). 

### <a name="incremental-copy"></a>Inkrementelles Kopieren 

Sie können mehrere Methoden verwenden, um nur die neuen oder aktualisierten Dateien aus Data Lake Storage Gen1 zu laden:

- Laden Sie neue oder aktualisierte Dateien nach zeitpartitioniertem Ordner- oder Dateinamen. Ein Beispiel hierfür ist „/2019/05/13/*“.
- Laden Sie neue oder aktualisierte Dateien nach „LastModifiedDate“.
- Identifizieren Sie neue oder aktualisierte Dateien durch ein Tool bzw. eine Lösung von Drittanbietern. Übergeben Sie dann den Datei- oder Ordnernamen per Parameter, Tabelle oder Datei an die Data Factory-Pipeline. 

Die richtige Häufigkeit für das inkrementelle Laden hängt von der Gesamtzahl der Dateien in Azure Data Lake Storage Gen1 und dem Volumen der neuen oder aktualisierten Dateien ab, die jedes Mal geladen werden müssen. 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Kopieraktivität – Übersicht](copy-activity-overview.md)
> [Azure Data Lake Storage Gen1-Connector](connector-azure-data-lake-store.md)
> [Azure Data Lake Storage Gen2-Connector](connector-azure-data-lake-storage.md)
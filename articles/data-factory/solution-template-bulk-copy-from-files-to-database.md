---
title: Massenkopieren von Dateien in eine Datenbank
description: Erfahren Sie, wie Sie eine Lösungsvorlage zum Massenkopieren von Daten aus Azure Data Lake Storage Gen2 nach Azure Synapse Analytics/Azure SQL-Datenbank verwenden.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 5f68c7b33a26bc8c01f3f413943351ff8d61b380
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376120"
---
# <a name="bulk-copy-from-files-to-database"></a>Massenkopieren von Dateien in eine Datenbank

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel wird eine Lösungsvorlage beschrieben, die Sie zum Massenkopieren von Daten aus Azure Data Lake Storage Gen2 nach Azure Synapse Analytics/Azure SQL-Datenbank verwenden können.

## <a name="about-this-solution-template"></a>Informationen zu dieser Lösungsvorlage

Mit dieser Vorlage werden Dateien aus der Azure Data Lake Storage Gen2-Quelle abgerufen. Anschließend wird jede Datei in der Quelle durchlaufen, und die Daten werden in den Zieldatenspeicher kopiert. 

Diese Vorlage unterstützt derzeit nur das Kopieren von Daten im **DelimitedText**-Format. Dateien mit anderen Datenformaten können ebenfalls aus dem Quelldatenspeicher abgerufen werden, sie können jedoch nicht in den Zieldatenspeicher kopiert werden.  

Die Vorlage enthält drei Aktivitäten:
- Die Aktivität **Metadaten abrufen** ruft Dateien aus Azure Data Lake Storage Gen2 ab und übergibt sie an die nachfolgende *ForEach*-Aktivität.
- Die **ForEach**-Aktivität ruft Dateien aus der Aktivität *Metadaten abrufen* ab und durchläuft jede Datei mit der Aktivität *Kopieren*.
- Die Aktivität **Kopieren** befindet sich innerhalb der *ForEach*-Aktivität, damit jede Datei aus dem Quelldatenspeicher in den Zieldatenspeicher kopiert werden kann.

Die Vorlage definiert die folgenden beiden Parameter:
- *SourceContainer* ist der Stammcontainerpfad, in den die Daten in Ihrer Azure Data Lake Storage Gen2-Instanz kopiert werden. 
- *SourceDirectory* ist der Verzeichnispfad im Stammcontainer, in den die Daten aus Ihrer Azure Data Lake Storage Gen2-Instanz kopiert werden.

## <a name="how-to-use-this-solution-template"></a>So verwenden Sie diese Lösungsvorlage

1. Navigieren Sie zur Vorlage **Bulk Copy from Files to Database** (Massenkopieren aus Dateien in eine Datenbank). Stellen Sie eine **neue Verbindung** mit dem Gen2-Quellspeicher her. Beachten Sie, dass „GetMetadataDataset“ und „SourceDataset“ Verweise auf dieselbe Verbindung Ihres Quelldateispeichers sind.

    ![Herstellen einer neuen Verbindung mit dem Quelldatenspeicher](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. Stellen Sie eine **neue Verbindung** mit dem Senkendatenspeicher her, in den Sie Daten kopieren.

    ![Herstellen einer neuen Verbindung mit dem Senkendatenspeicher](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. Klicken Sie auf **Diese Vorlage verwenden**.

    ![„Diese Vorlage verwenden“](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. Sie sehen, dass eine Pipeline erstellt wurde, wie im nachstehenden Beispiel gezeigt wird:

    ![Überprüfen der Pipeline](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > Wenn Sie in **Schritt 2** oben als Datenziel **Azure Synapse Analytics**  ausgewählt haben, müssen Sie eine Verbindung mit Azure Blob Storage für den Stagingprozess eingeben, da dies für Azure Synapse Analytics PolyBase erforderlich ist. Wie im folgenden Screenshot gezeigt, generiert die Vorlage automatisch einen *Speicherpfad* für Ihren Blobspeicher. Überprüfen Sie nach der Pipelineausführung, ob der Container erstellt wurde.
        
    ![PolyBase-Einstellung](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. Klicken Sie auf **Debuggen**, geben Sie die **Parameter** ein, und klicken Sie dann auf **Fertig stellen**.

    ![Auf **Debuggen** klicken](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. Wenn die Pipelineausführung erfolgreich abgeschlossen wurde, werden die Ergebnisse ähnlich wie im nachstehenden Beispiel angezeigt:

    ![Überprüfen des Ergebnisses](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>Nächste Schritte

- [Einführung in den Azure Data Factory-Dienst](introduction.md)
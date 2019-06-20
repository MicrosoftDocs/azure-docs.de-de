---
title: Streamen von Daten aus Stream Analytics in Azure Data Lake Storage Gen1 | Microsoft-Dokumentation
description: Verwenden von Azure Stream Analytics zum Streamen von Daten in Azure Data Lake Storage Gen1
services: data-lake-store,stream-analytics
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: edb58e0b-311f-44b0-a499-04d7e6c07a90
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: twooley
ms.openlocfilehash: d3dbacd58b3bda3fbf8ee8ad5f175eccc2cb2a24
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60194854"
---
# <a name="stream-data-from-azure-storage-blob-into-azure-data-lake-storage-gen1-using-azure-stream-analytics"></a>Streamen von Daten aus Azure Storage Blob in Azure Data Lake Storage Gen1 mit Azure Stream Analytics
In diesem Artikel erfahren Sie, wie Sie Azure Data Lake Storage Gen1 als Ausgabe für einen Azure Stream Analytics-Auftrag verwenden. Dieser Artikel beschreibt ein einfaches Szenario, bei dem Daten aus einem Azure Storage-Blob (Eingabe) gelesen und in Data Lake Storage Gen1 (Ausgabe) geschrieben werden.

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie mit diesem Tutorial beginnen können, benötigen Sie Folgendes:

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Storage-Konto**. Sie verwenden einen Blobcontainer aus diesem Konto, um Daten für einen Stream Analytics-Auftrag einzugeben. Nehmen Sie für dieses Tutorial an, dass Sie über ein Speicherkonto namens **storageforasa** und in diesem Konto über einen Container namens **storageforasacontainer** verfügen. Nachdem Sie den Container erstellt haben, laden Sie eine Beispieldatendatei in diesen hoch. 
  
* **Ein Data Lake Storage Gen1-Konto.** Befolgen Sie die Anweisungen unter [Erste Schritte mit Azure Data Lake Storage Gen1 über das Azure-Portal](data-lake-store-get-started-portal.md). Nehmen wir an, dass Sie über ein Data Lake Storage Gen1-Konto namens **myadlsg1** verfügen. 

## <a name="create-a-stream-analytics-job"></a>Erstellen eines Stream Analytics-Auftrags
Erstellen Sie zunächst einen Stream Analytics-Auftrag, der eine Eingabequelle und ein Ausgabeziel enthält. In diesem Tutorial ist die Quelle ein Azure-Blobcontainer, und das Ziel ist Azure Data Lake Storage Gen1.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.

2. Klicken Sie im linken Bereich auf **Stream Analytics-Aufträge**, und klicken Sie dann auf **Hinzufügen**.

    ![Erstellen eines Stream Analytics-Auftrags](./media/data-lake-store-stream-analytics/create.job.png "Erstellen eines Stream Analytics-Auftrags")

    > [!NOTE]
    > Stellen Sie sicher, dass Sie den Auftrag in der gleichen Region erstellen, in der sich das Speicherkonto befindet, da sonst zusätzliche Kosten für das Verschieben von Daten zwischen Regionen anfallen.
    >

## <a name="create-a-blob-input-for-the-job"></a>Erstellen einer Blobeingabe für den Auftrag

1. Öffnen Sie die Seite für den Stream Analytics-Auftrag, klicken Sie im linken Bereich auf die Registerkarte **Eingaben**, und klicken Sie dann auf **Hinzufügen**.

    ![Hinzufügen einer Eingabe zu Ihrem Auftrag](./media/data-lake-store-stream-analytics/create.input.1.png "Hinzufügen einer Eingabe zu Ihrem Auftrag")

2. Geben Sie auf dem Blatt **Neue Eingabe** die folgenden Werte ein.

    ![Hinzufügen einer Eingabe zu Ihrem Auftrag](./media/data-lake-store-stream-analytics/create.input.2.png "Hinzufügen einer Eingabe zu Ihrem Auftrag")

   * Geben Sie unter **Eingabealias** einen eindeutigen Namen für diese Auftragseingabe ein.
   * Wählen Sie als **Quelltyp** die Option **Datenstrom** aus.
   * Wählen Sie als **Quelle** die Option **Blobspeicher** aus.
   * Wählen Sie unter **Abonnement** die Option **Blobspeicher aus aktuellem Abonnement verwenden** aus.
   * Wählen Sie als **Speicherkonto** das Speicherkonto aus, das Sie unter „Voraussetzungen“ erstellt haben. 
   * Wählen Sie als **Container** den Container aus, den Sie im ausgewählten Speicherkonto erstellt haben.
   * Wählen Sie unter **Ereignisserialisierungsformat** die Option **CSV** aus.
   * Wählen Sie als **Trennzeichen** die Option **Tabstopp** aus.
   * Wählen Sie als **Codierung** die Option **UTF-8** aus.

     Klicken Sie auf **Create**. Das Portal fügt die Eingabe hinzu und testet die Verbindung.


## <a name="create-a-data-lake-storage-gen1-output-for-the-job"></a>Erstellen einer Data Lake Storage Gen1-Ausgabe für den Auftrag

1. Öffnen Sie die Seite für den Stream Analytics-Auftrag, klicken Sie auf die Registerkarte **Ausgaben**, klicken Sie auf **Hinzufügen**, und wählen Sie **Data Lake Storage Gen1** aus.

    ![Hinzufügen einer Ausgabe zu Ihrem Auftrag](./media/data-lake-store-stream-analytics/create.output.1.png "Hinzufügen einer Ausgabe zu Ihrem Auftrag")

2. Geben Sie auf dem Blatt **Neue Ausgabe** die folgenden Werte ein.

    ![Hinzufügen einer Ausgabe zu Ihrem Auftrag](./media/data-lake-store-stream-analytics/create.output.2.png "Hinzufügen einer Ausgabe zu Ihrem Auftrag")

    * Geben Sie unter **Ausgabealias** einen eindeutigen Namen für die Auftragsausgabe ein. Dies ist ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an dieses Data Lake Storage Gen1-Konto weiterzuleiten.
    * Sie werden aufgefordert, den Zugriff auf das Data Lake Storage Gen1-Konto zu autorisieren. Klicken Sie auf **Autorisieren**.

3. Geben Sie auf dem Blatt **Neue Ausgabe** die folgenden Werte ein.

    ![Hinzufügen einer Ausgabe zu Ihrem Auftrag](./media/data-lake-store-stream-analytics/create.output.3.png "Hinzufügen einer Ausgabe zu Ihrem Auftrag")

   * Wählen Sie unter **Kontoname** das bereits erstellte Data Lake Storage Gen1-Konto aus, an das die Auftragsausgabe gesendet werden soll.
   * Geben Sie unter **Pfadpräfixmuster** den Dateipfad ein, in den Ihre Dateien im angegebenen Data Lake Storage Gen1-Konto geschrieben werden.
   * Wenn Sie ein Datumstoken im Pfadpräfix verwendet wird, können Sie das **Datumsformat** auswählen, mit dem Ihre Dateien sortiert werden.
   * Wenn Sie ein Uhrzeittoken im Pfadpräfix verwendet wird, können Sie das **Uhrzeitformat** auswählen, mit dem Ihre Dateien sortiert werden.
   * Wählen Sie unter **Ereignisserialisierungsformat** die Option **CSV** aus.
   * Wählen Sie als **Trennzeichen** die Option **Tabstopp** aus.
   * Wählen Sie als **Codierung** die Option **UTF-8** aus.
    
     Klicken Sie auf **Create**. Das Portal fügt die Ausgabe hinzu und testet die Verbindung.
    
## <a name="run-the-stream-analytics-job"></a>Ausführen des Stream Analytics-Auftrags

1. Um einen Stream Analytics-Auftrag auszuführen, müssen Sie eine Abfrage auf der Registerkarte **Abfrage** ausführen. Für dieses Tutorial können Sie die Beispielabfrage ausführen, indem Sie die Platzhalter durch die Auftragseingabe- und Auftragsausgabealiase ersetzen, wie in der folgenden Bildschirmaufnahme gezeigt.

    ![Ausführen einer Abfrage](./media/data-lake-store-stream-analytics/run.query.png "Ausführen einer Abfrage")

2. Klicken Sie oben auf dem Bildschirm auf **Speichern**, und klicken Sie dann auf der Registerkarte **Übersicht** auf **Starten**. Wählen Sie im Dialogfeld die Option **Benutzerdefinierte Uhrzeit** aus, und legen Sie das aktuelle Datum und die aktuelle Uhrzeit fest.

    ![Festlegen der Auftragszeit](./media/data-lake-store-stream-analytics/run.query.2.png "Festlegen der Auftragszeit")

    Klicken Sie auf **Starten**, um den Auftrag zu starten. Es kann einige Minuten dauern, bis der Auftrag startet.

3. Um den Auftrag zum Abrufen der Daten aus dem Blob auszulösen, kopieren Sie eine Beispieldatendatei in den Blobcontainer. Eine Beispieldatendatei finden Sie im [Azure Data Lake Git-Repository](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). Für dieses Tutorial kopieren wir die Datei **vehicle1_09142014.csv**. Sie können verschiedene Clients verwenden, z.B. den [Azure Storage-Explorer](https://storageexplorer.com/), um Daten in einen Blobcontainer hochzuladen.

4. Auf der Registerkarte **Übersicht** können Sie unter **Überwachung** sehen, wie die Daten verarbeitet wurden.

    ![Überwachen des Auftrags](./media/data-lake-store-stream-analytics/run.query.3.png "Überwachen des Auftrags")

5. Abschließend können Sie überprüfen, ob die Ausgabedaten des Auftrags im Data Lake Storage Gen1-Konto verfügbar sind. 

    ![Überprüfen der Ausgabe](./media/data-lake-store-stream-analytics/run.query.4.png "Überprüfen der Ausgabe")

    Beachten Sie, dass die Ausgabe im Daten-Explorer-Fenster gemäß der Angabe in den Data Lake Storage Gen1-Ausgabeeinstellungen (`streamanalytics/job/output/{date}/{time}`) in einen Ordnerpfad geschrieben wird.  

## <a name="see-also"></a>Weitere Informationen
* [Erstellen eines HDInsight-Clusters für die Verwendung von Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

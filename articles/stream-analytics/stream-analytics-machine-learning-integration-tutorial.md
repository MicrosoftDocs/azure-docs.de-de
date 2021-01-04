---
title: Azure Stream Analytics-Integration in Azure Machine Learning Studio (klassisch)
description: Dieser Artikel beschreibt, wie Sie mithilfe einer benutzerdefinierten Funktion schnell einen einfachen Azure Stream Analytics-Auftrag mit Azure Machine Learning Studio-Integration (klassisch) einrichten.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/12/2020
ms.custom: seodec18
ms.openlocfilehash: 9b08b59090d9dd23405f8a0ba86ce608e3a64902
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011386"
---
# <a name="do-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic"></a>Durchführen von Stimmungsanalysen mit Azure Stream Analytics und Azure Machine Learning Studio (Classic)

Dieser Artikel zeigt, wie Sie einen einfachen Azure Stream Analytics-Auftrag einrichten, der Azure Machine Learning Studio (Classic) für die Stimmungsanalyse verwendet. Sie nutzen ein Stimmungsanalysemodell für Studio (klassisch) aus dem Cortana Intelligence-Katalog, um Streamingtextdaten zu analysieren und den Stimmungswert zu ermitteln.

> [!TIP]
> Es wird ausdrücklich empfohlen, [UDFs für Azure Machine Learning](machine-learning-udf.md) anstelle von UDFs für Azure Machine Learning Studio (Classic) zu verwenden, um Leistung und Zuverlässigkeit zu verbessern.

Sie können das in diesem Artikel Gelernte u.a. in folgenden Szenarien anwenden:

* Analysieren der Stimmungslage in Datenströmen von Twitter in Echtzeit
* Analysieren von Datensätzen von Kundenchats mit Supportmitarbeitern
* Bewerten von Kommentaren in Foren sowie zu Blogs und Videos
* Für viele andere Szenarien zur Vorhersage in Echtzeit

Der von Ihnen erstellte Stream Analytics-Auftrag wendet das Stimmungsanalysemodell als benutzerdefinierte Funktion (User-Defined Function, UDF) auf die Beispieltextdaten aus dem Blobspeicher an. Die Ausgabe (das Ergebnis der Standpunktanalyse) wird in denselben Blobspeicher, aber in eine andere CSV-Datei geschrieben. 

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie zunächst sicher, dass Sie über Folgendes verfügen:

* Ein aktives Azure-Abonnement.

* Eine CSV-Datei mit enthaltenen Twitter-Daten. Sie können eine Beispieldatei von [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv) herunterladen oder selbst eine Datei erstellen. In einem realen Szenario erhalten Sie die Daten direkt aus einem Datenstrom von Twitter.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Erstellen eines Speichercontainers und Hochladen der CSV-Eingabedatei

In diesem Schritt laden Sie eine CSV-Datei in Ihren Speichercontainer hoch.

1. Wählen Sie im Azure-Portal **Ressource erstellen** > **Storage** > **Speicherkonto** aus.

2. Füllen Sie die Registerkarte *Grundlagen* mit den folgenden Details aus, und übernehmen Sie für die restlichen Felder die Standardwerte:

   |Feld  |Wert  |
   |---------|---------|
   |Subscription|Wählen Sie Ihr Abonnement aus.|
   |Resource group|Wählen Sie Ihre Ressourcengruppe aus.|
   |Speicherkontoname|Geben Sie einen Namen für Ihr Speicherkonto ein. Der Name muss innerhalb von Azure eindeutig sein.|
   |Position|Wählen Sie einen Standort aus. Alle Ressourcen sollten denselben Standort verwenden.|
   |Kontoart|BlobStorage|

   ![Speicherkontodetails angeben](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

3. Klicken Sie auf **Überprüfen + erstellen**. Wählen Sie zum Bereitstellen Ihres Speicherkontos **Erstellen** aus.

4. Wenn die Bereitstellung abgeschlossen ist, navigieren Sie zu Ihrem Speicherkonto. Wählen Sie unter **Blob-Dienst** die Option **Container** aus. Wählen Sie dann **+ Container** aus, um einen neuen Container zu erstellen.

   ![Erstellen eines Blobspeichercontainers für Eingabe](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Geben Sie einen Namen für den Container an, und vergewissern Sie sich, dass **Öffentliche Zugriffsebene** auf **Privat** festgelegt ist. Wählen Sie **Erstellen**, wenn Sie fertig sind.

   ![Details zum Blobcontainer angeben](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. Navigieren Sie zu dem neu erstellten Container, und wählen Sie **Hochladen** aus. Laden Sie die Datei **sampleinput.csv** hoch, die Sie zuvor heruntergeladen haben.

   ![Schaltfläche „Hochladen“ für einen Container](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Hinzufügen des Stimmungsanalysemodells aus dem Cortana Analytics-Katalog

Nachdem sich die Beispieldaten in einem Blob befinden, können Sie das Standpunktanalysemodell im Cortana Intelligence-Katalog aktivieren.

1. Navigieren Sie im Cortana Intelligence-Katalog zur Seite [Predictive Experiment – Mini Twitter sentiment analysis](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1).  

2. Wählen Sie **In Studio öffnen (klassisch)** aus.  
   
   ![Stream Analytics Azure Machine Learning Studio (klassisch), Studio (klassisch) öffnen](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Melden Sie sich an, um zum Arbeitsbereich zu gelangen. Wählen Sie einen Standort aus.

4. Wählen Sie unten auf der Seite **Ausführen** aus. Der Prozess wird ausgeführt und dauert etwa Minute.

   ![Experiment in Studio (klassisch) ausführen](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Nachdem der Prozess erfolgreich ausgeführt wurde, wählen Sie unten auf der Seite **Webdienst bereitstellen** aus.

   ![Experiment als Webdienst in Studio (klassisch) bereitstellen](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Um zu überprüfen, ob das Modell zur Stimmungsanalyse bereit ist, wählen Sie die Schaltfläche **Testen** aus. Geben Sie eine Texteingabe wie „I love Microsoft“ ein.

   ![Experiment in Studio (klassisch) testen](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

   Wenn die Analyse funktioniert, wird ein Ergebnis ähnlich dem folgenden Text angezeigt:

   ![Testergebnisse in Studio (klassisch)](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. Wählen Sie in der Spalte **Apps** den Link **Excel 2010 or earlier workbook** (Arbeitsmappe für Excel 2010 oder früher) aus, um eine Excel-Arbeitsmappe herunterzuladen. Die Arbeitsmappe enthält den API-Schlüssel und die URL, die Sie später zum Einrichten des Stream Analytics-Auftrags benötigen.

    ![Stream Analytics Azure Machine Learning Studio (klassisch), Schnellübersicht](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-studio-classic-model"></a>Erstellen eines Stream Analytics-Auftrags, der das Studio-Modell (klassisch) verwendet

Sie können jetzt einen Stream Analytics-Auftrag erstellen, der die Beispiel-Tweets aus der CSV-Datei im Blobspeicher liest.

### <a name="create-the-job"></a>Erstellen des Auftrags

Wechseln Sie zum [Azure-Portal](https://portal.azure.com), und erstellen Sie einen Stream Analytics-Auftrag. Wenn Sie mit diesem Prozess nicht vertraut sind, finden Sie Informationen hierzu unter [Erstellen eines Stream Analytics-Auftrags mithilfe des Azure-Portals](stream-analytics-quick-create-portal.md).

### <a name="configure-the-job-input"></a>Konfigurieren einer Auftragseingabe

Der Auftrag ruft seine Eingabe aus der CSV-Datei ab, die Sie zuvor in den Blobspeicher hochgeladen haben.

1. Navigieren Sie zum Stream Analytics-Auftrag. Wählen Sie unter **Auftragstopologie** die Option **Eingaben** aus. Wählen Sie **Datenstromeingabe hinzufügen** >**Blobspeicher** aus.

2. Füllen Sie die Details von **Blobspeicher** mit den folgenden Werte aus:

   |Feld  |Wert  |
   |---------|---------|
   |Eingabealias|Geben Sie Ihrer Eingabe einen Namen. Merken Sie sich diesen Alias, wenn Sie Ihre Abfrage schreiben.|
   |Subscription|Wählen Sie Ihr Abonnement aus.|
   |Speicherkonto|Wählen Sie das Speicherkonto aus, das Sie im vorherigen Schritt erstellt haben.|
   |Container|Wählen Sie den Container aus, den Sie im vorherigen Schritt erstellt haben.|
   |Ereignisserialisierungsformat|CSV|

3. Wählen Sie **Speichern** aus.

### <a name="configure-the-job-output"></a>Konfigurieren der Auftragsausgabe

Der Auftrag sendet die Ergebnisse an denselben Blobspeicher, aus dem er die Eingaben abruft.

1. Navigieren Sie zum Stream Analytics-Auftrag. Wählen Sie unter **Auftragstopologie** die Option **Ausgaben** aus. Wählen Sie **Hinzufügen** > **Blobspeicher** aus.

2. Füllen Sie das Formular **Blobspeicher** mit folgenden Werten aus:

   |Feld  |Wert  |
   |---------|---------|
   |Eingabealias|Geben Sie Ihrer Eingabe einen Namen. Merken Sie sich diesen Alias, wenn Sie Ihre Abfrage schreiben.|
   |Subscription|Wählen Sie Ihr Abonnement aus.|
   |Speicherkonto|Wählen Sie das Speicherkonto aus, das Sie im vorherigen Schritt erstellt haben.|
   |Container|Wählen Sie den Container aus, den Sie im vorherigen Schritt erstellt haben.|
   |Ereignisserialisierungsformat|CSV|

3. Wählen Sie **Speichern** aus.

### <a name="add-the-studio-classic-function"></a>Hinzufügen der Studio (klassisch)-Funktion

Sie haben bereits ein Studio-Modell (klassisch) in einem Webdienst veröffentlicht. In diesem Szenario sendet der Stream Analysis-Auftrag während seiner Ausführung jeden Beispiel-Tweet aus der Eingabe für die Standpunktanalyse an den Webdienst. Der Studio-Webdienst (klassisch) gibt eine Stimmung (`positive`, `neutral` oder `negative`) und eine Wahrscheinlichkeit, ob der Tweet positiv ist, zurück.

In diesem Abschnitt definieren Sie eine Funktion im Stream Analysis-Auftrag. Die Funktion kann aufgerufen werden, um einen Tweet an den Webdienst zu senden und die Antwort abzurufen.

1. Sie benötigen dazu die Webdienst-URL und den API-Schlüssel, die Sie zuvor in der Excel-Arbeitsmappe heruntergeladen haben.

2. Navigieren Sie zum Stream Analytics-Auftrag. Wählen Sie dann **Functions** >  **+ Hinzufügen** > **Azure ML Studio**  aus.

3. Fügen Sie das Formular **Azure Machine Learning-Funktion** mit folgenden Werte aus:

   |Feld  |Wert  |
   |---------|---------|
   | Funktionsalias | Verwenden Sie den Namen `sentiment`, und wählen Sie **Einstellungen für Azure Machine Learning-Funktion manuell angeben**. Es wird eine Option zum Eingeben der URL und des Schlüssels angezeigt.      |
   | URL| Fügen Sie die Webdienst-URL ein.|
   |Schlüssel | Fügen Sie den API-Schlüssel ein. |

4. Wählen Sie **Speichern** aus.

### <a name="create-a-query-to-transform-the-data"></a>Erstellen einer Abfrage zum Transformieren der Daten

Stream Analytics verwendet eine deklarative, SQL-basierte Abfrage zur Analyse und Verarbeitung der Eingabe. In diesem Abschnitt erstellen Sie eine Abfrage, die jeden Tweet aus der Eingabe liest und dann die Studio (klassisch)-Funktion aufruft, um die Standpunktanalyse durchzuführen. Die Abfrage sendet das Ergebnis dann an die Ausgabe, die Sie definiert haben (Blobspeicher).

1. Kehren Sie zur Übersicht des Stream Analytics-Auftrags zurück.

2. Wählen Sie unter **Auftragstopologie** die Option **Abfrage** aus.

3. Geben Sie die folgende Abfrage ein:

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM <input>  
    )  

    SELECT text, result.[Score]  
    INTO <output>
    FROM sentiment  
    ```    

   Die Abfrage ruft die Funktion auf, die Sie zuvor erstellt haben (`sentiment`), um für jeden Tweet in der Eingabe eine Stimmungsanalyse durchzuführen.

4. Wählen Sie **Speichern** aus, um die Abfrage zu speichern.

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Starten des Stream Analytics-Auftrags und Überprüfen der Ausgabe

Sie können den Stream Analytics-Auftrag jetzt starten.

### <a name="start-the-job"></a>Starten des Auftrags

1. Kehren Sie zur Übersicht des Stream Analytics-Auftrags zurück.

2. Wählen Sie im oberen Bereich der Seite **Starten** aus.

3. Wählen Sie in **Auftrag starten** die Option **Benutzerdefiniert** aus, und wählen Sie dann einen Zeitpunkt aus, der vor dem Zeitpunkt des Hochladens der CSV-Datei in den Blobspeicher liegt. Klicken Sie auf **Starten**, wenn Sie fertig sind.  

### <a name="check-the-output"></a>Überprüfen der Ausgabe

1. Lassen Sie den Auftrag einige Minuten laufen, bis Aktivitäten im Feld **Überwachung** angezeigt werden.

2. Wenn Sie über ein Tool verfügen, mit dem Sie normalerweise den Inhalt von Blobspeicher untersuchen, verwenden Sie dieses Tool, um den Container zu untersuchen. Führen Sie andernfalls die folgenden Schritte im Azure-Portal aus:

      1. Suchen Sie im Azure-Portal Ihr Speicherkonto, und suchen Sie in diesem Konto den Container. Sie sehen zwei Dateien im Container: die Datei mit den Beispiel-Tweets und eine CSV-Datei, die vom Stream Analytics-Auftrags generiert wurde.
      2. Klicken Sie mit der rechten Maustaste auf die generierte Datei, und wählen Sie dann **Herunterladen** aus.

3. Öffnen Sie die generierte CSV-Datei. Die Anzeige sollte in etwa wie im folgenden Beispiel aussehen:  

   ![Stream Analytics Azure Machine Learning Studio (klassisch), CSV-Ansicht](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

### <a name="view-metrics"></a>Anzeigen von Metriken

Sie können auch auf die Studio (klassisch)-Funktion bezogene Metriken anzeigen. Die folgenden funktionenbezogenen Metriken werden im Feld **Überwachung** der Auftragsübersicht angezeigt:

* **Funktionsanforderungen** gibt die Anzahl von Anforderungen an, die an den Studio-Webdienst (klassisch) gesendet werden.  
* **Funktionsereignisse** gibt die Anzahl von Ereignissen in der Anforderung an. Standardmäßig enthält jede Anforderung an einen Studio-Webdienst (klassisch) bis zu 1000 Ereignisse.

## <a name="next-steps"></a>Nächste Schritte

* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Stream Analytics Query Language Reference (in englischer Sprache)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Integrieren von REST-API und Machine Learning Studio (klassisch)](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](/rest/api/streamanalytics/)
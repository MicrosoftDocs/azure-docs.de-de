---
title: Azure Stream Analytics-Integration in Azure Machine Learning
description: Dieser Artikel beschreibt, wie Sie mithilfe einer benutzerdefinierten Funktion schnell einen einfachen Azure Stream Analytics-Auftrag mit Azure Machine Learning-Integration einrichten.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: b06fec8ab726f48e1937bae4cfbdbd9842788d0a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61480648"
---
# <a name="performing-sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Durchführen von Standpunktanalysen mit Azure Stream Analytics und Azure Machine Learning
Dieser Artikel beschreibt, wie Sie schnell einen einfachen Azure Stream Analytics-Auftrag mit Azure Machine Learning-Integration einrichten. Sie nutzen ein Stimmungsanalysemodell für Machine Learning aus dem Cortana Intelligence-Katalog, um Streamingtextdaten zu analysieren und den Stimmungswert in Echtzeit zu ermitteln. Über die Cortana Intelligence Suite können Sie diese Aufgabe ausführen, ohne sich um die Komplexität der Erstellung eines Analysemodells für die Stimmung kümmern zu müssen.

Sie können das in diesem Artikel Gelernte u.a. in folgenden Szenarien anwenden:

* Analysieren der Stimmungslage in Datenströmen von Twitter in Echtzeit
* Analysieren von Datensätzen von Kundenchats mit Supportmitarbeitern
* Bewerten von Kommentaren in Foren sowie zu Blogs und Videos 
* Für viele andere Szenarien zur Vorhersage in Echtzeit

In einem realen Szenario erhalten Sie die Daten direkt aus einem Datenstrom von Twitter. Um das Tutorial zu vereinfachen, wurde es so verfasst, dass der Stream Analytics-Auftrag Tweets aus einer CSV-Datei in Azure Blob Storage abruft. Sie können eine eigene CSV-Datei erstellen oder eine CSV-Beispieldatei verwenden, wie in der folgenden Abbildung gezeigt:

![In einer CSV-Datei gezeigte Beispieltweets](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

Der von Ihnen erstellte Stream Analytics-Auftrag wendet das Stimmungsanalysemodell als benutzerdefinierte Funktion (User-Defined Function, UDF) auf die Beispieltextdaten aus dem Blobspeicher an. Die Ausgabe (das Ergebnis der Standpunktanalyse) wird in denselben Blobspeicher, aber in eine andere CSV-Datei geschrieben. 

Die folgende Abbildung veranschaulicht diese Konfiguration. Um ein realistischeres Szenario zu erhalten, können Sie wie bereits erwähnt den Blobspeicher durch Twitter-Streamingdaten aus einer Azure Event Hubs-Eingabe ersetzen. Außerdem können Sie eine [Microsoft Power BI](https://powerbi.microsoft.com/) -Echtzeitvisualisierung der zusammengefassten Stimmung erstellen.    

![Machine Learning-Integration in Stream Analytics – Übersicht](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Voraussetzungen
Stellen Sie zunächst sicher, dass Sie über Folgendes verfügen:

* Ein aktives Azure-Abonnement.
* Eine CSV-Datei mit einigen Daten. Sie können die oben gezeigte Datei aus [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv) herunterladen oder selbst eine Datei erstellen. In diesem Artikel wird davon ausgegangen, dass Sie die Datei aus GitHub verwenden.

Im Allgemeinen führen Sie mit den Aufgaben in diesem Artikel folgende Aktionen aus:

1. Erstellen eines Azure Storage-Kontos und eines Blob Storage-Containers und Hochladen einer CSV-Eingabedatei in den Container
3. Hinzufügen eines Stimmungsanalysemodells aus dem Cortana Intelligence-Katalog zu Ihrem Azure Machine Learning-Arbeitsbereich und bereitstellen dieses Modell als Webdienst im Machine Learning-Arbeitsbereich
5. Erstellen eines Stream Analytics-Auftrag, der diesen Webdienst als Funktion aufruft, um die Stimmung für die Texteingabe zu ermitteln
6. Starten des Stream Analytics-Auftrags und Überprüfen der Ausgabe

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Erstellen eines Speichercontainers und Hochladen der CSV-Eingabedatei
Für diesen Schritt können Sie eine beliebige CSV-Datei verwenden, also z.B. auch die bei GitHub verfügbare.

1. Klicken Sie im Azure-Portal auf **Ressource erstellen** > **Storage** > **Speicherkonto**.

2. Geben Sie einen Namen (`samldemo` im Beispiel) an. Der Name darf nur Kleinbuchstaben und Zahlen enthalten und muss innerhalb von Azure eindeutig sein. 

3. Geben Sie eine vorhandene Ressourcengruppe und einen Standort an. Es wird empfohlen, dass alle Ressourcen, die in diesem Tutorial erstellt werden, denselben Standort aufweisen.

    ![Speicherkontodetails angeben](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

4. Wählen Sie das Speicherkonto im Azure-Portal aus. Klicken Sie auf dem Blatt des Speicherkontos auf **Container** und dann auf **+&nbsp;Container**, um den Blobspeicher zu erstellen.

    ![Erstellen eines Blobspeichercontainers für Eingabe](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Geben Sie einen Namen für den Container (`azuresamldemoblob` in diesem Beispiel) ein, und vergewissern Sie sich, dass **Zugriffstyp** auf **Blob** festgelegt ist. Wenn Sie fertig sind, klicken Sie auf **OK**.

    ![Details zum Blobcontainer angeben](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. Wählen Sie auf dem Blatt **Container** den neuen Container aus, um das Blatt für den betreffenden Container zu öffnen.

7. Klicken Sie auf **Hochladen**.

    ![Schaltfläche „Hochladen“ für einen Container](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. Laden Sie auf dem Blatt **Blob hochladen** die Datei **sampleinput.csv** hoch, die Sie weiter oben heruntergeladen haben. Wählen Sie als **Blobtyp** die Option **Blockblob** aus, und legen Sie die Blockgröße auf 4 MB fest. Dies sollte für dieses Tutorial ausreichend sein.

9. Klicken Sie unten auf dem Blatt auf die Schaltfläche **Hochladen**.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Hinzufügen des Stimmungsanalysemodells aus dem Cortana Analytics-Katalog

Nachdem sich die Beispieldaten in einem Blob befinden, können Sie das Standpunktanalysemodell im Cortana Intelligence-Katalog aktivieren.

1. Navigieren Sie im Cortana Intelligence-Katalog zur Seite [Predictive Experiment – Mini Twitter sentiment analysis](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1).  

2. Klicken Sie auf **In Studio öffnen**.  
   
   ![Stream Analytics und Machine Learning, Machine Learning Studio öffnen](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Melden Sie sich an, um zum Arbeitsbereich zu gelangen. Wählen Sie einen Standort aus.

4. Klicken Sie unten auf der Seite auf **Ausführen** . Der Prozess wird ausgeführt und dauert etwa Minute.

   ![Experiment in Machine Learning Studio ausführen](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Nachdem der Prozess erfolgreich ausgeführt wurde, wählen Sie unten auf der Seite **Webdienst bereitstellen** aus.

   ![Experiment als Webdienst in Machine Learning Studio bereitstellen](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Um zu überprüfen, ob das Modell zur Stimmungsanalyse bereit ist, klicken Sie auf die Schaltfläche **Test**. Geben Sie eine Texteingabe wie „I love Microsoft“ ein. 

   ![Experiment in Machine Learning Studio testen](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Wenn die Analyse funktioniert, wird ein Ergebnis ähnlich dem folgenden Text angezeigt:

   ![Testergebnisse in Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. Klicken Sie in der Spalte **Apps** auf den Link **Excel 2010 or earlier workbook** (Arbeitsmappe für Excel 2010 oder früher), um eine Excel-Arbeitsmappe herunterzuladen. Die Arbeitsmappe enthält den API-Schlüssel und die URL, die Sie später zum Einrichten des Stream Analytics-Auftrags benötigen.

    ![Stream Analytics und Machine Learning auf einen Blick](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Erstellen eines Stream Analytics-Auftrags unter Verwendung des Machine Learning-Modells

Sie können jetzt einen Stream Analytics-Auftrag erstellen, der die Beispiel-Tweets aus der CSV-Datei im Blobspeicher liest. 

### <a name="create-the-job"></a>Erstellen des Auftrags

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).  

2. Klicken Sie auf **Ressource erstellen** > **Internet der Dinge** > **Stream Analytics-Auftrag**. 

3. Benennen Sie den Auftrag `azure-sa-ml-demo`. Geben Sie ein Abonnement und eine vorhandene Ressourcengruppe an, oder erstellen Sie eine neue, und wählen Sie den Speicherort für den Auftrag aus.

   ![Einstellungen für den neuen Stream Analytics-Auftrag angeben](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-job-1.png)
   

### <a name="configure-the-job-input"></a>Konfigurieren einer Auftragseingabe
Der Auftrag ruft seine Eingabe aus der CSV-Datei ab, die Sie zuvor in den Blobspeicher hochgeladen haben.

1. Klicken Sie, nachdem der Auftrag unter **Auftragstopologie** auf dem Blatt „Auftrag“ erstellt wurde, auf die Option **Eingaben**.    

2. Klicken Sie auf dem Blatt **Eingaben** auf **Datenstromeingabe hinzufügen** >**Blobspeicher**.

3. Fügen Sie auf dem Blatt **Blobspeicher** die folgenden Werte ein:

   
   |Feld  |Wert  |
   |---------|---------|
   |**Eingabealias** | Verwenden Sie den Namen `datainput`, und wählen Sie die Option **Select blob storage from your subscription** (Blobspeicher aus Ihrem Abonnement wählen).       |
   |**Speicherkonto**  |  Wählen Sie das zuvor erstellte Speicherkonto aus.  |
   |**Container**  | Wählen Sie den zuvor erstellten Container (`azuresamldemoblob`) aus.        |
   |**Ereignisserialisierungsformat**  |  Wählen Sie **CSV**.       |

   ![Einstellungen für die neue Stream Analytics-Auftragseingabe](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

1. Klicken Sie auf **Speichern**.

### <a name="configure-the-job-output"></a>Konfigurieren der Auftragsausgabe
Der Auftrag sendet die Ergebnisse an denselben Blobspeicher, aus dem er die Eingaben abruft. 

1. Klicken Sie auf dem Blatt des Auftrags unter **Auftragstopologie** auf die Option **Ausgaben**.  

2. Klicken Sie auf dem Blatt **Ausgaben** auf **Hinzufügen** >**Blobspeicher**, und fügen Sie dann eine Ausgabe mit dem Alias `datamloutput` hinzu. 

3. Fügen Sie auf dem Blatt **Blobspeicher** die folgenden Werte ein:

   |Feld  |Wert  |
   |---------|---------|
   |**Ausgabealias** | Verwenden Sie den Namen `datamloutput`, und wählen Sie die Option **Select blob storage from your subscription** (Blobspeicher aus Ihrem Abonnement wählen).       |
   |**Speicherkonto**  |  Wählen Sie das zuvor erstellte Speicherkonto aus.  |
   |**Container**  | Wählen Sie den zuvor erstellten Container (`azuresamldemoblob`) aus.        |
   |**Ereignisserialisierungsformat**  |  Wählen Sie **CSV**.       |

   ![Einstellungen für die neue Stream Analytics-Auftragsausgabe](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-output.png) 

4. Klicken Sie auf **Speichern**.   


### <a name="add-the-machine-learning-function"></a>Hinzufügen der Machine Learning-Funktion 
Sie haben bereits ein Machine Learning-Modell in einem Webdienst veröffentlicht. In diesem Szenario sendet der Stream Analysis-Auftrag während seiner Ausführung jeden Beispiel-Tweet aus der Eingabe für die Standpunktanalyse an den Webdienst. Der Machine Learning-Webdienst gibt eine Stimmung (`positive`, `neutral` oder `negative`) und eine Wahrscheinlichkeit, ob der Tweet positiv ist, zurück. 

In diesem Abschnitt des Tutorials definieren Sie eine Funktion im Stream Analysis-Auftrag. Die Funktion kann aufgerufen werden, um einen Tweet an den Webdienst zu senden und die Antwort abzurufen. 

1. Sie benötigen dazu die Webdienst-URL und den API-Schlüssel, die Sie zuvor in der Excel-Arbeitsmappe heruntergeladen haben.

2. Navigieren Sie zu Ihrem Auftragsblatt, und wählen Sie **Funktionen** >  **+ Hinzufügen** > **AzureML**.

3. Fügen Sie auf dem Blatt **Azure Machine Learning-Funktion** die folgenden Werte ein:

   |Feld  |Wert  |
   |---------|---------|
   | **Funktionsalias** | Verwenden Sie den Namen `sentiment`, und wählen Sie **Einstellungen für Azure Machine Learning-Funktion manuell angeben**. Es wird eine Option zum Eingeben der URL und des Schlüssels angezeigt.      |
   | **URL**| Fügen Sie die Webdienst-URL ein.|
   |**Schlüssel** | Fügen Sie den API-Schlüssel ein. |
  
   ![Einstellungen für das Hinzufügen einer Machine Learning-Funktion zum Stream Analytics-Auftrag](./media/stream-analytics-machine-learning-integration-tutorial/add-machine-learning-function.png)  
    
4. Klicken Sie auf **Speichern**.

### <a name="create-a-query-to-transform-the-data"></a>Erstellen einer Abfrage zum Transformieren der Daten

Stream Analytics verwendet eine deklarative, SQL-basierte Abfrage zur Analyse und Verarbeitung der Eingabe. In diesem Abschnitt erstellen Sie eine Abfrage, die jeden Tweet aus der Eingabe liest und dann die Machine Learning-Funktion aufruft, um die Standpunktanalyse durchzuführen. Die Abfrage sendet das Ergebnis dann an die Ausgabe, die Sie definiert haben (Blobspeicher).

1. Wechseln Sie zum Blatt mit der Auftragsübersicht zurück.

2.  Klicken Sie unter **Auftragstopologie** auf das Feld **Abfrage**.

3. Geben Sie die folgende Abfrage ein:

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM datainput  
    )  

    SELECT text, result.[Score]  
    INTO datamloutput
    FROM sentiment  
    ```    

    Die Abfrage ruft die Funktion auf, die Sie zuvor erstellt haben (`sentiment`), um für jeden Tweet in der Eingabe eine Standpunktanalyse durchzuführen. 

4. Klicken Sie auf **Speichern** , um die Abfrage zu speichern.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Starten des Stream Analytics-Auftrags und Überprüfen der Ausgabe

Sie können den Stream Analytics-Auftrag jetzt starten.

### <a name="start-the-job"></a>Starten des Auftrags
1. Wechseln Sie zum Blatt mit der Auftragsübersicht zurück.

2. Klicken Sie oben auf dem Blatt auf **Starten**.

3. Wählen Sie in **Abfrage starten** die Option **Benutzerdefiniert** aus, und wählen Sie dann einen Zeitpunkt aus, der vor dem Zeitpunkt des Hochladens der CSV-Datei in den Blobspeicher liegt. Wenn Sie fertig sind, klicken Sie auf **Starten**.  


### <a name="check-the-output"></a>Überprüfen der Ausgabe
1. Lassen Sie den Auftrag einige Minuten laufen, bis Aktivitäten im Feld **Überwachung** angezeigt werden. 

2. Wenn Sie über ein Tool verfügen, mit dem Sie normalerweise den Inhalt von Blobspeicher untersuchen, verwenden Sie dieses Tool, um den Container `azuresamldemoblob` zu untersuchen. Führen Sie andernfalls die folgenden Schritte im Azure-Portal aus:

    1. Suchen Sie im Portal das Speicherkonto `samldemo` und in diesem Konto den Container `azuresamldemoblob`. Sie sehen zwei Dateien im Container: die Datei mit den Beispiel-Tweets und eine CSV-Datei, die vom Stream Analytics-Auftrags generiert wurde.
    2. Klicken Sie mit der rechten Maustaste auf die generierte Datei, und wählen Sie dann **Herunterladen** aus. 

   ![CSV-Ausgabe des Auftrags aus dem Blobspeicher herunterladen](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Öffnen Sie die generierte CSV-Datei. Die Anzeige sollte in etwa wie im folgenden Beispiel aussehen:  
   
   ![Stream Analytics und Machine Learning, CSV-Ansicht](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Anzeigen von Metriken
Sie können auch auf Azure Machine Learning-Funktionen bezogene Metriken anzeigen. Die folgenden funktionenbezogenen Metriken werden im Feld **Überwachung** auf dem Blatt des Auftrags angezeigt:

* **Funktionsanforderungen** gibt die Anzahl von Anforderungen an, die an den Machine Learning-Webdienst gesendet werden.  
* **Funktionsereignisse** gibt die Anzahl von Ereignissen in der Anforderung an. Standardmäßig enthält jede Anforderung an einen Machine Learning-Webdienst bis zu 1000 Ereignisse.  


## <a name="next-steps"></a>Nächste Schritte

* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Integrieren von REST-API und Machine Learning](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)




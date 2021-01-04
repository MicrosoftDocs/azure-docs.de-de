---
title: Wettervorhersage mithilfe von Azure Machine Learning Studio (klassisch) mit Daten vom IoT-Hub
description: Verwenden Sie Azure Machine Learning Studio (klassisch), um das Regenrisiko basierend auf den Temperatur- und Luftfeuchtigkeitsdaten vorherzusagen, die Ihr IoT-Hub über einen Sensor erfasst.
author: robinsh
manager: philmea
keywords: Wettervorhersage, Machine Learning
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 09/16/2020
ms.author: robinsh
ms.openlocfilehash: ab9e122ba0b2b50203a2d66ae14f03f3b6300f96
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452346"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning-studio-classic"></a>Wettervorhersage mithilfe von Sensordaten Ihres IoT-Hubs in Azure Machine Learning Studio (klassisch)

![Lückenloses Diagramm](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Machine Learning ist ein Data Science-Verfahren, bei dem Computer aus vorhandenen Daten lernen können, um zukünftiges Verhalten, Ergebnisse und Trends vorherzusagen. Azure Machine Learning Studio (klassisch) ist ein Predictive Analytics-Clouddienst, der die schnelle Erstellung und Bereitstellung von Vorhersagemodellen als Analyselösungen ermöglicht.

## <a name="what-you-learn"></a>Lerninhalt

Sie erfahren, wie Sie Azure Machine Learning Studio (klassisch) für eine Wettervorhersage (Regenrisiko) mithilfe der Temperatur- und Luftfeuchtigkeitsdaten Ihres Azure IoT-Hubs verwenden. Das Regenrisiko ist die Ausgabe eines vorbereiteten Wettervorhersagemodells. Das Modell basiert auf Verlaufsdaten zur Vorhersage der Regenwahrscheinlichkeit basierend auf Temperatur und Luftfeuchtigkeit.

## <a name="what-you-do"></a>Aufgaben

- Stellen Sie das Wettervorhersagemodell als Webdienst bereit.
- Bereiten Sie Ihren IoT Hub für den Datenzugriff vor, indem Sie eine Consumergruppe hinzufügen.
- Erstellen Sie einen Stream Analytics-Auftrag, und konfigurieren Sie ihn für folgende Aufgaben:
  - Lesen der Temperatur- und Luftfeuchtigkeitsdaten von Ihrem IoT Hub.
  - Aufrufen des Webdiensts, um das Regenrisiko abzurufen.
  - Speichern des Ergebnisses in einem Azure Blob Storage.
- Verwenden des Microsoft Azure Storage-Explorers zur Anzeige der Wettervorhersage.

## <a name="what-you-need"></a>Voraussetzungen

- Sie müssen das Tutorial [Verbinden des Raspberry Pi-Onlinesimulators mit Azure IoT Hub (Node.js)](iot-hub-raspberry-pi-web-simulator-get-started.md) oder eines der gerätespezifischen Tutorials wie [Verbinden von Raspberry Pi mit Azure IoT Hub (Node.js)](iot-hub-raspberry-pi-kit-node-get-started.md) abgeschlossen haben. In diesen werden folgende Anforderungen beschrieben:
  - Ein aktives Azure-Abonnement.
  - Ein Azure IoT Hub in Ihrem Abonnement.
  - Eine Clientanwendung, die Nachrichten an Ihren Azure IoT Hub sendet.
- Ein [Azure Machine Learning Studio-Konto (klassisch)](https://studio.azureml.net/).
- Ein [Azure Storage-Konto](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts): Ein Konto vom Typ **Universell V2** wird empfohlen, aber es funktionieren auch alle Azure Storage-Konten, die Azure Blob Storage unterstützen.

> [!Note]
> In diesem Artikel werden Azure Stream Analytics und mehrere andere kostenpflichtige Dienste genutzt. Zusätzliche Gebühren fallen in Azure Stream Analytics an, wenn Daten über mehrere Azure-Regionen hinweg übertragen werden müssen. Aus diesem Grund sollten Sie sicherstellen, dass sich Ihre Ressourcengruppe, Ihr IoT-Hub und Ihr Azure Storage-Konto – sowie der Arbeitsbereich für Azure Machine Learning Studio (klassisch) und der Azure Stream Analytics-Auftrag, die später in diesem Tutorial hinzugefügt werden – alle in derselben Azure-Region befinden. Sie können die Regionsunterstützung für Azure Machine Learning Studio (klassisch) und andere Azure-Dienste auf der Seite [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-studio&regions=all) überprüfen.

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Bereitstellen des Wettervorhersagemodells als Webdienst

In diesem Abschnitt rufen Sie das Wettervorhersagemodell aus der Azure KI-Bibliothek ab. Anschließend fügen Sie dem Modell ein R-Skriptmodul hinzu, um die Temperatur- und Luftfeuchtigkeitsdaten zu bereinigen. Abschließend stellen Sie das Modell als Vorhersagewebdienst bereit.

### <a name="get-the-weather-prediction-model"></a>Abrufen des Wettervorhersagemodells

In diesem Abschnitt rufen Sie das Wettervorhersagemodell aus dem Azure KI-Katalog ab und öffnen es in Azure Machine Learning Studio (Classic).

1. Wechseln Sie zur [Seite des Wettervorhersagemodells](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).

   ![Öffnen der Seite des Wettervorhersagemodells im Azure KI-Katalog](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. Wählen Sie **In Studio (Classic) öffnen** aus, um das Modell in Microsoft Azure Machine Learning Studio (klassisch) zu öffnen. Wählen Sie eine Region in der Nähe Ihres IoT-Hubs und den richtigen Arbeitsbereich im Popupfenster **Copy Experiment from Gallery** (Experiment aus Katalog kopieren) aus.

   ![Öffnen des Wettervorhersagemodells in Azure Machine Learning Studio (Classic)](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>Hinzufügen eines R-Skriptmoduls zum Modell zum Bereinigen der Temperatur- und Luftfeuchtigkeitsdaten

Damit das Modell ordnungsgemäß funktioniert, müssen die Temperatur- und Luftfeuchtigkeitsdaten in numerische Daten konvertiert werden können. In diesem Abschnitt fügen Sie dem Wettervorhersagemodell ein R-Skriptmodul hinzu, das alle Zeilen mit Datenwerten für Temperatur und Luftfeuchtigkeit entfernt, die nicht in numerische Werte konvertiert werden können.

1. Wählen Sie auf der linken Seite des Fensters von Azure Machine Learning Studio (klassisch) den Pfeil aus, um den Werkzeugbereich zu erweitern. Geben Sie im Suchfeld „Execute“ ein. Wählen Sie das Modul **Execute R Script** aus.

   ![Auswählen des Moduls Execute R Script](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. Ziehen Sie das Modul **Execute R Script** nahe an das Modul **Clean Missing Data** und das vorhandene Modul **Execute R Script** im Diagramm. Löschen Sie die Verbindung zwischen den Modulen **Clean Missing Data** und **Execute R Script**, und verbinden Sie dann die Ein- und Ausgänge des neuen Moduls wie dargestellt.

   ![Hinzufügen des Moduls Execute R Script](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. Wählen Sie das neue Modul **Execute R Script** aus, um das zugehörige Fenster „Eigenschaften“ zu öffnen. Kopieren Sie den folgenden Code, und fügen Sie ihn in das Feld **R-Skript** ein.

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   Wenn Sie fertig sind, sollte das Fenster „Eigenschaften“ etwa wie folgt aussehen:

   ![Hinzufügen von Code zum Modul Execute R Script](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>Bereitstellen des Vorhersagewebdiensts

In diesem Abschnitt überprüfen Sie das Modell, richten einen Vorhersagewebdienst basierend auf dem Modell ein und stellen dann den Webdienst bereit.

1. Wählen Sie **Run** aus, um die Schritte im Modell zu überprüfen. Dieser Schritt kann einige Minuten in Anspruch nehmen.

   ![Ausführen des Experiments zum Überprüfen der Schritte](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. Wählen Sie **SET UP WEB SERVICE** > **Predictive Web Service** aus. Das Diagramm für das Vorhersageexperiment wird geöffnet.

   ![Bereitstellen des Wettervorhersagemodells in Azure Machine Learning Studio (Classic)](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. Löschen Sie im Diagramm für das Vorhersageexperiment die Verbindung zwischen dem Modul **Web service input** und **Select Columns in Dataset** im oberen Bereich. Ziehen Sie das Modul **Web service input** an eine beliebige Stelle in der Nähe des Moduls **Score Model**, und verbinden Sie es wie gezeigt:

   ![Verbinden von zwei Modulen in Azure Machine Learning Studio (Classic)](media/iot-hub-weather-forecast-machine-learning/connect-modules-azure-machine-learning-studio.png)

1. Wählen Sie **RUN** aus, um die Schritte im Modell zu überprüfen.

1. Wählen Sie **DEPLOY WEB SERVICE** aus, um das Modell als Webdienst bereitzustellen.

1. Laden Sie auf dem Dashboard des Modells das **Excel 2010 or earlier workbook** für **REQUEST/RESPONSE** herunter.

   > [!Note]
   > Stellen Sie sicher, dass Sie die Arbeitsmappe für **Excel 2010 oder früher** herunterladen, selbst wenn auf Ihrem Computer eine spätere Version von Excel ausgeführt wird.

   ![Herunterladen der Excel-Arbeitsmappe für den REQUEST/RESPONSE-Endpunkt](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. Öffnen Sie die Excel-Arbeitsmappe, und notieren Sie sich **WEB SERVICE URL** und **ACCESS KEY**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Erstellen, Konfigurieren und Ausführen eines Stream Analytics-Auftrags

### <a name="create-a-stream-analytics-job"></a>Erstellen eines Stream Analytics-Auftrags

1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) auf **Ressource erstellen**. Geben Sie im Suchfeld den Suchbegriff „Stream Analytics-Auftrag“ ein, und wählen Sie in der Dropdownliste mit den Ergebnissen dann **Stream Analytics-Auftrag** aus. Wählen Sie **Erstellen** aus, nachdem der Bereich **Stream Analytics-Auftrag** geöffnet wurde.
1. Geben Sie die folgenden Informationen für den Auftrag ein.

   **Auftragsname**: Der Name des Auftrags. Der Name muss global eindeutig sein.

   **Abonnement**: Wählen Sie Ihr Abonnement aus, falls es nicht die Standardeinstellung ist.

   **Ressourcengruppe**: Verwenden Sie dieselbe Ressourcengruppe wie für Ihren IoT Hub.

   **Standort**: Verwenden Sie denselben Speicherort wie für Ihre Ressourcengruppe.

   Behalten Sie bei allen anderen Feldern die Standardwerte bei.

   ![Erstellen eines Stream Analytics-Auftrags in Azure](media/iot-hub-weather-forecast-machine-learning/create-stream-analytics-job.png)

1. Klicken Sie auf **Erstellen**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Hinzufügen einer Eingabe zum Stream Analytics-Auftrag

1. Öffnen Sie den Stream Analytics-Auftrag.
1. Wählen Sie unter **Auftragstopologie** die Option **Eingaben** aus.
1. Wählen Sie im Bereich **Eingaben** die Option **Datenstromeingabe hinzufügen** und dann in der Dropdownliste die Option **IoT Hub** aus. Wählen Sie im Bereich **Neue Eingabe** die Option **IoT Hub aus Ihren Abonnements auswählen** aus, und geben Sie die folgenden Informationen ein:

   **Eingabealias**: Der eindeutige Alias für die Eingabe.

   **Abonnement**: Wählen Sie Ihr Abonnement aus, falls es nicht die Standardeinstellung ist.

   **IoT Hub**: Wählen Sie den IoT-Hub Ihres Abonnements aus.

   **Name der SAS-Richtlinie**: Wählen Sie **Dienst** aus. (Sie können auch **iothubowner** verwenden.)

   **Consumergruppe**: Wählen Sie die Consumergruppe aus, die Sie erstellt haben.

   Behalten Sie bei allen anderen Feldern die Standardwerte bei.

   ![Hinzufügen einer Eingabe zum Stream Analytics-Auftrag in Azure](media/iot-hub-weather-forecast-machine-learning/add-input-stream-analytics-job.png)

1. Wählen Sie **Speichern** aus.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Hinzufügen einer Ausgabe zum Stream Analytics-Auftrag

1. Wählen Sie unter **Auftragstopologie** die Option **Ausgaben** aus.
1. Wählen Sie im Bereich **Ausgaben** die Option **Hinzufügen** und dann in der Dropdownliste die Option **Blobspeicher/Data Lake Storage** aus. Wählen Sie im Bereich **Neue Ausgabe** die Option **Speicher aus Ihren Abonnements auswählen** aus, und geben Sie die folgenden Informationen ein:

   **Ausgabealias**: Der eindeutige Alias für die Ausgabe.

   **Abonnement**: Wählen Sie Ihr Abonnement aus, falls es nicht die Standardeinstellung ist.

   **Speicherkonto**: Das Speicherkonto für Ihren Blob Storage. Sie können ein Speicherkonto erstellen oder ein vorhandenes auswählen.

   **Container**: Der Container, in dem das Blob gespeichert wird. Sie können einen Container erstellen oder einen vorhandenen auswählen.

   **Ereignisserialisierungsformat**: Wählen Sie **CSV** aus.

   ![Hinzufügen einer Ausgabe zum Stream Analytics-Auftrag in Azure](media/iot-hub-weather-forecast-machine-learning/add-output-stream-analytics-job.png)

1. Klicken Sie auf **Speichern**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Hinzufügen einer Funktion zum Stream Analytics-Auftrag, um den von Ihnen bereitgestellten Webdienst aufzurufen

1. Wählen Sie unter **Auftragstopologie** die Option **Funktionen** aus.
1. Wählen Sie im Bereich **Funktionen** die Option **Hinzufügen** und in der Dropdownliste dann die Option **Azure ML Studio** aus. (Achten Sie darauf, dass Sie nicht **Azure ML Service**, sondern **Azure ML Studio** auswählen.) Wählen Sie im Bereich **Neue Funktion** die Option **Einstellungen für Azure Machine Learning-Funktion manuell angeben** aus, und geben Sie die folgenden Informationen ein:

   **Funktionsalias**: Geben Sie `machinelearning` ein.

   **URL**: Geben Sie die WEB SERVICE URL aus der Excel-Arbeitsmappe ein, die Sie notiert haben.

   **Schlüssel**: Geben Sie den ACCESS KEY aus der Excel-Arbeitsmappe ein, den Sie notiert haben.

   ![Hinzufügen einer Funktion zum Stream Analytics-Auftrag in Azure](media/iot-hub-weather-forecast-machine-learning/add-function-stream-analytics-job.png)

1. Wählen Sie **Speichern** aus.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurieren der Abfrage des Stream Analytics-Auftrags

1. Wählen Sie unter **Auftragstopologie** die Option **Abfrage** aus.
1. Ersetzen Sie den vorhandenen Code durch folgenden Code:

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[scored probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   Ersetzen Sie `[YourInputAlias]` durch den Eingabealias des Auftrags.

   Ersetzen Sie `[YourOutputAlias]` durch den Ausgabealias des Auftrags.

1. Wählen Sie **Abfrage speichern** aus.

> [!Note]
> Bei Auswahl von **Testabfrage** wird die folgende Meldung angezeigt: Abfragetests mit Machine Learning-Funktionen werden nicht unterstützt. Ändern Sie die Abfrage, und versuchen Sie es noch einmal. Sie können diese Meldung ignorieren und **OK** auswählen, um das Meldungsfeld zu schließen. Stellen Sie sicher, dass Sie die Abfrage speichern, bevor Sie mit dem nächsten Abschnitt fortfahren.

### <a name="run-the-stream-analytics-job"></a>Ausführen des Stream Analytics-Auftrags

Wählen Sie im Stream Analytics-Auftrag im linken Bereich die Option **Übersicht** aus. Wählen Sie anschließend **Start** > **Jetzt** > **Start** aus. Sobald der Auftrag erfolgreich gestartet wurde, ändert sich der Status des Auftrags von **Beendet** in **Wird ausgeführt**.

![Ausführen des Stream Analytics-Auftrags](media/iot-hub-weather-forecast-machine-learning/run-stream-analytics-job.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Verwenden des Microsoft Azure Storage-Explorers zur Anzeige der Wettervorhersage

Führen Sie die Clientanwendung aus, um das Sammeln der Temperatur- und Luftfeuchtigkeitsdaten sowie das Senden an Ihren IoT Hub zu starten. Für jede Nachricht, die Ihr IoT Hub empfängt, ruft der Stream Analytics-Auftrags den Wettervorhersage-Webdienst auf, um das Regenrisiko vorherzusagen. Das Ergebnis wird dann in Ihrem Azure Blob Storage gespeichert. Der Azure Storage-Explorer ist ein Tool, mit dem Sie das Ergebnis anzeigen können.

1. [Laden Sie den Microsoft Azure Storage-Explorer herunter, und installieren Sie ihn](https://storageexplorer.com/).
1. Öffnen Sie den Azure Storage-Explorer.
1. Melden Sie sich bei Ihrem Azure-Konto an.
1. Wählen Sie Ihr Abonnement aus.
1. Wählen Sie nacheinander Ihr Abonnement, die Option **Speicherkonten**, Ihr Speicherkonto, die Option **Blobcontainer** und dann Ihren Container aus.
1. Laden Sie eine CSV-Datei herunter, um das Ergebnis anzuzeigen. In der letzten Spalte ist das Regenrisiko aufgezeichnet.

   ![Abrufen des Ergebnisses der Wettervorhersage mit Azure Machine Learning Studio (klassisch)](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>Zusammenfassung

Sie haben Azure Machine Learning Studio (klassisch) erfolgreich eingesetzt, um das Regenrisiko basierend auf den Temperatur- und Luftfeuchtigkeitsdaten vorherzusagen, die Ihr IoT-Hub empfängt.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
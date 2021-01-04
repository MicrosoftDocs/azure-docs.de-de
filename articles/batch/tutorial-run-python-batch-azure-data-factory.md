---
title: 'Tutorial: Ausführen von Python-Skripts per Data Factory'
description: Es wird beschrieben, wie Sie Python-Skripts als Teil einer Pipeline per Azure Data Factory mit Azure Batch ausführen.
author: pkshultz
ms.devlang: python
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: peshultz
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 7752bc3f768aec7a3e98fb1813c4194f81fb9dfb
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917629"
---
# <a name="tutorial-run-python-scripts-through-azure-data-factory-using-azure-batch"></a>Tutorial: Ausführen von Python-Skripts per Azure Data Factory mit Azure Batch

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Authentifizieren mit Batch- und Storage-Konten
> * Entwickeln und Ausführen eines Skripts in Python
> * Erstellen eines Pools mit Computeknoten für die Ausführung einer Anwendung
> * Planen Ihrer Python-Workloads
> * Überwachen Ihrer Analysepipeline
> * Zugreifen auf Ihre Protokolldateien

Im Beispiel unten wird ein Python-Skript ausgeführt, das eine CSV-Eingabe von einem Blobspeichercontainer empfängt, einen Prozess zur Datenbearbeitung durchführt und die Ausgabe in einen separaten Blobspeichercontainer schreibt.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Eine installierte [Python](https://www.python.org/downloads/)-Distribution für lokale Tests.
* Das [-Paket ](https://pypi.org/project/azure-storage-blob/)azure-storage-blob`pip`
* Das [iris.csv-DataSet](https://www.kaggle.com/uciml/iris/version/2#Iris.csv)
* Ein Azure Batch-Konto und ein verknüpftes Azure Storage-Konto. Weitere Informationen zum Erstellen und Verknüpfen von Batch-Konten mit Speicherkonten finden Sie unter [Erstellen eines Batch-Kontos](quick-create-portal.md#create-a-batch-account).
* Ein Azure Data Factory-Konto. Weitere Informationen zum Erstellen einer Data Factory-Instanz über das Azure-Portal finden Sie unter [Erstellen einer Data Factory](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory).
* [Batch Explorer](https://azure.github.io/BatchExplorer/).
* [Azure Storage-Explorer:](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="create-a-batch-pool-using-batch-explorer"></a>Erstellen eines Batch-Pools per Batch Explorer

In diesem Abschnitt verwenden Sie Batch Explorer zum Erstellen des Batch-Pools, der von Ihrer Azure Data Factory-Pipeline verwendet wird. 

1. Melden Sie sich mit Ihren Azure-Anmeldeinformationen bei Batch Explorer an.
1. Wählen Sie Ihr Batch-Konto aus.
1. Erstellen Sie einen Pool, indem Sie auf der linken Seitenleiste die Option **Pools** und anschließend die Schaltfläche **Hinzufügen** über dem Suchformular auswählen. 
    1. Wählen Sie eine ID und einen Anzeigenamen aus. In diesem Beispiel wird `custom-activity-pool` verwendet.
    1. Legen Sie den Skalierungstyp auf **Feste Größe** und die Anzahl von dedizierten Knoten auf „2“ fest.
    1. Wählen Sie unter **Data Science** die Option **DSVM Windows** als Betriebssystem aus.
    1. Wählen Sie `Standard_f2s_v2` als Größe des virtuellen Computers aus.
    1. Aktivieren Sie den Starttask, und fügen Sie den Befehl `cmd /c "pip install azure-storage-blob pandas"` hinzu. Die Benutzeridentität kann als Standard-**Poolbenutzer** beibehalten werden.
    1. Klicken Sie auf **OK**.

## <a name="create-blob-containers"></a>Erstellen von Blobcontainern

Hier erstellen Sie Blobcontainer, in denen die Eingabe- und Ausgabedateien für den OCR-Batch-Auftrag gespeichert werden.

1. Melden Sie sich mit Ihren Azure-Anmeldeinformationen bei Storage-Explorer an.
1. Erstellen Sie unter Verwendung des mit Ihrem Batch-Konto verknüpften Speicherkontos entsprechend den unter [Erstellen eines Blobcontainers](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container) beschriebenen Schritten zwei Blobcontainer (einen für Eingabedateien und einen für Ausgabedateien).
    * In diesem Beispiel hat der Eingabecontainer den Namen `input`, und der Ausgabecontainer heißt `output`.
1. Laden Sie [`iris.csv`](https://www.kaggle.com/uciml/iris/version/2#Iris.csv) per Storage-Explorer in Ihren Eingabecontainer `input` hoch, indem Sie die Schritte unter [Verwalten von Blobs in einem Blobcontainer](../vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container) ausführen.

## <a name="develop-a-script-in-python"></a>Entwickeln eines Skripts in Python

Mit dem folgenden Python-Skript wird das Dataset `iris.csv` aus Ihrem Container `input` geladen und ein Prozess zur Datenbearbeitung durchgeführt, und die Ergebnisse werden im Container `output` gespeichert.

``` python
# Load libraries
from azure.storage.blob import BlobServiceClient
import pandas as pd

# Define parameters
storageAccountURL = "<storage-account-url>"
storageKey         = "<storage-account-key>"
containerName      = "output"

# Establish connection with the blob storage account
blob_service_client = BlockBlobService(account_url=storageAccountURL,
                               credential=storageKey
                               )

# Load iris dataset from the task node
df = pd.read_csv("iris.csv")

# Subset records
df = df[df['Species'] == "setosa"]

# Save the subset of the iris dataframe locally in task node
df.to_csv("iris_setosa.csv", index = False)

# Upload iris dataset
container_client = blob_service_client.get_container_client(containerName)
with open("iris_setosa.csv", "rb") as data:
    blob_client = container_client.upload_blob(name="iris_setosa.csv", data=data)
```

Speichern Sie das Skript als `main.py`, und laden Sie es in den **Azure Storage** `input`-Container hoch. Achten Sie darauf, dass Sie die Funktionalität lokal überprüfen und testen, bevor Sie das Skript in Ihren Blobcontainer hochladen:

``` bash
python main.py
```

## <a name="set-up-an-azure-data-factory-pipeline"></a>Einrichten einer Azure Data Factory-Pipeline

In diesem Abschnitt erstellen und überprüfen Sie eine Pipeline, indem Sie Ihr Python-Skript verwenden.

1. Führen Sie die Schritte zum Erstellen einer Data Factory-Instanz im Abschnitt „Erstellen einer Data Factory“ in [diesem Artikel](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory) aus.
1. Wählen Sie im Feld **Factory-Ressourcen** die Schaltfläche mit dem Pluszeichen (+) und dann **Pipeline** aus.
1. Legen Sie auf der Registerkarte **Allgemein** den Namen der Pipeline auf „Run Python“ fest.

    ![Legen Sie auf der Registerkarte „Allgemein“ den Namen der Pipeline auf „Run Python“ fest.](./media/run-python-batch-azure-data-factory/create-pipeline.png)

1. Erweitern Sie im Feld **Aktivitäten** die Option **Batch-Dienst**. Ziehen Sie die benutzerdefinierte Aktivität aus der Toolbox **Aktivitäten** auf die Oberfläche des Pipeline-Designers.
1. Geben Sie auf der Registerkarte **Allgemein** die Zeichenfolge **testPipeline** als Namen an.

    ![Geben Sie auf der Registerkarte „Allgemein“ die Zeichenfolge „testPipeline“ als Namen an.](./media/run-python-batch-azure-data-factory/create-custom-task.png)
1. Fügen Sie auf der Registerkarte **Azure Batch** das **Batch-Konto** hinzu, das in den vorherigen Schritten erstellt wurde, und verwenden Sie **Verbindung testen**, um die richtige Funktionsweise sicherzustellen.

    ![Fügen Sie auf der Registerkarte „Azure Batch“ das Batch-Konto hinzu, das in den vorherigen Schritten erstellt wurde, und verwenden Sie dann „Verbindung testen“.](./media/run-python-batch-azure-data-factory/integrate-pipeline-with-azure-batch.png)

1. Geben Sie auf der Registerkarte **Einstellungen** den Befehl `python main.py` ein.
1. Fügen Sie für **Resource Linked Service** (Ressourcenverknüpfungsdienst) das Speicherkonto hinzu, das in den vorherigen Schritten erstellt wurde. Testen Sie die Verbindung, um sicherzustellen, dass sie erfolgreich hergestellt werden kann.
1. Wählen Sie unter **Ordnerpfad** den Namen des **Azure Blob Storage**-Containers aus, in dem das Python-Skript und die zugeordneten Eingaben enthalten sind. Hierdurch werden die ausgewählten Dateien vom Container auf die Poolknoteninstanzen heruntergeladen, bevor das Python-Skript ausgeführt wird.

    ![Wählen Sie im Ordnerpfad den Namen des Azure Blob Storage-Containers aus.](./media/run-python-batch-azure-data-factory/create-custom-task-py-script-command.png)
1. Klicken Sie zum Überprüfen der Pipelineeinstellungen oberhalb der Canvas auf der Symbolleiste für die Pipeline auf **Überprüfen**. Vergewissern Sie sich, dass die Pipeline überprüft wurde. Klicken Sie auf die Schaltfläche &gt;&gt; (Pfeil nach rechts), um die Ausgabe der Überprüfung zu schließen.
1. Klicken Sie auf **Debuggen**, um die Pipeline zu testen und sicherzustellen, dass sie richtig funktioniert.
1. Klicken Sie auf **Veröffentlichen**, um die Pipeline zu veröffentlichen.
1. Klicken Sie auf **Trigger**, um das Python-Skript als Teil eines Batchprozesses auszuführen.

    ![Klicken Sie auf „Trigger“, um das Python-Skript als Teil eines Batchprozesses auszuführen.](./media/run-python-batch-azure-data-factory/create-custom-task-py-success-run.png)

### <a name="monitor-the-log-files"></a>Überwachen der Protokolldateien

Falls bei der Ausführung Ihres Skripts Warnungen oder Fehler auftreten, helfen Ihnen die Informationen in `stdout.txt` bzw. `stderr.txt` zur protokollierten Ausgabe weiter.

1. Wählen Sie links in Batch Explorer die Option **Aufträge** aus.
1. Wählen Sie den Auftrag aus, der von Ihrer Data Factory erstellt wurde. Wenn Ihr Pool beispielsweise den Namen `custom-activity-pool` hat, wählen Sie `adfv2-custom-activity-pool` aus.
1. Klicken Sie auf die Aufgabe, die über einen Fehlerexitcode verfügt.
1. Zeigen Sie `stdout.txt` und `stderr.txt` an, um Ihr Problem zu untersuchen und zu diagnostizieren.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie anhand eines Beispiels gelernt, wie Sie Python-Skripts mit Azure Batch als Teil einer Pipeline über Azure Data Factory ausführen.

Weitere Informationen zu Azure Data Factory:

> [!div class="nextstepaction"]
> [Übersicht über Azure Data Factory](../data-factory/introduction.md)


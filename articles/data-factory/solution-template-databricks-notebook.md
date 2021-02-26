---
title: Transformation mit Azure Databricks
description: Sie erfahren, wie in Azure Data Factory eine Lösungsvorlage zum Transformieren von Daten mithilfe eines Databricks-Notebooks verwendet wird.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/27/2020
ms.openlocfilehash: ee663423071458605f37f07293693dbc91f592bb
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362113"
---
# <a name="transformation-with-azure-databricks"></a>Transformation mit Azure Databricks

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Tutorial erstellen Sie in Azure Data Factory eine End-to-End-Pipeline, die die Aktivitäten **Validierung**, **Daten kopieren** und **Notebook** enthält.

- Die **Validierung** stellt sicher, dass das Quelldataset für die nachgeschaltete Nutzung bereit ist, bevor der Kopier- und Analyseauftrag ausgelöst wird.

- Mit **Daten kopieren** wird das Quelldataset im Senkenspeicher dupliziert, der im Azure Databricks-Notebook als DBFS eingebunden wird. Auf diese Weise kann das Dataset direkt von Spark genutzt werden.

- Durch **Notebook** wird das Databricks-Notebook ausgelöst, das das Dataset transformiert. Außerdem wird das Dataset einem verarbeiteten Ordner oder Azure Synapse Analytics hinzugefügt.

Der Einfachheit halber wird mit der Vorlage in diesem Tutorial kein geplanter Trigger erstellt. Sie können bei Bedarf einen solchen hinzufügen.

![Diagramm der Pipeline](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure Blob Storage-Konto mit einem Container mit dem Namen `sinkdata`, der als Senke verwendet werden soll.

  Notieren Sie sich den Speicherkontonamen, den Containernamen und den Zugriffsschlüssel. Sie benötigen diese Werte später in der Vorlage.

- Einen Azure Databricks-Arbeitsbereich.

## <a name="import-a-notebook-for-transformation"></a>Importieren eines Notebooks für die Transformation

So importieren Sie ein Notebook für die **Transformation** in den Databricks-Arbeitsbereich:

1. Melden Sie sich bei Ihrem Azure Databricks-Arbeitsbereich an, und wählen Sie dann **Importieren** aus.
       ![Menübefehl zum Importieren eines Arbeitsbereichs](media/solution-template-Databricks-notebook/import-notebook.png) Der Pfad Ihres Arbeitsbereichs kann sich von dem dargestellten unterscheiden, merken Sie sich ihn jedoch für später.
1. Wählen Sie **Importieren aus: URL**. Geben Sie im Textfeld `https://adflabstaging1.blob.core.windows.net/share/Transformations.html` ein.

   ![Auswahl zum Importieren eines Notebooks](media/solution-template-Databricks-notebook/import-from-url.png)

1. Jetzt aktualisieren wir das Notebook für die **Transformation** mit den Speicherverbindungsinformationen.

   Wechseln Sie im importierten Notebook zu **Befehl 5**, wie im folgenden Codeausschnitt gezeigt.

   - Ersetzen Sie `<storage name>` und `<access key>` durch Ihre eigenen Speicherverbindungsinformationen.
   - Verwenden Sie das Speicherkonto mit dem Container `sinkdata`.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1. Generieren Sie ein **Databricks Zugriffstoken**, damit Data Factory auf Databricks zugreifen kann.
   1. Wählen Sie in Ihrem Databricks-Arbeitsbereich in der oberen rechten Ecke das Symbol für Ihr Benutzerprofil aus.
   1. Wählen Sie **Benutzereinstellungen** aus.
    ![Menübefehl für Benutzereinstellungen](media/solution-template-Databricks-notebook/user-setting.png)
   1. Wählen Sie auf der Registerkarte **Zugriffstoken** die Option **Neues Token generieren** aus.
   1. Wählen Sie **Generieren** aus.

    ![Schaltfläche „Generieren“](media/solution-template-Databricks-notebook/generate-new-token.png)

   *Speichern Sie das Zugriffstoken* für die spätere Verwendung beim Erstellen eines verknüpften Databricks-Diensts. Das Zugriffstoken lautet ungefähr `dapi32db32cbb4w6eee18b7d87e45exxxxxx`.

## <a name="how-to-use-this-template"></a>So verwenden Sie diese Vorlage

1. Wechseln Sie zur Vorlage **Transformation mit Azure Databricks**, und erstellen Sie neue verknüpfte Dienste für die folgenden Verbindungen.

   ![Einstellung für Verbindungen](media/solution-template-Databricks-notebook/connections-preview.png)

    - **Quellblobverbindung**: Für den Zugriff auf die Quelldatenbank.

       Für diese Übung können Sie den öffentlichen Blobspeicher verwenden, der die Quelldateien enthält. Der folgende Screenshot dient als Referenz für die Konfiguration. Verwenden Sie die folgende **SAS-URL** zum Herstellen einer Verbindung mit dem Quellspeicher (schreibgeschützter Zugriff):

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![Auswahl für die Authentifizierungsmethode und SAS-URL](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - **Zielblobverbindung**: Zum Speichern der kopierten Daten.

       Wählen Sie im Fenster **Neuer verknüpfter Dienst** den Senkenspeicherblob aus.

       ![Senkenspeicherblob als neuer verknüpfter Dienst](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Azure Databricks**: Zum Herstellen einer Verbindung mit dem Databricks-Cluster.

        Erstellen Sie einen mit Databricks verknüpften Dienst, indem Sie den zuvor generierten Zugriffsschlüssel verwenden. Sie können einen *interaktiven Cluster* auswählen, sofern vorhanden. In diesem Beispiel wird die Option **Neuer Auftragscluster** verwendet.

        ![Optionen zum Herstellen einer Verbindung mit dem Cluster](media/solution-template-Databricks-notebook/databricks-connection.png)

1. Klicken Sie auf **Diese Vorlage verwenden**. Sie sehen, dass eine Pipeline erstellt wird.

    ![Erstellen einer Pipeline](media/solution-template-Databricks-notebook/new-pipeline.png)

## <a name="pipeline-introduction-and-configuration"></a>Einführung und Konfiguration der Pipeline

In der neuen Pipeline sind die meisten Einstellungen automatisch mit den jeweiligen Standardwerten konfiguriert. Überprüfen Sie die Konfigurationen der Pipeline, und nehmen Sie ggf. erforderliche Änderungen vor.

1. Überprüfen Sie, ob in der Aktivität **Validierung** mit dem Namen **Availability flag** der Wert für das Quell-**Dataset** auf das zuvor von Ihnen erstellte `SourceAvailabilityDataset` festgelegt ist.

   ![Quelldatasetwert](media/solution-template-Databricks-notebook/validation-settings.png)

1. Überprüfen Sie in der Aktivität **Daten kopieren** mit dem Namen **file-to-blob** die Registerkarten **Quelle** und **Senke**. Ändern Sie die Einstellungen bei Bedarf.

   - Registerkarte **Quelle** ![Registerkarte „Quelle“](media/solution-template-Databricks-notebook/copy-source-settings.png)

   - Registerkarte **Senke** ![Registerkarte „Senke“](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1. Überprüfen Sie in der Aktivität **Notebook** mit dem Namen **Transformation** die Pfade und Einstellungen, und aktualisieren Sie sie ggf.

   Der **verknüpfte Databricks-Dienst** sollte bereits mit dem Wert aus einem vorherigen Schritt aufgefüllt sein, wie hier gezeigt: ![Aufgefüllter Wert für den verknüpften Databricks-Dienst](media/solution-template-Databricks-notebook/notebook-activity.png)

   So überprüfen Sie die **Notebook**-Einstellungen:
  
    1. Wählen Sie die Registerkarte **Einstellungen** aus. Überprüfen Sie für **Notebook-Pfad**, ob der Standardpfad richtig ist. Möglicherweise müssen Sie den richtigen Notebook Pfad suchen und auswählen.

       ![Notebook-Pfad](media/solution-template-Databricks-notebook/notebook-settings.png)

    1. Erweitern Sie die Auswahl **Basisparameter**, und überprüfen Sie, ob die Parameter mit den im folgenden Screenshot gezeigten Parametern übereinstimmen. Diese Parameter werden von Data Factory an das Databricks-Notebook übergeben.

       ![Basisparameter](media/solution-template-Databricks-notebook/base-parameters.png)

1. Überprüfen Sie, ob die **Pipelineparameter** mit der Anzeige im folgenden Screenshot übereinstimmen: ![Pipelineparameter](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. Stellen Sie eine Verbindung mit den Datasets her.

    >[!NOTE]
    >In den folgenden Datasets wurde der Dateipfad in der Vorlage automatisch angegeben. Wenn Änderungen erforderlich sind, stellen Sie sicher, dass Sie den Pfad sowohl für **Container** als auch für **Verzeichnis** angeben, falls ein Verbindungsfehler auftreten sollte.

   - **SourceAvailabilityDataset**: Um zu überprüfen, ob die Quelldaten verfügbar sind.

     ![Auswahl für den verknüpften Dienst und den Dateipfad für „SourceAvailabilityDataset“](media/solution-template-Databricks-notebook/source-availability-dataset.png)

   - **SourceFilesDataset**: Für den Zugriff auf die Quelldaten.

       ![Auswahl für den verknüpften Dienst und den Dateipfad für „SourceFilesDataset“](media/solution-template-Databricks-notebook/source-file-dataset.png)

   - **DestinationFilesDataset**: Zum Kopieren der Daten in den Zielspeicherort de Senke. Verwenden Sie die folgenden Werte:

     - **Verknüpfter Dienst** - `sinkBlob_LS`, in einem vorherigen Schritt erstellt.

     - **Dateipfad** - `sinkdata/staged_sink`.

       ![Auswahl für den verknüpften Dienst und den Dateipfad für „DestinationFilesDataset“](media/solution-template-Databricks-notebook/destination-dataset.png)

1. Wählen Sie **Debuggen** aus, um die Pipeline auszuführen. Hier finden Sie den Link zu Databricks-Protokollen, über den Sie ausführlichere Spark-Protokolle erhalten.

    ![Link zu Databricks-Protokollen aus der Ausgabe](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    Sie können die Datendatei auch mit dem Azure Storage-Explorer überprüfen.

    > [!NOTE]
    > Für die Korrelation mit Data Factory-Pipelineausführungen wird in diesem Beispiel die Pipelineausführungs-ID aus der Data Factory an den Ausgabeordner angefügt. Dies erleichtert die Nachverfolgung der durch jede Ausführung generierten Dateien.
    > ![Angefügte Pipelineausführungs-ID](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>Nächste Schritte

- [Einführung in den Azure Data Factory-Dienst](introduction.md)

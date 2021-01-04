---
title: Sammeln von Daten zu Ihren Produktionsmodellen
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Daten aus einem Azure Machine Learning Modell sammeln, das in einem Azure Kubernetes Service-Cluster (AKS) bereitgestellt wird.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 07/14/2020
ms.topic: conceptual
ms.custom: how-to, data4ml
ms.openlocfilehash: c6b9dc95e1d50481ac5353460910032ca1711ab1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000452"
---
# <a name="collect-data-from-models-in-production"></a>Sammeln von Daten von Modellen in der Produktion

In diesem Artikel wird beschrieben, wie Sie Daten aus einem Azure Machine Learning Modell sammeln, das in einem Azure Kubernetes Service-Cluster (AKS) bereitgestellt wird. Die gesammelten Daten werden anschließend in Azure Blob Storage gespeichert.

Nach der Aktivierung der Sammlung unterstützen die gesammelten Daten Sie bei den folgenden Aufgaben:

* [Überwachen Sie Datendrifts](how-to-monitor-datasets.md) bei den Produktionsdaten, die Sie erfassen.

* Analysieren der gesammelten Daten mit [Power BI](#powerbi) oder [Azure Databricks](#databricks)

* Fundiertere Entscheidungen in Bezug auf den Zeitpunkt zum erneuten Trainieren oder Optimieren des Modells

* Erneutes Trainieren des Modells mit den gesammelten Daten

## <a name="what-is-collected-and-where-it-goes"></a>Welche Daten werden gesammelt, und wo werden sie gespeichert?

Die folgenden Daten können gesammelt werden:

* Modelleingabedaten von Webdiensten, die in einem AKS-Cluster bereitgestellt werden. Sprachaudiodaten, Bilder und Videos werden *nicht* erfasst.
  
* Modellvorhersagen, die auf Eingabedaten aus der Produktion gestützt sind.

>[!NOTE]
> Vorabaggregation und -berechnungen für diese Daten sind zurzeit nicht Teil des Sammlungsdiensts.

Die Ausgabe wird im Blobspeicher gespeichert. Da die Daten dem Blobspeicher hinzugefügt werden, können Sie Ihr bevorzugtes Tool zum Ausführen der Analyse auswählen.

Der Pfad zu den Ausgabedaten im Blob folgt dieser Syntax:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> In älteren Versionen des Azure Machine Learning SDK für Python als Version 0.1.0a16 hat das Argument `designation` den Namen `identifier`. Wenn Ihr Code mit einer älteren Version entwickelt wurde, müssen Sie ihn entsprechend aktualisieren.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://aka.ms/AMLFree) erstellen, bevor Sie beginnen.

- Ein Azure Machine Learning-Arbeitsbereich, ein lokales Verzeichnis mit Ihren Skripts und das Azure Machine Learning SDK für Python müssen installiert sein. Informationen zur Installation finden Sie unter [Konfigurieren einer Entwicklungsumgebung](how-to-configure-environment.md).

- Ein trainiertes Machine Learning-Modell muss in AKS bereitgestellt werden. Wenn Sie keines besitzen, sehen Sie sich das Tutorial zum [Trainieren eines Bildklassifizierungsmodells](tutorial-train-models-with-aml.md) an.

- Sie benötigen einen AKS-Cluster. Informationen zum Erstellen und Bereitstellen finden Sie unter [Bereitstellen von Modellen mit Azure Machine Learning](how-to-deploy-and-where.md).

- [Einrichten Ihrer Umgebung](how-to-configure-environment.md) und Installieren des [Azure Machine Learning Monitoring SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)

## <a name="enable-data-collection"></a>Aktivieren der Datensammlung

Sie können die [Datensammlung](/python/api/azureml-monitoring/azureml.monitoring.modeldatacollector.modeldatacollector?preserve-view=true&view=azure-ml-py) ungeachtet des über Azure Machine Learning oder andere Tools bereitgestellten Modells aktivieren.

Führen Sie zum Aktivieren der Datensammlung folgende Schritte aus:

1. Öffnen Sie die Bewertungsdatei.

1. Fügen Sie den folgenden Code am Anfang der Datei hinzu:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. Deklarieren Sie die Variablen für die Datensammlung in der `init`-Funktion:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3", "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    Der Parameter *CorrelationId* ist optional. Sie müssen ihn nicht verwenden, wenn er für das Modell nicht erforderlich ist. Die Verwendung von *CorrelationId* erleichtert die Zuordnung zu anderen Daten, etwa *LoanNumber* oder *CustomerId*.
    
    Der Parameter *Identifier* wird später zum Erstellen der Ordnerstruktur in Ihrem Blob verwendet. Sie können ihn verwenden, um Rohdaten von verarbeiteten Daten zu unterscheiden.

1. Fügen Sie die folgenden Codezeilen der `run(input_df)`-Funktion hinzu:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. Die Datensammlung wird *nicht* automatisch auf **true** festgelegt, wenn Sie einen Dienst in AKS bereitstellen. Aktualisieren Sie Ihre Konfigurationsdatei, wie im folgenden Beispiel gezeigt:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    Sie können auch Application Insights für die Dienstüberwachung aktivieren, indem Sie diese Konfiguration ändern:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. Informationen zum Erstellen eines neuen Images und zum Bereitstellen des Diensts finden Sie im Dokument [Bereitstellen von Modellen mit Azure Machine Learning](how-to-deploy-and-where.md).


## <a name="disable-data-collection"></a>Datensammlung deaktivieren

Sie können das Sammeln von Daten jederzeit beenden. Verwenden Sie Python-Code zum Deaktivieren der Datensammlung.

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>Überprüfen und Analysieren Ihrer Daten

Sie können ein beliebiges Tool Ihrer Wahl verwenden, um die im Blobspeicher erfassten Daten zu analysieren.

### <a name="quickly-access-your-blob-data"></a>Schnelles Zugreifen auf Ihre Blobdaten

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.

1. Öffnen Sie Ihren Arbeitsbereich.

1. Wählen Sie **Speicher**.

    [![Auswählen der Speicheroption](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Folgen Sie dem Pfad zu den Ausgabedaten des Blobs mit dieser Syntax:

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a><a id="powerbi"></a> Analysieren von Modelldaten mit Power BI

1. Laden Sie [Power BI Desktop](https://www.powerbi.com) herunter, und öffnen Sie diese Anwendung.

1. Wählen Sie **Daten abrufen** und anschließend [**Azure Blob Storage**](/power-bi/desktop-data-sources) aus.

    [![Power BI-Blob-Setup](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. Fügen Sie den Namen Ihres Speicherkontos hinzu, und geben Sie Ihren Speicherschlüssel ein. Sie finden diese Informationen, indem Sie in Ihrem Blob **Einstellungen** > **Zugriffsschlüssel** auswählen.

1. Wählen Sie den Container **Modelldaten** und dann **Bearbeiten** aus.

    [![Power BI-Navigator](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. Klicken Sie im Abfrage-Editor unter der Spalte **Name**, und fügen Sie Ihr Speicherkonto hinzu.

1. Geben Sie Ihren Modellpfad in den Filter ein. Erweitern Sie einfach den Filterpfad, wenn Sie sich nur die Dateien für ein bestimmtes Jahr oder einen bestimmten Monat ansehen möchten. Verwenden Sie beispielsweise den folgenden Filterpfad, wenn Sie nur die Daten vom März anzeigen möchten:

   /modeldata/\<subscriptionid>/\<resourcegroupname>/\<workspacename>/\<webservicename>/\<modelname>/\<modelversion>/\<designation>/\<year>/3

1. Filtern Sie die Daten, die für Sie relevant sind, nach den Werten für **Name**. Wenn Sie Vorhersagen und Eingaben gespeichert haben, müssen Sie für jede jeweils eine separate Abfrage erstellen.

1. Wählen Sie den nach unten zeigenden Doppelpfeil neben der Spaltenüberschrift **Inhalt** aus, um die Dateien zu kombinieren.

    [![Power BI: Inhalt](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Klicken Sie auf **OK**. Die Daten werden vorab geladen.

    [![Power BI: Kombinieren von Dateien](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Wählen Sie **Schließen und übernehmen** aus.

1. Wenn Sie Eingaben und Vorhersagen hinzugefügt haben, werden Ihre Tabellen automatisch anhand der **RequestId**-Werte sortiert.

1. Beginnen Sie mit der Erstellung Ihrer benutzerdefinierten Berichte in Ihren Modelldaten.

### <a name="analyze-model-data-using-azure-databricks"></a><a id="databricks"></a> Analysieren von Modelldaten mit Azure Databricks

1. Erstellen Sie einen [Azure Databricks-Arbeitsbereich](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal).

1. Greifen Sie auf Ihren Databricks-Arbeitsbereich zu.

1. Wählen Sie in Ihrem Databricks-Arbeitsbereich die Option **Daten hochladen** aus.

    [![Auswählen der Databricks-Option „Daten hochladen“](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Wählen Sie **Neue Tabelle erstellen** und dann **Weitere Datenquellen** > **Azure Blob Storage** > **Tabelle in Notebook erstellen** aus.

    [![Databricks: Erstellen einer Tabelle](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Aktualisieren Sie den Speicherort Ihrer Daten. Beispiel:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [![Databricks-Setup](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Führen Sie die Schritte in der Vorlage aus, um Ihre Daten anzuzeigen und zu analysieren.

## <a name="next-steps"></a>Nächste Schritte

[Erkennen von Datendrifts](how-to-monitor-datasets.md) bei den gesammelten Daten.
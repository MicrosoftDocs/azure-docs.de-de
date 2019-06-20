---
title: Sammeln von Daten zu Ihren Produktionsmodellen
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Daten des Azure Machine Learning-Eingabemodells in einem Azure-Blobspeicher gesammelt werden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: df2f0731aff6c90ef83b26041a9d383ce14974c6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65024242"
---
# <a name="collect-data-for-models-in-production"></a>Sammeln von Daten für Modelle in der Produktion

In diesem Artikel erfahren Sie, wie Eingabemodelldaten aus den Azure Machine Learning Services gesammelt werden, die Sie im Azure Kubernetes-Cluster (AKS) in einem Azure-Blobspeicher bereitgestellt haben. 

Nach der Aktivierung ermöglichen Ihnen diese Daten Folgendes:
* Überwachen von Datenverschiebungen bei der Aufnahme von Produktionsdaten in das Modell

* Entscheidungsfindung in Bezug auf das erneute Trainieren und Optimieren des Modells

* Erneutes Trainieren des Modells mit den gesammelten Daten

## <a name="what-is-collected-and-where-does-it-go"></a>Welche Daten werden gesammelt, und wo werden sie gespeichert?

Die folgenden Daten können gesammelt werden:
* **Modelleingabedaten** aus Webdiensten, die im Azure Kubernetes-Cluster (AKS) bereitgestellt wurden (Sprachdaten, Bilder und Videos werden **nicht** erfasst) 
  
* Modellvorhersagen, die auf Eingabedaten aus der Produktion gestützt sind.

> [!Note]
> Vorab-Aggregation und -Berechnungen werden vom Dienst derzeit nicht abgedeckt.   

Die Ausgabe wird in einem Azure-Blob gespeichert. Da die Daten in einem Azure-Blob hinzugefügt werden, können Sie das bevorzugte Tool zum Ausführen der Analyse auswählen. 

Der Pfad zu den Ausgabedaten im Blob folgt dieser Syntax:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie noch heute die [kostenlose oder kostenpflichtige Version von Azure Machine Learning Service](https://aka.ms/AMLFree) aus.

- Ein Arbeitsbereich des Azure Machine Learning Service, ein lokales Verzeichnis mit Ihren Skripts und das Azure Machine Learning SDK für Python müssen installiert sein. Informationen zum Erhalten dieser Voraussetzungen finden Sie im Dokument [Konfigurieren einer Entwicklungsumgebung](how-to-configure-environment.md).

- Ein trainiertes Machine Learning-Modell, das im Azure Kubernetes Service (AKS) bereitgestellt werden soll. Wenn Sie keines besitzen, sehen Sie sich das Tutorial zum [Trainieren eines Imageklassifizierungsmodells](tutorial-train-models-with-aml.md) an.

- Ein Azure Kubernetes Service-Cluster. Informationen zum Erstellen und Bereitstellen finden Sie im Dokument [Bereitstellung: wie und wo?](how-to-deploy-and-where.md).

- [Richten Sie Ihre Umgebung ein](how-to-configure-environment.md), und installieren Sie das [Überwachungs-SDK](https://aka.ms/aml-monitoring-sdk).

## <a name="enable-data-collection"></a>Aktivieren der Datensammlung
Die Datensammlung kann ungeachtet des über den Azure Machine Learning-Dienst oder andere Tools bereitgestellten Modells aktiviert werden. 

Zum Aktivieren müssen Sie folgende Schritte ausführen:

1. Öffnen Sie die Bewertungsdatei. 

1. Fügen Sie den [folgenden Code](https://aka.ms/aml-monitoring-sdk) am Anfang der Datei hinzu:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. Deklarieren Sie die Variablen für die Datensammlung in der `init()`-Funktion:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", identifier="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", identifier="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* ist ein optionaler Parameter. Er muss nicht festgelegt werden, wenn dies vom Modell nicht gefordert wird. Eine vorhandene Korrelations-ID erleichtert die Zuordnung zu anderen Daten. (Beispiele: LoanNumber, CustomerId etc.)
    
    *Identifier* wird später zum Erstellen der Ordnerstruktur im Blob verwendet. Damit können Rohdaten von verarbeiteten Daten abgegrenzt werden.

3.  Fügen Sie die folgenden Codezeilen der `run(input_df)`-Funktion hinzu:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

4. Die Datensammlung wird **nicht** automatisch auf **true** festgelegt, wenn Sie einen Dienst in AKS bereitstellen. Daher müssen Sie Ihre Konfigurationsdatei wie folgt aktualisieren: 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    AppInsights für die Dienstüberwachung kann ebenfalls aktiviert werden, indem diese Konfiguration geändert wird:
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. Informationen zum Erstellen eines neuen Images und zum Bereitstellen des Diensts finden Sie im Dokument [Bereitstellung: wie und wo?](how-to-deploy-and-where.md).


Wenn Sie in der **Umgebungsdatei** und **Bewertungsdatei** bereits einen Dienst mit den Abhängigkeiten installiert haben, aktivieren Sie die Datensammlung wie folgt:

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).

1. Öffnen Sie Ihren Arbeitsbereich.

1. Navigieren Sie zu **Bereitstellungen** -> **Dienst auswählen** -> **Bearbeiten**.

   ![Dienst bearbeiten](media/how-to-enable-data-collection/EditService.PNG)

1. Deaktivieren Sie in **Erweiterte Einstellungen** die Option **Modelldatensammlung aktivieren**. 

    [![Überprüfen der Datensammlung](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   In diesem Fenster können Sie auch die Option „AppInsights-Diagnose aktivieren“ auswählen, um die Integrität des Diensts zu verfolgen.  

1. Klicken Sie auf **Aktualisieren**, um die Änderungen zu übernehmen.


## <a name="disable-data-collection"></a>Datensammlung deaktivieren
Sie können die Datensammeln jederzeit beenden. Deaktivieren Sie die Datensammlung mithilfe von Python-Code oder im Azure-Portal.

+ Option 1 – Deaktivieren im Azure-Portal: 
  1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.

  1. Öffnen Sie Ihren Arbeitsbereich.

  1. Navigieren Sie zu **Bereitstellungen** -> **Dienst auswählen** -> **Bearbeiten**.

     [![Option „Bearbeiten“](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. Deaktivieren Sie in **Erweiterte Einstellungen** die Option **Modelldatensammlung aktivieren**. 

     [![Deaktivieren der Datensammlung](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. Klicken Sie auf **Aktualisieren**, um die Änderungen zu übernehmen.

+ Option 2 – Deaktivieren der Datensammlung mithilfe von Python-Code:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>Überprüfen und Analysieren Ihrer Daten
Sie können ein beliebiges Tool Ihrer Wahl verwenden, um die im Azure-Blob erfassten Daten zu analysieren. 

Greifen Sie wie folgt schnell auf die Daten Ihres Blobs zu:
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.

1. Öffnen Sie Ihren Arbeitsbereich.
1. Klicken Sie auf **Speicher**.

    [![Speicher](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Folgen Sie dem Pfad zu den Ausgabedaten im Blob mit dieser Syntax:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Analysieren von Modelldaten mit Power BI

1. Laden Sie [Power BI Desktop](https://www.powerbi.com) herunter, und öffnen Sie die Anwendung.

1. Wählen Sie **Daten abrufen**, und klicken Sie auf [**Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [![Power BI-Blob-Setup](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. Fügen Sie den Namen Ihres Speicherkontos hinzu, und geben Sie Ihren Speicherschlüssel ein. Sie finden diese Informationen für Ihr Blob unter **Einstellungen** > „Zugriffsschlüssel“. 

1. Wählen Sie den Container **modeldata** aus, und klicken Sie auf **Bearbeiten**. 

    [![Power BI Navigator](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. Klicken Sie im Abfrage-Editor unter der Spalte „Name“, und fügen Sie Ihr erstes Speicherkonto hinzu. Modellieren Sie Ihren Pfad im Filter. Hinweis: Erweitern Sie den Filterpfad, wenn Sie sich nur die Dateien für ein bestimmtes Jahr oder einen Monat ansehen möchten. Beispielsweise nur die Daten für März: /modeldata/subscriptionid>/resourcegroupname>/workspacename>/webservicename>/modelname>/modelversion>/identifier>/year>/3

1. Filtern Sie die Daten, die für Sie relevant sind, nach dem **Namen**. Wenn Sie **Vorhersagen** und **Eingaben** gespeichert haben, müssen Sie jeweils eine separate Abfrage erstellen.

1. Klicken Sie neben der Spalte **Inhalt** auf den Doppelpfeil, um die Dateien zu kombinieren. 

    [![Power BI-Inhalt](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Wenn Sie auf „OK“ klicken, werden die Daten vorab geladen.

    [![pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Sie können jetzt auf **Schließen und übernehmen** klicken.

1.  Wenn Sie Eingaben und Vorhersagen hinzugefügt haben, werden Ihre Tabellen automatisch anhand der **RequestId** korreliert.

1. Beginnen Sie mit der Erstellung Ihrer benutzerdefinierten Berichte in Ihren Modelldaten.


### <a name="analyzing-model-data-using-databricks"></a>Analysieren von Modelldaten mit Databricks

1. Erstellen Sie einen [Databricks-Arbeitsbereich](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). 

1. Greifen Sie auf Ihren Databricks-Arbeitsbereich zu. 

1. Wählen Sie in Ihrem Databricks Arbeitsbereich die Option **Daten hochladen**.

    [![Databricks-Upload](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Erstellen Sie eine neue Tabelle, und wählen Sie **Weitere Datenquellen** > „Azure Blob Storage“ > „Create Table in Notebook“ (Tabelle in Notebook erstellen).

    [![Databricks-Tabelle](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Aktualisieren Sie den Speicherort Ihrer Daten. Beispiel:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![Databricks-Setup](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Führen Sie die Schritte in der Vorlage aus, um Ihre Daten anzuzeigen und zu analysieren. 

## <a name="example-notebook"></a>Notebook mit Beispielen

Im Notebook [how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) werden die Konzepte in diesem Artikel veranschaulicht.  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

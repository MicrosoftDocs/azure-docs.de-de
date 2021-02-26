---
title: Entwickeln mit AutoML und Azure Databricks
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie eine Entwicklungsumgebung in Azure Machine Learning und Azure Databricks einrichten. Verwenden Sie die Azure Machine Learning SDKs für Databricks und Databricks mit automatisiertem maschinellem Lernen.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: daeb0d666c0dfb8306663da1d8d59dfba6adc359
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516922"
---
# <a name="set-up-a-development-environment-with-azure-databricks-and-automl-in-azure-machine-learning"></a>Einrichten einer Entwicklungsumgebung mit Azure Databricks und automatisiertem maschinellem Lernen in Azure Machine Learning 

Hier erfahren Sie, wie Sie in Azure Machine Learning eine Entwicklungsumgebung konfigurieren, die Azure Databricks und automatisiertes maschinelles Lernen (AutoML) verwendet.

Azure Databricks eignet sich ideal für die Ausführung umfangreicher ML-Workflows auf der skalierbaren Apache Spark-Plattform in der Azure-Cloud. Sie stellt eine Notebook-basierte Umgebung mit CPU- oder GPU-basierten Computeclustern für die Zusammenarbeit bereit.

Informationen zu anderen Entwicklungsumgebungen für maschinelles Lernen finden Sie unter [Einrichten einer Python-Entwicklungsumgebung](how-to-configure-environment.md).


## <a name="prerequisite"></a>Voraussetzung

Einen Azure Machine Learning-Arbeitsbereich. Wenn Sie nicht über einen Azure Machine Learning-Arbeitsbereich verfügen, können Sie einen über das [Azure-Portal](how-to-manage-workspace.md), die [Azure-Befehlszeilenschnittstelle](how-to-manage-workspace-cli.md#create-a-workspace) oder über [Azure Resource Manager-Vorlagen](how-to-create-workspace-template.md) erstellen.


## <a name="azure-databricks-with-azure-machine-learning-and-automl"></a>Azure Databricks mit Azure Machine Learning und automatisiertem maschinellem Lernen

Azure Databricks lässt sich in Azure Machine Learning und die zugehörigen AutoML-Funktionen integrieren. 

Sie können Azure Databricks für Folgendes verwenden:

+ Trainieren eines Modells mithilfe von Spark MLlib und Bereitstellen des Modells ACI/AKS
+ Nutzen von [AutoML-Funktionen](concept-automated-ml.md) über ein Azure ML SDK
+ Nutzen als Computeziel einer [Azure Machine Learning-Pipeline](concept-ml-pipelines.md)

## <a name="set-up-a-databricks-cluster"></a>Einrichten eines Databricks-Clusters

Erstellen Sie einen [Databricks-Cluster](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Einige Einstellungen sind nur erforderlich, wenn Sie das SDK für automatisiertes Machine Learning in Databricks verwenden.

**Die Erstellung des Clusters dauert einige Minuten.**

Verwenden Sie die folgenden Einstellungen:

| Einstellung |Anwendungsbereich| Wert |
|----|---|---|
| Clustername |immer| IhrClustername |
| Databricks-Runtimeversion |immer| Runtime 7.1 (Scala 2.21, Spark 3.0.0) – Nicht-ML|
| Python-Version |immer| 3 |
| Workertyp <br>(bestimmt die maximale Anzahl gleichzeitiger Iterationen) |Automatisiertes maschinelles Lernen<br>Machine Learning| Arbeitsspeicheroptimierte VM bevorzugt |
| Worker |immer| 2 oder mehr |
| Automatische Skalierung aktivieren |Automatisiertes maschinelles Lernen<br>Machine Learning| Deaktivieren |

Warten Sie, bis der Cluster ausgeführt wird, bevor Sie fortfahren.

## <a name="add-the-azure-ml-sdk-to-databricks"></a>Hinzufügen des Azure ML SDK zu Databricks

Erstellen Sie nach der Ausführung des Clusters [eine Bibliothek](https://docs.databricks.com/user-guide/libraries.html#create-a-library), um das entsprechende Azure Machine Learning SDK-Paket Ihrem Cluster anzufügen. 

Um AutoML zu verwenden, fahren Sie mit [Hinzufügen des Azure ML SDK mit AutoML zu Databricks](#add-the-azure-ml-sdk-with-automl-to-databricks) fort.


1. Klicken Sie mit der rechten Maustaste auf den aktuellen Arbeitsbereichsordner, in dem Sie die Bibliothek speichern möchten. Wählen Sie **Bibliothek** > **erstellen** aus.
    
    > [!TIP]
    > Wenn Sie eine alte SDK-Version nutzen, deaktivieren Sie diese in den installierten Bibliotheken des Clusters, und verschieben Sie sie in den Papierkorb. Installieren Sie die neue SDK-Version, und starten Sie den Cluster neu. Wenn nach dem Neustart ein Problem vorliegt, trennen Sie Ihren Cluster, und fügen Sie ihn wieder an.

1. Wählen Sie die folgende Option aus (andere SDK-Installationen werden nicht unterstützt).

   |Zusatzkomponenten für &nbsp;SDK-Paket&nbsp;|`Source`|&nbsp;PyPi-Name&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Für Databricks| Python Egg oder PyPI hochladen | azureml-sdk[databricks]|

   > [!WARNING]
   > Sie können keine weiteren SDK-Zusatzkomponenten installieren. Wählen Sie nur die [`databricks`]-Option aus.

   * Wählen Sie nicht **Attach automatically to all clusters** (Automatisch an alle Cluster anfügen) aus.
   * Wählen Sie **Anfügen** neben dem Namen Ihres Clusters aus.

1. Der Status wird in **Angefügt** geändert. Dieser Vorgang kann einige Minuten in Anspruch nehmen. Überprüfen Sie währenddessen, ob Fehler auftreten.  Wenn bei diesem Schritt ein Fehler auftritt:

   Versuchen Sie, Ihren Cluster wie folgt neu zu starten:
   1. Wählen Sie im linken Bereich die Option **Cluster** aus.
   1. Wählen Sie in der Tabelle den Namen Ihres Clusters aus.
   1. Klicken Sie auf der Registerkarte **Bibliotheken** auf **Neu starten**.

   Eine erfolgreiche Installation sieht wie folgt aus: 

  ![Azure Machine Learning SDK für Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg) 

## <a name="add-the-azure-ml-sdk-with-automl-to-databricks"></a>Hinzufügen des Azure ML SDK mit AutoML zu Databricks
Wenn der Cluster mit Databricks Runtime 7.1 oder höher (*nicht* ML) erstellt wurde, führen Sie den folgenden Befehl in der ersten Zelle Ihres Notebooks aus, um das AML SDK zu installieren.

```
%pip install --upgrade --force-reinstall -r https://aka.ms/automl_linux_requirements.txt
```
Für Databricks Runtime 7.0 und niedriger installieren Sie das Azure Machine Learning SDK über das [init-Skript](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/automl/README.md).

### <a name="automl-config-settings"></a>AutoML-Konfigurationseinstellungen

Fügen Sie in der AutoML-Konfiguration bei Verwendung von Azure Databricks die folgenden Parameter hinzu:

- ```max_concurrent_iterations``` basiert auf der Anzahl der Workerknoten in Ihrem Cluster.
- ```spark_context=sc``` basiert auf dem standardmäßigen Spark-Kontext.

## <a name="ml-notebooks-that-work-with-azure-databricks"></a>ML-Notebooks, die mit Azure Databricks funktionieren

So können Sie Azure Databricks testen:
+ Von den vielen verfügbaren Beispielnotebooks können **nur [ganz bestimmte](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) mit Azure Databricks verwendet werden.**

+ Importieren Sie diese Beispiele direkt aus Ihrem Arbeitsbereich. Siehe unten: ![„Importieren“ auswählen](./media/how-to-configure-environment/azure-db-screenshot.png)
![Bereich für Importieren](./media/how-to-configure-environment/azure-db-import.png)

+ Erfahren Sie, wie Sie [mit Databricks als Computeziel für das Trainieren von Modellen eine Pipeline erstellen](./how-to-create-machine-learning-pipelines.md).

## <a name="troubleshooting"></a>Problembehandlung

* **Fehler beim Installieren von Paketen**

    Bei der Installation des Azure Machine Learning SDK tritt in Azure Databricks ein Fehler auf, wenn mehrere Pakete installiert werden. Einige Pakete, z.B. `psutil`, können Konflikte verursachen. Um Fehler bei der Installation zu vermeiden, frieren Sie die Bibliotheksversion beim Installieren der Pakete ein. Dieses Problem hängt mit Databricks und nicht mit dem Azure Machine Learning SDK zusammen. Es kann auch mit anderen Bibliotheken auftreten. Beispiel:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    Falls bei Python-Bibliotheken immer wieder Installationsprobleme auftreten, können Sie alternativ Initialisierungsskripts verwenden. Dieser Ansatz wird nicht offiziell unterstützt. Weitere Informationen finden Sie unter [Initialisierungsskripts im Clusterbereich](/azure/databricks/clusters/init-scripts#cluster-scoped-init-scripts).

* **Importfehler: Der Name `Timedelta` kann nicht aus `pandas._libs.tslibs` importiert werden:** Wenn dieser Fehler angezeigt wird, wenn Sie automatisiertes Machine Learning verwenden, führen Sie die beiden folgenden Zeilen in Ihrem Notebook aus:
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Importfehler: Kein Modul mit dem Namen „pandas.core.indexes“** : Wenn diese Fehlermeldung bei Verwendung von automatisiertem maschinellem Lernen angezeigt wird, gehen Sie folgendermaßen vor:

    1. Führen Sie diesen Befehl aus, um zwei Pakete in Ihrem Azure Databricks-Cluster zu installieren:
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. Trennen Sie den Cluster auf, und fügen Sie ihn dann wieder an Ihr Notebook an.
    
    Wenn diese Schritte das Problem nicht beheben, versuchen Sie, den Cluster neu zu starten.

* **FailToSendFeather:** Sollte beim Lesen von Daten aus einem Azure Databricks-Cluster ein Fehler vom Typ `FailToSendFeather` auftreten, haben Sie folgende Möglichkeiten:
    
    * Upgraden Sie das Paket `azureml-sdk[automl]` auf die aktuelle Version.
    * Fügen Sie mindestens die Version 1.1.8 von `azureml-dataprep` hinzu.
    * Fügen Sie mindestens die Version 0.11 von `pyarrow` hinzu.
  

## <a name="next-steps"></a>Nächste Schritte

- [Trainieren Sie ein Modell](tutorial-train-models-with-aml.md) in Azure Machine Learning mit dem MNIST-Dataset.
- Weitere Informationen erhalten Sie in der [Referenz zum Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).
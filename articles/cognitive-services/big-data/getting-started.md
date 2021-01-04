---
title: Erste Schritte mit Cognitive Services für Big Data
description: Richten Sie Ihre MMLSpark-Pipeline mit Cognitive Services in Azure Databricks ein, und führen Sie ein Beispiel aus.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: 095f2c3ed17042bb616fb091d1af52a64c913709
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460498"
---
# <a name="getting-started"></a>Erste Schritte

Das Einrichten Ihrer Umgebung ist der erste Schritt beim Aufbau einer Pipeline für Ihre Daten. Nachdem Sie Ihre Umgebung vorbereitet haben, können Sie ein Beispiel schnell und einfach ausführen.

In diesem Artikel führen wir die folgenden Schritte aus, um Ihnen den Einstieg zu erleichtern:

1. [Erstellen einer Cognitive Services-Ressource](#create-a-cognitive-services-resource)
1. [Erstellen eines Apache Spark-Clusters](#create-an-apache-spark-cluster)
1. [Beispiel ausprobieren](#try-a-sample)

## <a name="create-a-cognitive-services-resource"></a>Erstellen einer Cognitive Services-Ressource

Um das Cognitive Services für Big Data verwenden zu können, müssen wir zuerst eine Cognitive Service-Instanz für unseren Workflow erstellen. Es gibt zwei Haupttypen von Cognitive Services: Clouddienste, die in Azure gehostet werden, und Containerdienste, die von Benutzern verwaltet werden. Es wird empfohlen, mit dem einfacheren cloudbasierten Typ von Cognitive Services zu beginnen.

### <a name="cloud-services"></a>Clouddienste

Cloudbasierte Cognitive Services-Instanzen sind intelligente Algorithmen, die in Azure gehostet werden. Diese Dienste sind ohne Training einsatzbereit. Sie benötigen lediglich eine Internetverbindung. Sie können [eine Cognitive Service-Instanz im Azure-Portal](../cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows) oder mit der [Azure CLI](../cognitive-services-apis-create-account-cli.md?tabs=windows) erstellen.

### <a name="containerized-services-optional"></a>Dienste in Containern (optional)

Wenn für Ihre Anwendung oder Workload große Datasets verwendet werden, private Netzwerke erforderlich sind oder keine Verbindung mit der Cloud hergestellt werden kann, ist die Kommunikation mit Clouddiensten unter Umständen nicht möglich. In dieser Situation haben Cognitive Services-Instanzen in Containern folgende Vorteile:

* **Niedrige Konnektivität**: Sie können Cognitive Services in Containern in jeder beliebigen Computerumgebung bereitstellen, sowohl in als auch außerhalb der Cloud. Wenn Ihre Anwendung nicht mit der Cloud in Kontakt treten kann, erwägen Sie die Bereitstellung von Cognitive Services in Containern für Ihre Anwendung.

* **Geringe Wartezeit**: Da für Dienste in Containern die Roundtrip-Kommunikation von/zur Cloud nicht erforderlich ist, werden Antworten mit erheblich geringeren Latenzzeiten zurückgegeben.

* **Datenschutz und -sicherheit**: Sie können Dienste in Containern in privaten Netzwerken bereitstellen, sodass sensible Daten das Netzwerk nicht verlassen.

* **Hohe Skalierbarkeit**: Für Dienste in Containern gibt es keine Begrenzung der Datenübertragungsrate, und sie werden auf von Benutzern verwalteten Computern ausgeführt. Daher können Sie Cognitive Services ohne Ende skalieren, um weitaus größere Workloads zu verarbeiten.

Befolgen Sie [diese Anleitung](../cognitive-services-container-support.md?tabs=luis), um eine Cognitive Service-Instanz in einem Container zu erstellen.

## <a name="create-an-apache-spark-cluster"></a>Erstellen eines Apache Spark-Clusters

[Apache Spark&trade;](http://spark.apache.org/) ist ein verteiltes Computing-Framework, das für die Verarbeitung von Big Data-Daten konzipiert ist. Benutzer können mit Apache Spark in Azure mit Diensten wie Azure Databricks, Azure Synapse Analytics, HDInsight und Azure Kubernetes Services arbeiten. Um das Cognitive Services für Big Data verwenden zu können, müssen wir zuerst einen Cluster erstellen. Wenn Sie bereits über einen Spark-Cluster verfügen, können Sie gleich ein Beispiel testen.

### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks ist eine Apache Spark-basierte Analyseplattform, die mit nur einem Klick eingerichtet werden kann und optimierte Workflows sowie einen interaktiven Arbeitsbereich bietet. Sie wird häufig für die Zusammenarbeit zwischen Datenanalysten, Technikern und Wirtschaftsanalytikern verwendet. Führen Sie die folgenden Schritte aus, um Cognitive Services für Big Data in Azure Databricks zu verwenden:

1. [Erstellen eines Azure Databricks-Arbeitsbereichs](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)
1. [Erstellen eines Spark-Clusters in Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal#create-a-spark-cluster-in-databricks)
1. Installieren von Cognitive Services für Big Data
    * Erstellen einer neuen Bibliothek in Ihrem Databricks-Arbeitsbereich  
       <img src="media/create-library.png" alt="Create library" width="50%"/>
    * Eingeben der folgenden Maven-Koordinaten:  `com.microsoft.ml.spark:mmlspark_2.11:1.0.0-rc3`Repository`https://mmlspark.azureedge.net/maven`:  
      <img src="media/library-coordinates.png" alt="Library Coordinates" width="50%"/>
    * Installieren der Bibliothek auf einem Cluster  
      <img src="media/install-library.png" alt="Install Library on Cluster" width="50%"/>

### <a name="azure-synapse-analytics-optional"></a>Azure Synapse Analytics (optional)

Optional können Sie Synapse Analytics zum Erstellen eines Spark-Clusters verwenden. Azure Synapse vereint Data Warehousing für Unternehmen mit Big Data-Analysen. Er ermöglicht flexible Datenabfragen nach Ihren Vorstellungen, indem serverlose On-Demand-Ressourcen oder bereitgestellten Ressourcen im gewünschten Umfang verwendet werden. Für erste Schritte mit Azure Synapse Analytics gehen Sie folgendermaßen vor:

1. [Erstellen Sie einen Synapse-Arbeitsbereich (Vorschau)](../../synapse-analytics/quickstart-create-workspace.md).
1. [Erstellen Sie einen neuen serverlosen Apache Spark-Pool über das Azure-Portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md).

In Azure Synapse Analytics wird Big Data für Cognitive Services standardmäßig installiert.

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Wenn Sie Cognitive Services in Containern verwenden, ist eine beliebte Option für die Bereitstellung von Spark neben Containern der Azure Kubernetes-Dienst.

Führen Sie zum Einstieg in den Azure Kubernetes-Dienst die folgenden Schritte aus:

1. [Schnellstart: Bereitstellen eines AKS-Clusters (Azure Kubernetes Service) über das Azure-Portal](../../aks/kubernetes-walkthrough-portal.md)
1. [Installieren des Apache Spark 2.4.0-Helm-Charts](https://hub.helm.sh/charts/microsoft/spark)
1. [Installieren eines Cognitive Service-Containers mithilfe von Helm](../computer-vision/deploy-computer-vision-on-premises.md)

## <a name="try-a-sample"></a>Beispiel ausprobieren

Nachdem Sie den Spark-Cluster und die Umgebung eingerichtet haben, können wir ein kurzes Beispiel ausführen. In diesem Abschnitt wird die Verwendung von Big Data für Cognitive Services in Azure Databricks veranschaulicht.

Zunächst können wir ein Notebooks in Azure Databricks erstellen. Verwenden Sie für andere Spark-Clusteranbieter deren Notebooks oder Spark Submit.

1. Erstellen Sie ein neues Databricks-Notebook, indem Sie im Menü **Azure Databricks** die Option **Neues Notebook** auswählen.

    <img src="media/new-notebook.png" alt="Create a new notebook" width="50%"/>

1. Geben Sie im Dialogfeld **Notizbuch erstellen** einen Namen ein, wählen Sie **Python** als Sprache und dann den zuvor erstellten Spark-Cluster aus.

    <img src="media/databricks-notebook-details.jpg" alt="New notebook details" width="50%"/>

    Klicken Sie auf **Erstellen**.

1. Fügen Sie diesen Codeausschnitt in Ihr neues Notebook ein.

```python
from mmlspark.cognitive import *
from pyspark.sql.functions import col

# Add your subscription key from Text Analytics (or a general Cognitive Service key)
service_key = "ADD-SUBSCRIPTION-KEY-HERE"

df = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US"),
], ["text", "language"])

sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

results = sentiment.transform(df)

# Show the results in a table
display(results.select("text", col("sentiment")[0].getItem("score").alias("sentiment")))

```

1. Beziehen Sie Ihren Abonnementschlüssel über das Menü **Schlüssel und Endpunkt** aus dem Textanalyse-Dashboard im Azure-Portal.
1. Ersetzen Sie den Platzhalter für den Abonnementschlüssel im Code Ihres Databricks-Notebook durch Ihren Abonnementschlüssel.
1. Wählen Sie das Wiedergabesymbol – bzw. das Dreieck – rechts oben in der Notebook-Zelle aus, um das Beispiel auszuführen. Wählen Sie optional **Alle** am Anfang des Notebooks aus, um alle Zellen auszuführen. Die Antworten werden unterhalb der Zelle in einer Tabelle angezeigt.

### <a name="expected-results"></a>Erwartete Ergebnisse

| text                                      |   Stimmung |
|:------------------------------------------|------------:|
| Ich bin heute so glücklich, die Sonne scheint!           |   0,978959  |
| Ich bin frustriert über diesen Berufsverkehr. |   0,0237956 |
| Cognitive Services in Spark sind gar nicht schlecht  |   0,888896  |

## <a name="next-steps"></a>Nächste Schritte

- [Kurze Python-Beispiele](samples-python.md)
- [Kurze Scala-Beispiele](samples-scala.md)
- [Anleitung: Predictive Maintenance](recipes/anomaly-detection.md)
- [Anleitung: Intelligente Erkundung von Kunstwerken](recipes/art-explorer.md)
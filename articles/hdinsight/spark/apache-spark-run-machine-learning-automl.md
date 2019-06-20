---
title: Ausführen von Azure Machine Learning-Workloads mit automatisiertem maschinellem Lernen (AutoML) in Apache Spark in Azure HDInsight
description: Hier erfahren Sie, wie Sie Azure Machine Learning-Workloads mit automatisiertem maschinellem Lernen (AutoML) in Apache Spark in Azure HDInsight ausführen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2019
ms.openlocfilehash: 5135de0fc87af227073f96c653d928ace1a50fd0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64917054"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>Ausführen von Azure Machine Learning-Workloads mit automatisiertem maschinellem Lernen (AutoML) in Apache Spark in Azure HDInsight

Azure Machine Learning vereinfacht und beschleunigt die Erstellung, das Training und die Bereitstellung von Machine Learning-Modellen. Beim automatisierten maschinellen Lernen (AutoML) beginnen Sie mit Trainingsdaten mit einer definierten Zielfunktion und iterieren dann durch Kombinationen von Algorithmen und Featureauswahlen, um automatisch das beste Modell für Ihre Daten basierend auf den Trainingswerten auszuwählen. HDInsight ermöglicht es Kunden, Cluster mit Hunderten von Knoten bereitzustellen. AutoML, das unter Spark in einem HDInsight-Cluster ausgeführt wird, ermöglicht es Benutzern, die Computekapazität knotenübergreifend zu nutzen, um Trainingsaufträge mit horizontaler Skalierung sowie mehrere Trainingsaufträge parallel auszuführen. Dadurch können Benutzer AutoML-Experimente durchführen, während sie den Computevorgang mit ihren anderen Big-Data-Workloads teilen.
 

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Installieren von Azure Machine Learning in einem HDInsight-Cluster

Allgemeine Tutorials zum automatisierten maschinellen Lernen finden Sie unter [Tutorial: Erstellen Ihres Regressionsmodells mit automatisiertem Machine Learning](../../machine-learning/service/tutorial-auto-train-models.md).
Auf allen neuen HDInsight-Spark-Clustern ist das AzureML-AutoML SDK vorinstalliert. Sie können mit AutoML unter HDInsight mit diesem [Jupyter-Beispielnotebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-hdi) beginnen. Dieses Jupyter Notebook veranschaulicht, wie Sie eine automatisierte Machine Learning-Klassifizierung für ein einfaches Klassifizierungsproblem verwenden.

> [!Note]
> Azure Machine Learning-Pakete werden in einer Python3-Conda-Umgebung installiert. Das installierte Jupyter Notebook sollte mit dem PySpark3-Kernel ausgeführt werden.

Alternativ können Sie auch Zeppelin-Notebooks für AutoML verwenden.

> [!Note]
> Zeppelin weist ein [bekanntes Problem](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) auf, bei dem PySpark3 nicht die richtige Version von Python auswählt. Verwenden Sie dazu die dokumentierte Problemumgehung.

## <a name="authentication-for-workspace"></a>Authentifizierung für Arbeitsbereich

Zum Erstellen von Arbeitsbereichen und Übermitteln von Experimenten benötigen Sie ein Authentifizierungstoken. Dieses Token können Sie mit einer [Azure AD-Anwendung](../../active-directory/develop/app-objects-and-service-principals.md) generieren. Mit einem [Azure AD-Benutzer](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python) können Sie auch das erforderliche Authentifizierungstoken generieren, wenn die mehrstufige Authentifizierung für das Konto nicht aktiviert ist.  

Im folgenden Codeausschnitt wird ein Authentifizierungstoken mit einer **Azure AD-Anwendung** erstellt.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
                tenant_id = '<Azure Tenant ID>',
                service_principal_id = '<Azure AD Application ID>',
                service_principal_password = '<Azure AD Application Key>'
                )
```
Im folgenden Codeausschnitt wird ein Authentifizierungstoken mit einem **Azure AD-Benutzer** erstellt.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com','my_smart_password')
```

## <a name="loading-dataset"></a>Laden von Datasets

Automatisiertes maschinelles Lernen in Spark verwendet automatisierte **Dataflows**. Dabei handelt es sich um verzögert ausgewertete, unveränderliche Vorgänge für Daten.  Ein Dataflow kann ein Dataset aus einem Blob mit öffentlichem Lesezugriff oder über eine Blob-URL mit einem SAS-Token laden.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

Sie können zudem den Datenspeicher mit einer einmaligen Registrierung beim Arbeitsbereich registrieren.

## <a name="experiment-submission"></a>Experimentübermittlung

In der [Konfiguration für automatisiertes maschinelles Lernen](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig) legen Sie die Eigenschaft `spark_context` fest, um das Paket im verteilten Modus auszuführen. Mit der Eigenschaft `concurrent_iterations` bestimmen Sie, wie viele Iterationen parallel ausgeführt werden dürfen. Legen Sie eine Zahl fest, die kleiner ist als die Anzahl von Executorkernen für die Spark-App.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur Motivation hinter automatisiertem Machine Learning finden Sie im Blogbeitrag [Release models at pace using Microsoft’s automated machine learning!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/) (Schnelles Veröffentlichen von Modellen mit automatisiertem maschinellem Lernen von Microsoft).
* Weitere Informationen zum Verwenden von Azure ML-AutoML-Funktionen finden Sie unter [Neue automatisierte Funktionen für maschinelles Lernen im Azure Machine Learning-Dienst](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/).
* [AutoML-Projekt von Microsoft Research](https://www.microsoft.com/research/project/automl/)

---
title: Exportieren oder Löschen von Arbeitsbereichsdaten
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie Ihren Arbeitsbereich mit dem Azure-Portal, der CLI, dem SDK und authentifizierten REST-APIs exportieren oder löschen können.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: ph-com
ms.author: pahusban
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 3b55282adf56bedb0724eca578b8eaea00c26074
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65023975"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Exportieren oder Löschen Ihrer Arbeitsbereichsdaten im Machine Learning-Dienst 

In Azure Machine Learning können Sie Ihre Arbeitsbereichsdaten mit der authentifizierten REST-API exportieren oder löschen. In diesem Artikel erfahren Sie, wie das geht.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Steuern Ihrer Arbeitsbereichsdaten
Im Produkt enthaltene und von Azure Machine Learning Services gespeicherte Daten stehen über das Azure-Portal, die CLI, das SDK und authentifizierte REST-APIs zum Exportieren und Löschen zur Verfügung. Auf Telemetriedaten können Sie über das Azure Privacy-Portal zugreifen. 

In Azure Machine Learning Services bestehen personenbezogene Daten aus Benutzerinformationen in Ausführungsverlaufsdokumenten und Telemetriedatensätzen einiger Benutzerinteraktionen mit dem Dienst.

## <a name="delete-workspace-data-with-the-rest-api"></a>Löschen von Arbeitsbereichsdaten mit der REST-API 

Um Daten zu löschen, können die folgenden API-Aufrufe mit dem HTTP DELETE-Verb vorgenommen werden. Diese werden durch einen `Authorization: Bearer <arm-token>`-Header in der Anforderung autorisiert, wobei `<arm-token>` das AAD-Zugriffstoken für den Endpunkt `https://management.core.windows.net/` ist.  

Wie Sie dieses Token erhalten und Azure-Endpunkte aufrufen, erfahren Sie in der [REST-API-Dokumentation von Azure](https://docs.microsoft.com/rest/api/azure/).  

In den folgenden Beispielen ersetzen Sie den Text in {} durch die Instanznamen, die die zugehörige Ressource bestimmen.

### <a name="delete-an-entire-workspace"></a>Löschen eines gesamten-Arbeitsbereichs

Verwenden Sie diesen Aufruf zum Löschen eines ganzen Arbeitsbereichs.  
> [!WARNING]
> Alle Informationen werden gelöscht, und der Arbeitsbereich kann nicht mehr verwendet werden.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="delete-models"></a>Löschen von Modellen

Verwenden Sie diesen Aufruf, um eine Liste der Modelle und ihrer IDs zu erhalten:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Einzelne Modelle können wie folgt gelöscht werden:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="delete-assets"></a>Löschen von Assets

Verwenden Sie diesen Aufruf, um eine Liste der Assets und ihrer IDs zu erhalten:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Einzelne Assets können wie folgt gelöscht werden:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="delete-images"></a>Löschen von Images

Verwenden Sie diesen Aufruf, um eine Liste der Images und ihrer IDs zu erhalten:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Einzelne Images können wie folgt gelöscht werden:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="delete-services"></a>Löschen von Diensten

Verwenden Sie diesen Aufruf, um eine Liste der Dienste und ihrer IDs zu erhalten:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Einzelne Dienste können wie folgt gelöscht werden:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

## <a name="export-service-data-with-the-rest-api"></a>Exportieren von Dienstdaten mit der REST-API

Um Daten zu exportieren, können die folgenden API-Aufrufe mit dem HTTP GET-Verb vorgenommen werden. Diese werden durch einen `Authorization: Bearer <arm-token>`-Header in der Anforderung autorisiert, wobei `<arm-token>` das AAD-Zugriffstoken für den Endpunkt `https://management.core.windows.net/` ist.  

Wie Sie dieses Token erhalten und Azure-Endpunkte aufrufen, erfahren Sie in der [REST-API-Dokumentation von Azure](https://docs.microsoft.com/rest/api/azure/).   

In den folgenden Beispielen ersetzen Sie den Text in {} durch die Instanznamen, die die zugehörige Ressource bestimmen.

### <a name="export-workspace-information"></a>Exportieren von Arbeitsbereichsinformationen

Verwenden Sie diesen Aufruf, um eine Liste aller Arbeitsbereiche zu erhalten:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2018-03-01-preview

Informationen zu einem einzelnen Arbeitsbereich können folgendermaßen abgerufen werden:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="export-compute-information"></a>Exportieren von Compute-Informationen

Alle Computeziele, die einem Arbeitsbereich zugeordnet sind, können folgendermaßen abgerufen werden:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

Informationen zu einem einzigen Computeziel können folgendermaßen abgerufen werden:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2018-03-01-preview

### <a name="export-run-history-data"></a>Exportieren von Ausführungsverlaufsdaten
Verwenden Sie diesen Aufruf, um eine Liste aller Experimente und ihrer Informationen zu erhalten:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments 

Alle Ausführungen für ein bestimmtes Experiment können folgendermaßen abgerufen werden:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs 

Elemente des Ausführungsverlaufs können wie folgt abgerufen werden:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId} 

Alle Ausführungsmetriken für ein Experiment können folgendermaßen abgerufen werden:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics 

Eine einzelne Ausführungsmetrik kann wie folgt abgerufen werden:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}
    
### <a name="export-artifacts"></a>Exportieren von Artefakten

Verwenden Sie diesen Aufruf, um eine Liste der Artefakte und ihrer Pfade zu erhalten:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}
    
### <a name="export-notifications"></a>Exportieren von Benachrichtigungen

Verwenden Sie diesen Aufruf, um eine Liste gespeicherter Aufgaben zu erhalten:     

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks

Benachrichtigungen für eine einzelne Aufgabe können folgendermaßen abgerufen werden:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}

### <a name="export-data-stores"></a>Exportieren von Datenspeichern

Verwenden Sie diesen Aufruf, um eine Liste von Datenspeichern zu erhalten:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores

Einzelne Datenspeicher können wie folgt abgerufen werden:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}

### <a name="export-models"></a>Exportieren von Modellen

Verwenden Sie diesen Aufruf, um eine Liste der Modelle und ihrer IDs zu erhalten:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Einzelne Modelle können wie folgt abgerufen werden:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="export-assets"></a>Exportieren von Assets

Verwenden Sie diesen Aufruf, um eine Liste der Assets und ihrer IDs zu erhalten:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Einzelne Assets können wie folgt abgerufen werden:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="export-images"></a>Exportieren von Images

Verwenden Sie diesen Aufruf, um eine Liste der Images und ihrer IDs zu erhalten:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Einzelne Images können wie folgt abgerufen werden:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="export-services"></a>Exportieren von Diensten

Verwenden Sie diesen Aufruf, um eine Liste der Dienste und ihrer IDs zu erhalten:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Einzelne Dienste können wie folgt abgerufen werden:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

### <a name="export-pipeline-experiments"></a>Exportieren von Pipelineexperimenten

Einzelne Experimente können wie folgt abgerufen werden:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}

### <a name="export-pipeline-graphs"></a>Exportieren von Pipelinediagrammen

Einzelne Diagramme können wie folgt abgerufen werden:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}

### <a name="export-pipeline-modules"></a>Exportieren von Pipelinemodulen

Module können folgendermaßen abgerufen werden:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}

### <a name="export-pipeline-templates"></a>Exportieren von Pipelinevorlagen

Vorlagen können folgendermaßen abgerufen werden:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}

### <a name="export-pipeline-data-sources"></a>Exportieren von Pipelinedatenquellen

Datenquellen können folgendermaßen abgerufen werden:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}

## <a name="delete-visual-interface-assets"></a>Löschen von Objekten der grafischen Benutzeroberfläche

Löschen Sie auf der grafischen Benutzeroberfläche, auf der Sie Ihr Experiment erstellt haben, einzelne Objekte:

1. Wählen Sie auf der linken Seite den Typ der Ressource aus, die Sie löschen möchten.

    ![Löschen von Assets](media/how-to-export-delete-data.md/delete-experiment.png)

1. Wählen Sie in der Liste die zu löschenden Objekte einzeln aus.

1. Wählen Sie unten die Option **Löschen** aus.

## <a name="export-visual-interface-data"></a>Exportieren von Daten der grafischen Benutzeroberfläche

Exportieren Sie auf der grafischen Benutzeroberfläche, auf der Sie Ihr Experiment erstellt haben, von Ihnen hinzugefügte Daten:

1. Wählen Sie links die Option **Daten** aus.

1. Wählen Sie oben die Option **Meine Datasets** oder **Beispiele** aus, um die Daten zu suchen, die Sie exportieren möchten.

    ![Herunterladen der Daten](media/how-to-export-delete-data.md/download-data.png)

1. Wählen Sie in der Liste die zu exportierenden Datasets einzeln aus.

1. Wählen Sie unten die Option **Herunterladen** aus.
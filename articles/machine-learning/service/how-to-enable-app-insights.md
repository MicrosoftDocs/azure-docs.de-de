---
title: Einrichten von Azure Application Insights für die Überwachung von Machine Learning-Modellen
titleSuffix: Azure Machine Learning service
description: Überwachen Sie Webdienste, die mit dem Azure Machine Learning Service bereitgestellt werden, mithilfe von Azure Application Insights.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 04/02/2019
ms.custom: seoapril2019
ms.openlocfilehash: 2e481a388d8cbd6baf66b95c74449396b2e70f7d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60820231"
---
# <a name="monitor-your-azure-machine-learning-models-with-application-insights"></a>Überwachen Ihrer Azure Machine Learning-Modelle mit Application Insights

In diesem Artikel erfahren Sie, wie Sie Azure Application Insights für Ihren Azure Machine Learning-Dienst einrichten. In Application Insights haben Sie die Möglichkeit, Folgendes zu überwachen:
* Anforderungsraten, Antwortzeiten und Fehlerraten
* Abhängigkeitsraten, Antwortzeiten und Fehlerraten
* Ausnahmen.

[Erfahren Sie mehr über Application Insights](../../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie heute die [kostenlose oder kostenpflichtige Version des von Azure Machine Learning Service](https://aka.ms/AMLFree) aus.

* Ein Azure Machine Learning-Arbeitsbereich, ein lokales Verzeichnis mit Ihren Skripts und das Azure Machine Learning SDK für Python müssen installiert sein. Informationen zum Erfüllen dieser Voraussetzungen finden Sie unter [Konfigurieren einer Entwicklungsumgebung](how-to-configure-environment.md).
* Ein trainiertes Machine Learning-Modell, das in Azure Kubernetes Service (AKS) oder Azure Container Instance (ACI) bereitgestellt werden soll. Wenn Sie keines besitzen, sehen Sie sich das Tutorial zum [Trainieren eines Imageklassifizierungsmodells](tutorial-train-models-with-aml.md) an.


## <a name="use-sdk-to-configure"></a>Verwenden des SDKs zum Konfigurieren 

### <a name="update-a-deployed-service"></a>Aktualisieren eines bereitgestellten Diensts
1. Suchen Sie den Dienst in Ihrem Arbeitsbereich. Der Wert für `ws` ist der Name des Arbeitsbereichs.

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Aktualisieren Sie Ihren Dienst, und aktivieren Sie Application Insights. 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Protokollieren von benutzerdefinierten Ablaufverfolgungen in Ihrem Dienst
Wenn Sie benutzerdefinierte Ablaufverfolgungen protokollieren möchten, führen Sie den im Dokument [Bereitstellung: wie und wo?](how-to-deploy-and-where.md) beschriebenen Standardbereitstellungsprozess für AKS oder ACI aus. Führen Sie dann die folgenden Schritte aus:

1. Aktualisieren Sie die Bewertungsdatei durch Hinzufügen von Druckanweisungen.
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Aktualisieren Sie die Dienstkonfiguration.
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Erstellen Sie ein Image, und stellen Sie es in [AKS](how-to-deploy-to-aks.md) oder [ACI](how-to-deploy-to-aci.md) bereit.  

### <a name="disable-tracking-in-python"></a>Deaktivieren der Nachverfolgung in Python

Verwenden Sie den folgenden Code, um Application Insights zu deaktivieren:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    
## <a name="use-portal-to-configure"></a>Verwenden des Portals zum Konfigurieren

Sie können Application Insights im Azure-Portal aktivieren und deaktivieren.

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihren Arbeitsbereich.

1. Wählen Sie auf der Registerkarte **Bereitstellungen** den Dienst aus, für den Sie Application Insights aktivieren möchten.

   [![Liste der Dienste auf der Registerkarte „Bereitstellungen“](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Wählen Sie **Bearbeiten** aus.

   [![Schaltfläche „Bearbeiten“](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. Aktivieren Sie in **Erweiterte Einstellungen** das Kontrollkästchen **AppInsights-Diagnose aktivieren**.

   [![Aktiviertes Kontrollkästchen zum Aktivieren der Diagnose](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Wählen Sie unten auf dem Bildschirm **Aktualisieren** aus, um die Änderungen zu übernehmen. 

### <a name="disable"></a>Disable
1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihren Arbeitsbereich.
1. Wählen Sie **Bereitstellungen**, den Dienst und dann **Bearbeiten** aus.

   [![Verwenden der Schaltfläche „Bearbeiten“](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. Deaktivieren Sie in **Erweiterte Einstellungen** das Kontrollkästchen **AppInsights-Diagnose aktivieren**. 

   [![Deaktiviertes Kontrollkästchen zum Aktivieren der Diagnose](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Wählen Sie unten auf dem Bildschirm **Aktualisieren** aus, um die Änderungen zu übernehmen. 
 

## <a name="evaluate-data"></a>Evaluieren von Daten
Die Daten Ihres Diensts werden in Ihrem Application Insights-Konto in der gleichen Ressourcengruppe gespeichert, in dem sich auch Ihr Azure Machine Learning-Dienst befindet.
So zeigen Sie sie an:
1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Machine Learning Service-Arbeitsbereich, und klicken Sie auf den Link „Application Insights“.

    [![AppInsightsLoc](media/how-to-enable-app-insights/AppInsightsLoc.png)](./media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Wählen Sie die Registerkarte **Übersicht** aus, um einen Standardsatz von Metriken für Ihren Dienst anzuzeigen.

   [![Übersicht](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

3. Um Ihre benutzerdefinierten Ablaufverfolgungen anzuzeigen, wählen Sie **Analyse** aus.
4. Wählen Sie im Abschnitt „Schema“ den Eintrag **Ablaufverfolgungen** aus. Wählen Sie dann **Ausführen** aus, um die Abfrage auszuführen. Die Daten sollten in einem Tabellenformat angezeigt werden und den benutzerdefinierten Aufrufen in Ihrer Bewertungsdatei zugeordnet sein. 

   [![Benutzerdefinierte Ablaufverfolgungen](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Weitere Informationen zu Application Insights finden Sie unter [Was ist Application Insights?](../../azure-monitor/app/app-insights-overview.md)
    

## <a name="example-notebook"></a>Notebook mit Beispielen

Das Notebook [how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) veranschaulicht die Konzepte in diesem Artikel. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nächste Schritte
Sie können Daten auch in Ihren Modellen in Produktionsumgebungen sammeln. Lesen Sie den Artikel [Sammeln von Daten für Modelle in der Produktion](how-to-enable-data-collection.md). 

Weitere Informationen finden Sie unter [Azure Monitor für Container](https://docs.microsoft.com/azure/monitoring/monitoring-container-insights-overview?toc=%2fazure%2fmonitoring%2ftoc.json).

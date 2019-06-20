---
title: Bereitstellung und Nutzung
titleSuffix: Azure Machine Learning Studio
description: Mit Azure Machine Learning Studio können Sie Machine Learning-Workflows und -Modelle als Webdienste bereitstellen. Diese Webdienste können dann verwendet werden, um die Machine Learning-Modelle in Anwendungen über das Internet aufzurufen und Vorhersagen im Echtzeit- oder Batchmodus zu nutzen.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: 0a29d763ab54ee716e514df23576e9c3b294d792
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60751075"
---
# <a name="azure-machine-learning-studio-web-services-deployment-and-consumption"></a>Azure Machine Learning Studio-Webdienste: Bereitstellung und Nutzung

Mit Azure Machine Learning Studio können Sie Machine Learning-Workflows und -Modelle als Webdienste bereitstellen. Diese Webdienste können dann verwendet werden, um die Machine Learning-Modelle in Anwendungen über das Internet aufzurufen und Vorhersagen im Echtzeit- oder Batchmodus zu nutzen. Da die Webdienste RESTful sind, können Sie sie über verschiedene Programmiersprachen und Plattformen wie etwa .NET und Java sowie über Anwendungen wie Excel aufrufen.

Die nächsten Abschnitte enthalten Links zu exemplarischen Vorgehensweisen, Code und Dokumentationen, die Ihnen beim Einstieg helfen.

## <a name="deploy-a-web-service"></a>Bereitstellen eines Webdiensts

### <a name="with-azure-machine-learning-studio"></a>Mit Azure Machine Learning Studio

Im Studio-Portal und Microsoft Azure Machine Learning Web Services-Portal können Sie einen Webdienst bereitstellen und verwalten, ohne Code schreiben zu müssen.

Unter den folgenden Links finden Sie allgemeine Informationen zur Bereitstellung eines neuen Webdiensts:

* Eine Übersicht über das Bereitstellen eines neuen Azure Resource Manager-basierten Webdiensts finden Sie unter [Bereitstellen eines neuen Webdiensts](publish-a-machine-learning-web-service.md).
* Eine exemplarische Vorgehensweise zum Bereitstellen eines Webdiensts finden Sie unter [Bereitstellen eines Azure Machine Learning-Webdiensts](publish-a-machine-learning-web-service.md).
* Eine umfassende exemplarische Vorgehensweise zum Erstellen und Bereitstellen eines Webdiensts finden Sie unter [Tutorial 1: Vorhersagen des Kreditrisikos](tutorial-part1-credit-risk.md).
* Spezifische Beispiele für das Bereitstellen eines Webdiensts finden Sie hier:

  * [Tutorial 3: Bereitstellen eines Kreditrisikomodells](tutorial-part3-credit-risk-deploy.md)
  * [Gewusst wie: Bereitstellen eines Webdiensts in mehreren Regionen](/azure/machine-learning/studio/publish-a-machine-learning-web-service#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Mit Webdienste-Ressourcenanbieter-APIs (Azure Resource Manager-APIs)

Der Azure Machine Learning Studio-Ressourcenanbieter für Webdienste ermöglicht die Bereitstellung und Verwaltung von Webdiensten mithilfe von REST-API-Aufrufen. Weitere Informationen finden Sie in der Referenz zu [Machine Learning Web Service (REST)](/rest/api/machinelearning/index).

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>Mit PowerShell-Cmdlets

Der Azure Machine Learning Studio-Ressourcenanbieter für Webdienste ermöglicht die Bereitstellung und Verwaltung von Webdiensten mithilfe von PowerShell-Cmdlets.

Um die Cmdlets verwenden zu können, müssen Sie sich innerhalb Ihrer PowerShell-Umgebung zunächst mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) bei Ihrem Azure-Konto anmelden. Wenn Sie mit dem Aufrufen Resource Manager-basierter PowerShell-Befehle nicht vertraut sind, nutzen Sie die Informationen unter [Verwenden von Azure PowerShell mit Azure Resource Manager](../../azure-resource-manager/manage-resources-powershell.md).

Verwenden Sie [diesen Beispielcode](https://github.com/ritwik20/AzureML-WebServices), um Ihr Vorhersageexperiment zu exportieren. Nachdem Sie die EXE-Datei auf der Grundlage des Codes erstellt haben, können Sie Folgendes eingeben:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Durch Ausführen der Anwendung wird eine JSON-Webdienstvorlage erstellt. Fügen Sie folgende Informationen hinzu, um mithilfe dieser Vorlage einen Webdienst bereitzustellen:

* Speicherkontoname und -schlüssel

    Den Namen und Schlüssel des Speicherkontos können Sie über das [Azure-Portal](https://portal.azure.com/) abrufen.
* Vertragsplan-ID

    Die Plan-ID können Sie über das [Azure Machine Learning-Webdiensteportal](https://services.azureml.net) ermitteln, indem Sie sich anmelden und auf einen Plannamen klicken.

Fügen Sie der JSON-Vorlage die Werte als untergeordnete Elemente des Knotens *Eigenschaften* auf der Ebene des Knotens *MachineLearningWorkspace* hinzu.

Hier sehen Sie ein Beispiel:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Ausführlichere Informationen finden Sie in den folgenden Artikeln sowie im Beispielcode:

* Referenz zu [Azure Machine Learning Studio-Cmdlets](https://docs.microsoft.com/powershell/module/az.machinelearning) auf MSDN
* [Exemplarische Vorgehensweise](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) auf GitHub

## <a name="consume-the-web-services"></a>Nutzen von Webdiensten

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Über die Benutzeroberfläche der Azure Machine Learning-Webdienste (Testen)

Sie können Ihren Webdienst über das Azure Machine Learning-Webdiensteportal testen. Dies schließt das Testen der Schnittstellen des Request-Response Service (RRS) und des Stapelausführungsdiensts (Batch Execution Service, BES) ein.

* [Bereitstellen eines neuen Webdiensts](publish-a-machine-learning-web-service.md)
* [Bereitstellen eines Azure Machine Learning-Webdiensts](publish-a-machine-learning-web-service.md)
* [Tutorial 3: Bereitstellen eines Kreditrisikomodells](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Über Excel

Sie können eine Excel-Vorlage zur Nutzung des Webdiensts herunterladen:

* [Verwenden eines Azure Machine Learning-Webdiensts aus Excel](consuming-from-excel.md)
* [Excel-Add-In für Azure Machine Learning-Webdienste](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Über einen REST-basierten Client

Azure Machine Learning-Webdienste sind RESTful-APIs. Diese APIs können über verschiedene Plattformen (.NET, Python, R, Java usw.) genutzt werden. Im [Microsoft Azure Machine Learning-Webdiensteportal](https://services.azureml.net) steht auf der **Nutzungsseite** Ihres Webdiensts Beispielcode zur Verfügung, der Ihnen den Einstieg erleichtert. Weitere Informationen finden Sie unter [Nutzen eines Azure Machine Learning-Webdiensts](consume-web-services.md).

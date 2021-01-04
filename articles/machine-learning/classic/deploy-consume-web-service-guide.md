---
title: 'ML Studio (Classic): Bereitstellung und Nutzung (Azure)'
description: Mit Azure Machine Learning Studio (klassisch) können Sie Machine Learning-Workflows und -Modelle als Webdienste bereitstellen. Diese Webdienste können dann verwendet werden, um die Machine Learning-Modelle in Anwendungen über das Internet aufzurufen und Vorhersagen im Echtzeit- oder Batchmodus zu nutzen.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: dc71dc87ca972c52fabb91a6412eec702eee5460
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93307435"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Azure Machine Learning Studio-Webdienste (klassisch): Bereitstellung und Nutzung

**GILT FÜR:**  ![Gilt für ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (Classic) ![Gilt nicht für ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Mit Azure Machine Learning Studio (klassisch) können Sie Machine Learning-Workflows und -Modelle als Webdienste bereitstellen. Diese Webdienste können dann verwendet werden, um die Machine Learning-Modelle in Anwendungen über das Internet aufzurufen und Vorhersagen im Echtzeit- oder Batchmodus zu nutzen. Da die Webdienste RESTful sind, können Sie sie über verschiedene Programmiersprachen und Plattformen wie etwa .NET und Java sowie über Anwendungen wie Excel aufrufen.

Die nächsten Abschnitte enthalten Links zu exemplarischen Vorgehensweisen, Code und Dokumentationen, die Ihnen beim Einstieg helfen.

## <a name="deploy-a-web-service"></a>Bereitstellen eines Webdiensts

### <a name="with-azure-machine-learning-studio-classic"></a>Mit Azure Machine Learning Studio (klassisch)

Im (klassischen) Studio-Portal und Webdienstportal von Microsoft Azure Machine Learning können Sie einen Webdienst bereitstellen und verwalten, ohne Code schreiben zu müssen.

Unter den folgenden Links finden Sie allgemeine Informationen zur Bereitstellung eines neuen Webdiensts:

* Eine Übersicht über das Bereitstellen eines neuen Azure Resource Manager-basierten Webdiensts finden Sie unter [Bereitstellen eines neuen Webdiensts](deploy-a-machine-learning-web-service.md).
* Eine exemplarische Vorgehensweise zum Bereitstellen eines Webdiensts finden Sie unter [Bereitstellen eines Azure Machine Learning-Webdiensts](deploy-a-machine-learning-web-service.md).
* Eine umfassende exemplarische Vorgehensweise zum Erstellen und Bereitstellen eines Webdiensts finden Sie unter [Tutorial 1: Vorhersagen des Kreditrisikos](tutorial-part1-credit-risk.md).
* Spezifische Beispiele für das Bereitstellen eines Webdiensts finden Sie hier:

  * [Tutorial 3: Bereitstellen eines Kreditrisikomodells](tutorial-part3-credit-risk-deploy.md)
  * [Gewusst wie: Bereitstellen eines Webdiensts in mehreren Regionen](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Mit Webdienste-Ressourcenanbieter-APIs (Azure Resource Manager-APIs)

Der Azure Machine Learning Studio-Ressourcenanbieter (klassisch) für Webdienste ermöglicht die Bereitstellung und Verwaltung von Webdiensten mithilfe von REST-API-Aufrufen. Weitere Informationen finden Sie in der Referenz zu [Machine Learning Web Service (REST)](/rest/api/machinelearning/index).

<!-- [Machine Learning Web Service (REST)](/rest/api/machinelearning/webservices) reference. -->

### <a name="with-powershell-cmdlets"></a>Mit PowerShell-Cmdlets

Der Azure Machine Learning Studio-Ressourcenanbieter (klassisch) für Webdienste ermöglicht die Bereitstellung und Verwaltung von Webdiensten mithilfe von PowerShell-Cmdlets.

Um die Cmdlets verwenden zu können, müssen Sie sich innerhalb Ihrer PowerShell-Umgebung zunächst mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) bei Ihrem Azure-Konto anmelden. Wenn Sie mit dem Aufrufen Resource Manager-basierter PowerShell-Befehle nicht vertraut sind, nutzen Sie die Informationen unter [Verwenden von Azure PowerShell mit Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md).

Verwenden Sie [diesen Beispielcode](https://github.com/ritwik20/AzureML-WebServices), um Ihr Vorhersageexperiment zu exportieren. Nachdem Sie die EXE-Datei auf der Grundlage des Codes erstellt haben, können Sie Folgendes eingeben:

```azurepowershell
C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>
```

Durch Ausführen der Anwendung wird eine JSON-Webdienstvorlage erstellt. Fügen Sie folgende Informationen hinzu, um mithilfe dieser Vorlage einen Webdienst bereitzustellen:

* Speicherkontoname und -schlüssel

    Den Namen und Schlüssel des Speicherkontos können Sie über das [Azure-Portal](https://portal.azure.com/) abrufen.
* Vertragsplan-ID

    Die Plan-ID können Sie über das [Azure Machine Learning-Webdiensteportal](https://services.azureml.net) ermitteln, indem Sie sich anmelden und auf einen Plannamen klicken.

Fügen Sie der JSON-Vorlage die Werte als untergeordnete Elemente des Knotens *Eigenschaften* auf der Ebene des Knotens *MachineLearningWorkspace* hinzu.

Hier sehen Sie ein Beispiel:

```json
"StorageAccount": {
        "name": "YourStorageAccountName",
        "key": "YourStorageAccountKey"
},
"CommitmentPlan": {
    "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
}
```

Ausführlichere Informationen finden Sie in den folgenden Artikeln sowie im Beispielcode:

* Referenz zu [(klassischen) Azure Machine Learning Studio-Cmdlets](/powershell/module/az.machinelearning) auf MSDN

## <a name="consume-the-web-services"></a>Nutzen von Webdiensten

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Über die Benutzeroberfläche der Azure Machine Learning-Webdienste (Testen)

Sie können Ihren Webdienst über das Azure Machine Learning-Webdiensteportal testen. Dies schließt das Testen der Schnittstellen des Request-Response Service (RRS) und des Stapelausführungsdiensts (Batch Execution Service, BES) ein.

* [Bereitstellen eines neuen Webdiensts](deploy-a-machine-learning-web-service.md)
* [Bereitstellen eines Azure Machine Learning-Webdiensts](deploy-a-machine-learning-web-service.md)
* [Tutorial 3: Bereitstellen eines Kreditrisikomodells](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Über Excel

Sie können eine Excel-Vorlage zur Nutzung des Webdiensts herunterladen:

* [Verwenden eines Azure Machine Learning-Webdiensts aus Excel](consuming-from-excel.md)
* [Excel-Add-In für Azure Machine Learning-Webdienste](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Über einen REST-basierten Client

Azure Machine Learning-Webdienste sind RESTful-APIs. Diese APIs können über verschiedene Plattformen (.NET, Python, R, Java usw.) genutzt werden. Im [Microsoft Azure Machine Learning-Webdiensteportal](https://services.azureml.net) steht auf der **Nutzungsseite** Ihres Webdiensts Beispielcode zur Verfügung, der Ihnen den Einstieg erleichtert. Weitere Informationen finden Sie unter [Nutzen eines Azure Machine Learning-Webdiensts](consume-web-services.md).
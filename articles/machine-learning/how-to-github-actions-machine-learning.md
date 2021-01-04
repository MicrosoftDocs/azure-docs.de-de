---
title: GitHub Actions für CI/CD
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie einen GitHub Actions-Workflow erstellen, um ein Modell in Azure Machine Learning zu trainieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: juliakm
ms.author: jukullam
ms.date: 10/19/2020
ms.topic: conceptual
ms.custom: github-actions-azure
ms.openlocfilehash: 4336827dc7f8cb45f04e4cef94d79d1e6409d5c0
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92794981"
---
# <a name="use-github-actions-with-azure-machine-learning"></a>Verwenden von GitHub Actions mit Azure Machine Learning

Beginnen Sie mit dem Trainieren eines Modells in Azure Machine Learning mithilfe von [GitHub Actions](https://help.github.com/en/articles/about-github-actions). 

> [!NOTE]
> GitHub Actions für Azure Machine Learning wird wie verfügbar bereitgestellt, und Microsoft bietet nicht im vollen Umfang Support dafür. Wenn Probleme mit einer bestimmten Aktion auftreten, erstellen Sie ein Issue im Repository für diese Aktion. Wenn beispielsweise ein Problem mit der Aktion „aml-deploy“ auftritt, melden Sie das Problem im Repository [https://github.com/Azure/aml-deploy]( https://github.com/Azure/aml-deploy).

## <a name="prerequisites"></a>Voraussetzungen 

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ein GitHub-Konto. Falls Sie noch nicht über ein Konto verfügen, können Sie sich [kostenlos](https://github.com/join) registrieren.  

## <a name="workflow-file-overview"></a>Übersicht über die Workflowdatei

Ein Workflow wird durch eine YAML-Datei im Pfad `/.github/workflows/` in Ihrem Repository definiert. Diese Definition enthält die verschiedenen Schritte und Parameter, die den Workflow bilden.

Die Datei besteht aus vier Abschnitten:

|`Section`  |Aufgaben  |
|---------|---------|
|**Authentifizierung** | 1. Definieren eines Dienstprinzipals. <br /> 2. Erstellen eines GitHub-Geheimnisses. |
|**Herstellen einer Verbindung** | 1. Herstellen einer Verbindung mit dem Machine Learning-Arbeitsbereich <br /> 2. Herstellen einer Verbindung mit einem Computeziel |
|**Ausführen** | 1. Übermitteln Sie eine Trainingsausführung. |
|**Bereitstellen** | 1. Registrieren des Modells bei der Azure Machine Learning-Registrierung 1. Bereitstellen des Modells. |

## <a name="create-repository"></a>Erstellen eines Repositorys

Erstellen Sie ein neues Repository basierend auf der Vorlage [ML Ops with GitHub Actions and Azure Machine Learning](https://github.com/machine-learning-apps/ml-template-azure) (ML Ops mit GitHub Actions und Azure Machine Learning). 

1. Öffnen Sie die [Vorlage](https://github.com/machine-learning-apps/ml-template-azure) auf GitHub. 
2. Klicken Sie auf **Diese Vorlage verwenden**. 

    :::image type="content" source="media/how-to-github-actions-machine-learning/gh-actions-use-template.png" alt-text="Klicken auf „Diese Vorlage verwenden“":::
3. Erstellen Sie basierend auf der Vorlage ein neues Repository. Legen Sie als Namen für das Repository `ml-learning` oder einen Namen Ihrer Wahl fest. 


## <a name="generate-deployment-credentials"></a>Generieren von Anmeldeinformationen für die Bereitstellung

Sie können mit dem Befehl [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) in der [Azure CLI](/cli/azure/) einen [Dienstprinzipal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) erstellen. Führen Sie diesen Befehl mit [Azure Cloud Shell](https://shell.azure.com/) im Azure-Portal oder durch Auswählen der Schaltfläche **Ausprobieren** aus.

```azurecli-interactive
az ad sp create-for-rbac --name "myML" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
```

Ersetzen Sie im obigen Beispiel die Platzhalter durch Ihre Abonnement-ID, den Ressourcengruppennamen und den App-Namen. Die Ausgabe ist ein JSON-Objekt mit den Anmeldeinformationen für die Rollenzuweisung, die ähnlich wie unten Zugriff auf Ihre App Service-App gewähren. Kopieren Sie dieses JSON-Objekt zur späteren Verwendung.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

## <a name="configure-the-github-secret"></a>Konfigurieren des GitHub-Geheimnisses

1. Navigieren Sie in [GitHub](https://github.com/) zu Ihrem Repository, und wählen Sie **Einstellungen > Geheimnisse > Neues Geheimnis hinzufügen** aus.

2. Fügen Sie die gesamte JSON-Ausgabe aus dem Azure CLI-Befehl in das Wertfeld des Geheimnisses ein. Geben Sie dem Geheimnis den Namen `AZURE_CREDENTIALS`.

## <a name="connect-to-the-workspace"></a>Herstellen einer Verbindung mit dem Arbeitsbereich

Verwenden Sie die [Aktion Azure Machine Learning Workspace](https://github.com/marketplace/actions/azure-machine-learning-workspace) (Azure Machine Learning-Arbeitsbereich), um eine Verbindung mit Ihrem Azure Machine Learning-Arbeitsbereich herzustellen. 

```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

Standardmäßig erwartet die Aktion eine Datei namens `workspace.json`. Wenn Ihre JSON-Datei anders heißt, können Sie sie mit dem Eingabeparameter `parameters_file` angeben. Wenn keine Datei vorhanden ist, wird eine neue mit dem Repositorynamen erstellt.


```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          parameters_file: "alternate_workspace.json"
```
Die Aktion schreibt die Azure Resource Manager-Eigenschaften (ARM) des Arbeitsbereichs in eine Konfigurationsdatei, die von allen künftigen Azure Machine Learning-GitHub-Aktionen ausgewählt wird. Diese Datei wird unter `GITHUB_WORKSPACE/aml_arm_config.json` gespeichert. 

## <a name="connect-to-a-compute-target-in-azure-machine-learning"></a>Herstellen einer Verbindung mit einem Computeziel in Azure Machine Learning

Verwenden Sie die [Azure Machine Learning-Computeaktion](https://github.com/Azure/aml-compute), um eine Verbindung mit einem Computeziel in Azure Machine Learning herzustellen.  Wenn das Computeziel vorhanden ist, stellt die Aktion eine Verbindung damit her. Andernfalls erstellt sie ein neues Computeziel. Die [AML Compute-Aktion](https://github.com/Azure/aml-compute) unterstützt nur den Azure ML-Computecluster und Azure Kubernetes Service (AKS). 

```yaml
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```
## <a name="submit-training-run"></a>Übermitteln der Trainingsausführung

Verwenden Sie die [Azure Machine Learning-Trainingsaktion](https://github.com/Azure/aml-run), um ein ScriptRun-Objekt, einen Schätzer oder eine Pipeline an Azure Machine Learning zu übermitteln. 

```yaml
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="register-model-in-registry"></a>Registrieren des Modells bei der Registrierung

Verwenden Sie die [Azure Machine Learning-Modellregistrierungsaktion](https://github.com/Azure/aml-registermodel), um ein Modell bei Azure Machine Learning zu registrieren.

```yaml
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}
```

## <a name="deploy-model-to-azure-machine-learning-to-aci"></a>Bereitstellen eines Modells in Azure Machine Learning in ACI

Verwenden Sie die [Azure Machine Learning-Bereitstellungsaktion](https://github.com/Azure/aml-deploy), um ein Modell bereitzustellen und einen Endpunkt für das Modell zu erstellen. Sie können mit dieser Aktion auch Azure Kubernetes Service bereitstellen. Sehen Sie sich [diesen Beispielworkflow](https://github.com/Azure-Samples/mlops-enterprise-template) für ein Modell an, das in Azure Kubernetes Service bereitgestellt wird.

```yaml
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="complete-example"></a>Vollständiges Beispiel

Trainieren Sie Ihr Modell, und stellen Sie es in Azure Machine Learning bereit. 

```yaml
# Actions train a model on Azure Machine Learning
name: Azure Machine Learning training and deployment
on:
  push:
    branches:
      - master
    # paths:
    #   - 'code/*'
jobs:
  train:
    runs-on: ubuntu-latest
    steps:
    # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
    - name: Check Out Repository
      id: checkout_repository
      uses: actions/checkout@v2
        
    # Connect or Create the Azure Machine Learning Workspace
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Connect or Create a Compute Target in Azure Machine Learning
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Submit a training run to the Azure Machine Learning
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}

    # Register model in Azure Machine Learning model registry
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}

    # Deploy model in Azure Machine Learning to ACI
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Ihre Ressourcengruppe und das Repository nicht mehr benötigt werden, bereinigen Sie die bereitgestellten Ressourcen, indem Sie die Ressourcengruppe und Ihr GitHub-Repository löschen. 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen und Ausführen von Machine Learning-Pipelines mit dem Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)

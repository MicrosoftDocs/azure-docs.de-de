---
title: Verwenden der Azure AD-Identität mit Ihrem Webdienst
titleSuffix: Azure Machine Learning
description: Verwenden Sie die Azure AD-Identität mit Ihrem Webdienst in Azure Kubernetes Service, um während der Bewertung auf Cloudressourcen zuzugreifen.
services: machine-learning
ms.author: larryfr
author: BlackMist
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: bcf5f75cf5cabe42f530a6a179c2cafd43b5520d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952538"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Verwenden der Azure AD-Identität mit Ihrem Machine Learning-Webdienst in Azure Kubernetes Service

In dieser Schrittanleitung wird beschrieben, wie Sie eine Azure AD-Identität (Azure Active Directory) Ihrem bereitgestellten Machine Learning-Modell in Azure Kubernetes Service zuweisen. Beim Projekt [Azure AD-Podidentität](https://github.com/Azure/aad-pod-identity) können Anwendungen per Azure AD auf sichere Weise auf Cloudressourcen zugreifen, indem eine [verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md) und Kubernetes-Primitive genutzt werden. So kann Ihr Webdienst sicher auf Ihre Azure-Ressourcen zugreifen, ohne dass Anmeldeinformationen eingebettet oder Token direkt in Ihrem Skript `score.py` verwaltet werden müssen. In diesem Artikel werden die Schritte zum Erstellen und Installieren einer Azure-Identität in Ihrem Azure Kubernetes Service-Cluster und zum Zuweisen der Identität zu Ihrem bereitgestellten Webdienst beschrieben.

## <a name="prerequisites"></a>Voraussetzungen

- Die [Azure CLI-Erweiterung für den Machine Learning Service](reference-azure-machine-learning-cli.md), das [Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) oder die [Visual Studio Code-Erweiterung für Azure Machine Learning](tutorial-setup-vscode-extension.md).

- Zugriff auf Ihren AKS-Cluster mit dem Befehl `kubectl`. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit dem Cluster](../aks/kubernetes-walkthrough.md#connect-to-the-cluster).

- Ein Azure Machine Learning-Webdienst, der in Ihrem AKS-Cluster bereitgestellt wird.

## <a name="create-and-install-an-azure-identity"></a>Erstellen und Installieren einer Azure-Identität

1. Verwenden Sie den folgenden Befehl, um zu ermitteln, ob für Ihren AKS-Cluster Kubernetes RBAC aktiviert ist:

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    Dieser Befehl gibt den Wert `true` zurück, wenn Kubernetes RBAC aktiviert ist. Anhand dieses Werts wird der Befehl bestimmt, der im nächsten Schritt verwendet werden soll.

1. Installieren Sie die [Azure AD-Podidentität](https://azure.github.io/aad-pod-identity/docs/getting-started/installation/) in Ihrem AKS-Cluster.

1. [Erstellen Sie eine Identität in Azure](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#2-create-an-identity-on-azure), indem Sie die Schritte ausführen, die auf der Seite mit dem Projekt für die Azure AD-Podidentität angezeigt werden.

1. [Stellen Sie eine Azure-Identität (AzureIdentity) bereit](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#3-deploy-azureidentity), indem Sie die Schritte ausführen, die auf der Seite mit dem Projekt für die Azure AD-Podidentität angezeigt werden.

1. [Stellen Sie eine Bindung für die Azure-Identität (AzureIdentityBinding) bereit](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#5-deploy-azureidentitybinding), indem Sie die Schritte ausführen, die auf der Seite mit dem Projekt für die Azure AD-Podidentität angezeigt werden.

1. Falls sich die im vorherigen Schritt erstellte Azure-Identität nicht in derselben Knotenressourcengruppe für Ihren AKS-Cluster befindet, sollten Sie die Schritte zur [Rollenzuweisung](https://azure.github.io/aad-pod-identity/docs/getting-started/role-assignment/#user-assigned-identities-that-are-not-within-the-node-resource-group) ausführen, die auf der Seite mit dem Projekt für die Azure AD-Podidentität angegeben sind.

## <a name="assign-azure-identity-to-web-service"></a>Zuweisen der Azure-Identität zum Webdienst

In den folgenden Schritten verwenden Sie die im vorherigen Abschnitt erstellte Azure-Identität und weisen sie mit einer **Selektorbezeichnung** Ihrem AKS-Webdienst zu.

Identifizieren Sie zunächst den Namen und Namespace der Bereitstellung in Ihrem AKS-Cluster, der Sie die Azure-Identität zuweisen möchten. Sie erhalten diese Informationen mit dem unten angegebenen Befehl. Der Namespace sollte dem Namen Ihres Azure Machine Learning-Arbeitsbereichs und der Bereitstellungsname Ihrem Endpunktnamen gemäß der Anzeige im Portal entsprechen.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

Fügen Sie der Bereitstellung die Azure Identity-Selektorbezeichnung hinzu, indem Sie die Bereitstellungsspezifikation bearbeiten. Der Selektorwert sollte der Wert sein, den Sie in Schritt 5 zum [Bereitstellen einer Bindung für die Azure-Identität](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#5-deploy-azureidentitybinding) definiert haben.

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

Bearbeiten Sie die Bereitstellung, um die zur Azure-Identität gehörende Selektorbezeichnung hinzuzufügen. Wechseln Sie zum folgenden Abschnitt unter `/spec/template/metadata/labels`. Es sollten Werte wie `isazuremlapp: “true”` angezeigt werden. Fügen Sie die Bezeichnung „aad-pod-identity“ wie unten gezeigt hinzu.

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
       aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

Führen Sie den unten angegebenen Befehl aus, um zu überprüfen, ob die Bezeichnung richtig hinzugefügt wurde. Es sollten auch die Status der neu erstellten Pods angezeigt werden.

```azurecli-interactive
   kubectl get pod -n azureml-<name of workspace> --show-labels
```

Wenn die Pods ausgeführt werden, ist für die Webdienste dieser Bereitstellung jetzt der Zugriff auf Azure-Ressourcen mit Ihrer Azure-Identität möglich, ohne dass Sie die Anmeldeinformationen in Ihren Code einbetten müssen.

## <a name="assign-roles-to-your-azure-identity"></a>Zuweisen von Rollen zu Ihrer Azure-Identität

[Weisen Sie Ihrer verwalteten Azure-Identität die richtigen Rollen](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) für den Zugriff auf andere Azure-Ressourcen zu. Stellen Sie sicher, dass die von Ihnen zugewiesenen Rollen über die richtigen **Datenaktionen** verfügen. Die [Rolle „Leser von Speicherblobdaten“](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) verfügt beispielsweise über Leseberechtigungen für Ihr Speicherblob, während dies für die generische [Rolle „Leser“](../role-based-access-control/built-in-roles.md#reader) nicht gilt.

## <a name="use-azure-identity-with-your-web-service"></a>Verwenden der Azure-Identität mit Ihrem Webdienst

Stellen Sie ein Modell in Ihrem AKS-Cluster bereit. Das Skript `score.py` kann Vorgänge enthalten, in denen auf die Azure-Ressourcen verwiesen wird, auf die Ihre Azure-Identität Zugriff hat. Stellen Sie sicher, dass Sie die benötigten Clientbibliotheksabhängigkeiten für die Ressource installiert haben, auf die Sie zugreifen möchten. Unten sind einige Beispiele dafür angegeben, wie Sie Ihre Azure-Identität zum Zugreifen auf verschiedene Azure-Ressourcen Ihres Diensts nutzen können.

### <a name="access-key-vault-from-your-web-service"></a>Zugreifen auf einen Schlüsseltresor über den Webdienst

Wenn Sie Ihrer Azure-Identität Lesezugriff auf ein Geheimnis in einem **Schlüsseltresor** gewährt haben, ist für `score.py` der Zugriff darauf mit dem folgenden Code möglich.

```python
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

my_vault_name = "yourkeyvaultname"
my_vault_url = "https://{}.vault.azure.net/".format(my_vault_name)
my_secret_name = "sample-secret"

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
secret_client = SecretClient(
    vault_url=my_vault_url,
    credential=credential)
secret = secret_client.get_secret(my_secret_name)
```

> [!IMPORTANT]
> In diesem Beispiel wird DefaultAzureCredential verwendet. Informationen zum Gewähren des Zugriffs für Ihre Identität mithilfe einer bestimmten Zugriffsrichtlinie finden Sie unter [Zuweisen einer Key Vault-Zugriffsrichtlinie](../key-vault/general/assign-access-policy-cli.md).

### <a name="access-blob-from-your-web-service"></a>Zugreifen auf das Blob über Ihren Webdienst

Wenn Sie Ihrer Azure-Identität Lesezugriff auf Daten in einem **Speicherblob** gewährt haben, ist für `score.py` der Zugriff darauf mit dem folgenden Code möglich.

```python
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobServiceClient

my_storage_account_name = "yourstorageaccountname"
my_storage_account_url = "https://{}.blob.core.windows.net/".format(my_storage_account_name)

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
blob_service_client = BlobServiceClient(
    account_url=my_storage_account_url,
    credential=credential
)
blob_client = blob_service_client.get_blob_client(container="some-container", blob="some_text.txt")
blob_data = blob_client.download_blob()
blob_data.readall()
```

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur Verwendung der Azure Identity-Clientbibliothek für Python finden Sie im entsprechenden [Repository](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python) auf GitHub.
* Eine ausführliche Anleitung zum Bereitstellen von Modellen für Azure Kubernetes Service-Cluster finden Sie in der [Schrittanleitung](how-to-deploy-azure-kubernetes-service.md).
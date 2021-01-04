---
title: Zurücksetzen der Anmeldeinformationen für einen Cluster
titleSuffix: Azure Kubernetes Service
description: Hier erfahren Sie, wie Sie die Dienstprinzipal- oder AAD-Anwendungs-Anmeldeinformationen für einen AKS-Cluster (Azure Kubernetes Service) aktualisieren oder zurücksetzen können.
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: c787f172bc03e11c574c4de967aee05da9df18aa
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427512"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>Aktualisieren oder Rotieren der Anmeldeinformationen für Azure Kubernetes Service (AKS)

Standardmäßig werden AKS-Cluster mit einem Dienstprinzipal mit einer Ablaufzeit von einem Jahr erstellt. Wenn Sie sich dem Ablaufdatum nähern, können Sie die Anmeldeinformationen zurücksetzen, um den Dienstprinzipal um einen zusätzlichen Zeitraum zu verlängern. Sie können die Anmeldeinformationen auch im Rahmen einer definierten Sicherheitsrichtlinie aktualisieren oder rotieren. Dieser Artikel beschreibt, wie diese Anmeldeinformationen für einen AKS-Cluster aktualisiert werden.

Möglicherweise haben Sie auch [Ihren AKS-Cluster in Azure Active Directory][aad-integration] integriert und verwenden ihn als Authentifizierungsanbieter für Ihren Cluster. In diesem Fall verfügen Sie über zwei weitere Identitäten, die für Ihren Cluster erstellt wurden: die AAD-Server-App und die AAD-Client-App. Sie können diese Anmeldeinformationen ebenfalls zurücksetzen.

Sie können anstelle eines Dienstprinzipals auch eine verwaltete Identität für Berechtigungen verwenden. Verwaltete Identitäten sind einfacher zu verwalten als Dienstprinzipale und erfordern keine Updates oder Drehungen. Weitere Informationen finden Sie unter [Verwenden verwalteter Identitäten](use-managed-identity.md).

## <a name="before-you-begin"></a>Voraussetzungen

Azure CLI-Version 2.0.65 oder höher muss installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI][install-azure-cli].

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>Aktualisieren oder Erstellen eines neuen Dienstprinzipals für Ihren AKS-Cluster

Wenn Sie die Anmeldeinformationen für einen AKS-Cluster aktualisieren möchten, haben Sie folgende Möglichkeiten:

* Aktualisieren der Anmeldeinformationen für den vorhandenen Dienstprinzipal.
* Erstellen eines neuen Dienstprinzipals und Aktualisieren des Clusters zur Verwendung der neuen Anmeldeinformationen. 

> [!WARNING]
> Wenn Sie sich dafür entscheiden, einen *neuen* Dienstprinzipal zu erstellen, kann die Aktualisierung eines großen AKS-Clusters zur Verwendung dieser Anmeldeinformationen sehr viel Zeit in Anspruch nehmen.

### <a name="check-the-expiration-date-of-your-service-principal"></a>Überprüfen des Ablaufdatums Ihres Dienstprinzipals

Um das Ablaufdatum Ihres Dienstprinzipals zu überprüfen, verwenden Sie den Befehl [az ad sp credential list][az-ad-sp-credential-list]. Das folgende Beispiel ruft die Dienstprinzipal-ID für den Cluster namens *myAKSCluster* in der Ressourcengruppe *myResourceGroup* mit dem Befehl [az aks show][az-aks-show] ab. Die Dienstprinzipal-ID wird als Variable mit dem Namen *SP_ID* zur Verwendung mit dem Befehl [az ad sp credential list][az-ad-sp-credential-list] festgelegt.

```azurecli
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
az ad sp credential list --id $SP_ID --query "[].endDate" -o tsv
```

### <a name="reset-the-existing-service-principal-credential"></a>Zurücksetzen der vorhandenen Dienstprinzipal-Anmeldeinformationen

Um die Anmeldeinformationen für den vorhandenen Dienstprinzipal zu aktualisieren, rufen Sie über den Befehl [az aks show][az-aks-show] die Dienstprinzipal-ID für Ihren Cluster ab. Im folgenden Beispiel wird die ID für den Cluster *myAKSCluster* in der Ressourcengruppe *myResourceGroup* abgerufen. Die Dienstprinzipal-ID wird als Variable mit dem Namen *SP_ID* zur Verwendung in einem zusätzlichen Befehl festgelegt. Diese Befehle verwenden die Bash-Syntax.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

Mit einer Variablen, die die Dienstprinzipal-ID enthält, können Sie nun die Anmeldeinformationen über den Befehl [az ad sp credential reset][az-ad-sp-credential-reset] zurücksetzen. Im folgenden Beispiel wird über die Azure-Plattform ein neues sicheres Geheimnis für den Dienstprinzipal erstellt. Dieses neue sichere Geheimnis wird ebenfalls als Variable gespeichert.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Setzen Sie jetzt den Vorgang anhand des Abschnitts [Aktualisieren des AKS-Clusters mit neuen Dienstprinzipal-Anmeldeinformationen](#update-aks-cluster-with-new-service-principal-credentials) fort. Dieser Schritt ist notwendig, damit die Änderungen am Dienstprinzipal vom AKS-Cluster übernommen werden können.

### <a name="create-a-new-service-principal"></a>Erstellen eines neuen Dienstprinzipals

Wenn Sie im vorherigen Abschnitt die vorhandenen Anmeldeinformationen für den vorhandenen Dienstprinzipal aktualisiert haben, überspringen Sie diesen Schritt. Setzen Sie den Vorgang anhand des Abschnitts [Aktualisieren des AKS-Clusters mit neuen Dienstprinzipal-Anmeldeinformationen](#update-aks-cluster-with-new-service-principal-credentials) fort.

Um einen Dienstprinzipal zu erstellen und anschließend den AKS-Cluster für die Verwendung dieser neuen Anmeldeinformationen zu aktualisieren, verwenden Sie den Befehl [az ad sp create-for-rbac][az-ad-sp-create]. Im folgenden Beispiel verhindert der `--skip-assignment`-Parameter, dass zusätzliche Standardzuweisungen durchgeführt werden:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus: Notieren Sie sich Ihre persönlichen Angaben für `appId` und `password`. Diese Werte werden im nächsten Schritt benötigt.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

Definieren Sie nun Variablen für die Dienstprinzipal-ID und das Clientgeheimnis unter Verwendung der Ausgabe Ihres eigenen Befehls [az ad sp create-for-rbac][az-ad-sp-create], wie im folgenden Beispiel gezeigt. Der Wert *SP_ID* steht für Ihre *App-ID* , der Wert *SP_SECRET* gibt Ihr *Kennwort* an:

```console
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Setzen Sie jetzt den Vorgang anhand des Abschnitts [Aktualisieren des AKS-Clusters mit neuen Dienstprinzipal-Anmeldeinformationen](#update-aks-cluster-with-new-service-principal-credentials) fort. Dieser Schritt ist notwendig, damit die Änderungen am Dienstprinzipal vom AKS-Cluster übernommen werden können.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>Aktualisieren des AKS-Clusters mit neuen Dienstprinzipal-Anmeldeinformationen

> [!IMPORTANT]
> Bei großen Clustern kann die Aktualisierung des AKS-Clusters mit einem neuen Dienstprinzipal viel Zeit in Anspruch nehmen.

Aktualisieren Sie nun unabhängig davon, ob Sie die Anmeldeinformationen für den vorhandenen Dienstprinzipal aktualisieren oder einen Dienstprinzipal erstellen, den AKS-Cluster mit Ihren neuen Anmeldeinformationen. Verwenden Sie dazu den Befehl[az aks update-credentials][az-aks-update-credentials]. Es werden die Variablen für *--service-principal* und *--client-secret* verwendet:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Bei kleinen und mittelgroßen Clustern dauert es einige Augenblicke, bis die Anmeldeinformationen für den Dienstprinzipal in AKS aktualisiert werden.

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>Aktualisieren eines AKS-Clusters mit neuen AAD-Anwendungs-Anmeldeinformationen

Sie können neue AAD-Server- und -Clientanwendungen erstellen, indem Sie die [Schritte für die AAD-Integration][create-aad-app] ausführen. Oder setzen Sie Ihre vorhandenen AAD-Anwendungen durch Ausführung [derselben Methode wie beim Zurücksetzen des Dienstprinzipals](#reset-the-existing-service-principal-credential) zurück. Danach müssen Sie nur noch die Anmeldeinformationen für Ihre Cluster-AAD-Anwendung mit dem gleichen [az aks update-credentials][az-aks-update-credentials]-Befehl aktualisieren, allerdings dabei die Variablen *–reset-aad* verwenden.

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-aad \
    --aad-server-app-id <SERVER APPLICATION ID> \
    --aad-server-app-secret <SERVER APPLICATION SECRET> \
    --aad-client-app-id <CLIENT APPLICATION ID>
```


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurden der Dienstprinzipal für den AKS-Cluster selbst und die AAD-Integrationsanwendungen aktualisiert. Weitere Informationen zum Verwalten der Identität für Workloads in einem Cluster finden Sie unter [Best Practices für die Authentifizierung und Autorisierung in Azure Kubernetes Service (AKS)][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: ./azure-ad-integration-cli.md
[create-aad-app]: ./azure-ad-integration-cli.md#create-azure-ad-server-component
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-list]: /cli/azure/ad/sp/credential#az-ad-sp-credential-list
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset

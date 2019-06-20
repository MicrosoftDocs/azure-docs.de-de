---
title: (VERALTET) Dienstprinzipal für Azure Kubernetes-Cluster
description: Erstellen und Verwalten eines Azure Active Directory-Dienstprinzipals für einen Kubernetes-Cluster in Azure Container Service
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 52ed101199126818abaddef47892e1f033eb3968
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60609131"
---
# <a name="deprecated-set-up-an-azure-ad-service-principal-for-a-kubernetes-cluster-in-container-service"></a>(VERALTET) Einrichten eines Azure AD-Dienstprinzipals für einen Kubernetes-Cluster in Container Service

> [!TIP]
> Die aktualisierte Version dieses Artikels, in dem Azure Kubernetes Service verwendet wird, finden Sie unter [Dienstprinzipale mit Azure Kubernetes Service (AKS)](../../aks/kubernetes-service-principal.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Für die Interaktion mit Azure-APIs benötigt Kubernetes in Azure Container Service einen [Azure Active Directory-Dienstprinzipal](../../active-directory/develop/app-objects-and-service-principals.md). Der Dienstprinzipal wird für die dynamische Verwaltung von Ressourcen wie etwa [benutzerdefinierte Routen](../../virtual-network/virtual-networks-udr-overview.md) und [Azure Load Balancer (Layer 4)](../../load-balancer/load-balancer-overview.md) benötigt.


In diesem Artikel werden verschiedene Optionen zum Einrichten eines Dienstprinzipals für Ihren Kubernetes-Cluster gezeigt. Wenn Sie beispielsweise die [Azure CLI](/cli/azure/install-az-cli2) installiert und eingerichtet haben, können Sie durch Ausführen des Befehls [`az acs create`](/cli/azure/acs) sowohl den Kubernetes-Cluster als auch den Dienstprinzipal erstellen.


## <a name="requirements-for-the-service-principal"></a>Anforderungen für den Dienstprinzipal

Sie können einen vorhandenen Azure AD-Dienstprinzipal verwenden, der die folgenden Anforderungen erfüllt, oder einen neuen Dienstprinzipal erstellen.

* **Umfang**: Ressourcengruppe

* **Rolle**: Mitwirkender

* **Geheimer Clientschlüssel**: Muss ein Kennwort sein. Derzeit kann kein für die Zertifikatauthentifizierung eingerichteter Dienstprinzipal verwendet werden.

> [!IMPORTANT]
> Um einen Dienstprinzipal zu erstellen, müssen Sie über die Berechtigungen verfügen, um eine Anwendung bei Ihrem Azure AD-Mandanten zu registrieren und die Anwendung einer Rolle in Ihrem Abonnement zuzuweisen. [Überprüfen Sie im Portal](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions), ob Sie über die erforderlichen Berechtigungen verfügen.
>

## <a name="option-1-create-a-service-principal-in-azure-ad"></a>Option 1: Erstellen eines Dienstprinzipals in Azure AD

Wenn Sie einen Azure AD-Dienstprinzipal erstellen möchten, bevor Sie Ihren Kubernetes-Cluster bereitstellen, bietet Azure dazu mehrere Möglichkeiten.

Bei den folgenden Beispielbefehlen wird die [Azure CLI](../../azure-resource-manager/resource-group-authenticate-service-principal-cli.md) verwendet. Alternativ dazu können Sie einen Dienstprinzipal auch mithilfe von [Azure PowerShell](../../active-directory/develop/howto-authenticate-service-principal-powershell.md), im [Portal](../../active-directory/develop/howto-create-service-principal-portal.md) oder mit anderen Methoden erstellen.

```azurecli
az login

az account set --subscription "mySubscriptionID"

az group create --name "myResourceGroup" --location "westus"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>"
```

Die Ausgabe sieht in etwa wie folgt aus (redigierte Darstellung):

![Erstellen eines Dienstprinzipals](./media/container-service-kubernetes-service-principal/service-principal-creds.png)

Hervorgehoben sind die **Client-ID** (`appId`) und das **Clientgeheimnis** (`password`), die Sie als Dienstprinzipalparameter für die Clusterbereitstellung verwenden.


### <a name="specify-service-principal-when-creating-the-kubernetes-cluster"></a>Angeben eines Dienstprinzipals beim Erstellen des Kubernetes-Clusters

Geben Sie beim Erstellen des Kubernetes-Clusters die **Client-ID** – auch als `appId` (Anwendungs-ID) bezeichnet – und das **Clientgeheimnis** (`password`) eines vorhandenen Dienstprinzipals als Parameter an. Stellen Sie sicher, dass der Dienstprinzipal die zu Beginn dieses Artikels aufgeführten Anforderungen erfüllt.

Sie können diese Parameter beim Bereitstellen des Kubernetes-Clusters über die [Azure-Befehlszeilenschnittstelle (Azure CLI)](container-service-kubernetes-walkthrough.md), im [Azure-Portal](../dcos-swarm/container-service-deployment.md) oder mit anderen Methoden angeben.

>[!TIP]
>Geben Sie als **Client-ID** nicht den `ObjectId`-Wert, sondern den `appId`-Wert des Dienstprinzipals an.
>

Das folgende Beispiel zeigt eine Möglichkeit, die Parameter über die Azure CLI zu übergeben. In diesem Beispiel wird die [Kubernetes-Schnellstartvorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes) verwendet.

1. Laden Sie [hier](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.parameters.json) die Vorlagenparameterdatei `azuredeploy.parameters.json` von GitHub herunter.

2. Geben Sie zum Angeben des Dienstprinzipals Werte für `servicePrincipalClientId` und `servicePrincipalClientSecret` in der Datei ein. (Geben Sie außerdem eigene Werte für `dnsNamePrefix` und `sshRSAPublicKey` an. Bei Letzterem handelt es sich um den öffentlichen SSH-Schlüssel für den Zugriff auf den Cluster.) Speichern Sie die Datei .

    ![Übergeben von Dienstprinzipalparametern](./media/container-service-kubernetes-service-principal/service-principal-params.png)

3. Führen Sie den folgenden Befehl aus, und legen Sie dabei mithilfe von `--parameters` den Pfad der Datei „azuredeploy.parameters.json“ fest. Dieser Befehl stellt den Cluster in einer von Ihnen erstellten Ressourcengruppe namens `myResourceGroup` in der Region „USA, Westen“ bereit.

    ```azurecli
    az login

    az account set --subscription "mySubscriptionID"

    az group create --name "myResourceGroup" --location "westus"

    az group deployment create -g "myResourceGroup" --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.json" --parameters @azuredeploy.parameters.json
    ```


## <a name="option-2-generate-a-service-principal-when-creating-the-cluster-with-az-acs-create"></a>Option 2: Generieren eines Dienstprinzipals beim Erstellen des Clusters per `az acs create`

Wenn Sie den Befehl [`az acs create`](/cli/azure/acs#az-acs-create) ausführen, um den Kubernetes-Cluster zu erstellen, haben Sie die Option, automatisch einen Dienstprinzipal zu generieren.

Analog zu anderen Erstellungsoptionen für Kubernetes-Cluster können Sie beim Ausführen von `az acs create` Parameter für einen vorhandenen Dienstprinzipal angeben. Ohne Angabe dieser Parameter erstellt die Azure CLI automatisch einen Dienstprinzipal zur Verwendung mit Container Service. Hierbei handelt es sich um einen transparenten Vorgang während der Bereitstellung.

Der folgende Befehl erstellt einen Kubernetes-Cluster und generiert sowohl SSH-Schlüssel als auch Dienstprinzipal-Anmeldeinformationen:

```console
az acs create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys --orchestrator-type kubernetes
```

> [!IMPORTANT]
> Wenn Ihr Konto in Azure AD und im Abonnement nicht über die Berechtigungen verfügt, einen Dienstprinzipal zu erstellen, generiert der Befehl einen Fehler ähnlich diesem: `Insufficient privileges to complete the operation.`
>

## <a name="additional-considerations"></a>Zusätzliche Überlegungen

* Wenn Sie nicht über die Berechtigungen zum Erstellen eines Dienstprinzipals für Ihr Abonnement verfügen, müssen Sie sich möglicherweise an Ihren Azure AD- oder Abonnementadministrator wenden, damit dieser Ihnen die erforderlichen Berechtigungen erteilt oder einen Dienstprinzipal für die Verwendung mit Azure Container Service erstellt.

* Der Dienstprinzipal für Kubernetes ist Bestandteil der Clusterkonfiguration. Verwenden Sie diese Identität jedoch nicht zum Bereitstellen des Clusters.

* Jeder Dienstprinzipal ist einer Azure AD-Anwendung zugeordnet. Der Dienstprinzipal für einen Kubernetes-Cluster kann einem beliebigen gültigen Azure AD-Anwendungsnamen zugeordnet sein (z.B. `https://www.contoso.org/example`). Bei der URL für die Anwendung muss es sich nicht um einen realen Endpunkt handeln.

* Wenn Sie die **Client-ID** des Dienstprinzipals angeben, können Sie den Wert von `appId` (wie in diesem Artikel gezeigt) oder den entsprechenden `name`-Wert für den Dienstprinzipal (beispielsweise `https://www.contoso.org/example`) verwenden.

* Auf dem virtuellen Mastercomputer und den virtuellen Agent-Computern des Kubernetes-Clusters werden die Dienstprinzipal-Anmeldeinformationen in der Datei `/etc/kubernetes/azure.json` gespeichert.

* Wenn Sie den Dienstprinzipal mithilfe des Befehls `az acs create` automatisch generieren, werden die Dienstprinzipal-Anmeldeinformationen auf dem Computer, auf dem der Befehl ausgeführt wird, in die Datei `~/.azure/acsServicePrincipal.json` geschrieben.

* Wenn Sie den Dienstprinzipal mithilfe des Befehls `az acs create` automatisch generieren, kann sich der Dienstprinzipal auch bei einer [Azure-Containerregistrierung](../../container-registry/container-registry-intro.md) registrieren, die im gleichen Abonnement erstellt wurde.

* Dienstprinzipal-Anmeldeinformationen können ablaufen, woraufhin die Clusterknoten in den Zustand **NotReady** wechseln. Informationen zur Behandlung dieses Problems finden Sie im Abschnitt [Ablauf von Anmeldeinformationen](#credential-expiration).

## <a name="credential-expiration"></a>Ablauf von Anmeldeinformationen

Sofern bei der Dienstprinzipalerstellung kein benutzerdefiniertes Gültigkeitszeitfenster (mithilfe des Parameters `--years`) angegeben wird, sind die Anmeldeinformationen des Dienstprinzipals ab dessen Erstellung ein Jahr lang gültig. Nach Ablauf der Anmeldeinformationen wechseln die Clusterknoten unter Umständen in den Zustand **NotReady**.

Führen Sie zum Überprüfen des Ablaufdatums eines Dienstprinzipals den Befehl [az ad app show](/cli/azure/ad/app#az-ad-app-show) mit dem Parameter `--debug` aus, und suchen Sie im unteren Bereich der Ausgabe nach dem `endDate`-Wert von `passwordCredentials`:

```azurecli
az ad app show --id <appId> --debug
```

Ausgabe (Ausschnitt):

```json
...
"passwordCredentials":[{"customKeyIdentifier":null,"endDate":"2018-11-20T23:29:49.316176Z"
...
```

Sind die Dienstprinzipal-Anmeldeinformationen abgelaufen, aktualisieren Sie sie mithilfe des Befehls [az ad sp reset-credentials](/cli/azure/ad/sp):

```azurecli
az ad sp reset-credentials --name <appId>
```

Ausgabe:

```json
{
  "appId": "4fd193b0-e6c6-408c-a21a-803441ad2851",
  "name": "4fd193b0-e6c6-408c-a21a-803441ad2851",
  "password": "404203c3-0000-0000-0000-d1d2956f3606",
  "tenant": "72f988bf-0000-0000-0000b-2d7cd011db47"
}
```

Aktualisieren Sie anschließend `/etc/kubernetes/azure.json` mit den neuen Anmeldeinformationen auf allen Clusterknoten, und starten Sie die Knoten neu.

## <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit Kubernetes](container-service-kubernetes-walkthrough.md) in Ihrem Container Service-Cluster

* Informationen zur Problembehandlung bei Dienstprinzipalen für Kubernetes finden Sie in der [ACS-Engine-Dokumentation](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes.md#troubleshooting).

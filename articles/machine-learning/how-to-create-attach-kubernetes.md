---
title: Erstellen und Anfügen eines Azure Kubernetes Service
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mit Azure Machine Learning einen neuen Azure Kubernetes Service-Cluster erstellen oder einen bestehenden AKS-Cluster an Ihren Arbeitsbereich anfügen können.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 10/02/2020
ms.openlocfilehash: e773c2db9c7849dd9680f8ae0c600405f422d7e1
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463179"
---
# <a name="create-and-attach-an-azure-kubernetes-service-cluster"></a>Erstellen und Anfügen eines Azure Kubernetes Service-Clusters

Mit Azure Machine Learning kann ein trainiertes Machine Learning-Modell im Azure Kubernetes Service bereitgestellt werden. Allerdings müssen Sie zunächst einen AKS-Cluster (Azure Kubernetes Service) in Ihrem Azure ML-Arbeitsbereich __erstellen__ oder einen vorhandenen AKS-Cluster __anfügen__. Dieser Artikel enthält Informationen sowohl zum Erstellen als auch zum Anfügen eines Clusters.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).

- Die [Azure CLI-Erweiterung für Machine Learning Service](reference-azure-machine-learning-cli.md), das [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) oder die [Visual Studio Code-Erweiterung für Azure Machine Learning](tutorial-setup-vscode-extension.md).

- Wenn Sie die Verwendung eines Azure-Virtual Network zum Sichern der Kommunikation zwischen Ihrem Azure ML-Arbeitsbereich und dem AKS-Cluster beabsichtigen, lesen Sie den Artikel [Netzwerkisolation während Training und Rückschluss](./how-to-network-security-overview.md).

## <a name="limitations"></a>Einschränkungen

- Wenn in Ihrem Cluster anstelle eines Load Balancer Basic (BLB) ein **Load Balancer Standard (SLB)** bereitgestellt werden muss, erstellen Sie im AKS-Portal, mit der CLI oder über das SDK einen Cluster, den Sie dann an den AML-Arbeitsbereich **anfügen**.

- Wenn Sie über eine Azure Policy verfügen, die die Erstellung von öffentlichen IP-Adressen einschränkt, tritt bei der AKS-Clustererstellung ein Fehler auf. AKS erfordert eine öffentliche IP-Adresse für [ausgehenden Datenverkehr](../aks/limit-egress-traffic.md). Der Artikel zum ausgehenden Datenverkehr enthält außerdem Anleitungen zum Sperren von aus dem Cluster über die öffentliche IP-Adresse ausgehenden Datenverkehr mit Ausnahme einiger vollqualifizierter Domänennamen. Es gibt 2 Möglichkeiten, eine öffentliche IP-Adresse zu aktivieren:
    - Der Cluster kann die öffentliche IP-Adresse verwenden, die standardmäßig mit dem BLB oder SLB erstellt wird, oder
    - der Cluster kann ohne öffentliche IP-Adresse erstellt werden, und anschließend wird eine öffentliche IP-Adresse mit einer Firewall mit einer benutzerdefinierten Route konfiguriert. Weitere Informationen finden Sie unter [Anpassen des ausgehenden Clusterdatenverkehrs mit einer benutzerdefinierten Route](../aks/egress-outboundtype.md).
    
    Die AML-Steuerungsebene kommuniziert nicht mit dieser öffentlichen IP-Adresse. Sie kommuniziert mit der AKS-Steuerungsebene für Bereitstellungen. 

- Wenn Sie einen AKS-Cluster **anfügen**, der über einen [autorisierten IP-Adressbereich mit Zugriff auf den API-Server](../aks/api-server-authorized-ip-ranges.md) verfügt, aktivieren Sie die IP-Adressbereiche der AML-Steuerungsebene für den AKS-Cluster. Die AML-Steuerungsebene wird für Regionspaare bereitgestellt und stellt Rückschlusspods im AKS-Cluster bereit. Ohne Zugriff auf den API-Server können die Rückschlusspods nicht bereitgestellt werden. Verwenden Sie die [IP-Adressbereiche](https://www.microsoft.com/download/confirmation.aspx?id=56519) für beide [Regionspaare](../best-practices-availability-paired-regions.md), wenn Sie die IP-Adressbereiche in einem AKS-Cluster aktivieren.

    Autorisierte IP-Adressbereiche funktionieren nur mit Load Balancer Standard.

- Beim **Anfügen** eines AKS-Clusters muss dieser sich im selben Azure-Abonnement befinden wie Ihr Azure Machine Learning-Arbeitsbereich.

- Wenn Sie einen privaten AKS-Cluster (mit Azure Private Link) verwenden möchten, müssen Sie zuerst den Cluster erstellen und diesen dann **an den Arbeitsbereich anfügen**. Weitere Informationen finden Sie unter [Erstellen eines privaten Azure Kubernetes Service-Clusters](../aks/private-clusters.md).

- Der Computename für den AKS-Cluster MUSS innerhalb Ihres Azure ML-Arbeitsbereichs eindeutig sein.
    - Der Name ist erforderlich und muss zwischen 3 und 24 Zeichen lang sein.
    - Gültige Zeichen sind Groß- und Kleinbuchstaben, Ziffern und das Zeichen -.
    - Der Name muss mit einem Buchstaben beginnen.
    - Der Name muss auf allen vorhandenen Compute-Instanzen innerhalb einer Azure-Region eindeutig sein. Sie erhalten eine Warnung, wenn der von Ihnen gewählte Name nicht eindeutig ist.
   
 - Wenn Sie Modelle auf **GPU**-Knoten oder **FPGA**-Knoten (oder einer bestimmten SKU) bereitstellen möchten, müssen Sie einen Cluster mit der jeweiligen SKU erstellen. Das Erstellen eines sekundären Knotenpools in einem vorhandenen Cluster und Bereitstellen von Modellen im sekundären Knotenpool wird nicht unterstützt.
 
- Beim Erstellen oder Anfügen eines Clusters können Sie auswählen, ob der Cluster für __Dev/Test__ oder die __Produktion__ erstellt werden soll. Wenn Sie einen AKS-Cluster für __Entwicklung__, __Validierung__ und __Tests__ statt für die Produktion erstellen möchten, legen Sie __Clusterzweck__ auf __Dev/Test__ fest. Wenn Sie den Clusterzweck nicht angeben, wird ein Cluster für die __Produktion__ erstellt. 

    > [!IMPORTANT]
    > Ein __Dev/Test__-Cluster ist nicht für Produktionsdatenverkehr geeignet und kann Rückschlusszeiten erhöhen. Entwicklungs- und Testcluster garantieren auch keine Fehlertoleranz.

- Wenn ein Cluster beim Erstellen oder Anfügen des Clusters für die __Produktion__ verwendet wird, muss er mindestens 12 __virtuelle CPUs__ enthalten. Die Anzahl der virtuellen CPUs kann berechnet werden, indem die __Anzahl der Knoten__ im Cluster mit der __Anzahl der Kerne__, die von der ausgewählten VM-Größe bereitgestellt werden, multipliziert wird. Wenn Sie beispielsweise die VM-Größe „Standard_D3_v2“ verwenden, die 4 virtuelle CPUs hat, müssen Sie für die Anzahl der Knoten einen Wert von mindestens 3 wählen.

    Für ein __Dev/Test__-Cluster werden mindestens 2 virtuelle CPUs empfohlen.

- Das Azure Machine Learning-SDK bietet keine Unterstützung zur Skalierung eines AKS-Clusters. Die Knoten in Ihrem Cluster können Sie über die Benutzeroberfläche Ihres AKS-Clusters in Azure Machine Learning Studio skalieren. Sie können nur die Knotenanzahl ändern, nicht die VM-Größe des Clusters. Weitere Informationen zum Skalieren der Knoten in einem AKS-Cluster finden Sie in den folgenden Artikeln:

    - [Skalieren der Anzahl der Knoten in einem Azure Kubernetes Service-Cluster (AKS)](../aks/scale-cluster.md)
    - [Automatisches Skalieren eines Clusters zur Erfüllung von Anwendungsanforderungen in Azure Kubernetes Service (AKS)](../aks/cluster-autoscaler.md)

## <a name="azure-kubernetes-service-version"></a>Version von Azure Kubernetes Service

Mit Azure Kubernetes Service können Sie einen Cluster mit einer Vielzahl von Kubernetes-Versionen erstellen. Weitere Informationen zu verfügbaren Versionen finden Sie unter [Unterstützte Kubernetes-Versionen in Azure Kubernetes Service](../aks/supported-kubernetes-versions.md).

Wenn Sie einen Azure Kubernetes Service-Cluster mithilfe einer der folgenden Methoden **erstellen**, *können Sie die Version des Clusters nicht auswählen*, der erstellt wird:

* Azure Machine Learning Studio oder den Abschnitt „Azure Machine Learning“ im Azure-Portal.
* Machine Learning-Erweiterung für die Azure CLI.
* Azure Machine Learning SDK.

Diese Methoden zum Erstellen eines AKS-Clusters verwenden die __Standardversion__ des Clusters. *Die Standardversion ändert sich im Laufe der Zeit*, wenn neue Kubernetes-Versionen veröffentlicht werden.

Beim **Anfügen** an einen vorhandenen AKS-Cluster werden alle derzeit unterstützten AKS-Versionen unterstützt.

> [!NOTE]
> Es gibt möglicherweise Grenzfälle, in denen Sie über einen älteren Cluster verfügen, der nicht mehr unterstützt wird. In diesem Fall gibt der Anfügevorgang einen Fehler zurück und listet die derzeit unterstützten Versionen auf.
>
> Sie können **Vorschauversionen** anfügen. Vorschaufunktionen werden ohne Vereinbarung zum Servicelevel bereitgestellt und sind nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Die Unterstützung für die Verwendung von Vorschauversionen ist möglicherweise eingeschränkt. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="available-and-default-versions"></a>Verfügbare und Standardversionen

Um die verfügbaren und Standard-AKS-Versionen zu ermitteln, verwenden Sie den [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)-Befehl [az aks get-versions](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_versions). Mit dem folgenden Befehl werden beispielsweise die in der Region „USA, Westen“ verfügbaren Versionen zurückgegeben:

```azurecli-interactive
az aks get-versions -l westus -o table
```

Die Ausgabe dieses Befehls ähnelt dem folgenden Text:

```text
KubernetesVersion    Upgrades
-------------------  ----------------------------------------
1.18.6(preview)      None available
1.18.4(preview)      1.18.6(preview)
1.17.9               1.18.4(preview), 1.18.6(preview)
1.17.7               1.17.9, 1.18.4(preview), 1.18.6(preview)
1.16.13              1.17.7, 1.17.9
1.16.10              1.16.13, 1.17.7, 1.17.9
1.15.12              1.16.10, 1.16.13
1.15.11              1.15.12, 1.16.10, 1.16.13
```

Um die Standardversion zu ermitteln, die beim **Erstellen** eines Clusters über Azure Machine Learning verwendet wird, können Sie den `--query`-Parameter verwenden, um die Standardversion auszuwählen:

```azurecli-interactive
az aks get-versions -l westus --query "orchestrators[?default == `true`].orchestratorVersion" -o table
```

Die Ausgabe dieses Befehls ähnelt dem folgenden Text:

```text
Result
--------
1.16.13
```

Wenn Sie die **verfügbaren Versionen programmgesteuert überprüfen** möchten, verwenden Sie die [Container Service Client – List Orchestrators](/rest/api/container-service/container%20service%20client/listorchestrators)-REST-API. Um die verfügbaren Versionen zu finden, sehen Sie sich die Einträge an, in denen `orchestratorType` den Wert `Kubernetes` hat. Die zugehörigen `orchestrationVersion`-Einträge enthalten die verfügbaren Versionen, die an Ihren Arbeitsbereich **angefügt** werden können.

Um die Standardversion zu ermitteln, die beim **Erstellen** eines Clusters über Azure Machine Learning verwendet wird, suchen Sie den Eintrag, bei dem `orchestratorType` den Wert `Kubernetes` und `default` den Wert `true` hat. Der zugeordnete `orchestratorVersion`-Wert ist die Standardversion. Der folgende JSON-Codeausschnitt zeigt einen Beispieleintrag:

```json
...
 {
        "orchestratorType": "Kubernetes",
        "orchestratorVersion": "1.16.13",
        "default": true,
        "upgrades": [
          {
            "orchestratorType": "",
            "orchestratorVersion": "1.17.7",
            "isPreview": false
          }
        ]
      },
...
```

## <a name="create-a-new-aks-cluster"></a>Erstellen eines neuen AKS-Clusters

**Geschätzter Zeitaufwand**: Ca. zehn Minuten.

Das Erstellen oder Anfügen eines AKS-Clusters ist ein für Ihren Arbeitsbereich einmaliger Vorgang. Sie können diesen Cluster für mehrere Bereitstellungen wiederverwenden. Wenn Sie den Cluster oder die Ressourcengruppe löschen, die ihn enthält, müssen Sie bei der nächsten Bereitstellung einen neuen Cluster erstellen. Sie können an Ihren Arbeitsbereich mehrere AKS-Cluster anfügen.

Das folgende Beispiel veranschaulicht, wie ein neuer AKS-Cluster mit dem SDK und der CLI erstellt wird:

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

# Example configuration to use an existing virtual network
# prov_config.vnet_name = "mynetwork"
# prov_config.vnet_resourcegroup_name = "mygroup"
# prov_config.subnet_name = "default"
# prov_config.service_cidr = "10.0.0.0/16"
# prov_config.dns_service_ip = "10.0.0.10"
# prov_config.docker_bridge_cidr = "172.17.0.1/16"

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

Weitere Informationen zu den in diesem Beispiel verwendeten Klassen, Methoden und Parametern finden Sie in den folgenden Referenzdokumenten:

* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?preserve-view=true&view=azure-ml-py)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget.create](/python/api/azureml-core/azureml.core.compute.computetarget?preserve-view=true&view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computetarget?preserve-view=true&view=azure-ml-py#wait-for-completion-show-output-false-)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks
```

Weitere Informationen finden Sie unter [az ml computetarget create aks](/cli/azure/ext/azure-cli-ml/ml/computetarget/create?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Weitere Informationen zum Erstellen eines AKS-Clusters im Portal finden Sie unter [Erstellen von Computezielen in Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#inference-clusters).

---

## <a name="attach-an-existing-aks-cluster"></a>Anfügen eines vorhandenen AKS-Clusters

**Geschätzter Zeitaufwand:** Ca. fünf Minuten.

Wenn Sie in Ihrem Azure-Abonnement bereits über einen AKS-Cluster verfügen und dieser maximal die Version 1.17.hat, können Sie ihn für die Bereitstellung Ihres Image verwenden.

> [!TIP]
> Der vorhandene AKS-Cluster kann sich in einer anderen Azure-Region befinden als Ihr Azure Machine Learning-Arbeitsbereich.


> [!WARNING]
> Erstellen Sie nicht mehrere gleichzeitige Verknüpfungen für einen AKS-Cluster in Ihrem Arbeitsbereich. Verwenden Sie beispielsweise beim Verknüpfen eines AKS-Clusters mit einem Arbeitsbereich nicht zwei unterschiedliche Namen. Jede neue Verknüpfung führt zu einem Fehler der vorherigen vorhandenen Verknüpfungen.
>
> Falls Sie einen AKS-Cluster erneut verknüpfen möchten (etwa zum Ändern der TLS-Einstellung oder einer anderen Clusterkonfigurationseinstellung), müssen Sie zuerst die vorhandene Verknüpfung mithilfe von [AksCompute.detach()](/python/api/azureml-core/azureml.core.compute.akscompute?preserve-view=true&view=azure-ml-py#detach--) entfernen.

Weitere Informationen zum Erstellen eines AKS-Clusters mithilfe der Azure-CLI oder des Portals finden Sie in den folgenden Artikeln:

* [Erstellen eines AKS-Clusters (CLI)](/cli/azure/aks?bc=%2fazure%2fbread%2ftoc.json&preserve-view=true&toc=%2fazure%2faks%2fTOC.json&view=azure-cli-latest#az-aks-create)
* [Schnellstart: Bereitstellen eines AKS-Clusters (Azure Kubernetes Service) über das Azure-Portal](../aks/kubernetes-walkthrough-portal.md?preserve-view=true&view=azure-cli-latest)
* [Erstellen eines AKS-Clusters (ARM-Vorlage auf Azure-Schnellstartvorlagen)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

Das folgende Beispiel veranschaulicht das Anfügen eines vorhandenen AKS-Clusters an Ihren Arbeitsbereich:

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)

# Wait for the attach process to complete
aks_target.wait_for_completion(show_output = True)
```

Weitere Informationen zu den in diesem Beispiel verwendeten Klassen, Methoden und Parametern finden Sie in den folgenden Referenzdokumenten:

* [AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?preserve-view=true&view=azure-ml-py)
* [AksCompute.attach](/python/api/azureml-core/azureml.core.compute.computetarget?preserve-view=true&view=azure-ml-py#attach-workspace--name--attach-configuration-)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um einen vorhandenen Cluster mithilfe der CLI anzufügen, müssen Sie die Ressourcen-ID des vorhandenen Clusters abrufen. Verwenden Sie zum Abrufen dieses Werts den folgenden Befehl. Ersetzen Sie `myexistingcluster` durch den Namen Ihres AKS-Clusters. Ersetzen Sie `myresourcegroup` durch die Ressourcengruppe, die den Cluster enthält:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Dieser Befehl gibt einen Wert zurück, der in etwa wie folgt aussieht:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Um den vorhandenen Cluster an Ihren Arbeitsbereich anzufügen, verwenden Sie den folgenden Befehl. Ersetzen Sie `aksresourceid` durch den Wert, der über den vorherigen Befehl zurückgegeben wurde. Ersetzen Sie `myresourcegroup` durch die Ressourcengruppe, die Ihren Arbeitsbereich enthält. Ersetzen Sie `myworkspace` durch den Namen Ihres Arbeitsbereichs.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Weitere Informationen finden Sie unter [az ml computetarget attach aks](/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Informationen zum Anfügen eines AKS-Clusters im Portal finden Sie unter [Erstellen von Computezielen in Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#inference-clusters).

---

## <a name="detach-an-aks-cluster"></a>Trennen eines AKS-Clusters

Verwenden Sie eine der folgenden Methoden, um einen Cluster von Ihrem Arbeitsbereich zu trennen:

> [!WARNING]
> Wenn Sie einen AKS-Cluster mit dem Azure Machine Learning Studio, dem SDK oder der Azure CLI-Erweiterung für maschinelles Lernen trennen, **wird der AKS-Cluster nicht gelöscht**. Informationen zum Löschen des Clusters finden Sie unter [Schnellstart: Bereitstellen eines Azure Kubernetes Service-Clusters über die Azure-Befehlszeilenschnittstelle](../aks/kubernetes-walkthrough.md#delete-the-cluster).

# <a name="python"></a>[Python](#tab/python)

```python
aks_target.detach()
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um den vorhandenen Cluster von Ihrem Arbeitsbereich zu trennen, verwenden Sie den folgenden Befehl. Ersetzen Sie `myaks` durch den Namen, unter dem der AKS-Cluster an Ihren Arbeitsbereich angefügt ist. Ersetzen Sie `myresourcegroup` durch die Ressourcengruppe, die Ihren Arbeitsbereich enthält. Ersetzen Sie `myworkspace` durch den Namen Ihres Arbeitsbereichs.

```azurecli
az ml computetarget detach -n myaks -g myresourcegroup -w myworkspace
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wählen Sie im Azure Machine Learning Studio __Compute__, __Rückschlusscluster__ und den Cluster aus, den Sie entfernen möchten. Verwenden Sie den Link __Trennen__, um den Cluster zu trennen.

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von Azure RBAC für die Kubernetes-Autorisierung](../aks/manage-azure-rbac.md)
* [Wie und wo Modelle bereitgestellt werden](how-to-deploy-and-where.md)
* [Bereitstellen eines Modells in einem Azure Kubernetes Service-Cluster](how-to-deploy-azure-kubernetes-service.md)
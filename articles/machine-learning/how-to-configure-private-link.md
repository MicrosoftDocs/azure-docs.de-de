---
title: Konfigurieren eines privaten Endpunkts
titleSuffix: Azure Machine Learning
description: Verwenden Sie Azure Private Link, um sicher von einem virtuellen Netzwerk aus auf Ihren Azure Machine Learning-Arbeitsbereich zuzugreifen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/30/2020
ms.openlocfilehash: 2953f85a5c21cdd670d6e133d09ffacf06f178ef
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842701"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace"></a>Konfigurieren von Azure Private Link für einen Azure Machine Learning-Arbeitsbereich

In diesem Dokument erfahren Sie, wie Sie Azure Private Link mit Ihrem Azure Machine Learning-Arbeitsbereich verwenden. Informationen zum Erstellen eines virtuellen Netzwerks für Azure Machine Learning finden Sie unter [Übersicht über die Isolation virtueller Netzwerke und Datenschutz](how-to-network-security-overview.md).

Azure Private Link ermöglicht Ihnen das Herstellen einer Verbindung mit Ihrem Arbeitsbereich über einen privaten Endpunkt. Bei einem privaten Endpunkt handelt es sich um eine Gruppe privater IP-Adressen in Ihrem virtuellen Netzwerk. Sie können dann den Zugriff auf Ihren Arbeitsbereich so einschränken, dass er nur über die privaten IP-Adressen erfolgt. Private Link hilft dabei, das Risiko einer Datenexfiltration zu verringern. Weitere Informationen zu privaten Endpunkten finden Sie im Artikel zu [Azure Private Link](../private-link/private-link-overview.md).

> [!IMPORTANT]
> Azure Private Link hat keine Auswirkungen auf die Azure-Steuerungsebene (Verwaltungsvorgänge) aus, etwa das Löschen des Arbeitsbereichs oder das Verwalten von Computeressourcen. Beispiele: Erstellen, Aktualisieren oder Löschen eines Computeziels. Diese Vorgänge werden ganz normal über das öffentliche Internet ausgeführt. Bei Vorgängen auf Datenebene, etwa bei der Verwendung von Azure Machine Learning Studio, APIs (einschließlich veröffentlichter Pipelines) oder des SDK, wird der private Endpunkt verwendet.
>
> Wenn Sie Mozilla Firefox verwenden, treten möglicherweise Probleme beim Zugriff auf den privaten Endpunkt für Ihren Arbeitsbereich auf. Dieses Problem kann im Zusammenhang mit DNS über HTTPS in Mozilla auftreten. Die Verwendung von Microsoft Edge von Google Chrome wird als Problemumgehung empfohlen.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie vorhaben, einen durch Private Link aktivierten Arbeitsbereich mit einem kundenseitig verwalteten Schlüssel zu verwenden, müssen Sie dieses Feature mithilfe eines Supporttickets anfordern. Weitere Informationen finden Sie unter [Verwalten und Erhöhen von Kontingenten](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

## <a name="limitations"></a>Einschränkungen

Die Verwendung eines Azure Machine Learning-Arbeitsbereichs mit Private Link ist in den Azure Government- und Azure China 21Vianet-Regionen nicht verfügbar.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Erstellen eines Arbeitsbereichs, der einen privaten Endpunkt verwendet

Verwenden Sie eine der folgenden Methoden, um einen Arbeitsbereich mit einem privaten Endpunkt zu erstellen. Jede dieser Methoden __erfordert ein vorhandenes virtuelles Netzwerk__:

> [!TIP]
> Wenn Sie einen Arbeitsbereich, einen privaten Endpunkt und ein virtuelles Netzwerk gleichzeitig erstellen möchten, lesen Sie die Informationen unter [Verwenden einer Azure Resource Manager-Vorlage zum Erstellen eines Arbeitsbereichs für Azure Machine Learning](how-to-create-workspace-template.md).

# <a name="python"></a>[Python](#tab/python)

Das Azure Machine Learning Python SDK bietet die Klasse [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig?view=azure-ml-py), die mit [Workspace.create()](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) verwendet werden kann, um einen Arbeitsbereich mit einem privaten Endpunkt zu erstellen. Diese Klasse erfordert ein vorhandenes virtuelles Netzwerk.

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.create(name='myworkspace',
    subscription_id='<my-subscription-id>',
    resource_group='myresourcegroup',
    location='eastus2',
    private_endpoint_config=pe,
    private_endpoint_auto_approval=True,
    show_output=True)
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Die [Azure CLI-Erweiterung für maschinelles Lernen](reference-azure-machine-learning-cli.md) stellt den Befehl [az ml workspace create](/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_create) bereit. Mit den folgenden Parametern für diesen Befehl kann ein Arbeitsbereich mit einem privaten Netzwerk erstellt werden, aber er erfordert ein vorhandenes virtuelles Netzwerk:

* `--pe-name`: Der Name des erstellten privaten Endpunkts.
* `--pe-auto-approval`: Gibt an, ob private Endpunktverbindungen mit dem Arbeitsbereich automatisch genehmigt werden sollen.
* `--pe-resource-group`: Die für den privaten Endpunkt zu erstellende Ressourcengruppe. Muss dieselbe Gruppe sein, die auch das virtuelle Netzwerk enthält.
* `--pe-vnet-name`: Das vorhandene virtuelle Netzwerk, in dem der private Endpunkt erstellt werden soll.
* `--pe-subnet-name`: Der Name des Subnetzes, in dem der private Endpunkt erstellt werden soll. Der Standardwert ist `default`.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Die Registerkarte __Netzwerk__ im Azure Machine Learning Studio ermöglicht Ihnen die Konfiguration eines privaten Endpunkts. Es erfordert jedoch ein vorhandenes virtuelles Netzwerk. Weitere Informationen finden Sie unter [Erstellen von Arbeitsbereichen im Portal](how-to-manage-workspace.md).

---

## <a name="add-a-private-endpoint-to-a-workspace"></a>Hinzufügen eines privaten Endpunkts zu einem Arbeitsbereich

Verwenden Sie eine der folgenden Methoden, um einem vorhandenen Arbeitsbereich einen privaten Endpunkt hinzuzufügen:

> [!IMPORTANT]
>
> Sie benötigen ein vorhandenes virtuelles Netzwerk, in dem der private Endpunkt erstellt werden soll. Außerdem müssen Sie vor dem Hinzufügen des privaten Endpunkts [Netzwerkrichtlinien für private Endpunkte deaktivieren](../private-link/disable-private-endpoint-network-policy.md).

> [!WARNING]
>
> Wenn Sie über vorhandene Computeziele verfügen, die diesem Arbeitsbereich zugeordnet sind und sich nicht hinter dem gleichen virtuellen Netzwerk befinden, in dem der private Endpunkt erstellt wird, funktionieren sie nicht.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.from_config()
ws.add_private_endpoint(private_endpoint_config=pe, private_endpoint_auto_approval=True, show_output=True)
```

Weitere Informationen zu den in diesem Beispiel verwendeten Klassen und Methoden finden Sie unter [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig?view=azure-ml-py) und [Workspace.add_private_endpoint](/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#add-private-endpoint-private-endpoint-config--private-endpoint-auto-approval-true--location-none--show-output-true--tags-none-).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Die [Azure CLI-Erweiterung für maschinelles Lernen](reference-azure-machine-learning-cli.md) stellt den Befehl [az ml workspace private-endpoint add](/cli/azure/ext/azure-cli-ml/ml/workspace/private-endpoint?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_private_endpoint_add) bereit.

```azurecli
az ml workspace private-endpoint add -w myworkspace  --pe-name myprivateendpoint --pe-auto-approval true --pe-vnet-name myvnet
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wählen Sie im Azure Machine Learning-Arbeitsbereich im Portal die Option __Private Endpunktverbindungen__ und dann __+ Privater Endpunkt__ aus. Erstellen Sie mithilfe der Felder einen neuen privaten Endpunkt.

* Wählen Sie beim Festlegen von __Region__ die gleiche Region wie für Ihr virtuelles Netzwerk aus. 
* Verwenden Sie beim Auswählen von __Ressourcentyp__ die Option __Microsoft.MachineLearningServices/workspaces__. 
* Legen Sie __Ressource__ auf den Namen des Arbeitsbereichs fest.

Wählen Sie abschließend __Erstellen__ aus, um den privaten Endpunkt zu erstellen.

---

## <a name="remove-a-private-endpoint"></a>Entfernen eines privaten Endpunkts

Verwenden Sie eine der folgenden Methoden, um einen privaten Endpunkt aus einem Arbeitsbereich zu entfernen:

# <a name="python"></a>[Python](#tab/python)

Verwenden Sie [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#delete-private-endpoint-connection-private-endpoint-connection-name-), um einen privaten Endpunkt zu entfernen.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
# get the connection name
_, _, connection_name = ws.get_details()['privateEndpointConnections'][0]['id'].rpartition('/')
ws.delete_private_endpoint_connection(private_endpoint_connection_name=connection_name)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Die [Azure CLI-Erweiterung für maschinelles Lernen](reference-azure-machine-learning-cli.md) stellt den Befehl [az ml workspace private-endpoint delete](/cli/azure/ext/azure-cli-ml/ml/workspace/private-endpoint?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_private_endpoint_delete) bereit.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wählen Sie im Azure Machine Learning-Arbeitsbereich im Portal die Option __Private Endpunktverbindungen__ und dann den zu entfernenden Endpunkt aus. Wählen Sie abschließend __Entfernen__ aus.

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>Verwenden eines Arbeitsbereichs über einen privaten Endpunkt

Da die Kommunikation mit dem Arbeitsbereich nur aus dem virtuellen Netzwerk zulässig ist, müssen alle Entwicklungsumgebungen, in denen der Arbeitsbereich verwendet wird, Mitglieder des virtuellen Netzwerks sein. Beispiel: ein virtueller Computer im virtuellen Netzwerk.

> [!IMPORTANT]
> Um eine vorübergehende Unterbrechung der Konnektivität zu vermeiden, empfiehlt Microsoft, den DNS-Cache auf Computern, die eine Verbindung mit dem Arbeitsbereich herstellen, nach dem Aktivieren von Private Link zu leeren. 

Weitere Informationen zu Azure Virtual Machines finden Sie in der [Dokumentation zu Virtual Machines](../virtual-machines/index.yml).


## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Schützen Ihres Azure Machine Learning-Arbeitsbereichs finden Sie im Artikel [Übersicht über die Isolation virtueller Netzwerke und Datenschutz](how-to-network-security-overview.md).

* Wenn Sie vorhaben, eine benutzerdefinierte DNS-Lösung in Ihrem virtuellen Netzwerk zu verwenden, finden Sie weitere Informationen unter [Verwenden eines Arbeitsbereichs mit einem benutzerdefinierten DNS-Server](how-to-custom-dns.md).

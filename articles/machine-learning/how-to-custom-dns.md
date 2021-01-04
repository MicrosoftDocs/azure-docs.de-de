---
title: Verwenden eines benutzerdefinierten DNS-Servers
titleSuffix: Azure Machine Learning
description: Konfigurieren eines benutzerdefinierten DNS-Servers für die Arbeit mit einem Azure Machine Learning-Arbeitsbereich und einem privaten Endpunkt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 07ff656c5eacbbcdc16c6c7cf098478ca6baf745
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509290"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>Verwenden Ihres Arbeitsbereichs mit einem benutzerdefinierten DNS-Server

Wenn Sie einen Azure Machine Learning-Arbeitsbereich mit einem privaten Endpunkt verwenden, gibt es [verschiedene Möglichkeiten, die DNS-Namensauflösung zu verarbeiten](../private-link/private-endpoint-dns.md). Standardmäßig übernimmt Azure automatisch die Namensauflösung für Ihren Arbeitsbereich und Ihren privaten Endpunkt. Wenn Sie stattdessen _Ihren eigenen benutzerdefinierten DNS-Server_ verwenden, müssen Sie DNS-Einträge für den Arbeitsbereich manuell erstellen.

> [!IMPORTANT]
> In diesem Artikel wird nur behandelt, wie der vollqualifizierte Domänenname (FQDN) und die IP-Adressen für diese Einträge gefunden werden können. Er enthält KEINE Informationen zum Konfigurieren der DNS-Einträge für diese Elemente. Weitere Informationen zum Hinzufügen von Einträgen finden Sie in der Dokumentation zu Ihrer DNS-Software.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure Virtual Network, das [Ihren eigenen DNS-Server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) verwendet.

- Ein Azure Machine Learning-Arbeitsbereich mit einem privaten Endpunkt. Weitere Informationen finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).

- Vertrautheit mit der Verwendung von [Netzwerkisolation während Training und Rückschluss](./how-to-network-security-overview.md).

- Vertrautheit mit der [DNS-Zonenkonfiguration für private Azure-Endpunkte](../private-link/private-endpoint-dns.md)

- Optional die [Azure CLI](/cli/azure/install-azure-cli) oder [Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="find-the-ip-addresses"></a>Suchen der IP-Adressen

Die folgende Liste enthält die vollqualifizierten Domänennamen (FQDN), die von Ihrem Arbeitsbereich und Ihrem privaten Endpunkt verwendet werden:

* `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
* `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
* `<workspace-GUID>.workspace.<region>.aether.ms`
* `ml-<workspace-name>-<region>-<workspace-guid>.notebooks.azure.net`
* Wenn Sie eine Compute-Instanz erstellen, müssen Sie auch einen Eintrag für `<instance-name>.<region>.instances.azureml.ms` mit der privaten IP-Adresse des privaten Endpunkts für den Arbeitsbereich hinzufügen.

    > [!NOTE]
    > Auf Compute-Instanzen kann nur innerhalb des virtuellen Netzwerks zugegriffen werden.

Verwenden Sie eine der folgenden Methoden, um die internen IP-Adressen für die FQDNs im VNet zu finden:

> [!NOTE]
> Die vollqualifizierten Domänennamen und IP-Adressen variieren je nach Ihrer Konfiguration. Beispielsweise ist der GUID-Wert im Domänennamen spezifisch für Ihren Arbeitsbereich.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az network private-endpoint show --endpoint-name <endpoint> --resource-group <resource-group> --query 'customDnsConfigs[*].{FQDN: fqdn, IPAddress: ipAddresses[0]}' --output table
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
$workspaceDns=Get-AzPrivateEndpoint -Name <endpoint> -resourcegroupname <resource-group>
$workspaceDns.CustomDnsConfigs | format-table
```

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) Ihren Azure Machine Learning-__Arbeitsbereich__ aus.
1. Wählen Sie im Abschnitt __Einstellungen__ die Option __Private Endpunktverbindungen__ aus.
1. Wählen Sie den Link in der Spalte __Privater Endpunkt__ aus, der angezeigt wird.
1. Eine Liste der vollqualifizierten Domänennamen (FQDN) und IP-Adressen für den privaten Endpunkt des Arbeitsbereichs finden Sie unten auf der Seite.

:::image type="content" source="./media/how-to-custom-dns/private-endpoint-custom-dns.png" alt-text="Liste der FQDNs im Portal":::

---

Die Informationen, die von allen Methoden zurückgegeben werden, sind dieselben: Eine Liste des FQDN und der privaten IP-Adresse für die Ressourcen.

| FQDN | IP-Adresse |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

> [!IMPORTANT]
> Einige FQDNs werden in der Liste des privaten Endpunkts nicht angezeigt, sind jedoch für den Arbeitsbereich erforderlich. Diese FQDNs sind in der folgenden Tabelle aufgeführt und müssen auch Ihrem DNS-Server hinzugefügt werden:
>
> * `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
> * `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
> * `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
> * `<workspace-GUID>.workspace.<region>.aether.ms`
> * Wenn Sie über eine Compute-Instanz verfügen, verwenden Sie `<instance-name>.<region>.instances.azureml.ms`, wobei `<instance-name>` der Name Ihrer Compute-Instanz ist. Verwenden Sie die private IP-Adresse des privaten Endpunkts des Arbeitsbereichs. Beachten Sie, dass nur innerhalb des virtuellen Netzwerks auf die Compute-Instanz zugegriffen werden kann.
>
> Verwenden Sie für alle diese IP-Adressen dieselbe Adresse wie die `*.api.azureml.ms`-Einträge, die von den vorherigen Schritten zurückgegeben wurden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von Azure Machine Learning mit einem virtuellen Netzwerk finden Sie in der [Übersicht über virtuelle Netzwerke](how-to-network-security-overview.md).

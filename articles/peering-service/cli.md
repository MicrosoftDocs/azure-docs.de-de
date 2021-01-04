---
title: Registrieren einer Verbindung mit der Peering Service-Vorschauversion mithilfe der Azure CLI
description: Anleitung zum Registrieren einer Peering Service-Verbindung mithilfe der Azure CLI
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/2/2020
ms.author: derekol
ms.openlocfilehash: e7b696ba052b2aca9e14628327c07275845607ad
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540585"
---
# <a name="register-a-peering-service-connection-by-using-the-azure-cli"></a>Registrieren einer Peering Service-Verbindung mithilfe der Azure CLI

Azure Peering Service ist ein Netzwerkdienst zur Verbesserung der Kundenkonnektivität mit Microsoft Cloud Services wie Microsoft 365, Dynamics 365, SaaS-Diensten (Software-as-a-Service), Azure und anderen Microsoft-Diensten, auf die über das öffentliche Internet zugegriffen werden kann. In diesem Artikel erfahren Sie, wie Sie eine Peering Service-Verbindung mithilfe der Azure CLI registrieren.

- Für diesen Artikel ist mindestens Version 2.0.28 der Azure CLI erforderlich. Führen Sie [az version](/cli/azure/reference-index#az_version) aus, um die installierte Version und die abhängigen Bibliotheken zu ermitteln. Führen Sie [az upgrade](/cli/azure/reference-index#az_upgrade) aus, um das Upgrade auf die aktuelle Version durchzuführen.

## <a name="prerequisites"></a>Voraussetzungen 

Sie benötigen Folgendes:

### <a name="azure-account"></a>Azure-Konto

Sie müssen über ein gültiges und aktives Microsoft Azure-Konto verfügen. Dieses Konto wird zum Einrichten der Peering Service-Verbindung benötigt. Peering Service ist eine Ressource innerhalb von Azure-Abonnements.

### <a name="connectivity-provider"></a>Konnektivitätsanbieter

Sie können mit einem Internetdienst- oder Internet Exchange-Anbieter zusammenarbeiten, um Peering Service zu erhalten und eine Verbindung zwischen Ihrem Netzwerk und dem Microsoft-Netzwerk herzustellen.

Achten Sie darauf, dass es sich bei den Konnektivitätsanbietern um Microsoft-Partner handelt.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

- Für diesen Artikel ist mindestens Version 2.0.28 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

### <a name="1-select-your-subscription"></a>Wählen Sie Ihr Abonnement aus.

Wählen Sie das Abonnement aus, für das Sie die Peering Service-Verbindung registrieren möchten.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

Wenn Sie noch keine Ressourcengruppe besitzen, müssen Sie zuerst eine erstellen, bevor Sie Ihre Peering Service-Verbindung erstellen. Sie können mithilfe des folgenden Befehls eine Ressourcengruppe erstellen:

```azurecli-interactive
az group create -n MyResourceGroup -l "West US"
```

### <a name="2-register-your-subscription-with-the-resource-provider-and-feature-flag"></a>2. Registrieren des Abonnements mit dem Ressourcenanbieter und Featureflag

Registrieren Sie zunächst mithilfe der Azure CLI Ihr Abonnement mit dem Ressourcenanbieter und Featureflag, bevor Sie mit der Registrierung der Peering Service-Verbindung mithilfe der Azure CLI beginnen. Die Azure CLI-Befehle finden Sie hier:

```azurecli-interactive

az feature register --namespace Microsoft.Peering --name AllowPeeringService

```

### <a name="3-register-the-peering-service-connection"></a>3. Registrieren der Peering Service-Verbindung

Registrieren Sie die Peering Service-Verbindung mithilfe der folgenden Befehle über die Azure CLI. In diesem Beispiel wird eine Peering Service-Verbindung namens „myPeeringService“ registriert.

```azurecli-interactive
az peering service create : Create peering service\
  --location -l \
  --name myPeeringService\
  --resource-group -g MyResourceGroup\
  --peering-service-location\
  --peering-service-provider\
  --tags
```

### <a name="4-register-the-prefix"></a>4. Präfix registrieren

Führen Sie die folgenden Befehle über die Azure CLI aus, um das vom Konnektivitätsanbieter bereitgestellte Präfix zu registrieren. In diesem Beispiel wird das Präfix „myPrefix“ registriert.

```azurecli-interactive
az peering service prefix create \
  --name  myPrefix\
  --peering-service-name myPeeringService\
  --resource-group  -g myResourceGroup\
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Peering Service-Verbindung finden Sie unter [Peering Service-Verbindung](connection.md).
- Informationen zur Peering Service-Verbindungstelemetrie finden Sie unter [Peering Service-Verbindungstelemetrie](connection-telemetry.md).
- Informationen zum Messen der Telemetrie finden Sie unter [Messen der Verbindungstelemetrie](measure-connection-telemetry.md).
- Informationen zum Registrieren der Verbindung mithilfe von Azure PowerShell finden Sie unter [Registrieren einer Peering Service-Verbindung – Azure PowerShell](powershell.md).
- Informationen zum Registrieren der Verbindung über das Azure-Portal finden Sie unter [Registrieren einer Peering Service-Verbindung – Azure-Portal](azure-portal.md).

---
title: Erstellen eines Load Balancer mit zonenredundantem Front-End – Azure CLI
titlesuffix: Azure Load Balancer
description: Erfahren Sie, wie Sie einen öffentlichen Load Balancer Standard mit zonenredundantem Front-End mit öffentlichen IP-Adressen mithilfe von Azure CLI erstellen.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 40396a8ef62553dd3ec721e97fe08bf8aa51c731
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66122119"
---
#  <a name="create-a-standard-load-balancer-with-zone-redundant-frontend-using-azure-cli"></a>Erstellen eines Load Balancer Standard mit zonenredundantem Front-End mithilfe der Azure-Befehlszeilenschnittstelle (CLI)

In diesem Artikel wird die Erstellung eines öffentlichen [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) mit zonenredundantem Front-End mithilfe einer öffentlichen IP-Standard-Adresse erläutert.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden, stellen Sie sicher, dass Sie die neueste Version der [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) installiert haben und über [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest) bei einem Azure-Konto angemeldet sind.

> [!NOTE]
>  Unterstützung für Verfügbarkeitszonen ist für ausgewählte Azure-Ressourcen und -Regionen sowie VM-Größenkategorien verfügbar. Weitere Informationen zu den ersten Schritten sowie zu den Azure-Ressourcen, -Regionen und VM-Größenkategorien, die mit Verfügbarkeitszonen verwendet werden können, finden Sie unter [Overview of Availability Zones in Azure (Preview) (Übersicht über Verfügbarkeitszonen in Azure (Vorschauversion))](https://docs.microsoft.com/azure/availability-zones/az-overview). Wenn Sie Unterstützung benötigen, können Sie über [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) Kontakt aufnehmen oder [ein Azure-Supportticket erstellen](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 


## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mithilfe des folgenden Befehls eine Ressourcengruppe:

```azurecli-interactive
az group create --name myResourceGroupSLB --location westeurope
```

## <a name="create-a-public-ip-standard"></a>Erstellen eines öffentlichen IP-Standards

Erstellen Sie mithilfe des folgenden Befehls einen öffentlichen IP-Standard:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku Standard
```

## <a name="create-a-load-balancer"></a>Einrichten eines Load Balancers

Erstellen Sie mithilfe des folgenden Befehls einen öffentlichen Load Balancer Standard mit der öffentlichen Standard-IP-Adresse, die Sie im vorherigen Schritt erstellt haben:

```azurecli-interactive
az network lb create --resource-group myResourceGroupSLB --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>Erstellen eines LB-Tests an Port 80

Erstellen Sie mithilfe des folgenden Befehls einen Integritätstest des Lastenausgleichs:

```azurecli-interactive
az network lb probe create --resource-group myResourceGroupSLB --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>Erstellen einer LB-Regel für Port 80

Erstellen Sie mithilfe des folgenden Befehls eine Lastenausgleichsregel:

```azurecli-interactive
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEnd \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr zu [Standard-Lastenausgleich und Verfügbarkeitszonen](load-balancer-standard-availability-zones.md).




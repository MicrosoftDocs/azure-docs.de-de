---
title: Erstellen eines Load Balancers mit einem Zonen-Front-End – Azure CLI
titlesuffix: Azure Load Balancer
description: Erfahren Sie, wie Sie einen Load Balancer Standard mit einem Zonen-Front-End mithilfe der Azure CLI erstellen.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: 3b89c11c11276781ec63367247601fccfd2fa858
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66122198"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-cli"></a>Erstellen eines Standard-Lastenausgleichs mit einem Zonen-Front-End mithilfe der Azure CLI

In diesem Artikel wird die Erstellung eines öffentlichen [Load Balancers Standard](https://aka.ms/azureloadbalancerstandard) mit einem Zonen-Front-End erläutert. Ein Zonen-Front-End bedeutet, dass alle eingehenden oder ausgehenden Datenflüsse von einer einzelnen Zone in einer Region verarbeitet werden. Sie können einen Lastenausgleich mit einem Zonen-Front-End erstellen, indem Sie eine öffentliche Standard-IP-Adresse in einer Zone in der jeweiligen Front-End-Konfiguration verwenden. Um zu verstehen, wie Verfügbarkeitszonen mit dem Standard-Lastenausgleich arbeiten, lesen Sie [Standard-Lastenausgleich und Verfügbarkeitszonen](load-balancer-standard-availability-zones.md). 

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden, stellen Sie sicher, dass Sie die neueste Version der [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) installiert haben und über [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest) bei einem Azure-Konto angemeldet sind.

> [!NOTE]
> Unterstützung für Verfügbarkeitszonen ist für ausgewählte Azure-Ressourcen und -Regionen sowie VM-Größenkategorien verfügbar. Weitere Informationen zu den ersten Schritten sowie zu den Azure-Ressourcen, -Regionen und VM-Größenkategorien, die mit Verfügbarkeitszonen verwendet werden können, finden Sie unter [Overview of Availability Zones in Azure (Preview) (Übersicht über Verfügbarkeitszonen in Azure (Vorschauversion))](https://docs.microsoft.com/azure/availability-zones/az-overview). Wenn Sie Unterstützung benötigen, können Sie über [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) Kontakt aufnehmen oder [ein Azure-Supportticket erstellen](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 


## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mithilfe des folgenden Befehls eine Ressourcengruppe:

```azurecli-interactive
az group create --name myResourceGroupZLB --location westeurope
```

## <a name="create-a-public-standard-ip-address"></a>Erstellen einer öffentlichen Standard-IP-Adresse

Erstellen Sie mithilfe des folgenden Befehls eine öffentliche Standard-IP-Adresse in einer Zone:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroupZLB --name myPublicIPZonal --sku Standard --zone 1
```

## <a name="create-a-load-balancer"></a>Einrichten eines Load Balancers

Erstellen Sie mithilfe des folgenden Befehls einen öffentlichen Load Balancer Standard mit der öffentlichen Standard-IP-Adresse, die Sie im vorherigen Schritt erstellt haben:

```azurecli-interactive
az network lb create --resource-group myResourceGroupZLB --name myLoadBalancer --public-ip-address myPublicIPZonal --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>Erstellen eines LB-Tests an Port 80

Erstellen Sie mithilfe des folgenden Befehls einen Integritätstest des Lastenausgleichs:

```azurecli-interactive
az network lb probe create --resource-group myResourceGroupZLB --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>Erstellen einer LB-Regel für Port 80

Erstellen Sie mithilfe des folgenden Befehls eine Lastenausgleichsregel:

```azurecli-interactive
az network lb rule create --resource-group myResourceGroupZLB --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEnd \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr zu [Standard-Lastenausgleich und Verfügbarkeitszonen](load-balancer-standard-availability-zones.md).




---
title: Erstellen und Konfigurieren eines Azure DDoS Protection-Plans über die Azure CLI
description: Erfahren Sie, wie Sie einen DDoS Protection-Plan über die Azure CLI erstellen
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: e2f5528fde977520dc0aa0215a480a40ef8f1e7d
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94989614"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-cli"></a>Schnellstart: Erstellen und Konfigurieren eines Azure DDoS Protection Standard-Plans über die Azure CLI

Erfahren Sie, wie Sie mit der Verwendung von Azure DDoS Protection Standard über die Azure CLI beginnen. 

Ein DDoS-Schutzplan definiert abonnementübergreifend eine Reihe von virtuellen Netzwerken, für die der DDoS-Schutzstandard aktiviert ist. Sie können einen DDoS-Schutzplan für Ihre Organisation konfigurieren und virtuelle Netzwerke über verschiedene Abonnements hinweg mit demselben Plan verknüpfen. 

In dieser Schnellstartanleitung erstellen Sie einen DDoS-Schutzplan und verknüpfen ihn mit einem virtuellen Netzwerk. 

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure CLI (lokal installiert) oder Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für diese Schnellstartanleitung mindestens die Azure CLI-Version 2.0.28 verwenden. Führen Sie `az --version` aus, um die Version zu finden. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-ddos-protection-plan"></a>Erstellen eines DDoS Protection-Plans

In Azure ordnen Sie verwandte Ressourcen einer Ressourcengruppe zu. Sie können entweder eine vorhandene Ressourcengruppe verwenden oder eine neue erstellen.

Verwenden Sie zum Erstellen einer Ressourcengruppe [az group create](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-create). In diesem Beispiel legen wir für die Ressourcengruppe den Namen _MyResourceGroup_ fest, und wir verwenden den Standort _USA, Osten_:

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus
```

Erstellen Sie nun einen DDoS-Schutzplan mit dem Namen _MyDdosProtectionPlan_:

```azurecli-interactive
az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

## <a name="enable-ddos-protection-for-a-virtual-network"></a>Aktivieren des DDoS-Schutzes für ein virtuelles Netzwerk

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>Aktivieren des DDoS-Schutzes für ein neues virtuelles Netzwerk

Sie können den DDoS-Schutz beim Erstellen eines virtuellen Netzwerks aktivieren. In diesem Beispiel legen wir für das virtuelle Netzwerk den Namen _MyVnet_ fest: 

```azurecli-interactive
az network vnet create \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --location eastus \
    --ddos-protection true
```

Ein virtuelles Netzwerk kann nicht in eine andere Ressourcengruppe oder ein anderes Abonnement verschoben werden, wenn der DDoS-Standard für das virtuelle Netzwerk aktiviert ist. Wenn Sie ein virtuelles Netzwerks mit aktiviertem DDoS-Standard verschieben müssen, deaktivieren Sie zuerst den DDoS-Standard, verschieben Sie das virtuelle Netzwerk, und aktivieren Sie dann den DDoS-Standard. Nach der Verschiebung werden die automatisch optimierten Schwellenwerte der Richtlinie für alle geschützten, öffentlichen IP-Adressen im virtuellen Netzwerk zurückgesetzt.

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>Aktivieren des DDoS-Schutzes für ein vorhandenes virtuelles Netzwerk

Beim [Erstellen eines DDoS-Schutzplans](#create-a-ddos-protection-plan) können Sie dem Plan ein oder mehrere virtuelle Netzwerke zuordnen. Um mehrere virtuelle Netzwerke hinzuzufügen, führen Sie die Namen oder IDs auf. Trennen Sie die Namen oder IDs dabei jeweils durch ein Leerzeichen. In diesem Beispiel fügen wir _MyVnet_ hinzu:

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
    --vnet MyVnet
```

Alternativ können Sie den DDoS-Schutz auch für ein vorhandenes virtuelles Netzwerk aktivieren:

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --vnet MyVnet \
    --ddos-protection true
```

## <a name="validate-and-test"></a>Überprüfen und Testen

Überprüfen Sie zunächst die Details Ihres DDoS-Schutzplans:

```azurecli-interactive
az network ddos-protection show \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

Stellen Sie sicher, dass der Befehl die korrekten Details Ihres DDoS-Schutzplans zurückgibt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können Ihre Ressourcen für das nächste Tutorial beibehalten. Löschen Sie die Ressourcengruppe _MyResourceGroup_, wenn Sie sie nicht mehr benötigen. Wenn Sie die Ressourcengruppe löschen, werden auch der DDoS-Schutzplan und alle zugehörigen Ressourcen gelöscht. 

Führen Sie [az group delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az_group_delete) aus, um die Ressourcengruppe zu löschen:

```azurecli-interactive
az group delete \
--name MyResourceGroup 
```

Aktualisieren Sie ein vorhandenes virtuelles Netzwerk, um den DDoS-Schutz zu deaktivieren:

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --vnet MyVnet \
    --ddos-protection false
```

Wenn Sie einen DDoS-Schutzplan löschen möchten, müssen Sie zuerst die Zuordnung aller virtuellen Netzwerke für diesen Plan aufheben. 

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Anzeigen und Konfigurieren von Telemetriedaten für Ihren DDoS-Schutzplan, finden Sie in den Tutorials.

> [!div class="nextstepaction"]
> [Anzeigen und Konfigurieren der DDoS Protection-Telemetrie](telemetry-monitoring-alerting.md)
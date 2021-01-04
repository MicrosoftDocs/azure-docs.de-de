---
title: Erstellen, Ändern oder Löschen eines VNet TAP – Azure-Befehlszeilenschnittstelle
description: In diesem Artikel erfahren Sie, wie Sie einen TAP eines virtuellen Netzwerks mithilfe der Azure CLI erstellen, ändern oder löschen.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/18/2018
ms.author: kaanan
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8e3a56e4a6eb1fb6eb633021178ef78f8ac7287d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014787"
---
# <a name="work-with-a-virtual-network-tap-using-the-azure-cli"></a>Arbeiten mit einem TAP eines virtuellen Netzwerks über die Azure CLI

Mit dem TAP (Terminal Access Point) für virtuelle Azure-Netzwerke können Sie Ihren VM-Netzwerkdatenverkehr kontinuierlich an einen Netzwerkpaketcollector oder ein Analysetool streamen. Der Collector oder das Analysetool wird von einem Partner für [virtuelle Netzwerkappliances](https://azure.microsoft.com/solutions/network-appliances/) bereitgestellt. Eine Liste der Partnerlösungen, die für die Arbeit mit einem TAP eines virtuellen Netzwerks geprüft wurden, finden Sie unter [Partnerlösungen für TAPs von virtuellen Netzwerken](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions). 

## <a name="create-a-virtual-network-tap-resource"></a>Erstellen einer TAP-Ressource für ein virtuelles Netzwerk

Lesen Sie die [Voraussetzungen](virtual-network-tap-overview.md#prerequisites), bevor Sie eine TAP-Ressource für ein virtuelles Netzwerk erstellen. Sie können die nachfolgenden Befehle in [Azure Cloud Shell](https://shell.azure.com/bash) oder über die Azure-Befehlszeilenschnittstelle (CLI) auf Ihrem Computer ausführen. Azure Cloud Shell ist eine kostenlose interaktive Shell, für die keine Installation der Azure CLI auf Ihrem Computer erforderlich ist. Sie müssen sich mit einem Konto mit den entsprechenden [Berechtigungen](virtual-network-tap-overview.md#permissions) bei Azure anmelden. Für diesen Artikel ist die Azure CLI-Version 2.0.46 oder höher erforderlich. Führen Sie `az --version` aus, um die installierte Version zu ermitteln. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Installieren von Azure CLI 2.0](/cli/azure/install-azure-cli) Informationen dazu. TAP von virtuellen Netzwerken ist derzeit als Erweiterung verfügbar. Zum Installieren der Erweiterung müssen Sie `az extension add -n virtual-network-tap` ausführen. Wenn Sie die Azure CLI lokal ausführen, müssen Sie auch `az login` ausführen, um eine Verbindung mit Azure herzustellen.

1. Rufen Sie die ID Ihres Abonnements in einer Variable ab, die in einem späteren Schritt verwendet wird:

   ```azurecli-interactive
   subscriptionId=$(az account show \
   --query id \
   --out tsv)
   ```

2. Legen Sie die Abonnement-ID fest, mit der Sie eine TAP-Ressource für ein virtuelles Netzwerk erstellen.

   ```azurecli-interactive
   az account set --subscription $subscriptionId
   ```

3. Registrieren Sie erneut die Abonnement-ID, mit der Sie eine TAP-Ressource für ein virtuelles Netzwerk erstellen. Wenn beim Erstellen einer TAP-Ressource ein Registrierungsfehler angezeigt wird, führen Sie den folgenden Befehl aus:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Network --subscription $subscriptionId
   ```

4. Wenn das Ziel für den TAP eines virtuellen Netzwerks die Netzwerkschnittstelle auf der virtuellen Netzwerkappliance für den Collector oder das Analysetool ist, gehen Sie wie folgt vor:

   - Rufen Sie die IP-Konfiguration der Netzwerkschnittstelle der virtuellen Netzwerkappliance in einer Variable ab, die in einem späteren Schritt verwendet wird. Die ID ist der Endpunkt, der den TAP-Datenverkehr aggregiert. Das folgende Beispiel ruft die ID der IP-Konfiguration *ipconfig1* für eine Netzwerkschnittstelle mit dem Namen *myNetworkInterface* in einer Ressourcengruppe namens *myResourceGroup* ab:

      ```azurecli-interactive
       IpConfigId=$(az network nic ip-config show \
       --name ipconfig1 \
       --nic-name myNetworkInterface \
       --resource-group myResourceGroup \
       --query id \
       --out tsv)
      ```

   - Erstellen Sie mit der ID der IP-Konfiguration als Ziel und einer optionalen Porteigenschaft den TAP für ein virtuelles Netzwerk in der Azure-Region „USA, Westen-Mitte“. Der Port gibt den Zielport in der IP-Konfiguration der Netzwerkschnittstelle an, an der der TAP-Datenverkehr empfangen wird:  

      ```azurecli-interactive
       az network vnet tap create \
       --resource-group myResourceGroup \
       --name myTap \
       --destination $IpConfigId \
       --port 4789 \
       --location westcentralus
      ```

5. Wenn das Ziel für den TAP des virtuellen Netzwerks ein interner Azure-Lastenausgleich ist, gehen Sie wie folgt vor:
  
   - Rufen Sie die Front-End-IP-Konfiguration des internen Azure-Lastenausgleichs in einer Variable ab, die in einem späteren Schritt verwendet wird. Die ID ist der Endpunkt, der den TAP-Datenverkehr aggregiert. Das folgende Beispiel ruft die ID der Front-End-IP-Konfiguration *frontendipconfig1* für einen Lastenausgleich namens *myInternalLoadBalancer* in einer Ressourcengruppe mit dem Namen *myResourceGroup* ab:

      ```azurecli-interactive
      FrontendIpConfigId=$(az network lb frontend-ip show \
      --name frontendipconfig1 \
      --lb-name myInternalLoadBalancer \
      --resource-group myResourceGroup \
      --query id \
      --out tsv)
      ```

   - Erstellen Sie mit der ID der Front-End-IP-Konfiguration als Ziel und einer optionalen Porteigenschaft den TAP für ein virtuelles Netzwerk. Der Port gibt den Zielport in der Front-End-IP-Konfiguration an, an der der TAP-Datenverkehr empfangen wird:  

      ```azurecli-interactive
      az network vnet tap create \
      --resource-group myResourceGroup \
      --name myTap \
      --destination $FrontendIpConfigId \
      --port 4789 \
     --location westcentralus
     ```

6. Bestätigen Sie die Erstellung des TAP für ein virtuelles Netzwerk:

   ```azurecli-interactive
   az network vnet tap show \
   --resource-group myResourceGroup
   --name myTap
   ```

## <a name="add-a-tap-configuration-to-a-network-interface"></a>Hinzufügen einer TAP-Konfiguration zu einer Netzwerkschnittstelle

1. Rufen Sie die ID einer vorhandenen TAP-Ressource eines virtuellen Netzwerks ab. Im folgenden Beispiel wird ein TAP für ein virtuelles Netzwerk namens *myTap* in einer Ressourcengruppe mit dem Namen *myResourceGroup* abgerufen:

   ```azurecli-interactive
   tapId=$(az network vnet tap show \
   --name myTap \
   --resource-group myResourceGroup \
   --query id \
   --out tsv)
   ```

2. Erstellen Sie eine TAP-Konfiguration für die Netzwerkschnittstelle des überwachten virtuellen Computers. Das folgende Beispiel erstellt eine TAP-Konfiguration für eine Netzwerkschnittstelle mit dem Namen *myNetworkInterface*:

   ```azurecli-interactive
   az network nic vtap-config create \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --vnet-tap $tapId \
   --name mytapconfig \
   --subscription subscriptionId
   ```

3. Bestätigen Sie die Erstellung der TAP-Konfiguration:

   ```azurecli-interactive
   az network nic vtap-config show \
   --resource-group myResourceGroup \
   --nic-name myNetworkInterface \
   --name mytapconfig \
   --subscription subscriptionId
   ```

## <a name="delete-the-tap-configuration-on-a-network-interface"></a>Löschen der TAP-Konfiguration für eine Netzwerkschnittstelle

   ```azurecli-interactive
   az network nic vtap-config delete \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --name myTapConfig \
   --subscription subscriptionId
   ```

## <a name="list-virtual-network-taps-in-a-subscription"></a>Auflisten der TAPs von virtuellen Netzwerken in einem Abonnement

   ```azurecli-interactive
   az network vnet tap list
   ```

## <a name="delete-a-virtual-network-tap-in-a-resource-group"></a>Löschen eines TAPs für ein virtuelles Netzwerk in einer Ressourcengruppe

   ```azurecli-interactive
   az network vnet tap delete \
   --resource-group myResourceGroup \
   --name myTap
   ```

---
title: 'Durchführen des Lastenausgleichs für VM-Datenverkehr zur Erzielung von Hochverfügbarkeit: Azure CLI – Azure Load Balancer'
description: Dieses Azure CLI-Skriptbeispiel veranschaulicht, wie Sie zum Ermöglichen der Hochverfügbarkeit einen Lastausgleich für den Datenverkehr zu virtuellen Computern vornehmen.
services: load-balancer
documentationcenter: load-balancer
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: sample
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7041bb568aed7ec0619e57887db64f8041168ee6
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696607"
---
# <a name="azure-cli-script-example-load-balance-traffic-to-vms-for-high-availability"></a>Azure CLI-Skriptbeispiel: Lastenausgleich für den Datenverkehr zu virtuellen Computern für Hochverfügbarkeit

Dieses Azure CLI-Beispielskript erstellt alle Komponenten, die zum Ausführen mehrerer Ubuntu-VMs in einer Konfiguration mit Hochverfügbarkeit und Lastenausgleich benötigt werden. Nach dem Ausführen dieses Skripts verfügen Sie über drei virtuelle Computer, die in einer Azure-Verfügbarkeitsgruppe zusammengefasst und über eine Azure Load Balancer-Instanz zugänglich sind. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung 

Führen Sie den folgenden Befehl aus, um die Ressourcengruppe, den virtuellen Computer und alle zugehörigen Ressourcen zu entfernen.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Erläuterung des Skripts

In diesem Skript werden die folgenden Befehle verwendet, um eine Ressourcengruppe, einen virtuellen Computer, eine Verfügbarkeitsgruppe, einen Load Balancer und alle zugehörigen Ressourcen zu erstellen. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) | Erstellt ein virtuelles Azure-Netzwerk und ein Subnetz. |
| [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) | Erstellt eine öffentliche IP-Adresse mit einer statischen IP-Adresse und zugeordnetem DNS-Namen. |
| [az network lb create](/cli/azure/network/lb#az-network-lb-create) | Erstellt eine Azure Load Balancer-Instanz. |
| [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) | Erstellt einen Lastenausgleichtest. Ein Lastenausgleichtest wird verwendet, um jeden virtuellen Computer in der Load Balancer-Gruppe zu überwachen. Falls eine VM nicht verfügbar ist, wird der Datenverkehr nicht an diese VM geroutet. |
| [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) | Erstellt eine Lastenausgleichsregel. In diesem Beispiel wird eine Regel für den Port 80 erstellt. Wenn HTTP-Datenverkehr beim Load Balancer eingeht, wird dieser an Port 80 einer der VMs in der Load Balancer-Gruppe geroutet. |
| [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule#az-network-lb-inbound-nat-rule-create) | Erstellt eine Regel des Load Balancers (Network Address Translation, NAT).  Mithilfe von NAT-Regeln wird ein Load Balancer-Port einem Port auf einer VM zugeordnet. In diesem Beispiel wird eine NAT-Regel für den SSH-Datenverkehr für jede VM in der Load Balancer-Gruppe erstellt.  |
| [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) | Erstellt eine Netzwerksicherheitsgruppe (NSG), die als Sicherheitsgrenze zwischen dem Internet und dem virtuellen Computer fungiert. |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) | Erstellt eine NSG-Regel zum Zulassen von eingehendem Datenverkehr. In diesem Beispiel wird Port 22 für SSH-Datenverkehr geöffnet. |
| [az network nic create](/cli/azure/network/nic#az-network-nic-create) | Erstellt eine virtuelle Netzwerkkarte und verbindet diese mit dem virtuellen Netzwerk, dem Subnetz und der NSG. |
| [az vm availability-set create](/cli/azure/network/lb/rule#az-network-lb-rule-create) | Erstellt eine Verfügbarkeitsgruppe. Verfügbarkeitsgruppen stellen die Verfügbarkeit von Anwendungen sicher, indem sie die virtuellen Computer auf physische Ressourcen verteilen. So wirken sich Ausfälle nicht auf die gesamte Gruppe aus. |
| [az vm create](/cli/azure/vm#az-vm-create) | Erstellt den virtuellen Computer und verbindet diesen mit der Netzwerkkarte, dem virtuellen Netzwerk, dem Subnetz und der NSG. Dieser Befehl legt außerdem das zu verwendende VM-Image und die Administratoranmeldeinformationen fest.  |
| [az group delete](/cli/azure/vm/extension#az-vm-extension-set) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Zusätzliche Azure-Netzwerk-CLI-Skriptbeispiele finden Sie in der [Azure-Netzwerkdokumentation](../cli-samples.md).
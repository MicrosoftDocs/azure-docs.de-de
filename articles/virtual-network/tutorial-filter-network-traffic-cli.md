---
title: Filtern von Netzwerkdatenverkehr – Azure-Befehlszeilenschnittstelle | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie Netzwerkdatenverkehr an ein Subnetz mithilfe einer Netzwerksicherheitsgruppe über die Azure-Befehlszeilenschnittstelle filtern.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: a1ade6e823201419c3a742a36c66a50a9dc09976
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64728802"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-the-azure-cli"></a>Filtern von Netzwerkdatenverkehr mithilfe einer Netzwerksicherheitsgruppe über die Azure-Befehlszeilenschnittstelle

Sie können eingehenden und ausgehenden Netzwerkdatenverkehr im Subnetz eines virtuellen Netzwerks mithilfe einer Netzwerksicherheitsgruppe filtern. Netzwerksicherheitsgruppen enthalten Sicherheitsregeln, die Netzwerkdatenverkehr nach IP-Adresse, Port und Protokoll filtern. Sicherheitsregeln werden auf Ressourcen angewendet, die in einem Subnetz bereitgestellt sind. In diesem Artikel werden folgende Vorgehensweisen behandelt:

* Erstellen von Netzwerksicherheitsgruppe und Sicherheitsregeln
* Erstellen eines virtuellen Netzwerks und Zuweisen einer Netzwerksicherheitsgruppe zu einem Subnetz
* Bereitstellen von virtuellen Computern in einem Subnetz
* Testen von Datenverkehrsfiltern

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie die Befehlszeilenschnittstelle (CLI) lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 2.0.28 der Azure CLI ausführen. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli). 


## <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

Eine Netzwerksicherheitsgruppe enthält Sicherheitsregeln. Sicherheitsregeln geben eine Quelle und ein Ziel an. Bei Quellen und Zielen kann es sich um Anwendungssicherheitsgruppen handeln.

### <a name="create-application-security-groups"></a>Erstellen von Anwendungssicherheitsgruppen

Erstellen Sie mit [az group create](/cli/azure/group) eine Ressourcengruppe für alle in diesem Artikel erstellten Ressourcen. Das folgende Beispiel erstellt eine Ressourcengruppe am Standort *eastus*: 

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Erstellen Sie mit [az network asg create](/cli/azure/network/asg) eine Anwendungssicherheitsgruppe. Mit einer Anwendungssicherheitsgruppe können Sie Server mit ähnlichen Anforderungen an die Portfilterung gruppieren. Das folgende Beispiel erstellt zwei Anwendungssicherheitsgruppen.

```azurecli-interactive
az network asg create \
  --resource-group myResourceGroup \
  --name myAsgWebServers \
  --location eastus

az network asg create \
  --resource-group myResourceGroup \
  --name myAsgMgmtServers \
  --location eastus
```

### <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

Erstellen Sie mit [az network nsg create](/cli/azure/network/nsg) eine Netzwerksicherheitsgruppe. Das folgende Beispiel erstellt eine Netzwerksicherheitsgruppe namens *myNsg*: 

```azurecli-interactive 
# Create a network security group
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsg
```

### <a name="create-security-rules"></a>Erstellen von Sicherheitsregeln

Erstellen Sie mit [az network nsg rule create](/cli/azure/network/nsg/rule) eine Sicherheitsregel. Das folgende Beispiel erstellt eine Regel, die eingehenden Datenverkehr aus dem Internet in die Anwendungssicherheitsgruppe *myWebServers* über die Ports 80 und 443 zulässt:

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-Web-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgWebServers" \
  --destination-port-range 80 443
```

Das folgende Beispiel erstellt eine Regel, die eingehenden Datenverkehr aus dem Internet in die Anwendungssicherheitsgruppe *myMgmtServers* über Port 22 zulässt:

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 110 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgMgmtServers" \
  --destination-port-range 22
```

In diesem Artikel wird SSH (Port 22) für den virtuellen Computer *myAsgMgmtServers* für das Internet verfügbar gemacht. In Produktionsumgebungen empfiehlt es sich, nicht Port 22 für das Internet verfügbar zu machen, sondern über [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) oder eine [private](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Netzwerkverbindung eine Verbindung zu den Azure-Ressourcen herzustellen, die Sie verwalten möchten.

## <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Erstellen Sie mit [az network vnet create](/cli/azure/network/vnet) ein virtuelles Netzwerk. Das folgende Beispiel erstellt ein virtuelles Netzwerk namens *myVirtualNetwork*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16
```

Fügen Sie einem virtuellen Netzwerk mit [az network vnet subnet create](/cli/azure/network/vnet/subnet) ein Subnetz hinzu. Das folgende Beispiel fügt dem virtuellen Netzwerk ein Subnetz namens *mySubnet* hinzu und weist diesem die Netzwerksicherheitsgruppe *myNsg* zu:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name mySubnet \
  --address-prefix 10.0.0.0/24 \
  --network-security-group myNsg
```

## <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Erstellen Sie zwei virtuelle Computer im virtuellen Netzwerk, sodass Sie in einem späteren Schritt die Datenverkehrsfilterung überprüfen können. 

Erstellen Sie mit [az vm create](/cli/azure/vm) einen virtuellen Computer. Das folgende Beispiel erstellt einen virtuellen Computer, der als Webserver dienen wird. Durch die `--asgs myAsgWebServers`-Option fügt Azure die für den virtuellen Computer erstellte Netzwerkschnittstelle als Mitglied der Anwendungssicherheitsgruppe *myAsgWebServers* hinzu.

Die `--nsg ""`-Option wird angegeben, um zu verhindern, dass Azure eine standardmäßige Netzwerksicherheitsgruppe für die Netzwerkschnittstelle erstellt, die Azure während der Erstellung des virtuellen Computers erstellt. Zur Vereinfachung dieses Artikels wird ein Kennwort verwendet. Schlüssel werden in der Regel in Produktionsbereitstellungen verwendet. Wenn Sie Schlüssel verwenden, müssen Sie für die verbleibenden Schritte außerdem die SSH-Agent-Weiterleitung konfigurieren. Weitere Informationen finden Sie in der Dokumentation Ihres SSH-Clients. Ersetzen Sie `<replace-with-your-password>` im folgenden Befehl durch ein Kennwort Ihrer Wahl.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgWebServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

Die Erstellung des virtuellen Computers dauert einige Minuten. Nachdem der virtuelle Computer erstellt wurde, wird eine Ausgabe ähnlich des folgenden Beispiels zurückgegeben: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmWeb",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Notieren Sie sich **publicIpAddress**. Diese Adresse wird in einem späteren Schritt für den Zugriff auf den virtuellen Computer über das Internet verwendet.  Erstellen Sie einen virtuellen Computer, der als Verwaltungsserver dient:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgMgmtServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

Die Erstellung des virtuellen Computers dauert einige Minuten. Nachdem der virtuelle Computer erstellt ist, beachten Sie die **publicIpAddress** in der zurückgegebenen Ausgabe. Diese Adresse wird im nächsten Schritt verwendet, um auf den virtuellen Computer zuzugreifen. Fahren Sie erst dann mit dem nächsten Schritt fort, wenn Azure die Erstellung des virtuellen Computers abgeschlossen hat.

## <a name="test-traffic-filters"></a>Testen von Datenverkehrsfiltern

Erstellen Sie mit dem folgenden Befehl eine SSH-Sitzung mit dem virtuellen Computer *myVmMgmt*. Ersetzen Sie *\<publicIpAddress>* durch die öffentliche IP-Adresse Ihrer VM. Im Beispiel oben lautet die IP-Adresse *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Geben Sie bei entsprechender Aufforderung das Kennwort ein, das Sie im Schritt [Erstellen von virtuellen Computern](#create-virtual-machines) eingegeben haben.

Die Verbindung kann erfolgreich hergestellt werden, weil eingehender Datenverkehr aus dem Internet über Port 22 in die Anwendungssicherheitsgruppe *myAsgMgmtServers* zulässig ist, in der sich die an den virtuellen Computer *myVmMgmt* angefügte Netzwerkschnittstelle befindet.

Erstellen Sie mit dem folgenden Befehl eine SSH-Verbindung vom virtuellen Computer *myVmMgmt* zum virtuellen Computer *myVmWeb*:

```bash 
ssh azureuser@myVmWeb
```

Die Verbindung kann erfolgreich hergestellt werden, weil eine Standardsicherheitsregel innerhalb jeder Netzwerksicherheitsgruppe Datenverkehr über alle Ports zwischen allen IP-Adressen in einem virtuellen Netzwerk zulässt. Sie können keine SSH-Verbindung mit dem virtuellen Computer *myVmWeb* aus dem Internet herstellen, weil die Sicherheitsregel für *myAsgWebServers* keinen eingehenden Datenverkehr aus dem Internet über Port 22 zulässt.

Verwenden Sie die folgenden Befehle, um den nginx-Webserver auf dem virtuellen Computer *myVmWeb* zu installieren:

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

Für den virtuellen Computer *myVmWeb* ist ausgehender Datenverkehr ins Internet für den Abruf von nginx zulässig, weil eine Standardsicherheitsregel sämtlichen ausgehenden Datenverkehr ins Internet zulässt. Beenden Sie die *myVmWeb*-SSH-Sitzung. Sie befinden sich an der Eingabeaufforderung `username@myVmMgmt:~$` des virtuellen Computers *myVmMgmt*. Um die nginx-Willkommensseite vom virtuellen Computer *myVmWeb* abzurufen, geben Sie den folgenden Befehl ein:

```bash
curl myVmWeb
```

Melden Sie sich vom virtuellen Computer *myVmMgmt* ab. Um zu bestätigen, dass Sie außerhalb von Azure auf den Webserver *myVmWeb* zugreifen können, geben Sie auf Ihrem eigenen Computer `curl <publicIpAddress>` ein. Die Verbindung kann erfolgreich hergestellt werden, weil eingehender Datenverkehr aus dem Internet über Port 80 in die Anwendungssicherheitsgruppe *myAsgWebServers* zulässig ist, in der sich die an den virtuellen Computer *myVmWeb* angefügte Netzwerkschnittstelle befindet.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe und alle enthaltenen Ressourcen nicht mehr benötigt werden, können Sie sie mit [az group delete](/cli/azure/group) entfernen.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie eine Netzwerksicherheitsgruppe erstellt und dem Subnetz eines virtuellen Netzwerks zugeordnet. Weitere Informationen zu Netzwerksicherheitsgruppen finden Sie unter [Netzwerksicherheitsgruppen – Übersicht](security-overview.md) sowie unter [Verwalten einer Netzwerksicherheitsgruppe](manage-network-security-group.md).

Azure leitet standardmäßig Datenverkehr zwischen Subnetzen weiter. Sie können Datenverkehr zwischen Subnetzen stattdessen auch über einen virtuellen Computer weiterleiten, der als Firewall dient. Informationen dazu finden Sie unter [Erstellen einer Routingtabelle](tutorial-create-route-table-cli.md).

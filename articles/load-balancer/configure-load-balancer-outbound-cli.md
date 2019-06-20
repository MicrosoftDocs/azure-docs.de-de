---
title: Konfigurieren von Lastenausgleichs- und Ausgangsregeln mit der Azure CLI
titlesuffix: Azure Load Balancer
description: In diesem Artikel wird gezeigt, wie Sie Lastenausgleichs- und Ausgangsregeln in Load Balancer Standard mithilfe der Azure CLI konfigurieren.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: kumud
ms.openlocfilehash: f28088a1a0586964092a0b5f86ce8bf0f95402cd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66122445"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>Konfigurieren von Lastenausgleichs- und Ausgangsregeln in Load Balancer Standard mithilfe der Azure CLI

In dieser Schnellstartanleitung erfahren Sie, wie Sie Ausgangsregeln in Load Balancer Standard mithilfe von Azure CLI konfigurieren.  

Anschließend enthält die Load Balancer-Ressource zwei Front-Ends mit zugeordneten Regeln: eines für eingehenden und eines für ausgehenden Datenverkehr.  Jedes Front-End verfügt über einen Verweis auf eine öffentliche IP-Adresse. In diesem Szenario werden für Eingangs- und Ausgangsdatenverkehr unterschiedliche öffentliche IP-Adressen verwendet.   Die Lastenausgleichsregel bietet nur Lastenausgleich für eingehenden Datenverkehr, und die Ausgangsregel steuert die für den virtuellen Computer angegebene NAT für ausgehenden Datenverkehr.  Dieser Schnellstart verwendet zwei separate Back-End-Pools: einen für den eingehenden und einen für den ausgehenden Datenverkehr, um die Leistungsfähigkeit zu veranschaulichen und Flexibilität für dieses Szenario zu ermöglichen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial mindestens die Azure CLI-Version 2.0.28 ausführen. Führen Sie `az --version` aus, um die Version zu finden. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Installieren von Azure CLI 2.0]( /cli/azure/install-azure-cli) Informationen dazu.

## <a name="create-resource-group"></a>Ressourcengruppe erstellen

Erstellen Sie mit [az group create](https://docs.microsoft.com/cli/azure/group) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Im folgenden Beispiel wird eine Ressourcengruppe namens *myresourcegroupoutbound* am Standort *eastus2* erstellt:

```azurecli-interactive
  az group create \
    --name myresourcegroupoutbound \
    --location eastus2
```
## <a name="create-virtual-network"></a>Virtuelles Netzwerk erstellen
Erstellen Sie ein virtuelles Netzwerk namens *myvnetoutbound* mit dem Subnetz *mysubnetoutbound* in *myresourcegroupoutbound*, indem Sie den Befehl [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) verwenden.

```azurecli-interactive
  az network vnet create \
    --resource-group myresourcegroupoutbound \
    --name myvnetoutbound \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mysubnetoutbound
    --subnet-prefix 192.168.0.0/24
```

## <a name="create-inbound-public-ip-address"></a>Erstellen einer öffentlichen Eingangs-IP-Adresse 

Um über das Internet auf Ihre Web-App zugreifen zu können, benötigen Sie eine öffentliche IP-Adresse für den Lastenausgleich. Ein Load Balancer im Standard-Tarif unterstützt nur öffentliche Standard-IP-Adressen. Verwenden Sie [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) zum Erstellen einer öffentlichen Standard-IP-Adresse namens *mypublicipinbound* in *myresourcegroupoutbound*.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipinbound --sku standard
```

## <a name="create-outbound-public-ip-address"></a>Erstellen einer öffentlichen Ausgangs-IP-Adresse 

Erstellen Sie mit [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) eine Standard-IP-Adresse für die Ausgangskonfiguration des Load Balancer-Front-Ends.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipoutbound --sku standard
```

## <a name="create-azure-load-balancer"></a>Erstellen einer Azure Load Balancer-Instanz

In diesem Abschnitt erfahren Sie, wie Sie die folgenden Komponenten des Lastenausgleichs erstellen und konfigurieren:
  - Front-End-IP-Adresse, die den eingehenden Netzwerkdatenverkehr für den Lastenausgleich empfängt
  - Back-End-Pool, an den die Front-End-IP-Adresse den Netzwerkdatenverkehr sendet, für den ein Lastenausgleich durchgeführt wurde
  - Back-End-Pool für ausgehende Verbindungen 
  - Integritätstest zum Ermitteln der Integrität der Back-End-VM-Instanzen
  - Eingangsregel des Lastenausgleichs, mit der definiert wird, wie der Datenverkehr auf die virtuellen Computer verteilt werden soll
  - Ausgangsregel des Lastenausgleichs, mit der definiert wird, wie der von den virtuellen Computern ausgehende Datenverkehr verteilt werden soll

### <a name="create-load-balancer"></a>Erstellen oder Aktualisieren eines Lastenausgleichs

Erstellen Sie mit [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) eine Load Balancer-Instanz mit der Eingangs-IP-Adresse mit dem Namen *lb*, die eine IP-Adresskonfiguration des Eingangs-Front-Ends und einen Back-End-Pool *bepoolinbound* enthält (mit Zuordnung zur öffentlichen IP-Adresse *mypublicipinbound*, die Sie im vorherigen Schritt erstellt haben).

```azurecli-interactive
  az network lb create \
    --resource-group myresourcegroupoutbound \
    --name lb \
    --sku standard \
    --backend-pool-name bepoolinbound \
    --frontend-ip-name myfrontendinbound \
    --location eastus2 \
    --public-ip-address mypublicipinbound   
  ```

### <a name="create-outbound-pool"></a>Erstellen eines ausgehenden Pools

Erstellen Sie einen zusätzlichen Back-End-Adresspool, um die ausgehenden Verbindungen für einen Pool von VMs mit [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) mit dem Namen *bepooloutbound* zu definieren.  Die Erstellung eines separaten ausgehenden Pools bietet maximale Flexibilität, aber Sie können diesen Schritt auslassen und auch nur *bepoolinbound* für den eingehenden Datenverkehr verwenden.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name bepooloutbound
```

### <a name="create-outbound-frontend-ip"></a>Erstellen einer IP-Adresse für das Ausgangs-Front-End
Erstellen Sie mit [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) die IP-Konfiguration für das Ausgangs-Front-End der Load Balancer-Instanz. Diese IP-Konfiguration des Ausgangs-Front-Ends wird *myfrontendoutbound* genannt und der öffentlichen IP-Adresse *mypublicipoutbound* zugeordnet.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myresourcegroupoutbound \
    --name myfrontendoutbound \
    --lb-name lb \
    --public-ip-address mypublicipoutbound 
  ```

### <a name="create-health-probe"></a>Erstellen eines Integritätstests

Ein Integritätstest überprüft alle VM-Instanzen, um sicherzustellen, dass sie Netzwerkdatenverkehr senden können. VM-Instanzen mit Fehlern beim Test werden aus dem Load Balancer entfernt, bis sie wieder online geschaltet werden und beim Test überprüft wurde, dass sie fehlerfrei sind. Erstellen Sie mit [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) einen Integritätstest zur Überwachung der Integrität von virtuellen Computern. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name http \
    --protocol http \
    --port 80 \
    --path /  
```

### <a name="create-load-balancing-rule"></a>Erstellen einer Lastenausgleichsregel

Mit einer Lastenausgleichsregel werden die Front-End-IP-Konfiguration für den eingehenden Datenverkehr und der Back-End-Pool zum Empfangen des Datenverkehrs zusammen mit dem erforderlichen Quell- und Zielport definiert. Erstellen Sie mit [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) eine Lastenausgleichsregel mit dem Namen *myinboundlbrule*, die an Port 80 des Front-End-Pools *myfrontendinbound* lauscht und den Netzwerkdatenverkehr nach erfolgtem Lastenausgleich an den Back-End-Adresspool *bepool* sendet, wobei ebenfalls der Port 80 verwendet wird. 

>[!NOTE]
>Diese Lastenausgleichsregel deaktiviert die automatische Ausgangs-(S)NAT mit dem Parameter „--disable-outbound-snat“. Die Ausgangs-NAT wird nur von der Ausgangsregel bereitgestellt.

```azurecli-interactive
az network lb rule create \
--resource-group myresourcegroupoutbound \
--lb-name lb \
--name inboundlbrule \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--probe http \
--frontend-ip-name myfrontendinbound \
--backend-pool-name bepoolinbound \
--disable-outbound-snat
```

### <a name="create-outbound-rule"></a>Erstellen einer Ausgangsregel

Eine Ausgangsregel definiert die öffentliche Front-End-IP-Adresse, dargestellt durch das Front-End *myfrontendoutbound*, das für den gesamten ausgehenden NAT-Datenverkehr verwendet wird, sowie den Back-End-Pool, für den diese Regel gilt.  Erstellen Sie eine Ausgangsregel *myoutboundrule* für die Ausgangsnetzwerkübersetzung aller virtuellen Computer (NIC-IP-Konfigurationen) im Back-End-Pool *bepool*.  Durch den folgenden Befehl wird außerdem das Leerlaufzeitlimit für ausgehenden Datenverkehr von 4 in 15 Minuten geändert, und es werden 10000 SNAT-Ports anstelle von 1024 zugeordnet.  Weitere Informationen finden Sie unter [Ausgangsregeln](https://aka.ms/lboutboundrules).

```azurecli-interactive
az network lb outbound-rule create \
 --resource-group myresourcegroupoutbound \
 --lb-name lb \
 --name outboundrule \
 --frontend-ip-configs myfrontendoutbound \
 --protocol All \
 --idle-timeout 15 \
 --outbound-ports 10000 \
 --address-pool bepooloutbound
```

Wenn Sie keinen separaten ausgehenden Pool verwenden möchten, können Sie das Argument des Adresspools im vorhergehenden Befehl ändern, um stattdessen *bepoolinbound* anzugeben.  Es wird empfohlen, separate Pools zu verwenden, um die Flexibilität und Lesbarkeit der resultierenden Konfiguration sicherzustellen.

Jetzt können Sie mit dem Hinzufügen Ihrer virtuellen Computer zum Back-End-Pool *bepoolinbound* __und__ *bepooloutbound* fortfahren, indem Sie die IP-Konfiguration der entsprechenden NIC-Ressourcen mit [az network nic ip-config address-pool add](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) aktualisieren.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Mit dem Befehl [az group delete](/cli/azure/group#az-group-delete) können Sie die Ressourcengruppe, den Lastenausgleich und alle dazugehörigen Ressourcen entfernen, wenn Sie sie nicht mehr benötigen.

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie eine Load Balancer Standard-Instanz erstellt und Regeln für den beim Lastenausgleich eingehenden Datenverkehr konfiguriert. Zudem haben Sie einen Integritätstest für die virtuellen Computer im Back-End-Pool erstellt. Weitere Informationen zu Azure Load Balancer finden Sie in den Tutorials zu Azure Load Balancer.

> [!div class="nextstepaction"]
> [Azure Load Balancer-Tutorials](tutorial-load-balancer-standard-public-zone-redundant-portal.md)

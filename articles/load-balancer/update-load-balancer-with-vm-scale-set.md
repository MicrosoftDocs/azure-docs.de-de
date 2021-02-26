---
title: Aktualisieren oder Löschen einer vorhandenen, von einer VM-Skalierungsgruppe verwendeten Load Balancer-Instanz
titleSuffix: Update or delete an existing load balancer used by virtual machine scale sets
description: In diesem Anleitungsartikel beginnen Sie mit Azure Load Balancer Standard und VM-Skalierungsgruppen.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/30/2020
ms.author: irenehua
ms.openlocfilehash: 0c491275f793ce2cd5e830ca6a3014dc45d6d509
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594534"
---
# <a name="update-or-delete-a-load-balancer-used-by-virtual-machine-scale-sets"></a>Aktualisieren oder Löschen einer von einer VM-Skalierungsgruppe verwendeten Load Balancer-Instanz

Wenn Sie mit VM-Skalierungsgruppen und einer Azure Load Balancer-Instanz arbeiten, haben Sie folgende Möglichkeiten:

- Hinzufügen, Aktualisieren und Löschen von Regeln
- Hinzufügen von Konfigurationen
- Löschen der Load Balancer-Instanz

## <a name="set-up-a-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>Einrichten einer Load Balancer-Instanz zum Aufskalieren von VM-Skalierungsgruppen

Stellen Sie sicher, dass für die Azure Load Balancer-Instanz ein [NAT-Pool für eingehenden Datenverkehr](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest) eingerichtet wurde und die VM-Skalierungsgruppe im Back-End-Pool der Load Balancer-Instanz platziert ist. Azure Load Balancer erstellt automatisch neue NAT-Regeln für eingehenden Datenverkehr im NAT-Pool für eingehenden Datenverkehr, wenn der VM-Skalierungsgruppe neue VM-Instanzen hinzugefügt werden.

So überprüfen Sie, ob der eingehende NAT-Pool ordnungsgemäß eingerichtet wurde:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Menü die Option **Alle Ressourcen** aus. Wählen Sie anschließend in der Ressourcenliste **MyLoadBalancer** aus.
1. Klicken Sie unter **Einstellungen** auf **NAT-Regeln für eingehenden Datenverkehr**. Wenn im rechten Bereich eine Liste der Regeln angezeigt wird, die für jede einzelne Instanz in der VM-Skalierungsgruppe erstellt wurden, können Sie jetzt jederzeit einen Aufskalierungsvorgang durchführen.

## <a name="add-inbound-nat-rules"></a>Hinzufügen von NAT-Regeln für eingehenden Datenverkehr

Einzelne NAT-Regeln für eingehenden Datenverkehr können nicht hinzugefügt werden. Sie können jedoch mehrere NAT-Regeln für eingehenden Datenverkehr mit definiertem Front-End-Portbereich und Back-End-Port für alle Instanzen in der VM-Skalierungsgruppe hinzufügen.

Erstellen Sie zunächst einen NAT-Pool für eingehenden Datenverkehr in der Load Balancer-Instanz, um mehrere NAT-Regeln für eingehenden Datenverkehr für die VM-Skalierungsgruppen hinzuzufügen. Verweisen Sie dann vom Netzwerkprofil der VM-Skalierungsgruppe auf den NAT-Pool für eingehenden Datenverkehr. Ein vollständiges Beispiel für die Verwendung der CLI wird angezeigt.

Der neue NAT-Pool für eingehenden Datenverkehr darf keinen überlappenden Front-End-Portbereich mit vorhandenen NAT-Pools für eingehenden Datenverkehr aufweisen. Verwenden Sie den folgenden [CLI-Befehl](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest#az_network_lb_inbound_nat_pool_list), um vorhandene eingerichtete NAT-Pools für eingehenden Datenverkehr anzuzeigen:
  
```azurecli-interactive
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool 
          --protocol Tcp 
          --frontend-port-range-start 80 
          --frontend-port-range-end 89 
          --backend-port 80 
          --frontend-ip-name MyFrontendIp
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
            
  az vmss update-instances
          -–instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
```
## <a name="update-inbound-nat-rules"></a>Aktualisieren von NAT-Regeln für eingehenden Datenverkehr

Einzelne NAT-Regeln für eingehenden Datenverkehr können nicht aktualisiert werden. Sie können jedoch mehrere NAT-Regeln für eingehenden Datenverkehr mit einem definierten Front-End-Portbereich und einem Back-End-Port für alle Instanzen in der VM-Skalierungsgruppe aktualisieren.

Aktualisieren Sie den NAT-Pool für eingehenden Datenverkehr in der Load Balancer-Instanz, um mehrere NAT-Regeln für eingehenden Datenverkehr für die VM-Skalierungsgruppen zu aktualisieren.
    
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="delete-inbound-nat-rules"></a>Löschen von NAT-Regeln für eingehenden Datenverkehr

Einzelne NAT-Regeln für eingehenden Datenverkehr können nicht gelöscht werden, aber Sie können die gesamte Gruppe der NAT-Regeln für eingehenden Datenverkehr löschen.

Entfernen Sie zunächst den NAT-Pool aus der Skalierungsgruppe, um die gesamte Gruppe der von der Skalierungsgruppe verwendeten NAT-Regeln für eingehenden Datenverkehr zu löschen. Ein vollständiges Beispiel für die Verwendung der CLI finden Sie hier:
    
```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
     az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
    az network lb inbound-nat-pool delete
       --resource-group MyResourceGroup
       -–lb-name MyLoadBalancer
       --name MyNatPool
```

## <a name="add-multiple-ip-configurations"></a>Hinzufügen mehrerer IP-Konfigurationen

So fügen Sie mehrere IP-Konfigurationen hinzu:

1. Wählen Sie im linken Menü die Option **Alle Ressourcen** aus. Wählen Sie anschließend in der Ressourcenliste **MyLoadBalancer** aus.
1. Klicken Sie unter **Einstellungen** auf **Front-End-IP-Konfiguration**. Wählen Sie anschließend **Hinzufügen**.
1. Geben Sie auf der Seite **Front-End-IP-Adresse hinzufügen** die Werte ein, und klicken Sie auf **OK**.
1. Führen Sie [Schritt 5](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) und [Schritt 6](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) in diesem Tutorial aus, wenn neue Lastenausgleichsregeln erforderlich sind.
1. Erstellen Sie bei Bedarf unter Verwendung der neu erstellten Front-End-IP-Konfigurationen neue NAT-Regeln für eingehenden Datenverkehr. Ein Beispiel finden Sie im vorherigen Abschnitt.

## <a name="delete-the-front-end-ip-configuration-used-by-the-virtual-machine-scale-set"></a>Löschen der von der VM-Skalierungsgruppe verwendeten Front-End-IP-Konfiguration

So löschen Sie die von der Skalierungsgruppe verwendete Front-End-IP-Konfiguration:

 1. Löschen Sie zuerst den NAT-Pool für eingehenden Datenverkehr (Gruppe der NAT-Regeln für eingehenden Datenverkehr), der auf die Front-End-IP-Konfiguration verweist. Anweisungen zum Löschen der Regeln für eingehenden Datenverkehr finden Sie im vorherigen Abschnitt.
 1. Löschen Sie die Lastenausgleichsregel, die auf die Front-End-IP-Konfiguration verweist.
 1. Löschen Sie die Front-End-IP-Konfiguration.

## <a name="delete-a-load-balancer-used-by-a-virtual-machine-scale-set"></a>Löschen einer von einer VM-Skalierungsgruppe verwendeten Load Balancer-Instanz

So löschen Sie die von der Skalierungsgruppe verwendete Front-End-IP-Konfiguration:

 1. Löschen Sie zuerst den NAT-Pool für eingehenden Datenverkehr (Gruppe der NAT-Regeln für eingehenden Datenverkehr), der auf die Front-End-IP-Konfiguration verweist. Anweisungen zum Löschen der Regeln für eingehenden Datenverkehr finden Sie im vorherigen Abschnitt.
 1. Löschen Sie die Lastenausgleichsregel, die auf den Back-End-Pool verweist, der die VM-Skalierungsgruppe enthält.
 1. Entfernen Sie den `loadBalancerBackendAddressPool`-Verweis aus dem Netzwerkprofil der VM-Skalierungsgruppe.
 
 Ein vollständiges Beispiel für die Verwendung der CLI finden Sie hier:

```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
       --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools
    az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
```
Löschen Sie zum Schluss die Load Balancer-Instanz.
 
## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Load Balancer und VM-Skalierungsgruppen finden Sie in den Artikeln zu den Konzepten.

> [Azure Load Balancer mit Azure-VM-Skalierungsgruppen](load-balancer-standard-virtual-machine-scale-sets.md)
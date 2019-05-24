---
title: Includedatei
description: Includedatei
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: cd686e1bf62bbd7f37f61ced767e92918edf919c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116914"
---
## <a name="how-to-create-a-classic-vnet-using-azure-cli"></a>Erstellen eines klassischen VNet über die Azure-Befehlszeilenschnittstelle
Sie können die Azure-Befehlszeilenschnittstelle verwenden, um Ihre Azure-Ressourcen über die Eingabeaufforderung eines beliebigen Computers mit Windows, Linux oder OSX zu verwalten.

1. Wenn Sie die Azure-Befehlszeilenschnittstelle noch nie verwendet haben, ziehen Sie [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](../articles/cli-install-nodejs.md) zurate, und folgen Sie den Anweisungen bis zu dem Punkt, an dem Sie Ihr Azure-Konto und Ihr Abonnement auswählen.
2. Führen Sie den Befehl **azure network vnet create** aus, um ein VNET und ein Subnetz zu erstellen:
   
            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
   
    Erwartete Ausgabe:
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK
   
   * **--vnet**. Name des zu erstellenden VNet. In diesem Szenario *TestVNet*.
   * **-e (oder --address-space)**. VNet-Adressraum. In diesem Szenario *192.168.0.0*.
   * **-i (oder --cidr)**. Netzwerkmaske im CIDR-Format. In diesem Szenario *16*.
   * **-n (oder --subnet-name**). Name des ersten Subnetzes. In diesem Szenario *FrontEnd*.
   * **-p (oder --subnet-start-ip)**. IP-Startadresse für das Subnetz oder Subnetzadressraum. In diesem Szenario *192.168.1.0*.
   * **-r (oder --subnet-cidr)**. Netzwerkmaske im CIDR-Format für das Subnetz. In diesem Szenario *24*.
   * **-l (oder --location)**. Azure-Region, in der das VNET erstellt wird. In diesem Szenario *USA, Mitte*.
3. Führen Sie den Befehl **azure network vnet subnet create** aus, um ein Subnetz zu erstellen:
   
            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
   
    Erwartete Ausgabe dieses Befehls:
   
            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK
   
   * **-t (oder --vnet-name)**. Name des VNet, in dem das Subnetz erstellt wird. In diesem Szenario *TestVNet*.
   * **-n (oder --name)**. Name des neuen Subnetzes. In diesem Szenario *BackEnd*.
   * **-a (oder --address-prefix)**. Subnetz-CIDR-Block. In diesem Szenario *192.168.2.0/24*.
4. Führen Sie den Befehl **azure network vnet show** aus, um die Eigenschaften des neuen VNET anzuzeigen:
   
            azure network vnet show
   
    Erwartete Ausgabe dieses Befehls:
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK


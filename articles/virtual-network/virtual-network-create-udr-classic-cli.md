---
title: Steuern des Routings in einem virtuellen Azure-Netzwerk – CLI – klassisch | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie das Routing in VNets mithilfe der Azure-Befehlszeilenschnittstelle im klassischen Bereitstellungsmodell steuern.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: ca2b4638-8777-4d30-b972-eb790a7c804f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: e1b8bb3544a08b60564ceb5bd7e1666214059e09
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60743920"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Steuern des Routings und Verwenden virtueller Geräte (klassisch) mithilfe der Azure-Befehlszeilenschnittstelle

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [Azure-Befehlszeilenschnittstelle](tutorial-create-route-table-cli.md)
> * [PowerShell (klassisch)](virtual-network-create-udr-classic-ps.md)
> * [CLI (klassisch)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Dieser Artikel gilt für das klassische Bereitstellungsmodell. Sie können auch [im Ressourcen-Manager-Bereitstellungsmodell das Routing steuern und virtuelle Geräte verwenden](tutorial-create-route-table-cli.md).

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Die folgenden Beispielbefehle für die Azure-Befehlszeilenschnittstelle setzen voraus, dass bereits eine einfache Umgebung erstellt wurde, die auf dem obigen Szenario basiert. Wenn Sie die in diesem Dokument aufgeführten Befehle ohne Veränderungen ausführen möchten, erstellen Sie zunächst die unter [Erstellen eines VNets (klassisch) mithilfe der Azure-Befehlszeilenschnittstelle](virtual-networks-create-vnet-classic-cli.md)beschriebene Umgebung.

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Erstellen der benutzerdefinierten Route für das Front-End-Subnetz
Führen Sie zum Erstellen der Routingtabelle und der für das Front-End-Subnetz erforderlichen Route anhand des oben beschriebenen Szenarios die folgenden Schritte aus.

1. Führen Sie den folgenden Befehl aus, um in den klassischen Modus zu wechseln:

    ```azurecli
    azure config mode asm
    ```

    Ausgabe:

        info:    New mode is asm

2. Führen Sie den folgenden Befehl aus, um eine Routingtabelle für das Front-End-Subnetz zu erstellen:

    ```azurecli
    azure network route-table create -n UDR-FrontEnd -l uswest
    ```
   
    Ausgabe:
   
        info:    Executing command network route-table create
        info:    Creating route table "UDR-FrontEnd"
        info:    Getting route table "UDR-FrontEnd"
        data:    Name                            : UDR-FrontEnd
        data:    Location                        : West US
        info:    network route-table create command OK
   
    Parameter:
   
   * **-l (oder --location)** . Azure-Region, in der die neue NSG erstellt wird. In diesem Szenario *westus*.
   * **-n (oder --name)** . Name der neuen NSG. In diesem Szenario *NSG-FrontEnd*.
3. Führen Sie den folgenden Befehl aus, um in der Routingtabelle eine Route zu erstellen, die sämtlichen an das Back-End-Subnetz (192.168.2.0/24) gerichteten Datenverkehr an den virtuellen Computer **FW1** (192.168.0.4) umleitet:

    ```azurecli
    azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

    Ausgabe:
   
        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK
   
    Parameter:
   
   * **-r (oder --route-table-name)** . Der Name der Routingtabelle, der die Route hinzugefügt wird. In diesem Szenario *UDR-FrontEnd*.
   * **-a (oder --address-prefix)** . Das Adresspräfix für das Zielsubnetz der Pakete. In diesem Szenario *192.168.2.0/24*.
   * **-t (oder --next-hop-type)** . Der Typ des Zielobjekts für den Datenverkehr. Mögliche Werte sind *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet* oder *None*.
   * **-p (oder --next-hop-ip-address**). Die IP-Adresse für den nächsten Hop. In diesem Szenario *192.168.0.4*.
4. Führen Sie den Befehl aus, um die oben erstellte Routingtabelle dem Subnetz **FrontEnd** zuzuordnen:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd
    ```
   
    Ausgabe:
   
        info:    Executing command network vnet subnet route-table add
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        data:    Route table name                : UDR-FrontEnd
        data:      Location                      : West US
        data:      Routes:
        info:    network vnet subnet route-table add command OK    
   
    Parameter:
   
   * **-t (oder --vnet-name)** . Name des VNets mit dem Subnetz. In diesem Szenario *TestVNet*.
   * **-n (oder --subnet-name**). Der Name des Subnetzes, dem die Routingtabelle hinzugefügt wird. In diesem Szenario *FrontEnd*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Erstellen der benutzerdefinierten Route für das Back-End-Subnetz
Führen Sie zum Erstellen der Routingtabelle und der für das Back-End-Subnetz erforderlichen Route anhand des beschriebenen Szenarios die folgenden Schritte aus:

1. Führen Sie den folgenden Befehl aus, um eine Routingtabelle für das Back-End-Subnetz zu erstellen:

    ```azurecli
    azure network route-table create -n UDR-BackEnd -l uswest
    ```

2. Führen Sie den folgenden Befehl aus, um in der Routingtabelle eine Route zu erstellen, die sämtlichen an das Front-End-Subnetz (192.168.1.0/24) gerichteten Datenverkehr an den virtuellen Computer **FW1** (192.168.0.4) umleitet:

    ```azurecli
    azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

3. Führen Sie den Befehl aus, um die oben erstellte Routingtabelle dem Subnetz **BackEnd** zuzuordnen:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd
    ```


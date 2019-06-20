---
title: Konfigurieren von privaten IP-Adressen für virtuelle Computer (klassisch) – klassische Azure CLI
titlesuffix: Azure Virtual Network
description: Erfahren Sie, wie Sie mithilfe der klassischen Azure-Befehlszeilenschnittstelle (CLI) private IP-Adressen für virtuelle Computer (klassisch) konfigurieren.
services: virtual-network
documentationcenter: na
author: genlin
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 2f71dc2bcd5463f81ae286bbe3099124eb3fa539
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62107976"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-classic-cli"></a>Konfigurieren von privaten IP-Adressen für einen virtuellen Computer (klassisch) mithilfe der klassischen Azure CLI

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Dieser Artikel gilt für das klassische Bereitstellungsmodell. Sie können [eine statische private IP-Adresse auch im Ressourcen-Manager-Bereitstellungsmodell verwalten](virtual-networks-static-private-ip-arm-cli.md).

Die folgenden Beispielbefehle für die klassische Azure CLI setzen voraus, dass bereits eine einfache Umgebung erstellt wurde. Wenn Sie die in diesem Dokument aufgeführten Befehle ohne Veränderungen ausführen möchten, erstellen Sie zunächst eine Testumgebung, wie unter [Erstellen eines VNets](virtual-networks-create-vnet-classic-cli.md)beschrieben.

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Angeben einer statischen privaten IP-Adresse beim Erstellen eines virtuellen Computers
Erstellen Sie basierend auf dem oben beschriebenen Szenario in einem neuen Clouddienst mit dem Namen *TestService* einen neuen virtuellen Computer mit dem Namen *DNS01*. Gehen Sie dabei wie folgt vor:

1. Wenn Sie die Azure-Befehlszeilenschnittstelle noch nie verwendet haben, ziehen Sie [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-cli-version-1.0) zurate, und folgen Sie den Anweisungen bis zu dem Punkt, an dem Sie Ihr Azure-Konto und Ihr Abonnement auswählen.
2. Führen Sie den Befehl **azure service create** aus, um den Clouddienst zu erstellen.
   
        azure service create TestService --location uscentral
   
    Erwartete Ausgabe:
   
        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
3. Führen Sie den Befehl **azure create vm** aus, um den virtuellen Computer zu erstellen. Beachten Sie, dass auch ein Wert für die statische private IP-Adresse angezeigt wird. In der nach der Ausgabe angezeigten Liste werden die verwendeten Parameter erläutert.
   
        azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd
   
    Erwartete Ausgabe:
   
        info:    Executing command vm create
        warn:    --vm-size has not been specified. Defaulting to "Small".
        info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
        info:    Looking up virtual network
        info:    Looking up cloud service
        warn:    --location option will be ignored
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Retrieving storage accounts
        info:    Creating VM
        info:    OK
        info:    vm create command OK
   
   * **-l (oder --location)** . Azure-Region aus, in der der virtuelle Computer erstellt wird. In diesem Szenario *centralus*.
   * **-n (oder --vm-name)** . Name des zu erstellenden virtuellen Computers.
   * **-w (oder --virtual-network-name)** . Name des VNets, in dem der virtuelle Computer erstellt wird. 
   * **-S (oder --static-ip)** . Statische private IP-Adresse für den virtuellen Computer.
   * **TestService**. Name des Clouddiensts, in dem der virtuelle Computer erstellt wird.
   * **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2**. Image, das zum Erstellen des virtuellen Computers verwendet wird.
   * **adminuser**. Lokaler Administrator für den virtuellen Windows-Computer.
   * <strong>AdminP@ssw0rd</strong>. Kennwort des lokalen Administrators für den virtuellen Windows-Computer.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Abrufen der Informationen zur statischen privaten IP-Adresse für einen virtuellen Computer
Um die Angaben zur statischen privaten IP-Adresse des mit dem Skript erstellten virtuellen Computers anzuzeigen, führen Sie an der Azure-Befehlszeilenschnittstelle folgenden Befehl aus, und überprüfen Sie die Werte für *Network StaticIP*:

    azure vm static-ip show DNS01

Erwartete Ausgabe:

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Entfernen einer statischen privaten IP-Adresse von einem virtuellen Computer
Führen Sie an der Azure-Befehlszeilenschnittstelle folgenden Befehl aus, um die statische private IP-Adresse zu entfernen, die dem virtuellen Computer im obigen Skript hinzugefügt wurde:

    azure vm static-ip remove DNS01

Erwartete Ausgabe:

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## <a name="how-to-add-a-static-private-ip-to-an-existing-vm"></a>Hinzufügen einer statischen privaten IP-Adresse zu einem vorhandenen virtuellen Computer
Führen Sie folgenden Befehl aus, um dem virtuellen Computer, der mit dem obigen Skript erstellt wurde, eine statische private IP-Adresse hinzuzufügen:

    azure vm static-ip set DNS01 192.168.1.101

Erwartete Ausgabe:

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="set-ip-addresses-within-the-operating-system"></a>Festlegen von IP-Adressen innerhalb des Betriebssystems

Es wird davon abgeraten, die private IP-Adresse, die dem virtuellen Azure-Computer innerhalb des Betriebssystems einer VM zugewiesen ist, statisch zuzuweisen, sofern dies nicht erforderlich ist. Wenn Sie die private IP-Adresse innerhalb des Betriebssystems manuell festlegen, sollten Sie sicherstellen, dass es sich um dieselbe Adresse wie die private IP-Adresse handelt, die dem virtuellen Azure-Computer zugewiesen ist. Andernfalls kann die Konnektivität mit dem virtuellen Computer verloren gehen. Weisen Sie die öffentliche IP-Adresse, die einem virtuellen Computer innerhalb seines Betriebssystems zugewiesen ist, nicht manuell zu.

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über [reservierte öffentliche IP-Adressen](virtual-networks-reserved-public-ip.md) .
* Erfahren Sie mehr über [öffentliche IP-Adressen auf Instanzebene (ILPIP)](virtual-networks-instance-level-public-ip.md) .
* Lesen Sie die Informationen zu [REST-APIs für reservierte IP-Adressen](https://msdn.microsoft.com/library/azure/dn722420.aspx).
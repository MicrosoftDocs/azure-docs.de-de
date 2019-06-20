---
title: Erstellen einer VM (klassisch) mit mehreren Netzwerkkarten – klassische Azure CLI | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine VM (klassisch) mit mehreren Netzwerkkarten mithilfe der klassischen Azure-Befehlszeilenschnittstelle (CLI) erstellen.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: b436e41e-866c-439f-a7c7-7b4b041725ef
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1e47b1e548516960c6aab3c48d64255370c94a77
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60743318"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-the-azure-classic-cli"></a>Erstellen einer VM (klassisch) mit mehreren Netzwerkkarten mithilfe der klassischen Azure CLI

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Sie können virtuelle Computer (VMs) in Azure erstellen und jedem virtuellen Computer mehrere Netzwerkkarten (NICs) zuordnen. Mehrere NICs ermöglichen die Trennung von Datenverkehrstypen für alle NICs. Beispiel: Eine NIC kommuniziert mit dem Internet, während eine andere nur mit internen Ressourcen kommuniziert, für die keine Internetverbindung besteht. Die Möglichkeit, Netzwerkdatenverkehr auf mehreren NICs zu trennen, ist für zahlreiche virtuelle Netzwerkgeräte erforderlich, beispielsweise für Lösungen zur Anwendungsbereitstellung und WAN-Optimierung.

> [!IMPORTANT]
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen:  [das Resource Manager-Modell und das klassische Bereitstellungsmodell](../resource-manager-deployment-model.md). Dieser Artikel befasst sich mit der Verwendung des klassischen Bereitstellungsmodells. Microsoft empfiehlt für die meisten neuen Bereitstellungen die Verwendung des Ressourcen-Manager-Modells. Erfahren Sie, wie Sie diese Schritte mit dem [Resource Manager-Bereitstellungsmodell ausführen](../virtual-machines/linux/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

In den folgenden Schritten verwenden Sie eine Ressourcengruppe mit dem Namen *IaaSStory* für die Webserver und eine Ressourcengruppe mit dem Namen *IaaSStory-BackEnd* für die DB-Server.

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie die DB-Server erstellen können, müssen Sie die Ressourcengruppe *IaaSStory* mit den erforderlichen Ressourcen für dieses Szenario erstellen. Führen Sie die folgenden Schritte aus, um diese Ressourcen zu erstellen: Erstellen Sie ein virtuelles Netzwerk, indem Sie die Schritte im Artikel [Erstellen eines virtuellen Netzwerks](virtual-networks-create-vnet-classic-cli.md) befolgen.

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Bereitstellen der Back-End-VMs
Die Back-End-VMs sind auf die Erstellung der folgenden Ressourcen angewiesen:

* **Speicherkonto für Datenträger.** Für eine bessere Leistung verwenden die Datenträger auf den Datenbankservern Solid State Drive (SSD)-Technik. Dafür ist ein Storage Premium-Konto erforderlich. Achten Sie darauf, dass der Azure-Speicherort für die Bereitstellung Storage Premium unterstützt.
* **NICs**. Jeder virtuelle Computer hat zwei Netzwerkkarten, eine für den Datenbankzugriff und eine für die Verwaltung.
* **Verfügbarkeitsgruppe**. Alle Datenbankserver werden einer einzigen Verfügbarkeitsgruppe hinzugefügt, damit sichergestellt ist, dass mindestens ein virtueller Computer während der Wartung ausgeführt wird.

### <a name="step-1---start-your-script"></a>Schritt 1: Starten des Skripts
Sie können das verwendete Bash-Skript ungekürzt [hier](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh)herunterladen. Schließen Sie die nachstehenden Schritte ab, um das Skript an Ihre Arbeitsumgebung anzupassen.

1. Ändern Sie die Werte der nachstehenden Variablen basierend auf der im obigen Abschnitt [Voraussetzungen](#prerequisites) bereitgestellten Ressourcengruppe.

    ```azurecli
    location="useast2"
    vnetName="WTestVNet"
    backendSubnetName="BackEnd"
    ```
2. Ändern Sie die Werte der nachstehenden Variablen basierend auf den Werten, die Sie für die Back-End-Bereitstellung verwenden möchten.

    ```azurecli
    backendCSName="IaaSStory-Backend"
    prmStorageAccountName="iaasstoryprmstorage"
    image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
    avSetName="ASDB"
    vmSize="Standard_DS3"
    diskSize=127
    vmNamePrefix="DB"
    osDiskName="osdiskdb"
    dataDiskPrefix="db"
    dataDiskName="datadisk"
    ipAddressPrefix="192.168.2."
    username='adminuser'
    password='adminP@ssw0rd'
    numberOfVMs=2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Schritt 2: Erstellen der erforderlichen Ressourcen für die virtuellen Computer
1. Erstellen Sie einen neuen Clouddienst für alle Back-End-VMs. Beachten Sie, dass die `$backendCSName`-Variable für den Ressourcengruppennamen und `$location` für die Azure-Region verwendet wird

    ```azurecli
    azure service create --serviceName $backendCSName \
        --location $location
    ```

2. Erstellen Sie ein Premium-Speicherkonto für das Betriebssystem und die Datenträger, die von den virtuellen Computern verwendet werden sollen.

    ```azurecli
    azure storage account create $prmStorageAccountName \
        --location $location \
        --type PLRS
    ```

### <a name="step-3---create-vms-with-multiple-nics"></a>Schritt 3: Erstellen von virtuellen Computern mit mehreren Netzwerkschnittstellenkarten
1. Erstellen Sie mithilfe einer Schleife mehrere virtuelle Computer auf Grundlage der `numberOfVMs` -Variablen.

    ```azurecli
    for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
    do
    ```

2. Geben Sie für jeden virtuellen Computer den Namen und die IP-Adresse der beiden Netzwerkkarten an.

    ```azurecli
    nic1Name=$vmNamePrefix$suffixNumber-DA
    x=$((suffixNumber+3))
    ipAddress1=$ipAddressPrefix$x

    nic2Name=$vmNamePrefix$suffixNumber-RA
    x=$((suffixNumber+53))
    ipAddress2=$ipAddressPrefix$x
    ```

3. Erstellen Sie den virtuellen Computer. Beachten Sie, dass der `--nic-config`-Parameter verwendet wird, der eine Liste aller Netzwerkkarten mit Namen, Subnetz und IP-Adresse enthält.

    ```azurecli
    azure vm create $backendCSName $image $username $password \
        --connect $backendCSName \
        --vm-name $vmNamePrefix$suffixNumber \
        --vm-size $vmSize \
        --availability-set $avSetName \
        --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
        --virtual-network-name $vnetName \
        --subnet-names $backendSubnetName \
        --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::
    ```

4. Erstellen Sie für jeden virtuellen Computer zwei Datenträger.

    ```azurecli
    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
    done
    ```

### <a name="step-4---run-the-script"></a>Schritt 4: Ausführen des Skripts
Führen Sie das Skript aus, nachdem sie es heruntergeladen und angepasst haben, um die Back-End-VMs mit mehreren Netzwerkkarten zu erstellen.

1. Speichern Sie Ihr Skript, und führen Sie es im **Bash** -Terminal aus. Anfänglich wird die folgende Ausgabe angezeigt.

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name IaaSStory-Backend
        info:    service create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM

2. Nach einigen Minuten wird die Ausführung beendet, und Sie sehen den im Folgenden gezeigten Rest der Ausgabe.

        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM
        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Schritt 5: Konfigurieren des Routings im Betriebssystem des virtuellen Computers

Azure DHCP weist der ersten (primären) Netzwerkschnittstelle, die an den virtuellen Computer angefügt ist, ein Standardgateway zu. Azure weist zusätzlichen (sekundären) Netzwerkschnittstellen, die an einen virtuellen Computer angefügt sind, kein Standardgateway zu. Daher können Sie standardmäßig nicht mit Ressourcen außerhalb des Subnetzes kommunizieren, in dem sich eine sekundäre Netzwerkschnittstelle befindet. Sekundäre Netzwerkschnittstellen können jedoch mit Ressourcen außerhalb ihres Subnetzes kommunizieren. Informationen zum Konfigurieren des Routings für sekundäre Netzwerkschnittstellen finden Sie unter [Routing innerhalb des Betriebssystems eines virtuellen Computers mit mehreren Netzwerkschnittstellen](virtual-network-network-interface-vm.md).

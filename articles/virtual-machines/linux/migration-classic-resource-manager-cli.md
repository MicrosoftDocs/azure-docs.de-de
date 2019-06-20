---
title: Migrieren von VMs in den Resource Manager mit der Azure-CLI | Microsoft-Dokumentation
description: In diesem Artikel wird die plattformgestützte Migration von Ressourcen aus dem klassischen Bereitstellungsmodell zu Azure Resource Manager mithilfe der Azure-Befehlszeilenschnittstelle erläutert.
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d6f5a877-05b6-4127-a545-3f5bede4e479
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 34dad39e3784dd0bc73e3be108d6b31d4f479a1e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60543144"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Migrieren von IaaS-Ressourcen aus dem klassischen Bereitstellungsmodell zu Azure Resource Manager mithilfe der Azure-Befehlszeilenschnittstelle
Diese Schritte zeigen, wie Sie Befehle der Azure-Befehlszeilenschnittstelle zum Migrieren von IaaS-Ressourcen (Infrastructure as a Service) aus dem klassischen Bereitstellungsmodell in das Azure Resource Manager-Bereitstellungsmodell verwenden. Für diesen Artikel ist die [klassische Azure CLI](../../cli-install-nodejs.md) erforderlich. Da die Azure CLI nur für Azure Resource Manager-Ressourcen gilt, kann sie nicht für diese Migration verwendet werden.

> [!NOTE]
> Alle hier beschriebenen Vorgänge sind idempotent. Sollte ein Problem auftreten, das nicht auf ein nicht unterstütztes Feature oder auf einen Konfigurationsfehler zurückzuführen ist, wiederholen Sie den Vorbereitungs-, Abbruch- oder Commitvorgang. Die Plattform versucht dann erneut, die Aktion auszuführen.
> 
> 

<br>
Hier sehen Sie ein Flussdiagramm, das die Reihenfolge veranschaulicht, in der Schritte während einer Migration ausgeführt werden müssen.

![Screenshot that shows the migration steps](../windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>Schritt 1: Vorbereiten der Migration
Hier finden Sie einige bewährte Methoden, die wir empfehlen, wenn Sie eine Migration von IaaS-Ressourcen aus dem klassischen Bereitstellungsmodell zu Resource Manager in Erwägung ziehen:

* Sehen Sie sich die [Liste mit nicht unterstützten Konfigurationen und Features](../windows/migration-classic-resource-manager-overview.md) an. Verwenden Ihre virtuellen Computer nicht unterstützte Konfigurationen oder Features, empfiehlt es sich, zu warten, bis die Unterstützung für die entsprechenden Features/Konfigurationen angekündigt wird. Alternativ können Sie ggf. die betroffenen Features entfernen oder eine andere Konfiguration verwenden, um die Migration zu ermöglichen.
* Wenn Sie derzeit über automatisierte Skripts zum Bereitstellen Ihrer Infrastruktur und Anwendungen verfügen, versuchen Sie, mithilfe dieser Skripts für die Migration eine ähnliche Testeinrichtung zu erstellen. Alternativ dazu können Sie über das Azure-Portal Beispielumgebungen einrichten.

> [!IMPORTANT]
> Application Gateways werden für die Migration vom klassischen zum Resource Manager-Modell derzeit nicht unterstützt. Entfernen Sie zum Migrieren eines klassischen virtuellen Netzwerks mit einem Application Gateway das Gateway, bevor Sie einen Vorbereitungsvorgang zum Verschieben des Netzwerks ausführen. Stellen Sie nach Abschluss der Migration die Verbindung zum Gateway im Azure Resource Manager wieder her. 
>
>ExpressRoute-Gateways, die sich mit ExpressRoute-Verbindungen in einem anderen Abonnement verbinden, können nicht automatisch migriert werden. Entfernen Sie in solchen Fällen das ExpressRoute-Gateway, migrieren Sie das virtuelle Netzwerk, und erstellen Sie das Gateway neu. Weitere Informationen finden Sie unter [Migrieren von ExpressRoute-Verbindungen und zugeordneten virtuellen Netzwerken vom klassischen Bereitstellungsmodell zum Resource Manager-Bereitstellungsmodell](../../expressroute/expressroute-migration-classic-resource-manager.md).
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Schritt 2: Festlegen des Abonnements und Registrieren des Anbieters
Für Migrationsszenarien müssen Sie Ihre Umgebung sowohl für das klassische Bereitstellungsmodell als auch für Resource Manager einrichten. [Installieren Sie die Azure-Befehlszeilenschnittstelle](../../cli-install-nodejs.md), und [wählen Sie Ihr Abonnement aus](/cli/azure/authenticate-azure-cli).

Melden Sie sich bei Ihrem Konto an.

    azure login

Führen Sie zum Auswählen des Azure-Abonnements den folgenden Befehl aus.

    azure account set "<azure-subscription-name>"

> [!NOTE]
> Die Registrierung ist ein einmaliger Schritt, der jedoch einmal ausgeführt werden muss, bevor Sie versuchen, die Migration auszuführen. Ohne Registrierung wird die folgende Fehlermeldung angezeigt: 
> 
> *BadRequest: Das Abonnement ist nicht für die Migration registriert.* 
> 
> 

Registrieren Sie sich mithilfe des folgenden Befehls beim Migrationsressourcenanbieter. Beachten Sie, dass in einigen Fällen für diesen Befehl ein Timeout festgelegt ist. Die Registrierung wird jedoch erfolgreich durchgeführt.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Der Abschluss der Registrierung kann bis zu fünf Minuten dauern. Warten Sie daher etwas. Der Genehmigungsstatus kann mithilfe des folgenden Befehls geprüft werden. Stellen Sie sicher, dass der RegistrationState-Wert `Registered` lautet, bevor Sie fortfahren.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Wechseln Sie nun in den `asm`-Modus der Befehlszeilenschnittstelle.

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Schritt 3: Sicherstellen, dass Sie über genügend vCPUs in virtuellen Azure Resource Manager-Computern in der Azure-Region Ihrer aktuellen Bereitstellung oder Ihres VNET verfügen
Für diesen Schritt müssen in den `arm` -Modus wechseln. Führen Sie dazu den folgenden Befehl aus.

```
azure config mode arm
```

Mit dem folgenden CLI-Befehl können Sie die aktuelle Anzahl von vCPUs in Azure Resource Manager überprüfen. Weitere Informationen zu vCPU-Kontingenten finden Sie unter [Grenzwerte und der Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-azure-resource-manager).

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Sobald Sie diesen Schritt überprüft haben, können Sie zurück in den `asm` -Modus wechseln.

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Schritt 4: Option 1 – Migrieren von virtuellen Computern in einem Clouddienst
Rufen Sie mithilfe des folgenden Befehls die Liste mit den Clouddiensten auf, und wählen Sie anschließend den zu migrierenden Clouddienst aus. Beachten Sie: Falls sich die virtuellen Computer im Clouddienst in einem virtuellen Netzwerk befinden oder über Web-/Workerrollen verfügen, wird eine Fehlermeldung zurückgegeben.

    azure service list

Führen Sie den folgenden Befehl aus, um in der ausführlichen Ausgabe den Bereitstellungsnamen für den Clouddienst zu ermitteln. In den meisten Fällen entspricht der Bereitstellungsname dem Namen des Clouddiensts.

    azure service show <serviceName> -vv

Überprüfen Sie zuerst, ob Sie den Clouddienst mithilfe des folgenden Befehls migrieren können:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Bereiten Sie die virtuellen Computer des Clouddiensts auf die Migration vor. Dabei stehen Ihnen zwei Optionen zur Auswahl.

Wenn Sie die virtuellen Computer in ein von der Plattform erstelltes virtuelles Netzwerk migrieren möchten, verwenden Sie den folgenden Befehl.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Wenn Sie als Migrationsziel ein vorhandenes virtuelles Netzwerk im Resource Manager-Bereitstellungsmodell verwenden möchten, führen Sie den folgenden Befehl aus.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>

Nach der Vorbereitung können Sie sich in der ausführlichen Ausgabe über den Migrationsstatus der virtuellen Computer informieren und sich vergewissern, dass sich die virtuellen Computer im Status `Prepared` befinden.

    azure vm show <vmName> -vv

Überprüfen Sie die Konfiguration der vorbereiteten Ressourcen mithilfe der Befehlszeilenschnittstelle oder im Azure-Portal. Wenn Sie noch nicht für die Migration bereit sind und zum alten Zustand zurückkehren möchten, verwenden Sie den folgenden Befehl.

    azure service deployment abort-migration <serviceName> <deploymentName>

Wenn die vorbereitete Konfiguration in Ordnung ist, können Sie den Vorgang fortsetzen und mithilfe des folgenden Befehls ein Commit für die Ressourcen ausführen.

    azure service deployment commit-migration <serviceName> <deploymentName>



## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Schritt 4: Option 2 – Migrieren virtueller Computer in einem virtuellen Netzwerk
Wählen Sie das virtuelle Netzwerk aus, das Sie migrieren möchten. Beachten Sie: Falls das virtuelle Netzwerk Web-/Workerrollen oder virtuelle Computer mit nicht unterstützten Konfigurationen enthält, tritt ein Validierungsfehler auf.

Rufen Sie mithilfe des folgenden Befehls alle virtuellen Netzwerke im Abonnement ab.

    azure network vnet list

Die Ausgabe sieht in etwa wie folgt aus:

![Screenshot der Befehlszeile, in dem der gesamte Name des virtuellen Netzwerks hervorgehoben ist.](../media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

Im obigen Beispiel ist **virtualNetworkName** der vollständige Name **Group classicubuntu16 classicubuntu16**.

Überprüfen Sie zuerst mithilfe des folgenden Befehls, ob Sie das virtuelle Netzwerk migrieren können:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Bereiten Sie das gewünschte virtuelle Netzwerk mithilfe des folgenden Befehls für die Migration vor.

    azure network vnet prepare-migration <virtualNetworkName>

Überprüfen Sie die Konfiguration der vorbereiteten virtuellen Computer mithilfe der Befehlszeilenschnittstelle oder im Azure-Portal. Wenn Sie noch nicht für die Migration bereit sind und zum alten Zustand zurückkehren möchten, verwenden Sie den folgenden Befehl.

    azure network vnet abort-migration <virtualNetworkName>

Wenn die vorbereitete Konfiguration in Ordnung ist, können Sie den Vorgang fortsetzen und mithilfe des folgenden Befehls ein Commit für die Ressourcen ausführen.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>Schritt 5: Migrieren eines Speicherkontos
Sobald Sie mit der Migration der virtuellen Computer fertig sind, sollten Sie das Speicherkonto migrieren.

Bereiten Sie das Speicherkonto Netzwerk mithilfe des folgenden Befehls für die Migration vor.

    azure storage account prepare-migration <storageAccountName>

Überprüfen Sie die Konfiguration des vorbereiteten Speicherkontos mithilfe der Befehlszeilenschnittstelle oder im Azure-Portal. Wenn Sie noch nicht für die Migration bereit sind und zum alten Zustand zurückkehren möchten, verwenden Sie den folgenden Befehl.

    azure storage account abort-migration <storageAccountName>

Wenn die vorbereitete Konfiguration in Ordnung ist, können Sie den Vorgang fortsetzen und mithilfe des folgenden Befehls ein Commit für die Ressourcen ausführen.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über die plattformgestützte Migration von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ausführliche technische Informationen zur plattformgestützten Migration vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planen der Migration von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migrieren von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager mithilfe von PowerShell](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Communitytools zur Unterstützung beim Migrieren von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Überprüfen der häufigsten Fehler bei der Migration](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Antworten auf die am häufigsten gestellten Fragen zum Migrieren von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

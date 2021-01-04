---
title: 'Tutorial: Bereitstellen eines vSphere-Clusters in Azure'
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe von Azure VMware Solution einen vSphere-Cluster in Azure bereitstellen.
ms.topic: tutorial
ms.date: 11/19/2020
ms.openlocfilehash: 93937f8ca0918494810885f5cb45de571a6e1529
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966308"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud-in-azure"></a>Tutorial: Bereitstellen einer privaten Azure VMware Solution-Cloud in Azure

Azure VMware Solution ermöglicht das Bereitstellen eines vSphere-Clusters in Azure. Die Bereitstellung muss mindestens drei Hosts umfassen. Später können nacheinander weitere Host hinzugefügt werden. Pro Cluster sind bis zu 16 Hosts möglich. 

Da Sie Ihre private Cloud zunächst nicht mit Ihrer lokalen vCenter-Instanz verwalten können, sind zusätzliche Konfigurationsschritte erforderlich. Diese Verfahren und die damit zusammenhängenden Voraussetzungen werden in diesem Tutorial behandelt.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer privaten Cloud von Azure VMware Solution
> * Vergewissern, dass die private Cloud bereitgestellt wurde

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Geeignete Administratorrechte und Berechtigung zum Erstellen einer privaten Cloud.
- Vergewissern Sie sich, dass das Netzwerk ordnungsgemäß konfiguriert ist, wie unter [Netzwerkprüfliste für die Azure-VMware-Lösung (Azure VMware Solution, AVS)](tutorial-network-checklist.md) beschrieben.

## <a name="register-the-resource-provider"></a>Registrieren des Ressourcenanbieters

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="create-a-private-cloud"></a>Erstellen einer privaten Cloud

Sie können eine private Azure VMware Solution-Cloud über das [Azure-Portal](#azure-portal) oder mithilfe der [Azure-Befehlszeilenschnittstelle](#azure-cli) erstellen.

### <a name="azure-portal"></a>Azure-Portal

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Azure CLI

Zum Erstellen einer privaten Azure VMware Solution-Cloud können Sie anstelle des Azure-Portals auch die Azure CLI über Azure Cloud Shell verwenden.  Eine Liste der Befehle, die Sie mit Azure VMware Solution verwenden können, finden Sie [hier](/cli/azure/ext/vmware/vmware).

#### <a name="open-azure-cloud-shell"></a>Öffnen von Azure Cloud Shell

Wählen Sie rechts oben in einem Codeblock die Option **Jetzt testen** aus. Sie können Cloud Shell auch auf einer separaten Browserregisterkarte starten, indem Sie zu [https://shell.azure.com/bash](https://shell.azure.com/bash) navigieren. Wählen Sie **Kopieren** aus, um die Blöcke mit dem Code zu kopieren. Fügen Sie ihn anschließend in Cloud Shell ein, und drücken Sie die **EINGABETASTE**, um ihn auszuführen.

#### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Befehl `[az group create](/cli/azure/group)` eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* erstellt:

```azurecli-interactive

az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Erstellen einer privaten Cloud

Geben Sie einen Namen für die Ressourcengruppe und für die private Cloud sowie einen Standort und die Größe des Clusters an.

| Eigenschaft  | BESCHREIBUNG  |
| --------- | ------------ |
| **-g** (Ressourcengruppenname)     | Der Name der Ressourcengruppe für die Ressourcen Ihrer privaten Cloud        |
| **-n** (Name der privaten Cloud)     | Der Name Ihrer privaten Azure VMware Solution-Cloud        |
| **--location**     | Der für Ihre private Cloud verwendete Standort         |
| **--cluster-size**     | Die Größe des Clusters. Der Mindestwert ist „3“.         |
| **--network-block**     | Der für die private Cloud zu verwendende CIDR-IP-Adressenblock des Netzwerks. Der Adressblock darf sich nicht mit Adressblöcken überlappen, die in anderen virtuellen Netzwerken in Ihrem Abonnement und Ihren lokalen Netzwerken verwendet werden.        |
| **--sku** | Der SKU-Wert AV36 |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="delete-an-azure-vmware-solution-private-cloud"></a>Löschen einer privaten Azure VMware Solution-Cloud

Wenn Sie über eine private Azure VMware Solution-Cloud verfügen, die Sie nicht mehr benötigen, können Sie sie löschen. Eine private Azure VMware Solution-Cloud umfasst eine isolierte Netzwerkdomäne, mindestens einen bereitgestellten vSphere-Cluster auf dedizierten Serverhosts und mehrere virtuelle Computer. Wenn eine private Cloud gelöscht wird, werden auch alle virtuellen Computer, ihre Daten und Cluster entfernt. Die dedizierten Bare-Metal-Hosts werden auf sichere Weise bereinigt und an den freien Pool zurückgegeben. Die für den Kunden bereitgestellte Netzwerkdomäne wird gelöscht.  

> [!CAUTION]
> Das Löschen der privaten Cloud kann nicht rückgängig gemacht werden. Die Daten können nach dem Löschen der privaten Cloud nicht wiederhergestellt werden, da alle aktiven Workloads und Komponenten beendet und sämtliche Daten und Konfigurationseinstellungen der privaten Cloud zerstört werden (einschließlich der öffentlichen IP-Adressen).

### <a name="prerequisites"></a>Voraussetzungen

Nachdem eine private Cloud gelöscht wurde, gibt es keine Möglichkeit, die virtuellen Computer und die zugehörigen Daten wiederherzustellen. Wenn die Daten des virtuellen Computers später benötigt werden, muss der Administrator vor dem Löschen der privaten Cloud zuerst alle Daten sichern.

### <a name="steps-to-delete-an-azure-vmware-solution-private-cloud"></a>Schritte zum Löschen einer privaten Azure VMware Solution-Cloud

1. Greifen Sie im Azure-Portal auf die Azure VMware Solution-Seite zu.

2. Wählen Sie die zu löschende private Cloud aus.
 
3. Geben Sie den Namen der privaten Cloud ein, und wählen Sie **Ja** aus. Der Löschvorgang ist in einigen Stunden abgeschlossen.  

## <a name="azure-vmware-commands"></a>Azure VMware-Befehle

Eine Liste der Befehle, die Sie mit Azure VMware Solution verwenden können, finden Sie [hier](/cli/azure/ext/vmware/vmware).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde Folgendes vermittelt:

> [!div class="checklist"]
> * Erstellen einer privaten Cloud von Azure VMware Solution
> * Vergewissern, dass die private Cloud bereitgestellt wurde
> * Löschen einer privaten Azure VMware Solution-Cloud

Im nächsten Tutorial erfahren Sie, wie Sie eine Jumpbox erstellen. Die Jumpbox wird verwendet, um eine Verbindung mit Ihrer Umgebung herzustellen, damit Sie Ihre private Cloud lokal verwalten können.


> [!div class="nextstepaction"]
> [Tutorial: Hier erfahren Sie, wie Sie auf eine private Azure VMware Solution-Cloud zugreifen.](tutorial-access-private-cloud.md)
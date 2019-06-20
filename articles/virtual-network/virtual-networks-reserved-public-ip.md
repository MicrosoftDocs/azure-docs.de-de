---
title: Verwalten reservierter Azure-IP-Adressen (klassisch) | Microsoft-Dokumentation
description: Erfahren Sie mehr über reservierte IP-Adressen (klassisch) und wie sie mithilfe von Azure PowerShell und Azure CLI verwaltet werden.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 34652a55-3ab8-4c2d-8fb2-43684033b191
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: genli
ms.openlocfilehash: 8afed4eb1add0ba3a7db474e54b2f78a0babab06
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60789076"
---
# <a name="reserved-ip-addresses-classic-deployment"></a>Reservierte IP-Adressen (klassische Bereitstellung)

 In Azure fallen IP-Adressen in zwei Kategorien: dynamisch und reserviert. Öffentliche IP-Adressen, die von Azure verwaltet werden, sind standardmäßig dynamische IP-Adressen. Daher kann sich die IP-Adresse, die für einen bestimmten Clouddienst (VIP) oder zum direkten Zugreifen auf einen virtuellen Computer oder eine Rolleninstanz (ILPIP) genutzt wird, von Zeit zu Zeit ändern, wenn Ressourcen heruntergefahren oder angehalten werden (ihre Zuordnung wird aufgehoben).

Sie können eine IP-Adresse reservieren, um zu verhindern, dass sie sich ändert. Reservierte IPs können nur als VIP verwendet werden. So wird sichergestellt, dass die IP-Adresse für den Clouddienst sich auch dann nicht ändert, wenn Ressourcen heruntergefahren oder angehalten werden (ihre Zuordnung wird aufgehoben). Außerdem können Sie vorhandene dynamische IP-Adressen, die als VIP verwendet werden, in eine reservierte IP-Adresse konvertieren.

> [!IMPORTANT]
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen:  [das Resource Manager-Modell und das klassische Bereitstellungsmodell](../azure-resource-manager/resource-manager-deployment-model.md). Dieser Artikel befasst sich mit der Verwendung des klassischen Bereitstellungsmodells. Microsoft empfiehlt für die meisten neuen Bereitstellungen die Verwendung des Ressourcen-Manager-Modells. Erfahren Sie, wie Sie eine statische öffentliche IP-Adresse mit dem [Resource Manager-Bereitstellungsmodell](virtual-network-ip-addresses-overview-arm.md) reservieren.

Lesen Sie für weitere Informationen zu IP-Adressen in Azure den Artikel [IP-Adressen](virtual-network-ip-addresses-overview-classic.md).

## <a name="when-do-i-need-a-reserved-ip"></a>Wann benötige ich eine reservierte IP-Adresse?
* **Sie möchten sicherstellen, dass die IP-Adresse in Ihrem Abonnement reserviert ist**. Wenn Sie eine IP-Adresse reservieren möchten, die Ihr Abonnement unter keinen Umständen verlässt, sollten Sie eine reservierte öffentliche IP-Adresse verwenden.  
* **Sie möchten, dass die IP-Adresse Ihrem Clouddienst auch im beendeten oder neu zugeordneten Zustand (VMs) zugewiesen bleibt**. Wenn auf Ihren Dienst mit einer IP-Adresse zugegriffen werden soll, die sich auch dann nicht ändert, wenn virtuelle Computer im Clouddienst heruntergefahren oder angehalten werden (ihre Zuordnung wird aufgehoben), sollten Sie eine reservierte öffentliche IP-Adresse verwenden.
* **Sie möchten sicherstellen, dass für ausgehenden Datenverkehr von Azure eine vorhersagbare IP-Adresse verwendet wird**. Unter Umständen haben Sie Ihre lokale Firewall so konfiguriert, dass nur Datenverkehr von bestimmten IP-Adressen zugelassen wird. Bei Reservierung einer IP-Adresse kennen Sie die IP-Quelladresse und müssen Ihre Firewallregeln bei einer IP-Änderung nicht aktualisieren.

## <a name="faqs"></a>Häufig gestellte Fragen
- Kann ich eine reservierte IP-Adresse für alle Azure-Dienste verwenden?
    Nein. Reservierte IPs können nur für virtuelle Computer und Clouddienst-Instanzenrollen, die über eine VIP verfügbar gemacht werden, verwendet werden.
- Wie viele reservierte IP-Adressen können verwendet werden?
    Weitere Informationen finden Sie im Artikel [Grenzwerte für Netzwerke](../azure-subscription-service-limits.md#networking-limits).
- Fällt für reservierte IPs eine Gebühr an?
    Manchmal. Preisinformationen finden Sie auf der Seite [Preise für reservierte IP-Adressen](https://go.microsoft.com/fwlink/?LinkID=398482).
- Wie wird eine IP-Adresse reserviert?
    Sie können PowerShell, die [Azure-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn722420.aspx) oder das [Azure-Portal](https://portal.azure.com) nutzen, um eine IP-Adresse in einer Azure-Region zu reservieren. Ihrem Abonnement wird eine reservierte IP-Adresse zugeordnet.
- Kann ich eine reservierte IP-Adresse mit auf Affinitätsgruppen basierenden VNets verwenden?
    Nein. Reservierte IP-Adressen werden nur in regionalen VNets unterstützt. Für VNets, die Affinitätsgruppen zugeordnet sind, besteht keine Unterstützung reservierter IP-Adressen. Weitere Informationen zum Zuordnen eines VNets zu einer Region oder Affinitätsgruppe finden Sie im Artikel [Informationen zu regionalen VNets und Affinitätsgruppen](virtual-networks-migrate-to-regional-vnet.md).

## <a name="manage-reserved-vips"></a>Verwalten von reservierten VIPs

### <a name="using-azure-powershell-classic"></a>Verwenden von Azure PowerShell (klassisch)

Bevor Sie reservierte IPs verwenden können, müssen Sie diese Ihrem Abonnement hinzufügen. Erstellen Sie wie folgt eine reservierte IP aus dem Pool öffentlicher IP-Adressen, die für den Standort *USA, Mitte* verfügbar sind:

> [!NOTE]
> Bei Verwendung des klassischen Bereitstellungsmodells müssen Sie die Dienstverwaltungsversion von Azure PowerShell installieren. Weitere Informationen finden Sie unter [Installing the Azure PowerShell Service Management module](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0) (Installieren des Azure PowerShell-Dienstverwaltungsmoduls). 

  ```powershell
    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
  ```
Beachten Sie dabei, dass Sie nicht angeben können, welche IP-Adresse reserviert wird. Führen Sie den folgenden PowerShell-Befehl aus, und notieren Sie sich die Werte für *ReservedIPName* und *Address*, um anzuzeigen, welche IP-Adressen in Ihrem Abonnement reserviert sind:

```powershell
Get-AzureReservedIP
```

Erwartete Ausgabe:

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                :
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          :
    DeploymentName       :
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

>[!NOTE]
>Wenn Sie mit PowerShell eine reservierte IP-Adresse erstellen, können Sie keine Ressourcengruppe angeben, in der die reservierte IP-Adresse erstellt werden soll. Azure platziert die Adresse automatisch in einer Ressourcengruppe namens *Default-Networking*. Wenn Sie die reservierte IP-Adresse im [Azure-Portal](https://portal.azure.com) erstellen, können Sie jede gewünschte Ressourcengruppe angeben. Wenn Sie die reservierte IP-Adresse jedoch in einer anderen Ressourcengruppe als *Default-Networking* erstellen, müssen Sie bei jedem Verweis auf die reservierte IP-Adresse mit Befehlen wie `Get-AzureReservedIP` und `Remove-AzureReservedIP` auf den Namen *Group resource-group-name reserved-ip-name* verweisen.  Wenn Sie z.B. eine reservierte IP-Adresse namens *myReservedIP* in einer Ressourcengruppe namens *myResourceGroup* erstellt haben, müssen Sie mit *Group myResourceGroup myReservedIP* auf den Namen der reservierten IP-Adresse verweisen.   


Nachdem eine IP-Adresse reserviert wurde, bleibt sie Ihrem Abonnement so lange zugeordnet, bis Sie sie löschen. Löschen Sie eine reservierte IP-Adresse wie folgt:

```powershell
Remove-AzureReservedIP -ReservedIPName "MyReservedIP"
```

### <a name="using-azure-cli-classic"></a>Verwenden von Azure CLI (klassisch)
Erstellen Sie mithilfe der klassischen Azure-Befehlszeilenschnittstelle wie folgt eine reservierte IP aus dem Pool öffentlicher IP-Adressen, die für den Standort *USA, Mitte* verfügbar sind:

> [!NOTE]
> Bei klassischen Bereitstellungen müssen Sie die klassische Azure-Befehlszeilenschnittstelle verwenden. Informationen zum Installieren der klassischen Azure-Befehlszeilenschnittstelle finden Sie unter [Installieren der klassischen Azure CLI](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest).
  
 Befehl:
 
```azurecli
azure network reserved-ip create <name> <location>
```
Beispiel:
 ```azurecli
 azure network reserved-ip create MyReservedIP centralus
 ```

Mit Azure CLI können Sie wie folgt anzeigen, welche IP-Adressen in Ihrem Abonnement reserviert sind: 

Befehl:
```azurecli
azure network reserved-ip list
```
Nachdem eine IP-Adresse reserviert wurde, bleibt sie Ihrem Abonnement so lange zugeordnet, bis Sie sie löschen. Löschen Sie eine reservierte IP-Adresse wie folgt:

Befehl:

 ```azurecli
 azure network reserved-ip delete <name>
 ```
  Beispiel:  
 ```azurecli
 azure network reserved-ip delete MyReservedIP
 ```
## <a name="reserve-the-ip-address-of-an-existing-cloud-service"></a>Reservieren der IP-Adresse eines vorhandenen Clouddiensts
Sie können die IP-Adresse eines vorhandenen Clouddiensts durch Hinzufügen des `-ServiceName`-Parameters reservieren. Reservieren Sie die IP-Adresse des Clouddiensts *TestService* am Standort *USA, Mitte* wie folgt:

- Verwenden von Azure PowerShell (klassisch):

  ```powershell
  New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService
  ```

- Verwenden von Azure CLI (klassisch):
  
    Befehl:

    ```azurecli
     azure network reserved-ip create <name> <location> -r <service-name> -d <deployment-name>
    ```
    Beispiel:

    ```azurecli
      azure network reserved-ip create MyReservedIP centralus -r TestService -d asmtest8942
    ```

## <a name="associate-a-reserved-ip-to-a-new-cloud-service"></a>Zuordnen einer reservierten IP zu einem neuen Clouddienst
Das folgende Skript erstellt eine neue reservierte IP-Adresse und ordnet diese einem neuen Clouddienst mit dem Namen *TestService* zu.

### <a name="using-azure-powershell-classic"></a>Verwenden von Azure PowerShell (klassisch)
```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"
```
> [!NOTE]
> Wenn Sie eine reservierte IP-Adresse zur Verwendung mit einem Clouddienst erstellen, müssen Sie auf den virtuellen Computer für die eingehende Kommunikation weiterhin mit *VIP:&lt;Portnummer>* verweisen. Das Reservieren einer IP-Adresse bedeutet nicht, dass Sie eine direkte Verbindung mit dem virtuellen Computer herstellen können. Die reservierte IP wird dem Clouddienst zugewiesen, für den der virtuelle Computer bereitgestellt wurde. Wenn Sie direkt per IP eine Verbindung mit dem virtuellen Computer herstellen möchten, müssen Sie eine öffentliche IP auf Instanzebene konfigurieren. Eine öffentliche IP-Adresse auf Instanzebene ist eine Art von öffentlicher IP-Adresse (als ILPIP bezeichnet), die Ihrem virtuellen Computer direkt zugewiesen ist. Sie kann nicht reserviert werden. Weitere Informationen finden Sie im Artikel [Öffentliche IP auf Instanzebene (ILPIP)](virtual-networks-instance-level-public-ip.md).
> 

## <a name="remove-a-reserved-ip-from-a-running-deployment"></a>Entfernen einer reservierten IP aus einer aktiven Bereitstellung

Entfernen Sie eine einem neuen Clouddienst hinzugefügte reservierte IP-Adresse wie folgt: 
### <a name="using-azure-powershell-classic"></a>Verwenden von Azure PowerShell (klassisch)

```powershell
Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService
```

### <a name="using-azure-cli-classic"></a>Verwenden von Azure CLI (klassisch)
Befehl:

```azurecli
azure network reserved-ip disassociate <name> <service-name> <deployment-name>
```

Beispiel:

```azurecli
azure network reserved-ip disassociate MyReservedIP TestService asmtest8942
```

> [!NOTE]
> Wenn Sie eine reservierte IP aus einer aktiven Bereitstellung entfernen, wird die Reservierung nicht aus Ihrem Abonnement entfernt. Die IP-Adresse wird lediglich frei und kann von einer anderen Ressource in Ihrem Abonnement verwendet werden.
> 

Um eine reservierte IP-Adresse vollständig aus einem Abonnement zu entfernen, führen Sie den folgenden Befehl aus:

Befehl:

```azurecli
azure network reserved-ip delete <name>
```
Beispiel:

```azurecli
azure network reserved-ip delete MyReservedIP
```

## <a name="associate-a-reserved-ip-to-a-running-deployment"></a>Zuordnen einer reservierten IP zu einer aktiven Bereitstellung

### <a name="using-azure-powershell-classic"></a>Verwenden von Azure PowerShell (klassisch)

Die folgenden Befehle erstellen einen Clouddienst namens *TestService2* mit einem neuen virtuellen Computer namens *TestVM2*. Die vorhandene reservierte IP-Adresse namens *MyReservedIP* wird dann dem Clouddienst zugewiesen.

```powershell
$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService2 -Location "Central US"

Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2
```

### <a name="using-azure-cli-classic"></a>Verwenden von Azure CLI (klassisch)
Mit Azure CLI können Sie Ihrer aktiven Clouddienstbereitstellung wie folgt eine neue reservierte IP-Adresse zuordnen:

Befehl:
```azurecli
azure network reserved-ip associate <name> <service-name> <deployment-name>
```
Beispiel:
```azurecli
azure network reserved-ip associate MyReservedIP TestService asmtest8942
```
## <a name="associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Zuordnen einer reservierten IP zu einem Clouddienst mit einer Dienstkonfigurationsdatei
Sie können eine reservierte IP einem Clouddienst auch zuordnen, indem Sie eine Dienstkonfigurationsdatei (CSCFG) verwenden. Der folgende XML-Beispielcode veranschaulicht, wie Sie einen Clouddienst für die Verwendung einer reservierten VIP mit dem Namen *MyReservedIP* konfigurieren:
```
   <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
```
## <a name="next-steps"></a>Nächste Schritte
* Informieren Sie sich über die Funktionsweise der [IP-Adressierung](virtual-network-ip-addresses-overview-classic.md) im klassischen Bereitstellungsmodell.
* Erfahren Sie mehr zu [reservierten privaten IP-Adressen](virtual-networks-reserved-private-ip.md).
* Erfahren Sie mehr zu [öffentlichen IP-Adressen auf Instanzebene (ILPIP)](virtual-networks-instance-level-public-ip.md).


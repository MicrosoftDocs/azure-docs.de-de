---
title: Bereitstellen eines Clusters für Verfügbarkeitszonen
description: Erfahren Sie, wie Sie einen Azure Service Fabric-Cluster über Verfügbarkeitszonen hinweg bereitstellen.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 50ab66a1f98d06d79a46d61f683d56822b619721
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007039"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Bereitstellen eines Azure Service Fabric-Clusters über Verfügbarkeitszonen hinweg
Verfügbarkeitszonen sind in Azure ein Hochverfügbarkeitsangebot, das Anwendungen und Daten vor Ausfällen von Rechenzentren schützt. Eine Verfügbarkeitszone ist ein eindeutiger physischer Standort, der mit unabhängiger Stromversorgung, Kühlung und Netzwerk innerhalb einer Azure-Region ausgestattet ist.

Service Fabric unterstützt Cluster, die sich über Verfügbarkeitszonen erstrecken, indem Knotentypen bereitgestellt werden, die an bestimmte Zonen angeheftet sind. Dadurch wird die Hochverfügbarkeit Ihrer Anwendungen sichergestellt. Azure-Verfügbarkeitszonen sind nur in ausgewählten Regionen verfügbar. Weitere Informationen finden Sie unter [Übersicht über Azure-Verfügbarkeitszonen](../availability-zones/az-overview.md).

Es sind Beispielvorlagen verfügbar: [Verfügbarkeitszonenübergreifende Service Fabric-Vorlage](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Empfohlene Topologie für den primären Knotentyp von Azure Service Fabric-Clustern, die sich über Verfügbarkeitszonen erstrecken
Ein Service Fabric-Cluster, der über die Verfügbarkeitszonen verteilt ist, stellt die Hochverfügbarkeit des Clusterzustands sicher. Um einen Service Fabric-Cluster zonenübergreifend zu verteilen, müssen Sie in jeder von der Region unterstützten Verfügbarkeitszone einen primären Knotentyp erstellen. Dadurch werden Startknoten gleichmäßig auf jeden der primären Knotentypen verteilt.

Die empfohlene Topologie für den primären Knotentyp erfordert die nachfolgend beschriebenen Ressourcen:

* Die Clusterzuverlässigkeitsstufe ist auf „Platinum“ (Platin) festgelegt.
* Drei Knotentypen sind als „primär“ markiert.
    * Jeder Knotentyp sollte seiner eigenen VM-Skalierungsgruppe zugeordnet sein, die sich in verschiedenen Zonen befinden.
    * Jede VM-Skalierungsgruppe sollte mindestens fünf Knoten aufweisen (Dauerhaftigkeit „Silver“ (Silber)).
* Eine einzelne öffentliche IP-Ressource mit Standard-SKU.
* Eine einzelne Load Balancer-Ressource mit Standard-SKU.
* Eine Netzwerksicherheitsgruppe (NSG), auf die das Subnetz verweist, in dem Sie Ihren virtuellen Computer bereitstellen.

>[!NOTE]
> Die Eigenschaft der VM-Skalierungsgruppe für die einzelne Platzierungsgruppe muss auf „true“ festgelegt sein, da eine einzelne VM-Skalierungsgruppe, die mehrere Zonen umfasst, von Service Fabric nicht unterstützt wird.

 ![Diagramm der Architektur der Azure Service Fabric-Verfügbarkeitszone][sf-architecture]

## <a name="networking-requirements"></a>Netzwerkanforderungen
### <a name="public-ip-and-load-balancer-resource"></a>Öffentliche IP- und Load Balancer-Ressource
Um die Zoneneigenschaft für eine VM-Skalierungsgruppenressource zu aktivieren, müssen die Load Balancer- und die IP-Ressource, auf die diese VM-Skalierungsgruppe verweist, beide eine *Standard*-SKU verwenden. Das Erstellen einer Load Balancer- oder IP-Ressource ohne die SKU-Eigenschaft erstellt eine Basic-SKU, die keine Verfügbarkeitszonen unterstützt. Ein Standard-SKU-Lastenausgleich blockiert standardmäßig den gesamten Datenverkehr von außen. Sie müssen eine NSG im Subnetz bereitstellen, um den externen Datenverkehr zu ermöglichen.

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
```

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> Es ist nicht möglich, eine direkte Änderung der SKU an den öffentlichen IP- und Load Balancer-Ressourcen vorzunehmen. Informationen zum Migrieren von vorhandenen Ressourcen mit Basic-SKU finden Sie im Abschnitt zur Migration in diesem Artikel.

### <a name="virtual-machine-scale-set-nat-rules"></a>NAT-Regeln für VM-Skalierungsgruppen
Die NAT-Regeln für eingehenden Datenverkehr des Lastenausgleichs müssen mit den NAT-Pools aus der VM-Skalierungsgruppe übereinstimmen. Jede VM-Skalierungsgruppe muss über einen eindeutigen NAT-Pool für eingehenden Datenverkehr verfügen.

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>Load Balancer-Ausgangsregeln der Standard-SKU
Load Balancer Standard und Standard Public IP führen im Vergleich zur Verwendung der Basic-SKUs neue Fähigkeiten und andere Verhaltensweisen für ausgehende Verbindungen ein. Wenn Sie bei der Arbeit mit Standard-SKUs ausgehende Verbindungen wünschen, müssen Sie diese explizit entweder mit Standard Public IP-Adressen oder Standard Public Load Balancer definieren. Weitere Informationen finden Sie unter [Ausgehende Verbindungen](../load-balancer/load-balancer-outbound-connections.md) und [Azure Load Balancer Standard](../load-balancer/load-balancer-overview.md).

>[!NOTE]
> Die Standardvorlage verweist auf eine Netzwerksicherheitsgruppe, die standardmäßig den gesamten ausgehenden Datenverkehr gestattet. Der eingehende Datenverkehr ist auf die Ports beschränkt, die für Vorgänge zur Service Fabric-Verwaltung erforderlich sind. Die Regeln der Netzwerksicherheitsgruppe können an Ihre Anforderungen angepasst werden.

>[!NOTE]
> Jeder Service Fabric-Cluster, der die Standard-SKU für SLB verwendet, muss sicherstellen, dass jeder Knotentyp über eine Regel verfügt, die ausgehenden Datenverkehr an Port 443 zulässt. Dies ist erforderlich, um die Clustereinrichtung abzuschließen, und jede Bereitstellung ohne diese Regel schlägt fehl.


### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Aktivieren von Zonen für eine VM-Skalierungsgruppe
Um eine Zone zu aktivieren, müssen Sie in einer VM-Skalierungsgruppe die folgenden drei Werte in die Ressourcen der VM-Skalierungsgruppe aufnehmen.

* Der erste Wert ist die **zones**-Eigenschaft, die angibt, in welcher Verfügbarkeitszone die VM-Skalierungsgruppe bereitgestellt wird.
* Der zweite Wert ist die Eigenschaft „singlePlacementGroup“, die auf „true“ festgelegt werden muss.
* Der dritte Wert ist die Eigenschaft „faultDomainOverride“ in der Erweiterung der Service Fabric-VM-Skalierungsgruppe. Der Wert für diese Eigenschaft sollte nur die Zone enthalten, in der diese VM-Skalierungsgruppe platziert wird. Beispiel: „faultDomainOverride“: „az1“. Alle Ressourcen der VM-Skalierungsgruppe müssen in derselben Region positioniert werden, da Azure Service Fabric-Cluster keine regionsübergreifende Unterstützung aufweisen.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1"],
    "properties": {
        "singlePlacementGroup": "true",
    },
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
            "faultDomainOverride": "az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Aktivieren mehrerer primärer Knotentypen in der Service Fabric-Clusterressource
Legen Sie die „isPrimary“-Eigenschaft auf „true“ fest, um mindestens einen Knotentyp in einer Clusterressource als „primär“ festzulegen. Wenn Sie einen Service Fabric-Cluster über Verfügbarkeitszonen hinweg bereitstellen, sollten Sie drei Knotentypen in unterschiedlichen Zonen verwenden.

```json
{
    "reliabilityLevel": "Platinum",
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType2Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt2applicationEndPort')]",
            "startPort": "[parameters('nt2applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt2ephemeralEndPort')]",
            "startPort": "[parameters('nt2ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt2InstanceCount')]"
    }
    ],
}
```

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Migration zur Verwendung von Verfügbarkeitszonen aus einem Cluster mit einem Basic-SKU-Load Balancer und einer Basic-SKU-IP
Sie müssen zunächst eine völlig neue Load Balancer- und IP-Ressource mit der Standard-SKU erstellen, um einen Cluster zu migrieren, der einen Load Balancer und eine IP mit einer Basic-SKU verwendet hat. Es ist nicht möglich, diese Ressourcen direkt zu aktualisieren.

Auf den neuen Load Balancer und die neue IP sollte in den neuen Knotentypen der Verfügbarkeitszone verwiesen werden, die Sie verwenden möchten. Im obigen Beispiel wurden drei neue VM-Skalierungsgruppenressourcen in den Zonen 1, 2 und 3 hinzugefügt. Diese VM-Skalierungsgruppen verweisen auf den neu erstellten Load Balancer und die neue IP und sind in der Service Fabric-Clusterresource als primäre Knotentypen gekennzeichnet.

Damit Sie beginnen können, müssen Sie die neuen Ressourcen zu Ihrer bestehenden Resource Manager-Vorlage hinzufügen. Diese Ressourcen umfassen Folgendes:
* Eine öffentliche IP-Ressource mit Standard-SKU.
* Eine Load Balancer-Ressource mit Standard-SKU.
* Eine Netzwerksicherheitsgruppe (NSG), auf die das Subnetz verweist, in dem Sie Ihren virtuellen Computer bereitstellen.
* Drei Knotentypen sind als „primär“ markiert.
    * Jeder Knotentyp sollte seiner eigenen VM-Skalierungsgruppe zugeordnet sein, die sich in verschiedenen Zonen befinden.
    * Jede VM-Skalierungsgruppe sollte mindestens fünf Knoten aufweisen (Dauerhaftigkeit „Silver“ (Silber)).

Ein Beispiel für diese Ressourcen finden Sie in der [Beispielvorlage](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Nachdem die Ressourcen die Bereitstellung abgeschlossen haben, können Sie beginnen, die Knoten im primären Knotentyp aus dem ursprünglichen Cluster zu deaktivieren. Wenn die Knoten deaktiviert sind, migrieren die Systemdienste zum neuen primären Knotentyp, der im obigen Schritt bereitgestellt wurde.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"

$nodeNames = @("_nt0_0", "_nt0_1", "_nt0_2", "_nt0_3", "_nt0_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames) {
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Sobald die Knoten alle deaktiviert sind, werden die Systemdienste auf dem primären Knotentyp ausgeführt, der über Zonen verteilt ist. Sie können dann die deaktivierten Knoten aus dem Cluster entfernen. Nachdem die Knoten entfernt wurden, können Sie die ursprünglichen IP-, Load Balancer- und VM-Skalierungsgruppenressourcen entfernen.

```powershell
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}

$scaleSetName="nt0"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force

$lbname="LB-cluster-nt0"
$oldPublicIpName="LBIP-cluster-0"
$newPublicIpName="LBIP-cluster-1"

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

Sie sollten dann die Verweise auf diese Ressourcen aus der Resource Manager-Vorlage entfernen, die Sie bereitgestellt haben.

Der abschließende Schritt besteht in der Aktualisierung des DNS-Namens und der öffentlichen IP-Adresse.

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

```

## <a name="preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set"></a>(Vorschau) Aktivieren mehrerer Verfügbarkeitszonen in einer einzelnen VM-Skalierungsgruppe

Die zuvor erwähnte Lösung verwendet einen NodeType pro VZ. Die folgende Lösung ermöglicht Benutzern die Bereitstellung von 3 VZs in demselben NodeType.

Eine vollständige Beispielvorlage finden Sie [hier](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-Windows-Multiple-AZ-Secure).

![Architektur der Azure Service Fabric-Verfügbarkeitszone][sf-multi-az-arch]

### <a name="configuring-zones-on-a-virtual-machine-scale-set"></a>Konfigurieren von Zonen für eine VM-Skalierungsgruppe
Um Zonen zu aktivieren, müssen Sie in einer VM-Skalierungsgruppe die folgenden drei Werte in die Ressourcen der VM-Skalierungsgruppe aufnehmen.

* Der erste Wert ist die **zones**-Eigenschaft, die angibt, welche Verfügbarkeitszonen in der VM-Skalierungsgruppe vorhanden sind.
* Der zweite Wert ist die Eigenschaft „singlePlacementGroup“, die auf „true“ festgelegt werden muss. **Die Skalierungsgruppe erstreckt sich über drei Verfügbarkeitszonen und kann auf bis zu 300 VMs skaliert werden, auch mit „singlePlacementGroup = true“.**
* Der dritte Wert zoneBalance stellt sicher, dass das strikte Zonengleichgewicht auf TRUE festgelegt ist. Sie sollten diese Einstellung auf TRUE festlegen, um eine unausgeglichene Verteilung von VMs auf Zonen zu vermeiden. Informieren Sie sich über [zoneBalancing](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing).
* Die Außerkraftsetzungen für FaultDomain und UpgradeDomain müssen nicht konfiguriert werden.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1", "2", "3"],
    "properties": {
        "singlePlacementGroup": "true",
        "zoneBalance": true
    }
}
```

>[!NOTE]
> * **SF-Cluster müssen mindestens einen primären NodeType aufweisen. Primäre NodeTypes müssen mindestens den DurabilityLevel „Silver“ aufweisen.**
> * Die sich über die VZs erstreckende VM-Skalierungsgruppe sollte unabhängig vom durabilityLevel mit mindestens 3 Verfügbarkeitszonen konfiguriert werden.
> * Die sich über die VZs erstreckende VM-Skalierungsgruppe mit Silver-Dauerhaftigkeit (oder höher) sollte mindestens 15 VMs aufweisen.
> * Die sich über die VZs erstreckende VM-Skalierungsgruppe mit Bronze-Dauerhaftigkeit sollte mindestens 6 VMs aufweisen.

### <a name="enabling-the-support-for-multiple-zones-in-the-service-fabric-nodetype"></a>Aktivieren der Unterstützung für mehrere Zonen im Service Fabric-NodeType
Der Service Fabric-NodeType muss aktiviert werden, um mehrere Verfügbarkeitszonen zu unterstützen.

* Der erste Wert, der für NodeType auf „true“ festgelegt werden sollte, ist **multipleAvailabilityZones**.
* Der zweite, optionale Wert ist **sfZonalUpgradeMode**. Diese Eigenschaft kann nicht geändert werden, wenn ein NodeType mit mehreren VZs bereits im Cluster vorhanden ist.
      Die Eigenschaft steuert die logische Gruppierung von VMs in Upgradedomänen.
          Folgendes gilt, wenn der Wert auf „Parallel“ festgelegt wird: Virtuelle Computer unter dem Knotentyp werden in UDs gruppiert, wobei die Zoneninformationen in fünf UDs ignoriert werden.
          Folgendes gilt, wenn der Wert ausgelassen oder auf „Hierarchical“ (Hierarchisch) festgelegt wird: VMs werden gruppiert, um die zonale Verteilung in bis zu 15 UDs widerzuspiegeln. Jede der drei Zonen erhält fünf UDS.
          Diese Eigenschaft definiert nur das Upgradeverhalten für ServiceFabric-Anwendungs- und Codeupgrades. Die zugrunde liegenden Upgrades für VM-Skalierungsgruppen werden in allen VZ weiterhin parallel durchlaufen.
      Diese Eigenschaft wirkt sich nicht auf die UD-Verteilung für Knotentypen aus, für die nicht mehrere Zonen aktiviert sind.
* Der dritte Wert ist **vmssZonalUpgradeMode = Parallel**. Dies ist eine *erforderliche* Eigenschaft, die im Cluster konfiguriert werden soll, wenn ein NodeType mit mehreren VZs hinzugefügt wird. Diese Eigenschaft definiert den Upgrademodus für die Updates der VM-Skalierungsgruppe, die in allen VZs parallel ausgeführt werden.
      Diese Eigenschaft kann derzeit nur auf „Parallel“ festgelegt werden.
* Die apiVersion der Service Fabric-Clusterressource sollte „2020-12-01-preview“ oder höher lauten.
* Die Clustercodeversion sollte „7.2.445“ oder höher sein.

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "location": "[parameters('clusterLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
    ],
    "properties": {
        "reliabilityLevel": "Platinum",
        "SFZonalUpgradeMode": "Hierarchical",
        "VMSSZonalUpgradeMode": "Parallel",
        "nodeTypes": [
          {
                "name": "[parameters('vmNodeType0Name')]",
                "multipleAvailabilityZones": true,
          }
        ]
}
```

>[!NOTE]
> * Öffentliche IP- und Load Balancer-Ressourcen sollten die Standard SKU verwenden, wie zuvor in diesem Artikel beschrieben.
> * Die Eigenschaft „multipleAvailabilityZones“ für NodeType kann nur zum Zeitpunkt der Erstellung von „NodeType“ definiert und später nicht geändert werden. Folglich können vorhandene NodeType-Typen nicht mit dieser Eigenschaft konfiguriert werden.
> * Wenn „sfZonalUpgradeMode“ ausgelassen oder auf „Hierarchisch“ festgelegt wird, werden die Cluster- und Anwendungsbereitstellungen langsamer, da im Cluster weitere Upgradedomänen vorhanden sind. Es ist wichtig, die Upgraderichtlinientimeouts ordnungsgemäß für die Upgradezeitdauer für 15 Upgradedomänen anzupassen.
> * Es wird empfohlen, als Zuverlässigkeitsstufe für den Cluster „Platin“ festzulegen, um sicherzustellen, dass der Cluster das Szenario mit einer ausgefallenen Zone überlebt.

>[!NOTE]
> Für bewährte Methoden empfiehlt es sich, sfZonalUpgradeMode auf „Hierarchisch“ festzulegen oder wegzulassen. Die Bereitstellung befolgt die zonale Verteilung von virtuellen Computern, die sich auf eine geringere Anzahl von Replikaten und/oder Instanzen auswirkt und sie sicherer macht.
> Verwenden Sie sfZonalUpgradeMode mit der Einstellung „Parallel“, wenn die Bereitstellungsgeschwindigkeit eine Priorität ist oder nur eine zustandslose Workload auf dem Knotentyp mit mehreren VZs ausgeführt wird. Dies führt dazu, dass der UD-Gang in allen VZs parallel erfolgt.

### <a name="migration-to-the-node-type-with-multiple-availability-zones"></a>Migration zum Knotentyp mit mehreren Verfügbarkeitszonen
Für alle Migrationsszenarien muss ein neuer NodeType hinzugefügt werden, der mehrere Verfügbarkeitszonen unterstützt. Ein vorhandener NodeType kann nicht migriert werden, um mehrere Zonen zu unterstützen.
In [diesem](./service-fabric-scale-up-primary-node-type.md) Artikel werden die ausführlichen Schritte zum Hinzufügen eines neuen NodeTypes und zum Hinzufügen der anderen Ressourcen erfasst, die für den neuen NodeType erforderlich sind, wie die IP-und LB-Ressourcen. Außerdem wird im gleichen Artikel beschrieben, wie Sie den vorhandenen NodeType außer Kraft setzen, nachdem der NodeType mit mehreren Verfügbarkeitszonen dem Cluster hinzugefügt wurde.

* Migration von einem NodeType, der grundlegende LB- und IP-Ressourcen verwendet: Dies wird bereits [hier](#migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip) für die Lösung mit einem Knotentyp pro VZ beschrieben. 
    Der einzige Unterschied für den neuen Knotentyp besteht darin, dass es nur 1 VM-Skalierungsgruppe und 1 NodeType für alle VZs anstatt 1 pro VZ gibt.
* Migration von einem NodeType, der Standard SKU-LB- und -IP-Ressourcen verwendet:   Befolgen Sie das oben beschriebene Verfahren mit der Ausnahme, dass es nicht erforderlich ist, neue LB-, IP- und NSG-Ressourcen hinzuzufügen, und dass die gleichen Ressourcen im neuen NodeType wiederverwendet werden können.


[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-multi-az-arch]: ./media/service-fabric-cross-availability-zones/sf-multi-az-topology.png
---
title: 'Tutorial: Bereitstellen in einem vorhandenen virtuellen Netzwerk mithilfe von PowerShell – Azure Dedicated HSM | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie ein dediziertes HSM mithilfe von PowerShell in einem vorhandenen virtuellen Netzwerk bereitstellen.
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18, devx-track-azurepowershell
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/14/2020
ms.author: johndaw
ms.openlocfilehash: ee431df89128a516e3a1cabeb43b5cbe9e356dae
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927854"
---
# <a name="tutorial--deploying-hsms-into-an-existing-virtual-network-using-powershell"></a>Tutorial: Bereitstellen von HSMs in einem vorhandenen virtuellen Netzwerk mithilfe von PowerShell

Der Azure-Dienst für dedizierte HSMs stellt ein physisches Gerät für die Verwendung durch einen Kunden mit umfassender administrativer Kontrolle und vollständiger Verwaltungsverantwortung bereit. Da es sich hierbei um physische Hardware handelt, muss Microsoft die Zuordnung dieser Geräte steuern, um eine effektive Kapazitätsverwaltung zu gewährleisten. Deshalb steht der Dienst für dedizierte HSMs innerhalb eines Azure-Abonnements standardmäßig nicht für die Ressourcenbereitstellung zur Verfügung. Azure-Kunden, die Zugriff auf den Dienst für dedizierte HSMs benötigen, müssen bei ihrem Microsoft-Kundenbetreuer die Registrierung für den Dienst für dedizierte HSMs beantragen. Die Bereitstellung ist erst nach Abschluss dieses Prozesses möglich.
Dieses Tutorial zeigt einen typischen Bereitstellungsprozess mit folgenden Kriterien:

- Ein Kunde verfügt bereits über ein virtuelles Netzwerk.
- Der Kunde verfügt über einen virtuellen Computer.
- Der Kunde muss seiner vorhandenen Umgebung HSM-Ressourcen hinzufügen.

Eine typische Bereitstellungsarchitektur mit Hochverfügbarkeit und mehreren Regionen kann beispielsweise wie folgt aussehen:

![Bereitstellung mit mehreren Regionen](media/tutorial-deploy-hsm-powershell/high-availability.png)

Dieses Tutorial konzentriert sich auf die Integration eines HSM-Paars und des erforderlichen ExpressRoute-Gateways („Subnet 1“ in der obigen Abbildung) in ein vorhandenes virtuelles Netzwerk („VNET 1“ in der obigen Abbildung).  Alle anderen Ressourcen sind Azure-Standardressourcen. Der gleiche Integrationsprozess kann auch für HSMs in „Subnet 4“ für „VNET 3“ (ebenfalls in der obigen Abbildung) verwendet werden.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Da der Azure-Dienst für dedizierte HSMs derzeit nicht im Azure-Portal verfügbar ist, werden sämtliche Interaktionen mit dem Dienst über die Befehlszeile oder mithilfe von PowerShell abgewickelt. In diesem Tutorial wird PowerShell in Azure Cloud Shell verwendet. Sollten Sie noch nicht mit der PowerShell vertraut sein, informieren Sie sich hier über die ersten Schritte: [Get started with Azure PowerShell](/powershell/azure/get-started-azureps) (Erste Schritte mit Azure PowerShell)

Voraussetzungen:

- Sie haben den Registrierungsprozess für den Azure-Dienst für dedizierte HSMs durchlaufen und sind zur Verwendung des Diensts berechtigt. Wenden Sie sich andernfalls an Ihren Microsoft-Kontobeauftragten. 
- Sie haben eine Ressourcengruppe für die Ressourcen erstellt, und die neuen, die im Rahmen dieses Tutorials erstellt werden, werden dieser Gruppe hinzugefügt.
- Sie haben bereits das erforderliche virtuelle Netzwerk und das Subnetz sowie virtuelle Computer gemäß dem obigen Diagramm erstellt und möchten nun zwei HSMs in diese Bereitstellung integrieren.

Bei allen weiteren Schritten wird davon ausgegangen, dass Sie bereits zum Azure-Portal navigiert sind und Cloud Shell geöffnet haben (durch Auswählen von „\>\_“ rechts oben im Portal).

## <a name="provisioning-a-dedicated-hsm"></a>Bereitstellen eines dedizierten HSM

Die Bereitstellung der HSMs und die Integration in ein vorhandenes virtuelles Netzwerk mittels ExpressRoute-Gateway wird mithilfe des ssh-Befehlszeilentools überprüft, um sicherzustellen, dass das HSM-Gerät für weitere Konfigurationsaktivitäten erreichbar und grundsätzlich verfügbar ist. In den folgenden Befehlen werden unter Verwendung einer Resource Manager-Vorlage HSM-Ressourcen und entsprechende Netzwerkressourcen erstellt.

### <a name="validating-feature-registration"></a>Überprüfen der Featureregistrierung

Der Dienst für dedizierte HSMs muss wie bereits erwähnt für Ihr Abonnement registriert sein, um Bereitstellungsaktivitäten ausführen zu können. Vergewissern Sie sich, dass diese Anforderung erfüllt ist, indem Sie im Azure-Portal den folgenden PowerShell-Befehl in Cloud Shell ausführen: 

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.HardwareSecurityModules -FeatureName AzureDedicatedHsm
```

Fahren Sie erst mit den weiteren Schritten fort, wenn beim Befehl der Status „Registered“ zurückgegeben wird, wie in der folgenden Abbildung zu sehen.  Falls Sie sich noch nicht für diesen Dienst registriert haben, wenden Sie sich an Ihren Microsoft-Kontobeauftragten.

![Abonnementstatus](media/tutorial-deploy-hsm-powershell/subscription-status.png)

### <a name="creating-hsm-resources"></a>Erstellen von HSM-Ressourcen

Ein HSM-Gerät wird im virtuellen Netzwerk eines Kunden bereitgestellt. Dies impliziert den Bedarf für ein Subnetz. Um die Kommunikation zwischen dem virtuellen Netzwerk und dem physischen Gerät zu ermöglichen, benötigt das HSM ein ExpressRoute-Gateway. Und schließlich wird noch ein virtueller Computer benötigt, um unter Verwendung der Gemalto-Clientsoftware auf das HSM-Gerät zugreifen zu können. Diese Ressourcen wurden zur Vereinfachung in einer Vorlagendatei mit entsprechender Parameterdatei zusammengefasst. Die Dateien können Sie direkt bei Microsoft anfordern: HSMrequest@Microsoft.com.

Wenn Sie über die Dateien verfügen, müssen Sie die Parameterdatei bearbeiten, um Ihre bevorzugten Ressourcennamen einzufügen. Dazu müssen Zeilen bearbeitet werden, die Folgendes enthalten: "value":""

- `namingInfix`: Präfix für Namen von HSM-Ressourcen
- `ExistingVirtualNetworkName`: Name des für die HSMs verwendeten virtuellen Netzwerks
- `DedicatedHsmResourceName1`: Name der HSM-Ressource im Rechenzentrum 1
- `DedicatedHsmResourceName2`: Name der HSM-Ressource im Rechenzentrum 2
- `hsmSubnetRange`: Subnetz-IP-Adressbereich für HSMs
- `ERSubnetRange`: Subnetz-IP-Adressbereich für das VNET-Gateway

Beispiel für diese Änderungen:

```json
{
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "namingInfix": {
      "value": "MyHSM"
    },
    "ExistingVirtualNetworkName": {
      "value": "MyHSM-vnet"
    },
    "DedicatedHsmResourceName1": {
      "value": "HSM1"
    },
    "DedicatedHsmResourceName2": {
      "value": "HSM2"
    },
    "hsmSubnetRange": {
      "value": "10.0.2.0/24"
    },
    "ERSubnetRange": {
      "value": "10.0.255.0/26"
    },
  }
}
```

Die zugeordnete Resource Manager-Vorlagendatei erstellt sechs Ressourcen mit folgenden Informationen:

- Ein Subnetz für die HSMs im angegebenen VNET
- Ein Subnetz für das Gateway für virtuelle Netzwerke 
- Ein Gateway für virtuelle Netzwerke, das das VNET mit den HSM-Geräten verbindet
- Eine öffentliche IP-Adresse für das Gateway
- Ein HSM im Rechenzentrum 1
- Ein HSM im Rechenzentrum 2

Nach dem Festlegen der Parameterwerte müssen die Dateien in die Cloud Shell-Dateifreigabe des Azure-Portals hochgeladen werden. Klicken Sie rechts oben im Azure-Portal auf das Cloud Shell-Symbol „\>\_“. Daraufhin wird im unteren Bildschirmbereich eine Befehlsumgebung angezeigt. Sie haben die Wahl zwischen BASH und PowerShell. Vergewissern Sie sich, dass „BASH“ ausgewählt ist.

Auf der Symbolleiste der Befehlsshell steht eine Upload-/Downloadoption zur Verfügung, mit der Sie die Vorlagen- und die Parameterdatei in Ihre Dateifreigabe hochladen können:

![Dateifreigabe](media/tutorial-deploy-hsm-powershell/file-share.png)

Nach dem Hochladen der Dateien können Sie mit der Ressourcenerstellung beginnen.
Vergewissern Sie sich vor der Erstellung neuer HSM-Ressourcen, dass bestimmte erforderliche Ressourcen vorhanden sind. Sie benötigen ein virtuelles Netzwerk mit Subnetzbereichen für Compute, HSMs und Gateway. Die folgenden Beispielbefehle zeigen, wie Sie ein solches virtuelles Netzwerk erstellen:

```powershell
$compute = New-AzVirtualNetworkSubnetConfig `
  -Name compute `
  -AddressPrefix 10.2.0.0/24
```

```powershell
$delegation = New-AzDelegation `
  -Name "myDelegation" `
  -ServiceName "Microsoft.HardwareSecurityModules/dedicatedHSMs"

```

```powershell
$hsmsubnet = New-AzVirtualNetworkSubnetConfig ` 
  -Name hsmsubnet ` 
  -AddressPrefix 10.2.1.0/24 ` 
  -Delegation $delegation 

```

```powershell

$gwsubnet= New-AzVirtualNetworkSubnetConfig `
  -Name GatewaySubnet `
  -AddressPrefix 10.2.255.0/26

```

```powershell

New-AzVirtualNetwork `
  -Name myHSM-vnet `
  -ResourceGroupName myRG `
  -Location westus `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $compute, $hsmsubnet, $gwsubnet

```

>[!NOTE]
>Bei der Konfiguration für das virtuelle Netzwerk ist insbesondere darauf zu achten, dass Delegierungen für das Subnetz des HSM-Geräts auf „Microsoft.HardwareSecurityModules/dedicatedHSMs“ festgelegt sind.  Andernfalls funktioniert die HSM-Bereitstellung nicht.

Sind alle Voraussetzungen erfüllt, führen Sie den folgenden Befehl aus, um die Resource Manager-Vorlage zu verwenden. Dabei muss zumindest der Name der Ressourcengruppe mit einem individuellen Namen aktualisiert werden:

```powershell

New-AzResourceGroupDeployment -ResourceGroupName myRG `
     -TemplateFile .\Deploy-2HSM-toVNET-Template.json `
     -TemplateParameterFile .\Deploy-2HSM-toVNET-Params.json `
     -Name HSMdeploy -Verbose

```

Die Ausführung dieses Befehls dauert etwa 20 Minuten. Die Option „-verbose“ sorgt dafür, dass der Status kontinuierlich angezeigt wird.

![Bereitstellungsstatus](media/tutorial-deploy-hsm-powershell/progress-status.png)

Nach erfolgreichem Abschluss des Vorgangs ("provisioningState": "Succeeded") können Sie sich bei Ihrem vorhandenen virtuellen Computer anmelden und sich per SSH vergewissern, dass das HSM-Gerät verfügbar ist.

## <a name="verifying-the-deployment"></a>Überprüfen der Bereitstellung

Führen Sie die folgenden Befehle aus, um sich zu vergewissern, dass die Geräte bereitgestellt wurden, und um die Geräteattribute anzuzeigen. Achten Sie darauf, dass die Ressourcengruppe korrekt festgelegt ist und der Ressourcenname exakt der Angabe in der Parameterdatei entspricht.

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG"
$resourceName = "HSM1"  
Get-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName

```

![Bereitstellungsstatus](media/tutorial-deploy-hsm-powershell/progress-status2.png)

Die Ressource wird nun auch im [Azure-Ressourcen-Explorer](https://resources.azure.com/) angezeigt.   Erweitern Sie im Explorer auf der linken Seite die Option „Abonnements“, erweitern Sie das Abonnement für dedizierte HSMs, erweitern Sie „Ressourcengruppen“, erweitern Sie die verwendete Ressourcengruppe, und wählen Sie schließlich das Element „Ressourcen“ aus.

## <a name="testing-the-deployment"></a>Testen der Bereitstellung

Zum Testen der Bereitstellung wird erst eine Verbindung mit einem virtuellen Computer, der auf die HSMs zugreifen kann, und anschließend eine direkte Verbindung mit dem HSM-Gerät hergestellt. Dadurch wird überprüft, ob das HSM erreichbar ist.
Die Verbindung mit dem virtuellen Computer wird mithilfe des ssh-Tools hergestellt. Der Befehl sieht in etwa wie folgt aus (allerdings mit dem Administratornamen und dem DNS-Namen aus dem Parameter):

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

Als Kennwort muss das Kennwort aus der Parameterdatei verwendet werden.
Nach der Anmeldung bei dem virtuellen Linux-Computer können Sie sich unter Verwendung der privaten IP-Adresse, die im Portal für die Ressource „\<prefix>hsm_vnic“ angegeben ist, beim HSM anmelden.

```powershell

(Get-AzResource -ResourceGroupName myRG -Name HSMdeploy -ExpandProperties).Properties.networkProfile.networkInterfaces.privateIpAddress

```
Wenn Sie die IP-Adresse ermittelt haben, führen Sie den folgenden Befehl aus:

`ssh tenantadmin@<ip address of HSM>`

Ist der Vorgang erfolgreich, werden Sie zur Eingabe eines Kennworts aufgefordert. Das Standardkennwort lautet „PASSWORD“. Das HSM fordert Sie zur Änderung des Kennworts auf. Legen Sie ein sicheres Kennwort fest, und verwenden Sie die von Ihrer Organisation bevorzugte Vorgehensweise, um es zu speichern und sicher aufzubewahren.  

>[!IMPORTANT]
>Sollte das Kennwort verloren gehen, muss das HSM zurückgesetzt werden, wodurch auch Ihre Schlüssel verloren gehen.

Führen Sie nach dem Herstellen der SSH-Verbindung mit dem HSM-Gerät den folgenden Befehl aus, um sich zu vergewissern, dass das HSM betriebsbereit ist.

`hsm show`

Die Ausgabe sollte in etwa wie folgt aussehen:

![Screenshot: Ausgabe des Befehls „hsm show“](media/tutorial-deploy-hsm-powershell/output.png)

Sie haben nun alle Ressourcen für eine hoch verfügbare Bereitstellung mit zwei HSMs zugeordnet und den Zugriff sowie die Betriebsbereitschaft geprüft. Die weiteren Konfigurations-/Testschritte erfordern eine intensivere Interaktion mit dem eigentlichen HSM-Gerät. Gehen Sie hierzu gemäß der Anleitung in Kapitel 7 des Administratorhandbuchs für Gemalto Luna Network HSM 7 vor, um das HSM zu initialisieren und Partitionen zu erstellen. Die gesamte Dokumentation und Software kann direkt von Gemalto heruntergeladen werden, nachdem Sie sich beim Kundensupportportal von Gemalto registriert und eine Kunden-ID erhalten haben. Laden Sie die Version 7.2 der Clientsoftware herunter, um alle erforderlichen Komponenten zu erhalten.

## <a name="delete-or-clean-up-resources"></a>Löschen oder Bereinigen von Ressourcen

Wenn Sie das HSM-Gerät nicht mehr benötigen, kann es als Ressource gelöscht und wieder dem freien Pool zugeführt werden. Ein wichtiger Aspekt bei diesem Schritt sind natürlich vertrauliche Kundendaten, die sich ggf. auf dem Gerät befinden. Ein Gerät lässt sich am besten durch dreimalige Falscheingabe des HSM-Administratorkennworts nullen. (Hinweis: Hierbei handelt es sich um das Kennwort des HSM-Administrators, nicht um das des Applianceadministrators.) Als Sicherheitsmaßnahme zum Schutz der Schlüsseldaten kann das Gerät erst als Azure-Ressource gelöscht werden, wenn es sich im genullten Zustand befindet.

> [!NOTE]
> Wenden Sie sich bei Problemen mit der Gemalto-Gerätekonfiguration bitte an den [Gemalto-Kundensupport](https://safenet.gemalto.com/technical-support/).

Wenn Sie die HSM-Ressource in Azure entfernen möchten, können Sie den folgenden Befehl verwenden. Ersetzen Sie dabei die „$“-Variablen durch Ihre eindeutigen Parameter:

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG" 
$resourceName = "HSMdeploy"  
Remove-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName 

```

## <a name="next-steps"></a>Nächste Schritte

Nach Abschluss dieses Tutorials verfügen Sie über bereitgestellte dedizierte HSM-Ressourcen, die in Ihrem virtuellen Netzwerk verfügbar sind. Diese Bereitstellung kann nun nach Bedarf mit weiteren Ressourcen für Ihre bevorzugte Bereitstellungsarchitektur ergänzt werden. Weitere hilfreiche Informationen für die Bereitstellungsplanung finden Sie in den Konzeptdokumenten. Es empfiehlt sich, zwei HSMs in einer primären Region (für die Verfügbarkeit auf Rackebene) und zwei HSMs in einer sekundären Region (für die regionale Verfügbarkeit) zu verwenden. Die Vorlagendatei aus diesem Tutorial kann problemlos als Grundlage für eine Bereitstellung mit zwei HSMs verwendet werden. Dabei müssen allerdings die Parameter an Ihre individuellen Anforderungen angepasst werden.

* [Hochverfügbarkeit](high-availability.md)
* [Physische Sicherheit](physical-security.md)
* [Netzwerk](networking.md)
* [Überwachung](monitoring.md)
* [Unterstützungsmöglichkeiten](supportability.md)

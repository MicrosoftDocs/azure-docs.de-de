---
title: 'Schnellstart: Erstellen eines Traffic Manager-Profils für Hochverfügbarkeit von Anwendungen mit Azure PowerShell'
description: In diesem Schnellstartartikel wird beschrieben, wie Sie ein Traffic Manager-Profil erstellen, um eine hoch verfügbare Webanwendung zu entwickeln.
services: traffic-manager
author: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2019
ms.author: kumud
ms.openlocfilehash: f6ed2e03352a335022d99cf703240552fa34e732
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729010"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>Schnellstart: Erstellen eines Traffic Manager-Profils für eine hoch verfügbare Webanwendung mit Azure PowerShell

In dieser Schnellstartanleitung wird beschrieben, wie Sie ein Traffic Manager-Profil erstellen, mit dem die Hochverfügbarkeit für Ihre Webanwendung sichergestellt wird.

In dieser Schnellstartanleitung erstellen Sie zwei Instanzen einer Webanwendung. Jede Instanz wird in einer anderen Azure-Region ausgeführt. Sie erstellen ein Traffic Manager-Profil basierend auf der [Endpunktpriorität](traffic-manager-routing-methods.md#priority). Das Profil leitet den Benutzerdatenverkehr an den primären Standort, an dem die Webanwendung ausgeführt wird. Die Webanwendung wird von Traffic Manager ständig überwacht. Wenn der primäre Standort nicht verfügbar ist, erfolgt automatisch ein Failover zum Sicherungsstandort.

Wenn Sie kein Azure-Abonnement besitzen, können Sie jetzt ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 5.4.1 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe.

```azurepowershell-interactive


# Variables
$Location1="WestUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>Erstellen eines Traffic Manager-Profils

Erstellen Sie mit [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) ein Traffic Manager-Profil, das Benutzerdatenverkehr basierend auf der Endpunktpriorität weiterleitet.

```azurepowershell-interactive

# Generates a random value
$Random=(New-Guid).ToString().Substring(0,8)
$mytrafficmanagerprofile="mytrafficmanagerprofile$Random"

New-AzTrafficManagerProfile `
-Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup `
-TrafficRoutingMethod Priority `
-MonitorPath '/' `
-MonitorProtocol "HTTP" `
-RelativeDnsName $mytrafficmanagerprofile `
-Ttl 30 `
-MonitorPort 80
```

## <a name="create-web-apps"></a>Erstellen von Web-Apps

Für diese Schnellstartanleitung benötigen Sie zwei Instanzen einer Webanwendung, die in zwei unterschiedlichen Azure-Regionen bereitgestellt werden (*USA, Westen* und *USA, Osten*). Jede dient jeweils als primärer bzw. Failoverendpunkt für Traffic Manager.

### <a name="create-web-app-service-plans"></a>Erstellen von Web-App-Dienstplänen
Erstellen Sie Web-App-Dienstpläne, indem Sie [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) für die beiden Instanzen der Webanwendung verwenden, die Sie in zwei unterschiedlichen Azure-Regionen bereitstellen.

```azurepowershell-interactive

# Variables
$App1Name="AppServiceTM1$Random"
$App2Name="AppServiceTM2$Random"
$Location1="WestUS"
$Location2="EastUS"

# Create an App service plan
New-AzAppservicePlan -Name "$App1Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location1 -Tier Standard
New-AzAppservicePlan -Name "$App2Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location2 -Tier Standard

```
### <a name="create-a-web-app-in-the-app-service-plan"></a>Erstellen einer Web-App im App Service-Plan
Erstellen Sie zwei Instanzen der Webanwendung, indem Sie [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) in den App Service-Plänen in den Azure-Regionen *USA, Westen* und *USA, Osten* verwenden.

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name $App1Name -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "$App1Name-Plan").Id
$App2ResourceId=(New-AzWebApp -Name $App2Name -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "$App2Name-Plan").Id

```

## <a name="add-traffic-manager-endpoints"></a>Hinzufügen von Traffic Manager-Endpunkten
Fügen Sie die beiden Web-Apps dem Traffic Manager-Profil wie folgt als Traffic Manager-Endpunkte hinzu, indem Sie [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) verwenden:
- Fügen Sie die Web-App aus der Azure-Region *USA, Westen* als primären Endpunkt für das Routing des gesamten Benutzerdatenverkehrs hinzu. 
- Fügen Sie die Web-App aus der Azure-Region *USA, Osten* als Failoverendpunkt hinzu. Wenn der primäre Endpunkt nicht verfügbar ist, wird der Datenverkehr automatisch an den Failoverendpunkt weitergeleitet.

```azurepowershell-interactive
New-AzTrafficManagerEndpoint -Name "$App1Name-$Location1" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App1ResourceId `
-EndpointStatus "Enabled"

New-AzTrafficManagerEndpoint -Name "$App2Name-$Location2" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App2ResourceId `
-EndpointStatus "Enabled"
```

## <a name="test-traffic-manager-profile"></a>Testen des Traffic Manager-Profils

In diesem Abschnitt überprüfen Sie den Domänennamen Ihres Traffic Manager-Profils. Außerdem konfigurieren Sie den primären Endpunkt so, dass er nicht verfügbar ist. Abschließend können Sie sehen, dass die Web-App weiterhin verfügbar ist. Dies liegt daran, dass Traffic Manager den Datenverkehr an den Failoverendpunkt sendet.

### <a name="determine-the-dns-name"></a>Ermitteln des DNS-Namens

Ermitteln Sie den DNS-Namen des Traffic Manager-Profils mit [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile).

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

Kopieren Sie den Wert von **RelativeDnsName**. Der DNS-Name Ihres Traffic Manager-Profils lautet „*http://<* relativednsname *>.trafficmanager.net*“. 

### <a name="view-traffic-manager-in-action"></a>Anzeigen von Traffic Manager in Aktion
1. Geben Sie in einem Webbrowser den DNS-Namen Ihres Traffic Manager-Profils (*http://<* relativednsname *>.trafficmanager.net*) ein, um die Standardwebsite Ihrer Web-App anzuzeigen.

    > [!NOTE]
    > In diesem Schnellstartszenario werden alle Anforderungen an den primären Endpunkt weitergeleitet. Es ist **Priorität 1** festgelegt.
2. Deaktivieren Sie Ihren primären Standort mit [Disable-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint), um das Traffic Manager-Failover in Aktion zu sehen.

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name $App1Name-$Location1 `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. Kopieren Sie den DNS-Namen Ihres Traffic Manager-Profils (*http://<* relativednsname *>.trafficmanager.net*), um die Website in einer neuen Browsersitzung anzuzeigen.
4. Vergewissern Sie sich, dass die Web-App weiterhin verfügbar ist.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppen, Webanwendungen und alle dazugehörigen Ressourcen mit [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup), wenn Sie fertig sind.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie ein Traffic Manager-Profil erstellt, um für Ihre Webanwendung für Hochverfügbarkeit zu sorgen. Weitere Informationen zum Weiterleiten des Datenverkehrs finden Sie in den Tutorials zu Traffic Manager.

> [!div class="nextstepaction"]
> [Traffic Manager-Tutorials](tutorial-traffic-manager-improve-website-response.md)
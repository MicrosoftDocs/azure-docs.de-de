---
title: Aktivieren von Azure AD Domain Services mithilfe von PowerShell | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure Active Directory Domain Services mithilfe von Azure AD PowerShell und Azure PowerShell konfigurieren und aktivieren.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 10/02/2020
ms.author: justinha
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 89061af04147d7cfaa0fdb3a6b1a8fb1cd8c8da7
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619146"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Aktivieren von Azure Active Directory Domain Services mithilfe von PowerShell

Azure Active Directory Domain Services (Azure AD DS) stellt verwaltete Domänendienste bereit, z. B. Domänenbeitritt, Gruppenrichtlinie, LDAP und Kerberos-/NTLM-Authentifizierung, die mit Windows Server Active Directory vollständig kompatibel sind. Sie können diese Domänendienste nutzen, ohne selbst Domänencontroller bereitstellen, verwalten und patchen zu müssen. Azure AD DS lässt sich in Ihren vorhandenen Azure AD-Mandanten integrieren. Dank dieser Integration können Benutzer sich mit ihren Unternehmensanmeldeinformationen anmelden, und Sie können vorhandene Gruppen und Benutzerkonten verwenden, um den Zugriff auf Ressourcen zu sichern.

In diesem Artikel erfahren Sie, wie Sie Azure AD DS mithilfe von PowerShell einrichten.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie folgende Ressourcen:

* Installieren und konfigurieren Sie Azure PowerShell.
    * Führen Sie bei Bedarf die Anweisungen zum [Installieren des Azure PowerShell-Moduls und Verbinden mit Ihrem Azure-Abonnement](/powershell/azure/install-az-ps) aus.
    * Stellen Sie sicher, dass Sie sich mit dem Cmdlet [Connect-AzAccount][Connect-AzAccount] bei Ihrem Azure-Abonnement anmelden.
* Installieren und konfigurieren Sie Azure AD PowerShell.
    * Führen Sie bei Bedarf die Anweisungen zum [Installieren des Azure AD PowerShell-Moduls und Verbinden mit Azure AD](/powershell/azure/active-directory/install-adv2) aus.
    * Stellen Sie sicher, dass Sie sich mit dem Cmdlet [Connect-AzureAD][Connect-AzureAD] bei Ihrem Azure AD-Mandanten anmelden.
* Sie benötigen Berechtigungen als *globaler Administrator* in Ihrem Azure AD-Mandanten, um Azure AD DS zu aktivieren.
* Sie benötigen Berechtigungen als *Mitwirkender* in Ihrem Azure-Abonnement, um die erforderlichen Azure AD DS-Ressourcen zu erstellen.

## <a name="create-required-azure-ad-resources"></a>Erstellen der erforderlichen Azure AD-Ressourcen

Azure AD DS erfordert einen Dienstprinzipal und eine Azure AD-Gruppe. Mit diesen Ressourcen kann die verwaltete Azure AD DS-Domäne Daten synchronisieren und definieren, welche Benutzer über Administratorrechte in der verwalteten Domäne verfügen.

Erstellen Sie zunächst einen Azure AD-Dienstprinzipal für Azure AD DS für die Kommunikation und Authentifizierung. Es wird eine bestimmte Anwendungs-ID namens *Domain Controller Services* mit der ID *6ba9a5d4-8456-4118-b521-9c5ca10cdf84* verwendet. Ändern Sie diese Anwendungs-ID nicht.

Erstellen Sie mit dem Cmdlet [New-AzureADServicePrincipal][New-AzureADServicePrincipal] einen Azure AD-Dienstprinzipal:

```powershell
New-AzureADServicePrincipal -AppId "6ba9a5d4-8456-4118-b521-9c5ca10cdf84"
```

Erstellen Sie nun eine Azure AD-Gruppe namens *AAD DC Administrators*. Den dieser Gruppe hinzugefügten Benutzern werden dann Berechtigungen zum Ausführen von Verwaltungsaufgaben für die verwaltete Domäne erteilt.

Rufen Sie zunächst mithilfe des Cmdlets [Get-AzureADGroup][Get-AzureADGroup] die Objekt-ID für die Gruppe *AAD DC Administrators* ab. Wenn die Gruppe nicht vorhanden ist, erstellen Sie sie mithilfe des Cmdlets [New-AzureADGroup][New-AzureADGroup] und der Gruppe *AAD DC Administrators*:

```powershell
# First, retrieve the object ID of the 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# If the group doesn't exist, create it
if (!$GroupObjectId) {
  $GroupObjectId = New-AzureADGroup -DisplayName "AAD DC Administrators" `
    -Description "Delegated group to administer Azure AD Domain Services" `
    -SecurityEnabled $true `
    -MailEnabled $false `
    -MailNickName "AADDCAdministrators"
  }
else {
  Write-Output "Admin group already exists."
}
```

Wenn die Gruppe *AAD DC Administrators* erstellt wurde, rufen Sie mithilfe des Cmdlets [Get-AzureADUser][Get-AzureADUser] die Objekt-ID des gewünschten Benutzers ab und fügen den Benutzer dann mithilfe des Cmdlets [Add-AzureADGroupMember][Add-AzureADGroupMember] zur Gruppe hinzu.

Das folgende Beispiel zeigt die Objekt-ID des Benutzers für das Konto mit dem Benutzerprinzipalnamen `admin@contoso.onmicrosoft.com`. Ersetzen Sie dieses Benutzerkonto durch den Benutzerprinzipalnamen des Benutzers, den Sie der Gruppe *AAD DC Administrators* hinzufügen möchten:

```powershell
# Retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="create-network-resources"></a>Erstellen von Netzwerkressourcen

Registrieren Sie zunächst den Azure AD Domain Services-Ressourcenanbieter mithilfe des Cmdlets [Register-AzResourceProvider][Register-AzResourceProvider]:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Erstellen Sie anschließend mit dem Cmdlet [New-AzResourceGroup][New-AzResourceGroup] eine neue Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* in der Region *westus* erstellt. Verwenden Sie Ihren eigenen Namen und die gewünschte Region:

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Erstellen Sie das virtuelle Netzwerk und Subnetze für Azure AD Domain Services. Es werden zwei Subnetze erstellt, und zwar eins für *DomainServices* und eins für *Workloads*. Azure AD DS wird im dedizierten Subnetz *DomainServices* bereitgestellt. Stellen Sie keine anderen Anwendungen oder Workloads in diesem Subnetz bereit. Verwenden Sie das separate Subnetz *Workloads* oder andere Subnetze für die übrigen virtuellen Computer.

Erstellen Sie die Subnetze mithilfe des Cmdlets [New-AzVirtualNetworkSubnetConfig][New-AzVirtualNetworkSubnetConfig], und erstellen Sie dann das virtuelle Netzwerk mithilfe des Cmdlets [New-AzVirtualNetwork][New-AzVirtualNetwork].

```powershell
$VnetName = "myVnet"

# Create the dedicated subnet for Azure AD Domain Services.
$SubnetName = "DomainServices"
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -AddressPrefix 10.0.0.0/24

# Create an additional subnet for your own VM workloads
$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet= New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```

### <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

Für Azure AD DS wird eine Netzwerksicherheitsgruppe benötigt, um die Ports zu schützen, die für die verwaltete Domäne erforderlich sind, und den gesamten anderen Datenverkehr in eingehender Richtung zu blockieren. Eine [Netzwerksicherheitsgruppe (NSG)][nsg-overview] enthält eine Liste von Regeln, die den Netzwerkdatenverkehr für den Datenverkehr in einem virtuellen Azure-Netzwerk gestatten oder ablehnen. In Azure AD DS fungiert diese Netzwerksicherheitsgruppe als zusätzliche Schutzschicht zum Sperren des Zugriffs auf die verwaltete Domäne. Informationen zu den erforderlichen Ports finden Sie unter [Netzwerksicherheitsgruppen und erforderliche Ports][network-ports].

Die folgenden PowerShell-Cmdlets verwenden [New-AzNetworkSecurityRuleConfig][New-AzNetworkSecurityRuleConfig], um die Regeln zu erstellen, und dann [New-AzNetworkSecurityGroup][New-AzNetworkSecurityGroup], um die Netzwerksicherheitsgruppe zu erstellen. Die Netzwerksicherheitsgruppe und die Regeln werden dann mit dem Cmdlet [Set-AzVirtualNetworkSubnetConfig][Set-AzVirtualNetworkSubnetConfig] dem Subnetz des virtuellen Netzwerks zugeordnet.

```powershell
$NSGName = "aaddsNSG"

# Create a rule to allow inbound TCP port 443 traffic for synchronization with Azure AD
$nsg101 = New-AzNetworkSecurityRuleConfig `
    -Name AllowSyncWithAzureAD `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 101 `
    -SourceAddressPrefix AzureActiveDirectoryDomainServices `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443

# Create a rule to allow inbound TCP port 3389 traffic from Microsoft secure access workstations for troubleshooting
$nsg201 = New-AzNetworkSecurityRuleConfig -Name AllowRD `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 201 `
    -SourceAddressPrefix CorpNetSaw `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389

# Create a rule to allow TCP port 5986 traffic for PowerShell remote management
$nsg301 = New-AzNetworkSecurityRuleConfig -Name AllowPSRemoting `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 301 `
    -SourceAddressPrefix AzureActiveDirectoryDomainServices `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 5986

# Create the network security group and rules
$nsg = New-AzNetworkSecurityGroup -Name $NSGName `
    -ResourceGroupName $ResourceGroupName `
    -Location $AzureLocation `
    -SecurityRules $nsg101,$nsg201,$nsg301

# Get the existing virtual network resource objects and information
$vnet = Get-AzVirtualNetwork -Name $VnetName -ResourceGroupName $ResourceGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $SubnetName
$addressPrefix = $subnet.AddressPrefix

# Associate the network security group with the virtual network subnet
Set-AzVirtualNetworkSubnetConfig -Name $SubnetName `
    -VirtualNetwork $vnet `
    -AddressPrefix $addressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

## <a name="create-a-managed-domain"></a>Erstellen einer verwalteten Domäne

Jetzt erstellen wir eine verwaltete Domäne. Legen Sie Ihre Azure-Abonnement-ID fest, und geben Sie dann einen Namen für die verwaltete Domäne (z. B. *aaddscontoso.com*) an. Die ID Ihres Abonnements können Sie mithilfe des Cmdlets [Get-AzSubscription][Get-AzSubscription] abrufen.

Wenn Sie eine Region mit Unterstützung von Verfügbarkeitszonen auswählen, werden die Azure AD DS-Ressourcen auf mehrere Zonen verteilt, um zusätzliche Redundanz zu erzielen.

Verfügbarkeitszonen sind eindeutige physische Standorte in einer Azure-Region. Jede Zone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkbetrieb unabhängig funktionieren. Zur Gewährleistung der Resilienz sind in allen aktivierten Regionen mindestens drei separate Zonen vorhanden.

Für die Verteilung auf Zonen für Azure AD DS fällt für Sie kein Konfigurationsaufwand an. Die Verteilung der Ressourcen auf Zonen wird von der Azure-Plattform automatisch durchgeführt. Weitere Informationen, z. B. zur regionalen Verfügbarkeit, finden Sie unter [Was sind Verfügbarkeitszonen in Azure?][availability-zones].

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -ApiVersion "2017-06-01" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

Es dauert einige Minuten, bis die Ressource erstellt und die Steuerung an die PowerShell-Eingabeaufforderung zurückgegeben wird. Die verwaltete Domäne wird weiterhin im Hintergrund bereitgestellt, und es kann bis zu einer Stunde dauern, bis die Bereitstellung abgeschlossen ist. In dieser Bereitstellungsphase wird im Azure-Portal auf der Seite **Übersicht** für Ihre verwaltete Domäne der aktuelle Status angezeigt.

Wenn im Azure-Portal angezeigt wird, dass die Bereitstellung der verwalteten Domäne abgeschlossen ist, müssen Sie die folgenden Aufgaben ausführen:

* Aktualisieren Sie DNS-Einstellungen für das virtuelle Netzwerk, damit virtuelle Computer die verwaltete Domäne für den Domänenbeitritt oder die Domänenauthentifizierung finden können.
    * Wählen Sie zum Konfigurieren von DNS Ihre verwaltete Domäne im Portal aus. Im Fenster **Übersicht** werden Sie aufgefordert, diese DNS-Einstellungen automatisch zu konfigurieren.
* [Aktivieren Sie die Kennwortsynchronisierung für Azure AD DS](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds), damit sich Endbenutzer mit ihren Unternehmensanmeldeinformationen bei der verwalteten Domäne anmelden können.

## <a name="complete-powershell-script"></a>Vollständiges PowerShell-Skript

Mit dem folgenden vollständigen PowerShell-Skript werden alle in diesem Artikel gezeigten Aufgaben kombiniert. Kopieren Sie das Skript, und speichern Sie es in einer Datei mit der Erweiterung „`.ps1`“. Führen Sie das Skript in einer lokalen PowerShell-Konsole oder in der [Azure Cloud Shell][cloud-shell] aus.

> [!NOTE]
> Um Azure AD DS aktivieren zu können, müssen Sie ein globaler Administrator für den Azure AD-Mandanten sein. Außerdem benötigen Sie mindestens die Berechtigung *Mitwirkender* im Azure-Abonnement.

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso.onmicrosoft.com"
$ResourceGroupName = "myResourceGroup"
$VnetName = "myVnet"
$AzureLocation = "westus"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Connect-AzAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "6ba9a5d4-8456-4118-b521-9c5ca10cdf84"

# First, retrieve the object ID of the 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Create the delegated administration group for Azure AD Domain Services if it doesn't already exist.
if (!$GroupObjectId) {
  $GroupObjectId = New-AzureADGroup -DisplayName "AAD DC Administrators" `
    -Description "Delegated group to administer Azure AD Domain Services" `
    -SecurityEnabled $true `
    -MailEnabled $false `
    -MailNickName "AADDCAdministrators"
  }
else {
  Write-Output "Admin group already exists."
}

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq '$AaddsAdminUserUpn'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId

# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$SubnetName = "DomainServices"
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
  
$NSGName = "aaddsNSG"

# Create a rule to allow inbound TCP port 443 traffic for synchronization with Azure AD
$nsg101 = New-AzNetworkSecurityRuleConfig `
    -Name AllowSyncWithAzureAD `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 101 `
    -SourceAddressPrefix AzureActiveDirectoryDomainServices `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443

# Create a rule to allow inbound TCP port 3389 traffic from Microsoft secure access workstations for troubleshooting
$nsg201 = New-AzNetworkSecurityRuleConfig -Name AllowRD `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 201 `
    -SourceAddressPrefix CorpNetSaw `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389

# Create a rule to allow TCP port 5986 traffic for PowerShell remote management
$nsg301 = New-AzNetworkSecurityRuleConfig -Name AllowPSRemoting `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 301 `
    -SourceAddressPrefix AzureActiveDirectoryDomainServices `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 5986

# Create the network security group and rules
$nsg = New-AzNetworkSecurityGroup -Name $NSGName `
    -ResourceGroupName $ResourceGroupName `
    -Location $AzureLocation `
    -SecurityRules $nsg101,$nsg201,$nsg301

# Get the existing virtual network resource objects and information
$vnet = Get-AzVirtualNetwork -Name $VnetName -ResourceGroupName $ResourceGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $SubnetName
$addressPrefix = $subnet.AddressPrefix

# Associate the network security group with the virtual network subnet
Set-AzVirtualNetworkSubnetConfig -Name $SubnetName `
    -VirtualNetwork $vnet `
    -AddressPrefix $addressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -ApiVersion "2017-06-01" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

Es dauert einige Minuten, bis die Ressource erstellt und die Steuerung an die PowerShell-Eingabeaufforderung zurückgegeben wird. Die verwaltete Domäne wird weiterhin im Hintergrund bereitgestellt, und es kann bis zu einer Stunde dauern, bis die Bereitstellung abgeschlossen ist. In dieser Bereitstellungsphase wird im Azure-Portal auf der Seite **Übersicht** für Ihre verwaltete Domäne der aktuelle Status angezeigt.

Wenn im Azure-Portal angezeigt wird, dass die Bereitstellung der verwalteten Domäne abgeschlossen ist, müssen Sie die folgenden Aufgaben ausführen:

* Aktualisieren Sie DNS-Einstellungen für das virtuelle Netzwerk, damit virtuelle Computer die verwaltete Domäne für den Domänenbeitritt oder die Domänenauthentifizierung finden können.
    * Wählen Sie zum Konfigurieren von DNS Ihre verwaltete Domäne im Portal aus. Im Fenster **Übersicht** werden Sie aufgefordert, diese DNS-Einstellungen automatisch zu konfigurieren.
* [Aktivieren Sie die Kennwortsynchronisierung für Azure AD DS](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds), damit sich Endbenutzer mit ihren Unternehmensanmeldeinformationen bei der verwalteten Domäne anmelden können.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie die verwaltete Domäne in Aktion sehen möchten, können Sie [einen virtuellen Windows-Computer in die Domäne einbinden][windows-join], [sicheres LDAP konfigurieren][tutorial-ldaps] und die [Kennworthashsynchronisierung konfigurieren][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md
[nsg-overview]: ../virtual-network/network-security-groups-overview.md
[network-ports]: network-considerations.md#network-security-groups-and-required-ports

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[New-AzVirtualNetworkSubnetConfig]: /powershell/module/Az.Network/New-AzVirtualNetworkSubnetConfig
[New-AzVirtualNetwork]: /powershell/module/Az.Network/New-AzVirtualNetwork
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: ../cloud-shell/cloud-shell-windows-users.md
[availability-zones]: ../availability-zones/az-overview.md
[New-AzNetworkSecurityRuleConfig]: /powershell/module/az.network/new-aznetworksecurityruleconfig
[New-AzNetworkSecurityGroup]: /powershell/module/az.network/new-aznetworksecuritygroup
[Set-AzVirtualNetworkSubnetConfig]: /powershell/module/az.network/set-azvirtualnetworksubnetconfig

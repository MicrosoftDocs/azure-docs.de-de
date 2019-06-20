---
title: Aktivieren von Azure Active Directory Domain Services mithilfe von PowerShell | Microsoft-Dokumentation
description: Aktivieren von Azure Active Directory Domain Services mithilfe von PowerShell
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: ergreenl
ms.openlocfilehash: f2c4f73af00e0093ce98f2de37e9c3a0ba381eda
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245425"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Aktivieren von Azure Active Directory Domain Services mithilfe von PowerShell
In diesem Artikel wird gezeigt, wie Sie Azure Active Directory (AD) Domain Services mithilfe von PowerShell aktivieren.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="task-1-install-the-required-powershell-modules"></a>Aufgabe 1: Installieren der erforderlichen PowerShell-Module

### <a name="install-and-configure-azure-ad-powershell"></a>Installieren und Konfigurieren von Azure AD PowerShell
Folgen Sie den Anweisungen im Artikel zum [Installieren des Azure AD PowerShell-Moduls und Herstellen einer Verbindung mit Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Installieren und Konfigurieren von Azure PowerShell
Folgen Sie den Anweisungen im Artikel zum [Installieren des Azure PowerShell-Moduls und Herstellen einer Verbindung mit Ihrem Azure-Abonnement](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="task-2-create-the-required-service-principal-in-your-azure-ad-directory"></a>Aufgabe 2: Erstellen des erforderlichen Dienstprinzipals in Ihrem Azure AD-Verzeichnis
Geben Sie den folgenden PowerShell-Befehl ein, um den für Azure AD Domain Services erforderlichen Dienstprinzipal in Ihrem Azure AD-Verzeichnis zu erstellen.
```powershell
# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

## <a name="task-3-create-and-configure-the-aad-dc-administrators-group"></a>Aufgabe 3: Erstellen und Konfigurieren der Gruppe „AAD DC-Administratoren“
Die nächste Aufgabe besteht darin, die Administratorengruppe zu erstellen, die zum Delegieren von Verwaltungsaufgaben für die verwaltete Domäne verwendet wird.
```powershell
# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Nachdem Sie die Gruppe erstellt haben, fügen Sie einige Benutzer zur Gruppe hinzu.
```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso100.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="task-4-register-the-azure-ad-domain-services-resource-provider"></a>Aufgabe 4: Registrieren des Ressourcenanbieters für Azure AD Domain Services
Geben Sie den folgenden PowerShell-Befehl ein, um den Ressourcenanbieter für Azure AD Domain Services zu registrieren:
```powershell
# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

## <a name="task-5-create-a-resource-group"></a>Aufgabe 5: Erstellen einer Ressourcengruppe
Geben Sie den folgenden PowerShell-Befehl ein, um eine Ressourcengruppe zu erstellen:
```powershell
$ResourceGroupName = "ContosoAaddsRg"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Sie können das virtuelle Netzwerk und die durch Azure AD Domain Services verwaltete Domäne in dieser Ressourcengruppe erstellen.


## <a name="task-6-create-and-configure-the-virtual-network"></a>Aufgabe 6: Erstellen und Konfigurieren des virtuellen Netzwerks
Erstellen Sie nun das virtuelle Netzwerk, in dem Sie Azure AD Domain Services aktivieren. Stellen Sie sicher, dass Sie ein dediziertes Subnetz für Azure AD Domain Services erstellen. Stellen Sie in diesem dedizierten Subnetz keine Workload-VMs bereit.

Geben Sie die folgenden PowerShell-Befehle ein, um ein virtuelles Netzwerk mit einem dedizierten Subnetz für Azure AD Domain Services zu erstellen.

```powershell
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```


## <a name="task-7-provision-the-azure-ad-domain-services-managed-domain"></a>Aufgabe 7: Bereitstellen der durch Azure AD Domain Services verwalteten Domäne
Geben Sie den folgenden PowerShell-Befehl ein, um Azure AD Domain Services für Ihr Verzeichnis zu aktivieren:

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso100.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **Vergessen Sie nicht die zusätzlichen Konfigurationsschritte nach der Bereitstellung der verwalteten Domäne.**
> Nachdem die verwaltete Domäne bereitgestellt wurde, müssen Sie noch die folgenden Aufgaben ausführen:
> * **[Aktualisieren Sie DNS-Einstellungen](active-directory-ds-getting-started-dns.md)** für das virtuelle Netzwerk, damit virtuelle Computer die verwaltete Domäne für den Domänenbeitritt oder die Domänenauthentifizierung finden können.
> * **[Aktivieren Sie die Kennwortsynchronisierung für Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md)** , damit sich Endbenutzer mit ihren Unternehmensanmeldeinformationen an der verwalteten Domäne anmelden können.


## <a name="powershell-script"></a>PowerShell-Skript
Nachfolgend sehen Sie das PowerShell-Skript, mit dem alle Aufgaben ausgeführt werden, die in diesem Artikel aufgelistet sind. Kopieren Sie das Skript, und speichern Sie es in einer Datei mit der Erweiterung „.ps1“. Führen Sie das Skript in PowerShell oder mithilfe der PowerShell Integrated Scripting Environment (ISE) aus.

> [!NOTE]
> **Erforderliche Berechtigungen zum Ausführen dieses Skripts** Zum Aktivieren von Azure AD Domain Services müssen Sie der globale Administrator für das Azure AD-Verzeichnis sein. Darüber hinaus müssen Sie mindestens über die Berechtigungen als „Mitwirkender“ für das virtuelle Netzwerk verfügen, in dem Sie Azure AD Domain Services aktivieren.
>

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso100.onmicrosoft.com"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso100.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Connect-AzAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"

# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"

# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

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

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **Vergessen Sie nicht die zusätzlichen Konfigurationsschritte nach der Bereitstellung der verwalteten Domäne.**
> Nachdem die verwaltete Domäne bereitgestellt wurde, müssen Sie noch die folgenden Aufgaben ausführen:
> * Aktualisieren Sie DNS-Einstellungen für das virtuelle Netzwerk, damit virtuelle Computer die verwaltete Domäne für den Domänenbeitritt oder die Domänenauthentifizierung finden können.
> * Aktivieren Sie die Kennwortsynchronisierung für Azure AD Domain Services, damit sich Endbenutzer mit ihren Unternehmensanmeldeinformationen an der verwalteten Domäne anmelden können.

## <a name="next-steps"></a>Nächste Schritte
Nachdem die verwaltete Domäne erstellt wurde, führen Sie die folgenden Konfigurationsaufgaben aus, damit Sie die verwaltete Domäne verwenden können:

* [Aktualisieren der DNS-Servereinstellungen für das virtuelle Netzwerk zum Verweisen auf die verwaltete Domäne](active-directory-ds-getting-started-dns.md)
* [Aktivieren der Kennwortsynchronisierung für die verwaltete Domäne](active-directory-ds-getting-started-password-sync.md)

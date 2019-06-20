---
title: Ändern des Lizenzierungsmodells für eine SQL Server-VM in Azure | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie die Lizenzierung für einen virtuellen SQL-Computer in Azure wechseln.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 3f62557d024f56b7014784b6956f15a950f8cca7
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926254"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Ändern des Lizenzierungsmodells für eine SQL Server-VM in Azure
In diesem Artikel wird beschrieben, wie Sie das Lizenzierungsmodell für einen virtuellen SQL Server-Computer in Azure ändern, indem Sie den neuen SQL-VM-Ressourcenanbieter **Microsoft.SqlVirtualMachine** verwenden. Es gibt zwei Lizenzierungsmodelle für einen virtuellen Computer (VM), der SQL Server hostet: nutzungsbasierte Bezahlung und Bring Your Own License (BYOL). Mit dem Azure-Portal, Azure CLI oder PowerShell können Sie jetzt ändern, welches Lizenzierungsmodell von Ihrer SQL Server-VM genutzt wird. 

**Nutzungsbasierte Bezahlung** bedeutet, dass die sekundenbasierten Kosten für die Ausführung des virtuellen Azure-Computers die Kosten für die SQL Server-Lizenz enthalten.

Das Modell **Bring Your Own License** (BYOL) wird auch als [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/) bezeichnet und ermöglicht Ihnen die Verwendung Ihrer eigenen SQL Server-Lizenz mit einer VM, auf der SQL Server ausgeführt wird. Weitere Informationen zu Preisen finden Sie unter [Preisinformationen für virtuelle Azure-Computer mit SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Beim Umschalten zwischen den beiden Lizenzierungsmodellen fällt **keine Ausfallzeit** an, die VM wird nicht neu gestartet, es ergeben sich **keine weiteren Kosten** (sondern durch das Aktivieren des Azure-Hybridvorteils werden die Kosten *reduziert*), und das andere Modell ist **sofort wirksam**. 

## <a name="remarks"></a>Anmerkungen


 - CSP-Kunden können den AHB-Vorteil nutzen, indem Sie zuerst eine VM mit nutzungsbasierter Bezahlung bereitstellen und diese dann in Bring-Your-Own-License umwandeln. 
 - Geben Sie beim Registrieren eines benutzerdefinierten SQL Server-VM-Images beim Ressourcenanbieter den Lizenztyp als ‚AHUB‘ an. Wenn Sie die Angabe zum Lizenztyp leer lassen oder den Wert ‚PAYG‘ eingeben, tritt bei der Registrierung ein Fehler auf. 
 - Wenn Sie Ihre SQL Server-VM-Ressource verwerfen, kehren Sie wieder zur hartcodierten Lizenzeinstellung des Images zurück. 
 - Beim Hinzufügen einer SQL Server-VM zu einer Verfügbarkeitsgruppe muss die VM erneut erstellt werden. Daher kehren alle VMs, die einer Verfügbarkeitsgruppe hinzugefügt werden, zum Standardlizenztyp „Nutzungsbasierte Bezahlung“ zurück, und AHB muss erneut aktiviert werden. 
 - Die Möglichkeit, das Lizenzierungsmodell zu ändern, ist ein Feature des SQL-VM-Ressourcenanbieters. Wird ein Marketplace-Image über das Azure-Portal bereitgestellt, wird automatisch eine SQL Server-VM mit dem Ressourcenanbieter registriert. Kunden, die SQL Server selbst installieren, müssen [ihre SQL Server-VM jedoch manuell registrieren](#register-sql-server-vm-with-the-sql-vm-resource-provider). 
 

 
## <a name="limitations"></a>Einschränkungen

 - Die Möglichkeit, das Lizenzierungsmodell umzuwandeln, ist derzeit nur verfügbar, wenn Sie mit einem SQL Server-VM-Image mit nutzungsbasierter Bezahlung beginnen. Wenn Sie mit einem Bring-Your-Own-License-Image aus dem Portal beginnen, können Sie dieses Image nicht in den nutzungsbasierten Bezahlmodus umwandeln.
 - Ändern des Lizenzierungsmodells wird nur für virtuelle Computer unterstützt, die mit dem Resource Manager-Modell bereitgestellt wurden. Virtuelle Computer, die mit dem klassischen Modell bereitgestellt wurden, werden nicht unterstützt. 
 - Ändern des Lizenzierungsmodells ist nur für Installationen in der öffentlichen Cloud aktiviert.
 - Ändern des Lizenzierungsmodells wird nur auf virtuellen Computern unterstützt, die eine einzige NIC (Netzwerkkarte) haben. Auf virtuellen Computern, die mehrere Netzwerkkarten haben, müssen Sie zunächst eine der Karten (über das Azure-Portal) entfernen, bevor Sie die Vorgehensweise versuchen. Andernfalls wird ein Fehler auftreten, der in etwa wie folgt aussieht: `The virtual machine '\<vmname\>' has more than one NIC associated.` Obwohl Sie die Netzwerkkarte dem virtuellen Computer möglicherweise wieder hinzufügen können, nachdem Sie den Lizenzierungsmodus geändert haben, werden Vorgänge, die über das SQL-Konfigurationsblatt ausgeführt werden, z. B. automatisches Patching und automatische Sicherung, nicht mehr unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

Für die Verwendung des SQL-VM-Ressourcenanbieters ist die SQL-IaaS-Erweiterung erforderlich. Um den SQL-VM-Ressourcenanbieter verwenden zu können, benötigen Sie daher Folgendes:
- Ein [Azure-Abonnement](https://azure.microsoft.com/free/).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- Eine [SQL Server-VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) mit *nutzungsbasierter Bezahlung* und mit installierter [SQL-IaaS-Erweiterung](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension). 

## <a name="with-the-azure-portal"></a>Über das Azure-Portal

Sie können das Lizenzierungsmodell direkt über das Portal ändern. 

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer SQL Server-VM. 
1. Wählen Sie **SQL Server-Konfiguration** im Bereich **Einstellungen** aus. 
1. Wählen Sie **Bearbeiten** im Bereich **SQL Server-Lizenz** aus, um die Lizenz zu ändern. 

![AHB im Portal](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)

  >[!NOTE]
  > Diese Option ist nicht für Bring-Your-Own-License-Images verfügbar. 

## <a name="with-azure-cli"></a>Mit der Azure-Befehlszeilenschnittstelle

Sie können die Azure CLI (Azure-Befehlszeilenschnittstelle) nutzen, um das Lizenzierungsmodell zu wechseln.  

Mit dem folgenden Codeausschnitt wird Ihr Lizenzierungsmodell von der nutzungsbasierten Bezahlung in BYOL (bzw. die Verwendung des Azure-Hybridvorteils) geändert:

```azurecli
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

Mit dem folgenden Codeausschnitt wird Ihr Bring-Your-Own-License-Modell in die nutzungsbasierte Bezahlung geändert: 

```azurecli
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```
## <a name="with-powershell"></a>Mit PowerShell
Sie können Ihr Lizenzierungsmodell auch mit PowerShell ändern.

Mit dem folgenden Codeausschnitt wird Ihr Lizenzierungsmodell von der nutzungsbasierten Bezahlung in BYOL (bzw. die Verwendung des Azure-Hybridvorteils) geändert:

```powershell
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

Mit dem folgenden Codeausschnitt wird Ihr BYOL-Modell in die nutzungsbasierte Bezahlung geändert:

```powershell
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```


## <a name="register-sql-server-vm-with-the-sql-vm-resource-provider"></a>Registrieren der SQL Server-VM beim SQL-VM-Ressourcenanbieter
In bestimmten Situationen müssen Sie Ihre SQL Server-VM möglicherweise manuell beim SQL-VM-Ressourcenanbieter registrieren. Dazu kann es auch erforderlich sein, dass Sie den Ressourcenanbieter manuell bei Ihrem Abonnement registrieren. 


### <a name="register-sql-vm-resource-provider-with-subscription"></a>Registrieren des SQL-VM-Ressourcenanbieters beim Abonnement 

Um Ihre SQL Server-VM beim SQL-Ressourcenanbieter zu registrieren, müssen Sie den Ressourcenanbieter bei Ihrem Abonnement registrieren. Sie können dazu das Azure-Portal oder die Azure-Befehlszeilenschnittstelle verwenden. 

#### <a name="with-the-azure-portal"></a>Über das Azure-Portal
Die folgenden Schritte registriert den SQL-Ressourcenanbieter bei Ihrem Azure-Abonnement mithilfe des Azure-Portals. 

1. Öffnen Sie das Azure-Portal, und navigieren Sie zu **Alle Dienste**. 
1. Navigieren Sie zu **Abonnements**, und wählen Sie das gewünschte Abonnement aus.  
1. Navigieren Sie auf dem Blatt **Abonnements** zu **Ressourcenanbieter**. 
1. Geben Sie im Filter `sql` ein, um die SQL-bezogenen Ressourcenanbieter aufzurufen. 
1. Wählen Sie je nach gewünschter Aktion für den Anbieter **Microsoft.SqlVirtualMachine** entweder *Registrieren*, *Erneut registrieren* oder *Registrierung aufheben*. 

   ![Ändern des Anbieters](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

#### <a name="with-azure-cli"></a>Mit der Azure-Befehlszeilenschnittstelle
Der folgende Codeausschnitt registriert den SQL-VM-Ressourcenanbieter bei Ihrem Azure-Abonnement. 

```azurecli
# Register the new SQL resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

#### <a name="with-powershell"></a>Mit PowerShell

Der folgende Codeausschnitt registriert den SQL-Ressourcenanbieter bei Ihrem Azure-Abonnement.

```powershell
# Register the new SQL resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

### <a name="register-sql-server-vm-with-sql-resource-provider"></a>Registrieren der SQL Server-VM beim SQL-Ressourcenanbieter
Sobald der SQL-VM-Ressourcenanbieter bei Ihrem Abonnement registriert ist, können Sie Ihre SQL Server-VM über die Azure-Befehlszeilenschnittstelle beim Ressourcenanbieter registrieren. 

#### <a name="with-azure-cli"></a>Mit der Azure-Befehlszeilenschnittstelle
Registrieren Sie die SQL Server-VM über die Azure-Befehlszeilenschnittstelle mit dem folgenden Codeausschnitt: 

```azurecli
# Register your existing SQL Server VM with the new resource provider
az sql vm create -n <VMName> -g <ResourceGroupName> -l <VMLocation>
```
#### <a name="with-powershell"></a>Mit PowerShell
Registrieren Sie die SQL Server-VM über PowerShell mit dem folgenden Codeausschnitt:

```powershell
# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```



## <a name="known-errors"></a>Bekannte Fehler

### <a name="sql-iaas-extension-is-not-installed-on-virtual-machine"></a>Auf dem virtuellen Computer ist die SQL-IaaS-Erweiterung nicht installiert.
Die SQL-IaaS-Erweiterung ist eine notwendige Voraussetzung für die Registrierung der SQL Server-VM beim SQL-VM-Ressourcenanbieter. Wenn Sie versuchen, die SQL Server-VM zu registrieren, bevor Sie die SQL-IaaS-Erweiterung installiert haben, tritt der folgende Fehler auf:

`Sql IaaS Extension is not installed on Virtual Machine: '{0}'. Please make sure it is installed and in running state and try again later.`

Um dieses Problem zu beheben, installieren Sie die SQL-IaaS-Erweiterung, bevor Sie die SQL Server-VM registrieren. 

  > [!NOTE]
  > Beim Installieren der SQL-IaaS-Erweiterung wird der SQL Server-Dienst neu gestartet, daher sollte dies nur während eines Wartungsfensters erfolgen. Weitere Informationen finden Sie in der Beschreibung der [Installation der SQL-IaaS-Erweiterung](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension#installation). 


### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>Die Ressource „Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<Ressourcengruppe>“ unter der Ressourcengruppe „\<Ressourcengruppe>“ wurde nicht gefunden. Die sqlServerLicenseType-Eigenschaft kann in diesem Objekt nicht gefunden werden. Vergewissern Sie sich, dass die Eigenschaft vorhanden ist und festgelegt werden kann.
Dieser Fehler tritt auf, wenn Sie versuchen, das Lizenzierungsmodell auf einer SQL Server-VM zu ändern, die nicht beim SQL-Ressourcenanbieter registriert wurde. Sie müssen den Ressourcenanbieter bei Ihrem [Abonnement](#register-sql-vm-resource-provider-with-subscription) und dann Ihre SQL Server-VM beim SQL-[Ressourcenanbieter](#register-sql-server-vm-with-sql-resource-provider) registrieren. 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Argument für „Sku“-Parameter kann nicht überprüft werden.
Dieser Fehler kann auftreten, wenn Sie versuchen, das Lizenzierungsmodell für Ihre SQL Server-VM bei Verwendung von Azure PowerShell in einer Version über 4.0 zu ändern: Set-AzResource : Das Argument für den Parameter "Sku" kann nicht überprüft werden. Das Argument ist NULL oder leer. Geben Sie ein Argument an, das nicht NULL oder leer ist, und führen Sie den Befehl erneut aus.
Um diesen Fehler zu beheben, heben Sie die Auskommentierung dieser Zeilen im zuvor genannten PowerShell-Codeausschnitt auf, wenn Sie das Lizenzierungsmodell wechseln:

```powershell
# the following code snippet is necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
```

Verwenden Sie den folgenden Code, um Ihre Azure PowerShell-Version zu überprüfen:

```powershell
Get-Module -ListAvailable -Name Azure -Refresh
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln: 

* [Übersicht über SQL Server auf einem virtuellen Windows-Computer](virtual-machines-windows-sql-server-iaas-overview.md)
* [Häufig gestellte Fragen zu SQL Server auf virtuellen Windows-Computern in Azure](virtual-machines-windows-sql-server-iaas-faq.md)
* [Preisinformationen für Azure-VMs mit SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server on Azure Virtual Machine release notes](virtual-machines-windows-sql-server-iaas-release-notes.md) (SQL Server auf virtuellem Azure-Computer – Versionshinweise)



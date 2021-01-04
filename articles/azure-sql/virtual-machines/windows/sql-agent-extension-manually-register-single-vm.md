---
title: Registrieren eines virtuellen Computers mit der SQL-IaaS-Agent-Erweiterung
description: Registrieren Sie Ihren virtuellen Azure SQL Server-Computer mit der SQL-IaaS-Agent-Erweiterung, um Features für virtuelle SQL Server-Computer zu aktivieren, die außerhalb von Azure Marketplace bereitgestellt werden, und um Compliance und eine einfachere Verwaltung zu gewährleisten.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell, contperf-fy21q2
ms.openlocfilehash: e7a8f54abbadb63c870c4d92843699c67f59752c
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505629"
---
# <a name="register-sql-server-vm-with-sql-iaas-agent-extension"></a>Registrieren einer SQL Server-VM mit der SQL-IaaS-Agent-Erweiterung
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Wenn Sie Ihre SQL Server-VM mit der [SQL-IaaS-Agent-Erweiterung](sql-server-iaas-agent-extension-automate-management.md) registrieren, können Sie viele Features für SQL Server auf Azure-VMs nutzen. 

In diesem Artikel erfahren Sie, wie Sie eine einzelne SQL Server-VM mit der SQL-IaaS-Agent-Erweiterung registrieren. Alternativ können Sie alle SQL Server-VMs [automatisch](sql-agent-extension-automatic-registration-all-vms.md) oder [mehrere VMs per Skript in einem Massenvorgang](sql-agent-extension-manually-register-vms-bulk.md) registrieren.


## <a name="overview"></a>Übersicht

Durch die Registrierung mit der [SQL Server-IaaS-Agent-Erweiterung](sql-server-iaas-agent-extension-automate-management.md) wird die _Ressource_ **Virtueller SQL-Computer** in Ihrem Abonnement erstellt. Dabei handelt es sich um eine _andere_ Ressource als die Ressource für Ihren virtuellen Computer. Wenn Sie die Registrierung der Erweiterung für Ihre SQL Server-VM aufheben, wird die _Ressource_ **Virtueller SQL-Computer** entfernt, während der tatsächliche virtuelle Computer jedoch erhalten bleibt.

Beim Bereitstellen eines Azure Marketplace-Images einer SQL Server-VM über das Azure-Portal wird die SQL Server-VM mit der Erweiterung automatisch registriert. Wenn Sie jedoch SQL Server auf einer Azure-VM selbst installieren oder eine Azure-VM von einer benutzerdefinierten VHD bereitstellen, müssen Sie Ihre SQL Server-VM mit der SQL-IaaS-Agent-Erweiterung registrieren, um alle Featurevorteile nutzen und von einer einfachen Verwaltbarkeit profitieren zu können. 

Wenn Sie die SQL-IaaS-Agent-Erweiterung verwenden möchten, müssen Sie zunächst [Ihr Abonnement beim Anbieter **Microsoft.SqlVirtualMachine** registrieren](#register-subscription-with-rp). Dadurch kann die SQL-IaaS-Agent-Erweiterung Ressourcen in diesem bestimmten Abonnement erstellen.

> [!IMPORTANT]
> Die SQL-IaaS-Agent-Erweiterung sammelt Daten ausschließlich, um Kunden zu ermöglichen, bei der Verwendung von SQL Server in Azure Virtual Machines optionale Vorteile zu nutzen. Microsoft verwendet diese Daten ohne vorherige Zustimmung des Kunden nicht für Lizenzierungsüberprüfungen. Weitere Informationen finden Sie unter [Ergänzende Datenschutzbestimmungen zu SQL Server](/sql/sql-server/sql-server-privacy#non-personal-data).

## <a name="prerequisites"></a>Voraussetzungen

Um Ihre SQL Server-VM mit der Erweiterung registrieren zu können, benötigen Sie Folgendes: 

- Ein [Azure-Abonnement](https://azure.microsoft.com/free/).
- Ein in der öffentlichen Cloud oder Azure Government-Cloud bereitgestelltes Azure-Ressourcenmodell des Typs [Windows Server 2008-VM (oder höher)](../../../virtual-machines/windows/quick-create-portal.md) mit [SQL Server 2008 (oder höher)](https://www.microsoft.com/sql-server/sql-server-downloads). 
- Die aktuelle Version von [Azure CLI](/cli/azure/install-azure-cli) oder [Azure PowerShell (mindestens 5.0)](/powershell/azure/install-az-ps). 


## <a name="register-subscription-with-rp"></a>Registrieren eines Abonnements mit RP

Um Ihre SQL Server-VM mit der SQL-IaaS-Agent-Erweiterung registrieren zu können, müssen Sie zunächst Ihr Abonnement beim Anbieter **Microsoft.SqlVirtualMachine** registrieren. Dadurch kann die SQL-IaaS-Agent-Erweiterung Ressourcen in Ihrem Abonnement erstellen.  Verwenden Sie hierzu das Azure-Portal, die Azure CLI oder Azure PowerShell.

### <a name="azure-portal"></a>Azure-Portal

1. Öffnen Sie das Azure-Portal, und wechseln Sie zu **Alle Dienste**. 
1. Wechseln Sie zu **Abonnements**, und wählen Sie das gewünschte Abonnement aus.  
1. Navigieren Sie auf der Seite **Abonnements** zu **Erweiterungen**. 
1. Geben Sie im Filter die Zeichenfolge **sql** ein, um die SQL-bezogenen Erweiterungen aufzurufen. 
1. Wählen Sie je nach gewünschter Aktion für den Anbieter **Microsoft.SqlVirtualMachine** eine der Optionen **Registrieren**, **Erneut registrieren** oder **Registrierung aufheben** aus. 

   ![Ändern des Anbieters](./media/sql-agent-extension-manually-register-single-vm/select-resource-provider-sql.png)


### <a name="command-line"></a>Befehlszeile

Registrieren Sie Ihr Azure-Abonnement beim Anbieter **Microsoft.SqlVirtualMachine** entweder mithilfe der Azure-Befehlszeilenschnittstelle oder mit Azure PowerShell. 

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/bash)

```azurecli-interactive
# Register the SQL IaaS Agent extension to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL IaaS Agent extension to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-extension"></a>Registrieren der Erweiterung

Für die [SQL Server-IaaS-Agent-Erweiterung](sql-server-iaas-agent-extension-automate-management.md#management-modes) gibt es drei Verwaltungsmodi. 

Beim Registrieren der Erweiterung im Verwaltungsmodus „Vollständig“ wird der SQL Server-Dienst neu gestartet. Es wird daher empfohlen, die Erweiterung zunächst im Modus „Lightweight“ zu registrieren und sie dann in einem Wartungsfenster [auf „Vollständig“ zu aktualisieren](#upgrade-to-full). 

### <a name="lightweight-management-mode"></a>Verwaltungsmodus „Lightweight“

Verwenden Sie die Azure-Befehlszeilenschnittstelle oder Azure PowerShell, um Ihre SQL Server-VM mit der Erweiterung im Modus „Lightweight“ zu registrieren. Dadurch wird der SQL Server-Dienst nicht neu gestartet. Sie können dann jederzeit ein Upgrade auf den Modus „Vollständig“ ausführen. Da hierdurch jedoch der SQL Server-Dienst neu gestartet wird, wird empfohlen, auf ein Fenster für die geplante Wartung zu warten. 

Geben Sie den SQL Server-Lizenztyp an. Sie können zwischen nutzungsbasierter Bezahlung (`PAYG`), Azure-Hybridvorteil (`AHUB`) zum Verwenden Ihrer eigenen Lizenz und Notfallwiederherstellung (`DR`) zum Aktivieren des [kostenlosen DR-Replikats](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) auswählen.

Failoverclusterinstanzen und Bereitstellungen mit mehreren Instanzen können nur mit der SQL-IaaS-Agent-Erweiterung im Modus „Lightweight“ registriert werden. 

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/bash)

Registrieren einer SQL Server-VM im Modus „Lightweight“ mit der Azure CLI: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type <license_type> 
  ```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Registrieren einer SQL Server-VM im Modus „Lightweight“ mit Azure PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license_type>  -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Verwaltungsmodus „Vollständig“

Beim Registrieren Ihrer SQL Server-VM im Modus „Vollständig“ wird der SQL Server-Dienst neu gestartet. Sie sollten daher mit Vorsicht vorgehen. 

Verwenden Sie den folgenden Azure PowerShell-Befehl, um Ihre SQL Server-VM direkt im Modus „Vollständig“ zu registrieren (und ggf. den SQL Server-Dienst neu zu starten): 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Verwaltungsmodus „NoAgent“ 

Installationen von SQL Server 2008 und SQL Server 2008 R2 unter Windows Server 2008 (_nicht R2_) können mit der SQL-IaaS-Agent-Erweiterung im [Modus „NoAgent“](sql-server-iaas-agent-extension-automate-management.md#management-modes) registriert werden. Diese Option stellt die Konformität sicher und ermöglicht die Überwachung der SQL Server-VM im Azure-Portal mit eingeschränktem Funktionsumfang.


Geben Sie als **Lizenztyp** entweder `AHUB`, `PAYG` oder `DR` an. Geben Sie als **Imageangebot** entweder `SQL2008-WS2008` oder `SQL2008R2-WS2008` an.

Verwenden Sie zum Registrieren Ihrer SQL Server 2008-Instanz (`SQL2008-WS2008`) oder Ihrer SQL Server 2008 R2-Instanz (`SQL2008R2-WS2008`) unter Windows Server 2008 den folgenden Azure CLI- oder Azure PowerShell-Codeausschnitt: 


# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/bash)

Registrieren Ihres virtuellen SQL Server-Computers im Modus „NoAgent“ mit Azure CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type <license type>  --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer <image offer> 
 ```
 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)


Registrieren Ihres virtuellen SQL Server-Computers im Modus „NoAgent“ mit Azure PowerShell: 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license type> -SqlManagementType NoAgent -Sku Standard -Offer <image offer>
  ```

---

## <a name="verify-mode"></a>Überprüfen des Modus

Über Azure PowerShell können Sie den aktuellen Modus des SQL Server-IaaS-Agents anzeigen: 

```powershell-interactive
# Get the SqlVirtualMachine
$sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
$sqlvm.SqlManagementType
```

## <a name="upgrade-to-full"></a>Upgrade auf „Vollständig“  

Für SQL Server-VMs, auf denen die Erweiterung im Modus *Lightweight* registriert wurde, können Sie über das Azure-Portal, die Azure-Befehlszeilenschnittstelle oder Azure PowerShell ein Upgrade auf den Modus _Vollständig_ durchführen. Für SQL Server-VMs im Modus _NoAgent_ kann ein Upgrade auf _Vollständig_ durchgeführt werden, nachdem das Betriebssystem auf Windows 2008 R2 oder höher aktualisiert wurde. Es ist nicht möglich, ein Downgrade auszuführen. Dazu müssen Sie die [Registrierung der SQL-IaaS-Agent-Erweiterung für die SQL Server-VM aufheben](#unregister-from-extension). Dadurch wird die _Ressource_ **Virtueller SQL-Computer** entfernt, der tatsächliche virtuelle Computer aber nicht gelöscht. 


### <a name="azure-portal"></a>Azure-Portal

Führen Sie die folgenden Schritte aus, um die Erweiterung im Azure-Portal auf den Modus „Vollständig“ zu aktualisieren: 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wechseln Sie zu Ihrer Ressource [Virtuelle SQL-Computer](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource). 
1. Wählen Sie Ihre SQL Server-VM aus, und klicken Sie auf **Übersicht**. 
1. Wählen Sie für SQL Server-VMs mit dem IaaS-Modus „NoAgent“ oder „Lightweight“ die Meldung **Mit der SQL-IaaS-Erweiterung sind nur Lizenztyp- und Editionsaktualisierungen verfügbar** aus.

   ![Auswahlmöglichkeiten zum Ändern des Modus im Portal](./media/sql-agent-extension-manually-register-single-vm/change-sql-iaas-mode-portal.png)

1. Aktivieren Sie das Kontrollkästchen **Ich stimme dem Neustart des SQL Server-Diensts auf dem virtuellen Computer** zu, und klicken Sie dann auf **Bestätigen**, um ein Upgrade des IaaS-Modus auf „Vollständig“ durchzuführen. 

    ![Kontrollkästchen für die Zustimmung zum Neustart des SQL Server-Diensts auf dem virtuellen Computer](./media/sql-agent-extension-manually-register-single-vm/enable-full-mode-iaas.png)

### <a name="command-line"></a>Befehlszeile

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/bash)

Führen Sie zum Aktualisieren der Erweiterung auf den Modus „Vollständig“ den folgenden Azure CLI-Code aus:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Führen Sie zum Aktualisieren der Erweiterung auf den Modus „Vollständig“ den folgenden Azure PowerShell-Code aus:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Überprüfen des Registrierungsstatus
Sie können über das Azure-Portal, die Azure-Befehlszeilenschnittstelle oder Azure PowerShell überprüfen, ob Ihre SQL Server-VM bereits mit der SQL-IaaS-Agent-Erweiterung registriert wurde. 

### <a name="azure-portal"></a>Azure-Portal 

Führen Sie die folgenden Schritte aus, um den Registrierungsstatus im Azure-Portal zu überprüfen: 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 
1. Navigieren Sie zu Ihrem [SQL Server-VMs](manage-sql-vm-portal.md).
1. Wählen Sie die SQL Server-VM in der Liste aus. Wenn Ihre SQL Server-VM hier nicht aufgeführt ist, wurde sie wahrscheinlich nicht mit der SQL-IaaS-Agent-Erweiterung registriert. 
1. Überprüfen Sie den Wert unter **Status**. Wenn das Feld **Status** den Wert **Erfolgreich** aufweist, wurde die SQL Server-VM mit der SQL-IaaS-Agent-Erweiterung registriert. 

   ![Überprüfen des Status per SQL RP-Registrierung](./media/sql-agent-extension-manually-register-single-vm/verify-registration-status.png)

### <a name="command-line"></a>Befehlszeile

Überprüfen Sie den aktuellen Registrierungsstatus der SQL Server-VM mit der Azure CLI oder Azure PowerShell. `ProvisioningState` zeigt `Succeeded` an, wenn die Registrierung erfolgreich abgeschlossen wurde. 

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/bash)

Führen Sie den folgenden Code aus, um den Registrierungsstatus mithilfe der Azure-Befehlszeilenschnittstelle zu überprüfen:  

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Führen Sie den folgenden Code aus, um den Registrierungsstatus mithilfe von Azure PowerShell zu überprüfen:

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Ein Fehler weist darauf hin, dass die SQL Server-VM nicht mit der Erweiterung registriert wurde. 


## <a name="unregister-from-extension"></a>Aufheben der Registrierung der Erweiterung

Um die Registrierung Ihrer SQL Server-VM mit der SQL-IaaS-Agent-Erweiterung aufzuheben, löschen Sie die *Ressource* „Virtueller SQL-Computer“ im Azure-Portal oder mithilfe der Azure-Befehlszeilenschnittstelle. Das Löschen der SQL-VM-*Ressource* löscht nicht die SQL Server-VM. Seien Sie jedoch vorsichtig, und führen Sie die Schritte sorgfältig aus, da es möglich ist, den virtuellen Computer versehentlich zu löschen, wenn Sie versuchen, die *Ressource* zu entfernen. 

Sie müssen die Registrierung des virtuellen SQL-Computers mit der SQL-IaaS-Agent-Erweiterung aufheben, um den Verwaltungsmodus „Vollständig“ herabstufen zu können. 

### <a name="azure-portal"></a>Azure-Portal

Führen Sie die folgenden Schritte aus, um die Registrierung der Erweiterung für Ihre SQL Server-VM im Azure-Portal aufzuheben:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zur SQL Server-VM-Ressource. 
  
   ![Ressource „Virtuelle SQL-Computer“](./media/sql-agent-extension-manually-register-single-vm/sql-vm-manage.png)

1. Klicken Sie auf **Löschen**. 

   ![Auswählen von „Löschen“ in der oberen Navigationsleiste](./media/sql-agent-extension-manually-register-single-vm/delete-sql-vm-resource.png)

1. Geben Sie den Namen der SQL-VM ein, und **deaktivieren Sie das Kontrollkästchen neben dem virtuellen Computer**.

   ![Deaktivieren der VM, um das Löschen der eigentlichen VM zu verhindern, und anschließend Auswählen von „Löschen“, um mit dem Löschen der SQL-VM-Ressource fortzufahren](./media/sql-agent-extension-manually-register-single-vm/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Wenn Sie das Kontrollkästchen neben dem Namen des virtuellen Computers nicht deaktivieren, wird der virtuelle Computer vollständig *gelöscht*. Deaktivieren Sie das Kontrollkästchen, um die Registrierung der Erweiterung für die SQL Server-VM aufzuheben, aber *den eigentlichen virtuellen Computer nicht zu löschen*. 

1. Wählen Sie **Löschen** aus, um das Löschen der SQL-VM-*Ressource* und nicht der SQL Server-VM zu bestätigen. 

### <a name="command-line"></a>Befehlszeile

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um die Registrierung der Erweiterung für Ihre SQL Server-VM mithilfe der Azure-Befehlszeilenschnittstelle aufzuheben, verwenden Sie den Befehl [az sql vm delete](/cli/azure/sql/vm?view=azure-cli-latest&preserve-view=true#az-sql-vm-delete). Dadurch wird die SQL Server-VM-*Ressource* entfernt, aber der virtuelle Computer nicht gelöscht. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um die Registrierung der Erweiterung für Ihre SQL Server-VM mit Azure PowerShell aufzuheben, verwenden Sie den Befehl [Remove-AzSqlVM](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm). Dadurch wird die SQL Server-VM-*Ressource* entfernt, aber der virtuelle Computer nicht gelöscht. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln: 

* [Übersicht über SQL Server auf einem virtuellen Windows-Computer](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Häufig gestellte Fragen zu SQL Server auf virtuellen Windows-Computern](frequently-asked-questions-faq.md)  
* [Preisinformationen für SQL Server auf virtuellen Windows-Computern](pricing-guidance.md)
* [SQL Server auf Windows-VMs – Versionshinweise](../../database/doc-changes-updates-release-notes.md)

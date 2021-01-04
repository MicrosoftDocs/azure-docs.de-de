---
title: Konfigurieren eines Verfügbarkeitsgruppe (PowerShell und Azure CLI)
description: Verwenden Sie entweder PowerShell oder die Azure-Befehlszeilenschnittstelle (Azure CLI), um den Windows-Failovercluster, den Verfügbarkeitsgruppenlistener und den internen Lastenausgleich auf einem virtuellen SQL Server-Computer in Azure zu erstellen.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: 865ee3a5aeb8a2dd06d8759ba04d02259d2b4bee
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359964"
---
# <a name="use-powershell-or-az-cli-to-configure-an-availability-group-for-sql-server-on-azure-vm"></a>Verwenden von PowerShell oder Azure CLI, um eine Verfügbarkeitsgruppe für SQL Server auf Azure-VMs zu konfigurieren 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In diesem Artikel wird die Verwendung von [PowerShell](/powershell/scripting/install/installing-powershell) oder der [Azure-Befehlszeilenschnittstelle (Azure CLI)](/cli/azure/sql/vm) zum Bereitstellen eines Windows-Failoverclusters, zum Hinzufügen von virtuellen SQL Server-Computern zum Cluster sowie zum Erstellen des internen Lastenausgleichs und Listeners für eine Always On-Verfügbarkeitsgruppe beschrieben. 

Die Bereitstellung der Verfügbarkeitsgruppe erfolgt weiterhin manuell über SQL Server Management Studio (SSMS) oder Transact-SQL (T-SQL). 

In diesem Artikel werden PowerShell und die Azure CLI verwendet, um die Umgebung der Verfügbarkeitsgruppe zu konfigurieren. Diese Konfiguration kann aber auch über das [Azure-Portal](availability-group-azure-portal-configure.md) mit [Azure-Schnellstartvorlagen](availability-group-quickstart-template-configure.md) oder [manuell](availability-group-manually-configure-tutorial.md) erfolgen. 

## <a name="prerequisites"></a>Voraussetzungen

Zum Konfigurieren einer Always On-Verfügbarkeitsgruppe müssen folgende Voraussetzungen erfüllt sein: 

- Ein [Azure-Abonnement](https://azure.microsoft.com/free/).
- Eine Ressourcengruppe mit einem Domänencontroller. 
- Ein oder mehrere in eine Domäne eingebundene [VMs in Azure, auf denen die Enterprise Edition von SQL Server 2016 (oder höher) ausgeführt wird](./create-sql-vm-portal.md), die sich in der *gleichen* Verfügbarkeitsgruppe oder in *unterschiedlichen* Verfügbarkeitszonen befinden, die mit der [SQL-IaaS-Agent-Erweiterung registriert wurden](sql-agent-extension-manually-register-single-vm.md).  
- Die aktuelle Version von [PowerShell](/powershell/scripting/install/installing-powershell) oder der [Azure CLI](/cli/azure/install-azure-cli). 
- Zwei verfügbare (nicht von einer Entität verwendete) IP-Adressen. Eine wird für den internen Lastenausgleich verwendet. Die andere ist für den Verfügbarkeitsgruppenlistener innerhalb des Subnetzes vorgesehen, in dem sich auch die Verfügbarkeitsgruppe befindet. Wenn ein vorhandener Lastenausgleich verwendet wird, ist nur eine verfügbare IP-Adresse für den Verfügbarkeitsgruppenlistener erforderlich. 

## <a name="permissions"></a>Berechtigungen

Sie benötigen die folgenden Kontoberechtigungen, um die Always On-Verfügbarkeitsgruppe mithilfe der Azure CLI zu konfigurieren: 

- Ein vorhandenes Domänenbenutzerkonto mit der Berechtigung zum **Erstellen von Computerobjekten** in der Domäne. Beispielsweise verfügt ein Domänenadministratorkonto in der Regel über ausreichende Berechtigungen (Beispiel: account@domain.com). _Dieses Konto muss auch Teil der lokalen Administratorgruppe auf allen virtuellen Computern sein, um den Cluster zu erstellen._
- Das Domänenbenutzerkonto, das SQL Server steuert. 
 
## <a name="create-a-storage-account"></a>Speicherkonto erstellen 

Der Cluster benötigt ein Speicherkonto, das als Cloudzeuge fungieren kann. Sie können ein beliebiges vorhandenes Speicherkonto verwenden oder ein neues Speicherkonto erstellen. Wenn Sie ein vorhandenes Speicherkonto verwenden möchten, fahren Sie mit dem nächsten Absatz fort. 

Das Speicherkonto wird mit dem folgenden Codeausschnitt erstellt: 

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> Möglicherweise wird der Fehler `az sql: 'vm' is not in the 'az sql' command group` angezeigt, wenn Sie eine veraltete Version der Azure CLI verwenden. Laden Sie die [neueste Version von Azure CLI](/cli/azure/install-azure-cli-windows) herunter, um diesen Fehler zu überwinden.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Create the storage account
# example: New-AzStorageAccount -ResourceGroupName SQLVM-RG -Name cloudwitness `
#    -SkuName Standard_LRS -Location West US -Kind StorageV2 `
#    -AccessTier Hot -EnableHttpsTrafficOnly

New-AzStorageAccount -ResourceGroupName <resource group name> -Name <name> `
    -SkuName Standard_LRS -Location <region> -Kind StorageV2 `
    -AccessTier Hot -EnableHttpsTrafficOnly
```

---

## <a name="define-cluster-metadata"></a>Definieren von Clustermetadaten

Die Azure CLI-Befehlsgruppe [az sql vm group](/cli/azure/sql/vm/group) verwaltet die Metadaten des Windows Server-Failoverclusterdiensts (WSFC), der die Verfügbarkeitsgruppe hostet. Zu den Clustermetadaten gehören die Active Directory-Domäne, Clusterkonten, als Cloudzeugen zu verwendende Speicherkonten und die SQL Server-Version. Verwenden Sie [az sql vm group create](/cli/azure/sql/vm/group#az-sql-vm-group-create), um die Metadaten für den WSFC zu definieren, sodass der Cluster beim Hinzufügen der ersten SQL Server-VM wie definiert erstellt wird. 

Im folgenden Codeausschnitt werden die Metadaten für den Cluster definiert:

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
# Define the cluster metadata
# example: az sql vm group create -n Cluster -l 'West US' -g SQLVM-RG `
#  --image-offer SQL2017-WS2016 --image-sku Enterprise --domain-fqdn domain.com `
#  --operator-acc vmadmin@domain.com --bootstrap-acc vmadmin@domain.com --service-acc sqlservice@domain.com `
#  --sa-key '4Z4/i1Dn8/bpbseyWX' `
#  --storage-account 'https://cloudwitness.blob.core.windows.net/'

az sql vm group create -n <cluster name> -l <region ex:eastus> -g <resource group name> `
  --image-offer <SQL2016-WS2016 or SQL2017-WS2016> --image-sku Enterprise --domain-fqdn <FQDN ex: domain.com> `
  --operator-acc <domain account ex: testop@domain.com> --bootstrap-acc <domain account ex:bootacc@domain.com> `
  --service-acc <service account ex: testservice@domain.com> `
  --sa-key '<PublicKey>' `
  --storage-account '<ex:https://cloudwitness.blob.core.windows.net/>'
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Define the cluster metadata
# example: $group = New-AzSqlVMGroup -Name Cluster -Location West US ' 
#  -ResourceGroupName SQLVM-RG -Offer SQL2017-WS2016
#  -Sku Enterprise -DomainFqdn domain.com -ClusterOperatorAccount vmadmin@domain.com
#  -ClusterBootstrapAccount vmadmin@domain.com  -SqlServiceAccount sqlservice@domain.com 
#  -StorageAccountUrl '<ex:https://cloudwitness.blob.core.windows.net/>' `
#  -StorageAccountPrimaryKey '4Z4/i1Dn8/bpbseyWX'

$group = New-AzSqlVMGroup -Name <name> -Location <regio> 
  -ResourceGroupName <resource group name> -Offer <SQL201?-WS201?> 
  -Sku Enterprise -DomainFqdn <FQDN> -ClusterOperatorAccount <domain account> 
  -ClusterBootstrapAccount <domain account>  -SqlServiceAccount <service account> 
  -StorageAccountUrl '<ex:StorageAccountUrl>' `
  -StorageAccountPrimaryKey '<PublicKey>'
```

---

## <a name="add-vms-to-the-cluster"></a>Hinzufügen von VMs zum Cluster

Beim Hinzufügen der ersten SQL Server-VM zum Cluster wird der Cluster erstellt. Der Befehl [az sql vm add-to-group](/cli/azure/sql/vm#az-sql-vm-add-to-group) erstellt den Cluster mit dem zuvor gegebenen Namen, installiert die Clusterrolle in den SQL Server-VMs und fügt sie dem Cluster hinzu. Nachfolgende Verwendungen des Befehls `az sql vm add-to-group` fügen dem neu erstellten Cluster weitere SQL Server-VMs hinzu. 

Der folgende Codeausschnitt erstellt den Cluster und fügt ihm die erste SQL Server-VM hinzu: 

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
# Add SQL Server VMs to cluster
# example: az sql vm add-to-group -n SQLVM1 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!
# example: az sql vm add-to-group -n SQLVM2 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

az sql vm add-to-group -n <VM1 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
az sql vm add-to-group -n <VM2 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
```
Verwenden Sie diesen Befehl, um dem Cluster weitere SQL Server-VMs hinzuzufügen. Ändern Sie nur den Parameter `-n` für den Namen der SQL Server-VM. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Add SQL Server VMs to cluster
# example: $sqlvm1 = Get-AzSqlVM -Name SQLVM1 -ResourceGroupName SQLVM-RG
# $sqlvm2 = Get-AzSqlVM -Name SQLVM2  -ResourceGroupName SQLVM-RG

# $sqlvmconfig1 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm1 `
#  -SqlVMGroup $group -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
#  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
#  -ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

# $sqlvmconfig2 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm2 `
#  -SqlVMGroup $group -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
#  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
#  - ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

$sqlvm1 = Get-AzSqlVM -Name <VM1 Name> -ResourceGroupName <Resource Group Name>
$sqlvm2 = Get-AzSqlVM -Name <VM2 Name> -ResourceGroupName <Resource Group Name>

$sqlvmconfig1 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm1 `
   -SqlVMGroup $group -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

Update-AzSqlVM -ResourceId $sqlvm1.ResourceId -SqlVM $sqlvmconfig1

$sqlvmconfig2 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm2 `
   -SqlVMGroup $group -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

Update-AzSqlVM -ResourceId $sqlvm2.ResourceId -SqlVM $sqlvmconfig2
```

---


## <a name="validate-cluster"></a>Validieren des Clusters 

Damit ein Failovercluster von Microsoft unterstützt werden kann, muss er die Clustervalidierung bestehen. Stellen Sie (z. B. über RDP, Remotedesktopprotokoll) eine Verbindung mit dem virtuellen Computer her, und überprüfen Sie, ob Ihr Cluster die Validierung besteht. Wird dieser Schritt nicht erfolgreich ausgeführt, verbleibt Ihr Cluster in einem nicht unterstützten Zustand. 

Sie können den Cluster mit dem Failovercluster-Manager (FCM) oder mit dem folgenden PowerShell-Befehl validieren:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

## <a name="create-availability-group"></a>Erstellen der Verfügbarkeitsgruppe

Erstellen Sie die Verfügbarkeitsgruppe wie gewohnt manuell mithilfe von [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell) oder [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> Erstellen Sie zu diesem Zeitpunkt *keinen* Listener, da dieser Schritt in den folgenden Abschnitten mithilfe der Azure CLI ausgeführt wird.  

## <a name="create-internal-load-balancer"></a>Erstellen eines internen Lastenausgleichs

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

Für den Always On-Verfügbarkeitsgruppenlistener ist eine interne Azure Load Balancer-Instanz erforderlich. Der interne Lastenausgleich stellt eine Floating IP-Adresse für den Verfügbarkeitsgruppenlistener bereit, um Failovervorgänge und Verbindungswiederherstellungen zu beschleunigen. Wenn die SQL Server-VMs in einer Verfügbarkeitsgruppe Teil des gleichen Verfügbarkeitssatzes sind, können Sie einen Lastenausgleich im Tarif „Basic“ verwenden. Andernfalls benötigen einen Lastenausgleich im Tarif „Standard“.  

> [!NOTE]
> Der interne Lastenausgleich muss sich im selben virtuellen Netzwerk befinden wie die SQL Server-VM-Instanzen. 

Der folgende Codeausschnitt erstellt den internen Lastenausgleich:

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Create the internal load balancer
# example: New-AzLoadBalancer -name sqlILB -ResourceGroupName SQLVM-RG  `
#  -Sku Standard -Location West US

New-AzLoadBalancer -name sqlILB -ResourceGroupName <resource group name> `
   -Sku Standard -Location <region>
```

---

>[!IMPORTANT]
> Die öffentliche IP-Adressressource für die einzelnen SQL Server-VMs muss über eine Standard-SKU verfügen, um mit dem Load Balancer „Standard“ kompatibel zu sein. Um die SKU der öffentlichen IP-Ressource Ihrer VM zu ermitteln, navigieren Sie zu **Ressourcengruppe**, und wählen Sie die Ressource **Öffentliche IP-Adresse** für die gewünschte SQL Server-VM aus. Der Wert befindet sich im Bereich **Übersicht** unter **SKU**.  

## <a name="create-listener"></a>Erstellen des Listeners

Nachdem die Verfügbarkeitsgruppe manuell erstellt wurde, können Sie den Listener mithilfe von [az sql vm ag-listener](/cli/azure/sql/vm/group/ag-listener#az-sql-vm-group-ag-listener-create) erstellen. 

Die *Subnetzressourcen-ID* ist der Wert von `/subnets/<subnetname>`, der an die Ressourcen-ID der VNET-Ressource angehängt wurde. So bestimmen Sie die Subnetzressourcen-ID:
   1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Ressourcengruppe. 
   1. Wählen Sie die Ressource für das virtuelle Netzwerk aus. 
   1. Wählen Sie im Bereich **Einstellungen** **Eigenschaften** aus. 
   1. Bestimmen Sie die Ressourcen-ID des virtuellen Netzwerks, und hängen Sie am Ende `/subnets/<subnetname>` an, um die Subnetzressourcen-ID zu erstellen. Beispiel:
      - Die Ressourcen-ID des virtuellen Netzwerks lautet `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`.
      - Ihr Subnetzname ist `default`.
      - Daher heißt Ihre Subnetzressourcen-ID `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`.


Der Verfügbarkeitsgruppenlistener wird mithilfe des folgenden Codeausschnitts erstellt:

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
# Create the availability group listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas, ex: sqlvm1 sqlvm2>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive

# example: New-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
#    -AvailabilityGroupName SQLAG  -GroupName Cluster `
#    -IpAddress 10.0.0.27 -LoadBalancerResourceId sqlilb `
#    -ProbePort 59999 `
#    -SubnetId /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#    -SqlVirtualMachineId sqlvm1 sqlvm2


New-AzAvailabilityGroupListener -Name <listener name> -ResourceGroupName <resource group name> `
   -AvailabilityGroupName <availability group name> -GroupName <cluster name> `
   -IpAddress <ag listener IP address> -LoadBalancerResourceId <lbid> `
   -ProbePort <Load Balancer probe port, default 59999> `
   -SubnetId <subnet resource id> `
   -SqlVirtualMachineId <names of SQL VM's hosting AG replicas>
```

---

## <a name="modify-number-of-replicas"></a>Ändern der Anzahl von Replikaten 
Beim Bereitstellen einer Verfügbarkeitsgruppe auf SQL Server-VMs, die in Azure gehostet sind, muss zusätzliche Komplexität berücksichtigt werden. Der Ressourcenanbieter und die Gruppe der virtuellen Computer verwalten nun die Ressourcen. Beim Hinzufügen oder Entfernen von Replikaten zur bzw. aus der Verfügbarkeitsgruppe ist daher das Aktualisieren der Metadaten des Listeners mit Informationen zu den SQL Server-VMs als zusätzlicher Schritt auszuführen. Beim Ändern der Anzahl von Replikaten in der Verfügbarkeitsgruppe muss außerdem der Befehl [az sql vm group ag-listener update](/cli/azure/sql/vm/group/ag-listener#az-sql-vm-group-ag-listener-update) verwendet werden, um den Listener mit den Metadaten der SQL Server-VMs zu aktualisieren. 


### <a name="add-a-replica"></a>Hinzufügen eines Replikats

So fügen Sie der Verfügbarkeitsgruppe ein neues Replikat hinzu:

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Hinzufügen des virtuellen SQL Server-Computers zur Clustergruppe:
   ```azurecli-interactive

   # Add the SQL Server VM to the cluster group
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```

1. Verwenden Sie SQL Server Management Studio, um die SQL Server-Instanz innerhalb der Verfügbarkeitsgruppe als Replikat hinzuzufügen.
1. Fügen Sie dem Listener die SQL Server-VM-Metadaten hinzu:

   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Hinzufügen des virtuellen SQL Server-Computers zur Clustergruppe:

   ```powershell-interactive
   # Add the SQL Server VM to the cluster group
   # example: $sqlvm3 = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG 
   # $group = Get-AzSqlVMGroup -ResourceGroupName SQLVM-RG -Name Cluster
   # $sqlvmconfig3 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm3 -SqlVMGroup $group `
   #  -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
   #  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
   #  -ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

   $sqlvm3 = Get-AzSqlVM -Name <VM1 Name> -ResourceGroupName <Resource Group Name>
   $group = Get-AzSqlVMGroup  -ResourceGroupName <resource group name> -Name <name>
   $sqlvmconfig3 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm3 -SqlVMGroup $group `
   -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

   Update-AzSqlVM -ResourceId $sqlvm3.ResourceId -SqlVM $sqlvmconfig3
   ```

1. Verwenden Sie SQL Server Management Studio, um die SQL Server-Instanz innerhalb der Verfügbarkeitsgruppe als Replikat hinzuzufügen.
1. Fügen Sie dem Listener die SQL Server-VM-Metadaten hinzu:

   ```powershell-interactive
   # Update the listener metadata with the new VM
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #   -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs, along with new SQL VM> 

   ```

---

### <a name="remove-a-replica"></a>Entfernung eines Replikats

So entfernen Sie ein Replikat aus der Verfügbarkeitsgruppe:

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Entfernen Sie das Replikat mithilfe von SQL Server Management Studio aus der Verfügbarkeitsgruppe. 
1. Entfernen Sie die SQL Server-VM-Metadaten aus dem Listener:
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Entfernen Sie die SQL Server-VM aus dem Cluster:
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Entfernen Sie das Replikat mithilfe von SQL Server Management Studio aus der Verfügbarkeitsgruppe. 
1. Entfernen Sie die SQL Server-VM-Metadaten aus dem Listener:

   ```powershell-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #  -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs that remain>

   ```
1. Entfernen Sie die SQL Server-VM aus dem Cluster:

   ```powershell-interactive
   # Remove the SQL VM from the cluster
   # example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
   #  $sqlvm. SqlVirtualMachineGroup = ""
   #  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

   $sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
      $sqlvm. SqlVirtualMachineGroup = ""
    
    Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
   ```

---

## <a name="remove-listener"></a>Entfernen des Listeners
Wenn Sie den mit der Azure-Befehlszeilenschnittstelle konfigurierten Verfügbarkeitsgruppenlistener später entfernen möchten, müssen Sie dies über die SQL-IaaS-Agent-Erweiterung tun. Da der Listener über die SQL-IaaS-Agent-Erweiterung registriert wurde, reicht das Löschen über SQL Server Management Studio nicht aus. 

Die beste Methode besteht darin, ihn über die SQL-IaaS-Agent-Erweiterung zu löschen, indem Sie in der Azure-Befehlszeilenschnittstelle den folgenden Codeausschnitt verwenden. Dadurch werden die Metadaten des Verfügbarkeitsgruppenlisteners aus der SQL-IaaS-Agent-Erweiterung entfernt. Außerdem wird der Listener physisch aus der Verfügbarkeitsgruppe gelöscht. 

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Remove the availability group listener
# example: Remove-AzAvailabilityGroupListener -Name AGListener `
#   -ResourceGroupName SQLVM-RG -SqlVMGroupName Cluster

Remove-AzAvailabilityGroupListener -Name <Listener> `
   -ResourceGroupName <Resource Group Name> -SqlVMGroupName <cluster name>
```

---

## <a name="remove-cluster"></a>Entfernen des Clusters

Entfernen Sie alle Knoten aus dem Cluster, um ihn zu zerstören, und entfernen Sie dann die Clustermetadaten aus der SQL-IaaS-Agent-Erweiterung. Verwenden Sie hierzu die Azure-Befehlszeilenschnittstelle oder PowerShell. 


# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Entfernen Sie zunächst alle virtuellen SQL Server-VMs aus dem Cluster: 

```azurecli-interactive
# Remove the VM from the cluster metadata
# example: az sql vm remove-from-group --name SQLVM2 --resource-group SQLVM-RG

az sql vm remove-from-group --name <VM1 name>  --resource-group <resource group name>
az sql vm remove-from-group --name <VM2 name>  --resource-group <resource group name>
```

Wenn es sich hierbei um die einzigen VMs im Cluster handelt, wird der Cluster zerstört. Wenn sich neben den entfernten SQL Server-VMs noch andere VMs im Cluster befinden, werden die anderen VMs nicht entfernt, und der Cluster wird nicht zerstört. 

Entfernen Sie als Nächstes die Clustermetadaten aus der SQL-IaaS-Agent-Erweiterung: 

```azurecli-interactive
# Remove the cluster from the SQL VM RP metadata
# example: az sql vm group delete --name Cluster --resource-group SQLVM-RG

az sql vm group delete --name <cluster name> Cluster --resource-group <resource group name>
```



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Entfernen Sie zunächst alle virtuellen SQL Server-VMs aus dem Cluster. Dadurch werden die Knoten physisch aus dem Cluster entfernt, und der Cluster wird zerstört: 

```powershell-interactive
# Remove the SQL VM from the cluster
# example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
#  $sqlvm. SqlVirtualMachineGroup = ""
#  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

$sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
   $sqlvm. SqlVirtualMachineGroup = ""
   
   Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
```

Wenn es sich hierbei um die einzigen VMs im Cluster handelt, wird der Cluster zerstört. Wenn sich neben den entfernten SQL Server-VMs noch andere VMs im Cluster befinden, werden die anderen VMs nicht entfernt, und der Cluster wird nicht zerstört. 

Entfernen Sie als Nächstes die Clustermetadaten aus der SQL-IaaS-Agent-Erweiterung: 

```powershell-interactive
# Remove the cluster metadata
# example: Remove-AzSqlVMGroup -ResourceGroupName "SQLVM-RG" -Name "Cluster"

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name> "
```

---

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln: 

* [Was ist SQL Server auf virtuellen Azure-Computern? (Windows)](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Häufig gestellte Fragen zu SQL Server auf virtuellen Windows-Computern in Azure](frequently-asked-questions-faq.md)
* [SQL Server auf Azure-VMs – Versionshinweise](../../database/doc-changes-updates-release-notes.md)
* [Ändern des Lizenzierungsmodells für eine SQL Server-VM in Azure](licensing-model-azure-hybrid-benefit-ahb-change.md)
* [Übersicht über Always On-Verfügbarkeitsgruppen (SQL Server)](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Aktivieren des Features für Always On-Verfügbarkeitsgruppen für eine SQL Server-Instanz](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Verwalten einer Verfügbarkeitsgruppe](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Tools zum Überwachen von Always On-Verfügbarkeitsgruppen](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Transact-SQL-Anweisungen für Always On-Verfügbarkeitsgruppen](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Übersicht über PowerShell-Cmdlets für Always On-Verfügbarkeitsgruppen](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)
---
title: Konfigurieren eines Verfügbarkeitsgruppe (Azure-Portal)
description: Verwenden Sie das Azure-Portal, um den Windows-Failovercluster, den Verfügbarkeitsgruppenlistener und den internen Lastenausgleich auf einer SQL Server-VM in Azure zu erstellen.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: 14760b4244d42e57aaed7f7d96f487a66147a554
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359505"
---
# <a name="use-azure-portal-to-configure-an-availability-group-preview-for-sql-server-on-azure-vm"></a>Verwenden des Azure-Portals zum Konfigurieren einer Verfügbarkeitsgruppe (Vorschau) für SQL Server auf einem virtuellen Azure-Computer 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In diesem Artikel wird beschrieben, wie Sie das [Azure-Portal](https://portal.azure.com) verwenden, um eine Verfügbarkeitsgruppe für SQL Server auf virtuellen Azure-Computern zu konfigurieren. 

Im Azure-Portal können Sie einen neuen Cluster erstellen oder einen vorhandenen Cluster integrieren und dann die Verfügbarkeitsgruppe, den Listener und den internen Load Balancer erstellen. 

Diese Funktion steht derzeit als Vorschau zur Verfügung. 

In diesem Artikel wird die Umgebung der Verfügbarkeitsgruppen über das Azure-Portal konfiguriert. Diese Konfiguration kann aber auch über [PowerShell oder die Azure CLI](availability-group-az-commandline-configure.md), [Azure-Schnellstartvorlagen](availability-group-quickstart-template-configure.md) oder aber [manuell](availability-group-manually-configure-tutorial.md) erledigt werden. 


## <a name="prerequisites"></a>Voraussetzungen

Zum Konfigurieren einer Always On-Verfügbarkeitsgruppe unter Verwendung des Azure-Portals müssen folgende Voraussetzungen erfüllt sein: 

- Ein [Azure-Abonnement](https://azure.microsoft.com/free/).
- Eine Ressourcengruppe mit einem Domänencontroller. 
- Ein oder mehrere in eine Domäne eingebundene [VMs in Azure, auf denen die Enterprise Edition von SQL Server 2016 (oder höher) ausgeführt wird](./create-sql-vm-portal.md), die sich in der *gleichen* Verfügbarkeitsgruppe oder in *unterschiedlichen* Verfügbarkeitszonen befinden, die mit der [SQL-IaaS-Agent-Erweiterung im Verwaltbarkeitsmodus „Vollständig“ registriert wurden](sql-agent-extension-manually-register-single-vm.md) und dasselbe Domänenkonto für den SQL Server-Dienst auf jeder VM verwenden.
- Zwei verfügbare (nicht von einer Entität verwendete) IP-Adressen. Eine wird für den internen Lastenausgleich verwendet. Die andere ist für den Verfügbarkeitsgruppenlistener innerhalb des Subnetzes vorgesehen, in dem sich auch die Verfügbarkeitsgruppe befindet. Wenn ein vorhandener Lastenausgleich verwendet wird, ist nur eine verfügbare IP-Adresse für den Verfügbarkeitsgruppenlistener erforderlich. 

## <a name="permissions"></a>Berechtigungen

Sie benötigen die folgenden Kontoberechtigungen, um die Verfügbarkeitsgruppe mithilfe des Azure-Portals zu konfigurieren: 

- Ein vorhandenes Domänenbenutzerkonto mit der Berechtigung zum **Erstellen von Computerobjekten** in der Domäne. Beispielsweise verfügt ein Domänenadministratorkonto in der Regel über ausreichende Berechtigungen (Beispiel: account@domain.com). _Dieses Konto muss auch Teil der lokalen Administratorgruppe auf allen virtuellen Computern sein, um den Cluster zu erstellen._
- Das Domänenbenutzerkonto, das SQL Server steuert. Dies sollte das gleiche Konto für jede SQL Server-VM sein, die Sie der Verfügbarkeitsgruppe hinzufügen möchten. 

## <a name="configure-cluster"></a>Konfigurieren des Clusters

Konfigurieren Sie den Cluster mit dem Azure-Portal. Sie können entweder einen neuen Cluster erstellen oder, wenn Sie bereits über einen vorhandenen Cluster verfügen, diesen zur Portalverwaltbarkeit in die SQL-IaaS-Agent-Erweiterung integrieren.


### <a name="create-a-new-cluster"></a>Neuen Cluster erstellen

Wenn Sie bereits über einen Cluster verfügen, überspringen Sie diesen Abschnitt, und fahren Sie stattdessen mit [Integrieren eines vorhandenen Clusters](#onboard-existing-cluster) fort. 

Wenn Sie noch keinen vorhandenen Cluster haben, führen Sie diese Schritte im Azure-Portal aus, um den Cluster zu erstellen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 
1. Navigieren Sie zur Ressource [Virtuelle SQL-Computer](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines). 
1. Wählen Sie **Hochverfügbarkeit** unter **Einstellungen** aus. 
1. Wählen Sie **+ Neuer Windows Server-Failovercluster** aus, um die Seite **Windows Server-Failovercluster konfigurieren** zu öffnen.  

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-cluster.png" alt-text="Erstellen Sie einen neuen Cluster, indem Sie „+ Neuer Cluster“ im Portal auswählen":::

1. Benennen Sie Ihren Cluster, und geben Sie ein als Cloudzeuge zu verwendendes Speicherkonto an. Verwenden Sie ein vorhandenes Speicherkonto, oder wählen Sie **Neu erstellen** aus, um ein neues Speicherkonto zu erstellen. Ein Speicherkontoname muss zwischen 3 und 24 Zeichen lang sein und darf ausschließlich Ziffern und Kleinbuchstaben enthalten.

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-1.png" alt-text="Geben Sie den Namen, das Speicherkonto und die Anmeldeinformationen für den Cluster an":::

1. Erweitern Sie **Anmeldeinformationen des Windows Server-Failoverclusters**, um [Anmeldeinformationen](/rest/api/sqlvm/sqlvirtualmachinegroups/createorupdate#wsfcdomainprofile) für das SQL Server-Dienstkonto sowie die Clusteroperator- und Bootstrapkonten anzugeben, sofern sie sich von dem für den SQL Server-Dienst verwendeten Konto unterscheiden. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-2.png" alt-text="Geben Sie Anmeldeinformationen für das SQL Server-Konto, das Clusteroperator- und das Bootstrapkonto an":::

1. Wählen Sie die SQL Server-VMs aus, die Sie dem Cluster hinzufügen möchten. Beachten Sie, ob ein Neustart erforderlich ist oder nicht, und gehen Sie mit Bedacht vor. Es werden nur VMs angezeigt, die mit der SQL-IaaS-Agent-Erweiterung im Verwaltbarkeitsmodus „Vollständig“ registriert sind und sich am gleichen Speicherort, in der gleichen Domäne und im gleichen virtuellen Netzwerk befinden wie die primäre SQL Server-VM. 
1. Wählen Sie **Übernehmen** aus, um den Cluster zu erstellen. Sie können den Status Ihrer Bereitstellung im **Aktivitätsprotokoll** überprüfen, auf das Sie über das Glockensymbol in der oberen Navigationsleiste zugreifen können. 
1. Damit ein Failovercluster von Microsoft unterstützt werden kann, muss er die Clustervalidierung bestehen. Stellen Sie (z. B. über RDP, Remotedesktopprotokoll) eine Verbindung mit dem virtuellen Computer her, und überprüfen Sie, ob Ihr Cluster die Validierung besteht. Wird dieser Schritt nicht erfolgreich ausgeführt, verbleibt Ihr Cluster in einem nicht unterstützten Zustand. Sie können den Cluster mit dem Failovercluster-Manager (FCM) oder mit dem folgenden PowerShell-Befehl validieren:

    ```powershell
    Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
    ```
    


### <a name="onboard-existing-cluster"></a>Integrieren eines vorhandenen Clusters

Wenn Sie in Ihrer SQL Server-VM-Umgebung bereits einen Cluster konfiguriert haben, können Sie ihn über das Azure-Portal integrieren.

Gehen Sie dazu folgendermaßen vor:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 
1. Navigieren Sie zur Ressource [Virtuelle SQL-Computer](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines). 
1. Wählen Sie **Hochverfügbarkeit** unter **Einstellungen** aus. 
1. Wählen Sie **Vorhandenen Windows Server-Failovercluster integrieren** aus, um die Seite **Windows Server-Failovercluster integrieren** zu öffnen. 

   :::image type="content" source="media/availability-group-az-portal-configure/onboard-existing-cluster.png" alt-text="Integrieren eines vorhandenen Clusters über die Seite „Hochverfügbarkeit“ auf der SQL-VM-Ressource":::

1. Überprüfen Sie die Einstellungen für Ihren Cluster. 
1. Wählen Sie **Übernehmen** aus, um Ihren Cluster zu integrieren, und wählen Sie dann an der Eingabeaufforderung **Ja** aus, um fortzufahren.

## <a name="create-availability-group"></a>Erstellen der Verfügbarkeitsgruppe

Nach dem Erstellen oder Integrieren des Clusters erstellen Sie die Verfügbarkeitsgruppe über das Azure-Portal. Gehen Sie dazu folgendermaßen vor:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 
1. Navigieren Sie zur Ressource [Virtuelle SQL-Computer](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines). 
1. Wählen Sie **Hochverfügbarkeit** unter **Einstellungen** aus. 
1. Wählen Sie **+ Neue Always On-Verfügbarkeitsgruppe** aus, um die Seite **Verfügbarkeitsgruppe erstellen** zu öffnen.

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-availability-group.png" alt-text="Wählen Sie „Neue Always On-Verfügbarkeitsgruppe“ aus, um die Seite „Verfügbarkeitsgruppe erstellen“ zu öffnen.":::

1. Geben Sie einen Namen für die Verfügbarkeitsgruppe ein. 
1. Wählen Sie **Listener konfigurieren** aus, um die Seite **Verfügbarkeitsgruppenlistener konfigurieren** zu öffnen. 

   :::image type="content" source="media/availability-group-az-portal-configure/create-availability-group.png" alt-text="Geben Sie einen Namen für die Verfügbarkeitsgruppe ein, und konfigurieren Sie einen Listener":::

1. Füllen Sie die Werte aus, und verwenden Sie entweder einen vorhandenen Load Balancer, oder wählen Sie **Neu erstellen** aus, um einen neuen Load Balancer zu erstellen.  Wählen Sie **Übernehmen** aus, um Ihre Einstellungen zu speichern sowie den Listener und Load Balancer zu erstellen. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-listener.png" alt-text="Füllen Sie die Werte im Formular aus, um den neuen Listener und Load Balancer zu erstellen":::

1. Wählen Sie **+ Replikat auswählen** aus, um die Seite **Verfügbarkeitsgruppenreplikate konfigurieren** zu öffnen.
1. Wählen Sie die virtuellen Computer aus, die Sie der Verfügbarkeitsgruppe hinzufügen möchten, und legen Sie die Verfügbarkeitsgruppeneinstellungen fest, die Ihren Geschäftsanforderungen am besten entsprechen. Wählen Sie **Übernehmen** aus, um die Einstellungen zu speichern. 

   :::image type="content" source="media/availability-group-az-portal-configure/add-replicas.png" alt-text="Wählen Sie die VMs aus, die Sie der Verfügbarkeitsgruppe hinzufügen möchten, und konfigurieren Sie die Einstellungen entsprechend Ihren Geschäftsanforderungen":::

1. Überprüfen Sie Ihre Verfügbarkeitsgruppeneinstellungen, und wählen Sie dann **Übernehmen** aus, um Ihre Verfügbarkeitsgruppe zu erstellen. 

Sie können den Status Ihrer Bereitstellung im **Aktivitätsprotokoll** überprüfen, auf das Sie über das Glockensymbol in der oberen Navigationsleiste zugreifen können. 

  > [!NOTE]
  > Der **Synchronisierungsstatus** auf der Seite **Hochverfügbarkeit** im Azure-Portal wird als **Fehlerhaft** angezeigt, bis Sie der Verfügbarkeitsgruppe Datenbanken hinzufügen. 


## <a name="add-database-to-availability-group"></a>Hinzufügen von Datenbanken zu einer Verfügbarkeitsgruppe

Fügen Sie Ihrer Verfügbarkeitsgruppe nach Abschluss der Bereitstellung Datenbanken hinzu. In den folgenden Schritte wird SQL Server Management Studio (SSMS) verwendet, aber Sie können auch [Transact-SQL oder PowerShell](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) verwenden. 

Um Datenbanken mit SQL Server Management Studio zu Ihrer Verfügbarkeitsgruppe hinzuzufügen, führen Sie folgende Schritte aus:

1. Stellen Sie mit Ihrer bevorzugten Methode eine Verbindung zu einer Ihrer SQL Server-VMs her, wie z. B. Remote Desktop Connection (RDP). 
1. Öffnen Sie SQL Server Management Studio (SSMS).
1. Stellen Sie eine Verbindung mit Ihrer SQL Server-Instanz her. 
1. Erweitern Sie **Always On-Hochverfügbarkeit** im **Objekt-Explorer**.
1. Erweitern Sie **Verfügbarkeitsgruppen**, klicken Sie mit der rechten Maustaste auf Ihre Verfügbarkeitsgruppe, und wählen Sie **Datenbank hinzufügen** aus.

   :::image type="content" source="media/availability-group-az-portal-configure/add-database.png" alt-text="Klicken Sie im Objekt-Explorer mit der rechten Maustaste auf die Verfügbarkeitsgruppe, und wählen Sie „Datenbank hinzufügen“ aus":::

1. Folgen Sie den Eingabeaufforderungen, um die Datenbank(en) auszuwählen, die Sie zu Ihrer Verfügbarkeitsgruppe hinzufügen möchten. 
1. Wählen Sie **OK** aus, um die Einstellungen zu speichern und die Datenbank zur Verfügbarkeitsgruppe hinzuzufügen. 
1. Nachdem die Datenbank hinzugefügt wurde, aktualisieren Sie den **Objekt-Explorer**, um sich zu vergewissern, dass der Status der Datenbank jetzt `synchronized` lautet. 

Nachdem Datenbanken hinzugefügt wurden, können Sie den Status Ihrer Verfügbarkeitsgruppe im Azure-Portal überprüfen: 

:::image type="content" source="media/availability-group-az-portal-configure/healthy-availability-group.png" alt-text="Überprüfen Sie den Status Ihrer Verfügbarkeitsgruppe auf der Seite „Hochverfügbarkeit“ im Azure-Portal, nachdem die Datenbanken synchronisiert wurden":::

## <a name="add-more-vms"></a>Hinzufügen weiterer VMs

Führen Sie die folgenden Schritte aus, um dem Cluster weitere SQL Server-VMs hinzuzufügen: 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 
1. Navigieren Sie zur Ressource [Virtuelle SQL-Computer](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines). 
1. Wählen Sie **Hochverfügbarkeit** unter **Einstellungen** aus. 
1. Wählen Sie **Windows Server-Failovercluster konfigurieren** aus, um die Seite **Windows Server-Failovercluster konfigurieren** zu öffnen. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-existing-cluster.png" alt-text="Wählen Sie „Windows Server-Failovercluster konfigurieren“ aus, um Ihrem Cluster VMs hinzuzufügen.":::

1. Erweitern Sie **Windows Server-Failovercluster-Anmeldeinformationen**, und geben Sie in die Konten, die für den SQL Server-Dienst verwendet werden, Clusteroperator- und Clusterbootstrap-Konten ein. 
1. Wählen Sie die SQL Server-VMs aus, die Sie dem Cluster hinzufügen möchten. 
1. Wählen Sie **Übernehmen**. 

Sie können den Status Ihrer Bereitstellung im **Aktivitätsprotokoll** überprüfen, auf das Sie über das Glockensymbol in der oberen Navigationsleiste zugreifen können. 


## <a name="modify-availability-group"></a>Ändern der Verfügbarkeitsgruppe 


Auf der Seite **Hochverfügbarkeit** im Azure-Portal können Sie **der Verfügbarkeitsgruppe weitere Replikate hinzufügen**, **den Listener konfigurieren** oder **den Listener löschen**, indem Sie die Auslassungspunkte (...) neben Ihrer Verfügbarkeitsgruppe auswählen: 

:::image type="content" source="media/availability-group-az-portal-configure/configure-listener.png" alt-text="Wählen Sie die Auslassungspunkte neben der Verfügbarkeitsgruppe und dann „Replikat hinzufügen“ aus, um weitere Replikate zur Verfügbarkeitsgruppe hinzuzufügen.":::

## <a name="remove-cluster"></a>Entfernen des Clusters

Entfernen Sie alle SQL Server-VMs aus dem Cluster, um ihn zu zerstören, und entfernen Sie dann die Clustermetadaten aus der SQL-IaaS-Agent-Erweiterung. Verwenden Sie hierzu die neueste Version von [Azure CLI](/cli/azure/install-azure-cli) oder PowerShell. 

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Entfernen Sie zunächst alle virtuellen SQL Server-VMs aus dem Cluster. Dadurch werden die Knoten physisch aus dem Cluster entfernt, und der Cluster wird zerstört:  

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

az sql vm group delete --name <cluster name> --resource-group <resource group name>
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

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name>"
```

---

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie auf Probleme stoßen, können Sie den Bereitstellungsverlauf überprüfen und die häufigsten Fehler sowie deren Lösungen anzeigen. 

### <a name="check-deployment-history"></a>Überprüfen des Bereitstellungsverlaufs

Änderungen am Cluster und an der Verfügbarkeitsgruppe über das Portal erfolgen über Bereitstellungen. Der Bereitstellungsverlauf bietet mehr Details, wenn Probleme beim Erstellen oder Integrieren des Clusters oder beim Erstellen der Verfügbarkeitsgruppe vorliegen.

Gehen Sie folgendermaßen vor, um die Protokolle für die Bereitstellung anzuzeigen und den Bereitstellungsverlauf zu überprüfen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wechseln Sie zu Ihrer Ressourcengruppe.
1. Wählen Sie unter **Einstellungen** die Option **Bereitstellungen** aus.
1. Wählen Sie die Bereitstellung aus, die Sie interessiert, um mehr darüber zu erfahren. 


   :::image type="content" source="media/availability-group-az-portal-configure/failed-deployment.png" alt-text="Wählen Sie die Bereitstellung aus, über die Sie mehr erfahren möchten." :::

### <a name="common-errors"></a>Häufige Fehler

Überprüfen Sie die folgenden häufigen Fehler und ihre Lösungen. 

#### <a name="the-account-which-is-used-to-start-up-sql-service-is-not-a-domain-account"></a>Das zum Starten des SQL-Diensts verwendete Konto ist kein Domänenkonto

Dies ist ein Hinweis darauf, dass der Ressourcenanbieter mit den angegebenen Anmeldeinformationen nicht auf den SQL Server-Dienst zugreifen konnte. Einige häufige Lösungen:
- Stellen Sie sicher, das der Domänencontroller ausgeführt wird.
- Überprüfen Sie, ob die im Portal angegebenen Anmeldeinformationen mit denen des SQL Server-Diensts übereinstimmen. 


## <a name="next-steps"></a>Nächste Schritte


Weitere Informationen zu Verfügbarkeitsgruppen finden Sie unter

- [Übersicht über Verfügbarkeitsgruppen](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
* [Verwaltung einer Verfügbarkeitsgruppe](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Tools zum Überwachen von Always On-Verfügbarkeitsgruppen](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Transact-SQL-Anweisungen für Verfügbarkeitsgruppen](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [PowerShell-Befehle für Verfügbarkeitsgruppen](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  

Weitere Informationen zu SQL Server-VMs finden Sie unter 

* [Was ist SQL Server auf virtuellen Azure-Computern? (Windows)](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server auf Azure-VMs – Versionshinweise](../../database/doc-changes-updates-release-notes.md)
* [Häufig gestellte Fragen zu SQL Server auf virtuellen Windows-Computern in Azure](frequently-asked-questions-faq.md)

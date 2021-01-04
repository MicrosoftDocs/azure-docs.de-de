---
title: Konfigurieren eines DNN-Listeners für eine Verfügbarkeitsgruppe
description: Erfahren Sie, wie Sie einen DNN-Listener (Distributed Network Name, Name des verteilten Netzwerks) konfigurieren, um den VNN-Listener (Virtual Network Name, Name des virtuellen Netzwerks) zu ersetzen und Datenverkehr an Ihre AlwaysOn-Verfügbarkeitsgruppen auf SQL Server auf Azure-VMs weiterzuleiten.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 07ce01304f27ded4e0a566777fcf7027f7a15e4b
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359437"
---
# <a name="configure-a-dnn-listener-for-an-availability-group"></a>Konfigurieren eines DNN-Listeners für eine Verfügbarkeitsgruppe
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Bei SQL Server auf Azure-VMs wird über den DNN (Distributed Network Name, Name des verteilten Netzwerks) Datenverkehr an die entsprechende Clusterressource weitergeleitet. Er bietet eine einfachere Möglichkeit zum Herstellen einer Verbindung mit einer AlwaysOn-Verfügbarkeitsgruppe als der VNN-Listener (Virtual Network Name, Name des virtuellen Netzwerks), ohne dass ein Azure Load Balancer-Objekt erforderlich ist. 

In diesem Artikel erfahren Sie, wie Sie einen DNN-Listener konfigurieren, durch den der VNN-Listener ersetzt und Datenverkehr an Ihre Verfügbarkeitsgruppe mit SQL Server auf Azure-VMs für Hochverfügbarkeit und Notfallwiederherstellung (HADR) weitergeleitet wird. 

Die DNN-Listener-Funktionalität ist zurzeit nur ab SQL Server 2019 CU8 unter Windows Server 2016 und höher verfügbar. 

Als alternative Konnektivitätsoption bietet sich ein [VNN-Listener mit Azure Load Balancer-Objekt](availability-group-vnn-azure-load-balancer-configure.md) an. 

## <a name="overview"></a>Übersicht

Ein DNN-Listener ersetzt den herkömmlichen VNN-Verfügbarkeitsgruppenlistener, wenn er mit [AlwaysOn-Verfügbarkeitsgruppen auf SQL Server-VMs](availability-group-overview.md) verwendet wird. Dadurch ist es nicht mehr erforderlich, Datenverkehr über ein Azure Load Balancer-Objekt weiterzuleiten, wodurch Bereitstellung und Wartung vereinfacht werden und Failover verbessert wird. 

Verwenden Sie den DNN-Listener, um einen vorhandenen VNN-Listener zu ersetzen, oder um ihn alternativ in Verbindung mit einem vorhandenen VNN-Listener zu nutzen, um Ihre Verfügbarkeitsgruppe mit zwei unterschiedlichen Verbindungspunkten auszustatten: einen, der den VNN-Listenernamen (und Port, falls nicht der Standardport) verwendet, und einen, der den DNN-Listenernamen und entsprechenden Port verwendet. 

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie die in diesem Artikel aufgeführten Schritte ausführen, sollten Sie über Folgendes verfügen:

- SQL Server 2019 mit CU8 oder höher unter Windows Server 2016 und höher
- Sie sollten entschieden haben, dass der Name des verteilten Netzwerks die geeignete [Konnektivitätsoption für die HADR-Lösung](hadr-cluster-best-practices.md#connectivity) ist.
- Sie sollten Ihre [AlwaysOn-Verfügbarkeitsgruppe](availability-group-overview.md) konfiguriert haben. 
- Sie müssen die neueste Version von [PowerShell](/powershell/azure/install-az-ps) installiert haben. 


## <a name="create-script"></a>Erstellen des Skripts

Verwenden Sie PowerShell, um die DNN-Ressource (Distributed Network Name, Name des verteilten Netzwerks) zu erstellen und sie Ihrer Verfügbarkeitsgruppe zuzuordnen. 

Gehen Sie dazu folgendermaßen vor: 

1. Öffnen Sie einen Text-Editor, z. B. Notepad. 
1. Kopieren Sie das folgende Skript, und fügen Sie es ein. 

   ```powershell
   param (
      [Parameter(Mandatory=$true)][string]$Ag,
      [Parameter(Mandatory=$true)][string]$Dns,
      [Parameter(Mandatory=$true)][string]$Port
   )
   
   Write-Host "Add a DNN listener for availability group $Ag with DNS name $Dns and port $Port"
   
   $ErrorActionPreference = "Stop"
   
   # create the DNN resource with the port as the resource name
   Add-ClusterResource -Name $Port -ResourceType "Distributed Network Name" -Group $Ag 
   
   # set the DNS name of the DNN resource
   Get-ClusterResource -Name $Port | Set-ClusterParameter -Name DnsName -Value $Dns 
   
   # start the DNN resource
   Start-ClusterResource -Name $Port
   
   
   $Dep = Get-ClusterResourceDependency -Resource $Ag
   if ( $Dep.DependencyExpression -match '\s*\((.*)\)\s*' )
   {
   $DepStr = "$($Matches.1) or [$Port]"
   }
   else
   {
   $DepStr = "[$Port]"
   }
   
   Write-Host "$DepStr"
   
   # add the Dependency from availability group resource to the DNN resource
   Set-ClusterResourceDependency -Resource $Ag -Dependency "$DepStr"
   
   
   #bounce the AG resource
   Stop-ClusterResource -Name $Ag
   Start-ClusterResource -Name $Ag
   ```

1. Speichern Sie das Skript als `.ps1`-Datei, z. B. `add_dnn_listener.ps1`. 


## <a name="execute-script"></a>Ausführen des Skripts

Um den DNN-Listener zu erstellen, führen Sie das Skript aus, wobei Sie Parameter für den Namen der Verfügbarkeitsgruppe, den Listenernamen und den Listenerport übergeben. 

Angenommen, Sie haben eine Verfügbarkeitsgruppe namens `ag1`, einen Listener namens `dnnlsnr` und den Port `6789` für den Listener, dann führen Sie die folgenden Schritte aus: 

1. Öffnen Sie ein Befehlszeilenschnittstellentool, z. B. Eingabeaufforderung oder PowerShell. 
1. Navigieren Sie zum Speicherort des `.ps1`-Skripts, z. B. „C:\Dokumente“. 
1. Führen Sie das Skript aus: ```add_dnn_listener.ps1 <ag name> <listener-name> <listener port>```. Zum Beispiel: 

   ```console
   c:\Documents> add_dnn_listener.ps1 ag1 dnnlsnr 6789
   ```

## <a name="verify-listener"></a>Überprüfen des Listeners

Verwenden Sie entweder SQL Server Management Studio oder Transact-SQL, um zu bestätigen, dass der DNN-Listener erfolgreich erstellt wurde. 

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

Erweitern Sie **Verfügbarkeitsgruppenlistener** in [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms), um Ihren DNN-Listener anzuzeigen: 

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-in-ssms.png" alt-text="Anzeigen des DNN-Listeners unter „Verfügbarkeitsgruppenlistener“ in SQL Server Management Studio (SSMS)":::

### <a name="transact-sql"></a>Transact-SQL

Verwenden Sie Transact-SQL, um den Status des DNN-Listeners anzuzeigen: 

```sql
SELECT * FROM SYS.AVAILABILITY_GROUP_LISTENERS
```

Der Wert `1` für `is_distributed_network_name` kennzeichnet, dass der Listener ein DNN-Listener (Distributed Network Name, Name des verteilten Netzwerks) ist: 

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-tsql.png" alt-text="Mit „sys.availability_group_listeners“ die DNN-Listener ermitteln, die den Wert „1“ in „is_distributed_network_name“ haben":::


## <a name="update-connection-string"></a>Aktualisieren der Verbindungszeichenfolge

Aktualisieren Sie die Verbindungszeichenfolgen für Anwendungen, sodass sie Verbindungen mit dem DNN-Listener herstellen. Fügen Sie `MultiSubnetFailover=True` zur Verbindungszeichenfolge hinzu, wenn der SQL-Client dies unterstützt, um bei einem Failover eine schnelle Konnektivität sicherzustellen. 

## <a name="test-failover"></a>Testfailover

Testen Sie das Failover der Verfügbarkeitsgruppe, um die Funktionalität sicherzustellen. 

Führen Sie die folgenden Schritte aus, um das Failover zu testen: 

1. Stellen Sie eine Verbindung mit dem DNN-Listener oder einem Replikat her, indem Sie [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) verwenden. 
1. Erweitern Sie **Always On-Verfügbarkeitsgruppe** im **Objekt-Explorer**. 
1. Klicken Sie mit der rechten Maustaste auf die Verfügbarkeitsgruppe, und wählen Sie **Failover** aus, um den **Failover-Assistenten** zu öffnen. 
1. Gehen Sie gemäß den Eingabeaufforderungen vor, um ein Failoverziel auszuwählen, und führen Sie für die Verfügbarkeitsgruppe ein Failover zu einem sekundären Replikat aus. 
1. Vergewissern Sie sich, dass die Datenbank auf dem neuen primären Replikat im synchronisierten Zustand vorliegt. 
1. (Optional) Führen Sie ein Failback zum ursprünglichen primären Replikat oder einem anderen sekundären Replikat aus. 

## <a name="test-connectivity"></a>Testen der Konnektivität

Testen Sie die Konnektivität Ihres DNN-Listeners mit den folgenden Schritten:

1. Öffnen Sie [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
1. Stellen Sie eine Verbindung mit Ihrem DNN-Listener her. 
1. Öffnen Sie ein neues Abfragefenster, und führen Sie `SELECT @@SERVERNAME` aus, um zu überprüfen Sie, mit welchem Replikat Sie verbunden sind. 
1. Führen Sie für die Verfügbarkeitsgruppe ein Failover zu einem anderen Replikat aus.
1. Warten Sie kurze Zeit, und führen Sie `SELECT @@SERVERNAME` aus, um zu bestätigen, dass Ihre Verfügbarkeitsgruppe jetzt auf einem anderen Replikatserver gehostet wird. 


## <a name="limitations"></a>Einschränkungen

- Derzeit wird ein DNN-Listener für eine Verfügbarkeitsgruppe nur für SQL Server 2019 CU8 und höher unter Windows Server 2016 und höher unterstützt. 
- Es gibt möglicherweise weitere Überlegungen, wenn Sie mit anderen SQL Server-Features und einer Verfügbarkeitsgruppe mit einem DNN arbeiten. Weitere Informationen finden Sie unter [Funktionsinteroperabilität mit VG und DNN-Listener](availability-group-dnn-interoperability.md). 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu SQL Server-HADR-Features in Azure finden Sie unter [Verfügbarkeitsgruppen](availability-group-overview.md) und [Failoverclusterinstanz](failover-cluster-instance-overview.md). Sie können sich auch mit den [bewährten Methoden](hadr-cluster-best-practices.md) zum Konfigurieren Ihrer Umgebung für Hochverfügbarkeit und Notfallwiederherstellung vertraut machen. 



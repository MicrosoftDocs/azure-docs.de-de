---
title: Konfigurieren eines DNN für eine Failoverclusterinstanz
description: Erfahren Sie, wie Sie einen verteilten Netzwerknamen (DNN) konfigurieren, um Datenverkehr an SQL Server in der Azure-VM-Failoverclusterinstanz (FCI) weiterzuleiten.
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
ms.openlocfilehash: 8549592ace00e712929ebc76045a32531b9db659
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358315"
---
# <a name="configure-a-dnn-for-failover-cluster-instance"></a>Konfigurieren eines DNN für eine Failoverclusterinstanz
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In Azure Virtual Machines wird über den Namen des verteilten Netzwerks (Distributed Network Name, DNN) Datenverkehr an die entsprechende Clusterressource weitergeleitet. Dieser Name stellt eine einfachere Möglichkeit zum Herstellen einer Verbindung mit der SQL Server-Failoverclusterinstanz (FCI) dar als der Name des virtuellen Netzwerks (Virtual Network Name, VNN). Zudem ist Azure Load Balancer nicht erforderlich. 

In diesem Artikel erfahren Sie, wie Sie eine DNN-Ressource zum Weiterleiten von Datenverkehr an Ihre Failoverclusterinstanz mit SQL Server auf Azure-VMs für Hochverfügbarkeit und Notfallwiederherstellung (HADR) konfigurieren. 

Das DNN-Feature ist derzeit nur in SQL Server 2019 CU2 und höher sowie unter Windows Server 2016 und höher verfügbar. 

Als alternative Konnektivitätsoption bieten sich ein [Name eines virtuellen Netzwerks und Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) an. 

## <a name="overview"></a>Übersicht

Der Name des verteilten Netzwerks (DNN) ersetzt den Namen des virtuellen Netzwerks (VNN) als Verbindungspunkt bei Verwendung mit einer [Always On-Failoverclusterinstanz auf SQL Server-VMs](failover-cluster-instance-overview.md). Dadurch muss Datenverkehr nicht mehr über eine Azure Load Balancer-Instanz weitergeleitet werden. So lassen sich Bereitstellung und Wartung vereinfachen und das Failover verbessern. 

Bei einer Failoverclusterinstanz-Bereitstellung ist der VNN zwar noch vorhanden, aber der Client stellt eine Verbindung mit dem DNS-Namen des DNN anstatt dem VNN her. 

## <a name="prerequisites"></a>Voraussetzungen 

Bevor Sie die in diesem Artikel aufgeführten Schritte ausführen, sollten Sie über Folgendes verfügen:

- SQL Server 2019 CU2 und höher unter Windows Server 2016 und höher
- Sie sollten entschieden haben, dass der Name des verteilten Netzwerks die geeignete [Konnektivitätsoption für die HADR-Lösung](hadr-cluster-best-practices.md#connectivity) ist.
- Konfigurierte [Failoverclusterinstanzen](failover-cluster-instance-overview.md). 
- Sie müssen die neueste Version von [PowerShell](/powershell/azure/install-az-ps) installiert haben. 

## <a name="create-dnn-resource"></a>Erstellen einer DNN-Ressource 

Die DNN-Ressource wird in derselben Clustergruppe wie die SQL Server-FCI erstellt. Verwenden Sie PowerShell, um die DNN-Ressource in der FCI-Clustergruppe zu erstellen. 

Mit dem folgenden PowerShell-Befehl wird der SQL Server-FCI-Clustergruppe mit dem Ressourcennamen `<dnnResourceName>` eine DNN-Ressource hinzugefügt. Der Ressourcenname wird verwendet, um eine Ressource eindeutig zu identifizieren. Verwenden Sie einen Namen, der für Sie sinnvoll und im gesamten Cluster eindeutig ist. Der Ressourcentyp muss `Distributed Network Name` sein. 

Der `-Group`-Wert muss der Name der Clustergruppe sein, die der SQL Server-FCI entspricht, der Sie den Namen des verteilten Netzwerks hinzufügen möchten. Für eine Standardinstanz ist das typische Format `SQL Server (MSSQLSERVER)`. 


```powershell
Add-ClusterResource -Name <dnnResourceName> `
-ResourceType "Distributed Network Name" -Group "<WSFC role of SQL server instance>"
```

Verwenden Sie z. B. den folgenden PowerShell-Befehl, um die DNN-Ressourcen `dnn-demo` für eine SQL Server-Standard-FCI zu erstellen:

```powershell
Add-ClusterResource -Name dnn-demo `
-ResourceType "Distributed Network Name" -Group "SQL Server (MSSQLSERVER)"

```

## <a name="set-cluster-dnn-dns-name"></a>Festlegen des DNN-DNS-Namens des Clusters

Legen Sie den DNS-Namen für die DNN-Ressource im Cluster fest. Der Cluster verwendet diesen Wert dann zum Weiterleiten von Datenverkehr an den Knoten, der derzeit die SQL Server-FCI hostet. 

Clients verwenden den DNS-Namen, um eine Verbindung mit der SQL Server-FCI herzustellen. Sie können einen eindeutigen Wert auswählen. Wenn Sie bereits über eine vorhandene FCI verfügen und keine Clientverbindungszeichenfolgen aktualisieren möchten, können Sie den DNN so konfigurieren, dass er den aktuellen VNN verwendet, den Clients bereits verwenden. Zu diesem Zweck müssen Sie den [VNN umbenennen](#rename-the-vnn), bevor Sie den DNN in DNS festlegen.

Verwenden Sie diesen Befehl, um den DNS-Namen für den DNN festzulegen: 

```powershell
Get-ClusterResource -Name <dnnResourceName> | `
Set-ClusterParameter -Name DnsName -Value <DNSName>
```

Clients verwenden den `DNSName`-Wert, um eine Verbindung mit der SQL Server-FCI herzustellen. Verwenden Sie beispielsweise den folgenden PowerShell-Befehl, damit Clients eine Verbindung mit `FCIDNN` herstellen:

```powershell
Get-ClusterResource -Name dnn-demo | `
Set-ClusterParameter -Name DnsName -Value FCIDNN
```

Clients geben nun beim Herstellen einer Verbindung mit der SQL Server-FCI `FCIDNN` in ihre Verbindungszeichenfolge ein. 

   > [!WARNING]
   > Löschen Sie den aktuellen Namen des virtuellen Netzwerks nicht, da es sich um eine erforderliche Komponente der FCI-Infrastruktur handelt. 


### <a name="rename-the-vnn"></a>Umbenennen des VNN 

Wenn Sie über einen vorhandenen virtuellen Netzwerknamen verfügen und möchten, dass Clients diesen Wert weiterhin verwenden, um eine Verbindung mit der SQL Server-FCI herzustellen, müssen Sie den aktuellen VNN in einen Platzhalterwert umbenennen. Nachdem der aktuelle VNN umbenannt wurde, können Sie den Wert des DNS-Namens für den DNN auf den VNN festlegen. 

Für das Umbenennen des VNN gelten einige Einschränkungen. Weitere Informationen finden Sie unter [Umbenennen einer FCI](/sql/sql-server/failover-clusters/install/rename-a-sql-server-failover-cluster-instance).

Wenn der aktuelle VNN für Ihr Unternehmen nicht erforderlich ist, überspringen Sie diesen Abschnitt. Nachdem Sie den VNN umbenannt haben, [legen Sie den DNN-DNS-Namen des Clusters fest](#set-cluster-dnn-dns-name). 

   
## <a name="set-dnn-resource-online"></a>Onlineschalten der DNN-Ressource

Nachdem die DNN-Ressource entsprechend benannt wurde und Sie den Wert des DNS-Namens im Cluster festgelegt haben, verwenden Sie PowerShell, um die DNN-Ressource im Cluster online zu schalten: 

```powershell
Start-ClusterResource -Name <dnnResourceName>
```

Verwenden Sie z. B. den folgenden PowerShell-Befehl, um die DNN-Ressource `dnn-demo` zu starten: 

```powershell
Start-ClusterResource -Name dnn-demo
```

## <a name="configure-possible-owners"></a>Konfigurieren möglicher Besitzer

Standardmäßig bindet der Cluster den DNN-DNS-Namen an alle Knoten im Cluster. Knoten im Cluster, die nicht Teil der SQL Server-FCI sind, sollten jedoch aus der Liste der möglichen DNN-Besitzer ausgeschlossen werden. 

Um mögliche Besitzer zu aktualisieren, führen Sie die folgenden Schritte aus:

1. Navigieren Sie im Failovercluster-Manager zu Ihrer DNN-Ressource. 
1. Klicken Sie mit der rechten Maustaste auf die DNN-Ressource, und wählen Sie **Eigenschaften** aus. 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/fci-dnn-properties.png" alt-text="Kontextmenü für die DNN-Ressource mit hervorgehobenem Befehl „Eigenschaften“.":::

1. Deaktivieren Sie das Kontrollkästchen für alle Knoten, die nicht an der Failoverclusterinstanz teilnehmen. Die Liste möglicher Besitzer für die DNN-Ressource sollte mit der Liste möglicher Besitzer für die SQL Server-Instanzressource übereinstimmen. Wenn z. B. Data3 nicht an der FCI teilnimmt, ist die folgende Abbildung ein Beispiel für das Entfernen von Data3 aus der Liste möglicher Besitzer für die DNN-Ressource: 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/clear-check-for-nodes-not-in-fci.png" alt-text="Deaktivieren Sie das Kontrollkästchen neben den Knoten, die nicht an der FCI beteiligt sind, für mögliche Besitzer der DNN-Ressource.":::

1. Klicken Sie auf **OK**, um die Einstellungen zu speichern. 


## <a name="restart-sql-server-instance"></a>Neustarten der SQL Server-Instanz 

Verwenden Sie den Failovercluster-Manager, um die SQL Server-Instanz neu zu starten. Folgen Sie diesen Schritten:

1. Navigieren Sie im Failovercluster-Manager zu Ihrer SQL Server-Ressource.
1. Klicken Sie mit der rechten Maustaste auf die SQL Server-Ressource, und schalten Sie diese offline. 
1. Wenn alle zugeordneten Ressourcen offline sind, klicken Sie mit der rechten Maustaste auf die SQL Server-Ressource, und schalten Sie sie erneut online. 

## <a name="update-connection-string"></a>Aktualisieren der Verbindungszeichenfolge

Um bei einem Failover schnelle Konnektivität sicherzustellen, fügen Sie der Verbindungszeichenfolge `MultiSubnetFailover=True` hinzu, wenn die SQL-Clientversion früher als 4.6.1 ist. 

Wenn der DNN den ursprünglichen VNN nicht verwendet, müssen SQL-Clients, die eine Verbindung mit der SQL Server-FCI herstellen, außerdem ihre Verbindungszeichenfolge auf den DNN-DNS-Namen aktualisieren. Um diese Anforderung zu vermeiden, können Sie den Wert des DNS-Namens auf den Namen des VNN aktualisieren. Zunächst müssen Sie jedoch [den vorhandenen VNN durch einen Platzhalter](#rename-the-vnn) ersetzen. 

## <a name="test-failover"></a>Testfailover


Testen Sie das Failover der Clusterressource, um die Clusterfunktionalität zu validieren. 


Führen Sie die folgenden Schritte aus, um das Failover zu testen: 

1. Stellen Sie mithilfe von RDP eine Verbindung mit einem der SQL Server-Clusterknoten her.
1. Öffnen Sie den **Failovercluster-Manager**. Wählen Sie **Rollen** aus. Achten Sie darauf, welcher Knoten im Besitz der SQL Server-FCI-Rolle ist.
1. Klicken Sie mit der rechten Maustaste auf die SQL Server-FCI-Rolle. 
1. Wählen Sie **Verschieben** aus, und wählen Sie dann **Bestmöglicher Knoten** aus.

Unter **Failovercluster-Manager** wird die Rolle angezeigt, und die Ressourcen werden in den Offlinezustand versetzt. Die Ressourcen werden dann verschoben und auf dem anderen Knoten dann wieder in den Onlinezustand versetzt.

## <a name="test-connectivity"></a>Testen der Konnektivität

Melden Sie sich zum Testen der Konnektivität an einem anderen virtuellen Computer in demselben virtuellen Netzwerk an. Öffnen Sie **SQL Server Management Studio**, und stellen Sie eine Verbindung mit der SQL Server-FCI mithilfe des DNN-DNS-Namens her.

Bei Bedarf können Sie [SQL Server Management Studio herunterladen](/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="avoid-ip-conflict"></a>Vermeiden von IP-Konflikten

Dies ist ein optionaler Schritt, um zu verhindern, dass die von der FCI-Ressource verwendete virtuelle IP-Adresse (VIP-Adresse) einer anderen Ressource in Azure als Duplikat zugewiesen wird. 

Obwohl Kunden jetzt den DNN verwenden, um eine Verbindung mit der SQL Server-FCI herzustellen, können der Name des virtuellen Netzwerks (VNN) und die virtuelle IP-Adresse nicht gelöscht werden, da es sich um erforderliche Komponenten der FCI-Infrastruktur handelt. Da es jedoch keinen Load Balancer mehr gibt, der die virtuelle IP-Adresse in Azure reserviert, besteht das Risiko, dass einer anderen Ressource im virtuellen Netzwerk dieselbe IP-Adresse wie die von der FCI verwendete virtuelle IP-Adresse zugewiesen wird. Dies kann möglicherweise zu einem Konflikt aufgrund der doppelt vorhandenen IP-Adresse führen. 

Konfigurieren Sie eine APIPA-Adresse oder einen dedizierten Netzwerkadapter, um die IP-Adresse zu reservieren. 

### <a name="apipa-address"></a>APIPA-Adresse

Um zu vermeiden, dass doppelte IP-Adressen verwendet werden, konfigurieren Sie eine APIPA-Adresse (auch als Link-Local-Adresse bezeichnet). Führen Sie hierzu den folgenden Befehl aus:

```powershell
Get-ClusterResource "virtual IP address" | Set-ClusterParameter 
    –Multiple @{"Address”=”169.254.1.1”;”SubnetMask”=”255.255.0.0”;"OverrideAddressMatch"=1;”EnableDhcp”=0}
```

In diesem Befehl ist „virtual IP address“ (virtuelle IP-Adresse) der Name der geclusterten VIP-Adressressource, und „169.254.1.1“ ist die für die VIP-Adresse ausgewählte APIPA-Adresse. Wählen Sie die Adresse aus, die am besten zu Ihrem Unternehmen passt. Legen Sie `OverrideAddressMatch=1` fest, damit die IP-Adresse in jedem Netzwerk, einschließlich des APIPA-Adressraums, verwendet werden kann. 

### <a name="dedicated-network-adapter"></a>Dedizierte Netzwerkadapter

Alternativ können Sie einen Netzwerkadapter in Azure konfigurieren, um die von der virtuellen IP-Adressressource verwendete IP-Adresse zu reservieren. Dadurch wird jedoch die Adresse im Subnetzadressraum genutzt, und es entsteht zusätzlicher Mehraufwand, weil sichergestellt werden muss, dass der Netzwerkadapter nicht für andere Zwecke verwendet wird.

## <a name="limitations"></a>Einschränkungen

- Derzeit wird ein DNN mit FCI nur für SQL Server 2019 CU2 oder höher unter Windows Server 2016 und höher unterstützt. 
- Es gibt möglicherweise weitere Überlegungen, wenn Sie mit anderen SQL Server-Features und einer FCI mit einem DNN arbeiten. Weitere Informationen finden Sie unter [FCI mit DNN-Interoperabilität](failover-cluster-instance-dnn-interoperability.md). 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu SQL Server-HADR-Features in Azure finden Sie unter [Verfügbarkeitsgruppen](availability-group-overview.md) und [Failoverclusterinstanz](failover-cluster-instance-overview.md). Sie können sich auch mit den [bewährten Methoden](hadr-cluster-best-practices.md) zum Konfigurieren Ihrer Umgebung für Hochverfügbarkeit und Notfallwiederherstellung vertraut machen. 



---
title: Featureinteroperabilität mit SQL Server-FCI und DNN
description: 'Erfahren Sie mehr über die zusätzlichen Erwägungen beim Arbeiten mit bestimmten SQL Server-Features und einer DNN-Ressource (Distributed Network Name, verteilter Netzwerkname) mit einer Failoverclusterinstanz in SQL Server auf Azure-VMs. '
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 3c92aa3b35240831fad14919dc73609d803c610a
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358213"
---
# <a name="feature-interoperability-with-sql-server-fci--dnn"></a>Featureinteroperabilität mit SQL Server-FCI und DNN
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Es gibt bestimmte SQL Server-Features, für die ein hartcodierter virtueller Netzwerkname (VNN) erforderlich ist. Daher sind einige zusätzliche Aspekte zu beachten, wenn Sie die DNN-Ressource (verteilter Netzwerkname) mit Ihrer Failoverclusterinstanz und SQL Server auf Azure-VMs verwenden. 

In diesem Artikel erfahren Sie, wie Sie den Netzwerkalias konfigurieren, wenn Sie die DNN-Ressource verwenden, und bei welchen SQL Server-Features weitere Aspekte berücksichtigt werden müssen.

## <a name="create-network-alias-fci"></a>Erstellen eines Netzwerkalias (FCI)

Bei einigen serverseitigen Komponenten sind ein hartcodierter VNN-Wert und ein Netzwerkalias, der den VNN dem DNN-DNS-Namen zuordnet, erforderlich, damit sie ordnungsgemäß funktionieren. Führen Sie die Schritte in [Erstellen oder Löschen eines Serveralias für die Verwendung durch einen Client](/sql/database-engine/configure-windows/create-or-delete-a-server-alias-for-use-by-a-client) aus, um einen Alias zu erstellen, der den VNN dem DNN-DNS-Namen zuordnet. 

Für eine Standardinstanz können Sie den VNN direkt dem DNN-DNS-Namen zuordnen, sodass VNN = DNN-DNS-Name.
Wenn der VNN beispielsweise `FCI1` ist, der Instanzname `MSSQLSERVER` und der DNN `FCI1DNN` (Clients haben zuvor eine Verbindung mit `FCI` hergestellt und stellen nun eine Verbindung mit `FCI1DNN` her), ordnen Sie den VNN `FCI1` dem DNN `FCI1DNN` zu. 

Bei benannten Instanzen sollte die Zuordnung des Netzwerkalias für die gesamte Instanz erfolgen, sodass `VNN\Instance` = `DNN\Instance`. Wenn der VNN beispielsweise `FCI1` ist, der Instanzname `instA` und der DNN `FCI1DNN` (Clients haben zuvor eine Verbindung mit `FCI1\instA` hergestellt und stellen nun eine Verbindung mit `FCI1DNN\instaA` her), ordnen Sie den VNN `FCI1\instaA` dem DNN `FCI1DNN\instaA` zu. 



## <a name="client-drivers"></a>Clienttreiber

Bei ODBC-, OLEDB-, ADO.NET-, JDBC-, PHP- und Node.js-Treibern müssen Benutzer den DNN-DNS-Namen explizit als Servernamen in der Verbindungszeichenfolge angeben. Fügen Sie `MultiSubnetFailover=True` zur Verbindungszeichenfolge hinzu, wenn der SQL-Client dies unterstützt, um bei einem Failover eine schnelle Konnektivität sicherzustellen. 

## <a name="tools"></a>Tools

Benutzer von [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms), [sqlcmd](/sql/tools/sqlcmd-utility), [Azure Data Studio](/sql/azure-data-studio/what-is) und [SQL Server Data Tools](/sql/ssdt/sql-server-data-tools) müssen den DNN-DNS-Namen explizit als Servernamen in der Verbindungszeichenfolge angeben. 

## <a name="availability-groups-and-fci"></a>Verfügbarkeitsgruppen und FCI

Sie können eine Always On-Verfügbarkeitsgruppe konfigurieren, indem Sie eine Failoverclusterinstanz als eines der Replikate verwenden. Bei dieser Konfiguration muss die Spiegelungsendpunkt-URL für das FCI-Replikat den FCI-DNN verwenden. Wenn die FCI als schreibgeschütztes Replikat verwendet wird, muss für das schreibgeschützte Routing zum FCI-Replikat ebenfalls der FCI-DNN verwendet werden. 

Das Format für den Spiegelungsendpunkt ist: `ENDPOINT_URL = 'TCP://<DNN DNS name>:<mirroring endpoint port>'`. 

Wenn der DNN-DNS-Name beispielsweise `dnnlsnr` ist und `5022` der Port des Spiegelungsendpunkts der FCI ist, sieht der Transact-SQL-Codeausschnitt (T-SQL) zum Erstellen der Endpunkt-URL wie folgt aus: 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

Das Format für die URL für das schreibgeschützte Routing ist dann: `TCP://<DNN DNS name>:<SQL Server instance port>`. 

Wenn der DNN-DNS-Name beispielsweise `dnnlsnr` ist und `1444` der Port ist, der von der schreibgeschützten Ziel-SQL Server-FCI verwendet wird, sieht der T-SQL-Codeausschnitt zum Erstellen der URL für das schreibgeschützte Routing wie folgt aus: 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

Sie können den Port in der URL weglassen, wenn es sich um den Standardport 1433 handelt. Bei einer benannten Instanz konfigurieren Sie einen statischen Port für die benannte Instanz und geben diesen in der URL für das schreibgeschützte Routing an.  

## <a name="replication"></a>Replikation

Die Replikation verfügt über drei Komponenten: Verleger, Verteiler und Abonnent. Jede dieser Komponenten kann eine Failoverclusterinstanz sein. Da der FCI-VNN häufig in der Replikationskonfiguration verwendet wird (sowohl explizit als auch implizit), ist möglicherweise ein Netzwerkalias erforderlich, der den VNN dem DNN zuordnet, damit die Replikation funktioniert. 

Verwenden Sie den VNN bei der Replikation weiterhin als FCI-Namen, erstellen Sie jedoch in den folgenden Remotesituationen *vor der Konfiguration der Replikation* einen Netzwerkalias:

| **Replikationskomponente (FCI mit DNN)** | **Remotekomponente** | **Netzwerkalias-Zuordnung** | **Server mit Netzwerkzuordnung**| 
|---------|---------|---------|-------- | 
|Herausgeber | Verteiler | Verleger-VNN zu Verleger-DNN| Verteiler| 
|Verteiler|Subscriber |Verteiler-VNN zu Verteiler-DNN| Subscriber | 
|Verteiler|Herausgeber | Verteiler-VNN zu Verteiler-DNN | Herausgeber| 
|Subscriber| Verteiler| Abonnenten-VNN zu Abonnenten-DNN | Verteiler| 

Angenommen, Sie verfügen beispielsweise über einen Herausgeber, der als FCI mit DNN in einer Replikationstopologie konfiguriert ist, und einen Remoteverteiler. Erstellen Sie in diesem Fall einen Netzwerkalias auf dem Verteilerserver, um den Verleger-VNN dem Verleger-DNN zuzuordnen: 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-in-configuration-manager.png" alt-text="Konfigurieren Sie den DNN-DNS-Namen als Netzwerkalias mit dem SQL Server-Konfigurations-Manager." :::

Verwenden Sie bei einer benannten Instanz den vollständigen Instanznamen, wie in der folgenden Beispielabbildung zu sehen: 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-named-instance-configuration-manager.png" alt-text="Verwenden Sie beim Konfigurieren eines Netzwerkalias für eine benannte Instanz den vollständigen Instanznamen." :::

## <a name="database-mirroring"></a>Datenbankspiegelung

Sie können für die Datenbankspiegelung jeden der Datenbank-Spiegelungspartner als FCI konfigurieren. Konfigurieren Sie sie mit [Transact-SQL (T-SQL)](/sql/database-engine/database-mirroring/example-setting-up-database-mirroring-using-windows-authentication-transact-sql) statt über die SQL Server Management Studio-GUI. Durch die Verwendung von T-SQL wird sichergestellt, dass der Datenbankspiegelungs-Endpunkt mit dem DNN statt mit dem VNN erstellt wird. 

Wenn der DNN-DNS-Name beispielsweise `dnnlsnr` ist und der Datenbankspiegelungs-Endpunkt 7022, wird mit dem folgenden T-SQL-Codeausschnitt der Datenbank-Spiegelungspartner konfiguriert: 

```sql
ALTER DATABASE AdventureWorks
    SET PARTNER =
    'TCP://dnnlsnr:7022'
GO 
```

Für den Clientzugriff kann die Eigenschaft **Failoverpartner** das Datenbankspiegelungs-Failover verarbeiten, aber nicht das FCI-Failover. 

## <a name="msdtc"></a>MSDTC

Die FCI kann an vom Microsoft Distributed Transaction Coordinator (MS DTC) koordinierten verteilten Transaktionen teilnehmen. Auch wenn sowohl der gruppierte MS DTC als auch der lokale MS DTC mit FCI-DNN unterstützt werden, ist in Azure dennoch ein Lastenausgleich für den gruppierten MS DTC erforderlich. Der in der FCI definierte DNN ersetzt nicht die Azure Load Balancer-Anforderung für den gruppierten MS DTC in Azure. 

## <a name="filestream"></a>FileStream

Auch wenn FileStream für Datenbanken in einer FCI unterstützt wird, wird der Zugriff auf FileStream oder die Dateitabelle über Dateisystem-APIs mit DNN nicht unterstützt. 

## <a name="linked-servers"></a>Verbindungsserver

Die Verwendung eines Verbindungsservers mit einem FCI-DNN wird unterstützt. Verwenden Sie entweder den DNN direkt für die Konfiguration eines Verbindungsservers, oder verwenden Sie einen Netzwerkalias, um den VNN dem DNN zuzuordnen. 


Verwenden Sie z. B. den folgenden Transact-SQL-Befehl (T-SQL), um einen Verbindungsserver mit dem DNN-DNS-Namen `dnnlsnr` für die benannte Instanz `insta1` zu erstellen:

```sql
USE [master]   
GO   

EXEC master.dbo.sp_addlinkedserver    
    @server = N'dnnlsnr\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 
```

Alternativ können Sie den Verbindungsserver mit dem virtuellen Netzwerknamen (VNN) erstellen. Sie müssen dann jedoch einen Netzwerkalias definieren, um den VNN dem DNN zuzuordnen. 

Verwenden Sie z B. für den Instanznamen `insta1`, den VNN `vnnname` und den DNN `dnnlsnr` den folgenden Transact-SQL-Befehl (T-SQL), um einen Verbindungsserver mit dem VNN zu erstellen:

```sql
USE [master]
GO   

EXEC master.dbo.sp_addlinkedserver   
    @server = N'vnnname\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 

```

Erstellen Sie dann einen Netzwerkalias, um `vnnname\insta1` `dnnlsnr\insta1` zuzuordnen. 



## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen


- Welche SQL Server-Version bietet DNN-Unterstützung? 

   SQL Server 2019 CU2 und höher

- Wie lange ist die erwartete Failoverzeit bei der Verwendung eines DNN?

   Bei der Verwendung eines DNN entspricht die Failoverzeit lediglich der FCI-Failoverzeit. Es kommt keine zusätzliche Zeit hinzu (wie die Testzeit bei der Verwendung von Azure Load Balancer).

- Gibt es eine Versionsanforderung für SQL-Clients für die Unterstützung eines DNN mit OLEDB und ODBC?

   Für DNNs wird die Unterstützung mit der Verbindungszeichenfolge `MultiSubnetFailover=True` empfohlen. Diese steht ab SQL Server 2012 (11.x) zur Verfügung.

- Sind in SQL Server für die Verwendung eines DNN Konfigurationsänderungen erforderlich? 

   Für die Verwendung eines DNN sind keine Konfigurationsänderungen in SQL Server erforderlich. Bei einigen SQL Server-Features müssen jedoch möglicherweise weitere Aspekte berücksichtigt werden. 

- Unterstützen DNNs Cluster mit mehreren Subnetzen?

   Ja. Der Cluster verknüpft den DNN im DNS mit den physischen IP-Adressen aller Knoten im Cluster unabhängig vom Subnetz. Der SQL-Client probiert unabhängig vom Subnetz alle IP-Adressen des DNS-Namens aus. 



## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter 

- [Windows-Clustertechnologie](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server-Failoverclusterinstanzen](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)


---
title: Verwaltete Azure SQL-Datenbank-Instanz – Herstellen einer Verbindung mit einer Anwendung | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie Sie eine Verbindung zwischen einer Anwendung und einer verwalteten Azure SQL-Datenbank-Instanz herstellen.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 11/09/2018
ms.openlocfilehash: 6cbfdc9e595ebdf682356990ec975dbd0514035d
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297089"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Herstellen einer Verbindung zwischen einer Anwendung und einer verwalteten Azure SQL-Datenbank-Instanz

Heutzutage haben Sie mehrere Möglichkeiten bei der Wahl, wie und wo Sie Anwendungen hosten.

Beispielsweise können Sie eine Anwendung unter Verwendung von Azure App Service oder einiger der integrierten Optionen des virtuellen Netzwerks (VNET) von Azure (z.B. Azure App Service-Umgebung, virtueller Computer, VM-Skalierungsgruppe) in der Cloud hosten. Alternativ können Sie auch den Hybrid Cloud-Ansatz wählen und Ihre Anwendungen lokal hosten.

Unabhängig von Ihrer Wahl können Sie die Anwendungen mit einer verwalteten Instanz verbinden.  

![Hochverfügbarkeit](./media/sql-database-managed-instance/application-deployment-topologies.png)

## <a name="connect-an-application-inside-the-same-vnet"></a>Herstellen einer Verbindung mit einer Anwendung im selben VNET

Dies ist das einfachste Szenario. Zwischen virtuellen Computern innerhalb des VNET kann eine direkte Verbindung hergestellt werden, auch wenn sie sich in unterschiedlichen Subnetzen befinden. Zum Herstellen einer Verbindung mit einer Anwendung in einer Azure-Anwendungsumgebung oder einem virtuellen Computer müssen Sie lediglich die Verbindungszeichenfolge entsprechend festlegen.  

## <a name="connect-an-application-inside-a-different-vnet"></a>Herstellen einer Verbindung mit einer Anwendung in einem anderen VNET

Dieses Szenario ist etwas komplexer, da die verwaltete Instanz über eine private IP-Adresse in ihrem eigenen VNET verfügt. Für die Verbindung ist für eine Anwendung der Zugriff auf das VNET erforderlich, in dem die verwaltete Instanz bereitgestellt wird. Daher müssen Sie zunächst eine Verbindung zwischen der Anwendung und dem VNET der verwalteten Instanz herstellen. Die VNETs müssen für dieses Szenario nicht zum selben Abonnement gehören.

Es gibt zwei Optionen zum Herstellen einer Verbindung mit VNETs:

- [Peering in virtuellen Azure-Netzwerken](../virtual-network/virtual-network-peering-overview.md)
- VNET-zu-VNET-VPN-Gateway ([Azure-Portal](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

Dabei ist die Option des Peerings vorzuziehen, da beim Peering das Microsoft-Backbonenetzwerk verwendet wird, sodass es im Hinblick auf die Konnektivität keinen erkennbaren Unterschied der Latenz zwischen virtuellen Computern im Peering-VNET und im selben VNET gibt. VNET-Peering ist auf die Netzwerke in der gleichen Region beschränkt.  

> [!IMPORTANT]
> Das VNET-Peering-Szenario für die verwaltete Instanz ist aufgrund der [Einschränkungen beim globalen Peering virtueller Netzwerke](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) auf die Netzwerke in der gleichen Region beschränkt.

## <a name="connect-an-on-premises-application"></a>Herstellen einer Verbindung mit einer lokalen Anwendung

Auf eine verwaltete Instanz kann nur über eine private IP-Adresse zugegriffen werden. Für den Zugriff auf die verwaltete Instanz über die lokale Umgebung müssen Sie eine Standort-zu-Standort-Verbindung zwischen der Anwendung und dem VNET der verwalteten Instanz herstellen.

Es gibt zwei Optionen für die lokale Verbindung mit dem Azure-VNET:

- Site-to-Site-VPN-Verbindung ([Azure-Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- [ExpressRoute](../expressroute/expressroute-introduction.md)-Verbindung  

Wenn Sie eine lokale Verbindung mit Azure hergestellt haben und keine Verbindung mit der verwalteten Instanz herstellen können, sollten Sie überprüfen, ob für die Firewall eine geöffnete ausgehende Verbindung am SQL-Port 1433 und ein geöffneter Portbereich 11000-11999 für die Umleitung festgelegt sind.

## <a name="connect-an-application-on-the-developers-box"></a>Herstellen einer Verbindung mit einer Anwendung in der Entwicklerbox

Auf eine verwaltete Instanz kann nur über eine private IP-Adresse zugegriffen werden. Für den Zugriff auf die verwaltete Instanz über die Entwicklerbox müssen Sie daher zunächst eine Verbindung zwischen der Entwicklerbox und dem VNET der verwalteten Instanz herstellen. Konfigurieren Sie hierfür eine Point-to-Site-Verbindung mit einem VNET unter Verwendung der nativen Azure-Zertifikatauthentifizierung. Weitere Informationen finden Sie unter [Konfigurieren einer Point-to-Site-Verbindung zwischen einem lokalen Computer und einer verwalteten Azure SQL-Datenbank-Instanz](sql-database-managed-instance-configure-p2s.md).

## <a name="connect-from-on-premises-with-vnet-peering"></a>Herstellen einer Verbindung mit einer lokalen Umgebung mittels VNET-Peering

Ein weiteres Szenario, das von Kunden implementiert wird, ist die Installation eines VPN-Gateways in einem separaten virtuellen Netzwerk und einem Abonnement des Netzwerks, das die verwaltete Instanz hostet. Beide virtuelle Netzwerke werden dann mittels Peering miteinander verbunden. Die folgende Beispielarchitekturabbildung zeigt, wie dies implementiert werden kann.

![VNet-Peering](./media/sql-database-managed-instance-connect-app/vnet-peering.png)

Nachdem Sie die grundlegende Infrastruktur eingerichtet haben, müssen Sie einige Einstellungen ändern, damit das VPN-Gateway die IP-Adressen im virtuellen Netzwerk, das die verwaltete Instanz hostet, erkennen kann. Nehmen Sie zu diesem Zweck die folgenden sehr spezifischen Änderungen unter **Peeringeinstellungen** vor.

1. Navigieren Sie im VNET, das das VPN-Gateway hostet, zu **Peerings** und dann zu der Verbindung, die mittels Peering zwischen der verwalteten Instanz und dem VNET hergestellt wurde, und klicken Sie dann auf **Gatewaytransit zulassen**.
2. Navigieren Sie im VNET, das die verwaltete Instanz hostet, zu **Peerings** und dann zu der Verbindung, die mittels Peering zwischen dem VPN-Gateway und dem VNET hergestellt wurde, und klicken Sie dann auf **Remotegateways verwenden**.

## <a name="connect-an-azure-app-service-hosted-application"></a>Herstellen einer Verbindung mit einer über Azure App Service gehosteten Anwendung

Auf eine verwaltete Instanz kann nur über eine private IP-Adresse zugegriffen werden. Für den Zugriff auf die verwaltete Instanz über Azure App Service müssen Sie daher zunächst eine Verbindung zwischen der Anwendung und dem VNET der verwalteten Instanz herstellen. Siehe [Integrieren Ihrer App in ein Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md).  

Informationen zur Problembehandlung finden Sie unter [Problembehandlung bei VNETs und Anwendungen](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Wenn keine Verbindung hergestellt werden kann, versuchen Sie, [die Netzwerkkonfiguration zu synchronisieren](sql-database-managed-instance-sync-network-configuration.md).

Ein spezieller Fall beim Herstellen einer Verbindung zwischen Azure App Service mit einer verwalteten Instanz besteht, wenn Sie Azure App Service in ein Netzwerk integriert haben, das mittels Peering mit dem VNET der verwalteten Instanz verbunden ist. In diesem Fall muss die folgende Konfiguration eingerichtet werden:

- Für das VNET der verwalteten Instanz darf KEIN Gateway eingerichtet sein.  
- Für das VNET der verwalteten Instanz muss die Option „Remotegateways verwenden“ aktiviert sein.
- Für das mittels Peering verbundene VNET muss die Option „Gatewaytransit zulassen“ aktiviert sein.

Dieses Szenario ist in der folgenden Abbildung dargestellt:

![Peering integrierte App](./media/sql-database-managed-instance/integrated-app-peering.png)

>[!NOTE]
>Die VNET-Integrationsfunktion umfasst keine VNET-App mit einem ExpressRoute-Gateway. Auch wenn das ExpressRoute-Gateway im Koexistenzmodus konfiguriert ist, funktioniert die VNET-Integration nicht. Wenn Sie über eine ExpressRoute-Verbindung auf Ressourcen zugreifen müssen, können Sie dazu eine App Service-Umgebung nutzen, die in Ihrem VNET ausgeführt wird.

## <a name="troubleshooting-connectivity-issues"></a>Behandlung von Konnektivitätsproblemen

Prüfen Sie zur Behandlung von Konnektivitätsproblemen Folgendes:

- Wenn Sie im gleichen VNET keine Verbindung zwischen einem virtuellen Azure-Computer und einer verwalteten Instanz herstellen können, überprüfen Sie, ob eine Netzwerksicherheitsgruppe im VM-Subnetz festgelegt ist, das den Zugriff möglicherweise blockiert. Beachten Sie außerdem, dass Sie ausgehende Verbindungen an SQL-Port 1433 sowie Ports im Bereich von 11000-11999 öffnen müssen, da diese zum Herstellen einer Verbindung per Umleitung innerhalb von Azure benötigt werden.
- Stellen Sie sicher, dass die BGP-Weitergabe für die Routingtabelle, die dem VNET zugeordnet ist, auf **Aktiviert** festgelegt ist.
- Wenn Sie ein P2S-VPN verwenden, überprüfen Sie, ob in der Konfiguration im Azure-Portal Zahlen zu **Eingehend/ausgehend** angezeigt werden. Zahlen ungleich 0 geben an, dass Datenverkehr von Azure in bzw. aus lokalen Umgebungen weitergeleitet wird.

   ![Zahlen zu „Eingehend/ausgehend“](./media/sql-database-managed-instance-connect-app/ingress-egress-numbers.png)

- Stellen Sie sicher, dass der Clientcomputer (auf dem der VPN-Client ausgeführt wird) für alle VNETs Routeneinträge enthält, auf die Sie zugreifen müssen. Die Routen werden in `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt` gespeichert.

   ![route.txt](./media/sql-database-managed-instance-connect-app/route-txt.png)

   Wie in dieser Abbildung gezeigt wird, gibt es zwei Einträge für jedes beteiligte VNET und einen dritten Eintrag für den VPN-Endpunkt, der im Portal konfiguriert ist.

   Eine weitere Möglichkeit, die Routen zu überprüfen, bietet der folgende Befehl. Die Ausgabe zeigt die Routen zu den verschiedenen Subnetzen:

   ```cmd
   C:\ >route print -4
   ===========================================================================
   Interface List
   14...54 ee 75 67 6b 39 ......Intel(R) Ethernet Connection (3) I218-LM
   57...........................rndatavnet
   18...94 65 9c 7d e5 ce ......Intel(R) Dual Band Wireless-AC 7265
   1...........................Software Loopback Interface 1
   Adapter===========================================================================

   IPv4 Route Table
   ===========================================================================
   Active Routes:
   Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0       10.83.72.1     10.83.74.112     35
         10.0.0.0    255.255.255.0         On-link       172.26.34.2     43
         10.4.0.0    255.255.255.0         On-link       172.26.34.2     43
   ===========================================================================
   Persistent Routes:
   None
   ```

- Stellen Sie beim VNET-Peering sicher, dass Sie die Anweisungen für die Einstellung [„Gatewaytransit zulassen“ und „Remotegateways verwenden“](#connect-from-on-premises-with-vnet-peering) befolgt haben.

## <a name="required-versions-of-drivers-and-tools"></a>Erforderliche Versionen von Treibern und Tools

Die folgenden Mindestversionen der Tools und Treiber werden empfohlen, wenn Sie eine Verbindung zu einer verwalteten Instanz herstellen möchten:

| Treiber/Tool | Version |
| --- | --- |
|.NET Framework | 4.6.1 (oder .NET Core) |
|ODBC-Treiber| v17 |
|PHP-Treiber| 5.2.0 |
|JDBC-Treiber| 6.4.0 |
|Node.js-Treiber| 2.1.1 |
|OLEDB-Treiber| 18.0.2.0 |
|SSMS| 18.0 oder [höher](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) oder höher |

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur verwalteten Instanz finden Sie unter [Was ist eine verwaltete Instanz?](sql-database-managed-instance.md)
- Ein Tutorial, in dem die Erstellung einer neuen verwalteten Instanz gezeigt wird, finden Sie unter [Create an Azure SQL Database Managed Instance in the Azure portal](sql-database-managed-instance-get-started.md) (Erstellen einer verwalteten Azure SQL-Datenbank-Instanz im Azure-Portal).

---
title: Herstellen einer Verbindung zwischen einer Anwendung und SQL Managed Instance
titleSuffix: Azure SQL Managed Instance
description: In diesem Artikel wird erläutert, wie Sie eine Verbindung zwischen einer Anwendung und Azure SQL Managed Instance herstellen.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, vanto
ms.date: 11/09/2018
ms.openlocfilehash: dd5c6527cd6a0beea291dce94ff0e5949ba00671
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791255"
---
# <a name="connect-your-application-to-azure-sql-managed-instance"></a>Herstellen einer Verbindung zwischen einer Anwendung und SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Heutzutage haben Sie mehrere Möglichkeiten bei der Wahl, wie und wo Sie Anwendungen hosten.

Beispielsweise können Sie eine Anwendung unter Verwendung von Azure App Service oder einiger der integrierten Optionen des virtuellen Netzwerks (VNET) von Azure (z .B. Azure App Service-Umgebung, Azure Virtual Machines und VM-Skalierungsgruppe) in der Cloud hosten. Alternativ können Sie auch den Hybrid Cloud-Ansatz wählen und Ihre Anwendungen lokal hosten.

Unabhängig von Ihrer Wahl können Sie sie mit Azure SQL Managed Instance verbinden. 

![Hochverfügbarkeit](./media/connect-application-instance/application-deployment-topologies.png)

In diesem Artikel wird beschrieben, wie eine Anwendung in verschiedenen Anwendungsszenarien mit Azure SQL Managed Instance verbunden werden kann. 

## <a name="connect-inside-the-same-vnet"></a>Herstellen einer Verbindung im selben VNET

Das Verbinden einer Anwendung innerhalb desselben virtuellen Netzwerks wie SQL Managed Instance ist das einfachste Szenario. Zwischen virtuellen Computern innerhalb des virtuellen Netzwerks kann eine direkte Verbindung hergestellt werden, auch wenn sie sich in unterschiedlichen Subnetzen befinden. Zum Herstellen einer Verbindung mit einer Anwendung in einer Azure App Service-Umgebung oder einem virtuellen Computer müssen Sie lediglich die Verbindungszeichenfolge entsprechend festlegen.  

## <a name="connect-inside-a-different-vnet"></a>Herstellen einer Verbindung in einem anderen VNET

Das Herstellen einer Verbindung zwischen einer Anwendung, wenn sich diese in einem anderen virtuellen Netzwerk als SQL Managed Instance befindet, ist etwas komplexer, da SQL Managed Instance in seinem eigenen virtuellen Netzwerk private IP-Adressen hat. Für die Verbindung muss eine Anwendung auf das virtuelle Netzwerk zugreifen können, in dem SQL Managed Instance bereitgestellt ist. Daher müssen Sie zunächst eine Verbindung zwischen der Anwendung und dem virtuellen Netzwerk von SQL Managed Instance herstellen. Die virtuelle Netzwerke müssen für dieses Szenario nicht zum selben Abonnement gehören.

Es gibt zwei Optionen zum Verbinden virtueller Netzwerke:

- [Azure-VNET-Peering](../../virtual-network/virtual-network-peering-overview.md)
- VNET-zu-VNET-VPN-Gateway ([Azure-Portal](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure CLI](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

Dabei ist Peering vorzuziehen, da dabei das Microsoft-Backbonenetzwerk verwendet wird, sodass es im Hinblick auf die Konnektivität keinen spürbaren Unterschied bei der Latenz zwischen virtuellen Computern im Peering-VNET und im selben VNET gibt. VNET-Peering wird zwischen Netzwerken in der gleichen Region unterstützt. Globales Peering virtueller Netzwerke wird ebenfalls unterstützt. Die einzige Einschränkung ist im folgenden Hinweis beschrieben.  

> [!IMPORTANT]
> [Am 22.09.2020 haben wir globales Peering virtueller Netzwerke für neu erstellte virtuelle Cluster angekündigt](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Dies bedeutet, dass globales Peering virtueller Netzwerke sowohl für SQL Managed Instance-Bereitstellungen, die nach dem Ankündigungsdatum in leeren Subnetzen erstellt wurden, als auch für alle späteren verwalteten Instanzen, die in diesen Subnetzen erstellt werden, unterstützt wird. Für alle anderen SQL Managed Instance-Bereitstellungen ist die Peeringunterstützung aufgrund der [Einschränkungen beim globalen Peering virtueller Netzwerke](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) auf die Netzwerke in derselben Region beschränkt. Ausführliche Informationen finden Sie im entsprechenden Abschnitt des Artikels [Azure Virtual Network – häufig gestellte Fragen](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). 

## <a name="connect-from-on-premises"></a>Herstellen einer Verbindung mit einer lokalen Anwendung 

Sie können auch Ihre lokale Anwendung mit SQL Managed Instance verbinden. Auf SQL Managed Instance kann nur über eine private IP-Adresse zugegriffen werden. Für den Zugriff darauf in der lokalen Umgebung müssen Sie eine Site-zu-Site-Verbindung zwischen der Anwendung und dem virtuellen Netzwerk von SQL Managed Instance herstellen.

Es gibt zwei Optionen, um lokal eine Verbindung mit einem virtuellen Azure-Netzwerk herzustellen:

- Site-to-Site-VPN-Verbindung ([Azure-Portal](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- [Azure ExpressRoute](../../expressroute/expressroute-introduction.md)-Verbindung  

Wenn Sie lokal eine Verbindung mit Azure hergestellt haben und keine Verbindung mit SQL Managed Instance herstellen können, prüfen Sie, ob für die Firewall eine geöffnete ausgehende Verbindung am SQL-Port 1433 und der Portbereich 11000-11999 für die Umleitung festgelegt sind.

## <a name="connect-the-developer-box"></a>Herstellen einer Verbindung mit dem Computer des Entwicklers

Es ist auch möglich, den Computer des Entwicklers mit SQL Managed Instance zu verbinden. Auf SQL Managed Instance kann nur über eine private IP-Adresse zugegriffen werden. Für den Zugriff darauf auf dem Computer des Entwicklers müssen Sie daher zunächst eine Verbindung zwischen diesem Computer und dem virtuellen Netzwerk von SQL Managed Instance herstellen. Konfigurieren Sie hierfür eine Point-to-Site-Verbindung mit einem virtuellen Netzwerk unter Verwendung der nativen Azure-Zertifikatauthentifizierung. Weitere Informationen finden Sie unter [Konfigurieren einer Point-to-Site-Verbindung zwischen einem lokalen Computer und Azure SQL Managed Instance](point-to-site-p2s-configure.md).

## <a name="connect-with-vnet-peering"></a>Herstellen einer Verbindung mit VNET-Peering

Ein weiteres Szenario von Kunden ist die Installation eines VPN-Gateways in einem separaten virtuellen Netzwerk und einem Abonnement des Netzwerks, in dem SQL Managed Instance gehostet wird. Beide virtuelle Netzwerke werden dann mittels Peering miteinander verbunden. Die folgende Beispielarchitekturabbildung zeigt, wie dies implementiert werden kann.

![Peering in virtuellen Netzwerken](./media/connect-application-instance/vnet-peering.png)

Nachdem Sie die grundlegende Infrastruktur eingerichtet haben, müssen Sie einige Einstellungen ändern, damit das VPN-Gateway die IP-Adressen im virtuellen Netzwerk, das SQL Managed Instance hostet, erkennen kann. Nehmen Sie zu diesem Zweck die folgenden sehr spezifischen Änderungen unter **Peeringeinstellungen** vor.

1. Navigieren Sie im virtuelle Netzwerk, das das VPN-Gateway hostet, zu **Peerings** und dann zur Verbindung, die mittels Peering zwischen SQL Managed Instance und dem virtuellen Netzwerk hergestellt wurde. Klicken Sie dann auf **Gatewaytransit zulassen** .
2. Navigieren Sie im virtuellen Netzwerk, das SQL Managed Instance hostet, zu **Peerings** und dann zur Verbindung, die mittels Peering zwischen dem VPN-Gateway und dem virtuellen Netzwerk hergestellt wurde. Klicken Sie dann auf **Remotegateways verwenden** .

## <a name="connect-azure-app-service"></a>Herstellen einer Verbindung mit Azure App Service 

Sie können auch eine Verbindung mit einer von Azure App Service gehosteten Anwendung herstellen. Auf SQL Managed Instance kann nur über eine private IP-Adresse zugegriffen werden. Für den Zugriff darauf über Azure App Service müssen Sie daher zunächst eine Verbindung zwischen der Anwendung und dem virtuellen Netzwerk von SQL Managed Instance herstellen. Weitere Informationen finden Sie unter [Integrieren Ihrer App in ein virtuelles Azure-Netzwerk](../../app-service/web-sites-integrate-with-vnet.md).  

Informationen zur Problembehandlung finden Sie unter [Problembehandlung bei virtuellen Netzwerken und Anwendungen](../../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Wenn keine Verbindung hergestellt werden kann, versuchen Sie, [die Netzwerkkonfiguration zu synchronisieren](azure-app-sync-network-configuration.md).

Ein spezieller Fall beim Herstellen einer Verbindung zwischen Azure App Service mit SQL Managed Instance besteht, wenn Sie Azure App Service in ein Netzwerk integriert haben, das mittels Peering mit dem virtuellen Netzwerk von SQL Managed Instance verbunden ist. In diesem Fall muss die folgende Konfiguration eingerichtet werden:

- Das virtuelle Netzwerk von SQL Managed Instance darf KEIN Gateway aufweisen.  
- Für das virtuelle Netzwerk von SQL Managed Instance muss die Option `Use remote gateways` festgelegt sein.
- Für das virtuelle Netzwerk mit Peering muss die Option `Allow gateway transit` festgelegt sein.

Dieses Szenario ist in der folgenden Abbildung dargestellt:

![Integriertes App-Peering](./media/connect-application-instance/integrated-app-peering.png)

>[!NOTE]
>Bei der von einem Gateway abhängigen VNET-Integration wird eine App nicht in ein virtuelles Netzwerk mit einem ExpressRoute-Gateway integriert. Auch wenn das ExpressRoute-Gateway im Koexistenzmodus konfiguriert ist, funktioniert die VNET-Integration nicht. Wenn Sie über eine ExpressRoute-Verbindung auf Ressourcen zugreifen müssen, können Sie dazu eine App Service-Umgebung nutzen, die in Ihrem virtuellen Netzwerk ausgeführt wird.

## <a name="troubleshooting-connectivity-issues"></a>Behandlung von Konnektivitätsproblemen

Prüfen Sie zur Behandlung von Konnektivitätsproblemen Folgendes:

- Wenn Sie im gleichen virtuellen Netzwerk keine Verbindung zwischen einer Azure-VM und SQL Managed Instance herstellen können, aber in einem anderen Subnetz, prüfen Sie, ob eine Netzwerksicherheitsgruppe im VM-Subnetz festgelegt ist, die den Zugriff möglicherweise blockiert. Beachten Sie außerdem, dass Sie ausgehende Verbindungen an SQL-Port 1433 sowie Ports im Bereich von 11000-11999 öffnen müssen, da diese zum Herstellen einer Verbindung per Umleitung innerhalb der Azure-Begrenzung benötigt werden.
- Stellen Sie sicher, dass die BGP-Weitergabe für die Routingtabelle, die dem virtuellen Netzwerk zugeordnet ist, auf **Aktiviert** festgelegt ist.
- Wenn Sie ein P2S-VPN verwenden, überprüfen Sie, ob in der Konfiguration im Azure-Portal Zahlen zu **Eingehend/ausgehend** angezeigt werden. Zahlen ungleich 0 geben an, dass Datenverkehr von Azure in bzw. aus lokalen Umgebungen weitergeleitet wird.

   ![Zahlen zu „Eingehend/ausgehend“](./media/connect-application-instance/ingress-egress-numbers.png)

- Stellen Sie sicher, dass der Clientcomputer (auf dem der VPN-Client ausgeführt wird) für alle virtuellen Netzwerke Routingeinträge enthält, auf die Sie zugreifen müssen. Die Routen werden in `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt` gespeichert.

   ![route.txt](./media/connect-application-instance/route-txt.png)

   Wie in dieser Abbildung gezeigt wird, gibt es zwei Einträge für jedes beteiligte virtuelle Netzwerk und einen dritten Eintrag für den VPN-Endpunkt, der im Portal konfiguriert ist.

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

- Stellen Sie beim VNET-Peering sicher, dass Sie die Anweisungen für die Einstellung [„Gatewaytransit zulassen“ und „Remotegateways verwenden“](#connect-from-on-premises) befolgt haben.

- Wenn Sie das VNET-Peering zum Verbinden einer in Azure App Service gehosteten Anwendung verwenden und das virtuelle Netzwerk von SQL Managed Instance über einen öffentlichen IP-Adressbereich verfügt, stellen Sie sicher, dass die Einstellungen Ihrer gehosteten Anwendung das Weiterleiten des ausgehenden Datenverkehrs an öffentliche IP-Netzwerke zulassen. Befolgen Sie die Anweisungen unter [Regionale Integration des virtuellen Netzwerks](../../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration).

## <a name="required-versions-of-drivers-and-tools"></a>Erforderliche Versionen von Treibern und Tools

Die folgenden Mindestversionen der Tools und Treiber werden empfohlen, wenn Sie eine Verbindung mit SQL Managed Instance herstellen möchten:

| Treiber/Tool | Version |
| --- | --- |
|.NET Framework | 4.6.1 (oder .NET Core) |
|ODBC-Treiber| v17 |
|PHP-Treiber| 5.2.0 |
|JDBC-Treiber| 6.4.0 |
|Node.js-Treiber| 2.1.1 |
|OLEDB-Treiber| 18.0.2.0 |
|SSMS| 18.0 oder [höher](/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) oder höher |

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu SQL Managed Instance finden Sie unter [Was ist SQL Managed Instance?](sql-managed-instance-paas-overview.md).
- Ein Tutorial, in dem die Erstellung einer neuen verwalteten Instanz gezeigt wird, finden Sie unter [Schnellstart: Erstellen einer verwalteten Azure SQL-Instanz](instance-create-quickstart.md).
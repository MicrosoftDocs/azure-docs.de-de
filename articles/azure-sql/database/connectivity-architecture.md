---
title: Verbindungsarchitektur der Azure SQL-Datenbank
description: In diesem Artikel wird die Verbindungsarchitektur in Azure SQL-Datenbank für interne und externe Datenbankverbindungen von Azure erläutert.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit, sqldbrb=1
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 01/25/2021
ms.openlocfilehash: 07303e3eea7e63f7c153db771168b8a741183362
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576497"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-connectivity-architecture"></a>Verbindungsarchitektur von Azure SQL-Datenbank und Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

In diesem Artikel wird die Architektur verschiedener Komponenten erläutert, die den Netzwerkdatenverkehr an einen Server in Azure SQL-Datenbank oder Azure Synapse Analytics weiterleiten. Außerdem werden andere Verbindungsrichtlinien und ihre Auswirkung auf Clients beschrieben, die eine Verbindung innerhalb und außerhalb von Azure herstellen.

> [!IMPORTANT]
> Dieser Artikel gilt *nicht* für **Azure SQL Managed Instance**. Informationen dazu finden Sie unter [Konnektivitätsarchitektur für eine verwaltete Instanz](../managed-instance/connectivity-architecture-overview.md).

## <a name="connectivity-architecture"></a>Verbindungsarchitektur

Das folgende Diagramm bietet einen allgemeinen Überblick über die Verbindungsarchitektur.

![Diagramm mit einer allgemeinen Übersicht über die Konnektivitätsarchitektur](./media/connectivity-architecture/connectivity-overview.png)

In den folgenden Schritten wird das Herstellen einer Verbindung mit einer Azure SQL-Datenbank-Instanz beschrieben:

- Clients stellen eine Verbindung mit dem Gateway her, das über eine öffentliche IP-Adresse verfügt und an Port 1433 lauscht.
- Je nach effektiver Verbindungsrichtlinie leitet das Gateway den Datenverkehr an den richtigen Datenbankcluster um oder fungiert als Proxy.
- Innerhalb des Datenbankclusters wird der Datenverkehr an die entsprechende Datenbank weitergeleitet.

## <a name="connection-policy"></a>Verbindungsrichtlinie

Server in Azure SQL-Datenbank und Azure Synapse unterstützen diese drei Optionen zum Festlegen der Verbindungsrichtlinie des Servers:

- **Umleiten (empfohlen):** Clients stellen Verbindungen direkt mit dem Knoten her, der die Datenbank hostet. Dies führt zu geringerer Latenz und verbessertem Durchsatz. Damit dieser Modus bei Verbindungen verwendet wird, müssen Clients
  - die ausgehende Kommunikation zwischen dem Client und allen IP-Adressen für Azure SQL in der Region an Ports im Bereich zwischen 11000 und 11999 zulassen. die Diensttags für SQL verwenden, um die Verwaltung zu vereinfachen.  
  - die ausgehende Kommunikation zwischen dem Client und den IP-Adressen des Azure SQL-Datenbank-Gateways an Port 1433 zulassen.

- **Proxy:** In diesem Modus werden alle Verbindungen über die Azure SQL-Datenbank-Gateways geleitet. Dies führt zu höherer Latenz und geringerem Durchsatz. Damit dieser Modus bei Verbindungen verwendet wird, müssen Clients die ausgehende Kommunikation zwischen dem Client und den IP-Adressen des Azure SQL-Datenbank-Gateways an Port 1433 zulassen.

- **Standardwert:** Diese Verbindungsrichtlinie ist nach dem Erstellen auf allen Servern aktiv, es sei denn, Sie ändern sie explizit in `Proxy` oder `Redirect`. Die Standardrichtlinie für alle Clientverbindungen, die innerhalb von Azure hergestellt werden (z. B. über einen virtuellen Azure-Computer), ist `Redirect`. Für alle Clientverbindungen, die außerhalb von Azure hergestellt werden (z. B. über Ihre lokale Arbeitsstation), lautet sie `Proxy`.

Es wird empfohlen, die Verbindungsrichtlinie `Redirect` statt der Verbindungsrichtlinie `Proxy` zu verwenden, um die niedrigste Latenz und den höchsten Durchsatz zu erzielen. Allerdings müssen Sie die zusätzlichen Anforderungen erfüllen, um Netzwerkdatenverkehr wie oben beschrieben zu ermöglichen. Wenn es sich bei dem Client um einen virtuellen Azure-Computer handelt, lässt sich dies mithilfe von Netzwerksicherheitsgruppen (NSG) mit [Diensttags](../../virtual-network/network-security-groups-overview.md#service-tags) erreichen. Wenn der Client eine Verbindung über eine lokale Arbeitsstation herstellt, müssen Sie sich möglicherweise an den Netzwerkadministrator wenden, um Netzwerkdatenverkehr über die Unternehmensfirewall zuzulassen.

## <a name="connectivity-from-within-azure"></a>Verbindung aus Azure

Wenn Sie eine Verbindung aus Azure herstellen, verfügen Ihre Verbindungen standardmäßig über die Verbindungsrichtlinie `Redirect`. Die Verbindungsrichtlinie `Redirect` bedeutet, dass nach Aufbau der TCP-Sitzung mit Azure SQL-Datenbank die Clientsitzung an den richtigen Datenbankcluster umgeleitet wird. Dabei wird die virtuelle Ziel-IP von der des Gateways für Azure SQL-Datenbank in die des Clusters geändert. Danach fließen alle nachfolgenden Pakete direkt an den Cluster, wobei das Gateway von Azure SQL-Datenbank umgangen wird. Das folgende Diagramm veranschaulicht diesen Datenverkehrfluss.

![Architekturübersicht](./media/connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Verbindung von außerhalb von Azure

Wenn Sie von außerhalb von Azure eine Verbindung herstellen, verfügen Ihre Verbindungen standardmäßig über die Verbindungsrichtlinie `Proxy`. Die Richtlinie `Proxy` bedeutet, dass die TCP-Sitzung über das Gateway von Azure SQL-Datenbank hergestellt wird und dass alle nachfolgenden Pakete über das Gateway fließen. Das folgende Diagramm veranschaulicht diesen Datenverkehrfluss.

![Diagramm der Einrichtung der TCP-Sitzung über das Gateway von Azure SQL-Datenbank und des gesamten nachfolgenden Paketflusses über das Gateway](./media/connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Öffnen Sie zusätzlich die TCP-Ports 1434 und 14000 bis 14999, um das [Herstellen einer dedizierten Administratorverbindung](/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators#connecting-with-dac) zu ermöglichen.

## <a name="gateway-ip-addresses"></a>Gateway-IP-Adressen

Die folgende Tabelle enthält die IP-Adressen der Gateways nach Region. Wenn Sie eine Verbindung mit Azure SQL-Datenbank oder Azure Synapse herstellen möchten, müssen Sie ein- und ausgehenden Netzwerkdatenverkehr für **alle** Gateways der Region zulassen.

Details zum Migrieren von Datenverkehr zu neuen Gateways in bestimmten Regionen finden Sie im folgenden Artikel: [Azure SQL Database Traffic-Migration auf neuere Gateways](gateway-migration.md).

| Regionsname          | Gateway-IP-Adressen |
| --- | --- |
| Australien, Mitte    | 20.36.105.0, 20.36.104.6, 20.36.104.7 |
| Australien, Mitte 2   | 20.36.113.0, 20.36.112.6 |
| Australien (Osten)       | 13.75.149.87, 40.79.161.1, 13.70.112.9 |
| Australien, Südosten | 191.239.192.109, 13.73.109.251, 13.77.48.10 |
| Brasilien Süd         | 104.41.11.5, 191.233.200.14, 191.234.144.16, 191.234.152.3 |
| Kanada, Mitte       | 40.85.224.249, 52.246.152.0, 20.38.144.1 |
| Kanada, Osten          | 40.86.226.166, 52.242.30.154, 40.69.105.9 , 40.69.105.10 |
| USA (Mitte)           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1, 13.89.169.20 |
| China, Osten           | 139.219.130.35     |
| China, Osten 2         | 40.73.82.1         |
| China, Norden          | 139.219.15.17      |
| China, Norden 2        | 40.73.50.0         |
| Asien, Osten            | 191.234.2.139, 52.175.33.150, 13.75.32.4, 13.75.32.14 |
| East US              | 40.121.158.30, 40.79.153.12, 191.238.6.43, 40.78.225.32 |
| USA (Ost) 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0,  191.239.224.107, 104.208.150.3 |
| Frankreich, Mitte       | 40.79.137.0, 40.79.129.1, 40.79.137.8, 40.79.145.12 |
| Frankreich, Süden         | 40.79.177.0, 40.79.177.10 ,40.79.177.12 |
| Deutschland, Mitte      | 51.4.144.100       |
| Deutschland, Nordosten   | 51.5.144.179       |
| Deutschland, Westen-Mitte | 51.116.240.0, 51.116.248.0, 51.116.152.0 |
| Indien, Mitte        | 104.211.96.159, 104.211.86.30, 104.211.86.31 |
| Indien, Süden          | 104.211.224.146    |
| Indien, Westen           | 104.211.160.80, 104.211.144.4 |
| Japan, Osten           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 191.237.240.43, 40.79.192.5 |
| Japan, Westen           | 104.214.148.156, 40.74.100.192, 191.238.68.11, 40.74.97.10 |
| Korea, Mitte        | 52.231.32.42, 52.231.17.22 ,52.231.17.23 |
| Korea, Süden          | 52.231.200.86      |
| USA Nord Mitte     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| Nordeuropa         | 40.113.93.91, 191.235.193.75, 52.138.224.1, 13.74.104.113 |
| Norwegen, Osten          | 51.120.96.0        |
| Norwegen, Westen          | 51.120.216.0       |
| Südafrika, Norden   | 102.133.152.0, 102.133.120.2       |
| Südafrika, Westen    | 102.133.24.0       |
| USA Süd Mitte     | 13.66.62.124, 23.98.162.75, 104.214.16.32, 20.45.121.1, 20.49.88.1   |
| Südostasien      | 104.43.15.0, 23.100.117.95, 40.78.232.3   |
| Schweiz, Norden    | 51.107.56.0, 51.107.57.0 |
| Schweiz, Westen     | 51.107.152.0, 51.107.153.0 |
| VAE, Mitte          | 20.37.72.64        |
| Vereinigte Arabische Emirate, Norden            | 65.52.248.0        |
| UK, Süden             | 51.140.184.11, 51.105.64.0 |
| UK, Westen              | 51.141.8.11        |
| USA, Westen-Mitte      | 13.78.145.25, 13.78.248.43        |
| Europa, Westen          | 40.68.37.158, 191.237.232.75, 104.40.168.105, 52.236.184.163  |
| USA (Westen)              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| USA, Westen 2            | 13.66.226.202, 40.78.240.8, 40.78.248.10  |
|                      |                    |

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Ändern der Verbindungsrichtlinie von Azure SQL-Datenbank für einen Server finden Sie unter [az sql server conn-policy](/cli/azure/sql/server/conn-policy).
- Weitere Informationen zum Verbindungsverhalten von Azure SQL-Datenbank für Kunden, die ADO.NET 4.5 oder höher verwenden, finden Sie unter [Andere Ports als 1433 für ADO.NET 4.5](adonet-v12-develop-direct-route-ports.md).
- Einen allgemeinen Überblick über die Anwendungsentwicklung finden Sie unter [SQL-Datenbankanwendungsentwicklung – Übersicht](develop-overview.md).

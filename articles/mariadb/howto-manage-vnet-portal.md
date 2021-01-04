---
title: Verwalten von VNET-Endpunkten – Azure-Portal – Azure Database for MariaDB
description: Erstellen und Verwalten von VNET-Dienstendpunkten und -Regeln für Azure Database for MariaDB mithilfe des Microsoft Azure-Portals
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 5eaa7821c61010b322d8f9032c439df28c297f3d
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540874"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Erstellen und Verwalten von VNET-Dienstendpunkten und -Regeln für Azure Database for MariaDB mithilfe des Azure-Portals

Über Dienstendpunkte und Regeln für ein virtuelles Netzwerk (VNET) wird der private Adressraum eines virtuellen Netzwerks auf Ihren Azure Database for MariaDB-Server ausgeweitet. Eine Übersicht über VNET-Dienstendpunkte für Azure Database for MariaDB – einschließlich der Einschränkungen – finden Sie unter [VNET-Dienstendpunkte für Azure Database for MariaDB-Server](concepts-data-access-security-vnet.md). VNET-Dienstendpunkte sind in allen unterstützten Regionen für Azure Database for MariaDB verfügbar.

> [!NOTE]
> VNET-Dienstendpunkte werden nur für Server vom Typ „Universell“ und „Arbeitsspeicheroptimiert“ unterstützt.

## <a name="create-a-vnet-rule-and-enable-service-endpoints"></a>Erstellen einer VNET-Regel und Aktivieren von Dienstendpunkten

1. Klicken Sie auf der Seite des MariaDB-Servers unter der Überschrift „Einstellungen“ auf **Verbindungssicherheit**, um den Bereich „Verbindungssicherheit“ für Azure Database for MariaDB zu öffnen.

2. Legen Sie das Steuerelement „Zugriff auf Azure-Dienste erlauben“ auf **AUS** fest.

> [!Important]
> Wenn Sie es auf ON festlegen, akzeptiert Ihr Azure MariaDB-Datenbankserver Kommunikation von beliebigen Subnetzen. Das Steuerelement auf ON festgelegt zu lassen, führt also möglicherweise aus Sicht der Sicherheit zu einem übermäßigen Zugriff. Mithilfe des Microsoft Azure Virtual Network-Dienstendpunkt-Features und des VNET-Regel-Features von Azure Database for MariaDB können Sie die Angriffsfläche für Sicherheitsangriffe reduzieren.

3. Klicken Sie dann auf **+ Vorhandenes virtuelles Netzwerk wird hinzugefügt**. Wenn kein VNET vorhanden ist, können Sie auf **+ Neues virtuelles Netzwerk erstellen** klicken, um eines zu erstellen. Informationen hierzu finden Sie unter [Schnellstart: Erstellen eines virtuellen Netzwerks über das Azure-Portal](../virtual-network/quick-create-portal.md).

   ![Azure-Portal – Klicken auf „Verbindungssicherheit“](./media/howto-manage-vnet-portal/1-connection-security.png)

4. Geben Sie einen VNET-Regelnamen ein, wählen Sie das Abonnement, das virtuelle Netzwerk sowie den Subnetznamen aus, und klicken Sie dann auf **Aktivieren**. Dadurch werden automatisch VNET-Dienstendpunkte im Subnetz mithilfe des **Microsoft.SQL**-Diensttags aktiviert.

   ![Azure-Portal – Konfigurieren von VNET](./media/howto-manage-vnet-portal/2-configure-vnet.png)

   Das Konto muss über die erforderlichen Berechtigungen zum Erstellen eines virtuellen Netzwerks und eines Dienstendpunkts verfügen.

   Dienstendpunkte können unabhängig für virtuelle Netzwerke konfiguriert werden, der Benutzer benötigt hierzu Schreibzugriff auf das virtuelle Netzwerk.
    
   Um Azure-Dienstressourcen in einem VNET zu sichern, muss der Benutzer für die hinzuzufügenden Subnetze über die Berechtigung für „Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/“ verfügen. Diese Berechtigung ist standardmäßig in die integrierten Dienstadministratorrollen integriert und kann durch die Erstellung von benutzerdefinierten Rollen geändert werden.
    
   Erfahren Sie mehr über [integrierte Rollen](../role-based-access-control/built-in-roles.md) und das Zuweisen bestimmter Berechtigungen zu [benutzerdefinierten Rollen](../role-based-access-control/custom-roles.md).
    
   VNETs und Ressourcen von Azure-Diensten können sich in demselben oder in unterschiedlichen Abonnements befinden. Wenn das VNET und die Ressourcen von Azure-Diensten in unterschiedlichen Abonnements enthalten sind, sollten sich die Ressourcen unter demselben Active Directory-Mandanten (AD) befinden. Stellen Sie sicher, dass für beide Abonnements der Ressourcenanbieter **Microsoft.Sql** registriert ist. Weitere Informationen finden Sie unter [Azure-Ressourcenanbieter und -typen][resource-manager-portal].

   > [!IMPORTANT]
   > Es wird dringend empfohlen, diesen Artikel zu Dienstendpunktkonfigurationen und Überlegungen zu lesen, bevor Sie Dienstendpunkte konfigurieren. **Virtual Network-Dienstendpunkt:** Ein [Virtual Network-Dienstendpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) ist ein Subnetz, dessen Eigenschaftswerte mindestens einen formalen Azure-Diensttypnamen enthalten. VNET-Dienstendpunkte verwenden den Diensttypnamen **Microsoft.Sql**, der auf den Azure-Dienst „SQL-Datenbank“ verweist. Dieses Diensttag gilt auch für die Dienste Azure SQL-Datenbank, Azure Database for MariaDB, Azure Database for PostgreSQL und Azure Database for MySQL. Beim Anwenden des Diensttags **Microsoft.Sql** auf einen VNET-Dienstendpunkt muss beachtet werden, dass auf diese Weise der Dienstendpunkt-Datenverkehr für alle Azure-Datenbankdienste konfiguriert wird. Dies schließt Azure SQL-Datenbank-, Azure Database for PostgreSQL-, Azure Database for MariaDB- und Azure Database for MySQL-Server im Subnetz ein.
   > 

5. Wenn Sie nach der Aktivierung auf **OK** klicken, sehen Sie, dass die VNET-Dienstendpunkte gemeinsam mit einer VNET-Regel aktiviert sind.

   ![Aktivierte VNET-Dienstendpunkte und erstellte VNET-Regel](./media/howto-manage-vnet-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Nächste Schritte
- Lesen Sie mehr über das [Konfigurieren von SSL in Azure Database for MariaDB](howto-configure-ssl.md).
- Auf ähnliche Weise können Sie zum [Aktivieren von VNET-Dienstendpunkten und Erstellen einer VNET-Regel für Azure Database for MariaDB unter Verwendung von Azure CLI](howto-manage-vnet-cli.md) Skripts erstellen.

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
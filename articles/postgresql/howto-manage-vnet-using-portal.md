---
title: Erstellen und Verwalten von VNET-Dienstendpunkten und -Regeln in Azure Database for PostgreSQL – Einzelserver, im Azure-Portal
description: Erstellen und Verwalten von VNET-Dienstendpunkten und -Regeln in Azure Database for PostgreSQL – Einzelserver, im Azure-Portal
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 9da46ae905457f6f6b1786a2161e224d397d0507
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65073172"
---
# <a name="create-and-manage-vnet-service-endpoints-and-vnet-rules-in-azure-database-for-postgresql---single-server-by-using-the-azure-portal"></a>Erstellen und Verwalten von VNET-Dienstendpunkten und VNET-Regeln in Azure Database for PostgreSQL – Einzelserver, im Azure-Portal
VNet-Dienstendpunkte und -regeln (Virtual Network) erweitern den privaten Adressraum eines virtuellen Netzwerks auf Ihren Azure Database for PostgreSQL-Server. Eine Übersicht über die VNet-Dienstendpunkte für Azure Database for PostgreSQL, einschließlich Einschränkungen, finden Sie unter [Azure Database for PostgreSQL Server VNet service endpoints (VNet-Dienstendpunkte für Azure Database for PostgreSQL Server)](concepts-data-access-and-security-vnet.md). VNET-Dienstendpunkte sind in allen unterstützten Regionen für Azure Database for PostgreSQL verfügbar.

> [!NOTE]
> VNET-Dienstendpunkte werden nur für Server vom Typ „Universell“ und „Arbeitsspeicheroptimiert“ unterstützt.
> Wenn beim VNET-Peering der Datenverkehr über eine gemeinsame VNet Gateway-Instanz mit Dienstendpunkten fließt und an den Peer fließen soll, erstellen Sie eine ACL/VNET-Regel, damit Azure Virtual Machines im Gateway-VNET auf den Azure Database for PostgreSQL-Server zugreifen kann.

## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Erstellen einer VNET-Regel und Aktivieren von Dienstendpunkten im Azure-Portal

1. Klicken Sie auf der Seite des PostgreSQL-Servers unter der Überschrift „Einstellungen“ auf **Verbindungssicherheit**, um den Bereich „Verbindungssicherheit“ für Azure Database for PostgreSQL zu öffnen. 

2. Legen Sie das Steuerelement „Zugriff auf Azure-Dienste erlauben“ auf **AUS** fest.

> [!Important]
> Wenn Sie das Steuerelement auf EIN festgelegt lassen, akzeptiert der Azure PostgreSQL-Datenbank-Server Kommunikation von beliebigen Subnetzen. Das Steuerelement auf ON festgelegt zu lassen, führt also möglicherweise aus Sicht der Sicherheit zu einem übermäßigen Zugriff. Mithilfe des Microsoft Azure Virtual Network-Dienstendpunkts und Regeln für ein virtuelles Netzwerk von Azure Database for PostgreSQL können Sie die sicherheitsbezogene Angriffsfläche verkleinern.

3. Klicken Sie dann auf **+ Vorhandenes virtuelles Netzwerk wird hinzugefügt**. Wenn kein VNET vorhanden ist, können Sie auf **+ Neues virtuelles Netzwerk erstellen** klicken, um eines zu erstellen. Weitere Informationen finden Sie unter [Schnellstart: Erstellen eines virtuellen Netzwerks im Azure-Portal](../virtual-network/quick-create-portal.md)

   ![Azure-Portal – Klicken auf „Verbindungssicherheit“](./media/howto-manage-vnet-using-portal/1-connection-security.png)

4. Geben Sie einen VNET-Regelnamen ein, wählen Sie das Abonnement, das virtuelle Netzwerk sowie den Subnetznamen aus, und klicken Sie dann auf **Aktivieren**. Dadurch werden automatisch VNET-Dienstendpunkte im Subnetz mithilfe des **Microsoft.SQL**-Diensttags aktiviert.

   ![Azure-Portal – Konfigurieren von VNET](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

    Das Konto muss über die erforderlichen Berechtigungen zum Erstellen eines virtuellen Netzwerks und eines Dienstendpunkts verfügen.

    Dienstendpunkte können unabhängig für virtuelle Netzwerke konfiguriert werden, der Benutzer benötigt hierzu Schreibzugriff auf das virtuelle Netzwerk.
    
    Um Azure-Dienstressourcen in einem VNET zu sichern, muss der Benutzer für die hinzuzufügenden Subnetze über die Berechtigung für „Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/“ verfügen. Diese Berechtigung ist standardmäßig in die integrierten Dienstadministratorrollen integriert und kann durch die Erstellung von benutzerdefinierten Rollen geändert werden.
    
    Erfahren Sie mehr über [integrierte Rollen](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) und das Zuweisen bestimmter Berechtigungen zu [benutzerdefinierten Rollen](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).
    
    VNETs und Ressourcen von Azure-Diensten können sich in demselben oder in unterschiedlichen Abonnements befinden. Wenn das VNET und die Ressourcen von Azure-Diensten in unterschiedlichen Abonnements enthalten sind, sollten sich die Ressourcen unter demselben Active Directory-Mandanten (AD) befinden.

   > [!IMPORTANT]
   > Es wird dringend empfohlen, diesen Artikel zu Dienstendpunktkonfigurationen und Überlegungen zu lesen, bevor Sie Dienstendpunkte konfigurieren. **VNET-Dienstendpunkt:** Ein [VNET-Dienstendpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) ist ein Subnetz, dessen Eigenschaftswerte mindestens einen formalen Azure-Diensttypnamen enthalten. VNET-Dienstendpunkte verwenden den Diensttypnamen **Microsoft.Sql**, der auf den Azure-Dienst „SQL-Datenbank“ verweist. Dieses Diensttag gilt auch für die Dienste Azure SQL-Datenbank, Azure Database for PostgreSQL und Azure Database for MySQL. Beim Anwenden des Diensttags **Microsoft.Sql** auf einen VNET-Dienstendpunkt muss beachtet werden, dass auf diese Weise der Dienstendpunkt-Datenverkehr für alle Azure-Datenbankdienste konfiguriert wird. Dies schließt Azure SQL-Datenbank-, Azure Database for PostgreSQL- und Azure Database for MySQL-Server im Subnetz ein. 
   > 

5. Wenn Sie nach der Aktivierung auf **OK** klicken, sehen Sie, dass die VNET-Dienstendpunkte gemeinsam mit einer VNET-Regel aktiviert sind.

   ![Aktivierte VNET-Dienstendpunkte und erstellte VNET-Regel](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Nächste Schritte
- Auf ähnliche Weise können Sie zum [Aktivieren von VNET-Dienstendpunkten und Erstellen einer VNET-Regel für Azure Database for PostgreSQL mithilfe von Azure CLI](howto-manage-vnet-using-cli.md) Skripts erstellen.
- Wenn Sie Unterstützung beim Herstellen einer Verbindung mit einem Server für Azure-Datenbank für MySQL benötigen, lesen Sie die Informationen unter [Connection libraries for Azure Database for PostgreSQL](./concepts-connection-libraries.md) (Verbindungsbibliotheken für Azure-Datenbank für PostgreSQL).

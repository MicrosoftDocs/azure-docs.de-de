---
title: Verwalten von VNET-Endpunkten für Azure Database for MySQL mit einer Azure-Befehlszeilenschnittstelle
description: In diesem Artikel wird beschrieben, wie Sie mithilfe der Azure CLI-Befehlszeile VNET-Dienstendpunkte und -Regeln für Azure Database for MySQL erstellen und verwalten.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 237b9bc4d7ac6366a67accb31fdf3c80c778b5d6
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636757"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-using-azure-cli"></a>Erstellen und Verwalten von VNET-Dienstendpunkten und -Regeln für Azure Database for MySQL mithilfe der Azure CLI
Über Dienstendpunkte und Regeln für ein virtuelles Netzwerk (VNET) wird der private Adressraum eines virtuellen Netzwerks auf Ihren Azure Database for MySQL-Server ausgeweitet. Mit den praktischen Befehlen der Azure-Befehlszeilenschnittstelle (Azure CLI) können Sie VNET-Dienstpunkte und -Regeln erstellen, aktualisieren, löschen, auflisten und anzeigen, um Ihren Server zu verwalten. Einen Überblick über VNET-Dienstendpunkte für Azure Database for MySQL – einschließlich der Einschränkungen – finden Sie unter [VNET-Dienstendpunkte für Azure Database for MySQL Server](concepts-data-access-and-security-vnet.md). VNET-Dienstendpunkte sind in allen unterstützten Regionen für Azure Database for MySQL verfügbar.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Sie benötigen einen [Azure Database for MySQL-Server und eine Datenbank](quickstart-create-mysql-server-database-using-azure-cli.md).
 
- Für diesen Artikel ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

> [!NOTE]
> VNET-Dienstendpunkte werden nur für Server vom Typ „Universell“ und „Arbeitsspeicheroptimiert“ unterstützt.
> Wenn beim VNet-Peering der Datenverkehr über eine gemeinsame VNet Gateway-Instanz mit Dienstendpunkten fließt und an den Peer fließen soll, erstellen Sie eine ACL/VNet-Regel, damit Azure Virtual Machines im Gateway-VNet auf den Azure Database for MySQL-Server zugreifen kann.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-mysql"></a>Konfigurieren von VNET-Dienstendpunkten für Azure Database for MySQL
Die [az network vnet](/cli/azure/network/vnet)-Befehle werden zum Konfigurieren von virtuellen Netzwerken verwendet.

Wenn Sie über mehrere Abonnements verfügen, wählen Sie das entsprechende Abonnement aus, in dem die Ressource fakturiert sein sollte. Wählen Sie mithilfe des Befehls [az account set](/cli/azure/account#az-account-set) die Abonnement-ID unter Ihrem Konto aus. Ersetzen Sie den Platzhalter für die Abonnement-ID durch die Eigenschaft **id** aus der Ausgabe von **az login** für Ihr Abonnement.

- Das Konto muss über die erforderlichen Berechtigungen zum Erstellen eines virtuellen Netzwerks und eines Dienstendpunkts verfügen.

Dienstendpunkte können unabhängig für virtuelle Netzwerke konfiguriert werden, der Benutzer benötigt hierzu Schreibzugriff auf das virtuelle Netzwerk.

Um Azure-Dienstressourcen in einem VNET zu sichern, muss der Benutzer für die hinzuzufügenden Subnetze über die Berechtigung für „Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/“ verfügen. Diese Berechtigung ist standardmäßig in die integrierten Dienstadministratorrollen integriert und kann durch die Erstellung von benutzerdefinierten Rollen geändert werden.

Erfahren Sie mehr über [integrierte Rollen](../role-based-access-control/built-in-roles.md) und das Zuweisen bestimmter Berechtigungen zu [benutzerdefinierten Rollen](../role-based-access-control/custom-roles.md).

VNETs und Ressourcen von Azure-Diensten können sich in demselben oder in unterschiedlichen Abonnements befinden. Wenn das VNET und die Ressourcen von Azure-Diensten in unterschiedlichen Abonnements enthalten sind, sollten sich die Ressourcen unter demselben Active Directory-Mandanten (AD) befinden. Stellen Sie sicher, dass für beide Abonnements der Ressourcenanbieter **Microsoft.Sql** registriert ist. Weitere Informationen finden Sie unter [Azure-Ressourcenanbieter und -typen][resource-manager-portal].

> [!IMPORTANT]
> Es wird dringend empfohlen, diesen Artikel zu Dienstendpunktkonfigurationen und -überlegungen zu lesen, bevor Sie das unten aufgeführte Beispielskript ausführen oder Dienstendpunkte konfigurieren. **Virtual Network-Dienstendpunkt:** Ein [VNET-Dienstendpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) ist ein Subnetz, dessen Eigenschaftswerte mindestens einen formalen Azure-Diensttypnamen enthalten. VNET-Dienstendpunkte verwenden den Diensttypnamen **Microsoft.Sql**, der auf den Azure-Dienst „SQL-Datenbank“ verweist. Dieses Diensttag gilt auch für die Dienste Azure SQL-Datenbank, Azure Database for PostgreSQL und Azure Database for MySQL. Beim Anwenden des Diensttags **Microsoft.Sql** auf einen VNET-Dienstendpunkt muss beachtet werden, dass auf diese Weise der Dienstendpunkt-Datenverkehr für alle Azure-Datenbankdienste konfiguriert wird. Dies schließt Azure SQL-Datenbank-, Azure Database for PostgreSQL- und Azure Database for MySQL-Server im Subnetz ein. 
> 

### <a name="sample-script-to-create-an-azure-database-for-mysql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Beispielskript zum Erstellen einer Azure Database for MySQL-Datenbank, eines virtuellen Netzwerks und eines VNET-Dienstendpunkts sowie zum Festlegen des Servers auf das Subnetz mit einer VNET-Regel
Ändern Sie in diesem Beispielskript die hervorgehobenen Zeilen, um den Benutzernamen und das Kennwort des Administrators anzupassen. Ersetzen Sie die SubscriptionID im Befehl `az account set --subscription` durch Ihre eigene Abonnement-ID.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MySQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung
Nach Ausführung des Skriptbeispiels können mit dem folgenden Befehl die Ressourcengruppe und alle damit verbundenen Ressourcen entfernt werden.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md

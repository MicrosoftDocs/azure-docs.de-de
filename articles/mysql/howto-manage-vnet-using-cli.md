---
title: Erstellen und Verwalten von VNET-Dienstendpunkten und -Regeln für Azure Database for MySQL mithilfe der Azure CLI | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie mithilfe der Azure CLI-Befehlszeile VNET-Dienstendpunkte und -Regeln für Azure Database for MySQL erstellen und verwalten.
author: bolzmj
ms.author: mbolz
manager: jhubbard
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 10/23/2018
ms.openlocfilehash: 199a5ead9856c5f9cba404ce89611efe2d78de8d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66160467"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-using-azure-cli"></a>Erstellen und Verwalten von VNET-Dienstendpunkten und -Regeln für Azure Database for MySQL mithilfe der Azure CLI
Über Dienstendpunkte und Regeln für ein virtuelles Netzwerk (VNET) wird der private Adressraum eines virtuellen Netzwerk auf Ihren Azure Database for MySQL-Server ausgeweitet. Mit den praktischen Befehlen der Azure-Befehlszeilenschnittstelle (Azure CLI) können Sie VNET-Dienstpunkte und -Regeln erstellen, aktualisieren, löschen, auflisten und anzeigen, um Ihren Server zu verwalten. Einen Überblick über VNET-Dienstendpunkte für Azure Database for MySQL – einschließlich der Einschränkungen – finden Sie unter [VNET-Dienstendpunkte für Azure Database for MySQL Server](concepts-data-access-and-security-vnet.md). VNET-Dienstendpunkte sind in allen unterstützten Regionen für Azure Database for MySQL verfügbar.

## <a name="prerequisites"></a>Voraussetzungen
Zum Ausführen der Schritte in dieser Anleitung benötigen Sie Folgendes:
- Installieren Sie die [Azure CLI](/cli/azure/install-azure-cli) oder verwenden Sie Azure Cloud Shell im Browser.
- Einen [Azure Database for MySQL-Server und eine Datenbank](quickstart-create-mysql-server-database-using-azure-cli.md)

> [!NOTE]
> VNET-Dienstendpunkte werden nur für Server vom Typ „Universell“ und „Arbeitsspeicheroptimiert“ unterstützt.
> Wenn beim VNet-Peering der Datenverkehr über eine gemeinsame VNet Gateway-Instanz mit Dienstendpunkten fließt und an den Peer fließen soll, erstellen Sie eine ACL/VNet-Regel, damit Azure Virtual Machines im Gateway-VNet auf den Azure Database for MySQL-Server zugreifen kann.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-mysql"></a>Konfigurieren von VNET-Dienstendpunkten für Azure Database for MySQL
Die [az network vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest)-Befehle werden zum Konfigurieren von virtuellen Netzwerken verwendet.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für diesen Artikel die Azure CLI-Version 2.0 oder höher ausführen. Führen Sie den Befehl `az --version` aus, um die installierte Version anzuzeigen. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli). 

Wenn Sie die CLI lokal ausführen, müssen Sie sich mit dem Befehl [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) an Ihrem Konto anmelden. Beachten Sie die Eigenschaft **id** aus der Befehlsausgabe für den entsprechenden Abonnementnamen.
```azurecli-interactive
az login
```

Wenn Sie über mehrere Abonnements verfügen, wählen Sie das entsprechende Abonnement aus, in dem die Ressource fakturiert sein sollte. Wählen Sie mithilfe des Befehls [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set) die Abonnement-ID unter Ihrem Konto aus. Ersetzen Sie den Platzhalter für die Abonnement-ID durch die Eigenschaft **id** aus der Ausgabe von **az login** für Ihr Abonnement.

- Das Konto muss über die erforderlichen Berechtigungen zum Erstellen eines virtuellen Netzwerks und eines Dienstendpunkts verfügen.

Dienstendpunkte können unabhängig für virtuelle Netzwerke konfiguriert werden, der Benutzer benötigt hierzu Schreibzugriff auf das virtuelle Netzwerk.

Um Azure-Dienstressourcen in einem VNET zu sichern, muss der Benutzer für die hinzuzufügenden Subnetze über die Berechtigung für „Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/“ verfügen. Diese Berechtigung ist standardmäßig in die integrierten Dienstadministratorrollen integriert und kann durch die Erstellung von benutzerdefinierten Rollen geändert werden.

Erfahren Sie mehr über [integrierte Rollen](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) und das Zuweisen bestimmter Berechtigungen zu [benutzerdefinierten Rollen](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

VNETs und Ressourcen von Azure-Diensten können sich in demselben oder in unterschiedlichen Abonnements befinden. Wenn das VNET und die Ressourcen von Azure-Diensten in unterschiedlichen Abonnements enthalten sind, sollten sich die Ressourcen unter demselben Active Directory-Mandanten (AD) befinden.

> [!IMPORTANT]
> Es wird dringend empfohlen, diesen Artikel zu Dienstendpunktkonfigurationen und -überlegungen zu lesen, bevor Sie das unten aufgeführte Beispielskript ausführen oder Dienstendpunkte konfigurieren. **VNET-Dienstendpunkt:** Ein [VNET-Dienstendpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) ist ein Subnetz, dessen Eigenschaftswerte mindestens einen formalen Azure-Diensttypnamen enthalten. VNET-Dienstendpunkte verwenden den Diensttypnamen **Microsoft.Sql**, der auf den Azure-Dienst „SQL-Datenbank“ verweist. Dieses Diensttag gilt auch für die Dienste Azure SQL-Datenbank, Azure Database for PostgreSQL und Azure Database for MySQL. Beim Anwendung des Diensttags **Microsoft.Sql** auf einen VNET-Dienstendpunkt muss beachtet werden, dass auf diese Weise der Dienstendpunktdatenverkehr für alle Azure-Datenbankdienste konfiguriert wird. Dies schließt Azure SQL-Datenbank-, Azure Database for PostgreSQL- und Azure Database for MySQL-Server im Subnetz ein. 
> 

### <a name="sample-script-to-create-an-azure-database-for-mysql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Beispielskript zum Erstellen einer Azure Database for MySQL-Datenbank, eines virtuellen Netzwerks und eines VNET-Dienstendpunkts sowie zum Festlegen des Servers auf das Subnetz mit einer VNET-Regel
Ändern Sie in diesem Beispielskript die hervorgehobenen Zeilen, um den Benutzernamen und das Kennwort des Administrators anzupassen. Ersetzen Sie die SubscriptionID im Befehl `az account set --subscription` durch Ihre eigene Abonnement-ID.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MySQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung
Nach Ausführung des Skriptbeispiels können mit dem folgenden Befehl die Ressourcengruppe und alle damit verbundenen Ressourcen entfernt werden.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]

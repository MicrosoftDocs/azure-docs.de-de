---
title: Verwalten von Firewallregeln für Azure Database for MySQL mithilfe der Azure-Befehlszeilenschnittstelle
description: In diesem Artikel wird beschrieben, wie Sie mithilfe der Azure CLI-Befehlszeile Firewallregeln für Azure Database for MySQL erstellen und verwalten.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: eaddd8b2979b30251301ad041ea4b872c23d680b
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541333"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-cli"></a>Erstellen und Verwalten von Firewallregeln für Azure Database for MySQL mithilfe der Azure CLI
Firewallregeln auf Serverebene können verwendet werden, um den Zugriff auf eine Azure Database for MySQL-Serverinstanz über eine bestimmte IP-Adresse oder über einen IP-Adressbereich zu verwalten. Dank praktischer Azure CLI-Befehle können Sie zum Verwalten Ihres Servers Firewallregeln erstellen, aktualisieren, löschen, auflisten und anzeigen. Eine Übersicht über Firewalls für Azure-Datenbank for MySQL finden Sie unter [Firewallregeln für Azure-Datenbank für MySQL-Server](./concepts-firewall-rules.md).

Virtual Network-Regeln (VNET) können auch verwendet werden, um den Zugriff auf Ihren Server zu sichern. Weitere Informationen finden Sie unter [Erstellen und Verwalten von VNET-Dienstendpunkten und -Regeln mithilfe der Azure CLI](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Voraussetzungen
* [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli)
* Einen [Azure Database for MySQL-Server und eine Datenbank](quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="firewall-rule-commands"></a>Befehle für Firewallregeln:
Der Befehl **az mysql server firewall-rule** wird in der Azure CLI zum Erstellen, Löschen, Auflisten, Anzeigen und Aktualisieren von Firewallregeln verwendet.

Befehle:
- **create**: Dient zum Erstellen einer Firewallregel für den Azure MySQL-Server.
- **delete**: Dient zum Löschen einer Firewallregel für den Azure MySQL-Server.
- **list**: Dient zum Auflisten der Firewallregeln für den Azure MySQL-Server.
- **show**: Dient zum Anzeigen der Details einer Firewallregel für den Azure MySQL-Server.
- **update**: Dient zum Aktualisieren einer Firewallregel für den Azure MySQL-Server.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Anmelden bei Azure und Auflisten Ihrer Azure Database for MySQL-Serverinstanzen
Stellen Sie mit dem Befehl **az login** eine sichere Verbindung zwischen der Azure CLI und Ihrem Azure-Konto her.

1. Führen Sie an der Befehlszeile den folgenden Befehl aus:
    ```azurecli
    az login
    ```
   Dieser Befehl gibt einen Code aus, der im nächsten Schritt verwendet wird.

2. Verwenden Sie zum Öffnen der Seite [https://aka.ms/devicelogin](https://aka.ms/devicelogin) einen Webbrowser, und geben Sie dann den Code ein.

3. Melden Sie sich an der Eingabeaufforderung mit Ihren Azure-Anmeldeinformationen an.

4. Nachdem Ihre Anmeldung autorisiert wurde, wird eine Liste der Abonnements in der Konsole ausgegeben. Kopieren Sie die ID des gewünschten Abonnements, um das aktuelle Abonnement festzulegen, das verwendet werden soll. Verwenden Sie den Befehl [az account set](/cli/azure/account#az-account-set).
    ```azurecli-interactive
    az account set --subscription <your subscription id>
    ```

5. Listen Sie die Server für Azure-Datenbanken für MySQL für Ihr Abonnement und die Ressourcengruppe auf, wenn Sie sich in Bezug auf die Namen unsicher sind. Verwenden Sie den Befehl [az mysql server list](/cli/azure/mysql/server#az-mysql-server-list).

    ```azurecli-interactive
    az mysql server list --resource-group myresourcegroup
    ```

   Notieren Sie das Attribut „Name“ in der Auflistung. Mit diesem Attribut wird angegeben, welche Serverinstanz von MySQL verwendet werden soll. Bestätigen Sie ggf. die Details für diesen Server, um mithilfe des name-Attributs die Richtigkeit des Namens zu überprüfen. Verwenden Sie den Befehl [az mysql server show](/cli/azure/mysql/server#az-mysql-server-show).

    ```azurecli-interactive
    az mysql server show --resource-group myresourcegroup --name mydemoserver
    ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Auflisten der Firewallregeln für die Azure Database for MySQL-Instanz 
Listen Sie mithilfe des Servernamens und des Ressourcengruppennamens die vorhandenen Firewallregeln auf dem Server auf. Verwenden Sie den Befehl [az mysql server firewall list](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-list).  Beachten Sie, dass das Attribut mit dem Servernamen im Switch **--server** und nicht im Switch **--name** angegeben ist. 
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
Die Ausgabe listet die Regeln (sofern vorhanden) standardmäßig im JSON-Format auf. Sie können mit dem Switch **--output table** Ergebnisse in einem besser lesbaren Tabellenformat ausgeben.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mysql-server"></a>Erstellen einer Firewallregel für die Azure Database for MySQL-Serverinstanz
Erstellen Sie mithilfe des Azure MySQL-Servernamens und des Ressourcengruppennamens eine neue Firewallregel auf dem Server. Verwenden Sie den Befehl [az mysql server firewall create](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create). Geben Sie einen Namen für die Regel sowie die Start-IP- und End-IP-Adresse ein, um einen Bereich von IP-Adressen anzugeben, denen der Zugriff gewährt wird.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Damit nur einer einzelnen IP-Adresse Zugriff gewährt wird, geben Sie wie in diesem Beispiel dieselbe Adresse als Start-IP- und End-IP-Adresse an.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Um Anwendungen die Verbindung von Azure-IP-Adressen mit dem Azure Database for MySQL-Server zu erlauben, geben Sie wie in diesem Beispiel die IP-Adresse 0.0.0.0 als Start-IP und End-IP an.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mysql --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Diese Option konfiguriert die Firewall derart, dass alle von Azure ausgehenden Verbindungen zugelassen werden, einschließlich Verbindungen von den Abonnements anderer Kunden. Wenn Sie diese Option auswählen, stellen Sie sicher, dass die Anmelde- und die Benutzerberechtigungen den Zugriff nur auf autorisierte Benutzer beschränken.
> 

Bei erfolgreicher Ausführung listet die Befehlsausgabe für die Erstellung die Details der von Ihnen erstellten Firewallregel standardmäßig im JSON-Format auf. Falls ein Fehler auftritt, wird in der Ausgabe stattdessen eine Fehlermeldung angezeigt.

## <a name="update-a-firewall-rule-on-azure-database-for-mysql-server"></a>Aktualisieren einer Firewallregel für die Azure Database for MySQL-Serverinstanz 
Aktualisieren Sie mithilfe des Azure MySQL-Servernamens und des Ressourcengruppennamens eine vorhandene Firewallregel auf dem Server. Verwenden Sie den Befehl [az mysql server firewall update](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-update). Geben Sie den Namen der vorhandenen Firewallregel als Eingabe sowie die zu aktualisierenden Attribute für die Start-IP- und End-IP-Adresse ein.
```azurecli-interactive
az mysql server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Bei erfolgreicher Ausführung listet die Befehlsausgabe die Details der von Ihnen aktualisierten Firewallregel standardmäßig im JSON-Format auf. Falls ein Fehler auftritt, wird in der Ausgabe stattdessen eine Fehlermeldung angezeigt.

> [!NOTE]
> Falls die Firewallregel nicht vorhanden ist, wird sie vom Befehl „Update“ erstellt.

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Anzeigen von Firewallregeldetails für die Azure Database for MySQL-Serverinstanz
Zeigen Sie mithilfe des Azure MySQL-Servernamens und des Ressourcengruppennamens die Details der vorhandenen Firewallregel auf dem Server an. Verwenden Sie den Befehl [az mysql server firewall show](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-show). Geben Sie den Namen der vorhandenen Firewallregel als Eingabe an.
```azurecli-interactive
az mysql server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Bei erfolgreicher Ausführung listet die Befehlsausgabe die Details der von Ihnen angegebenen Firewallregel standardmäßig im JSON-Format auf. Falls ein Fehler auftritt, wird in der Ausgabe stattdessen eine Fehlermeldung angezeigt.

## <a name="delete-a-firewall-rule-on-azure-database-for-mysql-server"></a>Löschen einer Firewallregel für die Azure Database for MySQL-Serverinstanz
Entfernen Sie mithilfe des Azure MySQL-Servernamens und des Ressourcengruppennamens eine vorhandene Firewallregel vom Server. Verwenden Sie den Befehl [az mysql server firewall delete](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-delete). Geben Sie den Namen der vorhandenen Firewallregel an.
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Bei erfolgreicher Ausführung wird keine Ausgabe angezeigt. Bei einem Fehler wird eine Fehlermeldung angezeigt.

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu den [Firewallregeln einer Azure Database for MySQL-Serverinstanz](./concepts-firewall-rules.md).
- [Erstellen und Verwalten von Firewallregeln für Azure Database for MySQL mithilfe des Azure-Portals](./howto-manage-firewall-using-portal.md)
- Sichern Sie den Zugriff auf Ihren Server noch mehr, indem Sie [VNET-Dienstendpunkte und -Regeln mithilfe der Azure CLI erstellen und verwalten](howto-manage-vnet-using-cli.md).
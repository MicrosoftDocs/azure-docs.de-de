---
title: 'Schnellstart: Erstellen eines Servers – Azure CLI – Azure Database for MySQL'
description: Dieser Schnellstart beschreibt die Verwendung der Azure CLI zum Erstellen einer Azure-Datenbank für MySQL-Server in einer Azure-Ressourcengruppe.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 07/15/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 4f774351fc0eaaf32069687a1943c72b74b6308a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96494323"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-azure-cli"></a>Schnellstart: Erstellen einer Azure-Datenbank für MySQL-Server mithilfe der Azure CLI

> [!TIP]
> Erwägen Sie die Verwendung des vereinfachten Befehls [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) der Azure-Befehlszeilenschnittstelle (derzeit in der Vorschauphase). Probieren Sie den [Schnellstart](./quickstart-create-server-up-azure-cli.md) aus.

In dieser Schnellstartanleitung erfahren Sie, wie Sie mit den Befehlen der [Azure CLI](/cli/azure/get-started-with-azure-cli) in [Azure Cloud Shell](https://shell.azure.com) in fünf Minuten einen Azure Database for MySQL-Server erstellen. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Für diesen Schnellstart ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

 - Wählen Sie mithilfe des Befehls [az account set](/cli/azure/account) das Abonnement unter Ihrem Konto aus. Notieren Sie sich aus der Ausgabe von **az login** den Wert für **id**. Sie verwenden ihn im Befehl als Wert für das Argument **subscription**. Wenn Sie über mehrere Abonnements verfügen, wählen Sie das entsprechende Abonnement aus, in dem die Ressource fakturiert sein sollte. Verwenden Sie [az account list](/cli/azure/account#az-account-list), um alle Abonnements abzurufen.

   ```azurecli
   az account set --subscription <subscription id>
   ```

## <a name="create-an-azure-database-for-mysql-server"></a>Erstellen eines Servers für Azure-Datenbank für MySQL
Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group) eine [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md) und anschließend in dieser Ressourcengruppe Ihren MySQL-Server. Geben Sie daher einen eindeutigen Namen an. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen `myresourcegroup` am Standort `westus` erstellt.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Erstellen Sie mit dem Befehl [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) einen Azure-Datenbank für MySQL-Server. Ein Server kann mehrere Datenbanken enthalten.

```azurecli
az mysql server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```

Hier sind die Details zu den obigen Argumenten aufgeführt: 

**Einstellung** | **Beispielwert** | **Beschreibung**
---|---|---
name | mydemoserver | Geben Sie einen eindeutigen Namen für Ihren Azure Database for MySQL-Server ein. Der Servername darf nur Kleinbuchstaben, Zahlen und den Bindestrich (-) enthalten. Es muss zwischen drei und 63 Zeichen lang sein.
resource-group | myresourcegroup | Geben Sie den Namen der Azure-Ressourcengruppe an.
location | westus | Der Azure-Standort für den Server.
admin-user | myadmin | Der Benutzername für die Administratoranmeldung. Dieser darf nicht **azure_superuser**, **admin**, **administrator**, **root**, **guest** oder **public** lauten.
admin-password | *sicheres Kennwort* | Das Kennwort des Administratorbenutzers. Es muss zwischen acht und 128 Zeichen lang sein. Das Kennwort muss Zeichen aus drei der folgenden Kategorien enthalten: Englische Großbuchstaben, englische Kleinbuchstaben, Zahlen und nicht alphanumerische Zeichen.
sku-name|GP_Gen5_2|Geben Sie den Namen des Tarifs und der Computekonfiguration ein. Folgt der Konvention „{Tarif} _{Computegeneration}_ {virtuelle Kerne}“ in Kurzform. Weitere Informationen finden Sie unter [Azure Database for MySQL – Tarife](./concepts-pricing-tiers.md).

>[!IMPORTANT] 
>- Die Standardversion von MySQL auf dem Server lautet 5.7. Derzeit sind außerdem die Versionen 5.6 und 8.0 verfügbar.
>- Alle Argumente für den Befehl **az mysql server create** finden Sie in [diesem Referenzdokument](/cli/azure/mysql/server#az-mysql-server-create).
>- SSL ist standardmäßig auf dem Server aktiviert. Weitere Informationen zu SSL finden Sie unter [Konfigurieren von SSL-Verbindungen in der Anwendung für eine sichere Verbindung mit der Azure-Datenbank für MySQL](howto-configure-ssl.md).

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurieren einer Firewallregel auf Serverebene 
Der neu erstellte Server ist standardmäßig durch Firewallregeln geschützt und nicht öffentlich zugänglich. Sie können die Firewallregel auf dem Server mithilfe des Befehls [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule) konfigurieren. Dadurch können Sie eine lokale Verbindung mit dem Server herstellen.

Das folgende Beispiel erstellt eine Firewallregel namens `AllowMyIP`, die Verbindungen über eine bestimmte IP-Adresse (192.168.0.1) zulässt. Ersetzen Sie die IP-Adresse, über die Sie eine Verbindung herstellen. Bei Bedarf können Sie einen Bereich von IP-Adressen verwenden. Wenn Sie nicht wissen, wie Sie Ihre IP-Adresse ermitteln, navigieren Sie zu [https://whatismyipaddress.com/](https://whatismyipaddress.com/), um Ihre IP-Adresse abzurufen.

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Die Kommunikation für Verbindungen mit Azure-Datenbank für MySQL erfolgt über Port 3306. Wenn Sie versuchen, eine Verbindung über ein Unternehmensnetzwerk herzustellen, wird ausgehender Datenverkehr über Port 3306 unter Umständen nicht zugelassen. In diesem Fall können Sie nur dann eine Verbindung mit Ihrem Server herstellen, wenn Ihre IT-Abteilung Port 3306 öffnet.

## <a name="get-the-connection-information"></a>Abrufen der Verbindungsinformationen

Zum Herstellen einer Verbindung zum Server müssen Sie Hostinformationen und Anmeldeinformationen für den Zugriff angeben.

```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Das Ergebnis liegt im JSON-Format vor. Notieren Sie sich die Werte für **fullyQualifiedDomainName** und **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>Herstellen einer Verbindung mit dem Azure Database for MySQL-Server mithilfe des mysql-Befehlszeilenclients
Für die Verbindungsherstellung mit Ihrem Server können Sie das beliebte Clientbefehlszeilentool **[mysql.exe](https://dev.mysql.com/downloads/)** mit [Azure Cloud Shell](../cloud-shell/overview.md) verwenden. Alternativ können Sie die mysql-Befehlszeile in Ihrer lokalen Umgebung nutzen.
```bash
 mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie diese Ressourcen nicht für einen anderen Schnellstart bzw. ein anderes Tutorial benötigen, können Sie sie mit dem folgenden Befehl löschen: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Wenn Sie nur den neu erstellten Server löschen möchten, können Sie den Befehl [az mysql server delete](/cli/azure/mysql/server#az-mysql-server-delete) ausführen.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
>[Erstellen einer PHP-App unter Windows mit MySQL](../app-service/tutorial-php-mysql-app.md)
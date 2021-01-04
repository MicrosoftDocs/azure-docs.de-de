---
title: 'Schnellstart: Erstellen eines Servers – Azure CLI – Azure Database for PostgreSQL-Einzelserver'
description: In dieser Schnellstartanleitung erstellen Sie einen Azure Database for PostgreSQL-Server über die Azure CLI.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 06/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d174e410aaef876dfe97af62750322641de95fd3
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659453"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-cli"></a>Schnellstart: Erstellen eines Azure Database for PostgreSQL-Servers mithilfe der Azure CLI

In dieser Schnellstartanleitung erfahren Sie, wie Sie mit den Befehlen der [Azure CLI](/cli/azure/get-started-with-azure-cli) in [Azure Cloud Shell](https://shell.azure.com) in fünf Minuten einen einzelnen Azure Database for PostgreSQL-Server erstellen. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

    > [!TIP]
    >  Erwägen Sie die Verwendung des vereinfachten Azure CLI-Befehls [az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up), der sich derzeit in der Vorschauphase befindet. Probieren Sie den [Schnellstart](./quickstart-create-server-up-azure-cli.md) aus.

- Wählen Sie mithilfe des Befehls [az account set](/cli/azure/account) die Abonnement-ID unter Ihrem Konto aus.

    - Notieren Sie sich aus der Ausgabe von **az login** den Wert für **id**. Sie verwenden ihn im Befehl als Wert für das Argument **subscription**. 

        ```azurecli
        az account set --subscription <subscription id>
        ```

    - Wenn Sie über mehrere Abonnements verfügen, wählen Sie das entsprechende Abonnement aus, in dem die Ressource fakturiert sein sollte. Verwenden Sie [az account list](/cli/azure/account#az-account-list), um alle Abonnements abzurufen.

## <a name="create-an-azure-database-for-postgresql-server"></a>Erstellen einer Azure-Datenbank für PostgreSQL-Server

Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group#az-group-create) eine [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md) und anschließend in dieser Ressourcengruppe Ihren PostgreSQL-Server. Geben Sie daher einen eindeutigen Namen an. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen `myresourcegroup` am Standort `westus` erstellt.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Erstellen Sie mit dem Befehl [az postgres server create](/cli/azure/postgres/server) einen [Azure Database for PostgreSQL-Server](overview.md). Ein Server kann mehrere Datenbanken enthalten.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```
Hier sind die Details zu den vorangehenden Argumenten aufgeführt: 

**Einstellung** | **Beispielwert** | **Beschreibung**
---|---|---
name | mydemoserver | Eindeutiger Name, der Ihren Azure Database for PostgreSQL-Server identifiziert. Der Servername darf nur Kleinbuchstaben, Zahlen und den Bindestrich (-) enthalten. Er muss zwischen 3 und 63 Zeichen aufweisen. Weitere Informationen finden Sie unter [Benennungsregeln und -einschränkungen für Azure-Ressourcen](../azure-resource-manager/management/resource-name-rules.md#microsoftdbforpostgresql).
resource-group | myresourcegroup | Der Name der Azure-Ressourcengruppe.
location | westus | Azure-Standort für den Server
admin-user | myadmin | Benutzername für die Administratoranmeldung. Dieser darf nicht **azure_superuser**, **admin**, **administrator**, **root**, **guest** oder **public** lauten.
admin-password | *sicheres Kennwort* | Kennwort des Administratorbenutzers. Es muss 8 bis 128 Zeichen aus drei der folgenden Kategorien enthalten: Englische Großbuchstaben, englische Kleinbuchstaben, Zahlen und nicht alphanumerische Zeichen.
sku-name|GP_Gen5_2| Name des Tarifs und der Computekonfiguration. Halten Sie sich an die Konvention „{Tarif} _{Computegeneration}_ {virtuelle Kerne}“ in Kurzform. Weitere Informationen finden Sie unter [Azure Database for PostgreSQL – Preise](https://azure.microsoft.com/pricing/details/postgresql/server/).

>[!IMPORTANT] 
>- Die Standardversion von PostgreSQL auf dem Server lautet 9.6. Informationen zu allen unterstützten Versionen finden Sie unter [Unterstützte PostgreSQL-Hauptversionen](./concepts-supported-versions.md).
>- Alle Argumente für den Befehl **az postgres server create** finden Sie in [diesem Referenzdokument](/cli/azure/postgres/server#az-postgres-server-create).
>- SSL ist standardmäßig auf dem Server aktiviert. Weitere Informationen zu SSL finden Sie unter [Konfigurieren der TLS-Konnektivität in Azure Database for PostgreSQL (Einzelserver)](./concepts-ssl-connection-security.md).

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurieren einer Firewallregel auf Serverebene 
Der erstellte Server ist standardmäßig durch Firewallregeln geschützt und nicht öffentlich zugänglich. Sie können die Firewallregel auf dem Server mithilfe des Befehls [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) konfigurieren, damit Ihre lokale Umgebung Zugriff zum Herstellen einer Verbindung mit dem Server erhält. 

Das folgende Beispiel erstellt eine Firewallregel namens `AllowMyIP`, die Verbindungen über eine bestimmte IP-Adresse (192.168.0.1) zulässt. Ersetzen Sie die IP-Adresse oder den IP-Adressbereich durch den Ort, von dem Sie eine Verbindung herstellen möchten. Wenn Sie Ihre IP-Adresse nicht kennen, rufen Sie sie unter [WhatIsMyIPAddress.com](https://whatismyipaddress.com/) ab.


```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Stellen Sie sicher, dass die Firewall des Netzwerks Port 5432 zulässt, um Konnektivitätsprobleme zu vermeiden. Azure Database for PostgreSQL-Server nutzen diesen Port. 

## <a name="get-the-connection-information"></a>Abrufen der Verbindungsinformationen

Geben Sie zum Herstellen einer Verbindung mit dem Server Hostinformationen und Anmeldeinformationen für den Zugriff an.

```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

Das Ergebnis liegt im JSON-Format vor. Notieren Sie sich die Werte für **administratorLogin** und **fullyQualifiedDomainName**.

```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
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
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-the-azure-database-for-postgresql-server-by-using-psql"></a>Herstellen einer Verbindung mit dem Azure Database for PostgreSQL-Server mithilfe von psql
Der [psql](https://www.postgresql.org/docs/current/static/app-psql.html)-Client wird häufig zum Herstellen einer Verbindung mit PostgreSQL-Servern verwendet. Sie können mithilfe von psql und [Azure Cloud Shell](../cloud-shell/overview.md) eine Verbindung mit Ihrem Server herstellen. Sie können auch psql in Ihrer lokalen Umgebung verwenden, sofern verfügbar. Mit einem neuen PostgreSQL-Server wird automatisch eine leere Datenbank (**postgres**) erstellt. Sie können diese Datenbank verwenden, um eine Verbindung mit psql herzustellen, wie im folgenden Code gezeigt: 

   ```bash
 psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

> [!TIP]
> Wenn Sie lieber einen URL-Pfad zum Herstellen einer Verbindung mit Postgres verwenden, codiert die URL das @-Zeichen im Benutzernamen mit `%40`. Die Verbindungszeichenfolge für psql sieht beispielsweise so aus:
>
> ```
> psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
> ```


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie diese Ressourcen nicht für einen anderen Schnellstart oder ein anderes Tutorial benötigen, können Sie sie mit dem folgenden Befehl löschen: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Wenn Sie nur den neu erstellten Server löschen möchten, können Sie den Befehl [az postgres server delete](/cli/azure/postgres/server) ausführen.

```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Migrieren der Datenbank mit Export und Import](./howto-migrate-using-export-and-import.md)

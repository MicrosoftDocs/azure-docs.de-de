---
title: 'Schnellstart: Erstellen eines Servers – Azure-Befehlszeilenschnittstelle – Azure Database for MariaDB'
description: Dieser Schnellstart beschreibt die Verwendung der Azure-Befehlszeilenschnittstelle zum Erstellen eines Azure Database for MariaDB-Servers in einer Azure-Ressourcengruppe.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 3279150d0cb7b287f0a78581094a51356033596c
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435738"
---
# <a name="quickstart-create-an-azure-database-for-mariadb-server-by-using-the-azure-cli"></a>Schnellstart: Erstellen eines Azure Database for MariaDB-Servers mit der Azure-Befehlszeilenschnittstelle

Sie können die Azure-Befehlszeilenschnittstelle (Azure CLI) verwenden, um Azure-Ressourcen über die Befehlszeile oder mit Skripts zu erstellen und zu verwalten. Dieser Schnellstart beschreibt die Verwendung der Azure-Befehlszeilenschnittstelle zum Erstellen eines Azure Database for MariaDB-Servers in einer Azure-Ressourcengruppe in etwa fünf Minuten.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

Falls Sie über mehrere Abonnements verfügen, wählen Sie das Abonnement aus, das die Ressource enthält, oder das Abonnement, über das Ihre Abrechnung erfolgt. Verwenden Sie zum Auswählen einer bestimmte Abonnement-ID in Ihrem Konto den Befehl [az account set](/cli/azure/account#az-account-set):

```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Befehl [az group create](/cli/azure/group#az-group-create) eine [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md). Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen als Gruppe bereitgestellt und verwaltet werden.

Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen `myresourcegroup` am Standort `westus`:

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Erstellen eines Azure Database for MariaDB-Servers

Erstellen Sie mit dem Befehl [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) einen Azure Database for MariaDB-Server. Ein Server kann mehrere Datenbanken verwalten. In der Regel wird für jedes Projekt oder jeden Benutzer eine separate Datenbank verwendet.

Einstellung | Beispielwert | BESCHREIBUNG
---|---|---
name | **mydemoserver** | Geben Sie einen eindeutigen Namen ein, der Ihren Azure Database for MariaDB-Server identifiziert. Der Servername darf nur Kleinbuchstaben, Zahlen und den Bindestrich (-) enthalten. Er muss zwischen 3 und 63 Zeichen lang sein.
resource-group | **myresourcegroup** | Geben Sie den Namen der Azure-Ressourcengruppe ein.
sku-name | **GP_Gen5_2** | Der Name der SKU. Die Benennung folgt der Konvention *Tarif*\_*Computegeneration*\_*V-Kerne* in Kurzform. Weitere Informationen zum **sku-name**-Parameter finden Sie im Abschnitt nach dieser Tabelle.
backup-retention | **7** | Gibt die Aufbewahrungsdauer für eine Sicherung an. Die Einheit ist Tage. Bereich: 7 bis 35. 
geo-redundant-backup | **Disabled** | Gibt an, ob georedundante Sicherungen für diesen Server aktiviert werden sollen. Zulässige Werte: **Aktiviert**, **Deaktiviert**.
location | **westus** | Der Azure-Standort für den Server.
ssl-enforcement | **Aktiviert** | Gibt an, ob SSL für diesen Server aktiviert werden soll. Zulässige Werte: **Aktiviert**, **Deaktiviert**.
storage-size | **51.200** | Die Speicherkapazität des Servers (Einheit: MB). Gültige Speichergrößen sind Werte ab 5.120 MB (Minimum) mit Erhöhungen in Inkrementen von 1.024 MB. Weitere Informationen zu den Grenzwerten für die Speichergröße finden Sie unter [Tarife](./concepts-pricing-tiers.md). 
version | **10.2** | Die Hauptversion der MariaDB-Engine.
admin-user | **myadmin** | Der Benutzername für die Administratoranmeldung. Der **admin-user**-Parameter darf nicht auf **azure_superuser**, **admin**, **administrator**, **root**, **guest** oder **public** festgelegt werden.
admin-password | *Ihr Kennwort* | Das Kennwort des Administratorbenutzers. Ihr Kennwort muss zwischen acht und 128 Zeichen lang sein. Es muss Zeichen aus drei der folgenden Kategorien enthalten: Englische Großbuchstaben, englische Kleinbuchstaben, Zahlen und nicht alphanumerische Zeichen.

Für den Parameterwert des SKU-Namens wird die Konvention „{Tarif}\_{Computegeneration}\_{V-Kerne}“ verwendet, wie in den folgenden Beispielen:
+ `--sku-name B_Gen5_1` ist „Basic“, „Gen 5“ und „1 virtueller Kern“ zugeordnet. Bei dieser Option handelt es sich um die kleinste verfügbare SKU.
+ `--sku-name GP_Gen5_32` ist „Universell“, „Gen 5“ und „32 V-Kerne“ zugeordnet.
+ `--sku-name MO_Gen5_2` ist „Arbeitsspeicheroptimiert“, „Gen 5“ und „2 V-Kerne“ zugeordnet.

Weitere Informationen zu den gültigen Werten für die einzelnen Regionen und den Tarifen finden Sie unter [Tarife](./concepts-pricing-tiers.md).

Im folgenden Beispiel wird ein Server mit dem Namen **mydemoserver** in der Region „USA, Westen“ erstellt. Der Server ist in der Ressourcengruppe **myresourcegroup** enthalten, und der Benutzername für die Serveradministratoranmeldung lautet **myadmin**. Der Server ist ein Gen 5-Server mit dem Tarif „Universell“ und zwei virtuellen Kernen. Ein Servername wird einem DNS-Namen zugeordnet und muss in Azure global eindeutig sein. Ersetzen Sie `<server_admin_password>` durch Ihr eigenes Serveradministratorkennwort.

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```

> [!NOTE]
> Ziehen Sie die Verwendung des Tarifs „Basic“ in Betracht, wenn geringe Compute- und E/A-Leistung für Ihre Workload angemessen ist. Beachten Sie, dass im Tarif „Basic“ erstellte Server später nicht auf „Universell“ oder „Arbeitsspeicheroptimiert“ skaliert werden können. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/mariadb/).

## <a name="configure-a-firewall-rule"></a>Konfigurieren einer Firewallregel

Erstellen Sie mit dem Befehl [az mariadb server firewall-rule create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) eine Azure Database for MariaDB-Firewallregel auf Serverebene. Eine Firewallregel auf Serverebene ermöglicht es einer externen Anwendung (z. B. dem MySQL-Befehlszeilentool oder MySQL Workbench), über die Firewall des Azure Database for MariaDB-Diensts eine Verbindung mit Ihrem Server herzustellen.

Das folgende Beispiel erstellt eine Firewallregel namens `AllowMyIP`, die Verbindungen über eine bestimmte IP-Adresse (192.168.0.1) zulässt. Geben Sie entsprechend dem Standort, von dem Sie eine Verbindung herstellen, eine IP-Adresse oder einen Bereich von IP-Adressen an.

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Die Kommunikation für Verbindungen mit Azure Database for MariaDB erfolgt über Port 3306. Wenn Sie versuchen, eine Verbindung über ein Unternehmensnetzwerk herzustellen, wird ausgehender Datenverkehr über Port 3306 unter Umständen nicht zugelassen. In diesem Fall können Sie nur dann eine Verbindung mit Ihrem Server herstellen, wenn Ihre IT-Abteilung Port 3306 öffnet.

## <a name="configure-ssl-settings"></a>Konfigurieren der SSL-Einstellungen

Standardmäßig werden SSL-Verbindungen zwischen Ihrem Server und Clientanwendungen erzwungen. Mit dieser Standardeinstellung wird die Sicherheit von Daten während des Betriebs gewährleistet, indem der Datenstrom über das Internet verschlüsselt wird. Für diesen Schnellstart deaktivieren Sie SSL-Verbindungen für Ihren Server. Das Deaktivieren von SSL ist für Produktionsserver aber nicht zu empfehlen. Weitere Informationen finden Sie unter [Konfigurieren von SSL-Verbindungen in der Anwendung für eine sichere Verbindung mit Azure Database for MariaDB](./howto-configure-ssl.md).

Das folgende Beispiel deaktiviert die SSL-Erzwingung auf Ihrem Azure Database for MariaDB-Server:

```azurecli-interactive
az mariadb server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
```

## <a name="get-connection-information"></a>Abrufen von Verbindungsinformationen

Zum Herstellen einer Verbindung zum Server müssen Sie Hostinformationen und Anmeldeinformationen für den Zugriff angeben. Führen Sie zum Abrufen der Verbindungsinformationen den folgenden Befehl aus:

```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

Das Ergebnis liegt im JSON-Format vor. Notieren Sie die Werte für **fullyQualifiedDomainName** und **administratorLogin**.

```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mariadb.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMariaDB/servers/mydemoserver",
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
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

## <a name="connect-to-the-server-by-using-the-mysql-command-line-tool"></a>Herstellen einer Verbindung mit dem Server mit dem MySQL-Befehlszeilentool

Stellen Sie mit dem MySQL-Befehlszeilentool eine Verbindung mit Ihrem Server her. Sie können das Befehlszeilentool [herunterladen](https://dev.mysql.com/downloads/) und auf Ihrem Computer installieren. Sie können auch auf das Befehlszeilentool zugreifen, indem Sie in einem der Codebeispiele in diesem Artikel auf die Schaltfläche **Ausprobieren** klicken. Alternativ können Sie im Azure-Portal auf der Symbolleiste oben rechts auf die Schaltfläche **>_** klicken, um **Azure Cloud Shell** zu öffnen, und so auf das Befehlszeilentool zugreifen.

So stellen Sie mit dem MySQL-Befehlszeilentool eine Verbindung mit dem Server her:

1. Stellen Sie eine Verbindung mit dem Server her:

   ```azurecli-interactive
   mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   ```

2. Zeigen Sie den Serverstatus an der Eingabeaufforderung `mysql>` an:

   ```sql
   status
   ```

   Es sollte ein Text ähnlich dem folgenden angezeigt werden:

   ```cmd
   C:\Users\>mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   Enter password: ***********
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 65512
   Server version: 5.6.39.0 MariaDB Server

   Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

   Oracle is a registered trademark of Oracle Corporation and/or its
   affiliates. Other names may be trademarks of their respective
   owners.

   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

   mysql> status
   --------------
   mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64)

   Connection id:          64681
   Current database:
   Current user:           myadmin@40.118.201.21
   SSL:                    Cipher in use is AES256-SHA
   Current pager:          stdout
   Using outfile:          ''
   Using delimiter:        ;
   Server version:         5.6.39.0 MariaDB Server
   Protocol version:       10
   Connection:             mydemoserver.mariadb.database.azure.com via TCP/IP
   Server characterset:    latin1
   Db     characterset:    latin1
   Client characterset:    utf8
   Conn.  characterset:    utf8
   TCP port:               3306
   Uptime:                 1 day 3 hours 28 min 50 sec

   Threads: 10  Questions: 29002  Slow queries: 0  Opens: 33  Flush tables: 3  Open tables: 1  Queries per second avg: 0.293
   --------------

   mysql>
   ```

> [!TIP]
> Zusätzliche Befehle finden Sie unter [MySQL 5.7-Referenzhandbuch – Kapitel 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-the-server-by-using-mysql-workbench"></a>Herstellen einer Verbindung mit dem Server unter Verwendung von MySQL Workbench

1. Öffnen Sie MySQL Workbench auf Ihrem Clientcomputer. Falls die Anwendung noch nicht installiert ist, können Sie sie [herunterladen](https://dev.mysql.com/downloads/workbench/) und installieren.

2. Geben Sie im Dialogfeld **Setup New Connection** (Neue Verbindung einrichten) auf der Registerkarte **Parameter** folgende Informationen ein:

   ![Einrichten einer neuen Verbindung](./media/quickstart-create-mariadb-server-database-using-azure-cli/setup-new-connection.png)

   | Einstellung | Vorgeschlagener Wert | BESCHREIBUNG |
   |---|---|---|
   | Verbindungsname | **Beispielverbindung** | Geben Sie eine Bezeichnung für die Verbindung ein (Sie können einen beliebigen Verbindungsnahmen verwenden). |
   | Verbindungsmethode | **Standard (TCP/IP)** | Verwenden Sie das TCP/IP-Protokoll zum Herstellen einer Verbindung mit Azure Database for MariaDB. |
   | Hostname | **mydemoserver.mariadb.database.azure.com** | Der Servername, den Sie zuvor notiert haben. |
   | Port | **3306** | Der Standardport für Azure Database for MariaDB. |
   | Username | **myadmin\@mydemoserver** | Der Benutzername für die Serveradministratoranmeldung, den Sie zuvor notiert haben. |
   | Kennwort | *Ihr Kennwort* | Verwenden Sie das Kennwort des Administratorkontos, das Sie zuvor eingerichtet haben. |

3. Klicken Sie auf **Verbindung testen**, um zu überprüfen, ob alle Parameter korrekt konfiguriert sind.

4. Wählen Sie die Verbindung aus, um eine Verbindung mit dem Server herzustellen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die in diesem Schnellstart verwendeten Ressourcen nicht für einen anderen Schnellstart oder ein anderes Tutorial benötigen, können Sie sie mit dem folgenden Befehl löschen: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Falls Sie nur den Server löschen möchten, den Sie in diesem Schnellstart erstellt haben, führen Sie den Befehl [az mariadb server delete](/cli/azure/mariadb/server#az-mariadb-server-delete) aus:

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Entwerfen einer MariaDB-Datenbank mit der Azure CLI](tutorial-design-database-cli.md)

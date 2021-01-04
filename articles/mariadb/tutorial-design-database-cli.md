---
title: 'Tutorial: Entwerfen einer Azure Database for MariaDB-Instanz – Azure CLI'
description: In diesem Tutorial wird das Erstellen und Verwalten des Servers und der Datenbank für Azure Database for MariaDB mithilfe der Azure CLI über die Befehlszeile erläutert.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 3/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 8f6f8d5a2cc9dc17d08486125fc2e44307c1be46
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436656"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-using-azure-cli"></a>Tutorial: Entwerfen einer Azure Database for MariaDB-Instanz mithilfe der Azure CLI

Azure Database for MariaDB ist ein relationaler Datenbankdienst in der Microsoft-Cloud, der auf der Datenbank-Engine „MariaDB Community Edition“ basiert. In diesem Tutorial verwenden Sie die Azure CLI (Befehlszeilenschnittstelle) und andere Hilfsprogramme, um zu lernen, wie Sie Folgendes ausführen:

> [!div class="checklist"]
> * Erstellen einer Azure Database for MariaDB-Instanz
> * Konfigurieren der Serverfirewall
> * Verwenden des [mysql-Befehlszeilentools](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) zum Erstellen einer Datenbank
> * Laden von Beispieldaten
> * Daten abfragen
> * Aktualisieren von Daten
> * Wiederherstellen von Daten

Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert. 

Wenn Sie über mehrere Abonnements verfügen, wählen Sie das entsprechende Abonnement aus, in dem die Ressource vorhanden ist oder in Rechnung gestellt wird. Wählen Sie eine bestimmte Abonnement-ID unter Ihrem Konto mit dem Befehl [az account set](/cli/azure/account#az-account-set) aus.
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
Erstellen Sie mit dem Befehl [az group create](/cli/azure/group#az-group-create) eine [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md). Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und als Gruppe verwaltet werden.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen `myresourcegroup` am Standort `westus` erstellt.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Erstellen eines Azure Database for MariaDB-Servers
Erstellen Sie mit dem Befehl `az mariadb server create` einen Azure Database for MariaDB-Server. Ein Server kann mehrere Datenbanken verwalten. In der Regel wird für jedes Projekt oder jeden Benutzer eine separate Datenbank verwendet.

Das folgende Beispiel erstellt einen Azure Database for MariaDB-Server in `westus` in der Ressourcengruppe `myresourcegroup` mit dem Namen `mydemoserver`. Der Server verfügt über die Administratoranmeldung `myadmin`. Es ist ein Gen 5-Server vom Typ „Universell“ mit zwei virtuellen Kernen. Ersetzen Sie das `<server_admin_password>` durch einen eigenen Wert.

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```
Für den Parameterwert des SKU-Namens wird die Konvention „{Tarif}\_{Computegeneration}\_{V-Kerne}“ verwendet, wie in den folgenden Beispielen:
+ `--sku-name B_Gen5_4` ist „Basic“, „Gen 5“ und „4 V-Kerne“ zugeordnet.
+ `--sku-name GP_Gen5_32` ist „Universell“, „Gen 5“ und „32 V-Kerne“ zugeordnet.
+ `--sku-name MO_Gen5_2` ist „Arbeitsspeicheroptimiert“, „Gen 5“ und „2 V-Kerne“ zugeordnet.

Informationen zu den gültigen Werten pro Region und Tarif finden Sie in der Dokumentation zu [Tarifen](./concepts-pricing-tiers.md).

> [!IMPORTANT]
> Der hier angegebene Benutzername und das Kennwort für den Serveradministrator sind erforderlich, um später in diesem Schnellstart die Anmeldung am Server und bei den zugehörigen Datenbanken auszuführen. Behalten Sie diese Angaben im Kopf, oder notieren Sie sie zur späteren Verwendung.


## <a name="configure-firewall-rule"></a>Konfigurieren der Firewallregel
Erstellen Sie mit dem Befehl `az mariadb server firewall-rule create` eine Firewallregel auf der Ebene des Azure Database for MariaDB-Servers. Eine Firewallregel auf Serverebene ermöglicht einer externen Anwendung, etwa dem Befehlszeilentool **mysql** oder MySQL Workbench, über die Firewall des Azure MariaDB-Diensts eine Verbindung mit Ihrem Server herzustellen.

Das folgende Beispiel erstellt eine Firewallregel namens `AllowMyIP`, die Verbindungen über eine bestimmte IP-Adresse (192.168.0.1) zulässt. Fügen Sie die IP-Adresse oder den IP-Adressbereich des Orts ein, von dem Sie eine Verbindung herstellen möchten.

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

## <a name="get-the-connection-information"></a>Abrufen der Verbindungsinformationen

Zum Herstellen einer Verbindung zum Server müssen Sie Hostinformationen und Anmeldeinformationen für den Zugriff angeben.
```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

Das Ergebnis liegt im JSON-Format vor. Notieren Sie sich die Werte für **fullyQualifiedDomainName** und **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
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

## <a name="connect-to-the-server-using-mysql"></a>Herstellen einer Verbindung mit dem Server unter Verwendung von mysql
Verwenden Sie das [MySQL-Befehlszeilentool](https://dev.mysql.com/doc/refman/5.7/en/mysql.html), um eine Verbindung mit Ihrem Azure Database for MariaDB-Server herzustellen. In diesem Beispiel lautet der Befehl:
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Leere Datenbank erstellen
Erstellen Sie eine leere Datenbank, nachdem Sie eine Verbindung mit dem Server hergestellt haben.
```sql
mysql> CREATE DATABASE mysampledb;
```

Führen Sie an der Eingabeaufforderung den folgenden Befehl zum Wechseln der Verbindung zu dieser neu erstellten Datenbank aus:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Erstellen von Tabellen in der Datenbank
Da Sie nun wissen, wie Sie eine Verbindung mit der Azure Database for MariaDB-Datenbank herstellen, können Sie einige grundlegende Aufgaben ausführen.

Zunächst erstellen Sie eine Tabelle und füllen sie mit einigen Daten auf. Wir erstellen eine Tabelle, die Bestandsinformationen speichert.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Laden von Daten in die Tabellen
Jetzt können Sie Daten in die Tabelle einfügen. Führen Sie im geöffneten Eingabeaufforderungsfenster die folgende Abfrage zum Einfügen einiger Datenzeilen aus.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Die Tabelle, die Sie zuvor erstellt haben, enthält nun zwei Zeilen mit Beispieldaten.

## <a name="query-and-update-the-data-in-the-tables"></a>Abfragen und Aktualisieren der Daten in den Tabellen
Führen Sie die folgende Abfrage aus, um Informationen aus der Datenbanktabelle abzurufen.
```sql
SELECT * FROM inventory;
```

Sie können auch die Daten in den Tabellen aktualisieren.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Die Zeile wird entsprechend aktualisiert, wenn Sie Daten abrufen.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Wiederherstellen eines früheren Zustands einer Datenbank
Stellen Sie sich vor, Sie haben versehentlich diese Tabelle gelöscht. Dies ist eine Situation, in der Sie die Datenbank nicht einfach wiederherstellen können. Azure Database for MariaDB ermöglicht es Ihnen, zu einem beliebigen Zeitpunkt innerhalb der letzten 35 Tage zurückzugehen und diesen Zeitpunkt auf einem neuen Server wiederherzustellen. Sie können diesen neuen Server zur Wiederherstellung gelöschter Daten verwenden. Mithilfe der folgenden Schritte wird der Status des Beispielservers zu einem Zeitpunkt wiederhergestellt, der vor dem Hinzufügen der Tabelle liegt.

Zum Wiederherstellen benötigen Sie die folgenden Informationen:

- Wiederherstellungspunkt: Wählen Sie einen Zeitpunkt vor der Änderung des Servers aus. Er darf nicht weiter zurückliegen als das älteste Sicherungsdatum der Quelldatenbank.
- Zielserver: Geben Sie einen neuen Servernamen für die Wiederherstellung ein.
- Quellserver: Geben Sie den Namen des Servers ein, von dem aus wiederhergestellt werden soll.
- Speicherort: Sie können nicht die Region wählen. Standardmäßig ist dieser Wert mit dem Quellserver identisch.

```azurecli-interactive
az mariadb server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mydemoserver
```

Für den Befehl `az mariadb server restore` sind folgende Parameter erforderlich:

| Einstellung | Vorgeschlagener Wert | BESCHREIBUNG  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Die Ressourcengruppe, in der sich der Quellserver befindet.  |
| name | mydemoserver-restored | Der Name des neuen Servers, der durch den Befehl „restore“ erstellt wird. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Wählen Sie einen Zeitpunkt für die Wiederherstellung aus. Datum und Zeit müssen innerhalb des Aufbewahrungszeitraums für Sicherungen des Quellservers liegen. Verwenden Sie das Datums- und Zeitformat nach ISO 8601. Beispielsweise können Sie Ihre eigene lokale Zeitzone wie etwa `2017-04-13T05:59:00-08:00` oder das UTC-Format Zulu `2017-04-13T13:59:00Z` verwenden. |
| source-server | mydemoserver | Der Name oder die ID des Quellservers, über den die Wiederherstellung durchgeführt wird. |

Bei der Wiederherstellung eines Servers zu einem früheren Zeitpunkt wird ein neuer Server erstellt. Dabei wird der ursprüngliche Server zu dem von Ihnen festgelegten Zeitpunkt kopiert. Die Werte zum Standort und Tarif des wiederhergestellten Servers sind mit denen des Quellservers identisch.

Der Befehl ist synchron und gibt nach der Wiederherstellung des Servers Daten zurück. Sobald die Wiederherstellung abgeschlossen ist, suchen Sie nach dem neuen Server, der erstellt wurde. Stellen Sie sicher, dass die Daten wie erwartet wiederhergestellt wurden.

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie Folgendes gelernt:
> [!div class="checklist"]
> * Erstellen eines Azure Database for MariaDB-Servers
> * Konfigurieren der Serverfirewall
> * Verwenden des [mysql-Befehlszeilentools](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) zum Erstellen einer Datenbank
> * Laden von Beispieldaten
> * Daten abfragen
> * Aktualisieren von Daten
> * Wiederherstellen von Daten
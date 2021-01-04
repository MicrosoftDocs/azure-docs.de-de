---
title: 'Tutorial: Entwerfen eines Azure Database for PostgreSQL-Einzelservers – Azure-Befehlszeilenschnittstelle'
description: In diesem Tutorial wird veranschaulicht, wie Sie Ihren ersten Azure Database for PostgreSQL – Einzelserver mit der Azure-CLI erstellen, konfigurieren und abfragen.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: mvc, devx-track-azurecli
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 06/25/2019
ms.openlocfilehash: 019e6e738ea312b7e6a16c44354c7dcd54e24f2f
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93331895"
---
# <a name="tutorial-design-an-azure-database-for-postgresql---single-server-using-azure-cli"></a>Tutorial: Entwerfen einer Azure Database for PostgreSQL – Einzelserver unter Verwendung der Azure-Befehlszeilenschnittstelle 
In diesem Tutorial verwenden Sie die Azure CLI (Befehlszeilenschnittstelle) und andere Hilfsprogramme, um zu lernen, wie Sie Folgendes ausführen:
> [!div class="checklist"]
> * Erstellen einer Azure-Datenbank für PostgreSQL-Server
> * Konfigurieren der Serverfirewall
> * Erstellen einer Datenbank mit dem Hilfsprogramm [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html)
> * Laden von Beispieldaten
> * Daten abfragen
> * Aktualisieren von Daten
> * Wiederherstellen von Daten

Sie können Azure Cloud Shell im Browser verwenden oder die [Azure CLI auf Ihrem Computer installieren]( /cli/azure/install-azure-cli), um die Befehle in diesem Tutorial auszuführen.

## <a name="prerequisites"></a>Voraussetzungen
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für diesen Artikel die Azure CLI-Version 2.0 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli). 

Wenn Sie über mehrere Abonnements verfügen, wählen Sie das entsprechende Abonnement aus, in dem die Ressource vorhanden ist oder in Rechnung gestellt wird. Wählen Sie eine bestimmte Abonnement-ID unter Ihrem Konto mit dem Befehl [az account set](/cli/azure/account) aus.
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
Erstellen Sie mit dem Befehl [az group create](/cli/azure/group) eine [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md). Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und als Gruppe verwaltet werden. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen `myresourcegroup` am Standort `westus` erstellt.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Erstellen einer Azure-Datenbank für PostgreSQL-Server
Erstellen Sie mit dem Befehl [az postgres server create](/cli/azure/postgres/server) eine [Azure-Datenbank für PostgreSQL-Server](overview.md). Ein Server enthält eine Gruppe von Datenbanken, die als Gruppe verwaltet werden. 

Im folgenden Beispiel wird ein Server mit dem Namen `mydemoserver` in der Ressourcengruppe `myresourcegroup` mit dem Serveradministrator-Anmeldenamen `myadmin` erstellt. Der Name eines Servers wird dem DNS-Namen zugeordnet und muss deshalb in Azure global eindeutig sein. Ersetzen Sie das `<server_admin_password>` durch einen eigenen Wert. Es ist ein Gen 5-Server vom Typ „Universell“ mit zwei virtuellen Kernen.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```
Für den Parameterwert des SKU-Namens wird die Konvention „{Tarif}\_{Computegeneration}\_{V-Kerne}“ verwendet, wie in den folgenden Beispielen:
+ `--sku-name B_Gen5_2` ist „Basic“, „Gen 5“ und „2 V-Kerne“ zugeordnet.
+ `--sku-name GP_Gen5_32` ist „Universell“, „Gen 5“ und „32 V-Kerne“ zugeordnet.
+ `--sku-name MO_Gen5_2` ist „Arbeitsspeicheroptimiert“, „Gen 5“ und „2 V-Kerne“ zugeordnet.

Informationen zu den gültigen Werten pro Region und Tarif finden Sie in der Dokumentation zu [Tarifen](./concepts-pricing-tiers.md).

> [!IMPORTANT]
> Der hier angegebene Benutzername und das Kennwort für den Serveradministrator sind erforderlich, um später in diesem Schnellstart die Anmeldung am Server und bei den zugehörigen Datenbanken auszuführen. Behalten Sie diese Angaben im Kopf, oder notieren Sie sie zur späteren Verwendung.

Standardmäßig wird die **postgres**-Datenbank unter dem Server erstellt. Die [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html)-Datenbank ist eine Standarddatenbank für die Verwendung durch Benutzer, Hilfsprogramme und Drittanbieteranwendungen. 


## <a name="configure-a-server-level-firewall-rule"></a>Konfigurieren einer Firewallregel auf Serverebene

Erstellen Sie mit dem Befehl [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) eine Azure-PostgreSQL-Firewallregel auf Serverebene. Eine Firewallregel auf Serverebene ermöglicht einer externen Anwendung wie z.B. [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) oder [PgAdmin](https://www.pgadmin.org/), über die Firewall des Azure-PostgreSQL-Diensts eine Verbindung mit Ihrem Server herzustellen. 

Sie können eine Firewallregel festlegen, die einen Bereich von IP-Adressen abdeckt, mit denen Verbindungen aus dem Netzwerk hergestellt werden können. Im folgenden Beispiel wird mit [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) die Firewallregel `AllowMyIP` erstellt, die Verbindungen von einer einzelnen IP-Adresse ermöglicht.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

Zum Einschränken des Zugriffs auf Ihren Azure PostgreSQL-Server ausschließlich auf Ihr Netzwerk können Sie die Firewallregel so festlegen, dass nur der IP-Adressbereich Ihres Unternehmensnetzwerks abgedeckt wird.

> [!NOTE]
> Der Azure-PostgreSQL-Server kommuniziert über Port 5432. Wenn Sie eine Verbindung aus einem Unternehmensnetzwerk heraus herstellen, wird der ausgehende Datenverkehr über Port 5432 von der Firewall Ihres Netzwerks unter Umständen nicht zugelassen. Ihre IT-Abteilung muss Port 5432 öffnen, damit Sie eine Verbindung mit Ihrem Azure SQL-Datenbank-Server herstellen können.
>

## <a name="get-the-connection-information"></a>Abrufen der Verbindungsinformationen

Zum Herstellen einer Verbindung zum Server müssen Sie Hostinformationen und Anmeldeinformationen für den Zugriff angeben.
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

## <a name="connect-to-azure-database-for-postgresql-database-using-psql"></a>Herstellen einer Verbindung mit der Azure-Datenbank für die PostgreSQL-Datenbank mit psql
Wenn auf Ihrem Clientcomputer PostgreSQL installiert ist, können Sie mit einer lokalen Instanz von [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) oder der Azure-Cloudkonsole eine Verbindung mit einem Azure-PostgreSQL-Server herstellen. Wir stellen jetzt mit dem Befehlszeilen-Hilfsprogramm psql eine Verbindung mit der Azure-Datenbank für PostgreSQL-Server her.

1. Führen Sie den folgenden psql-Befehl aus, um eine Verbindung mit einer Azure Database for PostgreSQL-Datenbank herzustellen:
   ```
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Mit dem folgenden Befehl wird beispielsweise mit den Zugriffsanmeldeinformationen eine Verbindung mit der Standarddatenbank **postgres** auf Ihrem PostgreSQL-Server **mydemoserver.postgres.database.azure.com** hergestellt. Geben Sie das `<server_admin_password>` ein, das Sie bei der Aufforderung zur Kennworteingabe ausgewählt haben.
  
   ```
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Wenn Sie lieber einen URL-Pfad zum Herstellen einer Verbindung mit Postgres verwenden, codiert die URL das @-Zeichen im Benutzernamen mit `%40`. Die Verbindungszeichenfolge für psql sieht beispielsweise so aus:
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```

2. Sobald Sie mit dem Server verbunden sind, erstellen Sie an der Eingabeaufforderung eine leere Datenbank:
   ```sql
   CREATE DATABASE mypgsqldb;
   ```

3. Führen Sie an der Eingabeaufforderung den folgenden Befehl zum Wechseln der Verbindung zur neu erstellten Datenbank **mypgsqldb** aus:
   ```sql
   \c mypgsqldb
   ```

## <a name="create-tables-in-the-database"></a>Erstellen von Tabellen in der Datenbank
Da Sie nun wissen, wie Sie eine Verbindung mit Azure Database for PostgreSQL herstellen, können Sie einige grundlegende Aufgaben ausführen:

Zunächst erstellen Sie eine Tabelle und füllen sie mit einigen Daten auf. Erstellen Sie beispielsweise eine Tabelle, die Bestandsinformationen nachverfolgt:
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

Um die neu erstellte Tabelle in der Liste der Tabellen anzuzeigen, geben Sie Folgendes ein:
```sql
\dt
```

## <a name="load-data-into-the-table"></a>Laden von Daten in die Tabelle
Die Tabelle ist jetzt erstellt. Fügen Sie einige Daten ein. Führen Sie im geöffneten Eingabeaufforderungsfenster die folgende Abfrage zum Einfügen einiger Datenzeilen aus:
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Die Tabelle, die Sie zuvor erstellt haben, enthält nun zwei hinzugefügte Zeilen mit Beispieldaten.

## <a name="query-and-update-the-data-in-the-tables"></a>Abfragen und Aktualisieren der Daten in den Tabellen
Führen Sie die folgende Abfrage aus, um Informationen aus der Bestandstabelle abzurufen: 
```sql
SELECT * FROM inventory;
```

Sie können die Daten in der Bestandstabelle auch aktualisieren:
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Die aktualisierten Werte werden angezeigt, wenn Sie die Daten abrufen:
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Wiederherstellen eines früheren Zustands einer Datenbank
Stellen Sie sich vor, Sie haben versehentlich eine Tabelle gelöscht. Dies ist eine Situation, in der Sie die Datenbank nicht einfach wiederherstellen können. Mit Azure Database for PostgreSQL können Sie eine beliebige Zeitpunktsicherung Ihres Servers (abhängig vom konfigurierten Aufbewahrungszeitraum für Sicherungen) auf einem neuen Server wiederherstellen. Sie können diesen neuen Server zur Wiederherstellung gelöschter Daten verwenden. 

Der folgende Befehl stellt den Beispielserver zu einem Zeitpunkt wieder her, der vor dem Hinzufügen der Tabelle liegt:
```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mydemoserver
```

Für den Befehl `az postgres server restore` sind folgende Parameter erforderlich:

| Einstellung | Vorgeschlagener Wert | BESCHREIBUNG  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Die Ressourcengruppe, in der sich der Quellserver befindet.  |
| name | mydemoserver-restored | Der Name des neuen Servers, der durch den Befehl „restore“ erstellt wird. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Wählen Sie einen Zeitpunkt für die Wiederherstellung aus. Datum und Zeit müssen innerhalb des Aufbewahrungszeitraums für Sicherungen des Quellservers liegen. Verwenden Sie das Datums- und Zeitformat nach ISO 8601. Beispielsweise können Sie Ihre eigene lokale Zeitzone wie etwa `2017-04-13T05:59:00-08:00` oder das UTC-Format Zulu `2017-04-13T13:59:00Z` verwenden. |
| source-server | mydemoserver | Der Name oder die ID des Quellservers, über den die Wiederherstellung durchgeführt wird. |

Bei der Wiederherstellung eines Servers zu einem früheren Zeitpunkt wird ein neuer Server erstellt. Dabei wird der ursprüngliche Server zu dem von Ihnen festgelegten Zeitpunkt kopiert. Die Werte zum Standort und Tarif des wiederhergestellten Servers sind mit denen des Quellservers identisch.

Der Befehl ist synchron und gibt nach der Wiederherstellung des Servers Daten zurück. Sobald die Wiederherstellung abgeschlossen ist, suchen Sie nach dem neuen Server, der erstellt wurde. Stellen Sie sicher, dass die Daten wie erwartet wiederhergestellt wurden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In den vorherigen Schritten haben Sie Azure-Ressourcen in einer Servergruppe erstellt. Wenn Sie diese Ressourcen nicht mehr benötigen, löschen Sie die Servergruppe. Klicken Sie auf der Seite *Übersicht* für Ihre Servergruppe auf die Schaltfläche *Löschen*. Wenn Sie auf einer Popupseite dazu aufgefordert werden, bestätigen Sie den Namen der Servergruppe, und klicken Sie abschließend auf die Schaltfläche *Löschen*.


## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie die Verwendung der Azure CLI (Befehlszeilenschnittstelle) und anderer Hilfsprogramme zu folgenden Zwecken gelernt:
> [!div class="checklist"]
> * Erstellen einer Azure-Datenbank für PostgreSQL-Server
> * Konfigurieren der Serverfirewall
> * Erstellen einer Datenbank mit dem Hilfsprogramm **psql**
> * Laden von Beispieldaten
> * Daten abfragen
> * Aktualisieren von Daten
> * Wiederherstellen von Daten

> [!div class="nextstepaction"]
> [Tutorial: Entwerfen einer Azure Database for PostgreSQL-Instanz mithilfe des Azure-Portals](tutorial-design-database-using-azure-portal.md)

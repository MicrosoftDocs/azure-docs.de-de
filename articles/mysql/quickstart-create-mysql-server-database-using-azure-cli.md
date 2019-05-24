---
title: 'Schnellstart: Erstellen eines Azure Database for MySQL-Servers – Azure CLI'
description: Dieser Schnellstart beschreibt die Verwendung der Azure CLI zum Erstellen einer Azure-Datenbank für MySQL-Server in einer Azure-Ressourcengruppe.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 01/09/2019
ms.custom: mvc
ms.openlocfilehash: 10acb353e282508c838bee89b131d94dcd3fa7ee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66160402"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-azure-cli"></a>Schnellstart: Erstellen einer Azure-Datenbank für MySQL-Server mithilfe der Azure CLI

> [!TIP]
> Erwägen Sie die Verwendung des vereinfachten Befehls [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) der Azure-Befehlszeilenschnittstelle (derzeit in der Vorschauphase). Probieren Sie den [Schnellstart](./quickstart-create-server-up-azure-cli.md) aus.

Dieser Schnellstart beschreibt die Verwendung der Azure CLI zum Erstellen einer Azure-Datenbank für MySQL-Server in einer Azure-Ressourcengruppe in etwa fünf Minuten. Die Azure CLI dient zum Erstellen und Verwalten von Azure-Ressourcen über die Befehlszeile oder mit Skripts.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für diesen Artikel die Azure CLI-Version 2.0 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli). 

Wenn Sie über mehrere Abonnements verfügen, wählen Sie das entsprechende Abonnement aus, in dem die Ressource vorhanden ist oder in Rechnung gestellt wird. Wählen Sie eine bestimmte Abonnement-ID unter Ihrem Konto mit dem Befehl [az account set](/cli/azure/account#az-account-set) aus.
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
Erstellen Sie mit dem Befehl [az group create](/cli/azure/group#az-group-create) eine [Azure-Ressourcengruppe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und als Gruppe verwaltet werden.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen `myresourcegroup` am Standort `westus` erstellt.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Erstellen eines Servers für Azure-Datenbank für MySQL
Erstellen Sie mit dem Befehl **[az mysql server create](/cli/azure/mysql/server#az-mysql-server-create)** einen Azure Database for MySQL-Server. Ein Server kann mehrere Datenbanken verwalten. In der Regel wird für jedes Projekt oder jeden Benutzer eine separate Datenbank verwendet.

**Einstellung** | **Beispielwert** | **Beschreibung**
---|---|---
name | mydemoserver | Wählen Sie einen eindeutigen Namen, der Ihren Server für Azure-Datenbank für MySQL identifiziert. Der Servername darf nur Kleinbuchstaben, Zahlen und den Bindestrich (-) enthalten. Es muss zwischen drei und 63 Zeichen lang sein.
resource-group | myresourcegroup | Geben Sie den Namen der Azure-Ressourcengruppe an.
sku-name | GP_Gen5_2 | Der Name der SKU. Folgt der Konvention „{Tarif}\_{Computegeneration}\_{virtuelle Kerne}“ in Kurzform. Unter dieser Tabelle finden Sie weitere Informationen zum sku-name-Parameter.
backup-retention | 7 | Gibt die Aufbewahrungsdauer für eine Sicherung an. Die Einheit ist Tage. Der Bereich umfasst 7 bis 35. 
geo-redundant-backup | Deaktiviert | Gibt an, ob georedundante Sicherungen für diesen Server aktiviert werden sollen. Zulässige Werte: „Aktiviert“, „Deaktiviert“
location | westus | Der Azure-Standort für den Server.
ssl-enforcement | Enabled | Gibt an, ob SSL für diesen Server aktiviert werden soll. Zulässige Werte: „Aktiviert“, „Deaktiviert“
storage-size | 51.200 | Die Speicherkapazität des Servers (Einheit: MB). Die kleinste gültige Speichergröße ist 5.120MB, die in Schritten von 1.024MB erhöht wird. Im Dokument [Tarife](./concepts-pricing-tiers.md) finden Sie weitere Informationen zu den Grenzwerten bei der Speichergröße. 
version | 5.7 | Die MySQL-Hauptversion.
admin-user | myadmin | Der Benutzername für die Administratoranmeldung. Dieser darf nicht **azure_superuser**, **admin**, **administrator**, **root**, **guest** oder **public** lauten.
admin-password | *sicheres Kennwort* | Das Kennwort des Administratorbenutzers. Es muss zwischen acht und 128 Zeichen lang sein. Das Kennwort muss Zeichen aus drei der folgenden Kategorien enthalten: Englische Großbuchstaben, englische Kleinbuchstaben, Zahlen und nicht alphanumerische Zeichen.


Für den Parameterwert des SKU-Namens wird die Konvention „{Tarif}\_{Computegeneration}\_{V-Kerne}“ verwendet, wie in den folgenden Beispielen:
+ `--sku-name B_Gen5_1` ist „Basic“, „Gen 5“ und „1 virtueller Kern“ zugeordnet. Bei dieser Option handelt es sich um die kleinste verfügbare SKU.
+ `--sku-name GP_Gen5_32` ist „Universell“, „Gen 5“ und „32 V-Kerne“ zugeordnet.
+ `--sku-name MO_Gen5_2` ist „Arbeitsspeicheroptimiert“, „Gen 5“ und „2 V-Kerne“ zugeordnet.

Informationen zu den gültigen Werten pro Region und Tarif finden Sie in der Dokumentation zu [Tarifen](./concepts-pricing-tiers.md).

Im folgenden Beispiel wird in der Region „USA, Westen“ die MySQL 5.7-Serverinstanz `mydemoserver` in der Ressourcengruppe `myresourcegroup` mit dem Serveradministrator-Anmeldenamen `myadmin` erstellt. Dies ist ein **Gen 4**-Server vom Typ **Universell** mit zwei **virtuellen Kernen**. Ersetzen Sie das `<server_admin_password>` durch einen eigenen Wert.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7
```

> [!NOTE]
> Ziehen Sie die Verwendung des Tarifs „Basic“ in Betracht, wenn geringe Compute- und E/A-Leistung für Ihre Workload angemessen ist. Beachten Sie, dass im Tarif „Basic“ erstellte Server später nicht auf „Universell“ oder „Arbeitsspeicheroptimiert“ skaliert werden können. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/mysql/).
> 

## <a name="configure-firewall-rule"></a>Konfigurieren der Firewallregel
Erstellen Sie mit dem Befehl **[az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create)** eine Azure Database for MySQL-Firewallregel auf Serverebene. Eine Firewallregel auf Serverebene ermöglicht einer externen Anwendung wie z.B. dem Befehlszeilentool **mysql.exe** oder MySQL Workbench, über die Firewall des Azure-MySQL-Diensts eine Verbindung zu Ihrem Server herzustellen. 

Das folgende Beispiel erstellt eine Firewallregel namens `AllowMyIP`, die Verbindungen über eine bestimmte IP-Adresse (192.168.0.1) zulässt. Fügen Sie die IP-Adresse oder den IP-Adressbereich des Orts ein, von dem Sie eine Verbindung herstellen möchten. 

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Die Kommunikation für Verbindungen mit Azure-Datenbank für MySQL erfolgt über Port 3306. Wenn Sie versuchen, eine Verbindung über ein Unternehmensnetzwerk herzustellen, wird ausgehender Datenverkehr über Port 3306 unter Umständen nicht zugelassen. In diesem Fall können Sie nur dann eine Verbindung mit Ihrem Server herstellen, wenn Ihre IT-Abteilung Port 3306 öffnet.
> 


## <a name="configure-ssl-settings"></a>Konfigurieren der SSL-Einstellungen
Standardmäßig werden SSL-Verbindungen zwischen Ihrem Server und Clientanwendungen erzwungen. Mit dieser Standardeinstellung wird die Sicherheit von Daten während des Betriebs gewährleistet, indem der Datenstrom über das Internet verschlüsselt wird. Um diesen Schnellstart zu vereinfachen, ist es ratsam, die SSL-Verbindungen für Ihren Server zu deaktivieren. Das Deaktivieren von SSL ist für Produktionsserver aber nicht zu empfehlen. Weitere Informationen finden Sie unter [Konfigurieren von SSL-Verbindungen in der Anwendung für eine sichere Verbindung mit Azure-Datenbank für MySQL](./howto-configure-ssl.md).

Im folgenden Beispiel wird das Erzwingen von SSL auf Ihrem MySQL-Server deaktiviert.
 
 ```azurecli-interactive
 az mysql server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>Abrufen der Verbindungsinformationen

Zum Herstellen einer Verbindung zum Server müssen Sie Hostinformationen und Anmeldeinformationen für den Zugriff angeben.

```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Das Ergebnis liegt im JSON-Format vor. Notieren Sie sich **fullyQualifiedDomainName** und **administratorLogin**.
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

## <a name="connect-to-the-server-using-the-mysqlexe-command-line-tool"></a>Herstellen einer Verbindung zum Server mit dem Befehlszeilentool „mysql.exe“
Herstellen einer Serververbindung mit dem Befehlszeilentool **mysql.exe** Sie können MySQL [hier](https://dev.mysql.com/downloads/) herunterladen und auf Ihrem Computer installieren. 

Geben Sie die nächsten Befehle ein: 

1. Stellen Sie eine Verbindung zum Server mit dem Befehlszeilentool **mysql** her:
   ```bash
   mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
   ```

2. Zeigen Sie den Serverstatus an:
   ```sql
   mysql> status
   ```
   Wenn alles funktioniert, sollte das Befehlszeilentool den folgenden Text ausgeben:

```dos
C:\Users\>mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
Enter password: ***********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65512
Server version: 5.6.26.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.6.35, for Win64 (x86_64)

Connection id:          65512
Current database:
Current user:           myadmin@116.230.243.143
SSL:                    Not in use
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             mydemoserver.mysql.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    gbk
Conn.  characterset:    gbk
TCP port:               3306
Uptime:                 2 days 9 hours 47 min 20 sec

Threads: 4  Questions: 34833  Slow queries: 2  Opens: 84  Flush tables: 4  Open tables: 1  Queries per second avg: 0.167
--------------

mysql>
```

> [!TIP]
> Zusätzliche Befehle finden Sie unter [MySQL 5.7-Referenzhandbuch – Kapitel 4.5.1 (in englischer Sprache)](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Herstellen einer Verbindung zum Server mit dem MySQL Workbench-GUI-Tool
1. Starten Sie die Anwendung MySQL Workbench auf dem Clientcomputer. Sie können MySQL Workbench [hier](https://dev.mysql.com/downloads/workbench/) herunterladen und installieren.

2. Geben Sie im Dialogfeld **Setup New Connection** (Neue Verbindung einrichten) auf der Registerkarte **Parameter** folgende Informationen ein:

   ![Einrichten einer neuen Verbindung](./media/quickstart-create-mysql-server-database-using-azure-cli/setup-new-connection.png)

| **Einstellung** | **Empfohlener Wert** | **Beschreibung** |
|---|---|---|
|   Verbindungsname | Eigene Verbindung | Geben Sie eine beliebige Bezeichnung für diese Verbindung ein. |
| Verbindungsmethode | Wählen Sie „Standard (TCP/IP)“ aus | Verwenden Sie das TCP/IP-Protokoll zum Herstellen einer Verbindung mit Azure-Datenbank für MySQL> |
| Hostname | mydemoserver.mysql.database.azure.com | Der zuvor notierte Servername. |
| Port | 3306 | Der Standardport für MySQL wird verwendet. |
| Username | myadmin@mydemoserver | Die zuvor notierte Serveradministratoranmeldung. |
| Kennwort | **** | Verwenden Sie das Administratorkontokennwort, das Sie zuvor konfiguriert haben. |

Klicken Sie auf **Verbindung testen**, um zu testen, ob alle Parameter richtig konfiguriert wurden.
Nun können Sie auf die Verbindung klicken, um eine Verbindung mit dem Server herzustellen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie diese Ressourcen nicht für einen anderen Schnellstart bzw. ein anderes Tutorial benötigen, können Sie sie mit dem folgenden Befehl löschen: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Wenn Sie nur den neu erstellten Server löschen möchten, können Sie den Befehl **[az mysql server delete](/cli/azure/mysql/server#az-mysql-server-delete)** ausführen.
```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Entwerfen einer MySQL-Datenbank mit der Azure CLI](./tutorial-design-database-using-cli.md)

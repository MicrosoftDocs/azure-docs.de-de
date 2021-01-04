---
title: 'Schnellstart: Erstellen eines Servers – Azure CLI – Azure Database for MySQL Flexible Server'
description: In diesem Schnellstart wird die Verwendung der Azure CLI zum Erstellen einer Azure Database for MySQL Flexible Server-Instanz in einer Azure-Ressourcengruppe beschrieben.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 65cc3d2fdcbdea934e80a5f0012ca4f3da157ca3
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843433"
---
# <a name="quickstart-create-an-azure-database-for-mysql-flexible-server-using-azure-cli"></a>Schnellstart: Erstellen einer Azure Database for MySQL Flexible Server-Instanz mit der Azure CLI

In diesem Schnellstart erfahren Sie, wie Sie mit den Befehlen der [Azure CLI](/cli/azure/get-started-with-azure-cli) in [Azure Cloud Shell](https://shell.azure.com) in fünf Minuten eine Azure Database for MySQL Flexible Server-Instanz erstellen können. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

> [!IMPORTANT] 
> Azure Database for MySQL – Flexible Server befindet sich aktuell in der öffentlichen Vorschau.

## <a name="launch-azure-cloud-shell"></a>Starten von Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel durchführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert.

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock einfach die Option **Ausprobieren**. Sie können Cloud Shell auch auf einer separaten Browserregisterkarte öffnen, indem Sie zu [https://shell.azure.com/bash](https://shell.azure.com/bash) navigieren. Wählen Sie **Kopieren** aus, um die Codeblöcke zu kopieren. Fügen Sie die Blöcke anschließend in Cloud Shell ein, und wählen Sie **Eingabe**, um sie auszuführen.

Wenn Sie es vorziehen, die CLI lokal zu installieren und zu verwenden, müssen Sie für diesen Schnellstart mindestens Version 2.0 der Azure CLI verwenden. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Voraussetzungen

Sie müssen sich mithilfe des Befehls [az login](/cli/azure/reference-index#az-login) bei Ihrem Konto anmelden. Beachten Sie die Eigenschaft **id**, die auf die **Abonnement-ID** für Ihr Azure-Konto verweist.

```azurecli-interactive
az login
```

Wählen Sie mithilfe des Befehls [az account set](/cli/azure/account#az-account-set) das Abonnement unter Ihrem Konto aus. Notieren Sie sich aus der Ausgabe von **az login** den Wert für **id**. Sie verwenden ihn im Befehl als Wert für das Argument **subscription**. Wenn Sie über mehrere Abonnements verfügen, wählen Sie das entsprechende Abonnement aus, in dem die Ressource fakturiert sein sollte. Verwenden Sie [az account list](/cli/azure/account#az-account-list), um alle Abonnements abzurufen.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Erstellen eines flexiblen Servers

Erstellen Sie mithilfe des Befehls `az group create` eine [Azure-Ressourcengruppe](../../azure-resource-manager/management/overview.md) und anschließend in dieser Ressourcengruppe Ihren flexiblen MySQL-Server. Geben Sie daher einen eindeutigen Namen an. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen `myresourcegroup` am Standort `eastus2` erstellt.

```azurecli-interactive
az group create --name myresourcegroup --location eastus2
```

Erstellen Sie mit dem Befehl `az mysql flexible-server create` einen flexiblen Server. Ein Server kann mehrere Datenbanken enthalten. Mit dem folgenden Befehl wird ein Server mit Dienststandards und -werten aus dem [lokalen Kontext](/cli/azure/local-context) Ihrer Azure CLI erstellt: 

```azurecli
az mysql flexible-server create
```

Der erstellte Server weist die folgenden Attribute auf: 
- Er hat einen automatisch generierten Servernamen, Administratorbenutzernamen, ein Administratorkennwort, einen Ressourcengruppennamen (sofern nicht bereits im lokalen Kontext angegeben), und er befindet sich am selben Speicherort wie die Ressourcengruppe. 
- Die Dienststandardwerte für verbleibende Serverkonfigurationen lauten wie folgt: Computetarif (Burstable) (Burstfähig), Computegröße/SKU (B1MS), Aufbewahrungszeitraum für Sicherungen (7 Tage) und MySQL-Version (5.7).
- Die Standardkonnektivitätsmethode ist der private Zugriff (VNET-Integration) mit einem automatisch generierten virtuellen Netzwerk und Subnetz.

> [!NOTE] 
> Die Konnektivitätsmethode kann nicht geändert werden, nachdem der Server erstellt wurde. Wenn Sie z. B. während der Erstellung *Private access (VNet Integration)* (Privater Zugriff (VNET-Integration)) ausgewählt haben, können Sie nach der Erstellung nicht zu *Public access (allowed IP addresses)* (Öffentlicher Zugriff (zulässige IP-Adressen)) wechseln. Es wird dringend empfohlen, einen Server mit privatem Zugriff zu erstellen, um mithilfe der VNET-Integration sicher auf den Server zugreifen zu können. Weitere Informationen zum privaten Zugriff finden Sie im Artikel zu [Konzepten](./concepts-networking.md).

Wenn Sie irgendwelche Standardwerte ändern möchten, finden Sie in der [Referenzdokumentation](/cli/azure/mysql/flexible-server) zur Azure CLI die komplette Liste der konfigurierbaren CLI-Parameter. 

Nachstehend ist eine Beispielausgabe aufgeführt: 

```json
Command group 'mysql flexible-server' is in preview. It may be changed/removed in a future release.
Creating Resource Group 'groupXXXXXXXXXX'...
Creating new vnet "serverXXXXXXXXXVNET" in resource group "groupXXXXXXXXXX"...
Creating new subnet "serverXXXXXXXXXSubnet" in resource group "groupXXXXXXXXXX" and delegating it to "Microsoft.DBforMySQL/flexibleServers"...
Creating MySQL Server 'serverXXXXXXXXX' in group 'groupXXXXXXXXXX'...
Your server 'serverXXXXXXXXX' is using sku 'Standard_B1ms' (Paid Tier). Please refer to https://aka.ms/mysql-pricing for pricing details
Creating MySQL database 'flexibleserverdb'...
Make a note of your password. If you forget, you would have to reset your password with 'az mysql flexible-server update -n serverXXXXXXXXX -g groupXXXXXXXXXX -p <new-password>'.
{
  "connectionString": "server=serverXXXXXXXXX.mysql.database.azure.com;database=flexibleserverdb;uid=secureusername;pwd=securepasswordstring",
  "databaseName": "flexibleserverdb",
  "host": "serverXXXXXXXXX.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.DBforMySQL/flexibleServers/serverXXXXXXXXX",
  "location": "East US 2",
  "password": "securepasswordstring",
  "resourceGroup": "groupXXXXXXXXXX",
  "skuname": "Standard_B1ms",
  "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.Network/virtualNetworks/serverXXXXXXXXXVNET/subnets/serverXXXXXXXXXSubnet",
  "username": "secureusername",
  "version": "5.7"
}
```

Wenn Sie irgendwelche Standardwerte ändern möchten, finden Sie in der [Referenzdokumentation](/cli/azure/mysql/flexible-server) zur Azure CLI die komplette Liste der konfigurierbaren CLI-Parameter. 

> [!NOTE]
> Die Kommunikation für Verbindungen mit Azure-Datenbank für MySQL erfolgt über Port 3306. Wenn Sie versuchen, eine Verbindung über ein Unternehmensnetzwerk herzustellen, wird ausgehender Datenverkehr über Port 3306 unter Umständen nicht zugelassen. In diesem Fall können Sie nur dann eine Verbindung mit Ihrem Server herstellen, wenn Ihre IT-Abteilung Port 3306 öffnet.

## <a name="get-the-connection-information"></a>Abrufen der Verbindungsinformationen

Zum Herstellen einer Verbindung zum Server müssen Sie Hostinformationen und Anmeldeinformationen für den Zugriff angeben.

```azurecli-interactive
az mysql flexible-server show --resource-group myresourcegroup --name mydemoserver
```

Das Ergebnis liegt im JSON-Format vor. Notieren Sie sich die Werte für **fullyQualifiedDomainName** und **administratorLogin**. Im Folgenden finden Sie ein Beispiel für die JSON-Ausgabe: 

```json
{
  "administratorLogin": "myadminusername",
  "administratorLoginPassword": null,
  "delegatedSubnetArguments": {
    "subnetArmResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/mydemoserverVNET/subnets/mydemoserverSubnet"
  },
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/flexibleServers/mydemoserver",
  "location": "East US 2",
  "name": "mydemoserver",
  "publicNetworkAccess": "Disabled",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 0,
    "name": "Standard_B1ms",
    "tier": "Burstable"
  },
  "storageProfile": {
    "backupRetentionDays": 7,
    "fileStorageSkuName": "Premium_LRS",
    "storageAutogrow": "Disabled",
    "storageIops": 0,
    "storageMb": 10240
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/flexibleServers",
  "version": "5.7"
}
```

## <a name="connect-using-mysql-command-line-client"></a>Herstellen einer Verbindung mithilfe des mysql-Befehlszeilenclients

Da der flexible Server mit *privatem Zugriff (VNET-Integration)* erstellt wurde, müssen Sie eine Verbindung mit Ihrem Server über eine Ressource innerhalb desselben virtuellen Netzwerks herstellen. Sie können einen virtuellen Computer erstellen und ihn dem erstellten virtuellen Netzwerk hinzufügen. 

Nachdem der virtuelle Computer erstellt wurde, können Sie eine SSH-Verbindung mit diesem herstellen und das beliebte Clienttool **[mysql.exe](https://dev.mysql.com/downloads/)** installieren.

Stellen Sie mit mysql.exe mithilfe des folgenden Befehls eine Verbindung her. Ersetzen Sie die Werte durch den tatsächlichen Servernamen und das Kennwort. 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese Ressourcen nicht für einen anderen Schnellstart bzw. ein anderes Tutorial benötigen, können Sie sie mit dem folgenden Befehl löschen:

```azurecli-interactive
az group delete --name myresourcegroup
```

Wenn Sie nur den einen neu erstellten Server löschen möchten, können Sie den Befehl `az mysql server delete` ausführen.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
>[Erstellen einer PHP-Web-App (Laravel) mit MySQL](tutorial-php-database-app.md)

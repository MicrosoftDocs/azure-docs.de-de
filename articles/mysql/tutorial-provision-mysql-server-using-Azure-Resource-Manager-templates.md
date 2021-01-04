---
title: 'Tutorial: Erstellen einer Azure Database for MySQL-Instanz – Azure Resource Manager-Vorlage'
description: In diesem Tutorial wird erläutert, wie Sie Azure Database for MySQL Server-Bereitstellungen mithilfe von Azure Resource Manager-Vorlagen vornehmen und automatisieren.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: json
ms.topic: tutorial
ms.date: 12/02/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 4727fa292a2516f3fa42578cb8ea1709951e0778
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325144"
---
# <a name="tutorial-provision-an-azure-database-for-mysql-server-using-azure-resource-manager-template"></a>Tutorial: Bereitstellen eines Azure Database for MySQL-Servers mithilfe von Azure Resource Manager-Vorlagen

Mit der [Azure Database for MySQL-REST-API](/rest/api/mysql/) können DevOps-Techniker Bereitstellung, Konfiguration und Prozesse für verwaltete MySQL-Server und -Datenbanken in Azure automatisieren und integrieren.  Die API ermöglicht Erstellung, Enumeration, Verwaltung und Löschung von MySQL-Servern und -Datenbanken im Azure Database for MySQL-Dienst.

Azure Resource Manager nutzt die zugrunde liegende REST-API, um die für bedarfsorientierte Bereitstellungen erforderlichen Azure-Ressourcen zu deklarieren und zu programmieren und sie an das Infrastructure-as-a-Code-Konzept anzugleichen. Die Vorlage parametrisiert Name, SKU, Netzwerk, Firewallkonfiguration und Einstellungen der Azure-Ressource, sodass sie einmalig erstellt und mehrfach verwendet werden kann.  Azure Resource Manager-Vorlagen können problemlos im [Azure-Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) oder über [Visual Studio Code](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=CLI) erstellt werden. Sie ermöglichen das Packen, die Standardisierung und Automatisierung der Bereitstellung für die Integration in die DevOps-CI/CD-Pipeline.  Wenn Sie beispielsweise schnell eine Web-App mit Azure Database for MySQL-Back-End bereitstellen möchten, können Sie die End-to-End-Bereitstellung mit der [Schnellstartvorlage](https://azure.microsoft.com/resources/templates/101-webapp-managed-mysql/) aus dem GitHub-Katalog vornehmen.

In diesem Tutorial verwenden Sie eine Azure Resource Manager-Vorlage und andere Hilfsprogramme, um die Vorgehensweise bei folgenden Aufgaben kennenzulernen:

> [!div class="checklist"]
> * Erstellen eines Azure Database for MySQL-Servers mit VNET-Dienstendpunkt mithilfe einer Azure Resource Manager-Vorlage
> * Verwenden des [mysql-Befehlszeilentools](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) zum Erstellen einer Datenbank
> * Laden von Beispieldaten
> * Daten abfragen
> * Aktualisieren von Daten

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="create-an-azure-database-for-mysql-server-with-vnet-service-endpoint-using-azure-resource-manager-template"></a>Erstellen eines Azure Database for MySQL-Servers mit VNET-Dienstendpunkt mithilfe einer Azure Resource Manager-Vorlage

Um die JSON-Vorlagenreferenz für einen Azure Database for MySQL-Server abzurufen, wechseln Sie zu der Vorlagenreferenz [Microsoft.DBforMySQL-Server](/azure/templates/microsoft.dbformysql/servers). Nachstehend finden Sie die JSON-Vorlage, die Sie zum Erstellen eines neuen Servers für Azure Database for MySQL mit VNet-Dienstendpunkt verwenden können.
```json
{
  "apiVersion": "2017-12-01",
  "type": "Microsoft.DBforMySQL/servers",
  "name": "string",
  "location": "string",
  "tags": "string",
  "properties": {
    "version": "string",
    "sslEnforcement": "string",
    "administratorLogin": "string",
    "administratorLoginPassword": "string",
    "storageProfile": {
      "storageMB": "string",
      "backupRetentionDays": "string",
      "geoRedundantBackup": "string"
    }
  },
  "sku": {
    "name": "string",
    "tier": "string",
    "capacity": "string",
    "family": "string"
  },
  "resources": [
    {
      "name": "AllowSubnet",
      "type": "virtualNetworkRules",
      "apiVersion": "2017-12-01",
      "properties": {
        "virtualNetworkSubnetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnetName'))]",
        "ignoreMissingVnetServiceEndpoint": true
      },
      "dependsOn": [
        "[concat('Microsoft.DBforMySQL/servers/', parameters('serverName'))]"
      ]
    }
  ]
}
```
In dieser Anforderung müssen die folgenden Werte angepasst werden:
+   `name`: Geben Sie den Namen für Ihren MySQL-Server (ohne Domänennamen) ein.
+   `location`: Geben Sie eine gültige Azure-Rechenzentrumsregion für Ihren MySQL-Server an, zum Beispiel „westus2“.
+   `properties/version`: Geben Sie die bereitzustellende MySQL-Serverversion an, zum Beispiel 5.6 oder 5.7.
+   `properties/administratorLogin`: Geben Sie die MySQL-Administratoranmeldung für den Server ein. Als Administratoranmeldename nicht zulässig sind „azure_superuser“, „admin“, „administrator“, „root“, „guest“ oder „public“.
+   `properties/administratorLoginPassword`: Geben Sie das Kennwort für den oben angegebenen MySQL-Administratorbenutzer ein.
+   `properties/sslEnforcement`: Geben Sie „Enabled“/„Disabled“ an, um sslEnforcement zu aktivieren bzw.zu deaktivieren.
+   `storageProfile/storageMB`: Geben Sie die maximal bereitgestellte Speichergröße in MB an, die für den Server erforderlich ist, zum Beispiel 5120.
+   `storageProfile/backupRetentionDays`: Geben Sie den gewünschten Aufbewahrungszeitraum für die Sicherung in Tagen an, zum Beispiel 7. 
+   `storageProfile/geoRedundantBackup`: Geben Sie je nach Anforderungen an die georedundante Notfallwiederherstellung „Enabled“ oder “Disabled“ an.
+   `sku/tier`: Geben Sie den Tarif „Basic“, „GeneralPurpose (Allgemein)“ oder „MemoryOptimized (Speicheroptimiert)“ für die Bereitstellung an.
+   `sku/capacity`: Geben Sie die Kapazität des virtuellen Kerns an. Folgende Werte sind möglich: 2, 4, 8, 16, 32 oder 64.
+   `sku/family`: Geben Sie Gen5 als ausgewählte Hardwaregeneration für die Serverbereitstellung an.
+   `sku/name`: Geben Sie „TierPrefix_family_capacity“ an, z. B. „B_Gen5_1“, „GP_Gen5_16“ oder „MO_Gen5_32“. In der Dokumentation zu den [Tarifen](./concepts-pricing-tiers.md) finden Sie Informationen zu den gültigen Werten pro Region und Tarif.
+   `resources/properties/virtualNetworkSubnetId`: Geben Sie die Azure-ID des Subnetzes im VNet an, in das der Azure MySQL-Server platziert werden soll. 
+   `tags(optional)`: Geben Sie optionale Tags als Schlüsselwertpaare an, die zum Kategorisieren der Ressourcen für die Abrechnung usw. verwendet werden sollen.

Wenn Sie eine Azure Resource Manager-Vorlage zum Automatisieren von Azure Database for MySQL-Bereitstellungen für Ihre Organisation erstellen möchten, ist es empfehlenswert, mit der [Azure Resource Manager-Beispielvorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) unter „azure-quickstart-templates“ im GitHub-Katalog zu beginnen und darauf aufzubauen. 

Wenn Sie noch nicht mit Azure Resource Manager-Vorlagen vertraut sind und diese ausprobieren möchten, können Sie mit folgenden Schritten beginnen:
+   Kopieren Sie die [Azure Resource Manager-Beispielvorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) aus dem Katalog „azure-quickstart-templates“, oder laden Sie sie herunter.  
+   Ändern Sie die Datei „azuredeploy.parameters.json“, um die Parameterwerte entsprechend Ihren Anforderungen zu aktualisieren, und speichern Sie die Datei. 
+   Verwenden Sie die Azure-Befehlszeilenschnittstelle (CLI), um den Azure MySQL-Server mit den folgenden Befehlen zu erstellen.

Sie können Azure Cloud Shell im Browser verwenden oder die Azure CLI auf Ihrem Computer installieren, um die Codeblöcke in diesem Tutorial auszuführen.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

```azurecli-interactive
az login
az group create -n ExampleResourceGroup  -l "West US2"
az group deployment create -g $ ExampleResourceGroup   --template-file $ {templateloc} --parameters $ {parametersloc}
```

## <a name="get-the-connection-information"></a>Abrufen der Verbindungsinformationen
Zum Herstellen einer Verbindung zum Server müssen Sie Hostinformationen und Anmeldeinformationen für den Zugriff angeben.
```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Das Ergebnis liegt im JSON-Format vor. Notieren Sie sich die Werte für **fullyQualifiedDomainName** und **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus2",
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

## <a name="connect-to-the-server-using-mysql"></a>Herstellen einer Verbindung mit dem Server unter Verwendung von mysql
Verwenden Sie das [mysql-Befehlszeilentool](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) zum Herstellen einer Verbindung mit Ihrem Azure Database for MySQL-Server. In diesem Beispiel lautet der Befehl:
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Leere Datenbank erstellen
Sobald Sie mit dem Server verbunden sind, erstellen Sie eine leere Datenbank.
```sql
mysql> CREATE DATABASE mysampledb;
```

Führen Sie an der Eingabeaufforderung den folgenden Befehl zum Wechseln der Verbindung zu dieser neu erstellten Datenbank aus:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Erstellen von Tabellen in der Datenbank
Da Sie nun wissen, wie Sie eine Verbindung mit einer Azure Database for MySQL-Datenbank herstellen, können Sie einige grundlegende Aufgaben ausführen.

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

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe. Dadurch werden die Ressourcen in der Ressourcengruppe gelöscht.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **Ressourcengruppen**, und wählen Sie die entsprechende Option aus.

2. Wählen Sie in der Liste der Ressourcengruppen den Namen Ihrer Ressourcengruppe aus.

3. Wählen Sie auf der Seite **Übersicht** der Ressourcengruppe die Option **Ressourcengruppe löschen** aus.

4. Geben Sie im Bestätigungsdialogfeld den Namen Ihrer Ressourcengruppe ein, und wählen Sie **Löschen** aus.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie Folgendes gelernt:
> [!div class="checklist"]
> * Erstellen eines Azure Database for MySQL-Servers mit VNET-Dienstendpunkt mithilfe einer Azure Resource Manager-Vorlage
> * Verwenden des MySQL-Befehlszeilentools zum Erstellen einer Datenbank
> * Laden von Beispieldaten
> * Daten abfragen
> * Aktualisieren von Daten

> [!div class="nextstepaction"]
> [Herstellen einer Verbindung zwischen Anwendungen und Azure-Datenbank für MySQL](./howto-connection-string.md)
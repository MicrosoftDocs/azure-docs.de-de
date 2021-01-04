---
title: 'Schnellstart: Erstellen eines Servers – Azure CLI – Azure Database for PostgreSQL – Flexibler Server'
description: In diesem Schnellstart wird die Verwendung der Azure CLI zum Erstellen einer Azure Database for PostgreSQL Flexible Server-Instanz in einer Azure-Ressourcengruppe beschrieben.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d43fab43b05ccfd88308390f58ba48ff9111b295
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842820"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-flexible-server-using-azure-cli"></a>Schnellstart: Erstellen einer Azure Database for PostgreSQL Flexible Server-Instanz mit der Azure CLI

In diesem Schnellstart erfahren Sie, wie Sie mit den Befehlen der [Azure CLI](/cli/azure/get-started-with-azure-cli) in [Azure Cloud Shell](https://shell.azure.com) in fünf Minuten eine Azure Database for PostgreSQL Flexible Server-Instanz erstellen können. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

> [!IMPORTANT] 
> Azure Database for PostgreSQL Flexible Server befindet sich aktuell in der Vorschau.

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

Erstellen Sie mithilfe des Befehls `az group create` eine [Azure-Ressourcengruppe](../../azure-resource-manager/management/overview.md) und anschließend in dieser Ressourcengruppe Ihren flexiblen PostgreSQL-Server. Geben Sie daher einen eindeutigen Namen an. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen `myresourcegroup` am Standort `westus` erstellt.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Erstellen Sie mit dem Befehl `az postgres flexible-server create` einen flexiblen Server. Ein Server kann mehrere Datenbanken enthalten. Mit dem folgenden Befehl wird ein Server mit Dienststandards und -werten aus dem [lokalen Kontext](/cli/azure/local-context) Ihrer Azure CLI erstellt: 

```azurecli
az postgres flexible-server create
```

Der erstellte Server weist die folgenden Attribute auf: 
- Er hat einen automatisch generierten Servernamen, Administratorbenutzernamen, ein Administratorkennwort, einen Ressourcengruppennamen (sofern nicht bereits im lokalen Kontext angegeben), und er befindet sich am selben Speicherort wie die Ressourcengruppe. 
- Die Dienststandardwerte für verbleibende Serverkonfigurationen lauten wie folgt: Computetarif (Universell), Computegröße/SKU (D2s_v3, zwei virtuelle Kerne, 8 GB RAM), Aufbewahrungszeitraum für Sicherungen (sieben Tage) und PostgreSQL-Version (12).
- Die Standardkonnektivitätsmethode ist der private Zugriff (VNET-Integration) mit einem automatisch generierten virtuellen Netzwerk und Subnetz.

> [!NOTE] 
> Die Konnektivitätsmethode kann nicht geändert werden, nachdem der Server erstellt wurde. Wenn Sie z. B. während der Erstellung *Private access (VNet Integration)* (Privater Zugriff (VNET-Integration)) ausgewählt haben, können Sie nach der Erstellung nicht zu *Public access (allowed IP addresses)* (Öffentlicher Zugriff (zulässige IP-Adressen)) wechseln. Es wird dringend empfohlen, einen Server mit privatem Zugriff zu erstellen, um mithilfe der VNET-Integration sicher auf den Server zugreifen zu können. Weitere Informationen zum privaten Zugriff finden Sie im Artikel zu [Konzepten](./concepts-networking.md).

Wenn Sie die Standardwerte ändern möchten, finden Sie in der Referenzdokumentation zur Azure CLI <!--FIXME --> die komplette Liste der konfigurierbaren CLI-Parameter. 

> [!NOTE]
> Die Kommunikation für Verbindungen mit Azure Database for PostgreSQL erfolgt über Port 5432. Wenn Sie versuchen, eine Verbindung über ein Unternehmensnetzwerk herzustellen, wird ausgehender Datenverkehr über Port 5432 unter Umständen nicht zugelassen. In diesem Fall können Sie nur dann eine Verbindung mit Ihrem Server herstellen, wenn Ihre IT-Abteilung Port 5432 öffnet.

## <a name="get-the-connection-information"></a>Abrufen der Verbindungsinformationen

Zum Herstellen einer Verbindung zum Server müssen Sie Hostinformationen und Anmeldeinformationen für den Zugriff angeben.

```azurecli-interactive
az postgres flexible-server show --resource-group myresourcegroup --name mydemoserver
```

Das Ergebnis liegt im JSON-Format vor. Notieren Sie sich die Werte für **fullyQualifiedDomainName** und **administratorLogin**.

<!--FIXME-->
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/flexibleServers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "name": "Standard_D2s_v3",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "publicAccess": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 131072
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/flexibleServers",
  "userVisibleState": "Ready",
  "version": "12"
}
```

## <a name="connect-using-postgresql-command-line-client"></a>Herstellen einer Verbindung über den PostgreSQL-Befehlszeilenclient

Da der flexible Server mit *privatem Zugriff (VNET-Integration)* erstellt wurde, müssen Sie eine Verbindung mit Ihrem Server über eine Ressource innerhalb desselben virtuellen Netzwerks herstellen. Sie können einen virtuellen Computer erstellen und ihn dem erstellten virtuellen Netzwerk hinzufügen. 

Nachdem der virtuelle Computer erstellt wurde, können Sie eine SSH-Verbindung mit diesem herstellen und das **[psql](https://www.postgresql.org/download/)** -Befehlszeilentool installieren.

Stellen Sie mithilfe von psql eine Verbindung mit dem folgenden Befehl her. Ersetzen Sie die Werte durch den tatsächlichen Servernamen und das Kennwort. 

```bash
psql -h mydemoserver.postgres.database.azure.com -u mydemouser -p
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese Ressourcen nicht für einen anderen Schnellstart bzw. ein anderes Tutorial benötigen, können Sie sie mit dem folgenden Befehl löschen:

```azurecli-interactive
az group delete --name myresourcegroup
```

Wenn Sie nur den einen neu erstellten Server löschen möchten, können Sie den Befehl `az postgres flexible-server delete` ausführen.

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
>[Bereitstellen einer Django-Web-App mit PostgreSQL in Azure App Service](tutorial-django-app-service-postgres.md)

---
title: 'Tutorial: Erstellen einer Instanz von Azure Database for PostgreSQL Flexible Server (Vorschau) und Azure App Service-Web-App im gleichen virtuellen Netzwerk'
description: Schnellstartanleitung zum Erstellen einer Instanz von Azure Database for PostgreSQL Flexible Server mit Web-App in einem virtuellen Netzwerk
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 09/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: ab606e357bd911f4d7f266977bd14871f92744a0
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546567"
---
# <a name="tutorial-create-an-azure-database-for-postgresql---flexible-server-with-app-services-web-app-in-virtual-network"></a>Tutorial: Erstellen einer Instanz von Azure Database for PostgreSQL Flexible Server mit App Services-Web-App im virtuellen Netzwerk

> [!IMPORTANT]
> Azure Database for PostgreSQL Flexible Server befindet sich in der Vorschau.

In diesem Tutorial wird gezeigt, wie Sie eine Azure App Service-Web-App mit Azure Database for PostgreSQL Flexible Server (Vorschau) in einem [virtuellen Netzwerk](../../virtual-network/virtual-networks-overview.md) erstellen.

In diesem Tutorial lernen Sie Folgendes:
>[!div class="checklist"]
> * Erstellen einer PostgreSQL Flexible Server-Instanz in einem virtuellen Netzwerk
> * Erstellen einer Web-App
> * Hinzufügen der Web-App zum virtuellen Netzwerk
> * Herstellen einer Verbindung mit Postgres aus der Web-App 

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

Für den Artikel müssen Sie mindestens Version 2.0 der Azure-Befehlszeilenschnittstelle lokal ausführen. Führen Sie den Befehl `az --version` aus, um die installierte Version anzuzeigen. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

Sie müssen sich mithilfe des Befehls [az login](/cli/azure/authenticate-azure-cli) bei Ihrem Konto anmelden. Beachten Sie die Eigenschaft **id** aus der Befehlsausgabe für den entsprechenden Abonnementnamen.

```azurecli
az login
```

Wenn Sie über mehrere Abonnements verfügen, wählen Sie das entsprechende Abonnement aus, in dem die Ressource fakturiert sein sollte. Wählen Sie mithilfe des Befehls [az account set](/cli/azure/account) die Abonnement-ID unter Ihrem Konto aus. Ersetzen Sie den Platzhalter für die Abonnement-ID durch die **subscription ID** -Eigenschaft der Ausgabe von **az login** für Ihr Abonnement.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>Erstellen einer PostgreSQL Flexible Server-Instanz in einem virtuellen Netzwerk

Erstellen Sie einen privaten flexiblen Server innerhalb eines virtuellen Netzwerks (VNET) mit dem folgenden Befehl:
```azurecli
az postgres flexible-server create --resource-group myresourcegroup --location westus2
```
Durch diesen Befehl werden folgende Aktionen ausgeführt, was einige Minuten dauern kann:

- Erstellen Sie die Ressourcengruppe, wenn sie noch nicht vorhanden ist.
- Generiert einen Servernamen, wenn er nicht angegeben ist.
- Erstellen Sie ein neues virtuelles Netzwerk für Ihre neue PostgreSQL-Serverinstanz. Notieren Sie sich den Namen des virtuellen Netzwerks und des Subnetzes, die für Ihre Serverinstanz erstellt wurden, da Sie die Web-App dem gleichen virtuellen Netzwerk hinzufügen müssen.
- Erstellt einen Administratorbenutzernamen und ein Kennwort für Ihren Server, sofern nicht angegeben.
- Erstellt eine leere Datenbank mit dem Namen **postgres** .

> [!NOTE]
> - Notieren Sie sich das Kennwort, das für Sie generiert wird, wenn es nicht angegeben wird. Wenn Sie das Kennwort vergessen haben, müssen Sie das Kennwort mit dem ``` az postgres flexible-server update```-Befehl zurücksetzen.
> - Wenn Sie die App Service-Umgebung nicht verwenden, müssen Sie den Zugriff von allen Azure-IPs mithilfe dieses Befehls ermöglichen. 
>  ```azurecli
>  az postgres flexible-server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
>  ```


## <a name="create-a-web-app"></a>Erstellen einer Web-App
In diesem Abschnitt erstellen Sie einen App-Host in der App Service-App, verbinden die App mit der Postgres-Datenbank und stellen anschließend Ihren Code auf dem Host bereit. Vergewissern Sie sich im Terminal, dass Sie sich im Repositorystamm Ihres Anwendungscodes befinden.

Erstellen Sie mithilfe des Befehls „az webapp up“ eine App Service-App (den Hostprozess):

```azurecli
az webapp up --resource-group myresourcegroup --location westus2 --plan testappserviceplan --sku B1 --name mywebapp
```

> [!NOTE]
> - Verwenden Sie für das Argument „--location“ den gleichen Standort wie für die Datenbank im vorherigen Abschnitt.
> - Ersetzen Sie <app-name> in ganz Azure durch einen eindeutigen Namen (der Serverendpunkt ist https://\<app-name>.azurewebsites.net). Gültige Zeichen für <app-name> sind A-Z, 0-9 und der Bindestrich (-). Ein bewährtes Muster ist eine Kombination aus Ihrem Firmennamen und einer App-ID.

Durch diesen Befehl werden folgende Aktionen ausgeführt, was einige Minuten dauern kann:

- Erstellen Sie die Ressourcengruppe, wenn sie noch nicht vorhanden ist. Verwenden Sie in diesem Befehl die gleiche Ressourcengruppe, in der Sie zuvor schon die Datenbank erstellt haben.
- Erstellen Sie den App Service-Plan ```testappserviceplan``` im Basic-Tarif (B1), falls er nicht vorhanden ist. „--plan“ und „--sku“ sind optional.
- Erstellen der App Service-App (sofern noch nicht vorhanden)
- Aktivieren der Standardprotokollierung für die App (sofern noch nicht aktiviert)
- Hochladen des Repositorys per ZIP-Bereitstellung mit aktivierter Buildautomatisierung

## <a name="add-the-web-app-to-the-virtual-network"></a>Hinzufügen der Web-App zum virtuellen Netzwerk
Verwenden Sie den **az webapp vnet-integration** -Befehl, um eine regionale Integration eines virtuellen Netzwerks einer Web-App hinzuzufügen. Ersetzen Sie <vnet-name> und <subnet-name durch den Namen des virtuellen Netzwerks und des Subnetzes, die der flexible Server verwendet.

```azurecli
az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet <vnet-name> --subnet <subnet-name>
```

## <a name="configure-environment-variables-to-connect-the-database"></a>Konfigurieren der Umgebungsvariablen für die Datenbankverbindung
Nachdem der Code nun in App Service bereitgestellt ist, muss als Nächstes eine Verbindung zwischen der App und dem flexiblen Server in Azure hergestellt werden. Vom App-Code werden Datenbankinformationen in einer Reihe von Umgebungsvariablen erwartet. Mithilfe des „```az webapp config appsettings``` set“-Befehls werden „App-Einstellungen“ erstellt, um Umgebungsvariablen in App Service festzulegen.

```azurecli
az webapp config appsettings set --settings DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>"
```


- Ersetzen Sie ```postgres-server-name```, ```username```, ```password``` für den neu erstellten „Flexibler Server“-Befehl.
- Ersetzen Sie <username> und <password> durch die Anmeldeinformationen, die durch den Befehl generiert wurden.
- Die Ressourcengruppe und der Name der App werden aus den zwischengespeicherten Werten in der Datei .azure/config abgerufen.
- Der Befehl erstellt Einstellungen namens ```DBHOST```, ```DBNAME```, ```DBUSER``` und ```DBPASS```. Wenn Ihr Anwendungscode einen anderen Namen für die Datenbankinformationen verwendet, verwenden Sie diese Namen für die App-Einstellungen, wie im Code erwähnt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Bereinigen Sie alle im Tutorial erstellten Ressourcen mit dem folgenden Befehl. Mit diesem Befehl werden alle Ressourcen in dieser Ressourcengruppe gelöscht.

```azurecli
az group delete -n myresourcegroup
```


## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Zuordnen eines vorhandenen benutzerdefinierten DNS-Namens zu Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md)
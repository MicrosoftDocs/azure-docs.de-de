---
title: 'Tutorial: Erstellen einer Instanz von Azure Database for MySQL Flexible Server (Vorschau) und Azure App Service-Web-App im gleichen virtuellen Netzwerk'
description: Schnellstartanleitung zum Erstellen einer Instanz von Azure Database for MySQL Flexible Server (Vorschau) mit Web-App in einem virtuellen Netzwerk
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a7b673dc8dfeb2ebf86aec5b7449df91c2ffd635
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92534055"
---
# <a name="tutorial-create-an-azure-database-for-mysql---flexible-server-preview-with-app-services-web-app-in-virtual-network"></a>Tutorial: Erstellen einer Instanz von Azure Database for MySQL Flexible Server (Vorschau) mit App Services-Web-App im virtuellen Netzwerk

> [!IMPORTANT]
> Azure Database for MySQL Flexible Server befindet sich aktuell in der öffentlichen Vorschau.

In diesem Tutorial wird gezeigt, wie Sie eine Azure App Service-Web-App mit MySQL Flexible Server (Vorschau) in einem [virtuellen Netzwerk](../../virtual-network/virtual-networks-overview.md) erstellen.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

Für den Artikel müssen Sie mindestens Version 2.0 der Azure-Befehlszeilenschnittstelle lokal ausführen. Führen Sie den Befehl `az --version` aus, um die installierte Version anzuzeigen. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

Sie müssen sich mithilfe des Befehls [az login](/cli/azure/reference-index#az-login) bei Ihrem Konto anmelden. Beachten Sie die Eigenschaft **id** aus der Befehlsausgabe für den entsprechenden Abonnementnamen.

```azurecli
az login
```

Wenn Sie über mehrere Abonnements verfügen, wählen Sie das entsprechende Abonnement aus, in dem die Ressource fakturiert sein sollte. Wählen Sie mithilfe des Befehls [az account set](/cli/azure/account) die Abonnement-ID unter Ihrem Konto aus. Ersetzen Sie den Platzhalter für die Abonnement-ID durch die **subscription ID** -Eigenschaft der Ausgabe von **az login** für Ihr Abonnement.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Erstellen einer Instanz von Azure Database for MySQL Flexible Server

Erstellen Sie einen privaten flexiblen Server innerhalb eines virtuellen Netzwerks (VNET) mit dem folgenden Befehl:
```azurecli
az mysql flexible-server create --resource-group myresourcegroup --location westus2
```
Durch diesen Befehl werden folgende Aktionen ausgeführt, was einige Minuten dauern kann:

- Erstellen Sie die Ressourcengruppe, wenn sie noch nicht vorhanden ist.
- Generiert einen Servernamen, wenn er nicht angegeben ist.
- Erstellen Sie ein neues virtuelles Netzwerk für Ihre neue MySQL-Serverinstanz. Notieren Sie sich den Namen des virtuellen Netzwerks und des Subnetzes, die für Ihre Serverinstanz erstellt wurden, da Sie die Web-App dem gleichen virtuellen Netzwerk hinzufügen müssen.
- Erstellt einen Administratorbenutzernamen und ein Kennwort für Ihren Server, sofern nicht angegeben.
- Erstellt eine leere Datenbank mit dem Namen **flexibleserverdb** .

> [!NOTE]
> Notieren Sie sich das Kennwort, das für Sie generiert wird, wenn es nicht angegeben wird. Wenn Sie das Kennwort vergessen haben, müssen Sie das Kennwort mit dem ``` az mysql flexible-server update```-Befehl zurücksetzen.

## <a name="create-a-web-app"></a>Erstellen einer Web-App

In diesem Abschnitt erstellen Sie einen App-Host in der App Service-App und stellen eine Verbindung dieser APP mit der MySQL-Datenbank her. Vergewissern Sie sich im Terminal, dass Sie sich im Repositorystamm Ihres Anwendungscodes befinden.

Erstellen Sie mithilfe des Befehls „az webapp up“ eine App Service-App (den Hostprozess):

```azurecli
az webapp up --resource-group myresourcegroup --location westus2 --plan testappserviceplan --sku B1 --name mywebapp
```

> [!NOTE]
> - Verwenden Sie für das Argument „--location“ den gleichen Standort wie für die Datenbank im vorherigen Abschnitt.
> - Ersetzen Sie _&lt;app-name>_ in ganz Azure durch einen eindeutigen Namen (der Serverendpunkt ist https://\<app-name>.azurewebsites.net). Gültige Zeichen für <app-name> sind A-Z, 0-9 und der Bindestrich (-). Ein bewährtes Muster ist eine Kombination aus Ihrem Firmennamen und einer App-ID.

Durch diesen Befehl werden folgende Aktionen ausgeführt, was einige Minuten dauern kann:

- Erstellen Sie die Ressourcengruppe, wenn sie noch nicht vorhanden ist. Verwenden Sie in diesem Befehl die gleiche Ressourcengruppe, in der Sie zuvor schon die Datenbank erstellt haben.
- Erstellen Sie den App Service-Plan ```testappserviceplan``` im Basic-Tarif (B1), falls er nicht vorhanden ist. „--plan“ und „--sku“ sind optional.
- Erstellen der App Service-App (sofern noch nicht vorhanden)
- Aktivieren der Standardprotokollierung für die App (sofern noch nicht aktiviert)
- Hochladen des Repositorys per ZIP-Bereitstellung mit aktivierter Buildautomatisierung

## <a name="add-the-web-app-to-the-virtual-network"></a>Hinzufügen der Web-App zum virtuellen Netzwerk

Verwenden Sie den **az webapp vnet-integration** -Befehl, um eine regionale Integration eines virtuellen Netzwerks einer Web-App hinzuzufügen. Ersetzen Sie _&lt;vnet-name>_ und _&lt;subnet-name_ durch den Namen des virtuellen Netzwerks und des Subnetzes, die der flexible Server verwendet.

```azurecli
az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet <vnet-name> --subnet <subnet-name>
```

## <a name="configure-environment-variables-to-connect-the-database"></a>Konfigurieren der Umgebungsvariablen für die Datenbankverbindung

Nachdem der Code nun in App Service bereitgestellt ist, muss als Nächstes eine Verbindung zwischen der App und dem flexiblen Server in Azure hergestellt werden. Vom App-Code werden Datenbankinformationen in einer Reihe von Umgebungsvariablen erwartet. Mithilfe des „```az webapp config appsettings``` set“-Befehls werden „App-Einstellungen“ erstellt, um Umgebungsvariablen in App Service festzulegen.

```azurecli
az webapp config appsettings set --settings DBHOST="<mysql-server-name>.mysql.database.azure.com" DBNAME="flexibleserverdb" DBUSER="<username>" DBPASS="<password>"
```

- Ersetzen Sie _&lt;mysql-server-name>_ , _&lt;username>_ und _&lt;password>_ für den neu erstellten „Flexibler Server“-Befehl.
- Ersetzen Sie _&lt;username>_ und _&lt;password>_ durch die Anmeldeinformationen, die durch den Befehl auch für Sie generiert wurden.
- Die Ressourcengruppe und der Name der App werden aus den zwischengespeicherten Werten in der Datei .azure/config abgerufen.
- Der Befehl erstellt Einstellungen namens DBHOST, DBNAME, DBUSER und DBPASS. Wenn Ihr Anwendungscode einen anderen Namen für die Datenbankinformationen verwendet, verwenden Sie diese Namen für die App-Einstellungen, wie im Code erwähnt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Bereinigen Sie alle im Tutorial erstellten Ressourcen mit dem folgenden Befehl. Mit diesem Befehl werden alle Ressourcen in dieser Ressourcengruppe gelöscht.

```azurecli
az group delete -n myresourcegroup
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Zuordnen eines vorhandenen benutzerdefinierten DNS-Namens zu Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md)
---
title: 'Tutorial: Erstellen einer App mit mehreren Containern'
description: Hier erfahren Sie, wie Sie eine App mit mehreren Containern in Azure App Service erstellen, die eine WordPress-App und einen MySQL-Container enthält, und wie Sie die WordPress-App konfigurieren.
keywords: Azure App Service, Web-App, Linux, Docker, Compose, mehrere Container, Web-App für Container, Container, Wordpress, Azure Database for MySQL, Produktionsdatenbank mit Containern
author: msangapu-msft
ms.topic: tutorial
ms.date: 10/31/2020
ms.author: msangapu
ms.custom: cli-validate, devx-track-azurecli
ms.openlocfilehash: e73d681cadaddd32290cec40ae7a9c8f6e8ac758
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97005646"
---
# <a name="tutorial-create-a-multi-container-preview-app-in-web-app-for-containers"></a>Tutorial: Erstellen einer App mit mehreren Containern (Vorschauversion) über Web-App für Container

> [!NOTE]
> Apps mit mehreren Containern befinden sich in der Vorschauphase.

[Web-App für Container](overview.md#app-service-on-linux) umfasst eine flexible Möglichkeit zum Verwenden von Docker-Images. In diesem Tutorial wird beschrieben, wie Sie eine App mit mehreren Containern per WordPress und MySQL erstellen. Sie arbeiten dieses Tutorial in Cloud Shell durch, aber Sie können diese Befehle auch lokal mit dem [Azure CLI](/cli/azure/install-azure-cli)-Befehlszeilentool (2.0.32 oder höher) ausführen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Konvertieren einer Docker Compose-Konfiguration für Web-App für Container
> * Bereitstellen einer App mit mehreren Containern in Azure
> * Hinzufügen von Anwendungseinstellungen
> * Verwenden von beständigem Speicher für Ihre Container
> * Herstellen einer Verbindung mit Azure-Datenbank für MySQL
> * Beheben von Fehlern

[!INCLUDE [Free trial note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Zum Durcharbeiten dieses Tutorials benötigen Sie Erfahrung mit [Docker Compose](https://docs.docker.com/compose/).

## <a name="download-the-sample"></a>Herunterladen des Beispiels

In diesem Tutorial wird die Compose-Datei aus [Docker](https://docs.docker.com/compose/wordpress/#define-the-project) verwendet. Sie wird jedoch geändert, um Azure Database for MySQL, beständigen Speicher und Redis einzubinden. Die Konfigurationsdatei finden Sie in den [Beispielen für Azure](https://github.com/Azure-Samples/multicontainerwordpress). Unterstützte Konfigurationsoptionen finden Sie unter [Docker Compose-Optionen](configure-custom-container.md#docker-compose-options).

[!code-yml[Main](../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

Erstellen Sie in Cloud Shell ein Tutorialverzeichnis, und navigieren Sie dann in dieses Verzeichnis.

```bash
mkdir tutorial

cd tutorial
```

Führen Sie als Nächstes den folgenden Befehl aus, um das Beispiel-App-Repository in Ihrem Tutorialverzeichnis zu klonen. Wechseln Sie anschließend in das Verzeichnis `multicontainerwordpress`.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

Erstellen Sie in Cloud Shell mit dem Befehl [`az group create`](/cli/azure/group#az-group-create) eine Ressourcengruppe. Im folgenden Beispiel wird am Standort *South Central US* (USA, Süden-Mitte) die Ressourcengruppe *myResourceGroup* erstellt. Wenn Sie alle unterstützten Standorte für App Service unter Linux im **Standard**-Tarif anzeigen möchten, führen Sie den Befehl [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice#az-appservice-list-locations) aus.

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Im Allgemeinen erstellen Sie Ressourcengruppen und Ressourcen in einer Region in Ihrer Nähe.

Nach Ausführung dieses Befehls werden die Ressourcengruppeneigenschaften in einer JSON-Ausgabe angezeigt.

## <a name="create-an-azure-app-service-plan"></a>Erstellen eines Azure App Service-Plans

Erstellen Sie in Cloud Shell mit dem Befehl [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) einen App Service-Plan in der Ressourcengruppe.

<!-- [!INCLUDE [app-service-plan](app-service-plan-linux.md)] -->

Im folgenden Beispiel wird ein App Service-Plan mit dem Namen `myAppServicePlan` in einem Linux-Container (`--is-linux`) für den Tarif **Standard** (`--sku S1`) erstellt.

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Nach der Erstellung des App Service-Plans zeigt Cloud Shell Informationen wie im folgenden Beispiel an:

<pre>
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
</pre>

### <a name="docker-compose-with-wordpress-and-mysql-containers"></a>Docker Compose mit WordPress und MySQL-Containern

## <a name="create-a-docker-compose-app"></a>Erstellen einer Docker Compose-App

Erstellen Sie in Cloud Shell eine [Web-App](overview.md) mit mehreren Containern im App Service-Plan `myAppServicePlan`, indem Sie den Befehl [az webapp create](/cli/azure/webapp#az-webapp-create) verwenden. Vergessen Sie nicht, _\<app-name>_ durch einen eindeutigen App-Namen zu ersetzen.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Nach Erstellung der Web-App zeigt Cloud Shell eine Ausgabe wie im folgenden Beispiel an:

<pre>
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

### <a name="browse-to-the-app"></a>Navigieren zur App

Navigieren Sie zur bereitgestellten App unter `http://<app-name>.azurewebsites.net`. Das Laden der App kann einige Minuten dauern. Wenn Sie einen Fehler erhalten, sollten Sie einige Minuten warten und dann den Browser aktualisieren. Falls Probleme auftreten und Sie die Problembehandlung durchführen möchten, helfen Ihnen die [Containerprotokolle](#find-docker-container-logs) weiter.

![Beispiel-App mit mehreren Containern in Web-App für Container][1]

**Glückwunsch!** Sie haben in Web-App für Container eine App mit mehreren Containern erstellt. Als Nächstes konfigurieren Sie Ihre App für die Verwendung mit Azure Database for MySQL. Warten Sie noch mit der Installation von WordPress.

## <a name="connect-to-production-database"></a>Herstellen einer Verbindung mit der Produktionsdatenbank

Es wird nicht empfohlen, Datenbankcontainer in einer Produktionsumgebung zu verwenden. Die lokalen Container sind nicht skalierbar. Stattdessen nutzen Sie Azure Database for MySQL, da dieser Dienst skaliert werden kann.

### <a name="create-an-azure-database-for-mysql-server"></a>Erstellen eines Servers für Azure-Datenbank für MySQL

Erstellen Sie mit dem Befehl [`az mysql server create`](/cli/azure/mysql/server#az-mysql-server-create) einen Azure Database for MySQL-Server.

Ersetzen Sie im folgenden Befehl den Platzhalter _&lt;mysql-server-name>_ durch Ihren MySQL-Servernamen (gültige Zeichen sind `a-z`, `0-9` und `-`). Dieser Name ist Teil des Hostnamens des MySQL-Servers (`<mysql-server-name>.database.windows.net`) und muss global eindeutig sein.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql-server-name>  --location "South Central US" --admin-user adminuser --admin-password My5up3rStr0ngPaSw0rd! --sku-name B_Gen5_1 --version 5.7
```

Das Erstellen des Servers kann einige Minuten dauern. Nach dem Erstellen des MySQL-Servers zeigt Cloud Shell Informationen wie im folgenden Beispiel an:

<pre>
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;mysql-server-name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/&lt;mysql-server-name&gt;",
  "location": "southcentralus",
  "name": "&lt;mysql-server-name&gt;",
  "resourceGroup": "myResourceGroup",
  ...
}
</pre>

### <a name="configure-server-firewall"></a>Konfigurieren der Serverfirewall

Erstellen Sie mit dem Befehl [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create) eine Firewallregel für Ihren MySQL-Server, um Clientverbindungen zuzulassen. Wenn sowohl Start- als auch End-IP auf 0.0.0.0 festgelegt ist, wird die Firewall nur für andere Azure-Ressourcen geöffnet.

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Sie können Ihre Firewallregel auch noch restriktiver gestalten und [nur die ausgehenden IP-Adressen verwenden, die Ihre App verwendet](overview-inbound-outbound-ips.md#find-outbound-ips).
>

### <a name="create-the-wordpress-database"></a>Erstellen der WordPress-Datenbank

```azurecli-interactive
az mysql db create --resource-group myResourceGroup --server-name <mysql-server-name> --name wordpress
```

Nach der Erstellung der Datenbank zeigt Cloud Shell Informationen wie im folgenden Beispiel an:

<pre>
{
  "additionalProperties": {},
  "charset": "latin1",
  "collation": "latin1_swedish_ci",
  "id": "/subscriptions/12db1644-4b12-4cab-ba54-8ba2f2822c1f/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/&lt;mysql-server-name&gt;/databases/wordpress",
  "name": "wordpress",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DBforMySQL/servers/databases"
}
</pre>

### <a name="configure-database-variables-in-wordpress"></a>Konfigurieren von Datenbankvariablen in WordPress

Zum Herstellen einer Verbindung für die WordPress-App mit diesem neuen MySQL-Server konfigurieren Sie einige WordPress-spezifische Umgebungsvariablen, z.B. den mit `MYSQL_SSL_CA` definierten SSL-Zertifizierungsstellenpfad. [Baltimore CyberTrust Root](https://www.digicert.com/digicert-root-certificates.htm) von [DigiCert](https://www.digicert.com/) ist unten im [benutzerdefinierten Image](#use-a-custom-image-for-mysql-ssl-and-other-configurations) angegeben.

Verwenden Sie zum Vornehmen dieser Änderungen in Cloud Shell den Befehl [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set). Bei App-Einstellungen wird die Groß-/Kleinschreibung berücksichtigt und die Einstellungen sind durch Leerzeichen getrennt.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WORDPRESS_DB_HOST="<mysql-server-name>.mysql.database.azure.com" WORDPRESS_DB_USER="adminuser@<mysql-server-name>" WORDPRESS_DB_PASSWORD="My5up3rStr0ngPaSw0rd!" WORDPRESS_DB_NAME="wordpress" MYSQL_SSL_CA="BaltimoreCyberTrustroot.crt.pem"
```

Nach der Erstellung der App-Einstellung zeigt Cloud Shell Informationen wie im folgenden Beispiel an:

<pre>
[
  {
    "name": "WORDPRESS_DB_HOST",
    "slotSetting": false,
    "value": "&lt;mysql-server-name&gt;.mysql.database.azure.com"
  },
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@&lt;mysql-server-name&gt;"
  },
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WORDPRESS_DB_PASSWORD",
    "slotSetting": false,
    "value": "My5up3rStr0ngPaSw0rd!"
  },
  {
    "name": "MYSQL_SSL_CA",
    "slotSetting": false,
    "value": "BaltimoreCyberTrustroot.crt.pem"
  }
]
</pre>

Weitere Informationen zu Umgebungsvariablen finden Sie unter [Konfigurieren von Umgebungsvariablen](configure-custom-container.md#configure-environment-variables).

### <a name="use-a-custom-image-for-mysql-ssl-and-other-configurations"></a>Verwenden eines benutzerdefinierten Images für MySQL-SSL und andere Konfigurationen

Von Azure Database for MySQL wird standardmäßig SSL genutzt. Für WordPress ist eine zusätzliche Konfiguration erforderlich, um SSL mit MySQL nutzen zu können. Mit dem offiziellen Image von WordPress wird nicht diese zusätzliche Konfiguration bereitgestellt, sondern es wurde ein [benutzerdefiniertes Image](https://github.com/Azure-Samples/multicontainerwordpress) erstellt, um Ihnen den Vorgang zu vereinfachen. In der Praxis fügen Sie Ihrem eigenen Image die gewünschten Änderungen hinzu.

Das benutzerdefinierte Image basiert auf dem „offiziellen Image“ von [WordPress per Docker-Hub](https://hub.docker.com/_/wordpress/). An diesem benutzerdefinierten Image für Azure Database for MySQL wurden die folgenden Änderungen vorgenommen:

* [Die Baltimore Cyber Trust Root-Zertifikatsdatei für SSL wurde MySQL hinzugefügt.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L61)
* [Die App-Einstellung für das MySQL-SSL-Zertifizierungsstellenzertifikat wird in WordPress „wp-config.php“ verwendet.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L163)
* [Die für MySQL-SSL erforderliche WordPress-Definition für MYSQL_CLIENT_FLAGS wurde hinzugefügt.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L164)

Die folgenden Änderungen wurden für Redis vorgenommen (Verwendung in einem späteren Abschnitt):

* [Die PHP-Erweiterung für Redis v4.0.2 wurde hinzugefügt.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35)
* [Erforderliches Entzippen für die Dateiextraktion wurde hinzugefügt.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71)
* [Das Redis Object Cache 1.3.8-WordPress-Plug-In wurde hinzugefügt.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [Die App-Einstellung für den Redis-Hostnamen in der WordPress-Datei „wp-config.php“ wird verwendet.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162)

Für die Verwendung des benutzerdefinierten Images müssen Sie Ihre Datei „docker-compose-wordpress.yml“ aktualisieren. Geben Sie in Cloud Shell `nano docker-compose-wordpress.yml` ein, um den Nano-Text-Editor zu öffnen. Ändern Sie `image: wordpress` so, dass `image: mcr.microsoft.com/azuredocs/multicontainerwordpress` verwendet wird. Den Datenbankcontainer benötigen Sie nicht mehr. Entfernen Sie die Abschnitte `db`, `environment`, `depends_on` und `volumes` aus der Konfigurationsdatei. Ihre Datei sollte wie im folgenden Code aussehen:

```yaml
version: '3.3'

services:
   wordpress:
     image: mcr.microsoft.com/azuredocs/multicontainerwordpress
     ports:
       - "8000:80"
     restart: always
```

Speichern Sie Ihre Änderungen, und beenden Sie Nano. Verwenden Sie `^O` zum Speichern und `^X` zum Beenden.

### <a name="update-app-with-new-configuration"></a>Aktualisieren der App mit einer neuen Konfiguration

Konfigurieren Sie Ihre [Web-App](overview.md) mit mehreren Containern in Cloud Shell mit dem Befehl [az webapp config container set](/cli/azure/webapp/config/container#az-webapp-config-container-set) neu. Vergessen Sie nicht, _\<app-name>_ durch den Namen der zuvor erstellten Web-App zu ersetzen.

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app-name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Nachdem die App neu konfiguriert wurde, zeigt Cloud Shell Informationen wie im folgenden Beispiel an:

<pre>
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICB3b3JkcHJlc3M6CiAgICAgaW1hZ2U6IG1zYW5nYXB1L3dvcmRwcmVzcwogICAgIHBvcnRzOgogICAgICAgLSAiODAwMDo4MCIKICAgICByZXN0YXJ0OiBhbHdheXM="
  }
]
</pre>

### <a name="browse-to-the-app"></a>Navigieren zur App

Navigieren Sie zur bereitgestellten App unter `http://<app-name>.azurewebsites.net`. Die App nutzt jetzt Azure Database for MySQL.

![Beispiel-App mit mehreren Containern für Web-App für Container][1]

## <a name="add-persistent-storage"></a>Hinzufügen von beständigem Speicher

Ihre App mit mehreren Containern wird jetzt in Web-App für Container ausgeführt. Wenn Sie jetzt WordPress installieren und Ihre App später neu starten, merken Sie, dass Ihre WordPress-Installation nicht mehr vorhanden ist. Dies ist der Fall, weil in Ihrer Docker Compose-Konfiguration derzeit auf einen Speicherort innerhalb Ihres Containers verwiesen wird. Die im Container installierten Dateien werden nicht über den Neustart der App hinaus beibehalten. In diesem Abschnitt fügen Sie Ihrem WordPress-Container [beständigen Speicher hinzu](configure-custom-container.md#use-persistent-shared-storage).

### <a name="configure-environment-variables"></a>Konfigurieren von Umgebungsvariablen

Für die Nutzung von beständigem Speicher aktivieren Sie diese Einstellung in App Service. Verwenden Sie zum Vornehmen dieser Änderung in Cloud Shell den Befehl [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set). Bei App-Einstellungen wird die Groß-/Kleinschreibung berücksichtigt und die Einstellungen sind durch Leerzeichen getrennt.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Nach der Erstellung der App-Einstellung zeigt Cloud Shell Informationen wie im folgenden Beispiel an:

<pre>
[
  &lt; JSON data removed for brevity. &gt;
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  }
]
</pre>

### <a name="modify-configuration-file"></a>Ändern der Konfigurationsdatei

Geben Sie in Cloud Shell `nano docker-compose-wordpress.yml` ein, um den Nano-Text-Editor zu öffnen.

Mit der Option `volumes` wird das Dateisystem einem Verzeichnis im Container zugeordnet. `${WEBAPP_STORAGE_HOME}` ist eine Umgebungsvariable in App Service, die dem beständigen Speicher für Ihre App zugeordnet wird. Sie verwenden diese Umgebungsvariable in der Option „volumes“, damit die WordPress-Dateien nicht im Container installiert werden, sondern im beständigen Speicher. Nehmen Sie an der Datei die folgenden Änderungen vor:

Fügen Sie im Abschnitt `wordpress` die Option `volumes` hinzu, damit er wie im folgenden Codebeispiel aussieht:

```yaml
version: '3.3'

services:
   wordpress:
     image: mcr.microsoft.com/azuredocs/multicontainerwordpress
     volumes:
      - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
     ports:
       - "8000:80"
     restart: always
```

### <a name="update-app-with-new-configuration"></a>Aktualisieren der App mit einer neuen Konfiguration

Konfigurieren Sie Ihre [Web-App](overview.md) mit mehreren Containern in Cloud Shell mit dem Befehl [az webapp config container set](/cli/azure/webapp/config/container#az-webapp-config-container-set) neu. Vergessen Sie nicht, _\<app-name>_ durch einen eindeutigen App-Namen zu ersetzen.

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app-name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Nachdem der Befehl ausgeführt wurde, wird die Ausgabe in etwa wie im folgenden Beispiel angezeigt:

<pre>
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  },
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICBteXNxbDoKICAgICBpbWFnZTogbXlzcWw6NS43CiAgICAgdm9sdW1lczoKICAgICAgIC0gZGJfZGF0YTovdmFyL2xpYi9teXNxbAogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgTVlTUUxfUk9PVF9QQVNTV09SRDogZXhhbXBsZXBhc3MKCiAgIHdvcmRwcmVzczoKICAgICBkZXBlbmRzX29uOgogICAgICAgLSBteXNxbAogICAgIGltYWdlOiB3b3JkcHJlc3M6bGF0ZXN0CiAgICAgcG9ydHM6CiAgICAgICAtICI4MDAwOjgwIgogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgV09SRFBSRVNTX0RCX1BBU1NXT1JEOiBleGFtcGxlcGFzcwp2b2x1bWVzOgogICAgZGJfZGF0YTo="
  }
]
</pre>

### <a name="browse-to-the-app"></a>Navigieren zur App

Navigieren Sie zur bereitgestellten App unter `http://<app-name>.azurewebsites.net`.

Für den WordPress-Container werden jetzt Azure Database for MySQL und beständiger Speicher verwendet.

## <a name="add-redis-container"></a>Hinzufügen des Redis-Containers

 Im „offiziellen Image“ von WordPress sind die Abhängigkeiten für Redis nicht enthalten. Diese Abhängigkeiten und die zusätzliche Konfiguration, die für die Verwendung von Redis mit WordPress erforderlich ist, wurden für Sie in diesem [benutzerdefinierten Image](https://github.com/Azure-Samples/multicontainerwordpress) vorbereitet. In der Praxis fügen Sie Ihrem eigenen Image die gewünschten Änderungen hinzu.

Das benutzerdefinierte Image basiert auf dem „offiziellen Image“ von [WordPress per Docker-Hub](https://hub.docker.com/_/wordpress/). Die folgenden Änderungen wurden an diesem benutzerdefinierten Image für Redis vorgenommen:

* [Die PHP-Erweiterung für Redis v4.0.2 wurde hinzugefügt.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35)
* [Erforderliches Entzippen für die Dateiextraktion wurde hinzugefügt.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71)
* [Das Redis Object Cache 1.3.8-WordPress-Plug-In wurde hinzugefügt.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [Die App-Einstellung für den Redis-Hostnamen in der WordPress-Datei „wp-config.php“ wird verwendet.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162)

Fügen Sie den Redis-Container unten in der Konfigurationsdatei hinzu, damit diese wie im folgenden Beispiel aussieht:

```yaml
version: '3.3'

services:
   wordpress:
     image: mcr.microsoft.com/azuredocs/multicontainerwordpress
     ports:
       - "8000:80"
     restart: always

   redis:
     image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
     environment: 
      - ALLOW_EMPTY_PASSWORD=yes
     restart: always
```

### <a name="configure-environment-variables"></a>Konfigurieren von Umgebungsvariablen

Für die Nutzung von Redis aktivieren Sie die Einstellung `WP_REDIS_HOST` in App Service. Dies ist eine *erforderliche Einstellung* für WordPress, um die Kommunikation mit dem Redis-Host zu ermöglichen. Verwenden Sie zum Vornehmen dieser Änderung in Cloud Shell den Befehl [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set). Bei App-Einstellungen wird die Groß-/Kleinschreibung berücksichtigt und die Einstellungen sind durch Leerzeichen getrennt.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WP_REDIS_HOST="redis"
```

Nach der Erstellung der App-Einstellung zeigt Cloud Shell Informationen wie im folgenden Beispiel an:

<pre>
[
  &lt; JSON data removed for brevity. &gt;
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@&lt;mysql-server-name&gt;"
  },
  {
    "name": "WP_REDIS_HOST",
    "slotSetting": false,
    "value": "redis"
  }
]
</pre>

### <a name="update-app-with-new-configuration"></a>Aktualisieren der App mit einer neuen Konfiguration

Konfigurieren Sie Ihre [Web-App](overview.md) mit mehreren Containern in Cloud Shell mit dem Befehl [az webapp config container set](/cli/azure/webapp/config/container#az-webapp-config-container-set) neu. Vergessen Sie nicht, _\<app-name>_ durch einen eindeutigen App-Namen zu ersetzen.

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app-name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

Nachdem der Befehl ausgeführt wurde, wird die Ausgabe in etwa wie im folgenden Beispiel angezeigt:

<pre>
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICBteXNxbDoKICAgICBpbWFnZTogbXlzcWw6NS43CiAgICAgdm9sdW1lczoKICAgICAgIC0gZGJfZGF0YTovdmFyL2xpYi9teXNxbAogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgTVlTUUxfUk9PVF9QQVNTV09SRDogZXhhbXBsZXBhc3MKCiAgIHdvcmRwcmVzczoKICAgICBkZXBlbmRzX29uOgogICAgICAgLSBteXNxbAogICAgIGltYWdlOiB3b3JkcHJlc3M6bGF0ZXN0CiAgICAgcG9ydHM6CiAgICAgICAtICI4MDAwOjgwIgogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgV09SRFBSRVNTX0RCX1BBU1NXT1JEOiBleGFtcGxlcGFzcwp2b2x1bWVzOgogICAgZGJfZGF0YTo="
  }
]
</pre>

### <a name="browse-to-the-app"></a>Navigieren zur App

Navigieren Sie zur bereitgestellten App unter `http://<app-name>.azurewebsites.net`.

Führen Sie die Schritte aus, und installieren Sie WordPress.

### <a name="connect-wordpress-to-redis"></a>Herstellen einer Verbindung für WordPress mit Redis

Melden Sie sich bei der WordPress-Administration an. Wählen Sie im linken Navigationsbereich die Option **Plugins** (Plug-Ins) und dann **Installed Plugins** (Installierte Plug-Ins).

![Auswählen von WordPress-Plug-Ins][2]

Anzeige aller Plug-Ins

Navigieren Sie auf der Seite mit den Plug-Ins zu **Redis Object Cache**, und klicken Sie auf **Activate** (Aktivieren).

![Aktivieren von Redis][3]

Klicken Sie auf **Einstellungen**.

![Klicken auf „Settings“ (Einstellungen)][4]

Klicken Sie auf die Schaltfläche **Enable Object Cache** (Object Cache aktivieren).

![Klicken auf die Schaltfläche „Enable Object Cache“ (Object Cache aktivieren)][5]

WordPress stellt die Verbindung mit dem Redis-Server her. Der **Status** der Verbindung wird auf derselben Seite angezeigt.

![WordPress stellt die Verbindung mit dem Redis-Server her. Der **Status** der Verbindung wird auf derselben Seite angezeigt.][6]

**Glückwunsch!** . Sie haben für WordPress eine Verbindung mit Redis hergestellt. Für die App, die jetzt für die Produktion bereit ist, werden nun **Azure Database for MySQL, beständiger Speicher und Redis** verwendet. Sie können Ihren App Service-Plan jetzt auf mehrere Instanzen aufskalieren.

## <a name="find-docker-container-logs"></a>Zugreifen auf Docker-Containerprotokolle

Falls bei der Verwendung mehrerer Container Probleme auftreten, können Sie auf die Containerprotokolle zugreifen, indem Sie zum folgenden Speicherort navigieren: `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Ihnen wird daraufhin eine Ausgabe angezeigt, die in etwa wie im folgenden Beispiel aussieht:

<pre>
[
   {
      "machineName":"RD00XYZYZE567A",
      "lastUpdated":"2018-05-10T04:11:45Z",
      "size":25125,
      "href":"https://&lt;app-name&gt;.scm.azurewebsites.net/api/vfs/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log",
      "path":"/home/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log"
   }
]
</pre>

Es ist ein Protokoll für jeden Container und ein zusätzliches Protokoll für den übergeordneten Prozess vorhanden. Kopieren Sie den jeweiligen `href`-Wert in den Browser, um das Protokoll anzuzeigen.

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:
> [!div class="checklist"]
> * Konvertieren einer Docker Compose-Konfiguration für Web-App für Container
> * Bereitstellen einer App mit mehreren Containern in Azure
> * Hinzufügen von Anwendungseinstellungen
> * Verwenden von beständigem Speicher für Ihre Container
> * Herstellen einer Verbindung mit Azure-Datenbank für MySQL
> * Beheben von Fehlern

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Ihrer App einen benutzerdefinierten DNS-Namen zuordnen.

> [!div class="nextstepaction"]
> [Tutorial: Zuordnen eines benutzerdefinierten DNS-Namens zu Ihrer App](app-service-web-tutorial-custom-domain.md)

Oder sehen Sie sich weitere Ressourcen an:

> [!div class="nextstepaction"]
> [Konfigurieren eines benutzerdefinierten Containers](configure-custom-container.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
[2]: ./media/tutorial-multi-container-app/wordpress-plugins.png
[3]: ./media/tutorial-multi-container-app/activate-redis.png
[4]: ./media/tutorial-multi-container-app/redis-settings.png
[5]: ./media/tutorial-multi-container-app/enable-object-cache.png
[6]: ./media/tutorial-multi-container-app/redis-connected.png

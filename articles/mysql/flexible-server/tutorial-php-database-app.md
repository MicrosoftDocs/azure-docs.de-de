---
title: 'Tutorial: Erstellen einer PHP-App (Laravel) mit Azure Database for MySQL-Server – Flexible Server.'
description: In diesem Tutorial wird erläutert, wie Sie eine PHP-App mit Flexible Server erstellen.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: tutorial
ms.devlang: php
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 426cf59c9fb9d88039231ed441b2ffc7246716c7
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844436"
---
# <a name="tutorial-build-a-php-laravel-and-mysql-flexible-server-preview-app-in-azure-app-service"></a>Tutorial: Erstellen einer PHP- (Laravel) und flexiblen MySQL – Flexible Server-App (Vorschau) in Azure App Service


:::image type="content" source="media/tutorial-php-database-app/complete-checkbox-published.png" alt-text="PHP-Web-App in Azure mit Flexible Server":::

[Azure App Service](../../app-service/overview.md) bietet einen hochgradig skalierbaren Webhostingdienst mit Self-Patching unter dem Linux-Betriebssystem. In diesem Tutorial wird gezeigt, wie Sie eine PHP-App in Azure erstellen und mit einer MySQL-Datenbank verbinden. Wenn Sie fertig sind, verfügen Sie über eine [Laravel](https://laravel.com/)-App, die in Azure App Service für Linux ausgeführt wird.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Einrichten einer PHP-App (Laravel) mit lokalem MySQL
> * Erstellen einer MySQL – Flexible Server-Instanz (Vorschau)
> * Verbinden einer PHP-App mit einer MySQL – Flexible Server-Instanz (Vorschau)
> * Bereitstellen der App in Azure App Service
> * Aktualisieren des Datenmodells und erneutes Bereitstellen der App
> * Verwalten der App im Azure-Portal

Wenn Sie kein [Azure-Abonnement](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

1. [Installation von Git](https://git-scm.com/)
2. [Installation von PHP 5.6.4 oder höher](https://php.net/downloads.php)
3. [Installation von Composer](https://getcomposer.org/doc/00-intro.md)
4. Aktivieren Sie die folgenden PHP-Erweiterungen, die Laravel benötigt: OpenSSL, PDO-MySQL, Mbstring, Tokenizer, XML
5. [Installieren und Starten von MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html)

## <a name="prepare-local-mysql"></a>Vorbereiten der lokalen MySQL-Instanz

In diesem Schritt erstellen Sie eine Datenbank auf einem lokalen MySQL-Server für die Verwendung in diesem Tutorial.

### <a name="connect-to-local-mysql-server"></a>Verbinden mit dem lokalen MySQL-Server

Stellen Sie in einem Terminalfenster eine Verbindung mit Ihrem lokalen MySQL-Server her. Sie können dieses Terminalfenster verwenden, um alle Befehle dieses Tutorials auszuführen.

```bash
mysql -u root -p
```

Wenn Sie zur Eingabe eines Kennworts aufgefordert werden, geben Sie das Kennwort für das `root`-Konto ein. Wenn Sie das Kennwort für Ihr Root-Konto vergessen haben, lesen Sie unter [MySQL: How to Reset the Root Password (MySQL: Zurücksetzen des Root-Kennworts)](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html) nach.

Wenn der Befehl erfolgreich ausgeführt wurde, wird der MySQL-Server ausgeführt. Stellen Sie andernfalls anhand der [Nachinstallationsschritte für MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html) sicher, dass Ihr lokaler MySQL-Server gestartet wurde.

### <a name="create-a-database-locally"></a>Lokales Erstellen einer Datenbank

Erstellen Sie an der Eingabeaufforderung `mysql` eine Datenbank.

```sql
CREATE DATABASE sampledb;
```

Beenden Sie die Serververbindung durch Eingabe von `quit`.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-php-app-locally"></a>Lokales Erstellen einer PHP-App
In diesem Schritt rufen Sie eine Laravel-Beispielanwendung ab, konfigurieren ihre Datenbankverbindung und führen sie lokal aus.

### <a name="clone-the-sample"></a>Klonen des Beispiels

Navigieren Sie im Terminalfenster zu einem leeren Verzeichnis, in dem Sie die Beispielanwendung klonen können.  Führen Sie den folgenden Befehl aus, um das Beispielrepository zu klonen.

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

Wechseln Sie mit `cd` in das geklonte Verzeichnis.
Installieren Sie die erforderlichen Pakete.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>Konfigurieren der MySQL-Verbindung

Erstellen Sie im Repositorystamm eine Datei mit dem Namen *.env*. Kopieren Sie die folgenden Variablen in die Datei *.env*. Ersetzen Sie den Platzhalter _&lt;root_password>_ durch das Kennwort des MySQL-Root-Benutzers.

```txt
APP_ENV=local
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

Informationen dazu, wie Laravel die _ENV_-Datei verwendet, finden Sie unter [Laravel Environment Configuration](https://laravel.com/docs/5.4/configuration#environment-configuration) (Konfigurieren der Laravel-Umgebung).

### <a name="run-the-sample-locally"></a>Lokales Ausführen des Beispiels

Führen Sie [Laravel-Datenbankmigrationen](https://laravel.com/docs/5.4/migrations) durch, um die von der Anwendung benötigten Tabellen zu erstellen. Im Verzeichnis _database/migrations_ im Git-Repository können Sie sehen, welche Tabellen bei den Migrationen erstellt werden.

```bash
php artisan migrate
```

Generieren Sie einen neuen Anwendungsschlüssel für Laravel.

```bash
php artisan key:generate
```

Führen Sie die Anwendung aus.

```bash
php artisan serve
```

Navigieren Sie in einem Browser zu `http://localhost:8000`. Fügen Sie auf der Seite einige Aufgaben hinzu.

:::image type="content" source="media/tutorial-php-database-app/mysql-connect-success.png" alt-text="Erfolgreiche Verbindung zwischen PHP und MySQL":::

Geben Sie zum Beenden von PHP im Terminal `Ctrl + C` ein.

## <a name="create-a-mysql-flexible-server-preview"></a>Erstellen einer MySQL – Flexible Server-Instanz (Vorschau)
In diesem Schritt erstellen Sie eine MySQL-Datenbank im Dienst [Azure Database for MySQL – Flexible Server](../index.yml), der sich in der öffentlichen Vorschauphase befindet. Später konfigurieren Sie die PHP-Anwendung für eine Verbindung mit dieser Datenbank. Erstellen Sie in [Azure Cloud Shell](../../cloud-shell/overview.md) mit dem Befehl [`az flexible-server create`](/cli/azure/mysql/server#az-mysql-flexible-server-create) einen Server.

```azurecli-interactive
az mysql flexible-server create  --resource-group myResourceGroup --public-access <IP-Address>
```

> [!IMPORTANT]
> - Notieren Sie sich **Servername** und **Verbindungszeichenfolge**, um sie im nächsten Schritt zur Herstellung einer Verbindung und zur Durchführung einer Laravel-Datenmigration zu verwenden.
> - Geben Sie für das Argument **IP-Adresse** die IP-Adresse Ihres Clientcomputers an. Der Server ist nach der Erstellung gesperrt, weshalb Sie den Zugriff auf Ihren Clientcomputer erlauben müssen, um den Server lokal zu verwalten.

### <a name="configure-server-firewall-to-allow-web-app-to-connect-to-the-server"></a>Konfigurieren der Serverfirewall, damit die Web-App eine Verbindung mit dem Server herstellen kann

Erstellen Sie in Cloud Shell mit dem Befehl az mysql server firewall-rule create eine Firewallregel für Ihren MySQL-Server, um Clientverbindungen zuzulassen. Wenn sowohl Start-IP-Adresse als auch End-IP-Adresse auf ```0.0.0.0``` festgelegt sind, wird die Firewall nur für andere Azure-Dienste geöffnet, die keine statische IP-Adressen zum Herstellen einer Verbindung mit dem Server haben.

```azurecli
az mysql flexible-server firewall-rule create --name allanyAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="connect-to-production-mysql-server-locally"></a>Lokales Verbinden mit dem MySQL-Produktionsserver

Stellen Sie im lokalen Terminalfenster eine Verbindung mit dem MySQL-Server in Azure her. Verwenden Sie für ```<admin-user>``` und ```<mysql-server-name>``` den zuvor angegebenen Wert. Wenn Sie zur Eingabe eines Kennworts aufgefordert werden, verwenden Sie das Kennwort, das Sie bei der Datenbankerstellung in Azure angegeben haben.

```bash
mysql -u <admin-user> -h <mysql-server-name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-production-database"></a>Erstellen einer Produktionsdatenbank

Erstellen Sie an der Eingabeaufforderung `mysql` eine Datenbank.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Erstellen eines Benutzers mit Berechtigungen

Erstellen Sie einen Datenbankbenutzer mit dem Namen _phpappuser_, und weisen Sie ihm alle Berechtigungen in der Datenbank `sampledb` zu. Verwenden Sie zur Vereinfachung des Tutorials als Kennwort _MySQLAzure2020_.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2020';
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

Beenden Sie die Serververbindung durch Eingabe von `quit`.

```sql
quit
```

## <a name="connect-app-to-mysql-flexible-server"></a>Verbinden der App mit einer MySQL – Flexible Server-Instanz

In diesem Schritt verbinden Sie die PHP-Anwendung mit der MySQL-Datenbank, die Sie in Azure Database for MySQL erstellt haben.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Konfigurieren der Datenbankverbindung

Erstellen Sie im Stammverzeichnis des Repositorys eine Datei _.env.production_, und kopieren Sie die folgenden Variablen in die Datei. Ersetzen Sie sowohl in *DB_HOST* als auch *DB_USERNAME* den Platzhalter _&lt;mysql-server-name>_ .

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql-server-name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

Speichern Sie die Änderungen.

> [!TIP]
> Um Ihre MySQL-Verbindungsinformationen zu schützen, ist diese Datei bereits aus dem Git-Repository ausgeschlossen (siehe _.gitignore_ im Repositorystamm). Später erfahren Sie, wie Sie die Umgebungsvariablen in App Service so konfigurieren, dass eine Verbindung mit Ihrer Datenbank in Azure Database for MySQL hergestellt wird. Bei Umgebungsvariablen benötigen Sie die *ENV*-Datei in App Service nicht.
>

### <a name="configure-tlsssl-certificate"></a>Konfigurieren des TLS-/SSL-Zertifikats

Standardmäßig erzwingt die MySQL – Flexible Server-Instanz TLS-Verbindungen von Clients. Zum Herstellen einer Verbindung mit Ihrer MySQL-Datenbank in Azure müssen Sie das [_PEM_-Zertifikat verwenden, das von Azure Database for MySQL – Flexible Server bereitgestellt wird](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem). Laden Sie [dieses Zertifikat](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)) herunter, und legen Sie es in der lokalen Kopie des Beispiel-App-Repositorys im Ordner **SSL** ab.

Öffnen Sie _config/database.php_, und fügen Sie `connections.mysql` die Parameter `sslmode` und `options` hinzu, wie im folgenden Code gezeigt.

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL') && extension_loaded('pdo_mysql')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/DigiCertGlobalRootCA.crt.pem',
    ] : []
],
```

### <a name="test-the-application-locally"></a>Lokales Testen der Anwendung

Führen Sie die Laravel-Datenbankmigrationen mit der Datei _.env.production_ als Umgebungsdatei aus, um die Tabellen in der MySQL-Datenbank in Azure Database for MySQL zu erstellen. Beachten Sie, dass _.env.production_ die Verbindungsinformationen für Ihre MySQL-Datenbank in Azure enthält.

```bash
php artisan migrate --env=production --force
```

_.env.production_ verfügt noch nicht über einen gültigen Anwendungsschlüssel. Generieren Sie im Terminal einen neuen.

```bash
php artisan key:generate --env=production --force
```

Führen Sie die Beispielanwendung mit _.env.production_ als Umgebungsdatei aus.

```bash
php artisan serve --env=production
```

Navigieren Sie zu `http://localhost:8000`. Wenn die Seite ohne Fehler geladen wird, ist die PHP-Anwendung mit der MySQL-Datenbank in Azure verbunden.

Fügen Sie auf der Seite einige Aufgaben hinzu.

:::image type="content" source="media/tutorial-php-database-app/mysql-connect-success.png" alt-text="Erfolgreiche Verbindung zwischen PHP und Azure Database for MySQL":::

Geben Sie zum Beenden von PHP im Terminal `Ctrl + C` ein.

### <a name="commit-your-changes"></a>Committen Ihrer Änderungen

Führen Sie die folgenden Git-Befehle aus, um für Ihre Änderungen einen Commit durchzuführen:

```bash
git add .
git commit -m "database.php updates"
```

Ihre App kann jetzt bereitgestellt werden.

## <a name="deploy-to-azure"></a>Bereitstellen in Azure

In diesem Schritt stellen Sie die mit MySQL verbundene PHP-Anwendung in Azure App Service bereit.

### <a name="configure-a-deployment-user"></a>Konfigurieren eines Bereitstellungsbenutzers

Für die Bereitstellung in einer Azure-Web-App über FTP oder ein lokales Git kann ein Bereitstellungsbenutzer verwendet werden. Nach der Konfiguration des Bereitstellungsbenutzers können Sie ihn für alle Azure-Bereitstellungen verwenden. Der Benutzername und das Kennwort für die Bereitstellung auf Kontoebene unterscheiden sich von den Anmeldeinformationen für Ihr Azure-Abonnement.

Führen Sie zum Konfigurieren des Bereitstellungsbenutzers den Befehl [az webapp deployment user set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) in Azure Cloud Shell aus. Ersetzen Sie _&lt;username>_ and _&lt;password>_ durch Ihren Benutzernamen und Ihr Kennwort für die Bereitstellung.

Der Benutzername muss in Azure eindeutig sein und darf bei lokalen Git-Pushes nicht das Symbol „@“ enthalten.
Das Kennwort muss mindestens acht Zeichen lang sein und zwei der folgenden drei Elemente enthalten: Buchstaben, Zahlen und Symbole.

```bash
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku F1 --is-linux
```

In der JSON-Ausgabe wird das Kennwort als „null“ angezeigt. Bei der Meldung „Konflikt“. Details: Fehler 409. Ändern Sie den Benutzernamen. Bei der Meldung „Ungültige Anforderung“. Details: Fehler 400. Verwenden Sie ein sichereres Kennwort. **Notieren Sie Ihren Benutzernamen und Ihr Kennwort für die Bereitstellung Ihrer Web-Apps.**

### <a name="create-an-app-service-plan"></a>Wie erstelle ich einen Plan?

Erstellen Sie in der Cloud Shell mit dem Befehl [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) einen App Service-Plan in der Ressourcengruppe. Im folgenden Beispiel wird ein App Service-Plan mit dem Namen myAppServicePlan im Tarif Free (--sku F1) und in einem Linux-Container (--is-linux) erstellt.

az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku F1 --is-linux

<a name="create"></a>

### <a name="create-a-web-app"></a>Erstellen einer Web-App

Erstellen Sie im App Service-Plan myAppServicePlan eine [Web-App](../../app-service/overview.md#app-service-on-linux).

In Cloud Shell können Sie den Befehl [az webapp create](/cli/azure/webapp#az-webapp-create) verwenden. Ersetzen Sie im folgenden Beispiel _&lt;App-Name>_ durch einen global eindeutigen App-Namen (gültige Zeichen sind `a-z`, `0-9` und `-`). Die Runtime ist auf `PHP|7.0` festgelegt. Führen Sie zum Anzeigen aller unterstützten Runtimes den folgenden Befehl aus:[az webapp list-runtimes --linux](/cli/azure/webapp#az-webapp-list-runtimes).

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
```

Nach Erstellung der Web-App zeigt die Azure CLI eine Ausgabe wie im folgenden Beispiel an:

```
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Sie haben eine leere neue Web-App mit aktivierter Git-Bereitstellung erstellt.

> [!NOTE]
> Die URL des Git-Remoterepository wird in der Eigenschaft deploymentLocalGitUrl im folgenden Format gezeigt: https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git. Speichern Sie diese URL, da Sie sie später noch benötigen.

### <a name="configure-database-settings"></a>Konfigurieren der Datenbankeinstellungen

Legen Sie in App Service mit dem Befehl [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) Umgebungsvariablen als _App-Einstellungen_ fest.

Mit dem folgenden Befehl werden die App-Einstellungen `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` und `DB_PASSWORD` konfiguriert. Ersetzen Sie die Platzhalter _&lt;app-name>_ und _&lt;mysql-server-name>_ .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<mysql-server-name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
```

Sie können die PHP-Methode [getenv](https://www.php.net/manual/en/function.getenv.php) verwenden, um auf die Einstellungen zuzugreifen. Im Laravel-Code wird ein [env](https://laravel.com/docs/5.4/helpers#method-env)-Wrapper für die PHP-Methode `getenv` verwendet. Die MySQL-Konfiguration in _config/database.php_ sieht beispielsweise wie im folgenden Code aus:

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>Konfigurieren der Laravel-Umgebungsvariablen

Für Laravel ist in App Service ein Anwendungsschlüssel erforderlich. Sie können ihn mit App-Einstellungen konfigurieren.

Verwenden Sie im lokalen Terminalfenster `php artisan`, um einen neuen Anwendungsschlüssel zu generieren, ohne ihn in der Datei _.env_ zu speichern.

```bash
php artisan key:generate --show
```

Legen Sie in Cloud Shell mit dem Befehl [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) den Anwendungsschlüssel in der App Service-App fest. Ersetzen Sie die Platzhalter _&lt;app-name>_ und _&lt;outputofphpartisankey:generate>_ .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"` weist Laravel an, Debuginformationen zurückzugeben, wenn die bereitgestellte App Fehler feststellt. Legen Sie beim Ausführen einer Produktionsanwendung `false` fest, weil dies sicherer ist.

### <a name="set-the-virtual-application-path"></a>Festlegen des virtuellen Anwendungspfads

Der [Lebenszyklus der Laravel-Anwendung](https://laravel.com/docs/5.4/lifecycle) beginnt im _öffentlichen_ Verzeichnis anstatt im Stammverzeichnis der Anwendung. Für das PHP-Docker-Image für App Service wird Apache verwendet, und das Anpassen des `DocumentRoot`-Elements für Laravel ist nicht möglich. Sie können aber `.htaccess` verwenden, um alle Anforderungen so umzuschreiben, dass sie nicht auf das Stammverzeichnis verweisen, sondern auf _/public_. Im Repositorystamm wurde bereits ein `.htaccess`-Element für diese Zwecke hinzugefügt. Hiermit ist Ihre Laravel-Anwendung bereit für die Bereitstellung.

Weitere Informationen finden Sie unter [Ändern des Stammverzeichnisses der Website](../../app-service/configure-language-php.md?pivots=platform-linux#change-site-root).

### <a name="push-to-azure-from-git"></a>Übertragen von Git an Azure mithilfe von Push

Kehren Sie zum lokalen Terminalfenster zurück, und fügen Sie Ihrem lokalen Git-Repository einen Azure-Remotespeicherort hinzu. Ersetzen Sie _&lt;deploymentLocalGitUrl-from-create-step>_ durch die URL des Git-Remotespeicherorts, den Sie in [Erstellen einer Web-App](#create-a-web-app) gespeichert haben.

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Führen Sie einen Pushvorgang zum Azure-Remotespeicherort durch, um Ihre App mit dem folgenden Befehl bereitzustellen. Wenn Sie von der Git-Anmeldeinformationsverwaltung zur Eingabe von Anmeldeinformationen aufgefordert werden, müssen Sie die Anmeldeinformationen eingeben, die Sie in **Konfigurieren eines Bereitstellungsbenutzers** erstellt haben (nicht die Anmeldeinformationen, die Sie zur Anmeldung beim Azure-Portal verwenden).

```bash
git push azure master
```

Die Ausführung dieses Befehls kann einige Minuten in Anspruch nehmen. Während der Ausführung werden Informationen angezeigt, die den Informationen im folgenden Beispiel ähneln:

<pre>
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
&lt; Output has been truncated for readability &gt;
</pre>

### <a name="browse-to-the-azure-app"></a>Navigieren zur Azure-App

Browsen Sie zu `http://<app-name>.azurewebsites.net`, und fügen Sie der Liste einige Aufgaben hinzu.

:::image type="content" source="media/tutorial-php-database-app/php-mysql-in-azure.png" alt-text="PHP-Web-App in Azure":::

Glückwunsch! Sie führen eine datengesteuerte PHP-App in Azure App Service aus.

## <a name="update-model-locally-and-redeploy"></a>Lokales Aktualisieren und erneutes Bereitstellen des Modells

In diesem Schritt nehmen Sie eine einfache Änderung am `task`-Datenmodell und der Web-App vor und veröffentlichen das Update dann in Azure.

Für das tasks-Szenario ändern Sie die Anwendung, damit Sie eine Aufgabe als abgeschlossen kennzeichnen können.

### <a name="add-a-column"></a>Hinzufügen eines Spaltennamens

Navigieren Sie im lokalen Terminalfenster zum Stamm des Git-Repositorys.

Generieren Sie eine neue Datenbankmigration für die Tabelle `tasks`:

```bash
php artisan make:migration add_complete_column --table=tasks
```

Dieser Befehl zeigt den Namen der Migrationsdatei an, die generiert wird. Suchen Sie unter _database/migrations_ nach dieser Datei, und öffnen Sie sie.

Ersetzen Sie die `up`-Methode durch den folgenden Code:

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

Mit dem obigen Code wird der Tabelle `tasks` die boolesche Spalte `complete` hinzugefügt.

Ersetzen Sie die `down`-Methode durch den folgenden Code für die Rollbackaktion:

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

Führen Sie im lokalen Terminalfenster Laravel-Datenbankmigrationen aus, um die Änderung in der lokalen Datenbank vorzunehmen.

```bash
php artisan migrate
```

Das Modell `Task` (siehe _app/Task.php_) wird basierend auf der [Laravel-Namenskonvention](https://laravel.com/docs/5.4/eloquent#defining-models) standardmäßig der Tabelle `tasks` zugeordnet.

### <a name="update-application-logic"></a>Aktualisieren der Anwendungslogik

Öffnen Sie die Datei *routes/web.php*. Die Anwendung definiert hier die Routen und Geschäftslogik.

Fügen Sie mit folgendem Code am Ende der Datei eine Route hinzu:

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

Der obige Code führt eine einfache Aktualisierung des Datenmodells durch, indem der Wert von `complete` geändert wird.

### <a name="update-the-view"></a>Aktualisieren der Ansicht

Öffnen Sie die Datei *resources/views/tasks.blade.php*. Suchen Sie das öffnende Tag `<tr>`, und ersetzen Sie es durch Folgendes:

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

Mit dem obigen Code wird die Zeilenfarbe abhängig davon geändert, ob die Aufgabe abgeschlossen ist.

Die nächste Zeile enthält folgenden Code:

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

Ersetzen Sie die gesamte Zeile durch den folgenden Code:

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

Der obige Code fügt die Schaltfläche zum Übermitteln hinzu, die auf die zuvor definierte Route verweist.

### <a name="test-the-changes-locally"></a>Lokales Testen der Änderungen

Führen Sie den Entwicklungsserver im lokalen Terminalfenster aus dem Stammverzeichnis des Git-Repositorys aus.

```bash
php artisan serve
```

Navigieren Sie zu `http://localhost:8000`, um die Änderung des Aufgabenstatus anzuzeigen, und aktivieren Sie das Kontrollkästchen.

:::image type="content" source="media/tutorial-php-database-app/complete-checkbox.png" alt-text="Hinzugefügtes Kontrollkästchen in der Aufgabe":::

Geben Sie zum Beenden von PHP im Terminal `Ctrl + C` ein.

### <a name="publish-changes-to-azure"></a>Veröffentlichen von Änderungen in Azure

Führen Sie im lokalen Terminalfenster Laravel-Datenbankmigrationen mit der Produktions-Verbindungszeichenfolge aus, um die Änderung in Azure-Datenbank vorzunehmen.

```bash
php artisan migrate --env=production --force
```

Führen Sie für alle Änderungen in Git einen Commit aus, und übertragen Sie dann die Codeänderungen per Pushvorgang an Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Wechseln Sie nach Abschluss des `git push`-Vorgangs zur Azure-App, und testen Sie die neuen Funktionen.

:::image type="content" source="media/tutorial-php-database-app/complete-checkbox-published.png" alt-text="Auf Azure veröffentlichte Änderungen an Modell und Datenbank":::

Wenn Sie Aufgaben hinzugefügt haben, werden sie in der Datenbank beibehalten. Bei Updates des Datenschemas bleiben vorhandene Daten erhalten.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
In den vorherigen Schritten haben Sie Azure-Ressourcen in einer Ressourcengruppe erstellt. Wenn Sie diese Ressourcen in Zukunft nicht mehr benötigen, löschen Sie die Ressourcengruppe, indem Sie den folgenden Befehl in Cloud Shell ausführen:

```bash
az group delete --name myResourceGroup
```

<a name="next"></a>

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwalten von Ressourcen im Azure-Portal](../../azure-resource-manager/management/manage-resources-portal.md) <br/>
> [!div class="nextstepaction"]
> [Verwalten Ihres Servers](how-to-manage-server-cli.md)

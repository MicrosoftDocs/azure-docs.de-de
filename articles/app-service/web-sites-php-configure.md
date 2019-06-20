---
title: Konfigurieren der PHP-Runtime – Azure App Service
description: Erfahren Sie mehr über das Konfigurieren der PHP-Standardinstallation oder das Hinzufügen einer benutzerdefinierten PHP-Installation für Azure App Service.
services: app-service
documentationcenter: php
author: msangapu
manager: cfowler
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 38e0983830c540082a915332aa4158d2af84567b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65408884"
---
# <a name="configure-php-in-azure-app-service"></a>Konfigurieren von PHP in Azure App Service

## <a name="introduction"></a>Einführung

In diesem Leitfaden erfahren Sie, wie Sie die integrierte PHP-Laufzeit für Web-Apps, mobile Back-Ends und API-Apps in [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) konfigurieren, eine benutzerdefinierte PHP-Laufzeit angeben und Erweiterungen aktivieren. Um App Service zu verwenden, registrieren Sie sich für die [kostenlose Testversion]. Um diese Anleitung optimal zu nutzen, sollten Sie zuerst eine PHP-App in App Service erstellen.

## <a name="how-to-change-the-built-in-php-version"></a>Gewusst wie: Ändern der integrierten PHP-Version

PHP 5.6 ist standardmäßig installiert und kann sofort verwendet werden, wenn Sie eine App Service-App erstellen. Die beste Möglichkeit, um die verfügbare Versionsrevision, die Standardkonfiguration und die aktivierten Erweiterungen anzuzeigen, ist die Bereitstellung eines Skripts, das die Funktion [phpinfo()] abruft.

PHP 7.0 und PHP 7.2 sind ebenfalls verfügbar, aber nicht standardmäßig aktiviert. Gehen Sie folgendermaßen vor, um die PHP-Version zu aktualisieren:

### <a name="azure-portal"></a>Azure-Portal

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer App, und scrollen Sie zur Seite **Konfiguration**.

2. Wählen Sie auf der Seite **Einstellungen** die Option **Allgemeine Einstellungen** und dann die neue PHP-Version aus.

3. Klicken Sie im oberen Bereich des Blatts **Allgemeine Einstellungen** auf die Schaltfläche **Speichern**.

### <a name="azure-powershell-windows"></a>Azure PowerShell (Windows)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Öffnen Sie Azure PowerShell, und melden Sie sich bei Ihrem Konto an:

        PS C:\> Connect-AzAccount
2. Legen Sie die PHP-Version für die App fest.

        PS C:\> Set-AzureWebsite -PhpVersion {5.6 | 7.0 | 7.2} -Name {app-name}
3. Die PHP-Version ist jetzt festgelegt. Sie können diese Einstellungen bestätigen:

        PS C:\> Get-AzureWebsite -Name {app-name} | findstr PhpVersion

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle 

Um die Azure-Befehlszeilenschnittstelle verwenden zu können, muss die [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) auf Ihrem Computer installiert sein.

1. Öffnen Sie Terminal, und melden sich bei Ihrem Konto an.

        az login

1. Überprüfen Sie dies, um eine Liste der unterstützen Laufzeiten anzuzeigen.

        az webapp list-runtimes | grep php

2. Legen Sie die PHP-Version für die App fest.

        az webapp config set --php-version {5.6 | 7.0 | 7.1 | 7.2} --name {app-name} --resource-group {resource-group-name}

3. Die PHP-Version ist jetzt festgelegt. Sie können diese Einstellungen bestätigen:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Gewusst wie: Ändern der integrierten PHP-Konfigurationen

Für jede integrierte PHP-Laufzeit können Sie alle Konfigurationsoptionen ändern, indem Sie diese Schritte ausführen. (Informationen zu php.ini-Direktiven finden Sie unter [Liste der php.ini-Direktiven]).

### <a name="changing-phpiniuser-phpiniperdir-phpiniall-configuration-settings"></a>Ändern der Konfigurationseinstellungen PHP\_INI\_USER, PHP\_INI\_PERDIR, PHP\_INI\_ALL

1. Fügen Sie eine [.user.ini] -Datei zum Stammverzeichnis hinzu.
1. Fügen Sie der Datei `.user.ini` die Konfigurationseinstellungen hinzu, und verwenden Sie dieselbe Syntax wie für eine `php.ini`-Datei. Wenn Sie zum Beispiel die Einstellung `display_errors` aktivieren und die Einstellung `upload_max_filesize` auf 10 M festlegen möchten, enthält die Datei `.user.ini` diesen Text:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
2. Stellen Sie Ihre App bereit.
3. Starten Sie die App neu. (Der Neustart ist erforderlich, da die Frequenz, mit der PHP `.user.ini`-Dateien liest, durch die Einstellung `user_ini.cache_ttl` geregelt wird. Dies ist eine Einstellung auf Systemebene, die standardmäßig 300 Sekunden (5 Minuten) beträgt. Durch einen Neustart der App wird PHP gezwungen, die neuen Einstellungen in der `.user.ini`-Datei zu lesen.)

Alternativ zur Verwendung einer`.user.ini`-Datei können Sie auch die Funktion [ini_set()] in Skripts verwenden, um Konfigurationsoptionen festzulegen, die keine Direktiven auf Systemebene sind.

### <a name="changing-phpinisystem-configuration-settings"></a>Ändern der Konfigurationseinstellungen PHP\_INI\_SYSTEM

1. Hinzufügen einer App-Einstellung zu Ihrer App mit Schlüssel `PHP_INI_SCAN_DIR` und Wert `d:\home\site\ini`
1. Erstellen einer `settings.ini`-Datei über die Kudu-Konsole (http://&lt;site-name&gt;.scm.azurewebsite.net) im Verzeichnis `d:\home\site\ini`.
1. Fügen Sie der Datei `settings.ini` die Konfigurationseinstellungen hinzu, und verwenden Sie dieselbe Syntax wie für eine `php.ini`-Datei. Wenn Sie beispielsweise auf die Einstellung `curl.cainfo` für eine `*.crt`-Datei verweisen und, und die Einstellung 'wincache.maxfilesize' auf 512 KB festlegen, enthält die Datei `settings.ini` diesen Text:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. Um die Änderungen zu laden, starten Sie Ihre App neu.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Gewusst wie: Aktivieren von Erweiterungen in der PHP-Standardlaufzeit

Wie im vorherigen Anschnitt angegeben ist die Standardkonfiguration die beste Möglichkeit, um die Standard-PHP-Version anzuzeigen, und die aktivierten Erweiterungen dienen zur Bereitstellung eines Skripts, das [phpinfo()]abruft. Um zusätzliche Erweiterungen zu aktivieren, führen Sie diese Schritte aus:

### <a name="configure-via-ini-settings"></a>Konfigurieren über ini-Einstellungen

1. Fügen Sie ein `ext`-Verzeichnis zum `d:\home\site`-Verzeichnis hinzu.
1. Legen Sie Dateien mit der Erweiterung `.dll` im Verzeichnis `ext` ab (zum Beispiel `php_xdebug.dll`). Stellen Sie sicher, dass die Erweiterungen mit der Standardversion von PHP und mit VC9 kompatibel sowie nicht threadsicher (non-thread-safe, nts) sind.
1. Hinzufügen einer App-Einstellung zu Ihrer App mit Schlüssel `PHP_INI_SCAN_DIR` und Wert `d:\home\site\ini`
1. Erstellen Sie in `d:\home\site\ini` eine `ini`-Datei namens `extensions.ini`.
1. Fügen Sie der Datei `extensions.ini` die Konfigurationseinstellungen hinzu, und verwenden Sie dieselbe Syntax wie für eine `php.ini`-Datei. Wenn Sie beispielsweise die MongoDB- und XDebug-Erweiterungen aktivieren möchten, würde Ihre `extensions.ini` -Datei diesen Text enthalten:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. Starten Sie Ihre App neu, um die Änderungen zu laden.

### <a name="configure-via-app-setting"></a>Konfigurieren über App-Einstellungen

1. Fügen Sie dem Stammverzeichnis das Verzeichnis `bin` hinzu.
2. Legen Sie Dateien mit der Erweiterung `.dll` im Verzeichnis `bin` ab (zum Beispiel `php_xdebug.dll`). Stellen Sie sicher, dass die Erweiterungen mit der Standardversion von PHP und mit VC9 kompatibel sowie nicht threadsicher (non-thread-safe, nts) sind.
3. Stellen Sie Ihre App bereit.
4. Navigieren Sie im Azure-Portal zu Ihrer App, und klicken Sie auf die Schaltfläche **Konfiguration**, die sich unterhalb des Abschnitt **Einstellungen** befindet.
5. Wählen Sie auf dem Blatt **Konfiguration** die Option **Anwendungseinstellungen** aus.
6. Klicken Sie im Abschnitt **Anwendungseinstellungen** auf **+ Neue Anwendungseinstellung**, und erstellen Sie einen Schlüssel **PHP_EXTENSIONS**. Der Wert für diesen Schlüssel ist ein Pfad relativ zum Stammverzeichnis der Website: **bin\your-ext-file**.
7. Klicken Sie ganz unten auf die Schaltfläche **Update**, und klicken Sie dann auf **Speichern** oberhalb der Registerkarte **Anwendungseinstellungen**.

Zend-Erweiterungen werden bei Verwendung des Schlüssels **PHP_ZENDEXTENSIONS** ebenfalls unterstützt. Um mehrere Erweiterungen zu aktivieren, fügen Sie eine durch Trennzeichen getrennte Liste von `.dll` -Dateien für den Wert der App-Einstellung hinzu.

## <a name="how-to-use-a-custom-php-runtime"></a>Gewusst wie: Verwenden einer benutzerdefinierten PHP-Laufzeit

Anstelle der PHP-Standardlaufzeit kann App Service auch eine PHP-Laufzeit verwenden, die Sie für die Ausführung von PHP-Skripten angeben. Die Laufzeit, die Sie angeben, kann durch eine `php.ini` -Datei konfiguriert werden, die Sie ebenfalls angeben. Folgen Sie diesen Schritten, um eine benutzerdefinierte PHP-Laufzeit mit App Service zu verwenden.

1. Besorgen Sie sich eine nicht threadsichere, VC9- oder VC11-kompatible Version von PHP für Windows. Neuere Versionen von PHP für Windows finden Sie hier: [https://windows.php.net/download/]. Ältere Versionen finden Sie hier im Archiv: [https://windows.php.net/downloads/releases/archives/].
2. Ändern Sie die Datei `php.ini` für Ihre Laufzeit. Konfigurationseinstellungen, die reine Systemebenendirektiven sind, werden von App Service ignoriert. (Informationen zu Direktiven nur auf Systemebene finden Sie unter [Liste der php.ini-Direktiven]).
3. Optional können Sie Ihrer PHP-Laufzeit auch Erweiterungen hinzufügen und in der Datei `php.ini` aktivieren.
4. Fügen Sie dem Stammverzeichnis das Verzeichnis `bin` hinzu, und legen Sie das Verzeichnis, das die PHP-Laufzeit enthält, darin ab (zum Beispiel `bin\php`).
5. Stellen Sie Ihre App bereit.
6. Navigieren Sie im Azure-Portal zu Ihrer App, und klicken Sie auf das Blatt **Konfiguration**.
8. Wählen Sie auf dem Blatt **Konfiguration** die Option **Pfadzuordnungen** aus. 
9. Klicken Sie auf **+Neuer Handler**, fügen Sie im Feld „Erweiterung“ `*.php` hinzu, und fügen Sie den Pfad zu der ausführbaren Datei `php-cgi.exe` im **Skriptprozessor** hinzu. Wenn Sie die PHP-Laufzeit in Verzeichnis `bin` des Stammverzeichnisses Ihrer Anwendung abgelegt haben, lautet der Pfad `D:\home\site\wwwroot\bin\php\php-cgi.exe`.
10. Klicken Sie ganz unten auf **Update**, um das Hinzufügen der Handlerzuordnung fertig zu stellen.
11. Klicken Sie zum Speichern der Änderungen auf **Speichern**.

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Gewusst wie: Aktivieren der Composer-Automatisierung in Azure

Standardmäßig wird „composer.json“ (sofern in Ihrem PHP-Projekt vorhanden) von App Service nicht verarbeitet. Wenn Sie die [Git-Bereitstellung](deploy-local-git.md) nutzen, können Sie während `git push` die Verarbeitung von „composer.json“ aktivieren, indem Sie die Composer-Erweiterung aktivieren.

> [!NOTE]
> Sie können [für erstklassigen Support für Composer in App Service abstimmen](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)!
>

1. Klicken Sie auf dem Blatt Ihrer PHP-App im [Azure-Portal](https://portal.azure.com) auf **Tools** > **Erweiterungen**.

    ![Blatt mit den Azure-Portal-Einstellungen zum Aktivieren der Composer-Automatisierung in Azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Klicken Sie auf **Hinzufügen** und anschließend auf **Composer**.

    ![Hinzufügen der Composer-Erweiterung zum Aktivieren der Composer-Automatisierung in Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Klicken Sie auf **OK** , um die rechtlichen Bedingungen zu akzeptieren. Klicken Sie erneut auf **OK** , um die Erweiterung hinzuzufügen.

    Auf dem Blatt **Installierte Erweiterungen** wird nun die Composer-Erweiterung angezeigt.
    ![Akzeptieren der Geschäftsbedingungen zum Aktivieren der Composer-Automatisierung in Azure](./media/web-sites-php-configure/composer-extension-view.png)
4. Führen Sie jetzt im Terminalfenster Ihres lokalen Computers `git add`, `git commit` und `git push` für Ihre App aus. Beachten Sie, dass Composer in composer.json definierte Abhängigkeiten installiert.

    ![Git-Bereitstellung mit Composer-Automatisierung in Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie im [PHP Developer Center](https://azure.microsoft.com/develop/php/).

[kostenlose Testversion]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: https://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Liste der php.ini-Direktiven]: https://www.php.net/manual/en/ini.list.php
[.user.ini]: https://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: https://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[https://windows.php.net/download/]: https://windows.php.net/download/
[https://windows.php.net/downloads/releases/archives/]: https://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png

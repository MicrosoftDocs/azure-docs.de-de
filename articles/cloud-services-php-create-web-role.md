---
title: Erstellen von Azure-Web- und -Workerrollen für PHP
description: Ein Leitfaden zum Erstellen von PHP-Web- und -Workerrollen in einem Azure-Clouddienst und zum Konfigurieren der PHP-Laufzeit.
services: ''
documentationcenter: php
author: msangapu-msft
manager: gwallace
ms.assetid: 9f7ccda0-bd96-4f7b-a7af-fb279a9e975b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: 0b02662658b2199a13f117ad95c6402f9b2974f9
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95519845"
---
# <a name="create-php-web-and-worker-roles"></a>Erstellen von PHP-Web- und Workerrollen

## <a name="overview"></a>Übersicht

In diesem Leitfaden erfahren Sie, wie Sie PHP-Web- oder Workerrollen in einer Windows-Entwicklungsumgebung erstellen, eine bestimmte Version von PHP aus den "integrierten" verfügbaren Versionen auswählen, die PHP-Konfiguration ändern, Erweiterungen aktivieren und diese schließlich in Azure bereitstellen. Es wird zudem beschrieben, wie Sie eine Web- oder Workerrolle für die Nutzung einer PHP-Laufzeit konfigurieren (mit benutzerdefinierter Konfiguration und Erweiterungen), die Sie bereitstellen.

Azure bietet drei Computemodelle für das Ausführen von Anwendungen: Azure App Service, Azure Virtual Machines und Azure Cloud Services. Alle drei Modell unterstützen PHP. Cloud Services enthält Web- und Workerrollen und bietet *Platform as a Service (PaaS)* . In einem Clouddienst stellt eine Webrolle einen dedizierten Internet Information Services (IIS)-Webserver zum Hosten von Front-End-Webanwendungen bereit. Eine Workerrolle kann asynchrone, langfristige oder fortwährende Aufgaben ausführen, die von einer Benutzerinteraktion oder -eingabe unabhängig sind.

Weitere Informationen zu diesen Optionen finden Sie unter [Computehostingoptionen in Azure](cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Herunterladen des Azure SDK für PHP

Das [Azure SDK für PHP](https://github.com/Azure/azure-sdk-for-php) besteht aus verschiedenen Komponenten. In diesem Artikel werden zwei Komponenten verwendet: Azure PowerShell und die Azure-Emulatoren. Diese beiden Komponenten können über den Microsoft-Webplattform-Installer installiert werden. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/).

## <a name="create-a-cloud-services-project"></a>Erstellen eines Cloud-Dienst-Projekts

Der erste Schritt bei der Erstellung einer PHP-Web- oder -Workerrolle ist die Erstellung eines Azure-Dienstprojekts. Ein Azure-Dienstprojekt dient als logischer Container für Web- und Workerrollen und enthält das [Azure-Dienstdefinitionsschema (CSDEF-Datei)] und das [Azure-Dienstkonfigurationsschema (CSCFG-Datei)] des Projekts.

Führen Sie Azure PowerShell als Administrator sowie folgenden Befehl aus, um ein neues Azure-Dienstprojekt zu erstellen:

```powershell
PS C:\>New-AzureServiceProject myProject
```

Durch diesen Befehl wird ein neues Verzeichnis (`myProject`) erstellt, dem Sie Web- und Workerrollen hinzufügen können.

## <a name="add-php-web-or-worker-roles"></a>Hinzufügen von PHP-Web- und Workerrollen

Führen Sie folgenden Befehl im Stammverzeichnis des Projekts aus, um eine PHP-Webrolle zu einem Projekt hinzuzufügen:

```powershell
PS C:\myProject> Add-AzurePHPWebRole roleName
```

Für eine Workerrolle verwenden Sie diesen Befehl:

```powershell
PS C:\myProject> Add-AzurePHPWorkerRole roleName
```

> [!NOTE]
> Das `roleName` ist optional. Wenn er nicht angegeben wird, wird automatisch ein Name generiert. Die erste erstellte Webrolle heißt `WebRole1`, die zweite `WebRole2` usw. Die erste erstellte Workerrolle heißt `WorkerRole1`, die zweite `WorkerRole2` usw.
>
>

## <a name="use-your-own-php-runtime"></a>Verwenden Ihrer eigenen PHP-Laufzeit

In einigen Fällen möchten Sie evtl. eine eigene PHP-Laufzeit angeben, anstatt eine integrierte PHP-Laufzeit auszuwählen und diese wie oben beschrieben zu konfigurieren. Sie können beispielsweise dieselbe PHP-Laufzeit in einer Web- oder Workerrolle verwenden, die Sie auch in Ihrer Entwicklungsumgebung verwenden. So lässt sich leichter sicherstellen, dass die Anwendung ihr Verhalten in der Produktionsumgebung nicht ändert.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Konfigurieren einer Webrolle für die Verwendung Ihrer eigenen PHP-Laufzeit

Führen Sie die folgenden Schritte aus, um eine Webrolle für die Verwendung einer von Ihnen angegebenen PHP-Laufzeit zu konfigurieren.

1. Erstellen Sie ein Azure-Dienstprojekt, und fügen Sie eine PHP-Webrolle hinzu, wie weiter oben in diesem Thema beschrieben.
2. Erstellen Sie einen Ordner `php` im Ordner `bin`, der sich im Stammverzeichnis der Webrolle befindet, und fügen Sie dann die PHP-Laufzeit (alle Binärdateien, Konfigurationsdateien, Unterordner usw.) dem Ordner `php` hinzu.
3. (OPTIONAL) Wenn Ihre PHP-Runtime [Microsoft-Treiber für PHP für SQL Server][sqlsrv drivers] verwendet, müssen Sie die Webrolle bei der Bereitstellung für die Installation von [SQL Server Native Client 2012][sql native client] konfigurieren. Fügen Sie dazu den [sqlncli.msi x64-Installer] dem Ordner `bin` im Stammverzeichnis der Webrolle hinzu. Das im nächsten Schritt beschriebene Startskript wird das Installationsprogramm ausführen, wenn die Rolle bereitgestellt wird. Wenn Ihre PHP-Laufzeit nicht Microsoft-Treiber für PHP für SQL Server verwendet, können Sie folgende Zeile aus dem im nächsten Schritt gezeigten Skript entfernen:

   ```console
   msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
   ```

4. Definieren Sie eine Startaufgabe, die [Internet Information Services (IIS)][iis.net] für die Verwendung Ihrer PHP-Runtime konfiguriert, um Anforderungen für `.php`-Seiten zu behandeln. Öffnen Sie dazu die Datei `setup_web.cmd` (in der Datei `bin` des Stammverzeichnisses Ihrer Webrolle) in einem Texteditor, und ersetzen Sie den Inhalt durch folgendes Skript:

    ```cmd
    @ECHO ON
    cd "%~dp0"

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
    SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"
    ```
5. Fügen Sie die Anwendungsdateien zum Stammverzeichnis Ihrer Webrolle hinzu. Dies wird das Stammverzeichnis des Webservers sein.
6. Veröffentlichen Sie die Anwendung entsprechend der Beschreibung im Abschnitt [Veröffentlichen der Anwendung](#publish-your-application) weiter unten.

> [!NOTE]
> Das Skript `download.ps1` (im Ordner `bin` des Stammverzeichnisses der Webrolle) kann gelöscht werden, nachdem Sie die oben beschriebenen Schritte für die Verwendung Ihrer eigenen PHP-Laufzeit ausgeführt haben.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Konfigurieren einer Workerrolle für die Verwendung Ihrer eigenen PHP-Laufzeit

Führen Sie die folgenden Schritte aus, um eine Workerrolle für die Verwendung einer von Ihnen angegebenen PHP-Laufzeit zu konfigurieren.

1. Erstellen Sie ein Azure-Dienstprojekt, und fügen Sie eine PHP-Workerrolle hinzu, wie weiter oben in diesem Thema beschrieben.
2. Erstellen Sie einen Ordner namens `php` im Stammverzeichnis der Workerrolle, und fügen Sie dann Ihre PHP-Laufzeit (alle Binärdateien, Konfigurationsdateien, Unterordner usw.) dem Ordner `php` hinzu.
3. (OPTIONAL) Wenn Ihre PHP-Runtime [Microsoft-Treiber für PHP für SQL Server][sqlsrv drivers] verwendet, müssen Sie die Workerrolle bei der Bereitstellung für die Installation von [SQL Server Native Client 2012][sql native client] konfigurieren. Fügen Sie dazu den [sqlncli.msi x64-Installer] dem Stammverzeichnis der Workerrolle hinzu. Das im nächsten Schritt beschriebene Startskript wird das Installationsprogramm ausführen, wenn die Rolle bereitgestellt wird. Wenn Ihre PHP-Laufzeit nicht Microsoft-Treiber für PHP für SQL Server verwendet, können Sie folgende Zeile aus dem im nächsten Schritt gezeigten Skript entfernen:

   ```console
   msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
   ```

4. Definieren Sie eine Startaufgabe, die die ausführbare Datei `php.exe` bei der Rollenbereitstellung zur PATH-Umgebungsvariable der Workerrolle hinzufügt. Öffnen Sie dazu die Datei `setup_worker.cmd` (im Stammverzeichnis der Workerrolle) in einem Texteditor, und ersetzen Sie den Inhalt durch folgendes Skript:

    ```cmd
    @echo on

    cd "%~dp0"

    echo Granting permissions for Network Service to the web root directory...
    icacls ..\ /grant "Network Service":(OI)(CI)W
    if %ERRORLEVEL% neq 0 goto error
    echo OK

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    setx Path "%PATH%;%~dp0php" /M

    if %ERRORLEVEL% neq 0 goto error

    echo SUCCESS
    exit /b 0

    :error

    echo FAILED
    exit /b -1
    ```
5. Fügen Sie die Anwendungsdateien zum Stammverzeichnis Ihrer Workerrolle hinzu.
6. Veröffentlichen Sie die Anwendung entsprechend der Beschreibung im Abschnitt [Veröffentlichen der Anwendung](#publish-your-application) weiter unten.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Ausführen der Anwendung in den Server- und Speicheremulatoren

Die Azure-Emulatoren bieten eine lokale Umgebung, in der Sie Ihre Azure-Anwendung testen können, bevor Sie sie in der Cloud bereitstellen. Es gibt einige Unterschiede zwischen den Emulatoren und der Azure-Umgebung. Zum besseren Verständnis finden Sie weitere Informationen unter [Einsatz des Azure-Speicheremulators für Entwicklung und Tests](storage/common/storage-use-emulator.md).

PHP muss lokal installiert sein, um den Serveremulator nutzen zu können. Der Serveremulator verwendet Ihre lokale PHP-Installation, um die Anwendung auszuführen.

Führen Sie folgenden Befehl im Stammverzeichnis des Projekts aus, um das Projekt in den Emulatoren auszuführen:

```powershell
PS C:\MyProject> Start-AzureEmulator
```

Eine Ausgabe ähnlich der folgenden wird angezeigt.

```output
Creating local package...
Starting Emulator...
Role is running at http://127.0.0.1:81
Started
```

Wenn Sie einen Webbrowser öffnen und zur lokalen Adresse in der Ausgabe (`http://127.0.0.1:81` in der Beispielausgabe oben) navigieren, können Sie im Emulator sehen, wie die Anwendung ausgeführt wird.

Führen Sie folgenden Befehl aus, um die Emulatoren zu stoppen:

```powershell
PS C:\MyProject> Stop-AzureEmulator
```

## <a name="publish-your-application"></a>Veröffentlichen der Anwendung

Um die Anwendung zu veröffentlichen, müssen Sie zunächst die Veröffentlichungseinstellungen mithilfe des Cmdlets [Import-AzurePublishSettingsFile](/powershell/module/servicemanagement/azure.service/import-azurepublishsettingsfile) importieren. Anschließend können Sie die Anwendung mithilfe des Cmdlets [Publish-AzureServiceProject](/powershell/module/servicemanagement/azure.service/publish-azureserviceproject) veröffentlichen. Informationen zum Anmelden finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie im [PHP Developer Center](https://azure.microsoft.com/develop/php/).

[install ps and emulators]: https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[Azure-Dienstdefinitionsschema (CSDEF-Datei)]: /previous-versions/azure/reference/ee758711(v=azure.100)
[Azure-Dienstkonfigurationsschema (CSCFG-Datei)]: /previous-versions/azure/reference/ee758710(v=azure.100)
[iis.net]: https://www.iis.net/
[sql native client]: /sql/sql-server/sql-server-technical-documentation
[sqlsrv drivers]: https://php.net/sqlsrv
[sqlncli.msi x64-Installer]: https://go.microsoft.com/fwlink/?LinkID=239648
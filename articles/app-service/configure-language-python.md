---
title: Konfigurieren von Linux-Python-Apps
description: Es wird beschrieben, wie Sie den Python-Container konfigurieren, in dem Web-Apps ausgeführt werden, indem Sie sowohl das Azure-Portal als auch die Azure CLI verwenden.
ms.topic: quickstart
ms.date: 11/16/2020
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 7589b5c66bf4fa86db243574f551ec585ccccea1
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855055"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Konfigurieren einer Linux-Python-App für Azure App Service

In diesem Artikel wird beschrieben, wie Python-Apps mit [Azure App Service](overview.md) ausgeführt werden und wie Sie vorhandene Apps zu Azure migrieren und das Verhalten von App Service bei Bedarf anpassen können. Python-Apps müssen mit allen erforderlichen [pip](https://pypi.org/project/pip/)-Modulen bereitgestellt werden.

Die App Service-Bereitstellungs-Engine aktiviert automatisch eine virtuelle Umgebung und führt `pip install -r requirements.txt` für Sie aus, wenn Sie ein [Git-Repository](deploy-local-git.md) oder ein [ZIP-Paket](deploy-zip.md) bereitstellen.

Dieser Leitfaden enthält wichtige Konzepte und Anleitungen für Python-Entwickler, die in App Service einen integrierten Linux-Container verwenden. Falls Sie Azure App Service noch nicht verwendet haben, ist es ratsam, zunächst die [Python-Schnellstartanleitung](quickstart-python.md) und das [Tutorial „Python mit PostgreSQL“](tutorial-python-postgresql-app.md) durchzuarbeiten.

Sie können für die Konfiguration entweder das [Azure-Portal](https://portal.azure.com) oder die Azure CLI verwenden:

- **Azure-Portal**: Verwenden Sie die Seite **Einstellungen** > **Konfiguration** der App wie unter [Konfigurieren einer App Service-App im Azure-Portal](configure-common.md) beschrieben.

- **Azure CLI**: Sie haben zwei Möglichkeiten.

    - Ausführen von Befehlen in der [Azure Cloud Shell](../cloud-shell/overview.md).
    - Führen Sie Befehle lokal aus, indem Sie die aktuelle Version der [Azure CLI](/cli/azure/install-azure-cli) installieren und sich dann mit dem Befehl [az login](/cli/azure/reference-index#az-login) bei Azure anmelden.
    
> [!NOTE]
> Linux ist derzeit die empfohlene Option zum Ausführen von Python-Apps in App Service. Informationen zur Windows-Option finden Sie unter [Einrichten einer Python-Umgebung in Azure App Service (Windows)](/visualstudio/python/managing-python-on-azure-app-service).

## <a name="configure-python-version"></a>Konfigurieren der Python-Version

- **Azure-Portal**: Verwenden Sie die Registerkarte **Allgemeine Einstellungen** auf der Seite **Konfiguration**. Dies ist unter [Konfigurieren allgemeiner Einstellungen](configure-common.md#configure-general-settings) für Linux-Container beschrieben.

- **Azure CLI**:

    -  Zeigen Sie die aktuelle Python-Version mit [az webapp config show](/cli/azure/webapp/config#az_webapp_config_show) an:
    
        ```azurecli
        az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
        ```
        
        Ersetzen Sie `<resource-group-name>` und `<app-name>` durch die entsprechenden Namen für Ihre Web-App.
    
    - Legen Sie die Python-Version mit [az webapp config set](/cli/azure/webapp/config#az_webapp_config_set) fest.
        
        ```azurecli
        az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
        ```
    
    - Zeigen Sie alle Python-Versionen an, die in Azure App Service unterstützt werden, indem Sie [az webapp list-runtimes](/cli/azure/webapp#az_webapp_list_runtimes) verwenden:
    
        ```azurecli
        az webapp list-runtimes --linux | grep PYTHON
        ```
    
Sie können auch eine nicht unterstützte Version von Python ausführen, indem Sie stattdessen Ihr eigenes Containerimage erstellen. Weitere Informationen finden Sie unter [Verwenden eines benutzerdefinierten Docker-Images für Web-App für Container](tutorial-custom-container.md?pivots=container-linux).

<!-- <a> element here to preserve external links-->
<a name="access-environment-variables"></a>

## <a name="customize-build-automation"></a>Anpassen der Buildautomatisierung

Mit dem Buildsystem von App Service (Bezeichnung: Oryx) werden die folgenden Schritte ausgeführt, wenn Sie Ihre App per Git oder mit ZIP-Paketen bereitstellen:

1. Führen Sie ein benutzerdefiniertes Präbuildskript aus, sofern dies über die Einstellung `PRE_BUILD_COMMAND` angegeben ist.
1. Führen Sie `pip install -r requirements.txt` aus. Die Datei *requirements.txt* muss im Stammordner des Projekts enthalten sein. Andernfalls wird vom Buildprozess ein Fehler der folgenden Art gemeldet: „setup.py oder requirements.txt nicht gefunden. pip-Installation wird nicht ausgeführt.“
1. Wenn sich *manage.py* im Stammverzeichnis des Repositorys befindet (Hinweis auf Django-App), wird *manage.py collectstatic* ausgeführt. Wenn die Einstellung `DISABLE_COLLECTSTATIC` aber auf `true` festgelegt ist, wird dieser Schritt übersprungen.
1. Führen Sie ein benutzerdefiniertes Postbuildskript aus, sofern dies über die Einstellung `POST_BUILD_COMMAND` angegeben ist.

Standardmäßig sind die Einstellungen für `PRE_BUILD_COMMAND`, `POST_BUILD_COMMAND` und `DISABLE_COLLECTSTATIC` leer. 

- Wenn Sie bei der Erstellung von Django-Apps die Ausführung von „collectstatic“ deaktivieren möchten, müssen Sie `DISABLE_COLLECTSTATIC` auf „true“ festlegen.

- Legen Sie zum Ausführen von Präbuildbefehlen die Einstellung `PRE_BUILD_COMMAND` so fest, dass sie entweder einen Befehl, z. B. `echo Pre-build command`, oder einen Pfad zu einer Skriptdatei, z. B. `scripts/prebuild.sh`, relativ zu Ihrem Projektstamm enthält. Für alle Befehle müssen relative Pfade zum Stammordner des Projekts verwendet werden.

- Legen Sie zum Ausführen von Postbuildbefehlen die Einstellung `POST_BUILD_COMMAND` so fest, dass sie entweder einen Befehl, z. B. `echo Post-build command`, oder einen Pfad zu einer Skriptdatei, z. B. `scripts/postbuild.sh`, relativ zu Ihrem Projektstamm enthält. Für alle Befehle müssen relative Pfade zum Stammordner des Projekts verwendet werden.

Weitere Einstellungen zum Anpassen der Buildautomatisierung finden Sie unter [Oryx-Konfiguration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md). 

Informationen zum Zugriff auf die Build- und Bereitstellungsprotokolle finden Sie unter [Zugreifen auf Bereitstellungsprotokolle](#access-deployment-logs).

Weitere Informationen zur Ausführung und Erstellung von Python-Apps unter Linux durch App Service finden Sie unter [Erkennung und Erstellung von Python-Apps durch Oryx](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/python.md).

> [!NOTE]
> Die Einstellungen `PRE_BUILD_SCRIPT_PATH` und `POST_BUILD_SCRIPT_PATH` sind mit `PRE_BUILD_COMMAND` und `POST_BUILD_COMMAND` identisch und werden aus Legacygründen unterstützt.
> 
> Eine Einstellung mit dem Namen `SCM_DO_BUILD_DURING_DEPLOYMENT` löst (sofern sie `true` oder 1 enthält) einen Oryx-Buildvorgang während der Bereitstellung aus. Die Einstellung ist auf „true“ festgelegt, wenn die Bereitstellung mit git, dem Azure CLI-Befehl `az webapp up` und Visual Studio Code durchgeführt wird.

> [!NOTE]
> Verwenden Sie in allen Prä- und Postbuildskripts immer relative Pfade, da sich der Buildcontainer, in dem Oryx ausgeführt wird, von dem Runtimecontainer unterscheidet, in dem die App ausgeführt wird. Verlassen Sie sich niemals auf die genaue Platzierung Ihres App-Projektordners im Container (z. B. die Platzierung unter *site/wwwroot*).

## <a name="migrate-existing-applications-to-azure"></a>Migrieren von vorhandenen Anwendungen zu Azure

Vorhandene Webanwendungen können in Azure wie folgt erneut bereitgestellt werden:

1. **Quellrepository**: Verwalten Sie Ihren Quellcode in einem geeigneten Repository wie GitHub, damit Sie zu einem späteren Zeitpunkt dieses Prozesses Continuous Deployment einrichten können.
    1. Ihre Datei *requirements.txt* muss sich im Stammverzeichnis Ihres Repositorys für App Service befinden, damit die erforderlichen Pakete automatisch installiert werden können.    

1. **Datenbank**: Falls Ihre App von einer Datenbank abhängig ist, sollten Sie die erforderlichen Ressourcen auch in Azure bereitstellen. Siehe [Tutorial: Bereitstellen einer Django-Web-App mit PostgreSQL in Azure App Service: Erstellen einer Postgres-Datenbankinstanz in Azure](tutorial-python-postgresql-app.md#3-create-postgres-database-in-azure), um ein Beispiel anzuzeigen.

1. **App Service-Ressourcen**: Erstellen Sie eine Ressourcengruppe, einen App Service-Plan und eine App Service-Web-App zum Hosten Ihrer Anwendung. Dies lässt sich am einfachsten erreichen, indem Sie eine anfängliche Bereitstellung Ihres Codes über den Azure CLI-Befehl `az webapp up` verwenden. Dies wird unter [Tutorial: Bereitstellen einer Django-Web-App mit PostgreSQL in Azure App Service: Bereitstellen des Codes in Azure App Service](tutorial-python-postgresql-app.md#4-deploy-the-code-to-azure-app-service) veranschaulicht. Ersetzen Sie die Namen der Ressourcengruppe, des App Service-Plans und der Web-App durch Namen, die für Ihre Anwendung besser geeignet sind.

1. **Environment variables** (Umgebungsvariablen): Falls für Ihre Anwendung Umgebungsvariablen benötigt werden, müssen Sie entsprechende [App Service-Anwendungseinstellungen](configure-common.md#configure-app-settings) erstellen. Diese App Service-Einstellungen werden für Ihren Code als Umgebungsvariablen angezeigt. Dies ist im Abschnitt mit den Informationen zum [Zugreifen auf Umgebungsvariablen](#access-app-settings-as-environment-variables) beschrieben.
    - Datenbankverbindungen werden beispielsweise häufig mit Einstellungen dieser Art verwaltet. Dis wird unter [Tutorial: Bereitstellen einer Django-Web-App mit PostgreSQL in Azure App Service: Konfigurieren der Umgebungsvariablen für die Datenbankverbindung](tutorial-python-postgresql-app.md#42-configure-environment-variables-to-connect-the-database) veranschaulicht.
    - Informationen zu spezifischen Einstellungen für typische Django-Apps finden Sie unter [Produktionseinstellungen für Django-Apps](#production-settings-for-django-apps).

1. **App-Start**: Lesen Sie den Abschnitt [Startprozess des Containers](#container-startup-process) weiter unten in diesem Artikel, um sich damit vertraut zu machen, wie App Service versucht, Ihre App auszuführen. Von App Service wird standardmäßig der Gunicorn-Webserver verwendet. Dieser muss dazu in der Lage sein, auf Ihr App-Objekt oder den Ordner *wsgi.py* zuzugreifen. Bei Bedarf können Sie den [Startbefehl anpassen](#customize-startup-command).

1. **Continuous Deployment:** Richten Sie Continuous Deployment so ein, wie dies unter [Continuous Deployment in Azure App Service](deploy-continuous-deployment.md) beschrieben ist, wenn Sie die Azure Pipelines- oder Kudu-Bereitstellung verwenden. Bei Verwendung von GitHub Actions helfen Ihnen die Informationen unter [Bereitstellen in App Service mithilfe von GitHub Actions](deploy-github-actions.md) weiter.

1. **Benutzerdefinierte Aktionen**: Zum Durchführen von Aktionen in dem App Service-Container, von dem Ihre App gehostet wird, z. B. Django-Datenbankmigrationen, können Sie eine [Verbindung mit dem Container per SSH herstellen](configure-linux-open-ssh-session.md). Ein Beispiel für die Ausführung von Django-Datenbankmigrationen finden Sie unter [Tutorial: Bereitstellen einer Django-Web-App mit PostgreSQL in Azure App Service: Ausführen von Django-Datenbankmigrationen](tutorial-python-postgresql-app.md#43-run-django-database-migrations).
    - Bei Verwendung von Continuous Deployment können Sie diese Aktionen mit Postbuildbefehlen durchführen. Dies ist weiter oben unter [Anpassen der Buildautomatisierung](#customize-build-automation) beschrieben.

Nachdem Sie diese Schritte abgeschlossen haben, sollten Sie Änderungen für Ihr Quellrepository committen können, und diese Aktualisierungen sollten dann automatisch in App Service bereitgestellt werden.

### <a name="production-settings-for-django-apps"></a>Produktionseinstellungen für Django-Apps

Für eine Produktionsumgebung wie Azure App Service sollten bei Django-Apps die Vorgaben in der [Bereitstellungsprüfliste](https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/) von Django (djangoproject.com) erfüllt werden.

In der folgenden Tabelle sind die Produktionseinstellungen beschrieben, die für Azure relevant sind. Diese Einstellungen werden in der Datei *setting.py* der App definiert.

| Django-Einstellung | Anleitung für Azure |
| --- | --- |
| `SECRET_KEY` | Speichern Sie den Wert in einer App Service-Einstellung, wie dies unter [Zugreifen auf App-Einstellungen als Umgebungsvariablen](#access-app-settings-as-environment-variables) beschrieben ist. Alternativ können Sie den [Wert in Azure Key Vault als „Geheimnis“ speichern](../key-vault/secrets/quick-create-python.md). |
| `DEBUG` | Erstellen Sie in App Service die Einstellung `DEBUG` mit dem Wert 0 (false), und laden Sie den Wert dann als Umgebungsvariable. Erstellen Sie in Ihrer Entwicklungsumgebung die Umgebungsvariable `DEBUG` mit dem Wert 1 (true). |
| `ALLOWED_HOSTS` | In der Produktion ist es für Django erforderlich, dass Sie die URL der App im Array `ALLOWED_HOSTS` von *settings.py* einfügen. Sie können diese URL zur Laufzeit mit dem Code `os.environ['WEBSITE_HOSTNAME']` abrufen. Von App Service wird die Umgebungsvariable `WEBSITE_HOSTNAME` automatisch auf die URL der App festgelegt. |
| `DATABASES` | Definieren Sie Einstellungen in App Service für die Datenbankverbindung, und laden Sie sie als Umgebungsvariablen, um das Wörterbuch [`DATABASES`](https://docs.djangoproject.com/en/3.1/ref/settings/#std:setting-DATABASES) aufzufüllen. Sie können diese Werte (vor allem Benutzername und Kennwort) auch als [Azure Key Vault-Geheimnisse](../key-vault/secrets/quick-create-python.md) speichern. |

## <a name="container-characteristics"></a>Eigenschaften von Containern

Bei der Bereitstellung in App Service werden Python-Apps in einem Linux-Docker-Container ausgeführt, der im [GitHub-Repository für Python in App Service](https://github.com/Azure-App-Service/python) definiert ist. Sie finden Imagekonfigurationen in den versionsspezifischen Verzeichnissen.

Dieser Container verfügt über die folgenden Eigenschaften:

- Apps werden per [Gunicorn WSGI HTTP Server](https://gunicorn.org/) mit den zusätzlichen Argumenten `--bind=0.0.0.0 --timeout 600` ausgeführt.
    - Sie können Konfigurationseinstellungen für Gunicorn über die Datei *gunicorn.conf.py* im Projektstamm angeben. Dies ist in der [Übersicht zur Gunicorn-Konfiguration](https://docs.gunicorn.org/en/stable/configure.html#configuration-file) (docs.gunicorn.org) beschrieben. Alternativ können Sie den [Startbefehl anpassen](#customize-startup-command).

    - Um Ihre Web-App vor versehentlichen oder absichtlichen DDoS-Angriffen zu schützen, wird Gunicorn hinter einem Nginx-Reverseproxy ausgeführt. Dies ist unter [Bereitstellen von Gunicorn](https://docs.gunicorn.org/en/latest/deploy.html) (docs.gunicorn.org) beschrieben.

- Standardmäßig enthält das Basiscontainerimage nur das Flask-Webframework, aber der Container unterstützt auch andere Frameworks, die WSGI-konform und mit Python 3.6 oder höher kompatibel sind, z. B. Django.

- Erstellen Sie zum Installieren von zusätzlichen Paketen, z. B. Django, im Stamm Ihres Projekts die Datei [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files), in der Ihre direkten Abhängigkeiten angegeben sind. App Service installiert diese Abhängigkeiten dann automatisch, wenn Sie Ihr Projekt bereitstellen.

    Die Datei *requirements.txt* *muss* sich im Projektstamm befinden, damit die Abhängigkeiten installiert werden. Andernfalls wird vom Buildprozess ein Fehler der folgenden Art gemeldet: „setup.py oder requirements.txt nicht gefunden. pip-Installation wird nicht ausgeführt.“ Wenn dieser Fehler auftritt, sollten Sie den Speicherort Ihrer Anforderungsdatei überprüfen.

- App Service definiert automatisch eine Umgebungsvariable mit dem Namen `WEBSITE_HOSTNAME` und der URL der Web-App, z. B. `msdocs-hello-world.azurewebsites.net`. Außerdem wird `WEBSITE_SITE_NAME` mit dem Namen Ihrer App definiert, z. B. `msdocs-hello-world`. 
   
## <a name="container-startup-process"></a>Startprozess des Containers

Beim Starten führt der Container vom Typ „App Service für Linux“ die folgenden Schritte aus:

1. Verwenden eines [benutzerdefinierten Startbefehls](#customize-startup-command) (sofern vorhanden)
2. Überprüfen, ob eine [Django-App](#django-app) vorhanden ist, und Starten von Gunicorn für die ggf. erkannte App
3. Überprüfen, ob eine [Flask-App](#flask-app) vorhanden ist, und Starten von Gunicorn für die ggf. erkannte App
4. Wenn keine andere App gefunden wird, können Sie eine Standard-App starten, die im Container integriert ist.

Die folgenden Abschnitte enthalten weitere Details zu den einzelnen Optionen.

### <a name="django-app"></a>Django-App

Für Django-Apps sucht App Service in Ihrem App-Code nach der Datei `wsgi.py` und führt dann mit dem folgenden Befehl Gunicorn aus:

```bash
# <module> is the name of the folder that contains wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Gehen Sie wie folgt vor, falls Sie eine präzisere Kontrolle über den Startbefehl benötigen: Verwenden Sie einen [benutzerdefinierten Startbefehl](#customize-startup-command), ersetzen Sie `<module>` durch den Namen des Ordners, der *wsgi.py* enthält, und fügen Sie das Argument `--chdir` hinzu, falls sich dieses Modul nicht im Projektstamm befindet. Verwenden Sie beispielsweise die Argumente `--chdir knboard/backend config.wsgi`, wenn sich die Datei *wsgi.py* bei Ihnen unter *knboard/backend/config* im Projektstamm befindet.

Fügen Sie zum Aktivieren der Produktionsprotokollierung die Parameter `--access-logfile` und `--error-logfile` so hinzu, wie dies in den Beispielen für [benutzerdefinierte Startbefehle](#customize-startup-command) veranschaulicht wird.

### <a name="flask-app"></a>Flask-App

Für Flask sucht App Service nach der Datei *application.py* oder *app.py* und startet Gunicorn:

```bash
# If application.py
gunicorn --bind=0.0.0.0 --timeout 600 application:app

# If app.py
gunicorn --bind=0.0.0.0 --timeout 600 app:app
```

Falls Ihr App-Hauptmodul in einer anderen Datei enthalten ist, sollten Sie einen anderen Namen für das App-Objekt verwenden. Wenn Sie zusätzliche Argumente für Gunicorn angeben möchten, können Sie einen [benutzerdefinierten Startbefehl](#customize-startup-command) nutzen.

### <a name="default-behavior"></a>Standardverhalten

Wenn App Service keinen benutzerdefinierten Befehl und keine Django-App oder Flask-App findet, wird eine schreibgeschützte Standard-App ausgeführt, die sich im Ordner _opt/defaultsite_ befindet (siehe folgende Abbildung).

Wenn Sie Code bereitgestellt haben und die Standard-App weiterhin angezeigt wird, finden Sie Informationen unter [Problembehandlung: Die App wird nicht angezeigt](#app-doesnt-appear).

[![Standardwebseite für App Service für Linux](media/configure-language-python/default-python-app.png)](#app-doesnt-appear)

Erneut, wenn Sie erwarten, dass eine bereitgestellte App anstelle der Standard-App angezeigt wird, finden Sie Informationen unter [Problembehandlung: Die App wird nicht angezeigt](#app-doesnt-appear).

## <a name="customize-startup-command"></a>Anpassen des Startbefehls

Wie in diesem Artikel bereits erwähnt, können Sie Konfigurationseinstellungen für Gunicorn über die Datei *gunicorn.conf.py* im Projektstamm angeben. Dies ist in der [Übersicht zur Gunicorn-Konfiguration](https://docs.gunicorn.org/en/stable/configure.html#configuration-file) beschrieben.

Wenn diese Konfiguration nicht ausreicht, können Sie das Startverhalten des Containers steuern, indem Sie entweder einen benutzerdefinierten Startbefehl oder mehrere Befehle in einer Datei mit Startbefehlen angeben. Für eine Datei mit Startbefehlen können Sie einen beliebigen Namen wählen, z. B. *startup.sh*, *startup.cmd*, *startup.txt* usw.

Für alle Befehle müssen relative Pfade zum Stammordner des Projekts verwendet werden.

Geben Sie einen Startbefehl oder eine Befehlsdatei wie folgt an:

- **Azure-Portal**: Wählen Sie die Seite **Konfiguration** der App und dann die Option **Allgemeine Einstellungen** aus. Geben Sie im Feld **Startbefehl** entweder den vollständigen Text Ihres Startbefehls oder den Namen Ihrer Startbefehlsdatei an. Klicken Sie anschließend auf **Speichern**, um die Änderungen zu übernehmen. Weitere Informationen finden Sie unter [Konfigurieren von allgemeinen Einstellungen](configure-common.md#configure-general-settings) für Linux-Container.

- **Azure CLI**: Verwenden Sie den Befehl [az webapp config set](/cli/azure/webapp/config#az_webapp_config_set) mit dem Parameter `--startup-file`, um den Startbefehl bzw. die Datei festzulegen:

    ```azurecli
    az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
    ```
        
    Ersetzen Sie `<custom-command>` entweder durch den vollständigen Text Ihres Startbefehls oder durch den Namen Ihrer Startbefehlsdatei.
        
App Service ignoriert alle Fehler, die beim Verarbeiten eines benutzerdefinierten Startbefehls oder einer Befehlsdatei auftreten, und setzt den Startprozess dann fort, indem nach Django- und Flask-Apps gesucht wird. Falls das erwartete Verhalten nicht eintritt, sollten Sie überprüfen, ob Ihr Startbefehl bzw. die Datei fehlerfrei ist und ob zusammen mit Ihrem App-Code eine Startbefehlsdatei für App Service bereitgestellt wird. Weitere Informationen finden Sie auch in den [Diagnoseprotokollen](#access-diagnostic-logs). Überprüfen Sie auch die Seite **Diagnose und Problembehandlung** der App im [Azure-Portal](https://portal.azure.com).

### <a name="example-startup-commands"></a>Beispiele für Startbefehle

- **Hinzugefügte Gunicorn-Argumente**: Im folgenden Beispiel wird `--workers=4` einer Gunicorn-Befehlszeile hinzugefügt, um eine Django-App zu starten: 

    ```bash
    # <module-path> is the relative path to the folder that contains the module
    # that contains wsgi.py; <module> is the name of the folder containing wsgi.py.
    gunicorn --bind=0.0.0.0 --timeout 600 --workers=4 --chdir <module_path> <module>.wsgi
    ```    

    Weitere Informationen finden Sie unter [Ausführen von Gunicorn](https://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org).

- **Aktivieren der Produktionsprotokollierung für Django**: Fügen Sie der Befehlszeile die Argumente `--access-logfile '-'` und `--error-logfile '-'` hinzu:

    ```bash    
    # '-' for the log files means stdout for --access-logfile and stderr for --error-logfile.
    gunicorn --bind=0.0.0.0 --timeout 600 --workers=4 --chdir <module_path> <module>.wsgi --access-logfile '-' --error-logfile '-'
    ```    

    Diese Protokolle werden im [App Service-Protokolldatenstrom](#access-diagnostic-logs) angezeigt.

    Weitere Informationen finden Sie unter [Gunicorn-Protokollierung](https://docs.gunicorn.org/en/stable/settings.html#logging) (docs.gunicorn.org).
    
- **Benutzerdefiniertes Flask-Hauptmodul**: Standardmäßig wird von App Service angenommen, dass das Hauptmodul einer Flask-App *application.py* oder *app.py* lautet. Falls für Ihr Hauptmodul ein anderer Name verwendet wird, müssen Sie den Startbefehl anpassen. Wenn Sie beispielsweise über eine Flask-App verfügen, deren Hauptmodul *hello.py* lautet, und das Flask-App-Objekt in dieser Datei den Namen `myapp` hat, lautet der Befehl wie folgt:

    ```bash
    gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
    ```
    
    Wenn Ihr Hauptmodul in einem Unterordner, z. B. `website` enthalten ist, können Sie diesen Ordner mit dem Argument `--chdir` angeben:
    
    ```bash
    gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
    ```
    
- **Verwenden eines anderen Servers als eines Gunicorn-Servers**: Verwenden Sie für die Nutzung eines anderen Webservers, z. B. [aiohttp](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html), den entsprechenden Befehl als Startbefehl oder in der Startbefehlsdatei:

    ```bash
    python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
    ```

## <a name="access-app-settings-as-environment-variables"></a>Zugreifen auf App-Einstellungen als Umgebungsvariablen

App-Einstellungen sind Werte, die in der Cloud speziell für Ihre App gespeichert werden. Dies ist unter [Konfigurieren von App-Einstellungen](configure-common.md#configure-app-settings) beschrieben. Diese Einstellungen sind für Ihren App-Code als Umgebungsvariablen verfügbar, und der Zugriff darauf erfolgt mit dem Standardmuster [os.environ](https://docs.python.org/3/library/os.html#os.environ).

Wenn Sie beispielsweise eine App-Einstellung mit dem Namen `DATABASE_SERVER` erstellt haben, wird der Wert dieser Einstellung mit dem folgenden Code abgerufen:

```python
db_server = os.environ['DATABASE_SERVER']
```
    
## <a name="detect-https-session"></a>Erkennen einer HTTPS-Sitzung

In App Service erfolgt die [SSL-Terminierung](https://wikipedia.org/wiki/TLS_termination_proxy) (wikipedia.org) in den Modulen für den Netzwerklastenausgleich, sodass alle HTTPS-Anforderungen Ihre App als unverschlüsselte HTTP-Anforderungen erreichen. Wenn Ihre App-Logik überprüfen muss, ob Benutzeranforderungen verschlüsselt sind, können Sie dazu den Header `X-Forwarded-Proto` untersuchen.

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

Gängige Webframeworks ermöglichen den Zugriff auf die Information `X-Forwarded-*` in Ihrem App-Standardmuster. In [CodeIgniter](https://codeigniter.com/) überprüft [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) standardmäßig den Wert von `X_FORWARDED_PROTO`.

## <a name="access-diagnostic-logs"></a>Zugreifen auf Diagnoseprotokolle

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

Wählen Sie im Menü auf der linken Seite für Ihre App die Option **Überwachung** > **Protokolldatenstrom** aus, um über das Azure-Portal auf Protokolle zuzugreifen.

## <a name="access-deployment-logs"></a>Zugriff auf Bereitstellungsprotokolle

Wenn Sie Ihren Code bereitstellen, führt App Service den Buildprozess aus, der weiter oben im Abschnitt [Anpassen der Buildautomatisierung](#customize-build-automation) beschrieben wird. Da der Build in seinem eigenen Container ausgeführt wird, werden Buildprotokolle getrennt von den Diagnoseprotokollen der App gespeichert.

Führen Sie die folgenden Schritte aus, um auf die Bereitstellungsprotokolle zuzugreifen:

1. Wählen Sie im Azure-Portal für Ihre Web-App im linken Menü **Bereitstellung** > **Bereitstellungscenter (Vorschau)** aus.
1. Wählen Sie auf der Registerkarte **Protokolle** die **Commit-ID** für den letzten Commit aus.
1. Wählen Sie auf der angezeigten Seite **Protokolldetails** den Link **Protokolle anzeigen...** aus, der neben „Oryx-Build wird ausgeführt...“ angezeigt wird.

Buildprobleme, wie z. B. falsche Abhängigkeiten in *requirements.txt* und Fehler in Prä- oder Postbuildskripts werden in diesen Protokollen angezeigt. Fehler werden auch angezeigt, wenn Ihre Anforderungsdatei nicht exakt *requirements.txt* heißt oder nicht im Stammordner Ihres Projekts angezeigt wird.

## <a name="open-ssh-session-in-browser"></a>Öffnen einer SSH-Sitzung im Browser

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

Wenn Sie erfolgreich eine Verbindung mit der SSH-Sitzung hergestellt haben, sollte am unteren Rand des Fensters die Meldung „SSH-VERBINDUNG HERGESTELLT“ angezeigt werden. Wenn Fehler wie „SSH_CONNECTION_CLOSED“ oder eine Meldung, dass der Container neu gestartet wird, angezeigt werden, verhindert möglicherweise ein Fehler das Starten des App-Containers. Schritte zur Untersuchung möglicher Probleme finden Sie unter [Problembehandlung](#troubleshooting).

## <a name="troubleshooting"></a>Problembehandlung

Im Allgemeinen besteht der erste Schritt bei der Problembehandlung in der Verwendung der App Service-Diagnose:

1. Wählen Sie im Azure-Portal für Ihre Web-App im linken Menü **Diagnose und Problembehandlung** aus.
1. Wählen Sie **Verfügbarkeit und Leistung** aus.
1. Überprüfen Sie die Informationen in den Optionen **Anwendungsprotokolle**, **Containerabsturz** und **Containerprobleme** daraufhin, wo die häufigsten Probleme angezeigt werden.

Überprüfen Sie als Nächstes die [Bereitstellungsprotokolle](#access-deployment-logs) und die [App-Protokolle](#access-diagnostic-logs) auf Fehlermeldungen. Diese Protokolle identifizieren häufig bestimmte Probleme, die die App-Bereitstellung oder den App-Start verhindern können. Beispielsweise kann der Build fehlschlagen, wenn Ihre Datei *requirements.txt* den falschen Dateinamen aufweist oder nicht im Stammordner Ihres Projekts vorhanden ist.

Die folgenden Abschnitte bieten weitere Anleitungen zu spezifischen Problemen.

- [App wird nicht angezeigt: Standard-App wird angezeigt](#app-doesnt-appear)
- [App wird nicht angezeigt: Meldung „Dienst nicht verfügbar“](#service-unavailable)
- [„setup.py“ oder „requirements.txt“ wurde nicht gefunden](#could-not-find-setuppy-or-requirementstxt)
- [Kennwörter werden bei der Eingabe in der SSH-Sitzung nicht angezeigt](#other-issues)
- [Befehle in der SSH-Sitzung scheinen abgeschnitten zu werden](#other-issues)
- [Statische Objekte werden in einer Django-App nicht angezeigt](#other-issues)
- [Schwerwiegende SSL-Verbindung ist erforderlich](#other-issues)

#### <a name="app-doesnt-appear"></a>App wird nicht angezeigt

- **Die Standard-App wird angezeigt, nachdem Sie Ihren eigenen App-Code bereitgestellt haben.** Die [Standard-App](#default-behavior) wird angezeigt, da Sie entweder Ihren App-Code nicht in App Service bereitgestellt haben oder App Service Ihren App-Code nicht finden konnte und stattdessen die Standard-App ausgeführt hat.

    - Starten Sie App Service neu, warten Sie 15 bis 20 Sekunden, und prüfen Sie die App dann erneut.
    
    - Stellen Sie sicher, dass Sie App Service für Linux verwenden (keine Windows-basierte Instanz). Führen Sie über die Azure CLI den Befehl `az webapp show --resource-group <resource-group-name> --name <app-name> --query kind` aus, und ersetzen Sie jeweils `<resource-group-name>` und `<app-name>`. `app,linux` sollte als Ausgabe angezeigt werden. Erstellen Sie andernfalls die App Service-Instanz neu, und wählen Sie Linux aus.
    
    - Verwenden Sie [SSH](#open-ssh-session-in-browser), um eine direkte Verbindung mit dem App Service-Container herzustellen, und stellen Sie sicher, dass Ihre Dateien unter *site/wwwroot* vorhanden sind. Wenn Ihre Dateien nicht vorhanden sind, führen Sie die folgenden Schritte aus:
      1. Erstellen Sie eine App-Einstellung namens `SCM_DO_BUILD_DURING_DEPLOYMENT` mit dem Wert 1, stellen Sie Ihren Code erneut bereit, warten Sie ein paar Minuten, und versuchen Sie dann noch mal, auf die App zuzugreifen. Weitere Informationen zum Erstellen von App-Einstellungen finden Sie unter [Konfigurieren einer App Service-App im Azure-Portal](configure-common.md).
      1. Überprüfen Sie Ihren Bereitstellungsprozess, [überprüfen Sie die Bereitstellungsprotokolle](#access-deployment-logs), korrigieren Sie alle Fehler, und stellen Sie die App erneut bereit.
    
    - Wenn Ihre Dateien vorhanden sind, konnte App Service Ihre spezifische Startdatei nicht identifizieren. Überprüfen Sie, ob Ihre App so strukturiert ist, wie App Service dies für [Django](#django-app) oder [Flask](#flask-app) erwartet, oder verwenden Sie einen [benutzerdefinierten Startbefehl](#customize-startup-command).

- <a name="service-unavailable"></a>**Im Browser wird die Meldung „Dienst nicht verfügbar“ angezeigt.** Für den Browser ist ein Timeout aufgetreten, während auf eine Antwort von App Service gewartet wurde. Dies weist darauf hin, dass App Service den Gunicorn-Server gestartet hat, aber dass die App selbst nicht gestartet wurde. Diese Bedingung könnte darauf hindeuten, dass die Gunicorn-Argumente falsch sind oder dass ein Fehler im App-Code vorliegt.

    - Aktualisieren Sie den Browser. Dies gilt besonders, wenn Sie in Ihrem App Service-Plan die niedrigsten Tarife nutzen. Bei Verwendung von Free-Tarifen kann das Starten der App beispielsweise länger dauern. Sie reagiert dann wieder, nachdem Sie den Browser aktualisiert haben.

    - Überprüfen Sie, ob Ihre App so strukturiert ist, wie App Service dies für [Django](#django-app) oder [Flask](#flask-app) erwartet, oder verwenden Sie einen [benutzerdefinierten Startbefehl](#customize-startup-command).

    - Untersuchen Sie den [App-Protokolldatenstrom](#access-diagnostic-logs) auf Fehlermeldungen. In den Protokollen werden alle Fehler im App-Code angezeigt.

#### <a name="could-not-find-setuppy-or-requirementstxt"></a>„setup.py“ oder „requirements.txt“ wurde nicht gefunden

- **Für den Protokolldatenstrom wird eine Fehlermeldung der Art „setup.py oder requirements.txt nicht gefunden. pip-Installation wird nicht ausgeführt.“ angezeigt**: Ihre Datei *requirements.txt* konnte während des Oryx-Buildprozesses nicht gefunden werden.

    - Stellen Sie eine Verbindung mit dem Container der Web-App über [SSH](#open-ssh-session-in-browser) her, und stellen Sie sicher, dass *requirements.txt* richtig benannt und direkt unter *site/wwwroot* vorhanden ist. Falls nicht, sollen Sie sicherstellen, dass sich die Datei in Ihrem Repository befindet und Teil Ihrer Bereitstellung ist. Verschieben Sie sie in den Stamm, wenn sie in einem separaten Ordner enthalten ist.

#### <a name="other-issues"></a>Andere Probleme

- **Kennwörter werden bei der Eingabe in der SSH-Sitzung nicht angezeigt**: Aus Sicherheitsgründen blendet die SSH-Sitzung Ihr Kennwort während der Eingabe aus. Die Zeichen werden jedoch aufgezeichnet. Geben Sie also Ihr Kennwort wie gewohnt ein, und drücken Sie zum Abschluss die **EINGABETASTE**.

- **Befehle in der SSH-Sitzung scheinen abgeschnitten zu werden**: Möglicherweise umbricht der Editor Befehle nicht, dennoch sollten Sie weiterhin ordnungsgemäß ausgeführt werden.

- **Statische Objekte werden in einer Django-App nicht angezeigt**: Stellen Sie sicher, dass Sie das [Whitenoise-Modul](http://whitenoise.evans.io/en/stable/django.html) aktiviert haben.

- **Die Meldung „Schwerwiegende SSL-Verbindung ist erforderlich“ wird angezeigt**: Überprüfen Sie alle Benutzernamen und Kennwörter, die für den Zugriff auf Ressourcen (wie Datenbanken) aus der App heraus verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Python-App mit PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Tutorial: Bereitstellen aus privatem Containerrepository](tutorial-custom-container.md?pivots=container-linux)

> [!div class="nextstepaction"]
> [Häufig gestellte Fragen (FAQ) zu Azure App Service unter Linux](faq-app-service-linux.md)
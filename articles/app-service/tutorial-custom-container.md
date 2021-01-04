---
title: 'Tutorial: Erstellen und Ausführen eines benutzerdefinierten Images in Azure App Service'
description: In dieser Schritt-für-Schritt-Anleitung wird erläutert, wie Sie ein benutzerdefiniertes Linux- oder Windows-Image erstellen, das Image in Azure Container Registry pushen und es dann in Azure App Service bereitstellen. Erfahren Sie, wie Sie benutzerdefinierte Software in einem benutzerdefinierten Container zu App Service migrieren.
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: msangapu
keywords: Azure App Service, Web-App, Linux, Windows, Docker, Container
ms.custom: devx-track-csharp, mvc, seodec18, devx-track-python, devx-track-azurecli
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 68fe49ff201ead89d846a0676e81dda9fc9b75b9
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558605"
---
# <a name="migrate-custom-software-to-azure-app-service-using-a-custom-container"></a>Migrieren benutzerdefinierter Software zu Azure App Service mithilfe eines benutzerdefinierten Containers

::: zone pivot="container-windows"  

[Azure App Service](overview.md) stellt vordefinierte Anwendungsstapel unter Windows wie ASP.NET oder Node.js bereit (ausgeführt unter IIS). Die vorkonfigurierte Windows-Umgebung sperrt das Betriebssystem für Administratorzugriff, Softwareinstallationen, Änderungen am globalen Assemblycache usw. (siehe [Betriebssystemfunktionen für Azure App Service](operating-system-functionality.md)). Jedoch ermöglicht Ihnen die Verwendung eines benutzerdefinierten Windows-Containers in App Service (Vorschau) die Vornahme von Änderungen am Betriebssystem, die für Ihre App erforderlich sind. Dadurch wird das Migrieren einer lokalen App erleichtert, die eine benutzerdefinierte Betriebssystem- und Softwarekonfiguration erfordert. Dieses Tutorial veranschaulicht, wie Sie eine ASP.NET-App zu App Service migrieren, die benutzerdefinierte Schriftarten verwendet, die in der Schriftartenbibliothek von Windows installiert sind. Sie stellen ein benutzerdefiniert konfiguriertes Windows-Image aus Visual Studio in der [Azure Container Registry](../container-registry/index.yml) bereit und führen es dann in App Service aus.

![In einem Windows-Container ausgeführte Web-App](media/tutorial-custom-container/app-running.png)

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- <a href="https://hub.docker.com/" target="_blank">Registrierung für ein Docker-Hub-Konto</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Installieren Sie Docker für Windows</a>.
- <a href="/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Wechseln Sie Docker, um Windows-Container auszuführen</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Installieren Sie Visual Studio 2019</a> mit den Workloads **ASP.NET und Webentwicklung** und **Azure-Entwicklung**. Sie haben Visual Studio 2019 bereits installiert:
    - Installieren Sie die neuesten Updates in Visual Studio, indem Sie auf **Hilfe** > **Nach Updates suchen** klicken.
    - Fügen Sie die Workloads in Visual Studio hinzu, indem Sie auf **Tools** > **Tools und Features abrufen** klicken.

## <a name="set-up-the-app-locally"></a>Lokales Einrichten der App

### <a name="download-the-sample"></a>Herunterladen des Beispiels

In diesem Schritt richten Sie das lokale .NET-Projekt ein.

- [Laden Sie das Beispielprojekt](https://github.com/Azure-Samples/custom-font-win-container/archive/master.zip) herunter.
- Extrahieren (Entzippen) Sie die Datei *custom-font-win-container.zip*.

Das Beispielprojekt enthält eine einfache ASP.NET-Anwendung, die eine benutzerdefinierte Schriftart verwendet, die in der Schriftartenbibliothek von Windows installiert ist. Es ist nicht erforderlich, Schriftarten zu installieren, aber es ist ein Beispiel für eine App, die in das zugrunde liegende Betriebssystem integriert ist. Um eine solche App zu App Service zu migrieren, strukturieren Sie entweder Ihren Code neu, um die Integration zu entfernen, oder Sie migrieren sie unverändert zu einem benutzerdefinierten Windows-Container.

### <a name="install-the-font"></a>Installieren der Schriftart

Navigieren Sie in Windows-Explorer zu _custom-font-win-container-master/CustomFontSample_, klicken Sie mit der rechten Maustaste auf _FrederickatheGreat-Regular.ttf_, und wählen Sie **installieren** aus.

Diese Schriftart ist öffentlich bei [Google Fonts](https://fonts.google.com/specimen/Fredericka+the+Great) erhältlich.

### <a name="run-the-app"></a>Ausführen der App

Öffnen Sie die Datei *custom-font-win-container/CustomFontSample.sln* in Visual Studio. 

Geben Sie `Ctrl+F5` ein, um die App ohne Debuggen auszuführen. Die App wird im Standardbrowser angezeigt. 

:::image type="content" source="media/tutorial-custom-container/local-app-in-browser.png" alt-text="Screenshot: im Standardbrowser angezeigte App":::

Da sie eine installierte Schriftart verwendet, kann die App nicht in der App Service-Sandbox ausgeführt werden. Allerdings können Sie sie stattdessen mithilfe eines Windows-Containers bereitstellen, da Sie die Schriftart in dem Windows-Container installieren können.

### <a name="configure-windows-container"></a>Konfigurieren des Windows-Containers

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **CustomFontSample**, und wählen Sie **Hinzufügen** > **Unterstützung für Containerorchestrierung** aus.

:::image type="content" source="media/tutorial-custom-container/enable-container-orchestration.png" alt-text="Screenshot: Fenster „Projektmappen-Explorer“ mit dem ausgewählten Projekt „CustomFontSample“ und den ausgewählten Menüelementen „Hinzufügen“ und „Unterstützung für Containerorchestrator“":::

Wählen Sie **Docker Compose** > **OK** aus.

Ihr Projekt ist jetzt für die Ausführung in einem Windows-Container eingerichtet. Eine _Dockerfile_ wird dem Projekt **CustomFontSample** hinzugefügt, und ein Projekt **docker-compose** wird der Projektmappe hinzugefügt. 

Öffnen Sie im Projektmappen-Explorer **Dockerfile**.

Sie müssen ein [unterstütztes, übergeordnetes Image](configure-custom-container.md#supported-parent-images) verwenden. Ändern Sie das übergeordnete Image, indem Sie die `FROM`-Zeile durch den folgenden Code ersetzen:

```dockerfile
FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
```

Fügen Sie am Ende der Datei die folgende Zeile hinzu, und speichern Sie die Datei:

```dockerfile
RUN ${source:-obj/Docker/publish/InstallFont.ps1}
```

Sie finden das Skript _InstallFont.ps1_ im Projekt **CustomFontSample**. Es ist ein einfaches Skript, das die Schriftart installiert. Eine komplexere Version des Skripts finden Sie im [Script Center](https://gallery.technet.microsoft.com/scriptcenter/fb742f92-e594-4d0c-8b79-27564c575133).

> [!NOTE]
> Wenn Sie den Windows-Container lokal testen möchten, stellen Sie sicher, dass Docker auf dem lokalen Computer gestartet wird.
>

## <a name="publish-to-azure-container-registry"></a>Veröffentlichen in der Azure Container Registry

[Azure Container Registry](../container-registry/index.yml) kann Ihre Images für Containerbereitstellungen speichern. Sie können App Service so konfigurieren, dass er Images verwendet, die in Azure Container Registry gehostet werden.

### <a name="open-publish-wizard"></a>Öffnen des Veröffentlichungs-Assistenten

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **CustomFontSample**, und wählen Sie **Veröffentlichen** aus.

:::image type="content" source="media/tutorial-custom-container/open-publish-wizard.png" alt-text="Screenshot: Projektmappen-Explorer mit dem ausgewählten Projekt „CustomFontSample“ und der ausgewählten Option „Veröffentlichen“":::

### <a name="create-registry-and-publish"></a>Erstellen der Registrierung und Veröffentlichen

Wählen Sie im Veröffentlichungs-Assistenten **Container Registry** > **Neue Azure Container Registry-Instanz erstellen** > **Veröffentlichen** aus.

:::image type="content" source="media/tutorial-custom-container/create-registry.png" alt-text="Screenshot: Veröffentlichungs-Assistent mit den ausgewählten Optionen „Containerregistrierung“ und „Neue Azure Container Registry-Instanz erstellen“ und der ausgewählten Schaltfläche „Veröffentlichen“":::

### <a name="sign-in-with-azure-account"></a>Anmelden mit Azure-Konto

Wählen Sie im Dialogfeld **Neue Azure Container Registry-Instanz erstellen** die Option **Konto hinzufügen** aus, und melden Sie sich bei Ihrem Azure-Abonnement an. Falls Sie bereits angemeldet sind, können Sie in der Dropdownliste das Konto mit dem gewünschten Abonnement auswählen.

![Anmelden bei Azure](./media/tutorial-custom-container/add-an-account.png)

### <a name="configure-the-registry"></a>Konfigurieren der Registrierung

Konfigurieren Sie die neue Containerregistrierung, basierend auf den in der folgenden Tabelle vorgeschlagenen Werten. Klicken Sie auf **Erstellen**, wenn Sie fertig sind.

| Einstellung  | Vorgeschlagener Wert | Weitere Informationen finden Sie unter |
| ----------------- | ------------ | ----|
|**DNS-Präfix**| Behalten Sie den generierten Registrierungsnamen bei, oder ändern Sie ihn in einen anderen eindeutigen Namen. |  |
|**Ressourcengruppe**| Klicken Sie auf **Neu**, geben Sie **myResourceGroup** ein, und klicken Sie auf **OK**. |  |
|**SKU**| Basic | [Tarife](https://azure.microsoft.com/pricing/details/container-registry/)|
|**Registrierungsstandort**| Europa, Westen | |

![Konfigurieren der Azure Container Registry](./media/tutorial-custom-container/configure-registry.png)

Ein Terminalfenster wird geöffnet, worin der Status der Imagebereitstellung angezeigt wird. Warten Sie, bis die Bereitstellung abgeschlossen ist.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="create-a-web-app"></a>Erstellen einer Web-App

Wählen Sie im linken Menü **Ressource erstellen** > **Web** > **Web-App für Container** aus.

### <a name="configure-app-basics"></a>Konfigurieren der grundlegenden App-Einstellungen

Konfigurieren Sie auf der Registerkarte **Grundlagen** die Einstellungen entsprechend der folgenden Tabelle, und klicken Sie dann auf **Weiter: Docker**.

| Einstellung  | Vorgeschlagener Wert | Weitere Informationen finden Sie unter |
| ----------------- | ------------ | ----|
|**Abonnement**| Vergewissern Sie sich, dass das richtige Abonnement ausgewählt ist. |  |
|**Ressourcengruppe**| Wählen Sie **Neu erstellen** aus, geben Sie **myResourceGroup** ein, und klicken Sie auf **OK**. |  |
|**Name**| Geben Sie einen eindeutigen Namen ein. | Die URL der Web-App lautet `http://<app-name>.azurewebsites.net`, wobei `<app-name>` der Name Ihrer App ist. |
|**Veröffentlichen**| Docker-Container | |
|**Betriebssystem**| Windows | |
|**Region**| Europa, Westen | |
|**Windows-Plan**| Wählen Sie **Neu erstellen** aus, geben Sie **myAppServicePlan** ein, und klicken Sie auf **OK**. | |

Die Registerkarte **Grundlagen** sollte wie folgt aussehen:

![Registerkarte „Grundlagen“ für die Konfiguration der Web-App](media/tutorial-custom-container/configure-app-basics.png)

### <a name="configure-windows-container"></a>Konfigurieren des Windows-Containers

Konfigurieren Sie auf der Registerkarte **Docker** Ihren benutzerdefinierten Windows-Container wie in der folgenden Tabelle gezeigt, und wählen Sie **Überprüfen + erstellen** aus.

| Einstellung  | Vorgeschlagener Wert |
| ----------------- | ------------ |
|**Imagequelle**| Azure-Containerregistrierung |
|**Registrierung**| Wählen Sie [die zuvor erstellte Registrierung](#publish-to-azure-container-registry) aus. |
|**Image**| customfontsample |
|**Tag**| latest |

### <a name="complete-app-creation"></a>Abschließen der App-Erstellung

Klicken Sie auf **Erstellen**, und warten Sie, bis Azure die erforderlichen Ressourcen erstellt hat.

## <a name="browse-to-the-web-app"></a>Navigieren zur Web-App

Wenn der Azure-Vorgang abgeschlossen ist, wird ein Benachrichtigungsfeld angezeigt.

![Abgeschlossener Azure-Vorgang](media/tutorial-custom-container/portal-create-finished.png)

1. Klicken Sie auf **Zu Ressource wechseln**.

2. Klicken Sie auf der App-Seite auf den Link unter **URL**.

Die folgende neue Browserseite wird geöffnet:

![Neue Browserseite für die Web-App](media/tutorial-custom-container/app-starting.png)

Warten Sie einige Minuten, und wiederholen Sie den Vorgang, bis die Startseite mit der von Ihnen erwarteten, ausdrucksstarken Schriftart angezeigt wird:

![Startseite mit der von Ihnen konfigurierten Schriftart](media/tutorial-custom-container/app-running.png)

**Glückwunsch!** Sie haben eine ASP.NET-Anwendung zu Azure App Service in einen Windows-Container migriert.

## <a name="see-container-start-up-logs"></a>Anzeigen der Startprotokolle des Containers

Das Laden des Windows-Containers kann eine Weile dauern. Wenn Sie den Status anzeigen möchten, navigieren Sie zur folgenden URL. Ersetzen Sie dabei *\<app-name>* durch den Namen Ihrer App.
```
https://<app-name>.scm.azurewebsites.net/api/logstream
```

Die gestreamten Protokolle sehen wie folgt aus:

```
14/09/2018 23:16:19.889 INFO - Site: fonts-win-container - Creating container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest.
14/09/2018 23:16:19.928 INFO - Site: fonts-win-container - Create container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest succeeded. Container Id 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:23.405 INFO - Site: fonts-win-container - Start container succeeded. Container: 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Configuring container
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container start-up and configuration completed successfully
```

::: zone-end

::: zone pivot="container-linux"

Für Azure App Service wird die Containertechnologie von Docker genutzt, um sowohl integrierte als auch benutzerdefinierte Images zu hosten. Führen Sie zum Anzeigen einer Liste der integrierten Images den Azure CLI-Befehl [„az webapp list-runtimes --linux“](/cli/azure/webapp?view=azure-cli-latest&preserve-view=true#az-webapp-list-runtimes) aus. Wenn diese Images Ihre Anforderungen nicht erfüllen, können Sie ein benutzerdefiniertes Image erstellen und bereitstellen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines benutzerdefinierten Images, wenn kein integriertes Image Ihre Anforderungen erfüllt
> * Pushen des benutzerdefinierten Images in eine private Containerregistrierung in Azure
> * Ausführen des benutzerdefinierten Images in App Service
> * Konfigurieren von Umgebungsvariablen
> * Aktualisieren und erneutes Bereitstellen des Images
> * Zugreifen auf Diagnoseprotokolle
> * Herstellen einer Verbindung mit dem Container per SSH

Bei diesem Tutorial fällt in Ihrem Azure-Konto eine geringfügige Gebühr für die Containerregistrierung an. Wird der Container länger als einen Monat gehostet, können weitere Kosten anfallen.

## <a name="set-up-your-initial-environment"></a>Einrichten der anfänglichen Umgebung

- Sie benötigen ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Installieren Sie [Docker](https://docs.docker.com/get-started/#setup). Mit diesem Tool erstellen Sie Docker-Images. Zum Installieren von Docker ist möglicherweise ein Neustart des Computers erforderlich.
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
- Für dieses Tutorial ist mindestens Version 2.0.80 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

Öffnen Sie nach der Installation von Docker oder der Ausführung von Azure Cloud Shell ein Terminalfenster, und vergewissern Sie sich, dass Docker installiert ist:

```bash
docker --version
```

## <a name="clone-or-download-the-sample-app"></a>Klonen oder Herunterladen der Beispiel-App

Das Beispiel für dieses Tutorial können Sie herunterladen oder mithilfe von „Git Clone“ klonen.

### <a name="clone-with-git"></a>Klonen mit Git

Klonen Sie das Beispielrepository:

```terminal
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
```

Fügen Sie unbedingt das Argument `--config core.autocrlf=input` mit ein, um ordnungsgemäße Zeilenenden in Dateien zu gewährleisten, die im Linux-Container verwendet werden:

Wechseln Sie dann zu diesem Ordner:

```terminal
cd docker-django-webapp-linux
```

### <a name="download-from-github"></a>Von GitHub herunterladen

Statt „Git Clone“ zu verwenden, können Sie zu [https://github.com/Azure-Samples/docker-django-webapp-linux](https://github.com/Azure-Samples/docker-django-webapp-linux) navigieren und dann **Klonen** und **ZIP herunterladen** auswählen. 

Entpacken Sie die ZIP-Datei in einem Ordner mit dem Namen *docker-django-webapp-linux*. 

Öffnen Sie dann ein Terminalfenster im Ordner *docker-django-webapp-linux*.

## <a name="optional-examine-the-docker-file"></a>(Optional) Untersuchen der Docker-Datei

Die Datei im Beispiel mit dem Namen _Dockerfile_, die das Docker-Image beschreibt und Konfigurationsanweisungen enthält:

```Dockerfile
FROM tiangolo/uwsgi-nginx-flask:python3.6

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt --no-cache-dir
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222

#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

* Mit der ersten Gruppe von Befehlen werden die erforderlichen Komponenten der App in der Umgebung installiert.
* Mit der zweiten Gruppe von Befehlen wird ein [SSH](https://www.ssh.com/ssh/protocol/)-Server für eine sichere Kommunikation zwischen dem Container und dem Host erstellt.
* Mit der letzten Zeile (`ENTRYPOINT ["init.sh"]`) wird `init.sh` aufgerufen, um den SSH-Dienst und den Python-Server zu starten.

## <a name="build-and-test-the-image-locally"></a>Lokales Erstellen und Testen des Images

> [!NOTE]
> Docker Hub verfügt über [Kontingente für die Anzahl anonymer Pullvorgänge pro IP-Adresse und die Anzahl authentifizierter Pullvorgänge pro Free-Benutzer (siehe **Datenübertragungen**)](https://www.docker.com/pricing). Wenn Sie bemerken, dass die Pullvorgänge aus Docker Hub eingeschränkt sind, probieren Sie `docker login` aus, falls Sie nicht bereits angemeldet sind.
> 

1. Führen Sie den folgenden Befehl aus, um das Image zu erstellen:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```
    
1. Testen Sie, ob der Build funktioniert, indem Sie den Docker-Container lokal ausführen:

    ```bash
    docker run -p 8000:8000 appsvc-tutorial-custom-image
    ```
    
    Mit diesem [`docker run`](https://docs.docker.com/engine/reference/commandline/run/)-Befehl wird mit dem Argument `-p` der Port gefolgt vom Namen des Images angegeben. 
    
    > [!TIP]
    > Wenn Sie Windows verwenden und der Fehler „*standard_init_linux.go:211: exec user process caused "Datei oder Verzeichnis nicht vorhanden"* “ angezeigt wird, enthält die Datei *init.sh* CRLF-Zeilenenden anstelle der erwarteten LF-Zeilenenden. Dieser Fehler tritt auf, wenn Sie Git zum Klonen des Beispielrepositorys verwendet, aber den Parameter `--config core.autocrlf=input` ausgelassen haben. In diesem Fall klonen Sie das Repository erneut mit dem Argument „--config“. Der Fehler wird möglicherweise auch angezeigt, wenn Sie *init.sh* bearbeitet und mit CRLF-Zeilenenden gespeichert haben. Speichern Sie in diesem Fall die Datei nur mit LF-Zeilenenden erneut.

1. Rufen Sie `http://localhost:8000` auf, um zu überprüfen, ob Web-App und Container ordnungsgemäß funktionieren.

    ![Lokales Testen der Web-App](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

In diesem Abschnitt und den nachfolgenden Abschnitten stellen Sie Ressourcen in Azure bereit, in die Sie das Image pushen, und stellen anschließend einen Container für Azure App Service bereit. Sie erstellen zunächst eine Ressourcengruppe, in der alle diese Ressourcen gesammelt werden.

Führen Sie den Befehl [az group create](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-create) aus, um eine Ressourcengruppe zu erstellen:

```azurecli-interactive
az group create --name AppSvc-DockerTutorial-rg --location westus2
```

Sie können den Wert `--location` ändern, um eine Region in Ihrer Nähe anzugeben.

## <a name="push-the-image-to-azure-container-registry"></a>Übertragen des Images an Azure Container Registry per Push

In diesem Abschnitt pushen Sie das Image in Azure Container Registry. Von dort kann es von App Service bereitgestellt werden.

1. Führen Sie den Befehl [`az acr create`](/cli/azure/acr?view=azure-cli-latest&preserve-view=true#az-acr-create) aus, um eine Azure Container Registry-Instanz zu erstellen:

    ```azurecli-interactive
    az acr create --name <registry-name> --resource-group AppSvc-DockerTutorial-rg --sku Basic --admin-enabled true
    ```
    
    Ersetzen Sie `<registry-name>` durch einen geeigneten Namen für die Registrierung. Der Name darf nur Buchstaben und Ziffern enthalten und muss in Azure eindeutig sein.

1. Führen Sie den Befehl [`az acr show`](/cli/azure/acr?view=azure-cli-latest&preserve-view=true#az-acr-show) aus, um Anmeldeinformationen für die Registrierung abzurufen:

    ```azurecli-interactive
    az acr credential show --resource-group AppSvc-DockerTutorial-rg --name <registry-name>
    ```
    
    Die JSON-Ausgabe dieses Befehls enthält zwei Kennwörter sowie den Benutzernamen der Registrierung.
    
1. Verwenden Sie den Befehl `docker login`, um sich bei der Containerregistrierung anzumelden:

    ```bash
    docker login <registry-name>.azurecr.io --username <registry-username>
    ```
    
    Ersetzen Sie `<registry-name>` und `<registry-username>` durch die Werte aus den vorherigen Schritten. Geben Sie bei entsprechender Aufforderung eines der Kennwörter aus dem vorherigen Schritt ein.

    Sie verwenden in allen verbleibenden Schritten dieses Abschnitts denselben Registrierungsnamen.

1. Wenn die Anmeldung erfolgreich ist, markieren Sie das lokale Docker-Image für die Registrierung:

    ```bash
   docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```    

1. Pushen Sie das Image mithilfe des Befehls `docker push` in die Registrierung:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    Das erste Hochladen des Images kann einige Minuten dauern, da es das Basisimage enthält. Nachfolgende Uploads sind in der Regel schneller.

    Während Sie warten, können Sie die Schritte im nächsten Abschnitt ausführen, um App Service für die Bereitstellung über die Registrierung zu konfigurieren.

1. Überprüfen Sie mithilfe des Befehls `az acr repository list`, ob der Pushvorgang erfolgreich war:

    ```azurecli-interactive
    az acr repository list -n <registry-name>
    ```
    
    In der Ausgabe sollte der Name des Images angezeigt werden.


## <a name="configure-app-service-to-deploy-the-image-from-the-registry"></a>Konfigurieren von App Service für die Bereitstellung des Images über die Registrierung

Zum Bereitstellen eines Containers in Azure App Service erstellen Sie zunächst eine Web-App in App Service und verbinden dann die Web-App mit der Containerregistrierung. Beim Starten der Web-App pullt App Service automatisch das Image aus der Registrierung.

1. Erstellen Sie mithilfe des Befehls [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest&preserve-view=true#az-appservice-plan-create) einen App Service-Plan:

    ```azurecli-interactive
    az appservice plan create --name AppSvc-DockerTutorial-plan --resource-group AppSvc-DockerTutorial-rg --is-linux
    ```

    Ein App Service-Plan entspricht dem virtuellen Computer, der die Web-App hostet. Standardmäßig verwendet der vorherige Befehl den preisgünstigen [B1-Tarif](https://azure.microsoft.com/pricing/details/app-service/linux/), bei dem der erste Monat kostenlos ist. Sie können den Tarif mit dem Parameter `--sku` steuern.

1. Erstellen Sie die Web-App mithilfe des Befehls [`az webpp create`](/cli/azure/webapp?view=azure-cli-latest&preserve-view=true#az-webapp-create):

    ```azurecli-interactive
    az webapp create --resource-group AppSvc-DockerTutorial-rg --plan AppSvc-DockerTutorial-plan --name <app-name> --deployment-container-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```
    
    Ersetzen Sie `<app-name>` durch einen Namen für die Web-App, der innerhalb von Azure eindeutig sein muss. Ersetzen Sie außerdem `<registry-name>` durch den Namen Ihrer Registrierung aus dem vorherigen Abschnitt.

1. Verwenden Sie [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest&preserve-view=true#az-webapp-config-appsettings-set), um die Umgebungsvariable `WEBSITES_PORT` wie vom App-Code erwartet festzulegen: 

    ```azurecli-interactive
    az webapp config appsettings set --resource-group AppSvc-DockerTutorial-rg --name <app-name> --settings WEBSITES_PORT=8000
    ```

    Ersetzen Sie `<app-name>` durch den Namen, den Sie im vorherigen Schritt verwendet haben.
    
    Weitere Informationen zu dieser Umgebungsvariablen finden Sie in der [Infodatei im GitHub-Repository des Beispiels](https://github.com/Azure-Samples/docker-django-webapp-linux).

1. Aktivieren Sie mithilfe des Befehls [`az webapp identity assign`](/cli/azure/webapp/identity?view=azure-cli-latest&preserve-view=true#az-webapp-identity-assign) eine [verwaltete Identität](./overview-managed-identity.md) für die Web-App:

    ```azurecli-interactive
    az webapp identity assign --resource-group AppSvc-DockerTutorial-rg --name <app-name> --query principalId --output tsv
    ```

    Ersetzen Sie `<app-name>` durch den Namen, den Sie im vorherigen Schritt verwendet haben. Die Ausgabe des Befehls (gefiltert nach den Argumenten `--query` und `--output`) ist der Dienstprinzipal der zugewiesenen Identität, den Sie in Kürze verwenden.

    Die verwaltete Identität ermöglicht es Ihnen, der Web-App Berechtigungen für den Zugriff auf andere Azure-Ressourcen zu erteilen, ohne dass bestimmte Anmeldeinformationen erforderlich sind.

1. Rufen Sie Ihre Abonnement-ID mit dem Befehl [`az account show`](/cli/azure/account?view=azure-cli-latest&preserve-view=true#az-account-show) ab, die Sie im nächsten Schritt benötigen:

    ```azurecli-interactive
    az account show --query id --output tsv
    ``` 

1. Erteilen Sie der Web-App die Berechtigung für den Zugriff auf die Containerregistrierung:

    ```azurecli-interactive
    az role assignment create --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourceGroups/AppSvc-DockerTutorial-rg/providers/Microsoft.ContainerRegistry/registries/<registry-name> --role "AcrPull"
    ```

    Ersetzen Sie die folgenden Werte:
    - `<principal-id>` durch die Dienstprinzipal-ID aus dem Befehl `az webapp identity assign`
    - `<registry-name>` durch den Namen Ihrer Containerregistrierung
    - `<subscription-id>` durch die vom Befehl `az account show` abgerufenen Abonnement-ID

Weitere Informationen zu diesen Berechtigungen finden Sie unter [Was ist die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) von Azure?](../role-based-access-control/overview.md). 

## <a name="deploy-the-image-and-test-the-app"></a>Bereitstellen des Images und Testen der App

Sie können diese Schritte ausführen, sobald das Image in die Containerregistrierung gepusht und App Service vollständig bereitgestellt wurde.

1. Verwenden Sie den Befehl [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest&preserve-view=true#az-webapp-config-container-set), um die für die Web-App bereitzustellende Containerregistrierung und das dazugehörige Image anzugeben:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-custom-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest --docker-registry-server-url https://<registry-name>.azurecr.io
    ```
    
    Ersetzen Sie `<app_name>` durch den Namen Ihrer Web-App und `<registry-name>` an zwei Stellen durch den Namen Ihrer Registrierung. 

    - Bei Verwendung einer anderen Registrierung als Docker Hub (wie in diesem Beispiel gezeigt) muss `--docker-registry-server-url` das Format `https://` haben, gefolgt vom vollqualifizierten Domänennamen der Registrierung.
    - Eine Meldung mit dem Hinweis, dass für den Zugriff auf Azure Container Registry keine Anmeldeinformationen angegeben wurden und dass versucht wird, diese nachzuschlagen, bedeutet, dass Azure die verwaltete Identität der App für die Authentifizierung bei der Containerregistrierung verwendet, anstelle nach Benutzername und Kennwort zu fragen.
    - Wird der Fehler „AttributeError: 'NoneType' object has no attribute 'reserved'“ (AttributeError: Das NoneType-Objekt hat kein Attribut 'reserved'.) angezeigt, vergewissern Sie sich, ob `<app-name>` korrekt ist.

    > [!TIP]
    > Sie können die Containereinstellungen der Web-App jederzeit mit dem Befehl `az webapp config container show --name <app-name> --resource-group AppSvc-DockerTutorial-rg`abrufen. Das Image wird in der Eigenschaft `DOCKER_CUSTOM_IMAGE_NAME` angegeben. Wenn die Web-App über Azure DevOps oder Azure Resource Manager-Vorlagen bereitgestellt wird, kann das Image auch in einer Eigenschaft mit dem Namen `LinuxFxVersion` angezeigt werden. Beide Eigenschaften dienen demselben Zweck. Sind beide in der Web-App-Konfiguration vorhanden, hat `LinuxFxVersion` Vorrang.

1. Nach Abschluss des Befehls `az webapp config container set` sollte die Web-App im Container in App Service ausgeführt werden.

    Navigieren Sie zum Testen der App zu `http://<app-name>.azurewebsites.net`, und ersetzen Sie `<app-name>` durch den Namen Ihrer Web-App. Beim ersten Zugriff kann es einige Zeit dauern, bis die App antwortet, da App Service das gesamte Image aus der Registrierung abrufen muss. Aktualisieren Sie einfach die Seite, wenn für den Browser eine Zeitüberschreitung eintritt. Nach dem Pullen des anfänglichen Images werden nachfolgende Tests wesentlich schneller ausgeführt.

    ![Erfolgreicher Test der Web-App in Azure](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="modify-the-app-code-and-redeploy"></a>Ändern des App-Codes und erneutes Bereitstellen

In diesem Abschnitt ändern Sie den Web-App-Code, erstellen den Container neu und pushen den Container anschließend in die Registrierung. App Service pullt das aktualisierte Image dann automatisch aus der Registrierung, um die ausgeführte Web-App zu aktualisieren.

1. Öffnen Sie im lokalen Ordner *docker-django-webapp-linux* die Datei *app/templates/app/index.html*.

1. Ändern Sie das erste HTML-Element entsprechend dem folgenden Code.

    ```html
    <nav class="navbar navbar-inverse navbar-fixed-top">
      <div class="container">
        <div class="navbar-header">
          <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
        </div>
      </div>
    </nav>
    ```
    
1. Speichern Sie die Änderungen.

1. Navigieren Sie zum Ordner *docker-django-webapp-linux*, und erstellen Sie das Image neu:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```

1. Aktualisieren Sie die Versionsnummer im Tag des Images auf v1.0.1:

    ```bash
    docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    Ersetzen Sie `<registry-name>` durch den Namen Ihrer Registrierung.

1. Übertragen Sie das Image per Push in die Registrierung:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

1. Starten Sie die Web-App neu:

    ```azurecli-interactive
    az webapp restart --name <app_name> --resource-group AppSvc-DockerTutorial-rg
    ```

    Ersetzen Sie `<app_name>` durch den Namen Ihrer Web-App. Beim Neustart ruft App Service das aktualisierte Image aus der Containerregistrierung ab.

1. Überprüfen Sie, ob das Update bereitgestellt wurde, indem Sie zu `http://<app-name>.azurewebsites.net` navigieren.

## <a name="access-diagnostic-logs"></a>Zugreifen auf Diagnoseprotokolle

1. Aktivieren Sie die Containerprotokollierung:

    ```azurecli-interactive
    az webapp log config --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-container-logging filesystem
    ```
    
1. Aktivieren Sie den Protokolldatenstrom:

    ```azurecli-interactive
    az webapp log tail --name <app-name> --resource-group AppSvc-DockerTutorial-rg
    ```
    
    Falls Sie nicht sofort Konsolenprotokolle sehen, können Sie es nach 30 Sekunden noch einmal versuchen.

    Sie können die Protokolldateien auch im Browser unter `https://<app-name>.scm.azurewebsites.net/api/logs/docker` untersuchen.

1. Zum Beenden des Protokollstreamings können Sie jederzeit **STRG**+**C** drücken.

## <a name="connect-to-the-container-using-ssh"></a>Herstellen einer Verbindung mit dem Container per SSH

SSH ermöglicht die sichere Kommunikation zwischen einem Container und einem Client. Zum Aktivieren der SSH-Verbindung mit Ihrem Container muss Ihr benutzerdefiniertes Image dafür konfiguriert werden. Sobald der Container ausgeführt wird, können Sie eine SSH-Verbindung öffnen.

### <a name="configure-the-container-for-ssh"></a>Konfigurieren des Containers für SSH

Die in diesem Tutorial verwendete Beispiel-App verfügt bereits über die erforderliche Konfiguration im *Dockerfile*, die den SSH-Server installiert und außerdem die Anmeldeinformationen festlegt. Dieser Abschnitt dient nur zu Informationszwecken. Fahren Sie mit dem nächsten Abschnitt fort, um eine Verbindung mit dem Container herzustellen.

```Dockerfile
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
  && apt-get install -y --no-install-recommends openssh-server \
  && echo "$SSH_PASSWD" | chpasswd 
```

> [!NOTE]
> Diese Konfiguration erlaubt keine externen Verbindungen zum Container. SSH ist nur über die Kudu/SCM-Website verfügbar. Die Authentifizierung für die Kudu/SCM-Website wird über Ihr Azure-Konto durchgeführt.

Das *Dockerfile* kopiert außerdem die Datei *sshd_config* in den Ordner */etc/ssh/* und macht Port 2222 für den Container verfügbar:

```Dockerfile
COPY sshd_config /etc/ssh/

# ...

EXPOSE 8000 2222
```

Port 2222 ist ein interner Port, auf den nur von Containern innerhalb des Brückennetzwerks eines privaten virtuellen Netzwerks zugegriffen werden kann. 

Mit dem Eingangsskript *init.sh* wird der SSH-Server schließlich gestartet.

```bash
#!/bin/bash
service ssh start
```

### <a name="open-ssh-connection-to-container"></a>Öffnen einer SSH-Verbindung mit einem Container

1. Navigieren Sie zu `https://<app-name>.scm.azurewebsites.net/webssh/host`, und melden Sie sich mit Ihrem Azure-Konto an. Ersetzen Sie `<app-name>` durch den Namen Ihrer Web-App.

1. Nachdem Sie sich angemeldet haben, werden Sie auf eine Informationsseite für die Web-App umgeleitet. Wählen Sie oben auf der Seite **SSH** aus, um die Shell zu öffnen und Befehle zu verwenden.

    Beispielsweise können Sie mithilfe des Befehls `top` die Prozesse untersuchen, die darin ausgeführt werden.
    
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In diesem Artikel erstellte Ressourcen können laufende Kosten verursachen. Zum Bereinigen der Ressourcen müssen Sie nur die Ressourcengruppe löschen, in der die Ressourcen enthalten sind:

```azurecli
az group delete --name AppSvc-DockerTutorial-rg
```

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Bereitstellen eines benutzerdefinierten Images in einer privaten Containerregistrierung
> * Bereitstellen des benutzerdefinierten Images in App Service
::: zone pivot="container-linux"
> * Aktualisieren und erneutes Bereitstellen des Images
::: zone-end
> * Zugreifen auf Diagnoseprotokolle
::: zone pivot="container-linux"
> * Herstellen einer Verbindung mit dem Container per SSH
::: zone-end

Im nächsten Tutorial erfahren Sie, wie Sie Ihrer App einen benutzerdefinierten DNS-Namen zuordnen:

> [!div class="nextstepaction"]
> [Tutorial: Zuordnen eines benutzerdefinierten DNS-Namens zu Ihrer App](app-service-web-tutorial-custom-domain.md)

Oder sehen Sie sich weitere Ressourcen an:

> [!div class="nextstepaction"]
> [Konfigurieren eines benutzerdefinierten Containers](configure-custom-container.md)

::: zone pivot="container-linux"
> [!div class="nextstepaction"]
> [Tutorial: WordPress-App mit mehreren Containern](tutorial-multi-container-app.md)
::: zone-end

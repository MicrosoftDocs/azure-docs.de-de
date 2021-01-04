---
title: 'Schnellstart: Erstellen einer Python-App für Linux'
description: Führen Sie erste Schritte mit Azure App Service aus, indem Sie eine Python-App in einem Linux-Container in App Service bereitstellen.
ms.topic: quickstart
ms.date: 09/22/2020
ms.custom: seo-python-october2019, cli-validate, devx-track-python, devx-track-azurecli
zone_pivot_groups: python-frameworks-01
robots: noindex
ms.openlocfilehash: e068de77760752070117644c269e80a52c8c1707
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561544"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-for-linux"></a>Schnellstart: Erstellen einer Python-App in Azure App Service für Linux

In diesem Schnellstart stellen Sie eine Python-Web-App für [App Service für Linux](overview.md#app-service-on-linux) bereit. Dies ist der überaus skalierbare Webhostingdienst von Azure mit automatisiertem Patchen. Sie verwenden die lokale [Azure-Befehlszeilenschnittstelle (CLI)](/cli/azure/install-azure-cli) auf einem Macintosh-, Linux- oder Windows-Computer, um ein Beispiel mit dem Flask- oder Django-Framework bereitzustellen. Die Web-App, die Sie konfigurieren, nutzt einen kostenlosen App-Service-Tarif, sodass Ihnen im Laufe dieses Artikels keine Kosten entstehen.

> [!TIP]
> Wenn Sie Apps lieber über eine IDE bereitstellen möchten, lesen Sie **[Bereitstellen von Python-Apps in App Service aus Visual Studio Code](/azure/developer/python/tutorial-deploy-app-service-on-linux-01)** .

## <a name="set-up-your-initial-environment"></a>Einrichten der anfänglichen Umgebung

1. Sie benötigen ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. Installieren Sie <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 oder höher</a>.
1. Installieren Sie die <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.0.80 oder höher, mit der Sie Befehle in einer beliebigen Shell ausführen, um Azure-Ressourcen bereitzustellen und zu konfigurieren.

Öffnen Sie ein Terminalfenster, und überprüfen Sie, ob mindestens die Python-Version 3.6 installiert ist:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Vergewissern Sie sich, dass Sie mindestens Version 2.0.80 der Azure CLI verwenden:

```azurecli
az --version
```

Melden Sie sich anschließend über die CLI bei Azure an:

```azurecli
az login
```

Dieser Befehl öffnet einen Browser zum Erfassen Ihrer Anmeldeinformationen. Wenn der Befehl abgeschlossen ist, wird eine JSON-Ausgabe mit Informationen zu Ihren Abonnements angezeigt.

Nachdem Sie sich angemeldet haben, können Sie Azure-Befehle mit der Azure CLI ausführen, um Ressourcen in Ihrem Abonnement zu verwenden.

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="clone-the-sample"></a>Klonen des Beispiels

Klonen Sie das Beispielrepository mit dem folgenden Befehl, und navigieren Sie zum Beispielordner. ([Installieren Sie Git](https://git-scm.com/downloads), sofern noch nicht vorhanden.)

::: zone pivot="python-framework-flask"
```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Navigieren Sie anschließend in diesen Ordner:

```terminal
cd python-docs-hello-world
```
::: zone-end

::: zone pivot="python-framework-django"
```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-django
```

Navigieren Sie anschließend in diesen Ordner:

```terminal
cd python-docs-hello-django
```
::: zone-end

Das Beispiel enthält frameworkspezifischen Code, der von Azure App Service beim Starten der App erkannt wird. Weitere Informationen finden Sie unter [Startprozess des Containers](configure-language-python.md#container-startup-process).

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="deploy-the-sample"></a>Bereitstellen des Beispiels

Stellen Sie mithilfe des Befehls `az webapp up` den Code in Ihrem lokalen Ordner (*python-docs-hello-world*) bereit:

```azurecli
az webapp up --sku F1 --name <app-name>
```

- Wird der Befehl `az` nicht erkannt, sollten Sie sich vergewissern, dass die Azure CLI wie unter [Einrichten der anfänglichen Umgebung](#set-up-your-initial-environment) beschrieben installiert wurde.
- Falls der Befehl `webapp` nicht erkannt wird, kann dies daran liegen, dass Sie Azure CLI-Version 2.0.80 oder höher verwenden. Wenn dies nicht der Fall ist, sollten Sie die [aktuelle Version installieren](/cli/azure/install-azure-cli).
- Ersetzen Sie `<app_name>` durch einen Namen, der innerhalb von Azure eindeutig ist (*gültige Zeichen: `a-z`, `0-9` und `-`* ). Ein bewährtes Muster ist eine Kombination aus Ihrem Firmennamen und einer App-ID.
- Mit dem Argument `--sku F1` wird die Web-App im Tarif „Free“ erstellt. Lassen Sie dieses Argument weg, um einen schnelleren Premium-Tarif zu verwenden. Dieser verursacht jedoch stündlich Kosten.
- Optional können Sie das Argument `--location <location-name>` einfügen, wobei `<location_name>` eine verfügbare Azure-Region ist. Sie können eine Liste der zulässigen Regionen für Ihr Azure-Konto abrufen, indem Sie den Befehl [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) ausführen.
- Wird der Fehler „Could not auto-detect the runtime stack of your app“ (Der Runtimestapel der App konnte nicht automatisch erkannt werden.) angezeigt, sollten Sie sich vergewissern, dass der Befehl im Ordner *python-docs-hello-world* (Flask) bzw. *python-docs-hello-django* (Django) ausgeführt wird, der die Datei *requirements.txt* enthält. (Siehe [Behandeln von Problemen bei der automatischen Erkennung mit „az webapp up“](https://github.com/Azure/app-service-linux-docs/blob/master/AzWebAppUP/runtime_detection.md) (GitHub))

Die Ausführung dieses Befehls kann einige Minuten in Anspruch nehmen. Bei der Ausführung werden Nachrichten zur Erstellung der Ressourcengruppe, des App Service-Plans und der Hosting-App, zur Konfiguration der Protokollierung und zur anschließenden ZIP-Bereitstellung bereitgestellt. Anschließend wird die Meldung „You can launch the app at http://&lt;app-name&gt;.azurewebsites.net“ (Sie können die App unter http://<App-Name>.azurewebsites.net starten.) angezeigt. Dabei handelt es sich um die URL der App in Azure.

![Beispielausgabe des Befehls „az webapp up“](./media/quickstart-python/az-webapp-up-output.png)

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/FlaskCLIQuickstartHelp)

[!include [az webapp up command note](../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Navigieren zur App

Navigieren Sie in Ihrem Webbrowser zur an der URL `http://<app-name>.azurewebsites.net` bereitgestellten Anwendung. Es dauert einen Moment, wenn die App zum ersten Mal gestartet wird.

Der Python-Beispielcode führt einen Linux-Container in App Service mit einem integrierten Image aus.

![Ausführen einer Python-Beispiel-App in Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Glückwunsch!** Sie haben Ihre Python-App in App Service bereitgestellt.

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="run-the-sample"></a>Ausführen des Beispiels 

::: zone pivot="python-framework-flask"
1. Stellen Sie sicher, dass Sie sich im Ordner *python-docs-hello-world* befinden. 

1. Erstellen Sie eine virtuelle Umgebung, und installieren Sie Abhängigkeiten:

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    Vergewissern Sie sich, dass Sie sich im Ordner *python-docs-hello-world* befinden, wenn die Meldung „[Errno 2] Datei oder Verzeichnis nicht vorhanden: 'requirements.txt'.“ angezeigt wird.

1. Führen Sie den Entwicklungsserver aus.

    ```terminal  
    flask run
    ```
    
    Standardmäßig wird vom Server angenommen, dass sich das Eingabemodul der App in *app.py* befindet (wie im Beispiel). (Wenn Sie einen anderen Modulnamen verwenden, müssen Sie die Umgebungsvariable `FLASK_APP` auf diesen Namen festlegen.)

1. Öffnen Sie einen Webbrowser, und navigieren Sie zur Beispiel-App unter `http://localhost:5000/`. Die App zeigt die Meldung **Hello, World!** an.

    ![Lokales Ausführen einer Python-Beispiel-App](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. Drücken Sie in Ihrem Terminalfenster **STRG**+**C**, um den Entwicklungsserver zu beenden.
::: zone-end

::: zone pivot="python-framework-django"
1. Stellen Sie sicher, dass Sie sich im Ordner *python-docs-hello-django* befinden. 

1. Erstellen Sie eine virtuelle Umgebung, und installieren Sie Abhängigkeiten:

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    Vergewissern Sie sich, dass Sie sich im Ordner *python-docs-hello-django* befinden, wenn die Meldung „[Errno 2] Datei oder Verzeichnis nicht vorhanden: 'requirements.txt'.“ angezeigt wird.
    
1. Führen Sie den Entwicklungsserver aus.

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    python3 manage.py runserver
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    py -3 manage.py runserver
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    py -3 manage.py runserver
    ```

    ---    

1. Öffnen Sie einen Webbrowser, und navigieren Sie zur Beispiel-App unter `http://localhost:8000/`. Die App zeigt die Meldung **Hello, World!** an.

    ![Lokales Ausführen einer Python-Beispiel-App](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. Drücken Sie in Ihrem Terminalfenster **STRG**+**C**, um den Entwicklungsserver zu beenden.
::: zone-end

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="redeploy-updates"></a>Erneutes Bereitstellen von Updates

In diesem Abschnitt nehmen Sie eine geringfügige Codeänderung vor und stellen den Code dann erneut in Azure bereit. Die Codeänderung umfasst eine `print`-Anweisung, um die Protokollierungsausgabe für den nächsten Abschnitt zu generieren.

::: zone pivot="python-framework-flask"
Öffnen Sie *app.py* in einem Editor, und aktualisieren Sie die Funktion `hello` so, dass sie dem folgenden Code entspricht. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello, Azure!"
```
::: zone-end
::: zone pivot="python-framework-django"
Öffnen Sie *hello/views.py* in einem Editor, und aktualisieren Sie die Funktion `hello` so, dass sie dem folgenden Code entspricht.

```python
def hello(request):
    print("Handling request to home page.")
    return HttpResponse("Hello, Azure!")
```
::: zone-end
    
Speichern Sie Ihre Änderungen, und stellen Sie die App dann mit dem Befehl `az webapp up` erneut bereit:

```azurecli
az webapp up
```

In diesem Befehl werden lokal zwischengespeicherte Werte aus der Datei *.azure/config* verwendet (einschließlich App-Name, Ressourcengruppe und App Service-Plan).

Wechseln Sie nach Abschluss der Bereitstellung wieder zum Browserfenster, in dem `http://<app-name>.azurewebsites.net` geöffnet ist. Aktualisieren Sie die Seite. Nun sollte die geänderte Meldung angezeigt werden:

![Ausführen einer aktualisierten Python-Beispiel-App in Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/FlaskCLIQuickstartHelp)

> [!TIP]
> Visual Studio Code bietet leistungsstarke Erweiterungen für Python und Azure App Service, die die Bereitstellung von Python-Web-Apps in App Service vereinfachen. Weitere Informationen finden Sie unter [Bereitstellen von Python-Apps in App Service aus Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Streaming von Protokollen

Sie können auf die Konsolenprotokolle zugreifen, die innerhalb der App und des Containers, in dem sie ausgeführt wird, generiert wurden. Protokolle enthalten alle Ausgaben, die mit `print`-Anweisungen generiert werden.

Führen Sie zum Streamen von Protokollen den Befehl [az webapp log tail](/cli/azure/webapp/log?view=azure-cli-latest&preserve-view=true#az_webapp_log_tail) aus:

```azurecli
az webapp log tail
```

Sie können auch den Parameter `--logs` mit dem Befehl `az webapp up` einfügen, damit der Protokolldatenstrom bei der Bereitstellung automatisch geöffnet wird.

Aktualisieren Sie die App im Browser, um Konsolenprotokolle zu erstellen, die Meldungen mit einer Beschreibung der HTTP-Anforderungen an die App enthalten. Wenn nicht sofort eine Ausgabe angezeigt wird, versuchen Sie es nach 30 Sekunden erneut.

Sie können die Protokolldateien auch im Browser unter `https://<app-name>.scm.azurewebsites.net/api/logs/docker` untersuchen.

Sie können im Terminal jederzeit **STRG**+**C** drücken, um das Protokollstreaming zu beenden.

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="manage-the-azure-app"></a>Verwalten der Azure-App

Wechseln Sie zum <a href="https://portal.azure.com" target="_blank">Azure-Portal</a>, um die erstellte App zu verwalten. Suchen Sie nach **App Services**, und wählen Sie diese Option aus.

![Navigieren zu App Services im Azure-Portal](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Wählen Sie den Namen Ihrer Azure-App aus.

![Navigieren zu Ihrer Python-App in App Services im Azure-Portal](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Wenn Sie die App auswählen, wird die Seite **Übersicht** geöffnet. Dort können Sie grundlegende Verwaltungsaufgaben wie Durchsuchen, Beenden, Starten, Neustarten und Löschen ausführen.

![Verwalten Ihrer Python-App auf der Seite „Übersicht“ im Azure-Portal](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

Im Menü „App Service“ werden verschiedene Seiten für die Konfiguration Ihrer App angezeigt.

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In den vorherigen Schritten haben Sie Azure-Ressourcen in einer Ressourcengruppe erstellt. Die Ressourcengruppe hat abhängig von Ihrem Standort einen Namen wie appsvc_rg_Linux_CentralUS. Wenn Sie eine andere App Service-SKU als den kostenlosen Tarif „F1“ verwenden, fallen laufende Kosten für diese Ressourcen an (siehe [App Service – Preise](https://azure.microsoft.com/pricing/details/app-service/linux/)).

Wenn Sie diese Ressourcen voraussichtlich nicht mehr benötigen, löschen Sie die Ressourcengruppen mit folgendem Befehl:

```azurecli
az group delete --no-wait
```

Der Befehl verwendet den Ressourcengruppennamen, der in der Datei *.azure/config* zwischengespeichert ist.

Mit dem Argument `--no-wait` kann für den Befehl die Rückgabe erfolgen, bevor der Vorgang abgeschlossen ist.

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Python-Web-App (Django) mit PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Konfigurieren einer Python-App](configure-language-python.md)

> [!div class="nextstepaction"]
> [Schnellstart: Hinzufügen von „Mit Microsoft anmelden“ zu einer Python-Web-App](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Tutorial: Ausführen einer Python-App in einem benutzerdefinierten Container](tutorial-custom-container.md)

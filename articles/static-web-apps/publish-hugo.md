---
title: 'Tutorial: Veröffentlichen einer Hugo-Website in Azure Static Web Apps'
description: Hier erfahren Sie, wie Sie eine Hugo-Anwendung in Azure Static Web Apps bereitstellen.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.openlocfilehash: 4539c32a367bb0974212d989176a96b530da21a4
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652331"
---
# <a name="tutorial-publish-a-hugo-site-to-azure-static-web-apps-preview"></a>Tutorial: Veröffentlichen einer Hugo-Website in Azure Static Web Apps (Vorschauversion)

In diesem Artikel wird gezeigt, wie Sie eine [Hugo](https://gohugo.io/)-Webanwendung erstellen und in [Azure Static Web Apps](overview.md) bereitstellen. Das Ergebnis ist eine neue Azure Static Web Apps-Instanz mit zugehöriger GitHub Actions-Instanz, mit der Sie steuern können, wie die App erstellt und veröffentlicht wird.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> - Erstellen einer Hugo-App
> - Einrichten einer Azure Static Web Apps-Instanz
> - Bereitstellen der Hugo-App in Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Falls Sie über keins verfügen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/).
- Ein GitHub-Konto. Falls Sie über keins verfügen, können Sie ein [kostenloses Konto erstellen](https://github.com/join).

## <a name="create-a-hugo-app"></a>Erstellen einer Hugo-App

Erstellen Sie mithilfe der Hugo-Befehlszeilenschnittstelle (Command Line Interface, CLI) eine Hugo-App:

1. Befolgen Sie die [Installationsanweisungen](https://gohugo.io/getting-started/installing/) für Hugo unter Ihrem Betriebssystem.

1. Öffnen Sie ein Terminal.

1. Führen Sie die Hugo-Befehlszeilenschnittstelle aus, um eine neue App zu erstellen.

   ```bash
   hugo new site static-app
   ```

1. Navigieren Sie zu der neu erstellten App.

   ```bash
   cd static-app
   ```

1. Initialisieren Sie ein Git-Repository.

   ```bash
    git init
   ```

1. Fügen Sie als Nächstes der Website ein Design hinzu, indem Sie ein Design als Git-Submodul installieren und es dann in der Hugo-Konfigurationsdatei angeben.

   ```bash
   git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke
   echo 'theme = "ananke"' >> config.toml
   ```

1. Führen Sie für die Änderungen einen Commit aus.

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Übertragen Ihrer Anwendung auf GitHub

Sie benötigen ein Repository auf GitHub, um eine Verbindung mit Azure Static Web Apps herzustellen. In den folgenden Schritten wird veranschaulicht, wie Sie ein Repository für Ihre Website erstellen.

1. Erstellen Sie über [https://github.com/new](https://github.com/new) ein leeres GitHub-Repository mit dem Namen **hugo-static-app**. (Erstellen Sie keine README-Datei.)

1. Fügen Sie das GitHub-Repository als Remoteressource zu Ihrem lokalen Repository hinzu. Fügen Sie unbedingt den GitHub-Benutzernamen anstelle des Platzhalters `<YOUR_USER_NAME>` im folgenden Befehl hinzu:

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/hugo-static-app
   ```

1. Übertragen Sie Ihr lokales Repository auf GitHub.

   ```bash
   git push --set-upstream origin main
   ```

## <a name="deploy-your-web-app"></a>Bereitstellen Ihrer Web-App

In den folgenden Schritten wird gezeigt, wie Sie eine neue statische Website-App erstellen und in einer Produktionsumgebung bereitstellen.

### <a name="create-the-application"></a>Erstellen der Anwendung

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
1. Klicken Sie auf **Ressource erstellen**.
1. Suchen Sie nach **Static Web Apps**.
1. Klicken Sie auf **Static Web Apps (Vorschau)** .
1. Klicken Sie auf **Erstellen**

   :::image type="content" source="./media/publish-hugo/create-in-portal.png" alt-text="Erstellen einer Azure Static Web Apps-Ressource im Portal":::

1. Akzeptieren Sie unter **Abonnement** das angegebene Abonnement, oder wählen Sie in der Dropdownliste ein neues Abonnement aus.

1. Wählen Sie unter _Ressourcengruppe_ die Option **Neu** aus. Geben Sie unter _Name für neue Ressourcengruppe_ den Namen **hugo-static-app** ein, und wählen Sie **OK** aus.

1. Geben Sie anschließend einen Namen für Ihre App im Feld **Name** ein. Gültige Zeichen sind `a-z`, `A-Z`, `0-9` und `-`.

1. Wählen Sie unter _Region_ eine verfügbare Region in Ihrer Nähe aus.

1. Wählen Sie unter _SKU_ die Option **Free** aus.

   :::image type="content" source="./media/publish-hugo/basic-app-details.png" alt-text="Eingegebene Details":::

1. Klicken Sie auf die Schaltfläche **Mit GitHub anmelden**.

1. Wählen Sie die **Organisation** aus, unter der Sie das Repository erstellt haben.

1. Wählen unter _Repository_ die Option **hugo-static-app** aus.

1. Wählen Sie unter _Branch_ die Option **main** aus.

   :::image type="content" source="./media/publish-hugo/completed-github-info.png" alt-text="Eingegebene GitHub-Informationen":::

### <a name="build"></a>Entwickeln

Als Nächstes fügen Sie Konfigurationseinstellungen hinzu, die beim Buildprozess zum Erstellen der App verwendet werden. Mit den folgenden Einstellungen wird die GitHub Actions-Workflowdatei konfiguriert.

1. Klicken Sie auf die Schaltfläche **Weiter: Erstellen >** , um die Buildkonfiguration zu bearbeiten

1. Legen Sie _App-Speicherort_ auf **/** fest.

1. Legen Sie _Speicherort für App-Artefakte_ auf **Öffentlich** fest.

   Ein Wert für _API-Speicherort_ ist nicht erforderlich, da Sie hier keine API bereitstellen.

### <a name="review-and-create"></a>Überprüfen und Erstellen

1. Klicken Sie auf die Schaltfläche **Bewerten + erstellen**, um sicherzustellen, dass alle Details stimmen.

1. Klicken Sie auf **Erstellen**, um mit der Erstellung der Azure Static Web Apps-Instanz zu beginnen und einen GitHub Actions-Vorgang für die Bereitstellung anzugeben.

1. Warten Sie, bis der GitHub Actions-Vorgang abgeschlossen wurde.

1. Klicken Sie im Azure-Portal im Fenster _Übersicht_ der neu erstellten Azure Static Web Apps-Ressource auf den Link _URL_, um Ihre bereitgestellte Anwendung zu öffnen.

   :::image type="content" source="./media/publish-hugo/deployed-app.png" alt-text="Bereitgestellte Anwendung":::

#### <a name="custom-hugo-version"></a>Benutzerdefinierte Hugo-Version

Beim Generieren einer statischen Web-App wird eine [Workflowdatei](./github-actions-workflow.md) generiert, die die Konfigurationseinstellungen der Anwendung für die Veröffentlichung enthält. Sie können eine bestimmte Hugo-Version in der Workflowdatei festlegen, indem Sie im Abschnitt `env` einen Wert für `HUGO_VERSION` angeben. In der folgenden Beispielkonfiguration wird veranschaulicht, wie Sie Hugo auf eine bestimmte Version festlegen.

```yaml
jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for Github integrations (i.e. PR comments)
          action: "upload"
          ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
          # For more information regarding Static Web App workflow configurations, please visit: https://aka.ms/swaworkflowconfig
          app_location: "/" # App source code path
          api_location: "api" # Api source code path - optional
          output_location: "public" # Built app content directory - optional
          ###### End of Repository/Build Configurations ######
        env:
          HUGO_VERSION: 0.58.0
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Hinzufügen einer benutzerdefinierten Domäne](custom-domain.md)

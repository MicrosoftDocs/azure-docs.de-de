---
title: 'Tutorial: Veröffentlichen einer Jekyll-Website in Azure Static Web Apps'
description: Hier erfahren Sie, wie Sie eine Jekyll-Anwendung in Azure Static Web Apps bereitstellen.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 06/08/2020
ms.author: cshoe
ms.openlocfilehash: 8c6764ad5b63aa2fde07326ab986404ea4312316
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585176"
---
# <a name="tutorial-publish-a-jekyll-site-to-azure-static-web-apps-preview"></a>Tutorial: Veröffentlichen einer Jekyll-Website in Azure Static Web Apps (Vorschauversion)

In diesem Artikel wird gezeigt, wie Sie eine [Jekyll](https://jekyllrb.com/)-Webanwendung erstellen und in [Azure Static Web Apps](overview.md) bereitstellen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> - Erstellen einer Jekyll-Website
> - Einrichten einer Azure Static Web Apps-Instanz
> - Bereitstellen der Jekyll-App in Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

- [Jekyll](https://jekyllrb.com/docs/installation/)-Installation
  - Sie können das Windows-Subsystem für Linux verwenden und bei Bedarf die Ubuntu-Anweisungen befolgen.
- Ein Azure-Konto mit einem aktiven Abonnement. Falls Sie über keins verfügen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/).
- Ein GitHub-Konto. Falls Sie über keins verfügen, können Sie ein [kostenloses Konto erstellen](https://github.com/join).

## <a name="create-jekyll-app"></a>Erstellen einer Jekyll-App

Erstellen Sie mithilfe der Jekyll-Befehlszeilenschnittstelle (Command Line Interface, CLI) eine Jekyll-App:

1. Führen Sie auf dem Terminal die Jekyll-Befehlszeilenschnittstelle aus, um eine neue App zu erstellen.

   ```bash
   jekyll new static-app
   ```

1. Navigieren Sie zu der neu erstellten App.

   ```bash
   cd static-app
   ```

1. Initialisieren Sie ein neues Git-Repository.

   ```bash
    git init
   ```

1. Führen Sie für die Änderungen einen Commit aus.

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Übertragen Ihrer Anwendung auf GitHub

Azure Static Web Apps verwendet GitHub zum Veröffentlichen Ihrer Website. In den folgenden Schritten wird veranschaulicht, wie Sie ein GitHub-Repository erstellen.

1. Erstellen Sie über [https://github.com/new](https://github.com/new) ein leeres GitHub-Repository mit dem Namen **jekyll-azure-static**. (Erstellen Sie keine README-Datei.)

1. Fügen Sie das GitHub-Repository als Remoteressource zu Ihrem lokalen Repository hinzu. Fügen Sie unbedingt den GitHub-Benutzernamen anstelle des Platzhalters `<YOUR_USER_NAME>` im folgenden Befehl hinzu:

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/jekyll-static-app
   ```

1. Übertragen Sie Ihr lokales Repository auf GitHub.

   ```bash
   git push --set-upstream origin main
   ```

   > [!NOTE]
   > Ihre Git-Verzweigung hat unter Umständen einen anderen Namen als `main`. Ersetzen Sie `main` in diesem Befehl durch den richtigen Wert.

## <a name="deploy-your-web-app"></a>Bereitstellen Ihrer Web-App

In den folgenden Schritten wird gezeigt, wie Sie eine neue statische Website-App erstellen und in einer Produktionsumgebung bereitstellen.

### <a name="create-the-application"></a>Erstellen der Anwendung

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).

1. Klicken Sie auf **Ressource erstellen**.

1. Suchen Sie nach **Static Web Apps**.

1. Klicken Sie auf **Static Web Apps (Vorschau)** .

1. Klicken Sie auf **Erstellen**.

1. Akzeptieren Sie unter **Abonnement** das angegebene Abonnement, oder wählen Sie in der Dropdownliste ein neues Abonnement aus.

1. Wählen Sie unter _Ressourcengruppe_ die Option **Neu** aus. Geben Sie unter _Name für neue Ressourcengruppe_ den Namen **jekyll-static-app** ein, und wählen Sie **OK** aus.

1. Geben Sie anschließend einen Namen für Ihre App im Feld _Name_ ein. Gültige Zeichen sind `a-z`, `A-Z`, `0-9` und `-`.

1. Wählen Sie unter _Region_ eine verfügbare Region in Ihrer Nähe aus.

1. Wählen Sie unter _SKU_ die Option **Free** aus.

    :::image type="content" source="./media/publish-jekyll/basic-app-details.png" alt-text="Eingegebene Details":::

1. Klicken Sie auf die Schaltfläche **Mit GitHub anmelden**.

1. Wählen Sie die **Organisation** aus, unter der Sie das Repository erstellt haben.

1. Wählen Sie unter _Repository_ die Option **jekyll-static-app** aus.

1. Wählen Sie unter _Branch_ die Option **main** aus.

    :::image type="content" source="./media/publish-jekyll/completed-github-info.png" alt-text="Eingegebene GitHub-Informationen":::

### <a name="build"></a>Entwickeln

Als Nächstes fügen Sie Konfigurationseinstellungen hinzu, die beim Buildprozess zum Erstellen der App verwendet werden. Mit den folgenden Einstellungen wird die GitHub Actions-Workflowdatei konfiguriert.

1. Klicken Sie auf die Schaltfläche **Weiter: Erstellen >** , um die Buildkonfiguration zu bearbeiten.

1. Legen Sie _App-Speicherort_ auf **/_site** fest.

1. Lassen Sie den _Speicherort für App-Artefakte_ leer.

   Ein Wert für _API-Speicherort_ ist nicht erforderlich, da Sie hier keine API bereitstellen.

### <a name="review-and-create"></a>Überprüfen und Erstellen

1. Klicken Sie auf die Schaltfläche **Bewerten + erstellen**, um sicherzustellen, dass alle Details stimmen.

1. Klicken Sie auf **Erstellen**, um mit der Erstellung der Azure Static Web Apps-Instanz zu beginnen und einen GitHub Actions-Vorgang für die Bereitstellung anzugeben.

1. Bei der Bereitstellung tritt zunächst ein Fehler auf, weil die Workflowdatei einige Jekyll-spezifische Einstellungen benötigt. Navigieren Sie zum Hinzufügen dieser Einstellungen zu Ihrem Terminal, und pullen Sie den Commit mit dem GitHub Actions-Vorgang auf Ihren Computer.

   ```bash
   git pull
   ```

1. Öffnen Sie die Jekyll-App in einem Text-Editor, und öffnen Sie anschließend die Datei _.github/workflows/azure-pages-<WORKFLOWNAME>.yml_.

1. Fügen Sie vor der Zeile `- name: Build And Deploy` den folgenden Konfigurationsblock hinzu:

    ```yml
    - name: Set up Ruby
      uses: ruby/setup-ruby@v1.59.1
      with:
        ruby-version: 2.6
    - name: Install dependencies
      run: bundle install
    - name: Jekyll build
      run: jekyll build
    ```

1. Committen Sie den aktualisierten Workflow, und übertragen Sie ihn per Push auf GitHub.

    ```bash
    git add -A
    git commit -m "Updating GitHub Actions workflow"
    git push
    ```

1. Warten Sie, bis der GitHub Actions-Vorgang abgeschlossen wurde.

1. Klicken Sie im Azure-Portal im Fenster _Übersicht_ auf den Link _URL_, um Ihre bereitgestellte Anwendung zu öffnen.

   :::image type="content" source="./media/publish-jekyll/deployed-app.png" alt-text="Bereitgestellte Anwendung":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Hinzufügen einer benutzerdefinierten Domäne](custom-domain.md)

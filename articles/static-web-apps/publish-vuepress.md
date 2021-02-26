---
title: 'Tutorial: Veröffentlichen einer VuePress-Website in Azure Static Web Apps'
description: In diesem Tutorial wird gezeigt, wie Sie eine VuePress-Anwendung in Azure Static Web Apps bereitstellen.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.custom: devx-track-js
ms.openlocfilehash: 6f0616df885a7f8fcd76337c810bc368aa02f3c8
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650444"
---
# <a name="tutorial-publish-a-vuepress-site-to-azure-static-web-apps-preview"></a>Tutorial: Veröffentlichen einer VuePress-Website in Azure Static Web Apps (Vorschauversion)

In diesem Artikel wird gezeigt, wie Sie eine [VuePress](https://vuepress.vuejs.org/)-Webanwendung erstellen und in [Azure Static Web Apps](overview.md) bereitstellen. Das Ergebnis ist eine neue Azure Static Web Apps-Anwendung mit der zugehörigen GitHub Actions-Instanz, mit der Sie steuern können, wie die App erstellt und veröffentlicht wird.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> - Erstellen einer VuePress-App
> - Einrichten einer Azure Static Web Apps-Instanz
> - Bereitstellen der VuePress-App in Azure

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Falls Sie über keins verfügen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/).
- Ein GitHub-Konto. Falls Sie über keins verfügen, können Sie ein [kostenloses Konto erstellen](https://github.com/join).
- Installation von [Node.js](https://nodejs.org)

## <a name="create-a-vuepress-app"></a>Erstellen einer VuePress-App

Erstellen Sie eine VuePress-App über die Befehlszeilenschnittstelle (Command Line Interface, CLI):

1. Erstellen Sie einen neuen Ordner für die VuePress-App.

   ```bash
   mkdir static-site
   ```

1. Fügen Sie dem Ordner eine Datei vom Typ _README.md_ hinzu.

   ```bash
   echo '# Hello From VuePress' > README.md
   ```

1. Initialisieren Sie die Datei _package.json_.

   ```bash
   npm init -y
   ```

1. Fügen Sie VuePress als `devDependency` hinzu.

   ```bash
   npm install --save-dev vuepress
   ```

1. Öffnen Sie die Datei _package.json_ in einem Text-Editor, und fügen Sie dem Abschnitt [`scripts`](https://docs.npmjs.com/cli-commands/run-script.html) einen Buildbefehl hinzu.

   ```json
   ...
   "scripts": {
       "build": "vuepress build"
   }
   ...
   ```

1. Erstellen Sie eine Datei vom Typ _.gitignore_, um den Ordner _node\_modules_ auszuschließen.

    ```bash
    echo 'node_modules' > .gitignore
    ```

1. Initialisieren Sie ein Git-Repository.

   ```bash
    git init
    git add -A
    git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Übertragen Ihrer Anwendung auf GitHub

Sie benötigen ein Repository auf GitHub, um eine Verbindung mit Azure Static Web Apps herzustellen. In den folgenden Schritten wird veranschaulicht, wie Sie ein Repository für Ihre Website erstellen.

1. Erstellen Sie über [https://github.com/new](https://github.com/new) ein leeres GitHub-Repository mit dem Namen **vuepress-static-app**. (Erstellen Sie keine README-Datei.)

1. Fügen Sie das GitHub-Repository als Remoteressource zu Ihrem lokalen Repository hinzu. Fügen Sie unbedingt den GitHub-Benutzernamen anstelle des Platzhalters `<YOUR_USER_NAME>` im folgenden Befehl hinzu:

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/vuepress-static-app
   ```

1. Übertragen Sie Ihr lokales Repository auf GitHub.

   ```bash
   git push --set-upstream origin main
   ```

## <a name="deploy-your-web-app"></a>Bereitstellen Ihrer Web-App

In den folgenden Schritten wird gezeigt, wie Sie eine neue Static Web Apps-Anwendung erstellen und in einer Produktionsumgebung bereitstellen.

### <a name="create-the-application"></a>Erstellen der Anwendung

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
1. Klicken Sie auf **Ressource erstellen**.
1. Suchen Sie nach **Static Web Apps**.
1. Klicken Sie auf **Static Web Apps (Vorschau)** .
1. Klicken Sie auf **Erstellen**

   :::image type="content" source="./media/publish-vuepress/create-in-portal.png" alt-text="Erstellen einer statischen Web-App (Vorschau) im Portal":::

1. Akzeptieren Sie unter **Abonnement** das angegebene Abonnement, oder wählen Sie in der Dropdownliste ein neues Abonnement aus.

1. Wählen Sie unter _Ressourcengruppe_ die Option **Neu** aus. Geben Sie unter _Name für neue Ressourcengruppe_ den Namen **vuepress-static-app** ein, und wählen Sie **OK** aus.

1. Geben Sie anschließend einen Namen für Ihre App im Feld **Name** ein. Gültige Zeichen sind `a-z`, `A-Z`, `0-9` und `-`.

1. Wählen Sie unter _Region_ eine verfügbare Region in Ihrer Nähe aus.

1. Wählen Sie unter _SKU_ die Option **Free** aus.

   :::image type="content" source="./media/publish-vuepress/basic-app-details.png" alt-text="Eingegebene Details":::

1. Klicken Sie auf die Schaltfläche **Mit GitHub anmelden**.

1. Wählen Sie die **Organisation** aus, unter der Sie das Repository erstellt haben.

1. Wählen Sie unter _Repository_ die Option **vuepress-static-app** aus.

1. Wählen Sie unter _Branch_ die Option **main** aus.

   :::image type="content" source="./media/publish-vuepress/completed-github-info.png" alt-text="Eingegebene GitHub-Informationen":::

### <a name="build"></a>Entwickeln

Als Nächstes fügen Sie Konfigurationseinstellungen hinzu, die beim Buildprozess zum Erstellen der App verwendet werden. Mit den folgenden Einstellungen wird die GitHub Actions-Workflowdatei konfiguriert.

1. Klicken Sie auf die Schaltfläche **Weiter: Erstellen >** , um die Buildkonfiguration zu bearbeiten

1. Legen Sie _App-Speicherort_ auf **/** fest.

1. Legen Sie _Speicherort für App-Artefakte_ auf **.vuepress/dist** fest.

Ein Wert für _API-Speicherort_ ist nicht erforderlich, da Sie hier keine API bereitstellen.

   :::image type="content" source="./media/publish-vuepress/build-details.png" alt-text="Buildeinstellungen":::

### <a name="review-and-create"></a>Überprüfen und Erstellen

1. Klicken Sie auf die Schaltfläche **Bewerten + erstellen**, um sicherzustellen, dass alle Details stimmen.

1. Klicken Sie auf **Erstellen**, um mit der Erstellung der Azure Static Web Apps-Instanz zu beginnen und einen GitHub Actions-Vorgang für die Bereitstellung anzugeben.

1. Wenn die Bereitstellung abgeschlossen ist, klicken Sie auf **Zu Ressource wechseln**.

1. Klicken Sie auf dem Ressourcenbildschirm auf den Link _URL_, um Ihre bereitgestellte Anwendung zu öffnen. Sie müssen ggf. ein bis zwei Minuten warten, bis der GitHub Actions-Vorgang abgeschlossen ist.

   :::image type="content" source="./media/publish-vuepress/deployed-app.png" alt-text="Bereitgestellte Anwendung":::

### <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Hinzufügen einer benutzerdefinierten Domäne](custom-domain.md)

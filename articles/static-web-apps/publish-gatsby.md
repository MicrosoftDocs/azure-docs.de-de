---
title: 'Tutorial: Veröffentlichen einer Gatsby-Website in Azure Static Web Apps'
description: In diesem Tutorial wird gezeigt, wie Sie eine Gatsby-Anwendung in Azure Static Web Apps bereitstellen.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.custom: devx-track-js
ms.openlocfilehash: 4430ed34858077b13b4fec69756c1c7e9f3ef7ac
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652357"
---
# <a name="tutorial-publish-a-gatsby-site-to-azure-static-web-apps-preview"></a>Tutorial: Veröffentlichen einer Gatsby-Website in Azure Static Web Apps (Vorschauversion)

In diesem Artikel wird gezeigt, wie Sie eine [Gatsby](https://gatsbyjs.org)-Webanwendung erstellen und in [Azure Static Web Apps](overview.md) bereitstellen. Das Ergebnis ist eine neue Static Web Apps-Website (mit der zugehörigen GitHub Actions-Instanz), mit der Sie steuern können, wie die App erstellt und veröffentlicht wird.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> - Erstellen einer Gatsby-App
> - Einrichten einer Azure Static Web Apps-Website
> - Bereitstellen der Gatsby-App in Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Falls Sie über keins verfügen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/).
- Ein GitHub-Konto. Falls Sie über keins verfügen, können Sie ein [kostenloses Konto erstellen](https://github.com/join).
- Installation von [Node.js](https://nodejs.org)

## <a name="create-a-gatsby-app"></a>Erstellen einer Gatsby-App

Erstellen Sie mithilfe der Gatsby-Befehlszeilenschnittstelle (Command Line Interface, CLI) eine Gatsby-App:

1. Öffnen Sie ein Terminal.
1. Verwenden Sie das Tool [npx](https://www.npmjs.com/package/npx) zum Erstellen einer neuen App mit der Gatsby-Befehlszeilenschnittstelle. Dies kann einige Minuten dauern.

   ```bash
   npx gatsby new static-web-app
   ```

1. Navigieren Sie zu der neu erstellten App.

   ```bash
   cd static-web-app
   ```

1. Initialisieren eines Git-Repositorys

   ```bash
   git init
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Übertragen Ihrer Anwendung auf GitHub

Sie benötigen ein Repository auf GitHub, um eine neue Azure Static Web Apps-Ressource erstellen zu können.

1. Erstellen Sie über [https://github.com/new](https://github.com/new) ein leeres GitHub-Repository mit dem Namen **gatsby-static-web-app**. (Erstellen Sie keine README-Datei.)

1. Fügen Sie als Nächstes das GitHub-Repository als Remoteressource zu Ihrem lokalen Repository hinzu. Fügen Sie unbedingt den GitHub-Benutzernamen anstelle des Platzhalters `<YOUR_USER_NAME>` im folgenden Befehl hinzu:

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/gatsby-static-web-app
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

   :::image type="content" source="./media/publish-gatsby/create-in-portal.png" alt-text="Erstellen einer statischen Web-App (Vorschau) im Portal":::

1. Akzeptieren Sie unter _Abonnement_ das angegebene Abonnement, oder wählen Sie in der Dropdownliste ein neues Abonnement aus.

1. Wählen Sie unter _Ressourcengruppe_ die Option **Neu** aus. Geben Sie unter _Name für neue Ressourcengruppe_ den Namen **gatsby-static-web-app** ein, und wählen Sie **OK** aus.

1. Geben Sie anschließend einen Namen für Ihre App im Feld **Name** ein. Gültige Zeichen sind `a-z`, `A-Z`, `0-9` und `-`.

1. Wählen Sie unter _Region_ eine verfügbare Region in Ihrer Nähe aus.

1. Wählen Sie unter _SKU_ die Option **Free** aus.

   :::image type="content" source="./media/publish-gatsby/basic-app-details.png" alt-text="Eingegebene Details":::

1. Klicken Sie auf die Schaltfläche **Mit GitHub anmelden**.

1. Wählen Sie die **Organisation** aus, unter der Sie das Repository erstellt haben.

1. Wählen Sie unter _Repository_ die Option **gatsby-static-web-app** aus.

1. Wählen Sie unter _Branch_ die Option **main** aus.

   :::image type="content" source="./media/publish-gatsby/completed-github-info.png" alt-text="Eingegebene GitHub-Informationen":::

### <a name="build"></a>Entwickeln

Fügen Sie als Nächstes Konfigurationseinstellungen hinzu, die beim Buildprozess zum Erstellen der App verwendet werden.

1. Klicken Sie auf die Schaltfläche **Weiter: Erstellen >** , um die Buildkonfiguration zu bearbeiten

1. Legen Sie zum Konfigurieren der Einstellungen des Schritts in GitHub Actions die Option _App-Speicherort_ auf **/** fest.

1. Legen Sie _Speicherort für App-Artefakte_ auf **Öffentlich** fest.

   Ein Wert für _API-Speicherort_ ist nicht erforderlich, da Sie hier keine API bereitstellen.

   :::image type="content" source="./media/publish-gatsby/build-details.png" alt-text="Buildeinstellungen":::

### <a name="review-and-create"></a>Überprüfen und Erstellen

1. Klicken Sie auf die Schaltfläche **Bewerten + erstellen**, um sicherzustellen, dass alle Details stimmen.

1. Klicken Sie auf **Erstellen**, um mit der Erstellung der statischen App Service-Web-App zu beginnen und einen GitHub Actions-Vorgang für die Bereitstellung anzugeben.

1. Wenn die Bereitstellung abgeschlossen ist, klicken Sie auf **Zu Ressource wechseln**.

1. Klicken Sie auf dem Ressourcenbildschirm auf den Link _URL_, um Ihre bereitgestellte Anwendung zu öffnen. Sie müssen ggf. ein bis zwei Minuten warten, bis der GitHub Actions-Vorgang abgeschlossen ist.

   :::image type="content" source="./media/publish-gatsby/deployed-app.png" alt-text="Bereitgestellte Anwendung":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Hinzufügen einer benutzerdefinierten Domäne](custom-domain.md)

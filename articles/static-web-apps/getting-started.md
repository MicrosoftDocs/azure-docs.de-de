---
title: 'Schnellstart: Erstellen Ihrer ersten statischen Web-App mit Azure Static Web Apps'
description: Hier erfahren Sie, wie Sie eine Azure Static Web Apps-Website erstellen.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: a78f74b4aadd26af141ed84ca99a092693f56af5
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369561"
---
# <a name="quickstart-building-your-first-static-web-app"></a>Schnellstart: Erstellen Ihrer ersten statischen Web-App

Azure Static Web Apps veröffentlicht eine Website in einer Produktionsumgebung, indem Apps aus einem GitHub-Repository erstellt werden. In dieser Schnellstartanleitung stellen Sie mithilfe der Visual Studio Code-Erweiterung eine Webanwendung in Azure Static Web Apps bereit.

Falls Sie noch nicht über ein Azure-Abonnement verfügen, können Sie ein [kostenloses Testkonto](https://azure.microsoft.com/free) erstellen.

## <a name="prerequisites"></a>Voraussetzungen

- [GitHub](https://github.com) -Konto
- [Azure](https://portal.azure.com)-Konto
- [Visual Studio Code](https://code.visualstudio.com)
- [Azure Static Web Apps-Erweiterung für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Öffnen Sie als Nächstes Visual Studio Code, und navigieren Sie zu **Datei > Ordner öffnen**, um das Repository zu öffnen, das Sie soeben im Editor auf Ihrem Computer geklont haben.

## <a name="create-a-static-web-app"></a>Erstellen einer statischen Web-App

1. Wählen Sie in Visual Studio Code auf der Aktivitätsleiste das Azure-Logo aus, um das Fenster mit den Azure-Erweiterungen zu öffnen.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Azure-Logo":::

    > [!NOTE]
    > Azure- und GitHub-Anmeldung ist erforderlich. Wenn Sie sich noch nicht über Visual Studio Code bei Azure und GitHub angemeldet haben, werden Sie von der Erweiterung aufgefordert, sich während der Erstellung bei beiden Komponenten anzumelden.

1. Bewegen Sie den Mauszeiger auf die Bezeichnung _Static Web Apps_, und wählen Sie das **Pluszeichen** aus.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Anwendungsname":::

1. Die Befehlspalette wird oben im Editor geöffnet, und Sie werden zur Eingabe eines Namens für Ihre Anwendung aufgefordert.

    Geben Sie **my-first-static-web-app** ein, und drücken Sie die **EINGABETASTE**.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Erstellen einer statischen Web-App":::

1. Wählen Sie den Branch **master** aus, und drücken Sie die **EINGABETASTE**.

    :::image type="content" source="media/getting-started/extension-branch.png" alt-text="Branchname":::

1. Wählen Sie **/** als Speicherort für den Anwendungscode aus, und drücken Sie die **EINGABETASTE**.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Speicherort des Anwendungscodes":::

1. Die Erweiterung sucht nach dem Speicherort der API in Ihrer Anwendung. In diesem Artikel wird keine API implementiert.

    Wählen Sie **Vorerst überspringen** aus, und drücken Sie die **EINGABETASTE**.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="API-Speicherort":::

1. Wählen Sie den Speicherort aus, an dem Dateien für die Produktion in Ihrer App erstellt werden.

    # <a name="no-framework"></a>[Kein Framework](#tab/vanilla-javascript)

    Deaktivieren Sie das Kontrollkästchen, und drücken Sie die **EINGABETASTE**.

    :::image type="content" source="media/getting-started/extension-artifact-no-framework.png" alt-text="Pfad zu den App-Dateien":::

    # <a name="angular"></a>[Angular](#tab/angular)

    Geben Sie **dist/angular-basic** ein, und drücken Sie die **EINGABETASTE**.

    :::image type="content" source="media/getting-started/extension-artifact-angular.png" alt-text="Pfad zu den Angular-App-Dateien":::

    # <a name="react"></a>[React](#tab/react)

    Geben Sie **build** ein, und drücken Sie die **EINGABETASTE**.

    :::image type="content" source="media/getting-started/extension-artifact-react.png" alt-text="Pfad zu den React-App-Dateien":::

    # <a name="vue"></a>[Vue](#tab/vue)

    Geben Sie **dist** ein, und drücken Sie die **EINGABETASTE**.

    :::image type="content" source="media/getting-started/extension-artifact-vue.png" alt-text="Pfad zu den Vue-App-Dateien":::

    ---

1. Wählen Sie den nächstgelegenen Standort aus, und drücken Sie die **EINGABETASTE**.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Ressourcenspeicherort":::

1. Nach der Erstellung der App wird eine Bestätigungsbenachrichtigung in Visual Studio Code angezeigt.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Bestätigung der Erstellung":::

1. Navigieren Sie im Visual Studio Code-Explorer-Fenster zu dem Knoten, der den Namen Ihres Abonnements aufweist, und erweitern Sie ihn. Beachten Sie, dass es ein paar Minuten dauern kann, bis die Bereitstellung abgeschlossen ist. Kehren Sie dann zum Abschnitt „Static Web Apps“ zurück, wählen Sie den Namen Ihrer App aus, und klicken Sie dann mit der rechten Maustaste auf „my-first-static-web-app“, und wählen Sie „Im Portal öffnen“ aus, um die App im Azure-Portal anzuzeigen.

    :::image type="content" source="media/getting-started/extension-open-in-portal.png" alt-text="Öffnen des Portals":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht weiter nutzen möchten, können Sie die Azure Static Web Apps-Instanz über die Erweiterung löschen.

Kehren Sie im Fenster des Visual Studio Code-Explorers zum Abschnitt _Static Web Apps_ zurück, klicken Sie mit der rechten Maustaste auf **my-first-static-web-app**, und wählen Sie **Löschen** aus.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Löschen einer App":::

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Hinzufügen einer API zu Azure Static Web Apps (Vorschauversion) mit Azure Functions](add-api.md)

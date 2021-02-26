---
title: 'Erstellen einer Python-Funktion mit Visual Studio Code: Azure Functions'
description: Erfahren Sie, wie Sie eine Python-Funktion erstellen und dann das lokale Projekt für serverloses Hosting in Azure Functions unter Verwendung der Azure Functions-Erweiterung in Visual Studio Code veröffentlichen.
ms.topic: quickstart
ms.date: 11/04/2020
ms.custom: devx-track-python
ms.openlocfilehash: e022843f95e5d5b52a15eaab2d28b6b9eb923006
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493565"
---
# <a name="quickstart-create-a-function-in-azure-with-python-using-visual-studio-code"></a>Schnellstart: Erstellen einer Funktion in Azure mit Python mithilfe von Visual Studio Code

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

In diesem Artikel wird mithilfe von Visual Studio Code eine Python-Funktion erstellt, die auf HTTP-Anforderungen reagiert. Der Code wird lokal getestet und anschließend in der serverlosen Umgebung von Azure Functions bereitgestellt.

Im Rahmen dieser Schnellstartanleitung fallen in Ihrem Azure-Konto ggf. geringfügige Kosten im Centbereich an.

Es gibt auch eine [CLI-basierte Version](create-first-function-cli-python.md) dieses Artikels.

## <a name="configure-your-environment"></a>Konfigurieren Ihrer Umgebung

Vergewissern Sie sich zunähst, dass Folgendes vorhanden ist:

+ Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools), Version 3.x.

+ [Von Azure Functions unterstützte Python-Versionen](supported-languages.md#languages-by-runtime-version)

+ [Visual Studio Code](https://code.visualstudio.com/) auf einer der [unterstützten Plattformen](https://code.visualstudio.com/docs/supporting/requirements#_platforms)

+ [Python-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-python.python) für Visual Studio Code  

+ [Azure Functions-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) für Visual Studio Code

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Erstellen Ihres lokalen Projekts

In diesem Abschnitt wird mithilfe von Visual Studio Code ein lokales Azure Functions-Projekt in Python erstellt. Weiter unten in diesem Artikel wird der Funktionscode in Azure veröffentlicht.

1. Wählen Sie auf der Aktivitätsleiste das Azure-Symbol und anschließend im Bereich **Azure: Funktionen** das Symbol **Neues Projekt erstellen** aus.

    ![Auswählen von „Neues Projekt erstellen“](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Wählen Sie einen Verzeichnisspeicherort für Ihren Projektarbeitsbereich und anschließend **Auswählen** aus.

    > [!NOTE]
    > Diese Schritte sollten außerhalb eines Arbeitsbereichs ausgeführt werden. Wählen Sie in diesem Fall keinen Projektordner aus, der Teil eines Arbeitsbereichs ist.

1. Geben Sie nach entsprechender Aufforderung Folgendes ein:

    + **Select a language for your function project** (Wählen Sie eine Sprache für Ihr Funktionsprojekt aus.): Wählen Sie die Option `Python`.

    + **Select a Python alias to create a virtual environment** (Wählen Sie einen Python-Alias zum Erstellen einer virtuellen Umgebung aus.): Wählen Sie den Speicherort Ihres Python-Interpreters aus.  
    Sollte der Speicherort nicht angezeigt werden, geben Sie den vollständigen Pfad Ihrer Python-Binärdatei ein.  

    + **Select a template for your project's first function** (Wählen Sie die Vorlage für die erste Funktion Ihres Projekts aus.): Wählen Sie die Option `HTTP trigger`.

    + **Provide a function name** (Geben Sie einen Funktionsnamen an.): Geben Sie `HttpExample`ein.

    + **Autorisierungsstufe:** Wählen Sie `Anonymous` aus, damit Ihr Funktionsendpunkt von jedem Benutzer aufgerufen werden kann. Weitere Informationen zur Autorisierungsstufe finden Sie unter [Autorisierungsschlüssel](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Select how you would like to open your project** (Wählen Sie aus, wie Sie Ihr Projekt öffnen möchten.): Wählen Sie die Option `Add to workspace`.

1. Auf der Grundlage dieser Informationen generiert Visual Studio Code ein Azure Functions-Projekt mit einem HTTP-Trigger. Die lokalen Projektdateien können im Explorer angezeigt werden. Weitere Informationen zu den erstellten Dateien finden Sie unter [Generierte Projektdateien](functions-develop-vs-code.md#generated-project-files).

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Nachdem Sie sich vergewissert haben, dass die Funktion auf Ihrem lokalen Computer korrekt ausgeführt wird, können Sie das Projekt mithilfe von Visual Studio Code direkt in Azure veröffentlichen.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-the-project-to-azure"></a>Veröffentlichen des Projekts in Azure

In diesem Abschnitt erstellen Sie eine Funktions-App sowie zugehörige Ressourcen in Ihrem Azure-Abonnement und stellen anschließend Ihren Code bereit. 

> [!IMPORTANT]
> Beim Veröffentlichen in einer vorhandenen Funktions-App wird der Inhalt dieser App in Azure überschrieben. 

1. Wählen Sie auf der Aktivitätsleiste das Azure-Symbol und anschließend im Bereich **Azure: Funktionen** die Schaltfläche **Deploy to function app...** (In Funktions-App bereitstellen...) aus.

    ![Veröffentlichen Ihres Projekts in Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Geben Sie nach entsprechender Aufforderung Folgendes ein:

    + **Wählen Sie einen Ordner aus:** Wählen Sie einen Ordner in Ihrem Arbeitsbereich aus, oder navigieren Sie zu einem Ordner, der Ihre Funktions-App enthält.   
    Diese Option wird nicht angezeigt, wenn Sie bereits eine gültige Funktions-App geöffnet haben.

    + **Wählen Sie das Abonnement aus:** Wählen Sie das zu verwendende Abonnement aus.  
    Wenn Sie nur über ein Abonnement verfügen, wird diese Option nicht angezeigt.

    + **Select Function App in Azure:** (Wählen Sie die Funktions-App in Azure aus:) Wählen Sie die Option `+ Create new Function App`.  
    (Wählen Sie nicht die Option `Advanced` aus, diese wird im vorliegenden Artikel nicht behandelt.)

    + **Enter a globally unique name for the function app:** (Geben Sie einen global eindeutigen Namen für die Funktions-App ein:) Geben Sie einen Namen ein, der in einem URL-Pfad gültig ist. Der eingegebene Name wird überprüft, um sicherzustellen, dass er in Azure Functions eindeutig ist. 

    + **Select a runtime:** (Wählen Sie eine Runtime aus:) Wählen Sie die lokal ausgeführte Python-Version aus. Sie können den Befehl `python --version` ausführen, um Ihre Version zu überprüfen.

    + **Wählen Sie einen Speicherort für neue Ressourcen aus:**  Wählen Sie eine [Region](https://azure.microsoft.com/regions/) in Ihrer Nähe aus, um eine bessere Leistung zu erzielen.

    Die Erweiterung zeigt den Status einzelner Ressourcen an, während diese in Azure im Benachrichtigungsbereich erstellt werden.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Benachrichtigung über die Erstellung von Azure-Ressourcen":::

1. Nach Abschluss des Vorgangs werden in Ihrem Abonnement die folgenden Azure-Ressourcen erstellt, deren Namen auf dem Namen Ihrer Funktions-App basieren:

    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    Nach der Erstellung der Funktions-App wird eine Benachrichtigung angezeigt, und das Bereitstellungspaket wird angewendet. 

    [!INCLUDE [functions-vs-code-create-tip](../../includes/functions-vs-code-create-tip.md)]

4. Wählen Sie in dieser Benachrichtigungen **Ausgabe anzeigen** aus, um die Erstellungs- und Bereitstellungsergebnisse (auch für die von Ihnen erstellten Azure-Ressourcen) anzuzeigen. Wenn Sie die Benachrichtigung übersehen haben, wählen Sie das Glockensymbol in der unteren rechten Ecke aus, um sie erneut anzuzeigen.

    ![Benachrichtigung nach Abschluss der Erstellung](./media/functions-create-first-function-vs-code/function-create-notifications.png)

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Nächste Schritte

Sie haben [Visual Studio Code](functions-develop-vs-code.md?tabs=python) genutzt, um eine Funktions-App mit einer einfachen Funktion zu erstellen, die über HTTP ausgelöst wird. Im nächsten Artikel erweitern Sie diese Funktion, indem Sie eine Verbindung mit Azure Storage herstellen. Weitere Informationen zum Herstellen einer Verbindung mit anderen Azure-Diensten finden Sie unter [Hinzufügen von Bindungen zu einer vorhandenen Funktion in Azure Functions](add-bindings-existing-function.md?tabs=python). 

> [!div class="nextstepaction"]
> [Herstellen einer Verbindung mit einer Azure Storage-Warteschlange](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-python)

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/python-functions-qs-survey)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions

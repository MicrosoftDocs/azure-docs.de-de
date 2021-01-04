---
title: Erstellen einer Funktions-App unter Linux im Azure-Portal
description: Hier erfahren Sie, wie Sie unter Linux Ihre erste Azure-Funktion über das Azure-Portal erstellen.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 20390239ed58e42749e9a3bae472a2f3f6324bb2
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181253"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Erstellen einer Funktions-App unter Linux in einem Azure App Service-Plan

Mit Azure Functions können Sie Funktionen unter Linux in einem Azure App Service-Standardcontainer hosten. In diesem Artikel wird Schritt für Schritt die Verwendung des [Azure-Portals](https://portal.azure.com) zum Erstellen einer unter Linux gehosteten Funktions-App beschrieben, die in einem [App Service-Plan](functions-scale.md#app-service-plan) ausgeführt wird. Sie können auch [einen eigenen benutzerdefinierten Container nutzen](functions-create-function-linux-custom-image.md).

![Erstellen einer Funktionen-App im Azure-Portal](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter <https://portal.azure.com> mit Ihrem Azure-Konto beim Azure-Portal an.

## <a name="create-a-function-app"></a>Erstellen einer Funktionen-App

Sie müssen über eine Funktions-App verfügen, die die Ausführung Ihrer Funktionen unter Linux hostet. Die Funktions-App bietet eine Umgebung für die Ausführung des Funktionscodes. Mit ihr können Sie Funktionen zu logischen Einheiten gruppieren. Dies erleichtert die Verwaltung, Bereitstellung, Skalierung und Freigabe von Ressourcen. In diesem Artikel erstellen Sie bei der Erstellung Ihrer Funktions-App einen App Service-Plan.

1. Wählen Sie im Menü des Azure-Portals oder auf der **Startseite** die Option **Ressource erstellen** aus.

1. Wählen Sie auf der Seite **Neu** die Option **Compute** > **Funktions-App** aus.

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-flow.png" alt-text="Erstellen einer Funktions-App im Azure-Portal":::

1. Verwenden Sie auf der Seite **Grundlagen** die Funktions-App-Einstellungen, die in der folgenden Tabelle angegeben sind.

    | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG |
    | ------------ | ---------------- | ----------- |
    | **Abonnement** | Ihr Abonnement | Das Abonnement, unter dem diese neue Funktions-App erstellt wird. |
    | **[Ressourcengruppe](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Der Name der neuen Ressourcengruppe, in der die Funktionen-App erstellt wird |
    | **Name der Funktions-App** | Global eindeutiger Name | Der Name, der Ihre neue Funktionen-App bezeichnet Gültige Zeichen sind `a-z` (Groß-/Kleinschreibung nicht beachtet), `0-9` und `-`.  |
    |**Veröffentlichen**| **Code** (Standard) | Option zum Veröffentlichen von Codedateien oder eines Docker-Containers. |
    | **Laufzeitstapel** | Bevorzugte Sprache | Wählen Sie eine Runtime aus, die Ihre bevorzugte Programmiersprache für Funktionen unterstützt. Wählen Sie **.NET Core** für C#- und F#-Funktionen aus. |
    |**Version**| Versionsnummer | Wählen Sie die Version der installierten Runtime aus.  |
    |**Region**| Bevorzugte Region | Wählen Sie eine [Region](https://azure.microsoft.com/regions/) in Ihrer Nähe oder in der Nähe von anderen Diensten aus, auf die Ihre Funktionen zugreifen. |

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-basics-linux.png" alt-text="Seite „Grundlagen“":::

1. Wählen Sie **Weiter: Hosting** aus. Geben Sie auf der Seite **Hosting** die folgenden Einstellungen ein.

    | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG |
    | ------------ | ---------------- | ----------- |
    | **[Speicherkonto](../storage/common/storage-account-create.md)** |  Global eindeutiger Name |  Erstellen Sie ein Speicherkonto, das von Ihrer Funktions-App verwendet wird. Speicherkontonamen müssen zwischen 3 und 24 Zeichen lang sein und dürfen nur Zahlen und Kleinbuchstaben enthalten. Sie können auch ein vorhandenes Konto verwenden, das die [Anforderungen an das Speicherkonto](../azure-functions/functions-scale.md#storage-account-requirements) erfüllen muss. |
    |**Betriebssystem**| **Linux** | Ein Betriebssystem ist für Sie basierend auf Ihrer Runtimestapelauswahl vorab ausgewählt, aber Sie können die Einstellung ggf. ändern. |
    | **[Plan](../azure-functions/functions-scale.md)** | **Verbrauch (serverlos)** | Der Hostingplan, der definiert, wie Ihre Ressourcen der Funktionen-App zugewiesen werden Im Standard-**Verbrauchstarif** werden Ressourcen so dynamisch hinzugefügt, wie dies für Ihre Funktionen erforderlich ist. Beim [serverlosen Hosting](https://azure.microsoft.com/overview/serverless-computing/) bezahlen Sie nur die Zeit, in der Ihre Funktionen ausgeführt werden. Bei der Ausführung in einem App Service-Plan müssen Sie die [Skalierung Ihrer Funktions-App](../azure-functions/functions-scale.md) verwalten.  |

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-hosting-linux.png" alt-text="Seite „Hosting“":::

1. Wählen Sie **Weiter: Überwachung** aus. Geben Sie auf der Seite **Überwachung** die folgenden Einstellungen ein.

    | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../azure-functions/functions-monitoring.md)** | **Ja** (Standard) | Erstellt eine Application Insights-Ressource mit dem gleichen *App-Namen* in der nächstgelegenen unterstützten Region. Durch Erweitern dieser Einstellung oder Auswählen von **Neu erstellen** können Sie den Application Insights-Namen ändern oder eine andere Region in einer [Azure-Geografie](https://azure.microsoft.com/global-infrastructure/geographies/) auswählen, in der Sie Ihre Daten speichern möchten. |

   :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-monitoring-linux.png" alt-text="Seite „Überwachung“":::

1. Wählen Sie **Bewerten + erstellen** aus, um die App-Konfigurationsauswahl zu überprüfen.

1. Überprüfen Sie auf der Seite **Bewerten + erstellen** Ihre Einstellungen, und wählen Sie dann **Erstellen** aus, um die Funktions-App bereitzustellen.

1. Wählen Sie oben rechts im Portal das **Benachrichtigungssymbol** aus, und achten Sie auf die Meldung **Bereitstellung erfolgreich**.

1. Wählen Sie **Zu Ressource wechseln**, um Ihre neue Funktionen-App anzuzeigen. Sie können auch die Option **An Dashboard anheften** auswählen. Wenn Sie die Funktions-App anheften, können Sie einfacher über das Dashboard auf sie zugreifen.

    ![Bereitstellungsbenachrichtigung](./media/create-function-app-linux-app-service-plan/function-app-create-notification2.png)

    Nachdem Ihre Funktions-App bereitgestellt wurde, kann es trotzdem noch einige Minuten dauern, bis die Initialisierung vollständig abgeschlossen ist.

Erstellen Sie als Nächstes eine Funktion in der neuen Funktions-App.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Erstellen einer HTTP-Triggerfunktion

In diesem Abschnitt wird veranschaulicht, wie Sie im Portal in Ihrer neuen Funktions-App eine Funktion erstellen.

> [!NOTE]
> Die Entwicklungsoberfläche im Portal kann zum Ausprobieren von Azure Functions nützlich sein. Für die meisten Szenarien ist es ratsam, Ihre Funktionen lokal zu entwickeln und das Projekt in Ihrer Funktions-App zu veröffentlichen, indem Sie entweder [Visual Studio Code](./create-first-function-vs-code-csharp.md#create-an-azure-functions-project) oder die [Azure Functions Core Tools](functions-run-local.md#create-a-local-functions-project) verwenden.  

1. Wählen Sie im linken Menü des Fensters **Funktionen** die Option **Funktionen** aus, und wählen Sie dann im obersten Menü **Hinzufügen** aus. 
 
1. Wählen Sie im Fenster **Neue Funktion** die Option **HTTP-Trigger** aus.

    ![Auswählen der HTTP-Triggerfunktion](./media/create-function-app-linux-app-service-plan/function-app-select-http-trigger.png)

1. Akzeptieren Sie im Fenster **Neue Funktion** den Standardnamen für **Neue Funktion**, oder geben Sie einen neuen Namen ein. 

1. Wählen Sie **Anonym** in der Dropdownliste **Autorisierungsstufe** aus, und wählen Sie dann **Funktion erstellen** aus.

    Azure erstellt die HTTP-Triggerfunktion. Nun können Sie die neue Funktion ausführen, indem Sie eine HTTP-Anforderung senden.

## <a name="test-the-function"></a>Testen der Funktion

1. Wählen Sie in Ihrer neuen HTTP-Triggerfunktion im Menü auf der linken Seite **Code + testen** aus, und wählen Sie dann im obersten Menü **Funktions-URL abrufen** aus.

    ![Auswählen von „Funktions-URL abrufen“](./media/create-function-app-linux-app-service-plan/function-app-select-get-function-url.png)

1. Wählen Sie im Dialogfeld **Funktions-URL abrufen** im Dropdownmenü die Option **default** (Standard) und anschließend das Symbol **In Zwischenablage kopieren** aus. 

    ![Kopieren der URL der Funktion aus dem Azure-Portal](./media/create-function-app-linux-app-service-plan/function-app-develop-tab-testing.png)

1. Fügen Sie die URL der Funktion in die Adressleiste Ihres Browsers ein. Fügen Sie den Wert der Abfragezeichenfolge `?name=<your_name>` am Ende der URL hinzu, und drücken Sie die EINGABETASTE, um die Anforderung auszuführen. 

    Das folgende Beispiel zeigt die Antwort im Browser:

    ![Funktionsantwort im Browser.](./media/create-function-app-linux-app-service-plan/function-app-browser-testing.png)

    Die Anfrage-URL enthält einen Schlüssel, der standardmäßig über HTTP auf Ihre Funktion zugreifen muss.

1. Wenn Ihre Funktion ausgeführt wird, werden Nachverfolgungsinformationen in die Protokolle geschrieben. Um die Nachverfolgungsausgabe anzuzeigen, kehren Sie zur Seite **Code + testen** im Portal zurück, und erweitern Sie den Pfeil **Protokolle** am unteren Rand der Seite.

   ![Viewer der Funktionsprotokolle im Azure-Portal](./media/create-function-app-linux-app-service-plan/function-view-logs.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

Sie haben eine Funktions-App mit einer einfachen HTTP-Triggerfunktion erstellt.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Weitere Informationen finden Sie unter [HTTP- und Webhookbindungen in Azure Functions](functions-bindings-http-webhook.md).
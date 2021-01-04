---
title: 'Tutorial: Erstellen einer statischen Web-App mit Blazor in Azure Static Web Apps'
description: Erfahren Sie, wie Sie eine Azure Static Web Apps-Website mit Blazor erstellen.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: cshoe
ms.openlocfilehash: 0086f7f68fd05d6925d19c7ab457fbc125e36be4
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350227"
---
# <a name="tutorial-building-a-static-web-app-with-blazor-in-azure-static-web-apps"></a>Tutorial: Erstellen einer statischen Web-App mit Blazor in Azure Static Web Apps

Azure Static Web Apps veröffentlicht eine Website in einer Produktionsumgebung, indem Apps aus einem GitHub-Repository erstellt werden. In diesem Tutorial stellen Sie eine Webanwendung in Azure Static Web Apps über das Azure-Portal bereit.

Falls Sie noch nicht über ein Azure-Abonnement verfügen, können Sie ein [kostenloses Testkonto](https://azure.microsoft.com/free) erstellen.

## <a name="prerequisites"></a>Voraussetzungen

- [GitHub](https://github.com) -Konto
- [Azure](https://portal.azure.com)-Konto

## <a name="application-overview"></a>Anwendungsübersicht

Mit Azure Static Web Apps können Sie statische Webanwendungen erstellen, die von einem serverlosen Back-End gestützt werden. Im folgenden Tutorial wird gezeigt, wie Sie eine C#-Blazor-Webanwendung bereitstellen, die Wetterdaten zurückgibt.

:::image type="content" source="./media/deploy-blazor/blazor-app-complete.png" alt-text="Fertig gestellte Blazor-App":::

Die in diesem Tutorial gezeigte App setzt sich aus drei verschiedenen Visual Studio-Projekten zusammen:

- **Api**: Die Azure Functions-C#-Anwendung, die den API-Endpunkt zur Bereitstellung von Wetterinformationen für die statische App implementiert. Die Funktion **WeatherForecastFunction** gibt ein Array mit `WeatherForecast`-Objekten zurück.

- **Client:** Das Front-End-Blazor-Webassemblyprojekt. Um sicherzustellen, dass alle Routen zur Datei _index.html_ führen, ist eine [Fallbackroute](#fallback-route) implementiert.

- **Gemeinsam genutzt**: Enthält gemeinsame Klassen, die sowohl von der API als auch von den Clientprojekten referenziert werden, damit Daten vom API-Endpunkt an die Front-End-Web-App übertragen werden können. Die Klasse [`WeatherForecast`](https://github.com/staticwebdev/blazor-starter/blob/main/Shared/WeatherForecast.cs) wird von beiden Apps gemeinsam genutzt.

Diese Projekte werden zum Erstellen einer Blazor-Webassemblyanwendung kombiniert, die in dem von einem API-Back-End unterstützten Browser ausgeführt wird.

## <a name="fallback-route"></a>Fallbackroute

Die Anwendung macht URLs wie _/counter_ und _/fetchdata_ verfügbar, die bestimmten Routen der Anwendung zugeordnet sind. Da diese App als Single-Page-Webanwendung implementiert ist, führt jede Route zur Datei _index.html_. Um sicherzustellen, dass bei jeglichen Anforderungen für einen beliebigen Pfad die Datei _index.html_ zurückgegeben wird, ist in der Datei _routes.json_ eine [Fallbackroute](./routes.md#fallback-routes) implementiert. Diese Datei befindet sich im Ordner _wwwroot_ des Clientprojekts.

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

Mit der obigen Konfiguration wird sichergestellt, dass bei jeglichen Anforderungen an eine beliebige Route in der App die Seite _index.html_ zurückgegeben wird.

## <a name="create-a-repository"></a>Erstellen eines Repositorys

In diesem Artikel wird ein GitHub-Vorlagenrepository verwendet, um Ihnen den Einstieg zu erleichtern. Die Vorlage enthält eine Starter-App, die in Azure Static Web Apps bereitgestellt wird.

1. Stellen Sie sicher, dass Sie bei GitHub angemeldet sind, und navigieren Sie zum folgenden Speicherort, um ein neues Repository zu erstellen:
    - [https://github.com/staticwebdev/blazor-starter/generate](https://github.com/login?return_to=/staticwebdev/blazor-starter/generate)
1. Geben Sie für Ihr Repository den Namen **my-first-static-blazor-app** ein.

## <a name="create-a-static-web-app"></a>Erstellen einer statischen Web-App

Nachdem das Repository erstellt wurde, erstellen Sie im Azure-Portal eine statische Web-App.

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
1. Wählen Sie **Ressource erstellen** aus.
1. Suchen Sie nach **Static Web Apps**.
1. Wählen Sie **Static Web Apps (Vorschau)** aus.
1. Klicken Sie auf **Erstellen**

Konfigurieren Sie im Abschnitt _Grundlagen_ zunächst Ihre neue App, und verknüpfen Sie sie mit einem GitHub-Repository.

:::image type="content" source="media/deploy-blazor/basics.png" alt-text="Registerkarte „Grundlagen“":::

1. Wählen Sie Ihr _Azure-Abonnement_ aus.
1. Wählen Sie eine neue _Ressourcengruppe_ aus, oder erstellen Sie sie.
1. Geben Sie als App-Namen **my-first-static-blazor-app** ein.
    - Gültige Zeichen sind `a-z` (Groß-/Kleinschreibung nicht beachtet), `0-9` und `-`.
1. Wählen Sie eine _Region_ aus, die in Ihrer Nähe liegt.
1. Wählen Sie für _SKU_ die Option **Free** aus.
1. Wählen Sie die Schaltfläche **Mit GitHub anmelden** aus, und führen Sie die Authentifizierung mit GitHub durch.

Geben Sie nach der Anmeldung mit GitHub die Informationen zum Repository ein.

:::image type="content" source="media/deploy-blazor/repository-details.png" alt-text="Details zum Repository":::

1. Wählen Sie Ihre bevorzugte _Organisation_ aus.
1. Wählen Sie in der Dropdownliste _Repository_ den Eintrag **my-first-static-blazor-app** aus.
1. Wählen Sie in der Dropdownliste _Branch_ den Eintrag **Hauptbranch** aus.

    Wenn Sie keine Repositorys sehen, müssen Sie möglicherweise den Azure Static Web Apps in GitHub autorisieren. Navigieren Sie zu Ihrem GitHub-Repository, und wechseln Sie zu **Einstellungen > Anwendungen > Autorisierte OAuth-Apps**, wählen Sie **Azure Static Web Apps** und dann **Erteilen** aus. Bei Organisationsrepositorys müssen Sie Besitzer der Organisation sein, um die Berechtigungen erteilen zu können.

1. Fügen Sie im Abschnitt _Builddetails_ Blazor-spezifische Konfigurationsdetails hinzu.

    - Wählen Sie in der Dropdownliste _Buildvoreinstellungen_ den Eintrag **Blazor** aus, und übernehmen Sie die Standardwerte.

1. Klicken Sie auf **Überprüfen + erstellen**.

    :::image type="content" source="media/deploy-blazor/review-create.png" alt-text="Schaltfläche „Bewerten + erstellen“":::

1. Klicken Sie auf **Erstellen**.

    :::image type="content" source="media/deploy-blazor/create-button.png" alt-text="Schaltfläche „Erstellen“":::

1. Wählen Sie **Zu Ressource wechseln** aus.

    :::image type="content" source="media/deploy-blazor/resource-button.png" alt-text="Schaltfläche „Zu Ressource wechseln“":::

## <a name="view-the-website"></a>Anzeigen der Website

Für die Bereitstellung einer statischen App gelten zwei Aspekte. Der erste Aspekt ist die Bereitstellung der zugrunde liegenden Azure-Ressourcen, aus denen Ihre App besteht. Der zweite Aspekt ist ein GitHub Actions-Workflow, mit dem Ihre Anwendung erstellt und veröffentlicht wird.

Bevor Sie zu Ihrer neuen statischen Website navigieren können, muss zuerst die Ausführung des Buildvorgangs für die Bereitstellung abgeschlossen sein.

Im Übersichtsfenster von Azure Static Web Apps werden einige Links angezeigt, die Ihnen als Hilfe bei der Interaktion mit Ihrer Web-App dienen.

:::image type="content" source="./media/deploy-blazor/overview-window.png" alt-text="Übersichtsfenster":::

1. Wenn Sie auf das Banner _Click here to check the status of your GitHub Actions runs_ (Hier klicken, um den Status Ihrer GitHub Actions-Ausführungen zu überprüfen) klicken, gelangen Sie zu den GitHub Actions-Instanzen, die für Ihr Repository ausgeführt werden. Nachdem Sie sich vergewissert haben, dass der Bereitstellungsauftrag abgeschlossen ist, können Sie über die generierte URL zu Ihrer Website navigieren.

2. Nachdem der GitHub Actions-Workflow abgeschlossen ist, können Sie den _URL_-Link auswählen, um die Website auf einer neuen Registerkarte zu öffnen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht weiter nutzen möchten, können Sie die Azure Static Web Apps-Instanz mit den folgenden Schritten löschen:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Suchen Sie in der oberen Suchleiste nach **my-first-static-blazor-app**.
1. Wählen Sie den App-Namen aus.
1. Wählen Sie die Schaltfläche **Löschen** aus.
1. Wählen Sie zum Bestätigen des Löschvorgangs **Ja** aus.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Authentifizierung und Autorisierung](./authentication-authorization.md)

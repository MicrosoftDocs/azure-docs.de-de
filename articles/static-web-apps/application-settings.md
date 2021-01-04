---
title: Konfigurieren von Anwendungseinstellungen für Azure Static Web Apps
description: Es wird beschrieben, wie Sie Anwendungseinstellungen für Azure Static Web Apps konfigurieren.
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: buhollan
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 00502c97e3fc06972c27cedf565d77b52f558795
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844811"
---
# <a name="configure-application-settings-for-azure-static-web-apps-preview"></a>Konfigurieren von Anwendungseinstellungen für Azure Static Web Apps (Vorschau)

Anwendungseinstellungen enthalten Konfigurationseinstellungen für Werte, die sich ggf. ändern können, z. B. Datenbank-Verbindungszeichenfolgen. Indem Sie Anwendungseinstellungen hinzufügen, können Sie die Konfigurationseingabe für Ihre App ändern, ohne dass Sie dafür den Anwendungscode ändern müssen.

Für Anwendungseinstellungen gilt Folgendes:

- Sie sind im Ruhezustand verschlüsselt.
- Sie werden in [Staging](review-publish-pull-requests.md)- und Produktionsumgebungen kopiert.

Anwendungseinstellungen werden auch als Umgebungsvariablen bezeichnet.

> [!IMPORTANT]
> Die in diesem Artikel beschriebenen Anwendungseinstellungen gelten nur für die Back-End-API einer statischen Azure-Web-App.
>
> Informationen zur Verwendung von Umgebungsvariablen mit Ihrer Front-End-Webanwendung finden Sie in der Dokumentation zum [JavaScript-Framework](#javascript-frameworks-and-libraries) oder zum [Generator für statische Websites](#static-site-generators).

## <a name="prerequisites"></a>Voraussetzungen

- Eine Azure Static Web Apps-Anwendung
- [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="types-of-application-settings"></a>Arten von Anwendungseinstellungen

Für eine Azure Static Web Apps-Anwendung gelten normalerweise zwei Aspekte. Der erste Aspekt ist die Webanwendung bzw. der statische Inhalt, der per HTML, CSS, JavaScript und mit Bildern dargestellt wird. Der zweite Aspekt ist die Back-End-API, die auf einer Azure Functions-Anwendung basiert.

In diesem Artikel wird veranschaulicht, wie Sie Anwendungseinstellungen für die Back-End-API in Azure Functions verwalten.

Die in diesem Artikel beschriebenen Anwendungseinstellungen können in statischen Webanwendungen nicht verwendet oder referenziert werden. Viele Front-End-Frameworks und Generatoren für statische Websites ermöglichen die Verwendung von Umgebungsvariablen während der Entwicklung. Beim Buildvorgang werden diese Variablen durch die entsprechenden Werte im generierten HTML- oder JavaScript-Code ersetzt. Da Daten im HTML- und JavaScript-Code von Besuchern der Website leicht ermittelt werden können, sollten Sie es vermeiden, vertrauliche Informationen in der Front-End-Anwendung anzuordnen. Einstellungen, die vertrauliche Daten enthalten, sollten am besten im API-Teil Ihrer Anwendung angeordnet werden.

Weitere Informationen zur Verwendung von Umgebungsvariablen mit Ihrem JavaScript-Framework bzw. der -Bibliothek finden Sie in den folgenden Artikeln.

### <a name="javascript-frameworks-and-libraries"></a>JavaScript-Frameworks und -Bibliotheken

- [Angular](https://angular.io/guide/build#configuring-application-environments)
- [React](https://create-react-app.dev/docs/adding-custom-environment-variables/)
- [Svelte](https://linguinecode.com/post/how-to-add-environment-variables-to-your-svelte-js-app)
- [Vue](https://cli.vuejs.org/guide/mode-and-env.html)

### <a name="static-site-generators"></a>Generatoren für statische Websites

- [Gatsby](https://www.gatsbyjs.org/docs/environment-variables/)
- [Hugo](https://gohugo.io/getting-started/configuration/)
- [Jekyll](https://jekyllrb.com/docs/configuration/environments/)

## <a name="about-api-app-settings"></a>Informationen zu API-App-Einstellungen

APIs in Azure Static Web Apps basieren auf Azure Functions. Dies ermöglicht es Ihnen, Anwendungseinstellungen in der Datei _local.settings.json_ zu definieren. In dieser Datei sind die Anwendungseinstellungen in der Eigenschaft `Values` der Konfiguration definiert.

Mit der unten angegebenen Beispieldatei _local.settings.json_ wird veranschaulicht, wie Sie einen Wert für `DATABASE_CONNECTION_STRING` hinzufügen.

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
  }
}
```

Auf in der Eigenschaft `Values` definierte Einstellungen kann im Code in Form von Umgebungsvariablen verwiesen werden, die über das Objekt `process.env` zur Verfügung stehen.

```js
const connectionString = process.env.DATABASE_CONNECTION_STRING;
```

Die Datei `local.settings.json` wird vom GitHub-Repository nicht nachverfolgt, weil vertrauliche Informationen, z. B. Datenbank-Verbindungszeichenfolgen, häufig in der Datei enthalten sind. Da die lokalen Einstellungen auf Ihrem Computer verbleiben, müssen Sie Ihre Einstellungen manuell in Azure hochladen.

Im Allgemeinen erfolgt das Hochladen Ihrer Einstellungen nur unregelmäßig und ist nicht bei jedem Buildvorgang erforderlich.

## <a name="uploading-application-settings"></a>Hochladen von Anwendungseinstellungen

Sie können Anwendungseinstellungen über das Azure-Portal oder mit der Azure CLI konfigurieren.

### <a name="using-the-azure-portal"></a>Verwenden des Azure-Portals

Das Azure-Portal verfügt über eine Oberfläche zum Erstellen, Aktualisieren und Löschen von Anwendungseinstellungen.

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).

1. Suchen Sie in der Suchleiste nach **Static Web Apps**, und wählen Sie die Option aus.

1. Klicken Sie in der Randleiste auf die Option **Konfiguration**.

1. Wählen Sie die Umgebung aus, auf die Sie die Anwendungseinstellungen anwenden möchten. Stagingumgebungen werden beim Generieren eines Pull Requests automatisch erstellt und nach dem Zusammenführen des Pull Requests in die Produktionsumgebung höhergestuft. Sie können Anwendungseinstellungen pro Umgebung festlegen.

1. Klicken Sie auf die Schaltfläche **Hinzufügen**, um eine neue App-Einstellung hinzuzufügen.

    :::image type="content" source="media/application-settings/configuration.png" alt-text="Ansicht für die Konfiguration von Azure Static Web Apps":::

1. Geben Sie einen **Namen** und **Wert** ein.

1. Klicken Sie auf **OK**.

1. Klicken Sie auf **Speichern**.

### <a name="using-the-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

Sie können den Befehl `az rest` verwenden, um für Ihre Einstellungen Massenuploads in Azure durchzuführen. Der Befehl akzeptiert Anwendungseinstellungen als JSON-Objekte in einer übergeordneten Eigenschaft mit dem Namen `properties`.

Die einfachste Möglichkeit, eine JSON-Datei mit den entsprechenden Werten zu erstellen, ist die Erstellung einer geänderten Version Ihrer Datei _local.settings.json_.

1. Fügen Sie Ihrer _GITIGNORE_-Datei den folgenden Eintrag hinzu, um sicherzustellen, dass Ihre neue Datei mit den vertraulichen Daten nicht öffentlich verfügbar gemacht wird.

   ```bash
   local.settings*.json
   ```

2. Erstellen Sie als Nächstes eine Kopie der Datei _local.settings.json_, und geben Sie ihr den Namen _local.settings.properties.json_.

3. Entfernen Sie in der neuen Datei alle anderen Daten, bei denen es sich nicht um Anwendungseinstellungen handelt, und benennen Sie `Values` in `properties` um.

   Die Datei sollte nun in etwa wie im folgenden Beispiel aussehen:

   ```json
   {
     "properties": {
       "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
     }
   }
   ```

Beim Azure CLI-Befehl werden einige spezifische Werte Ihres Kontos benötigt, um das Upload durchführen zu können. Im Fenster _Übersicht_ Ihrer Static Web Apps-Ressource haben Sie Zugriff auf die folgenden Informationen:

1. Name der statischen Website
2. Ressourcengruppenname
3. Abonnement-ID

:::image type="content" source="media/application-settings/overview.png" alt-text="Übersicht über Azure Static Web Apps":::

4. Führen Sie in einem Terminal oder an der Befehlszeile den unten angegebenen Befehl aus. Ersetzen Sie die Platzhalter `<YOUR_STATIC_SITE_NAME>`, `<YOUR_RESOURCE_GROUP_NAME>` und `<YOUR_SUBSCRIPTION_ID>` durch Ihre Werte aus dem Fenster _Übersicht_.

   ```bash
   az rest --method put --headers "Content-Type=application/json" --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/config/functionappsettings?api-version=2019-12-01-preview" --body @local.settings.properties.json
   ```

> [!IMPORTANT]
> Die Datei „local.settings.properties.json“ muss sich in demselben Verzeichnis befinden, in dem dieser Befehl ausgeführt wird. Diese Datei kann einen beliebigen Namen haben. Der Name hat keine signifikante Bedeutung.

### <a name="view-application-settings-with-the-azure-cli"></a>Anzeigen von Anwendungseinstellungen mit der Azure CLI

Sie können Anwendungseinstellungen mit der Azure CLI anzeigen.

- Führen Sie in einem Terminal oder an der Befehlszeile den unten angegebenen Befehl aus. Ersetzen Sie die Platzhalter `<YOUR_SUBSCRIPTION_ID>`, `<YOUR_RESOURCE_GROUP_NAME>` und `<YOUR_STATIC_SITE_NAME>` durch Ihre Werte.

   ```bash
   az rest --method post --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/listFunctionAppSettings?api-version=2019-12-01-preview"
   ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Einrichten der lokalen Entwicklung](local-development.md)

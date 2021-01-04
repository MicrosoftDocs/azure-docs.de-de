---
title: 'Tutorial: Bereitstellen von statisch gerenderten Next.js-Websites in Azure Static Web Apps'
description: Es wird beschrieben, wie Sie dynamische Next.js-Websites mit Azure Static Web Apps generieren und bereitstellen.
services: static-web-apps
author: christiannwamba
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: chnwamba
ms.custom: devx-track-js
ms.openlocfilehash: 6f96b74726684d37795c94be74e48611b4145402
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350210"
---
# <a name="deploy-static-rendered-nextjs-websites-on-azure-static-web-apps-preview"></a>Bereitstellen von statisch gerenderten Next.js-Websites in Azure Static Web Apps (Vorschauversion)

In diesem Tutorial erfahren Sie, wie Sie eine mit [Next.js](https://nextjs.org) generierte statische Website für [Azure Static Web Apps](overview.md) bereitstellen. Zunächst wird beschrieben, wie Sie eine Next.js-App einrichten, konfigurieren und bereitstellen. Im Rahmen dieses Prozesses erfahren Sie auch, wie Sie häufige Schwierigkeiten überwinden, die beim Generieren von statischen Seiten mit Next.js auftreten können.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/).
- Ein GitHub-Konto. Sie können [kostenlos ein Konto erstellen](https://github.com/join).
- Installation von [Node.js](https://nodejs.org)

## <a name="set-up-a-nextjs-app"></a>Einrichten einer Next.js-App

Anstatt die Next.js-CLI zum Erstellen einer App zu verwenden, können Sie auch ein Startrepository nutzen, in dem eine Next.js-App vorhanden ist. Dieses Repository enthält eine Next.js-App mit dynamischen Routen, die zu einem häufigen Bereitstellungsproblem führen können. Für dynamische Routen muss eine zusätzliche Bereitstellungskonfiguration durchgeführt werden. Dies ist weiter unten genauer beschrieben.

Erstellen Sie zunächst aus einem Vorlagenrepository unter Ihrem GitHub-Konto ein neues Repository.

1. Navigieren Sie zu [https://github.com/staticwebdev/nextjs-starter/generate](https://github.com/login?return_to=/staticwebdev/nextjs-starter/generate).
1. Geben Sie dem Repository den Namen **nextjs-starter**.
1. Klonen Sie das neue Repository anschließend auf Ihrem Computer. Ersetzen Sie `<YOUR_GITHUB_ACCOUNT_NAME>` durch Ihren Kontonamen.

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nextjs-starter
    ```

1. Navigieren Sie zur neu geklonten Next.js-App:

   ```bash
   cd nextjs-starter
   ```

1. Installieren von Abhängigkeiten:

    ```bash
    npm install
    ```

1. Starten Sie die Next.js-App in der Entwicklung:

    ```bash
    npm run dev
    ```

Navigieren Sie zu `http://localhost:3000`, um die App zu öffnen. Die folgende Website sollte in Ihrem bevorzugten Browser geöffnet werden:

:::image type="content" source="media/deploy-nextjs/start-nextjs-app.png" alt-text="Starten der Next.js-App":::

Wenn Sie auf ein Framework bzw. eine Bibliothek klicken, sollte eine Detailseite zum ausgewählten Element angezeigt werden:

:::image type="content" source="media/deploy-nextjs/start-nextjs-details.png" alt-text="Detailseite":::

## <a name="generate-a-static-website-from-nextjs-build"></a>Generieren einer statischen Website aus dem Next.js-Build

Beim Erstellen einer Next.js-Website mit `npm run build` wird die App als herkömmliche Web-App und nicht als statische Website erstellt. Verwenden Sie die unten angegebene Anwendungskonfiguration, um eine statische Website zu generieren.

1. Erstellen Sie zum Konfigurieren von statischen Routen im Stammverzeichnis Ihres Projekts eine Datei mit dem Namen _next.config.js_, und fügen Sie den unten angegebenen Code hinzu.

    ```javascript
    module.exports = {
      trailingSlash: true,
      exportPathMap: function() {
        return {
          '/': { page: '/' }
        };
      }
    };
    ```
    
      Mit dieser Konfiguration wird `/` der Next.js-Seite zugeordnet, die für die Route `/` bereitgestellt wird. Dies ist die Seitendatei _pages/index.js_.

1. Aktualisieren Sie das Buildskript der Datei _package.json_, um nach der Erstellung mit dem Befehl `next export` auch eine statische Website zu generieren. Mit dem Befehl `export` wird eine statische Website generiert.

    ```json
    "scripts": {
      "dev": "next dev",
      "build": "next build && next export",
    },
    ```

    Nach der Ausführung dieses Befehls wird das Skript `build` von Azure Static Web Apps jedes Mal ausgeführt, wenn Sie einen Commit pushen.

1. Generieren einer statischen Website:

    ```bash
    npm run build
    ```

    Die statische Website wird generiert und in einen _out_-Ordner im Stammverzeichnis Ihres Arbeitsverzeichnisses kopiert.

    > [!NOTE]
    > Dieser Ordner ist in der _GITIGNORE_-Datei aufgeführt, damit er während des CI/CD-Vorgangs generiert werden kann, wenn Sie die Bereitstellung durchführen.

## <a name="push-your-static-website-to-github"></a>Pushen Ihrer statischen Website zu GitHub

Bei Azure Static Web Apps wird Ihre App aus einem GitHub-Repository bereitgestellt. Dies wird dann für jedes Pushen eines Commits zu einem entsprechend angegebenen Branch durchgeführt. Verwenden Sie die folgenden Befehle, um Ihre Änderungen mit GitHub zu synchronisieren.

1. Stellen Sie alle geänderten Dateien bereit.

    ```bash
    git add .
    ```

1. Führen Sie einen Commit für alle Änderungen aus.

    ```bash
    git commit -m "Update build config"
    ```

1. Pushen Sie Ihre Änderungen zu GitHub.

    ```bash
    git push origin master
    ```

## <a name="deploy-your-static-website"></a>Bereitstellen Ihrer statischen Website

Die folgenden Schritte veranschaulichen, wie Sie die App, die Sie gerade zu GitHub gepusht haben, mit Azure Static Web Apps verknüpfen. Wenn sich die Anwendung in Azure befindet, können Sie sie in einer Produktionsumgebung bereitstellen.

### <a name="create-a-static-app"></a>Erstellen einer statischen App

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
1. Klicken Sie auf **Ressource erstellen**.
1. Suchen Sie nach **Static Web Apps**.
1. Klicken Sie auf **Static Web Apps (Vorschau)** .
1. Klicken Sie auf **Erstellen**

1. Wählen Sie in der Dropdownliste *Abonnement* ein Abonnement aus, oder verwenden Sie den Standardwert.
1. Klicken Sie unterhalb der Dropdownliste *Ressourcengruppe* auf den Link **Neu**. Geben Sie unter *Name für neue Ressourcengruppe* den Namen **mystaticsite** ein, und klicken Sie auf **OK**.
1. Geben Sie im Textfeld **Name** einen global eindeutigen Namen für Ihre App ein. Gültige Zeichen sind `a-z`, `A-Z`, `0-9` und `-`. Dieser Wert wird als URL-Präfix für Ihre statische App im Format `https://<APP_NAME>.azurestaticapps.net` verwendet.
1. Wählen Sie in der Dropdownliste *Region* eine Region in Ihrer Nähe aus.
1. Wählen Sie in der Dropdownliste mit den SKUs die Option **Free** aus.

   :::image type="content" source="media/deploy-nextjs/create-static-web-app.png" alt-text="Erstellen einer statischen Web-App":::

### <a name="add-a-github-repository"></a>Hinzufügen eines GitHub-Repositorys

Das neue Azure Static Web Apps-Konto benötigt Zugriff auf das Repository mit Ihrer Next.js-App, damit automatisch Commits bereitgestellt werden können.

1. Klicken Sie auf die Schaltfläche **Mit GitHub anmelden**.
1. Wählen Sie die **Organisation** aus, unter der Sie das Repository für Ihr Next.js-Projekt erstellt haben. Unter Umständen ist dies Ihr GitHub-Benutzername.
1. Suchen Sie nach dem Namen des zuvor erstellten Repositorys, und wählen Sie ihn aus.
1. Wählen Sie in der Dropdownliste *Branch* die Option **master** als Branch aus.

   :::image type="content" source="media/deploy-nextjs/connect-github.png" alt-text="Verbinden mit GitHub":::

### <a name="configure-the-build-process"></a>Konfigurieren des Buildprozesses

Azure Static Web Apps ist so konzipiert, dass häufig anfallende Aufgaben, z. B. die Installation von npm-Modulen und die Ausführung von `npm run build` bei jeder Bereitstellung, automatisch durchgeführt werden. Es gibt aber einige Einstellungen, z. B. den Quellordner der Anwendung und den Ordner für das Buildziel, die Sie manuell konfigurieren müssen.

1. Klicken Sie auf die Registerkarte **Build**, um den statischen Ausgabeordner zu konfigurieren.

   :::image type="content" source="media/deploy-nextjs/build-tab.png" alt-text="Registerkarte „Build“":::

2. Geben Sie im Textfeld *Speicherort für App-Artefakte* den Namen **out** ein.

### <a name="review-and-create"></a>Überprüfen und Erstellen

1. Klicken Sie auf die Schaltfläche **Bewerten + erstellen**, um sicherzustellen, dass alle Details stimmen.
1. Klicken Sie auf **Erstellen**, um mit der Erstellung der Ressource zu beginnen und eine GitHub-Aktion für die Bereitstellung anzugeben.
1. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
1. Klicken Sie im Fenster _Übersicht_ auf den Link *URL*, um Ihre bereitgestellte Anwendung zu öffnen. 

Falls die Website nicht sofort geladen wird, ist der Workflow für GitHub Actions im Hintergrund noch aktiv. Nach Abschluss des Workflows können Sie auf die Schaltfläche zum Aktualisieren des Browsers klicken, um Ihre Web-App anzuzeigen.
Falls die Website nicht sofort geladen wird, ist der Workflow für GitHub Actions im Hintergrund noch aktiv. Nach Abschluss des Workflows können Sie auf die Schaltfläche zum Aktualisieren des Browsers klicken, um Ihre Web-App anzuzeigen.

Sie können den Status der GitHub Actions-Workflows überprüfen, indem Sie zu den Aktionen für Ihr Repository navigieren:

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nextjs-starter/actions
```

### <a name="sync-changes"></a>Synchronisieren von Änderungen

Als Sie die App erstellt haben, wurde von Azure Static Web Apps in Ihrem Repository eine GitHub Actions-Workflowdatei erstellt. Sie müssen diese Datei in Ihr lokales Repository einfügen, damit Ihr Git-Verlauf synchronisiert wird.

Wechseln Sie zurück zum Terminal, und führen Sie den folgenden Befehl aus: `git pull origin master`.

## <a name="configure-dynamic-routes"></a>Konfigurieren dynamischer Routen

Navigieren Sie zur neu bereitgestellten Website, und klicken Sie auf eines der Logos für ein Framework oder eine Bibliothek. Anstelle der Detailseite wird eine Seite mit dem Fehler 404 angezeigt.

:::image type="content" source="media/deploy-nextjs/404-in-production.png" alt-text="Fehler 404 bei dynamischen Routen":::

Der Grund für diesen Fehler ist, dass von Next.js basierend auf der Anwendungskonfiguration nur die Startseite generiert wurde.

## <a name="generate-static-pages-from-dynamic-routes"></a>Generieren von statischen Seiten aus dynamischen Routen

1. Aktualisieren Sie die Datei _next.config.js_, damit von Next.js eine Liste mit allen verfügbaren Daten verwendet wird, um statische Seiten für jedes Framework bzw. jede Bibliothek zu generieren:

   ```javascript
   const data = require('./utils/projectsData');

   module.exports = {
     trailingSlash: true,
     exportPathMap: async function () {
       const { projects } = data;
       const paths = {
         '/': { page: '/' },
       };
  
       projects.forEach((project) => {
         paths[`/project/${project.slug}`] = {
           page: '/project/[path]',
           query: { path: project.slug },
         };
       });
  
       return paths;
     },
   };
   ```

   > [!NOTE]
   > Da die Funktion `exportPathMap` eine asynchrone Funktion ist, können Sie darin eine Anforderung an eine API senden und die zurückgegebene Liste verwenden, um die Pfade zu generieren.

2. Pushen Sie die neuen Änderungen in Ihr GitHub-Repository, und warten Sie einige Minuten, während Ihre Website von GitHub Actions neu erstellt wird. Nach Abschluss des Buildvorgangs wird der Fehler 404 nicht mehr angezeigt.

   :::image type="content" source="media/deploy-nextjs/404-in-production-fixed.png" alt-text="Behobener Fehler 404 für dynamische Routen":::

> [!div class="nextstepaction"]
> [Einrichten einer benutzerdefinierten Domäne](custom-domain.md)

---
title: Hero-Beispiel für Gruppenanrufe
titleSuffix: An Azure Communication Services sample overview
description: Enthält eine Übersicht über das Hero-Beispiel für Anrufe mit Azure Communication Services, damit Entwickler sich genauer über die Funktionsweise des Beispiels informieren können.
author: ddematheu2
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: c872c2a2799eee3ae9e39653b0046b70ca5d7e8b
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94887534"
---
# <a name="get-started-with-the-group-calling-hero-sample"></a>Erste Schritte mit dem Hero-Beispiel für Gruppenanrufe

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!----
> [!WARNING]
> Add links to our Hero Sample repo when the sample is publicly available.
---->

> [!IMPORTANT]
> [Dieses Beispiel ist auf GitHub verfügbar.](https://github.com/Azure-Samples/communication-services-web-calling-hero)

Das **Hero-Beispiel für Gruppenanrufe** von Azure Communication Services veranschaulicht, wie die Communication Services-Webclientbibliothek für Anrufe verwendet werden kann, um eine Benutzeroberfläche für Gruppenanrufe zu erstellen.

In dieser Beispiel-Schnellstartanleitung wird beschrieben, wie das Beispiel funktioniert, bevor wir es auf Ihrem lokalen Computer ausführen. Anschließend stellen wir das Beispiel in Azure bereit, indem wir Ihre eigenen Azure Communication Services-Ressourcen verwenden.

## <a name="overview"></a>Übersicht

Das Beispiel verfügt sowohl über eine clientseitige als auch eine serverseitige Anwendung. Die **clientseitige Anwendung** ist eine React/Redux-Webanwendung, für die das Fluent-UI-Framework von Microsoft verwendet wird. Diese Anwendung sendet Anforderungen an eine **serverseitige ASP.NET Core-Anwendung**, die für die clientseitige Anwendung die Verbindungsherstellung mit Azure ermöglicht. 

Das Beispiel sieht wie folgt aus:

:::image type="content" source="./media/calling/landing-page.png" alt-text="Screenshot: Landing Page der Beispielanwendung":::

Wenn Sie auf die Schaltfläche „Start a call“ (Anruf starten) klicken, ruft die Webanwendung ein Benutzerzugriffstoken aus der serverseitigen Anwendung ab. Dieses Token wird dann verwendet, um die Client-App mit Azure Communication Services zu verbinden. Nach dem Abrufen des Tokens werden Sie aufgefordert, die gewünschte Kamera und das Mikrofon anzugeben. Sie können Ihre Geräte mit den entsprechenden Umschaltern deaktivieren und aktivieren:

:::image type="content" source="./media/calling/pre-call.png" alt-text="Screenshot: Bildschirm zur Vorbereitung von Anrufen in der Beispielanwendung":::

Nachdem Sie Ihren Anzeigenamen und Ihre Geräte konfiguriert haben, können Sie der Anrufsitzung beitreten. Der Hauptbereich für Anrufe, der die zentrale Anrufbenutzeroberfläche enthält, wird angezeigt.

:::image type="content" source="./media/calling/main-app.png" alt-text="Screenshot: Hauptbildschirm der Beispielanwendung":::

Komponenten des Hauptbildschirms für Anrufe:

1. **Medienkatalog**: Der Hauptbereich, in dem die Teilnehmer angezeigt werden. Wenn ein Teilnehmer seine Kamera aktiviert hat, wird der Videofeed hier angezeigt. Jeder Teilnehmer verfügt über eine individuelle Kachel, auf der sein Anzeigename und der Videostream (falls vorhanden) angezeigt werden.
2. **Header**: Hier befinden sich die wichtigsten Anrufsteuerelemente zum Umschalten der Seitenleiste für Einstellungen und Teilnehmer, Aktivieren/Deaktivieren von Videos und der Mischung, Freigeben des Bildschirms und Beenden des Anrufs.
3. **Seitenleiste**: Hier werden die Informationen zu den Teilnehmern und Einstellungen angezeigt, wenn der entsprechende Umschalter im Headerbereich verwendet wird. Die Komponente kann mit dem „X“ oben rechts geschlossen werden. In der Seitenleiste für die Teilnehmer werden eine Liste mit Teilnehmern und ein Link zum Einladen von weiteren Benutzern zum Chatten angezeigt. In der Seitenleiste für die Einstellungen können Sie die Mikrofon- und Kameraeinstellungen konfigurieren.

Unten sind weitere Informationen zu den Voraussetzungen und Schritten zum Einrichten des Beispiels angegeben.

## <a name="prerequisites"></a>Voraussetzungen

- Erstellen Sie ein Azure-Konto mit einem aktiven Abonnement. Details finden Sie auf der [Seite zum Erstellen eines kostenloses Azure-Kontos](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js (12.18.4 und höher)](https://nodejs.org/en/download/)
- [Visual Studio (2019 und höher)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) (Stellen Sie sicher, dass Sie die Version installieren, die Ihrer Visual Studio-Instanz entspricht (32 oder 64 Bit).)
- Erstellen Sie eine Azure Communication Services-Ressource. Ausführlichere Informationen hierzu finden Sie unter [Erstellen einer Azure Communication Services-Ressource](../quickstarts/create-communication-resource.md). Sie müssen die **Verbindungszeichenfolge** Ihrer Ressource für diese Schnellstartanleitung aufzeichnen.

## <a name="locally-deploy-the-service--client-applications"></a>Lokales Bereitstellen der Dienst- und der Clientanwendung

Das Beispiel für Gruppenanrufe umfasst im Wesentlichen zwei Anwendungen: die Client-App und die .NET-Dienst-App.

Für die lokale Bereitstellung müssen wir beide Anwendungen starten. Wenn über den Browser auf die Server-App zugegriffen wird, wird die lokal bereitgestellte Client-App für die Benutzeroberfläche verwendet.

Sie können das Beispiel lokal testen, indem Sie mehrere Browsersitzungen mit der URL Ihres Anrufs öffnen, um einen Anruf mit mehreren Benutzern zu simulieren.

## <a name="before-running-the-sample-for-the-first-time"></a>Vor dem erstmaligen Ausführen des Beispiels

1. Öffnen Sie eine Instanz von PowerShell, des Windows-Terminals, einer Eingabeaufforderung oder eines gleichwertigen Tools, und navigieren Sie zu dem Verzeichnis, in dem Sie das Beispiel klonen möchten.
2. `git clone https://github.com/Azure-Samples/communication-services-web-calling-hero.git`
3. Rufen Sie im Azure-Portal die `Connection String` (Verbindungszeichenfolge) ab. Weitere Informationen zu Verbindungszeichenfolgen finden Sie unter [Erstellen einer Azure Communication Services-Ressource](../quickstarts/create-communication-resource.md).
4. Nachdem Sie die `Connection String` abgerufen haben, fügen Sie sie der Datei **Calling/appsetting.json** im Ordner für die .NET-Dienst-App (Service .NET) hinzu. Geben Sie Ihre Verbindungszeichenfolge in die Variable ein: `ResourceConnectionString`.

### <a name="local-run"></a>Lokale Ausführung

1. Wechseln Sie zum Ordner „Calling“, und öffnen Sie die Projektmappe `Calling.csproj` in Visual Studio
2. Führen Sie das `Calling`-Projekt aus. Der Browser wird unter „localhost:5001“ geöffnet.

#### <a name="troubleshooting"></a>Problembehandlung

- Die Lösung wird nicht erstellt. Während des npm-Installations- bzw. -Buildvorgangs werden Fehler ausgelöst.

   Versuchen Sie, die Projekte zu bereinigen bzw. neu zu erstellen.

## <a name="publish-the-sample-to-azure"></a>Veröffentlichen des Beispiels in Azure

1. Klicken Sie mit der rechten Maustaste auf das Projekt `Calling`, und wählen Sie „Veröffentlichen“ aus.
2. Erstellen Sie ein neues Veröffentlichungsprofil, und wählen Sie Ihr Azure-Abonnement aus.
3. Fügen Sie vor dem Veröffentlichen Ihre Verbindungszeichenfolge mit `Edit App Service Settings` hinzu. Fügen Sie `ResourceConnectionString` als Schlüssel ein, und geben Sie Ihre Verbindungszeichenfolge (Kopie aus „appsettings.json“) als Wert ein.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Communication Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind. Erfahren Sie mehr über das [Bereinigen von Ressourcen](../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Nächste Schritte

>[!div class="nextstepaction"] 
>[Laden Sie das Beispiel von GitHub herunter](https://github.com/Azure-Samples/communication-services-web-calling-hero).

Weitere Informationen finden Sie in den folgenden Artikeln:

- Machen Sie sich mit der [Verwendung der Clientbibliothek für Anrufe](../quickstarts/voice-video-calling/calling-client-samples.md) vertraut.
- Informieren Sie sich über die [Funktionsweise von Anrufen](../concepts/voice-video-calling/about-call-types.md).

## <a name="additional-reading"></a>Zusätzliche Lektüre

- [Azure Communication GitHub](https://github.com/Azure/communication): Weitere Beispiele und Informationen finden Sie auf der offiziellen GitHub-Seite
- [Redux](https://redux.js.org/): Clientseitige Zustandsverwaltung
- [Fluent-UI](https://aka.ms/fluent-ui): UI-Bibliothek von Microsoft
- [React](https://reactjs.org/): Bibliothek zum Erstellen von Benutzeroberflächen
- [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1): Framework für die Erstellung von Webanwendungen
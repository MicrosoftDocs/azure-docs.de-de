---
title: Includedatei
description: Includedatei
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 0c43fb72829614af339f7dcc1bb36edfc80e6c80
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121710"
---
## <a name="test-your-code"></a>Testen Ihres Codes

### <a name="test-with-node"></a>Testen mit Node

Wenn Sie nicht Visual Studio verwenden, stellen Sie sicher, dass Ihr Webserver gestartet wurde.

1. Konfigurieren Sie den Server zum Lauschen am TCP-Port, der auf dem Speicherort Ihrer Datei **index.html** basiert. Starten Sie für Node den Webserver, um über den Port zu lauschen, indem Sie an einer Eingabeaufforderung im Anwendungsordner die folgenden Befehle ausführen:

    ```bash
    npm install
    node server.js
    ```
1. Öffnen Sie den Browser, und geben Sie „http://<span></span>localhost:30662“ oder „http://<span></span>localhost:{Port}“ ein. Hierbei steht **Port** für den Port, an dem Ihr Webserver lauscht. Der Inhalt der Datei „index.html“ und die Schaltfläche **Anmelden** sollten angezeigt werden.

<p><!-- -->

### <a name="test-with-visual-studio"></a>Testen mit Visual Studio

Stellen Sie bei Verwendung von Visual Studio sicher, dass Sie die Projektlösung auswählen und **F5** drücken, um Ihr Projekt auszuführen. Der Browser wird geöffnet, und Sie werden zu „http://<span></span>localhost:{Port}“ weitergeleitet, wo die Schaltfläche **Anmelden** angezeigt wird.

## <a name="test-your-application"></a>Testen Ihrer Anwendung

Klicken Sie auf **Anmelden**, nachdem der Browser die Datei „index.html“ geladen hat. Sie werden aufgefordert, sich mit dem Microsoft Identity Platform-Endpunkt anzumelden:

![Anmelden bei Ihrem JavaScript SPA-Konto](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Zustimmen zum Anwendungszugriff

Bei der ersten Anmeldung bei Ihrer Anwendung werden Sie aufgefordert, Ihre Zustimmung zu geben, dass die Anwendung auf Ihr Profil zugreifen und Sie anmelden darf:

![Zustimmen zum Anwendungszugriff](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Anzeigen von Anwendungsergebnissen

Nach der Anmeldung sollten Ihre Benutzerprofilinformationen angezeigt werden, die in der Antwort der Microsoft Graph-API zurückgegeben werden.

![Erwartete Ergebnisse aus dem Microsoft Graph-API-Aufruf](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Weitere Informationen zu Bereichen und delegierten Berechtigungen

Die Microsoft Graph-API benötigt den Bereich `user.read`, um ein Benutzerprofil zu lesen. Dieser Bereich wird standardmäßig jeder Anwendung automatisch hinzugefügt, die im Registrierungsportal registriert ist. Andere Microsoft Graph-APIs sowie benutzerdefinierte APIs für Ihren Back-End-Server erfordern unter Umständen zusätzliche Bereiche. Die Microsoft Graph-API benötigt beispielsweise den Bereich `Calendars.Read`, um die Kalender des Benutzers aufzuführen.

Um auf die Kalender des Benutzers im Kontext einer Anwendung zugreifen zu können, müssen Sie den Informationen für die Anwendungsregistrierung die delegierte Berechtigung `Calendars.Read` hinzufügen. Fügen Sie anschließend den Bereich `Calendars.Read` dem Aufruf `acquireTokenSilent` hinzu.

>[!NOTE]
>Wenn Sie die Anzahl der Bereiche erhöhen, werden Benutzer ggf. zu weiteren Genehmigungen aufgefordert.

Wenn eine Back-End-API keinen Bereich benötigt (nicht empfohlen), können Sie `clientId` bei den Aufrufen zum Beschaffen von Token als Bereich verwenden.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]

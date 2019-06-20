---
title: Geschützte Web-API – App-Registrierung | Azure
description: Erfahren Sie, wie Sie eine geschützte Web-API erstellen und die App registrieren.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59af4e20c7fe838f7c725b47e45968941fa85cb7
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254053"
---
# <a name="protected-web-api---app-registration"></a>Geschützte Web-API – App-Registrierung

Dieser Artikel erläutert die Einzelheiten zur App-Registrierung für eine geschützte Web-API.

Weitere Informationen finden Sie unter [Schnellstart: Registrieren einer Anwendung bei Microsoft Identity Platform](quickstart-register-app.md). Dort sind die allgemeinen Schritte zum Registrieren der Anwendung aufgeführt.

## <a name="accepted-token-version"></a>Akzeptierte Tokenversion

Der Microsoft Identity Platform-Endpunkt kann zwei Arten von Token ausgeben: v1.0-Token und v2.0-Token. Weitere Informationen zu diesen Token finden Sie unter [Zugriffstoken](access-tokens.md). Die akzeptierte Tokenversion hängt von den **unterstützten Kontotypen** ab, die Sie bei Erstellung Ihrer Anwendung ausgewählt haben:

- Wenn der Wert von **Unterstützte Kontotypen** auf **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten (z.B. Skype, Xbox, Outlook.com)** festgelegt ist, wird v2.0 als Tokenversion akzeptiert.
- Andernfalls wird v1.0 als Tokenversion akzeptiert.

Nachdem Sie die Anwendung erstellt haben, können Sie die akzeptierte Tokenversion anhand der folgenden Schritte ändern:

1. Wählen Sie Ihre App im Azure-Portal aus, und wählen Sie dann das **Manifest** für Ihre App aus.
2. Suchen Sie im Manifest nach **AccessTokenAcceptedVersion**, und achten Sie darauf, dass der Wert **2** lautet. Mit dieser Eigenschaft können Sie Azure AD mitteilen, dass die Web-API v2.0-Token akzeptiert. Lautet der Wert **null**, wird v1.0 als Tokenversion akzeptiert.
3. Wählen Sie **Speichern** aus.

> [!NOTE]
> Es liegt an der Web-API zu entscheiden, welche Tokenversion (v1.0 oder v2.0) sie akzeptiert. Wenn Clients über den Microsoft Identity Platform v2. 0-Endpunkt ein Token für Ihre Web-API anfordern, erhalten sie ein Token, das angibt, welche Version von der Web-API akzeptiert wird.

## <a name="no-redirect-uri"></a>Kein Umleitungs-URI

Web-APIs müssen keinen Umleitungs-URI registrieren, weil kein Benutzer interaktiv angemeldet ist.

## <a name="expose-an-api"></a>Verfügbarmachen einer API

Weitere für Web-APIs spezifische Einstellungen sind die verfügbar gemachte API und die verfügbar gemachten Bereiche.

### <a name="resource-uri-and-scopes"></a>Ressourcen-URI und Bereiche

Bereiche liegen in der Regel im Format `resourceURI/scopeName` vor. Für Microsoft Graph besitzen die Bereiche Verknüpfungen wie `User.Read`, aber diese Zeichenfolge ist nur eine Verknüpfung für `https://graph.microsoft.com/user.read`.

Während der App-Registrierung müssen Sie die folgenden Parameter definieren:

- Einen Ressourcen-URI. Standardmäßig empfiehlt das Anwendungsregistrierungsportal die Verwendung von `api://{clientId}`. Dieser Ressourcen-URI ist eindeutig, aber nicht lesbar. Sie können ihn ändern, aber stellen Sie sicher, dass er eindeutig ist.
- Einen oder mehrere Bereiche

Die Bereiche werden auch im Einwilligungsbildschirm angezeigt, der für Endbenutzern eingeblendet wird, die Ihre Anwendung verwenden. Aus diesem Grund müssen Sie die entsprechenden Zeichenfolgen angeben, die den Bereich beschreiben:

- Wie er den Endbenutzern angezeigt wird
- Wie er dem Mandantenadministrator angezeigt wird, der die Administratoreinwilligung erteilen kann

### <a name="how-to-expose-the-api"></a>Verfügbarmachen der API

1. Wählen Sie bei der Anwendungsregistrierung den Abschnitt **Eine API verfügbar machen** aus, und:
   1. Wählen Sie **Bereich hinzufügen**.
   1. Akzeptieren Sie den vorgeschlagenen Anwendungs-ID-URI (api ://{clientId}) durch Auswahl von **Speichern und fortfahren**.
   1. Legen Sie die folgenden Parameter fest:
      - Verwenden Sie `access_as_user` als **Bereichsnamen**.
      - Stellen Sie sicher, dass für **Zum Einwilligen berechtigte Personen** die Option **Administratoren und Benutzer** ausgewählt ist.
      - Geben Sie unter **Anzeigename der Administratoreinwilligung** `Access TodoListService as a user` ein.
      - Geben Sie unter **Beschreibung der Administratoreinwilligung** `Accesses the TodoListService Web API as a user` ein.
      - Geben Sie unter **Anzeigename der Benutzereinwilligung** `Access TodoListService as a user` ein.
      - Geben Sie unter **Beschreibung der Benutzereinwilligung** `Accesses the TodoListService Web API as a user` ein.
      - Lassen Sie **Zustand** auf **Aktiviert** festgelegt.
      - Wählen Sie **Bereich hinzufügen** aus.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Codekonfiguration der App](scenario-protected-web-api-app-configuration.md)

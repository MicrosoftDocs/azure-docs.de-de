---
title: Registrierung einer geschützten Web-API-App | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie eine geschützte Web-API erstellen und die App registrieren.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5d93df0b6d59e013c22e138942ab4651784421ae
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584379"
---
# <a name="protected-web-api-app-registration"></a>Geschützte Web-API: App-Registrierung

Dieser Artikel erläutert die Besonderheiten der App-Registrierung für eine geschützte Web-API.

Die allgemeinen Schritte zum Registrieren einer App sind beschrieben in [Schnellstart: Registrieren einer Anwendung bei der Microsoft Identity Platform](quickstart-register-app.md).

## <a name="accepted-token-version"></a>Akzeptierte Tokenversion

Microsoft Identity Platform kann v1.0-Token und v2.0-Token ausgeben. Weitere Informationen zu diesen Token finden Sie unter [Zugriffstoken](access-tokens.md).

Die Tokenversion, die von Ihrer API akzeptiert wird, richtet sich nach den **unterstützten Kontotypen**, die Sie beim Erstellen der Registrierung Ihrer Web-API-Anwendung im Azure-Portal ausgewählt haben.

- Wenn der Wert von **Unterstützte Kontotypen** auf **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten (z. B. Skype, Xbox, Outlook.com)** festgelegt ist, wird nur Tokenversion v2.0 akzeptiert.
- Andernfalls wird auch v1.0 als Tokenversion akzeptiert.

Nachdem Sie die Anwendung erstellt haben, können Sie die akzeptierte Tokenversion ermitteln oder ändern, indem Sie die folgenden Schritte ausführen:

1. Wählen Sie im Azure-Portal Ihre App und dann **Manifest** aus.
1. Suchen Sie im Manifest nach der Eigenschaft **accessTokenAcceptedVersion**.
1. Dieser Wert gibt Azure Active Directory (Azure AD) an, welche Tokenversion die Web-API akzeptiert.
    - Mit dem Wert „2“ akzeptiert die Web-API v2.0-Token.
    - Ist der Wert **Null**, akzeptiert die Web-API v1.0-Token.
1. Falls Sie die Tokenversion geändert haben, wählen Sie **Speichern** aus.

> [!NOTE]
> Die Web-API gibt an, welche Tokenversion akzeptiert wird. Wenn ein Client ein Token für Ihre Web-API von Microsoft Identity Platform anfordert, erhält er ein Token, das angibt, welche Tokenversion die Web-API akzeptiert.

## <a name="no-redirect-uri"></a>Kein Umleitungs-URI

Web-APIs müssen keinen Umleitungs-URI registrieren, da kein Benutzer interaktiv angemeldet wird.

## <a name="exposed-api"></a>Verfügbar gemachte API

Weitere für Web-APIs spezifische Einstellungen sind die verfügbar gemachte API und die verfügbar gemachten Bereiche oder App-Rollen.

### <a name="application-id-uri-and-scopes"></a>Anwendungs-ID-URI und Bereiche

Bereiche haben in der Regel das Format `resourceURI/scopeName`. Bei Microsoft Graph haben die Bereiche Verknüpfungen. `User.Read` ist beispielsweise eine Verknüpfung für `https://graph.microsoft.com/user.read`.

Definieren Sie bei der App-Registrierung die folgenden Parameter:

- Den Ressourcen-URI
- Mindestens einen Bereich
- Mindestens eine App-Rolle

Standardmäßig empfiehlt das Anwendungsregistrierungsportal die Verwendung von `api://{clientId}` als Ressourcen-URI. Dieser URI ist eindeutig, aber nicht von Menschen lesbar. Wenn Sie den URI ändern, müssen Sie sicherstellen, dass der neue Wert eindeutig ist. Das Anwendungsregistrierungsportal stellt sicher, dass Sie eine [konfigurierte Herausgeberdomäne](howto-configure-publisher-domain.md) verwenden.

Den Clientanwendungen werden Bereiche als *delegierte Berechtigungen* und App-Rollen als *Anwendungsberechtigungen* für Ihre Web-API angezeigt.

Bereiche werden den Benutzern Ihrer App auch im Zustimmungsfenster angezeigt. Geben Sie daher die entsprechenden Zeichenfolgen an, die den Bereich beschreiben:

- Aus Sicht eines Benutzers
- Aus Sicht eines Mandantenadministrators, der Administratoreinwilligung erteilen kann

Die Einwilligung zu App-Rollen kann nicht durch einen Benutzer erteilt werden (da sie von einer Anwendung verwendet werden, die die Web-API selbstständig oder im Auftrag aufruft). Ein Mandantenadministrator muss in Clientanwendungen der App-Rollen einwilligen, die Ihre Web-API verfügbar machen. Weitere Informationen finden Sie unter [Administratoreinwilligung](v2-admin-consent.md).

### <a name="exposing-delegated-permissions-scopes"></a>Verfügbarmachen delegierter Berechtigungen (Bereiche)

1. Wählen Sie **Eine API verfügbar machen** in der Anwendungsregistrierung aus.
1. Wählen Sie **Bereich hinzufügen**.
1. Übernehmen Sie, wenn Sie dazu aufgefordert werden, den vorgeschlagenen Anwendungs-ID-URI (`api://{clientId}`), indem Sie **Speichern und fortfahren** auswählen.
1. Geben Sie folgende Werte an:
    - Wählen Sie **Bereichsname** aus, und geben Sie **access_as_user** ein.
    - Wählen Sie **Zum Einwilligen berechtigte Personen** aus, und stellen Sie sicher, dass **Admins und Benutzer** ausgewählt ist.
    - Wählen Sie **Anzeigename der Administratorzustimmung** aus, und geben Sie **Zugriff auf TodoListService als Benutzer** ein.
    - Wählen Sie **Beschreibung der Administratorzustimmung** aus, und geben Sie **Greift als Benutzer auf die TodoListService-Web-API zu** ein.
    - Wählen Sie **Anzeigename der Benutzerzustimmung** aus, und geben Sie **Zugriff auf TodoListService als Benutzer** ein.
    - Wählen Sie **Beschreibung der Benutzerzustimmung** aus, und geben Sie **Greift als Benutzer auf die TodoListService-Web-API zu** ein.
    - Behalten Sie **Aktiviert** als Einstellung für den Wert **Status** bei.
 1. Wählen Sie **Bereich hinzufügen** aus.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Aufrufen der Web-API von Daemon-Apps

In diesem Abschnitt erfahren Sie, wie Sie Ihre geschützte Web-API registrieren, damit Daemon-Apps sie sicher aufrufen können.

- Sie deklarieren lediglich *Anwendungsberechtigungen* und machen diese verfügbar, da Daemon-Apps nicht mit Benutzern interagieren. Delegierte Berechtigungen würden keinen Sinn machen.
- Mandantenadministratoren können Azure AD auffordern, Web-API-Token nur an Anwendungen auszugeben, die für den Zugriff auf eine der Anwendungsberechtigungen der Web-API registriert wurden.

#### <a name="exposing-application-permissions-app-roles"></a>Verfügbarmachen von Anwendungsberechtigungen (App-Rollen)

Bearbeiten Sie das Manifest, um Anwendungsberechtigungen verfügbar zu machen.

1. Wählen Sie in der Anwendungsregistrierung für Ihre Anwendung **Manifest** aus.
1. Suchen Sie zum Bearbeiten des Manifests nach der Einstellung `appRoles`, und fügen Sie Anwendungsrollen hinzu. Die Rollendefinitionen gehen aus dem folgenden Beispiel eines JSON-Blocks hervor.
1. Lassen Sie `allowedMemberTypes` auf `"Application"` eingestellt.
1. Stellen Sie sicher, dass `id` eine eindeutige GUID ist.
1. Stellen Sie sicher, dass `displayName` und `value` keine Leerzeichen enthalten.
1. Speichern Sie das Manifest.

Das folgende Beispiel enthält die Inhalte von `appRoles`, wobei der Wert `id` eine beliebige eindeutige GUID sein kann.

```json
"appRoles": [
    {
    "allowedMemberTypes": [ "Application" ],
    "description": "Accesses the TodoListService-Cert as an application.",
    "displayName": "access_as_application",
    "id": "ccf784a6-fd0c-45f2-9c08-2f9d162a0628",
    "isEnabled": true,
    "lang": null,
    "origin": "Application",
    "value": "access_as_application"
    }
],
```

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Sicherstellen der Tokenausgabe von Azure AD für Ihre Web-API nur an zugelassene Clients

Die Web-API überprüft die App-Rolle. Diese Rolle verwenden Softwareentwickler als Methode zum Verfügbarmachen von Anwendungsberechtigungen. Sie können Azure AD auch so konfigurieren, dass API-Token nur an Apps ausgegeben werden, für die der Mandantenadministrator den API-Zugriff genehmigt hat.

So fügen Sie diese erhöhte Sicherheit hinzu

1. Wechseln Sie in Ihrer App-Registrierung zur Seite **Übersicht** der App.
1. Wählen Sie unter **Verwaltete Anwendung in lokalem Verzeichnis** den Link mit dem Namen Ihrer App aus. Die Bezeichnung für diese Auswahl ist u. U. abgeschnitten. Möglicherweise wird nur **Verwaltete Anwendung in...** angezeigt.

   > [!NOTE]
   >
   > Wenn Sie diesen Link auswählen, wechseln Sie zur Seite **Übersicht über Unternehmensanwendungen**. Diese Seite ist mit dem Dienstprinzipal für Ihre Anwendung in dem Mandanten verknüpft, in dem Sie sie erstellt haben. Über die Schaltfläche „Zurück“ in Ihrem Browser können Sie wieder zur App-Registrierungsseite wechseln.

1. Wählen Sie im Abschnitt **Verwalten** der Unternehmensanwendungsseiten die Seite **Eigenschaften** aus.
1. Wenn Azure AD den Zugriff auf Ihre Web-API nur von bestimmten Clients aus zulassen soll, stellen Sie **Benutzerzuweisung erforderlich?** auf **Ja** ein.

   > [!IMPORTANT]
   >
   > Wenn Sie **Benutzerzuweisung erforderlich?** auf **Ja** einstellen, überprüft Azure AD die App-Rollenzuweisungen des Clients, wenn er ein Zugriffstoken für die Web-API anfordert. Wenn dem Client keinen App-Rollen zugewiesen sind, gibt Azure AD folgenden Fehler zurück: „invalid_client: AADSTS501051: Anwendung \<application name\> ist keine Rolle für die \<web API\> zugewiesen“.
   >
   > Wenn Sie **Benutzerzuweisung erforderlich?** auf **Nein** eingestellt lassen, überprüft Azure AD die App-Rollenzuweisungen nicht, wenn ein Client ein Zugriffstoken für Ihre Web-API anfordert. Jeder Daemon-Client (d. h. jeder Client, der den Clientanmeldeinformations-Flow verwendet) kann ein Zugriffstoken für die API abrufen, indem er lediglich seine Zielgruppe angibt. Jede Anwendung kann auf die API zugreifen, ohne für sie Berechtigungen anfordern zu müssen.
   >
   > Ihre Web-API kann jedoch, wie im vorherigen Abschnitt erläutert, immer überprüfen, ob die Anwendung die richtige Rolle (d. h. vom Mandantenadministrator autorisiert) hat. Die API führt diese Überprüfung durch, indem sie überprüft, ob das Zugriffstoken einen Rollenanspruch hat und ob der Wert für diesen Anspruch richtig ist. Im vorherigen JSON-Beispiel ist der Wert `access_as_application`.

1. Wählen Sie **Speichern** aus.

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [App-Codekonfiguration](scenario-protected-web-api-app-configuration.md).

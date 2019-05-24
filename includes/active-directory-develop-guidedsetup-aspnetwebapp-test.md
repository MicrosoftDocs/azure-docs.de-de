---
title: Includedatei
description: Includedatei
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 8795c9ab0a4dbb76327d0ead48ed33fb0cff9e86
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121777"
---
## <a name="test-your-code"></a>Testen Ihres Codes

Um Ihre Anwendung in Visual Studio zu testen, drücken Sie **F5**, um das Projekt auszuführen. Der Browser wird geöffnet und leitet Sie zu „http://<span></span>localhost:{Port}“ weiter, wo die Schaltfläche **Bei Microsoft anmelden** angezeigt wird. Wählen Sie die Schaltfläche aus, um den Anmeldeprozess zu starten.

Wenn Sie Ihren Test ausführen möchten, verwenden Sie für die Anmeldung ein Microsoft Azure Active Directory-Konto (Azure AD) (Geschäfts-, Schul- oder Unikonto) oder ein persönliches Microsoft-Konto (<span>live.</span>com oder <span>outlook.</span>com).

![Bei Microsoft anmelden](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Mit einem Microsoft-Konto anmelden](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Anzeigen von Anwendungsergebnissen

Nachdem Sie sich angemeldet haben, wird der Benutzer zur Startseite Ihrer Website umgeleitet. Die Startseite ist die HTTPS-URL, die im Microsoft-Anwendungsregistrierungsportal in Ihre Anwendungsregistrierungsinformationen eingegeben wurde. Auf der Startseite wird die Willkommensnachricht *„Hallo \<Benutzer“*, ein Link zum Abmelden und ein Link zum Anzeigen der Benutzeransprüche angezeigt. Über den Link für die Benutzeransprüche wird der vorher erstellte *Claims*-Controller durchsucht.

### <a name="browse-to-see-the-users-claims"></a>Navigieren zu den Benutzeransprüchen

Um die Benutzeransprüche anzuzeigen, klicken Sie auf den Link zum Navigieren zur Controlleransicht. Diese ist nur für authentifizierte Benutzer verfügbar.

#### <a name="view-the-claims-results"></a>Anzeigen der Anspruchsergebnisse

Nachdem Sie die Controlleransicht aufgerufen haben, sollten Sie eine Tabelle mit den grundlegenden Eigenschaften des Benutzers sehen:

|Eigenschaft |Wert |BESCHREIBUNG |
|---|---|---|
|**Name** |Vollständiger Name des Benutzers | Vor- und Nachname des Benutzers
|**Benutzername** |Benutzer<span>@domain.com</span> | Der zur Identifizierung des Benutzers verwendete Benutzername
|**Betreff** |Subject |Eine Zeichenfolge, die den Benutzer im Internet eindeutig identifiziert|
|**Tenant ID** |Guid | Eine **GUID**, die die Azure AD-Organisation des Benutzers eindeutig identifiziert|

Darüber hinaus sollten Sie eine Tabelle aller Ansprüche sehen, die in der Authentifizierungsanforderung enthalten sind. Weitere Informationen finden Sie in der [Liste der im Azure AD-ID-Token enthaltenen Ansprüche](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Testen des Zugriffs auf eine Methode mit einem Authorize-Attribut (optional)

Führen Sie die folgenden Schritte aus, um den Zugriff als anonymer Benutzer auf einen mit dem `Authorize`-Attribut geschützten Controller zu testen:

1. Klicken Sie auf den Link zum Abmelden des Benutzers, und schließen Sie den Abmeldevorgang ab.
2. Geben Sie nun im Browser „http://<span></span>localhost:{Port}/claims“ ein, um auf den mit dem `Authorize`-Attribut geschützten Controller zuzugreifen.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Erwartete Ergebnisse nach dem Zugriff auf einen geschützten Controller

Sie werden für die Verwendung der geschützten Controlleransicht zur Authentifizierung aufgefordert.

## <a name="advanced-options"></a>Erweiterte Optionen

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Schutz der gesamten Website
Zum Schutz der gesamten Website fügen Sie in der Datei **Global.asax** unter der Methode `Application_Start`dem Filter `GlobalFilters` das `AuthorizeAttribute`-Attribut hinzu:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Einschränken des Anmeldezugriffs auf Ihre Anwendung

Wenn Sie die Anwendung nach dieser Anleitung erstellen, akzeptiert Ihre Anwendung standardmäßig sowohl Anmeldungen von persönlichen Konten (z.B. outlook.com, live.com u.a.) als auch von Geschäfts,- Schul- und Unikonten von Unternehmen oder Organisationen, die in Azure Active Directory integriert wurden. Diese Option wird für SaaS-Anwendungen empfohlen.

Um die Anmeldung bei und den Benutzerzugriff auf Ihre Anwendung einzuschränken, stehen mehrere Optionen zur Verfügung:

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Option 1: Beschränken der Benutzer auf die Active Directory-Instanz nur einer Organisation für die Anmeldung bei der Anwendung (Einzelmandant)

Diese Option ist ein häufiges Szenario für *LOB-Anwendungen*: Wenn Ihre Anwendung Anmeldungen nur von Konten akzeptieren soll, die zu einer bestimmten Azure Active Directory-Instanz gehören (einschließlich *Gastkonten* dieser Instanz), gehen Sie wie folgt vor:

1. Ändern Sie in der Datei **Web.config** den Wert für den Parameter `Tenant` von `Common` auf den Mandantennamen der Organisation, z. B. `contoso.onmicrosoft.com`.
2. Legen Sie in Ihrer [OWIN-Startup-Klasse](#configure-the-authentication-pipeline) das Argument `ValidateIssuer` auf `true` fest.

#### <a name="option-2-restrict-access-to-your-application-to-users-in-a-specific-list-of-organizations"></a>Option 2: Einschränken des Zugriffs auf Ihre Anwendung auf Benutzer in einer bestimmten Liste von Organisationen

Sie können den Anmeldezugriff auf bestimmte Benutzerkonten, die in einer Azure AD-Organisation in der Liste der zulässigen Organisationen enthalten sind, beschränken:
1. Legen Sie in Ihrer [OWIN-Startup-Klasse](#configure-the-authentication-pipeline) das Argument `ValidateIssuer` auf `true` fest.
2. Legen Sie den Wert des Parameters `ValidIssuers` auf die Liste der zulässigen Organisationen fest.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Option 3: Überprüfen von Ausstellern mithilfe einer benutzerdefinierten Methode

Sie können eine benutzerdefinierte Methode implementieren, um Aussteller mit dem **IssuerValidator**-Parameter zu überprüfen. Weitere Informationen zur Verwendung dieses Parameters finden Sie unter [TokenValidationParameters-Klasse](/previous-versions/visualstudio/dn464192(v=vs.114)).

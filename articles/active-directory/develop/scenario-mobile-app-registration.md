---
title: Registrieren mobiler Apps, die Web-APIs aufrufen | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie eine mobile App erstellen, die Web-APIs aufruft (Registrierung der App).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 35c1ffb370a158acc91e2378119055337e28580d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443091"
---
# <a name="register-mobile-apps-that-call-web-apis"></a>Registrieren mobiler Apps, die Web-APIs aufrufen

Dieser Artikel enthält Anweisungen, die Sie beim Registrieren einer von Ihnen erstellten mobilen Anwendung unterstützen.

## <a name="supported-account-types"></a>Unterstützte Kontotypen

Die Kontotypen, die von Ihrer mobilen Anwendung unterstützt werden, hängen von den Funktionen, die Sie aktivieren möchten, und den Abläufen ab, die Sie verwenden möchten.

### <a name="audience-for-interactive-token-acquisition"></a>Zielgruppe für den interaktiven Tokenabruf

Die meisten mobilen Anwendungen verwenden die interaktive Authentifizierung. Wenn Ihre App diese Form der Authentifizierung verwendet, können Sie Benutzer jedes [Kontotyps](quickstart-register-app.md) anmelden.

### <a name="audience-for-integrated-windows-authentication-username-password-and-b2c"></a>Zielgruppe für die integrierte Windows-Authentifizierung, Benutzername/Kennwort und B2C

Wenn Sie über eine UWP-App (Universelle Windows-Plattform) verfügen, können Sie zum Anmelden von Benutzern die integrierte Windows-Authentifizierung verwenden. Um die integrierte Windows-Authentifizierung oder eine Authentifizierung mit Benutzername und Kennwort verwenden zu können, muss Ihre Anwendung Benutzer bei Ihrem eigenen LOB-Entwicklermandanten (Line of Business) anmelden. In einem Szenario mit einem unabhängigen Softwareanbieter (ISV) kann Ihre Anwendung Benutzer bei Azure Active Directory-Organisationen anmelden. Diese Authentifizierungsflows werden für persönliche Microsoft-Konten nicht unterstützt.

Sie können Benutzer außerdem mithilfe von Identitäten aus sozialen Netzwerken anmelden, die eine B2C-Autorität und eine Richtlinie übergeben. Beim Verwenden dieser Methode stehen nur die interaktive Authentifizierung und die Authentifizierung mithilfe von Benutzername und Kennwort zur Verfügung. Die Authentifizierung über Benutzername und Kennwort wird derzeit nur für Xamarin.iOS, Xamarin.Android und UWP unterstützt.

Weitere Informationen finden Sie unter [Scenarios and supported authentication flows (Szenarien und unterstützte Authentifizierungsabläufe)](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) und [Scenarios and supported platforms and languages (Szenarien und unterstützte Plattformen und Sprachen)](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages).

## <a name="platform-configuration-and-redirect-uris"></a>Plattformkonfiguration und Umleitungs-URIs

### <a name="interactive-authentication"></a>Interaktive Authentifizierung

Beim Erstellen einer mobilen App mit interaktiver Authentifizierung stellt der Umleitungs-URI den wichtigsten Teil der Registrierung dar. Sie können interaktive Authentifizierung mithilfe der [Plattformkonfiguration auf dem Blatt **Authentifizierung**](https://aka.ms/MobileAppReg) einrichten.

Dadurch kann Ihre App einmaliges Anmelden (SSO) über Microsoft Authenticator (und über das Intune-Unternehmensportal für Android) nutzen. Sie unterstützt dann außerdem Richtlinien zur Geräteverwaltung.

Im Portal für die App-Registrierung gibt es eine Vorschaufunktion, die Sie beim Berechnen des Antwort-Broker-URI für iOS- und Android-Anwendungen unterstützt:

1. Wählen Sie im App-Registrierungsportal **Authentifizierung** > **Neue Benutzeroberfläche ausprobieren** aus.

   ![Das Blatt „Authentifizierung“, auf dem Sie eine neue Benutzeroberfläche auswählen](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Wählen Sie **Eine Plattform hinzufügen** aus.

   ![Hinzufügen einer Plattform](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Wenn die Liste der Plattformen unterstützt wird, wählen Sie **iOS** aus.

   ![Wählen Sie eine mobile Anwendung aus](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Geben Sie Ihre Paket-ID ein, und wählen Sie dann **Registrieren** aus.

   ![Geben Sie Ihre Paket-ID ein.](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Wenn Sie die Schritte ausgeführt haben, wird der Umleitungs-URI berechnet, wie in der folgenden Abbildung dargestellt.

![Der resultierende Umleitungs-URI](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Wenn Sie den Umleitungs-URI stattdessen manuell konfigurieren möchten, können Sie dazu das Anwendungsmanifest verwenden. Dies ist das empfohlene Format für das Manifest:

- **iOS:** `msauth.<BUNDLE_ID>://auth`
  - Geben Sie z.B. `msauth.com.yourcompany.appName://auth` ein.
- **Android:** `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Sie können den Android-Signaturhash über den KeyTool-Befehl mit dem Release- oder Debugschlüssel generieren.

### <a name="username-password-authentication"></a>Authentifizierung mit Benutzername/Kennwort

Wenn Ihre App ausschließlich die Authentifizierung über Benutzernamen und Kennwort verwendet, brauchen Sie für Ihre Anwendung keinen Umleitungs-URI zu registrieren. Dieser Ablauf führt einen Roundtrip zum Microsoft Identity Platform-Endpunkt, Version 2.0, aus. Ihre Anwendung wird nicht über einen bestimmten URI zurückgerufen.

Allerdings müssen Sie Ihre Anwendung als öffentliche Clientanwendung identifizieren. Beginnen Sie zu diesem Zweck im Abschnitt **Authentifizierung** Ihrer Anwendung. Wählen Sie im Unterabschnitt **Erweiterte Einstellungen** im Abschnitt **Standardclienttyp** neben **Hiermit wird eine Anwendung als öffentlicher Client eingestuft** **Ja** aus.

## <a name="api-permissions"></a>API-Berechtigungen

Mobile Anwendungen rufen APIs für angemeldete Benutzer auf. Ihre Anwendung muss delegierte Berechtigungen anfordern. Diese Berechtigungen werden auch als Bereiche bezeichnet. Je nach der gewünschten Erfahrung können Sie delegierte Berechtigung statisch über das Azure-Portal anfordern. Alternativ können Sie sie auch dynamisch zur Laufzeit anfordern.

Wenn Sie Berechtigungen statisch registrieren, sind sie für Administratoren leicht zu genehmigen. Die statische Registrierung stellt das empfohlene Verfahren dar.

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [App-Codekonfiguration](scenario-mobile-app-configuration.md).

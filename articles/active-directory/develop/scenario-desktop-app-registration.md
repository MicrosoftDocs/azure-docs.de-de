---
title: Registrieren von Desktop-Apps, die Web-APIs aufrufen | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie eine Desktop-App erstellen, die Web-APIs aufruft (App-Registrierung)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 155df45d975a30991edc80d587445d699a8d2695
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103225"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>Desktop-App, die Web-APIs aufruft: App-Registrierung

In diesem Artikel sind die Besonderheiten zur App-Registrierung bei einer Desktopanwendung aufgeführt.

## <a name="supported-account-types"></a>Unterstützte Kontotypen

Die in einer Desktopanwendung unterstützten Kontotypen sind von der gewünschten Funktion abhängig. Aufgrund dieser Beziehung sind die unterstützten Kontotypen von den Flows abhängig, die Sie verwenden möchten.

### <a name="audience-for-interactive-token-acquisition"></a>Zielgruppe für den interaktiven Tokenabruf

Wenn Ihre Desktopanwendung die interaktive Authentifizierung verwendet, können Sie Benutzer jedes [Kontotyps](quickstart-register-app.md) anmelden.

### <a name="audience-for-desktop-app-silent-flows"></a>Zielgruppe für automatische Desktop-App-Flüsse

- Um die integrierte Windows-Authentifizierung oder Benutzername und Kennwort verwenden zu können, muss Ihre Anwendung Benutzer bei Ihrem Mandanten anmelden, beispielsweise wenn Sie LOB-Entwickler (Line of Business) sind. In Azure Active Directory-Organisationen muss Ihre Anwendung Benutzer bei Ihrem Mandanten anmelden, wenn es sich um ein ISV-Szenario handelt. Diese Authentifizierungsflows werden für persönliche Microsoft-Konten nicht unterstützt.
- Wenn Sie Benutzer mit Identitäten sozialer Netzwerke anmelden, die eine B2C-Autorität und -Richtlinie übergeben, können Sie ausschließlich die interaktive Authentifizierung oder die Authentifizierung über Benutzername und Kennwort verwenden.

## <a name="redirect-uris"></a>Umleitungs-URIs

Die in einer Desktopanwendung zu verwendenden Umleitungs-URIs hängen von dem Flow ab, den Sie verwenden möchten.

- Bei Verwendung der interaktiven Authentifizierung oder des Gerätecodeflows verwenden Sie `https://login.microsoftonline.com/common/oauth2/nativeclient`. Um diese Konfiguration zu erzielen, wählen Sie im Abschnitt **Authentifizierung** für Ihre Anwendung die entsprechende URL aus.

  > [!IMPORTANT]
  > Die Verwendung von `https://login.microsoftonline.com/common/oauth2/nativeclient` als Umleitungs-URI wird als bewährte Sicherheitsmaßnahme empfohlen.  Wenn kein Umleitungs-URI angegeben wird, verwendet MSAL.NET standardmäßig `urn:ietf:wg:oauth:2.0:oob`, was nicht empfohlen wird.  Diese Standardeinstellung wird als Breaking Change in der nächsten Hauptversion aktualisiert.

- Wenn Sie eine native Objective-C-oder Swift-App für macOS erstellen, sollten Sie den Umleitungs-URI basierend auf der Paket-ID Ihrer Anwendung im folgenden Format registrieren: `msauth.<your.app.bundle.id>://auth`. Ersetzen Sie `<your.app.bundle.id>` durch die Paket-ID Ihrer Anwendung.
- Wenn Ihre App nur die integrierte Windows-Authentifizierung oder Benutzername und Kennwort verwendet, müssen Sie für Ihre Anwendung keinen Umleitungs-URI registrieren. Diese Flows führen einen Roundtrip zum Microsoft Identity Platform v 2.0-Endpunkt aus. Ihre Anwendung wird nicht über einen bestimmten URI zurückgerufen.
- Konfigurieren Sie Ihre Anwendung als öffentliche Clientanwendung, um [Gerätecodeflow](scenario-desktop-acquire-token.md#device-code-flow), [Integrierte Windows-Authentifizierung](scenario-desktop-acquire-token.md#integrated-windows-authentication) sowie [Benutzername und Kennwort](scenario-desktop-acquire-token.md#username-and-password) von einer vertraulichen Clientanwendung mit einem Clientanmeldeinformationsflow zu unterscheiden, der in [Daemonanwendungen](scenario-daemon-overview.md) verwendet wird, die alle keinen Umleitungs-URI benötigen. Gehen Sie für diese Konfiguration wie folgt vor:

    1. Wählen Sie im <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> unter **App-Registrierungen** Ihre App aus, und wählen Sie dann **Authentifizierung** aus.
    1. Wählen Sie unter **Erweiterte Einstellungen** > **Öffentliche Clientflows zulassen** > **Folgende Flows für Mobilgerät und Desktop aktivieren:** die Option **Ja** aus.

        :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Aktivieren der Einstellung für die Behandlung als öffentlicher Client im Bereich „Authentifizierung“ im Azure-Portal":::

## <a name="api-permissions"></a>API-Berechtigungen

Desktopanwendungen rufen APIs für den angemeldeten Benutzer auf. Sie müssen delegierte Berechtigungen anfordern. Sie können keine Anwendungsberechtigungen anfordern, die nur in [Daemon-Anwendungen](scenario-daemon-overview.md) verarbeitet werden.

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [App-Codekonfiguration](scenario-desktop-app-configuration.md).

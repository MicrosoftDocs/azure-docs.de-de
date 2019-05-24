---
title: Mobile App, die Web-APIs aufruft (Codekonfiguration der App) – Microsoft Identity Platform
description: Erfahren Sie, wie Sie eine mobile App erstellen, die Web-APIs aufruft (Codekonfiguration der App).
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b6ebab0eeca6895e1c7a0f6008972030d81da42
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962404"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>Mobile App, die Web-APIs aufruft – App-Registrierung

Dieser Artikel enthält eine Anleitung zur App-Registrierung, die zur Erstellung einer mobilen Anwendung erforderlich ist.

## <a name="supported-account-types"></a>Unterstützte Kontotypen

Welche Kontotypen in mobilen Anwendungen unterstützt werden, hängt von den Benutzern Ihrer App und von den Funktionen ab, die Sie bereitstellen möchten.

## <a name="platform-configuration-and-redirect-uris"></a>Plattformkonfiguration und Umleitungs-URIs  

Beim Erstellen einer mobilen App ist der wichtigste Registrierungsschritt die Angabe des Umleitungs-URI. Sie können diesen über die [Plattformkonfiguration auf dem Blatt „Authentifizierung“](https://aka.ms/MobileAppReg) festlegen.

Dadurch kann Ihre App einmaliges Anmelden (SSO) über Microsoft Authenticator (und über das Intune-Unternehmensportal für Android) nutzen und Richtlinien zur Geräteverwaltung unterstützen.

Wenn Sie den Umleitungs-URI stattdessen manuell konfigurieren möchten, können Sie dazu das Anwendungsmanifest verwenden. Folgende Formate werden empfohlen:

- ***iOS:*** `msauth.<BUNDLE_ID>://auth`
- ***Android:*** `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Der Android-Signaturhash kann über den KeyTool-Befehl mit dem Release- oder Debugschlüssel generiert werden.

## <a name="api-permissions"></a>API-Berechtigungen

Mobile Anwendungen rufen APIs für angemeldete Benutzer auf. Ihre App muss dazu delegierte Berechtigungen anfordern, die auch als Bereiche bezeichnet werden. Dieser Vorgang kann entweder statisch über das Azure-Portal oder dynamisch zur Laufzeit erfolgen. Durch die statische Registrierung von Berechtigungen können Administratoren Ihre App leicht genehmigen, weswegen diese Vorgehensweise empfohlen wird.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Abrufen eines Tokens](scenario-mobile-acquire-token.md)

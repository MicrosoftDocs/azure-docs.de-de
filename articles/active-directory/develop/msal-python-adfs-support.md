---
title: Unterstützung der Azure Active Directory-Verbunddienste (MSAL Python)
titleSuffix: Microsoft identity platform
description: Erfahren Sie mehr über die Unterstützung der Active Directory-Verbunddienste (AD FS) in der Microsoft-Authentifizierungsbibliothek für Python.
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/23/2019
ms.author: abpati
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: d847e38a6114b0e5f8d56f6e994aa47dfbe929e7
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99583704"
---
# <a name="active-directory-federation-services-support-in-msal-for-python"></a>Unterstützung der Active Directory-Verbunddienste in MSAL für Python

Mithilfe der Active Directory-Verbunddienste (AD FS) in Windows Server können Sie den von Ihnen entwickelten Anwendungen die OpenID Connect- und OAuth 2.0-basierte Authentifizierung und Autorisierung hinzufügen, indem Sie die Microsoft-Authentifizierungsbibliothek (MSAL) für Python verwenden. Mithilfe der MSAL für Python-Bibliothek kann Ihre App Benutzer direkt gegen AD FS authentifizieren. Weitere Informationen zu den Szenarien finden Sie unter [AD FS-Szenarien für Entwickler](/windows-server/identity/ad-fs/ad-fs-development).

Es gibt in der Regel zwei Möglichkeiten, sich bei AD FS zu authentifizieren:

- MSAL Python kommuniziert mit Azure Active Directory, das sich wiederum selbst im Verbund mit anderen Identitätsanbietern befindet. Der Verbund erfolgt über AD FS. MSAL Python verbindet sich mit Azure AD, das Benutzer, anmeldet, die in Azure AD verwaltet werden (verwaltete Benutzer), oder Benutzer, die von einem anderen Identitätsanbieter verwaltet werden, z. B. AD FS (Verbundbenutzer). MSAL Python erkennt nicht, ob ein Benutzer zu einem Verbund gehört. Es kommuniziert lediglich mit Azure AD. Die in diesem Fall verwendete [Autorität](msal-client-application-configuration.md#authority) ist die übliche Autorität (Hostname der Autorität + „tenant“, „common“ oder „organizations“).
- MSAL Python kommuniziert direkt mit einer ADFS-Autorität. Dies wird nur von AD FS 2019 und höher unterstützt.

## <a name="connect-to-active-directory-federated-with-ad-fs"></a>Verbinden mit Active Directory im Verbund mit AD FS

### <a name="acquire-a-token-interactively-for-a-federated-user"></a>Interaktives Abrufen eines Tokens für einen Verbundbenutzer

Folgendes gilt unabhängig davon, ob Sie eine direkte Verbindung mit Active Directory-Verbunddiensten (AD FS) oder über Active Directory herstellen.

Wenn Sie `acquire_token_by_authorization_code` oder `acquire_token_by_device_flow` aufrufen, verläuft die Benutzeranmeldung normalerweise wie folgt:

1. Der Benutzer gibt seine Konto-ID ein.
2. Azure AD blendet kurz die Meldung „Weiterleitung zur Anmeldeseite Ihrer Organisation“ ein, und der Benutzer wird zur Anmeldeseite des Identitätsanbieters umgeleitet. Die Anmeldeseite ist in der Regel angepasst und weist das Logo der Organisation auf.

Die unterstützten AD FS-Versionen in diesem Verbundszenario lauten wie folgt:
- Active Directory-Verbunddienste FS v2
- Active Directory-Verbunddienste v3 (Windows Server 2012 R2)
- Active Directory-Verbunddienste v4 (AD FS 2016)

### <a name="acquire-a-token-via-username-and-password"></a>Abrufen eines Tokens mittels Benutzername und Kennwort

Folgendes gilt unabhängig davon, ob Sie eine direkte Verbindung mit Active Directory-Verbunddiensten (AD FS) oder über Active Directory herstellen.

Beim Abrufen eines Tokens mit `acquire_token_by_username_password` ruft MSAL Python den zu kontaktierenden Identitätsanbieter anhand des Benutzernamens ab. MSAL Python ruft ein [SAML 1.1-Token](reference-saml-tokens.md) vom Identitätsanbieter ab, das es dann Azure AD bereitstellt, das wiederum das JSON Web Token (JWT) zurückgibt.

## <a name="connecting-directly-to-ad-fs"></a>Herstellen einer direkten Verbindung mit AD FS

Bei einer direkten Verbindung mit AD FS lautet die Autorität, die Sie zum Erstellen Ihrer Anwendung verwenden möchten, ähnlich wie `https://somesite.contoso.com/adfs/`.

MSAL Python unterstützt ADFS 2019.

Es unterstützt keine direkte Verbindung mit ADFS 2016 oder ADFS v2. Verwenden Sie in Szenarien, die eine direkte Verbindung mit AD FS 2016 erfordern, die neueste Version von ADAL Python. Nachdem Sie ein Upgrade Ihres lokalen Systems auf AD FS 2019 vorgenommen haben, können Sie MSAL Python verwenden.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Verbundfall finden Sie unter [Konfigurieren des Verhaltens der Azure Active Directory-Anmeldung für eine Anwendung mit einer Richtlinie für die Startbereichsermittlung (Home Realm Discovery, HDR)](../manage-apps/configure-authentication-for-federated-users-portal.md).

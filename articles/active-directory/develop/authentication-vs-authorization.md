---
title: Authentifizierung im Vergleich zu Autorisierung | Azure
titleSuffix: Microsoft identity platform
description: Enthält eine Beschreibung der Grundlagen der Authentifizierung und Autorisierung bei der Microsoft Identity Platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 5244a8e572abb56c00d87d0bdd7e8d1291af9b9e
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581906"
---
# <a name="authentication-vs-authorization"></a>Authentifizierung im Vergleich zu Autorisierung

Dieser Artikel enthält eine Definition der Authentifizierung und Autorisierung. Darüber hinaus wird kurz beschrieben, wie Sie die Microsoft Identity Platform zum Authentifizieren und Autorisieren von Benutzern in Ihren Web-Apps, Web-APIs oder Apps, von denen geschützte Web-APIs aufgerufen werden, nutzen können. Wenn Sie auf einen Begriff stoßen, mit dem Sie nicht vertraut sind, können Sie einen Blick in unser [Glossar](developer-glossary.md) werfen oder sich unsere [Videos zu Microsoft Identity Platform](identity-videos.md) ansehen, in denen die grundlegenden Konzepte behandelt werden.

## <a name="authentication"></a>Authentifizierung

Bei der *Authentifizierung* weisen Sie nach, dass Sie die Person sind, als die Sie sich ausgeben. Dies wird manchmal auch kurz als *AuthN* bezeichnet. Für die Microsoft Identity Platform wird das [OpenID Connect](https://openid.net/connect/)-Protokoll für die Verarbeitung der Authentifizierungsvorgänge genutzt.

## <a name="authorization"></a>Authorization

Die *Autorisierung* ist die Gewährung einer Handlungsberechtigung für eine authentifizierte Person. Sie gibt an, auf welche Daten Sie zugreifen dürfen und welche Aktionen Sie damit ausführen können. Die Autorisierung wird auch kurz als *AuthZ* bezeichnet. Für die Microsoft Identity Platform wird das [OAuth 2.0](https://oauth.net/2/)-Autorisierungsprotokoll für die Verarbeitung der Autorisierungsvorgänge genutzt.

## <a name="authentication-and-authorization-using-the-microsoft-identity-platform"></a>Authentifizierung und Autorisierung mit Microsoft Identity Platform

Die Erstellung von Apps, für die jeweils die eigenen Informationen zum Benutzernamen und Kennwort beibehalten werden, führt zu einem hohen Verwaltungsaufwand, wenn Benutzer App-übergreifend hinzugefügt oder entfernt werden. Stattdessen kann diese Aufgabe von Ihren Apps an einen zentralen Identitätsanbieter delegiert werden.

Azure Active Directory (Azure AD) ist ein zentraler Identitätsanbieter in der Cloud. Indem die Authentifizierung und Autorisierung an diesen Anbieter delegiert wird, sind Szenarien der folgenden Art möglich:

- Richtlinien für bedingten Zugriff, bei denen sich ein Benutzer an einem bestimmten Standort befinden muss.
- Nutzung der [mehrstufigen Authentifizierung (Multi-Factor Authentication, MFA)](../authentication/concept-mfa-howitworks.md) (auch als zweistufige Authentifizierung (2FA) bezeichnet).
- Einmaliges Anmelden eines Benutzers und anschließende automatische Anmeldung bei allen Web-Apps, für die dasselbe zentrale Verzeichnis genutzt wird. Diese Funktion wird als *einmaliges Anmelden (Single Sign-On, SSO)* bezeichnet.

Die Microsoft Identity Platform vereinfacht die Autorisierung und Authentifizierung für Anwendungsentwickler per „Identity-as-a-Service“-Funktion. Damit Sie schnell mit dem Schreiben von Code beginnen können, werden Branchenstandardprotokolle und Open-Source-Bibliotheken für unterschiedliche Plattformen unterstützt. Entwickler können nicht nur Anwendungen erstellen, bei denen alle Microsoft-Identitäten angemeldet werden, sondern auch Token zum Aufrufen von [Microsoft Graph](https://developer.microsoft.com/graph/) oder zum Zugreifen auf Microsoft-APIs oder andere APIs abrufen, die von Entwicklern erstellt wurden.

In diesem Video werden die Microsoft Identity Platform und die Grundlagen der modernen Authentifizierung beschrieben: 

> [!VIDEO https://www.youtube.com/embed/tkQJSHFsduY]

Hier finden Sie einen Vergleich der Protokolle, die von der Microsoft Identity Platform verwendet werden:

* **OAuth und OpenID Connect**: Von der Plattform wird OAuth für die Autorisierung und OpenID Connect (OIDC) für die Authentifizierung genutzt. OpenID Connect baut auf OAuth 2.0 auf, sodass beide eine ähnliche Terminologie und einen ähnlichen Flow aufweisen. Sie können mit nur einer Anforderung sogar sowohl einen Benutzer authentifizieren (mit OpenID Connect) als auch die Autorisierung des Zugriffs auf eine geschützte Ressource durchführen (mit OAuth 2.0), die sich im Besitz des Benutzers befindet. Weitere Informationen finden Sie unter [OAuth 2.0- und OpenID Connect-Protokolle](active-directory-v2-protocols.md) und [OpenID Connect-Protokoll](v2-protocols-oidc.md).
* **OAuth und SAML**: Von der Plattform wird OAuth 2.0 für die Autorisierung und SAML für die Authentifizierung genutzt. Weitere Informationen dazu, wie Sie diese Protokolle zusammen zum Authentifizieren eines Benutzers und zum Durchführen der Autorisierung für den Zugriff auf eine geschützte Ressource einsetzen können, finden Sie unter [Microsoft Identity Platform und OAuth 2.0-SAML-Bearerassertionsflow](./scenario-token-exchange-saml-oauth.md).
* **OpenID Connect und SAML**: Für die Plattform wird sowohl OpenID Connect als auch SAML zum Authentifizieren eines Benutzers und zum Aktivieren des einmaligen Anmeldens verwendet. Die SAML-Authentifizierung wird häufig mit Identitätsanbietern wie Active Directory-Verbunddienste (AD FS) im Verbund mit Azure AD und daher häufig in Unternehmensanwendungen genutzt. OpenID Connect wird häufig für Apps verwendet, die sich ausschließlich in der Cloud befinden, z. B. mobile Apps, Websites und Web-APIs.

## <a name="next-steps"></a>Nächste Schritte

Weitere Themen zu den Grundlagen der Authentifizierung und Autorisierung:

* Unter [Sicherheitstoken](security-tokens.md) erfahren Sie, wie Zugriffstoken, Aktualisierungstoken und ID-Token bei der Autorisierung und Authentifizierung verwendet werden.
* Unter [Anwendungsmodell](application-model.md) erhalten Sie Informationen zum Registrierungsvorgang für Ihre Anwendung, damit diese in die Microsoft Identity Platform integriert werden kann.
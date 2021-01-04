---
title: SAML-Authentifizierung mit Azure Active Directory
description: Architektonischer Leitfaden zum Erreichen der SAML-Authentifizierung mit Azure Active Directory
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cd2aa5e9ff8cbaeead69f11d2e3de7f760b53ec
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168643"
---
# <a name="saml-authentication-with-azure-active-directory"></a>SAML-Authentifizierung mit Azure Active Directory

Security Assertion Markup Language (SAML) ist ein offener Standard für den Austausch von Authentifizierungs- und Autorisierungsdaten zwischen einem Identitätsanbieter und einem Dienstanbieter. SAML ist eine XML-basierte Markupsprache für Sicherheitsassertionen – Anweisungen, mit denen Dienstanbieter Entscheidungen zur Zugriffssteuerung treffen. 

Die SAML-Spezifikation definiert drei Rollen:

* Prinzipal, in der Regel ein Benutzer
* Identitätsanbieter (Identity Provider, IdP)
* Dienstanbieter (Service Provider, SP)


## <a name="use-when"></a>Wenn folgende Bedingungen vorliegen

Es müssen SSO-Funktionen (Single Sign-On, einmaliges Anmelden) für eine SAML-Anwendung des Unternehmens bereitgestellt werden.

Einer der wichtigsten Anwendungsfälle für SAML ist das einmalige Anmelden, insbesondere da SAML dieses Feature über Sicherheitsdomänen hinweg nutzbar macht. Allerdings gibt es auch noch andere Anwendungsfälle (so genannte Profile). 

![Architekturdiagramm für SAML](./media/authentication-patterns/saml-auth.png)

## <a name="components-of-system"></a>Komponenten des Systems

* **Benutzer:** Der Benutzer fordert einen Dienst von der Anwendung an.

* **Webbrowser**: Die Komponente, mit der der Benutzer interagiert.

* **Web-App**: Unternehmensanwendung, die SAML unterstützt und Azure AD als Identitätsanbieter verwendet.

* **Token**: Eine SAML-Assertion (auch als SAML-Token bezeichnet), die eine Reihe von Aussagen des Identitätsanbieters über die Eigenschaften und Berechtigungen des Prinzipals (Benutzers) enthält. Die Assertion bzw. das Token enthält Authentifizierungsinformationen, Attribute und Anweisungen zu Autorisierungsentscheidungen.

* **Azure AD**: Ein Identitätsanbieter der Unternehmenscloud, der einmaliges Anmelden und mehrstufige Authentifizierung für SAML-Apps bereitstellt. Dieser Anbieter synchronisiert, pflegt und verwaltet Identitätsinformationen für Benutzer und stellt gleichzeitig Authentifizierungsdienste für Anwendungen bereit, die diese benötigen. 

## <a name="implement-saml-authentication-with-azure-ad"></a>Implementieren der SAML-Authentifizierung mit Azure AD

* [Tutorials zur Integration von SaaS-Anwendungen mithilfe von Azure Active Directory](../saas-apps/tutorial-list.md) 

* [Konfigurieren des SAML-basierten einmaligen Anmeldens für nicht im Katalog aufgeführte Anwendungen](../manage-apps/add-application-portal.md) 

* [Verwendung des SAML-Protokolls durch Azure AD](../develop/active-directory-saml-protocol-reference.md)
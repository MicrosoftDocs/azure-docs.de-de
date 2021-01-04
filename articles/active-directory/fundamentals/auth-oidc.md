---
title: OpenID Connect-Authentifizierung mit Azure Active Directory
description: Architekturleitfaden zum Erzielen der OpenID Connect-Authentifizierung mit Azure Active Directory
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
ms.openlocfilehash: 0e5bf7e51de38d42e64f6737e687c5946a464160
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168660"
---
# <a name="openid-connect-authentication-with-azure-active-directory"></a>OpenID Connect-Authentifizierung mit Azure Active Directory

OpenID Connect (OIDC) ist ein Authentifizierungsprotokoll, das auf dem OAuth2-Protokoll basiert (dieses wird für die Autorisierung verwendet). OIDC verwendet die standardisierten Nachrichtenflows von OAuth2 zum Bereitstellen von Identitätsdiensten. 

Das Entwurfsziel von OIDC lautet „einfache Dinge einfach und komplizierte Dinge möglich machen“. OIDC ermöglicht es Entwicklern, ihre Benutzer über Websites und App hinweg zu authentifizieren, ohne Kennwortdateien anlegen und verwalten zu müssen. Damit erhalten App-Entwickler eine sichere Möglichkeit, die Identität einer Person zu überprüfen, die eine mit der Anwendung verknüpfte browserbasierte oder native App verwendet.

Die Authentifizierung des Benutzers muss bei einem Identitätsanbieter erfolgen, der die Sitzungs- bzw. Anmeldeinformationen des Benutzers überprüft. Zu diesem Zweck benötigen Sie einen vertrauenswürdigen Agent. Native Apps starten hierzu in der Regel den Systembrowser. Eingebettete Sichten werden als nicht vertrauenswürdig eingestuft, weil es keine Möglichkeit gibt, die App daran zu hindern, das Benutzerkennwort auszuspionieren. 

Zusätzlich zur Authentifizierung kann der Benutzer zu einer Einwilligung aufgefordert werden. Eine Einwilligung ist die explizite Zustimmung des Benutzers, einer Anwendung den Zugriff auf geschützte Ressourcen zu gestatten. Die Einwilligung unterscheidet sich von der Authentifizierung, weil die Einwilligung nur einmal für eine Ressource erteilt werden muss. Eine Einwilligung bleibt so lange gültig, bis der Benutzer oder Administrator sie manuell widerruft. 

## <a name="use-when"></a>Wenn folgende Bedingungen vorliegen

Benutzereinwilligung und Webanmeldung sind erforderlich.

![Architekturdiagramm](./media/authentication-patterns/oidc-auth.png)

## <a name="components-of-system"></a>Komponenten des Systems

* **Benutzer:** Der Benutzer fordert einen Dienst von der Anwendung an.

* **Vertrauenswürdiger Agent**: Die Komponente, mit der der Benutzer interagiert. Dieser vertrauenswürdige Agent ist in der Regel ein Webbrowser.

* **Anwendung**: Die Anwendung – der Ressourcenserver – ist der Ort, an dem die Ressource oder die Daten abgelegt sind. Die Anwendung vertraut darauf, dass der Identitätsanbieter den vertrauenswürdigen Agent sicher authentifiziert und autorisiert. 

* **Azure AD**: Der OIDC-Anbieter – auch als Identitätsanbieter bezeichnet – verarbeitet auf sichere Weise sämtliche Vorgänge im Zusammenhang mit den Informationen des Benutzers, dessen Zugriff und den Vertrauensstellungen zwischen den Beteiligten in einem Flow. Der Anbieter authentifiziert die Identität des Benutzers, gewährt und widerruft Zugriff auf Ressourcen und stellt Token aus. 

## <a name="implement-oidc-with-azure-ad"></a>Implementieren von OIDC mit Azure AD

* [Integrieren von Anwendungen in Azure Active Directory](../saas-apps/tutorial-list.md) 

* [OAuth 2.0 und OpenID Connect-Protokolle auf der Microsoft Identity Platform](../develop/active-directory-v2-protocols.md) 

* [Microsoft Identity Platform und das OpenID Connect-Protokoll](../develop/v2-protocols-oidc.md) 

* [Webanmeldungen mit OpenID Connect in Azure Active Directory B2C](../../active-directory-b2c/openid-connect.md) 

* [Schützen Ihrer Anwendung mithilfe von OpenID Connect und Azure AD](/learn/modules/secure-app-with-oidc-and-azure-ad/) 


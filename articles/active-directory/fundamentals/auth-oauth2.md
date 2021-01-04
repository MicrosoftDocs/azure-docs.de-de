---
title: OAUTH 2.0-Authentifizierung mit Azure Active Directory
description: Architekturleitfaden zum Erzielen der OAUTH 2.0-Authentifizierung mit Azure Active Directory
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
ms.openlocfilehash: c1604d79ce5eb9949028cd677b340bf3d4b09f6c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172838"
---
# <a name="oauth-20-authentication-with-azure-active-directory"></a>OAuth 2.0-Authentifizierung mit Azure Active Directory

OAuth 2.0 ist ein Branchenprotokoll für die Autorisierung. Es ermöglicht einem Benutzer, eingeschränkten Zugriff auf die geschützten Ressourcen zu gewähren. OAuth ist speziell für die Verwendung mit HTTP (Hypertext Transfer-Protokoll) konzipiert und trennt die Rolle des Clients vom Ressourcenbesitzer. Der Client fordert den Zugriff auf die Ressourcen an, die vom Ressourcenbesitzer gesteuert und vom Ressourcenserver gehostet werden. Der Ressourcenserver gibt Zugriffstoken mit der Genehmigung des Ressourcenbesitzers aus. Der Client verwendet die Zugriffstoken für den Zugriff auf die geschützten Ressourcen, die vom Ressourcenserver gehostet werden. 

OAuth 2.0 hängt direkt mit OpenID Connect (OIDC) zusammen. OIDC ist eine Autorisierungs- und Autorisierungsebene, die auf OAuth 2.0 basiert, und ist daher nicht mit OAuth 1.0 abwärtskompatibel. Azure Active Directory (Azure AD) unterstützt alle OAuth 2.0-Flows. 

## <a name="use-when"></a>Zu verwenden in folgenden Fällen:

Für Rich Client-Szenarien und Szenarien mit modernen Apps sowie den RESTful-Web-API-Zugriff.

![Diagramm der Architektur](./media/authentication-patterns/oauth.png)

## <a name="components-of-system"></a>Komponenten des Systems

* **Benutzer:** Der Benutzer fordert einen Dienst von der Webanwendung (Web-App) an. Der Benutzer ist in der Regel der Ressourcenbesitzer, der die Daten besitzt und die Möglichkeit hat, Clients den Zugriff auf die Daten oder die Ressource zu gewähren. 

* **Webbrowser:** Der Webbrowser, mit dem der Benutzer interagiert, ist der OAuth-Client. 

* **Web-App**: Die Web-App oder der Ressourcenserver ist der Ort, an dem die Ressourcen oder die Daten abgelegt sind. Er vertraut auf die sichere Authentifizierung und Autorisierung des OAuth-Clients durch den Autorisierungsserver. 

* **Azure AD**: Azure AD ist der Autorisierungsserver, der auch als Identitätsanbieter (Identity Provider, IdP) bezeichnet wird. Er verarbeitet auf sichere Weise alles im Zusammenhang mit den Informationen des Benutzers, dessen Zugriff und den Vertrauensstellungen. Er ist für die Ausgabe der Token zuständig, mit denen der Zugriff auf Ressourcen gewährt und widerrufen wird.

## <a name="implement-oauth-20-with-azure-ad"></a>Implementieren von OAuth 2.0 mit Azure AD

* [Integrieren von Anwendungen in Azure Active Directory](../saas-apps/tutorial-list.md) 

* [OAuth 2.0- und OpenID Connect-Protokolle auf der Microsoft Identity Platform](../develop/active-directory-v2-protocols.md) 

* [Anwendungstypen und OAuth2](../develop/v2-app-types.md) 


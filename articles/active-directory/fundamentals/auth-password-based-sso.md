---
title: Kennwortbasierte Authentifizierung mit Azure Active Directory
description: Architekturleitfaden zum Implementieren der kennwortbasierten Authentifizierung mit Azure Active Directory
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
ms.openlocfilehash: 5bd6a5c8af117bf6cb39969a5f1b1f17ff08681c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172821"
---
# <a name="password-based-authentication-with-azure-active-directory"></a>Kennwortbasierte Authentifizierung mit Azure Active Directory

Beim kennwortbasierten einmaligen Anmelden (Single Sign-On, SSO) wird der vorhandene Authentifizierungsvorgang für die Anwendung verwendet. Wenn Sie das kennwortbasierte einmalige Anmelden aktivieren, werden Benutzeranmeldeinformationen von Azure Active Directory (Azure AD) erfasst, verschlüsselt und sicher im Verzeichnis gespeichert. Azure AD stellt der Anwendung den Benutzernamen und das Kennwort bereit, wenn der Benutzer versucht, sich anzumelden.

Verwenden Sie das kennwortbasierte einmalige Anmelden, wenn eine Anwendung sich mit Benutzername und Kennwort anstelle von Zugriffstoken und Headern authentifiziert. Das kennwortbasierte einmalige Anmelden unterstützt jede cloudbasierte Anwendung, die über eine HTML-basierte Anmeldeseite verfügt. 

## <a name="use-when"></a>Wenn folgende Bedingungen vorliegen

Sie müssen Ihre Anwendung mit Vorauthentifizierung schützen und SSO über Kennworttresore für Web-Apps bereitstellen.

![Architekturdiagramm](./media/authentication-patterns/password-based-sso-auth.png)


## <a name="components-of-system"></a>Komponenten des Systems

* **Benutzer:** Der Benutzer greift über „Meine Apps“ oder direkt durch Besuchen der Website auf eine formularbasierte Anwendung zu. 

* **Webbrowser**: Die Komponente, mit der der Benutzer interagiert, um auf die externe URL der Anwendung zuzugreifen. Der Benutzer greift über die MyApps-Erweiterung auf die formularbasierte Anwendung zu. 

* **MyApps-Erweiterung**: Hiermit wird die konfigurierte kennwortbasierte SSO-Anwendung identifiziert, und die Anmeldeinformationen werden dem Anmeldeformular bereitgestellt. Die MyApps-Erweiterung ist im Webbrowser installiert. 

* **Azure AD**: Hiermit wird der Benutzer authentifiziert.

## <a name="implement-password-based-sso-with-azure-ad"></a>Implementieren des kennwortbasierten einmaligen Anmeldens mit Azure AD

* [Worum handelt es sich beim einmaligen Anmelden (Single Sign-On, SSO)?](../manage-apps/what-is-single-sign-on.md) 

* [Grundlegendes zum kennwortbasierten einmaligen Anmelden](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)

* [Kennworttresore (Password Vaulting) für einmaliges Anmelden mit Anwendungsproxy](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md)


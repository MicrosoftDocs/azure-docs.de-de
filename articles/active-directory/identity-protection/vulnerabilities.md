---
title: Von Azure Active Directory Identity Protection erkannte Sicherheitsrisiken
description: Übersicht über die von Azure Active Directory Identity Protection erkannten Sicherheitslücken
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: article
ms.date: 04/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e792551f4cac857f56454c67d527e01cb9c4281
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66113125"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Von Azure Active Directory Identity Protection erkannte Sicherheitsrisiken

Bei Sicherheitsrisiken handelt es sich um Schwachstellen in einer Umgebung, die von einem Angreifer ausgenutzt werden können. Es wird empfohlen, dass Administratoren diese Sicherheitsrisiken beheben, um den Sicherheitsstatus der Organisation zu verbessern.

![Von Identity Protection gemeldete Sicherheitsrisiken](./media/vulnerabilities/identity-protection-vulnerabilities.png)

Die folgenden Abschnitte bieten Ihnen eine Übersicht der Sicherheitsrisiken, die von Identity Protection gemeldet werden.

## <a name="multi-factor-authentication-registration-not-configured"></a>Multi-Factor Authentication-Registrierung nicht konfiguriert

Anhand dieses Sicherheitsrisikos können Sie die Bereitstellung von Azure Multi-Factor Authentication in Ihrer Organisation besser bewerten.

Azure Multi-Factor Authentication dient als zweite Sicherheitsebene für die Benutzerauthentifizierung. Sie hilft beim Schutz des Zugriffs auf Daten und Anwendungen und erfüllt gleichzeitig die Anforderungen von Benutzern an ein einfaches Anmeldeverfahren. Azure Multi-Factor Authentication umfasst folgende benutzerfreundliche Überprüfungsoptionen:

* Telefonanruf
* Textnachricht
* Benachrichtigung über eine mobile App
* OTP-Prüfcode

Es wird empfohlen, dass Sie die Azure Multi-Factor Authentication für Anmeldungen von Benutzern obligatorisch machen. Die mehrstufige Authentifizierung spielt eine wichtige Rolle in Bezug auf risikobasierte Richtlinien für bedingten Zugriff, die unter Identity Protection verfügbar sind.

Weitere Informationen finden Sie unter [Was ist Azure Multi-Factor Authentication?](../authentication/multi-factor-authentication.md).

## <a name="unmanaged-cloud-apps"></a>Nicht verwaltete Cloud-Apps

Mit diesem Sicherheitsrisiko können Sie in Ihrer Organisation nicht verwaltete Cloud-Apps identifizieren.

Häufig kennen IT-Mitarbeiter nicht alle Cloudanwendungen in ihrer Organisation. Daher ist es nicht verwunderlich, dass sich Administratoren Sorgen um den unberechtigten Zugriff auf Unternehmensdaten, mögliche Datenlecks und andere Sicherheitsrisiken machen.

Wir empfehlen, Cloud Discovery bereitzustellen, um nicht verwaltete Cloudanwendungen zu ermitteln und diese Anwendungen mit Azure Active Directory zu verwalten.

Weitere Informationen finden Sie unter [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="security-alerts-from-privileged-identity-management"></a>Sicherheitswarnungen von Privileged Identity Management

Dieses Sicherheitsrisiko hilft Ihnen beim Ermitteln und Bereinigen von Warnungen zu privilegierten Identitäten in Ihrer Organisation.  

Damit Benutzer privilegierte Vorgänge ausführen können, müssen Organisationen ihren Benutzern einen vorübergehenden oder dauerhaften privilegierten Zugriff auf Azure AD, Azure- oder Office 365-Ressourcen oder andere SaaS-Apps gewähren. Mit jedem dieser privilegierten Benutzer vergrößert sich die Angriffsfläche Ihrer Organisation. Dieses Sicherheitsrisiko dient Ihnen als Unterstützung beim Identifizieren von Benutzern mit nicht erforderlichem privilegiertem Zugriff und beim Treffen geeigneter Maßnahmen, um das damit verbundene Risiko zu mindern oder zu beseitigen.

Wir empfehlen Organisationen die Verwendung von Azure AD Privileged Identity Management, um privilegierte Identitäten in Azure AD und anderen Microsoft-Onlinediensten wie Office 365 oder Microsoft Intune zu verwalten, zu steuern und zu überwachen.

Weitere Informationen finden Sie unter [Was ist Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md).

## <a name="see-also"></a>Weitere Informationen

[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

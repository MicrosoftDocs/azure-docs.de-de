---
title: Erzielen von Resilienz durch Verwaltung von Anmeldeinformationen in Azure Active Directory
description: Ein Leitfaden für Architekten und IT-Administratoren zum Erstellen einer robusten Anmeldeinformationenstrategie.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5fd5cf419adb137df5c578e3b17b88749215be5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919060"
---
# <a name="build-resilience-with-credential-management"></a>Erzielen von Resilienz durch Verwaltung von Anmeldeinformationen

Wenn für Azure AD in einer Tokenanforderung Anmeldeinformationen bereitgestellt werden, gibt es mehrere Abhängigkeiten, die für die Überprüfung verfügbar sein müssen. Der erste Authentifizierungsfaktor basiert auf Azure AD-Authentifizierung und in einigen Fällen auf lokaler Infrastruktur. Weitere Informationen zu Hybridauthentifizierungsarchitekturen finden Sie unter [Erzielen von Resilienz in Ihrer Hybridarchitektur](resilience-in-hybrid.md). 

Wenn Sie einen zweiten Faktor implementieren, werden die Abhängigkeiten für den zweiten Faktor den Abhängigkeiten für den ersten Faktor hinzugefügt. Wenn der erste Faktor z. B. über PTA erfolgt und Ihr zweiter Faktor SMS ist, liegen die folgenden Abhängigkeiten vor:

* Azure AD-Authentifizierungsdienste

* Azure MFA-Dienst

* Lokale Infrastruktur

* Mobilfunkanbieter

* Das Gerät des Benutzers (nicht abgebildet)

 
![Abbildung: Authentifizierungsmethoden und Abhängigkeiten](./media/resilience-in-credentials/admin-resilience-credentials.png)

Ihre Strategie für Anmeldeinformationen sollte die Abhängigkeiten der einzelnen Authentifizierungstypen berücksichtigen und Methoden bereitstellen, die einen Single Point of Failure vermeiden. 

Da Authentifizierungsmethoden unterschiedliche Abhängigkeiten aufweisen, empfiehlt es sich, Benutzern die Registrierung für so viele zweistufige Optionen wie möglich zu ermöglichen. Stellen Sie sicher, dass Sie zweite Faktoren mit anderen Abhängigkeiten berücksichtigen, wenn möglich. Beispielsweise weisen Sprachanrufe und SMS als zweite Faktoren dieselben Abhängigkeiten auf, sodass das Risiko durch die Verwendung als einzige Optionen nicht verringert wird.

Die robusteste Strategie für Anmeldeinformationen ist die Verwendung von kennwortloser Authentifizierung. Windows Hello for Business- und FIDO 2.0-Sicherheitsschlüssel weisen weniger Abhängigkeiten als starke Authentifizierung mit zwei separaten Faktoren auf. Die Microsoft Authenticator-App, Windows Hello for Business- und FIDO 2.0-Sicherheitsschlüssel bieten die größte Sicherheit. 

Für zweite Faktoren verfügen die Microsoft Authenticator-App oder andere Authenticator-Apps, die zeitbasierte einmalige Passcodes (Time-based One Time Passcode, TOTP) oder OATH-Hardwaretoken verwenden, über die geringsten Abhängigkeiten und sind daher robuster.

## <a name="how-do-multiple-credentials-help-resilience"></a>Wie unterstützen mehrere Anmeldeinformationen die Resilienz?

Durch die Bereitstellung mehrerer Anmeldeinformationstypen erhalten Benutzer Optionen, die ihre Vorlieben und Umgebungseinschränkungen berücksichtigen. Das führt dazu, dass die interaktive Authentifizierung, bei der Benutzer zur Eingabe von Multi-Factor Authentication aufgefordert werden, robuster ist, wenn bestimmte Abhängigkeiten zum Zeitpunkt der Anforderung nicht verfügbar sind. Sie können [Eingabeaufforderungen für eine erneute Authentifizierung für Multi-Factor Authentication optimieren](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

Zusätzlich zu der oben beschriebenen Benutzerresilienz sollten Unternehmen Notfallmaßnahmen für größere Störungen planen, z. B. für Betriebsfehler, die einen lokalen Verbunddienst mit einer Fehlkonfiguration, einer Naturkatastrophe oder einem unternehmensweiten Ressourcenausfall konfrontieren (insbesondere wenn diese für Multi-Factor Authentication verwendet werden). 

## <a name="how-do-i-implement-resilient-credentials"></a>Wie werden robuste Anmeldeinformationen implementiert?

* Stellen Sie [kennwortlose Anmeldeinformationen](../authentication/howto-authentication-passwordless-deployment.md) bereit, z. B. Windows Hello for Business, Authentifizierung über das Telefon und FIDO2-Sicherheitsschlüssel, um Abhängigkeiten zu verringern.

* Stellen Sie die [Microsoft Authenticator-App](../user-help/user-help-auth-app-overview.md) als zweiten Faktor bereit.

* Aktivieren Sie [Kennworthashsynchronisierung](../hybrid/whatis-phs.md) für Hybridkonten, die über Windows Server Active Directory synchronisiert werden. Diese Option kann neben Verbunddiensten wie AD FS aktiviert werden und bietet eine Fallbackmöglichkeit für den Fall, dass der Verbunddienst ausfällt.

* [Analysieren Sie die Verwendung von Multi-Factor Authentication-Methoden](https://docs.microsoft.com/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/), um die Erfahrung der Benutzer zu verbessern.

* [Implementieren Sie eine robuste Zugriffsseuerungsstrategie](../authentication/concept-resilient-controls.md).

## <a name="next-steps"></a>Nächste Schritte
Resilienzressourcen für Administratoren und Architekten
 
* [Erzielen von Resilienz mithilfe des Gerätestatus](resilience-with-device-states.md)

* [Erzielen von Resilienz durch die Nutzung fortlaufender Zugriffsevaluierung (Continuous Access Evaluation, CAE)](resilience-with-continuous-access-evaluation.md)

* [Erzielen von Resilienz bei der externen Benutzerauthentifizierung](resilience-b2b-authentication.md)

* [Erzielen von Resilienz bei der Hybridauthentifizierung](resilience-in-hybrid.md)

* [Erzielen von Resilienz beim Anwendungszugriff mit dem Anwendungsproxy](resilience-on-premises-access.md)

Resilienzressourcen für Entwickler

* [Erzielen von Resilienz bei der Identitäts- und Zugriffsverwaltung (IAM) für Ihre Anwendungen](resilience-app-development-overview.md)

* [Erzielen von Resilienz für Ihre CIAM-Systeme](resilience-b2c.md)

---
title: Erzielen von Resilienz bei der externen Benutzerauthentifizierung mit Azure Active Directory
description: Ein Leitfaden für IT-Administratoren und -Architekten zum Erstellen einer resilienten Authentifizierung für externe Benutzer
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
ms.openlocfilehash: ae8e24341c321fbfffb84d9b072abc4cf925aff3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919074"
---
# <a name="build-resilience-in-external-user-authentication"></a>Erzielen von Resilienz bei der externen Benutzerauthentifizierung

Die [B2B-Zusammenarbeit in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/external-identities/what-is-b2b) (Azure AD B2B) ist ein Feature von [External Identities](https://docs.microsoft.com/azure/active-directory/external-identities/delegate-invitations), das die Zusammenarbeit mit anderen Organisationen und Personen ermöglicht. Dieses Feature ermöglicht das sichere Onboarding von Gastbenutzern in Ihrem Azure AD-Mandanten, ohne dass Sie die entsprechenden Anmeldeinformationen verwalten müssen. Externe Benutzer bringen ihre Identität und Anmeldeinformationen von einem externen Identitätsanbieter (Identity Provider, IdP) mit, damit sie sich keine neue Anmeldeinformationen merken müssen. 

## <a name="ways-to-authenticate-external-users"></a>Möglichkeiten zur Authentifizierung externer Benutzer

Sie können die Methoden auswählen, mit denen sich externe Benutzer bei Ihrem Verzeichnis authentifizieren. Sie können Microsoft-IdPs oder andere IdPs verwenden.

Mit jedem externen IdP gehen Sie eine Abhängigkeit von der Verfügbarkeit dieses Anbieters ein. Bei einigen Methoden der Verbindungsherstellung mit einem IdP können Sie verschiedene Faktoren beeinflussen, um die Resilienz zu erhöhen.

> [!NOTE] 
> Azure AD B2B besitzt die integrierte Möglichkeit, jeden Benutzer aus jedem [Azure Active Directory](https://docs.microsoft.com/azure/active-directory)-Mandanten oder mit einem persönlichen [Microsoft-Konto](https://account.microsoft.com/account) zu authentifizieren. Bei diesen integrierten Optionen müssen Sie keinerlei Einstellungen konfigurieren.

### <a name="considerations-for-resilience-with-other-idps"></a>Überlegungen in Bezug auf die Resilienz bei anderen IdPs

Wenn Sie externe IdPs für die Authentifizierung von Gastbenutzern verwenden, müssen Sie zur Vermeidung von Unterbrechungen bestimmte Einstellungen konfigurieren.

| Authentifizierungsmethode| Überlegungen zur Resilienz |
| - | - |
| Verbund mit IdPs sozialer Medien wie z. B. [Facebook](https://docs.microsoft.com/azure/active-directory/external-identities/facebook-federation) oder [Google](https://docs.microsoft.com/azure/active-directory/external-identities/google-federation).| Sie müssen Ihr Konto beim IdP verwalten und die Client-ID und das Clientgeheimnis konfigurieren. |
| [Direkter Verbund mit AD FS und Drittanbietern für Gastbenutzer](https://docs.microsoft.com/azure/active-directory/external-identities/direct-federation)| Sie müssen mit dem IdP-Besitzer zusammenarbeiten, um den Zugriff auf die Endpunkte einzurichten, die Sie benötigen. <br>Sie müssen die Metadaten verwalten, die die Zertifikate und Endpunkte enthalten. |
| [Authentifizierung mit Einmalkennung per E-Mail](https://docs.microsoft.com/azure/active-directory/external-identities/one-time-passcode)| Bei dieser Methode sind Sie vom E-Mail-System von Microsoft, dem E-Mail-System des Benutzers und dem E-Mail-Client des Benutzers abhängig. |


 

## <a name="self-service-sign-up-preview"></a>Self-Service-Registrierung (Vorschauversion)

Als Alternative zum Senden von Einladungen oder Links können Sie die [Self-Service-Registrierung](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-overview) aktivieren.  Damit können externe Benutzer Zugriff auf eine Anwendung anfordern. Sie müssen einen [API-Connector](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector) erstellen und einem Benutzerflow zuordnen. Sie ordnen Benutzerflows zu, die das Benutzererlebnis mit einer oder mehreren Anwendungen definieren. 

Sie können [API-Connectors](https://docs.microsoft.com/azure/active-directory/external-identities/api-connectors-overview) auch verwenden, um Ihren Benutzerflow für die Self-Service-Registrierung in die APIs externer Systeme zu integrieren. Diese API-Integration kann für [benutzerdefinierte Genehmigungsworkflows](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-approvals), [Identitätsüberprüfung](https://docs.microsoft.com/azure/active-directory/external-identities/code-samples-self-service-sign-up) und andere Aufgaben wie das Überschreiben von Benutzerattributen verwendet werden. Wenn Sie APIs verwenden, müssen Sie die folgenden Abhängigkeiten verwalten.

* **Authentifizierung des API-Connectors**: Zum Einrichten eines Connectors ist eine Endpunkt-URL, ein Benutzername und ein Kennwort erforderlich. Richten Sie einen Prozess ein, über den diese Anmeldeinformationen verwaltet werden, und arbeiten Sie mit dem API-Besitzer zusammen, um sicherzustellen, dass Sie über Ablaufzeitpläne informiert werden.

* **Antwort des API-Connectors**: Entwerfen Sie die API-Connectors im Anmeldeflow so, dass eine ordnungsgemäße Beendigung möglich ist, wenn die API nicht verfügbar ist. Untersuchen Sie diese [API-Beispielantworten](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector) und [Best Practices für die Problembehandlung](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector), und stellen Sie Ihren API-Entwicklern die entsprechenden Informationen bereit. Arbeiten Sie mit dem API-Entwicklungsteam zusammen, um alle möglichen Antwortszenarien zu testen, einschließlich Fortsetzung, Überprüfungsfehler und blockierenden Antworten. 

## <a name="next-steps"></a>Nächste Schritte
Resilienzressourcen für Administratoren und Architekten
 
* [Erzielen von Resilienz durch die Verwaltung von Anmeldeinformationen](resilience-in-credentials.md)

* [Erzielen von Resilienz mithilfe des Gerätestatus](resilience-with-device-states.md)

* [Erzielen von Resilienz durch die Nutzung fortlaufender Zugriffsevaluierung (Continuous Access Evaluation, CAE)](resilience-with-continuous-access-evaluation.md)

* [Erzielen von Resilienz bei der Hybridauthentifizierung](resilience-in-hybrid.md)

* [Erzielen von Resilienz beim Anwendungszugriff mit dem Anwendungsproxy](resilience-on-premises-access.md)

Resilienzressourcen für Entwickler

* [Erzielen von Resilienz bei der Identitäts- und Zugriffsverwaltung (IAM) für Ihre Anwendungen](resilience-app-development-overview.md)

* [Erzielen von Resilienz für Ihre CIAM-Systeme](resilience-b2c.md)
 

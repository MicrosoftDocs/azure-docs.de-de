---
title: Erstellen einer robusteren Hybridauthentifizierung in Azure Active Directory
description: Ein Leitfaden für Architekten und IT-Administratoren zum Erstellen einer robusten Hybridinfrastruktur.
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
ms.openlocfilehash: 5c45b362bc37df71346fc3b635c8ae4a51f62cdc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919049"
---
# <a name="build-resilience-in-your-hybrid-architecture"></a>Erzielen von Resilienz in Ihrer Hybridarchitektur

Hybridauthentifizierung ermöglicht Benutzern den Zugriff auf cloudbasierte Ressourcen mit ihren Identitäten, die lokal verwaltet werden. Eine Hybridinfrastruktur umfasst sowohl Cloud- als auch lokale Komponenten.

* Zu den Cloudkomponenten zählen Azure AD, Azure-Ressourcen und-Dienste, cloudbasierte Apps Ihrer Organisation und SaaS-Anwendungen.

* Lokale Komponenten umfassen lokale Anwendungen, Ressourcen wie SQL-Datenbanken sowie einen Identitätsanbieter wie Windows Server Active Directory. 

> [!IMPORTANT]
> Wenn Sie die Resilienz in Ihrer Hybridinfrastruktur planen, ist es entscheidend, Abhängigkeiten und Single Points of Failure zu minimieren. 

Microsoft bietet drei Mechanismen für Hybridauthentifizierung an. Die Optionen werden in der Reihenfolge der Resilienz aufgeführt. Es wird empfohlen, nach Möglichkeit Kennworthashsynchronisierung zu implementieren.

* [Kennworthashsynchronisierung](../hybrid/whatis-phs.md) (Password Hash Synchronization, PHS) verwendet Azure AD Connect, um die Identität und einen Hash des Kennworts mit Azure AD zu synchronisieren, sodass sich Benutzer bei cloudbasierten Ressourcen mit ihrem lokal verwalteten Kennwort anmelden können. PHS weist lokale Abhängigkeiten nur für die Synchronisierung auf, nicht für die Authentifizierung.

* [Passthrough-Authentifizierung](../hybrid/how-to-connect-pta.md) (PTA) leitet Benutzer für die Anmeldung an Azure AD um. Der Benutzername und das Kennwort werden dann anhand eines Agents, der im Unternehmensnetzwerk bereitgestellt wird, anhand von Active Directory lokal überprüft. PTA verfügt über lokalen Speicherbedarf für die Azure AD PTA-Agents, die sich lokal auf Servern befinden.

* [Verbundkunden](../hybrid/whatis-fed.md) stellen einen Verbunddienst wie AD FS bereit, und Azure AD überprüft dann die vom Verbunddienst generierte SAML-Assertion. Der Verbund weist die größte Abhängigkeit von der lokalen Infrastruktur und somit mehr Fehlerpunkte auf. 

   
‎Möglicherweise verwenden Sie mindestens eine dieser Methoden in Ihrer Organisation. Weitere Informationen finden Sie unter [Wählen der richtigen Authentifizierungsmethode für Ihre Azure AD-Hybrididentitätslösung](../hybrid/choose-ad-authn.md). Dieser Artikel enthält eine Entscheidungsstruktur, die Ihnen bei der Entscheidung bezüglich der Methodik helfen kann.

## <a name="password-hash-synchronization"></a>Kennworthashsynchronisierung

Die einfachste und robusteste Hybridauthentifizierungsoption für Azure AD ist [Kennworthashsynchronisierung](../hybrid/whatis-phs.md), die beim Verarbeiten von Authentifizierungsanforderungen keine lokale Identitätsinfrastrukturabhängigkeit aufweist. Nachdem Identitäten mit Kennworthashes mit Azure AD synchronisiert wurden, können sich Benutzer für Cloudressourcen ohne Abhängigkeit von den lokalen Identitätskomponenten authentifizieren. 

![Architekturdiagramm von PHS](./media/resilience-in-hybrid/admin-resilience-password-hash-sync.png)

Wenn Sie diese Authentifizierungsoption auswählen, treten keine Unterbrechungen auf, wenn lokale Identitätskomponenten nicht mehr verfügbar sind. Lokale Unterbrechungen können aus vielen Gründen auftreten, darunter Hardwarefehler, Stromausfälle, Naturkatastrophen und Angriffe durch Schadsoftware. 

### <a name="how-do-i-implement-phs"></a>Wie implementiere ich PHS?

Weitere Informationen zum Implementieren von PHS finden Sie in den folgenden Ressourcen:

* [Implementieren der Kennworthashsynchronisierung mit Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md)

* [Aktivieren der Kennworthashsynchronisierung](../hybrid/how-to-connect-password-hash-synchronization.md)

Wenn Ihre Anforderungen verhindern, dass Sie PHS verwenden können, verwenden Sie Passthrough-Authentifizierung.

## <a name="pass-through-authentication"></a>Passthrough-Authentifizierung

Passthrough-Authentifizierung weist eine Abhängigkeit von Authentifizierungs-Agents auf, die sich lokal auf Servern befinden. Eine permanente Verbindung oder ein Service Bus ist zwischen Azure AD und den lokalen PTA-Agents vorhanden. Die Firewall, die Server, auf denen die Authentifizierungs-Agents gehostet werden, und das lokale Windows Server Active Directory (oder ein anderer Identitätsanbieter) sind alles potenzielle Fehlerpunkte. 

![Architekturdiagramm von PTA](./media/resilience-in-hybrid/admin-resilience-pass-through-authentication.png)

### <a name="how-do-i-implement-pta"></a>Wie implementiere ich PTA?

Informationen zum Implementieren von Passthrough-Authentifizierung finden Sie in den folgenden Ressourcen.

* [Funktionsweise von Passthrough-Authentifizierung](../hybrid/how-to-connect-pta-how-it-works.md)

* [Azure Active Directory-Passthrough-Authentifizierung – ausführliche Informationen zur Sicherheit](../hybrid/how-to-connect-pta-security-deep-dive.md)

* [Installieren von Azure AD-Passthrough-Authentifizierung](../hybrid/how-to-connect-pta-quick-start.md)

* Wenn Sie PTA verwenden, definieren Sie eine [hochverfügbare Topologie](../hybrid/how-to-connect-pta-quick-start.md).

 ## <a name="federation"></a>Verbund

Der Verbund umfasst das Erstellen einer Vertrauensstellung zwischen Azure AD und dem Verbunddienst, die den Austausch von Endpunkten, Tokensignaturzertifikaten und anderen Metadaten umfasst. Wenn eine Anforderung an Azure AD gesendet wird, wird die Konfiguration gelesen und der Benutzer an die konfigurierten Endpunkte umgeleitet. Zu diesem Zeitpunkt interagiert der Benutzer mit dem Verbunddienst, der eine durch Azure AD validierte SAML-Assertion ausstellt. 

Das folgende Diagramm zeigt die Topologie einer Unternehmensbereitstellung der Active Directory-Verbunddienste (AD FS), die redundante Verbund- und Webanwendungs-Proxyserver über mehrere lokale Rechenzentren hinweg umfasst. Diese Konfiguration basiert auf Infrastrukturkomponenten für Unternehmensnetzwerke wie DNS, Netzwerklastenausgleich mit Geoaffinitätsfunktionen, Firewalls usw. Alle lokalen Komponenten und Verbindungen sind anfällig für Ausfälle. Weitere Informationen finden Sie in der [Dokumentation zur AD FS-Kapazitätsplanung](https://docs.microsoft.com/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity).

> [!NOTE]
>  Der Verbund verfügt über die höchste Anzahl von lokalen Abhängigkeiten und somit über die meisten potenziellen Fehlerquellen. Dieses Diagramm zeigt AD FS. Andere lokale Identitätsanbieter unterliegen aber ähnlichen Entwurfsüberlegungen, um Hochverfügbarkeit, Skalierbarkeit und Failover zu erzielen.

![Architekturdiagramm des Verbunds](./media/resilience-in-hybrid/admin-resilience-federation.png)

 ### <a name="how-do-i-implement-federation"></a>Wie implementiere ich den Verbund?

Wenn Sie eine Verbundauthentifizierungsstrategie implementieren oder diese robuster gestalten möchten, finden Sie weitere Informationen in den folgenden Ressourcen.

* [Was ist Verbundauthentifizierung?](../hybrid/whatis-fed.md)

* [Funktionsweise eines Verbunds](../hybrid/how-to-connect-fed-whatis.md)

* [Azure AD-Verbund – Kompatibilitätsliste](../hybrid/how-to-connect-fed-compatibility.md)

* Befolgen der [Dokumentation zur AD FS-Kapazitätsplanung](https://docs.microsoft.com/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity)

* [Bereitstellen von AD FS in Azure IaaS](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)

* [Aktivieren von PHS](../hybrid/tutorial-phs-backup.md) zusammen mit dem Verbund

## <a name="next-steps"></a>Nächste Schritte
Resilienzressourcen für Administratoren und Architekten
 
* [Erzielen von Resilienz durch die Verwaltung von Anmeldeinformationen](resilience-in-credentials.md)

* [Erzielen von Resilienz mithilfe des Gerätestatus](resilience-with-device-states.md)

* [Erzielen von Resilienz durch die Nutzung fortlaufender Zugriffsevaluierung (Continuous Access Evaluation, CAE)](resilience-with-continuous-access-evaluation.md)

* [Erzielen von Resilienz bei der externen Benutzerauthentifizierung](resilience-b2b-authentication.md)

* [Erzielen von Resilienz beim Anwendungszugriff mit dem Anwendungsproxy](resilience-on-premises-access.md)

Resilienzressourcen für Entwickler

* [Erzielen von Resilienz bei der Identitäts- und Zugriffsverwaltung (IAM) für Ihre Anwendungen](resilience-app-development-overview.md)

* [Erzielen von Resilienz für Ihre CIAM-Systeme](resilience-b2c.md)

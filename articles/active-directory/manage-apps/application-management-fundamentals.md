---
title: 'Anwendungsverwaltung: Bewährte Methoden und Empfehlungen | Microsoft-Dokumentation'
description: Hier finden Sie bewährte Methoden und Empfehlungen für das Verwalten von Anwendungen in Azure Active Directory. Erfahren Sie mehr über die automatische Bereitstellung und Veröffentlichung von lokalen Apps mit dem Anwendungsproxy.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f5aaf4bbff4c093872228d647d3850667fb9777
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656406"
---
# <a name="application-management-best-practices"></a>Bewährte Methoden für die Anwendungsverwaltung

Dieser Artikel enthält Empfehlungen und bewährte Methoden für das Verwalten von Anwendungen in Azure Active Directory (Azure AD), die Verwendung der automatischen Bereitstellung und das Veröffentlichen von lokalen Apps mit dem Anwendungsproxy.

## <a name="cloud-app-and-single-sign-on-recommendations"></a>Empfehlungen für Cloud-Apps und einmaliges Anmelden
| Empfehlung | Kommentare |
| --- | --- |
| Durchsuchen des Azure AD-Anwendungskatalogs nach Apps  | Azure AD enthält einen Katalog mit Tausenden von vorab integrierten Anwendungen mit Enterprise Single Sign-On (Einmaliges Anmelden für Unternehmen (SSO)). App-spezifische Einrichtungsanleitungen finden Sie in der [Liste der Tutorials für SaaS-Anwendungen](../saas-apps/tutorial-list.md).  | 
| Verwenden von SAML-basiertem Verbund-SSO  | Verwenden Sie SAML-basiertes Verbund-SSO mit Azure AD anstelle von kennwortbasiertem SSO und ADFS, wenn eine Anwendung dies unterstützt.  | 
| Verwenden von SHA-256 für die Zertifikatsignatur  | Azure AD verwendet standardmäßig den Algorithmus SHA-256 zum Signieren der SAML-Antwort. Verwenden Sie SHA-256, sofern die Anwendung nicht SHA-1 erfordert. (Weitere Informationen finden Sie unter [Optionen für die Zertifikatsignatur](certificate-signing-options.md) und [Problem bei der Anwendungsanmeldung](application-sign-in-problem-application-error.md).)  | 
| Erfordern der Benutzerzuweisung  | Standardmäßig können Benutzer auf Ihre Unternehmensanwendungen zugreifen, ohne diesen zugewiesen zu sein. Falls die Anwendung jedoch Rollen verfügbar macht oder Sie möchten, dass die Anwendung unter „Meine Apps“ für einen Benutzer angezeigt wird, müssen Sie Benutzerzuweisungen erforderlich machen. (Weitere Informationen finden Sie im [Entwicklerleitfaden zum Integrieren von Anwendungen](developer-guidance-for-integrating-applications.md).)  | 
| Bereitstellen von „Meine Apps“ für Ihre Benutzer | [Meine Apps](end-user-experiences.md) unter `https://myapps.microsoft.com` ist ein webbasiertes Portal, das Benutzern einen einzigen Einstiegspunkt für die ihnen zugewiesenen cloudbasierten Anwendungen bietet. Wenn zusätzliche Funktionen wie Gruppenverwaltung und Self-Service-Kennwortzurücksetzung hinzugefügt werden, können Benutzer diese in „Meine Apps“ finden. Informationen finden Sie unter [Planen einer Bereitstellung von „Meine Apps“](access-panel-deployment-plan.md).
| Verwenden der Gruppenzuweisung  | Wenn die Option in Ihrem Abonnement enthalten ist, weisen Sie einer Anwendung Gruppen zu, damit Sie die laufende Zugriffsverwaltung an den Gruppenbesitzer delegieren können. (Weitere Informationen finden Sie im [Entwicklerleitfaden zum Integrieren von Anwendungen](developer-guidance-for-integrating-applications.md).)   | 
| Einrichten eines Prozesses für das Verwalten von Zertifikaten | Die maximale Lebensdauer eines Signaturzertifikats beträgt drei Jahre. Um Ausfälle aufgrund eines ablaufenden Zertifikats zu verhindern oder zu minimieren, verwenden Sie Rollen und E-Mail-Verteilerlisten, damit sichergestellt werden kann, dass zertifikatbezogene Änderungsbenachrichtigungen genau überwacht werden. |

## <a name="provisioning-recommendations"></a>Bereitstellungsempfehlungen
| Empfehlung | Kommentare |
| --- | --- |
| Verwenden von Tutorials zum Einrichten der Bereitstellung mit Cloud-Apps | In der [Liste der Tutorials für SaaS-Anwendungen](../saas-apps/tutorial-list.md) finden Sie eine Schritt-für-Schritt-Anleitung zum Konfigurieren der Bereitstellung für die Katalog-App, die Sie hinzufügen möchten. |
| Verwenden von Bereitstellungsprotokollen (Vorschau) zum Überwachen des Status | Die [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) bieten Details zu allen vom Bereitstellungsdienst ausgeführten Aktionen, einschließlich des Status der Bereitstellung für einzelne Benutzer. |
| Zuweisen einer Verteilergruppe zur Bereitstellungsbenachrichtigungs-E-Mail | Weisen Sie der Einstellung für Benachrichtigungs-E-Mails eine Verteilergruppe zu, um die Sichtbarkeit der vom Bereitstellungsdienst gesendeten kritischen Warnungen zu erhöhen. |


## <a name="application-proxy-recommendations"></a>Empfehlungen für den Anwendungsproxy
| Empfehlung | Kommentare |
| --- | --- |
| Verwenden des Anwendungsproxys für den Remotezugriff auf interne Ressourcen | Die Verwendung des Anwendungsproxys wird empfohlen, um Remotebenutzern Zugriff auf interne Ressourcen zu gewähren, wodurch die Notwendigkeit eines virtuellen privaten Netzwerks oder Reverseproxys entfällt. Er ist nicht für den Zugriff auf Ressourcen innerhalb des Unternehmensnetzwerks vorgesehen, weil dadurch Latenz auftreten könnte.
| Verwenden benutzerdefinierter Domänen | Richten Sie benutzerdefinierte Domänen für Ihre Anwendungen ein (siehe [Konfigurieren von benutzerdefinierten Domänen](application-proxy-configure-custom-domain.md)), damit URLs für Benutzer und zwischen Anwendungen innerhalb oder außerhalb Ihres Netzwerks funktionieren. Sie können auch Ihr Branding steuern und Ihre URLs anpassen.  Wenn Sie benutzerdefinierte Domänennamen verwenden, müssen Sie ein öffentliches Zertifikat von einer anderen vertrauenswürdigen Zertifizierungsstelle als Microsoft erhalten. Der Azure-Anwendungsproxy unterstützt Standard-, [Platzhalter](application-proxy-wildcard.md)- oder SAN-basierte Zertifikate. (Weitere Informationen finden Sie unter [Planen der Bereitstellung eines Anwendungsproxys](application-proxy-deployment-plan.md).) |
| Synchronisieren von Benutzern vor dem Bereitstellen des Anwendungsproxys | Synchronisieren Sie vor dem Bereitstellen des Anwendungsproxys die Benutzeridentitäten aus einem lokalen Verzeichnis, oder erstellen Sie die Benutzeridentitäten direkt in Azure AD. Die Identitätssynchronisierung ermöglicht Azure AD die Vorabauthentifizierung von Benutzern, bevor ihnen Zugriff auf per Anwendungsproxy veröffentlichte Anwendungen gewährt wird. Außerdem werden die erforderlichen Benutzer-ID-Informationen für einmaliges Anmelden (Single Sign-On, SSO) bereitgestellt. (Weitere Informationen finden Sie unter [Planen der Bereitstellung eines Anwendungsproxys](application-proxy-deployment-plan.md).) |
| Befolgen unserer Tipps für Hochverfügbarkeit und Lastenausgleich | Informationen dazu, wie der Datenverkehr zwischen Benutzern, Anwendungsproxyconnectors und Back-End-App-Servern fließt, sowie Tipps zum Optimieren der Leistung und des Lastenausgleichs finden Sie unter [Hochverfügbarkeit und Lastenausgleich von Anwendungsproxy-Connectors und -Anwendungen](application-proxy-high-availability-load-balancing.md). |
| Verwenden mehrerer Connectors | Verwenden Sie mindestens zwei Anwendungsproxyconnectors, um eine bessere Resilienz, Verfügbarkeit und Skalierung zu ermöglichen (siehe [Anwendungsproxyconnectors](application-proxy-connectors.md)). Erstellen Sie Connectorgruppen, und stellen Sie sicher, dass jede Connectorgruppe über mindestens zwei Connectors verfügt (drei Connectors sind optimal). |
| Platzieren der Connectorserver in der Nähe der Anwendungsserver und Sicherstellen, dass sich die Server in derselben Domäne befinden | Platzieren Sie den Connectorserver physisch in der Nähe der Anwendungsserver, um die Leistung zu optimieren (siehe [Aspekte der Netzwerktopologie](application-proxy-network-topology.md)). Außerdem müssen der Connectorserver und die Webanwendungsserver derselben Active Directory-Domäne angehören oder vertrauenswürdige Domänen umfassen. Diese Konfiguration ist für einmaliges Anmelden mit integrierter Windows-Authentifizierung (IWA) und eingeschränkter Kerberos-Delegierung (Kerberos Constrained Delegation, KCD) erforderlich. Wenn sich die Server in unterschiedlichen Domänen befinden, müssen Sie die ressourcenbasierte Delegierung für einmaliges Anmelden verwenden (siehe [Eingeschränkte Kerberos-Delegierung für die einmalige Anmeldung mit dem Anwendungsproxy](application-proxy-configure-single-sign-on-with-kcd.md)). |
| Aktivieren automatischer Updates für Connectors | Aktivieren Sie automatische Updates für Ihre Connectors, um die neuesten Features und Fehlerbehebungen zu erhalten. Microsoft bietet direkte Unterstützung für die neueste Connectorversion und die unmittelbare Vorgängerversion. (Weitere Informationen finden Sie unter [Versionsverlauf des Anwendungsproxys](application-proxy-release-version-history.md).) |
| Umgehen Ihres lokalen Proxys | Um die Wartung zu vereinfachen, konfigurieren Sie den Connector so, dass Ihr lokaler Proxy umgangen wird, damit eine direkte Verbindung mit den Azure-Diensten hergestellt wird. (Weitere Informationen finden Sie unter [Anwendungsproxyconnectors und Proxyserver](application-proxy-configure-connectors-with-proxy-servers.md).) |
| Bevorzugen des Azure AD-Anwendungsproxys gegenüber dem Webanwendungsproxy | Verwenden Sie in den meisten lokalen Szenarien den Azure AD-Anwendungsproxy. Der Webanwendungsproxy wird nur in Szenarien bevorzugt, in denen ein Proxyserver für AD FS erforderlich ist und Sie in Azure Active Directory keine benutzerdefinierten Domänen verwenden können. (Weitere Informationen finden Sie unter [Vergleichen von Remotezugriffslösungen](application-proxy-migration.md).) |
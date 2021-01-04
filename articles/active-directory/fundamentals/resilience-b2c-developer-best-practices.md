---
title: Resilienz durch Best Practices für Entwickler mit Azure AD B2C | Microsoft-Dokumentation
description: Resilienz durch Best Practices für Entwickler in Bezug auf Kundenidentitäts- und Zugriffsverwaltung mit Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecde474abf3c814b7c3afa4ae18d044868785cf5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919073"
---
# <a name="resilience-through-developer-best-practices"></a>Resilienz durch Best Practices für Entwickler

In diesem Artikel möchten wir einige Erkenntnisse aus unserer langjährigen Erfahrung mit der Arbeit mit Großkunden an Sie weitergeben. Sie können beim Entwurf und bei der Implementierung Ihrer Dienste von diesen Empfehlungen profitieren.

![Abbildung: Komponenten der Entwicklerumgebung](media/resilience-b2c-developer-best-practices/developer-best-practices-architecture.png)

## <a name="use-the-microsoft-authentication-library-msal"></a>Verwenden der Microsoft Authentication Library (MSAL)

Die [Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) und die [Microsoft Identity-Webauthentifizierungsbibliothek für ASP.NET](https://docs.microsoft.com/azure/active-directory/develop/reference-v2-libraries) vereinfachen das Abrufen, Verwalten, Zwischenspeichern und Aktualisieren der Token, die für eine Anwendung erforderlich sind. Diese Bibliotheken sind speziell für die Unterstützung von Microsoft Identity optimiert und bieten Features zur Verbesserung der Anwendungsresilienz.

Entwickler sollten die neuesten Versionen der MSAL verwenden und diese stets aktualisieren. Erfahren Sie, wie Sie die [Resilienz bei Authentifizierung und Autorisierung](resilience-app-development-overview.md) in Ihren Anwendungen erhöhen können. Vermeiden Sie nach Möglichkeit die Implementierung eines eigenen Authentifizierungsstapels, und verwenden Sie stattdessen eingeführte und bewährte Bibliotheken.

## <a name="optimize-directory-reads-and-writes"></a>Optimieren von Lese- und Schreibvorgängen im Verzeichnis

Der Microsoft Azure AD B2C-Verzeichnisdienst unterstützt Milliarden von Authentifizierungen pro Tag. Er ist auf eine hohe Anzahl von Lesevorgängen pro Sekunde ausgelegt. Optimieren Sie Schreibvorgänge, um Abhängigkeiten zu minimieren und die Resilienz zu erhöhen.

### <a name="how-to-optimize-directory-reads-and-writes"></a>So optimieren Sie Lese- und Schreibvorgänge im Verzeichnis

- **Vermeiden Sie das Schreiben von Funktionen in das Verzeichnis während der Anmeldung**: Führen Sie in Ihren benutzerdefinierten Richtlinien einen Schreibvorgang bei der Anmeldung niemals ohne Vorbedingung (if-Klausel) aus. Ein Anwendungsfall, der während der Anmeldung einen Schreibvorgang erfordert, ist die [Just-In-Time-Migration von Benutzerkennwörtern](https://github.com/azure-ad-b2c/user-migration/tree/master/seamless-account-migration). Vermeiden Sie alle Szenarien, die bei jeder Anmeldung einen Schreibvorgang erfordern.

  - [Vorbedingungen](https://docs.microsoft.com/azure/active-directory-b2c/userjourneys) in einer User Journey sehen wie folgt aus:

  ``
  <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 
  <Value>requiresMigration</Value>
  ...
  < Precondition/>
  ``
  - Erhöhen Sie die Widerstandsfähigkeit gegen botgesteuerte Anmeldungen [durch Integration eines CAPTCHA-Systems](https://github.com/azure-ad-b2c/samples/tree/master/policies/captcha-integration).

  - Nutzen Sie ein [Beispiel für Auslastungstests](https://docs.microsoft.com/azure/active-directory-b2c/best-practices#testing), um Registrierungen und Anmeldungen zu simulieren. 

- **Grundlegendes zur Drosselung**: Das Verzeichnis implementiert Drosselungsregeln sowohl auf Anwendungs- als auch auf Mandantenebene. Es gibt weitere Ratenbegrenzungen für die Vorgänge zum Lesen/GET, Schreiben/POST, Aktualisieren/PUT und Löschen/DELETE. Für jeden Vorgang gilt ein anderes Limit.

  - Ein Schreibvorgang zum Zeitpunkt der Anmeldung gehört zu einem POST-Vorgang für neue Benutzer oder einem PUT-Vorgang für vorhandene Benutzer.

  - Eine benutzerdefinierte Richtlinie, die bei jeder Anmeldung einen Benutzer erstellt oder aktualisiert, kann möglicherweise eine PUT- oder POST-Ratenbegrenzung auf Anwendungsebene erreichen. Die gleichen Grenzwerte gelten beim Aktualisieren von Verzeichnisobjekten über Azure AD oder Microsoft Graph. Untersuchen Sie auch die Lesevorgänge, um die Anzahl dieser Vorgänge bei jeder Anmeldung auf ein Minimum zu beschränken.

  - Schätzen Sie die Spitzenlast, um die Rate der Verzeichnisschreibvorgänge vorherzusagen und eine Drosselung zu vermeiden. Bei der Schätzung von Datenverkehrsspitzen müssen auch Schätzwerte für Aktionen wie Registrierung, Anmeldung und Multi-Factor Authentication (MFA) berücksichtigt werden. Testen Sie unbedingt sowohl das Azure AD B2C-System als auch Ihre Anwendung auf Datenverkehrsspitzen. Es ist möglich, dass Azure AD B2C die Last ohne Drosselung verarbeiten kann, Ihre nachgelagerten Anwendungen oder Dienste aber nicht.

  - Erarbeiten Sie einen Zeitplan für die Migration. Wenn Sie planen, Benutzer mithilfe von Microsoft Graph zu Azure AD B2C zu migrieren, berücksichtigen Sie die Grenzwerte für Anwendung und Mandant, um die Zeit zu berechnen, die für die vollständige Migration der Benutzer benötigt wird. Wenn Sie den Auftrag oder das Skript zum Erstellen von Benutzern auf zwei Anwendungen aufteilen, können einen anwendungsbasierten Grenzwert verwenden. Dieser Grenzwert muss jedoch unter dem Schwellenwert pro Mandant liegen.

  - Informieren Sie sich gründlich über die Auswirkungen des Migrationsauftrags auf andere Anwendungen. Berücksichtigen Sie den Livedatenverkehr aus anderen abhängigen Anwendungen, um sicherzustellen, dass Sie keine Drosselung auf Mandantenebene und keine Ressourcenknappheit für Ihre Liveanwendung verursachen. Weitere Informationen finden Sie im [Leitfaden zu Einschränkungen in Microsoft Graph](https://docs.microsoft.com/graph/throttling).
  
## <a name="extend-token-lifetimes"></a>Verlängern der Tokenlebensdauer

Für unwahrscheinliche Szenarien – wenn beispielsweise der Azure AD B2C-Authentifizierungsdienst keine neuen Registrierungen und Anmeldungen durchführen kann – können Sie dennoch eine Problementschärfung für bereits angemeldete Benutzer bereitstellen. Per [Konfiguration](https://docs.microsoft.com/azure/active-directory-b2c/configure-tokens) können Sie es bereits angemeldeten Benutzern ermöglichen, die Anwendung ohne spürbare Unterbrechung weiter zu nutzen, bis sie sich von der Anwendung abmelden oder für die [Sitzung](https://docs.microsoft.com/azure/active-directory-b2c/session-behavior) aufgrund von Inaktivität ein Timeout auftritt.

Geschäftsanforderungen und das gewünschte Endbenutzererlebnis bestimmen die Häufigkeit der Tokenaktualisierung für Webanwendungen und Single-Page-Anwendungen (SPAs).

### <a name="how-to-extend-token-lifetimes"></a>So verlängern Sie die Lebensdauer von Token

- **Webanwendungen**: Bei Webanwendungen, bei denen das Authentifizierungstoken zu Beginn der Anmeldung überprüft wird, benötigt die Anwendung ein Sitzungscookie, um die Gültigkeit der Sitzung zu verlängern.

  - Implementieren Sie rollierende Sitzungszeiten, die eine Sitzungsverlängerung basierend auf der Benutzeraktivität ermöglichen, damit Benutzer angemeldet bleiben können. Wenn für längere Zeit keine Token ausgestellt werden können, lassen sich diese Sitzungszeiten in Form einer einmaligen Konfiguration der Anwendung weiter verlängern. Legen Sie die Sitzungslebensdauer auf den maximal zulässigen Wert fest.

- **SPAs**: Eine SPA benötigt möglicherweise Zugriffstoken, um APIs aufzurufen. Eine SPA verwendet traditionell den impliziten Flow, der nicht zu einem Aktualisierungstoken führt. Die SPA kann einen verborgenen iFrame verwenden, um neue Tokenanforderungen beim Autorisierungsendpunkt auszuführen, wenn im Browser noch eine aktive Sitzung mit dem Azure AD B2C-Dienst vorhanden ist. Bei SPAs stehen einige Optionen zur Verfügung, um dem Benutzer die weitere Nutzung der Anwendung zu ermöglichen.

  - Verlängern Sie die Gültigkeitsdauer des Zugriffstokens, um Ihre Geschäftsanforderungen zu erfüllen.

  - Konfigurieren Sie Ihre Anwendung so, dass sie ein API-Gateway als Authentifizierungsproxy verwendet. In dieser Konfiguration wird die SPA ohne Authentifizierung geladen, und die API-Aufrufe erfolgen an das API-Gateway. Das API-Gateway leitet den Benutzer basierend auf einer Richtlinie per [Zuweisung eines Autorisierungscodes](https://oauth.net/2/grant-types/authorization-code/) durch einen Anmeldevorgang und authentifiziert den Benutzer. Anschließend wird die Authentifizierungssitzung zwischen API-Gateway und Client über ein Authentifizierungscookie beibehalten. Die APIs werden über das API-Gateway unter Verwendung des Tokens bedient, das vom API-Gateway oder anderen direkten Authentifizierungsmethoden abgerufen wird, wie etwa Zertifikaten, Clientanmeldeinformationen oder API-Schlüsseln.

  - [Migrieren Sie Ihre SPA von implizite Genehmigungen](https://developer.microsoft.com/identity/blogs/msal-js-2-0-supports-authorization-code-flow-is-now-generally-available/) zu einem [Genehmigungsflow per Autorisierungscode](https://docs.microsoft.com/azure/active-directory-b2c/implicit-flow-single-page-application) mit Unterstützung für PKCE (Proof Key for Code Exchange) und CORS (Cross-Origin Resource Sharing). Migrieren Sie Ihre Anwendung von MSAL.js 1.x zu MSAL.js 2.x, um Resilienz für Ihre Webanwendungen zu erzielen.

  - Für mobile Anwendungen empfiehlt es sich, die Lebensdauer sowohl des Aktualisierungs- als auch des Zugriffstokens zu verlängern.

- **Back-End- oder Microserviceanwendungen**: Da Back-End-Anwendungen (Daemon-Anwendungen) nicht interaktiv sind und nicht in einem Benutzerkontext ausgeführt werden, ist die Wahrscheinlichkeit eines Tokendiebstahls sehr gering. Es wird empfohlen, einen Mittelweg zwischen Sicherheit und Lebensdauer zu finden und eine lange Tokenlebensdauer festzulegen.

## <a name="configure-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens

Mit dem Feature [Einmaliges Anmelden](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Single Sign-On, SSO) melden sich Benutzer einmal mit einem einzigen Konto an und erhalten Zugriff auf mehrere Anwendungen. Dabei kann es sich um eine mobile Anwendung, eine Webanwendung oder eine Single-Page-Anwendung handeln. Plattform und Domänenname spielen keine Rolle. Wenn ein Benutzer sich erstmals bei einer Anwendung anmeldet, speichert Azure AD B2C eine [cookiebasierte Sitzung](https://docs.microsoft.com/azure/active-directory-b2c/session-overview).

Bei nachfolgenden Authentifizierungsanforderungen liest und überprüft Azure AD B2C die cookiebasierte Sitzung und stellt ein Zugriffstoken aus, ohne den Benutzer zur erneuten Anmeldung aufzufordern. Wenn SSO mit eingeschränktem Gültigkeitsbereich auf Richtlinien- oder Anwendungsebene konfiguriert ist, wird für einen späteren Zugriff auf andere Richtlinien und Anwendungen eine erneute Authentifizierung benötigt.

### <a name="how-to-configure-sso"></a>So konfigurieren Sie SSO

[Konfigurieren Sie SSO](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso-quick-start) mandantenweit (dies ist die Standardeinstellung), sodass mehrere Anwendungen und Benutzerflows in Ihrem Mandanten dieselbe Benutzersitzung gemeinsam nutzen können. Eine mandantenweite Konfiguration bietet das höchste Maß an Resilienz für neue Authentifizierungen.  

## <a name="safe-deployment-practices"></a>Sichere Bereitstellungsmethoden

Änderungen an Code und Konfiguration sind die häufigsten Gründe für Dienstunterbrechungen. CI/CD-Prozesse und -Tools (Continuous Integration und Continuous Delivery) helfen bei einer schnellen Bereitstellung in großem Stil und reduzieren menschliche Fehler während der Test-, Bereitstellungs- und Produktionsphase. Nutzen Sie CI/CD, um Fehler zu minimieren und mehr Effizienz und Konsistenz zu erzielen. [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/cicd-data-overview) ist ein Beispiel für CI/CD.

## <a name="web-application-firewall"></a>Web Application Firewall

Schützen Sie Ihre Anwendungen vor bekannten Sicherheitsrisiken wie DDoS-Angriffen (Distributed Denial of Service), Einschleusung von SQL-Befehlen, Cross-Site Scripting, Remotecodeausführung und vielen weiteren. Eine Dokumentation dieser Risiken finden Sie unter [OWASP Top 10](https://owasp.org/www-project-top-ten/). Mit einer Web Application Firewall (WAF) können Sie Ihre Systeme und Benutzer vor gängigen Exploits und Sicherheitsrisiken schützen.

- Nutzen Sie die Azure [WAF](https://docs.microsoft.com/azure/web-application-firewall/overview), die zentralen Schutz vor Angriffen bietet.

- Verwenden Sie die WAF mit [Identity Protection und bedingtem Zugriff für Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/conditional-access-identity-protection-overview), um bei Verwendung von Azure AD B2C Schutz auf mehreren Ebenen bereitzustellen.  

## <a name="secrets-rotation"></a>Geheimnisrotation

Azure AD B2C verwendet Geheimnisse für Anwendungen, APIs, Richtlinien und Verschlüsselung. Diese Geheimnisse sichern die Authentifizierung, externe Interaktionen und den Speicher. Das National Institute of Standards and Technology (NIST) bezeichnet die Zeitspanne, in der ein bestimmter Schlüssel für die Nutzung durch legitime Entitäten autorisiert ist, als „cryptoperiod“ (Kryptoperiode). Wählen Sie die richtige Länge der [Kryptoperiode](https://csrc.nist.gov/publications/detail/sp/800-57-part-1/rev-5/final) für Ihre geschäftlichen Anforderungen. Entwickler müssen den Ablauf manuell festlegen und Geheimnisse weit vor ihrem Ablauf rotieren.

### <a name="how-to-implement-secret-rotation"></a>So implementieren Sie die Geheimnisrotation

- Verwenden Sie [verwaltete Identitäten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), damit sich unterstützte Ressourcen bei jedem Dienst authentifizieren können, der die Azure AD-Authentifizierung unterstützt. Mithilfe von verwalteten Identitäten können Sie Ressourcen automatisch verwalten, einschließlich der Rotation von Anmeldeinformationen.

- Legen Sie ein Inventar sämtlicher [Schlüssel und Zertifikate](https://docs.microsoft.com/azure/active-directory-b2c/policy-keys-overview) an, die in Azure AD B2C konfiguriert sind. Diese Liste enthält wahrscheinlich Schlüssel, die in benutzerdefinierten Richtlinien, [APIs](https://docs.microsoft.com/azure/active-directory-b2c/secure-rest-api), signierenden ID-Token und Zertifikaten für SAML verwendet werden.

- Rotieren Sie über CI/CD Geheimnisse, die innerhalb von zwei Monaten vor dem erwarteten Spitzenzeitraum ablaufen werden. Die empfohlene maximale Kryptoperiode für private Schlüssel, die einem Zertifikat zugeordnet sind, beträgt ein Jahr.

- Überwachen und rotieren Sie proaktiv die Anmeldeinformationen für APIs (z. B. Kennwörter und Zertifikate).

## <a name="test-rest-apis"></a>Testen von REST-APIs

Im Kontext der Resilienz muss das Testen von REST-APIs die Überprüfung von HTTP-Codes, Antwortnutzdaten, Headern sowie der Leistung beinhalten. Hierbei muss nicht nur überprüft werden, ob Pfade funktionieren, sondern es muss auch getestet werden, ob die API Problemszenarien ordnungsgemäß verarbeitet.

### <a name="how-to-test-apis"></a>So testen Sie APIs

Ihr Testplan sollte [umfassende API-Tests](https://docs.microsoft.com/azure/active-directory-b2c/best-practices#testing) beinhalten. Wenn Sie einen bevorstehenden Anstieg des Datenverkehrs aufgrund von Werbeaktionen oder Feiertagen planen, müssen Sie Ihre Auslastungstests mit den neuen Schätzungen überarbeiten. Führen Sie die Auslastungstests Ihrer APIs und des CDN (Content Delivery Network) in einer Entwicklerumgebung durch, nicht in der Produktionsumgebung.

## <a name="next-steps"></a>Nächste Schritte

- [Resilienzressourcen für Azure AD B2C-Entwickler](resilience-b2c.md)
  - [Resiliente Endbenutzerumgebung](resilient-end-user-experience.md)
  - [Resiliente Schnittstellen mit externen Prozessen](resilient-external-processes.md)
  - [Resilienz durch Überwachung und Analyse](resilience-with-monitoring-alerting.md)
- [Erzielen von Resilienz in der Authentifizierungsinfrastruktur](resilience-in-infrastructure.md)
- [Mehr Resilienz bei Authentifizierung und Autorisierung in Ihren Anwendungen](resilience-app-development-overview.md)

---
title: Azure-Sicherheitsbaseline für Azure App Configuration
description: Die Azure App Configuration-Sicherheitsbaseline enthält Anweisungen zur Vorgehensweise und Ressourcen für die Implementierung der Sicherheitsempfehlungen im Vergleichstest für die Azure-Sicherheit.
author: msmbaldwin
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 79a99ad37c526103fc3068562d62ed40defc983b
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532379"
---
# <a name="azure-security-baseline-for-azure-app-configuration"></a>Azure-Sicherheitsbaseline für Azure App Configuration

Diese Sicherheitsbaseline wendet Empfehlungen des [Azure-Sicherheitsvergleichstests Version 2.0](../security/benchmarks/overview.md) auf Azure App Configuration an. Der Azure-Sicherheitsvergleichstest enthält Empfehlungen zum Schutz Ihrer Cloudlösungen in Azure. Der Inhalt wird nach den **Sicherheitskontrollen** gruppiert, die durch den Azure-Sicherheitsvergleichstest und die entsprechenden für Azure App Configuration geltenden Empfehlungen definiert werden. Nicht auf Azure App Configuration anwendbare **Kontrollen** wurden ausgeschlossen.

Die vollständige Zuordnung von Azure App Configuration zum Azure-Sicherheitsvergleichstest finden Sie in der [vollständigen Zuordnungsdatei der Azure App Configuration-Sicherheitsbaseline](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: Implementieren der Sicherheit für internen Datenverkehr

**Leitfaden**: Azure App Configuration stellt keine Ressourcen direkt in einem virtuellen Netzwerk bereit. Da der Dienst nicht in einem virtuellen Netzwerk bereitgestellt wird, können Sie bestimmte Netzwerkfunktionen nicht nutzen, um den internen Datenverkehr des Diensts zu sichern, wie Netzwerksicherheitsgruppen, Routingtabellen oder andere Netzwerkgeräte wie z. B. eine Azure Firewall. App Configuration gestattet Ihnen aber die Verwendung privater Endpunkte, um eine sichere Verbindung mit Azure App Configuration aus einem virtuellen Netzwerk heraus herzustellen.

Verwenden Sie Azure Sentinel, um die Verwendung von unsicheren Legacyprotokollen wie SSL/TLSv1, TLSv1, LM/SMBv1, WDigest, nicht signierte LDAP-Bindungen und schwache Chiffren in Kerberos zu ermitteln.

- [Verwenden privater Endpunkte für Azure App Configuration](concept-private-endpoint.md)

- [Arbeitsmappe für unsichere Protokolle in Azure Sentinel](../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="ns-2-connect-private-networks-together"></a>NS-2: Verbinden privater Netzwerke

**Leitfaden**: Azure App Configuration unterstützt die Verwendung privater Endpunkte zum sicheren Senden von Daten über eine private Verbindung. Stellen Sie mit Azure ExpressRoute oder dem virtuellen privaten Azure-Netzwerk (VPN) private Verbindungen mit Azure-Rechenzentren und lokaler Infrastruktur in einer Housingumgebung her. ExpressRoute-Verbindungen werden nicht über das öffentliche Internet hergestellt und bieten mehr Zuverlässigkeit, eine höhere Geschwindigkeit und weniger Latenz als herkömmliche Internetverbindungen. Für Point-to-Site-VPN und Site-to-Site-VPN können Sie lokale Geräte oder Netzwerke mit einem virtuellen Netzwerk verbinden, indem Sie eine beliebige Kombination dieser VPN-Optionen und Azure ExpressRoute verwenden.

Stellen Sie eine Verbindung von zwei oder mehr virtuellen Netzwerken in Azure mittels Peering virtueller Netzwerke her. Der Netzwerkdatenverkehr zwischen mittels Peering verbundenen virtuellen Netzwerken ist privat und wird im Azure-Backbone-Netzwerk verwaltet.

- [ExpressRoute-Konnektivitätsmodelle](../expressroute/expressroute-connectivity-models.md)

- [Was ist VPN Gateway?](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Peering von virtuellen Netzwerken](../virtual-network/virtual-network-peering-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Einrichten des Zugriffs über das private Netzwerk auf Azure-Dienste

**Leitfaden**: Ermöglichen Sie mit Azure Private Link von Ihren virtuellen Netzwerken aus privaten Zugriff auf Azure App Configuration, ohne über das Internet zu gehen.

Der private Zugriff ist neben der Authentifizierung und der von Azure-Diensten gebotenen Datenverkehrssicherheit eine zusätzliche Verteidigungsmaßnahme.

- [Grundlegendes zu Azure Private Link](../private-link/private-link-overview.md)

- [Einrichten der privaten Verbindung in Azure App Configuration](concept-private-endpoint.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Schützen von Anwendungen und Diensten vor externen Netzwerkangriffen.

**Leitfaden**: Schützen Sie beim Zugriff auf Konfigurationswerte über ein virtuelles Netzwerk Ihre Ressourcen vor Angriffen aus externen Netzwerken, einschließlich verteilter Denial-of-Service-Angriffe (DDoS), anwendungsspezifischer Angriffe und unerwünschtem und potenziell schädlichem Internetdatenverkehr. Schützen Sie mit der Azure-Firewall Anwendungen und Dienste vor potenziell schädlichem Datenverkehr aus dem Internet und von anderen externen Standorten. Schützen Sie Ihre Ressourcen durch Aktivieren des DDoS-Standardschutzes in Ihren virtuellen Azure-Netzwerken vor DDoS-Angriffen. Verwenden Sie Azure Security Center, um Risiken durch Fehlkonfigurationen zu erkennen, die mit Ihren netzwerkbezogenen Ressourcen zusammenhängen.

Azure App Configuration ist nicht für die Ausführung von Webanwendungen gedacht. Es stellt die Konfiguration für diese Webanwendungen bereit. Sie müssen keine zusätzlichen Einstellungen konfigurieren oder zusätzliche Netzwerkdienste bereitstellen, um es vor externen Netzwerkangriffen auf Webanwendungen zu schützen.

- [Azure Firewall-Dokumentation](../firewall/index.yml)

- [Verwalten von Azure DDoS Protection Standard mithilfe des Azure-Portals](../ddos-protection/manage-ddos-protection.md)

- [Azure Security Center-Empfehlungen](../security-center/recommendations-reference.md#recs-network)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: Bereitstellen von Angriffserkennungs-/Eindringschutzsystemen (Intrusion Detection/Intrusion Prevention Systems, IDS/IPS)

**Leitfaden**: Verwenden Sie Azure Firewall mit Threat Intelligence-basierter Filterung, um bei bekannten böswilligen IP-Adressen und Domänen zu warnen und/oder Datenverkehr zu und von diesen Adressen oder Domänen zu blockieren. Die IP-Adressen und Domänen stammen aus dem Microsoft Threat Intelligence-Feed. Wenn eine Payloaduntersuchung erforderlich ist, können Sie ein Angriffserkennungs-/Eindringschutzsystem (IDS/IPS) eines Drittanbieters mit Payloaduntersuchungsfunktionen aus Azure Marketplace bereitstellen. Alternativ können Sie eine hostbasierte IDS/IPS- oder Endpunkterkennungs- und Antwortlösung (EDR) in Verbindung mit einem netzwerkbasierten IDS/IPS oder stattdessen verwenden.

Hinweis: Bei einer gesetzlichen oder sonstigen Anforderung der Verwendung von IDS/IPS müssen Sie sicherstellen, dass sie stets auf hochwertige Warnungen für Ihre SIEM-Lösung abgestimmt ist.

- [Bereitstellen von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace umfasst IDS-Funktionen von Drittanbietern](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [ATP-EDR-Funktion von Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: Vereinfachen von Netzwerksicherheitsregeln

**Leitfaden**: Verwenden Sie Azure Virtual Network-Diensttags, um Netzwerkzugriffssteuerungen in Netzwerksicherheitsgruppen oder Azure Firewall zu definieren, die für Ihre App Configuration-Ressourcen konfiguriert sind. Beim Erstellen von Sicherheitsregeln für ausgehenden Datenverkehr im Netzwerk Ihrer Anwendung können Sie das Diensttag „AppConfiguration“ anstelle spezifischer IP-Adressen verwenden. Indem Sie den Diensttagnamen im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

- [Grundlegendes zu Diensttags und Verwenden von Diensttags](../virtual-network/service-tags-overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="identity-management"></a>Identitätsverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Identitätsverwaltung](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardisieren von Azure Active Directory als zentrales Identitäts- und Authentifizierungssystem

**Leitfaden**: Azure App Configuration ist in Azure Active Directory (Azure AD) integriert, den standardmäßigen Identitäts- und Zugriffsverwaltungsdienst von Azure. Sie sollten Azure AD als Standard für die Identitäts- und Zugriffsverwaltung Ihrer Organisation verwenden:
- Microsoft-Cloudressourcen, wie z. B. das Azure-Portal, Azure Storage, Azure-VMs (Linux und Windows), Azure Key Vault, PaaS- und SaaS-Anwendungen.
- Die Ressourcen Ihrer Organisation, z. B. Anwendungen in Azure oder Ihre Unternehmensnetzwerkressourcen.

Die Sicherung von Azure AD sollte bei den Methoden Ihrer Organisation im Zusammenhang mit Cloudsicherheit eine hohe Priorität haben. Azure AD bietet eine Identitätssicherheitsbewertung, die Ihnen dabei hilft, den Identitätssicherheitsstatus in Bezug auf die Empfehlungen zu bewährten Methoden von Microsoft zu bewerten. Verwenden Sie die Bewertung, um zu beurteilen, wie gut Ihre Konfiguration den Empfehlungen zu bewährten Methoden entspricht, und um Ihren Sicherheitsstatus zu verbessern.

Azure stellt die folgenden integrierten Azure Rollen zum Autorisieren des Zugriffs auf App Configuration-Daten mittels Azure AD und OAuth bereit:

- App Configuration-Datenbesitzer: Verwenden Sie diese Rolle, um Lese-, Schreib- und Löschzugriff auf App Configuration-Daten zu gewähren. Dadurch wird kein Zugriff auf die App Configuration-Ressource gewährt.

- App Configuration-Datenleser: Verwenden Sie diese Rolle, um Lesezugriff auf App Configuration-Daten zu gewähren. Dadurch wird kein Zugriff auf die App Configuration-Ressource gewährt.

- Mitwirkender: Verwenden Sie diese Rolle, um die App Configuration-Ressource zu verwalten. Obwohl auf die App Configuration-Daten mithilfe von Zugriffsschlüsseln zugegriffen werden kann, wird mit dieser Rolle kein direkter Zugriff auf die Daten unter Verwendung von Azure AD gewährt.

- Leser: Verwenden Sie diese Rolle, um Lesezugriff auf die App Configuration-Ressource zu gewähren. Dadurch wird weder Zugriff auf die Zugriffsschlüssel der Ressource noch auf die in App Configuration gespeicherten Daten gewährt.

Weitere Informationen finden Sie in den folgenden Referenzen:

- [Erstellen und Konfigurieren einer Azure AD-Instanz](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Was ist der Identity Secure Score in Azure Active Directory?](../active-directory/fundamentals/identity-secure-score.md)

- [Autorisieren des Zugriffs auf Azure App Configuration mittels Azure AD](concept-enable-rbac.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Sicheres und automatisches Verwalten von Anwendungsidentitäten

**Leitfaden**: Verwenden Sie von Azure verwaltete Identitäten, um von nicht Personen zugeordneten Konten wie anderen Azure-Diensten auf Azure App Configuration zuzugreifen. Es wird empfohlen, das Feature für verwaltete Azure-Identitäten zu verwenden, anstatt ein leistungsfähigeres Benutzerkonto für den Zugriff auf Ihre Ressourcen oder deren Ausführung zu erstellen, um die Notwendigkeit der Verwaltung zusätzlicher Anmeldeinformationen einzuschränken. Azure App Configuration kann auch selbst eine verwaltete Identität zugewiesen werden, um sich bei anderen Azure-Diensten/-Ressourcen, die Azure AD-Authentifizierung unterstützen, nativ zu authentifizieren. Dies kann hilfreich sein, um beim Abrufen von Geheimnissen den einfachen Zugriff von App Configuration auf Azure Key Vault zu ermöglichen. Wenn Sie verwaltete Identitäten verwenden, wird die Identität von der Azure-Plattform verwaltet, und Sie müssen keine Geheimnisse bereitstellen oder rotieren.

Azure App Configuration unterstützt, dass Ihrer Anwendung zwei Arten von Identitäten zugewiesen werden:
- Eine systemseitig zugewiesene Identität ist an Ihre Konfigurationsressource gebunden. Sie wird gelöscht, wenn Ihre Konfigurationsressource gelöscht wird. Eine Konfigurationsressource kann nur eine systemseitig zugewiesene Identität aufweisen.
- Eine benutzerseitig zugewiesene Identität ist eine eigenständige Azure-Ressource, die Ihrer Konfigurationsressource zugewiesen werden kann. Eine Konfigurationsressource kann mehrere benutzerseitig zugewiesene Identitäten aufweisen.

Wenn verwaltete Identitäten nicht genutzt werden können, erstellen Sie einen Dienstprinzipal mit eingeschränkten Berechtigungen auf der Azure App Configuration-Ressourcenebene. Konfigurieren Sie diese Dienstprinzipale mit Zertifikatanmeldeinformationen und Fallback nur auf geheime Clientschlüssel. In beiden Fällen kann Azure Key Vault in Verbindung mit verwalteten Azure-Identitäten verwendet werden, damit die Laufzeitumgebung (z. B. eine Azure-Funktion) die Anmeldeinformationen aus dem Schlüsseltresor abrufen kann.

- [Verwenden von verwalteten Identitäten für Azure App Configuration](overview-managed-identity.md)

- [Von Azure verwaltete Identitäten](../active-directory/managed-identities-azure-resources/overview.md)

- [Dienste, die verwaltete Identitäten für Azure-Ressourcen unterstützen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Verwenden verwalteter Identitäten für den Zugriff auf App Configuration](howto-integrate-azure-managed-service-identity.md)

- [Azure-Dienstprinzipal](/powershell/azure/create-azure-service-principal-azureps) 

- [Erstellen eines Dienstprinzipals mit Zertifikaten](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [Verwenden von Azure Key Vault für die Registrierung von Sicherheitsprinzipalen](../key-vault/general/authentication.md#app-identity-and-security-principals)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Verwenden von Azure AD Single Sign-on (SSO) für den Anwendungszugriff

**Leitfaden**: Azure App Configuration verwendet Azure Active Directory (Azure AD), um die Identitäts- und Zugriffsverwaltung für Azure-Ressourcen, Cloudanwendungen und lokale Anwendungen bereitzustellen. Dies umfasst sowohl Unternehmensidentitäten wie Mitarbeiter als auch externe Identitäten wie Partner, Anbieter und Zulieferer. Azure AD ermöglicht es dem einmaligem Anmelden (Single Sign-On, SSO), den App Configuration-Dienst im Azure-Portal mithilfe synchronisierter Active Directory-Unternehmensidentitäten zu verwalten. Verbinden Sie all Ihre Benutzer, Anwendungen und Geräte mit Azure AD, um einen nahtlosen, sicheren Zugriff sowie mehr Transparenz und Kontrolle zu ermöglichen.

- [Grundlegendes zu Anwendungs-SSO mit Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Verwenden stärkerer Authentifizierungssteuerungen für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Azure App Configuration verwendet Azure Active Directory, das eine strenge Authentifizierung durch mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) und sichere kennwortlose Methoden unterstützt.
- Mehrstufige Authentifizierung: Aktivieren Sie Azure AD MFA, und wenden Sie mithilfe der Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center einige bewährte Methoden in Ihrem MFA-Setup an. MFA kann auf der Grundlage von Anmeldebedingungen und Risikofaktoren für alle, ausgewählte oder einzelne Benutzer erzwungen werden.
- Kennwortlose Authentifizierung: Es stehen drei Optionen für die kennwortlose Authentifizierung zur Verfügung: Windows Hello for Business, die Microsoft Authenticator-App und lokale Authentifizierungsmethoden wie Smartcards.

Stellen Sie für Administratoren und privilegierte Benutzer sicher, dass die höchste Stufe der starken Authentifizierungsmethode verwendet wird, und führen Sie für andere Benutzer eine geeignete starke Authentifizierungsrichtlinie ein.

Hinweis: MFA kann für die Benutzerkonten erzwungen werden, die auf die App Configuration zugreifen und diese verwalten, aber nicht für programmgesteuerte Dienstkonten. Verwenden Sie nach Möglichkeit kennwortlose Authentifizierung, z. B. verwaltete Identitäten, und erzwingen Sie MFA für alle Benutzerkonten.

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Einführung in Optionen für die kennwortlose Authentifizierung für Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Überwachen und Warnen bei Kontoanomalien

**Leitfaden**: Azure App Configuration ist in den Azure Active Directory-Dienst integriert, der folgende Datenquellen bereitstellt:

-   Anmeldungen: Der Bericht „Anmeldungen“ enthält Informationen zur Nutzung von verwalteten Anwendungen und Aktivitäten der Benutzeranmeldung.

-   Überwachungsprotokolle: Ermöglichen die Nachverfolgung sämtlicher Änderungen, die von verschiedenen Features in Azure AD vorgenommen wurden. Zu den in Überwachungsprotokollen protokollierten Änderungen zählen unter anderem das Hinzufügen oder Entfernen von Benutzern, Apps, Gruppen, Rollen und Richtlinien.

-   Riskante Anmeldungen: Eine riskante Anmeldung ist ein Indikator für einen Anmeldeversuch von einem Benutzer, der nicht der rechtmäßige Besitzer eines Benutzerkontos ist.

-   Benutzer mit Risikomarkierung: Ein Benutzer mit Risikomarkierung ist ein Indikator für ein ggf. kompromittiertes Benutzerkonto.

Diese Datenquellen können mit Azure Monitor, Azure Sentinel oder SIEM-Systemen von Drittanbietern integriert werden.

Azure Security Center kann auch bei bestimmten verdächtigen Aktivitäten warnen, wie z. B. eine übermäßige Anzahl fehlgeschlagener Authentifizierungsversuche und veraltete Konten im Abonnement. 

Azure Advanced Threat Protection (ATP) ist eine Sicherheitslösung, die lokale Active Directory-Signale nutzen kann, um erweiterte Bedrohungen, kompromittierte Identitäten und schädliche Aktionen von Insidern zu identifizieren, zu erkennen und zu untersuchen.

- [Überwachungsaktivitätsberichte in Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Anzeigen riskanter Azure AD-Anmeldungen](../active-directory/identity-protection/overview-identity-protection.md)

- [Identifizieren von Azure AD-Benutzern, die aufgrund riskanter Aktivitäten gekennzeichnet wurden](../active-directory/identity-protection/overview-identity-protection.md)

- [Überwachen der identitäts- und zugriffsbezogenen Aktivitäten von Benutzern in Azure Security Center](../security-center/security-center-identity-access.md)

- [Warnungen im Threat Intelligence-Schutzmodul von Azure Security Center](../security-center/alerts-reference.md)

- [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Verknüpfen von Daten aus Azure AD Identity Protection](../sentinel/connect-azure-ad-identity-protection.md)

- [Azure Advanced Threat Protection](/azure-advanced-threat-protection/what-is-atp)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Einschränken des Zugriffs auf Azure-Ressourcen basierend auf Bedingungen

**Leitfaden**: Azure App Configuration unterstützt den bedingten Zugriff von Azure Active Directory (Azure AD) für eine präzisere Zugriffssteuerung auf der Grundlage benutzerdefinierter Bedingungen, wie z. B. dass Benutzeranmeldungen aus bestimmten IP-Bereichen MFA für die Anmeldung verwenden müssen. Die Sitzungsverwaltungsrichtlinie für die differenzierte Authentifizierung kann ebenfalls für verschiedene Anwendungsfälle verwendet werden. Diese Richtlinien für bedingten Zugriff gelten nur für Benutzerkonten, die sich bei Azure AD authentifizieren, um auf den App Configuration-Dienst zuzugreifen und diesen zu verwalten, aber Sie gelten nicht für Dienstprinzipale oder Verbindungszeichenfolgen, die eine Verbindung mit Ihrer Konfigurationsressource herstellen.

- [Übersicht über den bedingten Zugriff in Azure](../active-directory/conditional-access/overview.md)

- [Allgemeine Richtlinien für bedingten Zugriff](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Konfigurieren der Verwaltung von Authentifizierungssitzungen mit bedingtem Zugriff](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Leitfaden**: Die Azure App Configuration ermöglicht Kunden das Speichern von Konfigurationen, die möglicherweise Identitäten oder Geheimnisse enthalten. Es wird empfohlen, den Credential Scanner zu implementieren, um Anmeldeinformationen in Konfigurationen zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen.

Verwenden Sie den Azure App Configuration-Dienst zusammen mit Azure Key Vault. Speichern Sie alle Anmeldeinformationen in Key Vault, und verknüpfen Sie dann mit diesen Anmeldeinformationen, indem Sie in Ihrer App Configuration-Ressource einen Key Vault-Verweis erstellen. Wenn App Configuration diese Verweise erstellt, werden nicht die eigentlichen Werte, sondern die URIs der Key Vault-Werte gespeichert. Anwendungen können eine Verbindung mit App Configuration herstellen, um Anmeldeinformationen aus Key Vault abzurufen.

Für GitHub können Sie das native Feature zur geheimen Überprüfung verwenden, um Anmeldeinformationen oder eine andere Form von Geheimnissen im Code zu identifizieren.

- [Tutorial zum Verwenden von Key Vault-Verweisen in einer ASP.NET Core-App](use-key-vault-references-dotnet-core.md)

- [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Geheime Überprüfung in GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="privileged-access"></a>Privilegierter Zugriff

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Privilegierter Zugriff](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Schützen und Einschränken stark privilegierter Benutzer

**Leitfaden**: Begrenzen Sie die Anzahl hoch privilegierter Konten oder Rollen, und schützen Sie diese Konten auf höherer Ebene, da Benutzer mit dieser Berechtigung alle Ressourcen in Ihrer Azure-Umgebung direkt oder indirekt lesen und ändern können.

Mit Azure AD Privileged Identity Management (PIM) können Sie privilegierten Just-in-Time-Zugriff (JIT) auf Azure-Ressourcen und Azure AD ermöglichen. JIT erteilt nur dann temporäre Berechtigungen zur Ausführung privilegierter Aufgaben, wenn die Benutzer sie benötigen. PIM kann auch Sicherheitswarnungen erzeugen, wenn es in Ihrer Azure AD-Organisation verdächtige oder unsichere Aktivitäten gibt.

Zugriffsschlüssel sind hoch privilegiert und sollten als bewährte Sicherheitsmethode regelmäßig rotiert werden. Zugriffsschlüssel enthalten Verbindungszeichenfolgen, die wiederum Anmeldeinformationen enthalten, und gelten als Geheimnisse. Diese Geheimnisse müssen in Azure Key Vault gespeichert werden, und Ihr Code muss bei Key Vault authentifiziert werden, um sie abrufen zu können. Zugriffsschlüssel können einer Anwendung Lese-/Schreibzugriff oder nur Lesezugriff auf eine Anwendung gewähren. Stellen Sie sicher, dass der richtige Typ von Zugriffsschlüssel ausgestellt wird, um nicht autorisierten Zugriff zu verhindern. Verwenden Sie zur Erhöhung des Schutzes die Funktion für verwaltete Identitäten in Azure AD. Hierzu müssen Anwendungen nur die Konfigurationsendpunkt-URL besitzen, um auf Konfigurationswerte zuzugreifen.

- [Bewährte Methoden für App Configuration](howto-best-practices.md#app-configuration-bootstrap)

- [Verwenden verwalteter Identitäten für den Zugriff auf App Configuration](howto-integrate-azure-managed-service-identity.md)
- [Administratorrollenberechtigungen in Azure AD](../active-directory/roles/permissions-reference.md)

- [Verwenden von Sicherheitswarnungen von Azure Privileged Identity Management](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Schützen des privilegierten Zugriffs für hybride und Cloudbereitstellungen in Azure AD](../active-directory/roles/security-planning.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Einschränken des administrativen Zugriffs auf unternehmenskritische Systeme

**Leitfaden**: Azure App Configuration verwendet Azure RBAC, um den Zugriff auf unternehmenskritische Systeme zu isolieren, indem eingeschränkt wird, welchen Konten privilegierter Zugriff gewährt wird. Azure RBAC wird von App Configuration auf Ressourcenebene unterstützt. Zum sicheren Aufbewahren von geschäftskritischen Konfigurationen in Silos, speichern Sie diese Informationen in einer eigenen App Configuration-Ressource. Innerhalb einer Ressource sind der fein abgestufte Zugriff auch durch Konten oder Schlüssel mit schreibgeschütztem Zugriff sowie das Bezeichnen und Tagging verfügbar.

Alle Zugriffssteuerungstypen sollten an der Segmentierungsstrategie des Unternehmens ausgerichtet werden, um eine konsistente Zugriffssteuerung sicherzustellen.

- [Zugriff auf die Verwaltungsgruppe](../governance/management-groups/overview.md#management-group-access)

- [Azure-Abonnementadministratoren](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [So integrieren Sie RBAC mithilfe von Azure AD in App Configuration](concept-enable-rbac.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure App Configuration verwendet Azure Active Directory-Konten (Azure AD) zum Verwalten seiner Ressourcen, zum regelmäßigen Überprüfen von Benutzerkonten und Zuweisen von Zugriffsberechtigungen, um die Gültigkeit der Konten und Zugriffsberechtigungen sicherzustellen. 

Azure stellt die folgenden integrierten Azure Rollen zum Autorisieren des Zugriffs auf App Configuration-Daten mittels Azure AD und OAuth bereit:

- App Configuration-Datenbesitzer: Verwenden Sie diese Rolle, um Lese-, Schreib- und Löschzugriff auf App Configuration-Daten zu gewähren. Dadurch wird kein Zugriff auf die App Configuration-Ressource gewährt.

- App Configuration-Datenleser: Verwenden Sie diese Rolle, um Lesezugriff auf App Configuration-Daten zu gewähren. Dadurch wird kein Zugriff auf die App Configuration-Ressource gewährt.

Sie können Azure AD-Zugriffsüberprüfungen verwenden, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen wie die obigen App Configuration-Rollen effizient zu überprüfen. Azure AD-Berichte können Protokolle zum Ermitteln von veralteten Konten bereitstellen. Sie können Azure AD Privileged Identity Management auch verwenden, um einen Workflow für den Zugriffsüberprüfungsbericht zu erstellen und so den Überprüfungsprozess zu vereinfachen.

Hinweis: Verwaltete Identitäten werden, wo möglich, für die Authentifizierung bei App Configuration aus einem anderen Dienst oder einer anderen Anwendung vorgeschlagen. Sie müssen alle Dienstprinzipale oder Verbindungszeichenfolgen, die mit Zugriff auf App Configuration konfiguriert sind, bei der Verwendung separat verwalten.

- [Erstellen einer Zugriffsüberprüfung für Azure-Ressourcenrollen in Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Was sind Azure AD-Zugriffsüberprüfungen?](/azure/active-directory/governance/access-reviews-overview)

- [Autorisieren des Zugriffs auf Azure App Configuration mittels Azure AD](concept-enable-rbac.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Einrichten des Notfallzugriffs in Azure AD

**Leitfaden**: Azure App Configuration ist in Azure Active Directory für die Ressourcenverwaltung integriert. Um zu verhindern, dass Sie versehentlich aus Ihrer Azure AD-Organisation ausgesperrt werden, richten Sie ein Konto für den Notfallzugriff ein, für den Fall, dass normale Verwaltungskonten nicht verwendet werden können. Konten für den Notfallzugriff verfügen normalerweise über umfangreiche Berechtigungen und sollten keinen Einzelpersonen zugewiesen werden. Konten für den Notfallzugriff sind auf Notfallsituationen oder Szenarien beschränkt, in denen normale Administratorkonten nicht verwendet werden können.

Sie sollten sicherstellen, dass die Anmeldeinformationen (z. B. Kennwort, Zertifikat oder Smartcard) für Konten für den Notfallzugriff sicher aufbewahrt werden und nur den Personen bekannt sind, die für deren Verwendung im Notfall autorisiert sind.

- [Verwalten von Konten für den Notfallzugriff in Azure AD](../active-directory/roles/security-emergency-access.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="pa-5-automate-entitlement-management"></a>PA-5: Automatisieren der Berechtigungsverwaltung 

**Leitfaden**: Azure App Configuration ist in Azure Active Directory für die Ressourcenverwaltung integriert. Verwenden Sie die Berechtigungsverwaltungsfeatures von Azure AD, um die Zugriffsanforderungsworkflows zu automatisieren, einschließlich Zugriffszuweisungen, Überprüfungen und Ablauf. Die duale oder die mehrstufige Genehmigung wird ebenfalls unterstützt.

- [Was sind Azure AD-Zugriffsüberprüfungen?](../active-directory/governance/access-reviews-overview.md)

- [Was ist die Azure AD-Berechtigungsverwaltung?](../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Verwenden von Privileged Access Workstations

**Leitfaden**: Gesicherte, isolierte Arbeitsstationen sind von entscheidender Bedeutung für die Sicherheit sensibler Rollen wie Administratoren, Entwickler und Operatoren kritischer Dienste. Verwenden Sie stark gesicherte Benutzerworkstations und/oder Azure Bastion für administrative Aufgaben im Zusammenhang mit App Configuration. Verwenden Sie Azure Active Directory, Microsoft Defender Advanced Threat Protection (ATP) und/oder Microsoft Intune, um eine sichere und verwaltete Benutzerworkstation für administrative Aufgaben einzurichten. Die gesicherten Workstations können zentral verwaltet werden, um eine gesicherte Konfiguration einschließlich starker Authentifizierung, Software- und Hardwarebaselines, eingeschränktem logischen und Netzwerkzugang durchzusetzen.

- [Informationen zu sicheren, von Azure verwalteten Arbeitsstationen](../active-directory/devices/concept-azure-managed-workstation.md) 

- [Bereitstellen einer sicheren, von Azure verwalteten Arbeitsstation](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Befolgen Sie die Prinzipien der Just Enough Administration (Prinzip der geringsten Rechte) 

**Leitfaden**: Azure App Configuration ist mit der integrierten rollenbasierten Zugriffssteuerung in Azure (Azure RBAC) integriert, um die eigenen Ressourcen zu verwalten. Mit Azure RBAC kann der Zugriff auf Azure-Ressourcen über Rollenzuweisungen verwaltet werden. Diese Rollen können Benutzern, Gruppen, Dienstprinzipalen und verwalteten Identitäten zugewiesen werden. Für Azure App Configuration sind vordefinierte integrierte Rollen verfügbar. Diese Rollen können über Tools wie die Azure CLI, Azure PowerShell oder das Azure-Portal inventarisiert oder abgefragt werden. Die Berechtigungen, die Sie Ressourcen über Azure RBAC zuweisen, sollten immer auf die nötigsten Anforderungen der Rollen beschränkt sein. Dies ergänzt den JIT-Ansatz (Just-in-Time) von Azure AD Privileged Identity Management (PIM), und diese Berechtigungen sollten regelmäßig überprüft werden.

Azure stellt die folgenden integrierten Azure Rollen zum Autorisieren des Zugriffs auf App Configuration-Daten mittels Azure AD und OAuth bereit:
- App Configuration-Datenbesitzer: Verwenden Sie diese Rolle, um Lese-, Schreib- und Löschzugriff auf App Configuration-Daten zu gewähren. Dadurch wird kein Zugriff auf die App Configuration-Ressource gewährt.
- App Configuration-Datenleser: Verwenden Sie diese Rolle, um Lesezugriff auf App Configuration-Daten zu gewähren. Dadurch wird kein Zugriff auf die App Configuration-Ressource gewährt.

Verwenden Sie integrierte Rollen zur Zuweisung von Berechtigungen, und erstellen Sie benutzerdefinierte Rollen nur bei Bedarf. 

App Configuration unterstützt das Speichern der Konfiguration mehrerer Anwendungen in einer App Configuration-Ressource. Um den Zugriff auf Informationen zwischen Anwendungen einzuschränken, erstellen Sie eine App Configuration-Ressource pro Anwendung, und richten Sie Azure RBAC entsprechend ein.

- [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?](../role-based-access-control/overview.md)

- [Konfigurieren von RBAC in Azure](../role-based-access-control/role-assignments-portal.md)

- [Was sind Azure AD-Zugriffsüberprüfungen?](../active-directory/governance/access-reviews-overview.md)

- [Autorisieren des Zugriffs auf Azure App Configuration mittels Azure AD](concept-enable-rbac.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: Auswählen des Genehmigungsprozesses für Microsoft-Support  

**Leitfaden**: Implementieren Sie einen organisatorischen Genehmigungsprozess für Supportszenarien, in denen Microsoft möglicherweise Zugriff auf Ihre App Configuration-Daten benötigt. Kunden-Lockbox ist für App Configuration-Supportszenarien zurzeit nicht verfügbar.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-protection"></a>Datenschutz

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Schutz von Daten](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1: Ermitteln, Klassifizieren und Bezeichnen von vertraulichen Daten

**Leitfaden**: Ermitteln, klassifizieren und bezeichnen Sie Ihre vertraulichen Daten, sodass Sie die entsprechenden Steuerungen entwerfen können, um sicherzustellen, dass vertrauliche Daten von den Technologiesystemen der Organisation sicher gespeichert, verarbeitet und übertragen werden. Das Bezeichnen vertraulicher Informationen in Form von Tagging wird für App Configuration-Ressourcen unterstützt, aber nicht für die darin enthaltenen Konfigurationswerte. Sobald eine Anwendung Lese- oder Lese-/Schreibzugriff auf einen Konfigurationsspeicher besitzt, verfügt sie über Vollzugriff auf jede der Konfigurationen in diesem Speicher.

- [Markieren vertraulicher Informationen mit Azure Information Protection](/azure/information-protection/what-is-information-protection)

- [Kennzeichnen von Datenklassifizierungen in Azure](/azure/cloud-adoption-framework/govern/policy-compliance/data-classification#tagging-data-classification-in-azure)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Schützen von vertraulichen Daten

**Leitfaden**: Für die zugrunde liegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und schützt Kundendaten vor Verlust und Gefährdung. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von Standard-Datenschutzsteuerungen und -funktionen implementiert. Stellen Sie sicher, dass Sie die Zugriffsschlüssel für Ihre App Configuration-Ressourcen regelmäßig rotieren. Anmeldeinformationen aus Verbindungszeichenfolgen können in Azure Key Vault gespeichert werden, und Ihr Code muss bei Key Vault authentifiziert werden, um sie abrufen zu können. Zugriffsschlüssel können einer Anwendung Lese-/Schreibzugriff oder nur Lesezugriff auf eine Anwendung gewähren. Stellen Sie sicher, dass der richtige Typ von Zugriffsschlüssel ausgestellt wird, um nicht autorisierten Zugriff zu verhindern. Verwenden Sie zur Erhöhung des Schutzes die Funktion für verwaltete Identitäten in Azure AD. Hierzu müssen Anwendungen nur die Konfigurationsendpunkt-URL besitzen, um auf Konfigurationswerte zuzugreifen.

Schränken Sie den Zugriff mithilfe der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC) ein:

- Isolieren Sie vertrauliche Daten in ihrer eigenen App Configuration-Ressource, und weisen Sie dann RBAC-Richtlinien entsprechend zu, sodass nur autorisierter Zugriff aktiviert ist. 

- Verwenden netzwerkbasierter Zugriffssteuerung

- Spezifische Steuerelemente in Azure-Diensten (wie Verschlüsselung in SQL und anderen Datenbanken) und um eine konsistente Zugriffssteuerung sicherzustellen, sollten alle Zugriffssteuerungstypen an der Segmentierungsstrategie Ihres Unternehmens ausgerichtet werden.

- Bei der Segmentierungsstrategie des Unternehmens sollte auch der Speicherort vertraulicher und unternehmenskritischer Daten und Systeme berücksichtigt werden.

Weitere Informationen finden Sie in den folgenden Referenzen:

- [Autorisieren des Zugriffs auf Azure App Configuration mittels Azure Active Directory](concept-enable-rbac.md)

- [App Configuration-Datenverschlüsselung](faq.md#does-app-configuration-encrypt-my-data)

- [Rollenbasierte Zugriffssteuerung in Azure (Role-Based Access Control, RBAC)](../role-based-access-control/overview.md) 

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Verschlüsseln vertraulicher Informationen während der Übertragung

**Leitfaden**: Als Ergänzung zu Zugriffssteuerungen sollten Daten während der Übertragung durch Verschlüsselung vor Out-of-Band-Angriffen geschützt werden. Dadurch wird sichergestellt, dass die Daten von Angreifern nicht einfach gelesen oder geändert werden können.

Azure App Configuration verwendet TLS-Verschlüsselung für alle HTTP-Anforderungen. Die Azure-Infrastruktur bietet eine zusätzliche Verschlüsselungsschicht auf Netzwerkebene für alle Anforderungen zwischen Azure-Rechenzentren. Stellen Sie für den HTTP-Datenverkehr sicher, dass alle Clients, die Verbindungen mit Ihren App Configuration-Ressourcen herstellen, TLS v1.2 oder höher aushandeln können.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Verschlüsseln vertraulicher ruhender Daten

**Leitfaden**: Als Ergänzung zu Zugriffssteuerungen sollten ruhende Daten durch Verschlüsselung vor Out-of-Band-Angriffen (z. B. Zugriffen auf den zugrunde liegenden Speicher) geschützt werden. Dadurch wird sichergestellt, dass die Daten von Angreifern nicht einfach gelesen oder geändert werden können.

Azure bietet standardmäßig Verschlüsselung für ruhende Daten. Bei streng vertraulichen Daten stehen Ihnen ggf. Optionen zur Verfügung, um eine zusätzliche Verschlüsselung im Ruhezustand für alle Azure-Ressourcen zu implementieren. Azure verwaltet standardmäßig Ihre Verschlüsselungsschlüssel, Azure bietet aber auch die Optionen, mit denen Sie Ihre eigenen Schlüssel (kundenseitig verwaltete Schlüssel) für Azure App Configuration verwalten können.

- [Verwenden von kundenseitig verwalteten Schlüsseln zum Verschlüsseln Ihrer Daten in Azure App Configuration](concept-customer-managed-keys.md)

- [Grundlegendes zur Verschlüsselung ruhender Daten in Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Tabelle für Verschlüsselungsmodell und Schlüsselverwaltung](../security/fundamentals/encryption-atrest.md#azure-resource-providers-encryption-model-support)

- [Doppelte Verschlüsselung für ruhende Daten in Azure](../security/fundamentals/double-encryption.md#data-at-rest)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="asset-management"></a>Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Ressourcenverwaltung](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Sicherstellen, dass das Sicherheitsteam Risiken für Ressourcen einsehen kann

**Leitfaden**: Stellen Sie sicher, dass Sicherheitsteams die Berechtigungen „Sicherheitsleseberechtigter“ in Ihrem Azure-Mandanten und Ihren Abonnements erhalten, damit sie mit Azure Security Center Sicherheitsrisiken überwachen können. 

Abhängig davon, wie die Verantwortungsbereiche des Sicherheitsteams strukturiert sind, kann die Überwachung auf Sicherheitsrisiken unter die Verantwortung eines zentralen Sicherheitsteams oder eines lokalen Teams fallen. Aus diesem Grund müssen Sicherheitserkenntnisse und -risiken immer innerhalb einer Organisation zentral aggregiert werden. 

Die Berechtigungen der Rolle „Sicherheitsleseberechtigter“ können weit gefasst auf einen gesamten Mandanten (Stammverwaltungsgruppe) angewandt oder auf Verwaltungsgruppen oder bestimmte Abonnements beschränkt werden. 

Hinweis: Möglicherweise sind zusätzliche Berechtigungen erforderlich, um Einblick in Workloads und Dienste zu erhalten. 

- [Übersicht über die Rolle „Sicherheitsleseberechtigter“](../role-based-access-control/built-in-roles.md#security-reader)

- [Übersicht über Azure-Verwaltungsgruppen](../governance/management-groups/overview.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Sicherstellen, dass das Sicherheitsteam Zugriff auf den Ressourcenbestand und die Metadaten hat

**Leitfaden**: Stellen Sie sicher, dass Sicherheitsteams Zugriff auf einen fortlaufend aktualisierten Bestand der Ressourcen in Azure haben, z. B. Azure App Configuration. Sicherheitsteams benötigen diesen Bestand häufig, um die potenziellen Angriffsflächen ihrer Organisation im Hinblick auf neue Risiken zu evaluieren, sowie als Quelle für die kontinuierliche Verbesserung der Sicherheit. Erstellen Sie eine Azure Active Directory-Gruppe, die das autorisierte Sicherheitsteam Ihrer Organisation aufnehmen soll, und weisen Sie dem Team Lesezugriff für alle Azure App Configuration-Ressourcen zu. Dies lässt sich durch eine einzelne Rollenzuweisung auf hoher Ebene innerhalb Ihres Abonnements vereinfachen.

Das Azure Security Center-Bestandsfeature und Azure Resource Graph können alle Ressourcen in Ihren Abonnements abfragen und ermitteln, einschließlich Azure-Diensten, Anwendungen und Netzwerkressourcen.

Wenden Sie Tags auf Ihre Ressourcen, Ressourcengruppen und Abonnements in Azure an, um sie logisch in einer Taxonomie zu strukturieren. Jedes Tag besteht aus einem Paar mit einem Namen und einem Wert. So können Sie beispielsweise den Namen „Umgebung“ und den Wert „Produktion“ auf alle Ressourcen in der Produktion anwenden.

- [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](../governance/resource-graph/first-query-portal.md)

- [Verwalten des Ressourcenbestands in Azure Security Center](../security-center/asset-inventory.md)

- [Weitere Informationen zum Taggen von Ressourcen finden Sie im „Leitfaden zur Entscheidungsfindung für Ressourcenbenennung und -markierung“.](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Azure App Configuration unterstützt auf Azure Resource Manager basierende Bereitstellungen und die Erzwingung von Konfigurationen mithilfe von Azure Policy. Verwenden Sie Azure Policy, um die Dienste, die von Benutzern in Ihrer Umgebung bereitgestellt werden, zu überwachen und einzuschränken. Verwenden Sie Azure Resource Graph, um Ressourcen in ihren Abonnements abzufragen und zu ermitteln. Sie können auch mit Azure Monitor Regeln erstellen, mit denen Warnungen ausgelöst werden, wenn ein nicht genehmigter Dienst erkannt wird.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Gewährleisten der Sicherheit der bei der Lebenszyklusverwaltung von Ressourcen

**Leitfaden**: Richten Sie Sicherheitsrichtlinien ein, mit denen die Prozesse der Lebenszyklusverwaltung von Ressourcen auf potenziell schwerwiegende Änderungen überwacht werden, oder aktualisieren Sie sie. Diese Änderungen umfassen Änderungen an Identitätsanbietern und dem Zugriff, der Vertraulichkeit von Daten, der Netzwerkkonfiguration und der Zuweisung von Administratorberechtigungen, sind aber nicht darauf beschränkt.

Entfernen Sie Azure-Ressourcen, wenn diese nicht mehr benötigt werden. Stellen Sie sicher, dass Administratoren Zugriffsschlüssel regelmäßig rotieren, um sicherzustellen, dass nur authentifizierte Benutzer auf die Konfigurationsressource besitzen.

- [Rotieren von für App Configration verwendeten Verschlüsselungsschlüsseln](concept-customer-managed-keys.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden**: Verwenden Sie den bedingten Azure-Zugriff, um die Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.

- [Konfigurieren des bedingten Zugriffs, um den Zugriff auf Azure Resource Manager zu blockieren](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-threat-detection"></a>Protokollierung und Bedrohungserkennung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Protokollierung und Bedrohungserkennung](/azure/security/benchmarks/security-controls-v2-logging-threat-detection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Aktivieren der Bedrohungserkennung für die Identitäts- und Zugriffsverwaltung in Azure

**Leitfaden**: App Configuration integriert sich in Azure Active Directory (Azure AD). Hierdurch werden die folgenden Benutzerprotokolle bereitgestellt, die Sie in der Azure AD-Berichterstellung anzeigen oder mit Azure Monitor, Azure Sentinel oder anderen SIEM- und Überwachungstools integrieren können, um komplexere Anwendungsfälle für Überwachung und Analyse zu erhalten:
- Anmeldungen: Der Bericht „Anmeldungen“ enthält Informationen zur Nutzung von verwalteten Anwendungen und Aktivitäten der Benutzeranmeldung.
- Überwachungsprotokolle: Ermöglichen die Nachverfolgung sämtlicher Änderungen, die von verschiedenen Features in Azure AD vorgenommen wurden. Hierzu zählen unter anderem Änderungen an Ressourcen in Azure AD, z. B. das Hinzufügen oder Entfernen von Benutzern, Apps, Gruppen, Rollen und Richtlinien.
- Riskante Anmeldungen: Eine riskante Anmeldung ist ein Indikator für einen Anmeldeversuch von einem Benutzer, der nicht der rechtmäßige Besitzer eines Benutzerkontos ist.
- Benutzer mit Risikomarkierung: Ein Benutzer mit Risikomarkierung ist ein Indikator für ein ggf. kompromittiertes Benutzerkonto.

Auch Azure Security Center kann bestimmte verdächtige Aktivitäten melden, wie z. B. eine übermäßige Anzahl fehlgeschlagener Authentifizierungsversuche oder veraltete Konten im Abonnement. Zusätzlich zur grundlegenden Sicherheitsüberwachung kann das Threat Protection-Modul von Azure Security Center auch ausführlichere Sicherheitswarnungen von einzelnen Azure-Dienstebenen sammeln. Diese Funktion bietet Ihnen Einblick in Kontoanomalien innerhalb der einzelnen Ressourcen.

Eine weitere Methode zum Erlangen des Zugriffs auf ihre App Configuration-Konfigurationsressource ist die Verwendung von Zugriffsschlüsseln. Diese müssen regelmäßig rotiert werden, um sicherzustellen, dass nicht autorisierte Agents Zugriff auf ihre Konfigurationsressource erhalten. Die Rotation kann direkt im Portal unter „Zugriffsschlüssel“ erfolgen.

- [So gewähren Sie Azure App Configuration Zugriff auf andere durch Azure AD geschützte Ressourcen mithilfe einer verwalteten Entität](overview-managed-identity.md)

- [Verwenden verwalteter Identitäten mit Azure App Configuration](howto-integrate-azure-managed-service-identity.md)

- [Berichte zu Überwachungsaktivitäten in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Aktivieren von Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Autorisieren des Zugriffs auf Azure App Configuration mittels Azure AD](concept-enable-rbac.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Aktivieren der Protokollierung für Azure-Netzwerkaktivitäten

**Leitfaden**: Azure App Configuration stellt keine Ressourcen direkt in einem virtuellen Netzwerk bereit. App Configuration gestattet Ihnen aber die Verwendung privater Endpunkte, um eine sichere Verbindung mit Azure App Configuration aus einem virtuellen Netzwerk heraus herzustellen. Azure App Configuration produziert oder verarbeitet auch keine DNS-Abfrageprotokolle, die aktiviert werden müssten.

Aktivieren Sie die Protokollierung für Ihre konfigurierten privaten App Configuration-Endpunkte, um Folgendes zu erfassen:
- Die vom privaten Endpunkt verarbeiteten Daten (ein-/ausgehend)
- Die vom Private Link-Dienst verarbeiteten Daten (ein-/ausgehend)
- NAT-Portverfügbarkeit

Weitere Informationen finden Sie in den folgenden Referenzen:

- [Überwachung von Azure Private Link](../private-link/private-link-overview.md#logging-and-monitoring)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Aktivieren der Protokollierung für Azure-Ressourcen

**Leitfaden**: Aktivitätsprotokolle, die automatisch verfügbar sind, enthalten alle Schreibvorgänge (PUT, POST, DELETE) für Ihre App Configuration-Ressourcen mit Ausnahme von Lesevorgängen (GET). Mit Aktivitätsprotokollen können Fehler ermittelt werden, und es kann nachverfolgt werden, welche Änderungen an einer Ressource ein Benutzer in Ihrer Organisation vorgenommen hat. Bei App Configuration sind Aktivitätsprotokolle nur auf Steuerungsebene verfügbar und werden durch den Azure Resource Manager (ARM) zur Verfügung gestellt. Die Protokollierung der kundenseitigen Datenebene für App Configuration wird zurzeit nicht unterstützt. Azure-Ressourcenprotokolle können ebenfalls nicht konfiguriert werden.

- [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Grundlegendes zur Protokollierung und zu verschiedenen Protokolltypen in Azure](../azure-monitor/platform/platform-logs-overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Zentralisieren der Verwaltung und Analyse von Sicherheitsprotokollen

**Leitfaden**: Zentralisieren Sie die Speicherung und Analyse von Protokollen, um eine Korrelation zu ermöglichen. Stellen Sie sicher, dass jeder Protokollquelle ein Datenbesitzer, eine Zugriffsanleitung, ein Speicherort, die für die Verarbeitung und den Zugriff verwendeten Tools sowie Anforderungen zur Datenaufbewahrung zugewiesen werden.

Stellen Sie sicher, dass die Azure-Aktivitätsprotokolle in die zentrale Protokollierung integriert werden. Erfassen von Protokollen über Azure Monitor zum Aggregieren von Sicherheitsdaten, die von Endpunktgeräten, Netzwerkressourcen und anderen Sicherheitssystemen generiert werden. Verwenden Sie in Azure Monitor Log Analytics-Arbeitsbereiche, um Analysen abzufragen und auszuführen, und verwenden Sie Azure Storage-Konten für langfristige Speicherung und Archivierung.

Zusätzlich können Sie auch Daten in Azure Sentinel oder der SIEM-Lösung eines Drittanbieters aktivieren und integrieren. Viele Unternehmen verwenden Azure Sentinel für „heiße“ Daten, die häufig verwendet werden, und Azure Storage für seltener verwendete „kalte“ Daten.

- [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurieren der Aufbewahrungsdauer von Protokollen im Speicher

**Leitfaden**: Stellen Sie sicher, dass für die Speicherkonten oder Log Analytics-Arbeitsbereiche zum Speichern der App Configuration-Protokolle der Protokollaufbewahrungszeitraum gemäß den Konformitätsbestimmungen Ihrer Organisation festgelegt ist. Verwenden Sie für langfristige Speicherungen und Archivierungen Konten von Azure Storage, Data Lake oder des Log Analytics-Arbeitsbereichs.

In Azure Monitor können Sie den Aufbewahrungszeitraum des Log Analytics-Arbeitsbereichs gemäß den Compliancevorschriften Ihres Unternehmens festlegen.

- [Verwalten von Nutzung und Kosten mit Azure Monitor-Protokollen](../azure-monitor/platform/manage-cost-storage.md)

- [Azure-Ressourcenprotokolle](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Reaktion auf Vorfälle](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Vorbereitung – Aktualisieren des Prozesses zur Reaktion auf Vorfälle für Azure

**Leitfaden**: Stellen Sie sicher, dass Ihre Organisation über Prozesse verfügt, um auf Sicherheitsvorfälle zu reagieren, dass diese Prozesse für Azure aktualisiert wurden und dass sie regelmäßig trainiert werden, um die Bereitschaft zu gewährleisten.

- [Implement security across the enterprise environment](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) (Implementieren von Sicherheit in der gesamten Unternehmensumgebung)

- [Referenzleitfaden für die Reaktion auf Vorfälle](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Vorbereitung – Einrichten von Ereignisbenachrichtigungen

**Leitfaden**: Richten Sie Kontaktinformationen für Sicherheitsvorfälle im Azure Security Center ein. Microsoft wendet sich unter den angegebenen Kontaktdaten an Sie, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf Ihre Daten zugegriffen haben. Sie haben auch die Möglichkeit, die Warnungen und Benachrichtigungen bei Vorfällen in verschiedenen Azure-Diensten entsprechend Ihrer Anforderungen bezüglich der Reaktion auf Vorfälle anzupassen. 

- [Festlegen der Kontaktinformationen in Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Erkennung und Analyse – Erstellen von Vorfällen basierend auf Warnungen mit hoher Qualität

**Leitfaden**: Stellen Sie sicher, dass Sie über einen Prozess zum Erstellen hochwertiger Warnungen und zum Messen der Qualität von Warnungen verfügen. Auf diese Weise können Sie Lehren aus vergangenen Vorfällen ziehen und Warnungen für Analysten priorisieren, damit diese keine Zeit mit falsch-positiven Ergebnissen verschwenden. 

Qualitativ hochwertige Warnungen können auf der Grundlage von Erfahrungen aus früheren Vorfällen, validierten Communityquellen und Tools zur Generierung und Bereinigung von Warnmeldungen durch Verschmelzung und Korrelation verschiedener Signalquellen erstellt werden. 

Azure Security Center bietet qualitativ hochwertige Warnungen für viele Azure-Ressourcen. Sie können den ASC-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen. Mit Azure Sentinel können Sie erweiterte Warnregeln erstellen, um Vorfälle automatisch für eine Untersuchung zu generieren. 

Exportieren Sie Ihre Azure Security Center-Warnungen und -Empfehlungen über das Exportfeature, um Risiken für Azure-Ressourcen zu ermitteln. Exportieren Sie Warnungen und Empfehlungen entweder manuell oder fortlaufend, kontinuierlich.

- [Konfigurieren des Exports](../security-center/continuous-export.md)

- [Streamen von Warnungen in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Erkennung und Analyse – Untersuchen eines Vorfalls

**Leitfaden**: Stellen Sie sicher, dass Analysten bei der Untersuchung potenzieller Vorfälle verschiedene Datenquellen abfragen und verwenden können, um eine vollständige Übersicht über das Geschehnis zu erhalten. Es empfiehlt sich, verschiedene Protokolle zu sammeln, um die Aktivitäten eines potenziellen Angreifers über das gesamte Kill Chain-Spektrum hinweg nachzuverfolgen und Schwachpunkte zu vermeiden.  Stellen Sie außerdem sicher, dass Erkenntnisse und Erfahrungen für andere Analysten und als zukünftige Referenzen erfasst werden.  

Zu den zu untersuchenden Datenquellen gehören die zentralisierten Protokollierungsquellen, die bereits von den zum Umfang gehörige Diensten und ausgeführten Systemen gesammelt werden, aber auch andere:

- Netzwerkdaten: Verwenden Sie die Datenflussprotokolle von Netzwerksicherheitsgruppen sowie Azure Network Watcher und Azure Monitor, um Netzwerkflussprotokolle und andere Analyseinformationen zu erfassen. 

- Momentaufnahmen von laufenden Systemen: 

    - Verwenden Sie die Momentaufnahmenfunktion des virtuellen Azure-Computers, um eine Momentaufnahme der Festplatte des laufenden Systems zu erstellen. 

    - Verwenden Sie die native Speicherabbildfunktion des Betriebssystems, um eine Momentaufnahme des Arbeitsspeichers des laufenden Systems zu erstellen.

    - Verwenden Sie das Momentaufnahmenfeature der Azure-Dienste oder die Funktion Ihrer Software, um Momentaufnahmen der laufenden Systeme zu erstellen.

Azure Sentinel bietet umfangreiche Datenanalysen über praktisch jede Protokollquelle sowie ein Fallverwaltungsportal zum Verwalten des gesamten Lebenszyklus von Vorfällen. Intelligenceinformationen während einer Untersuchung können zu Verfolgungs- und Berichtszwecken mit einem Vorfall verknüpft werden. 

- [Momentaufnahme des Datenträgers eines Windows-Computers](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Momentaufnahme des Datenträgers eines Linux-Computers](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure-Supportdiagnoseinformationen und Speicherabbilderfassung](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- Lesen Sie [Untersuchen von Incidents mit Azure Sentinel](../sentinel/tutorial-investigate-cases.md).

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Erkennung und Analyse – Priorisieren von Vorfällen

**Leitfaden**: Stellen Sie den Analysten Kontext bereit, auf welche Vorfälle sie sich zuerst konzentrieren sollten, je nach Schweregrad und Ressourcensensitivität. 

Azure Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, der oder die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

Markieren Sie Ressourcen außerdem mithilfe von Tags, und erstellen Sie ein Benennungssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren – insbesondere solche, von denen vertrauliche Daten verarbeitet werden.  Die Priorisierung der Behebung von Warnungen basierend auf der Wichtigkeit der Azure-Ressourcen und der Umgebung, in der der Vorfall aufgetreten ist, liegt in Ihrer Verantwortung.

- [Sicherheitswarnungen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Eindämmung, Ausmerzung und Wiederherstellung – Automatisieren der Behandlung von Vorfällen

**Leitfaden**: Automatisieren Sie manuelle, sich wiederholende Aufgaben, um die Antwortzeit zu beschleunigen und die Belastung der Analysten zu verringern. Manuelle Aufgaben dauern länger, verlangsamen jeden Vorfall und reduzieren die Anzahl der Vorfälle, die ein Analyst bewältigen kann. Manuelle Aufgaben erhöhen auch die Ermüdung der Analytiker. Dadurch erhöht sich das Risiko menschlicher Fehler, die zu Verzögerungen führen, und es verschlechtert sich die Fähigkeit der Analytiker, sich effektiv auf komplexe Aufgaben zu konzentrieren. Verwenden Sie die Workflowautomatisierungsfeatures in Azure Security Center und Azure Sentinel, um automatisch Aktionen auszulösen oder ein Playbook auszuführen, um auf eingehende Sicherheitswarnungen zu reagieren. Das Playbook führt Aktionen aus, wie das Versenden von Benachrichtigungen, das Deaktivieren von Konten und das Isolieren problematischer Netzwerke. 

- [Konfigurieren der Workflowautomatisierung in Security Center](../security-center/workflow-automation.md)

- [Einrichten automatisierter Reaktionen auf Bedrohungen in Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- Machen Sie sich mit dem [Einrichten automatisierter Reaktionen auf Bedrohungen in Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md) vertraut.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="posture-and-vulnerability-management"></a>Status- und Sicherheitsrisikoverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Status- und Sicherheitsrisikoverwaltung](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Einrichten sicherer Konfigurationen für Azure-Dienste 

**Leitfaden**: Azure App Configuration unterstützt die folgenden dienstspezifischen Richtlinien, die in Azure Security Center zur Überwachung und Durchsetzung von Konfigurationen Ihrer Azure-Ressourcen verfügbar sind. Die Konfiguration kann in Azure Security Center oder über Azure Policy-Initiativen erfolgen.
- App Configuration sollte einen kundenseitig verwalteten Schlüssel verwenden: Kundenseitig verwaltete Schlüssel bieten einen verbesserten Datenschutz, da Sie so Ihre Verschlüsselungsschlüssel verwalten können. Dies ist häufig zur Einhaltung von Compliancevorgaben erforderlich.
- App Configuration sollte eine private Verbindung verwenden: Private Endpunktverbindungen ermöglichen Clients in einem virtuellen Netzwerk den sicheren Zugriff auf Azure App Configuration über einen privaten Link.

Sie können Azure Blueprints verwenden, um die Bereitstellung und Konfiguration von Diensten und Anwendungsumgebungen wie Azure Resource Manager-Vorlagen, Azure RBAC-Steuerelementen und Richtlinien in einer einzigen Blaupausendefinition zu automatisieren.

- [Weitere Informationen zu App Configuration-Richtlinien](../governance/policy/samples/built-in-policies.md#app-configuration)

- [Arbeiten mit Sicherheitsrichtlinien in Azure Security Center](../security-center/tutorial-security-policy.md)

- [Abbildung der Implementierung von Schutzmaßnahmen in der Landing Zone auf Unternehmensebene](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint](../governance/blueprints/overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Aufrechterhalten sicherer Konfigurationen für Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Security Center, um Ihre Konfigurationsbaseline zu überwachen und die Verwendung von Azure Policy zu erzwingen. Azure Policy für App Configuration umfasst: 
- App Configuration sollte einen kundenseitig verwalteten Schlüssel verwenden: Kundenseitig verwaltete Schlüssel bieten einen verbesserten Datenschutz, da Sie so Ihre Verschlüsselungsschlüssel verwalten können. Dies ist häufig zur Einhaltung von Compliancevorgaben erforderlich.
- App Configuration sollte eine private Verbindung verwenden: Private Endpunktverbindungen ermöglichen Clients in einem virtuellen Netzwerk den sicheren Zugriff auf Azure App Configuration über einen privaten Link.

- [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md) 

- [Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Regelmäßiges Durchführen einer Angriffssimulation

**Leitfaden**: Führen Sie nach Bedarf Penetrationstests oder Red Team-Aktivitäten für Ihre Azure-Ressourcen durch, und stellen Sie sicher, dass alle kritischen Sicherheitsergebnisse behandelt werden.
Befolgen Sie die Einsatzregeln für Penetrationstests für die Microsoft Cloud, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen. Nutzen Sie die Microsoft-Strategie und Durchführung von Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastruktur, Dienste und Anwendungen.

- [Penetrationstests in Azure](../security/fundamentals/pen-testing.md)

- [Penetrationstests – Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="backup-and-recovery"></a>Sicherung und Wiederherstellung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Sicherung und Wiederherstellung](../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: Minimieren der Auswirkungen verlorener Schlüssel

**Leitfaden**: Stellen Sie sicher, dass Sie über Maßnahmen verfügen, um den Verlust von Schlüsseln zu verhindern bzw. sich von den daraus resultierenden Folgen zu erholen. Aktivieren Sie vorläufiges Löschen und den Löschschutz in Azure Key Vault, um Schlüssel vor versehentlichem oder böswilligem Löschen zu schützen.

- [Aktivieren des vorläufigen Löschens und des Löschschutzes in Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="governance-and-strategy"></a>Governance und Strategie

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Governance und Strategie](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definieren der Strategie für Ressourcenverwaltung und Datenschutz 

**Leitfaden**: Stellen Sie sicher, dass Sie eine klare Strategie für die kontinuierliche Überwachung und den Schutz von Systemen und Daten dokumentieren und kommunizieren. Priorisieren Sie die Ermittlung, die Bewertung, den Schutz und die Überwachung unternehmenskritischer Daten und Systeme. 

Diese Strategie sollte dokumentierte Anleitungen, Richtlinien und Standards für die folgenden Elemente umfassen: 

-   Datenklassifizierungsstandard in Übereinstimmung mit den Geschäftsrisiken

-   Sicherheitsorganisationseinblick in Risiken und Ressourcenbestand 

-   Genehmigung durch die Sicherheitsorganisation für die Nutzung der Azure-Dienste 

-   Sicherheit von Ressourcen durch ihren gesamten Lebenszyklus

-   Erforderliche Zugriffssteuerungsstrategie in Übereinstimmung mit der Organisationsdatenklassifizierung

-   Verwendung von nativen Azure- und Drittanbieterfunktionen für den Datenschutz

-   Datenverschlüsselungsanforderungen für Anwendungsfälle während der Übertragung und für ruhende Anwendungsfälle

-   Geeignete Verschlüsselungsstandards

Weitere Informationen finden Sie in den folgenden Referenzen:
- [Empfehlung für eine Azure-Sicherheitsarchitektur: Speicher, Daten, Verschlüsselung](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Grundlegende Azure-Sicherheitsinformationen: Sicherheit, Verschlüsselung und Speicherung von Azure-Daten](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework: Bewährte Methoden für Datensicherheit und Datenverschlüsselung in Azure](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure-Sicherheitsvergleichstest: Ressorcenverwaltung](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Azure-Sicherheitsvergleichstest: Datenschutz](/azure/security/benchmarks/security-controls-v2-data-protection)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definieren der Strategie für Unternehmenssegmentierung 

**Leitfaden**: Erstellen Sie eine unternehmensweite Strategie zum Segmentieren des Zugriffs auf Ressourcen durch eine Kombination aus Identität, Netzwerk, Anwendung, Abonnement, Verwaltungsgruppe und anderen Steuerelementen.

Wägen Sie die Notwendigkeit einer Sicherheitstrennung sorgfältig gegen die Notwendigkeit ab, den täglichen Betrieb der Systeme zu ermöglichen, die miteinander kommunizieren und auf Daten zugreifen müssen.

Stellen Sie sicher, dass die Segmentierungsstrategie einheitlich für alle Steuerelementtypen implementiert wird, einschließlich Netzwerksicherheit, Identitäts- und Zugriffsmodelle, Berechtigungs-/Zugriffsmodelle für Anwendungen sowie Steuerelemente für Benutzerprozesse.

- [Leitfaden für die Segmentierungsstrategie in Azure (Video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Leitfaden für die Segmentierungsstrategie in Azure (Dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Ausrichten der Netzwerksegmentierung an der Segmentierungsstrategie des Unternehmens](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definieren der Strategie für die Sicherheitsstatusverwaltung

**Leitfaden**: Führen Sie kontinuierliche Messungen durch, und Mindern Sie die Risiken für Ihre individuellen Ressourcen und die Umgebung, in der diese gehostet werden. Priorisieren Sie Ressourcen mit hohem Wert und hoch exponierte Angriffsflächen, wie z. B. veröffentlichte Anwendungen, Eingangs- und Ausgangspunkte für Netzwerke, Benutzer- und Administratorendpunkte usw.

- [Azure-Sicherheitsvergleichstest: Verwaltung von Status und Sicherheitsrisiken](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Ausrichten von Organisationsrollen, Zuständigkeiten und Verantwortlichkeiten

**Leitfaden**: Stellen Sie sicher, dass Sie eine klare Strategie für Rollen und Zuständigkeiten in ihrer Sicherheitsorganisation dokumentieren und kommunizieren. Priorisieren Sie durch Zuweisung klarer Verantwortlichkeiten für Sicherheitsentscheidungen, Schulung aller Beteiligten für das Modell der gemeinsamen Verantwortung und Schulung von technischen Teams hinsichtlich der Technologie zum Sichern der Cloud.

- [Azure Security Best Practice 1 – People: Educate Teams on Cloud Security Journey](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey) (Bewährte Methoden für die Sicherheit von Azure 1 – Personen: Schulen von Teams auf dem Weg zur Cloudsicherheit)

- [Azure Security Best Practice 2 – People: Educate Teams on Cloud Security Technology](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology) (Bewährte Methoden für die Sicherheit von Azure 1 – Personen: Schulen der Teams in Cloudsicherheitstechnologie)

- [Azure Security Best Practice 3 – Process: Assign Accountability for Cloud Security Decisions](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) (Bewährte Methoden für die Sicherheit von Azure 1 – Prozess: Zuweisen von Verantwortlichkeit für Cloudsicherheitsentscheidungen)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definieren einer Netzwerksicherheitsstrategie

**Leitfaden**: Richten Sie einen Ansatz für die Azure-Netzwerksicherheit im Rahmen der Gesamtstrategie Ihrer Organisation für die Sicherheitszugriffssteuerung ein.  

Diese Strategie sollte dokumentierte Anleitungen, Richtlinien und Standards für die folgenden Elemente umfassen: 

-   Zentralisieren der Verantwortlichkeit für Netzwerkverwaltung und -sicherheit

-   Segmentierungsmodell für virtuelle Netzwerke, das auf die Segmentierungsstrategie des Unternehmens abgestimmt ist

-   Wiederherstellungsstrategie in verschiedenen Bedrohungs- und Angriffsszenarien

-   Strategie für Internet-Randpunkte, -Eingangspunkte und -Ausgangspunkte

-   Strategie für Konnektivität zwischen Hybrid Cloud und lokalen Systemen

-   Aktuelle Netzwerksicherheitsartefakte (z. B. Netzwerkdiagramme, Referenznetzwerkarchitektur)

Weitere Informationen finden Sie in den folgenden Referenzen:
- [Azure Security Best Practice 11 – Architecture. Single unified security strategy](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy) (Bewährte Methoden für die Sicherheit von Azure 11 – Architektur: Zentralisierte einheitliche Sicherheitsstrategie)

- [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](/azure/security/benchmarks/security-controls-v2-network-security)

- [Die Netzwerksicherheit in Azure in der Übersicht](../security/fundamentals/network-overview.md)

- [Strategie für die Unternehmensnetzwerkarchitektur](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definieren der Strategie für Identität und privilegierten Zugriff

**Leitfaden**: Richten Sie einen Ansatz für Azure-Identität und privilegierten Zugriff im Rahmen der Gesamtstrategie Ihrer Organisation für die Sicherheitszugriffssteuerung ein.  

Diese Strategie sollte dokumentierte Anleitungen, Richtlinien und Standards für die folgenden Elemente umfassen: 

-   Zentralisiertes Identitäts- und Authentifizierungssystem mit Interkonnektivität zu anderen internen und externen Identitätssystemen

-   Starke Authentifizierungsmethoden in verschiedenen Anwendungsfällen und Bedingungen

-   Schutz stark privilegierter Benutzer

-   Überwachung und Behandlung von Anomalien bei Benutzeraktivitäten  

-   Benutzeridentität und Zugriffsüberprüfung und Abstimmungsprozess

Weitere Informationen finden Sie in den folgenden Referenzen:

- [Azure-Sicherheitsvergleichstest: Identitätsverwaltung](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure-Sicherheitsvergleichstest: Privilegierter Zugriff](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Azure Security Best Practice 11 – Architecture. Single unified security strategy](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy) (Bewährte Methoden für die Sicherheit von Azure 11 – Architektur: Zentralisierte einheitliche Sicherheitsstrategie)

- [Übersicht über die Sicherheit der Azure-Identitätsverwaltung](../security/fundamentals/identity-management-overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definieren einer Strategie für Protokollierung und Reaktion auf Bedrohungen

**Leitfaden**: Richten Sie eine Strategie für Protokollierung und Reaktion auf Bedrohungen zur schnellen Erkennung und Behebung von Bedrohungen bei gleichzeitiger Erfüllung von Complianceanforderungen ein. Priorisieren Sie, indem Sie Analysten qualitativ hochwertige Warnungen und nahtlose Umgebungen bereitstellen, sodass sie sich auf Bedrohungen konzentrieren können und sich nicht um Integration und manuelle Schritte kümmern müssen. 

Diese Strategie sollte dokumentierte Anleitungen, Richtlinien und Standards für die folgenden Elemente umfassen: 

-   Rolle und Verantwortlichkeiten der Organisation für Sicherheitsvorgänge (SecOps) 

-   Ein klar definierter Prozess zur Reaktion auf Vorfälle gemäß NIST oder einem anderen Branchenframework 

-   Erfassung und Aufbewahrung von Protokollen zur Unterstützung von Bedrohungserkennung, Reaktion auf Vorfälle und Complianceanforderungen

-   Zentralisierte Sichtbarkeit von und Korrelationsinformationen zu Bedrohungen unter Verwendung von SIEM, nativen Azure-Funktionen und anderen Quellen 

-   Kommunikations- und Benachrichtigungsplan mit Ihren Kunden, Lieferanten und öffentlichen Interessengruppen

-   Verwendung von nativen Azure-Plattformen und Plattformen von Drittanbietern für die Behandlung von Vorfällen, wie z. B. Protokollierung und Bedrohungserkennung, Forensik sowie Reduzierung und Bekämpfung von Angriffen

-   Prozesse für den Umgang mit Vorfällen und Aktivitäten nach Vorfällen, wie z. B. Erkenntnisse und Beweissicherung

Weitere Informationen finden Sie in den folgenden Referenzen:

- [Azure-Sicherheitsvergleichstest: Protokollierung und Bedrohungserkennung](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure-Sicherheitsvergleichstest: Reaktion auf Vorfälle](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Azure Security Best Practice 4 – Process. Update Incident Response Processes for Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) (Bewährte Methoden für die Sicherheit von Azure 4 – Prozess: Aktualisieren der Prozesse zur Reaktion auf Vorfälle für die Cloud)

- [Azure Adoption Framework, Entscheidungshilfe für Protokollierung und Berichterstattung](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure auf Unternehmensebene, Verwaltung und Überwachung](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die [Übersicht über Version 2 des Azure-Sicherheitsvergleichstests](../security/benchmarks/overview.md) an.
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).
---
title: Azure-Sicherheitsbaseline für Windows Virtual Desktop
description: Die Sicherheitsbaseline für Windows Virtual Desktop enthält Schrittanleitungen und Ressourcen für die Implementierung der Sicherheitsempfehlungen, die im Azure-Sicherheitsvergleichstest angegeben sind.
author: msmbaldwin
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 01cd79afe3e718a32663544a76a2a43353bc082e
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575380"
---
# <a name="azure-security-baseline-for-windows-virtual-desktop"></a>Azure-Sicherheitsbaseline für Windows Virtual Desktop

Diese Sicherheitsbaseline wendet Empfehlungen der [Version 2.0 des Vergleichstests für die Azure-Sicherheit](../security/benchmarks/overview.md) auf Windows Virtual Desktop an. Der Azure-Sicherheitsvergleichstest enthält Empfehlungen zum Schutz Ihrer Cloudlösungen in Azure. Der Inhalt ist nach den **Sicherheitskontrollen** gruppiert, die durch den Azure-Sicherheitsvergleichstest und die entsprechenden, für Windows Virtual Desktop geltenden Empfehlungen definiert werden. Nicht auf Windows Virtual Desktop anwendbare **Steuerungen** wurden ausgeschlossen.

Der Dienst Windows Virtual Desktop umfasst den Dienst selbst, Windows 10 Enterprise für die virtuelle SKU für mehrere Sitzungen sowie FSLogix. Informationen zu Sicherheitsempfehlungen im Zusammenhang mit FSLogix finden Sie in der [Azure-Sicherheitsbaseline für Azure Storage](../storage/common/security-baseline.md). Für den Dienst wird ein Agent auf virtuellen Computern ausgeführt, aber da die virtuellen Computer unter vollständiger Kontrolle des Kunden sind, befolgen Sie die [Azure-Sicherheitsbaseline für Azure Storage](../virtual-machines/windows/security-baseline.md).

Die gesamte Zuordnung zwischen Windows Virtual Desktop und dem Azure-Sicherheitsvergleichstest finden Sie in der [vollständigen Zuordnungsdatei der Windows Virtual Desktop-Sicherheitsbaseline](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: Implementieren der Sicherheit für internen Datenverkehr

**Leitfaden**: Sie müssen ein virtuelles Netzwerk erstellen oder ein vorhandenes verwenden, wenn Sie virtuelle Computer bereitstellen, die bei Windows Virtual Desktop registriert werden sollen. Stellen Sie sicher, dass alle virtuellen Azure-Netzwerke einem Prinzip der Unternehmenssegmentierung unterliegen, das sich den geschäftlichen Risiken anpasst. Jedes System, das ein höheres Risiko für das Unternehmen darstellen könnte, sollte innerhalb eines eigenen virtuellen Netzwerks isoliert und mit einer Netzwerksicherheitsgruppe oder Azure Firewall ausreichend geschützt werden.

Verwenden Sie die Features zur adaptiven Netzwerkhärtung in Azure Security Center, um Netzwerksicherheitsgruppen-Konfigurationen zu empfehlen, die Ports und Quell-IP-Adressen gemäß externer Regeln für den Netzwerk-Datenverkehr einschränken.

Beschränken oder ermöglichen Sie den Datenverkehr zwischen internen Ressourcen gemäß Ihren Anwendungen und der Strategie der Unternehmenssegmentierung auf der Basis von Netzwerksicherheitsgruppen-Regeln. Bei bestimmten klar definierten Anwendungen (z. B. einer App mit drei Ebenen) kann dies ein äußerst sicherer Ansatz sein (standardmäßig verweigern, als Ausnahme zulassen). Dies ist möglicherweise nicht gut skalierbar, wenn viele Anwendungen und Endpunkte miteinander interagieren. Sie können auch die Azure-Firewall verwenden, wenn für eine große Anzahl von Unternehmenssegmenten oder Spokes (in einer Hub/Spoke-Topologie) eine zentrale Verwaltung erforderlich ist.

Für die Netzwerksicherheitsgruppen, die Ihren VM-Subnetzen zugeordnet sind (die Teil von Windows Virtual Desktop sind), müssen Sie ausgehenden Datenverkehr zu bestimmten Endpunkten zulassen. 

- [Erfahren Sie, welche URLs für den Zugriff auf Windows Virtual Desktop erforderlich sind](safe-url-list.md)

- [Adaptive Netzwerkhärtung in Azure Security Center](../security-center/security-center-adaptive-network-hardening.md) 

- [Azure Firewall für Windows Virtual Desktop](../firewall/protect-windows-virtual-desktop.md)

- [Tutorial: Filtern von Netzwerkdatenverkehr mithilfe einer Netzwerksicherheitsgruppe über das Azure-Portal](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Bereitstellen und Konfigurieren von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="ns-2-connect-private-networks-together"></a>NS-2: Verbinden privater Netzwerke

**Leitfaden**: Stellen Sie mit Azure ExpressRoute oder dem virtuellen privaten Azure-Netzwerk private Verbindungen mit Azure-Rechenzentren und lokaler Infrastruktur in einer Housingumgebung her. ExpressRoute-Verbindungen werden nicht über das öffentliche Internet hergestellt, bieten mehr Zuverlässigkeit, eine höhere Geschwindigkeit und weniger Latenz als herkömmliche Internetverbindungen. 

Für virtuelle Point-to-Site- und Site-to-Site-Netzwerke können Sie lokale Geräte oder Netzwerke mit einem virtuellen Netzwerk verbinden, indem Sie eine beliebige Kombination dieser Optionen für virtuelle private Netzwerke und Azure ExpressRoute verwenden.

Um zwei oder mehr virtuelle Netzwerke in Azure miteinander zu verbinden, stellen Sie eine Peering-Verbindung her. Der Netzwerkdatenverkehr zwischen mittels Peering verbundenen virtuellen Netzwerken ist privat und wird im Azure-Backbone-Netzwerk verwaltet.

- [ExpressRoute-Konnektivitätsmodelle](../expressroute/expressroute-connectivity-models.md) 

- [Was ist VPN Gateway?](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [Peering von virtuellen Netzwerken](/azure/virtual-network/virtual-network-peering-overview)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Schützen von Anwendungen und Diensten vor externen Netzwerkangriffen.

**Leitfaden**: Schützen Sie mit der Azure-Firewall Anwendungen und Dienste vor potenziell schädlichem Datenverkehr aus dem Internet und von anderen externen Standorten. Schützen Sie Ihre Windows Virtual Desktop-Ressourcen vor Angriffen aus externen Netzwerken einschließlich verteilter Denial-of-Service-Angriffe, anwendungsspezifischer Angriffe und unerwünschtem und potenziell schädlichem Internetdatenverkehr. Schützen Sie Ihre Ressourcen durch Aktivieren des DDoS-Standardschutzes in Ihren virtuellen Azure-Netzwerken vor verteilten Denial-of-Service-Angriffen. Verwenden Sie Azure Security Center, um Risiken durch Fehlkonfigurationen zu erkennen, die mit Ihren netzwerkbezogenen Ressourcen zusammenhängen.

Windows Virtual Desktop ist nicht zum Ausführen von Webanwendungen gedacht und erfordert nicht, dass Sie zusätzliche Einstellungen konfigurieren oder zusätzliche Netzwerkdienste bereitstellen, um sie vor externen Netzwerkangriffen zu schützen, die auf Webanwendungen ausgerichtet sind.

- [Azure Firewall-Dokumentation](/azure/firewall)

- [Verwalten von Azure DDoS Protection Standard mithilfe des Azure-Portals](/azure/virtual-network/manage-ddos-protection) 

- [Azure Security Center-Empfehlungen](../security-center/recommendations-reference.md#networking-recommendations)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: Bereitstellen von Angriffserkennungs-/Eindringschutzsystemen (Intrusion Detection/Intrusion Prevention Systems, IDS/IPS)

**Leitfaden**: Verwenden Sie Azure Firewall mit Threat Intelligence-basierter Filterung, um bei bekannten böswilligen IP-Adressen und Domänen zu warnen und/oder Datenverkehr zu und von diesen Adressen oder Domänen optional zu blockieren. Die IP-Adressen und Domänen stammen aus dem Microsoft Threat Intelligence-Feed. Wenn eine Payloaduntersuchung erforderlich ist, können Sie die Lösung eines Drittanbieters zur Angriffserkennung oder -verhinderung vom Azure Marketplace aus bereitstellen. 

Wenn Sie eine gesetzliche oder sonstige Anforderung der Verwendung einer Lösung zur Angriffserkennung oder -verhinderung beachten müssen, stellen Sie sicher, dass sie immer auf hochwertige Warnungen für Ihre Lösung zur Sicherheits- und Ereignisverwaltung (Security Information and Eventmanagement, SIEM) eingestellt ist.

- [Bereitstellen von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md) 

- [Azure Marketplace umfasst IDS-Funktionen von Drittanbietern](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [ATP-EDR-Funktion von Microsoft Defender](/bs-cyrl-ba/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: Vereinfachen von Netzwerksicherheitsregeln

**Leitfaden**: Verwenden Sie Azure Virtual Network-Diensttags, um Netzwerkzugriffssteuerungen in Netzwerksicherheitsgruppen oder einer Azure Firewall zu definieren, die für Ihre Windows Virtual Desktop-Ressourcen konfiguriert sind. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Durch Angabe des Diensttagnamens (Beispiel: WindowsVirtualDesktop) im entsprechenden Quell- oder Zielfeld einer Regel können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

- [Grundlegendes zu Diensttags und Verwenden von Diensttags](../virtual-network/service-tags-overview.md)

- [Weitere Informationen zu den Funktionen des Windows Virtual Desktop-Diensttags finden Sie hier.](safe-url-list.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="identity-management"></a>Identitätsverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Identitätsverwaltung](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardisieren von Azure Active Directory als zentrales Identitäts- und Authentifizierungssystem

**Leitfaden**: Windows Virtual Desktop verwendet Azure Active Directory (Azure AD) als Standarddienst für die Identitäts- und Zugriffsverwaltung. Sie sollten Azure AD als Standard für die Identitäts- und Zugriffsverwaltung Ihrer Organisation verwenden:

- Microsoft-Cloudressourcen, wie z. B. das Azure-Portal, Azure Storage, Azure-VMs (Linux und Windows), Azure Key Vault, PaaS- und SaaS-Anwendungen.

- Die Ressourcen Ihrer Organisation, z. B. Anwendungen in Azure oder Ihre Unternehmensnetzwerkressourcen.

Die Sicherung von Azure AD sollte bei den Methoden Ihrer Organisation im Zusammenhang mit Cloudsicherheit eine hohe Priorität haben. Azure AD bietet eine Identitätssicherheitsbewertung, die Ihnen dabei hilft, den Identitätssicherheitsstatus in Bezug auf die Empfehlungen zu bewährten Methoden von Microsoft zu bewerten. Verwenden Sie die Bewertung, um zu beurteilen, wie gut Ihre Konfiguration den Empfehlungen zu bewährten Methoden entspricht, und um Ihren Sicherheitsstatus zu verbessern.

Azure AD unterstützt externe Identitäten, die Benutzern ohne Microsoft-Konto ermöglichen, sich mit ihrer externen Identität bei ihren Anwendungen und Ressourcen anzumelden.

- [Mandant in Azure AD](../active-directory/develop/single-and-multi-tenant-apps.md)

- [Verwenden externer Identitätsanbieter für eine Anwendung](/azure/active-directory/b2b/identity-providers)

- [Was ist die Identitätssicherheitsbewertung in Azure AD?](../active-directory/fundamentals/identity-secure-score.md)

- [Bestimmte Rollen, die für den Einsatz von Windows Virtual Desktop erforderlich sind](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Sicheres und automatisches Verwalten von Anwendungsidentitäten

**Leitfaden**: Windows Virtual Desktop unterstützt von Azure verwaltete Identitäten für nicht für Personen bestimmte Konten, z. B. für Dienste oder Automatisierung. Das Azure-Feature für verwaltete Identitäten sollte verwendet werden, anstatt ein leistungsfähigeres Benutzerkonto für den Zugriff auf Ihre Ressourcen oder deren Ausführung zu erstellen. 

Windows Virtual Desktop empfiehlt die Verwendung von Azure Active Directory (Azure AD) zum Erstellen eines Dienstprinzipals mit eingeschränkten Berechtigungen auf Ressourcenebene, um Dienstprinzipale mit Zertifikatanmeldeinformationen zu konfigurieren und auf geheime Clientschlüssel zurückzugreifen. In beiden Fällen kann Azure Key Vault in Verbindung mit verwalteten Azure-Identitäten verwendet werden, damit die Laufzeitumgebung (z. B. eine Azure-Funktion) die Anmeldeinformationen aus dem Schlüsseltresor abrufen kann.

- [Dienste, die verwaltete Identitäten für Azure-Ressourcen unterstützen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Azure-Dienstprinzipal](/powershell/azure/create-azure-service-principal-azureps) 

- [Erstellen eines Dienstprinzipals mit Zertifikaten](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [Verwenden von Azure Key Vault für die Registrierung von Sicherheitsprinzipalen](../key-vault/general/authentication.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Verwenden von Azure AD Single Sign-on (SSO) für den Anwendungszugriff

**Leitfaden**: Windows Virtual Desktop verwendet Azure Active Directory (Azure AD), um die Identitäts- und Zugriffsverwaltung für Azure-Ressourcen, Cloudanwendungen und lokale Anwendungen bereitzustellen. Dies umfasst sowohl Unternehmensidentitäten wie Mitarbeiter als auch externe Identitäten wie Partner, Anbieter und Zulieferer. Dies ermöglicht SSO (Single Sign-On, einmaliges Anmelden) die Verwaltung und den Schutz des Zugriffs auf die Daten und Ressourcen Ihrer Organisation, die lokal und in der Cloud gespeichert sind. Verbinden Sie alle Ihre Benutzer, Anwendungen und Geräte mit Azure AD für einen nahtlosen, sicheren Zugriff mit mehr Transparenz und Kontrolle.

- [Grundlegendes zu Anwendungs-SSO mit Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Verwenden stärkerer Authentifizierungssteuerungen für den gesamten Azure Active Directory-basierten Zugriff 

**Leitfaden**: Windows Virtual Desktop verwendet Azure Active Directory (Azure AD), das starke Authentifizierungssteuerungen über mehrstufige Authentifizierung und starke kennwortlose Methoden unterstützt.

- Mehrstufige Authentifizierung: Aktivieren Sie die mehrstufige Azure AD-Authentifizierung, und befolgen Sie die Empfehlungen zur Identitäts- und Zugriffsverwaltung von Azure Security Center in Bezug auf einige bewährte Methoden für Ihr Setup der mehrstufigen Authentifizierung. Die mehrstufige Authentifizierung kann für alle Benutzer, für ausgewählte Benutzer oder auf Benutzerebene auf der Grundlage von Anmeldebedingungen und Risikofaktoren erzwungen werden.

- Kennwortlose Authentifizierung: Es stehen drei Optionen für die kennwortlose Authentifizierung zur Verfügung: Windows Hello for Business, die Microsoft Authenticator-App und lokale Authentifizierungsmethoden wie Smartcards.

Windows Virtual Desktop unterstützt veraltete Authentifizierungen auf Kennwortbasis wie reine Cloudkonten (Benutzerkonten, die direkt in Azure erstellt werden), die über eine Baseline-Kennwortrichtlinie verfügen, oder Hybridkonten (Benutzerkonten von lokalem Active Directory, die den lokalen Kennwortrichtlinien folgen). Bei der Verwendung der kennwortbasierten Authentifizierung bietet Azure AD eine Kennwortschutzfunktion, die Benutzer daran hindert, leicht zu erratende Kennwörter festzulegen. Microsoft stellt eine globale Liste verbotener Kennwörter zur Verfügung, die auf der Grundlage von Telemetrie aktualisiert wird, und Kunden können die Liste entsprechend ihren Bedürfnissen erweitern (z. B. um Markennamen, kulturelle Verweise usw.). Dieser Kennwortschutz kann für reine Cloud- und Hybridkonten verwendet werden.

Die Authentifizierung allein auf der Grundlage von Kennwortanmeldeinformationen ist anfällig für beliebte Angriffsmethoden. Für höhere Sicherheit verwenden Sie starke Authentifizierungsmethoden wie mehrstufige Authentifizierung und eine starke Kennwortrichtlinie. Bei Anwendungen von Drittanbietern und Marketplace-Dienste, die möglicherweise über Standardkennwörter verfügen, sollten Sie diese während der ersten Einrichtung des Dienstes ändern.

Stellen Sie für Administratoren und privilegierte Benutzer sicher, dass die höchste Stufe der starken Authentifizierungsmethode verwendet wird, und führen Sie für andere Benutzer eine geeignete starke Authentifizierungsrichtlinie ein.

- [Einführung in Optionen für die kennwortlose Authentifizierung für Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Azure AD-Standardkennwortrichtlinie](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts) 

- [Ausschließen von ungeeigneten Kennwörtern mit dem Azure Active Directory-Kennwortschutz](../active-directory/authentication/concept-password-ban-bad.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Überwachen und Warnen bei Kontoanomalien

**Leitfaden**: Windows Virtual Desktop ist in Azure Active Directory (Azure AD) integriert, das folgende Datenquellen bereitstellt:

- Anmeldungen: Der Bericht zu Anmeldungen enthält Informationen zur Nutzung von verwalteten Anwendungen und Aktivitäten der Benutzeranmeldung.

- Überwachungsprotokolle: Ermöglichen die Nachverfolgung sämtlicher Änderungen, die von verschiedenen Features in Azure AD vorgenommen wurden. Hierzu zählen unter anderem Änderungen an Ressourcen in Azure AD, z. B. das Hinzufügen oder Entfernen von Benutzern, Apps, Gruppen, Rollen und Richtlinien.

- Riskante Anmeldungen: Eine riskante Anmeldung ist ein Hinweis auf einen Anmeldeversuch einer Person, die nicht der rechtmäßige Besitzer eines Benutzerkontos ist.

- Benutzer mit Risikomarkierung: Ein Benutzer mit Risikomarkierung ist ein Indikator für ein ggf. kompromittiertes Benutzerkonto.

Diese Datenquellen können in Azure Monitor, Azure Sentinel oder SIEM-Systeme (Security Information und Event Management) von Drittanbietern integriert sein. Auch Azure Security Center kann bestimmte verdächtige Aktivitäten melden, wie z. B. eine übermäßige Anzahl fehlgeschlagener Authentifizierungsversuche oder veraltete Konten im Abonnement. Azure Advanced Threat Protection (ATP) ist eine Sicherheitslösung, die Active Directory-Signale nutzen kann, um komplexe Bedrohungen, potenziell gefährdete Identitäten und schädliche Aktionen von Insidern zu identifizieren, zu erkennen und zu untersuchen.

- [Berichte zu Überwachungsaktivitäten in Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Anzeigen riskanter Azure AD-Anmeldungen](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Warnungen im Threat Intelligence-Schutzmodul von Azure Security Center](../security-center/alerts-reference.md)

- [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Einschränken des Zugriffs auf Azure-Ressourcen basierend auf Bedingungen

**Leitfaden**: Windows Virtual Desktop unterstützt bedingten Zugriff mit Azure Active Directory (Azure AD) für eine differenziertere Zugriffssteuerung auf Grundlage benutzerdefinierter Bedingungen. So könnte z. B. erforderlich sein, dass Benutzeranmeldungen aus bestimmten IP-Bereichen für den Zugriff die mehrstufige Authentifizierung verwenden. 

Außerdem kann die Sitzungsverwaltungsrichtlinie für die differenzierte Authentifizierung ebenfalls für verschiedene Anwendungsfälle verwendet werden.

- [Übersicht über den bedingten Zugriff in Azure](../active-directory/conditional-access/overview.md) 

- [Allgemeine Richtlinien für bedingten Zugriff](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Konfigurieren der Verwaltung von Authentifizierungssitzungen mit bedingtem Zugriff](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md) 

- [Informationen zum Einrichten des speziellen bedingten Zugriffs für Windows Virtual Desktop finden Sie hier](set-up-mfa.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="privileged-access"></a>Privilegierter Zugriff

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Privilegierter Zugriff](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Einschränken des administrativen Zugriffs auf unternehmenskritische Systeme

**Leitfaden**: Windows Virtual Desktop verwendet die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC), um den Zugriff auf unternehmenskritische Systeme zu isolieren. Stellen Sie sicher, dass Sie auch den Zugriff auf die Verwaltungs-, Identitäts- und Sicherheitssysteme einschränken, die über Administratorzugriff auf Ihre unternehmenskritischen Ressourcen verfügen, z. B. Active Directory-Domänencontroller, Sicherheitstools und Systemverwaltungstools mit Agents, die auf unternehmenskritischen Systemen installiert sind. Angreifer, die diese Verwaltungs- und Sicherheitssysteme kompromittieren, können diese potenziell sofort ausnutzen, um geschäftskritische Ressourcen zu gefährden.

Alle Zugriffssteuerungstypen sollten an der Segmentierungsstrategie des Unternehmens ausgerichtet werden, um eine konsistente Zugriffssteuerung sicherzustellen.

- [Azure-Komponenten und -Referenzmodell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Zugriff auf die Verwaltungsgruppe](../governance/management-groups/overview.md#management-group-access) 

- [Azure-Abonnementadministratoren](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Mindestens erforderliche Administratorberechtigungen für die Verwaltung von Windows Virtual Desktop](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Windows Virtual Desktop verwendet Azure AD-Konten zum Verwalten von Ressourcen sowie zum regelmäßigen Überprüfen von Benutzerkonten und Zugriffszuweisungen, um die Gültigkeit der Konten und zugehörigen Zugriffsberechtigungen sicherzustellen.

Überprüfen Sie mit Azure AD-Zugriffsüberprüfungen Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen. Azure AD-Berichte können Protokolle zum Ermitteln von veralteten Konten bereitstellen.

Darüber hinaus kann Azure Privileged Identity Management auch so konfiguriert werden, dass eine Warnung gesendet wird, wenn übermäßig viele Administratorkonten erstellt werden, und veraltete oder falsch konfigurierte Administratorkonten ermittelt werden.

Einige Azure-Dienste unterstützen lokale Benutzer und Rollen, die nicht über Azure AD verwaltet werden. Sie müssen diese Benutzer separat verwalten.

- [Integrierte Rollen für Windows Virtual Desktop](rbac.md)

- [Erstellen einer Zugriffsüberprüfung für Azure-Ressourcenrollen in Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Was sind Azure AD-Zugriffsüberprüfungen?](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Einrichten des Notfallzugriffs in Azure AD

**Leitfaden**: Windows Virtual Desktop verwendet Azure AD zur Ressourcenverwaltung. Um zu verhindern, dass Sie versehentlich aus Ihrer Azure AD-Organisation ausgesperrt werden, richten Sie ein Konto für den Notfallzugriff ein, für den Fall, dass normale Verwaltungskonten nicht verwendet werden können. Konten für den Notfallzugriff verfügen normalerweise über umfangreiche Berechtigungen und sollten keinen Einzelpersonen zugewiesen werden. Konten für den Notfallzugriff sind auf Notfallsituationen oder Szenarien beschränkt, in denen normale Administratorkonten nicht verwendet werden können.

Sie sollten sicherstellen, dass die Anmeldeinformationen (z. B. Kennwort, Zertifikat oder Smartcard) für Konten für den Notfallzugriff sicher aufbewahrt werden und nur den Personen bekannt sind, die für deren Verwendung im Notfall autorisiert sind.

- [Verwalten von Konten für den Notfallzugriff in Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="pa-5-automate-entitlement-management"></a>PA-5: Automatisieren der Berechtigungsverwaltung 

**Leitfaden**: Windows Virtual Desktop ist zur Ressourcenverwaltung in Azure AD integriert. Verwenden Sie die Berechtigungsverwaltungsfeatures von Azure AD, um die Zugriffsanforderungsworkflows zu automatisieren, einschließlich Zugriffszuweisungen, Überprüfungen und Ablauf. Zwei- oder mehrstufige Genehmigungen werden auch unterstützt.

- [Was sind Azure AD-Zugriffsüberprüfungen?](../active-directory/governance/access-reviews-overview.md) 

- [Was ist die Azure AD-Berechtigungsverwaltung?](../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Verwenden von Privileged Access Workstations

**Leitfaden**: Geschützte und isolierte Arbeitsstationen sind von entscheidender Bedeutung für die Sicherheit sensibler Rollen wie Administratoren, Entwickler und Operator kritischer Dienste. Verwenden Sie stark gesicherte Benutzerworkstations und/oder Azure Bastion für administrative Aufgaben. 

Verwenden Sie Azure Active Directory (Azure AD), Microsoft Defender Advanced Threat Protection (ATP) oder Microsoft Intune, um eine sichere und verwaltete Benutzerworkstation für administrative Aufgaben einzurichten. Die gesicherten Workstations können zentral verwaltet werden, um eine gesicherte Konfiguration einschließlich starker Authentifizierung, Software- und Hardwarebaselines, eingeschränktem logischen und Netzwerkzugang durchzusetzen.

- [Informationen zu sicheren, von Azure verwalteten Arbeitsstationen](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Bereitstellen einer sicheren, von Azure verwalteten Arbeitsstation](/azure/active-directory/devices/howto-azure-managed-workstation)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Befolgen Sie die Prinzipien der Just Enough Administration (Prinzip der geringsten Rechte) 

**Leitfaden**: Windows Virtual Desktop ist zur Ressourcenverwaltung in die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) integriert. Mit Azure RBAC kann der Zugriff auf Azure-Ressourcen über Rollenzuweisungen verwaltet werden. Diese Rollen können Benutzern, Gruppen, Dienstprinzipalen und verwalteten Identitäten zugewiesen werden. Für bestimmte Ressourcen sind vordefinierte integrierte Rollen verfügbar. Diese Rollen können über Tools wie die Azure CLI, Azure PowerShell oder das Azure-Portal inventarisiert oder abgefragt werden. 

Die Berechtigungen, die Sie Ressourcen über Azure RBAC zuweisen, sollten immer auf die nötigsten Anforderungen der Rollen beschränkt sein. Dies ergänzt den JIT-Ansatz (Just-in-Time) von Privileged Identity Management (PIM) mit Azure Active Directory (Azure AD), und diese Berechtigungen sollten regelmäßig überprüft werden.

Verwenden Sie zudem integrierte Rollen, um Berechtigungen zuzuweisen, und erstellen Sie benutzerdefinierte Rollen nur bei Bedarf.

- [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?](../role-based-access-control/overview.md) 

- [Konfigurieren von RBAC in Azure](../role-based-access-control/role-assignments-portal.md) 

- [Was sind Azure AD-Zugriffsüberprüfungen?](../active-directory/governance/access-reviews-overview.md)

- [Integrierte Rollen für Windows Virtual Desktop](rbac.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: Auswählen des Genehmigungsprozesses für Microsoft-Support  

**Leitfaden**: In Supportszenarien, bei denen Microsoft auf Kundendaten zugreifen muss, unterstützt Windows Virtual Desktop Kunden-Lockbox, eine Oberfläche, auf der Sie Anforderungen nach Zugriff auf Kundendaten prüfen und dann genehmigen oder ablehnen können.

- [Grundlegendes zu Kunden-Lockbox](../security/fundamentals/customer-lockbox-overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="data-protection"></a>Datenschutz

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Schutz von Daten](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: Ermitteln, Klassifizieren und Bezeichnen von vertraulichen Daten

**Leitfaden**: Ermitteln, klassifizieren und bezeichnen Sie Ihre sensiblen Daten, sodass Sie die entsprechenden Steuerelemente entwerfen können. Dadurch wird sichergestellt, dass sensible Informationen von den Technologiesystemen der Organisation sicher gespeichert, verarbeitet und übertragen werden.

Verwenden Sie Azure Information Protection (und das zugehörige Überprüfungstool) für vertrauliche Informationen in Office-Dokumenten in Azure-, lokalen, Office 365- und anderen Speicherorten.

Sie können Azure SQL Information Protection verwenden, um die Klassifizierung und Bezeichnung von Informationen zu vereinfachen, die in Azure SQL-Datenbanken gespeichert sind.

- [Markieren vertraulicher Informationen mit Azure Information Protection](/azure/information-protection/what-is-information-protection) 

- [Implementieren von Azure SQL Data Discovery](/azure/sql-database/sql-database-data-discovery-and-classification)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Schützen von vertraulichen Daten

**Leitfaden**: Schützen Sie vertrauliche Daten, indem Sie den Zugriff mithilfe der rollenbasierten Zugriffssteuerung in Azure (Azure RBAC), der netzwerkbasierten Zugriffssteuerungen und bestimmter Steuerungen in Azure-Diensten (z. B. Verschlüsselung in SQL und anderen Datenbanken) einschränken.

Um eine konsistente Zugriffssteuerung sicherzustellen, sollten alle Zugriffssteuerungstypen an der Segmentierungsstrategie des Unternehmens ausgerichtet werden. Bei der Segmentierungsstrategie des Unternehmens sollte auch der Speicherort vertraulicher und unternehmenskritischer Daten und Systeme berücksichtigt werden.

Microsoft behandelt alle Kundeninhalte vertraulich und schützt sie vor Verlust und Offenlegung. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von Standard-Datenschutzsteuerungen und -funktionen implementiert.

- [Rollenbasierte Zugriffssteuerung in Azure (Role-Based Access Control, RBAC)](../role-based-access-control/overview.md) 

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Überwachung auf nicht autorisierte Übertragung vertraulicher Daten

**Leitfaden**: Führen Sie eine Überwachung auf nicht autorisierte Übertragung von Daten an Speicherorte außerhalb der Sichtbarkeit und Kontrolle des Unternehmens durch. Dies umfasst in der Regel die Überwachung anomaler Aktivitäten (große oder ungewöhnliche Übertragungen), die auf eine nicht autorisierte Datenexfiltration hindeuten können.

ATP-Features (Advanced Threat Protection) mit Azure Storage und Azure SQL ATP können bei einer anomalen Übertragung von Informationen eine Warnung ausgeben, die angibt, was möglicherweise nicht autorisierte Übertragungen vertraulicher Informationen sind.

Azure Information Protection (AIP) bietet Überwachungsfunktionen für Informationen, die klassifiziert und bezeichnet wurden.

Erzwingen Sie mit (z. B. hostbasierten) Lösungen zur Verhinderung von Datenverlust Erkennungs- und/oder präventive Steuerungsmaßnahmen, um Datenexfiltration zu verhindern.

- [Aktivieren von Azure SQL ATP](../azure-sql/database/threat-detection-overview.md) 

- [Aktivieren von Azure Storage ATP](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-security-center)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="asset-management"></a>Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Ressourcenverwaltung](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Sicherstellen, dass das Sicherheitsteam Risiken für Ressourcen einsehen kann

**Leitfaden**: Stellen Sie sicher, dass Sicherheitsteams die Berechtigungen „Sicherheitsleseberechtigter“ in Ihrem Azure-Mandanten und Ihren Abonnements erhalten, damit sie mit Azure Security Center Sicherheitsrisiken überwachen können. 

Abhängig davon, wie die Verantwortungsbereiche des Sicherheitsteams strukturiert sind, kann die Überwachung auf Sicherheitsrisiken unter die Verantwortung eines zentralen Sicherheitsteams oder eines lokalen Teams fallen. Aus diesem Grund müssen Sicherheitserkenntnisse und -risiken immer innerhalb einer Organisation zentral aggregiert werden. 

Die Berechtigungen der Rolle „Sicherheitsleseberechtigter“ können weit gefasst auf einen gesamten Mandanten (Stammverwaltungsgruppe) angewandt oder auf Verwaltungsgruppen oder bestimmte Abonnements beschränkt werden. 

Möglicherweise sind zusätzliche Berechtigungen erforderlich, um Einblick in Workloads und Dienste zu erhalten. 

- [Übersicht über die Rolle „Sicherheitsleseberechtigter“](../role-based-access-control/built-in-roles.md#security-reader)

- [Übersicht über Azure-Verwaltungsgruppen](../governance/management-groups/overview.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Sicherstellen, dass das Sicherheitsteam Zugriff auf den Ressourcenbestand und die Metadaten hat

**Leitfaden**: Wenden Sie Tags auf Ihre Ressourcen, Ressourcengruppen und Abonnements in Azure an, um sie logisch in einer Taxonomie zu strukturieren. Jedes Tag besteht aus einem Paar mit einem Namen und einem Wert. So können Sie beispielsweise den Namen „Umgebung“ und den Wert „Produktion“ auf alle Ressourcen in der Produktion anwenden.

Verwenden Sie Azure Virtual Machine Inventory, um die Erfassung von Informationen zur Software auf virtuellen Computern zu automatisieren. Softwarename, Version, Herausgeber und Aktualisierungszeit sind im Azure-Portal verfügbar. Um Zugriff auf das Installationsdatum und andere Informationen zu erhalten, aktivieren Sie die Diagnose auf Gastebene, und fügen Sie die Windows-Ereignisprotokolle einem Log Analytics-Arbeitsbereich hinzu.

- [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](../governance/resource-graph/first-query-portal.md) 

- [Verwalten des Ressourcenbestands in Azure Security Center](../security-center/asset-inventory.md) 

- [Leitfaden zur Entscheidungsfindung für Ressourcenbenennung und -markierung](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

- [Aktivieren des Bestands an virtuellen Azure-Computern](../automation/automation-tutorial-installed-software.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy, um die Dienste, die von Benutzern in Ihrer Umgebung bereitgestellt werden, zu überwachen und einzuschränken. Verwenden Sie Azure Resource Graph, um Ressourcen in ihren Abonnements abzufragen und zu ermitteln. Sie können auch mit Azure Monitor Regeln erstellen, mit denen Warnungen ausgelöst werden, wenn ein nicht genehmigter Dienst erkannt wird.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Gewährleisten der Sicherheit der bei der Lebenszyklusverwaltung von Ressourcen

**Leitfaden**: Nicht zutreffend Windows Virtual Desktop kann nicht verwendet werden, um die Sicherheit von Ressourcen in einem Lebenszyklusverwaltungsprozess sicherzustellen. Es liegt in der Verantwortung des Kunden, die Attribute und Netzwerkkonfigurationen von Ressourcen zu verwalten, die als besonders wichtig erachtet werden. 

Es wird empfohlen, dass der Kunde einen Prozess erstellt, um die Attribut- und Netzwerkkonfigurationsänderungen zu erfassen, die Auswirkungen der Änderung zu messen und ggf. Abhilfemaßnahmen zu treffen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden**: Verwenden Sie den bedingten Azure-Zugriff, um die Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.

- [Konfigurieren des bedingten Zugriffs, um den Zugriff auf Azure Resource Manager zu blockieren](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: Ausschließliche Verwendung genehmigter Anwendungen in Computeressourcen

**Leitfaden**: Verwenden Sie den Bestand virtueller Azure-Computer, um die Erfassung von Informationen zur gesamten Software auf virtuellen Computern zu automatisieren. Softwarename, Version, Herausgeber und Aktualisierungszeit sind im Azure-Portal verfügbar. Um Zugriff auf das Installationsdatum und andere Informationen zu erhalten, aktivieren Sie die Diagnose auf Gastebene, und fügen Sie die Windows-Ereignisprotokolle einem Log Analytics-Arbeitsbereich hinzu.

- [Aktivieren des Bestands an virtuellen Azure-Computern](../automation/automation-tutorial-installed-software.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-threat-detection"></a>Protokollierung und Bedrohungserkennung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Protokollierung und Bedrohungserkennung](/azure/security/benchmarks/security-controls-v2-logging-threat-detection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Aktivieren der Bedrohungserkennung für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die im Azure Security Center integrierte Funktion zur Erkennung von Bedrohungen und aktivieren Sie Azure Defender (ehemals Azure Advanced Threat Protection) für Ihre Windows Virtual Desktop-Ressourcen. Azure Defender für Windows Virtual Desktop ermöglicht die Nutzung intelligenter Sicherheitsfunktionen zur Erkennung von ungewöhnlichen und möglicherweise schädlichen Versuchen, auf Ihre Windows Virtual Desktop-Ressourcen zuzugreifen oder diese unbefugt zu nutzen.

Leiten Sie alle Protokolle von Windows Virtual Desktop an Ihre SIEM-Lösung (Security Information and Event Management) weiter, die zum Einrichten der benutzerdefinierten Erkennung von Bedrohungen verwendet werden kann. Stellen Sie sicher, dass Sie unterschiedliche Arten von Azure-Ressourcen nach potenziellen Bedrohungen und Anomalien überwachen. Das Ziel sollten möglichst hochwertige Warnungen sein. Damit verringern Sie die Anzahl falsch positiver Ergebnisse, die später von Analysten aussortiert werden müssen. Die Quellen von Warnungen können Protokolldaten, Agents oder andere Daten darstellen.

- [Bedrohungsschutz in Azure Security Center](/azure/security-center/threat-protection) 

- [Sicherheitswarnungen von Azure Security Center (Referenzhandbuch)](../security-center/alerts-reference.md)

- [Erstellen benutzerdefinierter Analyseregeln zum Erkennen von Bedrohungen](../sentinel/tutorial-detect-threats-custom.md) 

- [Cyber Threat Intelligence mit Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Aktivieren der Bedrohungserkennung für die Identitäts- und Zugriffsverwaltung in Azure

**Leitfaden**: Azure AD stellt die folgenden Benutzerprotokolle bereit, die in der Azure AD-Berichterstellung angezeigt oder für komplexere Anwendungsfälle in Bezug auf Überwachung und Analyse in Azure Monitor, Azure Sentinel oder andere Tools zur Sicherheits- und Ereignisverwaltung (SIEM) oder Überwachungstools integriert werden können:

- Anmeldungen: Der Bericht zu Anmeldungen enthält Informationen zur Nutzung von verwalteten Anwendungen und Aktivitäten der Benutzeranmeldung.

- Überwachungsprotokolle: Ermöglichen die Nachverfolgung sämtlicher Änderungen, die von verschiedenen Features in Azure AD vorgenommen wurden. Hierzu zählen unter anderem Änderungen an Ressourcen in Azure AD, z. B. das Hinzufügen oder Entfernen von Benutzern, Apps, Gruppen, Rollen und Richtlinien.

- Riskante Anmeldungen: Eine riskante Anmeldung ist ein Hinweis auf einen Anmeldeversuch einer Person, die nicht der rechtmäßige Besitzer eines Benutzerkontos ist.

- Benutzer mit Risikomarkierung: Ein Benutzer mit Risikomarkierung ist ein Indikator für ein ggf. kompromittiertes Benutzerkonto.

Auch Azure Security Center kann bestimmte verdächtige Aktivitäten melden, wie z. B. eine übermäßige Anzahl fehlgeschlagener Authentifizierungsversuche oder veraltete Konten im Abonnement. Zusätzlich zur grundlegenden Sicherheitsüberwachung kann das Threat Protection-Modul in Azure Security Center auch ausführlichere Sicherheitswarnungen von einzelnen Azure-Computeressourcen (VMs, Container, App Service), Datenressourcen (SQL-Datenbanken und -Speicher) und Azure-Dienstebenen sammeln. So erhalten Sie Einblick in Kontoanomalien innerhalb einzelner Ressourcen.

- [Berichte zu Überwachungsaktivitäten in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Aktivieren von Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 

- [Bedrohungsschutz in Azure Security Center](/azure/security-center/threat-protection)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Aktivieren der Protokollierung für Azure-Netzwerkaktivitäten

**Leitfaden**: Windows Virtual Desktop erzeugt oder verarbeitet keine DNS-Abfrageprotokolle (Domain Name Service). Ressourcen, die für den Dienst registriert sind, können jedoch Datenflussprotokolle liefern.

Aktivieren und sammeln Sie für die Sicherheitsanalyse Ressourcen- und Datenflussprotokolle für Netzwerksicherheitsgruppen, Azure Firewall-Protokolle sowie WAF-Protokolle (Web Application Firewall), um die Untersuchung von Incidents, die Bedrohungssuche und die Generierung von Sicherheitswarnungen zu unterstützen. Sie können die Datenflussprotokolle an einen Log Analytics-Arbeitsbereich von Azure Monitor senden und dann mithilfe von Traffic Analytics Einblicke ermöglichen.

- [Aktivieren von Datenflussprotokollen für Netzwerksicherheitsgruppen](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Azure Firewall-Protokolle und -Metriken](/azure/firewall/logs-and-metrics) 

- [Aktivieren und Verwenden von Traffic Analytics](../network-watcher/traffic-analytics.md) 

- [Azure-Netzwerküberwachungslösungen in Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Aktivieren der Protokollierung für Azure-Ressourcen

**Leitfaden**: Aktivitätsprotokolle, die automatisch aktiviert werden, enthalten alle Schreibvorgänge (PUT, POST, DELETE) für Ihre Windows Virtual Desktop-Ressourcen mit Ausnahme von Lesevorgängen (GET). Mit Aktivitätsprotokollen können Fehler ermittelt werden, und es kann nachverfolgt werden, welche Änderungen an einer Ressource ein Benutzer in Ihrer Organisation vorgenommen hat.

- [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Grundlegendes zur Protokollierung und zu verschiedenen Protokolltypen in Azure](../azure-monitor/platform/platform-logs-overview.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Zentralisieren der Verwaltung und Analyse von Sicherheitsprotokollen

**Leitfaden**: Zentralisieren Sie die Speicherung und Analyse von Protokollen, um eine Korrelation zu ermöglichen. Stellen Sie sicher, dass jeder Protokollquelle ein Datenbesitzer, eine Zugriffsanleitung, ein Speicherort, die für die Verarbeitung und den Zugriff verwendeten Tools sowie Anforderungen zur Datenaufbewahrung zugewiesen werden.

Stellen Sie sicher, dass die Azure-Aktivitätsprotokolle in die zentrale Protokollierung integriert werden. Erfassen von Protokollen über Azure Monitor zum Aggregieren von Sicherheitsdaten, die von Endpunktgeräten, Netzwerkressourcen und anderen Sicherheitssystemen generiert werden. Verwenden Sie in Azure Monitor Log Analytics-Arbeitsbereiche, um Analysen abzufragen und auszuführen, und verwenden Sie Azure Storage-Konten für langfristige Speicherung und Archivierung.

Darüber hinaus können Sie Daten in Azure Sentinel oder ein SIEM-System (Security Information & Event Management) eines Drittanbieters integrieren und dort weiter nutzen. Viele Unternehmen verwenden Azure Sentinel für „heiße“ Daten, die häufig verwendet werden, und Azure Storage für seltener verwendete „kalte“ Daten.

- [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Reaktion auf Vorfälle](/azure/security/benchmarks/security-controls-v2-incident-response).*

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

Markieren Sie Ressourcen außerdem mithilfe von Tags, und erstellen Sie ein Benennungssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren – insbesondere solche, von denen vertrauliche Daten verarbeitet werden. Die Priorisierung der Behebung von Warnungen basierend auf der Wichtigkeit der Azure-Ressourcen und der Umgebung, in der der Vorfall aufgetreten ist, liegt in Ihrer Verantwortung.

- [Sicherheitswarnungen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Verwenden von Tags zum Organisieren von Azure-Ressourcen](/azure/azure-resource-manager/resource-group-using-tags)

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

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: Einrichten sicherer Konfigurationen für Computeressourcen

**Leitfaden**: Verwenden Sie Azure Security Center und Azure Policy, um sichere Konfigurationen auf allen Computeressourcen wie beispielsweise VMs und Containern einzurichten.

Sie können benutzerdefinierte Betriebssystemimages oder Azure Automation State Configuration verwenden, um die Sicherheitskonfiguration des für Ihre Organisation benötigten Betriebssystems festzulegen.

- [Überwachen von Empfehlungen in Azure Security Center](../security-center/security-center-recommendations.md) 

- [Übersicht über Azure Automation State Configuration](../automation/automation-dsc-overview.md) 

- [Windows Virtual Desktop-Umgebung](environment-setup.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4: Aufrechterhalten sicherer Konfigurationen für Computeressourcen

**Leitfaden**: Verwenden Sie Azure Security Center und Azure Policy, um Konfigurationsrisiken für Ihre Azure-Computeressourcen wie VMs, Container und Sonstiges regelmäßig zu bewerten und zu beseitigen. Darüber hinaus können Sie auch Azure Resource Manager-Vorlagen, benutzerdefinierte Betriebssystemimages oder Azure Automation State Configuration verwenden, um die Sicherheitskonfiguration des für Ihre Organisation benötigten Betriebssystems zu verwalten. Die Vorlagen für virtuelle Computer von Microsoft in Kombination mit Azure Automation State Configuration können als Hilfe beim Erfüllen und Verwalten der Sicherheitsanforderungen dienen.

Von Microsoft im Azure Marketplace veröffentlichte VM-Images werden von Microsoft verwaltet.

Azure Security Center kann auch Containerimages auf Sicherheitsrisiken überprüfen und eine kontinuierliche Überwachung ihrer Docker-Konfiguration in Containern auf Grundlage der Docker-Benchmark von Center Internet Security durchführen. Auf der Seite „Empfehlungen“ in Azure Security Center finden Sie Empfehlungen und können Probleme beheben.

- [Implementieren von Empfehlungen für die Sicherheitsrisikobewertung aus Azure Security Center](/azure/security-center/security-center-vulnerability-assessment-recommendations) 

- [Erstellen eines virtuellen Azure-Computers mithilfe einer ARM-Vorlage](../virtual-machines/windows/ps-template.md) 

- [Übersicht über Azure Automation State Configuration](../automation/automation-dsc-overview.md) 

- [Containersicherheit in Security Center](../security-center/container-security.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>PV-5: Sicheres Speichern von benutzerdefinierten Betriebssystem- und Containerimages

**Leitfaden**: Windows Virtual Desktop ermöglicht Kunden das Verwalten von Betriebssystemimages. Verwenden Sie die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC), um sicherzustellen, dass nur autorisierte Benutzer auf Ihre benutzerdefinierten Images zugreifen können. Mit einer Azure Shared Image Gallery können Sie Ihre Images für unterschiedliche Benutzer, Dienstprinzipale oder Active Directory-Gruppen in Ihrer Organisation freigeben. Speichern Sie Containerimages in Azure Container Registry, und stellen Sie mithilfe von RBAC sicher, dass nur autorisierte Benutzer über Zugriff verfügen.

- [Grundlegendes zu Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md) 

- [Konfigurieren von Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md) 

- [Übersicht über den Katalog mit freigegebenen Images](/azure/virtual-machines/windows/shared-image-galleries)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="pv-6-perform-software-vulnerability-assessments"></a>PV-6: Durchführen von Sicherheitsrisikobewertungen für Software

**Leitfaden**: Mit Windows Virtual Desktop können Sie Ihre eigenen virtuellen Computer bereitstellen und für den Dienst registrieren sowie SQL-Datenbank in der Umgebung ausführen.

Windows Virtual Desktop kann für die Durchführung von Sicherheitsrisikobewertungen für Netzwerkgeräte und Webanwendungen eine Drittanbieterlösung verwenden. Verwenden Sie bei der Durchführung von Remote-Scans kein einzelnes, unbefristetes Administratorkonto. Ziehen Sie die Implementierung der JIT-Bereitstellungsmethodik für das Scan-Konto in Erwägung. Anmeldeinformationen für das Scan-Konto sollten geschützt, überwacht und nur für die Überprüfung von Sicherheitsrisiken verwendet werden.

Exportieren Sie die Scanergebnisse wie erforderlich in regelmäßigen Abständen und vergleichen Sie die Ergebnisse mit vorherigen Scans, um zu überprüfen, ob die Sicherheitsrisiken behoben wurden.

Befolgen Sie die Empfehlungen aus dem Azure Security Center zum Durchführen von Sicherheitsrisikobewertungen für Ihre virtuellen Azure-Computer (und SQL-Server). Azure Security Center verfügt über einen integrierten Scanner für die Überprüfung von virtuellen Computern, Containerimages und SQL-Datenbank auf Sicherheitsrisiken.

Exportieren Sie die Scanergebnisse wie erforderlich in regelmäßigen Abständen und vergleichen Sie die Ergebnisse mit vorherigen Scans, um zu überprüfen, ob die Sicherheitsrisiken behoben wurden. Wenn Sie die von Azure Security Center vorgeschlagenen Empfehlungen zur Verwaltung von Sicherheitsrisiken verwenden, können Sie in das Portal der ausgewählten Lösung wechseln, um die historischen Scandaten anzuzeigen.

- [Implementieren von Empfehlungen für die Sicherheitsrisikobewertung aus Azure Security Center](/azure/security-center/security-center-vulnerability-assessment-recommendations) 

- [Integrierter Scanner für Sicherheitsrisiken für VMs](/azure/security-center/built-in-vulnerability-assessment) 
- [SQL-Sicherheitsrisikobewertung](../azure-sql/database/sql-vulnerability-assessment.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: Schnelles und automatisches Beheben von Softwaresicherheitsrisiken

**Leitfaden**: Windows Virtual Desktop verwendet bzw. erfordert keine Software von Drittanbietern. Mit Windows Virtual Desktop können Sie jedoch Ihre eigenen virtuellen Computer bereitstellen und für den Dienst registrieren.

Verwenden Sie die Azure Automation-Updateverwaltung oder eine Drittanbieterlösung, um sicherzustellen, dass auf Ihren Windows Server-VMs die aktuellen Sicherheitsupdates installiert sind. Stellen Sie bei virtuellen Windows-Computern sicher, dass Windows Update aktiviert wurde und auf „Automatisch Aktualisieren“ festgelegt ist.

Verwenden Sie für Drittanbietersoftware eine Lösung für die Patchverwaltung von Drittanbietern oder System Center Updates Publisher für Configuration Manager.

- [Konfigurieren der Updateverwaltung für virtuelle Computer in Azure](/azure/automation/update-management/overview) 

- [Verwalten von Updates und Patches für Ihre Azure-VMs](/azure/automation/update-management/manage-updates-for-vm)

- [Konfigurieren von Microsoft Endpoint Configuration Manager für Windows Virtual Desktop](configure-automatic-updates.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Regelmäßiges Durchführen einer Angriffssimulation

**Leitfaden**: Mit Windows Virtual Desktop können Kunden keine eigenen Penetrationstests für ihre Windows Virtual Desktop-Ressourcen durchführen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="endpoint-security"></a>Endpunktsicherheit

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Endpunktsicherheit](/azure/security/benchmarks/security-controls-v2-endpoint-security).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: Verwenden von Endpunkterkennung und -antwort (Endpoint Detection and Response, EDR)

**Leitfaden**: Windows Virtual Desktop bietet keine spezifischen Funktionen für die Prozesse zur Endpunkterkennung und -antwort (EDR). Ressourcen, die für den Dienst registriert sind, können jedoch von Funktionen für Endpunkterkennung und -antwort profitieren. 

Aktivieren Sie Funktionen für Endpunkterkennung und -antwort für Server und Clients, und integrieren Sie sie in SIEM-Lösungen (Sicherheits- und Ereignisverwaltung) und Security Operations-Prozesse.

Advanced Threat Protection von Microsoft Defender bietet Funktionen für Endpunkterkennung und -antwort im Rahmen einer Endpunktsicherheitsplattform für Unternehmen, um komplexe Bedrohungen zu vermeiden, zu erkennen, zu untersuchen und darauf zu reagieren.

- [Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) 

- [Durchführen des Onboardings von Windows-Servern für den Microsoft Defender ATP-Dienst](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints) 

- [Durchführen des Onboardings für Windows-fremde Server](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

- [Microsoft Defender ATP für eine nicht beständige virtuelle Desktopinfrastruktur](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-vdi)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: Verwenden von moderner, zentral verwalteter Antischadsoftware

**Leitfaden**: Schützen Sie Ihre Windows Virtual Desktop-Ressourcen durch eine zentral verwaltete und moderne Endpunkt-Antischadsoftware-Echtzeitlösung, die periodische Überprüfungen durchführt.

Azure Security Center kann automatisch die Verwendung verschiedener gängiger Antischadsoftwarelösungen für Ihre virtuellen Computer erkennen, den Ausführungsstatus des Endpunktschutzes melden und Empfehlungen abgeben.

Microsoft Antimalware für Azure Cloud Services ist die Standard-Antischadsoftware für virtuelle Windows-Computer (virtual machines, VMs). Mithilfe der Bedrohungserkennung mit Azure Security Center für Datendienste können Sie auch Schadsoftware erkennen, die in Azure Storage-Konten hochgeladen wurde.

- [Konfigurieren von Microsoft Antimalware für Cloud Services und Virtual Machines](../security/fundamentals/antimalware.md) 

- [Unterstützte Endpoint Protection-Lösungen](https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows#supported-endpoint-protection-solutions)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: Stellen Sie sicher, dass Antischadsoftwaresignaturen schnell und konsistent aktualisiert werden.

Befolgen Sie die Empfehlungen in Azure Security Center: „Compute &amp; Apps“, um sicherzustellen, dass alle VMs und/oder Container mit den neuesten Signaturen auf dem aktuellen Stand sind.

Mit Microsoft Antimalware werden standardmäßig die neuesten Signaturen und Engine-Updates automatisch installiert.

- [Bereitstellen von Microsoft Antimalware für Azure Cloud Services und Virtual Machines](../security/fundamentals/antimalware.md) 

- [Endpoint Protection: Bewertung und Empfehlungen in Azure Security Center](../security-center/security-center-endpoint-protection.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="backup-and-recovery"></a>Sicherung und Wiederherstellung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Sicherung und Wiederherstellung](/azure/security/benchmarks/security-controls-v2-backup-recovery).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Stellen Sie sicher, dass Systeme und Daten gesichert werden, um die Geschäftskontinuität nach einem unerwarteten Ereignis aufrechtzuerhalten. Dies sollte sich an den Zielen für das Recovery Point Objective (RPO) und das Recovery Time Objective (RTO) orientieren.

Aktivieren Sie Azure Backup, und konfigurieren Sie die Sicherungsquelle (z. B. virtuelle Azure-Computer, SQL Server, HANA-Datenbanken oder Dateifreigaben) sowie die gewünschte Häufigkeit und Beibehaltungsdauer.

Für ein höheres Maß an Redundanz können Sie die Option für georedundante Speicherung aktivieren, um Sicherungsdaten in eine sekundäre Region zu replizieren und mittels regionsübergreifender Wiederherstellung wiederherzustellen.

- [Business Continuity &amp; Disaster Recovery auf Unternehmensebene](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery) 

- [Aktivieren von Azure Backup](/azure/backup/) 

- [Wie kann ich die regionsübergreifende Wiederherstellung aktivieren?](/azure/backup/backup-azure-arm-restore-vms#cross-region-restore) 

- [Anleitung zum Einrichten eines Plans für Geschäftskontinuität und Notfallwiederherstellung in Windows Virtual Desktop](disaster-recovery.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="br-2-encrypt-backup-data"></a>BR-2: Verschlüsseln von Sicherungsdaten

**Leitfaden**: Stellen Sie sicher, dass Ihre Sicherungen vor Angriffen geschützt sind. Dazu gehört auch die Verschlüsselung der Sicherungen zum Schutz vor dem Verlust der Vertraulichkeit.

Für regelmäßige Azure-Dienstsicherungen werden die Sicherungsdaten automatisch mit von der Azure-Plattform verwalteten Schlüsseln verschlüsselt. Sie können sich dafür entscheiden, die Sicherung mit einem vom Kunden verwalteten Schlüssel zu verschlüsseln. Stellen Sie in diesem Fall sicher, dass der vom Kunden verwaltete Schlüssel im Schlüsseltresor ebenfalls Teil der Sicherung ist.

Verwenden Sie rollenbasierte Zugriffssteuerung in Azure Backup, Azure Key Vault oder anderen Ressourcen, um Sicherungen und vom Kunden verwaltete Schlüssel zu schützen. Zusätzlich können Sie erweiterte Sicherheitsfeatures aktivieren, die mehrstufige Authentifizierung erfordern, bevor Sicherungen geändert oder gelöscht werden können.

Übersicht über die Sicherheitsfeatures in Azure Backup /azure/backup/security-overview 

- [Verschlüsselung von Sicherungsdaten mit von Kunden verwalteten Schlüsseln](/azure/backup/encryption-at-rest-with-cmk) 

- [Sichern von Schlüsseltresorschlüsseln in Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0&amp;preserve-view=true)

- [Sicherheitsfeatures für den Schutz von Hybridsicherungen vor Angriffen](/azure/backup/backup-azure-security-feature#prevent-attacks)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Überprüfen aller Sicherungen einschließlich kundenseitig verwalteter Schlüssel

**Leitfaden**: Sie sollten die Datenintegrität auf Sicherungsmedien in regelmäßigen Abständen überprüfen, indem Sie eine Datenwiederherstellung durchführen, um sicherzustellen, dass die Sicherung ordnungsgemäß funktioniert.

- [Wiederherstellen von Dateien aus einer Sicherung von virtuellen Azure-Computern](/azure/backup/backup-azure-restore-files-from-vm)

- [Sicherheitsimplementierung](/azure/backup/backup-azure-restore-files-from-vm#security-implementations)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="governance-and-strategy"></a>Governance und Strategie

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Governance und Strategie](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definieren der Strategie für Ressourcenverwaltung und Datenschutz 

**Leitfaden**: Stellen Sie sicher, dass Sie eine klare Strategie für die kontinuierliche Überwachung und den Schutz von Systemen und Daten dokumentieren und kommunizieren. Priorisieren Sie die Ermittlung, die Bewertung, den Schutz und die Überwachung unternehmenskritischer Daten und Systeme. 

Diese Strategie sollte dokumentierte Anleitungen, Richtlinien und Standards für die folgenden Elemente umfassen: 

-   Datenklassifizierungsstandard in Übereinstimmung mit den Geschäftsrisiken

-   Sicherheitsorganisationseinblick in Risiken und Ressourcenbestand 

-   Genehmigung durch die Sicherheitsorganisation für die Nutzung der Azure-Dienste 

-   Sicherheit von Ressourcen durch ihren gesamten Lebenszyklus

-   Erforderliche Zugriffssteuerungsstrategie in Übereinstimmung mit der Organisationsdatenklassifizierung

-   Verwendung von nativen Azure- und Drittanbieter-Funktionen für den Datenschutz

-   Datenverschlüsselungsanforderungen für Anwendungsfälle während der Übertragung und für ruhende Anwendungsfälle

-   Geeignete Verschlüsselungsstandards

Weitere Informationen finden Sie in den folgenden Referenzen:
- [Empfehlung für eine Azure-Sicherheitsarchitektur: Speicher, Daten, Verschlüsselung](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Grundlegende Azure-Sicherheitsinformationen: Sicherheit, Verschlüsselung und Speicherung von Azure-Daten](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework: Bewährte Methoden für Datensicherheit und Datenverschlüsselung in Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

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

- [Azure-Sicherheitsvergleichstest: Identitätsverwaltung](/azure/automation/update-management/overview)

- [Azure-Sicherheitsvergleichstest: Privilegierter Zugriff](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Azure Security Best Practice 11 – Architecture. Single unified security strategy](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy) (Bewährte Methoden für die Sicherheit von Azure 11 – Architektur: Zentralisierte einheitliche Sicherheitsstrategie)

- [Übersicht über die Sicherheit der Azure-Identitätsverwaltung](../security/fundamentals/identity-management-overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definieren einer Strategie für Protokollierung und Reaktion auf Bedrohungen

**Leitfaden**: Richten Sie eine Strategie für Protokollierung und Reaktion auf Bedrohungen zur schnellen Erkennung und Behebung von Bedrohungen bei gleichzeitiger Erfüllung von Complianceanforderungen ein. Priorisieren Sie die Bereitstellung wertvoller Warnungen und nahtloser Funktionen für Analysten, damit sie sich auf Bedrohungen konzentrieren können, anstatt mit der Integration und der Ausführung manueller Schritte. 

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

- Sehen Sie sich die [Übersicht über Version 2 des Azure-Sicherheitsvergleichstests](/azure/security/benchmarks/overview) an.
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](/azure/security/benchmarks/security-baselines-overview).

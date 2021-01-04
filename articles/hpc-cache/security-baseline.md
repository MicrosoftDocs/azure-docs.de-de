---
title: Azure-Sicherheitsbaseline für Azure HPC Cache
description: Die Azure HPC Cache-Sicherheitsbaseline enthält Anweisungen zur Vorgehensweise und Ressourcen für die Implementierung der Sicherheitsempfehlungen im Vergleichstest für die Azure-Sicherheit.
author: msmbaldwin
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 036cab033e5b70a8940a488c07374e9092d52b33
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533654"
---
# <a name="azure-security-baseline-for-azure-hpc-cache"></a>Azure-Sicherheitsbaseline für Azure HPC Cache

Diese Sicherheitsbaseline wendet Empfehlungen des [Azure-Sicherheitsvergleichstests Version 2.0](../security/benchmarks/overview.md) auf Microsoft Azure HPC Cache an. Der Azure-Sicherheitsvergleichstest enthält Empfehlungen zum Schutz Ihrer Cloudlösungen in Azure. Der Inhalt wird nach den **Sicherheitskontrollen** gruppiert, die durch den Azure-Sicherheitsvergleichstest und die entsprechenden für Azure HPC Cache geltenden Empfehlungen definiert werden. Nicht auf Azure HPC Cache anwendbare **Kontrollen** wurden ausgeschlossen.

Zum Anzeigen der vollständigen Zuordnung von Azure HPC Cache zum Vergleichstest für die Azure-Sicherheit sehen Sie sich die [vollständige Zuordnungsdatei der Azure HPC Cache-Sicherheitsbaseline](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines) an.

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: Implementieren der Sicherheit für internen Datenverkehr

**Leitfaden**: Wenn Sie Azure HPC Cache-Ressourcen bereitstellen, müssen Sie ein virtuelles Netzwerk erstellen oder ein vorhandenes verwenden. 

Stellen Sie sicher, dass alle virtuellen Azure-Netzwerke einem Prinzip der Unternehmenssegmentierung unterliegen, das sich den geschäftlichen Risiken anpasst. Jedes System, das ein höheres Risiko für das Unternehmen darstellen könnte, sollte innerhalb eines eigenen virtuellen Netzwerks isoliert und mit einer Netzwerksicherheitsgruppe (NSG) und/oder Azure Firewall ausreichend geschützt werden.

Bevor Sie den Cache verwenden können, sollten Sie zwei netzwerkbezogene Voraussetzungen einrichten: 

- Ein dediziertes Subnetz für die Azure HPC Cache-Instanz

- DNS-Unterstützung, sodass der Cache auf Speicher und andere Ressourcen zugreifen kann

Der Azure HPC Cache benötigt ein dediziertes Subnetz mit diesen Eigenschaften: 

- Im Subnetz müssen mindestens 64 IP-Adressen verfügbar sein.

- Das Subnetz darf keine weiteren VMs hosten, nicht einmal für verwandte Dienste wie Clientcomputer.

- Wenn Sie mehrere Azure HPC Cache-Instanzen verwenden, benötigt jede ein eigenes Subnetz.

Die bewährte Methode besteht im Erstellen eines neuen Subnetzes für die einzelnen Caches. Sie können im Rahmen der Cache-Erstellung ein neues virtuelles Netzwerk und Subnetz erstellen.

Wenn Sie HPC Cache mit lokalem NAS-Speicher verwenden möchten, müssen Sie sicherstellen, dass bestimmte Ports im lokalen Netzwerk uneingeschränkten Datenverkehr aus dem Subnetz von Azure HPC Cache zulassen. 

- [Konfigurieren von Ports für den NFS-Speicherzugriff](hpc-cache-prerequisites.md#nfs-storage-requirements)

- [Tutorial: Filtern von Netzwerkdatenverkehr mithilfe einer Netzwerksicherheitsgruppe über das Azure-Portal](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Bereitstellen und Konfigurieren von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="ns-2-connect-private-networks-together"></a>NS-2: Verbinden privater Netzwerke

**Leitfaden**: Stellen Sie mit Azure ExpressRoute oder dem virtuellen privaten Azure-Netzwerk (VPN) private Verbindungen mit Azure-Rechenzentren und lokaler Infrastruktur in einer Housingumgebung her. ExpressRoute-Verbindungen werden nicht über das öffentliche Internet hergestellt und bieten mehr Zuverlässigkeit, eine höhere Geschwindigkeit und weniger Latenz als herkömmliche Internetverbindungen. Für Point-to-Site-VPN und Site-to-Site-VPN können Sie lokale Geräte oder Netzwerke mit einem virtuellen Netzwerk verbinden, indem Sie eine beliebige Kombination dieser VPN-Optionen und Azure ExpressRoute verwenden. 

Stellen Sie eine Verbindung von zwei oder mehr virtuellen Netzwerken in Azure mittels Peering virtueller Netzwerke her. Der Netzwerkdatenverkehr zwischen mittels Peering verbundenen virtuellen Netzwerken ist privat und wird im Azure-Backbone-Netzwerk verwaltet.

- [ExpressRoute-Konnektivitätsmodelle](../expressroute/expressroute-connectivity-models.md) 

- [Was ist VPN Gateway?](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [Peering von virtuellen Netzwerken](../virtual-network/virtual-network-peering-overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Einrichten des Zugriffs über das private Netzwerk auf Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Virtual Network-Dienstendpunkte, um sicheren Zugriff auf HPC Cache bereitzustellen. Dienstendpunkte sind eine optimierte Route über das Azure Backbone-Netzwerk, ohne das Internet zu benutzen. 

Der HPC-Cache unterstützt nicht die Verwendung von Azure Private Link, um seine Verwaltungsendpunkte in einem privaten Netzwerk zu sichern. 

Der private Zugriff ist neben der Authentifizierung und der von Azure-Diensten gebotenen Datenverkehrssicherheit eine zusätzliche Verteidigungsmaßnahme.

- [Grundlegendes zu VNET-Dienstendpunkten](../virtual-network/virtual-network-service-endpoints-overview.md) 

- [Grundlegendes zum Einbinden von Azure HPC Cache](hpc-cache-mount.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Schützen von Anwendungen und Diensten vor externen Netzwerkangriffen.

**Leitfaden**: Schützen Sie HPC Cache-Ressourcen vor Angriffen aus externen Netzwerken einschließlich verteilter Denial-of-Service-Angriffe (DDoS), anwendungsspezifischer Angriffe und unerwünschtem und potenziell schädlichem Internetdatenverkehr. 

Azure bietet native Funktionen für diesen Schutz: 

- Schützen Sie mit der Azure-Firewall Anwendungen und Dienste vor potenziell schädlichem Datenverkehr aus dem Internet und von anderen externen Standorten. 
- Schützen Sie Ihre Ressourcen durch Aktivieren des DDoS-Standardschutzes in Ihren virtuellen Azure-Netzwerken vor DDoS-Angriffen. 
- Verwenden Sie Azure Security Center, um Risiken durch Fehlkonfigurationen zu erkennen, die mit Ihren Netzwerkressourcen zusammenhängen.

Azure HPC Cache ist nicht zum Ausführen von Webanwendungen gedacht und erfordert nicht, dass Sie zusätzliche Einstellungen konfigurieren oder zusätzliche Netzwerkdienste bereitstellen, um sie vor externen Netzwerkangriffen zu schützen, die auf Webanwendungen ausgerichtet sind.

- [Azure Firewall-Dokumentation](../firewall/index.yml) 

- [Verwalten von Azure DDoS Protection Standard mithilfe des Azure-Portals](../ddos-protection/manage-ddos-protection.md) 

- [Azure Security Center-Empfehlungen](../security-center/recommendations-reference.md#recs-network)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: Bereitstellen von Angriffserkennungs-/Eindringschutzsystemen (Intrusion Detection/Intrusion Prevention Systems, IDS/IPS)

**Leitfaden**: Verwenden Sie Azure Firewall mit Threat Intelligence-basierter Filterung, um bei bekannten böswilligen IP-Adressen und Domänen zu warnen und/oder Datenverkehr zu und von diesen Adressen oder Domänen zu blockieren. Die IP-Adressen und Domänen stammen aus dem Microsoft Threat Intelligence-Feed. 

Wenn eine Payloaduntersuchung erforderlich ist, können Sie eine Lösung eines Angriffserkennungs-/Eindringschutzsystems (IDS/IPS) eines Drittanbieters mit Payloaduntersuchungsfunktionen aus Azure Marketplace bereitstellen. Alternativ können Sie eine hostbasierte IDS/IPS- oder Endpunkterkennungs- und Antwortlösung (EDR) in Verbindung mit einem netzwerkbasierten IDS/IPS oder stattdessen verwenden.

Hinweis: Bei einer gesetzlichen oder sonstigen Anforderung der Verwendung von IDS/IPS müssen Sie sicherstellen, dass sie stets auf hochwertige Warnungen für Ihre SIEM-Lösung abgestimmt ist.

- [Bereitstellen von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md) 

- [IDS-Funktionen von Drittanbietern im Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [ATP-EDR-Funktion von Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: Vereinfachen von Netzwerksicherheitsregeln

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Angebote gedacht, die in Azure Virtual Networks bereitgestellt werden können oder in der Lage sind, Gruppierungen von zulässigen IP-Bereichen für eine effiziente Verwaltung zu definieren. HPC Cache unterstützt derzeit keine Diensttags. 

Die bewährte Methode besteht im Erstellen eines neuen Subnetzes für die einzelnen Caches. Sie können im Rahmen der Cache-Erstellung ein neues virtuelles Netzwerk und Subnetz erstellen.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: Secure Domain Name Service (DNS)

**Leitfaden**: Befolgen Sie die bewährten Methoden für die DNS-Sicherheit, um häufige Angriffe wie verwaistes DNS, DNS-Verstärkungsangriffe, DNS-Vergiftungen und -Spoofing sowie andere zu vermeiden.

Azure HPC Cache benötigt DNS für den Zugriff auf Ressourcen außerhalb des privaten virtuellen Netzwerks des Caches. Wenn Ihr Workflow Ressourcen außerhalb von Azure umfasst, müssen Sie zusätzlich zur Verwendung von Azure DNS Ihren eigenen DNS-Server einrichten und sichern.

- Verwenden Sie Azure DNS, um auf Azure Blobspeicher-Endpunkte, Azure-basierte Clientcomputer oder andere Azure-Ressourcen zuzugreifen.
- Für den Zugriff auf den lokalen Speicher oder zum Herstellen einer Verbindung mit dem Cache von Clients außerhalb von Azure müssen Sie einen benutzerdefinierten DNS-Server erstellen, der diese Hostnamen auflösen kann.
- Wenn Ihr Workflow sowohl interne als auch externe Ressourcen enthält, richten Sie Ihren benutzerdefinierten DNS-Server ein, um Azure-spezifische Auflösungsanforderungen an den Azure DNS-Server weiterzuleiten. 

Wenn Azure DNS als autorisierender DNS-Dienst verwendet wird, stellen Sie sicher, dass DNS-Zonen und -Einträge vor versehentlicher oder böswilliger Änderung mit Azure RBAC und Ressourcensperren geschützt werden.

Wenn Sie einen eigenen DNS-Server konfigurieren, achten Sie darauf, dass Sie die folgenden Sicherheitsrichtlinien beachten:

- [Secure Domain Name System (DNS) Deployment Guide](https://csrc.nist.gov/publications/detail/sp/800-81/2/final) (Bereitstellungshandbuch für Secure Domain Name System) 

- [Verhindern verwaister DNS-Einträge und Vermeiden von Unterdomänenübernahmen](../security/fundamentals/subdomain-takeover.md) 

- [Weitere Informationen zu DNS-Zugriffsanforderungen für HPC Cache](hpc-cache-prerequisites.md#dns-access)

- [Azure DNS – Übersicht](../dns/dns-overview.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="identity-management"></a>Identitätsverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Identitätsverwaltung](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardisieren von Azure Active Directory als zentrales Identitäts- und Authentifizierungssystem

**Leitfaden**: Azure HPC Cache ist für interne Vorgänge nicht in Azure Active Directory integriert. Azure AD kann jedoch zur Authentifizierung von Benutzern im Azure-Portal oder der Befehlszeilenschnittstelle verwendet werden, um HPC Cache-Bereitstellungen und zugehörige Komponenten zu erstellen, anzuzeigen und zu verwalten.

Azure Active Directory (Azure AD) ist der Standarddienst für Identitäts- und Zugriffsverwaltung in Azure. Sie sollten Azure AD als Standard für die Identitäts- und Zugriffsverwaltung Ihrer Organisation verwenden:

- Microsoft-Cloudressourcen, wie z. B. das Azure-Portal, Azure Storage, Azure-VMs (Linux und Windows), Azure Key Vault, PaaS- und SaaS-Anwendungen.

- Die Ressourcen Ihrer Organisation, z. B. Anwendungen in Azure oder Ihre Unternehmensnetzwerkressourcen.

Die Sicherung von Azure AD sollte bei den Methoden Ihrer Organisation im Zusammenhang mit Cloudsicherheit eine hohe Priorität haben. Azure AD bietet eine Identitätssicherheitsbewertung, die Ihnen dabei hilft, den Identitätssicherheitsstatus in Bezug auf die Empfehlungen zu bewährten Methoden von Microsoft zu bewerten. Verwenden Sie die Bewertung, um zu beurteilen, wie gut Ihre Konfiguration den Empfehlungen zu bewährten Methoden entspricht, und um Ihren Sicherheitsstatus zu verbessern.

Hinweis: Azure AD unterstützt externe Identitäten, die es Benutzern ohne Microsoft-Konto ermöglichen, sich mit ihrer externen Identität bei ihren Anwendungen und Ressourcen anzumelden.

- [Mandanten in Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Erstellen und Konfigurieren einer Azure AD-Instanz](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Verwenden externer Identitätsanbieter für eine Anwendung](../active-directory/external-identities/identity-providers.md) 

- [Was ist der Identity Secure Score in Azure Active Directory?](../active-directory/fundamentals/identity-secure-score.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Sicheres und automatisches Verwalten von Anwendungsidentitäten

**Leitfaden**: HPC Cache verwendet von Azure verwaltete Identitäten für nicht für Personen bestimmte Konten, z. B. für Dienste oder Automatisierung. Es wird empfohlen, das Feature für verwaltete Azure-Identitäten zu verwenden, anstatt ein leistungsfähigeres Benutzerkonto für den Zugriff auf Ihre Ressourcen oder deren Ausführung zu erstellen. 

HPC Cache kann sich gegenüber Azure-Diensten/-Ressourcen, die die Azure AD-Authentifizierung unterstützen, durch vordefinierte Zugriffszuweisungsregeln nativ authentifizieren. Dadurch wird die Notwendigkeit vermieden, hartcodierte Anmeldeinformationen in Quellcode oder Konfigurationsdateien zu verwenden.

- [Von Azure verwaltete Identitäten](../active-directory/managed-identities-azure-resources/overview.md) 

- [Dienste, die verwaltete Identitäten für Azure-Ressourcen unterstützen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Verwenden von Azure AD Single Sign-on (SSO) für den Anwendungszugriff

**Leitfaden**: Azure HPC Cache wird für interne Vorgänge nicht mit Azure AD integriert. Azure AD kann jedoch zur Authentifizierung von Benutzern im Azure-Portal oder der Befehlszeilenschnittstelle verwendet werden, um HPC Cache-Bereitstellungen und zugehörige Komponenten zu erstellen, anzuzeigen und zu verwalten.

Azure Active Directory bietet Identitäts- und Zugriffsverwaltung für Azure-Ressourcen, Cloudanwendungen und lokale Anwendungen. Dies umfasst sowohl Unternehmensidentitäten wie Mitarbeiter als auch externe Identitäten wie Partner, Anbieter und Zulieferer. Dies ermöglicht SSO (Single Sign-On, einmaliges Anmelden) die Verwaltung und den Schutz des Zugriffs auf die Daten und Ressourcen Ihrer Organisation, die lokal und in der Cloud gespeichert sind. Verbinden Sie all Ihre Benutzer, Anwendungen und Geräte mit Azure AD, um einen nahtlosen, sicheren Zugriff sowie mehr Transparenz und Kontrolle zu ermöglichen.

- [Grundlegendes zu Anwendungs-SSO mit Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Verwenden stärkerer Authentifizierungssteuerungen für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Obwohl Azure HPC Cache für interne Vorgänge nicht in Azure AD integriert ist, kann Azure AD zur Authentifizierung von Benutzern im Azure-Portal oder der Befehlszeilenschnittstelle verwendet werden, um HPC Cache-Bereitstellungen und zugehörige Komponenten zu erstellen, anzuzeigen und zu verwalten.  

Azure AD unterstützt starke Authentifizierungskontrollelemente durch Multi-Factor Authentication (MFA) und starke kennwortlose Methoden.

- Multi-Factor Authentication: Aktivieren Sie Azure AD MFA, und wenden Sie mithilfe der Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center einige bewährte Methoden in Ihrem MFA-Setup an. MFA kann für alle Benutzer, für ausgewählte Benutzer oder auf Benutzerebene auf der Grundlage von Anmeldebedingungen und Risikofaktoren durchgesetzt werden.
- Kennwortlose Authentifizierung: Es stehen drei Optionen für die kennwortlose Authentifizierung zur Verfügung: Windows Hello for Business, die Microsoft Authenticator-App und lokale Authentifizierungsmethoden wie Smartcards.

Für Administratoren und privilegierte Benutzer ist darauf zu achten, die höchste Ebene der Methode der strengen Authentifizierung zu verwenden. Führen Sie den Rollout entsprechender Richtlinie zur strengen Authentifizierung für andere Benutzer durch.

Azure HPC Cache unterstützt auch die auf Kennwörtern basierende Legacyauthentifizierung für Clientsysteme, die eine Verbindung mit dem Cache herstellen. Zu diesen Arten von Verbindungen gehören reine Cloudkonten (Benutzerkonten, die direkt in Azure erstellt werden), die über eine Baseline-Kennwortrichtlinie verfügen, oder Hybridkonten (Benutzerkonten, die von lokalem Active Directory stammen), die den lokalen Kennwortrichtlinien folgen. 

Bei der Verwendung der kennwortbasierten Authentifizierung bietet Azure AD eine Kennwortschutzfunktion, die Benutzer daran hindert, leicht zu erratende Kennwörter festzulegen. Microsoft stellt eine globale Liste verbotener Kennwörter zur Verfügung, die auf der Grundlage von Telemetrie aktualisiert wird, und Kunden können die Liste entsprechend ihren Bedürfnissen erweitern (z. B. um Markennamen, kulturelle Verweise usw.). Dieser Kennwortschutz kann für reine Cloud- und Hybridkonten verwendet werden.

Hinweis: Die Authentifizierung allein auf der Grundlage von Kennwortanmeldeinformationen ist anfällig für beliebte Angriffsmethoden. Für höhere Sicherheit verwenden Sie starke Authentifizierungsmethoden wie MFA und eine starke Kennwortrichtlinie. Wenn Sie Anwendungen von Drittanbietern und Marketplace-Dienste mit Standardkennwörtern verwenden, legen Sie beim ersten Einrichten des Diensts ein neues, sicheres Kennwort fest. 

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Einführung in Optionen für die kennwortlose Authentifizierung für Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

- [Azure AD-Standardkennwortrichtlinie](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Ausschließen von ungeeigneten Kennwörtern mit dem Azure AD-Kennwortschutz](../active-directory/authentication/concept-password-ban-bad.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Überwachen und Warnen bei Kontoanomalien

**Leitfaden**: Azure HPC Cache kann Azure Active Directory für die Benutzerverwaltung über das Azure-Portal verwenden.  Azure Active Directory stellt die folgenden Datenquellen bereit:

- Anmeldungen: Der Bericht „Anmeldungen“ enthält Informationen zur Nutzung von verwalteten Anwendungen und Benutzeranmeldeaktivitäten.

- Überwachungsprotokolle: Ermöglichen die Nachverfolgung sämtlicher Änderungen, die von verschiedenen Features in Azure AD vorgenommen wurden. Hierzu zählen unter anderem Änderungen an Ressourcen in Azure AD, z. B. das Hinzufügen oder Entfernen von Benutzern, Apps, Gruppen, Rollen und Richtlinien.

- Riskante Anmeldungen: Eine riskante Anmeldung ist ein Indikator für einen Anmeldeversuch von einem Benutzer, der nicht der rechtmäßige Besitzer eines Benutzerkontos ist.

- Benutzer mit Risikomarkierung: Ein Benutzer mit Risikomarkierung ist ein Indikator für ein ggf. kompromittiertes Benutzerkonto.

Diese Datenquellen können mit Azure Monitor, Azure Sentinel oder SIEM-Systemen von Drittanbietern integriert werden.

Auch Azure Security Center kann bestimmte verdächtige Aktivitäten melden, wie z. B. eine übermäßige Anzahl fehlgeschlagener Authentifizierungsversuche oder veraltete Konten im Abonnement.

Azure Advanced Threat Protection (ATP) ist eine Sicherheitslösung, die Active Directory-Signale nutzen kann, um komplexe Bedrohungen, potenziell gefährdete Identitäten und schädliche Aktionen von Insidern zu identifizieren, zu erkennen und zu untersuchen.

- [Berichte zu Überwachungsaktivitäten in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Anzeigen riskanter Azure AD-Anmeldungen](../active-directory/identity-protection/overview-identity-protection.md) 

- [Identifizieren von Azure AD-Benutzern, die aufgrund riskanter Aktivitäten gekennzeichnet wurden](../active-directory/identity-protection/overview-identity-protection.md) 

- [Überwachen der identitäts- und zugriffsbezogenen Aktivitäten von Benutzern in Azure Security Center](../security-center/security-center-identity-access.md)

 

- [Warnungen im Threat Intelligence-Schutzmodul von Azure Security Center](../security-center/alerts-reference.md) 

- [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Leitfaden**: Nicht zutreffend. HPC Cache gestattet es Kunden nicht, persistente Daten in der aktiven Umgebung bereitzustellen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="privileged-access"></a>Privilegierter Zugriff

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Privilegierter Zugriff](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Einschränken des administrativen Zugriffs auf unternehmenskritische Systeme

**Leitfaden**: HPC Cache verwendet die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC), um den Zugriff auf unternehmenskritische Systeme zu isolieren. Zu diesem Zweck werden die Konten begrenzt, denen privilegierter Zugriff auf die Abonnements und Verwaltungsgruppen gewährt wird, denen sie angehören.

Erstellen Sie Standardbetriebsvorgänge für die Verwendung dedizierter Administratorkonten. Zum Erstellen eines Caches erfordert HPC Cache, dass die Benutzer über ausreichende Berechtigungen im Abonnement verfügen, um NICs zu erstellen. Wenn Sie Blobspeicher verwenden, sind die Azure-Rollen „Mitwirkender von Speicherkonto“ und „Mitwirkender an Speicherblobdaten“ erforderlich, damit HPC Cache auf den Speicher zugreifen kann. 

Stellen Sie sicher, dass Sie auch den Zugriff auf die Verwaltungs-, Identitäts- und Sicherheitssysteme einschränken, die über Administratorzugriff auf Ihre unternehmenskritischen Ressourcen verfügen, z. B. Active Directory-Domänencontroller (DCs), Sicherheitstools und Systemverwaltungstools mit Agents, die auf unternehmenskritischen Systemen installiert sind. Angreifer, die diese Verwaltungs- und Sicherheitssysteme kompromittieren, können diese sofort ausnutzen, um geschäftskritische Ressourcen zu gefährden.

Alle Zugriffssteuerungstypen sollten an der Segmentierungsstrategie des Unternehmens ausgerichtet werden, um eine konsistente Zugriffssteuerung sicherzustellen.

- [RBAC-Berechtigungen für Azure HPC Cache](hpc-cache-prerequisites.md#permissions)

- [Azure-Komponenten und -Referenzmodell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Zugriff auf die Verwaltungsgruppe](../governance/management-groups/overview.md#management-group-access)

- [Azure-Abonnementadministratoren](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Überprüfen Sie Benutzerkonten und die Zugriffszuweisung regelmäßig, um sicherzustellen, dass die Konten und ihre Zugriffsebenen angemessen sind. 

Azure HPC Cache kann Azure Active Directory-Konten (Azure AD) verwenden, um den Benutzerzugriff über das Azure-Portal und zugehörige Schnittstellen zu verwalten. Azure AD bietet Zugriffsüberprüfungen, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen zu überprüfen. Azure AD-Berichte können Protokolle zum Ermitteln von veralteten Konten bereitstellen. Sie können Azure AD Privileged Identity Management auch verwenden, um einen Workflow für den Zugriffsüberprüfungsbericht zu erstellen und so den Überprüfungsprozess zu vereinfachen.

Darüber hinaus können Sie Azure Privileged Identity Management konfigurieren, sodass Sie eine Warnung erhalten, wenn übermäßig viele Administratorkonten erstellt werden, und um veraltete oder falsch konfigurierte Administratorkonten zu ermitteln.

Hinweis: Einige Azure-Dienste unterstützen lokale Benutzer und Rollen, die nicht über Azure AD verwaltet werden. Sie müssen diese Benutzer separat verwalten.

Wenn Sie NFS-Speicherziele verwenden, müssen Sie mit Ihren Netzwerkadministratoren und Firewall-Managern zusammenarbeiten, um die Zugriffseinstellungen zu überprüfen und sicherzustellen, dass Azure HPC Cache mit den NFS-Speichersystemen kommunizieren kann.

- [Eine Liste der HPC Cache-Berechtigungen finden Sie unter den Voraussetzungen für Azure HPC Cache](hpc-cache-prerequisites.md) 

- [Erstellen einer Zugriffsüberprüfung für Azure-Ressourcenrollen in Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Was sind Azure AD-Zugriffsüberprüfungen?](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Einrichten des Notfallzugriffs in Azure AD

**Leitfaden**: HPC Cache kann Azure Active Directory verwenden, um den Zugriff auf Ressourcen über das Azure-Portal zu verwalten. Um zu verhindern, dass Sie versehentlich aus Ihrer Azure AD-Organisation ausgesperrt werden, richten Sie ein Konto für den Notfallzugriff ein, für den Fall, dass normale Verwaltungskonten nicht verwendet werden können. Konten für den Notfallzugriff verfügen normalerweise über umfangreiche Berechtigungen und sollten keinen Einzelpersonen zugewiesen werden. Konten für den Notfallzugriff sind auf Notfallsituationen oder Szenarien beschränkt, in denen normale Administratorkonten nicht verwendet werden können.

Sie sollten sicherstellen, dass die Anmeldeinformationen (z. B. Kennwort, Zertifikat oder Smartcard) für Konten für den Notfallzugriff sicher aufbewahrt werden und nur den Personen bekannt sind, die für deren Verwendung im Notfall autorisiert sind.

- [Verwalten von Konten für den Notfallzugriff in Azure AD](../active-directory/roles/security-emergency-access.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="pa-5-automate-entitlement-management"></a>PA-5: Automatisieren der Berechtigungsverwaltung 

**Leitfaden**: HPC Cache kann Azure Active Directory verwenden, um den Zugriff auf Azure-Ressourcen über das Azure-Portal zu verwalten. 

Verwenden Sie die Berechtigungsverwaltungsfeatures von Azure AD, um die Zugriffsanforderungsworkflows zu automatisieren, einschließlich Zugriffszuweisungen, Überprüfungen und Ablauf. Die duale oder die mehrstufige Genehmigung wird ebenfalls unterstützt. 

- [Was sind Azure AD-Zugriffsüberprüfungen?](../active-directory/governance/access-reviews-overview.md) 

- [Was ist die Azure AD-Berechtigungsverwaltung?](../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Befolgen Sie die Prinzipien der Just Enough Administration (Prinzip der geringsten Rechte) 

**Leitfaden**: HPC Cache ist zur Ressourcenverwaltung in die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) integriert. Mit Azure RBAC kann der Zugriff auf Azure-Ressourcen über Rollenzuweisungen verwaltet werden. Diese Rollen können Benutzern, Gruppen, Dienstprinzipalen und verwalteten Identitäten zugewiesen werden. Für bestimmte Ressourcen sind vordefinierte integrierte Rollen verfügbar. Diese Rollen können über Tools wie die Azure CLI, Azure PowerShell oder das Azure-Portal inventarisiert oder abgefragt werden. 

Die Berechtigungen, die Sie Ressourcen über Azure RBAC zuweisen, sollten immer auf die nötigsten Anforderungen der Rollen beschränkt sein. Dies ergänzt den JIT-Ansatz (Just-in-Time) von Azure AD Privileged Identity Management (PIM), und diese Berechtigungen sollten regelmäßig überprüft werden.

Verwenden Sie integrierte Rollen, um Berechtigungen zuzuweisen und benutzerdefinierte Rollen nur bei Bedarf zu erstellen.

- [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?](../role-based-access-control/overview.md) 

- [Konfigurieren von RBAC in Azure](../role-based-access-control/role-assignments-portal.md) 

- [Was sind Azure AD-Zugriffsüberprüfungen?](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="data-protection"></a>Datenschutz

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Schutz von Daten](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1: Ermitteln, Klassifizieren und Bezeichnen von vertraulichen Daten 

**Leitfaden**: HPC Cache verwaltet vertrauliche Daten, verfügt jedoch nicht über die Möglichkeit, vertrauliche Daten zu entdecken, zu klassifizieren und zu bezeichnen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Schützen von vertraulichen Daten

**Leitfaden**: Schützen Sie vertrauliche Daten, indem Sie den Zugriff mithilfe der rollenbasierten Zugriffssteuerung in Azure (Azure RBAC), der netzwerkbasierten Zugriffssteuerungen und bestimmter Steuerungen in Azure-Diensten (z. B. Verschlüsselung in SQL und anderen Datenbanken) einschränken.

Um eine konsistente Zugriffssteuerung sicherzustellen, sollten alle Zugriffssteuerungstypen an der Segmentierungsstrategie des Unternehmens ausgerichtet werden. Bei der Segmentierungsstrategie des Unternehmens sollte auch der Speicherort vertraulicher und unternehmenskritischer Daten und Systeme berücksichtigt werden.

Für die zugrunde liegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und schützt Kundendaten vor Verlust und Gefährdung. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von Standard-Datenschutzsteuerungen und -funktionen implementiert.

- [Rollenbasierte Zugriffssteuerung in Azure (Role-Based Access Control, RBAC)](../role-based-access-control/overview.md) 

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Überwachung auf nicht autorisierte Übertragung vertraulicher Daten

**Leitfaden**: HPC Cache überträgt vertrauliche Daten, unterstützt jedoch nicht die Überwachung auf nicht autorisierte Übertragung von vertraulichen Daten.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Verschlüsseln vertraulicher Informationen während der Übertragung

**Leitfaden**: HPC Cache unterstützt die Datenverschlüsselung während der Übertragung mit TLS v1.2 oder höher.

Obwohl dies bei Datenverkehr in privaten Netzwerken optional ist, ist es für den Datenverkehr in externen und öffentlichen Netzwerken von entscheidender Bedeutung. Stellen Sie bei HTTP-Datenverkehr sicher, dass alle Clients, die Verbindungen mit Ihren Azure-Ressourcen herstellen, TLS 1.2 oder höher aushandeln können. Verwenden Sie für die Remoteverwaltung SSH (für Linux) bzw. RDP/TLS (für Windows) anstelle eines unverschlüsselten Protokolls. Veraltete SSL-, TLS- und SSH-Versionen und -Protokolle sowie schwache Verschlüsselungsverfahren sollten deaktiviert werden.

Azure ermöglicht standardmäßig die Verschlüsselung von Daten während der Übertragung zwischen Azure-Rechenzentren.

- [Grundlegendes zur Verschlüsselung während der Übertragung mit Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [Informationen zur TLS-Sicherheit](/security/engineering/solving-tls1-problem) 

- [Doppelte Verschlüsselung für Azure-Daten während der Übertragung](../security/fundamentals/double-encryption.md#data-in-transit)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Verschlüsseln vertraulicher ruhender Daten

**Leitfaden**: Als Ergänzung zu Zugriffssteuerungen sollten ruhende Daten durch Verschlüsselung vor Out-of-Band-Angriffen (z. B. Zugriffen auf den zugrunde liegenden Speicher) geschützt werden. Dadurch wird sichergestellt, dass die Daten von Angreifern nicht einfach gelesen oder geändert werden können.

Azure bietet standardmäßig Verschlüsselung für ruhende Daten. Bei streng vertraulichen Daten stehen Ihnen ggf. Optionen zur Verfügung, um eine zusätzliche Verschlüsselung im Ruhezustand für alle Azure-Ressourcen zu implementieren. Azure verwaltet standardmäßig Ihre Verschlüsselungsschlüssel, Azure bietet jedoch auch Optionen, mit denen Sie Ihre Schlüssel für bestimmte Azure-Dienste selbst verwalten können (kundenseitig verwaltete Schlüssel).

Alle in Azure gespeicherten Daten, einschließlich auf Cachedatenträgern, werden ruhend standardmäßig mithilfe der von Microsoft verwalteten Schlüssel verschlüsselt. Sie müssen die Einstellungen von Azure HPC Cache nur anpassen, wenn Sie die zur Verschlüsselung Ihrer Daten verwendeten Schlüssel verwalten möchten.

Wenn aus Compliancegründen für Computeressourcen erforderlich, implementieren Sie ein Drittanbietertool, z. B. eine automatisierte Lösung für die hostbasierte Verhinderung von Datenverlust, um Zugriffssteuerungen für Daten auch dann zu erzwingen, wenn Daten von einem System kopiert werden.

- [Verwenden von kundenseitig verwalteten Verschlüsselungsschlüsseln mit Azure HPC Cache](./hpc-cache-create.md?tabs=azure-portal#enable-azure-key-vault-encryption-optional)

- [Grundlegendes zur Verschlüsselung ruhender Daten in Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Konfigurieren von kundenseitig verwalteten Schlüsseln mit Azure Key Vault über das Azure-Portal](../storage/common/customer-managed-keys-configure-key-vault.md) 

- [Tabelle für Verschlüsselungsmodell und Schlüsselverwaltung](../security/fundamentals/encryption-atrest.md)

 

- [Doppelte Verschlüsselung für ruhende Daten in Azure](../security/fundamentals/double-encryption.md#data-at-rest)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="asset-management"></a>Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Ressourcenverwaltung](../security/benchmarks/security-controls-v2-asset-management.md).*

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

**Leitfaden**: Azure HPC Cache unterstützt die Verwendung von Tags. Wenden Sie Tags auf Ihre Ressourcen, Ressourcengruppen und Abonnements in Azure an, um sie logisch in einer Taxonomie zu strukturieren. Jedes Tag besteht aus einem Paar mit einem Namen und einem Wert. 

So können Sie beispielsweise den Namen „Umgebung“ und den Wert „Produktion“ auf alle Ressourcen in der Produktion anwenden. Tags können sowohl beim Erstellen eines Caches als auch nach der Bereitstellung des Caches hinzugefügt werden. 

Verwenden Sie Azure Virtual Machine Inventory, um die Erfassung von Informationen zur Software auf virtuellen Computern zu automatisieren. Softwarename, Version, Herausgeber und Aktualisierungszeit sind im Azure-Portal verfügbar. Um Zugriff auf das Installationsdatum und andere Informationen zu erhalten, aktivieren Sie die Diagnose auf Gastebene, und fügen Sie die Windows-Ereignisprotokolle einem Log Analytics-Arbeitsbereich hinzu.
HPC Cache lässt die Ausführung einer Anwendung oder Installation von Software auf seinen Ressourcen nicht zu. 

- [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](../governance/resource-graph/first-query-portal.md) 

- [Verwalten des Ressourcenbestands in Azure Security Center](../security-center/asset-inventory.md) 

- [Leitfaden zur Entscheidungsfindung für Ressourcenbenennung und -markierung](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json) 

- [Aktivieren des Bestands an virtuellen Azure-Computern](../automation/automation-tutorial-installed-software.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: HPC Cache unterstützt Azure Resource Manager-Bereitstellungen. Verwenden Sie Azure Policy, um die Dienste, die von Benutzern in Ihrer Umgebung bereitgestellt werden, zu überwachen und einzuschränken. Verwenden Sie Azure Resource Graph, um Ressourcen in ihren Abonnements abzufragen und zu ermitteln. Sie können auch mit Azure Monitor Regeln erstellen, mit denen Warnungen ausgelöst werden, wenn ein nicht genehmigter Dienst erkannt wird.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../governance/policy/samples/index.md) 

- [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Gewährleisten der Sicherheit der bei der Lebenszyklusverwaltung von Ressourcen

**Leitfaden**: Nicht zutreffend Azure HPC Cache kann nicht verwendet werden, um die Sicherheit von Ressourcen in einem Lebenszyklusverwaltungsprozess sicherzustellen. Es liegt in der Verantwortung des Kunden, die Attribute und Netzwerkkonfigurationen von Ressourcen zu verwalten, die als besonders wichtig erachtet werden. 

Es wird empfohlen, dass der Kunde einen Prozess erstellt, um die Attribut- und Netzwerkkonfigurationsänderungen zu erfassen, die Auswirkungen der Änderung zu messen und ggf. Abhilfemaßnahmen zu treffen.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-threat-detection"></a>Protokollierung und Bedrohungserkennung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Protokollierung und Bedrohungserkennung](/azure/security/benchmarks/security-controls-v2-logging-threat-detection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Aktivieren der Bedrohungserkennung für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die im Azure Security Center integrierte Funktion zur Erkennung von Bedrohungen und aktivieren Sie Azure Defender (ehemals Azure Advanced Threat Protection) für Ihre HPC Cache-Ressourcen. Azure Defender für HPC Cache ermöglicht die Nutzung intelligenter Sicherheitsfunktionen zur Erkennung von ungewöhnlichen und möglicherweise schädlichen Versuchen, auf Ihre Cacheressourcen zuzugreifen oder diese unbefugt zu nutzen.

Leiten Sie alle Protokolle aus HPC Cache an Ihr SIEM weiter, das zur Einrichtung benutzerdefinierter Bedrohungserkennungen verwendet werden kann. Stellen Sie sicher, dass Sie unterschiedliche Arten von Azure-Ressourcen nach potenziellen Bedrohungen und Anomalien überwachen. Das Ziel sollten möglichst hochwertige Warnungen sein. Damit verringern Sie die Anzahl falsch positiver Ergebnisse, die später von Analysten aussortiert werden müssen. Die Quellen von Warnungen können Protokolldaten, Agents oder andere Daten darstellen.

- [Bedrohungsschutz in Azure Security Center](../security-center/azure-defender.md) 

- [Sicherheitswarnungen von Azure Security Center (Referenzhandbuch)](../security-center/alerts-reference.md) 

- [Erstellen benutzerdefinierter Analyseregeln zum Erkennen von Bedrohungen](../sentinel/tutorial-detect-threats-custom.md) 

- [Cyber Threat Intelligence mit Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Aktivieren der Bedrohungserkennung für die Identitäts- und Zugriffsverwaltung in Azure

**Leitfaden**: Azure AD stellt die folgenden Benutzerprotokolle bereit, die Sie in der Azure AD-Berichterstellung anzeigen oder mit Azure Monitor, Azure Sentinel oder anderen SIEM- und Überwachungstools integrieren können, um komplexere Anwendungsfälle für Überwachung und Analyse zu erhalten.
- Anmeldungen: Der Bericht „Anmeldungen“ enthält Informationen zur Nutzung von verwalteten Anwendungen und Aktivitäten der Benutzeranmeldung.

- Überwachungsprotokolle: Ermöglichen die Nachverfolgung sämtlicher Änderungen, die von verschiedenen Features in Azure AD vorgenommen wurden. Hierzu zählen unter anderem Änderungen an Ressourcen in Azure AD, z. B. das Hinzufügen oder Entfernen von Benutzern, Apps, Gruppen, Rollen und Richtlinien.

- Riskante Anmeldungen: Eine riskante Anmeldung ist ein Indikator für einen Anmeldeversuch von einem Benutzer, der nicht der rechtmäßige Besitzer eines Benutzerkontos ist.

- Benutzer mit Risikomarkierung: Ein Benutzer mit Risikomarkierung ist ein Indikator für ein ggf. kompromittiertes Benutzerkonto.

Auch Azure Security Center kann bestimmte verdächtige Aktivitäten melden, wie z. B. eine übermäßige Anzahl fehlgeschlagener Authentifizierungsversuche oder veraltete Konten im Abonnement. Zusätzlich zur grundlegenden Sicherheitsüberwachung kann das Threat Protection-Modul von Azure Security Center auch ausführlichere Sicherheitswarnungen von einzelnen Azure-Computeressourcen (VMs, Container, App Service), Datenressourcen (SQL-Datenbanken und -Speicher) und Azure-Dienstebenen sammeln. So erhalten Sie Einblick in Kontoanomalien innerhalb einzelner Ressourcen.

- [Berichte zu Überwachungsaktivitäten in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Aktivieren von Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 

- [Bedrohungsschutz in Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Aktivieren der Protokollierung für Azure-Netzwerkaktivitäten

**Leitfaden**: Sie können VPN-Gateways und ihre Paketerfassungsfunktion zusätzlich zu den allgemein verfügbaren Paketerfassungstools verwenden, um Netzwerkpakete aufzuzeichnen, die zwischen Ihren virtuellen Netzwerken übertragen werden.

Stellen Sie eine Netzwerksicherheitsgruppe in dem Netzwerk bereit, in dem Ihre Azure HPC Cache-Ressourcen bereitgestellt werden. Aktivieren Sie Datenflussprotokolle der Netzwerksicherheitsgruppe in Ihren Netzwerksicherheitsgruppen für die Überwachung des Datenverkehrs.

Ihre Datenflussprotokolle werden in einem Speicherkonto aufbewahrt. Aktivieren Sie die Traffic Analytics-Lösung, um diese Datenflussprotokolle zu verarbeiten und an einen Log Analytics-Arbeitsbereich zu senden. Traffic Analytics bietet zusätzliche Einblicke in den Datenverkehrsfluss für Ihre Azure-Netzwerke. Traffic Analytics kann Ihnen helfen, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

Der Cache benötigt DNS für den Zugriff auf Ressourcen außerhalb seines virtuellen Netzwerks. Je nachdem, welche Ressourcen Sie verwenden, müssen Sie möglicherweise einen angepassten DNS-Server einrichten und die Weiterleitung zwischen diesem Server und Azure DNS-Servern konfigurieren. 

Implementieren Sie eine Drittanbieterlösung aus Azure Marketplace für die DNS-Protokollierungslösung entsprechend den Anforderungen Ihrer Organisation.

- [Konfigurieren der Paketerfassung für VPN-Gateways](../vpn-gateway/packet-capture.md) 

- [Aktivieren von Datenflussprotokollen für Netzwerksicherheitsgruppen](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Aktivieren und Verwenden von Traffic Analytics](../network-watcher/traffic-analytics.md) 

- [Grundlegendes zu der von Azure Security Center bereitgestellten Netzwerksicherheit](../security-center/security-center-network-recommendations.md) 

- [Weitere Informationen zu den DNS-Voraussetzungen](hpc-cache-prerequisites.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Aktivieren der Protokollierung für Azure-Ressourcen

**Leitfaden**: Azure HPC Cache-Ressourcen erstellen automatisch Aktivitätsprotokolle. Diese Protokolle enthalten alle Schreibvorgänge (PUT, POST, DELETE), aber nicht die Lesevorgänge (GET). Mit Aktivitätsprotokollen können Fehler ermittelt werden, und es kann nachverfolgt werden, welche Änderungen an einer Ressource ein Benutzer in Ihrer Organisation vorgenommen hat.

Sie können auch Azure Security Center und Azure Policy verwenden, um Azure-Ressourcenprotokolle für HPC Cache zu aktivieren und die Datenerfassung zu protokollieren. Diese Protokolle können für die spätere Untersuchung von Sicherheitsvorfällen und die Durchführung forensischer Übungen von entscheidender Bedeutung sein.

- [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Grundlegendes zur Protokollierung und zu verschiedenen Protokolltypen in Azure](../azure-monitor/platform/platform-logs-overview.md) 

- [Grundlegendes zur Datensammlung in Azure Security Center](../security-center/security-center-enable-data-collection.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Zentralisieren der Verwaltung und Analyse von Sicherheitsprotokollen

**Leitfaden**: Zentralisieren Sie die Speicherung und Analyse von Protokollen, um eine Korrelation zu ermöglichen. Stellen Sie sicher, dass jeder Protokollquelle ein Datenbesitzer, eine Zugriffsanleitung, ein Speicherort, die für die Verarbeitung und den Zugriff verwendeten Tools sowie Anforderungen zur Datenaufbewahrung zugewiesen werden.

Stellen Sie sicher, dass die Azure-Aktivitätsprotokolle in die zentrale Protokollierung integriert werden. Erfassen von Protokollen über Azure Monitor zum Aggregieren von Sicherheitsdaten, die von Endpunktgeräten, Netzwerkressourcen und anderen Sicherheitssystemen generiert werden. Verwenden Sie in Azure Monitor Log Analytics-Arbeitsbereiche, um Analysen abzufragen und auszuführen, und verwenden Sie Azure Storage-Konten für langfristige Speicherung und Archivierung.

Zusätzlich können Sie auch Daten in Azure Sentinel oder der SIEM-Lösung eines Drittanbieters aktivieren und integrieren.

Viele Unternehmen verwenden Azure Sentinel für „heiße“ Daten, die häufig verwendet werden, und Azure Storage für seltener verwendete „kalte“ Daten.

- [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Reaktion auf Vorfälle](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Vorbereitung – Aktualisieren des Prozesses zur Reaktion auf Vorfälle für Azure

**Leitfaden**: Stellen Sie sicher, dass Ihre Organisation über Prozesse verfügt, um auf Sicherheitsvorfälle zu reagieren, dass diese Prozesse für Azure aktualisiert wurden und dass sie regelmäßig trainiert werden, um die Bereitschaft zu gewährleisten.

- [Implement security across the enterprise environment](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) (Implementieren von Sicherheit in der gesamten Unternehmensumgebung)

- [Referenzleitfaden für die Reaktion auf Vorfälle](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="ir-2-preparation--set-up-incident-notification"></a>IR-2: Vorbereitung – Einrichten von Ereignisbenachrichtigungen 

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

Azure Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie sicher Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

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

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: Einrichten sicherer Konfigurationen für Computeressourcen

**Leitfaden**: Verwenden Sie Azure Security Center und Azure Policy, um sichere Konfigurationen auf allen Computeressourcen wie beispielsweise VMs und Containern einzurichten.

- [Überwachen von Empfehlungen in Azure Security Center](../security-center/security-center-recommendations.md) 

- [Sicherheitsempfehlungen: Referenzhandbuch](../security-center/recommendations-reference.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

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

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Da es sich bei Azure HPC Cache um eine Cachinglösung und nicht um ein Speichersystem handelt, sollten Sie sich darauf konzentrieren, sicherzustellen, dass die Daten in ihren Speicherzielen regelmäßig gesichert werden. Befolgen Sie Standardverfahren für Azure-Blobcontainer und für die Sicherung aller lokalen Speicherziele. 

Um Unterbrechungen im Falle eines regionalen Ausfalls zu minimieren, können Sie Maßnahmen ergreifen, um einen regionsübergreifenden Datenzugriff sicherzustellen.  

Jede Azure HPC Cache-Instanz wird innerhalb eines bestimmten Abonnements und in genau einer Region ausgeführt. Dies bedeutet, dass der Cacheworkflow möglicherweise unterbrochen wird, wenn die Region vollständig ausfällt. Um diese Unterbrechung zu minimieren, sollte die Organisation Back-End-Speicher verwenden, auf den von mehreren Regionen aus zugegriffen werden kann. Bei diesem Speicher kann es sich um ein lokales NAS-System mit entsprechender DNS-Unterstützung oder um eine Azure Blob Storage-Instanz handeln, die sich in einer anderen Region als der Cache befindet.

Während Ihr Workflow in Ihrer primären Region ausgeführt wird, werden die Daten im langfristigen Speicher außerhalb der Region gespeichert. Wenn die Cacheregion nicht mehr verfügbar ist, können Sie ein Duplikat der Azure HPC Cache-Instanz in einer sekundären Region erstellen und mit dem gleichen Speicher verbinden und dann die Arbeit mit dem neuen Cache fortsetzen.

- [Erfahren Sie mehr über regionale Failover](hpc-region-recovery.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="br-2-encrypt-backup-data"></a>BR-2: Verschlüsseln von Sicherungsdaten

**Leitfaden**: Stellen Sie sicher, dass Ihre Sicherungen vor Angriffen geschützt sind. Dazu gehört auch die Verschlüsselung der Sicherungen zum Schutz vor dem Verlust der Vertraulichkeit.

Für die lokale Sicherung mit Azure Backup erfolgt eine Verschlüsselung im Ruhezustand über die bereitgestellte Passphrase. Für regelmäßige Azure-Dienstsicherungen werden die Sicherungsdaten automatisch mit von der Azure-Plattform verwalteten Schlüsseln verschlüsselt. Sie können sich dafür entscheiden, die Sicherung mit vom Kunden verwalteten Schlüsseln zu verschlüsseln. Stellen Sie in diesem Fall sicher, dass der vom Kunden verwaltete Schlüssel im Schlüsseltresor ebenfalls Teil der Sicherung ist.

Azure HPC Cache wird darüber hinaus durch VM-Hostverschlüsselung auf den verwalteten Datenträgern geschützt, die Ihre zwischengespeicherten Daten enthalten, auch wenn Sie einen Kundenschlüssel für die Cachedatenträger hinzufügen. Durch das Hinzufügen eines kundenseitig verwalteten Schlüssels für die doppelte Verschlüsselung wird für Kunden mit hohen Sicherheitsanforderungen die Sicherheit erhöht. Ausführliche Informationen finden Sie unter Serverseitige Verschlüsselung von Azure Disk Storage.

Verwenden Sie rollenbasierte Zugriffssteuerung in Azure Backup, Azure Key Vault oder anderen Ressourcen, um Sicherungen und vom Kunden verwaltete Schlüssel zu schützen. Zusätzlich können Sie erweiterte Sicherheitsfeatures aktivieren, die MFA erfordern, bevor Sicherungen geändert oder gelöscht werden können.

- [VM-Hostverschlüsselung](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)

- [Serverseitige Verschlüsselung von Azure Disk Storage](../virtual-machines/disk-encryption.md)

- [Übersicht über Sicherheitsfeatures in Azure Backup](../backup/security-overview.md) 

- [Verschlüsselung von Sicherungsdaten mit von Kunden verwalteten Schlüsseln](../backup/encryption-at-rest-with-cmk.md)  

- [Sichern von Key Vault-Schlüsseln in Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-5.1.0)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Überprüfen aller Sicherungen einschließlich kundenseitig verwalteter Schlüssel

**Leitfaden**: Stellen Sie regelmäßig sicher, dass gesicherte, kundenseitig verwaltete Schlüssel wiederhergestellt werden können.

- [Wiederherstellen von Key Vault-Schlüsseln in Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-5.1.0)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

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

- Sehen Sie sich die [Übersicht über Version 2 des Azure-Sicherheitsvergleichstests](../security/benchmarks/overview.md) an.
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).
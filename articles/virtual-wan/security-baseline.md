---
title: Azure-Sicherheitsbaseline für Virtual WAN
description: Die Sicherheitsbaseline für Virtual WAN enthält Schritt-für-Schritt-Anleitungen und Ressourcen für die Implementierung der Sicherheitsempfehlungen aus dem Vergleichstest für die Azure-Sicherheit.
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 994cb250aff2079e32eeef6a0d22466a284481f5
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602426"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>Azure-Sicherheitsbaseline für Virtual WAN

Diese Sicherheitsbaseline wendet Empfehlungen des [Azure-Sicherheitsvergleichstests Version 2.0](../security/benchmarks/overview.md) auf Microsoft Azure Virtual WAN an. Der Azure-Sicherheitsvergleichstest enthält Empfehlungen zum Schutz Ihrer Cloudlösungen in Azure. Der Inhalt wird anhand der **Sicherheitskontrollen** gruppiert, die durch den Azure-Sicherheitsvergleichstest und die entsprechenden für Virtual WAN geltenden Empfehlungen definiert werden. Nicht auf Virtual WAN anwendbare **Kontrollen** wurden ausgeschlossen.

Um die vollständige Zuordnung von Virtual WAN zum Azure-Sicherheitsvergleichstest anzuzeigen, sehen Sie sich die [vollständige Zuordnungsdatei der Virtual WAN-Sicherheitsbaseline](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines) an.

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: Implementieren der Sicherheit für internen Datenverkehr

**Leitfaden**: Microsoft Azure Virtual WAN bietet benutzerdefinierte Routingfunktionen sowie Verschlüsselung für Ihren ExpressRoute-Datenverkehr. Die gesamte Routenverwaltung wird vom Router des virtuellen Hubs bereitgestellt, der auch die Transitkonnektivität zwischen virtuellen Netzwerken ermöglicht. Die Verschlüsselung des ExpressRoute-Datenverkehrs mit Virtual WAN ermöglicht eine verschlüsselte Übertragung zwischen den lokalen Netzwerken und virtuellen Azure-Netzwerken über ExpressRoute, ohne Nutzung des öffentlichen Internets oder öffentlicher IP-Adressen. 

- [Verschlüsselung von ExpressRoute-Datenverkehr](virtual-wan-about.md#encryption)

- [Verwenden von Private Link in Virtual WAN](howto-private-link.md)

- [Benutzerdefinierte Routingszenarien](scenario-any-to-any.md)

- [Benutzerdefinierte Routingtabellen: Dokumentation](how-to-virtual-hub-routing.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="ns-2-connect-private-networks-together"></a>NS-2: Verbinden privater Netzwerke 

**Leitfaden**: Microsoft Azure ExpressRoute bietet private Konnektivität mit Azure Virtual WAN. Da die ExpressRoute-Verbindungen nicht über das öffentliche Internet erfolgen, bietet ExpressRoute mehr Zuverlässigkeit, eine höhere Geschwindigkeit und eine niedrigere Latenz als typische Internetverbindungen. Sie können die Verbindung mit Azure auch über ein virtuelles privates Netzwerk herstellen. Nutzen Sie dafür entweder ein S2S-VPN (Site-to-Site) oder ein P2S-VPN (Point-to-Site).

- [ExpressRoute in Virtual WAN](virtual-wan-expressroute-portal.md)

- [Übersicht über Site-to-Site-VPN](virtual-wan-site-to-site-portal.md)

- [Übersicht über Point-to-Site-VPN](virtual-wan-point-to-site-portal.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Schützen von Anwendungen und Diensten vor externen Netzwerkangriffen.

**Leitfaden**: Virtual WAN macht keine Endpunkte für externe Netzwerke verfügbar, wofür ein Schutz dieser Endpunkte über konventionelle Netzwerkschutzmethoden notwendig wäre. Sie können Ressourcen in virtuellen Spoke-Netzwerktopologien (also einem beliebigen mit einem virtuellen Hub verbundenen virtuellen Netzwerk) mithilfe von Schutzdiensten für virtuelle Netzwerke absichern. 

Verwenden Sie Azure Firewall, um Anwendungen und Dienste vor potenziell schädlichem Datenverkehr aus dem Internet und von anderen externen Standorten zu schützen. 

Entscheiden Sie sich für DDoS Protection von Azure zum Schutz Ihrer Ressourcen vor Angriffen auf Ihre Azure Virtual Networks. Verwenden Sie Azure Security Center, um Risiken durch Fehlkonfigurationen im Zusammenhang mit Ihren netzwerkbezogenen Ressourcen zu erkennen.

- [Azure Firewall-Dokumentation](/azure/firewall)

- [Verwalten von Azure DDoS Protection Standard mithilfe des Azure-Portals](/azure/virtual-network/manage-ddos-protection) 

- [Azure Security Center-Empfehlungen](../security-center/recommendations-reference.md#recs-network)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: Bereitstellen von Angriffserkennungs-/Eindringschutzsystemen (Intrusion Detection/Intrusion Prevention Systems, IDS/IPS)

**Leitfaden**: Virtual WAN ist ein von Microsoft verwalteter Dienst. Der Dienst bietet keine nativen Funktionen für die Erkennung oder Verhinderung von Angriffen. Azure Firewall stellt jedoch Sicherheitsfunktionen für Virtual WAN bereit, um eine zentrale Richtliniensteuerung zu ermöglichen. Sie können eine Azure Firewall-Richtlinie erstellen und mit einem Virtual WAN-Hub verbinden, sodass der vorhandene Virtual WAN-Hub als geschützter virtueller Hub fungieren kann, wenn die erforderlichen Azure Firewall-Ressourcen bereitgestellt sind.

- [Konfigurieren von Azure Firewall in einem Virtual WAN-Hub](howto-firewall.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: Vereinfachen von Netzwerksicherheitsregeln

**Leitfaden**: Vereinfachen Sie Netzwerksicherheitsregeln, indem Sie Virtual Network-Diensttags verwenden, um Netzwerkzugriffssteuerungen in Netzwerksicherheitsgruppen oder Azure Firewall zu definieren. Diensttags können anstelle von spezifischen IP-Adressen verwendet werden, wenn Sie Sicherheitsregeln erstellen. Indem Sie den Diensttagnamen im Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

- [Grundlegendes zu Diensttags und deren Verwendung](../virtual-network/service-tags-overview.md)

- [Grundlegendes zu Anwendungssicherheitsgruppen und deren Verwendung](/azure/virtual-network/security-overview#application-security-groups)

- [Azure Firewall-Dokumentation](/azure/firewall/)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: Secure Domain Name Service (DNS)

**Leitfaden**: Sichere DNS-Funktionen werden Virtual WAN mit Azure Firewall bereitgestellt. Konfigurieren Sie Azure Firewall als DNS-Proxy, der als Vermittler für DNS-Anforderungen von virtuellen Clientcomputern an einen DNS-Server fungiert. In benutzerdefinierten DNS-Serverkonfigurationen aktivieren Sie den DNS-Proxy, um DNS-Auflösungskonflikte zu vermeiden, und aktivieren die FQDN-Filterung (vollqualifizierter Domänenname) in den Netzwerkregeln. 

- [Azure Firewall-Dokumentation](/azure/firewall/)

- [DNS-Einstellungen für Azure Firewall](/azure/firewall/dns-settings)

- [Use Azure Firewall as a DNS Forwarder with Private Link](https://github.com/adstuart/azure-privatelink-dns-azurefirewall) (Verwenden von Azure Firewall als DNS-Weiterleitung mit Private Link)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="identity-management"></a>Identitätsverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Identitätsverwaltung](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardisieren von Azure Active Directory als zentrales Identitäts- und Authentifizierungssystem

**Leitfaden**: Azure Active Directory (Azure AD) ist der Standarddienst für die Identitäts- und Zugriffsverwaltung für Azure-Dienste. Dies schließt Virtual WAN ein. Konfigurieren Sie Azure AD als Standardmethode für die Identitäts- und Zugriffsverwaltung Ihrer Organisation in folgenden Ressourcen:

- Microsoft-Cloudressourcen, wie z. B. das Azure-Portal, Azure Storage, Azure Virtual Machines (Linux und Windows), Azure Key Vault, PaaS- und SaaS-Anwendungen (Platform-as-a-Service bzw. Software-as-a-Service).
- Die Ressourcen Ihrer Organisation, z. B. Anwendungen in Azure oder Ihre Unternehmensnetzwerkressourcen

Räumen Sie Azure AD bei der Einrichtung der Cloudsicherheit in Ihrer Organisation hohe Priorität ein. Bewerten Sie Ihren Identitäts- und Sicherheitsstatus mit dem Feature zur Sicherheitsbewertung von Security Center, um festzustellen, wie gut Ihre Konfiguration den Best Practices-Empfehlungen von Microsoft entspricht. Implementieren Sie die Best Practices-Empfehlungen von Microsoft bei Bedarf zur Verbesserung Ihres Sicherheitsstatus.

Azure AD unterstützt auch externe Identitäten, die es Benutzern ohne Microsoft-Konto ermöglichen, sich mit ihrer externen Identität bei ihren Anwendungen und Ressourcen anzumelden. 

Lesen Sie Informationen zur Verwendung von Azure AD in Point-to-Site-VPN-Szenarien unter den angegebenen Links.

- [Erstellen eines Azure Active Directory (AD)-Mandanten für Verbindungen mit dem P2S OpenVPN-Protokoll](openvpn-azure-ad-tenant-multi-app.md)

- [Konfigurieren der Azure Active Directory-Authentifizierung für ein Benutzer-VPN](virtual-wan-point-to-site-azure-ad.md)

- [Mandanten in Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Erstellen und Konfigurieren einer Azure AD-Instanz](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Verwenden stärkerer Authentifizierungssteuerungen für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Derzeit wird die Azure AD-Authentifizierung per Integration in ein Virtual WAN-P2S-VPN bereitgestellt.

Azure Active Directory (Azure AD) ist der Standarddienst für die Identitäts- und Zugriffsverwaltung für Azure-Dienste. Azure AD unterstützt starke Authentifizierungskontrollen mit mehrstufiger Authentifizierung sowie starke kennwortlose Methoden.

Azure AD empfiehlt Folgendes für starke Authentifizierungskontrollen:

- Mehrstufige Authentifizierung: Aktivieren Sie die mehrstufige Authentifizierung von Azure AD, und befolgen Sie die Empfehlungen der Identitäts- und Zugriffsverwaltung in Azure Security Center. Erzwingen Sie die mehrstufige Authentifizierung für alle Benutzer, für ausgewählte Benutzer oder auf Benutzerebene basierend auf Anmeldebedingungen und Risikofaktoren.

- Kennwortlose Authentifizierung: Es stehen drei Optionen für die kennwortlose Authentifizierung zur Verfügung. Dazu gehören Windows Hello for Business, die Microsoft Authenticator-App und lokale Authentifizierungsmethoden wie beispielsweise Smartcards.

Stellen Sie sicher, dass für Administratoren und privilegierte Benutzer die höchste Stufe der starken Authentifizierungsmethode verwendet wird. Führen Sie für alle anderen Benutzer eine starke Authentifizierungsrichtlinie ein.

- [Aktivieren der mehrstufigen Authentifizierung in Point-to-Site-VPN für Virtual WAN](openvpn-azure-ad-mfa.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Einschränken des Zugriffs auf Azure-Ressourcen basierend auf Bedingungen

**Leitfaden**: Aktivieren Sie für VPN-Benutzer die mehrstufige Azure Active Directory-Authentifizierung (P2S). Konfigurieren Sie die mehrstufige Authentifizierung auf Benutzerbasis, oder nutzen Sie die mehrstufige Authentifizierung mit bedingtem Zugriff. Der bedingte Zugriff ermöglicht eine differenziertere Kontrolle darüber, wie ein zweiter Faktor höher gestuft werden soll. Diese Methode kann die Zuweisung der mehrstufigen Authentifizierung auf VPN-Zugriffe beschränken und andere Anwendungen ausschließen, die an den Azure AD-Mandanten gebunden sind. 

Beachten Sie, dass die Azure AD-Authentifizierung nur für Gateways mit OpenVPN-Protokoll und Clients unter Windows verfügbar ist.

- [Was ist bedingter Zugriff?](../active-directory/conditional-access/overview.md)

- [Konfigurieren der Azure Active Directory-Authentifizierung für ein Benutzer-VPN](virtual-wan-point-to-site-azure-ad.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Leitfaden**: S2S-VPN in Virtual WAN verwendet Pre-Shared Keys (PSK), die vom Kunden in der Azure Key Vault-Instanz des Kunden ermittelt, erstellt und verwaltet werden. Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen.

Für GitHub können Sie das native Feature zur geheimen Überprüfung verwenden, um Anmeldeinformationen oder eine andere Form von Geheimnissen im Code zu identifizieren.

- [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [Geheime Überprüfung in GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="privileged-access"></a>Privilegierter Zugriff

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Privilegierter Zugriff](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Einschränken des administrativen Zugriffs auf unternehmenskritische Systeme

**Leitfaden**: Azure Virtual WAN verwendet die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC), um den Zugriff auf unternehmenskritische Systeme zu isolieren, indem die Konten begrenzt werden, denen privilegierter Zugriff auf die Abonnements und Verwaltungsgruppen gewährt wird, in denen sie sich befinden.

Schränken Sie auch den Zugriff auf die Verwaltungs-, Identitäts- und Sicherheitssysteme ein, die über Administratorzugriff für Ihren unternehmenskritischen Zugriff verfügen, z. B. Active Directory-Domänencontroller, Sicherheitstools und Systemverwaltungstools mit Agents, die auf unternehmenskritischen Systemen installiert sind. Angreifer, die diese Verwaltungs- und Sicherheitssysteme kompromittieren, können diese sofort ausnutzen, um geschäftskritische Ressourcen zu gefährden.

Alle Zugriffssteuerungstypen sollten an der Segmentierungsstrategie des Unternehmens ausgerichtet werden, um eine konsistente Zugriffssteuerung sicherzustellen.

- [Azure-Komponenten und -Referenzmodell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Zugriff auf die Verwaltungsgruppe](../governance/management-groups/overview.md#management-group-access) 

- [Azure-Abonnementadministratoren](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-protection"></a>Datenschutz

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Schutz von Daten](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Verschlüsseln vertraulicher Informationen während der Übertragung

**Leitfaden**: Verwenden Sie P2S-VPN, S2S-VPN und verschlüsseltes ExpressRoute mit Virtual WAN für Ihre Konnektivitätsanforderungen. Die VPN-Verschlüsselung schützt die Daten während der Übertragung vor Out-of-Band-Angriffen (z. B. Erfassung des Datenverkehrs), um sicherzustellen, dass Angreifer keine Daten lesen oder ändern können. 

- [Point-to-Site-VPN-Verbindung](virtual-wan-point-to-site-portal.md)

- [Site-to-Site-VPN-Verbindung](virtual-wan-site-to-site-portal.md)

- [Verschlüsseltes ExpressRoute](vpn-over-expressroute.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

## <a name="asset-management"></a>Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Ressourcenverwaltung](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Sicherstellen, dass das Sicherheitsteam Risiken für Ressourcen einsehen kann

**Leitfaden**: Stellen Sie sicher, dass Sicherheitsteams die Berechtigungen „Sicherheitsleseberechtigter“ in Ihrem Azure-Mandanten und Ihren Abonnements erhalten, damit sie mit Azure Security Center Sicherheitsrisiken überwachen können. 

Abhängig davon, wie die Verantwortungsbereiche des Sicherheitsteams strukturiert sind, kann die Überwachung auf Sicherheitsrisiken unter die Verantwortung eines zentralen Sicherheitsteams oder eines lokalen Teams fallen. Daher müssen Sicherheitserkenntnisse und -risiken innerhalb einer Organisation immer zentral aggregiert werden. 

Die Berechtigungen der Rolle „Sicherheitsleseberechtigter“ können auf einen vollständigen Mandanten (Stammverwaltungsgruppe) angewendet oder auf Verwaltungsgruppen oder bestimmte Abonnements beschränkt werden. 

Hinweis: Möglicherweise sind zusätzliche Berechtigungen erforderlich, um Einblick in Workloads und Dienste zu erhalten. 

- [Übersicht über die Rolle „Sicherheitsleseberechtigter“](../role-based-access-control/built-in-roles.md#security-reader)

- [Übersicht über Azure-Verwaltungsgruppen](../governance/management-groups/overview.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Sicherstellen, dass das Sicherheitsteam Zugriff auf den Ressourcenbestand und die Metadaten hat

**Leitfaden**: Wenden Sie Tags auf Ihre Ressourcen, Ressourcengruppen und Abonnements in Azure an, um sie logisch in einer Taxonomie zu strukturieren. Jedes Tag besteht aus einem Paar mit einem Namen und einem Wert. So können Sie beispielsweise den Namen „Umgebung“ und den Wert „Produktion“ auf alle Ressourcen in der Produktion anwenden. Azure Virtual WAN unterstützt auch auf Azure Resource Manager basierende Ressourcenbereitstellungen, über die Sie Ressourcenvorlagen exportieren können. 

- [Leitfaden zur Entscheidungsfindung für Ressourcenbenennung und -markierung](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

- [Verwalten des Ressourcenbestands in Azure Security Center](../security-center/asset-inventory.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Monitor, um Regeln zu erstellen, mit denen Warnungen ausgelöst werden, wenn ein nicht genehmigter Dienst erkannt wird. Virtual WAN vereint viele Netzwerk-, Sicherheits- und Routingfunktionen auf einer einzigen Bedienoberfläche. Für Virtual WAN-VPN-Gateways, ExpressRoute-Gateways und Azure Firewall sind über Azure Monitor Protokollierungsfunktionen und Metriken verfügbar. 
 

- [Virtual WAN-Protokolle und -Metriken](logs-metrics.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden**: Verwenden Sie den bedingten Azure-Zugriff, um die Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.

- [Verwalten des Zugriffs auf die Azure-Verwaltung mit bedingtem Zugriff](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-threat-detection"></a>Protokollierung und Bedrohungserkennung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Protokollierung und Bedrohungserkennung](/azure/security/benchmarks/security-controls-v2-logging-threat-detection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Aktivieren der Bedrohungserkennung für Azure-Ressourcen

**Leitfaden**: Point-to-Site-VPN mit Virtual WAN ist in Azure Active Directory (Azure AD) integriert. Azure AD stellt die folgenden Benutzerprotokolle bereit, die in der Azure AD-Berichterstellung angezeigt oder für komplexere Anwendungsfälle in Bezug auf Bedrohungsüberwachung und -analyse in Azure Monitor, Azure Sentinel, SIEM oder andere Überwachungstools integriert werden können. Dies sind:

- Anmeldungen: Der Bericht „Anmeldungen“ enthält Informationen zur Nutzung von verwalteten Anwendungen und Aktivitäten der Benutzeranmeldung.
- Überwachungsprotokolle: Ermöglichen die Nachverfolgung sämtlicher Änderungen, die von verschiedenen Features in Azure AD vorgenommen wurden. Hierzu zählen unter anderem Änderungen an Ressourcen in Azure AD, z. B. das Hinzufügen oder Entfernen von Benutzern, Apps, Gruppen, Rollen und Richtlinien.
- Riskante Anmeldungen: Eine riskante Anmeldung ist ein Indikator für einen Anmeldeversuch von einem Benutzer, der nicht der rechtmäßige Besitzer eines Benutzerkontos ist.
- Benutzer mit Risikomarkierung: Ein Benutzer mit Risikomarkierung ist ein Indikator für ein ggf. kompromittiertes Benutzerkonto.

Verwenden Sie Azure Security Center zum Erstellen von Warnungen zu bestimmten verdächtigen Aktivitäten, z. B. eine übermäßige Anzahl fehlgeschlagener Authentifizierungsversuche, einschließlich veralteter Konten im Abonnement. Verwenden Sie zusätzlich zur grundlegenden Sicherheitsüberwachung das Threat Protection-Modul von Security Center, um ausführlichere Sicherheitswarnungen von einzelnen Azure-Computeressourcen (virtuelle Computer, Container, App Service), Datenressourcen (SQL-Datenbank und SQL-Speicher) und Azure-Dienstebenen zu sammeln. So erhalten Sie Einblick in Kontoanomalien innerhalb einzelner Ressourcen.

- [Berichte zu Überwachungsaktivitäten in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Aktivieren von Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Konfigurieren von Azure Firewall in einem Virtual WAN-Hub](howto-firewall.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Aktivieren der Bedrohungserkennung für die Identitäts- und Zugriffsverwaltung in Azure

**Leitfaden**: Point-to-Site-VPN mit Virtual WAN ist in Azure Active Directory (Azure AD) integriert. Azure AD stellt die folgenden Benutzerprotokolle bereit, die in der Azure AD-Berichterstellung angezeigt oder für komplexere Anwendungsfälle in Bezug auf Bedrohungsüberwachung und -analyse in Azure Monitor, Azure Sentinel, SIEM oder andere Überwachungstools integriert werden können. Dies sind:

- Anmeldungen: Der Bericht „Anmeldungen“ enthält Informationen zur Nutzung von verwalteten Anwendungen und Aktivitäten der Benutzeranmeldung.
- Überwachungsprotokolle: Ermöglichen die Nachverfolgung sämtlicher Änderungen, die von verschiedenen Features in Azure AD vorgenommen wurden. Hierzu zählen unter anderem Änderungen an Ressourcen in Azure AD, z. B. das Hinzufügen oder Entfernen von Benutzern, Apps, Gruppen, Rollen und Richtlinien.
- Riskante Anmeldungen: Eine riskante Anmeldung ist ein Indikator für einen Anmeldeversuch von einem Benutzer, der nicht der rechtmäßige Besitzer eines Benutzerkontos ist.
- Benutzer mit Risikomarkierung: Ein Benutzer mit Risikomarkierung ist ein Indikator für ein ggf. kompromittiertes Benutzerkonto.

Verwenden Sie Azure Security Center zum Erstellen von Warnungen zu bestimmten verdächtigen Aktivitäten, z. B. eine übermäßige Anzahl fehlgeschlagener Authentifizierungsversuche, einschließlich veralteter Konten im Abonnement. Verwenden Sie zusätzlich zur grundlegenden Sicherheitsüberwachung das Threat Protection-Modul von Security Center, um ausführlichere Sicherheitswarnungen von einzelnen Azure-Computeressourcen (virtuelle Computer, Container, App Service), Datenressourcen (SQL-Datenbank und SQL-Speicher) und Azure-Dienstebenen zu sammeln. So erhalten Sie Einblick in Kontoanomalien innerhalb einzelner Ressourcen.

- [Berichte zu Überwachungsaktivitäten in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Aktivieren von Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Konfigurieren von Azure Firewall in einem Virtual WAN-Hub](howto-firewall.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Aktivieren der Protokollierung für Azure-Netzwerkaktivitäten

**Leitfaden**: Überwachen Sie Azure Virtual WAN mit Azure Monitor. Virtual WAN vereint viele Netzwerk-, Sicherheits- und Routingfunktionen auf einer einzigen Bedienoberfläche. Für Virtual WAN-VPN-Gateways, ExpressRoute-Gateways und Azure Firewall sind über Azure Monitor Protokollierungsfunktionen und Metriken verfügbar. Einträge im Aktivitätsprotokoll werden standardmäßig erfasst und können im Azure-Portal angezeigt werden. Mit Azure-Aktivitätsprotokollen (ehemals Betriebsprotokolle und Überwachungsprotokolle) können Sie alle an Ihr Azure-Abonnement übermittelten Vorgänge anzeigen.

Es stehen auch viele Diagnoseprotokolle für Virtual WAN bereit, die im Azure-Portal für die jeweilige Virtual WAN-Ressource konfiguriert werden können.  Sie können die Daten an Log Analytics senden, an einen Event Hub streamen oder einfach in einem Speicherkonto archivieren. 
 

- [Virtual WAN-Protokolle und -Metriken](logs-metrics.md)

- [Azure Firewall-Protokolle und -Metriken](/azure/firewall/logs-and-metrics)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Aktivieren der Protokollierung für Azure-Ressourcen

**Leitfaden**: Azure-Aktivitätsprotokolle, die automatisch aktiviert werden, enthalten alle Schreibvorgänge (PUT, POST, DELETE) für Ihre Azure Virtual WAN-Ressourcen, mit Ausnahme von Lesevorgängen (GET). Mit Aktivitätsprotokollen können Fehler ermittelt werden, und es kann nachverfolgt werden, welche Änderungen an einer Ressource ein Benutzer in Ihrer Organisation vorgenommen hat.

Aktivieren Sie Azure-Ressourcenprotokolle für Virtual WAN. Sie können Azure Security Center und Azure Policy verwenden, um Ressourcenprotokolle zu aktivieren und Datenerfassung zu protokollieren. Diese Protokolle können für die spätere Untersuchung von Sicherheitsvorfällen und die Durchführung forensischer Übungen von entscheidender Bedeutung sein.

- [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Grundlegendes zur Protokollierung und zu verschiedenen Protokolltypen in Azure](../azure-monitor/platform/platform-logs-overview.md) 

- [Grundlegendes zur Datensammlung in Azure Security Center](../security-center/security-center-enable-data-collection.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Zentralisieren der Verwaltung und Analyse von Sicherheitsprotokollen

**Leitfaden**: Aktivieren Sie die Sicherheitsprotokollierung für Azure Virtual WAN mit Azure Monitor. Virtual WAN vereint viele Netzwerk-, Sicherheits- und Routingfunktionen auf einer einzigen Bedienoberfläche. Für Virtual WAN-VPN-Gateways, ExpressRoute-Gateways und Azure Firewall sind über Azure Monitor Protokollierungsfunktionen und Metriken verfügbar. Einträge im Aktivitätsprotokoll werden standardmäßig erfasst und können im Azure-Portal angezeigt werden. Mit Azure-Aktivitätsprotokollen (ehemals Betriebsprotokolle und Überwachungsprotokolle) können Sie alle an Ihr Azure-Abonnement übermittelten Vorgänge anzeigen. 

Es stehen auch viele Diagnoseprotokolle für Virtual WAN bereit, die im Azure-Portal für die jeweilige Virtual WAN-Ressource konfiguriert werden können. Sie können Daten an Log Analytics senden, an einen Ereignishub streamen oder einfach in einem Speicherkonto archivieren. Darüber hinaus können Sie Daten in Azure Sentinel oder eine SIEM-Lösung eines Drittanbieters integrieren und dort weiter nutzen. 
 

- [Virtual WAN-Protokolle und -Metriken](logs-metrics.md)

- [Azure Firewall-Protokolle und -Metriken](/azure/firewall/logs-and-metrics)

Die Sicherheit für Azure Virtual WAN wird über Azure Firewall bereitgestellt. 

- [Azure Firewall-Dokumentation](/azure/firewall/overview)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurieren der Aufbewahrungsdauer von Protokollen im Speicher

**Leitfaden**: Konfigurieren Sie die Aufbewahrungsdauer der Protokolle entsprechend Ihrer Compliance, Vorschriften und geschäftlichen Anforderungen. In Azure Monitor können Sie den Aufbewahrungszeitraum des Log Analytics-Arbeitsbereichs gemäß den Compliancevorschriften Ihres Unternehmens festlegen. Verwenden Sie für langfristige Speicherungen und Archivierungen Konten von Azure Storage, Data Lake oder des Log Analytics-Arbeitsbereichs.

- [Ändern des Datenaufbewahrungszeitraums in Protokollanalyse](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Konfigurieren der Aufbewahrungsrichtlinie für Azure Storage-Kontoprotokolle](../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Exportieren von Azure Security Center-Warnungen und -Empfehlungen](../security-center/continuous-export.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

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

Das Azure Security Center bietet qualitativ hochwertige Warnungen für viele Azure-Ressourcen. Sie können den ASC-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen. Mit Azure Sentinel können Sie erweiterte Warnregeln erstellen, um Vorfälle automatisch für eine Untersuchung zu generieren. 

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

Azure Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

Markieren Sie Ressourcen außerdem mithilfe von Tags, und erstellen Sie ein Benennungssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren – insbesondere solche, von denen vertrauliche Daten verarbeitet werden.  Die Priorisierung der Behebung von Warnungen basierend auf der Wichtigkeit der Azure-Ressourcen und der Umgebung, in der der Vorfall aufgetreten ist, liegt in Ihrer Verantwortung.

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

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Regelmäßiges Durchführen einer Angriffssimulation

**Leitfaden**: Führen Sie nach Bedarf Penetrationstests oder Red Team-Aktivitäten für Ihre Azure-Ressourcen durch, und stellen Sie sicher, dass alle kritischen Sicherheitsergebnisse behandelt werden.
Befolgen Sie die Einsatzregeln für Penetrationstests für die Microsoft Cloud, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen. Nutzen Sie die Microsoft-Strategie und Durchführung von Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastruktur, Dienste und Anwendungen.

- [Penetrationstests in Azure](../security/fundamentals/pen-testing.md)

- [Penetrationstests – Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="endpoint-security"></a>Endpunktsicherheit

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Endpunktsicherheit](/azure/security/benchmarks/security-controls-v2-endpoint-security).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: Verwenden von Endpunkterkennung und -antwort (Endpoint Detection and Response, EDR)

**Leitfaden**: Kunden ist es nicht ausdrücklich gestattet, Einstellungen für die Endpunkterkennung und -antwort zu konfigurieren. Die im Azure Virtual WAN-Angebot verwendeten virtuellen Computer verwenden jedoch diese Funktionen. Weitere Informationen zu diesen allgemeinen Funktionen finden Sie unter den angegebenen Links. 

- [Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Durchführen des Onboardings von Windows-Servern für den Microsoft Defender ATP-Dienst](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Durchführen des Onboardings für Windows-fremde Server](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

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

- [Azure-Sicherheitsvergleichstest: Identitätsverwaltung](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure-Sicherheitsvergleichstest: Privilegierter Zugriff](/azure/security/benchmarks//security-controls-v2-privileged-access)

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

- Sehen Sie sich die [Übersicht über Version 2 des Azure-Sicherheitsvergleichstests](/azure/security/benchmarks/overview) an.
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](/azure/security/benchmarks/security-baselines-overview).

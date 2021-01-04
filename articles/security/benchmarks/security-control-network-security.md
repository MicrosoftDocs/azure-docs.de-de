---
title: Azure-Sicherheitskontrolle – Netzwerksicherheit
description: Azure-Sicherheitskontrolle – Netzwerksicherheit
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 3a232f8e8c35e265a8243ac79e465c03f6b9650e
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487863"
---
# <a name="security-control-network-security"></a>Sicherheitskontrolle: Netzwerksicherheit

Bei den Netzwerksicherheitsempfehlungen liegt der Schwerpunkt auf der Angabe, welchen Netzwerkprotokollen, TCP/UDP-Ports und mit dem Netzwerk verbundenen Diensten der Zugriff auf Azure-Dienste erlaubt oder verweigert wird.

## <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Schützen von Azure-Ressourcen in virtuellen Netzwerken

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 1.1 | 9.2, 9.4, 14.1, 14.2, 14.3 | Kunde |

Stellen Sie sicher, dass auf alle Virtual Network-Subnetzbereitstellungen eine Netzwerksicherheitsgruppe mit Netzwerkzugriffssteuerungen angewendet wurde, die für die vertrauenswürdigen Ports und Quellen der Anwendung spezifisch sind. Verwenden Sie bei Verfügbarkeit private Endpunkte mit Private Link, um Ihre Azure-Dienstressourcen in Ihrem virtuellen Netzwerk zu schützen, indem Sie die VNet-Identität auf den Dienst ausweiten. Wenn private Endpunkte und Private Link nicht verfügbar sind, verwenden Sie Dienstendpunkte. Informationen zu dienstspezifischen Anforderungen finden Sie in der Sicherheitsempfehlung für den jeweiligen Dienst. 

Sollte Ihr Anwendungsfall speziell sein, können die Anforderungen alternativ auch durch Implementieren von Azure Firewall erfüllt werden.

- [Grundlegendes zu VNET-Dienstendpunkten](../../virtual-network/virtual-network-service-endpoints-overview.md)

- [Grundlegendes zu Azure Private Link](../../private-link/private-link-overview.md)

- [Erstellen eines virtuellen Netzwerks](../../virtual-network/quick-create-portal.md)

- [Erstellen einer NSG mit einer Sicherheitskonfiguration](../../virtual-network/tutorial-filter-network-traffic.md)

- [Bereitstellen und Konfigurieren von Azure Firewall](../../firewall/tutorial-firewall-deploy-portal.md)

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von virtuellen Netzwerken, Subnetzen und Netzwerkkarten (NICs)

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 1.2 | 9.3, 12.2, 12.8 | Kunde |

Verwenden Sie Azure Security Center, und befolgen Sie die Empfehlungen für den Netzwerkschutz, um Ihre Netzwerkressourcen in Azure abzusichern. Aktivieren Sie NSG-Flussprotokolle und senden Sie Protokolle an ein Speicherkonto für die Datenverkehrsüberwachung. Sie können auch NSG-Flussprotokolle an einen Log Analytics-Arbeitsbereich senden und mithilfe von Traffic Analytics Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

- [Aktivieren der NSG-Flussprotokolle](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Aktivieren und Verwenden von Traffic Analytics](../../network-watcher/traffic-analytics.md)

- [Grundlegendes zu der von Azure Security Center bereitgestellten Netzwerksicherheit](../../security-center/security-center-network-recommendations.md)

## <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 1.3 | 9.5 | Kunde |

Stellen Sie die Azure Web Application Firewall (WAF) vor kritischen Webanwendungen bereit, um den eingehenden Datenverkehr weiteren Überprüfungen zu unterziehen. Aktivieren Sie die Diagnoseeinstellung für WAF, und erfassen Sie Protokolle in ein Speicherkonto, einen Event Hub oder einen Log Analytics-Arbeitsbereich.

- [Bereitstellen von Azure WAF](../../web-application-firewall/ag/create-waf-policy-ag.md)

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten bösartigen IP-Adressen

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 1.4 | 12,3 | Kunde |

Aktivieren Sie den DDoS-Standardschutz in Ihren virtuellen Azure-Netzwerken, um sie vor DDoS-Angriffe zu schützen. Verwenden Sie die integrierten Informationen zu Bedrohungen in Azure Security Center, um die Kommunikation mit bekannten schädlichen IP-Adressen zu verweigern.

Stellen Sie die Azure Firewall an allen Netzwerkgrenzen der Organisation bereit, aktiviert Sie Threat Intelligence, und konfigurieren Sie das Feature so, dass schädlicher Netzwerkdatenverkehr „mit Warnungen gemeldet und verweigert“ wird.

Verwenden Sie den Just-in-Time-Netzwerkzugriff in Azure Security Center, um NSGs so zu konfigurieren, dass die Exposition von Endpunkten auf genehmigte IP-Adressen und einen begrenzten Zeitraum eingeschränkt wird.

Verwenden Sie die adaptive Netzwerkhärtung in Azure Security Center, um NSG-Konfigurationen zu empfehlen, die Ports und Quell-IPs basierend auf tatsächlichem Datenverkehr und Threat Intelligence einschränken.

- [Konfigurieren von DDoS-Schutz](../../ddos-protection/manage-ddos-protection.md)

- [Bereitstellen von Azure Firewall](../../firewall/tutorial-firewall-deploy-portal.md)

- [Grundlegendes zur integrierten Threat Intelligence in Azure Security Center](../../security-center/azure-defender.md)

- [Grundlegendes zur adaptiven Netzwerkhärtung von Azure Security Center](../../security-center/security-center-adaptive-network-hardening.md)

- [Grundlegendes zur Just-in-Time-Netzwerkzugriffssteuerung in Azure Security Center](../../security-center/security-center-just-in-time.md)

## <a name="15-record-network-packets"></a>1.5: Aufzeichnen von Netzwerkpaketen

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 1.5 | 12,5 | Kunde |

Aktivieren Sie die Network Watcher-Paketerfassung, um anomale Aktivitäten zu untersuchen.

- [Aktivieren von Network Watcher](../../network-watcher/network-watcher-create.md)

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (IDS/IPS)

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 1.6 | 12.6, 12.7 | Kunde |

Wählen Sie ein Angebot aus Azure Marketplace aus, das IDS-/IPS-Funktionen mit Funktionen zur Nutzlastuntersuchung unterstützt.  Wenn Angriffserkennung und/oder -verhinderung auf der Grundlage der Nutzlastüberprüfung keine Anforderung ist, kann Azure Firewall mit Threat Intelligence verwendet werden. Das Filtern auf Basis von Threat Intelligence mit Azure Firewall kann vor Datenverkehr von und zu bekannten schädlichen IP-Adressen oder Domänen warnen und diesen verweigern. Die IP-Adressen und Domänen stammen aus dem Microsoft Threat Intelligence-Feed.

Stellen Sie die Firewalllösung Ihrer Wahl an allen Netzwerkgrenzen Ihrer Organisation bereit, um schädlichen Datenverkehr zu erkennen und/oder zu verweigern.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Bereitstellen von Azure Firewall](../../firewall/tutorial-firewall-deploy-portal.md)

- [Konfigurieren von Warnungen mit Azure Firewall](../../firewall/threat-intel.md)

## <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 1.7 | 12.9, 12.10 | Kunde |

Stellen Sie Azure Application Gateway für Webanwendungen mit aktiviertem HTTPS/TLS für vertrauenswürdige Zertifikate bereit.

- [Bereitstellen von Application Gateway](../../application-gateway/quick-create-portal.md)

- [Konfigurieren von Application Gateway für die Verwendung von HTTPS](../../application-gateway/create-ssl-portal.md)

- [Grundlegendes zum Lastenausgleich der Ebene 7 mit Azure-Webanwendungsgateways](../../application-gateway/overview.md)

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 1.8 | 1.5 | Kunde |

Verwenden Sie Virtual Network-Diensttags, um Netzwerkzugriffssteuerungen in Netzwerksicherheitsgruppen oder Azure Firewall zu definieren. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Indem Sie den Diensttagnamen (z. B. „ApiManagement“) im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

Sie können auch Anwendungssicherheitsgruppen verwenden, um eine komplexe Sicherheitskonfiguration zu vereinfachen. Mit Anwendungssicherheitsgruppen können Sie die Netzwerksicherheit als natürliche Erweiterung einer Anwendungsstruktur konfigurieren und virtuelle Computer gruppieren sowie auf der Grundlage dieser Gruppen Netzwerksicherheitsrichtlinien definieren.

- [Grundlegendes zu Diensttags und deren Verwendung](../../virtual-network/service-tags-overview.md)

- [Grundlegendes zu Anwendungssicherheitsgruppen und deren Verwendung](../../virtual-network/network-security-groups-overview.md#application-security-groups)

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 1.9 | 11,1 | Kunde |

Definieren und implementieren Sie Standardsicherheitskonfigurationen für Netzwerkressourcen mit Azure Policy.

Sie können umfangreiche Azure-Bereitstellungen auch mithilfe von Azure Blueprints vereinfachen, indem Sie wichtige Umgebungsartefakte wie Azure Resource Manager-Vorlagen, Azure RBAC-Kontrollen und Richtlinien in einer einzigen Blaupausendefinition packen. Sie können die Blaupause auf neue Abonnements anwenden sowie die Steuerung und Verwaltung durch die Versionsverwaltung optimieren.

- [Konfigurieren und Verwalten von Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy-Beispiele für Netzwerke](../../governance/policy/samples/built-in-policies.md#network)

- [Erstellen einer Azure-Blaupause](../../governance/blueprints/create-blueprint-portal.md)

## <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 1.10 | 11.2 | Kunde |

Verwenden Sie Tags für NSGs und andere Ressourcen im Zusammenhang mit Netzwerksicherheit und Datenverkehrsfluss. Verwenden Sie für einzelne NSG-Regeln das Feld „Beschreibung“, um geschäftliche Anforderungen und/oder deren Dauer (usw.) für alle Regeln festzulegen, die Datenverkehr in ein bzw. aus einem Netzwerk zulassen.

Verwenden Sie eine der integrierten Azure Policy-Definitionen zum Tagging, z. B. „Tag und zugehörigen Wert erzwingen“, um sicherzustellen, dass alle Ressourcen mit Tags erstellt werden und Sie über vorhandene nicht markierte Ressourcen benachrichtigt werden.

Sie können Azure PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden, um Ressourcen basierend auf ihren Tags zu suchen oder Aktionen auszuführen.

- [Erstellen und Verwenden von Tags](../../azure-resource-manager/management/tag-resources.md)

- [Erstellen eines virtuellen Netzwerks](../../virtual-network/quick-create-portal.md)

- [Erstellen einer NSG mit einer Sicherheitskonfiguration](../../virtual-network/tutorial-filter-network-traffic.md)

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 1.11 | 11,3 | Kunde |

Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen von Ressourcenkonfigurationen und Erkennen von Änderungen an Ihren Azure-Ressourcen. Erstellen Sie in Azure Monitor Warnungen, die bei Änderungen an wichtigen Ressourcen ausgelöst werden.

- [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Erstellen von Warnungen in Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md)

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie in der nächsten Sicherheitskontrolle: [Protokollierung und Überwachung](security-control-logging-monitoring.md)
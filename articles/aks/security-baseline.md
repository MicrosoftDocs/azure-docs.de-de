---
title: Azure-Sicherheitsbaseline für Azure Kubernetes Service
description: Die Sicherheitsbaseline für Azure Kubernetes Service enthält schrittweise Anleitungen und Ressourcen für die Implementierung der Sicherheitsempfehlungen aus dem Vergleichstest für Azure-Sicherheit.
author: msmbaldwin
ms.service: container-service
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 29bcf2fdd3e20b28b1f2cb014137cfba37ccaf2e
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931808"
---
# <a name="azure-security-baseline-for-azure-kubernetes-service"></a>Azure-Sicherheitsbaseline für Azure Kubernetes Service

Die Azure-Sicherheitsbaseline für Azure Kubernetes Service enthält Empfehlungen, mit deren Hilfe Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können.

Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](../security/benchmarks/overview.md) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können.

Weitere Informationen finden Sie unter [Übersicht über Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Schützen von Azure-Ressourcen in virtuellen Netzwerken

**Leitfaden**: Standardmäßig werden bei der Erstellung eines Microsoft Azure Kubernetes Service-Clusters (AKS) automatisch eine Netzwerksicherheitsgruppe und eine Routentabelle erstellt. AKS ändert automatisch die Netzwerksicherheitsgruppen für einen angemessenen Datenverkehrsfluss, wenn Dienste mit Lastenausgleich, Portzuordnungen oder Eingangsrouten erstellt werden. Die Netzwerksicherheitsgruppe wird automatisch mit den virtuellen NICs auf Kundenknoten und die Routingtabelle mit dem Subnetz im virtuellen Netzwerk verknüpft. 

Verwenden Sie AKS-Netzwerkrichtlinien, um den Netzwerkdatenverkehr einzuschränken, indem Sie Regeln für den eingehenden und ausgehenden Datenverkehr zwischen Linux-Pods in einem Cluster anhand der Auswahl von Namespaces und Bezeichnern definieren. Die Verwendung von Netzwerkrichtlinien erfordert das Azure CNI-Plug-In mit definiertem virtuellem Netzwerk und Subnetzen und kann nur bei der Clustererstellung aktiviert werden. Sie können nicht für einen vorhandenen AKS-Cluster bereitgestellt werden.

Sie können einen privaten AKS-Cluster implementieren, um sicherzustellen, dass der Netzwerkdatenverkehr zwischen Ihrem AKS-API-Server und den Knotenpools ausschließlich im privaten Netzwerk verbleibt. Die Steuerungsebene oder der API-Server befindet sich in einem von AKS verwalteten Azure-Abonnement und verwendet interne IP-Adressen (RFC1918), während sich der Cluster oder der Knotenpool des Kunden in einem eigenen Abonnement befinden. Der Server und der Cluster oder Knotenpool können mithilfe des Azure Private Link-Diensts im virtuellen Netzwerk des API-Servers und eines privaten Endpunkts, der im Subnetz des AKS-Clusters des Kunden verfügbar gemacht wird, miteinander kommunizieren.  Alternativ können Sie einen öffentlichen Endpunkt für den AKS-API-Server verwenden, den Zugriff jedoch mit der Funktion „Autorisierten IP-Bereiche“ des AKS-API-Servers einschränken. 

- [Sicherheitskonzepte für Anwendungen und Cluster in Azure Kubernetes Service (AKS)](concepts-security.md)

- [Sicherer Datenverkehr zwischen Pods durch Netzwerkrichtlinien in Azure Kubernetes Service (AKS)](use-network-policies.md)

- [Erstellen eines privaten Azure Kubernetes Service-Clusters](private-clusters.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von virtuellen Netzwerken, Subnetzen und Netzwerkkarten (NICs)

**Leitfaden**: Verwenden Sie Security Center und befolgen Sie die Empfehlungen zum Netzwerkschutz, um die von Ihren AKS-Clustern (Azure Kubernetes Service) verwendeten Netzwerkressourcen zu schützen. 

Aktivieren Sie Netzwerksicherheitsgruppen-Datenflussprotokolle, und senden Sie Protokolle zur Überwachung an ein Azure Storage-Konto. Sie können die Datenflussprotokolle auch an einen Log Analytics-Arbeitsbereich senden und dann Traffic Analytics verwenden, um Einblicke in die Datenverkehrsmuster in Ihrer Azure-Cloud bereitzustellen, um die Netzwerkaktivität zu visualisieren, Hotspots und Sicherheitsbedrohungen zu erkennen, Datenflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

- [Grundlegendes zu der von Azure Security Center bereitgestellten Netzwerksicherheit](../security-center/security-center-network-recommendations.md)

- [Aktivieren von Netzwerksicherheits-Datenflussprotokollen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Aktivieren und Verwenden von Traffic Analytics](../network-watcher/traffic-analytics.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Leitfaden**: Verwenden Sie eine für Azure Application Gateway aktivierte Web Application Firewall (WAF) vor einem AKS-Cluster, um eine zusätzliche Sicherheitsebene bereitzustellen, indem Sie den eingehenden Datenverkehr für Ihre Webanwendungen filtern. Azure WAF verwendet eine Sammlung von Regeln, die von OWASP (Open Web Application Security Project) bereitgestellt werden, um den Datenverkehr auf Angriffe wie websiteübergreifende Skripts oder Cookie-Poisoning zu überwachen. 

Verwenden Sie ein API-Gateway für Authentifizierung, Autorisierung, Drosselung, Zwischenspeicherung, Transformation und Überwachung für APIs, die in ihrer AKS-Umgebung verwendet werden. Ein API-Gateway dient als Einstiegspunkt für die Microservices, entkoppelt Clients von Ihren Microservices und verringert die Komplexität ihrer Microservices, da die Behandlung von bereichsübergreifenden Problemen entfällt.

- [Informationen zu bewährten Methoden für Netzwerkkonnektivität und Sicherheit in AKS](operator-best-practices-network.md)

- [Application Gateway-Eingangscontroller](../application-gateway/ingress-controller-overview.md)

- [Verwenden von API Management mit in Azure Kubernetes Service bereitgestellten Microservices](../api-management/api-management-kubernetes.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten bösartigen IP-Adressen

**Leitfaden**: Aktivieren Sie Microsoft DDoS-Standardschutz (Distributed Denial-of-Service) für die virtuellen Netzwerke, in denen Komponenten von Azure Kubernetes Service (AKS) zum Schutz vor DDoS-Angriffen bereitgestellt werden.
Installieren Sie das Modul für Netzwerkrichtlinien, und erstellen Sie Kubernetes-Netzwerkrichtlinien, um den Datenverkehrsfluss zwischen Pods in AKS zu steuern, da der gesamte Datenverkehr zwischen diesen Pods standardmäßig zulässig ist. Netzwerkrichtlinien sollten nur für Linux-basierte Knoten und Pods in AKS verwendet werden. Definieren Sie Regeln, um die Kommunikation zwischen den Pods einzuschränken und die Sicherheit zu verbessern. 

Anhand von Einstellungen wie zugewiesene Bezeichnungen, Namespace oder Port für den Datenverkehr können Sie Datenverkehr zulassen oder verweigern. Da Pods in einem AKS-Cluster dynamisch erstellt werden, können automatisch die erforderlichen Netzwerkrichtlinien angewendet werden. 

- [Sicherer Datenverkehr zwischen Pods durch Netzwerkrichtlinien in Azure Kubernetes Service (AKS)](use-network-policies.md)

- [Konfigurieren von DDoS-Schutz](../ddos-protection/manage-ddos-protection.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets"></a>1.5: Aufzeichnen von Netzwerkpaketen

**Leitfaden**: Aktivieren Sie Network Watcher-Paketerfassung, falls dies für die Untersuchung anomaler Aktivitäten erforderlich ist. 

Beim Erstellen oder Aktualisieren eines virtuellen Netzwerks in Ihrem Abonnement wird Network Watcher automatisch in der Region Ihres virtuellen Netzwerks aktiviert. Sie können auch neue Instanzen von Network Watcher mit PowerShell, der Azure CLI, der REST-API oder der ARMClient-Methode erstellen.

- [Aktivieren von Network Watcher](../network-watcher/network-watcher-create.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (Intrusion Detection/Intrusion Prevention Systems, IDS/IPS)

**Leitfaden**: Sichern Sie Ihren AKS-Cluster (Azure Kubernetes Service) mit einem Azure Application Gateway, das mit einer Web Application Firewall (WAF) aktiviert ist. 

Wenn die Angriffserkennung und/oder -verhinderung auf der Basis von Nutzlastuntersuchung oder Verhaltensanalyse nicht erforderlich ist, kann ein Azure Application Gateway mit WAF verwendet und im „Erkennungsmodus“ zur Protokollierung von Warnungen und Bedrohungen oder im „Verhinderungsmodus“ zur aktiven Blockierung erkannter Eindringversuche und Angriffe konfiguriert werden.

- [Informationen zu bewährten Methoden zum Sichern Ihres AKS-Clusters mit WAF](operator-best-practices-network.md#secure-traffic-with-a-web-application-firewall-waf)

- [Bereitstellen von Azure Application Gateway (Azure WAF)](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Leitfaden**: Verwenden Sie eine für Azure Application Gateway aktivierte Web Application Firewall (WAF) vor einem AKS-Cluster, um den eingehenden Datenverkehr zu filtern. Das Open Web Application Security Project (OWASP) bietet eine Reihe von Regeln, die in Azure WAF verwendet werden, um den Datenverkehr auf Angriffe wie websiteübergreifende Skripts oder Cookie-Poisoning zu überwachen.

Wenden Sie FQDN-Tags (Fully Qualified Domain Name, vollqualifizierter Domänenname) auf Anwendungen an, um das Einrichten von Anwendungsregeln innerhalb einer Netzwerksicherheitsgruppe zu vereinfachen. Nach dem Einrichten der Netzwerksicherheitsregeln. Fügen Sie eine Anwendungsregel mithilfe eines FQDN-Tags (z. B. AzureKubernetesService) hinzu, in dem alle erforderlichen FQDNs enthalten sind, auf die über TCP-Port 443 und Port 80 zugegriffen werden kann. 

- [Informationen zu bewährten Methoden für Netzwerkkonnektivität und Sicherheit in AKS](operator-best-practices-network.md)

- [Sicherer Datenverkehr zwischen Pods durch Netzwerkrichtlinien in Azure Kubernetes Service (AKS)](use-network-policies.md)

- [Bereitstellen von Azure Application Gateway (Azure WAF)](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Verwenden Sie Virtual Network-Diensttags, um Netzwerkzugriffssteuerungen für Netzwerksicherheitsgruppen zu definieren, die Ihren Azure AKS-Instanzen (Azure Kubernetes Service) zugeordnet sind. Diensttags können anstelle spezifischer IP-Adressen verwendet werden, wenn Sicherheitsregeln erstellt werden, um den Datenverkehr für den entsprechenden Dienst durch Angabe des Diensttagnamens zuzulassen oder abzulehnen. 

Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

Wenden Sie ein Azure-Tag auf Knotenpools in Ihrem AKS-Cluster an. Diese Tags unterscheiden sich von den Diensttags in virtuellen Netzwerken und werden auf jeden Knoten im Knotenpool angewendet und bleiben über Upgrades hinweg erhalten. 

- [Grundlegendes zu Diensttags und Verwenden von Diensttags](../virtual-network/service-tags-overview.md)

- [Grundlegendes zu Netzwerksicherheitsgruppen für AKS](support-policies.md)

- [Steuern des ausgehenden Datenverkehrs für Clusterknoten in Azure Kubernetes Service (AKS)](limit-egress-traffic.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen mit Azure Policy für Netzwerkressourcen, die Ihren AKS-Clustern (Azure Kubernetes Service) zugeordnet sind. Verwenden Sie Azure Policy-Aliase in den Namespaces „Microsoft.ContainerService“ und „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihrer AKS-Cluster zu erstellen. 

Verwenden Sie außerdem integrierte Richtliniendefinitionen, die sich auf AKS beziehen, z. B.:

• In Kubernetes Services sollten autorisierte IP-Adressbereiche definiert werden.

• Eingehenden HTTPS-Datenverkehr im Kubernetes-Cluster erzwingen.

• Sicherstellen, dass Dienste nur an zulässigen Ports im Kubernetes-Cluster lauschen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy-Beispiele für Netzwerke](../governance/policy/samples/built-in-policies.md#network)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Verwenden von Tags für Netzwerksicherheitsgruppen und andere Ressourcen für den Datenverkehrsfluss zu und aus AKS-Clustern (Azure Kubernetes Service). Verwenden Sie für einzelne Netzwerksicherheitsgruppen-Regeln das Feld „Beschreibung“, um geschäftliche Anforderungen und/oder die Dauer usw. für Regeln festzulegen, die Datenverkehr in ein oder aus einem Netzwerk zulassen.
Verwenden Sie beliebige der integrierten tagbezogenen Azure Policy-Definitionen (beispielsweise „Tag und zugehörigen Wert erzwingen“), um sicherzustellen, dass alle Ressourcen mit Tags erstellt werden und Sie Benachrichtigungen für vorhandene Ressourcen ohne Tags empfangen.

Mit Netzwerkrichtlinien können Sie den Zugriff auf bestimmte Netzwerkpfade im Cluster basierend auf Namespaces und Bezeichnungsselektoren zulassen oder verweigern. Verwenden Sie diese Namespaces und Bezeichnungen als Deskriptoren für Datenverkehrs-Konfigurationsregeln. Verwenden Sie Azure PowerShell oder die Azure CLI, um Ressourcen anhand ihrer Tags zu suchen oder Aktionen auszuführen.

- [Azure Policy mit CLI](/cli/azure/policy)

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

- [Erstellen einer NSG mit einer Sicherheitskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen der Konfigurationen von Netzwerkressourcen und zum Erkennen von Änderungen bei Netzwerkressourcen, die sich auf AKS (Azure Kubernetes Service) beziehen. 

Erstellen Sie Warnungen in Azure Monitor, die bei Änderungen an wichtigen Netzwerkressourcen ausgelöst werden. Alle Einträge des Benutzers AzureContainerService in den Aktivitätsprotokollen werden als Plattformaktionen protokolliert. 

Verwenden Sie Azure Monitor-Protokolle, um die Protokolle von AKS zu aktivieren und die Masterkomponenten abzufragen: kube-apiserver und kube-controller-manager. Erstellen und verwalten Sie die Knoten, die kubelet mit der Containerruntime ausführen, und stellen Sie deren Anwendungen über den verwalteten Kubernetes-API-Server bereit. 

- [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Erstellen von Warnungen in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

- [Aktivieren und Überprüfen der Kubernetes-Masterknotenprotokolle in Azure Kubernetes Service (AKS)](view-master-logs.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden**: AKS-Knoten (Azure Kubernetes Service) verwenden ntp.ubuntu.com für die Zeitsynchronisierung sowie UDP-Port 123 und NTP (Network Time Protocol). 

Bei Verwendung benutzerdefinierter DNS-Server muss sichergestellt werden, dass die Clusterknoten auf diese Server zugreifen können. 

- [Informationen zu NTP-Domänen und Portanforderungen für AKS-Clusterknoten](limit-egress-traffic.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Aktivieren Sie Überwachungsprotokolle über AKS-Masterkomponenten (Azure Kubernetes Service) wie kube-apiserver und kube-controller-manager, die als verwalteter Dienst bereitgestellt werden. 

• kube-auditaksService: Der Anzeigename im Überwachungsprotokoll für den Vorgang auf der Steuerungsebene (aus „hcpService“). 

• masterclient: Der Anzeigename im Überwachungsprotokoll für „MasterClientCertificate“ (das Zertifikat, das von „az aks get-credentials“ zurückgegeben wird). 

• nodeclient: Der Anzeigename für „ClientCertificate“ (wird von Agent-Knoten verwendet).

Aktiviert auch andere Überwachungsprotokolle, z. B. kube-audit. 

Exportieren Sie diese Protokolle in Log Analytics oder eine andere Speicherplattform. Verwenden Sie in Azure Monitor Log Analytics-Arbeitsbereiche, um Analysen abzufragen und auszuführen, und verwenden Sie Azure Storage-Konten für langfristige Speicherung und Archivierung.

Sie sollten die Daten für Azure Sentinel oder eine SIEM-Lösung eines Drittanbieters auf Grundlage der Geschäftsanforderungen Ihrer Organisation aktivieren und ein Onboarding durchführen.

- [Überprüfen Sie das Protokollschema (einschließlich der Protokollrollen) hier](view-master-logs.md)

- [Informationen zu Azure Monitor für Container](../azure-monitor/insights/container-insights-overview.md)

- [Aktivieren von Azure Monitor für Container](../azure-monitor/insights/container-insights-onboard.md)

- [Aktivieren und Überprüfen der Kubernetes-Masterknotenprotokolle in Azure Kubernetes Service (AKS)](view-master-logs.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Verwenden Sie Aktivitätsprotokolle zum Überwachen von Aktionen für Azure Kubernetes Service (AKS), um alle Aktivitäten und deren Status anzuzeigen. Ermitteln Sie mit Aktivitätsprotokollen, welche Vorgänge für die Ressourcen in Ihrem Abonnement ausgeführt wurden: Wer den Vorgang gestartet hat

Wann der Vorgang abgeschlossen wurde

Den Status des Vorgangs

Die Werte anderer Eigenschaften, mit denen Sie den Vorgang eventuell untersuchen können

Rufen Sie Informationen aus den Aktivitätsprotokollen über Azure PowerShell, die Azure-Befehlszeilenschnittstelle, die Azure REST-API oder über das Azure-Portal ab. 

Aktivieren Sie Überwachungsprotokolle für AKS-Masterkomponenten, z. B.: 

• kube-auditaksService: Der Anzeigename im Überwachungsprotokoll für den Vorgang auf der Steuerungsebene (aus „hcpService“). 

• masterclient: Der Anzeigename im Überwachungsprotokoll für „MasterClientCertificate“ (das Zertifikat, das von „az aks get-credentials“ zurückgegeben wird). 

• nodeclient: Der Anzeigename für „ClientCertificate“ (wird von Agent-Knoten verwendet).

Aktiviert auch andere Überwachungsprotokolle, z. B. kube-audit. 

- [Aktivieren und Überprüfen der Kubernetes-Masterknotenprotokolle in AKS](view-master-logs.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Leitfaden**: Aktivieren Sie die automatische Installation von Log Analytics-Agents zum Erfassen von Daten von den AKS-Clusterknoten. Aktivieren Sie außerdem die automatische Bereitstellung des Azure Log Analytics Monitoring-Agents aus Azure Security Center, da automatische Bereitstellung standardmäßig deaktiviert ist. Der Agent kann auch manuell installiert werden. Bei aktivierter automatischer Bereitstellung wird der Log Analytics-Agent von Security Center auf allen unterstützten und neu erstellten virtuellen Azure-Computern bereitgestellt. Security Center erfasst Daten von Ihren virtuellen Azure-Computern (VMs), VM-Skalierungsgruppen und IaaS-Containern, z. B. Kubernetes-Clusterknoten, um sie auf Sicherheitslücken und Bedrohungen zu überwachen. Die Daten werden mit dem Azure Log Analytics-Agent erfasst. Der Agent liest verschiedene sicherheitsrelevante Konfigurationen und Ereignisprotokolle auf dem Computer und kopiert die Daten zur Analyse in den Arbeitsbereich. 

Die Datensammlung ist erforderlich, um einen Einblick in fehlende Updates, falsch konfigurierte Sicherheitseinstellungen des Betriebssystems, den Status des Endpunktschutzes sowie die Integritäts- und Bedrohungserkennung bereitzustellen.

- [Aktivieren der automatischen Bereitstellung des Log Analytics-Agents](../security-center/security-center-enable-data-collection.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Onboarding von AKS-Instanzen (Azure Kubernetes Service) in Azure Monitor und Festlegen des entsprechenden Aufbewahrungszeitraums des Azure Log Analytics-Arbeitsbereichs gemäß den Complianceanforderungen Ihrer Organisation. 

- [Ändern des Datenaufbewahrungszeitraums](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Führen Sie ein Onboarding von AKS-Instanzen (Azure Kubernetes Service) in Azure Monitor durch, und konfigurieren Sie Diagnoseeinstellungen für Ihren Cluster. 

Verwenden Sie den Log Analytics-Arbeitsbereich von Azure Monitor, um Protokolle zu überprüfen und Abfragen nach Protokolldaten auszuführen. Azure Monitor-Protokolle werden im Azure-Portal oder über die Befehlszeilenschnittstelle aktiviert und verwaltet und funktionieren sowohl mit der rollenbasierten Zugriffssteuerung für Kubernetes (Kubernetes Role-Based Access Control, Kubernetes RBAC) als auch mit AKS-Clustern ohne RBAC-Unterstützung.

Zeigen Sie die von AKS-Masterkomponenten (kube-apiserver und kube-controllermanager) generierten Protokolle für die Problembehandlung von Anwendungen und Diensten an. Aktivieren Sie Daten für Azure Sentinel oder eine SIEM-Drittanbieterlösung, und führen Sie ein Onboarding durch, um zentrale Protokollverwaltung und Überwachung zu ermöglichen.

- [Aktivieren und Überprüfen der Kubernetes-Masterknotenprotokolle in AKS](view-master-logs.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Ausführen benutzerdefinierter Abfragen in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivieren von Warnungen bei anomalen Aktivitäten

**Leitfaden**: Verwenden Sie Azure Kubernetes Service (AKS) in Verbindung mit Security Center, um tiefere Einblicke in AKS-Knoten zu erhalten. Überprüfen Sie Security Center-Warnungen zu Bedrohungen und Schadsoftwareaktivitäten, die auf dem Host und auf Clusterebene erkannt wurden. Security Center implementiert kontinuierliche Analyse von Sicherheitsrohereignissen, die in einem AKS-Cluster auftreten, z. B. Netzwerkdaten, Prozesserstellung und das Kubernetes-Überwachungsprotokoll. Ermitteln Sie, ob es sich bei dieser Aktivität um ein erwartetes Verhalten handelt oder die Anwendung ein Fehlverhalten aufweist. Verwenden Sie Metriken und Protokolle in Azure Monitor, um Ihre Ergebnisse zu untermauern. 

- [Informationen zur Integration von Security Center in Azure Kubernetes Service](../security-center/defender-for-kubernetes-introduction.md)

- [Aktivieren des Standard-Tarifs von Azure Security Center](../security-center/security-center-get-started.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Installieren und aktivieren Sie Microsoft Antimalware für Azure für virtuelle AKS-Computer (Azure Kubernetes Service) und VM-Skalierungsgruppenknoten. Überprüfen Sie Warnungen in Security Center, um Probleme zu beheben.

- [Microsoft Antimalware für Azure Cloud Services und Virtual Machines](../security/fundamentals/antimalware.md)

- [Sicherheitswarnungen: Referenzhandbuch](../security-center/alerts-reference.md)

- [Warnungen für Container: Azure Kubernetes Service-Cluster](../security-center/alerts-reference.md#alerts-akscluster)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden**: Azure Kubernetes Service (AKS) verwendet das CoreDNS-Projekt für die Cluster-DNS-Verwaltung und -Auflösung.

Aktivieren Sie DNS-Abfrageprotokollierung, indem Sie die dokumentierte Konfiguration in Ihrer coredns-custom-ConfigMap anwenden. 

- [Anpassen von CoreDNS mit Azure Kubernetes Service](coredns-custom.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Leitfaden**: Verwenden Sie kubectl, einen Befehlszeilenclient in Azure Kubernetes Service (AKS), um einen Kubernetes-Cluster zu verwalten und seine Protokolle für Problembehandlung vom AKS-Knoten abzurufen. Bei Verwendung von Azure Cloud Shell ist kubectl bereits installiert. Verwenden Sie das Cmdlet Install-AzAksKubectl, um kubectl lokal zu installieren.

- [Schnellstart: Bereitstellen eines Azure Kubernetes Service-Clusters mit PowerShell](kubernetes-walkthrough-powershell.md)

- [Abrufen von Kubelet-Protokollen aus Azure Kubernetes Service-Clusterknoten (AKS)](kubelet-logs.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Azure Kubernetes Service (AKS) selbst bietet keine Identitätsverwaltungslösung, in der reguläre Benutzerkonten und Kennwörter gespeichert werden. Durch Integration in Azure Active Directory (Azure AD) können Sie Benutzern oder Gruppen Zugriff auf Kubernetes-Ressourcen in einem Namespace oder im ganzen Cluster gewähren. 

Führen Sie mit dem Azure AD PowerShell-Modul Ad-hoc-Abfragen zum Ermitteln von Konten aus, die Mitglieder von administrativen AKS-Gruppen sind.

Verwenden Sie die Azure CLI für Vorgänge wie „Abrufen von Zugriffsanmeldeinformationen für einen verwalteten Kubernetes-Cluster“, um den regelmäßigen Abgleich des Zugriffs zu erleichtern. Implementieren Sie diesen Prozess, um ein aktualisiertes Inventar der Dienstkonten zu erhalten, die ein anderer primärer Benutzertyp in AKS sind. Erzwingen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung von Security Center.

- [Integration von AKS in Azure AD](./azure-ad-integration-cli.md)

- [Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Überwachen der Identität und des Zugriffs](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Azure Kubernetes Service (AKS) verfolgt nicht das Konzept allgemeiner Standardkennwörter und bietet keine Identitätsverwaltungslösung, bei der reguläre Benutzerkonten und Kennwörter gespeichert werden können. Durch Integration in Azure Active Directory (Azure AD) können Sie rollenbasierten Zugriff auf AKS-Ressourcen in einem Namespace oder im ganzen Cluster gewähren. 

Führen Sie mit dem Azure AD PowerShell-Modul Ad-hoc-Abfragen zum Ermitteln von Konten aus, die Mitglieder von administrativen AKS-Gruppen sind.

- [Verstehen der Zugriffs- und Identitätsoptionen für AKS](concepts-identity.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Integrieren von Benutzerauthentifizierung für Ihre AKS-Cluster (Azure Kubernetes Service) mit Azure Active Directory (Azure AD). Melden Sie sich bei einem AKS-Cluster mit einem Azure AD-Authentifizierungstoken an. Konfigurieren Sie rollenbasierte Zugriffssteuerung für Kubernetes (Kubernetes Role-Based Access Control, Kubernetes RBAC) für den administrativen Zugriff auf Informationen und Berechtigungen, Namespaces und Clusterressourcen im Zusammenhang mit der Kubernetes-Konfiguration (kubeconfig). 

Erstellen Sie Richtlinien und Vorgänge für die Verwendung dedizierter Administratorkonten. Implementieren Sie Empfehlungen für die Identitäts- und Zugriffsverwaltung in Security Center.

- [Überwachen der Identität und des Zugriffs](../security-center/security-center-identity-access.md)

- [Steuern des Zugriffs auf Unternehmensressourcen](azure-ad-rbac.md)

- [Verwenden rollenbasierter Zugriffssteuerung (Azure)](control-kubeconfig-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

**Leitfaden**: Verwenden Sie einmaliges Anmelden (SSO) mit integrierter Azure Active Directory-Authentifizierung (Azure AD) für einen AKS-Cluster.

- [Anzeigen von Kubernetes-Protokollen, -Ereignissen und -Podmetriken in Echtzeit](../azure-monitor/insights/container-insights-livedata-overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Integrieren Sie Authentifizierung für Azure Kubernetes Service (AKS) mit Azure Active Directory (Azure AD). 

Aktivieren Sie mehrstufige Azure AD-Authentifizierung (MFA), und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung von Security Center.

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md) 

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Verwenden Sie eine Arbeitsstation mit privilegiertem Zugriff (Privileged Access Workstation, PAW) mit Multi-Factor Authentication (MFA), die so konfiguriert ist, dass sie sich bei ihren angegebenen Azure Kubernetes Service-Clustern (AKS) und zugehörigen Ressourcen anmeldet.
- [Informationen zu Arbeitsstationen mit privilegiertem Zugriff](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Protokollieren von und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Azure Active Directory-Sicherheitsberichte (Azure AD) mit in Azure AD integrierter Authentifizierung für Azure Kubernetes Service (AKS). Warnungen können generiert werden, wenn verdächtige oder unsichere Aktivitäten in der Umgebung auftreten. Verwenden Sie Security Center zum Überwachen von identitäts- und zugriffsbezogenen Aktivitäten.

- [Identifizieren von Azure AD-Benutzern, die aufgrund riskanter Aktivitäten gekennzeichnet wurden](../active-directory/identity-protection/overview-identity-protection.md)

- [Überwachen der identitäts- und zugriffsbezogenen Aktivitäten von Benutzern in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Verwenden Sie benannte Standorte mit bedingtem Zugriff, um den Zugriff auf Azure Kubernetes Service-Cluster (AKS) nur über bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen. Dafür ist integrierte Authentifizierung für AKS mit Azure Active Directory (Azure AD) erforderlich.

Beschränken Sie den Zugriff auf den AKS-API-Server auf eine begrenzte Sammlung von IP-Adressbereichen, wenn er Anforderungen zum Ausführen von Aktionen im Cluster zum Erstellen von Ressourcen oder zum Skalieren der Anzahl der Knoten empfängt. 

- [Sicherer Zugriff auf den API-Server mit autorisierten IP-Adressbereichen in Azure Kubernetes Service (AKS)](api-server-authorized-ip-ranges.md)

- [Konfigurieren benannter Standorte in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure Active Directory (Azure AD) als zentrales Authentifizierungs- und Autorisierungssystem für Azure Kubernetes Service. Azure AD schützt Daten mithilfe von starker Verschlüsselung für ruhende Daten und Daten in der Übertragung. Es werden Salts, Hashes und sichere Speicher für Benutzeranmeldeinformationen verwendet.

Verwenden Sie die integrierten AKS-Rollen (Ressourcenrichtlinienmitwirkender und Besitzer) mit rollenbasierter Zugriffssteuerung in Azure (Azure RBAC) für Richtlinienzuweisungsvorgänge an Ihren Kubernetes-Cluster.

- [Azure Policy – Übersicht](../governance/policy/overview.md)

- [Integrieren von Azure AD in AKS](./azure-ad-integration-cli.md) 

- [Integrieren einer von AKS verwalteten Azure AD-Instanz](managed-aad.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Verwenden Sie Azure Active Directory-Sicherheitsberichte (Azure AD) mit in Azure AD integrierter Authentifizierung für Azure Kubernetes Service (AKS). Durchsuchen Sie Azure AD-Protokolle, um veraltete Konten zu ermitteln. 

Führen Sie Zugriffsüberprüfungen für Azure-Identitäten durch, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen sowie Rollenzuweisungen effizient zu verwalten. Stellen Sie die Empfehlungen für Identität und Zugriff von Security Center wieder her.

Beachten Sie die Rollen, die zu Support- oder Problembehandlungszwecken verwendet werden. Beispielsweise werden alle vom Microsoft-Support durchgeführten Clusteraktionen (mit Zustimmung des Benutzers) unter einer integrierten Kubernetes-Rolle „Bearbeiten“ mit dem Namen aks-support-rolebinding vorgenommen. AKS-Unterstützung ist für diese Rolle aktiviert, um die Clusterkonfiguration und Ressourcen zur Problembehandlung und Diagnose von Clusterproblemen zu bearbeiten. Diese Rolle kann jedoch keine Berechtigungen ändern und keine Rollen oder Rollenbindungen erstellen. Dieser Rollenzugriff ist nur unter aktiven Supporttickets mit Just-In-Time-Zugriff (JIT) aktiviert.
 
- [Zugriffs- und Identitätsoptionen für Azure Kubernetes Service (AKS)](concepts-identity.md)

- [Verwenden von Zugriffsüberprüfungen für Azure-Identitäten](../active-directory/governance/access-reviews-overview.md)

- [Überwachen der Identitäts- und Zugriffsaktivitäten von Benutzern in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Anmeldeinformationen

**Leitfaden**: Integrieren Sie Benutzerauthentifizierung für Azure Kubernetes Service (AKS) mit Azure Active Directory (Azure AD). Erstellen Sie Diagnoseeinstellungen für Azure AD, und senden Sie die Überwachungs- und Anmeldeprotokolle an einen Azure Log Analytics-Arbeitsbereich. Konfigurieren Sie gewünschte Warnungen (z. B. wenn ein deaktiviertes Konto versucht, sich anzumelden) innerhalb eines Azure Log Analytics-Arbeitsbereichs.
- [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor](../azure-monitor/platform/alerts-log.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Integrieren Sie Benutzerauthentifizierung für Azure Kubernetes Service (AKS) mit Azure Active Directory (Azure AD). Mit den Funktionen zur Risikoerkennung und zum Identitätsschutz von Azure AD können Sie automatische Antworten auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten konfigurieren. Erfassen Sie Daten zur weiteren Untersuchung basierend auf Geschäftsanforderungen in Azure Sentinel.

- [Anzeigen riskanter Azure AD-Anmeldungen](../active-directory/identity-protection/overview-identity-protection.md)

- [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden**: Gilt nicht für Azure Kubernetes Service (AKS), da nicht von Kunden-Lockbox unterstützt.
- [Unterstützte Dienste und Szenarios bei allgemeiner Verfügbarkeit](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="data-protection"></a>Schutz von Daten

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenschutz](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags für Ressourcen im Zusammenhang mit Ihrer AKS-Bereitstellung (Azure Kubernetes Service) als Hilfe beim Nachverfolgen von Azure-Ressourcen, die vertrauliche Informationen speichern oder verarbeiten.

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

- [Aktualisieren von Tags für verwaltete Cluster](/rest/api/aks/managedclusters/updatetags)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Isolieren Sie Teams und Workloads im gleichen Cluster logisch mit Azure Kubernetes Service (AKS), um die geringste Anzahl von Berechtigungen gemäß den für die einzelnen Teams erforderlichen Ressourcen bereitzustellen. 

Verwenden Sie den Namespace in Kubernetes, um eine logische Isolationsgrenze zu erstellen. Ziehen Sie die Implementierung zusätzlicher Kubernetes-Features für Isolation und Mehrinstanzenfähigkeit in Erwägung, z. B. Zeitplanung, Netzwerke, Authentifizierung/Autorisierung und Container.

Implementieren Sie separate Abonnements oder Verwaltungsgruppen für Entwicklungs-, Test- und Produktionsumgebungen. Trennen Sie AKS-Cluster durch Netzwerke, indem Sie sie in unterschiedlichen virtuellen Netzwerken bereitstellen, die entsprechend gekennzeichnet sind.

- [Informationen zu bewährten Methoden für die Clusterisolierung in AKS](operator-best-practices-cluster-isolation.md)

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

- [Informationen zu bewährten Methoden für Netzwerkkonnektivität und Sicherheit in AKS](operator-best-practices-network.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden**: Verwenden Sie eine Drittanbieterlösung von Azure Marketplace an Netzwerkperimetern, die die nicht autorisierte Übertragung von vertraulichen Informationen überwacht, derartige Übertragungen blockiert und gleichzeitig die Informationssicherheitsexperten benachrichtigt.

Microsoft verwaltet die zugrunde liegende Plattform, behandelt alle Kundeninhalte vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Offenlegung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

- [Liste der erforderlichen Ports, Adressen und Domänennamen für AKS-Funktionalität](limit-egress-traffic.md)

- [Konfigurieren von Diagnoseeinstellungen für Azure Firewall](../firewall/firewall-diagnostics.md)

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Erstellen Sie einen HTTPS-Eingangscontroller, und verwenden Sie Ihre eigenen TLS-Zertifikate (oder optional Let‘s Encrypt) für Ihre AKS-Bereitstellungen (Azure Kubernetes Service). 

Ausgehender Kubernetes-Datenverkehr wird standardmäßig über HTTPS/TLS verschlüsselt. Überprüfen Sie potenziell nicht verschlüsselten ausgehenden Datenverkehr aus Ihren AKS-Instanzen, um die Überwachung zu verstärken. Dies kann in einigen Fällen NTP-Datenverkehr, DNS-Datenverkehr, HTTP-Datenverkehr zum Abrufen von Updates einschließen. 

- [Erstellen eines HTTPS-Eingangscontrollers für AKS und Verwenden Ihrer eigenen TLS-Zertifikate](ingress-own-tls.md)

- [Erstellen eines HTTPS-Eingangscontrollers in AKS mit Let‘s Encrypt](ingress-tls.md)

- [Liste der möglichen ausgehenden Ports und Protokolle, die von AKS verwendet werden](limit-egress-traffic.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Funktionen für Datenidentifizierung, -klassifizierung und -verlust sind für Azure Storage- oder Computeressourcen noch nicht verfügbar. Implementieren Sie eine Drittanbieterlösung, wenn dies für Compliancezwecke erforderlich ist.
Microsoft verwaltet die zugrunde liegende Plattform, behandelt alle Kundeninhalte vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Offenlegung zu schützen. 

Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Verwalten des Zugriffs auf Ressourcen

**Leitfaden**: Verwenden Sie rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC), ein Autorisierungssystem, das auf Azure Resource Manager basiert und eine präzise Verwaltung des Zugriffs auf Azure-Ressourcen ermöglicht.

Konfigurieren Sie Azure Kubernetes Service (AKS) für die Verwendung von Azure Active Directory (Azure AD) für Benutzerauthentifizierung. Mit dieser Konfiguration melden Sie sich bei einem AKS-Cluster über ein Azure AD-Authentifizierungstoken an. 

Verwenden Sie die integrierten AKS-Rollen (Ressourcenrichtlinienmitwirkender und Besitzer) mit Azure RBAC für Richtlinienzuweisungsvorgänge an Ihren AKS-Cluster.

- [Steuern des Zugriffs auf Clusterressourcen mithilfe von Azure RBAC und Azure AD-Identitäten in AKS](azure-ad-rbac.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Leitfaden**: Funktionen für Datenidentifizierung, -klassifizierung und -verlust sind für Azure Storage- oder Computeressourcen noch nicht verfügbar. Implementieren Sie eine Drittanbieterlösung, wenn dies für Compliancezwecke erforderlich ist.
Microsoft verwaltet die zugrunde liegende Plattform, behandelt alle Kundeninhalte vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Offenlegung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Die beiden primären Speichertypen, die für Volumes in Azure Kubernetes Service (AKS) zur Verfügung stehen, werden durch Azure-Datenträger oder Azure Files gesichert. Um die Sicherheit zu verbessern, verwenden beide Speichertypen Azure-Speicherdienstverschlüsselung (Storage Service Encryption, SSE) zur Verschlüsselung von ruhenden Daten. Standardmäßig werden Daten mit von Microsoft verwalteten Schlüsseln verschlüsselt.

Für zusätzliche Kontrolle über die Verschlüsselungsschlüssel können Sie kundenseitig verwaltete Schlüssel bereitstellen, die für die ruhende Verschlüsselung der Datenträger für Betriebssystem und Daten für Ihre AKS-Cluster verwendet werden sollen. Kunden tragen die Verantwortung für wichtige Verwaltungsaktivitäten, z. B. die Schlüsselsicherung und -rotation. Festplatten können derzeit nicht mit der Azure Disk Encryption auf AKS-Knotenebene verschlüsselt werden.

- [Best Practices für Speicherung und Sicherungen in Azure Kubernetes Service (AKS)](operator-best-practices-storage.md)

- [Bring Your Own Key (BYOK) mit Azure-Datenträgern in Azure Kubernetes Service (AKS)](azure-disk-customer-managed-keys.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor für Container zum Überwachen der Leistung von Containerworkloads, die in verwalteten Kubernetes-Clustern bereitgestellt und in Azure Kubernetes Service (AKS) gehostet werden. 

Konfigurieren Sie Warnungen so, dass proaktiv Benachrichtigungen oder Protokolle erstellt werden, wenn die CPU- und Arbeitsspeicherauslastung auf Knoten oder in Containern die definierten Schwellenwerte überschreitet oder wenn eine Änderung des Integritätszustands im Cluster beim Integritätsrollup für Infrastruktur oder Knoten erfolgt. 

Verwenden Sie das Azure-Aktivitätsprotokoll, um Ihre AKS-Cluster und zugehörige Ressourcen auf hoher Ebene zu überwachen. Über die Integration mit Prometheus können Sie Anwendungs- und Workloadmetriken anzeigen, die von Knoten und Kubernetes mithilfe von Abfragen gesammelt werden, um benutzerdefinierte Warnungen und Dashboards zu erstellen und ausführliche Analysen durchzuführen.

- [Informationen zu Azure Monitor für Container](../azure-monitor/insights/container-insights-overview.md)

- [Aktivieren von Azure Monitor für Container](../azure-monitor/insights/container-insights-onboard.md)

- [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Verwenden Sie Security Center, um Ihre Azure Container Registry einschließlich AKS-Instanzen (Azure Kubernetes Service) auf Sicherheitsrisiken zu überwachen. Durch Aktivierung des Pakets „Containerregistrierungen“ in Security Center wird sichergestellt, dass Security Center bereit ist, Images zu überprüfen, die in die Registrierung gepusht werden.

Lassen Sie sich im Security Center-Dashboard benachrichtigen, wenn Probleme auftreten, nachdem Security Center das Image mithilfe von Qualys überprüft hat. Das Paket „Containerregistrierungen“ bietet tiefere Einblicke in die Sicherheitsrisiken der Images in Ihren Azure Resource Manager-basierten Registrierungen. 

Verwenden Sie Security Center für handlungsrelevante Empfehlungen für jedes Sicherheitsrisiko. Zu diesen Empfehlungen gehören eine Klassifizierung des Schweregrads und eine Anleitung zur Behebung des Problems. 

- [Best Practices für Containerimageverwaltung und Sicherheit in Azure Kubernetes Service (AKS)](../security-center/defender-for-container-registries-introduction.md)

- [Informationen zu bewährten Methoden für die Verwaltung von Containerimages und Sicherheit in AKS](operator-best-practices-container-image-management.md)

- [Informationen zur Integration von Azure Security Center in Container Registry](../security-center/defender-for-container-registries-introduction.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Betriebssystemen

**Leitfaden**: Sicherheitsupdates werden automatisch auf Linux-Knoten angewendet, um die AKS-Cluster (Azure Kubernetes Service) des Kunden zu schützen. Diese Updates enthalten Sicherheitsfixes für das Betriebssystem oder Kernelupdates. 

Beachten Sie, dass sich der Prozess, mit dem Windows Server-Knoten auf dem neuesten Stand gehalten werden, von den unter Linux ausgeführten Knoten unterscheidet, da Windows Server-Knoten keine täglichen Updates erhalten. Stattdessen müssen Kunden ein Upgrade der Windows Server-Knotenpools in ihren AKS-Clustern durchführen, bei dem neue Knoten mit dem neuesten Window Server-Basisimage und Patches über die Azure-Systemsteuerung oder die Azure CLI bereitgestellt werden. Diese Updates enthalten Verbesserungen der Sicherheit oder Funktionalität von AKS.

- [Grundlegendes zur Anwendung von Updates auf AKS-Clusterknoten unter Linux](node-updates-kured.md)

- [Upgrade eines AKS-Knotenpools für AKS-Cluster, die Windows Server-Knoten verwenden](use-multiple-node-pools.md#upgrade-a-node-pool)

- [Upgrade für AKS-Knotenimages (AKS)](node-image-upgrade.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Bereitstellen einer automatisierten Patchverwaltungslösung für Softwaretitel von Drittanbietern

**Leitfaden**: Implementieren Sie einen manuellen Prozess, um sicherzustellen, dass die Drittanbieteranwendungen der Clusterknoten von Azure Kubernetes Service (AKS) für die gesamte Clusterlebensdauer gepatcht bleiben. Dies erfordert möglicherweise die Aktivierung von automatischen Updates, das Überwachen der Knoten oder das Ausführen regelmäßiger Neustarts.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Vergleichen von kaskadierenden Sicherheitsrisikoscans

**Leitfaden**: Exportieren Sie die Security Center-Scanergebnisse in regelmäßigen Abständen, und vergleichen Sie die Ergebnisse, um zu überprüfen, ob die Sicherheitsrisiken behoben wurden. 

Verwenden Sie das PowerShell-Cmdlet Get-AzSecurityTask, um den Abruf von Sicherheitstasks zu automatisieren, deren Durchführung Security Center empfiehlt, um den Sicherheitsstatus zu erhöhen und die Ergebnisse der Sicherheitsrisikoüberprüfung zu beheben.

- [Verwenden von PowerShell zum Anzeigen von Sicherheitsrisiken, die von Azure Security Center erkannt werden](/powershell/module/az.security/get-azsecuritytask)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Verwenden Sie die von Security Center bereitgestellte Schweregradzuordnung, um die Behebung von Sicherheitsrisiken zu priorisieren. 

Verwenden Sie CVSS (Common Vulnerability Scoring System) (oder ein anderes Bewertungssystem, wie von Ihrem Scantool bereitgestellt), wenn Sie ein integriertes Tool zur Bewertung von Sicherheitsrisiken verwenden (z. B. Qualys oder Rapid7, angeboten von Azure).

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Verwenden der automatisierten Asset Discovery-Lösung

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen (z. B. Computeressourcen, Speicher, Netzwerke usw.) in Ihren Abonnements abzufragen bzw. zu ermitteln. Stellen Sie entsprechende Berechtigungen (Leseberechtigungen) in Ihrem Mandanten sicher, und vergewissern Sie sich, dass Sie alle Azure-Abonnements sowie Ressourcen in Ihren Abonnements aufzählen können.

Obwohl klassische Azure-Ressourcen über Resource Graph ermittelt werden können, wird dringend empfohlen, Azure Resource Manager-Ressourcen zu erstellen und zu verwenden.

- [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Anzeigen Ihrer Azure-Abonnements](/powershell/module/az.accounts/get-azsubscription)

- [Grundlegendes zu Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Wenden Sie Tags auf Azure-Ressourcen mit Metadaten an, um sie logisch in einer Taxonomie zu organisieren.

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Verwenden Sie nach Bedarf Tagging, Verwaltungsgruppen und separate Abonnements zum Organisieren und Nachverfolgen von Ressourcen. 

Wenden Sie Taints, Bezeichnungen oder Tags an, wenn Sie einen AKS-Knotenpool (Azure Kubernetes Service) erstellen. Alle Knoten innerhalb dieses Knotenpools erben auch Taints, Bezeichnungen oder Tags.

Taints, Bezeichnungen oder Tags können verwendet werden, um den Bestand regelmäßig abzustimmen und sicherzustellen, dass nicht autorisierte Ressourcen rechtzeitig aus Ihrem Abonnement gelöscht werden.

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

- [Verwaltete Cluster: Aktualisieren von Tags](/rest/api/aks/managedclusters/updatetags)

- [Angeben von Taint, Bezeichnung oder Tag für einen Knotenpool](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definieren und Verwalten eines Bestands an genehmigten Azure-Ressourcen

**Leitfaden**: Definieren Sie eine Liste genehmigter Azure-Ressourcen und genehmigter Software für Computeressourcen basierend auf den Anforderungen Ihrer Organisation.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:
-   Not allowed resource types (Unzulässige Ressourcentypen) 

-   Zulässige Ressourcentypen

Verwenden Sie Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen und zu ermitteln. Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure-Ressourcen basierend auf den Geschäftsanforderungen Ihrer Organisation genehmigt sind.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Überwachen auf nicht genehmigte Softwareanwendungen innerhalb von Computeressourcen

**Leitfaden**: Verwenden Sie das Feature „Azure Automation-Änderungsnachverfolgung und Bestand“, um herauszufinden, welche Software in Ihrer Umgebung installiert ist. 

Sie können die Bestandsinformationen für Software, Dateien, Linux-Daemons, Windows-Dienste und Windows-Registrierungsschlüssel auf Ihren Computern erfassen und anzeigen und überwachen, ob nicht genehmigte Softwareanwendungen vorhanden sind. 

Durch das Nachverfolgen der Konfigurationen Ihrer Computer können Sie betriebsbezogene Probleme in Ihrer Umgebung erkennen und ein besseres Verständnis des Zustands Ihrer Computer entwickeln.

- [Aktivieren des Bestands virtueller Azure-Computer](../automation/automation-tutorial-installed-software.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden**: Verwenden Sie das Feature „Azure Automation-Änderungsnachverfolgung und Bestand“, um herauszufinden, welche Software in Ihrer Umgebung installiert ist. 

Sie können die Bestandsinformationen für Software, Dateien, Linux-Daemons, Windows-Dienste und Windows-Registrierungsschlüssel auf Ihren Computern erfassen und anzeigen und überwachen, ob nicht genehmigte Softwareanwendungen vorhanden sind. 

Durch das Nachverfolgen der Konfigurationen Ihrer Computer können Sie betriebsbezogene Probleme in Ihrer Umgebung erkennen und ein besseres Verständnis des Zustands Ihrer Computer entwickeln.

- [Aktivieren des Bestands virtueller Azure-Computer](../automation/automation-tutorial-installed-software.md)

- [Verwenden der Dateiintegritätsüberwachung](../security-center/security-center-file-integrity-monitoring.md)

- [Grundlegendes zur Azure-Änderungsnachverfolgung](../automation/change-tracking/overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="68-use-only-approved-applications"></a>6.8: Ausschließliche Verwendung genehmigter Anwendungen

**Leitfaden**: Verwenden Sie das Feature „Azure Automation-Änderungsnachverfolgung und Bestand“, um herauszufinden, welche Software in Ihrer Umgebung installiert ist. 

Sie können die Bestandsinformationen für Software, Dateien, Linux-Daemons, Windows-Dienste und Windows-Registrierungsschlüssel auf Ihren Computern erfassen und anzeigen und überwachen, ob nicht genehmigte Softwareanwendungen vorhanden sind. 

Durch das Nachverfolgen der Konfigurationen Ihrer Computer können Sie betriebsbezogene Probleme in Ihrer Umgebung erkennen und ein besseres Verständnis des Zustands Ihrer Computer entwickeln.

Aktivieren Sie adaptive Anwendungsanalyse in Security Center für Anwendungen, die in Ihrer Umgebung vorhanden sind.

- [Aktivieren des Bestands virtueller Azure-Computer](../automation/automation-tutorial-installed-software.md)

 
Verwenden der adaptiven Anwendung in Azure Security Center
- [Steuerelemente](../security-center/security-center-adaptive-application.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)

- Zulässige Ressourcentypen

Verwenden Sie Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen und zu ermitteln. Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure-Ressourcen genehmigt sind.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Verwalten eines Bestands an genehmigten Softwaretiteln

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Ihren Abonnements erstellt werden können. Nutzen Sie hierzu die integrierten Richtliniendefinitionen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden**: Verwenden Sie den bedingten Azure-Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.
- [Verwalten des Zugriffs auf die Azure-Verwaltung mit bedingtem Zugriff](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: Einschränken der Skriptausführung durch Benutzer in Computeressourcen

**Leitfaden**: Azure Kubernetes Service (AKS) selbst bietet keine Identitätsverwaltungslösung, in der reguläre Benutzerkonten und Kennwörter gespeichert werden. Verwenden Sie stattdessen Azure Active Directory (Azure AD) als integrierte Identitätslösung für Ihre AKS-Cluster. 

Gewähren Sie Benutzern oder Gruppen mit Azure AD-Integration Zugriff auf Kubernetes-Ressourcen in einem Namespace oder im gesamten Cluster. 

Verwenden Sie das Azure AD PowerShell-Modul, um Ad-hoc-Abfragen zum Ermitteln von Konten auszuführen, die Mitglieder administrativer AKS-Gruppen sind. Stimmen Sie damit den Zugriff in regelmäßigen Abständen ab. Verwenden Sie die Azure CLI für Vorgänge wie „Abrufen von Zugriffsanmeldeinformationen für einen verwalteten Kubernetes-Cluster“. Implementieren Sie Empfehlungen für die Identitäts- und Zugriffsverwaltung in Security Center.

- [Verwalten von AKS mit der Azure CLI](/cli/azure/aks)

- [Verstehen der AKS- und Azure AD-Integration](concepts-identity.md)

- [Integration von AKS in Azure AD](./azure-ad-integration-cli.md)

- [Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Überwachen der Identität und des Zugriffs](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Physische oder logische Trennung von Anwendungen mit hohem Risiko

**Leitfaden**: Verwenden Sie Funktionen von Azure Kubernetes Service (AKS), um Teams und Workloads im gleichen Cluster logisch zu isolieren, um die geringste Anzahl von Berechtigungen gemäß den für die einzelnen Teams erforderlichen Ressourcen bereitzustellen. 

Implementieren Sie den Namespace in Kubernetes, um eine logische Isolationsgrenze zu erstellen. Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.ContainerService“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration Ihrer AKS-Instanzen (Azure Kubernetes Service) zu erstellen. 

Überprüfen und implementieren Sie zusätzliche Kubernetes-Features und -Überlegungen für Isolation und Mehrinstanzenfähigkeit für Folgendes: Zeitplanung, Netzwerke, Authentifizierung/Autorisierung und Container. Implementieren Sie außerdem separate Abonnements oder Verwaltungsgruppen für Entwicklungs-, Test- und Produktionsumgebungen. Trennen Sie AKS-Cluster mit virtuellen Netzwerken bzw. Subnetzen, die entsprechend gekennzeichnet und durch eine Web Application Firewall (WAF) gesichert werden.

- [Informationen zu bewährten Methoden für die Clusterisolierung in AKS](operator-best-practices-cluster-isolation.md)

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

- [Informationen zu bewährten Methoden für Netzwerkkonnektivität und Sicherheit in AKS](operator-best-practices-network.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.ContainerService“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration Ihrer AKS-Instanzen (Azure Kubernetes Service) zu erstellen. Verwenden Sie integrierte Azure Policy-Definitionen.

Beispiele für integrierte Richtliniendefinitionen für AKS:

• Eingehenden HTTPS-Datenverkehr im Kubernetes-Cluster erzwingen.

• In Kubernetes Services sollten autorisierte IP-Adressbereiche definiert werden.

• Für Kubernetes-Dienste muss die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) verwendet werden.

• Nur Ausführung zulässiger Containerimages im Kubernetes-Cluster zulassen.

Exportieren Sie eine Vorlage der AKS-Konfiguration in JSON (JavaScript Object Notation) mit Azure Resource Manager. Überprüfen Sie diese regelmäßig, um sicherzustellen, dass diese Konfigurationen die Sicherheitsanforderungen Ihrer Organisation erfüllen. Verwenden Sie die Empfehlungen von Azure Security Center als sichere Konfigurationsbaseline für Ihre Azure-Ressourcen. 

- [Konfigurieren und Verwalten von Sicherheitsrichtlinien für AKS-Pods](use-pod-security-policies.md)

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Leitfaden**: AKS-Cluster (Azure Kubernetes Service) werden auf virtuellen Hostcomputern mit einem sicherheitsoptimierten Betriebssystem bereitgestellt. In das Hostbetriebssystem sind zusätzliche Schritte für Sicherheitshärtung integriert, um die Angriffsfläche zu verringern und die Bereitstellung von Containern auf sichere Weise zu ermöglichen. 

Azure wendet tägliche Patches (einschließlich Sicherheitspatches) auf AKS-Hosts für virtuelle Computer an. Für einige Patches ist ein Neustart erforderlich. Kunden sind für die Planung von AKS-VM-Hostneustarts nach Bedarf verantwortlich. 

- [Sicherheitshärtung beim AKS-Hostbetriebssystem für Agent-Knoten](security-hardened-vm-host-image.md)

- [Informationen zur Sicherheitshärtung bei AKS-Hosts für virtuelle Computer](security-hardened-vm-host-image.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Sichern Sie Ihren AKS-Cluster (Azure Kubernetes Service) mithilfe von Sicherheitsrichtlinien für Pods.  Schränken Sie ein,welche Pods geplant werden können, um die Sicherheit Ihres AKS-Clusters zu verbessern. 

Pods, die nicht zulässige Ressourcen anfordern, können nicht im AKS-Cluster ausgeführt werden. 

Verwenden Sie außerdem die Azure Policy-Richtlinien [deny] (Verweigern) und [deploy if not exist] (Bereitstellen, falls nicht vorhanden), um sichere Einstellungen für die Azure-Ressourcen zu erzwingen, die mit Ihren AKS-Bereitstellungen verknüpft sind (z. B. virtuelle Netzwerke, Subnetze, Azure Firewall-Instanzen, Azure Storage-Konten usw.). 

Erstellen Sie benutzerdefinierte Azure Policy-Definitionen mit Aliasen aus den folgenden Namespaces: 

• Microsoft.ContainerService

• Microsoft.Network

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Leitfaden**: AKS-Cluster (Azure Kubernetes Service) werden auf virtuellen Hostcomputern mit einem sicherheitsoptimierten Betriebssystem bereitgestellt. In das Hostbetriebssystem sind zusätzliche Schritte für Sicherheitshärtung integriert, um die Angriffsfläche zu verringern und die Bereitstellung von Containern auf sichere Weise zu ermöglichen. 

Weitere Informationen finden Sie in der Liste der CIS-Steuerelemente (Center for Internet Security), die in das Hostbetriebssystem integriert sind.  

- [Sicherheitshärtung beim AKS-Hostbetriebssystem für Agent-Knoten](security-hardened-vm-host-image.md)

- [Grundlegendes zur Zustandskonfiguration von AKS-Clustern](concepts-clusters-workloads.md#control-plane)

- [Informationen zur Sicherheitshärtung bei AKS-Hosts für virtuelle Computer](security-hardened-vm-host-image.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Repos, um Ihre Konfigurationen sicher zu speichern und zu verwalten, wenn Sie benutzerdefinierte Azure Policy-Definitionen verwenden. Exportieren Sie eine Vorlage der AKS-Konfiguration (Azure Kubernetes Service) in JSON (JavaScript Object Notation) mit Azure Resource Manager. Überprüfen Sie diese regelmäßig, um sicherzustellen, dass die Konfigurationen die Sicherheitsanforderungen Ihrer Organisation erfüllen. 

Implementieren Sie Drittanbieterlösungen wie Terraform, um eine Konfigurationsdatei zu erstellen, die die Ressourcen für den Kubernetes-Cluster deklariert. Sie können Ihre AKS-Bereitstellung härten, indem Sie bewährte Sicherheitsmethoden implementieren und Ihre Konfiguration als Code an einem gesicherten Ort speichern.

- [Definieren eines Kubernetes-Clusters](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks#define-a-kubernetes-cluster)

Sicherheitshärtung beim AKS-Hostbetriebssystem für Agent-Knoten

security-hardened-vm-host-image.md

- [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Leitfaden**: Gilt nicht für Azure Kubernetes Service (AKS). AKS stellt standardmäßig ein sicherheitsoptimiertes Hostbetriebssystem (OS) bereit. Es gibt aktuell keine Option zur Auswahl eines alternativen oder benutzerdefinierten Betriebssystems.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Abonnements mit integrierten Richtliniendefinitionen sowie Azure Policy-Aliasen im Namespace „Microsoft.ContainerService“ erstellt werden können. 

Erstellen Sie benutzerdefinierte Richtlinien, um Systemkonfigurationen zu überwachen und zu erzwingen. Entwickeln Sie einen Prozess und eine Pipeline zum Verwalten von Richtlinienausnahmen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Verwenden von Aliasen](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Konfigurationsverwaltungstools für Betriebssysteme

**Leitfaden**: AKS-Cluster (Azure Kubernetes Service) werden auf virtuellen Hostcomputern mit einem sicherheitsoptimierten Betriebssystem bereitgestellt. In das Hostbetriebssystem sind zusätzliche Schritte für Sicherheitshärtung integriert, um die Angriffsfläche zu verringern und die Bereitstellung von Containern auf sichere Weise zu ermöglichen. 

Weitere Informationen finden Sie in der Liste der CIS-Steuerelemente (Center for Internet Security), die in AKS-Hosts integriert sind.  

- [Sicherheitshärtung beim AKS-Hostbetriebssystem für Agent-Knoten](security-hardened-vm-host-image.md)

- [Informationen zur Sicherheitshärtung bei AKS-Hosts für virtuelle Computer](security-hardened-vm-host-image.md)

- [Grundlegendes zur Zustandskonfiguration von AKS-Clustern](concepts-clusters-workloads.md#control-plane)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Ressourcen

**Leitfaden**: Verwenden Sie Security Center, um Baselineüberprüfungen für Ressourcen im Zusammenhang mit Ihren AKS-Bereitstellungen (Azure Kubernetes Service) durchzuführen. Zu den Beispielressourcen gehören u. a. der AKS-Cluster selbst, das virtuelle Netzwerk, in dem der AKS-Cluster bereitgestellt wurde, das Azure Storage-Konto, das zur Nachverfolgung des Terraform-Status verwendet wird, oder Azure Key Vault-Instanzen, die für die Verschlüsselungsschlüssel für die Betriebssysteme und Datenträger Ihres AKS-Clusters verwendet werden.

- [Umsetzen von Empfehlungen in Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Leitfaden**: Verwenden Sie Security Center-Containerempfehlungen im Abschnitt „Compute &amp; Apps“, um Baselineüberprüfungen grundlegende Scans für Ihre AKS-Cluster (Azure Kubernetes Service) auszuführen. Lassen Sie sich im Security Center-Dashboard benachrichtigen, wenn Konfigurationsprobleme oder Sicherheitsrisiken gefunden werden. Dies erfordert die Aktivierung des optionalen Pakets für Containerregistrierungen, das Security Center das Überprüfen des Images ermöglicht.  

- [Grundlegendes zu Azure Security Center-Containerempfehlungen](../security-center/container-security.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Leitfaden**: Integrieren Sie Azure Key Vault mithilfe eines FlexVolume-Laufwerks in einen AKS-Cluster (Azure Kubernetes Service). Verwenden Sie Azure Key Vault, um Geheimnisse wie Anmeldeinformationen, Speicherkontoschlüssel oder Zertifikate zu speichern und regelmäßig zu rotieren. Mit dem FlexVolume-Treiber kann der AKS-Cluster nativ Anmeldeinformationen aus dem Schlüsseltresor abrufen und diese sicher nur dem anfordernden Pod zur Verfügung stellen. Verwenden Sie eine verwaltete Podidentität, um den Zugriff auf den Schlüsseltresor anzufordern und die erforderlichen Anmeldeinformationen über den FlexVolume-Treiber abzurufen. Stellen Sie sicher, dass das vorläufige Löschen für Azure Key Vault aktiviert ist. 

Schränken Sie die Verfügbarkeit von Anmeldeinformationen ein, indem Sie keine Anmeldeinformationen in Ihrem Anwendungscode definieren. 

Vermeiden Sie die Verwendung von festen oder freigegebenen Anmeldeinformationen. 

- [Sicherheitskonzepte für Anwendungen und Cluster in Azure Kubernetes Service (AKS)](concepts-security.md)

- [Verwenden von Key Vault mit Ihrem AKS-Cluster](developer-best-practices-pod-security.md#limit-credential-exposure)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Definieren Sie als bewährte Sicherheitsmaßnahme keine Anmeldeinformationen in Ihrem Anwendungscode. Verwenden Sie verwaltete Identitäten für Azure-Ressourcen, damit sich ein Pod bei jedem Dienst in Azure, der ihn unterstützt, authentifizieren kann, z. B. bei Azure Key Vault. Dem Pod wird eine Azure-Identität zugewiesen, um sich bei Azure Active Directory (Azure AD) zu authentifizieren und ein digitales Token zu erhalten, das anderen Azure-Diensten vorgelegt werden kann, die überprüfen, ob der Pod berechtigt ist, auf den Dienst zuzugreifen und die erforderlichen Aktionen durchzuführen. 

Beachten Sie, dass Pod-verwaltete Identitäten nur für die Verwendung mit Linux-Pods und -Containerimages vorgesehen sind. Stellen Sie stattdessen Azure Key Vault bereit, um digitale Schlüssel und Anmeldeinformationen zu speichern und abzurufen. Schlüssel, z. B. diejenigen, die zum Verschlüsseln von Betriebssystemdatenträgern verwendet werden, können in Azure Key Vault gespeichert werden.

Dienstprinzipale können ebenfalls in AKS-Clustern verwendet werden. Cluster mit Dienstprinzipalen erreichen jedoch möglicherweise einen Zustand, in dem der Dienstprinzipal erneuert werden muss, damit der Cluster ordnungsgemäß funktioniert. Das Verwalten von Dienstprinzipalen erhöht die Komplexität, daher ist es einfacher, stattdessen verwaltete Identitäten zu verwenden. Dieselben Berechtigungsanforderungen gelten sowohl für Dienstprinzipale als auch für verwaltete Identitäten.

- [Grundlegendes zu verwalteten Identitäten und Key Vault mit Azure Kubernetes Service (AKS)](developer-best-practices-pod-security.md#limit-credential-exposure)

- [Azure Active Directory-Podidentität](https://github.com/Azure/aad-pod-identity)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Anleitung:** Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) durch Empfehlungen befürwortet.

Schränken Sie die Verfügbarkeit von Anmeldeinformationen ein, indem Sie keine Anmeldeinformationen in Ihrem Anwendungscode definieren. Vermeiden Sie die Verwendung von freigegebenen Anmeldeinformationen. Azure Key Vault sollte verwendet werden, um digitale Schlüssel und Anmeldeinformationen zu speichern und abzurufen. Verwenden Sie verwaltete Identitäten für Azure-Ressourcen, damit Ihr Pod Zugriff auf andere Ressourcen anfordern kann. 

- [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Bewährte Entwicklermethoden für Podsicherheit](developer-best-practices-pod-security.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Schutz vor Schadsoftware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: Verwenden zentral verwalteter Antischadsoftware

**Leitfaden**: AKS verwaltet den Lebenszyklus und die Vorgänge von Agent-Knoten für Sie. Das Ändern der IaaS-Ressourcen, die den Agent-Knoten zugeordnet sind, wird nicht unterstützt. Allerdings können Sie für Linux-Knoten Daemonsets verwenden, um benutzerdefinierte Software wie eine Antischadsoftwarelösung zu installieren.

- [Sicherheitswarnungen: Referenzhandbuch](../security-center/alerts-reference.md)

- [Warnungen für Container: Azure Kubernetes Service-Cluster](../security-center/alerts-reference.md#alerts-akscluster)

- [Gemeinsame AKS-Verantwortung und Daemonsets](support-policies.md#shared-responsibility)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Überprüfen Sie vorab alle Dateien, die in Ihre AKS-Ressourcen hochgeladen werden. Mit der Bedrohungserkennung für Datendienste von Security Center können Sie Schadsoftware erkennen, die in Speicherkonten hochgeladen wurde, wenn Sie ein Azure Storage-Konto als Datenspeicher zum Nachverfolgen des Terraform-Status für Ihren AKS-Cluster verwenden. 

- [Grundlegendes zur Bedrohungserkennung für Datendienste in Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: AKS verwaltet den Lebenszyklus und die Vorgänge von Agent-Knoten für Sie. Das Ändern der IaaS-Ressourcen, die den Agent-Knoten zugeordnet sind, wird nicht unterstützt. Allerdings können Sie für Linux-Knoten Daemonsets verwenden, um benutzerdefinierte Software wie eine Antischadsoftwarelösung zu installieren.

- [Sicherheitswarnungen: Referenzhandbuch](../security-center/alerts-reference.md)

- [Warnungen für Container: Azure Kubernetes Service-Cluster](../security-center/alerts-reference.md#alerts-akscluster)

- [Gemeinsame AKS-Verantwortung und Daemonsets](support-policies.md#shared-responsibility)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Sichern Sie Ihre Daten mit einem geeigneten Tool für Ihren Speichertyp (z. B. Velero), mit dem persistente Volumes zusammen mit zusätzlichen Clusterressourcen und -konfigurationen gesichert werden können. Überprüfen Sie die Integrität und die Sicherheit dieser Sicherungen regelmäßig. 

Entfernen Sie den Zustand von Ihren Anwendungen vor der Sicherung. In Fällen, in denen dies nicht möglich ist, sollten Sie die Daten von persistenten Volumes sichern und die Wiederherstellungsvorgänge regelmäßig testen, um die Datenintegrität und die erforderlichen Prozesse zu überprüfen.

- [Bewährte Methoden für Speicher und Sicherungen in AKS](operator-best-practices-storage.md)

- [Bewährte Methoden für Geschäftskontinuität und Notfallwiederherstellung in AKS](operator-best-practices-multi-region.md)

- [Informationen zu Azure Site Recovery](../site-recovery/site-recovery-overview.md)

- [Einrichten von Velero in Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller kundenseitig verwalteten Schlüssel

**Leitfaden**: Sichern Sie Ihre Daten mit einem geeigneten Tool für Ihren Speichertyp (z. B. Velero), mit dem persistente Volumes zusammen mit zusätzlichen Clusterressourcen und -konfigurationen gesichert werden können. 

Führen Sie regelmäßige automatisierte Sicherungen von Key Vault-Zertifikaten, -Schlüsseln, verwalteten Speicherkonten und -Geheimnissen mit PowerShell-Befehlen aus. 

Zum Beispiel:

Backup-AzKeyVaultCertificate Backup-AzKeyVaultKey Backup-AzKeyVaultManagedStorageAccount Backup-AzKeyVaultSecret

- [Sichern von Key Vault-Zertifikaten](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

- [Sichern von Key Vault-Schlüsseln](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Sichern von verwalteten Key Vault-Speicherkonten](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Sichern von Key Vault-Geheimnissen](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

- [Aktivieren von Azure Backup](../backup/index.yml)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich kundenseitig verwalteter Schlüssel

**Leitfaden**: Führen Sie die Datenwiederherstellung von Inhalten in Velero Backup regelmäßig aus. Testen Sie die Wiederherstellung ggf. in einem isolierten virtuellen Netzwerk.

Führen Sie mithilfe von PowerShell-Befehlen regelmäßig Datenwiederherstellungen von Key Vault-Zertifikaten, -Schlüsseln und -Geheimnissen und von verwalteten Speicherkonten durch. 

Zum Beispiel:

Restore-AzKeyVaultCertificate Restore-AzKeyVaultKey Restore-AzKeyVaultManagedStorageAccount Restore-AzKeyVaultSecret

- [Wiederherstellen von Key Vault-Zertifikaten](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

- [Wiederherstellen von Key Vault-Schlüsseln](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

- [Wiederherstellen von per Key Vault verwalteten Storage-Konten](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Wiederherstellen von Key Vault-Geheimnissen](/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0)

- [Wiederherstellen von Dateien aus einer Sicherung von virtuellen Azure-Computern](../backup/backup-azure-restore-files-from-vm.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Sichern Sie Ihre Daten mit einem geeigneten Tool für Ihren Speichertyp (z. B. Velero), mit dem persistente Volumes zusammen mit zusätzlichen Clusterressourcen und -konfigurationen gesichert werden können. 

Wenn Azure Key Vault für Ihre AKS-Bereitstellungen (Azure Kubernetes Service) verwendet wird, sollten Sie in Key Vault vorläufiges Löschen aktivieren, um Schlüssel vor dem versehentlichen oder böswilligen Löschen zu schützen.

- [Azure-Speicherdienstverschlüsselung](../storage/common/storage-service-encryption.md)

- [Aktivieren des vorläufigen Löschens in Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Anleitung:** Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle für Ihre Organisation. Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind.

- [Leitfaden zu Planung und Betrieb](../security-center/security-center-planning-and-operations-guide.md)

- [Anleitung zum Entwickeln eines Prozesses für die Reaktion auf Sicherheitsvorfälle](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Struktur eines Vorfalls laut Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Kunden können außerdem den Computer Security Incident Handling Guide des US-amerikanischen National Institute of Standards and Technology (NIST) nutzen, um einen Plan zur Reaktion auf Incidents auszuarbeiten.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Leitfaden**: Priorisieren Sie anhand der Schweregrade, die Security Center Warnungen zuweist, welche Warnungen zuerst untersucht werden müssen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.
Markieren Sie Abonnements aussagekräftig (z. B. „Produktion“, „Nicht-Produktion“), und erstellen Sie ein Benennungssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren.

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Tests zur Reaktionsfähigkeit Ihrer Systeme auf Vorfälle durch. Konsultieren Sie die folgende NIST-Publikation zum Identifizieren von Schwachstellen und Lücken, und überarbeiten Sie den Plan bei Bedarf.

- [Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf die Kundendaten zugegriffen haben. Überprüfen Sie die Vorfälle anschließend, um sicherzustellen, dass die Probleme behoben wurden.

- [Festlegen der Kontaktinformationen in Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie Security Center-Warnungen und -Empfehlungen mithilfe des Features „Fortlaufender Export“. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können außerdem den Security Center-Datenconnector verwenden, um die Warnungen gemäß der Anforderungen Ihrer Geschäftsvorgänge an Azure Sentinel zu streamen.

- [Konfigurieren des fortlaufenden Exports](../security-center/continuous-export.md)

- [Streamen von Warnungen in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um über „Logic Apps“ automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen.

- [Konfigurieren von Workflowautomatisierung und Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Durchführen regelmäßiger Penetrationstests Ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnissen

**Leitfaden**: Befolgen Sie die Microsoft Rules of Engagement, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- Weitere Informationen zur Microsoft-Strategie im Zusammenhang mit Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastrukturen, Dienste und Anwendungen sowie zu deren Durchführung finden Sie [hier](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e).

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).

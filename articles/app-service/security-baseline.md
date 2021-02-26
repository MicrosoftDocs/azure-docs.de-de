---
title: Azure-Sicherheitsbaseline für App Service
description: Die App Service-Sicherheitsbaseline enthält Anleitungen und Ressourcen für die Implementierung der Sicherheitsempfehlungen, die im Azure-Sicherheitsvergleichstest definiert sind.
author: msmbaldwin
ms.service: app-service
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 648e7991784212e4fe781efd5d9f01cb571c5012
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807851"
---
# <a name="azure-security-baseline-for-app-service"></a>Azure-Sicherheitsbaseline für App Service

Die Azure-Sicherheitsbaseline für App Service enthält Empfehlungen, mit deren Hilfe Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können. Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](../security/benchmarks/overview-v1.md) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können. Der Inhalt wird anhand der **Sicherheitskontrollen** gruppiert, die durch den Azure-Sicherheitsvergleichstest und die entsprechenden für App Service geltenden Empfehlungen definiert werden. Nicht auf App Service anwendbare **Kontrollen** wurden ausgeschlossen.

Die vollständige Zuordnung von App Service zum Azure-Sicherheitsvergleichstest finden Sie in der [vollständigen Zuordnungsdatei der App Service-Sicherheitsbaseline](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Schützen von Azure-Ressourcen in virtuellen Netzwerken

**Leitfaden**: Wenn Sie App Service im Tarif „App Service (isoliert)“ verwenden, der auch als App Service-Umgebung (App Service Environment, ASE) bezeichnet wird, kann die Bereitstellung direkt in einem Subnetz innerhalb Ihres virtuellen Azure-Netzwerks erfolgen. Schützen Sie Ihre Azure App Service-Umgebung mithilfe von Netzwerksicherheitsgruppen, indem Sie ein- und ausgehenden Datenverkehr an Ressourcen in Ihrem virtuellen Netzwerk blockieren oder den Zugriff auf Apps in einer App Service-Umgebung einschränken.

Standardmäßig enthalten Netzwerksicherheitsgruppen eine implizite Ablehnungsregel mit der niedrigsten Priorität, sodass Sie explizit Zulassungsregeln hinzufügen müssen. Fügen Sie Zulassungsregeln für Ihre Netzwerksicherheitsgruppe hinzu, und halten Sie sich dabei an einen Netzwerkansatz mit geringstmöglichen Rechten. Auf die zugrunde liegenden VMs zum Hosten der App Service-Umgebung kann nicht direkt zugegriffen werden, da sie sich in einem von Microsoft verwalteten Abonnement befinden.

Schützen Sie die App Service-Umgebung durch das Weiterleiten von Datenverkehr über eine Azure Application Gateway-Instanz, auf der eine Web Application Firewall (WAF) aktiviert ist. Nutzen Sie für das Anwendungsgateway Dienstendpunkte, um den eingehenden Datenverkehr an Ihre App zu schützen.  

Bei App Service für mehrere Instanzen (Apps, die nicht im Tarif „App Service (isoliert)“ bereitgestellt wurden) können Sie ausgehenden Datenverkehr von Ihrer App mithilfe von Netzwerksicherheitsgruppen blockieren. Mit dem Feature Virtual Network-Integration können Sie Ihren Apps Zugriff auf Ressourcen in einem virtuellen Netzwerk oder über dieses ermöglichen. Sie können mit diesem Feature auch ausgehenden Datenverkehr von der App an öffentliche Adressen blockieren. Virtual Network-Integration kann nicht dazu verwendet werden, eingehenden Zugriff auf eine App bereitzustellen.  

Schützen Sie eingehenden Datenverkehr an Ihre App:
- Zugriffsbeschränkungen: verschiedene Zulassungs- oder Ablehnungsregeln zur Steuerung des eingehenden Zugriffs
- Dienstendpunkte: zum Verweigern von eingehendem Datenverkehr von außerhalb der angegebenen virtuellen Netzwerke oder Subnetze
- Private Endpunkte: zum Verfügbarmachen Ihrer App in Ihrem virtuellen Netzwerk über eine private IP-Adresse. Wenn private Endpunkte in Ihrer App aktiviert sind, ist diese nicht mehr über das Internet verfügbar.

Wenn Sie das Feature Virtual Network-Integration mit virtuellen Netzwerken in derselben Region verwenden, nutzen Sie Netzwerksicherheitsgruppen und Routingtabellen mit benutzerdefinierten Routen. Benutzerdefinierte Routen können im integrierten Subnetz platziert werden, damit ausgehender Datenverkehr wie vorgesehen gesendet wird.  

Erwägen Sie auch die Implementierung von Azure Firewall. Damit können Sie Richtlinien zur Anwendungs- und Netzwerkkonnektivität für alle Ihre Abonnements und virtuellen Netzwerke zentral erstellen, erzwingen und protokollieren. Azure Firewall verwendet eine statische öffentliche IP-Adresse für Ressourcen im virtuellen Netzwerk, die es außenstehenden Firewalls ermöglicht, Datenverkehr aus Ihrem virtuellen Netzwerk zu identifizieren. 

- [Sperren einer App Service-Umgebung](environment/firewall-integration.md)

- [Schutz vor den wichtigsten 10 OWASP-Sicherheitsrisiken (Open Web Application Security Project)](https://owasp.org/www-project-top-ten/)

- [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md)

- [Integrieren Ihrer App in ein Azure Virtual Network](web-sites-integrate-with-vnet.md)

- [Überlegungen zum Netzwerkbetrieb in einer App Service-Umgebung](environment/network-info.md)

- [Erstellen einer externen App Service-Umgebung](environment/create-external-ase.md)

- [Erstellen einer internen App Service-Umgebung](environment/create-ilb-ase.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von virtuellen Netzwerken, Subnetzen und Netzwerkschnittstellen

**Leitfaden**: Implementieren Sie die Empfehlungen für den Netzwerkschutz von Azure Security Center, um Netzwerkressourcen und -konfigurationen zu schützen, die sich auf Ihre App Service-Apps und -APIs beziehen.

Nutzen Sie Azure Firewall für das Weiterleiten von Datenverkehr und das Erstellen, Erzwingen und Protokollieren von Richtlinien zur Anwendungs- und Netzwerkkonnektivität übergreifend für Abonnements und virtuelle Netzwerke. Azure Firewall verwendet eine statische öffentliche IP-Adresse für Ressourcen in Ihrem virtuellen Netzwerk, die es außenstehenden Firewalls ermöglicht, Datenverkehr aus Ihrem virtuellen Netzwerk zu identifizieren. Der Azure Firewall-Dienst ist auch vollständig in Azure Monitor integriert und kann für Protokollierung und Analyse verwendet werden.

- [Übersicht über Azure Firewall](../firewall/overview.md)

- [Grundlegendes zu der von Azure Security Center bereitgestellten Netzwerksicherheit](../security-center/security-center-network-recommendations.md)

- [Aktivieren der Überwachung und des Schutzes von App Service](../security-center/defender-for-app-service-introduction.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Leitfaden**: Schützen Sie eine über das Internet zugängliche App in einer App Service-Umgebung (ASE) durch folgende Vorgehensweisen:
- Bereitstellen einer Web Application Firewall (WAF) mit Azure Application Gateway vor einer App mit Internetzugriff
- Verwenden von Zugriffsbeschränkungen zum Schützen des eingehenden Datenverkehrs an Application Gateway
- Schützen der App mit Azure Active Directory (Azure AD) zum Sicherstellen der Authentifizierung
- Festlegen der TLS-Mindestversion auf 1.2
- Festlegen von HTTPS als einzige Option für die App

Leiten Sie den gesamten ausgehenden Datenverkehr der Anwendung über ein Azure Firewall-Gerät, und überwachen Sie die Protokolle. 

So schützen Sie eine über das Internet zugängliche App in App Service für mehrere Instanzen (z. B. nicht im Tarif „App Service (isoliert)“)
- Bereitstellen eines Geräts mit aktivierter Web Application Firewall vor einer App
- Verwenden von Zugriffsbeschränkungen oder Dienstendpunkten zum Schützen des eingehenden Datenverkehrs an das WAF-Gerät (Web Application Firewall)
- Schützen der App mit Azure AD zum Sicherstellen der Authentifizierung
- Festlegen der TLS-Mindestversion auf 1.2
- Festlegen von HTTPS als einzige Option für die App
- Nutzen Sie Virtual Network-Integration und die App-Einstellung „WEBSITE_VIRTUAL NETWORK_ROUTE_ALL“, damit auf den gesamten ausgehenden Datenverkehr Netzwerksicherheitsgruppen und benutzerdefinierte Routen im Integrationssubnetz angewandt werden.

Leiten Sie bei der App Service-Umgebungs-App den gesamten ausgehenden Datenverkehr der Anwendung über ein Azure Firewall-Gerät, und überwachen Sie die Protokolle in der App.

Lesen und befolgen Sie außerdem die Empfehlungen im Dokument „Sperren einer App Service-Umgebung“.

- [Sperren einer App Service-Umgebung](environment/firewall-integration.md)

- [Azure Web Application Firewall für Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Azure App Service-Zugriffseinschränkungen](app-service-ip-restrictions.md)

- [Nachverfolgen von WAF-Warnungen zum mühelosen Überwachen von Trends mit Azure Monitor](../azure-monitor/overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten schädlichen IP-Adressen

**Leitfaden**: Schützen Sie die App Service-Umgebung, wie in der Dokumentation „Sperren einer App Service-Umgebung“ beschrieben wird. Wenden Sie die integrierte Threat Intelligence-Funktion von Azure Security Center an, um die Kommunikation mit bekannten schädlichen oder nicht genutzten öffentlichen IP-Adressen zu verweigern. Verwenden Sie Zugriffsbeschränkungen zum Schützen des eingehenden Datenverkehrs an Application Gateway. 

Schützen Sie App Service für mehrere Instanzen (Apps, die nicht im Tarif „App Service (isoliert)“ bereitgestellt wurden) über einen öffentlichen Endpunkt mit Internetzugriff. Dieser erlaubt nur Datenverkehr von einem bestimmten Subnetz innerhalb Ihres virtuellen Netzwerks und blockiert alle anderen Verbindungen. Verwenden Sie Zugriffsbeschränkungen, um Zugriffssteuerungslisten für das Netzwerk (IP-Einschränkungen) zu konfigurieren und damit nur den zulässigen eingehenden Datenverkehr zuzulassen.

Definieren Sie Prioritäten für die Zulassungs- oder Ablehnungslisten, um den Netzwerkzugriff auf Ihre App zu verwalten. Die Listen können IP-Adressen oder Subnetze virtueller Netzwerke enthalten. Wenn mindestens ein Eintrag vorhanden ist, weist die Liste am Ende eine implizite Regel vom Typ „Alle ablehnen“ auf. Diese Funktion funktioniert mit allen von App Service gehosteten Workloads. Hierzu gehören Web-Apps, API-Apps, Linux-Apps, Linux-Container-Apps und Functions. 

Verwenden Sie Dienstendpunkte, um den Zugriff auf Ihre Web-App aus einem virtuellen Azure-Netzwerk zu beschränken. Beschränken Sie den Zugriff auf App Service für mehrere Instanzen (nicht im Tarif „App Service (isoliert)“) aus ausgewählten Subnetzen mit Dienstendpunkten. 

- [Statische Azure App Service-IP-Einschränkungen](app-service-ip-restrictions.md)

- [Azure Web Application Firewall für Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Konfigurieren einer Web Application Firewall (WAF) für eine App Service-Umgebung](environment/app-service-app-service-environment-web-application-firewall.md)

- [Schützen der ASE wie unter „Sperren einer App Service-Umgebung“ beschrieben](environment/firewall-integration.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets"></a>1.5: Aufzeichnen von Netzwerkpaketen

**Leitfaden**: Überwachen Sie mit Security Center Anforderungen und Antworten, die an und von App Service-Apps gesendet werden. Angriffe auf eine Webanwendung können mithilfe einer Echtzeitinstanz von Application Gateway mit einer Web Application Firewall überwacht werden, in der die integrierte Protokollierung von Azure Monitor aktiviert ist. Damit können Sie Web Application Firewall-Warnungen nachverfolgen und Trends ganz einfach überwachen.

- [Azure Web Application Firewall für Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Leitfaden**: Verwalten Sie den Datenverkehr einer App in einer App Service-Umgebung.

- Schützen der App Service-Umgebung wie unter „Sperren einer App Service-Umgebung“ beschrieben
- Bereitstellen einer Application Gateway-Instanz mit Azure Web Application Firewall vor Ihren Apps mit Internetzugriff
- Erzwingen von HTTPS für den Zugriff auf die App

Verwalten Sie den Datenverkehr für eine über das Internet zugängliche App in App Service für mehrere Instanzen (nicht im Tarif „App Service (isoliert)“): 

- Bereitstellen einer Application Gateway-Instanz mit aktivierter Azure Web Application Firewall vor Ihren Apps mit Internetzugriff
- Verwenden von Zugriffsbeschränkungen oder Dienstendpunkten zum Schützen des eingehenden Datenverkehrs an die Web Application Firewall. Die Funktion für Zugriffsbeschränkungen funktioniert mit allen von App Service gehosteten Workloads. Hierzu gehören Web-Apps, API-Apps, Linux-Apps, Linux-Container-Apps und Functions.

- Erzwingen von HTTPS für den Zugriff auf die App
- Beschränken des Zugriffs auf Ihre App Service-App mit Einschränkungen für statische IP-Adressen, sodass nur Datenverkehr von der VIP auf einem Anwendungsgateway als einzige Adresse mit Zugriff empfangen wird.

Weitere Informationen finden Sie unter den angegebenen Links.

- [Statische Azure App Service-IP-Einschränkungen](app-service-ip-restrictions.md)

- [Azure Web Application Firewall für Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Konfigurieren von End-to-End-TLS mit Application Gateway im Azure-Portal](../application-gateway/end-to-end-ssl-portal.md)

- [Schützen der ASE wie unter „Sperren einer App Service-Umgebung“ beschrieben](./environment/firewall-integration.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: App Service hat einige Endpunkte, die zur Verwaltung des Diensts verwendet werden. Diese Endpunktadressen sind auch im IP-Diensttag AppServiceManagement enthalten. Das Tag AppServiceManagement wird nur mit einer App Service-Umgebung verwendet, um solchen Datenverkehr zuzulassen. 

Indem Sie den Diensttagnamen im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Die eingehenden Adressen für App Service werden im IP-Diensttag AppService nachverfolgt. Es gibt kein IP-Diensttag, das die ausgehenden Adressen enthält, die von App Service verwendet werden.

Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

- [Diensttags für virtuelle Netzwerke](../virtual-network/service-tags-overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für die Netzwerkeinstellungen Ihrer App Service-Apps. 

Verwalten Sie die Sicherheitskonfigurationen mithilfe von Azure Policy-Aliasen in den Namespaces „Microsoft.Web“ und „Microsoft.Network“. Erstellen Sie benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfigurationen Ihrer App Service-Apps. 

Verwenden Sie integrierte Richtliniendefinitionen für App Service, z. B.:
- Die App sollte einen VNET-Dienstendpunkt verwenden.
- Die App sollte nur über HTTPS zugänglich sein.
- Legen Sie die TLS-Mindestversion auf die aktuelle Version fest.

Weitere Informationen finden Sie unter den angegebenen Links.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Konfigurieren von End-to-End-TLS mit Application Gateway im Azure-Portal](../application-gateway/end-to-end-ssl-portal.md)

- [Schützen der ASE wie unter „Sperren einer App Service-Umgebung“ beschrieben](./environment/firewall-integration.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Verwenden Sie Tags für Netzwerksicherheitsgruppen und andere zugehörige Ressourcen wie den Datenverkehrsfluss in App Service.

Verwenden Sie für einzelne Netzwerksicherheitsgruppen-Regeln das Feld „Beschreibung“, um für Regeln, die Datenverkehr in ein oder aus einem Netzwerk zulassen, geschäftliche Anforderungen, eine Dauer usw. festzulegen.

Wenden Sie eine der integrierten Azure Policy-Definitionen für das Tagging an (z. B. „Tag und zugehörigen Wert erzwingen“), um sicherzustellen, dass alle Ressourcen mit Tags erstellt werden und Sie über vorhandene nicht markierte Ressourcen benachrichtigt werden. Verwenden Sie Azure PowerShell oder die Azure CLI, um nach Ressourcen basierend auf ihren Tags zu suchen oder Aktionen für diese Ressourcen auszuführen.

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

- [Azure App Service-Zugriffseinschränkungen](./app-service-ip-restrictions.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen der Konfigurationen von Netzwerkressourcen und zum Erkennen von Änderungen bei Netzwerkeinstellungen und -ressourcen, die sich auf App Service beziehen. 

Wenden Sie eine der zahlreichen integrierten Azure Policy-Definitionen für App Service an, z. B. eine Richtlinie zur Überwachung von Apps hinsichtlich der Verwendung des Diensts für VNET-Endpunkte. Erstellen Sie Warnungen in Azure Monitor, die bei Änderungen an wichtigen Netzwerkeinstellungen oder -ressourcen ausgelöst werden. 

Überprüfen Sie die detaillierten Sicherheitswarnungen und -empfehlungen in Security Center, im Portal oder über programmgesteuerte Tools. Exportieren Sie diese Informationen, oder senden Sie sie an andere Überwachungstools in Ihrer Umgebung. Es stehen Tools zur Verfügung, über die Sie Warnungen und Empfehlungen manuell oder fortlaufend exportieren können. Mit diesen Tools können Sie folgende Aktionen ausführen:
 
- Fortlaufendes Exportieren in einen Log Analytics-Arbeitsbereich
- Fortlaufender Export in Azure Event Hubs (für Integrationen mit Drittanbieter-SIEMs)
- Exportieren in eine CSV-Datei (einmalig)

Es wird empfohlen, einen Prozess mit automatisierten Tools zu erstellen, um Konfigurationen von Netzwerkressourcen zu überwachen und Änderungen schnell zu erkennen.

- [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Erstellen von Warnungen in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

- [Exportieren von Sicherheitswarnungen und -empfehlungen](../security-center/continuous-export.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Integrieren Sie Ihre App Service-Umgebung (ASE) mit Azure Monitor, um Protokolle an Azure Storage, Azure Event Hubs oder Log Analytics zu senden. Aktivieren Sie die Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll, um Überwachungsprotokolle auf Steuerungsebene zu erhalten. Sicherheitswarnungen von Security Center werden im Azure-Aktivitätsprotokoll veröffentlicht. Überwachen Sie die Daten im Azure-Aktivitätsprotokoll, um Antworten auf die Fragen „Was“, „Wer“ und „Wann“ für alle Schreibvorgänge (PUT, POST, DELETE) zu erhalten, die auf Steuerungsebene für Azure App Service oder andere Azure-Ressourcen durchgeführt werden. Speichern Sie Ihre Abfragen für die spätere Verwendung, heften Sie Abfrageergebnisse an Azure-Dashboards an, und erstellen Sie Protokollwarnungen. Verwenden Sie auch die Datenzugriffs-REST-API in Application Insights, um programmgesteuert auf Ihre Telemetriedaten zuzugreifen.

Nutzen Sie die skalierbare, cloudnative SIEM-Lösung (Security Information & Event Management) Microsoft Azure Sentinel, um gemäß Ihren Geschäftsanforderungen Verbindungen mit verschiedenen Datenquellen und Connectors herzustellen. In Azure Marketplace können Sie auch SIEM-Systeme (Security Information & Event Management) von Drittanbietern (z. B. Barracuda) aktivieren und ein Onboarding Ihrer Daten durchführen.

- [Protokollieren von Aktivitäten in der App Service-Umgebung](environment/using-an-ase.md#logging)

- [Aktivieren der Diagnoseeinstellungen für Azure App Service](troubleshoot-diagnostic-logs.md)

- [Was ist Application Insights?](../azure-monitor/app/app-insights-overview.md)

- [Exportieren von Telemetriedaten aus Application Insights](../azure-monitor/app/export-telemetry.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Aktivieren Sie die Diagnoseeinstellungen für das Azure-Aktivitätsprotokoll, um Überwachungsprotokolle zu App Service auf Steuerungsebene zu erhalten. Senden Sie die Protokolle an einen Log Analytics-Arbeitsbereich, an Azure Event Hubs oder ein Azure Storage-Konto.
Antworten auf die Fragen „Was“, „Wer“ und „Wann“ für alle Schreibvorgänge (PUT, POST, DELETE), die auf Steuerungsebene durchgeführt werden, erhalten Sie, indem Sie die Daten aus dem Azure-Aktivitätsprotokoll für App Service und andere Azure-Ressourcen nutzen.

Darüber hinaus bietet Azure Key Vault eine zentrale Verwaltung von Geheimnissen mit Zugriffsrichtlinien und Überprüfungsverlauf. 

- [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

- [Aktivieren der Diagnoseeinstellungen für Azure App Service](troubleshoot-diagnostic-logs.md)

- [Resource Manager-Vorgänge](../role-based-access-control/resource-provider-operations.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Legen Sie in Azure Monitor den Aufbewahrungszeitraum für Protokolle für Log Analytics-Arbeitsbereiche, die Ihren App Service-Ressourcen zugeordnet sind, gemäß den Compliancevorschriften Ihrer Organisation fest.
- [Ändern des Datenaufbewahrungszeitraums](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Überprüfen Sie die Diagnoseeinstellungen des Azure-Aktivitätsprotokolls in Ihren App Service-Ressourcen anhand der an einen Log Analytics-Arbeitsbereich gesendeten Protokolle. Führen Sie Abfragen in Log Analytics durch, um nach Begriffen zu suchen, Trends zu identifizieren, Muster zu analysieren und viele andere Erkenntnisse zu gewinnen, die auf den erfassten Daten basieren.

Verwenden Sie Application Insights für Ihre App Service-Apps und zum Sammeln von Protokoll-, Leistungs- und Fehlerdaten. Sie können die mit Application Insights erfassten Telemetriedaten im Azure-Portal anzeigen.

Wenn Sie eine Web Application Firewall (WAF) bereitgestellt haben, können Sie Angriffe auf Ihre App Service-Apps über ein Echtzeitprotokoll der Web Application Firewall überwachen. Dieses Protokoll ist in Azure Monitor integriert und dient zum Nachverfolgen von Web Application Firewall-Warnungen sowie zum mühelosen Überwachen von Trends.

Verwenden Sie die skalierbare und cloudnative SIEM-Lösung (Security Information & Event Management) Azure Sentinel, um je nach Anforderungen verschiedene Datenquellen und Connectors zu integrieren. In Azure Marketplace können Sie optional auch SIEM-Lösungen von Drittanbietern aktivieren und ein Onboarding Ihrer Daten durchführen.

- [Erfassen und Analysieren des Azure-Aktivitätsprotokolls in Azure Monitor](../azure-monitor/platform/activity-log.md)

- [Was ist Application Insights?](../azure-monitor/app/app-insights-overview.md)

- [Integrieren Ihrer App Service-Umgebung mit Azure Application Gateway (WAF)](environment/integrate-with-application-gateway.md)

- [Schnellstart: Ausführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivieren von Warnungen bei anomalen Aktivitäten

**Leitfaden**: Konfigurieren Sie Security Center in Ihrem Azure-Abonnement, und überprüfen Sie die generierten Warnungen. Nutzen Sie Azure Monitor, um die Daten aus Ihrem Aktivitätsprotokoll an einen Event Hub zu senden. Dort können sie von einer SIEM-Lösung (Security Information & Event Management) wie Azure Sentinel gelesen werden. 

Wenn Sie Azure Web Application Firewall (WAF) bereitgestellt haben, können Sie Angriffe auf Ihre App Service-Apps über ein Echtzeitprotokoll der Web Application Firewall überwachen. Dieses Protokoll ist in Azure Monitor integriert und dient zum Nachverfolgen von Web Application Firewall-Warnungen (WAF) sowie zum mühelosen Überwachen von Trends.

- [Integrieren Ihrer App Service-Umgebung mit Azure Application Gateway (WAF)](environment/integrate-with-application-gateway.md)

- [Exportieren von Sicherheitswarnungen und -empfehlungen](../security-center/continuous-export.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Azure Active Directory (Azure AD) umfasst integrierte Rollen, die explizit zugewiesen werden müssen und abgefragt werden können. Verwenden Sie das Azure AD PowerShell-Modul, um Ad-hoc-Abfragen zum Ermitteln von Konten auszuführen, die Mitglieder von administrativen Gruppen sind.

- [Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

- [Verwenden verwalteter Identitäten für App Service und Azure Functions](overview-managed-identity.md?context=azure%2Factive-directory%2Fmanaged-identities-azure-resources%2Fcontext%2Fmsi-context&amp;tabs=dotnet)

- [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Azure Active Directory (Azure AD) verfolgt nicht das Konzept der Standardkennwörter. Es wird der Zugriff auf Steuerungsebene auf App Service ermöglicht.

Vermeiden Sie beim Erstellen eigener Apps generell das Implementieren von Standardkennwörtern für den Benutzerzugriff. Verwenden Sie einen der standardmäßig verfügbaren Identitätsanbieter für App Service, z. B. Azure AD, Microsoft-Konto, Facebook, Google oder Twitter.

Deaktivieren Sie den anonymen Zugriff, wenn er nicht unbedingt unterstützt werden muss. 

- [Standardmäßig verfügbare Identitätsanbieter in Azure App Service](overview-authentication-authorization.md#identity-providers)

- [Authentifizierung und Autorisierung in Azure App Service und Azure Functions](overview-authentication-authorization.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Erstellen Sie Standardbetriebsvorgänge für die Verwendung dedizierter Administratorkonten. Verwenden Sie die Identitäts- und Zugriffsverwaltungsfeatures in Security Center, um die Anzahl der Administratorkonten zu überwachen und nachzuverfolgen. 

Nutzen Sie die Empfehlungen von Security Center oder integrierte Azure-Richtlinien, z. B.:

- Ihrem Abonnement sollten mehrere Besitzer zugewiesen sein. 
- Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.

Richten Sie einen Prozess zum Überwachen der Konfigurationen von Netzwerkressourcen und zum Erkennen von Änderungen an Administratorkonten ein.

- [Verwenden von Azure Security Center zum Überwachen der Identität und des Zugriffs](../security-center/security-center-identity-access.md)

- [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

- [Weitere Informationen zum Gewähren von Benutzerzugriff auf Anwendungen](../role-based-access-control/overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Verwenden des einmaligen Anmeldens (SSO) in Azure Active Directory

**Leitfaden**: Verwenden Sie für die Authentifizierung bei App Service Azure Active Directory (Azure AD). Damit steht Ihrem Identitätsanbieter ein OAuth 2.0-Dienst zur Verfügung, und der autorisierte Zugriff auf mobile und Webanwendungen wird ermöglicht. 

App Service-Apps nutzen die Verbundidentität. Dabei werden die Benutzeridentitäten und der Authentifizierungsablauf von einem externen Identitätsanbieter für Sie verwaltet. Die folgenden Identitätsanbieter sind standardmäßig verfügbar:

- Azure AD
- Microsoft-Konto

- Facebook

- Google

- Twitter

Wenn Sie die Authentifizierung und Autorisierung mit einem dieser Anbieter aktivieren, ist der entsprechende Anmeldungsendpunkt für die Benutzerauthentifizierung und die Überprüfung von Authentifizierungstoken vom Anbieter verfügbar.

- [Grundlegendes zur Authentifizierung und Autorisierung in Azure App Service](overview-authentication-authorization.md#identity-providers)

- [Weitere Informationen zur Authentifizierung und Autorisierung in Azure App Service](overview-authentication-authorization.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) in Azure Active Directory (Azure AD), und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Security Center.

Implementieren der mehrstufigen Authentifizierung für Azure AD. Administratoren müssen sicherstellen, dass die Abonnementkonten im Portal geschützt sind. Das Abonnement ist für Angriffe anfällig, da es die von Ihnen erstellten Ressourcen verwaltet. 

- [Azure Security: mehrstufige Authentifizierung](/previous-versions/azure/security/develop/secure-aad-app)

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Verwenden von sicheren, von Azure verwalteten Arbeitsstationen für Verwaltungsaufgaben

**Leitfaden**: Verwenden Sie Privileged Access Workstations (PAW) mit mehrstufiger Authentifizierung, die für die Anmeldung und Konfiguration von Azure-Ressourcen konfiguriert sind.

- [Informationen zu Arbeitsstationen mit privilegiertem Zugriff](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Protokollieren von und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Azure Active Directory Privileged Identity Management (Azure AD/PIM) für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung.

Verwenden Sie zusätzlich Azure AD-Risikoerkennungen, um Warnungen und Berichte zu riskantem Benutzerverhalten anzuzeigen.

Der Bedrohungsschutz in Security Center bietet umfassende Schutzmaßnahmen für Ihre Umgebung. Dazu gehören der Schutz vor Bedrohungen für Azure-Computeressourcen wie Windows-Computer, Linux-Computer, App Service und Azure-Container.

- [Bereitstellen von Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure Active Directory-Risikoerkennungen](../active-directory/identity-protection/overview-identity-protection.md)

- [Bedrohungsschutz für Azure-Computeressourcen](../security-center/azure-defender.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Verwenden Sie benannte Standorte mit bedingtem Zugriff, um den Zugriff auf das Azure-Portal nur über bestimmte logische Gruppierungen von IP-Adressbereichen, Ländern oder Regionen zuzulassen.

- [Konfigurieren benannter Standorte in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure Active Directory (Azure AD) als zentrales Authentifizierungs- und Autorisierungssystem für Ihre App Service-Apps. Azure AD schützt Daten mithilfe von starker Verschlüsselung für Daten im Ruhezustand und in der Übertragung. Außerdem werden Salts, Hashes und sichere Speicher für Benutzeranmeldeinformationen verwendet.

- [Konfigurieren Ihrer Azure App Service-Apps zur Verwendung der Azure AD-Anmeldung](configure-authentication-provider-aad.md)

- [Erstellen und Konfigurieren einer Azure AD-Instanz](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Ermitteln Sie veraltete Konten anhand von Protokollen in Azure Active Directory (Azure AD). Verwenden Sie Zugriffsüberprüfungen für Azure-Identitäten, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen sowie Rollenzuweisungen effizient zu verwalten. Überprüfen Sie den Benutzerzugriff in regelmäßigen Abständen, um sicherzustellen, dass nur die vorgesehenen Benutzer weiterhin Zugriff haben. 

- [Grundlegendes zur Azure AD-Berichterstellung](../active-directory/reports-monitoring/index.yml)

- [Verwenden von Zugriffsüberprüfungen für Azure-Identitäten](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Anmeldeinformationen

**Leitfaden**: Verwenden Sie Azure Active Directory (Azure AD) als zentrales Authentifizierungs- und Autorisierungssystem für Ihre App Service-Apps. Azure AD schützt Daten mithilfe von starker Verschlüsselung für Daten im Ruhezustand und während der Übertragung. Außerdem werden Salts, Hashes und sichere Speicher für Benutzeranmeldeinformationen verwendet.

Der Zugriff auf Azure AD-Anmeldeaktivitäten sowie Quellen für Überwachungs- und Risikoereignisprotokolle ermöglichen Ihnen die Integration mit Azure Sentinel oder SIEM-Lösungen (Security Information & Event Management) von Drittanbietern. Sie können diesen Prozess optimieren, indem Sie Diagnoseeinstellungen für Azure AD-Benutzerkonten erstellen und die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich senden. In Log Analytics können alle gewünschten Protokollwarnungen konfiguriert werden.

- [Konfigurieren Ihrer Azure App Service-Apps zur Verwendung der Azure AD-Anmeldung](configure-authentication-provider-aad.md)

- [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Schnellstart: Ausführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Verwenden Sie Azure Active Directory (Azure AD) als zentrales Authentifizierungs- und Autorisierungssystem für Ihre App Service-Apps. 

Verwenden Sie Azure AD Identity Protection, um automatisierte Reaktionen auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten zu konfigurieren. Dazu gehört beispielsweise abweichendes Verhalten bei der Kontoanmeldung auf Steuerungsebene über das Azure-Portal. Außerdem können Sie Daten zur weiteren Untersuchung in Azure Sentinel erfassen. 

- [Konfigurieren Ihrer Azure App Service-App zur Verwendung der Azure AD-Anmeldung](configure-authentication-provider-aad.md)

- [Anzeigen riskanter Azure AD-Anmeldungen](../active-directory/identity-protection/overview-identity-protection.md)

- [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden**: Nicht für App Service verfügbar. Die Kunden-Lockbox wird für Azure App Service nicht unterstützt.

- [Unterstützte Dienste und Szenarios bei allgemeiner Verfügbarkeit](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-protection"></a>Schutz von Daten

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenschutz](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags für die Nachverfolgung von App Service-Ressourcen, die vertrauliche Informationen speichern oder verarbeiten.

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie bei einer App Service-Umgebung separate Abonnements oder Verwaltungsgruppen für Entwicklungs-, Test- und Produktionsumgebungen. Auf die gleiche Weise können Sie Apps isolieren, die vertrauliche Informationen von anderen Apps verarbeiten. Stellen Sie Ihre App Service-App in einem virtuellen Netzwerk bereit. Verwenden Sie Netzwerksicherheitsgruppen und Subnetze, um die Anwendung noch stärker zu isolieren. 

Es gibt zwei Bereitstellungstypen für eine App Service-Umgebung (ASE). Beide ermöglichen Ihnen, den Datenverkehr basierend auf Ihren geschäftlichen Anforderungen zu isolieren.

- Externe Anwendungsdienstumgebung: macht die in der App Service-Umgebung gehosteten Apps über eine im Internet zugängliche IP-Adresse verfügbar.

-  Anwendungsdienstumgebung für internen Lastenausgleich (Internal Load Balancer, ILB): macht die in der App Service-Umgebung gehosteten Apps über eine IP-Adresse in Ihrem virtuellen Netzwerk verfügbar. Der interne Endpunkt ist ein Interner Lastenausgleich (Internal Load Balancer, ILB), daher der Name ILB-ASE. 

Verwenden Sie für App Service für mehrere Instanzen (Apps, die nicht im Tarif „App Service (isoliert)“ bereitgestellt wurden) Virtual Network-Integration für den Zugriff Ihrer App auf Ressourcen in Ihrem virtuellen Netzwerk.  Nutzen Sie den privaten Websitezugriff, um den Zugriff auf eine App nur über ein privates Netzwerk zuzulassen, z. B. aus einem virtuellen Azure-Netzwerk. Die Virtual Network-Integration wird nur für ausgehende Aufrufe aus Ihrer App an Ihr virtuelles Netzwerk verwendet. Das Feature Virtual Network-Integration verhält sich unterschiedlich, wenn es mit einem virtuellen Netzwerk in derselben Region oder mit virtuellen Netzwerken in anderen Regionen verwendet wird. 
 
- [Überlegungen zum Netzwerkbetrieb in einer App Service-Umgebung](environment/network-info.md)

- [Erstellen einer externen App Service-Umgebung](environment/create-external-ase.md)

- [Erstellen einer internen App Service-Umgebung](environment/create-ilb-ase.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden**: Obwohl die Features für die Datenidentifizierung und -klassifizierung sowie die Vermeidung von Datenverlusten noch nicht für App Service verfügbar sind, können Sie das Risiko der Datenexfiltration aus dem virtuellen Netzwerk reduzieren, indem Sie alle Regeln entfernen, bei denen das Ziel ein „Tag“ für Internet- oder Azure-Dienste verwendet. 

Microsoft verwaltet die zugrunde liegende Infrastruktur für App Service und hat strenge Kontrollen implementiert, um Verluste oder Offenlegungen Ihrer Daten zu verhindern.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Verwenden Sie für die Verschlüsselung sämtlicher übertragener Informationen als Mindestversion TLS 1.2 durch Konfigurieren in den TLS/SSL-Einstellungen. Stellen Sie außerdem sicher, dass alle HTTP-Verbindungsanforderungen an HTTPS umgeleitet werden.

- [Grundlegendes zur Verschlüsselung während der Übertragung für Azure App Service-Web-Apps](security-recommendations.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Zurzeit nicht verfügbar. Die Features zur Datenidentifizierung und -klassifizierung sowie zur Vermeidung von Datenverlusten sind für App Service noch nicht verfügbar. 

Nutzen Sie Tags für App Service-Apps, die möglicherweise vertrauliche Informationen verarbeiten. Implementieren Sie eine Drittanbieterlösung, wenn dies für Compliancezwecke erforderlich ist.

Microsoft verwaltet die zugrunde liegende Plattform, behandelt alle Kundendaten vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Offenlegung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Verwenden der rollenbasierten Zugriffssteuerung zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Verwenden Sie die rollenbasierte Zugriffssteuerung (Azure RBAC) in Azure Active Directory (Azure AD), um den Zugriff auf die Steuerungsebene von App Service über das Azure-Portal zu steuern.

- [Konfigurieren von RBAC in Azure](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Die Inhalte von Websites in App Service (z. B. Dateien) werden in Azure Storage gespeichert. Dort werden ruhende Inhalte automatisch verschlüsselt. Sie können festlegen, dass Anwendungsgeheimnisse in Key Vault gespeichert werden, und diese zur Laufzeit abrufen.

Die Kundengeheimnisse werden im Ruhezustand verschlüsselt, während sie in App Service-Konfigurationsdatenbanken gespeichert sind.

Beachten Sie, dass von Websites optional zwar lokal angefügte Datenträger als temporärer Speicher verwendet werden können (z. B. „D:\local“ und „%TMP%“), diese aber nicht im Ruhezustand verschlüsselt werden.

- [Grundlegendes zu Datenschutzkontrollen für Azure App Service]()

- [Grundlegendes zur Azure Storage-Verschlüsselung für ruhende Daten](../storage/common/storage-service-encryption.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit dem Azure-Aktivitätsprotokoll, um Warnungen für den Fall zu erstellen, dass Änderungen an Produktionsinstanzen von App Service-Apps und anderen kritischen bzw. zugehörigen Ressourcen vorgenommen werden.

- [Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Führen Sie eine DevSecOps-Übung durch, um sicherzustellen, dass Ihre App Service-Apps sicher sind und während des gesamten Lebenszyklus geschützt bleiben. Mit DevSecOps werden das Sicherheitsteam Ihrer Organisation und die entsprechenden Funktionen in Ihre DevOps-Verfahren eingebunden, damit die Sicherheit in der Verantwortung aller Teammitglieder liegt.

Lesen und befolgen Sie die Empfehlungen von Security Center zum Schützen Ihrer App Service-Apps.

- [Hinzufügen einer ständigen Sicherheitsüberprüfung zu Ihrer CI/CD-Pipeline](/azure/devops/migrate/security-validation-cicd-pipeline?preserve-view=true&view=azure-devops)

- [Implementieren von Empfehlungen für die Sicherheitsrisikobewertung aus Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Microsoft führt die Verwaltung der Sicherheitsrisiken auf den zugrunde liegenden Systemen aus, die App Service unterstützen. Allerdings können Sie den Schweregrad der Empfehlungen in Security Center und die Sicherheitsbewertung nutzen, um das Risiko in Ihrer Umgebung zu messen. Ihre Sicherheitsbewertung basiert darauf, wie viele Security Center-Empfehlungen Sie umgesetzt haben. Sehen Sie sich jeweils den Schweregrad einer Empfehlung an, um festzulegen, welche Empfehlungen zuerst umgesetzt werden sollten.

- [Sicherheitsempfehlungen: Referenzhandbuch](../security-center/recommendations-reference.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Verwenden der automatisierten Asset Discovery-Lösung

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen (etwa Computeressourcen, Speicher, Netzwerke, Ports, Protokolle usw.) in Ihren Abonnements abzufragen oder zu ermitteln. Stellen Sie sicher, dass entsprechende Berechtigungen in Ihrem Mandanten angewandt werden und dass Sie alle Azure-Abonnements und Ressourcen in Ihren Abonnements auflisten können.

Obwohl klassische Azure-Ressourcen über das Resource Graph ermittelt werden können, wird dringend empfohlen, Azure Resource Manager-Ressourcen zu erstellen und zu verwenden.

- [Erstellen von Abfragen mit Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Anzeigen Ihrer Azure-Abonnements](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Grundlegendes zu Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Wenden Sie Tags auf Ihre Azure-Ressourcen mithilfe von Metadaten an, um sie logisch in einer Taxonomie zu organisieren.

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Verwenden Sie Tags, Verwaltungsgruppen und separate Abonnements nach Bedarf, um Azure-Ressourcen zu verwalten und nachzuverfolgen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass dabei nicht autorisierte Ressourcen aus Ihren Abonnements entfernt werden.

Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)
- Zulässige Ressourcentypen

Weitere Informationen finden Sie unter den angegebenen Links.

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definieren und Verwalten des Bestands an genehmigten Azure-Ressourcen

**Leitfaden**: Erstellen Sie einen Bestand an genehmigten Azure-Ressourcen und genehmigter Software für Computeressourcen gemäß den Anforderungen Ihrer Organisation.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen festzulegen, die in Ihren Abonnements erstellt werden können.

Verwenden Sie Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen und zu ermitteln.  Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure-Ressourcen genehmigt sind. 

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Überwachen auf nicht genehmigte Softwareanwendungen innerhalb von Computeressourcen

**Leitfaden**: Verwenden Sie Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen oder zu ermitteln und um sicherzustellen, dass die ermittelten Azure-Ressourcen basierend gemäß Ihren Organisationsrichtlinien genehmigt sind.

Verwenden Sie WebJobs in App Service, um nicht genehmigte Softwareanwendungen zu überwachen, die in Computeressourcen bereitgestellt werden. Nutzen Sie WebJobs, um ein Programm oder Skript in derselben Instanz wie eine Web-App, API-App oder mobile App auszuführen. Definieren Sie WebJob-Konfigurationen und die Überwachung mit Protokollen. Wählen Sie auf der Seite Details zur WebJob-Ausführung die Option Ausgabe umschalten, um den Text der Protokollinhalte anzuzeigen. Beachten Sie, dass WebJobs derzeit noch nicht für App Service für Linux unterstützt werden.

- [Ausführen von Hintergrundaufgaben mit WebJobs in Azure App Service](webjobs-create.md)

- [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

- [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden**: Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure-Ressourcen genehmigt sind. Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen festzulegen, die in Ihren Abonnements erstellt werden können. Entfernen Sie alle bereitgestellten Softwareanwendungen, die gemäß Ihren Organisationsrichtlinien nicht genehmigt sind.

- [Ausführen von Hintergrundaufgaben mit WebJobs in Azure App Service](webjobs-create.md)

- [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

- [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="68-use-only-approved-applications"></a>6.8: Ausschließliche Verwendung genehmigter Anwendungen

**Leitfaden**: Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure-Ressourcen genehmigt sind. Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen festzulegen, die in Ihren Abonnements erstellt werden können. Entfernen Sie alle bereitgestellten Softwareanwendungen, die gemäß Ihren Organisationsrichtlinien nicht genehmigt sind. 

- [Ausführen von Hintergrundaufgaben mit WebJobs in Azure App Service](webjobs-create.md)

- [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

- [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Richten Sie einen Prozess ein, mit dem nicht autorisierte Azure-Dienste regelmäßig überprüft werden, um sicherzustellen, dass in Ihren Abonnements nur autorisierte Azure-Dienste verwendet werden.

Verwenden Sie bei diesem Prozess Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen und zu ermitteln. Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure-Ressourcen genehmigt sind.

Konfigurieren Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Ihren Abonnements erstellt werden können. Nutzen Sie hierzu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)

- Zulässige Ressourcentypen

Verwenden Sie WebJobs in App Service, um nicht genehmigte Softwareanwendungen zu überwachen, die in Computeressourcen bereitgestellt werden. Nutzen Sie WebJobs, um ein Programm oder Skript in derselben Instanz wie eine Web-App, API-App oder mobile App auszuführen. Definieren Sie WebJob-Konfigurationen und die Überwachung mit Protokollen. Wählen Sie auf der Seite Details zur WebJob-Ausführung die Option Ausgabe umschalten, um den Text der Protokollinhalte anzuzeigen. Beachten Sie, dass WebJobs derzeit noch nicht für App Service für Linux unterstützt werden.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Ausführen von Hintergrundaufgaben mit WebJobs in Azure App Service](webjobs-create.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Verwalten eines Bestands an genehmigten Softwaretiteln

**Leitfaden**: Implementieren Sie einen Prozess, mit dem Softwaretitel in Ihren Abonnements regelmäßig inventarisiert und überprüft werden, um sicherzustellen, dass in Ihren Abonnements nur autorisierte Azure-Dienste verwendet werden.

Verwenden Sie bei diesem Prozess Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen und zu ermitteln. Stellen Sie sicher, dass alle in der Umgebung ermittelten Azure-Ressourcen genehmigt sind.

Konfigurieren Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)

- Zulässige Ressourcentypen

Verwenden Sie auf gleiche Weise WebJobs in App Service, um nicht genehmigte Softwareanwendungen zu inventarisieren, die in Computeressourcen bereitgestellt werden. Definieren Sie deren Konfiguration und die Überwachung mit Protokollen. Wählen Sie auf der Seite Details zur WebJob-Ausführung die Option Ausgabe umschalten, um den Text der Protokollinhalte anzuzeigen. Beachten Sie, dass WebJobs derzeit noch nicht für App Service für Linux unterstützt werden.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden**: Konfigurieren Sie bedingten Azure-Zugriff, um die Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.

- [Verwalten des Zugriffs auf die Azure-Verwaltung mit bedingtem Zugriff](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Einschränken der Möglichkeiten der Benutzer, Skripte innerhalb von Computeressourcen auszuführen

**Leitfaden**: Mit WebJobs in App Service können Kunden ein Programm oder Skript in derselben Instanz wie eine Web-App, API-App oder mobile App ausführen. Sie sind dafür verantwortlich, Ihre Konfiguration so zu definieren, dass Skripts, die von der Organisation nicht zugelassen sind, eingeschränkt oder blockiert werden. App Service bietet keinen nativen Mechanismus zum Einschränken der Skriptausführung. Beachten Sie, dass WebJobs derzeit noch nicht für App Service für Linux unterstützt werden.

- [Ausführen von Hintergrundaufgaben mit WebJobs in Azure App Service](webjobs-create.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Physische oder logische Trennung von Anwendungen mit hohem Risiko

**Leitfaden**: Implementieren Sie separate Abonnements oder Verwaltungsgruppen, um stark risikobehaftete App Service-Apps zu isolieren. Stellen Sie eine App mit einem höherem Risiko in einem eigenen virtuellen Netzwerk bereit, da die Umkreissicherheit in App Service über die Verwendung virtueller Netzwerke erreicht wird. Eine App Service-Umgebung ist eine App Service-Bereitstellung in einem Subnetz in Ihrem virtuellen Azure-Netzwerk. 

Es gibt zwei Arten von Anwendungsdienstumgebungen, externen Anwendungsdienstumgebungen und ILB-Anwendungsdienstumgebungen (Internal Load Balancer, interner Lastenausgleich). Wählen Sie eine geeignete Architektur für Ihre Anforderungen aus.

- [Überlegungen zum Netzwerkbetrieb in einer App Service-Umgebung](environment/network-info.md)

- [Erstellen einer externen App Service-Umgebung](environment/create-external-ase.md)

- [Erstellen und Verwenden einer App Service-Umgebung für internen Lastenausgleich](environment/create-ilb-ase.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Definieren und implementieren Sie mit Azure Policy Standardsicherheitskonfigurationen für Ihre bereitgestellten App Service-Apps.

Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.Web“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration Ihrer App Service-Web-Apps zu erstellen.

Wenden Sie integrierte Richtliniendefinitionen wie die folgenden an:
- App Service sollte einen VNET-Dienstendpunkt verwenden.
- Webanwendungen sollten nur über HTTPS zugänglich sein.

- Verwenden Sie die neueste TLS-Version in Ihren Apps.

Es wird empfohlen, den Prozess zum Anwenden der integrierten Richtliniendefinitionen für die standardisierte Verwendung zu dokumentieren.   

- [Anzeigen verfügbarer Azure Policy-Aliase](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure Policy-Einstellungen [deny] (Verweigern) und [deploy if not exist] (Bereitstellen, falls nicht vorhanden), um durchgängig sichere Einstellungen für Ihre Azure App Service-Apps zu erzwingen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Nutzen Sie Azure DevOps oder Azure Repos, um Ihren Code sicher zu speichern und zu verwalten, wenn Sie benutzerdefinierte Azure Policy-Definitionen verwenden.

Verwenden Sie Ihre vorhandene CI/CD-Pipeline (Continuous Integration und Continuous Delivery), um eine als sicher bekannte Konfiguration bereitzustellen.

- [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Dokumentation zu Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden**: Verwenden Sie integrierte Azure Policy-Definitionen sowie Azure Policy-Aliase im Namespace „Microsoft.Web“, um benutzerdefinierte Richtlinien zum Überwachen und Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Entwickeln Sie einen Prozess und eine Pipeline zum Verwalten von Richtlinienausnahmen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Ressourcen

**Leitfaden**: Verwenden Sie integrierte Azure Policy-Definitionen sowie Azure Policy-Aliase im Namespace „Microsoft.Web“, um benutzerdefinierte Richtlinien zum Überwachen und Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. 

Wenden Sie die Azure Policy-Auswirkungen [audit] (Überwachen), [deny] (Verweigern) und [deploy if not exist] (Bereitstellen, falls nicht vorhanden) an, um Konfigurationen für Ihre Azure-Ressourcen automatisch zu erzwingen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Leitfaden**: Verwenden Sie verwaltete Identitäten, um App Service-Apps mit einer automatisch verwalteten Identität in Azure Active Directory (Azure AD) bereitzustellen. Mithilfe verwalteter Identitäten können Sie die Authentifizierung bei jedem Dienst durchführen, der die Azure AD-Authentifizierung unterstützt, einschließlich Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein. Stellen Sie sicher, dass das vorläufige Löschen in Azure Key Vault aktiviert ist.

- [Aktivieren des vorläufigen Löschens in Azure Key Vault](../key-vault/general/key-vault-recovery.md)

- [Verwenden von verwalteten Identitäten für App Service](overview-managed-identity.md)

- [Bereitstellen der Key Vault-Authentifizierung mit einer verwalteten Identität](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Verwenden Sie verwaltete Identitäten, um bereitgestellte App Service-Apps mit einer automatisch verwalteten Identität in Azure Active Directory (Azure AD) bereitzustellen. Mithilfe verwalteter Identitäten können Sie die Authentifizierung bei jedem Dienst durchführen, der die Azure AD-Authentifizierung unterstützt, einschließlich Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein.

- [Verwenden von verwalteten Identitäten für App Service](overview-managed-identity.md)

- [Bereitstellen der Key Vault-Authentifizierung mit einer verwalteten Identität](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Anleitung:** Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen.

- [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Das Feature zum Sichern und Wiederherstellen in App Service ermöglicht Ihnen, App-Sicherungen ganz einfach manuell oder nach einem Zeitplan zu erstellen. Sie können die Sicherungen so konfigurieren, dass Sie für einen bis zu unbestimmten Zeitraum aufbewahrt werden. Sie können die App mit einer Momentaufnahme eines früheren Zustands wiederherstellen, indem Sie die vorhandene App überschreiben oder als andere App wiederherstellen.

App Service kann die folgenden Informationen in einem Azure Storage-Konto und einem Container sichern, für deren Verwendung Sie die App konfiguriert haben:
- App-Konfiguration
- Dateiinhalte
- Datenbank, die mit Ihrer App verbunden ist.

Stellen Sie sicher, dass reguläre und automatisierte Sicherungen so oft ausgeführt werden, wie in Ihren Organisationsrichtlinien festgelegt wurde.

- [Grundlegendes zur Azure App Service-Sicherungsfunktion](manage-backup.md)

- [Kundenseitig verwaltete Schlüssel für die Azure Storage-Verschlüsselung](../storage/common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller kundenseitig verwalteten Schlüssel

**Leitfaden**: Verwenden Sie das Feature zur Sicherung und Wiederherstellung von App Service, um Ihre Anwendungen zu sichern. Die Sicherungsfeatures erfordern ein Azure Storage-Konto, um die Sicherungsinformationen Ihrer Anwendung zu speichern.

- Azure Storage bietet Verschlüsselung im Ruhezustand. Verwenden Sie die vom System bereitgestellten Schlüssel oder Ihre eigenen kundenseitig verwalteten Schlüssel. Dort werden Ihre Anwendungsdaten gespeichert, wenn sie nicht in einer Web-App in Azure ausgeführt werden.
- Das Ausführen aus einem Bereitstellungspaket ist eine Bereitstellungsfunktion von App Service. Sie ermöglicht Ihnen, Ihre Websiteinhalte mithilfe einer SAS-URL (Shared Access Signature) über ein Azure Storage-Konto bereitzustellen.

- Key Vault-Verweise sind ein Sicherheitsfeature von App Service. Es ermöglicht Ihnen, Geheimnisse zur Laufzeit als Anwendungseinstellungen zu importieren. Verwenden Sie dies zum Verschlüsseln der SAS-URL Ihres Azure Storage-Kontos.

Weitere Informationen finden Sie unter den aufgeführten Links.

- [Sichern Ihrer App in Azure](manage-backup.md)

- [Wiederherstellen einer in Azure App Service ausgeführten App](web-sites-restore.md)

- [Grundlegendes zur Verschlüsselung ruhender Daten in Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Tabelle für Verschlüsselungsmodell und Schlüsselverwaltung](../security/fundamentals/encryption-atrest.md)

- [Verschlüsselung im Ruhezustand mithilfe von Kunden verwalteter Schlüssel](configure-encrypt-at-rest-using-cmk.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich kundenseitig verwalteter Schlüssel

**Leitfaden**: Testen Sie den Wiederherstellungsprozess regelmäßig für alle Sicherungen Ihrer App Service-Anwendungen.

- [Sichern Ihrer App in Azure](manage-backup.md)

- [Wiederherstellen einer Azure App Service-Web-App](web-sites-restore.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: App Service-Sicherungen werden in einem Azure Storage-Konto gespeichert. Daten in Azure Storage werden auf transparente Weise mit der AES-256-Verschlüsselung – einer der stärksten verfügbaren Blockchiffren – ver- und entschlüsselt und sind mit dem FIPS 140-2-Standard konform. Die Azure Storage-Verschlüsselung ähnelt der BitLocker-Verschlüsselung unter Windows.

Die Azure Storage-Verschlüsselung ist für alle Speicherkonten aktiviert, einschließlich Resource Manager-Speicherkonten und klassischer Speicherkonten. Die Azure Storage-Verschlüsselung kann nicht deaktiviert werden. Da Ihre Daten standardmäßig geschützt werden, müssen Sie weder Code noch Anwendungen ändern, um die Azure Storage-Verschlüsselung nutzen zu können.

Standardmäßig werden Daten in einem Speicherkonto mit von Microsoft verwalteten Schlüsseln verschlüsselt. Sie können von Microsoft verwaltete Schlüssel für die Verschlüsselung Ihrer Daten nutzen oder die Verschlüsselung mit Ihren eigenen Schlüsseln verwalten. Stellen Sie sicher, dass das vorläufige Löschen in Azure Key Vault aktiviert ist.

- [Azure Storage-Verschlüsselung für ruhende Daten](../storage/common/storage-service-encryption.md)

- [Aktivieren des vorläufigen Löschens in Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Azure Security Center-Überwachung**: Ja

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

**Anleitung:** Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

Markieren Sie außerdem Abonnements aussagekräftig (z. B. „Produktion“, „Nicht-Produktion“), und erstellen Sie ein Benennungssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren.

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Tests zur Reaktionsfähigkeit Ihres Systems auf Vorfälle durch. Identifizieren Sie Schwachstellen und Lücken, und überarbeiten Sie den Plan bei Bedarf.

- [Siehe NIST-Veröffentlichung: „Leitfaden zum Testen, Trainieren und Ausführen von Programmen für IT-Pläne und -Funktionen“](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf die Kundendaten zugegriffen haben.  Überprüfen Sie die Vorfälle anschließend, um sicherzustellen, dass die Probleme behoben wurden.

- [Festlegen der Kontaktinformationen in Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie Ihre Security Center-Warnungen und -Empfehlungen mithilfe des Features „Fortlaufender Export“. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Verwenden Sie den Security Center-Datenconnector zum Streamen der Warnungen an Azure Sentinel gemäß Ihren Geschäftsanforderungen.

- [Konfigurieren des fortlaufenden Exports](../security-center/continuous-export.md)

- [Streamen von Warnungen in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Security Center, um über „Logic Apps“ automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen.

- [Konfigurieren von Workflowautomatisierung und Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Durchführen regelmäßiger Penetrationstests Ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnissen

**Leitfaden**: Befolgen Sie die Microsoft Rules of Engagement, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Informieren Sie sich über die Microsoft-Strategie im Zusammenhang mit Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastrukturen, Dienste und Anwendungen sowie zu deren Durchführung.

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).